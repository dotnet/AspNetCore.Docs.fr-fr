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
ms.openlocfilehash: 3d9b0cab42a826c7a5868324d891e597cd9ed986
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97678297"
---
# <a name="aspnet-core-no-locblazor-environments"></a><span data-ttu-id="f2493-103">BlazorEnvironnements ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="f2493-103">ASP.NET Core Blazor environments</span></span>

> [!NOTE]
> <span data-ttu-id="f2493-104">Cette rubrique s’applique à Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="f2493-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="f2493-105">Pour obtenir des conseils généraux sur ASP.NET Core configuration d’application, qui décrit les approches à utiliser pour les Blazor Server applications, consultez <xref:fundamentals/environments> .</span><span class="sxs-lookup"><span data-stu-id="f2493-105">For general guidance on ASP.NET Core app configuration, which describes the approaches to use for Blazor Server apps, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="f2493-106">Lors de l’exécution locale d’une application, l’environnement est par défaut développement.</span><span class="sxs-lookup"><span data-stu-id="f2493-106">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="f2493-107">Lorsque l’application est publiée, l’environnement prend par défaut la valeur production.</span><span class="sxs-lookup"><span data-stu-id="f2493-107">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="f2493-108">L’application côté client Blazor ( *`Client`* ) d’une solution hébergée Blazor WebAssembly détermine l’environnement à partir de l' *`Server`* application de la solution via un intergiciel (middleware) qui communique l’environnement au navigateur.</span><span class="sxs-lookup"><span data-stu-id="f2493-108">The client-side Blazor app (*`Client`*) of a hosted Blazor WebAssembly solution determines the environment from the *`Server`* app of the solution via a middleware that communicates the environment to the browser.</span></span> <span data-ttu-id="f2493-109">L' *`Server`* application ajoute un en-tête nommé `blazor-environment` avec l’environnement comme valeur de l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="f2493-109">The *`Server`* app adds a header named `blazor-environment` with the environment as the value of the header.</span></span> <span data-ttu-id="f2493-110">L' *`Client`* application lit l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="f2493-110">The *`Client`* app reads the header.</span></span> <span data-ttu-id="f2493-111">L' *`Server`* application de la solution est une application ASP.net Core, de sorte que vous trouverez plus d’informations sur la configuration de l’environnement dans <xref:fundamentals/environments> .</span><span class="sxs-lookup"><span data-stu-id="f2493-111">The *`Server`* app of the solution is an ASP.NET Core app, so more information on how to configure the environment is found in <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="f2493-112">Pour une Blazor WebAssembly application autonome exécutée localement, le serveur de développement ajoute l' `blazor-environment` en-tête pour spécifier l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="f2493-112">For a standalone Blazor WebAssembly app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="f2493-113">Pour spécifier l’environnement pour d’autres environnements d’hébergement, ajoutez l' `blazor-environment` en-tête.</span><span class="sxs-lookup"><span data-stu-id="f2493-113">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="f2493-114">Dans l’exemple suivant pour IIS, l’en-tête personnalisé ( `blazor-environment` ) est ajouté au `web.config` fichier publié.</span><span class="sxs-lookup"><span data-stu-id="f2493-114">In the following example for IIS, the custom header (`blazor-environment`) is added to the published `web.config` file.</span></span> <span data-ttu-id="f2493-115">Le `web.config` fichier se trouve dans le `bin/Release/{TARGET FRAMEWORK}/publish` dossier, où l’espace réservé `{TARGET FRAMEWORK}` est la version cible du .NET Framework :</span><span class="sxs-lookup"><span data-stu-id="f2493-115">The `web.config` file is located in the `bin/Release/{TARGET FRAMEWORK}/publish` folder, where the placeholder `{TARGET FRAMEWORK}` is the target framework:</span></span>

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
> <span data-ttu-id="f2493-116">Pour utiliser un `web.config` fichier personnalisé pour IIS qui n’est pas remplacé lors de la publication de l’application dans le `publish` dossier, consultez <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig> .</span><span class="sxs-lookup"><span data-stu-id="f2493-116">To use a custom `web.config` file for IIS that isn't overwritten when the app is published to the `publish` folder, see <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="f2493-117">Obtenez l’environnement de l’application dans un composant en injectant <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> et en lisant la <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> propriété.</span><span class="sxs-lookup"><span data-stu-id="f2493-117">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property.</span></span>

<span data-ttu-id="f2493-118">`Pages/ReadEnvironment.razor`:</span><span class="sxs-lookup"><span data-stu-id="f2493-118">`Pages/ReadEnvironment.razor`:</span></span>

[!code-razor[](environments/samples_snapshot/ReadEnvironment.razor?highlight=3,7)]

<span data-ttu-id="f2493-119">Au démarrage, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> expose le <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> via la <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> propriété, qui active la logique spécifique à l’environnement dans le code du générateur d’ordinateur hôte.</span><span class="sxs-lookup"><span data-stu-id="f2493-119">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables environment-specific logic in host builder code.</span></span>

<span data-ttu-id="f2493-120">Dans `Program.Main` de `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="f2493-120">In `Program.Main` of `Program.cs`:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="f2493-121">Les méthodes d’extension suivantes fournies par le biais <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions> de permettent de vérifier l’environnement actuel pour les noms d’environnement de développement, de production, intermédiaires et personnalisés :</span><span class="sxs-lookup"><span data-stu-id="f2493-121">The following convenience extension methods provided through <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions> permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsDevelopment%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsProduction%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsStaging%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsEnvironment%2A>

<span data-ttu-id="f2493-122">Dans `Program.Main` de `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="f2493-122">In `Program.Main` of `Program.cs`:</span></span>

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

<span data-ttu-id="f2493-123">La <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> propriété peut être utilisée au démarrage lorsque le <xref:Microsoft.AspNetCore.Components.NavigationManager> service n’est pas disponible.</span><span class="sxs-lookup"><span data-stu-id="f2493-123">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f2493-124">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="f2493-124">Additional resources</span></span>

* <xref:fundamentals/environments>
