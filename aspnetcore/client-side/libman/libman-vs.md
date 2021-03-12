---
title: Utiliser LibMan avec ASP.NET Core dans Visual Studio
author: scottaddie
description: Découvrez comment utiliser LibMan dans un projet ASP.NET Core avec Visual Studio.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/20/2018
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
uid: client-side/libman/libman-vs
ms.openlocfilehash: ae2bc34edaea2df6f329e47b00c7c02cc59d03bd
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589268"
---
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a><span data-ttu-id="20168-103">Utiliser LibMan avec ASP.NET Core dans Visual Studio</span><span class="sxs-lookup"><span data-stu-id="20168-103">Use LibMan with ASP.NET Core in Visual Studio</span></span>

<span data-ttu-id="20168-104">Par [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="20168-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="20168-105">Visual Studio offre une prise en charge intégrée de [LibMan](xref:client-side/libman/index) dans ASP.net Core projets, notamment :</span><span class="sxs-lookup"><span data-stu-id="20168-105">Visual Studio has built-in support for [LibMan](xref:client-side/libman/index) in ASP.NET Core projects, including:</span></span>

* <span data-ttu-id="20168-106">Prise en charge de la configuration et de l’exécution des opérations de restauration LibMan sur Build.</span><span class="sxs-lookup"><span data-stu-id="20168-106">Support for configuring and running LibMan restore operations on build.</span></span>
* <span data-ttu-id="20168-107">Éléments de menu pour déclencher des opérations de restauration et de nettoyage LibMan.</span><span class="sxs-lookup"><span data-stu-id="20168-107">Menu items for triggering LibMan restore and clean operations.</span></span>
* <span data-ttu-id="20168-108">Boîte de dialogue Rechercher pour rechercher des bibliothèques et ajouter les fichiers à un projet.</span><span class="sxs-lookup"><span data-stu-id="20168-108">Search dialog for finding libraries and adding the files to a project.</span></span>
* <span data-ttu-id="20168-109">La prise en charge de la modification de *libman.jssur* &mdash; le fichier manifeste LibMan.</span><span class="sxs-lookup"><span data-stu-id="20168-109">Editing support for *libman.json*&mdash;the LibMan manifest file.</span></span>

<span data-ttu-id="20168-110">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/client-side/libman/samples/) [(procédure de téléchargement)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="20168-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/client-side/libman/samples/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="20168-111">Prérequis</span><span class="sxs-lookup"><span data-stu-id="20168-111">Prerequisites</span></span>

* <span data-ttu-id="20168-112">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) avec la charge de travail de **développement Web et ASP.net**</span><span class="sxs-lookup"><span data-stu-id="20168-112">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>

## <a name="add-library-files"></a><span data-ttu-id="20168-113">Ajouter des fichiers de bibliothèque</span><span class="sxs-lookup"><span data-stu-id="20168-113">Add library files</span></span>

<span data-ttu-id="20168-114">Les fichiers de bibliothèque peuvent être ajoutés à un projet ASP.NET Core de deux manières différentes :</span><span class="sxs-lookup"><span data-stu-id="20168-114">Library files can be added to an ASP.NET Core project in two different ways:</span></span>

