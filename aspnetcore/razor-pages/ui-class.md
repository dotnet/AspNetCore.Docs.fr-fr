---
title: Interface utilisateur réutilisable Razor dans les bibliothèques de classes avec ASP.net Core
author: Rick-Anderson
description: Explique comment créer Razor une interface utilisateur réutilisable à l’aide de vues partielles dans une bibliothèque de classes dans ASP.net core.
ms.author: riande
ms.date: 01/19/2021
ms.custom: mvc, seodec18
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
uid: razor-pages/ui-class
ms.openlocfilehash: a878a3485ecee0782b21ac69c5ec6ff832b9f06c
ms.sourcegitcommit: cb984e0d7dc23a88c3a4121f23acfaea0acbfe1e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98571009"
---
# <a name="create-reusable-ui-using-the-no-locrazor-class-library-project-in-aspnet-core"></a><span data-ttu-id="b291f-103">Créer une interface utilisateur réutilisable à l’aide du Razor projet de bibliothèque de classes dans ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="b291f-103">Create reusable UI using the Razor class library project in ASP.NET Core</span></span>

<span data-ttu-id="b291f-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b291f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b291f-105">Razorles affichages, les pages, les contrôleurs, les modèles de page, les [ Razor composants](xref:blazor/components/class-libraries), les [composants de vue](xref:mvc/views/view-components)et les modèles de données peuvent être intégrés dans une Razor bibliothèque de classes (RCL).</span><span class="sxs-lookup"><span data-stu-id="b291f-105">Razor views, pages, controllers, page models, [Razor components](xref:blazor/components/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="b291f-106">La RCL peut être empaquetée et réutilisée.</span><span class="sxs-lookup"><span data-stu-id="b291f-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="b291f-107">Les applications peuvent inclure la RCL et remplacer les vues et les pages qu’elle contient.</span><span class="sxs-lookup"><span data-stu-id="b291f-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="b291f-108">Lorsqu’une vue, une vue partielle ou une Razor page se trouve à la fois dans l’application Web et le RCL, le Razor balisage (fichier *. cshtml* ) dans l’application Web est prioritaire.</span><span class="sxs-lookup"><span data-stu-id="b291f-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="b291f-109">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b291f-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-no-locrazor-ui"></a><span data-ttu-id="b291f-110">Créer une bibliothèque de classes contenant Razor l’interface utilisateur</span><span class="sxs-lookup"><span data-stu-id="b291f-110">Create a class library containing Razor UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b291f-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b291f-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b291f-112">Dans Visual Studio, sélectionnez **créer un nouveau projet**.</span><span class="sxs-lookup"><span data-stu-id="b291f-112">From Visual Studio select **Create new a new project**.</span></span>
* <span data-ttu-id="b291f-113">Sélectionnez **Razor bibliothèque de classes** > **suivant**.</span><span class="sxs-lookup"><span data-stu-id="b291f-113">Select **Razor Class Library** > **Next**.</span></span>
* <span data-ttu-id="b291f-114">Nommez la bibliothèque (par exemple, « Razor ClassLib »), > **créer**.</span><span class="sxs-lookup"><span data-stu-id="b291f-114">Name the library (for example, "RazorClassLib"), > **Create**.</span></span> <span data-ttu-id="b291f-115">Pour éviter une collision de nom de fichier avec la bibliothèque de vues générée, vérifiez que le nom de la bibliothèque ne se termine pas par `.Views`.</span><span class="sxs-lookup"><span data-stu-id="b291f-115">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="b291f-116">Sélectionnez **les pages et les vues de support** si vous avez besoin de prendre en charge les affichages.</span><span class="sxs-lookup"><span data-stu-id="b291f-116">Select **Support pages and views** if you need to support views.</span></span> <span data-ttu-id="b291f-117">Par défaut, seules Razor les pages sont prises en charge.</span><span class="sxs-lookup"><span data-stu-id="b291f-117">By default, only Razor Pages are supported.</span></span> <span data-ttu-id="b291f-118">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="b291f-118">Select **Create**.</span></span>

<span data-ttu-id="b291f-119">Par défaut, le Razor modèle de bibliothèque de classes (RCL) est Razor développement de composant par défaut.</span><span class="sxs-lookup"><span data-stu-id="b291f-119">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="b291f-120">L’option de **prise en charge des** pages et des affichages prend en charge les pages et les vues.</span><span class="sxs-lookup"><span data-stu-id="b291f-120">The **Support pages and views** option supports pages and views.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b291f-121">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="b291f-121">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="b291f-122">À partir de la ligne de commande, exécutez `dotnet new razorclasslib`.</span><span class="sxs-lookup"><span data-stu-id="b291f-122">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="b291f-123">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="b291f-123">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="b291f-124">Par défaut, le Razor modèle de bibliothèque de classes (RCL) est Razor développement de composant par défaut.</span><span class="sxs-lookup"><span data-stu-id="b291f-124">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="b291f-125">Transmettez l' `--support-pages-and-views` option ( `dotnet new razorclasslib --support-pages-and-views` ) pour assurer la prise en charge des pages et des vues.</span><span class="sxs-lookup"><span data-stu-id="b291f-125">Pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`) to provide support for pages and views.</span></span>

<span data-ttu-id="b291f-126">Pour plus d’informations, consultez [dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="b291f-126">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="b291f-127">Pour éviter une collision de nom de fichier avec la bibliothèque de vues générée, vérifiez que le nom de la bibliothèque ne se termine pas par `.Views`.</span><span class="sxs-lookup"><span data-stu-id="b291f-127">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="b291f-128">Ajoutez Razor des fichiers à RCL.</span><span class="sxs-lookup"><span data-stu-id="b291f-128">Add Razor files to the RCL.</span></span>

<span data-ttu-id="b291f-129">Les modèles ASP.NET Core supposent que le contenu RCL se trouve dans le dossier *zones* .</span><span class="sxs-lookup"><span data-stu-id="b291f-129">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="b291f-130">Consultez [RCL pages layout](#rcl-pages-layout) pour créer un RCL qui expose du contenu dans `~/Pages` plutôt que `~/Areas/Pages` .</span><span class="sxs-lookup"><span data-stu-id="b291f-130">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="b291f-131">Référencer le contenu RCL</span><span class="sxs-lookup"><span data-stu-id="b291f-131">Reference RCL content</span></span>

<span data-ttu-id="b291f-132">La RCL peut être référencée par :</span><span class="sxs-lookup"><span data-stu-id="b291f-132">The RCL can be referenced by:</span></span>

* <span data-ttu-id="b291f-133">Un package NuGet.</span><span class="sxs-lookup"><span data-stu-id="b291f-133">NuGet package.</span></span> <span data-ttu-id="b291f-134">Consultez [Création de packages NuGet](/nuget/create-packages/creating-a-package), [dotnet add package](/dotnet/core/tools/dotnet-add-package) et [Créer et publier un package NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="b291f-134">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="b291f-135">Un fichier *{NomProjet}.csproj*.</span><span class="sxs-lookup"><span data-stu-id="b291f-135">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="b291f-136">Consultez [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="b291f-136">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="b291f-137">Substituer des vues, des vues partielles et des pages</span><span class="sxs-lookup"><span data-stu-id="b291f-137">Override views, partial views, and pages</span></span>

<span data-ttu-id="b291f-138">Lorsqu’une vue, une vue partielle ou une Razor page se trouve à la fois dans l’application Web et le RCL, le Razor balisage (fichier *. cshtml* ) dans l’application Web est prioritaire.</span><span class="sxs-lookup"><span data-stu-id="b291f-138">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="b291f-139">Par exemple, ajoutez *application Web 1/Areas/MyFeature/pages/Page1. cshtml* à application Web 1, et Page1 dans le application Web 1 aura priorité sur Page1 dans le RCL.</span><span class="sxs-lookup"><span data-stu-id="b291f-139">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="b291f-140">Dans l’exemple proposé sous forme de téléchargement, renommez *WebApp1/Areas/MyFeature2* en *WebApp1/Areas/MyFeature* pour tester la priorité.</span><span class="sxs-lookup"><span data-stu-id="b291f-140">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="b291f-141">Copiez la vue partielle *Razor UIClassLib/zones/MyFeature/pages/Shared/_Message. cshtml* sur *application Web 1/Areas/MyFeature/pages/Shared/_Message. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="b291f-141">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="b291f-142">Mettez à jour le balisage pour indiquer le nouvel emplacement.</span><span class="sxs-lookup"><span data-stu-id="b291f-142">Update the markup to indicate the new location.</span></span> <span data-ttu-id="b291f-143">Générez et exécutez l’application pour vérifier que la version de la vue partielle de l’application est utilisée.</span><span class="sxs-lookup"><span data-stu-id="b291f-143">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="b291f-144">Mise en page des pages RCL</span><span class="sxs-lookup"><span data-stu-id="b291f-144">RCL Pages layout</span></span>

<span data-ttu-id="b291f-145">Pour faire référence au contenu RCL comme s’il fait partie du dossier *pages* de l’application Web, créez le projet RCL avec la structure de fichiers suivante :</span><span class="sxs-lookup"><span data-stu-id="b291f-145">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="b291f-146">*RazorUIClassLib/pages*</span><span class="sxs-lookup"><span data-stu-id="b291f-146">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="b291f-147">*RazorUIClassLib/pages/partagé*</span><span class="sxs-lookup"><span data-stu-id="b291f-147">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="b291f-148">Supposons que *Razor UIClassLib/pages/Shared* contient deux fichiers partiels : *_Header. cshtml* et *_Footer. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="b291f-148">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="b291f-149">Les `<partial>` balises peuvent être ajoutées au fichier *_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="b291f-149">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

<span data-ttu-id="b291f-150">Ajoutez le fichier *_ViewStart. cshtml* au dossier *pages* du projet RCL pour utiliser le fichier *_Layout. cshtml* à partir de l’application Web hôte :</span><span class="sxs-lookup"><span data-stu-id="b291f-150">Add the *_ViewStart.cshtml* file to the RCL project's *Pages* folder to use the *_Layout.cshtml* file from the host web app:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

## <a name="create-an-rcl-with-static-assets"></a><span data-ttu-id="b291f-151">Créer un RCL avec des ressources statiques</span><span class="sxs-lookup"><span data-stu-id="b291f-151">Create an RCL with static assets</span></span>

<span data-ttu-id="b291f-152">Un RCL peut nécessiter des ressources statiques auxiliaires qui peuvent être référencées par RCL ou l’application consommateur du RCL.</span><span class="sxs-lookup"><span data-stu-id="b291f-152">An RCL may require companion static assets that can be referenced by either the RCL or the consuming app of the RCL.</span></span> <span data-ttu-id="b291f-153">ASP.NET Core permet de créer des RCLs qui incluent des ressources statiques disponibles pour une application consommatrice.</span><span class="sxs-lookup"><span data-stu-id="b291f-153">ASP.NET Core allows creating RCLs that include static assets that are available to a consuming app.</span></span>

<span data-ttu-id="b291f-154">Pour inclure des ressources d’accompagnement dans le cadre d’un RCL, créez un dossier *wwwroot* dans la bibliothèque de classes et incluez tous les fichiers nécessaires dans ce dossier.</span><span class="sxs-lookup"><span data-stu-id="b291f-154">To include companion assets as part of an RCL, create a *wwwroot* folder in the class library and include any required files in that folder.</span></span>

<span data-ttu-id="b291f-155">Lors de la compression d’un RCL, toutes les ressources associées dans le dossier *wwwroot* sont automatiquement incluses dans le package.</span><span class="sxs-lookup"><span data-stu-id="b291f-155">When packing an RCL, all companion assets in the *wwwroot* folder are automatically included in the package.</span></span>

<span data-ttu-id="b291f-156">Utilisez la `dotnet pack` commande plutôt que la version NuGet.exe `nuget pack` .</span><span class="sxs-lookup"><span data-stu-id="b291f-156">Use the `dotnet pack` command rather than the NuGet.exe version `nuget pack`.</span></span>

### <a name="exclude-static-assets"></a><span data-ttu-id="b291f-157">Exclure des ressources statiques</span><span class="sxs-lookup"><span data-stu-id="b291f-157">Exclude static assets</span></span>

<span data-ttu-id="b291f-158">Pour exclure des ressources statiques, ajoutez le chemin d’exclusion souhaité au `$(DefaultItemExcludes)` groupe de propriétés dans le fichier projet.</span><span class="sxs-lookup"><span data-stu-id="b291f-158">To exclude static assets, add the desired exclusion path to the `$(DefaultItemExcludes)` property group in the project file.</span></span> <span data-ttu-id="b291f-159">Séparez les entrées par un point-virgule ( `;` ).</span><span class="sxs-lookup"><span data-stu-id="b291f-159">Separate entries with a semicolon (`;`).</span></span>

<span data-ttu-id="b291f-160">Dans l’exemple suivant, la feuille de style *lib. CSS* du dossier *wwwroot* n’est pas considérée comme une ressource statique et n’est pas incluse dans le RCL publié :</span><span class="sxs-lookup"><span data-stu-id="b291f-160">In the following example, the *lib.css* stylesheet in the *wwwroot* folder isn't considered a static asset and isn't included in the published RCL:</span></span>

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a><span data-ttu-id="b291f-161">Intégration de la machine à écrire</span><span class="sxs-lookup"><span data-stu-id="b291f-161">Typescript integration</span></span>

<span data-ttu-id="b291f-162">Pour inclure des fichiers de machine à écrire dans un RCL :</span><span class="sxs-lookup"><span data-stu-id="b291f-162">To include TypeScript files in an RCL:</span></span>

1. <span data-ttu-id="b291f-163">Placez les fichiers de machine à écrire (*. TS*) en dehors du dossier *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="b291f-163">Place the TypeScript files (*.ts*) outside of the *wwwroot* folder.</span></span> <span data-ttu-id="b291f-164">Par exemple, placez les fichiers dans un dossier *client* .</span><span class="sxs-lookup"><span data-stu-id="b291f-164">For example, place the files in a *Client* folder.</span></span>

1. <span data-ttu-id="b291f-165">Configurez la sortie de génération de machine à écrire pour le dossier *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="b291f-165">Configure the TypeScript build output for the *wwwroot* folder.</span></span> <span data-ttu-id="b291f-166">Définissez la `TypescriptOutDir` propriété à l’intérieur d’un `PropertyGroup` dans le fichier projet :</span><span class="sxs-lookup"><span data-stu-id="b291f-166">Set the `TypescriptOutDir` property inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. <span data-ttu-id="b291f-167">Incluez la cible de la machine à écrire comme dépendance de la `ResolveCurrentProjectStaticWebAssets` cible en ajoutant la cible suivante à l’intérieur d’un `PropertyGroup` dans le fichier projet :</span><span class="sxs-lookup"><span data-stu-id="b291f-167">Include the TypeScript target as a dependency of the `ResolveCurrentProjectStaticWebAssets` target by adding the following target inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a><span data-ttu-id="b291f-168">Consommer du contenu à partir d’un RCL référencé</span><span class="sxs-lookup"><span data-stu-id="b291f-168">Consume content from a referenced RCL</span></span>

<span data-ttu-id="b291f-169">Les fichiers inclus dans le dossier *wwwroot* du RCL sont exposés à RCL ou à l’application consommatrice sous le préfixe `_content/{LIBRARY NAME}/` .</span><span class="sxs-lookup"><span data-stu-id="b291f-169">The files included in the *wwwroot* folder of the RCL are exposed to either the RCL or the consuming app under the prefix `_content/{LIBRARY NAME}/`.</span></span> <span data-ttu-id="b291f-170">Par exemple, une bibliothèque nommée *Razor . Class. lib* génère un chemin d’accès au contenu statique à l’emplacement `_content/Razor.Class.Lib/` .</span><span class="sxs-lookup"><span data-stu-id="b291f-170">For example, a library named *Razor.Class.Lib* results in a path to static content at `_content/Razor.Class.Lib/`.</span></span> <span data-ttu-id="b291f-171">Lorsque vous générez un package NuGet et que le nom de l’assembly n’est pas le même que l’ID du package, utilisez l’ID de package pour `{LIBRARY NAME}` .</span><span class="sxs-lookup"><span data-stu-id="b291f-171">When producing a NuGet package and the assembly name isn't the same as the package ID, use the package ID for `{LIBRARY NAME}`.</span></span>

<span data-ttu-id="b291f-172">L’application consommatrice référence les ressources statiques fournies par la bibliothèque avec `<script>` ,, `<style>` et d' `<img>` autres balises html.</span><span class="sxs-lookup"><span data-stu-id="b291f-172">The consuming app references static assets provided by the library with `<script>`, `<style>`, `<img>`, and other HTML tags.</span></span> <span data-ttu-id="b291f-173">L’application consommatrice doit avoir la [prise en charge des fichiers statiques](xref:fundamentals/static-files) activée dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="b291f-173">The consuming app must have [static file support](xref:fundamentals/static-files) enabled in `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

<span data-ttu-id="b291f-174">Lors de l’exécution de l’application consommatrice à partir de la sortie de génération ( `dotnet run` ), les ressources Web statiques sont activées par défaut dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="b291f-174">When running the consuming app from build output (`dotnet run`), static web assets are enabled by default in the Development environment.</span></span> <span data-ttu-id="b291f-175">Pour prendre en charge les ressources dans d’autres environnements lors de l’exécution à partir de la sortie de génération, appelez `UseStaticWebAssets` sur le générateur d’hôte dans *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="b291f-175">To support assets in other environments when running from build output, call `UseStaticWebAssets` on the host builder in *Program.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStaticWebAssets();
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="b291f-176">`UseStaticWebAssets`L’appel de n’est pas requis lors de l’exécution d’une application à partir de la sortie publiée ( `dotnet publish` ).</span><span class="sxs-lookup"><span data-stu-id="b291f-176">Calling `UseStaticWebAssets` isn't required when running an app from published output (`dotnet publish`).</span></span>

### <a name="multi-project-development-flow"></a><span data-ttu-id="b291f-177">Déroulement du développement de projets multiples</span><span class="sxs-lookup"><span data-stu-id="b291f-177">Multi-project development flow</span></span>

<span data-ttu-id="b291f-178">Lorsque l’application consommatrice s’exécute :</span><span class="sxs-lookup"><span data-stu-id="b291f-178">When the consuming app runs:</span></span>

* <span data-ttu-id="b291f-179">Les ressources du RCL restent dans leurs dossiers d’origine.</span><span class="sxs-lookup"><span data-stu-id="b291f-179">The assets in the RCL stay in their original folders.</span></span> <span data-ttu-id="b291f-180">Les ressources ne sont pas déplacées vers l’application consommatrice.</span><span class="sxs-lookup"><span data-stu-id="b291f-180">The assets aren't moved to the consuming app.</span></span>
* <span data-ttu-id="b291f-181">Toute modification dans le dossier *wwwroot* de RCL est reflétée dans l’application consommatrice une fois que le RCL est reconstruit et sans régénérer l’application consommateur.</span><span class="sxs-lookup"><span data-stu-id="b291f-181">Any change within the RCL's *wwwroot* folder is reflected in the consuming app after the RCL is rebuilt and without rebuilding the consuming app.</span></span>

<span data-ttu-id="b291f-182">Lorsque le RCL est généré, un manifeste qui décrit les emplacements des ressources Web statiques est généré.</span><span class="sxs-lookup"><span data-stu-id="b291f-182">When the RCL is built, a manifest is produced that describes the static web asset locations.</span></span> <span data-ttu-id="b291f-183">L’application consommatrice lit le manifeste au moment de l’exécution pour consommer les ressources des packages et des projets référencés.</span><span class="sxs-lookup"><span data-stu-id="b291f-183">The consuming app reads the manifest at runtime to consume the assets from referenced projects and packages.</span></span> <span data-ttu-id="b291f-184">Lorsqu’un nouvel élément multimédia est ajouté à un RCL, le RCL doit être régénéré pour mettre à jour son manifeste avant qu’une application consommatrice puisse accéder au nouvel élément multimédia.</span><span class="sxs-lookup"><span data-stu-id="b291f-184">When a new asset is added to an RCL, the RCL must be rebuilt to update its manifest before a consuming app can access the new asset.</span></span>

### <a name="publish"></a><span data-ttu-id="b291f-185">Publier</span><span class="sxs-lookup"><span data-stu-id="b291f-185">Publish</span></span>

<span data-ttu-id="b291f-186">Lorsque l’application est publiée, les ressources complémentaires de tous les packages et projets référencés sont copiées dans le dossier *wwwroot* de l’application publiée sous `_content/{LIBRARY NAME}/` .</span><span class="sxs-lookup"><span data-stu-id="b291f-186">When the app is published, the companion assets from all referenced projects and packages are copied into the *wwwroot* folder of the published app under `_content/{LIBRARY NAME}/`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b291f-187">Razorles affichages, les pages, les contrôleurs, les modèles de page, les [ Razor composants](xref:blazor/components/class-libraries), les [composants de vue](xref:mvc/views/view-components)et les modèles de données peuvent être intégrés dans une Razor bibliothèque de classes (RCL).</span><span class="sxs-lookup"><span data-stu-id="b291f-187">Razor views, pages, controllers, page models, [Razor components](xref:blazor/components/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="b291f-188">La RCL peut être empaquetée et réutilisée.</span><span class="sxs-lookup"><span data-stu-id="b291f-188">The RCL can be packaged and reused.</span></span> <span data-ttu-id="b291f-189">Les applications peuvent inclure la RCL et remplacer les vues et les pages qu’elle contient.</span><span class="sxs-lookup"><span data-stu-id="b291f-189">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="b291f-190">Lorsqu’une vue, une vue partielle ou une Razor page se trouve à la fois dans l’application Web et le RCL, le Razor balisage (fichier *. cshtml* ) dans l’application Web est prioritaire.</span><span class="sxs-lookup"><span data-stu-id="b291f-190">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="b291f-191">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b291f-191">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-no-locrazor-ui"></a><span data-ttu-id="b291f-192">Créer une bibliothèque de classes contenant Razor l’interface utilisateur</span><span class="sxs-lookup"><span data-stu-id="b291f-192">Create a class library containing Razor UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b291f-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b291f-193">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b291f-194">Dans Visual Studio, dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.</span><span class="sxs-lookup"><span data-stu-id="b291f-194">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="b291f-195">Sélectionnez **Application web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="b291f-195">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="b291f-196">Nommez la bibliothèque (par exemple, « Razor ClassLib ») > **OK**.</span><span class="sxs-lookup"><span data-stu-id="b291f-196">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="b291f-197">Pour éviter une collision de nom de fichier avec la bibliothèque de vues générée, vérifiez que le nom de la bibliothèque ne se termine pas par `.Views`.</span><span class="sxs-lookup"><span data-stu-id="b291f-197">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="b291f-198">Vérifiez que **ASP.NET Core 2.1** ou ultérieur est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="b291f-198">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="b291f-199">Sélectionnez **Razor bibliothèque de classes** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="b291f-199">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="b291f-200">Un RCL a le fichier projet suivant :</span><span class="sxs-lookup"><span data-stu-id="b291f-200">An RCL has the following project file:</span></span>

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-cli"></a>[<span data-ttu-id="b291f-201">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="b291f-201">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="b291f-202">À partir de la ligne de commande, exécutez `dotnet new razorclasslib`.</span><span class="sxs-lookup"><span data-stu-id="b291f-202">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="b291f-203">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="b291f-203">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="b291f-204">Pour plus d’informations, consultez [dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="b291f-204">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="b291f-205">Pour éviter une collision de nom de fichier avec la bibliothèque de vues générée, vérifiez que le nom de la bibliothèque ne se termine pas par `.Views`.</span><span class="sxs-lookup"><span data-stu-id="b291f-205">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="b291f-206">Ajoutez Razor des fichiers à RCL.</span><span class="sxs-lookup"><span data-stu-id="b291f-206">Add Razor files to the RCL.</span></span>

<span data-ttu-id="b291f-207">Les modèles ASP.NET Core supposent que le contenu RCL se trouve dans le dossier *zones* .</span><span class="sxs-lookup"><span data-stu-id="b291f-207">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="b291f-208">Consultez [RCL pages layout](#rcl-pages-layout) pour créer un RCL qui expose du contenu dans `~/Pages` plutôt que `~/Areas/Pages` .</span><span class="sxs-lookup"><span data-stu-id="b291f-208">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="b291f-209">Référencer le contenu RCL</span><span class="sxs-lookup"><span data-stu-id="b291f-209">Reference RCL content</span></span>

<span data-ttu-id="b291f-210">La RCL peut être référencée par :</span><span class="sxs-lookup"><span data-stu-id="b291f-210">The RCL can be referenced by:</span></span>

* <span data-ttu-id="b291f-211">Un package NuGet.</span><span class="sxs-lookup"><span data-stu-id="b291f-211">NuGet package.</span></span> <span data-ttu-id="b291f-212">Consultez [Création de packages NuGet](/nuget/create-packages/creating-a-package), [dotnet add package](/dotnet/core/tools/dotnet-add-package) et [Créer et publier un package NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="b291f-212">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="b291f-213">Un fichier *{NomProjet}.csproj*.</span><span class="sxs-lookup"><span data-stu-id="b291f-213">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="b291f-214">Consultez [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="b291f-214">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-no-locrazor-pages-project"></a><span data-ttu-id="b291f-215">Procédure pas à pas : création d’un projet RCL et utilisation à partir d’un Razor projet pages</span><span class="sxs-lookup"><span data-stu-id="b291f-215">Walkthrough: Create an RCL project and use from a Razor Pages project</span></span>

<span data-ttu-id="b291f-216">Vous pouvez télécharger et tester le [projet complet](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) au lieu de le créer de toutes pièces.</span><span class="sxs-lookup"><span data-stu-id="b291f-216">You can download the [complete project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="b291f-217">L’exemple proposé sous forme de téléchargement contient du code et des liens supplémentaires qui facilitent le test du projet.</span><span class="sxs-lookup"><span data-stu-id="b291f-217">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="b291f-218">Si vous souhaitez commenter le mode d’obtention des exemples (téléchargement ou création au moyen d’instructions détaillées), entrez vos commentaires dans [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/6098).</span><span class="sxs-lookup"><span data-stu-id="b291f-218">You can leave feedback in [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="b291f-219">Tester l’application téléchargée</span><span class="sxs-lookup"><span data-stu-id="b291f-219">Test the download app</span></span>

<span data-ttu-id="b291f-220">Si vous n’avez pas téléchargé l’application complète et que vous souhaitez créer le projet pas à pas, passez à la [section suivante](#create-an-rcl).</span><span class="sxs-lookup"><span data-stu-id="b291f-220">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-an-rcl).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b291f-221">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b291f-221">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b291f-222">Ouvrez le fichier *.sln* dans Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b291f-222">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="b291f-223">Exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="b291f-223">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b291f-224">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="b291f-224">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="b291f-225">À partir d’une invite de commandes dans le répertoire *cli*, générez la RCL et l’application web.</span><span class="sxs-lookup"><span data-stu-id="b291f-225">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="b291f-226">Passez au répertoire *WebApp1* et exécutez l’application :</span><span class="sxs-lookup"><span data-stu-id="b291f-226">Move to the *WebApp1* directory and run the app:</span></span>

```dotnetcli
dotnet run
```

---

<span data-ttu-id="b291f-227">Suivez les instructions contenues dans [Test WebApp1](#test-webapp1)</span><span class="sxs-lookup"><span data-stu-id="b291f-227">Follow the instructions in [Test WebApp1](#test-webapp1)</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="b291f-228">Créer un RCL</span><span class="sxs-lookup"><span data-stu-id="b291f-228">Create an RCL</span></span>

<span data-ttu-id="b291f-229">Dans cette section, un RCL est créé.</span><span class="sxs-lookup"><span data-stu-id="b291f-229">In this section, an RCL is created.</span></span> <span data-ttu-id="b291f-230">Razor des fichiers sont ajoutés à RCL.</span><span class="sxs-lookup"><span data-stu-id="b291f-230">Razor files are added to the RCL.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b291f-231">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b291f-231">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b291f-232">Créez le projet RCL :</span><span class="sxs-lookup"><span data-stu-id="b291f-232">Create the RCL project:</span></span>

* <span data-ttu-id="b291f-233">Dans Visual Studio, dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.</span><span class="sxs-lookup"><span data-stu-id="b291f-233">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="b291f-234">Sélectionnez **Application web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="b291f-234">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="b291f-235">Nommez l’application **Razor UIClassLib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="b291f-235">Name the app **RazorUIClassLib** > **OK**.</span></span>
* <span data-ttu-id="b291f-236">Vérifiez que **ASP.NET Core 2.1** ou ultérieur est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="b291f-236">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="b291f-237">Sélectionnez **Razor bibliothèque de classes** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="b291f-237">Select **Razor Class Library** > **OK**.</span></span>
* <span data-ttu-id="b291f-238">Ajoutez un Razor fichier de vue partielle nommé *Razor UIClassLib/Areas/MyFeature/pages/shared/_Message. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="b291f-238">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b291f-239">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="b291f-239">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="b291f-240">À partir de la ligne de commande, exécutez ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="b291f-240">From the command line, run the following:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="b291f-241">Les commandes précédentes :</span><span class="sxs-lookup"><span data-stu-id="b291f-241">The preceding commands:</span></span>

* <span data-ttu-id="b291f-242">Crée le `RazorUIClassLib` RCL.</span><span class="sxs-lookup"><span data-stu-id="b291f-242">Creates the `RazorUIClassLib` RCL.</span></span>
* <span data-ttu-id="b291f-243">Crée une Razor _Message page et l’ajoute à RCL.</span><span class="sxs-lookup"><span data-stu-id="b291f-243">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="b291f-244">Le paramètre `-np` crée la page sans `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="b291f-244">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="b291f-245">Crée un fichier [_ViewStart. cshtml](xref:mvc/views/layout#running-code-before-each-view) et l’ajoute à RCL.</span><span class="sxs-lookup"><span data-stu-id="b291f-245">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="b291f-246">Le fichier *_ViewStart. cshtml* est requis pour utiliser la disposition du Razor projet pages (qui est ajouté dans la section suivante).</span><span class="sxs-lookup"><span data-stu-id="b291f-246">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

---

### <a name="add-no-locrazor-files-and-folders-to-the-project"></a><span data-ttu-id="b291f-247">Ajouter Razor des fichiers et des dossiers au projet</span><span class="sxs-lookup"><span data-stu-id="b291f-247">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="b291f-248">Remplacez le balisage dans *Razor UIClassLib/Areas/MyFeature/pages/Shared/_Message. cshtml* par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="b291f-248">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="b291f-249">Remplacez le balisage dans *Razor UIClassLib/Areas/MyFeature/pages/Page1. cshtml* par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="b291f-249">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  <span data-ttu-id="b291f-250">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` est nécessaire pour utiliser la vue partielle (`<partial name="_Message" />`).</span><span class="sxs-lookup"><span data-stu-id="b291f-250">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="b291f-251">Au lieu d’inclure la directive `@addTagHelper`, vous pouvez ajouter un fichier *_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="b291f-251">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="b291f-252">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="b291f-252">For example:</span></span>

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  <span data-ttu-id="b291f-253">Pour plus d’informations sur *_ViewImports. cshtml*, consultez [importation de directives partagées](xref:mvc/views/layout#importing-shared-directives)</span><span class="sxs-lookup"><span data-stu-id="b291f-253">For more information on *_ViewImports.cshtml*, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="b291f-254">Générez la bibliothèque de classes pour vérifier l’absence d’erreurs de compilateur :</span><span class="sxs-lookup"><span data-stu-id="b291f-254">Build the class library to verify there are no compiler errors:</span></span>

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

<span data-ttu-id="b291f-255">La sortie de la génération contient *RazorUIClassLib.dll* et *RazorUIClassLib.Views.dll*.</span><span class="sxs-lookup"><span data-stu-id="b291f-255">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="b291f-256">*RazorUIClassLib.Views.dll* contient le Razor contenu compilé.</span><span class="sxs-lookup"><span data-stu-id="b291f-256">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-no-locrazor-ui-library-from-a-no-locrazor-pages-project"></a><span data-ttu-id="b291f-257">Utiliser la Razor bibliothèque d’interface utilisateur à partir d’un Razor projet pages</span><span class="sxs-lookup"><span data-stu-id="b291f-257">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b291f-258">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b291f-258">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b291f-259">Créez l' Razor application Web pages :</span><span class="sxs-lookup"><span data-stu-id="b291f-259">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="b291f-260">Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur la solution > **Ajouter** >  **Nouveau projet**.</span><span class="sxs-lookup"><span data-stu-id="b291f-260">From **Solution Explorer**, right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="b291f-261">Sélectionnez **Application web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="b291f-261">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="b291f-262">Nommez l’application **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="b291f-262">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="b291f-263">Vérifiez que **ASP.NET Core 2.1** ou ultérieur est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="b291f-263">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="b291f-264">Sélectionnez **Application web** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="b291f-264">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="b291f-265">Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur **WebApp1**, puis sélectionnez **Définir comme projet de démarrage**.</span><span class="sxs-lookup"><span data-stu-id="b291f-265">From **Solution Explorer**, right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="b291f-266">Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur **WebApp1**, puis sélectionnez **Dépendances de build** > **Dépendances du projet**.</span><span class="sxs-lookup"><span data-stu-id="b291f-266">From **Solution Explorer**, right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="b291f-267">Vérifiez **Razor UIClassLib** comme dépendance de **application Web 1**.</span><span class="sxs-lookup"><span data-stu-id="b291f-267">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="b291f-268">Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur **WebApp1**, puis sélectionnez **Ajouter** > **Référence**.</span><span class="sxs-lookup"><span data-stu-id="b291f-268">From **Solution Explorer**, right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="b291f-269">Dans la boîte de dialogue **Gestionnaire de références** , activez la case à cocher **Razor UIClassLib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="b291f-269">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="b291f-270">Exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="b291f-270">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b291f-271">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="b291f-271">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="b291f-272">Créez une Razor application Web pages et un fichier solution contenant l' Razor application pages et le RCL :</span><span class="sxs-lookup"><span data-stu-id="b291f-272">Create a Razor Pages web app and a solution file containing the Razor Pages app and the RCL:</span></span>

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="b291f-273">Générez et exécutez l’application web :</span><span class="sxs-lookup"><span data-stu-id="b291f-273">Build and run the web app:</span></span>

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a><span data-ttu-id="b291f-274">Tester WebApp1</span><span class="sxs-lookup"><span data-stu-id="b291f-274">Test WebApp1</span></span>

<span data-ttu-id="b291f-275">Accédez à `/MyFeature/Page1` pour vérifier que la Razor bibliothèque de classes d’interface utilisateur est en cours d’utilisation.</span><span class="sxs-lookup"><span data-stu-id="b291f-275">Browse to `/MyFeature/Page1` to verify that the Razor UI class library is in use.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="b291f-276">Substituer des vues, des vues partielles et des pages</span><span class="sxs-lookup"><span data-stu-id="b291f-276">Override views, partial views, and pages</span></span>

<span data-ttu-id="b291f-277">Lorsqu’une vue, une vue partielle ou une Razor page se trouve à la fois dans l’application Web et le RCL, le Razor balisage (fichier *. cshtml* ) dans l’application Web est prioritaire.</span><span class="sxs-lookup"><span data-stu-id="b291f-277">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="b291f-278">Par exemple, ajoutez *application Web 1/Areas/MyFeature/pages/Page1. cshtml* à application Web 1, et Page1 dans le application Web 1 aura priorité sur Page1 dans le RCL.</span><span class="sxs-lookup"><span data-stu-id="b291f-278">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="b291f-279">Dans l’exemple proposé sous forme de téléchargement, renommez *WebApp1/Areas/MyFeature2* en *WebApp1/Areas/MyFeature* pour tester la priorité.</span><span class="sxs-lookup"><span data-stu-id="b291f-279">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="b291f-280">Copiez la vue partielle *Razor UIClassLib/zones/MyFeature/pages/Shared/_Message. cshtml* sur *application Web 1/Areas/MyFeature/pages/Shared/_Message. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="b291f-280">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="b291f-281">Mettez à jour le balisage pour indiquer le nouvel emplacement.</span><span class="sxs-lookup"><span data-stu-id="b291f-281">Update the markup to indicate the new location.</span></span> <span data-ttu-id="b291f-282">Générez et exécutez l’application pour vérifier que la version de la vue partielle de l’application est utilisée.</span><span class="sxs-lookup"><span data-stu-id="b291f-282">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="b291f-283">Mise en page des pages RCL</span><span class="sxs-lookup"><span data-stu-id="b291f-283">RCL Pages layout</span></span>

<span data-ttu-id="b291f-284">Pour faire référence au contenu RCL comme s’il fait partie du dossier *pages* de l’application Web, créez le projet RCL avec la structure de fichiers suivante :</span><span class="sxs-lookup"><span data-stu-id="b291f-284">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="b291f-285">*RazorUIClassLib/pages*</span><span class="sxs-lookup"><span data-stu-id="b291f-285">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="b291f-286">*RazorUIClassLib/pages/partagé*</span><span class="sxs-lookup"><span data-stu-id="b291f-286">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="b291f-287">Supposons que *Razor UIClassLib/pages/Shared* contient deux fichiers partiels : *_Header. cshtml* et *_Footer. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="b291f-287">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="b291f-288">Les `<partial>` balises peuvent être ajoutées au fichier *_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="b291f-288">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="b291f-289">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="b291f-289">Additional resources</span></span>

::: moniker range=">= aspnetcore-5.0"

* <xref:blazor/components/class-libraries>
* <xref:blazor/components/css-isolation#razor-class-library-rcl-support>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <xref:blazor/components/class-libraries>

::: moniker-end
