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
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a>Utiliser LibMan avec ASP.NET Core dans Visual Studio

Par [Scott Addie](https://twitter.com/Scott_Addie)

Visual Studio offre une prise en charge intégrée de [LibMan](xref:client-side/libman/index) dans ASP.net Core projets, notamment :

* Prise en charge de la configuration et de l’exécution des opérations de restauration LibMan sur Build.
* Éléments de menu pour déclencher des opérations de restauration et de nettoyage LibMan.
* Boîte de dialogue Rechercher pour rechercher des bibliothèques et ajouter les fichiers à un projet.
* La prise en charge de la modification de *libman.jssur* &mdash; le fichier manifeste LibMan.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/client-side/libman/samples/) [(procédure de téléchargement)](xref:index#how-to-download-a-sample)

## <a name="prerequisites"></a>Prérequis

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) avec la charge de travail de **développement Web et ASP.net**

## <a name="add-library-files"></a>Ajouter des fichiers de bibliothèque

Les fichiers de bibliothèque peuvent être ajoutés à un projet ASP.NET Core de deux manières différentes :

1. [Utilisez la boîte de dialogue Ajouter une bibliothèque de Client-Side](#use-the-add-client-side-library-dialog)
1. [Configurer manuellement les entrées du fichier manifeste LibMan](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a>Utilisez la boîte de dialogue Ajouter une bibliothèque de Client-Side

Procédez comme suit pour installer une bibliothèque côté client :

* Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le dossier du projet dans lequel les fichiers doivent être ajoutés. Sélectionnez **Ajouter**  >  **une bibliothèque côté client**. La boîte de dialogue **Ajouter une bibliothèque de Client-Side** s’affiche :

  ![Boîte de dialogue Ajouter une bibliothèque de Client-Side](_static/add-library-dialog.png)

* Sélectionnez le fournisseur de bibliothèque dans la liste déroulante **fournisseur** . CDNJS est le fournisseur par défaut.
* Tapez le nom de la bibliothèque à extraire dans la zone de texte **bibliothèque** . IntelliSense fournit une liste de bibliothèques qui commencent par le texte fourni.
* Sélectionnez la bibliothèque dans la liste IntelliSense. Notez que le nom de la bibliothèque est suivi du `@` symbole et de la dernière version stable connue du fournisseur sélectionné.
* Choisir les fichiers à inclure :
  * Sélectionnez la case d’option **inclure tous les fichiers de bibliothèque** pour inclure tous les fichiers de la bibliothèque.
  * Sélectionnez la case d’option **choisir des fichiers spécifiques** pour inclure un sous-ensemble des fichiers de la bibliothèque. Lorsque la case d’option est sélectionnée, l’arborescence du sélecteur de fichiers est activée. Activez les cases à cocher à gauche des noms de fichiers à télécharger.
* Spécifiez le dossier du projet pour le stockage des fichiers dans la zone de texte **emplacement cible** . En guise de recommandation, stockez chaque bibliothèque dans un dossier distinct.

  Le dossier d' **emplacement cible** suggéré est basé sur l’emplacement à partir duquel la boîte de dialogue a été lancée :

  * S’il est lancé à partir de la racine du projet :
    * *wwwroot/lib* est utilisé si *wwwroot* existe.
    * *lib* est utilisé si *wwwroot* n’existe pas.
  * S’il est lancé à partir d’un dossier de projet, le nom de dossier correspondant est utilisé.

  La suggestion de dossier est suivie du suffixe du nom de la bibliothèque. Le tableau suivant illustre les suggestions de dossiers lors de l’installation de jQuery dans un Razor projet pages.
  
  |Emplacement de lancement                           |Dossier suggéré      |
  |------------------------------------------|----------------------|
  |racine du projet (si *wwwroot* existe)        |*wwwroot/lib/jQuery/* |
  |racine du projet (si *wwwroot* n’existe pas) |*lib/jQuery/*         |
  |Dossier *pages* dans Project                 |*Pages/jQuery/*       |

* Cliquez sur le bouton **installer** pour télécharger les fichiers, conformément à la configuration de *libman.jssur*.
* Consultez le flux du **Gestionnaire de bibliothèques** de la fenêtre **sortie** pour plus d’informations sur l’installation. Par exemple :

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

### <a name="manually-configure-libman-manifest-file-entries"></a>Configurer manuellement les entrées du fichier manifeste LibMan

Toutes les opérations LibMan dans Visual Studio sont basées sur le contenu du manifeste LibMan de la racine du projet (*libman.jssur*). Vous pouvez modifier manuellement *libman.jssur* pour configurer des fichiers de bibliothèque pour le projet. Visual Studio restaure tous les fichiers de bibliothèque une fois que *libman.js* est enregistré.

Pour ouvrir *libman.js* pour modification, les options suivantes sont disponibles :

* Double-cliquez sur le fichier *libman.js* dans **Explorateur de solutions**.
* Dans **Explorateur de solutions** , cliquez avec le bouton droit sur le projet, puis sélectionnez **gérer les bibliothèques de Client-Side**. **&#8224;**
* Sélectionnez **gérer les bibliothèques de Client-Side** dans le menu **projet** de Visual Studio. **&#8224;**

**&#8224;** Si le *libman.jssur* le fichier n’existe pas encore dans la racine du projet, il sera créé avec le contenu du modèle d’élément par défaut.

Visual Studio offre une prise en charge enrichie de l’édition JSON, comme la colorisation, la mise en forme, IntelliSense et la validation de schéma. Le schéma JSON du manifeste LibMan se trouve à l’adresse [https://json.schemastore.org/libman](https://json.schemastore.org/libman) .

Avec le fichier manifeste suivant, LibMan récupère les fichiers selon la configuration définie dans la `libraries` propriété. Une explication des littéraux d’objet définis dans `libraries` suit :

* Un sous-ensemble de [jQuery](https://jquery.com/) version 3.3.1 est récupéré à partir du fournisseur CDNJS. Le sous-ensemble est défini dans la `files` propriété &mdash; *jquery.min.js*, *jquery.js* et *jQuery. min. map*. Les fichiers sont placés dans le dossier *wwwroot/lib/jQuery* du projet.
* L’intégralité de la version de [bootstrap](https://getbootstrap.com/) 4.1.3 est extraite et placée dans un dossier *wwwroot/lib/bootstrap* . La propriété du littéral d’objet `provider` remplace la valeur de la `defaultProvider` propriété. LibMan récupère les fichiers de démarrage à partir du fournisseur unpkg.
* Un sous-ensemble de [Lodash](https://lodash.com/) a été approuvé par un corps fédérateur au sein de l’organisation. Les fichiers *lodash.js* et *lodash.min.js* sont récupérés à partir du système de fichiers local à l’adresse *C : \\ temp \\ lodash \\*. Les fichiers sont copiés dans le dossier *wwwroot/lib/lodash* du projet.

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> LibMan ne prend en charge qu’une seule version de chaque bibliothèque de chaque fournisseur. Le *libman.jssur* le fichier échoue la validation du schéma s’il contient deux bibliothèques avec le même nom de bibliothèque pour un fournisseur donné.

## <a name="restore-library-files"></a>Restaurer les fichiers de bibliothèque

Pour restaurer des fichiers de bibliothèque à partir de Visual Studio, il doit y avoir un *libman.jsvalide sur* le fichier à la racine du projet. Les fichiers restaurés sont placés dans le projet à l’emplacement spécifié pour chaque bibliothèque.

Les fichiers de bibliothèque peuvent être restaurés dans un projet ASP.NET Core de deux manières :

1. [Restaurer des fichiers pendant la génération](#restore-files-during-build)
1. [Restaurer manuellement les fichiers](#restore-files-manually)

### <a name="restore-files-during-build"></a>Restaurer des fichiers pendant la génération

LibMan peut restaurer les fichiers de bibliothèque définis dans le cadre du processus de génération. Par défaut, le comportement *de Restore-on-Build* est désactivé.

Pour activer et tester le comportement de Restore-on-Build :

* Cliquez avec le bouton droit sur *libman.js* dans **Explorateur de solutions** puis sélectionnez **activer la restauration des bibliothèques de Client-Side sur la build** dans le menu contextuel.
* Cliquez sur le bouton **Oui** lorsque vous êtes invité à installer un package NuGet. Le package NuGet [Microsoft. Web. librarymanager. Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) est ajouté au projet :

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* Générez le projet pour confirmer la restauration du fichier LibMan. Le `Microsoft.Web.LibraryManager.Build` package injecte une cible MSBuild qui exécute LibMan pendant l’opération de génération du projet.
* Examinez le flux de **génération** de la fenêtre **sortie** pour un journal d’activité LibMan :

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

Lorsque le comportement de Restore-on-Build est activé, le *libman.jsdans* le menu contextuel affiche une option **Disable Restore Client-Side Libraries on Build** . La sélection de cette option supprime la `Microsoft.Web.LibraryManager.Build` Référence du package du fichier projet. Par conséquent, les bibliothèques côté client ne sont plus restaurées sur chaque Build.

Quel que soit le paramètre Restore-on-Build, vous pouvez procéder à une restauration manuelle à tout moment à partir de l' *libman.jsdans* le menu contextuel. Pour plus d’informations, consultez [restaurer des fichiers manuellement](#restore-files-manually).

### <a name="restore-files-manually"></a>Restaurer manuellement les fichiers

Pour restaurer manuellement les fichiers de bibliothèque :

* Pour tous les projets de la solution :
  * Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le nom de la solution.
  * Sélectionnez l’option **restaurer les bibliothèques de Client-Side** .
* Pour un projet spécifique :
  * Cliquez avec le bouton droit sur le fichier *libman.js* dans **Explorateur de solutions**.
  * Sélectionnez l’option **restaurer les bibliothèques de Client-Side** .

Pendant l’exécution de l’opération de restauration :

* L’icône d’Centre d’état des tâches (TSC) dans la barre d’état de Visual Studio est animée et l' *opération de restauration est lancée*. Cliquer sur l’icône ouvre une info-bulle qui répertorie les tâches d’arrière-plan connues.
* Les messages sont envoyés à la barre d’État et au flux du **Gestionnaire de bibliothèques** de la fenêtre **sortie** . Par exemple :

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

## <a name="delete-library-files"></a>Supprimer les fichiers de bibliothèque

Pour effectuer l’opération de *nettoyage* , qui supprime les fichiers de bibliothèque précédemment restaurés dans Visual Studio :

* Cliquez avec le bouton droit sur le fichier *libman.js* dans **Explorateur de solutions**.
* Sélectionnez l’option **nettoyer les bibliothèques Client-Side** .

Pour éviter la suppression accidentelle de fichiers non-bibliothèque, l’opération de nettoyage ne supprime pas les répertoires entiers. Elle supprime uniquement les fichiers qui ont été inclus dans la restauration précédente.

Pendant que l’opération de nettoyage est en cours d’exécution :

* L’icône TSC dans la barre d’état de Visual Studio sera animée et lira l' *opération des bibliothèques clientes démarrée*. Cliquer sur l’icône ouvre une info-bulle qui répertorie les tâches d’arrière-plan connues.
* Les messages sont envoyés à la barre d’État et au flux du **Gestionnaire de bibliothèques** de la fenêtre **sortie** . Par exemple :

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

L’opération de nettoyage supprime uniquement les fichiers du projet. Les fichiers de bibliothèque restent dans le cache pour une récupération plus rapide sur les futures opérations de restauration. Pour gérer les fichiers de bibliothèque stockés dans le cache de l’ordinateur local, utilisez l' [interface CLI LibMan](xref:client-side/libman/libman-cli).

## <a name="uninstall-library-files"></a>Désinstaller les fichiers de bibliothèque

Pour désinstaller les fichiers de bibliothèque :

* Ouvrez *libman.js*.
* Placer le signe insertion à l’intérieur du `libraries` littéral d’objet correspondant.
* Cliquez sur l’icône représentant une ampoule qui apparaît dans la marge de gauche, puis sélectionnez **désinstaller \<library_name> @ \<library_version>**:

  ![Option de menu contextuel de la bibliothèque Uninstall](_static/uninstall-menu-option.png)

Vous pouvez également modifier et enregistrer manuellement le manifeste LibMan (*libman.jssur*). L' [opération de restauration](#restore-library-files) s’exécute lors de l’enregistrement du fichier. Les fichiers de bibliothèque qui ne sont plus définis dans *libman.jssur* sont supprimés du projet.

## <a name="update-library-version"></a>Version de la bibliothèque de mises à jour

Pour rechercher une version de bibliothèque mise à jour :

* Ouvrez *libman.js*.
* Placer le signe insertion à l’intérieur du `libraries` littéral d’objet correspondant.
* Cliquez sur l’icône représentant une ampoule qui apparaît dans la marge de gauche. Placez le curseur sur **vérifier les mises à jour**.

LibMan recherche une version de bibliothèque plus récente que la version installée. Les résultats suivants peuvent se produire :

* Un message **aucune mise à jour trouvée** s’affiche si la version la plus récente est déjà installée.
* La dernière version stable est affichée si elle n’est pas déjà installée.

  ![Option de menu contextuel Rechercher les mises à jour](_static/update-menu-option.png)

* Si une version préliminaire antérieure à la version installée est disponible, la version préliminaire est affichée.

Pour passer à une version antérieure de la bibliothèque, modifiez manuellement le *libman.jssur* le fichier. Une fois le fichier enregistré, l' [opération de restauration](#restore-library-files)LibMan :

* Supprime les fichiers redondants de la version précédente.
* Ajoute des fichiers nouveaux et mis à jour à partir de la nouvelle version.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:client-side/libman/libman-cli>
* [Dépôt GitHub LibMan](https://github.com/aspnet/LibraryManager)
