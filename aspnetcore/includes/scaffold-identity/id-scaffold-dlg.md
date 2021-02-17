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
Exécutez le générateur de Identity modèles :

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le projet > **Ajouter**  >  **un nouvel élément de génération de modèles** automatique.
* Dans le volet gauche de la boîte de dialogue **Ajouter un nouvel élément de structure** , sélectionnez **Identity**  >  **Ajouter**.
* Dans la boîte de dialogue **ajouter Identity** , sélectionnez les options souhaitées.
  * Sélectionnez votre page de disposition existante, ou votre fichier de disposition sera remplacé par un balisage incorrect :
    * `~/Pages/Shared/_Layout.cshtml` pour les Razor pages
    * `~/Views/Shared/_Layout.cshtml` pour les projets MVC
    * Blazor Server les applications créées à partir du Blazor Server modèle ( `blazorserver` ) ne sont pas configurées pour les Razor pages ou MVC par défaut. Ne renseignez pas l’entrée page de disposition.
  * Sélectionnez le **+** bouton pour créer une **classe de contexte de données**. Acceptez la valeur par défaut ou spécifiez une classe (par exemple, `MyApplication.Data.ApplicationDbContext` ).
* Sélectionnez **Ajouter**.

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

Si vous n’avez pas déjà installé le générateur de modèles automatique ASP.NET Core, installez-le maintenant :

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Ajoutez les références de package NuGet nécessaires au fichier projet (*. csproj*). Exécutez les commandes suivantes dans le répertoire du projet :

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

Exécutez la commande suivante pour répertorier les options de l' Identity échafaudage :

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

Dans le dossier du projet, exécutez le générateur Identity de modèles automatique avec les options souhaitées. Par exemple, pour configurer l’identité avec l’interface utilisateur par défaut et le nombre minimal de fichiers, exécutez la commande suivante :

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
