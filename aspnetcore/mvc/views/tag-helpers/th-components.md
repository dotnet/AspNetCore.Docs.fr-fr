---
title: Tag Helper Components dans ASP.NET Core
author: scottaddie
description: Découvrez ce que sont les Tag Helper Components et apprenez à les utiliser dans ASP.NET Core.
monikerRange: '>= aspnetcore-2.0'
ms.author: scaddie
ms.date: 06/12/2019
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
uid: mvc/views/tag-helpers/th-components
ms.openlocfilehash: 15bddd8ce18546bef7ee7e6ec2e32e369d0858a3
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060558"
---
# <a name="tag-helper-components-in-aspnet-core"></a>Tag Helper Components dans ASP.NET Core

Par [Scott Addie](https://twitter.com/Scott_Addie) et [Fiyaz Bin Hasan](https://github.com/fiyazbinhasan)

Un Tag Helper Component est un Tag Helper qui vous permet de modifier ou d’ajouter des éléments HTML de manière conditionnelle à partir du code côté serveur. Cette fonctionnalité est disponible dans ASP.NET Core 2.0 ou version ultérieure.

ASP.NET Core inclut deux Tag Helper Components intégrés : `head` et `body`. Ils sont situés dans l' <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers> espace de noms et peuvent être utilisés à la fois dans MVC et dans les Razor pages. Les Tag Helper Components ne nécessitent pas d’être inscrits avec l’application dans *_ViewImports.cshtml* .

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/th-components/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="use-cases"></a>Cas d'utilisation

Voici deux cas d’usage courants des Tag Helper Components :

1. [Injection d’un `<link>` dans le `<head>` .](#inject-into-html-head-element)
1. [Injection d’un `<script>` dans le `<body>` .](#inject-into-html-body-element)

Les sections suivantes décrivent ces cas d’usage.

### <a name="inject-into-html-head-element"></a>Injection dans l’élément head HTML

Dans l’élément `<head>` HTML, les fichiers CSS sont généralement importés avec l’élément `<link>` HTML. Le code suivant injecte un élément `<link>` dans l’élément `<head>` à l’aide du Tag Helper Component `head` :

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressStyleTagHelperComponent.cs)]

Dans le code précédent :

* L'objet `AddressStyleTagHelperComponent` implémente l'objet <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent>. L’abstraction :
  * Permet l’initialisation de la classe avec un <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext>.
  * Permet l’utilisation des Tag Helper Components pour ajouter ou modifier des éléments HTML.
* La propriété <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.Order*> définit l’ordre dans lequel les Components sont rendus. `Order` est requis quand il y a plusieurs utilisations des Tag Helper Components dans une application.
* <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.ProcessAsync*> compare la valeur de la propriété <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext.TagName*> du contexte d’exécution à `head`. Si le résultat de la comparaison est true, le contenu du champ `_style` est injecté dans l’élément `<head>` HTML.

### <a name="inject-into-html-body-element"></a>Injection dans l’élément body HTML

Le Tag Helper Component `body` peut injecter un élément `<script>` dans l’élément `<body>`. Le code suivant illustre cette technique :

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressScriptTagHelperComponent.cs)]

Un fichier HTML distinct est utilisé pour stocker l’élément `<script>`. Le fichier HTML rend le code plus lisible et plus facile à tenir à jour. Le code précédent lit le contenu de *TagHelpers/Templates/AddressToolTipScript.html* et l’ajoute à la sortie du Tag Helper. Le fichier *AddressToolTipScript.html* inclut le balisage suivant :

[!code-html[](th-components/samples/RazorPagesSample/TagHelpers/Templates/AddressToolTipScript.html)]

