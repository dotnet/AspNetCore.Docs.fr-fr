---
title: Validation de modèle dans ASP.NET Core MVC
author: rick-anderson
description: En savoir plus sur la validation de modèle dans ASP.NET Core MVC et les Razor pages.
ms.author: riande
ms.custom: mvc
ms.date: 12/15/2019
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
uid: mvc/models/validation
ms.openlocfilehash: 21f2a65bd93c08f16de988381e648768debde438
ms.sourcegitcommit: acfe51c35497a204f75c2a61125c9408c04493e6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102605592"
---
# <a name="model-validation-in-aspnet-core-mvc-and-razor-pages"></a>Validation de modèle dans ASP.NET Core MVC et les Razor pages

::: moniker range=">= aspnetcore-3.0"

Par [Kirk Larkin](https://github.com/serpent5)

Cet article explique comment valider une entrée d’utilisateur dans une application ASP.NET Core MVC ou Razor pages.

[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/validation/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).

## <a name="model-state"></a>État du modèle

L’état du modèle représente les erreurs qui proviennent de deux sous-systèmes : liaison de modèle et validation de modèle. Les erreurs qui proviennent de la [liaison de modèle](model-binding.md) sont généralement des erreurs de conversion de données. Par exemple, un « x » est entré dans un champ de type entier. La validation de modèle se produit après la liaison de modèle et signale des erreurs où les données ne sont pas conformes aux règles d’entreprise. Par exemple, un 0 est entré dans un champ qui attend une évaluation comprise entre 1 et 5.

La liaison de modèle et la validation de modèle se produisent avant l’exécution d’une action de contrôleur ou d’une Razor méthode de gestionnaire de pages. Pour les applications web, il incombe à l’application d’inspecter `ModelState.IsValid` et de réagir de façon appropriée. En règle générale, les applications web réaffichent la page avec un message d’erreur :

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

Les contrôleurs d’API web ne sont pas obligés de vérifier `ModelState.IsValid` s’ils ont l’attribut `[ApiController]`. Dans ce cas, une réponse HTTP 400 automatique contenant les détails de l’erreur est retournée lorsque l’état du modèle n’est pas valide. Pour plus d’informations, consultez [Réponses HTTP 400 automatiques](xref:web-api/index#automatic-http-400-responses).

## <a name="rerun-validation"></a>Réexécuter la validation

La validation est automatique, mais vous souhaiterez peut-être la répéter manuellement. Par exemple, vous pourriez calculer une valeur pour une propriété, et souhaiter réexécuter la validation après avoir affecté la valeur calculée comme valeur de la propriété. Pour réexécuter la validation, appelez la méthode `TryValidateModel`, comme indiqué ici :

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_TryValidate&highlight=3-6)]

## <a name="validation-attributes"></a>Attributs de validation

Les attributs de validation vous permettent de spécifier des règles de validation pour des propriétés de modèle. L’exemple suivant tiré de l’exemple d’application montre une classe de modèle qui est annotée avec des attributs de validation. L’attribut `[ClassicMovie]` est un attribut de validation personnalisé, et les autres sont prédéfinis. Non affiché est `[ClassicMovieWithClientValidator]` . `[ClassicMovieWithClientValidator]` montre une autre façon d’implémenter un attribut personnalisé.

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/Movie.cs?name=snippet_Class)]

## <a name="built-in-attributes"></a>Attributs prédéfinis

Voici certains des attributs de validation prédéfinis :

