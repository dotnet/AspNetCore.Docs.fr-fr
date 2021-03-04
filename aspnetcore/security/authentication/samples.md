---
title: Exemples d’authentification pour ASP.NET Core
author: rick-anderson
description: Fournit des liens vers les exemples d’authentification dans le référentiel ASP.NET Core.
ms.author: riande
ms.date: 02/21/2021
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
uid: security/authentication/samples
ms.openlocfilehash: e7fb2ac32f57cf4ecd3c5db294bd0df8e186b6c6
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102110116"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="c51ff-103">Exemples d’authentification pour ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c51ff-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="c51ff-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c51ff-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c51ff-105">Le [référentiel ASP.net Core](https://github.com/dotnet/aspnetcore) contient les exemples d’authentification suivants ( `main` branche) :</span><span class="sxs-lookup"><span data-stu-id="c51ff-105">The [ASP.NET Core repository](https://github.com/dotnet/aspnetcore) contains the following authentication samples (`main` branch):</span></span>

* [<span data-ttu-id="c51ff-106">Transformation de revendications</span><span class="sxs-lookup"><span data-stu-id="c51ff-106">Claims transformation</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/ClaimsTransformation)
* <span data-ttu-id="c51ff-107">[Cookie identification](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/Cookies)</span><span class="sxs-lookup"><span data-stu-id="c51ff-107">[Cookie authentication](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/Cookies)</span></span>
* [<span data-ttu-id="c51ff-108">Réponse d’échec d’autorisation personnalisée</span><span class="sxs-lookup"><span data-stu-id="c51ff-108">Custom authorization failure response</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/CustomAuthorizationFailureResponse)
* [<span data-ttu-id="c51ff-109">Fournisseur de stratégie personnalisée-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="c51ff-109">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="c51ff-110">Options et schémas d’authentification dynamique</span><span class="sxs-lookup"><span data-stu-id="c51ff-110">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="c51ff-111">[Revendications externes](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="c51ff-111">[External claims](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="c51ff-112">Sélection de cookie l’un ou l’autre schéma d’authentification en fonction de la demande</span><span class="sxs-lookup"><span data-stu-id="c51ff-112">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="c51ff-113">Restreint l’accès aux fichiers statiques</span><span class="sxs-lookup"><span data-stu-id="c51ff-113">Restricts access to static files</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/StaticFilesAuth)

## <a name="obtain-and-run-the-samples"></a><span data-ttu-id="c51ff-114">Obtenir et exécuter les exemples</span><span class="sxs-lookup"><span data-stu-id="c51ff-114">Obtain and run the samples</span></span>

<span data-ttu-id="c51ff-115">Les exemples de liens fournis dans cet article fournissent des exemples pour la prochaine version de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c51ff-115">The sample links provided in this article provide samples for the upcoming release of ASP.NET Core.</span></span> <span data-ttu-id="c51ff-116">Pour obtenir un exemple pour la version actuelle ou une version antérieure, procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="c51ff-116">To obtain a sample for the current release or a prior release, perform the following steps:</span></span>

* <span data-ttu-id="c51ff-117">Sélectionnez la branche de version du [référentiel ASP.net Core](https://github.com/dotnet/aspnetcore)] ( https://github.com/dotnet/aspnetcore) .</span><span class="sxs-lookup"><span data-stu-id="c51ff-117">Select the release branch of the [ASP.NET Core repository](https://github.com/dotnet/aspnetcore)](https://github.com/dotnet/aspnetcore).</span></span> <span data-ttu-id="c51ff-118">Par exemple, la `release/5.0` branche contient les exemples pour la version 5,0 de ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="c51ff-118">For example, the `release/5.0` branch contains the samples for the ASP.NET Core 5.0 release.</span></span>
* <span data-ttu-id="c51ff-119">Clonez ou téléchargez le référentiel ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c51ff-119">Clone or download the ASP.NET Core repository.</span></span>
* <span data-ttu-id="c51ff-120">Sur votre système local, vérifiez que l’installation de la version de [Kit SDK .net Core](https://dotnet.microsoft.com/download/dotnet-core) correspond au clone du référentiel ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="c51ff-120">On your local system, verify installation of the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="c51ff-121">Accédez à un exemple dans le `aspnetcore/src/Security/samples` dossier et exécutez l’exemple avec la [ `dotnet run` commande](/dotnet/core/tools/dotnet-run).</span><span class="sxs-lookup"><span data-stu-id="c51ff-121">Navigate to a sample in `aspnetcore/src/Security/samples` folder and run the sample with the [`dotnet run` command](/dotnet/core/tools/dotnet-run).</span></span>
