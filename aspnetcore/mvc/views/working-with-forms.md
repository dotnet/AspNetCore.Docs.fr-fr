---
title: Tag Helpers dans les formulaires dans ASP.NET Core
author: rick-anderson
description: Décrit les Tag Helpers intégrés, utilisés avec des formulaires.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: mvc/views/working-with-forms
ms.openlocfilehash: a568ad481eb09587e2ddce8e84d0ac3eff01e990
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060896"
---
# <a name="tag-helpers-in-forms-in-aspnet-core"></a>Tag Helpers dans les formulaires dans ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT), [N. Taylor Mullen](https://github.com/NTaylorMullen), [Dave Paquette](https://twitter.com/Dave_Paquette) et [Jerrie Pelser](https://github.com/jerriep)

Ce document montre comment utiliser les formulaires, ainsi que les éléments HTML couramment utilisés dans un formulaire. L’élément HTML [Form](https://www.w3.org/TR/html401/interact/forms.html) fournit le principal mécanisme utilisé par les applications web pour publier des données sur le serveur. La majeure partie de ce document décrit les [Tag Helpers](tag-helpers/intro.md) et explique comment ils peuvent vous aider à créer des formulaires HTML robustes. Nous vous recommandons de lire [Introduction aux Tag Helpers](tag-helpers/intro.md) avant de lire ce document.

Dans de nombreux cas, les HTML Helpers offrent une autre approche par rapport à un Tag Helper spécifique. Toutefois, il est clair que les Tag Helpers ne remplacent pas les HTML Helpers, et qu’il n’existe pas toujours un Tag Helper pour chaque HTML Helper. Quand une alternative HTML Helper existe, elle est mentionnée.

<a name="my-asp-route-param-ref-label"></a>

## <a name="the-form-tag-helper"></a>Tag Helper Form

Tag Helper [Form](https://www.w3.org/TR/html401/interact/forms.html) :

* Génère la [\<FORM>](https://www.w3.org/TR/html401/interact/forms.html) `action` valeur de l’attribut HTML pour une action du contrôleur MVC ou un itinéraire nommé

* Génère un [jeton de vérification de requête](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) masqué pour empêcher la falsification de requête intersites (quand il est utilisé avec l’attribut `[ValidateAntiForgeryToken]` dans la méthode d’action HTTP Post)

* Fournit l’attribut `asp-route-<Parameter Name>`, où `<Parameter Name>` est ajouté aux valeurs de routage. Les paramètres `routeValues` de `Html.BeginForm` et `Html.BeginRouteForm` fournissent des fonctionnalités similaires.

* Comporte une alternative HTML Helper avec `Html.BeginForm` et `Html.BeginRouteForm`

Exemple :

[!code-cshtml[](working-with-forms/sample/final/Views/Demo/RegisterFormOnly.cshtml)]

Le Tag Helper Form ci-dessus génère le code HTML suivant :

```html
<form method="post" action="/Demo/Register">
    <!-- Input and Submit elements -->
    <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

Le runtime MVC génère la valeur de l’attribut `action` à partir des attributs `asp-controller` et `asp-action` du Tag Helper Form. Le Tag Helper Form génère également un [jeton de vérification de requête](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) masqué pour empêcher la falsification de requête intersites (quand il est utilisé avec l’attribut `[ValidateAntiForgeryToken]` dans la méthode d’action HTTP Post). Il est difficile de protéger un formulaire HTML contre une falsification de requête intersites. Le Tag Helper Form se charge de fournir ce service à votre place.

### <a name="using-a-named-route"></a>Utilisation d’un routage nommé

L’attribut Tag Helper `asp-route` peut également générer des balises pour l’attribut HTML `action`. Une application avec un [routage](../../fundamentals/routing.md) nommé `register` peut utiliser les balises suivantes pour la page d’inscription :

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterRoute.cshtml)]

Un bon nombre des vues du dossier *Vues/Compte* (généré quand vous créez une application web avec des *comptes d’utilisateurs individuels* ) contiennent l’attribut [asp-route-returnurl](xref:mvc/views/working-with-forms) :

```cshtml
<form asp-controller="Account" asp-action="Login"
     asp-route-returnurl="@ViewData["ReturnUrl"]"
     method="post" class="form-horizontal" role="form">
```

>[!NOTE]
>Avec les modèles intégrés, `returnUrl` est uniquement rempli automatiquement quand vous essayez d’accéder à une ressource autorisée sans l’authentification ou l’autorisation nécessaire. Quand vous tentez d’effectuer un accès non autorisé, l’intergiciel (middleware) de sécurité vous redirige vers la page de connexion avec le `returnUrl` défini.

## <a name="the-form-action-tag-helper"></a>Le Tag Helper Form Action

Le Tag Helper Form Action génère l’attribut `formaction` sur la balise `<button ...>` ou `<input type="image" ...>` générée. L’attribut `formaction` détermine où un formulaire envoie ses données. Il crée une liaison avec [\<input>](https://www.w3.org/wiki/HTML/Elements/input) des éléments de type `image` et des [\<button>](https://www.w3.org/wiki/HTML/Elements/button) éléments. Le Tag Helper Form Action permet l’utilisation de plusieurs attributs [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `asp-` pour contrôler quel lien `formaction` est généré pour l’élément correspondant.

Attributs [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) pris en charge pour contrôler la valeur de `formaction` :

|Attribut|Description|
|---|---|
|[asp-controller](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-controller)|Nom du contrôleur.|
|[asp-action](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-action)|Nom de la méthode d’action.|
|[asp-area](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-area)|Nom de la zone.|
|[asp-page](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-page)|Nom de la Razor page.|
|[asp-page-handler](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-page-handler)|Nom du gestionnaire de Razor page.|
|[asp-route](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-route)|Nom de l'itinéraire.|
|[asp-route-{value}](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-route-value)|Valeur de routage d’URL unique. Par exemple : `asp-route-id="1234"`.|
|[asp-all-route-data](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-all-route-data)|Toutes les valeurs d’itinéraire.|
|[asp-fragment](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-fragment)|Fragment d’URL.|

### <a name="submit-to-controller-example"></a>Exemple d’envoi au contrôleur

Le balisage suivant envoie le formulaire à l’action `Index` de `HomeController` lorsque input ou button est sélectionnée :

```cshtml
<form method="post">
    <button asp-controller="Home" asp-action="Index">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-controller="Home" 
                                asp-action="Index">
</form>
```

Le balisage précédent génère le code HTML suivant :

```html
<form method="post">
    <button formaction="/Home">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/Home">
</form>
```

### <a name="submit-to-page-example"></a>Exemple d’envoi à la page

Le balisage suivant envoie le formulaire à la `About` Razor page :

```cshtml
<form method="post">
    <button asp-page="About">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-page="About">
</form>
```

Le balisage précédent génère le code HTML suivant :

```html
<form method="post">
    <button formaction="/About">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/About">
</form>
```

### <a name="submit-to-route-example"></a>Exemple d’envoi à l’itinéraire

Prenez le point de terminaison `/Home/Test` :

```csharp
public class HomeController : Controller
{
    [Route("/Home/Test", Name = "Custom")]
    public string Test()
    {
        return "This is the test page";
    }
}
```

Le balisage suivant envoie le formulaire au point de terminaison `/Home/Test`.

```cshtml
<form method="post">
    <button asp-route="Custom">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-route="Custom">
</form>
```

Le balisage précédent génère le code HTML suivant :

```html
<form method="post">
    <button formaction="/Home/Test">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/Home/Test">
</form>
```

## <a name="the-input-tag-helper"></a>Tag Helper Input

Le tag Helper d’entrée lie un élément HTML [\<input>](https://www.w3.org/wiki/HTML/Elements/input) à une expression de modèle dans votre vue Razor.

Syntaxe :

```cshtml
<input asp-for="<Expression Name>">
```

Tag Helper Input :

* Génère les attributs HTML `id` et `name` pour le nom d’expression spécifié dans l’attribut `asp-for`. `asp-for="Property1.Property2"` équivaut à `m => m.Property1.Property2`. Nom de l’expression utilisée pour la valeur de l’attribut `asp-for`. Pour plus d’informations, consultez la section [Noms d’expressions](#expression-names).

* Définit la valeur de l’attribut HTML `type` en fonction du type de modèle et des attributs d’[annotation de données](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) appliqués à la propriété de modèle

* Ne remplace pas la valeur de l’attribut HTML `type` quand une valeur est spécifiée

* Génère des attributs de validation [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) à partir des attributs d’[annotation de données](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) appliqués aux propriétés de modèle

* Chevauche des fonctionnalités HTML Helper avec `Html.TextBoxFor` et `Html.EditorFor`. Pour plus d’informations, consultez la section **Alternatives HTML Helper au Tag Helper Input** .

* Fournit un typage fort. Si le nom de la propriété change et si vous ne mettez pas à jour le Tag Helper, vous obtenez une erreur similaire à celle-ci :

  ```
  An error occurred during the compilation of a resource required to process
  this request. Please review the following specific error details and modify
  your source code appropriately.

  Type expected
   'RegisterViewModel' does not contain a definition for 'Email' and no
   extension method 'Email' accepting a first argument of type 'RegisterViewModel'
   could be found (are you missing a using directive or an assembly reference?)
  ```

Le Tag Helper `Input` définit l’attribut HTML `type` en fonction du type .NET. Le tableau suivant liste certains types .NET usuels et le type HTML généré (tous les types .NET ne sont pas listés).

|Type .NET|Type d’entrée|
|---|---|
|Bool|type="checkbox"|
|String|type="text"|
|DateTime|type=["datetime-local"](https://developer.mozilla.org/docs/Web/HTML/Element/input/datetime-local)|
|Byte|type="number"|
|Int|type="number"|
|Single, Double|type="number"|

Le tableau suivant présente des attributs d’[annotations de données](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) usuels que le Tag Helper Input mappe à des types d’entrée spécifiques (tous les attributs de validation ne sont pas listés) :

|Attribut|Type d’entrée|
|---|---|
|[EmailAddress]|type="email"|
|[Url]|type="url"|
|[HiddenInput]|type="hidden"|
|[Phone]|type="tel"|
|[DataType(DataType.Password)]|type="password"|
|[DataType(DataType.Date)]|type="date"|
|[DataType(DataType.Time)]|type="time"|

Exemple :

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-cshtml[](working-with-forms/sample/final/Views/Demo/RegisterInput.cshtml)]

Le code ci-dessus génère le code HTML suivant :

```html
  <form method="post" action="/Demo/RegisterInput">
      Email:
      <input type="email" data-val="true"
             data-val-email="The Email Address field is not a valid email address."
             data-val-required="The Email Address field is required."
             id="Email" name="Email" value=""><br>
      Password:
      <input type="password" data-val="true"
             data-val-required="The Password field is required."
             id="Password" name="Password"><br>
      <button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
   </form>
```

Les annotations de données appliquées aux propriétés `Email` et `Password` génèrent des métadonnées pour le modèle. Le Tag Helper Input consomme les métadonnées du modèle et génère les attributs [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-val-*` (consultez [Validation de modèle](../models/validation.md)). Ces attributs décrivent les validateurs à attacher aux champs d’entrée. Cela permet d’effectuer une validation HTML5 et [jQuery](https://jquery.com/) discrète. Les attributs discrets ont le format `data-val-rule="Error Message"` , où Rule est le nom de la règle de validation (telle que `data-val-required` ,, `data-val-email` `data-val-maxlength` , etc.) Si un message d’erreur est fourni dans l’attribut, il est affiché en tant que valeur de l' `data-val-rule` attribut. Il existe également des attributs ayant la forme `data-val-ruleName-argumentName="argumentValue"` et qui fournissent des détails supplémentaires sur la règle, par exemple `data-val-maxlength-max="1024"`.

### <a name="html-helper-alternatives-to-input-tag-helper"></a>Alternatives HTML Helper au Tag Helper Input

`Html.TextBox`, `Html.TextBoxFor`, `Html.Editor` et `Html.EditorFor` ont des fonctionnalités qui chevauchent celles du Tag Helper Input. Le Tag Helper Input définit automatiquement l’attribut `type`, contrairement à `Html.TextBox` et `Html.TextBoxFor`. `Html.Editor` et `Html.EditorFor` gèrent les collections, les objets complexes et les modèles, contrairement au Tag Helper Input. Le Tag Helper Input, `Html.EditorFor` et `Html.TextBoxFor` sont fortement typés (ils utilisent des expressions lambda), contrairement à `Html.TextBox` et `Html.Editor` (qui utilisent des noms d’expressions).

### <a name="htmlattributes"></a>HtmlAttributes

`@Html.Editor()` et `@Html.EditorFor()` utilisent une entrée `ViewDataDictionary` spéciale nommée `htmlAttributes` durant l’exécution de leurs modèles par défaut. Ce comportement est éventuellement amélioré à l’aide des paramètres `additionalViewData`. La clé « htmlAttributes » ne respecte pas la casse. La clé « htmlAttributes » est prise en charge de manière similaire à l’objet `htmlAttributes` passé aux Helpers d’entrée tels que `@Html.TextBox()`.

```cshtml
@Html.EditorFor(model => model.YourProperty, 
  new { htmlAttributes = new { @class="myCssClass", style="Width:100px" } })
```

### <a name="expression-names"></a>Noms d’expressions

La valeur de l’attribut `asp-for` est un `ModelExpression` et correspond au côté droit d’une expression lambda. Ainsi, `asp-for="Property1"` devient `m => m.Property1` dans le code généré, ce qui explique pourquoi vous n’avez pas besoin de le faire commencer par `Model`. Vous pouvez utiliser le caractère « \@ » pour débuter une expression inline avant `m.` :

```cshtml
@{
  var joe = "Joe";
}

<input asp-for="@joe">
```

Génère ce qui suit :

```html
<input type="text" id="joe" name="joe" value="Joe">
```

Avec les propriétés de collection, `asp-for="CollectionProperty[23].Member"` génère le même nom que `asp-for="CollectionProperty[i].Member"` quand `i` a la valeur `23`.

Quand ASP.NET Core MVC calcule la valeur de `ModelExpression`, plusieurs sources sont inspectées, notamment `ModelState`. Prenez le cas de `<input type="text" asp-for="@Name">`. L’attribut `value` calculé est la première valeur non-null des éléments suivants :

* Entrée `ModelState` avec la clé « Name ».
* Résultat de l’expression `Model.Name`.

### <a name="navigating-child-properties"></a>Navigation dans les propriétés enfants

Vous pouvez également accéder aux propriétés enfants à l’aide du chemin de propriété du modèle de vue. Prenons le cas d’une classe de modèle plus complexe qui contient une propriété enfant `Address`.

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/AddressViewModel.cs?highlight=1,2,3,4&range=5-8)]

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/RegisterAddressViewModel.cs?highlight=8&range=5-13)]

