---
title: Configuration de ASP.NET Core Blazor
author: guardrex
description: En savoir plus sur la configuration des Blazor applications, notamment les paramètres d’application, l’authentification et la configuration de la journalisation.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/10/2020
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
uid: blazor/fundamentals/configuration
ms.openlocfilehash: fd2a9784e92b132d679b2065b37cb37b7dd89cde
ms.sourcegitcommit: 07e7ee573fe4e12be93249a385db745d714ff6ae
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103413403"
---
# <a name="aspnet-core-blazor-configuration"></a><span data-ttu-id="ad9e6-103">Configuration de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="ad9e6-103">ASP.NET Core Blazor configuration</span></span>

> [!NOTE]
> <span data-ttu-id="ad9e6-104">Cette rubrique s’applique à Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="ad9e6-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="ad9e6-105">Pour obtenir des instructions générales sur ASP.NET Core configuration des applications, consultez <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="ad9e6-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="ad9e6-106">Blazor WebAssembly charge la configuration à partir des fichiers de paramètres d’application suivants par défaut :</span><span class="sxs-lookup"><span data-stu-id="ad9e6-106">Blazor WebAssembly loads configuration from the following app settings files by default:</span></span>

* <span data-ttu-id="ad9e6-107">`wwwroot/appsettings.json`.</span><span class="sxs-lookup"><span data-stu-id="ad9e6-107">`wwwroot/appsettings.json`.</span></span>
* <span data-ttu-id="ad9e6-108">`wwwroot/appsettings.{ENVIRONMENT}.json`, où l' `{ENVIRONMENT}` espace réservé est l' [environnement d’exécution](xref:fundamentals/environments)de l’application.</span><span class="sxs-lookup"><span data-stu-id="ad9e6-108">`wwwroot/appsettings.{ENVIRONMENT}.json`, where the `{ENVIRONMENT}` placeholder is the app's [runtime environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="ad9e6-109">D’autres fournisseurs de configuration inscrits par l’application peuvent également fournir une configuration, mais tous les fournisseurs ou les fonctionnalités du fournisseur ne sont pas appropriés pour les Blazor WebAssembly applications :</span><span class="sxs-lookup"><span data-stu-id="ad9e6-109">Other configuration providers registered by the app can also provide configuration, but not all providers or provider features are appropriate for Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="ad9e6-110">[Fournisseur de configuration Azure Key Vault](xref:security/key-vault-configuration): le fournisseur n’est pas pris en charge pour l’identité managée et l’ID d’application (ID client) avec des scénarios de clé secrète client.</span><span class="sxs-lookup"><span data-stu-id="ad9e6-110">[Azure Key Vault configuration provider](xref:security/key-vault-configuration): The provider isn't supported for managed identity and application ID (client ID) with client secret scenarios.</span></span> <span data-ttu-id="ad9e6-111">L’ID d’application avec une clé secrète client n’est pas recommandé pour une application ASP.NET Core, en particulier pour les Blazor WebAssembly applications, car la clé secrète client ne peut pas être sécurisée côté client pour accéder au service Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="ad9e6-111">Application ID with a client secret isn't recommended for any ASP.NET Core app, especially Blazor WebAssembly apps because the client secret can't be secured client-side to access the Azure Key Vault service.</span></span>
* <span data-ttu-id="ad9e6-112">[Fournisseur de configuration Azure App](/azure/azure-app-configuration/quickstart-aspnet-core-app): le fournisseur n’est pas approprié pour les Blazor WebAssembly applications, car les Blazor WebAssembly applications ne s’exécutent pas sur un serveur dans Azure.</span><span class="sxs-lookup"><span data-stu-id="ad9e6-112">[Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app): The provider isn't appropriate for Blazor WebAssembly apps because Blazor WebAssembly apps don't run on a server in Azure.</span></span>

