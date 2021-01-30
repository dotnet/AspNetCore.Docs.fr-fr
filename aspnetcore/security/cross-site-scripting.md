---
title: Empêcher les scripts entre sites (XSS) dans ASP.NET Core
author: rick-anderson
description: Découvrez les scripts inter-sites (XSS) et les techniques permettant de résoudre cette vulnérabilité dans une application ASP.NET Core.
ms.author: riande
ms.date: 10/02/2018
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
uid: security/cross-site-scripting
ms.openlocfilehash: a7a0c0ff44de5b04d7fa9a8f2f16f7c9f786f64b
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057068"
---
# <a name="prevent-cross-site-scripting-xss-in-aspnet-core"></a>Empêcher les scripts entre sites (XSS) dans ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

XSS (cross-site scripting) est une faille de sécurité qui permet à une personne malveillante de placer des scripts côté client (généralement JavaScript) dans des pages Web. Quand d’autres utilisateurs chargent des pages affectées, les scripts de l’attaquant s’exécutent, ce qui permet à l’attaquant de voler des cookie jetons de session et de session, de modifier le contenu de la page Web par le biais de la manipulation DOM ou de rediriger le navigateur vers une autre page. Les vulnérabilités XSS se produisent généralement quand une application prend une entrée d’utilisateur et la renvoie à une page sans la valider, l’encoder ou la placer dans une séquence d’échappement.

## <a name="protecting-your-application-against-xss"></a>Protection de votre application contre XSS

À un niveau de base, XSS fonctionne en détrucnt votre application en insérant une `<script>` balise dans votre page rendue, ou en insérant un `On*` événement dans un élément. Les développeurs doivent utiliser les étapes de prévention suivantes pour éviter d’introduire du code XSS dans leur application.

1. Ne placez jamais de données non fiables dans votre entrée HTML, sauf si vous suivez les autres étapes ci-dessous. Les données non approuvées sont toutes les données qui peuvent être contrôlées par une personne malveillante, les entrées de formulaire HTML, les chaînes de requête, les en-têtes HTTP, même les données provenant d’une base de données comme une personne malveillante peuvent être en mesure de violer votre base de données, même si elles ne peuvent pas violer votre application.

2. Avant de placer des données non fiables à l’intérieur d’un élément HTML, assurez-vous qu’elles sont encodées en HTML. L’encodage HTML prend des caractères tels que &lt; et les modifie dans un format sécurisé comme &amp; lt ;

3. Avant de placer des données non fiables dans un attribut HTML, assurez-vous qu’elles sont encodées en HTML. L’encodage d’attribut HTML est un sur-ensemble de l’encodage HTML et encode des caractères supplémentaires tels que « et ».

4. Avant de placer des données non fiables dans JavaScript, placez les données dans un élément HTML dont vous récupérez le contenu au moment de l’exécution. Si ce n’est pas possible, assurez-vous que les données sont encodées en JavaScript. L’encodage JavaScript prend des caractères dangereux pour JavaScript et les remplace par leur Hex, par exemple &lt; est encodé sous la forme `\u003C` .

5. Avant de placer des données non fiables dans une chaîne de requête d’URL, assurez-vous qu’elles sont encodées dans l’URL.

## <a name="html-encoding-using-no-locrazor"></a>Encodage HTML à l’aide de Razor

Le Razor moteur utilisé dans MVC encode automatiquement toute la sortie source à partir de variables, sauf si vous travaillez vraiment difficile pour l’empêcher. Elle utilise des règles d’encodage d’attribut HTML chaque fois que vous utilisez la *@* directive. Comme l’encodage d’attribut HTML est un sur-ensemble de l’encodage HTML, vous n’avez pas à vous soucier de savoir si vous devez utiliser l’encodage HTML ou l’encodage d’attribut HTML. Vous devez vous assurer que vous utilisez uniquement @ dans un contexte HTML, et non pas lors d’une tentative d’insertion d’une entrée non approuvée directement dans JavaScript. Les tag helpers encodent également l’entrée que vous utilisez dans les paramètres de balise.

