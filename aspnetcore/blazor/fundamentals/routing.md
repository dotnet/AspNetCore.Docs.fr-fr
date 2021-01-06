---
title: Routage de ASP.NET Core Blazor
author: guardrex
description: Découvrez comment gérer le routage des demandes dans les applications et comment utiliser le composant NavLink dans les Blazor applications pour la navigation.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/09/2020
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
ms.openlocfilehash: ec183f4aadc6bafd8e77f9d97291ba3d47bd92f5
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97506927"
---
# <a name="aspnet-core-no-locblazor-routing"></a>Routage de ASP.NET Core Blazor

Par [Luke Latham](https://github.com/guardrex)

Dans cet article, vous allez apprendre à gérer le routage des demandes et à utiliser le <xref:Microsoft.AspNetCore.Components.Routing.NavLink> composant pour créer des liens de navigation dans les Blazor applications.

## <a name="route-templates"></a>Modèles de routage

Le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant permet le routage vers Razor des composants dans une Blazor application. Le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant est utilisé dans le `App` composant des Blazor applications.

`App.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/App1.razor)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/App1.razor)]

::: moniker-end

Quand un Razor composant ( `.razor` ) avec une [ `@page` directive](xref:mvc/views/razor#page) est compilé, la classe de composant générée est fournie en <xref:Microsoft.AspNetCore.Components.RouteAttribute> spécifiant le modèle de routage du composant.

Lorsque l’application démarre, l’assembly spécifié en tant que routeur `AppAssembly` est analysé pour collecter des informations de routage pour les composants de l’application qui ont un <xref:Microsoft.AspNetCore.Components.RouteAttribute> .

Au moment de l’exécution, le <xref:Microsoft.AspNetCore.Components.RouteView> composant :

* Reçoit du avec <xref:Microsoft.AspNetCore.Components.RouteData> <xref:Microsoft.AspNetCore.Components.Routing.Router> tous les paramètres d’itinéraire.
* Restitue le composant spécifié avec sa [disposition](xref:blazor/layouts), y compris les dispositions imbriquées supplémentaires.

Vous pouvez également spécifier un <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> paramètre avec une classe de disposition pour les composants qui ne spécifient pas de disposition avec la [ `@layout` directive](xref:blazor/layouts#specify-a-layout-in-a-component). Les modèles de projet de l’infrastructure Blazor spécifient le `MainLayout` composant ( `Shared/MainLayout.razor` ) comme disposition par défaut de l’application. Pour plus d’informations sur les mises en page, consultez <xref:blazor/layouts> .

Les composants prennent en charge plusieurs modèles de routage à l’aide de plusieurs [ `@page` directives](xref:mvc/views/razor#page). L’exemple de composant suivant se charge sur les demandes pour `/BlazorRoute` et `/DifferentBlazorRoute` .

`Pages/BlazorRoute.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

> [!IMPORTANT]
> Pour que les URL soient correctement résolues, l’application doit inclure une `<base>` balise dans son `wwwroot/index.html` fichier ( Blazor WebAssembly ) ou `Pages/_Host.cshtml` fichier ( Blazor Server ) avec le chemin d’accès de base de l’application spécifié dans l' `href` attribut. Pour plus d’informations, consultez <xref:blazor/host-and-deploy/index#app-base-path>.

## <a name="provide-custom-content-when-content-isnt-found"></a>Fournir du contenu personnalisé lorsque le contenu est introuvable

Le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant permet à l’application de spécifier du contenu personnalisé si le contenu est introuvable pour l’itinéraire demandé.

Dans le `App` composant, définissez le contenu personnalisé dans le <xref:Microsoft.AspNetCore.Components.Routing.Router> modèle du composant <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> .

`App.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/App2.razor?highlight=5-8)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/App2.razor?highlight=5-8)]

::: moniker-end

Les éléments arbitraires sont pris en charge en tant que contenu des `<NotFound>` balises, comme d’autres composants interactifs. Pour appliquer une disposition par défaut au <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> contenu, consultez <xref:blazor/layouts#default-layout> .

## <a name="route-to-components-from-multiple-assemblies"></a>Acheminer vers des composants à partir de plusieurs assemblys

Utilisez le <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> paramètre pour spécifier des assemblys supplémentaires <xref:Microsoft.AspNetCore.Components.Routing.Router> que le composant doit prendre en compte lors de la recherche de composants routables. Les assemblys supplémentaires sont analysés en plus de l’assembly spécifié à `AppAssembly` . Dans l’exemple suivant, `Component1` est un composant routable défini dans une bibliothèque de [classes de composants](xref:blazor/components/class-libraries)référencée. L' <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> exemple suivant entraîne la prise en charge du routage pour `Component1` .

`App.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/App3.razor)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/App3.razor)]

