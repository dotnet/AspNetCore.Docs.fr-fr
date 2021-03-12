---
title: Ajouter, télécharger et supprimer des données utilisateur Identity dans un projet ASP.net Core
author: rick-anderson
description: Découvrez comment ajouter des données utilisateur personnalisées à Identity dans un projet ASP.net core. Supprimez les données par RGPD.
ms.author: riande
ms.date: 03/26/2020
ms.custom: mvc, seodec18
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
uid: security/authentication/add-user-data
ms.openlocfilehash: 2d921a0c72fb7c03cd88966077e2d33e4b19ffa1
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102585914"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a><span data-ttu-id="c499b-104">Ajouter, télécharger et supprimer des données utilisateur personnalisées Identity dans un projet ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="c499b-104">Add, download, and delete custom user data to Identity in an ASP.NET Core project</span></span>

<span data-ttu-id="c499b-105">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c499b-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c499b-106">Cet article montre comment :</span><span class="sxs-lookup"><span data-stu-id="c499b-106">This article shows how to:</span></span>

* <span data-ttu-id="c499b-107">Ajouter des données utilisateur personnalisées à une application Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c499b-107">Add custom user data to an ASP.NET Core web app.</span></span>
* <span data-ttu-id="c499b-108">Marquez le modèle de données utilisateur personnalisé avec l' <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> attribut afin qu’il soit automatiquement disponible au téléchargement et à la suppression.</span><span class="sxs-lookup"><span data-stu-id="c499b-108">Mark the custom user data model with the <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> attribute so it's automatically available for download and deletion.</span></span> <span data-ttu-id="c499b-109">La possibilité de télécharger et de supprimer les données vous aide à répondre aux exigences de [RGPD](xref:security/gdpr) .</span><span class="sxs-lookup"><span data-stu-id="c499b-109">Making the data able to be downloaded and deleted helps meet [GDPR](xref:security/gdpr) requirements.</span></span>

<span data-ttu-id="c499b-110">L’exemple de projet est créé à partir d’une Razor application Web pages, mais les instructions sont similaires pour une asp.net Core application Web MVC.</span><span class="sxs-lookup"><span data-stu-id="c499b-110">The project sample is created from a Razor Pages web app, but the instructions are similar for a ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="c499b-111">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/add-user-data) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c499b-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/add-user-data) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c499b-112">Prérequis</span><span class="sxs-lookup"><span data-stu-id="c499b-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a><span data-ttu-id="c499b-113">Créer une Razor application Web</span><span class="sxs-lookup"><span data-stu-id="c499b-113">Create a Razor web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c499b-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c499b-114">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="c499b-115">Dans Visual Studio, dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.</span><span class="sxs-lookup"><span data-stu-id="c499b-115">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="c499b-116">Nommez le projet **application Web 1** si vous souhaitez qu’il corresponde à l’espace de noms de l’exemple de code de [Téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) .</span><span class="sxs-lookup"><span data-stu-id="c499b-116">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="c499b-117">Sélectionnez **ASP.net Core application Web** > **OK**</span><span class="sxs-lookup"><span data-stu-id="c499b-117">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="c499b-118">Sélectionnez **ASP.NET Core 3,0** dans la liste déroulante</span><span class="sxs-lookup"><span data-stu-id="c499b-118">Select **ASP.NET Core 3.0** in the dropdown</span></span>
* <span data-ttu-id="c499b-119">Sélectionner l' **application Web** > **OK**</span><span class="sxs-lookup"><span data-stu-id="c499b-119">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="c499b-120">Générez et exécutez le projet.</span><span class="sxs-lookup"><span data-stu-id="c499b-120">Build and run the project.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="c499b-121">Dans Visual Studio, dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.</span><span class="sxs-lookup"><span data-stu-id="c499b-121">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="c499b-122">Nommez le projet **application Web 1** si vous souhaitez qu’il corresponde à l’espace de noms de l’exemple de code de [Téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) .</span><span class="sxs-lookup"><span data-stu-id="c499b-122">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="c499b-123">Sélectionnez **ASP.net Core application Web** > **OK**</span><span class="sxs-lookup"><span data-stu-id="c499b-123">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="c499b-124">Sélectionnez **ASP.NET Core 2,2** dans la liste déroulante</span><span class="sxs-lookup"><span data-stu-id="c499b-124">Select **ASP.NET Core 2.2** in the dropdown</span></span>
* <span data-ttu-id="c499b-125">Sélectionner l' **application Web** > **OK**</span><span class="sxs-lookup"><span data-stu-id="c499b-125">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="c499b-126">Générez et exécutez le projet.</span><span class="sxs-lookup"><span data-stu-id="c499b-126">Build and run the project.</span></span>

