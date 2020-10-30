---
title: Présentation d’ASP.NET Core
author: rick-anderson
description: Apprenez-en plus sur ASP.NET Core, une infrastructure multiplateforme, hautes performances et open source pour la création d’applications modernes, basées sur le Cloud et connectées à Internet.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
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
uid: index
ms.openlocfilehash: 4301e0d59364573767ab4cae25a4818ff84b9abc
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052225"
---
# <a name="introduction-to-aspnet-core"></a><span data-ttu-id="e7728-103">Présentation d’ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e7728-103">Introduction to ASP.NET Core</span></span>

<span data-ttu-id="e7728-104">Par [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT) et [Shaun Luttin](https://twitter.com/dicshaunary)</span><span class="sxs-lookup"><span data-stu-id="e7728-104">By [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Shaun Luttin](https://twitter.com/dicshaunary)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e7728-105">ASP.NET Core est une infrastructure multiplateforme, à hautes performances et [Open source](https://github.com/dotnet/aspnetcore) pour la création d’applications modernes, basées sur le Cloud et connectées à Internet.</span><span class="sxs-lookup"><span data-stu-id="e7728-105">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/dotnet/aspnetcore) framework for building modern, cloud-enabled, Internet-connected apps.</span></span> <span data-ttu-id="e7728-106">Avec ASP.NET Core, vous pouvez :</span><span class="sxs-lookup"><span data-stu-id="e7728-106">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="e7728-107">Créez des applications et services Web, des applications d' [Internet des objets (IOT)](https://www.microsoft.com/internet-of-things/) et des serveurs principaux mobiles.</span><span class="sxs-lookup"><span data-stu-id="e7728-107">Build web apps and services, [Internet of Things (IoT)](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="e7728-108">Utiliser vos outils de développement préférés sur Windows, macOS et Linux.</span><span class="sxs-lookup"><span data-stu-id="e7728-108">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="e7728-109">Déployer dans le cloud ou localement.</span><span class="sxs-lookup"><span data-stu-id="e7728-109">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="e7728-110">Exécutez sur [.net Core](/dotnet/core/introduction).</span><span class="sxs-lookup"><span data-stu-id="e7728-110">Run on [.NET Core](/dotnet/core/introduction).</span></span>

## <a name="why-choose-aspnet-core"></a><span data-ttu-id="e7728-111">Pourquoi utiliser ASP.NET Core ?</span><span class="sxs-lookup"><span data-stu-id="e7728-111">Why choose ASP.NET Core?</span></span>

<span data-ttu-id="e7728-112">Des millions de développeurs utilisent ou ont utilisé [ASP.net 4. x](/aspnet/overview) pour créer des applications Web.</span><span class="sxs-lookup"><span data-stu-id="e7728-112">Millions of developers use or have used [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="e7728-113">ASP.NET Core est une nouvelle conception de ASP.NET 4. x, y compris des modifications architecturales qui se traduisent par un Framework plus léger et plus modulaire.</span><span class="sxs-lookup"><span data-stu-id="e7728-113">ASP.NET Core is a redesign of ASP.NET 4.x, including architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="e7728-114">Créer des API web et une interface utilisateur web en utilisant le modèle MVC d’ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e7728-114">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="e7728-115">Le modèle MVC d’ASP.NET Core fournit des fonctionnalités pour créer des [API web](xref:tutorials/first-web-api) et des [applications web](xref:tutorials/razor-pages/index) :</span><span class="sxs-lookup"><span data-stu-id="e7728-115">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/first-web-api) and [web apps](xref:tutorials/razor-pages/index):</span></span>

* <span data-ttu-id="e7728-116">Le [modèle MVC (Modèle-Vue-Contrôleur)](xref:mvc/overview) permet de rendre vos API web et vos applications web testables.</span><span class="sxs-lookup"><span data-stu-id="e7728-116">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps testable.</span></span>
* <span data-ttu-id="e7728-117">[ :::no-loc(Razor)::: Pages](xref:razor-pages/index) est un modèle de programmation basé sur des pages qui rend la création d’une interface utilisateur Web plus simple et plus productive.</span><span class="sxs-lookup"><span data-stu-id="e7728-117">[:::no-loc(Razor)::: Pages](xref:razor-pages/index) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="e7728-118">le [ :::no-loc(Razor)::: balisage](xref:mvc/views/razor) fournit une syntaxe productive pour les [ :::no-loc(Razor)::: pages](xref:razor-pages/index) et les [vues MVC](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="e7728-118">[:::no-loc(Razor)::: markup](xref:mvc/views/razor) provides a productive syntax for [:::no-loc(Razor)::: Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="e7728-119">Les [tag helpers](xref:mvc/views/tag-helpers/intro) permettent au code côté serveur de participer à la création et au rendu des éléments HTML dans les :::no-loc(Razor)::: fichiers.</span><span class="sxs-lookup"><span data-stu-id="e7728-119">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in :::no-loc(Razor)::: files.</span></span>
* <span data-ttu-id="e7728-120">La prise en charge intégrée de [plusieurs formats de données et de la négociation de contenu](xref:web-api/advanced/formatting) permet à vos API web d’être utilisées par un large éventail de clients, notamment des navigateurs et des appareils mobiles.</span><span class="sxs-lookup"><span data-stu-id="e7728-120">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="e7728-121">Le principe de la [liaison de modèle](xref:mvc/models/model-binding) permet le mappage automatiquement des données des requêtes HTTP aux paramètres des méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="e7728-121">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="e7728-122">La [validation de modèle](xref:mvc/models/validation) effectue automatiquement la validation côté client et côté serveur.</span><span class="sxs-lookup"><span data-stu-id="e7728-122">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="e7728-123">Développement côté client</span><span class="sxs-lookup"><span data-stu-id="e7728-123">Client-side development</span></span>

<span data-ttu-id="e7728-124">ASP.NET Core s’intègre de façon transparente aux infrastructures et bibliothèques courantes côté client, notamment [:::no-loc(Blazor):::](xref:blazor/index) , [angulaires](xref:spa/angular), [réactifs](xref:spa/react)et [bootstrap](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="e7728-124">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [:::no-loc(Blazor):::](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="e7728-125">Pour plus d’informations, consultez <xref:blazor/index> et les rubriques connexes sous *Développement côté client* .</span><span class="sxs-lookup"><span data-stu-id="e7728-125">For more information, see <xref:blazor/index> and related topics under *Client-side development* .</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-target-frameworks"></a><span data-ttu-id="e7728-126">ASP.NET Core frameworks cibles</span><span class="sxs-lookup"><span data-stu-id="e7728-126">ASP.NET Core target frameworks</span></span>

<span data-ttu-id="e7728-127">ASP.NET Core 3. x et versions ultérieures peuvent uniquement cibler .NET Core.</span><span class="sxs-lookup"><span data-stu-id="e7728-127">ASP.NET Core 3.x and later can only target .NET Core.</span></span> <span data-ttu-id="e7728-128">En règle générale, ASP.NET Core se compose de bibliothèques [.NET standard](/dotnet/standard/net-standard) .</span><span class="sxs-lookup"><span data-stu-id="e7728-128">Generally, ASP.NET Core is composed of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="e7728-129">Les bibliothèques écrites avec .NET Standard 2.0 s’exécutent sur [n’importe quelle plateforme .NET qui implémente .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span><span class="sxs-lookup"><span data-stu-id="e7728-129">Libraries written with .NET Standard 2.0 run on any [.NET platform that implements .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span></span>

<span data-ttu-id="e7728-130">Le ciblage de .NET Core présente plusieurs avantages, qui sont plus nombreux à chaque version.</span><span class="sxs-lookup"><span data-stu-id="e7728-130">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="e7728-131">Voici certains avantages de .NET Core par rapport à .NET Framework :</span><span class="sxs-lookup"><span data-stu-id="e7728-131">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="e7728-132">Multiplateforme.</span><span class="sxs-lookup"><span data-stu-id="e7728-132">Cross-platform.</span></span> <span data-ttu-id="e7728-133">S’exécute sur Windows, macOS et Linux.</span><span class="sxs-lookup"><span data-stu-id="e7728-133">Runs on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="e7728-134">performances améliorées</span><span class="sxs-lookup"><span data-stu-id="e7728-134">Improved performance</span></span>
* [<span data-ttu-id="e7728-135">Contrôle de version côte à côte</span><span class="sxs-lookup"><span data-stu-id="e7728-135">Side-by-side versioning</span></span>](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)
* <span data-ttu-id="e7728-136">Nouvelles API</span><span class="sxs-lookup"><span data-stu-id="e7728-136">New APIs</span></span>
* <span data-ttu-id="e7728-137">Open source</span><span class="sxs-lookup"><span data-stu-id="e7728-137">Open source</span></span>

## <a name="recommended-learning-path"></a><span data-ttu-id="e7728-138">Parcours d’apprentissage recommandé</span><span class="sxs-lookup"><span data-stu-id="e7728-138">Recommended learning path</span></span>

<span data-ttu-id="e7728-139">Pour une introduction au développement d’applications ASP.NET Core, nous vous recommandons d’utiliser la séquence de didacticiels suivante :</span><span class="sxs-lookup"><span data-stu-id="e7728-139">We recommend the following sequence of tutorials for an introduction to developing ASP.NET Core apps:</span></span>

1. <span data-ttu-id="e7728-140">Suivez un didacticiel pour le type d’application que vous souhaitez développer ou gérer.</span><span class="sxs-lookup"><span data-stu-id="e7728-140">Follow a tutorial for the app type you want to develop or maintain.</span></span>

   |<span data-ttu-id="e7728-141">Type d’application</span><span class="sxs-lookup"><span data-stu-id="e7728-141">App type</span></span>  |<span data-ttu-id="e7728-142">Scénario</span><span class="sxs-lookup"><span data-stu-id="e7728-142">Scenario</span></span>  |<span data-ttu-id="e7728-143">Didacticiel</span><span class="sxs-lookup"><span data-stu-id="e7728-143">Tutorial</span></span>  |
   |----------|----------|----------|
   |<span data-ttu-id="e7728-144">Application web</span><span class="sxs-lookup"><span data-stu-id="e7728-144">Web app</span></span>                   | <span data-ttu-id="e7728-145">Nouveau développement de l’interface utilisateur Web côté serveur</span><span class="sxs-lookup"><span data-stu-id="e7728-145">New server-side web UI development</span></span> |[<span data-ttu-id="e7728-146">Prise en main des :::no-loc(Razor)::: pages</span><span class="sxs-lookup"><span data-stu-id="e7728-146">Get started with :::no-loc(Razor)::: Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start) |
   |<span data-ttu-id="e7728-147">Application web</span><span class="sxs-lookup"><span data-stu-id="e7728-147">Web app</span></span>                   | <span data-ttu-id="e7728-148">Maintenance d’une application MVC</span><span class="sxs-lookup"><span data-stu-id="e7728-148">Maintaining an MVC app</span></span> |[<span data-ttu-id="e7728-149">Bien démarrer avec MVC</span><span class="sxs-lookup"><span data-stu-id="e7728-149">Get started with MVC</span></span>](xref:tutorials/first-mvc-app/start-mvc)|
   |<span data-ttu-id="e7728-150">Application web</span><span class="sxs-lookup"><span data-stu-id="e7728-150">Web app</span></span>                   | <span data-ttu-id="e7728-151">Développement de l’interface utilisateur Web côté client</span><span class="sxs-lookup"><span data-stu-id="e7728-151">Client-side web UI development</span></span> |[<span data-ttu-id="e7728-152">Prise en main de :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="e7728-152">Get started with :::no-loc(Blazor):::</span></span>](https://dotnet.microsoft.com/learn/aspnet/blazor-tutorial/intro) |
   |<span data-ttu-id="e7728-153">API web</span><span class="sxs-lookup"><span data-stu-id="e7728-153">Web API</span></span>                   | <span data-ttu-id="e7728-154">Services HTTP RESTful</span><span class="sxs-lookup"><span data-stu-id="e7728-154">RESTful HTTP services</span></span> |<span data-ttu-id="e7728-155">[Créer une API Web](xref:tutorials/first-web-api)&dagger;</span><span class="sxs-lookup"><span data-stu-id="e7728-155">[Create a web API](xref:tutorials/first-web-api)&dagger;</span></span> |
   |<span data-ttu-id="e7728-156">Application d’appel de procédure distante</span><span class="sxs-lookup"><span data-stu-id="e7728-156">Remote Procedure Call app</span></span> | <span data-ttu-id="e7728-157">Services contrat en premier à l’aide de mémoires tampons de protocole</span><span class="sxs-lookup"><span data-stu-id="e7728-157">Contract-first services using Protocol Buffers</span></span> |[<span data-ttu-id="e7728-158">Bien démarrer avec un service gRPC</span><span class="sxs-lookup"><span data-stu-id="e7728-158">Get started with a gRPC service</span></span>](xref:tutorials/grpc/grpc-start) |
   |<span data-ttu-id="e7728-159">Application en temps réel</span><span class="sxs-lookup"><span data-stu-id="e7728-159">Real-time app</span></span>             | <span data-ttu-id="e7728-160">Communication bidirectionnelle entre les serveurs et les clients connectés</span><span class="sxs-lookup"><span data-stu-id="e7728-160">Bidirectional communication between servers and connected clients</span></span> |[<span data-ttu-id="e7728-161">Prise en main de :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="e7728-161">Get started with :::no-loc(SignalR):::</span></span>](xref:tutorials/signalr) |

1. <span data-ttu-id="e7728-162">Suivez un didacticiel qui montre comment effectuer un accès aux données de base.</span><span class="sxs-lookup"><span data-stu-id="e7728-162">Follow a tutorial that shows how to do basic data access.</span></span>

   |<span data-ttu-id="e7728-163">Scénario</span><span class="sxs-lookup"><span data-stu-id="e7728-163">Scenario</span></span>  |<span data-ttu-id="e7728-164">Didacticiel</span><span class="sxs-lookup"><span data-stu-id="e7728-164">Tutorial</span></span>  |
   |----------|----------|
   |<span data-ttu-id="e7728-165">Nouveau développement</span><span class="sxs-lookup"><span data-stu-id="e7728-165">New development</span></span>        |[<span data-ttu-id="e7728-166">:::no-loc(Razor)::: Pages avec Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="e7728-166">:::no-loc(Razor)::: Pages with Entity Framework Core</span></span>](xref:data/ef-rp/intro) |
   |<span data-ttu-id="e7728-167">Maintenance d’une application MVC</span><span class="sxs-lookup"><span data-stu-id="e7728-167">Maintaining an MVC app</span></span> |[<span data-ttu-id="e7728-168">MVC avec Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="e7728-168">MVC with Entity Framework Core</span></span>](xref:data/ef-mvc/intro) |

1. <span data-ttu-id="e7728-169">Lisez une vue d’ensemble de ASP.NET Core [notions de base](xref:fundamentals/index) qui s’appliquent à tous les types d’applications.</span><span class="sxs-lookup"><span data-stu-id="e7728-169">Read an overview of ASP.NET Core [fundamentals](xref:fundamentals/index) that apply to all app types.</span></span>

1. <span data-ttu-id="e7728-170">Parcourez la table des matières pour consulter d’autres rubriques qui vous intéressent.</span><span class="sxs-lookup"><span data-stu-id="e7728-170">Browse the table of contents for other topics of interest.</span></span>

<span data-ttu-id="e7728-171">&dagger;Il y a également un [Didacticiel interactif sur l’API Web](/learn/modules/build-web-api-net-core).</span><span class="sxs-lookup"><span data-stu-id="e7728-171">&dagger;There's also an [interactive web API tutorial](/learn/modules/build-web-api-net-core).</span></span> <span data-ttu-id="e7728-172">Aucune installation locale des outils de développement n’est requise.</span><span class="sxs-lookup"><span data-stu-id="e7728-172">No local installation of development tools is required.</span></span> <span data-ttu-id="e7728-173">Le code s’exécute dans un [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) dans votre navigateur, et la [boucle](https://curl.haxx.se/) est utilisée pour le test.</span><span class="sxs-lookup"><span data-stu-id="e7728-173">The code runs in an [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) in your browser, and [curl](https://curl.haxx.se/) is used for testing.</span></span>

## <a name="migrate-from-net-framework"></a><span data-ttu-id="e7728-174">Migrer à partir de .NET Framework</span><span class="sxs-lookup"><span data-stu-id="e7728-174">Migrate from .NET Framework</span></span>

<span data-ttu-id="e7728-175">Pour obtenir un guide de référence sur la migration d’applications ASP.NET 4. x vers ASP.NET Core, consultez <xref:migration/proper-to-2x/index> .</span><span class="sxs-lookup"><span data-stu-id="e7728-175">For a reference guide to migrating ASP.NET 4.x apps to ASP.NET Core, see <xref:migration/proper-to-2x/index>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e7728-176">ASP.NET Core est une infrastructure multiplateforme, à hautes performances et [Open source](https://github.com/dotnet/aspnetcore) pour la création d’applications modernes, basées sur le Cloud et connectées à Internet.</span><span class="sxs-lookup"><span data-stu-id="e7728-176">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/dotnet/aspnetcore) framework for building modern, cloud-enabled, Internet-connected apps.</span></span> <span data-ttu-id="e7728-177">Avec ASP.NET Core, vous pouvez :</span><span class="sxs-lookup"><span data-stu-id="e7728-177">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="e7728-178">Créez des applications et services Web, des applications d' [Internet des objets (IOT)](https://www.microsoft.com/internet-of-things/) et des serveurs principaux mobiles.</span><span class="sxs-lookup"><span data-stu-id="e7728-178">Build web apps and services, [Internet of Things (IoT)](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="e7728-179">Utiliser vos outils de développement préférés sur Windows, macOS et Linux.</span><span class="sxs-lookup"><span data-stu-id="e7728-179">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="e7728-180">Déployer dans le cloud ou localement.</span><span class="sxs-lookup"><span data-stu-id="e7728-180">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="e7728-181">Exécuter sur [.NET Core ou .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span><span class="sxs-lookup"><span data-stu-id="e7728-181">Run on [.NET Core or .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span></span>

## <a name="why-choose-aspnet-core"></a><span data-ttu-id="e7728-182">Pourquoi utiliser ASP.NET Core ?</span><span class="sxs-lookup"><span data-stu-id="e7728-182">Why choose ASP.NET Core?</span></span>

<span data-ttu-id="e7728-183">Des millions de développeurs utilisent ou ont utilisé [ASP.net 4. x](/aspnet/overview) pour créer des applications Web.</span><span class="sxs-lookup"><span data-stu-id="e7728-183">Millions of developers use or have used [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="e7728-184">ASP.NET Core est une refonte d’ASP.NET 4.x, avec des modifications d’architecture qui aboutissent à un framework plus léger et modulaire.</span><span class="sxs-lookup"><span data-stu-id="e7728-184">ASP.NET Core is a redesign of ASP.NET 4.x, with architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="e7728-185">Créer des API web et une interface utilisateur web en utilisant le modèle MVC d’ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e7728-185">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="e7728-186">Le modèle MVC d’ASP.NET Core fournit des fonctionnalités pour créer des [API web](xref:tutorials/first-web-api) et des [applications web](xref:tutorials/razor-pages/index) :</span><span class="sxs-lookup"><span data-stu-id="e7728-186">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/first-web-api) and [web apps](xref:tutorials/razor-pages/index):</span></span>

* <span data-ttu-id="e7728-187">Le [modèle MVC (Modèle-Vue-Contrôleur)](xref:mvc/overview) permet de rendre vos API web et vos applications web testables.</span><span class="sxs-lookup"><span data-stu-id="e7728-187">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps testable.</span></span>
* <span data-ttu-id="e7728-188">[ :::no-loc(Razor)::: Pages](xref:razor-pages/index) est un modèle de programmation basé sur des pages qui rend la création d’une interface utilisateur Web plus simple et plus productive.</span><span class="sxs-lookup"><span data-stu-id="e7728-188">[:::no-loc(Razor)::: Pages](xref:razor-pages/index) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="e7728-189">le [ :::no-loc(Razor)::: balisage](xref:mvc/views/razor) fournit une syntaxe productive pour les [ :::no-loc(Razor)::: pages](xref:razor-pages/index) et les [vues MVC](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="e7728-189">[:::no-loc(Razor)::: markup](xref:mvc/views/razor) provides a productive syntax for [:::no-loc(Razor)::: Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="e7728-190">Les [tag helpers](xref:mvc/views/tag-helpers/intro) permettent au code côté serveur de participer à la création et au rendu des éléments HTML dans les :::no-loc(Razor)::: fichiers.</span><span class="sxs-lookup"><span data-stu-id="e7728-190">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in :::no-loc(Razor)::: files.</span></span>
* <span data-ttu-id="e7728-191">La prise en charge intégrée de [plusieurs formats de données et de la négociation de contenu](xref:web-api/advanced/formatting) permet à vos API web d’être utilisées par un large éventail de clients, notamment des navigateurs et des appareils mobiles.</span><span class="sxs-lookup"><span data-stu-id="e7728-191">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="e7728-192">Le principe de la [liaison de modèle](xref:mvc/models/model-binding) permet le mappage automatiquement des données des requêtes HTTP aux paramètres des méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="e7728-192">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="e7728-193">La [validation de modèle](xref:mvc/models/validation) effectue automatiquement la validation côté client et côté serveur.</span><span class="sxs-lookup"><span data-stu-id="e7728-193">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="e7728-194">Développement côté client</span><span class="sxs-lookup"><span data-stu-id="e7728-194">Client-side development</span></span>

<span data-ttu-id="e7728-195">ASP.NET Core s’intègre de façon transparente aux infrastructures et bibliothèques courantes côté client, notamment [:::no-loc(Blazor):::](xref:blazor/index) , [angulaires](xref:spa/angular), [réactifs](xref:spa/react)et [bootstrap](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="e7728-195">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [:::no-loc(Blazor):::](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="e7728-196">Pour plus d’informations, consultez <xref:blazor/index> et les rubriques connexes sous *Développement côté client* .</span><span class="sxs-lookup"><span data-stu-id="e7728-196">For more information, see <xref:blazor/index> and related topics under *Client-side development* .</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-targeting-net-framework"></a><span data-ttu-id="e7728-197">ASP.NET Core ciblant .NET Framework</span><span class="sxs-lookup"><span data-stu-id="e7728-197">ASP.NET Core targeting .NET Framework</span></span>

<span data-ttu-id="e7728-198">ASP.NET Core 2.x peut cibler .NET Core ou le .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="e7728-198">ASP.NET Core 2.x can target .NET Core or .NET Framework.</span></span> <span data-ttu-id="e7728-199">Les applications ASP.NET Core ciblant .NET Framework ne sont pas multiplateformes : elles s’exécutent seulement sur Windows.</span><span class="sxs-lookup"><span data-stu-id="e7728-199">ASP.NET Core apps targeting .NET Framework aren't cross-platform&mdash;they run on Windows only.</span></span> <span data-ttu-id="e7728-200">D’une façon générale, ASP.NET Core 2.x est constitué de bibliothèques [.NET Standard](/dotnet/standard/net-standard).</span><span class="sxs-lookup"><span data-stu-id="e7728-200">Generally, ASP.NET Core 2.x is made up of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="e7728-201">Les bibliothèques écrites avec .NET Standard 2.0 s’exécutent sur [n’importe quelle plateforme .NET qui implémente .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span><span class="sxs-lookup"><span data-stu-id="e7728-201">Libraries written with .NET Standard 2.0 run on any [.NET platform that implements .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span></span>

<span data-ttu-id="e7728-202">ASP.NET Core 2.x est pris en charge sur les versions .NET Framework compatibles avec .NET Standard 2.0 :</span><span class="sxs-lookup"><span data-stu-id="e7728-202">ASP.NET Core 2.x is supported on .NET Framework versions that implement .NET Standard 2.0:</span></span>

* <span data-ttu-id="e7728-203">.NET Framework dernière version est recommandée.</span><span class="sxs-lookup"><span data-stu-id="e7728-203">.NET Framework latest version is recommended.</span></span>
* <span data-ttu-id="e7728-204">.NET Framework 4.6.1 et versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="e7728-204">.NET Framework 4.6.1 and later.</span></span>

<span data-ttu-id="e7728-205">ASP.NET Core 3.0 et ultérieur s’exécute uniquement sur .NET Core.</span><span class="sxs-lookup"><span data-stu-id="e7728-205">ASP.NET Core 3.0 and later will only run on .NET Core.</span></span> <span data-ttu-id="e7728-206">Pour plus de détails concernant ce changement, consultez [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span><span class="sxs-lookup"><span data-stu-id="e7728-206">For more details regarding this change, see [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<span data-ttu-id="e7728-207">Le ciblage de .NET Core présente plusieurs avantages, qui sont plus nombreux à chaque version.</span><span class="sxs-lookup"><span data-stu-id="e7728-207">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="e7728-208">Voici certains avantages de .NET Core par rapport à .NET Framework :</span><span class="sxs-lookup"><span data-stu-id="e7728-208">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="e7728-209">Multiplateforme.</span><span class="sxs-lookup"><span data-stu-id="e7728-209">Cross-platform.</span></span> <span data-ttu-id="e7728-210">S’exécute sur macOS, Linux et Windows</span><span class="sxs-lookup"><span data-stu-id="e7728-210">Runs on macOS, Linux, and Windows.</span></span>
* <span data-ttu-id="e7728-211">performances améliorées</span><span class="sxs-lookup"><span data-stu-id="e7728-211">Improved performance</span></span>
* [<span data-ttu-id="e7728-212">Contrôle de version côte à côte</span><span class="sxs-lookup"><span data-stu-id="e7728-212">Side-by-side versioning</span></span>](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)
* <span data-ttu-id="e7728-213">Nouvelles API</span><span class="sxs-lookup"><span data-stu-id="e7728-213">New APIs</span></span>
* <span data-ttu-id="e7728-214">Open source</span><span class="sxs-lookup"><span data-stu-id="e7728-214">Open source</span></span>

<span data-ttu-id="e7728-215">Pour aider à fermer le fossé entre les API .NET Framework et .NET Core, le [Pack de compatibilité Windows](/dotnet/core/porting/windows-compat-pack) a fait des milliers d’API Windows uniquement disponibles dans .net core.</span><span class="sxs-lookup"><span data-stu-id="e7728-215">To help close the API gap from .NET Framework to .NET Core, the [Windows Compatibility Pack](/dotnet/core/porting/windows-compat-pack) made thousands of Windows-only APIs available in .NET Core.</span></span> <span data-ttu-id="e7728-216">Ces API n’étaient pas disponibles dans .NET Core 1.x.</span><span class="sxs-lookup"><span data-stu-id="e7728-216">These APIs weren't available in .NET Core 1.x.</span></span>

## <a name="recommended-learning-path"></a><span data-ttu-id="e7728-217">Parcours d’apprentissage recommandé</span><span class="sxs-lookup"><span data-stu-id="e7728-217">Recommended learning path</span></span>

<span data-ttu-id="e7728-218">Nous vous recommandons la séquence de tutoriels et d’articles suivante comme introduction au développement des applications ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="e7728-218">We recommend the following sequence of tutorials and articles for an introduction to developing ASP.NET Core apps:</span></span>

1. <span data-ttu-id="e7728-219">Suivez un didacticiel pour le type d’application que vous souhaitez développer ou gérer.</span><span class="sxs-lookup"><span data-stu-id="e7728-219">Follow a tutorial for the type of app you want to develop or maintain.</span></span>

   |<span data-ttu-id="e7728-220">Type d’application</span><span class="sxs-lookup"><span data-stu-id="e7728-220">App type</span></span>  |<span data-ttu-id="e7728-221">Scénario</span><span class="sxs-lookup"><span data-stu-id="e7728-221">Scenario</span></span>  |<span data-ttu-id="e7728-222">Didacticiel</span><span class="sxs-lookup"><span data-stu-id="e7728-222">Tutorial</span></span>  |
   |----------|----------|----------|
   |<span data-ttu-id="e7728-223">Application web</span><span class="sxs-lookup"><span data-stu-id="e7728-223">Web app</span></span>                   | <span data-ttu-id="e7728-224">Pour un nouveau développement</span><span class="sxs-lookup"><span data-stu-id="e7728-224">For new development</span></span>        |[<span data-ttu-id="e7728-225">Prise en main des :::no-loc(Razor)::: pages</span><span class="sxs-lookup"><span data-stu-id="e7728-225">Get started with :::no-loc(Razor)::: Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start) |
   |<span data-ttu-id="e7728-226">Application web</span><span class="sxs-lookup"><span data-stu-id="e7728-226">Web app</span></span>                   | <span data-ttu-id="e7728-227">Pour maintenir une application MVC</span><span class="sxs-lookup"><span data-stu-id="e7728-227">For maintaining an MVC app</span></span> |[<span data-ttu-id="e7728-228">Bien démarrer avec MVC</span><span class="sxs-lookup"><span data-stu-id="e7728-228">Get started with MVC</span></span>](xref:tutorials/first-mvc-app/start-mvc)|
   |<span data-ttu-id="e7728-229">API web</span><span class="sxs-lookup"><span data-stu-id="e7728-229">Web API</span></span>                   |                            |<span data-ttu-id="e7728-230">[Créer une API Web](xref:tutorials/first-web-api)&dagger;</span><span class="sxs-lookup"><span data-stu-id="e7728-230">[Create a web API](xref:tutorials/first-web-api)&dagger;</span></span> |
   |<span data-ttu-id="e7728-231">Application en temps réel</span><span class="sxs-lookup"><span data-stu-id="e7728-231">Real-time app</span></span>             |                            |[<span data-ttu-id="e7728-232">Prise en main de :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="e7728-232">Get started with :::no-loc(SignalR):::</span></span>](xref:tutorials/signalr) |

1. <span data-ttu-id="e7728-233">Suivez un didacticiel qui montre comment effectuer un accès aux données de base.</span><span class="sxs-lookup"><span data-stu-id="e7728-233">Follow a tutorial that shows how to do basic data access.</span></span>

   |<span data-ttu-id="e7728-234">Scénario</span><span class="sxs-lookup"><span data-stu-id="e7728-234">Scenario</span></span>  |<span data-ttu-id="e7728-235">Didacticiel</span><span class="sxs-lookup"><span data-stu-id="e7728-235">Tutorial</span></span>  |
   |----------|----------|
   | <span data-ttu-id="e7728-236">Pour un nouveau développement</span><span class="sxs-lookup"><span data-stu-id="e7728-236">For new development</span></span>        |[<span data-ttu-id="e7728-237">:::no-loc(Razor)::: Pages avec Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="e7728-237">:::no-loc(Razor)::: Pages with Entity Framework Core</span></span>](xref:data/ef-rp/intro) |
   | <span data-ttu-id="e7728-238">Pour maintenir une application MVC</span><span class="sxs-lookup"><span data-stu-id="e7728-238">For maintaining an MVC app</span></span> |[<span data-ttu-id="e7728-239">MVC avec Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="e7728-239">MVC with Entity Framework Core</span></span>](xref:data/ef-mvc/intro) |

1. <span data-ttu-id="e7728-240">Lisez une vue d’ensemble de ASP.NET Core [notions de base](xref:fundamentals/index) qui s’appliquent à tous les types d’applications.</span><span class="sxs-lookup"><span data-stu-id="e7728-240">Read an overview of ASP.NET Core [fundamentals](xref:fundamentals/index) that apply to all app types.</span></span>

1. <span data-ttu-id="e7728-241">Parcourez la Table des matières pour d’autres rubriques qui vous intéressent.</span><span class="sxs-lookup"><span data-stu-id="e7728-241">Browse the Table of Contents for other topics of interest.</span></span>

<span data-ttu-id="e7728-242">&dagger;Il y a également un [didacticiel d’API Web que vous suivez entièrement dans le navigateur](/learn/modules/build-web-api-net-core), aucune installation IDE locale n’est requise.</span><span class="sxs-lookup"><span data-stu-id="e7728-242">&dagger;There's also a [web API tutorial that you follow entirely in the browser](/learn/modules/build-web-api-net-core), no local IDE installation required.</span></span> <span data-ttu-id="e7728-243">Le code s’exécute dans [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), et [curl](https://curl.haxx.se/) est utilisé à des fins de test.</span><span class="sxs-lookup"><span data-stu-id="e7728-243">The code runs in an [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), and [curl](https://curl.haxx.se/) is used for testing.</span></span>

## <a name="migrate-from-net-framework"></a><span data-ttu-id="e7728-244">Migrer à partir de .NET Framework</span><span class="sxs-lookup"><span data-stu-id="e7728-244">Migrate from .NET Framework</span></span>

<span data-ttu-id="e7728-245">Pour obtenir un guide de référence sur la migration d’applications ASP.NET vers ASP.NET Core, consultez <xref:migration/proper-to-2x/index> .</span><span class="sxs-lookup"><span data-stu-id="e7728-245">For a reference guide to migrating ASP.NET apps to ASP.NET Core, see <xref:migration/proper-to-2x/index>.</span></span>

::: moniker-end

## <a name="how-to-download-a-sample"></a><span data-ttu-id="e7728-246">Comment télécharger un exemple</span><span class="sxs-lookup"><span data-stu-id="e7728-246">How to download a sample</span></span>

<span data-ttu-id="e7728-247">La plupart des articles et tutoriels contiennent des liens vers des exemples de code.</span><span class="sxs-lookup"><span data-stu-id="e7728-247">Many of the articles and tutorials include links to sample code.</span></span>

1. <span data-ttu-id="e7728-248">[Téléchargez le fichier zip du référentiel ASP.NET](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).</span><span class="sxs-lookup"><span data-stu-id="e7728-248">[Download the ASP.NET repository zip file](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).</span></span>
1. <span data-ttu-id="e7728-249">Décompressez le fichier *Docs-master.zip* .</span><span class="sxs-lookup"><span data-stu-id="e7728-249">Unzip the *Docs-master.zip* file.</span></span>
1. <span data-ttu-id="e7728-250">Utilisez l’URL contenue dans l’exemple de lien pour vous aider à naviguer dans l’exemple de répertoire.</span><span class="sxs-lookup"><span data-stu-id="e7728-250">Use the URL in the sample link to help you navigate to the sample directory.</span></span>

### <a name="preprocessor-directives-in-sample-code"></a><span data-ttu-id="e7728-251">Directives de préprocesseur dans l’exemple de code</span><span class="sxs-lookup"><span data-stu-id="e7728-251">Preprocessor directives in sample code</span></span>

<span data-ttu-id="e7728-252">Pour illustrer plusieurs scénarios, les exemples d’applications utilisent les `#define` `#if-#else/#elif-#endif` directives de préprocesseur et pour compiler et exécuter de manière sélective différentes sections d’exemples de code.</span><span class="sxs-lookup"><span data-stu-id="e7728-252">To demonstrate multiple scenarios, sample apps use the `#define` and `#if-#else/#elif-#endif` preprocessor directives to selectively compile and run different sections of sample code.</span></span> <span data-ttu-id="e7728-253">Pour les exemples qui utilisent cette approche, définissez la `#define` directive en haut des fichiers C# pour définir le symbole associé au scénario que vous souhaitez exécuter.</span><span class="sxs-lookup"><span data-stu-id="e7728-253">For those samples that make use of this approach, set the `#define` directive at the top of the C# files to define the symbol associated with the scenario that you want to run.</span></span> <span data-ttu-id="e7728-254">Certains exemples requièrent la définition du symbole en haut de plusieurs fichiers afin d’exécuter un scénario.</span><span class="sxs-lookup"><span data-stu-id="e7728-254">Some samples require defining the symbol at the top of multiple files in order to run a scenario.</span></span>

<span data-ttu-id="e7728-255">Par exemple, la liste des symboles `#define` suivante indique que les quatre scénarios sont disponibles (un scénario par symbole).</span><span class="sxs-lookup"><span data-stu-id="e7728-255">For example, the following `#define` symbol list indicates that four scenarios are available (one scenario per symbol).</span></span> <span data-ttu-id="e7728-256">La configuration actuelle de l’exemple exécute le scénario `TemplateCode` :</span><span class="sxs-lookup"><span data-stu-id="e7728-256">The current sample configuration runs the `TemplateCode` scenario:</span></span>

```csharp
#define TemplateCode // or LogFromMain or ExpandDefault or FilterInCode
```

<span data-ttu-id="e7728-257">Pour que l’exemple exécute le scénario `ExpandDefault`, définissez le symbole `ExpandDefault` et laissez les symboles restants commentés :</span><span class="sxs-lookup"><span data-stu-id="e7728-257">To change the sample to run the `ExpandDefault` scenario, define the `ExpandDefault` symbol and leave the remaining symbols commented-out:</span></span>

```csharp
#define ExpandDefault // TemplateCode or LogFromMain or FilterInCode
```

<span data-ttu-id="e7728-258">Pour plus d’informations sur l’utilisation des [directives de préprocesseur C#](/dotnet/csharp/language-reference/preprocessor-directives/) pour compiler de façon sélective des sections de code, consultez [#define (Référence C#)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) et [#if (Référence C#) ](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span><span class="sxs-lookup"><span data-stu-id="e7728-258">For more information on using [C# preprocessor directives](/dotnet/csharp/language-reference/preprocessor-directives/) to selectively compile sections of code, see [#define (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) and [#if (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span></span>

### <a name="regions-in-sample-code"></a><span data-ttu-id="e7728-259">Régions dans l’exemple de code</span><span class="sxs-lookup"><span data-stu-id="e7728-259">Regions in sample code</span></span>

<span data-ttu-id="e7728-260">Certains exemples d’applications contiennent des sections de code entourées de [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) et [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) directives C#.</span><span class="sxs-lookup"><span data-stu-id="e7728-260">Some sample apps contain sections of code surrounded by [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) and [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) C# directives.</span></span> <span data-ttu-id="e7728-261">Le système de génération de documentation injecte ces régions dans les rubriques de documentation affichées.</span><span class="sxs-lookup"><span data-stu-id="e7728-261">The documentation build system injects these regions into the rendered documentation topics.</span></span>  

<span data-ttu-id="e7728-262">Les noms des régions contiennent généralement le mot « snippet ».</span><span class="sxs-lookup"><span data-stu-id="e7728-262">Region names usually contain the word "snippet."</span></span> <span data-ttu-id="e7728-263">L’exemple suivant montre une région nommée `snippet_WebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="e7728-263">The following example shows a region named `snippet_WebHostDefaults`:</span></span>

```csharp
#region snippet_WebHostDefaults
Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
    });
#endregion
```

<span data-ttu-id="e7728-264">L’extrait de code C# précédent est référencé dans le fichier Markdown de la rubrique avec la ligne suivante :</span><span class="sxs-lookup"><span data-stu-id="e7728-264">The preceding C# code snippet is referenced in the topic's markdown file with the following line:</span></span>

```md
[!code-csharp[](sample/SampleApp/Program.cs?name=snippet_WebHostDefaults)]
```

<span data-ttu-id="e7728-265">Vous pouvez ignorer (ou supprimer) en toute sécurité `#region` les `#endregion` directives et qui entourent le code.</span><span class="sxs-lookup"><span data-stu-id="e7728-265">You may safely ignore (or remove) the `#region` and `#endregion` directives that surround the code.</span></span> <span data-ttu-id="e7728-266">Ne modifiez pas le code dans ces directives si vous envisagez d’exécuter les exemples de scénarios décrits dans la rubrique.</span><span class="sxs-lookup"><span data-stu-id="e7728-266">Don't alter the code within these directives if you plan to run the sample scenarios described in the topic.</span></span> <span data-ttu-id="e7728-267">N’hésitez pas à modifier le code quand vous testez d’autres scénarios.</span><span class="sxs-lookup"><span data-stu-id="e7728-267">Feel free to alter the code when experimenting with other scenarios.</span></span>

<span data-ttu-id="e7728-268">Pour plus d’informations, consultez [Contribuer à la documentation ASP.NET : extraits de code](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).</span><span class="sxs-lookup"><span data-stu-id="e7728-268">For more information, see [Contribute to the ASP.NET documentation: Code snippets](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).</span></span>

## <a name="next-steps"></a><span data-ttu-id="e7728-269">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="e7728-269">Next steps</span></span>

<span data-ttu-id="e7728-270">Pour plus d’informations, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="e7728-270">For more information, see the following resources:</span></span>

* <xref:getting-started>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="e7728-271">Notions de base d’ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e7728-271">ASP.NET Core fundamentals</span></span>](xref:fundamentals/index)
* <span data-ttu-id="e7728-272">[Le point hebdomadaire de la communauté ASP.NET](https://live.asp.net/) couvre l’avancement et les plans des équipes de développement.</span><span class="sxs-lookup"><span data-stu-id="e7728-272">[The weekly ASP.NET community standup](https://live.asp.net/) covers the team's progress and plans.</span></span> <span data-ttu-id="e7728-273">Il met en avant de nouveaux blogs et des logiciels de tiers.</span><span class="sxs-lookup"><span data-stu-id="e7728-273">It features new blogs and third-party software.</span></span>
