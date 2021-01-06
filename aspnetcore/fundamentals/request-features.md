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
# <a name="request-features-in-aspnet-core"></a>Fonctionnalités de requête dans ASP.NET Core

Par [Steve Smith](https://ardalis.com/)

L' `HttpContext` API utilisée par les applications et les intergiciels pour traiter les demandes a une couche d’abstraction en dessous de celle-ci appelée *interfaces de fonctionnalités*. Chaque interface de fonctionnalité fournit un sous-ensemble granulaire des fonctionnalités exposées par `HttpContext` . Ces interfaces peuvent être ajoutées, modifiées, encapsulées, remplacées ou même supprimées par le serveur ou l’intergiciel (middleware) lors du traitement de la demande sans avoir à réimplémenter l’ensemble du `HttpContext` . Elles peuvent également être utilisées pour simuler des fonctionnalités lors des tests.

## <a name="feature-collections"></a>Collections de fonctionnalités

La <xref:Microsoft.AspNetCore.Http.HttpContext.Features> propriété de `HttpContext` permet d’accéder à la collection d’interfaces de fonctionnalités de la requête actuelle. Étant donné que la collection de fonctionnalités est mutable même dans le contexte d’une requête, il est possible d’utiliser un intergiciel (middleware) pour modifier la collection et ajouter la prise en charge de fonctionnalités supplémentaires. Certaines fonctionnalités avancées sont uniquement disponibles en accédant à l’interface associée via la collection de fonctionnalités.

## <a name="feature-interfaces"></a>Interfaces de fonctionnalités

ASP.NET Core définit un certain nombre d’interfaces de fonctionnalités HTTP courantes dans <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName> , qui sont partagées par différents serveurs et middleware pour identifier les fonctionnalités qu’ils prennent en charge. Les serveurs et les intergiciels (middleware) peuvent également fournir leurs propres interfaces avec des fonctionnalités supplémentaires.

La plupart des interfaces de fonctionnalités fournissent des fonctionnalités facultatives, de mise en lumière et leurs `HttpContext` API associées fournissent des valeurs par défaut si la fonctionnalité n’est pas présente. Certaines interfaces sont indiquées dans le contenu suivant, selon les besoins, car elles fournissent les fonctionnalités principales de demande et de réponse et doivent être implémentées pour traiter la demande.

Les interfaces de fonctionnalités suivantes proviennent de <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName> :

<xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature>: Définit la structure d’une requête HTTP, y compris le protocole, le chemin d’accès, la chaîne de requête, les en-têtes et le corps. Cette fonctionnalité est nécessaire pour traiter les demandes.

<xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>: Définit la structure d’une réponse HTTP, y compris le code d’État, les en-têtes et le corps de la réponse. Cette fonctionnalité est nécessaire pour traiter les demandes.

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpResponseBodyFeature>: Définit différentes façons d’écrire le corps de la réponse, à l’aide d’un `Stream` , d’un `PipeWriter` ou d’un fichier. Cette fonctionnalité est nécessaire pour traiter les demandes. Cela remplace `IHttpResponseFeature.Body` et `IHttpSendFileFeature` .

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.Authentication.IHttpAuthenticationFeature>: Contient le <xref:System.Security.Claims.ClaimsPrincipal> actuellement associé à la demande.

<xref:Microsoft.AspNetCore.Http.Features.IFormFeature>: Permet d’analyser et de mettre en cache les envois de formulaire HTTP et multiparts entrants.

::: moniker range=">= aspnetcore-2.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpBodyControlFeature>: Utilisé pour contrôler si les opérations d’e/s synchrones sont autorisées pour le corps de la demande ou de la réponse.

::: moniker-end
   
::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpBufferingFeature>: Définit des méthodes pour désactiver la mise en mémoire tampon des demandes et/ou des réponses.

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IHttpConnectionFeature>: Définit les propriétés de l’ID de connexion et des adresses et des ports locaux et distants.

::: moniker range=">= aspnetcore-2.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>: Contrôle la taille maximale autorisée du corps de la requête pour la requête actuelle.

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

`IHttpRequestBodyDetectionFeature`: Indique si la demande peut avoir un corps.

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IHttpRequestIdentifierFeature>: Ajoute une propriété qui peut être implémentée pour identifier de manière unique les demandes.

<xref:Microsoft.AspNetCore.Http.Features.IHttpRequestLifetimeFeature>: Définit la prise en charge de l’abandon des connexions ou de la détection de l’arrêt prématuré d’une demande, par exemple lors de la déconnexion d’un client.

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpRequestTrailersFeature>: Fournit l’accès aux en-têtes de code de fin de demande, le cas échéant.

<xref:Microsoft.AspNetCore.Http.Features.IHttpResetFeature>: Utilisé pour envoyer des messages de réinitialisation pour les protocoles qui les prennent en charge, tels que HTTP/2 ou HTTP/3.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<xref:Microsoft.AspNetCore.Http.Features.IHttpResponseTrailersFeature>: Permet à l’application de fournir des en-têtes de code de fin de réponse s’ils sont pris en charge.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>: Définit une méthode pour envoyer des fichiers de façon asynchrone.

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IHttpUpgradeFeature>: Définit la prise en charge des [mises à niveau http](https://tools.ietf.org/html/rfc2616.html#section-14.42), ce qui permet au client de spécifier les protocoles supplémentaires qu’il souhaite utiliser si le serveur souhaite changer de protocole.

<xref:Microsoft.AspNetCore.Http.Features.IHttpWebSocketFeature>: Définit une API pour la prise en charge des sockets Web.

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpsCompressionFeature>: Contrôle si la compression de réponse doit être utilisée sur des connexions HTTPs.

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IItemsFeature>: Stocke la <xref:Microsoft.AspNetCore.Http.Features.IItemsFeature.Items> collection pour l’état de l’application par demande.

<xref:Microsoft.AspNetCore.Http.Features.IQueryFeature>: Analyse et met en cache la chaîne de requête.
   
::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IRequestBodyPipeFeature>: Représente le corps de la requête sous la forme d’un <xref:System.IO.Pipelines.PipeReader> .
 
::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IRequestCookiesFeature>: Analyse et met en cache les `Cookie` valeurs d’en-tête de demande.

<xref:Microsoft.AspNetCore.Http.Features.IResponseCookiesFeature>: Contrôle la manière dont cookie les réponses s sont appliquées à l' `Set-Cookie` en-tête.

::: moniker range=">= aspnetcore-2.2"

<xref:Microsoft.AspNetCore.Http.Features.IServerVariablesFeature>: Cette fonctionnalité permet d’accéder aux variables de serveur de demande, telles que celles fournies par IIS.

::: moniker-end
   
<xref:Microsoft.AspNetCore.Http.Features.IServiceProvidersFeature>: Fournit l’accès à un <xref:System.IServiceProvider> avec des services de requête étendus.

<xref:Microsoft.AspNetCore.Http.Features.ISessionFeature>: Définit `ISessionFactory` et <xref:Microsoft.AspNetCore.Http.ISession> abstractions pour la prise en charge des sessions utilisateur. `ISessionFeature` est implémenté par <xref:Microsoft.AspNetCore.Session.SessionMiddleware> (consultez <xref:fundamentals/app-state> ).

<xref:Microsoft.AspNetCore.Http.Features.ITlsConnectionFeature>: Définit une API pour la récupération des certificats clients.

<xref:Microsoft.AspNetCore.Http.Features.ITlsTokenBindingFeature>: Définit des méthodes pour l’utilisation des paramètres de liaison de jeton TLS.
   
::: moniker range=">= aspnetcore-2.2"
   
<xref:Microsoft.AspNetCore.Http.Features.ITrackingConsentFeature>: Utilisé pour interroger, accorder et retirer le consentement de l’utilisateur concernant le stockage des informations utilisateur relatives à l’activité et aux fonctionnalités du site.
   
::: moniker-end

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:fundamentals/servers/index>
* <xref:fundamentals/middleware/index>