> [!WARNING]
> <span data-ttu-id="ad9e6-113">La configuration dans une Blazor WebAssembly application est visible pour les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="ad9e6-113">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="ad9e6-114">**Ne stockez pas de secrets d’application, d’informations d’identification ou d’autres données sensibles dans la configuration d’une Blazor WebAssembly application.**</span><span class="sxs-lookup"><span data-stu-id="ad9e6-114">**Don't store app secrets, credentials, or any other sensitive data in the configuration of a Blazor WebAssembly app.**</span></span>

<span data-ttu-id="ad9e6-115">Pour plus d’informations sur les fournisseurs de configuration, consultez <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="ad9e6-115">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="ad9e6-116">Configuration des paramètres de l’application</span><span class="sxs-lookup"><span data-stu-id="ad9e6-116">App settings configuration</span></span>

<span data-ttu-id="ad9e6-117">La configuration dans les fichiers de paramètres d’application est chargée par défaut.</span><span class="sxs-lookup"><span data-stu-id="ad9e6-117">Configuration in app settings files are loaded by default.</span></span> <span data-ttu-id="ad9e6-118">Dans l’exemple suivant, une valeur de configuration de l’interface utilisateur est stockée dans un fichier de paramètres d’application et chargée par le Blazor Framework automatiquement.</span><span class="sxs-lookup"><span data-stu-id="ad9e6-118">In the following example, a UI configuration value is stored in an app settings file and loaded by the Blazor framework automatically.</span></span> <span data-ttu-id="ad9e6-119">La valeur est lue par un composant.</span><span class="sxs-lookup"><span data-stu-id="ad9e6-119">The value is read by a component.</span></span>

<span data-ttu-id="ad9e6-120">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="ad9e6-120">`wwwroot/appsettings.json`:</span></span>

```json
{
  "h1FontSize": "50px"
}
```

<span data-ttu-id="ad9e6-121">Injecter une <xref:Microsoft.Extensions.Configuration.IConfiguration> instance dans un composant pour accéder aux données de configuration.</span><span class="sxs-lookup"><span data-stu-id="ad9e6-121">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data.</span></span>

<span data-ttu-id="ad9e6-122">`Pages/ConfigurationExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="ad9e6-122">`Pages/ConfigurationExample.razor`:</span></span>

```razor
@page "/configuration-example"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1 style="font-size:@Configuration["h1FontSize"]">
    Configuration example
</h1>
```

<span data-ttu-id="ad9e6-123">Pour lire d’autres fichiers de configuration du `wwwroot` dossier dans la configuration, utilisez un <xref:System.Net.Http.HttpClient> pour obtenir le contenu du fichier.</span><span class="sxs-lookup"><span data-stu-id="ad9e6-123">To read other configuration files from the `wwwroot` folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="ad9e6-124">L’exemple suivant lit un fichier de configuration ( `cars.json` ) dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="ad9e6-124">The following example reads a configuration file (`cars.json`) into the app's configuration.</span></span>

<span data-ttu-id="ad9e6-125">`wwwroot/cars.json`:</span><span class="sxs-lookup"><span data-stu-id="ad9e6-125">`wwwroot/cars.json`:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="ad9e6-126">Ajoutez l’espace de noms pour <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> à `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="ad9e6-126">Add the namespace for <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="ad9e6-127">Dans `Program.Main` de `Program.cs` , modifiez l' <xref:System.Net.Http.HttpClient> enregistrement du service existant pour utiliser le client pour lire le fichier :</span><span class="sxs-lookup"><span data-stu-id="ad9e6-127">In `Program.Main` of `Program.cs`, modify the existing <xref:System.Net.Http.HttpClient> service registration to use the client to read the file:</span></span>

