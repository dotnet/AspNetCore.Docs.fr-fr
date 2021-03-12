---
title: Utiliser les conventions d’API web
author: pranavkm
description: Découvrez plus d’informations sur les conventions d’API web dans ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: scaddie
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
uid: web-api/advanced/conventions
ms.openlocfilehash: 1e6526f46fbd177add3699fb5b667021b741c6a4
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587838"
---
# <a name="use-web-api-conventions"></a>Utiliser les conventions d’API web

Par [Pranav Krishnamoorthy](https://github.com/pranavkm) et [Scott Addie](https://github.com/scottaddie)

ASP.NET Core 2.2 et ses versions ultérieures incluent un moyen d’extraire la [documentation des API](xref:tutorials/web-api-help-pages-using-swagger) courantes et de l’appliquer à plusieurs actions, à plusieurs contrôleurs ou à tous les contrôleurs au sein d’un assembly. Les conventions d’API Web sont un substitut de la décoration des actions individuelles avec [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) .

Une convention vous permet de :

* Définir les types de retours les plus courants et les codes d’état retournés à partir d’un type d’action spécifique.
* Identifier les actions qui s’écartent de la norme définie.

ASP.NET Core MVC 2.2 et ses versions ultérieures incluent un ensemble de conventions par défaut dans <xref:Microsoft.AspNetCore.Mvc.DefaultApiConventions?displayProperty=fullName>. Les conventions sont basées sur le contrôleur (*ValuesController.cs*) fourni dans le modèle de projet de l’**API** ASP.NET Core. Si vos actions suivent les profils dans le modèle, vous devriez normalement réussir à utiliser les conventions par défaut. Si les conventions par défaut ne répondent pas à vos besoins, consultez [Créer des conventions d’API web](#create-web-api-conventions).

À l’exécution, <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> comprend les conventions. `ApiExplorer` est l’abstraction de MVC pour communiquer avec des générateurs de documents [OpenAPI](https://www.openapis.org/) (également nommé Swagger). Les attributs provenant de la convention appliquée sont associés à une action et sont inclus dans la documentation OpenAPI de l’action. Les [analyseurs d’API](xref:web-api/advanced/analyzers) comprennent également les conventions. Si votre action n’est pas conventionnelle (par exemple, elle retourne un code d’état qui n’est pas documenté par la convention appliquée), un avertissement vous encourage à documenter le code d’état.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/advanced/conventions/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="apply-web-api-conventions"></a>Appliquer des conventions d’API web

Les conventions ne se combinent pas : chaque action peut être associée à une seule convention. Les conventions plus spécifiques sont prioritaires par rapport à celles qui sont moins spécifiques. La sélection est non déterministe quand plusieurs conventions de même priorité s’appliquent à une action. Les options suivantes sont disponibles pour appliquer une convention à une action, de la plus spécifique à la moins spécifique :

1. `Microsoft.AspNetCore.Mvc.ApiConventionMethodAttribute`&mdash;S’applique à des actions individuelles et spécifie le type de Convention et la méthode de Convention qui s’appliquent.

    Dans l’exemple suivant, la méthode de convention `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` du type de convention par défaut est appliquée à l’action `Update` :

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=snippet_ApiConventionMethod&highlight=3)]

    La méthode de convention `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` applique les attributs suivants à l’action :

    ```csharp
    [ProducesDefaultResponseType]
    [ProducesResponseType(StatusCodes.Status204NoContent)]
    [ProducesResponseType(StatusCodes.Status404NotFound)]
    [ProducesResponseType(StatusCodes.Status400BadRequest)]
    ```

    Pour plus d’informations sur `[ProducesDefaultResponseType]`, consultez [Réponse par défaut](https://swagger.io/docs/specification/describing-responses/#default).

1. `Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` appliqué à un contrôleur &mdash; applique le type de convention spécifié à toutes les actions sur le contrôleur. Une méthode de Convention est marquée avec des indicateurs qui déterminent les actions auxquelles la méthode de Convention s’applique. Pour plus d’informations sur les indicateurs, consultez [Créer des conventions d’API web](#create-web-api-conventions)).

    Dans l’exemple suivant, l’ensemble de conventions par défaut est appliqué à toutes les actions dans *ContactsConventionController* :

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=snippet_ApiConventionTypeAttribute&highlight=2)]

1. `Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` appliqué à un assembly &mdash; applique le type de convention spécifié à tous les contrôleurs dans l’assembly actif. Il est recommandé d’appliquer des attributs de niveau assembly au fichier *Startup.cs*.

    Dans l’exemple suivant, l’ensemble de conventions par défaut est appliqué à tous les contrôleurs dans l’assembly :

    [!code-csharp[](conventions/sample/Startup.cs?name=snippet_ApiConventionTypeAttribute&highlight=1)]

## <a name="create-web-api-conventions"></a>Créer des conventions d’API web

Si les conventions d’API par défaut ne répondent pas à vos besoins, créez vos propres conventions. Une convention est :

* Un type statique avec des méthodes.
* Capable de définir des [types de réponse](#response-types) et des [exigences de noms](#naming-requirements) sur les actions.

### <a name="response-types"></a>Types de réponse

Ces méthodes sont annotées avec des attributs `[ProducesResponseType]` ou `[ProducesDefaultResponseType]`. Par exemple :

```csharp
public static class MyAppConventions
{
    [ProducesResponseType(StatusCodes.Status200OK)]
    [ProducesResponseType(StatusCodes.Status404NotFound)]
    public static void Find(int id)
    {
    }
}
```

Si des attributs de métadonnées plus spécifiques sont absents, l’application de cette convention à un assembly considère que :

* La méthode de convention s’applique à toute action nommée `Find`.
* Un paramètre nommé `id` est présent sur l’action `Find`.

### <a name="naming-requirements"></a>Conditions de nommage

Les attributs `[ApiConventionNameMatch]` et `[ApiConventionTypeMatch]` peuvent être appliqués à la méthode de convention qui détermine les actions auxquelles ils s’appliquent. Par exemple :

```csharp
[ProducesResponseType(StatusCodes.Status200OK)]
[ProducesResponseType(StatusCodes.Status404NotFound)]
[ApiConventionNameMatch(ApiConventionNameMatchBehavior.Prefix)]
public static void Find(
    [ApiConventionNameMatch(ApiConventionNameMatchBehavior.Suffix)]
    int id)
{ }
```

Dans l’exemple précédent :

* L’option `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Prefix` appliquée à la méthode indique que la convention correspond à n’importe action préfixée par « Find ». Les exemples d’actions correspondantes incluent `Find`, `FindPet`, et `FindById`.
* `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Suffix` appliqué au paramètre indique que la convention correspond aux méthodes avec un seul paramètre se terminant par l’identificateur du suffixe. Les exemples incluent des paramètres comme `id` ou `petId`. `ApiConventionTypeMatch` peut être appliqué de façon similaire à des types pour contraindre le type du paramètre. Un argument `params[]` indique les paramètres restants pour lesquels une correspondance explicite n’est pas nécessaire.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:web-api/advanced/analyzers>
* <xref:tutorials/web-api-help-pages-using-swagger>
