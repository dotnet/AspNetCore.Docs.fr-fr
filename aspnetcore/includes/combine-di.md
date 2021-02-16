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
ms.openlocfilehash: 08d212b48c3f97531bea34b11d5b8c9a9069ae34
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536287"
---
<a name="csc"></a>

Considérez la `ConfigureServices` méthode suivante, qui inscrit les services et configure les options :

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup2.cs?name=snippet)]

Les groupes d’inscriptions associés peuvent être déplacés vers une méthode d’extension pour inscrire les services. Par exemple, les services de configuration sont ajoutés à la classe suivante :

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/MyConfigServiceCollectionExtensions.cs)]

Les services restants sont inscrits dans une classe similaire. La `ConfigureServices` méthode suivante utilise les nouvelles méthodes d’extension pour inscrire les services :

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup4.cs?name=snippet)]

**_Remarque :_** Chaque `services.Add{GROUP_NAME}` méthode d’extension ajoute et configure éventuellement des services. Par exemple, <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> ajoute les services MVC dont les vues ont besoin et <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> ajoute les pages de services Razor requis. Nous vous recommandons d’appliquer cette Convention d’affectation de noms aux applications. Placez les méthodes d’extension dans l’espace de noms <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> pour encapsuler des groupes d’inscriptions de service.
