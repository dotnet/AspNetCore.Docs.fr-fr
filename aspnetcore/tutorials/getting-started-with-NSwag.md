---
title: Bien démarrer avec NSwag et ASP.NET Core
author: zuckerthoben
description: Découvrez comment utiliser NSwag pour générer des pages d’aide et de documentation pour une API web ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
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
uid: tutorials/get-started-with-nswag
ms.openlocfilehash: 882bbe659990cb8ea66110fccb85b8c6197df3f7
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588573"
---
# <a name="get-started-with-nswag-and-aspnet-core"></a>Bien démarrer avec NSwag et ASP.NET Core

Par [Christoph Nienaber](https://twitter.com/zuckerthoben), [Rico Suter](https://rsuter.com) et [Dave Brock](https://twitter.com/daveabrock)

::: moniker range=">= aspnetcore-2.1"

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

::: moniker-end

NSwag offre les avantages suivants :

* La possibilité d’utiliser l’interface utilisateur de Swagger et le générateur Swagger.
* Fonctionnalités de génération de code flexibles.

Avec NSwag, vous n’avez pas besoin d’une API&mdash;existante, vous pouvez utiliser des API de tiers qui intègrent Swagger et génèrent une implémentation du client. NSwag vous permet d’accélérer le cycle de développement et de vous adapter facilement aux modifications de l’API.

## <a name="register-the-nswag-middleware"></a>Inscrire le middleware NSwag

Inscrivez le middleware NSwag pour :

* Générer la spécification Swagger pour l’API web implémentée.
* Utiliser l’IU Swagger pour parcourir et tester l’API web.

Pour utiliser le middleware [NSwag](https://github.com/RicoSuter/NSwag) avec ASP.NET Core, installez le package NuGet [NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/). Ce package contient le middleware nécessaire pour générer et utiliser la spécification Swagger, l’interface utilisateur Swagger (v2 et v3) et [l’IU ReDoc](https://github.com/Rebilly/ReDoc).

Vous pouvez installer le package NuGet NSwag avec l’une des méthodes suivantes :

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* À partir de la fenêtre **Console du Gestionnaire de package** :
  * Accéder à **la**  >  console du gestionnaire de  >  **package** Windows
  * Accédez au répertoire où se trouve le fichier *TodoApi.csproj*.
  * Exécutez la commande suivante :

    ```powershell
    Install-Package NSwag.AspNetCore
    ```

* À partir de la boîte de dialogue **Gérer les packages NuGet** :
  * Cliquez avec le bouton droit sur le projet dans **Explorateur de solutions**  >  **gérer les packages NuGet**
  * Affectez la valeur « nuget.org » à **Source du package**.
  * Entrez « NSwag.AspNetCore » dans la zone de recherche.
  * Sélectionnez le package « NSwag.AspNetCore » sous l’onglet **Parcourir** et cliquez sur **Installer**.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* Cliquez avec le bouton droit sur le dossier *Packages* dans **Panneau Solutions** > **Ajouter des packages**.
* Dans la fenêtre **Ajouter des packages**, sélectionnez « nuget.org » dans la liste déroulante **Source**.
* Entrez « NSwag.AspNetCore » dans la zone de recherche.
* Sélectionnez le package « NSwag.AspNetCore » dans le volet de résultats, puis cliquez sur **Ajouter un package**.

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

Exécutez la commande suivante :

```dotnetcli
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

---

## <a name="add-and-configure-swagger-middleware"></a>Ajouter et configurer l’intergiciel (middleware) Swagger

Ajoutez et configurez Swagger dans votre application ASP.NET Core en exécutant les étapes suivantes :

* Dans la méthode `Startup.ConfigureServices`, inscrivez les services Swagger requis :

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

* Dans la méthode `Startup.Configure`, activez l’intergiciel pour traiter la spécification Swagger générée et l’IU Swagger :

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_Configure&highlight=6-7)]

* Lancer l’application. Accédez à :
  * `http://localhost:<port>/swagger` pour voir l’IU de Swagger.
  * `http://localhost:<port>/swagger/v1/swagger.json` pour voir la spécification Swagger.

## <a name="code-generation"></a>Génération de code

Vous pouvez tirer parti des fonctionnalités de génération de code de NSwag en choisissant l’une des options suivantes :

* [NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio): application de bureau Windows permettant de générer du code client d’API en C# ou à la machine à écrire.
* Les packages NuGet [NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) ou [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) pour générer du code dans votre projet.
* NSwag à partir de la [ligne de commande](https://github.com/RicoSuter/NSwag/wiki/CommandLine).
* Le package NuGet [NSwag.MSBuild](https://github.com/RicoSuter/NSwag/wiki/NSwag.MSBuild).
* [Service connecté unchase openapi (Swagger)](https://marketplace.visualstudio.com/items?itemName=Unchase.unchaseopenapiconnectedservice): service connecté à Visual Studio pour générer le code client de l’API en C# ou à la machine à écrire. Génère également des contrôleurs C# pour les services OpenAPI avec NSwag.

### <a name="generate-code-with-nswagstudio"></a>Générer du code avec NSwagStudio

* Installez NSwagStudio en suivant les instructions fournies dans le [référentiel GitHub de NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio). Dans la page de publication de NSwag, vous pouvez télécharger une version xcopy qui peut être démarrée sans les privilèges d’installation et d’administration.
* Lancez NSwagStudio et entrez l’URL du fichier *swagger.json* dans la zone de texte **Swagger Specification URL** (URL de spécification Swagger). Par exemple : *http://localhost:44354/swagger/v1/swagger.json*.
* Cliquez sur le bouton **Créer une copie locale** pour générer la représentation JSON de votre spécification Swagger.

  ![Créer une copie locale de la spécification Swagger](web-api-help-pages-using-swagger/_static/CreateLocalCopy-NSwagStudio.PNG)

* Dans la zone **Sorties**, cliquez sur la case **CSharp Client**. Selon votre projet, vous pouvez également choisir **TypeScript Client** ou **CSharp Web API Controller**. Si vous sélectionnez **CSharp Web API Controller**, une spécification de service reconstruit le service, agissant comme une génération inverse.
* Cliquez sur **Générer des sorties** pour produire une implémentation client C# complète du projet *TodoApi.NSwag*. Pour afficher le code client généré, cliquez sur l’onglet **CSharp Client** :

```csharp
//----------------------
// <auto-generated>
//     Generated using the NSwag toolchain v12.0.9.0 (NJsonSchema v9.13.10.0 (Newtonsoft.Json v11.0.0.0)) (http://NSwag.org)
// </auto-generated>
//----------------------

namespace MyNamespace
{
    #pragma warning disable

    [System.CodeDom.Compiler.GeneratedCode("NSwag", "12.0.9.0 (NJsonSchema v9.13.10.0 (Newtonsoft.Json v11.0.0.0))")]
    public partial class TodoClient
    {
        private string _baseUrl = "https://localhost:44354";
        private System.Net.Http.HttpClient _httpClient;
        private System.Lazy<Newtonsoft.Json.JsonSerializerSettings> _settings;

        public TodoClient(System.Net.Http.HttpClient httpClient)
        {
            _httpClient = httpClient;
            _settings = new System.Lazy<Newtonsoft.Json.JsonSerializerSettings>(() =>
            {
                var settings = new Newtonsoft.Json.JsonSerializerSettings();
                UpdateJsonSerializerSettings(settings);
                return settings;
            });
        }

        public string BaseUrl
        {
            get { return _baseUrl; }
            set { _baseUrl = value; }
        }

        // code omitted for brevity
```

> [!TIP]
> Le code client C# est généré en fonction des sélections de l’onglet **paramètres** . Modifiez les paramètres pour effectuer des tâches telles que le changement de nom d’espace de noms par défaut et la génération de méthode synchrone.

* Copiez le code C# généré dans un fichier dans le projet client qui utilisera l’API.
* Démarrez l’utilisation de l’API web :

```csharp
 var todoClient = new TodoClient();

// Gets all to-dos from the API
 var allTodos = await todoClient.GetAllAsync();

 // Create a new TodoItem, and save it via the API.
var createdTodo = await todoClient.CreateAsync(new TodoItem());

// Get a single to-do by ID
var foundTodo = await todoClient.GetByIdAsync(1);
```

## <a name="customize-api-documentation"></a>Personnaliser la documentation sur l’API

Swagger fournit des options pour documenter le modèle objet pour faciliter l’utilisation de l’API web.

### <a name="api-info-and-description"></a>Informations sur l’API et description

Dans la méthode `Startup.ConfigureServices`, une action de configuration passée à la méthode `AddSwaggerDocument` ajoute des informations comme l’auteur, la licence et la description :

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup2.cs?name=snippet_AddSwaggerDocument)]

L’IU Swagger affiche les informations de la version :

![Interface utilisateur de Swagger avec des informations de version](web-api-help-pages-using-swagger/_static/custom-info-nswag.png)

### <a name="xml-comments"></a>Commentaires XML

Pour activer les commentaires XML, procédez comme suit :

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* Cliquez avec le bouton droit sur le projet dans **l’Explorateur de solutions**, puis sélectionnez **Modifier <nom_projet>.csproj**.
* Ajoutez manuellement les lignes en surbrillance au fichier *.csproj* :

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* Cliquez avec le bouton droit sur le projet dans **l’Explorateur de solutions**, puis sélectionnez **Propriétés**.
* Cochez la case **fichier de documentation XML** sous la section **sortie** de l’onglet **générer** .

::: moniker-end

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* Dans le *Panneau Solutions*, appuyez sur **contrôle** et cliquez sur le nom du projet. Accédez à **Outils**  >  **modifier le fichier**.
* Ajoutez manuellement les lignes en surbrillance au fichier *.csproj* :

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* Ouvrez la boîte de dialogue **Options du projet** > **Générer** > **Compilateur**
* Cochez la case **générer la documentation XML** sous la section **Options générales** .

::: moniker-end

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

Ajoutez manuellement les lignes en surbrillance au fichier *.csproj* :

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

---

### <a name="data-annotations"></a>Annotations de données

::: moniker range="<= aspnetcore-2.0"

Étant donné que NSwag utilise la [réflexion](/dotnet/csharp/programming-guide/concepts/reflection), et le type de retour recommandé pour les actions d’API web est [IActionResult](xref:Microsoft.AspNetCore.Mvc.IActionResult), il ne peut pas déduire ce que fait votre action, ni ce qu’elle retourne.

Prenons l’exemple suivant :

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

L’action précédente retourne `IActionResult`, mais à l’intérieur de l’action, [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) ou [BadRequest](xref:System.Web.Http.ApiController.BadRequest*) sont retournés. Utilisez les annotations de données pour indiquer aux clients les codes d’état HTTP que cette action est susceptible de retourner. Marquez l’action avec les attributs suivants :

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

Comme NSwag utilise la [réflexion](/dotnet/csharp/programming-guide/concepts/reflection)et que le type de retour recommandé pour les actions de l’API Web est [ActionResult \<T> ](xref:Microsoft.AspNetCore.Mvc.ActionResult%601), il peut uniquement déduire le type de retour défini par `T` . Vous ne pouvez pas déduire automatiquement d’autres types de retour possibles.

Prenons l’exemple suivant :

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

L’action précédente retourne `ActionResult<T>`. À l’intérieur de l’action, il retourne [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*). Étant donné que le contrôleur a l' [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribut, une réponse [BadRequest](xref:System.Web.Http.ApiController.BadRequest*) est également possible. Pour plus d’informations, consultez [Réponses HTTP 400 automatiques](xref:web-api/index#automatic-http-400-responses). Utilisez les annotations de données pour indiquer aux clients les codes d’état HTTP que cette action est susceptible de retourner. Marquez l’action avec les attributs suivants :

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

Dans ASP.NET Core 2.2 ou une version ultérieure, vous pouvez utiliser les conventions comme alternatives à la décoration explicite des actions individuelles avec `[ProducesResponseType]`. Pour plus d’informations, consultez <xref:web-api/advanced/conventions>.

::: moniker-end

Le générateur Swagger peut maintenant décrire précisément cette action et les clients générés savent ce qu’ils reçoivent quand ils appellent le point de terminaison. En guise de recommandation, marquez toutes les actions avec ces attributs.

Pour obtenir des indications sur les réponses HTTP que doivent retourner vos actions d’API, consultez la [spécification RFC 7231](https://tools.ietf.org/html/rfc7231#section-4.3).
