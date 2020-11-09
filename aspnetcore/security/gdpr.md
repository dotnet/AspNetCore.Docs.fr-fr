---
title: Prise en charge des Règlement général sur la protection des données (RGPD) dans ASP.NET Core
author: rick-anderson
description: Découvrez comment accéder aux points d’extension RGPD dans une application Web ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
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
uid: security/gdpr
ms.openlocfilehash: ec65a2c8362c15716bebd6b22f5639785ba74c98
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051003"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a><span data-ttu-id="1eff3-103">Prise en charge de l’UE Règlement général sur la protection des données (RGPD) dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1eff3-103">EU General Data Protection Regulation (GDPR) support in ASP.NET Core</span></span>

<span data-ttu-id="1eff3-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1eff3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="1eff3-105">ASP.NET Core fournit des API et des modèles pour vous aider à répondre à certaines exigences de l' [ue Règlement général sur la protection des données (RGPD)](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en) :</span><span class="sxs-lookup"><span data-stu-id="1eff3-105">ASP.NET Core provides APIs and templates to help meet some of the [EU General Data Protection Regulation (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en) requirements:</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="1eff3-106">Les modèles de projet incluent des points d’extension et le balisage stub que vous pouvez remplacer par votre stratégie de confidentialité et d' cookie utilisation.</span><span class="sxs-lookup"><span data-stu-id="1eff3-106">The project templates include extension points and stubbed markup that you can replace with your privacy and cookie use policy.</span></span>
* <span data-ttu-id="1eff3-107">La page *pages/privacy. cshtml* ou *vues/page d’affichage/privé/confidentialité. cshtml* fournit une page pour détailler la politique de confidentialité de votre site.</span><span class="sxs-lookup"><span data-stu-id="1eff3-107">The *Pages/Privacy.cshtml* page or *Views/Home/Privacy.cshtml* view provides a page to detail your site's privacy policy.</span></span>

<span data-ttu-id="1eff3-108">Pour activer la fonctionnalité de consentement par défaut cookie qui se trouve dans les modèles ASP.NET Core 2,2 dans une application générée par le modèle ASP.NET Core 3,0, procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="1eff3-108">To enable the default cookie consent feature like that found in the ASP.NET Core 2.2 templates in an ASP.NET Core 3.0 template generated app:</span></span>

