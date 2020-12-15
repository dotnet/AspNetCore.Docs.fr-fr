---
title: Partie 8, ajouter une validation
author: rick-anderson
description: Partie 8 de la série de didacticiels sur les Razor pages.
ms.author: riande
ms.custom: mvc, contperf-fy21q2
ms.date: 09/29/2020
no-loc:
- Index
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
uid: tutorials/razor-pages/validation
ms.openlocfilehash: 9774607b641005145bdb1c98d850c9ce79a25476
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97486120"
---
# <a name="part-8-of-tutorial-series-on-no-locrazor-pages"></a><span data-ttu-id="c64c8-103">Partie 8 de la série de didacticiels sur les Razor pages.</span><span class="sxs-lookup"><span data-stu-id="c64c8-103">Part 8 of tutorial series on Razor Pages.</span></span>

<span data-ttu-id="c64c8-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c64c8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c64c8-105">Dans cette section, une logique de validation est ajoutée au modèle `Movie`.</span><span class="sxs-lookup"><span data-stu-id="c64c8-105">In this section, validation logic is added to the `Movie` model.</span></span> <span data-ttu-id="c64c8-106">Les règles de validation sont appliquées chaque fois qu’un utilisateur crée ou modifie un film.</span><span class="sxs-lookup"><span data-stu-id="c64c8-106">The validation rules are enforced any time a user creates or edits a movie.</span></span>

## <a name="validation"></a><span data-ttu-id="c64c8-107">Validation</span><span class="sxs-lookup"><span data-stu-id="c64c8-107">Validation</span></span>

