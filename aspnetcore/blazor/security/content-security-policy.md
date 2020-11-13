---
title: 'Appliquer une stratégie de sécurité de contenu pour ASP.NET Core :::no-loc(Blazor):::'
author: guardrex
description: 'Découvrez comment utiliser une stratégie de sécurité de contenu (CSP) avec des :::no-loc(Blazor)::: applications de ASP.net Core pour vous protéger contre les attaques de script entre sites (XSS).'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/security/content-security-policy
ms.openlocfilehash: 744449240fabc3dae317d0d7bc9090311521c224
ms.sourcegitcommit: 1ea3f23bec63e96ffc3a927992f30a5fc0de3ff9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94570118"
---
# <a name="enforce-a-content-security-policy-for-aspnet-core-no-locblazor"></a><span data-ttu-id="c8a0f-103">Appliquer une stratégie de sécurité de contenu pour ASP.NET Core :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="c8a0f-103">Enforce a Content Security Policy for ASP.NET Core :::no-loc(Blazor):::</span></span>

<span data-ttu-id="c8a0f-104">Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c8a0f-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="c8a0f-105">L’exécution de scripts [entre sites (XSS)](xref:security/cross-site-scripting) est une faille de sécurité dans laquelle un attaquant place un ou plusieurs scripts malveillants côté client dans le contenu rendu d’une application.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-105">[Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) is a security vulnerability where an attacker places one or more malicious client-side scripts into an app's rendered content.</span></span> <span data-ttu-id="c8a0f-106">Une stratégie de sécurité de contenu (CSP) vous aide à vous protéger contre les attaques XSS en informant le navigateur de la validité :</span><span class="sxs-lookup"><span data-stu-id="c8a0f-106">A Content Security Policy (CSP) helps protect against XSS attacks by informing the browser of valid:</span></span>

* <span data-ttu-id="c8a0f-107">Sources pour le contenu chargé, y compris les scripts, les feuilles de style et les images.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-107">Sources for loaded content, including scripts, stylesheets, and images.</span></span>
* <span data-ttu-id="c8a0f-108">Actions effectuées par une page, en spécifiant des cibles d’URL autorisées de formulaires.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-108">Actions taken by a page, specifying permitted URL targets of forms.</span></span>
* <span data-ttu-id="c8a0f-109">Plug-ins qui peuvent être chargés.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-109">Plugins that can be loaded.</span></span>

<span data-ttu-id="c8a0f-110">Pour appliquer un CSP à une application, le développeur spécifie plusieurs *directives* de sécurité de contenu CSP dans un ou plusieurs `Content-Security-Policy` en-têtes ou `<meta>` balises.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-110">To apply a CSP to an app, the developer specifies several CSP content security *directives* in one or more `Content-Security-Policy` headers or `<meta>` tags.</span></span>

<span data-ttu-id="c8a0f-111">Les stratégies sont évaluées par le navigateur pendant le chargement d’une page.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-111">Policies are evaluated by the browser while a page is loading.</span></span> <span data-ttu-id="c8a0f-112">Le navigateur inspecte les sources de la page et détermine si elles répondent aux exigences des directives de sécurité du contenu.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-112">The browser inspects the page's sources and determines if they meet the requirements of the content security directives.</span></span> <span data-ttu-id="c8a0f-113">Lorsque les directives de stratégie ne sont pas respectées pour une ressource, le navigateur ne charge pas la ressource.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-113">When policy directives aren't met for a resource, the browser doesn't load the resource.</span></span> <span data-ttu-id="c8a0f-114">Par exemple, considérez une stratégie qui n’autorise pas les scripts tiers.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-114">For example, consider a policy that doesn't allow third-party scripts.</span></span> <span data-ttu-id="c8a0f-115">Quand une page contient une `<script>` balise avec une origine tierce dans l' `src` attribut, le navigateur empêche le chargement du script.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-115">When a page contains a `<script>` tag with a third-party origin in the `src` attribute, the browser prevents the script from loading.</span></span>