Dans la vue, nous effectuons une liaison à `Address.AddressLine1` :

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterAddress.cshtml?highlight=6)]

Le code HTML suivant est généré pour `Address.AddressLine1` :

```html
<input type="text" id="Address_AddressLine1" name="Address.AddressLine1" value="">
```

### <a name="expression-names-and-collections"></a>Noms d’expressions et collections

Dans cet exemple, un modèle contient un tableau de `Colors` :

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/Person.cs?highlight=3&range=5-10)]

Méthode d’action :

```csharp
public IActionResult Edit(int id, int colorIndex)
{
    ViewData["Index"] = colorIndex;
    return View(GetPerson(id));
}
```

L’exemple suivant Razor montre comment accéder à un `Color` élément spécifique :

[!code-cshtml[](working-with-forms/sample/final/Views/Demo/EditColor.cshtml)]

Modèle *Views/Shared/EditorTemplates/String.cshtml*  :

[!code-cshtml[](working-with-forms/sample/final/Views/Shared/EditorTemplates/String.cshtml)]

Exemple utilisant `List<T>` :

[!code-csharp[](working-with-forms/sample/final/ViewModels/ToDoItem.cs?range=3-8)]

L’exemple suivant Razor montre comment effectuer une itération au sein d’une collection :

[!code-cshtml[](working-with-forms/sample/final/Views/Demo/Edit.cshtml)]