* `[CreditCard]`: Vérifie que la propriété a un format de carte de crédit. Requiert [des méthodes supplémentaires de validation jQuery](https://cdnjs.cloudflare.com/ajax/libs/jquery-validate/1.19.1/additional-methods.min.js).
* `[Compare]`: Valide que deux propriétés d’un modèle correspondent.
* `[EmailAddress]`: Vérifie que la propriété a un format d’e-mail.
* `[Phone]`: Vérifie que la propriété a un format de numéro de téléphone.
* `[Range]`: Vérifie que la valeur de la propriété est comprise dans une plage spécifiée.
* `[RegularExpression]`: Valide le fait que la valeur de propriété corresponde à une expression régulière spécifiée.
* `[Required]`: Vérifie que le champ n’a pas la valeur null. Pour plus d’informations sur le comportement de cet attribut, consultez [ `[Required]` attribut](#required-attribute) .
* `[StringLength]`: Valide le fait qu’une valeur de propriété de type chaîne ne dépasse pas une limite de longueur spécifiée.
* `[Url]`: Vérifie que la propriété a un format d’URL.
* `[Remote]`: Valide l’entrée sur le client en appelant une méthode d’action sur le serveur. Pour plus d’informations sur le comportement de cet attribut, consultez [ `[Remote]` attribut](#remote-attribute) .

Vous trouverez la liste complète des attributs de validation dans l’espace de noms [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations).

### <a name="error-messages"></a>Messages d’erreur

Les attributs de validation vous permettent de spécifier le message d’erreur à afficher pour l’entrée non valide. Par exemple :

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

En interne, les attributs appellent `String.Format` avec un espace réservé pour le nom de champ et parfois d’autres espaces réservés. Par exemple :

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

Appliqué à une propriété `Name`, le message d’erreur créé par le code précédent serait « Name length must be between 6 and 8 ».

Pour savoir quels paramètres sont passés à `String.Format` pour le message d’erreur d’un attribut particulier, consultez le [code source de DataAnnotations](https://github.com/dotnet/runtime/tree/main/src/libraries/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).

## <a name="non-nullable-reference-types-and-required-attribute"></a>Types de référence non Nullable et attribut [required]

Le système de validation traite les paramètres n’acceptant pas les valeurs null ou les propriétés liées comme s’ils avaient un `[Required]` attribut. En [activant des `Nullable` contextes](/dotnet/csharp/nullable-references#nullable-contexts), MVC démarre implicitement la validation de propriétés ou de paramètres non Nullable comme s’ils avaient été attribués à l' `[Required]` attribut. Considérez le code suivant :

```csharp
public class Person
{
    public string Name { get; set; }
}
```

Si l’application a été générée avec `<Nullable>enable</Nullable>` , une valeur manquante pour `Name` dans une publication JSON ou de formulaire génère une erreur de validation. Utilisez un type de référence Nullable pour autoriser la spécification de valeurs null ou manquantes pour la `Name` propriété :

```csharp
public class Person
{
    public string? Name { get; set; }
}
```

. Vous pouvez désactiver ce comportement en configurant <xref:Microsoft.AspNetCore.Mvc.MvcOptions.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes> dans `Startup.ConfigureServices` :

```csharp
services.AddControllers(options => options.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes = true);
```

### <a name="required-validation-on-the-server"></a>Validation de [Required] sur le serveur

Sur le serveur, une valeur obligatoire est considérée comme manquante si la propriété est Null. Un champ qui n’accepte pas les valeurs NULL est toujours valide et le `[Required]` message d’erreur de l’attribut n’est jamais affiché.

Toutefois, la liaison de modèle pour une propriété n’acceptant pas les valeurs Null peut échouer, entraînant l’affichage d’un message d’erreur tel que `The value '' is invalid`. Pour spécifier un message d’erreur personnalisé pour la validation côté serveur des types n’acceptant pas les valeurs Null, vous disposez des options suivantes :

* Rendre le champ Nullable (par exemple, `decimal?` au lieu de `decimal`). [Valeur \<T> null](/dotnet/csharp/programming-guide/nullable-types/) les types valeur sont traités comme des types Nullable standard.
* Spécifier le message d’erreur par défaut devant être utilisé par la liaison de modèle, comme indiqué dans l’exemple suivant :

  [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=5-6)]

  Pour plus d’informations sur les erreurs de liaison de modèle pour lesquelles vous pouvez définir des messages par défaut, consultez <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.

### <a name="required-validation-on-the-client"></a>Validation de [Required] sur le client

Les chaînes et les types n’acceptant pas les valeurs Null sont gérés différemment sur le client et sur le serveur. Sur le client :

* Une valeur est considérée comme présente uniquement si une entrée est tapée pour celle-ci. Par conséquent, la validation côté client gère les types n’acceptant pas les valeurs Null de la même façon que les types Nullable
* Un espace blanc dans un champ de chaîne est considéré comme une entrée valide par la méthode [required](https://jqueryvalidation.org/required-method/) jQuery Validate. La validation côté serveur considère qu’un champ de chaîne obligatoire est non valide si seul un espace blanc est entré.

Comme indiqué précédemment, les types n’acceptant pas les valeurs Null sont traités comme s’ils avaient un attribut `[Required]`. Cela signifie que vous bénéficiez d’une validation côté client même si vous n’appliquez pas l’attribut `[Required]`. En revanche, si vous n’utilisez pas l’attribut, vous recevez un message d’erreur par défaut. Pour spécifier un message d’erreur personnalisé, utilisez l’attribut.

## <a name="remote-attribute"></a>Attribut [Remote]

L’attribut `[Remote]` implémente la validation côté client qui nécessite l’appel d’une méthode sur le serveur pour déterminer si le champ d’entrée est valide. Par exemple, l’application peut avoir besoin de vérifier si un nom d’utilisateur est déjà en cours d’utilisation.

Pour implémenter la validation à distance

1. Créez une méthode d’action devant être appelée par JavaScript.  La méthode [distante](https://jqueryvalidation.org/remote-method/) de validation jQuery attend une réponse JSON :

   * `true` signifie que les données d’entrée sont valides.
   * `false`, `undefined` ou `null` signifie que l’entrée n’est pas valide. Affichez le message d’erreur par défaut.
   * Toute autre chaîne signifie que l’entrée n’est pas valide. Affichez la chaîne en tant que message d’erreur personnalisé.

   Voici un exemple de méthode d’action qui retourne un message d’erreur personnalisé :

   [!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. Dans la classe de modèle, annotez la propriété avec un attribut `[Remote]` qui pointe vers la méthode d’action de validation, comme indiqué dans l’exemple suivant :

   [!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Email)]
 
   L’attribut `[Remote]` se trouve dans l’espace de noms `Microsoft.AspNetCore.Mvc`.
   
### <a name="additional-fields"></a>Champs supplémentaires

La propriété `AdditionalFields` de l’attribut `[Remote]` vous permet de valider des combinaisons de champs relativement à des données présentes sur le serveur. Par exemple, si le modèle `User` a des propriétés `FirstName` et `LastName`, vous pouvez vérifier qu’aucun utilisateur existant n’a déjà cette paire de noms. L'exemple suivant montre comment utiliser `AdditionalFields` :

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Name&highlight=1,5)]

`AdditionalFields` peut être défini explicitement avec les chaînes « FirstName » et « LastName », mais l’utilisation de l’opérateur [nameof](/dotnet/csharp/language-reference/keywords/nameof) simplifie la refactorisation ultérieure. La méthode d’action pour cette validation doit accepter `firstName` les `lastName` arguments et :

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

Quand l’utilisateur entre un nom ou un prénom, JavaScript effectue un appel à distance pour vérifier si cette paire de noms est déjà utilisée.

Pour valider deux champs supplémentaires ou plus, spécifiez-les sous la forme d’une liste délimitée par des virgules. Par exemple, pour ajouter une propriété `MiddleName` au modèle, définissez l’attribut `[Remote]` comme indiqué dans l’exemple suivant :

```csharp
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

`AdditionalFields`, comme tous les arguments d’attribut, doit être une expression constante. Vous ne devez donc pas utiliser une [chaîne interpolée](/dotnet/csharp/language-reference/keywords/interpolated-strings) ou appeler <xref:System.String.Join*> pour initialiser `AdditionalFields`.

## <a name="alternatives-to-built-in-attributes"></a>Alternatives aux attributs prédéfinis

Si vous avez besoin d’une validation non fournie par les attributs prédéfinis, vous pouvez :

* [Créer des attributs personnalisés](#custom-attributes).
* [Implémenter IValidatableObject](#ivalidatableobject).

## <a name="custom-attributes"></a>Attributs personnalisés

Pour les scénarios non gérés par les attributs de validation prédéfinis, vous pouvez créer des attributs de validation personnalisés. Créez une classe qui hérite de <xref:System.ComponentModel.DataAnnotations.ValidationAttribute> et substituez la méthode <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*>.

La méthode `IsValid` accepte un objet nommé *value*, qui est l’entrée à valider. Une surcharge accepte également un objet `ValidationContext`, qui fournit des informations supplémentaires telles que l’instance de modèle créée par la liaison de modèle.

L’exemple suivant vérifie que la date de sortie d’un film appartenant au genre *Classic* n’est pas ultérieure à une année spécifiée. L' `[ClassicMovie]` attribut :

* S’exécute uniquement sur le serveur.
* Pour les films classiques, valide la date de publication :

[!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttribute.cs?name=snippet_Class)]

La variable `movie` de l’exemple précédent représente un objet `Movie` qui contient les données de l’envoi du formulaire. Quand la validation échoue, un `ValidationResult` avec un message d’erreur est retourné.

## <a name="ivalidatableobject"></a>IValidatableObject

L’exemple précédent fonctionne uniquement avec les types `Movie`. Une autre option de validation au niveau de la classe consiste à implémenter `IValidatableObject` dans la classe de modèle, comme indiqué dans l’exemple suivant :

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/ValidatableMovie.cs?name=snippet_Class&highlight=1,26-34)]

## <a name="top-level-node-validation"></a>Validation du nœud de niveau supérieur

Les nœuds de niveau supérieur incluent les éléments suivants :

* Paramètres d’action
* Propriétés du contrôleur
* Paramètres du gestionnaire de page
* Propriétés du modèle de page

Les nœuds de niveau supérieur liés au modèle sont validés en plus de la validation des propriétés du modèle. Dans l’exemple suivant tiré de l’exemple d’application, la méthode `VerifyPhone` utilise <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> pour valider le paramètre d’action `phone` :

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

Les nœuds de niveau supérieur peuvent utiliser <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> avec des attributs de validation. Dans l’exemple suivant de l’exemple d’application, la méthode `CheckAge` spécifie que le paramètre `age` doit être lié à partir de la chaîne de requête au moment de l’envoi du formulaire :

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAgeSignature)]

Dans la page de vérification de l’âge (*CheckAge.cshtml*), il existe deux formulaires. Le premier formulaire envoie une `Age` valeur sous la `99` forme d’un paramètre de chaîne de requête : `https://localhost:5001/Users/CheckAge?Age=99` .

Quand un paramètre `age` au format approprié est envoyé à partir de la chaîne de requête, le formulaire est validé.

Le second formulaire de la page de vérification de l’âge envoie la valeur `Age` dans le corps de la requête, ce qui entraîne un échec de la validation. L’échec de la liaison est dû au fait que le paramètre `age` doit provenir d’une chaîne de requête.

## <a name="maximum-errors"></a>Quantité maximale d’erreurs

La validation s’arrête quand le nombre maximal d’erreurs est atteint (200 par défaut). Vous pouvez configurer ce nombre avec le code suivant dans `Startup.ConfigureServices` :

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=4)]

## <a name="maximum-recursion"></a>Récursivité maximale

<xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> parcourt le graphe d’objet du modèle en cours de validation. Pour les modèles en profondeur ou récursifs à l’infini, la validation peut entraîner un dépassement de capacité de la pile. [MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) fournit un moyen d’interrompre la validation de manière anticipée si la récursivité du visiteur dépasse une profondeur configurée. La valeur par défaut de `MvcOptions.MaxValidationDepth` est 32.

## <a name="automatic-short-circuit"></a>Court-circuit automatique

La validation est automatiquement court-circuitée (ignorée) si le graphe du modèle ne nécessite pas de validation. Les objets pour lesquels le runtime ignore la validation comprennent les collections de primitives (telles que `byte[]`, `string[]`, `Dictionary<string, string>`) et les graphes d’objets complexes qui n’ont pas de validateur.

## <a name="disable-validation"></a>Désactiver la validation

Pour désactiver la validation

1. Créez une implémentation de `IObjectModelValidator` qui ne marque aucun champ comme étant non valide.

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/NullObjectModelValidator.cs?name=snippet_Class)]

