---
title: Articles basés sur des projets de ASP.NET Core créés avec des comptes d’utilisateur individuels
author: rick-anderson
description: Découvrez des articles basés sur des projets ASP.NET Core créés avec des comptes d’utilisateur individuels.
ms.author: riande
ms.date: 12/11/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authentication/individual
ms.openlocfilehash: 656006396de120b7feae6f2e08b5dad3b5a170b5
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053343"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a><span data-ttu-id="3cff9-103">Articles basés sur des projets de ASP.NET Core créés avec des comptes d’utilisateur individuels</span><span class="sxs-lookup"><span data-stu-id="3cff9-103">Articles based on ASP.NET Core projects created with individual user accounts</span></span>

<span data-ttu-id="3cff9-104">ASP.NET Core Identity est inclus dans les modèles de projet dans Visual Studio avec l’option « comptes d’utilisateur individuels ».</span><span class="sxs-lookup"><span data-stu-id="3cff9-104">ASP.NET Core Identity is included in project templates in Visual Studio with the "Individual User Accounts" option.</span></span>

<span data-ttu-id="3cff9-105">Les modèles d’authentification sont disponibles dans CLI .NET Core avec `-au Individual` :</span><span class="sxs-lookup"><span data-stu-id="3cff9-105">The authentication templates are available in .NET Core CLI with `-au Individual`:</span></span>

::: moniker range=">= aspnetcore-2.1"

```dotnetcli
dotnet new mvc -au Individual
dotnet new webapp -au Individual
```

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```dotnetcli
dotnet new mvc -au Individual
dotnet new razor -au Individual
```

::: moniker-end

<span data-ttu-id="3cff9-106">Consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore/issues/5833) pour l’authentification de l’API Web.</span><span class="sxs-lookup"><span data-stu-id="3cff9-106">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5833) for web API authentication.</span></span>

<a name="no"></a>

## <a name="no-authentication"></a><span data-ttu-id="3cff9-107">Aucune authentification</span><span class="sxs-lookup"><span data-stu-id="3cff9-107">No Authentication</span></span>

<span data-ttu-id="3cff9-108">L’authentification est spécifiée dans le CLI .NET Core avec l' `-au` option.</span><span class="sxs-lookup"><span data-stu-id="3cff9-108">Authentication is specified in the .NET Core CLI with the `-au` option.</span></span> <span data-ttu-id="3cff9-109">Dans Visual Studio, la boîte de dialogue **modifier l’authentification** est disponible pour les nouvelles applications Web.</span><span class="sxs-lookup"><span data-stu-id="3cff9-109">In Visual Studio, the **Change Authentication** dialog is available for new web applications.</span></span> <span data-ttu-id="3cff9-110">La valeur par défaut pour les nouvelles applications Web dans Visual Studio n’est **pas une authentification** .</span><span class="sxs-lookup"><span data-stu-id="3cff9-110">The default for new web apps in Visual Studio is **No Authentication** .</span></span>

<span data-ttu-id="3cff9-111">Projets créés sans authentification :</span><span class="sxs-lookup"><span data-stu-id="3cff9-111">Projects created with no authentication:</span></span>

* <span data-ttu-id="3cff9-112">Ne contiennent pas les pages Web et l’interface utilisateur pour la connexion et la déconnexion.</span><span class="sxs-lookup"><span data-stu-id="3cff9-112">Don't contain web pages and UI to sign in and sign out.</span></span>
* <span data-ttu-id="3cff9-113">Ne contiennent pas de code d’authentification.</span><span class="sxs-lookup"><span data-stu-id="3cff9-113">Don't contain authentication code.</span></span>

<a name="win"></a>

## <a name="windows-authentication"></a><span data-ttu-id="3cff9-114">Authentification Windows</span><span class="sxs-lookup"><span data-stu-id="3cff9-114">Windows Authentication</span></span>

<span data-ttu-id="3cff9-115">L’authentification Windows est spécifiée pour les nouvelles applications Web dans le CLI .NET Core avec l' `-au Windows` option.</span><span class="sxs-lookup"><span data-stu-id="3cff9-115">Windows Authentication is specified for new web apps in the .NET Core CLI with the `-au Windows` option.</span></span> <span data-ttu-id="3cff9-116">Dans Visual Studio, la boîte de dialogue **modifier l’authentification** fournit les options **d’authentification Windows** .</span><span class="sxs-lookup"><span data-stu-id="3cff9-116">In Visual Studio, the **Change Authentication** dialog provides the **Windows Authentication** options.</span></span>

