---
title: Fichiers statiques dans ASP.NET Core
author: rick-anderson
description: Découvrez comment délivrer et sécuriser des fichiers statiques, et comment configurer le comportement d’un intergiciel (middleware) hébergeant des fichiers statiques dans une application web ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 6/23/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/static-files
ms.openlocfilehash: fb5ef59a74a768a1c202b49872b8a10327c96ff8
ms.sourcegitcommit: 07e7ee573fe4e12be93249a385db745d714ff6ae
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103413455"
---
# <a name="static-files-in-aspnet-core"></a><span data-ttu-id="87643-103">Fichiers statiques dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="87643-103">Static files in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="87643-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="87643-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="87643-105">Les fichiers statiques, tels que HTML, CSS, images et JavaScript, sont des ressources qu’une ASP.NET Core application sert directement aux clients par défaut.</span><span class="sxs-lookup"><span data-stu-id="87643-105">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients by default.</span></span>

<span data-ttu-id="87643-106">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/static-files/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="87643-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="87643-107">Délivrer des fichiers statiques</span><span class="sxs-lookup"><span data-stu-id="87643-107">Serve static files</span></span>

<span data-ttu-id="87643-108">Les fichiers statiques sont stockés dans le répertoire [racine Web](xref:fundamentals/index#web-root) du projet.</span><span class="sxs-lookup"><span data-stu-id="87643-108">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="87643-109">Le répertoire par défaut est `{content root}/wwwroot` , mais il peut être modifié à l’aide de la <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> méthode.</span><span class="sxs-lookup"><span data-stu-id="87643-109">The default directory is `{content root}/wwwroot`, but it can be changed with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> method.</span></span> <span data-ttu-id="87643-110">Pour plus d’informations, consultez [racine du contenu](xref:fundamentals/index#content-root) et [racine Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="87643-110">For more information, see [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="87643-111">La méthode <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> définit le répertoire actif comme racine du contenu :</span><span class="sxs-lookup"><span data-stu-id="87643-111">The <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> method sets the content root to the current directory:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Program2.cs?name=snippet_Program)]

<span data-ttu-id="87643-112">Le code précédent a été créé avec le modèle d’application Web.</span><span class="sxs-lookup"><span data-stu-id="87643-112">The preceding code was created with the web app template.</span></span>

<span data-ttu-id="87643-113">Les fichiers statiques sont accessibles via un chemin d’accès relatif à la [racine Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="87643-113">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="87643-114">Par exemple, les modèles de projet d' **application Web** contiennent plusieurs dossiers dans le `wwwroot` dossier :</span><span class="sxs-lookup"><span data-stu-id="87643-114">For example, the **Web Application** project templates contain several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `js`
  * `lib`

<span data-ttu-id="87643-115">Envisagez de créer le dossier *wwwroot/images* et d’ajouter le fichier *wwwroot/images/MyImage.jpg* .</span><span class="sxs-lookup"><span data-stu-id="87643-115">Consider creating the *wwwroot/images* folder and adding the *wwwroot/images/MyImage.jpg* file.</span></span> <span data-ttu-id="87643-116">Le format d’URI pour accéder à un fichier dans le `images` dossier est `https://<hostname>/images/<image_file_name>` .</span><span class="sxs-lookup"><span data-stu-id="87643-116">The URI format to access a file in the `images` folder is `https://<hostname>/images/<image_file_name>`.</span></span> <span data-ttu-id="87643-117">Par exemple : `https://localhost:5001/images/MyImage.jpg`</span><span class="sxs-lookup"><span data-stu-id="87643-117">For example, `https://localhost:5001/images/MyImage.jpg`</span></span>

### <a name="serve-files-in-web-root"></a><span data-ttu-id="87643-118">Traiter les fichiers dans la racine Web</span><span class="sxs-lookup"><span data-stu-id="87643-118">Serve files in web root</span></span>

<span data-ttu-id="87643-119">Les modèles d’application Web par défaut appellent la <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> méthode dans, qui permet la prise en charge des `Startup.Configure` fichiers statiques :</span><span class="sxs-lookup"><span data-stu-id="87643-119">The default web app templates call the <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> method in `Startup.Configure`, which enables static files to be served:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="87643-120">La `UseStaticFiles` surcharge de méthode sans paramètre marque les fichiers dans la [racine Web](xref:fundamentals/index#web-root) comme étant reservables.</span><span class="sxs-lookup"><span data-stu-id="87643-120">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="87643-121">Le balisage suivant référence *wwwroot/images/MyImage.jpg*:</span><span class="sxs-lookup"><span data-stu-id="87643-121">The following markup references *wwwroot/images/MyImage.jpg*:</span></span>

```html
<img src="~/images/MyImage.jpg" class="img" alt="My image" />
```

<span data-ttu-id="87643-122">Dans le code précédent, le caractère tilde `~/` pointe vers la [racine Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="87643-122">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="87643-123">Délivrer des fichiers en dehors de la racine web</span><span class="sxs-lookup"><span data-stu-id="87643-123">Serve files outside of web root</span></span>

<span data-ttu-id="87643-124">Prenons l’exemple d’une hiérarchie de répertoires dans laquelle les fichiers statiques à prendre en charge se trouvent en dehors de la [racine Web](xref:fundamentals/index#web-root):</span><span class="sxs-lookup"><span data-stu-id="87643-124">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `red-rose.jpg`

<span data-ttu-id="87643-125">Une demande peut accéder au `red-rose.jpg` fichier en configurant l’intergiciel de fichiers statiques comme suit :</span><span class="sxs-lookup"><span data-stu-id="87643-125">A request can access the `red-rose.jpg` file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupRose.cs?name=snippet_Configure&highlight=15-22)]

<span data-ttu-id="87643-126">Dans le code précédent, la hiérarchie de répertoires *MyStaticFiles* est exposée publiquement via le segment d’URI *StaticFiles*.</span><span class="sxs-lookup"><span data-stu-id="87643-126">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="87643-127">Demande de `https://<hostname>/StaticFiles/images/red-rose.jpg` servir le fichier *red-rose.jpg* .</span><span class="sxs-lookup"><span data-stu-id="87643-127">A request to `https://<hostname>/StaticFiles/images/red-rose.jpg` serves the *red-rose.jpg* file.</span></span>

<span data-ttu-id="87643-128">Le balisage suivant référence *MyStaticFiles/images/red-rose.jpg*:</span><span class="sxs-lookup"><span data-stu-id="87643-128">The following markup references *MyStaticFiles/images/red-rose.jpg*:</span></span>

```html
<img src="~/StaticFiles/images/red-rose.jpg" class="img" alt="A red rose" />
```

### <a name="set-http-response-headers"></a><span data-ttu-id="87643-129">Définir des en-têtes de réponse HTTP</span><span class="sxs-lookup"><span data-stu-id="87643-129">Set HTTP response headers</span></span>

<span data-ttu-id="87643-130">Un <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> objet peut être utilisé pour définir des en-têtes de réponse http.</span><span class="sxs-lookup"><span data-stu-id="87643-130">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="87643-131">En plus de configurer le service de fichiers statiques à partir de la [racine Web](xref:fundamentals/index#web-root), le code suivant définit l' `Cache-Control` en-tête :</span><span class="sxs-lookup"><span data-stu-id="87643-131">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_Configure&highlight=15-24)]

<!-- Q: The preceding code sets max-age to 604800 seconds (7 days), so what does the following mean? -->

<span data-ttu-id="87643-132">Les fichiers statiques sont publiquement mis en cache pendant 600 secondes :</span><span class="sxs-lookup"><span data-stu-id="87643-132">Static files are publicly cacheable for 600 seconds:</span></span>

![En-têtes de réponse montrant que l’en-tête Cache-Control a été ajouté](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="87643-134">Autorisations des fichiers statiques</span><span class="sxs-lookup"><span data-stu-id="87643-134">Static file authorization</span></span>

<span data-ttu-id="87643-135">Les modèles ASP.NET Core appellent <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> avant d’appeler <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> .</span><span class="sxs-lookup"><span data-stu-id="87643-135">The ASP.NET Core templates call <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> before calling <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>.</span></span> <span data-ttu-id="87643-136">La plupart des applications suivent ce modèle.</span><span class="sxs-lookup"><span data-stu-id="87643-136">Most apps follow this pattern.</span></span> <span data-ttu-id="87643-137">Lorsque l’intergiciel de fichiers statiques est appelé avant l’intergiciel (middleware) d’autorisation :</span><span class="sxs-lookup"><span data-stu-id="87643-137">When the Static File Middleware is called before the authorization middleware:</span></span>

  * <span data-ttu-id="87643-138">Aucune vérification d’autorisation n’est effectuée sur les fichiers statiques.</span><span class="sxs-lookup"><span data-stu-id="87643-138">No authorization checks are performed on the static files.</span></span>
  * <span data-ttu-id="87643-139">Les fichiers statiques pris en charge par l’intergiciel de fichiers statiques, tels que ceux sous `wwwroot` , sont accessibles publiquement.</span><span class="sxs-lookup"><span data-stu-id="87643-139">Static files served by the Static File Middleware, such as those those under `wwwroot`, are publicly accessible.</span></span>
  
<span data-ttu-id="87643-140">Pour traiter les fichiers statiques en fonction de l’autorisation :</span><span class="sxs-lookup"><span data-stu-id="87643-140">To serve static files based on authorization:</span></span>

  * <span data-ttu-id="87643-141">Stockez-les en dehors de `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="87643-141">Store them outside of `wwwroot`.</span></span>
  * <span data-ttu-id="87643-142">Appelez `UseStaticFiles` , en spécifiant un chemin d’accès, après avoir appelé `UseAuthorization` .</span><span class="sxs-lookup"><span data-stu-id="87643-142">Call `UseStaticFiles`, specifying a path, after calling `UseAuthorization`.</span></span>
  * <span data-ttu-id="87643-143">Définissez la [stratégie d’autorisation de secours](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span><span class="sxs-lookup"><span data-stu-id="87643-143">Set the [fallback authorization policy](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet2&highlight=24-29)]
  
  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet1&highlight=20-25)]

  <span data-ttu-id="87643-144">Dans le code précédent, la stratégie d’autorisation de secours exige que ***tous*** les utilisateurs soient authentifiés.</span><span class="sxs-lookup"><span data-stu-id="87643-144">In the preceding code, the fallback authorization policy requires ***all*** users to be authenticated.</span></span> <span data-ttu-id="87643-145">Les points de terminaison tels que les contrôleurs, Razor les pages, etc. qui spécifient leurs propres exigences d’autorisation n’utilisent pas la stratégie d’autorisation de secours.</span><span class="sxs-lookup"><span data-stu-id="87643-145">Endpoints such as controllers, Razor Pages, etc that specify their own authorization requirements don't use the fallback authorization policy.</span></span> <span data-ttu-id="87643-146">Par exemple, les Razor pages, les contrôleurs ou les méthodes d’action avec `[AllowAnonymous]` ou `[Authorize(PolicyName="MyPolicy")]` utilisent l’attribut d’autorisation appliqué plutôt que la stratégie d’autorisation de secours.</span><span class="sxs-lookup"><span data-stu-id="87643-146">For example, Razor Pages, controllers, or action methods with `[AllowAnonymous]` or `[Authorize(PolicyName="MyPolicy")]` use the applied authorization attribute rather than the fallback authorization policy.</span></span>

  <span data-ttu-id="87643-147"><xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> Ajoute <xref:Microsoft.AspNetCore.Authorization.Infrastructure.DenyAnonymousAuthorizationRequirement> à l’instance actuelle, ce qui garantit que l’utilisateur actuel est authentifié.</span><span class="sxs-lookup"><span data-stu-id="87643-147"><xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> adds <xref:Microsoft.AspNetCore.Authorization.Infrastructure.DenyAnonymousAuthorizationRequirement> to the current instance, which enforces that the current user is authenticated.</span></span>

  <span data-ttu-id="87643-148">Les ressources statiques sous `wwwroot` sont accessibles publiquement, car l’intergiciel de fichiers statiques par défaut ( `app.UseStaticFiles();` ) est appelé avant `UseAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="87643-148">Static assets under `wwwroot` are publicly accessible because the default Static File Middleware (`app.UseStaticFiles();`) is called before `UseAuthentication`.</span></span> <span data-ttu-id="87643-149">Les ressources statiques dans le dossier *MyStaticFiles* nécessitent une authentification.</span><span class="sxs-lookup"><span data-stu-id="87643-149">Static assets in the *MyStaticFiles* folder require authentication.</span></span> <span data-ttu-id="87643-150">L' [exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/static-files/samples) illustre cela.</span><span class="sxs-lookup"><span data-stu-id="87643-150">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/static-files/samples) demonstrates this.</span></span>

<span data-ttu-id="87643-151">Une autre approche pour traiter les fichiers en fonction de l’autorisation consiste à :</span><span class="sxs-lookup"><span data-stu-id="87643-151">An alternative approach to serve files based on authorization is to:</span></span>

  * <span data-ttu-id="87643-152">Stockez-les en dehors de `wwwroot` et de tout répertoire accessible à l’intergiciel de fichiers statiques.</span><span class="sxs-lookup"><span data-stu-id="87643-152">Store them outside of `wwwroot` and any directory accessible to the Static File Middleware.</span></span>
  * <span data-ttu-id="87643-153">Servez-vous via une méthode d’action à laquelle l’autorisation est appliquée et retournez un <xref:Microsoft.AspNetCore.Mvc.FileResult> objet :</span><span class="sxs-lookup"><span data-stu-id="87643-153">Serve them via an action method to which authorization is applied and return a <xref:Microsoft.AspNetCore.Mvc.FileResult> object:</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImage)]

## <a name="directory-browsing"></a><span data-ttu-id="87643-154">Exploration de répertoires</span><span class="sxs-lookup"><span data-stu-id="87643-154">Directory browsing</span></span>

<span data-ttu-id="87643-155">L’exploration des répertoires permet la liste des répertoires dans les répertoires spécifiés.</span><span class="sxs-lookup"><span data-stu-id="87643-155">Directory browsing allows directory listing within specified directories.</span></span>

<span data-ttu-id="87643-156">L’exploration des répertoires est désactivée par défaut pour des raisons de sécurité.</span><span class="sxs-lookup"><span data-stu-id="87643-156">Directory browsing is disabled by default for security reasons.</span></span> <span data-ttu-id="87643-157">Pour plus d’informations, consultez [considérations](#considerations).</span><span class="sxs-lookup"><span data-stu-id="87643-157">For more information, see [Considerations](#considerations).</span></span>

<span data-ttu-id="87643-158">Activer l’exploration des répertoires avec :</span><span class="sxs-lookup"><span data-stu-id="87643-158">Enable directory browsing with:</span></span>

* <span data-ttu-id="87643-159"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="87643-159"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="87643-160"><xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> dans `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="87643-160"><xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> in `Startup.Configure`.</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ClassMembers&highlight=4,21-35)]

<span data-ttu-id="87643-161">Le code précédent autorise l’exploration des répertoires du dossier *wwwroot/images* à l’aide de l’URL `https://<hostname>/MyImages` , avec des liens vers chaque fichier et dossier :</span><span class="sxs-lookup"><span data-stu-id="87643-161">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL `https://<hostname>/MyImages`, with links to each file and folder:</span></span>

![exploration des répertoires](static-files/_static/dir-browse.png)

## <a name="serve-default-documents"></a><span data-ttu-id="87643-163">Traiter les documents par défaut</span><span class="sxs-lookup"><span data-stu-id="87643-163">Serve default documents</span></span>

<span data-ttu-id="87643-164">La définition d’une page par défaut fournit aux visiteurs un point de départ sur un site.</span><span class="sxs-lookup"><span data-stu-id="87643-164">Setting a default page provides visitors a starting point on a site.</span></span> <span data-ttu-id="87643-165">Pour traiter une page par défaut `wwwroot` sans URI complet, appelez la <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> méthode :</span><span class="sxs-lookup"><span data-stu-id="87643-165">To serve a default page from `wwwroot` without a fully qualified URI, call the <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> method:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="87643-166">`UseDefaultFiles` doit être appelé avant `UseStaticFiles` pour délivrer le fichier par défaut.</span><span class="sxs-lookup"><span data-stu-id="87643-166">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="87643-167">`UseDefaultFiles` est un ReWriter d’URL qui ne traite pas le fichier.</span><span class="sxs-lookup"><span data-stu-id="87643-167">`UseDefaultFiles` is a URL rewriter that doesn't serve the file.</span></span>

<span data-ttu-id="87643-168">Avec `UseDefaultFiles` , les demandes à un dossier dans `wwwroot` Rechercher sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="87643-168">With `UseDefaultFiles`, requests to a folder in `wwwroot` search for:</span></span>

* <span data-ttu-id="87643-169">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="87643-169">*default.htm*</span></span>
* <span data-ttu-id="87643-170">*default.html*</span><span class="sxs-lookup"><span data-stu-id="87643-170">*default.html*</span></span>
* <span data-ttu-id="87643-171">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="87643-171">*index.htm*</span></span>
* <span data-ttu-id="87643-172">*index.html*</span><span class="sxs-lookup"><span data-stu-id="87643-172">*index.html*</span></span>

<span data-ttu-id="87643-173">Le premier fichier trouvé dans la liste est délivré comme si la requête était l’URI qualifié complet.</span><span class="sxs-lookup"><span data-stu-id="87643-173">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="87643-174">L’URL du navigateur continue de refléter l’URI demandé.</span><span class="sxs-lookup"><span data-stu-id="87643-174">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="87643-175">Le code suivant change le nom de fichier par défaut en *mydefault.html* :</span><span class="sxs-lookup"><span data-stu-id="87643-175">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_DefaultFiles)]

<span data-ttu-id="87643-176">Le code suivant est affiché `Startup.Configure` avec le code précédent :</span><span class="sxs-lookup"><span data-stu-id="87643-176">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_Configure&highlight=15-19)]

