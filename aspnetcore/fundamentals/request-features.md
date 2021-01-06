---
title: Fonctionnalités de requête dans ASP.NET Core
author: ardalis
description: Découvrez les détails d’implémentation d’un serveur web relatifs aux requêtes et réponses HTTP qui sont définies dans les interfaces pour ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
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
uid: fundamentals/request-features
ms.openlocfilehash: 88e97d88341789a76a79da8d92098c2e00396fe7
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "95870423"
---
# <a name="request-features-in-aspnet-core"></a><span data-ttu-id="311f2-103">Fonctionnalités de requête dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="311f2-103">Request Features in ASP.NET Core</span></span>

<span data-ttu-id="311f2-104">Par [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="311f2-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="311f2-105">L' `HttpContext` API utilisée par les applications et les intergiciels pour traiter les demandes a une couche d’abstraction en dessous de celle-ci appelée *interfaces de fonctionnalités*.</span><span class="sxs-lookup"><span data-stu-id="311f2-105">The `HttpContext` API that applications and middleware use to process requests has an abstraction layer underneath it called *feature interfaces*.</span></span> <span data-ttu-id="311f2-106">Chaque interface de fonctionnalité fournit un sous-ensemble granulaire des fonctionnalités exposées par `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="311f2-106">Each feature interface provides a granular subset of the functionality exposed by `HttpContext`.</span></span> <span data-ttu-id="311f2-107">Ces interfaces peuvent être ajoutées, modifiées, encapsulées, remplacées ou même supprimées par le serveur ou l’intergiciel (middleware) lors du traitement de la demande sans avoir à réimplémenter l’ensemble du `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="311f2-107">These interfaces can be added, modified, wrapped, replaced, or even removed by the server or middleware as the request is processed without having to re-implement the entire `HttpContext`.</span></span> <span data-ttu-id="311f2-108">Elles peuvent également être utilisées pour simuler des fonctionnalités lors des tests.</span><span class="sxs-lookup"><span data-stu-id="311f2-108">They can also be used to mock functionality when testing.</span></span>

## <a name="feature-collections"></a><span data-ttu-id="311f2-109">Collections de fonctionnalités</span><span class="sxs-lookup"><span data-stu-id="311f2-109">Feature collections</span></span>

<span data-ttu-id="311f2-110">La <xref:Microsoft.AspNetCore.Http.HttpContext.Features> propriété de `HttpContext` permet d’accéder à la collection d’interfaces de fonctionnalités de la requête actuelle.</span><span class="sxs-lookup"><span data-stu-id="311f2-110">The <xref:Microsoft.AspNetCore.Http.HttpContext.Features> property of `HttpContext` provides access to the collection of feature interfaces for the current request.</span></span> <span data-ttu-id="311f2-111">Étant donné que la collection de fonctionnalités est mutable même dans le contexte d’une requête, il est possible d’utiliser un intergiciel (middleware) pour modifier la collection et ajouter la prise en charge de fonctionnalités supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="311f2-111">Since the feature collection is mutable even within the context of a request, middleware can be used to modify the collection and add support for additional features.</span></span> <span data-ttu-id="311f2-112">Certaines fonctionnalités avancées sont uniquement disponibles en accédant à l’interface associée via la collection de fonctionnalités.</span><span class="sxs-lookup"><span data-stu-id="311f2-112">Some advanced features are only available by accessing the associated interface through the feature collection.</span></span>

## <a name="feature-interfaces"></a><span data-ttu-id="311f2-113">Interfaces de fonctionnalités</span><span class="sxs-lookup"><span data-stu-id="311f2-113">Feature interfaces</span></span>

<span data-ttu-id="311f2-114">ASP.NET Core définit un certain nombre d’interfaces de fonctionnalités HTTP courantes dans <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName> , qui sont partagées par différents serveurs et middleware pour identifier les fonctionnalités qu’ils prennent en charge.</span><span class="sxs-lookup"><span data-stu-id="311f2-114">ASP.NET Core defines a number of common HTTP feature interfaces in <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName>, which are shared by various servers and middleware to identify the features that they support.</span></span> <span data-ttu-id="311f2-115">Les serveurs et les intergiciels (middleware) peuvent également fournir leurs propres interfaces avec des fonctionnalités supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="311f2-115">Servers and middleware may also provide their own interfaces with additional functionality.</span></span>

<span data-ttu-id="311f2-116">La plupart des interfaces de fonctionnalités fournissent des fonctionnalités facultatives, de mise en lumière et leurs `HttpContext` API associées fournissent des valeurs par défaut si la fonctionnalité n’est pas présente.</span><span class="sxs-lookup"><span data-stu-id="311f2-116">Most feature interfaces provide optional, light-up functionality, and their associated `HttpContext` APIs provide defaults if the feature isn't present.</span></span> <span data-ttu-id="311f2-117">Certaines interfaces sont indiquées dans le contenu suivant, selon les besoins, car elles fournissent les fonctionnalités principales de demande et de réponse et doivent être implémentées pour traiter la demande.</span><span class="sxs-lookup"><span data-stu-id="311f2-117">A few interfaces are indicated in the following content as required because they provide core request and response functionality and must be implemented in order to process the request.</span></span>

<span data-ttu-id="311f2-118">Les interfaces de fonctionnalités suivantes proviennent de <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName> :</span><span class="sxs-lookup"><span data-stu-id="311f2-118">The following feature interfaces are from <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName>:</span></span>

<span data-ttu-id="311f2-119"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature>: Définit la structure d’une requête HTTP, y compris le protocole, le chemin d’accès, la chaîne de requête, les en-têtes et le corps.</span><span class="sxs-lookup"><span data-stu-id="311f2-119"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature>: Defines the structure of an HTTP request, including the protocol, path, query string, headers, and body.</span></span> <span data-ttu-id="311f2-120">Cette fonctionnalité est nécessaire pour traiter les demandes.</span><span class="sxs-lookup"><span data-stu-id="311f2-120">This feature is required in order to process requests.</span></span>

<span data-ttu-id="311f2-121"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>: Définit la structure d’une réponse HTTP, y compris le code d’État, les en-têtes et le corps de la réponse.</span><span class="sxs-lookup"><span data-stu-id="311f2-121"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>: Defines the structure of an HTTP response, including the status code, headers, and body of the response.</span></span> <span data-ttu-id="311f2-122">Cette fonctionnalité est nécessaire pour traiter les demandes.</span><span class="sxs-lookup"><span data-stu-id="311f2-122">This feature is required in order to process requests.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="311f2-123"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseBodyFeature>: Définit différentes façons d’écrire le corps de la réponse, à l’aide d’un `Stream` , d’un `PipeWriter` ou d’un fichier.</span><span class="sxs-lookup"><span data-stu-id="311f2-123"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseBodyFeature>: Defines different ways of writing out the response body, using either a `Stream`, a `PipeWriter`, or a file.</span></span> <span data-ttu-id="311f2-124">Cette fonctionnalité est nécessaire pour traiter les demandes.</span><span class="sxs-lookup"><span data-stu-id="311f2-124">This feature is required in order to process requests.</span></span> <span data-ttu-id="311f2-125">Cela remplace `IHttpResponseFeature.Body` et `IHttpSendFileFeature` .</span><span class="sxs-lookup"><span data-stu-id="311f2-125">This replaces `IHttpResponseFeature.Body` and `IHttpSendFileFeature`.</span></span>

::: moniker-end

<span data-ttu-id="311f2-126"><xref:Microsoft.AspNetCore.Http.Features.Authentication.IHttpAuthenticationFeature>: Contient le <xref:System.Security.Claims.ClaimsPrincipal> actuellement associé à la demande.</span><span class="sxs-lookup"><span data-stu-id="311f2-126"><xref:Microsoft.AspNetCore.Http.Features.Authentication.IHttpAuthenticationFeature>: Holds the <xref:System.Security.Claims.ClaimsPrincipal> currently associated with the request.</span></span>

<span data-ttu-id="311f2-127"><xref:Microsoft.AspNetCore.Http.Features.IFormFeature>: Permet d’analyser et de mettre en cache les envois de formulaire HTTP et multiparts entrants.</span><span class="sxs-lookup"><span data-stu-id="311f2-127"><xref:Microsoft.AspNetCore.Http.Features.IFormFeature>: Used to parse and cache incoming HTTP and multipart form submissions.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="311f2-128"><xref:Microsoft.AspNetCore.Http.Features.IHttpBodyControlFeature>: Utilisé pour contrôler si les opérations d’e/s synchrones sont autorisées pour le corps de la demande ou de la réponse.</span><span class="sxs-lookup"><span data-stu-id="311f2-128"><xref:Microsoft.AspNetCore.Http.Features.IHttpBodyControlFeature>: Used to control if synchronous IO operations are allowed for the request or response bodies.</span></span>

::: moniker-end
   
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="311f2-129"><xref:Microsoft.AspNetCore.Http.Features.IHttpBufferingFeature>: Définit des méthodes pour désactiver la mise en mémoire tampon des demandes et/ou des réponses.</span><span class="sxs-lookup"><span data-stu-id="311f2-129"><xref:Microsoft.AspNetCore.Http.Features.IHttpBufferingFeature>: Defines methods for disabling buffering of requests and/or responses.</span></span>

::: moniker-end

<span data-ttu-id="311f2-130"><xref:Microsoft.AspNetCore.Http.Features.IHttpConnectionFeature>: Définit les propriétés de l’ID de connexion et des adresses et des ports locaux et distants.</span><span class="sxs-lookup"><span data-stu-id="311f2-130"><xref:Microsoft.AspNetCore.Http.Features.IHttpConnectionFeature>: Defines properties for the connection id and local and remote addresses and ports.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="311f2-131"><xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>: Contrôle la taille maximale autorisée du corps de la requête pour la requête actuelle.</span><span class="sxs-lookup"><span data-stu-id="311f2-131"><xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>: Controls the maximum allowed request body size for the current request.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="311f2-132">`IHttpRequestBodyDetectionFeature`: Indique si la demande peut avoir un corps.</span><span class="sxs-lookup"><span data-stu-id="311f2-132">`IHttpRequestBodyDetectionFeature`: Indicates if the request can have a body.</span></span>

::: moniker-end

<span data-ttu-id="311f2-133"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestIdentifierFeature>: Ajoute une propriété qui peut être implémentée pour identifier de manière unique les demandes.</span><span class="sxs-lookup"><span data-stu-id="311f2-133"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestIdentifierFeature>: Adds a property that can be implemented to uniquely identify requests.</span></span>

<span data-ttu-id="311f2-134"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestLifetimeFeature>: Définit la prise en charge de l’abandon des connexions ou de la détection de l’arrêt prématuré d’une demande, par exemple lors de la déconnexion d’un client.</span><span class="sxs-lookup"><span data-stu-id="311f2-134"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestLifetimeFeature>: Defines support for aborting connections or detecting if a request has been terminated prematurely, such as by a client disconnect.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="311f2-135"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestTrailersFeature>: Fournit l’accès aux en-têtes de code de fin de demande, le cas échéant.</span><span class="sxs-lookup"><span data-stu-id="311f2-135"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestTrailersFeature>: Provides access to the request trailer headers, if any.</span></span>

<span data-ttu-id="311f2-136"><xref:Microsoft.AspNetCore.Http.Features.IHttpResetFeature>: Utilisé pour envoyer des messages de réinitialisation pour les protocoles qui les prennent en charge, tels que HTTP/2 ou HTTP/3.</span><span class="sxs-lookup"><span data-stu-id="311f2-136"><xref:Microsoft.AspNetCore.Http.Features.IHttpResetFeature>: Used to send reset messages for protocols that support them such as HTTP/2 or HTTP/3.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="311f2-137"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseTrailersFeature>: Permet à l’application de fournir des en-têtes de code de fin de réponse s’ils sont pris en charge.</span><span class="sxs-lookup"><span data-stu-id="311f2-137"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseTrailersFeature>: Enables the application to provide response trailer headers if supported.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="311f2-138"><xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>: Définit une méthode pour envoyer des fichiers de façon asynchrone.</span><span class="sxs-lookup"><span data-stu-id="311f2-138"><xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>: Defines a method for sending files asynchronously.</span></span>

::: moniker-end

<span data-ttu-id="311f2-139"><xref:Microsoft.AspNetCore.Http.Features.IHttpUpgradeFeature>: Définit la prise en charge des [mises à niveau http](https://tools.ietf.org/html/rfc2616.html#section-14.42), ce qui permet au client de spécifier les protocoles supplémentaires qu’il souhaite utiliser si le serveur souhaite changer de protocole.</span><span class="sxs-lookup"><span data-stu-id="311f2-139"><xref:Microsoft.AspNetCore.Http.Features.IHttpUpgradeFeature>: Defines support for [HTTP Upgrades](https://tools.ietf.org/html/rfc2616.html#section-14.42), which allow the client to specify which additional protocols it would like to use if the server wishes to switch protocols.</span></span>

<span data-ttu-id="311f2-140"><xref:Microsoft.AspNetCore.Http.Features.IHttpWebSocketFeature>: Définit une API pour la prise en charge des sockets Web.</span><span class="sxs-lookup"><span data-stu-id="311f2-140"><xref:Microsoft.AspNetCore.Http.Features.IHttpWebSocketFeature>: Defines an API for supporting web sockets.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="311f2-141"><xref:Microsoft.AspNetCore.Http.Features.IHttpsCompressionFeature>: Contrôle si la compression de réponse doit être utilisée sur des connexions HTTPs.</span><span class="sxs-lookup"><span data-stu-id="311f2-141"><xref:Microsoft.AspNetCore.Http.Features.IHttpsCompressionFeature>: Controls if response compression should be used over HTTPS connections.</span></span>

::: moniker-end

<span data-ttu-id="311f2-142"><xref:Microsoft.AspNetCore.Http.Features.IItemsFeature>: Stocke la <xref:Microsoft.AspNetCore.Http.Features.IItemsFeature.Items> collection pour l’état de l’application par demande.</span><span class="sxs-lookup"><span data-stu-id="311f2-142"><xref:Microsoft.AspNetCore.Http.Features.IItemsFeature>: Stores the <xref:Microsoft.AspNetCore.Http.Features.IItemsFeature.Items> collection for per request application state.</span></span>

<span data-ttu-id="311f2-143"><xref:Microsoft.AspNetCore.Http.Features.IQueryFeature>: Analyse et met en cache la chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="311f2-143"><xref:Microsoft.AspNetCore.Http.Features.IQueryFeature>: Parses and caches the query string.</span></span>
   
::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="311f2-144"><xref:Microsoft.AspNetCore.Http.Features.IRequestBodyPipeFeature>: Représente le corps de la requête sous la forme d’un <xref:System.IO.Pipelines.PipeReader> .</span><span class="sxs-lookup"><span data-stu-id="311f2-144"><xref:Microsoft.AspNetCore.Http.Features.IRequestBodyPipeFeature>: Represents the request body as a <xref:System.IO.Pipelines.PipeReader>.</span></span>
 
::: moniker-end

<span data-ttu-id="311f2-145"><xref:Microsoft.AspNetCore.Http.Features.IRequestCookiesFeature>: Analyse et met en cache les `Cookie` valeurs d’en-tête de demande.</span><span class="sxs-lookup"><span data-stu-id="311f2-145"><xref:Microsoft.AspNetCore.Http.Features.IRequestCookiesFeature>: Parses and caches the request `Cookie` header values.</span></span>

<span data-ttu-id="311f2-146"><xref:Microsoft.AspNetCore.Http.Features.IResponseCookiesFeature>: Contrôle la manière dont cookie les réponses s sont appliquées à l' `Set-Cookie` en-tête.</span><span class="sxs-lookup"><span data-stu-id="311f2-146"><xref:Microsoft.AspNetCore.Http.Features.IResponseCookiesFeature>: Controls how response cookies are applied to the `Set-Cookie` header.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="311f2-147"><xref:Microsoft.AspNetCore.Http.Features.IServerVariablesFeature>: Cette fonctionnalité permet d’accéder aux variables de serveur de demande, telles que celles fournies par IIS.</span><span class="sxs-lookup"><span data-stu-id="311f2-147"><xref:Microsoft.AspNetCore.Http.Features.IServerVariablesFeature>: This feature provides access to request server variables such as those provided by IIS.</span></span>

::: moniker-end
   
<span data-ttu-id="311f2-148"><xref:Microsoft.AspNetCore.Http.Features.IServiceProvidersFeature>: Fournit l’accès à un <xref:System.IServiceProvider> avec des services de requête étendus.</span><span class="sxs-lookup"><span data-stu-id="311f2-148"><xref:Microsoft.AspNetCore.Http.Features.IServiceProvidersFeature>: Provides access to an <xref:System.IServiceProvider> with scoped request services.</span></span>

<span data-ttu-id="311f2-149"><xref:Microsoft.AspNetCore.Http.Features.ISessionFeature>: Définit `ISessionFactory` et <xref:Microsoft.AspNetCore.Http.ISession> abstractions pour la prise en charge des sessions utilisateur.</span><span class="sxs-lookup"><span data-stu-id="311f2-149"><xref:Microsoft.AspNetCore.Http.Features.ISessionFeature>: Defines `ISessionFactory` and <xref:Microsoft.AspNetCore.Http.ISession> abstractions for supporting user sessions.</span></span> <span data-ttu-id="311f2-150">`ISessionFeature` est implémenté par <xref:Microsoft.AspNetCore.Session.SessionMiddleware> (consultez <xref:fundamentals/app-state> ).</span><span class="sxs-lookup"><span data-stu-id="311f2-150">`ISessionFeature` is implemented by the <xref:Microsoft.AspNetCore.Session.SessionMiddleware> (see <xref:fundamentals/app-state>).</span></span>

<span data-ttu-id="311f2-151"><xref:Microsoft.AspNetCore.Http.Features.ITlsConnectionFeature>: Définit une API pour la récupération des certificats clients.</span><span class="sxs-lookup"><span data-stu-id="311f2-151"><xref:Microsoft.AspNetCore.Http.Features.ITlsConnectionFeature>: Defines an API for retrieving client certificates.</span></span>

<span data-ttu-id="311f2-152"><xref:Microsoft.AspNetCore.Http.Features.ITlsTokenBindingFeature>: Définit des méthodes pour l’utilisation des paramètres de liaison de jeton TLS.</span><span class="sxs-lookup"><span data-stu-id="311f2-152"><xref:Microsoft.AspNetCore.Http.Features.ITlsTokenBindingFeature>: Defines methods for working with TLS token binding parameters.</span></span>
   
::: moniker range=">= aspnetcore-2.2"
   
<span data-ttu-id="311f2-153"><xref:Microsoft.AspNetCore.Http.Features.ITrackingConsentFeature>: Utilisé pour interroger, accorder et retirer le consentement de l’utilisateur concernant le stockage des informations utilisateur relatives à l’activité et aux fonctionnalités du site.</span><span class="sxs-lookup"><span data-stu-id="311f2-153"><xref:Microsoft.AspNetCore.Http.Features.ITrackingConsentFeature>: Used to query, grant, and withdraw user consent regarding the storage of user information related to site activity and functionality.</span></span>
   
::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="311f2-154">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="311f2-154">Additional resources</span></span>

* <xref:fundamentals/servers/index>
* <xref:fundamentals/middleware/index>