<span data-ttu-id="c8a0f-116">CSP est pris en charge dans la plupart des navigateurs mobiles et de bureau modernes, notamment chrome, Edge, Firefox, Opera et Safari.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-116">CSP is supported in most modern desktop and mobile browsers, including Chrome, Edge, Firefox, Opera, and Safari.</span></span> <span data-ttu-id="c8a0f-117">CSP est recommandé pour les :::no-loc(Blazor)::: applications.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-117">CSP is recommended for :::no-loc(Blazor)::: apps.</span></span>

## <a name="policy-directives"></a><span data-ttu-id="c8a0f-118">Directives de stratégie</span><span class="sxs-lookup"><span data-stu-id="c8a0f-118">Policy directives</span></span>

<span data-ttu-id="c8a0f-119">Au minimum, spécifiez les directives et les sources suivantes pour les :::no-loc(Blazor)::: applications.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-119">Minimally, specify the following directives and sources for :::no-loc(Blazor)::: apps.</span></span> <span data-ttu-id="c8a0f-120">Ajoutez des directives et des sources supplémentaires selon vos besoins.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-120">Add additional directives and sources as needed.</span></span> <span data-ttu-id="c8a0f-121">Les directives suivantes sont utilisées dans la section [appliquer la stratégie](#apply-the-policy) de cet article, où des exemples de stratégies de sécurité pour :::no-loc(Blazor WebAssembly)::: et :::no-loc(Blazor Server)::: sont fournis :</span><span class="sxs-lookup"><span data-stu-id="c8a0f-121">The following directives are used in the [Apply the policy](#apply-the-policy) section of this article, where example security policies for :::no-loc(Blazor WebAssembly)::: and :::no-loc(Blazor Server)::: are provided:</span></span>

* <span data-ttu-id="c8a0f-122">[base-URI](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): restreint les URL pour la balise d’une page `<base>` .</span><span class="sxs-lookup"><span data-stu-id="c8a0f-122">[base-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): Restricts the URLs for a page's `<base>` tag.</span></span> <span data-ttu-id="c8a0f-123">Spécifiez `self` pour indiquer que l’origine de l’application, y compris le schéma et le numéro de port, est une source valide.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-123">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="c8a0f-124">[bloc-tout-contenu mixte](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): empêche le chargement de contenu HTTP et HTTPS mixte.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-124">[block-all-mixed-content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): Prevents loading mixed HTTP and HTTPS content.</span></span>
* <span data-ttu-id="c8a0f-125">[default-SRC](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): indique une solution de secours pour les directives source qui ne sont pas explicitement spécifiées par la stratégie.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-125">[default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): Indicates a fallback for source directives that aren't explicitly specified by the policy.</span></span> <span data-ttu-id="c8a0f-126">Spécifiez `self` pour indiquer que l’origine de l’application, y compris le schéma et le numéro de port, est une source valide.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-126">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="c8a0f-127">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): indique des sources valides pour les images.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-127">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): Indicates valid sources for images.</span></span>
  * <span data-ttu-id="c8a0f-128">Spécifiez `data:` pour autoriser le chargement d’images à partir d' `data:` URL.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-128">Specify `data:` to permit loading images from `data:` URLs.</span></span>
  * <span data-ttu-id="c8a0f-129">Spécifiez `https:` pour autoriser le chargement d’images à partir de points de terminaison HTTPS.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-129">Specify `https:` to permit loading images from HTTPS endpoints.</span></span>