::: moniker-end

## <a name="route-parameters"></a>Paramètres d’itinéraire

Le routeur utilise des paramètres de routage pour remplir les [paramètres de composant](xref:blazor/components/index#component-parameters) correspondants portant le même nom. Les noms de paramètres d’itinéraire ne respectent pas la casse. Dans l’exemple suivant, le `text` paramètre assigne la valeur du segment de route à la propriété du composant `Text` . Quand une demande est effectuée pour `/RouteParameter/amazing` , le `<h1>` contenu de la balise est restitué sous la forme `Blazor is amazing!` .

`Pages/RouteParameter.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

Les paramètres facultatifs sont pris en charge. Dans l’exemple suivant, le `text` paramètre facultatif assigne la valeur du segment de routage à la propriété du composant `Text` . Si le segment n’est pas présent, la valeur de `Text` est définie sur `fantastic` .

`Pages/RouteParameter.razor`:

[!code-razor[](routing/samples_snapshot/5.x/RouteParameter2.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Les paramètres facultatifs ne sont pas pris en charge. Dans l’exemple suivant, deux [ `@page` directives](xref:mvc/views/razor#page) sont appliquées. La première directive autorise la navigation vers le composant sans paramètre. La deuxième directive assigne la `{text}` valeur de paramètre d’itinéraire à la `Text` propriété du composant.

`Pages/RouteParameter.razor`:

[!code-razor[](routing/samples_snapshot/3.x/RouteParameter2.razor?highlight=2)]

::: moniker-end

Utilisez sur [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set) au lieu de [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) pour autoriser la navigation de l’application vers le même composant avec une valeur de paramètre facultative différente. En fonction de l’exemple précédent, utilisez `OnParametersSet` lorsque l’utilisateur doit pouvoir naviguer de `/RouteParameter` vers `/RouteParameter/amazing` ou de `/RouteParameter/amazing` vers `/RouteParameter` :

```csharp
protected override void OnParametersSet()
{
    Text = Text ?? "fantastic";
}
```

## <a name="route-constraints"></a>Contraintes d’itinéraire

Une contrainte d’itinéraire applique la correspondance de type sur un segment de routage à un composant.

Dans l’exemple suivant, l’itinéraire vers le `User` composant correspond uniquement à si :

* Un `Id` segment de routage est présent dans l’URL de la demande.
* Le `Id` segment est un type entier ( `int` ).

`Pages/User.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/User.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/User.razor?highlight=1)]

::: moniker-end

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

## <a name="routing-with-urls-that-contain-dots"></a>Routage avec des URL qui contiennent des points

