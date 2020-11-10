---
title: Routage de ASP.NET Core Blazor
author: guardrex
description: Découvrez comment acheminer des requêtes dans des applications et sur le composant NavLink.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/02/2020
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
uid: blazor/fundamentals/routing
ms.openlocfilehash: 8f0aa80d092b6678131a2b7152f21ecb8e168257
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430989"
---
# <a name="aspnet-core-no-locblazor-routing"></a>Routage de ASP.NET Core Blazor

Par [Luke Latham](https://github.com/guardrex)

Découvrez comment acheminer les demandes et comment utiliser le <xref:Microsoft.AspNetCore.Components.Routing.NavLink> composant pour créer des liens de navigation dans les Blazor applications.

## <a name="aspnet-core-endpoint-routing-integration"></a>Intégration du routage du point de terminaison ASP.NET Core

Blazor Server est intégré dans [ASP.net core le routage du point de terminaison](xref:fundamentals/routing). Une application ASP.NET Core est configurée pour accepter les connexions entrantes pour les composants interactifs avec <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> dans `Startup.Configure` :

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

La configuration la plus courante consiste à acheminer toutes les demandes vers une Razor page, qui joue le rôle d’hôte pour la partie côté serveur de l' Blazor Server application. Par Convention, la page *hôte* est généralement nommée `_Host.cshtml` . L’itinéraire spécifié dans le fichier hôte est appelé *itinéraire de secours* , car il fonctionne avec une priorité basse dans la correspondance d’itinéraire. L’itinéraire de secours est pris en compte lorsque les autres itinéraires ne correspondent pas. Cela permet à l’application d’utiliser d’autres contrôleurs et pages sans interférer avec l' Blazor Server application.

Pour plus d’informations sur la configuration <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> de pour l’hébergement de serveur d’URL non racine, consultez <xref:blazor/host-and-deploy/index#app-base-path> .

## <a name="route-templates"></a>Modèles de routage

Le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant active le routage vers chaque composant avec un itinéraire spécifié. Le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant apparaît dans le `App.razor` fichier :

```razor
<Router AppAssembly="@typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

Lorsqu’un `.razor` fichier avec une `@page` directive est compilé, la classe générée est fournie en <xref:Microsoft.AspNetCore.Components.RouteAttribute> spécifiant le modèle de routage.

Au moment de l’exécution, le <xref:Microsoft.AspNetCore.Components.RouteView> composant :

* Reçoit du avec les <xref:Microsoft.AspNetCore.Components.RouteData> <xref:Microsoft.AspNetCore.Components.Routing.Router> paramètres souhaités.
* Restitue le composant spécifié avec sa disposition (ou une disposition par défaut facultative) à l’aide des paramètres spécifiés.

Vous pouvez éventuellement spécifier un <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> paramètre avec une classe de disposition à utiliser pour les composants qui ne spécifient pas de disposition. Les modèles par défaut Blazor spécifient le `MainLayout` composant. `MainLayout.razor` se trouve dans le dossier du projet de modèle `Shared` . Pour plus d’informations sur les mises en page, consultez <xref:blazor/layouts> .

Plusieurs modèles de routage peuvent être appliqués à un composant. Le composant suivant répond aux demandes pour `/BlazorRoute` et `/DifferentBlazorRoute` :

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> Pour que les URL soient correctement résolues, l’application doit inclure une `<base>` balise dans son `wwwroot/index.html` fichier ( Blazor WebAssembly ) ou `Pages/_Host.cshtml` fichier ( Blazor Server ) avec le chemin d’accès de base de l’application spécifié dans l' `href` attribut ( `<base href="/">` ). Pour plus d'informations, consultez <xref:blazor/host-and-deploy/index#app-base-path>.

## <a name="provide-custom-content-when-content-isnt-found"></a>Fournir du contenu personnalisé lorsque le contenu est introuvable

Le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant permet à l’application de spécifier du contenu personnalisé si le contenu est introuvable pour l’itinéraire demandé.

Dans le `App.razor` fichier, définissez le contenu personnalisé dans le <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> paramètre de modèle du <xref:Microsoft.AspNetCore.Components.Routing.Router> composant :

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFound>
</Router>
```

Le contenu des `<NotFound>` balises peut inclure des éléments arbitraires, tels que d’autres composants interactifs. Pour appliquer une disposition par défaut au <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> contenu, consultez <xref:blazor/layouts> .

## <a name="route-to-components-from-multiple-assemblies"></a>Acheminer vers des composants à partir de plusieurs assemblys

Utilisez le <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> paramètre pour spécifier des assemblys supplémentaires <xref:Microsoft.AspNetCore.Components.Routing.Router> que le composant doit prendre en compte lors de la recherche de composants routables. Les assemblys spécifiés sont pris en compte en plus de l' `AppAssembly` assembly spécifié. Dans l’exemple suivant, `Component1` est un composant routable défini dans une bibliothèque de classes référencée. L' <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> exemple suivant entraîne la prise en charge du routage pour `Component1` :

```razor
<Router
    AppAssembly="@typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a>Paramètres d’itinéraire

Le routeur utilise des paramètres de routage pour remplir les paramètres de composant correspondants avec le même nom (sans respect de la casse) :

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

Les paramètres facultatifs ne sont pas pris en charge. Deux `@page` directives sont appliquées dans l’exemple précédent. La première permet de naviguer jusqu’au composant sans paramètre. La deuxième `@page` directive prend le `{text}` paramètre d’itinéraire et assigne la valeur à la `Text` propriété.

## <a name="route-constraints"></a>Contraintes d’itinéraire

Une contrainte d’itinéraire applique la correspondance de type sur un segment de routage à un composant.

Dans l’exemple suivant, l’itinéraire vers le `Users` composant correspond uniquement à si :

* Un `Id` segment de routage est présent sur l’URL de la demande.
* Le `Id` segment est un entier ( `int` ).

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

Les contraintes de routage indiquées dans le tableau suivant sont disponibles. Pour plus d’informations sur les contraintes d’itinéraire qui correspondent à la culture dite indifférente, consultez l’avertissement sous le tableau.

| Contrainte | Exemple           | Exemples de correspondances                                                                  | Invariant<br>culture<br>correspondance |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | Non                               |
| `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Oui                              |
| `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Oui                              |
| `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Oui                              |
| `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Oui                              |
| `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Non                               |
| `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Oui                              |
| `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Oui                              |

> [!WARNING]
> Les contraintes de routage qui vérifient que l’URL peut être convertie en type CLR (comme `int` ou <xref:System.DateTime>) utilisent toujours la culture invariant. ces contraintes partent du principe que l’URL n’est pas localisable.

### <a name="routing-with-urls-that-contain-dots"></a>Routage avec des URL qui contiennent des points

Pour les Blazor WebAssembly applications hébergées et Blazor Server , le modèle de routage par défaut côté serveur suppose que si le dernier segment d’une URL de demande contient un point ( `.` ) qu’un fichier est demandé (par exemple, `https://localhost.com:5001/example/some.thing` ). Sans configuration supplémentaire, une application renvoie une réponse *404-introuvable* si cela devait être acheminé vers un composant. Pour utiliser un itinéraire avec un ou plusieurs paramètres contenant un point, l’application doit configurer l’itinéraire avec un modèle personnalisé.

Prenons le `Example` composant suivant qui peut recevoir un paramètre d’itinéraire à partir du dernier segment de l’URL :

```razor
@page "/example"
@page "/example/{param}"

<p>
    Param: @Param
</p>

@code {
    [Parameter]
    public string Param { get; set; }
}
```

Pour autoriser l’application *serveur* d’une solution hébergée Blazor WebAssembly à acheminer la demande avec un point dans le `param` paramètre, ajoutez un modèle d’itinéraire de fichier de secours avec le paramètre facultatif dans `Startup.Configure` ( `Startup.cs` ) :

```csharp
endpoints.MapFallbackToFile("/example/{param?}", "index.html");
```

Pour configurer une Blazor Server application pour acheminer la demande avec un point dans le `param` paramètre, ajoutez un modèle de routage de page de secours avec le paramètre facultatif dans `Startup.Configure` ( `Startup.cs` ) :

```csharp
endpoints.MapFallbackToPage("/example/{param?}", "/_Host");
```

Pour plus d'informations, consultez <xref:fundamentals/routing>.

## <a name="catch-all-route-parameters"></a>Paramètres d’itinéraire de rattrapage

::: moniker range=">= aspnetcore-5.0"

*Cette section s’applique à ASP.NET Core dans .NET 5 Release Candidate 1 (RC1) ou version ultérieure.*

Les paramètres d’itinéraire Catch-All, qui capturent les chemins d’accès dans plusieurs limites de dossiers, sont pris en charge dans les composants. Le paramètre d’itinéraire Catch-All doit être :

* Nommée pour correspondre au nom du segment de routage. Le nom ne respecte pas la casse.
* Type `string`. L’infrastructure ne fournit pas de conversion automatique.
* À la fin de l’URL.

```razor
@page "/page/{*pageRoute}"

@code {
    [Parameter]
    public string PageRoute { get; set; }
}
```

Pour l’URL `/page/this/is/a/test` avec un modèle de routage de `/page/{*pageRoute}` , la valeur de `PageRoute` est définie sur `this/is/a/test` .

Les barres obliques et les segments du chemin capturé sont décodés. Pour un modèle de routage de `/page/{*pageRoute}` , l’URL `/page/this/is/a%2Ftest%2A` génère `this/is/a/test*` .

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Les paramètres d’itinéraire Catch-All sont pris en charge dans ASP.NET Core 5,0 ou version ultérieure.

::: moniker-end

## <a name="navlink-component"></a>Composant NavLink

Utilisez un <xref:Microsoft.AspNetCore.Components.Routing.NavLink> composant à la place des éléments Hyperlink html ( `<a>` ) lors de la création de liens de navigation. Un <xref:Microsoft.AspNetCore.Components.Routing.NavLink> composant se comporte comme un `<a>` élément, sauf qu’il fait basculer une `active` classe CSS selon que son `href` correspond à l’URL actuelle. La `active` classe permet à un utilisateur de comprendre quelle page est la page active parmi les liens de navigation affichés. Éventuellement, assignez un nom de classe CSS à pour <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> appliquer une classe CSS personnalisée au lien rendu lorsque l’itinéraire actuel correspond à `href` .

Le `NavMenu` composant suivant crée une [`Bootstrap`](https://getbootstrap.com/docs/) barre de navigation qui montre comment utiliser des <xref:Microsoft.AspNetCore.Components.Routing.NavLink> composants :

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

Il existe deux <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options que vous pouvez assigner à l' `Match` attribut de l' `<NavLink>` élément :

* <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: Le <xref:Microsoft.AspNetCore.Components.Routing.NavLink> est actif lorsqu’il correspond à la totalité de l’URL actuelle.
* <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> ( *valeur par défaut* ) : le <xref:Microsoft.AspNetCore.Components.Routing.NavLink> est actif lorsqu’il correspond à n’importe quel préfixe de l’URL actuelle.

Dans l’exemple précédent, la page <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` d’hébergement correspond à l’URL de base et reçoit uniquement la `active` classe CSS à l’URL du chemin de base par défaut de l’application (par exemple, `https://localhost:5001/` ). Le deuxième <xref:Microsoft.AspNetCore.Components.Routing.NavLink> reçoit la `active` classe lorsque l’utilisateur visite une URL avec un `MyComponent` préfixe (par exemple, `https://localhost:5001/MyComponent` et `https://localhost:5001/MyComponent/AnotherSegment` ).

<xref:Microsoft.AspNetCore.Components.Routing.NavLink>Les attributs de composant supplémentaires sont passés à la balise d’ancrage rendue. Dans l’exemple suivant, le <xref:Microsoft.AspNetCore.Components.Routing.NavLink> composant comprend l' `target` attribut :

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

Le balisage HTML suivant est rendu :

```html
<a href="my-page" target="_blank">My page</a>
```

> [!WARNING]
> En raison de la façon dont le Blazor contenu enfant est rendu, `NavLink` les composants de rendu à l’intérieur d’une `for` boucle requièrent une variable d’index local si la variable de boucle d’incrémentation est utilisée dans le `NavLink` contenu du composant (enfant) :
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @current
>         </NavLink>
>     </li>
> }
> ```
>
> L’utilisation d’une variable d’index dans ce scénario est requise pour **tout** composant enfant qui utilise une variable de boucle dans son [contenu enfant](xref:blazor/components/index#child-content), et pas seulement pour le `NavLink` composant.
>
> Vous pouvez également utiliser une `foreach` boucle avec <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> :
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @c
>         </NavLink>
>     </li>
> }
> ```

## <a name="uri-and-navigation-state-helpers"></a>Aide des URI et de l’état de navigation

Utilisez <xref:Microsoft.AspNetCore.Components.NavigationManager> pour travailler avec les URI et la navigation dans le code C#. <xref:Microsoft.AspNetCore.Components.NavigationManager> fournit l’événement et les méthodes répertoriées dans le tableau suivant.

| Membre | Description |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | Obtient l’URI absolu actuel. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | Obtient l’URI de base (avec une barre oblique finale) qui peut être ajouté aux chemins d’accès URI relatifs pour produire un URI absolu. En général, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> correspond à l' `href` attribut sur l’élément du document `<base>` dans `wwwroot/index.html` ( Blazor WebAssembly ) ou `Pages/_Host.cshtml` ( Blazor Server ). |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | Navigue vers l’URI spécifié. Si `forceLoad` est `true` :<ul><li>Le routage côté client est contourné.</li><li>Le navigateur est obligé de charger la nouvelle page à partir du serveur, que l’URI soit normalement géré ou non par le routeur côté client.</li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | Événement qui se déclenche lorsque l’emplacement de navigation a changé. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | Convertit un URI relatif en URI absolu. |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | À partir d’un URI de base (par exemple, un URI précédemment retourné par <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> ), convertit un URI absolu en un URI relatif au préfixe URI de base. |

Le composant suivant accède au composant de l’application `Counter` lorsque le bouton est sélectionné :

```razor
@page "/navigate"
@inject NavigationManager NavigationManager

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        NavigationManager.NavigateTo("counter");
    }
}
```

Le composant suivant gère un événement de modification d’emplacement en s’abonnant à <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> . La `HandleLocationChanged` méthode est déconnectée lorsque `Dispose` est appelé par l’infrastructure. Le déraccordement de la méthode permet de garbage collection du composant.

```razor
@implements IDisposable
@inject NavigationManager NavigationManager

...

protected override void OnInitialized()
{
    NavigationManager.LocationChanged += HandleLocationChanged;
}

private void HandleLocationChanged(object sender, LocationChangedEventArgs e)
{
    ...
}

public void Dispose()
{
    NavigationManager.LocationChanged -= HandleLocationChanged;
}
```

<xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> fournit les informations suivantes sur l’événement :

* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: URL du nouvel emplacement.
* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: Si `true` , a Blazor intercepté la navigation à partir du navigateur. Si `false` <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> la valeur est, la navigation a eu lieu.

Pour plus d’informations sur la suppression de composants, consultez <xref:blazor/components/lifecycle#component-disposal-with-idisposable> .

## <a name="query-string-and-parse-parameters"></a>Chaîne de requête et paramètres d’analyse

La chaîne de requête d’une demande peut être obtenue à partir de la <xref:Microsoft.AspNetCore.Components.NavigationManager> <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> propriété de :

```razor
@inject NavigationManager Navigation

...

var query = new Uri(Navigation.Uri).Query;
```

Pour analyser les paramètres d’une chaîne de requête :

* Ajoutez une référence de package pour [Microsoft. AspNetCore. WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).
* Obtenez la valeur après l’analyse de la chaîne de requête avec <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType> .

```razor
@page "/"
@using Microsoft.AspNetCore.WebUtilities
@inject NavigationManager NavigationManager

<h1>Query string parse example</h1>

<p>Value: @queryValue</p>

@code {
    private string queryValue = "Not set";

    protected override void OnInitialized()
    {
        var query = new Uri(NavigationManager.Uri).Query;

        if (QueryHelpers.ParseQuery(query).TryGetValue("{KEY}", out var value))
        {
            queryValue = value;
        }
    }
}
```

L’espace réservé `{KEY}` dans l’exemple précédent est la clé de paramètre de chaîne de requête. Par exemple, la paire clé-valeur `?ship=Tardis` d’URL utilise une clé de `ship` .
