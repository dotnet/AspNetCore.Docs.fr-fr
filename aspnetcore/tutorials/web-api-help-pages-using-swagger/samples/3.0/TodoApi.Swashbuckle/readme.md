---
page_type: sample
description: Découvrez comment ajouter Swashbuckle à votre projet d’API web ASP.NET Core pour intégrer l’IU Swagger.
languages:
- csharp
products:
- dotnet-core
- aspnet-core
- vs
- vs-code
- vs-mac
urlFragment: getstarted-swashbuckle-aspnetcore
ms.openlocfilehash: e02247325f430b0ce23dbb3f5bc344a60a1a164a
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "78659935"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a><span data-ttu-id="e8ab6-102">Bien démarrer avec Swashbuckle et ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e8ab6-102">Get started with Swashbuckle and ASP.NET Core</span></span>

<span data-ttu-id="e8ab6-103">Les différentes méthodes qui permettent d’utiliser une API web ne sont pas toujours simples à comprendre pour les développeurs.</span><span class="sxs-lookup"><span data-stu-id="e8ab6-103">When consuming a Web API, understanding its various methods can be challenging for a developer.</span></span> <span data-ttu-id="e8ab6-104">[Swagger](https://swagger.io/), également appelé [OpenAPI](https://www.openapis.org/), résout le problème de la génération de pages d’aide et de documentation qu’utilisent les API web.</span><span class="sxs-lookup"><span data-stu-id="e8ab6-104">[Swagger](https://swagger.io/), also known as [OpenAPI](https://www.openapis.org/), solves the problem of generating useful documentation and help pages for Web APIs.</span></span> <span data-ttu-id="e8ab6-105">Ses avantages sont, entre autres, la documentation interactive, la génération de SDK client et la découvertibilité des API.</span><span class="sxs-lookup"><span data-stu-id="e8ab6-105">It provides benefits such as interactive documentation, client SDK generation, and API discoverability.</span></span>

<span data-ttu-id="e8ab6-106">Dans cet exemple, l’implémentation .NET [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) est indiquée.</span><span class="sxs-lookup"><span data-stu-id="e8ab6-106">In this sample, the [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) the .NET implementation is shown.</span></span>

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="e8ab6-107">Ajouter et configurer l’intergiciel (middleware) Swagger</span><span class="sxs-lookup"><span data-stu-id="e8ab6-107">Add and configure Swagger middleware</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<TodoContext>(opt =>
        opt.UseInMemoryDatabase("TodoList"));
    services.AddControllers();

    // Register the Swagger generator, defining 1 or more Swagger documents
    services.AddSwaggerGen(c =>
    {
        c.SwaggerDoc("v1", new OpenApiInfo { Title = "My API", Version = "v1" });
    });
}
```

<span data-ttu-id="e8ab6-108">Dans la méthode `Startup.Configure`, activez l’intergiciel pour traiter le document JSON généré et l’IU Swagger :</span><span class="sxs-lookup"><span data-stu-id="e8ab6-108">In the `Startup.Configure` method, enable the middleware for serving the generated JSON document and the Swagger UI:</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Enable middleware to serve generated Swagger as a JSON endpoint.
    app.UseSwagger();

    // Enable middleware to serve swagger-ui (HTML, JS, CSS, etc.),
    // specifying the Swagger JSON endpoint.
    app.UseSwaggerUI(c =>
    {
        c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1"); 
    });

    app.UseRouting();
    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

<span data-ttu-id="e8ab6-109">L’appel de méthode `UseSwaggerUI` précédent active le [middleware (intergiciel) des fichiers statiques](https://docs.microsoft.com/aspnet/core/fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="e8ab6-109">The preceding `UseSwaggerUI` method call enables the [Static File Middleware](https://docs.microsoft.com/aspnet/core/fundamentals/static-files).</span></span> <span data-ttu-id="e8ab6-110">Si vous ciblez .NET Framework ou .NET Core 1. x, ajoutez le package NuGet [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) au projet.</span><span class="sxs-lookup"><span data-stu-id="e8ab6-110">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet package to the project.</span></span>

<span data-ttu-id="e8ab6-111">Lancez l’application et accédez à `http://localhost:<port>/swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="e8ab6-111">Launch the app, and navigate to `http://localhost:<port>/swagger/v1/swagger.json`.</span></span> <span data-ttu-id="e8ab6-112">Le document généré décrivant les points de terminaison s’affiche comme illustré dans la [spécification Swagger (swagger.json)](https://docs.microsoft.com/aspnet/core/tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).</span><span class="sxs-lookup"><span data-stu-id="e8ab6-112">The generated document describing the endpoints appears as shown in [Swagger specification (swagger.json)](https://docs.microsoft.com/aspnet/core/tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).</span></span>