### <a name="usefileserver-for-default-documents"></a><span data-ttu-id="87643-177">UseFileServer pour les documents par défaut</span><span class="sxs-lookup"><span data-stu-id="87643-177">UseFileServer for default documents</span></span>

<span data-ttu-id="87643-178"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combine les fonctionnalités de `UseStaticFiles` , `UseDefaultFiles` et éventuellement `UseDirectoryBrowser` .</span><span class="sxs-lookup"><span data-stu-id="87643-178"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="87643-179">Appelez `app.UseFileServer` pour activer le service des fichiers statiques et le fichier par défaut.</span><span class="sxs-lookup"><span data-stu-id="87643-179">Call `app.UseFileServer` to enable the serving of static files and the default file.</span></span> <span data-ttu-id="87643-180">L’exploration des répertoires n’est pas activée.</span><span class="sxs-lookup"><span data-stu-id="87643-180">Directory browsing isn't enabled.</span></span> <span data-ttu-id="87643-181">Le code suivant est affiché `Startup.Configure` avec `UseFileServer` :</span><span class="sxs-lookup"><span data-stu-id="87643-181">The following code shows `Startup.Configure` with `UseFileServer`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty2.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="87643-182">Le code suivant permet le service des fichiers statiques, le fichier par défaut et l’exploration des répertoires :</span><span class="sxs-lookup"><span data-stu-id="87643-182">The following code enables the serving of static files, the default file, and directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="87643-183">Le code suivant est affiché `Startup.Configure` avec le code précédent :</span><span class="sxs-lookup"><span data-stu-id="87643-183">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty3.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="87643-184">Considérez la hiérarchie de répertoires suivante :</span><span class="sxs-lookup"><span data-stu-id="87643-184">Consider the following directory hierarchy:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `MyImage.jpg`
  * `default.html`