Modèle *Views/Shared/EditorTemplates/ToDoItem.cshtml*  :

[!code-cshtml[](working-with-forms/sample/final/Views/Shared/EditorTemplates/ToDoItem.cshtml)]

Utilisez si possible `foreach` quand la valeur doit être employée dans un contexte équivalent à `asp-for` ou `Html.DisplayFor`. En règle générale, préférez `for` à `foreach` (si le scénario le permet), car il n’a pas besoin d’allouer un énumérateur. Toutefois, l’évaluation d’un indexeur dans une expression LINQ peut s’avérer coûteuse et doit être réduite.

&nbsp;

>[!NOTE]
>L’exemple de code commenté ci-dessus montre comment remplacer l’expression lambda par l’opérateur `@` pour accéder à chaque `ToDoItem` dans la liste.

## <a name="the-textarea-tag-helper"></a>Tag Helper Textarea

Le Tag Helper `Textarea Tag Helper` est similaire au Tag Helper Input.

* Génère les `id` `name` attributs et, ainsi que les attributs de validation des données à partir du modèle pour un [\<textarea>](https://www.w3.org/wiki/HTML/Elements/textarea) élément.

* Fournit un typage fort.

* Alternative HTML Helper : `Html.TextAreaFor`

Exemple :

[!code-csharp[](working-with-forms/sample/final/ViewModels/DescriptionViewModel.cs)]

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterTextArea.cshtml?highlight=4)]

