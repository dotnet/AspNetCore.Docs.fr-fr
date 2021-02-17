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
ms.openlocfilehash: ca743cdb39423d833902c330f6f0682b600c9833
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551971"
---
::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

> [!WARNING]
> Un paramètre **catch-all** peut faire correspondre les itinéraires de manière incorrecte en raison d’un [bogue](https://github.com/dotnet/aspnetcore/issues/18677) dans le routage. Les applications affectées par ce bogue présentent les caractéristiques suivantes :
>
> * Un itinéraire de rattrapage, par exemple, `{**slug}"`
> * L’itinéraire catch-all ne parvient pas à faire correspondre les demandes auxquelles il doit correspondre.
> * La suppression d’autres itinéraires rend le fonctionnement de l’itinéraire de rattrapage.
>
> Consultez les bogues GitHub [18677](https://github.com/dotnet/aspnetcore/issues/18677) et [16579](https://github.com/dotnet/aspnetcore/issues/16579) pour obtenir des exemples de cas qui rencontrent ce bogue.
>
> Un correctif d’abonnement pour ce bogue est contenu dans [.net Core 3.1.301 SDK et versions ultérieures](https://dotnet.microsoft.com/download/dotnet-core/3.1). Le code suivant définit un commutateur interne qui corrige ce bogue :
>
>```csharp
>public static void Main(string[] args)
>{
>    AppContext.SetSwitch("Microsoft.AspNetCore.Routing.UseCorrectCatchAllBehavior", 
>                          true);
>    CreateHostBuilder(args).Build().Run();
>}
>// Remaining code removed for brevity.
>```

::: moniker-end