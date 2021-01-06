---
title: Partie 5, Razor pages avec EF Core dans le modèle de données ASP.net Core
author: rick-anderson
description: Partie 5 de Razor pages et Entity Framework série de didacticiels.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
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
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: 1ac9d6303daac82f3973c5d027fe1f453dc32e02
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054097"
---
# <a name="part-5-no-locrazor-pages-with-ef-core-in-aspnet-core---data-model"></a><span data-ttu-id="186ab-103">Partie 5, Razor pages avec EF Core dans le modèle de données ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="186ab-103">Part 5, Razor Pages with EF Core in ASP.NET Core - Data Model</span></span>

<span data-ttu-id="186ab-104">Par [Tom Dykstra](https://github.com/tdykstra) et [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="186ab-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="186ab-105">Dans les didacticiels précédents, nous avons travaillé avec un modèle de données de base composé de trois entités.</span><span class="sxs-lookup"><span data-stu-id="186ab-105">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="186ab-106">Dans ce tutoriel, vous allez :</span><span class="sxs-lookup"><span data-stu-id="186ab-106">In this tutorial:</span></span>

* <span data-ttu-id="186ab-107">Nous allons ajouter d’autres entités et relations</span><span class="sxs-lookup"><span data-stu-id="186ab-107">More entities and relationships are added.</span></span>
* <span data-ttu-id="186ab-108">Nous allons personnaliser le modèle de données en spécifiant des règles de mise en forme, de validation et de mappage de base de données.</span><span class="sxs-lookup"><span data-stu-id="186ab-108">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="186ab-109">Le modèle de données final est présenté dans l’illustration suivante :</span><span class="sxs-lookup"><span data-stu-id="186ab-109">The completed data model is shown in the following illustration:</span></span>

![Diagramme des entités](complex-data-model/_static/diagram.png)

## <a name="the-student-entity"></a><span data-ttu-id="186ab-111">L’entité Student</span><span class="sxs-lookup"><span data-stu-id="186ab-111">The Student entity</span></span>

![Entité Student](complex-data-model/_static/student-entity.png)

<span data-ttu-id="186ab-113">Remplacez le code de *MainWindow.xaml.cs* par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="186ab-113">Replace the code in *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Student.cs)]

<span data-ttu-id="186ab-114">Le code précédent ajoute une propriété `FullName` et les attributs suivants aux propriétés existantes :</span><span class="sxs-lookup"><span data-stu-id="186ab-114">The preceding code adds a `FullName` property and adds the following attributes to existing properties:</span></span>

* `[DataType]`
* `[DisplayFormat]`
* `[StringLength]`
* `[Column]`
* `[Required]`
* `[Display]`

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="186ab-115">Propriété calculée FullName</span><span class="sxs-lookup"><span data-stu-id="186ab-115">The FullName calculated property</span></span>

<span data-ttu-id="186ab-116">`FullName` est une propriété calculée qui retourne une valeur créée par concaténation de deux autres propriétés.</span><span class="sxs-lookup"><span data-stu-id="186ab-116">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="186ab-117">`FullName` ne pouvant pas être définie, elle n’a qu’un seul accesseur get.</span><span class="sxs-lookup"><span data-stu-id="186ab-117">`FullName` can't be set, so it has only a get accessor.</span></span> <span data-ttu-id="186ab-118">Aucune colonne `FullName` n’est créée dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="186ab-118">No `FullName` column is created in the database.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="186ab-119">Attribut DataType</span><span class="sxs-lookup"><span data-stu-id="186ab-119">The DataType attribute</span></span>

```csharp
[DataType(DataType.Date)]
```

<span data-ttu-id="186ab-120">Pour les dates d’inscription des étudiants, toutes les pages affichent actuellement l’heure du jour avec la date, alors que seule la date présente un intérêt.</span><span class="sxs-lookup"><span data-stu-id="186ab-120">For student enrollment dates, all of the pages currently display the time of day along with the date, although only the date is relevant.</span></span> <span data-ttu-id="186ab-121">Vous pouvez avoir recours aux attributs d’annotation de données pour apporter une modification au code, permettant de corriger le format d’affichage dans chaque page qui affiche ces données.</span><span class="sxs-lookup"><span data-stu-id="186ab-121">By using data annotation attributes, you can make one code change that will fix the display format in every page that shows the data.</span></span> 

<span data-ttu-id="186ab-122">L’attribut [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute) spécifie un type de données qui est plus spécifique que le type intrinsèque de la base de données.</span><span class="sxs-lookup"><span data-stu-id="186ab-122">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="186ab-123">Ici, seule la date doit être affichée (pas la date et l’heure).</span><span class="sxs-lookup"><span data-stu-id="186ab-123">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="186ab-124">L' [énumération DataType](/dotnet/api/system.componentmodel.dataannotations.datatype) fournit de nombreux types de données, tels que date, Time, PhoneNumber, Currency, EmailAddress, etc. L' `DataType` attribut peut également permettre à l’application de fournir automatiquement des fonctionnalités propres au type.</span><span class="sxs-lookup"><span data-stu-id="186ab-124">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="186ab-125">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="186ab-125">For example:</span></span>

* <span data-ttu-id="186ab-126">Le lien `mailto:` est créé automatiquement pour `DataType.EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="186ab-126">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="186ab-127">Le sélecteur de date est fourni pour `DataType.Date` dans la plupart des navigateurs.</span><span class="sxs-lookup"><span data-stu-id="186ab-127">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="186ab-128">L’attribut `DataType` émet des attributs HTML 5 `data-` (prononcé data dash en anglais).</span><span class="sxs-lookup"><span data-stu-id="186ab-128">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes.</span></span> <span data-ttu-id="186ab-129">Les attributs `DataType` ne fournissent aucune validation.</span><span class="sxs-lookup"><span data-stu-id="186ab-129">The `DataType` attributes don't provide validation.</span></span>

### <a name="the-displayformat-attribute"></a><span data-ttu-id="186ab-130">Attribut DisplayFormat</span><span class="sxs-lookup"><span data-stu-id="186ab-130">The DisplayFormat attribute</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="186ab-131">`DataType.Date` ne spécifie pas le format de la date qui s’affiche.</span><span class="sxs-lookup"><span data-stu-id="186ab-131">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="186ab-132">Par défaut, le champ de date est affiché conformément aux formats par défaut basés sur l’objet [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support) du serveur.</span><span class="sxs-lookup"><span data-stu-id="186ab-132">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="186ab-133">L’attribut `DisplayFormat` sert à spécifier explicitement le format de date.</span><span class="sxs-lookup"><span data-stu-id="186ab-133">The `DisplayFormat` attribute is used to explicitly specify the date format.</span></span> <span data-ttu-id="186ab-134">Le paramètre `ApplyFormatInEditMode` spécifie que la mise en forme doit également être appliquée à l’interface utilisateur de modification.</span><span class="sxs-lookup"><span data-stu-id="186ab-134">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="186ab-135">Certains champs ne doivent pas utiliser `ApplyFormatInEditMode`.</span><span class="sxs-lookup"><span data-stu-id="186ab-135">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="186ab-136">Par exemple, le symbole monétaire ne doit généralement pas être affiché dans une zone de texte d’édition.</span><span class="sxs-lookup"><span data-stu-id="186ab-136">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="186ab-137">L’attribut `DisplayFormat` peut être utilisé seul.</span><span class="sxs-lookup"><span data-stu-id="186ab-137">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="186ab-138">Il est généralement préférable d’utiliser l’attribut `DataType` avec l’attribut `DisplayFormat`.</span><span class="sxs-lookup"><span data-stu-id="186ab-138">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="186ab-139">L’attribut `DataType` transmet la sémantique des données, plutôt que la manière de l’afficher à l’écran.</span><span class="sxs-lookup"><span data-stu-id="186ab-139">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="186ab-140">L’attribut `DataType` offre les avantages suivants qui ne sont pas disponibles dans `DisplayFormat` :</span><span class="sxs-lookup"><span data-stu-id="186ab-140">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="186ab-141">Le navigateur peut activer des fonctionnalités HTML5</span><span class="sxs-lookup"><span data-stu-id="186ab-141">The browser can enable HTML5 features.</span></span> <span data-ttu-id="186ab-142">(par exemple, pour afficher un contrôle de calendrier, le symbole monétaire correspondant aux paramètres régionaux, des liens de messagerie, une validation d’entrées côté client).</span><span class="sxs-lookup"><span data-stu-id="186ab-142">For example, show a calendar control, the locale-appropriate currency symbol, email links, and client-side input validation.</span></span>
* <span data-ttu-id="186ab-143">Par défaut, le navigateur affiche les données à l’aide du format correspondant aux paramètres régionaux.</span><span class="sxs-lookup"><span data-stu-id="186ab-143">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="186ab-144">Pour plus d’informations, consultez la [ \<input> documentation tag Helper](xref:mvc/views/working-with-forms#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="186ab-144">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

### <a name="the-stringlength-attribute"></a><span data-ttu-id="186ab-145">Attribut StringLength</span><span class="sxs-lookup"><span data-stu-id="186ab-145">The StringLength attribute</span></span>

```csharp
[StringLength(50, ErrorMessage = "First name cannot be longer than 50 characters.")]
```

<span data-ttu-id="186ab-146">Vous pouvez également spécifier des règles de validation de données et des messages d’erreur de validation à l’aide d’attributs.</span><span class="sxs-lookup"><span data-stu-id="186ab-146">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="186ab-147">L’attribut [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute) spécifie les longueurs minimale et maximale de caractères autorisées dans un champ de données.</span><span class="sxs-lookup"><span data-stu-id="186ab-147">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="186ab-148">Le code présenté limite la longueur des noms à 50 caractères.</span><span class="sxs-lookup"><span data-stu-id="186ab-148">The code shown limits names to no more than 50 characters.</span></span> <span data-ttu-id="186ab-149">Un exemple qui définit la longueur de chaîne minimale est présenté [plus loin](#the-required-attribute).</span><span class="sxs-lookup"><span data-stu-id="186ab-149">An example that sets the minimum string length is shown [later](#the-required-attribute).</span></span>

<span data-ttu-id="186ab-150">L’attribut `StringLength` fournit également la validation côté client et côté serveur.</span><span class="sxs-lookup"><span data-stu-id="186ab-150">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="186ab-151">La valeur minimale n’a aucun impact sur le schéma de base de données.</span><span class="sxs-lookup"><span data-stu-id="186ab-151">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="186ab-152">L’attribut `StringLength` n’empêche pas un utilisateur d’entrer un espace blanc comme nom.</span><span class="sxs-lookup"><span data-stu-id="186ab-152">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="186ab-153">L’attribut [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) peut être utilisé pour appliquer des restrictions à l’entrée.</span><span class="sxs-lookup"><span data-stu-id="186ab-153">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) attribute can be used to apply restrictions to the input.</span></span> <span data-ttu-id="186ab-154">Par exemple, le code suivant exige que le premier caractère soit en majuscule et que les autres caractères soient alphabétiques :</span><span class="sxs-lookup"><span data-stu-id="186ab-154">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z]*$")]
```

# <a name="visual-studio"></a>[<span data-ttu-id="186ab-155">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="186ab-155">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="186ab-156">Dans **l’Explorateur d’objets SQL Server** (SSOX), ouvrez le concepteur de tables Student en double-cliquant sur la table **Student**.</span><span class="sxs-lookup"><span data-stu-id="186ab-156">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![Table Students dans SSOX avant les migrations](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="186ab-158">L’image précédente montre le schéma pour la table `Student`.</span><span class="sxs-lookup"><span data-stu-id="186ab-158">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="186ab-159">Les champs de nom sont de type `nvarchar(MAX)`.</span><span class="sxs-lookup"><span data-stu-id="186ab-159">The name fields have type `nvarchar(MAX)`.</span></span> <span data-ttu-id="186ab-160">Quand une migration est créée et appliquée plus loin dans ce tutoriel, les champs de nom deviennent `nvarchar(50)` en raison des attributs de longueur de chaîne.</span><span class="sxs-lookup"><span data-stu-id="186ab-160">When a migration is created and applied later in this tutorial, the name fields become `nvarchar(50)` as a result of the string length attributes.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="186ab-161">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="186ab-161">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="186ab-162">Dans votre outil SQLite, examinez les définitions de colonne pour la table `Student`.</span><span class="sxs-lookup"><span data-stu-id="186ab-162">In your SQLite tool, examine the column definitions for the `Student` table.</span></span> <span data-ttu-id="186ab-163">Les champs de nom sont de type `Text`.</span><span class="sxs-lookup"><span data-stu-id="186ab-163">The name fields have type `Text`.</span></span> <span data-ttu-id="186ab-164">Notez que le champ de prénom (« first name ») est appelé `FirstMidName`.</span><span class="sxs-lookup"><span data-stu-id="186ab-164">Notice that the first name field is called `FirstMidName`.</span></span> <span data-ttu-id="186ab-165">Dans la section suivante, vous allez remplacer le nom de cette colonne par `FirstName`.</span><span class="sxs-lookup"><span data-stu-id="186ab-165">In the next section, you change the name of that column to `FirstName`.</span></span>

---

### <a name="the-column-attribute"></a><span data-ttu-id="186ab-166">Attribut Column</span><span class="sxs-lookup"><span data-stu-id="186ab-166">The Column attribute</span></span>

```csharp
[Column("FirstName")]
public string FirstMidName { get; set; }
```

<span data-ttu-id="186ab-167">Les attributs peuvent contrôler comment les classes et les propriétés sont mappées à la base de données.</span><span class="sxs-lookup"><span data-stu-id="186ab-167">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="186ab-168">Dans le modèle `Student`, l’attribut `Column` sert à mapper le nom de la propriété `FirstMidName` à « FirstName » dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="186ab-168">In the `Student` model, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the database.</span></span>