<span data-ttu-id="3cff9-117">Si l’authentification Windows est sélectionnée, l’application est configurée pour utiliser le [module IIS d’authentification Windows](xref:host-and-deploy/iis/modules).</span><span class="sxs-lookup"><span data-stu-id="3cff9-117">If Windows Authentication is selected, the app is configured to use the [Windows Authentication IIS module](xref:host-and-deploy/iis/modules).</span></span> <span data-ttu-id="3cff9-118">L’authentification Windows est destinée aux sites Web intranet.</span><span class="sxs-lookup"><span data-stu-id="3cff9-118">Windows Authentication is intended for Intranet web sites.</span></span>

## <a name="dotnet-new-webapp-authentication-options"></a><span data-ttu-id="3cff9-119">nouvelles options d’authentification webapp de dotnet</span><span class="sxs-lookup"><span data-stu-id="3cff9-119">dotnet new webapp authentication options</span></span>

<span data-ttu-id="3cff9-120">Le tableau suivant présente les options d’authentification disponibles pour les nouvelles applications Web :</span><span class="sxs-lookup"><span data-stu-id="3cff9-120">The following table shows the authentication options available for new web apps:</span></span>

| <span data-ttu-id="3cff9-121">Option</span><span class="sxs-lookup"><span data-stu-id="3cff9-121">Option</span></span> | <span data-ttu-id="3cff9-122">Type d'authentification</span><span class="sxs-lookup"><span data-stu-id="3cff9-122">Type of authentication</span></span> | <span data-ttu-id="3cff9-123">Lien vers plus d’informations</span><span class="sxs-lookup"><span data-stu-id="3cff9-123">Link for more information</span></span> |
 | ----------------- | ------------ | ---------- |
