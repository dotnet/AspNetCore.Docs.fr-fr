---
title: Fichiers statiques dans ASP.NET Core
author: rick-anderson
description: Découvrez comment délivrer et sécuriser des fichiers statiques, et comment configurer le comportement d’un intergiciel (middleware) hébergeant des fichiers statiques dans une application web ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 6/23/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: fundamentals/static-files
ms.openlocfilehash: 2e25af03a8a6aaff5b343885711c6ebb68340fac
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057854"
---
# <a name="static-files-in-aspnet-core"></a><span data-ttu-id="d6214-103">Fichiers statiques dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d6214-103">Static files in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d6214-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="d6214-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="d6214-105">Les fichiers statiques, tels que HTML, CSS, images et JavaScript, sont des ressources qu’une ASP.NET Core application sert directement aux clients par défaut.</span><span class="sxs-lookup"><span data-stu-id="d6214-105">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients by default.</span></span>

<span data-ttu-id="d6214-106">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d6214-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="d6214-107">Délivrer des fichiers statiques</span><span class="sxs-lookup"><span data-stu-id="d6214-107">Serve static files</span></span>

<span data-ttu-id="d6214-108">Les fichiers statiques sont stockés dans le répertoire [racine Web](xref:fundamentals/index#web-root) du projet.</span><span class="sxs-lookup"><span data-stu-id="d6214-108">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="d6214-109">Le répertoire par défaut est `{content root}/wwwroot` , mais il peut être modifié à l’aide de la <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> méthode.</span><span class="sxs-lookup"><span data-stu-id="d6214-109">The default directory is `{content root}/wwwroot`, but it can be changed with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> method.</span></span> <span data-ttu-id="d6214-110">Pour plus d’informations, consultez [racine du contenu](xref:fundamentals/index#content-root) et [racine Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="d6214-110">For more information, see [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="d6214-111">La méthode <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> définit le répertoire actif comme racine du contenu :</span><span class="sxs-lookup"><span data-stu-id="d6214-111">The <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> method sets the content root to the current directory:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Program2.cs?name=snippet_Program)]

<span data-ttu-id="d6214-112">Le code précédent a été créé avec le modèle d’application Web.</span><span class="sxs-lookup"><span data-stu-id="d6214-112">The preceding code was created with the web app template.</span></span>

<span data-ttu-id="d6214-113">Les fichiers statiques sont accessibles via un chemin d’accès relatif à la [racine Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="d6214-113">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="d6214-114">Par exemple, les modèles de projet d' **application Web** contiennent plusieurs dossiers dans le `wwwroot` dossier :</span><span class="sxs-lookup"><span data-stu-id="d6214-114">For example, the **Web Application** project templates contain several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `js`
  * `lib`

<span data-ttu-id="d6214-115">Envisagez de créer le dossier *wwwroot/images* et d’ajouter le fichier *wwwroot/images/MyImage.jpg* .</span><span class="sxs-lookup"><span data-stu-id="d6214-115">Consider creating the *wwwroot/images* folder and adding the *wwwroot/images/MyImage.jpg* file.</span></span> <span data-ttu-id="d6214-116">Le format d’URI pour accéder à un fichier dans le `images` dossier est `https://<hostname>/images/<image_file_name>` .</span><span class="sxs-lookup"><span data-stu-id="d6214-116">The URI format to access a file in the `images` folder is `https://<hostname>/images/<image_file_name>`.</span></span> <span data-ttu-id="d6214-117">Par exemple : `https://localhost:5001/images/MyImage.jpg`</span><span class="sxs-lookup"><span data-stu-id="d6214-117">For example, `https://localhost:5001/images/MyImage.jpg`</span></span>

### <a name="serve-files-in-web-root"></a><span data-ttu-id="d6214-118">Traiter les fichiers dans la racine Web</span><span class="sxs-lookup"><span data-stu-id="d6214-118">Serve files in web root</span></span>

<span data-ttu-id="d6214-119">Les modèles d’application Web par défaut appellent la <xref:Owin.StaticFileExtensions.UseStaticFiles%2A> méthode dans, qui permet la prise en charge des `Startup.Configure` fichiers statiques :</span><span class="sxs-lookup"><span data-stu-id="d6214-119">The default web app templates call the <xref:Owin.StaticFileExtensions.UseStaticFiles%2A> method in `Startup.Configure`, which enables static files to be served:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="d6214-120">La `UseStaticFiles` surcharge de méthode sans paramètre marque les fichiers dans la [racine Web](xref:fundamentals/index#web-root) comme étant reservables.</span><span class="sxs-lookup"><span data-stu-id="d6214-120">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="d6214-121">Le balisage suivant référence *wwwroot/images/MyImage.jpg* :</span><span class="sxs-lookup"><span data-stu-id="d6214-121">The following markup references *wwwroot/images/MyImage.jpg* :</span></span>

```html
<img src="~/images/MyImage.jpg" class="img" alt="My image" />
```

<span data-ttu-id="d6214-122">Dans le code précédent, le caractère tilde `~/` pointe vers la [racine Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="d6214-122">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="d6214-123">Délivrer des fichiers en dehors de la racine web</span><span class="sxs-lookup"><span data-stu-id="d6214-123">Serve files outside of web root</span></span>

<span data-ttu-id="d6214-124">Prenons l’exemple d’une hiérarchie de répertoires dans laquelle les fichiers statiques à prendre en charge se trouvent en dehors de la [racine Web](xref:fundamentals/index#web-root):</span><span class="sxs-lookup"><span data-stu-id="d6214-124">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `red-rose.jpg`

<span data-ttu-id="d6214-125">Une demande peut accéder au `red-rose.jpg` fichier en configurant l’intergiciel de fichiers statiques comme suit :</span><span class="sxs-lookup"><span data-stu-id="d6214-125">A request can access the `red-rose.jpg` file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupRose.cs?name=snippet_Configure&highlight=15-22)]

<span data-ttu-id="d6214-126">Dans le code précédent, la hiérarchie de répertoires *MyStaticFiles* est exposée publiquement via le segment d’URI *StaticFiles* .</span><span class="sxs-lookup"><span data-stu-id="d6214-126">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="d6214-127">Demande de `https://<hostname>/StaticFiles/images/red-rose.jpg` servir le fichier *red-rose.jpg* .</span><span class="sxs-lookup"><span data-stu-id="d6214-127">A request to `https://<hostname>/StaticFiles/images/red-rose.jpg` serves the *red-rose.jpg* file.</span></span>

<span data-ttu-id="d6214-128">Le balisage suivant référence *MyStaticFiles/images/red-rose.jpg* :</span><span class="sxs-lookup"><span data-stu-id="d6214-128">The following markup references *MyStaticFiles/images/red-rose.jpg* :</span></span>

```html
<img src="~/StaticFiles/images/red-rose.jpg" class="img" alt="A red rose" />
```

### <a name="set-http-response-headers"></a><span data-ttu-id="d6214-129">Définir des en-têtes de réponse HTTP</span><span class="sxs-lookup"><span data-stu-id="d6214-129">Set HTTP response headers</span></span>

<span data-ttu-id="d6214-130">Un <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> objet peut être utilisé pour définir des en-têtes de réponse http.</span><span class="sxs-lookup"><span data-stu-id="d6214-130">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="d6214-131">En plus de configurer le service de fichiers statiques à partir de la [racine Web](xref:fundamentals/index#web-root), le code suivant définit l' `Cache-Control` en-tête :</span><span class="sxs-lookup"><span data-stu-id="d6214-131">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_Configure&highlight=15-24)]

<!-- Q: The preceding code sets max-age to 604800 seconds (7 days), so what does the following mean? -->

<span data-ttu-id="d6214-132">Les fichiers statiques sont publiquement mis en cache pendant 600 secondes :</span><span class="sxs-lookup"><span data-stu-id="d6214-132">Static files are publicly cacheable for 600 seconds:</span></span>

![En-têtes de réponse montrant que l’en-tête Cache-Control a été ajouté](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="d6214-134">Autorisations des fichiers statiques</span><span class="sxs-lookup"><span data-stu-id="d6214-134">Static file authorization</span></span>

<span data-ttu-id="d6214-135">Le middleware de fichiers statiques ne fournit pas de vérification des autorisations.</span><span class="sxs-lookup"><span data-stu-id="d6214-135">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="d6214-136">Tous les fichiers desservis par celui-ci, y compris ceux sous `wwwroot` , sont accessibles publiquement.</span><span class="sxs-lookup"><span data-stu-id="d6214-136">Any files served by it, including those under `wwwroot`, are publicly accessible.</span></span> <span data-ttu-id="d6214-137">Pour délivrer des fichiers en fonction d’une autorisation :</span><span class="sxs-lookup"><span data-stu-id="d6214-137">To serve files based on authorization:</span></span>

* <span data-ttu-id="d6214-138">Stockez-les en dehors de `wwwroot` et de tout répertoire accessible à l’intergiciel de fichiers statiques par défaut.</span><span class="sxs-lookup"><span data-stu-id="d6214-138">Store them outside of `wwwroot` and any directory accessible to the default Static File Middleware.</span></span>
* <span data-ttu-id="d6214-139">Appelez `UseStaticFiles` après `UseAuthorization` et spécifiez le chemin d’accès :</span><span class="sxs-lookup"><span data-stu-id="d6214-139">Call `UseStaticFiles` after `UseAuthorization` and specify the path:</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet2)]
  
  <span data-ttu-id="d6214-140">L’approche précédente nécessite l’authentification des utilisateurs :</span><span class="sxs-lookup"><span data-stu-id="d6214-140">The preceding approach requires users to be authenticated:</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet1&highlight=20-99)]

   [!INCLUDE[](~/includes/requireAuth.md)]

<span data-ttu-id="d6214-141">Une autre approche pour traiter les fichiers en fonction de l’autorisation :</span><span class="sxs-lookup"><span data-stu-id="d6214-141">An alternative approach to serve files based on authorization:</span></span>

* <span data-ttu-id="d6214-142">Stockez-les en dehors de `wwwroot` et de tout répertoire accessible à l’intergiciel de fichiers statiques.</span><span class="sxs-lookup"><span data-stu-id="d6214-142">Store them outside of `wwwroot` and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="d6214-143">Servez-vous via une méthode d’action à laquelle l’autorisation est appliquée et retournez un <xref:Microsoft.AspNetCore.Mvc.FileResult> objet :</span><span class="sxs-lookup"><span data-stu-id="d6214-143">Serve them via an action method to which authorization is applied and return a <xref:Microsoft.AspNetCore.Mvc.FileResult> object:</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImage)]