<span data-ttu-id="186ab-169">Pendant la création de la base de données, les noms de propriétés du modèle sont utilisés comme noms de colonnes (sauf quand l’attribut `Column` est utilisé).</span><span class="sxs-lookup"><span data-stu-id="186ab-169">When the database is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span> <span data-ttu-id="186ab-170">Le modèle `Student` utilise `FirstMidName` pour le champ de prénom, car le champ peut également contenir un deuxième prénom.</span><span class="sxs-lookup"><span data-stu-id="186ab-170">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="186ab-171">Avec l’attribut `[Column]`, dans le modèle de données, `Student.FirstMidName` est mappé à la colonne `FirstName` de la table `Student`.</span><span class="sxs-lookup"><span data-stu-id="186ab-171">With the `[Column]` attribute, `Student.FirstMidName` in the data model maps to the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="186ab-172">L’ajout de l’attribut `Column` change le modèle sur lequel repose `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="186ab-172">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="186ab-173">Le modèle sur lequel repose le `SchoolContext` ne correspond plus à la base de données.</span><span class="sxs-lookup"><span data-stu-id="186ab-173">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="186ab-174">Cette différence sera résolue en ajoutant une migration plus loin dans ce tutoriel.</span><span class="sxs-lookup"><span data-stu-id="186ab-174">That discrepancy will be resolved by adding a migration later in this tutorial.</span></span>

### <a name="the-required-attribute"></a><span data-ttu-id="186ab-175">Attribut Required</span><span class="sxs-lookup"><span data-stu-id="186ab-175">The Required attribute</span></span>

```csharp
[Required]
```

<span data-ttu-id="186ab-176">L’attribut `Required` fait des propriétés de nom des champs obligatoires.</span><span class="sxs-lookup"><span data-stu-id="186ab-176">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="186ab-177">L’attribut `Required` n’est pas nécessaire pour les types qui n’autorisent pas les valeurs Null comme les types valeur (par exemple, `DateTime`, `int` et `double`).</span><span class="sxs-lookup"><span data-stu-id="186ab-177">The `Required` attribute isn't needed for non-nullable types such as value types (for example, `DateTime`, `int`, and `double`).</span></span> <span data-ttu-id="186ab-178">Les types qui n’acceptent pas les valeurs Null sont traités automatiquement comme des champs obligatoires.</span><span class="sxs-lookup"><span data-stu-id="186ab-178">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="186ab-179">L'attribut `Required` doit être utilisé avec `MinimumLength` pour appliquer `MinimumLength`.</span><span class="sxs-lookup"><span data-stu-id="186ab-179">The `Required` attribute must be used with `MinimumLength` for the `MinimumLength` to be enforced.</span></span>

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

<span data-ttu-id="186ab-180">`MinimumLength` et `Required` autorisent un espace blanc pour satisfaire la validation.</span><span class="sxs-lookup"><span data-stu-id="186ab-180">`MinimumLength` and `Required` allow whitespace to satisfy the validation.</span></span> <span data-ttu-id="186ab-181">Utilisez l'attribut `RegularExpression` pour un contrôle total sur la chaîne.</span><span class="sxs-lookup"><span data-stu-id="186ab-181">Use the `RegularExpression` attribute for full control over the string.</span></span>

### <a name="the-display-attribute"></a><span data-ttu-id="186ab-182">Attribut Display</span><span class="sxs-lookup"><span data-stu-id="186ab-182">The Display attribute</span></span>

```csharp
[Display(Name = "Last Name")]
```

<span data-ttu-id="186ab-183">L’attribut `Display` indique que la légende des zones de texte doit être « First Name », « Last Name », « Full Name » et « Enrollment Date ».</span><span class="sxs-lookup"><span data-stu-id="186ab-183">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="186ab-184">Les légendes par défaut ne comportaient pas d’espace entre les mots, par exemple « Lastname ».</span><span class="sxs-lookup"><span data-stu-id="186ab-184">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="create-a-migration"></a><span data-ttu-id="186ab-185">Créer une migration</span><span class="sxs-lookup"><span data-stu-id="186ab-185">Create a migration</span></span>

<span data-ttu-id="186ab-186">Exécutez l’application et accédez à la page des étudiants.</span><span class="sxs-lookup"><span data-stu-id="186ab-186">Run the app and go to the Students page.</span></span> <span data-ttu-id="186ab-187">Une exception est levée.</span><span class="sxs-lookup"><span data-stu-id="186ab-187">An exception is thrown.</span></span> <span data-ttu-id="186ab-188">En raison de l’attribut `[Column]`, EF s’attend à trouver une colonne nommée `FirstName`, mais le nom de la colonne dans la base de données est toujours `FirstMidName`.</span><span class="sxs-lookup"><span data-stu-id="186ab-188">The `[Column]` attribute causes EF to expect to find a column named `FirstName`, but the column name in the database is still `FirstMidName`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="186ab-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="186ab-189">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="186ab-190">Le message d’erreur est semblable à l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="186ab-190">The error message is similar to the following example:</span></span>

```
SqlException: Invalid column name 'FirstName'.
```

* <span data-ttu-id="186ab-191">Dans PMC, entrez les commandes suivantes pour créer une migration et mettre à jour la base de données :</span><span class="sxs-lookup"><span data-stu-id="186ab-191">In the PMC, enter the following commands to create a new migration and update the database:</span></span>

  ```powershell
  Add-Migration ColumnFirstName
  Update-Database
  ```

  <span data-ttu-id="186ab-192">La première de ces commandes génère le message d’avertissement suivant :</span><span class="sxs-lookup"><span data-stu-id="186ab-192">The first of these commands generates the following warning message:</span></span>

  ```text
  An operation was scaffolded that may result in the loss of data.
  Please review the migration for accuracy.
  ```

  <span data-ttu-id="186ab-193">Cet avertissement est généré, car les champs de nom sont désormais limités à 50 caractères.</span><span class="sxs-lookup"><span data-stu-id="186ab-193">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="186ab-194">Si la base de données comporte un nom de plus 50 caractères, tous les caractères au-delà du cinquantième sont perdus.</span><span class="sxs-lookup"><span data-stu-id="186ab-194">If a name in the database had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="186ab-195">Ouvrez la table Student dans SSOX :</span><span class="sxs-lookup"><span data-stu-id="186ab-195">Open the Student table in SSOX:</span></span>

  ![Table Students dans SSOX après les migrations](complex-data-model/_static/ssox-after-migration.png)

  <span data-ttu-id="186ab-197">Avant l’application de la migration, les colonnes de noms étaient de type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span><span class="sxs-lookup"><span data-stu-id="186ab-197">Before the migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="186ab-198">Les colonnes de nom sont maintenant `nvarchar(50)`.</span><span class="sxs-lookup"><span data-stu-id="186ab-198">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="186ab-199">Le nom de la colonne est passé de `FirstMidName` à `FirstName`.</span><span class="sxs-lookup"><span data-stu-id="186ab-199">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="186ab-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="186ab-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="186ab-201">Le message d’erreur est semblable à l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="186ab-201">The error message is similar to the following example:</span></span>

```
SqliteException: SQLite Error 1: 'no such column: s.FirstName'.
```

* <span data-ttu-id="186ab-202">Ouvrez une fenêtre de commande dans le dossier du projet.</span><span class="sxs-lookup"><span data-stu-id="186ab-202">Open a command window in the project folder.</span></span> <span data-ttu-id="186ab-203">Entrez les commandes suivantes pour créer une migration et mettre à jour la base de données :</span><span class="sxs-lookup"><span data-stu-id="186ab-203">Enter the following commands to create a new migration and update the database:</span></span>

  ```dotnetcli
  dotnet ef migrations add ColumnFirstName
  dotnet ef database update
  ```

  <span data-ttu-id="186ab-204">La commande de mise à jour de base de données affiche une erreur semblable à l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="186ab-204">The database update command displays an error like the following example:</span></span>

  ```text
  SQLite does not support this migration operation ('AlterColumnOperation'). For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
  ```

<span data-ttu-id="186ab-205">Pour ce tutoriel, la façon de passer cette erreur consiste à supprimer et à recréer la migration initiale.</span><span class="sxs-lookup"><span data-stu-id="186ab-205">For this tutorial, the way to get past this error is to delete and re-create the initial migration.</span></span> <span data-ttu-id="186ab-206">Pour plus d’informations, consultez la note d’avertissement SQLite au début du [tutoriel sur les migrations](xref:data/ef-rp/migrations).</span><span class="sxs-lookup"><span data-stu-id="186ab-206">For more information, see the SQLite warning note at the top of the [migrations tutorial](xref:data/ef-rp/migrations).</span></span>

* <span data-ttu-id="186ab-207">Supprimez le dossier *Migrations*.</span><span class="sxs-lookup"><span data-stu-id="186ab-207">Delete the *Migrations* folder.</span></span>
* <span data-ttu-id="186ab-208">Exécutez les commandes suivantes pour supprimer la base de données, créer une migration initiale et appliquer la migration :</span><span class="sxs-lookup"><span data-stu-id="186ab-208">Run the following commands to drop the database, create a new initial migration, and apply the migration:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

* <span data-ttu-id="186ab-209">Examinez la table Student dans votre outil SQLite.</span><span class="sxs-lookup"><span data-stu-id="186ab-209">Examine the Student table in your SQLite tool.</span></span> <span data-ttu-id="186ab-210">La colonne FirstMidName est désormais FirstName.</span><span class="sxs-lookup"><span data-stu-id="186ab-210">The column that was FirstMidName is now FirstName.</span></span>

---

* <span data-ttu-id="186ab-211">Exécutez l’application et accédez à la page des étudiants.</span><span class="sxs-lookup"><span data-stu-id="186ab-211">Run the app and go to the Students page.</span></span>
* <span data-ttu-id="186ab-212">Notez que les heures ne sont pas entrées ou affichées avec les dates.</span><span class="sxs-lookup"><span data-stu-id="186ab-212">Notice that times are not input or displayed along with dates.</span></span>
* <span data-ttu-id="186ab-213">Sélectionnez **Create New** et essayez d’entrer un nom de plus de 50 caractères.</span><span class="sxs-lookup"><span data-stu-id="186ab-213">Select **Create New**, and try to enter a name longer than 50 characters.</span></span>

> [!Note]
> <span data-ttu-id="186ab-214">Dans les sections suivantes, la génération de l’application à certaines étapes génère des erreurs du compilateur.</span><span class="sxs-lookup"><span data-stu-id="186ab-214">In the following sections, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="186ab-215">Les instructions indiquent quand générer l’application.</span><span class="sxs-lookup"><span data-stu-id="186ab-215">The instructions specify when to build the app.</span></span>

## <a name="the-instructor-entity"></a><span data-ttu-id="186ab-216">Entité Instructor</span><span class="sxs-lookup"><span data-stu-id="186ab-216">The Instructor Entity</span></span>

![Entité Instructor](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="186ab-218">Créez *Models/Instructor.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="186ab-218">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Instructor.cs)]

<span data-ttu-id="186ab-219">Plusieurs attributs peuvent être sur une seule ligne.</span><span class="sxs-lookup"><span data-stu-id="186ab-219">Multiple attributes can be on one line.</span></span> <span data-ttu-id="186ab-220">Les attributs `HireDate` peuvent être écrits comme suit :</span><span class="sxs-lookup"><span data-stu-id="186ab-220">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="navigation-properties"></a><span data-ttu-id="186ab-221">Propriétés de navigation</span><span class="sxs-lookup"><span data-stu-id="186ab-221">Navigation properties</span></span>

<span data-ttu-id="186ab-222">Les propriétés `CourseAssignments` et `OfficeAssignment` sont des propriétés de navigation.</span><span class="sxs-lookup"><span data-stu-id="186ab-222">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="186ab-223">Un formateur pouvant animer un nombre quelconque de cours, `CourseAssignments` est défini comme une collection.</span><span class="sxs-lookup"><span data-stu-id="186ab-223">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="186ab-224">Un formateur ne pouvant avoir au plus un bureau, la propriété `OfficeAssignment` contient une seule entité `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="186ab-224">An instructor can have at most one office, so the `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="186ab-225">`OfficeAssignment` a la valeur Null si aucun bureau n’est affecté.</span><span class="sxs-lookup"><span data-stu-id="186ab-225">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="the-officeassignment-entity"></a><span data-ttu-id="186ab-226">Entité OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="186ab-226">The OfficeAssignment entity</span></span>

![Entité OfficeAssignment](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="186ab-228">Créez *Models/OfficeAssignment.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="186ab-228">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="186ab-229">Attribut Key</span><span class="sxs-lookup"><span data-stu-id="186ab-229">The Key attribute</span></span>

<span data-ttu-id="186ab-230">L’attribut `[Key]` est utilisé pour identifier une propriété comme clé primaire quand le nom de la propriété est autre que classnameID ou ID.</span><span class="sxs-lookup"><span data-stu-id="186ab-230">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="186ab-231">Il existe une relation un-à-zéro-ou-un entre les entités `Instructor` et `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="186ab-231">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="186ab-232">Une affectation de bureau existe uniquement relativement au formateur auquel elle est assignée.</span><span class="sxs-lookup"><span data-stu-id="186ab-232">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="186ab-233">La clé primaire `OfficeAssignment` est également sa clé étrangère pour l’entité `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="186ab-233">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span>

<span data-ttu-id="186ab-234">EF Core ne peut pas reconnaître automatiquement `InstructorID` comme clé primaire de `OfficeAssignment`, car `InstructorID` ne respecte pas la convention d’affectation de noms d’ID ou de classnameID.</span><span class="sxs-lookup"><span data-stu-id="186ab-234">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because `InstructorID` doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="186ab-235">Ainsi, l’attribut `Key` est utilisé pour identifier `InstructorID` comme clé primaire :</span><span class="sxs-lookup"><span data-stu-id="186ab-235">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="186ab-236">Par défaut, EF Core traite la clé comme n’étant pas générée par la base de données, car la colonne est utilisée pour une relation d’identification.</span><span class="sxs-lookup"><span data-stu-id="186ab-236">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="186ab-237">Propriété de navigation Instructor</span><span class="sxs-lookup"><span data-stu-id="186ab-237">The Instructor navigation property</span></span>

<span data-ttu-id="186ab-238">La propriété de navigation `Instructor.OfficeAssignment` peut avoir la valeur null, car il n’est pas certain qu’il existe une ligne `OfficeAssignment` pour un formateur donné.</span><span class="sxs-lookup"><span data-stu-id="186ab-238">The `Instructor.OfficeAssignment` navigation property can be null because there might not be an `OfficeAssignment` row for a given instructor.</span></span> <span data-ttu-id="186ab-239">Un formateur peut ne pas avoir d’affectation de bureau.</span><span class="sxs-lookup"><span data-stu-id="186ab-239">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="186ab-240">La propriété de navigation `OfficeAssignment.Instructor` aura toujours une entité Instructor, car le type `InstructorID` de clé étrangère est `int`, type valeur qui n’autorise pas les valeurs Null.</span><span class="sxs-lookup"><span data-stu-id="186ab-240">The `OfficeAssignment.Instructor` navigation property will always have an instructor entity because the foreign key `InstructorID` type is `int`, a non-nullable value type.</span></span> <span data-ttu-id="186ab-241">Une affectation de bureau ne peut pas exister sans un formateur.</span><span class="sxs-lookup"><span data-stu-id="186ab-241">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="186ab-242">Quand une entité `Instructor` a une entité `OfficeAssignment` associée, chaque entité a une référence à l’autre dans sa propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="186ab-242">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="186ab-243">Entité Course</span><span class="sxs-lookup"><span data-stu-id="186ab-243">The Course Entity</span></span>

![Entité Course](complex-data-model/_static/course-entity.png)

<span data-ttu-id="186ab-245">Mettez à jour *Models/Course.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="186ab-245">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Course.cs?highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="186ab-246">L’entité `Course` a une propriété de clé étrangère `DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="186ab-246">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="186ab-247">`DepartmentID` pointe vers l’entité `Department` associée.</span><span class="sxs-lookup"><span data-stu-id="186ab-247">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="186ab-248">L’entité `Course` a une propriété de navigation `Department`.</span><span class="sxs-lookup"><span data-stu-id="186ab-248">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="186ab-249">EF Core n’exige pas de propriété de clé étrangère pour un modèle de données quand le modèle a une propriété de navigation pour une entité associée.</span><span class="sxs-lookup"><span data-stu-id="186ab-249">EF Core doesn't require a foreign key property for a data model when the model has a navigation property for a related entity.</span></span> <span data-ttu-id="186ab-250">EF Core crée automatiquement des clés étrangères dans la base de données partout où elles sont nécessaires.</span><span class="sxs-lookup"><span data-stu-id="186ab-250">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="186ab-251">EF Core crée des [propriétés cachées](/ef/core/modeling/shadow-properties) pour les clés étrangères créées automatiquement.</span><span class="sxs-lookup"><span data-stu-id="186ab-251">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="186ab-252">Cependant, le fait d’inclure la clé étrangère dans le modèle de données peut rendre les mises à jour plus simples et plus efficaces.</span><span class="sxs-lookup"><span data-stu-id="186ab-252">However, explicitly including the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="186ab-253">Par exemple, considérez un modèle où la propriété de clé étrangère `DepartmentID` n’est *pas* incluse.</span><span class="sxs-lookup"><span data-stu-id="186ab-253">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="186ab-254">Quand une entité Course est récupérée en vue d’une modification :</span><span class="sxs-lookup"><span data-stu-id="186ab-254">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="186ab-255">La propriété `Department` a la valeur Null si elle n’est pas chargée explicitement.</span><span class="sxs-lookup"><span data-stu-id="186ab-255">The `Department` property is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="186ab-256">Pour mettre à jour l’entité Course, vous devez d’abord récupérer l’entité `Department`.</span><span class="sxs-lookup"><span data-stu-id="186ab-256">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="186ab-257">Quand la propriété de clé étrangère `DepartmentID` est incluse dans le modèle de données, il n’est pas nécessaire de récupérer l’entité `Department` avant une mise à jour.</span><span class="sxs-lookup"><span data-stu-id="186ab-257">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="186ab-258">Attribut DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="186ab-258">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="186ab-259">L’attribut `[DatabaseGenerated(DatabaseGeneratedOption.None)]` indique que la clé primaire est fournie par l’application plutôt que générée par la base de données.</span><span class="sxs-lookup"><span data-stu-id="186ab-259">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="186ab-260">Par défaut, EF Core part du principe que les valeurs de clé primaire sont générées par la base de données.</span><span class="sxs-lookup"><span data-stu-id="186ab-260">By default, EF Core assumes that PK values are generated by the database.</span></span> <span data-ttu-id="186ab-261">La génération par la base de données est généralement la meilleure approche.</span><span class="sxs-lookup"><span data-stu-id="186ab-261">Database-generated is generally the best approach.</span></span> <span data-ttu-id="186ab-262">Pour les entités `Course`, l’utilisateur spécifie la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="186ab-262">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="186ab-263">Par exemple, un numéro de cours comme une série 1000 pour le département Mathématiques et une série 2000 pour le département Anglais.</span><span class="sxs-lookup"><span data-stu-id="186ab-263">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="186ab-264">Vous pouvez aussi utiliser l’attribut `DatabaseGenerated` pour générer des valeurs par défaut.</span><span class="sxs-lookup"><span data-stu-id="186ab-264">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="186ab-265">Par exemple, la base de données peut générer automatiquement un champ de date pour enregistrer la date de création ou de mise à jour d’une ligne.</span><span class="sxs-lookup"><span data-stu-id="186ab-265">For example, the database can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="186ab-266">Pour plus d’informations, consultez [Propriétés générées](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="186ab-266">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="186ab-267">Propriétés de clé étrangère et de navigation</span><span class="sxs-lookup"><span data-stu-id="186ab-267">Foreign key and navigation properties</span></span>

<span data-ttu-id="186ab-268">Les propriétés de clé étrangère et les propriétés de navigation dans l’entité `Course` reflètent les relations suivantes :</span><span class="sxs-lookup"><span data-stu-id="186ab-268">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="186ab-269">Un cours étant affecté à un seul département, il y a une clé étrangère `DepartmentID` et une propriété de navigation `Department`.</span><span class="sxs-lookup"><span data-stu-id="186ab-269">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="186ab-270">Un cours pouvant avoir un nombre quelconque d’étudiants inscrits, la propriété de navigation `Enrollments` est une collection :</span><span class="sxs-lookup"><span data-stu-id="186ab-270">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="186ab-271">Un cours pouvant être animé par plusieurs formateurs, la propriété de navigation `CourseAssignments` est une collection :</span><span class="sxs-lookup"><span data-stu-id="186ab-271">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="186ab-272">`CourseAssignment` est expliquée [plus loin](#many-to-many-relationships).</span><span class="sxs-lookup"><span data-stu-id="186ab-272">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="the-department-entity"></a><span data-ttu-id="186ab-273">Entité Department</span><span class="sxs-lookup"><span data-stu-id="186ab-273">The Department entity</span></span>

![Entité de service](complex-data-model/_static/department-entity.png)

<span data-ttu-id="186ab-275">Créez *Models/Department.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="186ab-275">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Models/Department1.cs)]

