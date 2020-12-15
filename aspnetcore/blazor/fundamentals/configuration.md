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
ms.openlocfilehash: 5889d775c09ee23f19bf3ff59344c52d469c4bdc
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97485965"
---
# <a name="aspnet-core-no-locblazor-configuration"></a>Configuration de ASP.NET Core Blazor

> [!NOTE]
> Cette rubrique s’applique à Blazor WebAssembly . Pour obtenir des instructions générales sur ASP.NET Core configuration des applications, consultez <xref:fundamentals/configuration/index> .

Blazor WebAssembly charge la configuration à partir des fichiers de paramètres d’application suivants par défaut :

* `wwwroot/appsettings.json`.
* `wwwroot/appsettings.{ENVIRONMENT}.json`, où l' `{ENVIRONMENT}` espace réservé est l' [environnement d’exécution](xref:fundamentals/environments)de l’application.

D’autres fournisseurs de configuration inscrits par l’application peuvent également fournir une configuration, mais tous les fournisseurs ou les fonctionnalités du fournisseur ne sont pas appropriés pour les Blazor WebAssembly applications :

* [Fournisseur de configuration Azure Key Vault](xref:security/key-vault-configuration): le fournisseur n’est pas pris en charge pour l’identité managée et l’ID d’application (ID client) avec des scénarios de clé secrète client. L’ID d’application avec une clé secrète client n’est pas recommandé pour une application ASP.NET Core, en particulier pour les Blazor WebAssembly applications, car la clé secrète client ne peut pas être sécurisée côté client pour accéder au service Azure Key Vault.
* [Fournisseur de configuration Azure App](/azure/azure-app-configuration/quickstart-aspnet-core-app): le fournisseur n’est pas approprié pour les Blazor WebAssembly applications, car les Blazor WebAssembly applications ne s’exécutent pas sur un serveur dans Azure.

> [!WARNING]
> La configuration dans une Blazor WebAssembly application est visible pour les utilisateurs. **Ne stockez pas de secrets d’application, d’informations d’identification ou d’autres données sensibles dans la configuration d’une Blazor WebAssembly application.**

Pour plus d’informations sur les fournisseurs de configuration, consultez <xref:fundamentals/configuration/index> .

## <a name="app-settings-configuration"></a>Configuration des paramètres de l’application

La configuration dans les fichiers de paramètres d’application est chargée par défaut. Dans l’exemple suivant, une valeur de configuration de l’interface utilisateur est stockée dans un fichier de paramètres d’application et chargée par le Blazor Framework automatiquement. La valeur est lue par un composant.

`wwwroot/appsettings.json`:

```json
{
  "h1FontSize": "50px"
}
```

Injecter une <xref:Microsoft.Extensions.Configuration.IConfiguration> instance dans un composant pour accéder aux données de configuration.

`Pages/ConfigurationExample.razor`:

```razor
@page "/configuration-example"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1 style="font-size:@Configuration["h1FontSize"]">
    Configuration example
</h1>
```

Pour lire d’autres fichiers de configuration du `wwwroot` dossier dans la configuration, utilisez un <xref:System.Net.Http.HttpClient> pour obtenir le contenu du fichier. L’exemple suivant lit un fichier de configuration ( `cars.json` ) dans la configuration de l’application.

`wwwroot/cars.json`:

```json
{
    "size": "tiny"
}
```

Ajoutez l’espace de noms pour <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> à `Program.cs` :

```csharp
using Microsoft.Extensions.Configuration;
```

Dans `Program.Main` de `Program.cs` , modifiez l' <xref:System.Net.Http.HttpClient> enregistrement du service existant pour utiliser le client pour lire le fichier :

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

## <a name="custom-configuration-provider-with-ef-core"></a>Fournisseur de configuration personnalisé avec EF Core

Le fournisseur de configuration personnalisé avec EF Core illustré dans <xref:fundamentals/configuration/index#custom-configuration-provider> fonctionne avec les Blazor WebAssembly applications.

> [!WARNING]
> Les chaînes de connexion et les bases de données chargées avec les Blazor WebAssembly applications ne sont pas sécurisées et ne doivent pas être utilisées pour stocker des données sensibles.

