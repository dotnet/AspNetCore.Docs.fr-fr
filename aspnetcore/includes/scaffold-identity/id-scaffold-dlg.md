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
ms.openlocfilehash: ed6de823b8b860c7d27e932e9ece40d8b43b0893
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552482"
---
<span data-ttu-id="e6f99-101">Exécutez le générateur de Identity modèles :</span><span class="sxs-lookup"><span data-stu-id="e6f99-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e6f99-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6f99-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e6f99-103">Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le projet > **Ajouter**  >  **un nouvel élément de génération de modèles** automatique.</span><span class="sxs-lookup"><span data-stu-id="e6f99-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="e6f99-104">Dans le volet gauche de la boîte de dialogue **Ajouter un nouvel élément de structure** , sélectionnez **Identity**  >  **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="e6f99-104">From the left pane of the **Add New Scaffolded Item** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="e6f99-105">Dans la boîte de dialogue **ajouter Identity** , sélectionnez les options souhaitées.</span><span class="sxs-lookup"><span data-stu-id="e6f99-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="e6f99-106">Sélectionnez votre page de disposition existante, ou votre fichier de disposition sera remplacé par un balisage incorrect :</span><span class="sxs-lookup"><span data-stu-id="e6f99-106">Select your existing layout page, or your layout file will be overwritten with incorrect markup:</span></span>
    * <span data-ttu-id="e6f99-107">`~/Pages/Shared/_Layout.cshtml` pour les Razor pages</span><span class="sxs-lookup"><span data-stu-id="e6f99-107">`~/Pages/Shared/_Layout.cshtml` for Razor Pages</span></span>
    * <span data-ttu-id="e6f99-108">`~/Views/Shared/_Layout.cshtml` pour les projets MVC</span><span class="sxs-lookup"><span data-stu-id="e6f99-108">`~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
    * <span data-ttu-id="e6f99-109">Blazor Server les applications créées à partir du Blazor Server modèle ( `blazorserver` ) ne sont pas configurées pour les Razor pages ou MVC par défaut.</span><span class="sxs-lookup"><span data-stu-id="e6f99-109">Blazor Server apps created from the Blazor Server template (`blazorserver`) aren't configured for Razor Pages or MVC by default.</span></span> <span data-ttu-id="e6f99-110">Ne renseignez pas l’entrée page de disposition.</span><span class="sxs-lookup"><span data-stu-id="e6f99-110">Leave the layout page entry blank.</span></span>
  * <span data-ttu-id="e6f99-111">Sélectionnez le **+** bouton pour créer une **classe de contexte de données**.</span><span class="sxs-lookup"><span data-stu-id="e6f99-111">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="e6f99-112">Acceptez la valeur par défaut ou spécifiez une classe (par exemple, `MyApplication.Data.ApplicationDbContext` ).</span><span class="sxs-lookup"><span data-stu-id="e6f99-112">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
* <span data-ttu-id="e6f99-113">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="e6f99-113">Select **Add**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="e6f99-114">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="e6f99-114">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="e6f99-115">Si vous n’avez pas déjà installé le générateur de modèles automatique ASP.NET Core, installez-le maintenant :</span><span class="sxs-lookup"><span data-stu-id="e6f99-115">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="e6f99-116">Ajoutez les références de package NuGet nécessaires au fichier projet (*. csproj*).</span><span class="sxs-lookup"><span data-stu-id="e6f99-116">Add required NuGet package references to the project file (*.csproj*).</span></span> <span data-ttu-id="e6f99-117">Exécutez les commandes suivantes dans le répertoire du projet :</span><span class="sxs-lookup"><span data-stu-id="e6f99-117">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="e6f99-118">Exécutez la commande suivante pour répertorier les options de l' Identity échafaudage :</span><span class="sxs-lookup"><span data-stu-id="e6f99-118">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="e6f99-119">Dans le dossier du projet, exécutez le générateur Identity de modèles automatique avec les options souhaitées.</span><span class="sxs-lookup"><span data-stu-id="e6f99-119">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="e6f99-120">Par exemple, pour configurer l’identité avec l’interface utilisateur par défaut et le nombre minimal de fichiers, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="e6f99-120">For example, to setup identity with the default UI and the minimum number of files, run the following command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
