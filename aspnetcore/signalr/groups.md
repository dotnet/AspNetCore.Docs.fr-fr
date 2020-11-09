---
title: 'Gérer les utilisateurs et les groupes dans SignalR'
author: bradygaster
description: 'Vue d’ensemble de la SignalR gestion des utilisateurs et des groupes ASP.net core.'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 05/17/2020
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
uid: signalr/groups
ms.openlocfilehash: a86408eaae8d3df32faef79453d9db0cdbd64a78
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050951"
---
# <a name="manage-users-and-groups-in-no-locsignalr"></a><span data-ttu-id="54699-103">Gérer les utilisateurs et les groupes dans SignalR</span><span class="sxs-lookup"><span data-stu-id="54699-103">Manage users and groups in SignalR</span></span>

<span data-ttu-id="54699-104">Par [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="54699-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

<span data-ttu-id="54699-105">SignalR permet d’envoyer des messages à toutes les connexions associées à un utilisateur spécifique, ainsi qu’à des groupes nommés de connexions.</span><span class="sxs-lookup"><span data-stu-id="54699-105">SignalR allows messages to be sent to all connections associated with a specific user, as well as to named groups of connections.</span></span>

<span data-ttu-id="54699-106">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(procédure de téléchargement)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="54699-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="users-in-no-locsignalr"></a><span data-ttu-id="54699-107">Utilisateurs dans SignalR</span><span class="sxs-lookup"><span data-stu-id="54699-107">Users in SignalR</span></span>

<span data-ttu-id="54699-108">Un utilisateur unique dans SignalR peut avoir plusieurs connexions à une application.</span><span class="sxs-lookup"><span data-stu-id="54699-108">A single user in SignalR can have multiple connections to an app.</span></span> <span data-ttu-id="54699-109">Par exemple, un utilisateur peut être connecté sur son bureau, ainsi que sur son téléphone.</span><span class="sxs-lookup"><span data-stu-id="54699-109">For example, a user could be connected on their desktop as well as their phone.</span></span> <span data-ttu-id="54699-110">Chaque appareil dispose d’une SignalR connexion distincte, mais tous sont associés au même utilisateur.</span><span class="sxs-lookup"><span data-stu-id="54699-110">Each device has a separate SignalR connection, but they're all associated with the same user.</span></span> <span data-ttu-id="54699-111">Si un message est envoyé à l’utilisateur, toutes les connexions associées à cet utilisateur reçoivent le message.</span><span class="sxs-lookup"><span data-stu-id="54699-111">If a message is sent to the user, all of the connections associated with that user receive the message.</span></span> <span data-ttu-id="54699-112">L’identificateur d’utilisateur pour une connexion est accessible par la `Context.UserIdentifier` propriété dans le concentrateur.</span><span class="sxs-lookup"><span data-stu-id="54699-112">The user identifier for a connection can be accessed by the `Context.UserIdentifier` property in the hub.</span></span>

<span data-ttu-id="54699-113">Par défaut, SignalR utilise `ClaimTypes.NameIdentifier` à partir du `ClaimsPrincipal` associé à la connexion comme identificateur d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="54699-113">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> <span data-ttu-id="54699-114">Pour personnaliser ce comportement, consultez [utiliser des revendications pour personnaliser la gestion des identités](xref:signalr/authn-and-authz#use-claims-to-customize-identity-handling).</span><span class="sxs-lookup"><span data-stu-id="54699-114">To customize this behavior, see [Use claims to customize identity handling](xref:signalr/authn-and-authz#use-claims-to-customize-identity-handling).</span></span>

<span data-ttu-id="54699-115">Envoyez un message à un utilisateur spécifique en passant l’identificateur d’utilisateur à la `User` fonction dans une méthode de concentrateur, comme illustré dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="54699-115">Send a message to a specific user by passing the user identifier to the `User` function in a hub method, as shown in the following example:</span></span>

> [!NOTE]
> <span data-ttu-id="54699-116">L’identificateur d’utilisateur respecte la casse.</span><span class="sxs-lookup"><span data-stu-id="54699-116">The user identifier is case-sensitive.</span></span>

[!code-csharp[Configure service](groups/sample/Hubs/ChatHub.cs?range=29-32)]

## <a name="groups-in-no-locsignalr"></a><span data-ttu-id="54699-117">Groupes dans SignalR</span><span class="sxs-lookup"><span data-stu-id="54699-117">Groups in SignalR</span></span>

<span data-ttu-id="54699-118">Un groupe est une collection de connexions associées à un nom.</span><span class="sxs-lookup"><span data-stu-id="54699-118">A group is a collection of connections associated with a name.</span></span> <span data-ttu-id="54699-119">Les messages peuvent être envoyés à toutes les connexions d’un groupe.</span><span class="sxs-lookup"><span data-stu-id="54699-119">Messages can be sent to all connections in a group.</span></span> <span data-ttu-id="54699-120">Les groupes sont la méthode recommandée pour l’envoi à une connexion ou à plusieurs connexions, car les groupes sont gérés par l’application.</span><span class="sxs-lookup"><span data-stu-id="54699-120">Groups are the recommended way to send to a connection or multiple connections because the groups are managed by the application.</span></span> <span data-ttu-id="54699-121">Une connexion peut être membre de plusieurs groupes.</span><span class="sxs-lookup"><span data-stu-id="54699-121">A connection can be a member of multiple groups.</span></span> <span data-ttu-id="54699-122">Les groupes sont idéaux pour une application de conversation, où chaque pièce peut être représentée en tant que groupe.</span><span class="sxs-lookup"><span data-stu-id="54699-122">Groups are ideal for something like a chat application, where each room can be represented as a group.</span></span> <span data-ttu-id="54699-123">Les connexions sont ajoutées ou supprimées des groupes via `AddToGroupAsync` les `RemoveFromGroupAsync` méthodes et.</span><span class="sxs-lookup"><span data-stu-id="54699-123">Connections are added to or removed from groups via the `AddToGroupAsync` and `RemoveFromGroupAsync` methods.</span></span>

[!code-csharp[Hub methods](groups/sample/Hubs/ChatHub.cs?range=15-27)]

<span data-ttu-id="54699-124">L’appartenance au groupe n’est pas conservée lorsqu’une connexion se reconnecte.</span><span class="sxs-lookup"><span data-stu-id="54699-124">Group membership isn't preserved when a connection reconnects.</span></span> <span data-ttu-id="54699-125">La connexion doit rejoindre le groupe lorsqu’elle est rétablie.</span><span class="sxs-lookup"><span data-stu-id="54699-125">The connection needs to rejoin the group when it's re-established.</span></span> <span data-ttu-id="54699-126">Il n’est pas possible de compter les membres d’un groupe, car ces informations ne sont pas disponibles si l’application est mise à l’échelle sur plusieurs serveurs.</span><span class="sxs-lookup"><span data-stu-id="54699-126">It's not possible to count the members of a group, since this information is not available if the application is scaled to multiple servers.</span></span>

<span data-ttu-id="54699-127">Pour protéger l’accès aux ressources lors de l’utilisation de groupes, utilisez la fonctionnalité d' [authentification et d’autorisation](xref:signalr/authn-and-authz) dans ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="54699-127">To protect access to resources while using groups, use [authentication and authorization](xref:signalr/authn-and-authz) functionality in ASP.NET Core.</span></span> <span data-ttu-id="54699-128">Si un utilisateur est ajouté à un groupe uniquement lorsque les informations d’identification sont valides pour ce groupe, les messages envoyés à ce groupe sont uniquement dirigés vers les utilisateurs autorisés.</span><span class="sxs-lookup"><span data-stu-id="54699-128">If a user is added to a group only when the credentials are valid for that group, messages sent to that group will only go to authorized users.</span></span> <span data-ttu-id="54699-129">Toutefois, les groupes ne sont pas une fonctionnalité de sécurité.</span><span class="sxs-lookup"><span data-stu-id="54699-129">However, groups are not a security feature.</span></span> <span data-ttu-id="54699-130">Les revendications d’authentification ont des fonctionnalités que les groupes n’ont pas, telles que l’expiration et la révocation.</span><span class="sxs-lookup"><span data-stu-id="54699-130">Authentication claims have features that groups do not, such as expiry and revocation.</span></span> <span data-ttu-id="54699-131">Si l’autorisation d’un utilisateur pour accéder au groupe est révoquée, l’application doit supprimer l’utilisateur du groupe de manière explicite.</span><span class="sxs-lookup"><span data-stu-id="54699-131">If a user's permission to access the group is revoked, the app must remove the user from the group explicitly.</span></span>

> [!NOTE]
> <span data-ttu-id="54699-132">Les noms de groupes respectent la casse.</span><span class="sxs-lookup"><span data-stu-id="54699-132">Group names are case-sensitive.</span></span>

## <a name="related-resources"></a><span data-ttu-id="54699-133">Ressources associées</span><span class="sxs-lookup"><span data-stu-id="54699-133">Related resources</span></span>

* [<span data-ttu-id="54699-134">Prise en main</span><span class="sxs-lookup"><span data-stu-id="54699-134">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="54699-135">Hubs</span><span class="sxs-lookup"><span data-stu-id="54699-135">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="54699-136">Publication dans Azure</span><span class="sxs-lookup"><span data-stu-id="54699-136">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
