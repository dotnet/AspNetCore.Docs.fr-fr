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
# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Créez un projet.
1. Sélectionnez **Worker service**. Sélectionnez **Suivant**.
1. Indiquez un nom de projet dans le champ **Nom du projet**, ou acceptez le nom de projet par défaut. Sélectionnez **Create** (Créer).
1. Dans la boîte de dialogue **créer un service de travail** , sélectionnez **créer**.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

1. Créez un projet.
1. Sélectionnez **application** sous **.net Core** dans la barre latérale.
1. Sélectionnez **Worker** sous **ASP.net Core**. Sélectionnez **Suivant**.
1. Sélectionnez **.net Core 3,0** ou une version ultérieure pour le **Framework cible**. Sélectionnez **Suivant**.
1. Indiquez un nom dans le champ **nom du projet** . Sélectionnez **Create** (Créer).

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

Utilisez le modèle Service Worker (`worker`) avec la commande [dotnet new](/dotnet/core/tools/dotnet-new) à partir d’un interpréteur de commandes. Dans l’exemple suivant, une application Service Worker est créée et se nomme `ContosoWorker`. Un dossier pour l’application `ContosoWorker` est créé automatiquement durant l’exécution de la commande.

```dotnetcli
dotnet new worker -o ContosoWorker
```

---
