---
title: Gestion de l’état des ASP.NET Core Blazor
author: guardrex
description: Découvrez comment rendre l’état persistant dans les Blazor Server applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
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
uid: blazor/state-management
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 5b7a6fefd13d7255fbe3ae7010df48c6211fa755
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97854325"
---
# <a name="aspnet-core-no-locblazor-state-management"></a><span data-ttu-id="db98f-103">Gestion de l’état des ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="db98f-103">ASP.NET Core Blazor state management</span></span>

<span data-ttu-id="db98f-104">Par [Steve Sanderson](https://github.com/SteveSandersonMS) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="db98f-104">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="db98f-105">L’état utilisateur créé dans une Blazor WebAssembly application est conservé dans la mémoire du navigateur.</span><span class="sxs-lookup"><span data-stu-id="db98f-105">User state created in a Blazor WebAssembly app is held in the browser's memory.</span></span>

<span data-ttu-id="db98f-106">Voici quelques exemples d’état utilisateur contenu dans la mémoire du navigateur :</span><span class="sxs-lookup"><span data-stu-id="db98f-106">Examples of user state held in browser memory include:</span></span>

* <span data-ttu-id="db98f-107">La hiérarchie des instances de composant et leur sortie de rendu la plus récente dans l’interface utilisateur rendue.</span><span class="sxs-lookup"><span data-stu-id="db98f-107">The hierarchy of component instances and their most recent render output in the rendered UI.</span></span>
* <span data-ttu-id="db98f-108">Valeurs des champs et des propriétés dans les instances de composant.</span><span class="sxs-lookup"><span data-stu-id="db98f-108">The values of fields and properties in component instances.</span></span>
* <span data-ttu-id="db98f-109">Données conservées dans des instances de service d' [injection de dépendances](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="db98f-109">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances.</span></span>
* <span data-ttu-id="db98f-110">Valeurs définies par le biais d’appels [Interop JavaScript](xref:blazor/call-javascript-from-dotnet) .</span><span class="sxs-lookup"><span data-stu-id="db98f-110">Values set through [JavaScript interop](xref:blazor/call-javascript-from-dotnet) calls.</span></span>

<span data-ttu-id="db98f-111">Lorsqu’un utilisateur ferme et ouvre à nouveau son navigateur ou recharge la page, l’état utilisateur contenu dans la mémoire du navigateur est perdu.</span><span class="sxs-lookup"><span data-stu-id="db98f-111">When a user closes and re-opens their browser or reloads the page, user state held in the browser's memory is lost.</span></span>

> [!NOTE]
> <span data-ttu-id="db98f-112">Le [stockage du navigateur protégé](xref:blazor/state-management?pivots=server#aspnet-core-protected-browser-storage) ( <xref:Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage?displayProperty=fullName> espace de noms) s’appuie sur la Protection des données ASP.net Core et n’est pris en charge que pour les Blazor Server applications.</span><span class="sxs-lookup"><span data-stu-id="db98f-112">[Protected Browser Storage](xref:blazor/state-management?pivots=server#aspnet-core-protected-browser-storage) (<xref:Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage?displayProperty=fullName> namespace) relies on ASP.NET Core Data Protection and is only supported for Blazor Server apps.</span></span>

## <a name="persist-state-across-browser-sessions"></a><span data-ttu-id="db98f-113">Conserver l’état entre les sessions de navigateur</span><span class="sxs-lookup"><span data-stu-id="db98f-113">Persist state across browser sessions</span></span>

<span data-ttu-id="db98f-114">En règle générale, conservez l’état entre les sessions de navigateur où les utilisateurs créent activement des données, et non simplement des données qui existent déjà.</span><span class="sxs-lookup"><span data-stu-id="db98f-114">Generally, maintain state across browser sessions where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="db98f-115">Pour conserver l’état entre les sessions de navigateur, l’application doit conserver les données dans un autre emplacement de stockage que la mémoire du navigateur.</span><span class="sxs-lookup"><span data-stu-id="db98f-115">To preserve state across browser sessions, the app must persist the data to some other storage location than the browser's memory.</span></span> <span data-ttu-id="db98f-116">La persistance de l’État n’est pas automatique.</span><span class="sxs-lookup"><span data-stu-id="db98f-116">State persistence isn't automatic.</span></span> <span data-ttu-id="db98f-117">Vous devez prendre des mesures lors du développement de l’application pour implémenter la persistance des données avec état.</span><span class="sxs-lookup"><span data-stu-id="db98f-117">You must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="db98f-118">La persistance des données est généralement requise uniquement pour l’état de valeur élevée que les utilisateurs ont consacrés à la création.</span><span class="sxs-lookup"><span data-stu-id="db98f-118">Data persistence is typically only required for high-value state that users expended effort to create.</span></span> <span data-ttu-id="db98f-119">Dans les exemples suivants, l’état persistant fait gagner du temps ou contribue à des activités commerciales :</span><span class="sxs-lookup"><span data-stu-id="db98f-119">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="db98f-120">Web Forms à plusieurs étapes : il est fastidieux pour un utilisateur de saisir à nouveau des données pour plusieurs étapes terminées d’un formulaire Web à plusieurs étapes si leur état est perdu.</span><span class="sxs-lookup"><span data-stu-id="db98f-120">Multi-step web forms: It's time-consuming for a user to re-enter data for several completed steps of a multi-step web form if their state is lost.</span></span> <span data-ttu-id="db98f-121">Un utilisateur perd l’État dans ce scénario s’il quitte le formulaire et le retourne plus tard.</span><span class="sxs-lookup"><span data-stu-id="db98f-121">A user loses state in this scenario if they navigate away from the form and return later.</span></span>
* <span data-ttu-id="db98f-122">Paniers d’achat : tout composant commercialement important d’une application qui représente un chiffre d’affaires potentiel peut être maintenu.</span><span class="sxs-lookup"><span data-stu-id="db98f-122">Shopping carts: Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="db98f-123">Un utilisateur qui perd son état et, par conséquent, son panier, peut acheter moins de produits ou de services lorsqu’ils reviennent sur le site ultérieurement.</span><span class="sxs-lookup"><span data-stu-id="db98f-123">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="db98f-124">Une application peut conserver uniquement l’état de l' *application*.</span><span class="sxs-lookup"><span data-stu-id="db98f-124">An app can only persist *app state*.</span></span> <span data-ttu-id="db98f-125">Les interfaces utilisateur ne peuvent pas être rendues persistantes, telles que les instances de composant et leurs arborescences de rendu.</span><span class="sxs-lookup"><span data-stu-id="db98f-125">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="db98f-126">Les composants et les arborescences de rendu ne sont généralement pas sérialisables.</span><span class="sxs-lookup"><span data-stu-id="db98f-126">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="db98f-127">Pour conserver l’état de l’interface utilisateur, tel que les nœuds développés d’un contrôle Tree View, l’application doit utiliser du code personnalisé pour modéliser le comportement de l’état de l’interface utilisateur en tant qu’État de l’application sérialisable.</span><span class="sxs-lookup"><span data-stu-id="db98f-127">To persist UI state, such as the expanded nodes of a tree view control, the app must use custom code to model the behavior of the UI state as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="db98f-128">Emplacement de conservation de l’État</span><span class="sxs-lookup"><span data-stu-id="db98f-128">Where to persist state</span></span>

<span data-ttu-id="db98f-129">Les emplacements communs existent pour l’état de persistance :</span><span class="sxs-lookup"><span data-stu-id="db98f-129">Common locations exist for persisting state:</span></span>

* [<span data-ttu-id="db98f-130">Stockage côté serveur</span><span class="sxs-lookup"><span data-stu-id="db98f-130">Server-side storage</span></span>](#server-side-storage-wasm)
* [<span data-ttu-id="db98f-131">URL</span><span class="sxs-lookup"><span data-stu-id="db98f-131">URL</span></span>](#url-wasm)
* [<span data-ttu-id="db98f-132">Stockage du navigateur</span><span class="sxs-lookup"><span data-stu-id="db98f-132">Browser storage</span></span>](#browser-storage-wasm)
* [<span data-ttu-id="db98f-133">Service de conteneur d’État en mémoire</span><span class="sxs-lookup"><span data-stu-id="db98f-133">In-memory state container service</span></span>](#in-memory-state-container-service-wasm)

<h2 id="server-side-storage-wasm"><span data-ttu-id="db98f-134">Stockage côté serveur</span><span class="sxs-lookup"><span data-stu-id="db98f-134">Server-side storage</span></span></h2>

<span data-ttu-id="db98f-135">Pour la persistance des données permanente qui s’étend sur plusieurs utilisateurs et appareils, l’application peut utiliser un stockage indépendant côté serveur accessible via une API Web.</span><span class="sxs-lookup"><span data-stu-id="db98f-135">For permanent data persistence that spans multiple users and devices, the app can use independent server-side storage accessed via a web API.</span></span> <span data-ttu-id="db98f-136">Options disponibles :</span><span class="sxs-lookup"><span data-stu-id="db98f-136">Options include:</span></span>

* <span data-ttu-id="db98f-137">Stockage d'objets blob</span><span class="sxs-lookup"><span data-stu-id="db98f-137">Blob storage</span></span>
* <span data-ttu-id="db98f-138">Stockage clé-valeur</span><span class="sxs-lookup"><span data-stu-id="db98f-138">Key-value storage</span></span>
* <span data-ttu-id="db98f-139">Base de données relationnelle</span><span class="sxs-lookup"><span data-stu-id="db98f-139">Relational database</span></span>
* <span data-ttu-id="db98f-140">Stockage de tables</span><span class="sxs-lookup"><span data-stu-id="db98f-140">Table storage</span></span>

<span data-ttu-id="db98f-141">Une fois les données enregistrées, l’état de l’utilisateur est conservé et disponible dans toute nouvelle session de navigateur.</span><span class="sxs-lookup"><span data-stu-id="db98f-141">After data is saved, the user's state is retained and available in any new browser session.</span></span>

<span data-ttu-id="db98f-142">Étant donné que Blazor WebAssembly les applications s’exécutent entièrement dans le navigateur de l’utilisateur, elles nécessitent des mesures supplémentaires pour accéder à des systèmes externes sécurisés, tels que les services de stockage et les bases de données.</span><span class="sxs-lookup"><span data-stu-id="db98f-142">Because Blazor WebAssembly apps run entirely in the user's browser, they require additional measures to access secure external systems, such as storage services and databases.</span></span> <span data-ttu-id="db98f-143">Blazor WebAssembly les applications sont sécurisées de la même façon que les applications à page unique (SPAs).</span><span class="sxs-lookup"><span data-stu-id="db98f-143">Blazor WebAssembly apps are secured in the same manner as Single Page Applications (SPAs).</span></span> <span data-ttu-id="db98f-144">En règle générale, une application authentifie un utilisateur via [OAuth](https://oauth.net) / [OpenID Connect (OIDC)](https://openid.net/connect/) , puis interagit avec les services de stockage et les bases de données par le biais d’appels d’API Web à une application côté serveur.</span><span class="sxs-lookup"><span data-stu-id="db98f-144">Typically, an app authenticates a user via [OAuth](https://oauth.net)/[OpenID Connect (OIDC)](https://openid.net/connect/) and then interacts with storage services and databases through web API calls to a server-side app.</span></span> <span data-ttu-id="db98f-145">L’application côté serveur convertit le transfert de données entre l' Blazor WebAssembly application et le service de stockage ou la base de données.</span><span class="sxs-lookup"><span data-stu-id="db98f-145">The server-side app mediates the transfer of data between the Blazor WebAssembly app and the storage service or database.</span></span> <span data-ttu-id="db98f-146">L' Blazor WebAssembly application maintient une connexion éphémère à l’application côté serveur, tandis que l’application côté serveur dispose d’une connexion permanente au stockage.</span><span class="sxs-lookup"><span data-stu-id="db98f-146">The Blazor WebAssembly app maintains an ephemeral connection to the server-side app, while the server-side app has a persistent connection to storage.</span></span>

<span data-ttu-id="db98f-147">Pour plus d’informations, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="db98f-147">For more information, see the following resources:</span></span>

* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>
* <span data-ttu-id="db98f-148">Blazor*Sécurité et Identity* traitant</span><span class="sxs-lookup"><span data-stu-id="db98f-148">Blazor *Security and Identity* articles</span></span>

<span data-ttu-id="db98f-149">Pour plus d’informations sur les options de stockage de données Azure, consultez les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="db98f-149">For more information on Azure data storage options, see the following:</span></span>

* [<span data-ttu-id="db98f-150">Bases de données Azure</span><span class="sxs-lookup"><span data-stu-id="db98f-150">Azure Databases</span></span>](https://azure.microsoft.com/product-categories/databases/)
* [<span data-ttu-id="db98f-151">Documentation d’Azure Storage</span><span class="sxs-lookup"><span data-stu-id="db98f-151">Azure Storage Documentation</span></span>](/azure/storage/)

<h2 id="url-wasm"><span data-ttu-id="db98f-152">URL</span><span class="sxs-lookup"><span data-stu-id="db98f-152">URL</span></span></h2>

<span data-ttu-id="db98f-153">Pour les données temporaires représentant l’état de navigation, modélisez les données en tant que partie de l’URL.</span><span class="sxs-lookup"><span data-stu-id="db98f-153">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="db98f-154">Voici des exemples d’état utilisateur modélisé dans l’URL :</span><span class="sxs-lookup"><span data-stu-id="db98f-154">Examples of user state modeled in the URL include:</span></span>

* <span data-ttu-id="db98f-155">ID d’une entité affichée.</span><span class="sxs-lookup"><span data-stu-id="db98f-155">The ID of a viewed entity.</span></span>
* <span data-ttu-id="db98f-156">Numéro de page actuel dans une grille paginée.</span><span class="sxs-lookup"><span data-stu-id="db98f-156">The current page number in a paged grid.</span></span>

<span data-ttu-id="db98f-157">Le contenu de la barre d’adresse du navigateur est conservé si l’utilisateur recharge manuellement la page.</span><span class="sxs-lookup"><span data-stu-id="db98f-157">The contents of the browser's address bar are retained if the user manually reloads the page.</span></span>

<span data-ttu-id="db98f-158">Pour plus d’informations sur la définition de modèles d’URL avec la [`@page`](xref:mvc/views/razor#page) directive, consultez <xref:blazor/fundamentals/routing> .</span><span class="sxs-lookup"><span data-stu-id="db98f-158">For information on defining URL patterns with the [`@page`](xref:mvc/views/razor#page) directive, see <xref:blazor/fundamentals/routing>.</span></span>

<h2 id="browser-storage-wasm"><span data-ttu-id="db98f-159">Stockage du navigateur</span><span class="sxs-lookup"><span data-stu-id="db98f-159">Browser storage</span></span></h2>

<span data-ttu-id="db98f-160">Pour les données temporaires que l’utilisateur crée activement, un emplacement de stockage couramment utilisé est celui des [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) regroupements et du navigateur :</span><span class="sxs-lookup"><span data-stu-id="db98f-160">For transient data that the user is actively creating, a commonly used storage location is the browser's [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) collections:</span></span>

* <span data-ttu-id="db98f-161">`localStorage` est limité à la fenêtre du navigateur.</span><span class="sxs-lookup"><span data-stu-id="db98f-161">`localStorage` is scoped to the browser's window.</span></span> <span data-ttu-id="db98f-162">Si l’utilisateur recharge la page ou ferme et ouvre à nouveau le navigateur, l’état persiste.</span><span class="sxs-lookup"><span data-stu-id="db98f-162">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="db98f-163">Si l’utilisateur ouvre plusieurs onglets de navigateur, l’État est partagé à travers les onglets.</span><span class="sxs-lookup"><span data-stu-id="db98f-163">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="db98f-164">Les données sont conservées dans `localStorage` jusqu’à ce qu’elles soient explicitement effacées.</span><span class="sxs-lookup"><span data-stu-id="db98f-164">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="db98f-165">`sessionStorage` est étendu à l’onglet navigateur. Si l’utilisateur recharge l’onglet, l’état persiste.</span><span class="sxs-lookup"><span data-stu-id="db98f-165">`sessionStorage` is scoped to the browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="db98f-166">Si l’utilisateur ferme l’onglet ou le navigateur, l’État est perdu.</span><span class="sxs-lookup"><span data-stu-id="db98f-166">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="db98f-167">Si l’utilisateur ouvre plusieurs onglets de navigateur, chaque onglet possède sa propre version indépendante des données.</span><span class="sxs-lookup"><span data-stu-id="db98f-167">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

> [!NOTE]
> <span data-ttu-id="db98f-168">`localStorage` et `sessionStorage` peuvent être utilisés dans les Blazor WebAssembly applications, mais uniquement en écrivant du code personnalisé ou à l’aide d’un package tiers.</span><span class="sxs-lookup"><span data-stu-id="db98f-168">`localStorage` and `sessionStorage` can be used in Blazor WebAssembly apps but only by writing custom code or using a third-party package.</span></span>

<span data-ttu-id="db98f-169">En règle générale, `sessionStorage` il est plus sûr d’utiliser.</span><span class="sxs-lookup"><span data-stu-id="db98f-169">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="db98f-170">`sessionStorage` évite le risque qu’un utilisateur ouvre plusieurs onglets et rencontre les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="db98f-170">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="db98f-171">Bogues dans le stockage d’État sur les onglets.</span><span class="sxs-lookup"><span data-stu-id="db98f-171">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="db98f-172">Comportement confus quand une tabulation remplace l’état d’autres onglets.</span><span class="sxs-lookup"><span data-stu-id="db98f-172">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="db98f-173">`localStorage` est le meilleur choix si l’application doit conserver l’État dans la fermeture et la réouverture du navigateur.</span><span class="sxs-lookup"><span data-stu-id="db98f-173">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

> [!WARNING]
> <span data-ttu-id="db98f-174">Les utilisateurs peuvent afficher ou altérer les données stockées dans `localStorage` et `sessionStorage` .</span><span class="sxs-lookup"><span data-stu-id="db98f-174">Users may view or tamper with the data stored in `localStorage` and `sessionStorage`.</span></span>

<h2 id="in-memory-state-container-service-wasm"><span data-ttu-id="db98f-175">Service de conteneur d’État en mémoire</span><span class="sxs-lookup"><span data-stu-id="db98f-175">In-memory state container service</span></span></h2>

[!INCLUDE[](~/blazor/includes/state-container.md)]

## <a name="additional-resources"></a><span data-ttu-id="db98f-176">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="db98f-176">Additional resources</span></span>

* [<span data-ttu-id="db98f-177">Enregistrer l’état de l’application avant une opération d’authentification</span><span class="sxs-lookup"><span data-stu-id="db98f-177">Save app state before an authentication operation</span></span>](xref:blazor/security/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation)
* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>

::: zone-end

::: zone pivot="server"

<span data-ttu-id="db98f-178">Blazor Server est une infrastructure d’application avec état.</span><span class="sxs-lookup"><span data-stu-id="db98f-178">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="db98f-179">La plupart du temps, l’application maintient une connexion au serveur.</span><span class="sxs-lookup"><span data-stu-id="db98f-179">Most of the time, the app maintains a connection to the server.</span></span> <span data-ttu-id="db98f-180">L’état de l’utilisateur est conservé dans la mémoire du serveur dans un *circuit*.</span><span class="sxs-lookup"><span data-stu-id="db98f-180">The user's state is held in the server's memory in a *circuit*.</span></span> 

<span data-ttu-id="db98f-181">Voici des exemples d’état utilisateur contenu dans un circuit :</span><span class="sxs-lookup"><span data-stu-id="db98f-181">Examples of user state held in a circuit include:</span></span>

* <span data-ttu-id="db98f-182">La hiérarchie des instances de composant et leur sortie de rendu la plus récente dans l’interface utilisateur rendue.</span><span class="sxs-lookup"><span data-stu-id="db98f-182">The hierarchy of component instances and their most recent render output in the rendered UI.</span></span>
* <span data-ttu-id="db98f-183">Valeurs des champs et des propriétés dans les instances de composant.</span><span class="sxs-lookup"><span data-stu-id="db98f-183">The values of fields and properties in component instances.</span></span>
* <span data-ttu-id="db98f-184">Données conservées dans des instances de service d' [injection de dépendance (di)](xref:fundamentals/dependency-injection) dont l’étendue correspond au circuit.</span><span class="sxs-lookup"><span data-stu-id="db98f-184">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span>

<span data-ttu-id="db98f-185">L’état utilisateur peut également être trouvé dans les variables JavaScript dans le jeu de mémoire du navigateur via des appels [Interop JavaScript](xref:blazor/call-javascript-from-dotnet) .</span><span class="sxs-lookup"><span data-stu-id="db98f-185">User state might also be found in JavaScript variables in the browser's memory set via [JavaScript interop](xref:blazor/call-javascript-from-dotnet) calls.</span></span>

<span data-ttu-id="db98f-186">Si un utilisateur subit une perte de connexion réseau temporaire, Blazor tente de reconnecter l’utilisateur à son circuit d’origine avec son état d’origine.</span><span class="sxs-lookup"><span data-stu-id="db98f-186">If a user experiences a temporary network connection loss, Blazor attempts to reconnect the user to their original circuit with their original state.</span></span> <span data-ttu-id="db98f-187">Toutefois, la reconnexion d’un utilisateur à son circuit d’origine dans la mémoire du serveur n’est pas toujours possible :</span><span class="sxs-lookup"><span data-stu-id="db98f-187">However, reconnecting a user to their original circuit in the server's memory isn't always possible:</span></span>

* <span data-ttu-id="db98f-188">Le serveur ne peut pas conserver un circuit déconnecté de façon infinie.</span><span class="sxs-lookup"><span data-stu-id="db98f-188">The server can't retain a disconnected circuit forever.</span></span> <span data-ttu-id="db98f-189">Le serveur doit libérer un circuit déconnecté après un délai d’attente ou lorsque le serveur subit une sollicitation de la mémoire.</span><span class="sxs-lookup"><span data-stu-id="db98f-189">The server must release a disconnected circuit after a timeout or when the server is under memory pressure.</span></span>
* <span data-ttu-id="db98f-190">Dans les environnements de déploiement avec équilibrage de charge sur plusieurs serveurs, les serveurs individuels peuvent échouer ou être automatiquement supprimés lorsqu’ils ne sont plus nécessaires pour gérer le volume global de demandes.</span><span class="sxs-lookup"><span data-stu-id="db98f-190">In multi-server, load-balanced deployment environments, individual servers may fail or be automatically removed when no longer required to handle the overall volume of requests.</span></span> <span data-ttu-id="db98f-191">Le serveur d’origine qui traite les demandes pour un utilisateur peut devenir indisponible lorsque l’utilisateur tente de se reconnecter.</span><span class="sxs-lookup"><span data-stu-id="db98f-191">The original server processing requests for a user may become unavailable when the user attempts to reconnect.</span></span>
* <span data-ttu-id="db98f-192">L’utilisateur peut fermer et rouvrir son navigateur ou recharger la page, ce qui supprime tout État contenu dans la mémoire du navigateur.</span><span class="sxs-lookup"><span data-stu-id="db98f-192">The user might close and re-open their browser or reload the page, which removes any state held in the browser's memory.</span></span> <span data-ttu-id="db98f-193">Par exemple, les valeurs des variables JavaScript définies par le biais d’appels Interop JavaScript sont perdues.</span><span class="sxs-lookup"><span data-stu-id="db98f-193">For example, JavaScript variable values set through JavaScript interop calls are lost.</span></span>

<span data-ttu-id="db98f-194">Lorsqu’un utilisateur ne peut pas être reconnecté à son circuit d’origine, l’utilisateur reçoit un nouveau circuit avec un état vide.</span><span class="sxs-lookup"><span data-stu-id="db98f-194">When a user can't be reconnected to their original circuit, the user receives a new circuit with an empty state.</span></span> <span data-ttu-id="db98f-195">Cela équivaut à fermer et à rouvrir une application de bureau.</span><span class="sxs-lookup"><span data-stu-id="db98f-195">This is equivalent to closing and re-opening a desktop app.</span></span>

## <a name="persist-state-across-circuits"></a><span data-ttu-id="db98f-196">Conserver l’état entre les circuits</span><span class="sxs-lookup"><span data-stu-id="db98f-196">Persist state across circuits</span></span>

<span data-ttu-id="db98f-197">En règle générale, conservez l’état entre les circuits où les utilisateurs créent activement des données, et non simplement des données qui existent déjà.</span><span class="sxs-lookup"><span data-stu-id="db98f-197">Generally, maintain state across circuits where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="db98f-198">Pour conserver l’état entre les circuits, l’application doit conserver les données dans un autre emplacement de stockage que la mémoire du serveur.</span><span class="sxs-lookup"><span data-stu-id="db98f-198">To preserve state across circuits, the app must persist the data to some other storage location than the server's memory.</span></span> <span data-ttu-id="db98f-199">La persistance de l’État n’est pas automatique.</span><span class="sxs-lookup"><span data-stu-id="db98f-199">State persistence isn't automatic.</span></span> <span data-ttu-id="db98f-200">Vous devez prendre des mesures lors du développement de l’application pour implémenter la persistance des données avec état.</span><span class="sxs-lookup"><span data-stu-id="db98f-200">You must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="db98f-201">La persistance des données est généralement requise uniquement pour l’état de valeur élevée que les utilisateurs ont consacrés à la création.</span><span class="sxs-lookup"><span data-stu-id="db98f-201">Data persistence is typically only required for high-value state that users expended effort to create.</span></span> <span data-ttu-id="db98f-202">Dans les exemples suivants, l’état persistant fait gagner du temps ou contribue à des activités commerciales :</span><span class="sxs-lookup"><span data-stu-id="db98f-202">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="db98f-203">Web Forms à plusieurs étapes : il est fastidieux pour un utilisateur de saisir à nouveau des données pour plusieurs étapes terminées d’un formulaire Web à plusieurs étapes si leur état est perdu.</span><span class="sxs-lookup"><span data-stu-id="db98f-203">Multi-step web forms: It's time-consuming for a user to re-enter data for several completed steps of a multi-step web form if their state is lost.</span></span> <span data-ttu-id="db98f-204">Un utilisateur perd l’État dans ce scénario s’il quitte le formulaire et le retourne plus tard.</span><span class="sxs-lookup"><span data-stu-id="db98f-204">A user loses state in this scenario if they navigate away from the form and return later.</span></span>
* <span data-ttu-id="db98f-205">Paniers d’achat : tout composant commercialement important d’une application qui représente un chiffre d’affaires potentiel peut être maintenu.</span><span class="sxs-lookup"><span data-stu-id="db98f-205">Shopping carts: Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="db98f-206">Un utilisateur qui perd son état et, par conséquent, son panier, peut acheter moins de produits ou de services lorsqu’ils reviennent sur le site ultérieurement.</span><span class="sxs-lookup"><span data-stu-id="db98f-206">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="db98f-207">Une application peut conserver uniquement l’état de l' *application*.</span><span class="sxs-lookup"><span data-stu-id="db98f-207">An app can only persist *app state*.</span></span> <span data-ttu-id="db98f-208">Les interfaces utilisateur ne peuvent pas être rendues persistantes, telles que les instances de composant et leurs arborescences de rendu.</span><span class="sxs-lookup"><span data-stu-id="db98f-208">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="db98f-209">Les composants et les arborescences de rendu ne sont généralement pas sérialisables.</span><span class="sxs-lookup"><span data-stu-id="db98f-209">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="db98f-210">Pour conserver l’état de l’interface utilisateur, tel que les nœuds développés d’un contrôle Tree View, l’application doit utiliser du code personnalisé pour modéliser le comportement de l’état de l’interface utilisateur en tant qu’État de l’application sérialisable.</span><span class="sxs-lookup"><span data-stu-id="db98f-210">To persist UI state, such as the expanded nodes of a tree view control, the app must use custom code to model the behavior of the UI state as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="db98f-211">Emplacement de conservation de l’État</span><span class="sxs-lookup"><span data-stu-id="db98f-211">Where to persist state</span></span>

<span data-ttu-id="db98f-212">Les emplacements communs existent pour l’état de persistance :</span><span class="sxs-lookup"><span data-stu-id="db98f-212">Common locations exist for persisting state:</span></span>

* [<span data-ttu-id="db98f-213">Stockage côté serveur</span><span class="sxs-lookup"><span data-stu-id="db98f-213">Server-side storage</span></span>](#server-side-storage-server)
* [<span data-ttu-id="db98f-214">URL</span><span class="sxs-lookup"><span data-stu-id="db98f-214">URL</span></span>](#url-server)
* [<span data-ttu-id="db98f-215">Stockage du navigateur</span><span class="sxs-lookup"><span data-stu-id="db98f-215">Browser storage</span></span>](#browser-storage-server)
* [<span data-ttu-id="db98f-216">Service de conteneur d’État en mémoire</span><span class="sxs-lookup"><span data-stu-id="db98f-216">In-memory state container service</span></span>](#in-memory-state-container-service-server)

<h2 id="server-side-storage-server"><span data-ttu-id="db98f-217">Stockage côté serveur</span><span class="sxs-lookup"><span data-stu-id="db98f-217">Server-side storage</span></span></h2>

<span data-ttu-id="db98f-218">Pour la persistance des données permanente qui s’étend sur plusieurs utilisateurs et appareils, l’application peut utiliser le stockage côté serveur.</span><span class="sxs-lookup"><span data-stu-id="db98f-218">For permanent data persistence that spans multiple users and devices, the app can use server-side storage.</span></span> <span data-ttu-id="db98f-219">Options disponibles :</span><span class="sxs-lookup"><span data-stu-id="db98f-219">Options include:</span></span>

* <span data-ttu-id="db98f-220">Stockage d'objets blob</span><span class="sxs-lookup"><span data-stu-id="db98f-220">Blob storage</span></span>
* <span data-ttu-id="db98f-221">Stockage clé-valeur</span><span class="sxs-lookup"><span data-stu-id="db98f-221">Key-value storage</span></span>
* <span data-ttu-id="db98f-222">Base de données relationnelle</span><span class="sxs-lookup"><span data-stu-id="db98f-222">Relational database</span></span>
* <span data-ttu-id="db98f-223">Stockage de tables</span><span class="sxs-lookup"><span data-stu-id="db98f-223">Table storage</span></span>

<span data-ttu-id="db98f-224">Une fois les données enregistrées, l’état de l’utilisateur est conservé et disponible dans n’importe quel nouveau circuit.</span><span class="sxs-lookup"><span data-stu-id="db98f-224">After data is saved, the user's state is retained and available in any new circuit.</span></span>

<span data-ttu-id="db98f-225">Pour plus d’informations sur les options de stockage de données Azure, consultez les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="db98f-225">For more information on Azure data storage options, see the following:</span></span>

* [<span data-ttu-id="db98f-226">Bases de données Azure</span><span class="sxs-lookup"><span data-stu-id="db98f-226">Azure Databases</span></span>](https://azure.microsoft.com/product-categories/databases/)
* [<span data-ttu-id="db98f-227">Documentation d’Azure Storage</span><span class="sxs-lookup"><span data-stu-id="db98f-227">Azure Storage Documentation</span></span>](/azure/storage/)

<h2 id="url-server"><span data-ttu-id="db98f-228">URL</span><span class="sxs-lookup"><span data-stu-id="db98f-228">URL</span></span></h2>

<span data-ttu-id="db98f-229">Pour les données temporaires représentant l’état de navigation, modélisez les données en tant que partie de l’URL.</span><span class="sxs-lookup"><span data-stu-id="db98f-229">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="db98f-230">Voici des exemples d’état utilisateur modélisé dans l’URL :</span><span class="sxs-lookup"><span data-stu-id="db98f-230">Examples of user state modeled in the URL include:</span></span>

* <span data-ttu-id="db98f-231">ID d’une entité affichée.</span><span class="sxs-lookup"><span data-stu-id="db98f-231">The ID of a viewed entity.</span></span>
* <span data-ttu-id="db98f-232">Numéro de page actuel dans une grille paginée.</span><span class="sxs-lookup"><span data-stu-id="db98f-232">The current page number in a paged grid.</span></span>

<span data-ttu-id="db98f-233">Le contenu de la barre d’adresse du navigateur est conservé :</span><span class="sxs-lookup"><span data-stu-id="db98f-233">The contents of the browser's address bar are retained:</span></span>

* <span data-ttu-id="db98f-234">Si l’utilisateur recharge manuellement la page.</span><span class="sxs-lookup"><span data-stu-id="db98f-234">If the user manually reloads the page.</span></span>
* <span data-ttu-id="db98f-235">Si le serveur Web devient indisponible et que l’utilisateur est obligé de recharger la page pour se connecter à un autre serveur.</span><span class="sxs-lookup"><span data-stu-id="db98f-235">If the web server becomes unavailable, and the user is forced to reload the page in order to connect to a different server.</span></span>

<span data-ttu-id="db98f-236">Pour plus d’informations sur la définition de modèles d’URL avec la [`@page`](xref:mvc/views/razor#page) directive, consultez <xref:blazor/fundamentals/routing> .</span><span class="sxs-lookup"><span data-stu-id="db98f-236">For information on defining URL patterns with the [`@page`](xref:mvc/views/razor#page) directive, see <xref:blazor/fundamentals/routing>.</span></span>

<h2 id="browser-storage-server"><span data-ttu-id="db98f-237">Stockage du navigateur</span><span class="sxs-lookup"><span data-stu-id="db98f-237">Browser storage</span></span></h2>

<span data-ttu-id="db98f-238">Pour les données temporaires que l’utilisateur crée activement, un emplacement de stockage couramment utilisé est celui des [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) regroupements et du navigateur :</span><span class="sxs-lookup"><span data-stu-id="db98f-238">For transient data that the user is actively creating, a commonly used storage location is the browser's [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) collections:</span></span>

* <span data-ttu-id="db98f-239">`localStorage` est limité à la fenêtre du navigateur.</span><span class="sxs-lookup"><span data-stu-id="db98f-239">`localStorage` is scoped to the browser's window.</span></span> <span data-ttu-id="db98f-240">Si l’utilisateur recharge la page ou ferme et ouvre à nouveau le navigateur, l’état persiste.</span><span class="sxs-lookup"><span data-stu-id="db98f-240">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="db98f-241">Si l’utilisateur ouvre plusieurs onglets de navigateur, l’État est partagé à travers les onglets.</span><span class="sxs-lookup"><span data-stu-id="db98f-241">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="db98f-242">Les données sont conservées dans `localStorage` jusqu’à ce qu’elles soient explicitement effacées.</span><span class="sxs-lookup"><span data-stu-id="db98f-242">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="db98f-243">`sessionStorage` est étendu à l’onglet navigateur. Si l’utilisateur recharge l’onglet, l’état persiste.</span><span class="sxs-lookup"><span data-stu-id="db98f-243">`sessionStorage` is scoped to the browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="db98f-244">Si l’utilisateur ferme l’onglet ou le navigateur, l’État est perdu.</span><span class="sxs-lookup"><span data-stu-id="db98f-244">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="db98f-245">Si l’utilisateur ouvre plusieurs onglets de navigateur, chaque onglet possède sa propre version indépendante des données.</span><span class="sxs-lookup"><span data-stu-id="db98f-245">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

<span data-ttu-id="db98f-246">En règle générale, `sessionStorage` il est plus sûr d’utiliser.</span><span class="sxs-lookup"><span data-stu-id="db98f-246">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="db98f-247">`sessionStorage` évite le risque qu’un utilisateur ouvre plusieurs onglets et rencontre les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="db98f-247">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="db98f-248">Bogues dans le stockage d’État sur les onglets.</span><span class="sxs-lookup"><span data-stu-id="db98f-248">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="db98f-249">Comportement confus quand une tabulation remplace l’état d’autres onglets.</span><span class="sxs-lookup"><span data-stu-id="db98f-249">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="db98f-250">`localStorage` est le meilleur choix si l’application doit conserver l’État dans la fermeture et la réouverture du navigateur.</span><span class="sxs-lookup"><span data-stu-id="db98f-250">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

<span data-ttu-id="db98f-251">Avertissements relatifs à l’utilisation du stockage du navigateur :</span><span class="sxs-lookup"><span data-stu-id="db98f-251">Caveats for using browser storage:</span></span>

* <span data-ttu-id="db98f-252">À l’instar de l’utilisation d’une base de données côté serveur, le chargement et l’enregistrement des données sont asynchrones.</span><span class="sxs-lookup"><span data-stu-id="db98f-252">Similar to the use of a server-side database, loading and saving data are asynchronous.</span></span>
* <span data-ttu-id="db98f-253">Contrairement à une base de données côté serveur, le stockage n’est pas disponible pendant le prérendu, car la page demandée n’existe pas dans le navigateur pendant l’étape de prérendu.</span><span class="sxs-lookup"><span data-stu-id="db98f-253">Unlike a server-side database, storage isn't available during prerendering because the requested page doesn't exist in the browser during the prerendering stage.</span></span>
* <span data-ttu-id="db98f-254">Le stockage de quelques kilo-octets de données est raisonnable à conserver pour les Blazor Server applications.</span><span class="sxs-lookup"><span data-stu-id="db98f-254">Storage of a few kilobytes of data is reasonable to persist for Blazor Server apps.</span></span> <span data-ttu-id="db98f-255">Au-delà de quelques kilo-octets, vous devez prendre en compte les implications en termes de performances, car les données sont chargées et enregistrées sur le réseau.</span><span class="sxs-lookup"><span data-stu-id="db98f-255">Beyond a few kilobytes, you must consider the performance implications because the data is loaded and saved across the network.</span></span>
* <span data-ttu-id="db98f-256">Les utilisateurs peuvent afficher ou altérer les données.</span><span class="sxs-lookup"><span data-stu-id="db98f-256">Users may view or tamper with the data.</span></span> <span data-ttu-id="db98f-257">La [protection des données ASP.net Core](xref:security/data-protection/introduction) peut atténuer le risque.</span><span class="sxs-lookup"><span data-stu-id="db98f-257">[ASP.NET Core Data Protection](xref:security/data-protection/introduction) can mitigate the risk.</span></span> <span data-ttu-id="db98f-258">Par exemple, [ASP.net Core stockage du navigateur protégé](#aspnet-core-protected-browser-storage) utilise la protection des données ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="db98f-258">For example, [ASP.NET Core Protected Browser Storage](#aspnet-core-protected-browser-storage) uses ASP.NET Core Data Protection.</span></span>

<span data-ttu-id="db98f-259">Les packages NuGet tiers fournissent des API pour travailler avec `localStorage` et `sessionStorage` .</span><span class="sxs-lookup"><span data-stu-id="db98f-259">Third-party NuGet packages provide APIs for working with `localStorage` and `sessionStorage`.</span></span> <span data-ttu-id="db98f-260">Il est préférable de choisir un package qui utilise en toute transparence [ASP.net Core la protection des données](xref:security/data-protection/introduction).</span><span class="sxs-lookup"><span data-stu-id="db98f-260">It's worth considering choosing a package that transparently uses [ASP.NET Core Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="db98f-261">La protection des données chiffre les données stockées et réduit le risque potentiel de falsification des données stockées.</span><span class="sxs-lookup"><span data-stu-id="db98f-261">Data Protection encrypts stored data and reduces the potential risk of tampering with stored data.</span></span> <span data-ttu-id="db98f-262">Si les données sérialisées JSON sont stockées en texte brut, les utilisateurs peuvent consulter les données à l’aide des outils de développement du navigateur et modifier également les données stockées.</span><span class="sxs-lookup"><span data-stu-id="db98f-262">If JSON-serialized data is stored in plain text, users can see the data using browser developer tools and also modify the stored data.</span></span> <span data-ttu-id="db98f-263">La sécurisation des données n’est pas toujours un problème, car les données peuvent être de nature insignifiante.</span><span class="sxs-lookup"><span data-stu-id="db98f-263">Securing data isn't always a problem because the data might be trivial in nature.</span></span> <span data-ttu-id="db98f-264">Par exemple, la lecture ou la modification de la couleur stockée d’un élément d’interface utilisateur n’est pas un risque de sécurité significatif pour l’utilisateur ou l’organisation.</span><span class="sxs-lookup"><span data-stu-id="db98f-264">For example, reading or modifying the stored color of a UI element isn't a significant security risk to the user or the organization.</span></span> <span data-ttu-id="db98f-265">Évitez d’autoriser les utilisateurs à inspecter ou altérer des *données sensibles*.</span><span class="sxs-lookup"><span data-stu-id="db98f-265">Avoid allowing users to inspect or tamper with *sensitive data*.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="aspnet-core-protected-browser-storage"></a><span data-ttu-id="db98f-266">Stockage du navigateur protégé ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="db98f-266">ASP.NET Core Protected Browser Storage</span></span>

<span data-ttu-id="db98f-267">ASP.NET Core le stockage protégé du navigateur tire parti de [ASP.net Core protection des données](xref:security/data-protection/introduction) pour [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) et [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) .</span><span class="sxs-lookup"><span data-stu-id="db98f-267">ASP.NET Core Protected Browser Storage leverages [ASP.NET Core Data Protection](xref:security/data-protection/introduction) for [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).</span></span>

> [!NOTE]
> <span data-ttu-id="db98f-268">Le stockage du navigateur protégé s’appuie sur la protection des données ASP.NET Core et n’est pris en charge que pour les Blazor Server applications.</span><span class="sxs-lookup"><span data-stu-id="db98f-268">Protected Browser Storage relies on ASP.NET Core Data Protection and is only supported for Blazor Server apps.</span></span>

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="db98f-269">Enregistrer et charger des données dans un composant</span><span class="sxs-lookup"><span data-stu-id="db98f-269">Save and load data within a component</span></span>

<span data-ttu-id="db98f-270">Dans tout composant qui requiert le chargement ou l’enregistrement de données dans le stockage du navigateur, utilisez la [`@inject`](xref:mvc/views/razor#inject) directive pour injecter une instance de l’un des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="db98f-270">In any component that requires loading or saving data to browser storage, use the [`@inject`](xref:mvc/views/razor#inject) directive to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="db98f-271">Le choix dépend de l’emplacement de stockage du navigateur que vous souhaitez utiliser.</span><span class="sxs-lookup"><span data-stu-id="db98f-271">The choice depends on which browser storage location you wish to use.</span></span> <span data-ttu-id="db98f-272">Dans l’exemple suivant, `sessionStorage` est utilisé :</span><span class="sxs-lookup"><span data-stu-id="db98f-272">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="db98f-273">La `@using` directive peut être placée dans le fichier de l’application `_Imports.razor` plutôt que dans le composant.</span><span class="sxs-lookup"><span data-stu-id="db98f-273">The `@using` directive can be placed in the app's `_Imports.razor` file instead of in the component.</span></span> <span data-ttu-id="db98f-274">L’utilisation du `_Imports.razor` fichier rend l’espace de noms disponible pour les plus grands segments de l’application ou de l’application entière.</span><span class="sxs-lookup"><span data-stu-id="db98f-274">Use of the `_Imports.razor` file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="db98f-275">Pour rendre la `currentCount` valeur persistante dans le `Counter` composant d’une application en fonction du Blazor Server modèle de projet, modifiez la `IncrementCount` méthode pour utiliser `ProtectedSessionStore.SetAsync` :</span><span class="sxs-lookup"><span data-stu-id="db98f-275">To persist the `currentCount` value in the `Counter` component of an app based on the Blazor Server project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="db98f-276">Dans les applications plus volumineuses et plus réalistes, le stockage de champs individuels est un scénario peu probable.</span><span class="sxs-lookup"><span data-stu-id="db98f-276">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="db98f-277">Les applications sont plus susceptibles de stocker des objets de modèle entiers qui incluent un État complexe.</span><span class="sxs-lookup"><span data-stu-id="db98f-277">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="db98f-278">`ProtectedSessionStore` sérialise et désérialise automatiquement des données JSON pour stocker des objets d’État complexes.</span><span class="sxs-lookup"><span data-stu-id="db98f-278">`ProtectedSessionStore` automatically serializes and deserializes JSON data to store complex state objects.</span></span>

<span data-ttu-id="db98f-279">Dans l’exemple de code précédent, les `currentCount` données sont stockées sous `sessionStorage['count']` la forme dans le navigateur de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="db98f-279">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="db98f-280">Les données ne sont pas stockées en texte brut mais sont protégées à l’aide d’ASP.NET Core protection des données.</span><span class="sxs-lookup"><span data-stu-id="db98f-280">The data isn't stored in plain text but rather is protected using ASP.NET Core Data Protection.</span></span> <span data-ttu-id="db98f-281">Les données chiffrées peuvent être inspectées si la `sessionStorage['count']` est évaluée dans la console de développement du navigateur.</span><span class="sxs-lookup"><span data-stu-id="db98f-281">The encrypted data can be inspected if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="db98f-282">Pour récupérer les `currentCount` données si l’utilisateur retourne au `Counter` composant ultérieurement, y compris si l’utilisateur se trouve sur un nouveau circuit, utilisez `ProtectedSessionStore.GetAsync` :</span><span class="sxs-lookup"><span data-stu-id="db98f-282">To recover the `currentCount` data if the user returns to the `Counter` component later, including if the user is on a new circuit, use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    var result = await ProtectedSessionStore.GetAsync<int>("count");
    currentCount = result.Success ? result.Value : 0;
}
```

<span data-ttu-id="db98f-283">Si les paramètres du composant incluent l’état de navigation, appelez `ProtectedSessionStore.GetAsync` et assignez un non- `null` résultat dans <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> , et non <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="db98f-283">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign a non-`null` result in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, not <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span> <span data-ttu-id="db98f-284"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> la méthode est appelée une seule fois lors de la première instanciation du composant.</span><span class="sxs-lookup"><span data-stu-id="db98f-284"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is only called once when the component is first instantiated.</span></span> <span data-ttu-id="db98f-285"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> n’est pas rappelée ultérieurement si l’utilisateur accède à une autre URL tout en restant sur la même page.</span><span class="sxs-lookup"><span data-stu-id="db98f-285"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="db98f-286">Pour plus d’informations, consultez <xref:blazor/components/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="db98f-286">For more information, see <xref:blazor/components/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="db98f-287">Les exemples de cette section ne fonctionnent que si le prérendu n’est pas activé sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="db98f-287">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="db98f-288">Quand le prérendu est activé, une erreur est générée, expliquant que les appels Interop JavaScript ne peuvent pas être émis car le composant est en cours de prérendu.</span><span class="sxs-lookup"><span data-stu-id="db98f-288">With prerendering enabled, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>
>
> <span data-ttu-id="db98f-289">Désactivez le prérendu ou ajoutez du code supplémentaire pour utiliser le prérendu.</span><span class="sxs-lookup"><span data-stu-id="db98f-289">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="db98f-290">Pour en savoir plus sur l’écriture de code qui fonctionne avec le prérendu, consultez la section [handle PreRender](#handle-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="db98f-290">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="db98f-291">Gérer l’état de chargement</span><span class="sxs-lookup"><span data-stu-id="db98f-291">Handle the loading state</span></span>

<span data-ttu-id="db98f-292">Étant donné que le stockage du navigateur est accessible de façon asynchrone via une connexion réseau, il y a toujours un certain temps avant que les données soient chargées et disponibles pour un composant.</span><span class="sxs-lookup"><span data-stu-id="db98f-292">Since browser storage is accessed asynchronously over a network connection, there's always a period of time before the data is loaded and available to a component.</span></span> <span data-ttu-id="db98f-293">Pour obtenir les meilleurs résultats, affichez un message d’état de chargement pendant le chargement en cours au lieu d’afficher les données vides ou par défaut.</span><span class="sxs-lookup"><span data-stu-id="db98f-293">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="db98f-294">Une approche consiste à déterminer si les données sont `null` , ce qui signifie que les données sont toujours en cours de chargement.</span><span class="sxs-lookup"><span data-stu-id="db98f-294">One approach is to track whether the data is `null`, which means that the data is still loading.</span></span> <span data-ttu-id="db98f-295">Dans le composant par défaut `Counter` , le nombre est conservé dans un `int` .</span><span class="sxs-lookup"><span data-stu-id="db98f-295">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="db98f-296">[Rendez la valeur `currentCount` null](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) en ajoutant un point d’interrogation ( `?` ) au type ( `int` ) :</span><span class="sxs-lookup"><span data-stu-id="db98f-296">[Make `currentCount` nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="db98f-297">Au lieu d’afficher sans condition le nombre et le **`Increment`** bouton, affichez ces éléments uniquement si les données sont chargées en vérifiant <xref:System.Nullable%601.HasValue%2A> :</span><span class="sxs-lookup"><span data-stu-id="db98f-297">Instead of unconditionally displaying the count and **`Increment`** button, display these elements only if the data is loaded by checking <xref:System.Nullable%601.HasValue%2A>:</span></span>

```razor
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a><span data-ttu-id="db98f-298">Gérer le prérendu</span><span class="sxs-lookup"><span data-stu-id="db98f-298">Handle prerendering</span></span>

<span data-ttu-id="db98f-299">Lors du prérendu :</span><span class="sxs-lookup"><span data-stu-id="db98f-299">During prerendering:</span></span>

* <span data-ttu-id="db98f-300">Une connexion interactive au navigateur de l’utilisateur n’existe pas.</span><span class="sxs-lookup"><span data-stu-id="db98f-300">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="db98f-301">Le navigateur ne dispose pas encore d’une page dans laquelle il peut exécuter du code JavaScript.</span><span class="sxs-lookup"><span data-stu-id="db98f-301">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="db98f-302">`localStorage` ou `sessionStorage` ne sont pas disponibles pendant le prérendu.</span><span class="sxs-lookup"><span data-stu-id="db98f-302">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="db98f-303">Si le composant tente d’interagir avec le stockage, une erreur est générée, expliquant que les appels Interop JavaScript ne peuvent pas être émis car le composant est en cours de prérendu.</span><span class="sxs-lookup"><span data-stu-id="db98f-303">If the component attempts to interact with storage, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>

<span data-ttu-id="db98f-304">L’une des méthodes permettant de résoudre l’erreur consiste à désactiver le prérendu.</span><span class="sxs-lookup"><span data-stu-id="db98f-304">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="db98f-305">C’est généralement le meilleur choix si l’application utilise beaucoup le stockage basé sur le navigateur.</span><span class="sxs-lookup"><span data-stu-id="db98f-305">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="db98f-306">Le prérendu ajoute de la complexité et ne tire pas parti de l’application, car l’application ne peut pas prérestituer de contenu utile tant que `localStorage` ou `sessionStorage` n’est pas disponible.</span><span class="sxs-lookup"><span data-stu-id="db98f-306">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="db98f-307">Pour désactiver le prérendu, ouvrez le `Pages/_Host.cshtml` fichier et remplacez l' `render-mode` attribut de [tag Helper du composant](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) par <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> :</span><span class="sxs-lookup"><span data-stu-id="db98f-307">To disable prerendering, open the `Pages/_Host.cshtml` file and change the `render-mode` attribute of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>:</span></span>

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

<span data-ttu-id="db98f-308">Le prérendu peut être utile pour d’autres pages qui n’utilisent pas `localStorage` ou `sessionStorage` .</span><span class="sxs-lookup"><span data-stu-id="db98f-308">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="db98f-309">Pour conserver le prérendu, différez l’opération de chargement jusqu’à ce que le navigateur soit connecté au circuit.</span><span class="sxs-lookup"><span data-stu-id="db98f-309">To retain prerendering, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="db98f-310">Voici un exemple de stockage d’une valeur de compteur :</span><span class="sxs-lookup"><span data-stu-id="db98f-310">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

@if (isConnected)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}

@code {
    private int currentCount;
    private bool isConnected;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        var result = await ProtectedLocalStore.GetAsync<int>("count");
        currentCount = result.Success ? result.Value : 0;
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedLocalStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="db98f-311">Factoriser la conservation de l’État à un emplacement commun</span><span class="sxs-lookup"><span data-stu-id="db98f-311">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="db98f-312">Si de nombreux composants reposent sur un stockage basé sur un navigateur, la réimplémentation du code du fournisseur d’état de nombreuses fois crée une duplication du code.</span><span class="sxs-lookup"><span data-stu-id="db98f-312">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="db98f-313">Une option pour éviter la duplication de code consiste à créer un *composant parent du fournisseur d’État* qui encapsule la logique du fournisseur d’État.</span><span class="sxs-lookup"><span data-stu-id="db98f-313">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="db98f-314">Les composants enfants peuvent fonctionner avec des données persistantes sans tenir compte du mécanisme de persistance de l’État.</span><span class="sxs-lookup"><span data-stu-id="db98f-314">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="db98f-315">Dans l’exemple suivant d’un `CounterStateProvider` composant, les données de compteur sont conservées dans `sessionStorage` :</span><span class="sxs-lookup"><span data-stu-id="db98f-315">In the following example of a `CounterStateProvider` component, counter data is persisted to `sessionStorage`:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (isLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool isLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        var result = await ProtectedSessionStore.GetAsync<int>("count");
        currentCount = result.Success ? result.Value : 0;
        isLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="db98f-316">Le `CounterStateProvider` composant gère la phase de chargement en n’affichant pas son contenu enfant tant que le chargement n’est pas terminé.</span><span class="sxs-lookup"><span data-stu-id="db98f-316">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="db98f-317">Pour utiliser le `CounterStateProvider` composant, encapsulez une instance du composant autour de tout autre composant qui requiert l’accès à l’état du compteur.</span><span class="sxs-lookup"><span data-stu-id="db98f-317">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="db98f-318">Pour rendre l’état accessible à tous les composants d’une application, encapsulez le `CounterStateProvider` composant autour de <xref:Microsoft.AspNetCore.Components.Routing.Router> dans le `App` composant ( `App.razor` ) :</span><span class="sxs-lookup"><span data-stu-id="db98f-318">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the <xref:Microsoft.AspNetCore.Components.Routing.Router> in the `App` component (`App.razor`):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="@typeof(Program).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<span data-ttu-id="db98f-319">Les composants encapsulés reçoivent et peuvent modifier l’état du compteur persistant.</span><span class="sxs-lookup"><span data-stu-id="db98f-319">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="db98f-320">Le `Counter` composant suivant implémente le modèle :</span><span class="sxs-lookup"><span data-stu-id="db98f-320">The following `Counter` component implements the pattern:</span></span>

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>
<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

<span data-ttu-id="db98f-321">Le composant précédent n’est pas requis pour interagir avec `ProtectedBrowserStorage` et ne gère pas une phase de « chargement ».</span><span class="sxs-lookup"><span data-stu-id="db98f-321">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="db98f-322">Pour traiter le prérendu comme décrit précédemment, `CounterStateProvider` peut être modifié de façon à ce que tous les composants qui consomment les données de compteur fonctionnent automatiquement avec le prérendu.</span><span class="sxs-lookup"><span data-stu-id="db98f-322">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="db98f-323">Pour plus d’informations, consultez la section [handle PreRender](#handle-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="db98f-323">For more information, see the [Handle prerendering](#handle-prerendering) section.</span></span>

<span data-ttu-id="db98f-324">En général, le modèle de *composant parent du fournisseur d’État* est recommandé :</span><span class="sxs-lookup"><span data-stu-id="db98f-324">In general, the *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="db98f-325">Pour utiliser l’État sur de nombreux composants.</span><span class="sxs-lookup"><span data-stu-id="db98f-325">To consume state across many components.</span></span>
* <span data-ttu-id="db98f-326">S’il n’existe qu’un seul objet d’état de niveau supérieur à conserver.</span><span class="sxs-lookup"><span data-stu-id="db98f-326">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="db98f-327">Pour conserver un grand nombre d’objets d’état différents et utiliser différents sous-ensembles d’objets à des emplacements différents, il est préférable d’éviter de conserver l’État globalement.</span><span class="sxs-lookup"><span data-stu-id="db98f-327">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid persisting state globally.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="protected-browser-storage-experimental-nuget-package"></a><span data-ttu-id="db98f-328">Package NuGet expérimental du stockage du navigateur protégé</span><span class="sxs-lookup"><span data-stu-id="db98f-328">Protected Browser Storage experimental NuGet package</span></span>

<span data-ttu-id="db98f-329">ASP.NET Core le stockage protégé du navigateur tire parti de [ASP.net Core protection des données](xref:security/data-protection/introduction) pour [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) et [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) .</span><span class="sxs-lookup"><span data-stu-id="db98f-329">ASP.NET Core Protected Browser Storage leverages [ASP.NET Core Data Protection](xref:security/data-protection/introduction) for [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).</span></span>

> [!WARNING]
> <span data-ttu-id="db98f-330">`Microsoft.AspNetCore.ProtectedBrowserStorage` est un package expérimental non pris en charge et inapproprié pour une utilisation en production.</span><span class="sxs-lookup"><span data-stu-id="db98f-330">`Microsoft.AspNetCore.ProtectedBrowserStorage` is an unsupported, experimental package unsuitable for production use.</span></span>
>
> <span data-ttu-id="db98f-331">Le package n’est disponible que pour une utilisation dans les applications ASP.NET Core 3,1 Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="db98f-331">The package is only available for use in ASP.NET Core 3.1 Blazor Server apps.</span></span>

### <a name="configuration"></a><span data-ttu-id="db98f-332">Configuration</span><span class="sxs-lookup"><span data-stu-id="db98f-332">Configuration</span></span>

1. <span data-ttu-id="db98f-333">Ajoutez une référence de package à [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage) .</span><span class="sxs-lookup"><span data-stu-id="db98f-333">Add a package reference to [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>
1. <span data-ttu-id="db98f-334">Dans le `Pages/_Host.cshtml` fichier, ajoutez le script suivant à l’intérieur de la `</body>` balise de fermeture :</span><span class="sxs-lookup"><span data-stu-id="db98f-334">In the `Pages/_Host.cshtml` file, add the following script inside the closing `</body>` tag:</span></span>

   ```cshtml
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. <span data-ttu-id="db98f-335">Dans `Startup.ConfigureServices` , appelez `AddProtectedBrowserStorage` pour ajouter `localStorage` des `sessionStorage` services à la collection de services :</span><span class="sxs-lookup"><span data-stu-id="db98f-335">In `Startup.ConfigureServices`, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="db98f-336">Enregistrer et charger des données dans un composant</span><span class="sxs-lookup"><span data-stu-id="db98f-336">Save and load data within a component</span></span>

<span data-ttu-id="db98f-337">Dans tout composant qui requiert le chargement ou l’enregistrement de données dans le stockage du navigateur, utilisez la [`@inject`](xref:mvc/views/razor#inject) directive pour injecter une instance de l’un des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="db98f-337">In any component that requires loading or saving data to browser storage, use the [`@inject`](xref:mvc/views/razor#inject) directive to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="db98f-338">Le choix dépend de l’emplacement de stockage du navigateur que vous souhaitez utiliser.</span><span class="sxs-lookup"><span data-stu-id="db98f-338">The choice depends on which browser storage location you wish to use.</span></span> <span data-ttu-id="db98f-339">Dans l’exemple suivant, `sessionStorage` est utilisé :</span><span class="sxs-lookup"><span data-stu-id="db98f-339">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="db98f-340">L' `@using` instruction peut être placée dans un `_Imports.razor` fichier plutôt que dans le composant.</span><span class="sxs-lookup"><span data-stu-id="db98f-340">The `@using` statement can be placed into an `_Imports.razor` file instead of in the component.</span></span> <span data-ttu-id="db98f-341">L’utilisation du `_Imports.razor` fichier rend l’espace de noms disponible pour les plus grands segments de l’application ou de l’application entière.</span><span class="sxs-lookup"><span data-stu-id="db98f-341">Use of the `_Imports.razor` file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="db98f-342">Pour rendre la `currentCount` valeur persistante dans le `Counter` composant d’une application en fonction du Blazor Server modèle de projet, modifiez la `IncrementCount` méthode pour utiliser `ProtectedSessionStore.SetAsync` :</span><span class="sxs-lookup"><span data-stu-id="db98f-342">To persist the `currentCount` value in the `Counter` component of an app based on the Blazor Server project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="db98f-343">Dans les applications plus volumineuses et plus réalistes, le stockage de champs individuels est un scénario peu probable.</span><span class="sxs-lookup"><span data-stu-id="db98f-343">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="db98f-344">Les applications sont plus susceptibles de stocker des objets de modèle entiers qui incluent un État complexe.</span><span class="sxs-lookup"><span data-stu-id="db98f-344">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="db98f-345">`ProtectedSessionStore` sérialise et désérialise automatiquement les données JSON.</span><span class="sxs-lookup"><span data-stu-id="db98f-345">`ProtectedSessionStore` automatically serializes and deserializes JSON data.</span></span>

<span data-ttu-id="db98f-346">Dans l’exemple de code précédent, les `currentCount` données sont stockées sous `sessionStorage['count']` la forme dans le navigateur de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="db98f-346">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="db98f-347">Les données ne sont pas stockées en texte brut mais sont protégées à l’aide d’ASP.NET Core protection des données.</span><span class="sxs-lookup"><span data-stu-id="db98f-347">The data isn't stored in plain text but rather is protected using ASP.NET Core Data Protection.</span></span> <span data-ttu-id="db98f-348">Les données chiffrées peuvent être inspectées si la `sessionStorage['count']` est évaluée dans la console de développement du navigateur.</span><span class="sxs-lookup"><span data-stu-id="db98f-348">The encrypted data can be inspected if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="db98f-349">Pour récupérer les `currentCount` données si l’utilisateur retourne au `Counter` composant ultérieurement, y compris s’il s’agit d’un circuit entièrement nouveau, `ProtectedSessionStore.GetAsync` utilisez :</span><span class="sxs-lookup"><span data-stu-id="db98f-349">To recover the `currentCount` data if the user returns to the `Counter` component later, including if they're on an entirely new circuit, use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

<span data-ttu-id="db98f-350">Si les paramètres du composant incluent l’état de navigation, appelez `ProtectedSessionStore.GetAsync` et assignez le résultat dans <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> , et non <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="db98f-350">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign the result in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, not <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span> <span data-ttu-id="db98f-351"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> la méthode est appelée une seule fois lors de la première instanciation du composant.</span><span class="sxs-lookup"><span data-stu-id="db98f-351"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is only called once when the component is first instantiated.</span></span> <span data-ttu-id="db98f-352"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> n’est pas rappelée ultérieurement si l’utilisateur accède à une autre URL tout en restant sur la même page.</span><span class="sxs-lookup"><span data-stu-id="db98f-352"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="db98f-353">Pour plus d’informations, consultez <xref:blazor/components/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="db98f-353">For more information, see <xref:blazor/components/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="db98f-354">Les exemples de cette section ne fonctionnent que si le prérendu n’est pas activé sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="db98f-354">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="db98f-355">Quand le prérendu est activé, une erreur est générée, expliquant que les appels Interop JavaScript ne peuvent pas être émis car le composant est en cours de prérendu.</span><span class="sxs-lookup"><span data-stu-id="db98f-355">With prerendering enabled, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>
>
> <span data-ttu-id="db98f-356">Désactivez le prérendu ou ajoutez du code supplémentaire pour utiliser le prérendu.</span><span class="sxs-lookup"><span data-stu-id="db98f-356">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="db98f-357">Pour en savoir plus sur l’écriture de code qui fonctionne avec le prérendu, consultez la section [handle PreRender](#handle-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="db98f-357">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="db98f-358">Gérer l’état de chargement</span><span class="sxs-lookup"><span data-stu-id="db98f-358">Handle the loading state</span></span>

<span data-ttu-id="db98f-359">Étant donné que le stockage du navigateur est accessible de façon asynchrone via une connexion réseau, il y a toujours un certain temps avant que les données soient chargées et disponibles pour un composant.</span><span class="sxs-lookup"><span data-stu-id="db98f-359">Since browser storage is accessed asynchronously over a network connection, there's always a period of time before the data is loaded and available to a component.</span></span> <span data-ttu-id="db98f-360">Pour obtenir les meilleurs résultats, affichez un message d’état de chargement pendant le chargement en cours au lieu d’afficher les données vides ou par défaut.</span><span class="sxs-lookup"><span data-stu-id="db98f-360">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="db98f-361">Une approche consiste à déterminer si les données sont `null` , ce qui signifie que les données sont toujours en cours de chargement.</span><span class="sxs-lookup"><span data-stu-id="db98f-361">One approach is to track whether the data is `null`, which means that the data is still loading.</span></span> <span data-ttu-id="db98f-362">Dans le composant par défaut `Counter` , le nombre est conservé dans un `int` .</span><span class="sxs-lookup"><span data-stu-id="db98f-362">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="db98f-363">[Rendez la valeur `currentCount` null](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) en ajoutant un point d’interrogation ( `?` ) au type ( `int` ) :</span><span class="sxs-lookup"><span data-stu-id="db98f-363">[Make `currentCount` nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="db98f-364">Au lieu d’afficher sans condition le nombre et le **`Increment`** bouton, choisissez d’afficher ces éléments uniquement si les données sont chargées :</span><span class="sxs-lookup"><span data-stu-id="db98f-364">Instead of unconditionally displaying the count and **`Increment`** button, choose to display these elements only if the data is loaded:</span></span>

```razor
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a><span data-ttu-id="db98f-365">Gérer le prérendu</span><span class="sxs-lookup"><span data-stu-id="db98f-365">Handle prerendering</span></span>

<span data-ttu-id="db98f-366">Lors du prérendu :</span><span class="sxs-lookup"><span data-stu-id="db98f-366">During prerendering:</span></span>

* <span data-ttu-id="db98f-367">Une connexion interactive au navigateur de l’utilisateur n’existe pas.</span><span class="sxs-lookup"><span data-stu-id="db98f-367">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="db98f-368">Le navigateur ne dispose pas encore d’une page dans laquelle il peut exécuter du code JavaScript.</span><span class="sxs-lookup"><span data-stu-id="db98f-368">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="db98f-369">`localStorage` ou `sessionStorage` ne sont pas disponibles pendant le prérendu.</span><span class="sxs-lookup"><span data-stu-id="db98f-369">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="db98f-370">Si le composant tente d’interagir avec le stockage, une erreur est générée, expliquant que les appels Interop JavaScript ne peuvent pas être émis car le composant est en cours de prérendu.</span><span class="sxs-lookup"><span data-stu-id="db98f-370">If the component attempts to interact with storage, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>

<span data-ttu-id="db98f-371">L’une des méthodes permettant de résoudre l’erreur consiste à désactiver le prérendu.</span><span class="sxs-lookup"><span data-stu-id="db98f-371">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="db98f-372">C’est généralement le meilleur choix si l’application utilise beaucoup le stockage basé sur le navigateur.</span><span class="sxs-lookup"><span data-stu-id="db98f-372">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="db98f-373">Le prérendu ajoute de la complexité et ne tire pas parti de l’application, car l’application ne peut pas prérestituer de contenu utile tant que `localStorage` ou `sessionStorage` n’est pas disponible.</span><span class="sxs-lookup"><span data-stu-id="db98f-373">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="db98f-374">Pour désactiver le prérendu, ouvrez le `Pages/_Host.cshtml` fichier et remplacez l' `render-mode` attribut de [tag Helper du composant](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) par <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> :</span><span class="sxs-lookup"><span data-stu-id="db98f-374">To disable prerendering, open the `Pages/_Host.cshtml` file and change the `render-mode` attribute of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>:</span></span>

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

<span data-ttu-id="db98f-375">Le prérendu peut être utile pour d’autres pages qui n’utilisent pas `localStorage` ou `sessionStorage` .</span><span class="sxs-lookup"><span data-stu-id="db98f-375">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="db98f-376">Pour conserver le prérendu, différez l’opération de chargement jusqu’à ce que le navigateur soit connecté au circuit.</span><span class="sxs-lookup"><span data-stu-id="db98f-376">To retain prerendering, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="db98f-377">Voici un exemple de stockage d’une valeur de compteur :</span><span class="sxs-lookup"><span data-stu-id="db98f-377">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

@if (isConnected)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}

@code {
    private int? currentCount;
    private bool isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        currentCount = await ProtectedLocalStore.GetAsync<int>("count");
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedLocalStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="db98f-378">Factoriser la conservation de l’État à un emplacement commun</span><span class="sxs-lookup"><span data-stu-id="db98f-378">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="db98f-379">Si de nombreux composants reposent sur un stockage basé sur un navigateur, la réimplémentation du code du fournisseur d’état de nombreuses fois crée une duplication du code.</span><span class="sxs-lookup"><span data-stu-id="db98f-379">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="db98f-380">Une option pour éviter la duplication de code consiste à créer un *composant parent du fournisseur d’État* qui encapsule la logique du fournisseur d’État.</span><span class="sxs-lookup"><span data-stu-id="db98f-380">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="db98f-381">Les composants enfants peuvent fonctionner avec des données persistantes sans tenir compte du mécanisme de persistance de l’État.</span><span class="sxs-lookup"><span data-stu-id="db98f-381">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="db98f-382">Dans l’exemple suivant d’un `CounterStateProvider` composant, les données de compteur sont conservées dans `sessionStorage` :</span><span class="sxs-lookup"><span data-stu-id="db98f-382">In the following example of a `CounterStateProvider` component, counter data is persisted to `sessionStorage`:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (isLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool isLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        isLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="db98f-383">Le `CounterStateProvider` composant gère la phase de chargement en n’affichant pas son contenu enfant tant que le chargement n’est pas terminé.</span><span class="sxs-lookup"><span data-stu-id="db98f-383">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="db98f-384">Pour utiliser le `CounterStateProvider` composant, encapsulez une instance du composant autour de tout autre composant qui requiert l’accès à l’état du compteur.</span><span class="sxs-lookup"><span data-stu-id="db98f-384">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="db98f-385">Pour rendre l’état accessible à tous les composants d’une application, encapsulez le `CounterStateProvider` composant autour de <xref:Microsoft.AspNetCore.Components.Routing.Router> dans le `App` composant ( `App.razor` ) :</span><span class="sxs-lookup"><span data-stu-id="db98f-385">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the <xref:Microsoft.AspNetCore.Components.Routing.Router> in the `App` component (`App.razor`):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="@typeof(Program).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<span data-ttu-id="db98f-386">Les composants encapsulés reçoivent et peuvent modifier l’état du compteur persistant.</span><span class="sxs-lookup"><span data-stu-id="db98f-386">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="db98f-387">Le `Counter` composant suivant implémente le modèle :</span><span class="sxs-lookup"><span data-stu-id="db98f-387">The following `Counter` component implements the pattern:</span></span>

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>
<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

<span data-ttu-id="db98f-388">Le composant précédent n’est pas requis pour interagir avec `ProtectedBrowserStorage` et ne gère pas une phase de « chargement ».</span><span class="sxs-lookup"><span data-stu-id="db98f-388">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="db98f-389">Pour traiter le prérendu comme décrit précédemment, `CounterStateProvider` peut être modifié de façon à ce que tous les composants qui consomment les données de compteur fonctionnent automatiquement avec le prérendu.</span><span class="sxs-lookup"><span data-stu-id="db98f-389">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="db98f-390">Pour plus d’informations, consultez la section [handle PreRender](#handle-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="db98f-390">For more information, see the [Handle prerendering](#handle-prerendering) section.</span></span>

<span data-ttu-id="db98f-391">En général, le modèle de *composant parent du fournisseur d’État* est recommandé :</span><span class="sxs-lookup"><span data-stu-id="db98f-391">In general, *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="db98f-392">Pour utiliser l’État sur de nombreux composants.</span><span class="sxs-lookup"><span data-stu-id="db98f-392">To consume state across many components.</span></span>
* <span data-ttu-id="db98f-393">S’il n’existe qu’un seul objet d’état de niveau supérieur à conserver.</span><span class="sxs-lookup"><span data-stu-id="db98f-393">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="db98f-394">Pour conserver un grand nombre d’objets d’état différents et utiliser différents sous-ensembles d’objets à des emplacements différents, il est préférable d’éviter de conserver l’État globalement.</span><span class="sxs-lookup"><span data-stu-id="db98f-394">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid persisting state globally.</span></span>

::: moniker-end

<h2 id="in-memory-state-container-service-server"><span data-ttu-id="db98f-395">Service de conteneur d’État en mémoire</span><span class="sxs-lookup"><span data-stu-id="db98f-395">In-memory state container service</span></span></h2>

[!INCLUDE[](~/blazor/includes/state-container.md)]

::: zone-end
