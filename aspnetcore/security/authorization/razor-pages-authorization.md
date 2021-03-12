---
title: Razor Conventions d’autorisation des pages dans ASP.NET Core
author: rick-anderson
description: Découvrez comment contrôler l’accès aux pages à l’aide de conventions qui autorisent les utilisateurs et autorisent les utilisateurs anonymes à accéder aux pages ou aux dossiers de pages.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2019
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
uid: security/authorization/razor-pages-authorization
ms.openlocfilehash: ffcb16b626773da69c45b8ab5dbd7c3cdc84bb5f
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589112"
---
# <a name="razor-pages-authorization-conventions-in-aspnet-core"></a><span data-ttu-id="c80fb-103">Razor Conventions d’autorisation des pages dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c80fb-103">Razor Pages authorization conventions in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c80fb-104">Une façon de contrôler l’accès dans votre Razor application pages consiste à utiliser des conventions d’autorisation au démarrage.</span><span class="sxs-lookup"><span data-stu-id="c80fb-104">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="c80fb-105">Ces conventions vous permettent d’autoriser les utilisateurs et d’autoriser les utilisateurs anonymes à accéder à des pages ou des dossiers de pages individuels.</span><span class="sxs-lookup"><span data-stu-id="c80fb-105">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="c80fb-106">Les conventions décrites dans cette rubrique appliquent automatiquement des [filtres d’autorisation](xref:mvc/controllers/filters#authorization-filters) pour contrôler l’accès.</span><span class="sxs-lookup"><span data-stu-id="c80fb-106">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="c80fb-107">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/razor-pages-authorization/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c80fb-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="c80fb-108">L’exemple d’application utilise [ cookie l' ASP.NET Core Identity authentification sans ](xref:security/authentication/cookie).</span><span class="sxs-lookup"><span data-stu-id="c80fb-108">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="c80fb-109">Les concepts et les exemples présentés dans cette rubrique s’appliquent également aux applications qui utilisent ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="c80fb-109">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="c80fb-110">Pour utiliser ASP.NET Core Identity , suivez les instructions de la <xref:security/authentication/identity> .</span><span class="sxs-lookup"><span data-stu-id="c80fb-110">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="c80fb-111">Demander l’autorisation d’accéder à une page</span><span class="sxs-lookup"><span data-stu-id="c80fb-111">Require authorization to access a page</span></span>

<span data-ttu-id="c80fb-112">Utilisez la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> Convention pour ajouter un <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> à la page à l’emplacement spécifié :</span><span class="sxs-lookup"><span data-stu-id="c80fb-112">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=3)]