Le code HTML suivant est généré :

```html
<form method="post" action="/Demo/RegisterTextArea">
  <textarea data-val="true"
   data-val-maxlength="The field Description must be a string or array type with a maximum length of &#x27;1024&#x27;."
   data-val-maxlength-max="1024"
   data-val-minlength="The field Description must be a string or array type with a minimum length of &#x27;5&#x27;."
   data-val-minlength-min="5"
   id="Description" name="Description">
  </textarea>
  <button type="submit">Test</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

## <a name="the-label-tag-helper"></a>Tag Helper Label

* Génère la légende et l' `for` attribut de l’étiquette sur un [\<label>](https://www.w3.org/wiki/HTML/Elements/label) élément pour un nom d’expression

* Alternative HTML Helper : `Html.LabelFor`.

Le `Label Tag Helper` offre les avantages suivants sur un élément d’étiquette HTML pur :

* Vous obtenez automatiquement la valeur d’étiquette descriptive à partir de l’attribut `Display`. Le nom d’affichage prévu peut changer plus tard. La combinaison de l’attribut `Display` et du Tag Helper Label applique `Display` partout où il est utilisé.

* Moins de balises dans le code source

* Typage fort avec la propriété de modèle.

Exemple :

[!code-csharp[](working-with-forms/sample/final/ViewModels/SimpleViewModel.cs)]

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterLabel.cshtml?highlight=4)]

Le code HTML suivant est généré pour l’élément `<label>` :

```html
<label for="Email">Email Address</label>
```

Le Tag Helper Label a généré la valeur « Email » pour l’attribut `for`, qui représente l’ID associé à l’élément `<input>`. Les Tag Helpers génèrent des éléments `id` et `for` cohérents pour qu’ils puissent être correctement associés. La légende de cet exemple provient de l’attribut `Display`. Si le modèle ne contient pas d’attribut `Display`, la légende correspond au nom de propriété de l’expression.

## <a name="the-validation-tag-helpers"></a>Tag Helpers Validation

Il existe deux Tag Helpers Validation. Le `Validation Message Tag Helper` (qui affiche un message de validation pour une seule propriété de votre modèle) et le `Validation Summary Tag Helper` (qui affiche un récapitulatif des erreurs de validation). Le `Input Tag Helper` ajoute des attributs de validation HTML5 côté client aux éléments d’entrée en fonction des attributs d’annotation de données pour vos classes de modèle. La validation est également effectuée sur le serveur. Le Tag Helper Validation affiche ces messages d’erreur quand une erreur de validation se produit.

### <a name="the-validation-message-tag-helper"></a>Le Tag Helper Validation Message

* Ajoute l’attribut [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5)  `data-valmsg-for="property"` à l’élément [span](https://developer.mozilla.org/docs/Web/HTML/Element/span), qui attache les messages d’erreur de validation du champ d’entrée de la propriété de modèle spécifiée. Quand une erreur de validation côté client se produit, [jQuery](https://jquery.com/) affiche le message d’erreur dans l’élément `<span>`.

* La validation a également lieu sur le serveur. Il arrive que JavaScript soit désactivé sur les clients et qu’une partie de la validation puisse être effectuée uniquement côté serveur.

* Alternative HTML Helper : `Html.ValidationMessageFor`

Le `Validation Message Tag Helper` est utilisé avec l’attribut `asp-validation-for` sur un élément HTML [span](https://developer.mozilla.org/docs/Web/HTML/Element/span).

```cshtml
<span asp-validation-for="Email"></span>
```

Le Tag Helper Validation Message génère le code HTML suivant :

```html
<span class="field-validation-valid"
  data-valmsg-for="Email"
  data-valmsg-replace="true"></span>
