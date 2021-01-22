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
ms.openlocfilehash: 5031273c3395be4365b3a6d239ebce7aaf9b66ac
ms.sourcegitcommit: cc405f20537484744423ddaf87bd1e7d82b6bdf0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98658636"
---
# <a name="secure-aspnet-core-no-locblazor-server-apps"></a><span data-ttu-id="ce992-103">Sécuriser les Blazor Server applications ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="ce992-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="ce992-104">Par [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ce992-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="ce992-105">Blazor Server les applications sont configurées pour la sécurité de la même façon que les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ce992-105">Blazor Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="ce992-106">Pour plus d’informations, consultez les articles sous <xref:security/index> .</span><span class="sxs-lookup"><span data-stu-id="ce992-106">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="ce992-107">Les rubriques de cette vue d’ensemble s’appliquent spécifiquement à Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="ce992-107">Topics under this overview apply specifically to Blazor Server.</span></span>

## <a name="no-locblazor-server-project-template"></a><span data-ttu-id="ce992-108">Blazor Server modèle de projet</span><span class="sxs-lookup"><span data-stu-id="ce992-108">Blazor Server project template</span></span>

<span data-ttu-id="ce992-109">Le Blazor Server modèle de projet peut être configuré pour l’authentification lors de la création du projet.</span><span class="sxs-lookup"><span data-stu-id="ce992-109">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ce992-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ce992-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ce992-111">Suivez les instructions de Visual Studio dans <xref:blazor/tooling> pour créer un nouveau Blazor Server projet avec un mécanisme d’authentification.</span><span class="sxs-lookup"><span data-stu-id="ce992-111">Follow the Visual Studio guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="ce992-112">Après avoir choisi le modèle d' **Blazor Server application** dans la boîte de dialogue **créer une application Web ASP.net Core** , sélectionnez **modifier** sous **authentification**.</span><span class="sxs-lookup"><span data-stu-id="ce992-112">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="ce992-113">Une boîte de dialogue s’ouvre pour offrir le même ensemble de mécanismes d’authentification que ceux disponibles pour les autres projets ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="ce992-113">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="ce992-114">**Aucune authentification**</span><span class="sxs-lookup"><span data-stu-id="ce992-114">**No Authentication**</span></span>
* <span data-ttu-id="ce992-115">**Comptes d’utilisateur individuels : les** comptes d’utilisateur peuvent être stockés :</span><span class="sxs-lookup"><span data-stu-id="ce992-115">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="ce992-116">Au sein de l’application à l’aide du système de ASP.NET Core [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="ce992-116">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="ce992-117">Avec [Azure ad B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="ce992-117">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="ce992-118">**Comptes professionnels ou scolaires**</span><span class="sxs-lookup"><span data-stu-id="ce992-118">**Work or School Accounts**</span></span>
* <span data-ttu-id="ce992-119">**Authentification Windows**</span><span class="sxs-lookup"><span data-stu-id="ce992-119">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ce992-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ce992-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ce992-121">Suivez les instructions de Visual Studio Code dans <xref:blazor/tooling> pour créer un nouveau Blazor Server projet avec un mécanisme d’authentification :</span><span class="sxs-lookup"><span data-stu-id="ce992-121">Follow the Visual Studio Code guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="ce992-122">Les valeurs autorisées d’authentification (`{AUTHENTICATION}`) sont présentées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="ce992-122">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="ce992-123">Mécanisme d’authentification</span><span class="sxs-lookup"><span data-stu-id="ce992-123">Authentication mechanism</span></span> | <span data-ttu-id="ce992-124">Description</span><span class="sxs-lookup"><span data-stu-id="ce992-124">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="ce992-125">`None` (valeur par défaut)</span><span class="sxs-lookup"><span data-stu-id="ce992-125">`None` (default)</span></span>         | <span data-ttu-id="ce992-126">Aucune authentification</span><span class="sxs-lookup"><span data-stu-id="ce992-126">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="ce992-127">Utilisateurs stockés dans l’application avec ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="ce992-127">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="ce992-128">Utilisateurs stockés dans [Azure ad B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="ce992-128">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="ce992-129">Authentification d’organisation pour un seul locataire</span><span class="sxs-lookup"><span data-stu-id="ce992-129">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="ce992-130">Authentification d’organisation pour plusieurs locataires</span><span class="sxs-lookup"><span data-stu-id="ce992-130">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="ce992-131">Authentification Windows</span><span class="sxs-lookup"><span data-stu-id="ce992-131">Windows Authentication</span></span> |

<span data-ttu-id="ce992-132">À l’aide de l' `-o|--output` option, la commande utilise la valeur fournie pour l' `{APP NAME}` espace réservé à :</span><span class="sxs-lookup"><span data-stu-id="ce992-132">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="ce992-133">Créez un dossier pour le projet.</span><span class="sxs-lookup"><span data-stu-id="ce992-133">Create a folder for the project.</span></span>
* <span data-ttu-id="ce992-134">Nommez ce projet.</span><span class="sxs-lookup"><span data-stu-id="ce992-134">Name the project.</span></span>

<span data-ttu-id="ce992-135">Pour plus d’informations, consultez la [`dotnet new`](/dotnet/core/tools/dotnet-new) commande dans le guide .net core.</span><span class="sxs-lookup"><span data-stu-id="ce992-135">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ce992-136">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="ce992-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="ce992-137">Suivez les instructions de Visual Studio pour Mac dans <xref:blazor/tooling> .</span><span class="sxs-lookup"><span data-stu-id="ce992-137">Follow the Visual Studio for Mac guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="ce992-138">Dans l’étape **configurer votre nouvelle Blazor Server application** , sélectionnez **authentification individuelle (dans l’application)** dans la liste déroulante **authentification** .</span><span class="sxs-lookup"><span data-stu-id="ce992-138">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="ce992-139">L’application est créée pour les utilisateurs individuels stockés dans l’application avec ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="ce992-139">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="ce992-140">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="ce992-140">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="ce992-141">Créez un Blazor Server projet avec un mécanisme d’authentification à l’aide de la commande suivante dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="ce992-141">Create a new Blazor Server project with an authentication mechanism using the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="ce992-142">Les valeurs autorisées d’authentification (`{AUTHENTICATION}`) sont présentées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="ce992-142">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="ce992-143">Mécanisme d’authentification</span><span class="sxs-lookup"><span data-stu-id="ce992-143">Authentication mechanism</span></span> | <span data-ttu-id="ce992-144">Description</span><span class="sxs-lookup"><span data-stu-id="ce992-144">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="ce992-145">`None` (valeur par défaut)</span><span class="sxs-lookup"><span data-stu-id="ce992-145">`None` (default)</span></span>         | <span data-ttu-id="ce992-146">Aucune authentification</span><span class="sxs-lookup"><span data-stu-id="ce992-146">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="ce992-147">Utilisateurs stockés dans l’application avec ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="ce992-147">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="ce992-148">Utilisateurs stockés dans [Azure ad B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="ce992-148">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="ce992-149">Authentification d’organisation pour un seul locataire</span><span class="sxs-lookup"><span data-stu-id="ce992-149">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="ce992-150">Authentification d’organisation pour plusieurs locataires</span><span class="sxs-lookup"><span data-stu-id="ce992-150">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="ce992-151">Authentification Windows</span><span class="sxs-lookup"><span data-stu-id="ce992-151">Windows Authentication</span></span> |

<span data-ttu-id="ce992-152">À l’aide de l' `-o|--output` option, la commande utilise la valeur fournie pour l' `{APP NAME}` espace réservé à :</span><span class="sxs-lookup"><span data-stu-id="ce992-152">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="ce992-153">Créez un dossier pour le projet.</span><span class="sxs-lookup"><span data-stu-id="ce992-153">Create a folder for the project.</span></span>
* <span data-ttu-id="ce992-154">Nommez ce projet.</span><span class="sxs-lookup"><span data-stu-id="ce992-154">Name the project.</span></span>

<span data-ttu-id="ce992-155">Pour plus d'informations :</span><span class="sxs-lookup"><span data-stu-id="ce992-155">For more information:</span></span>

* <span data-ttu-id="ce992-156">Consultez la [`dotnet new`](/dotnet/core/tools/dotnet-new) commande dans le guide .net core.</span><span class="sxs-lookup"><span data-stu-id="ce992-156">See the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>
* <span data-ttu-id="ce992-157">Exécutez la commande help pour le Blazor Server modèle ( `blazorserver` ) dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="ce992-157">Execute the help command for the Blazor Server template (`blazorserver`) in a command shell:</span></span>

  ```dotnetcli
  dotnet new blazorserver --help
  ```

---

## <a name="scaffold-no-locidentity"></a><span data-ttu-id="ce992-158">Destin Identity</span><span class="sxs-lookup"><span data-stu-id="ce992-158">Scaffold Identity</span></span>

<span data-ttu-id="ce992-159">Génération Identity de modèles automatique dans un Blazor Server projet :</span><span class="sxs-lookup"><span data-stu-id="ce992-159">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="ce992-160">[Sans autorisation existante](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span><span class="sxs-lookup"><span data-stu-id="ce992-160">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="ce992-161">[Avec autorisation](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="ce992-161">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>

## <a name="azure-app-service-on-linux-with-no-locidentity-server"></a><span data-ttu-id="ce992-162">Azure App Service sur Linux avec Identity serveur</span><span class="sxs-lookup"><span data-stu-id="ce992-162">Azure App Service on Linux with Identity Server</span></span>

<span data-ttu-id="ce992-163">Spécifiez l’émetteur de manière explicite lors du déploiement sur Azure App Service sur Linux avec le Identity serveur.</span><span class="sxs-lookup"><span data-stu-id="ce992-163">Specify the issuer explicitly when deploying to Azure App Service on Linux with Identity Server.</span></span> <span data-ttu-id="ce992-164">Pour plus d'informations, consultez <xref:security/authentication/identity/spa#azure-app-service-on-linux>.</span><span class="sxs-lookup"><span data-stu-id="ce992-164">For more information, see <xref:security/authentication/identity/spa#azure-app-service-on-linux>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ce992-165">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="ce992-165">Additional resources</span></span>

* [<span data-ttu-id="ce992-166">Démarrage rapide : Ajouter la connexion avec Microsoft à une application web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ce992-166">Quickstart: Add sign-in with Microsoft to an ASP.NET Core web app</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [<span data-ttu-id="ce992-167">Démarrage rapide : Protéger une API web ASP.NET Core avec la plateforme d’identités Microsoft</span><span class="sxs-lookup"><span data-stu-id="ce992-167">Quickstart: Protect an ASP.NET Core web API with Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-web-api)
* <span data-ttu-id="ce992-168"><xref:host-and-deploy/proxy-load-balancer>: Fournit des conseils sur :</span><span class="sxs-lookup"><span data-stu-id="ce992-168"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="ce992-169">Utilisation de l’intergiciel d’en-têtes transférés pour conserver les informations de schéma HTTPs entre les serveurs proxy et les réseaux internes.</span><span class="sxs-lookup"><span data-stu-id="ce992-169">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="ce992-170">Scénarios et cas d’utilisation supplémentaires, y compris la configuration manuelle du schéma, les modifications du chemin des demandes pour le routage des demandes correct et le transfert du modèle de demande pour les proxys inversés Linux et non-IIS.</span><span class="sxs-lookup"><span data-stu-id="ce992-170">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>
