---
title: 'Présentation de :::no-loc(Identity)::: sur ASP.net Core'
author: rick-anderson
description: 'Utilisez :::no-loc(Identity)::: avec une application ASP.net core. Découvrez comment définir les exigences de mot de passe (RequireDigit, RequiredLength, RequiredUniqueChars, etc.).'
ms.author: riande
ms.date: 7/15/2020
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
uid: security/authentication/identity
ms.openlocfilehash: bfcef860beb07ab81dda1a10a1648491ae187bef
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052017"
---
# <a name="introduction-to-no-locidentity-on-aspnet-core"></a><span data-ttu-id="48bce-104">Présentation de :::no-loc(Identity)::: sur ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="48bce-104">Introduction to :::no-loc(Identity)::: on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="48bce-105">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="48bce-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="48bce-106">:::no-loc(ASP.NET Core Identity)::::</span><span class="sxs-lookup"><span data-stu-id="48bce-106">:::no-loc(ASP.NET Core Identity)::::</span></span>

* <span data-ttu-id="48bce-107">Est une API qui prend en charge la fonctionnalité de connexion de l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="48bce-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="48bce-108">Gère les utilisateurs, les mots de passe, les données de profil, les rôles, les revendications, les jetons, la confirmation par e-mail et bien plus encore.</span><span class="sxs-lookup"><span data-stu-id="48bce-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="48bce-109">Les utilisateurs peuvent créer un compte avec les informations de connexion stockées dans :::no-loc(Identity)::: ou ils peuvent utiliser un fournisseur de connexion externe.</span><span class="sxs-lookup"><span data-stu-id="48bce-109">Users can create an account with the login information stored in :::no-loc(Identity)::: or they can use an external login provider.</span></span> <span data-ttu-id="48bce-110">Les fournisseurs de connexion externes pris en charge incluent [Facebook, Google, Microsoft Account et Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="48bce-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

[!INCLUDE[](~/includes/requireAuth.md)]

<span data-ttu-id="48bce-111">Le [ :::no-loc(Identity)::: code source](https://github.com/dotnet/AspNetCore/tree/master/src/:::no-loc(Identity):::) est disponible sur GitHub.</span><span class="sxs-lookup"><span data-stu-id="48bce-111">The [:::no-loc(Identity)::: source code](https://github.com/dotnet/AspNetCore/tree/master/src/:::no-loc(Identity):::) is available on GitHub.</span></span> <span data-ttu-id="48bce-112">[Structure :::no-loc(Identity)::: ](xref:security/authentication/scaffold-identity) et affichez les fichiers générés pour examiner l’interaction du modèle avec :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="48bce-112">[Scaffold :::no-loc(Identity):::](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with :::no-loc(Identity):::.</span></span>

<span data-ttu-id="48bce-113">:::no-loc(Identity)::: est généralement configurée à l’aide d’une base de données SQL Server pour stocker les noms d’utilisateur, les mots de passe et les données de profil.</span><span class="sxs-lookup"><span data-stu-id="48bce-113">:::no-loc(Identity)::: is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="48bce-114">Vous pouvez également utiliser un autre magasin persistant, par exemple, le stockage table Azure.</span><span class="sxs-lookup"><span data-stu-id="48bce-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="48bce-115">Dans cette rubrique, vous allez apprendre à utiliser :::no-loc(Identity)::: pour inscrire, se connecter et déconnecter un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="48bce-115">In this topic, you learn how to use :::no-loc(Identity)::: to register, log in, and log out a user.</span></span> <span data-ttu-id="48bce-116">Remarque : les modèles traitent le nom d’utilisateur et l’adresse de messagerie comme les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="48bce-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="48bce-117">Pour obtenir des instructions plus détaillées sur la création d’applications qui utilisent :::no-loc(Identity)::: , consultez [étapes suivantes](#next).</span><span class="sxs-lookup"><span data-stu-id="48bce-117">For more detailed instructions about creating apps that use :::no-loc(Identity):::, see [Next Steps](#next).</span></span>

<span data-ttu-id="48bce-118">[Plateforme d’identité Microsoft](/azure/active-directory/develop/) :</span><span class="sxs-lookup"><span data-stu-id="48bce-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="48bce-119">Évolution de la plateforme de développement Azure Active Directory (Azure AD).</span><span class="sxs-lookup"><span data-stu-id="48bce-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="48bce-120">Non lié à :::no-loc(ASP.NET Core Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="48bce-120">Unrelated to :::no-loc(ASP.NET Core Identity):::.</span></span>

[!INCLUDE[](~/includes/:::no-loc(Identity):::Server4.md)]

<span data-ttu-id="48bce-121">[Affichez ou téléchargez l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="48bce-121">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="48bce-122">Créer une application Web avec l’authentification</span><span class="sxs-lookup"><span data-stu-id="48bce-122">Create a Web app with authentication</span></span>

<span data-ttu-id="48bce-123">Créez un projet d’application Web ASP.NET Core avec des comptes d’utilisateur individuels.</span><span class="sxs-lookup"><span data-stu-id="48bce-123">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="48bce-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="48bce-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="48bce-125">Sélectionnez **fichier** > **nouveau** > **projet** .</span><span class="sxs-lookup"><span data-stu-id="48bce-125">Select **File** > **New** > **Project** .</span></span>
* <span data-ttu-id="48bce-126">Sélectionnez **Application web ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="48bce-126">Select **ASP.NET Core Web Application** .</span></span> <span data-ttu-id="48bce-127">Nommez le projet **application Web 1** pour avoir le même espace de noms que le projet à télécharger.</span><span class="sxs-lookup"><span data-stu-id="48bce-127">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="48bce-128">Cliquez sur **OK** .</span><span class="sxs-lookup"><span data-stu-id="48bce-128">Click **OK** .</span></span>
* <span data-ttu-id="48bce-129">Sélectionnez une **application Web** ASP.net Core, puis sélectionnez **modifier l’authentification** .</span><span class="sxs-lookup"><span data-stu-id="48bce-129">Select an ASP.NET Core **Web Application** , then select **Change Authentication** .</span></span>
* <span data-ttu-id="48bce-130">Sélectionnez **comptes d’utilisateur individuels** , puis cliquez sur **OK** .</span><span class="sxs-lookup"><span data-stu-id="48bce-130">Select **Individual User Accounts** and click **OK** .</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="48bce-131">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="48bce-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="48bce-132">La commande précédente crée une :::no-loc(Razor)::: application Web à l’aide de sqlite.</span><span class="sxs-lookup"><span data-stu-id="48bce-132">The preceding command creates a :::no-loc(Razor)::: web app using SQLite.</span></span> <span data-ttu-id="48bce-133">Pour créer l’application Web avec la base de données locale, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="48bce-133">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="48bce-134">Le projet généré fournit [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) une [ :::no-loc(Razor)::: bibliothèque de classes](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="48bce-134">The generated project provides [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) as a [:::no-loc(Razor)::: Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="48bce-135">La :::no-loc(Identity)::: :::no-loc(Razor)::: bibliothèque de classes expose des points de terminaison avec la `:::no-loc(Identity):::` zone.</span><span class="sxs-lookup"><span data-stu-id="48bce-135">The :::no-loc(Identity)::: :::no-loc(Razor)::: Class Library exposes endpoints with the `:::no-loc(Identity):::` area.</span></span> <span data-ttu-id="48bce-136">Exemple :</span><span class="sxs-lookup"><span data-stu-id="48bce-136">For example:</span></span>

* <span data-ttu-id="48bce-137">/:::no-loc(Identity):::/Account/Login</span><span class="sxs-lookup"><span data-stu-id="48bce-137">/:::no-loc(Identity):::/Account/Login</span></span>
* <span data-ttu-id="48bce-138">/:::no-loc(Identity):::/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="48bce-138">/:::no-loc(Identity):::/Account/Logout</span></span>
* <span data-ttu-id="48bce-139">/:::no-loc(Identity):::/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="48bce-139">/:::no-loc(Identity):::/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="48bce-140">Appliquer des migrations</span><span class="sxs-lookup"><span data-stu-id="48bce-140">Apply migrations</span></span>

<span data-ttu-id="48bce-141">Appliquez les migrations pour initialiser la base de données.</span><span class="sxs-lookup"><span data-stu-id="48bce-141">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="48bce-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="48bce-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="48bce-143">Exécutez la commande suivante dans la console du gestionnaire de package (PMC) :</span><span class="sxs-lookup"><span data-stu-id="48bce-143">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="48bce-144">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="48bce-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="48bce-145">Les migrations ne sont pas nécessaires pour cette étape lors de l’utilisation de SQLite.</span><span class="sxs-lookup"><span data-stu-id="48bce-145">Migrations are not necessary at this step when using SQLite.</span></span>

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="48bce-146">Pour la base de données locale, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="48bce-146">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="48bce-147">Registre de test et connexion</span><span class="sxs-lookup"><span data-stu-id="48bce-147">Test Register and Login</span></span>

<span data-ttu-id="48bce-148">Exécutez l’application et inscrivez un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="48bce-148">Run the app and register a user.</span></span> <span data-ttu-id="48bce-149">Selon la taille de votre écran, vous devrez peut-être sélectionner le bouton bascule de navigation pour afficher les liens de **connexion** et de **Registre** .</span><span class="sxs-lookup"><span data-stu-id="48bce-149">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a><span data-ttu-id="48bce-150">Configurer les :::no-loc(Identity)::: services</span><span class="sxs-lookup"><span data-stu-id="48bce-150">Configure :::no-loc(Identity)::: services</span></span>

<span data-ttu-id="48bce-151">Les services sont ajoutés dans `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="48bce-151">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="48bce-152">Le modèle par défaut consiste à appeler toutes les méthodes `Add{Service}`, puis toutes les méthodes `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="48bce-152">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=11-99)]

<span data-ttu-id="48bce-153">Le code en surbrillance précédent configure :::no-loc(Identity)::: avec les valeurs d’option par défaut.</span><span class="sxs-lookup"><span data-stu-id="48bce-153">The preceding highlighted code configures :::no-loc(Identity)::: with default option values.</span></span> <span data-ttu-id="48bce-154">Les services sont mis à la disposition de l’application via l' [injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="48bce-154">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="48bce-155">:::no-loc(Identity)::: est activé en appelant <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> .</span><span class="sxs-lookup"><span data-stu-id="48bce-155">:::no-loc(Identity)::: is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="48bce-156">`UseAuthentication` Ajoute l' [intergiciel (middleware](xref:fundamentals/middleware/index) ) d’authentification au pipeline de requête.</span><span class="sxs-lookup"><span data-stu-id="48bce-156">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="48bce-157">L’application générée par un modèle n’utilise pas [d’autorisation](xref:security/authorization/secure-data).</span><span class="sxs-lookup"><span data-stu-id="48bce-157">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="48bce-158">`app.UseAuthorization` est inclus pour s’assurer qu’il est ajouté dans le bon ordre si l’application ajoute une autorisation.</span><span class="sxs-lookup"><span data-stu-id="48bce-158">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="48bce-159">`UseRouting`, `UseAuthentication` , `UseAuthorization` et `UseEndpoints` doivent être appelés dans l’ordre indiqué dans le code précédent.</span><span class="sxs-lookup"><span data-stu-id="48bce-159">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="48bce-160">Pour plus d’informations sur `:::no-loc(Identity):::Options` et `Startup` , consultez <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::Options> et démarrage de l' [application](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="48bce-160">For more information on `:::no-loc(Identity):::Options` and `Startup`, see <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::Options> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-logout-and-registerconfirmation"></a><span data-ttu-id="48bce-161">Registre de génération de modèles, connexion, déconnexion et RegisterConfirmation</span><span class="sxs-lookup"><span data-stu-id="48bce-161">Scaffold Register, Login, LogOut, and RegisterConfirmation</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="48bce-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="48bce-162">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="48bce-163">Ajoutez les `Register` fichiers,, `Login` `LogOut` et `RegisterConfirmation` .</span><span class="sxs-lookup"><span data-stu-id="48bce-163">Add the `Register`, `Login`, `LogOut`, and `RegisterConfirmation` files.</span></span> <span data-ttu-id="48bce-164">Suivez l' [identité de l’échafaudage dans un :::no-loc(Razor)::: projet avec des instructions d’autorisation](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) pour générer le code présenté dans cette section.</span><span class="sxs-lookup"><span data-stu-id="48bce-164">Follow the [Scaffold identity into a :::no-loc(Razor)::: project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="48bce-165">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="48bce-165">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="48bce-166">Si vous avez créé le projet avec le nom **application Web 1** , exécutez les commandes suivantes.</span><span class="sxs-lookup"><span data-stu-id="48bce-166">If you created the project with name **WebApp1** , run the following commands.</span></span> <span data-ttu-id="48bce-167">Sinon, utilisez l’espace de noms correct pour `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="48bce-167">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.RegisterConfirmation"
```

<span data-ttu-id="48bce-168">PowerShell utilise un point-virgule comme séparateur de commande.</span><span class="sxs-lookup"><span data-stu-id="48bce-168">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="48bce-169">Quand vous utilisez PowerShell, échappez les points-virgules dans la liste de fichiers ou placez la liste de fichiers entre guillemets doubles, comme le montre l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="48bce-169">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="48bce-170">Pour plus d’informations sur la génération de modèles automatique :::no-loc(Identity)::: , consultez identification de l' [échafaudage dans un :::no-loc(Razor)::: projet avec autorisation](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="48bce-170">For more information on scaffolding :::no-loc(Identity):::, see [Scaffold identity into a :::no-loc(Razor)::: project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="48bce-171">Examiner le registre</span><span class="sxs-lookup"><span data-stu-id="48bce-171">Examine Register</span></span>

<span data-ttu-id="48bce-172">Quand un utilisateur clique sur le bouton **Register** de la `Register` page, l' `RegisterModel.OnPostAsync` action est appelée.</span><span class="sxs-lookup"><span data-stu-id="48bce-172">When a user clicks the **Register** button on the `Register` page, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="48bce-173">L’utilisateur est créé par [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_:::no-loc(Identity):::_UserManager_1_CreateAsync__0_System_String_) sur l' `_userManager` objet :</span><span class="sxs-lookup"><span data-stu-id="48bce-173">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_:::no-loc(Identity):::_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/:::no-loc(Identity):::/UI/src/Areas/:::no-loc(Identity):::/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->
[!INCLUDE[](~/includes/disableVer.md)]

### <a name="log-in"></a><span data-ttu-id="48bce-174">Se connecter</span><span class="sxs-lookup"><span data-stu-id="48bce-174">Log in</span></span>

<span data-ttu-id="48bce-175">Le formulaire de connexion s’affiche lorsque :</span><span class="sxs-lookup"><span data-stu-id="48bce-175">The Login form is displayed when:</span></span>

* <span data-ttu-id="48bce-176">Le lien **connexion** est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="48bce-176">The **Log in** link is selected.</span></span>
* <span data-ttu-id="48bce-177">Un utilisateur tente d’accéder à une page restreinte à laquelle il n’est pas autorisé à accéder **ou** lorsqu’il n’a pas été authentifié par le système.</span><span class="sxs-lookup"><span data-stu-id="48bce-177">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="48bce-178">Lorsque le formulaire de la page de connexion est envoyé, l' `OnPostAsync` action est appelée.</span><span class="sxs-lookup"><span data-stu-id="48bce-178">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="48bce-179">`PasswordSignInAsync` est appelé sur l' `_signInManager` objet.</span><span class="sxs-lookup"><span data-stu-id="48bce-179">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="48bce-180">Pour plus d’informations sur la façon de prendre des décisions d’autorisation, consultez <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="48bce-180">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="48bce-181">Se déconnecter</span><span class="sxs-lookup"><span data-stu-id="48bce-181">Log out</span></span>

<span data-ttu-id="48bce-182">Le lien de **déconnexion** appelle l' `LogoutModel.OnPost` action.</span><span class="sxs-lookup"><span data-stu-id="48bce-182">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/:::no-loc(Identity):::/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="48bce-183">Dans le code précédent, le code `return RedirectToPage();` doit être une redirection afin que le navigateur exécute une nouvelle demande et que l’identité de l’utilisateur soit mise à jour.</span><span class="sxs-lookup"><span data-stu-id="48bce-183">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="48bce-184">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_SignOutAsync) efface les revendications de l’utilisateur stockées dans un :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="48bce-184">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_SignOutAsync) clears the user's claims stored in a :::no-loc(cookie):::.</span></span>

<span data-ttu-id="48bce-185">La publication est spécifiée dans *pages/Shared/_LoginPartial. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="48bce-185">Post is specified in the *Pages/Shared/_LoginPartial.cshtml* :</span></span>

[!code-cshtml[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-no-locidentity"></a><span data-ttu-id="48bce-186">Test :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="48bce-186">Test :::no-loc(Identity):::</span></span>

<span data-ttu-id="48bce-187">Les modèles de projet Web par défaut autorisent l’accès anonyme aux pages d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="48bce-187">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="48bce-188">Pour tester :::no-loc(Identity)::: , ajoutez [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) :</span><span class="sxs-lookup"><span data-stu-id="48bce-188">To test :::no-loc(Identity):::, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="48bce-189">Si vous êtes connecté, déconnectez-vous. Exécutez l’application et sélectionnez le lien **confidentialité** .</span><span class="sxs-lookup"><span data-stu-id="48bce-189">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="48bce-190">Vous êtes redirigé vers la page de connexion.</span><span class="sxs-lookup"><span data-stu-id="48bce-190">You are redirected to the login page.</span></span>

### <a name="explore-no-locidentity"></a><span data-ttu-id="48bce-191">Explorer :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="48bce-191">Explore :::no-loc(Identity):::</span></span>

<span data-ttu-id="48bce-192">Pour explorer :::no-loc(Identity)::: plus en détail :</span><span class="sxs-lookup"><span data-stu-id="48bce-192">To explore :::no-loc(Identity)::: in more detail:</span></span>

* [<span data-ttu-id="48bce-193">Créer une source d’interface utilisateur d’identité complète</span><span class="sxs-lookup"><span data-stu-id="48bce-193">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="48bce-194">Examinez la source de chaque page et parcourez le débogueur.</span><span class="sxs-lookup"><span data-stu-id="48bce-194">Examine the source of each page and step through the debugger.</span></span>

## <a name="no-locidentity-components"></a><span data-ttu-id="48bce-195">:::no-loc(Identity)::: -</span><span class="sxs-lookup"><span data-stu-id="48bce-195">:::no-loc(Identity)::: Components</span></span>

<span data-ttu-id="48bce-196">Tous les :::no-loc(Identity)::: packages NuGet dépendants sont inclus dans le [ASP.net Core Framework partagé](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span><span class="sxs-lookup"><span data-stu-id="48bce-196">All the :::no-loc(Identity):::-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="48bce-197">Le package principal pour :::no-loc(Identity)::: est [Microsoft. AspNetCore. :::no-loc(Identity)::: ](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(Identity):::/)</span><span class="sxs-lookup"><span data-stu-id="48bce-197">The primary package for :::no-loc(Identity)::: is [Microsoft.AspNetCore.:::no-loc(Identity):::](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(Identity):::/).</span></span> <span data-ttu-id="48bce-198">Ce package contient l’ensemble principal d’interfaces pour :::no-loc(ASP.NET Core Identity)::: et est inclus dans `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="48bce-198">This package contains the core set of interfaces for :::no-loc(ASP.NET Core Identity):::, and is included by `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-no-locaspnet-core-identity"></a><span data-ttu-id="48bce-199">Migration vers :::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="48bce-199">Migrating to :::no-loc(ASP.NET Core Identity):::</span></span>

<span data-ttu-id="48bce-200">Pour plus d’informations et pour obtenir des conseils sur la migration de votre :::no-loc(Identity)::: magasin existant, consultez [ :::no-loc(Identity)::: migrer l’authentification et ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="48bce-200">For more information and guidance on migrating your existing :::no-loc(Identity)::: store, see [Migrate Authentication and :::no-loc(Identity):::](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="48bce-201">Définition de la force du mot de passe</span><span class="sxs-lookup"><span data-stu-id="48bce-201">Setting password strength</span></span>

<span data-ttu-id="48bce-202">Consultez [configuration](#pw) d’un exemple qui définit la configuration minimale requise pour le mot de passe.</span><span class="sxs-lookup"><span data-stu-id="48bce-202">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a><span data-ttu-id="48bce-203">AddDefault :::no-loc(Identity)::: et ajouter:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="48bce-203">AddDefault:::no-loc(Identity)::: and Add:::no-loc(Identity):::</span></span>

<span data-ttu-id="48bce-204"><xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::*> a été introduite dans ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="48bce-204"><xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="48bce-205">L’appel de `AddDefault:::no-loc(Identity):::` est similaire à l’appel de ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="48bce-205">Calling `AddDefault:::no-loc(Identity):::` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="48bce-206">Pour plus d’informations, consultez [ :::no-loc(Identity)::: source AddDefault](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/:::no-loc(Identity):::/UI/src/:::no-loc(Identity):::ServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="48bce-206">See [AddDefault:::no-loc(Identity)::: source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/:::no-loc(Identity):::/UI/src/:::no-loc(Identity):::ServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-no-locidentity-assets"></a><span data-ttu-id="48bce-207">Empêcher la publication d' :::no-loc(Identity)::: actifs statiques</span><span class="sxs-lookup"><span data-stu-id="48bce-207">Prevent publish of static :::no-loc(Identity)::: assets</span></span>

<span data-ttu-id="48bce-208">Pour empêcher la publication :::no-loc(Identity)::: des ressources statiques (feuilles de style et fichiers JavaScript pour l' :::no-loc(Identity)::: interface utilisateur) sur la racine Web, ajoutez la `ResolveStaticWebAssetsInputsDependsOn` propriété et la `Remove:::no-loc(Identity):::Assets` cible suivantes au fichier projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="48bce-208">To prevent publishing static :::no-loc(Identity)::: assets (stylesheets and JavaScript files for :::no-loc(Identity)::: UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `Remove:::no-loc(Identity):::Assets` target to the app's project file:</span></span>

```xml
<PropertyGroup>
  <ResolveStaticWebAssetsInputsDependsOn>Remove:::no-loc(Identity):::Assets</ResolveStaticWebAssetsInputsDependsOn>
</PropertyGroup>

<Target Name="Remove:::no-loc(Identity):::Assets">
  <ItemGroup>
    <StaticWebAsset Remove="@(StaticWebAsset)" Condition="%(SourceId) == 'Microsoft.AspNetCore.:::no-loc(Identity):::.UI'" />
  </ItemGroup>
</Target>
```

<a name="next"></a>

## <a name="next-steps"></a><span data-ttu-id="48bce-209">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="48bce-209">Next Steps</span></span>

* <span data-ttu-id="48bce-210">[:::no-loc(ASP.NET Core Identity)::: code source](https://github.com/dotnet/aspnetcore/tree/master/src/:::no-loc(Identity):::)</span><span class="sxs-lookup"><span data-stu-id="48bce-210">[:::no-loc(ASP.NET Core Identity)::: source code](https://github.com/dotnet/aspnetcore/tree/master/src/:::no-loc(Identity):::)</span></span>
* <span data-ttu-id="48bce-211">Pour plus d’informations sur la configuration de à l’aide de SQLite, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="48bce-211">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring :::no-loc(Identity)::: using SQLite.</span></span>
* [<span data-ttu-id="48bce-212">Configurer :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="48bce-212">Configure :::no-loc(Identity):::</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="48bce-213">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="48bce-213">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="48bce-214">:::no-loc(ASP.NET Core Identity)::: est un système d’appartenance qui ajoute des fonctionnalités de connexion aux applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="48bce-214">:::no-loc(ASP.NET Core Identity)::: is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="48bce-215">Les utilisateurs peuvent créer un compte avec les informations de connexion stockées dans :::no-loc(Identity)::: ou ils peuvent utiliser un fournisseur de connexion externe.</span><span class="sxs-lookup"><span data-stu-id="48bce-215">Users can create an account with the login information stored in :::no-loc(Identity)::: or they can use an external login provider.</span></span> <span data-ttu-id="48bce-216">Les fournisseurs de connexion externes pris en charge incluent [Facebook, Google, Microsoft Account et Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="48bce-216">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="48bce-217">:::no-loc(Identity)::: peut être configuré à l’aide d’une base de données SQL Server pour stocker les noms d’utilisateur, les mots de passe et les données de profil.</span><span class="sxs-lookup"><span data-stu-id="48bce-217">:::no-loc(Identity)::: can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="48bce-218">Vous pouvez également utiliser un autre magasin persistant, par exemple, le stockage table Azure.</span><span class="sxs-lookup"><span data-stu-id="48bce-218">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="48bce-219">[Affichez ou téléchargez l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-:::no-loc(Identity):::DemoComplete/) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="48bce-219">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-:::no-loc(Identity):::DemoComplete/) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="48bce-220">Dans cette rubrique, vous allez apprendre à utiliser :::no-loc(Identity)::: pour inscrire, se connecter et déconnecter un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="48bce-220">In this topic, you learn how to use :::no-loc(Identity)::: to register, log in, and log out a user.</span></span> <span data-ttu-id="48bce-221">Pour obtenir des instructions plus détaillées sur la création d’applications qui utilisent :::no-loc(Identity)::: , consultez la section étapes suivantes à la fin de cet article.</span><span class="sxs-lookup"><span data-stu-id="48bce-221">For more detailed instructions about creating apps that use :::no-loc(Identity):::, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a><span data-ttu-id="48bce-222">AddDefault :::no-loc(Identity)::: et ajouter:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="48bce-222">AddDefault:::no-loc(Identity)::: and Add:::no-loc(Identity):::</span></span>

<span data-ttu-id="48bce-223"><xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::*> a été introduite dans ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="48bce-223"><xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="48bce-224">L’appel de `AddDefault:::no-loc(Identity):::` est similaire à l’appel de ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="48bce-224">Calling `AddDefault:::no-loc(Identity):::` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="48bce-225">Pour plus d’informations, consultez [ :::no-loc(Identity)::: source AddDefault](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/:::no-loc(Identity):::/UI/src/:::no-loc(Identity):::ServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="48bce-225">See [AddDefault:::no-loc(Identity)::: source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/:::no-loc(Identity):::/UI/src/:::no-loc(Identity):::ServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="48bce-226">Créer une application Web avec l’authentification</span><span class="sxs-lookup"><span data-stu-id="48bce-226">Create a Web app with authentication</span></span>

<span data-ttu-id="48bce-227">Créez un projet d’application Web ASP.NET Core avec des comptes d’utilisateur individuels.</span><span class="sxs-lookup"><span data-stu-id="48bce-227">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="48bce-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="48bce-228">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="48bce-229">Sélectionnez **fichier** > **nouveau** > **projet** .</span><span class="sxs-lookup"><span data-stu-id="48bce-229">Select **File** > **New** > **Project** .</span></span>
* <span data-ttu-id="48bce-230">Sélectionnez **Application web ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="48bce-230">Select **ASP.NET Core Web Application** .</span></span> <span data-ttu-id="48bce-231">Nommez le projet **application Web 1** pour avoir le même espace de noms que le projet à télécharger.</span><span class="sxs-lookup"><span data-stu-id="48bce-231">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="48bce-232">Cliquez sur **OK** .</span><span class="sxs-lookup"><span data-stu-id="48bce-232">Click **OK** .</span></span>
* <span data-ttu-id="48bce-233">Sélectionnez une **application Web** ASP.net Core, puis sélectionnez **modifier l’authentification** .</span><span class="sxs-lookup"><span data-stu-id="48bce-233">Select an ASP.NET Core **Web Application** , then select **Change Authentication** .</span></span>
* <span data-ttu-id="48bce-234">Sélectionnez **comptes d’utilisateur individuels** , puis cliquez sur **OK** .</span><span class="sxs-lookup"><span data-stu-id="48bce-234">Select **Individual User Accounts** and click **OK** .</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="48bce-235">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="48bce-235">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="48bce-236">Le projet généré fournit [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) une [ :::no-loc(Razor)::: bibliothèque de classes](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="48bce-236">The generated project provides [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) as a [:::no-loc(Razor)::: Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="48bce-237">La :::no-loc(Identity)::: :::no-loc(Razor)::: bibliothèque de classes expose des points de terminaison avec la `:::no-loc(Identity):::` zone.</span><span class="sxs-lookup"><span data-stu-id="48bce-237">The :::no-loc(Identity)::: :::no-loc(Razor)::: Class Library exposes endpoints with the `:::no-loc(Identity):::` area.</span></span> <span data-ttu-id="48bce-238">Exemple :</span><span class="sxs-lookup"><span data-stu-id="48bce-238">For example:</span></span>

* <span data-ttu-id="48bce-239">/:::no-loc(Identity):::/Account/Login</span><span class="sxs-lookup"><span data-stu-id="48bce-239">/:::no-loc(Identity):::/Account/Login</span></span>
* <span data-ttu-id="48bce-240">/:::no-loc(Identity):::/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="48bce-240">/:::no-loc(Identity):::/Account/Logout</span></span>
* <span data-ttu-id="48bce-241">/:::no-loc(Identity):::/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="48bce-241">/:::no-loc(Identity):::/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="48bce-242">Appliquer des migrations</span><span class="sxs-lookup"><span data-stu-id="48bce-242">Apply migrations</span></span>

<span data-ttu-id="48bce-243">Appliquez les migrations pour initialiser la base de données.</span><span class="sxs-lookup"><span data-stu-id="48bce-243">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="48bce-244">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="48bce-244">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="48bce-245">Exécutez la commande suivante dans la console du gestionnaire de package (PMC) :</span><span class="sxs-lookup"><span data-stu-id="48bce-245">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="48bce-246">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="48bce-246">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="48bce-247">Registre de test et connexion</span><span class="sxs-lookup"><span data-stu-id="48bce-247">Test Register and Login</span></span>

<span data-ttu-id="48bce-248">Exécutez l’application et inscrivez un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="48bce-248">Run the app and register a user.</span></span> <span data-ttu-id="48bce-249">Selon la taille de votre écran, vous devrez peut-être sélectionner le bouton bascule de navigation pour afficher les liens de **connexion** et de **Registre** .</span><span class="sxs-lookup"><span data-stu-id="48bce-249">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a><span data-ttu-id="48bce-250">Configurer les :::no-loc(Identity)::: services</span><span class="sxs-lookup"><span data-stu-id="48bce-250">Configure :::no-loc(Identity)::: services</span></span>

<span data-ttu-id="48bce-251">Les services sont ajoutés dans `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="48bce-251">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="48bce-252">Le modèle par défaut consiste à appeler toutes les méthodes `Add{Service}`, puis toutes les méthodes `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="48bce-252">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="48bce-253">Le code précédent configure :::no-loc(Identity)::: avec les valeurs d’option par défaut.</span><span class="sxs-lookup"><span data-stu-id="48bce-253">The preceding code configures :::no-loc(Identity)::: with default option values.</span></span> <span data-ttu-id="48bce-254">Les services sont mis à la disposition de l’application via l' [injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="48bce-254">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="48bce-255">:::no-loc(Identity)::: est activé en appelant [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="48bce-255">:::no-loc(Identity)::: is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="48bce-256">`UseAuthentication` Ajoute l' [intergiciel (middleware](xref:fundamentals/middleware/index) ) d’authentification au pipeline de requête.</span><span class="sxs-lookup"><span data-stu-id="48bce-256">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="48bce-257">Pour plus d’informations, consultez la [ :::no-loc(Identity)::: classe options](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) et le démarrage de l' [application](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="48bce-257">For more information, see the [:::no-loc(Identity):::Options Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="48bce-258">Registre de génération de modèles, connexion et déconnexion</span><span class="sxs-lookup"><span data-stu-id="48bce-258">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="48bce-259">Suivez l' [identité de l’échafaudage dans un :::no-loc(Razor)::: projet avec des instructions d’autorisation](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) pour générer le code présenté dans cette section.</span><span class="sxs-lookup"><span data-stu-id="48bce-259">Follow the [Scaffold identity into a :::no-loc(Razor)::: project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="48bce-260">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="48bce-260">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="48bce-261">Ajoutez les fichiers Register, login et LogOut.</span><span class="sxs-lookup"><span data-stu-id="48bce-261">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="48bce-262">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="48bce-262">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="48bce-263">Si vous avez créé le projet avec le nom **application Web 1** , exécutez les commandes suivantes.</span><span class="sxs-lookup"><span data-stu-id="48bce-263">If you created the project with name **WebApp1** , run the following commands.</span></span> <span data-ttu-id="48bce-264">Sinon, utilisez l’espace de noms correct pour `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="48bce-264">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="48bce-265">PowerShell utilise un point-virgule comme séparateur de commande.</span><span class="sxs-lookup"><span data-stu-id="48bce-265">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="48bce-266">Quand vous utilisez PowerShell, échappez les points-virgules dans la liste de fichiers ou placez la liste de fichiers entre guillemets doubles, comme le montre l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="48bce-266">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="48bce-267">Examiner le registre</span><span class="sxs-lookup"><span data-stu-id="48bce-267">Examine Register</span></span>

<span data-ttu-id="48bce-268">Quand un utilisateur clique sur le lien **Register** , l' `RegisterModel.OnPostAsync` action est appelée.</span><span class="sxs-lookup"><span data-stu-id="48bce-268">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="48bce-269">L’utilisateur est créé par [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_:::no-loc(Identity):::_UserManager_1_CreateAsync__0_System_String_) sur l' `_userManager` objet :</span><span class="sxs-lookup"><span data-stu-id="48bce-269">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_:::no-loc(Identity):::_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="48bce-270">Si l’utilisateur a été créé avec succès, l’utilisateur est connecté par l’appel à `_signInManager.SignInAsync` .</span><span class="sxs-lookup"><span data-stu-id="48bce-270">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="48bce-271">**Remarque :** Consultez [confirmation du compte](xref:security/authentication/accconfirm#prevent-login-at-registration) pour connaître les étapes permettant d’empêcher la connexion immédiate lors de l’inscription.</span><span class="sxs-lookup"><span data-stu-id="48bce-271">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="48bce-272">Se connecter</span><span class="sxs-lookup"><span data-stu-id="48bce-272">Log in</span></span>

<span data-ttu-id="48bce-273">Le formulaire de connexion s’affiche lorsque :</span><span class="sxs-lookup"><span data-stu-id="48bce-273">The Login form is displayed when:</span></span>

* <span data-ttu-id="48bce-274">Le lien **connexion** est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="48bce-274">The **Log in** link is selected.</span></span>
* <span data-ttu-id="48bce-275">Un utilisateur tente d’accéder à une page restreinte à laquelle il n’est pas autorisé à accéder **ou** lorsqu’il n’a pas été authentifié par le système.</span><span class="sxs-lookup"><span data-stu-id="48bce-275">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="48bce-276">Lorsque le formulaire de la page de connexion est envoyé, l' `OnPostAsync` action est appelée.</span><span class="sxs-lookup"><span data-stu-id="48bce-276">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="48bce-277">`PasswordSignInAsync` est appelé sur l' `_signInManager` objet.</span><span class="sxs-lookup"><span data-stu-id="48bce-277">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="48bce-278">Pour plus d’informations sur la façon de prendre des décisions d’autorisation, consultez <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="48bce-278">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="48bce-279">Se déconnecter</span><span class="sxs-lookup"><span data-stu-id="48bce-279">Log out</span></span>

<span data-ttu-id="48bce-280">Le lien de **déconnexion** appelle l' `LogoutModel.OnPost` action.</span><span class="sxs-lookup"><span data-stu-id="48bce-280">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/:::no-loc(Identity):::/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="48bce-281">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_SignOutAsync) efface les revendications de l’utilisateur stockées dans un :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="48bce-281">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_SignOutAsync) clears the user's claims stored in a :::no-loc(cookie):::.</span></span>

<span data-ttu-id="48bce-282">La publication est spécifiée dans *pages/Shared/_LoginPartial. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="48bce-282">Post is specified in the *Pages/Shared/_LoginPartial.cshtml* :</span></span>

[!code-cshtml[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-no-locidentity"></a><span data-ttu-id="48bce-283">Test :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="48bce-283">Test :::no-loc(Identity):::</span></span>

<span data-ttu-id="48bce-284">Les modèles de projet Web par défaut autorisent l’accès anonyme aux pages d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="48bce-284">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="48bce-285">Pour tester :::no-loc(Identity)::: , ajoutez [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) à la page confidentialité.</span><span class="sxs-lookup"><span data-stu-id="48bce-285">To test :::no-loc(Identity):::, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="48bce-286">Si vous êtes connecté, déconnectez-vous. Exécutez l’application et sélectionnez le lien **confidentialité** .</span><span class="sxs-lookup"><span data-stu-id="48bce-286">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="48bce-287">Vous êtes redirigé vers la page de connexion.</span><span class="sxs-lookup"><span data-stu-id="48bce-287">You are redirected to the login page.</span></span>

### <a name="explore-no-locidentity"></a><span data-ttu-id="48bce-288">Explorer :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="48bce-288">Explore :::no-loc(Identity):::</span></span>

<span data-ttu-id="48bce-289">Pour explorer :::no-loc(Identity)::: plus en détail :</span><span class="sxs-lookup"><span data-stu-id="48bce-289">To explore :::no-loc(Identity)::: in more detail:</span></span>

* [<span data-ttu-id="48bce-290">Créer une source d’interface utilisateur d’identité complète</span><span class="sxs-lookup"><span data-stu-id="48bce-290">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="48bce-291">Examinez la source de chaque page et parcourez le débogueur.</span><span class="sxs-lookup"><span data-stu-id="48bce-291">Examine the source of each page and step through the debugger.</span></span>

## <a name="no-locidentity-components"></a><span data-ttu-id="48bce-292">:::no-loc(Identity)::: -</span><span class="sxs-lookup"><span data-stu-id="48bce-292">:::no-loc(Identity)::: Components</span></span>

<span data-ttu-id="48bce-293">Tous les :::no-loc(Identity)::: packages NuGet dépendants sont inclus dans le sous- [package Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="48bce-293">All the :::no-loc(Identity)::: dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="48bce-294">Le package principal pour :::no-loc(Identity)::: est [Microsoft. AspNetCore. :::no-loc(Identity)::: ](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(Identity):::/)</span><span class="sxs-lookup"><span data-stu-id="48bce-294">The primary package for :::no-loc(Identity)::: is [Microsoft.AspNetCore.:::no-loc(Identity):::](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(Identity):::/).</span></span> <span data-ttu-id="48bce-295">Ce package contient l’ensemble principal d’interfaces pour :::no-loc(ASP.NET Core Identity)::: et est inclus dans `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="48bce-295">This package contains the core set of interfaces for :::no-loc(ASP.NET Core Identity):::, and is included by `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-no-locaspnet-core-identity"></a><span data-ttu-id="48bce-296">Migration vers :::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="48bce-296">Migrating to :::no-loc(ASP.NET Core Identity):::</span></span>

<span data-ttu-id="48bce-297">Pour plus d’informations et pour obtenir des conseils sur la migration de votre :::no-loc(Identity)::: magasin existant, consultez [ :::no-loc(Identity)::: migrer l’authentification et ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="48bce-297">For more information and guidance on migrating your existing :::no-loc(Identity)::: store, see [Migrate Authentication and :::no-loc(Identity):::](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="48bce-298">Définition de la force du mot de passe</span><span class="sxs-lookup"><span data-stu-id="48bce-298">Setting password strength</span></span>

<span data-ttu-id="48bce-299">Consultez [configuration](#pw) d’un exemple qui définit la configuration minimale requise pour le mot de passe.</span><span class="sxs-lookup"><span data-stu-id="48bce-299">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="48bce-300">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="48bce-300">Next Steps</span></span>

* <span data-ttu-id="48bce-301">Pour plus d’informations sur la configuration de à l’aide de SQLite, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="48bce-301">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring :::no-loc(Identity)::: using SQLite.</span></span>
* [<span data-ttu-id="48bce-302">Configurer :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="48bce-302">Configure :::no-loc(Identity):::</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