<span data-ttu-id="c64c8-108">[DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) (« **D** on't **R** epeat **Y** ourself », Ne vous répétez pas) constitue un principe clé du développement de logiciel.</span><span class="sxs-lookup"><span data-stu-id="c64c8-108">A key tenet of software development is called [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D** on't **R** epeat **Y** ourself").</span></span> <span data-ttu-id="c64c8-109">Razor Les pages encouragent le développement dans lequel la fonctionnalité est spécifiée une seule fois, et elle est reflétée dans l’application.</span><span class="sxs-lookup"><span data-stu-id="c64c8-109">Razor Pages encourages development where functionality is specified once, and it's reflected throughout the app.</span></span> <span data-ttu-id="c64c8-110">DRY peut aider à :</span><span class="sxs-lookup"><span data-stu-id="c64c8-110">DRY can help:</span></span>

* <span data-ttu-id="c64c8-111">Réduire la quantité de code dans une application.</span><span class="sxs-lookup"><span data-stu-id="c64c8-111">Reduce the amount of code in an app.</span></span>
* <span data-ttu-id="c64c8-112">Rendre le code moins sujet aux erreurs, et plus facile à tester et à maintenir.</span><span class="sxs-lookup"><span data-stu-id="c64c8-112">Make the code less error prone, and easier to test and maintain.</span></span>

<span data-ttu-id="c64c8-113">La prise en charge de la validation fournie par les Razor pages et les Entity Framework est un bon exemple du principe Dry :</span><span class="sxs-lookup"><span data-stu-id="c64c8-113">The validation support provided by Razor Pages and Entity Framework is a good example of the DRY principle:</span></span>

* <span data-ttu-id="c64c8-114">Les règles de validation sont spécifiées de façon déclarative à un seul emplacement, dans la classe de modèle.</span><span class="sxs-lookup"><span data-stu-id="c64c8-114">Validation rules are declaratively specified in one place, in the model class.</span></span>
* <span data-ttu-id="c64c8-115">Les règles sont appliquées partout dans l’application.</span><span class="sxs-lookup"><span data-stu-id="c64c8-115">Rules are enforced everywhere in the app.</span></span>

## <a name="add-validation-rules-to-the-movie-model"></a><span data-ttu-id="c64c8-116">Ajouter des règles de validation au modèle de film</span><span class="sxs-lookup"><span data-stu-id="c64c8-116">Add validation rules to the movie model</span></span>

<span data-ttu-id="c64c8-117">L' `DataAnnotations` espace de noms fournit les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="c64c8-117">The `DataAnnotations` namespace provides:</span></span>

* <span data-ttu-id="c64c8-118">Ensemble d’attributs de validation intégrés qui sont appliqués de façon déclarative à une classe ou une propriété.</span><span class="sxs-lookup"><span data-stu-id="c64c8-118">A set of built-in validation attributes that are applied declaratively to a class or property.</span></span>
* <span data-ttu-id="c64c8-119">Les attributs de mise en forme tels `[DataType]` que l’aide à la mise en forme et ne fournissent aucune validation.</span><span class="sxs-lookup"><span data-stu-id="c64c8-119">Formatting attributes like `[DataType]` that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="c64c8-120">Mettez à jour la classe `Movie` pour tirer parti des attributs de validation intégrés `[Required]`, `[StringLength]`, `[RegularExpression]` et `[Range]`.</span><span class="sxs-lookup"><span data-stu-id="c64c8-120">Update the `Movie` class to take advantage of the built-in `[Required]`, `[StringLength]`, `[RegularExpression]`, and `[Range]` validation attributes.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="c64c8-121">Les attributs de validation spécifient le comportement à appliquer sur les propriétés de modèle auxquelles ils sont appliqués :</span><span class="sxs-lookup"><span data-stu-id="c64c8-121">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="c64c8-122">Les attributs `[Required]` et `[MinimumLength]` indiquent qu’une propriété doit avoir une valeur.</span><span class="sxs-lookup"><span data-stu-id="c64c8-122">The `[Required]` and `[MinimumLength]` attributes indicate that a property must have a value.</span></span> <span data-ttu-id="c64c8-123">Rien n’empêche un utilisateur d’entrer un espace blanc pour satisfaire cette validation.</span><span class="sxs-lookup"><span data-stu-id="c64c8-123">Nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="c64c8-124">L’attribut `[RegularExpression]` sert à limiter les caractères pouvant être entrés.</span><span class="sxs-lookup"><span data-stu-id="c64c8-124">The `[RegularExpression]` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="c64c8-125">Dans le code précédent, `Genre` :</span><span class="sxs-lookup"><span data-stu-id="c64c8-125">In the preceding code, `Genre`:</span></span>

  * <span data-ttu-id="c64c8-126">Doit utiliser seulement des lettres.</span><span class="sxs-lookup"><span data-stu-id="c64c8-126">Must only use letters.</span></span>
  * <span data-ttu-id="c64c8-127">La première lettre doit être une majuscule.</span><span class="sxs-lookup"><span data-stu-id="c64c8-127">The first letter is required to be uppercase.</span></span> <span data-ttu-id="c64c8-128">Les espaces, les chiffres et les caractères spéciaux ne sont pas autorisés.</span><span class="sxs-lookup"><span data-stu-id="c64c8-128">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="c64c8-129">Le `RegularExpression` `Rating` :</span><span class="sxs-lookup"><span data-stu-id="c64c8-129">The `RegularExpression` `Rating`:</span></span>

  * <span data-ttu-id="c64c8-130">Nécessite que le premier caractère soit une lettre majuscule.</span><span class="sxs-lookup"><span data-stu-id="c64c8-130">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="c64c8-131">Autorise les caractères spéciaux et les nombres dans les espaces suivants.</span><span class="sxs-lookup"><span data-stu-id="c64c8-131">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="c64c8-132">« PG-13 » est valide pour une évaluation, mais échoue pour un `Genre` .</span><span class="sxs-lookup"><span data-stu-id="c64c8-132">"PG-13" is valid for a rating, but fails for a `Genre`.</span></span>

* <span data-ttu-id="c64c8-133">L’attribut `[Range]` limite une valeur à une plage spécifiée.</span><span class="sxs-lookup"><span data-stu-id="c64c8-133">The `[Range]` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="c64c8-134">L' `[StringLength]` attribut peut définir une longueur maximale d’une propriété de type chaîne et, éventuellement, sa longueur minimale.</span><span class="sxs-lookup"><span data-stu-id="c64c8-134">The `[StringLength]` attribute can set a maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="c64c8-135">Les types valeur, tels que,,, `decimal` `int` `float` `DateTime` , sont obligatoires par nature et n’ont pas besoin de l' `[Required]` attribut.</span><span class="sxs-lookup"><span data-stu-id="c64c8-135">Value types, such as `decimal`, `int`, `float`, `DateTime`, are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="c64c8-136">Les règles de validation précédentes sont utilisées pour la démonstration, elles ne sont pas optimales pour un système de production.</span><span class="sxs-lookup"><span data-stu-id="c64c8-136">The preceding validation rules are used for demonstration, they are not optimal for a production system.</span></span> <span data-ttu-id="c64c8-137">Par exemple, le précédent empêche l’entrée d’un film avec seulement deux caractères et n’autorise pas les caractères spéciaux dans `Genre` .</span><span class="sxs-lookup"><span data-stu-id="c64c8-137">For example, the preceding prevents entering a movie with only two chars and doesn't allow special characters in `Genre`.</span></span>

<span data-ttu-id="c64c8-138">L’application automatique des règles de validation par ASP.NET Core aide à :</span><span class="sxs-lookup"><span data-stu-id="c64c8-138">Having validation rules automatically enforced by ASP.NET Core helps:</span></span>

* <span data-ttu-id="c64c8-139">Permet de rendre l’application plus robuste.</span><span class="sxs-lookup"><span data-stu-id="c64c8-139">Helps make the app more robust.</span></span>
* <span data-ttu-id="c64c8-140">Réduisez les risques d’enregistrement des données non valides dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="c64c8-140">Reduce chances of saving invalid data to the database.</span></span>

### <a name="validation-error-ui-in-no-locrazor-pages"></a><span data-ttu-id="c64c8-141">Interface utilisateur d’erreur de validation dans les Razor pages</span><span class="sxs-lookup"><span data-stu-id="c64c8-141">Validation Error UI in Razor Pages</span></span>

<span data-ttu-id="c64c8-142">Exécutez l’application, puis accédez à Pages/Movies.</span><span class="sxs-lookup"><span data-stu-id="c64c8-142">Run the app and navigate to Pages/Movies.</span></span>

<span data-ttu-id="c64c8-143">Sélectionnez le lien **Créer nouveau**.</span><span class="sxs-lookup"><span data-stu-id="c64c8-143">Select the **Create New** link.</span></span> <span data-ttu-id="c64c8-144">Complétez le formulaire avec des valeurs non valides.</span><span class="sxs-lookup"><span data-stu-id="c64c8-144">Complete the form with some invalid values.</span></span> <span data-ttu-id="c64c8-145">Quand la validation jQuery côté client détecte l’erreur, elle affiche un message d’erreur.</span><span class="sxs-lookup"><span data-stu-id="c64c8-145">When jQuery client-side validation detects the error, it displays an error message.</span></span>

![Formulaire de vue Movie avec plusieurs erreurs de validation jQuery côté client](validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

<span data-ttu-id="c64c8-147">Notez que le formulaire a affiché automatiquement un message d’erreur de validation dans chaque champ contenant une valeur non valide.</span><span class="sxs-lookup"><span data-stu-id="c64c8-147">Notice how the form has automatically rendered a validation error message in each field containing an invalid value.</span></span> <span data-ttu-id="c64c8-148">Les erreurs sont appliquées à la fois côté client, à l’aide de JavaScript et jQuery et côté serveur, lorsqu’un utilisateur a désactivé JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c64c8-148">The errors are enforced both client-side, using JavaScript and jQuery, and server-side, when a user has JavaScript disabled.</span></span>

<span data-ttu-id="c64c8-149">L’un des principaux avantages est qu' **aucune** modification du code n’était nécessaire dans les pages de création ou de modification.</span><span class="sxs-lookup"><span data-stu-id="c64c8-149">A significant benefit is that **no** code changes were necessary in the Create or Edit pages.</span></span> <span data-ttu-id="c64c8-150">Une fois que les annotations de données ont été appliquées au modèle, l’interface utilisateur de validation a été activée.</span><span class="sxs-lookup"><span data-stu-id="c64c8-150">Once data annotations were applied to the model, the validation UI was enabled.</span></span> <span data-ttu-id="c64c8-151">Les Razor pages créées dans ce didacticiel ont automatiquement récupéré les règles de validation, à l’aide des attributs de validation sur les propriétés de la `Movie` classe de modèle.</span><span class="sxs-lookup"><span data-stu-id="c64c8-151">The Razor Pages created in this tutorial automatically picked up the validation rules, using validation attributes on the properties of the `Movie` model class.</span></span> <span data-ttu-id="c64c8-152">Testez la validation à l’aide de la page de modification. La même validation est appliquée.</span><span class="sxs-lookup"><span data-stu-id="c64c8-152">Test validation using the Edit page, the same validation is applied.</span></span>

<span data-ttu-id="c64c8-153">Les données de formulaire ne sont pas publiées sur le serveur tant qu’il y a des erreurs de validation côté client.</span><span class="sxs-lookup"><span data-stu-id="c64c8-153">The form data isn't posted to the server until there are no client-side validation errors.</span></span> <span data-ttu-id="c64c8-154">Vérifiez que les données du formulaire ne sont pas publiées à l’aide d’une ou de plusieurs des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="c64c8-154">Verify form data isn't posted by one or more of the following approaches:</span></span>

* <span data-ttu-id="c64c8-155">Placez un point d’arrêt dans la méthode `OnPostAsync`.</span><span class="sxs-lookup"><span data-stu-id="c64c8-155">Put a break point in the `OnPostAsync` method.</span></span> <span data-ttu-id="c64c8-156">Envoyez le formulaire en sélectionnant **créer** ou **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="c64c8-156">Submit the form by selecting **Create** or **Save**.</span></span> <span data-ttu-id="c64c8-157">Le point d’arrêt n’est jamais atteint.</span><span class="sxs-lookup"><span data-stu-id="c64c8-157">The break point is never hit.</span></span>
* <span data-ttu-id="c64c8-158">Utilisez l’[outil Fiddler](https://www.telerik.com/fiddler).</span><span class="sxs-lookup"><span data-stu-id="c64c8-158">Use the [Fiddler tool](https://www.telerik.com/fiddler).</span></span>
* <span data-ttu-id="c64c8-159">Utilisez les outils de développement du navigateur pour surveiller le trafic réseau.</span><span class="sxs-lookup"><span data-stu-id="c64c8-159">Use the browser developer tools to monitor network traffic.</span></span>

### <a name="server-side-validation"></a><span data-ttu-id="c64c8-160">Validation côté serveur</span><span class="sxs-lookup"><span data-stu-id="c64c8-160">Server-side validation</span></span>

<span data-ttu-id="c64c8-161">Quand JavaScript est désactivé dans le navigateur, l’envoi du formulaire avec des erreurs poste les données sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="c64c8-161">When JavaScript is disabled in the browser, submitting the form with errors will post to the server.</span></span>

<span data-ttu-id="c64c8-162">Facultatif : Testez la validation côté serveur :</span><span class="sxs-lookup"><span data-stu-id="c64c8-162">Optional, test server-side validation:</span></span>

1. <span data-ttu-id="c64c8-163">Désactivez JavaScript dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="c64c8-163">Disable JavaScript in the browser.</span></span> <span data-ttu-id="c64c8-164">JavaScript peut être désactivé à l’aide des outils de développement du navigateur.</span><span class="sxs-lookup"><span data-stu-id="c64c8-164">JavaScript can be disabled using browser's developer tools.</span></span> <span data-ttu-id="c64c8-165">Si JavaScript ne peut pas être désactivé dans le navigateur, essayez un autre navigateur.</span><span class="sxs-lookup"><span data-stu-id="c64c8-165">If JavaScript cannot be disabled in the browser, try another browser.</span></span>
1. <span data-ttu-id="c64c8-166">Définissez un point d’arrêt dans la méthode `OnPostAsync` de la page Créer ou Modifier.</span><span class="sxs-lookup"><span data-stu-id="c64c8-166">Set a break point in the `OnPostAsync` method of the Create or Edit page.</span></span>
1. <span data-ttu-id="c64c8-167">Envoyez un formulaire avec des données non valides.</span><span class="sxs-lookup"><span data-stu-id="c64c8-167">Submit a form with invalid data.</span></span>
1. <span data-ttu-id="c64c8-168">Vérifiez que l’état de modèle n’est pas valide :</span><span class="sxs-lookup"><span data-stu-id="c64c8-168">Verify the model state is invalid:</span></span>

   ```csharp
    if (!ModelState.IsValid)
    {
       return Page();
    }
   ```
  
<span data-ttu-id="c64c8-169">Vous pouvez également [désactiver la validation côté client sur le serveur](xref:mvc/models/validation#disable-client-side-validation).</span><span class="sxs-lookup"><span data-stu-id="c64c8-169">Alternatively, [Disable client-side validation on the server](xref:mvc/models/validation#disable-client-side-validation).</span></span>

<span data-ttu-id="c64c8-170">Le code suivant affiche la partie de la page *Create.cshtml* générée automatiquement plus tôt dans le tutoriel.</span><span class="sxs-lookup"><span data-stu-id="c64c8-170">The following code shows a portion of the *Create.cshtml* page scaffolded earlier in the tutorial.</span></span> <span data-ttu-id="c64c8-171">Elle est utilisée par les pages de création et de modification pour :</span><span class="sxs-lookup"><span data-stu-id="c64c8-171">It's used by the Create and Edit pages to:</span></span>

* <span data-ttu-id="c64c8-172">Affichez le formulaire initial.</span><span class="sxs-lookup"><span data-stu-id="c64c8-172">Display the initial form.</span></span>
* <span data-ttu-id="c64c8-173">Réaffichez le formulaire en cas d’erreur.</span><span class="sxs-lookup"><span data-stu-id="c64c8-173">Redisplay the form in the event of an error.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

<span data-ttu-id="c64c8-174">Le [Tag Helper d’entrée](xref:mvc/views/working-with-forms) utilise les attributs [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) et produit les attributs HTML nécessaires à la validation jQuery côté client.</span><span class="sxs-lookup"><span data-stu-id="c64c8-174">The [Input Tag Helper](xref:mvc/views/working-with-forms) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span> <span data-ttu-id="c64c8-175">Le [Tag Helper Validation](xref:mvc/views/working-with-forms#the-validation-tag-helpers) affiche les erreurs de validation.</span><span class="sxs-lookup"><span data-stu-id="c64c8-175">The [Validation Tag Helper](xref:mvc/views/working-with-forms#the-validation-tag-helpers) displays validation errors.</span></span> <span data-ttu-id="c64c8-176">Pour plus d’informations, consultez [Validation](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="c64c8-176">See [Validation](xref:mvc/models/validation) for more information.</span></span>

<span data-ttu-id="c64c8-177">Les pages Créer et Modifier ne contiennent pas de règles de validation.</span><span class="sxs-lookup"><span data-stu-id="c64c8-177">The Create and Edit pages have no validation rules in them.</span></span> <span data-ttu-id="c64c8-178">Les règles de validation et les chaînes d’erreur sont spécifiées uniquement dans la classe `Movie`.</span><span class="sxs-lookup"><span data-stu-id="c64c8-178">The validation rules and the error strings are specified only in the `Movie` class.</span></span> <span data-ttu-id="c64c8-179">Ces règles de validation sont appliquées automatiquement aux Razor pages qui modifient le `Movie` modèle.</span><span class="sxs-lookup"><span data-stu-id="c64c8-179">These validation rules are automatically applied to Razor Pages that edit the `Movie` model.</span></span>

<span data-ttu-id="c64c8-180">Quand la logique de validation doit être modifiée, cela s’effectue uniquement dans le modèle.</span><span class="sxs-lookup"><span data-stu-id="c64c8-180">When validation logic needs to change, it's done only in the model.</span></span> <span data-ttu-id="c64c8-181">La validation est appliquée de manière cohérente dans toute l’application, la logique de validation est définie à un seul emplacement.</span><span class="sxs-lookup"><span data-stu-id="c64c8-181">Validation is applied consistently throughout the application, validation logic is defined in one place.</span></span> <span data-ttu-id="c64c8-182">La validation dans un emplacement unique permet de maintenir votre code clair, et d’en faciliter la maintenance et la mise à jour.</span><span class="sxs-lookup"><span data-stu-id="c64c8-182">Validation in one place helps keep the code clean, and makes it easier to maintain and update.</span></span>

## <a name="use-datatype-attributes"></a><span data-ttu-id="c64c8-183">Utiliser les attributs DataType</span><span class="sxs-lookup"><span data-stu-id="c64c8-183">Use DataType Attributes</span></span>

<span data-ttu-id="c64c8-184">Examiner la classe `Movie`.</span><span class="sxs-lookup"><span data-stu-id="c64c8-184">Examine the `Movie` class.</span></span> <span data-ttu-id="c64c8-185">L’espace de noms `System.ComponentModel.DataAnnotations` fournit des attributs de mise en forme en plus de l’ensemble intégré d’attributs de validation.</span><span class="sxs-lookup"><span data-stu-id="c64c8-185">The `System.ComponentModel.DataAnnotations` namespace provides formatting attributes in addition to the built-in set of validation attributes.</span></span> <span data-ttu-id="c64c8-186">L’attribut `[DataType]` est appliqué aux propriétés `ReleaseDate` et `Price`.</span><span class="sxs-lookup"><span data-stu-id="c64c8-186">The `[DataType]` attribute is applied to the `ReleaseDate` and `Price` properties.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

<span data-ttu-id="c64c8-187">Les `[DataType]` attributs fournissent :</span><span class="sxs-lookup"><span data-stu-id="c64c8-187">The `[DataType]` attributes provide:</span></span>

* <span data-ttu-id="c64c8-188">Indicateurs pour le moteur d’affichage pour mettre en forme les données.</span><span class="sxs-lookup"><span data-stu-id="c64c8-188">Hints for the view engine to format the data.</span></span>
* <span data-ttu-id="c64c8-189">Fournit des attributs tels que `<a>` pour les URL et `<a href="mailto:EmailAddress.com">` pour les courriers électroniques.</span><span class="sxs-lookup"><span data-stu-id="c64c8-189">Supplies attributes such as `<a>` for URL's and `<a href="mailto:EmailAddress.com">` for email.</span></span>

<span data-ttu-id="c64c8-190">Utilisez l’attribut `[RegularExpression]` pour valider le format des données.</span><span class="sxs-lookup"><span data-stu-id="c64c8-190">Use the `[RegularExpression]` attribute to validate the format of the data.</span></span> <span data-ttu-id="c64c8-191">L’attribut `[DataType]` sert à spécifier un type de données qui est plus spécifique que le type intrinsèque de la base de données.</span><span class="sxs-lookup"><span data-stu-id="c64c8-191">The `[DataType]` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="c64c8-192">`[DataType]` les attributs ne sont pas des attributs de validation.</span><span class="sxs-lookup"><span data-stu-id="c64c8-192">`[DataType]` attributes aren't validation attributes.</span></span> <span data-ttu-id="c64c8-193">Dans l’exemple d’application, seule la date est affichée, sans l’heure.</span><span class="sxs-lookup"><span data-stu-id="c64c8-193">In the sample application, only the date is displayed, without time.</span></span>

<span data-ttu-id="c64c8-194">L' `DataType` énumération fournit de nombreux types de données, tels que,,,,, `Date` `Time` `PhoneNumber` `Currency` `EmailAddress` et bien plus encore.</span><span class="sxs-lookup"><span data-stu-id="c64c8-194">The `DataType` enumeration provides many data types, such as `Date`, `Time`, `PhoneNumber`, `Currency`, `EmailAddress`, and more.</span></span> 

<span data-ttu-id="c64c8-195">`[DataType]`Attributs :</span><span class="sxs-lookup"><span data-stu-id="c64c8-195">The `[DataType]` attributes:</span></span>

* <span data-ttu-id="c64c8-196">Peut permettre à l’application de fournir automatiquement des fonctionnalités propres au type.</span><span class="sxs-lookup"><span data-stu-id="c64c8-196">Can enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="c64c8-197">Par exemple, il est possible de créer un lien `mailto:` pour `DataType.EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="c64c8-197">For example, a `mailto:` link can be created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="c64c8-198">Peut fournir un sélecteur `DataType.Date` de date dans les navigateurs qui prennent en charge HTML5.</span><span class="sxs-lookup"><span data-stu-id="c64c8-198">Can provide a date selector `DataType.Date` in browsers that support HTML5.</span></span>
* <span data-ttu-id="c64c8-199">Émettez le code HTML 5 `data-` , prononcé « Dash de données », attributs consommés par les navigateurs HTML 5.</span><span class="sxs-lookup"><span data-stu-id="c64c8-199">Emit HTML 5 `data-`, pronounced "data dash", attributes that HTML 5 browsers consume.</span></span>
* <span data-ttu-id="c64c8-200">Ne fournissez **pas** de validation.</span><span class="sxs-lookup"><span data-stu-id="c64c8-200">Do **not** provide any validation.</span></span>

<span data-ttu-id="c64c8-201">`DataType.Date` ne spécifie pas le format de la date qui s’affiche.</span><span class="sxs-lookup"><span data-stu-id="c64c8-201">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="c64c8-202">Par défaut, le champ de données est affiché conformément aux formats par défaut basés sur le `CultureInfo` du serveur.</span><span class="sxs-lookup"><span data-stu-id="c64c8-202">By default, the data field is displayed according to the default formats based on the server's `CultureInfo`.</span></span>

<span data-ttu-id="c64c8-203">L’annotation de données `[Column(TypeName = "decimal(18, 2)")]` est nécessaire pour qu’Entity Framework Core puisse correctement mapper `Price` en devise dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="c64c8-203">The `[Column(TypeName = "decimal(18, 2)")]` data annotation is required so Entity Framework Core can correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="c64c8-204">Pour plus d’informations, consultez [Types de données](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="c64c8-204">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="c64c8-205">L’attribut `[DisplayFormat]` est utilisé pour spécifier explicitement le format de date :</span><span class="sxs-lookup"><span data-stu-id="c64c8-205">The `[DisplayFormat]` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

<span data-ttu-id="c64c8-206">Le `ApplyFormatInEditMode` paramètre spécifie que la mise en forme sera appliquée lorsque la valeur sera affichée pour modification.</span><span class="sxs-lookup"><span data-stu-id="c64c8-206">The `ApplyFormatInEditMode` setting specifies that the formatting will be applied when the value is displayed for editing.</span></span> <span data-ttu-id="c64c8-207">Ce comportement peut ne pas être souhaité pour certains champs.</span><span class="sxs-lookup"><span data-stu-id="c64c8-207">That behavior may not be wanted for some fields.</span></span> <span data-ttu-id="c64c8-208">Par exemple, dans les valeurs monétaires, le symbole monétaire n’est généralement pas souhaité dans l’interface utilisateur de modification.</span><span class="sxs-lookup"><span data-stu-id="c64c8-208">For example, in currency values, the currency symbol is usually not wanted in the edit UI.</span></span>

<span data-ttu-id="c64c8-209">L’attribut `[DisplayFormat]` peut être utilisé seul, mais il est généralement préférable d’utiliser l’attribut `[DataType]`.</span><span class="sxs-lookup"><span data-stu-id="c64c8-209">The `[DisplayFormat]` attribute can be used by itself, but it's generally a good idea to use the `[DataType]` attribute.</span></span> <span data-ttu-id="c64c8-210">L’attribut `[DataType]` transmet la sémantique des données, plutôt que la manière de l’afficher à l’écran.</span><span class="sxs-lookup"><span data-stu-id="c64c8-210">The `[DataType]` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="c64c8-211">L' `[DataType]` attribut offre les avantages suivants qui ne sont pas disponibles avec `[DisplayFormat]` :</span><span class="sxs-lookup"><span data-stu-id="c64c8-211">The `[DataType]` attribute provides the following benefits that aren't available with `[DisplayFormat]`:</span></span>

* <span data-ttu-id="c64c8-212">Le navigateur peut activer des fonctionnalités HTML5, par exemple pour afficher un contrôle de calendrier, les paramètres régionaux-symbole monétaire approprié, les liens de messagerie, etc.</span><span class="sxs-lookup"><span data-stu-id="c64c8-212">The browser can enable HTML5 features, for example to show a calendar control, the locale-appropriate currency symbol, email links, etc.</span></span>
* <span data-ttu-id="c64c8-213">Par défaut, le navigateur restitue les données à l’aide du format correct en fonction de ses paramètres régionaux.</span><span class="sxs-lookup"><span data-stu-id="c64c8-213">By default, the browser renders data using the correct format based on its locale.</span></span>
* <span data-ttu-id="c64c8-214">L’attribut `[DataType]` peut permettre à l’infrastructure ASP.NET Core de choisir le modèle de champ approprié pour afficher les données.</span><span class="sxs-lookup"><span data-stu-id="c64c8-214">The `[DataType]` attribute can enable the ASP.NET Core framework to choose the right field template to render the data.</span></span> <span data-ttu-id="c64c8-215">`DisplayFormat`, S’il est utilisé par lui-même, utilise le modèle de chaîne.</span><span class="sxs-lookup"><span data-stu-id="c64c8-215">The `DisplayFormat`, if used by itself, uses the string template.</span></span>

<span data-ttu-id="c64c8-216">**Remarque :** la validation jQuery ne fonctionne pas avec l' `[Range]` attribut et `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="c64c8-216">**Note:** jQuery validation doesn't work with the `[Range]` attribute and `DateTime`.</span></span> <span data-ttu-id="c64c8-217">Par exemple, le code suivant affiche toujours une erreur de validation côté client, même quand la date se trouve dans la plage spécifiée :</span><span class="sxs-lookup"><span data-stu-id="c64c8-217">For example, the following code will always display a client-side validation error, even when the date is in the specified range:</span></span>

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

<span data-ttu-id="c64c8-218">Il est recommandé d’éviter de compiler des dates difficiles dans les modèles, de sorte que l’utilisation de l' `[Range]` attribut et `DateTime` est déconseillée.</span><span class="sxs-lookup"><span data-stu-id="c64c8-218">It's a best practice to avoid compiling hard dates in models, so using the `[Range]` attribute and `DateTime` is discouraged.</span></span> <span data-ttu-id="c64c8-219">Utilisez la [configuration](xref:fundamentals/configuration/index) pour les plages de dates et d’autres valeurs sujettes à des modifications fréquentes au lieu de les spécifier dans le code.</span><span class="sxs-lookup"><span data-stu-id="c64c8-219">Use [Configuration](xref:fundamentals/configuration/index) for date ranges and other values that are subject to frequent change rather than specifying it in code.</span></span>

<span data-ttu-id="c64c8-220">Le code suivant illustre la combinaison d’attributs sur une seule ligne :</span><span class="sxs-lookup"><span data-stu-id="c64c8-220">The following code shows combining attributes on one line:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDAmult.cs?name=snippet1)]

<span data-ttu-id="c64c8-221">[Prise en main de Razor Pages et EF Core](xref:data/ef-rp/intro) affiche des opérations de EF Core avancées avec des Razor pages.</span><span class="sxs-lookup"><span data-stu-id="c64c8-221">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) shows advanced EF Core operations with Razor Pages.</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="c64c8-222">Appliquer des migrations</span><span class="sxs-lookup"><span data-stu-id="c64c8-222">Apply migrations</span></span>

<span data-ttu-id="c64c8-223">Le DataAnnotations appliqué à la classe modifie le schéma.</span><span class="sxs-lookup"><span data-stu-id="c64c8-223">The DataAnnotations applied to the class changes the schema.</span></span> <span data-ttu-id="c64c8-224">Par exemple, DataAnnotations appliqué au champ `Title` :</span><span class="sxs-lookup"><span data-stu-id="c64c8-224">For example, the DataAnnotations applied to the `Title` field:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet11)]

