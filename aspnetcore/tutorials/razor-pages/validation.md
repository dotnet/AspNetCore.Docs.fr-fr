---
title: 'Partie 8, ajouter une validation à une :::no-loc(Razor)::: Page ASP.net Core'
author: rick-anderson
description: 'Partie 8 de la série de didacticiels sur les :::no-loc(Razor)::: pages.'
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
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
uid: tutorials/razor-pages/validation
ms.openlocfilehash: 991a0f29c0edc5a220dfde69bd22dc4ed758394d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060727"
---
# <a name="part-8-add-validation-to-an-aspnet-core-no-locrazor-page"></a><span data-ttu-id="5587b-103">Partie 8, ajouter une validation à une :::no-loc(Razor)::: Page ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="5587b-103">Part 8, add validation to an ASP.NET Core :::no-loc(Razor)::: Page</span></span>

<span data-ttu-id="5587b-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5587b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="5587b-105">Dans cette section, une logique de validation est ajoutée au modèle `Movie`.</span><span class="sxs-lookup"><span data-stu-id="5587b-105">In this section, validation logic is added to the `Movie` model.</span></span> <span data-ttu-id="5587b-106">Les règles de validation sont appliquées chaque fois qu’un utilisateur crée ou modifie un film.</span><span class="sxs-lookup"><span data-stu-id="5587b-106">The validation rules are enforced any time a user creates or edits a movie.</span></span>

## <a name="validation"></a><span data-ttu-id="5587b-107">Validation</span><span class="sxs-lookup"><span data-stu-id="5587b-107">Validation</span></span>

