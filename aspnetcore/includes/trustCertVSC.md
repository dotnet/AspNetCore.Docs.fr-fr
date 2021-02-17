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
ms.openlocfilehash: df4a9a7db8097ea765b2d0f404305b771f994ddf
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552403"
---
* Approuvez le certificat de développement HTTPS en exécutant la commande suivante :

  ```dotnetcli
  dotnet dev-certs https --trust
  ```
  
  La commande précédente ne fonctionne pas dans Linux. Consultez la documentation de votre distribution Linux concernant l’approbation des certificats.

  La commande précédente affiche la boîte de dialogue suivante :

  ![Boîte de dialogue Avertissement de sécurité](~/getting-started/_static/cert.png)

* Sélectionnez **Oui** si vous acceptez d’approuver le certificat de développement.

  Pour plus d’informations, consultez [Approuver le certificat de développement HTTPS ASP.NET Core](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).
  
[!INCLUDE[trust FF](~/includes/trust-ff.md)]