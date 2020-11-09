---
title: Vue d’ensemble de la sécurité ASP.NET Core
author: rick-anderson
description: Découvrez les concepts de base de l’authentification, de l’autorisation et de la sécurité dans ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
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
uid: security/index
ms.openlocfilehash: 3c86c66bebe8a5ce1c195ebf931193e7e2a73fef
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051627"
---
# <a name="overview-of-aspnet-core-security"></a><span data-ttu-id="acf3b-103">Vue d’ensemble de la sécurité ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="acf3b-103">Overview of ASP.NET Core Security</span></span>

<span data-ttu-id="acf3b-104">ASP.NET Core permet aux développeurs de configurer et de gérer facilement la sécurité de leurs applications.</span><span class="sxs-lookup"><span data-stu-id="acf3b-104">ASP.NET Core enables developers to easily configure and manage security for their apps.</span></span> <span data-ttu-id="acf3b-105">ASP.NET Core contient des fonctionnalités de gestion de l’authentification, de l’autorisation, de la protection des données, de la contrainte HTTPs, des secrets d’application, de la prévention XSRF/CSRF et de la gestion CORS.</span><span class="sxs-lookup"><span data-stu-id="acf3b-105">ASP.NET Core contains features for managing authentication, authorization, data protection, HTTPS enforcement, app secrets, XSRF/CSRF prevention, and CORS management.</span></span> <span data-ttu-id="acf3b-106">Ces fonctionnalités de sécurité vous permettent de générer des applications ASP.NET Core robustes et néanmoins sécurisées.</span><span class="sxs-lookup"><span data-stu-id="acf3b-106">These security features allow you to build robust yet secure ASP.NET Core apps.</span></span>

## <a name="aspnet-core-security-features"></a><span data-ttu-id="acf3b-107">Fonctionnalités de sécurité ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="acf3b-107">ASP.NET Core security features</span></span>

<span data-ttu-id="acf3b-108">ASP.NET Core fournit de nombreux outils et bibliothèques pour sécuriser vos applications, notamment les fournisseurs d’identité intégrés, mais vous pouvez utiliser des services d’identité tiers tels que Facebook, Twitter et LinkedIn.</span><span class="sxs-lookup"><span data-stu-id="acf3b-108">ASP.NET Core provides many tools and libraries to secure your apps including built-in identity providers, but you can use third-party identity services such as Facebook, Twitter, and LinkedIn.</span></span> <span data-ttu-id="acf3b-109">Avec ASP.NET Core, vous pouvez facilement gérer les secrets des applications, qui sont un moyen de stocker et d’utiliser des informations confidentielles sans avoir à les exposer dans le code.</span><span class="sxs-lookup"><span data-stu-id="acf3b-109">With ASP.NET Core, you can easily manage app secrets, which are a way to store and use confidential information without having to expose it in the code.</span></span>

## <a name="authentication-vs-authorization"></a><span data-ttu-id="acf3b-110">Authentification et autorisation</span><span class="sxs-lookup"><span data-stu-id="acf3b-110">Authentication vs. Authorization</span></span>

<span data-ttu-id="acf3b-111">L’authentification est un processus selon lequel un utilisateur fournit des informations d’identification qui sont ensuite comparées à celles stockées dans un système d’exploitation, une base de données, une application ou une ressource.</span><span class="sxs-lookup"><span data-stu-id="acf3b-111">Authentication is a process in which a user provides credentials that are then compared to those stored in an operating system, database, app or resource.</span></span> <span data-ttu-id="acf3b-112">Si elles correspondent, les utilisateurs sont authentifiés et peuvent alors effectuer les actions pour lesquelles ils disposent d’autorisations pendant un processus d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="acf3b-112">If they match, users authenticate successfully, and can then perform actions that they're authorized for, during an authorization process.</span></span> <span data-ttu-id="acf3b-113">L’autorisation désigne le processus qui détermine ce qu’un utilisateur est autorisé à faire.</span><span class="sxs-lookup"><span data-stu-id="acf3b-113">The authorization refers to the process that determines what a user is allowed to do.</span></span>

<span data-ttu-id="acf3b-114">Vous pouvez aussi vous représenter l’authentification comme un moyen d’entrer dans un espace, tel qu’un serveur, une base de données, une application ou une ressource, tandis que l’autorisation consiste à définir quelles actions l’utilisateur peut effectuer sur quels objets à l’intérieur de cet espace (serveur, base de données ou application).</span><span class="sxs-lookup"><span data-stu-id="acf3b-114">Another way to think of authentication is to consider it as a way to enter a space, such as a server, database, app or resource, while authorization is which actions the user can perform to which objects inside that space (server, database, or app).</span></span>

## <a name="common-vulnerabilities-in-software"></a><span data-ttu-id="acf3b-115">Failles de sécurité courantes dans les logiciels</span><span class="sxs-lookup"><span data-stu-id="acf3b-115">Common Vulnerabilities in software</span></span>

<span data-ttu-id="acf3b-116">ASP.NET Core et Entity Framework contiennent des fonctionnalités qui vous aident à sécuriser vos applications et à empêcher les violations de sécurité.</span><span class="sxs-lookup"><span data-stu-id="acf3b-116">ASP.NET Core and EF contain features that help you secure your apps and prevent security breaches.</span></span> <span data-ttu-id="acf3b-117">La liste de liens ci-après vous permet d’accéder à une documentation décrivant en détail des techniques destinées à éviter les failles de sécurité les plus courantes dans les applications web :</span><span class="sxs-lookup"><span data-stu-id="acf3b-117">The following list of links takes you to documentation detailing techniques to avoid the most common security vulnerabilities in web apps:</span></span>

* [<span data-ttu-id="acf3b-118">Attaques de script entre sites (XSS)</span><span class="sxs-lookup"><span data-stu-id="acf3b-118">Cross-Site Scripting (XSS) attacks</span></span>](xref:security/cross-site-scripting)
* [<span data-ttu-id="acf3b-119">Attaques par injection de code SQL</span><span class="sxs-lookup"><span data-stu-id="acf3b-119">SQL injection attacks</span></span>](/ef/core/querying/raw-sql)
* [<span data-ttu-id="acf3b-120">Attaques de falsification de requête intersites (XSRF/CSRF)</span><span class="sxs-lookup"><span data-stu-id="acf3b-120">Cross-Site Request Forgery (XSRF/CSRF) attacks</span></span>](xref:security/anti-request-forgery)
* [<span data-ttu-id="acf3b-121">Attaques par redirection ouverte</span><span class="sxs-lookup"><span data-stu-id="acf3b-121">Open redirect attacks</span></span>](xref:security/preventing-open-redirects)

<span data-ttu-id="acf3b-122">Il existe d’autres failles de sécurité que vous devez connaître.</span><span class="sxs-lookup"><span data-stu-id="acf3b-122">There are more vulnerabilities that you should be aware of.</span></span> <span data-ttu-id="acf3b-123">Pour plus d’informations, reportez-vous aux autres Articles de la section **sécurité et Identity** de la table des matières.</span><span class="sxs-lookup"><span data-stu-id="acf3b-123">For more information, see the other articles in the **Security and Identity** section of the table of contents.</span></span>
