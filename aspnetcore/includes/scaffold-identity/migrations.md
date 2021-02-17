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
ms.openlocfilehash: 1700adafb58cad57ea1becbf53cad45edd047962
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552818"
---
Le Identity Code de base de données généré requiert des [migrations de Entity Framework Core](/ef/core/managing-schemas/migrations/). Créer une migration et mettre à jour la base de données. Par exemple, exécutez les commandes suivantes :

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Dans la console du **Gestionnaire de package** Visual Studio :

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

Le Identity paramètre de nom « Create Schema » pour la `Add-Migration` commande est arbitraire. `"CreateIdentitySchema"` décrit la migration.
