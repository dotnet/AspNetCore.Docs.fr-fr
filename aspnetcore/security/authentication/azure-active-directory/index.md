---
title: Plateforme d’identité Microsoft et Azure Active Directory avec ASP.NET Core
author: rick-anderson
description: Découvrez les rubriques relatives à l’authentification avec Microsoft Identity Platform Azure Active Directory pour les applications Web et les API dans ASP.NET Core.
ms.author: riande
ms.date: 01/21/2020
ms.custom: mvc, seodec18
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
uid: security/authentication/azure-active-directory/index
ms.openlocfilehash: c8a3d6838b9b93ff58dfaff8423bd6946b5ca743
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061403"
---
# <a name="azure-active-directory-with-aspnet-core"></a><span data-ttu-id="ddeca-103">Azure Active Directory avec ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ddeca-103">Azure Active Directory with ASP.NET Core</span></span>

<span data-ttu-id="ddeca-104">Ces didacticiels et exemples illustrent l’authentification dans ASP.NET Core à l’aide de la plateforme d’identité Microsoft et Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="ddeca-104">These tutorials and samples demonstrate authentication in ASP.NET Core using Microsoft identity platform and Azure Active Directory.</span></span> <span data-ttu-id="ddeca-105">Pour obtenir des didacticiels et des exemples supplémentaires sur l’utilisation de ASP.NET Core avec Azure AD, consultez [plateforme d’identité Microsoft](/azure/active-directory/develop/).</span><span class="sxs-lookup"><span data-stu-id="ddeca-105">For additional tutorials and samples using ASP.NET Core with Azure AD, see [Microsoft identity platform](/azure/active-directory/develop/).</span></span>

## <a name="application-scenarios"></a><span data-ttu-id="ddeca-106">Scénarios d’application</span><span class="sxs-lookup"><span data-stu-id="ddeca-106">Application Scenarios</span></span>

* [<span data-ttu-id="ddeca-107">Démarrage rapide : Ajouter la connexion avec Microsoft à une application web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ddeca-107">Quickstart: Add sign-in with Microsoft to an ASP.NET Core web app</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [<span data-ttu-id="ddeca-108">Application web qui connecte les utilisateurs</span><span class="sxs-lookup"><span data-stu-id="ddeca-108">Web app that signs in users</span></span>](/azure/active-directory/develop/scenario-web-app-sign-user-overview?tabs=aspnetcore)
* [<span data-ttu-id="ddeca-109">Application web qui appelle des API web</span><span class="sxs-lookup"><span data-stu-id="ddeca-109">Web app that calls web APIs</span></span>](/azure/active-directory/develop/scenario-web-app-call-api-overview)
* [<span data-ttu-id="ddeca-110">API web protégée</span><span class="sxs-lookup"><span data-stu-id="ddeca-110">Protected web API</span></span>](/azure/active-directory/develop/scenario-protected-web-api-overview)
* [<span data-ttu-id="ddeca-111">API web qui appelle d’autres API web</span><span class="sxs-lookup"><span data-stu-id="ddeca-111">Web API that calls other web APIs</span></span>](/azure/active-directory/develop/scenario-web-api-call-api-overview)
* [<span data-ttu-id="ddeca-112">Application Web qui connecte les utilisateurs avec Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="ddeca-112">Web app that signs in users with Azure AD B2C</span></span>](xref:security/authentication/azure-ad-b2c)

## <a name="samples"></a><span data-ttu-id="ddeca-113">Exemples</span><span class="sxs-lookup"><span data-stu-id="ddeca-113">Samples</span></span>

* <span data-ttu-id="ddeca-114">[Activez votre application ASP.net Core pour connecter des utilisateurs et appeler des API Web à l’aide de Azure ad v2](/samples/azure-samples/active-directory-aspnetcore-webapp-openidconnect-v2/enable-webapp-signin/):</span><span class="sxs-lookup"><span data-stu-id="ddeca-114">[Enable your ASP.NET Core app to sign-in users and call web APIs using Azure AD V2](/samples/azure-samples/active-directory-aspnetcore-webapp-openidconnect-v2/enable-webapp-signin/):</span></span> 
  * <span data-ttu-id="ddeca-115">Regarder [cette vidéo associée](https://channel9.msdn.com/Events/Build/2018/THR5001)</span><span class="sxs-lookup"><span data-stu-id="ddeca-115">See [this associated video](https://channel9.msdn.com/Events/Build/2018/THR5001)</span></span>
* <span data-ttu-id="ddeca-116">[Appel d’une API Web ASP.NET Core 2,0 à partir d’une application WPF à l’aide de Azure ad v2](/samples/azure-samples/active-directory-dotnet-native-aspnetcore-v2/calling-an-aspnet-core-web-api-from-a-wpf-application-using-azure-ad-v2/):</span><span class="sxs-lookup"><span data-stu-id="ddeca-116">[Calling an ASP.NET Core 2.0 Web API from a WPF application using Azure AD V2](/samples/azure-samples/active-directory-dotnet-native-aspnetcore-v2/calling-an-aspnet-core-web-api-from-a-wpf-application-using-azure-ad-v2/):</span></span> 
  * <span data-ttu-id="ddeca-117">Regarder [cette vidéo associée](https://channel9.msdn.com/Events/Build/2018/THR5000)</span><span class="sxs-lookup"><span data-stu-id="ddeca-117">See [this associated video](https://channel9.msdn.com/Events/Build/2018/THR5000)</span></span>
* [<span data-ttu-id="ddeca-118">Une application web ASP.NET Core Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="ddeca-118">An ASP.NET Core web app with Azure AD B2C</span></span>](/samples/azure-samples/active-directory-b2c-dotnetcore-webapp/an-aspnet-core-web-app-with-azure-ad-b2c/)