<span data-ttu-id="87643-185">Le code suivant permet le service des fichiers statiques, le fichier par défaut et l’exploration des répertoires de `MyStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="87643-185">The following code enables the serving of static files, the default file, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileServer.cs?name=snippet_ClassMembers&highlight=4,21-31)]

<span data-ttu-id="87643-186"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> doit être appelé lorsque la `EnableDirectoryBrowsing` valeur de la propriété est `true` .</span><span class="sxs-lookup"><span data-stu-id="87643-186"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> must be called when the `EnableDirectoryBrowsing` property value is `true`.</span></span>

<span data-ttu-id="87643-187">En utilisant la hiérarchie de fichiers et le code précédent, les URL sont résolues comme suit :</span><span class="sxs-lookup"><span data-stu-id="87643-187">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="87643-188">URI</span><span class="sxs-lookup"><span data-stu-id="87643-188">URI</span></span>            |      <span data-ttu-id="87643-189">response</span><span class="sxs-lookup"><span data-stu-id="87643-189">Response</span></span>  |
| ------- | ------|
| `https://<hostname>/StaticFiles/images/MyImage.jpg` | <span data-ttu-id="87643-190">*MyStaticFiles/images/MyImage.jpg*</span><span class="sxs-lookup"><span data-stu-id="87643-190">*MyStaticFiles/images/MyImage.jpg*</span></span> |
| `https://<hostname>/StaticFiles` | <span data-ttu-id="87643-191">*MyStaticFiles/default.html*</span><span class="sxs-lookup"><span data-stu-id="87643-191">*MyStaticFiles/default.html*</span></span> |

<span data-ttu-id="87643-192">Si aucun fichier nommé par défaut n’existe dans le répertoire *MyStaticFiles* , `https://<hostname>/StaticFiles` retourne la liste des répertoires avec des liens interactives :</span><span class="sxs-lookup"><span data-stu-id="87643-192">If no default-named file exists in the *MyStaticFiles* directory, `https://<hostname>/StaticFiles` returns the directory listing with clickable links:</span></span>

![Liste des fichiers statiques](static-files/_static/db2.png)

