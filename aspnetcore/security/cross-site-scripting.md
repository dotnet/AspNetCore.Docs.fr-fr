---
title: Empêcher les scripts entre sites (XSS) dans ASP.NET Core
author: rick-anderson
description: Découvrez les scripts inter-sites (XSS) et les techniques permettant de résoudre cette vulnérabilité dans une application ASP.NET Core.
ms.author: riande
ms.date: 10/02/2018
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/cross-site-scripting
ms.openlocfilehash: 1c90a786efe8c3c205a729a2da9d3a99d0222012
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053083"
---
# <a name="prevent-cross-site-scripting-xss-in-aspnet-core"></a><span data-ttu-id="4c5d4-103">Empêcher les scripts entre sites (XSS) dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4c5d4-103">Prevent Cross-Site Scripting (XSS) in ASP.NET Core</span></span>

<span data-ttu-id="4c5d4-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4c5d4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4c5d4-105">XSS (cross-site scripting) est une faille de sécurité qui permet à une personne malveillante de placer des scripts côté client (généralement JavaScript) dans des pages Web.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-105">Cross-Site Scripting (XSS) is a security vulnerability which enables an attacker to place client side scripts (usually JavaScript) into web pages.</span></span> <span data-ttu-id="4c5d4-106">Quand d’autres utilisateurs chargent des pages affectées, les scripts de l’attaquant s’exécutent, ce qui permet à l’attaquant de voler des :::no-loc(cookie)::: jetons de session et de session, de modifier le contenu de la page Web par le biais de la manipulation DOM ou de rediriger le navigateur vers une autre page.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-106">When other users load affected pages the attacker's scripts will run, enabling the attacker to steal :::no-loc(cookie):::s and session tokens, change the contents of the web page through DOM manipulation or redirect the browser to another page.</span></span> <span data-ttu-id="4c5d4-107">Les vulnérabilités XSS se produisent généralement quand une application prend une entrée d’utilisateur et la renvoie à une page sans la valider, l’encoder ou la placer dans une séquence d’échappement.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-107">XSS vulnerabilities generally occur when an application takes user input and outputs it to a page without validating, encoding or escaping it.</span></span>

## <a name="protecting-your-application-against-xss"></a><span data-ttu-id="4c5d4-108">Protection de votre application contre XSS</span><span class="sxs-lookup"><span data-stu-id="4c5d4-108">Protecting your application against XSS</span></span>

<span data-ttu-id="4c5d4-109">À un niveau de base, XSS fonctionne en détrucnt votre application en insérant une `<script>` balise dans votre page rendue, ou en insérant un `On*` événement dans un élément.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-109">At a basic level XSS works by tricking your application into inserting a `<script>` tag into your rendered page, or by inserting an `On*` event into an element.</span></span> <span data-ttu-id="4c5d4-110">Les développeurs doivent utiliser les étapes de prévention suivantes pour éviter d’introduire du code XSS dans leur application.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-110">Developers should use the following prevention steps to avoid introducing XSS into their application.</span></span>

1. <span data-ttu-id="4c5d4-111">Ne placez jamais de données non fiables dans votre entrée HTML, sauf si vous suivez les autres étapes ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-111">Never put untrusted data into your HTML input, unless you follow the rest of the steps below.</span></span> <span data-ttu-id="4c5d4-112">Les données non approuvées sont toutes les données qui peuvent être contrôlées par une personne malveillante, les entrées de formulaire HTML, les chaînes de requête, les en-têtes HTTP, même les données provenant d’une base de données comme une personne malveillante peuvent être en mesure de violer votre base de données, même si elles ne peuvent pas violer votre application.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-112">Untrusted data is any data that may be controlled by an attacker, HTML form inputs, query strings, HTTP headers, even data sourced from a database as an attacker may be able to breach your database even if they cannot breach your application.</span></span>