### <a name="the-column-attribute"></a><span data-ttu-id="186ab-276">Attribut Column</span><span class="sxs-lookup"><span data-stu-id="186ab-276">The Column attribute</span></span>

<span data-ttu-id="186ab-277">Précédemment, vous avez utilisé l’attribut `Column` pour changer le mappage des noms de colonne.</span><span class="sxs-lookup"><span data-stu-id="186ab-277">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="186ab-278">Dans le code de l’entité `Department`, l’attribut `Column` est utilisé pour changer le mappage des types de données SQL.</span><span class="sxs-lookup"><span data-stu-id="186ab-278">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="186ab-279">La colonne `Budget` est définie à l’aide du type monétaire SQL Server dans la base de données :</span><span class="sxs-lookup"><span data-stu-id="186ab-279">The `Budget` column is defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="186ab-280">Le mappage de colonnes n’est généralement pas nécessaire.</span><span class="sxs-lookup"><span data-stu-id="186ab-280">Column mapping is generally not required.</span></span> <span data-ttu-id="186ab-281">EF Core choisit le type de données SQL Server approprié en fonction du type CLR de la propriété.</span><span class="sxs-lookup"><span data-stu-id="186ab-281">EF Core chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="186ab-282">Le type CLR `decimal` est mappé à un type SQL Server `decimal`.</span><span class="sxs-lookup"><span data-stu-id="186ab-282">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="186ab-283">`Budget` étant une valeur monétaire, le type de données money est plus approprié.</span><span class="sxs-lookup"><span data-stu-id="186ab-283">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="186ab-284">Propriétés de clé étrangère et de navigation</span><span class="sxs-lookup"><span data-stu-id="186ab-284">Foreign key and navigation properties</span></span>

