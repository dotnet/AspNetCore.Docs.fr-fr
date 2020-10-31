---
title: 'Génération :::no-loc(Identity)::: de modèles automatique dans les projets ASP.net Core'
author: rick-anderson
description: 'Découvrez comment générer :::no-loc(Identity)::: une structure dans un projet ASP.net core.'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
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
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 813dd7837c265c78c584d66dd51bc23399d12fbe
ms.sourcegitcommit: 5156eab2118584405eb663e1fcd82f8bd7764504
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/31/2020
ms.locfileid: "93141493"
---
# <a name="scaffold-no-locidentity-in-aspnet-core-projects"></a><span data-ttu-id="03cf6-103">Génération :::no-loc(Identity)::: de modèles automatique dans les projets ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="03cf6-103">Scaffold :::no-loc(Identity)::: in ASP.NET Core projects</span></span>

<span data-ttu-id="03cf6-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="03cf6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="03cf6-105">ASP.NET Core fournit [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) comme [ :::no-loc(Razor)::: bibliothèque de classes](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="03cf6-105">ASP.NET Core provides [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) as a [:::no-loc(Razor)::: Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="03cf6-106">Les applications qui incluent :::no-loc(Identity)::: peuvent appliquer l’échafaudage pour ajouter de manière sélective le code source contenu dans la :::no-loc(Identity)::: :::no-loc(Razor)::: bibliothèque de classes (RCL).</span><span class="sxs-lookup"><span data-stu-id="03cf6-106">Applications that include :::no-loc(Identity)::: can apply the scaffolder to selectively add the source code contained in the :::no-loc(Identity)::: :::no-loc(Razor)::: Class Library (RCL).</span></span> <span data-ttu-id="03cf6-107">Vous pouvez souhaiter générer le code source afin de pouvoir modifier le code et changer le comportement.</span><span class="sxs-lookup"><span data-stu-id="03cf6-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="03cf6-108">Par exemple, vous pouvez demander au générateur de modèles automatique de générer le code utilisé dans l’inscription.</span><span class="sxs-lookup"><span data-stu-id="03cf6-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="03cf6-109">Le code généré est prioritaire sur le même code dans le :::no-loc(Identity)::: RCL.</span><span class="sxs-lookup"><span data-stu-id="03cf6-109">Generated code takes precedence over the same code in the :::no-loc(Identity)::: RCL.</span></span> <span data-ttu-id="03cf6-110">Pour obtenir le contrôle total de l’interface utilisateur et ne pas utiliser le RCL par défaut, consultez la section [créer une :::no-loc(Identity)::: source d’interface utilisateur complète](#full).</span><span class="sxs-lookup"><span data-stu-id="03cf6-110">To gain full control of the UI and not use the default RCL, see the section [Create full :::no-loc(Identity)::: UI source](#full).</span></span>

<span data-ttu-id="03cf6-111">Les applications qui n’incluent **pas** l’authentification peuvent appliquer l’échafaudage pour ajouter le :::no-loc(Identity)::: package RCL.</span><span class="sxs-lookup"><span data-stu-id="03cf6-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL :::no-loc(Identity)::: package.</span></span> <span data-ttu-id="03cf6-112">Vous avez la possibilité de sélectionner du :::no-loc(Identity)::: code à générer.</span><span class="sxs-lookup"><span data-stu-id="03cf6-112">You have the option of selecting :::no-loc(Identity)::: code to be generated.</span></span>

<span data-ttu-id="03cf6-113">Bien que le générateur de modèles génère la majeure partie du code nécessaire, vous devez mettre à jour votre projet pour terminer le processus.</span><span class="sxs-lookup"><span data-stu-id="03cf6-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="03cf6-114">Ce document explique les étapes nécessaires à la réalisation d’une :::no-loc(Identity)::: mise à jour de génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="03cf6-114">This document explains the steps needed to complete an :::no-loc(Identity)::: scaffolding update.</span></span>

<span data-ttu-id="03cf6-115">Nous vous recommandons d’utiliser un système de contrôle de code source qui affiche les différences entre les fichiers et vous permet d’annuler les modifications.</span><span class="sxs-lookup"><span data-stu-id="03cf6-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="03cf6-116">Inspectez les modifications après avoir exécuté le générateur de :::no-loc(Identity)::: modèles.</span><span class="sxs-lookup"><span data-stu-id="03cf6-116">Inspect the changes after running the :::no-loc(Identity)::: scaffolder.</span></span>

<span data-ttu-id="03cf6-117">Les services sont requis lorsque vous utilisez l' [authentification à deux facteurs](xref:security/authentication/identity-enable-qrcodes), la [confirmation de compte et la récupération de mot de passe](xref:security/authentication/accconfirm), ainsi que d’autres fonctionnalités de sécurité avec :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="03cf6-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with :::no-loc(Identity):::.</span></span> <span data-ttu-id="03cf6-118">Les services ou stubs de service ne sont pas générés lors de la génération de modèles automatique :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="03cf6-118">Services or service stubs aren't generated when scaffolding :::no-loc(Identity):::.</span></span> <span data-ttu-id="03cf6-119">Les services pour activer ces fonctionnalités doivent être ajoutés manuellement.</span><span class="sxs-lookup"><span data-stu-id="03cf6-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="03cf6-120">Par exemple, consultez [exiger une confirmation par courrier électronique](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="03cf6-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="03cf6-121">Lors de la génération de modèles automatique :::no-loc(Identity)::: avec un nouveau contexte de données dans un projet avec des comptes individuels existants :</span><span class="sxs-lookup"><span data-stu-id="03cf6-121">When scaffolding :::no-loc(Identity)::: with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="03cf6-122">Dans `Startup.ConfigureServices` , supprimez les appels à :</span><span class="sxs-lookup"><span data-stu-id="03cf6-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefault:::no-loc(Identity):::`

<span data-ttu-id="03cf6-123">Par exemple, `AddDbContext` et `AddDefault:::no-loc(Identity):::` sont commentés dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="03cf6-123">For example, `AddDbContext` and `AddDefault:::no-loc(Identity):::` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="03cf6-124">Le code précédent fait un commentaire sur le code qui est dupliqué dans *Areas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs*</span><span class="sxs-lookup"><span data-stu-id="03cf6-124">The preceeding code comments out the code that is duplicated in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs*</span></span>

<span data-ttu-id="03cf6-125">En règle générale, les applications créées avec des comptes individuels ne doivent **pas** créer un nouveau contexte de données.</span><span class="sxs-lookup"><span data-stu-id="03cf6-125">Typically, apps that were created with individual accounts should \* **not** _ create a new data context.</span></span>

## <a name="scaffold-no-locidentity-into-an-empty-project"></a><span data-ttu-id="03cf6-126">Génération de modèles automatique :::no-loc(Identity)::: dans un projet vide</span><span class="sxs-lookup"><span data-stu-id="03cf6-126">Scaffold :::no-loc(Identity)::: into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="03cf6-127">Mettez à jour la `Startup` classe avec un code similaire à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="03cf6-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a><span data-ttu-id="03cf6-128">Générer :::no-loc(Identity)::: une structure dans un :::no-loc(Razor)::: projet sans autorisation existante</span><span class="sxs-lookup"><span data-stu-id="03cf6-128">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.:::no-loc(Identity):::.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef database drop
dotnet ef migrations add Create:::no-loc(Identity):::Schema0
dotnet ef database update
-->

<!-- ERROR
There is already an object named 'AspNetRoles' in the database.

Fixed via dotnet ef database drop
before dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="03cf6-129">:::no-loc(Identity):::est configuré dans _Areas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs \*.</span><span class="sxs-lookup"><span data-stu-id="03cf6-129">:::no-loc(Identity)::: is configured in _Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs\*.</span></span> <span data-ttu-id="03cf6-130">Pour plus d’informations, consultez [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="03cf6-130">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="03cf6-131">Migrations, UseAuthentication et disposition</span><span class="sxs-lookup"><span data-stu-id="03cf6-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="03cf6-132">Activer l’authentification</span><span class="sxs-lookup"><span data-stu-id="03cf6-132">Enable authentication</span></span>

<span data-ttu-id="03cf6-133">Mettez à jour la `Startup` classe avec un code similaire à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="03cf6-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="03cf6-134">Modifications de la disposition</span><span class="sxs-lookup"><span data-stu-id="03cf6-134">Layout changes</span></span>

<span data-ttu-id="03cf6-135">Facultatif : ajoutez la connexion partielle ( `_LoginPartial` ) au fichier de disposition :</span><span class="sxs-lookup"><span data-stu-id="03cf6-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a><span data-ttu-id="03cf6-136">Génération de modèles automatique :::no-loc(Identity)::: dans un :::no-loc(Razor)::: projet avec autorisation</span><span class="sxs-lookup"><span data-stu-id="03cf6-136">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project with authorization</span></span>

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="03cf6-137">Certaines :::no-loc(Identity)::: options sont configurées dans *Areas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* .</span><span class="sxs-lookup"><span data-stu-id="03cf6-137">Some :::no-loc(Identity)::: options are configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="03cf6-138">Pour plus d’informations, consultez [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="03cf6-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="03cf6-139">Génération de modèles automatique :::no-loc(Identity)::: dans un projet MVC sans autorisation existante</span><span class="sxs-lookup"><span data-stu-id="03cf6-139">Scaffold :::no-loc(Identity)::: into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add Create:::no-loc(Identity):::Schema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="03cf6-140">Facultatif : ajoutez la connexion partielle ( `_LoginPartial` ) au fichier *Views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="03cf6-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="03cf6-141">Déplacez le fichier *pages/Shared/_LoginPartial. cshtml* vers *views/shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="03cf6-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="03cf6-142">:::no-loc(Identity):::est configuré dans *Areas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* .</span><span class="sxs-lookup"><span data-stu-id="03cf6-142">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="03cf6-143">Pour plus d’informations, consultez IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="03cf6-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="03cf6-144">Mettez à jour la `Startup` classe avec un code similaire à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="03cf6-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="03cf6-145">Génération :::no-loc(Identity)::: de modèles automatique dans un projet MVC avec autorisation</span><span class="sxs-lookup"><span data-stu-id="03cf6-145">Scaffold :::no-loc(Identity)::: into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-without-existing-authorization"></a><span data-ttu-id="03cf6-146">Générer :::no-loc(Identity)::: une structure dans un :::no-loc(Blazor Server)::: projet sans autorisation existante</span><span class="sxs-lookup"><span data-stu-id="03cf6-146">Scaffold :::no-loc(Identity)::: into a :::no-loc(Blazor Server)::: project without existing authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="03cf6-147">:::no-loc(Identity):::est configuré dans *Areas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* .</span><span class="sxs-lookup"><span data-stu-id="03cf6-147">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="03cf6-148">Pour plus d’informations, consultez [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="03cf6-148">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

### <a name="migrations"></a><span data-ttu-id="03cf6-149">Migrations</span><span class="sxs-lookup"><span data-stu-id="03cf6-149">Migrations</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a><span data-ttu-id="03cf6-150">Passer un jeton XSRF à l’application</span><span class="sxs-lookup"><span data-stu-id="03cf6-150">Pass an XSRF token to the app</span></span>

<span data-ttu-id="03cf6-151">Les jetons peuvent être passés à des composants :</span><span class="sxs-lookup"><span data-stu-id="03cf6-151">Tokens can be passed to components:</span></span>

* <span data-ttu-id="03cf6-152">Lorsque les jetons d’authentification sont approvisionnés et enregistrés dans l’authentification :::no-loc(cookie)::: , ils peuvent être passés à des composants.</span><span class="sxs-lookup"><span data-stu-id="03cf6-152">When authentication tokens are provisioned and saved to the authentication :::no-loc(cookie):::, they can be passed to components.</span></span>
* <span data-ttu-id="03cf6-153">:::no-loc(Razor)::: les composants ne peuvent pas être utilisés `HttpContext` directement. il n’existe donc aucun moyen d’obtenir un [jeton XSRF (contrefaçon anti-request)](xref:security/anti-request-forgery) pour effectuer une publication sur le :::no-loc(Identity)::: point de terminaison de déconnexion de `/:::no-loc(Identity):::/Account/Logout` .</span><span class="sxs-lookup"><span data-stu-id="03cf6-153">:::no-loc(Razor)::: components can't use `HttpContext` directly, so there's no way to obtain an [anti-request forgery (XSRF) token](xref:security/anti-request-forgery) to POST to :::no-loc(Identity):::'s logout endpoint at `/:::no-loc(Identity):::/Account/Logout`.</span></span> <span data-ttu-id="03cf6-154">Un jeton XSRF peut être passé à des composants.</span><span class="sxs-lookup"><span data-stu-id="03cf6-154">An XSRF token can be passed to components.</span></span>

<span data-ttu-id="03cf6-155">Pour plus d'informations, consultez <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span><span class="sxs-lookup"><span data-stu-id="03cf6-155">For more information, see <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>

<span data-ttu-id="03cf6-156">Dans le fichier *pages/_Host. cshtml* , établissez le jeton après l’avoir ajouté `InitialApplicationState` aux `TokenProvider` classes et :</span><span class="sxs-lookup"><span data-stu-id="03cf6-156">In the *Pages/_Host.cshtml* file, establish the token after adding it to the `InitialApplicationState` and `TokenProvider` classes:</span></span>

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

<span data-ttu-id="03cf6-157">Mettez à jour le `App` composant ( *app. Razor* ) pour affecter le `InitialState.XsrfToken` :</span><span class="sxs-lookup"><span data-stu-id="03cf6-157">Update the `App` component ( *App.razor* ) to assign the `InitialState.XsrfToken`:</span></span>

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

<span data-ttu-id="03cf6-158">Le `TokenProvider` service présenté dans la rubrique est utilisé dans le `LoginDisplay` composant dans la section [mise en page et changements de workflow d’authentification](#layout-and-authentication-flow-changes) suivants.</span><span class="sxs-lookup"><span data-stu-id="03cf6-158">The `TokenProvider` service demonstrated in the topic is used in the `LoginDisplay` component in the following [Layout and authentication flow changes](#layout-and-authentication-flow-changes) section.</span></span>

### <a name="enable-authentication"></a><span data-ttu-id="03cf6-159">Activer l’authentification</span><span class="sxs-lookup"><span data-stu-id="03cf6-159">Enable authentication</span></span>

<span data-ttu-id="03cf6-160">Dans la `Startup` classe :</span><span class="sxs-lookup"><span data-stu-id="03cf6-160">In the `Startup` class:</span></span>

* <span data-ttu-id="03cf6-161">Confirmez que :::no-loc(Razor)::: les services de pages sont ajoutés dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="03cf6-161">Confirm that :::no-loc(Razor)::: Pages services are added in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="03cf6-162">Si vous utilisez [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), inscrivez le service.</span><span class="sxs-lookup"><span data-stu-id="03cf6-162">If using the [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), register the service.</span></span>
* <span data-ttu-id="03cf6-163">Appelez `UseDatabaseErrorPage` sur le générateur d’applications dans `Startup.Configure` pour l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="03cf6-163">Call `UseDatabaseErrorPage` on the application builder in `Startup.Configure` for the Development environment.</span></span>
* <span data-ttu-id="03cf6-164">Appelez `UseAuthentication` et `UseAuthorization` after `UseRouting` .</span><span class="sxs-lookup"><span data-stu-id="03cf6-164">Call `UseAuthentication` and `UseAuthorization` after `UseRouting`.</span></span>
* <span data-ttu-id="03cf6-165">Ajoutez un point de terminaison pour les :::no-loc(Razor)::: pages.</span><span class="sxs-lookup"><span data-stu-id="03cf6-165">Add an endpoint for :::no-loc(Razor)::: Pages.</span></span>

[!code-csharp[](scaffold-identity/3.1sample/Startup:::no-loc(Blazor):::.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a><span data-ttu-id="03cf6-166">Modifications de la disposition et du workflow d’authentification</span><span class="sxs-lookup"><span data-stu-id="03cf6-166">Layout and authentication flow changes</span></span>

<span data-ttu-id="03cf6-167">Ajoutez un `RedirectToLogin` composant ( *RedirectToLogin. Razor* ) au dossier *partagé* de l’application dans la racine du projet :</span><span class="sxs-lookup"><span data-stu-id="03cf6-167">Add a `RedirectToLogin` component ( *RedirectToLogin.razor* ) to the app's *Shared* folder in the project root:</span></span>

```razor
@inject NavigationManager Navigation
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo(":::no-loc(Identity):::/Account/Login?returnUrl=" +
            Uri.EscapeDataString(Navigation.Uri), true);
    }
}
```

Ajoutez un `LoginDisplay` composant ( *LoginDisplay. Razor* ) au dossier *partagé* de l’application. <span data-ttu-id="03cf6-169">Le [service TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) fournit le jeton XSRF pour le formulaire HTML qui publie sur le :::no-loc(Identity)::: point de terminaison logout de déconnexion :</span><span class="sxs-lookup"><span data-stu-id="03cf6-169">The [TokenProvider service](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) provides the XSRF token for the HTML form that POSTs to :::no-loc(Identity):::'s logout endpoint:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@inject NavigationManager Navigation
@inject TokenProvider TokenProvider

<AuthorizeView>
    <Authorized>
        <a href=":::no-loc(Identity):::/Account/Manage/Index">
            Hello, @context.User.:::no-loc(Identity):::.Name!
        </a>
        <form action="/:::no-loc(Identity):::/Account/Logout?returnUrl=%2F" method="post">
            <button class="nav-link btn btn-link" type="submit">Logout</button>
            <input name="__RequestVerificationToken" type="hidden" 
                value="@TokenProvider.XsrfToken">
        </form>
    </Authorized>
    <NotAuthorized>
        <a href=":::no-loc(Identity):::/Account/Register">Register</a>
        <a href=":::no-loc(Identity):::/Account/Login">Login</a>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="03cf6-170">Dans le `MainLayout` composant ( *Shared/MainLayout. Razor* ), ajoutez le `LoginDisplay` composant au contenu de l’élément de ligne de plus haut niveau `<div>` :</span><span class="sxs-lookup"><span data-stu-id="03cf6-170">In the `MainLayout` component ( *Shared/MainLayout.razor* ), add the `LoginDisplay` component to the top-row `<div>` element's content:</span></span>

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a><span data-ttu-id="03cf6-171">Points de terminaison d’authentification de style</span><span class="sxs-lookup"><span data-stu-id="03cf6-171">Style authentication endpoints</span></span>

<span data-ttu-id="03cf6-172">Étant donné que :::no-loc(Blazor Server)::: utilise :::no-loc(Razor)::: :::no-loc(Identity)::: les pages pages, le style de l’interface utilisateur change lorsqu’un visiteur navigue entre :::no-loc(Identity)::: les pages et les composants.</span><span class="sxs-lookup"><span data-stu-id="03cf6-172">Because :::no-loc(Blazor Server)::: uses :::no-loc(Razor)::: Pages :::no-loc(Identity)::: pages, the styling of the UI changes when a visitor navigates between :::no-loc(Identity)::: pages and components.</span></span> <span data-ttu-id="03cf6-173">Vous avez deux options pour traiter les styles Incongruous :</span><span class="sxs-lookup"><span data-stu-id="03cf6-173">You have two options to address the incongruous styles:</span></span>

#### <a name="build-no-locidentity-components"></a><span data-ttu-id="03cf6-174">Composants de build :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="03cf6-174">Build :::no-loc(Identity)::: components</span></span>

<span data-ttu-id="03cf6-175">Une approche de l’utilisation de composants :::no-loc(Identity)::: au lieu de pages consiste à créer des :::no-loc(Identity)::: composants.</span><span class="sxs-lookup"><span data-stu-id="03cf6-175">An approach to using components for :::no-loc(Identity)::: instead of pages is to build :::no-loc(Identity)::: components.</span></span> <span data-ttu-id="03cf6-176">Étant donné que `SignInManager` et `UserManager` ne sont pas pris en charge dans :::no-loc(Razor)::: les composants, utilisez les points de terminaison d’API dans l' :::no-loc(Blazor Server)::: application pour traiter les actions de compte d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="03cf6-176">Because `SignInManager` and `UserManager` aren't supported in :::no-loc(Razor)::: components, use API endpoints in the :::no-loc(Blazor Server)::: app to process user account actions.</span></span>

#### <a name="use-a-custom-layout-with-no-locblazor-app-styles"></a><span data-ttu-id="03cf6-177">Utiliser une disposition personnalisée avec les :::no-loc(Blazor)::: styles d’application</span><span class="sxs-lookup"><span data-stu-id="03cf6-177">Use a custom layout with :::no-loc(Blazor)::: app styles</span></span>

<span data-ttu-id="03cf6-178">La :::no-loc(Identity)::: disposition et les styles des pages peuvent être modifiés pour produire des pages qui utilisent le thème par défaut :::no-loc(Blazor)::: .</span><span class="sxs-lookup"><span data-stu-id="03cf6-178">The :::no-loc(Identity)::: pages layout and styles can be modified to produce pages that use the default :::no-loc(Blazor)::: theme.</span></span>

> [!NOTE]
> <span data-ttu-id="03cf6-179">L’exemple de cette section est simplement un point de départ pour la personnalisation.</span><span class="sxs-lookup"><span data-stu-id="03cf6-179">The example in this section is merely a starting point for customization.</span></span> <span data-ttu-id="03cf6-180">Un travail supplémentaire est probablement requis pour une expérience utilisateur optimale.</span><span class="sxs-lookup"><span data-stu-id="03cf6-180">Additional work is likely required for the best user experience.</span></span>

<span data-ttu-id="03cf6-181">Créez un nouveau `NavMenu_:::no-loc(Identity):::Layout` composant ( *Shared/NavMenu_ :::no-loc(Identity)::: Layout. Razor* ).</span><span class="sxs-lookup"><span data-stu-id="03cf6-181">Create a new `NavMenu_:::no-loc(Identity):::Layout` component ( *Shared/NavMenu_:::no-loc(Identity):::Layout.razor* ).</span></span> <span data-ttu-id="03cf6-182">Pour le balisage et le code du composant, utilisez le même contenu du composant de l’application `NavMenu` ( *Shared/NavMenu. Razor* ).</span><span class="sxs-lookup"><span data-stu-id="03cf6-182">For the markup and code of the component, use the same content of the app's `NavMenu` component ( *Shared/NavMenu.razor* ).</span></span> <span data-ttu-id="03cf6-183">Supprimez les `NavLink` composants de tous les composants qui ne sont pas accessibles de façon anonyme, car les redirections automatiques du `RedirectToLogin` composant échouent pour les composants nécessitant une authentification ou une autorisation.</span><span class="sxs-lookup"><span data-stu-id="03cf6-183">Strip out any `NavLink`s to components that can't be reached anonymously because automatic redirects in the `RedirectToLogin` component fail for components requiring authentication or authorization.</span></span>

<span data-ttu-id="03cf6-184">Dans le fichier *pages/Shared/Layout. cshtml* , apportez les modifications suivantes :</span><span class="sxs-lookup"><span data-stu-id="03cf6-184">In the *Pages/Shared/Layout.cshtml* file, make the following changes:</span></span>

* <span data-ttu-id="03cf6-185">Ajoutez :::no-loc(Razor)::: des directives en haut du fichier pour utiliser tag Helper et les composants de l’application dans le dossier *partagé* :</span><span class="sxs-lookup"><span data-stu-id="03cf6-185">Add :::no-loc(Razor)::: directives to the top of the file to use Tag Helpers and the app's components in the *Shared* folder:</span></span>

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  <span data-ttu-id="03cf6-186">Remplacez `{APPLICATION ASSEMBLY}` par le nom de l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="03cf6-186">Replace `{APPLICATION ASSEMBLY}` with the app's assembly name.</span></span>

* <span data-ttu-id="03cf6-187">Ajoutez une `<base>` balise et une :::no-loc(Blazor)::: feuille `<link>` de style au `<head>` contenu :</span><span class="sxs-lookup"><span data-stu-id="03cf6-187">Add a `<base>` tag and :::no-loc(Blazor)::: stylesheet `<link>` to the `<head>` content:</span></span>

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* <span data-ttu-id="03cf6-188">Remplacez le contenu de la `<body>` balise par ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="03cf6-188">Change the content of the `<body>` tag to the following:</span></span>

  ```cshtml
  <div class="sidebar" style="float:left">
      <component type="typeof(NavMenu_:::no-loc(Identity):::Layout)" 
          render-mode="ServerPrerendered" />
  </div>

  <div class="main" style="padding-left:250px">
      <div class="top-row px-4">
          @{
              var result = Engine.FindView(ViewContext, "_LoginPartial", 
                  isMainPage: false);
          }
          @if (result.Success)
          {
              await Html.RenderPartialAsync("_LoginPartial");
          }
          else
          {
              throw new InvalidOperationException("The default :::no-loc(Identity)::: UI " +
                  "layout requires a partial view '_LoginPartial'.");
          }
          <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
      </div>

      <div class="content px-4">
          @RenderBody()
      </div>
  </div>

  <script src="~/:::no-loc(Identity):::/lib/jquery/dist/jquery.min.js"></script>
  <script src="~/:::no-loc(Identity):::/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
  <script src="~/:::no-loc(Identity):::/js/site.js" asp-append-version="true"></script>
  @RenderSection("Scripts", required: false)
  <script src="_framework/blazor.server.js"></script>
  ```

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-with-authorization"></a><span data-ttu-id="03cf6-189">Génération de modèles automatique :::no-loc(Identity)::: dans un :::no-loc(Blazor Server)::: projet avec autorisation</span><span class="sxs-lookup"><span data-stu-id="03cf6-189">Scaffold :::no-loc(Identity)::: into a :::no-loc(Blazor Server)::: project with authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="03cf6-190">Certaines :::no-loc(Identity)::: options sont configurées dans *Areas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* .</span><span class="sxs-lookup"><span data-stu-id="03cf6-190">Some :::no-loc(Identity)::: options are configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="03cf6-191">Pour plus d’informations, consultez [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="03cf6-191">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="standalone-or-hosted-no-locblazor-webassembly-apps"></a><span data-ttu-id="03cf6-192">Applications autonomes ou hébergées :::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="03cf6-192">Standalone or hosted :::no-loc(Blazor WebAssembly)::: apps</span></span>

<span data-ttu-id="03cf6-193">:::no-loc(Blazor WebAssembly):::Les applications côté client utilisent leurs propres :::no-loc(Identity)::: approches de l’interface utilisateur et ne peuvent pas utiliser la :::no-loc(ASP.NET Core Identity)::: génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="03cf6-193">Client-side :::no-loc(Blazor WebAssembly)::: apps use their own :::no-loc(Identity)::: UI approaches and can't use :::no-loc(ASP.NET Core Identity)::: scaffolding.</span></span> <span data-ttu-id="03cf6-194">Les applications ASP.NET Core côté serveur des solutions hébergées :::no-loc(Blazor)::: peuvent suivre les :::no-loc(Razor)::: instructions des pages/MVC de cet article et sont configurées comme n’importe quel autre type d’application ASP.net core qui prend en charge :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="03cf6-194">Server-side ASP.NET Core apps of hosted :::no-loc(Blazor)::: solutions can follow the :::no-loc(Razor)::: Pages/MVC guidance in this article and are configured just like any other type of ASP.NET Core app that supports :::no-loc(Identity):::.</span></span>

<span data-ttu-id="03cf6-195">Le :::no-loc(Blazor)::: Framework n’inclut pas les :::no-loc(Razor)::: versions de composants des :::no-loc(Identity)::: pages d’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="03cf6-195">The :::no-loc(Blazor)::: framework doesn't include :::no-loc(Razor)::: component versions of :::no-loc(Identity)::: UI pages.</span></span> <span data-ttu-id="03cf6-196">:::no-loc(Identity)::: Les :::no-loc(Razor)::: composants d’interface utilisateur peuvent être générés de façon personnalisée ou obtenus à partir de sources tierces non prises en charge.</span><span class="sxs-lookup"><span data-stu-id="03cf6-196">:::no-loc(Identity)::: UI :::no-loc(Razor)::: components can be custom built or obtained from unsupported third-party sources.</span></span>

<span data-ttu-id="03cf6-197">Pour plus d’informations, consultez la section [ :::no-loc(Blazor)::: sécurité et :::no-loc(Identity)::: Articles](xref:blazor/security/index).</span><span class="sxs-lookup"><span data-stu-id="03cf6-197">For more information, see the [:::no-loc(Blazor)::: Security and :::no-loc(Identity)::: articles](xref:blazor/security/index).</span></span>

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a><span data-ttu-id="03cf6-198">Créer une :::no-loc(Identity)::: source d’interface utilisateur complète</span><span class="sxs-lookup"><span data-stu-id="03cf6-198">Create full :::no-loc(Identity)::: UI source</span></span>

<span data-ttu-id="03cf6-199">Pour conserver le contrôle total de l' :::no-loc(Identity)::: interface utilisateur, exécutez le générateur de :::no-loc(Identity)::: modèles automatique et sélectionnez **remplacer tous les fichiers** .</span><span class="sxs-lookup"><span data-stu-id="03cf6-199">To maintain full control of the :::no-loc(Identity)::: UI, run the :::no-loc(Identity)::: scaffolder and select **Override all files** .</span></span>

<span data-ttu-id="03cf6-200">Le code en surbrillance suivant montre les modifications pour remplacer l’interface utilisateur par défaut par :::no-loc(Identity)::: :::no-loc(Identity)::: dans une application web ASP.net Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="03cf6-200">The following highlighted code shows the changes to replace the default :::no-loc(Identity)::: UI with :::no-loc(Identity)::: in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="03cf6-201">Vous pouvez le faire pour avoir le contrôle total de l' :::no-loc(Identity)::: interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="03cf6-201">You might want to do this to have full control of the :::no-loc(Identity)::: UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="03cf6-202">La valeur par défaut :::no-loc(Identity)::: est remplacée dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="03cf6-202">The default :::no-loc(Identity)::: is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="03cf6-203">Le code suivant définit les [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath)et [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="03cf6-203">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="03cf6-204">Inscrire une `IEmailSender` implémentation, par exemple :</span><span class="sxs-lookup"><span data-stu-id="03cf6-204">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="03cf6-205">Configuration du mot de passe</span><span class="sxs-lookup"><span data-stu-id="03cf6-205">Password configuration</span></span>

<span data-ttu-id="03cf6-206">Si <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> est configuré dans `Startup.ConfigureServices` , la configuration de l' [ `[StringLength]` attribut](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) peut être requise pour la `Password` propriété dans les pages de génération de modèles automatique :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="03cf6-206">If <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded :::no-loc(Identity)::: pages.</span></span> <span data-ttu-id="03cf6-207">`InputModel``Password`les propriétés se trouvent dans les fichiers suivants :</span><span class="sxs-lookup"><span data-stu-id="03cf6-207">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs`
* `Areas/:::no-loc(Identity):::/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-a-page"></a><span data-ttu-id="03cf6-208">Désactiver une page</span><span class="sxs-lookup"><span data-stu-id="03cf6-208">Disable a page</span></span>

<span data-ttu-id="03cf6-209">Cette section montre comment désactiver la page de Registre, mais l’approche peut être utilisée pour désactiver n’importe quelle page.</span><span class="sxs-lookup"><span data-stu-id="03cf6-209">This sections show how to disable the register page but the approach can be used to disable any page.</span></span>

<span data-ttu-id="03cf6-210">Pour désactiver l’inscription des utilisateurs :</span><span class="sxs-lookup"><span data-stu-id="03cf6-210">To disable user registration:</span></span>

* <span data-ttu-id="03cf6-211">Génération de modèles automatique :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="03cf6-211">Scaffold :::no-loc(Identity):::.</span></span> <span data-ttu-id="03cf6-212">Incluez Account. Register, Account. Login et Account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="03cf6-212">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="03cf6-213">Exemple :</span><span class="sxs-lookup"><span data-stu-id="03cf6-213">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="03cf6-214">Mettre à jour les *zones/ :::no-loc(Identity)::: /pages/Account/Register.cshtml.cs* afin que les utilisateurs ne puissent pas s’inscrire depuis ce point de terminaison :</span><span class="sxs-lookup"><span data-stu-id="03cf6-214">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="03cf6-215">Mettez à jour les *zones/ :::no-loc(Identity)::: /pages/Account/Register.cshtml* pour qu’ils soient cohérents avec les modifications précédentes :</span><span class="sxs-lookup"><span data-stu-id="03cf6-215">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="03cf6-216">Commentez ou supprimez le lien d’inscription de *Areas/ :::no-loc(Identity)::: /pages/Account/login.cshtml*</span><span class="sxs-lookup"><span data-stu-id="03cf6-216">Comment out or remove the registration link from *Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml*</span></span>

  ```cshtml
  @*
  <p>
      <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
  </p>
  *@
  ```

* <span data-ttu-id="03cf6-217">Mettez à jour la page *zones/ :::no-loc(Identity)::: /pages/Account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="03cf6-217">Update the *Areas/:::no-loc(Identity):::/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="03cf6-218">Supprimez le code et les liens du fichier cshtml.</span><span class="sxs-lookup"><span data-stu-id="03cf6-218">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="03cf6-219">Supprimez le code de confirmation du `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="03cf6-219">Remove the confirmation code from the `PageModel`:</span></span>

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="03cf6-220">Utiliser une autre application pour ajouter des utilisateurs</span><span class="sxs-lookup"><span data-stu-id="03cf6-220">Use another app to add users</span></span>

<span data-ttu-id="03cf6-221">Fournissez un mécanisme pour ajouter des utilisateurs en dehors de l’application Web.</span><span class="sxs-lookup"><span data-stu-id="03cf6-221">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="03cf6-222">Les options permettant d’ajouter des utilisateurs sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="03cf6-222">Options to add users include:</span></span>

* <span data-ttu-id="03cf6-223">Une application Web d’administration dédiée.</span><span class="sxs-lookup"><span data-stu-id="03cf6-223">A dedicated admin web app.</span></span>
* <span data-ttu-id="03cf6-224">Application console.</span><span class="sxs-lookup"><span data-stu-id="03cf6-224">A console app.</span></span>

<span data-ttu-id="03cf6-225">Le code suivant décrit une approche de l’ajout d’utilisateurs :</span><span class="sxs-lookup"><span data-stu-id="03cf6-225">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="03cf6-226">Une liste d’utilisateurs est lue en mémoire.</span><span class="sxs-lookup"><span data-stu-id="03cf6-226">A list of users is read into memory.</span></span>
* <span data-ttu-id="03cf6-227">Un mot de passe fort unique est généré pour chaque utilisateur.</span><span class="sxs-lookup"><span data-stu-id="03cf6-227">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="03cf6-228">L’utilisateur est ajouté à la :::no-loc(Identity)::: base de données.</span><span class="sxs-lookup"><span data-stu-id="03cf6-228">The user is added to the :::no-loc(Identity)::: database.</span></span>
* <span data-ttu-id="03cf6-229">L’utilisateur est averti et a demandé à modifier le mot de passe.</span><span class="sxs-lookup"><span data-stu-id="03cf6-229">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="03cf6-230">L’exemple de code suivant présente l’ajout d’un utilisateur :</span><span class="sxs-lookup"><span data-stu-id="03cf6-230">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="03cf6-231">Une approche similaire peut être suivie pour les scénarios de production.</span><span class="sxs-lookup"><span data-stu-id="03cf6-231">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-no-locidentity-assets"></a><span data-ttu-id="03cf6-232">Empêcher la publication d' :::no-loc(Identity)::: actifs statiques</span><span class="sxs-lookup"><span data-stu-id="03cf6-232">Prevent publish of static :::no-loc(Identity)::: assets</span></span>

<span data-ttu-id="03cf6-233">Pour empêcher la publication :::no-loc(Identity)::: des ressources statiques sur la racine Web, consultez <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> .</span><span class="sxs-lookup"><span data-stu-id="03cf6-233">To prevent publishing static :::no-loc(Identity)::: assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="03cf6-234">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="03cf6-234">Additional resources</span></span>

* [<span data-ttu-id="03cf6-235">Modifications apportées au code d’authentification pour ASP.NET Core 2,1 et versions ultérieures</span><span class="sxs-lookup"><span data-stu-id="03cf6-235">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="03cf6-236">ASP.NET Core 2,1 et versions ultérieures fournissent [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) comme [ :::no-loc(Razor)::: bibliothèque de classes](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="03cf6-236">ASP.NET Core 2.1 and later provides [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) as a [:::no-loc(Razor)::: Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="03cf6-237">Les applications qui incluent :::no-loc(Identity)::: peuvent appliquer l’échafaudage pour ajouter de manière sélective le code source contenu dans la :::no-loc(Identity)::: :::no-loc(Razor)::: bibliothèque de classes (RCL).</span><span class="sxs-lookup"><span data-stu-id="03cf6-237">Applications that include :::no-loc(Identity)::: can apply the scaffolder to selectively add the source code contained in the :::no-loc(Identity)::: :::no-loc(Razor)::: Class Library (RCL).</span></span> <span data-ttu-id="03cf6-238">Vous pouvez souhaiter générer le code source afin de pouvoir modifier le code et changer le comportement.</span><span class="sxs-lookup"><span data-stu-id="03cf6-238">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="03cf6-239">Par exemple, vous pouvez demander au générateur de modèles automatique de générer le code utilisé dans l’inscription.</span><span class="sxs-lookup"><span data-stu-id="03cf6-239">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="03cf6-240">Le code généré est prioritaire sur le même code dans le :::no-loc(Identity)::: RCL.</span><span class="sxs-lookup"><span data-stu-id="03cf6-240">Generated code takes precedence over the same code in the :::no-loc(Identity)::: RCL.</span></span> <span data-ttu-id="03cf6-241">Pour obtenir le contrôle total de l’interface utilisateur et ne pas utiliser le RCL par défaut, consultez la section [créer une source d’interface utilisateur d’identité complète](#full).</span><span class="sxs-lookup"><span data-stu-id="03cf6-241">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="03cf6-242">Les applications qui n’incluent **pas** l’authentification peuvent appliquer l’échafaudage pour ajouter le :::no-loc(Identity)::: package RCL.</span><span class="sxs-lookup"><span data-stu-id="03cf6-242">Applications that do **not** include authentication can apply the scaffolder to add the RCL :::no-loc(Identity)::: package.</span></span> <span data-ttu-id="03cf6-243">Vous avez la possibilité de sélectionner du :::no-loc(Identity)::: code à générer.</span><span class="sxs-lookup"><span data-stu-id="03cf6-243">You have the option of selecting :::no-loc(Identity)::: code to be generated.</span></span>

<span data-ttu-id="03cf6-244">Bien que le générateur de modèles génère la plus grande partie du code nécessaire, vous devez mettre à jour votre projet pour terminer le processus.</span><span class="sxs-lookup"><span data-stu-id="03cf6-244">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="03cf6-245">Ce document explique les étapes nécessaires à la réalisation d’une :::no-loc(Identity)::: mise à jour de génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="03cf6-245">This document explains the steps needed to complete an :::no-loc(Identity)::: scaffolding update.</span></span>

<span data-ttu-id="03cf6-246">Quand vous exécutez le générateur de :::no-loc(Identity)::: modèles, un fichier de *ScaffoldingReadme.txt* est créé dans le répertoire du projet.</span><span class="sxs-lookup"><span data-stu-id="03cf6-246">When the :::no-loc(Identity)::: scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="03cf6-247">Le fichier *ScaffoldingReadme.txt* contient des instructions générales sur ce qui est nécessaire pour effectuer la :::no-loc(Identity)::: mise à jour de la génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="03cf6-247">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the :::no-loc(Identity)::: scaffolding update.</span></span> <span data-ttu-id="03cf6-248">Ce document contient des instructions plus complètes que le fichier *ScaffoldingReadme.txt* .</span><span class="sxs-lookup"><span data-stu-id="03cf6-248">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="03cf6-249">Nous vous recommandons d’utiliser un système de contrôle de code source qui affiche les différences entre les fichiers et vous permet d’annuler les modifications.</span><span class="sxs-lookup"><span data-stu-id="03cf6-249">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="03cf6-250">Inspectez les modifications après avoir exécuté le générateur de :::no-loc(Identity)::: modèles.</span><span class="sxs-lookup"><span data-stu-id="03cf6-250">Inspect the changes after running the :::no-loc(Identity)::: scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="03cf6-251">Les services sont requis lorsque vous utilisez l' [authentification à deux facteurs](xref:security/authentication/identity-enable-qrcodes), la [confirmation de compte et la récupération de mot de passe](xref:security/authentication/accconfirm), ainsi que d’autres fonctionnalités de sécurité avec :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="03cf6-251">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with :::no-loc(Identity):::.</span></span> <span data-ttu-id="03cf6-252">Les services ou stubs de service ne sont pas générés lors de la génération de modèles automatique :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="03cf6-252">Services or service stubs aren't generated when scaffolding :::no-loc(Identity):::.</span></span> <span data-ttu-id="03cf6-253">Les services pour activer ces fonctionnalités doivent être ajoutés manuellement.</span><span class="sxs-lookup"><span data-stu-id="03cf6-253">Services to enable these features must be added manually.</span></span> <span data-ttu-id="03cf6-254">Par exemple, consultez [exiger une confirmation par courrier électronique](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="03cf6-254">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-no-locidentity-into-an-empty-project"></a><span data-ttu-id="03cf6-255">Génération de modèles automatique :::no-loc(Identity)::: dans un projet vide</span><span class="sxs-lookup"><span data-stu-id="03cf6-255">Scaffold :::no-loc(Identity)::: into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="03cf6-256">Ajoutez les appels en surbrillance suivants à la `Startup` classe :</span><span class="sxs-lookup"><span data-stu-id="03cf6-256">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a><span data-ttu-id="03cf6-257">Générer :::no-loc(Identity)::: une structure dans un :::no-loc(Razor)::: projet sans autorisation existante</span><span class="sxs-lookup"><span data-stu-id="03cf6-257">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.:::no-loc(Identity):::.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add Create:::no-loc(Identity):::Schema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="03cf6-258">:::no-loc(Identity):::est configuré dans *Areas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* .</span><span class="sxs-lookup"><span data-stu-id="03cf6-258">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="03cf6-259">Pour plus d’informations, consultez [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="03cf6-259">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="03cf6-260">Migrations, UseAuthentication et disposition</span><span class="sxs-lookup"><span data-stu-id="03cf6-260">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="03cf6-261">Activer l’authentification</span><span class="sxs-lookup"><span data-stu-id="03cf6-261">Enable authentication</span></span>

<span data-ttu-id="03cf6-262">Dans la `Configure` méthode de la `Startup` classe, appelez <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> après `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="03cf6-262">In the `Configure` method of the `Startup` class, call <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="03cf6-263">Modifications de la disposition</span><span class="sxs-lookup"><span data-stu-id="03cf6-263">Layout changes</span></span>

<span data-ttu-id="03cf6-264">Facultatif : ajoutez la connexion partielle ( `_LoginPartial` ) au fichier de disposition :</span><span class="sxs-lookup"><span data-stu-id="03cf6-264">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a><span data-ttu-id="03cf6-265">Génération de modèles automatique :::no-loc(Identity)::: dans un :::no-loc(Razor)::: projet avec autorisation</span><span class="sxs-lookup"><span data-stu-id="03cf6-265">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project with authorization</span></span>

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="03cf6-266">Certaines :::no-loc(Identity)::: options sont configurées dans *Areas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* .</span><span class="sxs-lookup"><span data-stu-id="03cf6-266">Some :::no-loc(Identity)::: options are configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="03cf6-267">Pour plus d’informations, consultez [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="03cf6-267">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="03cf6-268">Génération de modèles automatique :::no-loc(Identity)::: dans un projet MVC sans autorisation existante</span><span class="sxs-lookup"><span data-stu-id="03cf6-268">Scaffold :::no-loc(Identity)::: into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add Create:::no-loc(Identity):::Schema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="03cf6-269">Facultatif : ajoutez la connexion partielle ( `_LoginPartial` ) au fichier *Views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="03cf6-269">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="03cf6-270">Déplacez le fichier *pages/Shared/_LoginPartial. cshtml* vers *views/shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="03cf6-270">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="03cf6-271">:::no-loc(Identity):::est configuré dans *Areas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* .</span><span class="sxs-lookup"><span data-stu-id="03cf6-271">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="03cf6-272">Pour plus d’informations, consultez IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="03cf6-272">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="03cf6-273">Appeler <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> après `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="03cf6-273">Call <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="03cf6-274">Génération :::no-loc(Identity)::: de modèles automatique dans un projet MVC avec autorisation</span><span class="sxs-lookup"><span data-stu-id="03cf6-274">Scaffold :::no-loc(Identity)::: into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="03cf6-275">Supprimez le dossier *pages/Shared* et les fichiers de ce dossier.</span><span class="sxs-lookup"><span data-stu-id="03cf6-275">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a><span data-ttu-id="03cf6-276">Créer une :::no-loc(Identity)::: source d’interface utilisateur complète</span><span class="sxs-lookup"><span data-stu-id="03cf6-276">Create full :::no-loc(Identity)::: UI source</span></span>

<span data-ttu-id="03cf6-277">Pour conserver le contrôle total de l' :::no-loc(Identity)::: interface utilisateur, exécutez le générateur de :::no-loc(Identity)::: modèles automatique et sélectionnez **remplacer tous les fichiers** .</span><span class="sxs-lookup"><span data-stu-id="03cf6-277">To maintain full control of the :::no-loc(Identity)::: UI, run the :::no-loc(Identity)::: scaffolder and select **Override all files** .</span></span>

<span data-ttu-id="03cf6-278">Le code en surbrillance suivant montre les modifications pour remplacer l’interface utilisateur par défaut par :::no-loc(Identity)::: :::no-loc(Identity)::: dans une application web ASP.net Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="03cf6-278">The following highlighted code shows the changes to replace the default :::no-loc(Identity)::: UI with :::no-loc(Identity)::: in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="03cf6-279">Vous pouvez le faire pour avoir le contrôle total de l' :::no-loc(Identity)::: interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="03cf6-279">You might want to do this to have full control of the :::no-loc(Identity)::: UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="03cf6-280">La valeur par défaut :::no-loc(Identity)::: est remplacée dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="03cf6-280">The default :::no-loc(Identity)::: is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="03cf6-281">Le code suivant définit les [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath)et [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="03cf6-281">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="03cf6-282">Inscrire une `IEmailSender` implémentation, par exemple :</span><span class="sxs-lookup"><span data-stu-id="03cf6-282">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="03cf6-283">Configuration du mot de passe</span><span class="sxs-lookup"><span data-stu-id="03cf6-283">Password configuration</span></span>

<span data-ttu-id="03cf6-284">Si <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> est configuré dans `Startup.ConfigureServices` , la configuration de l' [ `[StringLength]` attribut](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) peut être requise pour la `Password` propriété dans les pages de génération de modèles automatique :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="03cf6-284">If <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded :::no-loc(Identity)::: pages.</span></span> <span data-ttu-id="03cf6-285">`InputModel``Password`les propriétés se trouvent dans les fichiers suivants :</span><span class="sxs-lookup"><span data-stu-id="03cf6-285">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs`
* `Areas/:::no-loc(Identity):::/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-register-page"></a><span data-ttu-id="03cf6-286">Désactiver la page de Registre</span><span class="sxs-lookup"><span data-stu-id="03cf6-286">Disable register page</span></span>

<span data-ttu-id="03cf6-287">Pour désactiver l’inscription des utilisateurs :</span><span class="sxs-lookup"><span data-stu-id="03cf6-287">To disable user registration:</span></span>

* <span data-ttu-id="03cf6-288">Génération de modèles automatique :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="03cf6-288">Scaffold :::no-loc(Identity):::.</span></span> <span data-ttu-id="03cf6-289">Incluez Account. Register, Account. Login et Account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="03cf6-289">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="03cf6-290">Exemple :</span><span class="sxs-lookup"><span data-stu-id="03cf6-290">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="03cf6-291">Mettre à jour les *zones/ :::no-loc(Identity)::: /pages/Account/Register.cshtml.cs* afin que les utilisateurs ne puissent pas s’inscrire depuis ce point de terminaison :</span><span class="sxs-lookup"><span data-stu-id="03cf6-291">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="03cf6-292">Mettez à jour les *zones/ :::no-loc(Identity)::: /pages/Account/Register.cshtml* pour qu’ils soient cohérents avec les modifications précédentes :</span><span class="sxs-lookup"><span data-stu-id="03cf6-292">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="03cf6-293">Commentez ou supprimez le lien d’inscription de *Areas/ :::no-loc(Identity)::: /pages/Account/login.cshtml*</span><span class="sxs-lookup"><span data-stu-id="03cf6-293">Comment out or remove the registration link from *Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="03cf6-294">Mettez à jour la page *zones/ :::no-loc(Identity)::: /pages/Account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="03cf6-294">Update the *Areas/:::no-loc(Identity):::/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="03cf6-295">Supprimez le code et les liens du fichier cshtml.</span><span class="sxs-lookup"><span data-stu-id="03cf6-295">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="03cf6-296">Supprimez le code de confirmation du `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="03cf6-296">Remove the confirmation code from the `PageModel`:</span></span>

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="03cf6-297">Utiliser une autre application pour ajouter des utilisateurs</span><span class="sxs-lookup"><span data-stu-id="03cf6-297">Use another app to add users</span></span>

<span data-ttu-id="03cf6-298">Fournissez un mécanisme pour ajouter des utilisateurs en dehors de l’application Web.</span><span class="sxs-lookup"><span data-stu-id="03cf6-298">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="03cf6-299">Les options permettant d’ajouter des utilisateurs sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="03cf6-299">Options to add users include:</span></span>

* <span data-ttu-id="03cf6-300">Une application Web d’administration dédiée.</span><span class="sxs-lookup"><span data-stu-id="03cf6-300">A dedicated admin web app.</span></span>
* <span data-ttu-id="03cf6-301">Application console.</span><span class="sxs-lookup"><span data-stu-id="03cf6-301">A console app.</span></span>

<span data-ttu-id="03cf6-302">Le code suivant décrit une approche de l’ajout d’utilisateurs :</span><span class="sxs-lookup"><span data-stu-id="03cf6-302">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="03cf6-303">Une liste d’utilisateurs est lue en mémoire.</span><span class="sxs-lookup"><span data-stu-id="03cf6-303">A list of users is read into memory.</span></span>
* <span data-ttu-id="03cf6-304">Un mot de passe fort unique est généré pour chaque utilisateur.</span><span class="sxs-lookup"><span data-stu-id="03cf6-304">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="03cf6-305">L’utilisateur est ajouté à la :::no-loc(Identity)::: base de données.</span><span class="sxs-lookup"><span data-stu-id="03cf6-305">The user is added to the :::no-loc(Identity)::: database.</span></span>
* <span data-ttu-id="03cf6-306">L’utilisateur est averti et a demandé à modifier le mot de passe.</span><span class="sxs-lookup"><span data-stu-id="03cf6-306">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="03cf6-307">L’exemple de code suivant présente l’ajout d’un utilisateur :</span><span class="sxs-lookup"><span data-stu-id="03cf6-307">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="03cf6-308">Une approche similaire peut être suivie pour les scénarios de production.</span><span class="sxs-lookup"><span data-stu-id="03cf6-308">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="03cf6-309">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="03cf6-309">Additional resources</span></span>

* [<span data-ttu-id="03cf6-310">Modifications apportées au code d’authentification pour ASP.NET Core 2,1 et versions ultérieures</span><span class="sxs-lookup"><span data-stu-id="03cf6-310">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end
