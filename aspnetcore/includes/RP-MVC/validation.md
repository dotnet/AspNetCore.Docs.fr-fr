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

## <a name="add-validation-rules-to-the-movie-model"></a><span data-ttu-id="ef275-101">Ajouter des règles de validation au modèle de film</span><span class="sxs-lookup"><span data-stu-id="ef275-101">Add validation rules to the movie model</span></span>

<span data-ttu-id="ef275-102">L’espace de noms DataAnnotations fournit un ensemble d’attributs de validation intégrés qui sont appliqués de façon déclarative à une classe ou à une propriété.</span><span class="sxs-lookup"><span data-stu-id="ef275-102">The DataAnnotations namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="ef275-103">DataAnnotations contient également des attributs de mise en forme, comme `DataType`, qui aident à effectuer la mise en forme et ne fournissent aucune validation.</span><span class="sxs-lookup"><span data-stu-id="ef275-103">DataAnnotations also contains formatting attributes like `DataType` that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="ef275-104">Mettez à jour la classe `Movie` pour tirer parti des attributs de validation intégrés `Required`, `StringLength`, `RegularExpression` et `Range`.</span><span class="sxs-lookup"><span data-stu-id="ef275-104">Update the `Movie` class to take advantage of the built-in `Required`, `StringLength`, `RegularExpression`, and `Range` validation attributes.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="ef275-105">Les attributs de validation spécifient le comportement que vous souhaitez appliquer sur les propriétés du modèle sur lesquels ils sont appliqués :</span><span class="sxs-lookup"><span data-stu-id="ef275-105">The validation attributes specify behavior that you want to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="ef275-106">Les attributs `Required` et `MinimumLength` indiquent qu’une propriété doit avoir une valeur, mais rien n’empêche un utilisateur d’entrer un espace pour satisfaire à cette validation.</span><span class="sxs-lookup"><span data-stu-id="ef275-106">The `Required` and `MinimumLength` attributes indicate that a property must have a value; but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="ef275-107">L’attribut `RegularExpression` sert à limiter les caractères pouvant être entrés.</span><span class="sxs-lookup"><span data-stu-id="ef275-107">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="ef275-108">Dans le code précédent, « Genre » :</span><span class="sxs-lookup"><span data-stu-id="ef275-108">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="ef275-109">Doit utiliser seulement des lettres.</span><span class="sxs-lookup"><span data-stu-id="ef275-109">Must only use letters.</span></span>
  * <span data-ttu-id="ef275-110">La première lettre doit être une majuscule.</span><span class="sxs-lookup"><span data-stu-id="ef275-110">The first letter is required to be uppercase.</span></span> <span data-ttu-id="ef275-111">Les espaces, les chiffres et les caractères spéciaux ne sont pas autorisés.</span><span class="sxs-lookup"><span data-stu-id="ef275-111">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="ef275-112">L’expression `RegularExpression` « Rating » :</span><span class="sxs-lookup"><span data-stu-id="ef275-112">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="ef275-113">Nécessite que le premier caractère soit une lettre majuscule.</span><span class="sxs-lookup"><span data-stu-id="ef275-113">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="ef275-114">Autorise les caractères spéciaux et les chiffres aux emplacements qui suivent.</span><span class="sxs-lookup"><span data-stu-id="ef275-114">Allows special characters and numbers in  subsequent spaces.</span></span> <span data-ttu-id="ef275-115">« PG-13 » est valide pour une évaluation, mais échoue pour un « Genre ».</span><span class="sxs-lookup"><span data-stu-id="ef275-115">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="ef275-116">L’attribut `Range` limite une valeur à une plage spécifiée.</span><span class="sxs-lookup"><span data-stu-id="ef275-116">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="ef275-117">L’attribut `StringLength` vous permet de définir la longueur maximale d’une propriété de chaîne, et éventuellement sa longueur minimale.</span><span class="sxs-lookup"><span data-stu-id="ef275-117">The `StringLength` attribute lets you set the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="ef275-118">Les types valeur (tels que `decimal`, `int`, `float` et `DateTime`) sont obligatoires par nature et n’ont pas besoin de l’attribut `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="ef275-118">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="ef275-119">L’application automatique des règles de validation par ASP.NET Core permet d’accroître la fiabilité de votre application.</span><span class="sxs-lookup"><span data-stu-id="ef275-119">Having validation rules automatically enforced by ASP.NET Core helps make your app more robust.</span></span> <span data-ttu-id="ef275-120">Cela garantit également que vous n’oublierez pas de valider un élément et que vous n’autoriserez pas par inadvertance l’insertion de données incorrectes dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="ef275-120">It also ensures that you can't forget to validate something and inadvertently let bad data into the database.</span></span>