* <span data-ttu-id="c8a0f-130">[Object-SRC](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): indique des sources valides pour les `<object>` `<embed>` `<applet>` balises, et.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-130">[object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): Indicates valid sources for the `<object>`, `<embed>`, and `<applet>` tags.</span></span> <span data-ttu-id="c8a0f-131">Spécifiez `none` pour empêcher toutes les sources d’URL.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-131">Specify `none` to prevent all URL sources.</span></span>
* <span data-ttu-id="c8a0f-132">[script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): indique des sources valides pour les scripts.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-132">[script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): Indicates valid sources for scripts.</span></span>
  * <span data-ttu-id="c8a0f-133">Spécifiez la `https://stackpath.bootstrapcdn.com/` source de l’hôte pour les scripts de démarrage.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-133">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap scripts.</span></span>
  * <span data-ttu-id="c8a0f-134">Spécifiez `self` pour indiquer que l’origine de l’application, y compris le schéma et le numéro de port, est une source valide.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-134">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="c8a0f-135">Dans une :::no-loc(Blazor WebAssembly)::: application :</span><span class="sxs-lookup"><span data-stu-id="c8a0f-135">In a :::no-loc(Blazor WebAssembly)::: app:</span></span>
    * <span data-ttu-id="c8a0f-136">Spécifiez les hachages pour autoriser le chargement des scripts requis.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-136">Specify hashes to permit required scripts to load.</span></span>
    * <span data-ttu-id="c8a0f-137">Spécifiez `unsafe-eval` pour utiliser `eval()` les méthodes et afin de créer du code à partir de chaînes.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-137">Specify `unsafe-eval` to use `eval()` and methods for creating code from strings.</span></span>
  * <span data-ttu-id="c8a0f-138">Dans une :::no-loc(Blazor Server)::: application, spécifiez les hachages pour autoriser le chargement des scripts requis.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-138">In a :::no-loc(Blazor Server)::: app, specify hashes to permit required scripts to load.</span></span>
* <span data-ttu-id="c8a0f-139">[style-SRC](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): indique des sources valides pour les feuilles de style.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-139">[style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): Indicates valid sources for stylesheets.</span></span>
  * <span data-ttu-id="c8a0f-140">Spécifiez la `https://stackpath.bootstrapcdn.com/` source de l’hôte pour les feuilles de style de démarrage.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-140">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap stylesheets.</span></span>
  * <span data-ttu-id="c8a0f-141">Spécifiez `self` pour indiquer que l’origine de l’application, y compris le schéma et le numéro de port, est une source valide.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-141">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="c8a0f-142">Spécifiez `unsafe-inline` pour autoriser l’utilisation de styles intralignes.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-142">Specify `unsafe-inline` to allow the use of inline styles.</span></span> <span data-ttu-id="c8a0f-143">La déclaration inline est requise pour l’interface utilisateur dans les :::no-loc(Blazor Server)::: applications pour la reconnexion du client et du serveur après la demande initiale.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-143">The inline declaration is required for the UI in :::no-loc(Blazor Server)::: apps for reconnecting the client and server after the initial request.</span></span> <span data-ttu-id="c8a0f-144">Dans une version ultérieure, le style intraligne peut être supprimé afin qu' `unsafe-inline` il ne soit plus nécessaire.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-144">In a future release, inline styling might be removed so that `unsafe-inline` is no longer required.</span></span>
* <span data-ttu-id="c8a0f-145">[Upgrade-insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests): indique que les URL de contenu provenant de sources non sécurisées (http) doivent être acquises en toute sécurité via HTTPS.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-145">[upgrade-insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests): Indicates that content URLs from insecure (HTTP) sources should be acquired securely over HTTPS.</span></span>

<span data-ttu-id="c8a0f-146">Les directives précédentes sont prises en charge par tous les navigateurs, à l’exception de Microsoft Internet Explorer.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-146">The preceding directives are supported by all browsers except Microsoft Internet Explorer.</span></span>

<span data-ttu-id="c8a0f-147">Pour obtenir des hachages SHA pour d’autres scripts inline :</span><span class="sxs-lookup"><span data-stu-id="c8a0f-147">To obtain SHA hashes for additional inline scripts:</span></span>

