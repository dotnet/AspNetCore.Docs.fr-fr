---
title: Présentation de Identity sur ASP.net Core
author: rick-anderson
description: Utilisez Identity avec une application ASP.net core. Découvrez comment définir les exigences de mot de passe (RequireDigit, RequiredLength, RequiredUniqueChars, etc.).
ms.author: riande
ms.date: 7/15/2020
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
uid: security/authentication/identity
ms.openlocfilehash: ad4184fce494ba06acf7e583a42a54d04d37ea20
ms.sourcegitcommit: 92439194682dc788b8b5b3a08bd2184dc00e200b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96556643"
---
# <a name="introduction-to-no-locidentity-on-aspnet-core"></a><span data-ttu-id="f2899-104">Présentation de Identity sur ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="f2899-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f2899-105">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f2899-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f2899-106">ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="f2899-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="f2899-107">Est une API qui prend en charge la fonctionnalité de connexion de l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="f2899-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="f2899-108">Gère les utilisateurs, les mots de passe, les données de profil, les rôles, les revendications, les jetons, la confirmation par e-mail et bien plus encore.</span><span class="sxs-lookup"><span data-stu-id="f2899-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="f2899-109">Les utilisateurs peuvent créer un compte avec les informations de connexion stockées dans Identity ou ils peuvent utiliser un fournisseur de connexion externe.</span><span class="sxs-lookup"><span data-stu-id="f2899-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="f2899-110">Les fournisseurs de connexion externes pris en charge incluent [Facebook, Google, Microsoft Account et Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="f2899-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

[!INCLUDE[](~/includes/requireAuth.md)]

<span data-ttu-id="f2899-111">Le [ Identity code source](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) est disponible sur GitHub.</span><span class="sxs-lookup"><span data-stu-id="f2899-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="f2899-112">[Structure Identity ](xref:security/authentication/scaffold-identity) et affichez les fichiers générés pour examiner l’interaction du modèle avec Identity .</span><span class="sxs-lookup"><span data-stu-id="f2899-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

<span data-ttu-id="f2899-113">Identity est généralement configurée à l’aide d’une base de données SQL Server pour stocker les noms d’utilisateur, les mots de passe et les données de profil.</span><span class="sxs-lookup"><span data-stu-id="f2899-113">Identity is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="f2899-114">Vous pouvez également utiliser un autre magasin persistant, par exemple, le stockage table Azure.</span><span class="sxs-lookup"><span data-stu-id="f2899-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="f2899-115">Dans cette rubrique, vous allez apprendre à utiliser Identity pour inscrire, se connecter et déconnecter un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="f2899-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="f2899-116">Remarque : les modèles traitent le nom d’utilisateur et l’adresse de messagerie comme les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="f2899-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="f2899-117">Pour obtenir des instructions plus détaillées sur la création d’applications qui utilisent Identity , consultez [étapes suivantes](#next).</span><span class="sxs-lookup"><span data-stu-id="f2899-117">For more detailed instructions about creating apps that use Identity, see [Next Steps](#next).</span></span>

