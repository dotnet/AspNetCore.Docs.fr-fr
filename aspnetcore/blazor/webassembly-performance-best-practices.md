---
title: Blazor WebAssemblyMeilleures pratiques en matière de performances de ASP.net Core
author: pranavkm
description: Conseils pour améliorer les performances dans les Blazor WebAssembly applications ASP.net Core et éviter les problèmes de performances courants.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 10/09/2020
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
uid: blazor/webassembly-performance-best-practices
ms.openlocfilehash: 423745d734d8da2b8f3f974f9b4dd1a0265d4877
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054734"
---
# <a name="aspnet-core-no-locblazor-webassembly-performance-best-practices"></a>Blazor WebAssemblyMeilleures pratiques en matière de performances de ASP.net Core

Par [Pranav Krishnamoorthy](https://github.com/pranavkm) et [Steve Sanderson](https://github.com/SteveSandersonMS)

Blazor WebAssembly est conçu et optimisé pour assurer des performances élevées dans les scénarios d’interface utilisateur d’application les plus réalistes. Toutefois, la production des meilleurs résultats dépend des développeurs qui utilisent les modèles et les fonctionnalités appropriés. Tenez compte des aspects suivants :

* **Débit du runtime** : le code .net s’exécute sur un interpréteur au sein du runtime webassembly, le débit de l’UC est donc limité. Dans les scénarios exigeants, l’application tire parti de l’optimisation de la [vitesse de rendu](#optimize-rendering-speed).
* **Heure de démarrage** : l’application transfère un Runtime .net au navigateur. il est donc important d’utiliser des fonctionnalités qui [réduisent la taille du téléchargement de l’application](#minimize-app-download-size).

## <a name="optimize-rendering-speed"></a>Optimiser la vitesse de rendu

Les sections suivantes fournissent des recommandations pour réduire la charge de travail de rendu et améliorer la réactivité de l’interface utilisateur. Le fait de suivre ce Conseil pourrait facilement améliorer la vitesse de rendu de l’interface utilisateur à *dix fois ou plus* .

### <a name="avoid-unnecessary-rendering-of-component-subtrees"></a>Éviter le rendu inutile des sous-arborescences de composants

Au moment de l’exécution, les composants existent sous la forme d’une hiérarchie. Un composant racine a des composants enfants. À son tour, les enfants de la racine ont leurs propres composants enfants, et ainsi de suite. Lorsqu’un événement se produit, tel qu’un utilisateur qui sélectionne un bouton, c’est la méthode Blazor qui détermine les composants à restituer :

 1. L’événement lui-même est distribué à n’importe quel composant qui rend le gestionnaire de l’événement. Après l’exécution du gestionnaire d’événements, ce composant est rerendu.
 1. Chaque fois qu’un composant est rerendu, il fournit une nouvelle copie des valeurs de paramètre à chacun de ses composants enfants.
 1. Lors de la réception d’un nouvel ensemble de valeurs de paramètres, chaque composant choisit s’il faut effectuer un nouveau rendu. Par défaut, les composants sont rerendus si les valeurs des paramètres ont pu être modifiées (par exemple, s’il s’agit d’objets mutables).

Les deux dernières étapes de cette séquence continuent de manière récursive la hiérarchie des composants. Dans de nombreux cas, l’ensemble de la sous-arborescence est rerendu. Cela signifie que les événements ciblant des composants de haut niveau peuvent entraîner des processus de rerendu coûteux, car tout ce qui se trouve au-dessous de ce point doit être rerendu.

Si vous souhaitez interrompre ce processus et empêcher la récursivité du rendu dans une sous-arborescence particulière, vous pouvez soit :

 * Assurez-vous que tous les paramètres d’un certain composant sont de types immuables primitifs (par exemple,,,, `string` `int` `bool` `DateTime` et autres). La logique intégrée pour la détection des modifications ignore automatiquement le rerendu si aucune de ces valeurs de paramètre n’a changé. Si vous affichez un composant enfant avec `<Customer CustomerId="@item.CustomerId" />` , où `CustomerId` est une `int` valeur, il n’est pas rerendu, sauf en cas de `item.CustomerId` modification.
 * Si vous avez besoin d’accepter des valeurs de paramètre non primitives, telles que des types de modèles personnalisés, des rappels d’événements ou des <xref:Microsoft.AspNetCore.Components.RenderFragment> valeurs, vous pouvez substituer <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> pour contrôler la décision relative à l’affichage, qui est décrit dans l' [utilisation `ShouldRender` de](#use-of-shouldrender) la section.

En ignorant le rerendu des sous-arborescences entières, vous pouvez supprimer la grande majorité du coût de rendu lorsqu’un événement se produit.

Vous souhaiterez peut-être prendre en compte les composants enfants spécifiquement pour pouvoir ignorer le rerendu de cette partie de l’interface utilisateur. Il s’agit d’un moyen valide de réduire le coût de rendu d’un composant parent.

#### <a name="use-of-shouldrender"></a>Utilisation de `ShouldRender`

Si vous créez un composant d’interface utilisateur qui n’est jamais modifié après le rendu initial (indépendamment des valeurs de paramètre), configurez <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> pour retourner `false` :

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

Si le composant nécessite uniquement un rerendu lorsque ses valeurs de paramètres changent de manière particulière, vous pouvez utiliser des champs privés pour suivre les informations nécessaires pour détecter les modifications. Dans l’exemple suivant, `shouldRender` est basé sur la vérification de tout type de modification ou de mutation qui doit demander un rerendu. `prevOutboundFlightId` et `prevInboundFlightId` suivent les informations relatives à la prochaine mise à jour potentielle :

```razor
@code {
    [Parameter]
    public FlightInfo OutboundFlight { get; set; }
    
    [Parameter]
    public FlightInfo InboundFlight { get; set; }

    private int prevOutboundFlightId;
    private int prevInboundFlightId;
    private bool shouldRender;

    protected override void OnParametersSet()
    {
        shouldRender = OutboundFlight.FlightId != prevOutboundFlightId
            || InboundFlight.FlightId != prevInboundFlightId;

        prevOutboundFlightId = OutboundFlight.FlightId;
        prevInboundFlightId = InboundFlight.FlightId;
    }

    protected override void ShouldRender() => shouldRender;

    // Note that 
}
```

Dans le code précédent, un gestionnaire d’événements peut également `shouldRender` avoir la valeur pour `true` que le composant soit rerendu après l’événement.

Pour la plupart des composants, ce niveau de contrôle manuel n’est pas nécessaire. Vous devez uniquement vous préoccuper du fait que vous ignorez le rendu des sous-arborescences si ces sous-arborescences sont particulièrement coûteuses à afficher et sont à l’origine du décalage de l’interface utilisateur.

Pour plus d'informations, consultez <xref:blazor/components/lifecycle>.

::: moniker range=">= aspnetcore-5.0"

### <a name="virtualization"></a>Virtualisation

Lors du rendu de grandes quantités d’IU au sein d’une boucle, par exemple une liste ou une grille avec des milliers d’entrées, la quantité importante d’opérations de rendu peut entraîner un décalage de l’interface utilisateur et donc une expérience utilisateur médiocre. Étant donné que l’utilisateur ne peut voir qu’un petit nombre d’éléments à la fois sans faire défiler la liste, il semblerait gaspiller de perdre du temps à afficher des éléments qui ne sont pas actuellement visibles.

Pour résoudre ce Blazor problème, fournit un [ `<Virtualize>` composant](xref:blazor/components/virtualization) intégré qui crée les comportements d’apparence et de défilement d’une liste arbitrairement grande, mais qui affiche en fait uniquement les éléments de liste qui se trouvent dans la fenêtre d’affichage de défilement en cours. Par exemple, cela signifie que l’application peut avoir une liste avec 100 000 entrées, mais uniquement le coût de rendu de 20 éléments visibles à un moment donné. L’utilisation du `<Virtualize>` composant peut mettre à l’échelle les performances de l’interface utilisateur par ordre de grandeur.

`<Virtualize>` peut être utilisé dans les cas suivants :

 * Rendu d’un ensemble d’éléments de données dans une boucle.
 * La plupart des éléments ne sont pas visibles en raison du défilement.
 * Les éléments rendus ont exactement la même taille. Lorsque l’utilisateur fait défiler jusqu’à un point arbitraire, le composant peut calculer les éléments visibles à afficher.

L’exemple suivant illustre une liste non virtualisée :

```razor
<div class="all-flights" style="height:500px;overflow-y:scroll">
    @foreach (var flight in allFlights)
    {
        <FlightSummary @key="flight.FlightId" Flight="@flight" />
    }
</div>
```

Si la `allFlights` collection contient 10 000 éléments, elle instancie et restitue les `<FlightSummary>` instances de composant 10 000. En comparaison, le code suivant montre un exemple de liste virtualisée :

```razor
<div class="all-flights" style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights" Context="flight">
        <FlightSummary @key="flight.FlightId" Flight="@flight" />
    </Virtualize>
</div>
```

Même si l’interface utilisateur résultante est similaire à un utilisateur, en arrière-plan, le composant instancie et affiche uniquement autant d' `<FlightSummary>` instances que nécessaire pour remplir la région défilante. L’ensemble des `<FlightSummary>` instances affichées est recalculé et restitué lorsque l’utilisateur fait défiler.

`<Virtualize>` offre également d’autres avantages. Par exemple, lorsqu’un composant demande des données à partir d’une API externe, `<Virtualize>` permet au composant d’extraire uniquement le segment des enregistrements qui correspondent à la région visible actuelle, au lieu de télécharger toutes les données de la collection.

Pour plus d'informations, consultez <xref:blazor/components/virtualization>.

::: moniker-end

### <a name="create-lightweight-optimized-components"></a>Créer des composants légers et optimisés

La plupart des Blazor composants ne nécessitent pas d’effort d’optimisation agressif. Cela est dû au fait que la plupart des composants ne se répètent souvent pas dans l’interface utilisateur et ne sont pas restitués à une fréquence élevée. Par exemple, les `@page` composants et les composants qui représentent des éléments d’interface utilisateur de haut niveau, tels que les boîtes de dialogue ou les formulaires, s’affichent le plus souvent un seul à la fois et sont uniquement rerendus en réponse à un mouvement utilisateur. Ces composants ne créent pas une charge de travail de rendu élevée. vous pouvez utiliser librement toute combinaison de fonctionnalités d’infrastructure que vous souhaitez sans vous soucier des performances de rendu.

Toutefois, il existe également des scénarios courants dans lesquels vous créez des composants qui doivent être répétés à l’échelle. Exemple :

 * Les grands formulaires imbriqués peuvent avoir des centaines d’entrées individuelles, d’étiquettes et d’autres éléments.
 * Les grilles peuvent avoir des milliers de cellules.
 * Les nuages de points peuvent comporter des millions de points de données.

En cas de modélisation de chaque unité comme des instances de composant distinctes, il y aura autant d’entre elles que leurs performances de rendu deviennent critiques. Cette section fournit des conseils sur la façon de rendre ces composants légers afin que l’interface utilisateur reste rapide et réactive.

#### <a name="avoid-thousands-of-component-instances"></a>Éviter des milliers d’instances de composant

Chaque composant est un îlot distinct qui peut être rendu indépendamment de ses parents et de ses enfants. En choisissant comment fractionner l’interface utilisateur en une hiérarchie de composants, vous prenez le contrôle de la granularité du rendu de l’interface utilisateur. Il peut s’agir d’une valeur correcte ou d’une mauvaise performance.

 * En fractionnant l’interface utilisateur en plusieurs composants, vous pouvez avoir des parties plus petites du rendu de l’interface utilisateur lorsque des événements se produisent. Par exemple, lorsqu’un utilisateur clique sur un bouton dans une ligne de table, vous pouvez être en mesure d’avoir uniquement ce rerendu de ligne unique à la place de la page ou de la table entière.
 * Toutefois, chaque composant supplémentaire implique des surcharges mémoire et processeur supplémentaires pour gérer son cycle de vie d’État et de rendu indépendant.

Lors du réglage des performances de Blazor WebAssembly sur .net 5, nous avons mesuré une surcharge de rendu d’environ 0,06 ms par instance de composant. Elle est basée sur un composant simple qui accepte trois paramètres s’exécutant sur un ordinateur portable standard. En interne, la surcharge est principalement due à la récupération de l’État par composant à partir de dictionnaires et au passage et à la réception de paramètres. Par multiplication, vous pouvez constater que l’ajout d’instances de composants supplémentaires 2 000 ajoute 0,12 secondes à l’heure de rendu et que l’interface utilisateur a commencé à être trop lente pour les utilisateurs.

Il est possible de rendre les composants plus légers afin de pouvoir en avoir davantage, mais la technique la plus puissante n’est souvent pas d’avoir autant de composants. Les sections suivantes décrivent deux approches.

##### <a name="inline-child-components-into-their-parents"></a>Composants enfants Inline dans leurs parents

Prenons le composant suivant qui restitue une séquence de composants enfants :

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        <ChatMessageDisplay Message="@message" />
    }
</div>
```

Pour l’exemple de code précédent, le `<ChatMessageDisplay>` composant est défini dans un fichier `ChatMessageDisplay.razor` contenant :

```razor
<div class="chat-message">
    <span class="author">@Message.Author</span>
    <span class="text">@Message.Text</span>
</div>

@code {
    [Parameter]
    public ChatMessage Message { get; set; }
}
```

L’exemple précédent fonctionne correctement et fonctionne bien tant que des milliers de messages ne sont pas affichés à la fois. Pour afficher des milliers de messages à la fois, envisagez de *ne pas* factoriser le `ChatMessageDisplay` composant séparé. Au lieu de cela, incorporez le rendu directement dans le parent :

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        <div class="chat-message">
            <span class="author">@message.Author</span>
            <span class="text">@message.Text</span>
        </div>
    }
</div>
```

Cela permet d’éviter la surcharge par composant du rendu d’un grand nombre de composants enfants au prix de ne pas être en mesure de les restituer séparément.

##### <a name="define-reusable-renderfragments-in-code"></a>Définir réutilisable `RenderFragments` dans le code

Vous pouvez prendre en compte les composants enfants comme un moyen de réutiliser la logique de rendu. Si c’est le cas, il est toujours possible de réutiliser la logique de rendu sans déclarer les composants réels. Dans le bloc d’un composant `@code` , vous pouvez définir un <xref:Microsoft.AspNetCore.Components.RenderFragment> qui émet l’interface utilisateur et peut être appelé à partir de n’importe quel emplacement :

```razor
<h1>Hello, world!</h1>

@RenderWelcomeInfo

@code {
    RenderFragment RenderWelcomeInfo = __builder =>
    {
        <div>
            <p>Welcome to your new app!</p>

            <SurveyPrompt Title="How is Blazor working for you?" />
        </div>
    };
}
```

Comme demonstated dans l’exemple précédent, les composants peuvent émettre un balisage à partir du code dans leur `@code` bloc et en dehors de celui-ci. Cette approche définit un <xref:Microsoft.AspNetCore.Components.RenderFragment> délégué que vous pouvez afficher à l’intérieur de la sortie de rendu normale du composant, éventuellement à plusieurs endroits. Il est nécessaire que le délégué accepte un paramètre appelé `__builder` de type <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> afin que le Razor compilateur puisse produire des instructions de rendu pour celui-ci.

Si vous souhaitez rendre ce réutilisable sur plusieurs composants, pensez à le déclarer en tant que `public static` membre :

```razor
public static RenderFragment SayHello = __builder =>
{
    <h1>Hello!</h1>
};
```

Cela peut maintenant être appelé à partir d’un composant non lié. Cette technique est utile pour générer des bibliothèques d’extraits de code réutilisables qui s’affichent sans surcharge par composant.

<xref:Microsoft.AspNetCore.Components.RenderFragment> les délégués peuvent également accepter des paramètres. Pour créer l’équivalent du `ChatMessageDisplay` composant à partir de l’exemple précédent :

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        @DisplayChatMessage(message)
    }
</div>

@code {
    RenderFragment<ChatMessage> DisplayChatMessage = message => __builder =>
    {
        <div class="chat-message">
            <span class="author">@message.Author</span>
            <span class="text">@message.Text</span>
        </div>
    };
}
```

Cette approche offre l’avantage de réutiliser la logique de rendu sans surcharge par composant. Toutefois, il n’a pas l’avantage de pouvoir actualiser sa sous-arborescence de l’interface utilisateur de manière indépendante, pas plus qu’il ne peut ignorer le rendu de cette sous-arborescence de l’interface utilisateur lors du rendu de son parent, car il n’y a pas de limite de composant.

#### <a name="dont-receive-too-many-parameters"></a>Ne pas recevoir un trop grand nombre de paramètres

Si un composant se répète très souvent, par exemple des centaines ou des milliers de fois, gardez à l’esprit que la surcharge liée à la transmission et à la réception de chaque paramètre est générée.

Il est rare qu’un trop grand nombre de paramètres restreint gravement les performances, mais cela peut être un facteur. Pour un `<TableCell>` composant qui restitue 1 000 fois dans une grille, chaque paramètre supplémentaire qui lui est transmis peut ajouter environ 15 ms au coût total de rendu. Si chaque cellule a accepté 10 paramètres, le passage de paramètres prend environ 150 MS par rendu de composant et, par conséquent, peut-être 150 000 MS (150 secondes) et, par conséquent, une interface utilisateur laggy.

Pour réduire cette charge, vous pouvez regrouper plusieurs paramètres via des classes personnalisées. Par exemple, un `<TableCell>` composant peut accepter les éléments suivants :

```razor
@typeparam TItem

...

@code {
    [Parameter]
    public TItem Data { get; set; }
    
    [Parameter]
    public GridOptions Options { get; set; }
}
```

Dans l’exemple précédent, `Data` est différent pour chaque cellule, mais `Options` est commun à tous. Bien entendu, il peut s’agir d’une amélioration ne permettant pas d’avoir un composant et d’inverser `<TableCell>` sa logique dans le composant parent.

#### <a name="ensure-cascading-parameters-are-fixed"></a>S’assurer que les paramètres en cascade sont fixes

Le `<CascadingValue>` composant a un paramètre facultatif appelé `IsFixed` .

 * Si la `IsFixed` valeur est `false` (valeur par défaut), chaque destinataire de la valeur en cascade configure un abonnement pour recevoir des notifications de modification. Dans ce cas, chacune `[CascadingParameter]` d’elles est **beaucoup plus coûteuse** qu’une normale `[Parameter]` en raison du suivi des abonnements.
 * Si la `IsFixed` valeur est `true` (par exemple, `<CascadingValue Value="@someValue" IsFixed="true">` ), destinataires reçoit la valeur initiale, mais ne configure *pas* d’abonnement pour recevoir des mises à jour. Dans ce cas, chacune `[CascadingParameter]` est légère et **n’est pas plus coûteuse** qu’une normale `[Parameter]` .

Par conséquent, dans la mesure du possible, vous devez utiliser `IsFixed="true"` des valeurs en cascade. Vous pouvez effectuer cette opération chaque fois que la valeur fournie ne change pas au fil du temps. Dans le modèle commun où un composant passe `this` comme une valeur en cascade, vous devez utiliser `IsFixed="true"` :

```razor
<CascadingValue Value="this" IsFixed="true">
    <SomeOtherComponents>
</CascadingValue>
```

Cela fait une énorme différence s’il y a un grand nombre d’autres composants qui reçoivent la valeur en cascade. Pour plus d'informations, consultez <xref:blazor/components/cascading-values-and-parameters>.

#### <a name="avoid-attribute-splatting-with-captureunmatchedvalues"></a>Éviter la projection d’attributs avec `CaptureUnmatchedValues`

Les composants peuvent choisir de recevoir des valeurs de paramètre « sans correspondance » à l’aide de l' <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> indicateur :

```razor
<div @attributes="OtherAttributes">...</div>

@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public IDictionary<string, object> OtherAttributes { get; set; }
}
```

Cette approche permet de passer des attributs supplémentaires arbitraires à l’élément. Toutefois, il est également très onéreux, car le convertisseur doit :

* Mettre en correspondance tous les paramètres fournis par rapport au jeu de paramètres connus pour générer un dictionnaire.
* Effectuer le suivi de la façon dont plusieurs copies du même attribut sont remplacées.

N’hésitez pas à utiliser <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> des composants non critiques pour les performances, tels que ceux qui ne sont pas fréquemment répétés. Toutefois, pour les composants qui restituent à l’échelle, tels que les éléments d’une grande liste ou les cellules d’une grille, essayez d’éviter la projection d’attributs.

Pour plus d'informations, consultez <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.

#### <a name="implement-setparametersasync-manually"></a>Implémenter `SetParametersAsync` manuellement

L’un des principaux aspects de la surcharge de rendu par composant consiste à écrire des valeurs de paramètre entrantes dans les `[Parameter]` Propriétés. Pour ce faire, le convertisseur doit utiliser la réflexion. Même s’il est un peu optimisé, l’absence de prise en charge de JIT sur le runtime webassembly impose des limites.

Dans certains cas extrêmes, vous souhaiterez peut-être éviter la réflexion et implémenter manuellement votre propre logique de paramétrage de paramètres. Cela peut s’appliquer dans les cas suivants :

 * Vous disposez d’un composant qui effectue un rendu extrêmement fréquent (par exemple, il y a des centaines voire des milliers de copies dans l’interface utilisateur).
 * Il accepte un grand nombre de paramètres.
 * Vous constatez que la surcharge liée à la réception de paramètres a un impact observable sur la réactivité de l’interface utilisateur.

Dans ce cas, vous pouvez remplacer la méthode virtuelle du composant <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> et implémenter votre propre logique propre au composant. L’exemple suivant évite délibérément les recherches dans les dictionnaires :

```razor
@code {
    [Parameter]
    public int MessageId { get; set; }

    [Parameter]
    public string Text { get; set; }

    [Parameter]
    public EventCallback<string> TextChanged { get; set; }

    [Parameter]
    public Theme CurrentTheme { get; set; }

    public override Task SetParametersAsync(ParameterView parameters)
    {
        foreach (var parameter in parameters)
        {
            switch (parameter.Name)
            {
                case nameof(MessageId):
                    MessageId = (int)parameter.Value;
                    break;
                case nameof(Text):
                    Text = (string)parameter.Value;
                    break;
                case nameof(TextChanged):
                    TextChanged = (EventCallback<string>)parameter.Value;
                    break;
                case nameof(CurrentTheme):
                    CurrentTheme = (Theme)parameter.Value;
                    break;
                default:
                    throw new ArgumentException($"Unknown parameter: {parameter.Name}");
            }
        }

        return base.SetParametersAsync(ParameterView.Empty);
    }
}
```

Dans le code précédent, le retour de la classe de base <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> exécute les méthodes de cycle de vie normales sans assigner à nouveau les paramètres.

Comme vous pouvez le voir dans le code précédent, la substitution <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> et la fourniture d’une logique personnalisée sont compliquées et fastidieuses, donc nous ne recommandons pas cette approche en général. Dans les cas extrêmes, il peut améliorer les performances de rendu de 20-25%, mais cette approche ne doit être prise en compte que dans les scénarios répertoriés plus haut.

### <a name="dont-trigger-events-too-rapidly"></a>Ne pas déclencher d’événements trop rapidement

Certains événements de navigateur se déclenchent très souvent, par exemple `onmousemove` et `onscroll` , ce qui peut déclencher des dizaines ou des centaines de fois par seconde. Dans la plupart des cas, vous n’avez pas besoin d’effectuer des mises à jour de l’interface utilisateur fréquemment. Si vous essayez de le faire, vous risquez de nuire à la réactivité de l’interface utilisateur ou de consommer un temps processeur excessif.

Au lieu d’utiliser `@onmousemove` des événements natifs ou `@onscroll` , vous préférerez peut-être utiliser l’interopérabilité js pour inscrire un rappel qui se déclenche moins fréquemment. Par exemple, le composant suivant ( `MyComponent.razor` ) affiche la position de la souris, mais uniquement les mises à jour une fois toutes les 500 ms :

```razor
@inject IJSRuntime JS
@implements IDisposable