```

En règle générale, vous utilisez le `Validation Message Tag Helper` après un Tag Helper `Input` pour la même propriété. Dans ce cas, les messages d’erreur de validation s’affichent près de l’entrée qui a provoqué l’erreur.

> [!NOTE]
> Vous devez avoir une vue avec les références de script JavaScript et [jQuery](https://jquery.com/) appropriées pour la validation côté client. Pour plus d’informations, consultez [Validation de modèle](../models/validation.md).

Quand une erreur de validation côté serveur se produit (par exemple, quand vous disposez d’une validation personnalisée côté serveur ou quand la validation côté client est désactivée), MVC place ce message d’erreur dans le corps de l’élément `<span>`.

```html
<span class="field-validation-error" data-valmsg-for="Email"
            data-valmsg-replace="true">
   The Email Address field is required.
</span>
```

### <a name="the-validation-summary-tag-helper"></a>Le Tag Helper Validation Summary

* Cible les éléments `<div>` avec les attributs `asp-validation-summary`

* Alternative HTML Helper : `@Html.ValidationSummary`

Le `Validation Summary Tag Helper` est utilisé pour afficher un récapitulatif des messages de validation. La valeur de l’attribut `asp-validation-summary` peut correspondre à l’une des valeurs suivantes :

|asp-validation-summary|Messages de validation affichés|
|--- |--- |
|ValidationSummary.All|Niveau de la propriété et du modèle|
|ValidationSummary.ModelOnly|Modèle|
|ValidationSummary.None|Aucun|

### <a name="sample"></a>Exemple

Dans l’exemple suivant, le modèle de données a des `DataAnnotation` attributs, qui génèrent des messages d’erreur de validation sur l' `<input>` élément.  Quand une erreur de validation se produit, le Tag Helper Validation affiche le message d’erreur :

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterValidation.cshtml?highlight=4,6,8&range=1-10)]

Code HTML généré (quand le modèle est valide) :

```html
<form action="/DemoReg/Register" method="post">
  <div class="validation-summary-valid" data-valmsg-summary="true">
  <ul><li style="display:none"></li></ul></div>
  Email:  <input name="Email" id="Email" type="email" value=""
   data-val-required="The Email field is required."
   data-val-email="The Email field is not a valid email address."
   data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Email"></span><br>
  Password: <input name="Password" id="Password" type="password"
   data-val-required="The Password field is required." data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Password"></span><br>
  <button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

