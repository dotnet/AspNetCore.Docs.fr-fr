---
title: Utilisez les services JavaScript pour créer des applications à page unique dans ASP.NET Core
author: scottaddie
description: Découvrez les avantages de l’utilisation des services JavaScript pour créer une application à page unique (SPA) sauvegardée par ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017, devx-track-js
ms.date: 09/06/2019
no-loc:
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
uid: client-side/spa-services
ms.openlocfilehash: 33ae16c033142aa1c0f4cea53ca746e8a6aaf052
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690664"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a><span data-ttu-id="51487-103">Utilisez les services JavaScript pour créer des applications à page unique dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="51487-103">Use JavaScript Services to Create Single Page Applications in ASP.NET Core</span></span>

<span data-ttu-id="51487-104">Par [Scott Addie](https://github.com/scottaddie) et [Fiyaz Hasan](https://fiyazhasan.me/)</span><span class="sxs-lookup"><span data-stu-id="51487-104">By [Scott Addie](https://github.com/scottaddie) and [Fiyaz Hasan](https://fiyazhasan.me/)</span></span>

<span data-ttu-id="51487-105">Une application à page unique (SPA) est un type d’application Web populaire en raison de son expérience utilisateur riche inhérente.</span><span class="sxs-lookup"><span data-stu-id="51487-105">A Single Page Application (SPA) is a popular type of web application due to its inherent rich user experience.</span></span> <span data-ttu-id="51487-106">L’intégration d’infrastructures ou de bibliothèques SPA côté client, telles que l' [angle](https://angular.io/) ou la [réaction](https://facebook.github.io/react/), avec des frameworks côté serveur, tels que les ASP.net Core peut s’avérer difficile.</span><span class="sxs-lookup"><span data-stu-id="51487-106">Integrating client-side SPA frameworks or libraries, such as [Angular](https://angular.io/) or [React](https://facebook.github.io/react/), with server-side frameworks such as ASP.NET Core can be difficult.</span></span> <span data-ttu-id="51487-107">Les services JavaScript ont été développés pour réduire les frottements dans le processus d’intégration.</span><span class="sxs-lookup"><span data-stu-id="51487-107">JavaScript Services was developed to reduce friction in the integration process.</span></span> <span data-ttu-id="51487-108">Il permet une opération transparente entre les différentes piles de technologies client et serveur.</span><span class="sxs-lookup"><span data-stu-id="51487-108">It enables seamless operation between the different client and server technology stacks.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="51487-109">Les fonctionnalités décrites dans cet article sont obsolètes à partir de ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="51487-109">The features described in this article are obsolete as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="51487-110">Un mécanisme d’intégration de frameworks SPA plus simple est disponible dans le package NuGet [Microsoft. AspNetCore. SpaServices. extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) .</span><span class="sxs-lookup"><span data-stu-id="51487-110">A simpler SPA frameworks integration mechanism is available in the [Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet package.</span></span> <span data-ttu-id="51487-111">Pour plus d’informations, consultez [[Announcement] Obsoleting Microsoft. AspNetCore. SpaServices et Microsoft. AspNetCore. NodeServices](https://github.com/dotnet/AspNetCore/issues/12890).</span><span class="sxs-lookup"><span data-stu-id="51487-111">For more information, see [[Announcement] Obsoleting Microsoft.AspNetCore.SpaServices and Microsoft.AspNetCore.NodeServices](https://github.com/dotnet/AspNetCore/issues/12890).</span></span>

::: moniker-end

## <a name="what-is-javascript-services"></a><span data-ttu-id="51487-112">Qu’est-ce que les services JavaScript ?</span><span class="sxs-lookup"><span data-stu-id="51487-112">What is JavaScript Services</span></span>

<span data-ttu-id="51487-113">Les services JavaScript sont une collection de technologies côté client pour ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="51487-113">JavaScript Services is a collection of client-side technologies for ASP.NET Core.</span></span> <span data-ttu-id="51487-114">Son objectif est de positionner ASP.NET Core en tant que plateforme côté serveur préférée des développeurs pour la création de l’application de cas.</span><span class="sxs-lookup"><span data-stu-id="51487-114">Its goal is to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span>

<span data-ttu-id="51487-115">Les services JavaScript sont constitués de deux packages NuGet distincts :</span><span class="sxs-lookup"><span data-stu-id="51487-115">JavaScript Services consists of two distinct NuGet packages:</span></span>

* <span data-ttu-id="51487-116">[Microsoft. AspNetCore. NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span><span class="sxs-lookup"><span data-stu-id="51487-116">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span></span>
* <span data-ttu-id="51487-117">[Microsoft. AspNetCore. SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span><span class="sxs-lookup"><span data-stu-id="51487-117">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span></span>

<span data-ttu-id="51487-118">Ces packages sont utiles dans les scénarios suivants :</span><span class="sxs-lookup"><span data-stu-id="51487-118">These packages are useful in the following scenarios:</span></span>

* <span data-ttu-id="51487-119">Exécuter JavaScript sur le serveur</span><span class="sxs-lookup"><span data-stu-id="51487-119">Run JavaScript on the server</span></span>
* <span data-ttu-id="51487-120">Utiliser une bibliothèque ou une infrastructure SPA</span><span class="sxs-lookup"><span data-stu-id="51487-120">Use a SPA framework or library</span></span>
* <span data-ttu-id="51487-121">Créer des ressources côté client avec WebPack</span><span class="sxs-lookup"><span data-stu-id="51487-121">Build client-side assets with Webpack</span></span>

<span data-ttu-id="51487-122">L’essentiel de cet article est consacré à l’utilisation du package SpaServices.</span><span class="sxs-lookup"><span data-stu-id="51487-122">Much of the focus in this article is placed on using the SpaServices package.</span></span>

## <a name="what-is-spaservices"></a><span data-ttu-id="51487-123">Qu’est-ce que SpaServices</span><span class="sxs-lookup"><span data-stu-id="51487-123">What is SpaServices</span></span>

<span data-ttu-id="51487-124">SpaServices a été créé pour positionner ASP.NET Core en tant que plateforme côté serveur préférée des développeurs pour la création de cas.</span><span class="sxs-lookup"><span data-stu-id="51487-124">SpaServices was created to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span> <span data-ttu-id="51487-125">SpaServices n’est pas requis pour développer des SPAs avec ASP.NET Core, et il ne verrouille pas les développeurs dans une infrastructure cliente particulière.</span><span class="sxs-lookup"><span data-stu-id="51487-125">SpaServices isn't required to develop SPAs with ASP.NET Core, and it doesn't lock developers into a particular client framework.</span></span>

<span data-ttu-id="51487-126">SpaServices fournit une infrastructure utile telle que :</span><span class="sxs-lookup"><span data-stu-id="51487-126">SpaServices provides useful infrastructure such as:</span></span>

* [<span data-ttu-id="51487-127">Prérendu côté serveur</span><span class="sxs-lookup"><span data-stu-id="51487-127">Server-side prerendering</span></span>](#server-side-prerendering)
* [<span data-ttu-id="51487-128">Middleware de développement WebPack</span><span class="sxs-lookup"><span data-stu-id="51487-128">Webpack Dev Middleware</span></span>](#webpack-dev-middleware)
* [<span data-ttu-id="51487-129">Remplacement du module réactif</span><span class="sxs-lookup"><span data-stu-id="51487-129">Hot Module Replacement</span></span>](#hot-module-replacement)
* [<span data-ttu-id="51487-130">Assistance du routage</span><span class="sxs-lookup"><span data-stu-id="51487-130">Routing helpers</span></span>](#routing-helpers)

<span data-ttu-id="51487-131">Collectivement, ces composants d’infrastructure améliorent à la fois le flux de travail de développement et l’expérience d’exécution.</span><span class="sxs-lookup"><span data-stu-id="51487-131">Collectively, these infrastructure components enhance both the development workflow and the runtime experience.</span></span> <span data-ttu-id="51487-132">Les composants peuvent être adoptés individuellement.</span><span class="sxs-lookup"><span data-stu-id="51487-132">The components can be adopted individually.</span></span>

## <a name="prerequisites-for-using-spaservices"></a><span data-ttu-id="51487-133">Conditions préalables à l’utilisation de SpaServices</span><span class="sxs-lookup"><span data-stu-id="51487-133">Prerequisites for using SpaServices</span></span>

<span data-ttu-id="51487-134">Pour utiliser SpaServices, installez les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="51487-134">To work with SpaServices, install the following:</span></span>

* <span data-ttu-id="51487-135">[Node.js](https://nodejs.org/) (version 6 ou ultérieure) avec NPM</span><span class="sxs-lookup"><span data-stu-id="51487-135">[Node.js](https://nodejs.org/) (version 6 or later) with npm</span></span>

  * <span data-ttu-id="51487-136">Pour vérifier que ces composants sont installés et sont disponibles, exécutez la commande suivante à partir de la ligne de commande :</span><span class="sxs-lookup"><span data-stu-id="51487-136">To verify these components are installed and can be found, run the following from the command line:</span></span>

    ```console
    node -v && npm -v
    ```

  * <span data-ttu-id="51487-137">En cas de déploiement sur un site Web Azure, aucune action n’est requise &mdash;Node.js est installé et disponible dans les environnements de serveur.</span><span class="sxs-lookup"><span data-stu-id="51487-137">If deploying to an Azure web site, no action is required&mdash;Node.js is installed and available in the server environments.</span></span>

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * <span data-ttu-id="51487-138">Sur Windows à l’aide de Visual Studio 2017, le kit de développement logiciel (SDK) est installé en sélectionnant la charge de travail **développement multiplateforme .net Core** .</span><span class="sxs-lookup"><span data-stu-id="51487-138">On Windows using Visual Studio 2017, the SDK is installed by selecting the **.NET Core cross-platform development** workload.</span></span>

* <span data-ttu-id="51487-139">Package NuGet [Microsoft. AspNetCore. SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/)</span><span class="sxs-lookup"><span data-stu-id="51487-139">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet package</span></span>

## <a name="server-side-prerendering"></a><span data-ttu-id="51487-140">Prérendu côté serveur</span><span class="sxs-lookup"><span data-stu-id="51487-140">Server-side prerendering</span></span>

<span data-ttu-id="51487-141">Une application universelle (également appelée isomorphes) est une application JavaScript qui peut s’exécuter à la fois sur le serveur et sur le client.</span><span class="sxs-lookup"><span data-stu-id="51487-141">A universal (also known as isomorphic) application is a JavaScript application capable of running both on the server and the client.</span></span> <span data-ttu-id="51487-142">Les infrastructures angulaires, de réréaction et d’autres infrastructures populaires fournissent une plateforme universelle pour ce style de développement d’applications.</span><span class="sxs-lookup"><span data-stu-id="51487-142">Angular, React, and other popular frameworks provide a universal platform for this application development style.</span></span> <span data-ttu-id="51487-143">L’idée consiste à restituer d’abord les composants d’infrastructure sur le serveur via Node.js, puis à déléguer l’exécution au client.</span><span class="sxs-lookup"><span data-stu-id="51487-143">The idea is to first render the framework components on the server via Node.js, and then delegate further execution to the client.</span></span>

<span data-ttu-id="51487-144">ASP.NET Core [balises](xref:mvc/views/tag-helpers/intro) fournies par SpaServices simplifient l’implémentation du prérendu côté serveur en appelant les fonctions JavaScript sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="51487-144">ASP.NET Core [Tag Helpers](xref:mvc/views/tag-helpers/intro) provided by SpaServices simplify the implementation of server-side prerendering by invoking the JavaScript functions on the server.</span></span>

### <a name="server-side-prerendering-prerequisites"></a><span data-ttu-id="51487-145">Conditions préalables pour le prérendu côté serveur</span><span class="sxs-lookup"><span data-stu-id="51487-145">Server-side prerendering prerequisites</span></span>

<span data-ttu-id="51487-146">Installer le package NPM [ASPNET-PreRender](https://www.npmjs.com/package/aspnet-prerendering) :</span><span class="sxs-lookup"><span data-stu-id="51487-146">Install the [aspnet-prerendering](https://www.npmjs.com/package/aspnet-prerendering) npm package:</span></span>

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a><span data-ttu-id="51487-147">Configuration du prérendu côté serveur</span><span class="sxs-lookup"><span data-stu-id="51487-147">Server-side prerendering configuration</span></span>

<span data-ttu-id="51487-148">Les tag helpers sont rendus détectables via l’inscription de l’espace de noms dans le fichier *_ViewImports. cshtml* du projet :</span><span class="sxs-lookup"><span data-stu-id="51487-148">The Tag Helpers are made discoverable via namespace registration in the project's *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

<span data-ttu-id="51487-149">Ces tag helpers éliminent les subtilités de la communication directe avec les API de bas niveau en tirant parti d’une syntaxe de type HTML à l’intérieur de la :::no-loc(Razor)::: vue :</span><span class="sxs-lookup"><span data-stu-id="51487-149">These Tag Helpers abstract away the intricacies of communicating directly with low-level APIs by leveraging an HTML-like syntax inside the :::no-loc(Razor)::: view:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a><span data-ttu-id="51487-150">ASP-prerende-tag Helper module</span><span class="sxs-lookup"><span data-stu-id="51487-150">asp-prerender-module Tag Helper</span></span>

<span data-ttu-id="51487-151">Le `asp-prerender-module` tag Helper, utilisé dans l’exemple de code précédent, exécute l’instruction *ClientApp/dist/main-server.js* sur le serveur via Node.js.</span><span class="sxs-lookup"><span data-stu-id="51487-151">The `asp-prerender-module` Tag Helper, used in the preceding code example, executes *ClientApp/dist/main-server.js* on the server via Node.js.</span></span> <span data-ttu-id="51487-152">Par souci de clarté, *main-server.js* fichier est un artefact de la tâche de Transpilation de machine à écrire en JavaScript dans le processus de génération [WebPack](https://webpack.github.io/) .</span><span class="sxs-lookup"><span data-stu-id="51487-152">For clarity's sake, *main-server.js* file is an artifact of the TypeScript-to-JavaScript transpilation task in the [Webpack](https://webpack.github.io/) build process.</span></span> <span data-ttu-id="51487-153">WebPack définit un alias de point d’entrée `main-server` ; et, la traversée du graphique de dépendance pour cet alias commence au niveau du fichier *ClientApp/Boot-Server. TS* :</span><span class="sxs-lookup"><span data-stu-id="51487-153">Webpack defines an entry point alias of `main-server`; and, traversal of the dependency graph for this alias begins at the *ClientApp/boot-server.ts* file:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

<span data-ttu-id="51487-154">Dans l’exemple angulaire suivant, le fichier *ClientApp/Boot-Server. TS* utilise la `createServerRenderer` fonction et le `RenderResult` type du `aspnet-prerendering` package NPM pour configurer le rendu du serveur via Node.js.</span><span class="sxs-lookup"><span data-stu-id="51487-154">In the following Angular example, the *ClientApp/boot-server.ts* file utilizes the `createServerRenderer` function and `RenderResult` type of the `aspnet-prerendering` npm package to configure server rendering via Node.js.</span></span> <span data-ttu-id="51487-155">Le balisage HTML destiné au rendu côté serveur est passé à un appel de fonction Resolve, qui est encapsulé dans un objet JavaScript fortement typé `Promise` .</span><span class="sxs-lookup"><span data-stu-id="51487-155">The HTML markup destined for server-side rendering is passed to a resolve function call, which is wrapped in a strongly-typed JavaScript `Promise` object.</span></span> <span data-ttu-id="51487-156">L' `Promise` importance de l’objet est qu’il fournit de manière asynchrone le balisage HTML à la page pour l’injection dans l’élément d’espace réservé du DOM.</span><span class="sxs-lookup"><span data-stu-id="51487-156">The `Promise` object's significance is that it asynchronously supplies the HTML markup to the page for injection in the DOM's placeholder element.</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a><span data-ttu-id="51487-157">ASP-prerende-tag Helper de données</span><span class="sxs-lookup"><span data-stu-id="51487-157">asp-prerender-data Tag Helper</span></span>

<span data-ttu-id="51487-158">Lorsqu' `asp-prerender-module` il est associé au tag Helper, le `asp-prerender-data` tag Helper peut être utilisé pour transmettre des informations contextuelles de la :::no-loc(Razor)::: vue au code JavaScript côté serveur.</span><span class="sxs-lookup"><span data-stu-id="51487-158">When coupled with the `asp-prerender-module` Tag Helper, the `asp-prerender-data` Tag Helper can be used to pass contextual information from the :::no-loc(Razor)::: view to the server-side JavaScript.</span></span> <span data-ttu-id="51487-159">Par exemple, le balisage suivant transmet les données utilisateur au `main-server` module :</span><span class="sxs-lookup"><span data-stu-id="51487-159">For example, the following markup passes user data to the `main-server` module:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

<span data-ttu-id="51487-160">L’argument received `UserName` est sérialisé à l’aide du sérialiseur JSON intégré et est stocké dans l' `params.data` objet.</span><span class="sxs-lookup"><span data-stu-id="51487-160">The received `UserName` argument is serialized using the built-in JSON serializer and is stored in the `params.data` object.</span></span> <span data-ttu-id="51487-161">Dans l’exemple angulaire suivant, les données sont utilisées pour construire un message de salutation personnalisé au sein d’un `h1` élément :</span><span class="sxs-lookup"><span data-stu-id="51487-161">In the following Angular example, the data is used to construct a personalized greeting within an `h1` element:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

<span data-ttu-id="51487-162">Les noms de propriétés passés dans tag helpers sont représentés par la notation **casse Pascal** .</span><span class="sxs-lookup"><span data-stu-id="51487-162">Property names passed in Tag Helpers are represented with **PascalCase** notation.</span></span> <span data-ttu-id="51487-163">Comparez-le à JavaScript, où les mêmes noms de propriétés sont représentés avec **la casse mixte** .</span><span class="sxs-lookup"><span data-stu-id="51487-163">Contrast that to JavaScript, where the same property names are represented with **camelCase** .</span></span> <span data-ttu-id="51487-164">La configuration de sérialisation JSON par défaut est responsable de cette différence.</span><span class="sxs-lookup"><span data-stu-id="51487-164">The default JSON serialization configuration is responsible for this difference.</span></span>

<span data-ttu-id="51487-165">Pour développer l’exemple de code précédent, les données peuvent être transmises du serveur à la vue en mettant en attente la `globals` propriété fournie à la `resolve` fonction :</span><span class="sxs-lookup"><span data-stu-id="51487-165">To expand upon the preceding code example, data can be passed from the server to the view by hydrating the `globals` property provided to the `resolve` function:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

<span data-ttu-id="51487-166">Le `postList` tableau défini à l’intérieur de l' `globals` objet est attaché à l’objet global du navigateur `window` .</span><span class="sxs-lookup"><span data-stu-id="51487-166">The `postList` array defined inside the `globals` object is attached to the browser's global `window` object.</span></span> <span data-ttu-id="51487-167">Cette variable de portée globale élimine les doublons d’effort, en particulier lorsqu’elle se rapporte au chargement des mêmes données une seule fois sur le serveur et sur le client.</span><span class="sxs-lookup"><span data-stu-id="51487-167">This variable hoisting to global scope eliminates duplication of effort, particularly as it pertains to loading the same data once on the server and again on the client.</span></span>

![variable globale postList jointe à l’objet Window](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a><span data-ttu-id="51487-169">Middleware de développement WebPack</span><span class="sxs-lookup"><span data-stu-id="51487-169">Webpack Dev Middleware</span></span>

<span data-ttu-id="51487-170">L’intergiciel (middleware) de développement [WebPack](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) introduit un flux de travail de développement rationalisé dans lequel WebPack génère des ressources à la demande.</span><span class="sxs-lookup"><span data-stu-id="51487-170">[Webpack Dev Middleware](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) introduces a streamlined development workflow whereby Webpack builds resources on demand.</span></span> <span data-ttu-id="51487-171">L’intergiciel (middleware) compile et fournit automatiquement des ressources côté client lorsqu’une page est rechargée dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="51487-171">The middleware automatically compiles and serves client-side resources when a page is reloaded in the browser.</span></span> <span data-ttu-id="51487-172">L’autre approche consiste à appeler manuellement WebPack via le script de build NPM du projet lorsqu’une dépendance tierce ou le code personnalisé change.</span><span class="sxs-lookup"><span data-stu-id="51487-172">The alternate approach is to manually invoke Webpack via the project's npm build script when a third-party dependency or the custom code changes.</span></span> <span data-ttu-id="51487-173">Dans l’exemple suivant, un script de génération NPM dans le fichier *package.js* est illustré ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="51487-173">An npm build script in the *package.json* file is shown in the following example:</span></span>

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a><span data-ttu-id="51487-174">Conditions préalables pour le middleware de développement WebPack</span><span class="sxs-lookup"><span data-stu-id="51487-174">Webpack Dev Middleware prerequisites</span></span>

<span data-ttu-id="51487-175">Installez le package [ASPNET-WebPack](https://www.npmjs.com/package/aspnet-webpack) NPM :</span><span class="sxs-lookup"><span data-stu-id="51487-175">Install the [aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm package:</span></span>

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a><span data-ttu-id="51487-176">Configuration de l’intergiciel (middleware) WebPack</span><span class="sxs-lookup"><span data-stu-id="51487-176">Webpack Dev Middleware configuration</span></span>

<span data-ttu-id="51487-177">L’intergiciel (middleware) de développement WebPack est inscrit dans le pipeline de requête HTTP via le code suivant dans la méthode du fichier *Startup.cs* `Configure` :</span><span class="sxs-lookup"><span data-stu-id="51487-177">Webpack Dev Middleware is registered into the HTTP request pipeline via the following code in the *Startup.cs* file's `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

<span data-ttu-id="51487-178">La `UseWebpackDevMiddleware` méthode d’extension doit être appelée avant l’inscription de l' [Hébergement de fichiers statiques](xref:fundamentals/static-files) via la `UseStaticFiles` méthode d’extension.</span><span class="sxs-lookup"><span data-stu-id="51487-178">The `UseWebpackDevMiddleware` extension method must be called before [registering static file hosting](xref:fundamentals/static-files) via the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="51487-179">Pour des raisons de sécurité, inscrivez l’intergiciel uniquement lorsque l’application s’exécute en mode de développement.</span><span class="sxs-lookup"><span data-stu-id="51487-179">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="51487-180">La propriété du fichier *webpack.config.js* `output.publicPath` indique à l’intergiciel (middleware) de surveiller les `dist` modifications apportées au dossier :</span><span class="sxs-lookup"><span data-stu-id="51487-180">The *webpack.config.js* file's `output.publicPath` property tells the middleware to watch the `dist` folder for changes:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a><span data-ttu-id="51487-181">Remplacement du module réactif</span><span class="sxs-lookup"><span data-stu-id="51487-181">Hot Module Replacement</span></span>

<span data-ttu-id="51487-182">Considérez la fonctionnalité de [remplacement de module à chaud](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) de WebPack comme une évolution de l’intergiciel (middleware) de [développement WebPack](#webpack-dev-middleware).</span><span class="sxs-lookup"><span data-stu-id="51487-182">Think of Webpack's [Hot Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) feature as an evolution of [Webpack Dev Middleware](#webpack-dev-middleware).</span></span> <span data-ttu-id="51487-183">HMR présente les mêmes avantages, mais il simplifie davantage le flux de travail de développement en mettant automatiquement à jour le contenu de la page après la compilation des modifications.</span><span class="sxs-lookup"><span data-stu-id="51487-183">HMR introduces all the same benefits, but it further streamlines the development workflow by automatically updating page content after compiling the changes.</span></span> <span data-ttu-id="51487-184">Ne confondez pas cela avec une actualisation du navigateur, ce qui interfère avec l’État en mémoire actuel et la session de débogage du SPA.</span><span class="sxs-lookup"><span data-stu-id="51487-184">Don't confuse this with a refresh of the browser, which would interfere with the current in-memory state and debugging session of the SPA.</span></span> <span data-ttu-id="51487-185">Il existe un lien direct entre le service WebPack dev middleware et le navigateur, ce qui signifie que les modifications sont envoyées au navigateur.</span><span class="sxs-lookup"><span data-stu-id="51487-185">There's a live link between the Webpack Dev Middleware service and the browser, which means changes are pushed to the browser.</span></span>

### <a name="hot-module-replacement-prerequisites"></a><span data-ttu-id="51487-186">Conditions préalables pour le remplacement des modules à chaud</span><span class="sxs-lookup"><span data-stu-id="51487-186">Hot Module Replacement prerequisites</span></span>

<span data-ttu-id="51487-187">Installez le package NPM [WebPack-Hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) :</span><span class="sxs-lookup"><span data-stu-id="51487-187">Install the [webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm package:</span></span>

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a><span data-ttu-id="51487-188">Configuration du remplacement des modules à chaud</span><span class="sxs-lookup"><span data-stu-id="51487-188">Hot Module Replacement configuration</span></span>

<span data-ttu-id="51487-189">Le composant HMR doit être inscrit dans le pipeline de requête HTTP de MVC dans la `Configure` méthode :</span><span class="sxs-lookup"><span data-stu-id="51487-189">The HMR component must be registered into MVC's HTTP request pipeline in the `Configure` method:</span></span>

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

<span data-ttu-id="51487-190">Comme c’était le cas avec l’intergiciel (middleware) de [développement WebPack](#webpack-dev-middleware), la `UseWebpackDevMiddleware` méthode d’extension doit être appelée avant la `UseStaticFiles` méthode d’extension.</span><span class="sxs-lookup"><span data-stu-id="51487-190">As was true with [Webpack Dev Middleware](#webpack-dev-middleware), the `UseWebpackDevMiddleware` extension method must be called before the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="51487-191">Pour des raisons de sécurité, inscrivez l’intergiciel uniquement lorsque l’application s’exécute en mode de développement.</span><span class="sxs-lookup"><span data-stu-id="51487-191">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="51487-192">Le fichier *webpack.config.js* doit définir un `plugins` tableau, même s’il est laissé vide :</span><span class="sxs-lookup"><span data-stu-id="51487-192">The *webpack.config.js* file must define a `plugins` array, even if it's left empty:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

<span data-ttu-id="51487-193">Après le chargement de l’application dans le navigateur, l’onglet Console des outils de développement fournit la confirmation de l’activation de HMR :</span><span class="sxs-lookup"><span data-stu-id="51487-193">After loading the app in the browser, the developer tools' Console tab provides confirmation of HMR activation:</span></span>

![Message connecté de remplacement de module chaud](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a><span data-ttu-id="51487-195">Assistance du routage</span><span class="sxs-lookup"><span data-stu-id="51487-195">Routing helpers</span></span>

<span data-ttu-id="51487-196">Dans la plupart des ASP.NET Core, le routage côté client est souvent souhaité en plus du routage côté serveur.</span><span class="sxs-lookup"><span data-stu-id="51487-196">In most ASP.NET Core-based SPAs, client-side routing is often desired in addition to server-side routing.</span></span> <span data-ttu-id="51487-197">Les systèmes de routage SPA et MVC peuvent fonctionner indépendamment sans interférence.</span><span class="sxs-lookup"><span data-stu-id="51487-197">The SPA and MVC routing systems can work independently without interference.</span></span> <span data-ttu-id="51487-198">Toutefois, il existe un cas limite qui pose des problèmes : identification des réponses HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="51487-198">There's, however, one edge case posing challenges: identifying 404 HTTP responses.</span></span>

<span data-ttu-id="51487-199">Considérez le scénario dans lequel un itinéraire sans extension de `/some/page` est utilisé.</span><span class="sxs-lookup"><span data-stu-id="51487-199">Consider the scenario in which an extensionless route of `/some/page` is used.</span></span> <span data-ttu-id="51487-200">Supposons que la demande ne corresponde pas à un itinéraire côté serveur, mais que son modèle correspond à un itinéraire côté client.</span><span class="sxs-lookup"><span data-stu-id="51487-200">Assume the request doesn't pattern-match a server-side route, but its pattern does match a client-side route.</span></span> <span data-ttu-id="51487-201">Considérons maintenant une demande entrante pour `/images/user-512.png` , qui s’attend généralement à trouver un fichier image sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="51487-201">Now consider an incoming request for `/images/user-512.png`, which generally expects to find an image file on the server.</span></span> <span data-ttu-id="51487-202">Si le chemin d’accès de la ressource demandé ne correspond à aucun itinéraire côté serveur ou fichier statique, il est peu probable que l’application côté client ne puisse le gérer. en général, le &mdash; code d’état HTTP 404 est attendu.</span><span class="sxs-lookup"><span data-stu-id="51487-202">If that requested resource path doesn't match any server-side route or static file, it's unlikely that the client-side application would handle it&mdash;generally returning a 404 HTTP status code is desired.</span></span>

### <a name="routing-helpers-prerequisites"></a><span data-ttu-id="51487-203">Conditions préalables pour le routage</span><span class="sxs-lookup"><span data-stu-id="51487-203">Routing helpers prerequisites</span></span>

<span data-ttu-id="51487-204">Installez le package NPM du routage côté client.</span><span class="sxs-lookup"><span data-stu-id="51487-204">Install the client-side routing npm package.</span></span> <span data-ttu-id="51487-205">À l’aide de l’angulaire comme exemple :</span><span class="sxs-lookup"><span data-stu-id="51487-205">Using Angular as an example:</span></span>

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a><span data-ttu-id="51487-206">Configuration des assistances pour le routage</span><span class="sxs-lookup"><span data-stu-id="51487-206">Routing helpers configuration</span></span>

<span data-ttu-id="51487-207">Une méthode d’extension nommée `MapSpaFallbackRoute` est utilisée dans la `Configure` méthode :</span><span class="sxs-lookup"><span data-stu-id="51487-207">An extension method named `MapSpaFallbackRoute` is used in the `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

<span data-ttu-id="51487-208">Les itinéraires sont évalués dans l’ordre dans lequel ils sont configurés.</span><span class="sxs-lookup"><span data-stu-id="51487-208">Routes are evaluated in the order in which they're configured.</span></span> <span data-ttu-id="51487-209">Par conséquent, l' `default` itinéraire dans l’exemple de code précédent est utilisé en premier pour les critères spéciaux.</span><span class="sxs-lookup"><span data-stu-id="51487-209">Consequently, the `default` route in the preceding code example is used first for pattern matching.</span></span>

## <a name="create-a-new-project"></a><span data-ttu-id="51487-210">Créer un projet</span><span class="sxs-lookup"><span data-stu-id="51487-210">Create a new project</span></span>

<span data-ttu-id="51487-211">Les services JavaScript fournissent des modèles d’application préconfigurés.</span><span class="sxs-lookup"><span data-stu-id="51487-211">JavaScript Services provide pre-configured application templates.</span></span> <span data-ttu-id="51487-212">SpaServices est utilisé dans ces modèles conjointement avec différents frameworks et bibliothèques, tels que angulaire, REACT et Redux.</span><span class="sxs-lookup"><span data-stu-id="51487-212">SpaServices is used in these templates in conjunction with different frameworks and libraries such as Angular, React, and Redux.</span></span>

<span data-ttu-id="51487-213">Ces modèles peuvent être installés par le biais de la CLI .NET Core en exécutant la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="51487-213">These templates can be installed via the .NET Core CLI by running the following command:</span></span>

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

<span data-ttu-id="51487-214">La liste des modèles SPA disponibles s’affiche :</span><span class="sxs-lookup"><span data-stu-id="51487-214">A list of available SPA templates is displayed:</span></span>

| <span data-ttu-id="51487-215">Modèles</span><span class="sxs-lookup"><span data-stu-id="51487-215">Templates</span></span>                                 | <span data-ttu-id="51487-216">Nom court</span><span class="sxs-lookup"><span data-stu-id="51487-216">Short Name</span></span> | <span data-ttu-id="51487-217">Langage</span><span class="sxs-lookup"><span data-stu-id="51487-217">Language</span></span> | <span data-ttu-id="51487-218">Balises</span><span class="sxs-lookup"><span data-stu-id="51487-218">Tags</span></span>        |
| ------------------------------------------| :--------: | :------: | :---------: |
| <span data-ttu-id="51487-219">ASP.NET Core MVC avec angle</span><span class="sxs-lookup"><span data-stu-id="51487-219">MVC ASP.NET Core with Angular</span></span>             | <span data-ttu-id="51487-220">angular</span><span class="sxs-lookup"><span data-stu-id="51487-220">angular</span></span>    | <span data-ttu-id="51487-221">[C#]</span><span class="sxs-lookup"><span data-stu-id="51487-221">[C#]</span></span>     | <span data-ttu-id="51487-222">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="51487-222">Web/MVC/SPA</span></span> |
| <span data-ttu-id="51487-223">ASP.NET Core MVC avec React.js</span><span class="sxs-lookup"><span data-stu-id="51487-223">MVC ASP.NET Core with React.js</span></span>            | <span data-ttu-id="51487-224">react</span><span class="sxs-lookup"><span data-stu-id="51487-224">react</span></span>      | <span data-ttu-id="51487-225">[C#]</span><span class="sxs-lookup"><span data-stu-id="51487-225">[C#]</span></span>     | <span data-ttu-id="51487-226">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="51487-226">Web/MVC/SPA</span></span> |
| <span data-ttu-id="51487-227">ASP.NET Core MVC avec React.js et Redux</span><span class="sxs-lookup"><span data-stu-id="51487-227">MVC ASP.NET Core with React.js and Redux</span></span>  | <span data-ttu-id="51487-228">reactredux</span><span class="sxs-lookup"><span data-stu-id="51487-228">reactredux</span></span> | <span data-ttu-id="51487-229">[C#]</span><span class="sxs-lookup"><span data-stu-id="51487-229">[C#]</span></span>     | <span data-ttu-id="51487-230">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="51487-230">Web/MVC/SPA</span></span> |

<span data-ttu-id="51487-231">Pour créer un nouveau projet à l’aide de l’un des modèles SPA, incluez le **nom abrégé** du modèle dans la commande [dotnet New](/dotnet/core/tools/dotnet-new) .</span><span class="sxs-lookup"><span data-stu-id="51487-231">To create a new project using one of the SPA templates, include the **Short Name** of the template in the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="51487-232">La commande suivante crée une application angulaire avec ASP.NET Core MVC configuré pour le côté serveur :</span><span class="sxs-lookup"><span data-stu-id="51487-232">The following command creates an Angular application with ASP.NET Core MVC configured for the server side:</span></span>

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a><span data-ttu-id="51487-233">Définir le mode de configuration du Runtime</span><span class="sxs-lookup"><span data-stu-id="51487-233">Set the runtime configuration mode</span></span>

<span data-ttu-id="51487-234">Il existe deux modes principaux de configuration du Runtime :</span><span class="sxs-lookup"><span data-stu-id="51487-234">Two primary runtime configuration modes exist:</span></span>

* <span data-ttu-id="51487-235">**Développement** :</span><span class="sxs-lookup"><span data-stu-id="51487-235">**Development** :</span></span>
  * <span data-ttu-id="51487-236">Comprend des mappages de sources pour faciliter le débogage.</span><span class="sxs-lookup"><span data-stu-id="51487-236">Includes source maps to ease debugging.</span></span>
  * <span data-ttu-id="51487-237">N’optimise pas le code côté client pour des performances optimales.</span><span class="sxs-lookup"><span data-stu-id="51487-237">Doesn't optimize the client-side code for performance.</span></span>
* <span data-ttu-id="51487-238">**Production** :</span><span class="sxs-lookup"><span data-stu-id="51487-238">**Production** :</span></span>
  * <span data-ttu-id="51487-239">Exclut les mappages de sources.</span><span class="sxs-lookup"><span data-stu-id="51487-239">Excludes source maps.</span></span>
  * <span data-ttu-id="51487-240">Optimise le code côté client via le regroupement et la minimisation.</span><span class="sxs-lookup"><span data-stu-id="51487-240">Optimizes the client-side code via bundling and minification.</span></span>

<span data-ttu-id="51487-241">ASP.NET Core utilise une variable d’environnement nommée `ASPNETCORE_ENVIRONMENT` pour stocker le mode de configuration.</span><span class="sxs-lookup"><span data-stu-id="51487-241">ASP.NET Core uses an environment variable named `ASPNETCORE_ENVIRONMENT` to store the configuration mode.</span></span> <span data-ttu-id="51487-242">Pour plus d’informations, consultez [définir l’environnement](xref:fundamentals/environments#set-the-environment).</span><span class="sxs-lookup"><span data-stu-id="51487-242">For more information, see [Set the environment](xref:fundamentals/environments#set-the-environment).</span></span>

### <a name="run-with-net-core-cli"></a><span data-ttu-id="51487-243">Exécuter avec CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="51487-243">Run with .NET Core CLI</span></span>

<span data-ttu-id="51487-244">Restaurez les packages NuGet et NPM requis en exécutant la commande suivante à la racine du projet :</span><span class="sxs-lookup"><span data-stu-id="51487-244">Restore the required NuGet and npm packages by running the following command at the project root:</span></span>

```dotnetcli
dotnet restore && npm i
```

<span data-ttu-id="51487-245">Générez et exécutez l’application :</span><span class="sxs-lookup"><span data-stu-id="51487-245">Build and run the application:</span></span>

```dotnetcli
dotnet run
```

<span data-ttu-id="51487-246">L’application démarre sur localhost en fonction du [mode de configuration](#set-the-runtime-configuration-mode)de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="51487-246">The application starts on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span> <span data-ttu-id="51487-247">La navigation vers `http://localhost:5000` dans le navigateur affiche la page d’accueil.</span><span class="sxs-lookup"><span data-stu-id="51487-247">Navigating to `http://localhost:5000` in the browser displays the landing page.</span></span>

### <a name="run-with-visual-studio-2017"></a><span data-ttu-id="51487-248">Exécuter avec Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="51487-248">Run with Visual Studio 2017</span></span>

<span data-ttu-id="51487-249">Ouvrez le fichier *. csproj* généré par la commande [dotnet New](/dotnet/core/tools/dotnet-new) .</span><span class="sxs-lookup"><span data-stu-id="51487-249">Open the *.csproj* file generated by the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="51487-250">Les packages NuGet et NPM requis sont restaurés automatiquement lors de l’ouverture du projet.</span><span class="sxs-lookup"><span data-stu-id="51487-250">The required NuGet and npm packages are restored automatically upon project open.</span></span> <span data-ttu-id="51487-251">Ce processus de restauration peut prendre quelques minutes, et l’application est prête à s’exécuter lorsqu’elle se termine.</span><span class="sxs-lookup"><span data-stu-id="51487-251">This restoration process may take up to a few minutes, and the application is ready to run when it completes.</span></span> <span data-ttu-id="51487-252">Cliquez sur le bouton vert exécuter ou appuyez sur `Ctrl + F5` , et le navigateur s’ouvre sur la page d’accueil de l’application.</span><span class="sxs-lookup"><span data-stu-id="51487-252">Click the green run button or press `Ctrl + F5`, and the browser opens to the application's landing page.</span></span> <span data-ttu-id="51487-253">L’application s’exécute sur localhost en fonction du [mode de configuration du runtime](#set-the-runtime-configuration-mode).</span><span class="sxs-lookup"><span data-stu-id="51487-253">The application runs on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span>

## <a name="test-the-app"></a><span data-ttu-id="51487-254">Tester l’application</span><span class="sxs-lookup"><span data-stu-id="51487-254">Test the app</span></span>

<span data-ttu-id="51487-255">Les modèles SpaServices sont préconfigurés pour exécuter des tests côté client à l’aide de [Karma](https://karma-runner.github.io/1.0/index.html) et [Jasmine](https://jasmine.github.io/).</span><span class="sxs-lookup"><span data-stu-id="51487-255">SpaServices templates are pre-configured to run client-side tests using [Karma](https://karma-runner.github.io/1.0/index.html) and [Jasmine](https://jasmine.github.io/).</span></span> <span data-ttu-id="51487-256">Jasmine est un Framework de tests unitaires populaire pour JavaScript, tandis que Karma est un testeur de test pour ces tests.</span><span class="sxs-lookup"><span data-stu-id="51487-256">Jasmine is a popular unit testing framework for JavaScript, whereas Karma is a test runner for those tests.</span></span> <span data-ttu-id="51487-257">Karma est configuré pour fonctionner avec l' [intergiciel (middleware) WebPack dev](#webpack-dev-middleware) , de sorte que le développeur n’est pas obligé d’arrêter et d’exécuter le test à chaque fois que des modifications sont apportées.</span><span class="sxs-lookup"><span data-stu-id="51487-257">Karma is configured to work with the [Webpack Dev Middleware](#webpack-dev-middleware) such that the developer isn't required to stop and run the test every time changes are made.</span></span> <span data-ttu-id="51487-258">S’il s’agit du code qui s’exécute sur le cas de test ou le cas de test lui-même, le test s’exécute automatiquement.</span><span class="sxs-lookup"><span data-stu-id="51487-258">Whether it's the code running against the test case or the test case itself, the test runs automatically.</span></span>

<span data-ttu-id="51487-259">À l’aide de l’application angulaire comme exemple, deux cas de test Jasmine sont déjà fournis pour le `CounterComponent` dans le fichier *Counter. composant. spec. TS* :</span><span class="sxs-lookup"><span data-stu-id="51487-259">Using the Angular application as an example, two Jasmine test cases are already provided for the `CounterComponent` in the *counter.component.spec.ts* file:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

<span data-ttu-id="51487-260">Ouvrez l’invite de commandes dans le répertoire *ClientApp* .</span><span class="sxs-lookup"><span data-stu-id="51487-260">Open the command prompt in the *ClientApp* directory.</span></span> <span data-ttu-id="51487-261">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="51487-261">Run the following command:</span></span>

```console
npm test
```

<span data-ttu-id="51487-262">Le script lance Karma Test Runner, qui lit les paramètres définis dans le fichier *karma.conf.js* .</span><span class="sxs-lookup"><span data-stu-id="51487-262">The script launches the Karma test runner, which reads the settings defined in the *karma.conf.js* file.</span></span> <span data-ttu-id="51487-263">Parmi d’autres paramètres, le *karma.conf.js* identifie les fichiers de test à exécuter via son `files` tableau :</span><span class="sxs-lookup"><span data-stu-id="51487-263">Among other settings, the *karma.conf.js* identifies the test files to be executed via its `files` array:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a><span data-ttu-id="51487-264">Publier l’application</span><span class="sxs-lookup"><span data-stu-id="51487-264">Publish the app</span></span>

<span data-ttu-id="51487-265">Pour plus d’informations sur la publication sur Azure, consultez [ce numéro GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/12474) .</span><span class="sxs-lookup"><span data-stu-id="51487-265">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/12474) for more information on publishing to Azure.</span></span>

<span data-ttu-id="51487-266">La combinaison des ressources côté client générées et des artefacts de ASP.NET Core publiés dans un package prêt à déployer peut s’avérer fastidieuse.</span><span class="sxs-lookup"><span data-stu-id="51487-266">Combining the generated client-side assets and the published ASP.NET Core artifacts into a ready-to-deploy package can be cumbersome.</span></span> <span data-ttu-id="51487-267">Heureusement, SpaServices orchestre la totalité du processus de publication avec une cible MSBuild personnalisée nommée `RunWebpack` :</span><span class="sxs-lookup"><span data-stu-id="51487-267">Thankfully, SpaServices orchestrates that entire publication process with a custom MSBuild target named `RunWebpack`:</span></span>

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

<span data-ttu-id="51487-268">La cible MSBuild a les responsabilités suivantes :</span><span class="sxs-lookup"><span data-stu-id="51487-268">The MSBuild target has the following responsibilities:</span></span>

1. <span data-ttu-id="51487-269">Restaurez les packages NPM.</span><span class="sxs-lookup"><span data-stu-id="51487-269">Restore the npm packages.</span></span>
1. <span data-ttu-id="51487-270">Créer une build de niveau production des ressources tierces côté client.</span><span class="sxs-lookup"><span data-stu-id="51487-270">Create a production-grade build of the third-party, client-side assets.</span></span>
1. <span data-ttu-id="51487-271">Créez une build de niveau production des ressources client personnalisées.</span><span class="sxs-lookup"><span data-stu-id="51487-271">Create a production-grade build of the custom client-side assets.</span></span>
1. <span data-ttu-id="51487-272">Copiez les ressources générées par WebPack dans le dossier de publication.</span><span class="sxs-lookup"><span data-stu-id="51487-272">Copy the Webpack-generated assets to the publish folder.</span></span>

<span data-ttu-id="51487-273">La cible MSBuild est appelée lors de l’exécution de :</span><span class="sxs-lookup"><span data-stu-id="51487-273">The MSBuild target is invoked when running:</span></span>

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a><span data-ttu-id="51487-274">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="51487-274">Additional resources</span></span>

* [<span data-ttu-id="51487-275">Documents angulaires</span><span class="sxs-lookup"><span data-stu-id="51487-275">Angular Docs</span></span>](https://angular.io/docs)