## <a name="directory-browsing"></a><span data-ttu-id="d6214-144">Exploration de répertoires</span><span class="sxs-lookup"><span data-stu-id="d6214-144">Directory browsing</span></span>

<span data-ttu-id="d6214-145">L’exploration des répertoires permet la liste des répertoires dans les répertoires spécifiés.</span><span class="sxs-lookup"><span data-stu-id="d6214-145">Directory browsing allows directory listing within specified directories.</span></span>

<span data-ttu-id="d6214-146">L’exploration des répertoires est désactivée par défaut pour des raisons de sécurité.</span><span class="sxs-lookup"><span data-stu-id="d6214-146">Directory browsing is disabled by default for security reasons.</span></span> <span data-ttu-id="d6214-147">Pour plus d’informations, consultez [considérations](#considerations).</span><span class="sxs-lookup"><span data-stu-id="d6214-147">For more information, see [Considerations](#considerations).</span></span>

<span data-ttu-id="d6214-148">Activer l’exploration des répertoires avec :</span><span class="sxs-lookup"><span data-stu-id="d6214-148">Enable directory browsing with:</span></span>

* <span data-ttu-id="d6214-149"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d6214-149"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="d6214-150"><xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> dans `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="d6214-150"><xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> in `Startup.Configure`.</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ClassMembers&highlight=4,21-35)]

<span data-ttu-id="d6214-151">Le code précédent autorise l’exploration des répertoires du dossier *wwwroot/images* à l’aide de l’URL `https://<hostname>/MyImages` , avec des liens vers chaque fichier et dossier :</span><span class="sxs-lookup"><span data-stu-id="d6214-151">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL `https://<hostname>/MyImages`, with links to each file and folder:</span></span>

![exploration des répertoires](static-files/_static/dir-browse.png)

## <a name="serve-default-documents"></a><span data-ttu-id="d6214-153">Traiter les documents par défaut</span><span class="sxs-lookup"><span data-stu-id="d6214-153">Serve default documents</span></span>

<span data-ttu-id="d6214-154">La définition d’une page par défaut fournit aux visiteurs un point de départ sur un site.</span><span class="sxs-lookup"><span data-stu-id="d6214-154">Setting a default page provides visitors a starting point on a site.</span></span> <span data-ttu-id="d6214-155">Pour traiter une page par défaut `wwwroot` sans URI complet, appelez la <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A> méthode :</span><span class="sxs-lookup"><span data-stu-id="d6214-155">To serve a default page from `wwwroot` without a fully qualified URI, call the <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A> method:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="d6214-156">`UseDefaultFiles` doit être appelé avant `UseStaticFiles` pour délivrer le fichier par défaut.</span><span class="sxs-lookup"><span data-stu-id="d6214-156">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="d6214-157">`UseDefaultFiles` est un ReWriter d’URL qui ne traite pas le fichier.</span><span class="sxs-lookup"><span data-stu-id="d6214-157">`UseDefaultFiles` is a URL rewriter that doesn't serve the file.</span></span>

<span data-ttu-id="d6214-158">Avec `UseDefaultFiles` , les demandes à un dossier dans `wwwroot` Rechercher sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="d6214-158">With `UseDefaultFiles`, requests to a folder in `wwwroot` search for:</span></span>

* <span data-ttu-id="d6214-159">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="d6214-159">*default.htm*</span></span>
* <span data-ttu-id="d6214-160">*default.html*</span><span class="sxs-lookup"><span data-stu-id="d6214-160">*default.html*</span></span>
* <span data-ttu-id="d6214-161">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="d6214-161">*index.htm*</span></span>
* <span data-ttu-id="d6214-162">*index.html*</span><span class="sxs-lookup"><span data-stu-id="d6214-162">*index.html*</span></span>

<span data-ttu-id="d6214-163">Le premier fichier trouvé dans la liste est délivré comme si la requête était l’URI qualifié complet.</span><span class="sxs-lookup"><span data-stu-id="d6214-163">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="d6214-164">L’URL du navigateur continue de refléter l’URI demandé.</span><span class="sxs-lookup"><span data-stu-id="d6214-164">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="d6214-165">Le code suivant change le nom de fichier par défaut en *mydefault.html* :</span><span class="sxs-lookup"><span data-stu-id="d6214-165">The following code changes the default file name to *mydefault.html* :</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_DefaultFiles)]

