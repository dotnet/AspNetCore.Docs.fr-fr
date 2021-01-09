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
ms.openlocfilehash: cb9ffab19062bf726dd519c782d502f76e372073
ms.sourcegitcommit: 97243663fd46c721660e77ef652fe2190a461f81
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2021
ms.locfileid: "98058283"
---
# <a name="no-locrazor-syntax-reference-for-aspnet-core"></a><span data-ttu-id="86203-103">Razor Référence de syntaxe pour ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="86203-103">Razor syntax reference for ASP.NET Core</span></span>

<span data-ttu-id="86203-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen)et [Dan Vicarel](https://github.com/Rabadash8820)</span><span class="sxs-lookup"><span data-stu-id="86203-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen), and [Dan Vicarel](https://github.com/Rabadash8820)</span></span>

<span data-ttu-id="86203-105">Razor est une syntaxe de balisage pour incorporer du code basé sur le serveur dans des pages Web.</span><span class="sxs-lookup"><span data-stu-id="86203-105">Razor is a markup syntax for embedding server-based code into webpages.</span></span> <span data-ttu-id="86203-106">La Razor syntaxe se compose du Razor balisage, de C# et du code html.</span><span class="sxs-lookup"><span data-stu-id="86203-106">The Razor syntax consists of Razor markup, C#, and HTML.</span></span> <span data-ttu-id="86203-107">Les fichiers contenant Razor ont généralement une extension de fichier *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="86203-107">Files containing Razor generally have a *.cshtml* file extension.</span></span> <span data-ttu-id="86203-108">Razorse trouve également dans les fichiers de [ Razor composants](xref:blazor/components/index) (*. Razor*).</span><span class="sxs-lookup"><span data-stu-id="86203-108">Razor is also found in [Razor components](xref:blazor/components/index) files (*.razor*).</span></span>

## <a name="rendering-html"></a><span data-ttu-id="86203-109">Rendu HTML</span><span class="sxs-lookup"><span data-stu-id="86203-109">Rendering HTML</span></span>

<span data-ttu-id="86203-110">La langue par défaut Razor est html.</span><span class="sxs-lookup"><span data-stu-id="86203-110">The default Razor language is HTML.</span></span> <span data-ttu-id="86203-111">Le rendu HTML à partir du Razor balisage n’est pas différent du rendu HTML d’un fichier html.</span><span class="sxs-lookup"><span data-stu-id="86203-111">Rendering HTML from Razor markup is no different than rendering HTML from an HTML file.</span></span> <span data-ttu-id="86203-112">Le balisage HTML dans les fichiers *. cshtml* Razor est rendu par le serveur inchangé.</span><span class="sxs-lookup"><span data-stu-id="86203-112">HTML markup in *.cshtml* Razor files is rendered by the server unchanged.</span></span>

## <a name="no-locrazor-syntax"></a><span data-ttu-id="86203-113">Syntaxe de Razor</span><span class="sxs-lookup"><span data-stu-id="86203-113">Razor syntax</span></span>

<span data-ttu-id="86203-114">Razor prend en charge C# et utilise le `@` symbole pour passer du code HTML à c#.</span><span class="sxs-lookup"><span data-stu-id="86203-114">Razor supports C# and uses the `@` symbol to transition from HTML to C#.</span></span> <span data-ttu-id="86203-115">Razor évalue les expressions C# et les restitue dans la sortie HTML.</span><span class="sxs-lookup"><span data-stu-id="86203-115">Razor evaluates C# expressions and renders them in the HTML output.</span></span>

<span data-ttu-id="86203-116">Quand un `@` symbole est suivi d’un [ Razor mot clé réservé](#razor-reserved-keywords), il passe à un Razor balisage spécifique.</span><span class="sxs-lookup"><span data-stu-id="86203-116">When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords), it transitions into Razor-specific markup.</span></span> <span data-ttu-id="86203-117">Sinon, il est converti en code C# brut.</span><span class="sxs-lookup"><span data-stu-id="86203-117">Otherwise, it transitions into plain C#.</span></span>

<span data-ttu-id="86203-118">Pour échapper un `@` symbole dans Razor le balisage, utilisez un deuxième `@` symbole :</span><span class="sxs-lookup"><span data-stu-id="86203-118">To escape an `@` symbol in Razor markup, use a second `@` symbol:</span></span>

```cshtml
<p>@@Username</p>
```

<span data-ttu-id="86203-119">Le code est affiché en HTML avec un seul symbole `@` :</span><span class="sxs-lookup"><span data-stu-id="86203-119">The code is rendered in HTML with a single `@` symbol:</span></span>

```html
<p>@Username</p>
```

<span data-ttu-id="86203-120">Les attributs et le code HTML contenant des adresses e-mail ne traitent pas le symbole `@` comme un caractère de conversion.</span><span class="sxs-lookup"><span data-stu-id="86203-120">HTML attributes and content containing email addresses don't treat the `@` symbol as a transition character.</span></span> <span data-ttu-id="86203-121">Les adresses de messagerie de l’exemple suivant ne sont pas touchées par l' Razor analyse :</span><span class="sxs-lookup"><span data-stu-id="86203-121">The email addresses in the following example are untouched by Razor parsing:</span></span>

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-no-locrazor-expressions"></a><span data-ttu-id="86203-122">Expressions implicites Razor</span><span class="sxs-lookup"><span data-stu-id="86203-122">Implicit Razor expressions</span></span>

<span data-ttu-id="86203-123">RazorLes expressions implicites commencent par `@` suivies du code C# :</span><span class="sxs-lookup"><span data-stu-id="86203-123">Implicit Razor expressions start with `@` followed by C# code:</span></span>

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

<span data-ttu-id="86203-124">À l’exception du mot clé `await` C#, les expressions implicites ne doivent pas contenir d’espaces.</span><span class="sxs-lookup"><span data-stu-id="86203-124">With the exception of the C# `await` keyword, implicit expressions must not contain spaces.</span></span> <span data-ttu-id="86203-125">Si l’instruction C# se termine de façon non ambigüe, il est possible d’insérer des espaces n’importe où dans la chaîne :</span><span class="sxs-lookup"><span data-stu-id="86203-125">If the C# statement has a clear ending, spaces can be intermingled:</span></span>

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

<span data-ttu-id="86203-126">Les expressions implicites **ne doivent pas** contenir de caractères génériques C#, car les caractères entre crochets (`<>`) sont interprétés comme une balise HTML.</span><span class="sxs-lookup"><span data-stu-id="86203-126">Implicit expressions **cannot** contain C# generics, as the characters inside the brackets (`<>`) are interpreted as an HTML tag.</span></span> <span data-ttu-id="86203-127">Le code suivant n’est **pas** valide :</span><span class="sxs-lookup"><span data-stu-id="86203-127">The following code is **not** valid:</span></span>

```cshtml
<p>@GenericMethod<int>()</p>
```

<span data-ttu-id="86203-128">Le code précédent génère l’un des types d’erreur de compilateur suivants :</span><span class="sxs-lookup"><span data-stu-id="86203-128">The preceding code generates a compiler error similar to one of the following:</span></span>

* <span data-ttu-id="86203-129">L’élément « int » n’a pas été fermé.</span><span class="sxs-lookup"><span data-stu-id="86203-129">The "int" element wasn't closed.</span></span> <span data-ttu-id="86203-130">Tous les éléments doivent se fermer automatiquement ou contenir une balise de fin correspondante.</span><span class="sxs-lookup"><span data-stu-id="86203-130">All elements must be either self-closing or have a matching end tag.</span></span>
* <span data-ttu-id="86203-131">Impossible de convertir le groupe de méthodes 'GenericMethod' en type non-délégué 'object'.</span><span class="sxs-lookup"><span data-stu-id="86203-131">Cannot convert method group 'GenericMethod' to non-delegate type 'object'.</span></span> <span data-ttu-id="86203-132">Souhaitiez-vous appeler la méthode ?</span><span class="sxs-lookup"><span data-stu-id="86203-132">Did you intend to invoke the method?\`</span></span>

<span data-ttu-id="86203-133">Les appels de méthode génériques doivent être encapsulés dans une [ Razor expression explicite](#explicit-razor-expressions) ou un [ Razor bloc de code](#razor-code-blocks).</span><span class="sxs-lookup"><span data-stu-id="86203-133">Generic method calls must be wrapped in an [explicit Razor expression](#explicit-razor-expressions) or a [Razor code block](#razor-code-blocks).</span></span>

## <a name="explicit-no-locrazor-expressions"></a><span data-ttu-id="86203-134">Expressions explicites Razor</span><span class="sxs-lookup"><span data-stu-id="86203-134">Explicit Razor expressions</span></span>

<span data-ttu-id="86203-135">RazorLes expressions explicites se composent d’un `@` symbole avec des parenthèses équilibrées.</span><span class="sxs-lookup"><span data-stu-id="86203-135">Explicit Razor expressions consist of an `@` symbol with balanced parenthesis.</span></span> <span data-ttu-id="86203-136">Pour afficher l’heure de la semaine dernière, le Razor balisage suivant est utilisé :</span><span class="sxs-lookup"><span data-stu-id="86203-136">To render last week's time, the following Razor markup is used:</span></span>

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

<span data-ttu-id="86203-137">Le contenu situé entre les parenthèses `@()` est évalué et affiché dans la sortie.</span><span class="sxs-lookup"><span data-stu-id="86203-137">Any content within the `@()` parenthesis is evaluated and rendered to the output.</span></span>

<span data-ttu-id="86203-138">Les expressions implicites, décrites dans la section précédente, ne doivent généralement pas contenir d’espaces.</span><span class="sxs-lookup"><span data-stu-id="86203-138">Implicit expressions, described in the previous section, generally can't contain spaces.</span></span> <span data-ttu-id="86203-139">Dans le code suivant, une semaine n’est pas déduite de l’heure actuelle :</span><span class="sxs-lookup"><span data-stu-id="86203-139">In the following code, one week isn't subtracted from the current time:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

<span data-ttu-id="86203-140">Le code s’affiche en HTML de la façon suivante :</span><span class="sxs-lookup"><span data-stu-id="86203-140">The code renders the following HTML:</span></span>

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

<span data-ttu-id="86203-141">Les expressions explicites peuvent servir à concaténer du texte avec un résultat d’expression :</span><span class="sxs-lookup"><span data-stu-id="86203-141">Explicit expressions can be used to concatenate text with an expression result:</span></span>

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

<span data-ttu-id="86203-142">Sans l’expression explicite, `<p>Age@joe.Age</p>` est traité comme une adresse e-mail, et `<p>Age@joe.Age</p>` est affiché.</span><span class="sxs-lookup"><span data-stu-id="86203-142">Without the explicit expression, `<p>Age@joe.Age</p>` is treated as an email address, and `<p>Age@joe.Age</p>` is rendered.</span></span> <span data-ttu-id="86203-143">Avec une expression explicite, `<p>Age33</p>` est affiché.</span><span class="sxs-lookup"><span data-stu-id="86203-143">When written as an explicit expression, `<p>Age33</p>` is rendered.</span></span>

<span data-ttu-id="86203-144">Les expressions explicites peuvent être utilisées pour afficher la sortie de méthodes génériques dans les fichiers *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="86203-144">Explicit expressions can be used to render output from generic methods in *.cshtml* files.</span></span> <span data-ttu-id="86203-145">Le balisage suivant montre comment corriger l’erreur affichée précédemment provoquée par les crochets d’un générique C#.</span><span class="sxs-lookup"><span data-stu-id="86203-145">The following markup shows how to correct the error shown earlier caused by the brackets of a C# generic.</span></span> <span data-ttu-id="86203-146">Le code est écrit sous forme d’expression explicite :</span><span class="sxs-lookup"><span data-stu-id="86203-146">The code is written as an explicit expression:</span></span>

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a><span data-ttu-id="86203-147">Encodage des expressions</span><span class="sxs-lookup"><span data-stu-id="86203-147">Expression encoding</span></span>

<span data-ttu-id="86203-148">Les expressions C# évaluées qui correspondent à une chaîne sont encodées en HTML.</span><span class="sxs-lookup"><span data-stu-id="86203-148">C# expressions that evaluate to a string are HTML encoded.</span></span> <span data-ttu-id="86203-149">Les expressions C# évaluées qui correspondent à `IHtmlContent` sont affichées directement par `IHtmlContent.WriteTo`.</span><span class="sxs-lookup"><span data-stu-id="86203-149">C# expressions that evaluate to `IHtmlContent` are rendered directly through `IHtmlContent.WriteTo`.</span></span> <span data-ttu-id="86203-150">Les expressions C# évaluées qui ne correspondent pas à `IHtmlContent` sont converties en chaîne par `ToString` et sont encodées avant d’être affichées.</span><span class="sxs-lookup"><span data-stu-id="86203-150">C# expressions that don't evaluate to `IHtmlContent` are converted to a string by `ToString` and encoded before they're rendered.</span></span>

```cshtml
@("<span>Hello World</span>")
```

<span data-ttu-id="86203-151">Le code précédent génère le code HTML suivant :</span><span class="sxs-lookup"><span data-stu-id="86203-151">The preceding code renders the following HTML:</span></span>

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

<span data-ttu-id="86203-152">Le code HTML est affiché sous forme de texte brut dans le navigateur :</span><span class="sxs-lookup"><span data-stu-id="86203-152">The HTML is shown in the browser as plain text:</span></span>

<span data-ttu-id="86203-153">&lt;span &gt; Hello World &lt; /span&gt;</span><span class="sxs-lookup"><span data-stu-id="86203-153">&lt;span&gt;Hello World&lt;/span&gt;</span></span>

<span data-ttu-id="86203-154">La sortie `HtmlHelper.Raw` n’est pas encodée, mais elle est affichée sous forme de balisage HTML.</span><span class="sxs-lookup"><span data-stu-id="86203-154">`HtmlHelper.Raw` output isn't encoded but rendered as HTML markup.</span></span>

> [!WARNING]
> <span data-ttu-id="86203-155">Utiliser `HtmlHelper.Raw` sur des entrées utilisateur non nettoyées présente un risque pour la sécurité.</span><span class="sxs-lookup"><span data-stu-id="86203-155">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span></span> <span data-ttu-id="86203-156">Les entrées utilisateur peuvent contenir du code malveillant JavaScript ou d’un autre type.</span><span class="sxs-lookup"><span data-stu-id="86203-156">User input might contain malicious JavaScript or other exploits.</span></span> <span data-ttu-id="86203-157">Le nettoyage des entrées utilisateur est difficile.</span><span class="sxs-lookup"><span data-stu-id="86203-157">Sanitizing user input is difficult.</span></span> <span data-ttu-id="86203-158">C’est pourquoi il est préférable de ne pas utiliser `HtmlHelper.Raw` sur des entrées utilisateur.</span><span class="sxs-lookup"><span data-stu-id="86203-158">Avoid using `HtmlHelper.Raw` with user input.</span></span>

```cshtml
@Html.Raw("<span>Hello World</span>")
```

<span data-ttu-id="86203-159">Le code s’affiche en HTML de la façon suivante :</span><span class="sxs-lookup"><span data-stu-id="86203-159">The code renders the following HTML:</span></span>

```html
<span>Hello World</span>
```

## <a name="no-locrazor-code-blocks"></a><span data-ttu-id="86203-160">Razor blocs de code</span><span class="sxs-lookup"><span data-stu-id="86203-160">Razor code blocks</span></span>

<span data-ttu-id="86203-161">Razor les blocs de code commencent par `@` et sont encadrés par `{}` .</span><span class="sxs-lookup"><span data-stu-id="86203-161">Razor code blocks start with `@` and are enclosed by `{}`.</span></span> <span data-ttu-id="86203-162">Contrairement aux expressions, le code C# figurant dans des blocs de code n’est pas affiché.</span><span class="sxs-lookup"><span data-stu-id="86203-162">Unlike expressions, C# code inside code blocks isn't rendered.</span></span> <span data-ttu-id="86203-163">Les blocs de code et les expressions dans une vue ont la même étendue et sont définis dans l’ordre :</span><span class="sxs-lookup"><span data-stu-id="86203-163">Code blocks and expressions in a view share the same scope and are defined in order:</span></span>

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

<span data-ttu-id="86203-164">Le code s’affiche en HTML de la façon suivante :</span><span class="sxs-lookup"><span data-stu-id="86203-164">The code renders the following HTML:</span></span>

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="86203-165">Dans des blocs de code, déclarez des [fonctions locales](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) avec des balises servant de méthodes de création de modèles :</span><span class="sxs-lookup"><span data-stu-id="86203-165">In code blocks, declare [local functions](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) with markup to serve as templating methods:</span></span>

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

<span data-ttu-id="86203-166">Le code s’affiche en HTML de la façon suivante :</span><span class="sxs-lookup"><span data-stu-id="86203-166">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a><span data-ttu-id="86203-167">Transitions implicites</span><span class="sxs-lookup"><span data-stu-id="86203-167">Implicit transitions</span></span>

<span data-ttu-id="86203-168">La langue par défaut dans un bloc de code est C#, mais la Razor page peut passer au format HTML :</span><span class="sxs-lookup"><span data-stu-id="86203-168">The default language in a code block is C#, but the Razor Page can transition back to HTML:</span></span>

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a><span data-ttu-id="86203-169">Conversion délimitée explicite</span><span class="sxs-lookup"><span data-stu-id="86203-169">Explicit delimited transition</span></span>

<span data-ttu-id="86203-170">Pour définir une sous-section d’un bloc de code qui doit restituer du code HTML, entourez les caractères du rendu à l’aide de la Razor `<text>` balise :</span><span class="sxs-lookup"><span data-stu-id="86203-170">To define a subsection of a code block that should render HTML, surround the characters for rendering with the Razor `<text>` tag:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

<span data-ttu-id="86203-171">Utilisez cette approche pour afficher du code HTML qui n’est pas entouré d’une balise HTML.</span><span class="sxs-lookup"><span data-stu-id="86203-171">Use this approach to render HTML that isn't surrounded by an HTML tag.</span></span> <span data-ttu-id="86203-172">Sans code HTML ou Razor balise, une Razor erreur d’exécution se produit.</span><span class="sxs-lookup"><span data-stu-id="86203-172">Without an HTML or Razor tag, a Razor runtime error occurs.</span></span>

<span data-ttu-id="86203-173">La balise `<text>` est utile pour contrôler les espaces blancs dans le contenu affiché :</span><span class="sxs-lookup"><span data-stu-id="86203-173">The `<text>` tag is useful to control whitespace when rendering content:</span></span>

* <span data-ttu-id="86203-174">Seul le contenu situé dans la balise `<text>` est affiché.</span><span class="sxs-lookup"><span data-stu-id="86203-174">Only the content between the `<text>` tag is rendered.</span></span>
* <span data-ttu-id="86203-175">Aucun espace blanc avant ou après la balise `<text>` ne s’affiche dans la sortie HTML.</span><span class="sxs-lookup"><span data-stu-id="86203-175">No whitespace before or after the `<text>` tag appears in the HTML output.</span></span>

### <a name="explicit-line-transition"></a><span data-ttu-id="86203-176">Transition de ligne explicite</span><span class="sxs-lookup"><span data-stu-id="86203-176">Explicit line transition</span></span>

<span data-ttu-id="86203-177">Pour afficher le reste d’une ligne entière au format HTML à l’intérieur d’un bloc de code, utilisez la `@:` syntaxe suivante :</span><span class="sxs-lookup"><span data-stu-id="86203-177">To render the rest of an entire line as HTML inside a code block, use `@:` syntax:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

<span data-ttu-id="86203-178">Sans le `@:` dans le code, une Razor erreur d’exécution est générée.</span><span class="sxs-lookup"><span data-stu-id="86203-178">Without the `@:` in the code, a Razor runtime error is generated.</span></span>

<span data-ttu-id="86203-179">`@`Les caractères supplémentaires d’un Razor fichier peuvent provoquer des erreurs du compilateur dans des instructions plus loin dans le bloc.</span><span class="sxs-lookup"><span data-stu-id="86203-179">Extra `@` characters in a Razor file can cause compiler errors at statements later in the block.</span></span> <span data-ttu-id="86203-180">Ces erreurs du compilateur peuvent être difficiles à comprendre, car elles se produisent en réalité avant les erreurs signalées.</span><span class="sxs-lookup"><span data-stu-id="86203-180">These compiler errors can be difficult to understand because the actual error occurs before the reported error.</span></span> <span data-ttu-id="86203-181">Ce type d’erreur est courant après la combinaison de plusieurs expressions implicites ou explicites dans un même bloc de code.</span><span class="sxs-lookup"><span data-stu-id="86203-181">This error is common after combining multiple implicit/explicit expressions into a single code block.</span></span>

## <a name="control-structures"></a><span data-ttu-id="86203-182">Structures de contrôle</span><span class="sxs-lookup"><span data-stu-id="86203-182">Control structures</span></span>

<span data-ttu-id="86203-183">Les structures de contrôle sont une extension des blocs de code.</span><span class="sxs-lookup"><span data-stu-id="86203-183">Control structures are an extension of code blocks.</span></span> <span data-ttu-id="86203-184">Toutes les caractéristiques des blocs de code (conversion de balisage, Inline C#) valent aussi pour les structures suivantes :</span><span class="sxs-lookup"><span data-stu-id="86203-184">All aspects of code blocks (transitioning to markup, inline C#) also apply to the following structures:</span></span>

### <a name="conditionals-if-else-if-else-and-switch"></a><span data-ttu-id="86203-185">Conditions `@if, else if, else, and @switch`</span><span class="sxs-lookup"><span data-stu-id="86203-185">Conditionals `@if, else if, else, and @switch`</span></span>

<span data-ttu-id="86203-186">`@if` contrôle l’exécution du code :</span><span class="sxs-lookup"><span data-stu-id="86203-186">`@if` controls when code runs:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

<span data-ttu-id="86203-187">`else` et `else if` ne nécessitent pas le symbole `@` :</span><span class="sxs-lookup"><span data-stu-id="86203-187">`else` and `else if` don't require the `@` symbol:</span></span>

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

<span data-ttu-id="86203-188">Le balisage suivant montre comment utiliser une instruction switch :</span><span class="sxs-lookup"><span data-stu-id="86203-188">The following markup shows how to use a switch statement:</span></span>

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

### <a name="looping-for-foreach-while-and-do-while"></a><span data-ttu-id="86203-189">Bouclage `@for, @foreach, @while, and @do while`</span><span class="sxs-lookup"><span data-stu-id="86203-189">Looping `@for, @foreach, @while, and @do while`</span></span>

<span data-ttu-id="86203-190">Le rendu HTML peut être réalisé à partir d'instructions de contrôle de boucle.</span><span class="sxs-lookup"><span data-stu-id="86203-190">Templated HTML can be rendered with looping control statements.</span></span> <span data-ttu-id="86203-191">Pour afficher une liste de personnes :</span><span class="sxs-lookup"><span data-stu-id="86203-191">To render a list of people:</span></span>

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

<span data-ttu-id="86203-192">Les instructions de boucle suivantes sont prises en charge :</span><span class="sxs-lookup"><span data-stu-id="86203-192">The following looping statements are supported:</span></span>

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

### <a name="compound-using"></a><span data-ttu-id="86203-193">Instruction composée `@using`</span><span class="sxs-lookup"><span data-stu-id="86203-193">Compound `@using`</span></span>

<span data-ttu-id="86203-194">En C#, une instruction `using` est utilisée pour garantir la dispose d’un objet.</span><span class="sxs-lookup"><span data-stu-id="86203-194">In C#, a `using` statement is used to ensure an object is disposed.</span></span> <span data-ttu-id="86203-195">Dans Razor , le même mécanisme est utilisé pour créer des applications auxiliaires HTML qui contiennent du contenu supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="86203-195">In Razor, the same mechanism is used to create HTML Helpers that contain additional content.</span></span> <span data-ttu-id="86203-196">Dans le code suivant, les HTML Helpers affichent une balise `<form>` à l’aide de l’instruction `@using` :</span><span class="sxs-lookup"><span data-stu-id="86203-196">In the following code, HTML Helpers render a `<form>` tag with the `@using` statement:</span></span>

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

<span data-ttu-id="86203-197">La gestion des exceptions est similaire à C# :</span><span class="sxs-lookup"><span data-stu-id="86203-197">Exception handling is similar to C#:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### `@lock`

<span data-ttu-id="86203-198">Razor a la possibilité de protéger des sections critiques avec des instructions Lock :</span><span class="sxs-lookup"><span data-stu-id="86203-198">Razor has the capability to protect critical sections with lock statements:</span></span>

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a><span data-ttu-id="86203-199">Commentaires</span><span class="sxs-lookup"><span data-stu-id="86203-199">Comments</span></span>

<span data-ttu-id="86203-200">Razor prend en charge les commentaires HTML et C# :</span><span class="sxs-lookup"><span data-stu-id="86203-200">Razor supports C# and HTML comments:</span></span>

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

<span data-ttu-id="86203-201">Le code s’affiche en HTML de la façon suivante :</span><span class="sxs-lookup"><span data-stu-id="86203-201">The code renders the following HTML:</span></span>

```html
<!-- HTML comment -->
```

<span data-ttu-id="86203-202">Razor Les commentaires sont supprimés par le serveur avant le rendu de la page Web.</span><span class="sxs-lookup"><span data-stu-id="86203-202">Razor comments are removed by the server before the webpage is rendered.</span></span> <span data-ttu-id="86203-203">Razor utilise `@*  *@` pour délimiter les commentaires.</span><span class="sxs-lookup"><span data-stu-id="86203-203">Razor uses `@*  *@` to delimit comments.</span></span> <span data-ttu-id="86203-204">Le code suivant est commenté pour indiquer au serveur de ne pas afficher le balisage :</span><span class="sxs-lookup"><span data-stu-id="86203-204">The following code is commented out, so the server doesn't render any markup:</span></span>

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a><span data-ttu-id="86203-205">Directives</span><span class="sxs-lookup"><span data-stu-id="86203-205">Directives</span></span>

<span data-ttu-id="86203-206">Razor les directives sont représentées par des expressions implicites avec des mots clés réservés après le `@` symbole.</span><span class="sxs-lookup"><span data-stu-id="86203-206">Razor directives are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="86203-207">En règle générale, une directive change la manière dont une vue est analysée ou active une fonctionnalité différente.</span><span class="sxs-lookup"><span data-stu-id="86203-207">A directive typically changes the way a view is parsed or enables different functionality.</span></span>

<span data-ttu-id="86203-208">Comprendre comment Razor génère du code pour une vue facilite la compréhension du fonctionnement des directives.</span><span class="sxs-lookup"><span data-stu-id="86203-208">Understanding how Razor generates code for a view makes it easier to understand how directives work.</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

<span data-ttu-id="86203-209">Le code génère une classe semblable à celle-ci :</span><span class="sxs-lookup"><span data-stu-id="86203-209">The code generates a class similar to the following:</span></span>

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

<span data-ttu-id="86203-210">Plus loin dans cet article, la section [inspecter la Razor classe C# générée pour une vue](#inspect-the-razor-c-class-generated-for-a-view) explique comment afficher cette classe générée.</span><span class="sxs-lookup"><span data-stu-id="86203-210">Later in this article, the section [Inspect the Razor C# class generated for a view](#inspect-the-razor-c-class-generated-for-a-view) explains how to view this generated class.</span></span>

### `@attribute`

<span data-ttu-id="86203-211">La directive `@attribute` permet d’ajouter l’attribut donné à la classe de la page ou de la vue générée.</span><span class="sxs-lookup"><span data-stu-id="86203-211">The `@attribute` directive adds the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="86203-212">L’exemple suivant ajoute l’attribut `[Authorize]` :</span><span class="sxs-lookup"><span data-stu-id="86203-212">The following example adds the `[Authorize]` attribute:</span></span>

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### `@code`

<span data-ttu-id="86203-213">*Ce scénario s’applique uniquement aux Razor composants (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="86203-213">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="86203-214">Le `@code` bloc permet à un [ Razor composant](xref:blazor/components/index) d’ajouter des membres C# (champs, propriétés et méthodes) à un composant :</span><span class="sxs-lookup"><span data-stu-id="86203-214">The `@code` block enables a [Razor component](xref:blazor/components/index) to add C# members (fields, properties, and methods) to a component:</span></span>

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

<span data-ttu-id="86203-215">Pour les Razor composants, `@code` est un alias de [`@functions`](#functions) et est recommandé sur `@functions` .</span><span class="sxs-lookup"><span data-stu-id="86203-215">For Razor components, `@code` is an alias of [`@functions`](#functions) and recommended over `@functions`.</span></span> <span data-ttu-id="86203-216">Plus d’un bloc `@code` est autorisé.</span><span class="sxs-lookup"><span data-stu-id="86203-216">More than one `@code` block is permissible.</span></span>

::: moniker-end

### `@functions`

<span data-ttu-id="86203-217">La directive `@functions` permet d’ajouter des membres C# (champs, propriétés et méthodes) à la classe générée :</span><span class="sxs-lookup"><span data-stu-id="86203-217">The `@functions` directive enables adding C# members (fields, properties, and methods) to the generated class:</span></span>

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="86203-218">Dans [ Razor composants](xref:blazor/components/index), utilisez `@code` sur `@functions` pour ajouter des membres C#.</span><span class="sxs-lookup"><span data-stu-id="86203-218">In [Razor components](xref:blazor/components/index), use `@code` over `@functions` to add C# members.</span></span>

::: moniker-end

<span data-ttu-id="86203-219">Exemple :</span><span class="sxs-lookup"><span data-stu-id="86203-219">For example:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

<span data-ttu-id="86203-220">Le code génère le balisage HTML suivant :</span><span class="sxs-lookup"><span data-stu-id="86203-220">The code generates the following HTML markup:</span></span>

```html
<div>From method: Hello</div>
```

<span data-ttu-id="86203-221">Le code suivant est la Razor classe C# générée :</span><span class="sxs-lookup"><span data-stu-id="86203-221">The following code is the generated Razor C# class:</span></span>

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="86203-222">Les méthodes `@functions` servent de méthodes de création de modèles lorsqu’elles comprennent des balises :</span><span class="sxs-lookup"><span data-stu-id="86203-222">`@functions` methods serve as templating methods when they have markup:</span></span>

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

<span data-ttu-id="86203-223">Le code s’affiche en HTML de la façon suivante :</span><span class="sxs-lookup"><span data-stu-id="86203-223">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### `@implements`

<span data-ttu-id="86203-224">La directive `@implements` implémente une interface pour la classe générée.</span><span class="sxs-lookup"><span data-stu-id="86203-224">The `@implements` directive implements an interface for the generated class.</span></span>

<span data-ttu-id="86203-225">L’exemple suivant implémente <xref:System.IDisposable?displayProperty=fullName> afin que la méthode <xref:System.IDisposable.Dispose*> puisse être appelée :</span><span class="sxs-lookup"><span data-stu-id="86203-225">The following example implements <xref:System.IDisposable?displayProperty=fullName> so that the <xref:System.IDisposable.Dispose*> method can be called:</span></span>

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

<span data-ttu-id="86203-226">La directive `@inherits` fournit un contrôle complet de la classe héritée par la vue :</span><span class="sxs-lookup"><span data-stu-id="86203-226">The `@inherits` directive provides full control of the class the view inherits:</span></span>

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

<span data-ttu-id="86203-227">Le code suivant est un Razor type de page personnalisé :</span><span class="sxs-lookup"><span data-stu-id="86203-227">The following code is a custom Razor page type:</span></span>

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

<span data-ttu-id="86203-228">Le `CustomText` s’affiche dans une vue :</span><span class="sxs-lookup"><span data-stu-id="86203-228">The `CustomText` is displayed in a view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

<span data-ttu-id="86203-229">Le code s’affiche en HTML de la façon suivante :</span><span class="sxs-lookup"><span data-stu-id="86203-229">The code renders the following HTML:</span></span>

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 <span data-ttu-id="86203-230">`@model` et `@inherits` peuvent s’utiliser dans la même vue.</span><span class="sxs-lookup"><span data-stu-id="86203-230">`@model` and `@inherits` can be used in the same view.</span></span> <span data-ttu-id="86203-231">`@inherits` peut être dans un fichier *_ViewImports.cshtml* importé par la vue :</span><span class="sxs-lookup"><span data-stu-id="86203-231">`@inherits` can be in a *_ViewImports.cshtml* file that the view imports:</span></span>

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

<span data-ttu-id="86203-232">Le code suivant est un exemple de vue fortement typée :</span><span class="sxs-lookup"><span data-stu-id="86203-232">The following code is an example of a strongly-typed view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

<span data-ttu-id="86203-233">Si « rick@contoso.com » est passé au modèle, la vue génère le balisage HTML suivant :</span><span class="sxs-lookup"><span data-stu-id="86203-233">If "rick@contoso.com" is passed in the model, the view generates the following HTML markup:</span></span>

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### `@inject`

<span data-ttu-id="86203-234">La `@inject` directive permet Razor à la page d’injecter un service à partir du [conteneur de service](xref:fundamentals/dependency-injection) dans une vue.</span><span class="sxs-lookup"><span data-stu-id="86203-234">The `@inject` directive enables the Razor Page to inject a service from the [service container](xref:fundamentals/dependency-injection) into a view.</span></span> <span data-ttu-id="86203-235">Pour plus d’informations, consultez [Injection de dépendances dans les vues](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="86203-235">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

::: moniker range=">= aspnetcore-3.0"

### `@layout`

<span data-ttu-id="86203-236">*Ce scénario s’applique uniquement aux Razor composants (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="86203-236">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="86203-237">La `@layout` directive spécifie une disposition pour les Razor composants routables qui ont une [`@page`](#page) directive.</span><span class="sxs-lookup"><span data-stu-id="86203-237">The `@layout` directive specifies a layout for routable Razor components that have an [`@page`](#page) directive.</span></span> <span data-ttu-id="86203-238">Les composants de disposition sont utilisés pour éviter la duplication et l’incohérence de code.</span><span class="sxs-lookup"><span data-stu-id="86203-238">Layout components are used to avoid code duplication and inconsistency.</span></span> <span data-ttu-id="86203-239">Pour plus d'informations, consultez <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="86203-239">For more information, see <xref:blazor/layouts>.</span></span>

::: moniker-end

### `@model`

<span data-ttu-id="86203-240">*Ce scénario s’applique uniquement aux affichages et Razor pages MVC (. cshtml).*</span><span class="sxs-lookup"><span data-stu-id="86203-240">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="86203-241">La directive `@model` spécifie le type du modèle passé à une vue ou une page :</span><span class="sxs-lookup"><span data-stu-id="86203-241">The `@model` directive specifies the type of the model passed to a view or page:</span></span>

```cshtml
@model TypeNameOfModel
```

<span data-ttu-id="86203-242">Dans une application ASP.NET Core MVC ou Razor pages créée avec des comptes d’utilisateur individuels, *views/Account/login. cshtml* contient la déclaration de modèle suivante :</span><span class="sxs-lookup"><span data-stu-id="86203-242">In an ASP.NET Core MVC or Razor Pages app created with individual user accounts, *Views/Account/Login.cshtml* contains the following model declaration:</span></span>

```cshtml
@model LoginViewModel
```

<span data-ttu-id="86203-243">La classe générée hérite de `RazorPage<dynamic>` :</span><span class="sxs-lookup"><span data-stu-id="86203-243">The class generated inherits from `RazorPage<dynamic>`:</span></span>

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

<span data-ttu-id="86203-244">Razor expose une `Model` propriété pour accéder au modèle passé à la vue :</span><span class="sxs-lookup"><span data-stu-id="86203-244">Razor exposes a `Model` property for accessing the model passed to the view:</span></span>

```cshtml
<div>The Login Email: @Model.Email</div>
```

<span data-ttu-id="86203-245">La directive `@model` spécifie le type de la propriété `Model`.</span><span class="sxs-lookup"><span data-stu-id="86203-245">The `@model` directive specifies the type of the `Model` property.</span></span> <span data-ttu-id="86203-246">La directive spécifie le type `T` dans `RazorPage<T>` pour la classe générée dont dérive la vue.</span><span class="sxs-lookup"><span data-stu-id="86203-246">The directive specifies the `T` in `RazorPage<T>` that the generated class that the view derives from.</span></span> <span data-ttu-id="86203-247">Si la directive `@model` n’est pas spécifiée, la propriété `Model` est de type `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="86203-247">If the `@model` directive isn't specified, the `Model` property is of type `dynamic`.</span></span> <span data-ttu-id="86203-248">Pour plus d’informations, consultez [modèles fortement typés et le @model mot clé](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span><span class="sxs-lookup"><span data-stu-id="86203-248">For more information, see [Strongly typed models and the @model keyword](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span></span>

### `@namespace`

<span data-ttu-id="86203-249">La directive `@namespace` :</span><span class="sxs-lookup"><span data-stu-id="86203-249">The `@namespace` directive:</span></span>

* <span data-ttu-id="86203-250">Définit l’espace de noms de la classe de la page générée, de la Razor vue MVC ou du Razor composant.</span><span class="sxs-lookup"><span data-stu-id="86203-250">Sets the namespace of the class of the generated Razor page, MVC view, or Razor component.</span></span>
* <span data-ttu-id="86203-251">Définit les espaces de noms dérivés racine d’une classe de pages, de vues ou de composants à partir du fichier d’importations le plus proche dans l’arborescence de répertoires, *_ViewImports. cshtml* (vues ou pages) ou *_Imports. Razor* ( Razor composants).</span><span class="sxs-lookup"><span data-stu-id="86203-251">Sets the root derived namespaces of a pages, views, or components classes from the closest imports file in the directory tree, *_ViewImports.cshtml* (views or pages) or *_Imports.razor* (Razor components).</span></span>

```cshtml
@namespace Your.Namespace.Here
```

<span data-ttu-id="86203-252">Pour l' Razor exemple de pages illustré dans le tableau suivant :</span><span class="sxs-lookup"><span data-stu-id="86203-252">For the Razor Pages example shown in the following table:</span></span>

* <span data-ttu-id="86203-253">Chaque page importe *Pages/_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="86203-253">Each page imports *Pages/_ViewImports.cshtml*.</span></span>
* <span data-ttu-id="86203-254">*Pages/_ViewImports.cshtml* contient `@namespace Hello.World`.</span><span class="sxs-lookup"><span data-stu-id="86203-254">*Pages/_ViewImports.cshtml* contains `@namespace Hello.World`.</span></span>
* <span data-ttu-id="86203-255">Chaque page a `Hello.World` comme racine de son espace de noms.</span><span class="sxs-lookup"><span data-stu-id="86203-255">Each page has `Hello.World` as the root of it's namespace.</span></span>

| <span data-ttu-id="86203-256">Page</span><span class="sxs-lookup"><span data-stu-id="86203-256">Page</span></span>                                        | <span data-ttu-id="86203-257">Espace de noms</span><span class="sxs-lookup"><span data-stu-id="86203-257">Namespace</span></span>                             |
| ------------------------------------------- | ------------------------------------- |
| <span data-ttu-id="86203-258">*Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="86203-258">*Pages/Index.cshtml*</span></span>                        | `Hello.World`                         |
| <span data-ttu-id="86203-259">*Pages/MorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="86203-259">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages`               |
| <span data-ttu-id="86203-260">*Pages/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="86203-260">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Hello.World.MorePages.EvenMorePages` |

<span data-ttu-id="86203-261">Les relations précédentes s’appliquent aux fichiers d’importation utilisés avec les vues et les Razor composants Mvc.</span><span class="sxs-lookup"><span data-stu-id="86203-261">The preceding relationships apply to import files used with MVC views and Razor components.</span></span>

<span data-ttu-id="86203-262">Lorsque plusieurs fichiers d’importation ont une directive `@namespace`, le fichier le plus proche de la page, de la vue ou du composant dans l’arborescence de répertoires est utilisé pour définir l’espace de noms racine.</span><span class="sxs-lookup"><span data-stu-id="86203-262">When multiple import files have a `@namespace` directive, the file closest to the page, view, or component in the directory tree is used to set the root namespace.</span></span>

<span data-ttu-id="86203-263">Si le dossier *EvenMorePages* dans l’exemple précédent comprend un fichier d’importations avec `@namespace Another.Planet` (ou si le fichier *Pages/MorePages/EvenMorePages/Page.cshtml* contient `@namespace Another.Planet`), le résultat est indiqué dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="86203-263">If the *EvenMorePages* folder in the preceding example has an imports file with `@namespace Another.Planet` (or the *Pages/MorePages/EvenMorePages/Page.cshtml* file contains `@namespace Another.Planet`), the result is shown in the following table.</span></span>

| <span data-ttu-id="86203-264">Page</span><span class="sxs-lookup"><span data-stu-id="86203-264">Page</span></span>                                        | <span data-ttu-id="86203-265">Espace de noms</span><span class="sxs-lookup"><span data-stu-id="86203-265">Namespace</span></span>               |
| ------------------------------------------- | ----------------------- |
| <span data-ttu-id="86203-266">*Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="86203-266">*Pages/Index.cshtml*</span></span>                        | `Hello.World`           |
| <span data-ttu-id="86203-267">*Pages/MorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="86203-267">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages` |
| <span data-ttu-id="86203-268">*Pages/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="86203-268">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Another.Planet`        |

### `@page`

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="86203-269">La directive `@page` a des effets différents selon le type du fichier dans lequel elle apparaît.</span><span class="sxs-lookup"><span data-stu-id="86203-269">The `@page` directive has different effects depending on the type of the file where it appears.</span></span> <span data-ttu-id="86203-270">La directive :</span><span class="sxs-lookup"><span data-stu-id="86203-270">The directive:</span></span>

* <span data-ttu-id="86203-271">Dans un fichier *. cshtml* indique que le fichier est une Razor page.</span><span class="sxs-lookup"><span data-stu-id="86203-271">In a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="86203-272">Pour plus d’informations, consultez [itinéraires personnalisés](xref:razor-pages/index#custom-routes) et <xref:razor-pages/index> .</span><span class="sxs-lookup"><span data-stu-id="86203-272">For more information, see [Custom routes](xref:razor-pages/index#custom-routes) and <xref:razor-pages/index>.</span></span>
* <span data-ttu-id="86203-273">Spécifie qu’un Razor composant doit gérer les demandes directement.</span><span class="sxs-lookup"><span data-stu-id="86203-273">Specifies that a Razor component should handle requests directly.</span></span> <span data-ttu-id="86203-274">Pour plus d'informations, consultez <xref:blazor/fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="86203-274">For more information, see <xref:blazor/fundamentals/routing>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="86203-275">La `@page` directive sur la première ligne d’un fichier *. cshtml* indique que le fichier est une Razor page.</span><span class="sxs-lookup"><span data-stu-id="86203-275">The `@page` directive on the first line of a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="86203-276">Pour plus d'informations, consultez <xref:razor-pages/index>.</span><span class="sxs-lookup"><span data-stu-id="86203-276">For more information, see <xref:razor-pages/index>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

### `@preservewhitespace`

<span data-ttu-id="86203-277">*Ce scénario s’applique uniquement aux Razor composants ( `.razor` ).*</span><span class="sxs-lookup"><span data-stu-id="86203-277">*This scenario only applies to Razor components (`.razor`).*</span></span>

<span data-ttu-id="86203-278">Quand la valeur `false` est (valeur par défaut), l’espace blanc dans le balisage rendu à partir des Razor composants ( `.razor` ) est supprimé si :</span><span class="sxs-lookup"><span data-stu-id="86203-278">When set to `false` (default), whitespace in the rendered markup from Razor components (`.razor`) is removed if:</span></span>

* <span data-ttu-id="86203-279">À gauche ou à droite dans un élément.</span><span class="sxs-lookup"><span data-stu-id="86203-279">Leading or trailing within an element.</span></span>
* <span data-ttu-id="86203-280">De début ou de fin dans un `RenderFragment` paramètre.</span><span class="sxs-lookup"><span data-stu-id="86203-280">Leading or trailing within a `RenderFragment` parameter.</span></span> <span data-ttu-id="86203-281">Par exemple, le contenu enfant est passé à un autre composant.</span><span class="sxs-lookup"><span data-stu-id="86203-281">For example, child content passed to another component.</span></span>
* <span data-ttu-id="86203-282">Il précède ou suit un bloc de code C#, tel que `@if` ou `@foreach` .</span><span class="sxs-lookup"><span data-stu-id="86203-282">It precedes or follows a C# code block, such as `@if` or `@foreach`.</span></span>

::: moniker-end

### `@section`

<span data-ttu-id="86203-283">*Ce scénario s’applique uniquement aux affichages et Razor pages MVC (. cshtml).*</span><span class="sxs-lookup"><span data-stu-id="86203-283">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="86203-284">La `@section` directive est utilisée conjointement avec [MVC et les Razor mises en page de pages](xref:mvc/views/layout) pour permettre aux vues ou aux pages de restituer le contenu dans différentes parties de la page html.</span><span class="sxs-lookup"><span data-stu-id="86203-284">The `@section` directive is used in conjunction with [MVC and Razor Pages layouts](xref:mvc/views/layout) to enable views or pages to render content in different parts of the HTML page.</span></span> <span data-ttu-id="86203-285">Pour plus d'informations, consultez <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="86203-285">For more information, see <xref:mvc/views/layout>.</span></span>

### `@using`

<span data-ttu-id="86203-286">La directive `@using` ajoute la directive `using` C# à la vue générée :</span><span class="sxs-lookup"><span data-stu-id="86203-286">The `@using` directive adds the C# `using` directive to the generated view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="86203-287">Dans [ Razor composants](xref:blazor/components/index), `@using` contrôle également les composants qui sont dans la portée.</span><span class="sxs-lookup"><span data-stu-id="86203-287">In [Razor components](xref:blazor/components/index), `@using` also controls which components are in scope.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a><span data-ttu-id="86203-288">Attributs de directive</span><span class="sxs-lookup"><span data-stu-id="86203-288">Directive attributes</span></span>

<span data-ttu-id="86203-289">Razor les attributs de directive sont représentés par des expressions implicites avec des mots clés réservés qui suivent le `@` symbole.</span><span class="sxs-lookup"><span data-stu-id="86203-289">Razor directive attributes are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="86203-290">Un attribut de directive change généralement la manière dont un élément est analysé ou active des fonctionnalités différentes.</span><span class="sxs-lookup"><span data-stu-id="86203-290">A directive attribute typically changes the way an element is parsed or enables different functionality.</span></span>

### `@attributes`

<span data-ttu-id="86203-291">*Ce scénario s’applique uniquement aux Razor composants (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="86203-291">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="86203-292">`@attributes` permet à un composant de restituer des attributs non déclarés.</span><span class="sxs-lookup"><span data-stu-id="86203-292">`@attributes` allows a component to render non-declared attributes.</span></span> <span data-ttu-id="86203-293">Pour plus d'informations, consultez <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span><span class="sxs-lookup"><span data-stu-id="86203-293">For more information, see <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span></span>

### `@bind`

<span data-ttu-id="86203-294">*Ce scénario s’applique uniquement aux Razor composants (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="86203-294">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="86203-295">La liaison de données dans des composants s’effectue avec l’attribut `@bind`.</span><span class="sxs-lookup"><span data-stu-id="86203-295">Data binding in components is accomplished with the `@bind` attribute.</span></span> <span data-ttu-id="86203-296">Pour plus d'informations, consultez <xref:blazor/components/data-binding>.</span><span class="sxs-lookup"><span data-stu-id="86203-296">For more information, see <xref:blazor/components/data-binding>.</span></span>

### `@on{EVENT}`

<span data-ttu-id="86203-297">*Ce scénario s’applique uniquement aux Razor composants (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="86203-297">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="86203-298">Razor fournit des fonctionnalités de gestion des événements pour les composants.</span><span class="sxs-lookup"><span data-stu-id="86203-298">Razor provides event handling features for components.</span></span> <span data-ttu-id="86203-299">Pour plus d'informations, consultez <xref:blazor/components/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="86203-299">For more information, see <xref:blazor/components/event-handling>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### `@on{EVENT}:preventDefault`

<span data-ttu-id="86203-300">*Ce scénario s’applique uniquement aux Razor composants (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="86203-300">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="86203-301">Empêche l’action par défaut pour l’événement.</span><span class="sxs-lookup"><span data-stu-id="86203-301">Prevents the default action for the event.</span></span>

### `@on{EVENT}:stopPropagation`

<span data-ttu-id="86203-302">*Ce scénario s’applique uniquement aux Razor composants (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="86203-302">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="86203-303">Arrête la propagation d’événements pour l’événement.</span><span class="sxs-lookup"><span data-stu-id="86203-303">Stops event propagation for the event.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### `@key`

<span data-ttu-id="86203-304">*Ce scénario s’applique uniquement aux Razor composants (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="86203-304">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="86203-305">L’attribut de directive `@key` amène les composants à comparer l’algorithme afin de garantir la préservation des éléments ou des composants en fonction de la valeur de la clé.</span><span class="sxs-lookup"><span data-stu-id="86203-305">The `@key` directive attribute causes the components diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span> <span data-ttu-id="86203-306">Pour plus d'informations, consultez <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>.</span><span class="sxs-lookup"><span data-stu-id="86203-306">For more information, see <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>.</span></span>

### `@ref`

<span data-ttu-id="86203-307">*Ce scénario s’applique uniquement aux Razor composants (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="86203-307">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="86203-308">Les références de composants (`@ref`) permettent de référencer une instance de composant afin que vous puissiez émettre des commandes vers cette instance.</span><span class="sxs-lookup"><span data-stu-id="86203-308">Component references (`@ref`) provide a way to reference a component instance so that you can issue commands to that instance.</span></span> <span data-ttu-id="86203-309">Pour plus d'informations, consultez <xref:blazor/components/index#capture-references-to-components>.</span><span class="sxs-lookup"><span data-stu-id="86203-309">For more information, see <xref:blazor/components/index#capture-references-to-components>.</span></span>

### `@typeparam`

<span data-ttu-id="86203-310">*Ce scénario s’applique uniquement aux Razor composants (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="86203-310">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="86203-311">La `@typeparam` directive déclare un paramètre de type générique pour la classe de composant générée.</span><span class="sxs-lookup"><span data-stu-id="86203-311">The `@typeparam` directive declares a generic type parameter for the generated component class.</span></span> <span data-ttu-id="86203-312">Pour plus d'informations, consultez <xref:blazor/components/templated-components#generic-typed-components>.</span><span class="sxs-lookup"><span data-stu-id="86203-312">For more information, see <xref:blazor/components/templated-components#generic-typed-components>.</span></span>

::: moniker-end

## <a name="templated-no-locrazor-delegates"></a><span data-ttu-id="86203-313">Délégués basés sur un modèle Razor</span><span class="sxs-lookup"><span data-stu-id="86203-313">Templated Razor delegates</span></span>

<span data-ttu-id="86203-314">Razor les modèles vous permettent de définir un extrait de code d’interface utilisateur au format suivant :</span><span class="sxs-lookup"><span data-stu-id="86203-314">Razor templates allow you to define a UI snippet with the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="86203-315">L’exemple suivant montre comment spécifier un délégué basé sur Razor un modèle en tant que <xref:System.Func%602> .</span><span class="sxs-lookup"><span data-stu-id="86203-315">The following example illustrates how to specify a templated Razor delegate as a <xref:System.Func%602>.</span></span> <span data-ttu-id="86203-316">Le [type dynamique](/dotnet/csharp/programming-guide/types/using-type-dynamic) est spécifié pour le paramètre de la méthode encapsulée par le délégué.</span><span class="sxs-lookup"><span data-stu-id="86203-316">The [dynamic type](/dotnet/csharp/programming-guide/types/using-type-dynamic) is specified for the parameter of the method that the delegate encapsulates.</span></span> <span data-ttu-id="86203-317">Un [type objet](/dotnet/csharp/language-reference/keywords/object) est spécifié comme valeur de retour du délégué.</span><span class="sxs-lookup"><span data-stu-id="86203-317">An [object type](/dotnet/csharp/language-reference/keywords/object) is specified as the return value of the delegate.</span></span> <span data-ttu-id="86203-318">Le modèle est utilisé avec une <xref:System.Collections.Generic.List%601> de `Pet` qui a une propriété `Name`.</span><span class="sxs-lookup"><span data-stu-id="86203-318">The template is used with a <xref:System.Collections.Generic.List%601> of `Pet` that has a `Name` property.</span></span>

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

<span data-ttu-id="86203-319">Le modèle est rendu avec des éléments `pets` fournis par une instruction `foreach` :</span><span class="sxs-lookup"><span data-stu-id="86203-319">The template is rendered with `pets` supplied by a `foreach` statement:</span></span>

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

<span data-ttu-id="86203-320">Sortie rendue :</span><span class="sxs-lookup"><span data-stu-id="86203-320">Rendered output:</span></span>

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

<span data-ttu-id="86203-321">Vous pouvez également fournir un modèle inline Razor en tant qu’argument à une méthode.</span><span class="sxs-lookup"><span data-stu-id="86203-321">You can also supply an inline Razor template as an argument to a method.</span></span> <span data-ttu-id="86203-322">Dans l’exemple suivant, la `Repeat` méthode reçoit un Razor modèle.</span><span class="sxs-lookup"><span data-stu-id="86203-322">In the following example, the `Repeat` method receives a Razor template.</span></span> <span data-ttu-id="86203-323">La méthode utilise le modèle pour produire du contenu HTML avec des répétitions d’éléments fournis à partir d’une liste :</span><span class="sxs-lookup"><span data-stu-id="86203-323">The method uses the template to produce HTML content with repeats of items supplied from a list:</span></span>

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

<span data-ttu-id="86203-324">En utilisant la liste d’éléments « pets » de l’exemple précédent, la méthode `Repeat` est appelée avec :</span><span class="sxs-lookup"><span data-stu-id="86203-324">Using the list of pets from the prior example, the `Repeat` method is called with:</span></span>

* <span data-ttu-id="86203-325"><xref:System.Collections.Generic.List%601> de `Pet`.</span><span class="sxs-lookup"><span data-stu-id="86203-325"><xref:System.Collections.Generic.List%601> of `Pet`.</span></span>
* <span data-ttu-id="86203-326">Nombre de fois que chaque élément « pet » doit être répété.</span><span class="sxs-lookup"><span data-stu-id="86203-326">Number of times to repeat each pet.</span></span>
* <span data-ttu-id="86203-327">Modèle inline à utiliser pour les éléments de liste d’une liste non triée.</span><span class="sxs-lookup"><span data-stu-id="86203-327">Inline template to use for the list items of an unordered list.</span></span>

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

<span data-ttu-id="86203-328">Sortie rendue :</span><span class="sxs-lookup"><span data-stu-id="86203-328">Rendered output:</span></span>

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

## <a name="tag-helpers"></a><span data-ttu-id="86203-329">Tag Helpers</span><span class="sxs-lookup"><span data-stu-id="86203-329">Tag Helpers</span></span>

<span data-ttu-id="86203-330">*Ce scénario s’applique uniquement aux affichages et Razor pages MVC (. cshtml).*</span><span class="sxs-lookup"><span data-stu-id="86203-330">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="86203-331">Il existe trois directives spécifiques aux [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="86203-331">There are three directives that pertain to [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

| <span data-ttu-id="86203-332">Directive</span><span class="sxs-lookup"><span data-stu-id="86203-332">Directive</span></span> | <span data-ttu-id="86203-333">Fonction</span><span class="sxs-lookup"><span data-stu-id="86203-333">Function</span></span> |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | <span data-ttu-id="86203-334">Rend les Tag Helpers disponibles dans une vue.</span><span class="sxs-lookup"><span data-stu-id="86203-334">Makes Tag Helpers available to a view.</span></span> |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | <span data-ttu-id="86203-335">Supprime les Tag Helpers précédemment ajoutés à une vue.</span><span class="sxs-lookup"><span data-stu-id="86203-335">Removes Tag Helpers previously added from a view.</span></span> |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | <span data-ttu-id="86203-336">Spécifie un préfixe de balise pour activer la prise en charge des Tag Helpers et rendre leur usage explicite.</span><span class="sxs-lookup"><span data-stu-id="86203-336">Specifies a tag prefix to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> |

## <a name="no-locrazor-reserved-keywords"></a><span data-ttu-id="86203-337">Razor Mots clés réservés</span><span class="sxs-lookup"><span data-stu-id="86203-337">Razor reserved keywords</span></span>

### <a name="no-locrazor-keywords"></a><span data-ttu-id="86203-338">Razor mot</span><span class="sxs-lookup"><span data-stu-id="86203-338">Razor keywords</span></span>

* <span data-ttu-id="86203-339">`page` (Nécessite ASP.NET Core 2,1 ou version ultérieure)</span><span class="sxs-lookup"><span data-stu-id="86203-339">`page` (Requires ASP.NET Core 2.1 or later)</span></span>
* `namespace`
* `functions`
* `inherits`
* `model`
* `section`
* <span data-ttu-id="86203-340">`helper` (Actuellement pris en charge par ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="86203-340">`helper` (Not currently supported by ASP.NET Core)</span></span>

<span data-ttu-id="86203-341">Razor les mots clés sont placés dans une séquence d’échappement `@(Razor Keyword)` (par exemple, `@(functions)` ).</span><span class="sxs-lookup"><span data-stu-id="86203-341">Razor keywords are escaped with `@(Razor Keyword)` (for example, `@(functions)`).</span></span>

### <a name="c-no-locrazor-keywords"></a><span data-ttu-id="86203-342">RazorMots clés C#</span><span class="sxs-lookup"><span data-stu-id="86203-342">C# Razor keywords</span></span>

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

<span data-ttu-id="86203-343">Les Razor Mots clés C# doivent être double-échappés par `@(@C# Razor Keyword)` (par exemple, `@(@case)` ).</span><span class="sxs-lookup"><span data-stu-id="86203-343">C# Razor keywords must be double-escaped with `@(@C# Razor Keyword)` (for example, `@(@case)`).</span></span> <span data-ttu-id="86203-344">Le premier `@` échappe l' Razor analyseur.</span><span class="sxs-lookup"><span data-stu-id="86203-344">The first `@` escapes the Razor parser.</span></span> <span data-ttu-id="86203-345">La seconde séquence d’échappement `@` est pour l’analyseur C#.</span><span class="sxs-lookup"><span data-stu-id="86203-345">The second `@` escapes the C# parser.</span></span>

### <a name="reserved-keywords-not-used-by-no-locrazor"></a><span data-ttu-id="86203-346">Mots clés réservés non utilisés par Razor</span><span class="sxs-lookup"><span data-stu-id="86203-346">Reserved keywords not used by Razor</span></span>

* `class`

## <a name="inspect-the-no-locrazor-c-class-generated-for-a-view"></a><span data-ttu-id="86203-347">Inspecter la Razor classe C# générée pour une vue</span><span class="sxs-lookup"><span data-stu-id="86203-347">Inspect the Razor C# class generated for a view</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="86203-348">Avec kit SDK .NET Core 2,1 ou version ultérieure, le [ Razor Kit de développement logiciel (SDK)](xref:razor-pages/sdk) gère la compilation des Razor fichiers.</span><span class="sxs-lookup"><span data-stu-id="86203-348">With .NET Core SDK 2.1 or later, the [Razor SDK](xref:razor-pages/sdk) handles compilation of Razor files.</span></span> <span data-ttu-id="86203-349">Lors de la génération d’un projet, le Razor Kit de développement logiciel (SDK) génère un répertoire *obj/<build_configuration>/<target_framework_moniker>/ Razor* dans la racine du projet.</span><span class="sxs-lookup"><span data-stu-id="86203-349">When building a project, the Razor SDK generates an *obj/<build_configuration>/<target_framework_moniker>/Razor* directory in the project root.</span></span> <span data-ttu-id="86203-350">La structure de répertoires dans le *Razor* répertoire reflète la structure de répertoire du projet.</span><span class="sxs-lookup"><span data-stu-id="86203-350">The directory structure within the *Razor* directory mirrors the project's directory structure.</span></span>

<span data-ttu-id="86203-351">Considérez la structure de répertoires suivante dans un projet ASP.NET Core 2,1 Razor pages ciblant .net Core 2,1 :</span><span class="sxs-lookup"><span data-stu-id="86203-351">Consider the following directory structure in an ASP.NET Core 2.1 Razor Pages project targeting .NET Core 2.1:</span></span>

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

<span data-ttu-id="86203-352">La création du projet dans la configuration *Debug* génère le répertoire *obj* suivant :</span><span class="sxs-lookup"><span data-stu-id="86203-352">Building the project in *Debug* configuration yields the following *obj* directory:</span></span>

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

<span data-ttu-id="86203-353">Pour afficher la classe générée pour *pages/index. cshtml*, ouvrez *obj/Debug/netcoreapp 2.1/ Razor /pages/index.g.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="86203-353">To view the generated class for *Pages/Index.cshtml*, open *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="86203-354">Ajoutez la classe suivante au projet ASP.NET Core MVC :</span><span class="sxs-lookup"><span data-stu-id="86203-354">Add the following class to the ASP.NET Core MVC project:</span></span>

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

<span data-ttu-id="86203-355">Dans `Startup.ConfigureServices`, remplacez la classe `RazorTemplateEngine` ajoutée par MVC par la classe `CustomTemplateEngine` :</span><span class="sxs-lookup"><span data-stu-id="86203-355">In `Startup.ConfigureServices`, override the `RazorTemplateEngine` added by MVC with the `CustomTemplateEngine` class:</span></span>

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

<span data-ttu-id="86203-356">Définissez un point d’arrêt sur l’instruction `return csharpDocument;` de `CustomTemplateEngine`.</span><span class="sxs-lookup"><span data-stu-id="86203-356">Set a breakpoint on the `return csharpDocument;` statement of `CustomTemplateEngine`.</span></span> <span data-ttu-id="86203-357">Quand l’exécution du programme s’arrête au point d’arrêt, affichez la valeur de `generatedCode`.</span><span class="sxs-lookup"><span data-stu-id="86203-357">When program execution stops at the breakpoint, view the value of `generatedCode`.</span></span>

![Vue Visualiseur de texte du code généré](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a><span data-ttu-id="86203-359">Recherches de vues et respect de la casse</span><span class="sxs-lookup"><span data-stu-id="86203-359">View lookups and case sensitivity</span></span>

<span data-ttu-id="86203-360">Le Razor moteur d’affichage effectue des recherches respectant la casse pour les vues.</span><span class="sxs-lookup"><span data-stu-id="86203-360">The Razor view engine performs case-sensitive lookups for views.</span></span> <span data-ttu-id="86203-361">Toutefois, la recherche réellement effectuée est déterminée par le système de fichiers sous-jacent :</span><span class="sxs-lookup"><span data-stu-id="86203-361">However, the actual lookup is determined by the underlying file system:</span></span>

* <span data-ttu-id="86203-362">Source basé sur un fichier :</span><span class="sxs-lookup"><span data-stu-id="86203-362">File based source:</span></span>
  * <span data-ttu-id="86203-363">Sur les systèmes d’exploitation avec des systèmes de fichiers qui ne respectent pas la casse (par exemple, Windows), les recherches de fournisseurs de fichiers physiques ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="86203-363">On operating systems with case insensitive file systems (for example, Windows), physical file provider lookups are case insensitive.</span></span> <span data-ttu-id="86203-364">Par exemple, `return View("Test")` trouve les correspondances */Views/Home/Test.cshtml*, */Views/home/test.cshtml* et toutes les autres variantes de casse.</span><span class="sxs-lookup"><span data-stu-id="86203-364">For example, `return View("Test")` results in matches for */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, and any other casing variant.</span></span>
  * <span data-ttu-id="86203-365">Sur des systèmes de fichiers respectant la casse (par exemple, Linux, OSX, et avec `EmbeddedFileProvider`), les recherches respectent la casse.</span><span class="sxs-lookup"><span data-stu-id="86203-365">On case-sensitive file systems (for example, Linux, OSX, and with `EmbeddedFileProvider`), lookups are case-sensitive.</span></span> <span data-ttu-id="86203-366">Par exemple, `return View("Test")` trouve uniquement la correspondance */Views/Home/Test.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="86203-366">For example, `return View("Test")` specifically matches */Views/Home/Test.cshtml*.</span></span>
* <span data-ttu-id="86203-367">Vues précompilées : Avec ASP.NET Core 2.0 et les versions ultérieures, les recherches de vues précompilées ne respectent pas la casse, quels que soient les systèmes d’exploitation.</span><span class="sxs-lookup"><span data-stu-id="86203-367">Precompiled views: With ASP.NET Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span></span> <span data-ttu-id="86203-368">Le comportement est le même que celui du fournisseur de fichiers physiques sur Windows.</span><span class="sxs-lookup"><span data-stu-id="86203-368">The behavior is identical to physical file provider's behavior on Windows.</span></span> <span data-ttu-id="86203-369">Si deux vues précompilées diffèrent seulement par leur casse, le résultat de la recherche est non déterministe.</span><span class="sxs-lookup"><span data-stu-id="86203-369">If two precompiled views differ only in case, the result of lookup is non-deterministic.</span></span>

<span data-ttu-id="86203-370">Les développeurs doivent s’efforcer d’utiliser la même casse pour les noms de fichiers et de répertoires que pour les noms des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="86203-370">Developers are encouraged to match the casing of file and directory names to the casing of:</span></span>

* <span data-ttu-id="86203-371">Zone, contrôleur et action</span><span class="sxs-lookup"><span data-stu-id="86203-371">Area, controller, and action names.</span></span>
* <span data-ttu-id="86203-372">Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="86203-372">Razor Pages.</span></span>

<span data-ttu-id="86203-373">L’utilisation d’une casse identique garantit que les déploiements trouvent toujours les vues associées, indépendamment du système de fichiers sous-jacent.</span><span class="sxs-lookup"><span data-stu-id="86203-373">Matching case ensures the deployments find their views regardless of the underlying file system.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="86203-374">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="86203-374">Additional resources</span></span>

<span data-ttu-id="86203-375">[Présentation de la programmation Web ASP.net à l’aide de Razor La syntaxe](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) fournit de nombreux exemples de programmation à l’aide de la Razor syntaxe.</span><span class="sxs-lookup"><span data-stu-id="86203-375">[Introduction to ASP.NET Web Programming Using the Razor Syntax](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) provides many samples of programming with Razor syntax.</span></span>
