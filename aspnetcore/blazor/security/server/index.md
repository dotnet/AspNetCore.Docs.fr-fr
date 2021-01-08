---
title: Sécuriser les Blazor Server applications ASP.net Core
author: guardrex
description: Découvrez comment sécuriser des applications Blazor Server en tant qu’applications ASP.net core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
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
uid: blazor/security/server/index
ms.openlocfilehash: aa24def1a003a2c2608691e6168066c740f47205
ms.sourcegitcommit: 8b0e9a72c1599ce21830c843558a661ba908ce32
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024624"
---
# <a name="secure-aspnet-core-no-locblazor-server-apps"></a><span data-ttu-id="f3834-103">Sécuriser les Blazor Server applications ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="f3834-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="f3834-104">Par [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f3834-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f3834-105">Blazor Server les applications sont configurées pour la sécurité de la même façon que les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f3834-105">Blazor Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="f3834-106">Pour plus d’informations, consultez les articles sous <xref:security/index> .</span><span class="sxs-lookup"><span data-stu-id="f3834-106">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="f3834-107">Les rubriques de cette vue d’ensemble s’appliquent spécifiquement à Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="f3834-107">Topics under this overview apply specifically to Blazor Server.</span></span>

## <a name="no-locblazor-server-project-template"></a><span data-ttu-id="f3834-108">Blazor Server modèle de projet</span><span class="sxs-lookup"><span data-stu-id="f3834-108">Blazor Server project template</span></span>

