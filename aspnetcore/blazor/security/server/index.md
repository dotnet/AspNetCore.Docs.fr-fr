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
ms.openlocfilehash: 5a3d3c6e06653de7f0d01565444d37013f347a5b
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280319"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="b5ff6-103">Sécuriser les Blazor Server applications ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="b5ff6-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="b5ff6-104">Blazor Server les applications sont configurées pour la sécurité de la même façon que les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b5ff6-104">Blazor Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="b5ff6-105">Pour plus d’informations, consultez les articles sous <xref:security/index> .</span><span class="sxs-lookup"><span data-stu-id="b5ff6-105">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="b5ff6-106">Les rubriques de cette vue d’ensemble s’appliquent spécifiquement à Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="b5ff6-106">Topics under this overview apply specifically to Blazor Server.</span></span>

## <a name="blazor-server-project-template"></a><span data-ttu-id="b5ff6-107">Blazor Server modèle de projet</span><span class="sxs-lookup"><span data-stu-id="b5ff6-107">Blazor Server project template</span></span>

<span data-ttu-id="b5ff6-108">Le Blazor Server modèle de projet peut être configuré pour l’authentification lors de la création du projet.</span><span class="sxs-lookup"><span data-stu-id="b5ff6-108">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b5ff6-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b5ff6-109">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b5ff6-110">Suivez les instructions de Visual Studio dans <xref:blazor/tooling> pour créer un nouveau Blazor Server projet avec un mécanisme d’authentification.</span><span class="sxs-lookup"><span data-stu-id="b5ff6-110">Follow the Visual Studio guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="b5ff6-111">Après avoir choisi le modèle d' **Blazor Server application** dans la boîte de dialogue **créer une application Web ASP.net Core** , sélectionnez **modifier** sous **authentification**.</span><span class="sxs-lookup"><span data-stu-id="b5ff6-111">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="b5ff6-112">Une boîte de dialogue s’ouvre pour offrir le même ensemble de mécanismes d’authentification que ceux disponibles pour les autres projets ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="b5ff6-112">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="b5ff6-113">**Aucune authentification**</span><span class="sxs-lookup"><span data-stu-id="b5ff6-113">**No Authentication**</span></span>
* <span data-ttu-id="b5ff6-114">**Comptes d’utilisateur individuels : les** comptes d’utilisateur peuvent être stockés :</span><span class="sxs-lookup"><span data-stu-id="b5ff6-114">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="b5ff6-115">Au sein de l’application à l’aide du système de ASP.NET Core [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="b5ff6-115">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="b5ff6-116">Avec [Azure ad B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="b5ff6-116">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="b5ff6-117">**Comptes professionnels ou scolaires**</span><span class="sxs-lookup"><span data-stu-id="b5ff6-117">**Work or School Accounts**</span></span>
* <span data-ttu-id="b5ff6-118">**Authentification Windows**</span><span class="sxs-lookup"><span data-stu-id="b5ff6-118">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b5ff6-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b5ff6-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b5ff6-120">Suivez les instructions de Visual Studio Code dans <xref:blazor/tooling> pour créer un nouveau Blazor Server projet avec un mécanisme d’authentification :</span><span class="sxs-lookup"><span data-stu-id="b5ff6-120">Follow the Visual Studio Code guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="b5ff6-121">Les valeurs autorisées d’authentification (`{AUTHENTICATION}`) sont présentées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="b5ff6-121">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="b5ff6-122">Mécanisme d’authentification</span><span class="sxs-lookup"><span data-stu-id="b5ff6-122">Authentication mechanism</span></span> | <span data-ttu-id="b5ff6-123">Description</span><span class="sxs-lookup"><span data-stu-id="b5ff6-123">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="b5ff6-124">`None` (valeur par défaut)</span><span class="sxs-lookup"><span data-stu-id="b5ff6-124">`None` (default)</span></span>         | <span data-ttu-id="b5ff6-125">Aucune authentification</span><span class="sxs-lookup"><span data-stu-id="b5ff6-125">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="b5ff6-126">Utilisateurs stockés dans l’application avec ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="b5ff6-126">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="b5ff6-127">Utilisateurs stockés dans [Azure ad B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="b5ff6-127">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="b5ff6-128">Authentification d’organisation pour un seul locataire</span><span class="sxs-lookup"><span data-stu-id="b5ff6-128">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="b5ff6-129">Authentification d’organisation pour plusieurs locataires</span><span class="sxs-lookup"><span data-stu-id="b5ff6-129">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="b5ff6-130">Authentification Windows</span><span class="sxs-lookup"><span data-stu-id="b5ff6-130">Windows Authentication</span></span> |

<span data-ttu-id="b5ff6-131">À l’aide de l' `-o|--output` option, la commande utilise la valeur fournie pour l' `{APP NAME}` espace réservé à :</span><span class="sxs-lookup"><span data-stu-id="b5ff6-131">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="b5ff6-132">Créez un dossier pour le projet.</span><span class="sxs-lookup"><span data-stu-id="b5ff6-132">Create a folder for the project.</span></span>
* <span data-ttu-id="b5ff6-133">Nommez ce projet.</span><span class="sxs-lookup"><span data-stu-id="b5ff6-133">Name the project.</span></span>

<span data-ttu-id="b5ff6-134">Pour plus d’informations, consultez la [`dotnet new`](/dotnet/core/tools/dotnet-new) commande dans le guide .net core.</span><span class="sxs-lookup"><span data-stu-id="b5ff6-134">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b5ff6-135">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="b5ff6-135">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="b5ff6-136">Suivez les instructions de Visual Studio pour Mac dans <xref:blazor/tooling> .</span><span class="sxs-lookup"><span data-stu-id="b5ff6-136">Follow the Visual Studio for Mac guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="b5ff6-137">Dans l’étape **configurer votre nouvelle Blazor Server application** , sélectionnez **authentification individuelle (dans l’application)** dans la liste déroulante **authentification** .</span><span class="sxs-lookup"><span data-stu-id="b5ff6-137">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="b5ff6-138">L’application est créée pour les utilisateurs individuels stockés dans l’application avec ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="b5ff6-138">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b5ff6-139">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="b5ff6-139">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="b5ff6-140">Créez un Blazor Server projet avec un mécanisme d’authentification à l’aide de la commande suivante dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="b5ff6-140">Create a new Blazor Server project with an authentication mechanism using the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="b5ff6-141">Les valeurs autorisées d’authentification (`{AUTHENTICATION}`) sont présentées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="b5ff6-141">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="b5ff6-142">Mécanisme d’authentification</span><span class="sxs-lookup"><span data-stu-id="b5ff6-142">Authentication mechanism</span></span> | <span data-ttu-id="b5ff6-143">Description</span><span class="sxs-lookup"><span data-stu-id="b5ff6-143">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="b5ff6-144">`None` (valeur par défaut)</span><span class="sxs-lookup"><span data-stu-id="b5ff6-144">`None` (default)</span></span>         | <span data-ttu-id="b5ff6-145">Aucune authentification</span><span class="sxs-lookup"><span data-stu-id="b5ff6-145">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="b5ff6-146">Utilisateurs stockés dans l’application avec ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="b5ff6-146">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="b5ff6-147">Utilisateurs stockés dans [Azure ad B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="b5ff6-147">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="b5ff6-148">Authentification d’organisation pour un seul locataire</span><span class="sxs-lookup"><span data-stu-id="b5ff6-148">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="b5ff6-149">Authentification d’organisation pour plusieurs locataires</span><span class="sxs-lookup"><span data-stu-id="b5ff6-149">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="b5ff6-150">Authentification Windows</span><span class="sxs-lookup"><span data-stu-id="b5ff6-150">Windows Authentication</span></span> |

<span data-ttu-id="b5ff6-151">À l’aide de l' `-o|--output` option, la commande utilise la valeur fournie pour l' `{APP NAME}` espace réservé à :</span><span class="sxs-lookup"><span data-stu-id="b5ff6-151">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="b5ff6-152">Créez un dossier pour le projet.</span><span class="sxs-lookup"><span data-stu-id="b5ff6-152">Create a folder for the project.</span></span>
* <span data-ttu-id="b5ff6-153">Nommez ce projet.</span><span class="sxs-lookup"><span data-stu-id="b5ff6-153">Name the project.</span></span>

<span data-ttu-id="b5ff6-154">Pour plus d'informations :</span><span class="sxs-lookup"><span data-stu-id="b5ff6-154">For more information:</span></span>

* <span data-ttu-id="b5ff6-155">Consultez la [`dotnet new`](/dotnet/core/tools/dotnet-new) commande dans le guide .net core.</span><span class="sxs-lookup"><span data-stu-id="b5ff6-155">See the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>
* <span data-ttu-id="b5ff6-156">Exécutez la commande help pour le Blazor Server modèle ( `blazorserver` ) dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="b5ff6-156">Execute the help command for the Blazor Server template (`blazorserver`) in a command shell:</span></span>

  ```dotnetcli
  dotnet new blazorserver --help
  ```

---

## <a name="scaffold-identity"></a><span data-ttu-id="b5ff6-157">Destin Identity</span><span class="sxs-lookup"><span data-stu-id="b5ff6-157">Scaffold Identity</span></span>

<span data-ttu-id="b5ff6-158">Génération Identity de modèles automatique dans un Blazor Server projet :</span><span class="sxs-lookup"><span data-stu-id="b5ff6-158">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="b5ff6-159">[Sans autorisation existante](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span><span class="sxs-lookup"><span data-stu-id="b5ff6-159">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="b5ff6-160">[Avec autorisation](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="b5ff6-160">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>

## <a name="azure-app-service-on-linux-with-identity-server"></a><span data-ttu-id="b5ff6-161">Azure App Service sur Linux avec Identity serveur</span><span class="sxs-lookup"><span data-stu-id="b5ff6-161">Azure App Service on Linux with Identity Server</span></span>

<span data-ttu-id="b5ff6-162">Spécifiez l’émetteur de manière explicite lors du déploiement sur Azure App Service sur Linux avec le Identity serveur.</span><span class="sxs-lookup"><span data-stu-id="b5ff6-162">Specify the issuer explicitly when deploying to Azure App Service on Linux with Identity Server.</span></span> <span data-ttu-id="b5ff6-163">Pour plus d’informations, consultez <xref:security/authentication/identity/spa#azure-app-service-on-linux>.</span><span class="sxs-lookup"><span data-stu-id="b5ff6-163">For more information, see <xref:security/authentication/identity/spa#azure-app-service-on-linux>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b5ff6-164">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="b5ff6-164">Additional resources</span></span>

* [<span data-ttu-id="b5ff6-165">Démarrage rapide : Ajouter la connexion avec Microsoft à une application web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b5ff6-165">Quickstart: Add sign-in with Microsoft to an ASP.NET Core web app</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [<span data-ttu-id="b5ff6-166">Démarrage rapide : Protéger une API web ASP.NET Core avec la plateforme d’identités Microsoft</span><span class="sxs-lookup"><span data-stu-id="b5ff6-166">Quickstart: Protect an ASP.NET Core web API with Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-web-api)
* <span data-ttu-id="b5ff6-167"><xref:host-and-deploy/proxy-load-balancer>: Fournit des conseils sur :</span><span class="sxs-lookup"><span data-stu-id="b5ff6-167"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="b5ff6-168">Utilisation de l’intergiciel d’en-têtes transférés pour conserver les informations de schéma HTTPs entre les serveurs proxy et les réseaux internes.</span><span class="sxs-lookup"><span data-stu-id="b5ff6-168">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="b5ff6-169">Scénarios et cas d’utilisation supplémentaires, y compris la configuration manuelle du schéma, les modifications du chemin des demandes pour le routage des demandes correct et le transfert du modèle de demande pour les proxys inversés Linux et non-IIS.</span><span class="sxs-lookup"><span data-stu-id="b5ff6-169">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>
