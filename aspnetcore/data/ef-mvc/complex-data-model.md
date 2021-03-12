---
title: 'Didacticiel : créer un modèle de données complexe-ASP.NET MVC avec EF Core'
description: Dans ce tutoriel, vous ajoutez des entités et des relations, et vous personnalisez le modèle de données en spécifiant des règles de mise en forme, de validation et de mappage.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
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
uid: data/ef-mvc/complex-data-model
ms.openlocfilehash: a881ff28d41a272ade559c60efbd884f2a3c4e3e
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587786"
---
# <a name="tutorial-create-a-complex-data-model---aspnet-mvc-with-ef-core"></a><span data-ttu-id="744a9-103">Didacticiel : créer un modèle de données complexe-ASP.NET MVC avec EF Core</span><span class="sxs-lookup"><span data-stu-id="744a9-103">Tutorial: Create a complex data model - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="744a9-104">Dans les didacticiels précédents, vous avez travaillé avec un modèle de données simple composé de trois entités.</span><span class="sxs-lookup"><span data-stu-id="744a9-104">In the previous tutorials, you worked with a simple data model that was composed of three entities.</span></span> <span data-ttu-id="744a9-105">Dans ce didacticiel, vous allez ajouter des entités et des relations, et vous personnaliserez le modèle de données en spécifiant des règles de mise en forme, de validation et de mappage de base de données.</span><span class="sxs-lookup"><span data-stu-id="744a9-105">In this tutorial, you'll add more entities and relationships and you'll customize the data model by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="744a9-106">Lorsque vous aurez terminé, les classes d’entité composeront le modèle de données complet indiqué dans l’illustration suivante :</span><span class="sxs-lookup"><span data-stu-id="744a9-106">When you're finished, the entity classes will make up the completed data model that's shown in the following illustration:</span></span>

![Diagramme des entités](complex-data-model/_static/diagram.png)

<span data-ttu-id="744a9-108">Dans ce tutoriel, vous allez :</span><span class="sxs-lookup"><span data-stu-id="744a9-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="744a9-109">Personnaliser le modèle de données</span><span class="sxs-lookup"><span data-stu-id="744a9-109">Customize the Data model</span></span>
> * <span data-ttu-id="744a9-110">Apporter des modifications à l’entité Student</span><span class="sxs-lookup"><span data-stu-id="744a9-110">Make changes to Student entity</span></span>
> * <span data-ttu-id="744a9-111">Créer une entité Instructor</span><span class="sxs-lookup"><span data-stu-id="744a9-111">Create Instructor entity</span></span>
> * <span data-ttu-id="744a9-112">Créer une entité OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="744a9-112">Create OfficeAssignment entity</span></span>
> * <span data-ttu-id="744a9-113">Modifier l’entité Course</span><span class="sxs-lookup"><span data-stu-id="744a9-113">Modify Course entity</span></span>
> * <span data-ttu-id="744a9-114">Créer l’entité Department</span><span class="sxs-lookup"><span data-stu-id="744a9-114">Create Department entity</span></span>
> * <span data-ttu-id="744a9-115">Modifier l’entité Enrollment</span><span class="sxs-lookup"><span data-stu-id="744a9-115">Modify Enrollment entity</span></span>
> * <span data-ttu-id="744a9-116">Mettre à jour le contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="744a9-116">Update the database context</span></span>
> * <span data-ttu-id="744a9-117">Remplir la base de données avec des données de test</span><span class="sxs-lookup"><span data-stu-id="744a9-117">Seed database with test data</span></span>
> * <span data-ttu-id="744a9-118">Ajouter une migration</span><span class="sxs-lookup"><span data-stu-id="744a9-118">Add a migration</span></span>
> * <span data-ttu-id="744a9-119">Changer la chaîne de connexion</span><span class="sxs-lookup"><span data-stu-id="744a9-119">Change the connection string</span></span>
> * <span data-ttu-id="744a9-120">Mettre à jour la base de données</span><span class="sxs-lookup"><span data-stu-id="744a9-120">Update the database</span></span>

## <a name="prerequisites"></a><span data-ttu-id="744a9-121">Prérequis</span><span class="sxs-lookup"><span data-stu-id="744a9-121">Prerequisites</span></span>

* [<span data-ttu-id="744a9-122">À l’aide de migrations d’EF Core</span><span class="sxs-lookup"><span data-stu-id="744a9-122">Using EF Core migrations</span></span>](migrations.md)

## <a name="customize-the-data-model"></a><span data-ttu-id="744a9-123">Personnaliser le modèle de données</span><span class="sxs-lookup"><span data-stu-id="744a9-123">Customize the Data model</span></span>

<span data-ttu-id="744a9-124">Dans cette section, vous allez apprendre à personnaliser le modèle de données en utilisant des attributs qui spécifient des règles de mise en forme, de validation et de mappage de base de données.</span><span class="sxs-lookup"><span data-stu-id="744a9-124">In this section you'll see how to customize the data model by using attributes that specify formatting, validation, and database mapping rules.</span></span> <span data-ttu-id="744a9-125">Ensuite, dans plusieurs des sections suivantes, vous allez créer le modèle de données School complet en ajoutant des attributs aux classes que vous avez déjà créées et en créant de nouvelles classes pour les autres types d’entités dans le modèle.</span><span class="sxs-lookup"><span data-stu-id="744a9-125">Then in several of the following sections you'll create the complete School data model by adding attributes to the classes you already created and creating new classes for the remaining entity types in the model.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="744a9-126">Attribut DataType</span><span class="sxs-lookup"><span data-stu-id="744a9-126">The DataType attribute</span></span>

<span data-ttu-id="744a9-127">Pour les dates d’inscription des étudiants, toutes les pages web affichent l’heure avec la date, alors que seule la date vous intéresse dans ce champ.</span><span class="sxs-lookup"><span data-stu-id="744a9-127">For student enrollment dates, all of the web pages currently display the time along with the date, although all you care about for this field is the date.</span></span> <span data-ttu-id="744a9-128">Vous pouvez avoir recours aux attributs d’annotation de données pour apporter une modification au code, permettant de corriger le format d’affichage dans chaque vue qui affiche ces données.</span><span class="sxs-lookup"><span data-stu-id="744a9-128">By using data annotation attributes, you can make one code change that will fix the display format in every view that shows the data.</span></span> <span data-ttu-id="744a9-129">Pour voir un exemple de la procédure à suivre, vous allez ajouter un attribut à la propriété `EnrollmentDate` dans la classe `Student`.</span><span class="sxs-lookup"><span data-stu-id="744a9-129">To see an example of how to do that, you'll add an attribute to the `EnrollmentDate` property in the `Student` class.</span></span>

<span data-ttu-id="744a9-130">Dans *Models/Student.cs*, ajoutez une instruction `using` pour l’espace de noms `System.ComponentModel.DataAnnotations` et ajoutez les attributs `DataType` et `DisplayFormat` à la propriété `EnrollmentDate`, comme indiqué dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="744a9-130">In *Models/Student.cs*, add a `using` statement for the `System.ComponentModel.DataAnnotations` namespace and add `DataType` and `DisplayFormat` attributes to the `EnrollmentDate` property, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="744a9-131">L’attribut `DataType` sert à spécifier un type de données qui est plus spécifique que le type intrinsèque de la base de données.</span><span class="sxs-lookup"><span data-stu-id="744a9-131">The `DataType` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="744a9-132">Dans le cas présent, nous voulons uniquement effectuer le suivi de la date, pas de la date et de l’heure.</span><span class="sxs-lookup"><span data-stu-id="744a9-132">In this case we only want to keep track of the date, not the date and time.</span></span> <span data-ttu-id="744a9-133">L’énumération `DataType` fournit de nombreux types de données, tels que Date, Time, PhoneNumber, Currency, EmailAddress, etc.</span><span class="sxs-lookup"><span data-stu-id="744a9-133">The  `DataType` Enumeration provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, and more.</span></span> <span data-ttu-id="744a9-134">L’attribut `DataType` peut également permettre à l’application de fournir automatiquement des fonctionnalités propres au type.</span><span class="sxs-lookup"><span data-stu-id="744a9-134">The `DataType` attribute can also enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="744a9-135">Par exemple, vous pouvez créer un lien `mailto:` pour `DataType.EmailAddress`, et vous pouvez fournir un sélecteur de date pour `DataType.Date` dans les navigateurs qui prennent en charge HTML5.</span><span class="sxs-lookup"><span data-stu-id="744a9-135">For example, a `mailto:` link can be created for `DataType.EmailAddress`, and a date selector can be provided for `DataType.Date` in browsers that support HTML5.</span></span> <span data-ttu-id="744a9-136">L’attribut `DataType` émet des attributs HTML 5 `data-` compréhensibles par les navigateurs HTML 5.</span><span class="sxs-lookup"><span data-stu-id="744a9-136">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers can understand.</span></span> <span data-ttu-id="744a9-137">Les attributs `DataType` ne fournissent aucune validation.</span><span class="sxs-lookup"><span data-stu-id="744a9-137">The `DataType` attributes don't provide any validation.</span></span>

<span data-ttu-id="744a9-138">`DataType.Date` ne spécifie pas le format de la date qui s’affiche.</span><span class="sxs-lookup"><span data-stu-id="744a9-138">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="744a9-139">Par défaut, le champ de données est affiché conformément aux formats par défaut basés sur l’objet CultureInfo du serveur.</span><span class="sxs-lookup"><span data-stu-id="744a9-139">By default, the data field is displayed according to the default formats based on the server's CultureInfo.</span></span>

