---
title: Gestion de l’état des ASP.NET Core Blazor
author: guardrex
description: Découvrez comment rendre l’état persistant dans les Blazor Server applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
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
uid: blazor/state-management
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 811ec08eb457fcf0697a64dc4990d29082454f73
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280860"
---
# <a name="aspnet-core-blazor-state-management"></a>Gestion de l’état des ASP.NET Core Blazor

::: zone pivot="webassembly"

L’état utilisateur créé dans une Blazor WebAssembly application est conservé dans la mémoire du navigateur.

Voici quelques exemples d’état utilisateur contenu dans la mémoire du navigateur :

* La hiérarchie des instances de composant et leur sortie de rendu la plus récente dans l’interface utilisateur rendue.
* Valeurs des champs et des propriétés dans les instances de composant.
* Données conservées dans des instances de service d' [injection de dépendances](xref:fundamentals/dependency-injection) .
* Valeurs définies par le biais d’appels [Interop JavaScript](xref:blazor/call-javascript-from-dotnet) .

Lorsqu’un utilisateur ferme et ouvre à nouveau son navigateur ou recharge la page, l’état utilisateur contenu dans la mémoire du navigateur est perdu.

> [!NOTE]
> Le [stockage du navigateur protégé](xref:blazor/state-management?pivots=server#aspnet-core-protected-browser-storage) ( <xref:Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage?displayProperty=fullName> espace de noms) s’appuie sur la Protection des données ASP.net Core et n’est pris en charge que pour les Blazor Server applications.

## <a name="persist-state-across-browser-sessions"></a>Conserver l’état entre les sessions de navigateur

En règle générale, conservez l’état entre les sessions de navigateur où les utilisateurs créent activement des données, et non simplement des données qui existent déjà.

Pour conserver l’état entre les sessions de navigateur, l’application doit conserver les données dans un autre emplacement de stockage que la mémoire du navigateur. La persistance de l’État n’est pas automatique. Vous devez prendre des mesures lors du développement de l’application pour implémenter la persistance des données avec état.

La persistance des données est généralement requise uniquement pour l’état de valeur élevée que les utilisateurs ont consacrés à la création. Dans les exemples suivants, l’état persistant fait gagner du temps ou contribue à des activités commerciales :

* Web Forms à plusieurs étapes : il est fastidieux pour un utilisateur de saisir à nouveau des données pour plusieurs étapes terminées d’un formulaire Web à plusieurs étapes si leur état est perdu. Un utilisateur perd l’État dans ce scénario s’il quitte le formulaire et le retourne plus tard.
* Paniers d’achat : tout composant commercialement important d’une application qui représente un chiffre d’affaires potentiel peut être maintenu. Un utilisateur qui perd son état et, par conséquent, son panier, peut acheter moins de produits ou de services lorsqu’ils reviennent sur le site ultérieurement.

Une application peut conserver uniquement l’état de l' *application*. Les interfaces utilisateur ne peuvent pas être rendues persistantes, telles que les instances de composant et leurs arborescences de rendu. Les composants et les arborescences de rendu ne sont généralement pas sérialisables. Pour conserver l’état de l’interface utilisateur, tel que les nœuds développés d’un contrôle Tree View, l’application doit utiliser du code personnalisé pour modéliser le comportement de l’état de l’interface utilisateur en tant qu’État de l’application sérialisable.

## <a name="where-to-persist-state"></a>Emplacement de conservation de l’État

Les emplacements communs existent pour l’état de persistance :

* [Stockage côté serveur](#server-side-storage-wasm)
* [URL](#url-wasm)
* [Stockage du navigateur](#browser-storage-wasm)
* [Service de conteneur d’État en mémoire](#in-memory-state-container-service-wasm)

<h2 id="server-side-storage-wasm">Stockage côté serveur</h2>

Pour la persistance des données permanente qui s’étend sur plusieurs utilisateurs et appareils, l’application peut utiliser un stockage indépendant côté serveur accessible via une API Web. Les options sont :

* Stockage Blob
* Stockage clé-valeur
* Base de données relationnelle
* Stockage de tables

Une fois les données enregistrées, l’état de l’utilisateur est conservé et disponible dans toute nouvelle session de navigateur.

Étant donné que Blazor WebAssembly les applications s’exécutent entièrement dans le navigateur de l’utilisateur, elles nécessitent des mesures supplémentaires pour accéder à des systèmes externes sécurisés, tels que les services de stockage et les bases de données. Blazor WebAssembly les applications sont sécurisées de la même façon que les applications à page unique (SPAs). En règle générale, une application authentifie un utilisateur via [OAuth](https://oauth.net) / [OpenID Connect (OIDC)](https://openid.net/connect/) , puis interagit avec les services de stockage et les bases de données par le biais d’appels d’API Web à une application côté serveur. L’application côté serveur convertit le transfert de données entre l' Blazor WebAssembly application et le service de stockage ou la base de données. L' Blazor WebAssembly application maintient une connexion éphémère à l’application côté serveur, tandis que l’application côté serveur dispose d’une connexion permanente au stockage.

Pour plus d’informations, consultez les ressources suivantes :

* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>
* Blazor*Sécurité et Identity* traitant

Pour plus d’informations sur les options de stockage de données Azure, consultez les rubriques suivantes :

* [Bases de données Azure](https://azure.microsoft.com/product-categories/databases/)
* [Documentation d’Azure Storage](/azure/storage/)

<h2 id="url-wasm">URL</h2>

Pour les données temporaires représentant l’état de navigation, modélisez les données en tant que partie de l’URL. Voici des exemples d’état utilisateur modélisé dans l’URL :

* ID d’une entité affichée.
* Numéro de page actuel dans une grille paginée.

Le contenu de la barre d’adresse du navigateur est conservé si l’utilisateur recharge manuellement la page.

Pour plus d’informations sur la définition de modèles d’URL avec la [`@page`](xref:mvc/views/razor#page) directive, consultez <xref:blazor/fundamentals/routing> .

<h2 id="browser-storage-wasm">Stockage du navigateur</h2>

Pour les données temporaires que l’utilisateur crée activement, un emplacement de stockage couramment utilisé est celui des [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) regroupements et du navigateur :

* `localStorage` est limité à la fenêtre du navigateur. Si l’utilisateur recharge la page ou ferme et ouvre à nouveau le navigateur, l’état persiste. Si l’utilisateur ouvre plusieurs onglets de navigateur, l’État est partagé à travers les onglets. Les données sont conservées dans `localStorage` jusqu’à ce qu’elles soient explicitement effacées.
* `sessionStorage` est étendu à l’onglet navigateur. Si l’utilisateur recharge l’onglet, l’état persiste. Si l’utilisateur ferme l’onglet ou le navigateur, l’État est perdu. Si l’utilisateur ouvre plusieurs onglets de navigateur, chaque onglet possède sa propre version indépendante des données.

> [!NOTE]
> `localStorage` et `sessionStorage` peuvent être utilisés dans les Blazor WebAssembly applications, mais uniquement en écrivant du code personnalisé ou à l’aide d’un package tiers.

En règle générale, `sessionStorage` il est plus sûr d’utiliser. `sessionStorage` évite le risque qu’un utilisateur ouvre plusieurs onglets et rencontre les éléments suivants :

* Bogues dans le stockage d’État sur les onglets.
* Comportement confus quand une tabulation remplace l’état d’autres onglets.

`localStorage` est le meilleur choix si l’application doit conserver l’État dans la fermeture et la réouverture du navigateur.

> [!WARNING]
> Les utilisateurs peuvent afficher ou altérer les données stockées dans `localStorage` et `sessionStorage` .

<h2 id="in-memory-state-container-service-wasm">Service de conteneur d’État en mémoire</h2>

[!INCLUDE[](~/blazor/includes/state-container.md)]

## <a name="additional-resources"></a>Ressources supplémentaires

* [Enregistrer l’état de l’application avant une opération d’authentification](xref:blazor/security/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation)
* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>

::: zone-end

::: zone pivot="server"

Blazor Server est une infrastructure d’application avec état. La plupart du temps, l’application maintient une connexion au serveur. L’état de l’utilisateur est conservé dans la mémoire du serveur dans un *circuit*. 

Voici des exemples d’état utilisateur contenu dans un circuit :

* La hiérarchie des instances de composant et leur sortie de rendu la plus récente dans l’interface utilisateur rendue.
* Valeurs des champs et des propriétés dans les instances de composant.
* Données conservées dans des instances de service d' [injection de dépendance (di)](xref:fundamentals/dependency-injection) dont l’étendue correspond au circuit.

L’état utilisateur peut également être trouvé dans les variables JavaScript dans le jeu de mémoire du navigateur via des appels [Interop JavaScript](xref:blazor/call-javascript-from-dotnet) .

Si un utilisateur subit une perte de connexion réseau temporaire, Blazor tente de reconnecter l’utilisateur à son circuit d’origine avec son état d’origine. Toutefois, la reconnexion d’un utilisateur à son circuit d’origine dans la mémoire du serveur n’est pas toujours possible :

* Le serveur ne peut pas conserver un circuit déconnecté de façon infinie. Le serveur doit libérer un circuit déconnecté après un délai d’attente ou lorsque le serveur subit une sollicitation de la mémoire.
* Dans les environnements de déploiement avec équilibrage de charge sur plusieurs serveurs, les serveurs individuels peuvent échouer ou être automatiquement supprimés lorsqu’ils ne sont plus nécessaires pour gérer le volume global de demandes. Le serveur d’origine qui traite les demandes pour un utilisateur peut devenir indisponible lorsque l’utilisateur tente de se reconnecter.
* L’utilisateur peut fermer et rouvrir son navigateur ou recharger la page, ce qui supprime tout État contenu dans la mémoire du navigateur. Par exemple, les valeurs des variables JavaScript définies par le biais d’appels Interop JavaScript sont perdues.

Lorsqu’un utilisateur ne peut pas être reconnecté à son circuit d’origine, l’utilisateur reçoit un nouveau circuit avec un état vide. Cela équivaut à fermer et à rouvrir une application de bureau.

## <a name="persist-state-across-circuits"></a>Conserver l’état entre les circuits

En règle générale, conservez l’état entre les circuits où les utilisateurs créent activement des données, et non simplement des données qui existent déjà.

Pour conserver l’état entre les circuits, l’application doit conserver les données dans un autre emplacement de stockage que la mémoire du serveur. La persistance de l’État n’est pas automatique. Vous devez prendre des mesures lors du développement de l’application pour implémenter la persistance des données avec état.

La persistance des données est généralement requise uniquement pour l’état de valeur élevée que les utilisateurs ont consacrés à la création. Dans les exemples suivants, l’état persistant fait gagner du temps ou contribue à des activités commerciales :

* Web Forms à plusieurs étapes : il est fastidieux pour un utilisateur de saisir à nouveau des données pour plusieurs étapes terminées d’un formulaire Web à plusieurs étapes si leur état est perdu. Un utilisateur perd l’État dans ce scénario s’il quitte le formulaire et le retourne plus tard.
* Paniers d’achat : tout composant commercialement important d’une application qui représente un chiffre d’affaires potentiel peut être maintenu. Un utilisateur qui perd son état et, par conséquent, son panier, peut acheter moins de produits ou de services lorsqu’ils reviennent sur le site ultérieurement.

Une application peut conserver uniquement l’état de l' *application*. Les interfaces utilisateur ne peuvent pas être rendues persistantes, telles que les instances de composant et leurs arborescences de rendu. Les composants et les arborescences de rendu ne sont généralement pas sérialisables. Pour conserver l’état de l’interface utilisateur, tel que les nœuds développés d’un contrôle Tree View, l’application doit utiliser du code personnalisé pour modéliser le comportement de l’état de l’interface utilisateur en tant qu’État de l’application sérialisable.

## <a name="where-to-persist-state"></a>Emplacement de conservation de l’État

Les emplacements communs existent pour l’état de persistance :

* [Stockage côté serveur](#server-side-storage-server)
* [URL](#url-server)
* [Stockage du navigateur](#browser-storage-server)
* [Service de conteneur d’État en mémoire](#in-memory-state-container-service-server)

<h2 id="server-side-storage-server">Stockage côté serveur</h2>

Pour la persistance des données permanente qui s’étend sur plusieurs utilisateurs et appareils, l’application peut utiliser le stockage côté serveur. Les options sont :

* Stockage Blob
* Stockage clé-valeur
* Base de données relationnelle
* Stockage de tables

Une fois les données enregistrées, l’état de l’utilisateur est conservé et disponible dans n’importe quel nouveau circuit.

Pour plus d’informations sur les options de stockage de données Azure, consultez les rubriques suivantes :

* [Bases de données Azure](https://azure.microsoft.com/product-categories/databases/)
* [Documentation d’Azure Storage](/azure/storage/)

<h2 id="url-server">URL</h2>

Pour les données temporaires représentant l’état de navigation, modélisez les données en tant que partie de l’URL. Voici des exemples d’état utilisateur modélisé dans l’URL :

* ID d’une entité affichée.
* Numéro de page actuel dans une grille paginée.

Le contenu de la barre d’adresse du navigateur est conservé :

* Si l’utilisateur recharge manuellement la page.
* Si le serveur Web devient indisponible et que l’utilisateur est obligé de recharger la page pour se connecter à un autre serveur.

Pour plus d’informations sur la définition de modèles d’URL avec la [`@page`](xref:mvc/views/razor#page) directive, consultez <xref:blazor/fundamentals/routing> .

<h2 id="browser-storage-server">Stockage du navigateur</h2>

Pour les données temporaires que l’utilisateur crée activement, un emplacement de stockage couramment utilisé est celui des [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) regroupements et du navigateur :

* `localStorage` est limité à la fenêtre du navigateur. Si l’utilisateur recharge la page ou ferme et ouvre à nouveau le navigateur, l’état persiste. Si l’utilisateur ouvre plusieurs onglets de navigateur, l’État est partagé à travers les onglets. Les données sont conservées dans `localStorage` jusqu’à ce qu’elles soient explicitement effacées.
* `sessionStorage` est étendu à l’onglet navigateur. Si l’utilisateur recharge l’onglet, l’état persiste. Si l’utilisateur ferme l’onglet ou le navigateur, l’État est perdu. Si l’utilisateur ouvre plusieurs onglets de navigateur, chaque onglet possède sa propre version indépendante des données.

En règle générale, `sessionStorage` il est plus sûr d’utiliser. `sessionStorage` évite le risque qu’un utilisateur ouvre plusieurs onglets et rencontre les éléments suivants :

* Bogues dans le stockage d’État sur les onglets.
* Comportement confus quand une tabulation remplace l’état d’autres onglets.

`localStorage` est le meilleur choix si l’application doit conserver l’État dans la fermeture et la réouverture du navigateur.

Avertissements relatifs à l’utilisation du stockage du navigateur :

* À l’instar de l’utilisation d’une base de données côté serveur, le chargement et l’enregistrement des données sont asynchrones.
* Contrairement à une base de données côté serveur, le stockage n’est pas disponible pendant le prérendu, car la page demandée n’existe pas dans le navigateur pendant l’étape de prérendu.
* Le stockage de quelques kilo-octets de données est raisonnable à conserver pour les Blazor Server applications. Au-delà de quelques kilo-octets, vous devez prendre en compte les implications en termes de performances, car les données sont chargées et enregistrées sur le réseau.
* Les utilisateurs peuvent afficher ou altérer les données. La [protection des données ASP.net Core](xref:security/data-protection/introduction) peut atténuer le risque. Par exemple, [ASP.net Core stockage du navigateur protégé](#aspnet-core-protected-browser-storage) utilise la protection des données ASP.net core.

Les packages NuGet tiers fournissent des API pour travailler avec `localStorage` et `sessionStorage` . Il est préférable de choisir un package qui utilise en toute transparence [ASP.net Core la protection des données](xref:security/data-protection/introduction). La protection des données chiffre les données stockées et réduit le risque potentiel de falsification des données stockées. Si les données sérialisées JSON sont stockées en texte brut, les utilisateurs peuvent consulter les données à l’aide des outils de développement du navigateur et modifier également les données stockées. La sécurisation des données n’est pas toujours un problème, car les données peuvent être de nature insignifiante. Par exemple, la lecture ou la modification de la couleur stockée d’un élément d’interface utilisateur n’est pas un risque de sécurité significatif pour l’utilisateur ou l’organisation. Évitez d’autoriser les utilisateurs à inspecter ou altérer des *données sensibles*.

::: moniker range=">= aspnetcore-5.0"

## <a name="aspnet-core-protected-browser-storage"></a>Stockage du navigateur protégé ASP.NET Core

ASP.NET Core le stockage protégé du navigateur tire parti de [ASP.net Core protection des données](xref:security/data-protection/introduction) pour [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) et [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) .

> [!NOTE]
> Le stockage du navigateur protégé s’appuie sur la protection des données ASP.NET Core et n’est pris en charge que pour les Blazor Server applications.

### <a name="save-and-load-data-within-a-component"></a>Enregistrer et charger des données dans un composant

Dans tout composant qui requiert le chargement ou l’enregistrement de données dans le stockage du navigateur, utilisez la [`@inject`](xref:mvc/views/razor#inject) directive pour injecter une instance de l’un des éléments suivants :

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

Le choix dépend de l’emplacement de stockage du navigateur que vous souhaitez utiliser. Dans l’exemple suivant, `sessionStorage` est utilisé :

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

La `@using` directive peut être placée dans le fichier de l’application `_Imports.razor` plutôt que dans le composant. L’utilisation du `_Imports.razor` fichier rend l’espace de noms disponible pour les plus grands segments de l’application ou de l’application entière.

Pour rendre la `currentCount` valeur persistante dans le `Counter` composant d’une application en fonction du Blazor Server modèle de projet, modifiez la `IncrementCount` méthode pour utiliser `ProtectedSessionStore.SetAsync` :

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

Dans les applications plus volumineuses et plus réalistes, le stockage de champs individuels est un scénario peu probable. Les applications sont plus susceptibles de stocker des objets de modèle entiers qui incluent un État complexe. `ProtectedSessionStore` sérialise et désérialise automatiquement des données JSON pour stocker des objets d’État complexes.

Dans l’exemple de code précédent, les `currentCount` données sont stockées sous `sessionStorage['count']` la forme dans le navigateur de l’utilisateur. Les données ne sont pas stockées en texte brut mais sont protégées à l’aide d’ASP.NET Core protection des données. Les données chiffrées peuvent être inspectées si la `sessionStorage['count']` est évaluée dans la console de développement du navigateur.

Pour récupérer les `currentCount` données si l’utilisateur retourne au `Counter` composant ultérieurement, y compris si l’utilisateur se trouve sur un nouveau circuit, utilisez `ProtectedSessionStore.GetAsync` :

```csharp
protected override async Task OnInitializedAsync()
{
    var result = await ProtectedSessionStore.GetAsync<int>("count");
    currentCount = result.Success ? result.Value : 0;
}
```

Si les paramètres du composant incluent l’état de navigation, appelez `ProtectedSessionStore.GetAsync` et assignez un non- `null` résultat dans <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> , et non <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> . <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> la méthode est appelée une seule fois lors de la première instanciation du composant. <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> n’est pas rappelée ultérieurement si l’utilisateur accède à une autre URL tout en restant sur la même page. Pour plus d’informations, consultez <xref:blazor/components/lifecycle>.

> [!WARNING]
> Les exemples de cette section ne fonctionnent que si le prérendu n’est pas activé sur le serveur. Quand le prérendu est activé, une erreur est générée, expliquant que les appels Interop JavaScript ne peuvent pas être émis car le composant est en cours de prérendu.
>
> Désactivez le prérendu ou ajoutez du code supplémentaire pour utiliser le prérendu. Pour en savoir plus sur l’écriture de code qui fonctionne avec le prérendu, consultez la section [handle PreRender](#handle-prerendering) .

### <a name="handle-the-loading-state"></a>Gérer l’état de chargement

Étant donné que le stockage du navigateur est accessible de façon asynchrone via une connexion réseau, il y a toujours un certain temps avant que les données soient chargées et disponibles pour un composant. Pour obtenir les meilleurs résultats, affichez un message d’état de chargement pendant le chargement en cours au lieu d’afficher les données vides ou par défaut.

Une approche consiste à déterminer si les données sont `null` , ce qui signifie que les données sont toujours en cours de chargement. Dans le composant par défaut `Counter` , le nombre est conservé dans un `int` . [Rendez la valeur `currentCount` null](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) en ajoutant un point d’interrogation ( `?` ) au type ( `int` ) :

```csharp
private int? currentCount;
```

Au lieu d’afficher sans condition le nombre et le **`Increment`** bouton, affichez ces éléments uniquement si les données sont chargées en vérifiant <xref:System.Nullable%601.HasValue%2A> :

```razor
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a>Gérer le prérendu

Lors du prérendu :

* Une connexion interactive au navigateur de l’utilisateur n’existe pas.
* Le navigateur ne dispose pas encore d’une page dans laquelle il peut exécuter du code JavaScript.

`localStorage` ou `sessionStorage` ne sont pas disponibles pendant le prérendu. Si le composant tente d’interagir avec le stockage, une erreur est générée, expliquant que les appels Interop JavaScript ne peuvent pas être émis car le composant est en cours de prérendu.

L’une des méthodes permettant de résoudre l’erreur consiste à désactiver le prérendu. C’est généralement le meilleur choix si l’application utilise beaucoup le stockage basé sur le navigateur. Le prérendu ajoute de la complexité et ne tire pas parti de l’application, car l’application ne peut pas prérestituer de contenu utile tant que `localStorage` ou `sessionStorage` n’est pas disponible.

Pour désactiver le prérendu, ouvrez le `Pages/_Host.cshtml` fichier et remplacez l' `render-mode` attribut de [tag Helper du composant](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) par <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> :

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

Le prérendu peut être utile pour d’autres pages qui n’utilisent pas `localStorage` ou `sessionStorage` . Pour conserver le prérendu, différez l’opération de chargement jusqu’à ce que le navigateur soit connecté au circuit. Voici un exemple de stockage d’une valeur de compteur :

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

@if (isConnected)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}

@code {
    private int currentCount;
    private bool isConnected;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        var result = await ProtectedLocalStore.GetAsync<int>("count");
        currentCount = result.Success ? result.Value : 0;
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedLocalStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a>Factoriser la conservation de l’État à un emplacement commun

Si de nombreux composants reposent sur un stockage basé sur un navigateur, la réimplémentation du code du fournisseur d’état de nombreuses fois crée une duplication du code. Une option pour éviter la duplication de code consiste à créer un *composant parent du fournisseur d’État* qui encapsule la logique du fournisseur d’État. Les composants enfants peuvent fonctionner avec des données persistantes sans tenir compte du mécanisme de persistance de l’État.

Dans l’exemple suivant d’un `CounterStateProvider` composant, les données de compteur sont conservées dans `sessionStorage` :

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (isLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool isLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        var result = await ProtectedSessionStore.GetAsync<int>("count");
        currentCount = result.Success ? result.Value : 0;
        isLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

Le `CounterStateProvider` composant gère la phase de chargement en n’affichant pas son contenu enfant tant que le chargement n’est pas terminé.

Pour utiliser le `CounterStateProvider` composant, encapsulez une instance du composant autour de tout autre composant qui requiert l’accès à l’état du compteur. Pour rendre l’état accessible à tous les composants d’une application, encapsulez le `CounterStateProvider` composant autour de <xref:Microsoft.AspNetCore.Components.Routing.Router> dans le `App` composant ( `App.razor` ) :

```razor
<CounterStateProvider>
    <Router AppAssembly="@typeof(Program).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

Les composants encapsulés reçoivent et peuvent modifier l’état du compteur persistant. Le `Counter` composant suivant implémente le modèle :

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>
<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

Le composant précédent n’est pas requis pour interagir avec `ProtectedBrowserStorage` et ne gère pas une phase de « chargement ».

Pour traiter le prérendu comme décrit précédemment, `CounterStateProvider` peut être modifié de façon à ce que tous les composants qui consomment les données de compteur fonctionnent automatiquement avec le prérendu. Pour plus d’informations, consultez la section [handle PreRender](#handle-prerendering) .

En général, le modèle de *composant parent du fournisseur d’État* est recommandé :

* Pour utiliser l’État sur de nombreux composants.
* S’il n’existe qu’un seul objet d’état de niveau supérieur à conserver.

Pour conserver un grand nombre d’objets d’état différents et utiliser différents sous-ensembles d’objets à des emplacements différents, il est préférable d’éviter de conserver l’État globalement.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="protected-browser-storage-experimental-nuget-package"></a>Package NuGet expérimental du stockage du navigateur protégé

ASP.NET Core le stockage protégé du navigateur tire parti de [ASP.net Core protection des données](xref:security/data-protection/introduction) pour [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) et [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) .

> [!WARNING]
> `Microsoft.AspNetCore.ProtectedBrowserStorage` est un package expérimental non pris en charge et inapproprié pour une utilisation en production.
>
> Le package n’est disponible que pour une utilisation dans les applications ASP.NET Core 3,1 Blazor Server .

### <a name="configuration"></a>Configuration

1. Ajoutez une référence de package à [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage) .
1. Dans le `Pages/_Host.cshtml` fichier, ajoutez le script suivant à l’intérieur de la `</body>` balise de fermeture :

   ```cshtml
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. Dans `Startup.ConfigureServices` , appelez `AddProtectedBrowserStorage` pour ajouter `localStorage` des `sessionStorage` services à la collection de services :

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a>Enregistrer et charger des données dans un composant

Dans tout composant qui requiert le chargement ou l’enregistrement de données dans le stockage du navigateur, utilisez la [`@inject`](xref:mvc/views/razor#inject) directive pour injecter une instance de l’un des éléments suivants :

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

Le choix dépend de l’emplacement de stockage du navigateur que vous souhaitez utiliser. Dans l’exemple suivant, `sessionStorage` est utilisé :

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

L' `@using` instruction peut être placée dans un `_Imports.razor` fichier plutôt que dans le composant. L’utilisation du `_Imports.razor` fichier rend l’espace de noms disponible pour les plus grands segments de l’application ou de l’application entière.

Pour rendre la `currentCount` valeur persistante dans le `Counter` composant d’une application en fonction du Blazor Server modèle de projet, modifiez la `IncrementCount` méthode pour utiliser `ProtectedSessionStore.SetAsync` :

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

Dans les applications plus volumineuses et plus réalistes, le stockage de champs individuels est un scénario peu probable. Les applications sont plus susceptibles de stocker des objets de modèle entiers qui incluent un État complexe. `ProtectedSessionStore` sérialise et désérialise automatiquement les données JSON.

Dans l’exemple de code précédent, les `currentCount` données sont stockées sous `sessionStorage['count']` la forme dans le navigateur de l’utilisateur. Les données ne sont pas stockées en texte brut mais sont protégées à l’aide d’ASP.NET Core protection des données. Les données chiffrées peuvent être inspectées si la `sessionStorage['count']` est évaluée dans la console de développement du navigateur.

Pour récupérer les `currentCount` données si l’utilisateur retourne au `Counter` composant ultérieurement, y compris s’il s’agit d’un circuit entièrement nouveau, `ProtectedSessionStore.GetAsync` utilisez :

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

Si les paramètres du composant incluent l’état de navigation, appelez `ProtectedSessionStore.GetAsync` et assignez le résultat dans <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> , et non <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> . <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> la méthode est appelée une seule fois lors de la première instanciation du composant. <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> n’est pas rappelée ultérieurement si l’utilisateur accède à une autre URL tout en restant sur la même page. Pour plus d’informations, consultez <xref:blazor/components/lifecycle>.

> [!WARNING]
> Les exemples de cette section ne fonctionnent que si le prérendu n’est pas activé sur le serveur. Quand le prérendu est activé, une erreur est générée, expliquant que les appels Interop JavaScript ne peuvent pas être émis car le composant est en cours de prérendu.
>
> Désactivez le prérendu ou ajoutez du code supplémentaire pour utiliser le prérendu. Pour en savoir plus sur l’écriture de code qui fonctionne avec le prérendu, consultez la section [handle PreRender](#handle-prerendering) .

### <a name="handle-the-loading-state"></a>Gérer l’état de chargement

Étant donné que le stockage du navigateur est accessible de façon asynchrone via une connexion réseau, il y a toujours un certain temps avant que les données soient chargées et disponibles pour un composant. Pour obtenir les meilleurs résultats, affichez un message d’état de chargement pendant le chargement en cours au lieu d’afficher les données vides ou par défaut.

Une approche consiste à déterminer si les données sont `null` , ce qui signifie que les données sont toujours en cours de chargement. Dans le composant par défaut `Counter` , le nombre est conservé dans un `int` . [Rendez la valeur `currentCount` null](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) en ajoutant un point d’interrogation ( `?` ) au type ( `int` ) :

```csharp
private int? currentCount;
```

Au lieu d’afficher sans condition le nombre et le **`Increment`** bouton, choisissez d’afficher ces éléments uniquement si les données sont chargées :

```razor
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a>Gérer le prérendu

Lors du prérendu :

* Une connexion interactive au navigateur de l’utilisateur n’existe pas.
* Le navigateur ne dispose pas encore d’une page dans laquelle il peut exécuter du code JavaScript.

`localStorage` ou `sessionStorage` ne sont pas disponibles pendant le prérendu. Si le composant tente d’interagir avec le stockage, une erreur est générée, expliquant que les appels Interop JavaScript ne peuvent pas être émis car le composant est en cours de prérendu.

L’une des méthodes permettant de résoudre l’erreur consiste à désactiver le prérendu. C’est généralement le meilleur choix si l’application utilise beaucoup le stockage basé sur le navigateur. Le prérendu ajoute de la complexité et ne tire pas parti de l’application, car l’application ne peut pas prérestituer de contenu utile tant que `localStorage` ou `sessionStorage` n’est pas disponible.

Pour désactiver le prérendu, ouvrez le `Pages/_Host.cshtml` fichier et remplacez l' `render-mode` attribut de [tag Helper du composant](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) par <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> :

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

Le prérendu peut être utile pour d’autres pages qui n’utilisent pas `localStorage` ou `sessionStorage` . Pour conserver le prérendu, différez l’opération de chargement jusqu’à ce que le navigateur soit connecté au circuit. Voici un exemple de stockage d’une valeur de compteur :

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

@if (isConnected)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}

@code {
    private int? currentCount;
    private bool isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        currentCount = await ProtectedLocalStore.GetAsync<int>("count");
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedLocalStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a>Factoriser la conservation de l’État à un emplacement commun

Si de nombreux composants reposent sur un stockage basé sur un navigateur, la réimplémentation du code du fournisseur d’état de nombreuses fois crée une duplication du code. Une option pour éviter la duplication de code consiste à créer un *composant parent du fournisseur d’État* qui encapsule la logique du fournisseur d’État. Les composants enfants peuvent fonctionner avec des données persistantes sans tenir compte du mécanisme de persistance de l’État.

Dans l’exemple suivant d’un `CounterStateProvider` composant, les données de compteur sont conservées dans `sessionStorage` :

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (isLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool isLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        isLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

Le `CounterStateProvider` composant gère la phase de chargement en n’affichant pas son contenu enfant tant que le chargement n’est pas terminé.

Pour utiliser le `CounterStateProvider` composant, encapsulez une instance du composant autour de tout autre composant qui requiert l’accès à l’état du compteur. Pour rendre l’état accessible à tous les composants d’une application, encapsulez le `CounterStateProvider` composant autour de <xref:Microsoft.AspNetCore.Components.Routing.Router> dans le `App` composant ( `App.razor` ) :

```razor
<CounterStateProvider>
    <Router AppAssembly="@typeof(Program).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

Les composants encapsulés reçoivent et peuvent modifier l’état du compteur persistant. Le `Counter` composant suivant implémente le modèle :

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>
<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

Le composant précédent n’est pas requis pour interagir avec `ProtectedBrowserStorage` et ne gère pas une phase de « chargement ».

Pour traiter le prérendu comme décrit précédemment, `CounterStateProvider` peut être modifié de façon à ce que tous les composants qui consomment les données de compteur fonctionnent automatiquement avec le prérendu. Pour plus d’informations, consultez la section [handle PreRender](#handle-prerendering) .

En général, le modèle de *composant parent du fournisseur d’État* est recommandé :

* Pour utiliser l’État sur de nombreux composants.
* S’il n’existe qu’un seul objet d’état de niveau supérieur à conserver.

Pour conserver un grand nombre d’objets d’état différents et utiliser différents sous-ensembles d’objets à des emplacements différents, il est préférable d’éviter de conserver l’État globalement.

::: moniker-end

<h2 id="in-memory-state-container-service-server">Service de conteneur d’État en mémoire</h2>

[!INCLUDE[](~/blazor/includes/state-container.md)]

::: zone-end