1. Ajoutez le code suivant à `Startup.ConfigureServices` pour remplacer l’implémentation par défaut de `IObjectModelValidator` dans le conteneur d’injection de dépendances.

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

Vous risquez toujours de voir des erreurs d’état du modèle provenant de la liaison de modèle.

## <a name="client-side-validation"></a>Validation côté client

La validation côté client empêche l’envoi jusqu’à ce que le formulaire soit valide. Le bouton Submit exécute le code JavaScript qui envoie le formulaire ou qui affiche des messages d’erreur.

La validation côté client permet d’éviter un aller-retour inutile vers le serveur quand il existe des erreurs d’entrée sur un formulaire. Les références de script suivantes dans *_Layout.cshtml* et *_ValidationScriptsPartial.cshtml* prennent en charge la validation côté client :

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_Scripts)]

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_Scripts)]

Le script de [validation jQuery discrète](https://github.com/aspnet/jquery-validation-unobtrusive) est une bibliothèque Microsoft frontale personnalisée qui s’appuie sur le plug-in de [validation jQuery](https://jqueryvalidation.org/) populaire. Sans jQuery Unobtrusive Validation, vous devriez coder la même logique de validation à deux endroits : une fois dans les attributs de validation côté serveur sur les propriétés du modèle, puis à nouveau dans les scripts côté client. Au lieu de cela, les [Tag Helpers](xref:mvc/views/tag-helpers/intro) et les [helpers HTML](xref:mvc/views/overview) utilisent les attributs de validation et les métadonnées de type des propriétés du modèle afin de restituer les attributs `data-` HTML 5 pour les éléments de formulaire nécessitant une validation. la validation jQuery discrète analyse les `data-` attributs et passe la logique à la validation jQuery, en « copiant » en fait la logique de validation côté serveur vers le client. Vous pouvez afficher les erreurs de validation sur le client en utilisant des Tag Helpers, comme indiqué ici :

[!code-cshtml[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=3-4)]

Les balises d’assistance précédentes affichent le code HTML suivant :

```html
<div class="form-group">
    <label class="control-label" for="Movie_ReleaseDate">Release Date</label>
    <input class="form-control" type="date" data-val="true"
        data-val-required="The Release Date field is required."
        id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
    <span class="text-danger field-validation-valid"
        data-valmsg-for="Movie.ReleaseDate" data-valmsg-replace="true"></span>
</div>
```

Notez que les attributs `data-` dans la sortie HTML correspondent aux attributs de validation pour la propriété `Movie.ReleaseDate`. L’attribut `data-val-required` contient un message d’erreur à afficher si l’utilisateur ne renseigne pas le champ correspondant à la date de sortie. la validation jQuery discrète passe cette valeur à la méthode jQuery validation [Required ()](https://jqueryvalidation.org/required-method/) , qui affiche ensuite ce message dans l' **\<span>** élément associé.

La validation du type de données est basée sur le type .NET d’une propriété, sauf en cas de substitution par un attribut `[DataType]`. Les navigateurs ont leurs propres messages d’erreur par défaut, mais le package jQuery Validation Unobtrusive Validation peut remplacer ces messages. Les attributs `[DataType]` et les sous-classes comme `[EmailAddress]` vous permettent de spécifier le message d’erreur.

## <a name="unobtrusive-validation"></a>Validation discrète

Pour plus d’informations sur la validation discrète, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/1111).

### <a name="add-validation-to-dynamic-forms"></a>Ajouter une validation à des formulaires dynamiques

la validation jQuery discrète passe la logique de validation et les paramètres à la validation jQuery lors du premier chargement de la page. Par conséquent, la validation ne fonctionne pas automatiquement sur les formulaires générés de manière dynamique. Pour activer la validation, vous devez faire en sorte que jQuery Validate analyse le formulaire dynamique immédiatement après l’avoir créé. Par exemple, le code suivant définit la validation côté client sur un formulaire ajouté par le biais d’AJAX.

```javascript
$.get({
    url: "https://url/that/returns/a/form",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add form. " + errorThrown);
    },
    success: function(newFormHTML) {
        var container = document.getElementById("form-container");
        container.insertAdjacentHTML("beforeend", newFormHTML);
        var forms = container.getElementsByTagName("form");
        var newForm = forms[forms.length - 1];
        $.validator.unobtrusive.parse(newForm);
    }
})
```

La méthode `$.validator.unobtrusive.parse()` accepte un sélecteur jQuery comme argument. Cette méthode indique à jQuery Unobtrusive Validation d’analyser les attributs `data-` des formulaires dans ce sélecteur. Les valeurs de ces attributs sont ensuite transmises au plug-in de validation jQuery.

### <a name="add-validation-to-dynamic-controls"></a>Ajouter une validation à des contrôles dynamiques

La méthode `$.validator.unobtrusive.parse()` opère sur un formulaire entier, et non sur des contrôles individuels générés de manière dynamique tels que `<input>` et `<select/>`. Pour réanalyser le formulaire, supprimez les données de validation qui ont été ajoutées quand le formulaire a été analysé précédemment, comme illustré dans l’exemple suivant :

```javascript
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add control. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        $(form).removeData("validator")    // Added by jQuery Validation
               .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="custom-client-side-validation"></a>Validation personnalisée côté client

La validation côté client personnalisée s’effectue en générant `data-` des attributs HTML qui fonctionnent avec un adaptateur de validation jQuery personnalisé. L’exemple de code d’adaptateur suivant a été écrit pour les attributs `[ClassicMovie]` et `[ClassicMovieWithClientValidator]` qui ont été introduits plus haut dans cet article :

[!code-javascript[](validation/samples/3.x/ValidationSample/wwwroot/js/classicMovieValidator.js)]

Pour plus d’informations sur l’écriture des adaptateurs, consultez la documentation sur la [validation jQuery](https://jqueryvalidation.org/documentation/).

L’utilisation d’un adaptateur pour un champ donné est déclenchée par des attributs `data-` qui :

* Marquent le champ comme étant soumis à validation (`data-val="true"`).
* Identifient un nom de règle de validation et un texte de message d’erreur (par exemple, `data-val-rulename="Error message."`).
* Fournissent les éventuels paramètres supplémentaires dont le validateur a besoin (par exemple, `data-val-rulename-param1="value"`).

L’exemple suivant montre les attributs `data-` pour l’attribut `ClassicMovie` de l’exemple d’application :

```html
<input class="form-control" type="date"
    data-val="true"
    data-val-classicmovie="Classic movies must have a release year no later than 1960."
    data-val-classicmovie-year="1960"
    data-val-required="The Release Date field is required."
    id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
```

Comme mentionné plus haut, les [Tag Helpers](xref:mvc/views/tag-helpers/intro) et les [helpers HTML](xref:mvc/views/overview) utilisent les informations des attributs de validation pour restituer les attributs `data-`. Il existe deux options pour l’écriture de code qui entraîne la création d’attributs HTML `data-` personnalisés :

* Créez une classe qui dérive de `AttributeAdapterBase<TAttribute>` et une classe qui implémente `IValidationAttributeAdapterProvider`, et inscrivez votre attribut et son adaptateur dans l’injection de dépendances. Cette méthode respecte le [principe de responsabilité unique](https://wikipedia.org/wiki/Single_responsibility_principle), dans la mesure où le code de validation lié au serveur et celui lié au client se trouvent dans des classes distinctes. Il existe un autre avantage : l’adaptateur étant inscrit dans l’injection de dépendances, les autres services dans l’injection de dépendances lui sont accessibles si nécessaire.
* Implémentez `IClientModelValidator` dans votre classe `ValidationAttribute`. Cette méthode peut être appropriée si l’attribut n’effectue aucune validation côté serveur et n’a besoin d’aucun service à partir de l’injection de dépendances.

### <a name="attributeadapter-for-client-side-validation"></a>AttributeAdapter pour la validation côté client

Cette méthode de rendu des attributs `data-` en HTML est utilisée par l’attribut `ClassicMovie` dans l’exemple d’application. Pour ajouter la validation côté client à l’aide de cette méthode

1. Créez une classe d’adaptateurs d’attributs pour l’attribut de validation personnalisé. Dérivez la classe de <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.AttributeAdapterBase%601>. Créez une méthode `AddValidation` qui ajoute des attributs `data-` à la sortie restituée, comme illustré dans cet exemple :

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttributeAdapter.cs?name=snippet_Class)]

1. Créez une classe de fournisseurs d’adaptateurs qui implémente <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>. Dans la méthode `GetAttributeAdapter`, passez l’attribut personnalisé au constructeur de l’adaptateur, comme illustré dans cet exemple :

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/CustomValidationAttributeAdapterProvider.cs?name=snippet_Class)]

1. Inscrivez le fournisseur d’adaptateurs auprès de l’injection de dépendances dans `Startup.ConfigureServices` :

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=9-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a>IClientModelValidator pour la validation côté client

Cette méthode de rendu des attributs `data-` en HTML est utilisée par l’attribut `ClassicMovieWithClientValidator` dans l’exemple d’application. Pour ajouter la validation côté client à l’aide de cette méthode

* Dans l’attribut de validation personnalisé, implémentez l’interface `IClientModelValidator` et créez une méthode `AddValidation`. Dans la méthode `AddValidation`, ajoutez des attributs `data-` pour la validation, comme indiqué dans l’exemple suivant :

  [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieWithClientValidatorAttribute.cs?name=snippet_Class)]

## <a name="disable-client-side-validation"></a>Désactiver la validation côté client

Le code suivant désactive la validation client dans les Razor pages :

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableClientValidation&highlight=2-5)]

Autres options pour désactiver la validation côté client :

* Commentez la référence à `_ValidationScriptsPartial` dans tous les fichiers *. cshtml* .
* Supprimez le contenu du fichier *Pages\Shared \_ ValidationScriptsPartial. cshtml* .

L’approche précédente n’empêchera pas la validation côté client de la ASP.NET Core Identity Razor bibliothèque de classes. Pour plus d’informations, consultez <xref:security/authentication/scaffold-identity>.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Espace de noms System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations)
* [Liaison de modèle](model-binding.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Cet article explique comment valider une entrée d’utilisateur dans une application ASP.NET Core MVC ou Razor pages.

[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/validation/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).

## <a name="model-state"></a>État du modèle

L’état du modèle représente les erreurs qui proviennent de deux sous-systèmes : liaison de modèle et validation de modèle. Les erreurs qui proviennent de la [liaison de modèle](model-binding.md) sont généralement des erreurs de conversion de données (par exemple, un « x » est entré dans un champ qui attend un nombre entier). La validation de modèle se produit après la liaison de modèle, et signale les erreurs où les données ne sont pas conformes aux règles d’entreprise (par exemple, un 0 est entré dans un champ qui attend une évaluation comprise entre 1 et 5).

La liaison de modèle et la validation se produisent avant l’exécution d’une action de contrôleur ou d’une Razor méthode de gestionnaire de pages. Pour les applications web, il incombe à l’application d’inspecter `ModelState.IsValid` et de réagir de façon appropriée. En règle générale, les applications web réaffichent la page avec un message d’erreur :

[!code-csharp[](validation/samples_snapshot/2.x/Create.cshtml.cs?name=snippet&highlight=3-6)]

Les contrôleurs d’API web ne sont pas obligés de vérifier `ModelState.IsValid` s’ils ont l’attribut `[ApiController]`. Dans ce cas, une réponse HTTP 400 automatique contenant les détails de l’erreur est retournée lorsque l’état du modèle n’est pas valide. Pour plus d’informations, consultez [Réponses HTTP 400 automatiques](xref:web-api/index#automatic-http-400-responses).

## <a name="rerun-validation"></a>Réexécuter la validation

La validation est automatique, mais vous souhaiterez peut-être la répéter manuellement. Par exemple, vous pourriez calculer une valeur pour une propriété, et souhaiter réexécuter la validation après avoir affecté la valeur calculée comme valeur de la propriété. Pour réexécuter la validation, appelez la méthode `TryValidateModel`, comme indiqué ici :

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/MoviesController.cs?name=snippet_TryValidateModel&highlight=11)]

## <a name="validation-attributes"></a>Attributs de validation

Les attributs de validation vous permettent de spécifier des règles de validation pour des propriétés de modèle. L’exemple suivant tiré de l’[exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/validation/sample) montre une classe de modèle qui est annotée avec des attributs de validation. L’attribut `[ClassicMovie]` est un attribut de validation personnalisé, et les autres sont prédéfinis. Non affiché est `[ClassicMovie2]` , qui illustre une autre façon d’implémenter un attribut personnalisé.

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/Movie.cs?name=snippet_ModelClass)]

## <a name="built-in-attributes"></a>Attributs prédéfinis

Les attributs de validation intégrés sont les suivants :

* `[CreditCard]`: Vérifie que la propriété a un format de carte de crédit.
* `[Compare]`: Valide que deux propriétés d’un modèle correspondent. Par exemple, le fichier *Register.cshtml.cs* utilise `[Compare]` pour valider les deux correspondances de mots de passe entrées. [Structure Identity ](xref:security/authentication/scaffold-identity) pour afficher le code d’enregistrement.
* `[EmailAddress]`: Vérifie que la propriété a un format d’e-mail.
* `[Phone]`: Vérifie que la propriété a un format de numéro de téléphone.
* `[Range]`: Vérifie que la valeur de la propriété est comprise dans une plage spécifiée.
* `[RegularExpression]`: Valide le fait que la valeur de propriété corresponde à une expression régulière spécifiée.
* `[Required]`: Vérifie que le champ n’a pas la valeur null. Pour plus d’informations sur le comportement de cet attribut, consultez [ `[Required]` attribut](#required-attribute) .
* `[StringLength]`: Valide le fait qu’une valeur de propriété de type chaîne ne dépasse pas une limite de longueur spécifiée.
* `[Url]`: Vérifie que la propriété a un format d’URL.
* `[Remote]`: Valide l’entrée sur le client en appelant une méthode d’action sur le serveur. Pour plus d’informations sur le comportement de cet attribut, consultez [ `[Remote]` attribut](#remote-attribute) .

Lors de l’utilisation de l' `[RegularExpression]` attribut avec la validation côté client, l’expression régulière est exécutée dans JavaScript sur le client. Cela signifie que le comportement de correspondance [ECMAScript](/dotnet/standard/base-types/regular-expression-options#ecmascript-matching-behavior) sera utilisé. Pour plus d’informations, consultez [ce problème GitHub](https://github.com/dotnet/corefx/issues/42487).

Vous trouverez la liste complète des attributs de validation dans l’espace de noms [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations).

### <a name="error-messages"></a>Messages d’erreur

Les attributs de validation vous permettent de spécifier le message d’erreur à afficher pour l’entrée non valide. Par exemple :

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

En interne, les attributs appellent `String.Format` avec un espace réservé pour le nom de champ et parfois d’autres espaces réservés. Par exemple :

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

Appliqué à une propriété `Name`, le message d’erreur créé par le code précédent serait « Name length must be between 6 and 8 ».

Pour savoir quels paramètres sont passés à `String.Format` pour le message d’erreur d’un attribut particulier, consultez le [code source de DataAnnotations](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).

## <a name="required-attribute"></a>Attribut [Required]

Par défaut, le système de validation traite les propriétés ou paramètres n’acceptant pas les valeurs Null comme s’ils avaient un attribut `[Required]`. Les [types valeur](/dotnet/csharp/language-reference/keywords/value-types) comme `decimal` et `int` n’acceptent pas les valeurs Null.

### <a name="required-validation-on-the-server"></a>Validation de [Required] sur le serveur

Sur le serveur, une valeur obligatoire est considérée comme manquante si la propriété est Null. Un champ n’acceptant pas les valeurs Null est toujours valide, et le message d’erreur de l’attribut [Required] n’est jamais affiché.

Toutefois, la liaison de modèle pour une propriété n’acceptant pas les valeurs Null peut échouer, entraînant l’affichage d’un message d’erreur tel que `The value '' is invalid`. Pour spécifier un message d’erreur personnalisé pour la validation côté serveur des types n’acceptant pas les valeurs Null, vous disposez des options suivantes :

* Rendre le champ Nullable (par exemple, `decimal?` au lieu de `decimal`). [Valeur \<T> null](/dotnet/csharp/programming-guide/nullable-types/) les types valeur sont traités comme des types Nullable standard.
* Spécifier le message d’erreur par défaut devant être utilisé par la liaison de modèle, comme indiqué dans l’exemple suivant :

  [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=4-5)]

  Pour plus d’informations sur les erreurs de liaison de modèle pour lesquelles vous pouvez définir des messages par défaut, consultez <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.

### <a name="required-validation-on-the-client"></a>Validation de [Required] sur le client

Les chaînes et les types n’acceptant pas les valeurs Null sont gérés différemment sur le client et sur le serveur. Sur le client :

* Une valeur est considérée comme présente uniquement si une entrée est tapée pour celle-ci. Par conséquent, la validation côté client gère les types n’acceptant pas les valeurs Null de la même façon que les types Nullable
* Un espace blanc dans un champ de chaîne est considéré comme une entrée valide par la méthode [required](https://jqueryvalidation.org/required-method/) jQuery Validate. La validation côté serveur considère qu’un champ de chaîne obligatoire est non valide si seul un espace blanc est entré.

Comme indiqué précédemment, les types n’acceptant pas les valeurs Null sont traités comme s’ils avaient un attribut `[Required]`. Cela signifie que vous bénéficiez d’une validation côté client même si vous n’appliquez pas l’attribut `[Required]`. En revanche, si vous n’utilisez pas l’attribut, vous recevez un message d’erreur par défaut. Pour spécifier un message d’erreur personnalisé, utilisez l’attribut.

## <a name="remote-attribute"></a>Attribut [Remote]

L’attribut `[Remote]` implémente la validation côté client qui nécessite l’appel d’une méthode sur le serveur pour déterminer si le champ d’entrée est valide. Par exemple, l’application peut avoir besoin de vérifier si un nom d’utilisateur est déjà en cours d’utilisation.

Pour implémenter la validation à distance

1. Créez une méthode d’action devant être appelée par JavaScript.  La méthode [remote](https://jqueryvalidation.org/remote-method/) jQuery Validate attend une réponse JSON :

   * `"true"` signifie que les données d’entrée sont valides.
   * `"false"`, `undefined` ou `null` signifie que l’entrée n’est pas valide.  Affichez le message d’erreur par défaut.
   * Toute autre chaîne signifie que l’entrée n’est pas valide. Affichez la chaîne en tant que message d’erreur personnalisé.

   Voici un exemple de méthode d’action qui retourne un message d’erreur personnalisé :

   [!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. Dans la classe de modèle, annotez la propriété avec un attribut `[Remote]` qui pointe vers la méthode d’action de validation, comme indiqué dans l’exemple suivant :

   [!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserEmailProperty)]
 
   L’attribut `[Remote]` se trouve dans l’espace de noms `Microsoft.AspNetCore.Mvc`. Installez le package NuGet [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures), si vous n’utilisez pas le métapaquet `Microsoft.AspNetCore.App` ou `Microsoft.AspNetCore.All`.
   
### <a name="additional-fields"></a>Champs supplémentaires

La propriété `AdditionalFields` de l’attribut `[Remote]` vous permet de valider des combinaisons de champs relativement à des données présentes sur le serveur. Par exemple, si le modèle `User` a des propriétés `FirstName` et `LastName`, vous pouvez vérifier qu’aucun utilisateur existant n’a déjà cette paire de noms. L'exemple suivant montre comment utiliser `AdditionalFields` :

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserNameProperties)]

`AdditionalFields` peut être défini explicitement sur les chaînes `"FirstName"` et `"LastName"` , mais l’utilisation de l’opérateur [nameof](/dotnet/csharp/language-reference/keywords/nameof) simplifie la refactorisation ultérieure. La méthode d’action pour cette validation doit accepter les arguments de nom et de prénom :

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

Quand l’utilisateur entre un nom ou un prénom, JavaScript effectue un appel à distance pour vérifier si cette paire de noms est déjà utilisée.

Pour valider deux champs supplémentaires ou plus, spécifiez-les sous la forme d’une liste délimitée par des virgules. Par exemple, pour ajouter une propriété `MiddleName` au modèle, définissez l’attribut `[Remote]` comme indiqué dans l’exemple suivant :

```csharp
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

`AdditionalFields`, comme tous les arguments d’attribut, doit être une expression constante. Vous ne devez donc pas utiliser une [chaîne interpolée](/dotnet/csharp/language-reference/keywords/interpolated-strings) ou appeler <xref:System.String.Join*> pour initialiser `AdditionalFields`.

## <a name="alternatives-to-built-in-attributes"></a>Alternatives aux attributs prédéfinis

Si vous avez besoin d’une validation non fournie par les attributs prédéfinis, vous pouvez :

* [Créer des attributs personnalisés](#custom-attributes).
* [Implémenter IValidatableObject](#ivalidatableobject).

## <a name="custom-attributes"></a>Attributs personnalisés

Pour les scénarios non gérés par les attributs de validation prédéfinis, vous pouvez créer des attributs de validation personnalisés. Créez une classe qui hérite de <xref:System.ComponentModel.DataAnnotations.ValidationAttribute> et substituez la méthode <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*>.

La méthode `IsValid` accepte un objet nommé *value*, qui est l’entrée à valider. Une surcharge accepte également un objet `ValidationContext`, qui fournit des informations supplémentaires telles que l’instance de modèle créée par la liaison de modèle.

L’exemple suivant vérifie que la date de sortie d’un film appartenant au genre *Classic* n’est pas ultérieure à une année spécifiée. L’attribut `[ClassicMovie2]` vérifie d’abord le genre, et continue uniquement s’il s’agit de *Classic*. Pour les films identifiés comme des classiques, il vérifie si la date de sortie n’est pas ultérieure à la limite passée au constructeur d’attribut.

[!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttribute.cs?name=snippet_ClassicMovieAttribute)]

La variable `movie` de l’exemple précédent représente un objet `Movie` qui contient les données de l’envoi du formulaire. La méthode `IsValid` vérifie la date et le genre. Si la validation réussit, `IsValid` retourne un code `ValidationResult.Success`. Quand la validation échoue, un `ValidationResult` avec un message d’erreur est retourné.

## <a name="ivalidatableobject"></a>IValidatableObject

L’exemple précédent fonctionne uniquement avec les types `Movie`. Une autre option de validation au niveau de la classe consiste à implémenter `IValidatableObject` dans la classe de modèle, comme indiqué dans l’exemple suivant :

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/MovieIValidatable.cs?name=snippet&highlight=1,26-34)]

