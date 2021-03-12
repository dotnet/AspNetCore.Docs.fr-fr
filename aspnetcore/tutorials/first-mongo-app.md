---
title: Créer une API web avec ASP.NET Core et MongoDB
author: prkhandelwal
description: Ce tutoriel montre comment créer une API web ASP.NET Core à l’aide d’une base de données NoSQL MongoDB.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 08/17/2019
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
uid: tutorials/first-mongo-app
ms.openlocfilehash: 489a15f2011e44ffd9f9bfe7d5410b7d79a10632
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587604"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="c3499-103">Créer une API web avec ASP.NET Core et MongoDB</span><span class="sxs-lookup"><span data-stu-id="c3499-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="c3499-104">Par [Pratik Khandelwal](https://twitter.com/K2Prk) et [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="c3499-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c3499-105">Ce didacticiel crée une API web qui effectue des opérations de création, lecture, mise à jour et suppression (CRUD) sur une base de données NoSQL [MongoDB](https://www.mongodb.com/what-is-mongodb).</span><span class="sxs-lookup"><span data-stu-id="c3499-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="c3499-106">Dans ce tutoriel, vous allez apprendre à :</span><span class="sxs-lookup"><span data-stu-id="c3499-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c3499-107">Configurer MongoDB</span><span class="sxs-lookup"><span data-stu-id="c3499-107">Configure MongoDB</span></span>
> * <span data-ttu-id="c3499-108">Créer une base de données MongoDB</span><span class="sxs-lookup"><span data-stu-id="c3499-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="c3499-109">Définir une collection et un schéma MongoDB</span><span class="sxs-lookup"><span data-stu-id="c3499-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="c3499-110">Effectuer des opérations CRUD MongoDB à partir d’une API web</span><span class="sxs-lookup"><span data-stu-id="c3499-110">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="c3499-111">Personnaliser la sérialisation JSON</span><span class="sxs-lookup"><span data-stu-id="c3499-111">Customize JSON serialization</span></span>

<span data-ttu-id="c3499-112">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-mongo-app/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c3499-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c3499-113">Prérequis</span><span class="sxs-lookup"><span data-stu-id="c3499-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c3499-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c3499-114">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="c3499-115">SDK .NET Core 3.0 ou ultérieur</span><span class="sxs-lookup"><span data-stu-id="c3499-115">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="c3499-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) avec la charge de travail de **développement Web et ASP.net**</span><span class="sxs-lookup"><span data-stu-id="c3499-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="c3499-117">MongoDB</span><span class="sxs-lookup"><span data-stu-id="c3499-117">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="c3499-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c3499-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="c3499-119">SDK .NET Core 3.0 ou ultérieur</span><span class="sxs-lookup"><span data-stu-id="c3499-119">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="c3499-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c3499-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="c3499-121">C# pour Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c3499-121">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="c3499-122">MongoDB</span><span class="sxs-lookup"><span data-stu-id="c3499-122">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c3499-123">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="c3499-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="c3499-124">SDK .NET Core 3.0 ou ultérieur</span><span class="sxs-lookup"><span data-stu-id="c3499-124">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="c3499-125">Visual Studio pour Mac 7.7 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="c3499-125">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="c3499-126">MongoDB</span><span class="sxs-lookup"><span data-stu-id="c3499-126">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="c3499-127">Configurer MongoDB</span><span class="sxs-lookup"><span data-stu-id="c3499-127">Configure MongoDB</span></span>

