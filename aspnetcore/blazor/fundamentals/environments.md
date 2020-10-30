---
title: BlazorEnvironnements ASP.net Core
author: guardrex
description: Découvrez les environnements dans Blazor , notamment comment définir l’environnement d’une Blazor WebAssembly application.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
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
ms.openlocfilehash: 61d46e0bd83d8bd82bf7faaf9d8f2fecbacc2ffa
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056034"
---
# <a name="aspnet-core-no-locblazor-environments"></a>BlazorEnvironnements ASP.net Core

> [!NOTE]
> Cette rubrique s’applique à Blazor WebAssembly . Pour obtenir des instructions générales sur ASP.NET Core configuration des applications, consultez <xref:fundamentals/environments> .

Lors de l’exécution locale d’une application, l’environnement est par défaut développement. Lorsque l’application est publiée, l’environnement prend par défaut la valeur production.

Une application hébergée Blazor WebAssembly récupère l’environnement à partir du serveur via un intergiciel qui communique l’environnement au navigateur en ajoutant l' `blazor-environment` en-tête. La valeur de l’en-tête est l’environnement. L’application hébergée Blazor et l’application serveur partagent le même environnement. Pour plus d’informations, notamment sur la configuration de l’environnement, consultez <xref:fundamentals/environments> .

Pour une application autonome exécutée localement, le serveur de développement ajoute l' `blazor-environment` en-tête pour spécifier l’environnement de développement. Pour spécifier l’environnement pour d’autres environnements d’hébergement, ajoutez l' `blazor-environment` en-tête.

Dans l’exemple suivant pour IIS, ajoutez l’en-tête personnalisé au `web.config` fichier publié. Le `web.config` fichier se trouve dans le `bin/Release/{TARGET FRAMEWORK}/publish` dossier :

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

Obtenez l’environnement de l’application dans un composant en injectant <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> et en lisant la <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> propriété :

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

Au démarrage, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> expose le <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> via la <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> propriété, qui permet aux développeurs d’avoir une logique spécifique à l’environnement dans leur code :

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

Les méthodes d’extension suivantes permettent de vérifier l’environnement actuel pour les noms d’environnement de développement, de production, intermédiaires et personnalisés :

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* `IsEnvironment("{ENVIRONMENT NAME}")`

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