## <a name="top-level-node-validation"></a>Validation du nœud de niveau supérieur

Les nœuds de niveau supérieur incluent les éléments suivants :

* Paramètres d’action
* Propriétés du contrôleur
* Paramètres du gestionnaire de page
* Propriétés du modèle de page

Les nœuds de niveau supérieur liés au modèle sont validés en plus de la validation des propriétés du modèle. Dans l’exemple suivant tiré de l’exemple d’application, la méthode `VerifyPhone` utilise <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> pour valider le paramètre d’action `phone` :

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

Les nœuds de niveau supérieur peuvent utiliser <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> avec des attributs de validation. Dans l’exemple suivant de l’exemple d’application, la méthode `CheckAge` spécifie que le paramètre `age` doit être lié à partir de la chaîne de requête au moment de l’envoi du formulaire :

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAge)]

Dans la page de vérification de l’âge (*CheckAge.cshtml*), il existe deux formulaires. Le premier formulaire envoie une valeur `Age` égale à `99` en tant que chaîne de requête : `https://localhost:5001/Users/CheckAge?Age=99`.

Quand un paramètre `age` au format approprié est envoyé à partir de la chaîne de requête, le formulaire est validé.

Le second formulaire de la page de vérification de l’âge envoie la valeur `Age` dans le corps de la requête, ce qui entraîne un échec de la validation. L’échec de la liaison est dû au fait que le paramètre `age` doit provenir d’une chaîne de requête.