<span data-ttu-id="f3834-109">Le Blazor Server modèle de projet peut être configuré pour l’authentification lors de la création du projet.</span><span class="sxs-lookup"><span data-stu-id="f3834-109">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f3834-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f3834-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f3834-111">Suivez les instructions de Visual Studio dans <xref:blazor/tooling> pour créer un nouveau Blazor Server projet avec un mécanisme d’authentification.</span><span class="sxs-lookup"><span data-stu-id="f3834-111">Follow the Visual Studio guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="f3834-112">Après avoir choisi le modèle d' **Blazor Server application** dans la boîte de dialogue **créer une application Web ASP.net Core** , sélectionnez **modifier** sous **authentification**.</span><span class="sxs-lookup"><span data-stu-id="f3834-112">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="f3834-113">Une boîte de dialogue s’ouvre pour offrir le même ensemble de mécanismes d’authentification que ceux disponibles pour les autres projets ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="f3834-113">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="f3834-114">**Aucune authentification**</span><span class="sxs-lookup"><span data-stu-id="f3834-114">**No Authentication**</span></span>
* <span data-ttu-id="f3834-115">**Comptes d’utilisateur individuels : les** comptes d’utilisateur peuvent être stockés :</span><span class="sxs-lookup"><span data-stu-id="f3834-115">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="f3834-116">Au sein de l’application à l’aide du système de ASP.NET Core [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="f3834-116">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="f3834-117">Avec [Azure ad B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="f3834-117">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="f3834-118">**Comptes professionnels ou scolaires**</span><span class="sxs-lookup"><span data-stu-id="f3834-118">**Work or School Accounts**</span></span>
* <span data-ttu-id="f3834-119">**Authentification Windows**</span><span class="sxs-lookup"><span data-stu-id="f3834-119">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f3834-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f3834-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f3834-121">Suivez les instructions de Visual Studio Code dans <xref:blazor/tooling> pour créer un nouveau Blazor Server projet avec un mécanisme d’authentification :</span><span class="sxs-lookup"><span data-stu-id="f3834-121">Follow the Visual Studio Code guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="f3834-122">Les valeurs autorisées d’authentification (`{AUTHENTICATION}`) sont présentées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="f3834-122">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="f3834-123">Mécanisme d’authentification</span><span class="sxs-lookup"><span data-stu-id="f3834-123">Authentication mechanism</span></span> | <span data-ttu-id="f3834-124">Description</span><span class="sxs-lookup"><span data-stu-id="f3834-124">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="f3834-125">`None` (valeur par défaut)</span><span class="sxs-lookup"><span data-stu-id="f3834-125">`None` (default)</span></span>         | <span data-ttu-id="f3834-126">Aucune authentification</span><span class="sxs-lookup"><span data-stu-id="f3834-126">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="f3834-127">Utilisateurs stockés dans l’application avec ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="f3834-127">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="f3834-128">Utilisateurs stockés dans [Azure ad B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="f3834-128">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="f3834-129">Authentification d’organisation pour un seul locataire</span><span class="sxs-lookup"><span data-stu-id="f3834-129">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="f3834-130">Authentification d’organisation pour plusieurs locataires</span><span class="sxs-lookup"><span data-stu-id="f3834-130">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="f3834-131">Authentification Windows</span><span class="sxs-lookup"><span data-stu-id="f3834-131">Windows Authentication</span></span> |

<span data-ttu-id="f3834-132">À l’aide de l' `-o|--output` option, la commande utilise la valeur fournie pour l' `{APP NAME}` espace réservé à :</span><span class="sxs-lookup"><span data-stu-id="f3834-132">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="f3834-133">Créez un dossier pour le projet.</span><span class="sxs-lookup"><span data-stu-id="f3834-133">Create a folder for the project.</span></span>
* <span data-ttu-id="f3834-134">Nommez ce projet.</span><span class="sxs-lookup"><span data-stu-id="f3834-134">Name the project.</span></span>

<span data-ttu-id="f3834-135">Pour plus d’informations, consultez la [`dotnet new`](/dotnet/core/tools/dotnet-new) commande dans le guide .net core.</span><span class="sxs-lookup"><span data-stu-id="f3834-135">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f3834-136">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f3834-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="f3834-137">Suivez les instructions de Visual Studio pour Mac dans <xref:blazor/tooling> .</span><span class="sxs-lookup"><span data-stu-id="f3834-137">Follow the Visual Studio for Mac guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="f3834-138">Dans l’étape **configurer votre nouvelle Blazor Server application** , sélectionnez **authentification individuelle (dans l’application)** dans la liste déroulante **authentification** .</span><span class="sxs-lookup"><span data-stu-id="f3834-138">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="f3834-139">L’application est créée pour les utilisateurs individuels stockés dans l’application avec ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="f3834-139">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f3834-140">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="f3834-140">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="f3834-141">Créez un Blazor Server projet avec un mécanisme d’authentification à l’aide de la commande suivante dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="f3834-141">Create a new Blazor Server project with an authentication mechanism using the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="f3834-142">Les valeurs autorisées d’authentification (`{AUTHENTICATION}`) sont présentées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="f3834-142">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="f3834-143">Mécanisme d’authentification</span><span class="sxs-lookup"><span data-stu-id="f3834-143">Authentication mechanism</span></span> | <span data-ttu-id="f3834-144">Description</span><span class="sxs-lookup"><span data-stu-id="f3834-144">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="f3834-145">`None` (valeur par défaut)</span><span class="sxs-lookup"><span data-stu-id="f3834-145">`None` (default)</span></span>         | <span data-ttu-id="f3834-146">Aucune authentification</span><span class="sxs-lookup"><span data-stu-id="f3834-146">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="f3834-147">Utilisateurs stockés dans l’application avec ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="f3834-147">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="f3834-148">Utilisateurs stockés dans [Azure ad B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="f3834-148">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="f3834-149">Authentification d’organisation pour un seul locataire</span><span class="sxs-lookup"><span data-stu-id="f3834-149">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="f3834-150">Authentification d’organisation pour plusieurs locataires</span><span class="sxs-lookup"><span data-stu-id="f3834-150">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="f3834-151">Authentification Windows</span><span class="sxs-lookup"><span data-stu-id="f3834-151">Windows Authentication</span></span> |

<span data-ttu-id="f3834-152">À l’aide de l' `-o|--output` option, la commande utilise la valeur fournie pour l' `{APP NAME}` espace réservé à :</span><span class="sxs-lookup"><span data-stu-id="f3834-152">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="f3834-153">Créez un dossier pour le projet.</span><span class="sxs-lookup"><span data-stu-id="f3834-153">Create a folder for the project.</span></span>
* <span data-ttu-id="f3834-154">Nommez ce projet.</span><span class="sxs-lookup"><span data-stu-id="f3834-154">Name the project.</span></span>

<span data-ttu-id="f3834-155">Pour plus d'informations :</span><span class="sxs-lookup"><span data-stu-id="f3834-155">For more information:</span></span>

* <span data-ttu-id="f3834-156">Consultez la [`dotnet new`](/dotnet/core/tools/dotnet-new) commande dans le guide .net core.</span><span class="sxs-lookup"><span data-stu-id="f3834-156">See the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>
* <span data-ttu-id="f3834-157">Exécutez la commande help pour le Blazor Server modèle ( `blazorserver` ) dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="f3834-157">Execute the help command for the Blazor Server template (`blazorserver`) in a command shell:</span></span>

  ```dotnetcli
  dotnet new blazorserver --help
  ```

---

## <a name="scaffold-no-locidentity"></a><span data-ttu-id="f3834-158">Destin Identity</span><span class="sxs-lookup"><span data-stu-id="f3834-158">Scaffold Identity</span></span>

<span data-ttu-id="f3834-159">Génération Identity de modèles automatique dans un Blazor Server projet :</span><span class="sxs-lookup"><span data-stu-id="f3834-159">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="f3834-160">[Sans autorisation existante](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span><span class="sxs-lookup"><span data-stu-id="f3834-160">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="f3834-161">[Avec autorisation](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="f3834-161">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f3834-162">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="f3834-162">Additional resources</span></span>

* [<span data-ttu-id="f3834-163">Démarrage rapide : Ajouter la connexion avec Microsoft à une application web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f3834-163">Quickstart: Add sign-in with Microsoft to an ASP.NET Core web app</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [<span data-ttu-id="f3834-164">Démarrage rapide : Protéger une API web ASP.NET Core avec la plateforme d’identités Microsoft</span><span class="sxs-lookup"><span data-stu-id="f3834-164">Quickstart: Protect an ASP.NET Core web API with Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-web-api)
* <span data-ttu-id="f3834-165"><xref:host-and-deploy/proxy-load-balancer>: Fournit des conseils sur :</span><span class="sxs-lookup"><span data-stu-id="f3834-165"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="f3834-166">Utilisation de l’intergiciel d’en-têtes transférés pour conserver les informations de schéma HTTPs entre les serveurs proxy et les réseaux internes.</span><span class="sxs-lookup"><span data-stu-id="f3834-166">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="f3834-167">Scénarios et cas d’utilisation supplémentaires, y compris la configuration manuelle du schéma, les modifications du chemin des demandes pour le routage des demandes correct et le transfert du modèle de demande pour les proxys inversés Linux et non-IIS.</span><span class="sxs-lookup"><span data-stu-id="f3834-167">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>