2. <span data-ttu-id="4c5d4-113">Avant de placer des données non fiables à l’intérieur d’un élément HTML, assurez-vous qu’elles sont encodées en HTML.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-113">Before putting untrusted data inside an HTML element ensure it's HTML encoded.</span></span> <span data-ttu-id="4c5d4-114">L’encodage HTML prend des caractères tels que &lt; et les modifie dans un format sécurisé comme &amp; lt ;</span><span class="sxs-lookup"><span data-stu-id="4c5d4-114">HTML encoding takes characters such as &lt; and changes them into a safe form like &amp;lt;</span></span>

3. <span data-ttu-id="4c5d4-115">Avant de placer des données non fiables dans un attribut HTML, assurez-vous qu’elles sont encodées en HTML.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-115">Before putting untrusted data into an HTML attribute ensure it's HTML encoded.</span></span> <span data-ttu-id="4c5d4-116">L’encodage d’attribut HTML est un sur-ensemble de l’encodage HTML et encode des caractères supplémentaires tels que « et ».</span><span class="sxs-lookup"><span data-stu-id="4c5d4-116">HTML attribute encoding is a superset of HTML encoding and encodes additional characters such as " and '.</span></span>

4. <span data-ttu-id="4c5d4-117">Avant de placer des données non fiables dans JavaScript, placez les données dans un élément HTML dont vous récupérez le contenu au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-117">Before putting untrusted data into JavaScript place the data in an HTML element whose contents you retrieve at runtime.</span></span> <span data-ttu-id="4c5d4-118">Si ce n’est pas possible, assurez-vous que les données sont encodées en JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-118">If this isn't possible, then ensure the data is JavaScript encoded.</span></span> <span data-ttu-id="4c5d4-119">L’encodage JavaScript prend des caractères dangereux pour JavaScript et les remplace par leur Hex, par exemple &lt; est encodé sous la forme `\u003C` .</span><span class="sxs-lookup"><span data-stu-id="4c5d4-119">JavaScript encoding takes dangerous characters for JavaScript and replaces them with their hex, for example &lt; would be encoded as `\u003C`.</span></span>

5. <span data-ttu-id="4c5d4-120">Avant de placer des données non fiables dans une chaîne de requête d’URL, assurez-vous qu’elles sont encodées dans l’URL.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-120">Before putting untrusted data into a URL query string ensure it's URL encoded.</span></span>

## <a name="html-encoding-using-no-locrazor"></a><span data-ttu-id="4c5d4-121">Encodage HTML à l’aide de :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="4c5d4-121">HTML Encoding using :::no-loc(Razor):::</span></span>

<span data-ttu-id="4c5d4-122">Le :::no-loc(Razor)::: moteur utilisé dans MVC encode automatiquement toute la sortie source à partir de variables, sauf si vous travaillez vraiment difficile pour l’empêcher.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-122">The :::no-loc(Razor)::: engine used in MVC automatically encodes all output sourced from variables, unless you work really hard to prevent it doing so.</span></span> <span data-ttu-id="4c5d4-123">Elle utilise des règles d’encodage d’attribut HTML chaque fois que vous utilisez la *@* directive.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-123">It uses HTML attribute encoding rules whenever you use the *@* directive.</span></span> <span data-ttu-id="4c5d4-124">Comme l’encodage d’attribut HTML est un sur-ensemble de l’encodage HTML, vous n’avez pas à vous soucier de savoir si vous devez utiliser l’encodage HTML ou l’encodage d’attribut HTML.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-124">As HTML attribute encoding is a superset of HTML encoding this means you don't have to concern yourself with whether you should use HTML encoding or HTML attribute encoding.</span></span> <span data-ttu-id="4c5d4-125">Vous devez vous assurer que vous utilisez uniquement @ dans un contexte HTML, et non pas lors d’une tentative d’insertion d’une entrée non approuvée directement dans JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-125">You must ensure that you only use @ in an HTML context, not when attempting to insert untrusted input directly into JavaScript.</span></span> <span data-ttu-id="4c5d4-126">Les tag helpers encodent également l’entrée que vous utilisez dans les paramètres de balise.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-126">Tag helpers will also encode input you use in tag parameters.</span></span>

