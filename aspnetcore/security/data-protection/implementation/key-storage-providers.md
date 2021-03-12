---
title: Fournisseurs de stockage de clés dans ASP.NET Core
author: rick-anderson
description: En savoir plus sur les fournisseurs de stockage de clés dans ASP.NET Core et sur la configuration des emplacements de stockage de clés.
ms.author: riande
ms.date: 12/05/2019
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
uid: security/data-protection/implementation/key-storage-providers
ms.openlocfilehash: 137cdabc12b7cd01b82f7fe9921c17a5be957eb7
ms.sourcegitcommit: acfe51c35497a204f75c2a61125c9408c04493e6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102605527"
---
# <a name="key-storage-providers-in-aspnet-core"></a>Fournisseurs de stockage de clés dans ASP.NET Core

Le système de protection [des données utilise par défaut un mécanisme de découverte](xref:security/data-protection/configuration/default-settings) pour déterminer où les clés de chiffrement doivent être conservées. Le développeur peut remplacer le mécanisme de découverte par défaut et spécifier manuellement l’emplacement.

> [!WARNING]
> Si vous spécifiez un emplacement de persistance de clé explicite, le système de protection des données annule l’inscription du mécanisme de chiffrement à clé par défaut au repos, de sorte que les clés ne sont plus chiffrées au repos. Il est recommandé de [spécifier un mécanisme de chiffrement à clé explicite](xref:security/data-protection/implementation/key-encryption-at-rest) pour les déploiements de production.

## <a name="file-system"></a>Système de fichiers

Pour configurer un référentiel de clés basé sur un système de fichiers, appelez la routine de configuration [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) comme indiqué ci-dessous. Indiquez un [DirectoryInfo](/dotnet/api/system.io.directoryinfo) pointant vers le référentiel où les clés doivent être stockées :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"c:\temp-keys\"));
}
```

Le `DirectoryInfo` peut pointer vers un répertoire sur l’ordinateur local, ou il peut pointer vers un dossier sur un partage réseau. Si vous pointez vers un répertoire sur l’ordinateur local (et que seules les applications sur l’ordinateur local requièrent l’accès pour utiliser ce référentiel), envisagez d’utiliser [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (sur Windows) pour chiffrer les clés au repos. Sinon, envisagez d’utiliser un [certificat X. 509](xref:security/data-protection/implementation/key-encryption-at-rest) pour chiffrer les clés au repos.

## <a name="azure-storage"></a>Stockage Azure

Le package [Azure. extensions. AspNetCore. dataprotection. blobs](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Blobs) permet le stockage des clés de protection des données dans le stockage d’objets BLOB Azure. Les clés peuvent être partagées entre plusieurs instances d’une application Web. Les applications peuvent partager l’authentification cookie ou la protection CSRF sur plusieurs serveurs.

Pour configurer le fournisseur de stockage d’objets BLOB Azure, appelez l’une des surcharges [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blob URI including SAS token>"));
}
```

Si l’application Web s’exécute en tant que service Azure, la chaîne de connexion peut être utilisée pour l’authentification auprès du stockage Azure à l’aide d' [Azure. Storage. blob](/dotnet/api/azure.storage.blobs.blobcontainerclient).

```csharp
string connectionString = "<connection_string>";
string containerName = "my-key-container";
BlobContainerClient container = new BlobContainerClient(connectionString, containerName);

// optional - provision the container automatically
await container.CreateIfNotExistsAsync();

services.AddDataProtection()
    .PersistKeysToAzureBlobStorage(container, "keys.xml");
```

> [!NOTE]
> La chaîne de connexion à votre compte de stockage se trouve dans le portail Azure, sous la section « clés d’accès » ou en exécutant la commande CLI suivante : 
> ```bash
> az storage account show-connection-string --name <account_name> --resource-group <resource_group>
> ```

## <a name="redis"></a>Redis

::: moniker range=">= aspnetcore-2.2"

