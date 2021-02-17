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
* <span data-ttu-id="50663-101">Approuvez le certificat de développement HTTPS en exécutant la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="50663-101">Trust the HTTPS development certificate by running the following command:</span></span>

  ```dotnetcli
  dotnet dev-certs https --trust
  ```
  
  <span data-ttu-id="50663-102">La commande précédente ne fonctionne pas dans Linux.</span><span class="sxs-lookup"><span data-stu-id="50663-102">The preceding command doesn't work on Linux.</span></span> <span data-ttu-id="50663-103">Consultez la documentation de votre distribution Linux concernant l’approbation des certificats.</span><span class="sxs-lookup"><span data-stu-id="50663-103">See your Linux distribution's documentation for trusting a certificate.</span></span>

  <span data-ttu-id="50663-104">La commande précédente affiche la boîte de dialogue suivante :</span><span class="sxs-lookup"><span data-stu-id="50663-104">The preceding command displays the following dialog:</span></span>

  ![Boîte de dialogue Avertissement de sécurité](~/getting-started/_static/cert.png)

* <span data-ttu-id="50663-106">Sélectionnez **Oui** si vous acceptez d’approuver le certificat de développement.</span><span class="sxs-lookup"><span data-stu-id="50663-106">Select **Yes** if you agree to trust the development certificate.</span></span>

  <span data-ttu-id="50663-107">Pour plus d’informations, consultez [Approuver le certificat de développement HTTPS ASP.NET Core](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span><span class="sxs-lookup"><span data-stu-id="50663-107">See [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) for more information.</span></span>
  
[!INCLUDE[trust FF](~/includes/trust-ff.md)]