<span data-ttu-id="5587b-108">[DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) (« **D** on't **R** epeat **Y** ourself », Ne vous répétez pas) constitue un principe clé du développement de logiciel.</span><span class="sxs-lookup"><span data-stu-id="5587b-108">A key tenet of software development is called [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) (" **D** on't **R** epeat **Y** ourself").</span></span> <span data-ttu-id="5587b-109">:::no-loc(Razor)::: Les pages encouragent le développement dans lequel la fonctionnalité est spécifiée une seule fois, et elle est reflétée dans l’application.</span><span class="sxs-lookup"><span data-stu-id="5587b-109">:::no-loc(Razor)::: Pages encourages development where functionality is specified once, and it's reflected throughout the app.</span></span> <span data-ttu-id="5587b-110">DRY peut aider à :</span><span class="sxs-lookup"><span data-stu-id="5587b-110">DRY can help:</span></span>

* <span data-ttu-id="5587b-111">Réduire la quantité de code dans une application.</span><span class="sxs-lookup"><span data-stu-id="5587b-111">Reduce the amount of code in an app.</span></span>
* <span data-ttu-id="5587b-112">Rendre le code moins sujet aux erreurs, et plus facile à tester et à maintenir.</span><span class="sxs-lookup"><span data-stu-id="5587b-112">Make the code less error prone, and easier to test and maintain.</span></span>

<span data-ttu-id="5587b-113">La prise en charge de la validation fournie par les :::no-loc(Razor)::: pages et les Entity Framework est un bon exemple du principe Dry.</span><span class="sxs-lookup"><span data-stu-id="5587b-113">The validation support provided by :::no-loc(Razor)::: Pages and Entity Framework is a good example of the DRY principle.</span></span> <span data-ttu-id="5587b-114">Des règles de validation sont spécifiées de façon déclarative à un seul emplacement (dans la classe de modèle), et sont appliquées partout dans l’application.</span><span class="sxs-lookup"><span data-stu-id="5587b-114">Validation rules are declaratively specified in one place (in the model class), and the rules are enforced everywhere in the app.</span></span>

## <a name="add-validation-rules-to-the-movie-model"></a><span data-ttu-id="5587b-115">Ajouter des règles de validation au modèle de film</span><span class="sxs-lookup"><span data-stu-id="5587b-115">Add validation rules to the movie model</span></span>

<span data-ttu-id="5587b-116">L’espace de noms DataAnnotations fournit un ensemble d’attributs de validation intégrés qui sont appliqués de façon déclarative à une classe ou à une propriété.</span><span class="sxs-lookup"><span data-stu-id="5587b-116">The DataAnnotations namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="5587b-117">DataAnnotations contient également des attributs de mise en forme, comme `DataType`, qui aident à effectuer la mise en forme et ne fournissent aucune validation.</span><span class="sxs-lookup"><span data-stu-id="5587b-117">DataAnnotations also contains formatting attributes like `DataType` that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="5587b-118">Mettez à jour la classe `Movie` pour tirer parti des attributs de validation intégrés `Required`, `StringLength`, `RegularExpression` et `Range`.</span><span class="sxs-lookup"><span data-stu-id="5587b-118">Update the `Movie` class to take advantage of the built-in `Required`, `StringLength`, `RegularExpression`, and `Range` validation attributes.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="5587b-119">Les attributs de validation spécifient le comportement que vous souhaitez appliquer sur les propriétés du modèle sur lesquels ils sont appliqués :</span><span class="sxs-lookup"><span data-stu-id="5587b-119">The validation attributes specify behavior that you want to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="5587b-120">Les attributs `Required` et `MinimumLength` indiquent qu’une propriété doit avoir une valeur, mais rien n’empêche un utilisateur d’entrer un espace pour satisfaire à cette validation.</span><span class="sxs-lookup"><span data-stu-id="5587b-120">The `Required` and `MinimumLength` attributes indicate that a property must have a value; but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="5587b-121">L’attribut `RegularExpression` sert à limiter les caractères pouvant être entrés.</span><span class="sxs-lookup"><span data-stu-id="5587b-121">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="5587b-122">Dans le code précédent, « Genre » :</span><span class="sxs-lookup"><span data-stu-id="5587b-122">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="5587b-123">Doit utiliser seulement des lettres.</span><span class="sxs-lookup"><span data-stu-id="5587b-123">Must only use letters.</span></span>
  * <span data-ttu-id="5587b-124">La première lettre doit être une majuscule.</span><span class="sxs-lookup"><span data-stu-id="5587b-124">The first letter is required to be uppercase.</span></span> <span data-ttu-id="5587b-125">Les espaces, les chiffres et les caractères spéciaux ne sont pas autorisés.</span><span class="sxs-lookup"><span data-stu-id="5587b-125">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="5587b-126">L’expression `RegularExpression` « Rating » :</span><span class="sxs-lookup"><span data-stu-id="5587b-126">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="5587b-127">Nécessite que le premier caractère soit une lettre majuscule.</span><span class="sxs-lookup"><span data-stu-id="5587b-127">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="5587b-128">Autorise les caractères spéciaux et les chiffres aux emplacements qui suivent.</span><span class="sxs-lookup"><span data-stu-id="5587b-128">Allows special characters and numbers in  subsequent spaces.</span></span> <span data-ttu-id="5587b-129">« PG-13 » est valide pour une évaluation, mais échoue pour un « Genre ».</span><span class="sxs-lookup"><span data-stu-id="5587b-129">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="5587b-130">L’attribut `Range` limite une valeur à une plage spécifiée.</span><span class="sxs-lookup"><span data-stu-id="5587b-130">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="5587b-131">L’attribut `StringLength` vous permet de définir la longueur maximale d’une propriété de chaîne, et éventuellement sa longueur minimale.</span><span class="sxs-lookup"><span data-stu-id="5587b-131">The `StringLength` attribute lets you set the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="5587b-132">Les types valeur (tels que `decimal`, `int`, `float` et `DateTime`) sont obligatoires par nature et n’ont pas besoin de l’attribut `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="5587b-132">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="5587b-133">L’application automatique des règles de validation par ASP.NET Core permet d’accroître la fiabilité de votre application.</span><span class="sxs-lookup"><span data-stu-id="5587b-133">Having validation rules automatically enforced by ASP.NET Core helps make your app more robust.</span></span> <span data-ttu-id="5587b-134">Cela garantit également que vous n’oublierez pas de valider un élément et que vous n’autoriserez pas par inadvertance l’insertion de données incorrectes dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="5587b-134">It also ensures that you can't forget to validate something and inadvertently let bad data into the database.</span></span>

### <a name="validation-error-ui-in-no-locrazor-pages"></a><span data-ttu-id="5587b-135">Interface utilisateur d’erreur de validation dans les :::no-loc(Razor)::: pages</span><span class="sxs-lookup"><span data-stu-id="5587b-135">Validation Error UI in :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="5587b-136">Exécutez l’application, puis accédez à Pages/Movies.</span><span class="sxs-lookup"><span data-stu-id="5587b-136">Run the app and navigate to Pages/Movies.</span></span>

<span data-ttu-id="5587b-137">Sélectionnez le lien **Créer nouveau** .</span><span class="sxs-lookup"><span data-stu-id="5587b-137">Select the **Create New** link.</span></span> <span data-ttu-id="5587b-138">Complétez le formulaire avec des valeurs non valides.</span><span class="sxs-lookup"><span data-stu-id="5587b-138">Complete the form with some invalid values.</span></span> <span data-ttu-id="5587b-139">Quand la validation jQuery côté client détecte l’erreur, elle affiche un message d’erreur.</span><span class="sxs-lookup"><span data-stu-id="5587b-139">When jQuery client-side validation detects the error, it displays an error message.</span></span>

![Formulaire de vue Movie avec plusieurs erreurs de validation jQuery côté client](validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

<span data-ttu-id="5587b-141">Notez que le formulaire a affiché automatiquement un message d’erreur de validation dans chaque champ contenant une valeur non valide.</span><span class="sxs-lookup"><span data-stu-id="5587b-141">Notice how the form has automatically rendered a validation error message in each field containing an invalid value.</span></span> <span data-ttu-id="5587b-142">Les erreurs sont appliquées à la fois côté client (à l’aide de JavaScript et de jQuery) et côté serveur (quand JavaScript est désactivé pour un utilisateur).</span><span class="sxs-lookup"><span data-stu-id="5587b-142">The errors are enforced both client-side (using JavaScript and jQuery) and server-side (when a user has JavaScript disabled).</span></span>

<span data-ttu-id="5587b-143">L’un des principaux avantages est qu’ **aucun** changement de code n’a été nécessaire dans les pages Créer ou Modifier.</span><span class="sxs-lookup"><span data-stu-id="5587b-143">A significant benefit is that **no** code changes were necessary in the Create  or Edit pages.</span></span> <span data-ttu-id="5587b-144">Une fois les attributs DataAnnotations appliqués au modèle, l’interface utilisateur de validation a été activée.</span><span class="sxs-lookup"><span data-stu-id="5587b-144">Once DataAnnotations were applied to the model, the validation UI was enabled.</span></span> <span data-ttu-id="5587b-145">Les :::no-loc(Razor)::: pages créées dans ce didacticiel ont automatiquement récupéré les règles de validation (à l’aide des attributs de validation sur les propriétés de la `Movie` classe de modèle).</span><span class="sxs-lookup"><span data-stu-id="5587b-145">The :::no-loc(Razor)::: Pages created in this tutorial automatically picked up the validation rules (using validation attributes on the properties of the `Movie` model class).</span></span> <span data-ttu-id="5587b-146">Testez la validation à l’aide de la page de modification. La même validation est appliquée.</span><span class="sxs-lookup"><span data-stu-id="5587b-146">Test validation using the Edit page, the same validation is applied.</span></span>

<span data-ttu-id="5587b-147">Les données de formulaire ne sont pas publiées sur le serveur tant qu’il y a des erreurs de validation côté client.</span><span class="sxs-lookup"><span data-stu-id="5587b-147">The form data isn't posted to the server until there are no client-side validation errors.</span></span> <span data-ttu-id="5587b-148">Vérifiez que les données du formulaire ne sont pas publiées à l’aide d’une ou de plusieurs des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="5587b-148">Verify form data isn't posted by one or more of the following approaches:</span></span>

* <span data-ttu-id="5587b-149">Placez un point d’arrêt dans la méthode `OnPostAsync`.</span><span class="sxs-lookup"><span data-stu-id="5587b-149">Put a break point in the `OnPostAsync` method.</span></span> <span data-ttu-id="5587b-150">Envoyer le formulaire (en sélectionnant **Créer** ou **Enregistrer** ).</span><span class="sxs-lookup"><span data-stu-id="5587b-150">Submit the form (select **Create** or **Save** ).</span></span> <span data-ttu-id="5587b-151">Le point d’arrêt n’est jamais atteint.</span><span class="sxs-lookup"><span data-stu-id="5587b-151">The break point is never hit.</span></span>
* <span data-ttu-id="5587b-152">Utilisez l’[outil Fiddler](https://www.telerik.com/fiddler).</span><span class="sxs-lookup"><span data-stu-id="5587b-152">Use the [Fiddler tool](https://www.telerik.com/fiddler).</span></span>
* <span data-ttu-id="5587b-153">Utilisez les outils de développement du navigateur pour surveiller le trafic réseau.</span><span class="sxs-lookup"><span data-stu-id="5587b-153">Use the browser developer tools to monitor network traffic.</span></span>

### <a name="server-side-validation"></a><span data-ttu-id="5587b-154">Validation côté serveur</span><span class="sxs-lookup"><span data-stu-id="5587b-154">Server-side validation</span></span>

<span data-ttu-id="5587b-155">Quand JavaScript est désactivé dans le navigateur, l’envoi du formulaire avec des erreurs poste les données sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="5587b-155">When JavaScript is disabled in the browser, submitting the form with errors will post to the server.</span></span>

<span data-ttu-id="5587b-156">Facultatif : Testez la validation côté serveur :</span><span class="sxs-lookup"><span data-stu-id="5587b-156">Optional, test server-side validation:</span></span>

* <span data-ttu-id="5587b-157">Désactivez JavaScript dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="5587b-157">Disable JavaScript in the browser.</span></span> <span data-ttu-id="5587b-158">Vous pouvez désactiver JavaScript à l’aide d’outils de développement du navigateur.</span><span class="sxs-lookup"><span data-stu-id="5587b-158">You can disable JavaScript using browser's developer tools.</span></span> <span data-ttu-id="5587b-159">Si vous ne pouvez pas désactiver JavaScript dans le navigateur, essayez un autre navigateur.</span><span class="sxs-lookup"><span data-stu-id="5587b-159">If you can't disable JavaScript in the browser, try another browser.</span></span>
* <span data-ttu-id="5587b-160">Définissez un point d’arrêt dans la méthode `OnPostAsync` de la page Créer ou Modifier.</span><span class="sxs-lookup"><span data-stu-id="5587b-160">Set a break point in the `OnPostAsync` method of the Create or Edit page.</span></span>
* <span data-ttu-id="5587b-161">Envoyez un formulaire avec des données non valides.</span><span class="sxs-lookup"><span data-stu-id="5587b-161">Submit a form with invalid data.</span></span>
* <span data-ttu-id="5587b-162">Vérifiez que l’état de modèle n’est pas valide :</span><span class="sxs-lookup"><span data-stu-id="5587b-162">Verify the model state is invalid:</span></span>

  ```csharp
   if (!ModelState.IsValid)
   {
      return Page();
   }
  ```
  
<span data-ttu-id="5587b-163">Vous pouvez également [désactiver la validation côté client sur le serveur](xref:mvc/models/validation#disable-client-side-validation).</span><span class="sxs-lookup"><span data-stu-id="5587b-163">Alternatively, you can [Disable client-side validation on the server](xref:mvc/models/validation#disable-client-side-validation).</span></span>

<span data-ttu-id="5587b-164">Le code suivant affiche la partie de la page *Create.cshtml* générée automatiquement plus tôt dans le tutoriel.</span><span class="sxs-lookup"><span data-stu-id="5587b-164">The following code shows a portion of the *Create.cshtml* page scaffolded earlier in the tutorial.</span></span> <span data-ttu-id="5587b-165">Elle est utilisée par les pages Créer et Modifier pour afficher le formulaire initial et le réafficher en cas d’erreur.</span><span class="sxs-lookup"><span data-stu-id="5587b-165">It's used by the Create and Edit pages to display the initial form and to redisplay the form in the event of an error.</span></span>

[!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

<span data-ttu-id="5587b-166">Le [Tag Helper d’entrée](xref:mvc/views/working-with-forms) utilise les attributs [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) et produit les attributs HTML nécessaires à la validation jQuery côté client.</span><span class="sxs-lookup"><span data-stu-id="5587b-166">The [Input Tag Helper](xref:mvc/views/working-with-forms) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span> <span data-ttu-id="5587b-167">Le [Tag Helper Validation](xref:mvc/views/working-with-forms#the-validation-tag-helpers) affiche les erreurs de validation.</span><span class="sxs-lookup"><span data-stu-id="5587b-167">The [Validation Tag Helper](xref:mvc/views/working-with-forms#the-validation-tag-helpers) displays validation errors.</span></span> <span data-ttu-id="5587b-168">Pour plus d’informations, consultez [Validation](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="5587b-168">See [Validation](xref:mvc/models/validation) for more information.</span></span>

<span data-ttu-id="5587b-169">Les pages Créer et Modifier ne contiennent pas de règles de validation.</span><span class="sxs-lookup"><span data-stu-id="5587b-169">The Create and Edit pages have no validation rules in them.</span></span> <span data-ttu-id="5587b-170">Les règles de validation et les chaînes d’erreur sont spécifiées uniquement dans la classe `Movie`.</span><span class="sxs-lookup"><span data-stu-id="5587b-170">The validation rules and the error strings are specified only in the `Movie` class.</span></span> <span data-ttu-id="5587b-171">Ces règles de validation sont appliquées automatiquement aux :::no-loc(Razor)::: pages qui modifient le `Movie` modèle.</span><span class="sxs-lookup"><span data-stu-id="5587b-171">These validation rules are automatically applied to :::no-loc(Razor)::: Pages that edit the `Movie` model.</span></span>

<span data-ttu-id="5587b-172">Quand la logique de validation doit être modifiée, cela s’effectue uniquement dans le modèle.</span><span class="sxs-lookup"><span data-stu-id="5587b-172">When validation logic needs to change, it's done only in the model.</span></span> <span data-ttu-id="5587b-173">La validation est appliquée de manière cohérente dans l’ensemble de l’application (la logique de validation est définie dans un emplacement unique).</span><span class="sxs-lookup"><span data-stu-id="5587b-173">Validation is applied consistently throughout the application (validation logic is defined in one place).</span></span> <span data-ttu-id="5587b-174">La validation dans un emplacement unique permet de maintenir votre code clair, et d’en faciliter la maintenance et la mise à jour.</span><span class="sxs-lookup"><span data-stu-id="5587b-174">Validation in one place helps keep the code clean, and makes it easier to maintain and update.</span></span>

## <a name="using-datatype-attributes"></a><span data-ttu-id="5587b-175">Utilisation d’attributs DataType</span><span class="sxs-lookup"><span data-stu-id="5587b-175">Using DataType Attributes</span></span>

<span data-ttu-id="5587b-176">Examiner la classe `Movie`.</span><span class="sxs-lookup"><span data-stu-id="5587b-176">Examine the `Movie` class.</span></span> <span data-ttu-id="5587b-177">L’espace de noms `System.ComponentModel.DataAnnotations` fournit des attributs de mise en forme en plus de l’ensemble intégré d’attributs de validation.</span><span class="sxs-lookup"><span data-stu-id="5587b-177">The `System.ComponentModel.DataAnnotations` namespace provides formatting attributes in addition to the built-in set of validation attributes.</span></span> <span data-ttu-id="5587b-178">L’attribut `DataType` est appliqué aux propriétés `ReleaseDate` et `Price`.</span><span class="sxs-lookup"><span data-stu-id="5587b-178">The `DataType` attribute is applied to the `ReleaseDate` and `Price` properties.</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

<span data-ttu-id="5587b-179">Les attributs `DataType` fournissent uniquement des conseils permettant au moteur de vue de mettre en forme les données (et fournissent des attributs tels que `<a>` pour les URL et `<a href="mailto:EmailAddress.com">` pour l’e-mail).</span><span class="sxs-lookup"><span data-stu-id="5587b-179">The `DataType` attributes only provide hints for the view engine to format the data (and supplies attributes such as `<a>` for URL's and `<a href="mailto:EmailAddress.com">` for email).</span></span> <span data-ttu-id="5587b-180">Utilisez l’attribut `RegularExpression` pour valider le format des données.</span><span class="sxs-lookup"><span data-stu-id="5587b-180">Use the `RegularExpression` attribute to validate the format of the data.</span></span> <span data-ttu-id="5587b-181">L’attribut `DataType` sert à spécifier un type de données qui est plus spécifique que le type intrinsèque de la base de données.</span><span class="sxs-lookup"><span data-stu-id="5587b-181">The `DataType` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="5587b-182">Les attributs `DataType` ne sont pas des attributs de validation.</span><span class="sxs-lookup"><span data-stu-id="5587b-182">`DataType` attributes are not validation attributes.</span></span> <span data-ttu-id="5587b-183">Dans l’exemple d’application, seule la date est affichée, sans l’heure.</span><span class="sxs-lookup"><span data-stu-id="5587b-183">In the sample application, only the date is displayed, without time.</span></span>

<span data-ttu-id="5587b-184">L’énumération `DataType` fournit de nombreux types de données, tels que Date, Time, PhoneNumber, Currency ou EmailAddress.</span><span class="sxs-lookup"><span data-stu-id="5587b-184">The `DataType` Enumeration provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, and more.</span></span> <span data-ttu-id="5587b-185">L’attribut `DataType` peut également permettre à l’application de fournir automatiquement des fonctionnalités propres au type.</span><span class="sxs-lookup"><span data-stu-id="5587b-185">The `DataType` attribute can also enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="5587b-186">Par exemple, il est possible de créer un lien `mailto:` pour `DataType.EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="5587b-186">For example, a `mailto:` link can be created for `DataType.EmailAddress`.</span></span> <span data-ttu-id="5587b-187">Un sélecteur de date peut être fourni pour `DataType.Date` dans les navigateurs qui prennent en charge HTML5.</span><span class="sxs-lookup"><span data-stu-id="5587b-187">A date selector can be provided for `DataType.Date` in browsers that support HTML5.</span></span> <span data-ttu-id="5587b-188">Les `DataType` attributs émettent des attributs HTML 5 `data-` (prononciation des données) que les navigateurs HTML 5 consomment.</span><span class="sxs-lookup"><span data-stu-id="5587b-188">The `DataType` attributes emit HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="5587b-189">Les attributs `DataType` ne fournissent **aucune** validation.</span><span class="sxs-lookup"><span data-stu-id="5587b-189">The `DataType` attributes do **not** provide any validation.</span></span>

<span data-ttu-id="5587b-190">`DataType.Date` ne spécifie pas le format de la date qui s’affiche.</span><span class="sxs-lookup"><span data-stu-id="5587b-190">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="5587b-191">Par défaut, le champ de données est affiché conformément aux formats par défaut basés sur le `CultureInfo` du serveur.</span><span class="sxs-lookup"><span data-stu-id="5587b-191">By default, the data field is displayed according to the default formats based on the server's `CultureInfo`.</span></span>

<span data-ttu-id="5587b-192">L’annotation de données `[Column(TypeName = "decimal(18, 2)")]` est nécessaire pour qu’Entity Framework Core puisse correctement mapper `Price` en devise dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="5587b-192">The `[Column(TypeName = "decimal(18, 2)")]` data annotation is required so Entity Framework Core can correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="5587b-193">Pour plus d’informations, consultez [Types de données](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="5587b-193">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="5587b-194">L’attribut `DisplayFormat` est utilisé pour spécifier explicitement le format de date :</span><span class="sxs-lookup"><span data-stu-id="5587b-194">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

<span data-ttu-id="5587b-195">Le paramètre `ApplyFormatInEditMode` indique que la mise en forme doit être appliquée quand la valeur est affichée à des fins de modification.</span><span class="sxs-lookup"><span data-stu-id="5587b-195">The `ApplyFormatInEditMode` setting specifies that the formatting should be applied when the value is displayed for editing.</span></span> <span data-ttu-id="5587b-196">Vous pouvez souhaiter ce comportement pour certains champs.</span><span class="sxs-lookup"><span data-stu-id="5587b-196">You might not want that behavior for some fields.</span></span> <span data-ttu-id="5587b-197">Par exemple, dans les valeurs monétaires, vous ne voulez probablement pas le symbole monétaire dans l’interface utilisateur de modification.</span><span class="sxs-lookup"><span data-stu-id="5587b-197">For example, in currency values, you probably don't want the currency symbol in the edit UI.</span></span>

<span data-ttu-id="5587b-198">L’attribut `DisplayFormat` peut être utilisé seul, mais il est généralement préférable d’utiliser l’attribut `DataType`.</span><span class="sxs-lookup"><span data-stu-id="5587b-198">The `DisplayFormat` attribute can be used by itself, but it's generally a good idea to use the `DataType` attribute.</span></span> <span data-ttu-id="5587b-199">L’attribut `DataType` donne la sémantique des données, plutôt que de décrire comment effectuer le rendu sur un écran, et il offre les avantages suivants dont vous ne bénéficiez pas avec DisplayFormat :</span><span class="sxs-lookup"><span data-stu-id="5587b-199">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen, and provides the following benefits that you don't get with DisplayFormat:</span></span>

* <span data-ttu-id="5587b-200">Le navigateur peut activer des fonctionnalités HTML5 (par exemple pour afficher un contrôle de calendrier, le symbole monétaire correspondant aux paramètres régionaux, des liens de messagerie, etc.).</span><span class="sxs-lookup"><span data-stu-id="5587b-200">The browser can enable HTML5 features (for example to show a calendar control, the locale-appropriate currency symbol, email links, etc.)</span></span>
* <span data-ttu-id="5587b-201">Par défaut, le navigateur affiche les données à l’aide du format correspondant à vos paramètres régionaux.</span><span class="sxs-lookup"><span data-stu-id="5587b-201">By default, the browser will render data using the correct format based on your locale.</span></span>
* <span data-ttu-id="5587b-202">L’attribut `DataType` peut permettre à l’infrastructure ASP.NET Core de choisir le modèle de champ approprié pour afficher les données.</span><span class="sxs-lookup"><span data-stu-id="5587b-202">The `DataType` attribute can enable the ASP.NET Core framework to choose the right field template to render the data.</span></span> <span data-ttu-id="5587b-203">`DisplayFormat`, S’il est utilisé par lui-même, utilise le modèle de chaîne.</span><span class="sxs-lookup"><span data-stu-id="5587b-203">The `DisplayFormat`, if used by itself, uses the string template.</span></span>

<span data-ttu-id="5587b-204">**Remarque :** la validation jQuery ne fonctionne pas avec l' `Range` attribut et `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="5587b-204">**Note:** jQuery validation doesn't work with the `Range` attribute and `DateTime`.</span></span> <span data-ttu-id="5587b-205">Par exemple, le code suivant affiche toujours une erreur de validation côté client, même quand la date se trouve dans la plage spécifiée :</span><span class="sxs-lookup"><span data-stu-id="5587b-205">For example, the following code will always display a client-side validation error, even when the date is in the specified range:</span></span>

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

<span data-ttu-id="5587b-206">Il n’est généralement pas recommandé de compiler des dates dures dans vos modèles. Par conséquent, l’utilisation de l’attribut `Range` et de `DateTime` est déconseillée.</span><span class="sxs-lookup"><span data-stu-id="5587b-206">It's generally not a good practice to compile hard dates in your models, so using the `Range` attribute and `DateTime` is discouraged.</span></span>

<span data-ttu-id="5587b-207">Le code suivant illustre la combinaison d’attributs sur une seule ligne :</span><span class="sxs-lookup"><span data-stu-id="5587b-207">The following code shows combining attributes on one line:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Models/MovieDateRatingDAmult.cs?name=snippet1)]