<span data-ttu-id="c3499-128">Si vous utilisez Windows, MongoDB est installé par défaut dans *C:\\Program Files\\MongoDB*.</span><span class="sxs-lookup"><span data-stu-id="c3499-128">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="c3499-129">Ajoutez *C : \\ Program Files de l' \\ \\ \\ \<version_number> \\ emplacement du serveur MongoDB* à la `Path` variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="c3499-129">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="c3499-130">Cette modification permet d’accéder à MongoDB depuis n’importe quel emplacement sur votre ordinateur de développement.</span><span class="sxs-lookup"><span data-stu-id="c3499-130">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="c3499-131">Utilisez l’interpréteur de commandes mongo dans les étapes suivantes pour créer une base de données, des collections, et stocker des documents.</span><span class="sxs-lookup"><span data-stu-id="c3499-131">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="c3499-132">Pour plus d’informations sur les commandes de l’interpréteur mongo, consultez [Utilisation de l’interpréteur de commandes mongo](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="c3499-132">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="c3499-133">Choisissez sur votre ordinateur de développement un répertoire pour le stockage des données.</span><span class="sxs-lookup"><span data-stu-id="c3499-133">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="c3499-134">Par exemple, *C:\\BooksData* sous Windows.</span><span class="sxs-lookup"><span data-stu-id="c3499-134">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="c3499-135">Le cas échéant, créez le répertoire.</span><span class="sxs-lookup"><span data-stu-id="c3499-135">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="c3499-136">L’interpréteur de commandes mongo ne crée pas nouveaux répertoires.</span><span class="sxs-lookup"><span data-stu-id="c3499-136">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="c3499-137">Ouvrez un interpréteur de commandes.</span><span class="sxs-lookup"><span data-stu-id="c3499-137">Open a command shell.</span></span> <span data-ttu-id="c3499-138">Exécutez la commande suivante pour vous connecter à MongoDB sur le port par défaut 27017.</span><span class="sxs-lookup"><span data-stu-id="c3499-138">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="c3499-139">N’oubliez pas de remplacer `<data_directory_path>` par le répertoire que vous avez choisi à l’étape précédente.</span><span class="sxs-lookup"><span data-stu-id="c3499-139">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="c3499-140">Ouvrez une autre instance de l’interpréteur de commandes.</span><span class="sxs-lookup"><span data-stu-id="c3499-140">Open another command shell instance.</span></span> <span data-ttu-id="c3499-141">Connectez-vous à la base de données de test par défaut en exécutant la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="c3499-141">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="c3499-142">Utilisez la ligne suivante dans l’interpréteur de commandes :</span><span class="sxs-lookup"><span data-stu-id="c3499-142">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="c3499-143">Le cas échéant, une base de données nommée *BookstoreDb* est créée.</span><span class="sxs-lookup"><span data-stu-id="c3499-143">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="c3499-144">Si la base de données existe déjà, sa connexion est ouverte pour les transactions.</span><span class="sxs-lookup"><span data-stu-id="c3499-144">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="c3499-145">Créez une collection `Books` à l’aide de la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="c3499-145">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="c3499-146">Le résultat suivant s’affiche :</span><span class="sxs-lookup"><span data-stu-id="c3499-146">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="c3499-147">Définissez un schéma pour la collection `Books` et insérez deux documents à l’aide de la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="c3499-147">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="c3499-148">Le résultat suivant s’affiche :</span><span class="sxs-lookup"><span data-stu-id="c3499-148">The following result is displayed:</span></span>

   ```console
   {
     "acknowledged" : true,
     "insertedIds" : [
       ObjectId("5bfd996f7b8e48dc15ff215d"),
       ObjectId("5bfd996f7b8e48dc15ff215e")
     ]
   }
   ```
  
   > [!NOTE]
   > <span data-ttu-id="c3499-149">Les ID indiqués dans cet article ne correspondent pas aux ID obtenus quand vous exécutez cet exemple.</span><span class="sxs-lookup"><span data-stu-id="c3499-149">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="c3499-150">Affichez les documents de la base de données à l’aide de la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="c3499-150">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="c3499-151">Le résultat suivant s’affiche :</span><span class="sxs-lookup"><span data-stu-id="c3499-151">The following result is displayed:</span></span>

   ```console
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
     "Name" : "Design Patterns",
     "Price" : 54.93,
     "Category" : "Computers",
     "Author" : "Ralph Johnson"
   }
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
     "Name" : "Clean Code",
     "Price" : 43.15,
     "Category" : "Computers",
     "Author" : "Robert C. Martin"
   }
   ```

   <span data-ttu-id="c3499-152">Le schéma ajoute une propriété `_id` automatiquement générée de type `ObjectId` pour chaque document.</span><span class="sxs-lookup"><span data-stu-id="c3499-152">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="c3499-153">La base de données est en lecture seule.</span><span class="sxs-lookup"><span data-stu-id="c3499-153">The database is ready.</span></span> <span data-ttu-id="c3499-154">Vous pouvez commencer à créer l’API web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c3499-154">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="c3499-155">Créer le projet d’API web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c3499-155">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c3499-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c3499-156">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="c3499-157">Accédez à **fichier** > **nouveau** > **projet**.</span><span class="sxs-lookup"><span data-stu-id="c3499-157">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="c3499-158">Sélectionnez le type de projet **Application web ASP.NET Core**, puis sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="c3499-158">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="c3499-159">Nommez le projet *BooksApi*, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="c3499-159">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="c3499-160">Sélectionnez le framework cible **.NET Core** et **ASP.NET Core 3.0**.</span><span class="sxs-lookup"><span data-stu-id="c3499-160">Select the **.NET Core** target framework and **ASP.NET Core 3.0**.</span></span> <span data-ttu-id="c3499-161">Sélectionnez le modèle de projet **API**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="c3499-161">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="c3499-162">Visitez la [galerie NuGet : MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) pour déterminer la dernière version stable du pilote .net pour MongoDB.</span><span class="sxs-lookup"><span data-stu-id="c3499-162">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="c3499-163">Dans la fenêtre **Console du Gestionnaire de Package**, accédez à la racine du projet.</span><span class="sxs-lookup"><span data-stu-id="c3499-163">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="c3499-164">Exécutez la commande suivante afin d’installer le pilote .NET pour MongoDB :</span><span class="sxs-lookup"><span data-stu-id="c3499-164">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="c3499-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c3499-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="c3499-166">Exécutez les commandes suivantes dans l’interpréteur de commandes :</span><span class="sxs-lookup"><span data-stu-id="c3499-166">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="c3499-167">Un nouveau projet API web ASP.NET Core ciblant .NET Core est généré et ouvert dans Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="c3499-167">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="c3499-168">Une fois que l’icône de flamme OmniSharp de la barre d’État devient verte, une boîte de dialogue demande les **ressources requises à générer et à déboguer manquantes dans « BooksApi ». Ajoutez-les ?**.</span><span class="sxs-lookup"><span data-stu-id="c3499-168">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="c3499-169">Sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="c3499-169">Select **Yes**.</span></span>
1. <span data-ttu-id="c3499-170">Visitez la [galerie NuGet : MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) pour déterminer la dernière version stable du pilote .net pour MongoDB.</span><span class="sxs-lookup"><span data-stu-id="c3499-170">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="c3499-171">Ouvrez **Terminal intégré** et accédez à la racine du projet.</span><span class="sxs-lookup"><span data-stu-id="c3499-171">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="c3499-172">Exécutez la commande suivante afin d’installer le pilote .NET pour MongoDB :</span><span class="sxs-lookup"><span data-stu-id="c3499-172">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c3499-173">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="c3499-173">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="c3499-174">Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez **fichier**  >  **nouvelle solution**  >  **application .net Core**  >   dans la barre latérale.</span><span class="sxs-lookup"><span data-stu-id="c3499-174">In Visual Studio for Mac earlier than version 8.6, select **File** > **New Solution** > **.NET Core** > **App** from the sidebar.</span></span> <span data-ttu-id="c3499-175">Dans la version 8,6 ou ultérieure, sélectionnez **fichier**  >  **nouvelle solution**  >  **Web et**  >  **application** console dans la barre latérale.</span><span class="sxs-lookup"><span data-stu-id="c3499-175">In version 8.6 or later, select **File** > **New Solution** > **Web and Console** > **App** from the sidebar.</span></span>
1. <span data-ttu-id="c3499-176">Sélectionnez le modèle de projet **ASP.net Core** > **API** C#, puis sélectionnez **suivant**.</span><span class="sxs-lookup"><span data-stu-id="c3499-176">Select the **ASP.NET Core** > **API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="c3499-177">Sélectionnez **.net Core 3,1** dans la liste déroulante **Framework cible** , puis sélectionnez **suivant**.</span><span class="sxs-lookup"><span data-stu-id="c3499-177">Select **.NET Core 3.1** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="c3499-178">Entrez *BooksApi* pour **Nom de projet**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="c3499-178">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="c3499-179">Dans le panneau **Solution**, cliquez avec le bouton droit sur le nœud **Dépendances** du projet, puis sélectionnez **Ajouter des packages**.</span><span class="sxs-lookup"><span data-stu-id="c3499-179">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="c3499-180">Entrez *MongoDB.Driver* dans la zone de recherche, sélectionnez le package *MongoDB.Driver*, puis sélectionnez **Ajouter un package**.</span><span class="sxs-lookup"><span data-stu-id="c3499-180">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="c3499-181">Sélectionnez le bouton **Accepter** dans la boîte de dialogue **Acceptation de la licence**.</span><span class="sxs-lookup"><span data-stu-id="c3499-181">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="c3499-182">Ajouter un modèle d’entité</span><span class="sxs-lookup"><span data-stu-id="c3499-182">Add an entity model</span></span>