<span data-ttu-id="f2899-118">[Plateforme d’identité Microsoft](/azure/active-directory/develop/) :</span><span class="sxs-lookup"><span data-stu-id="f2899-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="f2899-119">Évolution de la plateforme de développement Azure Active Directory (Azure AD).</span><span class="sxs-lookup"><span data-stu-id="f2899-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="f2899-120">Non lié à ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="f2899-120">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="f2899-121">[Affichez ou téléchargez l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="f2899-121">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="f2899-122">Créer une application Web avec l’authentification</span><span class="sxs-lookup"><span data-stu-id="f2899-122">Create a Web app with authentication</span></span>

<span data-ttu-id="f2899-123">Créez un projet d’application Web ASP.NET Core avec des comptes d’utilisateur individuels.</span><span class="sxs-lookup"><span data-stu-id="f2899-123">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2899-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2899-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f2899-125">Sélectionnez **fichier** > **nouveau** > **projet**.</span><span class="sxs-lookup"><span data-stu-id="f2899-125">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="f2899-126">Sélectionnez **Application web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="f2899-126">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="f2899-127">Nommez le projet **application Web 1** pour avoir le même espace de noms que le projet à télécharger.</span><span class="sxs-lookup"><span data-stu-id="f2899-127">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="f2899-128">Cliquez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="f2899-128">Click **OK**.</span></span>
* <span data-ttu-id="f2899-129">Sélectionnez une **application Web** ASP.net Core, puis sélectionnez **modifier l’authentification**.</span><span class="sxs-lookup"><span data-stu-id="f2899-129">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="f2899-130">Sélectionnez **comptes d’utilisateur individuels** , puis cliquez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="f2899-130">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f2899-131">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="f2899-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="f2899-132">La commande précédente crée une Razor application Web à l’aide de sqlite.</span><span class="sxs-lookup"><span data-stu-id="f2899-132">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="f2899-133">Pour créer l’application Web avec la base de données locale, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="f2899-133">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="f2899-134">Le projet généré fournit [ASP.NET Core Identity](xref:security/authentication/identity) une [ Razor bibliothèque de classes](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="f2899-134">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="f2899-135">La Identity Razor bibliothèque de classes expose des points de terminaison avec la `Identity` zone.</span><span class="sxs-lookup"><span data-stu-id="f2899-135">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="f2899-136">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="f2899-136">For example:</span></span>

* <span data-ttu-id="f2899-137">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="f2899-137">/Identity/Account/Login</span></span>
* <span data-ttu-id="f2899-138">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="f2899-138">/Identity/Account/Logout</span></span>
* <span data-ttu-id="f2899-139">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="f2899-139">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="f2899-140">Appliquer des migrations</span><span class="sxs-lookup"><span data-stu-id="f2899-140">Apply migrations</span></span>

<span data-ttu-id="f2899-141">Appliquez les migrations pour initialiser la base de données.</span><span class="sxs-lookup"><span data-stu-id="f2899-141">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2899-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2899-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f2899-143">Exécutez la commande suivante dans la console du gestionnaire de package (PMC) :</span><span class="sxs-lookup"><span data-stu-id="f2899-143">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="f2899-144">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="f2899-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="f2899-145">Les migrations ne sont pas nécessaires pour cette étape lors de l’utilisation de SQLite.</span><span class="sxs-lookup"><span data-stu-id="f2899-145">Migrations are not necessary at this step when using SQLite.</span></span>

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="f2899-146">Pour la base de données locale, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="f2899-146">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="f2899-147">Registre de test et connexion</span><span class="sxs-lookup"><span data-stu-id="f2899-147">Test Register and Login</span></span>

<span data-ttu-id="f2899-148">Exécutez l’application et inscrivez un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="f2899-148">Run the app and register a user.</span></span> <span data-ttu-id="f2899-149">Selon la taille de votre écran, vous devrez peut-être sélectionner le bouton bascule de navigation pour afficher les liens de **connexion** et de **Registre** .</span><span class="sxs-lookup"><span data-stu-id="f2899-149">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a><span data-ttu-id="f2899-150">Configurer les Identity services</span><span class="sxs-lookup"><span data-stu-id="f2899-150">Configure Identity services</span></span>

<span data-ttu-id="f2899-151">Les services sont ajoutés dans `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f2899-151">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="f2899-152">Le modèle par défaut consiste à appeler toutes les méthodes `Add{Service}`, puis toutes les méthodes `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="f2899-152">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=11-99)]

<span data-ttu-id="f2899-153">Le code en surbrillance précédent configure Identity avec les valeurs d’option par défaut.</span><span class="sxs-lookup"><span data-stu-id="f2899-153">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="f2899-154">Les services sont mis à la disposition de l’application via l' [injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="f2899-154">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="f2899-155">Identity est activé en appelant <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> .</span><span class="sxs-lookup"><span data-stu-id="f2899-155">Identity is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="f2899-156">`UseAuthentication` Ajoute l' [intergiciel (middleware](xref:fundamentals/middleware/index) ) d’authentification au pipeline de requête.</span><span class="sxs-lookup"><span data-stu-id="f2899-156">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](identity/sample/WebApp5x/Startup.cs?name=snippet_configureservices&highlight=12-99)]

