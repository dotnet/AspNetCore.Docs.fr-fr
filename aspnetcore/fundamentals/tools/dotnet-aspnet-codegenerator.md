---
title: commande dotnet aspnet-codegenerator
author: rick-anderson
description: La commande dotnet aspnet-codegenerator structure des projets ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/16/2020
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
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: 8844b0014cac58f414d79df4c64bc0efac75bfe1
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "94920701"
---
# <a name="dotnet-aspnet-codegenerator"></a><span data-ttu-id="5e1c2-103">dotnet aspnet-codegenerator</span><span class="sxs-lookup"><span data-stu-id="5e1c2-103">dotnet aspnet-codegenerator</span></span>

<span data-ttu-id="5e1c2-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5e1c2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="5e1c2-105">`dotnet aspnet-codegenerator` - Exécute le moteur de génération de modèles automatique ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5e1c2-105">`dotnet aspnet-codegenerator` - Runs the ASP.NET Core scaffolding engine.</span></span> <span data-ttu-id="5e1c2-106">`dotnet aspnet-codegenerator` étant uniquement requis pour générer automatiquement des modèles à partir de la ligne de commande, il n’est pas nécessaire d’utiliser la génération de modèles automatique avec Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5e1c2-106">`dotnet aspnet-codegenerator` is only required to scaffold from the command line, it's not needed to use scaffolding with Visual Studio.</span></span>

## <a name="install-and-update-aspnet-codegenerator"></a><span data-ttu-id="5e1c2-107">Installer et mettre à jour ASPNET-CodeGenerator</span><span class="sxs-lookup"><span data-stu-id="5e1c2-107">Install and update aspnet-codegenerator</span></span>