```csharp
var http = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddScoped(sp => http);

using var response = await http.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

## <a name="memory-configuration-source"></a><span data-ttu-id="ad9e6-128">Source de la configuration de la mémoire</span><span class="sxs-lookup"><span data-stu-id="ad9e6-128">Memory Configuration Source</span></span>

<span data-ttu-id="ad9e6-129">L’exemple suivant utilise un <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> dans `Program.Main` pour fournir une configuration supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="ad9e6-129">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> in `Program.Main` to supply additional configuration.</span></span>

<span data-ttu-id="ad9e6-130">Ajoutez l’espace de noms pour <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> à `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="ad9e6-130">Add the namespace for <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.Configuration.Memory;
```

<span data-ttu-id="ad9e6-131">Dans `Program.Main` de `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="ad9e6-131">In `Program.Main` of `Program.cs`:</span></span>

```csharp
var vehicleData = new Dictionary<string, string>()
{
    { "color", "blue" },
    { "type", "car" },
    { "wheels:count", "3" },
    { "wheels:brand", "Blazin" },
    { "wheels:brand:type", "rally" },
    { "wheels:year", "2008" },
};

var memoryConfig = new MemoryConfigurationSource { InitialData = vehicleData };

builder.Configuration.Add(memoryConfig);
```

<span data-ttu-id="ad9e6-132">Injecter une <xref:Microsoft.Extensions.Configuration.IConfiguration> instance dans un composant pour accéder aux données de configuration.</span><span class="sxs-lookup"><span data-stu-id="ad9e6-132">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data.</span></span>

<span data-ttu-id="ad9e6-133">`Pages/MemoryConfig.razor`:</span><span class="sxs-lookup"><span data-stu-id="ad9e6-133">`Pages/MemoryConfig.razor`:</span></span>

```razor
@page "/memory-config"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Memory configuration example</h1>

<h2>General specifications</h2>

<ul>
    <li>Color: @Configuration["color"]</li>
    <li>Type: @Configuration["type"]</li>
</ul>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</li>
    <li>Brand: @Configuration["wheels:brand"]</li>
    <li>Type: @Configuration["wheels:brand:type"]</li>
    <li>Year: @Configuration["wheels:year"]</li>
</ul>
```

<span data-ttu-id="ad9e6-134">Obtenez une section de la configuration dans le code C# avec <xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="ad9e6-134">Obtain a section of the configuration in C# code with <xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="ad9e6-135">L’exemple suivant obtient la `wheels` section de la configuration de l’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="ad9e6-135">The following example obtains the `wheels` section for the configuration in the preceding example:</span></span>

```razor
@code {
    protected override void OnInitialized()
    {
        var wheelsSection = Configuration.GetSection("wheels");

        ...
    }
}
```

## <a name="authentication-configuration"></a><span data-ttu-id="ad9e6-136">Configuration de l’authentification</span><span class="sxs-lookup"><span data-stu-id="ad9e6-136">Authentication configuration</span></span>

<span data-ttu-id="ad9e6-137">Fournir la configuration d’authentification dans un fichier de paramètres d’application.</span><span class="sxs-lookup"><span data-stu-id="ad9e6-137">Provide authentication configuration in an app settings file.</span></span>

<span data-ttu-id="ad9e6-138">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="ad9e6-138">`wwwroot/appsettings.json`:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="ad9e6-139">Chargez la configuration pour un Identity fournisseur avec <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind%2A?displayProperty=nameWithType> dans `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="ad9e6-139">Load the configuration for an Identity provider with <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind%2A?displayProperty=nameWithType> in `Program.Main`.</span></span> <span data-ttu-id="ad9e6-140">L’exemple suivant charge la configuration d’un [fournisseur OIDC](xref:blazor/security/webassembly/standalone-with-authentication-library).</span><span class="sxs-lookup"><span data-stu-id="ad9e6-140">The following example loads configuration for an [OIDC provider](xref:blazor/security/webassembly/standalone-with-authentication-library).</span></span>

<span data-ttu-id="ad9e6-141">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="ad9e6-141">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="ad9e6-142">Configuration de la journalisation</span><span class="sxs-lookup"><span data-stu-id="ad9e6-142">Logging configuration</span></span>

<span data-ttu-id="ad9e6-143">Ajoutez une référence de package pour [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) au fichier projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="ad9e6-143">Add a package reference for [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERION}" />
```