<span data-ttu-id="87643-194"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> et <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> effectuent une redirection côté client à partir de l’URI cible sans fin `/`  vers l’URI cible avec une fin `/` .</span><span class="sxs-lookup"><span data-stu-id="87643-194"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from the target URI without a trailing `/`  to the target URI with a trailing `/`.</span></span> <span data-ttu-id="87643-195">Par exemple, de `https://<hostname>/StaticFiles` à `https://<hostname>/StaticFiles/` .</span><span class="sxs-lookup"><span data-stu-id="87643-195">For example, from `https://<hostname>/StaticFiles` to `https://<hostname>/StaticFiles/`.</span></span> <span data-ttu-id="87643-196">Les URL relatives dans le répertoire *StaticFiles* ne sont pas valides sans barre oblique finale ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="87643-196">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash (`/`).</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="87643-197">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="87643-197">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="87643-198">La <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> classe contient une `Mappings` propriété qui sert de mappage des extensions de fichier aux types de contenu MIME.</span><span class="sxs-lookup"><span data-stu-id="87643-198">The <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> class contains a `Mappings` property that serves as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="87643-199">Dans l’exemple suivant, plusieurs extensions de fichier sont mappées à des types MIME connus.</span><span class="sxs-lookup"><span data-stu-id="87643-199">In the following sample, several file extensions are mapped to known MIME types.</span></span> <span data-ttu-id="87643-200">L’extension *. rtf* est remplacée et *. MP4* est supprimé :</span><span class="sxs-lookup"><span data-stu-id="87643-200">The *.rtf* extension is replaced, and *.mp4* is removed:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Provider)]

<span data-ttu-id="87643-201">Le code suivant est affiché `Startup.Configure` avec le code précédent :</span><span class="sxs-lookup"><span data-stu-id="87643-201">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Configure&highlight=15-43)]

<span data-ttu-id="87643-202">Consultez [Types de contenu MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="87643-202">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="87643-203">Types de contenu non standard</span><span class="sxs-lookup"><span data-stu-id="87643-203">Non-standard content types</span></span>

<span data-ttu-id="87643-204">L’intergiciel de fichiers statiques comprend presque 400 types de contenu de fichier connus.</span><span class="sxs-lookup"><span data-stu-id="87643-204">The Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="87643-205">Si l’utilisateur demande un fichier dont le type de fichier est inconnu, l’intergiciel (middleware) de fichiers statique transmet la demande à l’intergiciel (middleware) suivant dans le pipeline.</span><span class="sxs-lookup"><span data-stu-id="87643-205">If the user requests a file with an unknown file type, the Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="87643-206">Si aucun intergiciel ne gère la requête, une réponse *404 introuvable* est retournée.</span><span class="sxs-lookup"><span data-stu-id="87643-206">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="87643-207">Si l’exploration des répertoires est activée, un lien vers le fichier est affiché dans la liste de répertoires.</span><span class="sxs-lookup"><span data-stu-id="87643-207">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="87643-208">Le code suivant permet de délivrer des types inconnus et rend le fichier inconnu en tant qu’image :</span><span class="sxs-lookup"><span data-stu-id="87643-208">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_UseStaticFiles)]

<span data-ttu-id="87643-209">Le code suivant est affiché `Startup.Configure` avec le code précédent :</span><span class="sxs-lookup"><span data-stu-id="87643-209">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_Configure&highlight=15-19)]

<span data-ttu-id="87643-210">Avec le code précédent, une requête pour un fichier avec un type de contenu inconnu est retournée en tant qu’image.</span><span class="sxs-lookup"><span data-stu-id="87643-210">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="87643-211">L’activation <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> de est un risque pour la sécurité.</span><span class="sxs-lookup"><span data-stu-id="87643-211">Enabling <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> is a security risk.</span></span> <span data-ttu-id="87643-212">Il est désactivé par défaut et son utilisation est déconseillée.</span><span class="sxs-lookup"><span data-stu-id="87643-212">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="87643-213">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) fournit une alternative plus sûre pour délivrer des fichiers avec des extensions non standard.</span><span class="sxs-lookup"><span data-stu-id="87643-213">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="87643-214">Servir des fichiers à partir de plusieurs emplacements</span><span class="sxs-lookup"><span data-stu-id="87643-214">Serve files from multiple locations</span></span>

<span data-ttu-id="87643-215">`UseStaticFiles` et `UseFileServer` par défaut, le fournisseur de fichiers pointe sur `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="87643-215">`UseStaticFiles` and `UseFileServer` default to the file provider pointing at `wwwroot`.</span></span> <span data-ttu-id="87643-216">Des instances supplémentaires de `UseStaticFiles` et `UseFileServer` peuvent être fournies avec d’autres fournisseurs de fichiers pour servir des fichiers à partir d’autres emplacements.</span><span class="sxs-lookup"><span data-stu-id="87643-216">Additional instances of `UseStaticFiles` and `UseFileServer` can be provided with other file providers to serve files from other locations.</span></span> <span data-ttu-id="87643-217">Pour plus d’informations, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span><span class="sxs-lookup"><span data-stu-id="87643-217">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

<a name="sc"></a>

### <a name="security-considerations-for-static-files"></a><span data-ttu-id="87643-218">Considérations sur la sécurité des fichiers statiques</span><span class="sxs-lookup"><span data-stu-id="87643-218">Security considerations for static files</span></span>

> [!WARNING]
> <span data-ttu-id="87643-219">`UseDirectoryBrowser` et `UseStaticFiles` peuvent entraîner une fuite de secrets.</span><span class="sxs-lookup"><span data-stu-id="87643-219">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="87643-220">La désactivation de l’exploration de répertoires est fortement recommandée en production.</span><span class="sxs-lookup"><span data-stu-id="87643-220">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="87643-221">Examinez attentivement les répertoires qui sont activés via `UseStaticFiles` ou `UseDirectoryBrowser`.</span><span class="sxs-lookup"><span data-stu-id="87643-221">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="87643-222">L’ensemble du répertoire et de ses sous-répertoires deviennent accessibles publiquement.</span><span class="sxs-lookup"><span data-stu-id="87643-222">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="87643-223">Stocker les fichiers pouvant être affectés au public dans un répertoire dédié, tel que `<content_root>/wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="87643-223">Store files suitable for serving to the public in a dedicated directory, such as `<content_root>/wwwroot`.</span></span> <span data-ttu-id="87643-224">Séparez ces fichiers des vues MVC, des Razor pages, des fichiers de configuration, etc.</span><span class="sxs-lookup"><span data-stu-id="87643-224">Separate these files from MVC views, Razor Pages, configuration files, etc.</span></span>

* <span data-ttu-id="87643-225">Les URL pour le contenu exposé avec `UseDirectoryBrowser` et `UseStaticFiles` sont soumises aux restrictions de respect de la casse et de caractères du système de fichiers sous-jacent.</span><span class="sxs-lookup"><span data-stu-id="87643-225">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="87643-226">Par exemple, Windows ne respecte pas la casse, contrairement à macOS et Linux.</span><span class="sxs-lookup"><span data-stu-id="87643-226">For example, Windows is case insensitive, but macOS and Linux aren't.</span></span>

* <span data-ttu-id="87643-227">Les applications ASP.NET Core hébergées dans IIS utilisent le [module ASP.NET Core](xref:host-and-deploy/aspnet-core-module) pour transférer toutes les requêtes à l’application, notamment les requêtes de fichiers statiques.</span><span class="sxs-lookup"><span data-stu-id="87643-227">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="87643-228">Le gestionnaire de fichiers statiques IIS n’est pas utilisé et n’a pas la possibilité de traiter les demandes.</span><span class="sxs-lookup"><span data-stu-id="87643-228">The IIS static file handler isn't used and has no chance to handle requests.</span></span>