| <span data-ttu-id="3cff9-124">Aucun</span><span class="sxs-lookup"><span data-stu-id="3cff9-124">None</span></span>            |  <span data-ttu-id="3cff9-125">Aucune authentification</span><span class="sxs-lookup"><span data-stu-id="3cff9-125">No authentication</span></span> | | 
| <span data-ttu-id="3cff9-126">Individuel</span><span class="sxs-lookup"><span data-stu-id="3cff9-126">Individual</span></span>      |  <span data-ttu-id="3cff9-127">Authentification individuelle</span><span class="sxs-lookup"><span data-stu-id="3cff9-127">Individual authentication</span></span> | <xref:security/authentication/identity>
| <span data-ttu-id="3cff9-128">IndividualB2C</span><span class="sxs-lookup"><span data-stu-id="3cff9-128">IndividualB2C</span></span>   |  <span data-ttu-id="3cff9-129">Authentification individuelle hébergée dans le Cloud avec Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="3cff9-129">Cloud-hosted individual authentication with Azure AD B2C</span></span> | [<span data-ttu-id="3cff9-130">Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="3cff9-130">Azure AD B2C</span></span>](/azure/active-directory-b2c/) |
| <span data-ttu-id="3cff9-131">SingleOrg</span><span class="sxs-lookup"><span data-stu-id="3cff9-131">SingleOrg</span></span>       |  <span data-ttu-id="3cff9-132">Authentification d’organisation pour un seul locataire</span><span class="sxs-lookup"><span data-stu-id="3cff9-132">Organizational authentication for a single tenant</span></span> | [<span data-ttu-id="3cff9-133">Azure AD</span><span class="sxs-lookup"><span data-stu-id="3cff9-133">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="3cff9-134">MultiOrg</span><span class="sxs-lookup"><span data-stu-id="3cff9-134">MultiOrg</span></span>        |  <span data-ttu-id="3cff9-135">Authentification d’organisation pour plusieurs locataires</span><span class="sxs-lookup"><span data-stu-id="3cff9-135">Organizational authentication for multiple tenants</span></span> | [<span data-ttu-id="3cff9-136">Azure AD</span><span class="sxs-lookup"><span data-stu-id="3cff9-136">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="3cff9-137">Windows</span><span class="sxs-lookup"><span data-stu-id="3cff9-137">Windows</span></span>         |  <span data-ttu-id="3cff9-138">Authentification Windows</span><span class="sxs-lookup"><span data-stu-id="3cff9-138">Windows authentication</span></span> | [<span data-ttu-id="3cff9-139">Authentification Windows</span><span class="sxs-lookup"><span data-stu-id="3cff9-139">Windows Authentication</span></span>](xref:security/authentication/windowsauth)

## <a name="visual-studio-new-webapp-authentication-options"></a><span data-ttu-id="3cff9-140">Nouvelles options d’authentification webapp de Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cff9-140">Visual Studio new webapp authentication options</span></span>

<span data-ttu-id="3cff9-141">Le tableau suivant présente les options d’authentification disponibles lors de la création d’une nouvelle application Web avec Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="3cff9-141">The following table shows the authentication options available when creating a new web app with Visual Studio:</span></span>

| <span data-ttu-id="3cff9-142">Option</span><span class="sxs-lookup"><span data-stu-id="3cff9-142">Option</span></span> | <span data-ttu-id="3cff9-143">Type d'authentification</span><span class="sxs-lookup"><span data-stu-id="3cff9-143">Type of authentication</span></span> | <span data-ttu-id="3cff9-144">Lien vers plus d’informations</span><span class="sxs-lookup"><span data-stu-id="3cff9-144">Link for more information</span></span> |
 | ----------------- | ------------ | ---------- |
| <span data-ttu-id="3cff9-145">Aucun</span><span class="sxs-lookup"><span data-stu-id="3cff9-145">None</span></span>            |  <span data-ttu-id="3cff9-146">Aucune authentification</span><span class="sxs-lookup"><span data-stu-id="3cff9-146">No authentication</span></span> | | 
| <span data-ttu-id="3cff9-147">Comptes d’utilisateur individuels/stocker les comptes d’utilisateur dans l’application</span><span class="sxs-lookup"><span data-stu-id="3cff9-147">Individual User Accounts / Store user accounts in-app</span></span> |  <span data-ttu-id="3cff9-148">Authentification individuelle</span><span class="sxs-lookup"><span data-stu-id="3cff9-148">Individual authentication</span></span> | <xref:security/authentication/identity> |
| <span data-ttu-id="3cff9-149">Comptes d’utilisateur individuels/se connecter à un magasin d’utilisateurs existant dans le Cloud</span><span class="sxs-lookup"><span data-stu-id="3cff9-149">Individual User Accounts / Connect to an existing user store in the cloud</span></span> |  <span data-ttu-id="3cff9-150">Authentification individuelle hébergée dans le Cloud avec Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="3cff9-150">Cloud-hosted individual authentication with Azure AD B2C</span></span> | [<span data-ttu-id="3cff9-151">Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="3cff9-151">Azure AD B2C</span></span>](/azure/active-directory-b2c/) |
| <span data-ttu-id="3cff9-152">Cloud professionnel ou scolaire/organisation unique</span><span class="sxs-lookup"><span data-stu-id="3cff9-152">Work or School Cloud / Single Org</span></span>  |  <span data-ttu-id="3cff9-153">Authentification d’organisation pour un seul locataire</span><span class="sxs-lookup"><span data-stu-id="3cff9-153">Organizational authentication for a single tenant</span></span> | [<span data-ttu-id="3cff9-154">Azure AD</span><span class="sxs-lookup"><span data-stu-id="3cff9-154">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="3cff9-155">Cloud professionnel ou scolaire/organisation multiple</span><span class="sxs-lookup"><span data-stu-id="3cff9-155">Work or School Cloud / Multiple Org</span></span> |  <span data-ttu-id="3cff9-156">Authentification d’organisation pour plusieurs locataires</span><span class="sxs-lookup"><span data-stu-id="3cff9-156">Organizational authentication for multiple tenants</span></span> | [<span data-ttu-id="3cff9-157">Azure AD</span><span class="sxs-lookup"><span data-stu-id="3cff9-157">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="3cff9-158">Windows</span><span class="sxs-lookup"><span data-stu-id="3cff9-158">Windows</span></span>         |  <span data-ttu-id="3cff9-159">Authentification Windows</span><span class="sxs-lookup"><span data-stu-id="3cff9-159">Windows authentication</span></span> | [<span data-ttu-id="3cff9-160">Authentification Windows</span><span class="sxs-lookup"><span data-stu-id="3cff9-160">Windows Authentication</span></span>](xref:security/authentication/windowsauth)

## <a name="additional-resources"></a><span data-ttu-id="3cff9-161">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="3cff9-161">Additional resources</span></span>

<span data-ttu-id="3cff9-162">Les articles suivants montrent comment utiliser le code généré dans ASP.NET Core modèles qui utilisent des comptes d’utilisateur individuels :</span><span class="sxs-lookup"><span data-stu-id="3cff9-162">The following articles show how to use the code generated in ASP.NET Core templates that use individual user accounts:</span></span>

* [<span data-ttu-id="3cff9-163">Account confirmation and password recovery in ASP.NET Core (Confirmation de compte et récupération de mot de passe dans ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="3cff9-163">Account confirmation and password recovery in ASP.NET Core</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="3cff9-164">Créer une application ASP.NET Core avec les données utilisateur protégées par l’autorisation</span><span class="sxs-lookup"><span data-stu-id="3cff9-164">Create an ASP.NET Core app with user data protected by authorization</span></span>](xref:security/authorization/secure-data)