<h1>@message</h1>

<div @ref="myMouseMoveElement" style="border:1px dashed red;height:200px;">
    Move mouse here
</div>

@code {
    ElementReference myMouseMoveElement;
    DotNetObjectReference<MyComponent> selfReference;
    private string message = "Move the mouse in the box";

    [JSInvokable]
    public void HandleMouseMove(int x, int y)
    {
        message = $"Mouse move at {x}, {y}";
        StateHasChanged();
    }

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            selfReference = DotNetObjectReference.Create(this);
            var minInterval = 500; // Only notify every 500 ms
            await JS.InvokeVoidAsync("onThrottledMouseMove", 
                myMouseMoveElement, selfReference, minInterval);
        }
    }

    public void Dispose() => selfReference?.Dispose();
}
```

Le code JavaScript correspondant, qui peut être placé dans la `index.html` page ou chargé en tant que module ES6, inscrit l’écouteur d’événements DOM réel. Dans cet exemple, l’écouteur d’événements utilise la [ `throttle` fonction de Lodash](https://lodash.com/docs/4.17.15#throttle) pour limiter le taux d’appels :

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.20/lodash.min.js"></script>
<script>
  function onThrottledMouseMove(elem, component, interval) {
    elem.addEventListener('mousemove', _.throttle(e => {
      component.invokeMethodAsync('HandleMouseMove', e.offsetX, e.offsetY);
    }, interval));
  }
</script>
```

