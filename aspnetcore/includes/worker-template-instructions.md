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
ms.openlocfilehash: 34e12afd6bc7015e4609fbdf773259ed5545809b
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552320"
---
# <a name="visual-studio"></a>[<span data-ttu-id="dbcc6-101">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dbcc6-101">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="dbcc6-102">Créez un projet.</span><span class="sxs-lookup"><span data-stu-id="dbcc6-102">Create a new project.</span></span>
1. <span data-ttu-id="dbcc6-103">Sélectionnez **Worker service**.</span><span class="sxs-lookup"><span data-stu-id="dbcc6-103">Select **Worker Service**.</span></span> <span data-ttu-id="dbcc6-104">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="dbcc6-104">Select **Next**.</span></span>
1. <span data-ttu-id="dbcc6-105">Indiquez un nom de projet dans le champ **Nom du projet**, ou acceptez le nom de projet par défaut.</span><span class="sxs-lookup"><span data-stu-id="dbcc6-105">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="dbcc6-106">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="dbcc6-106">Select **Create**.</span></span>
1. <span data-ttu-id="dbcc6-107">Dans la boîte de dialogue **créer un service de travail** , sélectionnez **créer**.</span><span class="sxs-lookup"><span data-stu-id="dbcc6-107">In the **Create a new Worker service** dialog, select **Create**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dbcc6-108">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="dbcc6-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="dbcc6-109">Créez un projet.</span><span class="sxs-lookup"><span data-stu-id="dbcc6-109">Create a new project.</span></span>
1. <span data-ttu-id="dbcc6-110">Sélectionnez **application** sous **.net Core** dans la barre latérale.</span><span class="sxs-lookup"><span data-stu-id="dbcc6-110">Select **App** under **.NET Core** in the sidebar.</span></span>
1. <span data-ttu-id="dbcc6-111">Sélectionnez **Worker** sous **ASP.net Core**.</span><span class="sxs-lookup"><span data-stu-id="dbcc6-111">Select **Worker** under **ASP.NET Core**.</span></span> <span data-ttu-id="dbcc6-112">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="dbcc6-112">Select **Next**.</span></span>
1. <span data-ttu-id="dbcc6-113">Sélectionnez **.net Core 3,0** ou une version ultérieure pour le **Framework cible**.</span><span class="sxs-lookup"><span data-stu-id="dbcc6-113">Select **.NET Core 3.0** or later for the **Target Framework**.</span></span> <span data-ttu-id="dbcc6-114">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="dbcc6-114">Select **Next**.</span></span>
1. <span data-ttu-id="dbcc6-115">Indiquez un nom dans le champ **nom du projet** .</span><span class="sxs-lookup"><span data-stu-id="dbcc6-115">Provide a name in the **Project Name** field.</span></span> <span data-ttu-id="dbcc6-116">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="dbcc6-116">Select **Create**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="dbcc6-117">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="dbcc6-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="dbcc6-118">Utilisez le modèle Service Worker (`worker`) avec la commande [dotnet new](/dotnet/core/tools/dotnet-new) à partir d’un interpréteur de commandes.</span><span class="sxs-lookup"><span data-stu-id="dbcc6-118">Use the Worker Service (`worker`) template with the [dotnet new](/dotnet/core/tools/dotnet-new) command from a command shell.</span></span> <span data-ttu-id="dbcc6-119">Dans l’exemple suivant, une application Service Worker est créée et se nomme `ContosoWorker`.</span><span class="sxs-lookup"><span data-stu-id="dbcc6-119">In the following example, a Worker Service app is created named `ContosoWorker`.</span></span> <span data-ttu-id="dbcc6-120">Un dossier pour l’application `ContosoWorker` est créé automatiquement durant l’exécution de la commande.</span><span class="sxs-lookup"><span data-stu-id="dbcc6-120">A folder for the `ContosoWorker` app is created automatically when the command is executed.</span></span>

```dotnetcli
dotnet new worker -o ContosoWorker
```

---
