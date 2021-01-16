---
title: Charger des fichiers dans ASP.NET Core
author: rick-anderson
description: Comment utiliser la liaison de modèle et le streaming pour charger des fichiers dans ASP.NET Core MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/21/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/models/file-uploads
ms.openlocfilehash: c32d20d4616650db004c78fb4d8ea9a4d5a3beab
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252797"
---
# <a name="upload-files-in-aspnet-core"></a>Charger des fichiers dans ASP.NET Core

Par [Steve Smith](https://ardalis.com/) et [Rutger Storm](https://github.com/rutix)

::: moniker range=">= aspnetcore-5.0"

ASP.NET Core prend en charge le chargement d’un ou plusieurs fichiers à l’aide d’une liaison de modèle mise en mémoire tampon pour les fichiers plus petits et la diffusion en continu sans mise en mémoire tampon pour les fichiers plus volumineux

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="security-considerations"></a>Considérations relatives à la sécurité

Soyez prudent lorsque vous fournissez aux utilisateurs la possibilité de charger des fichiers sur un serveur. Les attaquants peuvent tenter d’effectuer les opérations suivantes :

* Exécuter [des attaques par déni de service](/windows-hardware/drivers/ifs/denial-of-service) .
* Télécharger des virus ou des programmes malveillants.
* Compromettre les réseaux et les serveurs d’une autre manière.

Les étapes de sécurité qui réduisent la probabilité d’une attaque réussie sont les suivantes :

* Chargez les fichiers dans une zone de chargement de fichier dédiée, de préférence sur un lecteur non-système. Un emplacement dédié permet d’imposer des restrictions de sécurité plus faciles sur les fichiers téléchargés. Désactivez les autorisations d’exécution sur l’emplacement de chargement du fichier.&dagger;
* Ne conservez **pas** les fichiers téléchargés dans la même arborescence de répertoires que l’application.&dagger;
* Utilisez un nom de fichier sécurisé déterminé par l’application. N’utilisez pas un nom de fichier fourni par l’utilisateur ou le nom de fichier non approuvé du fichier téléchargé. &dagger; Code HTML encode le nom de fichier non fiable lors de son affichage. Par exemple, la journalisation du nom de fichier ou l’affichage dans l’interface utilisateur ( Razor génère automatiquement le code html).
* Autorisez uniquement les extensions de fichier approuvées pour la spécification de conception de l’application.&dagger; <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* Vérifiez que les vérifications côté client sont effectuées sur le serveur. &dagger; Les contrôles côté client sont faciles à contourner.
* Vérifiez la taille d’un fichier téléchargé. Définissez une limite de taille maximale pour empêcher les chargements volumineux.&dagger;
* Lorsque les fichiers ne doivent pas être remplacés par un fichier téléchargé portant le même nom, vérifiez le nom du fichier par rapport à la base de données ou au stockage physique avant de charger le fichier.
* **Exécutez un programme de détection de virus et de logiciels malveillants sur le contenu chargé avant que le fichier ne soit stocké.**

&dagger;L’exemple d’application illustre une approche qui répond aux critères.

> [!WARNING]
> Le chargement d’un code malveillant sur un système est généralement la première étape de l’exécution de code capable de :
>
> * Maîtrisez complètement un système.
> * Surchargez un système avec le résultat du blocage du système.
> * Compromettre les données utilisateur ou système.
> * Appliquez Graffiti à une interface utilisateur publique.
>
> Pour plus d’informations sur la réduction de la surface d’attaque quand vous acceptez des fichiers d’utilisateurs, consultez les ressources suivantes :
>
> * [Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) (Chargement de fichiers illimité)
> * [Sécurité Azure : Vérifier que les contrôles appropriés sont en place quand vous acceptez des fichiers d’utilisateurs](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

Pour plus d’informations sur l’implémentation de mesures de sécurité, y compris des exemples de l’exemple d’application, consultez la section [validation](#validation) .

## <a name="storage-scenarios"></a>Scénarios de stockage

Les options de stockage courantes pour les fichiers sont les suivantes :

* Base de données

  * Pour les petits chargements de fichiers, une base de données est souvent plus rapide que les options de stockage physique (système de fichiers ou partage réseau).
  * Une base de données est souvent plus pratique que les options de stockage physique, car la récupération d’un enregistrement de base de données pour les données utilisateur peut fournir simultanément le contenu du fichier (par exemple, une image de l’avatar).
  * Une base de données est potentiellement moins coûteuse que l’utilisation d’un service de stockage de données.

* Stockage physique (système de fichiers ou partage réseau)

  * Pour les chargements de fichiers volumineux :
    * Les limites de base de données peuvent limiter la taille du téléchargement.
    * Le stockage physique est souvent moins économique que le stockage dans une base de données.
  * Le stockage physique est potentiellement moins onéreux que l’utilisation d’un service de stockage de données.
  * Le processus de l’application doit disposer d’autorisations en lecture et en écriture sur l’emplacement de stockage. **N’accordez jamais l’autorisation EXECUTE.**

* Service de stockage de données (par exemple, [stockage d’objets BLOB Azure](https://azure.microsoft.com/services/storage/blobs/))

  * Les services offrent généralement une évolutivité et une résilience améliorées par rapport aux solutions locales qui sont généralement sujettes à des points de défaillance uniques.
  * Les services sont potentiellement moins coûteux dans les scénarios d’infrastructure de stockage volumineux.

  Pour plus d’informations, consultez [démarrage rapide : utiliser .net pour créer un objet BLOB dans le stockage d’objets](/azure/storage/blobs/storage-quickstart-blobs-dotnet).

## <a name="file-upload-scenarios"></a>Scénarios de chargement de fichiers

La mise en mémoire tampon et la diffusion en continu sont deux approches générales pour le téléchargement de fichiers.

**des réponses**

La totalité du fichier est lue dans un <xref:Microsoft.AspNetCore.Http.IFormFile> , qui est une représentation C# du fichier utilisé pour traiter ou enregistrer le fichier.

Les ressources (disque, mémoire) utilisées par les chargements de fichiers dépendent du nombre et de la taille des chargements de fichiers simultanés. Si une application tente de mettre en mémoire tampon un trop grand nombre de chargements, le site se bloque lorsqu’il manque de mémoire ou d’espace disque. Si la taille ou la fréquence des chargements de fichiers épuisent les ressources d’application, utilisez la diffusion en continu.

> [!NOTE]
> Tout fichier mis en mémoire tampon dépassant 64 Ko est déplacé de la mémoire vers un fichier temporaire sur le disque.

La mise en mémoire tampon de petits fichiers est traitée dans les sections suivantes de cette rubrique :

* [Stockage physique](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [Base de données](#upload-small-files-with-buffered-model-binding-to-a-database)

**Streaming**

Le fichier est reçu à partir d’une demande en plusieurs parties et est directement traité ou enregistré par l’application. La diffusion en continu n’améliore pas les performances de manière significative. La diffusion en continu réduit les demandes de mémoire ou d’espace disque lors du chargement de fichiers.

La diffusion en continu de fichiers volumineux est traitée dans la section [chargement de fichiers volumineux avec diffusion](#upload-large-files-with-streaming) .

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a>Charger des fichiers de petite taille avec une liaison de modèle mise en mémoire tampon vers un stockage physique

Pour télécharger des fichiers de petite taille, utilisez un formulaire en plusieurs parties ou créez une requête de publication à l’aide de JavaScript.

L’exemple suivant illustre l’utilisation d’un Razor formulaire de pages pour télécharger un seul fichier (*pages/BufferedSingleFileUploadPhysical. cshtml* dans l’exemple d’application) :

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

L’exemple suivant est similaire à l’exemple précédent, à l’exception de :

* JavaScript ([API FETCH](https://developer.mozilla.org/docs/Web/API/Fetch_API)) est utilisé pour envoyer les données du formulaire.
* Il n’y a aucune validation.

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

Pour exécuter la publication de formulaire dans JavaScript pour les clients qui [ne prennent pas en charge l’API FETCH](https://caniuse.com/#feat=fetch), utilisez l’une des approches suivantes :

* Utilisez un Polyfill d’extraction (par exemple, [Window. Fetch Polyfill (GitHub/fetch)](https://github.com/github/fetch)).
* Utiliser `XMLHttpRequest`. Exemple :

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

Afin de prendre en charge les chargements de fichiers, les formulaires HTML doivent spécifier un type d’encodage ( `enctype` ) de `multipart/form-data` .

Pour `files` qu’un élément INPUT prenne en charge le téléchargement de plusieurs fichiers, fournissez l' `multiple` attribut sur l' `<input>` élément :

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

Les fichiers individuels téléchargés sur le serveur sont accessibles via la [liaison de modèle](xref:mvc/models/model-binding) à l’aide de <xref:Microsoft.AspNetCore.Http.IFormFile> . L’exemple d’application illustre plusieurs chargements de fichiers mis en mémoire tampon pour les scénarios de base de données et de stockage physique.

<a name="filename"></a>

> [!WARNING]
> N’utilisez **pas** la `FileName` propriété <xref:Microsoft.AspNetCore.Http.IFormFile> autre que pour l’affichage et la journalisation. Lors de l’affichage ou de la journalisation, le nom du fichier est encodé au format HTML. Une personne malveillante peut fournir un nom de fichier malveillant, y compris les chemins d’accès complets ou les chemins d’accès relatifs. Les applications doivent :
>
> * Supprimez le chemin d’accès du nom de fichier fourni par l’utilisateur.
> * Enregistrez le nom de fichier encodé au format HTML, avec chemin d’accès supprimé pour l’interface utilisateur ou la journalisation.
> * Générez un nouveau nom de fichier aléatoire pour le stockage.
>
> Le code suivant supprime le chemin d’accès du nom de fichier :
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> Les exemples fournis jusqu’à présent ne prennent pas en compte les considérations de sécurité. Des informations supplémentaires sont fournies par les sections suivantes et l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):
>
> * [Considérations relatives à la sécurité](#security-considerations)
> * [Validation](#validation)

Lors du chargement de fichiers à l’aide d’une liaison de modèle et <xref:Microsoft.AspNetCore.Http.IFormFile> , la méthode d’action peut accepter les éléments suivants :

* Une seule <xref:Microsoft.AspNetCore.Http.IFormFile> .
* L’un des regroupements suivants qui représentent plusieurs fichiers :
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * [Tarifs](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>>

> [!NOTE]
> La liaison correspond aux fichiers de formulaire par nom. Par exemple, la `name` valeur html dans `<input type="file" name="formFile">` doit correspondre au paramètre C#/lié à la propriété ( `FormFile` ). Pour plus d’informations, consultez la section valeur de l' [attribut de nom de correspondance pour le nom de paramètre de la méthode de publication](#match-name-attribute-value-to-parameter-name-of-post-method) .

L’exemple suivant :

* Effectue une boucle sur un ou plusieurs fichiers téléchargés.
* Utilise [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) pour retourner le chemin d’accès complet d’un fichier, y compris le nom de fichier. 
* Enregistre les fichiers dans le système de fichiers local à l’aide d’un nom de fichier généré par l’application.
* Retourne le nombre total et la taille des fichiers téléchargés.

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

Utilisez `Path.GetRandomFileName` pour générer un nom de fichier sans chemin d’accès. Dans l’exemple suivant, le chemin d’accès est obtenu à partir de la configuration :

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

Le chemin d’accès passé à <xref:System.IO.FileStream> *doit* inclure le nom de fichier. Si le nom de fichier n’est pas fourni, une <xref:System.UnauthorizedAccessException> exception est levée au moment de l’exécution.

Les fichiers chargés à l’aide de la <xref:Microsoft.AspNetCore.Http.IFormFile> technique sont mis en mémoire tampon ou sur disque sur le serveur avant le traitement. À l’intérieur de la méthode d’action, le <xref:Microsoft.AspNetCore.Http.IFormFile> contenu est accessible en tant que <xref:System.IO.Stream> . Outre le système de fichiers local, les fichiers peuvent être enregistrés sur un partage réseau ou un service de stockage de fichiers, tel que le [stockage d’objets BLOB Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).

Pour obtenir un autre exemple qui effectue une boucle sur plusieurs fichiers pour le téléchargement et utilise des noms de fichiers sécurisés, consultez *pages/BufferedMultipleFileUploadPhysical. cshtml. cs* dans l’exemple d’application.

> [!WARNING]
> [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) lève une exception <xref:System.IO.IOException> si plus de 65 535 fichiers sont créés sans supprimer les fichiers temporaires précédents. La limite de 65 535 fichiers est une limite par serveur. Pour plus d’informations sur cette limite sur le système d’exploitation Windows, consultez les notes dans les rubriques suivantes :
>
> * [GetTempFileNameA fonction)](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a>Charger des fichiers de petite taille avec une liaison de modèle mise en mémoire tampon vers une base de données

Pour stocker des données de fichier binaires dans une base de données à l’aide de [Entity Framework](/ef/core/index), définissez une <xref:System.Byte> propriété de tableau sur l’entité :

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

Spécifiez une propriété de modèle de page pour la classe qui comprend <xref:Microsoft.AspNetCore.Http.IFormFile> :

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Http.IFormFile> peut être utilisé directement comme paramètre de méthode d’action ou comme propriété de modèle liée. L’exemple précédent utilise une propriété de modèle liée.

Le `FileUpload` est utilisé dans le Razor formulaire pages :

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

Lorsque le formulaire est publié sur le serveur, copiez <xref:Microsoft.AspNetCore.Http.IFormFile> -le dans un flux et enregistrez-le en tant que tableau d’octets dans la base de données. Dans l’exemple suivant, `_dbContext` stocke le contexte de base de données de l’application :

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

L’exemple précédent est semblable à un scénario illustré dans l’exemple d’application :

* *Pages/BufferedSingleFileUploadDb. cshtml*
* *Pages/BufferedSingleFileUploadDb. cshtml. cs*

> [!WARNING]
> Soyez prudent quand vous stockez des données binaires dans des bases de données relationnelles, car cela peut avoir un impact négatif sur les performances.
>
> Ne vous fiez pas à la `FileName` propriété de sans validation ni à approuver celle-ci <xref:Microsoft.AspNetCore.Http.IFormFile> . La `FileName` propriété doit uniquement être utilisée à des fins d’affichage et uniquement après l’encodage HTML.
>
> Les exemples fournis ne prennent pas en compte les considérations de sécurité. Des informations supplémentaires sont fournies par les sections suivantes et l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):
>
> * [Considérations relatives à la sécurité](#security-considerations)
> * [Validation](#validation)

### <a name="upload-large-files-with-streaming"></a>Charger des fichiers volumineux avec streaming

L’exemple suivant montre comment utiliser JavaScript pour diffuser un fichier vers une action de contrôleur. Le jeton anti-contrefaçon du fichier est généré à l’aide d’un attribut de filtre personnalisé et transmis aux en-têtes HTTP du client plutôt que dans le corps de la demande. Étant donné que la méthode d’action traite directement les données chargées, la liaison de modèle de formulaire est désactivée par un autre filtre personnalisé. Dans l’action, le contenu du formulaire est lu en avec `MultipartReader`, qui lit chaque `MultipartSection` individuelle, traitant le fichier ou enregistrant le contenu selon ce qui est approprié. Une fois les sections en plusieurs parties lues, l’action effectue sa propre liaison de modèle.

La réponse de page initiale charge le formulaire et enregistre un jeton anti-contrefaçon dans un cookie (via l' `GenerateAntiforgeryTokenCookieAttribute` attribut). L’attribut utilise la [prise en charge de l’anticontrefaçon](xref:security/anti-request-forgery) intégrée de ASP.net Core pour définir un cookie avec un jeton de demande :

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

`DisableFormValueModelBindingAttribute`Est utilisé pour désactiver la liaison de modèle :

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

Dans l’exemple d’application, `GenerateAntiforgeryTokenCookieAttribute` et `DisableFormValueModelBindingAttribute` sont appliqués en tant que filtres aux modèles d’application de page de et à l' `/StreamedSingleFileUploadDb` aide des conventions de `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` [ Razor pages](xref:razor-pages/razor-pages-conventions):

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=7-10,16-19)]

Étant donné que la liaison de modèle ne lit pas le formulaire, les paramètres qui sont liés depuis le formulaire ne sont pas liés (la requête, l’itinéraire et l’en-tête continuent de fonctionner). La méthode d’action fonctionne directement avec la `Request` propriété. Un `MultipartReader` est utilisé pour lire chaque section. Les données de clé/valeur sont stockées dans un `KeyValueAccumulator` . Une fois les sections en plusieurs parties lues, le contenu du `KeyValueAccumulator` est utilisé pour lier les données de formulaire à un type de modèle.

La `StreamingController.UploadDatabase` méthode complète pour la diffusion en continu vers une base de données avec EF Core :

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

`MultipartRequestHelper` (*Utilities/MultipartRequestHelper. cs*) :

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

Méthode complète `StreamingController.UploadPhysical` pour la diffusion en continu vers un emplacement physique :

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

Dans l’exemple d’application, les contrôles de validation sont gérés par `FileHelpers.ProcessStreamedFile` .

## <a name="validation"></a>Validation

La classe de l’exemple d’application `FileHelpers` illustre plusieurs vérifications des chargements de fichiers mis en mémoire tampon <xref:Microsoft.AspNetCore.Http.IFormFile> et diffusés en continu. Pour traiter les <xref:Microsoft.AspNetCore.Http.IFormFile> chargements de fichiers mis en mémoire tampon dans l’exemple d’application, consultez la `ProcessFormFile` méthode dans le fichier *Utilities/FileHelpers. cs* . Pour le traitement de fichiers en continu, consultez la `ProcessStreamedFile` méthode dans le même fichier.

> [!WARNING]
> Les méthodes de traitement de validation présentées dans l’exemple d’application n’analysent pas le contenu des fichiers téléchargés. Dans la plupart des scénarios de production, une API de détection de virus et de logiciels malveillants est utilisée sur le fichier avant que le fichier soit mis à la disposition des utilisateurs ou d’autres systèmes.
>
> Bien que l’exemple de rubrique fournit un exemple fonctionnel de techniques de validation, n’implémentez pas la `FileHelpers` classe dans une application de production, sauf si vous :
>
> * Comprenez pleinement l’implémentation.
> * Modifiez l’implémentation en fonction de l’environnement et des spécifications de l’application.
>
> **N’implémentez jamais non plus de code de sécurité dans une application sans répondre à ces exigences.**

### <a name="content-validation"></a>Validation du contenu

**Utilisez une API d’analyse de virus/programme malveillant sur le contenu chargé.**

L’analyse des fichiers exige des ressources serveur dans des scénarios de volume élevé. Si les performances de traitement des demandes sont réduites en raison de l’analyse de fichiers, envisagez de décharger le travail d’analyse vers un [service en arrière-plan](xref:fundamentals/host/hosted-services), éventuellement un service qui s’exécute sur un serveur différent du serveur de l’application. En règle générale, les fichiers téléchargés sont conservés dans une zone en quarantaine jusqu’à ce que l’antivirus en arrière-plan les vérifie. Lorsqu’un fichier réussit, le fichier est déplacé vers l’emplacement de stockage de fichiers normal. Ces étapes sont généralement effectuées conjointement avec un enregistrement de base de données qui indique l’état d’analyse d’un fichier. À l’aide de cette approche, l’application et le serveur d’applications restent concentrés sur la réponse aux requêtes.

### <a name="file-extension-validation"></a>Validation de l’extension de fichier

L’extension du fichier chargé doit être vérifiée par rapport à une liste d’extensions autorisées. Exemple :

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a>Validation de la signature de fichier

La signature d’un fichier est déterminée par les premiers octets au début d’un fichier. Ces octets peuvent être utilisés pour indiquer si l’extension correspond au contenu du fichier. L’exemple d’application vérifie les signatures de fichiers pour quelques types de fichiers courants. Dans l’exemple suivant, la signature de fichier pour une image JPEG est vérifiée par rapport au fichier :

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

Pour obtenir des signatures de fichiers supplémentaires, consultez la [base de données signatures de fichiers](https://www.filesignatures.net/) et les spécifications de fichier officielles.

### <a name="file-name-security"></a>Sécurité des noms de fichiers

N’utilisez jamais un nom de fichier fourni par le client pour enregistrer un fichier dans le stockage physique. Créez un nom de fichier sécurisé pour le fichier à l’aide de [Path. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) ou [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) pour créer un chemin d’accès complet (y compris le nom de fichier) pour le stockage temporaire.

Razor encode automatiquement les valeurs des propriétés pour l’affichage. Le code suivant peut être utilisé en toute sécurité :

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

En dehors de Razor , toujours <xref:System.Net.WebUtility.HtmlEncode*> le contenu du nom de fichier à partir de la demande d’un utilisateur.

De nombreuses implémentations doivent inclure une vérification de l’existence du fichier ; dans le cas contraire, le fichier est remplacé par un fichier du même nom. Fournissez une logique supplémentaire pour répondre aux spécifications de votre application.

### <a name="size-validation"></a>Validation de la taille

Limitez la taille des fichiers téléchargés.

Dans l’exemple d’application, la taille du fichier est limitée à 2 Mo (indiquée en octets). La limite est fournie via la [configuration](xref:fundamentals/configuration/index) à partir du *appsettings.json* fichier :

```json
{
  "FileSizeLimit": 2097152
}
```

`FileSizeLimit`Est injecté dans des `PageModel` classes :

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

Quand une taille de fichier dépasse la limite, le fichier est rejeté :

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a>Valeur de l’attribut de nom de correspondance avec le nom de paramètre de la méthode de publication

Dans les non- Razor formulaires qui publient des données de formulaire ou utilisent `FormData` directement JavaScript, le nom spécifié dans l’élément du formulaire ou `FormData` doit correspondre au nom du paramètre dans l’action du contrôleur.

Dans l’exemple suivant :

* Lorsque vous utilisez un `<input>` élément, l' `name` attribut est défini sur la valeur `battlePlans` :

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* Lors de l’utilisation `FormData` de dans JavaScript, le nom est défini sur la valeur `battlePlans` :

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

Utilisez un nom correspondant pour le paramètre de la méthode C# ( `battlePlans` ) :

* Pour une Razor méthode de gestionnaire de page pages nommée `Upload` :

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* Pour une méthode d’action du billet de contrôleur MVC :

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a>Configuration du serveur et de l’application

### <a name="multipart-body-length-limit"></a>Longueur limite du corps en plusieurs parties

<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> définit la limite de la longueur de chaque corps en plusieurs parties. Les sections de formulaire qui dépassent cette limite lèvent une lorsqu’elles sont <xref:System.IO.InvalidDataException> analysées. La valeur par défaut est 134 217 728 (128 Mo). Personnaliser la limite à l’aide du <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> paramètre dans `Startup.ConfigureServices` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> est utilisé pour définir le <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> pour une seule page ou action.

Dans une Razor application pages, appliquez le filtre avec une [Convention](xref:razor-pages/razor-pages-conventions) dans `Startup.ConfigureServices` :

```csharp
services.AddRazorPages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model.Filters.Add(
                new RequestFormLimitsAttribute()
                {
                    // Set the limit to 256 MB
                    MultipartBodyLengthLimit = 268435456
                });
});
```

Dans une Razor application de pages ou une application MVC, appliquez le filtre au modèle de page ou à la méthode d’action :

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a>Taille maximale du corps de la demande Kestrel

Pour les applications hébergées par Kestrel, la taille de corps de requête maximale par défaut est de 30 millions octets, soit environ 28,6 Mo. Personnaliser la limite à l’aide de l’option de serveur [MaxRequestBodySize](xref:fundamentals/servers/kestrel/options#maximum-request-body-size) Kestrel :

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel((context, options) =>
            {
                // Handle requests up to 50 MB
                options.Limits.MaxRequestBodySize = 52428800;
            })
            .UseStartup<Startup>();
        });
```

<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> est utilisé pour définir le [MaxRequestBodySize](xref:fundamentals/servers/kestrel/options#maximum-request-body-size) pour une seule page ou action.

Dans une Razor application pages, appliquez le filtre avec une [Convention](xref:razor-pages/razor-pages-conventions) dans `Startup.ConfigureServices` :

```csharp
services.AddRazorPages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model =>
            {
                // Handle requests up to 50 MB
                model.Filters.Add(
                    new RequestSizeLimitAttribute(52428800));
            });
});
```

Dans une Razor application de pages ou une application MVC, appliquez le filtre à la classe du gestionnaire de pages ou à la méthode d’action :

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

`RequestSizeLimitAttribute`Peut également être appliqué à l’aide de la [`@attribute`](xref:mvc/views/razor#attribute) Razor directive :

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a>Autres limites Kestrel

D’autres limites Kestrel peuvent s’appliquer aux applications hébergées par Kestrel :

* [Nombre maximale de connexions client](xref:fundamentals/servers/kestrel/options#maximum-client-connections)
* [Taux de données de demande et de réponse](xref:fundamentals/servers/kestrel/options#minimum-request-body-data-rate)

### <a name="iis"></a>IIS

La limite de demandes par défaut ( `maxAllowedContentLength` ) est de 30 millions octets, soit environ 28,6 Mo. Personnaliser la limite dans le `web.config` fichier. Dans l’exemple suivant, la limite est définie sur 50 Mo (52 428 800 octets) :

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

Le `maxAllowedContentLength` paramètre s’applique uniquement à IIS. Pour plus d’informations, consultez [limites `<requestLimits>` des demandes ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).

## <a name="troubleshoot"></a>Dépanner

Voici certains problèmes courants rencontrés avec le chargement de fichiers et leurs solutions possibles.

### <a name="not-found-error-when-deployed-to-an-iis-server"></a>Erreur introuvable lors du déploiement sur un serveur IIS

L’erreur suivante indique que le fichier chargé dépasse la longueur du contenu configurée du serveur :

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

Pour plus d’informations, consultez la section [IIS](#iis) .

### <a name="connection-failure"></a>Échec de connexion

Une erreur de connexion et une connexion du serveur de réinitialisation indiquent probablement que le fichier téléchargé dépasse la taille maximale du corps de la demande de Kestrel. Pour plus d’informations, consultez la section [taille maximale du corps de la demande Kestrel](#kestrel-maximum-request-body-size) . Les limites de connexion du client Kestrel peuvent également nécessiter des ajustements.

### <a name="null-reference-exception-with-iformfile"></a>Exception de référence null avec IFormFile

Si le contrôleur accepte les fichiers téléchargés à l’aide <xref:Microsoft.AspNetCore.Http.IFormFile> de mais que la valeur est `null` , vérifiez que le formulaire HTML spécifie la `enctype` valeur `multipart/form-data` . Si cet attribut n’est pas défini sur l' `<form>` élément, le chargement du fichier ne se produit pas et tous les <xref:Microsoft.AspNetCore.Http.IFormFile> arguments liés sont `null` . Vérifiez également que l' [appellation de chargement dans les données de formulaire correspond à celle de l’application](#match-name-attribute-value-to-parameter-name-of-post-method).

### <a name="stream-was-too-long"></a>Le flux est trop long

Les exemples de cette rubrique reposent sur <xref:System.IO.MemoryStream> pour stocker le contenu du fichier chargé. La limite de taille d’un `MemoryStream` est `int.MaxValue` . Si le scénario de chargement de fichiers de l’application nécessite la conservation d’un contenu de fichier d’une taille supérieure à 50 Mo, utilisez une autre approche qui ne repose pas sur un seul `MemoryStream` pour conserver le contenu d’un fichier chargé.

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

ASP.NET Core prend en charge le chargement d’un ou plusieurs fichiers à l’aide d’une liaison de modèle mise en mémoire tampon pour les fichiers plus petits et la diffusion en continu sans mise en mémoire tampon pour les fichiers plus volumineux

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="security-considerations"></a>Considérations relatives à la sécurité

Soyez prudent lorsque vous fournissez aux utilisateurs la possibilité de charger des fichiers sur un serveur. Les attaquants peuvent tenter d’effectuer les opérations suivantes :

* Exécuter [des attaques par déni de service](/windows-hardware/drivers/ifs/denial-of-service) .
* Télécharger des virus ou des programmes malveillants.
* Compromettre les réseaux et les serveurs d’une autre manière.

Les étapes de sécurité qui réduisent la probabilité d’une attaque réussie sont les suivantes :

* Chargez les fichiers dans une zone de chargement de fichier dédiée, de préférence sur un lecteur non-système. Un emplacement dédié permet d’imposer des restrictions de sécurité plus faciles sur les fichiers téléchargés. Désactivez les autorisations d’exécution sur l’emplacement de chargement du fichier.&dagger;
* Ne conservez **pas** les fichiers téléchargés dans la même arborescence de répertoires que l’application.&dagger;
* Utilisez un nom de fichier sécurisé déterminé par l’application. N’utilisez pas un nom de fichier fourni par l’utilisateur ou le nom de fichier non approuvé du fichier téléchargé. &dagger; Code HTML encode le nom de fichier non fiable lors de son affichage. Par exemple, la journalisation du nom de fichier ou l’affichage dans l’interface utilisateur ( Razor génère automatiquement le code html).
* Autorisez uniquement les extensions de fichier approuvées pour la spécification de conception de l’application.&dagger; <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* Vérifiez que les vérifications côté client sont effectuées sur le serveur. &dagger; Les contrôles côté client sont faciles à contourner.
* Vérifiez la taille d’un fichier téléchargé. Définissez une limite de taille maximale pour empêcher les chargements volumineux.&dagger;
* Lorsque les fichiers ne doivent pas être remplacés par un fichier téléchargé portant le même nom, vérifiez le nom du fichier par rapport à la base de données ou au stockage physique avant de charger le fichier.
* **Exécutez un programme de détection de virus et de logiciels malveillants sur le contenu chargé avant que le fichier ne soit stocké.**

&dagger;L’exemple d’application illustre une approche qui répond aux critères.

> [!WARNING]
> Le chargement d’un code malveillant sur un système est généralement la première étape de l’exécution de code capable de :
>
> * Maîtrisez complètement un système.
> * Surchargez un système avec le résultat du blocage du système.
> * Compromettre les données utilisateur ou système.
> * Appliquez Graffiti à une interface utilisateur publique.
>
> Pour plus d’informations sur la réduction de la surface d’attaque quand vous acceptez des fichiers d’utilisateurs, consultez les ressources suivantes :
>
> * [Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) (Chargement de fichiers illimité)
> * [Sécurité Azure : Vérifier que les contrôles appropriés sont en place quand vous acceptez des fichiers d’utilisateurs](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

Pour plus d’informations sur l’implémentation de mesures de sécurité, y compris des exemples de l’exemple d’application, consultez la section [validation](#validation) .

## <a name="storage-scenarios"></a>Scénarios de stockage

Les options de stockage courantes pour les fichiers sont les suivantes :

* Base de données

  * Pour les petits chargements de fichiers, une base de données est souvent plus rapide que les options de stockage physique (système de fichiers ou partage réseau).
  * Une base de données est souvent plus pratique que les options de stockage physique, car la récupération d’un enregistrement de base de données pour les données utilisateur peut fournir simultanément le contenu du fichier (par exemple, une image de l’avatar).
  * Une base de données est potentiellement moins coûteuse que l’utilisation d’un service de stockage de données.

* Stockage physique (système de fichiers ou partage réseau)

  * Pour les chargements de fichiers volumineux :
    * Les limites de base de données peuvent limiter la taille du téléchargement.
    * Le stockage physique est souvent moins économique que le stockage dans une base de données.
  * Le stockage physique est potentiellement moins onéreux que l’utilisation d’un service de stockage de données.
  * Le processus de l’application doit disposer d’autorisations en lecture et en écriture sur l’emplacement de stockage. **N’accordez jamais l’autorisation EXECUTE.**

* Service de stockage de données (par exemple, [stockage d’objets BLOB Azure](https://azure.microsoft.com/services/storage/blobs/))

  * Les services offrent généralement une évolutivité et une résilience améliorées par rapport aux solutions locales qui sont généralement sujettes à des points de défaillance uniques.
  * Les services sont potentiellement moins coûteux dans les scénarios d’infrastructure de stockage volumineux.

  Pour plus d’informations, consultez [démarrage rapide : utiliser .net pour créer un objet BLOB dans le stockage d’objets](/azure/storage/blobs/storage-quickstart-blobs-dotnet).

## <a name="file-upload-scenarios"></a>Scénarios de chargement de fichiers

La mise en mémoire tampon et la diffusion en continu sont deux approches générales pour le téléchargement de fichiers.

**des réponses**

La totalité du fichier est lue dans un <xref:Microsoft.AspNetCore.Http.IFormFile> , qui est une représentation C# du fichier utilisé pour traiter ou enregistrer le fichier.

Les ressources (disque, mémoire) utilisées par les chargements de fichiers dépendent du nombre et de la taille des chargements de fichiers simultanés. Si une application tente de mettre en mémoire tampon un trop grand nombre de chargements, le site se bloque lorsqu’il manque de mémoire ou d’espace disque. Si la taille ou la fréquence des chargements de fichiers épuisent les ressources d’application, utilisez la diffusion en continu.

> [!NOTE]
> Tout fichier mis en mémoire tampon dépassant 64 Ko est déplacé de la mémoire vers un fichier temporaire sur le disque.

La mise en mémoire tampon de petits fichiers est traitée dans les sections suivantes de cette rubrique :

* [Stockage physique](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [Base de données](#upload-small-files-with-buffered-model-binding-to-a-database)

**Streaming**

Le fichier est reçu à partir d’une demande en plusieurs parties et est directement traité ou enregistré par l’application. La diffusion en continu n’améliore pas les performances de manière significative. La diffusion en continu réduit les demandes de mémoire ou d’espace disque lors du chargement de fichiers.

La diffusion en continu de fichiers volumineux est traitée dans la section [chargement de fichiers volumineux avec diffusion](#upload-large-files-with-streaming) .

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a>Charger des fichiers de petite taille avec une liaison de modèle mise en mémoire tampon vers un stockage physique

Pour télécharger des fichiers de petite taille, utilisez un formulaire en plusieurs parties ou créez une requête de publication à l’aide de JavaScript.

L’exemple suivant illustre l’utilisation d’un Razor formulaire de pages pour télécharger un seul fichier (*pages/BufferedSingleFileUploadPhysical. cshtml* dans l’exemple d’application) :

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

L’exemple suivant est similaire à l’exemple précédent, à l’exception de :

* JavaScript ([API FETCH](https://developer.mozilla.org/docs/Web/API/Fetch_API)) est utilisé pour envoyer les données du formulaire.
* Il n’y a aucune validation.

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

Pour exécuter la publication de formulaire dans JavaScript pour les clients qui [ne prennent pas en charge l’API FETCH](https://caniuse.com/#feat=fetch), utilisez l’une des approches suivantes :

* Utilisez un Polyfill d’extraction (par exemple, [Window. Fetch Polyfill (GitHub/fetch)](https://github.com/github/fetch)).
* Utiliser `XMLHttpRequest`. Exemple :

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

Afin de prendre en charge les chargements de fichiers, les formulaires HTML doivent spécifier un type d’encodage ( `enctype` ) de `multipart/form-data` .

Pour `files` qu’un élément INPUT prenne en charge le téléchargement de plusieurs fichiers, fournissez l' `multiple` attribut sur l' `<input>` élément :

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

Les fichiers individuels téléchargés sur le serveur sont accessibles via la [liaison de modèle](xref:mvc/models/model-binding) à l’aide de <xref:Microsoft.AspNetCore.Http.IFormFile> . L’exemple d’application illustre plusieurs chargements de fichiers mis en mémoire tampon pour les scénarios de base de données et de stockage physique.

<a name="filename"></a>

> [!WARNING]
> N’utilisez **pas** la `FileName` propriété <xref:Microsoft.AspNetCore.Http.IFormFile> autre que pour l’affichage et la journalisation. Lors de l’affichage ou de la journalisation, le nom du fichier est encodé au format HTML. Une personne malveillante peut fournir un nom de fichier malveillant, y compris les chemins d’accès complets ou les chemins d’accès relatifs. Les applications doivent :
>
> * Supprimez le chemin d’accès du nom de fichier fourni par l’utilisateur.
> * Enregistrez le nom de fichier encodé au format HTML, avec chemin d’accès supprimé pour l’interface utilisateur ou la journalisation.
> * Générez un nouveau nom de fichier aléatoire pour le stockage.
>
> Le code suivant supprime le chemin d’accès du nom de fichier :
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> Les exemples fournis jusqu’à présent ne prennent pas en compte les considérations de sécurité. Des informations supplémentaires sont fournies par les sections suivantes et l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):
>
> * [Considérations relatives à la sécurité](#security-considerations)
> * [Validation](#validation)

Lors du chargement de fichiers à l’aide d’une liaison de modèle et <xref:Microsoft.AspNetCore.Http.IFormFile> , la méthode d’action peut accepter les éléments suivants :

* Une seule <xref:Microsoft.AspNetCore.Http.IFormFile> .
* L’un des regroupements suivants qui représentent plusieurs fichiers :
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * [Tarifs](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>>

> [!NOTE]
> La liaison correspond aux fichiers de formulaire par nom. Par exemple, la `name` valeur html dans `<input type="file" name="formFile">` doit correspondre au paramètre C#/lié à la propriété ( `FormFile` ). Pour plus d’informations, consultez la section valeur de l' [attribut de nom de correspondance pour le nom de paramètre de la méthode de publication](#match-name-attribute-value-to-parameter-name-of-post-method) .

L’exemple suivant :

* Effectue une boucle sur un ou plusieurs fichiers téléchargés.
* Utilise [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) pour retourner le chemin d’accès complet d’un fichier, y compris le nom de fichier. 
* Enregistre les fichiers dans le système de fichiers local à l’aide d’un nom de fichier généré par l’application.
* Retourne le nombre total et la taille des fichiers téléchargés.

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

Utilisez `Path.GetRandomFileName` pour générer un nom de fichier sans chemin d’accès. Dans l’exemple suivant, le chemin d’accès est obtenu à partir de la configuration :

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

Le chemin d’accès passé à <xref:System.IO.FileStream> *doit* inclure le nom de fichier. Si le nom de fichier n’est pas fourni, une <xref:System.UnauthorizedAccessException> exception est levée au moment de l’exécution.

Les fichiers chargés à l’aide de la <xref:Microsoft.AspNetCore.Http.IFormFile> technique sont mis en mémoire tampon ou sur disque sur le serveur avant le traitement. À l’intérieur de la méthode d’action, le <xref:Microsoft.AspNetCore.Http.IFormFile> contenu est accessible en tant que <xref:System.IO.Stream> . Outre le système de fichiers local, les fichiers peuvent être enregistrés sur un partage réseau ou un service de stockage de fichiers, tel que le [stockage d’objets BLOB Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).

Pour obtenir un autre exemple qui effectue une boucle sur plusieurs fichiers pour le téléchargement et utilise des noms de fichiers sécurisés, consultez *pages/BufferedMultipleFileUploadPhysical. cshtml. cs* dans l’exemple d’application.

> [!WARNING]
> [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) lève une exception <xref:System.IO.IOException> si plus de 65 535 fichiers sont créés sans supprimer les fichiers temporaires précédents. La limite de 65 535 fichiers est une limite par serveur. Pour plus d’informations sur cette limite sur le système d’exploitation Windows, consultez les notes dans les rubriques suivantes :
>
> * [GetTempFileNameA fonction)](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a>Charger des fichiers de petite taille avec une liaison de modèle mise en mémoire tampon vers une base de données

Pour stocker des données de fichier binaires dans une base de données à l’aide de [Entity Framework](/ef/core/index), définissez une <xref:System.Byte> propriété de tableau sur l’entité :

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

Spécifiez une propriété de modèle de page pour la classe qui comprend <xref:Microsoft.AspNetCore.Http.IFormFile> :

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Http.IFormFile> peut être utilisé directement comme paramètre de méthode d’action ou comme propriété de modèle liée. L’exemple précédent utilise une propriété de modèle liée.

Le `FileUpload` est utilisé dans le Razor formulaire pages :

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

Lorsque le formulaire est publié sur le serveur, copiez <xref:Microsoft.AspNetCore.Http.IFormFile> -le dans un flux et enregistrez-le en tant que tableau d’octets dans la base de données. Dans l’exemple suivant, `_dbContext` stocke le contexte de base de données de l’application :

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

L’exemple précédent est semblable à un scénario illustré dans l’exemple d’application :

* *Pages/BufferedSingleFileUploadDb. cshtml*
* *Pages/BufferedSingleFileUploadDb. cshtml. cs*

> [!WARNING]
> Soyez prudent quand vous stockez des données binaires dans des bases de données relationnelles, car cela peut avoir un impact négatif sur les performances.
>
> Ne vous fiez pas à la `FileName` propriété de sans validation ni à approuver celle-ci <xref:Microsoft.AspNetCore.Http.IFormFile> . La `FileName` propriété doit uniquement être utilisée à des fins d’affichage et uniquement après l’encodage HTML.
>
> Les exemples fournis ne prennent pas en compte les considérations de sécurité. Des informations supplémentaires sont fournies par les sections suivantes et l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):
>
> * [Considérations relatives à la sécurité](#security-considerations)
> * [Validation](#validation)

### <a name="upload-large-files-with-streaming"></a>Charger des fichiers volumineux avec streaming

L’exemple suivant montre comment utiliser JavaScript pour diffuser un fichier vers une action de contrôleur. Le jeton anti-contrefaçon du fichier est généré à l’aide d’un attribut de filtre personnalisé et transmis aux en-têtes HTTP du client plutôt que dans le corps de la demande. Étant donné que la méthode d’action traite directement les données chargées, la liaison de modèle de formulaire est désactivée par un autre filtre personnalisé. Dans l’action, le contenu du formulaire est lu en avec `MultipartReader`, qui lit chaque `MultipartSection` individuelle, traitant le fichier ou enregistrant le contenu selon ce qui est approprié. Une fois les sections en plusieurs parties lues, l’action effectue sa propre liaison de modèle.

La réponse de page initiale charge le formulaire et enregistre un jeton anti-contrefaçon dans un cookie (via l' `GenerateAntiforgeryTokenCookieAttribute` attribut). L’attribut utilise la [prise en charge de l’anticontrefaçon](xref:security/anti-request-forgery) intégrée de ASP.net Core pour définir un cookie avec un jeton de demande :

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

`DisableFormValueModelBindingAttribute`Est utilisé pour désactiver la liaison de modèle :

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

Dans l’exemple d’application, `GenerateAntiforgeryTokenCookieAttribute` et `DisableFormValueModelBindingAttribute` sont appliqués en tant que filtres aux modèles d’application de page de et à l' `/StreamedSingleFileUploadDb` aide des conventions de `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` [ Razor pages](xref:razor-pages/razor-pages-conventions):

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=7-10,16-19)]

Étant donné que la liaison de modèle ne lit pas le formulaire, les paramètres qui sont liés depuis le formulaire ne sont pas liés (la requête, l’itinéraire et l’en-tête continuent de fonctionner). La méthode d’action fonctionne directement avec la `Request` propriété. Un `MultipartReader` est utilisé pour lire chaque section. Les données de clé/valeur sont stockées dans un `KeyValueAccumulator` . Une fois les sections en plusieurs parties lues, le contenu du `KeyValueAccumulator` est utilisé pour lier les données de formulaire à un type de modèle.

La `StreamingController.UploadDatabase` méthode complète pour la diffusion en continu vers une base de données avec EF Core :

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

`MultipartRequestHelper` (*Utilities/MultipartRequestHelper. cs*) :

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

Méthode complète `StreamingController.UploadPhysical` pour la diffusion en continu vers un emplacement physique :

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

Dans l’exemple d’application, les contrôles de validation sont gérés par `FileHelpers.ProcessStreamedFile` .

## <a name="validation"></a>Validation

La classe de l’exemple d’application `FileHelpers` illustre plusieurs vérifications des chargements de fichiers mis en mémoire tampon <xref:Microsoft.AspNetCore.Http.IFormFile> et diffusés en continu. Pour traiter les <xref:Microsoft.AspNetCore.Http.IFormFile> chargements de fichiers mis en mémoire tampon dans l’exemple d’application, consultez la `ProcessFormFile` méthode dans le fichier *Utilities/FileHelpers. cs* . Pour le traitement de fichiers en continu, consultez la `ProcessStreamedFile` méthode dans le même fichier.

> [!WARNING]
> Les méthodes de traitement de validation présentées dans l’exemple d’application n’analysent pas le contenu des fichiers téléchargés. Dans la plupart des scénarios de production, une API de détection de virus et de logiciels malveillants est utilisée sur le fichier avant que le fichier soit mis à la disposition des utilisateurs ou d’autres systèmes.
>
> Bien que l’exemple de rubrique fournit un exemple fonctionnel de techniques de validation, n’implémentez pas la `FileHelpers` classe dans une application de production, sauf si vous :
>
> * Comprenez pleinement l’implémentation.
> * Modifiez l’implémentation en fonction de l’environnement et des spécifications de l’application.
>
> **N’implémentez jamais non plus de code de sécurité dans une application sans répondre à ces exigences.**

### <a name="content-validation"></a>Validation du contenu

**Utilisez une API d’analyse de virus/programme malveillant sur le contenu chargé.**

L’analyse des fichiers exige des ressources serveur dans des scénarios de volume élevé. Si les performances de traitement des demandes sont réduites en raison de l’analyse de fichiers, envisagez de décharger le travail d’analyse vers un [service en arrière-plan](xref:fundamentals/host/hosted-services), éventuellement un service qui s’exécute sur un serveur différent du serveur de l’application. En règle générale, les fichiers téléchargés sont conservés dans une zone en quarantaine jusqu’à ce que l’antivirus en arrière-plan les vérifie. Lorsqu’un fichier réussit, le fichier est déplacé vers l’emplacement de stockage de fichiers normal. Ces étapes sont généralement effectuées conjointement avec un enregistrement de base de données qui indique l’état d’analyse d’un fichier. À l’aide de cette approche, l’application et le serveur d’applications restent concentrés sur la réponse aux requêtes.

### <a name="file-extension-validation"></a>Validation de l’extension de fichier

L’extension du fichier chargé doit être vérifiée par rapport à une liste d’extensions autorisées. Exemple :

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a>Validation de la signature de fichier

La signature d’un fichier est déterminée par les premiers octets au début d’un fichier. Ces octets peuvent être utilisés pour indiquer si l’extension correspond au contenu du fichier. L’exemple d’application vérifie les signatures de fichiers pour quelques types de fichiers courants. Dans l’exemple suivant, la signature de fichier pour une image JPEG est vérifiée par rapport au fichier :

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

Pour obtenir des signatures de fichiers supplémentaires, consultez la [base de données signatures de fichiers](https://www.filesignatures.net/) et les spécifications de fichier officielles.

### <a name="file-name-security"></a>Sécurité des noms de fichiers

N’utilisez jamais un nom de fichier fourni par le client pour enregistrer un fichier dans le stockage physique. Créez un nom de fichier sécurisé pour le fichier à l’aide de [Path. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) ou [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) pour créer un chemin d’accès complet (y compris le nom de fichier) pour le stockage temporaire.

Razor encode automatiquement les valeurs des propriétés pour l’affichage. Le code suivant peut être utilisé en toute sécurité :

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

En dehors de Razor , toujours <xref:System.Net.WebUtility.HtmlEncode*> le contenu du nom de fichier à partir de la demande d’un utilisateur.

De nombreuses implémentations doivent inclure une vérification de l’existence du fichier ; dans le cas contraire, le fichier est remplacé par un fichier du même nom. Fournissez une logique supplémentaire pour répondre aux spécifications de votre application.

### <a name="size-validation"></a>Validation de la taille

Limitez la taille des fichiers téléchargés.

Dans l’exemple d’application, la taille du fichier est limitée à 2 Mo (indiquée en octets). La limite est fournie via la [configuration](xref:fundamentals/configuration/index) à partir du *appsettings.json* fichier :

```json
{
  "FileSizeLimit": 2097152
}
```

`FileSizeLimit`Est injecté dans des `PageModel` classes :

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

Quand une taille de fichier dépasse la limite, le fichier est rejeté :

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a>Valeur de l’attribut de nom de correspondance avec le nom de paramètre de la méthode de publication

Dans les non- Razor formulaires qui publient des données de formulaire ou utilisent `FormData` directement JavaScript, le nom spécifié dans l’élément du formulaire ou `FormData` doit correspondre au nom du paramètre dans l’action du contrôleur.

Dans l’exemple suivant :

* Lorsque vous utilisez un `<input>` élément, l' `name` attribut est défini sur la valeur `battlePlans` :

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* Lors de l’utilisation `FormData` de dans JavaScript, le nom est défini sur la valeur `battlePlans` :

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

Utilisez un nom correspondant pour le paramètre de la méthode C# ( `battlePlans` ) :

* Pour une Razor méthode de gestionnaire de page pages nommée `Upload` :

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* Pour une méthode d’action du billet de contrôleur MVC :

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a>Configuration du serveur et de l’application

### <a name="multipart-body-length-limit"></a>Longueur limite du corps en plusieurs parties

<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> définit la limite de la longueur de chaque corps en plusieurs parties. Les sections de formulaire qui dépassent cette limite lèvent une lorsqu’elles sont <xref:System.IO.InvalidDataException> analysées. La valeur par défaut est 134 217 728 (128 Mo). Personnaliser la limite à l’aide du <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> paramètre dans `Startup.ConfigureServices` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> est utilisé pour définir le <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> pour une seule page ou action.

Dans une Razor application pages, appliquez le filtre avec une [Convention](xref:razor-pages/razor-pages-conventions) dans `Startup.ConfigureServices` :

```csharp
services.AddRazorPages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model.Filters.Add(
                new RequestFormLimitsAttribute()
                {
                    // Set the limit to 256 MB
                    MultipartBodyLengthLimit = 268435456
                });
});
```

Dans une Razor application de pages ou une application MVC, appliquez le filtre au modèle de page ou à la méthode d’action :

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a>Taille maximale du corps de la demande Kestrel

Pour les applications hébergées par Kestrel, la taille de corps de requête maximale par défaut est de 30 millions octets, soit environ 28,6 Mo. Personnaliser la limite à l’aide de l’option de serveur [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel :

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel((context, options) =>
            {
                // Handle requests up to 50 MB
                options.Limits.MaxRequestBodySize = 52428800;
            })
            .UseStartup<Startup>();
        });
```

<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> est utilisé pour définir le [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) pour une seule page ou action.

Dans une Razor application pages, appliquez le filtre avec une [Convention](xref:razor-pages/razor-pages-conventions) dans `Startup.ConfigureServices` :

```csharp
services.AddRazorPages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model =>
            {
                // Handle requests up to 50 MB
                model.Filters.Add(
                    new RequestSizeLimitAttribute(52428800));
            });
});
```

Dans une Razor application de pages ou une application MVC, appliquez le filtre à la classe du gestionnaire de pages ou à la méthode d’action :

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

`RequestSizeLimitAttribute`Peut également être appliqué à l’aide de la [`@attribute`](xref:mvc/views/razor#attribute) Razor directive :

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a>Autres limites Kestrel

D’autres limites Kestrel peuvent s’appliquer aux applications hébergées par Kestrel :

* [Nombre maximale de connexions client](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [Taux de données de demande et de réponse](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a>IIS

La limite de demandes par défaut ( `maxAllowedContentLength` ) est de 30 millions octets, soit environ 28,6 Mo. Personnaliser la limite dans le `web.config` fichier. Dans l’exemple suivant, la limite est définie sur 50 Mo (52 428 800 octets) :

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

Le `maxAllowedContentLength` paramètre s’applique uniquement à IIS. Pour plus d’informations, consultez [limites `<requestLimits>` des demandes ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).

Augmentez la taille maximale du corps de la requête pour la requête HTTP en définissant <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> dans `Startup.ConfigureServices` . Dans l’exemple suivant, la limite est définie sur 50 Mo (52 428 800 octets) :

```csharp
services.Configure<IISServerOptions>(options =>
{
    options.MaxRequestBodySize = 52428800;
});
```

Pour plus d'informations, consultez <xref:host-and-deploy/iis/index#iis-options>.

## <a name="troubleshoot"></a>Dépanner

Voici certains problèmes courants rencontrés avec le chargement de fichiers et leurs solutions possibles.

### <a name="not-found-error-when-deployed-to-an-iis-server"></a>Erreur introuvable lors du déploiement sur un serveur IIS

L’erreur suivante indique que le fichier chargé dépasse la longueur du contenu configurée du serveur :

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

Pour plus d’informations, consultez la section [IIS](#iis) .

### <a name="connection-failure"></a>Échec de connexion

Une erreur de connexion et une connexion du serveur de réinitialisation indiquent probablement que le fichier téléchargé dépasse la taille maximale du corps de la demande de Kestrel. Pour plus d’informations, consultez la section [taille maximale du corps de la demande Kestrel](#kestrel-maximum-request-body-size) . Les limites de connexion du client Kestrel peuvent également nécessiter des ajustements.

### <a name="null-reference-exception-with-iformfile"></a>Exception de référence null avec IFormFile

Si le contrôleur accepte les fichiers téléchargés à l’aide <xref:Microsoft.AspNetCore.Http.IFormFile> de mais que la valeur est `null` , vérifiez que le formulaire HTML spécifie la `enctype` valeur `multipart/form-data` . Si cet attribut n’est pas défini sur l' `<form>` élément, le chargement du fichier ne se produit pas et tous les <xref:Microsoft.AspNetCore.Http.IFormFile> arguments liés sont `null` . Vérifiez également que l' [appellation de chargement dans les données de formulaire correspond à celle de l’application](#match-name-attribute-value-to-parameter-name-of-post-method).

### <a name="stream-was-too-long"></a>Le flux est trop long

Les exemples de cette rubrique reposent sur <xref:System.IO.MemoryStream> pour stocker le contenu du fichier chargé. La limite de taille d’un `MemoryStream` est `int.MaxValue` . Si le scénario de chargement de fichiers de l’application nécessite la conservation d’un contenu de fichier d’une taille supérieure à 50 Mo, utilisez une autre approche qui ne repose pas sur un seul `MemoryStream` pour conserver le contenu d’un fichier chargé.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core prend en charge le chargement d’un ou plusieurs fichiers à l’aide d’une liaison de modèle mise en mémoire tampon pour les fichiers plus petits et la diffusion en continu sans mise en mémoire tampon pour les fichiers plus volumineux

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="security-considerations"></a>Considérations relatives à la sécurité

Soyez prudent lorsque vous fournissez aux utilisateurs la possibilité de charger des fichiers sur un serveur. Les attaquants peuvent tenter d’effectuer les opérations suivantes :

* Exécuter [des attaques par déni de service](/windows-hardware/drivers/ifs/denial-of-service) .
* Télécharger des virus ou des programmes malveillants.
* Compromettre les réseaux et les serveurs d’une autre manière.

Les étapes de sécurité qui réduisent la probabilité d’une attaque réussie sont les suivantes :

* Chargez les fichiers dans une zone de chargement de fichier dédiée, de préférence sur un lecteur non-système. Un emplacement dédié permet d’imposer des restrictions de sécurité plus faciles sur les fichiers téléchargés. Désactivez les autorisations d’exécution sur l’emplacement de chargement du fichier.&dagger;
* Ne conservez **pas** les fichiers téléchargés dans la même arborescence de répertoires que l’application.&dagger;
* Utilisez un nom de fichier sécurisé déterminé par l’application. N’utilisez pas un nom de fichier fourni par l’utilisateur ou le nom de fichier non approuvé du fichier téléchargé. &dagger; Code HTML encode le nom de fichier non fiable lors de son affichage. Par exemple, la journalisation du nom de fichier ou l’affichage dans l’interface utilisateur ( Razor génère automatiquement le code html).
* Autorisez uniquement les extensions de fichier approuvées pour la spécification de conception de l’application.&dagger; <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* Vérifiez que les vérifications côté client sont effectuées sur le serveur. &dagger; Les contrôles côté client sont faciles à contourner.
* Vérifiez la taille d’un fichier téléchargé. Définissez une limite de taille maximale pour empêcher les chargements volumineux.&dagger;
* Lorsque les fichiers ne doivent pas être remplacés par un fichier téléchargé portant le même nom, vérifiez le nom du fichier par rapport à la base de données ou au stockage physique avant de charger le fichier.
* **Exécutez un programme de détection de virus et de logiciels malveillants sur le contenu chargé avant que le fichier ne soit stocké.**

&dagger;L’exemple d’application illustre une approche qui répond aux critères.

> [!WARNING]
> Le chargement d’un code malveillant sur un système est généralement la première étape de l’exécution de code capable de :
>
> * Maîtrisez complètement un système.
> * Surchargez un système avec le résultat du blocage du système.
> * Compromettre les données utilisateur ou système.
> * Appliquez Graffiti à une interface utilisateur publique.
>
> Pour plus d’informations sur la réduction de la surface d’attaque quand vous acceptez des fichiers d’utilisateurs, consultez les ressources suivantes :
>
> * [Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) (Chargement de fichiers illimité)
> * [Sécurité Azure : Vérifier que les contrôles appropriés sont en place quand vous acceptez des fichiers d’utilisateurs](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

Pour plus d’informations sur l’implémentation de mesures de sécurité, y compris des exemples de l’exemple d’application, consultez la section [validation](#validation) .

## <a name="storage-scenarios"></a>Scénarios de stockage

Les options de stockage courantes pour les fichiers sont les suivantes :

* Base de données

  * Pour les petits chargements de fichiers, une base de données est souvent plus rapide que les options de stockage physique (système de fichiers ou partage réseau).
  * Une base de données est souvent plus pratique que les options de stockage physique, car la récupération d’un enregistrement de base de données pour les données utilisateur peut fournir simultanément le contenu du fichier (par exemple, une image de l’avatar).
  * Une base de données est potentiellement moins coûteuse que l’utilisation d’un service de stockage de données.

* Stockage physique (système de fichiers ou partage réseau)

  * Pour les chargements de fichiers volumineux :
    * Les limites de base de données peuvent limiter la taille du téléchargement.
    * Le stockage physique est souvent moins économique que le stockage dans une base de données.
  * Le stockage physique est potentiellement moins onéreux que l’utilisation d’un service de stockage de données.
  * Le processus de l’application doit disposer d’autorisations en lecture et en écriture sur l’emplacement de stockage. **N’accordez jamais l’autorisation EXECUTE.**

* Service de stockage de données (par exemple, [stockage d’objets BLOB Azure](https://azure.microsoft.com/services/storage/blobs/))

  * Les services offrent généralement une évolutivité et une résilience améliorées par rapport aux solutions locales qui sont généralement sujettes à des points de défaillance uniques.
  * Les services sont potentiellement moins coûteux dans les scénarios d’infrastructure de stockage volumineux.

  Pour plus d’informations, consultez [démarrage rapide : utiliser .net pour créer un objet BLOB dans le stockage d’objets](/azure/storage/blobs/storage-quickstart-blobs-dotnet). La rubrique montre <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*> , mais <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> peut être utilisée pour enregistrer un <xref:System.IO.FileStream> dans le stockage d’objets BLOB quand vous utilisez un <xref:System.IO.Stream> .

## <a name="file-upload-scenarios"></a>Scénarios de chargement de fichiers

La mise en mémoire tampon et la diffusion en continu sont deux approches générales pour le téléchargement de fichiers.

**des réponses**

La totalité du fichier est lue dans un <xref:Microsoft.AspNetCore.Http.IFormFile> , qui est une représentation C# du fichier utilisé pour traiter ou enregistrer le fichier.

Les ressources (disque, mémoire) utilisées par les chargements de fichiers dépendent du nombre et de la taille des chargements de fichiers simultanés. Si une application tente de mettre en mémoire tampon un trop grand nombre de chargements, le site se bloque lorsqu’il manque de mémoire ou d’espace disque. Si la taille ou la fréquence des chargements de fichiers épuisent les ressources d’application, utilisez la diffusion en continu.

> [!NOTE]
> Tout fichier mis en mémoire tampon dépassant 64 Ko est déplacé de la mémoire vers un fichier temporaire sur le disque.

La mise en mémoire tampon de petits fichiers est traitée dans les sections suivantes de cette rubrique :

* [Stockage physique](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [Base de données](#upload-small-files-with-buffered-model-binding-to-a-database)

**Streaming**

Le fichier est reçu à partir d’une demande en plusieurs parties et est directement traité ou enregistré par l’application. La diffusion en continu n’améliore pas les performances de manière significative. La diffusion en continu réduit les demandes de mémoire ou d’espace disque lors du chargement de fichiers.

La diffusion en continu de fichiers volumineux est traitée dans la section [chargement de fichiers volumineux avec diffusion](#upload-large-files-with-streaming) .

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a>Charger des fichiers de petite taille avec une liaison de modèle mise en mémoire tampon vers un stockage physique

Pour télécharger des fichiers de petite taille, utilisez un formulaire en plusieurs parties ou créez une requête de publication à l’aide de JavaScript.

L’exemple suivant illustre l’utilisation d’un Razor formulaire de pages pour télécharger un seul fichier (*pages/BufferedSingleFileUploadPhysical. cshtml* dans l’exemple d’application) :

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

L’exemple suivant est similaire à l’exemple précédent, à l’exception de :

* JavaScript ([API FETCH](https://developer.mozilla.org/docs/Web/API/Fetch_API)) est utilisé pour envoyer les données du formulaire.
* Il n’y a aucune validation.

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

Pour exécuter la publication de formulaire dans JavaScript pour les clients qui [ne prennent pas en charge l’API FETCH](https://caniuse.com/#feat=fetch), utilisez l’une des approches suivantes :

* Utilisez un Polyfill d’extraction (par exemple, [Window. Fetch Polyfill (GitHub/fetch)](https://github.com/github/fetch)).
* Utiliser `XMLHttpRequest`. Exemple :

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

Afin de prendre en charge les chargements de fichiers, les formulaires HTML doivent spécifier un type d’encodage ( `enctype` ) de `multipart/form-data` .

Pour `files` qu’un élément INPUT prenne en charge le téléchargement de plusieurs fichiers, fournissez l' `multiple` attribut sur l' `<input>` élément :

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

Les fichiers individuels téléchargés sur le serveur sont accessibles via la [liaison de modèle](xref:mvc/models/model-binding) à l’aide de <xref:Microsoft.AspNetCore.Http.IFormFile> . L’exemple d’application illustre plusieurs chargements de fichiers mis en mémoire tampon pour les scénarios de base de données et de stockage physique.

<a name="filename2"></a>

> [!WARNING]
> N’utilisez **pas** la `FileName` propriété <xref:Microsoft.AspNetCore.Http.IFormFile> autre que pour l’affichage et la journalisation. Lors de l’affichage ou de la journalisation, le nom du fichier est encodé au format HTML. Une personne malveillante peut fournir un nom de fichier malveillant, y compris les chemins d’accès complets ou les chemins d’accès relatifs. Les applications doivent :
>
> * Supprimez le chemin d’accès du nom de fichier fourni par l’utilisateur.
> * Enregistrez le nom de fichier encodé au format HTML, avec chemin d’accès supprimé pour l’interface utilisateur ou la journalisation.
> * Générez un nouveau nom de fichier aléatoire pour le stockage.
>
> Le code suivant supprime le chemin d’accès du nom de fichier :
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> Les exemples fournis jusqu’à présent ne prennent pas en compte les considérations de sécurité. Des informations supplémentaires sont fournies par les sections suivantes et l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):
>
> * [Considérations relatives à la sécurité](#security-considerations)
> * [Validation](#validation)

Lors du chargement de fichiers à l’aide d’une liaison de modèle et <xref:Microsoft.AspNetCore.Http.IFormFile> , la méthode d’action peut accepter les éléments suivants :

* Une seule <xref:Microsoft.AspNetCore.Http.IFormFile> .
* L’un des regroupements suivants qui représentent plusieurs fichiers :
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * [Tarifs](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>>

> [!NOTE]
> La liaison correspond aux fichiers de formulaire par nom. Par exemple, la `name` valeur html dans `<input type="file" name="formFile">` doit correspondre au paramètre C#/lié à la propriété ( `FormFile` ). Pour plus d’informations, consultez la section valeur de l' [attribut de nom de correspondance pour le nom de paramètre de la méthode de publication](#match-name-attribute-value-to-parameter-name-of-post-method) .

L’exemple suivant :

* Effectue une boucle sur un ou plusieurs fichiers téléchargés.
* Utilise [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) pour retourner le chemin d’accès complet d’un fichier, y compris le nom de fichier. 
* Enregistre les fichiers dans le système de fichiers local à l’aide d’un nom de fichier généré par l’application.
* Retourne le nombre total et la taille des fichiers téléchargés.

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

Utilisez `Path.GetRandomFileName` pour générer un nom de fichier sans chemin d’accès. Dans l’exemple suivant, le chemin d’accès est obtenu à partir de la configuration :

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

Le chemin d’accès passé à <xref:System.IO.FileStream> *doit* inclure le nom de fichier. Si le nom de fichier n’est pas fourni, une <xref:System.UnauthorizedAccessException> exception est levée au moment de l’exécution.

Les fichiers chargés à l’aide de la <xref:Microsoft.AspNetCore.Http.IFormFile> technique sont mis en mémoire tampon ou sur disque sur le serveur avant le traitement. À l’intérieur de la méthode d’action, le <xref:Microsoft.AspNetCore.Http.IFormFile> contenu est accessible en tant que <xref:System.IO.Stream> . Outre le système de fichiers local, les fichiers peuvent être enregistrés sur un partage réseau ou un service de stockage de fichiers, tel que le [stockage d’objets BLOB Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).

Pour obtenir un autre exemple qui effectue une boucle sur plusieurs fichiers pour le téléchargement et utilise des noms de fichiers sécurisés, consultez *pages/BufferedMultipleFileUploadPhysical. cshtml. cs* dans l’exemple d’application.

> [!WARNING]
> [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) lève une exception <xref:System.IO.IOException> si plus de 65 535 fichiers sont créés sans supprimer les fichiers temporaires précédents. La limite de 65 535 fichiers est une limite par serveur. Pour plus d’informations sur cette limite sur le système d’exploitation Windows, consultez les notes dans les rubriques suivantes :
>
> * [GetTempFileNameA fonction)](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a>Charger des fichiers de petite taille avec une liaison de modèle mise en mémoire tampon vers une base de données

Pour stocker des données de fichier binaires dans une base de données à l’aide de [Entity Framework](/ef/core/index), définissez une <xref:System.Byte> propriété de tableau sur l’entité :

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

Spécifiez une propriété de modèle de page pour la classe qui comprend <xref:Microsoft.AspNetCore.Http.IFormFile> :

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Http.IFormFile> peut être utilisé directement comme paramètre de méthode d’action ou comme propriété de modèle liée. L’exemple précédent utilise une propriété de modèle liée.

Le `FileUpload` est utilisé dans le Razor formulaire pages :

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

Lorsque le formulaire est publié sur le serveur, copiez <xref:Microsoft.AspNetCore.Http.IFormFile> -le dans un flux et enregistrez-le en tant que tableau d’octets dans la base de données. Dans l’exemple suivant, `_dbContext` stocke le contexte de base de données de l’application :

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

L’exemple précédent est semblable à un scénario illustré dans l’exemple d’application :

* *Pages/BufferedSingleFileUploadDb. cshtml*
* *Pages/BufferedSingleFileUploadDb. cshtml. cs*

> [!WARNING]
> Soyez prudent quand vous stockez des données binaires dans des bases de données relationnelles, car cela peut avoir un impact négatif sur les performances.
>
> Ne vous fiez pas à la `FileName` propriété de sans validation ni à approuver celle-ci <xref:Microsoft.AspNetCore.Http.IFormFile> . La `FileName` propriété doit uniquement être utilisée à des fins d’affichage et uniquement après l’encodage HTML.
>
> Les exemples fournis ne prennent pas en compte les considérations de sécurité. Des informations supplémentaires sont fournies par les sections suivantes et l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):
>
> * [Considérations relatives à la sécurité](#security-considerations)
> * [Validation](#validation)

### <a name="upload-large-files-with-streaming"></a>Charger des fichiers volumineux avec streaming

L’exemple suivant montre comment utiliser JavaScript pour diffuser un fichier vers une action de contrôleur. Le jeton anti-contrefaçon du fichier est généré à l’aide d’un attribut de filtre personnalisé et transmis aux en-têtes HTTP du client plutôt que dans le corps de la demande. Étant donné que la méthode d’action traite directement les données chargées, la liaison de modèle de formulaire est désactivée par un autre filtre personnalisé. Dans l’action, le contenu du formulaire est lu en avec `MultipartReader`, qui lit chaque `MultipartSection` individuelle, traitant le fichier ou enregistrant le contenu selon ce qui est approprié. Une fois les sections en plusieurs parties lues, l’action effectue sa propre liaison de modèle.

La réponse de page initiale charge le formulaire et enregistre un jeton anti-contrefaçon dans un cookie (via l' `GenerateAntiforgeryTokenCookieAttribute` attribut). L’attribut utilise la [prise en charge de l’anticontrefaçon](xref:security/anti-request-forgery) intégrée de ASP.net Core pour définir un cookie avec un jeton de demande :

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

`DisableFormValueModelBindingAttribute`Est utilisé pour désactiver la liaison de modèle :

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

Dans l’exemple d’application, `GenerateAntiforgeryTokenCookieAttribute` et `DisableFormValueModelBindingAttribute` sont appliqués en tant que filtres aux modèles d’application de page de et à l' `/StreamedSingleFileUploadDb` aide des conventions de `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` [ Razor pages](xref:razor-pages/razor-pages-conventions):

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

Étant donné que la liaison de modèle ne lit pas le formulaire, les paramètres qui sont liés depuis le formulaire ne sont pas liés (la requête, l’itinéraire et l’en-tête continuent de fonctionner). La méthode d’action fonctionne directement avec la `Request` propriété. Un `MultipartReader` est utilisé pour lire chaque section. Les données de clé/valeur sont stockées dans un `KeyValueAccumulator` . Une fois les sections en plusieurs parties lues, le contenu du `KeyValueAccumulator` est utilisé pour lier les données de formulaire à un type de modèle.

La `StreamingController.UploadDatabase` méthode complète pour la diffusion en continu vers une base de données avec EF Core :

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

`MultipartRequestHelper` (*Utilities/MultipartRequestHelper. cs*) :

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

Méthode complète `StreamingController.UploadPhysical` pour la diffusion en continu vers un emplacement physique :

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

Dans l’exemple d’application, les contrôles de validation sont gérés par `FileHelpers.ProcessStreamedFile` .

## <a name="validation"></a>Validation

La classe de l’exemple d’application `FileHelpers` illustre plusieurs vérifications des chargements de fichiers mis en mémoire tampon <xref:Microsoft.AspNetCore.Http.IFormFile> et diffusés en continu. Pour traiter les <xref:Microsoft.AspNetCore.Http.IFormFile> chargements de fichiers mis en mémoire tampon dans l’exemple d’application, consultez la `ProcessFormFile` méthode dans le fichier *Utilities/FileHelpers. cs* . Pour le traitement de fichiers en continu, consultez la `ProcessStreamedFile` méthode dans le même fichier.

> [!WARNING]
> Les méthodes de traitement de validation présentées dans l’exemple d’application n’analysent pas le contenu des fichiers téléchargés. Dans la plupart des scénarios de production, une API de détection de virus et de logiciels malveillants est utilisée sur le fichier avant que le fichier soit mis à la disposition des utilisateurs ou d’autres systèmes.
>
> Bien que l’exemple de rubrique fournit un exemple fonctionnel de techniques de validation, n’implémentez pas la `FileHelpers` classe dans une application de production, sauf si vous :
>
> * Comprenez pleinement l’implémentation.
> * Modifiez l’implémentation en fonction de l’environnement et des spécifications de l’application.
>
> **N’implémentez jamais non plus de code de sécurité dans une application sans répondre à ces exigences.**

### <a name="content-validation"></a>Validation du contenu

**Utilisez une API d’analyse de virus/programme malveillant sur le contenu chargé.**

L’analyse des fichiers exige des ressources serveur dans des scénarios de volume élevé. Si les performances de traitement des demandes sont réduites en raison de l’analyse de fichiers, envisagez de décharger le travail d’analyse vers un [service en arrière-plan](xref:fundamentals/host/hosted-services), éventuellement un service qui s’exécute sur un serveur différent du serveur de l’application. En règle générale, les fichiers téléchargés sont conservés dans une zone en quarantaine jusqu’à ce que l’antivirus en arrière-plan les vérifie. Lorsqu’un fichier réussit, le fichier est déplacé vers l’emplacement de stockage de fichiers normal. Ces étapes sont généralement effectuées conjointement avec un enregistrement de base de données qui indique l’état d’analyse d’un fichier. À l’aide de cette approche, l’application et le serveur d’applications restent concentrés sur la réponse aux requêtes.

### <a name="file-extension-validation"></a>Validation de l’extension de fichier

L’extension du fichier chargé doit être vérifiée par rapport à une liste d’extensions autorisées. Exemple :

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a>Validation de la signature de fichier

La signature d’un fichier est déterminée par les premiers octets au début d’un fichier. Ces octets peuvent être utilisés pour indiquer si l’extension correspond au contenu du fichier. L’exemple d’application vérifie les signatures de fichiers pour quelques types de fichiers courants. Dans l’exemple suivant, la signature de fichier pour une image JPEG est vérifiée par rapport au fichier :

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

Pour obtenir des signatures de fichiers supplémentaires, consultez la [base de données signatures de fichiers](https://www.filesignatures.net/) et les spécifications de fichier officielles.

### <a name="file-name-security"></a>Sécurité des noms de fichiers

N’utilisez jamais un nom de fichier fourni par le client pour enregistrer un fichier dans le stockage physique. Créez un nom de fichier sécurisé pour le fichier à l’aide de [Path. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) ou [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) pour créer un chemin d’accès complet (y compris le nom de fichier) pour le stockage temporaire.

Razor encode automatiquement les valeurs des propriétés pour l’affichage. Le code suivant peut être utilisé en toute sécurité :

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

En dehors de Razor , toujours <xref:System.Net.WebUtility.HtmlEncode*> le contenu du nom de fichier à partir de la demande d’un utilisateur.

De nombreuses implémentations doivent inclure une vérification de l’existence du fichier ; dans le cas contraire, le fichier est remplacé par un fichier du même nom. Fournissez une logique supplémentaire pour répondre aux spécifications de votre application.

### <a name="size-validation"></a>Validation de la taille

Limitez la taille des fichiers téléchargés.

Dans l’exemple d’application, la taille du fichier est limitée à 2 Mo (indiquée en octets). La limite est fournie via la [configuration](xref:fundamentals/configuration/index) à partir du *appsettings.json* fichier :

```json
{
  "FileSizeLimit": 2097152
}
```

`FileSizeLimit`Est injecté dans des `PageModel` classes :

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

Quand une taille de fichier dépasse la limite, le fichier est rejeté :

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a>Valeur de l’attribut de nom de correspondance avec le nom de paramètre de la méthode de publication

Dans les non- Razor formulaires qui publient des données de formulaire ou utilisent `FormData` directement JavaScript, le nom spécifié dans l’élément du formulaire ou `FormData` doit correspondre au nom du paramètre dans l’action du contrôleur.

Dans l’exemple suivant :

* Lorsque vous utilisez un `<input>` élément, l' `name` attribut est défini sur la valeur `battlePlans` :

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* Lors de l’utilisation `FormData` de dans JavaScript, le nom est défini sur la valeur `battlePlans` :

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

Utilisez un nom correspondant pour le paramètre de la méthode C# ( `battlePlans` ) :

* Pour une Razor méthode de gestionnaire de page pages nommée `Upload` :

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* Pour une méthode d’action du billet de contrôleur MVC :

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a>Configuration du serveur et de l’application

### <a name="multipart-body-length-limit"></a>Longueur limite du corps en plusieurs parties

<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> définit la limite de la longueur de chaque corps en plusieurs parties. Les sections de formulaire qui dépassent cette limite lèvent une lorsqu’elles sont <xref:System.IO.InvalidDataException> analysées. La valeur par défaut est 134 217 728 (128 Mo). Personnaliser la limite à l’aide du <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> paramètre dans `Startup.ConfigureServices` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> est utilisé pour définir le <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> pour une seule page ou action.

Dans une Razor application pages, appliquez le filtre avec une [Convention](xref:razor-pages/razor-pages-conventions) dans `Startup.ConfigureServices` :

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model.Filters.Add(
                    new RequestFormLimitsAttribute()
                    {
                        // Set the limit to 256 MB
                        MultipartBodyLengthLimit = 268435456
                    });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

Dans une Razor application de pages ou une application MVC, appliquez le filtre au modèle de page ou à la méthode d’action :

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a>Taille maximale du corps de la demande Kestrel

Pour les applications hébergées par Kestrel, la taille de corps de requête maximale par défaut est de 30 millions octets, soit environ 28,6 Mo. Personnaliser la limite à l’aide de l’option de serveur [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel :

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            // Handle requests up to 50 MB
            options.Limits.MaxRequestBodySize = 52428800;
        });
```

<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> est utilisé pour définir le [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) pour une seule page ou action.

Dans une Razor application pages, appliquez le filtre avec une [Convention](xref:razor-pages/razor-pages-conventions) dans `Startup.ConfigureServices` :

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model =>
                {
                    // Handle requests up to 50 MB
                    model.Filters.Add(
                        new RequestSizeLimitAttribute(52428800));
                });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

Dans une Razor application de pages ou une application MVC, appliquez le filtre à la classe du gestionnaire de pages ou à la méthode d’action :

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a>Autres limites Kestrel

D’autres limites Kestrel peuvent s’appliquer aux applications hébergées par Kestrel :

* [Nombre maximale de connexions client](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [Taux de données de demande et de réponse](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a>IIS

La limite de demandes par défaut ( `maxAllowedContentLength` ) est de 30 millions octets, soit environ 28,6 Mo. Personnaliser la limite dans le `web.config` fichier. Dans l’exemple suivant, la limite est définie sur 50 Mo (52 428 800 octets) :

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

Le `maxAllowedContentLength` paramètre s’applique uniquement à IIS. Pour plus d’informations, consultez [limites `<requestLimits>` des demandes ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).

Augmentez la taille maximale du corps de la requête pour la requête HTTP en définissant <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> dans `Startup.ConfigureServices` . Dans l’exemple suivant, la limite est définie sur 50 Mo (52 428 800 octets) :

```csharp
services.Configure<IISServerOptions>(options =>
{
    options.MaxRequestBodySize = 52428800;
});
```

Pour plus d'informations, consultez <xref:host-and-deploy/iis/index#iis-options>.

## <a name="troubleshoot"></a>Dépanner

Voici certains problèmes courants rencontrés avec le chargement de fichiers et leurs solutions possibles.

### <a name="not-found-error-when-deployed-to-an-iis-server"></a>Erreur introuvable lors du déploiement sur un serveur IIS

L’erreur suivante indique que le fichier chargé dépasse la longueur du contenu configurée du serveur :

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

Pour plus d’informations, consultez la section [IIS](#iis) .

### <a name="connection-failure"></a>Échec de connexion

Une erreur de connexion et une connexion du serveur de réinitialisation indiquent probablement que le fichier téléchargé dépasse la taille maximale du corps de la demande de Kestrel. Pour plus d’informations, consultez la section [taille maximale du corps de la demande Kestrel](#kestrel-maximum-request-body-size) . Les limites de connexion du client Kestrel peuvent également nécessiter des ajustements.

### <a name="null-reference-exception-with-iformfile"></a>Exception de référence null avec IFormFile

Si le contrôleur accepte les fichiers téléchargés à l’aide <xref:Microsoft.AspNetCore.Http.IFormFile> de mais que la valeur est `null` , vérifiez que le formulaire HTML spécifie la `enctype` valeur `multipart/form-data` . Si cet attribut n’est pas défini sur l' `<form>` élément, le chargement du fichier ne se produit pas et tous les <xref:Microsoft.AspNetCore.Http.IFormFile> arguments liés sont `null` . Vérifiez également que l' [appellation de chargement dans les données de formulaire correspond à celle de l’application](#match-name-attribute-value-to-parameter-name-of-post-method).

### <a name="stream-was-too-long"></a>Le flux est trop long

Les exemples de cette rubrique reposent sur <xref:System.IO.MemoryStream> pour stocker le contenu du fichier chargé. La limite de taille d’un `MemoryStream` est `int.MaxValue` . Si le scénario de chargement de fichiers de l’application nécessite la conservation d’un contenu de fichier d’une taille supérieure à 50 Mo, utilisez une autre approche qui ne repose pas sur un seul `MemoryStream` pour conserver le contenu d’un fichier chargé.

::: moniker-end


## <a name="additional-resources"></a>Ressources supplémentaires

::: moniker range="< aspnetcore-5.0"
* [Vidange des demandes de connexion HTTP](xref:fundamentals/servers/kestrel#http11-request-draining)
::: moniker-end
::: moniker range=">= aspnetcore-5.0"
* [Vidange des demandes de connexion HTTP](xref:fundamentals/servers/kestrel/request-draining)
::: moniker-end

* [Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) (Chargement de fichiers illimité)
* [Sécurité Azure : frame de sécurité : validation des entrées | Atténuations](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [Modèles de conception de Cloud Azure : modèle de clé valet](/azure/architecture/patterns/valet-key)
