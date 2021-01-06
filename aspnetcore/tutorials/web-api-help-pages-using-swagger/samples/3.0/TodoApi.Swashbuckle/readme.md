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
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a>Bien démarrer avec Swashbuckle et ASP.NET Core

Les différentes méthodes qui permettent d’utiliser une API web ne sont pas toujours simples à comprendre pour les développeurs. [Swagger](https://swagger.io/), également appelé [OpenAPI](https://www.openapis.org/), résout le problème de la génération de pages d’aide et de documentation qu’utilisent les API web. Ses avantages sont, entre autres, la documentation interactive, la génération de SDK client et la découvertibilité des API.

Dans cet exemple, l’implémentation .NET [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) est indiquée.

## <a name="add-and-configure-swagger-middleware"></a>Ajouter et configurer l’intergiciel (middleware) Swagger

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

Dans la méthode `Startup.Configure`, activez l’intergiciel pour traiter le document JSON généré et l’IU Swagger :

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

L’appel de méthode `UseSwaggerUI` précédent active le [middleware (intergiciel) des fichiers statiques](https://docs.microsoft.com/aspnet/core/fundamentals/static-files). Si vous ciblez .NET Framework ou .NET Core 1. x, ajoutez le package NuGet [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) au projet.

Lancez l’application et accédez à `http://localhost:<port>/swagger/v1/swagger.json`. Le document généré décrivant les points de terminaison s’affiche comme illustré dans la [spécification Swagger (swagger.json)](https://docs.microsoft.com/aspnet/core/tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).

L’interface utilisateur Swagger se trouve à l’adresse `http://localhost:<port>/swagger`. Explorez l’API via l’interface utilisateur Swagger et incorporez-la dans d’autres programmes.

> [!TIP]
> Pour utiliser l’IU Swagger à la racine de l’application (`http://localhost:<port>/`), définissez la propriété `RoutePrefix` sur une chaîne vide :
>
> ```csharp
>app.UseSwaggerUI(c =>
>{
>    c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1");
>    c.RoutePrefix = string.Empty;
>});
>```

Si vous utilisez des répertoires avec IIS ou un proxy inverse, définissez le point de terminaison Swagger sur un chemin relatif avec le préfixe `./`. Par exemple : `./swagger/v1/swagger.json`. L’utilisation de `/swagger/v1/swagger.json` indique à l’application de rechercher le fichier JSON à la racine réelle de l’URL (plus le préfixe de la route s’il est utilisé). Par exemple, utilisez `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` au lieu de `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.

## <a name="customize-and-extend"></a>Personnaliser et étendre

Swagger fournit des options pour documenter le modèle objet et personnaliser l’interface utilisateur en fonction de votre thème.

Dans la classe `Startup`, ajoutez les espaces de noms suivants :
```csharp
using System;
using System.Reflection;
using System.IO;
```

### <a name="api-info-and-description"></a>Informations sur l’API et description

L’action de configuration transmise à la méthode `AddSwaggerGen` ajoute des informations comme l’auteur, la license et la description :

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

L’IU Swagger affiche les informations de la version :

![Interface utilisateur de Swagger avec les informations de version : description, auteur et lien pour en savoir plus](sample_images/custom-info.png)

### <a name="xml-comments"></a>Commentaires XML

Vous pouvez activer les commentaires XML en adoptant l’une des approches suivantes :

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Cliquez avec le bouton droit sur le projet dans **l’Explorateur de solutions**, puis sélectionnez **Modifier <nom_projet>.csproj**.
* Ajoutez manuellement les lignes en surbrillance au fichier *.csproj* :

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

#### <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* Dans le *Panneau Solutions*, appuyez sur **contrôle** et cliquez sur le nom du projet. Accédez à **Outils**  >  **modifier le fichier**.
* Ajoutez manuellement les lignes en surbrillance au fichier *.csproj* :

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Ajoutez manuellement les lignes en surbrillance au fichier *.csproj* :

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

---

L’activation de commentaires XML fournit des informations de débogage sur les membres et types publics non documentés. Les membres et types non documentés sont indiqués par le message d’avertissement. Par exemple, le message suivant indique une violation du code d’avertissement 1591 :

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

Pour supprimer des avertissements à l’échelle d’un projet, définissez une liste séparée par des points-virgules de codes d’avertissement à ignorer dans le fichier de projet. L’ajout des codes d’avertissement à `$(NoWarn);` applique aussi les [valeurs par défaut C#](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16).

```xml
<NoWarn>$(NoWarn);1591</NoWarn>
```

Pour supprimer des avertissements uniquement pour des membres spécifiques, placez le code dans les directives de préprocesseur [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning). Cette approche est utile pour le code qui ne doit pas être exposé via les docs de l’API. Dans l’exemple suivant, le code d’avertissement CS1591 est ignoré pour la `Program` classe entière. La mise en œuvre de code d’avertissement est restaurée à la fin de la définition de classe. Spécifier plusieurs codes d’avertissement avec une liste délimitée par des virgules.

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

Configurez Swagger de façon à utiliser le fichier XML généré avec les instructions précédentes. Pour les systèmes d’exploitation Linux ou non-Windows, les chemins et les noms de fichiers peuvent respecter la casse. Par exemple, un fichier *ToDoApi.XML* est valide sur Windows, mais pas sur CentOS.

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

Dans le code précédent, la [réflexion](/dotnet/csharp/programming-guide/concepts/reflection) est utilisée pour générer un nom de fichier XML correspondant à celui du projet d’API Web. La propriété [AppContext.BaseDirectory](/dotnet/api/system.appcontext.basedirectory) est utilisée pour construire le chemin du fichier XML. Certaines fonctionnalités de Swagger (par exemple, les schémas de paramètres d’entrée ou les méthodes HTTP et les codes de réponse issus des attributs respectifs) fonctionnent sans fichier de documentation XML. Pour la plupart des fonctionnalités cependant, à savoir les résumés de méthode et les descriptions des paramètres et des codes de réponse, l’utilisation d’un fichier XML est obligatoire.

Quand vous ajoutez des commentaires précédés de trois barres obliques à une action, Swagger UI ajoute la description à l’en-tête de la section. Ajoutez un [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) élément au-dessus de l' `Delete` action :

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
L’IU Swagger affiche le texte interne de l’élément `<summary>` du code précédent :

![Interface utilisateur de Swagger affichant le commentaire XML « Deletes a specific TodoItem. » pour la méthode DELETE.](sample_images/triple-slash-comments.png)

L’IU est définie par le schéma JSON généré :

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
Ajoutez un [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) élément à la `Create` documentation de la méthode d’action. Il complète les informations spécifiées dans l’élément `<summary>` et fournit une interface utilisateur Swagger plus robuste. Le contenu de l’élément `<remarks>` peut être du texte, du code JSON ou du code XML.

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
Notez les améliorations de l’IU avec ces commentaires supplémentaires :

![Interface utilisateur de Swagger avec des commentaires supplémentaires affichés](sample_images/xml-comments-extended.png)

### <a name="data-annotations"></a>Annotations de données

Marquez le modèle avec des attributs, qui se trouvent dans l’espace de noms [System. ComponentModel. DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) , pour aider à piloter les composants de l’interface utilisateur Swagger.

Ajoutez l’attribut `[Required]` à la propriété `Name` de la classe `TodoItem` :

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

La présence de cet attribut change le comportement de l’interface utilisateur et modifie le schéma JSON sous-jacent :

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

Ajoutez l’attribut `[Produces("application/json")]` au contrôleur d’API. Son objectif est de déclarer que les actions du contrôleur prennent en charge une réponse dont le type de contenu est *application/json* :

```csharp
[Produces("application/json")]
[Route("api/[controller]")]
[ApiController]
public class TodoController : ControllerBase
{
    private readonly TodoContext _context;
```
La liste déroulante **type de contenu** de la réponse sélectionne ce type de contenu comme valeur par défaut pour les actions obtenir du contrôleur :

![Interface utilisateur de Swagger avec le type de contenu de réponse par défaut](sample_images/json-response-content-type.png)

À mesure que l’utilisation des annotations de données dans l’API web augmente, l’interface utilisateur et les pages d’aide de l’API deviennent de plus en plus descriptives et utiles.

### <a name="describe-response-types"></a>Décrire des types de réponse

Les développeurs consommant une API web s’intéressent surtout à ce qui est retourné&mdash;, en particulier les types de réponse et les codes d’erreur (s’ils ne sont pas standards). Les types de réponse et les codes d’erreur sont décrits dans les commentaires XML et les annotations de données.

L’action `Create` retourne un code d’état HTTP 201 en cas de réussite. Un code d’état HTTP 400 est retourné quand le corps de la demande postée est null. Sans documentation appropriée dans l’interface utilisateur de Swagger, le consommateur n’a pas connaissance de ces résultats attendus. Pour résoudre ce problème, ajoutez les lignes en surbrillance de l’exemple suivant :

```csharp
/// <returns>A newly created TodoItem</returns>
/// <response code="201">Returns the newly created item</response>
/// <response code="400">If the item is null</response>            
[HttpPost]
[ProducesResponseType(StatusCodes.Status201Created)]
[ProducesResponseType(StatusCodes.Status400BadRequest)]
public ActionResult<TodoItem> Create(TodoItem item)
```

L’interface utilisateur de Swagger documente maintenant clairement les codes de réponse HTTP attendus :

![Interface utilisateur de Swagger affichant la description de la classe de réponse POST « Returns the newly created Todo item » et « 400 - If the item is null » pour le code d’état et la raison sous Response Messages](sample_images/data-annotations-response-types.png)

Dans ASP.NET Core 2.2 ou une version ultérieure, les conventions peuvent être utilisées comme alternatives à la décoration explicites des actions individuelles avec `[ProducesResponseType]`. Pour plus d'informations, consultez [Utiliser les conventions d’API web](https://docs.microsoft.com/aspnet/core/web-api/advanced/conventions).

Pour plus d’informations sur la personnalisation de l’interface utilisateur, consultez [personnaliser l’interface utilisateur](/aspnet/core/tutorials/getting-started-with-swashbuckle?#customize-and-extend) .
