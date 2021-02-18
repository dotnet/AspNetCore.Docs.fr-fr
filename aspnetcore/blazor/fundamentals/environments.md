---
title: BlazorEnvironnements ASP.net Core
author: guardrex
description: Découvrez les environnements dans Blazor , notamment comment définir l’environnement d’une Blazor WebAssembly application.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/11/2020
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
uid: blazor/fundamentals/environments
ms.openlocfilehash: a5ead59e467da331b585e8daefb1d7d259c7edba
ms.sourcegitcommit: 422e8444b9f5cedc373be5efe8032822db54fcaf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101101046"
---
# <a name="aspnet-core-blazor-environments"></a>BlazorEnvironnements ASP.net Core

> [!NOTE]
> Cette rubrique s’applique à Blazor WebAssembly . Pour obtenir des conseils généraux sur ASP.NET Core configuration d’application, qui décrit les approches à utiliser pour les Blazor Server applications, consultez <xref:fundamentals/environments> .

Lors de l’exécution locale d’une application, l’environnement est par défaut développement. Lorsque l’application est publiée, l’environnement prend par défaut la valeur production.

L’application côté client Blazor ( *`Client`* ) d’une solution hébergée Blazor WebAssembly détermine l’environnement à partir de l' *`Server`* application de la solution via un intergiciel (middleware) qui communique l’environnement au navigateur. L' *`Server`* application ajoute un en-tête nommé `blazor-environment` avec l’environnement comme valeur de l’en-tête. L' *`Client`* application lit l’en-tête. L' *`Server`* application de la solution est une application ASP.net Core, de sorte que vous trouverez plus d’informations sur la configuration de l’environnement dans <xref:fundamentals/environments> .

Pour une Blazor WebAssembly application autonome exécutée localement, le serveur de développement ajoute l' `blazor-environment` en-tête pour spécifier l’environnement de développement. Pour spécifier l’environnement pour d’autres environnements d’hébergement, ajoutez l' `blazor-environment` en-tête.

Dans l’exemple suivant pour IIS, l’en-tête personnalisé ( `blazor-environment` ) est ajouté au `web.config` fichier publié. Le `web.config` fichier se trouve dans le `bin/Release/{TARGET FRAMEWORK}/publish` dossier, où l’espace réservé `{TARGET FRAMEWORK}` est la version cible du .NET Framework :

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>

    ...

    <httpProtocol>
      <customHeaders>
        <add name="blazor-environment" value="Staging" />
      </customHeaders>
    </httpProtocol>
  </system.webServer>
</configuration>
```

> [!NOTE]
> Pour utiliser un `web.config` fichier personnalisé pour IIS qui n’est pas remplacé lors de la publication de l’application dans le `publish` dossier, consultez <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig> .

Obtenez l’environnement de l’application dans un composant en injectant <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> et en lisant la <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> propriété.

`Pages/ReadEnvironment.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/environments/ReadEnvironment.razor?highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/environments/ReadEnvironment.razor?highlight=3,7)]

::: moniker-end

Au démarrage, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> expose le <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> via la <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> propriété, qui active la logique spécifique à l’environnement dans le code du générateur d’ordinateur hôte.

Dans `Program.Main` de `Program.cs` :

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

Les méthodes d’extension suivantes fournies par le biais <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions> de permettent de vérifier l’environnement actuel pour les noms d’environnement de développement, de production, intermédiaires et personnalisés :

* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsDevelopment%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsProduction%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsStaging%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsEnvironment%2A>

Dans `Program.Main` de `Program.cs` :

```csharp
if (builder.HostEnvironment.IsStaging())
{
    ...
};

if (builder.HostEnvironment.IsEnvironment("Custom"))
{
    ...
};
```

La <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> propriété peut être utilisée au démarrage lorsque le <xref:Microsoft.AspNetCore.Components.NavigationManager> service n’est pas disponible.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:fundamentals/environments>