* <span data-ttu-id="1eff3-109">Ajoutez `using Microsoft.AspNetCore.Http` à la liste des directives using.</span><span class="sxs-lookup"><span data-stu-id="1eff3-109">Add `using Microsoft.AspNetCore.Http` to the list of using directives.</span></span>
* <span data-ttu-id="1eff3-110">Ajoutez [ Cookie PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) à `Startup.ConfigureServices` et [Utilisez la Cookie stratégie](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) pour `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="1eff3-110">Add [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) to `Startup.ConfigureServices` and [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) to `Startup.Configure`:</span></span>

  [!code-csharp[Main](gdpr/sample/RP3.0/Startup.cs?name=snippet1&highlight=12-19,38)]

* <span data-ttu-id="1eff3-111">Ajoutez le cookie consentement partiel au fichier *_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="1eff3-111">Add the cookie consent partial to the *_Layout.cshtml* file:</span></span>

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_Layout.cshtml?name=snippet&highlight=4)]

* <span data-ttu-id="1eff3-112">Ajoutez le fichier *\_ Cookie ConsentPartial. cshtml* au projet :</span><span class="sxs-lookup"><span data-stu-id="1eff3-112">Add the *\_CookieConsentPartial.cshtml* file to the project:</span></span>

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_CookieConsentPartial.cshtml)]

* <span data-ttu-id="1eff3-113">Sélectionnez la version ASP.NET Core 2,2 de cet article pour en savoir plus sur la cookie fonctionnalité de consentement.</span><span class="sxs-lookup"><span data-stu-id="1eff3-113">Select the ASP.NET Core 2.2 version of this article to read about the cookie consent feature.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* <span data-ttu-id="1eff3-114">Les modèles de projet incluent des points d’extension et le balisage stub que vous pouvez remplacer par votre stratégie de confidentialité et d' cookie utilisation.</span><span class="sxs-lookup"><span data-stu-id="1eff3-114">The project templates include extension points and stubbed markup that you can replace with your privacy and cookie use policy.</span></span>
* <span data-ttu-id="1eff3-115">Une cookie fonctionnalité de consentement vous permet de demander (et de suivre) le consentement de vos utilisateurs pour le stockage des informations personnelles.</span><span class="sxs-lookup"><span data-stu-id="1eff3-115">A cookie consent feature allows you to ask for (and track) consent from your users for storing personal information.</span></span> <span data-ttu-id="1eff3-116">Si un utilisateur n’a pas consenti à la collecte de données et que l’application a [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) défini sur `true` , cookie les s non essentiels ne sont pas envoyées au navigateur.</span><span class="sxs-lookup"><span data-stu-id="1eff3-116">If a user hasn't consented to data collection and the app has [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) set to `true`, non-essential cookies aren't sent to the browser.</span></span>
* <span data-ttu-id="1eff3-117">Cookieles s peuvent être marquées comme essentielles.</span><span class="sxs-lookup"><span data-stu-id="1eff3-117">Cookies can be marked as essential.</span></span> <span data-ttu-id="1eff3-118">Les éléments essentiels cookie sont envoyés au navigateur même lorsque l’utilisateur n’a pas donné son consentement et que le suivi est désactivé.</span><span class="sxs-lookup"><span data-stu-id="1eff3-118">Essential cookies are sent to the browser even when the user hasn't consented and tracking is disabled.</span></span>
* <span data-ttu-id="1eff3-119">[TempData et la session cookie s](#tempdata) ne sont pas fonctionnels lorsque le suivi est désactivé.</span><span class="sxs-lookup"><span data-stu-id="1eff3-119">[TempData and Session cookies](#tempdata) aren't functional when tracking is disabled.</span></span>
* <span data-ttu-id="1eff3-120">La page [ Identity gérer](#pd) fournit un lien permettant de télécharger et de supprimer des données utilisateur.</span><span class="sxs-lookup"><span data-stu-id="1eff3-120">The [Identity manage](#pd) page provides a link to download and delete user data.</span></span>

<span data-ttu-id="1eff3-121">L' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) vous permet de tester la plupart des points d’extension RGPD et des API ajoutés aux modèles ASP.net Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="1eff3-121">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) allows you test most of the GDPR extension points and APIs added to the ASP.NET Core 2.1 templates.</span></span> <span data-ttu-id="1eff3-122">Consultez le fichier [Lisez-moi](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) pour obtenir des instructions de test.</span><span class="sxs-lookup"><span data-stu-id="1eff3-122">See the [ReadMe](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) file for testing instructions.</span></span>

<span data-ttu-id="1eff3-123">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1eff3-123">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a><span data-ttu-id="1eff3-124">ASP.NET Core la prise en charge RGPD dans le code généré par modèle</span><span class="sxs-lookup"><span data-stu-id="1eff3-124">ASP.NET Core GDPR support in template-generated code</span></span>

<span data-ttu-id="1eff3-125">Razor Les pages et les projets MVC créés avec les modèles de projet incluent la prise en charge RGPD suivante :</span><span class="sxs-lookup"><span data-stu-id="1eff3-125">Razor Pages and MVC projects created with the project templates include the following GDPR support:</span></span>

* <span data-ttu-id="1eff3-126">[ Cookie PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) et [la Cookie stratégie d’utilisation](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) sont définis dans la `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="1eff3-126">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) and [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) are set in the `Startup` class.</span></span>
* <span data-ttu-id="1eff3-127">[Vue partielle](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) *\_ Cookie ConsentPartial. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="1eff3-127">The *\_CookieConsentPartial.cshtml* [partial view](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span></span> <span data-ttu-id="1eff3-128">Un bouton **accepter** est inclus dans ce fichier.</span><span class="sxs-lookup"><span data-stu-id="1eff3-128">An **Accept** button is included in this file.</span></span> <span data-ttu-id="1eff3-129">Quand l’utilisateur clique sur le bouton **accepter** , le consentement pour les magasins cookie est fourni.</span><span class="sxs-lookup"><span data-stu-id="1eff3-129">When the user clicks the **Accept** button, consent to store cookies is provided.</span></span>
* <span data-ttu-id="1eff3-130">La page *pages/privacy. cshtml* ou *vues/page d’affichage/privé/confidentialité. cshtml* fournit une page pour détailler la politique de confidentialité de votre site.</span><span class="sxs-lookup"><span data-stu-id="1eff3-130">The *Pages/Privacy.cshtml* page or *Views/Home/Privacy.cshtml* view provides a page to detail your site's privacy policy.</span></span> <span data-ttu-id="1eff3-131">Le fichier *\_ Cookie ConsentPartial. cshtml* génère un lien vers la page de confidentialité.</span><span class="sxs-lookup"><span data-stu-id="1eff3-131">The *\_CookieConsentPartial.cshtml* file generates a link to the Privacy page.</span></span>
* <span data-ttu-id="1eff3-132">Pour les applications créées avec des comptes d’utilisateur individuels, la page gérer fournit des liens permettant de télécharger et de supprimer des [données utilisateur personnelles](#pd).</span><span class="sxs-lookup"><span data-stu-id="1eff3-132">For apps created with individual user accounts, the Manage page provides links to download and delete [personal user data](#pd).</span></span>

### <a name="no-loccookiepolicyoptions-and-useno-loccookiepolicy"></a><span data-ttu-id="1eff3-133">CookiePolicyOptions et utiliser la Cookie stratégie</span><span class="sxs-lookup"><span data-stu-id="1eff3-133">CookiePolicyOptions and UseCookiePolicy</span></span>

<span data-ttu-id="1eff3-134">Les [ Cookie PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) sont initialisés dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="1eff3-134">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) are initialized in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

<span data-ttu-id="1eff3-135">[Utilisez Cookie La stratégie](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) est appelée dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="1eff3-135">[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) is called in `Startup.Configure`:</span></span>

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="_no-loccookieconsentpartialcshtml-partial-view"></a><span data-ttu-id="1eff3-136">\_CookieVue partielle de ConsentPartial. cshtml</span><span class="sxs-lookup"><span data-stu-id="1eff3-136">\_CookieConsentPartial.cshtml partial view</span></span>

<span data-ttu-id="1eff3-137">Vue partielle *\_ Cookie ConsentPartial. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="1eff3-137">The *\_CookieConsentPartial.cshtml* partial view:</span></span>

[!code-cshtml[](gdpr/sample/RP2.2/Pages/Shared/_CookieConsentPartial.cshtml)]

<span data-ttu-id="1eff3-138">Voici ce qui est partiel :</span><span class="sxs-lookup"><span data-stu-id="1eff3-138">This partial:</span></span>

* <span data-ttu-id="1eff3-139">Obtient l’état de suivi de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="1eff3-139">Obtains the state of tracking for the user.</span></span> <span data-ttu-id="1eff3-140">Si l’application est configurée pour exiger un consentement, l’utilisateur doit donner son consentement avant que les cookie s puissent être suivies.</span><span class="sxs-lookup"><span data-stu-id="1eff3-140">If the app is configured to require consent, the user must consent before cookies can be tracked.</span></span> <span data-ttu-id="1eff3-141">Si le consentement est requis, le cookie volet de consentement est corrigé en haut de la barre de navigation créée par le fichier *\_ Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="1eff3-141">If consent is required, the cookie consent panel is fixed at top of the navigation bar created by the *\_Layout.cshtml* file.</span></span>
* <span data-ttu-id="1eff3-142">Fournit un `<p>` élément HTML pour résumer votre stratégie de confidentialité et d' cookie utilisation.</span><span class="sxs-lookup"><span data-stu-id="1eff3-142">Provides an HTML `<p>` element to summarize your privacy and cookie use policy.</span></span>
* <span data-ttu-id="1eff3-143">Fournit un lien vers la page de confidentialité ou une vue qui vous permet de détailler la politique de confidentialité de votre site.</span><span class="sxs-lookup"><span data-stu-id="1eff3-143">Provides a link to Privacy page or view where you can detail your site's privacy policy.</span></span>

## <a name="essential-no-loccookies"></a><span data-ttu-id="1eff3-144">cookieS essentiels</span><span class="sxs-lookup"><span data-stu-id="1eff3-144">Essential cookies</span></span>

<span data-ttu-id="1eff3-145">Si le consentement au stockage cookie n’a pas été fourni, seuls cookie les marqués comme essentiels sont envoyés au navigateur.</span><span class="sxs-lookup"><span data-stu-id="1eff3-145">If consent to store cookies hasn't been provided, only cookies marked essential are sent to the browser.</span></span> <span data-ttu-id="1eff3-146">Le code suivant rend un cookie élément essentiel :</span><span class="sxs-lookup"><span data-stu-id="1eff3-146">The following code makes a cookie essential:</span></span>

[!code-csharp[Main](gdpr/sample/RP2.2/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-no-loccookies-arent-essential"></a><span data-ttu-id="1eff3-147">Le fournisseur TempData et l’état de session cookie ne sont pas essentiels</span><span class="sxs-lookup"><span data-stu-id="1eff3-147">TempData provider and session state cookies aren't essential</span></span>

<span data-ttu-id="1eff3-148">Le [fournisseur TempData](xref:fundamentals/app-state#tempdata) cookie n’est pas essentiel.</span><span class="sxs-lookup"><span data-stu-id="1eff3-148">The [TempData provider](xref:fundamentals/app-state#tempdata) cookie isn't essential.</span></span> <span data-ttu-id="1eff3-149">Si le suivi est désactivé, le fournisseur TempData n’est pas fonctionnel.</span><span class="sxs-lookup"><span data-stu-id="1eff3-149">If tracking is disabled, the TempData provider isn't functional.</span></span> <span data-ttu-id="1eff3-150">Pour activer le fournisseur TempData lorsque le suivi est désactivé, marquez le TempData cookie comme essentiel dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="1eff3-150">To enable the TempData provider when tracking is disabled, mark the TempData cookie as essential in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/RP2.2/Startup.cs?name=snippet1)]

<span data-ttu-id="1eff3-151">[État](xref:fundamentals/app-state) cookie de session les s ne sont pas essentiels.</span><span class="sxs-lookup"><span data-stu-id="1eff3-151">[Session state](xref:fundamentals/app-state) cookies are not essential.</span></span> <span data-ttu-id="1eff3-152">L’état de session n’est pas fonctionnel lorsque le suivi est désactivé.</span><span class="sxs-lookup"><span data-stu-id="1eff3-152">Session state isn't functional when tracking is disabled.</span></span> <span data-ttu-id="1eff3-153">Le code suivant rend la session cookie s essentielle :</span><span class="sxs-lookup"><span data-stu-id="1eff3-153">The following code makes session cookies essential:</span></span>

[!code-csharp[](gdpr/sample/RP2.2/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a><span data-ttu-id="1eff3-154">Données à caractère personnel</span><span class="sxs-lookup"><span data-stu-id="1eff3-154">Personal data</span></span>

<span data-ttu-id="1eff3-155">ASP.NET Core applications créées avec des comptes d’utilisateur individuels incluent du code pour télécharger et supprimer des données personnelles.</span><span class="sxs-lookup"><span data-stu-id="1eff3-155">ASP.NET Core apps created with individual user accounts include code to download and delete personal data.</span></span>

<span data-ttu-id="1eff3-156">Sélectionnez le nom d’utilisateur, puis sélectionnez **données personnelles** :</span><span class="sxs-lookup"><span data-stu-id="1eff3-156">Select the user name and then select **Personal data** :</span></span>

![Page gérer les données personnelles](gdpr/_static/pd.png)

<span data-ttu-id="1eff3-158">Remarques :</span><span class="sxs-lookup"><span data-stu-id="1eff3-158">Notes:</span></span>

* <span data-ttu-id="1eff3-159">Pour générer le `Account/Manage` code, consultez [structure Identity ](xref:security/authentication/scaffold-identity).</span><span class="sxs-lookup"><span data-stu-id="1eff3-159">To generate the `Account/Manage` code, see [Scaffold Identity](xref:security/authentication/scaffold-identity).</span></span>
* <span data-ttu-id="1eff3-160">Les liens **Delete** et **Download** agissent uniquement sur les données d’identité par défaut.</span><span class="sxs-lookup"><span data-stu-id="1eff3-160">The **Delete** and **Download** links only act on the default identity data.</span></span> <span data-ttu-id="1eff3-161">Les applications qui créent des données utilisateur personnalisées doivent être étendues pour supprimer/télécharger les données utilisateur personnalisées.</span><span class="sxs-lookup"><span data-stu-id="1eff3-161">Apps that create custom user data must be extended to delete/download the custom user data.</span></span> <span data-ttu-id="1eff3-162">Pour plus d’informations, consultez [Ajouter, télécharger et supprimer des données utilisateur personnalisées dans Identity ](xref:security/authentication/add-user-data).</span><span class="sxs-lookup"><span data-stu-id="1eff3-162">For more information, see [Add, download, and delete custom user data to Identity](xref:security/authentication/add-user-data).</span></span>
* <span data-ttu-id="1eff3-163">Les jetons enregistrés pour l’utilisateur qui sont stockés dans la Identity table de base de données `AspNetUserTokens` sont supprimés lorsque l’utilisateur est supprimé par le biais du comportement de suppression en cascade en raison de la [clé étrangère](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).</span><span class="sxs-lookup"><span data-stu-id="1eff3-163">Saved tokens for the user that are stored in the Identity database table `AspNetUserTokens` are deleted when the user is deleted via the cascading delete behavior due to the [foreign key](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).</span></span>
* <span data-ttu-id="1eff3-164">L' [authentification du fournisseur externe](xref:security/authentication/social/index), telle que Facebook et Google, n’est pas disponible avant que la cookie stratégie soit acceptée.</span><span class="sxs-lookup"><span data-stu-id="1eff3-164">[External provider authentication](xref:security/authentication/social/index), such as Facebook and Google, isn't available before the cookie policy is accepted.</span></span>

::: moniker-end

## <a name="encryption-at-rest"></a><span data-ttu-id="1eff3-165">Chiffrement au repos</span><span class="sxs-lookup"><span data-stu-id="1eff3-165">Encryption at rest</span></span>

<span data-ttu-id="1eff3-166">Certaines bases de données et mécanismes de stockage permettent le chiffrement au repos.</span><span class="sxs-lookup"><span data-stu-id="1eff3-166">Some databases and storage mechanisms allow for encryption at rest.</span></span> <span data-ttu-id="1eff3-167">Chiffrement au repos :</span><span class="sxs-lookup"><span data-stu-id="1eff3-167">Encryption at rest:</span></span>

* <span data-ttu-id="1eff3-168">Chiffre automatiquement les données stockées.</span><span class="sxs-lookup"><span data-stu-id="1eff3-168">Encrypts stored data automatically.</span></span>
* <span data-ttu-id="1eff3-169">Chiffre sans configuration, programmation ou autre travail pour le logiciel qui accède aux données.</span><span class="sxs-lookup"><span data-stu-id="1eff3-169">Encrypts without configuration, programming, or other work for the software that accesses the data.</span></span>
* <span data-ttu-id="1eff3-170">Est l’option la plus simple et la plus sûre.</span><span class="sxs-lookup"><span data-stu-id="1eff3-170">Is the easiest and safest option.</span></span>
* <span data-ttu-id="1eff3-171">Permet à la base de données de gérer les clés et le chiffrement.</span><span class="sxs-lookup"><span data-stu-id="1eff3-171">Allows the database to manage keys and encryption.</span></span>

<span data-ttu-id="1eff3-172">Exemple :</span><span class="sxs-lookup"><span data-stu-id="1eff3-172">For example:</span></span>

* <span data-ttu-id="1eff3-173">Microsoft SQL et Azure SQL fournissent des [transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).</span><span class="sxs-lookup"><span data-stu-id="1eff3-173">Microsoft SQL and Azure SQL provide [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).</span></span>
* [<span data-ttu-id="1eff3-174">SQL Azure chiffre par défaut la base de données</span><span class="sxs-lookup"><span data-stu-id="1eff3-174">SQL Azure encrypts the database by default</span></span>](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* <span data-ttu-id="1eff3-175">[Les objets BLOB, les fichiers, les tables et le stockage de files d’attente Azure sont chiffrés par défaut](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).</span><span class="sxs-lookup"><span data-stu-id="1eff3-175">[Azure Blobs, Files, Table, and Queue Storage are encrypted by default](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).</span></span>

<span data-ttu-id="1eff3-176">Pour les bases de données qui ne fournissent pas de chiffrement intégré au repos, vous pouvez utiliser le chiffrement de disque pour offrir la même protection.</span><span class="sxs-lookup"><span data-stu-id="1eff3-176">For databases that don't provide built-in encryption at rest, you may be able to use disk encryption to provide the same protection.</span></span> <span data-ttu-id="1eff3-177">Exemple :</span><span class="sxs-lookup"><span data-stu-id="1eff3-177">For example:</span></span>

* [<span data-ttu-id="1eff3-178">BitLocker pour Windows Server</span><span class="sxs-lookup"><span data-stu-id="1eff3-178">BitLocker for Windows Server</span></span>](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* <span data-ttu-id="1eff3-179">Linux :</span><span class="sxs-lookup"><span data-stu-id="1eff3-179">Linux:</span></span>
  * [<span data-ttu-id="1eff3-180">eCryptfs</span><span class="sxs-lookup"><span data-stu-id="1eff3-180">eCryptfs</span></span>](https://launchpad.net/ecryptfs)
  * <span data-ttu-id="1eff3-181">[Encfs](https://github.com/vgough/encfs).</span><span class="sxs-lookup"><span data-stu-id="1eff3-181">[EncFS](https://github.com/vgough/encfs).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1eff3-182">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="1eff3-182">Additional resources</span></span>

* [<span data-ttu-id="1eff3-183">Microsoft.com/GDPR</span><span class="sxs-lookup"><span data-stu-id="1eff3-183">Microsoft.com/GDPR</span></span>](https://www.microsoft.com/trustcenter/Privacy/GDPR)
* [<span data-ttu-id="1eff3-184">RGPD-ajout d’un bouton de consentement REVOKE dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1eff3-184">GDPR - Adding a Revoke Consent Button in ASP.NET Core</span></span>](https://www.joeaudette.com/blog/2018/08/28/gdpr---adding-a-revoke-consent-button-in-aspnet-core)