::: moniker-end


# <a name="net-core-cli"></a>[<span data-ttu-id="c499b-127">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="c499b-127">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a><span data-ttu-id="c499b-128">Exécuter le générateur de Identity modèles</span><span class="sxs-lookup"><span data-stu-id="c499b-128">Run the Identity scaffolder</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c499b-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c499b-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c499b-130">Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le projet > **Ajouter**  >  **un nouvel élément de génération de modèles** automatique.</span><span class="sxs-lookup"><span data-stu-id="c499b-130">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="c499b-131">Dans le volet gauche de la boîte de dialogue **Ajouter une structure** , sélectionnez **Identity**  >  **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="c499b-131">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="c499b-132">Dans la boîte de dialogue **ajouter Identity** , les options suivantes sont disponibles :</span><span class="sxs-lookup"><span data-stu-id="c499b-132">In the **Add Identity** dialog, the following options:</span></span>
  * <span data-ttu-id="c499b-133">Sélectionner le fichier de disposition existant  *~/Pages/Shared/_Layout. cshtml*</span><span class="sxs-lookup"><span data-stu-id="c499b-133">Select the existing layout  file  *~/Pages/Shared/_Layout.cshtml*</span></span>
  * <span data-ttu-id="c499b-134">Sélectionnez les fichiers suivants à remplacer :</span><span class="sxs-lookup"><span data-stu-id="c499b-134">Select the following files to override:</span></span>
    * <span data-ttu-id="c499b-135">**Compte/inscription**</span><span class="sxs-lookup"><span data-stu-id="c499b-135">**Account/Register**</span></span>
    * <span data-ttu-id="c499b-136">**Compte/gestion/index**</span><span class="sxs-lookup"><span data-stu-id="c499b-136">**Account/Manage/Index**</span></span>
  * <span data-ttu-id="c499b-137">Sélectionnez le **+** bouton pour créer une **classe de contexte de données**.</span><span class="sxs-lookup"><span data-stu-id="c499b-137">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="c499b-138">Acceptez le type (**application Web 1. Models. WebApp1Context** si le projet est nommé **application Web 1**).</span><span class="sxs-lookup"><span data-stu-id="c499b-138">Accept the type (**WebApp1.Models.WebApp1Context** if the project is named **WebApp1**).</span></span>
  * <span data-ttu-id="c499b-139">Sélectionnez le **+** bouton pour créer une nouvelle **classe d’utilisateur**.</span><span class="sxs-lookup"><span data-stu-id="c499b-139">Select the **+** button to create a new **User class**.</span></span> <span data-ttu-id="c499b-140">Acceptez le type (**WebApp1User** si le projet est nommé **application Web 1**) > **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="c499b-140">Accept the type (**WebApp1User** if the project is named **WebApp1**) > **Add**.</span></span>
* <span data-ttu-id="c499b-141">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="c499b-141">Select **Add**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="c499b-142">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="c499b-142">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="c499b-143">Si vous n’avez pas déjà installé le générateur de modèles automatique ASP.NET Core, installez-le maintenant :</span><span class="sxs-lookup"><span data-stu-id="c499b-143">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="c499b-144">Ajoutez une référence de package à [Microsoft. VisualStudio. Web. CodeGeneration. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) dans le fichier projet (. csproj).</span><span class="sxs-lookup"><span data-stu-id="c499b-144">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (.csproj) file.</span></span> <span data-ttu-id="c499b-145">Exécutez la commande suivante dans le répertoire du projet :</span><span class="sxs-lookup"><span data-stu-id="c499b-145">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="c499b-146">Exécutez la commande suivante pour répertorier les options de l' Identity échafaudage :</span><span class="sxs-lookup"><span data-stu-id="c499b-146">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="c499b-147">Dans le dossier du projet, exécutez le générateur de Identity modèles :</span><span class="sxs-lookup"><span data-stu-id="c499b-147">In the project folder, run the Identity scaffolder:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