* <span data-ttu-id="c8a0f-148">Appliquez le CSP présenté dans la section [appliquer la stratégie](#apply-the-policy) .</span><span class="sxs-lookup"><span data-stu-id="c8a0f-148">Apply the CSP shown in the [Apply the policy](#apply-the-policy) section.</span></span>
* <span data-ttu-id="c8a0f-149">Accédez à la console outils de développement du navigateur tout en exécutant l’application localement.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-149">Access the browser's developer tools console while running the app locally.</span></span> <span data-ttu-id="c8a0f-150">Le navigateur calcule et affiche les hachages pour les scripts bloqués lorsqu’un en-tête ou une `meta` balise CSP est présent.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-150">The browser calculates and displays hashes for blocked scripts when a CSP header or `meta` tag is present.</span></span>
* <span data-ttu-id="c8a0f-151">Copiez les hachages fournis par le navigateur dans les `script-src` sources.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-151">Copy the hashes provided by the browser to the `script-src` sources.</span></span> <span data-ttu-id="c8a0f-152">Utilisez des guillemets simples autour de chaque hachage.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-152">Use single quotes around each hash.</span></span>

<span data-ttu-id="c8a0f-153">Pour obtenir une matrice de prise en charge des navigateurs de niveau 2 de stratégie de sécurité de contenu, consultez la page [comment utiliser le niveau de stratégie de sécurité de contenu 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).</span><span class="sxs-lookup"><span data-stu-id="c8a0f-153">For a Content Security Policy Level 2 browser support matrix, see [Can I use: Content Security Policy Level 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).</span></span>

## <a name="apply-the-policy"></a><span data-ttu-id="c8a0f-154">Application de la stratégie</span><span class="sxs-lookup"><span data-stu-id="c8a0f-154">Apply the policy</span></span>

<span data-ttu-id="c8a0f-155">Utilisez une `<meta>` balise pour appliquer la stratégie :</span><span class="sxs-lookup"><span data-stu-id="c8a0f-155">Use a `<meta>` tag to apply the policy:</span></span>

* <span data-ttu-id="c8a0f-156">Affectez `http-equiv` à l’attribut la valeur `Content-Security-Policy` .</span><span class="sxs-lookup"><span data-stu-id="c8a0f-156">Set the value of the `http-equiv` attribute to `Content-Security-Policy`.</span></span>
* <span data-ttu-id="c8a0f-157">Placez les directives dans la `content` valeur de l’attribut.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-157">Place the directives in the `content` attribute value.</span></span> <span data-ttu-id="c8a0f-158">Séparez les directives par un point-virgule ( `;` ).</span><span class="sxs-lookup"><span data-stu-id="c8a0f-158">Separate directives with a semicolon (`;`).</span></span>
* <span data-ttu-id="c8a0f-159">Placez toujours la `meta` balise dans le `<head>` contenu.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-159">Always place the `meta` tag in the `<head>` content.</span></span>

<span data-ttu-id="c8a0f-160">Les sections suivantes présentent des exemples de stratégies pour :::no-loc(Blazor WebAssembly)::: et :::no-loc(Blazor Server)::: .</span><span class="sxs-lookup"><span data-stu-id="c8a0f-160">The following sections show example policies for :::no-loc(Blazor WebAssembly)::: and :::no-loc(Blazor Server):::.</span></span> <span data-ttu-id="c8a0f-161">Ces exemples sont associés à des versions de cet article pour chaque version de :::no-loc(Blazor)::: .</span><span class="sxs-lookup"><span data-stu-id="c8a0f-161">These examples are versioned with this article for each release of :::no-loc(Blazor):::.</span></span> <span data-ttu-id="c8a0f-162">Pour utiliser une version appropriée pour votre version, sélectionnez la version du document avec le sélecteur de **version** déroulante sur cette page Web.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-162">To use a version appropriate for your release, select the document version with the **Version** drop down selector on this webpage.</span></span>

### :::no-loc(Blazor WebAssembly):::

<span data-ttu-id="c8a0f-163">Dans la `<head>` page contenu de l' `wwwroot/index.html` hôte, appliquez les directives décrites dans la section [directives de stratégie](#policy-directives) :</span><span class="sxs-lookup"><span data-stu-id="c8a0f-163">In the `<head>` content of the `wwwroot/index.html` host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

::: moniker range=">= aspnetcore-5.0"

```html
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=' 
                          'unsafe-eval';
               style-src https://stackpath.bootstrapcdn.com/
                         'self'
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```html
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=' 
                          'sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=' 
                          'sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=' 
                          'unsafe-eval';
               style-src https://stackpath.bootstrapcdn.com/
                         'self'
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

::: moniker-end

<span data-ttu-id="c8a0f-164">Ajoutez `script-src` `style-src` les hachages supplémentaires requis par l’application.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-164">Add additional `script-src` and `style-src` hashes as required by the app.</span></span> <span data-ttu-id="c8a0f-165">Pendant le développement, utilisez un outil en ligne ou des outils de développement de navigateur pour que les hachages soient calculés pour vous.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-165">During development, use an online tool or browser developer tools to have the hashes calculated for you.</span></span> <span data-ttu-id="c8a0f-166">Par exemple, l’erreur de la console outils de navigateur suivante signale le hachage pour un script requis non couvert par la stratégie :</span><span class="sxs-lookup"><span data-stu-id="c8a0f-166">For example, the following browser tools console error reports the hash for a required script not covered by the policy:</span></span>

> <span data-ttu-id="c8a0f-167">Refus d’exécuter le script inline, car il viole la directive de stratégie de sécurité de contenu suivante : "... ".</span><span class="sxs-lookup"><span data-stu-id="c8a0f-167">Refused to execute inline script because it violates the following Content Security Policy directive: " ... ".</span></span> <span data-ttu-id="c8a0f-168">Le mot clé unsafe-Inline, un hash ('SHA256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA = ') ou une valeur à usage unique ('nonce-... ') est requis pour permettre l’exécution inline.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-168">Either the 'unsafe-inline' keyword, a hash ('sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA='), or a nonce ('nonce-...') is required to enable inline execution.</span></span>

<span data-ttu-id="c8a0f-169">Le script particulier associé à l’erreur s’affiche dans la console en regard de l’erreur.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-169">The particular script associated with the error is displayed in the console next to the error.</span></span>

### :::no-loc(Blazor Server):::

<span data-ttu-id="c8a0f-170">Dans la `<head>` page contenu de l' `Pages/_Host.cshtml` hôte, appliquez les directives décrites dans la section [directives de stratégie](#policy-directives) :</span><span class="sxs-lookup"><span data-stu-id="c8a0f-170">In the `<head>` content of the `Pages/_Host.cshtml` host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

::: moniker range=">= aspnetcore-5.0"

```cshtml
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self';
               style-src https://stackpath.bootstrapcdn.com/
                         'self' 
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```cshtml
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=';
               style-src https://stackpath.bootstrapcdn.com/
                         'self' 
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

::: moniker-end

<span data-ttu-id="c8a0f-171">Ajoutez `script-src` `style-src` les hachages supplémentaires requis par l’application.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-171">Add additional `script-src` and `style-src` hashes as required by the app.</span></span> <span data-ttu-id="c8a0f-172">Pendant le développement, utilisez un outil en ligne ou des outils de développement de navigateur pour que les hachages soient calculés pour vous.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-172">During development, use an online tool or browser developer tools to have the hashes calculated for you.</span></span> <span data-ttu-id="c8a0f-173">Par exemple, l’erreur de la console outils de navigateur suivante signale le hachage pour un script requis non couvert par la stratégie :</span><span class="sxs-lookup"><span data-stu-id="c8a0f-173">For example, the following browser tools console error reports the hash for a required script not covered by the policy:</span></span>

> <span data-ttu-id="c8a0f-174">Refus d’exécuter le script inline, car il viole la directive de stratégie de sécurité de contenu suivante : "... ".</span><span class="sxs-lookup"><span data-stu-id="c8a0f-174">Refused to execute inline script because it violates the following Content Security Policy directive: " ... ".</span></span> <span data-ttu-id="c8a0f-175">Le mot clé unsafe-Inline, un hash ('SHA256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA = ') ou une valeur à usage unique ('nonce-... ') est requis pour permettre l’exécution inline.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-175">Either the 'unsafe-inline' keyword, a hash ('sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA='), or a nonce ('nonce-...') is required to enable inline execution.</span></span>

<span data-ttu-id="c8a0f-176">Le script particulier associé à l’erreur s’affiche dans la console en regard de l’erreur.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-176">The particular script associated with the error is displayed in the console next to the error.</span></span>

## <a name="meta-tag-limitations"></a><span data-ttu-id="c8a0f-177">Limitations des balises meta</span><span class="sxs-lookup"><span data-stu-id="c8a0f-177">Meta tag limitations</span></span>

<span data-ttu-id="c8a0f-178">Une `<meta>` stratégie de balise ne prend pas en charge les directives suivantes :</span><span class="sxs-lookup"><span data-stu-id="c8a0f-178">A `<meta>` tag policy doesn't support the following directives:</span></span>

* [<span data-ttu-id="c8a0f-179">ancêtres de cadre</span><span class="sxs-lookup"><span data-stu-id="c8a0f-179">frame-ancestors</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [<span data-ttu-id="c8a0f-180">rapport-à</span><span class="sxs-lookup"><span data-stu-id="c8a0f-180">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="c8a0f-181">rapport-URI</span><span class="sxs-lookup"><span data-stu-id="c8a0f-181">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [<span data-ttu-id="c8a0f-182">immédiatement</span><span class="sxs-lookup"><span data-stu-id="c8a0f-182">sandbox</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

<span data-ttu-id="c8a0f-183">Pour prendre en charge les directives précédentes, utilisez un en-tête nommé `Content-Security-Policy` .</span><span class="sxs-lookup"><span data-stu-id="c8a0f-183">To support the preceding directives, use a header named `Content-Security-Policy`.</span></span> <span data-ttu-id="c8a0f-184">La chaîne de directive est la valeur de l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-184">The directive string is the header's value.</span></span>

## <a name="test-a-policy-and-receive-violation-reports"></a><span data-ttu-id="c8a0f-185">Tester une stratégie et recevoir des rapports de violation</span><span class="sxs-lookup"><span data-stu-id="c8a0f-185">Test a policy and receive violation reports</span></span>

<span data-ttu-id="c8a0f-186">Le test permet de vérifier que les scripts tiers ne sont pas bloqués par inadvertance lors de la création d’une stratégie initiale.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-186">Testing helps confirm that third-party scripts aren't inadvertently blocked when building an initial policy.</span></span>

<span data-ttu-id="c8a0f-187">Pour tester une stratégie sur une période sans appliquer les directives de stratégie, définissez le `<meta>` `http-equiv` nom d’en-tête ou d’attribut de la balise d’une stratégie basée sur un en-tête sur `Content-Security-Policy-Report-Only` .</span><span class="sxs-lookup"><span data-stu-id="c8a0f-187">To test a policy over a period of time without enforcing the policy directives, set the `<meta>` tag's `http-equiv` attribute or header name of a header-based policy to `Content-Security-Policy-Report-Only`.</span></span> <span data-ttu-id="c8a0f-188">Les rapports d’échec sont envoyés en tant que documents JSON à une URL spécifiée.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-188">Failure reports are sent as JSON documents to a specified URL.</span></span> <span data-ttu-id="c8a0f-189">Pour plus d’informations, consultez la [page documentation Web MDN : contenu-sécurité-stratégie-rapport uniquement](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).</span><span class="sxs-lookup"><span data-stu-id="c8a0f-189">For more information, see [MDN web docs: Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).</span></span>

<span data-ttu-id="c8a0f-190">Pour la création de rapports sur les violations pendant qu’une stratégie est active, consultez les articles suivants :</span><span class="sxs-lookup"><span data-stu-id="c8a0f-190">For reporting on violations while a policy is active, see the following articles:</span></span>

* [<span data-ttu-id="c8a0f-191">rapport-à</span><span class="sxs-lookup"><span data-stu-id="c8a0f-191">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="c8a0f-192">rapport-URI</span><span class="sxs-lookup"><span data-stu-id="c8a0f-192">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

<span data-ttu-id="c8a0f-193">Bien que ne `report-uri` soit plus recommandé pour une utilisation, les deux directives doivent être utilisées jusqu’à ce que `report-to` soit pris en charge par tous les principaux navigateurs.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-193">Although `report-uri` is no longer recommended for use, both directives should be used until `report-to` is supported by all of the major browsers.</span></span> <span data-ttu-id="c8a0f-194">N’utilisez pas exclusivement `report-uri` , car la prise en charge de `report-uri` est sujette à être supprimée à *tout moment* à partir des navigateurs.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-194">Don't exclusively use `report-uri` because support for `report-uri` is subject to being dropped *at any time* from browsers.</span></span> <span data-ttu-id="c8a0f-195">Supprimez la prise en charge de `report-uri` dans vos stratégies lorsque `report-to` est entièrement pris en charge.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-195">Remove support for `report-uri` in your policies when `report-to` is fully supported.</span></span> <span data-ttu-id="c8a0f-196">Pour suivre l’adoption de `report-to` , consultez [puis-je utiliser : Report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).</span><span class="sxs-lookup"><span data-stu-id="c8a0f-196">To track adoption of `report-to`, see [Can I use: report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).</span></span>

<span data-ttu-id="c8a0f-197">Testez et mettez à jour la stratégie d’une application chaque version.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-197">Test and update an app's policy every release.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="c8a0f-198">Dépanner</span><span class="sxs-lookup"><span data-stu-id="c8a0f-198">Troubleshoot</span></span>

* <span data-ttu-id="c8a0f-199">Les erreurs s’affichent dans la console outils de développement du navigateur.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-199">Errors appear in the browser's developer tools console.</span></span> <span data-ttu-id="c8a0f-200">Les navigateurs fournissent des informations sur les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="c8a0f-200">Browsers provide information about:</span></span>
  * <span data-ttu-id="c8a0f-201">Éléments qui ne sont pas conformes à la stratégie.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-201">Elements that don't comply with the policy.</span></span>
  * <span data-ttu-id="c8a0f-202">Comment modifier la stratégie pour autoriser un élément bloqué.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-202">How to modify the policy to allow for a blocked item.</span></span>
* <span data-ttu-id="c8a0f-203">Une stratégie est entièrement efficace lorsque le navigateur du client prend en charge toutes les directives incluses.</span><span class="sxs-lookup"><span data-stu-id="c8a0f-203">A policy is only completely effective when the client's browser supports all of the included directives.</span></span> <span data-ttu-id="c8a0f-204">Pour obtenir une matrice de prise en charge actuelle du navigateur, consultez Comment [utiliser : Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).</span><span class="sxs-lookup"><span data-stu-id="c8a0f-204">For a current browser support matrix, see [Can I use: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c8a0f-205">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="c8a0f-205">Additional resources</span></span>

* [<span data-ttu-id="c8a0f-206">MDN Web docs : Content-Security-Policy</span><span class="sxs-lookup"><span data-stu-id="c8a0f-206">MDN web docs: Content-Security-Policy</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [<span data-ttu-id="c8a0f-207">Niveau de stratégie de sécurité du contenu 2</span><span class="sxs-lookup"><span data-stu-id="c8a0f-207">Content Security Policy Level 2</span></span>](https://www.w3.org/TR/CSP2/)
* [<span data-ttu-id="c8a0f-208">Évaluateur Google CSP</span><span class="sxs-lookup"><span data-stu-id="c8a0f-208">Google CSP Evaluator</span></span>](https://csp-evaluator.withgoogle.com/)
