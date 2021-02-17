---
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
ms.openlocfilehash: c96c5e66d2e15db03c321d239a64b98119a7543a
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552189"
---
<!-- USED in RP and MVC tutorial -->

## <a name="add-validation-rules-to-the-movie-model"></a>Ajouter des règles de validation au modèle de film

L’espace de noms DataAnnotations fournit un ensemble d’attributs de validation intégrés qui sont appliqués de façon déclarative à une classe ou à une propriété. DataAnnotations contient également des attributs de mise en forme, comme `DataType`, qui aident à effectuer la mise en forme et ne fournissent aucune validation.

Mettez à jour la classe `Movie` pour tirer parti des attributs de validation intégrés `Required`, `StringLength`, `RegularExpression` et `Range`.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRatingDA.cs?name=snippet1)]

Les attributs de validation spécifient le comportement que vous souhaitez appliquer sur les propriétés du modèle sur lesquels ils sont appliqués :

* Les attributs `Required` et `MinimumLength` indiquent qu’une propriété doit avoir une valeur, mais rien n’empêche un utilisateur d’entrer un espace pour satisfaire à cette validation.
* L’attribut `RegularExpression` sert à limiter les caractères pouvant être entrés. Dans le code précédent, « Genre » :

  * Doit utiliser seulement des lettres.
  * La première lettre doit être une majuscule. Les espaces, les chiffres et les caractères spéciaux ne sont pas autorisés.

* L’expression `RegularExpression` « Rating » :

  * Nécessite que le premier caractère soit une lettre majuscule.
  * Autorise les caractères spéciaux et les chiffres aux emplacements qui suivent. « PG-13 » est valide pour une évaluation, mais échoue pour un « Genre ».

* L’attribut `Range` limite une valeur à une plage spécifiée.
* L’attribut `StringLength` vous permet de définir la longueur maximale d’une propriété de chaîne, et éventuellement sa longueur minimale.
* Les types valeur (tels que `decimal`, `int`, `float` et `DateTime`) sont obligatoires par nature et n’ont pas besoin de l’attribut `[Required]`.

L’application automatique des règles de validation par ASP.NET Core permet d’accroître la fiabilité de votre application. Cela garantit également que vous n’oublierez pas de valider un élément et que vous n’autoriserez pas par inadvertance l’insertion de données incorrectes dans la base de données.
