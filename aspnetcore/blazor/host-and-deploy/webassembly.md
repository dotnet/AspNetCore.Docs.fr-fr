---
title: Héberger et déployer des ASP.NET Core Blazor WebAssembly
author: guardrex
description: Découvrez comment héberger et déployer une Blazor application à l’aide de ASP.net Core, de réseaux de distribution de contenu (CDN), de serveurs de fichiers et de pages github.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/12/2021
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
uid: blazor/host-and-deploy/webassembly
ms.openlocfilehash: 77e4efe0ac2e87458558dabc78d47099b5698edc
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252446"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="cc6ad-103">Héberger et déployer des ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="cc6ad-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="cc6ad-104">Par [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams)et [safia Abdalla](https://safia.rocks)</span><span class="sxs-lookup"><span data-stu-id="cc6ad-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="cc6ad-105">Avec le [ Blazor WebAssembly modèle d’hébergement](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="cc6ad-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="cc6ad-106">L' Blazor application, ses dépendances et le Runtime .net sont téléchargés dans le navigateur en parallèle.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="cc6ad-107">L’application est exécutée directement sur le thread d’interface utilisateur du navigateur.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="cc6ad-108">Les stratégies de déploiement suivantes sont prises en charge :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="cc6ad-109">L' Blazor application est traitée par une application ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="cc6ad-110">Cette stratégie est abordée dans la section [Déploiement hébergé avec ASP.NET Core](#hosted-deployment-with-aspnet-core).</span><span class="sxs-lookup"><span data-stu-id="cc6ad-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="cc6ad-111">L' Blazor application est placée sur un service ou un serveur Web d’hébergement statique, où .net n’est pas utilisé pour traiter l' Blazor application.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="cc6ad-112">Cette stratégie est traitée dans la section [Déploiement autonome](#standalone-deployment) , qui comprend des informations sur l’hébergement d’une Blazor WebAssembly application en tant que sous-application IIS.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="compression"></a><span data-ttu-id="cc6ad-113">Compression</span><span class="sxs-lookup"><span data-stu-id="cc6ad-113">Compression</span></span>

<span data-ttu-id="cc6ad-114">Quand une Blazor WebAssembly application est publiée, la sortie est compressée de manière statique lors de la publication afin de réduire la taille de l’application et de supprimer la surcharge liée à la compression du Runtime.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-114">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="cc6ad-115">Les algorithmes de compression suivants sont utilisés :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-115">The following compression algorithms are used:</span></span>

* <span data-ttu-id="cc6ad-116">[Brotli](https://tools.ietf.org/html/rfc7932) (niveau le plus élevé)</span><span class="sxs-lookup"><span data-stu-id="cc6ad-116">[Brotli](https://tools.ietf.org/html/rfc7932) (highest level)</span></span>
* [<span data-ttu-id="cc6ad-117">Gzip</span><span class="sxs-lookup"><span data-stu-id="cc6ad-117">Gzip</span></span>](https://tools.ietf.org/html/rfc1952)

<span data-ttu-id="cc6ad-118">Blazor s’appuie sur l’hôte pour servir les fichiers compressés appropriés.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-118">Blazor relies on the host to the serve the appropriate compressed files.</span></span> <span data-ttu-id="cc6ad-119">Lors de l’utilisation d’un projet hébergé ASP.NET Core, le projet hôte peut effectuer la négociation de contenu et traiter les fichiers compressés statiquement.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-119">When using an ASP.NET Core hosted project, the host project is capable of performing content negotiation and serving the statically-compressed files.</span></span> <span data-ttu-id="cc6ad-120">Lors de l’hébergement d’une Blazor WebAssembly application autonome, un travail supplémentaire peut être nécessaire pour s’assurer que les fichiers compressés statiquement sont pris en charge :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-120">When hosting a Blazor WebAssembly standalone app, additional work might be required to ensure that statically-compressed files are served:</span></span>

* <span data-ttu-id="cc6ad-121">Pour `web.config` la configuration de la compression IIS, consultez la section [IIS : Brotli et compression gzip](#brotli-and-gzip-compression) .</span><span class="sxs-lookup"><span data-stu-id="cc6ad-121">For IIS `web.config` compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span> 
* <span data-ttu-id="cc6ad-122">Lors de l’hébergement sur des solutions d’hébergement statiques qui ne prennent pas en charge la négociation de contenu de fichier compressée statiquement, telles que les pages GitHub, envisagez de configurer l’application pour extraire et décoder les fichiers compressés Brotli :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-122">When hosting on static hosting solutions that don't support statically-compressed file content negotiation, such as GitHub Pages, consider configuring the app to fetch and decode Brotli compressed files:</span></span>

  * <span data-ttu-id="cc6ad-123">Obtenez le décodeur Brotli JavaScript à partir du [référentiel GitHub Google/Brotli](https://github.com/google/brotli).</span><span class="sxs-lookup"><span data-stu-id="cc6ad-123">Obtain the JavaScript Brotli decoder from the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="cc6ad-124">À compter de septembre 2020, le fichier de décodeur est nommé `decode.js` et se trouve dans le [ `js` dossier](https://github.com/google/brotli/tree/master/js)du référentiel.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-124">As of September 2020, the decoder file is named `decode.js` and found in the repository's [`js` folder](https://github.com/google/brotli/tree/master/js).</span></span>
  
    > [!NOTE]
    > <span data-ttu-id="cc6ad-125">Une régression est présente dans la version minimisés du `decode.js` script ( `decode.min.js` ) dans le [référentiel GitHub Google/brotli](https://github.com/google/brotli).</span><span class="sxs-lookup"><span data-stu-id="cc6ad-125">A regression is present in the minified version of the `decode.js` script (`decode.min.js`) in the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="cc6ad-126">Vous pouvez soit réduire le script par vous-même, soit utiliser le [package NPM](https://www.npmjs.com/package/brotli) jusqu’à la fenêtre de problème [. BrotliDecode n’est pas défini dans decode.min.js (google/brotli #844)](https://github.com/google/brotli/issues/844) est résolu.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-126">Either minify the script on your own or use the [npm package](https://www.npmjs.com/package/brotli) until the issue [Window.BrotliDecode is not set in decode.min.js (google/brotli #844)](https://github.com/google/brotli/issues/844) is resolved.</span></span> <span data-ttu-id="cc6ad-127">L’exemple de code dans cette section utilise la version **unminified** du script.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-127">The example code in this section uses the **unminified** version of the script.</span></span>

  * <span data-ttu-id="cc6ad-128">Mettez à jour l’application pour utiliser le décodeur.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-128">Update the app to use the decoder.</span></span> <span data-ttu-id="cc6ad-129">Remplacez le balisage dans la `<body>` balise de fermeture par `wwwroot/index.html` ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-129">Change the markup inside the closing `<body>` tag in `wwwroot/index.html` to the following:</span></span>
  
    ```html
    <script src="decode.js"></script>
    <script src="_framework/blazor.webassembly.js" autostart="false"></script>
    <script>
      Blazor.start({
        loadBootResource: function (type, name, defaultUri, integrity) {
          if (type !== 'dotnetjs' && location.hostname !== 'localhost') {
            return (async function () {
              const response = await fetch(defaultUri + '.br', { cache: 'no-cache' });
              if (!response.ok) {
                throw new Error(response.statusText);
              }
              const originalResponseBuffer = await response.arrayBuffer();
              const originalResponseArray = new Int8Array(originalResponseBuffer);
              const decompressedResponseArray = BrotliDecode(originalResponseArray);
              const contentType = type === 
                'dotnetwasm' ? 'application/wasm' : 'application/octet-stream';
              return new Response(decompressedResponseArray, 
                { headers: { 'content-type': contentType } });
            })();
          }
        }
      });
    </script>
    ```
 
<span data-ttu-id="cc6ad-130">Pour désactiver la compression, ajoutez la `BlazorEnableCompression` propriété MSBuild au fichier projet de l’application et définissez la valeur sur `false` :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-130">To disable compression, add the `BlazorEnableCompression` MSBuild property to the app's project file and set the value to `false`:</span></span>

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

<span data-ttu-id="cc6ad-131">La `BlazorEnableCompression` propriété peut être passée à la [`dotnet publish`](/dotnet/core/tools/dotnet-publish) commande avec la syntaxe suivante dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-131">The `BlazorEnableCompression` property can be passed to the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the following syntax in a command shell:</span></span>

```dotnetcli
dotnet publish -p:BlazorEnableCompression=false
```

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="cc6ad-132">Réécriture d’URL pour un routage correct</span><span class="sxs-lookup"><span data-stu-id="cc6ad-132">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="cc6ad-133">Le routage des requêtes pour les composants de page dans une Blazor WebAssembly application n’est pas aussi simple que les demandes de routage dans une Blazor Server application hébergée.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-133">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="cc6ad-134">Prenons l’exemple d’une Blazor WebAssembly application avec deux composants :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-134">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="cc6ad-135">`Main.razor`: Charge à la racine de l’application et contient un lien vers le `About` composant ( `href="About"` ).</span><span class="sxs-lookup"><span data-stu-id="cc6ad-135">`Main.razor`: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="cc6ad-136">`About.razor`: `About` composant.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-136">`About.razor`: `About` component.</span></span>

<span data-ttu-id="cc6ad-137">Quand le document par défaut de l’application est demandé à l’aide de la barre d’adresses du navigateur (par exemple, `https://www.contoso.com/`) :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-137">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="cc6ad-138">Le navigateur effectue une requête.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-138">The browser makes a request.</span></span>
1. <span data-ttu-id="cc6ad-139">La page par défaut est retournée, qui est généralement `index.html` .</span><span class="sxs-lookup"><span data-stu-id="cc6ad-139">The default page is returned, which is usually `index.html`.</span></span>
1. <span data-ttu-id="cc6ad-140">`index.html` amorce l’application.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-140">`index.html` bootstraps the app.</span></span>
1. <span data-ttu-id="cc6ad-141">Blazorle routeur est chargé et le Razor `Main` composant est rendu.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-141">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="cc6ad-142">Dans la page principale, le fait de sélectionner le lien vers le `About` composant fonctionne sur le client car le Blazor routeur empêche le navigateur d’effectuer une requête sur Internet pour `www.contoso.com` `About` et sert le `About` composant rendu lui-même.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-142">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="cc6ad-143">Toutes les demandes de points de terminaison internes *au sein de l' Blazor WebAssembly application* fonctionnent de la même façon : les demandes ne déclenchent pas de requêtes basées sur un navigateur vers des ressources hébergées sur le serveur sur Internet.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-143">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="cc6ad-144">Le routeur gère les requêtes en interne.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-144">The router handles the requests internally.</span></span>

<span data-ttu-id="cc6ad-145">Si une requête pour `www.contoso.com/About` est effectuée à l’aide de la barre d’adresses du navigateur, elle échoue.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-145">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="cc6ad-146">Comme cette ressource n’existe pas sur l’hôte Internet de l’application, une réponse *404 – Non trouvé* est retournée.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-146">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="cc6ad-147">Étant donné que les navigateurs effectuent des demandes aux hôtes basés sur Internet pour les pages côté client, les serveurs Web et les services d’hébergement doivent réécrire toutes les demandes de ressources qui ne sont pas physiquement sur le serveur sur la `index.html` page.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-147">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the `index.html` page.</span></span> <span data-ttu-id="cc6ad-148">Lorsque `index.html` est retourné, le routeur de l’application Blazor prend le relais et répond avec la ressource correcte.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-148">When `index.html` is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="cc6ad-149">Lors du déploiement sur un serveur IIS, vous pouvez utiliser le module de réécriture d’URL avec le fichier publié de l’application `web.config` .</span><span class="sxs-lookup"><span data-stu-id="cc6ad-149">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published `web.config` file.</span></span> <span data-ttu-id="cc6ad-150">Pour plus d’informations, consultez la section [IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="cc6ad-150">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="cc6ad-151">Déploiement hébergé avec ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cc6ad-151">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="cc6ad-152">Un *déploiement hébergé* sert l' Blazor WebAssembly application aux navigateurs à partir d’une [application ASP.net Core](xref:index) qui s’exécute sur un serveur Web.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-152">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="cc6ad-153">L’application cliente Blazor WebAssembly est publiée dans le `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` dossier de l’application serveur, ainsi que les autres ressources Web statiques de l’application serveur.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-153">The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="cc6ad-154">Les deux applications sont déployées ensemble.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-154">The two apps are deployed together.</span></span> <span data-ttu-id="cc6ad-155">Un serveur web capable d’héberger une application ASP.NET Core est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-155">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="cc6ad-156">Pour un déploiement hébergé, Visual Studio comprend le modèle de projet d' **Blazor WebAssembly application** ( `blazorwasm` modèle lors de l’utilisation de la [`dotnet new`](/dotnet/core/tools/dotnet-new) commande) avec l' **`Hosted`** option sélectionnée (lors de l' `-ho|--hosted` utilisation de la `dotnet new` commande).</span><span class="sxs-lookup"><span data-stu-id="cc6ad-156">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command) with the **`Hosted`** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="cc6ad-157">Pour plus d’informations sur l’hébergement et le déploiement d’applications ASP.NET Core, consultez <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-157">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="cc6ad-158">Pour plus d’informations concernant le déploiement sur Azure App Service, consultez <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-158">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="hosted-deployment-with-multiple-no-locblazor-webassembly-apps"></a><span data-ttu-id="cc6ad-159">Déploiement hébergé avec plusieurs Blazor WebAssembly applications</span><span class="sxs-lookup"><span data-stu-id="cc6ad-159">Hosted deployment with multiple Blazor WebAssembly apps</span></span>

### <a name="app-configuration"></a><span data-ttu-id="cc6ad-160">la configuration d’une application ;</span><span class="sxs-lookup"><span data-stu-id="cc6ad-160">App configuration</span></span>

<span data-ttu-id="cc6ad-161">Les Blazor solutions hébergées peuvent servir plusieurs Blazor WebAssembly applications.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-161">Hosted Blazor solutions can serve multiple Blazor WebAssembly apps.</span></span>

> [!NOTE]
> <span data-ttu-id="cc6ad-162">L’exemple de cette section fait référence à l’utilisation d’une *solution* Visual Studio, mais l’utilisation de Visual Studio et d’une solution Visual Studio n’est pas nécessaire pour que plusieurs applications clientes fonctionnent dans un scénario d’applications hébergées Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="cc6ad-162">The example in this section references the use of a Visual Studio *solution*, but the use of Visual Studio and a Visual Studio solution isn't required for multiple client apps to work in a hosted Blazor WebAssembly apps scenario.</span></span> <span data-ttu-id="cc6ad-163">Si vous n’utilisez pas Visual Studio, ignorez le `{SOLUTION NAME}.sln` fichier et tous les autres fichiers créés pour Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-163">If you aren't using Visual Studio, ignore the `{SOLUTION NAME}.sln` file and any other files created for Visual Studio.</span></span>

<span data-ttu-id="cc6ad-164">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-164">In the following example:</span></span>

* <span data-ttu-id="cc6ad-165">L’application cliente initiale (première) est le projet client par défaut d’une solution créée à partir du Blazor WebAssembly modèle de projet.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-165">The initial (first) client app is the default client project of a solution created from the Blazor WebAssembly project template.</span></span> <span data-ttu-id="cc6ad-166">La première application cliente est accessible dans un navigateur à partir de l’URL `/FirstApp` sur le port 5001 ou avec un hôte de `firstapp.com` .</span><span class="sxs-lookup"><span data-stu-id="cc6ad-166">The first client app is accessible in a browser from the URL `/FirstApp` on either port 5001 or with a host of `firstapp.com`.</span></span>
* <span data-ttu-id="cc6ad-167">Une deuxième application cliente est ajoutée à la solution, `SecondBlazorApp.Client` .</span><span class="sxs-lookup"><span data-stu-id="cc6ad-167">A second client app is added to the solution, `SecondBlazorApp.Client`.</span></span> <span data-ttu-id="cc6ad-168">La deuxième application cliente est accessible dans un navigateur à partir de l’URL `/SecondApp` sur le port 5002 ou avec un hôte de `secondapp.com` .</span><span class="sxs-lookup"><span data-stu-id="cc6ad-168">The second client app is accessible in a browser from the the URL `/SecondApp` on either port 5002 or with a host of `secondapp.com`.</span></span>

<span data-ttu-id="cc6ad-169">Utilisez une solution hébergée existante Blazor ou créez une nouvelle solution à partir du Blazor modèle de projet hébergé :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-169">Use an existing hosted Blazor solution or create a new solution from the Blazor Hosted project template:</span></span>

* <span data-ttu-id="cc6ad-170">Dans le fichier projet de l’application cliente, ajoutez une `<StaticWebAssetBasePath>` propriété à `<PropertyGroup>` avec une valeur `FirstApp` pour définir le chemin d’accès de base pour les ressources statiques du projet :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-170">In the client app's project file, add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `FirstApp` to set the base path for the project's static assets:</span></span>

  ```xml
  <PropertyGroup>
    ...
    <StaticWebAssetBasePath>FirstApp</StaticWebAssetBasePath>
  </PropertyGroup>
  ```

* <span data-ttu-id="cc6ad-171">Ajoutez une deuxième application cliente à la solution :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-171">Add a second client app to the solution:</span></span>

  * <span data-ttu-id="cc6ad-172">Ajoutez un dossier nommé `SecondClient` au dossier de la solution.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-172">Add a folder named `SecondClient` to the solution's folder.</span></span> <span data-ttu-id="cc6ad-173">Le dossier de solution créé à partir du modèle de projet contient le fichier solution et les dossiers suivants après l' `SecondClient` Ajout du dossier :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-173">The solution folder created from the project template contains the following solution file and folders after the `SecondClient` folder is added:</span></span>
  
    * <span data-ttu-id="cc6ad-174">`Client` Répertoire</span><span class="sxs-lookup"><span data-stu-id="cc6ad-174">`Client` (folder)</span></span>
    * <span data-ttu-id="cc6ad-175">`SecondClient` Répertoire</span><span class="sxs-lookup"><span data-stu-id="cc6ad-175">`SecondClient` (folder)</span></span>
    * <span data-ttu-id="cc6ad-176">`Server` Répertoire</span><span class="sxs-lookup"><span data-stu-id="cc6ad-176">`Server` (folder)</span></span>
    * <span data-ttu-id="cc6ad-177">`Shared` Répertoire</span><span class="sxs-lookup"><span data-stu-id="cc6ad-177">`Shared` (folder)</span></span>
    * <span data-ttu-id="cc6ad-178">`{SOLUTION NAME}.sln` txt</span><span class="sxs-lookup"><span data-stu-id="cc6ad-178">`{SOLUTION NAME}.sln` (file)</span></span>
    
    <span data-ttu-id="cc6ad-179">L’espace réservé `{SOLUTION NAME}` est le nom de la solution.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-179">The placeholder `{SOLUTION NAME}` is the solution's name.</span></span>

  * <span data-ttu-id="cc6ad-180">Créez une Blazor WebAssembly application nommée `SecondBlazorApp.Client` dans le `SecondClient` dossier à partir du Blazor WebAssembly modèle de projet.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-180">Create a Blazor WebAssembly app named `SecondBlazorApp.Client` in the `SecondClient` folder from the Blazor WebAssembly project template.</span></span>

  * <span data-ttu-id="cc6ad-181">Dans le `SecondBlazorApp.Client` fichier projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-181">In the `SecondBlazorApp.Client` app's project file:</span></span>

    * <span data-ttu-id="cc6ad-182">Ajoutez une `<StaticWebAssetBasePath>` propriété à `<PropertyGroup>` avec la valeur `SecondApp` :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-182">Add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `SecondApp`:</span></span>

      ```xml
      <PropertyGroup>
        ...
        <StaticWebAssetBasePath>SecondApp</StaticWebAssetBasePath>
      </PropertyGroup>
      ```

    * <span data-ttu-id="cc6ad-183">Ajoutez une référence de projet au `Shared` projet :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-183">Add a project reference to the `Shared` project:</span></span>

      ```xml
      <ItemGroup>
        <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
      </ItemGroup>
      ```

      <span data-ttu-id="cc6ad-184">L’espace réservé `{SOLUTION NAME}` est le nom de la solution.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-184">The placeholder `{SOLUTION NAME}` is the solution's name.</span></span>

* <span data-ttu-id="cc6ad-185">Dans le fichier projet de l’application serveur, créez une référence de projet pour l' `SecondBlazorApp.Client` application cliente ajoutée :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-185">In the server app's project file, create a project reference for the added `SecondBlazorApp.Client` client app:</span></span>

  ```xml
  <ItemGroup>
    <ProjectReference Include="..\Client\{SOLUTION NAME}.Client.csproj" />
    <ProjectReference Include="..\SecondClient\SecondBlazorApp.Client.csproj" />
    <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
  </ItemGroup>
  ```
  
  <span data-ttu-id="cc6ad-186">L’espace réservé `{SOLUTION NAME}` est le nom de la solution.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-186">The placeholder `{SOLUTION NAME}` is the solution's name.</span></span>

* <span data-ttu-id="cc6ad-187">Dans le fichier de l’application serveur `Properties/launchSettings.json` , configurez le `applicationUrl` du profil Kestrel ( `{SOLUTION NAME}.Server` ) pour accéder aux applications clientes aux ports 5001 et 5002 :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-187">In the server app's `Properties/launchSettings.json` file, configure the `applicationUrl` of the Kestrel profile (`{SOLUTION NAME}.Server`) to access the client apps at ports 5001 and 5002:</span></span>

  ```json
  "applicationUrl": "https://localhost:5001;https://localhost:5002",
  ```

* <span data-ttu-id="cc6ad-188">Dans la méthode de l’application serveur `Startup.Configure` ( `Startup.cs` ), supprimez les lignes suivantes, qui apparaissent après l’appel à <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-188">In the server app's `Startup.Configure` method (`Startup.cs`), remove the following lines, which appear after the call to <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>:</span></span>

  ```csharp
  app.UseBlazorFrameworkFiles();
  app.UseStaticFiles();

  app.UseRouting();

  app.UseEndpoints(endpoints =>
  {
      endpoints.MapRazorPages();
      endpoints.MapControllers();
      endpoints.MapFallbackToFile("index.html");
  });
  ```

  <span data-ttu-id="cc6ad-189">Ajoutez un intergiciel (middleware) qui mappe les demandes aux applications clientes.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-189">Add middleware that maps requests to the client apps.</span></span> <span data-ttu-id="cc6ad-190">L’exemple suivant configure l’intergiciel (middleware) à exécuter lorsque :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-190">The following example configures the middleware to run when:</span></span>

  * <span data-ttu-id="cc6ad-191">Le port de la demande est soit 5001 pour l’application cliente d’origine, soit 5002 pour l’application cliente ajoutée.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-191">The request port is either 5001 for the original client app or 5002 for the added client app.</span></span>
  * <span data-ttu-id="cc6ad-192">L’hôte de la requête est soit `firstapp.com` pour l’application cliente d’origine, soit `secondapp.com` pour l’application cliente ajoutée.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-192">The request host is either `firstapp.com` for the original client app or `secondapp.com` for the added client app.</span></span>

    > [!NOTE]
    > <span data-ttu-id="cc6ad-193">L’exemple présenté dans cette section requiert une configuration supplémentaire pour :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-193">The example shown in this section requires additional configuration for:</span></span>
    >
    > * <span data-ttu-id="cc6ad-194">L’accès aux applications des exemples de domaines hôtes, `firstapp.com` et `secondapp.com` .</span><span class="sxs-lookup"><span data-stu-id="cc6ad-194">Accessing the apps at the example host domains, `firstapp.com` and `secondapp.com`.</span></span>
    > * <span data-ttu-id="cc6ad-195">Certificats pour les applications clientes afin d’activer la sécurité TLS (HTTPs).</span><span class="sxs-lookup"><span data-stu-id="cc6ad-195">Certificates for the client apps to enable TLS security (HTTPS).</span></span>
    >
    > <span data-ttu-id="cc6ad-196">La configuration requise dépasse le cadre de cet article et dépend de la façon dont la solution est hébergée.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-196">The required configuration is beyond the scope of this article and depends on how the solution is hosted.</span></span> <span data-ttu-id="cc6ad-197">Pour plus d’informations, consultez les [Articles Host et deploy](xref:host-and-deploy/index).</span><span class="sxs-lookup"><span data-stu-id="cc6ad-197">For more information see the [Host and deploy articles](xref:host-and-deploy/index).</span></span>

  <span data-ttu-id="cc6ad-198">Placez le code suivant dans lequel les lignes ont été supprimées précédemment :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-198">Place the following code where the lines were removed earlier:</span></span>

  ```csharp
  app.MapWhen(ctx => ctx.Request.Host.Port == 5001 || 
      ctx.Request.Host.Equals("firstapp.com"), first =>
  {
      first.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/FirstApp" + ctx.Request.Path;
          return nxt();
      });

      first.UseBlazorFrameworkFiles("/FirstApp");
      first.UseStaticFiles();
      first.UseStaticFiles("/FirstApp");
      first.UseRouting();

      first.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.MapFallbackToFile("/FirstApp/{*path:nonfile}", 
              "FirstApp/index.html");
      });
  });
  
  app.MapWhen(ctx => ctx.Request.Host.Port == 5002 || 
      ctx.Request.Host.Equals("secondapp.com"), second =>
  {
      second.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/SecondApp" + ctx.Request.Path;
          return nxt();
      });

      second.UseBlazorFrameworkFiles("/SecondApp");
      second.UseStaticFiles();
      second.UseStaticFiles("/SecondApp");
      second.UseRouting();

      second.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.MapFallbackToFile("/SecondApp/{*path:nonfile}", 
              "SecondApp/index.html");
      });
  });
  ```

* <span data-ttu-id="cc6ad-199">Dans le contrôleur de prévisions météorologiques de l’application serveur ( `Controllers/WeatherForecastController.cs` ), remplacez l’itinéraire existant ( `[Route("[controller]")]` ) `WeatherForecastController` par les itinéraires suivants :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-199">In the server app's weather forecast controller (`Controllers/WeatherForecastController.cs`), replace the existing route (`[Route("[controller]")]`) to `WeatherForecastController` with the following routes:</span></span>

  ```csharp
  [Route("FirstApp/[controller]")]
  [Route("SecondApp/[controller]")]
  ```

  <span data-ttu-id="cc6ad-200">L’intergiciel ajouté à la méthode de l’application serveur `Startup.Configure` modifie précédemment les demandes entrantes vers `/WeatherForecast` `/FirstApp/WeatherForecast` ou, `/SecondApp/WeatherForecast` selon le port (5001/5002) ou le domaine ( `firstapp.com` / `secondapp.com` ).</span><span class="sxs-lookup"><span data-stu-id="cc6ad-200">The middleware added to the server app's `Startup.Configure` method earlier modifies incoming requests to `/WeatherForecast` to either `/FirstApp/WeatherForecast` or `/SecondApp/WeatherForecast` depending on the port (5001/5002) or domain (`firstapp.com`/`secondapp.com`).</span></span> <span data-ttu-id="cc6ad-201">Les itinéraires de contrôleur précédents sont requis pour retourner les données météorologiques de l’application serveur aux applications clientes.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-201">The preceding controller routes are required in order to return weather data from the server app to the client apps.</span></span>

### <a name="static-assets-and-class-libraries"></a><span data-ttu-id="cc6ad-202">Ressources statiques et bibliothèques de classes</span><span class="sxs-lookup"><span data-stu-id="cc6ad-202">Static assets and class libraries</span></span>

<span data-ttu-id="cc6ad-203">Utilisez les approches suivantes pour les ressources statiques :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-203">Use the following approaches for static assets:</span></span>

* <span data-ttu-id="cc6ad-204">Lorsque la ressource se trouve dans le dossier de l’application cliente `wwwroot` , indiquez les chemins d’accès normalement :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-204">When the asset is in the client app's `wwwroot` folder, provide their paths normally:</span></span>

  ```razor
  <img alt="..." src="/{ASSET FILE NAME}" />
  ```

* <span data-ttu-id="cc6ad-205">Lorsque la ressource se trouve dans le `wwwroot` dossier d’une [ Razor bibliothèque de classes (RCL)](xref:blazor/components/class-libraries), référencez la ressource statique dans l’application cliente conformément aux instructions de l' [article RCL](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):</span><span class="sxs-lookup"><span data-stu-id="cc6ad-205">When the asset is in the `wwwroot` folder of a [Razor Class Library (RCL)](xref:blazor/components/class-libraries), reference the static asset in the client app per the guidance in the [RCL article](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):</span></span>

  ```razor
  <img alt="..." src="_content/{LIBRARY NAME}/{ASSET FILE NAME}" />
  ```

<!-- HOLD for reactivation at 5.x

::: moniker range=">= aspnetcore-5.0"

Components provided to a client app by a class library are referenced normally. If any components require stylesheets or JavaScript files, use either of the following approaches to obtain the static assets:

* The client app's `wwwroot/index.html` file can link (`<link>`) to the static assets.
* The component can use the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) to obtain the static assets.

The preceding approaches are demonstrated in the following examples.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

-->

<span data-ttu-id="cc6ad-206">Les composants fournis à une application cliente par une bibliothèque de classes sont référencés normalement.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-206">Components provided to a client app by a class library are referenced normally.</span></span> <span data-ttu-id="cc6ad-207">Si des composants requièrent des feuilles de style ou des fichiers JavaScript, le fichier de l’application cliente `wwwroot/index.html` doit inclure les liens d’éléments statiques appropriés.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-207">If any components require stylesheets or JavaScript files, the client app's `wwwroot/index.html` file must include the correct static asset links.</span></span> <span data-ttu-id="cc6ad-208">Ces approches sont illustrées dans les exemples suivants.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-208">These approaches are demonstrated in the following examples.</span></span>

<!-- HOLD for reactivation at 5.x

::: moniker-end

-->

<span data-ttu-id="cc6ad-209">Ajoutez le `Jeep` composant suivant à l’une des applications clientes.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-209">Add the following `Jeep` component to one of the client apps.</span></span> <span data-ttu-id="cc6ad-210">Le `Jeep` composant utilise :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-210">The `Jeep` component uses:</span></span>

* <span data-ttu-id="cc6ad-211">Image du dossier de l’application cliente `wwwroot` ( `jeep-cj.png` ).</span><span class="sxs-lookup"><span data-stu-id="cc6ad-211">An image from the client app's `wwwroot` folder (`jeep-cj.png`).</span></span>
* <span data-ttu-id="cc6ad-212">Image d’un dossier [de Razor bibliothèque de composants](xref:blazor/components/class-libraries) ( `JeepImage` ) ajouté () `wwwroot` `jeep-yj.png` .</span><span class="sxs-lookup"><span data-stu-id="cc6ad-212">An image from an [added Razor component library](xref:blazor/components/class-libraries) (`JeepImage`) `wwwroot` folder (`jeep-yj.png`).</span></span>
* <span data-ttu-id="cc6ad-213">L’exemple de composant ( `Component1` ) est créé automatiquement par le modèle de projet RCL lorsque la `JeepImage` bibliothèque est ajoutée à la solution.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-213">The example component (`Component1`) is created automatically by the RCL project template when the `JeepImage` library is added to the solution.</span></span>

```razor
@page "/Jeep"

<h1>1979 Jeep CJ-5&trade;</h1>

<p>
    <img alt="1979 Jeep CJ-5&trade;" src="/jeep-cj.png" />
</p>

<h1>1991 Jeep YJ&trade;</h1>

<p>
    <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
</p>

<p>
    <em>Jeep CJ-5</em> and <em>Jeep YJ</em> are a trademarks of 
    <a href="https://www.fcagroup.com">Fiat Chrysler Automobiles</a>.
</p>

<JeepImage.Component1 />
```

> [!WARNING]
> <span data-ttu-id="cc6ad-214">Ne publiez **pas** d’images de véhicules publiquement, sauf si vous possédez les images.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-214">Do **not** publish images of vehicles publicly unless you own the images.</span></span> <span data-ttu-id="cc6ad-215">Dans le cas contraire, vous risquez de violation des droits d’auteur.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-215">Otherwise, you risk copyright infringement.</span></span>

<!-- HOLD for reactivation at 5.x

::: moniker range=">= aspnetcore-5.0"

The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`). To provide the `my-component` CSS class to the client app's page, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements):

```razor
<div class="my-component">
    <Link href="_content/JeepImage/styles.css" rel="stylesheet" />

    <h1>JeepImage.Component1</h1>

    <p>
        This Blazor component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

An alternative to using the [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) is to load the stylesheet from the client app's `wwwroot/index.html` file. This approach makes the stylesheet available to all of the components in the client app:

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

-->

<span data-ttu-id="cc6ad-216">L’image de la bibliothèque `jeep-yj.png` peut également être ajoutée au composant de la bibliothèque `Component1` ( `Component1.razor` ) :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-216">The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`):</span></span>

```razor
<div class="my-component">
    <h1>JeepImage.Component1</h1>

    <p>
        This Blazor component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

<span data-ttu-id="cc6ad-217">Le fichier de l’application cliente `wwwroot/index.html` demande la feuille de style de la bibliothèque avec la `<link>` balise ajoutée suivante :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-217">The client app's `wwwroot/index.html` file requests the library's stylesheet with the following added `<link>` tag:</span></span>

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

<!-- HOLD for reactivation at 5.x

::: moniker-end

-->

<span data-ttu-id="cc6ad-218">Ajoutez la navigation au `Jeep` composant dans le composant de l’application cliente `NavMenu` ( `Shared/NavMenu.razor` ) :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-218">Add navigation to the `Jeep` component in the client app's `NavMenu` component (`Shared/NavMenu.razor`):</span></span>

```razor
<li class="nav-item px-3">
    <NavLink class="nav-link" href="Jeep">
        <span class="oi oi-list-rich" aria-hidden="true"></span> Jeep
    </NavLink>
</li>
```

<span data-ttu-id="cc6ad-219">Pour plus d’informations sur RCLs, consultez :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-219">For more information on RCLs, see:</span></span>

* <xref:blazor/components/class-libraries>
* <xref:razor-pages/ui-class>

## <a name="standalone-deployment"></a><span data-ttu-id="cc6ad-220">Déploiement autonome</span><span class="sxs-lookup"><span data-stu-id="cc6ad-220">Standalone deployment</span></span>

<span data-ttu-id="cc6ad-221">Un *Déploiement autonome* sert l' Blazor WebAssembly application sous la forme d’un ensemble de fichiers statiques demandés directement par les clients.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-221">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="cc6ad-222">Tout serveur de fichiers statique peut traiter l' Blazor application.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-222">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="cc6ad-223">Les ressources de déploiement autonomes sont publiées dans le `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` dossier.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-223">Standalone deployment assets are published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="cc6ad-224">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="cc6ad-224">Azure App Service</span></span>

<span data-ttu-id="cc6ad-225">Blazor WebAssembly les applications peuvent être déployées sur Azure App services sur Windows, qui héberge l’application sur [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="cc6ad-225">Blazor WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="cc6ad-226">Le déploiement d’une Blazor WebAssembly application autonome sur Azure App service pour Linux n’est pas pris en charge actuellement.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-226">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="cc6ad-227">Une image de serveur Linux pour héberger l’application n’est pas disponible pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-227">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="cc6ad-228">Le travail est en cours pour activer ce scénario.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-228">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="cc6ad-229">IIS</span><span class="sxs-lookup"><span data-stu-id="cc6ad-229">IIS</span></span>

<span data-ttu-id="cc6ad-230">IIS est un serveur de fichiers statiques et puissant pour les Blazor applications.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-230">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="cc6ad-231">Pour configurer IIS pour héberger Blazor , consultez [créer un site Web statique sur IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="cc6ad-231">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="cc6ad-232">Les ressources publiées sont créées dans le `/bin/Release/{TARGET FRAMEWORK}/publish` dossier.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-232">Published assets are created in the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="cc6ad-233">Hébergez le contenu du `publish` dossier sur le serveur Web ou le service d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-233">Host the contents of the `publish` folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="cc6ad-234">web.config</span><span class="sxs-lookup"><span data-stu-id="cc6ad-234">web.config</span></span>

<span data-ttu-id="cc6ad-235">Lorsqu’un Blazor projet est publié, un `web.config` fichier est créé avec la configuration IIS suivante :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-235">When a Blazor project is published, a `web.config` file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="cc6ad-236">Les types MIME sont définis pour les extensions de fichiers suivantes :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-236">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="cc6ad-237">`.dll`: `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="cc6ad-237">`.dll`: `application/octet-stream`</span></span>
  * <span data-ttu-id="cc6ad-238">`.json`: `application/json`</span><span class="sxs-lookup"><span data-stu-id="cc6ad-238">`.json`: `application/json`</span></span>
  * <span data-ttu-id="cc6ad-239">`.wasm`: `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="cc6ad-239">`.wasm`: `application/wasm`</span></span>
  * <span data-ttu-id="cc6ad-240">`.woff`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="cc6ad-240">`.woff`: `application/font-woff`</span></span>
  * <span data-ttu-id="cc6ad-241">`.woff2`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="cc6ad-241">`.woff2`: `application/font-woff`</span></span>
* <span data-ttu-id="cc6ad-242">La compression HTTP est activée pour les types MIME suivants :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-242">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="cc6ad-243">Des règles du module de réécriture d’URL sont établies :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-243">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="cc6ad-244">Servez-vous du sous-répertoire dans lequel résident les ressources statiques de l’application ( `wwwroot/{PATH REQUESTED}` ).</span><span class="sxs-lookup"><span data-stu-id="cc6ad-244">Serve the sub-directory where the app's static assets reside (`wwwroot/{PATH REQUESTED}`).</span></span>
  * <span data-ttu-id="cc6ad-245">Créez le routage de secours SPA afin que les demandes pour les ressources non-fichier soient redirigées vers le document par défaut de l’application dans son dossier ressources statiques ( `wwwroot/index.html` ).</span><span class="sxs-lookup"><span data-stu-id="cc6ad-245">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (`wwwroot/index.html`).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="cc6ad-246">Utiliser un web.config personnalisé</span><span class="sxs-lookup"><span data-stu-id="cc6ad-246">Use a custom web.config</span></span>

<span data-ttu-id="cc6ad-247">Pour utiliser un `web.config` fichier personnalisé, placez le `web.config` fichier personnalisé à la racine du dossier du projet.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-247">To use a custom `web.config` file, place the custom `web.config` file at the root of the project folder.</span></span> <span data-ttu-id="cc6ad-248">Configurez le projet pour publier des ressources spécifiques à IIS à l’aide `PublishIISAssets` de dans le fichier projet de l’application et publiez le projet :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-248">Configure the project to publish IIS-specific assets using `PublishIISAssets` in the app's project file and publish the project:</span></span>

```xml
<PropertyGroup>
  <PublishIISAssets>true</PublishIISAssets>
</PropertyGroup>
```

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="cc6ad-249">Installer le module de réécriture d’URL</span><span class="sxs-lookup"><span data-stu-id="cc6ad-249">Install the URL Rewrite Module</span></span>

<span data-ttu-id="cc6ad-250">Le [module de réécriture d’URL](https://www.iis.net/downloads/microsoft/url-rewrite) est nécessaire pour réécrire les URL.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-250">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="cc6ad-251">Il n’est pas installé par défaut, et ne peut pas l’être en tant que fonctionnalité de service de rôle Serveur Web (IIS).</span><span class="sxs-lookup"><span data-stu-id="cc6ad-251">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="cc6ad-252">Vous devez le télécharger à partir du site web IIS.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-252">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="cc6ad-253">Utilisez Web Platform Installer pour installer le module :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-253">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="cc6ad-254">Localement, accédez à la [page des téléchargements du Module de réécriture d’URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="cc6ad-254">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="cc6ad-255">Pour la version anglaise, sélectionnez **WebPI** pour télécharger le programme d’installation WebPI.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-255">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="cc6ad-256">Pour les autres langues, sélectionnez l’architecture appropriée pour le serveur (x86/x64) afin de télécharger le programme d’installation.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-256">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="cc6ad-257">Copiez le programme d’installation sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-257">Copy the installer to the server.</span></span> <span data-ttu-id="cc6ad-258">Exécutez le programme d’installation.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-258">Run the installer.</span></span> <span data-ttu-id="cc6ad-259">Sélectionnez le bouton **Installer** et acceptez les termes du contrat de licence.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-259">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="cc6ad-260">Il n’est pas nécessaire de redémarrer le serveur après l’installation.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-260">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="cc6ad-261">Configurer le site web</span><span class="sxs-lookup"><span data-stu-id="cc6ad-261">Configure the website</span></span>

<span data-ttu-id="cc6ad-262">Affectez le dossier de l’application comme **chemin physique** du site web.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-262">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="cc6ad-263">Le dossier contient les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-263">The folder contains:</span></span>

* <span data-ttu-id="cc6ad-264">`web.config`Fichier utilisé par IIS pour configurer le site Web, y compris les règles de redirection et les types de contenu de fichier requis.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-264">The `web.config` file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="cc6ad-265">Le dossier de ressources statiques de l’application</span><span class="sxs-lookup"><span data-stu-id="cc6ad-265">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="cc6ad-266">Héberger en tant que sous-application IIS</span><span class="sxs-lookup"><span data-stu-id="cc6ad-266">Host as an IIS sub-app</span></span>

<span data-ttu-id="cc6ad-267">Si une application autonome est hébergée en tant que sous-application IIS, effectuez l’une des opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-267">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="cc6ad-268">Désactivez le gestionnaire de module ASP.NET Core hérité.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-268">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="cc6ad-269">Supprimez le gestionnaire dans le Blazor fichier publié de l’application `web.config` en ajoutant une `<handlers>` section au fichier :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-269">Remove the handler in the Blazor app's published `web.config` file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="cc6ad-270">Désactivez l’héritage de la section de l’application racine (parent) `<system.webServer>` à l’aide d’un `<location>` élément dont la `inheritInChildApplications` valeur est `false` :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-270">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <handlers>
          <add name="aspNetCore" ... />
        </handlers>
        <aspNetCore ... />
      </system.webServer>
    </location>
  </configuration>
  ```

<span data-ttu-id="cc6ad-271">La suppression du gestionnaire ou la désactivation de l’héritage est effectuée en plus de [la configuration du chemin d’accès de base de l’application](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="cc6ad-271">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span> <span data-ttu-id="cc6ad-272">Définissez le chemin d’accès de base de l’application dans le fichier de l’application `index.html` sur l’alias IIS utilisé lors de la configuration de la sous-application dans IIS.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-272">Set the app base path in the app's `index.html` file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="cc6ad-273">Compression Brotli et gzip</span><span class="sxs-lookup"><span data-stu-id="cc6ad-273">Brotli and Gzip compression</span></span>

<span data-ttu-id="cc6ad-274">*Cette section s’applique uniquement aux Blazor WebAssembly applications autonomes. Blazor les applications hébergées utilisent un fichier d’application ASP.net Core par défaut `web.config` , et non le fichier lié dans cette section.*</span><span class="sxs-lookup"><span data-stu-id="cc6ad-274">*This section only applies to standalone Blazor WebAssembly apps. Hosted Blazor apps use a default ASP.NET Core app `web.config` file, not the file linked in this section.*</span></span>

<span data-ttu-id="cc6ad-275">IIS peut être configuré via `web.config` pour servir des ressources compressées Brotli ou gzip Blazor pour des applications autonomes Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="cc6ad-275">IIS can be configured via `web.config` to serve Brotli or Gzip compressed Blazor assets for standalone Blazor WebAssembly apps.</span></span> <span data-ttu-id="cc6ad-276">Pour obtenir un exemple de fichier de configuration, consultez [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true) .</span><span class="sxs-lookup"><span data-stu-id="cc6ad-276">For an example configuration file, see [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).</span></span>

<span data-ttu-id="cc6ad-277">Une configuration supplémentaire de l’exemple de `web.config` fichier peut être nécessaire dans les scénarios suivants :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-277">Additional configuration of the example `web.config` file might be required in the following scenarios:</span></span>

* <span data-ttu-id="cc6ad-278">La spécification de l’application appelle l’un des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-278">The app's specification calls for either of the following:</span></span>
  * <span data-ttu-id="cc6ad-279">Service des fichiers compressés qui ne sont pas configurés par l’exemple de `web.config` fichier.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-279">Serving compressed files that aren't configured by the example `web.config` file.</span></span>
  * <span data-ttu-id="cc6ad-280">Service des fichiers compressés configurés par l’exemple `web.config` de fichier dans un format non compressé.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-280">Serving compressed files configured by the example `web.config` file in an uncompressed format.</span></span>
* <span data-ttu-id="cc6ad-281">La configuration IIS du serveur (par exemple, `applicationHost.config` ) fournit des paramètres IIS par défaut au niveau du serveur.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-281">The server's IIS configuration (for example, `applicationHost.config`) provides server-level IIS defaults.</span></span> <span data-ttu-id="cc6ad-282">Selon la configuration au niveau du serveur, l’application peut nécessiter une configuration IIS différente de celle contenue dans l’exemple de `web.config` fichier.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-282">Depending on the server-level configuration, the app might require a different IIS configuration than what the example `web.config` file contains.</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="cc6ad-283">Dépannage</span><span class="sxs-lookup"><span data-stu-id="cc6ad-283">Troubleshooting</span></span>

<span data-ttu-id="cc6ad-284">Si vous recevez un message *500 – Erreur interne du serveur* et que le Gestionnaire IIS lève des erreurs quand vous tentez d’accéder à la configuration du site web, vérifiez que le module de réécriture d’URL est installé.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-284">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="cc6ad-285">Lorsque le module n’est pas installé, le `web.config` fichier ne peut pas être analysé par IIS.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-285">When the module isn't installed, the `web.config` file can't be parsed by IIS.</span></span> <span data-ttu-id="cc6ad-286">Cela empêche le gestionnaire des services Internet de charger la configuration du site Web et le site Web à partir des Blazor fichiers statiques de service.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-286">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="cc6ad-287">Pour plus d’informations sur le dépannage des déploiements sur IIS, consultez <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-287">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="cc6ad-288">Stockage Azure</span><span class="sxs-lookup"><span data-stu-id="cc6ad-288">Azure Storage</span></span>

<span data-ttu-id="cc6ad-289">L’hébergement de fichiers statiques [Azure Storage](/azure/storage/) permet l’hébergement d’applications sans serveur Blazor .</span><span class="sxs-lookup"><span data-stu-id="cc6ad-289">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="cc6ad-290">Les noms de domaine personnalisé, le réseau de distribution de contenu Azure (CDN) et HTTPS sont pris en charge.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-290">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="cc6ad-291">Lorsque le service blob est activé pour l’hébergement de site Web statique sur un compte de stockage :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-291">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="cc6ad-292">Définissez le **nom du document d’index** sur `index.html`.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-292">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="cc6ad-293">Définissez le **chemin d’accès au document d’erreur** sur `index.html`.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-293">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="cc6ad-294">Razor les composants et autres points de terminaison non-fichier ne résident pas sur des chemins d’accès physiques dans le contenu statique stocké par le service BLOB.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-294">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="cc6ad-295">Lorsqu’une demande pour l’une de ces ressources est reçue que le Blazor routeur doit gérer, l’erreur *404-introuvable* générée par le service BLOB achemine la requête vers le **chemin du document d’erreur**.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-295">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="cc6ad-296">L' `index.html` objet blob est retourné et le Blazor routeur charge et traite le chemin d’accès.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-296">The `index.html` blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="cc6ad-297">Si les fichiers ne sont pas chargés au moment de l’exécution en raison de types MIME inappropriés dans les `Content-Type` en-têtes des fichiers, effectuez l’une des actions suivantes :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-297">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

* <span data-ttu-id="cc6ad-298">Configurez vos outils pour définir les types MIME corrects ( `Content-Type` en-têtes) lors du déploiement des fichiers.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-298">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>
* <span data-ttu-id="cc6ad-299">Modifiez les types MIME ( `Content-Type` en-têtes) des fichiers après le déploiement de l’application.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-299">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

  <span data-ttu-id="cc6ad-300">En Explorateur Stockage (Portail Azure) pour chaque fichier :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-300">In Storage Explorer (Azure portal) for each file:</span></span>
  
  1. <span data-ttu-id="cc6ad-301">Cliquez avec le bouton droit sur le fichier et sélectionnez **Propriétés**.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-301">Right-click the file and select **Properties**.</span></span>
  1. <span data-ttu-id="cc6ad-302">Définissez le **ContentType** et sélectionnez le bouton **Enregistrer** .</span><span class="sxs-lookup"><span data-stu-id="cc6ad-302">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="cc6ad-303">Pour plus d’informations, consultez [Hébergement de sites web statiques dans le service Stockage Azure](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="cc6ad-303">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="cc6ad-304">Nginx</span><span class="sxs-lookup"><span data-stu-id="cc6ad-304">Nginx</span></span>

<span data-ttu-id="cc6ad-305">Le `nginx.conf` fichier suivant est simplifié pour montrer comment configurer Nginx pour envoyer le `index.html` fichier lorsqu’il ne peut pas trouver de fichier correspondant sur le disque.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-305">The following `nginx.conf` file is simplified to show how to configure Nginx to send the `index.html` file whenever it can't find a corresponding file on disk.</span></span>

```
events { }
http {
    server {
        listen 80;

        location / {
            root      /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

<span data-ttu-id="cc6ad-306">Lors de la définition de la [limite de débit de rafales Nginx](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) avec [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req) , Blazor WebAssembly les applications peuvent nécessiter une `burst` valeur de paramètre élevée pour prendre en charge le nombre relativement élevé de demandes effectuées par une application.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-306">When setting the [NGINX burst rate limit](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) with [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req), Blazor WebAssembly apps may require a large `burst` parameter value to accommodate the relatively large number of requests made by an app.</span></span> <span data-ttu-id="cc6ad-307">Au départ, définissez la valeur sur au moins 60 :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-307">Initially, set the value to at least 60:</span></span>

```
http {
    server {
        ...

        location / {
            ...

            limit_req zone=one burst=60 nodelay;
        }
    }
}
```

<span data-ttu-id="cc6ad-308">Augmentez la valeur si outils de développement de navigateur ou outil de trafic réseau indique que les demandes reçoivent un code d’état *503-Service non disponible* .</span><span class="sxs-lookup"><span data-stu-id="cc6ad-308">Increase the value if browser developer tools or a network traffic tool indicates that requests are receiving a *503 - Service Unavailable* status code.</span></span>

<span data-ttu-id="cc6ad-309">Pour plus d’informations sur la configuration du serveur web Nginx de production, consultez [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) (Création de fichiers de configuration NGINX et NGINX Plus).</span><span class="sxs-lookup"><span data-stu-id="cc6ad-309">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="apache"></a><span data-ttu-id="cc6ad-310">Apache</span><span class="sxs-lookup"><span data-stu-id="cc6ad-310">Apache</span></span>

<span data-ttu-id="cc6ad-311">Pour déployer une Blazor WebAssembly application sur CentOS 7 ou version ultérieure :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-311">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="cc6ad-312">Créez le fichier de configuration Apache.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-312">Create the Apache configuration file.</span></span> <span data-ttu-id="cc6ad-313">L’exemple suivant est un fichier de configuration simplifié ( `blazorapp.config` ) :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-313">The following example is a simplified configuration file (`blazorapp.config`):</span></span>

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType application/wasm .wasm
       AddType application/octet-stream .dll
   
       <Directory "/var/www/blazorapp">
           Options -Indexes
           AllowOverride None
       </Directory>

       <IfModule mod_deflate.c>
           AddOutputFilterByType DEFLATE text/css
           AddOutputFilterByType DEFLATE application/javascript
           AddOutputFilterByType DEFLATE text/html
           AddOutputFilterByType DEFLATE application/octet-stream
           AddOutputFilterByType DEFLATE application/wasm
           <IfModule mod_setenvif.c>
           BrowserMatch ^Mozilla/4 gzip-only-text/html
           BrowserMatch ^Mozilla/4.0[678] no-gzip
           BrowserMatch bMSIE !no-gzip !gzip-only-text/html
       </IfModule>
       </IfModule>

       ErrorLog /var/log/httpd/blazorapp-error.log
       CustomLog /var/log/httpd/blazorapp-access.log common
   </VirtualHost>
   ```

1. <span data-ttu-id="cc6ad-314">Placez le fichier de configuration Apache dans le `/etc/httpd/conf.d/` répertoire, qui est le répertoire de configuration Apache par défaut dans CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-314">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="cc6ad-315">Placez les fichiers de l’application dans le `/var/www/blazorapp` répertoire (l’emplacement spécifié `DocumentRoot` dans le fichier de configuration).</span><span class="sxs-lookup"><span data-stu-id="cc6ad-315">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="cc6ad-316">Redémarrez le service Apache.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-316">Restart the Apache service.</span></span>

<span data-ttu-id="cc6ad-317">Pour plus d’informations, consultez [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) et [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span><span class="sxs-lookup"><span data-stu-id="cc6ad-317">For more information, see [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="cc6ad-318">GitHub Pages</span><span class="sxs-lookup"><span data-stu-id="cc6ad-318">GitHub Pages</span></span>

<span data-ttu-id="cc6ad-319">Pour gérer la réécriture d’URL, ajoutez un `wwwroot/404.html` fichier avec un script qui gère la redirection de la requête vers la `index.html` page.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-319">To handle URL rewrites, add a `wwwroot/404.html` file with a script that handles redirecting the request to the `index.html` page.</span></span> <span data-ttu-id="cc6ad-320">Pour obtenir un exemple, consultez le [ Blazor référentiel GitHub SteveSandersonMS/OnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):</span><span class="sxs-lookup"><span data-stu-id="cc6ad-320">For an example, see the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):</span></span>

* [`wwwroot/404.html`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/wwwroot/404.html)
* <span data-ttu-id="cc6ad-321">[Site actif](https://stevesandersonms.github.io/BlazorOnGitHubPages/))</span><span class="sxs-lookup"><span data-stu-id="cc6ad-321">[Live site](https://stevesandersonms.github.io/BlazorOnGitHubPages/))</span></span>

<span data-ttu-id="cc6ad-322">Lorsque vous utilisez un site de projet plutôt qu’un site d’organisation, mettez à jour la `<base>` balise dans `wwwroot/index.html` .</span><span class="sxs-lookup"><span data-stu-id="cc6ad-322">When using a project site instead of an organization site, update the `<base>` tag in `wwwroot/index.html`.</span></span> <span data-ttu-id="cc6ad-323">Définissez la `href` valeur de l’attribut sur le nom du dépôt github avec une barre oblique finale (par exemple, `/my-repository/` ).</span><span class="sxs-lookup"><span data-stu-id="cc6ad-323">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `/my-repository/`).</span></span> <span data-ttu-id="cc6ad-324">Dans le [ Blazor référentiel GitHub SteveSandersonMS/OnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages), la base `href` est mise à jour lors de la publication par le [ `.github/workflows/main.yml` fichier de configuration](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml).</span><span class="sxs-lookup"><span data-stu-id="cc6ad-324">In the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages), the base `href` is updated at publish by the [`.github/workflows/main.yml` configuration file](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml).</span></span>

> [!NOTE]
> <span data-ttu-id="cc6ad-325">Le [ Blazor référentiel GitHub SteveSandersonMS/OnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) n’est pas détenu, géré ou pris en charge par .net Foundation ou Microsoft.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-325">The [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) isn't owned, maintained, or supported by the .NET Foundation or Microsoft.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="cc6ad-326">Valeurs de configuration de l’hôte</span><span class="sxs-lookup"><span data-stu-id="cc6ad-326">Host configuration values</span></span>

<span data-ttu-id="cc6ad-327">les [ Blazor WebAssembly applications](xref:blazor/hosting-models#blazor-webassembly) peuvent accepter les valeurs de configuration d’hôte suivantes comme arguments de ligne de commande au moment de l’exécution dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-327">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="cc6ad-328">Racine de contenu</span><span class="sxs-lookup"><span data-stu-id="cc6ad-328">Content root</span></span>

<span data-ttu-id="cc6ad-329">L' `--contentroot` argument définit le chemin d’accès absolu au répertoire qui contient les fichiers de contenu de l’application ([racine du contenu](xref:fundamentals/index#content-root)).</span><span class="sxs-lookup"><span data-stu-id="cc6ad-329">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="cc6ad-330">Dans les exemples suivants, `/content-root-path` est le chemin racine du contenu de l’application.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-330">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="cc6ad-331">Passez l’argument lors de l’exécution de l’application localement à une invite de commandes.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-331">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="cc6ad-332">À partir du répertoire de l’application, exécutez :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-332">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="cc6ad-333">Ajoutez une entrée au fichier de l’application `launchSettings.json` dans le profil **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="cc6ad-333">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="cc6ad-334">Ce paramètre est utilisé en cas d’exécution de l’application avec le débogueur Visual Studio et dans une invite de commandes avec `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-334">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="cc6ad-335">Dans Visual Studio, spécifiez l’argument dans **Propriétés**  >  **Déboguer** les arguments de l'  >  **application**.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-335">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="cc6ad-336">La définition de l’argument dans la page de propriétés de Visual Studio ajoute l’argument au `launchSettings.json` fichier.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-336">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="cc6ad-337">Base du chemin</span><span class="sxs-lookup"><span data-stu-id="cc6ad-337">Path base</span></span>

<span data-ttu-id="cc6ad-338">L' `--pathbase` argument définit le chemin d’accès de base d’application pour une application exécutée localement avec un chemin d’URL relatif non racine (la `<base>` balise `href` est définie sur un chemin d’accès autre que `/` pour la mise en lots et la production).</span><span class="sxs-lookup"><span data-stu-id="cc6ad-338">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="cc6ad-339">Dans les exemples suivants, `/relative-URL-path` est la base du chemin de l’application.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-339">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="cc6ad-340">Pour plus d’informations, consultez chemin de la base de l' [application](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="cc6ad-340">For more information, see [App base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="cc6ad-341">Contrairement au chemin fourni au `href` de la balise `<base>`, n’incluez pas de barre oblique (`/`) quand vous passez la valeur d’argument `--pathbase`.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-341">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="cc6ad-342">Si vous spécifiez `<base href="/CoolApp/">` (inclut une barre oblique) comme chemin de base de l’application dans la balise `<base>`, passez `--pathbase=/CoolApp` (aucune barre oblique de fin) comme valeur d’argument de ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-342">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="cc6ad-343">Passez l’argument lors de l’exécution de l’application localement à une invite de commandes.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-343">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="cc6ad-344">À partir du répertoire de l’application, exécutez :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-344">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="cc6ad-345">Ajoutez une entrée au fichier de l’application `launchSettings.json` dans le profil **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="cc6ad-345">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="cc6ad-346">Ce paramètre est utilisé en cas d’exécution de l’application avec le débogueur Visual Studio et dans une invite de commandes avec `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-346">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="cc6ad-347">Dans Visual Studio, spécifiez l’argument dans **Propriétés**  >  **Déboguer** les arguments de l'  >  **application**.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-347">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="cc6ad-348">La définition de l’argument dans la page de propriétés de Visual Studio ajoute l’argument au `launchSettings.json` fichier.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-348">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="cc6ad-349">URLs</span><span class="sxs-lookup"><span data-stu-id="cc6ad-349">URLs</span></span>

<span data-ttu-id="cc6ad-350">L’argument `--urls` définit les adresses IP ou les adresses d’hôtes avec les ports et protocoles sur lesquels il faut écouter les demandes.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-350">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="cc6ad-351">Passez l’argument lors de l’exécution de l’application localement à une invite de commandes.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-351">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="cc6ad-352">À partir du répertoire de l’application, exécutez :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-352">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="cc6ad-353">Ajoutez une entrée au fichier de l’application `launchSettings.json` dans le profil **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="cc6ad-353">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="cc6ad-354">Ce paramètre est utilisé en cas d’exécution de l’application avec le débogueur Visual Studio et dans une invite de commandes avec `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-354">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="cc6ad-355">Dans Visual Studio, spécifiez l’argument dans **Propriétés**  >  **Déboguer** les arguments de l'  >  **application**.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-355">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="cc6ad-356">La définition de l’argument dans la page de propriétés de Visual Studio ajoute l’argument au `launchSettings.json` fichier.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-356">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

::: moniker range=">= aspnetcore-5.0"

## <a name="configure-the-trimmer"></a><span data-ttu-id="cc6ad-357">Configurer l’outil de découpage</span><span class="sxs-lookup"><span data-stu-id="cc6ad-357">Configure the Trimmer</span></span>

<span data-ttu-id="cc6ad-358">Blazor effectue un découpage en langage intermédiaire sur chaque version de mise en production pour supprimer l’IL inutile des assemblys de sortie.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-358">Blazor performs Intermediate Language (IL) trimming on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="cc6ad-359">Pour plus d'informations, consultez <xref:blazor/host-and-deploy/configure-trimmer>.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-359">For more information, see <xref:blazor/host-and-deploy/configure-trimmer>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="configure-the-linker"></a><span data-ttu-id="cc6ad-360">Configurer l'éditeur de liens</span><span class="sxs-lookup"><span data-stu-id="cc6ad-360">Configure the Linker</span></span>

<span data-ttu-id="cc6ad-361">Blazor effectue une liaison IL (Intermediate Language) sur chaque version de mise en production pour supprimer l’IL inutile des assemblys de sortie.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-361">Blazor performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="cc6ad-362">Pour plus d'informations, consultez <xref:blazor/host-and-deploy/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-362">For more information, see <xref:blazor/host-and-deploy/configure-linker>.</span></span>

::: moniker-end

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="cc6ad-363">Chargement des ressources de démarrage personnalisé</span><span class="sxs-lookup"><span data-stu-id="cc6ad-363">Custom boot resource loading</span></span>

<span data-ttu-id="cc6ad-364">Une Blazor WebAssembly application peut être initialisée avec la `loadBootResource` fonction pour remplacer le mécanisme de chargement des ressources de démarrage intégré.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-364">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="cc6ad-365">Utilisez `loadBootResource` pour les scénarios suivants :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-365">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="cc6ad-366">Autorisez les utilisateurs à charger des ressources statiques, telles que des données de fuseau horaire ou `dotnet.wasm` à partir d’un CDN.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-366">Allow users to load static resources, such as timezone data or `dotnet.wasm` from a CDN.</span></span>
* <span data-ttu-id="cc6ad-367">Chargez les assemblys compressés à l’aide d’une requête HTTP et décompressez-les sur le client pour les hôtes qui ne prennent pas en charge l’extraction du contenu compressé à partir du serveur.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-367">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="cc6ad-368">Alias des ressources à un autre nom en redirigeant chaque `fetch` requête vers un nouveau nom.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-368">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="cc6ad-369">`loadBootResource` les paramètres s’affichent dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-369">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="cc6ad-370">Paramètre</span><span class="sxs-lookup"><span data-stu-id="cc6ad-370">Parameter</span></span>    | <span data-ttu-id="cc6ad-371">Description</span><span class="sxs-lookup"><span data-stu-id="cc6ad-371">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="cc6ad-372">Type de la ressource.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-372">The type of the resource.</span></span> <span data-ttu-id="cc6ad-373">Types permissables : `assembly` , `pdb` , `dotnetjs` , `dotnetwasm` , `timezonedata`</span><span class="sxs-lookup"><span data-stu-id="cc6ad-373">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="cc6ad-374">Nom de la ressource.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-374">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="cc6ad-375">URI relatif ou absolu de la ressource.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-375">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="cc6ad-376">Chaîne d’intégrité représentant le contenu attendu dans la réponse.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-376">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="cc6ad-377">`loadBootResource` retourne l’un des éléments suivants pour remplacer le processus de chargement :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-377">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="cc6ad-378">Chaîne d’URI.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-378">URI string.</span></span> <span data-ttu-id="cc6ad-379">Dans l’exemple suivant ( `wwwroot/index.html` ), les fichiers suivants sont pris en charge à partir d’un CDN à l’adresse `https://my-awesome-cdn.com/` :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-379">In the following example (`wwwroot/index.html`), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * `dotnet.*.js`
  * `dotnet.wasm`
  * <span data-ttu-id="cc6ad-380">Données de fuseau horaire</span><span class="sxs-lookup"><span data-stu-id="cc6ad-380">Timezone data</span></span>

  ```html
  ...

  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        console.log(`Loading: '${type}', '${name}', '${defaultUri}', '${integrity}'`);
        switch (type) {
          case 'dotnetjs':
          case 'dotnetwasm':
          case 'timezonedata':
            return `https://my-awesome-cdn.com/blazorwebassembly/3.2.0/${name}`;
        }
      }
    });
  </script>
  ```

* <span data-ttu-id="cc6ad-381">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-381">`Promise<Response>`.</span></span> <span data-ttu-id="cc6ad-382">Transmettez le `integrity` paramètre dans un en-tête pour conserver le comportement de contrôle d’intégrité par défaut.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-382">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="cc6ad-383">L’exemple suivant ( `wwwroot/index.html` ) ajoute un en-tête HTTP personnalisé aux demandes sortantes et passe `integrity` le paramètre à l' `fetch` appel :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-383">The following example (`wwwroot/index.html`) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
  ```html
  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        return fetch(defaultUri, { 
          cache: 'no-cache',
          integrity: integrity,
          headers: { 'MyCustomHeader': 'My custom value' }
        });
      }
    });
  </script>
  ```

* <span data-ttu-id="cc6ad-384">`null`/`undefined`, ce qui entraîne le comportement de chargement par défaut.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-384">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="cc6ad-385">Les sources externes doivent retourner les en-têtes CORS requis pour les navigateurs afin d’autoriser le chargement des ressources Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-385">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="cc6ad-386">Les CDN fournissent généralement les en-têtes requis par défaut.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-386">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="cc6ad-387">Il vous suffit de spécifier des types pour les comportements personnalisés.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-387">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="cc6ad-388">Les types non spécifiés à `loadBootResource` sont chargés par le Framework en fonction de leurs comportements de chargement par défaut.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-388">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="cc6ad-389">Modifier l’extension de nom de fichier des fichiers DLL</span><span class="sxs-lookup"><span data-stu-id="cc6ad-389">Change the filename extension of DLL files</span></span>

<span data-ttu-id="cc6ad-390">Si vous avez besoin de modifier les extensions de nom de fichier des fichiers publiés de l’application `.dll` , suivez les instructions de cette section.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-390">In case you have a need to change the filename extensions of the app's published `.dll` files, follow the guidance in this section.</span></span>

<span data-ttu-id="cc6ad-391">Après avoir publié l’application, utilisez un script d’interpréteur de commandes ou un pipeline de build DevOps pour renommer les `.dll` fichiers afin d’utiliser une extension de fichier différente.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-391">After publishing the app, use a shell script or DevOps build pipeline to rename `.dll` files to use a different file extension.</span></span> <span data-ttu-id="cc6ad-392">Ciblez les `.dll` fichiers dans le `wwwroot` Répertoire de la sortie publiée de l’application (par exemple, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot` ).</span><span class="sxs-lookup"><span data-stu-id="cc6ad-392">Target the `.dll` files in the `wwwroot` directory of the app's published output (for example, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).</span></span>

<span data-ttu-id="cc6ad-393">Dans les exemples suivants, les `.dll` fichiers sont renommés pour utiliser l' `.bin` extension de fichier.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-393">In the following examples, `.dll` files are renamed to use the `.bin` file extension.</span></span>

<span data-ttu-id="cc6ad-394">Sur Windows :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-394">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="cc6ad-395">Si des ressources de service Worker sont également utilisées, ajoutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-395">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="cc6ad-396">Sur Linux ou macOS :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-396">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="cc6ad-397">Si des ressources de service Worker sont également utilisées, ajoutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-397">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="cc6ad-398">Pour utiliser une extension de fichier différente de `.bin` , remplacez `.bin` dans les commandes précédentes.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-398">To use a different file extension than `.bin`, replace `.bin` in the preceding commands.</span></span>

<span data-ttu-id="cc6ad-399">Pour résoudre les fichiers et compressés `blazor.boot.json.gz` `blazor.boot.json.br` , adoptez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-399">To address the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="cc6ad-400">Supprimez les `blazor.boot.json.gz` fichiers et compressés `blazor.boot.json.br` .</span><span class="sxs-lookup"><span data-stu-id="cc6ad-400">Remove the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files.</span></span> <span data-ttu-id="cc6ad-401">La compression est désactivée avec cette approche.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-401">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="cc6ad-402">Recompressez le fichier mis à jour `blazor.boot.json` .</span><span class="sxs-lookup"><span data-stu-id="cc6ad-402">Recompress the updated `blazor.boot.json` file.</span></span>

<span data-ttu-id="cc6ad-403">Les instructions précédentes s’appliquent également lorsque les ressources de service Worker sont en cours d’utilisation.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-403">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="cc6ad-404">Supprimez ou recompressez `wwwroot/service-worker-assets.js.br` et `wwwroot/service-worker-assets.js.gz` .</span><span class="sxs-lookup"><span data-stu-id="cc6ad-404">Remove or recompress `wwwroot/service-worker-assets.js.br` and `wwwroot/service-worker-assets.js.gz`.</span></span> <span data-ttu-id="cc6ad-405">Dans le cas contraire, les vérifications de l’intégrité des fichiers échouent dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-405">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="cc6ad-406">L’exemple Windows suivant utilise un script PowerShell placé à la racine du projet.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-406">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="cc6ad-407">`ChangeDLLExtensions.ps1:`:</span><span class="sxs-lookup"><span data-stu-id="cc6ad-407">`ChangeDLLExtensions.ps1:`:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="cc6ad-408">Si des ressources de service Worker sont également utilisées, ajoutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-408">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="cc6ad-409">Dans le fichier projet, le script est exécuté après la publication de l’application :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-409">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

> [!NOTE]
> <span data-ttu-id="cc6ad-410">Lorsque vous renommez et chargez en différé les mêmes assemblys, consultez les instructions dans <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files> .</span><span class="sxs-lookup"><span data-stu-id="cc6ad-410">When renaming and lazy loading the same assemblies, see the guidance in <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files>.</span></span>

## <a name="resolve-integrity-check-failures"></a><span data-ttu-id="cc6ad-411">Résoudre les échecs de vérification de l’intégrité</span><span class="sxs-lookup"><span data-stu-id="cc6ad-411">Resolve integrity check failures</span></span>

<span data-ttu-id="cc6ad-412">Lorsque Blazor WebAssembly télécharge les fichiers de démarrage d’une application, il demande au navigateur d’effectuer des contrôles d’intégrité sur les réponses.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-412">When Blazor WebAssembly downloads an app's startup files, it instructs the browser to perform integrity checks on the responses.</span></span> <span data-ttu-id="cc6ad-413">Elle utilise les informations du `blazor.boot.json` fichier pour spécifier les valeurs de hachage SHA-256 attendues pour `.dll` , `.wasm` et d’autres fichiers.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-413">It uses information in the `blazor.boot.json` file to specify the expected SHA-256 hash values for `.dll`, `.wasm`, and other files.</span></span> <span data-ttu-id="cc6ad-414">Cela est bénéfique pour les raisons suivantes :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-414">This is beneficial for the following reasons:</span></span>

* <span data-ttu-id="cc6ad-415">Cela vous garantit que vous ne risquez pas de charger un ensemble incohérent de fichiers, par exemple si un nouveau déploiement est appliqué à votre serveur Web pendant que l’utilisateur est en train de télécharger les fichiers d’application.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-415">It ensures you don't risk loading an inconsistent set of files, for example if a new deployment is applied to your web server while the user is in the process of downloading the application files.</span></span> <span data-ttu-id="cc6ad-416">Des fichiers incohérents peuvent entraîner un comportement indéfini.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-416">Inconsistent files could lead to undefined behavior.</span></span>
* <span data-ttu-id="cc6ad-417">Il garantit que le navigateur de l’utilisateur ne met jamais en cache les réponses incohérentes ou non valides, ce qui peut empêcher le démarrage de l’application, même s’il actualise la page manuellement.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-417">It ensures the user's browser never caches inconsistent or invalid responses, which could prevent them from starting the app even if they manually refresh the page.</span></span>
* <span data-ttu-id="cc6ad-418">Cela permet de mettre en cache les réponses et de ne pas même vérifier les modifications côté serveur tant que les hachages SHA-256 attendus eux-mêmes ne sont pas modifiés, de sorte que les chargements de page suivants impliquent moins de demandes et s’exécutent beaucoup plus rapidement.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-418">It makes it safe to cache the responses and not even check for server-side changes until the expected SHA-256 hashes themselves change, so subsequent page loads involve fewer requests and complete much faster.</span></span>

<span data-ttu-id="cc6ad-419">Si votre serveur Web renvoie des réponses qui ne correspondent pas aux hachages SHA-256 attendus, vous verrez une erreur semblable à celle qui suit s’afficher dans la console de développeur du navigateur :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-419">If your web server returns responses that don't match the expected SHA-256 hashes, you will see an error similar to the following appear in the browser's developer console:</span></span>

> <span data-ttu-id="cc6ad-420">Impossible de trouver un condensé valide dans l’attribut « Integrity » pour la ressource « https://myapp.example.com/\_framework/My BlazorApp.dll » avec l’intégrité SHA-256 calculée « IIa70iwvmEg5WiDV17OpQ5eCztNYqL186J56852RpJY = ».</span><span class="sxs-lookup"><span data-stu-id="cc6ad-420">Failed to find a valid digest in the 'integrity' attribute for resource 'https://myapp.example.com/\_framework/MyBlazorApp.dll' with computed SHA-256 integrity 'IIa70iwvmEg5WiDV17OpQ5eCztNYqL186J56852RpJY='.</span></span> <span data-ttu-id="cc6ad-421">La ressource a été bloquée.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-421">The resource has been blocked.</span></span>

<span data-ttu-id="cc6ad-422">Dans la plupart des cas, il ne s’agit *pas* d’un problème de vérification de l’intégrité.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-422">In most cases, this is *not* a problem with integrity checking itself.</span></span> <span data-ttu-id="cc6ad-423">Au lieu de cela, cela signifie qu’il existe un autre problème et que la vérification de l’intégrité vous avertit de cet autre problème.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-423">Instead, it means there is some other problem, and the integrity check is warning you about that other problem.</span></span>

### <a name="diagnosing-integrity-problems"></a><span data-ttu-id="cc6ad-424">Diagnostic des problèmes d’intégrité</span><span class="sxs-lookup"><span data-stu-id="cc6ad-424">Diagnosing integrity problems</span></span>

<span data-ttu-id="cc6ad-425">Quand une application est générée, le `blazor.boot.json` manifeste généré décrit les hachages SHA-256 de vos ressources de démarrage (par exemple,, `.dll` et d' `.wasm` autres fichiers) au moment où la sortie de la génération est générée.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-425">When an app is built, the generated `blazor.boot.json` manifest describes the SHA-256 hashes of your boot resources (for example, `.dll`, `.wasm`, and other files) at the time that the build output is produced.</span></span> <span data-ttu-id="cc6ad-426">Le contrôle d’intégrité réussit tant que les hachages SHA-256 dans `blazor.boot.json` correspondent aux fichiers remis au navigateur.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-426">The integrity check passes as long as the SHA-256 hashes in `blazor.boot.json` match the files delivered to the browser.</span></span>

<span data-ttu-id="cc6ad-427">Causes courantes de cet échec :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-427">Common reasons why this fails are:</span></span>

 * <span data-ttu-id="cc6ad-428">La réponse du serveur Web est une erreur (par exemple, *404-introuvable* ou une *erreur de serveur 500-Internal*) au lieu du fichier demandé par le navigateur.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-428">The web server's response is an error (for example, a *404 - Not Found* or a *500 - Internal Server Error*) instead of the file the browser requested.</span></span> <span data-ttu-id="cc6ad-429">Cela est signalé par le navigateur comme un échec de vérification de l’intégrité et non comme un échec de réponse.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-429">This is reported by the browser as an integrity check failure and not as a response failure.</span></span>
 * <span data-ttu-id="cc6ad-430">Une modification a été apportée au contenu des fichiers entre la génération et la remise des fichiers dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-430">Something has changed the contents of the files between the build and delivery of the files to the browser.</span></span> <span data-ttu-id="cc6ad-431">Cela peut se produire :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-431">This might happen:</span></span>
   * <span data-ttu-id="cc6ad-432">Si vous ou les outils de génération modifiez manuellement la sortie de la génération.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-432">If you or build tools manually modify the build output.</span></span>
   * <span data-ttu-id="cc6ad-433">Si certains aspects du processus de déploiement ont modifié les fichiers.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-433">If some aspect of the deployment process modified the files.</span></span> <span data-ttu-id="cc6ad-434">Par exemple, si vous utilisez un mécanisme de déploiement basé sur git, gardez à l’esprit que git convertit en toute transparence les fins de ligne de style Windows en terminaisons de ligne de style UNIX si vous validez des fichiers sur Windows et que vous les consultez sur Linux.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-434">For example if you use a Git-based deployment mechanism, bear in mind that Git transparently converts Windows-style line endings to Unix-style line endings if you commit files on Windows and check them out on Linux.</span></span> <span data-ttu-id="cc6ad-435">La modification des fins de ligne de fichier modifie les hachages SHA-256.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-435">Changing file line endings change the SHA-256 hashes.</span></span> <span data-ttu-id="cc6ad-436">Pour éviter ce problème, envisagez [ `.gitattributes` d’utiliser pour traiter les artefacts de build comme des `binary` fichiers](https://git-scm.com/book/en/v2/Customizing-Git-Git-Attributes).</span><span class="sxs-lookup"><span data-stu-id="cc6ad-436">To avoid this problem, consider [using `.gitattributes` to treat build artifacts as `binary` files](https://git-scm.com/book/en/v2/Customizing-Git-Git-Attributes).</span></span>
   * <span data-ttu-id="cc6ad-437">Le serveur Web modifie le contenu du fichier dans le cadre de ses services.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-437">The web server modifies the file contents as part of serving them.</span></span> <span data-ttu-id="cc6ad-438">Par exemple, certains réseaux de distribution de contenu (CDN) tentent automatiquement de [réduire](xref:client-side/bundling-and-minification#minification) html, ce qui les modifie.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-438">For example, some content distribution networks (CDNs) automatically attempt to [minify](xref:client-side/bundling-and-minification#minification) HTML, thereby modifying it.</span></span> <span data-ttu-id="cc6ad-439">Vous devrez peut-être désactiver ces fonctionnalités.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-439">You may need to disable such features.</span></span>

<span data-ttu-id="cc6ad-440">Pour diagnostiquer les éléments suivants dans votre cas :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-440">To diagnose which of these applies in your case:</span></span>

 1. <span data-ttu-id="cc6ad-441">Notez le fichier qui déclenche l’erreur en lisant le message d’erreur.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-441">Note which file is triggering the error by reading the error message.</span></span>
 1. <span data-ttu-id="cc6ad-442">Ouvrez les outils de développement de votre navigateur et recherchez dans l’onglet *réseau* . Si nécessaire, rechargez la page pour afficher la liste des demandes et des réponses.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-442">Open your browser's developer tools and look in the *Network* tab. If necessary, reload the page to see the list of requests and responses.</span></span> <span data-ttu-id="cc6ad-443">Recherchez le fichier qui déclenche l’erreur dans cette liste.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-443">Find the file that is triggering the error in that list.</span></span>
 1. <span data-ttu-id="cc6ad-444">Vérifiez le code d’état HTTP dans la réponse.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-444">Check the HTTP status code in the response.</span></span> <span data-ttu-id="cc6ad-445">Si le serveur retourne une valeur autre que *200-OK* (ou un autre code d’État 2xx), vous pouvez diagnostiquer un problème côté serveur.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-445">If the server returns anything other than *200 - OK* (or another 2xx status code), then you have a server-side problem to diagnose.</span></span> <span data-ttu-id="cc6ad-446">Par exemple, le code d’état 403 signifie qu’il y a un problème d’autorisation, alors que le code d’état 500 signifie que le serveur échoue de manière non spécifiée.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-446">For example, status code 403 means there's an authorization problem, whereas status code 500 means the server is failing in an unspecified manner.</span></span> <span data-ttu-id="cc6ad-447">Consultez les journaux côté serveur pour diagnostiquer et corriger l’application.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-447">Consult server-side logs to diagnose and fix the app.</span></span>
 1. <span data-ttu-id="cc6ad-448">Si le code d’État est *200-OK* pour la ressource, examinez le contenu de la réponse dans les outils de développement du navigateur et vérifiez que le contenu correspond aux données attendues.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-448">If the status code is *200 - OK* for the resource, look at the response content in browser's developer tools and check that the content matches up with the data expected.</span></span> <span data-ttu-id="cc6ad-449">Par exemple, un problème courant consiste à configurer le routage de manière inhabituelle afin que les demandes retournent vos `index.html` données même pour d’autres fichiers.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-449">For example, a common problem is to misconfigure routing so that requests return your `index.html` data even for other files.</span></span> <span data-ttu-id="cc6ad-450">Assurez-vous que `.wasm` les réponses aux requêtes sont des binaires Webassembly et que les réponses aux `.dll` requêtes sont des binaires d’assembly .net.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-450">Make sure that responses to `.wasm` requests are WebAssembly binaries and that responses to `.dll` requests are .NET assembly binaries.</span></span> <span data-ttu-id="cc6ad-451">Si ce n’est pas le cas, vous pouvez diagnostiquer un problème de routage côté serveur.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-451">If not, you have a server-side routing problem to diagnose.</span></span>
 1. <span data-ttu-id="cc6ad-452">Recherchez la validation de la sortie publiée et déployée de l’application avec le [script PowerShell d’intégrité](#troubleshoot-integrity-powershell-script)de la résolution des problèmes.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-452">Seek to validate the app's published and deployed output with the [Troubleshoot integrity PowerShell script](#troubleshoot-integrity-powershell-script).</span></span>

<span data-ttu-id="cc6ad-453">Si vous confirmez que le serveur retourne des données correctes plausibly, il doit y avoir une autre modification du contenu entre la génération et la remise du fichier.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-453">If you confirm that the server is returning plausibly correct data, there must be something else modifying the contents in between build and delivery of the file.</span></span> <span data-ttu-id="cc6ad-454">Pour examiner ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-454">To investigate this:</span></span>

 * <span data-ttu-id="cc6ad-455">Examinez le chaîne d’outils de build et le mécanisme de déploiement en cas de modification des fichiers après la génération des fichiers.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-455">Examine the build toolchain and deployment mechanism in case they're modifying files after the files are built.</span></span> <span data-ttu-id="cc6ad-456">C’est le cas, par exemple, lorsque git transforme les fins de ligne de fichier, comme décrit précédemment.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-456">An example of this is when Git transforms file line endings, as described earlier.</span></span>
 * <span data-ttu-id="cc6ad-457">Examinez le serveur Web ou la configuration CDN au cas où ils seraient configurés pour modifier les réponses de manière dynamique (par exemple, en tentant de réduire HTML).</span><span class="sxs-lookup"><span data-stu-id="cc6ad-457">Examine the web server or CDN configuration in case they're set up to modify responses dynamically (for example, trying to minify HTML).</span></span> <span data-ttu-id="cc6ad-458">Il convient que le serveur Web implémente la compression HTTP (par exemple, en retournant `content-encoding: br` ou `content-encoding: gzip` ), car cela n’affecte pas le résultat après la décompression.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-458">It's fine for the web server to implement HTTP compression (for example, returning `content-encoding: br` or `content-encoding: gzip`), since this doesn't affect the result after decompression.</span></span> <span data-ttu-id="cc6ad-459">Toutefois, il *n’est pas correct* pour le serveur Web de modifier les données non compressées.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-459">However, it's *not* fine for the web server to modify the uncompressed data.</span></span>

### <a name="troubleshoot-integrity-powershell-script"></a><span data-ttu-id="cc6ad-460">Résoudre les problèmes de script PowerShell d’intégrité</span><span class="sxs-lookup"><span data-stu-id="cc6ad-460">Troubleshoot integrity PowerShell script</span></span>

<span data-ttu-id="cc6ad-461">Utilisez le [`integrity.ps1`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/integrity.ps1?raw=true) script PowerShell pour valider une application publiée et déployée Blazor .</span><span class="sxs-lookup"><span data-stu-id="cc6ad-461">Use the [`integrity.ps1`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/integrity.ps1?raw=true) PowerShell script to validate a published and deployed Blazor app.</span></span> <span data-ttu-id="cc6ad-462">Le script est fourni comme point de départ lorsque l’application rencontre des problèmes d’intégrité que l' Blazor infrastructure ne peut pas identifier.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-462">The script is provided as a starting point when the app has integrity issues that the Blazor framework can't identify.</span></span> <span data-ttu-id="cc6ad-463">La personnalisation du script peut être nécessaire pour vos applications.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-463">Customization of the script might be required for your apps.</span></span>

<span data-ttu-id="cc6ad-464">Le script vérifie les fichiers dans le `publish` dossier et les télécharge à partir de l’application déployée pour détecter les problèmes dans les différents manifestes qui contiennent des hachages d’intégrité.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-464">The script checks the files in the `publish` folder and downloaded from the deployed app to detect issues in the different manifests that contain integrity hashes.</span></span> <span data-ttu-id="cc6ad-465">Ces contrôles doivent détecter les problèmes les plus courants :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-465">These checks should detect the most common problems:</span></span>

* <span data-ttu-id="cc6ad-466">Vous avez modifié un fichier dans la sortie publiée sans l’avoir réalisé.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-466">You modified a file in the published output without realizing it.</span></span>
* <span data-ttu-id="cc6ad-467">L’application n’a pas été correctement déployée sur la cible de déploiement, ou une modification a été apportée dans l’environnement de la cible de déploiement.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-467">The app wasn't correctly deployed to the deployment target, or something changed within the deployment target's environment.</span></span>
* <span data-ttu-id="cc6ad-468">Il existe des différences entre l’application déployée et la sortie de la publication de l’application.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-468">There are differences between the deployed app and the output from publishing the app.</span></span>

<span data-ttu-id="cc6ad-469">Appelez le script avec la commande suivante dans une interface de commande PowerShell :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-469">Invoke the script with the following command in a PowerShell command shell:</span></span>

```powershell
.\integrity.ps1 {BASE URL} {PUBLISH OUTPUT FOLDER}
```

<span data-ttu-id="cc6ad-470">Espaces réservés</span><span class="sxs-lookup"><span data-stu-id="cc6ad-470">Placeholders:</span></span>

* <span data-ttu-id="cc6ad-471">`{BASE URL}`: URL de l’application déployée.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-471">`{BASE URL}`: The URL of the deployed app.</span></span>
* <span data-ttu-id="cc6ad-472">`{PUBLISH OUTPUT FOLDER}`: Chemin d’accès au dossier de l’application ou à l' `publish` emplacement où l’application est publiée pour le déploiement.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-472">`{PUBLISH OUTPUT FOLDER}`: The path to the app's `publish` folder or location where the app is published for deployment.</span></span>

> [!NOTE]
> <span data-ttu-id="cc6ad-473">Pour cloner le `dotnet/AspNetCore.Docs` référentiel GitHub sur un système qui utilise l’antivirus [BitDefender](https://www.bitdefender.com) , ajoutez une exception à BitDefender pour le `integrity.ps1` script.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-473">To clone the `dotnet/AspNetCore.Docs` GitHub repository to a system that uses the [Bitdefender](https://www.bitdefender.com) virus scanner, add an exception to Bitdefender for the `integrity.ps1` script.</span></span> <span data-ttu-id="cc6ad-474">Ajoutez l’exception à BitDefender avant de cloner le référentiel pour éviter que le script soit mis en quarantaine par l’antivirus.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-474">Add the exception to Bitdefender before cloning the repo to avoid having the script quarantined by the virus scanner.</span></span> <span data-ttu-id="cc6ad-475">L’exemple suivant est un chemin d’accès classique au script pour le référentiel cloné sur un système Windows.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-475">The following example is a typical path to the script for the cloned repo on a Windows system.</span></span> <span data-ttu-id="cc6ad-476">Ajustez le chemin d’accès en fonction des besoins.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-476">Adjust the path as needed.</span></span> <span data-ttu-id="cc6ad-477">L’espace réservé `{USER}` est le segment de chemin d’accès de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-477">The placeholder `{USER}` is the user's path segment.</span></span>
>
> ```
> C:\Users\{USER}\Documents\GitHub\AspNetCore.Docs\aspnetcore\blazor\host-and-deploy\webassembly\_samples\integrity.ps1
> ```

### <a name="disable-integrity-checking-for-non-pwa-apps"></a><span data-ttu-id="cc6ad-478">Désactiver le contrôle d’intégrité pour les applications non-PWA</span><span class="sxs-lookup"><span data-stu-id="cc6ad-478">Disable integrity checking for non-PWA apps</span></span>

<span data-ttu-id="cc6ad-479">Dans la plupart des cas, ne désactivez pas la vérification de l’intégrité.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-479">In most cases, don't disable integrity checking.</span></span> <span data-ttu-id="cc6ad-480">La désactivation de la vérification de l’intégrité ne résout pas le problème sous-jacent qui a provoqué les réponses inattendues et entraîne la perte des avantages répertoriés plus haut.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-480">Disabling integrity checking doesn't solve the underlying problem that has caused the unexpected responses and results in losing the benefits listed earlier.</span></span>

<span data-ttu-id="cc6ad-481">Dans certains cas, le serveur Web ne peut pas être reporté pour retourner des réponses cohérentes et vous n’avez aucun choix, mais vous pouvez désactiver les contrôles d’intégrité.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-481">There may be cases where the web server can't be relied upon to return consistent responses, and you have no choice but to disable integrity checks.</span></span> <span data-ttu-id="cc6ad-482">Pour désactiver les contrôles d’intégrité, ajoutez ce qui suit à un groupe de propriétés dans le Blazor WebAssembly fichier du projet `.csproj` :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-482">To disable integrity checks, add the following to a property group in the Blazor WebAssembly project's `.csproj` file:</span></span>

```xml
<BlazorCacheBootResources>false</BlazorCacheBootResources>
```

<span data-ttu-id="cc6ad-483">`BlazorCacheBootResources` désactive également Blazor le comportement par défaut de la mise en cache des `.dll` `.wasm` fichiers, et d’autres fichiers en fonction de leurs hachages SHA-256, car la propriété indique que les hachages SHA-256 ne peuvent pas être basés sur l’exactitude.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-483">`BlazorCacheBootResources` also disables Blazor's default behavior of caching the `.dll`, `.wasm`, and other files based on their SHA-256 hashes because the property indicates that the SHA-256 hashes can't be relied upon for correctness.</span></span> <span data-ttu-id="cc6ad-484">Même avec ce paramètre, le cache HTTP normal du navigateur peut toujours mettre en cache ces fichiers, mais cela dépend de la configuration de votre serveur Web et des `cache-control` en-têtes qu’il dessert.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-484">Even with this setting, the browser's normal HTTP cache may still cache those files, but whether or not this happens depends on your web server configuration and the `cache-control` headers that it serves.</span></span>

> [!NOTE]
> <span data-ttu-id="cc6ad-485">La `BlazorCacheBootResources` propriété ne désactive pas les vérifications de l’intégrité des [applications Web progressives (PWA)](xref:blazor/progressive-web-app).</span><span class="sxs-lookup"><span data-stu-id="cc6ad-485">The `BlazorCacheBootResources` property doesn't disable integrity checks for [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app).</span></span> <span data-ttu-id="cc6ad-486">Pour plus d’informations sur PWA, consultez la section [Disable Integrity checking for PWA](#disable-integrity-checking-for-pwas) .</span><span class="sxs-lookup"><span data-stu-id="cc6ad-486">For guidance pertaining to PWAs, see the [Disable integrity checking for PWAs](#disable-integrity-checking-for-pwas) section.</span></span>

### <a name="disable-integrity-checking-for-pwas"></a><span data-ttu-id="cc6ad-487">Désactiver la vérification de l’intégrité pour PWA</span><span class="sxs-lookup"><span data-stu-id="cc6ad-487">Disable integrity checking for PWAs</span></span>

<span data-ttu-id="cc6ad-488">Blazorle modèle d’application Web progressive (PWA) contient un `service-worker.published.js` fichier suggéré qui est responsable de l’extraction et du stockage des fichiers d’application en vue d’une utilisation hors connexion.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-488">Blazor's Progressive Web Application (PWA) template contains a suggested `service-worker.published.js` file that's responsible for fetching and storing application files for offline use.</span></span> <span data-ttu-id="cc6ad-489">Il s’agit d’un processus distinct du mécanisme normal de démarrage de l’application et d’une logique de vérification de l’intégrité distincte.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-489">This is a separate process from the normal app startup mechanism and has its own separate integrity checking logic.</span></span>

<span data-ttu-id="cc6ad-490">À l’intérieur du `service-worker.published.js` fichier, la ligne suivante est présente :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-490">Inside the `service-worker.published.js` file, following line is present:</span></span>

```javascript
.map(asset => new Request(asset.url, { integrity: asset.hash }));
```

<span data-ttu-id="cc6ad-491">Pour désactiver la vérification de l’intégrité, supprimez le `integrity` paramètre en remplaçant la ligne par ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="cc6ad-491">To disable integrity checking, remove the `integrity` parameter by changing the line to the following:</span></span>

```javascript
.map(asset => new Request(asset.url));
```

<span data-ttu-id="cc6ad-492">Là encore, la désactivation de la vérification de l’intégrité signifie que vous perdez les garanties de sécurité offertes par le contrôle d’intégrité.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-492">Again, disabling integrity checking means that you lose the safety guarantees offered by integrity checking.</span></span> <span data-ttu-id="cc6ad-493">Par exemple, il existe un risque que si le navigateur de l’utilisateur met en cache l’application au moment précis où vous déployez une nouvelle version, il peut mettre en cache certains fichiers de l’ancien déploiement et d’autres du nouveau déploiement.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-493">For example, there is a risk that if the user's browser is caching the app at the exact moment that you deploy a new version, it could cache some files from the old deployment and some from the new deployment.</span></span> <span data-ttu-id="cc6ad-494">Si cela se produit, l’application est bloquée dans un état endommagé jusqu’à ce que vous déployiez une autre mise à jour.</span><span class="sxs-lookup"><span data-stu-id="cc6ad-494">If that happens, the app becomes stuck in a broken state until you deploy a further update.</span></span>