Pour Blazor WebAssembly les applications hébergées et Blazor Server , le modèle de routage par défaut côté serveur suppose que si le dernier segment d’une URL de demande contient un point ( `.` ) qu’un fichier est demandé. Par exemple, l’URL `https://localhost.com:5001/example/some.thing` est interprétée par le routeur comme une demande pour un fichier nommé `some.thing` . Sans configuration supplémentaire, une application retourne une réponse *404-introuvable* si `some.thing` a été conçu pour router vers un composant avec une [ `@page` directive](xref:mvc/views/razor#page) et `some.thing` est une valeur de paramètre d’itinéraire. Pour utiliser un itinéraire avec un ou plusieurs paramètres contenant un point, l’application doit configurer l’itinéraire avec un modèle personnalisé.

Prenons le `Example` composant suivant qui peut recevoir un paramètre d’itinéraire à partir du dernier segment de l’URL.

`Pages/Example.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/Example.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/Example.razor?highlight=2)]

::: moniker-end

Pour permettre à l' *`Server`* application d’une Blazor WebAssembly solution hébergée d’acheminer la demande avec un point dans le `param` paramètre d’itinéraire, ajoutez un modèle d’itinéraire de fichier de secours avec le paramètre facultatif dans `Startup.Configure` .

`Startup.cs`:

```csharp
endpoints.MapFallbackToFile("/example/{param?}", "index.html");
```

Pour configurer une Blazor Server application afin d’acheminer la demande avec un point dans le `param` paramètre d’itinéraire, ajoutez un modèle de routage de page de secours avec le paramètre facultatif dans `Startup.Configure` .

`Startup.cs`:

```csharp
endpoints.MapFallbackToPage("/example/{param?}", "/_Host");
```

Pour plus d’informations, consultez <xref:fundamentals/routing>.

## <a name="catch-all-route-parameters"></a>Paramètres d’itinéraire de rattrapage

::: moniker range=">= aspnetcore-5.0"

Les paramètres d’itinéraire Catch-All, qui capturent les chemins d’accès dans plusieurs limites de dossiers, sont pris en charge dans les composants.

Les paramètres d’itinéraire Catch-All sont :

* Nommée pour correspondre au nom du segment de routage. Le nom ne respecte pas la casse.
* Type `string`. L’infrastructure ne fournit pas de conversion automatique.
* À la fin de l’URL.

`Pages/CatchAll.razor`:

[!code-razor[](routing/samples_snapshot/5.x/CatchAll.razor)]

Pour l’URL `/catch-all/this/is/a/test` avec un modèle de routage de `/catch-all/{*pageRoute}` , la valeur de `PageRoute` est définie sur `this/is/a/test` .

Les barres obliques et les segments du chemin capturé sont décodés. Pour un modèle de routage de `/catch-all/{*pageRoute}` , l’URL `/catch-all/this/is/a%2Ftest%2A` génère `this/is/a/test*` .

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Les paramètres d’itinéraire Catch-All sont pris en charge dans ASP.NET Core 5,0 ou version ultérieure. Pour plus d’informations, sélectionnez la version 5,0 de cet article.

::: moniker-end

## <a name="uri-and-navigation-state-helpers"></a>Aide des URI et de l’état de navigation

Utilisez <xref:Microsoft.AspNetCore.Components.NavigationManager> pour gérer les URI et la navigation dans le code C#. <xref:Microsoft.AspNetCore.Components.NavigationManager> fournit l’événement et les méthodes répertoriées dans le tableau suivant.

| Membre | Description |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | Obtient l’URI absolu actuel. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | Obtient l’URI de base (avec une barre oblique finale) qui peut être ajouté aux chemins d’accès URI relatifs pour produire un URI absolu. En général, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> correspond à l' `href` attribut sur l’élément du document `<base>` dans `wwwroot/index.html` ( Blazor WebAssembly ) ou `Pages/_Host.cshtml` ( Blazor Server ). |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | Navigue vers l’URI spécifié. Si `forceLoad` est `true` :<ul><li>Le routage côté client est contourné.</li><li>Le navigateur est obligé de charger la nouvelle page à partir du serveur, que l’URI soit normalement géré ou non par le routeur côté client.</li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | Événement qui se déclenche lorsque l’emplacement de navigation a changé. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | Convertit un URI relatif en URI absolu. |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | À partir d’un URI de base (par exemple, un URI précédemment retourné par <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> ), convertit un URI absolu en un URI relatif au préfixe URI de base. |

Pour l' <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> événement, <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> fournit les informations suivantes sur les événements de navigation :

* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: URL du nouvel emplacement.
* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: Si `true` , a Blazor intercepté la navigation à partir du navigateur. Si `false` <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> la valeur est, la navigation a eu lieu.

Le composant suivant :

* Accède au composant de l’application `Counter` ( `Pages/Counter.razor` ) lorsque le bouton est sélectionné à l’aide de <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> .
* Gère l’événement de modification d’emplacement en s’abonnant à <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> .
  * La `HandleLocationChanged` méthode est déconnectée lorsque `Dispose` est appelé par l’infrastructure. Le déraccordement de la méthode permet de garbage collection du composant.
  * L’implémentation du journal enregistre les informations suivantes lorsque le bouton est sélectionné :

    > `BlazorSample.Pages.Navigate: Information: URL of new location: https://localhost:5001/counter`

`Pages/Navigate.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/Navigate.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/Navigate.razor)]

::: moniker-end

Pour plus d’informations sur la suppression de composants, consultez <xref:blazor/components/lifecycle#component-disposal-with-idisposable> .

## <a name="query-string-and-parse-parameters"></a>Chaîne de requête et paramètres d’analyse

La chaîne de requête d’une requête est obtenue à partir de la <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri?displayProperty=nameWithType> propriété :

```razor
@inject NavigationManager Navigation

...

var query = new Uri(Navigation.Uri).Query;
```

Pour analyser les paramètres d’une chaîne de requête :

* Une application peut utiliser l' <xref:Microsoft.AspNetCore.WebUtilities> API. Si l’API n’est pas disponible pour l’application, ajoutez une référence de package dans le fichier projet de l’application pour [Microsoft. AspNetCore. WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).
* Obtenez la valeur après l’analyse de la chaîne de requête avec <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType> .

L' `ParseQueryString` exemple de composant suivant analyse une clé de paramètre de chaîne de requête nommée `ship` . Par exemple, la paire clé-valeur de la chaîne de requête d’URL `?ship=Tardis` capture la valeur `Tardis` dans `queryValue` . Dans l’exemple suivant, accédez à l’application avec l’URL `https://localhost:5001/parse-query-string?ship=Tardis` .

`Pages/ParseQueryString.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/ParseQueryString.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/ParseQueryString.razor)]

::: moniker-end

## <a name="navlink-component"></a>Composant `NavLink`

Utilisez un <xref:Microsoft.AspNetCore.Components.Routing.NavLink> composant à la place des éléments Hyperlink html ( `<a>` ) lors de la création de liens de navigation. Un <xref:Microsoft.AspNetCore.Components.Routing.NavLink> composant se comporte comme un `<a>` élément, sauf qu’il fait basculer une `active` classe CSS selon que son `href` correspond à l’URL actuelle. La `active` classe permet à un utilisateur de comprendre quelle page est la page active parmi les liens de navigation affichés. Éventuellement, assignez un nom de classe CSS à pour <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> appliquer une classe CSS personnalisée au lien rendu lorsque l’itinéraire actuel correspond à `href` .

Le `NavMenu` composant suivant crée une [`Bootstrap`](https://getbootstrap.com/docs/) barre de navigation qui montre comment utiliser des <xref:Microsoft.AspNetCore.Components.Routing.NavLink> composants :

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/NavMenu.razor?highlight=4,9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

::: moniker-end

> [!NOTE]
> Le `NavMenu` composant ( `NavMenu.razor` ) est fourni dans le `Shared` dossier d’une application générée à partir des Blazor modèles de projet.

Il existe deux <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options que vous pouvez assigner à l' `Match` attribut de l' `<NavLink>` élément :

* <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: Le <xref:Microsoft.AspNetCore.Components.Routing.NavLink> est actif lorsqu’il correspond à la totalité de l’URL actuelle.
* <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*valeur par défaut*) : le <xref:Microsoft.AspNetCore.Components.Routing.NavLink> est actif lorsqu’il correspond à n’importe quel préfixe de l’URL actuelle.

Dans l’exemple précédent, la page <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` d’hébergement correspond à l’URL de base et reçoit uniquement la `active` classe CSS à l’URL du chemin de base par défaut de l’application (par exemple, `https://localhost:5001/` ). Le deuxième <xref:Microsoft.AspNetCore.Components.Routing.NavLink> reçoit la `active` classe lorsque l’utilisateur visite une URL avec un `component` préfixe (par exemple, `https://localhost:5001/component` et `https://localhost:5001/component/another-segment` ).

<xref:Microsoft.AspNetCore.Components.Routing.NavLink>Les attributs de composant supplémentaires sont passés à la balise d’ancrage rendue. Dans l’exemple suivant, le <xref:Microsoft.AspNetCore.Components.Routing.NavLink> composant comprend l' `target` attribut :

```razor
<NavLink href="example-page" target="_blank">Example page</NavLink>
```

Le balisage HTML suivant est rendu :

```html
<a href="example-page" target="_blank">Example page</a>
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

## <a name="aspnet-core-endpoint-routing-integration"></a>Intégration du routage du point de terminaison ASP.NET Core

*Cette section s’applique uniquement aux Blazor Server applications.*

Blazor Server est intégré dans [ASP.net core le routage du point de terminaison](xref:fundamentals/routing). Une ASP.NET Core application est configurée pour accepter les connexions entrantes pour les composants interactifs avec <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> dans `Startup.Configure` .

`Startup.cs`:

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](routing/samples_snapshot/5.x/Startup.cs?highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

::: moniker-end

La configuration classique consiste à acheminer toutes les demandes vers une Razor page, qui joue le rôle d’hôte pour la partie côté serveur de l' Blazor Server application. Par Convention, la page *hôte* est généralement nommée `_Host.cshtml` dans le `Pages` dossier de l’application.

L’itinéraire spécifié dans le fichier hôte est appelé *itinéraire de secours* , car il fonctionne avec une priorité basse dans la correspondance d’itinéraire. L’itinéraire de secours est utilisé lorsque les autres itinéraires ne correspondent pas. Cela permet à l’application d’utiliser d’autres contrôleurs et pages sans interférer avec le routage des composants dans l' Blazor Server application.

Pour plus d’informations sur la configuration <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> de pour l’hébergement de serveur d’URL non racine, consultez <xref:blazor/host-and-deploy/index#app-base-path> .