Lors de l’exécution avec `CompatibilityVersion.Version_2_1` ou version ultérieure, la validation du nœud de niveau supérieur est activée par défaut. Autrement, la validation du nœud de niveau supérieur est désactivée. L’option par défaut peut être remplacée en définissant la propriété <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> dans (`Startup.ConfigureServices`), comme illustré ici :

[!code-csharp[](validation/samples_snapshot/2.x/Startup.cs?name=snippet_AddMvc&highlight=4)]

## <a name="maximum-errors"></a>Quantité maximale d’erreurs

La validation s’arrête quand le nombre maximal d’erreurs est atteint (200 par défaut). Vous pouvez configurer ce nombre avec le code suivant dans `Startup.ConfigureServices` :

[!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=3)]

## <a name="maximum-recursion"></a>Récursivité maximale

<xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> parcourt le graphe d’objet du modèle en cours de validation. Pour les modèles très profonds ou infiniment récursifs, la validation peut entraîner un dépassement de la capacité de la pile. [MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) fournit un moyen d’interrompre la validation de manière anticipée si la récursivité du visiteur dépasse une profondeur configurée. La valeur par défaut de `MvcOptions.MaxValidationDepth` est 32 lors de l’exécution avec `CompatibilityVersion.Version_2_2` ou version ultérieure. Pour les versions antérieures, la valeur est Null, ce qui signifie qu’il n’y a aucune contrainte de profondeur.

