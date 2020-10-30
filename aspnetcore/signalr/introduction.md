---
title: 'Présentation de ASP.NET Core :::no-loc(SignalR):::'
author: bradygaster
description: 'Découvrez comment la :::no-loc(SignalR)::: bibliothèque ASP.net Core simplifie l’ajout de fonctionnalités en temps réel aux applications.'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/27/2019
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
uid: signalr/introduction
ms.openlocfilehash: 1810fef903362addcef4a6c9ec53264604f58d2b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051471"
---
# <a name="introduction-to-aspnet-core-no-locsignalr"></a><span data-ttu-id="29563-103">Présentation de ASP.NET Core :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="29563-103">Introduction to ASP.NET Core :::no-loc(SignalR):::</span></span>

## <a name="what-is-no-locsignalr"></a><span data-ttu-id="29563-104">Qu’est-ce que c’est :::no-loc(SignalR)::: ?</span><span class="sxs-lookup"><span data-stu-id="29563-104">What is :::no-loc(SignalR):::?</span></span>

<span data-ttu-id="29563-105">ASP.NET Core :::no-loc(SignalR)::: est une bibliothèque open source qui simplifie l’ajout de fonctionnalités Web en temps réel aux applications.</span><span class="sxs-lookup"><span data-stu-id="29563-105">ASP.NET Core :::no-loc(SignalR)::: is an open-source library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="29563-106">La fonctionnalité Web en temps réel permet au code côté serveur de transmettre instantanément du contenu aux clients.</span><span class="sxs-lookup"><span data-stu-id="29563-106">Real-time web functionality enables server-side code to push content to clients instantly.</span></span>

<span data-ttu-id="29563-107">Bons candidats pour :::no-loc(SignalR)::: :</span><span class="sxs-lookup"><span data-stu-id="29563-107">Good candidates for :::no-loc(SignalR)::::</span></span>

* <span data-ttu-id="29563-108">Les applications ayant besoin de mises à jour fréquentes auprès du serveur.</span><span class="sxs-lookup"><span data-stu-id="29563-108">Apps that require high frequency updates from the server.</span></span> <span data-ttu-id="29563-109">Exemples : jeux, réseaux sociaux, scrutin, enchères, cartes et applications GPS.</span><span class="sxs-lookup"><span data-stu-id="29563-109">Examples are gaming, social networks, voting, auction, maps, and GPS apps.</span></span>
* <span data-ttu-id="29563-110">Les tableaux de bord et les applications de monitoring.</span><span class="sxs-lookup"><span data-stu-id="29563-110">Dashboards and monitoring apps.</span></span> <span data-ttu-id="29563-111">Exemples : tableaux de bord des entreprises, mises à jour instantanées des ventes et alertes de voyage.</span><span class="sxs-lookup"><span data-stu-id="29563-111">Examples include company dashboards, instant sales updates, or travel alerts.</span></span>
* <span data-ttu-id="29563-112">Les applications de collaboration.</span><span class="sxs-lookup"><span data-stu-id="29563-112">Collaborative apps.</span></span> <span data-ttu-id="29563-113">Exemples : applications de tableau blanc et logiciels de réunion d’équipe.</span><span class="sxs-lookup"><span data-stu-id="29563-113">Whiteboard apps and team meeting software are examples of collaborative apps.</span></span>
* <span data-ttu-id="29563-114">Les applications qui envoient des notifications.</span><span class="sxs-lookup"><span data-stu-id="29563-114">Apps that require notifications.</span></span> <span data-ttu-id="29563-115">Exemples : réseaux sociaux, messagerie, conversation instantanée, jeux, alertes de voyage, etc.</span><span class="sxs-lookup"><span data-stu-id="29563-115">Social networks, email, chat, games, travel alerts, and many other apps use notifications.</span></span>

