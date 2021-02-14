---
title: Héberger et déployer des ASP.NET Core Blazor
author: guardrex
description: Découvrez comment héberger et déployer des Blazor applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2020
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
uid: blazor/host-and-deploy/index
ms.openlocfilehash: e7bc44b396b46e2ac3e0279520c7cc8ea6679f5a
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279769"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a><span data-ttu-id="07992-103">Héberger et déployer des ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="07992-103">Host and deploy ASP.NET Core Blazor</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="07992-104">Publier l’application</span><span class="sxs-lookup"><span data-stu-id="07992-104">Publish the app</span></span>

<span data-ttu-id="07992-105">Les applications sont publiées pour le déploiement dans la configuration Release.</span><span class="sxs-lookup"><span data-stu-id="07992-105">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="07992-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07992-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="07992-107">Sélectionnez **générer**  >  **publier {application}** dans la barre de navigation.</span><span class="sxs-lookup"><span data-stu-id="07992-107">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="07992-108">Sélectionnez l’onglet *Cible de publication*.</span><span class="sxs-lookup"><span data-stu-id="07992-108">Select the *publish target*.</span></span> <span data-ttu-id="07992-109">Pour publier localement, sélectionnez **Dossier**.</span><span class="sxs-lookup"><span data-stu-id="07992-109">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="07992-110">Acceptez l’emplacement par défaut dans le champ **Choisir un dossier** ou spécifiez un autre emplacement.</span><span class="sxs-lookup"><span data-stu-id="07992-110">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="07992-111">Sélectionner le bouton **`Publish`**.</span><span class="sxs-lookup"><span data-stu-id="07992-111">Select the **`Publish`** button.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="07992-112">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="07992-112">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="07992-113">Sélectionnez **générer**  >  **publier dans le dossier**.</span><span class="sxs-lookup"><span data-stu-id="07992-113">Select **Build** > **Publish to Folder**.</span></span>
1. <span data-ttu-id="07992-114">Confirmez le dossier pour recevoir les ressources publiées et sélectionnez **`Publish`** .</span><span class="sxs-lookup"><span data-stu-id="07992-114">Confirm the folder to receive the published assets and select **`Publish`**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="07992-115">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="07992-115">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="07992-116">Utilisez la [`dotnet publish`](/dotnet/core/tools/dotnet-publish) commande pour publier l’application avec une configuration Release :</span><span class="sxs-lookup"><span data-stu-id="07992-116">Use the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```dotnetcli
dotnet publish -c Release
```

---

<span data-ttu-id="07992-117">La publication de l’application déclenche une [restauration](/dotnet/core/tools/dotnet-restore) des dépendances du projet et [crée](/dotnet/core/tools/dotnet-build) le projet avant de créer les ressources pour le déploiement.</span><span class="sxs-lookup"><span data-stu-id="07992-117">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="07992-118">Dans le cadre du processus de génération, les assemblys et méthodes inutilisés sont supprimés pour réduire la durée du chargement et la taille du téléchargement de l’application.</span><span class="sxs-lookup"><span data-stu-id="07992-118">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="07992-119">Emplacements de publication :</span><span class="sxs-lookup"><span data-stu-id="07992-119">Publish locations:</span></span>

* Blazor WebAssembly
  * <span data-ttu-id="07992-120">Autonome : l’application est publiée dans le `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` dossier.</span><span class="sxs-lookup"><span data-stu-id="07992-120">Standalone: The app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span> <span data-ttu-id="07992-121">Pour déployer l’application en tant que site statique, copiez le contenu du `wwwroot` dossier sur l’hôte de site statique.</span><span class="sxs-lookup"><span data-stu-id="07992-121">To deploy the app as a static site, copy the contents of the `wwwroot` folder to the static site host.</span></span>
  * <span data-ttu-id="07992-122">Hébergé : l’application cliente Blazor WebAssembly est publiée dans le `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` dossier de l’application serveur, ainsi que les autres ressources Web statiques de l’application serveur.</span><span class="sxs-lookup"><span data-stu-id="07992-122">Hosted: The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="07992-123">Déployez le contenu du `publish` dossier sur l’hôte.</span><span class="sxs-lookup"><span data-stu-id="07992-123">Deploy the contents of the `publish` folder to the host.</span></span>