<span data-ttu-id="f2899-157">Le code précédent configure Identity avec les valeurs d’option par défaut.</span><span class="sxs-lookup"><span data-stu-id="f2899-157">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="f2899-158">Les services sont mis à la disposition de l’application via l' [injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="f2899-158">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="f2899-159">Identity est activé en appelant [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="f2899-159">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="f2899-160">`UseAuthentication` Ajoute l' [intergiciel (middleware](xref:fundamentals/middleware/index) ) d’authentification au pipeline de requête.</span><span class="sxs-lookup"><span data-stu-id="f2899-160">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp5x/Startup.cs?name=snippet_configure&highlight=19)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f2899-161">L’application générée par un modèle n’utilise pas [d’autorisation](xref:security/authorization/secure-data).</span><span class="sxs-lookup"><span data-stu-id="f2899-161">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="f2899-162">`app.UseAuthorization` est inclus pour s’assurer qu’il est ajouté dans le bon ordre si l’application ajoute une autorisation.</span><span class="sxs-lookup"><span data-stu-id="f2899-162">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="f2899-163">`UseRouting`, `UseAuthentication` , `UseAuthorization` et `UseEndpoints` doivent être appelés dans l’ordre indiqué dans le code précédent.</span><span class="sxs-lookup"><span data-stu-id="f2899-163">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="f2899-164">Pour plus d’informations sur `IdentityOptions` et `Startup` , consultez <xref:Microsoft.AspNetCore.Identity.IdentityOptions> et démarrage de l' [application](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="f2899-164">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-logout-and-registerconfirmation"></a><span data-ttu-id="f2899-165">Registre de génération de modèles, connexion, déconnexion et RegisterConfirmation</span><span class="sxs-lookup"><span data-stu-id="f2899-165">Scaffold Register, Login, LogOut, and RegisterConfirmation</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2899-166">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2899-166">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f2899-167">Ajoutez les `Register` fichiers,, `Login` `LogOut` et `RegisterConfirmation` .</span><span class="sxs-lookup"><span data-stu-id="f2899-167">Add the `Register`, `Login`, `LogOut`, and `RegisterConfirmation` files.</span></span> <span data-ttu-id="f2899-168">Suivez l' [identité de l’échafaudage dans un Razor projet avec des instructions d’autorisation](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) pour générer le code présenté dans cette section.</span><span class="sxs-lookup"><span data-stu-id="f2899-168">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f2899-169">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="f2899-169">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="f2899-170">Si vous avez créé le projet avec le nom **application Web 1**, exécutez les commandes suivantes.</span><span class="sxs-lookup"><span data-stu-id="f2899-170">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="f2899-171">Sinon, utilisez l’espace de noms correct pour `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="f2899-171">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.RegisterConfirmation"
```

<span data-ttu-id="f2899-172">PowerShell utilise un point-virgule comme séparateur de commande.</span><span class="sxs-lookup"><span data-stu-id="f2899-172">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="f2899-173">Quand vous utilisez PowerShell, échappez les points-virgules dans la liste de fichiers ou placez la liste de fichiers entre guillemets doubles, comme le montre l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="f2899-173">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="f2899-174">Pour plus d’informations sur la génération de modèles automatique Identity , consultez identification de l' [échafaudage dans un Razor projet avec autorisation](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="f2899-174">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="f2899-175">Examiner le registre</span><span class="sxs-lookup"><span data-stu-id="f2899-175">Examine Register</span></span>

<span data-ttu-id="f2899-176">Quand un utilisateur clique sur le bouton **Register** de la `Register` page, l' `RegisterModel.OnPostAsync` action est appelée.</span><span class="sxs-lookup"><span data-stu-id="f2899-176">When a user clicks the **Register** button on the `Register` page, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="f2899-177">L’utilisateur est créé par [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) sur l' `_userManager` objet :</span><span class="sxs-lookup"><span data-stu-id="f2899-177">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/Identity/UI/src/Areas/Identity/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->
[!INCLUDE[](~/includes/disableVer.md)]