<span data-ttu-id="e8ab6-113">L’interface utilisateur Swagger se trouve à l’adresse `http://localhost:<port>/swagger`.</span><span class="sxs-lookup"><span data-stu-id="e8ab6-113">The Swagger UI can be found at `http://localhost:<port>/swagger`.</span></span> <span data-ttu-id="e8ab6-114">Explorez l’API via l’interface utilisateur Swagger et incorporez-la dans d’autres programmes.</span><span class="sxs-lookup"><span data-stu-id="e8ab6-114">Explore the API via Swagger UI and incorporate it in other programs.</span></span>

> [!TIP]
> <span data-ttu-id="e8ab6-115">Pour utiliser l’IU Swagger à la racine de l’application (`http://localhost:<port>/`), définissez la propriété `RoutePrefix` sur une chaîne vide :</span><span class="sxs-lookup"><span data-stu-id="e8ab6-115">To serve the Swagger UI at the app's root (`http://localhost:<port>/`), set the `RoutePrefix` property to an empty string:</span></span>
>
> ```csharp
>app.UseSwaggerUI(c =>
>{
>    c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1");
>    c.RoutePrefix = string.Empty;
>});
>```

<span data-ttu-id="e8ab6-116">Si vous utilisez des répertoires avec IIS ou un proxy inverse, définissez le point de terminaison Swagger sur un chemin relatif avec le préfixe `./`.</span><span class="sxs-lookup"><span data-stu-id="e8ab6-116">If using directories with IIS or a reverse proxy, set the Swagger endpoint to a relative path using the `./` prefix.</span></span> <span data-ttu-id="e8ab6-117">Par exemple : `./swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="e8ab6-117">For example, `./swagger/v1/swagger.json`.</span></span> <span data-ttu-id="e8ab6-118">L’utilisation de `/swagger/v1/swagger.json` indique à l’application de rechercher le fichier JSON à la racine réelle de l’URL (plus le préfixe de la route s’il est utilisé).</span><span class="sxs-lookup"><span data-stu-id="e8ab6-118">Using `/swagger/v1/swagger.json` instructs the app to look for the JSON file at the true root of the URL (plus the route prefix, if used).</span></span> <span data-ttu-id="e8ab6-119">Par exemple, utilisez `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` au lieu de `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="e8ab6-119">For example, use `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` instead of `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span></span>

## <a name="customize-and-extend"></a><span data-ttu-id="e8ab6-120">Personnaliser et étendre</span><span class="sxs-lookup"><span data-stu-id="e8ab6-120">Customize and extend</span></span>

<span data-ttu-id="e8ab6-121">Swagger fournit des options pour documenter le modèle objet et personnaliser l’interface utilisateur en fonction de votre thème.</span><span class="sxs-lookup"><span data-stu-id="e8ab6-121">Swagger provides options for documenting the object model and customizing the UI to match your theme.</span></span>

<span data-ttu-id="e8ab6-122">Dans la classe `Startup`, ajoutez les espaces de noms suivants :</span><span class="sxs-lookup"><span data-stu-id="e8ab6-122">In the `Startup` class, add the following namespaces:</span></span>
```csharp
using System;
using System.Reflection;
using System.IO;
```

### <a name="api-info-and-description"></a><span data-ttu-id="e8ab6-123">Informations sur l’API et description</span><span class="sxs-lookup"><span data-stu-id="e8ab6-123">API info and description</span></span>

<span data-ttu-id="e8ab6-124">L’action de configuration transmise à la méthode `AddSwaggerGen` ajoute des informations comme l’auteur, la license et la description :</span><span class="sxs-lookup"><span data-stu-id="e8ab6-124">The configuration action passed to the `AddSwaggerGen` method adds information such as the author, license, and description:</span></span>

```csharp
// Register the Swagger generator, defining 1 or more Swagger documents
services.AddSwaggerGen(c =>
{
    c.SwaggerDoc("v1", new OpenApiInfo
    {
        Version = "v1",
        Title = "ToDo API",
        Description = "A simple example ASP.NET Core Web API",
        TermsOfService = new Uri("https://example.com/terms"),
        Contact = new OpenApiContact
        {
            Name = "Shayne Boyer",
            Email = string.Empty,
            Url = new Uri("https://twitter.com/spboyer"),
        },
        License = new OpenApiLicense
        {
            Name = "Use under LICX",
            Url = new Uri("https://example.com/license"),
        }
    });
});
```

<span data-ttu-id="e8ab6-125">L’IU Swagger affiche les informations de la version :</span><span class="sxs-lookup"><span data-stu-id="e8ab6-125">The Swagger UI displays the version's information:</span></span>

![Interface utilisateur de Swagger avec les informations de version : description, auteur et lien pour en savoir plus](sample_images/custom-info.png)

### <a name="xml-comments"></a><span data-ttu-id="e8ab6-127">Commentaires XML</span><span class="sxs-lookup"><span data-stu-id="e8ab6-127">XML comments</span></span>

<span data-ttu-id="e8ab6-128">Vous pouvez activer les commentaires XML en adoptant l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="e8ab6-128">XML comments can be enabled with the following approaches:</span></span>

#### <a name="visual-studio"></a>[<span data-ttu-id="e8ab6-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e8ab6-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e8ab6-130">Cliquez avec le bouton droit sur le projet dans **l’Explorateur de solutions**, puis sélectionnez **Modifier <nom_projet>.csproj**.</span><span class="sxs-lookup"><span data-stu-id="e8ab6-130">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="e8ab6-131">Ajoutez manuellement les lignes en surbrillance au fichier *.csproj* :</span><span class="sxs-lookup"><span data-stu-id="e8ab6-131">Manually add the highlighted lines to the *.csproj* file:</span></span>

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

#### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e8ab6-132">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="e8ab6-132">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e8ab6-133">Dans le *Panneau Solutions*, appuyez sur **contrôle** et cliquez sur le nom du projet.</span><span class="sxs-lookup"><span data-stu-id="e8ab6-133">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="e8ab6-134">Accédez à **Outils**  >  **modifier le fichier**.</span><span class="sxs-lookup"><span data-stu-id="e8ab6-134">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="e8ab6-135">Ajoutez manuellement les lignes en surbrillance au fichier *.csproj* :</span><span class="sxs-lookup"><span data-stu-id="e8ab6-135">Manually add the highlighted lines to the *.csproj* file:</span></span>

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

#### <a name="visual-studio-code"></a>[<span data-ttu-id="e8ab6-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e8ab6-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e8ab6-137">Ajoutez manuellement les lignes en surbrillance au fichier *.csproj* :</span><span class="sxs-lookup"><span data-stu-id="e8ab6-137">Manually add the highlighted lines to the *.csproj* file:</span></span>

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

---

<span data-ttu-id="e8ab6-138">L’activation de commentaires XML fournit des informations de débogage sur les membres et types publics non documentés.</span><span class="sxs-lookup"><span data-stu-id="e8ab6-138">Enabling XML comments provides debug information for undocumented public types and members.</span></span> <span data-ttu-id="e8ab6-139">Les membres et types non documentés sont indiqués par le message d’avertissement.</span><span class="sxs-lookup"><span data-stu-id="e8ab6-139">Undocumented types and members are indicated by the warning message.</span></span> <span data-ttu-id="e8ab6-140">Par exemple, le message suivant indique une violation du code d’avertissement 1591 :</span><span class="sxs-lookup"><span data-stu-id="e8ab6-140">For example, the following message indicates a violation of warning code 1591:</span></span>

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

<span data-ttu-id="e8ab6-141">Pour supprimer des avertissements à l’échelle d’un projet, définissez une liste séparée par des points-virgules de codes d’avertissement à ignorer dans le fichier de projet.</span><span class="sxs-lookup"><span data-stu-id="e8ab6-141">To suppress warnings project-wide, define a semicolon-delimited list of warning codes to ignore in the project file.</span></span> <span data-ttu-id="e8ab6-142">L’ajout des codes d’avertissement à `$(NoWarn);` applique aussi les [valeurs par défaut C#](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16).</span><span class="sxs-lookup"><span data-stu-id="e8ab6-142">Appending the warning codes to `$(NoWarn);` applies the [C# default values](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) too.</span></span>

```xml
<NoWarn>$(NoWarn);1591</NoWarn>
```

<span data-ttu-id="e8ab6-143">Pour supprimer des avertissements uniquement pour des membres spécifiques, placez le code dans les directives de préprocesseur [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning).</span><span class="sxs-lookup"><span data-stu-id="e8ab6-143">To suppress warnings only for specific members, enclose the code in [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) preprocessor directives.</span></span> <span data-ttu-id="e8ab6-144">Cette approche est utile pour le code qui ne doit pas être exposé via les docs de l’API. Dans l’exemple suivant, le code d’avertissement CS1591 est ignoré pour la `Program` classe entière.</span><span class="sxs-lookup"><span data-stu-id="e8ab6-144">This approach is useful for code that shouldn't be exposed via the API docs. In the following example, warning code CS1591 is ignored for the entire `Program` class.</span></span> <span data-ttu-id="e8ab6-145">La mise en œuvre de code d’avertissement est restaurée à la fin de la définition de classe.</span><span class="sxs-lookup"><span data-stu-id="e8ab6-145">Enforcement of the warning code is restored at the close of the class definition.</span></span> <span data-ttu-id="e8ab6-146">Spécifier plusieurs codes d’avertissement avec une liste délimitée par des virgules.</span><span class="sxs-lookup"><span data-stu-id="e8ab6-146">Specify multiple warning codes with a comma-delimited list.</span></span>

```csharp
namespace TodoApi
{
#pragma warning disable CS1591
    public class Program
    {
        public static void Main(string[] args) =>
            BuildWebHost(args).Run();

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>()
                .Build();
    }
#pragma warning restore CS1591
}
```

<span data-ttu-id="e8ab6-147">Configurez Swagger de façon à utiliser le fichier XML généré avec les instructions précédentes.</span><span class="sxs-lookup"><span data-stu-id="e8ab6-147">Configure Swagger to use the XML file that's generated with the preceding instructions.</span></span> <span data-ttu-id="e8ab6-148">Pour les systèmes d’exploitation Linux ou non-Windows, les chemins et les noms de fichiers peuvent respecter la casse.</span><span class="sxs-lookup"><span data-stu-id="e8ab6-148">For Linux or non-Windows operating systems, file names and paths can be case-sensitive.</span></span> <span data-ttu-id="e8ab6-149">Par exemple, un fichier *ToDoApi.XML* est valide sur Windows, mais pas sur CentOS.</span><span class="sxs-lookup"><span data-stu-id="e8ab6-149">For example, a *TodoApi.XML* file is valid on Windows but not CentOS.</span></span>

```csharp
/// NOTE LAST 3 LINES IN THIS SNIPPET
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<TodoContext>(opt =>
        opt.UseInMemoryDatabase("TodoList"));
    services.AddControllers();

    // Register the Swagger generator, defining 1 or more Swagger documents
    services.AddSwaggerGen(c =>
    {
        c.SwaggerDoc("v1", new OpenApiInfo
        {
            Version = "v1",
            Title = "ToDo API",
            Description = "A simple example ASP.NET Core Web API",
            TermsOfService = new Uri("https://example.com/terms"),
            Contact = new OpenApiContact
            {
                Name = "Shayne Boyer",
                Email = string.Empty,
                Url = new Uri("https://twitter.com/spboyer"),
            },
            License = new OpenApiLicense
            {
                Name = "Use under LICX",
                Url = new Uri("https://example.com/license"),
            }
        });

        // Set the comments path for the Swagger JSON and UI.
        var xmlFile = $"{Assembly.GetExecutingAssembly().GetName().Name}.xml";
        var xmlPath = Path.Combine(AppContext.BaseDirectory, xmlFile);
        c.IncludeXmlComments(xmlPath);
    });
}
```

<span data-ttu-id="e8ab6-150">Dans le code précédent, la [réflexion](/dotnet/csharp/programming-guide/concepts/reflection) est utilisée pour générer un nom de fichier XML correspondant à celui du projet d’API Web.</span><span class="sxs-lookup"><span data-stu-id="e8ab6-150">In the preceding code, [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) is used to build an XML file name matching that of the web API project.</span></span> <span data-ttu-id="e8ab6-151">La propriété [AppContext.BaseDirectory](/dotnet/api/system.appcontext.basedirectory) est utilisée pour construire le chemin du fichier XML.</span><span class="sxs-lookup"><span data-stu-id="e8ab6-151">The [AppContext.BaseDirectory](/dotnet/api/system.appcontext.basedirectory) property is used to construct a path to the XML file.</span></span> <span data-ttu-id="e8ab6-152">Certaines fonctionnalités de Swagger (par exemple, les schémas de paramètres d’entrée ou les méthodes HTTP et les codes de réponse issus des attributs respectifs) fonctionnent sans fichier de documentation XML.</span><span class="sxs-lookup"><span data-stu-id="e8ab6-152">Some Swagger features (for example, schemata of input parameters or HTTP methods and response codes from the respective attributes) work without the use of an XML documentation file.</span></span> <span data-ttu-id="e8ab6-153">Pour la plupart des fonctionnalités cependant, à savoir les résumés de méthode et les descriptions des paramètres et des codes de réponse, l’utilisation d’un fichier XML est obligatoire.</span><span class="sxs-lookup"><span data-stu-id="e8ab6-153">For most features, namely method summaries and the descriptions of parameters and response codes, the use of an XML file is mandatory.</span></span>

<span data-ttu-id="e8ab6-154">Quand vous ajoutez des commentaires précédés de trois barres obliques à une action, Swagger UI ajoute la description à l’en-tête de la section.</span><span class="sxs-lookup"><span data-stu-id="e8ab6-154">Adding triple-slash comments to an action enhances the Swagger UI by adding the description to the section header.</span></span> <span data-ttu-id="e8ab6-155">Ajoutez un [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) élément au-dessus de l' `Delete` action :</span><span class="sxs-lookup"><span data-stu-id="e8ab6-155">Add a [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) element above the `Delete` action:</span></span>

```csharp
/// <summary>
/// Deletes a specific TodoItem.
/// </summary>
/// <param name="id"></param>        
[HttpDelete("{id}")]
public IActionResult Delete(long id)
{
    var todo = _context.TodoItems.Find(id);

    if (todo == null)
    {
        return NotFound();
    }

    _context.TodoItems.Remove(todo);
    _context.SaveChanges();

    return NoContent();
}
```
<span data-ttu-id="e8ab6-156">L’IU Swagger affiche le texte interne de l’élément `<summary>` du code précédent :</span><span class="sxs-lookup"><span data-stu-id="e8ab6-156">The Swagger UI displays the inner text of the preceding code's `<summary>` element:</span></span>

![Interface utilisateur de Swagger affichant le commentaire XML « Deletes a specific TodoItem. »](sample_images/triple-slash-comments.png)

<span data-ttu-id="e8ab6-159">L’IU est définie par le schéma JSON généré :</span><span class="sxs-lookup"><span data-stu-id="e8ab6-159">The UI is driven by the generated JSON schema:</span></span>

```json
"delete": {
    "tags": [
        "Todo"
    ],
    "summary": "Deletes a specific TodoItem.",
    "operationId": "ApiTodoByIdDelete",
    "consumes": [],
    "produces": [],
    "parameters": [
        {
            "name": "id",
            "in": "path",
            "description": "",
            "required": true,
            "type": "integer",
            "format": "int64"
        }
    ],
    "responses": {
        "200": {
            "description": "Success"
        }
    }
}
```
<span data-ttu-id="e8ab6-160">Ajoutez un [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) élément à la `Create` documentation de la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="e8ab6-160">Add a [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) element to the `Create` action method documentation.</span></span> <span data-ttu-id="e8ab6-161">Il complète les informations spécifiées dans l’élément `<summary>` et fournit une interface utilisateur Swagger plus robuste.</span><span class="sxs-lookup"><span data-stu-id="e8ab6-161">It supplements information specified in the `<summary>` element and provides a more robust Swagger UI.</span></span> <span data-ttu-id="e8ab6-162">Le contenu de l’élément `<remarks>` peut être du texte, du code JSON ou du code XML.</span><span class="sxs-lookup"><span data-stu-id="e8ab6-162">The `<remarks>` element content can consist of text, JSON, or XML.</span></span>

```csharp
/// <summary>
/// Creates a TodoItem.
/// </summary>
/// <remarks>
/// Sample request:
///
///     POST /Todo
///     {
///        "id": 1,
///        "name": "Item1",
///        "isComplete": true
///     }
///
/// </remarks>
/// <param name="item"></param>
/// <returns>A newly created TodoItem</returns>
/// <response code="201">Returns the newly created item</response>
/// <response code="400">If the item is null</response>            
[HttpPost]
[ProducesResponseType(StatusCodes.Status201Created)]
[ProducesResponseType(StatusCodes.Status400BadRequest)]
public ActionResult<TodoItem> Create(TodoItem item)
{
    _context.TodoItems.Add(item);
    _context.SaveChanges();

    return CreatedAtRoute("GetTodo", new { id = item.Id }, item);
}
```
<span data-ttu-id="e8ab6-163">Notez les améliorations de l’IU avec ces commentaires supplémentaires :</span><span class="sxs-lookup"><span data-stu-id="e8ab6-163">Notice the UI enhancements with these additional comments:</span></span>

![Interface utilisateur de Swagger avec des commentaires supplémentaires affichés](sample_images/xml-comments-extended.png)

### <a name="data-annotations"></a><span data-ttu-id="e8ab6-165">Annotations de données</span><span class="sxs-lookup"><span data-stu-id="e8ab6-165">Data annotations</span></span>

<span data-ttu-id="e8ab6-166">Marquez le modèle avec des attributs, qui se trouvent dans l’espace de noms [System. ComponentModel. DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) , pour aider à piloter les composants de l’interface utilisateur Swagger.</span><span class="sxs-lookup"><span data-stu-id="e8ab6-166">Mark the model with attributes, found in the [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) namespace, to help drive the Swagger UI components.</span></span>

<span data-ttu-id="e8ab6-167">Ajoutez l’attribut `[Required]` à la propriété `Name` de la classe `TodoItem` :</span><span class="sxs-lookup"><span data-stu-id="e8ab6-167">Add the `[Required]` attribute to the `Name` property of the `TodoItem` class:</span></span>

```csharp
using System.ComponentModel;
using System.ComponentModel.DataAnnotations;

