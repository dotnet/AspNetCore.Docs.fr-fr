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
ms.openlocfilehash: 9c977e5407f9a3dc562ef0fb1127fefaa0dc5fc2
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552370"
---
<a name="ddav"></a>
### <a name="disable-default-account-verification"></a>Désactiver la vérification du compte par défaut

Avec les modèles par défaut, l’utilisateur est redirigé vers le `Account.RegisterConfirmation` où il peut sélectionner un lien pour que le compte soit confirmé. La valeur par défaut `Account.RegisterConfirmation` est utilisée ***uniquement*** pour les tests, la vérification automatique de compte doit être désactivée dans une application de production.

Pour exiger un compte confirmé et empêcher la connexion immédiate lors de l’inscription, définissez `DisplayConfirmAccountLink = false` dans */Areas/ Identity /pages/Account/RegisterConfirmation.cshtml.cs*:

[!code-csharp[](~/security/authentication/identity/sample/WebApp3/Areas/Identity/Pages/Account/RegisterConfirmation.cshtml.cs?name=snippet&highlight=34)]