<span data-ttu-id="5587b-208">[Prise en main de :::no-loc(Razor)::: Pages et EF Core](xref:data/ef-rp/intro) affiche des opérations de EF Core avancées avec des :::no-loc(Razor)::: pages.</span><span class="sxs-lookup"><span data-stu-id="5587b-208">[Get started with :::no-loc(Razor)::: Pages and EF Core](xref:data/ef-rp/intro) shows advanced EF Core operations with :::no-loc(Razor)::: Pages.</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="5587b-209">Appliquer des migrations</span><span class="sxs-lookup"><span data-stu-id="5587b-209">Apply migrations</span></span>

<span data-ttu-id="5587b-210">Le DataAnnotations appliqué à la classe modifie le schéma.</span><span class="sxs-lookup"><span data-stu-id="5587b-210">The DataAnnotations applied to the class changes the schema.</span></span> <span data-ttu-id="5587b-211">Par exemple, DataAnnotations appliqué au champ `Title` :</span><span class="sxs-lookup"><span data-stu-id="5587b-211">For example, the DataAnnotations applied to the `Title` field:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Models/MovieDateRatingDA.cs?name=snippet11)]

* <span data-ttu-id="5587b-212">limite les caractères à 60 ;</span><span class="sxs-lookup"><span data-stu-id="5587b-212">Limits the characters to 60.</span></span>
* <span data-ttu-id="5587b-213">n’autorise pas de valeur `null`.</span><span class="sxs-lookup"><span data-stu-id="5587b-213">Doesn't allow a `null` value.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5587b-214">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5587b-214">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5587b-215">La table `Movie` a actuellement le schéma suivant :</span><span class="sxs-lookup"><span data-stu-id="5587b-215">The `Movie` table currently has the following schema:</span></span>

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

