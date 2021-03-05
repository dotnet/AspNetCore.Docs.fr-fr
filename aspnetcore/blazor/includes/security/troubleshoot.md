---
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
ms.openlocfilehash: eefd16dca126c9bce1efa2cdc90d4201bac09b91
ms.sourcegitcommit: 3982ff9dabb5b12aeb0a61cde2686b5253364f5d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102193659"
---
## <a name="troubleshoot"></a><span data-ttu-id="126ad-101">Dépanner</span><span class="sxs-lookup"><span data-stu-id="126ad-101">Troubleshoot</span></span>

### <a name="common-errors"></a><span data-ttu-id="126ad-102">Erreurs courantes</span><span class="sxs-lookup"><span data-stu-id="126ad-102">Common errors</span></span>

* <span data-ttu-id="126ad-103">Inconfiguration de l’application ou du Identity fournisseur (IP)</span><span class="sxs-lookup"><span data-stu-id="126ad-103">Misconfiguration of the app or Identity Provider (IP)</span></span>

  <span data-ttu-id="126ad-104">Les erreurs les plus courantes sont provoquées par une configuration incorrecte.</span><span class="sxs-lookup"><span data-stu-id="126ad-104">The most common errors are caused by incorrect configuration.</span></span> <span data-ttu-id="126ad-105">Voici quelques exemples :</span><span class="sxs-lookup"><span data-stu-id="126ad-105">The following are a few examples:</span></span>
  
  * <span data-ttu-id="126ad-106">Selon les exigences du scénario, une autorité, une instance, un ID de locataire, un domaine de locataire, un ID de client ou un URI de redirection manquant ou incorrect empêche une application d’authentifier des clients.</span><span class="sxs-lookup"><span data-stu-id="126ad-106">Depending on the requirements of the scenario, a missing or incorrect Authority, Instance, Tenant ID, Tenant domain, Client ID, or Redirect URI prevents an app from authenticating clients.</span></span>
  * <span data-ttu-id="126ad-107">Une étendue de jeton d’accès incorrecte empêche les clients d’accéder aux points de terminaison de l’API Web du serveur.</span><span class="sxs-lookup"><span data-stu-id="126ad-107">An incorrect access token scope prevents clients from accessing server web API endpoints.</span></span>
  * <span data-ttu-id="126ad-108">Les autorisations d’API serveur incorrectes ou manquantes empêchent les clients d’accéder aux points de terminaison de l’API Web du serveur.</span><span class="sxs-lookup"><span data-stu-id="126ad-108">Incorrect or missing server API permissions prevent clients from accessing server web API endpoints.</span></span>
  
  <span data-ttu-id="126ad-109">Les sections de configuration de cet article montrent des exemples de configuration correcte.</span><span class="sxs-lookup"><span data-stu-id="126ad-109">Configuration sections of this article's guidance show examples of the correct configuration.</span></span> <span data-ttu-id="126ad-110">Vérifiez attentivement chaque section de l’article à la recherche de la configuration de l’application et de l’adresse IP.</span><span class="sxs-lookup"><span data-stu-id="126ad-110">Carefully check each section of the article looking for app and IP misconfiguration.</span></span>
  
  <span data-ttu-id="126ad-111">Si la configuration semble correcte :</span><span class="sxs-lookup"><span data-stu-id="126ad-111">If the configuration appears correct:</span></span>
  
  * <span data-ttu-id="126ad-112">Analyser les journaux des applications.</span><span class="sxs-lookup"><span data-stu-id="126ad-112">Analyze application logs.</span></span>
  * <span data-ttu-id="126ad-113">Examinez le trafic réseau entre l’application cliente et l’application IP ou serveur avec les outils de développement du navigateur.</span><span class="sxs-lookup"><span data-stu-id="126ad-113">Examine the network traffic between the client app and the IP or server app with the browser's developer tools.</span></span> <span data-ttu-id="126ad-114">Souvent, un message d’erreur exact ou un message indiquant ce qui est à l’origine du problème est renvoyé au client par l’application IP ou serveur après avoir effectué une demande.</span><span class="sxs-lookup"><span data-stu-id="126ad-114">Often, an exact error message or a message with a clue to what's causing the problem is returned to the client by the IP or server app after making a request.</span></span> <span data-ttu-id="126ad-115">Des conseils sur les outils de développement sont disponibles dans les articles suivants :</span><span class="sxs-lookup"><span data-stu-id="126ad-115">Developer tools guidance is found in the following articles:</span></span>

    * <span data-ttu-id="126ad-116">[Google Chrome](https://developers.google.com/web/tools/chrome-devtools/network) (documentation Google)</span><span class="sxs-lookup"><span data-stu-id="126ad-116">[Google Chrome](https://developers.google.com/web/tools/chrome-devtools/network) (Google documentation)</span></span>
    * [<span data-ttu-id="126ad-117">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="126ad-117">Microsoft Edge</span></span>](/microsoft-edge/devtools-guide-chromium/network/)
    * <span data-ttu-id="126ad-118">[Mozilla Firefox](https://developer.mozilla.org/docs/Tools/Network_Monitor) (documentation Mozilla)</span><span class="sxs-lookup"><span data-stu-id="126ad-118">[Mozilla Firefox](https://developer.mozilla.org/docs/Tools/Network_Monitor) (Mozilla documentation)</span></span>

  * <span data-ttu-id="126ad-119">Décoder le contenu d’un JSON Web Token (JWT) utilisé pour l’authentification d’un client ou l’accès à une API Web de serveur, en fonction de l’endroit où le problème se produit.</span><span class="sxs-lookup"><span data-stu-id="126ad-119">Decode the contents of a JSON Web Token (JWT) used for authenticating a client or accessing a server web API, depending on where the problem is occurring.</span></span> <span data-ttu-id="126ad-120">Pour plus d’informations, consultez [inspecter le contenu d’un JSON Web Token (JWT)](#inspect-the-content-of-a-json-web-token-jwt).</span><span class="sxs-lookup"><span data-stu-id="126ad-120">For more information, see [Inspect the content of a JSON Web Token (JWT)](#inspect-the-content-of-a-json-web-token-jwt).</span></span>
  
  <span data-ttu-id="126ad-121">L’équipe documentation répond aux commentaires sur les documents et aux bogues dans les articles (en ouvrant un problème de la section commentaires sur **cette page** ), mais ne peut pas fournir de support technique.</span><span class="sxs-lookup"><span data-stu-id="126ad-121">The documenation team responds to document feedback and bugs in articles (open an issue from the **This page** feedback section) but is unable to provide product support.</span></span> <span data-ttu-id="126ad-122">Plusieurs forums de support publics sont disponibles pour vous aider à résoudre les problèmes d’une application.</span><span class="sxs-lookup"><span data-stu-id="126ad-122">Several public support forums are available to assist with troubleshooting an app.</span></span> <span data-ttu-id="126ad-123">Nous recommandons ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="126ad-123">We recommend the following:</span></span>
  
  * [<span data-ttu-id="126ad-124">Stack Overflow (étiquette : `blazor` )</span><span class="sxs-lookup"><span data-stu-id="126ad-124">Stack Overflow (tag: `blazor`)</span></span>](https://stackoverflow.com/questions/tagged/blazor)
  * [<span data-ttu-id="126ad-125">Équipe de marge ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="126ad-125">ASP.NET Core Slack Team</span></span>](http://tattoocoder.com/aspnet-slack-sign-up/)
  * <span data-ttu-id="126ad-126">[Blazor Gitter](https://gitter.im/aspnet/Blazor)</span><span class="sxs-lookup"><span data-stu-id="126ad-126">[Blazor Gitter](https://gitter.im/aspnet/Blazor)</span></span>
  
  <span data-ttu-id="126ad-127">Pour les rapports de bogues d’infrastructure reproductibles non-sécurité, non sensibles et non confidentiels, [ouvrez un problème avec l’unité de produit ASP.net Core](https://github.com/dotnet/aspnetcore/issues).</span><span class="sxs-lookup"><span data-stu-id="126ad-127">For non-security, non-sensitive, and non-confidential reproducible framework bug reports, [open an issue with the ASP.NET Core product unit](https://github.com/dotnet/aspnetcore/issues).</span></span> <span data-ttu-id="126ad-128">N’ouvrez pas de problème avec l’unité de produit tant que vous n’avez pas soigneusement étudié la cause d’un problème et que vous ne pouvez pas le résoudre par vous-même et avec l’aide de la communauté sur un forum de support public.</span><span class="sxs-lookup"><span data-stu-id="126ad-128">Don't open an issue with the product unit until you've thoroughly investigated the cause of a problem and can't resolve it on your own and with the help of the community on a public support forum.</span></span> <span data-ttu-id="126ad-129">L’unité de produit n’est pas en mesure de dépanner des applications individuelles endommagées en raison d’une configuration simple ou de cas d’utilisation impliquant des services tiers.</span><span class="sxs-lookup"><span data-stu-id="126ad-129">The product unit isn't able to troubleshoot individual apps that are broken due to simple misconfiguration or use cases involving third-party services.</span></span> <span data-ttu-id="126ad-130">Si un rapport est sensible ou confidentiel par nature ou décrit une faille de sécurité potentielle dans le produit que des attaquants peuvent exploiter, consultez Rapports sur les [problèmes de sécurité et les bogues (référentiel GitHub dotnet/aspnetcore)](https://github.com/dotnet/aspnetcore/blob/main/CONTRIBUTING.md#reporting-security-issues-and-bugs).</span><span class="sxs-lookup"><span data-stu-id="126ad-130">If a report is sensitive or confidential in nature or describes a potential security flaw in the product that attackers may exploit, see [Reporting security issues and bugs (dotnet/aspnetcore GitHub repository)](https://github.com/dotnet/aspnetcore/blob/main/CONTRIBUTING.md#reporting-security-issues-and-bugs).</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="126ad-131">Client non autorisé pour AAD</span><span class="sxs-lookup"><span data-stu-id="126ad-131">Unauthorized client for AAD</span></span>

  > <span data-ttu-id="126ad-132">info : échec de l’autorisation Microsoft. AspNetCore. Authorization. DefaultAuthorizationService [2].</span><span class="sxs-lookup"><span data-stu-id="126ad-132">info: Microsoft.AspNetCore.Authorization.DefaultAuthorizationService[2] Authorization failed.</span></span> <span data-ttu-id="126ad-133">Ces exigences n’ont pas été satisfaites : DenyAnonymousAuthorizationRequirement : requiert un utilisateur authentifié.</span><span class="sxs-lookup"><span data-stu-id="126ad-133">These requirements were not met: DenyAnonymousAuthorizationRequirement: Requires an authenticated user.</span></span>

  <span data-ttu-id="126ad-134">Erreur de rappel de connexion à partir d’AAD :</span><span class="sxs-lookup"><span data-stu-id="126ad-134">Login callback error from AAD:</span></span>

  * <span data-ttu-id="126ad-135">Erreur : `unauthorized_client`</span><span class="sxs-lookup"><span data-stu-id="126ad-135">Error: `unauthorized_client`</span></span>
  * <span data-ttu-id="126ad-136">Description : `AADB2C90058: The provided application is not configured to allow public clients.`</span><span class="sxs-lookup"><span data-stu-id="126ad-136">Description: `AADB2C90058: The provided application is not configured to allow public clients.`</span></span>

  <span data-ttu-id="126ad-137">Pour résoudre l’erreur :</span><span class="sxs-lookup"><span data-stu-id="126ad-137">To resolve the error:</span></span>

  1. <span data-ttu-id="126ad-138">Dans le Portail Azure, accédez au [manifeste de l’application](/azure/active-directory/develop/reference-app-manifest).</span><span class="sxs-lookup"><span data-stu-id="126ad-138">In the Azure portal, access the [app's manifest](/azure/active-directory/develop/reference-app-manifest).</span></span>
  1. <span data-ttu-id="126ad-139">Affectez à l' [ `allowPublicClient` attribut](/azure/active-directory/develop/reference-app-manifest#allowpublicclient-attribute) la valeur `null` ou `true` .</span><span class="sxs-lookup"><span data-stu-id="126ad-139">Set the [`allowPublicClient` attribute](/azure/active-directory/develop/reference-app-manifest#allowpublicclient-attribute) to `null` or `true`.</span></span>

::: moniker-end

### <a name="cookies-and-site-data"></a><span data-ttu-id="126ad-140">Cookies et données de site</span><span class="sxs-lookup"><span data-stu-id="126ad-140">Cookies and site data</span></span>

<span data-ttu-id="126ad-141">Cookieles s et les données de site peuvent être conservées entre les mises à jour d’application et interfèrent avec les tests et la résolution des problèmes.</span><span class="sxs-lookup"><span data-stu-id="126ad-141">Cookies and site data can persist across app updates and interfere with testing and troubleshooting.</span></span> <span data-ttu-id="126ad-142">Désactivez les éléments suivants lorsque vous apportez des modifications de code d’application, des modifications de compte d’utilisateur avec le fournisseur ou des modifications de configuration d’application de fournisseur :</span><span class="sxs-lookup"><span data-stu-id="126ad-142">Clear the following when making app code changes, user account changes with the provider, or provider app configuration changes:</span></span>

* <span data-ttu-id="126ad-143">Connexion cookie de l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="126ad-143">User sign-in cookies</span></span>
* <span data-ttu-id="126ad-144">Application cookie s</span><span class="sxs-lookup"><span data-stu-id="126ad-144">App cookies</span></span>
* <span data-ttu-id="126ad-145">Données de site mises en cache et stockées</span><span class="sxs-lookup"><span data-stu-id="126ad-145">Cached and stored site data</span></span>

<span data-ttu-id="126ad-146">L’une des méthodes permettant d’empêcher les éléments en attente cookie et les données de site d’interférer avec les tests et la résolution des problèmes consiste à :</span><span class="sxs-lookup"><span data-stu-id="126ad-146">One approach to prevent lingering cookies and site data from interfering with testing and troubleshooting is to:</span></span>

* <span data-ttu-id="126ad-147">Configurer un navigateur</span><span class="sxs-lookup"><span data-stu-id="126ad-147">Configure a browser</span></span>
  * <span data-ttu-id="126ad-148">Utilisez un navigateur pour le test que vous pouvez configurer pour supprimer toutes les cookie données de site et chaque fois que le navigateur est fermé.</span><span class="sxs-lookup"><span data-stu-id="126ad-148">Use a browser for testing that you can configure to delete all cookie and site data each time the browser is closed.</span></span>
  * <span data-ttu-id="126ad-149">Assurez-vous que le navigateur est fermé manuellement ou par l’IDE pour toute modification apportée à l’application, à l’utilisateur de test ou à la configuration du fournisseur.</span><span class="sxs-lookup"><span data-stu-id="126ad-149">Make sure that the browser is closed manually or by the IDE for any change to the app, test user, or provider configuration.</span></span>
* <span data-ttu-id="126ad-150">Utilisez une commande personnalisée pour ouvrir un navigateur en mode Incognito ou privé dans Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="126ad-150">Use a custom command to open a browser in incognito or private mode in Visual Studio:</span></span>
  * <span data-ttu-id="126ad-151">Ouvrez la boîte de dialogue **naviguer avec** à partir du bouton **exécuter** de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="126ad-151">Open **Browse With** dialog box from Visual Studio's **Run** button.</span></span>
  * <span data-ttu-id="126ad-152">Sélectionnez le bouton **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="126ad-152">Select the **Add** button.</span></span>
  * <span data-ttu-id="126ad-153">Indiquez le chemin d’accès à votre navigateur dans le champ **programme** .</span><span class="sxs-lookup"><span data-stu-id="126ad-153">Provide the path to your browser in the **Program** field.</span></span> <span data-ttu-id="126ad-154">Les chemins d’accès exécutables suivants sont des emplacements d’installation typiques pour Windows 10.</span><span class="sxs-lookup"><span data-stu-id="126ad-154">The following executable paths are typical installation locations for Windows 10.</span></span> <span data-ttu-id="126ad-155">Si votre navigateur est installé à un autre emplacement ou si vous n’utilisez pas Windows 10, indiquez le chemin d’accès à l’exécutable du navigateur.</span><span class="sxs-lookup"><span data-stu-id="126ad-155">If your browser is installed in a different location or you aren't using Windows 10, provide the path to the browser's executable.</span></span>
    * <span data-ttu-id="126ad-156">Microsoft Edge : `C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`</span><span class="sxs-lookup"><span data-stu-id="126ad-156">Microsoft Edge: `C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`</span></span>
    * <span data-ttu-id="126ad-157">Google Chrome : `C:\Program Files (x86)\Google\Chrome\Application\chrome.exe`</span><span class="sxs-lookup"><span data-stu-id="126ad-157">Google Chrome: `C:\Program Files (x86)\Google\Chrome\Application\chrome.exe`</span></span>
    * <span data-ttu-id="126ad-158">Mozilla Firefox : `C:\Program Files\Mozilla Firefox\firefox.exe`</span><span class="sxs-lookup"><span data-stu-id="126ad-158">Mozilla Firefox: `C:\Program Files\Mozilla Firefox\firefox.exe`</span></span>
  * <span data-ttu-id="126ad-159">Dans le champ **arguments** , indiquez l’option de ligne de commande utilisée par le navigateur pour ouvrir en mode Incognito ou privé.</span><span class="sxs-lookup"><span data-stu-id="126ad-159">In the **Arguments** field, provide the command-line option that the browser uses to open in incognito or private mode.</span></span> <span data-ttu-id="126ad-160">Certains navigateurs requièrent l’URL de l’application.</span><span class="sxs-lookup"><span data-stu-id="126ad-160">Some browsers require the URL of the app.</span></span>
    * <span data-ttu-id="126ad-161">Microsoft Edge : utilisez `-inprivate` .</span><span class="sxs-lookup"><span data-stu-id="126ad-161">Microsoft Edge: Use `-inprivate`.</span></span>
    * <span data-ttu-id="126ad-162">Google Chrome : utilisez `--incognito --new-window {URL}` , où l’espace réservé `{URL}` est l’URL à ouvrir (par exemple, `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="126ad-162">Google Chrome: Use `--incognito --new-window {URL}`, where the placeholder `{URL}` is the URL to open (for example, `https://localhost:5001`).</span></span>
    * <span data-ttu-id="126ad-163">Mozilla Firefox : utilisez `-private -url {URL}` , où l’espace réservé `{URL}` est l’URL à ouvrir (par exemple, `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="126ad-163">Mozilla Firefox: Use `-private -url {URL}`, where the placeholder `{URL}` is the URL to open (for example, `https://localhost:5001`).</span></span>
  * <span data-ttu-id="126ad-164">Entrez un nom dans le champ **nom convivial** .</span><span class="sxs-lookup"><span data-stu-id="126ad-164">Provide a name in the **Friendly name** field.</span></span> <span data-ttu-id="126ad-165">Par exemple : `Firefox Auth Testing`.</span><span class="sxs-lookup"><span data-stu-id="126ad-165">For example, `Firefox Auth Testing`.</span></span>
  * <span data-ttu-id="126ad-166">Cliquez sur le bouton **OK**.</span><span class="sxs-lookup"><span data-stu-id="126ad-166">Select the **OK** button.</span></span>
  * <span data-ttu-id="126ad-167">Pour éviter d’avoir à sélectionner le profil de navigateur pour chaque itération de test avec une application, définissez le profil par défaut avec le bouton **définir comme valeur par défaut** .</span><span class="sxs-lookup"><span data-stu-id="126ad-167">To avoid having to select the browser profile for each iteration of testing with an app, set the profile as the default with the **Set as Default** button.</span></span>
  * <span data-ttu-id="126ad-168">Assurez-vous que le navigateur est fermé par l’IDE pour toute modification apportée à l’application, à l’utilisateur de test ou à la configuration du fournisseur.</span><span class="sxs-lookup"><span data-stu-id="126ad-168">Make sure that the browser is closed by the IDE for any change to the app, test user, or provider configuration.</span></span>

### <a name="app-upgrades"></a><span data-ttu-id="126ad-169">Mise à niveau d’applications</span><span class="sxs-lookup"><span data-stu-id="126ad-169">App upgrades</span></span>

<span data-ttu-id="126ad-170">Une application fonctionnelle peut échouer immédiatement après la mise à niveau de la kit SDK .NET Core sur l’ordinateur de développement ou la modification des versions de package dans l’application.</span><span class="sxs-lookup"><span data-stu-id="126ad-170">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="126ad-171">Dans certains cas, les packages incohérents peuvent bloquer une application quand vous effectuez des mises à niveau majeures.</span><span class="sxs-lookup"><span data-stu-id="126ad-171">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="126ad-172">Vous pouvez résoudre la plupart de ces problèmes en suivant les instructions suivantes :</span><span class="sxs-lookup"><span data-stu-id="126ad-172">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="126ad-173">Effacez les caches de package NuGet du système local en exécutant [`dotnet nuget locals all --clear`](/dotnet/core/tools/dotnet-nuget-locals) à partir d’une interface de commande.</span><span class="sxs-lookup"><span data-stu-id="126ad-173">Clear the local system's NuGet package caches by executing [`dotnet nuget locals all --clear`](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>
1. <span data-ttu-id="126ad-174">Supprimez les `bin` dossiers et du projet `obj` .</span><span class="sxs-lookup"><span data-stu-id="126ad-174">Delete the project's `bin` and `obj` folders.</span></span>
1. <span data-ttu-id="126ad-175">Restaurez et regénérez le projet.</span><span class="sxs-lookup"><span data-stu-id="126ad-175">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="126ad-176">Supprimez tous les fichiers du dossier de déploiement sur le serveur avant de redéployer l’application.</span><span class="sxs-lookup"><span data-stu-id="126ad-176">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

> [!NOTE]
> <span data-ttu-id="126ad-177">L’utilisation de versions de package incompatibles avec le Framework cible de l’application n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="126ad-177">Use of package versions incompatible with the app's target framework isn't supported.</span></span> <span data-ttu-id="126ad-178">Pour plus d’informations sur un package, utilisez la [galerie NuGet](https://www.nuget.org) ou l' [Explorateur de packages FuGet](https://www.fuget.org).</span><span class="sxs-lookup"><span data-stu-id="126ad-178">For information on a package, use the [NuGet Gallery](https://www.nuget.org) or [FuGet Package Explorer](https://www.fuget.org).</span></span>

### <a name="run-the-server-app"></a><span data-ttu-id="126ad-179">Exécuter l’application serveur</span><span class="sxs-lookup"><span data-stu-id="126ad-179">Run the Server app</span></span>

<span data-ttu-id="126ad-180">Lors du test et du dépannage d’une application hébergée, assurez- Blazor vous que vous exécutez l’application à partir du **`Server`** projet.</span><span class="sxs-lookup"><span data-stu-id="126ad-180">When testing and troubleshooting a hosted Blazor app, make sure that you're running the app from the **`Server`** project.</span></span> <span data-ttu-id="126ad-181">Par exemple, dans Visual Studio, vérifiez que le projet serveur est mis en surbrillance dans **Explorateur de solutions** avant de démarrer l’application avec l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="126ad-181">For example in Visual Studio, confirm that the Server project is highlighted in **Solution Explorer** before you start the app with any of the following approaches:</span></span>

* <span data-ttu-id="126ad-182">Sélectionnez le bouton **Run**.</span><span class="sxs-lookup"><span data-stu-id="126ad-182">Select the **Run** button.</span></span>
* <span data-ttu-id="126ad-183">Utilisez **débogage**  >  **Démarrer le débogage** à partir du menu.</span><span class="sxs-lookup"><span data-stu-id="126ad-183">Use **Debug** > **Start Debugging** from the menu.</span></span>
* <span data-ttu-id="126ad-184">Appuyez sur <kbd>F5</kbd>.</span><span class="sxs-lookup"><span data-stu-id="126ad-184">Press <kbd>F5</kbd>.</span></span>

### <a name="inspect-the-content-of-a-json-web-token-jwt"></a><span data-ttu-id="126ad-185">Inspecter le contenu d’un JSON Web Token (JWT)</span><span class="sxs-lookup"><span data-stu-id="126ad-185">Inspect the content of a JSON Web Token (JWT)</span></span>

<span data-ttu-id="126ad-186">Pour décoder un JSON Web Token (JWT), utilisez l’outil [JWT.ms](https://jwt.ms/) de Microsoft.</span><span class="sxs-lookup"><span data-stu-id="126ad-186">To decode a JSON Web Token (JWT), use Microsoft's [jwt.ms](https://jwt.ms/) tool.</span></span> <span data-ttu-id="126ad-187">Les valeurs de l’interface utilisateur ne laissent jamais votre navigateur.</span><span class="sxs-lookup"><span data-stu-id="126ad-187">Values in the UI never leave your browser.</span></span>

<span data-ttu-id="126ad-188">Exemple de JWT encodé (raccourci pour l’affichage) :</span><span class="sxs-lookup"><span data-stu-id="126ad-188">Example encoded JWT (shortened for display):</span></span>

> <span data-ttu-id="126ad-189">eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1j ... bQdHBHGcQQRbW7Wmo6SWYG4V_bU55Ug_PW4pLPr20tTS8Ct7_uwy9DWrzCMzpD-EiwT5IjXwlGX3IXVjHIlX50IVIydBoPQtadvT7saKo1G5Jmutgq41o-dmz6-yBMKV2_nXA25Q</span><span class="sxs-lookup"><span data-stu-id="126ad-189">eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1j ... bQdHBHGcQQRbW7Wmo6SWYG4V_bU55Ug_PW4pLPr20tTS8Ct7_uwy9DWrzCMzpD-EiwT5IjXwlGX3IXVjHIlX50IVIydBoPQtadvT7saKo1G5Jmutgq41o-dmz6-yBMKV2_nXA25Q</span></span>

<span data-ttu-id="126ad-190">Exemple JWT décodé par l’outil pour une application qui s’authentifie auprès d’Azure AAD B2C :</span><span class="sxs-lookup"><span data-stu-id="126ad-190">Example JWT decoded by the tool for an app that authenticates against Azure AAD B2C:</span></span>

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1610059429,
  "nbf": 1610055829,
  "ver": "1.0",
  "iss": "https://mysiteb2c.b2clogin.com/5cc15ea8-a296-4aa3-97e4-226dcc9ad298/v2.0/",
  "sub": "5ee963fb-24d6-4d72-a1b6-889c6e2c7438",
  "aud": "70bde375-fce3-4b82-984a-b247d823a03f",
  "nonce": "b2641f54-8dc4-42ca-97ea-7f12ff4af871",
  "iat": 1610055829,
  "auth_time": 1610055822,
  "idp": "idp.com",
  "tfp": "B2C_1_signupsignin"
}.[Signature]
```