* <span data-ttu-id="c64c8-225">limite les caractères à 60 ;</span><span class="sxs-lookup"><span data-stu-id="c64c8-225">Limits the characters to 60.</span></span>
* <span data-ttu-id="c64c8-226">n’autorise pas de valeur `null`.</span><span class="sxs-lookup"><span data-stu-id="c64c8-226">Doesn't allow a `null` value.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c64c8-227">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c64c8-227">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c64c8-228">La table `Movie` a actuellement le schéma suivant :</span><span class="sxs-lookup"><span data-stu-id="c64c8-228">The `Movie` table currently has the following schema:</span></span>

```sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (MAX)  NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (MAX)  NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (MAX)  NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

<span data-ttu-id="c64c8-229">Les modifications précédentes du schéma n’entraînent pas la levée d’une exception par EF.</span><span class="sxs-lookup"><span data-stu-id="c64c8-229">The preceding schema changes don't cause EF to throw an exception.</span></span> <span data-ttu-id="c64c8-230">Cependant, créez une migration pour que le schéma soit cohérent avec le modèle.</span><span class="sxs-lookup"><span data-stu-id="c64c8-230">However, create a migration so the schema is consistent with the model.</span></span>

<span data-ttu-id="c64c8-231">Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet > Console du gestionnaire de package**.</span><span class="sxs-lookup"><span data-stu-id="c64c8-231">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="c64c8-232">Dans la console du gestionnaire de package, entrez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="c64c8-232">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration New_DataAnnotations
Update-Database
```

