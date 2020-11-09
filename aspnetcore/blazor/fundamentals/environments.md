---
title: BlazorEnvironnements ASP.net Core
author: guardrex
description: 'Découvrez les environnements dans Blazor , notamment comment définir l’environnement d’une Blazor WebAssembly application.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/fundamentals/environments
ms.openlocfilehash: 61d46e0bd83d8bd82bf7faaf9d8f2fecbacc2ffa
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056034"
---
# <a name="aspnet-core-no-locblazor-environments"></a><span data-ttu-id="39049-103">BlazorEnvironnements ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="39049-103">ASP.NET Core Blazor environments</span></span>

> [!NOTE]
> <span data-ttu-id="39049-104">Cette rubrique s’applique à Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="39049-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="39049-105">Pour obtenir des instructions générales sur ASP.NET Core configuration des applications, consultez <xref:fundamentals/environments> .</span><span class="sxs-lookup"><span data-stu-id="39049-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="39049-106">Lors de l’exécution locale d’une application, l’environnement est par défaut développement.</span><span class="sxs-lookup"><span data-stu-id="39049-106">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="39049-107">Lorsque l’application est publiée, l’environnement prend par défaut la valeur production.</span><span class="sxs-lookup"><span data-stu-id="39049-107">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="39049-108">Une application hébergée Blazor WebAssembly récupère l’environnement à partir du serveur via un intergiciel qui communique l’environnement au navigateur en ajoutant l' `blazor-environment` en-tête.</span><span class="sxs-lookup"><span data-stu-id="39049-108">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="39049-109">La valeur de l’en-tête est l’environnement.</span><span class="sxs-lookup"><span data-stu-id="39049-109">The value of the header is the environment.</span></span> <span data-ttu-id="39049-110">L’application hébergée Blazor et l’application serveur partagent le même environnement.</span><span class="sxs-lookup"><span data-stu-id="39049-110">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="39049-111">Pour plus d’informations, notamment sur la configuration de l’environnement, consultez <xref:fundamentals/environments> .</span><span class="sxs-lookup"><span data-stu-id="39049-111">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="39049-112">Pour une application autonome exécutée localement, le serveur de développement ajoute l' `blazor-environment` en-tête pour spécifier l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="39049-112">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="39049-113">Pour spécifier l’environnement pour d’autres environnements d’hébergement, ajoutez l' `blazor-environment` en-tête.</span><span class="sxs-lookup"><span data-stu-id="39049-113">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="39049-114">Dans l’exemple suivant pour IIS, ajoutez l’en-tête personnalisé au `web.config` fichier publié.</span><span class="sxs-lookup"><span data-stu-id="39049-114">In the following example for IIS, add the custom header to the published `web.config` file.</span></span> <span data-ttu-id="39049-115">Le `web.config` fichier se trouve dans le `bin/Release/{TARGET FRAMEWORK}/publish` dossier :</span><span class="sxs-lookup"><span data-stu-id="39049-115">The `web.config` file is located in the `bin/Release/{TARGET FRAMEWORK}/publish` folder:</span></span>

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
> <span data-ttu-id="39049-116">Pour utiliser un `web.config` fichier personnalisé pour IIS qui n’est pas remplacé lors de la publication de l’application dans le `publish` dossier, consultez <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig> .</span><span class="sxs-lookup"><span data-stu-id="39049-116">To use a custom `web.config` file for IIS that isn't overwritten when the app is published to the `publish` folder, see <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="39049-117">Obtenez l’environnement de l’application dans un composant en injectant <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> et en lisant la <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> propriété :</span><span class="sxs-lookup"><span data-stu-id="39049-117">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="39049-118">Au démarrage, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> expose le <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> via la <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> propriété, qui permet aux développeurs d’avoir une logique spécifique à l’environnement dans leur code :</span><span class="sxs-lookup"><span data-stu-id="39049-118">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="39049-119">Les méthodes d’extension suivantes permettent de vérifier l’environnement actuel pour les noms d’environnement de développement, de production, intermédiaires et personnalisés :</span><span class="sxs-lookup"><span data-stu-id="39049-119">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

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

<span data-ttu-id="39049-120">La <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> propriété peut être utilisée au démarrage lorsque le <xref:Microsoft.AspNetCore.Components.NavigationManager> service n’est pas disponible.</span><span class="sxs-lookup"><span data-stu-id="39049-120">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="39049-121">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="39049-121">Additional resources</span></span>

* <xref:fundamentals/environments>