<span data-ttu-id="5587b-216">Les modifications précédentes du schéma n’entraînent pas la levée d’une exception par EF.</span><span class="sxs-lookup"><span data-stu-id="5587b-216">The preceding schema changes don't cause EF to throw an exception.</span></span> <span data-ttu-id="5587b-217">Cependant, créez une migration pour que le schéma soit cohérent avec le modèle.</span><span class="sxs-lookup"><span data-stu-id="5587b-217">However, create a migration so the schema is consistent with the model.</span></span>

<span data-ttu-id="5587b-218">Dans le menu **Outils** , sélectionnez **Gestionnaire de package NuGet > Console du gestionnaire de package** .</span><span class="sxs-lookup"><span data-stu-id="5587b-218">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console** .</span></span>
<span data-ttu-id="5587b-219">Dans la console du gestionnaire de package, entrez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="5587b-219">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration New_DataAnnotations
Update-Database
```

<span data-ttu-id="5587b-220">`Update-Database` exécute les méthodes `Up` de la classe `New_DataAnnotations`.</span><span class="sxs-lookup"><span data-stu-id="5587b-220">`Update-Database` runs the `Up` methods of the `New_DataAnnotations` class.</span></span> <span data-ttu-id="5587b-221">Examinez la méthode `Up` :</span><span class="sxs-lookup"><span data-stu-id="5587b-221">Examine the `Up` method:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Migrations/20190724163003_New_DataAnnotations.cs?name=snippet)]

<span data-ttu-id="5587b-222">La table `Movie` mise à jour a le schéma suivant :</span><span class="sxs-lookup"><span data-stu-id="5587b-222">The updated `Movie` table has the following schema:</span></span>

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

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="5587b-223">Visual Studio Code / Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="5587b-223">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="5587b-224">Des migrations ne sont pas requises pour SQLite.</span><span class="sxs-lookup"><span data-stu-id="5587b-224">Migrations are not required for SQLite.</span></span>

---

### <a name="publish-to-azure"></a><span data-ttu-id="5587b-225">Publication dans Azure</span><span class="sxs-lookup"><span data-stu-id="5587b-225">Publish to Azure</span></span>

<span data-ttu-id="5587b-226">Pour plus d’informations sur le déploiement sur Azure, consultez [Didacticiel : créer une application ASP.net core dans Azure avec SQL Database](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).</span><span class="sxs-lookup"><span data-stu-id="5587b-226">For information on deploying to Azure, see [Tutorial: Build an ASP.NET Core app in Azure with SQL Database](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).</span></span>

<span data-ttu-id="5587b-227">Merci d’avoir effectué cette introduction aux :::no-loc(Razor)::: pages.</span><span class="sxs-lookup"><span data-stu-id="5587b-227">Thanks for completing this introduction to :::no-loc(Razor)::: Pages.</span></span> <span data-ttu-id="5587b-228">[Prise en main de :::no-loc(Razor)::: Pages et EF Core](xref:data/ef-rp/intro) est un excellent suivi de ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="5587b-228">[Get started with :::no-loc(Razor)::: Pages and EF Core](xref:data/ef-rp/intro) is an excellent follow up to this tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5587b-229">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="5587b-229">Additional resources</span></span>

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>
* [<span data-ttu-id="5587b-230">Version YouTube de ce tutoriel</span><span class="sxs-lookup"><span data-stu-id="5587b-230">YouTube version of this tutorial</span></span>](https://youtu.be/b63m66eu7us)

> [!div class="step-by-step"]
> [<span data-ttu-id="5587b-231">Précédent : Ajout d’un nouveau champ</span><span class="sxs-lookup"><span data-stu-id="5587b-231">Previous: Adding a new field</span></span>](xref:tutorials/razor-pages/new-field)