## <a name="the-select-tag-helper"></a>Tag Helper Select

* Génère l’élément [select](https://www.w3.org/wiki/HTML/Elements/select) et les éléments [option](https://www.w3.org/wiki/HTML/Elements/option) associés pour les propriétés de votre modèle.

* Comporte une alternative HTML Helper avec `Html.DropDownListFor` et `Html.ListBoxFor`

Le `Select Tag Helper` `asp-for` spécifie le nom de propriété de modèle de l’élément [select](https://www.w3.org/wiki/HTML/Elements/select), et `asp-items` spécifie les éléments [option](https://www.w3.org/wiki/HTML/Elements/option).  Exemple :

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

Exemple :

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryViewModel.cs)]

La méthode `Index` initialise `CountryViewModel`, définit le pays sélectionné et le passe à la vue `Index`.

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=8-13)]

La méthode HTTP POST `Index` affiche la sélection :

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=15-27)]

Vue `Index` :

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?highlight=4)]

Qui génère le code HTML suivant (avec « CA » sélectionné) :

```html
<form method="post" action="/">
     <select id="Country" name="Country">
       <option value="MX">Mexico</option>
       <option selected="selected" value="CA">Canada</option>
       <option value="US">USA</option>
     </select>
       <br /><button type="submit">Register</button>
     <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
   </form>
```

> [!NOTE]
> Nous déconseillons d’utiliser `ViewBag` ou `ViewData` avec le Tag Helper Select. Un modèle de vue est plus robuste et, en général, moins problématique pour fournir des métadonnées MVC.

La valeur de l’attribut `asp-for` est un cas particulier et ne nécessite pas de préfixe `Model`, contrairement aux autres attributs du Tag Helper (par exemple `asp-items`)

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

### <a name="enum-binding"></a>Liaison d’enum

Il est souvent pratique d’utiliser `<select>` avec une propriété `enum` et de générer les éléments `SelectListItem` à partir des valeurs de `enum`.

Exemple :

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnumViewModel.cs?range=3-7)]

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs)]

La méthode `GetEnumSelectList` génère un objet `SelectList` pour un enum.

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEnum.cshtml?highlight=5)]

Vous pouvez marquer votre liste d’énumérateurs avec l' `Display` attribut pour obtenir une interface utilisateur plus riche :

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs?highlight=5,7)]