<span data-ttu-id="d6214-166">Le code suivant est affiché `Startup.Configure` avec le code précédent :</span><span class="sxs-lookup"><span data-stu-id="d6214-166">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_Configure&highlight=15-19)]

### <a name="usefileserver-for-default-documents"></a><span data-ttu-id="d6214-167">UseFileServer pour les documents par défaut</span><span class="sxs-lookup"><span data-stu-id="d6214-167">UseFileServer for default documents</span></span>

<span data-ttu-id="d6214-168"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combine les fonctionnalités de `UseStaticFiles` , `UseDefaultFiles` et éventuellement `UseDirectoryBrowser` .</span><span class="sxs-lookup"><span data-stu-id="d6214-168"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="d6214-169">Appelez `app.UseFileServer` pour activer le service des fichiers statiques et le fichier par défaut.</span><span class="sxs-lookup"><span data-stu-id="d6214-169">Call `app.UseFileServer` to enable the serving of static files and the default file.</span></span> <span data-ttu-id="d6214-170">L’exploration des répertoires n’est pas activée.</span><span class="sxs-lookup"><span data-stu-id="d6214-170">Directory browsing isn't enabled.</span></span> <span data-ttu-id="d6214-171">Le code suivant est affiché `Startup.Configure` avec `UseFileServer` :</span><span class="sxs-lookup"><span data-stu-id="d6214-171">The following code shows `Startup.Configure` with `UseFileServer`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty2.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="d6214-172">Le code suivant permet le service des fichiers statiques, le fichier par défaut et l’exploration des répertoires :</span><span class="sxs-lookup"><span data-stu-id="d6214-172">The following code enables the serving of static files, the default file, and directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="d6214-173">Le code suivant est affiché `Startup.Configure` avec le code précédent :</span><span class="sxs-lookup"><span data-stu-id="d6214-173">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty3.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="d6214-174">Considérez la hiérarchie de répertoires suivante :</span><span class="sxs-lookup"><span data-stu-id="d6214-174">Consider the following directory hierarchy:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `MyImage.jpg`
  * `default.html`

<span data-ttu-id="d6214-175">Le code suivant permet le service des fichiers statiques, le fichier par défaut et l’exploration des répertoires de `MyStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="d6214-175">The following code enables the serving of static files, the default file, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileServer.cs?name=snippet_ClassMembers&highlight=4,21-31)]

<span data-ttu-id="d6214-176"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> doit être appelé lorsque la `EnableDirectoryBrowsing` valeur de la propriété est `true` .</span><span class="sxs-lookup"><span data-stu-id="d6214-176"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> must be called when the `EnableDirectoryBrowsing` property value is `true`.</span></span>

<span data-ttu-id="d6214-177">En utilisant la hiérarchie de fichiers et le code précédent, les URL sont résolues comme suit :</span><span class="sxs-lookup"><span data-stu-id="d6214-177">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="d6214-178">URI</span><span class="sxs-lookup"><span data-stu-id="d6214-178">URI</span></span>            |      <span data-ttu-id="d6214-179">response</span><span class="sxs-lookup"><span data-stu-id="d6214-179">Response</span></span>  |
| ------- | ------|
| `https://<hostname>/StaticFiles/images/MyImage.jpg` | <span data-ttu-id="d6214-180">*MyStaticFiles/images/MyImage.jpg*</span><span class="sxs-lookup"><span data-stu-id="d6214-180">*MyStaticFiles/images/MyImage.jpg*</span></span> |
| `https://<hostname>/StaticFiles` | <span data-ttu-id="d6214-181">*MyStaticFiles/default.html*</span><span class="sxs-lookup"><span data-stu-id="d6214-181">*MyStaticFiles/default.html*</span></span> |

<span data-ttu-id="d6214-182">Si aucun fichier nommé par défaut n’existe dans le répertoire *MyStaticFiles* , `https://<hostname>/StaticFiles` retourne la liste des répertoires avec des liens interactives :</span><span class="sxs-lookup"><span data-stu-id="d6214-182">If no default-named file exists in the *MyStaticFiles* directory, `https://<hostname>/StaticFiles` returns the directory listing with clickable links:</span></span>

![Liste des fichiers statiques](static-files/_static/db2.png)

<span data-ttu-id="d6214-184"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> et <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> effectuent une redirection côté client à partir de l’URI cible sans fin `/`  vers l’URI cible avec une fin `/` .</span><span class="sxs-lookup"><span data-stu-id="d6214-184"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from the target URI without a trailing `/`  to the target URI with a trailing `/`.</span></span> <span data-ttu-id="d6214-185">Par exemple, de `https://<hostname>/StaticFiles` à `https://<hostname>/StaticFiles/` .</span><span class="sxs-lookup"><span data-stu-id="d6214-185">For example, from `https://<hostname>/StaticFiles` to `https://<hostname>/StaticFiles/`.</span></span> <span data-ttu-id="d6214-186">Les URL relatives dans le répertoire *StaticFiles* ne sont pas valides sans barre oblique finale ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="d6214-186">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash (`/`).</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="d6214-187">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="d6214-187">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="d6214-188">La <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> classe contient une `Mappings` propriété qui sert de mappage des extensions de fichier aux types de contenu MIME.</span><span class="sxs-lookup"><span data-stu-id="d6214-188">The <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> class contains a `Mappings` property that serves as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="d6214-189">Dans l’exemple suivant, plusieurs extensions de fichier sont mappées à des types MIME connus.</span><span class="sxs-lookup"><span data-stu-id="d6214-189">In the following sample, several file extensions are mapped to known MIME types.</span></span> <span data-ttu-id="d6214-190">L’extension *. rtf* est remplacée et *. MP4* est supprimé :</span><span class="sxs-lookup"><span data-stu-id="d6214-190">The *.rtf* extension is replaced, and *.mp4* is removed:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Provider)]

<span data-ttu-id="d6214-191">Le code suivant est affiché `Startup.Configure` avec le code précédent :</span><span class="sxs-lookup"><span data-stu-id="d6214-191">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Configure&highlight=15-43)]

<span data-ttu-id="d6214-192">Consultez [Types de contenu MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="d6214-192">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="d6214-193">Types de contenu non standard</span><span class="sxs-lookup"><span data-stu-id="d6214-193">Non-standard content types</span></span>

<span data-ttu-id="d6214-194">L’intergiciel de fichiers statiques comprend presque 400 types de contenu de fichier connus.</span><span class="sxs-lookup"><span data-stu-id="d6214-194">The Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="d6214-195">Si l’utilisateur demande un fichier dont le type de fichier est inconnu, l’intergiciel (middleware) de fichiers statique transmet la demande à l’intergiciel (middleware) suivant dans le pipeline.</span><span class="sxs-lookup"><span data-stu-id="d6214-195">If the user requests a file with an unknown file type, the Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="d6214-196">Si aucun intergiciel ne gère la requête, une réponse *404 introuvable* est retournée.</span><span class="sxs-lookup"><span data-stu-id="d6214-196">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="d6214-197">Si l’exploration des répertoires est activée, un lien vers le fichier est affiché dans la liste de répertoires.</span><span class="sxs-lookup"><span data-stu-id="d6214-197">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="d6214-198">Le code suivant permet de délivrer des types inconnus et rend le fichier inconnu en tant qu’image :</span><span class="sxs-lookup"><span data-stu-id="d6214-198">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_UseStaticFiles)]

