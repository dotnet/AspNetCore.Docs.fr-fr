---
title: informations de référence sur la syntaxe Razor pour ASP.NET Core
author: rick-anderson
description: En savoir plus sur la Razor syntaxe de balisage pour incorporer du code serveur dans des pages Web.
ms.author: riande
ms.date: 02/12/2020
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
uid: mvc/views/razor
ms.openlocfilehash: 60471232b3373039404b27c4afd1a1725d4d21eb
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586863"
---
# <a name="razor-syntax-reference-for-aspnet-core"></a>Razor Référence de syntaxe pour ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen)et [Dan Vicarel](https://github.com/Rabadash8820)

Razor est une syntaxe de balisage pour incorporer du code basé sur le serveur dans des pages Web. La Razor syntaxe se compose du Razor balisage, de C# et du code html. Les fichiers contenant Razor ont généralement une extension de fichier *. cshtml* . Razorse trouve également dans les fichiers de [ Razor composants](xref:blazor/components/index) (*. Razor*).

## <a name="rendering-html"></a>Rendu HTML

La langue par défaut Razor est html. Le rendu HTML à partir du Razor balisage n’est pas différent du rendu HTML d’un fichier html. Le balisage HTML dans les fichiers *. cshtml* Razor est rendu par le serveur inchangé.

## <a name="razor-syntax"></a>Syntaxe de Razor

Razor prend en charge C# et utilise le `@` symbole pour passer du code HTML à c#. Razor évalue les expressions C# et les restitue dans la sortie HTML.