* <span data-ttu-id="07992-124">Blazor Server: L’application est publiée dans le `/bin/Release/{TARGET FRAMEWORK}/publish` dossier.</span><span class="sxs-lookup"><span data-stu-id="07992-124">Blazor Server: The app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="07992-125">Déployez le contenu du `publish` dossier sur l’hôte.</span><span class="sxs-lookup"><span data-stu-id="07992-125">Deploy the contents of the `publish` folder to the host.</span></span>

<span data-ttu-id="07992-126">Les ressources du dossier sont déployées sur le serveur web.</span><span class="sxs-lookup"><span data-stu-id="07992-126">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="07992-127">Le déploiement peut être un processus manuel ou automatisé, en fonction des outils de développement utilisés.</span><span class="sxs-lookup"><span data-stu-id="07992-127">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="07992-128">Chemin de base de l’application</span><span class="sxs-lookup"><span data-stu-id="07992-128">App base path</span></span>

<span data-ttu-id="07992-129">Le *chemin d’accès de base* de l’application est le chemin de l’URL racine de l’application.</span><span class="sxs-lookup"><span data-stu-id="07992-129">The *app base path* is the app's root URL path.</span></span> <span data-ttu-id="07992-130">Considérez les éléments suivants ASP.NET Core application et Blazor sous-application :</span><span class="sxs-lookup"><span data-stu-id="07992-130">Consider the following ASP.NET Core app and Blazor sub-app:</span></span>

* <span data-ttu-id="07992-131">L’application ASP.NET Core est nommée `MyApp` :</span><span class="sxs-lookup"><span data-stu-id="07992-131">The ASP.NET Core app is named `MyApp`:</span></span>
  * <span data-ttu-id="07992-132">L’application réside physiquement à l’adresse `d:/MyApp` .</span><span class="sxs-lookup"><span data-stu-id="07992-132">The app physically resides at `d:/MyApp`.</span></span>
  * <span data-ttu-id="07992-133">Les demandes sont reçues à l’adresse `https://www.contoso.com/{MYAPP RESOURCE}` .</span><span class="sxs-lookup"><span data-stu-id="07992-133">Requests are received at `https://www.contoso.com/{MYAPP RESOURCE}`.</span></span>
* <span data-ttu-id="07992-134">Une Blazor application nommée `CoolApp` est une sous-application de `MyApp` :</span><span class="sxs-lookup"><span data-stu-id="07992-134">A Blazor app named `CoolApp` is a sub-app of `MyApp`:</span></span>
  * <span data-ttu-id="07992-135">La sous-application réside physiquement à l’adresse `d:/MyApp/CoolApp` .</span><span class="sxs-lookup"><span data-stu-id="07992-135">The sub-app physically resides at `d:/MyApp/CoolApp`.</span></span>
  * <span data-ttu-id="07992-136">Les demandes sont reçues à l’adresse `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}` .</span><span class="sxs-lookup"><span data-stu-id="07992-136">Requests are received at `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span></span>

<span data-ttu-id="07992-137">Si vous ne spécifiez pas de configuration supplémentaire pour `CoolApp` , la sous-application dans ce scénario n’a aucune connaissance de son emplacement sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="07992-137">Without specifying additional configuration for `CoolApp`, the sub-app in this scenario has no knowledge of where it resides on the server.</span></span> <span data-ttu-id="07992-138">Par exemple, l’application ne peut pas construire des URL relatives correctes pour ses ressources sans savoir qu’elle réside sur le chemin d’URL relatif `/CoolApp/` .</span><span class="sxs-lookup"><span data-stu-id="07992-138">For example, the app can't construct correct relative URLs to its resources without knowing that it resides at the relative URL path `/CoolApp/`.</span></span>

<span data-ttu-id="07992-139">Pour fournir la configuration du Blazor chemin d’accès de base de l’application `https://www.contoso.com/CoolApp/` , l’attribut de la `<base>` balise `href` est défini sur le chemin d’accès racine relatif dans le `wwwroot/index.html` fichier ( Blazor WebAssembly ) ou le `Pages/_Host.cshtml` fichier ( Blazor Server ).</span><span class="sxs-lookup"><span data-stu-id="07992-139">To provide configuration for the Blazor app's base path of `https://www.contoso.com/CoolApp/`, the `<base>` tag's `href` attribute is set to the relative root path in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span>

<span data-ttu-id="07992-140">Blazor WebAssembly (`wwwroot/index.html`):</span><span class="sxs-lookup"><span data-stu-id="07992-140">Blazor WebAssembly (`wwwroot/index.html`):</span></span>

