---
title: Formateurs personnalisés dans l’API web ASP.NET Core
author: rick-anderson
description: Découvrez comment créer et utiliser des formateurs personnalisés pour les API web dans ASP.NET Core.
ms.author: riande
ms.date: 06/25/2020
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
uid: web-api/advanced/custom-formatters
ms.openlocfilehash: 91c9c6513d7c8df671e283508ecc276768d79539
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587825"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a>Formateurs personnalisés dans l’API web ASP.NET Core

Par [Kirk Larkin](https://twitter.com/serpent5) et [Tom Dykstra](https://github.com/tdykstra).

ASP.NET Core MVC prend en charge l’échange de données dans les API Web à l’aide de formateurs d’entrée et de sortie. Les formateurs d’entrée sont utilisés par la [liaison de modèle](xref:mvc/models/model-binding). Les formateurs de sortie sont utilisés pour [mettre en forme les réponses](xref:web-api/advanced/formatting).

L’infrastructure fournit des formateurs d’entrée et de sortie intégrés pour JSON et XML. Il fournit un formateur de sortie intégré pour le texte brut, mais ne fournit pas de formateur d’entrée pour le texte brut.

Cet article montre comment ajouter la prise en charge de formats supplémentaires en créant des formateurs personnalisés. Pour obtenir un exemple de formateur d’entrée de texte brut personnalisé, consultez [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) sur GitHub.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/advanced/custom-formatters/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="when-to-use-custom-formatters"></a>Quand utiliser les formateurs personnalisés

Utilisez un formateur personnalisé pour ajouter la prise en charge d’un type de contenu qui n’est pas géré par les formateurs intégrés.

## <a name="overview-of-how-to-use-a-custom-formatter"></a>Présentation de l’utilisation d’un formateur personnalisé

Pour créer un formateur personnalisé :

* Pour sérialiser des données envoyées au client, créez une classe de formateur de sortie.
* Pour désérialiser les données reçues du client, créez une classe de formateur d’entrée.
* Ajoutez des instances de classes de formateur `InputFormatters` aux `OutputFormatters` collections et dans <xref:Microsoft.AspNetCore.Mvc.MvcOptions> .

## <a name="how-to-create-a-custom-formatter-class"></a>Guide pratique pour créer une classe de formateur personnalisé

Pour créer un formateur :

* Faites dériver la classe de la classe de base appropriée. L’exemple d’application dérive de <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> et de <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> .
* Spécifiez les encodages et types de média valides dans le constructeur.
* Remplacez les méthodes <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.CanReadType%2A> et <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter.CanWriteType%2A>.
* Remplacez les méthodes <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.ReadRequestBodyAsync%2A> et `WriteResponseBodyAsync`.

Le code suivant illustre la `VcardOutputFormatter` classe de l' [exemple](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/advanced/custom-formatters/samples):

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_Class)]
  
### <a name="derive-from-the-appropriate-base-class"></a>Effectuer une dérivation à partir de la classe de base appropriée

Pour les types de supports de texte (par exemple, vCard), dérivez de la <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> classe de base ou.

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_ClassDeclaration)]

Pour les types binaires, dérivez de la <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter> <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter> classe de base ou.

### <a name="specify-valid-media-types-and-encodings"></a>Spécifier les encodages et types de média valides

Dans le constructeur, spécifiez les encodages et types de média valides en effectuant les ajouts nécessaires aux collections `SupportedMediaTypes` et `SupportedEncodings`.

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_ctor)]

Une classe de formateur **ne peut pas** utiliser l’injection de constructeur pour ses dépendances. Par exemple, `ILogger<VcardOutputFormatter>` ne peut pas être ajouté en tant que paramètre au constructeur. Pour accéder aux services, utilisez l’objet de contexte qui est passé aux méthodes. Un exemple de code dans cet article et l' [exemple](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/advanced/custom-formatters/samples) montrent comment procéder.

### <a name="override-canreadtype-and-canwritetype"></a>Remplacer CanReadType et CanWriteType

Spécifiez le type à désérialiser ou à partir duquel effectuer la sérialisation en remplaçant les `CanReadType` `CanWriteType` méthodes ou. Par exemple, la création d’un texte vCard à partir d’un `Contact` type et vice versa.

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_CanWriteType)]

#### <a name="the-canwriteresult-method"></a>Méthode CanWriteResult

Dans certains scénarios, `CanWriteResult` doit être substitué plutôt que `CanWriteType` . Utilisez `CanWriteResult` si les conditions suivantes sont vraies :

* La méthode d’action retourne une classe de modèle.
* Il existe des classes dérivées qui peuvent être retournées au moment de l’exécution.
* La classe dérivée retournée par l’action doit être connue au moment de l’exécution.

Par exemple, supposons que la méthode d’action :

* La signature retourne un `Person` type.
* Peut retourner un `Student` `Instructor` type ou qui dérive de `Person` . 

Pour que le formateur gère uniquement les `Student` objets, vérifiez le type de <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatterCanWriteContext.Object> dans l’objet de contexte fourni à la `CanWriteResult` méthode. Lorsque la méthode d’action retourne `IActionResult` :

* Il n’est pas nécessaire d’utiliser `CanWriteResult` .
* La `CanWriteType` méthode reçoit le type au moment de l’exécution.

<a id="read-write"></a>

### <a name="override-readrequestbodyasync-and-writeresponsebodyasync"></a>Remplacer ReadRequestBodyAsync et WriteResponseBodyAsync

La désérialisation ou la sérialisation est effectuée dans `ReadRequestBodyAsync` ou `WriteResponseBodyAsync` . L’exemple suivant montre comment récupérer des services à partir du conteneur d’injection de dépendances. Les services ne peuvent pas être obtenus à partir des paramètres du constructeur.

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_WriteResponseBodyAsync)]

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a>Guide pratique pour configurer MVC et utiliser un formateur personnalisé

Pour utiliser un formateur personnalisé, ajoutez une instance de la classe du formateur à la collection `InputFormatters` ou `OutputFormatters`.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](custom-formatters/samples/2.x/CustomFormattersSample/Startup.cs?name=mvcoptions&highlight=3-4)]

::: moniker-end

Les formateurs sont évalués dans l’ordre dans lequel vous les insérez. Le premier est prioritaire.

## <a name="the-complete-vcardinputformatter-class"></a>La `VcardInputFormatter` classe complète

Le code suivant illustre la `VcardInputFormatter` classe de l' [exemple](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/advanced/custom-formatters/samples):

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardInputFormatter.cs?name=snippet_Class)]

## <a name="test-the-app"></a>Test de l'application

[Exécutez l’exemple d’application pour cet article](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/advanced/custom-formatters/samples), qui implémente des formateurs de sortie et d’entrée vCard de base. L’application lit et écrit les vCards semblables à ce qui suit :

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
END:VCARD
```

Pour afficher la sortie vCard, exécutez l’application et envoyez une demande obtenir avec l’en-tête Accept `text/vcard` à `https://localhost:5001/api/contacts` .

Pour ajouter une vCard à la collection de contacts en mémoire :

* Envoyez une `Post` demande à à l' `/api/contacts` aide d’un outil tel que poster.
* Attribuez à l’en-tête `Content-Type` la valeur `text/vcard`.
* Définissez `vCard` le texte dans le corps, mis en forme comme dans l’exemple précédent.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:web-api/advanced/formatting>
* <xref:grpc/dotnet-grpc>