## <a name="automatic-short-circuit"></a>Court-circuit automatique

La validation est automatiquement court-circuitée (ignorée) si le graphe du modèle ne nécessite pas de validation. Les objets pour lesquels le runtime ignore la validation comprennent les collections de primitives (telles que `byte[]`, `string[]`, `Dictionary<string, string>`) et les graphes d’objets complexes qui n’ont pas de validateur.

## <a name="disable-validation"></a>Désactiver la validation

Pour désactiver la validation

1. Créez une implémentation de `IObjectModelValidator` qui ne marque aucun champ comme étant non valide.

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/NullObjectModelValidator.cs?name=snippet_DisableValidation)]

1. Ajoutez le code suivant à `Startup.ConfigureServices` pour remplacer l’implémentation par défaut de `IObjectModelValidator` dans le conteneur d’injection de dépendances.

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

Vous risquez toujours de voir des erreurs d’état du modèle provenant de la liaison de modèle.

## <a name="client-side-validation"></a>Validation côté client

La validation côté client empêche l’envoi jusqu’à ce que le formulaire soit valide. Le bouton Submit exécute le code JavaScript qui envoie le formulaire ou qui affiche des messages d’erreur.

La validation côté client permet d’éviter un aller-retour inutile vers le serveur quand il existe des erreurs d’entrée sur un formulaire. Les références de script suivantes dans *_Layout.cshtml* et *_ValidationScriptsPartial.cshtml* prennent en charge la validation côté client :

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_ScriptTag)]

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_ScriptTags)]