```html
<base href="/CoolApp/">
```

<span data-ttu-id="07992-141">Blazor Server (`Pages/_Host.cshtml`):</span><span class="sxs-lookup"><span data-stu-id="07992-141">Blazor Server (`Pages/_Host.cshtml`):</span></span>

```html
<base href="~/CoolApp/">
```

<span data-ttu-id="07992-142">Blazor Server les applications définissent également le chemin d’accès de base côté serveur en appelant <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> dans le pipeline de demande de l’application `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="07992-142">Blazor Server apps additionally set the server-side base path by calling <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> in the app's request pipeline of `Startup.Configure`:</span></span>

```csharp
app.UsePathBase("/CoolApp");
```

<span data-ttu-id="07992-143">En fournissant le chemin d’URL relatif, un composant qui ne se trouve pas dans le répertoire racine peut construire des URL relatives au chemin d’accès racine de l’application.</span><span class="sxs-lookup"><span data-stu-id="07992-143">By providing the relative URL path, a component that isn't in the root directory can construct URLs relative to the app's root path.</span></span> <span data-ttu-id="07992-144">Les composants à différents niveaux de la structure de répertoires peuvent créer des liens vers d’autres ressources à des emplacements de l’application.</span><span class="sxs-lookup"><span data-stu-id="07992-144">Components at different levels of the directory structure can build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="07992-145">Le chemin d’accès de base de l’application est également utilisé pour intercepter les liens hypertexte sélectionnés où la `href` cible du lien se trouve dans l’espace URI du chemin d’accès de base de l’application.</span><span class="sxs-lookup"><span data-stu-id="07992-145">The app base path is also used to intercept selected hyperlinks where the `href` target of the link is within the app base path URI space.</span></span> <span data-ttu-id="07992-146">Le Blazor routeur gère la navigation interne.</span><span class="sxs-lookup"><span data-stu-id="07992-146">The Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="07992-147">Dans de nombreux scénarios d’hébergement, le chemin d’URL relatif à l’application est la racine de l’application.</span><span class="sxs-lookup"><span data-stu-id="07992-147">In many hosting scenarios, the relative URL path to the app is the root of the app.</span></span> <span data-ttu-id="07992-148">Dans ce cas, le chemin d’accès de base de l’URL relative de l’application est une barre oblique ( `<base href="/" />` ), qui est la configuration par défaut pour une Blazor application.</span><span class="sxs-lookup"><span data-stu-id="07992-148">In these cases, the app's relative URL base path is a forward slash (`<base href="/" />`), which is the default configuration for a Blazor app.</span></span> <span data-ttu-id="07992-149">Dans d’autres scénarios d’hébergement, tels que les pages GitHub et les sous-applications IIS, le chemin d’accès de base de l’application doit être défini sur le chemin d’URL relatif du serveur de l’application.</span><span class="sxs-lookup"><span data-stu-id="07992-149">In other hosting scenarios, such as GitHub Pages and IIS sub-apps, the app base path must be set to the server's relative URL path of the app.</span></span>

<span data-ttu-id="07992-150">Pour définir le chemin d’accès de base de l’application, mettez à jour la `<base>` balise dans les `<head>` éléments tag du `Pages/_Host.cshtml` fichier ( Blazor Server ) ou du `wwwroot/index.html` fichier ( Blazor WebAssembly ).</span><span class="sxs-lookup"><span data-stu-id="07992-150">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the `Pages/_Host.cshtml` file (Blazor Server) or `wwwroot/index.html` file (Blazor WebAssembly).</span></span> <span data-ttu-id="07992-151">Affectez `href` à l’attribut la valeur `/{RELATIVE URL PATH}/` ( Blazor WebAssembly ) ou `~/{RELATIVE URL PATH}/` ( Blazor Server ).</span><span class="sxs-lookup"><span data-stu-id="07992-151">Set the `href` attribute value to `/{RELATIVE URL PATH}/` (Blazor WebAssembly) or `~/{RELATIVE URL PATH}/` (Blazor Server).</span></span> <span data-ttu-id="07992-152">**La barre oblique de fin est obligatoire.**</span><span class="sxs-lookup"><span data-stu-id="07992-152">**The trailing slash is required.**</span></span> <span data-ttu-id="07992-153">L’espace réservé `{RELATIVE URL PATH}` est le chemin d’URL relatif complet de l’application.</span><span class="sxs-lookup"><span data-stu-id="07992-153">The placeholder `{RELATIVE URL PATH}` is the app's full relative URL path.</span></span>

<span data-ttu-id="07992-154">Pour une Blazor WebAssembly application avec un chemin d’URL relatif non racine (par exemple, `<base href="/CoolApp/">` ), l’application ne parvient pas à trouver ses ressources lorsqu’elle est *exécutée localement*.</span><span class="sxs-lookup"><span data-stu-id="07992-154">For a Blazor WebAssembly app with a non-root relative URL path (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="07992-155">Pour surmonter ce problème pendant le développement local et les tests, vous pouvez fournir un argument de *base de chemin* qui correspond à la valeur `href` de la balise `<base>` au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="07992-155">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span> <span data-ttu-id="07992-156">**N’incluez pas de barre oblique finale.**</span><span class="sxs-lookup"><span data-stu-id="07992-156">**Don't include a trailing slash.**</span></span> <span data-ttu-id="07992-157">Pour passer l’argument de base Path lors de l’exécution locale de l’application, exécutez la `dotnet run` commande à partir du répertoire de l’application avec l' `--pathbase` option :</span><span class="sxs-lookup"><span data-stu-id="07992-157">To pass the path base argument when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

<span data-ttu-id="07992-158">Pour une Blazor WebAssembly application avec un chemin d’URL relatif `/CoolApp/` ( `<base href="/CoolApp/">` ), la commande est la suivante :</span><span class="sxs-lookup"><span data-stu-id="07992-158">For a Blazor WebAssembly app with a relative URL path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```dotnetcli
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="07992-159">L' Blazor WebAssembly application répond localement à l’adresse `http://localhost:port/CoolApp` .</span><span class="sxs-lookup"><span data-stu-id="07992-159">The Blazor WebAssembly app responds locally at `http://localhost:port/CoolApp`.</span></span>

