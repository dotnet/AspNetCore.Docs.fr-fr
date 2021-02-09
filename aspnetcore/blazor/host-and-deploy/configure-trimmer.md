---
title: Configurer le massicot pour ASP.NET Core Blazor
author: guardrex
description: Découvrez comment contrôler l’éditeur de liens en langage intermédiaire (massicot) lors de la création d’une Blazor application.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/08/2021
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
uid: blazor/host-and-deploy/configure-trimmer
ms.openlocfilehash: 41887638f13a08d375075e8377da19d1d0098c4b
ms.sourcegitcommit: ef8d8c79993a6608bf597ad036edcf30b231843f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975211"
---
# <a name="configure-the-trimmer-for-aspnet-core-blazor"></a><span data-ttu-id="95b62-103">Configurer le massicot pour ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="95b62-103">Configure the Trimmer for ASP.NET Core Blazor</span></span>

<span data-ttu-id="95b62-104">Blazor WebAssembly effectue un découpage en [langage intermédiaire (il)](/dotnet/standard/managed-code#intermediate-language--execution) pour réduire la taille de la sortie publiée.</span><span class="sxs-lookup"><span data-stu-id="95b62-104">Blazor WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) trimming to reduce the size of the published output.</span></span> <span data-ttu-id="95b62-105">Par défaut, la suppression se produit lors de la publication d’une application.</span><span class="sxs-lookup"><span data-stu-id="95b62-105">By default, trimming occurs when publishing an app.</span></span>

<span data-ttu-id="95b62-106">Le découpage peut avoir des effets néfastes.</span><span class="sxs-lookup"><span data-stu-id="95b62-106">Trimming may have detrimental effects.</span></span> <span data-ttu-id="95b62-107">Dans les applications qui utilisent la réflexion, le massicot ne peut souvent pas déterminer les types requis pour la réflexion au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="95b62-107">In apps that use reflection, the Trimmer often can't determine the required types for reflection at runtime.</span></span> <span data-ttu-id="95b62-108">Pour supprimer les applications qui utilisent la réflexion, le massicot doit être informé des types requis pour la réflexion dans le code de l’application et dans les packages ou infrastructures dont dépend l’application.</span><span class="sxs-lookup"><span data-stu-id="95b62-108">To trim apps that use reflection, the Trimmer must be informed about required types for reflection in both the app's code and in the packages or frameworks that the app depends on.</span></span> <span data-ttu-id="95b62-109">Le massicot ne peut pas non plus réagir au comportement dynamique d’une application au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="95b62-109">The Trimmer is also unable to react to an app's dynamic behavior at runtime.</span></span> <span data-ttu-id="95b62-110">Pour garantir le bon fonctionnement de l’application tronquée une fois déployée, testez fréquemment la sortie publiée lors du développement.</span><span class="sxs-lookup"><span data-stu-id="95b62-110">To ensure the trimmed app works correctly once deployed, test published output frequently while developing.</span></span>

<span data-ttu-id="95b62-111">Pour configurer le massicot, consultez l’article sur les [options de suppression](/dotnet/core/deploying/trimming-options) dans la documentation sur les notions de base de .net, qui comprend des conseils sur les sujets suivants :</span><span class="sxs-lookup"><span data-stu-id="95b62-111">To configure the Trimmer, see the [Trimming options](/dotnet/core/deploying/trimming-options) article in the .NET Fundamentals documentation, which includes guidance on the following subjects:</span></span>

* <span data-ttu-id="95b62-112">Désactivez le filtrage pour l’application entière avec la `<PublishTrimmed>` propriété dans le fichier projet.</span><span class="sxs-lookup"><span data-stu-id="95b62-112">Disable trimming for the entire app with the `<PublishTrimmed>` property in the project file.</span></span>
* <span data-ttu-id="95b62-113">Contrôler la façon dont le langage intermédiaire inutilisé est supprimé par le massicot.</span><span class="sxs-lookup"><span data-stu-id="95b62-113">Control how aggressively unused IL is discarded by the Trimmer.</span></span>
* <span data-ttu-id="95b62-114">Arrêtez le massicot pour découper des assemblys spécifiques.</span><span class="sxs-lookup"><span data-stu-id="95b62-114">Stop the Trimmer from trimming specific assemblies.</span></span>
* <span data-ttu-id="95b62-115">Assemblys « racine » à découper.</span><span class="sxs-lookup"><span data-stu-id="95b62-115">"Root" assemblies for trimming.</span></span>
* <span data-ttu-id="95b62-116">Avertissements de surface pour les types réfléchis en affectant à la propriété la valeur `<SuppressTrimAnalysisWarnings>` `false` dans le fichier projet.</span><span class="sxs-lookup"><span data-stu-id="95b62-116">Surface warnings for reflected types by setting the `<SuppressTrimAnalysisWarnings>` property to `false` in the project file.</span></span>
* <span data-ttu-id="95b62-117">Contrôle du découpage des symboles et de la prise en charge degugger.</span><span class="sxs-lookup"><span data-stu-id="95b62-117">Control symbol trimming and degugger support.</span></span>
* <span data-ttu-id="95b62-118">Définissez les fonctionnalités du massicot pour la suppression des fonctionnalités de la bibliothèque d’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="95b62-118">Set Trimmer features for trimming framework library features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="95b62-119">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="95b62-119">Additional resources</span></span>

* [<span data-ttu-id="95b62-120">Supprimer les exécutables et les déploiements autonomes</span><span class="sxs-lookup"><span data-stu-id="95b62-120">Trim self-contained deployments and executables</span></span>](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>