Quand un `@` symbole est suivi d’un [ Razor mot clé réservé](#razor-reserved-keywords), il passe à un Razor balisage spécifique. Sinon, il est converti en code C# brut.

Pour échapper un `@` symbole dans Razor le balisage, utilisez un deuxième `@` symbole :

```cshtml
<p>@@Username</p>
```

Le code est affiché en HTML avec un seul symbole `@` :

```html
<p>@Username</p>
```

Les attributs et le code HTML contenant des adresses e-mail ne traitent pas le symbole `@` comme un caractère de conversion. Les adresses de messagerie de l’exemple suivant ne sont pas touchées par l' Razor analyse :

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a>Expressions implicites Razor

RazorLes expressions implicites commencent par `@` suivies du code C# :

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

À l’exception du mot clé `await` C#, les expressions implicites ne doivent pas contenir d’espaces. Si l’instruction C# se termine de façon non ambigüe, il est possible d’insérer des espaces n’importe où dans la chaîne :

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

Les expressions implicites **ne doivent pas** contenir de caractères génériques C#, car les caractères entre crochets (`<>`) sont interprétés comme une balise HTML. Le code suivant n’est **pas** valide :

```cshtml
<p>@GenericMethod<int>()</p>
```

Le code précédent génère l’un des types d’erreur de compilateur suivants :

* L’élément « int » n’a pas été fermé. Tous les éléments doivent se fermer automatiquement ou contenir une balise de fin correspondante.
* Impossible de convertir le groupe de méthodes 'GenericMethod' en type non-délégué 'object'. Souhaitiez-vous appeler la méthode ?

Les appels de méthode génériques doivent être encapsulés dans une [ Razor expression explicite](#explicit-razor-expressions) ou un [ Razor bloc de code](#razor-code-blocks).

## <a name="explicit-razor-expressions"></a>Expressions explicites Razor

RazorLes expressions explicites se composent d’un `@` symbole avec des parenthèses équilibrées. Pour afficher l’heure de la semaine dernière, le Razor balisage suivant est utilisé :

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

Le contenu situé entre les parenthèses `@()` est évalué et affiché dans la sortie.

Les expressions implicites, décrites dans la section précédente, ne doivent généralement pas contenir d’espaces. Dans le code suivant, une semaine n’est pas déduite de l’heure actuelle :

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

Le code s’affiche en HTML de la façon suivante :

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

Les expressions explicites peuvent servir à concaténer du texte avec un résultat d’expression :

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

Sans l’expression explicite, `<p>Age@joe.Age</p>` est traité comme une adresse e-mail, et `<p>Age@joe.Age</p>` est affiché. Avec une expression explicite, `<p>Age33</p>` est affiché.

Les expressions explicites peuvent être utilisées pour afficher la sortie de méthodes génériques dans les fichiers *.cshtml*. Le balisage suivant montre comment corriger l’erreur affichée précédemment provoquée par les crochets d’un générique C#. Le code est écrit sous forme d’expression explicite :

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a>Encodage des expressions

Les expressions C# évaluées qui correspondent à une chaîne sont encodées en HTML. Les expressions C# évaluées qui correspondent à `IHtmlContent` sont affichées directement par `IHtmlContent.WriteTo`. Les expressions C# évaluées qui ne correspondent pas à `IHtmlContent` sont converties en chaîne par `ToString` et sont encodées avant d’être affichées.

```cshtml
@("<span>Hello World</span>")
```

Le code précédent génère le code HTML suivant :

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

Le code HTML est affiché sous forme de texte brut dans le navigateur :

&lt;span &gt; Hello World &lt; /span&gt;

La sortie `HtmlHelper.Raw` n’est pas encodée, mais elle est affichée sous forme de balisage HTML.

> [!WARNING]
> Utiliser `HtmlHelper.Raw` sur des entrées utilisateur non nettoyées présente un risque pour la sécurité. Les entrées utilisateur peuvent contenir du code malveillant JavaScript ou d’un autre type. Le nettoyage des entrées utilisateur est difficile. C’est pourquoi il est préférable de ne pas utiliser `HtmlHelper.Raw` sur des entrées utilisateur.

```cshtml
@Html.Raw("<span>Hello World</span>")
```

Le code s’affiche en HTML de la façon suivante :

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a>Razor blocs de code

Razor les blocs de code commencent par `@` et sont encadrés par `{}` . Contrairement aux expressions, le code C# figurant dans des blocs de code n’est pas affiché. Les blocs de code et les expressions dans une vue ont la même étendue et sont définis dans l’ordre :

```cshtml
@{
    var quote = "The future depends on what you do today. - Mahatma Gandhi";
}

<p>@quote</p>

@{
    quote = "Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.";
}

<p>@quote</p>
```

Le code s’affiche en HTML de la façon suivante :

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

Dans des blocs de code, déclarez des [fonctions locales](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) avec des balises servant de méthodes de création de modèles :

```cshtml
@{
    void RenderName(string name)
    {
        <p>Name: <strong>@name</strong></p>
    }

    RenderName("Mahatma Gandhi");
    RenderName("Martin Luther King, Jr.");
}
```

Le code s’affiche en HTML de la façon suivante :

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a>Transitions implicites

La langue par défaut dans un bloc de code est C#, mais la Razor page peut passer au format HTML :

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a>Conversion délimitée explicite

Pour définir une sous-section d’un bloc de code qui doit restituer du code HTML, entourez les caractères du rendu à l’aide de la Razor `<text>` balise :

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

Utilisez cette approche pour afficher du code HTML qui n’est pas entouré d’une balise HTML. Sans code HTML ou Razor balise, une Razor erreur d’exécution se produit.

La balise `<text>` est utile pour contrôler les espaces blancs dans le contenu affiché :

* Seul le contenu situé dans la balise `<text>` est affiché.
* Aucun espace blanc avant ou après la balise `<text>` ne s’affiche dans la sortie HTML.

### <a name="explicit-line-transition"></a>Transition de ligne explicite

Pour afficher le reste d’une ligne entière au format HTML à l’intérieur d’un bloc de code, utilisez la `@:` syntaxe suivante :

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

Sans le `@:` dans le code, une Razor erreur d’exécution est générée.

`@`Les caractères supplémentaires d’un Razor fichier peuvent provoquer des erreurs du compilateur dans des instructions plus loin dans le bloc. Ces erreurs du compilateur peuvent être difficiles à comprendre, car elles se produisent en réalité avant les erreurs signalées. Ce type d’erreur est courant après la combinaison de plusieurs expressions implicites ou explicites dans un même bloc de code.

## <a name="control-structures"></a>Structures de contrôle

Les structures de contrôle sont une extension des blocs de code. Toutes les caractéristiques des blocs de code (conversion de balisage, Inline C#) valent aussi pour les structures suivantes :

### <a name="conditionals-if-else-if-else-and-switch"></a>Conditions `@if, else if, else, and @switch`

`@if` contrôle l’exécution du code :

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

`else` et `else if` ne nécessitent pas le symbole `@` :

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
else if (value >= 1337)
{
    <p>The value is large.</p>
}
else
{
    <p>The value is odd and small.</p>
}
```

Le balisage suivant montre comment utiliser une instruction switch :

```cshtml
@switch (value)
{
    case 1:
        <p>The value is 1!</p>
        break;
    case 1337:
        <p>Your number is 1337!</p>
        break;
    default:
        <p>Your number wasn't 1 or 1337.</p>
        break;
}
```

### <a name="looping-for-foreach-while-and-do-while"></a>Bouclage `@for, @foreach, @while, and @do while`

Le rendu HTML peut être réalisé à partir d'instructions de contrôle de boucle. Pour afficher une liste de personnes :

```cshtml
@{
    var people = new Person[]
    {
          new Person("Weston", 33),
          new Person("Johnathon", 41),
          ...
    };
}
```

Les instructions de boucle suivantes sont prises en charge :

`@for`

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@foreach`

```cshtml
@foreach (var person in people)
{
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@while`

```cshtml
@{ var i = 0; }
@while (i < people.Length)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
}
```

`@do while`

```cshtml
@{ var i = 0; }
@do
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
} while (i < people.Length);
```

### <a name="compound-using"></a>Instruction composée `@using`

En C#, une instruction `using` est utilisée pour garantir la dispose d’un objet. Dans Razor , le même mécanisme est utilisé pour créer des applications auxiliaires HTML qui contiennent du contenu supplémentaire. Dans le code suivant, les HTML Helpers affichent une balise `<form>` à l’aide de l’instruction `@using` :

```cshtml
@using (Html.BeginForm())
{
    <div>
        Email: <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

### `@try, catch, finally`

La gestion des exceptions est similaire à C# :

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### `@lock`

Razor a la possibilité de protéger des sections critiques avec des instructions Lock :

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a>Commentaires

Razor prend en charge les commentaires HTML et C# :

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

Le code s’affiche en HTML de la façon suivante :

```html
<!-- HTML comment -->
```

Razor Les commentaires sont supprimés par le serveur avant le rendu de la page Web. Razor utilise `@*  *@` pour délimiter les commentaires. Le code suivant est commenté pour indiquer au serveur de ne pas afficher le balisage :

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a>Directives

Razor les directives sont représentées par des expressions implicites avec des mots clés réservés après le `@` symbole. En règle générale, une directive change la manière dont une vue est analysée ou active une fonctionnalité différente.

Comprendre comment Razor génère du code pour une vue facilite la compréhension du fonctionnement des directives.

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

Le code génère une classe semblable à celle-ci :

```csharp
public class _Views_Something_cshtml : RazorPage<dynamic>
{
    public override async Task ExecuteAsync()
    {
        var output = "Getting old ain't for wimps! - Anonymous";

        WriteLiteral("/r/n<div>Quote of the Day: ");
        Write(output);
        WriteLiteral("</div>");
    }
}
```

Plus loin dans cet article, la section [inspecter la Razor classe C# générée pour une vue](#inspect-the-razor-c-class-generated-for-a-view) explique comment afficher cette classe générée.

### `@attribute`

La directive `@attribute` permet d’ajouter l’attribut donné à la classe de la page ou de la vue générée. L’exemple suivant ajoute l’attribut `[Authorize]` :

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### `@code`

*Ce scénario s’applique uniquement aux Razor composants (. Razor).*

Le `@code` bloc permet à un [ Razor composant](xref:blazor/components/index) d’ajouter des membres C# (champs, propriétés et méthodes) à un composant :

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

Pour les Razor composants, `@code` est un alias de [`@functions`](#functions) et est recommandé sur `@functions` . Plus d’un bloc `@code` est autorisé.

::: moniker-end

### `@functions`

La directive `@functions` permet d’ajouter des membres C# (champs, propriétés et méthodes) à la classe générée :

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

Dans [ Razor composants](xref:blazor/components/index), utilisez `@code` sur `@functions` pour ajouter des membres C#.

::: moniker-end

Par exemple :

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

Le code génère le balisage HTML suivant :

```html
<div>From method: Hello</div>
```

Le code suivant est la Razor classe C# générée :

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

Les méthodes `@functions` servent de méthodes de création de modèles lorsqu’elles comprennent des balises :

```cshtml
@{
    RenderName("Mahatma Gandhi");
    RenderName("Martin Luther King, Jr.");
}

@functions {
    private void RenderName(string name)
    {
        <p>Name: <strong>@name</strong></p>
    }
}
```

Le code s’affiche en HTML de la façon suivante :

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### `@implements`

La directive `@implements` implémente une interface pour la classe générée.

L’exemple suivant implémente <xref:System.IDisposable?displayProperty=fullName> afin que la méthode <xref:System.IDisposable.Dispose*> puisse être appelée :

```cshtml
@implements IDisposable

<h1>Example</h1>

@functions {
    private bool _isDisposed;

    ...

    public void Dispose() => _isDisposed = true;
}
```

::: moniker-end

### `@inherits`

La directive `@inherits` fournit un contrôle complet de la classe héritée par la vue :

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

Le code suivant est un Razor type de page personnalisé :

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

Le `CustomText` s’affiche dans une vue :

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

Le code s’affiche en HTML de la façon suivante :

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 `@model` et `@inherits` peuvent s’utiliser dans la même vue. `@inherits` peut être dans un fichier *_ViewImports.cshtml* importé par la vue :

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

Le code suivant est un exemple de vue fortement typée :

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

Si « rick@contoso.com » est passé au modèle, la vue génère le balisage HTML suivant :

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### `@inject`

La `@inject` directive permet Razor à la page d’injecter un service à partir du [conteneur de service](xref:fundamentals/dependency-injection) dans une vue. Pour plus d’informations, consultez [Injection de dépendances dans les vues](xref:mvc/views/dependency-injection).

::: moniker range=">= aspnetcore-3.0"

### `@layout`

*Ce scénario s’applique uniquement aux Razor composants (. Razor).*

La `@layout` directive spécifie une disposition pour les Razor composants routables qui ont une [`@page`](#page) directive. Les composants de disposition sont utilisés pour éviter la duplication et l’incohérence de code. Pour plus d’informations, consultez <xref:blazor/layouts>.

::: moniker-end

### `@model`

*Ce scénario s’applique uniquement aux affichages et Razor pages MVC (. cshtml).*

La directive `@model` spécifie le type du modèle passé à une vue ou une page :

```cshtml
@model TypeNameOfModel
```

Dans une application ASP.NET Core MVC ou Razor pages créée avec des comptes d’utilisateur individuels, *views/Account/login. cshtml* contient la déclaration de modèle suivante :

```cshtml
@model LoginViewModel
```

La classe générée hérite de `RazorPage<dynamic>` :

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

Razor expose une `Model` propriété pour accéder au modèle passé à la vue :

```cshtml
<div>The Login Email: @Model.Email</div>
```

La directive `@model` spécifie le type de la propriété `Model`. La directive spécifie le type `T` dans `RazorPage<T>` pour la classe générée dont dérive la vue. Si la directive `@model` n’est pas spécifiée, la propriété `Model` est de type `dynamic`. Pour plus d’informations, consultez [modèles fortement typés et le @model mot clé](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).

### `@namespace`

La directive `@namespace` :

* Définit l’espace de noms de la classe de la page générée, de la Razor vue MVC ou du Razor composant.
* Définit les espaces de noms dérivés racine d’une classe de pages, de vues ou de composants à partir du fichier d’importations le plus proche dans l’arborescence de répertoires, *_ViewImports. cshtml* (vues ou pages) ou *_Imports. Razor* ( Razor composants).

```cshtml
@namespace Your.Namespace.Here
```

Pour l' Razor exemple de pages illustré dans le tableau suivant :

* Chaque page importe *Pages/_ViewImports.cshtml*.
* *Pages/_ViewImports.cshtml* contient `@namespace Hello.World`.
* Chaque page a `Hello.World` comme racine de son espace de noms.

| Page                                        | Espace de noms                             |
| ------------------------------------------- | ------------------------------------- |
| *Pages/Index.cshtml*                        | `Hello.World`                         |
| *Pages/MorePages/Page.cshtml*               | `Hello.World.MorePages`               |
| *Pages/MorePages/EvenMorePages/Page.cshtml* | `Hello.World.MorePages.EvenMorePages` |

Les relations précédentes s’appliquent aux fichiers d’importation utilisés avec les vues et les Razor composants Mvc.

Lorsque plusieurs fichiers d’importation ont une directive `@namespace`, le fichier le plus proche de la page, de la vue ou du composant dans l’arborescence de répertoires est utilisé pour définir l’espace de noms racine.

Si le dossier *EvenMorePages* dans l’exemple précédent comprend un fichier d’importations avec `@namespace Another.Planet` (ou si le fichier *Pages/MorePages/EvenMorePages/Page.cshtml* contient `@namespace Another.Planet`), le résultat est indiqué dans le tableau suivant.

| Page                                        | Espace de noms               |
| ------------------------------------------- | ----------------------- |
| *Pages/Index.cshtml*                        | `Hello.World`           |
| *Pages/MorePages/Page.cshtml*               | `Hello.World.MorePages` |
| *Pages/MorePages/EvenMorePages/Page.cshtml* | `Another.Planet`        |

### `@page`

::: moniker range=">= aspnetcore-3.0"

La directive `@page` a des effets différents selon le type du fichier dans lequel elle apparaît. La directive :

* Dans un fichier *. cshtml* indique que le fichier est une Razor page. Pour plus d’informations, consultez [itinéraires personnalisés](xref:razor-pages/index#custom-routes) et <xref:razor-pages/index> .
* Spécifie qu’un Razor composant doit gérer les demandes directement. Pour plus d’informations, consultez <xref:blazor/fundamentals/routing>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

La `@page` directive sur la première ligne d’un fichier *. cshtml* indique que le fichier est une Razor page. Pour plus d’informations, consultez <xref:razor-pages/index>.

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

### `@preservewhitespace`

*Ce scénario s’applique uniquement aux Razor composants ( `.razor` ).*

Quand la valeur `false` est (valeur par défaut), l’espace blanc dans le balisage rendu à partir des Razor composants ( `.razor` ) est supprimé si :

* À gauche ou à droite dans un élément.
* De début ou de fin dans un `RenderFragment` paramètre. Par exemple, le contenu enfant est passé à un autre composant.
* Il précède ou suit un bloc de code C#, tel que `@if` ou `@foreach` .

::: moniker-end

### `@section`

*Ce scénario s’applique uniquement aux affichages et Razor pages MVC (. cshtml).*

La `@section` directive est utilisée conjointement avec [MVC et les Razor mises en page de pages](xref:mvc/views/layout) pour permettre aux vues ou aux pages de restituer le contenu dans différentes parties de la page html. Pour plus d’informations, consultez <xref:mvc/views/layout>.

### `@using`

La directive `@using` ajoute la directive `using` C# à la vue générée :

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

Dans [ Razor composants](xref:blazor/components/index), `@using` contrôle également les composants qui sont dans la portée.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a>Attributs de directive

Razor les attributs de directive sont représentés par des expressions implicites avec des mots clés réservés qui suivent le `@` symbole. Un attribut de directive change généralement la manière dont un élément est analysé ou active des fonctionnalités différentes.

### `@attributes`

*Ce scénario s’applique uniquement aux Razor composants (. Razor).*

`@attributes` permet à un composant de restituer des attributs non déclarés. Pour plus d’informations, consultez <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.

### `@bind`

*Ce scénario s’applique uniquement aux Razor composants (. Razor).*

La liaison de données dans des composants s’effectue avec l’attribut `@bind`. Pour plus d’informations, consultez <xref:blazor/components/data-binding>.

### `@on{EVENT}`

*Ce scénario s’applique uniquement aux Razor composants (. Razor).*

Razor fournit des fonctionnalités de gestion des événements pour les composants. Pour plus d’informations, consultez <xref:blazor/components/event-handling>.

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### `@on{EVENT}:preventDefault`

*Ce scénario s’applique uniquement aux Razor composants (. Razor).*

Empêche l’action par défaut pour l’événement.

### `@on{EVENT}:stopPropagation`

*Ce scénario s’applique uniquement aux Razor composants (. Razor).*

Arrête la propagation d’événements pour l’événement.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### `@key`

*Ce scénario s’applique uniquement aux Razor composants (. Razor).*

L’attribut de directive `@key` amène les composants à comparer l’algorithme afin de garantir la préservation des éléments ou des composants en fonction de la valeur de la clé. Pour plus d’informations, consultez <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>.

### `@ref`

*Ce scénario s’applique uniquement aux Razor composants (. Razor).*

Les références de composants (`@ref`) permettent de référencer une instance de composant afin que vous puissiez émettre des commandes vers cette instance. Pour plus d’informations, consultez <xref:blazor/components/index#capture-references-to-components>.

### `@typeparam`

*Ce scénario s’applique uniquement aux Razor composants (. Razor).*

La `@typeparam` directive déclare un paramètre de type générique pour la classe de composant générée. Pour plus d’informations, consultez <xref:blazor/components/templated-components>.

::: moniker-end

## <a name="templated-razor-delegates"></a>Délégués basés sur un modèle Razor

Razor les modèles vous permettent de définir un extrait de code d’interface utilisateur au format suivant :

```cshtml
@<tag>...</tag>
```

L’exemple suivant montre comment spécifier un délégué basé sur Razor un modèle en tant que <xref:System.Func%602> . Le [type dynamique](/dotnet/csharp/programming-guide/types/using-type-dynamic) est spécifié pour le paramètre de la méthode encapsulée par le délégué. Un [type objet](/dotnet/csharp/language-reference/keywords/object) est spécifié comme valeur de retour du délégué. Le modèle est utilisé avec une <xref:System.Collections.Generic.List%601> de `Pet` qui a une propriété `Name`.

```csharp
public class Pet
{
    public string Name { get; set; }
}
```

```cshtml
@{
    Func<dynamic, object> petTemplate = @<p>You have a pet named <strong>@item.Name</strong>.</p>;

    var pets = new List<Pet>
    {
        new Pet { Name = "Rin Tin Tin" },
        new Pet { Name = "Mr. Bigglesworth" },
        new Pet { Name = "K-9" }
    };
}
```

Le modèle est rendu avec des éléments `pets` fournis par une instruction `foreach` :

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

Sortie rendue :

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

Vous pouvez également fournir un modèle inline Razor en tant qu’argument à une méthode. Dans l’exemple suivant, la `Repeat` méthode reçoit un Razor modèle. La méthode utilise le modèle pour produire du contenu HTML avec des répétitions d’éléments fournis à partir d’une liste :

```cshtml
@using Microsoft.AspNetCore.Html

@functions {
    public static IHtmlContent Repeat(IEnumerable<dynamic> items, int times,
        Func<dynamic, IHtmlContent> template)
    {
        var html = new HtmlContentBuilder();

        foreach (var item in items)
        {
            for (var i = 0; i < times; i++)
            {
                html.AppendHtml(template(item));
            }
        }

        return html;
    }
}
```

En utilisant la liste d’éléments « pets » de l’exemple précédent, la méthode `Repeat` est appelée avec :

* <xref:System.Collections.Generic.List%601> de `Pet`.
* Nombre de fois que chaque élément « pet » doit être répété.
* Modèle inline à utiliser pour les éléments de liste d’une liste non triée.

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

Sortie rendue :

```html
<ul>
    <li>Rin Tin Tin</li>
    <li>Rin Tin Tin</li>
    <li>Rin Tin Tin</li>
    <li>Mr. Bigglesworth</li>
    <li>Mr. Bigglesworth</li>
    <li>Mr. Bigglesworth</li>
    <li>K-9</li>
    <li>K-9</li>
    <li>K-9</li>
</ul>
```

## <a name="tag-helpers"></a>Tag Helpers

*Ce scénario s’applique uniquement aux affichages et Razor pages MVC (. cshtml).*

Il existe trois directives spécifiques aux [Tag Helpers](xref:mvc/views/tag-helpers/intro).

| Directive | Fonction |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | Rend les Tag Helpers disponibles dans une vue. |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | Supprime les Tag Helpers précédemment ajoutés à une vue. |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | Spécifie un préfixe de balise pour activer la prise en charge des Tag Helpers et rendre leur usage explicite. |

## <a name="razor-reserved-keywords"></a>Razor Mots clés réservés

### <a name="razor-keywords"></a>Razor mot

* `page` (Nécessite ASP.NET Core 2,1 ou version ultérieure)
* `namespace`
* `functions`
* `inherits`
* `model`
* `section`
* `helper` (Actuellement pris en charge par ASP.NET Core)

Razor les mots clés sont placés dans une séquence d’échappement `@(Razor Keyword)` (par exemple, `@(functions)` ).

### <a name="c-razor-keywords"></a>RazorMots clés C#

* `case`
* `do`
* `default`
* `for`
* `foreach`
* `if`
* `else`
* `lock`
* `switch`
* `try`
* `catch`
* `finally`
* `using`
* `while`

Les Razor Mots clés C# doivent être double-échappés par `@(@C# Razor Keyword)` (par exemple, `@(@case)` ). Le premier `@` échappe l' Razor analyseur. La seconde séquence d’échappement `@` est pour l’analyseur C#.

### <a name="reserved-keywords-not-used-by-razor"></a>Mots clés réservés non utilisés par Razor

* `class`

## <a name="inspect-the-razor-c-class-generated-for-a-view"></a>Inspecter la Razor classe C# générée pour une vue

::: moniker range=">= aspnetcore-2.1"

Avec kit SDK .NET Core 2,1 ou version ultérieure, le [ Razor Kit de développement logiciel (SDK)](xref:razor-pages/sdk) gère la compilation des Razor fichiers. Lors de la génération d’un projet, le Razor Kit de développement logiciel (SDK) génère un répertoire *obj/<build_configuration>/<target_framework_moniker>/ Razor* dans la racine du projet. La structure de répertoires dans le *Razor* répertoire reflète la structure de répertoire du projet.

Considérez la structure de répertoires suivante dans un projet ASP.NET Core 2,1 Razor pages ciblant .net Core 2,1 :

```
 Areas/
   Admin/
     Pages/
       Index.cshtml
       Index.cshtml.cs
 Pages/
   Shared/
     _Layout.cshtml
   _ViewImports.cshtml
   _ViewStart.cshtml
   Index.cshtml
   Index.cshtml.cs
  ```

La création du projet dans la configuration *Debug* génère le répertoire *obj* suivant :

```
 obj/
   Debug/
     netcoreapp2.1/
       Razor/
         Areas/
           Admin/
             Pages/
               Index.g.cshtml.cs
         Pages/
           Shared/
             _Layout.g.cshtml.cs
           _ViewImports.g.cshtml.cs
           _ViewStart.g.cshtml.cs
           Index.g.cshtml.cs
```

Pour afficher la classe générée pour *pages/index. cshtml*, ouvrez *obj/Debug/netcoreapp 2.1/ Razor /pages/index.g.cshtml.cs*.

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

Ajoutez la classe suivante au projet ASP.NET Core MVC :

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

Dans `Startup.ConfigureServices`, remplacez la classe `RazorTemplateEngine` ajoutée par MVC par la classe `CustomTemplateEngine` :

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

Définissez un point d’arrêt sur l’instruction `return csharpDocument;` de `CustomTemplateEngine`. Quand l’exécution du programme s’arrête au point d’arrêt, affichez la valeur de `generatedCode`.

![Vue Visualiseur de texte du code généré](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a>Recherches de vues et respect de la casse

Le Razor moteur d’affichage effectue des recherches respectant la casse pour les vues. Toutefois, la recherche réellement effectuée est déterminée par le système de fichiers sous-jacent :

* Source basé sur un fichier :
  * Sur les systèmes d’exploitation avec des systèmes de fichiers qui ne respectent pas la casse (par exemple, Windows), les recherches de fournisseurs de fichiers physiques ne respectent pas la casse. Par exemple, `return View("Test")` trouve les correspondances */Views/Home/Test.cshtml*, */Views/home/test.cshtml* et toutes les autres variantes de casse.
  * Sur des systèmes de fichiers respectant la casse (par exemple, Linux, OSX, et avec `EmbeddedFileProvider`), les recherches respectent la casse. Par exemple, `return View("Test")` trouve uniquement la correspondance */Views/Home/Test.cshtml*.
* Vues précompilées : Avec ASP.NET Core 2.0 et les versions ultérieures, les recherches de vues précompilées ne respectent pas la casse, quels que soient les systèmes d’exploitation. Le comportement est le même que celui du fournisseur de fichiers physiques sur Windows. Si deux vues précompilées diffèrent seulement par leur casse, le résultat de la recherche est non déterministe.

Les développeurs doivent s’efforcer d’utiliser la même casse pour les noms de fichiers et de répertoires que pour les noms des éléments suivants :

* Zone, contrôleur et action
* Razor Pages.

L’utilisation d’une casse identique garantit que les déploiements trouvent toujours les vues associées, indépendamment du système de fichiers sous-jacent.

## <a name="additional-resources"></a>Ressources supplémentaires

[Présentation de la programmation Web ASP.net à l’aide de Razor La syntaxe](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) fournit de nombreux exemples de programmation à l’aide de la Razor syntaxe.