<span data-ttu-id="07992-160">**Blazor Server`MapFallbackToPage`configuration de**</span><span class="sxs-lookup"><span data-stu-id="07992-160">**Blazor Server `MapFallbackToPage` configuration**</span></span>

<span data-ttu-id="07992-161">Transmettez le chemin d’accès suivant à <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="07992-161">Pass the following path to <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> in `Startup.Configure`:</span></span>

```csharp
endpoints.MapFallbackToPage("/{RELATIVE PATH}/{**path:nonfile}");
```

<span data-ttu-id="07992-162">L’espace réservé `{RELATIVE PATH}` est le chemin d’accès non racine sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="07992-162">The placeholder `{RELATIVE PATH}` is the non-root path on the server.</span></span> <span data-ttu-id="07992-163">Par exemple, `CoolApp` est le segment de l’espace réservé si l’URL non racine de l’application est `https://{HOST}:{PORT}/CoolApp/` :</span><span class="sxs-lookup"><span data-stu-id="07992-163">For example, `CoolApp` is the placeholder segment if the non-root URL to the app is `https://{HOST}:{PORT}/CoolApp/`):</span></span>

```csharp
endpoints.MapFallbackToPage("/CoolApp/{**path:nonfile}");
```

<span data-ttu-id="07992-164">**Héberger plusieurs Blazor WebAssembly applications**</span><span class="sxs-lookup"><span data-stu-id="07992-164">**Host multiple Blazor WebAssembly apps**</span></span>

<span data-ttu-id="07992-165">Pour plus d’informations sur l’hébergement de plusieurs Blazor WebAssembly applications dans une solution hébergée Blazor , consultez <xref:blazor/host-and-deploy/webassembly#hosted-deployment-with-multiple-blazor-webassembly-apps> .</span><span class="sxs-lookup"><span data-stu-id="07992-165">For more information on hosting multiple Blazor WebAssembly apps in a hosted Blazor solution, see <xref:blazor/host-and-deploy/webassembly#hosted-deployment-with-multiple-blazor-webassembly-apps>.</span></span>

## <a name="deployment"></a><span data-ttu-id="07992-166">Déploiement</span><span class="sxs-lookup"><span data-stu-id="07992-166">Deployment</span></span>

<span data-ttu-id="07992-167">Pour obtenir des conseils de déploiement, consultez les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="07992-167">For deployment guidance, see the following topics:</span></span>

* <xref:blazor/host-and-deploy/webassembly>
* <xref:blazor/host-and-deploy/server>