<span data-ttu-id="ad9e6-144">Dans l’exemple précédent, l' `{VERSION}` espace réservé correspond à la version du package.</span><span class="sxs-lookup"><span data-stu-id="ad9e6-144">In the preceding example, the `{VERSION}` placeholder is the package's version.</span></span> <span data-ttu-id="ad9e6-145">Les versions de package se trouvent sur [NuGet.org](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration).</span><span class="sxs-lookup"><span data-stu-id="ad9e6-145">Package versions are found at [NuGet.org](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration).</span></span>

<span data-ttu-id="ad9e6-146">Dans le fichier de paramètres de l’application, fournissez la configuration de journalisation.</span><span class="sxs-lookup"><span data-stu-id="ad9e6-146">In the app settings file, provide logging configuration.</span></span> <span data-ttu-id="ad9e6-147">La configuration de la journalisation est chargée dans `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="ad9e6-147">The logging configuration is loaded in `Program.Main`.</span></span>

<span data-ttu-id="ad9e6-148">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="ad9e6-148">`wwwroot/appsettings.json`:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```

<span data-ttu-id="ad9e6-149">Ajoutez l’espace de noms pour <xref:Microsoft.Extensions.Logging?displayProperty=fullName> à `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="ad9e6-149">Add the namespace for <xref:Microsoft.Extensions.Logging?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.Logging;
```

<span data-ttu-id="ad9e6-150">Dans `Program.Main` de `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="ad9e6-150">In `Program.Main` of `Program.cs`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="ad9e6-151">Configuration du générateur d’ordinateurs hôtes</span><span class="sxs-lookup"><span data-stu-id="ad9e6-151">Host builder configuration</span></span>

<span data-ttu-id="ad9e6-152">Lisez configuration de l’ordinateur hôte à partir de <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Configuration?displayProperty=nameWithType> dans `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="ad9e6-152">Read host builder configuration from <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Configuration?displayProperty=nameWithType> in `Program.Main`.</span></span>

<span data-ttu-id="ad9e6-153">Dans `Program.Main` de `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="ad9e6-153">In `Program.Main` of `Program.cs`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="ad9e6-154">Configuration mise en cache</span><span class="sxs-lookup"><span data-stu-id="ad9e6-154">Cached configuration</span></span>

<span data-ttu-id="ad9e6-155">Les fichiers de configuration sont mis en cache pour une utilisation hors connexion.</span><span class="sxs-lookup"><span data-stu-id="ad9e6-155">Configuration files are cached for offline use.</span></span> <span data-ttu-id="ad9e6-156">Avec les [applications Web progressives (PWA)](xref:blazor/progressive-web-app), vous pouvez uniquement mettre à jour les fichiers de configuration lors de la création d’un déploiement.</span><span class="sxs-lookup"><span data-stu-id="ad9e6-156">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="ad9e6-157">La modification des fichiers de configuration entre les déploiements n’a aucun effet, car :</span><span class="sxs-lookup"><span data-stu-id="ad9e6-157">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="ad9e6-158">Les utilisateurs ont des versions mises en cache des fichiers qu’ils continuent d’utiliser.</span><span class="sxs-lookup"><span data-stu-id="ad9e6-158">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="ad9e6-159">Les `service-worker.js` fichiers et de PWA `service-worker-assets.js` doivent être reconstruits lors de la compilation, qui signalent à l’application à la prochaine visite en ligne de l’utilisateur que l’application a été redéployée.</span><span class="sxs-lookup"><span data-stu-id="ad9e6-159">The PWA's `service-worker.js` and `service-worker-assets.js` files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="ad9e6-160">Pour plus d’informations sur la façon dont les mises à jour en arrière-plan sont gérées par PWA, consultez <xref:blazor/progressive-web-app#background-updates> .</span><span class="sxs-lookup"><span data-stu-id="ad9e6-160">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>