<span data-ttu-id="c499b-148">Suivez les instructions dans [migrations, UseAuthentication et Layout](xref:security/authentication/scaffold-identity#efm) pour effectuer les étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="c499b-148">Follow the instruction in [Migrations, UseAuthentication, and layout](xref:security/authentication/scaffold-identity#efm) to perform the following steps:</span></span>

* <span data-ttu-id="c499b-149">Créer une migration et mettre à jour la base de données.</span><span class="sxs-lookup"><span data-stu-id="c499b-149">Create a migration and update the database.</span></span>
* <span data-ttu-id="c499b-150">Ajout de `UseAuthentication` à `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="c499b-150">Add `UseAuthentication` to `Startup.Configure`.</span></span>
* <span data-ttu-id="c499b-151">Ajoutez `<partial name="_LoginPartial" />` au fichier de disposition.</span><span class="sxs-lookup"><span data-stu-id="c499b-151">Add `<partial name="_LoginPartial" />` to the layout file.</span></span>
* <span data-ttu-id="c499b-152">Testez l’application :</span><span class="sxs-lookup"><span data-stu-id="c499b-152">Test the app:</span></span>
  * <span data-ttu-id="c499b-153">Inscrire un utilisateur</span><span class="sxs-lookup"><span data-stu-id="c499b-153">Register a user</span></span>
  * <span data-ttu-id="c499b-154">Sélectionnez le nouveau nom d’utilisateur (à côté du lien de **déconnexion** ).</span><span class="sxs-lookup"><span data-stu-id="c499b-154">Select the new user name (next to the **Logout** link).</span></span> <span data-ttu-id="c499b-155">Vous devrez peut-être développer la fenêtre ou sélectionner l’icône de barre de navigation pour afficher le nom d’utilisateur et d’autres liens.</span><span class="sxs-lookup"><span data-stu-id="c499b-155">You might need to expand the window or select the navigation bar icon to show the user name and other links.</span></span>
  * <span data-ttu-id="c499b-156">Sélectionnez l’onglet **données personnelles** .</span><span class="sxs-lookup"><span data-stu-id="c499b-156">Select the **Personal Data** tab.</span></span>
  * <span data-ttu-id="c499b-157">Sélectionnez le bouton **Télécharger** et examinez le *PersonalData.js* fichier.</span><span class="sxs-lookup"><span data-stu-id="c499b-157">Select the **Download** button and examined the *PersonalData.json* file.</span></span>
  * <span data-ttu-id="c499b-158">Testez le bouton **supprimer** , qui supprime l’utilisateur connecté.</span><span class="sxs-lookup"><span data-stu-id="c499b-158">Test the **Delete** button, which deletes the logged on user.</span></span>

## <a name="add-custom-user-data-to-the-identity-db"></a><span data-ttu-id="c499b-159">Ajouter des données utilisateur personnalisées à la base de données Identity</span><span class="sxs-lookup"><span data-stu-id="c499b-159">Add custom user data to the Identity DB</span></span>

<span data-ttu-id="c499b-160">Mettez à jour la `IdentityUser` classe dérivée avec des propriétés personnalisées.</span><span class="sxs-lookup"><span data-stu-id="c499b-160">Update the `IdentityUser` derived class with custom properties.</span></span> <span data-ttu-id="c499b-161">Si vous avez nommé le projet application Web 1, le fichier est nommé *Areas/ Identity /Data/WebApp1User.cs*.</span><span class="sxs-lookup"><span data-stu-id="c499b-161">If you named the project WebApp1, the file is named *Areas/Identity/Data/WebApp1User.cs*.</span></span> <span data-ttu-id="c499b-162">Mettez à jour le fichier avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="c499b-162">Update the file with the following code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

<span data-ttu-id="c499b-163">Les propriétés avec l’attribut [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="c499b-163">Properties with the [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) attribute are:</span></span>

* <span data-ttu-id="c499b-164">Supprimé lorsque la page *zones/ Identity /pages/Account/Manage/DeletePersonalData.cshtml* Razor appelle `UserManager.Delete` .</span><span class="sxs-lookup"><span data-stu-id="c499b-164">Deleted when the *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* Razor Page calls `UserManager.Delete`.</span></span>
* <span data-ttu-id="c499b-165">Inclus dans les données téléchargées par la page *zones/ Identity /pages/Account/Manage/DownloadPersonalData.cshtml* Razor .</span><span class="sxs-lookup"><span data-stu-id="c499b-165">Included in the downloaded data by the *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor Page.</span></span>

### <a name="update-the-accountmanageindexcshtml-page"></a><span data-ttu-id="c499b-166">Mettre à jour la page Account/Manage/index. cshtml</span><span class="sxs-lookup"><span data-stu-id="c499b-166">Update the Account/Manage/Index.cshtml page</span></span>

<span data-ttu-id="c499b-167">Mettez à jour `InputModel` dans *Areas/ Identity /pages/Account/Manage/index.cshtml.cs* avec le code en surbrillance suivant :</span><span class="sxs-lookup"><span data-stu-id="c499b-167">Update the `InputModel` in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

<span data-ttu-id="c499b-168">Mettez à jour les *zones/ Identity /pages/Account/Manage/index.cshtml* avec le balisage en surbrillance suivant :</span><span class="sxs-lookup"><span data-stu-id="c499b-168">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

<span data-ttu-id="c499b-169">Mettez à jour les *zones/ Identity /pages/Account/Manage/index.cshtml* avec le balisage en surbrillance suivant :</span><span class="sxs-lookup"><span data-stu-id="c499b-169">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a><span data-ttu-id="c499b-170">Mettre à jour la page Account/Register. cshtml</span><span class="sxs-lookup"><span data-stu-id="c499b-170">Update the Account/Register.cshtml page</span></span>

<span data-ttu-id="c499b-171">Mettez à jour `InputModel` dans *Areas/ Identity /pages/Account/Register.cshtml.cs* avec le code en surbrillance suivant :</span><span class="sxs-lookup"><span data-stu-id="c499b-171">Update the `InputModel` in *Areas/Identity/Pages/Account/Register.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

<span data-ttu-id="c499b-172">Mettez à jour les *zones/ Identity /pages/Account/Register.cshtml* avec le balisage en surbrillance suivant :</span><span class="sxs-lookup"><span data-stu-id="c499b-172">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

<span data-ttu-id="c499b-173">Mettez à jour les *zones/ Identity /pages/Account/Register.cshtml* avec le balisage en surbrillance suivant :</span><span class="sxs-lookup"><span data-stu-id="c499b-173">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


<span data-ttu-id="c499b-174">Créez le projet.</span><span class="sxs-lookup"><span data-stu-id="c499b-174">Build the project.</span></span>

### <a name="add-a-migration-for-the-custom-user-data"></a><span data-ttu-id="c499b-175">Ajouter une migration pour les données utilisateur personnalisées</span><span class="sxs-lookup"><span data-stu-id="c499b-175">Add a migration for the custom user data</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c499b-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c499b-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c499b-177">Dans la console du **Gestionnaire de package** Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="c499b-177">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="c499b-178">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="c499b-178">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a><span data-ttu-id="c499b-179">Test créer, afficher, télécharger, supprimer des données utilisateur personnalisées</span><span class="sxs-lookup"><span data-stu-id="c499b-179">Test create, view, download, delete custom user data</span></span>

<span data-ttu-id="c499b-180">Testez l’application :</span><span class="sxs-lookup"><span data-stu-id="c499b-180">Test the app:</span></span>

* <span data-ttu-id="c499b-181">Inscrire un nouvel utilisateur.</span><span class="sxs-lookup"><span data-stu-id="c499b-181">Register a new user.</span></span>
* <span data-ttu-id="c499b-182">Affichez les données utilisateur personnalisées sur la `/Identity/Account/Manage` page.</span><span class="sxs-lookup"><span data-stu-id="c499b-182">View the custom user data on the `/Identity/Account/Manage` page.</span></span>
* <span data-ttu-id="c499b-183">Téléchargez et affichez les données personnelles des utilisateurs à partir de la `/Identity/Account/Manage/PersonalData` page.</span><span class="sxs-lookup"><span data-stu-id="c499b-183">Download and view the users personal data from the `/Identity/Account/Manage/PersonalData` page.</span></span>

## <a name="add-claims-to-identity-using-iuserclaimsprincipalfactoryapplicationuser"></a><span data-ttu-id="c499b-184">Ajouter des revendications à à Identity l’aide de IUserClaimsPrincipalFactory<ApplicationUser></span><span class="sxs-lookup"><span data-stu-id="c499b-184">Add claims to Identity using IUserClaimsPrincipalFactory<ApplicationUser></span></span>

> [!NOTE]
> <span data-ttu-id="c499b-185">Cette section n’est pas une extension du didacticiel précédent.</span><span class="sxs-lookup"><span data-stu-id="c499b-185">This section isn't an extension of the previous tutorial.</span></span> <span data-ttu-id="c499b-186">Pour appliquer les étapes suivantes à l’application générée à l’aide du didacticiel, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/18797).</span><span class="sxs-lookup"><span data-stu-id="c499b-186">To apply the following steps to the app built using the tutorial, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/18797).</span></span>

<span data-ttu-id="c499b-187">Des revendications supplémentaires peuvent être ajoutées à à ASP.NET Core Identity l’aide de l' `IUserClaimsPrincipalFactory<T>` interface.</span><span class="sxs-lookup"><span data-stu-id="c499b-187">Additional claims can be added to ASP.NET Core Identity by using the `IUserClaimsPrincipalFactory<T>` interface.</span></span> <span data-ttu-id="c499b-188">Cette classe peut être ajoutée à l’application dans la `Startup.ConfigureServices` méthode.</span><span class="sxs-lookup"><span data-stu-id="c499b-188">This class can be added to the app in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="c499b-189">Ajoutez l’implémentation personnalisée de la classe comme suit :</span><span class="sxs-lookup"><span data-stu-id="c499b-189">Add the custom implementation of the class as follows:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddScoped<IUserClaimsPrincipalFactory<ApplicationUser>, 
        AdditionalUserClaimsPrincipalFactory>();
```

<span data-ttu-id="c499b-190">Le code de démonstration utilise la `ApplicationUser` classe.</span><span class="sxs-lookup"><span data-stu-id="c499b-190">The demo code uses the `ApplicationUser` class.</span></span> <span data-ttu-id="c499b-191">Cette classe ajoute une `IsAdmin` propriété qui est utilisée pour ajouter la revendication supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="c499b-191">This class adds an `IsAdmin` property which is used to add the additional claim.</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public bool IsAdmin { get; set; }
}
```

<span data-ttu-id="c499b-192">`AdditionalUserClaimsPrincipalFactory` implémente l'interface `UserClaimsPrincipalFactory`.</span><span class="sxs-lookup"><span data-stu-id="c499b-192">The `AdditionalUserClaimsPrincipalFactory` implements the `UserClaimsPrincipalFactory` interface.</span></span> <span data-ttu-id="c499b-193">Une nouvelle revendication de rôle est ajoutée au `ClaimsPrincipal` .</span><span class="sxs-lookup"><span data-stu-id="c499b-193">A new role claim is added to the `ClaimsPrincipal`.</span></span>

```csharp
public class AdditionalUserClaimsPrincipalFactory 
        : UserClaimsPrincipalFactory<ApplicationUser, IdentityRole>
{
    public AdditionalUserClaimsPrincipalFactory( 
        UserManager<ApplicationUser> userManager,
        RoleManager<IdentityRole> roleManager, 
        IOptions<IdentityOptions> optionsAccessor) 
        : base(userManager, roleManager, optionsAccessor)
    {}

    public async override Task<ClaimsPrincipal> CreateAsync(ApplicationUser user)
    {
        var principal = await base.CreateAsync(user);
        var identity = (ClaimsIdentity)principal.Identity;

        var claims = new List<Claim>();
        if (user.IsAdmin)
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "admin"));
        }
        else
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "user"));
        }

        identity.AddClaims(claims);
        return principal;
    }
}
```

<span data-ttu-id="c499b-194">La revendication supplémentaire peut ensuite être utilisée dans l’application.</span><span class="sxs-lookup"><span data-stu-id="c499b-194">The additional claim can then be used in the app.</span></span> <span data-ttu-id="c499b-195">Dans une Razor page, l' `IAuthorizationService` instance peut être utilisée pour accéder à la valeur de revendication.</span><span class="sxs-lookup"><span data-stu-id="c499b-195">In a Razor Page, the `IAuthorizationService` instance can be used to access the claim value.</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

@if ((await AuthorizationService.AuthorizeAsync(User, "IsAdmin")).Succeeded)
{
    <ul class="mr-auto navbar-nav">
        <li class="nav-item">
            <a class="nav-link" asp-controller="Admin" asp-action="Index">ADMIN</a>
        </li>
    </ul>
}
```