Prenez la Razor vue suivante :

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   @untrustedInput
   ```

Cette vue renvoie le contenu de la variable *untrustedInput* . Cette variable comprend certains caractères qui sont utilisés dans les attaques XSS, à savoir &lt; «et &gt; . L’examen de la source montre la sortie rendue encodée comme suit :

```html
&lt;&quot;123&quot;&gt;
   ```

>[!WARNING]
> ASP.NET Core MVC fournit une `HtmlString` classe qui n’est pas automatiquement encodée au moment de la sortie. Cela ne doit jamais être utilisé en combinaison avec une entrée non fiable, car cela expose une vulnérabilité XSS.

## <a name="javascript-encoding-using-no-locrazor"></a>Encodage JavaScript à l’aide de Razor

Il peut arriver que vous souhaitiez insérer une valeur dans JavaScript à traiter dans votre vue. Il existe deux façons d'effectuer cette opération. Le moyen le plus sûr d’insérer des valeurs consiste à placer la valeur dans un attribut de données d’une balise et à la récupérer dans votre code JavaScript. Par exemple :

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

Le balisage ci-dessus génère le code HTML suivant :

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

Le code précédent génère la sortie suivante :

```
<script>alert(1)</script>
<script>alert(1)</script>
<script>alert(1)</script>
```

>[!WARNING]
> Do ***not** _ concaténer une entrée non fiable dans JavaScript pour créer des éléments DOM ou utiliser `document.write()` sur du contenu généré dynamiquement.
>
> Utilisez l’une des approches suivantes pour empêcher le code d’être exposé au code XSS basé sur DOM : _ `createElement()` et assignez des valeurs de propriété avec des méthodes ou des propriétés appropriées, telles que `node.textContent=` ou `node.InnerText=` .
> * `document.CreateTextNode()` et l’ajouter à l’emplacement DOM approprié.
> * `element.SetAttribute()`
> * `element[attribute]=`

## <a name="accessing-encoders-in-code"></a>Accès aux encodeurs dans le code

Les encodeurs HTML, JavaScript et d’URL sont disponibles pour votre code de deux manières, vous pouvez les injecter via l' [injection de dépendances](xref:fundamentals/dependency-injection) ou vous pouvez utiliser les encodeurs par défaut contenus dans l' `System.Text.Encodings.Web` espace de noms. Si vous utilisez les encodeurs par défaut, tout ce que vous avez appliqué aux plages de caractères à traiter comme sécurisé ne prendra pas effet : les encodeurs par défaut utilisent les règles d’encodage les plus sûres possibles.

Pour utiliser les encodeurs configurables via l’injection de code, les constructeurs doivent accepter un paramètre *HtmlEncoder*, *JavaScriptEncoder* et *UrlEncoder* , selon le cas. Par exemple :

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

## <a name="encoding-url-parameters"></a>Encodage des paramètres d’URL

Si vous souhaitez créer une chaîne de requête d’URL avec une entrée non approuvée comme valeur, utilisez `UrlEncoder` pour encoder la valeur. Par exemple,

```csharp
var example = "\"Quoted Value with spaces and &\"";
   var encodedValue = _urlEncoder.Encode(example);
   ```

Après l’encodage, la variable encodedValue contient `%22Quoted%20Value%20with%20spaces%20and%20%26%22` . Les espaces, les guillemets, les signes de ponctuation et autres caractères non sécurisés sont codés en pourcentage à leur valeur hexadécimale, par exemple, un espace devient %20.

>[!WARNING]
> N’utilisez pas d’entrée non approuvée dans le cadre d’un chemin d’URL. Transmettez toujours une entrée non fiable en tant que valeur de chaîne de requête.

<a name="security-cross-site-scripting-customization"></a>

## <a name="customizing-the-encoders"></a>Personnalisation des encodeurs

Par défaut, les encodeurs utilisent une liste sécurisée limitée à la plage Unicode latin de base et encodent tous les caractères en dehors de cette plage comme équivalents de code de caractère. Ce comportement affecte également Razor le rendu TagHelper et HtmlHelper, car il utilise les encodeurs pour générer vos chaînes.

Le raisonnement derrière cela est de se protéger contre les bogues de navigateurs inconnus ou futurs (les bogues de navigateur précédents ont dépassé l’analyse en fonction du traitement de caractères non anglais). Si votre site Web utilise beaucoup de caractères non latins, tels que le chinois, le cyrillique ou d’autres, il ne s’agit probablement pas du comportement que vous souhaitez.

Vous pouvez personnaliser les listes sécurisées de l’encodeur pour inclure les plages Unicode appropriées à votre application au démarrage, dans `ConfigureServices()` .

Par exemple, en utilisant la configuration par défaut, vous pouvez utiliser une HtmlHelper telle que celle-ci Razor .

```html
<p>This link text is in Chinese: @Html.ActionLink("汉语/漢語", "Index")</p>
   ```

Lorsque vous affichez la source de la page Web, vous voyez qu’elle a été rendue comme suit, avec l’encodage de texte en chinois ;

```html
<p>This link text is in Chinese: <a href="/">&#x6C49;&#x8BED;/&#x6F22;&#x8A9E;</a></p>
   ```

Pour élargir les caractères traités comme sécurisés par l’encodeur, insérez la ligne suivante dans la `ConfigureServices()` méthode dans `startup.cs` ;

```csharp
services.AddSingleton<HtmlEncoder>(
     HtmlEncoder.Create(allowedRanges: new[] { UnicodeRanges.BasicLatin,
                                               UnicodeRanges.CjkUnifiedIdeographs }));
   ```

Cet exemple élargit la liste sécurisée pour inclure la plage Unicode CjkUnifiedIdeographs. La sortie rendue devient alors

```html
<p>This link text is in Chinese: <a href="/">汉语/漢語</a></p>
   ```

Les plages de liste sécurisée sont spécifiées sous la forme de graphiques de code Unicode, et non de langues. La [norme Unicode](https://unicode.org/) contient une liste de [graphiques de code](https://www.unicode.org/charts/index.html) que vous pouvez utiliser pour rechercher le graphique contenant vos caractères. Chaque encodeur, html, JavaScript et URL doivent être configurés séparément.

> [!NOTE]
> La personnalisation de la liste sécurisée affecte uniquement les encodeurs provenant de l’injection de sécurité. Si vous accédez directement à un encodeur par `System.Text.Encodings.Web.*Encoder.Default` le biais de, la valeur par défaut est l’utilisation de la safeli Basic latin only.

## <a name="where-should-encoding-take-place"></a>Où l’encodage doit-il avoir lieu ?

La pratique générale acceptée est que l’encodage a lieu au point de sortie et que les valeurs encodées ne doivent jamais être stockées dans une base de données. L’encodage au point de sortie vous permet de modifier l’utilisation des données, par exemple du code HTML à une valeur de chaîne de requête. Elle vous permet également de rechercher facilement vos données sans avoir à encoder les valeurs avant de les Rechercher et vous permet de tirer parti des modifications ou des correctifs de bogues apportés aux encodeurs.

## <a name="validation-as-an-xss-prevention-technique"></a>Validation en tant que technique de prévention XSS

La validation peut être un outil utile pour limiter les attaques XSS. Par exemple, une chaîne numérique contenant uniquement les caractères 0-9 ne déclenchera pas d’attaque XSS. La validation devient plus complexe lors de l’acceptation du code HTML dans l’entrée utilisateur. L’analyse de l’entrée HTML est difficile, voire impossible. La démarque, couplée avec un analyseur qui supprime le code HTML incorporé, est une option plus sûre pour accepter une entrée enrichie. Ne vous fiez jamais à la validation. Encodez toujours les entrées non approuvées avant la sortie, quelle que soit la validation ou l’assainissement effectué.