<span data-ttu-id="4c5d4-127">Prenez la :::no-loc(Razor)::: vue suivante :</span><span class="sxs-lookup"><span data-stu-id="4c5d4-127">Take the following :::no-loc(Razor)::: view:</span></span>

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   @untrustedInput
   ```

<span data-ttu-id="4c5d4-128">Cette vue renvoie le contenu de la variable *untrustedInput* .</span><span class="sxs-lookup"><span data-stu-id="4c5d4-128">This view outputs the contents of the *untrustedInput* variable.</span></span> <span data-ttu-id="4c5d4-129">Cette variable comprend certains caractères qui sont utilisés dans les attaques XSS, à savoir &lt; «et &gt; .</span><span class="sxs-lookup"><span data-stu-id="4c5d4-129">This variable includes some characters which are used in XSS attacks, namely &lt;, " and &gt;.</span></span> <span data-ttu-id="4c5d4-130">L’examen de la source montre la sortie rendue encodée comme suit :</span><span class="sxs-lookup"><span data-stu-id="4c5d4-130">Examining the source shows the rendered output encoded as:</span></span>

```html
&lt;&quot;123&quot;&gt;
   ```

>[!WARNING]
> <span data-ttu-id="4c5d4-131">ASP.NET Core MVC fournit une `HtmlString` classe qui n’est pas automatiquement encodée au moment de la sortie.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-131">ASP.NET Core MVC provides an `HtmlString` class which isn't automatically encoded upon output.</span></span> <span data-ttu-id="4c5d4-132">Cela ne doit jamais être utilisé en combinaison avec une entrée non fiable, car cela expose une vulnérabilité XSS.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-132">This should never be used in combination with untrusted input as this will expose an XSS vulnerability.</span></span>

## <a name="javascript-encoding-using-no-locrazor"></a><span data-ttu-id="4c5d4-133">Encodage JavaScript à l’aide de :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="4c5d4-133">JavaScript Encoding using :::no-loc(Razor):::</span></span>

<span data-ttu-id="4c5d4-134">Il peut arriver que vous souhaitiez insérer une valeur dans JavaScript à traiter dans votre vue.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-134">There may be times you want to insert a value into JavaScript to process in your view.</span></span> <span data-ttu-id="4c5d4-135">Il existe deux façons d'effectuer cette opération.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-135">There are two ways to do this.</span></span> <span data-ttu-id="4c5d4-136">Le moyen le plus sûr d’insérer des valeurs consiste à placer la valeur dans un attribut de données d’une balise et à la récupérer dans votre code JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-136">The safest way to insert values is to place the value in a data attribute of a tag and retrieve it in your JavaScript.</span></span> <span data-ttu-id="4c5d4-137">Exemple :</span><span class="sxs-lookup"><span data-stu-id="4c5d4-137">For example:</span></span>

```cshtml
@{
    var untrustedInput = "<script>alert(1)</script>";
}

<div id="injectedData"
     data-untrustedinput="@untrustedInput" />

<div id="scriptedWrite" />
<div id="scriptedWrite-html5" />

<script>
    var injectedData = document.getElementById("injectedData");

    // All clients
    var clientSideUntrustedInputOldStyle =
        injectedData.getAttribute("data-untrustedinput");

    // HTML 5 clients only
    var clientSideUntrustedInputHtml5 =
        injectedData.dataset.untrustedinput;

    // Put the injected, untrusted data into the scriptedWrite div tag.
    // Do NOT use document.write() on dynamically generated data as it
    // can lead to XSS.

    document.getElementById("scriptedWrite").innerText += clientSideUntrustedInputOldStyle;

    // Or you can use createElement() to dynamically create document elements
    // This time we're using textContent to ensure the data is properly encoded.
    var x = document.createElement("div");
    x.textContent = clientSideUntrustedInputHtml5;
    document.body.appendChild(x);

    // You can also use createTextNode on an element to ensure data is properly encoded.
    var y = document.createElement("div");
    y.appendChild(document.createTextNode(clientSideUntrustedInputHtml5));
    document.body.appendChild(y);