### <a name="log-in"></a><span data-ttu-id="f2899-178">Se connecter</span><span class="sxs-lookup"><span data-stu-id="f2899-178">Log in</span></span>

<span data-ttu-id="f2899-179">Le formulaire de connexion s’affiche lorsque :</span><span class="sxs-lookup"><span data-stu-id="f2899-179">The Login form is displayed when:</span></span>

* <span data-ttu-id="f2899-180">Le lien **connexion** est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="f2899-180">The **Log in** link is selected.</span></span>
* <span data-ttu-id="f2899-181">Un utilisateur tente d’accéder à une page restreinte à laquelle il n’est pas autorisé à accéder **ou** lorsqu’il n’a pas été authentifié par le système.</span><span class="sxs-lookup"><span data-stu-id="f2899-181">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="f2899-182">Lorsque le formulaire de la page de connexion est envoyé, l' `OnPostAsync` action est appelée.</span><span class="sxs-lookup"><span data-stu-id="f2899-182">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="f2899-183">`PasswordSignInAsync` est appelé sur l' `_signInManager` objet.</span><span class="sxs-lookup"><span data-stu-id="f2899-183">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="f2899-184">Pour plus d’informations sur la façon de prendre des décisions d’autorisation, consultez <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="f2899-184">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="f2899-185">Se déconnecter</span><span class="sxs-lookup"><span data-stu-id="f2899-185">Log out</span></span>

<span data-ttu-id="f2899-186">Le lien de **déconnexion** appelle l' `LogoutModel.OnPost` action.</span><span class="sxs-lookup"><span data-stu-id="f2899-186">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="f2899-187">Dans le code précédent, le code `return RedirectToPage();` doit être une redirection afin que le navigateur exécute une nouvelle demande et que l’identité de l’utilisateur soit mise à jour.</span><span class="sxs-lookup"><span data-stu-id="f2899-187">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="f2899-188">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) efface les revendications de l’utilisateur stockées dans un cookie .</span><span class="sxs-lookup"><span data-stu-id="f2899-188">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="f2899-189">La publication est spécifiée dans *pages/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="f2899-189">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-no-locidentity"></a><span data-ttu-id="f2899-190">Test Identity</span><span class="sxs-lookup"><span data-stu-id="f2899-190">Test Identity</span></span>

<span data-ttu-id="f2899-191">Les modèles de projet Web par défaut autorisent l’accès anonyme aux pages d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="f2899-191">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="f2899-192">Pour tester Identity , ajoutez [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) :</span><span class="sxs-lookup"><span data-stu-id="f2899-192">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="f2899-193">Si vous êtes connecté, déconnectez-vous. Exécutez l’application et sélectionnez le lien **confidentialité** .</span><span class="sxs-lookup"><span data-stu-id="f2899-193">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="f2899-194">Vous êtes redirigé vers la page de connexion.</span><span class="sxs-lookup"><span data-stu-id="f2899-194">You are redirected to the login page.</span></span>

### <a name="explore-no-locidentity"></a><span data-ttu-id="f2899-195">Explorer Identity</span><span class="sxs-lookup"><span data-stu-id="f2899-195">Explore Identity</span></span>

<span data-ttu-id="f2899-196">Pour explorer Identity plus en détail :</span><span class="sxs-lookup"><span data-stu-id="f2899-196">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="f2899-197">Créer une source d’interface utilisateur d’identité complète</span><span class="sxs-lookup"><span data-stu-id="f2899-197">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="f2899-198">Examinez la source de chaque page et parcourez le débogueur.</span><span class="sxs-lookup"><span data-stu-id="f2899-198">Examine the source of each page and step through the debugger.</span></span>

## <a name="no-locidentity-components"></a><span data-ttu-id="f2899-199">Identity -</span><span class="sxs-lookup"><span data-stu-id="f2899-199">Identity Components</span></span>