<span data-ttu-id="c80fb-113">Le chemin d’accès spécifié est le chemin d’accès du moteur d’affichage, qui est le Razor chemin d’accès relatif à la racine des pages sans extension et qui contient uniquement des barres obliques.</span><span class="sxs-lookup"><span data-stu-id="c80fb-113">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="c80fb-114">Pour spécifier une [stratégie d’autorisation](xref:security/authorization/policies), utilisez une [surcharge AuthorizePage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span><span class="sxs-lookup"><span data-stu-id="c80fb-114">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="c80fb-115">Un <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> peut être appliqué à une classe de modèle de page avec l' `[Authorize]` attribut de filtre.</span><span class="sxs-lookup"><span data-stu-id="c80fb-115">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="c80fb-116">Pour plus d’informations, consultez [Authorize Filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span><span class="sxs-lookup"><span data-stu-id="c80fb-116">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="c80fb-117">Demander l’autorisation d’accéder à un dossier de pages</span><span class="sxs-lookup"><span data-stu-id="c80fb-117">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="c80fb-118">Utilisez la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> Convention pour ajouter un <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> à toutes les pages d’un dossier à l’emplacement spécifié :</span><span class="sxs-lookup"><span data-stu-id="c80fb-118">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=4)]

<span data-ttu-id="c80fb-119">Le chemin d’accès spécifié est le chemin d’accès du moteur d’affichage, qui est le Razor chemin d’accès relatif à la racine des pages.</span><span class="sxs-lookup"><span data-stu-id="c80fb-119">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="c80fb-120">Pour spécifier une [stratégie d’autorisation](xref:security/authorization/policies), utilisez une [surcharge AuthorizeFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span><span class="sxs-lookup"><span data-stu-id="c80fb-120">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="c80fb-121">Exiger une autorisation pour accéder à une page de zone</span><span class="sxs-lookup"><span data-stu-id="c80fb-121">Require authorization to access an area page</span></span>

<span data-ttu-id="c80fb-122">Utilisez la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> Convention pour ajouter un <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> à la page de zone à l’emplacement spécifié :</span><span class="sxs-lookup"><span data-stu-id="c80fb-122">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="c80fb-123">Le nom de la page est le chemin d’accès du fichier sans extension par rapport au répertoire racine des pages pour la zone spécifiée.</span><span class="sxs-lookup"><span data-stu-id="c80fb-123">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="c80fb-124">Par exemple, le nom de la page zones de fichier */ Identity /pages/manage/Accounts.cshtml* est */Manage/Accounts*.</span><span class="sxs-lookup"><span data-stu-id="c80fb-124">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts*.</span></span>

<span data-ttu-id="c80fb-125">Pour spécifier une [stratégie d’autorisation](xref:security/authorization/policies), utilisez une [surcharge AuthorizeAreaPage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span><span class="sxs-lookup"><span data-stu-id="c80fb-125">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="c80fb-126">Demander l’autorisation d’accéder à un dossier de zones</span><span class="sxs-lookup"><span data-stu-id="c80fb-126">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="c80fb-127">Utilisez la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> Convention pour ajouter un <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> à toutes les zones d’un dossier au chemin d’accès spécifié :</span><span class="sxs-lookup"><span data-stu-id="c80fb-127">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="c80fb-128">Le chemin d’accès au dossier est le chemin d’accès du dossier relatif au répertoire racine des pages pour la zone spécifiée.</span><span class="sxs-lookup"><span data-stu-id="c80fb-128">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="c80fb-129">Par exemple, le chemin d’accès au dossier pour les fichiers sous *Areas/ Identity /pages/manage/* est */Manage*.</span><span class="sxs-lookup"><span data-stu-id="c80fb-129">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage*.</span></span>

<span data-ttu-id="c80fb-130">Pour spécifier une [stratégie d’autorisation](xref:security/authorization/policies), utilisez une [surcharge AuthorizeAreaFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span><span class="sxs-lookup"><span data-stu-id="c80fb-130">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="c80fb-131">Autoriser l’accès anonyme à une page</span><span class="sxs-lookup"><span data-stu-id="c80fb-131">Allow anonymous access to a page</span></span>

<span data-ttu-id="c80fb-132">Utilisez la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> Convention pour ajouter un <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> à une page à l’emplacement spécifié :</span><span class="sxs-lookup"><span data-stu-id="c80fb-132">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="c80fb-133">Le chemin d’accès spécifié est le chemin d’accès du moteur d’affichage, qui est le Razor chemin d’accès relatif à la racine des pages sans extension et qui contient uniquement des barres obliques.</span><span class="sxs-lookup"><span data-stu-id="c80fb-133">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="c80fb-134">Autoriser l’accès anonyme à un dossier de pages</span><span class="sxs-lookup"><span data-stu-id="c80fb-134">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="c80fb-135">Utilisez la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> Convention pour ajouter un <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> à toutes les pages d’un dossier à l’emplacement spécifié :</span><span class="sxs-lookup"><span data-stu-id="c80fb-135">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="c80fb-136">Le chemin d’accès spécifié est le chemin d’accès du moteur d’affichage, qui est le Razor chemin d’accès relatif à la racine des pages.</span><span class="sxs-lookup"><span data-stu-id="c80fb-136">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="c80fb-137">Remarque sur la combinaison des accès autorisés et anonymes</span><span class="sxs-lookup"><span data-stu-id="c80fb-137">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="c80fb-138">Il est possible de spécifier qu’un dossier de pages requiert une autorisation, puis de spécifier qu’une page de ce dossier autorise un accès anonyme :</span><span class="sxs-lookup"><span data-stu-id="c80fb-138">It's valid to specify that a folder of pages requires authorization and then specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="c80fb-139">L’inverse, toutefois, n’est pas valide.</span><span class="sxs-lookup"><span data-stu-id="c80fb-139">The reverse, however, isn't valid.</span></span> <span data-ttu-id="c80fb-140">Vous ne pouvez pas déclarer un dossier de pages pour un accès anonyme, puis spécifier une page dans ce dossier qui nécessite une autorisation :</span><span class="sxs-lookup"><span data-stu-id="c80fb-140">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="c80fb-141">La demande d’autorisation sur la page privée échoue.</span><span class="sxs-lookup"><span data-stu-id="c80fb-141">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="c80fb-142">Lorsque <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> et <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> sont appliqués à la page, le <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> est prioritaire et contrôle l’accès.</span><span class="sxs-lookup"><span data-stu-id="c80fb-142">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c80fb-143">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="c80fb-143">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c80fb-144">Une façon de contrôler l’accès dans votre Razor application pages consiste à utiliser des conventions d’autorisation au démarrage.</span><span class="sxs-lookup"><span data-stu-id="c80fb-144">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="c80fb-145">Ces conventions vous permettent d’autoriser les utilisateurs et d’autoriser les utilisateurs anonymes à accéder à des pages ou des dossiers de pages individuels.</span><span class="sxs-lookup"><span data-stu-id="c80fb-145">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="c80fb-146">Les conventions décrites dans cette rubrique appliquent automatiquement des [filtres d’autorisation](xref:mvc/controllers/filters#authorization-filters) pour contrôler l’accès.</span><span class="sxs-lookup"><span data-stu-id="c80fb-146">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="c80fb-147">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/razor-pages-authorization/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c80fb-147">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="c80fb-148">L’exemple d’application utilise [ cookie l' ASP.NET Core Identity authentification sans ](xref:security/authentication/cookie).</span><span class="sxs-lookup"><span data-stu-id="c80fb-148">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="c80fb-149">Les concepts et les exemples présentés dans cette rubrique s’appliquent également aux applications qui utilisent ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="c80fb-149">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="c80fb-150">Pour utiliser ASP.NET Core Identity , suivez les instructions de la <xref:security/authentication/identity> .</span><span class="sxs-lookup"><span data-stu-id="c80fb-150">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="c80fb-151">Demander l’autorisation d’accéder à une page</span><span class="sxs-lookup"><span data-stu-id="c80fb-151">Require authorization to access a page</span></span>

<span data-ttu-id="c80fb-152">Utilisez la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> Convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> pour ajouter un <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> à la page à l’emplacement spécifié :</span><span class="sxs-lookup"><span data-stu-id="c80fb-152">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

<span data-ttu-id="c80fb-153">Le chemin d’accès spécifié est le chemin d’accès du moteur d’affichage, qui est le Razor chemin d’accès relatif à la racine des pages sans extension et qui contient uniquement des barres obliques.</span><span class="sxs-lookup"><span data-stu-id="c80fb-153">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="c80fb-154">Pour spécifier une [stratégie d’autorisation](xref:security/authorization/policies), utilisez une [surcharge AuthorizePage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span><span class="sxs-lookup"><span data-stu-id="c80fb-154">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="c80fb-155">Un <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> peut être appliqué à une classe de modèle de page avec l' `[Authorize]` attribut de filtre.</span><span class="sxs-lookup"><span data-stu-id="c80fb-155">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="c80fb-156">Pour plus d’informations, consultez [Authorize Filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span><span class="sxs-lookup"><span data-stu-id="c80fb-156">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="c80fb-157">Demander l’autorisation d’accéder à un dossier de pages</span><span class="sxs-lookup"><span data-stu-id="c80fb-157">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="c80fb-158">Utilisez la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> Convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> pour ajouter un <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> à toutes les pages d’un dossier à l’emplacement spécifié :</span><span class="sxs-lookup"><span data-stu-id="c80fb-158">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

<span data-ttu-id="c80fb-159">Le chemin d’accès spécifié est le chemin d’accès du moteur d’affichage, qui est le Razor chemin d’accès relatif à la racine des pages.</span><span class="sxs-lookup"><span data-stu-id="c80fb-159">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="c80fb-160">Pour spécifier une [stratégie d’autorisation](xref:security/authorization/policies), utilisez une [surcharge AuthorizeFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span><span class="sxs-lookup"><span data-stu-id="c80fb-160">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="c80fb-161">Exiger une autorisation pour accéder à une page de zone</span><span class="sxs-lookup"><span data-stu-id="c80fb-161">Require authorization to access an area page</span></span>

<span data-ttu-id="c80fb-162">Utilisez la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> Convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> pour ajouter un <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> à la page de zone à l’emplacement spécifié :</span><span class="sxs-lookup"><span data-stu-id="c80fb-162">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="c80fb-163">Le nom de la page est le chemin d’accès du fichier sans extension par rapport au répertoire racine des pages pour la zone spécifiée.</span><span class="sxs-lookup"><span data-stu-id="c80fb-163">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="c80fb-164">Par exemple, le nom de la page zones de fichier */ Identity /pages/manage/Accounts.cshtml* est */Manage/Accounts*.</span><span class="sxs-lookup"><span data-stu-id="c80fb-164">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts*.</span></span>

<span data-ttu-id="c80fb-165">Pour spécifier une [stratégie d’autorisation](xref:security/authorization/policies), utilisez une [surcharge AuthorizeAreaPage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span><span class="sxs-lookup"><span data-stu-id="c80fb-165">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="c80fb-166">Demander l’autorisation d’accéder à un dossier de zones</span><span class="sxs-lookup"><span data-stu-id="c80fb-166">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="c80fb-167">Utilisez la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> Convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> pour ajouter un <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> à toutes les zones d’un dossier au chemin d’accès spécifié :</span><span class="sxs-lookup"><span data-stu-id="c80fb-167">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="c80fb-168">Le chemin d’accès au dossier est le chemin d’accès du dossier relatif au répertoire racine des pages pour la zone spécifiée.</span><span class="sxs-lookup"><span data-stu-id="c80fb-168">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="c80fb-169">Par exemple, le chemin d’accès au dossier pour les fichiers sous *Areas/ Identity /pages/manage/* est */Manage*.</span><span class="sxs-lookup"><span data-stu-id="c80fb-169">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage*.</span></span>

<span data-ttu-id="c80fb-170">Pour spécifier une [stratégie d’autorisation](xref:security/authorization/policies), utilisez une [surcharge AuthorizeAreaFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span><span class="sxs-lookup"><span data-stu-id="c80fb-170">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="c80fb-171">Autoriser l’accès anonyme à une page</span><span class="sxs-lookup"><span data-stu-id="c80fb-171">Allow anonymous access to a page</span></span>

<span data-ttu-id="c80fb-172">Utilisez la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> Convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> pour ajouter un <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> à une page à l’emplacement spécifié :</span><span class="sxs-lookup"><span data-stu-id="c80fb-172">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

<span data-ttu-id="c80fb-173">Le chemin d’accès spécifié est le chemin d’accès du moteur d’affichage, qui est le Razor chemin d’accès relatif à la racine des pages sans extension et qui contient uniquement des barres obliques.</span><span class="sxs-lookup"><span data-stu-id="c80fb-173">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="c80fb-174">Autoriser l’accès anonyme à un dossier de pages</span><span class="sxs-lookup"><span data-stu-id="c80fb-174">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="c80fb-175">Utilisez la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> Convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> pour ajouter un <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> à toutes les pages d’un dossier à l’emplacement spécifié :</span><span class="sxs-lookup"><span data-stu-id="c80fb-175">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

<span data-ttu-id="c80fb-176">Le chemin d’accès spécifié est le chemin d’accès du moteur d’affichage, qui est le Razor chemin d’accès relatif à la racine des pages.</span><span class="sxs-lookup"><span data-stu-id="c80fb-176">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="c80fb-177">Remarque sur la combinaison des accès autorisés et anonymes</span><span class="sxs-lookup"><span data-stu-id="c80fb-177">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="c80fb-178">Il est possible de spécifier qu’un dossier de pages nécessitant une autorisation et de spécifier qu’une page de ce dossier autorise un accès anonyme :</span><span class="sxs-lookup"><span data-stu-id="c80fb-178">It's valid to specify that a folder of pages that require authorization and than specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="c80fb-179">L’inverse, toutefois, n’est pas valide.</span><span class="sxs-lookup"><span data-stu-id="c80fb-179">The reverse, however, isn't valid.</span></span> <span data-ttu-id="c80fb-180">Vous ne pouvez pas déclarer un dossier de pages pour un accès anonyme, puis spécifier une page dans ce dossier qui nécessite une autorisation :</span><span class="sxs-lookup"><span data-stu-id="c80fb-180">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="c80fb-181">La demande d’autorisation sur la page privée échoue.</span><span class="sxs-lookup"><span data-stu-id="c80fb-181">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="c80fb-182">Lorsque <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> et <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> sont appliqués à la page, le <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> est prioritaire et contrôle l’accès.</span><span class="sxs-lookup"><span data-stu-id="c80fb-182">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c80fb-183">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="c80fb-183">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end