</script>
   ```

<span data-ttu-id="4c5d4-138">Le balisage ci-dessus génère le code HTML suivant :</span><span class="sxs-lookup"><span data-stu-id="4c5d4-138">The preceding markup generates the following HTML:</span></span>

```html
<div id="injectedData"
     data-untrustedinput="&lt;script&gt;alert(1)&lt;/script&gt;" />

<div id="scriptedWrite" />
<div id="scriptedWrite-html5" />

<script>
    var injectedData = document.getElementById("injectedData");

    // All clients
    var clientSideUntrustedInputOldStyle =
        injectedData.getAttribute("data-untrustedinput");

    // HTML 5 clients only
    var clientSideUntrustedInputHtml5 =
        injectedData.dataset.untrustedinput;

    // Put the injected, untrusted data into the scriptedWrite div tag.
    // Do NOT use document.write() on dynamically generated data as it can
    // lead to XSS.

    document.getElementById("scriptedWrite").innerText += clientSideUntrustedInputOldStyle;

    // Or you can use createElement() to dynamically create document elements
    // This time we're using textContent to ensure the data is properly encoded.
    var x = document.createElement("div");
    x.textContent = clientSideUntrustedInputHtml5;
    document.body.appendChild(x);

    // You can also use createTextNode on an element to ensure data is properly encoded.
    var y = document.createElement("div");
    y.appendChild(document.createTextNode(clientSideUntrustedInputHtml5));
    document.body.appendChild(y);