<span data-ttu-id="d6214-199">Le code suivant est affiché `Startup.Configure` avec le code précédent :</span><span class="sxs-lookup"><span data-stu-id="d6214-199">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_Configure&highlight=15-19)]

<span data-ttu-id="d6214-200">Avec le code précédent, une requête pour un fichier avec un type de contenu inconnu est retournée en tant qu’image.</span><span class="sxs-lookup"><span data-stu-id="d6214-200">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="d6214-201">L’activation <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> de est un risque pour la sécurité.</span><span class="sxs-lookup"><span data-stu-id="d6214-201">Enabling <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> is a security risk.</span></span> <span data-ttu-id="d6214-202">Il est désactivé par défaut et son utilisation est déconseillée.</span><span class="sxs-lookup"><span data-stu-id="d6214-202">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="d6214-203">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) fournit une alternative plus sûre pour délivrer des fichiers avec des extensions non standard.</span><span class="sxs-lookup"><span data-stu-id="d6214-203">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="d6214-204">Servir des fichiers à partir de plusieurs emplacements</span><span class="sxs-lookup"><span data-stu-id="d6214-204">Serve files from multiple locations</span></span>

<span data-ttu-id="d6214-205">`UseStaticFiles` et `UseFileServer` par défaut, le fournisseur de fichiers pointe sur `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="d6214-205">`UseStaticFiles` and `UseFileServer` default to the file provider pointing at `wwwroot`.</span></span> <span data-ttu-id="d6214-206">Des instances supplémentaires de `UseStaticFiles` et `UseFileServer` peuvent être fournies avec d’autres fournisseurs de fichiers pour servir des fichiers à partir d’autres emplacements.</span><span class="sxs-lookup"><span data-stu-id="d6214-206">Additional instances of `UseStaticFiles` and `UseFileServer` can be provided with other file providers to serve files from other locations.</span></span> <span data-ttu-id="d6214-207">Pour plus d’informations, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span><span class="sxs-lookup"><span data-stu-id="d6214-207">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

<a name="sc"></a>

### <a name="security-considerations-for-static-files"></a><span data-ttu-id="d6214-208">Considérations sur la sécurité des fichiers statiques</span><span class="sxs-lookup"><span data-stu-id="d6214-208">Security considerations for static files</span></span>

> [!WARNING]
> <span data-ttu-id="d6214-209">`UseDirectoryBrowser` et `UseStaticFiles` peuvent entraîner une fuite de secrets.</span><span class="sxs-lookup"><span data-stu-id="d6214-209">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="d6214-210">La désactivation de l’exploration de répertoires est fortement recommandée en production.</span><span class="sxs-lookup"><span data-stu-id="d6214-210">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="d6214-211">Examinez attentivement les répertoires qui sont activés via `UseStaticFiles` ou `UseDirectoryBrowser`.</span><span class="sxs-lookup"><span data-stu-id="d6214-211">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="d6214-212">L’ensemble du répertoire et de ses sous-répertoires deviennent accessibles publiquement.</span><span class="sxs-lookup"><span data-stu-id="d6214-212">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="d6214-213">Stocker les fichiers pouvant être affectés au public dans un répertoire dédié, tel que `<content_root>/wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="d6214-213">Store files suitable for serving to the public in a dedicated directory, such as `<content_root>/wwwroot`.</span></span> <span data-ttu-id="d6214-214">Séparez ces fichiers des vues MVC, des :::no-loc(Razor)::: pages, des fichiers de configuration, etc.</span><span class="sxs-lookup"><span data-stu-id="d6214-214">Separate these files from MVC views, :::no-loc(Razor)::: Pages, configuration files, etc.</span></span>

* <span data-ttu-id="d6214-215">Les URL pour le contenu exposé avec `UseDirectoryBrowser` et `UseStaticFiles` sont soumises aux restrictions de respect de la casse et de caractères du système de fichiers sous-jacent.</span><span class="sxs-lookup"><span data-stu-id="d6214-215">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="d6214-216">Par exemple, Windows ne respecte pas la casse, contrairement à macOS et Linux.</span><span class="sxs-lookup"><span data-stu-id="d6214-216">For example, Windows is case insensitive, but macOS and Linux aren't.</span></span>

* <span data-ttu-id="d6214-217">Les applications ASP.NET Core hébergées dans IIS utilisent le [module ASP.NET Core](xref:host-and-deploy/aspnet-core-module) pour transférer toutes les requêtes à l’application, notamment les requêtes de fichiers statiques.</span><span class="sxs-lookup"><span data-stu-id="d6214-217">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="d6214-218">Le gestionnaire de fichiers statiques IIS n’est pas utilisé et n’a pas la possibilité de traiter les demandes.</span><span class="sxs-lookup"><span data-stu-id="d6214-218">The IIS static file handler isn't used and has no chance to handle requests.</span></span>