Cette technique peut être encore plus importante pour Blazor Server , puisque chaque appel d’événement implique la transmission d’un message sur le réseau. Il est utile pour Blazor WebAssembly , car il peut réduire sensiblement la quantité de travail de rendu.

## <a name="optimize-javascript-interop-speed"></a>Optimiser la vitesse de l’interopérabilité JavaScript

Les appels entre .NET et JavaScript impliquent une surcharge supplémentaire, car :

 * Par défaut, les appels sont asynchrones.
 * Par défaut, les paramètres et les valeurs de retour sont sérialisés au format JSON. Cela permet de fournir un mécanisme de conversion facile à comprendre entre les types .NET et JavaScript.

En outre Blazor Server , ces appels sont transmis sur le réseau.

### <a name="avoid-excessively-fine-grained-calls"></a>Évitez les appels extrêmement précis

Étant donné que chaque appel implique une surcharge, il peut être utile de réduire le nombre d’appels. Prenons le code suivant, qui stocke une collection d’éléments dans le magasin du navigateur `localStorage` :

```csharp
private async Task StoreAllInLocalStorage(IEnumerable<TodoItem> items)
{
    foreach (var item in items)
    {
        await JS.InvokeVoidAsync("localStorage.setItem", item.Id, 
            JsonSerializer.Serialize(item));
    }
}
```