* <span data-ttu-id="87643-229">Effectuez les étapes suivantes dans le Gestionnaire des services Internet (IIS) pour supprimer le gestionnaire de fichiers statiques d’IIS au niveau du serveur ou du site web :</span><span class="sxs-lookup"><span data-stu-id="87643-229">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="87643-230">Accédez à la fonctionnalité **Modules**.</span><span class="sxs-lookup"><span data-stu-id="87643-230">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="87643-231">Sélectionnez **StaticFileModule** dans la liste.</span><span class="sxs-lookup"><span data-stu-id="87643-231">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="87643-232">Cliquez sur **Supprimer** dans l’encadré **Actions**.</span><span class="sxs-lookup"><span data-stu-id="87643-232">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="87643-233">Si le gestionnaire de fichiers statiques d’IIS est activé **et** que le module ASP.NET Core est incorrectement configuré, les fichiers statiques peuvent être délivrés.</span><span class="sxs-lookup"><span data-stu-id="87643-233">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="87643-234">Cela se produit par exemple si le fichier *web.config* n’est pas déployé.</span><span class="sxs-lookup"><span data-stu-id="87643-234">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="87643-235">Placez les fichiers de code, y compris les fichiers *. cs* et *. cshtml*, en dehors de la [racine Web](xref:fundamentals/index#web-root)du projet d’application.</span><span class="sxs-lookup"><span data-stu-id="87643-235">Place code files, including *.cs* and *.cshtml*, outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="87643-236">Par conséquent, une séparation logique est créée entre le contenu côté client et le code basé sur le serveur de l’application.</span><span class="sxs-lookup"><span data-stu-id="87643-236">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="87643-237">Ceci empêche la fuite de code côté serveur.</span><span class="sxs-lookup"><span data-stu-id="87643-237">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="87643-238">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="87643-238">Additional resources</span></span>

* [<span data-ttu-id="87643-239">Middleware</span><span class="sxs-lookup"><span data-stu-id="87643-239">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="87643-240">Introduction à ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="87643-240">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="87643-241">Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="87643-241">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="87643-242">Les fichiers statiques, comme les fichiers HTML, CSS, images et JavaScript, sont des ressources qu’une application ASP.NET Core délivre directement aux clients.</span><span class="sxs-lookup"><span data-stu-id="87643-242">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients.</span></span> <span data-ttu-id="87643-243">Une configuration est nécessaire pour pouvoir délivrer ces fichiers.</span><span class="sxs-lookup"><span data-stu-id="87643-243">Some configuration is required to enable serving of these files.</span></span>

<span data-ttu-id="87643-244">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/static-files/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="87643-244">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="87643-245">Délivrer des fichiers statiques</span><span class="sxs-lookup"><span data-stu-id="87643-245">Serve static files</span></span>

<span data-ttu-id="87643-246">Les fichiers statiques sont stockés dans le répertoire [racine Web](xref:fundamentals/index#web-root) du projet.</span><span class="sxs-lookup"><span data-stu-id="87643-246">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="87643-247">Le répertoire par défaut est *{content root}/wwwroot*, mais il peut être modifié à l’aide de la <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> méthode.</span><span class="sxs-lookup"><span data-stu-id="87643-247">The default directory is *{content root}/wwwroot*, but it can be changed via the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> method.</span></span> <span data-ttu-id="87643-248">Pour plus d’informations, consultez [Racine du contenu](xref:fundamentals/index#content-root) et [Racine web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="87643-248">See [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root) for more information.</span></span>

<span data-ttu-id="87643-249">L’hôte web de l’application doit être informé du répertoire racine du contenu.</span><span class="sxs-lookup"><span data-stu-id="87643-249">The app's web host must be made aware of the content root directory.</span></span>

<span data-ttu-id="87643-250">La méthode `WebHost.CreateDefaultBuilder` définit le répertoire actif comme racine du contenu :</span><span class="sxs-lookup"><span data-stu-id="87643-250">The `WebHost.CreateDefaultBuilder` method sets the content root to the current directory:</span></span>

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

<span data-ttu-id="87643-251">Les fichiers statiques sont accessibles via un chemin d’accès relatif à la [racine Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="87643-251">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="87643-252">Par exemple, le modèle de projet d' **application Web** contient plusieurs dossiers dans le `wwwroot` dossier :</span><span class="sxs-lookup"><span data-stu-id="87643-252">For example, the **Web Application** project template contains several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`

<span data-ttu-id="87643-253">Le format d’URI permettant d’accéder à un fichier dans le sous-dossier *images* est *http:// \<server_address> /images/ \<image_file_name>*.</span><span class="sxs-lookup"><span data-stu-id="87643-253">The URI format to access a file in the *images* subfolder is *http://\<server_address>/images/\<image_file_name>*.</span></span> <span data-ttu-id="87643-254">Par exemple : *http://localhost:9189/images/banner3.svg*.</span><span class="sxs-lookup"><span data-stu-id="87643-254">For example, *http://localhost:9189/images/banner3.svg*.</span></span>

<span data-ttu-id="87643-255">Si vous ciblez .NET Framework, ajoutez le package [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) au projet.</span><span class="sxs-lookup"><span data-stu-id="87643-255">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span> <span data-ttu-id="87643-256">Si vous ciblez .NET Core, le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) comprend ce package.</span><span class="sxs-lookup"><span data-stu-id="87643-256">If targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) includes this package.</span></span>

<span data-ttu-id="87643-257">Configurez l' [intergiciel (middleware](xref:fundamentals/middleware/index)), qui permet de servir des fichiers statiques.</span><span class="sxs-lookup"><span data-stu-id="87643-257">Configure the [middleware](xref:fundamentals/middleware/index), which enables the serving of static files.</span></span>

### <a name="serve-files-inside-of-web-root"></a><span data-ttu-id="87643-258">Délivrer des fichiers dans la racine web</span><span class="sxs-lookup"><span data-stu-id="87643-258">Serve files inside of web root</span></span>

<span data-ttu-id="87643-259">Appelez la <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> méthode dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="87643-259">Invoke the <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> method within `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

<span data-ttu-id="87643-260">La `UseStaticFiles` surcharge de méthode sans paramètre marque les fichiers dans la [racine Web](xref:fundamentals/index#web-root) comme étant reservables.</span><span class="sxs-lookup"><span data-stu-id="87643-260">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="87643-261">Le balisage suivant référence *wwwroot/images/banner1.svg* :</span><span class="sxs-lookup"><span data-stu-id="87643-261">The following markup references *wwwroot/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

<span data-ttu-id="87643-262">Dans le code précédent, le caractère tilde `~/` pointe vers la [racine Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="87643-262">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="87643-263">Délivrer des fichiers en dehors de la racine web</span><span class="sxs-lookup"><span data-stu-id="87643-263">Serve files outside of web root</span></span>

<span data-ttu-id="87643-264">Prenons l’exemple d’une hiérarchie de répertoires dans laquelle les fichiers statiques à prendre en charge se trouvent en dehors de la [racine Web](xref:fundamentals/index#web-root):</span><span class="sxs-lookup"><span data-stu-id="87643-264">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `banner1.svg`

<span data-ttu-id="87643-265">Vous permettez à une requête d’accéder au fichier *banner1.svg* en configurant le middleware (intergiciel) des fichiers statiques comme suit :</span><span class="sxs-lookup"><span data-stu-id="87643-265">A request can access the *banner1.svg* file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

<span data-ttu-id="87643-266">Dans le code précédent, la hiérarchie de répertoires *MyStaticFiles* est exposée publiquement via le segment d’URI *StaticFiles*.</span><span class="sxs-lookup"><span data-stu-id="87643-266">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="87643-267">Une demande à *http:// \<server_address> /StaticFiles/images/banner1.svg* sert le fichier *banner1. svg* .</span><span class="sxs-lookup"><span data-stu-id="87643-267">A request to *http://\<server_address>/StaticFiles/images/banner1.svg* serves the *banner1.svg* file.</span></span>

<span data-ttu-id="87643-268">Le balisage suivant référence *MyStaticFiles/images/banner1.svg* :</span><span class="sxs-lookup"><span data-stu-id="87643-268">The following markup references *MyStaticFiles/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a><span data-ttu-id="87643-269">Définir des en-têtes de réponse HTTP</span><span class="sxs-lookup"><span data-stu-id="87643-269">Set HTTP response headers</span></span>

<span data-ttu-id="87643-270">Un <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> objet peut être utilisé pour définir des en-têtes de réponse http.</span><span class="sxs-lookup"><span data-stu-id="87643-270">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="87643-271">En plus de configurer le service de fichiers statiques à partir de la [racine Web](xref:fundamentals/index#web-root), le code suivant définit l' `Cache-Control` en-tête :</span><span class="sxs-lookup"><span data-stu-id="87643-271">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="87643-272">La <xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType> méthode existe dans le package [Microsoft. AspNetCore. http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) .</span><span class="sxs-lookup"><span data-stu-id="87643-272">The <xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType> method exists in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

<span data-ttu-id="87643-273">Les fichiers peuvent être mis en cache publiquement pendant 10 minutes (600 secondes) dans l’environnement de développement :</span><span class="sxs-lookup"><span data-stu-id="87643-273">The files have been made publicly cacheable for 10 minutes (600 seconds) in the Development environment:</span></span>

![En-têtes de réponse montrant que l’en-tête Cache-Control a été ajouté](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="87643-275">Autorisations des fichiers statiques</span><span class="sxs-lookup"><span data-stu-id="87643-275">Static file authorization</span></span>

<span data-ttu-id="87643-276">Le middleware de fichiers statiques ne fournit pas de vérification des autorisations.</span><span class="sxs-lookup"><span data-stu-id="87643-276">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="87643-277">Tous les fichiers qu’il délivre, notamment ceux sous *wwwroot*, sont accessibles publiquement.</span><span class="sxs-lookup"><span data-stu-id="87643-277">Any files served by it, including those under *wwwroot*, are publicly accessible.</span></span> <span data-ttu-id="87643-278">Pour délivrer des fichiers en fonction d’une autorisation :</span><span class="sxs-lookup"><span data-stu-id="87643-278">To serve files based on authorization:</span></span>

* <span data-ttu-id="87643-279">Stockez-les en dehors de *wwwroot* et de tout répertoire accessible au middleware de fichiers statiques.</span><span class="sxs-lookup"><span data-stu-id="87643-279">Store them outside of *wwwroot* and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="87643-280">Délivrez-les via une méthode d’action à laquelle une autorisation est appliquée.</span><span class="sxs-lookup"><span data-stu-id="87643-280">Serve them via an action method to which authorization is applied.</span></span> <span data-ttu-id="87643-281">Retourne un <xref:Microsoft.AspNetCore.Mvc.FileResult> objet :</span><span class="sxs-lookup"><span data-stu-id="87643-281">Return a <xref:Microsoft.AspNetCore.Mvc.FileResult> object:</span></span>

  [!code-csharp[](static-files/samples/1.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a><span data-ttu-id="87643-282">Activer l’exploration des répertoires</span><span class="sxs-lookup"><span data-stu-id="87643-282">Enable directory browsing</span></span>

<span data-ttu-id="87643-283">L’exploration des répertoires permet aux utilisateurs de votre application web de voir une liste des répertoires et des fichiers dans un répertoire spécifié.</span><span class="sxs-lookup"><span data-stu-id="87643-283">Directory browsing allows users of your web app to see a directory listing and files within a specified directory.</span></span> <span data-ttu-id="87643-284">L’exploration des répertoires est désactivée par défaut pour des raisons de sécurité (consultez [Considérations](#considerations)).</span><span class="sxs-lookup"><span data-stu-id="87643-284">Directory browsing is disabled by default for security reasons (see [Considerations](#considerations)).</span></span> <span data-ttu-id="87643-285">Activez l’exploration des répertoires en appelant la <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> méthode dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="87643-285">Enable directory browsing by invoking the <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> method in `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

<span data-ttu-id="87643-286">Ajoutez les services requis en appelant la <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> méthode à partir de `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="87643-286">Add required services by invoking the <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> method from `Startup.ConfigureServices`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

<span data-ttu-id="87643-287">Le code précédent autorise l’exploration des répertoires du dossier *wwwroot/images* à l’aide de l’URL *http:// \<server_address> /myImages*, avec des liens vers chaque fichier et dossier :</span><span class="sxs-lookup"><span data-stu-id="87643-287">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*, with links to each file and folder:</span></span>

![exploration des répertoires](static-files/_static/dir-browse.png)

<span data-ttu-id="87643-289">Consultez [Considérations](#considerations) sur les risques de sécurité lors de l’activation de l’exploration.</span><span class="sxs-lookup"><span data-stu-id="87643-289">See [Considerations](#considerations) on the security risks when enabling browsing.</span></span>

<span data-ttu-id="87643-290">Notez les deux appels de `UseStaticFiles` dans l’exemple suivant.</span><span class="sxs-lookup"><span data-stu-id="87643-290">Note the two `UseStaticFiles` calls in the following example.</span></span> <span data-ttu-id="87643-291">Le premier appel permet de délivrer des fichiers statiques dans le dossier *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="87643-291">The first call enables the serving of static files in the *wwwroot* folder.</span></span> <span data-ttu-id="87643-292">Le deuxième appel active l’exploration des répertoires du dossier *wwwroot/images* à l’aide de l’URL *http:// \<server_address> /myImages*:</span><span class="sxs-lookup"><span data-stu-id="87643-292">The second call enables directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a><span data-ttu-id="87643-293">Délivrer un document par défaut</span><span class="sxs-lookup"><span data-stu-id="87643-293">Serve a default document</span></span>

<span data-ttu-id="87643-294">La définition d’une page d’accueil par défaut donne aux visiteurs un point de départ logique lors de la visite de votre site.</span><span class="sxs-lookup"><span data-stu-id="87643-294">Setting a default home page provides visitors a logical starting point when visiting your site.</span></span> <span data-ttu-id="87643-295">Pour traiter une page par défaut sans que l’utilisateur ne qualifie complètement l’URI, appelez la <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> méthode à partir de `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="87643-295">To serve a default page without the user fully qualifying the URI, call the <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> method from `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> <span data-ttu-id="87643-296">`UseDefaultFiles` doit être appelé avant `UseStaticFiles` pour délivrer le fichier par défaut.</span><span class="sxs-lookup"><span data-stu-id="87643-296">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="87643-297">`UseDefaultFiles` est un module de réécriture d’URL qui ne délivre pas réellement le fichier.</span><span class="sxs-lookup"><span data-stu-id="87643-297">`UseDefaultFiles` is a URL rewriter that doesn't actually serve the file.</span></span> <span data-ttu-id="87643-298">Activez le middleware de fichiers statiques via `UseStaticFiles` pour délivrer le fichier.</span><span class="sxs-lookup"><span data-stu-id="87643-298">Enable Static File Middleware via `UseStaticFiles` to serve the file.</span></span>

<span data-ttu-id="87643-299">Avec `UseDefaultFiles`, les requêtes sur un dossier recherchent :</span><span class="sxs-lookup"><span data-stu-id="87643-299">With `UseDefaultFiles`, requests to a folder search for:</span></span>

* <span data-ttu-id="87643-300">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="87643-300">*default.htm*</span></span>
* <span data-ttu-id="87643-301">*default.html*</span><span class="sxs-lookup"><span data-stu-id="87643-301">*default.html*</span></span>
* <span data-ttu-id="87643-302">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="87643-302">*index.htm*</span></span>
* <span data-ttu-id="87643-303">*index.html*</span><span class="sxs-lookup"><span data-stu-id="87643-303">*index.html*</span></span>

<span data-ttu-id="87643-304">Le premier fichier trouvé dans la liste est délivré comme si la requête était l’URI qualifié complet.</span><span class="sxs-lookup"><span data-stu-id="87643-304">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="87643-305">L’URL du navigateur continue de refléter l’URI demandé.</span><span class="sxs-lookup"><span data-stu-id="87643-305">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="87643-306">Le code suivant change le nom de fichier par défaut en *mydefault.html* :</span><span class="sxs-lookup"><span data-stu-id="87643-306">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a><span data-ttu-id="87643-307">UseFileServer</span><span class="sxs-lookup"><span data-stu-id="87643-307">UseFileServer</span></span>

<span data-ttu-id="87643-308"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combine les fonctionnalités de `UseStaticFiles` , `UseDefaultFiles` et éventuellement `UseDirectoryBrowser` .</span><span class="sxs-lookup"><span data-stu-id="87643-308"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="87643-309">Le code suivant active la possibilité de délivrer des fichiers statiques et le fichier par défaut.</span><span class="sxs-lookup"><span data-stu-id="87643-309">The following code enables the serving of static files and the default file.</span></span> <span data-ttu-id="87643-310">L’exploration des répertoires n’est pas activée.</span><span class="sxs-lookup"><span data-stu-id="87643-310">Directory browsing isn't enabled.</span></span>

```csharp
app.UseFileServer();
```

<span data-ttu-id="87643-311">Le code suivant s’appuie sur la surcharge sans paramètre en activant l’exploration des répertoires :</span><span class="sxs-lookup"><span data-stu-id="87643-311">The following code builds upon the parameterless overload by enabling directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="87643-312">Considérez la hiérarchie de répertoires suivante :</span><span class="sxs-lookup"><span data-stu-id="87643-312">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="87643-313">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="87643-313">**wwwroot**</span></span>
  * <span data-ttu-id="87643-314">**format**</span><span class="sxs-lookup"><span data-stu-id="87643-314">**css**</span></span>
  * <span data-ttu-id="87643-315">**images**</span><span class="sxs-lookup"><span data-stu-id="87643-315">**images**</span></span>
  * <span data-ttu-id="87643-316">**js**</span><span class="sxs-lookup"><span data-stu-id="87643-316">**js**</span></span>
* <span data-ttu-id="87643-317">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="87643-317">**MyStaticFiles**</span></span>
  * <span data-ttu-id="87643-318">**images**</span><span class="sxs-lookup"><span data-stu-id="87643-318">**images**</span></span>
    * <span data-ttu-id="87643-319">*banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="87643-319">*banner1.svg*</span></span>
  * <span data-ttu-id="87643-320">*default.html*</span><span class="sxs-lookup"><span data-stu-id="87643-320">*default.html*</span></span>

<span data-ttu-id="87643-321">Le code suivant active les fichiers statiques, les fichiers par défaut et l’exploration des répertoires de `MyStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="87643-321">The following code enables static files, default files, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

<span data-ttu-id="87643-322">`AddDirectoryBrowser` doit être appelé quand la valeur de la propriété `EnableDirectoryBrowsing` est `true` :</span><span class="sxs-lookup"><span data-stu-id="87643-322">`AddDirectoryBrowser` must be called when the `EnableDirectoryBrowsing` property value is `true`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

<span data-ttu-id="87643-323">En utilisant la hiérarchie de fichiers et le code précédent, les URL sont résolues comme suit :</span><span class="sxs-lookup"><span data-stu-id="87643-323">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="87643-324">URI</span><span class="sxs-lookup"><span data-stu-id="87643-324">URI</span></span>            |                             <span data-ttu-id="87643-325">response</span><span class="sxs-lookup"><span data-stu-id="87643-325">Response</span></span>  |
| ------- | ------|
| <span data-ttu-id="87643-326">*http:// \<server_address> /StaticFiles/images/banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="87643-326">*http://\<server_address>/StaticFiles/images/banner1.svg*</span></span>    |      <span data-ttu-id="87643-327">MyStaticFiles/images/banner1.svg</span><span class="sxs-lookup"><span data-stu-id="87643-327">MyStaticFiles/images/banner1.svg</span></span> |
| <span data-ttu-id="87643-328">*http:// \<server_address> /StaticFiles*</span><span class="sxs-lookup"><span data-stu-id="87643-328">*http://\<server_address>/StaticFiles*</span></span>             |     <span data-ttu-id="87643-329">MyStaticFiles/default.html</span><span class="sxs-lookup"><span data-stu-id="87643-329">MyStaticFiles/default.html</span></span> |

<span data-ttu-id="87643-330">Si aucun fichier nommé par défaut n’existe dans le répertoire *MyStaticFiles* , *http:// \<server_address> /StaticFiles* retourne la liste des répertoires avec des liens interactives :</span><span class="sxs-lookup"><span data-stu-id="87643-330">If no default-named file exists in the *MyStaticFiles* directory, *http://\<server_address>/StaticFiles* returns the directory listing with clickable links:</span></span>

![Liste des fichiers statiques](static-files/_static/db2.png)

> [!NOTE]
> <span data-ttu-id="87643-332"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> et <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> effectuent une redirection côté client à partir de `http://{SERVER ADDRESS}/StaticFiles` (sans barre oblique) vers `http://{SERVER ADDRESS}/StaticFiles/` (avec barre oblique).</span><span class="sxs-lookup"><span data-stu-id="87643-332"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from `http://{SERVER ADDRESS}/StaticFiles` (without a trailing slash) to `http://{SERVER ADDRESS}/StaticFiles/` (with a trailing slash).</span></span> <span data-ttu-id="87643-333">Les URL relatives du répertoire *StaticFiles* ne sont pas valides sans barre oblique de fin.</span><span class="sxs-lookup"><span data-stu-id="87643-333">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash.</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="87643-334">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="87643-334">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="87643-335">La <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> classe contient une `Mappings` propriété servant de mappage d’extensions de fichier à des types de contenu MIME.</span><span class="sxs-lookup"><span data-stu-id="87643-335">The <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> class contains a `Mappings` property serving as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="87643-336">Dans l’exemple suivant, plusieurs extensions de fichiers sont inscrites avec des types MIME connus.</span><span class="sxs-lookup"><span data-stu-id="87643-336">In the following sample, several file extensions are registered to known MIME types.</span></span> <span data-ttu-id="87643-337">L’extension *.rtf* est remplacée et *.mp4* est supprimée.</span><span class="sxs-lookup"><span data-stu-id="87643-337">The *.rtf* extension is replaced, and *.mp4* is removed.</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

<span data-ttu-id="87643-338">Consultez [Types de contenu MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="87643-338">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

<span data-ttu-id="87643-339">Pour plus d’informations sur l’utilisation d’un personnalisé <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> ou pour configurer d’autres <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> Blazor applications serveur, consultez <xref:blazor/fundamentals/static-files> .</span><span class="sxs-lookup"><span data-stu-id="87643-339">For information on using a custom <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> or to configure other <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> in Blazor Server apps, see <xref:blazor/fundamentals/static-files>.</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="87643-340">Types de contenu non standard</span><span class="sxs-lookup"><span data-stu-id="87643-340">Non-standard content types</span></span>

<span data-ttu-id="87643-341">Static File Middleware comprend près de 400 types connus de contenu de fichier.</span><span class="sxs-lookup"><span data-stu-id="87643-341">Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="87643-342">Si l’utilisateur demande un fichier d’un type inconnu, Static File Middleware transmet la requête à l’intergiciel (middleware) suivant dans le pipeline.</span><span class="sxs-lookup"><span data-stu-id="87643-342">If the user requests a file with an unknown file type, Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="87643-343">Si aucun intergiciel ne gère la requête, une réponse *404 introuvable* est retournée.</span><span class="sxs-lookup"><span data-stu-id="87643-343">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="87643-344">Si l’exploration des répertoires est activée, un lien vers le fichier est affiché dans la liste de répertoires.</span><span class="sxs-lookup"><span data-stu-id="87643-344">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="87643-345">Le code suivant permet de délivrer des types inconnus et rend le fichier inconnu en tant qu’image :</span><span class="sxs-lookup"><span data-stu-id="87643-345">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

<span data-ttu-id="87643-346">Avec le code précédent, une requête pour un fichier avec un type de contenu inconnu est retournée en tant qu’image.</span><span class="sxs-lookup"><span data-stu-id="87643-346">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="87643-347">L’activation <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> de est un risque pour la sécurité.</span><span class="sxs-lookup"><span data-stu-id="87643-347">Enabling <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> is a security risk.</span></span> <span data-ttu-id="87643-348">Il est désactivé par défaut et son utilisation est déconseillée.</span><span class="sxs-lookup"><span data-stu-id="87643-348">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="87643-349">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) fournit une alternative plus sûre pour délivrer des fichiers avec des extensions non standard.</span><span class="sxs-lookup"><span data-stu-id="87643-349">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="87643-350">Servir des fichiers à partir de plusieurs emplacements</span><span class="sxs-lookup"><span data-stu-id="87643-350">Serve files from multiple locations</span></span>

<span data-ttu-id="87643-351">`UseStaticFiles``UseFileServer`la valeur par défaut est le fournisseur de fichiers qui pointe vers *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="87643-351">`UseStaticFiles` and `UseFileServer` defaults to the file provider pointing at *wwwroot*.</span></span> <span data-ttu-id="87643-352">Vous pouvez fournir des instances supplémentaires de `UseStaticFiles` et `UseFileServer` avec d’autres fournisseurs de fichiers pour servir des fichiers à partir d’autres emplacements.</span><span class="sxs-lookup"><span data-stu-id="87643-352">You can provide additional instances of `UseStaticFiles` and `UseFileServer` with other file providers to serve files from other locations.</span></span> <span data-ttu-id="87643-353">Pour plus d’informations, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span><span class="sxs-lookup"><span data-stu-id="87643-353">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

### <a name="considerations"></a><span data-ttu-id="87643-354">Considérations</span><span class="sxs-lookup"><span data-stu-id="87643-354">Considerations</span></span>

> [!WARNING]
> <span data-ttu-id="87643-355">`UseDirectoryBrowser` et `UseStaticFiles` peuvent entraîner une fuite de secrets.</span><span class="sxs-lookup"><span data-stu-id="87643-355">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="87643-356">La désactivation de l’exploration de répertoires est fortement recommandée en production.</span><span class="sxs-lookup"><span data-stu-id="87643-356">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="87643-357">Examinez attentivement les répertoires qui sont activés via `UseStaticFiles` ou `UseDirectoryBrowser`.</span><span class="sxs-lookup"><span data-stu-id="87643-357">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="87643-358">L’ensemble du répertoire et de ses sous-répertoires deviennent accessibles publiquement.</span><span class="sxs-lookup"><span data-stu-id="87643-358">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="87643-359">Stocker les fichiers pouvant être affectés au public dans un répertoire dédié, par exemple *\<content_root> /wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="87643-359">Store files suitable for serving to the public in a dedicated directory, such as *\<content_root>/wwwroot*.</span></span> <span data-ttu-id="87643-360">Séparez ces fichiers des vues MVC, des Razor pages (2. x uniquement), des fichiers de configuration, etc.</span><span class="sxs-lookup"><span data-stu-id="87643-360">Separate these files from MVC views, Razor Pages (2.x only), configuration files, etc.</span></span>

* <span data-ttu-id="87643-361">Les URL pour le contenu exposé avec `UseDirectoryBrowser` et `UseStaticFiles` sont soumises aux restrictions de respect de la casse et de caractères du système de fichiers sous-jacent.</span><span class="sxs-lookup"><span data-stu-id="87643-361">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="87643-362">Par exemple, Windows ne respecte pas la casse, mais macOS et Linux la respectent.</span><span class="sxs-lookup"><span data-stu-id="87643-362">For example, Windows is case insensitive&mdash;macOS and Linux aren't.</span></span>

* <span data-ttu-id="87643-363">Les applications ASP.NET Core hébergées dans IIS utilisent le [module ASP.NET Core](xref:host-and-deploy/aspnet-core-module) pour transférer toutes les requêtes à l’application, notamment les requêtes de fichiers statiques.</span><span class="sxs-lookup"><span data-stu-id="87643-363">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="87643-364">Le gestionnaire de fichiers statiques d’IIS n’est pas utilisé.</span><span class="sxs-lookup"><span data-stu-id="87643-364">The IIS static file handler isn't used.</span></span> <span data-ttu-id="87643-365">Il ne lui est pas permis de traiter les requêtes avant qu’elles soient gérées par le module.</span><span class="sxs-lookup"><span data-stu-id="87643-365">It has no chance to handle requests before they're handled by the module.</span></span>

* <span data-ttu-id="87643-366">Effectuez les étapes suivantes dans le Gestionnaire des services Internet (IIS) pour supprimer le gestionnaire de fichiers statiques d’IIS au niveau du serveur ou du site web :</span><span class="sxs-lookup"><span data-stu-id="87643-366">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="87643-367">Accédez à la fonctionnalité **Modules**.</span><span class="sxs-lookup"><span data-stu-id="87643-367">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="87643-368">Sélectionnez **StaticFileModule** dans la liste.</span><span class="sxs-lookup"><span data-stu-id="87643-368">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="87643-369">Cliquez sur **Supprimer** dans l’encadré **Actions**.</span><span class="sxs-lookup"><span data-stu-id="87643-369">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="87643-370">Si le gestionnaire de fichiers statiques d’IIS est activé **et** que le module ASP.NET Core est incorrectement configuré, les fichiers statiques peuvent être délivrés.</span><span class="sxs-lookup"><span data-stu-id="87643-370">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="87643-371">Cela se produit par exemple si le fichier *web.config* n’est pas déployé.</span><span class="sxs-lookup"><span data-stu-id="87643-371">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="87643-372">Placez les fichiers de code (y compris les fichiers *. cs* et *. cshtml*) en dehors de la [racine Web](xref:fundamentals/index#web-root)du projet d’application.</span><span class="sxs-lookup"><span data-stu-id="87643-372">Place code files (including *.cs* and *.cshtml*) outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="87643-373">Par conséquent, une séparation logique est créée entre le contenu côté client et le code basé sur le serveur de l’application.</span><span class="sxs-lookup"><span data-stu-id="87643-373">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="87643-374">Ceci empêche la fuite de code côté serveur.</span><span class="sxs-lookup"><span data-stu-id="87643-374">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="87643-375">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="87643-375">Additional resources</span></span>

* [<span data-ttu-id="87643-376">Middleware</span><span class="sxs-lookup"><span data-stu-id="87643-376">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="87643-377">Introduction à ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="87643-377">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end