* <span data-ttu-id="d6214-219">Effectuez les étapes suivantes dans le Gestionnaire des services Internet (IIS) pour supprimer le gestionnaire de fichiers statiques d’IIS au niveau du serveur ou du site web :</span><span class="sxs-lookup"><span data-stu-id="d6214-219">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="d6214-220">Accédez à la fonctionnalité **Modules** .</span><span class="sxs-lookup"><span data-stu-id="d6214-220">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="d6214-221">Sélectionnez **StaticFileModule** dans la liste.</span><span class="sxs-lookup"><span data-stu-id="d6214-221">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="d6214-222">Cliquez sur **Supprimer** dans l’encadré **Actions** .</span><span class="sxs-lookup"><span data-stu-id="d6214-222">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="d6214-223">Si le gestionnaire de fichiers statiques d’IIS est activé **et** que le module ASP.NET Core est incorrectement configuré, les fichiers statiques peuvent être délivrés.</span><span class="sxs-lookup"><span data-stu-id="d6214-223">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="d6214-224">Cela se produit par exemple si le fichier *web.config* n’est pas déployé.</span><span class="sxs-lookup"><span data-stu-id="d6214-224">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="d6214-225">Placez les fichiers de code, y compris les fichiers *. cs* et *. cshtml* , en dehors de la [racine Web](xref:fundamentals/index#web-root)du projet d’application.</span><span class="sxs-lookup"><span data-stu-id="d6214-225">Place code files, including *.cs* and *.cshtml* , outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="d6214-226">Par conséquent, une séparation logique est créée entre le contenu côté client et le code basé sur le serveur de l’application.</span><span class="sxs-lookup"><span data-stu-id="d6214-226">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="d6214-227">Ceci empêche la fuite de code côté serveur.</span><span class="sxs-lookup"><span data-stu-id="d6214-227">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d6214-228">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="d6214-228">Additional resources</span></span>

* [<span data-ttu-id="d6214-229">Middleware</span><span class="sxs-lookup"><span data-stu-id="d6214-229">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="d6214-230">Introduction à ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d6214-230">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d6214-231">Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="d6214-231">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="d6214-232">Les fichiers statiques, comme les fichiers HTML, CSS, images et JavaScript, sont des ressources qu’une application ASP.NET Core délivre directement aux clients.</span><span class="sxs-lookup"><span data-stu-id="d6214-232">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients.</span></span> <span data-ttu-id="d6214-233">Une configuration est nécessaire pour pouvoir délivrer ces fichiers.</span><span class="sxs-lookup"><span data-stu-id="d6214-233">Some configuration is required to enable serving of these files.</span></span>

<span data-ttu-id="d6214-234">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d6214-234">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="d6214-235">Délivrer des fichiers statiques</span><span class="sxs-lookup"><span data-stu-id="d6214-235">Serve static files</span></span>

<span data-ttu-id="d6214-236">Les fichiers statiques sont stockés dans le répertoire [racine Web](xref:fundamentals/index#web-root) du projet.</span><span class="sxs-lookup"><span data-stu-id="d6214-236">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="d6214-237">Le répertoire par défaut est *{content root}/wwwroot* , mais il peut être modifié à l’aide de la <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> méthode.</span><span class="sxs-lookup"><span data-stu-id="d6214-237">The default directory is *{content root}/wwwroot* , but it can be changed via the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> method.</span></span> <span data-ttu-id="d6214-238">Pour plus d’informations, consultez [Racine du contenu](xref:fundamentals/index#content-root) et [Racine web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="d6214-238">See [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root) for more information.</span></span>

<span data-ttu-id="d6214-239">L’hôte web de l’application doit être informé du répertoire racine du contenu.</span><span class="sxs-lookup"><span data-stu-id="d6214-239">The app's web host must be made aware of the content root directory.</span></span>

<span data-ttu-id="d6214-240">La méthode `WebHost.CreateDefaultBuilder` définit le répertoire actif comme racine du contenu :</span><span class="sxs-lookup"><span data-stu-id="d6214-240">The `WebHost.CreateDefaultBuilder` method sets the content root to the current directory:</span></span>

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

<span data-ttu-id="d6214-241">Les fichiers statiques sont accessibles via un chemin d’accès relatif à la [racine Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="d6214-241">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="d6214-242">Par exemple, le modèle de projet d' **application Web** contient plusieurs dossiers dans le `wwwroot` dossier :</span><span class="sxs-lookup"><span data-stu-id="d6214-242">For example, the **Web Application** project template contains several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`

<span data-ttu-id="d6214-243">Le format d’URI permettant d’accéder à un fichier dans le sous-dossier *images* est *http:// \<server_address> /images/ \<image_file_name>* .</span><span class="sxs-lookup"><span data-stu-id="d6214-243">The URI format to access a file in the *images* subfolder is *http://\<server_address>/images/\<image_file_name>* .</span></span> <span data-ttu-id="d6214-244">Par exemple : *http://localhost:9189/images/banner3.svg* .</span><span class="sxs-lookup"><span data-stu-id="d6214-244">For example, *http://localhost:9189/images/banner3.svg* .</span></span>

<span data-ttu-id="d6214-245">Si vous ciblez .NET Framework, ajoutez le package [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) au projet.</span><span class="sxs-lookup"><span data-stu-id="d6214-245">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span> <span data-ttu-id="d6214-246">Si vous ciblez .NET Core, le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) comprend ce package.</span><span class="sxs-lookup"><span data-stu-id="d6214-246">If targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) includes this package.</span></span>

<span data-ttu-id="d6214-247">Configurez l' [intergiciel (middleware](xref:fundamentals/middleware/index)), qui permet de servir des fichiers statiques.</span><span class="sxs-lookup"><span data-stu-id="d6214-247">Configure the [middleware](xref:fundamentals/middleware/index), which enables the serving of static files.</span></span>

### <a name="serve-files-inside-of-web-root"></a><span data-ttu-id="d6214-248">Délivrer des fichiers dans la racine web</span><span class="sxs-lookup"><span data-stu-id="d6214-248">Serve files inside of web root</span></span>

<span data-ttu-id="d6214-249">Appelez la <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> méthode dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="d6214-249">Invoke the <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> method within `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

<span data-ttu-id="d6214-250">La `UseStaticFiles` surcharge de méthode sans paramètre marque les fichiers dans la [racine Web](xref:fundamentals/index#web-root) comme étant reservables.</span><span class="sxs-lookup"><span data-stu-id="d6214-250">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="d6214-251">Le balisage suivant référence *wwwroot/images/banner1.svg* :</span><span class="sxs-lookup"><span data-stu-id="d6214-251">The following markup references *wwwroot/images/banner1.svg* :</span></span>

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

<span data-ttu-id="d6214-252">Dans le code précédent, le caractère tilde `~/` pointe vers la [racine Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="d6214-252">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="d6214-253">Délivrer des fichiers en dehors de la racine web</span><span class="sxs-lookup"><span data-stu-id="d6214-253">Serve files outside of web root</span></span>

<span data-ttu-id="d6214-254">Prenons l’exemple d’une hiérarchie de répertoires dans laquelle les fichiers statiques à prendre en charge se trouvent en dehors de la [racine Web](xref:fundamentals/index#web-root):</span><span class="sxs-lookup"><span data-stu-id="d6214-254">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `banner1.svg`

<span data-ttu-id="d6214-255">Vous permettez à une requête d’accéder au fichier *banner1.svg* en configurant le middleware (intergiciel) des fichiers statiques comme suit :</span><span class="sxs-lookup"><span data-stu-id="d6214-255">A request can access the *banner1.svg* file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

<span data-ttu-id="d6214-256">Dans le code précédent, la hiérarchie de répertoires *MyStaticFiles* est exposée publiquement via le segment d’URI *StaticFiles* .</span><span class="sxs-lookup"><span data-stu-id="d6214-256">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="d6214-257">Une demande à *http:// \<server_address> /StaticFiles/images/banner1.svg* sert le fichier *banner1. svg* .</span><span class="sxs-lookup"><span data-stu-id="d6214-257">A request to *http://\<server_address>/StaticFiles/images/banner1.svg* serves the *banner1.svg* file.</span></span>

<span data-ttu-id="d6214-258">Le balisage suivant référence *MyStaticFiles/images/banner1.svg* :</span><span class="sxs-lookup"><span data-stu-id="d6214-258">The following markup references *MyStaticFiles/images/banner1.svg* :</span></span>

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a><span data-ttu-id="d6214-259">Définir des en-têtes de réponse HTTP</span><span class="sxs-lookup"><span data-stu-id="d6214-259">Set HTTP response headers</span></span>

<span data-ttu-id="d6214-260">Un <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> objet peut être utilisé pour définir des en-têtes de réponse http.</span><span class="sxs-lookup"><span data-stu-id="d6214-260">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="d6214-261">En plus de configurer le service de fichiers statiques à partir de la [racine Web](xref:fundamentals/index#web-root), le code suivant définit l' `Cache-Control` en-tête :</span><span class="sxs-lookup"><span data-stu-id="d6214-261">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="d6214-262">La <xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType> méthode existe dans le package [Microsoft. AspNetCore. http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) .</span><span class="sxs-lookup"><span data-stu-id="d6214-262">The <xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType> method exists in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

<span data-ttu-id="d6214-263">Les fichiers peuvent être mis en cache publiquement pendant 10 minutes (600 secondes) dans l’environnement de développement :</span><span class="sxs-lookup"><span data-stu-id="d6214-263">The files have been made publicly cacheable for 10 minutes (600 seconds) in the Development environment:</span></span>

![En-têtes de réponse montrant que l’en-tête Cache-Control a été ajouté](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="d6214-265">Autorisations des fichiers statiques</span><span class="sxs-lookup"><span data-stu-id="d6214-265">Static file authorization</span></span>

<span data-ttu-id="d6214-266">Le middleware de fichiers statiques ne fournit pas de vérification des autorisations.</span><span class="sxs-lookup"><span data-stu-id="d6214-266">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="d6214-267">Tous les fichiers qu’il délivre, notamment ceux sous *wwwroot* , sont accessibles publiquement.</span><span class="sxs-lookup"><span data-stu-id="d6214-267">Any files served by it, including those under *wwwroot* , are publicly accessible.</span></span> <span data-ttu-id="d6214-268">Pour délivrer des fichiers en fonction d’une autorisation :</span><span class="sxs-lookup"><span data-stu-id="d6214-268">To serve files based on authorization:</span></span>

* <span data-ttu-id="d6214-269">Stockez-les en dehors de *wwwroot* et de tout répertoire accessible au middleware de fichiers statiques.</span><span class="sxs-lookup"><span data-stu-id="d6214-269">Store them outside of *wwwroot* and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="d6214-270">Délivrez-les via une méthode d’action à laquelle une autorisation est appliquée.</span><span class="sxs-lookup"><span data-stu-id="d6214-270">Serve them via an action method to which authorization is applied.</span></span> <span data-ttu-id="d6214-271">Retourne un <xref:Microsoft.AspNetCore.Mvc.FileResult> objet :</span><span class="sxs-lookup"><span data-stu-id="d6214-271">Return a <xref:Microsoft.AspNetCore.Mvc.FileResult> object:</span></span>

  [!code-csharp[](static-files/samples/1.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a><span data-ttu-id="d6214-272">Activer l’exploration des répertoires</span><span class="sxs-lookup"><span data-stu-id="d6214-272">Enable directory browsing</span></span>

<span data-ttu-id="d6214-273">L’exploration des répertoires permet aux utilisateurs de votre application web de voir une liste des répertoires et des fichiers dans un répertoire spécifié.</span><span class="sxs-lookup"><span data-stu-id="d6214-273">Directory browsing allows users of your web app to see a directory listing and files within a specified directory.</span></span> <span data-ttu-id="d6214-274">L’exploration des répertoires est désactivée par défaut pour des raisons de sécurité (consultez [Considérations](#considerations)).</span><span class="sxs-lookup"><span data-stu-id="d6214-274">Directory browsing is disabled by default for security reasons (see [Considerations](#considerations)).</span></span> <span data-ttu-id="d6214-275">Activez l’exploration des répertoires en appelant la <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> méthode dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="d6214-275">Enable directory browsing by invoking the <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> method in `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

<span data-ttu-id="d6214-276">Ajoutez les services requis en appelant la <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> méthode à partir de `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d6214-276">Add required services by invoking the <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> method from `Startup.ConfigureServices`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

<span data-ttu-id="d6214-277">Le code précédent autorise l’exploration des répertoires du dossier *wwwroot/images* à l’aide de l’URL *http:// \<server_address> /myImages* , avec des liens vers chaque fichier et dossier :</span><span class="sxs-lookup"><span data-stu-id="d6214-277">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages* , with links to each file and folder:</span></span>

![exploration des répertoires](static-files/_static/dir-browse.png)

<span data-ttu-id="d6214-279">Consultez [Considérations](#considerations) sur les risques de sécurité lors de l’activation de l’exploration.</span><span class="sxs-lookup"><span data-stu-id="d6214-279">See [Considerations](#considerations) on the security risks when enabling browsing.</span></span>

<span data-ttu-id="d6214-280">Notez les deux appels de `UseStaticFiles` dans l’exemple suivant.</span><span class="sxs-lookup"><span data-stu-id="d6214-280">Note the two `UseStaticFiles` calls in the following example.</span></span> <span data-ttu-id="d6214-281">Le premier appel permet de délivrer des fichiers statiques dans le dossier *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="d6214-281">The first call enables the serving of static files in the *wwwroot* folder.</span></span> <span data-ttu-id="d6214-282">Le deuxième appel active l’exploration des répertoires du dossier *wwwroot/images* à l’aide de l’URL *http:// \<server_address> /myImages* :</span><span class="sxs-lookup"><span data-stu-id="d6214-282">The second call enables directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages* :</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a><span data-ttu-id="d6214-283">Délivrer un document par défaut</span><span class="sxs-lookup"><span data-stu-id="d6214-283">Serve a default document</span></span>

<span data-ttu-id="d6214-284">La définition d’une page d’accueil par défaut donne aux visiteurs un point de départ logique lors de la visite de votre site.</span><span class="sxs-lookup"><span data-stu-id="d6214-284">Setting a default home page provides visitors a logical starting point when visiting your site.</span></span> <span data-ttu-id="d6214-285">Pour traiter une page par défaut sans que l’utilisateur ne qualifie complètement l’URI, appelez la <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> méthode à partir de `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="d6214-285">To serve a default page without the user fully qualifying the URI, call the <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> method from `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> <span data-ttu-id="d6214-286">`UseDefaultFiles` doit être appelé avant `UseStaticFiles` pour délivrer le fichier par défaut.</span><span class="sxs-lookup"><span data-stu-id="d6214-286">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="d6214-287">`UseDefaultFiles` est un module de réécriture d’URL qui ne délivre pas réellement le fichier.</span><span class="sxs-lookup"><span data-stu-id="d6214-287">`UseDefaultFiles` is a URL rewriter that doesn't actually serve the file.</span></span> <span data-ttu-id="d6214-288">Activez le middleware de fichiers statiques via `UseStaticFiles` pour délivrer le fichier.</span><span class="sxs-lookup"><span data-stu-id="d6214-288">Enable Static File Middleware via `UseStaticFiles` to serve the file.</span></span>

<span data-ttu-id="d6214-289">Avec `UseDefaultFiles`, les requêtes sur un dossier recherchent :</span><span class="sxs-lookup"><span data-stu-id="d6214-289">With `UseDefaultFiles`, requests to a folder search for:</span></span>

* <span data-ttu-id="d6214-290">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="d6214-290">*default.htm*</span></span>
* <span data-ttu-id="d6214-291">*default.html*</span><span class="sxs-lookup"><span data-stu-id="d6214-291">*default.html*</span></span>
* <span data-ttu-id="d6214-292">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="d6214-292">*index.htm*</span></span>
* <span data-ttu-id="d6214-293">*index.html*</span><span class="sxs-lookup"><span data-stu-id="d6214-293">*index.html*</span></span>

<span data-ttu-id="d6214-294">Le premier fichier trouvé dans la liste est délivré comme si la requête était l’URI qualifié complet.</span><span class="sxs-lookup"><span data-stu-id="d6214-294">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="d6214-295">L’URL du navigateur continue de refléter l’URI demandé.</span><span class="sxs-lookup"><span data-stu-id="d6214-295">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="d6214-296">Le code suivant change le nom de fichier par défaut en *mydefault.html* :</span><span class="sxs-lookup"><span data-stu-id="d6214-296">The following code changes the default file name to *mydefault.html* :</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a><span data-ttu-id="d6214-297">UseFileServer</span><span class="sxs-lookup"><span data-stu-id="d6214-297">UseFileServer</span></span>

<span data-ttu-id="d6214-298"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combine les fonctionnalités de `UseStaticFiles` , `UseDefaultFiles` et éventuellement `UseDirectoryBrowser` .</span><span class="sxs-lookup"><span data-stu-id="d6214-298"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="d6214-299">Le code suivant active la possibilité de délivrer des fichiers statiques et le fichier par défaut.</span><span class="sxs-lookup"><span data-stu-id="d6214-299">The following code enables the serving of static files and the default file.</span></span> <span data-ttu-id="d6214-300">L’exploration des répertoires n’est pas activée.</span><span class="sxs-lookup"><span data-stu-id="d6214-300">Directory browsing isn't enabled.</span></span>

```csharp
app.UseFileServer();
```

<span data-ttu-id="d6214-301">Le code suivant s’appuie sur la surcharge sans paramètre en activant l’exploration des répertoires :</span><span class="sxs-lookup"><span data-stu-id="d6214-301">The following code builds upon the parameterless overload by enabling directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="d6214-302">Considérez la hiérarchie de répertoires suivante :</span><span class="sxs-lookup"><span data-stu-id="d6214-302">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="d6214-303">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="d6214-303">**wwwroot**</span></span>
  * <span data-ttu-id="d6214-304">**format**</span><span class="sxs-lookup"><span data-stu-id="d6214-304">**css**</span></span>
  * <span data-ttu-id="d6214-305">**images**</span><span class="sxs-lookup"><span data-stu-id="d6214-305">**images**</span></span>
  * <span data-ttu-id="d6214-306">**js**</span><span class="sxs-lookup"><span data-stu-id="d6214-306">**js**</span></span>
* <span data-ttu-id="d6214-307">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="d6214-307">**MyStaticFiles**</span></span>
  * <span data-ttu-id="d6214-308">**images**</span><span class="sxs-lookup"><span data-stu-id="d6214-308">**images**</span></span>
    * <span data-ttu-id="d6214-309">*banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="d6214-309">*banner1.svg*</span></span>
  * <span data-ttu-id="d6214-310">*default.html*</span><span class="sxs-lookup"><span data-stu-id="d6214-310">*default.html*</span></span>

<span data-ttu-id="d6214-311">Le code suivant active les fichiers statiques, les fichiers par défaut et l’exploration des répertoires de `MyStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="d6214-311">The following code enables static files, default files, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

<span data-ttu-id="d6214-312">`AddDirectoryBrowser` doit être appelé quand la valeur de la propriété `EnableDirectoryBrowsing` est `true` :</span><span class="sxs-lookup"><span data-stu-id="d6214-312">`AddDirectoryBrowser` must be called when the `EnableDirectoryBrowsing` property value is `true`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

<span data-ttu-id="d6214-313">En utilisant la hiérarchie de fichiers et le code précédent, les URL sont résolues comme suit :</span><span class="sxs-lookup"><span data-stu-id="d6214-313">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="d6214-314">URI</span><span class="sxs-lookup"><span data-stu-id="d6214-314">URI</span></span>            |                             <span data-ttu-id="d6214-315">response</span><span class="sxs-lookup"><span data-stu-id="d6214-315">Response</span></span>  |
| ------- | ------|
| <span data-ttu-id="d6214-316">*http:// \<server_address> /StaticFiles/images/banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="d6214-316">*http://\<server_address>/StaticFiles/images/banner1.svg*</span></span>    |      <span data-ttu-id="d6214-317">MyStaticFiles/images/banner1.svg</span><span class="sxs-lookup"><span data-stu-id="d6214-317">MyStaticFiles/images/banner1.svg</span></span> |
| <span data-ttu-id="d6214-318">*http:// \<server_address> /StaticFiles*</span><span class="sxs-lookup"><span data-stu-id="d6214-318">*http://\<server_address>/StaticFiles*</span></span>             |     <span data-ttu-id="d6214-319">MyStaticFiles/default.html</span><span class="sxs-lookup"><span data-stu-id="d6214-319">MyStaticFiles/default.html</span></span> |

<span data-ttu-id="d6214-320">Si aucun fichier nommé par défaut n’existe dans le répertoire *MyStaticFiles* , *http:// \<server_address> /StaticFiles* retourne la liste des répertoires avec des liens interactives :</span><span class="sxs-lookup"><span data-stu-id="d6214-320">If no default-named file exists in the *MyStaticFiles* directory, *http://\<server_address>/StaticFiles* returns the directory listing with clickable links:</span></span>

![Liste des fichiers statiques](static-files/_static/db2.png)

> [!NOTE]
> <span data-ttu-id="d6214-322"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> et <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> effectuent une redirection côté client à partir de `http://{SERVER ADDRESS}/StaticFiles` (sans barre oblique) vers `http://{SERVER ADDRESS}/StaticFiles/` (avec barre oblique).</span><span class="sxs-lookup"><span data-stu-id="d6214-322"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from `http://{SERVER ADDRESS}/StaticFiles` (without a trailing slash) to `http://{SERVER ADDRESS}/StaticFiles/` (with a trailing slash).</span></span> <span data-ttu-id="d6214-323">Les URL relatives du répertoire *StaticFiles* ne sont pas valides sans barre oblique de fin.</span><span class="sxs-lookup"><span data-stu-id="d6214-323">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash.</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="d6214-324">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="d6214-324">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="d6214-325">La <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> classe contient une `Mappings` propriété servant de mappage d’extensions de fichier à des types de contenu MIME.</span><span class="sxs-lookup"><span data-stu-id="d6214-325">The <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> class contains a `Mappings` property serving as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="d6214-326">Dans l’exemple suivant, plusieurs extensions de fichiers sont inscrites avec des types MIME connus.</span><span class="sxs-lookup"><span data-stu-id="d6214-326">In the following sample, several file extensions are registered to known MIME types.</span></span> <span data-ttu-id="d6214-327">L’extension *.rtf* est remplacée et *.mp4* est supprimée.</span><span class="sxs-lookup"><span data-stu-id="d6214-327">The *.rtf* extension is replaced, and *.mp4* is removed.</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

<span data-ttu-id="d6214-328">Consultez [Types de contenu MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="d6214-328">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

<span data-ttu-id="d6214-329">Pour plus d’informations sur l’utilisation d’un personnalisé <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> ou pour configurer d’autres <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> :::no-loc(Blazor)::: applications serveur, consultez <xref:blazor/fundamentals/additional-scenarios#static-files> .</span><span class="sxs-lookup"><span data-stu-id="d6214-329">For information on using a custom <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> or to configure other <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> in :::no-loc(Blazor)::: Server apps, see <xref:blazor/fundamentals/additional-scenarios#static-files>.</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="d6214-330">Types de contenu non standard</span><span class="sxs-lookup"><span data-stu-id="d6214-330">Non-standard content types</span></span>

<span data-ttu-id="d6214-331">Static File Middleware comprend près de 400 types connus de contenu de fichier.</span><span class="sxs-lookup"><span data-stu-id="d6214-331">Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="d6214-332">Si l’utilisateur demande un fichier d’un type inconnu, Static File Middleware transmet la requête à l’intergiciel (middleware) suivant dans le pipeline.</span><span class="sxs-lookup"><span data-stu-id="d6214-332">If the user requests a file with an unknown file type, Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="d6214-333">Si aucun intergiciel ne gère la requête, une réponse *404 introuvable* est retournée.</span><span class="sxs-lookup"><span data-stu-id="d6214-333">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="d6214-334">Si l’exploration des répertoires est activée, un lien vers le fichier est affiché dans la liste de répertoires.</span><span class="sxs-lookup"><span data-stu-id="d6214-334">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="d6214-335">Le code suivant permet de délivrer des types inconnus et rend le fichier inconnu en tant qu’image :</span><span class="sxs-lookup"><span data-stu-id="d6214-335">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

<span data-ttu-id="d6214-336">Avec le code précédent, une requête pour un fichier avec un type de contenu inconnu est retournée en tant qu’image.</span><span class="sxs-lookup"><span data-stu-id="d6214-336">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="d6214-337">L’activation <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> de est un risque pour la sécurité.</span><span class="sxs-lookup"><span data-stu-id="d6214-337">Enabling <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> is a security risk.</span></span> <span data-ttu-id="d6214-338">Il est désactivé par défaut et son utilisation est déconseillée.</span><span class="sxs-lookup"><span data-stu-id="d6214-338">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="d6214-339">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) fournit une alternative plus sûre pour délivrer des fichiers avec des extensions non standard.</span><span class="sxs-lookup"><span data-stu-id="d6214-339">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="d6214-340">Servir des fichiers à partir de plusieurs emplacements</span><span class="sxs-lookup"><span data-stu-id="d6214-340">Serve files from multiple locations</span></span>

<span data-ttu-id="d6214-341">`UseStaticFiles``UseFileServer`la valeur par défaut est le fournisseur de fichiers qui pointe vers *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="d6214-341">`UseStaticFiles` and `UseFileServer` defaults to the file provider pointing at *wwwroot* .</span></span> <span data-ttu-id="d6214-342">Vous pouvez fournir des instances supplémentaires de `UseStaticFiles` et `UseFileServer` avec d’autres fournisseurs de fichiers pour servir des fichiers à partir d’autres emplacements.</span><span class="sxs-lookup"><span data-stu-id="d6214-342">You can provide additional instances of `UseStaticFiles` and `UseFileServer` with other file providers to serve files from other locations.</span></span> <span data-ttu-id="d6214-343">Pour plus d’informations, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span><span class="sxs-lookup"><span data-stu-id="d6214-343">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

### <a name="considerations"></a><span data-ttu-id="d6214-344">Considérations</span><span class="sxs-lookup"><span data-stu-id="d6214-344">Considerations</span></span>

> [!WARNING]
> <span data-ttu-id="d6214-345">`UseDirectoryBrowser` et `UseStaticFiles` peuvent entraîner une fuite de secrets.</span><span class="sxs-lookup"><span data-stu-id="d6214-345">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="d6214-346">La désactivation de l’exploration de répertoires est fortement recommandée en production.</span><span class="sxs-lookup"><span data-stu-id="d6214-346">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="d6214-347">Examinez attentivement les répertoires qui sont activés via `UseStaticFiles` ou `UseDirectoryBrowser`.</span><span class="sxs-lookup"><span data-stu-id="d6214-347">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="d6214-348">L’ensemble du répertoire et de ses sous-répertoires deviennent accessibles publiquement.</span><span class="sxs-lookup"><span data-stu-id="d6214-348">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="d6214-349">Stocker les fichiers pouvant être affectés au public dans un répertoire dédié, par exemple *\<content_root> /wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="d6214-349">Store files suitable for serving to the public in a dedicated directory, such as *\<content_root>/wwwroot* .</span></span> <span data-ttu-id="d6214-350">Séparez ces fichiers des vues MVC, des :::no-loc(Razor)::: pages (2. x uniquement), des fichiers de configuration, etc.</span><span class="sxs-lookup"><span data-stu-id="d6214-350">Separate these files from MVC views, :::no-loc(Razor)::: Pages (2.x only), configuration files, etc.</span></span>

* <span data-ttu-id="d6214-351">Les URL pour le contenu exposé avec `UseDirectoryBrowser` et `UseStaticFiles` sont soumises aux restrictions de respect de la casse et de caractères du système de fichiers sous-jacent.</span><span class="sxs-lookup"><span data-stu-id="d6214-351">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="d6214-352">Par exemple, Windows ne respecte pas la casse, mais macOS et Linux la respectent.</span><span class="sxs-lookup"><span data-stu-id="d6214-352">For example, Windows is case insensitive&mdash;macOS and Linux aren't.</span></span>

* <span data-ttu-id="d6214-353">Les applications ASP.NET Core hébergées dans IIS utilisent le [module ASP.NET Core](xref:host-and-deploy/aspnet-core-module) pour transférer toutes les requêtes à l’application, notamment les requêtes de fichiers statiques.</span><span class="sxs-lookup"><span data-stu-id="d6214-353">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="d6214-354">Le gestionnaire de fichiers statiques d’IIS n’est pas utilisé.</span><span class="sxs-lookup"><span data-stu-id="d6214-354">The IIS static file handler isn't used.</span></span> <span data-ttu-id="d6214-355">Il ne lui est pas permis de traiter les requêtes avant qu’elles soient gérées par le module.</span><span class="sxs-lookup"><span data-stu-id="d6214-355">It has no chance to handle requests before they're handled by the module.</span></span>

* <span data-ttu-id="d6214-356">Effectuez les étapes suivantes dans le Gestionnaire des services Internet (IIS) pour supprimer le gestionnaire de fichiers statiques d’IIS au niveau du serveur ou du site web :</span><span class="sxs-lookup"><span data-stu-id="d6214-356">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="d6214-357">Accédez à la fonctionnalité **Modules** .</span><span class="sxs-lookup"><span data-stu-id="d6214-357">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="d6214-358">Sélectionnez **StaticFileModule** dans la liste.</span><span class="sxs-lookup"><span data-stu-id="d6214-358">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="d6214-359">Cliquez sur **Supprimer** dans l’encadré **Actions** .</span><span class="sxs-lookup"><span data-stu-id="d6214-359">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="d6214-360">Si le gestionnaire de fichiers statiques d’IIS est activé **et** que le module ASP.NET Core est incorrectement configuré, les fichiers statiques peuvent être délivrés.</span><span class="sxs-lookup"><span data-stu-id="d6214-360">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="d6214-361">Cela se produit par exemple si le fichier *web.config* n’est pas déployé.</span><span class="sxs-lookup"><span data-stu-id="d6214-361">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="d6214-362">Placez les fichiers de code (y compris les fichiers *. cs* et *. cshtml* ) en dehors de la [racine Web](xref:fundamentals/index#web-root)du projet d’application.</span><span class="sxs-lookup"><span data-stu-id="d6214-362">Place code files (including *.cs* and *.cshtml* ) outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="d6214-363">Par conséquent, une séparation logique est créée entre le contenu côté client et le code basé sur le serveur de l’application.</span><span class="sxs-lookup"><span data-stu-id="d6214-363">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="d6214-364">Ceci empêche la fuite de code côté serveur.</span><span class="sxs-lookup"><span data-stu-id="d6214-364">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d6214-365">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="d6214-365">Additional resources</span></span>

* [<span data-ttu-id="d6214-366">Middleware</span><span class="sxs-lookup"><span data-stu-id="d6214-366">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="d6214-367">Introduction à ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d6214-367">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end
