---
title: 'Configurer le massicot pour ASP.NET Core :::no-loc(Blazor):::'
author: guardrex
description: 'Découvrez comment contrôler l’éditeur de liens en langage intermédiaire (massicot) lors de la création d’une :::no-loc(Blazor)::: application.'
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/14/2020
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
uid: blazor/host-and-deploy/configure-trimmer
ms.openlocfilehash: 337b188d3c0aeac9c5c635ebca265b9a35c6904d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055800"
---
# <a name="configure-the-trimmer-for-aspnet-core-no-locblazor"></a><span data-ttu-id="b70b5-103">Configurer le massicot pour ASP.NET Core :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="b70b5-103">Configure the Trimmer for ASP.NET Core :::no-loc(Blazor):::</span></span>

<span data-ttu-id="b70b5-104">Par [Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="b70b5-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="b70b5-105">:::no-loc(Blazor WebAssembly)::: effectue un découpage en [langage intermédiaire (il)](/dotnet/standard/managed-code#intermediate-language--execution) pour réduire la taille de la sortie publiée.</span><span class="sxs-lookup"><span data-stu-id="b70b5-105">:::no-loc(Blazor WebAssembly)::: performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) trimming to reduce the size of the published output.</span></span>

<span data-ttu-id="b70b5-106">Le découpage d’une application optimise sa taille, mais peut avoir des effets néfastes.</span><span class="sxs-lookup"><span data-stu-id="b70b5-106">Trimming an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="b70b5-107">Les applications qui utilisent la réflexion ou les fonctionnalités dynamiques associées peuvent s’arrêter quand elles sont tronquées, car le massicot n’a pas connaissance du comportement dynamique et ne peut pas déterminer en général les types requis pour la réflexion au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="b70b5-107">Apps that use reflection or related dynamic features may break when trimmed because the trimmer doesn't know about dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="b70b5-108">Pour supprimer de telles applications, le massicot doit être informé des types requis par la réflexion dans le code et dans les packages ou infrastructures dont dépend l’application.</span><span class="sxs-lookup"><span data-stu-id="b70b5-108">To trim such apps, the trimmer must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span>

<span data-ttu-id="b70b5-109">Pour garantir le bon fonctionnement de l’application tronquée une fois déployée, il est important de tester fréquemment la sortie publiée lors du développement.</span><span class="sxs-lookup"><span data-stu-id="b70b5-109">To ensure the trimmed app works correctly once deployed, it's important to test published output frequently while developing.</span></span>

<span data-ttu-id="b70b5-110">Le filtrage pour les applications .NET peut être désactivé en affectant `PublishTrimmed` à la propriété MSBuild la valeur `false` dans le fichier projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="b70b5-110">Trimming for .NET apps can be disabled by setting the `PublishTrimmed` MSBuild property to `false` in the app's project file:</span></span>

```xml
<PropertyGroup>
  <PublishTrimmed>false</PublishTrimmed>
</PropertyGroup>
```
<span data-ttu-id="b70b5-111">Vous trouverez des options supplémentaires pour configurer le massicot dans [options de suppression](/dotnet/core/deploying/trimming-options).</span><span class="sxs-lookup"><span data-stu-id="b70b5-111">Additional options to configure the trimmer can be found at [Trimming options](/dotnet/core/deploying/trimming-options).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b70b5-112">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="b70b5-112">Additional resources</span></span>

* [<span data-ttu-id="b70b5-113">Supprimer les exécutables et les déploiements autonomes</span><span class="sxs-lookup"><span data-stu-id="b70b5-113">Trim self-contained deployments and executables</span></span>](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>