Le code précédent lie un [widget tooltip amorçable](https://getbootstrap.com/docs/3.3/javascript/#tooltips) à tout élément `<address>` contenant un attribut `printable`. L’effet est visible quand l’utilisateur pointe sur l’élément avec la souris.

## <a name="register-a-component"></a>Inscrire un Component

Un Tag Helper Component doit être ajouté à la collection des Tag Helper Components de l’application. Cet ajout à la collection peut s’effectuer de trois façons :

* [Inscription par le biais d’un conteneur de services](#registration-via-services-container)
* [Inscription par le biais du Razor fichier](#registration-via-razor-file)
* [Inscription à l’aide d’un modèle de page ou d’un contrôleur](#registration-via-page-model-or-controller)

### <a name="registration-via-services-container"></a>Inscription par le biais d’un conteneur de services

Si la classe Tag Helper Component n’est pas managée avec <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.ITagHelperComponentManager>, elle doit être inscrite avec le système [d’injection de dépendances](xref:fundamentals/dependency-injection). Le code `Startup.ConfigureServices` ci-dessous inscrit les classes `AddressStyleTagHelperComponent` et `AddressScriptTagHelperComponent` avec une [durée de vie transitoire (transient)](xref:fundamentals/dependency-injection#lifetime-and-registration-options) :

[!code-csharp[](th-components/samples/RazorPagesSample/Startup.cs?name=snippet_ConfigureServices&highlight=12-15)]

### <a name="registration-via-no-locrazor-file"></a>Inscription par le biais du Razor fichier

Si le composant tag Helper n’est pas inscrit auprès de DI, il peut être enregistré à partir d’une Razor page pages ou d’une vue Mvc. Cette technique est utilisée pour contrôler le balisage injecté et l’ordre d’exécution du composant à partir d’un Razor fichier.

`ITagHelperComponentManager` est utilisé pour ajouter ou supprimer des Tag Helper Components dans l’application. Le code suivant illustre cette technique avec `AddressTagHelperComponent` :

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_ITagHelperComponentManager)]

Dans le code précédent :

* La directive `@inject` fournit une instance de `ITagHelperComponentManager`. L’instance est assignée à une variable nommée `manager` pour l’accès en aval dans le Razor fichier.
* Une instance de `AddressTagHelperComponent` est ajoutée à la collection des Tag Helper Components de l’application.

`AddressTagHelperComponent` est modifié pour contenir un constructeur qui accepte les paramètres `markup` et `order` :

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_Constructor)]

Voici comment le paramètre `markup` s’utilise dans `ProcessAsync` :

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_ProcessAsync&highlight=10-11)]

### <a name="registration-via-page-model-or-controller"></a>Inscription à l’aide d’un modèle de page ou d’un contrôleur

Si le composant tag Helper n’est pas inscrit auprès de DI, il peut être inscrit à partir d’un Razor modèle de page pages ou d’un contrôleur Mvc. Cette technique est utile pour séparer la logique C# des Razor fichiers.

L’injection de constructeur est utilisée pour accéder à une instance de `ITagHelperComponentManager`. Le Tag Helper Component est ajouté à la collection des Tag Helper Components de l’instance. Le Razor modèle de page pages suivant illustre cette technique avec `AddressTagHelperComponent` :

[!code-csharp[](th-components/samples/RazorPagesSample/Pages/Index.cshtml.cs?name=snippet_IndexModelClass)]

Dans le code précédent :

* L’injection de constructeur est utilisée pour accéder à une instance de `ITagHelperComponentManager`.
* Une instance de `AddressTagHelperComponent` est ajoutée à la collection des Tag Helper Components de l’application.

## <a name="create-a-component"></a>Créer un Component

Pour créer un Tag Helper Component personnalisé :

* Créez une classe publique dérivée de <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperComponentTagHelper>.
* Appliquez un [`[HtmlTargetElement]`](xref:Microsoft.AspNetCore.Razor.TagHelpers.HtmlTargetElementAttribute) attribut à la classe. Spécifiez le nom de l’élément HTML cible.
* *Facultatif* : appliquez un [`[EditorBrowsable(EditorBrowsableState.Never)]`](xref:System.ComponentModel.EditorBrowsableAttribute) attribut à la classe pour supprimer l’affichage du type dans IntelliSense.

Le code suivant crée un Tag Helper Component personnalisé qui cible l’élément HTML `<address>` :

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponentTagHelper.cs)]

Utilisez le Tag Helper Component `address` personnalisé pour injecter le balisage HTML comme ceci :

```csharp
public class AddressTagHelperComponent : TagHelperComponent
{
    private readonly string _printableButton =
        "<button type='button' class='btn btn-info' onclick=\"window.open(" +
        "'https://binged.it/2AXRRYw')\">" +
        "<span class='glyphicon glyphicon-road' aria-hidden='true'></span>" +
        "</button>";

    public override int Order => 3;

    public override async Task ProcessAsync(TagHelperContext context,
                                            TagHelperOutput output)
    {
        if (string.Equals(context.TagName, "address",
                StringComparison.OrdinalIgnoreCase) &&
            output.Attributes.ContainsName("printable"))
        {
            var content = await output.GetChildContentAsync();
            output.Content.SetHtmlContent(
                $"<div>{content.GetContent()}</div>{_printableButton}");
        }
    }
}
```

La méthode `ProcessAsync` précédente injecte le code HTML fourni à <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContent.SetHtmlContent*> dans l’élément `<address>` correspondant. L’injection se produit quand :

* La propriété `TagName` du contexte d’exécution a la valeur `address`.
* L’élément `<address>` correspondant a un attribut `printable`.

Par exemple, l’instruction `if` a la valeur true quand l’élément `<address>` suivant est exécuté :

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_AddressPrintable)]

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
* <xref:mvc/views/tag-helpers/builtin-th/Index>