Le package [Microsoft. AspNetCore. dataprotection. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) permet de stocker les clés de protection des données dans un cache ReDim. Les clés peuvent être partagées entre plusieurs instances d’une application Web. Les applications peuvent partager l’authentification cookie ou la protection CSRF sur plusieurs serveurs.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Le package [Microsoft. AspNetCore. dataprotection. redims](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) permet de stocker les clés de protection des données dans un cache ReDim. Les clés peuvent être partagées entre plusieurs instances d’une application Web. Les applications peuvent partager l’authentification cookie ou la protection CSRF sur plusieurs serveurs.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

Pour configurer sur les ReDim, appelez l’une des surcharges [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToStackExchangeRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Pour configurer sur les ReDim, appelez l’une des surcharges [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

Pour plus d'informations, voir les rubriques suivantes :

* [StackExchange. Redims ConnectionMultiplexer](https://github.com/StackExchange/StackExchange.Redis/blob/main/docs/Basics.md)
* [Cache Redis Azure](/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache#connect-to-the-cache)
* [Exemples de DataProtection ASP.NET Core](https://github.com/dotnet/AspNetCore/tree/2.2.0/src/DataProtection/samples)

## <a name="registry"></a>Registre

**S’applique uniquement aux déploiements Windows.**

Il arrive parfois que l’application ne dispose pas d’un accès en écriture au système de fichiers. Imaginez un scénario dans lequel une application s’exécute en tant que compte de service virtuel (par exemple, l’identité du pool d’applications de *w3wp.exe*). Dans ce cas, l’administrateur peut configurer une clé de Registre accessible par l’identité du compte de service. Appelez la méthode d’extension [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) comme indiqué ci-dessous. Fournissez un [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) pointant vers l’emplacement où les clés de chiffrement doivent être stockées :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToRegistry(Registry.CurrentUser.OpenSubKey(@"SOFTWARE\Sample\keys", true));
}
```

> [!IMPORTANT]
> Nous vous recommandons d’utiliser [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) pour chiffrer les clés au repos.

::: moniker range=">= aspnetcore-2.2"

## <a name="entity-framework-core"></a>Entity Framework Core

Le package [Microsoft. AspNetCore. dataprotection. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) fournit un mécanisme permettant de stocker les clés de protection des données dans une base de données à l’aide d’Entity Framework Core. Le `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` package NuGet doit être ajouté au fichier projet, car il ne fait pas partie du [AspNetCore](xref:fundamentals/metapackage-app).

Avec ce package, les clés peuvent être partagées entre plusieurs instances d’une application Web.

Pour configurer le fournisseur de EF Core, appelez la méthode [PersistKeysToDbContext \<TContext> ](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) :

[!code-csharp[Main](key-storage-providers/sample/Startup.cs?name=snippet&highlight=13-20)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Le paramètre générique, `TContext` , doit hériter de [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) et implémenter [IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):

[!code-csharp[Main](key-storage-providers/sample/MyKeysContext.cs)]

Créez la table `DataProtectionKeys`.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Exécutez les commandes suivantes dans la fenêtre **console du gestionnaire de package** (PMC) :

```powershell
Add-Migration AddDataProtectionKeys -Context MyKeysContext
Update-Database -Context MyKeysContext
```

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

Exécutez les commandes suivantes dans une interface de commande :

```dotnetcli
dotnet ef migrations add AddDataProtectionKeys --context MyKeysContext
dotnet ef database update --context MyKeysContext
```

---

`MyKeysContext` est le `DbContext` défini dans l’exemple de code précédent. Si vous utilisez un `DbContext` avec un nom différent, remplacez par votre `DbContext` nom `MyKeysContext` .

La `DataProtectionKeys` classe/entité adopte la structure indiquée dans le tableau suivant.

| Propriété/champ | Type CLR | Type SQL              |
| -------------- | -------- | --------------------- |
| `Id`           | `int`    | `int`, PK, `IDENTITY(1,1)` , non null   |
| `FriendlyName` | `string` | `nvarchar(MAX)`, null |
| `Xml`          | `string` | `nvarchar(MAX)`, null |

::: moniker-end

## <a name="custom-key-repository"></a>Référentiel de clés personnalisé

Si les mécanismes intégrés ne sont pas appropriés, le développeur peut spécifier son propre mécanisme de persistance de clé en fournissant un [IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository)personnalisé.