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
ms.openlocfilehash: ff0502f68546213d76fe33f45574b5d8654b522b
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551659"
---
### <a name="view-the-identity-database"></a>Afficher la Identity base de données

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio) 

* Dans le menu **affichage** , sélectionnez **Explorateur d’objets SQL Server** (SSOX).
* Accédez à (base de données locale **) MSSQLLocalDB (SQL Server 13)**. Cliquez avec le bouton droit sur **dbo. AspNetUsers**  >  **afficher les données**:

![Menu contextuel sur la table AspNetUsers dans Explorateur d’objets SQL Server](~/security/authentication/accconfirm/_static/ssox.png)

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

Vous pouvez télécharger de nombreux outils tiers pour gérer et afficher une base de données SQLite, par exemple, le [navigateur DB pour SQLite](https://sqlitebrowser.org/).

---