<span data-ttu-id="744a9-140">L’attribut `DisplayFormat` est utilisé pour spécifier explicitement le format de date :</span><span class="sxs-lookup"><span data-stu-id="744a9-140">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="744a9-141">Le paramètre `ApplyFormatInEditMode` indique que la mise en forme doit également être appliquée quand la valeur est affichée dans une zone de texte à des fins de modification.</span><span class="sxs-lookup"><span data-stu-id="744a9-141">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied when the value is displayed in a text box for editing.</span></span> <span data-ttu-id="744a9-142">(Ceci peut ne pas être souhaitable pour certains champs ; par exemple, pour les valeurs monétaires, vous ne souhaiterez peut-être pas que le symbole monétaire figure dans la zone de texte.)</span><span class="sxs-lookup"><span data-stu-id="744a9-142">(You might not want that for some fields -- for example, for currency values, you might not want the currency symbol in the text box for editing.)</span></span>

<span data-ttu-id="744a9-143">Vous pouvez utiliser l’attribut `DisplayFormat` seul, mais il est généralement judicieux d’utiliser également l’attribut `DataType`.</span><span class="sxs-lookup"><span data-stu-id="744a9-143">You can use the `DisplayFormat` attribute by itself, but it's generally a good idea to use the `DataType` attribute also.</span></span> <span data-ttu-id="744a9-144">L’attribut `DataType` donne la sémantique des données au lieu d’expliquer comment les afficher à l’écran. Il présente, par ailleurs, les avantages suivants, dont vous ne bénéficiez pas avec `DisplayFormat` :</span><span class="sxs-lookup"><span data-stu-id="744a9-144">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen, and provides the following benefits that you don't get with `DisplayFormat`:</span></span>

* <span data-ttu-id="744a9-145">Le navigateur peut activer des fonctionnalités HTML5 (par exemple pour afficher un contrôle de calendrier, le symbole monétaire correspondant aux paramètres régionaux, des liens de messagerie, une certaine validation des entrées côté client, etc.).</span><span class="sxs-lookup"><span data-stu-id="744a9-145">The browser can enable HTML5 features (for example to show a calendar control, the locale-appropriate currency symbol, email links, some client-side input validation, etc.).</span></span>

* <span data-ttu-id="744a9-146">Par défaut, le navigateur affiche les données à l’aide du format correspondant à vos paramètres régionaux.</span><span class="sxs-lookup"><span data-stu-id="744a9-146">By default, the browser will render data using the correct format based on your locale.</span></span>