<span data-ttu-id="f2899-200">Tous les Identity packages NuGet dépendants sont inclus dans le [ASP.net Core Framework partagé](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span><span class="sxs-lookup"><span data-stu-id="f2899-200">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="f2899-201">Le package principal pour Identity est [Microsoft. AspNetCore. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)</span><span class="sxs-lookup"><span data-stu-id="f2899-201">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="f2899-202">Ce package contient l’ensemble principal d’interfaces pour ASP.NET Core Identity et est inclus dans `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="f2899-202">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-no-locaspnet-core-identity"></a><span data-ttu-id="f2899-203">Migration vers ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="f2899-203">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="f2899-204">Pour plus d’informations et pour obtenir des conseils sur la migration de votre Identity magasin existant, consultez [ Identity migrer l’authentification et ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="f2899-204">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="f2899-205">Définition de la force du mot de passe</span><span class="sxs-lookup"><span data-stu-id="f2899-205">Setting password strength</span></span>

<span data-ttu-id="f2899-206">Consultez [configuration](#pw) d’un exemple qui définit la configuration minimale requise pour le mot de passe.</span><span class="sxs-lookup"><span data-stu-id="f2899-206">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a><span data-ttu-id="f2899-207">AddDefault Identity et ajouterIdentity</span><span class="sxs-lookup"><span data-stu-id="f2899-207">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="f2899-208"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> a été introduite dans ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="f2899-208"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="f2899-209">L’appel de `AddDefaultIdentity` est similaire à l’appel de ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="f2899-209">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="f2899-210">Pour plus d’informations, consultez [ Identity source AddDefault](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="f2899-210">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-no-locidentity-assets"></a><span data-ttu-id="f2899-211">Empêcher la publication d' Identity actifs statiques</span><span class="sxs-lookup"><span data-stu-id="f2899-211">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="f2899-212">Pour empêcher la publication Identity des ressources statiques (feuilles de style et fichiers JavaScript pour l' Identity interface utilisateur) sur la racine Web, ajoutez la `ResolveStaticWebAssetsInputsDependsOn` propriété et la `RemoveIdentityAssets` cible suivantes au fichier projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="f2899-212">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

```xml
<PropertyGroup>
  <ResolveStaticWebAssetsInputsDependsOn>RemoveIdentityAssets</ResolveStaticWebAssetsInputsDependsOn>
</PropertyGroup>

<Target Name="RemoveIdentityAssets">
  <ItemGroup>
    <StaticWebAsset Remove="@(StaticWebAsset)" Condition="%(SourceId) == 'Microsoft.AspNetCore.Identity.UI'" />
  </ItemGroup>
</Target>
```

<a name="next"></a>

## <a name="next-steps"></a><span data-ttu-id="f2899-213">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="f2899-213">Next Steps</span></span>

* <span data-ttu-id="f2899-214">[ASP.NET Core Identity code source](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span><span class="sxs-lookup"><span data-stu-id="f2899-214">[ASP.NET Core Identity source code](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span></span>
* <span data-ttu-id="f2899-215">Pour plus d’informations sur la configuration de à l’aide de SQLite, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) Identity .</span><span class="sxs-lookup"><span data-stu-id="f2899-215">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="f2899-216">Configuration de Identity</span><span class="sxs-lookup"><span data-stu-id="f2899-216">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f2899-217">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f2899-217">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f2899-218">ASP.NET Core Identity est un système d’appartenance qui ajoute des fonctionnalités de connexion aux applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f2899-218">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="f2899-219">Les utilisateurs peuvent créer un compte avec les informations de connexion stockées dans Identity ou ils peuvent utiliser un fournisseur de connexion externe.</span><span class="sxs-lookup"><span data-stu-id="f2899-219">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="f2899-220">Les fournisseurs de connexion externes pris en charge incluent [Facebook, Google, Microsoft Account et Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="f2899-220">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="f2899-221">Identity peut être configuré à l’aide d’une base de données SQL Server pour stocker les noms d’utilisateur, les mots de passe et les données de profil.</span><span class="sxs-lookup"><span data-stu-id="f2899-221">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="f2899-222">Vous pouvez également utiliser un autre magasin persistant, par exemple, le stockage table Azure.</span><span class="sxs-lookup"><span data-stu-id="f2899-222">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="f2899-223">[Affichez ou téléchargez l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="f2899-223">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="f2899-224">Dans cette rubrique, vous allez apprendre à utiliser Identity pour inscrire, se connecter et déconnecter un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="f2899-224">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="f2899-225">Pour obtenir des instructions plus détaillées sur la création d’applications qui utilisent Identity , consultez la section étapes suivantes à la fin de cet article.</span><span class="sxs-lookup"><span data-stu-id="f2899-225">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a><span data-ttu-id="f2899-226">AddDefault Identity et ajouterIdentity</span><span class="sxs-lookup"><span data-stu-id="f2899-226">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="f2899-227"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> a été introduite dans ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="f2899-227"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="f2899-228">L’appel de `AddDefaultIdentity` est similaire à l’appel de ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="f2899-228">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="f2899-229">Pour plus d’informations, consultez [ Identity source AddDefault](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="f2899-229">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="f2899-230">Créer une application Web avec l’authentification</span><span class="sxs-lookup"><span data-stu-id="f2899-230">Create a Web app with authentication</span></span>

<span data-ttu-id="f2899-231">Créez un projet d’application Web ASP.NET Core avec des comptes d’utilisateur individuels.</span><span class="sxs-lookup"><span data-stu-id="f2899-231">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2899-232">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2899-232">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f2899-233">Sélectionnez **fichier** > **nouveau** > **projet**.</span><span class="sxs-lookup"><span data-stu-id="f2899-233">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="f2899-234">Sélectionnez **Application web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="f2899-234">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="f2899-235">Nommez le projet **application Web 1** pour avoir le même espace de noms que le projet à télécharger.</span><span class="sxs-lookup"><span data-stu-id="f2899-235">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="f2899-236">Cliquez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="f2899-236">Click **OK**.</span></span>
* <span data-ttu-id="f2899-237">Sélectionnez une **application Web** ASP.net Core, puis sélectionnez **modifier l’authentification**.</span><span class="sxs-lookup"><span data-stu-id="f2899-237">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="f2899-238">Sélectionnez **comptes d’utilisateur individuels** , puis cliquez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="f2899-238">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f2899-239">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="f2899-239">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="f2899-240">Le projet généré fournit [ASP.NET Core Identity](xref:security/authentication/identity) une [ Razor bibliothèque de classes](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="f2899-240">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="f2899-241">La Identity Razor bibliothèque de classes expose des points de terminaison avec la `Identity` zone.</span><span class="sxs-lookup"><span data-stu-id="f2899-241">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="f2899-242">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="f2899-242">For example:</span></span>

* <span data-ttu-id="f2899-243">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="f2899-243">/Identity/Account/Login</span></span>
* <span data-ttu-id="f2899-244">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="f2899-244">/Identity/Account/Logout</span></span>
* <span data-ttu-id="f2899-245">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="f2899-245">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="f2899-246">Appliquer des migrations</span><span class="sxs-lookup"><span data-stu-id="f2899-246">Apply migrations</span></span>

<span data-ttu-id="f2899-247">Appliquez les migrations pour initialiser la base de données.</span><span class="sxs-lookup"><span data-stu-id="f2899-247">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2899-248">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2899-248">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f2899-249">Exécutez la commande suivante dans la console du gestionnaire de package (PMC) :</span><span class="sxs-lookup"><span data-stu-id="f2899-249">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="f2899-250">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="f2899-250">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="f2899-251">Registre de test et connexion</span><span class="sxs-lookup"><span data-stu-id="f2899-251">Test Register and Login</span></span>

<span data-ttu-id="f2899-252">Exécutez l’application et inscrivez un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="f2899-252">Run the app and register a user.</span></span> <span data-ttu-id="f2899-253">Selon la taille de votre écran, vous devrez peut-être sélectionner le bouton bascule de navigation pour afficher les liens de **connexion** et de **Registre** .</span><span class="sxs-lookup"><span data-stu-id="f2899-253">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a><span data-ttu-id="f2899-254">Configurer les Identity services</span><span class="sxs-lookup"><span data-stu-id="f2899-254">Configure Identity services</span></span>

<span data-ttu-id="f2899-255">Les services sont ajoutés dans `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f2899-255">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="f2899-256">Le modèle par défaut consiste à appeler toutes les méthodes `Add{Service}`, puis toutes les méthodes `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="f2899-256">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

<span data-ttu-id="f2899-257">Le code précédent configure Identity avec les valeurs d’option par défaut.</span><span class="sxs-lookup"><span data-stu-id="f2899-257">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="f2899-258">Les services sont mis à la disposition de l’application via l' [injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="f2899-258">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="f2899-259">Identity est activé en appelant [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="f2899-259">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="f2899-260">`UseAuthentication` Ajoute l' [intergiciel (middleware](xref:fundamentals/middleware/index) ) d’authentification au pipeline de requête.</span><span class="sxs-lookup"><span data-stu-id="f2899-260">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="f2899-261">Pour plus d’informations, consultez la [ Identity classe options](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) et le démarrage de l' [application](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="f2899-261">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="f2899-262">Registre de génération de modèles, connexion et déconnexion</span><span class="sxs-lookup"><span data-stu-id="f2899-262">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="f2899-263">Suivez l' [identité de l’échafaudage dans un Razor projet avec des instructions d’autorisation](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) pour générer le code présenté dans cette section.</span><span class="sxs-lookup"><span data-stu-id="f2899-263">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2899-264">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2899-264">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f2899-265">Ajoutez les fichiers Register, login et LogOut.</span><span class="sxs-lookup"><span data-stu-id="f2899-265">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f2899-266">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="f2899-266">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="f2899-267">Si vous avez créé le projet avec le nom **application Web 1**, exécutez les commandes suivantes.</span><span class="sxs-lookup"><span data-stu-id="f2899-267">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="f2899-268">Sinon, utilisez l’espace de noms correct pour `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="f2899-268">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="f2899-269">PowerShell utilise un point-virgule comme séparateur de commande.</span><span class="sxs-lookup"><span data-stu-id="f2899-269">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="f2899-270">Quand vous utilisez PowerShell, échappez les points-virgules dans la liste de fichiers ou placez la liste de fichiers entre guillemets doubles, comme le montre l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="f2899-270">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="f2899-271">Examiner le registre</span><span class="sxs-lookup"><span data-stu-id="f2899-271">Examine Register</span></span>

<span data-ttu-id="f2899-272">Quand un utilisateur clique sur le lien **Register** , l' `RegisterModel.OnPostAsync` action est appelée.</span><span class="sxs-lookup"><span data-stu-id="f2899-272">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="f2899-273">L’utilisateur est créé par [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) sur l' `_userManager` objet :</span><span class="sxs-lookup"><span data-stu-id="f2899-273">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="f2899-274">Si l’utilisateur a été créé avec succès, l’utilisateur est connecté par l’appel à `_signInManager.SignInAsync` .</span><span class="sxs-lookup"><span data-stu-id="f2899-274">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="f2899-275">**Remarque :** Consultez [confirmation du compte](xref:security/authentication/accconfirm#prevent-login-at-registration) pour connaître les étapes permettant d’empêcher la connexion immédiate lors de l’inscription.</span><span class="sxs-lookup"><span data-stu-id="f2899-275">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="f2899-276">Se connecter</span><span class="sxs-lookup"><span data-stu-id="f2899-276">Log in</span></span>

<span data-ttu-id="f2899-277">Le formulaire de connexion s’affiche lorsque :</span><span class="sxs-lookup"><span data-stu-id="f2899-277">The Login form is displayed when:</span></span>

* <span data-ttu-id="f2899-278">Le lien **connexion** est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="f2899-278">The **Log in** link is selected.</span></span>
* <span data-ttu-id="f2899-279">Un utilisateur tente d’accéder à une page restreinte à laquelle il n’est pas autorisé à accéder **ou** lorsqu’il n’a pas été authentifié par le système.</span><span class="sxs-lookup"><span data-stu-id="f2899-279">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="f2899-280">Lorsque le formulaire de la page de connexion est envoyé, l' `OnPostAsync` action est appelée.</span><span class="sxs-lookup"><span data-stu-id="f2899-280">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="f2899-281">`PasswordSignInAsync` est appelé sur l' `_signInManager` objet.</span><span class="sxs-lookup"><span data-stu-id="f2899-281">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="f2899-282">Pour plus d’informations sur la façon de prendre des décisions d’autorisation, consultez <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="f2899-282">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="f2899-283">Se déconnecter</span><span class="sxs-lookup"><span data-stu-id="f2899-283">Log out</span></span>

<span data-ttu-id="f2899-284">Le lien de **déconnexion** appelle l' `LogoutModel.OnPost` action.</span><span class="sxs-lookup"><span data-stu-id="f2899-284">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="f2899-285">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) efface les revendications de l’utilisateur stockées dans un cookie .</span><span class="sxs-lookup"><span data-stu-id="f2899-285">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="f2899-286">La publication est spécifiée dans *pages/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="f2899-286">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-no-locidentity"></a><span data-ttu-id="f2899-287">Test Identity</span><span class="sxs-lookup"><span data-stu-id="f2899-287">Test Identity</span></span>

<span data-ttu-id="f2899-288">Les modèles de projet Web par défaut autorisent l’accès anonyme aux pages d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="f2899-288">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="f2899-289">Pour tester Identity , ajoutez [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) à la page confidentialité.</span><span class="sxs-lookup"><span data-stu-id="f2899-289">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="f2899-290">Si vous êtes connecté, déconnectez-vous. Exécutez l’application et sélectionnez le lien **confidentialité** .</span><span class="sxs-lookup"><span data-stu-id="f2899-290">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="f2899-291">Vous êtes redirigé vers la page de connexion.</span><span class="sxs-lookup"><span data-stu-id="f2899-291">You are redirected to the login page.</span></span>

### <a name="explore-no-locidentity"></a><span data-ttu-id="f2899-292">Explorer Identity</span><span class="sxs-lookup"><span data-stu-id="f2899-292">Explore Identity</span></span>

<span data-ttu-id="f2899-293">Pour explorer Identity plus en détail :</span><span class="sxs-lookup"><span data-stu-id="f2899-293">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="f2899-294">Créer une source d’interface utilisateur d’identité complète</span><span class="sxs-lookup"><span data-stu-id="f2899-294">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="f2899-295">Examinez la source de chaque page et parcourez le débogueur.</span><span class="sxs-lookup"><span data-stu-id="f2899-295">Examine the source of each page and step through the debugger.</span></span>

## <a name="no-locidentity-components"></a><span data-ttu-id="f2899-296">Identity -</span><span class="sxs-lookup"><span data-stu-id="f2899-296">Identity Components</span></span>

<span data-ttu-id="f2899-297">Tous les Identity packages NuGet dépendants sont inclus dans le sous- [package Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="f2899-297">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="f2899-298">Le package principal pour Identity est [Microsoft. AspNetCore. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)</span><span class="sxs-lookup"><span data-stu-id="f2899-298">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="f2899-299">Ce package contient l’ensemble principal d’interfaces pour ASP.NET Core Identity et est inclus dans `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="f2899-299">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-no-locaspnet-core-identity"></a><span data-ttu-id="f2899-300">Migration vers ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="f2899-300">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="f2899-301">Pour plus d’informations et pour obtenir des conseils sur la migration de votre Identity magasin existant, consultez [ Identity migrer l’authentification et ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="f2899-301">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="f2899-302">Définition de la force du mot de passe</span><span class="sxs-lookup"><span data-stu-id="f2899-302">Setting password strength</span></span>

<span data-ttu-id="f2899-303">Consultez [configuration](#pw) d’un exemple qui définit la configuration minimale requise pour le mot de passe.</span><span class="sxs-lookup"><span data-stu-id="f2899-303">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="f2899-304">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="f2899-304">Next Steps</span></span>

* <span data-ttu-id="f2899-305">Pour plus d’informations sur la configuration de à l’aide de SQLite, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) Identity .</span><span class="sxs-lookup"><span data-stu-id="f2899-305">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="f2899-306">Configuration de Identity</span><span class="sxs-lookup"><span data-stu-id="f2899-306">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