Le script [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) est une bibliothèque frontale personnalisée de Microsoft qui s’appuie sur le plug-in bien connu [jQuery Validate](https://jqueryvalidation.org/). Sans jQuery Unobtrusive Validation, vous devriez coder la même logique de validation à deux endroits : une fois dans les attributs de validation côté serveur sur les propriétés du modèle, puis à nouveau dans les scripts côté client. Au lieu de cela, les [Tag Helpers](xref:mvc/views/tag-helpers/intro) et les [helpers HTML](xref:mvc/views/overview) utilisent les attributs de validation et les métadonnées de type des propriétés du modèle afin de restituer les attributs `data-` HTML 5 pour les éléments de formulaire nécessitant une validation. jQuery Unobtrusive Validation analyse les attributs `data-` et passe la logique à jQuery Validate, en « copiant » la logique de validation côté serveur vers le client. Vous pouvez afficher les erreurs de validation sur le client en utilisant des Tag Helpers, comme indiqué ici :

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=4-5)]

Les Tag Helpers précédents restituent le code HTML suivant.

```html
<form action="/Movies/Create" method="post">
    <div class="form-horizontal">
        <h4>Movie</h4>
        <div class="text-danger"></div>
        <div class="form-group">
            <label class="col-md-2 control-label" for="ReleaseDate">ReleaseDate</label>
            <div class="col-md-10">
                <input class="form-control" type="datetime"
                data-val="true" data-val-required="The ReleaseDate field is required."
                id="ReleaseDate" name="ReleaseDate" value="">
                <span class="text-danger field-validation-valid"
                data-valmsg-for="ReleaseDate" data-valmsg-replace="true"></span>
            </div>
        </div>
    </div>
</form>
```