</script>
   ```

<span data-ttu-id="4c5d4-139">Le code précédent génère la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="4c5d4-139">The preceding code generates the following output:</span></span>

```
<script>alert(1)</script>
<script>alert(1)</script>
<script>alert(1)</script>
```

>[!WARNING]
> <span data-ttu-id="4c5d4-140">Do \* **not** _ concaténer une entrée non fiable dans JavaScript pour créer des éléments DOM ou utiliser `document.write()` sur du contenu généré dynamiquement.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-140">Do \* **NOT** _ concatenate untrusted input in JavaScript to create DOM elements or use `document.write()` on dynamically generated content.</span></span>
>
> <span data-ttu-id="4c5d4-141">Utilisez l’une des approches suivantes pour empêcher le code d’être exposé au code XSS basé sur DOM : _ `createElement()` et assigner des valeurs de propriété avec des méthodes ou des propriétés appropriées telles que `node.textContent=` ou node. InnerText = '.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-141">Use one of the following approaches to prevent code from being exposed to DOM-based XSS: _ `createElement()` and assign property values with appropriate methods or properties such as `node.textContent=` or node.InnerText=\`.</span></span>
> * <span data-ttu-id="4c5d4-142">`document.CreateTextNode()` et l’ajouter à l’emplacement DOM approprié.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-142">`document.CreateTextNode()` and append it in the appropriate DOM location.</span></span>
> * `element.SetAttribute()`
> * `element[attribute]=`

## <a name="accessing-encoders-in-code"></a><span data-ttu-id="4c5d4-143">Accès aux encodeurs dans le code</span><span class="sxs-lookup"><span data-stu-id="4c5d4-143">Accessing encoders in code</span></span>

<span data-ttu-id="4c5d4-144">Les encodeurs HTML, JavaScript et d’URL sont disponibles pour votre code de deux manières, vous pouvez les injecter via l' [injection de dépendances](xref:fundamentals/dependency-injection) ou vous pouvez utiliser les encodeurs par défaut contenus dans l' `System.Text.Encodings.Web` espace de noms.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-144">The HTML, JavaScript and URL encoders are available to your code in two ways, you can inject them via [dependency injection](xref:fundamentals/dependency-injection) or you can use the default encoders contained in the `System.Text.Encodings.Web` namespace.</span></span> <span data-ttu-id="4c5d4-145">Si vous utilisez les encodeurs par défaut, tout ce que vous avez appliqué aux plages de caractères à traiter comme sécurisé ne prendra pas effet : les encodeurs par défaut utilisent les règles d’encodage les plus sûres possibles.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-145">If you use the default encoders then any  you applied to character ranges to be treated as safe won't take effect - the default encoders use the safest encoding rules possible.</span></span>

<span data-ttu-id="4c5d4-146">Pour utiliser les encodeurs configurables via l’injection de code, les constructeurs doivent accepter un paramètre *HtmlEncoder* , *JavaScriptEncoder* et *UrlEncoder* , selon le cas.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-146">To use the configurable encoders via DI your constructors should take an *HtmlEncoder* , *JavaScriptEncoder* and *UrlEncoder* parameter as appropriate.</span></span> <span data-ttu-id="4c5d4-147">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="4c5d4-147">For example;</span></span>

```csharp
public class HomeController : Controller
   {
       HtmlEncoder _htmlEncoder;
       JavaScriptEncoder _javaScriptEncoder;
       UrlEncoder _urlEncoder;

       public HomeController(HtmlEncoder htmlEncoder,
                             JavaScriptEncoder javascriptEncoder,
                             UrlEncoder urlEncoder)
       {
           _htmlEncoder = htmlEncoder;
           _javaScriptEncoder = javascriptEncoder;
           _urlEncoder = urlEncoder;
       }
   }
   ```

## <a name="encoding-url-parameters"></a><span data-ttu-id="4c5d4-148">Encodage des paramètres d’URL</span><span class="sxs-lookup"><span data-stu-id="4c5d4-148">Encoding URL Parameters</span></span>

<span data-ttu-id="4c5d4-149">Si vous souhaitez créer une chaîne de requête d’URL avec une entrée non approuvée comme valeur, utilisez `UrlEncoder` pour encoder la valeur.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-149">If you want to build a URL query string with untrusted input as a value use the `UrlEncoder` to encode the value.</span></span> <span data-ttu-id="4c5d4-150">Par exemple,</span><span class="sxs-lookup"><span data-stu-id="4c5d4-150">For example,</span></span>

```csharp
var example = "\"Quoted Value with spaces and &\"";
   var encodedValue = _urlEncoder.Encode(example);
   ```

<span data-ttu-id="4c5d4-151">Après l’encodage, la variable encodedValue contient `%22Quoted%20Value%20with%20spaces%20and%20%26%22` .</span><span class="sxs-lookup"><span data-stu-id="4c5d4-151">After encoding the encodedValue variable will contain `%22Quoted%20Value%20with%20spaces%20and%20%26%22`.</span></span> <span data-ttu-id="4c5d4-152">Les espaces, les guillemets, les signes de ponctuation et autres caractères non sécurisés sont codés en pourcentage à leur valeur hexadécimale, par exemple, un espace devient %20.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-152">Spaces, quotes, punctuation and other unsafe characters will be percent encoded to their hexadecimal value, for example a space character will become %20.</span></span>

>[!WARNING]
> <span data-ttu-id="4c5d4-153">N’utilisez pas d’entrée non approuvée dans le cadre d’un chemin d’URL.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-153">Don't use untrusted input as part of a URL path.</span></span> <span data-ttu-id="4c5d4-154">Transmettez toujours une entrée non fiable en tant que valeur de chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-154">Always pass untrusted input as a query string value.</span></span>

<a name="security-cross-site-scripting-customization"></a>

## <a name="customizing-the-encoders"></a><span data-ttu-id="4c5d4-155">Personnalisation des encodeurs</span><span class="sxs-lookup"><span data-stu-id="4c5d4-155">Customizing the Encoders</span></span>

<span data-ttu-id="4c5d4-156">Par défaut, les encodeurs utilisent une liste sécurisée limitée à la plage Unicode latin de base et encodent tous les caractères en dehors de cette plage comme équivalents de code de caractère.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-156">By default encoders use a safe list limited to the Basic Latin Unicode range and encode all characters outside of that range as their character code equivalents.</span></span> <span data-ttu-id="4c5d4-157">Ce comportement affecte également :::no-loc(Razor)::: le rendu TagHelper et HtmlHelper, car il utilise les encodeurs pour générer vos chaînes.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-157">This behavior also affects :::no-loc(Razor)::: TagHelper and HtmlHelper rendering as it will use the encoders to output your strings.</span></span>

<span data-ttu-id="4c5d4-158">Le raisonnement derrière cela est de se protéger contre les bogues de navigateurs inconnus ou futurs (les bogues de navigateur précédents ont dépassé l’analyse en fonction du traitement de caractères non anglais).</span><span class="sxs-lookup"><span data-stu-id="4c5d4-158">The reasoning behind this is to protect against unknown or future browser bugs (previous browser bugs have tripped up parsing based on the processing of non-English characters).</span></span> <span data-ttu-id="4c5d4-159">Si votre site Web utilise beaucoup de caractères non latins, tels que le chinois, le cyrillique ou d’autres, il ne s’agit probablement pas du comportement que vous souhaitez.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-159">If your web site makes heavy use of non-Latin characters, such as Chinese, Cyrillic or others this is probably not the behavior you want.</span></span>

<span data-ttu-id="4c5d4-160">Vous pouvez personnaliser les listes sécurisées de l’encodeur pour inclure les plages Unicode appropriées à votre application au démarrage, dans `ConfigureServices()` .</span><span class="sxs-lookup"><span data-stu-id="4c5d4-160">You can customize the encoder safe lists to include Unicode ranges appropriate to your application during startup, in `ConfigureServices()`.</span></span>

<span data-ttu-id="4c5d4-161">Par exemple, en utilisant la configuration par défaut, vous pouvez utiliser une HtmlHelper telle que celle-ci :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="4c5d4-161">For example, using the default configuration you might use a :::no-loc(Razor)::: HtmlHelper like so;</span></span>

```html
<p>This link text is in Chinese: @Html.ActionLink("汉语/漢語", "Index")</p>
   ```

<span data-ttu-id="4c5d4-162">Lorsque vous affichez la source de la page Web, vous voyez qu’elle a été rendue comme suit, avec l’encodage de texte en chinois ;</span><span class="sxs-lookup"><span data-stu-id="4c5d4-162">When you view the source of the web page you will see it has been rendered as follows, with the Chinese text encoded;</span></span>

```html
<p>This link text is in Chinese: <a href="/">&#x6C49;&#x8BED;/&#x6F22;&#x8A9E;</a></p>
   ```

<span data-ttu-id="4c5d4-163">Pour élargir les caractères traités comme sécurisés par l’encodeur, insérez la ligne suivante dans la `ConfigureServices()` méthode dans `startup.cs` ;</span><span class="sxs-lookup"><span data-stu-id="4c5d4-163">To widen the characters treated as safe by the encoder you would insert the following line into the `ConfigureServices()` method in `startup.cs`;</span></span>

```csharp
services.AddSingleton<HtmlEncoder>(
     HtmlEncoder.Create(allowedRanges: new[] { UnicodeRanges.BasicLatin,
                                               UnicodeRanges.CjkUnifiedIdeographs }));
   ```

<span data-ttu-id="4c5d4-164">Cet exemple élargit la liste sécurisée pour inclure la plage Unicode CjkUnifiedIdeographs.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-164">This example widens the safe list to include the Unicode Range CjkUnifiedIdeographs.</span></span> <span data-ttu-id="4c5d4-165">La sortie rendue devient alors</span><span class="sxs-lookup"><span data-stu-id="4c5d4-165">The rendered output would now become</span></span>

```html
<p>This link text is in Chinese: <a href="/">汉语/漢語</a></p>
   ```

<span data-ttu-id="4c5d4-166">Les plages de liste sécurisée sont spécifiées sous la forme de graphiques de code Unicode, et non de langues.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-166">Safe list ranges are specified as Unicode code charts, not languages.</span></span> <span data-ttu-id="4c5d4-167">La [norme Unicode](https://unicode.org/) contient une liste de [graphiques de code](https://www.unicode.org/charts/index.html) que vous pouvez utiliser pour rechercher le graphique contenant vos caractères.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-167">The [Unicode standard](https://unicode.org/) has a list of [code charts](https://www.unicode.org/charts/index.html) you can use to find the chart containing your characters.</span></span> <span data-ttu-id="4c5d4-168">Chaque encodeur, html, JavaScript et URL doivent être configurés séparément.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-168">Each encoder, Html, JavaScript and Url, must be configured separately.</span></span>

> [!NOTE]
> <span data-ttu-id="4c5d4-169">La personnalisation de la liste sécurisée affecte uniquement les encodeurs provenant de l’injection de sécurité.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-169">Customization of the safe list only affects encoders sourced via DI.</span></span> <span data-ttu-id="4c5d4-170">Si vous accédez directement à un encodeur par `System.Text.Encodings.Web.*Encoder.Default` le biais de, la valeur par défaut est l’utilisation de la safeli Basic latin only.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-170">If you directly access an encoder via `System.Text.Encodings.Web.*Encoder.Default` then the default, Basic Latin only safelist will be used.</span></span>

## <a name="where-should-encoding-take-place"></a><span data-ttu-id="4c5d4-171">Où l’encodage doit-il avoir lieu ?</span><span class="sxs-lookup"><span data-stu-id="4c5d4-171">Where should encoding take place?</span></span>

<span data-ttu-id="4c5d4-172">La pratique générale acceptée est que l’encodage a lieu au point de sortie et que les valeurs encodées ne doivent jamais être stockées dans une base de données.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-172">The general accepted practice is that encoding takes place at the point of output and encoded values should never be stored in a database.</span></span> <span data-ttu-id="4c5d4-173">L’encodage au point de sortie vous permet de modifier l’utilisation des données, par exemple du code HTML à une valeur de chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-173">Encoding at the point of output allows you to change the use of data, for example, from HTML to a query string value.</span></span> <span data-ttu-id="4c5d4-174">Elle vous permet également de rechercher facilement vos données sans avoir à encoder les valeurs avant de les Rechercher et vous permet de tirer parti des modifications ou des correctifs de bogues apportés aux encodeurs.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-174">It also enables you to easily search your data without having to encode values before searching and allows you to take advantage of any changes or bug fixes made to encoders.</span></span>

## <a name="validation-as-an-xss-prevention-technique"></a><span data-ttu-id="4c5d4-175">Validation en tant que technique de prévention XSS</span><span class="sxs-lookup"><span data-stu-id="4c5d4-175">Validation as an XSS prevention technique</span></span>

<span data-ttu-id="4c5d4-176">La validation peut être un outil utile pour limiter les attaques XSS.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-176">Validation can be a useful tool in limiting XSS attacks.</span></span> <span data-ttu-id="4c5d4-177">Par exemple, une chaîne numérique contenant uniquement les caractères 0-9 ne déclenchera pas d’attaque XSS.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-177">For example, a numeric string containing only the characters 0-9 won't trigger an XSS attack.</span></span> <span data-ttu-id="4c5d4-178">La validation devient plus complexe lors de l’acceptation du code HTML dans l’entrée utilisateur.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-178">Validation becomes more complicated when accepting HTML in user input.</span></span> <span data-ttu-id="4c5d4-179">L’analyse de l’entrée HTML est difficile, voire impossible.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-179">Parsing HTML input is difficult, if not impossible.</span></span> <span data-ttu-id="4c5d4-180">La démarque, couplée avec un analyseur qui supprime le code HTML incorporé, est une option plus sûre pour accepter une entrée enrichie.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-180">Markdown, coupled with a parser that strips embedded HTML, is a safer option for accepting rich input.</span></span> <span data-ttu-id="4c5d4-181">Ne vous fiez jamais à la validation.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-181">Never rely on validation alone.</span></span> <span data-ttu-id="4c5d4-182">Encodez toujours les entrées non approuvées avant la sortie, quelle que soit la validation ou l’assainissement effectué.</span><span class="sxs-lookup"><span data-stu-id="4c5d4-182">Always encode untrusted input before output, no matter what validation or sanitization has been performed.</span></span>
