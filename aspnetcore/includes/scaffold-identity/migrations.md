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
<span data-ttu-id="14413-101">Le Identity Code de base de données généré requiert des [migrations de Entity Framework Core](/ef/core/managing-schemas/migrations/).</span><span class="sxs-lookup"><span data-stu-id="14413-101">The generated Identity database code requires [Entity Framework Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="14413-102">Créer une migration et mettre à jour la base de données.</span><span class="sxs-lookup"><span data-stu-id="14413-102">Create a migration and update the database.</span></span> <span data-ttu-id="14413-103">Par exemple, exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="14413-103">For example, run the following commands:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="14413-104">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="14413-104">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="14413-105">Dans la console du **Gestionnaire de package** Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="14413-105">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="14413-106">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="14413-106">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

<span data-ttu-id="14413-107">Le Identity paramètre de nom « Create Schema » pour la `Add-Migration` commande est arbitraire.</span><span class="sxs-lookup"><span data-stu-id="14413-107">The "CreateIdentitySchema" name parameter for the `Add-Migration` command is arbitrary.</span></span> <span data-ttu-id="14413-108">`"CreateIdentitySchema"` décrit la migration.</span><span class="sxs-lookup"><span data-stu-id="14413-108">`"CreateIdentitySchema"` describes the migration.</span></span>
