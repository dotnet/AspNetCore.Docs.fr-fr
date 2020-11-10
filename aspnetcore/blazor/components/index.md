---
title: Créer et utiliser des Razor composants ASP.net Core
author: guardrex
description: Découvrez comment créer et utiliser des Razor composants, notamment comment lier des données, gérer des événements et gérer des cycles de vie de composant.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/09/2020
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
uid: blazor/components/index
ms.openlocfilehash: 0f02bc3a92b9f62eb0e3efea0cd780ad6d09bef5
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431002"
---
# <a name="create-and-use-aspnet-core-no-locrazor-components"></a>Créer et utiliser des Razor composants ASP.net Core

Par [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)et [Tobias Bartsch](https://www.aveo-solutions.com/)

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

Blazor les applications sont générées à l’aide de *composants*. Un composant est un bloc d’interface utilisateur (IU) autonome, tel qu’une page, une boîte de dialogue ou un formulaire. Un composant comprend le balisage HTML et la logique de traitement requise pour injecter des données ou répondre à des événements d’interface utilisateur. Les composants sont flexibles et légers. Elles peuvent être imbriquées, réutilisées et partagées entre les projets.

## <a name="component-classes"></a>Classes de composant

Les composants sont implémentés dans des [Razor](xref:mvc/views/razor) fichiers composants ( `.razor` ) à l’aide d’une combinaison de balisage C# et html. Un composant dans Blazor est officiellement désigné sous le terme de *Razor composant*.

### <a name="no-locrazor-syntax"></a>Syntaxe de Razor

Razor les composants dans les Blazor applications utilisent largement la Razor syntaxe. Si vous n’êtes pas familiarisé avec le Razor langage de balisage, nous vous recommandons de lire <xref:mvc/views/razor> avant de continuer.

Lorsque vous accédez au contenu sur la Razor syntaxe, portez une attention particulière aux sections suivantes :

* [Directives](xref:mvc/views/razor#directives): `@` -préfixe des mots clés réservés qui modifient généralement la manière dont le balisage du composant est analysé ou fonction.
* [Attributs de directive](xref:mvc/views/razor#directive-attributes): `@` Mots clés réservés préfixés qui modifient généralement le mode d’analyse ou de fonction des éléments de composant.

### <a name="names"></a>Noms

Le nom d’un composant doit commencer par un caractère majuscule. Par exemple, `MyCoolComponent.razor` est valide et `myCoolComponent.razor` n’est pas valide.

### <a name="routing"></a>Routage

Le routage dans Blazor est effectué en fournissant un modèle de routage à chaque composant accessible dans l’application. Lorsqu’un Razor fichier avec une [`@page`][9] directive est compilé, la classe générée reçoit un <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> qui spécifie le modèle de routage. Lors de l’exécution, le routeur recherche les classes de composant avec un <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> et rend le composant qui a un modèle de routage correspondant à l’URL demandée. Pour plus d'informations, consultez <xref:blazor/fundamentals/routing>.

```razor
@page "/ParentComponent"

...
```

### <a name="markup"></a>balisage

L’interface utilisateur d’un composant est définie à l’aide de HTML. La logique de rendu dynamique (par exemple, les boucles, les instructions conditionnelles, les expressions) est ajoutée à l’aide d’une syntaxe C# incorporée appelée *Razor* . Quand une application est compilée, le balisage HTML et la logique de rendu C# sont convertis en une classe de composant. Le nom de la classe générée correspond au nom du fichier.

Les membres de la classe Component sont définis dans un [`@code`][1] bloc. Dans le [`@code`][1] bloc, l’état du composant (propriétés, champs) est spécifié avec des méthodes pour la gestion des événements ou pour la définition d’une autre logique de composant. Plus d’un [`@code`][1] bloc est autorisé.

Les membres de composant peuvent être utilisés dans le cadre de la logique de rendu du composant à l’aide d’expressions C# qui commencent par `@` . Par exemple, un champ C# est rendu en préfixant `@` le nom du champ. L’exemple suivant évalue et affiche :

* `headingFontStyle` à la valeur de propriété CSS pour `font-style` .
* `headingText` au contenu de l' `<h1>` élément.

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

Une fois le composant restitué initialement, le composant régénère son arborescence de rendu en réponse aux événements. Blazor compare ensuite la nouvelle arborescence de rendu à la précédente et applique toutes les modifications apportées au Document Object Model du navigateur (DOM).

Les composants sont des classes C# ordinaires qui peuvent être placées n’importe où dans un projet. Les composants qui produisent des pages Web résident généralement dans le `Pages` dossier. Les composants qui ne sont pas des pages sont souvent placés dans le `Shared` dossier ou dans un dossier personnalisé ajouté au projet.

### <a name="namespaces"></a>Espaces de noms

En règle générale, l’espace de noms d’un composant est dérivé de l’espace de noms racine de l’application et de l’emplacement (dossier) du composant dans l’application. Si l’espace de noms racine de l’application est `BlazorSample` et que le `Counter` composant se trouve dans le `Pages` dossier :

* L' `Counter` espace de noms du composant est `BlazorSample.Pages` .
* Le nom de type qualifié complet du composant est `BlazorSample.Pages.Counter` .

Pour les dossiers personnalisés qui contiennent des composants, ajoutez une [`@using`][2] directive au composant parent ou au fichier de l’application `_Imports.razor` . L’exemple suivant rend les composants du `Components` dossier disponibles :

```razor
@using BlazorSample.Components
```

Les composants peuvent également être référencés à l’aide de leurs noms complets, ce qui ne requiert pas la [`@using`][2] directive :

```razor
<BlazorSample.Components.MyComponent />
```

L’espace de noms d’un composant créé avec Razor est basé sur (par ordre de priorité) :

* [`@namespace`][8] désignation dans le Razor balisage de fichier ( `.razor` ) `@namespace BlazorSample.MyNamespace` .
* Du projet `RootNamespace` dans le fichier projet ( `<RootNamespace>BlazorSample</RootNamespace>` ).
* Nom du projet, pris à partir du nom de fichier du fichier projet ( `.csproj` ) et chemin d’accès de la racine du projet au composant. Par exemple, le Framework résout `{PROJECT ROOT}/Pages/Index.razor` ( `BlazorSample.csproj` ) l’espace de noms `BlazorSample.Pages` . Les composants suivent les règles de liaison de noms C#. Pour le `Index` composant dans cet exemple, les composants de l’étendue sont tous les composants :
  * Dans le même dossier, `Pages` .
  * Composants de la racine du projet qui ne spécifient pas explicitement un espace de noms différent.

> [!NOTE]
> La `global::` qualification n’est pas prise en charge.
>
> L’importation de composants avec des instructions avec alias [`using`](/dotnet/csharp/language-reference/keywords/using-statement) (par exemple, `@using Foo = Bar` ) n’est pas prise en charge.
>
> Les noms partiellement qualifiés ne sont pas pris en charge. Par exemple, `@using BlazorSample` l’ajout et la référencement du `NavMenu` composant ( `NavMenu.razor` ) avec `<Shared.NavMenu></Shared.NavMenu>` ne sont pas pris en charge.

### <a name="partial-class-support"></a>Prise en charge des classes partielles

Razor les composants sont générés en tant que classes partielles. Razor les composants sont créés à l’aide de l’une des approches suivantes :

* Le code C# est défini dans un [`@code`][1] bloc avec le balisage HTML et Razor le code dans un fichier unique. Blazor les modèles définissent leurs Razor composants à l’aide de cette approche.
* Le code C# est placé dans un fichier code-behind défini en tant que classe partielle.

L’exemple suivant montre le composant par défaut `Counter` avec un [`@code`][1] bloc dans une application générée à partir d’un Blazor modèle. Le balisage HTML, le Razor code et le code C# se trouvent dans le même fichier :

`Pages/Counter.razor`:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    void IncrementCount()
    {
        currentCount++;
    }
}
```

Le `Counter` composant peut également être créé à l’aide d’un fichier code-behind avec une classe partielle :

`Pages/Counter.razor`:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

`Counter.razor.cs`:

```csharp
namespace BlazorSample.Pages
{
    public partial class Counter
    {
        private int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
}
```

Ajoutez tous les espaces de noms requis au fichier de classe partielle, si nécessaire. Les espaces de noms standard utilisés par les Razor composants sont les suivants :

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

> [!IMPORTANT]
> [`@using`][2] les directives du `_Imports.razor` fichier ne sont appliquées qu’aux fichiers Razor ( `.razor` ), et non aux fichiers C# ( `.cs` ).

### <a name="specify-a-base-class"></a>Spécifier une classe de base

La [`@inherits`][6] directive peut être utilisée pour spécifier une classe de base pour un composant. L’exemple suivant montre comment un composant peut hériter d’une classe de base, `BlazorRocksBase` , pour fournir les propriétés et les méthodes du composant. La classe de base doit dériver de <xref:Microsoft.AspNetCore.Components.ComponentBase> .

`Pages/BlazorRocks.razor`:

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

`BlazorRocksBase.cs`:

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample
{
    public class BlazorRocksBase : ComponentBase
    {
        public string BlazorRocksText { get; set; } = 
            "Blazor rocks the browser!";
    }
}
```

### <a name="use-components"></a>Utiliser des composants

Les composants peuvent inclure d’autres composants en les déclarant à l’aide de la syntaxe d’élément HTML. Le balisage pour l’utilisation d’un composant ressemble à une balise HTML où le nom de la balise est le type du composant.

Le balisage suivant dans `Pages/Index.razor` rend une `HeadingComponent` instance de :

```razor
<HeadingComponent />
```

`Components/HeadingComponent.razor`:

[!code-razor[](index/samples_snapshot/HeadingComponent.razor)]

Si un composant contient un élément HTML avec une première lettre majuscule qui ne correspond pas à un nom de composant, un avertissement est émis pour indiquer que l’élément a un nom inattendu. L’ajout d’une [`@using`][2] directive pour l’espace de noms du composant rend le composant disponible, ce qui résout l’avertissement.

## <a name="parameters"></a>Paramètres

### <a name="route-parameters"></a>Paramètres d’itinéraire

Les composants peuvent recevoir des paramètres de routage du modèle de routage fourni dans la [`@page`][9] directive. Le routeur utilise des paramètres de routage pour remplir les paramètres de composant correspondants.

`Pages/RouteParameter.razor`:

[!code-razor[](index/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

Les paramètres facultatifs ne sont pas pris en charge [`@page`][9] . deux directives sont donc appliquées dans l’exemple précédent. La première permet de naviguer jusqu’au composant sans paramètre. La deuxième [`@page`][9] directive reçoit le `{text}` paramètre d’itinéraire et assigne la valeur à la `Text` propriété.

Pour plus d’informations sur les paramètres d’itinéraire Catch-All ( `{*pageRoute}` ), qui capturent les chemins d’accès dans plusieurs limites de dossiers, consultez <xref:blazor/fundamentals/routing#catch-all-route-parameters> .

### <a name="component-parameters"></a>Paramètres de composant

Les composants peuvent avoir des *paramètres de composant* , qui sont définis à l’aide de propriétés publiques sur la classe de composant avec l' [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribut. Utilisez des attributs pour spécifier des arguments pour un composant dans le balisage.

`Components/ChildComponent.razor`:

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

Dans l’exemple suivant tiré de l’exemple d’application, le `ParentComponent` définit la valeur de la `Title` propriété de `ChildComponent` .

`Pages/ParentComponent.razor`:

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> Ne créez pas de composants qui écrivent dans leurs propres *paramètres de composant* , utilisez un champ privé à la place. Pour plus d’informations, consultez la section [paramètres remplacés](#overwritten-parameters) .

## <a name="child-content"></a>Contenu enfant

Les composants peuvent définir le contenu d’un autre composant. Le composant d’affectation fournit le contenu entre les balises qui spécifient le composant récepteur.

Dans l’exemple suivant, `ChildComponent` a une `ChildContent` propriété qui représente un <xref:Microsoft.AspNetCore.Components.RenderFragment> , qui représente un segment de l’interface utilisateur à restituer. La valeur de `ChildContent` est positionnée dans le balisage du composant où le contenu doit être rendu. La valeur de `ChildContent` est reçue du composant parent et rendue à l’intérieur du panneau de démarrage `panel-body` .

`Components/ChildComponent.razor`:

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> La propriété qui reçoit le <xref:Microsoft.AspNetCore.Components.RenderFragment> contenu doit être nommée `ChildContent` par Convention.

`ParentComponent`Dans l’exemple d’application, vous pouvez fournir du contenu pour le rendu de `ChildComponent` en plaçant le contenu à l’intérieur des `<ChildComponent>` balises.

`Pages/ParentComponent.razor`:

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=7-8)]

En raison de la façon dont le Blazor contenu enfant est rendu, les composants de rendu à l’intérieur d’une `for` boucle requièrent une variable d’index local si la variable de boucle d’incrémentation est utilisée dans le contenu du composant enfant :
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <ChildComponent Param1="@c">
>         Child Content: Count: @current
>     </ChildComponent>
> }
> ```
>
> Vous pouvez également utiliser une `foreach` boucle avec <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> :
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <ChildComponent Param1="@c">
>         Child Content: Count: @c
>     </ChildComponent>
> }
> ```

## <a name="attribute-splatting-and-arbitrary-parameters"></a>Réprojection d’attribut et paramètres arbitraires

Les composants peuvent capturer et restituer des attributs supplémentaires en plus des paramètres déclarés du composant. Des attributs supplémentaires peuvent être capturés dans un dictionnaire *, puis* réintégrés à un élément lorsque le composant est rendu à l’aide de la [`@attributes`][3] Razor directive. Ce scénario est utile lors de la définition d’un composant qui produit un élément de balisage qui prend en charge diverses personnalisations. Par exemple, il peut être fastidieux de définir des attributs séparément pour un `<input>` qui prend en charge de nombreux paramètres.

Dans l’exemple suivant, le premier `<input>` élément ( `id="useIndividualParams"` ) utilise des paramètres de composant individuels, tandis que le deuxième `<input>` élément ( `id="useAttributesDict"` ) utilise la projection d’attributs :

```razor
<input id="useIndividualParams"
       maxlength="@maxlength"
       placeholder="@placeholder"
       required="@required"
       size="@size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    public string maxlength = "10";
    public string placeholder = "Input placeholder text";
    public string required = "required";
    public string size = "50";

    public Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "required" },
            { "size", "50" }
        };
}
```

Le type du paramètre doit implémenter `IEnumerable<KeyValuePair<string, object>>` ou `IReadOnlyDictionary<string, object>` avec des clés de chaîne.

Les éléments rendus `<input>` à l’aide des deux approches sont identiques :

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

Pour accepter des attributs arbitraires, définissez un paramètre de composant à l’aide de l' [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribut avec la <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> propriété définie sur `true` :

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

La <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> propriété sur [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) permet au paramètre de correspondre à tous les attributs qui ne correspondent à aucun autre paramètre. Un composant ne peut définir qu’un seul paramètre avec <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> . Le type de propriété utilisé avec <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> doit pouvoir être assigné à partir de `Dictionary<string, object>` avec des clés de chaîne. `IEnumerable<KeyValuePair<string, object>>` ou `IReadOnlyDictionary<string, object>` sont également des options dans ce scénario.

La position [`@attributes`][3] relative à la position des attributs d’élément est importante. Quand sont représentées [`@attributes`][3] sur l’élément, les attributs sont traités de droite à gauche (dernier à premier). Prenons l’exemple suivant d’un composant qui consomme un `Child` composant :

`ParentComponent.razor`:

```razor
<ChildComponent extra="10" />
```

`ChildComponent.razor`:

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

L' `Child` attribut du composant `extra` est défini à droite de [`@attributes`][3] . Le `Parent` rendu du composant `<div>` contient `extra="5"` lorsqu’il est transmis via l’attribut supplémentaire, car les attributs sont traités de droite à gauche (dernier à premier) :

```html
<div extra="5" />
```

Dans l’exemple suivant, l’ordre de `extra` et [`@attributes`][3] est inversé dans le d' `Child` un composant `<div>` :

`ParentComponent.razor`:

```razor
<ChildComponent extra="10" />
```

`ChildComponent.razor`:

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

Le rendu `<div>` dans le `Parent` composant contient `extra="10"` lorsqu’il est passé par le biais de l’attribut supplémentaire :

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a>Capturer des références à des composants

Les références de composant offrent un moyen de référencer une instance de composant afin que vous puissiez émettre des commandes vers cette instance, telles que `Show` ou `Reset` . Pour capturer une référence de composant :

* Ajoutez un [`@ref`][4] attribut au composant enfant.
* Définissez un champ avec le même type que le composant enfant.

```razor
<CustomLoginDialog @ref="loginDialog" ... />

@code {
    private CustomLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

Lors du rendu du composant, le `loginDialog` champ est rempli avec l' `MyLoginDialog` instance du composant enfant. Vous pouvez ensuite appeler des méthodes .NET sur l’instance du composant.

> [!IMPORTANT]
> La `loginDialog` variable est remplie uniquement après le rendu du composant et sa sortie comprend l' `MyLoginDialog` élément. Tant que le composant n’est pas rendu, il n’y a rien à référencer.
>
> Pour manipuler des références de composants après la fin du rendu du composant, utilisez les [ `OnAfterRenderAsync` `OnAfterRender` méthodes ou](xref:blazor/components/lifecycle#after-component-render).
>
> Pour utiliser une variable de référence avec un gestionnaire d’événements, utilisez une expression lambda ou assignez le délégué de gestionnaire d’événements dans les [ `OnAfterRenderAsync` `OnAfterRender` méthodes ou](xref:blazor/components/lifecycle#after-component-render). Cela garantit que la variable de référence est assignée avant que le gestionnaire d’événements soit affecté.
>
> ```razor
> <button type="button" 
>     @onclick="@(() => loginDialog.DoSomething())">Do Something</button>
>
> <MyLoginDialog @ref="loginDialog" ... />
>
> @code {
>     private MyLoginDialog loginDialog;
> }
> ```

Pour référencer des composants dans une boucle, consultez [capturer des références à plusieurs composants enfants similaires (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).

Bien que la capture de références de composant utilise une syntaxe similaire pour [capturer des références d’élément](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), il ne s’agit pas d’une fonctionnalité JavaScript Interop. Les références de composant ne sont pas passées au code JavaScript. Les références de composants sont uniquement utilisées dans le code .NET.

> [!NOTE]
> N’utilisez **pas** de références de composant pour muter l’état des composants enfants. Utilisez plutôt des paramètres déclaratifs normaux pour passer des données aux composants enfants. L’utilisation de paramètres déclaratifs normaux entraîne le rerendu automatique des composants enfants.

## <a name="synchronization-context"></a>Contexte de synchronisation

Blazor utilise un contexte de synchronisation ( <xref:System.Threading.SynchronizationContext> ) pour appliquer un seul thread logique d’exécution. Les méthodes de [cycle de vie](xref:blazor/components/lifecycle) d’un composant et les rappels d’événements déclenchés par Blazor sont exécutés sur le contexte de synchronisation.

Blazor Serverle contexte de synchronisation de tente d’émuler un environnement à thread unique afin qu’il corresponde étroitement au modèle webassembly dans le navigateur, qui est monothread. À un moment donné, le travail est effectué sur un seul thread, ce qui donne l’impression d’un seul thread logique. Deux opérations ne sont pas exécutées simultanément.

### <a name="avoid-thread-blocking-calls"></a>Éviter les appels de blocage de thread

En règle générale, n’appelez pas les méthodes suivantes. Les méthodes suivantes bloquent le thread et empêchent donc l’application de reprendre le travail jusqu’à ce que le sous-jacent <xref:System.Threading.Tasks.Task> soit terminé :

* <xref:System.Threading.Tasks.Task%601.Result%2A>
* <xref:System.Threading.Tasks.Task.Wait%2A>
* <xref:System.Threading.Tasks.Task.WaitAny%2A>
* <xref:System.Threading.Tasks.Task.WaitAll%2A>
* <xref:System.Threading.Thread.Sleep%2A>
* <xref:System.Runtime.CompilerServices.TaskAwaiter.GetResult%2A>

### <a name="invoke-component-methods-externally-to-update-state"></a>Appeler des méthodes de composant en externe pour mettre à jour l’État

Dans le cas où un composant doit être mis à jour en fonction d’un événement externe, tel qu’un minuteur ou d’autres notifications, utilisez la `InvokeAsync` méthode, qui est distribuée au Blazor contexte de synchronisation de. Par exemple, considérez un *service de notification* qui peut notifier n’importe quel composant d’écoute de l’État mis à jour :

```csharp
public class NotifierService
{
    // Can be called from anywhere
    public async Task Update(string key, int value)
    {
        if (Notify != null)
        {
            await Notify.Invoke(key, value);
        }
    }

    public event Func<string, int, Task> Notify;
}
```

Inscrire `NotifierService` :

* Dans Blazor WebAssembly , inscrivez le service comme Singleton dans `Program.Main` :

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* Dans Blazor Server , inscrivez le service comme étant étendu dans `Startup.ConfigureServices` :

  ```csharp
  services.AddScoped<NotifierService>();
  ```

Utilisez `NotifierService` pour mettre à jour un composant :

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @lastNotification.key = @lastNotification.value</p>

@code {
    private (string key, int value) lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

Dans l’exemple précédent, `NotifierService` appelle la méthode du composant `OnNotify` en dehors du Blazor contexte de synchronisation de. `InvokeAsync` est utilisé pour basculer vers le contexte correct et pour la mise en file d’attente d’un rendu.

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a>Utiliser \@ la clé pour contrôler la conservation des éléments et des composants

Lors du rendu d’une liste d’éléments ou de composants, et si les éléments ou les composants changent par la suite, l' Blazor algorithme de différenciation de doit décider quels éléments ou composants précédents peuvent être conservés et comment les objets de modèle doivent être mappés à eux. Normalement, ce processus est automatique et peut être ignoré, mais dans certains cas, il peut être utile de contrôler le processus.

Prenons l’exemple suivant :

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

Le contenu de la `People` collection peut changer avec des entrées insérées, supprimées ou réorganisées. Lors du rerendu du composant, le `<DetailsEditor>` composant peut changer pour recevoir des `Details` valeurs de paramètre différentes. Cela peut entraîner un rerendu plus complexe que prévu. Dans certains cas, le rerendu peut entraîner des différences de comportement visibles, telles que le focus de l’élément perdu.

Le processus de mappage peut être contrôlé à l’aide de l' [`@key`][5] attribut directive. [`@key`][5] force l’algorithme de comparaison à garantir la préservation des éléments ou des composants en fonction de la valeur de la clé :

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

Lorsque la `People` collection est modifiée, l’algorithme de comparaison conserve l’association entre les `<DetailsEditor>` instances et les `person` instances :

* Si un `Person` est supprimé de la `People` liste, seule l' `<DetailsEditor>` instance correspondante est supprimée de l’interface utilisateur. Les autres instances restent inchangées.
* Si un `Person` est inséré à une position dans la liste, une nouvelle `<DetailsEditor>` instance est insérée à cette position correspondante. Les autres instances restent inchangées.
* Si `Person` les entrées sont réordonnées, les `<DetailsEditor>` instances correspondantes sont conservées et réordonnées dans l’interface utilisateur.

Dans certains scénarios, l’utilisation de [`@key`][5] réduit la complexité du rerendu et évite les problèmes potentiels liés aux parties avec état de la modification du modèle DOM, telles que la position du focus.

> [!IMPORTANT]
> Les clés sont locales pour chaque élément ou composant conteneur. Les clés ne sont pas comparées globalement dans le document.

### <a name="when-to-use-key"></a>Quand utiliser la \@ clé

En règle générale, il est judicieux d’utiliser [`@key`][5] chaque fois qu’une liste est affichée (par exemple, dans un bloc [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) ) et qu’une valeur appropriée existe pour définir [`@key`][5] .

Vous pouvez également utiliser [`@key`][5] pour empêcher Blazor de conserver un élément ou une sous-arborescence de composants lorsqu’un objet change :

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

En cas `@currentPerson` de modification, la [`@key`][5] directive Blazor d’attribut force à ignorer la totalité `<div>` et ses descendants, et à reconstruire la sous-arborescence de l’interface utilisateur avec de nouveaux éléments et composants. Cela peut être utile si vous devez garantir qu’aucun État d’interface utilisateur n’est préservé lorsque des `@currentPerson` modifications sont apportées.

### <a name="when-not-to-use-key"></a>Quand ne pas utiliser la \@ clé

Il y a un coût en matière de performances lors de la comparaison avec [`@key`][5] . Le coût des performances n’est pas important, mais spécifiez uniquement [`@key`][5] si les règles de conservation des éléments ou des composants bénéficient de l’application.

Même si [`@key`][5] n’est pas utilisé, Blazor conserve autant que possible les instances d’élément et de composant enfants. Le seul avantage de l’utilisation de [`@key`][5] est de contrôler la *façon dont* les instances de modèle sont mappées aux instances de composant conservées, au lieu de l’algorithme de comparaison qui sélectionne le mappage.

### <a name="what-values-to-use-for-key"></a>Valeurs à utiliser pour la \@ clé

En règle générale, il est logique de fournir l’un des types de valeur suivants pour [`@key`][5] :

* Instances d’objet de modèle (par exemple, une `Person` instance comme dans l’exemple précédent). Cela garantit la préservation en fonction de l’égalité des références d’objet.
* Identificateurs uniques (par exemple, les valeurs de clé primaire de type `int` , `string` ou `Guid` ).

Vérifiez que les valeurs utilisées pour [`@key`][5] ne sont pas en conflit. Si les valeurs en conflit sont détectées dans le même élément parent, Blazor lève une exception, car il ne peut pas mapper de manière déterministe les anciens éléments ou composants aux nouveaux éléments ou composants. Utilisez uniquement des valeurs distinctes, telles que des instances d’objets ou des valeurs de clé primaire.

## <a name="overwritten-parameters"></a>Paramètres remplacés

L' Blazor infrastructure impose généralement une attribution de paramètres parent-enfant sécurisée :

* Les paramètres ne sont pas remplacés de manière inattendue.
* Les effets secondaires sont réduits. Par exemple, les rendus supplémentaires sont évités, car ils peuvent créer des boucles de rendu infinies.

Un composant enfant reçoit de nouvelles valeurs de paramètre qui, éventuellement, remplacent les valeurs existantes lors du rerendu du composant parent. Accidentially remplacement des valeurs de paramètre dans un composant enfant se produit souvent lors du développement du composant avec un ou plusieurs paramètres liés aux données et le développeur écrit directement dans un paramètre de l’enfant :

* Le composant enfant est restitué avec une ou plusieurs valeurs de paramètre à partir du composant parent.
* L’enfant écrit directement dans la valeur d’un paramètre.
* Le composant parent effectue un rerendu et remplace la valeur du paramètre de l’enfant.

Le potentiel de remplacement des valeurs de paramètre s’étend également dans les accesseurs set de propriété du composant enfant.

**Nos conseils généraux ne permettent pas de créer des composants qui écrivent directement dans leurs propres paramètres.**

Prenons l’exemple du composant défaillant suivant `Expander` :

* Restitue le contenu enfant.
* Active ou désactive l’indication du contenu enfant avec un paramètre de composant ( `Expanded` ).
* Le composant écrit directement dans le `Expanded` paramètre, qui illustre le problème avec les paramètres remplacés et doit être évité.

```razor
<div @onclick="@Toggle" class="card bg-light mb-3" style="width:30rem">
    <div class="card-body">
        <h2 class="card-title">Toggle (<code>Expanded</code> = @Expanded)</h2>

        @if (Expanded)
        {
            <p class="card-text">@ChildContent</p>
        }
    </div>
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void Toggle()
    {
        Expanded = !Expanded;
    }
}
```

Le `Expander` composant est ajouté à un composant parent qui peut appeler <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> :

```razor
@page "/expander"

<Expander Expanded="true">
    Expander 1 content
</Expander>

<Expander Expanded="true" />

<button @onclick="StateHasChanged">
    Call StateHasChanged
</button>
```

Au départ, les `Expander` composants se comportent indépendamment lorsque leurs `Expanded` propriétés sont basculées. Les composants enfants maintiennent leurs États comme prévu. Lorsque <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> est appelé dans le parent, le `Expanded` paramètre du premier composant enfant est réinitialisé à sa valeur initiale ( `true` ). La `Expander` valeur du deuxième composant `Expanded` n’est pas réinitialisée, car aucun contenu enfant n’est restitué dans le deuxième composant.

Pour maintenir l’État dans le scénario précédent, utilisez un *champ privé* dans le `Expander` composant pour maintenir son état bascule.

Le composant révisé suivant `Expander` :

* Accepte la `Expanded` valeur de paramètre du composant à partir du parent.
* Affecte la valeur du paramètre de composant à un *champ privé* ( `expanded` ) dans l' [événement OnInitialized](xref:blazor/components/lifecycle#component-initialization-methods).
* Utilise le champ privé pour conserver son état bascule interne, qui montre comment éviter d’écrire directement dans un paramètre.

```razor
<div @onclick="@Toggle" class="card bg-light mb-3" style="width:30rem">
    <div class="card-body">
        <h2 class="card-title">Toggle (<code>expanded</code> = @expanded)</h2>

        @if (expanded)
        {
            <p class="card-text">@ChildContent</p>
        }
    </div>
</div>

@code {
    private bool expanded;

    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    protected override void OnInitialized()
    {
        expanded = Expanded;
    }

    private void Toggle()
    {
        expanded = !expanded;
    }
}
```

Pour plus d’informations, consultez [ Blazor erreur de liaison bidirectionnelle (dotnet/aspnetcore #24599)](https://github.com/dotnet/aspnetcore/issues/24599). 

## <a name="apply-an-attribute"></a>Appliquer un attribut

Les attributs peuvent être appliqués aux Razor composants avec la [`@attribute`][7] directive. L’exemple suivant applique l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut à la classe de composant :

```razor
@page "/"
@attribute [Authorize]
```

## <a name="conditional-html-element-attributes"></a>Attributs d’éléments HTML conditionnels

Les attributs des éléments HTML sont rendus de manière conditionnelle en fonction de la valeur .NET. Si la valeur est `false` ou `null` , l’attribut n’est pas rendu. Si la valeur est `true` , l’attribut est rendu réduit.

Dans l’exemple suivant, `IsCompleted` détermine si `checked` est rendu dans le balisage de l’élément :

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

Si `IsCompleted` est `true` , la case à cocher s’affiche comme suit :

```html
<input type="checkbox" checked />
```

Si `IsCompleted` est `false` , la case à cocher s’affiche comme suit :

```html
<input type="checkbox" />
```

Pour plus d'informations, consultez <xref:mvc/views/razor>.

> [!WARNING]
> Certains attributs HTML, tels que [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons) , ne fonctionnent pas correctement quand le type .net est un `bool` . Dans ce cas, utilisez un `string` type au lieu d’un `bool` .

## <a name="raw-html"></a>HTML brut

Les chaînes sont normalement rendues à l’aide de nœuds de texte DOM, ce qui signifie que tout balisage qu’elles peuvent contenir est ignorée et traitée comme du texte littéral. Pour afficher le code HTML brut, encapsulez le contenu HTML dans une `MarkupString` valeur. La valeur est analysée au format HTML ou SVG et est insérée dans le DOM.

> [!WARNING]
> Le rendu de code HTML brut construit à partir de n’importe quelle source non approuvée constitue un risque pour la **sécurité** et doit être évité !

L’exemple suivant illustre l’utilisation du `MarkupString` type pour ajouter un bloc de contenu HTML statique à la sortie rendue d’un composant :

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="no-locrazor-templates"></a>Razor ceux

Les fragments de rendu peuvent être définis à l’aide de la Razor syntaxe de modèle. Razor les modèles sont un moyen de définir un extrait de code d’interface utilisateur et de supposer le format suivant :

```razor
@<{HTML tag}>...</{HTML tag}>
```

L’exemple suivant montre comment spécifier <xref:Microsoft.AspNetCore.Components.RenderFragment> des valeurs et <xref:Microsoft.AspNetCore.Components.RenderFragment%601> et restituer des modèles directement dans un composant. Les fragments de rendu peuvent également être passés comme arguments à des [composants basés](xref:blazor/components/templated-components)sur un modèle.

```razor
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

Sortie rendue du code précédent :

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="static-assets"></a>Les ressources statiques

Blazorrespecte la Convention de ASP.NET Core les applications qui placent des ressources statiques dans le [ `web root (wwwroot)` dossier](xref:fundamentals/index#web-root)du projet.

Utilisez un chemin d’accès relatif à `/` la base () pour faire référence à la racine Web d’une ressource statique. Dans l’exemple suivant, `logo.png` se trouve physiquement dans le `{PROJECT ROOT}/wwwroot/images` dossier :

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Razor les composants ne prennent **pas** en charge la notation tilde-slash ( `~/` ).

Pour plus d’informations sur la définition du chemin d’accès de base d’une application, consultez <xref:blazor/host-and-deploy/index#app-base-path> .

## <a name="tag-helpers-arent-supported-in-components"></a>Les balises tag Helper ne sont pas prises en charge dans les composants

[`Tag Helpers`](xref:mvc/views/tag-helpers/intro) ne sont pas pris en charge dans les Razor composants ( `.razor` fichiers). Pour fournir des fonctionnalités semblables à tag Helper dans Blazor , créez un composant avec les mêmes fonctionnalités que le tag Helper et utilisez le composant à la place.

## <a name="scalable-vector-graphics-svg-images"></a>Images SVG (Scalable Vector Graphics)

Étant donné que Blazor le rendu des images HTML, prises en charge par le navigateur, y compris les images SVG (Scalable Vector Graphics) ( `.svg` ), est pris en charge via la `<img>` balise :

```html
<img alt="Example image" src="some-image.svg" />
```

De même, les images SVG sont prises en charge dans les règles CSS d’un fichier de feuille de style ( `.css` ) :

```css
.my-element {
    background-image: url("some-image.svg");
}
```

Toutefois, le balisage SVG en ligne n’est pas pris en charge dans tous les scénarios. Si vous placez une `<svg>` balise directement dans un fichier de composant ( `.razor` ), le rendu d’image de base est pris en charge, mais de nombreux scénarios avancés ne sont pas encore pris en charge. Par exemple, les `<use>` balises ne sont pas actuellement respectées et [`@bind`][10] ne peuvent pas être utilisées avec certaines balises SVG. Pour plus d’informations, consultez [prise en charge SVG dans Blazor (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:blazor/security/server/threat-mitigation>: Fournit des conseils sur la création d' Blazor Server applications qui doivent être en concurrence avec l’épuisement des ressources.

<!--Reference links in article-->
[1]: <xref:mvc/views/razor#code>
[2]: <xref:mvc/views/razor#using>
[3]: <xref:mvc/views/razor#attributes>
[4]: <xref:mvc/views/razor#ref>
[5]: <xref:mvc/views/razor#key>
[6]: <xref:mvc/views/razor#inherits>
[7]: <xref:mvc/views/razor#attribute>
[8]: <xref:mvc/views/razor#namespace>
[9]: <xref:mvc/views/razor#page>
[10]: <xref:mvc/views/razor#bind>