<span data-ttu-id="c64c8-233">`Update-Database` exécute les méthodes `Up` de la classe `New_DataAnnotations`.</span><span class="sxs-lookup"><span data-stu-id="c64c8-233">`Update-Database` runs the `Up` methods of the `New_DataAnnotations` class.</span></span> <span data-ttu-id="c64c8-234">Examinez la méthode `Up` :</span><span class="sxs-lookup"><span data-stu-id="c64c8-234">Examine the `Up` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Migrations/20190724163003_New_DataAnnotations.cs?name=snippet)]

<span data-ttu-id="c64c8-235">La table `Movie` mise à jour a le schéma suivant :</span><span class="sxs-lookup"><span data-stu-id="c64c8-235">The updated `Movie` table has the following schema:</span></span>

```sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (60)   NOT NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (30)   NOT NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (5)    NOT NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c64c8-236">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="c64c8-236">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="c64c8-237">Des migrations ne sont pas requises pour SQLite.</span><span class="sxs-lookup"><span data-stu-id="c64c8-237">Migrations are not required for SQLite.</span></span>

---

### <a name="publish-to-azure"></a><span data-ttu-id="c64c8-238">Publication dans Azure</span><span class="sxs-lookup"><span data-stu-id="c64c8-238">Publish to Azure</span></span>

<span data-ttu-id="c64c8-239">Pour plus d’informations sur le déploiement sur Azure, consultez [Didacticiel : créer une application ASP.net core dans Azure avec SQL Database](/azure/app-service/tutorial-dotnetcore-sqldb-app).</span><span class="sxs-lookup"><span data-stu-id="c64c8-239">For information on deploying to Azure, see [Tutorial: Build an ASP.NET Core app in Azure with SQL Database](/azure/app-service/tutorial-dotnetcore-sqldb-app).</span></span>

<span data-ttu-id="c64c8-240">Merci d’avoir effectué cette introduction aux Razor pages.</span><span class="sxs-lookup"><span data-stu-id="c64c8-240">Thanks for completing this introduction to Razor Pages.</span></span> <span data-ttu-id="c64c8-241">[Prise en main de Razor Pages et EF Core](xref:data/ef-rp/intro) est un excellent suivi de ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="c64c8-241">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) is an excellent follow up to this tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c64c8-242">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="c64c8-242">Additional resources</span></span>

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>

> [!div class="step-by-step"]
> [<span data-ttu-id="c64c8-243">Précédent : ajouter un nouveau champ</span><span class="sxs-lookup"><span data-stu-id="c64c8-243">Previous: Add a new field</span></span>](xref:tutorials/razor-pages/new-field)