1. <span data-ttu-id="c3499-183">Ajoutez un répertoire *Models* à la racine du projet.</span><span class="sxs-lookup"><span data-stu-id="c3499-183">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="c3499-184">Ajoutez une classe `Book` au répertoire *Modèles* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="c3499-184">Add a `Book` class to the *Models* directory with the following code:</span></span>

   ```csharp
   using MongoDB.Bson;
   using MongoDB.Bson.Serialization.Attributes;

   namespace BooksApi.Models
   {
       public class Book
       {
           [BsonId]
           [BsonRepresentation(BsonType.ObjectId)]
           public string Id { get; set; }

           [BsonElement("Name")]
           public string BookName { get; set; }

           public decimal Price { get; set; }

           public string Category { get; set; }

           public string Author { get; set; }
       }
   }
   ```

   <span data-ttu-id="c3499-185">Dans la classe précédente, la propriété `Id` :</span><span class="sxs-lookup"><span data-stu-id="c3499-185">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="c3499-186">Est requise pour mapper l’objet Common Language Runtime (CLR) à la collection MongoDB.</span><span class="sxs-lookup"><span data-stu-id="c3499-186">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="c3499-187">Est annoté avec [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) pour désigner cette propriété comme clé primaire du document.</span><span class="sxs-lookup"><span data-stu-id="c3499-187">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="c3499-188">Est annoté avec [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) pour permettre la transmission du paramètre en tant que type `string` au lieu d’une structure [ObjectID](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) .</span><span class="sxs-lookup"><span data-stu-id="c3499-188">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="c3499-189">Mongo gère la conversion de `string` en `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="c3499-189">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="c3499-190">La `BookName` propriété est annotée avec l' [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribut.</span><span class="sxs-lookup"><span data-stu-id="c3499-190">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="c3499-191">La valeur de l’attribut de `Name` représente le nom de propriété dans la collection MongoDB.</span><span class="sxs-lookup"><span data-stu-id="c3499-191">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="c3499-192">Ajouter un modèle de configuration</span><span class="sxs-lookup"><span data-stu-id="c3499-192">Add a configuration model</span></span>

1. <span data-ttu-id="c3499-193">Ajoutez les valeurs de configuration de la base de données suivantes à *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="c3499-193">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="c3499-194">Ajoutez un fichier *BookstoreDatabaseSettings.cs* au répertoire *Models* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="c3499-194">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="c3499-195">La `BookstoreDatabaseSettings` classe précédente est utilisée pour stocker les *appsettings.json* valeurs de `BookstoreDatabaseSettings` propriété du fichier.</span><span class="sxs-lookup"><span data-stu-id="c3499-195">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="c3499-196">Les noms de propriétés JSON et C# sont nommés de manière identique pour faciliter le processus de mappage.</span><span class="sxs-lookup"><span data-stu-id="c3499-196">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="c3499-197">Ajoutez le code en surbrillance suivant à `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c3499-197">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-8)]

   <span data-ttu-id="c3499-198">Dans le code précédent :</span><span class="sxs-lookup"><span data-stu-id="c3499-198">In the preceding code:</span></span>

   * <span data-ttu-id="c3499-199">L’instance de configuration vers laquelle la *appsettings.json* section du fichier `BookstoreDatabaseSettings` est liée est inscrite dans le conteneur d’injection de dépendance (di).</span><span class="sxs-lookup"><span data-stu-id="c3499-199">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="c3499-200">Par exemple, `BookstoreDatabaseSettings` la propriété d’un objet `ConnectionString` est remplie avec la `BookstoreDatabaseSettings:ConnectionString` propriété dans *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="c3499-200">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="c3499-201">L’interface `IBookstoreDatabaseSettings` est inscrite auprès de l’injection de dépendances avec une [durée de vie de service](xref:fundamentals/dependency-injection#service-lifetimes) de singleton.</span><span class="sxs-lookup"><span data-stu-id="c3499-201">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="c3499-202">Une fois injectée, l’instance d’interface est résolue en objet `BookstoreDatabaseSettings`.</span><span class="sxs-lookup"><span data-stu-id="c3499-202">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="c3499-203">Ajoutez le code suivant en haut de *Startup.cs* pour résoudre les références à `BookstoreDatabaseSettings` et `IBookstoreDatabaseSettings` :</span><span class="sxs-lookup"><span data-stu-id="c3499-203">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="c3499-204">Ajouter un service d’opérations CRUD</span><span class="sxs-lookup"><span data-stu-id="c3499-204">Add a CRUD operations service</span></span>

1. <span data-ttu-id="c3499-205">Ajoutez un répertoire *Services* à la racine du projet.</span><span class="sxs-lookup"><span data-stu-id="c3499-205">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="c3499-206">Ajoutez une classe `BookService` au répertoire *Services* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="c3499-206">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="c3499-207">Dans le code précédent, une instance de `IBookstoreDatabaseSettings` est récupérée à partir de l’injection de dépendances via l’injection de constructeur.</span><span class="sxs-lookup"><span data-stu-id="c3499-207">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="c3499-208">Cette technique permet d’accéder aux *appsettings.json* valeurs de configuration qui ont été ajoutées dans la section [Ajouter un modèle de configuration](#add-a-configuration-model) .</span><span class="sxs-lookup"><span data-stu-id="c3499-208">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="c3499-209">Ajoutez le code en surbrillance suivant à `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c3499-209">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="c3499-210">Dans le code précédent, la classe `BookService` est inscrite auprès de l’injection de dépendances pour permettre la prise en charge de l’injection de constructeur dans les classes consommatrices.</span><span class="sxs-lookup"><span data-stu-id="c3499-210">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="c3499-211">La durée de vie de service de singleton est la plus appropriée, car `BookService` a une dépendance directe sur `MongoClient`.</span><span class="sxs-lookup"><span data-stu-id="c3499-211">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="c3499-212">Selon les [instructions de réutilisation du client Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)officielles, `MongoClient` doit être inscrit dans di avec une durée de vie de service Singleton.</span><span class="sxs-lookup"><span data-stu-id="c3499-212">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="c3499-213">Ajoutez le code suivant en haut de *Startup.cs* pour résoudre la référence à `BookService` :</span><span class="sxs-lookup"><span data-stu-id="c3499-213">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="c3499-214">La classe `BookService` utilise les membres `MongoDB.Driver` suivants pour effectuer des opérations CRUD dans la base de données :</span><span class="sxs-lookup"><span data-stu-id="c3499-214">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="c3499-215">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): lit l’instance de serveur pour effectuer des opérations de base de données.</span><span class="sxs-lookup"><span data-stu-id="c3499-215">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): Reads the server instance for performing database operations.</span></span> <span data-ttu-id="c3499-216">Le constructeur de cette classe reçoit la chaîne de connexion MongoDB :</span><span class="sxs-lookup"><span data-stu-id="c3499-216">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="c3499-217">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): représente la base de données Mongo pour l’exécution d’opérations.</span><span class="sxs-lookup"><span data-stu-id="c3499-217">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="c3499-218">Ce tutoriel utilise la méthode générique [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) sur l’interface pour accéder aux données d’une collection spécifique.</span><span class="sxs-lookup"><span data-stu-id="c3499-218">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="c3499-219">Effectuez des opérations CRUD sur la collection, après l’appel de cette méthode.</span><span class="sxs-lookup"><span data-stu-id="c3499-219">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="c3499-220">Dans l’appel à la méthode `GetCollection<TDocument>(collection)` :</span><span class="sxs-lookup"><span data-stu-id="c3499-220">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="c3499-221">`collection` représente le nom de la collection.</span><span class="sxs-lookup"><span data-stu-id="c3499-221">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="c3499-222">`TDocument` représente le type d’objet CLR stocké dans la collection.</span><span class="sxs-lookup"><span data-stu-id="c3499-222">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="c3499-223">`GetCollection<TDocument>(collection)` retourne un objet [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) représentant la collection.</span><span class="sxs-lookup"><span data-stu-id="c3499-223">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="c3499-224">Dans ce didacticiel, les méthodes suivantes sont appelées sur la collection :</span><span class="sxs-lookup"><span data-stu-id="c3499-224">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="c3499-225">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): supprime un document unique correspondant aux critères de recherche fournis.</span><span class="sxs-lookup"><span data-stu-id="c3499-225">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="c3499-226">[Find \<TDocument> ](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): retourne tous les documents de la collection correspondant aux critères de recherche fournis.</span><span class="sxs-lookup"><span data-stu-id="c3499-226">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="c3499-227">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): insère l’objet fourni sous la forme d’un nouveau document dans la collection.</span><span class="sxs-lookup"><span data-stu-id="c3499-227">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="c3499-228">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): remplace le document unique qui correspond aux critères de recherche fournis par l’objet fourni.</span><span class="sxs-lookup"><span data-stu-id="c3499-228">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="c3499-229">Ajout d'un contrôleur</span><span class="sxs-lookup"><span data-stu-id="c3499-229">Add a controller</span></span>

<span data-ttu-id="c3499-230">Ajoutez une classe `BooksController` au répertoire *Controllers* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="c3499-230">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="c3499-231">Le contrôleur d’API web précédent :</span><span class="sxs-lookup"><span data-stu-id="c3499-231">The preceding web API controller:</span></span>

* <span data-ttu-id="c3499-232">Utilise la classe `BookService` pour effectuer des opérations CRUD.</span><span class="sxs-lookup"><span data-stu-id="c3499-232">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="c3499-233">Contient des méthodes d’action pour prendre en charge les requêtes HTTP GET, POST, PUT et DELETE.</span><span class="sxs-lookup"><span data-stu-id="c3499-233">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="c3499-234">Appelle <xref:System.Web.Http.ApiController.CreatedAtRoute*> dans la méthode d’action `Create` pour retourner une réponse [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="c3499-234">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="c3499-235">Le code d’état 201 est la réponse standard d’une méthode HTTP POST qui crée une ressource sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="c3499-235">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="c3499-236">`CreatedAtRoute` ajoute également un en-tête `Location` à la réponse.</span><span class="sxs-lookup"><span data-stu-id="c3499-236">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="c3499-237">L’en-tête `Location` spécifie l’URI du livre qui vient d’être créé.</span><span class="sxs-lookup"><span data-stu-id="c3499-237">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="c3499-238">Tester l’API web</span><span class="sxs-lookup"><span data-stu-id="c3499-238">Test the web API</span></span>

1. <span data-ttu-id="c3499-239">Générez et exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="c3499-239">Build and run the app.</span></span>

1. <span data-ttu-id="c3499-240">Accédez à `http://localhost:<port>/api/books` pour tester la méthode d’action `Get` sans paramètre du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="c3499-240">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="c3499-241">La réponse JSON suivante apparaît :</span><span class="sxs-lookup"><span data-stu-id="c3499-241">The following JSON response is displayed:</span></span>

   ```json
   [
     {
       "id":"5bfd996f7b8e48dc15ff215d",
       "bookName":"Design Patterns",
       "price":54.93,
       "category":"Computers",
       "author":"Ralph Johnson"
     },
     {
       "id":"5bfd996f7b8e48dc15ff215e",
       "bookName":"Clean Code",
       "price":43.15,
       "category":"Computers",
       "author":"Robert C. Martin"
     }
   ]
   ```

1. <span data-ttu-id="c3499-242">Accédez à `http://localhost:<port>/api/books/{id here}` pour tester la méthode d’action `Get` surchargée du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="c3499-242">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="c3499-243">La réponse JSON suivante apparaît :</span><span class="sxs-lookup"><span data-stu-id="c3499-243">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="c3499-244">Configurer les options de sérialisation JSON</span><span class="sxs-lookup"><span data-stu-id="c3499-244">Configure JSON serialization options</span></span>

<span data-ttu-id="c3499-245">Vous devez changer deux détails concernant les réponses JSON retournées dans la section [Tester l’API web](#test-the-web-api) :</span><span class="sxs-lookup"><span data-stu-id="c3499-245">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="c3499-246">Vous devez changer la casse mixte par défaut des noms de propriétés pour qu’elle corresponde à la casse Pascal des noms de propriétés de l’objet CLR.</span><span class="sxs-lookup"><span data-stu-id="c3499-246">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="c3499-247">La propriété `bookName` doit être retournée sous la forme `Name`.</span><span class="sxs-lookup"><span data-stu-id="c3499-247">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="c3499-248">Pour satisfaire les exigences précédentes, apportez les changements suivants :</span><span class="sxs-lookup"><span data-stu-id="c3499-248">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="c3499-249">JSON.NET a été supprimé du framework partagé ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="c3499-249">JSON.NET has been removed from ASP.NET shared framework.</span></span> <span data-ttu-id="c3499-250">Ajoutez une référence de package à [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson) .</span><span class="sxs-lookup"><span data-stu-id="c3499-250">Add a package reference to [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span></span>

1. <span data-ttu-id="c3499-251">Dans `Startup.ConfigureServices`, ajoutez le code en surbrillance suivant à l’appel de méthode `AddControllers` :</span><span class="sxs-lookup"><span data-stu-id="c3499-251">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddControllers` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="c3499-252">À la suite du changement effectué, les noms de propriétés de la réponse JSON sérialisée de l’API web correspondent aux noms de propriétés équivalents du type d’objet CLR.</span><span class="sxs-lookup"><span data-stu-id="c3499-252">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="c3499-253">Par exemple, la propriété `Author` de la classe `Book` est sérialisée en tant que `Author`.</span><span class="sxs-lookup"><span data-stu-id="c3499-253">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="c3499-254">Dans *Models/Book. cs*, annotez la `BookName` propriété avec l' [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribut suivant :</span><span class="sxs-lookup"><span data-stu-id="c3499-254">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="c3499-255">La valeur de l’attribut `[JsonProperty]` de `Name` représente le nom de propriété dans la réponse JSON sérialisée de l’API web.</span><span class="sxs-lookup"><span data-stu-id="c3499-255">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="c3499-256">Ajoutez le code suivant en haut de *Models/Book.cs* pour résoudre la référence d’attribut `[JsonProperty]` :</span><span class="sxs-lookup"><span data-stu-id="c3499-256">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="c3499-257">Répétez les étapes définies dans la section [Tester l’API web](#test-the-web-api).</span><span class="sxs-lookup"><span data-stu-id="c3499-257">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="c3499-258">Notez la différence des noms de propriétés JSON.</span><span class="sxs-lookup"><span data-stu-id="c3499-258">Notice the difference in JSON property names.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c3499-259">Ce didacticiel crée une API web qui effectue des opérations de création, lecture, mise à jour et suppression (CRUD) sur une base de données NoSQL [MongoDB](https://www.mongodb.com/what-is-mongodb).</span><span class="sxs-lookup"><span data-stu-id="c3499-259">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="c3499-260">Dans ce tutoriel, vous allez apprendre à :</span><span class="sxs-lookup"><span data-stu-id="c3499-260">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c3499-261">Configurer MongoDB</span><span class="sxs-lookup"><span data-stu-id="c3499-261">Configure MongoDB</span></span>
> * <span data-ttu-id="c3499-262">Créer une base de données MongoDB</span><span class="sxs-lookup"><span data-stu-id="c3499-262">Create a MongoDB database</span></span>
> * <span data-ttu-id="c3499-263">Définir une collection et un schéma MongoDB</span><span class="sxs-lookup"><span data-stu-id="c3499-263">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="c3499-264">Effectuer des opérations CRUD MongoDB à partir d’une API web</span><span class="sxs-lookup"><span data-stu-id="c3499-264">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="c3499-265">Personnaliser la sérialisation JSON</span><span class="sxs-lookup"><span data-stu-id="c3499-265">Customize JSON serialization</span></span>

<span data-ttu-id="c3499-266">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-mongo-app/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c3499-266">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c3499-267">Prérequis</span><span class="sxs-lookup"><span data-stu-id="c3499-267">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c3499-268">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c3499-268">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="c3499-269">Kit SDK .NET Core 2.2</span><span class="sxs-lookup"><span data-stu-id="c3499-269">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="c3499-270">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) avec la charge de travail de **développement Web et ASP.net**</span><span class="sxs-lookup"><span data-stu-id="c3499-270">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="c3499-271">MongoDB</span><span class="sxs-lookup"><span data-stu-id="c3499-271">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="c3499-272">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c3499-272">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="c3499-273">Kit SDK .NET Core 2.2</span><span class="sxs-lookup"><span data-stu-id="c3499-273">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="c3499-274">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c3499-274">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="c3499-275">C# pour Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c3499-275">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="c3499-276">MongoDB</span><span class="sxs-lookup"><span data-stu-id="c3499-276">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c3499-277">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="c3499-277">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="c3499-278">Kit SDK .NET Core 2.2</span><span class="sxs-lookup"><span data-stu-id="c3499-278">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="c3499-279">Visual Studio pour Mac 7.7 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="c3499-279">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="c3499-280">MongoDB</span><span class="sxs-lookup"><span data-stu-id="c3499-280">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="c3499-281">Configurer MongoDB</span><span class="sxs-lookup"><span data-stu-id="c3499-281">Configure MongoDB</span></span>

<span data-ttu-id="c3499-282">Si vous utilisez Windows, MongoDB est installé par défaut dans *C:\\Program Files\\MongoDB*.</span><span class="sxs-lookup"><span data-stu-id="c3499-282">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="c3499-283">Ajoutez *C : \\ Program Files de l' \\ \\ \\ \<version_number> \\ emplacement du serveur MongoDB* à la `Path` variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="c3499-283">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="c3499-284">Cette modification permet d’accéder à MongoDB depuis n’importe quel emplacement sur votre ordinateur de développement.</span><span class="sxs-lookup"><span data-stu-id="c3499-284">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="c3499-285">Utilisez l’interpréteur de commandes mongo dans les étapes suivantes pour créer une base de données, des collections, et stocker des documents.</span><span class="sxs-lookup"><span data-stu-id="c3499-285">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="c3499-286">Pour plus d’informations sur les commandes de l’interpréteur mongo, consultez [Utilisation de l’interpréteur de commandes mongo](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="c3499-286">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="c3499-287">Choisissez sur votre ordinateur de développement un répertoire pour le stockage des données.</span><span class="sxs-lookup"><span data-stu-id="c3499-287">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="c3499-288">Par exemple, *C:\\BooksData* sous Windows.</span><span class="sxs-lookup"><span data-stu-id="c3499-288">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="c3499-289">Le cas échéant, créez le répertoire.</span><span class="sxs-lookup"><span data-stu-id="c3499-289">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="c3499-290">L’interpréteur de commandes mongo ne crée pas nouveaux répertoires.</span><span class="sxs-lookup"><span data-stu-id="c3499-290">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="c3499-291">Ouvrez un interpréteur de commandes.</span><span class="sxs-lookup"><span data-stu-id="c3499-291">Open a command shell.</span></span> <span data-ttu-id="c3499-292">Exécutez la commande suivante pour vous connecter à MongoDB sur le port par défaut 27017.</span><span class="sxs-lookup"><span data-stu-id="c3499-292">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="c3499-293">N’oubliez pas de remplacer `<data_directory_path>` par le répertoire que vous avez choisi à l’étape précédente.</span><span class="sxs-lookup"><span data-stu-id="c3499-293">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="c3499-294">Ouvrez une autre instance de l’interpréteur de commandes.</span><span class="sxs-lookup"><span data-stu-id="c3499-294">Open another command shell instance.</span></span> <span data-ttu-id="c3499-295">Connectez-vous à la base de données de test par défaut en exécutant la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="c3499-295">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="c3499-296">Utilisez la ligne suivante dans l’interpréteur de commandes :</span><span class="sxs-lookup"><span data-stu-id="c3499-296">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="c3499-297">Le cas échéant, une base de données nommée *BookstoreDb* est créée.</span><span class="sxs-lookup"><span data-stu-id="c3499-297">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="c3499-298">Si la base de données existe déjà, sa connexion est ouverte pour les transactions.</span><span class="sxs-lookup"><span data-stu-id="c3499-298">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="c3499-299">Créez une collection `Books` à l’aide de la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="c3499-299">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="c3499-300">Le résultat suivant s’affiche :</span><span class="sxs-lookup"><span data-stu-id="c3499-300">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="c3499-301">Définissez un schéma pour la collection `Books` et insérez deux documents à l’aide de la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="c3499-301">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="c3499-302">Le résultat suivant s’affiche :</span><span class="sxs-lookup"><span data-stu-id="c3499-302">The following result is displayed:</span></span>

   ```console
   {
     "acknowledged" : true,
     "insertedIds" : [
       ObjectId("5bfd996f7b8e48dc15ff215d"),
       ObjectId("5bfd996f7b8e48dc15ff215e")
     ]
   }
   ```
  
   > [!NOTE]
   > <span data-ttu-id="c3499-303">Les ID indiqués dans cet article ne correspondent pas aux ID obtenus quand vous exécutez cet exemple.</span><span class="sxs-lookup"><span data-stu-id="c3499-303">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="c3499-304">Affichez les documents de la base de données à l’aide de la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="c3499-304">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="c3499-305">Le résultat suivant s’affiche :</span><span class="sxs-lookup"><span data-stu-id="c3499-305">The following result is displayed:</span></span>

   ```console
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
     "Name" : "Design Patterns",
     "Price" : 54.93,
     "Category" : "Computers",
     "Author" : "Ralph Johnson"
   }
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
     "Name" : "Clean Code",
     "Price" : 43.15,
     "Category" : "Computers",
     "Author" : "Robert C. Martin"
   }
   ```

   <span data-ttu-id="c3499-306">Le schéma ajoute une propriété `_id` automatiquement générée de type `ObjectId` pour chaque document.</span><span class="sxs-lookup"><span data-stu-id="c3499-306">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="c3499-307">La base de données est en lecture seule.</span><span class="sxs-lookup"><span data-stu-id="c3499-307">The database is ready.</span></span> <span data-ttu-id="c3499-308">Vous pouvez commencer à créer l’API web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c3499-308">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="c3499-309">Créer le projet d’API web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c3499-309">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c3499-310">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c3499-310">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="c3499-311">Accédez à **fichier** > **nouveau** > **projet**.</span><span class="sxs-lookup"><span data-stu-id="c3499-311">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="c3499-312">Sélectionnez le type de projet **Application web ASP.NET Core**, puis sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="c3499-312">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="c3499-313">Nommez le projet *BooksApi*, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="c3499-313">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="c3499-314">Sélectionnez la version cible de .NET Framework **.NET Core** et **ASP.NET Core 2.2**.</span><span class="sxs-lookup"><span data-stu-id="c3499-314">Select the **.NET Core** target framework and **ASP.NET Core 2.2**.</span></span> <span data-ttu-id="c3499-315">Sélectionnez le modèle de projet **API**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="c3499-315">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="c3499-316">Visitez la [galerie NuGet : MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) pour déterminer la dernière version stable du pilote .net pour MongoDB.</span><span class="sxs-lookup"><span data-stu-id="c3499-316">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="c3499-317">Dans la fenêtre **Console du Gestionnaire de Package**, accédez à la racine du projet.</span><span class="sxs-lookup"><span data-stu-id="c3499-317">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="c3499-318">Exécutez la commande suivante afin d’installer le pilote .NET pour MongoDB :</span><span class="sxs-lookup"><span data-stu-id="c3499-318">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="c3499-319">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c3499-319">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="c3499-320">Exécutez les commandes suivantes dans l’interpréteur de commandes :</span><span class="sxs-lookup"><span data-stu-id="c3499-320">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="c3499-321">Un nouveau projet API web ASP.NET Core ciblant .NET Core est généré et ouvert dans Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="c3499-321">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="c3499-322">Une fois que l’icône de flamme OmniSharp de la barre d’État devient verte, une boîte de dialogue demande les **ressources requises à générer et à déboguer manquantes dans « BooksApi ». Ajoutez-les ?**.</span><span class="sxs-lookup"><span data-stu-id="c3499-322">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="c3499-323">Sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="c3499-323">Select **Yes**.</span></span>
1. <span data-ttu-id="c3499-324">Visitez la [galerie NuGet : MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) pour déterminer la dernière version stable du pilote .net pour MongoDB.</span><span class="sxs-lookup"><span data-stu-id="c3499-324">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="c3499-325">Ouvrez **Terminal intégré** et accédez à la racine du projet.</span><span class="sxs-lookup"><span data-stu-id="c3499-325">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="c3499-326">Exécutez la commande suivante afin d’installer le pilote .NET pour MongoDB :</span><span class="sxs-lookup"><span data-stu-id="c3499-326">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c3499-327">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="c3499-327">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="c3499-328">Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez **fichier**  >  **nouvelle solution**  >  **application .net Core**  >   dans la barre latérale.</span><span class="sxs-lookup"><span data-stu-id="c3499-328">In Visual Studio for Mac earlier than version 8.6, select **File** > **New Solution** > **.NET Core** > **App** from the sidebar.</span></span> <span data-ttu-id="c3499-329">Dans la version 8,6 ou ultérieure, sélectionnez **fichier**  >  **nouvelle solution**  >  **Web et**  >  **application** console dans la barre latérale.</span><span class="sxs-lookup"><span data-stu-id="c3499-329">In version 8.6 or later, select **File** > **New Solution** > **Web and Console** > **App** from the sidebar.</span></span>
1. <span data-ttu-id="c3499-330">Sélectionnez le modèle de projet C# **API web ASP.NET Core**, puis sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="c3499-330">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="c3499-331">Sélectionnez **.NET Core 2.2** dans la liste déroulante **Framework cible**, puis sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="c3499-331">Select **.NET Core 2.2** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="c3499-332">Entrez *BooksApi* pour **Nom de projet**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="c3499-332">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="c3499-333">Dans le panneau **Solution**, cliquez avec le bouton droit sur le nœud **Dépendances** du projet, puis sélectionnez **Ajouter des packages**.</span><span class="sxs-lookup"><span data-stu-id="c3499-333">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="c3499-334">Entrez *MongoDB.Driver* dans la zone de recherche, sélectionnez le package *MongoDB.Driver*, puis sélectionnez **Ajouter un package**.</span><span class="sxs-lookup"><span data-stu-id="c3499-334">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="c3499-335">Sélectionnez le bouton **Accepter** dans la boîte de dialogue **Acceptation de la licence**.</span><span class="sxs-lookup"><span data-stu-id="c3499-335">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="c3499-336">Ajouter un modèle d’entité</span><span class="sxs-lookup"><span data-stu-id="c3499-336">Add an entity model</span></span>

1. <span data-ttu-id="c3499-337">Ajoutez un répertoire *Models* à la racine du projet.</span><span class="sxs-lookup"><span data-stu-id="c3499-337">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="c3499-338">Ajoutez une classe `Book` au répertoire *Modèles* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="c3499-338">Add a `Book` class to the *Models* directory with the following code:</span></span>

   ```csharp
   using MongoDB.Bson;
   using MongoDB.Bson.Serialization.Attributes;

   namespace BooksApi.Models
   {
       public class Book
       {
           [BsonId]
           [BsonRepresentation(BsonType.ObjectId)]
           public string Id { get; set; }

           [BsonElement("Name")]
           public string BookName { get; set; }

           public decimal Price { get; set; }

           public string Category { get; set; }

           public string Author { get; set; }
       }
   }
   ```

   <span data-ttu-id="c3499-339">Dans la classe précédente, la propriété `Id` :</span><span class="sxs-lookup"><span data-stu-id="c3499-339">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="c3499-340">Est requise pour mapper l’objet Common Language Runtime (CLR) à la collection MongoDB.</span><span class="sxs-lookup"><span data-stu-id="c3499-340">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="c3499-341">Est annoté avec [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) pour désigner cette propriété comme clé primaire du document.</span><span class="sxs-lookup"><span data-stu-id="c3499-341">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="c3499-342">Est annoté avec [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) pour permettre la transmission du paramètre en tant que type `string` au lieu d’une structure [ObjectID](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) .</span><span class="sxs-lookup"><span data-stu-id="c3499-342">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="c3499-343">Mongo gère la conversion de `string` en `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="c3499-343">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="c3499-344">La `BookName` propriété est annotée avec l' [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribut.</span><span class="sxs-lookup"><span data-stu-id="c3499-344">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="c3499-345">La valeur de l’attribut de `Name` représente le nom de propriété dans la collection MongoDB.</span><span class="sxs-lookup"><span data-stu-id="c3499-345">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="c3499-346">Ajouter un modèle de configuration</span><span class="sxs-lookup"><span data-stu-id="c3499-346">Add a configuration model</span></span>

1. <span data-ttu-id="c3499-347">Ajoutez les valeurs de configuration de la base de données suivantes à *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="c3499-347">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="c3499-348">Ajoutez un fichier *BookstoreDatabaseSettings.cs* au répertoire *Models* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="c3499-348">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="c3499-349">La `BookstoreDatabaseSettings` classe précédente est utilisée pour stocker les *appsettings.json* valeurs de `BookstoreDatabaseSettings` propriété du fichier.</span><span class="sxs-lookup"><span data-stu-id="c3499-349">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="c3499-350">Les noms de propriétés JSON et C# sont nommés de manière identique pour faciliter le processus de mappage.</span><span class="sxs-lookup"><span data-stu-id="c3499-350">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="c3499-351">Ajoutez le code en surbrillance suivant à `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c3499-351">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   <span data-ttu-id="c3499-352">Dans le code précédent :</span><span class="sxs-lookup"><span data-stu-id="c3499-352">In the preceding code:</span></span>

   * <span data-ttu-id="c3499-353">L’instance de configuration vers laquelle la *appsettings.json* section du fichier `BookstoreDatabaseSettings` est liée est inscrite dans le conteneur d’injection de dépendance (di).</span><span class="sxs-lookup"><span data-stu-id="c3499-353">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="c3499-354">Par exemple, `BookstoreDatabaseSettings` la propriété d’un objet `ConnectionString` est remplie avec la `BookstoreDatabaseSettings:ConnectionString` propriété dans *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="c3499-354">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="c3499-355">L’interface `IBookstoreDatabaseSettings` est inscrite auprès de l’injection de dépendances avec une [durée de vie de service](xref:fundamentals/dependency-injection#service-lifetimes) de singleton.</span><span class="sxs-lookup"><span data-stu-id="c3499-355">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="c3499-356">Une fois injectée, l’instance d’interface est résolue en objet `BookstoreDatabaseSettings`.</span><span class="sxs-lookup"><span data-stu-id="c3499-356">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="c3499-357">Ajoutez le code suivant en haut de *Startup.cs* pour résoudre les références à `BookstoreDatabaseSettings` et `IBookstoreDatabaseSettings` :</span><span class="sxs-lookup"><span data-stu-id="c3499-357">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="c3499-358">Ajouter un service d’opérations CRUD</span><span class="sxs-lookup"><span data-stu-id="c3499-358">Add a CRUD operations service</span></span>

1. <span data-ttu-id="c3499-359">Ajoutez un répertoire *Services* à la racine du projet.</span><span class="sxs-lookup"><span data-stu-id="c3499-359">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="c3499-360">Ajoutez une classe `BookService` au répertoire *Services* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="c3499-360">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="c3499-361">Dans le code précédent, une instance de `IBookstoreDatabaseSettings` est récupérée à partir de l’injection de dépendances via l’injection de constructeur.</span><span class="sxs-lookup"><span data-stu-id="c3499-361">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="c3499-362">Cette technique permet d’accéder aux *appsettings.json* valeurs de configuration qui ont été ajoutées dans la section [Ajouter un modèle de configuration](#add-a-configuration-model) .</span><span class="sxs-lookup"><span data-stu-id="c3499-362">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="c3499-363">Ajoutez le code en surbrillance suivant à `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c3499-363">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="c3499-364">Dans le code précédent, la classe `BookService` est inscrite auprès de l’injection de dépendances pour permettre la prise en charge de l’injection de constructeur dans les classes consommatrices.</span><span class="sxs-lookup"><span data-stu-id="c3499-364">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="c3499-365">La durée de vie de service de singleton est la plus appropriée, car `BookService` a une dépendance directe sur `MongoClient`.</span><span class="sxs-lookup"><span data-stu-id="c3499-365">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="c3499-366">Selon les [instructions de réutilisation du client Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)officielles, `MongoClient` doit être inscrit dans di avec une durée de vie de service Singleton.</span><span class="sxs-lookup"><span data-stu-id="c3499-366">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="c3499-367">Ajoutez le code suivant en haut de *Startup.cs* pour résoudre la référence à `BookService` :</span><span class="sxs-lookup"><span data-stu-id="c3499-367">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="c3499-368">La classe `BookService` utilise les membres `MongoDB.Driver` suivants pour effectuer des opérations CRUD dans la base de données :</span><span class="sxs-lookup"><span data-stu-id="c3499-368">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="c3499-369">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): lit l’instance de serveur pour effectuer des opérations de base de données.</span><span class="sxs-lookup"><span data-stu-id="c3499-369">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): Reads the server instance for performing database operations.</span></span> <span data-ttu-id="c3499-370">Le constructeur de cette classe reçoit la chaîne de connexion MongoDB :</span><span class="sxs-lookup"><span data-stu-id="c3499-370">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="c3499-371">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): représente la base de données Mongo pour l’exécution d’opérations.</span><span class="sxs-lookup"><span data-stu-id="c3499-371">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="c3499-372">Ce tutoriel utilise la méthode générique [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) sur l’interface pour accéder aux données d’une collection spécifique.</span><span class="sxs-lookup"><span data-stu-id="c3499-372">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="c3499-373">Effectuez des opérations CRUD sur la collection, après l’appel de cette méthode.</span><span class="sxs-lookup"><span data-stu-id="c3499-373">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="c3499-374">Dans l’appel à la méthode `GetCollection<TDocument>(collection)` :</span><span class="sxs-lookup"><span data-stu-id="c3499-374">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="c3499-375">`collection` représente le nom de la collection.</span><span class="sxs-lookup"><span data-stu-id="c3499-375">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="c3499-376">`TDocument` représente le type d’objet CLR stocké dans la collection.</span><span class="sxs-lookup"><span data-stu-id="c3499-376">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="c3499-377">`GetCollection<TDocument>(collection)` retourne un objet [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) représentant la collection.</span><span class="sxs-lookup"><span data-stu-id="c3499-377">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="c3499-378">Dans ce didacticiel, les méthodes suivantes sont appelées sur la collection :</span><span class="sxs-lookup"><span data-stu-id="c3499-378">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="c3499-379">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): supprime un document unique correspondant aux critères de recherche fournis.</span><span class="sxs-lookup"><span data-stu-id="c3499-379">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="c3499-380">[Find \<TDocument> ](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): retourne tous les documents de la collection correspondant aux critères de recherche fournis.</span><span class="sxs-lookup"><span data-stu-id="c3499-380">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="c3499-381">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): insère l’objet fourni sous la forme d’un nouveau document dans la collection.</span><span class="sxs-lookup"><span data-stu-id="c3499-381">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="c3499-382">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): remplace le document unique qui correspond aux critères de recherche fournis par l’objet fourni.</span><span class="sxs-lookup"><span data-stu-id="c3499-382">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="c3499-383">Ajout d'un contrôleur</span><span class="sxs-lookup"><span data-stu-id="c3499-383">Add a controller</span></span>

<span data-ttu-id="c3499-384">Ajoutez une classe `BooksController` au répertoire *Controllers* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="c3499-384">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="c3499-385">Le contrôleur d’API web précédent :</span><span class="sxs-lookup"><span data-stu-id="c3499-385">The preceding web API controller:</span></span>

* <span data-ttu-id="c3499-386">Utilise la classe `BookService` pour effectuer des opérations CRUD.</span><span class="sxs-lookup"><span data-stu-id="c3499-386">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="c3499-387">Contient des méthodes d’action pour prendre en charge les requêtes HTTP GET, POST, PUT et DELETE.</span><span class="sxs-lookup"><span data-stu-id="c3499-387">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="c3499-388">Appelle <xref:System.Web.Http.ApiController.CreatedAtRoute*> dans la méthode d’action `Create` pour retourner une réponse [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="c3499-388">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="c3499-389">Le code d’état 201 est la réponse standard d’une méthode HTTP POST qui crée une ressource sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="c3499-389">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="c3499-390">`CreatedAtRoute` ajoute également un en-tête `Location` à la réponse.</span><span class="sxs-lookup"><span data-stu-id="c3499-390">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="c3499-391">L’en-tête `Location` spécifie l’URI du livre qui vient d’être créé.</span><span class="sxs-lookup"><span data-stu-id="c3499-391">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="c3499-392">Tester l’API web</span><span class="sxs-lookup"><span data-stu-id="c3499-392">Test the web API</span></span>

1. <span data-ttu-id="c3499-393">Générez et exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="c3499-393">Build and run the app.</span></span>

1. <span data-ttu-id="c3499-394">Accédez à `http://localhost:<port>/api/books` pour tester la méthode d’action `Get` sans paramètre du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="c3499-394">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="c3499-395">La réponse JSON suivante apparaît :</span><span class="sxs-lookup"><span data-stu-id="c3499-395">The following JSON response is displayed:</span></span>

   ```json
   [
     {
       "id":"5bfd996f7b8e48dc15ff215d",
       "bookName":"Design Patterns",
       "price":54.93,
       "category":"Computers",
       "author":"Ralph Johnson"
     },
     {
       "id":"5bfd996f7b8e48dc15ff215e",
       "bookName":"Clean Code",
       "price":43.15,
       "category":"Computers",
       "author":"Robert C. Martin"
     }
   ]
   ```

1. <span data-ttu-id="c3499-396">Accédez à `http://localhost:<port>/api/books/{id here}` pour tester la méthode d’action `Get` surchargée du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="c3499-396">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="c3499-397">La réponse JSON suivante apparaît :</span><span class="sxs-lookup"><span data-stu-id="c3499-397">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="c3499-398">Configurer les options de sérialisation JSON</span><span class="sxs-lookup"><span data-stu-id="c3499-398">Configure JSON serialization options</span></span>

<span data-ttu-id="c3499-399">Vous devez changer deux détails concernant les réponses JSON retournées dans la section [Tester l’API web](#test-the-web-api) :</span><span class="sxs-lookup"><span data-stu-id="c3499-399">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="c3499-400">Vous devez changer la casse mixte par défaut des noms de propriétés pour qu’elle corresponde à la casse Pascal des noms de propriétés de l’objet CLR.</span><span class="sxs-lookup"><span data-stu-id="c3499-400">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="c3499-401">La propriété `bookName` doit être retournée sous la forme `Name`.</span><span class="sxs-lookup"><span data-stu-id="c3499-401">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="c3499-402">Pour satisfaire les exigences précédentes, apportez les changements suivants :</span><span class="sxs-lookup"><span data-stu-id="c3499-402">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="c3499-403">Dans `Startup.ConfigureServices`, ajoutez le code en surbrillance suivant à l’appel de méthode `AddMvc` :</span><span class="sxs-lookup"><span data-stu-id="c3499-403">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="c3499-404">À la suite du changement effectué, les noms de propriétés de la réponse JSON sérialisée de l’API web correspondent aux noms de propriétés équivalents du type d’objet CLR.</span><span class="sxs-lookup"><span data-stu-id="c3499-404">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="c3499-405">Par exemple, la propriété `Author` de la classe `Book` est sérialisée en tant que `Author`.</span><span class="sxs-lookup"><span data-stu-id="c3499-405">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="c3499-406">Dans *Models/Book. cs*, annotez la `BookName` propriété avec l' [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribut suivant :</span><span class="sxs-lookup"><span data-stu-id="c3499-406">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="c3499-407">La valeur de l’attribut `[JsonProperty]` de `Name` représente le nom de propriété dans la réponse JSON sérialisée de l’API web.</span><span class="sxs-lookup"><span data-stu-id="c3499-407">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="c3499-408">Ajoutez le code suivant en haut de *Models/Book.cs* pour résoudre la référence d’attribut `[JsonProperty]` :</span><span class="sxs-lookup"><span data-stu-id="c3499-408">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="c3499-409">Répétez les étapes définies dans la section [Tester l’API web](#test-the-web-api).</span><span class="sxs-lookup"><span data-stu-id="c3499-409">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="c3499-410">Notez la différence des noms de propriétés JSON.</span><span class="sxs-lookup"><span data-stu-id="c3499-410">Notice the difference in JSON property names.</span></span>

::: moniker-end

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="c3499-411">Ajouter la prise en charge de l’authentification à une API Web</span><span class="sxs-lookup"><span data-stu-id="c3499-411">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="next-steps"></a><span data-ttu-id="c3499-412">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="c3499-412">Next steps</span></span>

<span data-ttu-id="c3499-413">Pour plus d’informations sur la création d’API web ASP.NET Core, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="c3499-413">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="c3499-414">Version YouTube de cet article</span><span class="sxs-lookup"><span data-stu-id="c3499-414">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
* [<span data-ttu-id="c3499-415">Microsoft Learn : créer une API Web avec ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c3499-415">Microsoft Learn: Create a web API with ASP.NET Core</span></span>](/learn/modules/build-web-api-aspnet-core/)