Notez que les attributs `data-` dans la sortie HTML correspondent aux attributs de validation pour la propriété `ReleaseDate`. L’attribut `data-val-required` contient un message d’erreur à afficher si l’utilisateur ne renseigne pas le champ correspondant à la date de sortie. la validation jQuery discrète passe cette valeur à la méthode jQuery Validate [Required ()](https://jqueryvalidation.org/required-method/) , qui affiche ensuite ce message dans l' **\<span>** élément associé.

La validation du type de données est basée sur le type .NET d’une propriété, sauf en cas de substitution par un attribut `[DataType]`. Les navigateurs ont leurs propres messages d’erreur par défaut, mais le package jQuery Validation Unobtrusive Validation peut remplacer ces messages. Les attributs `[DataType]` et les sous-classes comme `[EmailAddress]` vous permettent de spécifier le message d’erreur.

### <a name="add-validation-to-dynamic-forms"></a>Ajouter une validation à des formulaires dynamiques

jQuery Unobtrusive Validation passe la logique et les paramètres de validation à jQuery Validate lors du premier chargement de la page. Par conséquent, la validation ne fonctionne pas automatiquement sur les formulaires générés de manière dynamique. Pour activer la validation, vous devez faire en sorte que jQuery Validate analyse le formulaire dynamique immédiatement après l’avoir créé. Par exemple, le code suivant définit la validation côté client sur un formulaire ajouté par le biais d’AJAX.

```javascript
$.get({
    url: "https://url/that/returns/a/form",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add form. " + errorThrown);
    },
    success: function(newFormHTML) {
        var container = document.getElementById("form-container");
        container.insertAdjacentHTML("beforeend", newFormHTML);
        var forms = container.getElementsByTagName("form");
        var newForm = forms[forms.length - 1];
        $.validator.unobtrusive.parse(newForm);
    }
})
```

La méthode `$.validator.unobtrusive.parse()` accepte un sélecteur jQuery comme argument. Cette méthode indique à jQuery Unobtrusive Validation d’analyser les attributs `data-` des formulaires dans ce sélecteur. Les valeurs de ces attributs sont ensuite passées au plug-in jQuery Validate.

### <a name="add-validation-to-dynamic-controls"></a>Ajouter une validation à des contrôles dynamiques

La méthode `$.validator.unobtrusive.parse()` opère sur un formulaire entier, et non sur des contrôles individuels générés de manière dynamique tels que `<input>` et `<select/>`. Pour réanalyser le formulaire, supprimez les données de validation qui ont été ajoutées quand le formulaire a été analysé précédemment, comme illustré dans l’exemple suivant :

```javascript
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add control. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        $(form).removeData("validator")    // Added by jQuery Validate
               .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="custom-client-side-validation"></a>Validation personnalisée côté client

La validation personnalisée côté client s’effectue en générant des attributs HTML `data-` qui fonctionnent avec un adaptateur jQuery Validate personnalisé. L’exemple de code d’adaptateur suivant a été écrit pour les attributs `ClassicMovie` et `ClassicMovie2` qui ont été introduits plus haut dans cet article :

[!code-javascript[](validation/samples/2.x/ValidationSample/wwwroot/js/classicMovieValidator.js?name=snippet_UnobtrusiveValidation)]

Pour plus d’informations sur la façon d’écrire des adaptateurs, consultez la [documentation de jQuery Validate](https://jqueryvalidation.org/documentation/).

L’utilisation d’un adaptateur pour un champ donné est déclenchée par des attributs `data-` qui :

* Marquent le champ comme étant soumis à validation (`data-val="true"`).
* Identifient un nom de règle de validation et un texte de message d’erreur (par exemple, `data-val-rulename="Error message."`).
* Fournissent les éventuels paramètres supplémentaires dont le validateur a besoin (par exemple, `data-val-rulename-parm1="value"`).

L’exemple suivant montre les attributs `data-` pour l’attribut `ClassicMovie` de l’exemple d’application :

```html
<input class="form-control" type="datetime"
    data-val="true"
    data-val-classicmovie1="Classic movies must have a release year earlier than 1960."
    data-val-classicmovie1-year="1960"
    data-val-required="The ReleaseDate field is required."
    id="ReleaseDate" name="ReleaseDate" value="">
```

Comme mentionné plus haut, les [Tag Helpers](xref:mvc/views/tag-helpers/intro) et les [helpers HTML](xref:mvc/views/overview) utilisent les informations des attributs de validation pour restituer les attributs `data-`. Il existe deux options pour l’écriture de code qui entraîne la création d’attributs HTML `data-` personnalisés :

* Créez une classe qui dérive de `AttributeAdapterBase<TAttribute>` et une classe qui implémente `IValidationAttributeAdapterProvider`, et inscrivez votre attribut et son adaptateur dans l’injection de dépendances. Cette méthode respecte le [principe de responsabilité unique](https://wikipedia.org/wiki/Single_responsibility_principle), dans la mesure où le code de validation lié au serveur et celui lié au client se trouvent dans des classes distinctes. Il existe un autre avantage : l’adaptateur étant inscrit dans l’injection de dépendances, les autres services dans l’injection de dépendances lui sont accessibles si nécessaire.
* Implémentez `IClientModelValidator` dans votre classe `ValidationAttribute`. Cette méthode peut être appropriée si l’attribut n’effectue aucune validation côté serveur et n’a besoin d’aucun service à partir de l’injection de dépendances.

### <a name="attributeadapter-for-client-side-validation"></a>AttributeAdapter pour la validation côté client

Cette méthode de rendu des attributs `data-` en HTML est utilisée par l’attribut `ClassicMovie` dans l’exemple d’application. Pour ajouter la validation côté client à l’aide de cette méthode

1. Créez une classe d’adaptateurs d’attributs pour l’attribut de validation personnalisé. Dérivez la classe de <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.AttributeAdapterBase%601>. Créez une méthode `AddValidation` qui ajoute des attributs `data-` à la sortie restituée, comme illustré dans cet exemple :

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttributeAdapter.cs?name=snippet_ClassicMovieAttributeAdapter)]

1. Créez une classe de fournisseurs d’adaptateurs qui implémente <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>. Dans la méthode `GetAttributeAdapter`, passez l’attribut personnalisé au constructeur de l’adaptateur, comme illustré dans cet exemple :

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/CustomValidationAttributeAdapterProvider.cs?name=snippet_CustomValidationAttributeAdapterProvider)]

1. Inscrivez le fournisseur d’adaptateurs auprès de l’injection de dépendances dans `Startup.ConfigureServices` :

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=8-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a>IClientModelValidator pour la validation côté client

Cette méthode de rendu des attributs `data-` en HTML est utilisée par l’attribut `ClassicMovie2` dans l’exemple d’application. Pour ajouter la validation côté client à l’aide de cette méthode

* Dans l’attribut de validation personnalisé, implémentez l’interface `IClientModelValidator` et créez une méthode `AddValidation`. Dans la méthode `AddValidation`, ajoutez des attributs `data-` pour la validation, comme indiqué dans l’exemple suivant :

  [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovie2Attribute.cs?name=snippet_ClassicMovie2Attribute)]

## <a name="disable-client-side-validation"></a>Désactiver la validation côté client

Le code suivant désactive la validation côté client dans les vues MVC :

[!code-csharp[](validation/samples_snapshot/2.x/Startup2.cs?name=snippet_DisableClientValidation)]

Et dans les Razor pages :

[!code-csharp[](validation/samples_snapshot/2.x/Startup3.cs?name=snippet_DisableClientValidation)]

Une autre option permettant de désactiver la validation côté client consiste à commenter la référence à `_ValidationScriptsPartial` dans votre fichier *.cshtml*.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Espace de noms System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations)
* [Liaison de modèle](model-binding.md)

::: moniker-end