<span data-ttu-id="29563-116">:::no-loc(SignalR)::: fournit une API permettant de créer des [appels de procédure distante (RPC)](https://wikipedia.org/wiki/Remote_procedure_call)de serveur à client.</span><span class="sxs-lookup"><span data-stu-id="29563-116">:::no-loc(SignalR)::: provides an API for creating server-to-client [remote procedure calls (RPC)](https://wikipedia.org/wiki/Remote_procedure_call).</span></span> <span data-ttu-id="29563-117">Les RPC appellent des fonctions JavaScript sur les clients à partir du code .NET Core côté serveur.</span><span class="sxs-lookup"><span data-stu-id="29563-117">The RPCs call JavaScript functions on clients from server-side .NET Core code.</span></span>

<span data-ttu-id="29563-118">Voici quelques-unes des fonctionnalités de :::no-loc(SignalR)::: pour ASP.net Core :</span><span class="sxs-lookup"><span data-stu-id="29563-118">Here are some features of :::no-loc(SignalR)::: for ASP.NET Core:</span></span>

* <span data-ttu-id="29563-119">Gère automatiquement la gestion des connexions.</span><span class="sxs-lookup"><span data-stu-id="29563-119">Handles connection management automatically.</span></span>
* <span data-ttu-id="29563-120">Envoie des messages à tous les clients connectés simultanément.</span><span class="sxs-lookup"><span data-stu-id="29563-120">Sends messages to all connected clients simultaneously.</span></span> <span data-ttu-id="29563-121">Par exemple, une salle de conversation.</span><span class="sxs-lookup"><span data-stu-id="29563-121">For example, a chat room.</span></span>
* <span data-ttu-id="29563-122">Envoie des messages à des clients ou groupes de clients spécifiques.</span><span class="sxs-lookup"><span data-stu-id="29563-122">Sends messages to specific clients or groups of clients.</span></span>
* <span data-ttu-id="29563-123">Met à l’échelle pour gérer le trafic qui augmente.</span><span class="sxs-lookup"><span data-stu-id="29563-123">Scales to handle increasing traffic.</span></span>

<span data-ttu-id="29563-124">La source est hébergée dans un [ :::no-loc(SignalR)::: référentiel sur GitHub](https://github.com/dotnet/AspNetCore/tree/master/src/:::no-loc(SignalR):::).</span><span class="sxs-lookup"><span data-stu-id="29563-124">The source is hosted in a [:::no-loc(SignalR)::: repository on GitHub](https://github.com/dotnet/AspNetCore/tree/master/src/:::no-loc(SignalR):::).</span></span>

## <a name="transports"></a><span data-ttu-id="29563-125">Transports</span><span class="sxs-lookup"><span data-stu-id="29563-125">Transports</span></span>

<span data-ttu-id="29563-126">:::no-loc(SignalR)::: prend en charge les techniques suivantes pour gérer les communications en temps réel (par ordre de secours normal) :</span><span class="sxs-lookup"><span data-stu-id="29563-126">:::no-loc(SignalR)::: supports the following techniques for handling real-time communication (in order of graceful fallback):</span></span>

* [<span data-ttu-id="29563-127">WebSockets</span><span class="sxs-lookup"><span data-stu-id="29563-127">WebSockets</span></span>](https://tools.ietf.org/html/rfc7118)
* <span data-ttu-id="29563-128">Événements de Server-Sent</span><span class="sxs-lookup"><span data-stu-id="29563-128">Server-Sent Events</span></span>
* <span data-ttu-id="29563-129">Interrogation longue</span><span class="sxs-lookup"><span data-stu-id="29563-129">Long Polling</span></span>

<span data-ttu-id="29563-130">:::no-loc(SignalR)::: choisit automatiquement la meilleure méthode de transport parmi les capacités du serveur et du client.</span><span class="sxs-lookup"><span data-stu-id="29563-130">:::no-loc(SignalR)::: automatically chooses the best transport method that is within the capabilities of the server and client.</span></span>

## <a name="hubs"></a><span data-ttu-id="29563-131">Hubs</span><span class="sxs-lookup"><span data-stu-id="29563-131">Hubs</span></span>

<span data-ttu-id="29563-132">:::no-loc(SignalR)::: utilise des *concentrateurs* pour la communication entre les clients et les serveurs.</span><span class="sxs-lookup"><span data-stu-id="29563-132">:::no-loc(SignalR)::: uses *hubs* to communicate between clients and servers.</span></span>

<span data-ttu-id="29563-133">Un Hub est un pipeline de haut niveau qui permet à un client et un serveur d’appeler des méthodes les unes sur les autres.</span><span class="sxs-lookup"><span data-stu-id="29563-133">A hub is a high-level pipeline that allows a client and server to call methods on each other.</span></span> <span data-ttu-id="29563-134">:::no-loc(SignalR)::: gère automatiquement la distribution à travers les limites de l’ordinateur, ce qui permet aux clients d’appeler des méthodes sur le serveur et vice versa.</span><span class="sxs-lookup"><span data-stu-id="29563-134">:::no-loc(SignalR)::: handles the dispatching across machine boundaries automatically, allowing clients to call methods on the server and vice versa.</span></span> <span data-ttu-id="29563-135">Vous pouvez passer des paramètres fortement typés à des méthodes, ce qui active la liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="29563-135">You can pass strongly-typed parameters to methods, which enables model binding.</span></span> <span data-ttu-id="29563-136">:::no-loc(SignalR)::: fournit deux protocoles Hub intégrés : un protocole texte basé sur JSON et un protocole binaire basé sur [MessagePack](https://msgpack.org/).</span><span class="sxs-lookup"><span data-stu-id="29563-136">:::no-loc(SignalR)::: provides two built-in hub protocols: a text protocol based on JSON and a binary protocol based on [MessagePack](https://msgpack.org/).</span></span>  <span data-ttu-id="29563-137">MessagePack crée généralement des messages plus petits par rapport à JSON.</span><span class="sxs-lookup"><span data-stu-id="29563-137">MessagePack generally creates smaller messages compared to JSON.</span></span> <span data-ttu-id="29563-138">Les anciens navigateurs doivent prendre en charge le [niveau 2 de XHR](https://caniuse.com/#feat=xhr2) pour fournir la prise en charge du protocole MessagePack.</span><span class="sxs-lookup"><span data-stu-id="29563-138">Older browsers must support [XHR level 2](https://caniuse.com/#feat=xhr2) to provide MessagePack protocol support.</span></span>

<span data-ttu-id="29563-139">Les hubs appellent le code côté client en envoyant des messages qui contiennent le nom et les paramètres de la méthode côté client.</span><span class="sxs-lookup"><span data-stu-id="29563-139">Hubs call client-side code by sending messages that contain the name and parameters of the client-side method.</span></span> <span data-ttu-id="29563-140">Les objets envoyés en tant que paramètres de méthode sont désérialisés à l’aide du protocole configuré.</span><span class="sxs-lookup"><span data-stu-id="29563-140">Objects sent as method parameters are deserialized using the configured protocol.</span></span> <span data-ttu-id="29563-141">Le client tente de faire correspondre le nom à une méthode dans le code côté client.</span><span class="sxs-lookup"><span data-stu-id="29563-141">The client tries to match the name to a method in the client-side code.</span></span> <span data-ttu-id="29563-142">Lorsque le client trouve une correspondance, il appelle la méthode et lui passe les données de paramètre désérialisées.</span><span class="sxs-lookup"><span data-stu-id="29563-142">When the client finds a match, it calls the method and passes to it the deserialized parameter data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="29563-143">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="29563-143">Additional resources</span></span>

* [<span data-ttu-id="29563-144">Prise en main :::no-loc(SignalR)::: de pour ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="29563-144">Get started with :::no-loc(SignalR)::: for ASP.NET Core</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="29563-145">Plateformes prises en charge</span><span class="sxs-lookup"><span data-stu-id="29563-145">Supported Platforms</span></span>](xref:signalr/supported-platforms)
* [<span data-ttu-id="29563-146">Hubs</span><span class="sxs-lookup"><span data-stu-id="29563-146">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="29563-147">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="29563-147">JavaScript client</span></span>](xref:signalr/javascript-client)
