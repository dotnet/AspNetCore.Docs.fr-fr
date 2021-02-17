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
ms.openlocfilehash: 4e7c0e9b0a164e0181af5d6baaedf0669c1c06aa
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551662"
---
## <a name="troubleshoot"></a><span data-ttu-id="ac32b-101">Dépanner</span><span class="sxs-lookup"><span data-stu-id="ac32b-101">Troubleshoot</span></span>

::: moniker range=">= aspnetcore-5.0"

### <a name="common-errors"></a><span data-ttu-id="ac32b-102">Erreurs courantes</span><span class="sxs-lookup"><span data-stu-id="ac32b-102">Common errors</span></span>

* <span data-ttu-id="ac32b-103">Client non autorisé pour AAD</span><span class="sxs-lookup"><span data-stu-id="ac32b-103">Unauthorized client for AAD</span></span>

  > <span data-ttu-id="ac32b-104">info : échec de l’autorisation Microsoft. AspNetCore. Authorization. DefaultAuthorizationService [2].</span><span class="sxs-lookup"><span data-stu-id="ac32b-104">info: Microsoft.AspNetCore.Authorization.DefaultAuthorizationService[2] Authorization failed.</span></span> <span data-ttu-id="ac32b-105">Ces exigences n’ont pas été satisfaites : DenyAnonymousAuthorizationRequirement : requiert un utilisateur authentifié.</span><span class="sxs-lookup"><span data-stu-id="ac32b-105">These requirements were not met: DenyAnonymousAuthorizationRequirement: Requires an authenticated user.</span></span>

  <span data-ttu-id="ac32b-106">Erreur de rappel de connexion à partir d’AAD :</span><span class="sxs-lookup"><span data-stu-id="ac32b-106">Login callback error from AAD:</span></span>

  * <span data-ttu-id="ac32b-107">Erreur : `unauthorized_client`</span><span class="sxs-lookup"><span data-stu-id="ac32b-107">Error: `unauthorized_client`</span></span>
  * <span data-ttu-id="ac32b-108">Description : `AADB2C90058: The provided application is not configured to allow public clients.`</span><span class="sxs-lookup"><span data-stu-id="ac32b-108">Description: `AADB2C90058: The provided application is not configured to allow public clients.`</span></span>

  <span data-ttu-id="ac32b-109">Pour résoudre l’erreur :</span><span class="sxs-lookup"><span data-stu-id="ac32b-109">To resolve the error:</span></span>

  1. <span data-ttu-id="ac32b-110">Dans le Portail Azure, accédez au [manifeste de l’application](/azure/active-directory/develop/reference-app-manifest).</span><span class="sxs-lookup"><span data-stu-id="ac32b-110">In the Azure portal, access the [app's manifest](/azure/active-directory/develop/reference-app-manifest).</span></span>
  1. <span data-ttu-id="ac32b-111">Affectez à l' [ `allowPublicClient` attribut](/azure/active-directory/develop/reference-app-manifest#allowpublicclient-attribute) la valeur `null` ou `true` .</span><span class="sxs-lookup"><span data-stu-id="ac32b-111">Set the [`allowPublicClient` attribute](/azure/active-directory/develop/reference-app-manifest#allowpublicclient-attribute) to `null` or `true`.</span></span>

::: moniker-end

### <a name="cookies-and-site-data"></a><span data-ttu-id="ac32b-112">Cookies et données de site</span><span class="sxs-lookup"><span data-stu-id="ac32b-112">Cookies and site data</span></span>

<span data-ttu-id="ac32b-113">Cookieles s et les données de site peuvent être conservées entre les mises à jour d’application et interfèrent avec les tests et la résolution des problèmes.</span><span class="sxs-lookup"><span data-stu-id="ac32b-113">Cookies and site data can persist across app updates and interfere with testing and troubleshooting.</span></span> <span data-ttu-id="ac32b-114">Désactivez les éléments suivants lorsque vous apportez des modifications de code d’application, des modifications de compte d’utilisateur avec le fournisseur ou des modifications de configuration d’application de fournisseur :</span><span class="sxs-lookup"><span data-stu-id="ac32b-114">Clear the following when making app code changes, user account changes with the provider, or provider app configuration changes:</span></span>

* <span data-ttu-id="ac32b-115">Connexion cookie de l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="ac32b-115">User sign-in cookies</span></span>
* <span data-ttu-id="ac32b-116">Application cookie s</span><span class="sxs-lookup"><span data-stu-id="ac32b-116">App cookies</span></span>
* <span data-ttu-id="ac32b-117">Données de site mises en cache et stockées</span><span class="sxs-lookup"><span data-stu-id="ac32b-117">Cached and stored site data</span></span>

<span data-ttu-id="ac32b-118">L’une des méthodes permettant d’empêcher les éléments en attente cookie et les données de site d’interférer avec les tests et la résolution des problèmes consiste à :</span><span class="sxs-lookup"><span data-stu-id="ac32b-118">One approach to prevent lingering cookies and site data from interfering with testing and troubleshooting is to:</span></span>

* <span data-ttu-id="ac32b-119">Configurer un navigateur</span><span class="sxs-lookup"><span data-stu-id="ac32b-119">Configure a browser</span></span>
  * <span data-ttu-id="ac32b-120">Utilisez un navigateur pour le test que vous pouvez configurer pour supprimer toutes les cookie données de site et chaque fois que le navigateur est fermé.</span><span class="sxs-lookup"><span data-stu-id="ac32b-120">Use a browser for testing that you can configure to delete all cookie and site data each time the browser is closed.</span></span>
  * <span data-ttu-id="ac32b-121">Assurez-vous que le navigateur est fermé manuellement ou par l’IDE pour toute modification apportée à l’application, à l’utilisateur de test ou à la configuration du fournisseur.</span><span class="sxs-lookup"><span data-stu-id="ac32b-121">Make sure that the browser is closed manually or by the IDE for any change to the app, test user, or provider configuration.</span></span>
* <span data-ttu-id="ac32b-122">Utilisez une commande personnalisée pour ouvrir un navigateur en mode Incognito ou privé dans Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="ac32b-122">Use a custom command to open a browser in incognito or private mode in Visual Studio:</span></span>
  * <span data-ttu-id="ac32b-123">Ouvrez la boîte de dialogue **naviguer avec** à partir du bouton **exécuter** de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ac32b-123">Open **Browse With** dialog box from Visual Studio's **Run** button.</span></span>
  * <span data-ttu-id="ac32b-124">Sélectionnez le bouton **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="ac32b-124">Select the **Add** button.</span></span>
  * <span data-ttu-id="ac32b-125">Indiquez le chemin d’accès à votre navigateur dans le champ **programme** .</span><span class="sxs-lookup"><span data-stu-id="ac32b-125">Provide the path to your browser in the **Program** field.</span></span> <span data-ttu-id="ac32b-126">Les chemins d’accès exécutables suivants sont des emplacements d’installation typiques pour Windows 10.</span><span class="sxs-lookup"><span data-stu-id="ac32b-126">The following executable paths are typical installation locations for Windows 10.</span></span> <span data-ttu-id="ac32b-127">Si votre navigateur est installé à un autre emplacement ou si vous n’utilisez pas Windows 10, indiquez le chemin d’accès à l’exécutable du navigateur.</span><span class="sxs-lookup"><span data-stu-id="ac32b-127">If your browser is installed in a different location or you aren't using Windows 10, provide the path to the browser's executable.</span></span>
    * <span data-ttu-id="ac32b-128">Microsoft Edge : `C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`</span><span class="sxs-lookup"><span data-stu-id="ac32b-128">Microsoft Edge: `C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`</span></span>
    * <span data-ttu-id="ac32b-129">Google Chrome : `C:\Program Files (x86)\Google\Chrome\Application\chrome.exe`</span><span class="sxs-lookup"><span data-stu-id="ac32b-129">Google Chrome: `C:\Program Files (x86)\Google\Chrome\Application\chrome.exe`</span></span>
    * <span data-ttu-id="ac32b-130">Mozilla Firefox : `C:\Program Files\Mozilla Firefox\firefox.exe`</span><span class="sxs-lookup"><span data-stu-id="ac32b-130">Mozilla Firefox: `C:\Program Files\Mozilla Firefox\firefox.exe`</span></span>
  * <span data-ttu-id="ac32b-131">Dans le champ **arguments** , indiquez l’option de ligne de commande utilisée par le navigateur pour ouvrir en mode Incognito ou privé.</span><span class="sxs-lookup"><span data-stu-id="ac32b-131">In the **Arguments** field, provide the command-line option that the browser uses to open in incognito or private mode.</span></span> <span data-ttu-id="ac32b-132">Certains navigateurs requièrent l’URL de l’application.</span><span class="sxs-lookup"><span data-stu-id="ac32b-132">Some browsers require the URL of the app.</span></span>
    * <span data-ttu-id="ac32b-133">Microsoft Edge : utilisez `-inprivate` .</span><span class="sxs-lookup"><span data-stu-id="ac32b-133">Microsoft Edge: Use `-inprivate`.</span></span>
    * <span data-ttu-id="ac32b-134">Google Chrome : utilisez `--incognito --new-window {URL}` , où l’espace réservé `{URL}` est l’URL à ouvrir (par exemple, `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="ac32b-134">Google Chrome: Use `--incognito --new-window {URL}`, where the placeholder `{URL}` is the URL to open (for example, `https://localhost:5001`).</span></span>
    * <span data-ttu-id="ac32b-135">Mozilla Firefox : utilisez `-private -url {URL}` , où l’espace réservé `{URL}` est l’URL à ouvrir (par exemple, `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="ac32b-135">Mozilla Firefox: Use `-private -url {URL}`, where the placeholder `{URL}` is the URL to open (for example, `https://localhost:5001`).</span></span>
  * <span data-ttu-id="ac32b-136">Entrez un nom dans le champ **nom convivial** .</span><span class="sxs-lookup"><span data-stu-id="ac32b-136">Provide a name in the **Friendly name** field.</span></span> <span data-ttu-id="ac32b-137">Par exemple : `Firefox Auth Testing`.</span><span class="sxs-lookup"><span data-stu-id="ac32b-137">For example, `Firefox Auth Testing`.</span></span>
  * <span data-ttu-id="ac32b-138">Cliquez sur le bouton **OK**.</span><span class="sxs-lookup"><span data-stu-id="ac32b-138">Select the **OK** button.</span></span>
  * <span data-ttu-id="ac32b-139">Pour éviter d’avoir à sélectionner le profil de navigateur pour chaque itération de test avec une application, définissez le profil par défaut avec le bouton **définir comme valeur par défaut** .</span><span class="sxs-lookup"><span data-stu-id="ac32b-139">To avoid having to select the browser profile for each iteration of testing with an app, set the profile as the default with the **Set as Default** button.</span></span>
  * <span data-ttu-id="ac32b-140">Assurez-vous que le navigateur est fermé par l’IDE pour toute modification apportée à l’application, à l’utilisateur de test ou à la configuration du fournisseur.</span><span class="sxs-lookup"><span data-stu-id="ac32b-140">Make sure that the browser is closed by the IDE for any change to the app, test user, or provider configuration.</span></span>

### <a name="run-the-server-app"></a><span data-ttu-id="ac32b-141">Exécuter l’application serveur</span><span class="sxs-lookup"><span data-stu-id="ac32b-141">Run the Server app</span></span>

<span data-ttu-id="ac32b-142">Lors du test et du dépannage d’une application hébergée, assurez- Blazor vous que vous exécutez l’application à partir du **`Server`** projet.</span><span class="sxs-lookup"><span data-stu-id="ac32b-142">When testing and troubleshooting a hosted Blazor app, make sure that you're running the app from the **`Server`** project.</span></span> <span data-ttu-id="ac32b-143">Par exemple, dans Visual Studio, vérifiez que le projet serveur est mis en surbrillance dans **Explorateur de solutions** avant de démarrer l’application avec l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="ac32b-143">For example in Visual Studio, confirm that the Server project is highlighted in **Solution Explorer** before you start the app with any of the following approaches:</span></span>

* <span data-ttu-id="ac32b-144">Sélectionnez le bouton **Run**.</span><span class="sxs-lookup"><span data-stu-id="ac32b-144">Select the **Run** button.</span></span>
* <span data-ttu-id="ac32b-145">Utilisez **débogage**  >  **Démarrer le débogage** à partir du menu.</span><span class="sxs-lookup"><span data-stu-id="ac32b-145">Use **Debug** > **Start Debugging** from the menu.</span></span>
* <span data-ttu-id="ac32b-146">Appuyez sur <kbd>F5</kbd>.</span><span class="sxs-lookup"><span data-stu-id="ac32b-146">Press <kbd>F5</kbd>.</span></span>

### <a name="inspect-the-content-of-a-json-web-token-jwt"></a><span data-ttu-id="ac32b-147">Inspecter le contenu d’un JSON Web Token (JWT)</span><span class="sxs-lookup"><span data-stu-id="ac32b-147">Inspect the content of a JSON Web Token (JWT)</span></span>

<span data-ttu-id="ac32b-148">Pour décoder un JSON Web Token (JWT), utilisez l’outil [JWT.ms](https://jwt.ms/) de Microsoft.</span><span class="sxs-lookup"><span data-stu-id="ac32b-148">To decode a JSON Web Token (JWT), use Microsoft's [jwt.ms](https://jwt.ms/) tool.</span></span> <span data-ttu-id="ac32b-149">Les valeurs de l’interface utilisateur ne laissent jamais votre navigateur.</span><span class="sxs-lookup"><span data-stu-id="ac32b-149">Values in the UI never leave your browser.</span></span>