1. [<span data-ttu-id="20168-115">Utilisez la boîte de dialogue Ajouter une bibliothèque de Client-Side</span><span class="sxs-lookup"><span data-stu-id="20168-115">Use the Add Client-Side Library dialog</span></span>](#use-the-add-client-side-library-dialog)
1. [<span data-ttu-id="20168-116">Configurer manuellement les entrées du fichier manifeste LibMan</span><span class="sxs-lookup"><span data-stu-id="20168-116">Manually configure LibMan manifest file entries</span></span>](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a><span data-ttu-id="20168-117">Utilisez la boîte de dialogue Ajouter une bibliothèque de Client-Side</span><span class="sxs-lookup"><span data-stu-id="20168-117">Use the Add Client-Side Library dialog</span></span>

<span data-ttu-id="20168-118">Procédez comme suit pour installer une bibliothèque côté client :</span><span class="sxs-lookup"><span data-stu-id="20168-118">Follow these steps to install a client-side library:</span></span>

* <span data-ttu-id="20168-119">Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le dossier du projet dans lequel les fichiers doivent être ajoutés.</span><span class="sxs-lookup"><span data-stu-id="20168-119">In **Solution Explorer**, right-click the project folder in which the files should be added.</span></span> <span data-ttu-id="20168-120">Sélectionnez **Ajouter**  >  **une bibliothèque côté client**.</span><span class="sxs-lookup"><span data-stu-id="20168-120">Choose **Add** > **Client-Side Library**.</span></span> <span data-ttu-id="20168-121">La boîte de dialogue **Ajouter une bibliothèque de Client-Side** s’affiche :</span><span class="sxs-lookup"><span data-stu-id="20168-121">The **Add Client-Side Library** dialog appears:</span></span>

  ![Boîte de dialogue Ajouter une bibliothèque de Client-Side](_static/add-library-dialog.png)

* <span data-ttu-id="20168-123">Sélectionnez le fournisseur de bibliothèque dans la liste déroulante **fournisseur** .</span><span class="sxs-lookup"><span data-stu-id="20168-123">Select the library provider from the **Provider** drop down.</span></span> <span data-ttu-id="20168-124">CDNJS est le fournisseur par défaut.</span><span class="sxs-lookup"><span data-stu-id="20168-124">CDNJS is the default provider.</span></span>
* <span data-ttu-id="20168-125">Tapez le nom de la bibliothèque à extraire dans la zone de texte **bibliothèque** .</span><span class="sxs-lookup"><span data-stu-id="20168-125">Type the library name to fetch in the **Library** text box.</span></span> <span data-ttu-id="20168-126">IntelliSense fournit une liste de bibliothèques qui commencent par le texte fourni.</span><span class="sxs-lookup"><span data-stu-id="20168-126">IntelliSense provides a list of libraries beginning with the provided text.</span></span>
* <span data-ttu-id="20168-127">Sélectionnez la bibliothèque dans la liste IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="20168-127">Select the library from the IntelliSense list.</span></span> <span data-ttu-id="20168-128">Notez que le nom de la bibliothèque est suivi du `@` symbole et de la dernière version stable connue du fournisseur sélectionné.</span><span class="sxs-lookup"><span data-stu-id="20168-128">Notice the library name is suffixed with the `@` symbol and the latest stable version known to the selected provider.</span></span>
* <span data-ttu-id="20168-129">Choisir les fichiers à inclure :</span><span class="sxs-lookup"><span data-stu-id="20168-129">Decide which files to include:</span></span>
  * <span data-ttu-id="20168-130">Sélectionnez la case d’option **inclure tous les fichiers de bibliothèque** pour inclure tous les fichiers de la bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="20168-130">Select the **Include all library files** radio button to include all of the library's files.</span></span>
  * <span data-ttu-id="20168-131">Sélectionnez la case d’option **choisir des fichiers spécifiques** pour inclure un sous-ensemble des fichiers de la bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="20168-131">Select the **Choose specific files** radio button to include a subset of the library's files.</span></span> <span data-ttu-id="20168-132">Lorsque la case d’option est sélectionnée, l’arborescence du sélecteur de fichiers est activée.</span><span class="sxs-lookup"><span data-stu-id="20168-132">When the radio button is selected, the file selector tree is enabled.</span></span> <span data-ttu-id="20168-133">Activez les cases à cocher à gauche des noms de fichiers à télécharger.</span><span class="sxs-lookup"><span data-stu-id="20168-133">Check the boxes to the left of the file names to download.</span></span>
* <span data-ttu-id="20168-134">Spécifiez le dossier du projet pour le stockage des fichiers dans la zone de texte **emplacement cible** .</span><span class="sxs-lookup"><span data-stu-id="20168-134">Specify the project folder for storing the files in the **Target Location** text box.</span></span> <span data-ttu-id="20168-135">En guise de recommandation, stockez chaque bibliothèque dans un dossier distinct.</span><span class="sxs-lookup"><span data-stu-id="20168-135">As a recommendation, store each library in a separate folder.</span></span>

  <span data-ttu-id="20168-136">Le dossier d' **emplacement cible** suggéré est basé sur l’emplacement à partir duquel la boîte de dialogue a été lancée :</span><span class="sxs-lookup"><span data-stu-id="20168-136">The suggested **Target Location** folder is based on the location from which the dialog launched:</span></span>

  * <span data-ttu-id="20168-137">S’il est lancé à partir de la racine du projet :</span><span class="sxs-lookup"><span data-stu-id="20168-137">If launched from the project root:</span></span>
    * <span data-ttu-id="20168-138">*wwwroot/lib* est utilisé si *wwwroot* existe.</span><span class="sxs-lookup"><span data-stu-id="20168-138">*wwwroot/lib* is used if *wwwroot* exists.</span></span>
    * <span data-ttu-id="20168-139">*lib* est utilisé si *wwwroot* n’existe pas.</span><span class="sxs-lookup"><span data-stu-id="20168-139">*lib* is used if *wwwroot* doesn't exist.</span></span>
  * <span data-ttu-id="20168-140">S’il est lancé à partir d’un dossier de projet, le nom de dossier correspondant est utilisé.</span><span class="sxs-lookup"><span data-stu-id="20168-140">If launched from a project folder, the corresponding folder name is used.</span></span>

  <span data-ttu-id="20168-141">La suggestion de dossier est suivie du suffixe du nom de la bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="20168-141">The folder suggestion is suffixed with the library name.</span></span> <span data-ttu-id="20168-142">Le tableau suivant illustre les suggestions de dossiers lors de l’installation de jQuery dans un Razor projet pages.</span><span class="sxs-lookup"><span data-stu-id="20168-142">The following table illustrates folder suggestions when installing jQuery in a Razor Pages project.</span></span>
  
  |<span data-ttu-id="20168-143">Emplacement de lancement</span><span class="sxs-lookup"><span data-stu-id="20168-143">Launch location</span></span>                           |<span data-ttu-id="20168-144">Dossier suggéré</span><span class="sxs-lookup"><span data-stu-id="20168-144">Suggested folder</span></span>      |
  |------------------------------------------|----------------------|
  |<span data-ttu-id="20168-145">racine du projet (si *wwwroot* existe)</span><span class="sxs-lookup"><span data-stu-id="20168-145">project root (if *wwwroot* exists)</span></span>        |<span data-ttu-id="20168-146">*wwwroot/lib/jQuery/*</span><span class="sxs-lookup"><span data-stu-id="20168-146">*wwwroot/lib/jquery/*</span></span> |
  |<span data-ttu-id="20168-147">racine du projet (si *wwwroot* n’existe pas)</span><span class="sxs-lookup"><span data-stu-id="20168-147">project root (if *wwwroot* doesn't exist)</span></span> |<span data-ttu-id="20168-148">*lib/jQuery/*</span><span class="sxs-lookup"><span data-stu-id="20168-148">*lib/jquery/*</span></span>         |
  |<span data-ttu-id="20168-149">Dossier *pages* dans Project</span><span class="sxs-lookup"><span data-stu-id="20168-149">*Pages* folder in project</span></span>                 |<span data-ttu-id="20168-150">*Pages/jQuery/*</span><span class="sxs-lookup"><span data-stu-id="20168-150">*Pages/jquery/*</span></span>       |

* <span data-ttu-id="20168-151">Cliquez sur le bouton **installer** pour télécharger les fichiers, conformément à la configuration de *libman.jssur*.</span><span class="sxs-lookup"><span data-stu-id="20168-151">Click the **Install** button to download the files, per the configuration in *libman.json*.</span></span>
* <span data-ttu-id="20168-152">Consultez le flux du **Gestionnaire de bibliothèques** de la fenêtre **sortie** pour plus d’informations sur l’installation.</span><span class="sxs-lookup"><span data-stu-id="20168-152">Review the **Library Manager** feed of the **Output** window for installation details.</span></span> <span data-ttu-id="20168-153">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="20168-153">For example:</span></span>

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

### <a name="manually-configure-libman-manifest-file-entries"></a><span data-ttu-id="20168-154">Configurer manuellement les entrées du fichier manifeste LibMan</span><span class="sxs-lookup"><span data-stu-id="20168-154">Manually configure LibMan manifest file entries</span></span>

<span data-ttu-id="20168-155">Toutes les opérations LibMan dans Visual Studio sont basées sur le contenu du manifeste LibMan de la racine du projet (*libman.jssur*).</span><span class="sxs-lookup"><span data-stu-id="20168-155">All LibMan operations in Visual Studio are based on the content of the project root's LibMan manifest (*libman.json*).</span></span> <span data-ttu-id="20168-156">Vous pouvez modifier manuellement *libman.jssur* pour configurer des fichiers de bibliothèque pour le projet.</span><span class="sxs-lookup"><span data-stu-id="20168-156">You can manually edit *libman.json* to configure library files for the project.</span></span> <span data-ttu-id="20168-157">Visual Studio restaure tous les fichiers de bibliothèque une fois que *libman.js* est enregistré.</span><span class="sxs-lookup"><span data-stu-id="20168-157">Visual Studio restores all library files once *libman.json* is saved.</span></span>

<span data-ttu-id="20168-158">Pour ouvrir *libman.js* pour modification, les options suivantes sont disponibles :</span><span class="sxs-lookup"><span data-stu-id="20168-158">To open *libman.json* for editing, the following options exist:</span></span>

* <span data-ttu-id="20168-159">Double-cliquez sur le fichier *libman.js* dans **Explorateur de solutions**.</span><span class="sxs-lookup"><span data-stu-id="20168-159">Double-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="20168-160">Dans **Explorateur de solutions** , cliquez avec le bouton droit sur le projet, puis sélectionnez **gérer les bibliothèques de Client-Side**.</span><span class="sxs-lookup"><span data-stu-id="20168-160">Right-click the project in **Solution Explorer** and select **Manage Client-Side Libraries**.</span></span> <span data-ttu-id="20168-161">**&#8224;**</span><span class="sxs-lookup"><span data-stu-id="20168-161">**&#8224;**</span></span>
* <span data-ttu-id="20168-162">Sélectionnez **gérer les bibliothèques de Client-Side** dans le menu **projet** de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="20168-162">Select **Manage Client-Side Libraries** from the Visual Studio **Project** menu.</span></span> <span data-ttu-id="20168-163">**&#8224;**</span><span class="sxs-lookup"><span data-stu-id="20168-163">**&#8224;**</span></span>

<span data-ttu-id="20168-164">**&#8224;** Si le *libman.jssur* le fichier n’existe pas encore dans la racine du projet, il sera créé avec le contenu du modèle d’élément par défaut.</span><span class="sxs-lookup"><span data-stu-id="20168-164">**&#8224;** If the *libman.json* file doesn't already exist in the project root, it will be created with the default item template content.</span></span>

<span data-ttu-id="20168-165">Visual Studio offre une prise en charge enrichie de l’édition JSON, comme la colorisation, la mise en forme, IntelliSense et la validation de schéma.</span><span class="sxs-lookup"><span data-stu-id="20168-165">Visual Studio offers rich JSON editing support such as colorization, formatting, IntelliSense, and schema validation.</span></span> <span data-ttu-id="20168-166">Le schéma JSON du manifeste LibMan se trouve à l’adresse [https://json.schemastore.org/libman](https://json.schemastore.org/libman) .</span><span class="sxs-lookup"><span data-stu-id="20168-166">The LibMan manifest's JSON schema is found at [https://json.schemastore.org/libman](https://json.schemastore.org/libman).</span></span>

<span data-ttu-id="20168-167">Avec le fichier manifeste suivant, LibMan récupère les fichiers selon la configuration définie dans la `libraries` propriété.</span><span class="sxs-lookup"><span data-stu-id="20168-167">With the following manifest file, LibMan retrieves files per the configuration defined in the `libraries` property.</span></span> <span data-ttu-id="20168-168">Une explication des littéraux d’objet définis dans `libraries` suit :</span><span class="sxs-lookup"><span data-stu-id="20168-168">An explanation of the object literals defined within `libraries` follows:</span></span>

* <span data-ttu-id="20168-169">Un sous-ensemble de [jQuery](https://jquery.com/) version 3.3.1 est récupéré à partir du fournisseur CDNJS.</span><span class="sxs-lookup"><span data-stu-id="20168-169">A subset of [jQuery](https://jquery.com/) version 3.3.1 is retrieved from the CDNJS provider.</span></span> <span data-ttu-id="20168-170">Le sous-ensemble est défini dans la `files` propriété &mdash; *jquery.min.js*, *jquery.js* et *jQuery. min. map*.</span><span class="sxs-lookup"><span data-stu-id="20168-170">The subset is defined in the `files` property&mdash;*jquery.min.js*, *jquery.js*, and *jquery.min.map*.</span></span> <span data-ttu-id="20168-171">Les fichiers sont placés dans le dossier *wwwroot/lib/jQuery* du projet.</span><span class="sxs-lookup"><span data-stu-id="20168-171">The files are placed in the project's *wwwroot/lib/jquery* folder.</span></span>
* <span data-ttu-id="20168-172">L’intégralité de la version de [bootstrap](https://getbootstrap.com/) 4.1.3 est extraite et placée dans un dossier *wwwroot/lib/bootstrap* .</span><span class="sxs-lookup"><span data-stu-id="20168-172">The entirety of [Bootstrap](https://getbootstrap.com/) version 4.1.3 is retrieved and placed in a *wwwroot/lib/bootstrap* folder.</span></span> <span data-ttu-id="20168-173">La propriété du littéral d’objet `provider` remplace la valeur de la `defaultProvider` propriété.</span><span class="sxs-lookup"><span data-stu-id="20168-173">The object literal's `provider` property overrides the `defaultProvider` property value.</span></span> <span data-ttu-id="20168-174">LibMan récupère les fichiers de démarrage à partir du fournisseur unpkg.</span><span class="sxs-lookup"><span data-stu-id="20168-174">LibMan retrieves the Bootstrap files from the unpkg provider.</span></span>
* <span data-ttu-id="20168-175">Un sous-ensemble de [Lodash](https://lodash.com/) a été approuvé par un corps fédérateur au sein de l’organisation.</span><span class="sxs-lookup"><span data-stu-id="20168-175">A subset of [Lodash](https://lodash.com/) was approved by a governing body within the organization.</span></span> <span data-ttu-id="20168-176">Les fichiers *lodash.js* et *lodash.min.js* sont récupérés à partir du système de fichiers local à l’adresse *C : \\ temp \\ lodash \\*.</span><span class="sxs-lookup"><span data-stu-id="20168-176">The *lodash.js* and *lodash.min.js* files are retrieved from the local file system at *C:\\temp\\lodash\\*.</span></span> <span data-ttu-id="20168-177">Les fichiers sont copiés dans le dossier *wwwroot/lib/lodash* du projet.</span><span class="sxs-lookup"><span data-stu-id="20168-177">The files are copied to the project's *wwwroot/lib/lodash* folder.</span></span>

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> <span data-ttu-id="20168-178">LibMan ne prend en charge qu’une seule version de chaque bibliothèque de chaque fournisseur.</span><span class="sxs-lookup"><span data-stu-id="20168-178">LibMan only supports one version of each library from each provider.</span></span> <span data-ttu-id="20168-179">Le *libman.jssur* le fichier échoue la validation du schéma s’il contient deux bibliothèques avec le même nom de bibliothèque pour un fournisseur donné.</span><span class="sxs-lookup"><span data-stu-id="20168-179">The *libman.json* file fails schema validation if it contains two libraries with the same library name for a given provider.</span></span>

## <a name="restore-library-files"></a><span data-ttu-id="20168-180">Restaurer les fichiers de bibliothèque</span><span class="sxs-lookup"><span data-stu-id="20168-180">Restore library files</span></span>

<span data-ttu-id="20168-181">Pour restaurer des fichiers de bibliothèque à partir de Visual Studio, il doit y avoir un *libman.jsvalide sur* le fichier à la racine du projet.</span><span class="sxs-lookup"><span data-stu-id="20168-181">To restore library files from within Visual Studio, there must be a valid *libman.json* file in the project root.</span></span> <span data-ttu-id="20168-182">Les fichiers restaurés sont placés dans le projet à l’emplacement spécifié pour chaque bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="20168-182">Restored files are placed in the project at the location specified for each library.</span></span>

<span data-ttu-id="20168-183">Les fichiers de bibliothèque peuvent être restaurés dans un projet ASP.NET Core de deux manières :</span><span class="sxs-lookup"><span data-stu-id="20168-183">Library files can be restored in an ASP.NET Core project in two ways:</span></span>

1. [<span data-ttu-id="20168-184">Restaurer des fichiers pendant la génération</span><span class="sxs-lookup"><span data-stu-id="20168-184">Restore files during build</span></span>](#restore-files-during-build)
1. [<span data-ttu-id="20168-185">Restaurer manuellement les fichiers</span><span class="sxs-lookup"><span data-stu-id="20168-185">Restore files manually</span></span>](#restore-files-manually)

### <a name="restore-files-during-build"></a><span data-ttu-id="20168-186">Restaurer des fichiers pendant la génération</span><span class="sxs-lookup"><span data-stu-id="20168-186">Restore files during build</span></span>

<span data-ttu-id="20168-187">LibMan peut restaurer les fichiers de bibliothèque définis dans le cadre du processus de génération.</span><span class="sxs-lookup"><span data-stu-id="20168-187">LibMan can restore the defined library files as part of the build process.</span></span> <span data-ttu-id="20168-188">Par défaut, le comportement *de Restore-on-Build* est désactivé.</span><span class="sxs-lookup"><span data-stu-id="20168-188">By default, the *restore-on-build* behavior is disabled.</span></span>

<span data-ttu-id="20168-189">Pour activer et tester le comportement de Restore-on-Build :</span><span class="sxs-lookup"><span data-stu-id="20168-189">To enable and test the restore-on-build behavior:</span></span>

* <span data-ttu-id="20168-190">Cliquez avec le bouton droit sur *libman.js* dans **Explorateur de solutions** puis sélectionnez **activer la restauration des bibliothèques de Client-Side sur la build** dans le menu contextuel.</span><span class="sxs-lookup"><span data-stu-id="20168-190">Right-click *libman.json* in **Solution Explorer** and select **Enable Restore Client-Side Libraries on Build** from the context menu.</span></span>
* <span data-ttu-id="20168-191">Cliquez sur le bouton **Oui** lorsque vous êtes invité à installer un package NuGet.</span><span class="sxs-lookup"><span data-stu-id="20168-191">Click the **Yes** button when prompted to install a NuGet package.</span></span> <span data-ttu-id="20168-192">Le package NuGet [Microsoft. Web. librarymanager. Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) est ajouté au projet :</span><span class="sxs-lookup"><span data-stu-id="20168-192">The [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet package is added to the project:</span></span>

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* <span data-ttu-id="20168-193">Générez le projet pour confirmer la restauration du fichier LibMan.</span><span class="sxs-lookup"><span data-stu-id="20168-193">Build the project to confirm LibMan file restoration occurs.</span></span> <span data-ttu-id="20168-194">Le `Microsoft.Web.LibraryManager.Build` package injecte une cible MSBuild qui exécute LibMan pendant l’opération de génération du projet.</span><span class="sxs-lookup"><span data-stu-id="20168-194">The `Microsoft.Web.LibraryManager.Build` package injects an MSBuild target that runs LibMan during the project's build operation.</span></span>
* <span data-ttu-id="20168-195">Examinez le flux de **génération** de la fenêtre **sortie** pour un journal d’activité LibMan :</span><span class="sxs-lookup"><span data-stu-id="20168-195">Review the **Build** feed of the **Output** window for a LibMan activity log:</span></span>

  ```console
  1>------ Build started: Project: LibManSample, Configuration: Debug Any CPU ------
  1>
  1>Restore operation started...
  1>Restoring library jquery@3.3.1...
  1>Restoring library bootstrap@4.1.3...
  1>
  1>2 libraries restored in 10.66 seconds
  1>LibManSample -> C:\LibManSample\bin\Debug\netcoreapp2.1\LibManSample.dll
  ========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
  ```

<span data-ttu-id="20168-196">Lorsque le comportement de Restore-on-Build est activé, le *libman.jsdans* le menu contextuel affiche une option **Disable Restore Client-Side Libraries on Build** .</span><span class="sxs-lookup"><span data-stu-id="20168-196">When the restore-on-build behavior is enabled, the *libman.json* context menu displays a **Disable Restore Client-Side Libraries on Build** option.</span></span> <span data-ttu-id="20168-197">La sélection de cette option supprime la `Microsoft.Web.LibraryManager.Build` Référence du package du fichier projet.</span><span class="sxs-lookup"><span data-stu-id="20168-197">Selecting this option removes the `Microsoft.Web.LibraryManager.Build` package reference from the project file.</span></span> <span data-ttu-id="20168-198">Par conséquent, les bibliothèques côté client ne sont plus restaurées sur chaque Build.</span><span class="sxs-lookup"><span data-stu-id="20168-198">Consequently, the client-side libraries are no longer restored on each build.</span></span>

<span data-ttu-id="20168-199">Quel que soit le paramètre Restore-on-Build, vous pouvez procéder à une restauration manuelle à tout moment à partir de l' *libman.jsdans* le menu contextuel.</span><span class="sxs-lookup"><span data-stu-id="20168-199">Regardless of the restore-on-build setting, you can manually restore at any time from the *libman.json* context menu.</span></span> <span data-ttu-id="20168-200">Pour plus d’informations, consultez [restaurer des fichiers manuellement](#restore-files-manually).</span><span class="sxs-lookup"><span data-stu-id="20168-200">For more information, see [Restore files manually](#restore-files-manually).</span></span>

### <a name="restore-files-manually"></a><span data-ttu-id="20168-201">Restaurer manuellement les fichiers</span><span class="sxs-lookup"><span data-stu-id="20168-201">Restore files manually</span></span>

<span data-ttu-id="20168-202">Pour restaurer manuellement les fichiers de bibliothèque :</span><span class="sxs-lookup"><span data-stu-id="20168-202">To manually restore library files:</span></span>

* <span data-ttu-id="20168-203">Pour tous les projets de la solution :</span><span class="sxs-lookup"><span data-stu-id="20168-203">For all projects in the solution:</span></span>
  * <span data-ttu-id="20168-204">Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le nom de la solution.</span><span class="sxs-lookup"><span data-stu-id="20168-204">Right-click the solution name in **Solution Explorer**.</span></span>
  * <span data-ttu-id="20168-205">Sélectionnez l’option **restaurer les bibliothèques de Client-Side** .</span><span class="sxs-lookup"><span data-stu-id="20168-205">Select the **Restore Client-Side Libraries** option.</span></span>
* <span data-ttu-id="20168-206">Pour un projet spécifique :</span><span class="sxs-lookup"><span data-stu-id="20168-206">For a specific project:</span></span>
  * <span data-ttu-id="20168-207">Cliquez avec le bouton droit sur le fichier *libman.js* dans **Explorateur de solutions**.</span><span class="sxs-lookup"><span data-stu-id="20168-207">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
  * <span data-ttu-id="20168-208">Sélectionnez l’option **restaurer les bibliothèques de Client-Side** .</span><span class="sxs-lookup"><span data-stu-id="20168-208">Select the **Restore Client-Side Libraries** option.</span></span>

<span data-ttu-id="20168-209">Pendant l’exécution de l’opération de restauration :</span><span class="sxs-lookup"><span data-stu-id="20168-209">While the restore operation is running:</span></span>

* <span data-ttu-id="20168-210">L’icône d’Centre d’état des tâches (TSC) dans la barre d’état de Visual Studio est animée et l' *opération de restauration est lancée*.</span><span class="sxs-lookup"><span data-stu-id="20168-210">The Task Status Center (TSC) icon on the Visual Studio status bar will be animated and will read *Restore operation started*.</span></span> <span data-ttu-id="20168-211">Cliquer sur l’icône ouvre une info-bulle qui répertorie les tâches d’arrière-plan connues.</span><span class="sxs-lookup"><span data-stu-id="20168-211">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="20168-212">Les messages sont envoyés à la barre d’État et au flux du **Gestionnaire de bibliothèques** de la fenêtre **sortie** .</span><span class="sxs-lookup"><span data-stu-id="20168-212">Messages will be sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="20168-213">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="20168-213">For example:</span></span>

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

## <a name="delete-library-files"></a><span data-ttu-id="20168-214">Supprimer les fichiers de bibliothèque</span><span class="sxs-lookup"><span data-stu-id="20168-214">Delete library files</span></span>

<span data-ttu-id="20168-215">Pour effectuer l’opération de *nettoyage* , qui supprime les fichiers de bibliothèque précédemment restaurés dans Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="20168-215">To perform the *clean* operation, which deletes library files previously restored in Visual Studio:</span></span>

* <span data-ttu-id="20168-216">Cliquez avec le bouton droit sur le fichier *libman.js* dans **Explorateur de solutions**.</span><span class="sxs-lookup"><span data-stu-id="20168-216">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="20168-217">Sélectionnez l’option **nettoyer les bibliothèques Client-Side** .</span><span class="sxs-lookup"><span data-stu-id="20168-217">Select the **Clean Client-Side Libraries** option.</span></span>

<span data-ttu-id="20168-218">Pour éviter la suppression accidentelle de fichiers non-bibliothèque, l’opération de nettoyage ne supprime pas les répertoires entiers.</span><span class="sxs-lookup"><span data-stu-id="20168-218">To prevent unintentional removal of non-library files, the clean operation doesn't delete whole directories.</span></span> <span data-ttu-id="20168-219">Elle supprime uniquement les fichiers qui ont été inclus dans la restauration précédente.</span><span class="sxs-lookup"><span data-stu-id="20168-219">It only removes files that were included in the previous restore.</span></span>

<span data-ttu-id="20168-220">Pendant que l’opération de nettoyage est en cours d’exécution :</span><span class="sxs-lookup"><span data-stu-id="20168-220">While the clean operation is running:</span></span>

* <span data-ttu-id="20168-221">L’icône TSC dans la barre d’état de Visual Studio sera animée et lira l' *opération des bibliothèques clientes démarrée*.</span><span class="sxs-lookup"><span data-stu-id="20168-221">The TSC icon on the Visual Studio status bar will be animated and will read *Client libraries operation started*.</span></span> <span data-ttu-id="20168-222">Cliquer sur l’icône ouvre une info-bulle qui répertorie les tâches d’arrière-plan connues.</span><span class="sxs-lookup"><span data-stu-id="20168-222">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="20168-223">Les messages sont envoyés à la barre d’État et au flux du **Gestionnaire de bibliothèques** de la fenêtre **sortie** .</span><span class="sxs-lookup"><span data-stu-id="20168-223">Messages are sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="20168-224">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="20168-224">For example:</span></span>

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

<span data-ttu-id="20168-225">L’opération de nettoyage supprime uniquement les fichiers du projet.</span><span class="sxs-lookup"><span data-stu-id="20168-225">The clean operation only deletes files from the project.</span></span> <span data-ttu-id="20168-226">Les fichiers de bibliothèque restent dans le cache pour une récupération plus rapide sur les futures opérations de restauration.</span><span class="sxs-lookup"><span data-stu-id="20168-226">Library files stay in the cache for faster retrieval on future restore operations.</span></span> <span data-ttu-id="20168-227">Pour gérer les fichiers de bibliothèque stockés dans le cache de l’ordinateur local, utilisez l' [interface CLI LibMan](xref:client-side/libman/libman-cli).</span><span class="sxs-lookup"><span data-stu-id="20168-227">To manage library files stored in the local machine's cache, use the [LibMan CLI](xref:client-side/libman/libman-cli).</span></span>

## <a name="uninstall-library-files"></a><span data-ttu-id="20168-228">Désinstaller les fichiers de bibliothèque</span><span class="sxs-lookup"><span data-stu-id="20168-228">Uninstall library files</span></span>

<span data-ttu-id="20168-229">Pour désinstaller les fichiers de bibliothèque :</span><span class="sxs-lookup"><span data-stu-id="20168-229">To uninstall library files:</span></span>

* <span data-ttu-id="20168-230">Ouvrez *libman.js*.</span><span class="sxs-lookup"><span data-stu-id="20168-230">Open *libman.json*.</span></span>
* <span data-ttu-id="20168-231">Placer le signe insertion à l’intérieur du `libraries` littéral d’objet correspondant.</span><span class="sxs-lookup"><span data-stu-id="20168-231">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="20168-232">Cliquez sur l’icône représentant une ampoule qui apparaît dans la marge de gauche, puis sélectionnez **désinstaller \<library_name> @ \<library_version>**:</span><span class="sxs-lookup"><span data-stu-id="20168-232">Click the light bulb icon that appears in the left margin, and select **Uninstall \<library_name>@\<library_version>**:</span></span>

  ![Option de menu contextuel de la bibliothèque Uninstall](_static/uninstall-menu-option.png)

<span data-ttu-id="20168-234">Vous pouvez également modifier et enregistrer manuellement le manifeste LibMan (*libman.jssur*).</span><span class="sxs-lookup"><span data-stu-id="20168-234">Alternatively, you can manually edit and save the LibMan manifest (*libman.json*).</span></span> <span data-ttu-id="20168-235">L' [opération de restauration](#restore-library-files) s’exécute lors de l’enregistrement du fichier.</span><span class="sxs-lookup"><span data-stu-id="20168-235">The [restore operation](#restore-library-files) runs when the file is saved.</span></span> <span data-ttu-id="20168-236">Les fichiers de bibliothèque qui ne sont plus définis dans *libman.jssur* sont supprimés du projet.</span><span class="sxs-lookup"><span data-stu-id="20168-236">Library files that are no longer defined in *libman.json* are removed from the project.</span></span>

## <a name="update-library-version"></a><span data-ttu-id="20168-237">Version de la bibliothèque de mises à jour</span><span class="sxs-lookup"><span data-stu-id="20168-237">Update library version</span></span>

<span data-ttu-id="20168-238">Pour rechercher une version de bibliothèque mise à jour :</span><span class="sxs-lookup"><span data-stu-id="20168-238">To check for an updated library version:</span></span>

* <span data-ttu-id="20168-239">Ouvrez *libman.js*.</span><span class="sxs-lookup"><span data-stu-id="20168-239">Open *libman.json*.</span></span>
* <span data-ttu-id="20168-240">Placer le signe insertion à l’intérieur du `libraries` littéral d’objet correspondant.</span><span class="sxs-lookup"><span data-stu-id="20168-240">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="20168-241">Cliquez sur l’icône représentant une ampoule qui apparaît dans la marge de gauche.</span><span class="sxs-lookup"><span data-stu-id="20168-241">Click the light bulb icon that appears in the left margin.</span></span> <span data-ttu-id="20168-242">Placez le curseur sur **vérifier les mises à jour**.</span><span class="sxs-lookup"><span data-stu-id="20168-242">Hover over **Check for updates**.</span></span>

<span data-ttu-id="20168-243">LibMan recherche une version de bibliothèque plus récente que la version installée.</span><span class="sxs-lookup"><span data-stu-id="20168-243">LibMan checks for a library version newer than the version installed.</span></span> <span data-ttu-id="20168-244">Les résultats suivants peuvent se produire :</span><span class="sxs-lookup"><span data-stu-id="20168-244">The following outcomes can occur:</span></span>

* <span data-ttu-id="20168-245">Un message **aucune mise à jour trouvée** s’affiche si la version la plus récente est déjà installée.</span><span class="sxs-lookup"><span data-stu-id="20168-245">A **No updates found** message is displayed if the latest version is already installed.</span></span>
* <span data-ttu-id="20168-246">La dernière version stable est affichée si elle n’est pas déjà installée.</span><span class="sxs-lookup"><span data-stu-id="20168-246">The latest stable version is displayed if not already installed.</span></span>

  ![Option de menu contextuel Rechercher les mises à jour](_static/update-menu-option.png)

* <span data-ttu-id="20168-248">Si une version préliminaire antérieure à la version installée est disponible, la version préliminaire est affichée.</span><span class="sxs-lookup"><span data-stu-id="20168-248">If a pre-release newer than the installed version is available, the pre-release is displayed.</span></span>

<span data-ttu-id="20168-249">Pour passer à une version antérieure de la bibliothèque, modifiez manuellement le *libman.jssur* le fichier.</span><span class="sxs-lookup"><span data-stu-id="20168-249">To downgrade to an older library version, manually edit the *libman.json* file.</span></span> <span data-ttu-id="20168-250">Une fois le fichier enregistré, l' [opération de restauration](#restore-library-files)LibMan :</span><span class="sxs-lookup"><span data-stu-id="20168-250">When the file is saved, the LibMan [restore operation](#restore-library-files):</span></span>

* <span data-ttu-id="20168-251">Supprime les fichiers redondants de la version précédente.</span><span class="sxs-lookup"><span data-stu-id="20168-251">Removes redundant files from the previous version.</span></span>
* <span data-ttu-id="20168-252">Ajoute des fichiers nouveaux et mis à jour à partir de la nouvelle version.</span><span class="sxs-lookup"><span data-stu-id="20168-252">Adds new and updated files from the new version.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="20168-253">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="20168-253">Additional resources</span></span>

* <xref:client-side/libman/libman-cli>
* [<span data-ttu-id="20168-254">Dépôt GitHub LibMan</span><span class="sxs-lookup"><span data-stu-id="20168-254">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
