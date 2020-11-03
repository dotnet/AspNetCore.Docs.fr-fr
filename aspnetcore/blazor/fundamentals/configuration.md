---
title: Configuration de ASP.NET Core Blazor
author: guardrex
description: En savoir plus sur la configuration des Blazor applications, notamment les paramètres d’application, l’authentification et la configuration de la journalisation.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/29/2020
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
ms.openlocfilehash: f8b1c49ab29bb8a88ca6d9785cd7ee151315e065
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234372"
---
# <a name="aspnet-core-no-locblazor-configuration"></a>Configuration de ASP.NET Core Blazor

> [!NOTE]
> Cette rubrique s’applique à Blazor WebAssembly . Pour obtenir des instructions générales sur ASP.NET Core configuration des applications, consultez <xref:fundamentals/configuration/index> .

Blazor WebAssembly charge la configuration à partir des fichiers de paramètres d’application par défaut :

* `wwwroot/appsettings.json`
* `wwwroot/appsettings.{ENVIRONMENT}.json`

D’autres fournisseurs de configuration inscrits par l’application peuvent également fournir une configuration.

Les fonctionnalités des fournisseurs ou du fournisseur ne sont pas toutes appropriées pour les Blazor WebAssembly applications :

* [Fournisseur de configuration Azure Key Vault](xref:security/key-vault-configuration): le fournisseur n’est pas pris en charge pour l’identité managée et l’ID d’application (ID client) avec des scénarios de clé secrète client. L’ID d’application avec une clé secrète client n’est pas recommandé pour une application ASP.NET Core, en particulier pour les Blazor WebAssembly applications, car la clé secrète client ne peut pas être sécurisée côté client pour accéder au service.
* [Fournisseur de configuration Azure App](/azure/azure-app-configuration/quickstart-aspnet-core-app): le fournisseur n’est pas approprié pour les Blazor WebAssembly applications, car les Blazor WebAssembly applications ne s’exécutent pas sur un serveur dans Azure.

> [!WARNING]
> La configuration dans une Blazor WebAssembly application est visible pour les utilisateurs. **Ne stockez pas les secrets ou les informations d’identification de l’application dans la configuration.**

Pour plus d’informations sur les fournisseurs de configuration, consultez <xref:fundamentals/configuration/index> .

## <a name="app-settings-configuration"></a>Configuration des paramètres de l’application

`wwwroot/appsettings.json`:

```json
{
  "message": "Hello from config!"
}
```

Injecter une <xref:Microsoft.Extensions.Configuration.IConfiguration> instance dans un composant pour accéder aux données de configuration :

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

## <a name="custom-configuration-provider-with-ef-core"></a>Fournisseur de configuration personnalisé avec EF Core

Le fournisseur de configuration personnalisé avec EF Core illustré dans <xref:fundamentals/configuration/index#custom-configuration-provider> fonctionne avec les Blazor WebAssembly applications.

Ajoutez le fournisseur de configuration de l’exemple avec le code suivant dans `Program.Main` ( `Program.cs` ) :

```csharp
builder.Configuration.AddEFConfiguration(
    options => options.UseInMemoryDatabase("InMemoryDb"));
```

Injecter une <xref:Microsoft.Extensions.Configuration.IConfiguration> instance dans un composant pour accéder aux données de configuration :

```razor
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<ul>
    <li>@Configuration["quote1"]</li>
    <li>@Configuration["quote2"]</li>
    <li>@Configuration["quote3"]</li>
</ul>
```

## <a name="memory-configuration-source"></a>Source de la configuration de la mémoire

L’exemple suivant utilise un <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> pour fournir une configuration supplémentaire :

`Program.Main`:

```csharp
using Microsoft.Extensions.Configuration.Memory;

...

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

...

builder.Configuration.Add(memoryConfig);
```

Injecter une <xref:Microsoft.Extensions.Configuration.IConfiguration> instance dans un composant pour accéder aux données de configuration :

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</li>
    <li>Brand: @Configuration["wheels:brand"]</li>
    <li>Type: @Configuration["wheels:brand:type"]</li>
    <li>Year: @Configuration["wheels:year"]</li>
</ul>

@code {
    protected override void OnInitialized()
    {
        var wheelsSection = Configuration.GetSection("wheels");
        
        ...
    }
}
```

Pour lire d’autres fichiers de configuration du `wwwroot` dossier dans la configuration, utilisez un <xref:System.Net.Http.HttpClient> pour obtenir le contenu du fichier. Lors de l’utilisation de cette approche, l' <xref:System.Net.Http.HttpClient> inscription de service existante peut utiliser le client local créé pour lire le fichier, comme le montre l’exemple suivant :

`wwwroot/cars.json`:

```json
{
    "size": "tiny"
}
```

`Program.Main`:

```csharp
using Microsoft.Extensions.Configuration;

...

var http = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddScoped(sp => http);

using var response = await http.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

## <a name="authentication-configuration"></a>Configuration de l’authentification

`wwwroot/appsettings.json`:

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

`Program.Main`:

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a>Configuration de la journalisation

Ajoutez une référence de package pour [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) :

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

Pour l’espace réservé `{VERSION}` , la dernière version stable du package qui correspond à la version du Framework partagé de l’application est disponible dans l' **historique des versions** du package sur [NuGet.org](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration).

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

`Program.Main`:

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a>Configuration du générateur d’ordinateurs hôtes

`Program.Main`:

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a>Configuration mise en cache

Les fichiers de configuration sont mis en cache pour une utilisation hors connexion. Avec les [applications Web progressives (PWA)](xref:blazor/progressive-web-app), vous pouvez uniquement mettre à jour les fichiers de configuration lors de la création d’un déploiement. La modification des fichiers de configuration entre les déploiements n’a aucun effet, car :

* Les utilisateurs ont des versions mises en cache des fichiers qu’ils continuent d’utiliser.
* Les `service-worker.js` fichiers et de PWA `service-worker-assets.js` doivent être reconstruits lors de la compilation, qui signalent à l’application à la prochaine visite en ligne de l’utilisateur que l’application a été redéployée.

Pour plus d’informations sur la façon dont les mises à jour en arrière-plan sont gérées par PWA, consultez <xref:blazor/progressive-web-app#background-updates> .