<span data-ttu-id="744a9-147">Pour plus d’informations, consultez la [ \<input> documentation tag Helper](../../mvc/views/working-with-forms.md#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="744a9-147">For more information, see the [\<input> tag helper documentation](../../mvc/views/working-with-forms.md#the-input-tag-helper).</span></span>

<span data-ttu-id="744a9-148">Exécutez l’application, accédez à la page d’index des étudiants et notez que les heures ne sont plus affichées pour les dates d’inscription.</span><span class="sxs-lookup"><span data-stu-id="744a9-148">Run the app, go to the Students Index page and notice that times are no longer displayed for the enrollment dates.</span></span> <span data-ttu-id="744a9-149">La même chose est vraie pour toute vue qui utilise le modèle Student.</span><span class="sxs-lookup"><span data-stu-id="744a9-149">The same will be true for any view that uses the Student model.</span></span>

![Page d’index des étudiants affichant les dates sans les heures](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="744a9-151">Attribut StringLength</span><span class="sxs-lookup"><span data-stu-id="744a9-151">The StringLength attribute</span></span>

<span data-ttu-id="744a9-152">Vous pouvez également spécifier les règles de validation de données et les messages d’erreur de validation à l’aide d’attributs.</span><span class="sxs-lookup"><span data-stu-id="744a9-152">You can also specify data validation rules and validation error messages using attributes.</span></span> <span data-ttu-id="744a9-153">L’attribut `StringLength` définit la longueur maximale dans la base de données et assure la validation côté client et côté serveur pour ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="744a9-153">The `StringLength` attribute sets the maximum length  in the database and provides client side and server side validation for ASP.NET Core MVC.</span></span> <span data-ttu-id="744a9-154">Vous pouvez également spécifier la longueur de chaîne minimale dans cet attribut, mais la valeur minimale n’a aucun impact sur le schéma de base de données.</span><span class="sxs-lookup"><span data-stu-id="744a9-154">You can also specify the minimum string length in this attribute, but the minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="744a9-155">Supposons que vous voulez garantir que les utilisateurs n’entrent pas plus de 50 caractères pour un nom.</span><span class="sxs-lookup"><span data-stu-id="744a9-155">Suppose you want to ensure that users don't enter more than 50 characters for a name.</span></span> <span data-ttu-id="744a9-156">Pour ajouter cette limitation, ajoutez des attributs `StringLength` aux propriétés `LastName` et `FirstMidName`, comme indiqué dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="744a9-156">To add this limitation, add `StringLength` attributes to the `LastName` and `FirstMidName` properties, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="744a9-157">L’attribut `StringLength` n’empêche pas un utilisateur d’entrer un espace blanc comme nom.</span><span class="sxs-lookup"><span data-stu-id="744a9-157">The `StringLength` attribute won't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="744a9-158">Vous pouvez utiliser l’attribut `RegularExpression` pour appliquer des restrictions à l’entrée.</span><span class="sxs-lookup"><span data-stu-id="744a9-158">You can use the `RegularExpression` attribute to apply restrictions to the input.</span></span> <span data-ttu-id="744a9-159">Par exemple, le code suivant exige que le premier caractère soit en majuscule et que les autres caractères soient alphabétiques :</span><span class="sxs-lookup"><span data-stu-id="744a9-159">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z]*$")]
```

<span data-ttu-id="744a9-160">L’attribut `MaxLength` fournit des fonctionnalités similaires à l’attribut `StringLength`, mais n’assure pas la validation côté client.</span><span class="sxs-lookup"><span data-stu-id="744a9-160">The `MaxLength` attribute provides functionality similar to the `StringLength` attribute but doesn't provide client side validation.</span></span>

<span data-ttu-id="744a9-161">Le modèle de base de données a maintenant changé d’une manière qui nécessite la modification du schéma de base de données.</span><span class="sxs-lookup"><span data-stu-id="744a9-161">The database model has now changed in a way that requires a change in the database schema.</span></span> <span data-ttu-id="744a9-162">Vous allez utiliser des migrations pour mettre à jour le schéma sans perdre les données que vous avez éventuellement ajoutées à la base de données via l’interface utilisateur de l’application.</span><span class="sxs-lookup"><span data-stu-id="744a9-162">You'll use migrations to update the schema without losing any data that you may have added to the database by using the application UI.</span></span>

<span data-ttu-id="744a9-163">Enregistrez vos modifications et générez le projet.</span><span class="sxs-lookup"><span data-stu-id="744a9-163">Save your changes and build the project.</span></span> <span data-ttu-id="744a9-164">Ensuite, ouvrez la fenêtre de commande dans le dossier du projet et entrez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="744a9-164">Then open the command window in the project folder and enter the following commands:</span></span>

```dotnetcli
dotnet ef migrations add MaxLengthOnNames
```

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="744a9-165">La commande `migrations add` vous avertit qu’une perte de données peut se produire, car la modification raccourcit la longueur maximale de deux colonnes.</span><span class="sxs-lookup"><span data-stu-id="744a9-165">The `migrations add` command warns that data loss may occur, because the change makes the maximum length shorter for two columns.</span></span>  <span data-ttu-id="744a9-166">Les migrations créent un fichier nommé *\<timeStamp> _MaxLengthOnNames. cs*.</span><span class="sxs-lookup"><span data-stu-id="744a9-166">Migrations creates a file named *\<timeStamp>_MaxLengthOnNames.cs*.</span></span> <span data-ttu-id="744a9-167">Ce fichier contient du code dans la méthode `Up` qui met à jour la base de données pour qu’elle corresponde au modèle de données actuel.</span><span class="sxs-lookup"><span data-stu-id="744a9-167">This file contains code in the `Up` method that will update the database to match the current data model.</span></span> <span data-ttu-id="744a9-168">La commande `database update` a exécuté ce code.</span><span class="sxs-lookup"><span data-stu-id="744a9-168">The `database update` command ran that code.</span></span>

<span data-ttu-id="744a9-169">L’horodatage utilisé comme préfixe du nom de fichier migrations est utilisé par Entity Framework pour ordonner les migrations.</span><span class="sxs-lookup"><span data-stu-id="744a9-169">The timestamp prefixed to the migrations file name is used by Entity Framework to order the migrations.</span></span> <span data-ttu-id="744a9-170">Vous pouvez créer plusieurs migrations avant d’exécuter la commande de mise à jour de base de données, puis toutes les migrations sont appliquées dans l’ordre où elles ont été créées.</span><span class="sxs-lookup"><span data-stu-id="744a9-170">You can create multiple migrations before running the update-database command, and then all of the migrations are applied in the order in which they were created.</span></span>

<span data-ttu-id="744a9-171">Exécutez l’application, sélectionnez l’onglet **Students**, cliquez sur **Create New** et essayez d’entrer un nom de plus de 50 caractères.</span><span class="sxs-lookup"><span data-stu-id="744a9-171">Run the app, select the **Students** tab, click **Create New**, and try to enter either name longer than 50 characters.</span></span> <span data-ttu-id="744a9-172">L’application doit empêcher cette opération.</span><span class="sxs-lookup"><span data-stu-id="744a9-172">The application should prevent you from doing this.</span></span> 

### <a name="the-column-attribute"></a><span data-ttu-id="744a9-173">Attribut Column</span><span class="sxs-lookup"><span data-stu-id="744a9-173">The Column attribute</span></span>

<span data-ttu-id="744a9-174">Vous pouvez également utiliser des attributs pour contrôler la façon dont les classes et les propriétés sont mappées à la base de données.</span><span class="sxs-lookup"><span data-stu-id="744a9-174">You can also use attributes to control how your classes and properties are mapped to the database.</span></span> <span data-ttu-id="744a9-175">Supposons que vous aviez utilisé le nom `FirstMidName` pour le champ de prénom, car le champ peut également contenir un deuxième prénom.</span><span class="sxs-lookup"><span data-stu-id="744a9-175">Suppose you had used the name `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span> <span data-ttu-id="744a9-176">Mais vous souhaitez que la colonne de base de données soit nommée `FirstName`, car les utilisateurs qui écriront des requêtes ad-hoc par rapport à la base de données sont habitués à ce nom.</span><span class="sxs-lookup"><span data-stu-id="744a9-176">But you want the database column to be named `FirstName`, because users who will be writing ad-hoc queries against the database are accustomed to that name.</span></span> <span data-ttu-id="744a9-177">Pour effectuer ce mappage, vous pouvez utiliser l’attribut `Column`.</span><span class="sxs-lookup"><span data-stu-id="744a9-177">To make this mapping, you can use the `Column` attribute.</span></span>

<span data-ttu-id="744a9-178">L’attribut `Column` spécifie que lorsque la base de données sera créée, la colonne de la table `Student` qui est mappée sur la propriété `FirstMidName` sera nommée `FirstName`.</span><span class="sxs-lookup"><span data-stu-id="744a9-178">The `Column` attribute specifies that when the database is created, the column of the `Student` table that maps to the `FirstMidName` property will be named `FirstName`.</span></span> <span data-ttu-id="744a9-179">En d’autres termes, lorsque votre code fait référence à `Student.FirstMidName`, les données proviennent de la colonne `FirstName` de la table `Student` ou y sont mises à jour.</span><span class="sxs-lookup"><span data-stu-id="744a9-179">In other words, when your code refers to `Student.FirstMidName`, the data will come from or be updated in the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="744a9-180">Si vous ne nommez pas les colonnes, elles obtiennent le nom de la propriété.</span><span class="sxs-lookup"><span data-stu-id="744a9-180">If you don't specify column names, they're given the same name as the property name.</span></span>

<span data-ttu-id="744a9-181">Dans le fichier *Student.cs*, ajoutez une instruction `using` pour `System.ComponentModel.DataAnnotations.Schema` et ajoutez l’attribut de nom de colonne à la propriété `FirstMidName`, comme indiqué dans le code en surbrillance suivant :</span><span class="sxs-lookup"><span data-stu-id="744a9-181">In the *Student.cs* file, add a `using` statement for `System.ComponentModel.DataAnnotations.Schema` and add the column name attribute to the `FirstMidName` property, as shown in the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="744a9-182">L’ajout de l’attribut `Column` change le modèle sur lequel repose `SchoolContext`, donc il ne correspond pas à la base de données.</span><span class="sxs-lookup"><span data-stu-id="744a9-182">The addition of the `Column` attribute changes the model backing the `SchoolContext`, so it won't match the database.</span></span>

<span data-ttu-id="744a9-183">Enregistrez vos modifications et générez le projet.</span><span class="sxs-lookup"><span data-stu-id="744a9-183">Save your changes and build the project.</span></span> <span data-ttu-id="744a9-184">Ensuite, ouvrez la fenêtre de commande dans le dossier du projet et entrez les commandes suivantes pour créer une autre migration :</span><span class="sxs-lookup"><span data-stu-id="744a9-184">Then open the command window in the project folder and enter the following commands to create another migration:</span></span>

```dotnetcli
dotnet ef migrations add ColumnFirstName
```

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="744a9-185">Dans l’**Explorateur d’objets SQL Server**, ouvrez le concepteur de tables Student en double-cliquant sur la table **Student**.</span><span class="sxs-lookup"><span data-stu-id="744a9-185">In **SQL Server Object Explorer**, open the Student table designer by double-clicking the **Student** table.</span></span>

![Table Students dans SSOX après les migrations](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="744a9-187">Avant d’appliquer les deux premières migrations, les colonnes de nom étaient de type nvarchar(MAX).</span><span class="sxs-lookup"><span data-stu-id="744a9-187">Before you applied the first two migrations, the name columns were of type nvarchar(MAX).</span></span> <span data-ttu-id="744a9-188">Elles sont maintenant de type nvarchar(50) et le nom de colonne FirstMidName a été remplacé par FirstName.</span><span class="sxs-lookup"><span data-stu-id="744a9-188">They're now nvarchar(50) and the column name has changed from FirstMidName to FirstName.</span></span>

> [!Note]
> <span data-ttu-id="744a9-189">Si vous essayez de compiler avant d’avoir fini de créer toutes les classes d’entité dans les sections suivantes, vous pouvez obtenir des erreurs de compilation.</span><span class="sxs-lookup"><span data-stu-id="744a9-189">If you try to compile before you finish creating all of the entity classes in the following sections, you might get compiler errors.</span></span>

## <a name="changes-to-student-entity"></a><span data-ttu-id="744a9-190">Apporter des modifications à l’entité Student</span><span class="sxs-lookup"><span data-stu-id="744a9-190">Changes to Student entity</span></span>

![Entité Student](complex-data-model/_static/student-entity.png)

<span data-ttu-id="744a9-192">Dans *Models/Student.cs*, remplacez le code que vous avez ajouté précédemment par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="744a9-192">In *Models/Student.cs*, replace the code you added earlier with the following code.</span></span> <span data-ttu-id="744a9-193">Les modifications sont mises en surbrillance.</span><span class="sxs-lookup"><span data-stu-id="744a9-193">The changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="744a9-194">Attribut Required</span><span class="sxs-lookup"><span data-stu-id="744a9-194">The Required attribute</span></span>

<span data-ttu-id="744a9-195">L’attribut `Required` fait des propriétés de nom des champs obligatoires.</span><span class="sxs-lookup"><span data-stu-id="744a9-195">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="744a9-196">L’attribut `Required` n’est pas requis pour les types non nullables tels que les types valeur (DateTime, int, double, float, etc.).</span><span class="sxs-lookup"><span data-stu-id="744a9-196">The `Required` attribute isn't needed for non-nullable types such as value types (DateTime, int, double, float, etc.).</span></span> <span data-ttu-id="744a9-197">Les types qui n’acceptent pas les valeurs Null sont traités automatiquement comme des champs obligatoires.</span><span class="sxs-lookup"><span data-stu-id="744a9-197">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="744a9-198">L'attribut `Required` doit être utilisé avec `MinimumLength` pour appliquer `MinimumLength`.</span><span class="sxs-lookup"><span data-stu-id="744a9-198">The `Required` attribute must be used with `MinimumLength` for the `MinimumLength` to be enforced.</span></span>

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="744a9-199">Attribut Display</span><span class="sxs-lookup"><span data-stu-id="744a9-199">The Display attribute</span></span>

<span data-ttu-id="744a9-200">L’attribut `Display` spécifie que la légende pour les zones de texte doit être « First Name », « Last Name », « Full Name » et « Enrollment Date », au lieu du nom de propriété dans chaque instance (qui n’a pas d’espace pour séparer les mots).</span><span class="sxs-lookup"><span data-stu-id="744a9-200">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date" instead of the property name in each instance (which has no space dividing the words).</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="744a9-201">Propriété calculée FullName</span><span class="sxs-lookup"><span data-stu-id="744a9-201">The FullName calculated property</span></span>

<span data-ttu-id="744a9-202">`FullName` est une propriété calculée qui retourne une valeur créée par concaténation de deux autres propriétés.</span><span class="sxs-lookup"><span data-stu-id="744a9-202">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="744a9-203">Par conséquent, elle a uniquement un accesseur get et aucune colonne `FullName` n’est générée dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="744a9-203">Therefore it has only a get accessor, and no `FullName` column will be generated in the database.</span></span>

## <a name="create-instructor-entity"></a><span data-ttu-id="744a9-204">Créer une entité Instructor</span><span class="sxs-lookup"><span data-stu-id="744a9-204">Create Instructor entity</span></span>

![Entité Instructor](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="744a9-206">Créez *Models/Instructor.cs*, en remplaçant le code du modèle par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="744a9-206">Create *Models/Instructor.cs*, replacing the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_BeforeInheritance)]

<span data-ttu-id="744a9-207">Notez que plusieurs propriétés sont identiques dans les entités Student et Instructor.</span><span class="sxs-lookup"><span data-stu-id="744a9-207">Notice that several properties are the same in the Student and Instructor entities.</span></span> <span data-ttu-id="744a9-208">Dans le didacticiel [Implémentation de l’héritage](inheritance.md) plus loin dans cette série, vous allez refactoriser ce code pour éliminer la redondance.</span><span class="sxs-lookup"><span data-stu-id="744a9-208">In the [Implementing Inheritance](inheritance.md) tutorial later in this series, you'll refactor this code to eliminate the redundancy.</span></span>

<span data-ttu-id="744a9-209">Vous pouvez placer plusieurs attributs sur une seule ligne et écrire les attributs `HireDate` comme suit :</span><span class="sxs-lookup"><span data-stu-id="744a9-209">You can put multiple attributes on one line, so you could also write the `HireDate` attributes as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="744a9-210">Propriétés de navigation CourseAssignments et OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="744a9-210">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="744a9-211">Les propriétés `CourseAssignments` et `OfficeAssignment` sont des propriétés de navigation.</span><span class="sxs-lookup"><span data-stu-id="744a9-211">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="744a9-212">Un formateur pouvant animer un nombre quelconque de cours, `CourseAssignments` est défini comme une collection.</span><span class="sxs-lookup"><span data-stu-id="744a9-212">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="744a9-213">Si une propriété de navigation peut contenir plusieurs entités, son type doit être une liste dans laquelle les entrées peuvent être ajoutées, supprimées et mises à jour.</span><span class="sxs-lookup"><span data-stu-id="744a9-213">If a navigation property can hold multiple entities, its type must be a list in which entries can be added, deleted, and updated.</span></span>  <span data-ttu-id="744a9-214">Vous pouvez spécifier `ICollection<T>` ou un type tel que `List<T>` ou `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="744a9-214">You can specify `ICollection<T>` or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="744a9-215">Si vous spécifiez `ICollection<T>`, EF crée une collection `HashSet<T>` par défaut.</span><span class="sxs-lookup"><span data-stu-id="744a9-215">If you specify `ICollection<T>`, EF creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="744a9-216">La raison pour laquelle ce sont des entités `CourseAssignment` est expliquée ci-dessous dans la section sur les relations plusieurs-à-plusieurs.</span><span class="sxs-lookup"><span data-stu-id="744a9-216">The reason why these are `CourseAssignment` entities is explained below in the section about many-to-many relationships.</span></span>

<span data-ttu-id="744a9-217">Les règles d’entreprise de Contoso University stipulent qu’un formateur peut avoir au plus un bureau, de sorte que la propriété `OfficeAssignment` contient une seule entité OfficeAssignment (qui peut être null si aucun bureau n’est affecté).</span><span class="sxs-lookup"><span data-stu-id="744a9-217">Contoso University business rules state that an instructor can only have at most one office, so the `OfficeAssignment` property holds a single OfficeAssignment entity (which may be null if no office is assigned).</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-officeassignment-entity"></a><span data-ttu-id="744a9-218">Créer une entité OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="744a9-218">Create OfficeAssignment entity</span></span>

![Entité OfficeAssignment](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="744a9-220">Créez *Models/OfficeAssignment.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="744a9-220">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="744a9-221">Attribut Key</span><span class="sxs-lookup"><span data-stu-id="744a9-221">The Key attribute</span></span>

<span data-ttu-id="744a9-222">Il existe une relation un-à-zéro-ou-un entre les entités Instructor et OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="744a9-222">There's a one-to-zero-or-one relationship  between the Instructor and the OfficeAssignment entities.</span></span> <span data-ttu-id="744a9-223">Une affectation de bureau existe uniquement en relation avec le formateur auquel elle est affectée. Par conséquent, sa clé primaire est également sa clé étrangère pour l’entité Instructor.</span><span class="sxs-lookup"><span data-stu-id="744a9-223">An office assignment only exists in relation to the instructor it's assigned to, and therefore its primary key is also its foreign key to the Instructor entity.</span></span> <span data-ttu-id="744a9-224">Mais Entity Framework ne peut pas reconnaître automatiquement InstructorID comme clé primaire de cette entité, car son nom ne suit pas la convention de nommage d’ID ou de classnameID.</span><span class="sxs-lookup"><span data-stu-id="744a9-224">But the Entity Framework can't automatically recognize InstructorID as the primary key of this entity because its name doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="744a9-225">Par conséquent, l’attribut `Key` est utilisé pour l’identifier comme clé :</span><span class="sxs-lookup"><span data-stu-id="744a9-225">Therefore, the `Key` attribute is used to identify it as the key:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="744a9-226">Vous pouvez également utiliser l’attribut `Key` si l’entité a sa propre clé primaire, mais que vous souhaitez nommer la propriété autrement que classnameID ou ID.</span><span class="sxs-lookup"><span data-stu-id="744a9-226">You can also use the `Key` attribute if the entity does have its own primary key but you want to name the property something other than classnameID or ID.</span></span>

<span data-ttu-id="744a9-227">Par défaut, EF traite la clé comme n’étant pas générée par la base de données, car la colonne est utilisée pour une relation d’identification.</span><span class="sxs-lookup"><span data-stu-id="744a9-227">By default, EF treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="744a9-228">Propriété de navigation Instructor</span><span class="sxs-lookup"><span data-stu-id="744a9-228">The Instructor navigation property</span></span>

<span data-ttu-id="744a9-229">L’entité Instructor a une propriété de navigation `OfficeAssignment` nullable (parce qu’un formateur n’a peut-être pas d’affectation de bureau) et l’entité OfficeAssignment a une propriété de navigation `Instructor` non nullable (comme une affectation de bureau ne peut pas exister sans formateur, `InstructorID` est non nullable).</span><span class="sxs-lookup"><span data-stu-id="744a9-229">The Instructor entity has a nullable `OfficeAssignment` navigation property (because an instructor might not have an office assignment), and the OfficeAssignment entity has a non-nullable `Instructor` navigation property (because an office assignment can't exist without an instructor -- `InstructorID` is non-nullable).</span></span> <span data-ttu-id="744a9-230">Lorsqu’une entité Instructor a une entité OfficeAssignment associée, chaque entité a une référence à l’autre dans sa propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="744a9-230">When an Instructor entity has a related OfficeAssignment entity, each entity will have a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="744a9-231">Vous pouvez placer un attribut `[Required]` sur la propriété de navigation du formateur pour spécifier qu’il doit y avoir un formateur associé, mais vous n’êtes pas obligé de le faire, car la clé étrangère `InstructorID` (qui est également la clé pour cette table) est non nullable.</span><span class="sxs-lookup"><span data-stu-id="744a9-231">You could put a `[Required]` attribute on the Instructor navigation property to specify that there must be a related instructor, but you don't have to do that because the `InstructorID` foreign key (which is also the key to this table) is non-nullable.</span></span>

## <a name="modify-course-entity"></a><span data-ttu-id="744a9-232">Modifier l’entité Course</span><span class="sxs-lookup"><span data-stu-id="744a9-232">Modify Course entity</span></span>

![Entité Course](complex-data-model/_static/course-entity.png)

<span data-ttu-id="744a9-234">Dans *Models/Course.cs*, remplacez le code que vous avez ajouté précédemment par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="744a9-234">In *Models/Course.cs*, replace the code you added earlier with the following code.</span></span> <span data-ttu-id="744a9-235">Les modifications sont mises en surbrillance.</span><span class="sxs-lookup"><span data-stu-id="744a9-235">The changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="744a9-236">L’entité de cours a une propriété de clé étrangère `DepartmentID` qui pointe sur l’entité Department associée et elle a une propriété de navigation `Department`.</span><span class="sxs-lookup"><span data-stu-id="744a9-236">The course entity has a foreign key property `DepartmentID` which points to the related Department entity and it has a `Department` navigation property.</span></span>

<span data-ttu-id="744a9-237">Entity Framework ne vous demande pas d’ajouter une propriété de clé étrangère à votre modèle de données lorsque vous avez une propriété de navigation pour une entité associée.</span><span class="sxs-lookup"><span data-stu-id="744a9-237">The Entity Framework doesn't require you to add a foreign key property to your data model when you have a navigation property for a related entity.</span></span>  <span data-ttu-id="744a9-238">EF crée automatiquement des clés étrangères dans la base de données partout où elles sont nécessaires et crée des [propriétés fantôme](/ef/core/modeling/shadow-properties) pour elles.</span><span class="sxs-lookup"><span data-stu-id="744a9-238">EF automatically creates foreign keys in the database wherever they're needed and creates [shadow properties](/ef/core/modeling/shadow-properties) for them.</span></span> <span data-ttu-id="744a9-239">Mais le fait d’avoir la clé étrangère dans le modèle de données peut rendre les mises à jour plus simples et plus efficaces.</span><span class="sxs-lookup"><span data-stu-id="744a9-239">But having the foreign key in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="744a9-240">Par exemple, lorsque vous récupérez une entité de cours à modifier, l’entité Department a la valeur Null si vous ne la chargez pas. Par conséquent, lorsque vous mettez à jour l’entité de cours, vous devriez tout d’abord récupérer l’entité Department.</span><span class="sxs-lookup"><span data-stu-id="744a9-240">For example, when you fetch a course entity to edit, the  Department entity is null if you don't load it, so when you update the course entity, you would have to first fetch the Department entity.</span></span> <span data-ttu-id="744a9-241">Lorsque la propriété de clé étrangère `DepartmentID` est incluse dans le modèle de données, vous n’avez pas besoin de récupérer l’entité Department avant de mettre à jour.</span><span class="sxs-lookup"><span data-stu-id="744a9-241">When the foreign key property `DepartmentID` is included in the data model, you don't need to fetch the Department entity before you update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="744a9-242">Attribut DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="744a9-242">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="744a9-243">L’attribut `DatabaseGenerated` avec le paramètre `None` sur la propriété `CourseID` spécifie que les valeurs de clé primaire sont fournies par l’utilisateur au lieu d’être générées par la base de données.</span><span class="sxs-lookup"><span data-stu-id="744a9-243">The `DatabaseGenerated` attribute with the `None` parameter on the `CourseID` property specifies that primary key values are provided by the user rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="744a9-244">Par défaut, Entity Framework suppose que les valeurs de clé primaire sont générées par la base de données.</span><span class="sxs-lookup"><span data-stu-id="744a9-244">By default, Entity Framework assumes that primary key values are generated by the database.</span></span> <span data-ttu-id="744a9-245">C’est ce que vous souhaitez dans la plupart des scénarios.</span><span class="sxs-lookup"><span data-stu-id="744a9-245">That's what you want in most scenarios.</span></span> <span data-ttu-id="744a9-246">Toutefois, pour les entités Course, vous allez utiliser un numéro de cours spécifié par l’utilisateur comme une série de 1000 pour un département, une série de 2000 pour un autre département, etc.</span><span class="sxs-lookup"><span data-stu-id="744a9-246">However, for Course entities, you'll use a user-specified course number such as a 1000 series for one department, a 2000 series for another department, and so on.</span></span>

<span data-ttu-id="744a9-247">L’attribut `DatabaseGenerated` peut également être utilisé pour générer des valeurs par défaut, comme dans le cas des colonnes de base de données utilisées pour enregistrer la date à laquelle une ligne a été créée ou mise à jour.</span><span class="sxs-lookup"><span data-stu-id="744a9-247">The `DatabaseGenerated` attribute can also be used to generate default values, as in the case of database columns used to record the date a row was created or updated.</span></span>  <span data-ttu-id="744a9-248">Pour plus d’informations, consultez [Propriétés générées](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="744a9-248">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="744a9-249">Propriétés de clé étrangère et de navigation</span><span class="sxs-lookup"><span data-stu-id="744a9-249">Foreign key and navigation properties</span></span>

<span data-ttu-id="744a9-250">Les propriétés de clé étrangère et les propriétés de navigation dans l’entité Course reflètent les relations suivantes :</span><span class="sxs-lookup"><span data-stu-id="744a9-250">The foreign key properties and navigation properties in the Course entity reflect the following relationships:</span></span>

<span data-ttu-id="744a9-251">Un cours est affecté à un seul département, donc il existe une clé étrangère `DepartmentID` et une propriété de navigation `Department` pour les raisons mentionnées ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="744a9-251">A course is assigned to one department, so there's a `DepartmentID` foreign key and a `Department` navigation property for the reasons mentioned above.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="744a9-252">Un cours pouvant avoir un nombre quelconque d’étudiants inscrits, la propriété de navigation `Enrollments` est une collection :</span><span class="sxs-lookup"><span data-stu-id="744a9-252">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="744a9-253">Un cours peut être animé par plusieurs formateurs, si bien que la propriété de navigation `CourseAssignments` est une collection (le type `CourseAssignment` est expliqué [ultérieurement](#many-to-many-relationships)) :</span><span class="sxs-lookup"><span data-stu-id="744a9-253">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection (the type `CourseAssignment` is explained [later](#many-to-many-relationships)):</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

## <a name="create-department-entity"></a><span data-ttu-id="744a9-254">Créer l’entité Department</span><span class="sxs-lookup"><span data-stu-id="744a9-254">Create Department entity</span></span>

![Entité de service](complex-data-model/_static/department-entity.png)

<span data-ttu-id="744a9-256">Créez *Models/Department.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="744a9-256">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="744a9-257">Attribut Column</span><span class="sxs-lookup"><span data-stu-id="744a9-257">The Column attribute</span></span>

<span data-ttu-id="744a9-258">Précédemment, vous avez utilisé l’attribut `Column` pour changer le mappage de noms de colonne.</span><span class="sxs-lookup"><span data-stu-id="744a9-258">Earlier you used the `Column` attribute to change column name mapping.</span></span> <span data-ttu-id="744a9-259">Dans le code de l’entité Department, l’attribut `Column` sert à modifier le mappage des types de données SQL afin que la colonne soit définie à l’aide du type monétaire (money) SQL Server dans la base de données :</span><span class="sxs-lookup"><span data-stu-id="744a9-259">In the code for the Department entity, the `Column` attribute is being used to change SQL data type mapping so that the column will be defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="744a9-260">Le mappage de colonnes n’est généralement pas nécessaire, car Entity Framework choisit le type de données SQL Server approprié en fonction du type CLR que vous définissez pour la propriété.</span><span class="sxs-lookup"><span data-stu-id="744a9-260">Column mapping is generally not required, because the Entity Framework chooses the appropriate SQL Server data type based on the CLR type that you define for the property.</span></span> <span data-ttu-id="744a9-261">Le type CLR `decimal` est mappé à un type SQL Server `decimal`.</span><span class="sxs-lookup"><span data-stu-id="744a9-261">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="744a9-262">Toutefois, dans ce cas, vous savez que la colonne contiendra des montants en devise et que le type de données monétaire est plus approprié pour cela.</span><span class="sxs-lookup"><span data-stu-id="744a9-262">But in this case you know that the column will be holding currency amounts, and the money data type is more appropriate for that.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="744a9-263">Propriétés de clé étrangère et de navigation</span><span class="sxs-lookup"><span data-stu-id="744a9-263">Foreign key and navigation properties</span></span>

<span data-ttu-id="744a9-264">Les propriétés de clé étrangère et de navigation reflètent les relations suivantes :</span><span class="sxs-lookup"><span data-stu-id="744a9-264">The foreign key and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="744a9-265">Un département peut ou non avoir un administrateur, et un administrateur est toujours un formateur.</span><span class="sxs-lookup"><span data-stu-id="744a9-265">A department may or may not have an administrator, and an administrator is always an instructor.</span></span> <span data-ttu-id="744a9-266">Par conséquent, la propriété `InstructorID` est incluse en tant que clé étrangère à l’entité Instructor, et un point d’interrogation est ajouté après la désignation du type `int` pour marquer la propriété comme nullable.</span><span class="sxs-lookup"><span data-stu-id="744a9-266">Therefore the `InstructorID` property is included as the foreign key to the Instructor entity, and a question mark is added after the `int` type designation to mark the property as nullable.</span></span> <span data-ttu-id="744a9-267">La propriété de navigation est nommée `Administrator`, mais elle contient une entité Instructor :</span><span class="sxs-lookup"><span data-stu-id="744a9-267">The navigation property is named `Administrator` but holds an Instructor entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="744a9-268">Un département pouvant avoir de nombreux cours, il existe une propriété de navigation Courses :</span><span class="sxs-lookup"><span data-stu-id="744a9-268">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

> [!NOTE]
> <span data-ttu-id="744a9-269">Par convention, Entity Framework permet la suppression en cascade pour les clés étrangères non nullables et pour les relations plusieurs à plusieurs.</span><span class="sxs-lookup"><span data-stu-id="744a9-269">By convention, the Entity Framework enables cascade delete for non-nullable foreign keys and for many-to-many relationships.</span></span> <span data-ttu-id="744a9-270">Cela peut entraîner des règles de suppression en cascade circulaires, qui provoqueront une exception lorsque vous essaierez d’ajouter une migration.</span><span class="sxs-lookup"><span data-stu-id="744a9-270">This can result in circular cascade delete rules, which will cause an exception when you try to add a migration.</span></span> <span data-ttu-id="744a9-271">Par exemple, si vous n’avez pas défini la propriété Department.InstructorID comme nullable, EF configure une règle de suppression en cascade pour supprimer le département lorsque vous supprimez le formateur, ce qui n’est pas ce que vous voulez.</span><span class="sxs-lookup"><span data-stu-id="744a9-271">For example, if you didn't define the Department.InstructorID property as nullable, EF would configure a cascade delete rule to delete the department when you delete the instructor, which isn't what you want to have happen.</span></span> <span data-ttu-id="744a9-272">Si vos règles d’entreprise exigent que la propriété `InstructorID` soit non nullable, vous devez utiliser l’instruction d’API Fluent suivante pour désactiver la suppression en cascade sur la relation :</span><span class="sxs-lookup"><span data-stu-id="744a9-272">If your business rules required the `InstructorID` property to be non-nullable, you would have to use the following fluent API statement to disable cascade delete on the relationship:</span></span>
>
> ```csharp
> modelBuilder.Entity<Department>()
>    .HasOne(d => d.Administrator)
>    .WithMany()
>    .OnDelete(DeleteBehavior.Restrict)
> ```

## <a name="modify-enrollment-entity"></a><span data-ttu-id="744a9-273">Modifier l’entité Enrollment</span><span class="sxs-lookup"><span data-stu-id="744a9-273">Modify Enrollment entity</span></span>

![Entité Enrollment](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="744a9-275">Dans *Models/Enrollment.cs*, remplacez le code que vous avez ajouté précédemment par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="744a9-275">In *Models/Enrollment.cs*, replace the code you added earlier with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="744a9-276">Propriétés de clé étrangère et de navigation</span><span class="sxs-lookup"><span data-stu-id="744a9-276">Foreign key and navigation properties</span></span>

<span data-ttu-id="744a9-277">Les propriétés de clé étrangère et de navigation reflètent les relations suivantes :</span><span class="sxs-lookup"><span data-stu-id="744a9-277">The foreign key properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="744a9-278">Un enregistrement d’inscription est utilisé pour un cours unique, si bien qu’il existe une propriété de clé étrangère `CourseID` et une propriété de navigation `Course` :</span><span class="sxs-lookup"><span data-stu-id="744a9-278">An enrollment record is for a single course, so there's a `CourseID` foreign key property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="744a9-279">Un enregistrement d’inscription est utilisé pour un étudiant unique, si bien qu’il existe une propriété de clé étrangère `StudentID` et une propriété de navigation `Student` :</span><span class="sxs-lookup"><span data-stu-id="744a9-279">An enrollment record is for a single student, so there's a `StudentID` foreign key property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="744a9-280">Relations plusieurs-à-plusieurs</span><span class="sxs-lookup"><span data-stu-id="744a9-280">Many-to-Many relationships</span></span>

<span data-ttu-id="744a9-281">Il existe une relation plusieurs-à-plusieurs entre les entités Student et Course, et l’entité Enrollment fonctionne comme une table de jointure plusieurs-à-plusieurs *avec une charge utile* dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="744a9-281">There's a many-to-many relationship between the Student and Course entities, and the Enrollment entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="744a9-282">« Avec une charge utile » signifie que la table Enrollment contient des données supplémentaires en plus des clés étrangères pour les tables jointes (dans ce cas, une clé primaire et une propriété Grade).</span><span class="sxs-lookup"><span data-stu-id="744a9-282">"With payload" means that the Enrollment table contains additional data besides foreign keys for the joined tables (in this case, a primary key and a Grade property).</span></span>

<span data-ttu-id="744a9-283">L’illustration suivante montre à quoi ressemblent ces relations dans un diagramme d’entité.</span><span class="sxs-lookup"><span data-stu-id="744a9-283">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="744a9-284">(Ce diagramme a été généré à l’aide d’Entity Framework Power Tools pour EF 6.x ; la création du diagramme ne fait pas partie de ce didacticiel, elle est uniquement utilisée ici à titre d’illustration.)</span><span class="sxs-lookup"><span data-stu-id="744a9-284">(This diagram was generated using the Entity Framework Power Tools for EF 6.x; creating the diagram isn't part of the tutorial, it's just being used here as an illustration.)</span></span>

![Relation plusieurs-à-plusieurs Student-Course](complex-data-model/_static/student-course.png)

<span data-ttu-id="744a9-286">Chaque ligne de relation comporte un 1 à une extrémité et un astérisque (\*) à l’autre, ce qui indique une relation un-à-plusieurs.</span><span class="sxs-lookup"><span data-stu-id="744a9-286">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="744a9-287">Si la table Enrollment n’incluait pas d’informations de notes, elle aurait uniquement besoin de contenir les deux clés étrangères CourseID et StudentID.</span><span class="sxs-lookup"><span data-stu-id="744a9-287">If the Enrollment table didn't include grade information, it would only need to contain the two foreign keys CourseID and StudentID.</span></span> <span data-ttu-id="744a9-288">Dans ce cas, ce serait une table de jointure plusieurs-à-plusieurs sans charge utile (ou une table de jointure pure) dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="744a9-288">In that case, it would be a many-to-many join table without payload (or a pure join table) in the database.</span></span> <span data-ttu-id="744a9-289">Les entités Instructor and Course ont ce type de relation plusieurs-à-plusieurs, et l’étape suivante consiste à créer une classe d’entité qui fonctionnera comme une table de jointure sans charge utile.</span><span class="sxs-lookup"><span data-stu-id="744a9-289">The Instructor and Course entities have that kind of many-to-many relationship, and your next step is to create an entity class to function as a join table without payload.</span></span>

<span data-ttu-id="744a9-290">(EF 6.x prend en charge les tables de jointure implicites pour les relations plusieurs-à-plusieurs, mais EF Core ne le fait pas.</span><span class="sxs-lookup"><span data-stu-id="744a9-290">(EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="744a9-291">Pour plus d’informations, consultez la [discussion dans le dépôt GitHub EF Core](https://github.com/aspnet/EntityFramework/issues/1368).)</span><span class="sxs-lookup"><span data-stu-id="744a9-291">For more information, see the [discussion in the EF Core GitHub repository](https://github.com/aspnet/EntityFramework/issues/1368).)</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="744a9-292">Entité CourseAssignment</span><span class="sxs-lookup"><span data-stu-id="744a9-292">The CourseAssignment entity</span></span>

![Entité CourseAssignment](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="744a9-294">Créez *Models/CourseAssignment.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="744a9-294">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/CourseAssignment.cs)]

### <a name="join-entity-names"></a><span data-ttu-id="744a9-295">Noms des entités de jointure</span><span class="sxs-lookup"><span data-stu-id="744a9-295">Join entity names</span></span>

<span data-ttu-id="744a9-296">Une table de jointure est requise dans la base de données pour la relation plusieurs-à-plusieurs entre formateurs et cours, et elle doit être représentée par un jeu d’entités.</span><span class="sxs-lookup"><span data-stu-id="744a9-296">A join table is required in the database for the Instructor-to-Courses many-to-many relationship, and it has to be represented by an entity set.</span></span> <span data-ttu-id="744a9-297">Il est courant de nommer une entité de jointure `EntityName1EntityName2`, ce qui donnerait dans ce cas `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="744a9-297">It's common to name a join entity `EntityName1EntityName2`, which in this case would be `CourseInstructor`.</span></span> <span data-ttu-id="744a9-298">Toutefois, nous vous recommandons de choisir un nom qui décrit la relation.</span><span class="sxs-lookup"><span data-stu-id="744a9-298">However, we recommend that you choose a name that describes the relationship.</span></span> <span data-ttu-id="744a9-299">Les modèles de données sont simples au départ, puis croissent, avec des jointures sans charge utile qui obtiennent souvent des charges utiles plus tard.</span><span class="sxs-lookup"><span data-stu-id="744a9-299">Data models start out simple and grow, with no-payload joins frequently getting payloads later.</span></span> <span data-ttu-id="744a9-300">Si vous commencez avec un nom d’entité descriptif, vous n’aurez pas à le modifier par la suite.</span><span class="sxs-lookup"><span data-stu-id="744a9-300">If you start with a descriptive entity name, you won't have to change the name later.</span></span> <span data-ttu-id="744a9-301">Dans l’idéal, l’entité de jointure aura son propre nom (éventuellement un mot unique) naturel dans le domaine d’entreprise.</span><span class="sxs-lookup"><span data-stu-id="744a9-301">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="744a9-302">Par exemple, les livres et les clients pourraient être liés par le biais d’évaluations.</span><span class="sxs-lookup"><span data-stu-id="744a9-302">For example, Books and Customers could be linked through Ratings.</span></span> <span data-ttu-id="744a9-303">Pour cette relation, `CourseAssignment` est un meilleur choix que `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="744a9-303">For this relationship, `CourseAssignment` is a better choice than `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="744a9-304">Clé composite</span><span class="sxs-lookup"><span data-stu-id="744a9-304">Composite key</span></span>

<span data-ttu-id="744a9-305">Étant donné que les clés étrangères ne sont pas nullables et qu’elles identifient ensemble de façon unique chaque ligne de la table, une clé primaire distincte n’est pas requise.</span><span class="sxs-lookup"><span data-stu-id="744a9-305">Since the foreign keys are not nullable and together uniquely identify each row of the table, there's no need for a separate primary key.</span></span> <span data-ttu-id="744a9-306">Les propriétés *InstructorID* et *CourseID* doivent fonctionner comme une clé primaire composite.</span><span class="sxs-lookup"><span data-stu-id="744a9-306">The *InstructorID* and *CourseID* properties should function as a composite primary key.</span></span> <span data-ttu-id="744a9-307">La seule façon d’identifier des clés primaires composites pour EF consiste à utiliser l’*API Fluent* (ce n’est pas possible à l’aide d’attributs).</span><span class="sxs-lookup"><span data-stu-id="744a9-307">The only way to identify composite primary keys to EF is by using the *fluent API* (it can't be done by using attributes).</span></span> <span data-ttu-id="744a9-308">Vous allez voir comment configurer la clé primaire composite dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="744a9-308">You'll see how to configure the composite primary key in the next section.</span></span>