L’exemple précédent effectue un appel d’interopérabilité JS distinct pour chaque élément. Au lieu de cela, l’approche suivante réduit l’interopérabilité de JS à un appel unique :

```csharp
private async Task StoreAllInLocalStorage(IEnumerable<TodoItem> items)
{
    await JS.InvokeVoidAsync("storeAllInLocalStorage", items);
}
```

La fonction JavaScript correspondante est définie comme suit :

```javascript
function storeAllInLocalStorage(items) {
  items.forEach(item => {
    localStorage.setItem(item.id, JSON.stringify(item));
  });
}
```

Pour Blazor WebAssembly , cela est généralement important uniquement si vous effectuez un grand nombre d’appels d’interopérabilité js.

### <a name="consider-making-synchronous-calls"></a>Envisagez d’effectuer des appels synchrones

Les appels Interop JavaScript sont asynchrones par défaut, que le code appelé soit synchrone ou asynchrone. Cela permet de garantir la compatibilité des composants avec Blazor WebAssembly et Blazor Server . Sur Blazor Server , tous les appels de l’interopérabilité JavaScript doivent être asynchrones, car ils sont envoyés via une connexion réseau.

Si vous savez que votre application n’est jamais exécutée sur Blazor WebAssembly , vous pouvez choisir d’effectuer des appels d’interopérabilité JavaScript synchrones. Cela a une surcharge légèrement inférieure à la création d’appels asynchrones et peut entraîner moins de cycles de rendu, car il n’y a pas d’état intermédiaire en attendant les résultats.

