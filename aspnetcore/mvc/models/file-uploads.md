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
ms.openlocfilehash: 90bde63ac94ba3fd29a067962989cf773ec613db
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587233"
---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="317d5-103">Charger des fichiers dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="317d5-103">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="317d5-104">Par [Steve Smith](https://ardalis.com/) et [Rutger Storm](https://github.com/rutix)</span><span class="sxs-lookup"><span data-stu-id="317d5-104">By [Steve Smith](https://ardalis.com/) and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="317d5-105">ASP.NET Core prend en charge le chargement d’un ou plusieurs fichiers à l’aide d’une liaison de modèle mise en mémoire tampon pour les fichiers plus petits et la diffusion en continu sans mise en mémoire tampon pour les fichiers plus volumineux</span><span class="sxs-lookup"><span data-stu-id="317d5-105">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="317d5-106">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/file-uploads/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="317d5-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="317d5-107">Considérations relatives à la sécurité</span><span class="sxs-lookup"><span data-stu-id="317d5-107">Security considerations</span></span>

<span data-ttu-id="317d5-108">Soyez prudent lorsque vous fournissez aux utilisateurs la possibilité de charger des fichiers sur un serveur.</span><span class="sxs-lookup"><span data-stu-id="317d5-108">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="317d5-109">Les attaquants peuvent tenter d’effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="317d5-109">Attackers may attempt to:</span></span>

* <span data-ttu-id="317d5-110">Exécuter [des attaques par déni de service](/windows-hardware/drivers/ifs/denial-of-service) .</span><span class="sxs-lookup"><span data-stu-id="317d5-110">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="317d5-111">Télécharger des virus ou des programmes malveillants.</span><span class="sxs-lookup"><span data-stu-id="317d5-111">Upload viruses or malware.</span></span>
* <span data-ttu-id="317d5-112">Compromettre les réseaux et les serveurs d’une autre manière.</span><span class="sxs-lookup"><span data-stu-id="317d5-112">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="317d5-113">Les étapes de sécurité qui réduisent la probabilité d’une attaque réussie sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="317d5-113">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="317d5-114">Chargez les fichiers dans une zone de chargement de fichier dédiée, de préférence sur un lecteur non-système.</span><span class="sxs-lookup"><span data-stu-id="317d5-114">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="317d5-115">Un emplacement dédié permet d’imposer des restrictions de sécurité plus faciles sur les fichiers téléchargés.</span><span class="sxs-lookup"><span data-stu-id="317d5-115">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="317d5-116">Désactivez les autorisations d’exécution sur l’emplacement de chargement du fichier.&dagger;</span><span class="sxs-lookup"><span data-stu-id="317d5-116">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="317d5-117">Ne conservez **pas** les fichiers téléchargés dans la même arborescence de répertoires que l’application.&dagger;</span><span class="sxs-lookup"><span data-stu-id="317d5-117">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="317d5-118">Utilisez un nom de fichier sécurisé déterminé par l’application.</span><span class="sxs-lookup"><span data-stu-id="317d5-118">Use a safe file name determined by the app.</span></span> <span data-ttu-id="317d5-119">N’utilisez pas un nom de fichier fourni par l’utilisateur ou le nom de fichier non approuvé du fichier téléchargé. &dagger; Code HTML encode le nom de fichier non fiable lors de son affichage.</span><span class="sxs-lookup"><span data-stu-id="317d5-119">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="317d5-120">Par exemple, la journalisation du nom de fichier ou l’affichage dans l’interface utilisateur ( Razor génère automatiquement le code html).</span><span class="sxs-lookup"><span data-stu-id="317d5-120">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="317d5-121">Autorisez uniquement les extensions de fichier approuvées pour la spécification de conception de l’application.&dagger;</span><span class="sxs-lookup"><span data-stu-id="317d5-121">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="317d5-122">Vérifiez que les vérifications côté client sont effectuées sur le serveur. &dagger; Les contrôles côté client sont faciles à contourner.</span><span class="sxs-lookup"><span data-stu-id="317d5-122">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="317d5-123">Vérifiez la taille d’un fichier téléchargé.</span><span class="sxs-lookup"><span data-stu-id="317d5-123">Check the size of an uploaded file.</span></span> <span data-ttu-id="317d5-124">Définissez une limite de taille maximale pour empêcher les chargements volumineux.&dagger;</span><span class="sxs-lookup"><span data-stu-id="317d5-124">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="317d5-125">Lorsque les fichiers ne doivent pas être remplacés par un fichier téléchargé portant le même nom, vérifiez le nom du fichier par rapport à la base de données ou au stockage physique avant de charger le fichier.</span><span class="sxs-lookup"><span data-stu-id="317d5-125">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="317d5-126">**Exécutez un programme de détection de virus et de logiciels malveillants sur le contenu chargé avant que le fichier ne soit stocké.**</span><span class="sxs-lookup"><span data-stu-id="317d5-126">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="317d5-127">&dagger;L’exemple d’application illustre une approche qui répond aux critères.</span><span class="sxs-lookup"><span data-stu-id="317d5-127">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="317d5-128">Le chargement d’un code malveillant sur un système est généralement la première étape de l’exécution de code capable de :</span><span class="sxs-lookup"><span data-stu-id="317d5-128">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="317d5-129">Maîtrisez complètement un système.</span><span class="sxs-lookup"><span data-stu-id="317d5-129">Completely gain control of a system.</span></span>
> * <span data-ttu-id="317d5-130">Surchargez un système avec le résultat du blocage du système.</span><span class="sxs-lookup"><span data-stu-id="317d5-130">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="317d5-131">Compromettre les données utilisateur ou système.</span><span class="sxs-lookup"><span data-stu-id="317d5-131">Compromise user or system data.</span></span>
> * <span data-ttu-id="317d5-132">Appliquez Graffiti à une interface utilisateur publique.</span><span class="sxs-lookup"><span data-stu-id="317d5-132">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="317d5-133">Pour plus d’informations sur la réduction de la surface d’attaque quand vous acceptez des fichiers d’utilisateurs, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="317d5-133">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * <span data-ttu-id="317d5-134">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) (Chargement de fichiers illimité)</span><span class="sxs-lookup"><span data-stu-id="317d5-134">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)</span></span>
> * [<span data-ttu-id="317d5-135">Sécurité Azure : Vérifier que les contrôles appropriés sont en place quand vous acceptez des fichiers d’utilisateurs</span><span class="sxs-lookup"><span data-stu-id="317d5-135">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="317d5-136">Pour plus d’informations sur l’implémentation de mesures de sécurité, y compris des exemples de l’exemple d’application, consultez la section [validation](#validation) .</span><span class="sxs-lookup"><span data-stu-id="317d5-136">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="317d5-137">Scénarios de stockage</span><span class="sxs-lookup"><span data-stu-id="317d5-137">Storage scenarios</span></span>

<span data-ttu-id="317d5-138">Les options de stockage courantes pour les fichiers sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="317d5-138">Common storage options for files include:</span></span>

* <span data-ttu-id="317d5-139">Base de données</span><span class="sxs-lookup"><span data-stu-id="317d5-139">Database</span></span>

  * <span data-ttu-id="317d5-140">Pour les petits chargements de fichiers, une base de données est souvent plus rapide que les options de stockage physique (système de fichiers ou partage réseau).</span><span class="sxs-lookup"><span data-stu-id="317d5-140">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="317d5-141">Une base de données est souvent plus pratique que les options de stockage physique, car la récupération d’un enregistrement de base de données pour les données utilisateur peut fournir simultanément le contenu du fichier (par exemple, une image de l’avatar).</span><span class="sxs-lookup"><span data-stu-id="317d5-141">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="317d5-142">Une base de données est potentiellement moins coûteuse que l’utilisation d’un service de stockage de données.</span><span class="sxs-lookup"><span data-stu-id="317d5-142">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="317d5-143">Stockage physique (système de fichiers ou partage réseau)</span><span class="sxs-lookup"><span data-stu-id="317d5-143">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="317d5-144">Pour les chargements de fichiers volumineux :</span><span class="sxs-lookup"><span data-stu-id="317d5-144">For large file uploads:</span></span>
    * <span data-ttu-id="317d5-145">Les limites de base de données peuvent limiter la taille du téléchargement.</span><span class="sxs-lookup"><span data-stu-id="317d5-145">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="317d5-146">Le stockage physique est souvent moins économique que le stockage dans une base de données.</span><span class="sxs-lookup"><span data-stu-id="317d5-146">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="317d5-147">Le stockage physique est potentiellement moins onéreux que l’utilisation d’un service de stockage de données.</span><span class="sxs-lookup"><span data-stu-id="317d5-147">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="317d5-148">Le processus de l’application doit disposer d’autorisations en lecture et en écriture sur l’emplacement de stockage.</span><span class="sxs-lookup"><span data-stu-id="317d5-148">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="317d5-149">**N’accordez jamais l’autorisation EXECUTE.**</span><span class="sxs-lookup"><span data-stu-id="317d5-149">**Never grant execute permission.**</span></span>

* <span data-ttu-id="317d5-150">Service de stockage de données (par exemple, [stockage d’objets BLOB Azure](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="317d5-150">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="317d5-151">Les services offrent généralement une évolutivité et une résilience améliorées par rapport aux solutions locales qui sont généralement sujettes à des points de défaillance uniques.</span><span class="sxs-lookup"><span data-stu-id="317d5-151">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="317d5-152">Les services sont potentiellement moins coûteux dans les scénarios d’infrastructure de stockage volumineux.</span><span class="sxs-lookup"><span data-stu-id="317d5-152">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="317d5-153">Pour plus d’informations, consultez [démarrage rapide : utiliser .net pour créer un objet BLOB dans le stockage d’objets](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="317d5-153">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="317d5-154">Scénarios de chargement de fichiers</span><span class="sxs-lookup"><span data-stu-id="317d5-154">File upload scenarios</span></span>

<span data-ttu-id="317d5-155">La mise en mémoire tampon et la diffusion en continu sont deux approches générales pour le téléchargement de fichiers.</span><span class="sxs-lookup"><span data-stu-id="317d5-155">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="317d5-156">**des réponses**</span><span class="sxs-lookup"><span data-stu-id="317d5-156">**Buffering**</span></span>

<span data-ttu-id="317d5-157">La totalité du fichier est lue dans un <xref:Microsoft.AspNetCore.Http.IFormFile> , qui est une représentation C# du fichier utilisé pour traiter ou enregistrer le fichier.</span><span class="sxs-lookup"><span data-stu-id="317d5-157">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="317d5-158">Les ressources (disque, mémoire) utilisées par les chargements de fichiers dépendent du nombre et de la taille des chargements de fichiers simultanés.</span><span class="sxs-lookup"><span data-stu-id="317d5-158">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="317d5-159">Si une application tente de mettre en mémoire tampon un trop grand nombre de chargements, le site se bloque lorsqu’il manque de mémoire ou d’espace disque.</span><span class="sxs-lookup"><span data-stu-id="317d5-159">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="317d5-160">Si la taille ou la fréquence des chargements de fichiers épuisent les ressources d’application, utilisez la diffusion en continu.</span><span class="sxs-lookup"><span data-stu-id="317d5-160">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="317d5-161">Tout fichier mis en mémoire tampon dépassant 64 Ko est déplacé de la mémoire vers un fichier temporaire sur le disque.</span><span class="sxs-lookup"><span data-stu-id="317d5-161">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="317d5-162">La mise en mémoire tampon de petits fichiers est traitée dans les sections suivantes de cette rubrique :</span><span class="sxs-lookup"><span data-stu-id="317d5-162">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="317d5-163">Stockage physique</span><span class="sxs-lookup"><span data-stu-id="317d5-163">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="317d5-164">Sauvegarde de la base de données</span><span class="sxs-lookup"><span data-stu-id="317d5-164">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="317d5-165">**Streaming**</span><span class="sxs-lookup"><span data-stu-id="317d5-165">**Streaming**</span></span>

<span data-ttu-id="317d5-166">Le fichier est reçu à partir d’une demande en plusieurs parties et est directement traité ou enregistré par l’application.</span><span class="sxs-lookup"><span data-stu-id="317d5-166">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="317d5-167">La diffusion en continu n’améliore pas les performances de manière significative.</span><span class="sxs-lookup"><span data-stu-id="317d5-167">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="317d5-168">La diffusion en continu réduit les demandes de mémoire ou d’espace disque lors du chargement de fichiers.</span><span class="sxs-lookup"><span data-stu-id="317d5-168">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="317d5-169">La diffusion en continu de fichiers volumineux est traitée dans la section [chargement de fichiers volumineux avec diffusion](#upload-large-files-with-streaming) .</span><span class="sxs-lookup"><span data-stu-id="317d5-169">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="317d5-170">Charger des fichiers de petite taille avec une liaison de modèle mise en mémoire tampon vers un stockage physique</span><span class="sxs-lookup"><span data-stu-id="317d5-170">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="317d5-171">Pour télécharger des fichiers de petite taille, utilisez un formulaire en plusieurs parties ou créez une requête de publication à l’aide de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="317d5-171">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="317d5-172">L’exemple suivant illustre l’utilisation d’un Razor formulaire de pages pour télécharger un seul fichier (*pages/BufferedSingleFileUploadPhysical. cshtml* dans l’exemple d’application) :</span><span class="sxs-lookup"><span data-stu-id="317d5-172">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="317d5-173">L’exemple suivant est similaire à l’exemple précédent, à l’exception de :</span><span class="sxs-lookup"><span data-stu-id="317d5-173">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="317d5-174">JavaScript ([API FETCH](https://developer.mozilla.org/docs/Web/API/Fetch_API)) est utilisé pour envoyer les données du formulaire.</span><span class="sxs-lookup"><span data-stu-id="317d5-174">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="317d5-175">Il n’y a aucune validation.</span><span class="sxs-lookup"><span data-stu-id="317d5-175">There's no validation.</span></span>

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

<span data-ttu-id="317d5-176">Pour exécuter la publication de formulaire dans JavaScript pour les clients qui [ne prennent pas en charge l’API FETCH](https://caniuse.com/#feat=fetch), utilisez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="317d5-176">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="317d5-177">Utilisez un Polyfill d’extraction (par exemple, [Window. Fetch Polyfill (GitHub/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="317d5-177">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="317d5-178">Utiliser `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="317d5-178">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="317d5-179">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="317d5-179">For example:</span></span>

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

<span data-ttu-id="317d5-180">Afin de prendre en charge les chargements de fichiers, les formulaires HTML doivent spécifier un type d’encodage ( `enctype` ) de `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="317d5-180">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="317d5-181">Pour `files` qu’un élément INPUT prenne en charge le téléchargement de plusieurs fichiers, fournissez l' `multiple` attribut sur l' `<input>` élément :</span><span class="sxs-lookup"><span data-stu-id="317d5-181">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="317d5-182">Les fichiers individuels téléchargés sur le serveur sont accessibles via la [liaison de modèle](xref:mvc/models/model-binding) à l’aide de <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="317d5-182">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="317d5-183">L’exemple d’application illustre plusieurs chargements de fichiers mis en mémoire tampon pour les scénarios de base de données et de stockage physique.</span><span class="sxs-lookup"><span data-stu-id="317d5-183">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="317d5-184">N’utilisez **pas** la `FileName` propriété <xref:Microsoft.AspNetCore.Http.IFormFile> autre que pour l’affichage et la journalisation.</span><span class="sxs-lookup"><span data-stu-id="317d5-184">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="317d5-185">Lors de l’affichage ou de la journalisation, le nom du fichier est encodé au format HTML.</span><span class="sxs-lookup"><span data-stu-id="317d5-185">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="317d5-186">Une personne malveillante peut fournir un nom de fichier malveillant, y compris les chemins d’accès complets ou les chemins d’accès relatifs.</span><span class="sxs-lookup"><span data-stu-id="317d5-186">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="317d5-187">Les applications doivent :</span><span class="sxs-lookup"><span data-stu-id="317d5-187">Applications should:</span></span>
>
> * <span data-ttu-id="317d5-188">Supprimez le chemin d’accès du nom de fichier fourni par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="317d5-188">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="317d5-189">Enregistrez le nom de fichier encodé au format HTML, avec chemin d’accès supprimé pour l’interface utilisateur ou la journalisation.</span><span class="sxs-lookup"><span data-stu-id="317d5-189">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="317d5-190">Générez un nouveau nom de fichier aléatoire pour le stockage.</span><span class="sxs-lookup"><span data-stu-id="317d5-190">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="317d5-191">Le code suivant supprime le chemin d’accès du nom de fichier :</span><span class="sxs-lookup"><span data-stu-id="317d5-191">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="317d5-192">Les exemples fournis jusqu’à présent ne prennent pas en compte les considérations de sécurité.</span><span class="sxs-lookup"><span data-stu-id="317d5-192">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="317d5-193">Des informations supplémentaires sont fournies par les sections suivantes et l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="317d5-193">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="317d5-194">Considérations relatives à la sécurité</span><span class="sxs-lookup"><span data-stu-id="317d5-194">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="317d5-195">Validation</span><span class="sxs-lookup"><span data-stu-id="317d5-195">Validation</span></span>](#validation)

<span data-ttu-id="317d5-196">Lors du chargement de fichiers à l’aide d’une liaison de modèle et <xref:Microsoft.AspNetCore.Http.IFormFile> , la méthode d’action peut accepter les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="317d5-196">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="317d5-197">Une seule <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="317d5-197">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="317d5-198">L’un des regroupements suivants qui représentent plusieurs fichiers :</span><span class="sxs-lookup"><span data-stu-id="317d5-198">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="317d5-199">[Tarifs](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="317d5-199">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="317d5-200">La liaison correspond aux fichiers de formulaire par nom.</span><span class="sxs-lookup"><span data-stu-id="317d5-200">Binding matches form files by name.</span></span> <span data-ttu-id="317d5-201">Par exemple, la `name` valeur html dans `<input type="file" name="formFile">` doit correspondre au paramètre C#/lié à la propriété ( `FormFile` ).</span><span class="sxs-lookup"><span data-stu-id="317d5-201">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="317d5-202">Pour plus d’informations, consultez la section valeur de l' [attribut de nom de correspondance pour le nom de paramètre de la méthode de publication](#match-name-attribute-value-to-parameter-name-of-post-method) .</span><span class="sxs-lookup"><span data-stu-id="317d5-202">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="317d5-203">L’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="317d5-203">The following example:</span></span>

* <span data-ttu-id="317d5-204">Effectue une boucle sur un ou plusieurs fichiers téléchargés.</span><span class="sxs-lookup"><span data-stu-id="317d5-204">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="317d5-205">Utilise [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) pour retourner le chemin d’accès complet d’un fichier, y compris le nom de fichier.</span><span class="sxs-lookup"><span data-stu-id="317d5-205">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="317d5-206">Enregistre les fichiers dans le système de fichiers local à l’aide d’un nom de fichier généré par l’application.</span><span class="sxs-lookup"><span data-stu-id="317d5-206">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="317d5-207">Retourne le nombre total et la taille des fichiers téléchargés.</span><span class="sxs-lookup"><span data-stu-id="317d5-207">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="317d5-208">Utilisez `Path.GetRandomFileName` pour générer un nom de fichier sans chemin d’accès.</span><span class="sxs-lookup"><span data-stu-id="317d5-208">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="317d5-209">Dans l’exemple suivant, le chemin d’accès est obtenu à partir de la configuration :</span><span class="sxs-lookup"><span data-stu-id="317d5-209">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="317d5-210">Le chemin d’accès passé à <xref:System.IO.FileStream> *doit* inclure le nom de fichier.</span><span class="sxs-lookup"><span data-stu-id="317d5-210">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="317d5-211">Si le nom de fichier n’est pas fourni, une <xref:System.UnauthorizedAccessException> exception est levée au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="317d5-211">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="317d5-212">Les fichiers chargés à l’aide de la <xref:Microsoft.AspNetCore.Http.IFormFile> technique sont mis en mémoire tampon ou sur disque sur le serveur avant le traitement.</span><span class="sxs-lookup"><span data-stu-id="317d5-212">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="317d5-213">À l’intérieur de la méthode d’action, le <xref:Microsoft.AspNetCore.Http.IFormFile> contenu est accessible en tant que <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="317d5-213">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="317d5-214">Outre le système de fichiers local, les fichiers peuvent être enregistrés sur un partage réseau ou un service de stockage de fichiers, tel que le [stockage d’objets BLOB Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="317d5-214">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="317d5-215">Pour obtenir un autre exemple qui effectue une boucle sur plusieurs fichiers pour le téléchargement et utilise des noms de fichiers sécurisés, consultez *pages/BufferedMultipleFileUploadPhysical. cshtml. cs* dans l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="317d5-215">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="317d5-216">[Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) lève une exception <xref:System.IO.IOException> si plus de 65 535 fichiers sont créés sans supprimer les fichiers temporaires précédents.</span><span class="sxs-lookup"><span data-stu-id="317d5-216">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="317d5-217">La limite de 65 535 fichiers est une limite par serveur.</span><span class="sxs-lookup"><span data-stu-id="317d5-217">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="317d5-218">Pour plus d’informations sur cette limite sur le système d’exploitation Windows, consultez les notes dans les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="317d5-218">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="317d5-219">GetTempFileNameA fonction)</span><span class="sxs-lookup"><span data-stu-id="317d5-219">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="317d5-220">Charger des fichiers de petite taille avec une liaison de modèle mise en mémoire tampon vers une base de données</span><span class="sxs-lookup"><span data-stu-id="317d5-220">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="317d5-221">Pour stocker des données de fichier binaires dans une base de données à l’aide de [Entity Framework](/ef/core/index), définissez une <xref:System.Byte> propriété de tableau sur l’entité :</span><span class="sxs-lookup"><span data-stu-id="317d5-221">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="317d5-222">Spécifiez une propriété de modèle de page pour la classe qui comprend <xref:Microsoft.AspNetCore.Http.IFormFile> :</span><span class="sxs-lookup"><span data-stu-id="317d5-222">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="317d5-223"><xref:Microsoft.AspNetCore.Http.IFormFile> peut être utilisé directement comme paramètre de méthode d’action ou comme propriété de modèle liée.</span><span class="sxs-lookup"><span data-stu-id="317d5-223"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="317d5-224">L’exemple précédent utilise une propriété de modèle liée.</span><span class="sxs-lookup"><span data-stu-id="317d5-224">The prior example uses a bound model property.</span></span>

<span data-ttu-id="317d5-225">Le `FileUpload` est utilisé dans le Razor formulaire pages :</span><span class="sxs-lookup"><span data-stu-id="317d5-225">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="317d5-226">Lorsque le formulaire est publié sur le serveur, copiez <xref:Microsoft.AspNetCore.Http.IFormFile> -le dans un flux et enregistrez-le en tant que tableau d’octets dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="317d5-226">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="317d5-227">Dans l’exemple suivant, `_dbContext` stocke le contexte de base de données de l’application :</span><span class="sxs-lookup"><span data-stu-id="317d5-227">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="317d5-228">L’exemple précédent est semblable à un scénario illustré dans l’exemple d’application :</span><span class="sxs-lookup"><span data-stu-id="317d5-228">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="317d5-229">*Pages/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="317d5-229">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="317d5-230">*Pages/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="317d5-230">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="317d5-231">Soyez prudent quand vous stockez des données binaires dans des bases de données relationnelles, car cela peut avoir un impact négatif sur les performances.</span><span class="sxs-lookup"><span data-stu-id="317d5-231">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="317d5-232">Ne vous fiez pas à la `FileName` propriété de sans validation ni à approuver celle-ci <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="317d5-232">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="317d5-233">La `FileName` propriété doit uniquement être utilisée à des fins d’affichage et uniquement après l’encodage HTML.</span><span class="sxs-lookup"><span data-stu-id="317d5-233">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="317d5-234">Les exemples fournis ne prennent pas en compte les considérations de sécurité.</span><span class="sxs-lookup"><span data-stu-id="317d5-234">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="317d5-235">Des informations supplémentaires sont fournies par les sections suivantes et l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="317d5-235">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="317d5-236">Considérations relatives à la sécurité</span><span class="sxs-lookup"><span data-stu-id="317d5-236">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="317d5-237">Validation</span><span class="sxs-lookup"><span data-stu-id="317d5-237">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="317d5-238">Charger des fichiers volumineux avec streaming</span><span class="sxs-lookup"><span data-stu-id="317d5-238">Upload large files with streaming</span></span>

<span data-ttu-id="317d5-239">L’exemple suivant montre comment utiliser JavaScript pour diffuser un fichier vers une action de contrôleur.</span><span class="sxs-lookup"><span data-stu-id="317d5-239">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="317d5-240">Le jeton anti-contrefaçon du fichier est généré à l’aide d’un attribut de filtre personnalisé et transmis aux en-têtes HTTP du client plutôt que dans le corps de la demande.</span><span class="sxs-lookup"><span data-stu-id="317d5-240">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="317d5-241">Étant donné que la méthode d’action traite directement les données chargées, la liaison de modèle de formulaire est désactivée par un autre filtre personnalisé.</span><span class="sxs-lookup"><span data-stu-id="317d5-241">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="317d5-242">Dans l’action, le contenu du formulaire est lu en avec `MultipartReader`, qui lit chaque `MultipartSection` individuelle, traitant le fichier ou enregistrant le contenu selon ce qui est approprié.</span><span class="sxs-lookup"><span data-stu-id="317d5-242">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="317d5-243">Une fois les sections en plusieurs parties lues, l’action effectue sa propre liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="317d5-243">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="317d5-244">La réponse de page initiale charge le formulaire et enregistre un jeton anti-contrefaçon dans un cookie (via l' `GenerateAntiforgeryTokenCookieAttribute` attribut).</span><span class="sxs-lookup"><span data-stu-id="317d5-244">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="317d5-245">L’attribut utilise la [prise en charge de l’anticontrefaçon](xref:security/anti-request-forgery) intégrée de ASP.net Core pour définir un cookie avec un jeton de demande :</span><span class="sxs-lookup"><span data-stu-id="317d5-245">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="317d5-246">`DisableFormValueModelBindingAttribute`Est utilisé pour désactiver la liaison de modèle :</span><span class="sxs-lookup"><span data-stu-id="317d5-246">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="317d5-247">Dans l’exemple d’application, `GenerateAntiforgeryTokenCookieAttribute` et `DisableFormValueModelBindingAttribute` sont appliqués en tant que filtres aux modèles d’application de page de et à l' `/StreamedSingleFileUploadDb` aide des conventions de `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` [ Razor pages](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="317d5-247">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=7-10,16-19)]

<span data-ttu-id="317d5-248">Étant donné que la liaison de modèle ne lit pas le formulaire, les paramètres qui sont liés depuis le formulaire ne sont pas liés (la requête, l’itinéraire et l’en-tête continuent de fonctionner).</span><span class="sxs-lookup"><span data-stu-id="317d5-248">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="317d5-249">La méthode d’action fonctionne directement avec la `Request` propriété.</span><span class="sxs-lookup"><span data-stu-id="317d5-249">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="317d5-250">Un `MultipartReader` est utilisé pour lire chaque section.</span><span class="sxs-lookup"><span data-stu-id="317d5-250">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="317d5-251">Les données de clé/valeur sont stockées dans un `KeyValueAccumulator` .</span><span class="sxs-lookup"><span data-stu-id="317d5-251">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="317d5-252">Une fois les sections en plusieurs parties lues, le contenu du `KeyValueAccumulator` est utilisé pour lier les données de formulaire à un type de modèle.</span><span class="sxs-lookup"><span data-stu-id="317d5-252">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="317d5-253">La `StreamingController.UploadDatabase` méthode complète pour la diffusion en continu vers une base de données avec EF Core :</span><span class="sxs-lookup"><span data-stu-id="317d5-253">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="317d5-254">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper. cs*) :</span><span class="sxs-lookup"><span data-stu-id="317d5-254">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="317d5-255">Méthode complète `StreamingController.UploadPhysical` pour la diffusion en continu vers un emplacement physique :</span><span class="sxs-lookup"><span data-stu-id="317d5-255">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="317d5-256">Dans l’exemple d’application, les contrôles de validation sont gérés par `FileHelpers.ProcessStreamedFile` .</span><span class="sxs-lookup"><span data-stu-id="317d5-256">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="317d5-257">Validation</span><span class="sxs-lookup"><span data-stu-id="317d5-257">Validation</span></span>

<span data-ttu-id="317d5-258">La classe de l’exemple d’application `FileHelpers` illustre plusieurs vérifications des chargements de fichiers mis en mémoire tampon <xref:Microsoft.AspNetCore.Http.IFormFile> et diffusés en continu.</span><span class="sxs-lookup"><span data-stu-id="317d5-258">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="317d5-259">Pour traiter les <xref:Microsoft.AspNetCore.Http.IFormFile> chargements de fichiers mis en mémoire tampon dans l’exemple d’application, consultez la `ProcessFormFile` méthode dans le fichier *Utilities/FileHelpers. cs* .</span><span class="sxs-lookup"><span data-stu-id="317d5-259">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="317d5-260">Pour le traitement de fichiers en continu, consultez la `ProcessStreamedFile` méthode dans le même fichier.</span><span class="sxs-lookup"><span data-stu-id="317d5-260">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="317d5-261">Les méthodes de traitement de validation présentées dans l’exemple d’application n’analysent pas le contenu des fichiers téléchargés.</span><span class="sxs-lookup"><span data-stu-id="317d5-261">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="317d5-262">Dans la plupart des scénarios de production, une API de détection de virus et de logiciels malveillants est utilisée sur le fichier avant que le fichier soit mis à la disposition des utilisateurs ou d’autres systèmes.</span><span class="sxs-lookup"><span data-stu-id="317d5-262">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="317d5-263">Bien que l’exemple de rubrique fournit un exemple fonctionnel de techniques de validation, n’implémentez pas la `FileHelpers` classe dans une application de production, sauf si vous :</span><span class="sxs-lookup"><span data-stu-id="317d5-263">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="317d5-264">Comprenez pleinement l’implémentation.</span><span class="sxs-lookup"><span data-stu-id="317d5-264">Fully understand the implementation.</span></span>
> * <span data-ttu-id="317d5-265">Modifiez l’implémentation en fonction de l’environnement et des spécifications de l’application.</span><span class="sxs-lookup"><span data-stu-id="317d5-265">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="317d5-266">**N’implémentez jamais non plus de code de sécurité dans une application sans répondre à ces exigences.**</span><span class="sxs-lookup"><span data-stu-id="317d5-266">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="317d5-267">Validation du contenu</span><span class="sxs-lookup"><span data-stu-id="317d5-267">Content validation</span></span>

<span data-ttu-id="317d5-268">**Utilisez une API d’analyse de virus/programme malveillant sur le contenu chargé.**</span><span class="sxs-lookup"><span data-stu-id="317d5-268">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="317d5-269">L’analyse des fichiers exige des ressources serveur dans des scénarios de volume élevé.</span><span class="sxs-lookup"><span data-stu-id="317d5-269">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="317d5-270">Si les performances de traitement des demandes sont réduites en raison de l’analyse de fichiers, envisagez de décharger le travail d’analyse vers un [service en arrière-plan](xref:fundamentals/host/hosted-services), éventuellement un service qui s’exécute sur un serveur différent du serveur de l’application.</span><span class="sxs-lookup"><span data-stu-id="317d5-270">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="317d5-271">En règle générale, les fichiers téléchargés sont conservés dans une zone en quarantaine jusqu’à ce que l’antivirus en arrière-plan les vérifie.</span><span class="sxs-lookup"><span data-stu-id="317d5-271">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="317d5-272">Lorsqu’un fichier réussit, le fichier est déplacé vers l’emplacement de stockage de fichiers normal.</span><span class="sxs-lookup"><span data-stu-id="317d5-272">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="317d5-273">Ces étapes sont généralement effectuées conjointement avec un enregistrement de base de données qui indique l’état d’analyse d’un fichier.</span><span class="sxs-lookup"><span data-stu-id="317d5-273">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="317d5-274">À l’aide de cette approche, l’application et le serveur d’applications restent concentrés sur la réponse aux requêtes.</span><span class="sxs-lookup"><span data-stu-id="317d5-274">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="317d5-275">Validation de l’extension de fichier</span><span class="sxs-lookup"><span data-stu-id="317d5-275">File extension validation</span></span>

<span data-ttu-id="317d5-276">L’extension du fichier chargé doit être vérifiée par rapport à une liste d’extensions autorisées.</span><span class="sxs-lookup"><span data-stu-id="317d5-276">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="317d5-277">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="317d5-277">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="317d5-278">Validation de la signature de fichier</span><span class="sxs-lookup"><span data-stu-id="317d5-278">File signature validation</span></span>

<span data-ttu-id="317d5-279">La signature d’un fichier est déterminée par les premiers octets au début d’un fichier.</span><span class="sxs-lookup"><span data-stu-id="317d5-279">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="317d5-280">Ces octets peuvent être utilisés pour indiquer si l’extension correspond au contenu du fichier.</span><span class="sxs-lookup"><span data-stu-id="317d5-280">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="317d5-281">L’exemple d’application vérifie les signatures de fichiers pour quelques types de fichiers courants.</span><span class="sxs-lookup"><span data-stu-id="317d5-281">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="317d5-282">Dans l’exemple suivant, la signature de fichier pour une image JPEG est vérifiée par rapport au fichier :</span><span class="sxs-lookup"><span data-stu-id="317d5-282">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="317d5-283">Pour obtenir des signatures de fichiers supplémentaires, consultez la [base de données signatures de fichiers](https://www.filesignatures.net/) et les spécifications de fichier officielles.</span><span class="sxs-lookup"><span data-stu-id="317d5-283">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="317d5-284">Sécurité des noms de fichiers</span><span class="sxs-lookup"><span data-stu-id="317d5-284">File name security</span></span>

<span data-ttu-id="317d5-285">N’utilisez jamais un nom de fichier fourni par le client pour enregistrer un fichier dans le stockage physique.</span><span class="sxs-lookup"><span data-stu-id="317d5-285">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="317d5-286">Créez un nom de fichier sécurisé pour le fichier à l’aide de [Path. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) ou [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) pour créer un chemin d’accès complet (y compris le nom de fichier) pour le stockage temporaire.</span><span class="sxs-lookup"><span data-stu-id="317d5-286">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="317d5-287">Razor encode automatiquement les valeurs des propriétés pour l’affichage.</span><span class="sxs-lookup"><span data-stu-id="317d5-287">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="317d5-288">Le code suivant peut être utilisé en toute sécurité :</span><span class="sxs-lookup"><span data-stu-id="317d5-288">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="317d5-289">En dehors de Razor , toujours <xref:System.Net.WebUtility.HtmlEncode*> le contenu du nom de fichier à partir de la demande d’un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="317d5-289">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="317d5-290">De nombreuses implémentations doivent inclure une vérification de l’existence du fichier ; dans le cas contraire, le fichier est remplacé par un fichier du même nom.</span><span class="sxs-lookup"><span data-stu-id="317d5-290">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="317d5-291">Fournissez une logique supplémentaire pour répondre aux spécifications de votre application.</span><span class="sxs-lookup"><span data-stu-id="317d5-291">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="317d5-292">Validation de la taille</span><span class="sxs-lookup"><span data-stu-id="317d5-292">Size validation</span></span>

<span data-ttu-id="317d5-293">Limitez la taille des fichiers téléchargés.</span><span class="sxs-lookup"><span data-stu-id="317d5-293">Limit the size of uploaded files.</span></span>

<span data-ttu-id="317d5-294">Dans l’exemple d’application, la taille du fichier est limitée à 2 Mo (indiquée en octets).</span><span class="sxs-lookup"><span data-stu-id="317d5-294">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="317d5-295">La limite est fournie via la [configuration](xref:fundamentals/configuration/index) à partir du *appsettings.json* fichier :</span><span class="sxs-lookup"><span data-stu-id="317d5-295">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="317d5-296">`FileSizeLimit`Est injecté dans des `PageModel` classes :</span><span class="sxs-lookup"><span data-stu-id="317d5-296">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="317d5-297">Quand une taille de fichier dépasse la limite, le fichier est rejeté :</span><span class="sxs-lookup"><span data-stu-id="317d5-297">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="317d5-298">Valeur de l’attribut de nom de correspondance avec le nom de paramètre de la méthode de publication</span><span class="sxs-lookup"><span data-stu-id="317d5-298">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="317d5-299">Dans les non- Razor formulaires qui publient des données de formulaire ou utilisent `FormData` directement JavaScript, le nom spécifié dans l’élément du formulaire ou `FormData` doit correspondre au nom du paramètre dans l’action du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="317d5-299">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="317d5-300">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="317d5-300">In the following example:</span></span>

* <span data-ttu-id="317d5-301">Lorsque vous utilisez un `<input>` élément, l' `name` attribut est défini sur la valeur `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="317d5-301">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="317d5-302">Lors de l’utilisation `FormData` de dans JavaScript, le nom est défini sur la valeur `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="317d5-302">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="317d5-303">Utilisez un nom correspondant pour le paramètre de la méthode C# ( `battlePlans` ) :</span><span class="sxs-lookup"><span data-stu-id="317d5-303">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="317d5-304">Pour une Razor méthode de gestionnaire de page pages nommée `Upload` :</span><span class="sxs-lookup"><span data-stu-id="317d5-304">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="317d5-305">Pour une méthode d’action du billet de contrôleur MVC :</span><span class="sxs-lookup"><span data-stu-id="317d5-305">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="317d5-306">Configuration du serveur et de l’application</span><span class="sxs-lookup"><span data-stu-id="317d5-306">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="317d5-307">Longueur limite du corps en plusieurs parties</span><span class="sxs-lookup"><span data-stu-id="317d5-307">Multipart body length limit</span></span>

<span data-ttu-id="317d5-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> définit la limite de la longueur de chaque corps en plusieurs parties.</span><span class="sxs-lookup"><span data-stu-id="317d5-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="317d5-309">Les sections de formulaire qui dépassent cette limite lèvent une lorsqu’elles sont <xref:System.IO.InvalidDataException> analysées.</span><span class="sxs-lookup"><span data-stu-id="317d5-309">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="317d5-310">La valeur par défaut est 134 217 728 (128 Mo).</span><span class="sxs-lookup"><span data-stu-id="317d5-310">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="317d5-311">Personnaliser la limite à l’aide du <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> paramètre dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="317d5-311">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="317d5-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> est utilisé pour définir le <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> pour une seule page ou action.</span><span class="sxs-lookup"><span data-stu-id="317d5-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="317d5-313">Dans une Razor application pages, appliquez le filtre avec une [Convention](xref:razor-pages/razor-pages-conventions) dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="317d5-313">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="317d5-314">Dans une Razor application de pages ou une application MVC, appliquez le filtre au modèle de page ou à la méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="317d5-314">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="317d5-315">Taille maximale du corps de la demande Kestrel</span><span class="sxs-lookup"><span data-stu-id="317d5-315">Kestrel maximum request body size</span></span>

<span data-ttu-id="317d5-316">Pour les applications hébergées par Kestrel, la taille de corps de requête maximale par défaut est de 30 millions octets, soit environ 28,6 Mo.</span><span class="sxs-lookup"><span data-stu-id="317d5-316">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="317d5-317">Personnaliser la limite à l’aide de l’option de serveur [MaxRequestBodySize](xref:fundamentals/servers/kestrel/options#maximum-request-body-size) Kestrel :</span><span class="sxs-lookup"><span data-stu-id="317d5-317">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel/options#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="317d5-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> est utilisé pour définir le [MaxRequestBodySize](xref:fundamentals/servers/kestrel/options#maximum-request-body-size) pour une seule page ou action.</span><span class="sxs-lookup"><span data-stu-id="317d5-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel/options#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="317d5-319">Dans une Razor application pages, appliquez le filtre avec une [Convention](xref:razor-pages/razor-pages-conventions) dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="317d5-319">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="317d5-320">Dans une Razor application de pages ou une application MVC, appliquez le filtre à la classe du gestionnaire de pages ou à la méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="317d5-320">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="317d5-321">`RequestSizeLimitAttribute`Peut également être appliqué à l’aide de la [`@attribute`](xref:mvc/views/razor#attribute) Razor directive :</span><span class="sxs-lookup"><span data-stu-id="317d5-321">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="317d5-322">Autres limites Kestrel</span><span class="sxs-lookup"><span data-stu-id="317d5-322">Other Kestrel limits</span></span>

<span data-ttu-id="317d5-323">D’autres limites Kestrel peuvent s’appliquer aux applications hébergées par Kestrel :</span><span class="sxs-lookup"><span data-stu-id="317d5-323">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="317d5-324">Nombre maximale de connexions client</span><span class="sxs-lookup"><span data-stu-id="317d5-324">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel/options#maximum-client-connections)
* [<span data-ttu-id="317d5-325">Taux de données de demande et de réponse</span><span class="sxs-lookup"><span data-stu-id="317d5-325">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel/options#minimum-request-body-data-rate)

### <a name="iis"></a><span data-ttu-id="317d5-326">IIS</span><span class="sxs-lookup"><span data-stu-id="317d5-326">IIS</span></span>

<span data-ttu-id="317d5-327">La limite de demandes par défaut ( `maxAllowedContentLength` ) est de 30 millions octets, soit environ 28,6 Mo.</span><span class="sxs-lookup"><span data-stu-id="317d5-327">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="317d5-328">Personnaliser la limite dans le `web.config` fichier.</span><span class="sxs-lookup"><span data-stu-id="317d5-328">Customize the limit in the `web.config` file.</span></span> <span data-ttu-id="317d5-329">Dans l’exemple suivant, la limite est définie sur 50 Mo (52 428 800 octets) :</span><span class="sxs-lookup"><span data-stu-id="317d5-329">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="317d5-330">Le `maxAllowedContentLength` paramètre s’applique uniquement à IIS.</span><span class="sxs-lookup"><span data-stu-id="317d5-330">The `maxAllowedContentLength` setting only applies to IIS.</span></span> <span data-ttu-id="317d5-331">Pour plus d’informations, consultez [limites `<requestLimits>` des demandes ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="317d5-331">For more information, see [Request Limits `<requestLimits>`](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="317d5-332">Dépanner</span><span class="sxs-lookup"><span data-stu-id="317d5-332">Troubleshoot</span></span>

<span data-ttu-id="317d5-333">Voici certains problèmes courants rencontrés avec le chargement de fichiers et leurs solutions possibles.</span><span class="sxs-lookup"><span data-stu-id="317d5-333">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="317d5-334">Erreur introuvable lors du déploiement sur un serveur IIS</span><span class="sxs-lookup"><span data-stu-id="317d5-334">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="317d5-335">L’erreur suivante indique que le fichier chargé dépasse la longueur du contenu configurée du serveur :</span><span class="sxs-lookup"><span data-stu-id="317d5-335">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="317d5-336">Pour plus d’informations, consultez la section [IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="317d5-336">For more information, see the [IIS](#iis) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="317d5-337">Échec de connexion</span><span class="sxs-lookup"><span data-stu-id="317d5-337">Connection failure</span></span>

<span data-ttu-id="317d5-338">Une erreur de connexion et une connexion du serveur de réinitialisation indiquent probablement que le fichier téléchargé dépasse la taille maximale du corps de la demande de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="317d5-338">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="317d5-339">Pour plus d’informations, consultez la section [taille maximale du corps de la demande Kestrel](#kestrel-maximum-request-body-size) .</span><span class="sxs-lookup"><span data-stu-id="317d5-339">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="317d5-340">Les limites de connexion du client Kestrel peuvent également nécessiter des ajustements.</span><span class="sxs-lookup"><span data-stu-id="317d5-340">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="317d5-341">Exception de référence null avec IFormFile</span><span class="sxs-lookup"><span data-stu-id="317d5-341">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="317d5-342">Si le contrôleur accepte les fichiers téléchargés à l’aide <xref:Microsoft.AspNetCore.Http.IFormFile> de mais que la valeur est `null` , vérifiez que le formulaire HTML spécifie la `enctype` valeur `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="317d5-342">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="317d5-343">Si cet attribut n’est pas défini sur l' `<form>` élément, le chargement du fichier ne se produit pas et tous les <xref:Microsoft.AspNetCore.Http.IFormFile> arguments liés sont `null` .</span><span class="sxs-lookup"><span data-stu-id="317d5-343">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="317d5-344">Vérifiez également que l' [appellation de chargement dans les données de formulaire correspond à celle de l’application](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="317d5-344">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="317d5-345">Le flux est trop long</span><span class="sxs-lookup"><span data-stu-id="317d5-345">Stream was too long</span></span>

<span data-ttu-id="317d5-346">Les exemples de cette rubrique reposent sur <xref:System.IO.MemoryStream> pour stocker le contenu du fichier chargé.</span><span class="sxs-lookup"><span data-stu-id="317d5-346">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="317d5-347">La limite de taille d’un `MemoryStream` est `int.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="317d5-347">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="317d5-348">Si le scénario de chargement de fichiers de l’application nécessite la conservation d’un contenu de fichier d’une taille supérieure à 50 Mo, utilisez une autre approche qui ne repose pas sur un seul `MemoryStream` pour conserver le contenu d’un fichier chargé.</span><span class="sxs-lookup"><span data-stu-id="317d5-348">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="317d5-349">ASP.NET Core prend en charge le chargement d’un ou plusieurs fichiers à l’aide d’une liaison de modèle mise en mémoire tampon pour les fichiers plus petits et la diffusion en continu sans mise en mémoire tampon pour les fichiers plus volumineux</span><span class="sxs-lookup"><span data-stu-id="317d5-349">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="317d5-350">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/file-uploads/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="317d5-350">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="317d5-351">Considérations relatives à la sécurité</span><span class="sxs-lookup"><span data-stu-id="317d5-351">Security considerations</span></span>

<span data-ttu-id="317d5-352">Soyez prudent lorsque vous fournissez aux utilisateurs la possibilité de charger des fichiers sur un serveur.</span><span class="sxs-lookup"><span data-stu-id="317d5-352">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="317d5-353">Les attaquants peuvent tenter d’effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="317d5-353">Attackers may attempt to:</span></span>

* <span data-ttu-id="317d5-354">Exécuter [des attaques par déni de service](/windows-hardware/drivers/ifs/denial-of-service) .</span><span class="sxs-lookup"><span data-stu-id="317d5-354">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="317d5-355">Télécharger des virus ou des programmes malveillants.</span><span class="sxs-lookup"><span data-stu-id="317d5-355">Upload viruses or malware.</span></span>
* <span data-ttu-id="317d5-356">Compromettre les réseaux et les serveurs d’une autre manière.</span><span class="sxs-lookup"><span data-stu-id="317d5-356">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="317d5-357">Les étapes de sécurité qui réduisent la probabilité d’une attaque réussie sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="317d5-357">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="317d5-358">Chargez les fichiers dans une zone de chargement de fichier dédiée, de préférence sur un lecteur non-système.</span><span class="sxs-lookup"><span data-stu-id="317d5-358">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="317d5-359">Un emplacement dédié permet d’imposer des restrictions de sécurité plus faciles sur les fichiers téléchargés.</span><span class="sxs-lookup"><span data-stu-id="317d5-359">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="317d5-360">Désactivez les autorisations d’exécution sur l’emplacement de chargement du fichier.&dagger;</span><span class="sxs-lookup"><span data-stu-id="317d5-360">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="317d5-361">Ne conservez **pas** les fichiers téléchargés dans la même arborescence de répertoires que l’application.&dagger;</span><span class="sxs-lookup"><span data-stu-id="317d5-361">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="317d5-362">Utilisez un nom de fichier sécurisé déterminé par l’application.</span><span class="sxs-lookup"><span data-stu-id="317d5-362">Use a safe file name determined by the app.</span></span> <span data-ttu-id="317d5-363">N’utilisez pas un nom de fichier fourni par l’utilisateur ou le nom de fichier non approuvé du fichier téléchargé. &dagger; Code HTML encode le nom de fichier non fiable lors de son affichage.</span><span class="sxs-lookup"><span data-stu-id="317d5-363">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="317d5-364">Par exemple, la journalisation du nom de fichier ou l’affichage dans l’interface utilisateur ( Razor génère automatiquement le code html).</span><span class="sxs-lookup"><span data-stu-id="317d5-364">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="317d5-365">Autorisez uniquement les extensions de fichier approuvées pour la spécification de conception de l’application.&dagger;</span><span class="sxs-lookup"><span data-stu-id="317d5-365">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="317d5-366">Vérifiez que les vérifications côté client sont effectuées sur le serveur. &dagger; Les contrôles côté client sont faciles à contourner.</span><span class="sxs-lookup"><span data-stu-id="317d5-366">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="317d5-367">Vérifiez la taille d’un fichier téléchargé.</span><span class="sxs-lookup"><span data-stu-id="317d5-367">Check the size of an uploaded file.</span></span> <span data-ttu-id="317d5-368">Définissez une limite de taille maximale pour empêcher les chargements volumineux.&dagger;</span><span class="sxs-lookup"><span data-stu-id="317d5-368">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="317d5-369">Lorsque les fichiers ne doivent pas être remplacés par un fichier téléchargé portant le même nom, vérifiez le nom du fichier par rapport à la base de données ou au stockage physique avant de charger le fichier.</span><span class="sxs-lookup"><span data-stu-id="317d5-369">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="317d5-370">**Exécutez un programme de détection de virus et de logiciels malveillants sur le contenu chargé avant que le fichier ne soit stocké.**</span><span class="sxs-lookup"><span data-stu-id="317d5-370">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="317d5-371">&dagger;L’exemple d’application illustre une approche qui répond aux critères.</span><span class="sxs-lookup"><span data-stu-id="317d5-371">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="317d5-372">Le chargement d’un code malveillant sur un système est généralement la première étape de l’exécution de code capable de :</span><span class="sxs-lookup"><span data-stu-id="317d5-372">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="317d5-373">Maîtrisez complètement un système.</span><span class="sxs-lookup"><span data-stu-id="317d5-373">Completely gain control of a system.</span></span>
> * <span data-ttu-id="317d5-374">Surchargez un système avec le résultat du blocage du système.</span><span class="sxs-lookup"><span data-stu-id="317d5-374">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="317d5-375">Compromettre les données utilisateur ou système.</span><span class="sxs-lookup"><span data-stu-id="317d5-375">Compromise user or system data.</span></span>
> * <span data-ttu-id="317d5-376">Appliquez Graffiti à une interface utilisateur publique.</span><span class="sxs-lookup"><span data-stu-id="317d5-376">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="317d5-377">Pour plus d’informations sur la réduction de la surface d’attaque quand vous acceptez des fichiers d’utilisateurs, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="317d5-377">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * <span data-ttu-id="317d5-378">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) (Chargement de fichiers illimité)</span><span class="sxs-lookup"><span data-stu-id="317d5-378">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)</span></span>
> * [<span data-ttu-id="317d5-379">Sécurité Azure : Vérifier que les contrôles appropriés sont en place quand vous acceptez des fichiers d’utilisateurs</span><span class="sxs-lookup"><span data-stu-id="317d5-379">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="317d5-380">Pour plus d’informations sur l’implémentation de mesures de sécurité, y compris des exemples de l’exemple d’application, consultez la section [validation](#validation) .</span><span class="sxs-lookup"><span data-stu-id="317d5-380">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="317d5-381">Scénarios de stockage</span><span class="sxs-lookup"><span data-stu-id="317d5-381">Storage scenarios</span></span>

<span data-ttu-id="317d5-382">Les options de stockage courantes pour les fichiers sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="317d5-382">Common storage options for files include:</span></span>

* <span data-ttu-id="317d5-383">Base de données</span><span class="sxs-lookup"><span data-stu-id="317d5-383">Database</span></span>

  * <span data-ttu-id="317d5-384">Pour les petits chargements de fichiers, une base de données est souvent plus rapide que les options de stockage physique (système de fichiers ou partage réseau).</span><span class="sxs-lookup"><span data-stu-id="317d5-384">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="317d5-385">Une base de données est souvent plus pratique que les options de stockage physique, car la récupération d’un enregistrement de base de données pour les données utilisateur peut fournir simultanément le contenu du fichier (par exemple, une image de l’avatar).</span><span class="sxs-lookup"><span data-stu-id="317d5-385">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="317d5-386">Une base de données est potentiellement moins coûteuse que l’utilisation d’un service de stockage de données.</span><span class="sxs-lookup"><span data-stu-id="317d5-386">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="317d5-387">Stockage physique (système de fichiers ou partage réseau)</span><span class="sxs-lookup"><span data-stu-id="317d5-387">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="317d5-388">Pour les chargements de fichiers volumineux :</span><span class="sxs-lookup"><span data-stu-id="317d5-388">For large file uploads:</span></span>
    * <span data-ttu-id="317d5-389">Les limites de base de données peuvent limiter la taille du téléchargement.</span><span class="sxs-lookup"><span data-stu-id="317d5-389">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="317d5-390">Le stockage physique est souvent moins économique que le stockage dans une base de données.</span><span class="sxs-lookup"><span data-stu-id="317d5-390">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="317d5-391">Le stockage physique est potentiellement moins onéreux que l’utilisation d’un service de stockage de données.</span><span class="sxs-lookup"><span data-stu-id="317d5-391">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="317d5-392">Le processus de l’application doit disposer d’autorisations en lecture et en écriture sur l’emplacement de stockage.</span><span class="sxs-lookup"><span data-stu-id="317d5-392">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="317d5-393">**N’accordez jamais l’autorisation EXECUTE.**</span><span class="sxs-lookup"><span data-stu-id="317d5-393">**Never grant execute permission.**</span></span>

* <span data-ttu-id="317d5-394">Service de stockage de données (par exemple, [stockage d’objets BLOB Azure](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="317d5-394">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="317d5-395">Les services offrent généralement une évolutivité et une résilience améliorées par rapport aux solutions locales qui sont généralement sujettes à des points de défaillance uniques.</span><span class="sxs-lookup"><span data-stu-id="317d5-395">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="317d5-396">Les services sont potentiellement moins coûteux dans les scénarios d’infrastructure de stockage volumineux.</span><span class="sxs-lookup"><span data-stu-id="317d5-396">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="317d5-397">Pour plus d’informations, consultez [démarrage rapide : utiliser .net pour créer un objet BLOB dans le stockage d’objets](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="317d5-397">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="317d5-398">Scénarios de chargement de fichiers</span><span class="sxs-lookup"><span data-stu-id="317d5-398">File upload scenarios</span></span>

<span data-ttu-id="317d5-399">La mise en mémoire tampon et la diffusion en continu sont deux approches générales pour le téléchargement de fichiers.</span><span class="sxs-lookup"><span data-stu-id="317d5-399">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="317d5-400">**des réponses**</span><span class="sxs-lookup"><span data-stu-id="317d5-400">**Buffering**</span></span>

<span data-ttu-id="317d5-401">La totalité du fichier est lue dans un <xref:Microsoft.AspNetCore.Http.IFormFile> , qui est une représentation C# du fichier utilisé pour traiter ou enregistrer le fichier.</span><span class="sxs-lookup"><span data-stu-id="317d5-401">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="317d5-402">Les ressources (disque, mémoire) utilisées par les chargements de fichiers dépendent du nombre et de la taille des chargements de fichiers simultanés.</span><span class="sxs-lookup"><span data-stu-id="317d5-402">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="317d5-403">Si une application tente de mettre en mémoire tampon un trop grand nombre de chargements, le site se bloque lorsqu’il manque de mémoire ou d’espace disque.</span><span class="sxs-lookup"><span data-stu-id="317d5-403">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="317d5-404">Si la taille ou la fréquence des chargements de fichiers épuisent les ressources d’application, utilisez la diffusion en continu.</span><span class="sxs-lookup"><span data-stu-id="317d5-404">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="317d5-405">Tout fichier mis en mémoire tampon dépassant 64 Ko est déplacé de la mémoire vers un fichier temporaire sur le disque.</span><span class="sxs-lookup"><span data-stu-id="317d5-405">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="317d5-406">La mise en mémoire tampon de petits fichiers est traitée dans les sections suivantes de cette rubrique :</span><span class="sxs-lookup"><span data-stu-id="317d5-406">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="317d5-407">Stockage physique</span><span class="sxs-lookup"><span data-stu-id="317d5-407">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="317d5-408">Sauvegarde de la base de données</span><span class="sxs-lookup"><span data-stu-id="317d5-408">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="317d5-409">**Streaming**</span><span class="sxs-lookup"><span data-stu-id="317d5-409">**Streaming**</span></span>

<span data-ttu-id="317d5-410">Le fichier est reçu à partir d’une demande en plusieurs parties et est directement traité ou enregistré par l’application.</span><span class="sxs-lookup"><span data-stu-id="317d5-410">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="317d5-411">La diffusion en continu n’améliore pas les performances de manière significative.</span><span class="sxs-lookup"><span data-stu-id="317d5-411">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="317d5-412">La diffusion en continu réduit les demandes de mémoire ou d’espace disque lors du chargement de fichiers.</span><span class="sxs-lookup"><span data-stu-id="317d5-412">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="317d5-413">La diffusion en continu de fichiers volumineux est traitée dans la section [chargement de fichiers volumineux avec diffusion](#upload-large-files-with-streaming) .</span><span class="sxs-lookup"><span data-stu-id="317d5-413">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="317d5-414">Charger des fichiers de petite taille avec une liaison de modèle mise en mémoire tampon vers un stockage physique</span><span class="sxs-lookup"><span data-stu-id="317d5-414">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="317d5-415">Pour télécharger des fichiers de petite taille, utilisez un formulaire en plusieurs parties ou créez une requête de publication à l’aide de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="317d5-415">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="317d5-416">L’exemple suivant illustre l’utilisation d’un Razor formulaire de pages pour télécharger un seul fichier (*pages/BufferedSingleFileUploadPhysical. cshtml* dans l’exemple d’application) :</span><span class="sxs-lookup"><span data-stu-id="317d5-416">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="317d5-417">L’exemple suivant est similaire à l’exemple précédent, à l’exception de :</span><span class="sxs-lookup"><span data-stu-id="317d5-417">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="317d5-418">JavaScript ([API FETCH](https://developer.mozilla.org/docs/Web/API/Fetch_API)) est utilisé pour envoyer les données du formulaire.</span><span class="sxs-lookup"><span data-stu-id="317d5-418">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="317d5-419">Il n’y a aucune validation.</span><span class="sxs-lookup"><span data-stu-id="317d5-419">There's no validation.</span></span>

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

<span data-ttu-id="317d5-420">Pour exécuter la publication de formulaire dans JavaScript pour les clients qui [ne prennent pas en charge l’API FETCH](https://caniuse.com/#feat=fetch), utilisez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="317d5-420">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="317d5-421">Utilisez un Polyfill d’extraction (par exemple, [Window. Fetch Polyfill (GitHub/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="317d5-421">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="317d5-422">Utiliser `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="317d5-422">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="317d5-423">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="317d5-423">For example:</span></span>

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

<span data-ttu-id="317d5-424">Afin de prendre en charge les chargements de fichiers, les formulaires HTML doivent spécifier un type d’encodage ( `enctype` ) de `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="317d5-424">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="317d5-425">Pour `files` qu’un élément INPUT prenne en charge le téléchargement de plusieurs fichiers, fournissez l' `multiple` attribut sur l' `<input>` élément :</span><span class="sxs-lookup"><span data-stu-id="317d5-425">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="317d5-426">Les fichiers individuels téléchargés sur le serveur sont accessibles via la [liaison de modèle](xref:mvc/models/model-binding) à l’aide de <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="317d5-426">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="317d5-427">L’exemple d’application illustre plusieurs chargements de fichiers mis en mémoire tampon pour les scénarios de base de données et de stockage physique.</span><span class="sxs-lookup"><span data-stu-id="317d5-427">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="317d5-428">N’utilisez **pas** la `FileName` propriété <xref:Microsoft.AspNetCore.Http.IFormFile> autre que pour l’affichage et la journalisation.</span><span class="sxs-lookup"><span data-stu-id="317d5-428">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="317d5-429">Lors de l’affichage ou de la journalisation, le nom du fichier est encodé au format HTML.</span><span class="sxs-lookup"><span data-stu-id="317d5-429">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="317d5-430">Une personne malveillante peut fournir un nom de fichier malveillant, y compris les chemins d’accès complets ou les chemins d’accès relatifs.</span><span class="sxs-lookup"><span data-stu-id="317d5-430">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="317d5-431">Les applications doivent :</span><span class="sxs-lookup"><span data-stu-id="317d5-431">Applications should:</span></span>
>
> * <span data-ttu-id="317d5-432">Supprimez le chemin d’accès du nom de fichier fourni par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="317d5-432">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="317d5-433">Enregistrez le nom de fichier encodé au format HTML, avec chemin d’accès supprimé pour l’interface utilisateur ou la journalisation.</span><span class="sxs-lookup"><span data-stu-id="317d5-433">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="317d5-434">Générez un nouveau nom de fichier aléatoire pour le stockage.</span><span class="sxs-lookup"><span data-stu-id="317d5-434">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="317d5-435">Le code suivant supprime le chemin d’accès du nom de fichier :</span><span class="sxs-lookup"><span data-stu-id="317d5-435">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="317d5-436">Les exemples fournis jusqu’à présent ne prennent pas en compte les considérations de sécurité.</span><span class="sxs-lookup"><span data-stu-id="317d5-436">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="317d5-437">Des informations supplémentaires sont fournies par les sections suivantes et l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="317d5-437">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="317d5-438">Considérations relatives à la sécurité</span><span class="sxs-lookup"><span data-stu-id="317d5-438">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="317d5-439">Validation</span><span class="sxs-lookup"><span data-stu-id="317d5-439">Validation</span></span>](#validation)

<span data-ttu-id="317d5-440">Lors du chargement de fichiers à l’aide d’une liaison de modèle et <xref:Microsoft.AspNetCore.Http.IFormFile> , la méthode d’action peut accepter les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="317d5-440">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="317d5-441">Une seule <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="317d5-441">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="317d5-442">L’un des regroupements suivants qui représentent plusieurs fichiers :</span><span class="sxs-lookup"><span data-stu-id="317d5-442">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="317d5-443">[Tarifs](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="317d5-443">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="317d5-444">La liaison correspond aux fichiers de formulaire par nom.</span><span class="sxs-lookup"><span data-stu-id="317d5-444">Binding matches form files by name.</span></span> <span data-ttu-id="317d5-445">Par exemple, la `name` valeur html dans `<input type="file" name="formFile">` doit correspondre au paramètre C#/lié à la propriété ( `FormFile` ).</span><span class="sxs-lookup"><span data-stu-id="317d5-445">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="317d5-446">Pour plus d’informations, consultez la section valeur de l' [attribut de nom de correspondance pour le nom de paramètre de la méthode de publication](#match-name-attribute-value-to-parameter-name-of-post-method) .</span><span class="sxs-lookup"><span data-stu-id="317d5-446">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="317d5-447">L’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="317d5-447">The following example:</span></span>

* <span data-ttu-id="317d5-448">Effectue une boucle sur un ou plusieurs fichiers téléchargés.</span><span class="sxs-lookup"><span data-stu-id="317d5-448">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="317d5-449">Utilise [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) pour retourner le chemin d’accès complet d’un fichier, y compris le nom de fichier.</span><span class="sxs-lookup"><span data-stu-id="317d5-449">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="317d5-450">Enregistre les fichiers dans le système de fichiers local à l’aide d’un nom de fichier généré par l’application.</span><span class="sxs-lookup"><span data-stu-id="317d5-450">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="317d5-451">Retourne le nombre total et la taille des fichiers téléchargés.</span><span class="sxs-lookup"><span data-stu-id="317d5-451">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="317d5-452">Utilisez `Path.GetRandomFileName` pour générer un nom de fichier sans chemin d’accès.</span><span class="sxs-lookup"><span data-stu-id="317d5-452">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="317d5-453">Dans l’exemple suivant, le chemin d’accès est obtenu à partir de la configuration :</span><span class="sxs-lookup"><span data-stu-id="317d5-453">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="317d5-454">Le chemin d’accès passé à <xref:System.IO.FileStream> *doit* inclure le nom de fichier.</span><span class="sxs-lookup"><span data-stu-id="317d5-454">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="317d5-455">Si le nom de fichier n’est pas fourni, une <xref:System.UnauthorizedAccessException> exception est levée au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="317d5-455">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="317d5-456">Les fichiers chargés à l’aide de la <xref:Microsoft.AspNetCore.Http.IFormFile> technique sont mis en mémoire tampon ou sur disque sur le serveur avant le traitement.</span><span class="sxs-lookup"><span data-stu-id="317d5-456">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="317d5-457">À l’intérieur de la méthode d’action, le <xref:Microsoft.AspNetCore.Http.IFormFile> contenu est accessible en tant que <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="317d5-457">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="317d5-458">Outre le système de fichiers local, les fichiers peuvent être enregistrés sur un partage réseau ou un service de stockage de fichiers, tel que le [stockage d’objets BLOB Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="317d5-458">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="317d5-459">Pour obtenir un autre exemple qui effectue une boucle sur plusieurs fichiers pour le téléchargement et utilise des noms de fichiers sécurisés, consultez *pages/BufferedMultipleFileUploadPhysical. cshtml. cs* dans l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="317d5-459">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="317d5-460">[Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) lève une exception <xref:System.IO.IOException> si plus de 65 535 fichiers sont créés sans supprimer les fichiers temporaires précédents.</span><span class="sxs-lookup"><span data-stu-id="317d5-460">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="317d5-461">La limite de 65 535 fichiers est une limite par serveur.</span><span class="sxs-lookup"><span data-stu-id="317d5-461">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="317d5-462">Pour plus d’informations sur cette limite sur le système d’exploitation Windows, consultez les notes dans les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="317d5-462">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="317d5-463">GetTempFileNameA fonction)</span><span class="sxs-lookup"><span data-stu-id="317d5-463">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="317d5-464">Charger des fichiers de petite taille avec une liaison de modèle mise en mémoire tampon vers une base de données</span><span class="sxs-lookup"><span data-stu-id="317d5-464">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="317d5-465">Pour stocker des données de fichier binaires dans une base de données à l’aide de [Entity Framework](/ef/core/index), définissez une <xref:System.Byte> propriété de tableau sur l’entité :</span><span class="sxs-lookup"><span data-stu-id="317d5-465">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="317d5-466">Spécifiez une propriété de modèle de page pour la classe qui comprend <xref:Microsoft.AspNetCore.Http.IFormFile> :</span><span class="sxs-lookup"><span data-stu-id="317d5-466">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="317d5-467"><xref:Microsoft.AspNetCore.Http.IFormFile> peut être utilisé directement comme paramètre de méthode d’action ou comme propriété de modèle liée.</span><span class="sxs-lookup"><span data-stu-id="317d5-467"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="317d5-468">L’exemple précédent utilise une propriété de modèle liée.</span><span class="sxs-lookup"><span data-stu-id="317d5-468">The prior example uses a bound model property.</span></span>

<span data-ttu-id="317d5-469">Le `FileUpload` est utilisé dans le Razor formulaire pages :</span><span class="sxs-lookup"><span data-stu-id="317d5-469">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="317d5-470">Lorsque le formulaire est publié sur le serveur, copiez <xref:Microsoft.AspNetCore.Http.IFormFile> -le dans un flux et enregistrez-le en tant que tableau d’octets dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="317d5-470">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="317d5-471">Dans l’exemple suivant, `_dbContext` stocke le contexte de base de données de l’application :</span><span class="sxs-lookup"><span data-stu-id="317d5-471">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="317d5-472">L’exemple précédent est semblable à un scénario illustré dans l’exemple d’application :</span><span class="sxs-lookup"><span data-stu-id="317d5-472">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="317d5-473">*Pages/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="317d5-473">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="317d5-474">*Pages/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="317d5-474">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="317d5-475">Soyez prudent quand vous stockez des données binaires dans des bases de données relationnelles, car cela peut avoir un impact négatif sur les performances.</span><span class="sxs-lookup"><span data-stu-id="317d5-475">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="317d5-476">Ne vous fiez pas à la `FileName` propriété de sans validation ni à approuver celle-ci <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="317d5-476">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="317d5-477">La `FileName` propriété doit uniquement être utilisée à des fins d’affichage et uniquement après l’encodage HTML.</span><span class="sxs-lookup"><span data-stu-id="317d5-477">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="317d5-478">Les exemples fournis ne prennent pas en compte les considérations de sécurité.</span><span class="sxs-lookup"><span data-stu-id="317d5-478">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="317d5-479">Des informations supplémentaires sont fournies par les sections suivantes et l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="317d5-479">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="317d5-480">Considérations relatives à la sécurité</span><span class="sxs-lookup"><span data-stu-id="317d5-480">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="317d5-481">Validation</span><span class="sxs-lookup"><span data-stu-id="317d5-481">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="317d5-482">Charger des fichiers volumineux avec streaming</span><span class="sxs-lookup"><span data-stu-id="317d5-482">Upload large files with streaming</span></span>

<span data-ttu-id="317d5-483">L’exemple suivant montre comment utiliser JavaScript pour diffuser un fichier vers une action de contrôleur.</span><span class="sxs-lookup"><span data-stu-id="317d5-483">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="317d5-484">Le jeton anti-contrefaçon du fichier est généré à l’aide d’un attribut de filtre personnalisé et transmis aux en-têtes HTTP du client plutôt que dans le corps de la demande.</span><span class="sxs-lookup"><span data-stu-id="317d5-484">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="317d5-485">Étant donné que la méthode d’action traite directement les données chargées, la liaison de modèle de formulaire est désactivée par un autre filtre personnalisé.</span><span class="sxs-lookup"><span data-stu-id="317d5-485">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="317d5-486">Dans l’action, le contenu du formulaire est lu en avec `MultipartReader`, qui lit chaque `MultipartSection` individuelle, traitant le fichier ou enregistrant le contenu selon ce qui est approprié.</span><span class="sxs-lookup"><span data-stu-id="317d5-486">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="317d5-487">Une fois les sections en plusieurs parties lues, l’action effectue sa propre liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="317d5-487">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="317d5-488">La réponse de page initiale charge le formulaire et enregistre un jeton anti-contrefaçon dans un cookie (via l' `GenerateAntiforgeryTokenCookieAttribute` attribut).</span><span class="sxs-lookup"><span data-stu-id="317d5-488">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="317d5-489">L’attribut utilise la [prise en charge de l’anticontrefaçon](xref:security/anti-request-forgery) intégrée de ASP.net Core pour définir un cookie avec un jeton de demande :</span><span class="sxs-lookup"><span data-stu-id="317d5-489">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="317d5-490">`DisableFormValueModelBindingAttribute`Est utilisé pour désactiver la liaison de modèle :</span><span class="sxs-lookup"><span data-stu-id="317d5-490">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="317d5-491">Dans l’exemple d’application, `GenerateAntiforgeryTokenCookieAttribute` et `DisableFormValueModelBindingAttribute` sont appliqués en tant que filtres aux modèles d’application de page de et à l' `/StreamedSingleFileUploadDb` aide des conventions de `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` [ Razor pages](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="317d5-491">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=7-10,16-19)]

<span data-ttu-id="317d5-492">Étant donné que la liaison de modèle ne lit pas le formulaire, les paramètres qui sont liés depuis le formulaire ne sont pas liés (la requête, l’itinéraire et l’en-tête continuent de fonctionner).</span><span class="sxs-lookup"><span data-stu-id="317d5-492">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="317d5-493">La méthode d’action fonctionne directement avec la `Request` propriété.</span><span class="sxs-lookup"><span data-stu-id="317d5-493">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="317d5-494">Un `MultipartReader` est utilisé pour lire chaque section.</span><span class="sxs-lookup"><span data-stu-id="317d5-494">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="317d5-495">Les données de clé/valeur sont stockées dans un `KeyValueAccumulator` .</span><span class="sxs-lookup"><span data-stu-id="317d5-495">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="317d5-496">Une fois les sections en plusieurs parties lues, le contenu du `KeyValueAccumulator` est utilisé pour lier les données de formulaire à un type de modèle.</span><span class="sxs-lookup"><span data-stu-id="317d5-496">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="317d5-497">La `StreamingController.UploadDatabase` méthode complète pour la diffusion en continu vers une base de données avec EF Core :</span><span class="sxs-lookup"><span data-stu-id="317d5-497">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="317d5-498">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper. cs*) :</span><span class="sxs-lookup"><span data-stu-id="317d5-498">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="317d5-499">Méthode complète `StreamingController.UploadPhysical` pour la diffusion en continu vers un emplacement physique :</span><span class="sxs-lookup"><span data-stu-id="317d5-499">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="317d5-500">Dans l’exemple d’application, les contrôles de validation sont gérés par `FileHelpers.ProcessStreamedFile` .</span><span class="sxs-lookup"><span data-stu-id="317d5-500">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="317d5-501">Validation</span><span class="sxs-lookup"><span data-stu-id="317d5-501">Validation</span></span>

<span data-ttu-id="317d5-502">La classe de l’exemple d’application `FileHelpers` illustre plusieurs vérifications des chargements de fichiers mis en mémoire tampon <xref:Microsoft.AspNetCore.Http.IFormFile> et diffusés en continu.</span><span class="sxs-lookup"><span data-stu-id="317d5-502">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="317d5-503">Pour traiter les <xref:Microsoft.AspNetCore.Http.IFormFile> chargements de fichiers mis en mémoire tampon dans l’exemple d’application, consultez la `ProcessFormFile` méthode dans le fichier *Utilities/FileHelpers. cs* .</span><span class="sxs-lookup"><span data-stu-id="317d5-503">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="317d5-504">Pour le traitement de fichiers en continu, consultez la `ProcessStreamedFile` méthode dans le même fichier.</span><span class="sxs-lookup"><span data-stu-id="317d5-504">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="317d5-505">Les méthodes de traitement de validation présentées dans l’exemple d’application n’analysent pas le contenu des fichiers téléchargés.</span><span class="sxs-lookup"><span data-stu-id="317d5-505">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="317d5-506">Dans la plupart des scénarios de production, une API de détection de virus et de logiciels malveillants est utilisée sur le fichier avant que le fichier soit mis à la disposition des utilisateurs ou d’autres systèmes.</span><span class="sxs-lookup"><span data-stu-id="317d5-506">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="317d5-507">Bien que l’exemple de rubrique fournit un exemple fonctionnel de techniques de validation, n’implémentez pas la `FileHelpers` classe dans une application de production, sauf si vous :</span><span class="sxs-lookup"><span data-stu-id="317d5-507">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="317d5-508">Comprenez pleinement l’implémentation.</span><span class="sxs-lookup"><span data-stu-id="317d5-508">Fully understand the implementation.</span></span>
> * <span data-ttu-id="317d5-509">Modifiez l’implémentation en fonction de l’environnement et des spécifications de l’application.</span><span class="sxs-lookup"><span data-stu-id="317d5-509">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="317d5-510">**N’implémentez jamais non plus de code de sécurité dans une application sans répondre à ces exigences.**</span><span class="sxs-lookup"><span data-stu-id="317d5-510">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="317d5-511">Validation du contenu</span><span class="sxs-lookup"><span data-stu-id="317d5-511">Content validation</span></span>

<span data-ttu-id="317d5-512">**Utilisez une API d’analyse de virus/programme malveillant sur le contenu chargé.**</span><span class="sxs-lookup"><span data-stu-id="317d5-512">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="317d5-513">L’analyse des fichiers exige des ressources serveur dans des scénarios de volume élevé.</span><span class="sxs-lookup"><span data-stu-id="317d5-513">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="317d5-514">Si les performances de traitement des demandes sont réduites en raison de l’analyse de fichiers, envisagez de décharger le travail d’analyse vers un [service en arrière-plan](xref:fundamentals/host/hosted-services), éventuellement un service qui s’exécute sur un serveur différent du serveur de l’application.</span><span class="sxs-lookup"><span data-stu-id="317d5-514">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="317d5-515">En règle générale, les fichiers téléchargés sont conservés dans une zone en quarantaine jusqu’à ce que l’antivirus en arrière-plan les vérifie.</span><span class="sxs-lookup"><span data-stu-id="317d5-515">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="317d5-516">Lorsqu’un fichier réussit, le fichier est déplacé vers l’emplacement de stockage de fichiers normal.</span><span class="sxs-lookup"><span data-stu-id="317d5-516">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="317d5-517">Ces étapes sont généralement effectuées conjointement avec un enregistrement de base de données qui indique l’état d’analyse d’un fichier.</span><span class="sxs-lookup"><span data-stu-id="317d5-517">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="317d5-518">À l’aide de cette approche, l’application et le serveur d’applications restent concentrés sur la réponse aux requêtes.</span><span class="sxs-lookup"><span data-stu-id="317d5-518">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="317d5-519">Validation de l’extension de fichier</span><span class="sxs-lookup"><span data-stu-id="317d5-519">File extension validation</span></span>

<span data-ttu-id="317d5-520">L’extension du fichier chargé doit être vérifiée par rapport à une liste d’extensions autorisées.</span><span class="sxs-lookup"><span data-stu-id="317d5-520">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="317d5-521">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="317d5-521">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="317d5-522">Validation de la signature de fichier</span><span class="sxs-lookup"><span data-stu-id="317d5-522">File signature validation</span></span>

<span data-ttu-id="317d5-523">La signature d’un fichier est déterminée par les premiers octets au début d’un fichier.</span><span class="sxs-lookup"><span data-stu-id="317d5-523">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="317d5-524">Ces octets peuvent être utilisés pour indiquer si l’extension correspond au contenu du fichier.</span><span class="sxs-lookup"><span data-stu-id="317d5-524">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="317d5-525">L’exemple d’application vérifie les signatures de fichiers pour quelques types de fichiers courants.</span><span class="sxs-lookup"><span data-stu-id="317d5-525">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="317d5-526">Dans l’exemple suivant, la signature de fichier pour une image JPEG est vérifiée par rapport au fichier :</span><span class="sxs-lookup"><span data-stu-id="317d5-526">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="317d5-527">Pour obtenir des signatures de fichiers supplémentaires, consultez la [base de données signatures de fichiers](https://www.filesignatures.net/) et les spécifications de fichier officielles.</span><span class="sxs-lookup"><span data-stu-id="317d5-527">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="317d5-528">Sécurité des noms de fichiers</span><span class="sxs-lookup"><span data-stu-id="317d5-528">File name security</span></span>

<span data-ttu-id="317d5-529">N’utilisez jamais un nom de fichier fourni par le client pour enregistrer un fichier dans le stockage physique.</span><span class="sxs-lookup"><span data-stu-id="317d5-529">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="317d5-530">Créez un nom de fichier sécurisé pour le fichier à l’aide de [Path. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) ou [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) pour créer un chemin d’accès complet (y compris le nom de fichier) pour le stockage temporaire.</span><span class="sxs-lookup"><span data-stu-id="317d5-530">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="317d5-531">Razor encode automatiquement les valeurs des propriétés pour l’affichage.</span><span class="sxs-lookup"><span data-stu-id="317d5-531">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="317d5-532">Le code suivant peut être utilisé en toute sécurité :</span><span class="sxs-lookup"><span data-stu-id="317d5-532">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="317d5-533">En dehors de Razor , toujours <xref:System.Net.WebUtility.HtmlEncode*> le contenu du nom de fichier à partir de la demande d’un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="317d5-533">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="317d5-534">De nombreuses implémentations doivent inclure une vérification de l’existence du fichier ; dans le cas contraire, le fichier est remplacé par un fichier du même nom.</span><span class="sxs-lookup"><span data-stu-id="317d5-534">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="317d5-535">Fournissez une logique supplémentaire pour répondre aux spécifications de votre application.</span><span class="sxs-lookup"><span data-stu-id="317d5-535">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="317d5-536">Validation de la taille</span><span class="sxs-lookup"><span data-stu-id="317d5-536">Size validation</span></span>

<span data-ttu-id="317d5-537">Limitez la taille des fichiers téléchargés.</span><span class="sxs-lookup"><span data-stu-id="317d5-537">Limit the size of uploaded files.</span></span>

<span data-ttu-id="317d5-538">Dans l’exemple d’application, la taille du fichier est limitée à 2 Mo (indiquée en octets).</span><span class="sxs-lookup"><span data-stu-id="317d5-538">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="317d5-539">La limite est fournie via la [configuration](xref:fundamentals/configuration/index) à partir du *appsettings.json* fichier :</span><span class="sxs-lookup"><span data-stu-id="317d5-539">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="317d5-540">`FileSizeLimit`Est injecté dans des `PageModel` classes :</span><span class="sxs-lookup"><span data-stu-id="317d5-540">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="317d5-541">Quand une taille de fichier dépasse la limite, le fichier est rejeté :</span><span class="sxs-lookup"><span data-stu-id="317d5-541">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="317d5-542">Valeur de l’attribut de nom de correspondance avec le nom de paramètre de la méthode de publication</span><span class="sxs-lookup"><span data-stu-id="317d5-542">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="317d5-543">Dans les non- Razor formulaires qui publient des données de formulaire ou utilisent `FormData` directement JavaScript, le nom spécifié dans l’élément du formulaire ou `FormData` doit correspondre au nom du paramètre dans l’action du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="317d5-543">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="317d5-544">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="317d5-544">In the following example:</span></span>

* <span data-ttu-id="317d5-545">Lorsque vous utilisez un `<input>` élément, l' `name` attribut est défini sur la valeur `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="317d5-545">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="317d5-546">Lors de l’utilisation `FormData` de dans JavaScript, le nom est défini sur la valeur `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="317d5-546">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="317d5-547">Utilisez un nom correspondant pour le paramètre de la méthode C# ( `battlePlans` ) :</span><span class="sxs-lookup"><span data-stu-id="317d5-547">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="317d5-548">Pour une Razor méthode de gestionnaire de page pages nommée `Upload` :</span><span class="sxs-lookup"><span data-stu-id="317d5-548">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="317d5-549">Pour une méthode d’action du billet de contrôleur MVC :</span><span class="sxs-lookup"><span data-stu-id="317d5-549">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="317d5-550">Configuration du serveur et de l’application</span><span class="sxs-lookup"><span data-stu-id="317d5-550">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="317d5-551">Longueur limite du corps en plusieurs parties</span><span class="sxs-lookup"><span data-stu-id="317d5-551">Multipart body length limit</span></span>

<span data-ttu-id="317d5-552"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> définit la limite de la longueur de chaque corps en plusieurs parties.</span><span class="sxs-lookup"><span data-stu-id="317d5-552"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="317d5-553">Les sections de formulaire qui dépassent cette limite lèvent une lorsqu’elles sont <xref:System.IO.InvalidDataException> analysées.</span><span class="sxs-lookup"><span data-stu-id="317d5-553">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="317d5-554">La valeur par défaut est 134 217 728 (128 Mo).</span><span class="sxs-lookup"><span data-stu-id="317d5-554">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="317d5-555">Personnaliser la limite à l’aide du <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> paramètre dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="317d5-555">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="317d5-556"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> est utilisé pour définir le <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> pour une seule page ou action.</span><span class="sxs-lookup"><span data-stu-id="317d5-556"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="317d5-557">Dans une Razor application pages, appliquez le filtre avec une [Convention](xref:razor-pages/razor-pages-conventions) dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="317d5-557">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="317d5-558">Dans une Razor application de pages ou une application MVC, appliquez le filtre au modèle de page ou à la méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="317d5-558">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="317d5-559">Taille maximale du corps de la demande Kestrel</span><span class="sxs-lookup"><span data-stu-id="317d5-559">Kestrel maximum request body size</span></span>

<span data-ttu-id="317d5-560">Pour les applications hébergées par Kestrel, la taille de corps de requête maximale par défaut est de 30 millions octets, soit environ 28,6 Mo.</span><span class="sxs-lookup"><span data-stu-id="317d5-560">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="317d5-561">Personnaliser la limite à l’aide de l’option de serveur [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel :</span><span class="sxs-lookup"><span data-stu-id="317d5-561">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="317d5-562"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> est utilisé pour définir le [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) pour une seule page ou action.</span><span class="sxs-lookup"><span data-stu-id="317d5-562"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="317d5-563">Dans une Razor application pages, appliquez le filtre avec une [Convention](xref:razor-pages/razor-pages-conventions) dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="317d5-563">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="317d5-564">Dans une Razor application de pages ou une application MVC, appliquez le filtre à la classe du gestionnaire de pages ou à la méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="317d5-564">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="317d5-565">`RequestSizeLimitAttribute`Peut également être appliqué à l’aide de la [`@attribute`](xref:mvc/views/razor#attribute) Razor directive :</span><span class="sxs-lookup"><span data-stu-id="317d5-565">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="317d5-566">Autres limites Kestrel</span><span class="sxs-lookup"><span data-stu-id="317d5-566">Other Kestrel limits</span></span>

<span data-ttu-id="317d5-567">D’autres limites Kestrel peuvent s’appliquer aux applications hébergées par Kestrel :</span><span class="sxs-lookup"><span data-stu-id="317d5-567">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="317d5-568">Nombre maximale de connexions client</span><span class="sxs-lookup"><span data-stu-id="317d5-568">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="317d5-569">Taux de données de demande et de réponse</span><span class="sxs-lookup"><span data-stu-id="317d5-569">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a><span data-ttu-id="317d5-570">IIS</span><span class="sxs-lookup"><span data-stu-id="317d5-570">IIS</span></span>

<span data-ttu-id="317d5-571">La limite de demandes par défaut ( `maxAllowedContentLength` ) est de 30 millions octets, soit environ 28,6 Mo.</span><span class="sxs-lookup"><span data-stu-id="317d5-571">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="317d5-572">Personnaliser la limite dans le `web.config` fichier.</span><span class="sxs-lookup"><span data-stu-id="317d5-572">Customize the limit in the `web.config` file.</span></span> <span data-ttu-id="317d5-573">Dans l’exemple suivant, la limite est définie sur 50 Mo (52 428 800 octets) :</span><span class="sxs-lookup"><span data-stu-id="317d5-573">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="317d5-574">Le `maxAllowedContentLength` paramètre s’applique uniquement à IIS.</span><span class="sxs-lookup"><span data-stu-id="317d5-574">The `maxAllowedContentLength` setting only applies to IIS.</span></span> <span data-ttu-id="317d5-575">Pour plus d’informations, consultez [limites `<requestLimits>` des demandes ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="317d5-575">For more information, see [Request Limits `<requestLimits>`](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="317d5-576">Augmentez la taille maximale du corps de la requête pour la requête HTTP en définissant <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="317d5-576">Increase the maximum request body size for the HTTP request by setting <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="317d5-577">Dans l’exemple suivant, la limite est définie sur 50 Mo (52 428 800 octets) :</span><span class="sxs-lookup"><span data-stu-id="317d5-577">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```csharp
services.Configure<IISServerOptions>(options =>
{
    options.MaxRequestBodySize = 52428800;
});
```

<span data-ttu-id="317d5-578">Pour plus d’informations, consultez <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="317d5-578">For more information, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="317d5-579">Dépanner</span><span class="sxs-lookup"><span data-stu-id="317d5-579">Troubleshoot</span></span>

<span data-ttu-id="317d5-580">Voici certains problèmes courants rencontrés avec le chargement de fichiers et leurs solutions possibles.</span><span class="sxs-lookup"><span data-stu-id="317d5-580">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="317d5-581">Erreur introuvable lors du déploiement sur un serveur IIS</span><span class="sxs-lookup"><span data-stu-id="317d5-581">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="317d5-582">L’erreur suivante indique que le fichier chargé dépasse la longueur du contenu configurée du serveur :</span><span class="sxs-lookup"><span data-stu-id="317d5-582">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="317d5-583">Pour plus d’informations, consultez la section [IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="317d5-583">For more information, see the [IIS](#iis) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="317d5-584">Échec de connexion</span><span class="sxs-lookup"><span data-stu-id="317d5-584">Connection failure</span></span>

<span data-ttu-id="317d5-585">Une erreur de connexion et une connexion du serveur de réinitialisation indiquent probablement que le fichier téléchargé dépasse la taille maximale du corps de la demande de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="317d5-585">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="317d5-586">Pour plus d’informations, consultez la section [taille maximale du corps de la demande Kestrel](#kestrel-maximum-request-body-size) .</span><span class="sxs-lookup"><span data-stu-id="317d5-586">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="317d5-587">Les limites de connexion du client Kestrel peuvent également nécessiter des ajustements.</span><span class="sxs-lookup"><span data-stu-id="317d5-587">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="317d5-588">Exception de référence null avec IFormFile</span><span class="sxs-lookup"><span data-stu-id="317d5-588">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="317d5-589">Si le contrôleur accepte les fichiers téléchargés à l’aide <xref:Microsoft.AspNetCore.Http.IFormFile> de mais que la valeur est `null` , vérifiez que le formulaire HTML spécifie la `enctype` valeur `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="317d5-589">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="317d5-590">Si cet attribut n’est pas défini sur l' `<form>` élément, le chargement du fichier ne se produit pas et tous les <xref:Microsoft.AspNetCore.Http.IFormFile> arguments liés sont `null` .</span><span class="sxs-lookup"><span data-stu-id="317d5-590">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="317d5-591">Vérifiez également que l' [appellation de chargement dans les données de formulaire correspond à celle de l’application](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="317d5-591">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="317d5-592">Le flux est trop long</span><span class="sxs-lookup"><span data-stu-id="317d5-592">Stream was too long</span></span>

<span data-ttu-id="317d5-593">Les exemples de cette rubrique reposent sur <xref:System.IO.MemoryStream> pour stocker le contenu du fichier chargé.</span><span class="sxs-lookup"><span data-stu-id="317d5-593">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="317d5-594">La limite de taille d’un `MemoryStream` est `int.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="317d5-594">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="317d5-595">Si le scénario de chargement de fichiers de l’application nécessite la conservation d’un contenu de fichier d’une taille supérieure à 50 Mo, utilisez une autre approche qui ne repose pas sur un seul `MemoryStream` pour conserver le contenu d’un fichier chargé.</span><span class="sxs-lookup"><span data-stu-id="317d5-595">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="317d5-596">ASP.NET Core prend en charge le chargement d’un ou plusieurs fichiers à l’aide d’une liaison de modèle mise en mémoire tampon pour les fichiers plus petits et la diffusion en continu sans mise en mémoire tampon pour les fichiers plus volumineux</span><span class="sxs-lookup"><span data-stu-id="317d5-596">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="317d5-597">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/file-uploads/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="317d5-597">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="317d5-598">Considérations relatives à la sécurité</span><span class="sxs-lookup"><span data-stu-id="317d5-598">Security considerations</span></span>

<span data-ttu-id="317d5-599">Soyez prudent lorsque vous fournissez aux utilisateurs la possibilité de charger des fichiers sur un serveur.</span><span class="sxs-lookup"><span data-stu-id="317d5-599">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="317d5-600">Les attaquants peuvent tenter d’effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="317d5-600">Attackers may attempt to:</span></span>

* <span data-ttu-id="317d5-601">Exécuter [des attaques par déni de service](/windows-hardware/drivers/ifs/denial-of-service) .</span><span class="sxs-lookup"><span data-stu-id="317d5-601">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="317d5-602">Télécharger des virus ou des programmes malveillants.</span><span class="sxs-lookup"><span data-stu-id="317d5-602">Upload viruses or malware.</span></span>
* <span data-ttu-id="317d5-603">Compromettre les réseaux et les serveurs d’une autre manière.</span><span class="sxs-lookup"><span data-stu-id="317d5-603">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="317d5-604">Les étapes de sécurité qui réduisent la probabilité d’une attaque réussie sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="317d5-604">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="317d5-605">Chargez les fichiers dans une zone de chargement de fichier dédiée, de préférence sur un lecteur non-système.</span><span class="sxs-lookup"><span data-stu-id="317d5-605">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="317d5-606">Un emplacement dédié permet d’imposer des restrictions de sécurité plus faciles sur les fichiers téléchargés.</span><span class="sxs-lookup"><span data-stu-id="317d5-606">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="317d5-607">Désactivez les autorisations d’exécution sur l’emplacement de chargement du fichier.&dagger;</span><span class="sxs-lookup"><span data-stu-id="317d5-607">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="317d5-608">Ne conservez **pas** les fichiers téléchargés dans la même arborescence de répertoires que l’application.&dagger;</span><span class="sxs-lookup"><span data-stu-id="317d5-608">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="317d5-609">Utilisez un nom de fichier sécurisé déterminé par l’application.</span><span class="sxs-lookup"><span data-stu-id="317d5-609">Use a safe file name determined by the app.</span></span> <span data-ttu-id="317d5-610">N’utilisez pas un nom de fichier fourni par l’utilisateur ou le nom de fichier non approuvé du fichier téléchargé. &dagger; Code HTML encode le nom de fichier non fiable lors de son affichage.</span><span class="sxs-lookup"><span data-stu-id="317d5-610">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="317d5-611">Par exemple, la journalisation du nom de fichier ou l’affichage dans l’interface utilisateur ( Razor génère automatiquement le code html).</span><span class="sxs-lookup"><span data-stu-id="317d5-611">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="317d5-612">Autorisez uniquement les extensions de fichier approuvées pour la spécification de conception de l’application.&dagger;</span><span class="sxs-lookup"><span data-stu-id="317d5-612">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="317d5-613">Vérifiez que les vérifications côté client sont effectuées sur le serveur. &dagger; Les contrôles côté client sont faciles à contourner.</span><span class="sxs-lookup"><span data-stu-id="317d5-613">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="317d5-614">Vérifiez la taille d’un fichier téléchargé.</span><span class="sxs-lookup"><span data-stu-id="317d5-614">Check the size of an uploaded file.</span></span> <span data-ttu-id="317d5-615">Définissez une limite de taille maximale pour empêcher les chargements volumineux.&dagger;</span><span class="sxs-lookup"><span data-stu-id="317d5-615">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="317d5-616">Lorsque les fichiers ne doivent pas être remplacés par un fichier téléchargé portant le même nom, vérifiez le nom du fichier par rapport à la base de données ou au stockage physique avant de charger le fichier.</span><span class="sxs-lookup"><span data-stu-id="317d5-616">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="317d5-617">**Exécutez un programme de détection de virus et de logiciels malveillants sur le contenu chargé avant que le fichier ne soit stocké.**</span><span class="sxs-lookup"><span data-stu-id="317d5-617">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="317d5-618">&dagger;L’exemple d’application illustre une approche qui répond aux critères.</span><span class="sxs-lookup"><span data-stu-id="317d5-618">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="317d5-619">Le chargement d’un code malveillant sur un système est généralement la première étape de l’exécution de code capable de :</span><span class="sxs-lookup"><span data-stu-id="317d5-619">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="317d5-620">Maîtrisez complètement un système.</span><span class="sxs-lookup"><span data-stu-id="317d5-620">Completely gain control of a system.</span></span>
> * <span data-ttu-id="317d5-621">Surchargez un système avec le résultat du blocage du système.</span><span class="sxs-lookup"><span data-stu-id="317d5-621">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="317d5-622">Compromettre les données utilisateur ou système.</span><span class="sxs-lookup"><span data-stu-id="317d5-622">Compromise user or system data.</span></span>
> * <span data-ttu-id="317d5-623">Appliquez Graffiti à une interface utilisateur publique.</span><span class="sxs-lookup"><span data-stu-id="317d5-623">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="317d5-624">Pour plus d’informations sur la réduction de la surface d’attaque quand vous acceptez des fichiers d’utilisateurs, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="317d5-624">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * <span data-ttu-id="317d5-625">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) (Chargement de fichiers illimité)</span><span class="sxs-lookup"><span data-stu-id="317d5-625">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)</span></span>
> * [<span data-ttu-id="317d5-626">Sécurité Azure : Vérifier que les contrôles appropriés sont en place quand vous acceptez des fichiers d’utilisateurs</span><span class="sxs-lookup"><span data-stu-id="317d5-626">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="317d5-627">Pour plus d’informations sur l’implémentation de mesures de sécurité, y compris des exemples de l’exemple d’application, consultez la section [validation](#validation) .</span><span class="sxs-lookup"><span data-stu-id="317d5-627">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="317d5-628">Scénarios de stockage</span><span class="sxs-lookup"><span data-stu-id="317d5-628">Storage scenarios</span></span>

<span data-ttu-id="317d5-629">Les options de stockage courantes pour les fichiers sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="317d5-629">Common storage options for files include:</span></span>

* <span data-ttu-id="317d5-630">Base de données</span><span class="sxs-lookup"><span data-stu-id="317d5-630">Database</span></span>

  * <span data-ttu-id="317d5-631">Pour les petits chargements de fichiers, une base de données est souvent plus rapide que les options de stockage physique (système de fichiers ou partage réseau).</span><span class="sxs-lookup"><span data-stu-id="317d5-631">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="317d5-632">Une base de données est souvent plus pratique que les options de stockage physique, car la récupération d’un enregistrement de base de données pour les données utilisateur peut fournir simultanément le contenu du fichier (par exemple, une image de l’avatar).</span><span class="sxs-lookup"><span data-stu-id="317d5-632">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="317d5-633">Une base de données est potentiellement moins coûteuse que l’utilisation d’un service de stockage de données.</span><span class="sxs-lookup"><span data-stu-id="317d5-633">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="317d5-634">Stockage physique (système de fichiers ou partage réseau)</span><span class="sxs-lookup"><span data-stu-id="317d5-634">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="317d5-635">Pour les chargements de fichiers volumineux :</span><span class="sxs-lookup"><span data-stu-id="317d5-635">For large file uploads:</span></span>
    * <span data-ttu-id="317d5-636">Les limites de base de données peuvent limiter la taille du téléchargement.</span><span class="sxs-lookup"><span data-stu-id="317d5-636">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="317d5-637">Le stockage physique est souvent moins économique que le stockage dans une base de données.</span><span class="sxs-lookup"><span data-stu-id="317d5-637">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="317d5-638">Le stockage physique est potentiellement moins onéreux que l’utilisation d’un service de stockage de données.</span><span class="sxs-lookup"><span data-stu-id="317d5-638">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="317d5-639">Le processus de l’application doit disposer d’autorisations en lecture et en écriture sur l’emplacement de stockage.</span><span class="sxs-lookup"><span data-stu-id="317d5-639">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="317d5-640">**N’accordez jamais l’autorisation EXECUTE.**</span><span class="sxs-lookup"><span data-stu-id="317d5-640">**Never grant execute permission.**</span></span>

* <span data-ttu-id="317d5-641">Service de stockage de données (par exemple, [stockage d’objets BLOB Azure](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="317d5-641">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="317d5-642">Les services offrent généralement une évolutivité et une résilience améliorées par rapport aux solutions locales qui sont généralement sujettes à des points de défaillance uniques.</span><span class="sxs-lookup"><span data-stu-id="317d5-642">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="317d5-643">Les services sont potentiellement moins coûteux dans les scénarios d’infrastructure de stockage volumineux.</span><span class="sxs-lookup"><span data-stu-id="317d5-643">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="317d5-644">Pour plus d’informations, consultez [démarrage rapide : utiliser .net pour créer un objet BLOB dans le stockage d’objets](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="317d5-644">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="317d5-645">La rubrique montre <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*> , mais <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> peut être utilisée pour enregistrer un <xref:System.IO.FileStream> dans le stockage d’objets BLOB quand vous utilisez un <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="317d5-645">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="317d5-646">Scénarios de chargement de fichiers</span><span class="sxs-lookup"><span data-stu-id="317d5-646">File upload scenarios</span></span>

<span data-ttu-id="317d5-647">La mise en mémoire tampon et la diffusion en continu sont deux approches générales pour le téléchargement de fichiers.</span><span class="sxs-lookup"><span data-stu-id="317d5-647">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="317d5-648">**des réponses**</span><span class="sxs-lookup"><span data-stu-id="317d5-648">**Buffering**</span></span>

<span data-ttu-id="317d5-649">La totalité du fichier est lue dans un <xref:Microsoft.AspNetCore.Http.IFormFile> , qui est une représentation C# du fichier utilisé pour traiter ou enregistrer le fichier.</span><span class="sxs-lookup"><span data-stu-id="317d5-649">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="317d5-650">Les ressources (disque, mémoire) utilisées par les chargements de fichiers dépendent du nombre et de la taille des chargements de fichiers simultanés.</span><span class="sxs-lookup"><span data-stu-id="317d5-650">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="317d5-651">Si une application tente de mettre en mémoire tampon un trop grand nombre de chargements, le site se bloque lorsqu’il manque de mémoire ou d’espace disque.</span><span class="sxs-lookup"><span data-stu-id="317d5-651">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="317d5-652">Si la taille ou la fréquence des chargements de fichiers épuisent les ressources d’application, utilisez la diffusion en continu.</span><span class="sxs-lookup"><span data-stu-id="317d5-652">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="317d5-653">Tout fichier mis en mémoire tampon dépassant 64 Ko est déplacé de la mémoire vers un fichier temporaire sur le disque.</span><span class="sxs-lookup"><span data-stu-id="317d5-653">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="317d5-654">La mise en mémoire tampon de petits fichiers est traitée dans les sections suivantes de cette rubrique :</span><span class="sxs-lookup"><span data-stu-id="317d5-654">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="317d5-655">Stockage physique</span><span class="sxs-lookup"><span data-stu-id="317d5-655">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="317d5-656">Sauvegarde de la base de données</span><span class="sxs-lookup"><span data-stu-id="317d5-656">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="317d5-657">**Streaming**</span><span class="sxs-lookup"><span data-stu-id="317d5-657">**Streaming**</span></span>

<span data-ttu-id="317d5-658">Le fichier est reçu à partir d’une demande en plusieurs parties et est directement traité ou enregistré par l’application.</span><span class="sxs-lookup"><span data-stu-id="317d5-658">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="317d5-659">La diffusion en continu n’améliore pas les performances de manière significative.</span><span class="sxs-lookup"><span data-stu-id="317d5-659">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="317d5-660">La diffusion en continu réduit les demandes de mémoire ou d’espace disque lors du chargement de fichiers.</span><span class="sxs-lookup"><span data-stu-id="317d5-660">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="317d5-661">La diffusion en continu de fichiers volumineux est traitée dans la section [chargement de fichiers volumineux avec diffusion](#upload-large-files-with-streaming) .</span><span class="sxs-lookup"><span data-stu-id="317d5-661">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="317d5-662">Charger des fichiers de petite taille avec une liaison de modèle mise en mémoire tampon vers un stockage physique</span><span class="sxs-lookup"><span data-stu-id="317d5-662">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="317d5-663">Pour télécharger des fichiers de petite taille, utilisez un formulaire en plusieurs parties ou créez une requête de publication à l’aide de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="317d5-663">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="317d5-664">L’exemple suivant illustre l’utilisation d’un Razor formulaire de pages pour télécharger un seul fichier (*pages/BufferedSingleFileUploadPhysical. cshtml* dans l’exemple d’application) :</span><span class="sxs-lookup"><span data-stu-id="317d5-664">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="317d5-665">L’exemple suivant est similaire à l’exemple précédent, à l’exception de :</span><span class="sxs-lookup"><span data-stu-id="317d5-665">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="317d5-666">JavaScript ([API FETCH](https://developer.mozilla.org/docs/Web/API/Fetch_API)) est utilisé pour envoyer les données du formulaire.</span><span class="sxs-lookup"><span data-stu-id="317d5-666">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="317d5-667">Il n’y a aucune validation.</span><span class="sxs-lookup"><span data-stu-id="317d5-667">There's no validation.</span></span>

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

<span data-ttu-id="317d5-668">Pour exécuter la publication de formulaire dans JavaScript pour les clients qui [ne prennent pas en charge l’API FETCH](https://caniuse.com/#feat=fetch), utilisez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="317d5-668">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="317d5-669">Utilisez un Polyfill d’extraction (par exemple, [Window. Fetch Polyfill (GitHub/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="317d5-669">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="317d5-670">Utiliser `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="317d5-670">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="317d5-671">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="317d5-671">For example:</span></span>

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

<span data-ttu-id="317d5-672">Afin de prendre en charge les chargements de fichiers, les formulaires HTML doivent spécifier un type d’encodage ( `enctype` ) de `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="317d5-672">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="317d5-673">Pour `files` qu’un élément INPUT prenne en charge le téléchargement de plusieurs fichiers, fournissez l' `multiple` attribut sur l' `<input>` élément :</span><span class="sxs-lookup"><span data-stu-id="317d5-673">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="317d5-674">Les fichiers individuels téléchargés sur le serveur sont accessibles via la [liaison de modèle](xref:mvc/models/model-binding) à l’aide de <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="317d5-674">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="317d5-675">L’exemple d’application illustre plusieurs chargements de fichiers mis en mémoire tampon pour les scénarios de base de données et de stockage physique.</span><span class="sxs-lookup"><span data-stu-id="317d5-675">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename2"></a>

> [!WARNING]
> <span data-ttu-id="317d5-676">N’utilisez **pas** la `FileName` propriété <xref:Microsoft.AspNetCore.Http.IFormFile> autre que pour l’affichage et la journalisation.</span><span class="sxs-lookup"><span data-stu-id="317d5-676">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="317d5-677">Lors de l’affichage ou de la journalisation, le nom du fichier est encodé au format HTML.</span><span class="sxs-lookup"><span data-stu-id="317d5-677">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="317d5-678">Une personne malveillante peut fournir un nom de fichier malveillant, y compris les chemins d’accès complets ou les chemins d’accès relatifs.</span><span class="sxs-lookup"><span data-stu-id="317d5-678">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="317d5-679">Les applications doivent :</span><span class="sxs-lookup"><span data-stu-id="317d5-679">Applications should:</span></span>
>
> * <span data-ttu-id="317d5-680">Supprimez le chemin d’accès du nom de fichier fourni par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="317d5-680">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="317d5-681">Enregistrez le nom de fichier encodé au format HTML, avec chemin d’accès supprimé pour l’interface utilisateur ou la journalisation.</span><span class="sxs-lookup"><span data-stu-id="317d5-681">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="317d5-682">Générez un nouveau nom de fichier aléatoire pour le stockage.</span><span class="sxs-lookup"><span data-stu-id="317d5-682">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="317d5-683">Le code suivant supprime le chemin d’accès du nom de fichier :</span><span class="sxs-lookup"><span data-stu-id="317d5-683">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="317d5-684">Les exemples fournis jusqu’à présent ne prennent pas en compte les considérations de sécurité.</span><span class="sxs-lookup"><span data-stu-id="317d5-684">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="317d5-685">Des informations supplémentaires sont fournies par les sections suivantes et l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="317d5-685">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="317d5-686">Considérations relatives à la sécurité</span><span class="sxs-lookup"><span data-stu-id="317d5-686">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="317d5-687">Validation</span><span class="sxs-lookup"><span data-stu-id="317d5-687">Validation</span></span>](#validation)

<span data-ttu-id="317d5-688">Lors du chargement de fichiers à l’aide d’une liaison de modèle et <xref:Microsoft.AspNetCore.Http.IFormFile> , la méthode d’action peut accepter les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="317d5-688">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="317d5-689">Une seule <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="317d5-689">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="317d5-690">L’un des regroupements suivants qui représentent plusieurs fichiers :</span><span class="sxs-lookup"><span data-stu-id="317d5-690">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="317d5-691">[Tarifs](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="317d5-691">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="317d5-692">La liaison correspond aux fichiers de formulaire par nom.</span><span class="sxs-lookup"><span data-stu-id="317d5-692">Binding matches form files by name.</span></span> <span data-ttu-id="317d5-693">Par exemple, la `name` valeur html dans `<input type="file" name="formFile">` doit correspondre au paramètre C#/lié à la propriété ( `FormFile` ).</span><span class="sxs-lookup"><span data-stu-id="317d5-693">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="317d5-694">Pour plus d’informations, consultez la section valeur de l' [attribut de nom de correspondance pour le nom de paramètre de la méthode de publication](#match-name-attribute-value-to-parameter-name-of-post-method) .</span><span class="sxs-lookup"><span data-stu-id="317d5-694">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="317d5-695">L’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="317d5-695">The following example:</span></span>

* <span data-ttu-id="317d5-696">Effectue une boucle sur un ou plusieurs fichiers téléchargés.</span><span class="sxs-lookup"><span data-stu-id="317d5-696">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="317d5-697">Utilise [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) pour retourner le chemin d’accès complet d’un fichier, y compris le nom de fichier.</span><span class="sxs-lookup"><span data-stu-id="317d5-697">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="317d5-698">Enregistre les fichiers dans le système de fichiers local à l’aide d’un nom de fichier généré par l’application.</span><span class="sxs-lookup"><span data-stu-id="317d5-698">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="317d5-699">Retourne le nombre total et la taille des fichiers téléchargés.</span><span class="sxs-lookup"><span data-stu-id="317d5-699">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="317d5-700">Utilisez `Path.GetRandomFileName` pour générer un nom de fichier sans chemin d’accès.</span><span class="sxs-lookup"><span data-stu-id="317d5-700">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="317d5-701">Dans l’exemple suivant, le chemin d’accès est obtenu à partir de la configuration :</span><span class="sxs-lookup"><span data-stu-id="317d5-701">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="317d5-702">Le chemin d’accès passé à <xref:System.IO.FileStream> *doit* inclure le nom de fichier.</span><span class="sxs-lookup"><span data-stu-id="317d5-702">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="317d5-703">Si le nom de fichier n’est pas fourni, une <xref:System.UnauthorizedAccessException> exception est levée au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="317d5-703">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="317d5-704">Les fichiers chargés à l’aide de la <xref:Microsoft.AspNetCore.Http.IFormFile> technique sont mis en mémoire tampon ou sur disque sur le serveur avant le traitement.</span><span class="sxs-lookup"><span data-stu-id="317d5-704">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="317d5-705">À l’intérieur de la méthode d’action, le <xref:Microsoft.AspNetCore.Http.IFormFile> contenu est accessible en tant que <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="317d5-705">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="317d5-706">Outre le système de fichiers local, les fichiers peuvent être enregistrés sur un partage réseau ou un service de stockage de fichiers, tel que le [stockage d’objets BLOB Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="317d5-706">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="317d5-707">Pour obtenir un autre exemple qui effectue une boucle sur plusieurs fichiers pour le téléchargement et utilise des noms de fichiers sécurisés, consultez *pages/BufferedMultipleFileUploadPhysical. cshtml. cs* dans l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="317d5-707">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="317d5-708">[Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) lève une exception <xref:System.IO.IOException> si plus de 65 535 fichiers sont créés sans supprimer les fichiers temporaires précédents.</span><span class="sxs-lookup"><span data-stu-id="317d5-708">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="317d5-709">La limite de 65 535 fichiers est une limite par serveur.</span><span class="sxs-lookup"><span data-stu-id="317d5-709">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="317d5-710">Pour plus d’informations sur cette limite sur le système d’exploitation Windows, consultez les notes dans les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="317d5-710">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="317d5-711">GetTempFileNameA fonction)</span><span class="sxs-lookup"><span data-stu-id="317d5-711">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="317d5-712">Charger des fichiers de petite taille avec une liaison de modèle mise en mémoire tampon vers une base de données</span><span class="sxs-lookup"><span data-stu-id="317d5-712">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="317d5-713">Pour stocker des données de fichier binaires dans une base de données à l’aide de [Entity Framework](/ef/core/index), définissez une <xref:System.Byte> propriété de tableau sur l’entité :</span><span class="sxs-lookup"><span data-stu-id="317d5-713">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="317d5-714">Spécifiez une propriété de modèle de page pour la classe qui comprend <xref:Microsoft.AspNetCore.Http.IFormFile> :</span><span class="sxs-lookup"><span data-stu-id="317d5-714">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="317d5-715"><xref:Microsoft.AspNetCore.Http.IFormFile> peut être utilisé directement comme paramètre de méthode d’action ou comme propriété de modèle liée.</span><span class="sxs-lookup"><span data-stu-id="317d5-715"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="317d5-716">L’exemple précédent utilise une propriété de modèle liée.</span><span class="sxs-lookup"><span data-stu-id="317d5-716">The prior example uses a bound model property.</span></span>

<span data-ttu-id="317d5-717">Le `FileUpload` est utilisé dans le Razor formulaire pages :</span><span class="sxs-lookup"><span data-stu-id="317d5-717">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="317d5-718">Lorsque le formulaire est publié sur le serveur, copiez <xref:Microsoft.AspNetCore.Http.IFormFile> -le dans un flux et enregistrez-le en tant que tableau d’octets dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="317d5-718">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="317d5-719">Dans l’exemple suivant, `_dbContext` stocke le contexte de base de données de l’application :</span><span class="sxs-lookup"><span data-stu-id="317d5-719">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="317d5-720">L’exemple précédent est semblable à un scénario illustré dans l’exemple d’application :</span><span class="sxs-lookup"><span data-stu-id="317d5-720">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="317d5-721">*Pages/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="317d5-721">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="317d5-722">*Pages/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="317d5-722">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="317d5-723">Soyez prudent quand vous stockez des données binaires dans des bases de données relationnelles, car cela peut avoir un impact négatif sur les performances.</span><span class="sxs-lookup"><span data-stu-id="317d5-723">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="317d5-724">Ne vous fiez pas à la `FileName` propriété de sans validation ni à approuver celle-ci <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="317d5-724">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="317d5-725">La `FileName` propriété doit uniquement être utilisée à des fins d’affichage et uniquement après l’encodage HTML.</span><span class="sxs-lookup"><span data-stu-id="317d5-725">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="317d5-726">Les exemples fournis ne prennent pas en compte les considérations de sécurité.</span><span class="sxs-lookup"><span data-stu-id="317d5-726">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="317d5-727">Des informations supplémentaires sont fournies par les sections suivantes et l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="317d5-727">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="317d5-728">Considérations relatives à la sécurité</span><span class="sxs-lookup"><span data-stu-id="317d5-728">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="317d5-729">Validation</span><span class="sxs-lookup"><span data-stu-id="317d5-729">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="317d5-730">Charger des fichiers volumineux avec streaming</span><span class="sxs-lookup"><span data-stu-id="317d5-730">Upload large files with streaming</span></span>

<span data-ttu-id="317d5-731">L’exemple suivant montre comment utiliser JavaScript pour diffuser un fichier vers une action de contrôleur.</span><span class="sxs-lookup"><span data-stu-id="317d5-731">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="317d5-732">Le jeton anti-contrefaçon du fichier est généré à l’aide d’un attribut de filtre personnalisé et transmis aux en-têtes HTTP du client plutôt que dans le corps de la demande.</span><span class="sxs-lookup"><span data-stu-id="317d5-732">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="317d5-733">Étant donné que la méthode d’action traite directement les données chargées, la liaison de modèle de formulaire est désactivée par un autre filtre personnalisé.</span><span class="sxs-lookup"><span data-stu-id="317d5-733">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="317d5-734">Dans l’action, le contenu du formulaire est lu en avec `MultipartReader`, qui lit chaque `MultipartSection` individuelle, traitant le fichier ou enregistrant le contenu selon ce qui est approprié.</span><span class="sxs-lookup"><span data-stu-id="317d5-734">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="317d5-735">Une fois les sections en plusieurs parties lues, l’action effectue sa propre liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="317d5-735">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="317d5-736">La réponse de page initiale charge le formulaire et enregistre un jeton anti-contrefaçon dans un cookie (via l' `GenerateAntiforgeryTokenCookieAttribute` attribut).</span><span class="sxs-lookup"><span data-stu-id="317d5-736">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="317d5-737">L’attribut utilise la [prise en charge de l’anticontrefaçon](xref:security/anti-request-forgery) intégrée de ASP.net Core pour définir un cookie avec un jeton de demande :</span><span class="sxs-lookup"><span data-stu-id="317d5-737">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="317d5-738">`DisableFormValueModelBindingAttribute`Est utilisé pour désactiver la liaison de modèle :</span><span class="sxs-lookup"><span data-stu-id="317d5-738">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="317d5-739">Dans l’exemple d’application, `GenerateAntiforgeryTokenCookieAttribute` et `DisableFormValueModelBindingAttribute` sont appliqués en tant que filtres aux modèles d’application de page de et à l' `/StreamedSingleFileUploadDb` aide des conventions de `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` [ Razor pages](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="317d5-739">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="317d5-740">Étant donné que la liaison de modèle ne lit pas le formulaire, les paramètres qui sont liés depuis le formulaire ne sont pas liés (la requête, l’itinéraire et l’en-tête continuent de fonctionner).</span><span class="sxs-lookup"><span data-stu-id="317d5-740">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="317d5-741">La méthode d’action fonctionne directement avec la `Request` propriété.</span><span class="sxs-lookup"><span data-stu-id="317d5-741">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="317d5-742">Un `MultipartReader` est utilisé pour lire chaque section.</span><span class="sxs-lookup"><span data-stu-id="317d5-742">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="317d5-743">Les données de clé/valeur sont stockées dans un `KeyValueAccumulator` .</span><span class="sxs-lookup"><span data-stu-id="317d5-743">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="317d5-744">Une fois les sections en plusieurs parties lues, le contenu du `KeyValueAccumulator` est utilisé pour lier les données de formulaire à un type de modèle.</span><span class="sxs-lookup"><span data-stu-id="317d5-744">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="317d5-745">La `StreamingController.UploadDatabase` méthode complète pour la diffusion en continu vers une base de données avec EF Core :</span><span class="sxs-lookup"><span data-stu-id="317d5-745">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="317d5-746">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper. cs*) :</span><span class="sxs-lookup"><span data-stu-id="317d5-746">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="317d5-747">Méthode complète `StreamingController.UploadPhysical` pour la diffusion en continu vers un emplacement physique :</span><span class="sxs-lookup"><span data-stu-id="317d5-747">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="317d5-748">Dans l’exemple d’application, les contrôles de validation sont gérés par `FileHelpers.ProcessStreamedFile` .</span><span class="sxs-lookup"><span data-stu-id="317d5-748">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="317d5-749">Validation</span><span class="sxs-lookup"><span data-stu-id="317d5-749">Validation</span></span>

<span data-ttu-id="317d5-750">La classe de l’exemple d’application `FileHelpers` illustre plusieurs vérifications des chargements de fichiers mis en mémoire tampon <xref:Microsoft.AspNetCore.Http.IFormFile> et diffusés en continu.</span><span class="sxs-lookup"><span data-stu-id="317d5-750">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="317d5-751">Pour traiter les <xref:Microsoft.AspNetCore.Http.IFormFile> chargements de fichiers mis en mémoire tampon dans l’exemple d’application, consultez la `ProcessFormFile` méthode dans le fichier *Utilities/FileHelpers. cs* .</span><span class="sxs-lookup"><span data-stu-id="317d5-751">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="317d5-752">Pour le traitement de fichiers en continu, consultez la `ProcessStreamedFile` méthode dans le même fichier.</span><span class="sxs-lookup"><span data-stu-id="317d5-752">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="317d5-753">Les méthodes de traitement de validation présentées dans l’exemple d’application n’analysent pas le contenu des fichiers téléchargés.</span><span class="sxs-lookup"><span data-stu-id="317d5-753">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="317d5-754">Dans la plupart des scénarios de production, une API de détection de virus et de logiciels malveillants est utilisée sur le fichier avant que le fichier soit mis à la disposition des utilisateurs ou d’autres systèmes.</span><span class="sxs-lookup"><span data-stu-id="317d5-754">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="317d5-755">Bien que l’exemple de rubrique fournit un exemple fonctionnel de techniques de validation, n’implémentez pas la `FileHelpers` classe dans une application de production, sauf si vous :</span><span class="sxs-lookup"><span data-stu-id="317d5-755">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="317d5-756">Comprenez pleinement l’implémentation.</span><span class="sxs-lookup"><span data-stu-id="317d5-756">Fully understand the implementation.</span></span>
> * <span data-ttu-id="317d5-757">Modifiez l’implémentation en fonction de l’environnement et des spécifications de l’application.</span><span class="sxs-lookup"><span data-stu-id="317d5-757">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="317d5-758">**N’implémentez jamais non plus de code de sécurité dans une application sans répondre à ces exigences.**</span><span class="sxs-lookup"><span data-stu-id="317d5-758">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="317d5-759">Validation du contenu</span><span class="sxs-lookup"><span data-stu-id="317d5-759">Content validation</span></span>

<span data-ttu-id="317d5-760">**Utilisez une API d’analyse de virus/programme malveillant sur le contenu chargé.**</span><span class="sxs-lookup"><span data-stu-id="317d5-760">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="317d5-761">L’analyse des fichiers exige des ressources serveur dans des scénarios de volume élevé.</span><span class="sxs-lookup"><span data-stu-id="317d5-761">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="317d5-762">Si les performances de traitement des demandes sont réduites en raison de l’analyse de fichiers, envisagez de décharger le travail d’analyse vers un [service en arrière-plan](xref:fundamentals/host/hosted-services), éventuellement un service qui s’exécute sur un serveur différent du serveur de l’application.</span><span class="sxs-lookup"><span data-stu-id="317d5-762">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="317d5-763">En règle générale, les fichiers téléchargés sont conservés dans une zone en quarantaine jusqu’à ce que l’antivirus en arrière-plan les vérifie.</span><span class="sxs-lookup"><span data-stu-id="317d5-763">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="317d5-764">Lorsqu’un fichier réussit, le fichier est déplacé vers l’emplacement de stockage de fichiers normal.</span><span class="sxs-lookup"><span data-stu-id="317d5-764">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="317d5-765">Ces étapes sont généralement effectuées conjointement avec un enregistrement de base de données qui indique l’état d’analyse d’un fichier.</span><span class="sxs-lookup"><span data-stu-id="317d5-765">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="317d5-766">À l’aide de cette approche, l’application et le serveur d’applications restent concentrés sur la réponse aux requêtes.</span><span class="sxs-lookup"><span data-stu-id="317d5-766">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="317d5-767">Validation de l’extension de fichier</span><span class="sxs-lookup"><span data-stu-id="317d5-767">File extension validation</span></span>

<span data-ttu-id="317d5-768">L’extension du fichier chargé doit être vérifiée par rapport à une liste d’extensions autorisées.</span><span class="sxs-lookup"><span data-stu-id="317d5-768">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="317d5-769">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="317d5-769">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="317d5-770">Validation de la signature de fichier</span><span class="sxs-lookup"><span data-stu-id="317d5-770">File signature validation</span></span>

<span data-ttu-id="317d5-771">La signature d’un fichier est déterminée par les premiers octets au début d’un fichier.</span><span class="sxs-lookup"><span data-stu-id="317d5-771">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="317d5-772">Ces octets peuvent être utilisés pour indiquer si l’extension correspond au contenu du fichier.</span><span class="sxs-lookup"><span data-stu-id="317d5-772">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="317d5-773">L’exemple d’application vérifie les signatures de fichiers pour quelques types de fichiers courants.</span><span class="sxs-lookup"><span data-stu-id="317d5-773">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="317d5-774">Dans l’exemple suivant, la signature de fichier pour une image JPEG est vérifiée par rapport au fichier :</span><span class="sxs-lookup"><span data-stu-id="317d5-774">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="317d5-775">Pour obtenir des signatures de fichiers supplémentaires, consultez la [base de données signatures de fichiers](https://www.filesignatures.net/) et les spécifications de fichier officielles.</span><span class="sxs-lookup"><span data-stu-id="317d5-775">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="317d5-776">Sécurité des noms de fichiers</span><span class="sxs-lookup"><span data-stu-id="317d5-776">File name security</span></span>

<span data-ttu-id="317d5-777">N’utilisez jamais un nom de fichier fourni par le client pour enregistrer un fichier dans le stockage physique.</span><span class="sxs-lookup"><span data-stu-id="317d5-777">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="317d5-778">Créez un nom de fichier sécurisé pour le fichier à l’aide de [Path. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) ou [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) pour créer un chemin d’accès complet (y compris le nom de fichier) pour le stockage temporaire.</span><span class="sxs-lookup"><span data-stu-id="317d5-778">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="317d5-779">Razor encode automatiquement les valeurs des propriétés pour l’affichage.</span><span class="sxs-lookup"><span data-stu-id="317d5-779">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="317d5-780">Le code suivant peut être utilisé en toute sécurité :</span><span class="sxs-lookup"><span data-stu-id="317d5-780">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="317d5-781">En dehors de Razor , toujours <xref:System.Net.WebUtility.HtmlEncode*> le contenu du nom de fichier à partir de la demande d’un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="317d5-781">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="317d5-782">De nombreuses implémentations doivent inclure une vérification de l’existence du fichier ; dans le cas contraire, le fichier est remplacé par un fichier du même nom.</span><span class="sxs-lookup"><span data-stu-id="317d5-782">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="317d5-783">Fournissez une logique supplémentaire pour répondre aux spécifications de votre application.</span><span class="sxs-lookup"><span data-stu-id="317d5-783">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="317d5-784">Validation de la taille</span><span class="sxs-lookup"><span data-stu-id="317d5-784">Size validation</span></span>

<span data-ttu-id="317d5-785">Limitez la taille des fichiers téléchargés.</span><span class="sxs-lookup"><span data-stu-id="317d5-785">Limit the size of uploaded files.</span></span>

<span data-ttu-id="317d5-786">Dans l’exemple d’application, la taille du fichier est limitée à 2 Mo (indiquée en octets).</span><span class="sxs-lookup"><span data-stu-id="317d5-786">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="317d5-787">La limite est fournie via la [configuration](xref:fundamentals/configuration/index) à partir du *appsettings.json* fichier :</span><span class="sxs-lookup"><span data-stu-id="317d5-787">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="317d5-788">`FileSizeLimit`Est injecté dans des `PageModel` classes :</span><span class="sxs-lookup"><span data-stu-id="317d5-788">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="317d5-789">Quand une taille de fichier dépasse la limite, le fichier est rejeté :</span><span class="sxs-lookup"><span data-stu-id="317d5-789">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="317d5-790">Valeur de l’attribut de nom de correspondance avec le nom de paramètre de la méthode de publication</span><span class="sxs-lookup"><span data-stu-id="317d5-790">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="317d5-791">Dans les non- Razor formulaires qui publient des données de formulaire ou utilisent `FormData` directement JavaScript, le nom spécifié dans l’élément du formulaire ou `FormData` doit correspondre au nom du paramètre dans l’action du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="317d5-791">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="317d5-792">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="317d5-792">In the following example:</span></span>

* <span data-ttu-id="317d5-793">Lorsque vous utilisez un `<input>` élément, l' `name` attribut est défini sur la valeur `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="317d5-793">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="317d5-794">Lors de l’utilisation `FormData` de dans JavaScript, le nom est défini sur la valeur `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="317d5-794">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="317d5-795">Utilisez un nom correspondant pour le paramètre de la méthode C# ( `battlePlans` ) :</span><span class="sxs-lookup"><span data-stu-id="317d5-795">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="317d5-796">Pour une Razor méthode de gestionnaire de page pages nommée `Upload` :</span><span class="sxs-lookup"><span data-stu-id="317d5-796">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="317d5-797">Pour une méthode d’action du billet de contrôleur MVC :</span><span class="sxs-lookup"><span data-stu-id="317d5-797">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="317d5-798">Configuration du serveur et de l’application</span><span class="sxs-lookup"><span data-stu-id="317d5-798">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="317d5-799">Longueur limite du corps en plusieurs parties</span><span class="sxs-lookup"><span data-stu-id="317d5-799">Multipart body length limit</span></span>

<span data-ttu-id="317d5-800"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> définit la limite de la longueur de chaque corps en plusieurs parties.</span><span class="sxs-lookup"><span data-stu-id="317d5-800"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="317d5-801">Les sections de formulaire qui dépassent cette limite lèvent une lorsqu’elles sont <xref:System.IO.InvalidDataException> analysées.</span><span class="sxs-lookup"><span data-stu-id="317d5-801">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="317d5-802">La valeur par défaut est 134 217 728 (128 Mo).</span><span class="sxs-lookup"><span data-stu-id="317d5-802">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="317d5-803">Personnaliser la limite à l’aide du <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> paramètre dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="317d5-803">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="317d5-804"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> est utilisé pour définir le <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> pour une seule page ou action.</span><span class="sxs-lookup"><span data-stu-id="317d5-804"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="317d5-805">Dans une Razor application pages, appliquez le filtre avec une [Convention](xref:razor-pages/razor-pages-conventions) dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="317d5-805">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="317d5-806">Dans une Razor application de pages ou une application MVC, appliquez le filtre au modèle de page ou à la méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="317d5-806">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="317d5-807">Taille maximale du corps de la demande Kestrel</span><span class="sxs-lookup"><span data-stu-id="317d5-807">Kestrel maximum request body size</span></span>

<span data-ttu-id="317d5-808">Pour les applications hébergées par Kestrel, la taille de corps de requête maximale par défaut est de 30 millions octets, soit environ 28,6 Mo.</span><span class="sxs-lookup"><span data-stu-id="317d5-808">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="317d5-809">Personnaliser la limite à l’aide de l’option de serveur [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel :</span><span class="sxs-lookup"><span data-stu-id="317d5-809">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="317d5-810"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> est utilisé pour définir le [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) pour une seule page ou action.</span><span class="sxs-lookup"><span data-stu-id="317d5-810"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="317d5-811">Dans une Razor application pages, appliquez le filtre avec une [Convention](xref:razor-pages/razor-pages-conventions) dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="317d5-811">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="317d5-812">Dans une Razor application de pages ou une application MVC, appliquez le filtre à la classe du gestionnaire de pages ou à la méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="317d5-812">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="317d5-813">Autres limites Kestrel</span><span class="sxs-lookup"><span data-stu-id="317d5-813">Other Kestrel limits</span></span>

<span data-ttu-id="317d5-814">D’autres limites Kestrel peuvent s’appliquer aux applications hébergées par Kestrel :</span><span class="sxs-lookup"><span data-stu-id="317d5-814">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="317d5-815">Nombre maximale de connexions client</span><span class="sxs-lookup"><span data-stu-id="317d5-815">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="317d5-816">Taux de données de demande et de réponse</span><span class="sxs-lookup"><span data-stu-id="317d5-816">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a><span data-ttu-id="317d5-817">IIS</span><span class="sxs-lookup"><span data-stu-id="317d5-817">IIS</span></span>

<span data-ttu-id="317d5-818">La limite de demandes par défaut ( `maxAllowedContentLength` ) est de 30 millions octets, soit environ 28,6 Mo.</span><span class="sxs-lookup"><span data-stu-id="317d5-818">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="317d5-819">Personnaliser la limite dans le `web.config` fichier.</span><span class="sxs-lookup"><span data-stu-id="317d5-819">Customize the limit in the `web.config` file.</span></span> <span data-ttu-id="317d5-820">Dans l’exemple suivant, la limite est définie sur 50 Mo (52 428 800 octets) :</span><span class="sxs-lookup"><span data-stu-id="317d5-820">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="317d5-821">Le `maxAllowedContentLength` paramètre s’applique uniquement à IIS.</span><span class="sxs-lookup"><span data-stu-id="317d5-821">The `maxAllowedContentLength` setting only applies to IIS.</span></span> <span data-ttu-id="317d5-822">Pour plus d’informations, consultez [limites `<requestLimits>` des demandes ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="317d5-822">For more information, see [Request Limits `<requestLimits>`](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="317d5-823">Augmentez la taille maximale du corps de la requête pour la requête HTTP en définissant <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="317d5-823">Increase the maximum request body size for the HTTP request by setting <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="317d5-824">Dans l’exemple suivant, la limite est définie sur 50 Mo (52 428 800 octets) :</span><span class="sxs-lookup"><span data-stu-id="317d5-824">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```csharp
services.Configure<IISServerOptions>(options =>
{
    options.MaxRequestBodySize = 52428800;
});
```

<span data-ttu-id="317d5-825">Pour plus d’informations, consultez <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="317d5-825">For more information, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="317d5-826">Dépanner</span><span class="sxs-lookup"><span data-stu-id="317d5-826">Troubleshoot</span></span>

<span data-ttu-id="317d5-827">Voici certains problèmes courants rencontrés avec le chargement de fichiers et leurs solutions possibles.</span><span class="sxs-lookup"><span data-stu-id="317d5-827">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="317d5-828">Erreur introuvable lors du déploiement sur un serveur IIS</span><span class="sxs-lookup"><span data-stu-id="317d5-828">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="317d5-829">L’erreur suivante indique que le fichier chargé dépasse la longueur du contenu configurée du serveur :</span><span class="sxs-lookup"><span data-stu-id="317d5-829">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="317d5-830">Pour plus d’informations, consultez la section [IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="317d5-830">For more information, see the [IIS](#iis) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="317d5-831">Échec de connexion</span><span class="sxs-lookup"><span data-stu-id="317d5-831">Connection failure</span></span>

<span data-ttu-id="317d5-832">Une erreur de connexion et une connexion du serveur de réinitialisation indiquent probablement que le fichier téléchargé dépasse la taille maximale du corps de la demande de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="317d5-832">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="317d5-833">Pour plus d’informations, consultez la section [taille maximale du corps de la demande Kestrel](#kestrel-maximum-request-body-size) .</span><span class="sxs-lookup"><span data-stu-id="317d5-833">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="317d5-834">Les limites de connexion du client Kestrel peuvent également nécessiter des ajustements.</span><span class="sxs-lookup"><span data-stu-id="317d5-834">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="317d5-835">Exception de référence null avec IFormFile</span><span class="sxs-lookup"><span data-stu-id="317d5-835">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="317d5-836">Si le contrôleur accepte les fichiers téléchargés à l’aide <xref:Microsoft.AspNetCore.Http.IFormFile> de mais que la valeur est `null` , vérifiez que le formulaire HTML spécifie la `enctype` valeur `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="317d5-836">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="317d5-837">Si cet attribut n’est pas défini sur l' `<form>` élément, le chargement du fichier ne se produit pas et tous les <xref:Microsoft.AspNetCore.Http.IFormFile> arguments liés sont `null` .</span><span class="sxs-lookup"><span data-stu-id="317d5-837">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="317d5-838">Vérifiez également que l' [appellation de chargement dans les données de formulaire correspond à celle de l’application](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="317d5-838">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="317d5-839">Le flux est trop long</span><span class="sxs-lookup"><span data-stu-id="317d5-839">Stream was too long</span></span>

<span data-ttu-id="317d5-840">Les exemples de cette rubrique reposent sur <xref:System.IO.MemoryStream> pour stocker le contenu du fichier chargé.</span><span class="sxs-lookup"><span data-stu-id="317d5-840">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="317d5-841">La limite de taille d’un `MemoryStream` est `int.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="317d5-841">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="317d5-842">Si le scénario de chargement de fichiers de l’application nécessite la conservation d’un contenu de fichier d’une taille supérieure à 50 Mo, utilisez une autre approche qui ne repose pas sur un seul `MemoryStream` pour conserver le contenu d’un fichier chargé.</span><span class="sxs-lookup"><span data-stu-id="317d5-842">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="317d5-843">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="317d5-843">Additional resources</span></span>

::: moniker range="< aspnetcore-5.0"
* [<span data-ttu-id="317d5-844">Vidange des demandes de connexion HTTP</span><span class="sxs-lookup"><span data-stu-id="317d5-844">HTTP connection request draining</span></span>](xref:fundamentals/servers/kestrel#http11-request-draining)
::: moniker-end
::: moniker range=">= aspnetcore-5.0"
* [<span data-ttu-id="317d5-845">Vidange des demandes de connexion HTTP</span><span class="sxs-lookup"><span data-stu-id="317d5-845">HTTP connection request draining</span></span>](xref:fundamentals/servers/kestrel/request-draining)
::: moniker-end

* <span data-ttu-id="317d5-846">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) (Chargement de fichiers illimité)</span><span class="sxs-lookup"><span data-stu-id="317d5-846">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)</span></span>
* [<span data-ttu-id="317d5-847">Sécurité Azure : frame de sécurité : validation des entrées | Atténuations</span><span class="sxs-lookup"><span data-stu-id="317d5-847">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="317d5-848">Modèles de conception de Cloud Azure : modèle de clé valet</span><span class="sxs-lookup"><span data-stu-id="317d5-848">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)