namespace TodoApi.Models
{
    public class TodoItem
    {
        public long Id { get; set; }

        [Required]
        public string Name { get; set; }

        [DefaultValue(false)]
        public bool IsComplete { get; set; }
    }
}
```

<span data-ttu-id="e8ab6-168">La présence de cet attribut change le comportement de l’interface utilisateur et modifie le schéma JSON sous-jacent :</span><span class="sxs-lookup"><span data-stu-id="e8ab6-168">The presence of this attribute changes the UI behavior and alters the underlying JSON schema:</span></span>

```json
"definitions": {
    "TodoItem": {
        "required": [
            "name"
        ],
        "type": "object",
        "properties": {
            "id": {
                "format": "int64",
                "type": "integer"
            },
            "name": {
                "type": "string"
            },
            "isComplete": {
                "default": false,
                "type": "boolean"
            }
        }
    }
},
```

<span data-ttu-id="e8ab6-169">Ajoutez l’attribut `[Produces("application/json")]` au contrôleur d’API.</span><span class="sxs-lookup"><span data-stu-id="e8ab6-169">Add the `[Produces("application/json")]` attribute to the API controller.</span></span> <span data-ttu-id="e8ab6-170">Son objectif est de déclarer que les actions du contrôleur prennent en charge une réponse dont le type de contenu est *application/json* :</span><span class="sxs-lookup"><span data-stu-id="e8ab6-170">Its purpose is to declare that the controller's actions support a response content type of *application/json*:</span></span>

```csharp
[Produces("application/json")]
[Route("api/[controller]")]
[ApiController]
public class TodoController : ControllerBase
{
    private readonly TodoContext _context;
```
<span data-ttu-id="e8ab6-171">La liste déroulante **type de contenu** de la réponse sélectionne ce type de contenu comme valeur par défaut pour les actions obtenir du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="e8ab6-171">The **Response Content Type** drop-down selects this content type as the default for the controller's GET actions:</span></span>

![Interface utilisateur de Swagger avec le type de contenu de réponse par défaut](sample_images/json-response-content-type.png)

<span data-ttu-id="e8ab6-173">À mesure que l’utilisation des annotations de données dans l’API web augmente, l’interface utilisateur et les pages d’aide de l’API deviennent de plus en plus descriptives et utiles.</span><span class="sxs-lookup"><span data-stu-id="e8ab6-173">As the usage of data annotations in the web API increases, the UI and API help pages become more descriptive and useful.</span></span>

### <a name="describe-response-types"></a><span data-ttu-id="e8ab6-174">Décrire des types de réponse</span><span class="sxs-lookup"><span data-stu-id="e8ab6-174">Describe response types</span></span>

<span data-ttu-id="e8ab6-175">Les développeurs consommant une API web s’intéressent surtout à ce qui est retourné&mdash;, en particulier les types de réponse et les codes d’erreur (s’ils ne sont pas standards).</span><span class="sxs-lookup"><span data-stu-id="e8ab6-175">Developers consuming a web API are most concerned with what's returned&mdash;specifically response types and error codes (if not standard).</span></span> <span data-ttu-id="e8ab6-176">Les types de réponse et les codes d’erreur sont décrits dans les commentaires XML et les annotations de données.</span><span class="sxs-lookup"><span data-stu-id="e8ab6-176">The response types and error codes are denoted in the XML comments and data annotations.</span></span>

<span data-ttu-id="e8ab6-177">L’action `Create` retourne un code d’état HTTP 201 en cas de réussite.</span><span class="sxs-lookup"><span data-stu-id="e8ab6-177">The `Create` action returns an HTTP 201 status code on success.</span></span> <span data-ttu-id="e8ab6-178">Un code d’état HTTP 400 est retourné quand le corps de la demande postée est null.</span><span class="sxs-lookup"><span data-stu-id="e8ab6-178">An HTTP 400 status code is returned when the posted request body is null.</span></span> <span data-ttu-id="e8ab6-179">Sans documentation appropriée dans l’interface utilisateur de Swagger, le consommateur n’a pas connaissance de ces résultats attendus.</span><span class="sxs-lookup"><span data-stu-id="e8ab6-179">Without proper documentation in the Swagger UI, the consumer lacks knowledge of these expected outcomes.</span></span> <span data-ttu-id="e8ab6-180">Pour résoudre ce problème, ajoutez les lignes en surbrillance de l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="e8ab6-180">Fix that problem by adding the highlighted lines in the following example:</span></span>

```csharp
/// <returns>A newly created TodoItem</returns>
/// <response code="201">Returns the newly created item</response>
/// <response code="400">If the item is null</response>            
[HttpPost]
[ProducesResponseType(StatusCodes.Status201Created)]
[ProducesResponseType(StatusCodes.Status400BadRequest)]
public ActionResult<TodoItem> Create(TodoItem item)
```

<span data-ttu-id="e8ab6-181">L’interface utilisateur de Swagger documente maintenant clairement les codes de réponse HTTP attendus :</span><span class="sxs-lookup"><span data-stu-id="e8ab6-181">The Swagger UI now clearly documents the expected HTTP response codes:</span></span>

![Interface utilisateur de Swagger affichant la description de la classe de réponse POST « Returns the newly created Todo item » et « 400 - If the item is null » pour le code d’état et la raison sous Response Messages](sample_images/data-annotations-response-types.png)

<span data-ttu-id="e8ab6-183">Dans ASP.NET Core 2.2 ou une version ultérieure, les conventions peuvent être utilisées comme alternatives à la décoration explicites des actions individuelles avec `[ProducesResponseType]`.</span><span class="sxs-lookup"><span data-stu-id="e8ab6-183">In ASP.NET Core 2.2 or later, conventions can be used as an alternative to explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="e8ab6-184">Pour plus d'informations, consultez [Utiliser les conventions d’API web](https://docs.microsoft.com/aspnet/core/web-api/advanced/conventions).</span><span class="sxs-lookup"><span data-stu-id="e8ab6-184">For more information, see [Use web API conventions](https://docs.microsoft.com/aspnet/core/web-api/advanced/conventions).</span></span>

<span data-ttu-id="e8ab6-185">Pour plus d’informations sur la personnalisation de l’interface utilisateur, consultez [personnaliser l’interface utilisateur](/aspnet/core/tutorials/getting-started-with-swashbuckle?#customize-and-extend) .</span><span class="sxs-lookup"><span data-stu-id="e8ab6-185">For information on customizing the UI see: [Customize the UI](/aspnet/core/tutorials/getting-started-with-swashbuckle?#customize-and-extend)</span></span>
