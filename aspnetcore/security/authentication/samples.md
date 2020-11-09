---
title: Exemples d’authentification pour ASP.NET Core
author: rick-anderson
description: Fournit des liens vers les exemples d’authentification dans le référentiel ASP.NET Core.
ms.author: riande
ms.date: 01/31/2019
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
uid: security/authentication/samples
ms.openlocfilehash: 4153a443748dbff40be19e25fc1c719ee4e39609
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060337"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="d0901-103">Exemples d’authentification pour ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d0901-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="d0901-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d0901-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d0901-105">Le [référentiel ASP.net Core](https://github.com/dotnet/AspNetCore) contient les exemples d’authentification suivants dans le dossier *AspNetCore/SRC/Security/Samples* :</span><span class="sxs-lookup"><span data-stu-id="d0901-105">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="d0901-106">Transformation de revendications</span><span class="sxs-lookup"><span data-stu-id="d0901-106">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* <span data-ttu-id="d0901-107">[Cookie identification](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)</span><span class="sxs-lookup"><span data-stu-id="d0901-107">[Cookie authentication](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)</span></span>
* [<span data-ttu-id="d0901-108">Fournisseur de stratégie personnalisée-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="d0901-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="d0901-109">Options et schémas d’authentification dynamique</span><span class="sxs-lookup"><span data-stu-id="d0901-109">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="d0901-110">[Revendications externes](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="d0901-110">[External claims](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="d0901-111">Sélection de cookie l’un ou l’autre schéma d’authentification en fonction de la demande</span><span class="sxs-lookup"><span data-stu-id="d0901-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="d0901-112">Restreint l’accès aux fichiers statiques</span><span class="sxs-lookup"><span data-stu-id="d0901-112">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="d0901-113">Exécuter des exemples</span><span class="sxs-lookup"><span data-stu-id="d0901-113">Run the samples</span></span>

* <span data-ttu-id="d0901-114">Sélectionnez une [branche](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="d0901-114">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="d0901-115">Par exemple : `release/3.1`</span><span class="sxs-lookup"><span data-stu-id="d0901-115">For example, `release/3.1`</span></span>
* <span data-ttu-id="d0901-116">Clonez ou téléchargez le [référentiel ASP.net Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="d0901-116">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="d0901-117">Vérifiez que vous avez installé la version de [Kit SDK .net Core](https://dotnet.microsoft.com/download/dotnet-core) qui correspond au clone du référentiel ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="d0901-117">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="d0901-118">Accédez à un exemple dans *AspNetCore/SRC/Security/Samples* et exécutez l’exemple avec `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="d0901-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d0901-119">Le [référentiel ASP.net Core](https://github.com/dotnet/AspNetCore) contient les exemples d’authentification suivants dans le dossier *AspNetCore/SRC/Security/Samples* :</span><span class="sxs-lookup"><span data-stu-id="d0901-119">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="d0901-120">Transformation de revendications</span><span class="sxs-lookup"><span data-stu-id="d0901-120">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/ClaimsTransformation)
* <span data-ttu-id="d0901-121">[Cookie identification](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Cookies)</span><span class="sxs-lookup"><span data-stu-id="d0901-121">[Cookie authentication](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Cookies)</span></span>
* [<span data-ttu-id="d0901-122">Fournisseur de stratégie personnalisée-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="d0901-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="d0901-123">Options et schémas d’authentification dynamique</span><span class="sxs-lookup"><span data-stu-id="d0901-123">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="d0901-124">[Revendications externes](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="d0901-124">[External claims](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="d0901-125">Sélection de cookie l’un ou l’autre schéma d’authentification en fonction de la demande</span><span class="sxs-lookup"><span data-stu-id="d0901-125">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="d0901-126">Restreint l’accès aux fichiers statiques</span><span class="sxs-lookup"><span data-stu-id="d0901-126">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="d0901-127">Exécuter des exemples</span><span class="sxs-lookup"><span data-stu-id="d0901-127">Run the samples</span></span>

* <span data-ttu-id="d0901-128">Sélectionnez une [branche](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="d0901-128">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="d0901-129">Par exemple : `release/2.1`</span><span class="sxs-lookup"><span data-stu-id="d0901-129">For example, `release/2.1`</span></span>
* <span data-ttu-id="d0901-130">Clonez ou téléchargez le [référentiel ASP.net Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="d0901-130">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="d0901-131">Vérifiez que vous avez installé la version de [Kit SDK .net Core](https://dotnet.microsoft.com/download/dotnet-core) qui correspond au clone du référentiel ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="d0901-131">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="d0901-132">Accédez à un exemple dans *AspNetCore/SRC/Security/Samples* et exécutez l’exemple avec `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="d0901-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end