<span data-ttu-id="744a9-309">La clé composite garantit qu’en ayant plusieurs lignes pour un cours et plusieurs lignes pour un formateur, vous ne puissiez pas avoir plusieurs lignes pour les mêmes formateur et cours.</span><span class="sxs-lookup"><span data-stu-id="744a9-309">The composite key ensures that while you can have multiple rows for one course, and multiple rows for one instructor, you can't have multiple rows for the same instructor and course.</span></span> <span data-ttu-id="744a9-310">L’entité de jointure `Enrollment` définit sa propre clé primaire, si bien que les doublons de ce type sont possibles.</span><span class="sxs-lookup"><span data-stu-id="744a9-310">The `Enrollment` join entity defines its own primary key, so duplicates of this sort are possible.</span></span> <span data-ttu-id="744a9-311">Pour éviter ces doublons, vous pourriez ajouter un index unique sur les champs de clé étrangère ou configurer `Enrollment` avec une clé composite primaire similaire à `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="744a9-311">To prevent such duplicates, you could add a unique index on the foreign key fields, or configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="744a9-312">Pour plus d’informations, consultez [Index](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="744a9-312">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-database-context"></a><span data-ttu-id="744a9-313">Mettre à jour le contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="744a9-313">Update the database context</span></span>

<span data-ttu-id="744a9-314">Ajoutez le code en surbrillance suivant au fichier *Data/SchoolContext.cs* :</span><span class="sxs-lookup"><span data-stu-id="744a9-314">Add the following highlighted code to the *Data/SchoolContext.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="744a9-315">Ce code ajoute les nouvelles entités et configure la clé primaire composite de l’entité CourseAssignment.</span><span class="sxs-lookup"><span data-stu-id="744a9-315">This code adds the new entities and configures the CourseAssignment entity's composite primary key.</span></span>