Ajoutez des références de package pour [`Microsoft.EntityFrameworkCore`](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore) et [`Microsoft.EntityFrameworkCore.InMemory`](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory) au fichier projet de l’application.

Ajoutez les classes de configuration EF Core décrites dans <xref:fundamentals/configuration/index#custom-configuration-provider> .

Ajouter des espaces de noms pour <xref:Microsoft.EntityFrameworkCore?displayProperty=fullName> et <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> à `Program.cs` :

```csharp
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration.Memory;
```

Dans `Program.Main` de `Program.cs` :

```csharp
builder.Configuration.AddEFConfiguration(
    options => options.UseInMemoryDatabase("InMemoryDb"));
```

Injecter une <xref:Microsoft.Extensions.Configuration.IConfiguration> instance dans un composant pour accéder aux données de configuration.

`Pages/EFCoreConfig.razor`:

```razor
@page "/efcore-config"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>EF Core configuration example</h1>

<h2>Quotes</h2>

<ul>
    <li>@Configuration["quote1"]</li>
    <li>@Configuration["quote2"]</li>
    <li>@Configuration["quote3"]</li>
</ul>

<p>
    Quotes &copy;2005 
    <a href="https://www.uphe.com/">Universal Pictures</a>: 
    <a href="https://www.uphe.com/movies/serenity">Serenity</a>
</p>
```

## <a name="memory-configuration-source"></a>Source de la configuration de la mémoire

L’exemple suivant utilise un <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> dans `Program.Main` pour fournir une configuration supplémentaire.

Ajoutez l’espace de noms pour <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> à `Program.cs` :

```csharp
using Microsoft.Extensions.Configuration.Memory;
```

Dans `Program.Main` de `Program.cs` :

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

Injecter une <xref:Microsoft.Extensions.Configuration.IConfiguration> instance dans un composant pour accéder aux données de configuration.

`Pages/MemoryConfig.razor`:

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

Obtenez une section de la configuration dans le code C# avec <xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection%2A?displayProperty=nameWithType> . L’exemple suivant obtient la `wheels` section de la configuration de l’exemple précédent :

```razor
@code {
    protected override void OnInitialized()
    {
        var wheelsSection = Configuration.GetSection("wheels");

        ...
    }
}
```

## <a name="authentication-configuration"></a>Configuration de l’authentification

Fournir la configuration d’authentification dans un fichier de paramètres d’application.

`wwwroot/appsettings.json`:

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

Chargez la configuration pour un Identity fournisseur avec <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind%2A?displayProperty=nameWithType> dans `Program.Main` . L’exemple suivant charge la configuration d’un [fournisseur OIDC](xref:blazor/security/webassembly/standalone-with-authentication-library).

`Program.cs`:

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a>Configuration de la journalisation

Ajoutez une référence de package pour [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) au fichier projet de l’application. Dans le fichier de paramètres de l’application, fournissez la configuration de journalisation. La configuration de la journalisation est chargée dans `Program.Main` .

`wwwroot/appsettings.json`:

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

Ajoutez l’espace de noms pour <xref:Microsoft.Extensions.Logging?displayProperty=fullName> à `Program.cs` :

```csharp
using Microsoft.Extensions.Logging;
```

Dans `Program.Main` de `Program.cs` :

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a>Configuration du générateur d’ordinateurs hôtes

Lisez configuration de l’ordinateur hôte à partir de <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Configuration?displayProperty=nameWithType> dans `Program.Main` .

Dans `Program.Main` de `Program.cs` :

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a>Configuration mise en cache

Les fichiers de configuration sont mis en cache pour une utilisation hors connexion. Avec les [applications Web progressives (PWA)](xref:blazor/progressive-web-app), vous pouvez uniquement mettre à jour les fichiers de configuration lors de la création d’un déploiement. La modification des fichiers de configuration entre les déploiements n’a aucun effet, car :

* Les utilisateurs ont des versions mises en cache des fichiers qu’ils continuent d’utiliser.
* Les `service-worker.js` fichiers et de PWA `service-worker-assets.js` doivent être reconstruits lors de la compilation, qui signalent à l’application à la prochaine visite en ligne de l’utilisateur que l’application a été redéployée.

Pour plus d’informations sur la façon dont les mises à jour en arrière-plan sont gérées par PWA, consultez <xref:blazor/progressive-web-app#background-updates> .