<span data-ttu-id="5e1c2-108">Installez le [Kit de développement logiciel (SDK) .net](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="5e1c2-108">Install the [.NET SDK](https://dotnet.microsoft.com/download).</span></span>

<span data-ttu-id="5e1c2-109">`dotnet-aspnet-codegenerator` est un [outil global](/dotnet/core/tools/global-tools) qui doit être installé.</span><span class="sxs-lookup"><span data-stu-id="5e1c2-109">`dotnet-aspnet-codegenerator` is a [global tool](/dotnet/core/tools/global-tools) that must be installed.</span></span> <span data-ttu-id="5e1c2-110">La commande suivante installe la dernière version stable de l’outil `dotnet-aspnet-codegenerator` :</span><span class="sxs-lookup"><span data-stu-id="5e1c2-110">The following command installs the latest stable version of the `dotnet-aspnet-codegenerator` tool:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="5e1c2-111">La commande suivante met à jour `dotnet-aspnet-codegenerator` vers la dernière version stable disponible à partir du SDK .NET Core installé :</span><span class="sxs-lookup"><span data-stu-id="5e1c2-111">The following command updates `dotnet-aspnet-codegenerator` to the latest stable version available from the installed .NET Core SDKs:</span></span>

```dotnetcli
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="uninstall-aspnet-codegenerator"></a><span data-ttu-id="5e1c2-112">Désinstaller ASPNET-CodeGenerator</span><span class="sxs-lookup"><span data-stu-id="5e1c2-112">Uninstall aspnet-codegenerator</span></span>

<span data-ttu-id="5e1c2-113">Il peut être nécessaire de désinstaller `aspnet-codegenerator` pour résoudre les problèmes.</span><span class="sxs-lookup"><span data-stu-id="5e1c2-113">It may be necessary to uninstall the `aspnet-codegenerator` to resolve problems.</span></span> <span data-ttu-id="5e1c2-114">Par exemple, si vous avez installé une version préliminaire de `aspnet-codegenerator` , désinstallez-la avant d’installer la version finale.</span><span class="sxs-lookup"><span data-stu-id="5e1c2-114">For example, if you installed a preview version of `aspnet-codegenerator`, uninstall it before installing the released version.</span></span>

<span data-ttu-id="5e1c2-115">Les commandes suivantes `dotnet-aspnet-codegenerator` désinstallent l’outil et installent la dernière version stable :</span><span class="sxs-lookup"><span data-stu-id="5e1c2-115">The following commands uninstall the `dotnet-aspnet-codegenerator` tool and installs the latest stable version:</span></span>

```dotnetcli
dotnet tool uninstall -g dotnet-aspnet-codegenerator
dotnet tool install -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a><span data-ttu-id="5e1c2-116">Synopsis</span><span class="sxs-lookup"><span data-stu-id="5e1c2-116">Synopsis</span></span>

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a><span data-ttu-id="5e1c2-117">Description</span><span class="sxs-lookup"><span data-stu-id="5e1c2-117">Description</span></span>

<span data-ttu-id="5e1c2-118">La commande globale `dotnet aspnet-codegenerator` exécute le générateur de code ASP.NET Core et le moteur de génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="5e1c2-118">The `dotnet aspnet-codegenerator` global command runs the ASP.NET Core code generator and scaffolding engine.</span></span>

## <a name="arguments"></a><span data-ttu-id="5e1c2-119">Arguments</span><span class="sxs-lookup"><span data-stu-id="5e1c2-119">Arguments</span></span>

`generator`

<span data-ttu-id="5e1c2-120">Le générateur de code à effectuer.</span><span class="sxs-lookup"><span data-stu-id="5e1c2-120">The code generator to run.</span></span> <span data-ttu-id="5e1c2-121">Les générateurs suivants sont disponibles :</span><span class="sxs-lookup"><span data-stu-id="5e1c2-121">The following generators are available:</span></span>

| <span data-ttu-id="5e1c2-122">Générateur</span><span class="sxs-lookup"><span data-stu-id="5e1c2-122">Generator</span></span>  | <span data-ttu-id="5e1c2-123">Opération</span><span class="sxs-lookup"><span data-stu-id="5e1c2-123">Operation</span></span>                                                            |
| ---------- | -------------------------------------------------------------------- |
| <span data-ttu-id="5e1c2-124">superficie</span><span class="sxs-lookup"><span data-stu-id="5e1c2-124">area</span></span>       | [<span data-ttu-id="5e1c2-125">Génération de modèles automatique pour une zone</span><span class="sxs-lookup"><span data-stu-id="5e1c2-125">Scaffolds an Area</span></span>](xref:mvc/controllers/areas)                      |
| <span data-ttu-id="5e1c2-126">contrôleur</span><span class="sxs-lookup"><span data-stu-id="5e1c2-126">controller</span></span> | [<span data-ttu-id="5e1c2-127">Génération de modèles automatique pour un contrôleur</span><span class="sxs-lookup"><span data-stu-id="5e1c2-127">Scaffolds a controller</span></span>](xref:tutorials/first-mvc-app/adding-model)  |
| <span data-ttu-id="5e1c2-128">identité</span><span class="sxs-lookup"><span data-stu-id="5e1c2-128">identity</span></span>   | [<span data-ttu-id="5e1c2-129">Structure Identity</span><span class="sxs-lookup"><span data-stu-id="5e1c2-129">Scaffolds Identity</span></span>](xref:security/authentication/scaffold-identity) |
| <span data-ttu-id="5e1c2-130">razorpage</span><span class="sxs-lookup"><span data-stu-id="5e1c2-130">razorpage</span></span>  | [<span data-ttu-id="5e1c2-131">Pages de modèles Razor</span><span class="sxs-lookup"><span data-stu-id="5e1c2-131">Scaffolds Razor Pages</span></span>](xref:tutorials/razor-pages/model)            |
| <span data-ttu-id="5e1c2-132">vue</span><span class="sxs-lookup"><span data-stu-id="5e1c2-132">view</span></span>       | [<span data-ttu-id="5e1c2-133">Génération de modèles automatique pour une vue</span><span class="sxs-lookup"><span data-stu-id="5e1c2-133">Scaffolds a view</span></span>](xref:mvc/views/overview)                          |

## <a name="options"></a><span data-ttu-id="5e1c2-134">Options</span><span class="sxs-lookup"><span data-stu-id="5e1c2-134">Options</span></span>

`-n|--nuget-package-dir`

<span data-ttu-id="5e1c2-135">Spécifie le répertoire du package NuGet.</span><span class="sxs-lookup"><span data-stu-id="5e1c2-135">Specifies the NuGet package directory.</span></span>

`-c|--configuration {Debug|Release}`

<span data-ttu-id="5e1c2-136">Définit la configuration de build.</span><span class="sxs-lookup"><span data-stu-id="5e1c2-136">Defines the build configuration.</span></span> <span data-ttu-id="5e1c2-137">La valeur par défaut est `Debug`.</span><span class="sxs-lookup"><span data-stu-id="5e1c2-137">The default value is `Debug`.</span></span>

`-tfm|--target-framework`

<span data-ttu-id="5e1c2-138">[Framework](/dotnet/standard/frameworks) cible à utiliser.</span><span class="sxs-lookup"><span data-stu-id="5e1c2-138">Target [Framework](/dotnet/standard/frameworks) to use.</span></span> <span data-ttu-id="5e1c2-139">Par exemple : `net46`.</span><span class="sxs-lookup"><span data-stu-id="5e1c2-139">For example, `net46`.</span></span>

`-b|--build-base-path`

<span data-ttu-id="5e1c2-140">Le chemin de base de génération.</span><span class="sxs-lookup"><span data-stu-id="5e1c2-140">The build base path.</span></span>

`-h|--help`

<span data-ttu-id="5e1c2-141">Affiche une aide brève pour la commande.</span><span class="sxs-lookup"><span data-stu-id="5e1c2-141">Prints out a short help for the command.</span></span>

`--no-build`

<span data-ttu-id="5e1c2-142">Ne génère pas le projet avant l’exécution.</span><span class="sxs-lookup"><span data-stu-id="5e1c2-142">Doesn't build the project before running.</span></span> <span data-ttu-id="5e1c2-143">L’indicateur `--no-restore` est également défini implicitement.</span><span class="sxs-lookup"><span data-stu-id="5e1c2-143">It also implicitly sets the `--no-restore` flag.</span></span>

`-p|--project <PATH>`

<span data-ttu-id="5e1c2-144">Spécifie le chemin du fichier projet à exécuter (nom de dossier ou chemin complet).</span><span class="sxs-lookup"><span data-stu-id="5e1c2-144">Specifies the path of the project file to run (folder name or full path).</span></span> <span data-ttu-id="5e1c2-145">Si aucune valeur n’est spécifiée, le répertoire actif est utilisé par défaut.</span><span class="sxs-lookup"><span data-stu-id="5e1c2-145">If not specified, it defaults to the current directory.</span></span>

## <a name="generator-options"></a><span data-ttu-id="5e1c2-146">Options du générateur</span><span class="sxs-lookup"><span data-stu-id="5e1c2-146">Generator options</span></span>

<span data-ttu-id="5e1c2-147">Les sections suivantes décrivent en détail les options disponibles pour les générateurs pris en charge :</span><span class="sxs-lookup"><span data-stu-id="5e1c2-147">The following sections detail the options available for the supported generators:</span></span>

* <span data-ttu-id="5e1c2-148">Domaine</span><span class="sxs-lookup"><span data-stu-id="5e1c2-148">Area</span></span>
* <span data-ttu-id="5e1c2-149">Contrôleur</span><span class="sxs-lookup"><span data-stu-id="5e1c2-149">Controller</span></span>
* Identity  
* <span data-ttu-id="5e1c2-150">Razorpage</span><span class="sxs-lookup"><span data-stu-id="5e1c2-150">Razorpage</span></span>
* <span data-ttu-id="5e1c2-151">Affichage</span><span class="sxs-lookup"><span data-stu-id="5e1c2-151">View</span></span>

<a name="area"></a>

### <a name="area-options"></a><span data-ttu-id="5e1c2-152">Options de zone</span><span class="sxs-lookup"><span data-stu-id="5e1c2-152">Area options</span></span>

<span data-ttu-id="5e1c2-153">Cet outil est conçu pour les projets web ASP.NET Core avec des contrôleurs et des vues.</span><span class="sxs-lookup"><span data-stu-id="5e1c2-153">This tool is intended for ASP.NET Core web projects with controllers and views.</span></span> <span data-ttu-id="5e1c2-154">Elle n’est pas destinée aux Razor applications pages.</span><span class="sxs-lookup"><span data-stu-id="5e1c2-154">It's not intended for Razor Pages apps.</span></span>

<span data-ttu-id="5e1c2-155">Utilisation : `dotnet aspnet-codegenerator area AreaNameToGenerate`</span><span class="sxs-lookup"><span data-stu-id="5e1c2-155">Usage: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span></span>

<span data-ttu-id="5e1c2-156">La commande précédente génère les dossiers suivants :</span><span class="sxs-lookup"><span data-stu-id="5e1c2-156">The preceding command generates the following folders:</span></span>

* <span data-ttu-id="5e1c2-157">*Régions*</span><span class="sxs-lookup"><span data-stu-id="5e1c2-157">*Areas*</span></span>
  * <span data-ttu-id="5e1c2-158">*AreaNameToGenerate*</span><span class="sxs-lookup"><span data-stu-id="5e1c2-158">*AreaNameToGenerate*</span></span>
    * <span data-ttu-id="5e1c2-159">*Controllers*</span><span class="sxs-lookup"><span data-stu-id="5e1c2-159">*Controllers*</span></span>
    * <span data-ttu-id="5e1c2-160">*Données*</span><span class="sxs-lookup"><span data-stu-id="5e1c2-160">*Data*</span></span>
    * <span data-ttu-id="5e1c2-161">*Modèles*</span><span class="sxs-lookup"><span data-stu-id="5e1c2-161">*Models*</span></span>
    * <span data-ttu-id="5e1c2-162">*Views*</span><span class="sxs-lookup"><span data-stu-id="5e1c2-162">*Views*</span></span>

<a name="ctl"></a>

### <a name="controller-options"></a><span data-ttu-id="5e1c2-163">Options de contrôleur</span><span class="sxs-lookup"><span data-stu-id="5e1c2-163">Controller options</span></span>

<span data-ttu-id="5e1c2-164">Le tableau suivant répertorie les options pour  `aspnet-codegenerator` `controller` et `razorpage` :</span><span class="sxs-lookup"><span data-stu-id="5e1c2-164">The following table lists options for  `aspnet-codegenerator` `controller` and `razorpage`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="5e1c2-165">Le tableau ci-dessous répertorie les options uniques à `aspnet-codegenerator controller` :</span><span class="sxs-lookup"><span data-stu-id="5e1c2-165">The following table lists options unique to  `aspnet-codegenerator controller`:</span></span>

| <span data-ttu-id="5e1c2-166">Option</span><span class="sxs-lookup"><span data-stu-id="5e1c2-166">Option</span></span>                         | <span data-ttu-id="5e1c2-167">Description</span><span class="sxs-lookup"><span data-stu-id="5e1c2-167">Description</span></span>                                                                                               |
| ------------------------------ | --------------------------------------------------------------------------------------------------------- |
| <span data-ttu-id="5e1c2-168">--controllerName ou -name</span><span class="sxs-lookup"><span data-stu-id="5e1c2-168">--controllerName or -name</span></span>      | <span data-ttu-id="5e1c2-169">Nom du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="5e1c2-169">Name of the controller.</span></span>                                                                                   |
| <span data-ttu-id="5e1c2-170">--useAsyncActions ou -async</span><span class="sxs-lookup"><span data-stu-id="5e1c2-170">--useAsyncActions or -async</span></span>    | <span data-ttu-id="5e1c2-171">Générer des actions asynchrones du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="5e1c2-171">Generate async controller actions.</span></span>                                                                        |
| <span data-ttu-id="5e1c2-172">--noViews ou -nv</span><span class="sxs-lookup"><span data-stu-id="5e1c2-172">--noViews or -nv</span></span>               | <span data-ttu-id="5e1c2-173">Ne générer **aucune** vue.</span><span class="sxs-lookup"><span data-stu-id="5e1c2-173">Generate **no** views.</span></span>                                                                                    |
| <span data-ttu-id="5e1c2-174">--restWithNoViews ou -api</span><span class="sxs-lookup"><span data-stu-id="5e1c2-174">--restWithNoViews or -api</span></span>      | <span data-ttu-id="5e1c2-175">Générer un contrôleur avec l’API de style REST.</span><span class="sxs-lookup"><span data-stu-id="5e1c2-175">Generate a Controller with REST style API.</span></span> <span data-ttu-id="5e1c2-176">`noViews` est supposé et les options associées à la vue sont ignorées.</span><span class="sxs-lookup"><span data-stu-id="5e1c2-176">`noViews` is assumed and any view related options are ignored.</span></span> |
| <span data-ttu-id="5e1c2-177">--readWriteActions ou -actions</span><span class="sxs-lookup"><span data-stu-id="5e1c2-177">--readWriteActions or -actions</span></span> | <span data-ttu-id="5e1c2-178">Générer un contrôleur avec actions en lecture/écriture sans modèle.</span><span class="sxs-lookup"><span data-stu-id="5e1c2-178">Generate controller with read/write actions without a model.</span></span>                                              |

<span data-ttu-id="5e1c2-179">Utilisez le commutateur `-h` pour obtenir de l’aide sur la commande `aspnet-codegenerator controller` :</span><span class="sxs-lookup"><span data-stu-id="5e1c2-179">Use the `-h` switch for help on the `aspnet-codegenerator controller` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

<span data-ttu-id="5e1c2-180">Consultez [Générer automatiquement le modèle de film](xref:tutorials/first-mvc-app/adding-model) pour obtenir un exemple de `dotnet aspnet-codegenerator controller`.</span><span class="sxs-lookup"><span data-stu-id="5e1c2-180">See [Scaffold the movie model](xref:tutorials/first-mvc-app/adding-model) for an example of `dotnet aspnet-codegenerator controller`.</span></span>

### <a name="no-locrazorpage"></a><span data-ttu-id="5e1c2-181">Razorpage</span><span class="sxs-lookup"><span data-stu-id="5e1c2-181">Razorpage</span></span>

<a name="rp"></a>

<span data-ttu-id="5e1c2-182">Razor Les pages peuvent être structurées individuellement en spécifiant le nom de la nouvelle page et le modèle à utiliser.</span><span class="sxs-lookup"><span data-stu-id="5e1c2-182">Razor Pages can be individually scaffolded by specifying the name of the new page and the template to use.</span></span> <span data-ttu-id="5e1c2-183">Les modèles pris en charge sont :</span><span class="sxs-lookup"><span data-stu-id="5e1c2-183">The supported templates are:</span></span>

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="5e1c2-184">Par exemple, la commande suivante utilise le modèle de modification pour générer *MyEdit.cshtml* et *MyEdit.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="5e1c2-184">For example, the following command uses the Edit template to generate *MyEdit.cshtml* and *MyEdit.cshtml.cs*:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

<span data-ttu-id="5e1c2-185">En règle générale, le modèle et le nom de fichier générés ne sont pas spécifiés, et les modèles suivants sont créés :</span><span class="sxs-lookup"><span data-stu-id="5e1c2-185">Typically, the template and generated file name is not specified, and the following templates are created:</span></span>

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="5e1c2-186">Le tableau suivant répertorie les options pour  `aspnet-codegenerator` `razorpage` et `controller` :</span><span class="sxs-lookup"><span data-stu-id="5e1c2-186">The following table lists options for  `aspnet-codegenerator` `razorpage` and `controller`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="5e1c2-187">Le tableau ci-dessous répertorie les options uniques à `aspnet-codegenerator razorpage` :</span><span class="sxs-lookup"><span data-stu-id="5e1c2-187">The following table lists options unique to  `aspnet-codegenerator razorpage`:</span></span>

| <span data-ttu-id="5e1c2-188">Option</span><span class="sxs-lookup"><span data-stu-id="5e1c2-188">Option</span></span>                        | <span data-ttu-id="5e1c2-189">Description</span><span class="sxs-lookup"><span data-stu-id="5e1c2-189">Description</span></span>                                                                           |
| ----------------------------- | ------------------------------------------------------------------------------------- |
| <span data-ttu-id="5e1c2-190">--namespaceName ou -namespace</span><span class="sxs-lookup"><span data-stu-id="5e1c2-190">--namespaceName or -namespace</span></span> | <span data-ttu-id="5e1c2-191">Nom de l’espace de noms à utiliser pour le PageModel généré</span><span class="sxs-lookup"><span data-stu-id="5e1c2-191">The name of the namespace to use for the generated PageModel</span></span>                          |
| <span data-ttu-id="5e1c2-192">--partialView ou -partial</span><span class="sxs-lookup"><span data-stu-id="5e1c2-192">--partialView or -partial</span></span>     | <span data-ttu-id="5e1c2-193">Générer une vue partielle.</span><span class="sxs-lookup"><span data-stu-id="5e1c2-193">Generate a partial view.</span></span> <span data-ttu-id="5e1c2-194">Les options de mise en page -l et -udl sont ignorées si ceci est spécifié.</span><span class="sxs-lookup"><span data-stu-id="5e1c2-194">Layout options -l and -udl are ignored if this is specified.</span></span> |
| <span data-ttu-id="5e1c2-195">--noPageModel ou -npm</span><span class="sxs-lookup"><span data-stu-id="5e1c2-195">--noPageModel or -npm</span></span>         | <span data-ttu-id="5e1c2-196">Choisir de ne pas générer une classe PageModel pour le modèle vide</span><span class="sxs-lookup"><span data-stu-id="5e1c2-196">Switch to not generate a PageModel class for Empty template</span></span>                           |

<span data-ttu-id="5e1c2-197">Utilisez le commutateur `-h` pour obtenir de l’aide sur la commande `aspnet-codegenerator razorpage` :</span><span class="sxs-lookup"><span data-stu-id="5e1c2-197">Use the `-h` switch for help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

<span data-ttu-id="5e1c2-198">Consultez [Générer automatiquement le modèle de film](xref:tutorials/razor-pages/model) pour obtenir un exemple de `dotnet aspnet-codegenerator razorpage`.</span><span class="sxs-lookup"><span data-stu-id="5e1c2-198">See [Scaffold the movie model](xref:tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator razorpage`.</span></span>

### Identity

<span data-ttu-id="5e1c2-199">Voir [l' Identity échafaudage](xref:security/authentication/scaffold-identity)</span><span class="sxs-lookup"><span data-stu-id="5e1c2-199">See [Scaffold Identity](xref:security/authentication/scaffold-identity)</span></span>