Le code HTML suivant est généré :

```html
  <form method="post" action="/Home/IndexEnum">
         <select data-val="true" data-val-required="The EnumCountry field is required."
                 id="EnumCountry" name="EnumCountry">
             <option value="0">United Mexican States</option>
             <option value="1">United States of America</option>
             <option value="2">Canada</option>
             <option value="3">France</option>
             <option value="4">Germany</option>
             <option selected="selected" value="5">Spain</option>
         </select>
         <br /><button type="submit">Register</button>
         <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
    </form>
```

### <a name="option-group"></a>Groupe d’options

L'  [\<optgroup>](https://www.w3.org/wiki/HTML/Elements/optgroup) élément HTML est généré lorsque le modèle de vue contient un ou plusieurs `SelectListGroup` objets.

`CountryViewModelGroup` regroupe les éléments `SelectListItem` dans les groupes « North America » et « Europe » :

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelGroup.cs?highlight=5,6,14,20,26,32,38,44&range=6-56)]

Les deux groupes sont affichés ci-dessous :

![exemple de groupe d’options](working-with-forms/_static/grp.png)

Code HTML généré :

```html
 <form method="post" action="/Home/IndexGroup">
      <select id="Country" name="Country">
          <optgroup label="North America">
              <option value="MEX">Mexico</option>
              <option value="CAN">Canada</option>
              <option value="US">USA</option>
          </optgroup>
          <optgroup label="Europe">
              <option value="FR">France</option>
              <option value="ES">Spain</option>
              <option value="DE">Germany</option>
          </optgroup>
      </select>
      <br /><button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
 </form>
```

### <a name="multiple-select"></a>Sélection multiple

Le Tag Helper Select génère automatiquement l’attribut [multiple = "multiple"](https://w3c.github.io/html-reference/select.html) si la propriété spécifiée dans l’attribut `asp-for` est `IEnumerable`. Par exemple, le modèle suivant :

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelIEnumerable.cs?highlight=6)]

Avec la vue suivante :

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexMultiSelect.cshtml?highlight=4)]

Génère le code HTML suivant :

```html
<form method="post" action="/Home/IndexMultiSelect">
    <select id="CountryCodes"
    multiple="multiple"
    name="CountryCodes"><option value="MX">Mexico</option>
<option value="CA">Canada</option>
<option value="US">USA</option>
<option value="FR">France</option>
<option value="ES">Spain</option>
<option value="DE">Germany</option>
</select>
    <br /><button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

### <a name="no-selection"></a>Aucune sélection

Si vous constatez que l’option « not specified » est utilisée dans plusieurs pages, vous pouvez créer un modèle pour éviter de répéter le code HTML :

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEmptyTemplate.cshtml?highlight=5)]

Modèle *Views/Shared/EditorTemplates/CountryViewModel.cshtml*  :

[!code-cshtml[](working-with-forms/sample/final/Views/Shared/EditorTemplates/CountryViewModel.cshtml)]

L’ajout [\<option>](https://www.w3.org/wiki/HTML/Elements/option) d’éléments HTML n’est pas limité au cas *sans sélection* . Par exemple, la vue et la méthode d’action suivante génèrent du code HTML similaire au code ci-dessus :

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?name=snippetNone)]

[!code-cshtml[](working-with-forms/sample/final/Views/Home/IndexOption.cshtml)]

L’élément `<option>` approprié est sélectionné (il contient l’attribut `selected="selected"`) en fonction de la valeur actuelle de `Country`.

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=114-119)]

```html
 <form method="post" action="/Home/IndexEmpty">
      <select id="Country" name="Country">
          <option value="">&lt;none&gt;</option>
          <option value="MX">Mexico</option>
          <option value="CA" selected="selected">Canada</option>
          <option value="US">USA</option>
      </select>
      <br /><button type="submit">Register</button>
   <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
 </form>
 ```

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:mvc/views/tag-helpers/intro>
* [Élément de formulaire HTML](https://www.w3.org/TR/html401/interact/forms.html)
* [Jeton de vérification de demande](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages)
* <xref:mvc/models/model-binding>
* <xref:mvc/models/validation>
* [Interface IAttributeAdapter](/dotnet/api/Microsoft.AspNetCore.Mvc.DataAnnotations.IAttributeAdapter)
* [Extraits de code de ce document](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/working-with-forms/sample/final)