Pour effectuer un appel synchrone de .NET à JavaScript, effectuez un cast <xref:Microsoft.JSInterop.IJSRuntime> en <xref:Microsoft.JSInterop.IJSInProcessRuntime> :

```razor
@inject IJSRuntime JS

...

@code {
    protected override void HandleSomeEvent()
    {
        var jsInProcess = (IJSInProcessRuntime)JS;
        var value = jsInProcess.Invoke<string>("javascriptFunctionIdentifier");
    }
}
```

::: moniker range=">= aspnetcore-5.0"

Lorsque vous utilisez `IJSObjectReference` , vous pouvez effectuer un appel synchrone en effectuant un cast en `IJSInProcessObjectReference` .

::: moniker-end

Pour effectuer un appel synchrone de JavaScript à .NET, utilisez à la `DotNet.invokeMethod` place de `DotNet.invokeMethodAsync` .

Les appels synchrones fonctionnent dans les cas suivants :

* L’application est en cours d’exécution sur Blazor WebAssembly , et non Blazor Server .
* La fonction appelée retourne une valeur de façon synchrone (il ne s’agit pas d’une `async` méthode et ne retourne pas de .NET <xref:System.Threading.Tasks.Task> ou JavaScript `Promise` ).

Pour plus d'informations, consultez <xref:blazor/call-javascript-from-dotnet>.

