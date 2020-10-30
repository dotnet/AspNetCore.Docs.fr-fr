---
title: 'Sécuriser les :::no-loc(Blazor Server)::: applications ASP.net Core'
author: guardrex
description: 'Découvrez comment sécuriser des applications :::no-loc(Blazor Server)::: en tant qu’applications ASP.net core.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
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
uid: blazor/security/server/index
ms.openlocfilehash: 108fb3a8a24295cad43fd8c83303abd95a7ecd33
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055475"
---
# <a name="secure-aspnet-core-no-locblazor-server-apps"></a><span data-ttu-id="2cbfc-103">Sécuriser les :::no-loc(Blazor Server)::: applications ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="2cbfc-103">Secure ASP.NET Core :::no-loc(Blazor Server)::: apps</span></span>

<span data-ttu-id="2cbfc-104">Par [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2cbfc-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="2cbfc-105">:::no-loc(Blazor Server)::: les applications sont configurées pour la sécurité de la même façon que les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2cbfc-105">:::no-loc(Blazor Server)::: apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="2cbfc-106">Pour plus d’informations, consultez les articles sous <xref:security/index> .</span><span class="sxs-lookup"><span data-stu-id="2cbfc-106">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="2cbfc-107">Les rubriques de cette vue d’ensemble s’appliquent spécifiquement à :::no-loc(Blazor Server)::: .</span><span class="sxs-lookup"><span data-stu-id="2cbfc-107">Topics under this overview apply specifically to :::no-loc(Blazor Server):::.</span></span>

## <a name="no-locblazor-server-project-template"></a><span data-ttu-id="2cbfc-108">:::no-loc(Blazor Server)::: modèle de projet</span><span class="sxs-lookup"><span data-stu-id="2cbfc-108">:::no-loc(Blazor Server)::: project template</span></span>

<span data-ttu-id="2cbfc-109">Le :::no-loc(Blazor Server)::: modèle de projet peut être configuré pour l’authentification lors de la création du projet.</span><span class="sxs-lookup"><span data-stu-id="2cbfc-109">The :::no-loc(Blazor Server)::: project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2cbfc-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2cbfc-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2cbfc-111">Suivez les instructions de Visual Studio dans <xref:blazor/tooling> pour créer un nouveau :::no-loc(Blazor Server)::: projet avec un mécanisme d’authentification.</span><span class="sxs-lookup"><span data-stu-id="2cbfc-111">Follow the Visual Studio guidance in <xref:blazor/tooling> to create a new :::no-loc(Blazor Server)::: project with an authentication mechanism.</span></span>

<span data-ttu-id="2cbfc-112">Après avoir choisi le modèle d' **:::no-loc(Blazor Server)::: application** dans la boîte de dialogue **créer une application Web ASP.net Core** , sélectionnez **modifier** sous **authentification** .</span><span class="sxs-lookup"><span data-stu-id="2cbfc-112">After choosing the **:::no-loc(Blazor Server)::: App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication** .</span></span>

<span data-ttu-id="2cbfc-113">Une boîte de dialogue s’ouvre pour offrir le même ensemble de mécanismes d’authentification que ceux disponibles pour les autres projets ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="2cbfc-113">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="2cbfc-114">**Aucune authentification**</span><span class="sxs-lookup"><span data-stu-id="2cbfc-114">**No Authentication**</span></span>
* <span data-ttu-id="2cbfc-115">**Comptes d’utilisateur individuels : les** comptes d’utilisateur peuvent être stockés :</span><span class="sxs-lookup"><span data-stu-id="2cbfc-115">**Individual User Accounts** : User accounts can be stored:</span></span>
  * <span data-ttu-id="2cbfc-116">Au sein de l’application à l’aide du système de ASP.NET Core [:::no-loc(Identity):::](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="2cbfc-116">Within the app using ASP.NET Core's [:::no-loc(Identity):::](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="2cbfc-117">Avec [Azure ad B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="2cbfc-117">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="2cbfc-118">**Comptes professionnels ou scolaires**</span><span class="sxs-lookup"><span data-stu-id="2cbfc-118">**Work or School Accounts**</span></span>
* <span data-ttu-id="2cbfc-119">**Authentification Windows**</span><span class="sxs-lookup"><span data-stu-id="2cbfc-119">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2cbfc-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2cbfc-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2cbfc-121">Suivez les instructions de Visual Studio Code dans <xref:blazor/tooling> pour créer un nouveau :::no-loc(Blazor Server)::: projet avec un mécanisme d’authentification :</span><span class="sxs-lookup"><span data-stu-id="2cbfc-121">Follow the Visual Studio Code guidance in <xref:blazor/tooling> to create a new :::no-loc(Blazor Server)::: project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="2cbfc-122">Les valeurs autorisées d’authentification (`{AUTHENTICATION}`) sont présentées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="2cbfc-122">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="2cbfc-123">Mécanisme d’authentification</span><span class="sxs-lookup"><span data-stu-id="2cbfc-123">Authentication mechanism</span></span> | <span data-ttu-id="2cbfc-124">Description</span><span class="sxs-lookup"><span data-stu-id="2cbfc-124">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="2cbfc-125">`None` (valeur par défaut)</span><span class="sxs-lookup"><span data-stu-id="2cbfc-125">`None` (default)</span></span>         | <span data-ttu-id="2cbfc-126">Aucune authentification</span><span class="sxs-lookup"><span data-stu-id="2cbfc-126">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="2cbfc-127">Utilisateurs stockés dans l’application avec :::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="2cbfc-127">Users stored in the app with :::no-loc(ASP.NET Core Identity):::</span></span> |
| `IndividualB2C`          | <span data-ttu-id="2cbfc-128">Utilisateurs stockés dans [Azure ad B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="2cbfc-128">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="2cbfc-129">Authentification d’organisation pour un seul locataire</span><span class="sxs-lookup"><span data-stu-id="2cbfc-129">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="2cbfc-130">Authentification d’organisation pour plusieurs locataires</span><span class="sxs-lookup"><span data-stu-id="2cbfc-130">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="2cbfc-131">Authentification Windows</span><span class="sxs-lookup"><span data-stu-id="2cbfc-131">Windows Authentication</span></span> |

<span data-ttu-id="2cbfc-132">À l’aide de l' `-o|--output` option, la commande utilise la valeur fournie pour l' `{APP NAME}` espace réservé à :</span><span class="sxs-lookup"><span data-stu-id="2cbfc-132">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="2cbfc-133">Créez un dossier pour le projet.</span><span class="sxs-lookup"><span data-stu-id="2cbfc-133">Create a folder for the project.</span></span>
* <span data-ttu-id="2cbfc-134">Nommez ce projet.</span><span class="sxs-lookup"><span data-stu-id="2cbfc-134">Name the project.</span></span>

<span data-ttu-id="2cbfc-135">Pour plus d’informations, consultez la [`dotnet new`](/dotnet/core/tools/dotnet-new) commande dans le guide .net core.</span><span class="sxs-lookup"><span data-stu-id="2cbfc-135">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2cbfc-136">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="2cbfc-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="2cbfc-137">Suivez les instructions de Visual Studio pour Mac dans <xref:blazor/tooling> .</span><span class="sxs-lookup"><span data-stu-id="2cbfc-137">Follow the Visual Studio for Mac guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="2cbfc-138">Dans l’étape **configurer votre nouvelle :::no-loc(Blazor Server)::: application** , sélectionnez **authentification individuelle (dans l’application)** dans la liste déroulante **authentification** .</span><span class="sxs-lookup"><span data-stu-id="2cbfc-138">On the **Configure your new :::no-loc(Blazor Server)::: App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="2cbfc-139">L’application est créée pour les utilisateurs individuels stockés dans l’application avec :::no-loc(ASP.NET Core Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="2cbfc-139">The app is created for individual users stored in the app with :::no-loc(ASP.NET Core Identity):::.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="2cbfc-140">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="2cbfc-140">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="2cbfc-141">Créez un :::no-loc(Blazor Server)::: projet avec un mécanisme d’authentification à l’aide de la commande suivante dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="2cbfc-141">Create a new :::no-loc(Blazor Server)::: project with an authentication mechanism using the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="2cbfc-142">Les valeurs autorisées d’authentification (`{AUTHENTICATION}`) sont présentées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="2cbfc-142">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="2cbfc-143">Mécanisme d’authentification</span><span class="sxs-lookup"><span data-stu-id="2cbfc-143">Authentication mechanism</span></span> | <span data-ttu-id="2cbfc-144">Description</span><span class="sxs-lookup"><span data-stu-id="2cbfc-144">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="2cbfc-145">`None` (valeur par défaut)</span><span class="sxs-lookup"><span data-stu-id="2cbfc-145">`None` (default)</span></span>         | <span data-ttu-id="2cbfc-146">Aucune authentification</span><span class="sxs-lookup"><span data-stu-id="2cbfc-146">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="2cbfc-147">Utilisateurs stockés dans l’application avec :::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="2cbfc-147">Users stored in the app with :::no-loc(ASP.NET Core Identity):::</span></span> |
| `IndividualB2C`          | <span data-ttu-id="2cbfc-148">Utilisateurs stockés dans [Azure ad B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="2cbfc-148">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="2cbfc-149">Authentification d’organisation pour un seul locataire</span><span class="sxs-lookup"><span data-stu-id="2cbfc-149">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="2cbfc-150">Authentification d’organisation pour plusieurs locataires</span><span class="sxs-lookup"><span data-stu-id="2cbfc-150">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="2cbfc-151">Authentification Windows</span><span class="sxs-lookup"><span data-stu-id="2cbfc-151">Windows Authentication</span></span> |

<span data-ttu-id="2cbfc-152">À l’aide de l' `-o|--output` option, la commande utilise la valeur fournie pour l' `{APP NAME}` espace réservé à :</span><span class="sxs-lookup"><span data-stu-id="2cbfc-152">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="2cbfc-153">Créez un dossier pour le projet.</span><span class="sxs-lookup"><span data-stu-id="2cbfc-153">Create a folder for the project.</span></span>
* <span data-ttu-id="2cbfc-154">Nommez ce projet.</span><span class="sxs-lookup"><span data-stu-id="2cbfc-154">Name the project.</span></span>

<span data-ttu-id="2cbfc-155">Pour plus d'informations :</span><span class="sxs-lookup"><span data-stu-id="2cbfc-155">For more information:</span></span>

* <span data-ttu-id="2cbfc-156">Consultez la [`dotnet new`](/dotnet/core/tools/dotnet-new) commande dans le guide .net core.</span><span class="sxs-lookup"><span data-stu-id="2cbfc-156">See the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>
* <span data-ttu-id="2cbfc-157">Exécutez la commande help pour le :::no-loc(Blazor Server)::: modèle ( `blazorserver` ) dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="2cbfc-157">Execute the help command for the :::no-loc(Blazor Server)::: template (`blazorserver`) in a command shell:</span></span>

  ```dotnetcli
  dotnet new blazorserver --help
  ```

---

## <a name="scaffold-no-locidentity"></a><span data-ttu-id="2cbfc-158">Destin :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="2cbfc-158">Scaffold :::no-loc(Identity):::</span></span>

<span data-ttu-id="2cbfc-159">Génération :::no-loc(Identity)::: de modèles automatique dans un :::no-loc(Blazor Server)::: projet :</span><span class="sxs-lookup"><span data-stu-id="2cbfc-159">Scaffold :::no-loc(Identity)::: into a :::no-loc(Blazor Server)::: project:</span></span>

* <span data-ttu-id="2cbfc-160">[Sans autorisation existante](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span><span class="sxs-lookup"><span data-stu-id="2cbfc-160">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="2cbfc-161">[Avec autorisation](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="2cbfc-161">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2cbfc-162">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="2cbfc-162">Additional resources</span></span>

* [<span data-ttu-id="2cbfc-163">Démarrage rapide : Ajouter la connexion avec Microsoft à une application web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2cbfc-163">Quickstart: Add sign-in with Microsoft to an ASP.NET Core web app</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [<span data-ttu-id="2cbfc-164">Démarrage rapide : Protéger une API web ASP.NET Core avec la plateforme d’identités Microsoft</span><span class="sxs-lookup"><span data-stu-id="2cbfc-164">Quickstart: Protect an ASP.NET Core web API with Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-web-api)