<span data-ttu-id="186ab-285">Les propriétés de clé étrangère et de navigation reflètent les relations suivantes :</span><span class="sxs-lookup"><span data-stu-id="186ab-285">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="186ab-286">Un département peut avoir ou ne pas avoir un administrateur.</span><span class="sxs-lookup"><span data-stu-id="186ab-286">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="186ab-287">Un administrateur est toujours un formateur.</span><span class="sxs-lookup"><span data-stu-id="186ab-287">An administrator is always an instructor.</span></span> <span data-ttu-id="186ab-288">Ainsi, la propriété `InstructorID` est incluse en tant que clé étrangère de l’entité `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="186ab-288">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="186ab-289">La propriété de navigation se nomme `Administrator`, mais elle contient une entité `Instructor` :</span><span class="sxs-lookup"><span data-stu-id="186ab-289">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="186ab-290">Le point d’interrogation (?) dans le code précédent indique que la propriété est nullable.</span><span class="sxs-lookup"><span data-stu-id="186ab-290">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="186ab-291">Un département pouvant avoir de nombreux cours, il existe une propriété de navigation Courses :</span><span class="sxs-lookup"><span data-stu-id="186ab-291">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="186ab-292">Par convention, EF Core autorise la suppression en cascade pour les clés étrangères non nullables et pour les relations plusieurs à plusieurs.</span><span class="sxs-lookup"><span data-stu-id="186ab-292">By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="186ab-293">Ce comportement par défaut peut engendrer des règles de suppression en cascade circulaires.</span><span class="sxs-lookup"><span data-stu-id="186ab-293">This default behavior can result in circular cascade delete rules.</span></span> <span data-ttu-id="186ab-294">Les règles de suppression en cascade circulaires provoquent une exception quand une migration est ajoutée.</span><span class="sxs-lookup"><span data-stu-id="186ab-294">Circular cascade delete rules cause an exception when a migration is added.</span></span>

<span data-ttu-id="186ab-295">Par exemple, si la propriété `Department.InstructorID` a été définie comme n’acceptant pas les valeurs Null, EF Core configure une règle de suppression en cascade.</span><span class="sxs-lookup"><span data-stu-id="186ab-295">For example, if the `Department.InstructorID` property was defined as non-nullable, EF Core would configure a cascade delete rule.</span></span> <span data-ttu-id="186ab-296">Dans ce cas, le service est supprimé quand le formateur désigné comme étant son administrateur est supprimé.</span><span class="sxs-lookup"><span data-stu-id="186ab-296">In that case, the department would be deleted when the instructor assigned as its administrator is deleted.</span></span> <span data-ttu-id="186ab-297">Dans ce scénario, une règle de restriction est plus logique.</span><span class="sxs-lookup"><span data-stu-id="186ab-297">In this scenario, a restrict rule would make more sense.</span></span> <span data-ttu-id="186ab-298">L' [API Fluent](#fluent-api-alternative-to-attributes) suivante définit une règle de restriction et désactive la suppression en cascade.</span><span class="sxs-lookup"><span data-stu-id="186ab-298">The following [fluent API](#fluent-api-alternative-to-attributes) would set a restrict rule and disable cascade delete.</span></span>

  ```csharp
  modelBuilder.Entity<Department>()
     .HasOne(d => d.Administrator)
     .WithMany()
     .OnDelete(DeleteBehavior.Restrict)
  ```

## <a name="the-enrollment-entity"></a><span data-ttu-id="186ab-299">L’entité Enrollment</span><span class="sxs-lookup"><span data-stu-id="186ab-299">The Enrollment entity</span></span>

<span data-ttu-id="186ab-300">Un enregistrement d’inscription correspond à un cours suivi par un étudiant.</span><span class="sxs-lookup"><span data-stu-id="186ab-300">An enrollment record is for one course taken by one student.</span></span>

![Entité Enrollment](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="186ab-302">Mettez à jour *Models/Enrollment.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="186ab-302">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Enrollment.cs?highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="186ab-303">Propriétés de clé étrangère et de navigation</span><span class="sxs-lookup"><span data-stu-id="186ab-303">Foreign key and navigation properties</span></span>

<span data-ttu-id="186ab-304">Les propriétés de clé étrangère et de navigation reflètent les relations suivantes :</span><span class="sxs-lookup"><span data-stu-id="186ab-304">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="186ab-305">Un enregistrement d’inscription ne concernant qu’un seul cours, il y a une propriété de clé étrangère `CourseID` et une propriété de navigation `Course` :</span><span class="sxs-lookup"><span data-stu-id="186ab-305">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="186ab-306">Un enregistrement d’inscription ne concernant qu’un seul étudiant, il y a une propriété de clé étrangère `StudentID` et une propriété de navigation `Student` :</span><span class="sxs-lookup"><span data-stu-id="186ab-306">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="186ab-307">Relations plusieurs-à-plusieurs</span><span class="sxs-lookup"><span data-stu-id="186ab-307">Many-to-Many Relationships</span></span>

<span data-ttu-id="186ab-308">Il existe une relation plusieurs-à-plusieurs entre les entités `Student` et `Course`.</span><span class="sxs-lookup"><span data-stu-id="186ab-308">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="186ab-309">L’entité `Enrollment` joue le rôle de table de jointure plusieurs-à-plusieurs *avec charge utile* dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="186ab-309">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="186ab-310">« Avec charge utile » signifie que la table `Enrollment` contient des données supplémentaires en plus des clés étrangères pour les tables jointes (dans le cas présent, la clé primaire et `Grade`).</span><span class="sxs-lookup"><span data-stu-id="186ab-310">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="186ab-311">L’illustration suivante montre à quoi ressemblent ces relations dans un diagramme d’entité.</span><span class="sxs-lookup"><span data-stu-id="186ab-311">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="186ab-312">(Ce diagramme a été généré à l’aide [d’EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) pour EF 6. x.</span><span class="sxs-lookup"><span data-stu-id="186ab-312">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="186ab-313">Sa création ne fait pas partie de ce didacticiel.)</span><span class="sxs-lookup"><span data-stu-id="186ab-313">Creating the diagram isn't part of the tutorial.)</span></span>

![Relation plusieurs-à-plusieurs Student-Course](complex-data-model/_static/student-course.png)

<span data-ttu-id="186ab-315">Chaque ligne de relation comporte un 1 à une extrémité et un astérisque (\*) à l’autre, ce qui indique une relation un-à-plusieurs.</span><span class="sxs-lookup"><span data-stu-id="186ab-315">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="186ab-316">Si la table `Enrollment` n’incluait pas d’informations de notes, elle aurait uniquement besoin de contenir les deux clés étrangères (`CourseID` et `StudentID`).</span><span class="sxs-lookup"><span data-stu-id="186ab-316">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="186ab-317">Une table de jointure plusieurs-à-plusieurs sans charge utile est parfois appelée « table de jointure pure ».</span><span class="sxs-lookup"><span data-stu-id="186ab-317">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="186ab-318">Les entités `Instructor` et `Course` ont une relation plusieurs-à-plusieurs à l’aide d’une table de jointure pure.</span><span class="sxs-lookup"><span data-stu-id="186ab-318">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="186ab-319">Remarque : EF 6.x prend en charge les tables de jointure implicites pour les relations plusieurs-à-plusieurs, mais pas EF Core.</span><span class="sxs-lookup"><span data-stu-id="186ab-319">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="186ab-320">Pour plus d’informations, consultez [Many-to-many relationships in EF Core 2.0 (Relations plusieurs-à-plusieurs dans EF Core 2.0)](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span><span class="sxs-lookup"><span data-stu-id="186ab-320">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="186ab-321">Entité CourseAssignment</span><span class="sxs-lookup"><span data-stu-id="186ab-321">The CourseAssignment entity</span></span>

![Entité CourseAssignment](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="186ab-323">Créez *Models/CourseAssignment.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="186ab-323">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/CourseAssignment.cs)]

<span data-ttu-id="186ab-324">La relation « plusieurs-à-plusieurs » entre le formateur et les cours nécessite une table de jointure, et l’entité pour cette table de jointure est CourseAssignment.</span><span class="sxs-lookup"><span data-stu-id="186ab-324">The Instructor-to-Courses many-to-many relationship requires a join table, and the entity for that join table is CourseAssignment.</span></span>

![Instructor-to-Courses m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="186ab-326">Il est courant de nommer une entité de jointure `EntityName1EntityName2`.</span><span class="sxs-lookup"><span data-stu-id="186ab-326">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="186ab-327">Par exemple, la table de jointure Instructor-to-Courses utilisant ce modèle est `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="186ab-327">For example, the Instructor-to-Courses join table using this pattern would be `CourseInstructor`.</span></span> <span data-ttu-id="186ab-328">Toutefois, nous vous recommandons d’utiliser un nom qui décrit la relation.</span><span class="sxs-lookup"><span data-stu-id="186ab-328">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="186ab-329">Les modèles de données sont simples au début, puis ils augmentent en complexité.</span><span class="sxs-lookup"><span data-stu-id="186ab-329">Data models start out simple and grow.</span></span> <span data-ttu-id="186ab-330">Les tables de jointure sans charge utile évoluent fréquemment pour inclure une charge utile.</span><span class="sxs-lookup"><span data-stu-id="186ab-330">Join tables without payload (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="186ab-331">En commençant par un nom d’entité descriptif, vous n’aurez pas besoin de modifier le nom quand la table de jointure changera.</span><span class="sxs-lookup"><span data-stu-id="186ab-331">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="186ab-332">Dans l’idéal, l’entité de jointure aura son propre nom (éventuellement un mot unique) naturel dans le domaine d’entreprise.</span><span class="sxs-lookup"><span data-stu-id="186ab-332">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="186ab-333">Par exemple, les livres et les clients pourraient être liés avec une entité de jointure nommée Évaluations.</span><span class="sxs-lookup"><span data-stu-id="186ab-333">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="186ab-334">Pour la relation plusieurs-à-plusieurs Instructor-Courses, il vaut mieux utiliser `CourseAssignment` que `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="186ab-334">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="186ab-335">Clé composite</span><span class="sxs-lookup"><span data-stu-id="186ab-335">Composite key</span></span>

<span data-ttu-id="186ab-336">Ensemble, le deux clés étrangères dans `CourseAssignment` (`InstructorID` et `CourseID`) identifient de façon unique chaque ligne de la table `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="186ab-336">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="186ab-337">`CourseAssignment` ne nécessite pas de clé primaire dédiée.</span><span class="sxs-lookup"><span data-stu-id="186ab-337">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="186ab-338">Les propriétés `InstructorID` et `CourseID` fonctionnent comme une clé primaire composite.</span><span class="sxs-lookup"><span data-stu-id="186ab-338">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="186ab-339">Le seul moyen de spécifier des clés primaires composites dans EF Core consiste à faire appel à l’*API Fluent*.</span><span class="sxs-lookup"><span data-stu-id="186ab-339">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="186ab-340">La section suivante montre comment configurer la clé primaire composite.</span><span class="sxs-lookup"><span data-stu-id="186ab-340">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="186ab-341">La clé composite garantit que :</span><span class="sxs-lookup"><span data-stu-id="186ab-341">The composite key ensures that:</span></span>

* <span data-ttu-id="186ab-342">Plusieurs lignes sont autorisées pour un cours.</span><span class="sxs-lookup"><span data-stu-id="186ab-342">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="186ab-343">Plusieurs lignes sont autorisées pour un formateur.</span><span class="sxs-lookup"><span data-stu-id="186ab-343">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="186ab-344">Plusieurs lignes ne sont pas autorisées pour la même paire formateur-cours.</span><span class="sxs-lookup"><span data-stu-id="186ab-344">Multiple rows aren't allowed for the same instructor and course.</span></span>

<span data-ttu-id="186ab-345">Comme l’entité de jointure `Enrollment` définit sa propre clé primaire, des doublons de ce type sont possibles.</span><span class="sxs-lookup"><span data-stu-id="186ab-345">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="186ab-346">Pour éviter ces doublons :</span><span class="sxs-lookup"><span data-stu-id="186ab-346">To prevent such duplicates:</span></span>

* <span data-ttu-id="186ab-347">Ajoutez un index unique sur les champs de clé primaire, ou</span><span class="sxs-lookup"><span data-stu-id="186ab-347">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="186ab-348">Configurez `Enrollment` avec une clé primaire composite similaire à `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="186ab-348">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="186ab-349">Pour plus d’informations, consultez [Index](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="186ab-349">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-database-context"></a><span data-ttu-id="186ab-350">Mettre à jour le contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="186ab-350">Update the database context</span></span>

<span data-ttu-id="186ab-351">Mettez à jour *Data/SchoolContext.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="186ab-351">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Data/SchoolContext.cs?highlight=15-18,25-31)]

<span data-ttu-id="186ab-352">Le code précédent ajoute les nouvelles entités et configure la clé primaire composite de l’entité `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="186ab-352">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="186ab-353">Alternative d’API Fluent aux attributs</span><span class="sxs-lookup"><span data-stu-id="186ab-353">Fluent API alternative to attributes</span></span>

<span data-ttu-id="186ab-354">La méthode `OnModelCreating` du code précédent utilise l’*API Fluent* pour configurer le comportement d’EF Core.</span><span class="sxs-lookup"><span data-stu-id="186ab-354">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="186ab-355">L’API est appelée « Fluent », car elle est souvent utilisée en enchaînant une série d’appels de méthode en une seule instruction.</span><span class="sxs-lookup"><span data-stu-id="186ab-355">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="186ab-356">Le [code suivant](/ef/core/modeling/#use-fluent-api-to-configure-a-model) est un exemple de l’API Fluent :</span><span class="sxs-lookup"><span data-stu-id="186ab-356">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="186ab-357">Dans ce tutoriel, l’API Fluent est utilisée uniquement pour le mappage de base de données qui ne peut pas être effectué avec des attributs.</span><span class="sxs-lookup"><span data-stu-id="186ab-357">In this tutorial, the fluent API is used only for database mapping that can't be done with attributes.</span></span> <span data-ttu-id="186ab-358">Toutefois, l’API Fluent peut spécifier la plupart des règles de mise en forme, de validation et de mappage pouvant être spécifiées à l’aide d’attributs.</span><span class="sxs-lookup"><span data-stu-id="186ab-358">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="186ab-359">Certains attributs, tels que `MinimumLength`, ne peuvent pas être appliqués avec l’API Fluent.</span><span class="sxs-lookup"><span data-stu-id="186ab-359">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="186ab-360">`MinimumLength` ne change pas le schéma. Il applique uniquement une règle de validation de longueur minimale.</span><span class="sxs-lookup"><span data-stu-id="186ab-360">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="186ab-361">Certains développeurs préfèrent utiliser exclusivement l’API Fluent afin de conserver des classes d’entité « propres ».</span><span class="sxs-lookup"><span data-stu-id="186ab-361">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="186ab-362">Vous pouvez combiner des attributs et l’API Fluent.</span><span class="sxs-lookup"><span data-stu-id="186ab-362">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="186ab-363">Certaines configurations peuvent être effectuées uniquement avec l’API Fluent (spécification d’une clé primaire composite).</span><span class="sxs-lookup"><span data-stu-id="186ab-363">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="186ab-364">Certaines autres peuvent être effectuées uniquement avec des attributs (`MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="186ab-364">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="186ab-365">Voici ce que nous recommandons pour l’utilisation des API Fluent ou des attributs :</span><span class="sxs-lookup"><span data-stu-id="186ab-365">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="186ab-366">Choisissez l’une de ces deux approches.</span><span class="sxs-lookup"><span data-stu-id="186ab-366">Choose one of these two approaches.</span></span>
* <span data-ttu-id="186ab-367">Dans la mesure du possible, utilisez l’approche choisie de manière cohérente.</span><span class="sxs-lookup"><span data-stu-id="186ab-367">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="186ab-368">Certains des attributs utilisés dans ce tutoriel sont utilisés pour :</span><span class="sxs-lookup"><span data-stu-id="186ab-368">Some of the attributes used in this tutorial are used for:</span></span>

* <span data-ttu-id="186ab-369">La validation uniquement (par exemple, `MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="186ab-369">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="186ab-370">La configuration d’EF Core uniquement (par exemple, `HasKey`).</span><span class="sxs-lookup"><span data-stu-id="186ab-370">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="186ab-371">La validation et la configuration d’EF Core (par exemple, `[StringLength(50)]`).</span><span class="sxs-lookup"><span data-stu-id="186ab-371">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="186ab-372">Pour plus d’informations sur les attributs et l’API Fluent, consultez [Méthodes de configuration](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="186ab-372">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram"></a><span data-ttu-id="186ab-373">Diagramme des entités</span><span class="sxs-lookup"><span data-stu-id="186ab-373">Entity diagram</span></span>

<span data-ttu-id="186ab-374">L’illustration suivante montre le diagramme que les outils EF Power créent pour le modèle School complet.</span><span class="sxs-lookup"><span data-stu-id="186ab-374">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![Diagramme des entités](complex-data-model/_static/diagram.png)

<span data-ttu-id="186ab-376">Le schéma précédent illustre :</span><span class="sxs-lookup"><span data-stu-id="186ab-376">The preceding diagram shows:</span></span>

* <span data-ttu-id="186ab-377">Plusieurs lignes de relations un-à-plusieurs (1 à \*).</span><span class="sxs-lookup"><span data-stu-id="186ab-377">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="186ab-378">La ligne de relation un-à-zéro-ou-un (1 à 0..1) entre les entités `Instructor` et `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="186ab-378">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="186ab-379">La ligne de relation zéro-ou-un-à-plusieurs (0..1 à \*) entre les entités `Instructor` et `Department`.</span><span class="sxs-lookup"><span data-stu-id="186ab-379">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="186ab-380">Amorcer la base de données</span><span class="sxs-lookup"><span data-stu-id="186ab-380">Seed the database</span></span>

<span data-ttu-id="186ab-381">Mettez à jour le code dans *Data/DbInitializer.cs* :</span><span class="sxs-lookup"><span data-stu-id="186ab-381">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu30/Data/DbInitializer.cs)]

<span data-ttu-id="186ab-382">Le code précédent fournit des données de valeur initiale pour les nouvelles entités.</span><span class="sxs-lookup"><span data-stu-id="186ab-382">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="186ab-383">La majeure partie de ce code crée des objets d’entités et charge des exemples de données.</span><span class="sxs-lookup"><span data-stu-id="186ab-383">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="186ab-384">Les exemples de données sont utilisés à des fins de test.</span><span class="sxs-lookup"><span data-stu-id="186ab-384">The sample data is used for testing.</span></span> <span data-ttu-id="186ab-385">Consultez `Enrollments` et `CourseAssignments` pour obtenir des exemples de la façon dont des tables de jointure plusieurs-à-plusieurs peuvent être amorcées.</span><span class="sxs-lookup"><span data-stu-id="186ab-385">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="186ab-386">Ajouter une migration</span><span class="sxs-lookup"><span data-stu-id="186ab-386">Add a migration</span></span>

<span data-ttu-id="186ab-387">Créez le projet.</span><span class="sxs-lookup"><span data-stu-id="186ab-387">Build the project.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="186ab-388">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="186ab-388">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="186ab-389">Dans PMC, exécutez la commande suivante.</span><span class="sxs-lookup"><span data-stu-id="186ab-389">In PMC, run the following command.</span></span>

```powershell
Add-Migration ComplexDataModel
```

<span data-ttu-id="186ab-390">La commande précédente affiche un avertissement concernant les pertes de données possibles.</span><span class="sxs-lookup"><span data-stu-id="186ab-390">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="186ab-391">Si la commande `database update` est exécutée, l’erreur suivante se produit :</span><span class="sxs-lookup"><span data-stu-id="186ab-391">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

<span data-ttu-id="186ab-392">Dans la section suivante, vous allez voir comment traiter cette erreur.</span><span class="sxs-lookup"><span data-stu-id="186ab-392">In the next section, you see what to do about this error.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="186ab-393">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="186ab-393">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="186ab-394">Si vous ajoutez une migration et exécutez la commande `database update`, l’erreur suivante se produit :</span><span class="sxs-lookup"><span data-stu-id="186ab-394">If you add a migration and run the `database update` command, the following error would be produced:</span></span>

```text
SQLite does not support this migration operation ('DropForeignKeyOperation').
For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
```

<span data-ttu-id="186ab-395">Dans la section suivante, vous allez découvrir comment éviter cette erreur.</span><span class="sxs-lookup"><span data-stu-id="186ab-395">In the next section, you see how to avoid this error.</span></span>

---

## <a name="apply-the-migration-or-drop-and-re-create"></a><span data-ttu-id="186ab-396">Appliquer la migration ou supprimer et recréer</span><span class="sxs-lookup"><span data-stu-id="186ab-396">Apply the migration or drop and re-create</span></span>

<span data-ttu-id="186ab-397">Maintenant que vous disposez d’une base de données, vous devez réfléchir à la façon dont vous y apporterez des modifications.</span><span class="sxs-lookup"><span data-stu-id="186ab-397">Now that you have an existing database, you need to think about how to apply changes to it.</span></span> <span data-ttu-id="186ab-398">Ce tutoriel présente deux autres solutions :</span><span class="sxs-lookup"><span data-stu-id="186ab-398">This tutorial shows two alternatives:</span></span>

* <span data-ttu-id="186ab-399">[Supprimer et recréer la base de données](#drop).</span><span class="sxs-lookup"><span data-stu-id="186ab-399">[Drop and re-create the database](#drop).</span></span> <span data-ttu-id="186ab-400">Choisissez cette section si vous utilisez SQLite.</span><span class="sxs-lookup"><span data-stu-id="186ab-400">Choose this section if you're using SQLite.</span></span>
* <span data-ttu-id="186ab-401">[Appliquez la migration à la base de données existante](#applyexisting).</span><span class="sxs-lookup"><span data-stu-id="186ab-401">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="186ab-402">Les instructions de cette section valent uniquement pour SQL Server, **pas pour SQLite**.</span><span class="sxs-lookup"><span data-stu-id="186ab-402">The instructions in this section work for SQL Server only, **not for SQLite**.</span></span> 

<span data-ttu-id="186ab-403">Les deux options fonctionnent pour SQL Server.</span><span class="sxs-lookup"><span data-stu-id="186ab-403">Either choice works for SQL Server.</span></span> <span data-ttu-id="186ab-404">Bien que la méthode d’application de la migration soit plus longue et complexe, il s’agit de l’approche privilégiée pour les environnements de production réels.</span><span class="sxs-lookup"><span data-stu-id="186ab-404">While the apply-migration method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> 

<a name="drop"></a>

## <a name="drop-and-re-create-the-database"></a><span data-ttu-id="186ab-405">Supprimer et recréer la base de données</span><span class="sxs-lookup"><span data-stu-id="186ab-405">Drop and re-create the database</span></span>

<span data-ttu-id="186ab-406">[Ignorez cette section](#apply-the-migration) si vous utilisez SQL Server et que vous souhaitez effectuer l’approche d’application de la migration dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="186ab-406">[Skip this section](#apply-the-migration) if you're using SQL Server and want to do the apply-migration approach in the following section.</span></span>

<span data-ttu-id="186ab-407">Pour forcer EF Core à créer une base de données, supprimez et mettez à jour la base de données :</span><span class="sxs-lookup"><span data-stu-id="186ab-407">To force EF Core to create a new database, drop and update the database:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="186ab-408">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="186ab-408">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="186ab-409">Dans la **console du Gestionnaire de package**, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="186ab-409">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Drop-Database
  ```

* <span data-ttu-id="186ab-410">Supprimez le dossier *Migrations*, puis exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="186ab-410">Delete the *Migrations* folder, then run the following command:</span></span>

  ```powershell
  Add-Migration InitialCreate
  Update-Database
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="186ab-411">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="186ab-411">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="186ab-412">Ouvrez une fenêtre de commande et accédez au dossier du projet.</span><span class="sxs-lookup"><span data-stu-id="186ab-412">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="186ab-413">Le dossier du projet contient le fichier *ContosoUniversity.csproj*.</span><span class="sxs-lookup"><span data-stu-id="186ab-413">The project folder contains the *ContosoUniversity.csproj* file.</span></span>

* <span data-ttu-id="186ab-414">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="186ab-414">Run the following command:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  ```

* <span data-ttu-id="186ab-415">Supprimez le dossier *Migrations*, puis exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="186ab-415">Delete the *Migrations* folder, then run the following command:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="186ab-416">Exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="186ab-416">Run the app.</span></span> <span data-ttu-id="186ab-417">L’exécution de l’application entraîne l’exécution de la méthode `DbInitializer.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="186ab-417">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="186ab-418">La méthode `DbInitializer.Initialize` remplit la nouvelle base de données.</span><span class="sxs-lookup"><span data-stu-id="186ab-418">The `DbInitializer.Initialize` populates the new database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="186ab-419">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="186ab-419">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="186ab-420">Ouvrez la base de données dans SSOX :</span><span class="sxs-lookup"><span data-stu-id="186ab-420">Open the database in SSOX:</span></span>

* <span data-ttu-id="186ab-421">Si SSOX était déjà ouvert, cliquez sur le bouton **Actualiser**.</span><span class="sxs-lookup"><span data-stu-id="186ab-421">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="186ab-422">Développez le nœud **Tables**.</span><span class="sxs-lookup"><span data-stu-id="186ab-422">Expand the **Tables** node.</span></span> <span data-ttu-id="186ab-423">Les tables créées sont affichées.</span><span class="sxs-lookup"><span data-stu-id="186ab-423">The created tables are displayed.</span></span>

  ![Tables dans SSOX](complex-data-model/_static/ssox-tables.png)

* <span data-ttu-id="186ab-425">Examinez la table **CourseAssignment** :</span><span class="sxs-lookup"><span data-stu-id="186ab-425">Examine the **CourseAssignment** table:</span></span>

  * <span data-ttu-id="186ab-426">Cliquez avec le bouton droit sur la table **CourseAssignment** et sélectionnez **Afficher les données**.</span><span class="sxs-lookup"><span data-stu-id="186ab-426">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
  * <span data-ttu-id="186ab-427">Vérifiez que la table **CourseAssignment** contient des données.</span><span class="sxs-lookup"><span data-stu-id="186ab-427">Verify the **CourseAssignment** table contains data.</span></span>

  ![Données CourseAssignment dans SSOX](complex-data-model/_static/ssox-ci-data.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="186ab-429">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="186ab-429">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="186ab-430">Utilisez votre outil SQLite pour examiner la base de données :</span><span class="sxs-lookup"><span data-stu-id="186ab-430">Use your SQLite tool to examine the database:</span></span>

* <span data-ttu-id="186ab-431">Nouvelles tables et colonnes.</span><span class="sxs-lookup"><span data-stu-id="186ab-431">New tables and columns.</span></span>
* <span data-ttu-id="186ab-432">Données amorcées dans des tables, par exemple la table **CourseAssignment**.</span><span class="sxs-lookup"><span data-stu-id="186ab-432">Seeded data in tables, for example the **CourseAssignment** table.</span></span>

---

<a name="applyexisting"></a>

## <a name="apply-the-migration"></a><span data-ttu-id="186ab-433">Appliquer la migration</span><span class="sxs-lookup"><span data-stu-id="186ab-433">Apply the migration</span></span>

<span data-ttu-id="186ab-434">Cette section est facultative.</span><span class="sxs-lookup"><span data-stu-id="186ab-434">This section is optional.</span></span> <span data-ttu-id="186ab-435">Ces étapes fonctionnent uniquement pour la Base de données locale SQL Server et seulement si vous avez ignoré la section précédente [Supprimer et recréer la base de données](#drop).</span><span class="sxs-lookup"><span data-stu-id="186ab-435">These steps work only for SQL Server LocalDB and only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="186ab-436">Quand des migrations sont exécutées avec des données existantes, il peut y avoir des contraintes de clé étrangère qui ne sont pas satisfaites avec les données existantes.</span><span class="sxs-lookup"><span data-stu-id="186ab-436">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="186ab-437">Avec des données de production, vous devez effectuer certaines actions pour migrer les données existantes.</span><span class="sxs-lookup"><span data-stu-id="186ab-437">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="186ab-438">Cette section fournit un exemple de correction des violations de contraintes de clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="186ab-438">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="186ab-439">N’effectuez pas ces modifications de code sans une sauvegarde.</span><span class="sxs-lookup"><span data-stu-id="186ab-439">Don't make these code changes without a backup.</span></span> <span data-ttu-id="186ab-440">N’apportez pas ces modifications au code si vous avez terminé la section précédente [Supprimer et recréer la base de données](#drop).</span><span class="sxs-lookup"><span data-stu-id="186ab-440">Don't make these code changes if you completed the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="186ab-441">Le fichier *{horodatage}_ComplexDataModel.cs* contient le code suivant :</span><span class="sxs-lookup"><span data-stu-id="186ab-441">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="186ab-442">Le code précédent ajoute une clé étrangère `DepartmentID` non-nullable à la table `Course`.</span><span class="sxs-lookup"><span data-stu-id="186ab-442">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="186ab-443">La base de données du tutoriel précédent contient des lignes dans `Course` ; cette table ne peut donc pas être mise à jour par des migrations.</span><span class="sxs-lookup"><span data-stu-id="186ab-443">The database from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="186ab-444">Pour faire en sorte que la migration `ComplexDataModel` fonctionne avec des données existantes</span><span class="sxs-lookup"><span data-stu-id="186ab-444">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="186ab-445">Modifiez le code pour affecter une valeur par défaut à la nouvelle colonne (`DepartmentID`).</span><span class="sxs-lookup"><span data-stu-id="186ab-445">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="186ab-446">Créez un département fictif nommé « Temp » assumant la fonction de département par défaut.</span><span class="sxs-lookup"><span data-stu-id="186ab-446">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="186ab-447">Corriger les contraintes de clé étrangère</span><span class="sxs-lookup"><span data-stu-id="186ab-447">Fix the foreign key constraints</span></span>

<span data-ttu-id="186ab-448">Dans la classe de migration `ComplexDataModel`, mettez à jour la méthode `Up` :</span><span class="sxs-lookup"><span data-stu-id="186ab-448">In the `ComplexDataModel` migration class, update the `Up` method:</span></span>

* <span data-ttu-id="186ab-449">Ouvrez le fichier *{timestamp}_ComplexDataModel.cs*.</span><span class="sxs-lookup"><span data-stu-id="186ab-449">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="186ab-450">Commentez la ligne de code qui ajoute la colonne `DepartmentID` à la table `Course`.</span><span class="sxs-lookup"><span data-stu-id="186ab-450">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="186ab-451">Ajoutez le code en surbrillance suivant.</span><span class="sxs-lookup"><span data-stu-id="186ab-451">Add the following highlighted code.</span></span> <span data-ttu-id="186ab-452">Le nouveau code va après le bloc `.CreateTable( name: "Department"` :</span><span class="sxs-lookup"><span data-stu-id="186ab-452">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]

<span data-ttu-id="186ab-453">Avec les modifications précédentes, les lignes `Course` existantes seront liées au service « Temp » après l’exécution de la méthode `ComplexDataModel.Up`.</span><span class="sxs-lookup"><span data-stu-id="186ab-453">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel.Up` method runs.</span></span>

<span data-ttu-id="186ab-454">La façon de gérer la situation présentée ici est simplifiée pour ce tutoriel.</span><span class="sxs-lookup"><span data-stu-id="186ab-454">The way of handling the situation shown here is simplified for this tutorial.</span></span> <span data-ttu-id="186ab-455">Une application de production :</span><span class="sxs-lookup"><span data-stu-id="186ab-455">A production app would:</span></span>

* <span data-ttu-id="186ab-456">Comprendrait du code ou des scripts pour ajouter des lignes `Department` et des lignes `Course` associées aux nouvelles lignes `Department`.</span><span class="sxs-lookup"><span data-stu-id="186ab-456">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="186ab-457">N’utiliserait pas le département « Temp » ou la valeur par défaut pour `Course.DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="186ab-457">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="186ab-458">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="186ab-458">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="186ab-459">Dans la **console du Gestionnaire de package**, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="186ab-459">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Update-Database
  ```

<span data-ttu-id="186ab-460">La méthode `DbInitializer.Initialize` étant conçue pour fonctionner uniquement avec une base de données vide, utilisez SSOX pour supprimer toutes les lignes des tables Student et Course.</span><span class="sxs-lookup"><span data-stu-id="186ab-460">Because the `DbInitializer.Initialize` method is designed to work only with an empty database, use SSOX to delete all the rows in the Student and Course tables.</span></span> <span data-ttu-id="186ab-461">(La suppression en cascade s’occupe de la table Enrollment)</span><span class="sxs-lookup"><span data-stu-id="186ab-461">(Cascade delete will take care of the Enrollment table.)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="186ab-462">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="186ab-462">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="186ab-463">Si vous utilisez la Base de données locale SQL Server base avec Visual Studio Code, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="186ab-463">If you're using SQL Server LocalDB with Visual Studio Code, run the following command:</span></span>

  ```dotnetcli
  dotnet ef database update
  ```

---

<span data-ttu-id="186ab-464">Exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="186ab-464">Run the app.</span></span> <span data-ttu-id="186ab-465">L’exécution de l’application entraîne l’exécution de la méthode `DbInitializer.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="186ab-465">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="186ab-466">La méthode `DbInitializer.Initialize` remplit la nouvelle base de données.</span><span class="sxs-lookup"><span data-stu-id="186ab-466">The `DbInitializer.Initialize` populates the new database.</span></span>

## <a name="next-steps"></a><span data-ttu-id="186ab-467">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="186ab-467">Next steps</span></span>

<span data-ttu-id="186ab-468">Les deux tutoriels suivants montrent comment lire et mettre à jour des données associées.</span><span class="sxs-lookup"><span data-stu-id="186ab-468">The next two tutorials show how to read and update related data.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="186ab-469">[Didacticiel précédent](xref:data/ef-rp/migrations) 
>  [Didacticiel suivant](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="186ab-469">[Previous tutorial](xref:data/ef-rp/migrations)
[Next tutorial](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="186ab-470">Dans les didacticiels précédents, nous avons travaillé avec un modèle de données de base composé de trois entités.</span><span class="sxs-lookup"><span data-stu-id="186ab-470">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="186ab-471">Dans ce tutoriel, vous allez :</span><span class="sxs-lookup"><span data-stu-id="186ab-471">In this tutorial:</span></span>

* <span data-ttu-id="186ab-472">Nous allons ajouter d’autres entités et relations</span><span class="sxs-lookup"><span data-stu-id="186ab-472">More entities and relationships are added.</span></span>
* <span data-ttu-id="186ab-473">Nous allons personnaliser le modèle de données en spécifiant des règles de mise en forme, de validation et de mappage de base de données.</span><span class="sxs-lookup"><span data-stu-id="186ab-473">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="186ab-474">Les classes d’entité pour le modèle de données final sont présentées dans l’illustration suivante :</span><span class="sxs-lookup"><span data-stu-id="186ab-474">The entity classes for the completed data model are shown in the following illustration:</span></span>

![Diagramme des entités](complex-data-model/_static/diagram.png)

<span data-ttu-id="186ab-476">Si vous rencontrez des problèmes que vous ne pouvez pas résoudre, téléchargez [l’application terminée](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="186ab-476">If you run into problems you can't solve, download the [completed app](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

## <a name="customize-the-data-model-with-attributes"></a><span data-ttu-id="186ab-477">Personnaliser le modèle de données avec des attributs</span><span class="sxs-lookup"><span data-stu-id="186ab-477">Customize the data model with attributes</span></span>

<span data-ttu-id="186ab-478">Dans cette section, nous allons personnaliser le modèle de données à l’aide d’attributs.</span><span class="sxs-lookup"><span data-stu-id="186ab-478">In this section, the data model is customized using attributes.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="186ab-479">Attribut DataType</span><span class="sxs-lookup"><span data-stu-id="186ab-479">The DataType attribute</span></span>

<span data-ttu-id="186ab-480">Actuellement, les pages sur les étudiants affichent l’heure et la date d’inscription.</span><span class="sxs-lookup"><span data-stu-id="186ab-480">The student pages currently displays the time of the enrollment date.</span></span> <span data-ttu-id="186ab-481">En règle générale, les champs de date ne montrent que la date, et non l’heure.</span><span class="sxs-lookup"><span data-stu-id="186ab-481">Typically, date fields show only the date and not the time.</span></span>

<span data-ttu-id="186ab-482">Mettez à jour *Models/Student.cs* avec le code en surbrillance suivant :</span><span class="sxs-lookup"><span data-stu-id="186ab-482">Update *Models/Student.cs* with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="186ab-483">L’attribut [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute) spécifie un type de données qui est plus spécifique que le type intrinsèque de la base de données.</span><span class="sxs-lookup"><span data-stu-id="186ab-483">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="186ab-484">Ici, seule la date doit être affichée (pas la date et l’heure).</span><span class="sxs-lookup"><span data-stu-id="186ab-484">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="186ab-485">L' [énumération DataType](/dotnet/api/system.componentmodel.dataannotations.datatype) fournit de nombreux types de données, tels que date, Time, PhoneNumber, Currency, EmailAddress, etc. L' `DataType` attribut peut également permettre à l’application de fournir automatiquement des fonctionnalités propres au type.</span><span class="sxs-lookup"><span data-stu-id="186ab-485">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="186ab-486">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="186ab-486">For example:</span></span>

* <span data-ttu-id="186ab-487">Le lien `mailto:` est créé automatiquement pour `DataType.EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="186ab-487">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="186ab-488">Le sélecteur de date est fourni pour `DataType.Date` dans la plupart des navigateurs.</span><span class="sxs-lookup"><span data-stu-id="186ab-488">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="186ab-489">L’attribut `DataType` émet des attributs HTML 5 `data-` utilisés par les navigateurs HTML 5.</span><span class="sxs-lookup"><span data-stu-id="186ab-489">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="186ab-490">Les attributs `DataType` ne fournissent aucune validation.</span><span class="sxs-lookup"><span data-stu-id="186ab-490">The `DataType` attributes don't provide validation.</span></span>

<span data-ttu-id="186ab-491">`DataType.Date` ne spécifie pas le format de la date qui s’affiche.</span><span class="sxs-lookup"><span data-stu-id="186ab-491">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="186ab-492">Par défaut, le champ de date est affiché conformément aux formats par défaut basés sur l’objet [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support) du serveur.</span><span class="sxs-lookup"><span data-stu-id="186ab-492">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="186ab-493">L’attribut `DisplayFormat` est utilisé pour spécifier explicitement le format de date :</span><span class="sxs-lookup"><span data-stu-id="186ab-493">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="186ab-494">Le paramètre `ApplyFormatInEditMode` spécifie que la mise en forme doit également être appliquée à l’interface utilisateur de modification.</span><span class="sxs-lookup"><span data-stu-id="186ab-494">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="186ab-495">Certains champs ne doivent pas utiliser `ApplyFormatInEditMode`.</span><span class="sxs-lookup"><span data-stu-id="186ab-495">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="186ab-496">Par exemple, le symbole monétaire ne doit généralement pas être affiché dans une zone de texte d’édition.</span><span class="sxs-lookup"><span data-stu-id="186ab-496">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="186ab-497">L’attribut `DisplayFormat` peut être utilisé seul.</span><span class="sxs-lookup"><span data-stu-id="186ab-497">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="186ab-498">Il est généralement préférable d’utiliser l’attribut `DataType` avec l’attribut `DisplayFormat`.</span><span class="sxs-lookup"><span data-stu-id="186ab-498">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="186ab-499">L’attribut `DataType` transmet la sémantique des données, plutôt que la manière de l’afficher à l’écran.</span><span class="sxs-lookup"><span data-stu-id="186ab-499">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="186ab-500">L’attribut `DataType` offre les avantages suivants qui ne sont pas disponibles dans `DisplayFormat` :</span><span class="sxs-lookup"><span data-stu-id="186ab-500">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="186ab-501">Le navigateur peut activer des fonctionnalités HTML5</span><span class="sxs-lookup"><span data-stu-id="186ab-501">The browser can enable HTML5 features.</span></span> <span data-ttu-id="186ab-502">(par exemple pour afficher un contrôle de calendrier, le symbole monétaire correspondant aux paramètres régionaux, des liens de messagerie, une validation des entrées côté client, et ainsi de suite).</span><span class="sxs-lookup"><span data-stu-id="186ab-502">For example, show a calendar control, the locale-appropriate currency symbol, email links, client-side input validation, etc.</span></span>
* <span data-ttu-id="186ab-503">Par défaut, le navigateur affiche les données à l’aide du format correspondant aux paramètres régionaux.</span><span class="sxs-lookup"><span data-stu-id="186ab-503">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="186ab-504">Pour plus d’informations, consultez la [ \<input> documentation tag Helper](xref:mvc/views/working-with-forms#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="186ab-504">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

<span data-ttu-id="186ab-505">Exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="186ab-505">Run the app.</span></span> <span data-ttu-id="186ab-506">Accédez à la page d’index des étudiants.</span><span class="sxs-lookup"><span data-stu-id="186ab-506">Navigate to the Students Index page.</span></span> <span data-ttu-id="186ab-507">Les heures ne sont plus affichées.</span><span class="sxs-lookup"><span data-stu-id="186ab-507">Times are no longer displayed.</span></span> <span data-ttu-id="186ab-508">Tous les affichages qui utilisent le modèle `Student` affichent la date sans heure.</span><span class="sxs-lookup"><span data-stu-id="186ab-508">Every view that uses the `Student` model displays the date without time.</span></span>

![Page d’index des étudiants affichant les dates sans les heures](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="186ab-510">Attribut StringLength</span><span class="sxs-lookup"><span data-stu-id="186ab-510">The StringLength attribute</span></span>

<span data-ttu-id="186ab-511">Vous pouvez également spécifier des règles de validation de données et des messages d’erreur de validation à l’aide d’attributs.</span><span class="sxs-lookup"><span data-stu-id="186ab-511">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="186ab-512">L’attribut [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute) spécifie les longueurs minimale et maximale de caractères autorisées dans un champ de données.</span><span class="sxs-lookup"><span data-stu-id="186ab-512">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="186ab-513">L’attribut `StringLength` fournit également la validation côté client et côté serveur.</span><span class="sxs-lookup"><span data-stu-id="186ab-513">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="186ab-514">La valeur minimale n’a aucun impact sur le schéma de base de données.</span><span class="sxs-lookup"><span data-stu-id="186ab-514">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="186ab-515">Mettez à jour le modèle `Student` avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="186ab-515">Update the `Student` model with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="186ab-516">Le code précédent limite la longueur des noms à 50 caractères.</span><span class="sxs-lookup"><span data-stu-id="186ab-516">The preceding code limits names to no more than 50 characters.</span></span> <span data-ttu-id="186ab-517">L’attribut `StringLength` n’empêche pas un utilisateur d’entrer un espace blanc comme nom.</span><span class="sxs-lookup"><span data-stu-id="186ab-517">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="186ab-518">L’attribut [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) est utilisé pour appliquer des restrictions à l’entrée.</span><span class="sxs-lookup"><span data-stu-id="186ab-518">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) attribute is used to apply restrictions to the input.</span></span> <span data-ttu-id="186ab-519">Par exemple, le code suivant exige que le premier caractère soit en majuscule et que les autres caractères soient alphabétiques :</span><span class="sxs-lookup"><span data-stu-id="186ab-519">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z]*$")]
```

<span data-ttu-id="186ab-520">Exécutez l’application :</span><span class="sxs-lookup"><span data-stu-id="186ab-520">Run the app:</span></span>

* <span data-ttu-id="186ab-521">Accédez à la page Students.</span><span class="sxs-lookup"><span data-stu-id="186ab-521">Navigate to the Students page.</span></span>
* <span data-ttu-id="186ab-522">Sélectionnez **Create New** et entrez un nom de plus de 50 caractères.</span><span class="sxs-lookup"><span data-stu-id="186ab-522">Select **Create New**, and enter a name longer than 50 characters.</span></span>
* <span data-ttu-id="186ab-523">Sélectionnez **Create**. La validation côté client affiche un message d’erreur.</span><span class="sxs-lookup"><span data-stu-id="186ab-523">Select **Create**, client-side validation shows an error message.</span></span>

![Page d’index des étudiants affichant des erreurs de longueur de chaîne](complex-data-model/_static/string-length-errors.png)

<span data-ttu-id="186ab-525">Dans **l’Explorateur d’objets SQL Server** (SSOX), ouvrez le concepteur de tables Student en double-cliquant sur la table **Student**.</span><span class="sxs-lookup"><span data-stu-id="186ab-525">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![Table Students dans SSOX avant les migrations](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="186ab-527">L’image précédente montre le schéma pour la table `Student`.</span><span class="sxs-lookup"><span data-stu-id="186ab-527">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="186ab-528">Les champs de nom sont de type `nvarchar(MAX)`, car les migrations n’ont pas été exécutées sur la base de données.</span><span class="sxs-lookup"><span data-stu-id="186ab-528">The name fields have type `nvarchar(MAX)` because migrations has not been run on the DB.</span></span> <span data-ttu-id="186ab-529">Quand nous exécuterons les migrations plus loin dans ce didacticiel, les champs de nom deviendront `nvarchar(50)`.</span><span class="sxs-lookup"><span data-stu-id="186ab-529">When migrations are run later in this tutorial, the name fields become `nvarchar(50)`.</span></span>

### <a name="the-column-attribute"></a><span data-ttu-id="186ab-530">Attribut Column</span><span class="sxs-lookup"><span data-stu-id="186ab-530">The Column attribute</span></span>

<span data-ttu-id="186ab-531">Les attributs peuvent contrôler comment les classes et les propriétés sont mappées à la base de données.</span><span class="sxs-lookup"><span data-stu-id="186ab-531">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="186ab-532">Dans cette section, nous utilisons l’attribut `Column` pour mapper le nom de la propriété `FirstMidName` « FirstName » dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="186ab-532">In this section, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the DB.</span></span>

<span data-ttu-id="186ab-533">Lors de la création de la base de données, les noms des propriétés sur le modèle sont utilisés comme noms de colonne (sauf quand l’attribut `Column` est utilisé).</span><span class="sxs-lookup"><span data-stu-id="186ab-533">When the DB is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span>

<span data-ttu-id="186ab-534">Le modèle `Student` utilise `FirstMidName` pour le champ de prénom, car le champ peut également contenir un deuxième prénom.</span><span class="sxs-lookup"><span data-stu-id="186ab-534">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="186ab-535">Mettez à jour *Student.cs* avec le code en surbrillance suivant :</span><span class="sxs-lookup"><span data-stu-id="186ab-535">Update the *Student.cs* file with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="186ab-536">Avec la modification précédente, `Student.FirstMidName` dans l’application est mappé à la colonne `FirstName` de la table `Student`.</span><span class="sxs-lookup"><span data-stu-id="186ab-536">With the preceding change, `Student.FirstMidName` in the app maps to the `FirstName` column of the `Student` table.</span></span>

<span data-ttu-id="186ab-537">L’ajout de l’attribut `Column` change le modèle sur lequel repose `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="186ab-537">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="186ab-538">Le modèle sur lequel repose le `SchoolContext` ne correspond plus à la base de données.</span><span class="sxs-lookup"><span data-stu-id="186ab-538">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="186ab-539">Si vous exécutez l’application avant d’appliquer les migrations, l’exception suivante est générée :</span><span class="sxs-lookup"><span data-stu-id="186ab-539">If the app is run before applying migrations, the following exception is generated:</span></span>

```
SqlException: Invalid column name 'FirstName'.
```

<span data-ttu-id="186ab-540">Pour mettre à jour la base de données</span><span class="sxs-lookup"><span data-stu-id="186ab-540">To update the DB:</span></span>

* <span data-ttu-id="186ab-541">Créez le projet.</span><span class="sxs-lookup"><span data-stu-id="186ab-541">Build the project.</span></span>
* <span data-ttu-id="186ab-542">Ouvrez une fenêtre de commande dans le dossier du projet.</span><span class="sxs-lookup"><span data-stu-id="186ab-542">Open a command window in the project folder.</span></span> <span data-ttu-id="186ab-543">Entrez les commandes suivantes pour créer une migration et mettre à jour la base de données :</span><span class="sxs-lookup"><span data-stu-id="186ab-543">Enter the following commands to create a new migration and update the DB:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="186ab-544">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="186ab-544">Visual Studio</span></span>](#tab/visual-studio)

```powershell
Add-Migration ColumnFirstName
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="186ab-545">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="186ab-545">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ColumnFirstName
dotnet ef database update
```

---

<span data-ttu-id="186ab-546">La commande `migrations add ColumnFirstName` génère le message d’avertissement suivant :</span><span class="sxs-lookup"><span data-stu-id="186ab-546">The `migrations add ColumnFirstName` command generates the following warning message:</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

<span data-ttu-id="186ab-547">Cet avertissement est généré, car les champs de nom sont désormais limités à 50 caractères.</span><span class="sxs-lookup"><span data-stu-id="186ab-547">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="186ab-548">Si un nom dans la base de données a plus de 50 caractères, tous les caractères au-delà du cinquantième sont perdus.</span><span class="sxs-lookup"><span data-stu-id="186ab-548">If a name in the DB had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="186ab-549">Tester l'application.</span><span class="sxs-lookup"><span data-stu-id="186ab-549">Test the app.</span></span>

<span data-ttu-id="186ab-550">Ouvrez la table Student dans SSOX :</span><span class="sxs-lookup"><span data-stu-id="186ab-550">Open the Student table in SSOX:</span></span>

![Table Students dans SSOX après les migrations](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="186ab-552">Avant l’application de la migration, les colonnes de nom étaient de type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span><span class="sxs-lookup"><span data-stu-id="186ab-552">Before migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="186ab-553">Les colonnes de nom sont maintenant `nvarchar(50)`.</span><span class="sxs-lookup"><span data-stu-id="186ab-553">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="186ab-554">Le nom de la colonne est passé de `FirstMidName` à `FirstName`.</span><span class="sxs-lookup"><span data-stu-id="186ab-554">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

> [!Note]
> <span data-ttu-id="186ab-555">Dans la section suivante, la génération de l’application à certaines étapes génère des erreurs du compilateur.</span><span class="sxs-lookup"><span data-stu-id="186ab-555">In the following section, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="186ab-556">Les instructions indiquent quand générer l’application.</span><span class="sxs-lookup"><span data-stu-id="186ab-556">The instructions specify when to build the app.</span></span>

## <a name="student-entity-update"></a><span data-ttu-id="186ab-557">Mise à jour de l’entité Student</span><span class="sxs-lookup"><span data-stu-id="186ab-557">Student entity update</span></span>

![Entité Student](complex-data-model/_static/student-entity.png)

<span data-ttu-id="186ab-559">Mettez à jour *Models/Student.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="186ab-559">Update *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="186ab-560">Attribut Required</span><span class="sxs-lookup"><span data-stu-id="186ab-560">The Required attribute</span></span>

<span data-ttu-id="186ab-561">L’attribut `Required` fait des propriétés de nom des champs obligatoires.</span><span class="sxs-lookup"><span data-stu-id="186ab-561">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="186ab-562">L’attribut `Required` n’est pas nécessaire pour les types non nullables tels que les types valeur (`DateTime`, `int`, `double` et ainsi de suite).</span><span class="sxs-lookup"><span data-stu-id="186ab-562">The `Required` attribute isn't needed for non-nullable types such as value types (`DateTime`, `int`, `double`, etc.).</span></span> <span data-ttu-id="186ab-563">Les types qui n’acceptent pas les valeurs Null sont traités automatiquement comme des champs obligatoires.</span><span class="sxs-lookup"><span data-stu-id="186ab-563">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="186ab-564">L’attribut `Required` peut être remplacé par un paramètre de longueur minimale dans l’attribut `StringLength` :</span><span class="sxs-lookup"><span data-stu-id="186ab-564">The `Required` attribute could be replaced with a minimum length parameter in the `StringLength` attribute:</span></span>

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="186ab-565">Attribut Display</span><span class="sxs-lookup"><span data-stu-id="186ab-565">The Display attribute</span></span>

<span data-ttu-id="186ab-566">L’attribut `Display` indique que la légende des zones de texte doit être « First Name », « Last Name », « Full Name » et « Enrollment Date ».</span><span class="sxs-lookup"><span data-stu-id="186ab-566">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="186ab-567">Les légendes par défaut ne comportaient pas d’espace entre les mots, par exemple « Lastname ».</span><span class="sxs-lookup"><span data-stu-id="186ab-567">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="186ab-568">Propriété calculée FullName</span><span class="sxs-lookup"><span data-stu-id="186ab-568">The FullName calculated property</span></span>

<span data-ttu-id="186ab-569">`FullName` est une propriété calculée qui retourne une valeur créée par concaténation de deux autres propriétés.</span><span class="sxs-lookup"><span data-stu-id="186ab-569">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="186ab-570">`FullName` ne peut pas être définie. Elle a uniquement un accesseur get.</span><span class="sxs-lookup"><span data-stu-id="186ab-570">`FullName` cannot be set, it has only a get accessor.</span></span> <span data-ttu-id="186ab-571">Aucune colonne `FullName` n’est créée dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="186ab-571">No `FullName` column is created in the database.</span></span>

## <a name="create-the-instructor-entity"></a><span data-ttu-id="186ab-572">Créer l’entité Instructor</span><span class="sxs-lookup"><span data-stu-id="186ab-572">Create the Instructor Entity</span></span>

![Entité Instructor](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="186ab-574">Créez *Models/Instructor.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="186ab-574">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Instructor.cs)]

<span data-ttu-id="186ab-575">Plusieurs attributs peuvent être sur une seule ligne.</span><span class="sxs-lookup"><span data-stu-id="186ab-575">Multiple attributes can be on one line.</span></span> <span data-ttu-id="186ab-576">Les attributs `HireDate` peuvent être écrits comme suit :</span><span class="sxs-lookup"><span data-stu-id="186ab-576">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="186ab-577">Propriétés de navigation CourseAssignments et OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="186ab-577">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="186ab-578">Les propriétés `CourseAssignments` et `OfficeAssignment` sont des propriétés de navigation.</span><span class="sxs-lookup"><span data-stu-id="186ab-578">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="186ab-579">Un formateur pouvant animer un nombre quelconque de cours, `CourseAssignments` est défini comme une collection.</span><span class="sxs-lookup"><span data-stu-id="186ab-579">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="186ab-580">Si une propriété de navigation contient plusieurs entités :</span><span class="sxs-lookup"><span data-stu-id="186ab-580">If a navigation property holds multiple entities:</span></span>

* <span data-ttu-id="186ab-581">Elle doit être un type de liste où les entrées peuvent être ajoutées, supprimées et mises à jour.</span><span class="sxs-lookup"><span data-stu-id="186ab-581">It must be a list type where the entries can be added, deleted, and updated.</span></span>

<span data-ttu-id="186ab-582">Les types de propriétés de navigation sont :</span><span class="sxs-lookup"><span data-stu-id="186ab-582">Navigation property types include:</span></span>

* `ICollection<T>`
* `List<T>`
* `HashSet<T>`

<span data-ttu-id="186ab-583">Si `ICollection<T>` est spécifié, EF Core crée une collection `HashSet<T>` par défaut.</span><span class="sxs-lookup"><span data-stu-id="186ab-583">If `ICollection<T>` is specified, EF Core creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="186ab-584">L’entité `CourseAssignment` est expliquée dans la section sur les relations plusieurs-à-plusieurs.</span><span class="sxs-lookup"><span data-stu-id="186ab-584">The `CourseAssignment` entity is explained in the section on many-to-many relationships.</span></span>

<span data-ttu-id="186ab-585">Les règles professionnelles de Contoso University stipulent qu’un formateur peut avoir au plus un bureau.</span><span class="sxs-lookup"><span data-stu-id="186ab-585">Contoso University business rules state that an instructor can have at most one office.</span></span> <span data-ttu-id="186ab-586">La propriété `OfficeAssignment` contient une seule entité `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="186ab-586">The `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="186ab-587">`OfficeAssignment` a la valeur Null si aucun bureau n’est affecté.</span><span class="sxs-lookup"><span data-stu-id="186ab-587">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a><span data-ttu-id="186ab-588">Créer l’entité OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="186ab-588">Create the OfficeAssignment entity</span></span>

![Entité OfficeAssignment](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="186ab-590">Créez *Models/OfficeAssignment.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="186ab-590">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="186ab-591">Attribut Key</span><span class="sxs-lookup"><span data-stu-id="186ab-591">The Key attribute</span></span>

<span data-ttu-id="186ab-592">L’attribut `[Key]` est utilisé pour identifier une propriété comme clé primaire quand le nom de la propriété est autre que classnameID ou ID.</span><span class="sxs-lookup"><span data-stu-id="186ab-592">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="186ab-593">Il existe une relation un-à-zéro-ou-un entre les entités `Instructor` et `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="186ab-593">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="186ab-594">Une affectation de bureau existe uniquement relativement au formateur auquel elle est assignée.</span><span class="sxs-lookup"><span data-stu-id="186ab-594">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="186ab-595">La clé primaire `OfficeAssignment` est également sa clé étrangère pour l’entité `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="186ab-595">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span> <span data-ttu-id="186ab-596">EF Core ne peut pas reconnaître automatiquement `InstructorID` comme clé primaire de `OfficeAssignment`, car :</span><span class="sxs-lookup"><span data-stu-id="186ab-596">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because:</span></span>

* <span data-ttu-id="186ab-597">`InstructorID` ne suit pas la convention de nommage ID ou classnameID.</span><span class="sxs-lookup"><span data-stu-id="186ab-597">`InstructorID` doesn't follow the ID or classnameID naming convention.</span></span>

<span data-ttu-id="186ab-598">Ainsi, l’attribut `Key` est utilisé pour identifier `InstructorID` comme clé primaire :</span><span class="sxs-lookup"><span data-stu-id="186ab-598">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="186ab-599">Par défaut, EF Core traite la clé comme n’étant pas générée par la base de données, car la colonne est utilisée pour une relation d’identification.</span><span class="sxs-lookup"><span data-stu-id="186ab-599">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="186ab-600">Propriété de navigation Instructor</span><span class="sxs-lookup"><span data-stu-id="186ab-600">The Instructor navigation property</span></span>

<span data-ttu-id="186ab-601">La propriété de navigation `OfficeAssignment` pour l’entité `Instructor` est nullable car :</span><span class="sxs-lookup"><span data-stu-id="186ab-601">The `OfficeAssignment` navigation property for the `Instructor` entity is nullable because:</span></span>

* <span data-ttu-id="186ab-602">Les types référence (tels que les classes) sont nullables.</span><span class="sxs-lookup"><span data-stu-id="186ab-602">Reference types (such as classes are nullable).</span></span>
* <span data-ttu-id="186ab-603">Un formateur peut ne pas avoir d’affectation de bureau.</span><span class="sxs-lookup"><span data-stu-id="186ab-603">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="186ab-604">L’entité `OfficeAssignment` a une propriété de navigation `Instructor` non-nullable car :</span><span class="sxs-lookup"><span data-stu-id="186ab-604">The `OfficeAssignment` entity has a non-nullable `Instructor` navigation property because:</span></span>

* <span data-ttu-id="186ab-605">`InstructorID` n'autorise pas les valeurs Null.</span><span class="sxs-lookup"><span data-stu-id="186ab-605">`InstructorID` is non-nullable.</span></span>
* <span data-ttu-id="186ab-606">Une affectation de bureau ne peut pas exister sans un formateur.</span><span class="sxs-lookup"><span data-stu-id="186ab-606">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="186ab-607">Quand une entité `Instructor` a une entité `OfficeAssignment` associée, chaque entité a une référence à l’autre dans sa propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="186ab-607">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="186ab-608">L’attribut `[Required]` peut être appliqué à la propriété de navigation `Instructor` :</span><span class="sxs-lookup"><span data-stu-id="186ab-608">The `[Required]` attribute could be applied to the `Instructor` navigation property:</span></span>

```csharp
[Required]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="186ab-609">Le code précédent spécifie qu’il doit y avoir un formateur associé.</span><span class="sxs-lookup"><span data-stu-id="186ab-609">The preceding code specifies that there must be a related instructor.</span></span> <span data-ttu-id="186ab-610">Le code précédent n’est pas nécessaire, car la clé étrangère `InstructorID` (qui est également la clé primaire) n'autorise pas les valeurs Null.</span><span class="sxs-lookup"><span data-stu-id="186ab-610">The preceding code is unnecessary because the `InstructorID` foreign key (which is also the PK) is non-nullable.</span></span>

## <a name="modify-the-course-entity"></a><span data-ttu-id="186ab-611">Modifier l’entité Course</span><span class="sxs-lookup"><span data-stu-id="186ab-611">Modify the Course Entity</span></span>

![Entité Course](complex-data-model/_static/course-entity.png)

<span data-ttu-id="186ab-613">Mettez à jour *Models/Course.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="186ab-613">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="186ab-614">L’entité `Course` a une propriété de clé étrangère `DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="186ab-614">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="186ab-615">`DepartmentID` pointe vers l’entité `Department` associée.</span><span class="sxs-lookup"><span data-stu-id="186ab-615">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="186ab-616">L’entité `Course` a une propriété de navigation `Department`.</span><span class="sxs-lookup"><span data-stu-id="186ab-616">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="186ab-617">EF Core n’exige pas de propriété de clé étrangère pour un modèle de données quand le modèle a une propriété de navigation pour une entité associée.</span><span class="sxs-lookup"><span data-stu-id="186ab-617">EF Core doesn't require a FK property for a data model when the model has a navigation property for a related entity.</span></span>

<span data-ttu-id="186ab-618">EF Core crée automatiquement des clés étrangères dans la base de données partout où elles sont nécessaires.</span><span class="sxs-lookup"><span data-stu-id="186ab-618">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="186ab-619">EF Core crée des [propriétés cachées](/ef/core/modeling/shadow-properties) pour les clés étrangères créées automatiquement.</span><span class="sxs-lookup"><span data-stu-id="186ab-619">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="186ab-620">Le fait d’avoir la clé étrangère dans le modèle de données peut rendre les mises à jour plus simples et plus efficaces.</span><span class="sxs-lookup"><span data-stu-id="186ab-620">Having the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="186ab-621">Par exemple, considérez un modèle où la propriété de clé étrangère `DepartmentID` n’est *pas* incluse.</span><span class="sxs-lookup"><span data-stu-id="186ab-621">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="186ab-622">Quand une entité Course est récupérée en vue d’une modification :</span><span class="sxs-lookup"><span data-stu-id="186ab-622">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="186ab-623">L’entité `Department` a la valeur Null si elle n’est pas chargée explicitement.</span><span class="sxs-lookup"><span data-stu-id="186ab-623">The `Department` entity is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="186ab-624">Pour mettre à jour l’entité Course, vous devez d’abord récupérer l’entité `Department`.</span><span class="sxs-lookup"><span data-stu-id="186ab-624">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="186ab-625">Quand la propriété de clé étrangère `DepartmentID` est incluse dans le modèle de données, il n’est pas nécessaire de récupérer l’entité `Department` avant une mise à jour.</span><span class="sxs-lookup"><span data-stu-id="186ab-625">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="186ab-626">Attribut DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="186ab-626">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="186ab-627">L’attribut `[DatabaseGenerated(DatabaseGeneratedOption.None)]` indique que la clé primaire est fournie par l’application plutôt que générée par la base de données.</span><span class="sxs-lookup"><span data-stu-id="186ab-627">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="186ab-628">Par défaut, EF Core part du principe que les valeurs de clé primaire sont générées par la base de données.</span><span class="sxs-lookup"><span data-stu-id="186ab-628">By default, EF Core assumes that PK values are generated by the DB.</span></span> <span data-ttu-id="186ab-629">Les valeurs de clé primaire générées par la base de données constituent en général la meilleure approche.</span><span class="sxs-lookup"><span data-stu-id="186ab-629">DB generated PK values is generally the best approach.</span></span> <span data-ttu-id="186ab-630">Pour les entités `Course`, l’utilisateur spécifie la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="186ab-630">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="186ab-631">Par exemple, un numéro de cours comme une série 1000 pour le département Mathématiques et une série 2000 pour le département Anglais.</span><span class="sxs-lookup"><span data-stu-id="186ab-631">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="186ab-632">Vous pouvez aussi utiliser l’attribut `DatabaseGenerated` pour générer des valeurs par défaut.</span><span class="sxs-lookup"><span data-stu-id="186ab-632">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="186ab-633">Par exemple, la base de données peut générer automatiquement un champ de date pour enregistrer la date de création ou de mise à jour d’une ligne.</span><span class="sxs-lookup"><span data-stu-id="186ab-633">For example, the DB can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="186ab-634">Pour plus d’informations, consultez [Propriétés générées](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="186ab-634">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="186ab-635">Propriétés de clé étrangère et de navigation</span><span class="sxs-lookup"><span data-stu-id="186ab-635">Foreign key and navigation properties</span></span>

<span data-ttu-id="186ab-636">Les propriétés de clé étrangère et les propriétés de navigation dans l’entité `Course` reflètent les relations suivantes :</span><span class="sxs-lookup"><span data-stu-id="186ab-636">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="186ab-637">Un cours étant affecté à un seul département, il y a une clé étrangère `DepartmentID` et une propriété de navigation `Department`.</span><span class="sxs-lookup"><span data-stu-id="186ab-637">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="186ab-638">Un cours pouvant avoir un nombre quelconque d’étudiants inscrits, la propriété de navigation `Enrollments` est une collection :</span><span class="sxs-lookup"><span data-stu-id="186ab-638">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="186ab-639">Un cours pouvant être animé par plusieurs formateurs, la propriété de navigation `CourseAssignments` est une collection :</span><span class="sxs-lookup"><span data-stu-id="186ab-639">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="186ab-640">`CourseAssignment` est expliquée [plus loin](#many-to-many-relationships).</span><span class="sxs-lookup"><span data-stu-id="186ab-640">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="create-the-department-entity"></a><span data-ttu-id="186ab-641">Créer l’entité Department</span><span class="sxs-lookup"><span data-stu-id="186ab-641">Create the Department entity</span></span>

![Entité de service](complex-data-model/_static/department-entity.png)

<span data-ttu-id="186ab-643">Créez *Models/Department.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="186ab-643">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="186ab-644">Attribut Column</span><span class="sxs-lookup"><span data-stu-id="186ab-644">The Column attribute</span></span>

<span data-ttu-id="186ab-645">Précédemment, vous avez utilisé l’attribut `Column` pour changer le mappage des noms de colonne.</span><span class="sxs-lookup"><span data-stu-id="186ab-645">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="186ab-646">Dans le code de l’entité `Department`, l’attribut `Column` est utilisé pour changer le mappage des types de données SQL.</span><span class="sxs-lookup"><span data-stu-id="186ab-646">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="186ab-647">La colonne `Budget` est définie à l’aide du type monétaire de SQL Server dans la base de données :</span><span class="sxs-lookup"><span data-stu-id="186ab-647">The `Budget` column is defined using the SQL Server money type in the DB:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="186ab-648">Le mappage de colonnes n’est généralement pas nécessaire.</span><span class="sxs-lookup"><span data-stu-id="186ab-648">Column mapping is generally not required.</span></span> <span data-ttu-id="186ab-649">EF Core choisit en général le type de données SQL Server approprié en fonction du type CLR de la propriété.</span><span class="sxs-lookup"><span data-stu-id="186ab-649">EF Core generally chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="186ab-650">Le type CLR `decimal` est mappé à un type SQL Server `decimal`.</span><span class="sxs-lookup"><span data-stu-id="186ab-650">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="186ab-651">`Budget` étant une valeur monétaire, le type de données money est plus approprié.</span><span class="sxs-lookup"><span data-stu-id="186ab-651">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="186ab-652">Propriétés de clé étrangère et de navigation</span><span class="sxs-lookup"><span data-stu-id="186ab-652">Foreign key and navigation properties</span></span>

<span data-ttu-id="186ab-653">Les propriétés de clé étrangère et de navigation reflètent les relations suivantes :</span><span class="sxs-lookup"><span data-stu-id="186ab-653">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="186ab-654">Un département peut avoir ou ne pas avoir un administrateur.</span><span class="sxs-lookup"><span data-stu-id="186ab-654">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="186ab-655">Un administrateur est toujours un formateur.</span><span class="sxs-lookup"><span data-stu-id="186ab-655">An administrator is always an instructor.</span></span> <span data-ttu-id="186ab-656">Ainsi, la propriété `InstructorID` est incluse en tant que clé étrangère de l’entité `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="186ab-656">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="186ab-657">La propriété de navigation se nomme `Administrator`, mais elle contient une entité `Instructor` :</span><span class="sxs-lookup"><span data-stu-id="186ab-657">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="186ab-658">Le point d’interrogation (?) dans le code précédent indique que la propriété est nullable.</span><span class="sxs-lookup"><span data-stu-id="186ab-658">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="186ab-659">Un département pouvant avoir de nombreux cours, il existe une propriété de navigation Courses :</span><span class="sxs-lookup"><span data-stu-id="186ab-659">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="186ab-660">Remarque : Par convention, EF Core autorise la suppression en cascade pour les clés étrangères non nullables et pour les relations plusieurs à plusieurs.</span><span class="sxs-lookup"><span data-stu-id="186ab-660">Note: By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="186ab-661">Les suppressions en cascade peuvent engendrer des règles de suppression en cascade circulaires.</span><span class="sxs-lookup"><span data-stu-id="186ab-661">Cascading delete can result in circular cascade delete rules.</span></span> <span data-ttu-id="186ab-662">Les règles de suppression en cascade circulaires provoquent une exception quand une migration est ajoutée.</span><span class="sxs-lookup"><span data-stu-id="186ab-662">Circular cascade delete rules causes an exception when a migration is added.</span></span>

<span data-ttu-id="186ab-663">Par exemple, si la propriété `Department.InstructorID` ne doit pas accepter les valeurs Null :</span><span class="sxs-lookup"><span data-stu-id="186ab-663">For example, if the `Department.InstructorID` property was defined as non-nullable:</span></span>

* <span data-ttu-id="186ab-664">EF Core configure une règle de suppression en cascade pour supprimer le service lorsque l’instructeur est supprimé.</span><span class="sxs-lookup"><span data-stu-id="186ab-664">EF Core configures a cascade delete rule to delete the department when the instructor is deleted.</span></span>
* <span data-ttu-id="186ab-665">La suppression du service lorsque l’instructeur est supprimé n’est pas le comportement souhaité.</span><span class="sxs-lookup"><span data-stu-id="186ab-665">Deleting the department when the instructor is deleted isn't the intended behavior.</span></span>
* <span data-ttu-id="186ab-666">L' [API Fluent](#fluent-api-alternative-to-attributes) suivante définit une règle de restriction au lieu de cascade.</span><span class="sxs-lookup"><span data-stu-id="186ab-666">The following [fluent API](#fluent-api-alternative-to-attributes) would set a restrict rule instead of cascade.</span></span>

   ```csharp
   modelBuilder.Entity<Department>()
      .HasOne(d => d.Administrator)
      .WithMany()
      .OnDelete(DeleteBehavior.Restrict)
  ```

<span data-ttu-id="186ab-667">Le code précédent désactive la suppression en cascade sur la relation formateur-département.</span><span class="sxs-lookup"><span data-stu-id="186ab-667">The preceding code disables cascade delete on the department-instructor relationship.</span></span>

## <a name="update-the-enrollment-entity"></a><span data-ttu-id="186ab-668">Mettre à jour l’entité Enrollment</span><span class="sxs-lookup"><span data-stu-id="186ab-668">Update the Enrollment entity</span></span>

<span data-ttu-id="186ab-669">Un enregistrement d’inscription correspond à un cours suivi par un étudiant.</span><span class="sxs-lookup"><span data-stu-id="186ab-669">An enrollment record is for one course taken by one student.</span></span>

![Entité Enrollment](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="186ab-671">Mettez à jour *Models/Enrollment.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="186ab-671">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="186ab-672">Propriétés de clé étrangère et de navigation</span><span class="sxs-lookup"><span data-stu-id="186ab-672">Foreign key and navigation properties</span></span>

<span data-ttu-id="186ab-673">Les propriétés de clé étrangère et de navigation reflètent les relations suivantes :</span><span class="sxs-lookup"><span data-stu-id="186ab-673">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="186ab-674">Un enregistrement d’inscription ne concernant qu’un seul cours, il y a une propriété de clé étrangère `CourseID` et une propriété de navigation `Course` :</span><span class="sxs-lookup"><span data-stu-id="186ab-674">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="186ab-675">Un enregistrement d’inscription ne concernant qu’un seul étudiant, il y a une propriété de clé étrangère `StudentID` et une propriété de navigation `Student` :</span><span class="sxs-lookup"><span data-stu-id="186ab-675">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="186ab-676">Relations plusieurs-à-plusieurs</span><span class="sxs-lookup"><span data-stu-id="186ab-676">Many-to-Many Relationships</span></span>

<span data-ttu-id="186ab-677">Il existe une relation plusieurs-à-plusieurs entre les entités `Student` et `Course`.</span><span class="sxs-lookup"><span data-stu-id="186ab-677">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="186ab-678">L’entité `Enrollment` joue le rôle de table de jointure plusieurs-à-plusieurs *avec charge utile* dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="186ab-678">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="186ab-679">« Avec charge utile » signifie que la table `Enrollment` contient des données supplémentaires en plus des clés étrangères pour les tables jointes (dans le cas présent, la clé primaire et `Grade`).</span><span class="sxs-lookup"><span data-stu-id="186ab-679">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="186ab-680">L’illustration suivante montre à quoi ressemblent ces relations dans un diagramme d’entité.</span><span class="sxs-lookup"><span data-stu-id="186ab-680">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="186ab-681">(Ce diagramme a été généré à l’aide [d’EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) pour EF 6. x.</span><span class="sxs-lookup"><span data-stu-id="186ab-681">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="186ab-682">Sa création ne fait pas partie de ce didacticiel.)</span><span class="sxs-lookup"><span data-stu-id="186ab-682">Creating the diagram isn't part of the tutorial.)</span></span>

![Relation plusieurs-à-plusieurs Student-Course](complex-data-model/_static/student-course.png)

<span data-ttu-id="186ab-684">Chaque ligne de relation comporte un 1 à une extrémité et un astérisque (\*) à l’autre, ce qui indique une relation un-à-plusieurs.</span><span class="sxs-lookup"><span data-stu-id="186ab-684">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="186ab-685">Si la table `Enrollment` n’incluait pas d’informations de notes, elle aurait uniquement besoin de contenir les deux clés étrangères (`CourseID` et `StudentID`).</span><span class="sxs-lookup"><span data-stu-id="186ab-685">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="186ab-686">Une table de jointure plusieurs-à-plusieurs sans charge utile est parfois appelée « table de jointure pure ».</span><span class="sxs-lookup"><span data-stu-id="186ab-686">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="186ab-687">Les entités `Instructor` et `Course` ont une relation plusieurs-à-plusieurs à l’aide d’une table de jointure pure.</span><span class="sxs-lookup"><span data-stu-id="186ab-687">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="186ab-688">Remarque : EF 6.x prend en charge les tables de jointure implicites pour les relations plusieurs-à-plusieurs, mais pas EF Core.</span><span class="sxs-lookup"><span data-stu-id="186ab-688">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="186ab-689">Pour plus d’informations, consultez [Many-to-many relationships in EF Core 2.0 (Relations plusieurs-à-plusieurs dans EF Core 2.0)](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span><span class="sxs-lookup"><span data-stu-id="186ab-689">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="186ab-690">Entité CourseAssignment</span><span class="sxs-lookup"><span data-stu-id="186ab-690">The CourseAssignment entity</span></span>

![Entité CourseAssignment](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="186ab-692">Créez *Models/CourseAssignment.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="186ab-692">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a><span data-ttu-id="186ab-693">Instructor-Courses</span><span class="sxs-lookup"><span data-stu-id="186ab-693">Instructor-to-Courses</span></span>

![Instructor-to-Courses m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="186ab-695">La relation plusieurs-à-plusieurs Instructor-Courses :</span><span class="sxs-lookup"><span data-stu-id="186ab-695">The Instructor-to-Courses many-to-many relationship:</span></span>

* <span data-ttu-id="186ab-696">Nécessite une table de jointure qui doit être représentée par un jeu d’entités.</span><span class="sxs-lookup"><span data-stu-id="186ab-696">Requires a join table that must be represented by an entity set.</span></span>
* <span data-ttu-id="186ab-697">Est une table de jointure pure (table sans charge utile).</span><span class="sxs-lookup"><span data-stu-id="186ab-697">Is a pure join table (table without payload).</span></span>

<span data-ttu-id="186ab-698">Il est courant de nommer une entité de jointure `EntityName1EntityName2`.</span><span class="sxs-lookup"><span data-stu-id="186ab-698">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="186ab-699">Par exemple, la table de jointure Instructor-to-Courses utilisant ce modèle est `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="186ab-699">For example, the Instructor-to-Courses join table using this pattern is `CourseInstructor`.</span></span> <span data-ttu-id="186ab-700">Toutefois, nous vous recommandons d’utiliser un nom qui décrit la relation.</span><span class="sxs-lookup"><span data-stu-id="186ab-700">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="186ab-701">Les modèles de données sont simples au début, puis ils augmentent en complexité.</span><span class="sxs-lookup"><span data-stu-id="186ab-701">Data models start out simple and grow.</span></span> <span data-ttu-id="186ab-702">Les jointures sans charge utile (tables de jointure pures) évoluent fréquemment pour inclure une charge utile.</span><span class="sxs-lookup"><span data-stu-id="186ab-702">No-payload joins (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="186ab-703">En commençant par un nom d’entité descriptif, vous n’aurez pas besoin de modifier le nom quand la table de jointure changera.</span><span class="sxs-lookup"><span data-stu-id="186ab-703">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="186ab-704">Dans l’idéal, l’entité de jointure aura son propre nom (éventuellement un mot unique) naturel dans le domaine d’entreprise.</span><span class="sxs-lookup"><span data-stu-id="186ab-704">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="186ab-705">Par exemple, les livres et les clients pourraient être liés avec une entité de jointure nommée Évaluations.</span><span class="sxs-lookup"><span data-stu-id="186ab-705">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="186ab-706">Pour la relation plusieurs-à-plusieurs Instructor-Courses, il vaut mieux utiliser `CourseAssignment` que `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="186ab-706">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="186ab-707">Clé composite</span><span class="sxs-lookup"><span data-stu-id="186ab-707">Composite key</span></span>

<span data-ttu-id="186ab-708">Les clés étrangères ne sont pas nullables.</span><span class="sxs-lookup"><span data-stu-id="186ab-708">FKs are not nullable.</span></span> <span data-ttu-id="186ab-709">Ensemble, le deux clés étrangères dans `CourseAssignment` (`InstructorID` et `CourseID`) identifient de façon unique chaque ligne de la table `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="186ab-709">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="186ab-710">`CourseAssignment` ne nécessite pas de clé primaire dédiée.</span><span class="sxs-lookup"><span data-stu-id="186ab-710">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="186ab-711">Les propriétés `InstructorID` et `CourseID` fonctionnent comme une clé primaire composite.</span><span class="sxs-lookup"><span data-stu-id="186ab-711">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="186ab-712">Le seul moyen de spécifier des clés primaires composites dans EF Core consiste à faire appel à l’*API Fluent*.</span><span class="sxs-lookup"><span data-stu-id="186ab-712">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="186ab-713">La section suivante montre comment configurer la clé primaire composite.</span><span class="sxs-lookup"><span data-stu-id="186ab-713">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="186ab-714">La clé composite garantit que :</span><span class="sxs-lookup"><span data-stu-id="186ab-714">The composite key ensures:</span></span>

* <span data-ttu-id="186ab-715">Plusieurs lignes sont autorisées pour un cours.</span><span class="sxs-lookup"><span data-stu-id="186ab-715">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="186ab-716">Plusieurs lignes sont autorisées pour un formateur.</span><span class="sxs-lookup"><span data-stu-id="186ab-716">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="186ab-717">Plusieurs lignes pour la même paire formateur-cours ne sont pas autorisées.</span><span class="sxs-lookup"><span data-stu-id="186ab-717">Multiple rows for the same instructor and course isn't allowed.</span></span>

<span data-ttu-id="186ab-718">Comme l’entité de jointure `Enrollment` définit sa propre clé primaire, des doublons de ce type sont possibles.</span><span class="sxs-lookup"><span data-stu-id="186ab-718">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="186ab-719">Pour éviter ces doublons :</span><span class="sxs-lookup"><span data-stu-id="186ab-719">To prevent such duplicates:</span></span>

* <span data-ttu-id="186ab-720">Ajoutez un index unique sur les champs de clé primaire, ou</span><span class="sxs-lookup"><span data-stu-id="186ab-720">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="186ab-721">Configurez `Enrollment` avec une clé primaire composite similaire à `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="186ab-721">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="186ab-722">Pour plus d’informations, consultez [Index](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="186ab-722">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-db-context"></a><span data-ttu-id="186ab-723">Mettre à jour le contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="186ab-723">Update the DB context</span></span>

<span data-ttu-id="186ab-724">Ajoutez le code en surbrillance suivant à *Data/SchoolContext.cs* :</span><span class="sxs-lookup"><span data-stu-id="186ab-724">Add the following highlighted code to *Data/SchoolContext.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="186ab-725">Le code précédent ajoute les nouvelles entités et configure la clé primaire composite de l’entité `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="186ab-725">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="186ab-726">Alternative d’API Fluent aux attributs</span><span class="sxs-lookup"><span data-stu-id="186ab-726">Fluent API alternative to attributes</span></span>

<span data-ttu-id="186ab-727">La méthode `OnModelCreating` du code précédent utilise l’*API Fluent* pour configurer le comportement d’EF Core.</span><span class="sxs-lookup"><span data-stu-id="186ab-727">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="186ab-728">L’API est appelée « Fluent », car elle est souvent utilisée en enchaînant une série d’appels de méthode en une seule instruction.</span><span class="sxs-lookup"><span data-stu-id="186ab-728">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="186ab-729">Le [code suivant](/ef/core/modeling/#use-fluent-api-to-configure-a-model) est un exemple de l’API Fluent :</span><span class="sxs-lookup"><span data-stu-id="186ab-729">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="186ab-730">Dans ce didacticiel, l’API Fluent est utilisée uniquement pour le mappage de base de données qui ne peut pas être effectué avec des attributs.</span><span class="sxs-lookup"><span data-stu-id="186ab-730">In this tutorial, the fluent API is used only for DB mapping that can't be done with attributes.</span></span> <span data-ttu-id="186ab-731">Toutefois, l’API Fluent peut spécifier la plupart des règles de mise en forme, de validation et de mappage pouvant être spécifiées à l’aide d’attributs.</span><span class="sxs-lookup"><span data-stu-id="186ab-731">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="186ab-732">Certains attributs, tels que `MinimumLength`, ne peuvent pas être appliqués avec l’API Fluent.</span><span class="sxs-lookup"><span data-stu-id="186ab-732">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="186ab-733">`MinimumLength` ne change pas le schéma. Il applique uniquement une règle de validation de longueur minimale.</span><span class="sxs-lookup"><span data-stu-id="186ab-733">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="186ab-734">Certains développeurs préfèrent utiliser exclusivement l’API Fluent afin de conserver des classes d’entité « propres ».</span><span class="sxs-lookup"><span data-stu-id="186ab-734">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="186ab-735">Vous pouvez combiner des attributs et l’API Fluent.</span><span class="sxs-lookup"><span data-stu-id="186ab-735">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="186ab-736">Certaines configurations peuvent être effectuées uniquement avec l’API Fluent (spécification d’une clé primaire composite).</span><span class="sxs-lookup"><span data-stu-id="186ab-736">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="186ab-737">Certaines autres peuvent être effectuées uniquement avec des attributs (`MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="186ab-737">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="186ab-738">Voici ce que nous recommandons pour l’utilisation des API Fluent ou des attributs :</span><span class="sxs-lookup"><span data-stu-id="186ab-738">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="186ab-739">Choisissez l’une de ces deux approches.</span><span class="sxs-lookup"><span data-stu-id="186ab-739">Choose one of these two approaches.</span></span>
* <span data-ttu-id="186ab-740">Dans la mesure du possible, utilisez l’approche choisie de manière cohérente.</span><span class="sxs-lookup"><span data-stu-id="186ab-740">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="186ab-741">Certains des attributs utilisés dans ce didacticiel sont utilisés pour :</span><span class="sxs-lookup"><span data-stu-id="186ab-741">Some of the attributes used in the this tutorial are used for:</span></span>

* <span data-ttu-id="186ab-742">La validation uniquement (par exemple, `MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="186ab-742">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="186ab-743">La configuration d’EF Core uniquement (par exemple, `HasKey`).</span><span class="sxs-lookup"><span data-stu-id="186ab-743">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="186ab-744">La validation et la configuration d’EF Core (par exemple, `[StringLength(50)]`).</span><span class="sxs-lookup"><span data-stu-id="186ab-744">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="186ab-745">Pour plus d’informations sur les attributs et l’API Fluent, consultez [Méthodes de configuration](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="186ab-745">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="186ab-746">Diagramme des entités montrant les relations</span><span class="sxs-lookup"><span data-stu-id="186ab-746">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="186ab-747">L’illustration suivante montre le diagramme que les outils EF Power créent pour le modèle School complet.</span><span class="sxs-lookup"><span data-stu-id="186ab-747">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![Diagramme des entités](complex-data-model/_static/diagram.png)

<span data-ttu-id="186ab-749">Le schéma précédent illustre :</span><span class="sxs-lookup"><span data-stu-id="186ab-749">The preceding diagram shows:</span></span>

* <span data-ttu-id="186ab-750">Plusieurs lignes de relations un-à-plusieurs (1 à \*).</span><span class="sxs-lookup"><span data-stu-id="186ab-750">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="186ab-751">La ligne de relation un-à-zéro-ou-un (1 à 0..1) entre les entités `Instructor` et `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="186ab-751">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="186ab-752">La ligne de relation zéro-ou-un-à-plusieurs (0..1 à \*) entre les entités `Instructor` et `Department`.</span><span class="sxs-lookup"><span data-stu-id="186ab-752">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-db-with-test-data"></a><span data-ttu-id="186ab-753">Amorcer la base de données avec des données de test</span><span class="sxs-lookup"><span data-stu-id="186ab-753">Seed the DB with Test Data</span></span>

<span data-ttu-id="186ab-754">Mettez à jour le code dans *Data/DbInitializer.cs* :</span><span class="sxs-lookup"><span data-stu-id="186ab-754">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="186ab-755">Le code précédent fournit des données de valeur initiale pour les nouvelles entités.</span><span class="sxs-lookup"><span data-stu-id="186ab-755">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="186ab-756">La majeure partie de ce code crée des objets d’entités et charge des exemples de données.</span><span class="sxs-lookup"><span data-stu-id="186ab-756">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="186ab-757">Les exemples de données sont utilisés à des fins de test.</span><span class="sxs-lookup"><span data-stu-id="186ab-757">The sample data is used for testing.</span></span> <span data-ttu-id="186ab-758">Consultez `Enrollments` et `CourseAssignments` pour obtenir des exemples de la façon dont des tables de jointure plusieurs-à-plusieurs peuvent être amorcées.</span><span class="sxs-lookup"><span data-stu-id="186ab-758">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="186ab-759">Ajouter une migration</span><span class="sxs-lookup"><span data-stu-id="186ab-759">Add a migration</span></span>

<span data-ttu-id="186ab-760">Créez le projet.</span><span class="sxs-lookup"><span data-stu-id="186ab-760">Build the project.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="186ab-761">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="186ab-761">Visual Studio</span></span>](#tab/visual-studio)

```powershell
Add-Migration ComplexDataModel
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="186ab-762">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="186ab-762">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

---

<span data-ttu-id="186ab-763">La commande précédente affiche un avertissement concernant les pertes de données possibles.</span><span class="sxs-lookup"><span data-stu-id="186ab-763">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="186ab-764">Si la commande `database update` est exécutée, l’erreur suivante se produit :</span><span class="sxs-lookup"><span data-stu-id="186ab-764">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

## <a name="apply-the-migration"></a><span data-ttu-id="186ab-765">Appliquer la migration</span><span class="sxs-lookup"><span data-stu-id="186ab-765">Apply the migration</span></span>

<span data-ttu-id="186ab-766">Disposant à présent d’une base de données, vous devez réfléchir à la façon dont vous y apporterez des modifications.</span><span class="sxs-lookup"><span data-stu-id="186ab-766">Now that you have an existing database, you need to think about how to apply future changes to it.</span></span> <span data-ttu-id="186ab-767">Ce tutoriel montre deux approches :</span><span class="sxs-lookup"><span data-stu-id="186ab-767">This tutorial shows two approaches:</span></span>

* [<span data-ttu-id="186ab-768">Supprimer et recréer la base de données</span><span class="sxs-lookup"><span data-stu-id="186ab-768">Drop and re-create the database</span></span>](#drop)
* <span data-ttu-id="186ab-769">[Appliquez la migration à la base de données existante](#applyexisting).</span><span class="sxs-lookup"><span data-stu-id="186ab-769">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="186ab-770">Bien que cette méthode soit plus longue et complexe, elle constitue l’approche privilégiée pour les environnements de production réels.</span><span class="sxs-lookup"><span data-stu-id="186ab-770">While this method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> <span data-ttu-id="186ab-771">**Remarque** : Cette section du tutoriel est facultative.</span><span class="sxs-lookup"><span data-stu-id="186ab-771">**Note**: This is an optional section of the tutorial.</span></span> <span data-ttu-id="186ab-772">Vous pouvez effectuer les étapes de suppression et de recréation et ignorer cette section.</span><span class="sxs-lookup"><span data-stu-id="186ab-772">You can do the drop and re-create steps and skip this section.</span></span> <span data-ttu-id="186ab-773">Si vous souhaitez suivre les étapes décrites dans cette section, n’effectuez pas les étapes de suppression et de recréation.</span><span class="sxs-lookup"><span data-stu-id="186ab-773">If you do want to follow the steps in this section, don't do the drop and re-create steps.</span></span> 

<a name="drop"></a>

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="186ab-774">Supprimer et recréer la base de données</span><span class="sxs-lookup"><span data-stu-id="186ab-774">Drop and re-create the database</span></span>

<span data-ttu-id="186ab-775">Le code dans le `DbInitializer` mis à jour ajoute des données de valeur initiale pour les nouvelles entités.</span><span class="sxs-lookup"><span data-stu-id="186ab-775">The code in the updated `DbInitializer` adds seed data for the new entities.</span></span> <span data-ttu-id="186ab-776">Pour forcer EF Core à créer une autre base de données, supprimez et mettez à jour la base de données :</span><span class="sxs-lookup"><span data-stu-id="186ab-776">To force EF Core to create a new  DB, drop and update the DB:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="186ab-777">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="186ab-777">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="186ab-778">Dans la **console du Gestionnaire de package**, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="186ab-778">In the **Package Manager Console** (PMC), run the following command:</span></span>

```powershell
Drop-Database
Update-Database
```

<span data-ttu-id="186ab-779">Exécutez `Get-Help about_EntityFrameworkCore` à partir de la console du Gestionnaire de package pour obtenir des informations d’aide.</span><span class="sxs-lookup"><span data-stu-id="186ab-779">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="186ab-780">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="186ab-780">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="186ab-781">Ouvrez une fenêtre de commande et accédez au dossier du projet.</span><span class="sxs-lookup"><span data-stu-id="186ab-781">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="186ab-782">Le dossier du projet contient le fichier *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="186ab-782">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="186ab-783">Entrez ce qui suit dans la fenêtre de commande :</span><span class="sxs-lookup"><span data-stu-id="186ab-783">Enter the following in the command window:</span></span>

```dotnetcli
dotnet ef database drop
dotnet ef database update
```

---

<span data-ttu-id="186ab-784">Exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="186ab-784">Run the app.</span></span> <span data-ttu-id="186ab-785">L’exécution de l’application entraîne l’exécution de la méthode `DbInitializer.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="186ab-785">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="186ab-786">La méthode `DbInitializer.Initialize` remplit la nouvelle base de données.</span><span class="sxs-lookup"><span data-stu-id="186ab-786">The `DbInitializer.Initialize` populates the new DB.</span></span>

<span data-ttu-id="186ab-787">Ouvrez la base de données dans SSOX :</span><span class="sxs-lookup"><span data-stu-id="186ab-787">Open the DB in SSOX:</span></span>

* <span data-ttu-id="186ab-788">Si SSOX était déjà ouvert, cliquez sur le bouton **Actualiser**.</span><span class="sxs-lookup"><span data-stu-id="186ab-788">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="186ab-789">Développez le nœud **Tables**.</span><span class="sxs-lookup"><span data-stu-id="186ab-789">Expand the **Tables** node.</span></span> <span data-ttu-id="186ab-790">Les tables créées sont affichées.</span><span class="sxs-lookup"><span data-stu-id="186ab-790">The created tables are displayed.</span></span>

![Tables dans SSOX](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="186ab-792">Examinez la table **CourseAssignment** :</span><span class="sxs-lookup"><span data-stu-id="186ab-792">Examine the **CourseAssignment** table:</span></span>

* <span data-ttu-id="186ab-793">Cliquez avec le bouton droit sur la table **CourseAssignment** et sélectionnez **Afficher les données**.</span><span class="sxs-lookup"><span data-stu-id="186ab-793">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
* <span data-ttu-id="186ab-794">Vérifiez que la table **CourseAssignment** contient des données.</span><span class="sxs-lookup"><span data-stu-id="186ab-794">Verify the **CourseAssignment** table contains data.</span></span>

![Données CourseAssignment dans SSOX](complex-data-model/_static/ssox-ci-data.png)

<a name="applyexisting"></a>

### <a name="apply-the-migration-to-the-existing-database"></a><span data-ttu-id="186ab-796">Appliquer la migration à la base de données</span><span class="sxs-lookup"><span data-stu-id="186ab-796">Apply the migration to the existing database</span></span>

<span data-ttu-id="186ab-797">Cette section est facultative.</span><span class="sxs-lookup"><span data-stu-id="186ab-797">This section is optional.</span></span> <span data-ttu-id="186ab-798">Ces étapes fonctionnent seulement si vous avez ignoré la section [Supprimer et recréer la base de données](#drop) précédente.</span><span class="sxs-lookup"><span data-stu-id="186ab-798">These steps work only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="186ab-799">Quand des migrations sont exécutées avec des données existantes, il peut y avoir des contraintes de clé étrangère qui ne sont pas satisfaites avec les données existantes.</span><span class="sxs-lookup"><span data-stu-id="186ab-799">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="186ab-800">Avec des données de production, vous devez effectuer certaines actions pour migrer les données existantes.</span><span class="sxs-lookup"><span data-stu-id="186ab-800">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="186ab-801">Cette section fournit un exemple de correction des violations de contraintes de clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="186ab-801">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="186ab-802">N’effectuez pas ces modifications de code sans une sauvegarde.</span><span class="sxs-lookup"><span data-stu-id="186ab-802">Don't make these code changes without a backup.</span></span> <span data-ttu-id="186ab-803">N’effectuez pas ces modifications de code si vous avez terminé la section précédente et mis à jour la base de données.</span><span class="sxs-lookup"><span data-stu-id="186ab-803">Don't make these code changes if you completed the previous section and updated the database.</span></span>

<span data-ttu-id="186ab-804">Le fichier *{horodatage}_ComplexDataModel.cs* contient le code suivant :</span><span class="sxs-lookup"><span data-stu-id="186ab-804">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="186ab-805">Le code précédent ajoute une clé étrangère `DepartmentID` non-nullable à la table `Course`.</span><span class="sxs-lookup"><span data-stu-id="186ab-805">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="186ab-806">La base de données du didacticiel précédent contient des lignes dans `Course` ; cette table ne peut donc pas être mise à jour par des migrations.</span><span class="sxs-lookup"><span data-stu-id="186ab-806">The DB from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="186ab-807">Pour faire en sorte que la migration `ComplexDataModel` fonctionne avec des données existantes</span><span class="sxs-lookup"><span data-stu-id="186ab-807">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="186ab-808">Modifiez le code pour affecter une valeur par défaut à la nouvelle colonne (`DepartmentID`).</span><span class="sxs-lookup"><span data-stu-id="186ab-808">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="186ab-809">Créez un département fictif nommé « Temp » assumant la fonction de département par défaut.</span><span class="sxs-lookup"><span data-stu-id="186ab-809">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="186ab-810">Corriger les contraintes de clé étrangère</span><span class="sxs-lookup"><span data-stu-id="186ab-810">Fix the foreign key constraints</span></span>

<span data-ttu-id="186ab-811">Mettez à jour la méthode `Up` de la classe `ComplexDataModel` :</span><span class="sxs-lookup"><span data-stu-id="186ab-811">Update the `ComplexDataModel` classes `Up` method:</span></span>

* <span data-ttu-id="186ab-812">Ouvrez le fichier *{timestamp}_ComplexDataModel.cs*.</span><span class="sxs-lookup"><span data-stu-id="186ab-812">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="186ab-813">Commentez la ligne de code qui ajoute la colonne `DepartmentID` à la table `Course`.</span><span class="sxs-lookup"><span data-stu-id="186ab-813">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="186ab-814">Ajoutez le code en surbrillance suivant.</span><span class="sxs-lookup"><span data-stu-id="186ab-814">Add the following highlighted code.</span></span> <span data-ttu-id="186ab-815">Le nouveau code va après le bloc `.CreateTable( name: "Department"` :</span><span class="sxs-lookup"><span data-stu-id="186ab-815">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="186ab-816">Avec les modifications précédentes, les lignes `Course` existantes seront liées au service « Temp » après l’exécution de la méthode `ComplexDataModel` `Up`.</span><span class="sxs-lookup"><span data-stu-id="186ab-816">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel` `Up` method runs.</span></span>

<span data-ttu-id="186ab-817">Une application de production :</span><span class="sxs-lookup"><span data-stu-id="186ab-817">A production app would:</span></span>

* <span data-ttu-id="186ab-818">Comprendrait du code ou des scripts pour ajouter des lignes `Department` et des lignes `Course` associées aux nouvelles lignes `Department`.</span><span class="sxs-lookup"><span data-stu-id="186ab-818">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="186ab-819">N’utiliserait pas le département « Temp » ou la valeur par défaut pour `Course.DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="186ab-819">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

<span data-ttu-id="186ab-820">Le didacticiel suivant traite des données associées.</span><span class="sxs-lookup"><span data-stu-id="186ab-820">The next tutorial covers related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="186ab-821">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="186ab-821">Additional resources</span></span>

* [<span data-ttu-id="186ab-822">Version YouTube de ce tutoriel(Partie 1)</span><span class="sxs-lookup"><span data-stu-id="186ab-822">YouTube version of this tutorial(Part 1)</span></span>](https://www.youtube.com/watch?v=0n2f0ObgCoA)
* [<span data-ttu-id="186ab-823">Version YouTube de ce tutoriel(Partie 2)</span><span class="sxs-lookup"><span data-stu-id="186ab-823">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=Je0Z5K1TNmY)

> [!div class="step-by-step"]
> <span data-ttu-id="186ab-824">[Précédent](xref:data/ef-rp/migrations) 
>  [Suivant](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="186ab-824">[Previous](xref:data/ef-rp/migrations)
[Next](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end