::: moniker range=">= aspnetcore-5.0"
 
### <a name="consider-making-unmarshalled-calls"></a>Envisagez d’effectuer des appels démarshalés

Lors de son exécution sur Blazor WebAssembly , il est possible d’effectuer des appels démarshalés de .net à JavaScript. Il s’agit d’appels synchrones qui n’effectuent pas de sérialisation JSON des arguments ou des valeurs de retour. Tous les aspects de la gestion de la mémoire et des traductions entre les représentations .NET et JavaScript sont laissés au développeur.

> [!WARNING]
> Bien que l’utilisation `IJSUnmarshalledRuntime` de ait la surcharge la plus faible des approches de l’interopérabilité de js, les API JavaScript requises pour interagir avec ces API ne sont actuellement pas documentées et sujettes à des modifications importantes dans les versions ultérieures.

```javascript
function jsInteropCall() {
    return BINDING.js_to_mono_obj("Hello world");
}
```

```razor
@inject IJSRuntime JS

@code {
    protected override void OnInitialized()
    {
        var unmarshalledJs = (IJSUnmarshalledRuntime)JS;
        var value = unmarshalledJs.InvokeUnmarshalled<string>("jsInteropCall");
    }
}
```

::: moniker-end

## <a name="minimize-app-download-size"></a>Réduire la taille du téléchargement de l’application

