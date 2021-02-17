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
ms.openlocfilehash: 41d4fd2e746e08d32d9f666faab55acc56817be2
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551268"
---
<!-- Options common to Razor Pages and Controller -->
| <span data-ttu-id="43f5a-101">Option</span><span class="sxs-lookup"><span data-stu-id="43f5a-101">Option</span></span>               | <span data-ttu-id="43f5a-102">Description</span><span class="sxs-lookup"><span data-stu-id="43f5a-102">Description</span></span>|
| ----------------- | ------------ |
| <span data-ttu-id="43f5a-103">--model ou -m</span><span class="sxs-lookup"><span data-stu-id="43f5a-103">--model or -m</span></span>  | <span data-ttu-id="43f5a-104">Classe de modèle à utiliser.</span><span class="sxs-lookup"><span data-stu-id="43f5a-104">Model class to use.</span></span> |
| <span data-ttu-id="43f5a-105">--dataContext ou -dc</span><span class="sxs-lookup"><span data-stu-id="43f5a-105">--dataContext or -dc</span></span>  | <span data-ttu-id="43f5a-106">Classe `DbContext` à utiliser.</span><span class="sxs-lookup"><span data-stu-id="43f5a-106">The `DbContext` class to use.</span></span> |
| <span data-ttu-id="43f5a-107">--bootstrapVersion ou -b</span><span class="sxs-lookup"><span data-stu-id="43f5a-107">--bootstrapVersion or -b</span></span>  | <span data-ttu-id="43f5a-108">Spécifie la version de démarrage.</span><span class="sxs-lookup"><span data-stu-id="43f5a-108">Specifies the bootstrap version.</span></span> <span data-ttu-id="43f5a-109">Les valeurs valides sont `3` ou `4`.</span><span class="sxs-lookup"><span data-stu-id="43f5a-109">Valid values are `3` or `4`.</span></span> <span data-ttu-id="43f5a-110">La valeur par défaut est `4`.</span><span class="sxs-lookup"><span data-stu-id="43f5a-110">Default is `4`.</span></span> <span data-ttu-id="43f5a-111">S’il est nécessaire mais absent, un répertoire *wwwroot* est créé, qui comprend les fichiers de démarrage de la version spécifiée.</span><span class="sxs-lookup"><span data-stu-id="43f5a-111">If needed and not present, a *wwwroot* directory is created that includes the bootstrap files of the specified version.</span></span> |
| <span data-ttu-id="43f5a-112">--referenceScriptLibraries ou -scripts</span><span class="sxs-lookup"><span data-stu-id="43f5a-112">--referenceScriptLibraries or -scripts</span></span> |  <span data-ttu-id="43f5a-113">Référence les bibliothèques de scripts dans les vues générées.</span><span class="sxs-lookup"><span data-stu-id="43f5a-113">Reference script libraries in the generated views.</span></span> <span data-ttu-id="43f5a-114">Ajoute `_ValidationScriptsPartial` aux pages Modifier et Créer.</span><span class="sxs-lookup"><span data-stu-id="43f5a-114">Adds `_ValidationScriptsPartial` to Edit and Create pages.</span></span> |
| <span data-ttu-id="43f5a-115">--layout ou -l</span><span class="sxs-lookup"><span data-stu-id="43f5a-115">--layout or -l</span></span> | <span data-ttu-id="43f5a-116">Page de disposition personnalisée à utiliser.</span><span class="sxs-lookup"><span data-stu-id="43f5a-116">Custom Layout page to use.</span></span> |
| <span data-ttu-id="43f5a-117">--useDefaultLayout ou -udl</span><span class="sxs-lookup"><span data-stu-id="43f5a-117">--useDefaultLayout or -udl</span></span> | <span data-ttu-id="43f5a-118">Utiliser la disposition par défaut pour les vues.</span><span class="sxs-lookup"><span data-stu-id="43f5a-118">Use the default layout for the views.</span></span> |
| <span data-ttu-id="43f5a-119">--force ou -f</span><span class="sxs-lookup"><span data-stu-id="43f5a-119">--force or -f</span></span> | <span data-ttu-id="43f5a-120">Remplacer les fichiers existants.</span><span class="sxs-lookup"><span data-stu-id="43f5a-120">Overwrite existing files.</span></span> |
| <span data-ttu-id="43f5a-121">--relativeFolderPath ou -outDir</span><span class="sxs-lookup"><span data-stu-id="43f5a-121">--relativeFolderPath or -outDir</span></span> | <span data-ttu-id="43f5a-122">Chemin relatif du dossier de sortie du projet où les fichiers sont générés.</span><span class="sxs-lookup"><span data-stu-id="43f5a-122">The relative output folder path from project where the file are generated.</span></span> <span data-ttu-id="43f5a-123">S’il n’est pas spécifié, les fichiers sont générés dans le dossier du projet.</span><span class="sxs-lookup"><span data-stu-id="43f5a-123">If not specified, files are generated in the project folder.</span></span> |