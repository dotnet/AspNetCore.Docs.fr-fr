---
title: Chargement différé d’assemblys dans ASP.NET Core Blazor WebAssembly
author: guardrex
description: Découvrez comment charger en différé des assemblys dans des Blazor WebAssembly applications ASP.net core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/09/2020
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
uid: blazor/webassembly-lazy-load-assemblies
ms.openlocfilehash: 6e7fa6e231e97793fbf7e1ac1d208bf3013c6fce
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97506568"
---
# <a name="lazy-load-assemblies-in-aspnet-core-no-locblazor-webassembly"></a>Chargement différé d’assemblys dans ASP.NET Core Blazor WebAssembly

Par [safia Abdalla](https://safia.rocks) et [Luke Latham](https://github.com/guardrex)

Blazor WebAssembly les performances de démarrage de l’application peuvent être améliorées en différant le chargement de certains assemblys d’application jusqu’à ce qu’ils soient nécessaires, ce qui s’appelle le *chargement différé*. Par exemple, les assemblys qui sont utilisés uniquement pour le rendu d’un seul composant peuvent être configurés pour être chargés uniquement si l’utilisateur accède à ce composant. Après le chargement, les assemblys sont mis en cache côté client et sont disponibles pour toutes les navigations ultérieures.

Blazorla fonctionnalité de chargement différé de vous permet de marquer des assemblys d’application pour le chargement différé, qui charge les assemblys lors de l’exécution lorsque l’utilisateur accède à un itinéraire particulier. La fonctionnalité est constituée de modifications apportées au fichier projet et des modifications apportées au routeur de l’application.

> [!NOTE]
> Le chargement différé d’un assembly n’avantage pas Blazor Server les applications, car les assemblys ne sont pas téléchargés vers le client dans une Blazor Server application.

## <a name="project-file"></a>Fichier projet

Marquez les assemblys pour le chargement différé dans le fichier projet de l’application ( `.csproj` ) à l’aide de l' `BlazorWebAssemblyLazyLoad` élément. Utilisez le nom de l’assembly avec l' `.dll` extension. L' Blazor infrastructure empêche le chargement des assemblys spécifiés par ce groupe d’éléments au lancement de l’application. L’exemple suivant marque un grand assembly personnalisé ( `GrantImaharaRobotControls.dll` ) pour le chargement différé. Si un assembly marqué pour le chargement différé a des dépendances, il doit également être marqué pour le chargement différé dans le fichier projet.

```xml
<ItemGroup>
  <BlazorWebAssemblyLazyLoad Include="GrantImaharaRobotControls.dll" />
</ItemGroup>
```

## <a name="router-component"></a>Composant `Router`

Blazor`Router`le composant de désigne les assemblys qui Blazor recherchent les composants routables. Le `Router` composant est également responsable du rendu du composant pour l’itinéraire à partir duquel l’utilisateur navigue. Le `Router` composant prend en charge une `OnNavigateAsync` fonctionnalité qui peut être utilisée conjointement avec le chargement différé.

Dans le composant de l’application `Router` ( `App.razor` ) :

* Ajoutez un `OnNavigateAsync` rappel. Le `OnNavigateAsync` gestionnaire est appelé lorsque l’utilisateur :
  * Visite un itinéraire pour la première fois en y accédant directement à partir de son navigateur.
  * Navigue vers un nouvel itinéraire à l’aide d’un lien ou d’un <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> appel.
* Si les assemblys à chargement différé contiennent des composants routables, ajoutez une [liste](xref:System.Collections.Generic.List%601) \<<xref:System.Reflection.Assembly>> (par exemple, nommée `lazyLoadedAssemblies` ) au composant. Les assemblys sont passés à la <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> collection au cas où les assemblys contiennent des composants routables. L’infrastructure recherche les itinéraires dans les assemblys et met à jour la collection d’itinéraires si de nouveaux itinéraires sont trouvés.

```razor
@using System.Reflection

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
    }
}
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

Si le `OnNavigateAsync` rappel lève une exception non gérée, l' [ Blazor interface utilisateur d’erreur](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development) est appelée.

### <a name="assembly-load-logic-in-onnavigateasync"></a>Logique de chargement d’assembly dans `OnNavigateAsync`

`OnNavigateAsync` a un `NavigationContext` paramètre qui fournit des informations sur l’événement de navigation asynchrone actuel, y compris le chemin d’accès cible ( `Path` ) et le jeton d’annulation ( `CancellationToken` ) :

* La `Path` propriété est le chemin d’accès de destination de l’utilisateur par rapport au chemin d’accès de base de l’application, par exemple `/robot` .
* Le `CancellationToken` peut être utilisé pour observer l’annulation de la tâche asynchrone. `OnNavigateAsync` annule automatiquement la tâche de navigation en cours d’exécution lorsque l’utilisateur accède à une autre page.

Dans `OnNavigateAsync` , implémentez une logique pour déterminer les assemblys à charger. Options disponibles :

* Vérifications conditionnelles à l’intérieur de la `OnNavigateAsync` méthode.
* Table de recherche qui mappe des itinéraires à des noms d’assemblys, soit injectés dans le composant, soit implémentée dans le [`@code`](xref:mvc/views/razor#code) bloc.

`LazyAssemblyLoader` est un service Singleton fourni par l’infrastructure pour le chargement d’assemblys. Injecter `LazyAssemblyLoader` dans le `Router` composant :

```razor
...
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

...
```

Le `LazyAssemblyLoader` fournit la `LoadAssembliesAsync` méthode qui :

* Utilise l’interopérabilité JS pour extraire des assemblys via un appel réseau.
* Charge les assemblys dans le runtime s’exécutant sur webassembly dans le navigateur.

L’implémentation du chargement différé de l’infrastructure prend en charge le chargement différé avec prérendu dans une solution hébergée Blazor . Lors du prérendu, tous les assemblys, y compris ceux marqués pour le chargement différé, sont supposés être chargés. Inscrivez-vous manuellement `LazyAssemblyLoader` dans la méthode du projet *serveur* `Startup.ConfigureServices` ( `Startup.cs` ) :

```csharp
services.AddScoped<LazyAssemblyLoader>();
```

### <a name="user-interaction-with-navigating-content"></a>Interaction de l’utilisateur avec le `<Navigating>` contenu

Lors du chargement des assemblys, ce qui peut prendre plusieurs secondes, le `Router` composant peut indiquer à l’utilisateur qu’une transition de page se produit :

* Ajoutez une [`@using`](xref:mvc/views/razor#using) directive pour l' <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName> espace de noms.
* Ajoutez une `<Navigating>` balise au composant avec le balisage à afficher pendant les événements de transition de page.

```razor
...
@using Microsoft.AspNetCore.Components.Routing
...

<Router ...>
    <Navigating>
        <div style="...">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
</Router>

...
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

### <a name="handle-cancellations-in-onnavigateasync"></a>Gérer les annulations dans `OnNavigateAsync`

L' `NavigationContext` objet passé au `OnNavigateAsync` rappel contient un `CancellationToken` qui est défini lorsqu’un nouvel événement de navigation se produit. Le `OnNavigateAsync` rappel doit lever une exception quand ce jeton d’annulation est défini pour éviter de continuer à exécuter le `OnNavigateAsync` rappel sur une navigation obsolète.

Si un utilisateur accède à l’itinéraire A, puis immédiatement à l’itinéraire B, l’application ne doit pas continuer à exécuter le `OnNavigateAsync` rappel pour l’itinéraire a :

```razor
@inject HttpClient Http
@inject ProductCatalog Products

<Router AppAssembly="@typeof(Program).Assembly" 
    OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private async Task OnNavigateAsync(NavigationContext context)
    {
        if (context.Path == "/about") 
        {
            var stats = new Stats = { Page = "/about" };
            await Http.PostAsJsonAsync("api/visited", stats, context.CancellationToken);
        }
        else if (context.Path == "/store")
        {
            var productIds = [345, 789, 135, 689];

            foreach (var productId in productIds) 
            {
                context.CancellationToken.ThrowIfCancellationRequested();
                Products.Prefetch(productId);
            }
        }
    }
}
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

> [!NOTE]
> Ne pas lever la valeur si le jeton d’annulation dans `NavigationContext` est annulé peut entraîner un comportement inattendu, tel que le rendu d’un composant à partir d’une navigation précédente.

### <a name="onnavigateasync-events-and-renamed-assembly-files"></a>`OnNavigateAsync` événements et fichiers d’assembly renommés

Le chargeur de ressource s’appuie sur les noms d’assemblys qui sont définis dans le `blazor.boot.json` fichier. Si les [assemblys sont renommés](xref:blazor/host-and-deploy/webassembly#change-the-filename-extension-of-dll-files), les noms d’assembly utilisés dans `OnNavigateAsync` les méthodes et les noms d’assembly dans le `blazor.boot.json` fichier ne sont pas synchronisés.

Pour rectifier ce qui suit :

* Vérifiez si l’application s’exécute dans l’environnement de production lorsque vous déterminez les noms d’assembly à utiliser.
* Stockez les noms d’assemblys renommés dans un fichier distinct et lisez-les à partir de ce fichier pour déterminer le nom de l’assembly à utiliser dans les `LazyLoadAssemblyService` `OnNavigateAsync` méthodes et.

### <a name="complete-example"></a>Exemple complet

Le composant complet suivant `Router` montre le chargement de l' `GrantImaharaRobotControls.dll` assembly lorsque l’utilisateur accède à `/robot` . Lors des transitions de page, un message stylisé est affiché à l’utilisateur.

```razor
@using System.Reflection
@using Microsoft.AspNetCore.Components.Routing
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    <Navigating>
        <div style="padding:20px;background-color:blue;color:white">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <LayoutView Layout="@typeof(MainLayout)">
            <p>Sorry, there's nothing at this address.</p>
        </LayoutView>
    </NotFound>
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
        try
        {
            if (args.Path.EndsWith("/robot"))
            {
                var assemblies = await assemblyLoader.LoadAssembliesAsync(
                    new List<string>() { "GrantImaharaRobotControls.dll" });
                lazyLoadedAssemblies.AddRange(assemblies);
            }
        }
        catch (Exception ex)
        {
            ...
        }
    }
}
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

## <a name="troubleshoot"></a>Dépanner

* Si un rendu inattendu se produit (par exemple, un composant d’une navigation précédente est rendu), confirmez que le code lève si le jeton d’annulation est défini.
* Si les assemblys sont toujours chargés au démarrage de l’application, vérifiez que l’assembly est marqué comme étant différé dans le fichier projet.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:blazor/webassembly-performance-best-practices>