## <a name="about-a-fluent-api-alternative"></a><span data-ttu-id="744a9-316">À propos de l’alternative d’API Fluent</span><span class="sxs-lookup"><span data-stu-id="744a9-316">About a fluent API alternative</span></span>

<span data-ttu-id="744a9-317">Le code dans la méthode `OnModelCreating` de la classe `DbContext` utilise l’*API Fluent* pour configurer le comportement EF.</span><span class="sxs-lookup"><span data-stu-id="744a9-317">The code in the `OnModelCreating` method of the `DbContext` class uses the *fluent API* to configure EF behavior.</span></span> <span data-ttu-id="744a9-318">L’API est appelée « fluent », car elle est souvent utilisée pour enchaîner une série d’appels de méthode en une seule instruction, comme dans cet exemple tiré de la [documentation d’EF Core](/ef/core/modeling/#use-fluent-api-to-configure-a-model) :</span><span class="sxs-lookup"><span data-stu-id="744a9-318">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement, as in this example from the [EF Core documentation](/ef/core/modeling/#use-fluent-api-to-configure-a-model):</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="744a9-319">Dans ce didacticiel, vous utilisez l’API Fluent uniquement pour le mappage de base de données que vous ne pouvez pas faire avec des attributs.</span><span class="sxs-lookup"><span data-stu-id="744a9-319">In this tutorial, you're using the fluent API only for database mapping that you can't do with attributes.</span></span> <span data-ttu-id="744a9-320">Toutefois, vous pouvez également utiliser l’API Fluent pour spécifier la majorité des règles de mise en forme, de validation et de mappage que vous pouvez spécifier à l’aide d’attributs.</span><span class="sxs-lookup"><span data-stu-id="744a9-320">However, you can also use the fluent API to specify most of the formatting, validation, and mapping rules that you can do by using attributes.</span></span> <span data-ttu-id="744a9-321">Certains attributs, tels que `MinimumLength`, ne peuvent pas être appliqués avec l’API Fluent.</span><span class="sxs-lookup"><span data-stu-id="744a9-321">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="744a9-322">Comme mentionné précédemment, `MinimumLength` ne change pas le schéma, il applique uniquement une règle de validation côté client et côté serveur.</span><span class="sxs-lookup"><span data-stu-id="744a9-322">As mentioned previously, `MinimumLength` doesn't change the schema, it only applies a client and server side validation rule.</span></span>

<span data-ttu-id="744a9-323">Certains développeurs préfèrent utiliser exclusivement l’API Fluent afin de conserver des classes d’entité « propres ».</span><span class="sxs-lookup"><span data-stu-id="744a9-323">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="744a9-324">Vous pouvez combiner les attributs et l’API Fluent si vous le voulez, et il existe quelques personnalisations qui peuvent être effectuées uniquement à l’aide de l’API Fluent, mais en général la pratique recommandée consiste à choisir l’une de ces deux approches et à l’utiliser constamment, autant que possible.</span><span class="sxs-lookup"><span data-stu-id="744a9-324">You can mix attributes and fluent API if you want, and there are a few customizations that can only be done by using fluent API, but in general the recommended practice is to choose one of these two approaches and use that consistently as much as possible.</span></span> <span data-ttu-id="744a9-325">Si vous utilisez ces deux approches, notez que partout où il existe un conflit, l’API Fluent a priorité sur les attributs.</span><span class="sxs-lookup"><span data-stu-id="744a9-325">If you do use both, note that wherever there's a conflict, Fluent API overrides attributes.</span></span>

<span data-ttu-id="744a9-326">Pour plus d’informations sur les attributs et l’API Fluent, consultez [Méthodes de configuration](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="744a9-326">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="744a9-327">Diagramme des entités montrant les relations</span><span class="sxs-lookup"><span data-stu-id="744a9-327">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="744a9-328">L’illustration suivante montre le diagramme que les outils Entity Framework Power Tools créent pour le modèle School complet.</span><span class="sxs-lookup"><span data-stu-id="744a9-328">The following illustration shows the diagram that the Entity Framework Power Tools create for the completed School model.</span></span>

![Diagramme des entités](complex-data-model/_static/diagram.png)

<span data-ttu-id="744a9-330">Outre les lignes de relation un-à-plusieurs (1 à \*), vous pouvez voir ici la ligne de relation un-à-zéro-ou-un (1 à 0..1) entre les entités Instructor et OfficeAssignment et la ligne de relation zéro-ou-un-à-plusieurs (0..1 à \*) entre les entités Instructor et Department.</span><span class="sxs-lookup"><span data-stu-id="744a9-330">Besides the one-to-many relationship lines (1 to \*), you can see here the one-to-zero-or-one relationship line (1 to 0..1) between the Instructor and OfficeAssignment entities and the zero-or-one-to-many relationship line (0..1 to \*) between the Instructor and Department entities.</span></span>

## <a name="seed-database-with-test-data"></a><span data-ttu-id="744a9-331">Remplir la base de données avec des données de test</span><span class="sxs-lookup"><span data-stu-id="744a9-331">Seed database with test data</span></span>

<span data-ttu-id="744a9-332">Remplacez le code dans le fichier *Data/DbInitializer.cs* par le code suivant afin de fournir des données initiales pour les nouvelles entités que vous avez créées.</span><span class="sxs-lookup"><span data-stu-id="744a9-332">Replace the code in the *Data/DbInitializer.cs* file with the following code in order to provide seed data for the new entities you've created.</span></span>

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="744a9-333">Comme vous l’avez vu dans le premier didacticiel, la majeure partie de ce code crée simplement de nouveaux objets d’entité et charge des exemples de données dans les propriétés requises pour les tests.</span><span class="sxs-lookup"><span data-stu-id="744a9-333">As you saw in the first tutorial, most of this code simply creates new entity objects and loads sample data into properties as required for testing.</span></span> <span data-ttu-id="744a9-334">Notez la façon dont les relations plusieurs à plusieurs sont gérées : le code crée des relations en créant des entités dans les jeux d’entités de jointure `Enrollments` et `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="744a9-334">Notice how the many-to-many relationships are handled: the code creates relationships by creating entities in the `Enrollments` and `CourseAssignment` join entity sets.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="744a9-335">Ajouter une migration</span><span class="sxs-lookup"><span data-stu-id="744a9-335">Add a migration</span></span>

<span data-ttu-id="744a9-336">Enregistrez vos modifications et générez le projet.</span><span class="sxs-lookup"><span data-stu-id="744a9-336">Save your changes and build the project.</span></span> <span data-ttu-id="744a9-337">Ensuite, ouvrez la fenêtre de commande dans le dossier du projet et entrez la commande `migrations add` (n’exécutez pas encore la commande de mise à jour de base de données) :</span><span class="sxs-lookup"><span data-stu-id="744a9-337">Then open the command window in the project folder and enter the `migrations add` command (don't do the update-database command yet):</span></span>

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

<span data-ttu-id="744a9-338">Vous obtenez un avertissement concernant une perte possible de données.</span><span class="sxs-lookup"><span data-stu-id="744a9-338">You get a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data. Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="744a9-339">Si vous tentiez d’exécuter la commande `database update` à ce stade (ne le faites pas encore), vous obtiendriez l’erreur suivante :</span><span class="sxs-lookup"><span data-stu-id="744a9-339">If you tried to run the `database update` command at this point (don't do it yet), you would get the following error:</span></span>

> <span data-ttu-id="744a9-340">L’instruction ALTER TABLE est en conflit avec la contrainte FOREIGN KEY « FK_dbo.Course_dbo.Department_DepartmentID ».</span><span class="sxs-lookup"><span data-stu-id="744a9-340">The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID".</span></span> <span data-ttu-id="744a9-341">Le conflit s’est produit dans la base de données « ContosoUniversity », table « dbo.Department », colonne « DepartmentID ».</span><span class="sxs-lookup"><span data-stu-id="744a9-341">The conflict occurred in database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.</span></span>

<span data-ttu-id="744a9-342">Parfois, lorsque vous exécutez des migrations avec des données existantes, vous devez insérer des données stub dans la base de données pour répondre aux contraintes de clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="744a9-342">Sometimes when you execute migrations with existing data, you need to insert stub data into the database to satisfy foreign key constraints.</span></span> <span data-ttu-id="744a9-343">Le code généré dans la méthode `Up` ajoute une clé étrangère DepartmentID non nullable à la table Course.</span><span class="sxs-lookup"><span data-stu-id="744a9-343">The generated code in the `Up` method adds a non-nullable DepartmentID foreign key to the Course table.</span></span> <span data-ttu-id="744a9-344">S’il existe déjà des lignes dans la table Course lorsque le code s’exécute, l’opération `AddColumn` échoue car SQL Server ne sait pas quelle valeur placer dans la colonne qui ne peut pas être null.</span><span class="sxs-lookup"><span data-stu-id="744a9-344">If there are already rows in the Course table when the code runs, the `AddColumn` operation fails because SQL Server doesn't know what value to put in the column that can't be null.</span></span> <span data-ttu-id="744a9-345">Pour ce didacticiel, vous allez exécuter la migration sur une nouvelle base de données. Toutefois, dans une application de production, vous devriez faire en sorte que la migration traite les données existantes, si bien que les instructions suivantes montrent un exemple de la procédure à suivre pour ce faire.</span><span class="sxs-lookup"><span data-stu-id="744a9-345">For this tutorial you'll run the migration on a new database, but in a production application you'd have to make the migration handle existing data, so the following directions show an example of how to do that.</span></span>

<span data-ttu-id="744a9-346">Pour faire en sorte que cette migration fonctionne avec les données existantes, vous devez modifier le code pour attribuer à la nouvelle colonne une valeur par défaut et créer un département stub nommé « Temp » qui agira en tant que département par défaut.</span><span class="sxs-lookup"><span data-stu-id="744a9-346">To make this migration work with existing data you have to change the code to give the new column a default value, and create a stub department named "Temp" to act as the default department.</span></span> <span data-ttu-id="744a9-347">Par conséquent, les lignes Course existantes seront toutes associées au département « Temp » après l’exécution de la méthode `Up`.</span><span class="sxs-lookup"><span data-stu-id="744a9-347">As a result, existing Course rows will all be related to the "Temp" department after the `Up` method runs.</span></span>

* <span data-ttu-id="744a9-348">Ouvrez le fichier *{timestamp}_ComplexDataModel.cs*.</span><span class="sxs-lookup"><span data-stu-id="744a9-348">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>

* <span data-ttu-id="744a9-349">Commentez la ligne de code qui ajoute la colonne DepartmentID à la table Course.</span><span class="sxs-lookup"><span data-stu-id="744a9-349">Comment out the line of code that adds the DepartmentID column to the Course table.</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

* <span data-ttu-id="744a9-350">Ajoutez le code en surbrillance suivant après le code qui crée la table Department :</span><span class="sxs-lookup"><span data-stu-id="744a9-350">Add the following highlighted code after the code that creates the Department table:</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="744a9-351">Dans une application de production, vous devez écrire un code ou des scripts pour ajouter des lignes Department et associer des lignes Course aux nouvelles lignes Department.</span><span class="sxs-lookup"><span data-stu-id="744a9-351">In a production application, you would write code or scripts to add Department rows and relate Course rows to the new Department rows.</span></span> <span data-ttu-id="744a9-352">Vous n’avez alors plus besoin du département « Temp » ni de la valeur par défaut sur la colonne Course.DepartmentID.</span><span class="sxs-lookup"><span data-stu-id="744a9-352">You would then no longer need the "Temp" department or the default value on the Course.DepartmentID column.</span></span>

<span data-ttu-id="744a9-353">Enregistrez vos modifications et générez le projet.</span><span class="sxs-lookup"><span data-stu-id="744a9-353">Save your changes and build the project.</span></span>

## <a name="change-the-connection-string"></a><span data-ttu-id="744a9-354">Changer la chaîne de connexion</span><span class="sxs-lookup"><span data-stu-id="744a9-354">Change the connection string</span></span>

<span data-ttu-id="744a9-355">Vous avez maintenant un nouveau code dans la classe `DbInitializer` qui ajoute des données initiales pour les nouvelles entités à une base de données vide.</span><span class="sxs-lookup"><span data-stu-id="744a9-355">You now have new code in the `DbInitializer` class that adds seed data for the new entities to an empty database.</span></span> <span data-ttu-id="744a9-356">Pour rendre EF créer une nouvelle base de données vide, modifiez le nom de la base de données dans la chaîne de connexion dans *appsettings.json* par contosouniversity3 ou un autre nom que vous n’avez pas utilisé sur l’ordinateur que vous utilisez.</span><span class="sxs-lookup"><span data-stu-id="744a9-356">To make EF create a new empty database, change the name of the database in the connection string in *appsettings.json* to ContosoUniversity3 or some other name that you haven't used on the computer you're using.</span></span>

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=ContosoUniversity3;Trusted_Connection=True;MultipleActiveResultSets=true"
  },
```

<span data-ttu-id="744a9-357">Enregistrez vos modifications dans *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="744a9-357">Save your change to *appsettings.json*.</span></span>

> [!NOTE]
> <span data-ttu-id="744a9-358">Comme alternative au changement de nom de la base de données, vous pouvez supprimer la base de données.</span><span class="sxs-lookup"><span data-stu-id="744a9-358">As an alternative to changing the database name, you can delete the database.</span></span> <span data-ttu-id="744a9-359">Utilisez **SQL Server Object Explorer** (SSOX) ou la commande CLI `database drop` :</span><span class="sxs-lookup"><span data-stu-id="744a9-359">Use **SQL Server Object Explorer** (SSOX) or the `database drop` CLI command:</span></span>
>
> ```dotnetcli
> dotnet ef database drop
> ```

## <a name="update-the-database"></a><span data-ttu-id="744a9-360">Mettre à jour la base de données</span><span class="sxs-lookup"><span data-stu-id="744a9-360">Update the database</span></span>

<span data-ttu-id="744a9-361">Une fois que vous avez modifié le nom de la base de données ou supprimé la base de données, exécutez la commande `database update` dans la fenêtre de commande pour exécuter les migrations.</span><span class="sxs-lookup"><span data-stu-id="744a9-361">After you have changed the database name or deleted the database, run the `database update` command in the command window to execute the migrations.</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="744a9-362">Exécutez l’application pour que la méthode `DbInitializer.Initialize` exécute la nouvelle base de données et la remplisse.</span><span class="sxs-lookup"><span data-stu-id="744a9-362">Run the app to cause the `DbInitializer.Initialize` method to run and populate the new database.</span></span>

<span data-ttu-id="744a9-363">Ouvrez la base de données dans SSOX comme vous l’avez fait précédemment, puis développez le nœud **Tables** pour voir que toutes les tables ont été créées.</span><span class="sxs-lookup"><span data-stu-id="744a9-363">Open the database in SSOX as you did earlier, and expand the **Tables** node to see that all of the tables have been created.</span></span> <span data-ttu-id="744a9-364">(Si SSOX est resté ouvert, cliquez sur le bouton **Actualiser**.)</span><span class="sxs-lookup"><span data-stu-id="744a9-364">(If you still have SSOX open from the earlier time, click the **Refresh** button.)</span></span>

![Tables dans SSOX](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="744a9-366">Exécutez l’application pour déclencher le code d’initialiseur qui remplit la base de données.</span><span class="sxs-lookup"><span data-stu-id="744a9-366">Run the app to trigger the initializer code that seeds the database.</span></span>

<span data-ttu-id="744a9-367">Cliquez avec le bouton droit sur la table **CourseAssignment** et sélectionnez **Afficher les données** pour vérifier qu’elle comporte des données.</span><span class="sxs-lookup"><span data-stu-id="744a9-367">Right-click the **CourseAssignment** table and select **View Data** to verify that it has data in it.</span></span>

![Données CourseAssignment dans SSOX](complex-data-model/_static/ssox-ci-data.png)

## <a name="get-the-code"></a><span data-ttu-id="744a9-369">Obtenir le code</span><span class="sxs-lookup"><span data-stu-id="744a9-369">Get the code</span></span>

[<span data-ttu-id="744a9-370">Télécharger ou afficher l’application complète.</span><span class="sxs-lookup"><span data-stu-id="744a9-370">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="744a9-371">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="744a9-371">Next steps</span></span>

<span data-ttu-id="744a9-372">Dans ce tutoriel, vous allez :</span><span class="sxs-lookup"><span data-stu-id="744a9-372">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="744a9-373">Personnaliser le modèle de données</span><span class="sxs-lookup"><span data-stu-id="744a9-373">Customized the Data model</span></span>
> * <span data-ttu-id="744a9-374">Apporter des modifications à l’entité Student</span><span class="sxs-lookup"><span data-stu-id="744a9-374">Made changes to Student entity</span></span>
> * <span data-ttu-id="744a9-375">Créer une entité Instructor</span><span class="sxs-lookup"><span data-stu-id="744a9-375">Created Instructor entity</span></span>
> * <span data-ttu-id="744a9-376">Créer une entité OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="744a9-376">Created OfficeAssignment entity</span></span>
> * <span data-ttu-id="744a9-377">Modifier l’entité Course</span><span class="sxs-lookup"><span data-stu-id="744a9-377">Modified Course entity</span></span>
> * <span data-ttu-id="744a9-378">Créer l’entité Department</span><span class="sxs-lookup"><span data-stu-id="744a9-378">Created Department entity</span></span>
> * <span data-ttu-id="744a9-379">Modifier l’entité Enrollment</span><span class="sxs-lookup"><span data-stu-id="744a9-379">Modified Enrollment entity</span></span>
> * <span data-ttu-id="744a9-380">Mettre à jour le contexte de base de données</span><span class="sxs-lookup"><span data-stu-id="744a9-380">Updated the database context</span></span>
> * <span data-ttu-id="744a9-381">Remplir la base de données avec des données de test</span><span class="sxs-lookup"><span data-stu-id="744a9-381">Seeded database with test data</span></span>
> * <span data-ttu-id="744a9-382">Ajouter une migration</span><span class="sxs-lookup"><span data-stu-id="744a9-382">Added a migration</span></span>
> * <span data-ttu-id="744a9-383">Changer la chaîne de connexion</span><span class="sxs-lookup"><span data-stu-id="744a9-383">Changed the connection string</span></span>
> * <span data-ttu-id="744a9-384">Base de données mise à jour</span><span class="sxs-lookup"><span data-stu-id="744a9-384">Updated the database</span></span>

<span data-ttu-id="744a9-385">Passez au tutoriel suivant pour en savoir plus sur l’accès aux données associées.</span><span class="sxs-lookup"><span data-stu-id="744a9-385">Advance to the next tutorial to learn more about how to access related data.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="744a9-386">Suivant : accéder aux données associées</span><span class="sxs-lookup"><span data-stu-id="744a9-386">Next: Access related data</span></span>](read-related-data.md)