::: moniker range=">= aspnetcore-5.0"

### <a name="intermediate-language-il-trimming"></a>Découpage en langage intermédiaire (IL)

Le [découpage des assemblys inutilisés d’une Blazor WebAssembly application](xref:blazor/host-and-deploy/configure-trimmer) réduit la taille de l’application en supprimant le code inutilisé dans les fichiers binaires de l’application. Par défaut, le massicot est exécuté lors de la publication d’une application. Pour tirer parti de la suppression, publiez l’application pour le déploiement à l’aide de la [`dotnet publish`](/dotnet/core/tools/dotnet-publish) commande avec l’option [-c |--configuration](/dotnet/core/tools/dotnet-publish#options) définie sur `Release` :

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="intermediate-language-il-linking"></a>Liaison en langage intermédiaire (IL)

La [liaison d’une Blazor WebAssembly application](xref:blazor/host-and-deploy/configure-linker) réduit la taille de l’application en découpant le code inutilisé dans les fichiers binaires de l’application. Par défaut, l’éditeur de liens de langage intermédiaire (IL) est activé uniquement lors de la génération de la `Release` Configuration. Pour tirer parti de ce, publiez l’application pour le déploiement à l’aide de la [`dotnet publish`](/dotnet/core/tools/dotnet-publish) commande avec l’option [-c |--configuration](/dotnet/core/tools/dotnet-publish#options) définie sur `Release` :

::: moniker-end

```dotnetcli
dotnet publish -c Release
```

### <a name="use-systemtextjson"></a>Utiliser System.Text.Jssur

Blazorl’implémentation de l’interopérabilité JS de est basée sur <xref:System.Text.Json> , qui est une bibliothèque de SÉRIALISATION JSON haute performance avec une faible allocation de mémoire. L’utilisation <xref:System.Text.Json> de n’entraîne pas de taille de charge utile d’application supplémentaire par rapport à l’ajout d’une ou de plusieurs autres bibliothèques JSON.

Pour obtenir des conseils sur la migration, consultez [Comment migrer de `Newtonsoft.Json` vers `System.Text.Json` ](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).

### <a name="lazy-load-assemblies"></a>Charger des assemblys en différé

Chargez les assemblys au moment de l’exécution lorsque les assemblys sont requis par un itinéraire. Pour plus d'informations, consultez <xref:blazor/webassembly-lazy-load-assemblies>.

### <a name="compression"></a>Compression

Quand une Blazor WebAssembly application est publiée, la sortie est compressée de manière statique lors de la publication afin de réduire la taille de l’application et de supprimer la surcharge liée à la compression du Runtime. Blazor s’appuie sur le serveur pour effectuer des negotation de contenu et traiter des fichiers compressés statiquement.

Une fois qu’une application a été déployée, vérifiez que l’application dessert des fichiers compressés. Examinez l’onglet réseau dans le Outils de développement d’un navigateur et vérifiez que les fichiers sont pris en charge avec `Content-Encoding: br` ou `Content-Encoding: gz` . Si l’hôte ne dessert pas de fichiers compressés, suivez les instructions de la procédure <xref:blazor/host-and-deploy/webassembly#compression> .

### <a name="disable-unused-features"></a>Désactiver les fonctionnalités inutilisées

Blazor WebAssemblyle runtime de comprend les fonctionnalités .NET suivantes qui peuvent être désactivées si l’application n’en a pas besoin pour une plus petite taille de charge utile :

* Un fichier de données est inclus pour corriger les informations de fuseau horaire. Si l’application n’a pas besoin de cette fonctionnalité, envisagez de la désactiver en définissant la `BlazorEnableTimeZoneSupport` propriété MSBuild dans le fichier projet de l’application sur `false` :

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

::: moniker range=">= aspnetcore-5.0"

* Par défaut, Blazor WebAssembly transporte les ressources de globalisation requises pour afficher des valeurs, telles que les dates et les devises, dans la culture de l’utilisateur. Si l’application ne nécessite pas de localisation, vous pouvez [configurer l’application pour qu’elle prenne en charge la culture dite indifférente](xref:blazor/globalization-localization), qui est basée sur la `en-US` culture :

  ```xml
  <PropertyGroup>
    <InvariantGlobalization>true</InvariantGlobalization>
  </PropertyGroup>
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* Les informations de classement sont incluses pour que les API telles que <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> fonctionnent correctement. Si vous êtes certain que l’application n’a pas besoin des données de classement, envisagez de la désactiver en définissant la `BlazorWebAssemblyPreserveCollationData` propriété MSBuild dans le fichier projet de l’application sur `false` :

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```

::: moniker-end
