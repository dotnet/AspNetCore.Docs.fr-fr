---
title: Fournisseurs de stockage de clés dans ASP.NET Core
author: rick-anderson
description: En savoir plus sur les fournisseurs de stockage de clés dans ASP.NET Core et sur la configuration des emplacements de stockage de clés.
ms.author: riande
ms.date: 12/05/2019
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
uid: security/data-protection/implementation/key-storage-providers
ms.openlocfilehash: 36e8bc494125d0770347ddf32390365d83a91d27
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051744"
---
# <a name="key-storage-providers-in-aspnet-core"></a><span data-ttu-id="df171-103">Fournisseurs de stockage de clés dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="df171-103">Key storage providers in ASP.NET Core</span></span>

<span data-ttu-id="df171-104">Le système de protection [des données utilise par défaut un mécanisme de découverte](xref:security/data-protection/configuration/default-settings) pour déterminer où les clés de chiffrement doivent être conservées.</span><span class="sxs-lookup"><span data-stu-id="df171-104">The data protection system [employs a discovery mechanism by default](xref:security/data-protection/configuration/default-settings) to determine where cryptographic keys should be persisted.</span></span> <span data-ttu-id="df171-105">Le développeur peut remplacer le mécanisme de découverte par défaut et spécifier manuellement l’emplacement.</span><span class="sxs-lookup"><span data-stu-id="df171-105">The developer can override the default discovery mechanism and manually specify the location.</span></span>

> [!WARNING]
> <span data-ttu-id="df171-106">Si vous spécifiez un emplacement de persistance de clé explicite, le système de protection des données annule l’inscription du mécanisme de chiffrement à clé par défaut au repos, de sorte que les clés ne sont plus chiffrées au repos.</span><span class="sxs-lookup"><span data-stu-id="df171-106">If you specify an explicit key persistence location, the data protection system deregisters the default key encryption at rest mechanism, so keys are no longer encrypted at rest.</span></span> <span data-ttu-id="df171-107">Il est recommandé de [spécifier un mécanisme de chiffrement à clé explicite](xref:security/data-protection/implementation/key-encryption-at-rest) pour les déploiements de production.</span><span class="sxs-lookup"><span data-stu-id="df171-107">It's recommended that you additionally [specify an explicit key encryption mechanism](xref:security/data-protection/implementation/key-encryption-at-rest) for production deployments.</span></span>

## <a name="file-system"></a><span data-ttu-id="df171-108">Système de fichiers</span><span class="sxs-lookup"><span data-stu-id="df171-108">File system</span></span>

<span data-ttu-id="df171-109">Pour configurer un référentiel de clés basé sur un système de fichiers, appelez la routine de configuration [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) comme indiqué ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="df171-109">To configure a file system-based key repository, call the [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) configuration routine as shown below.</span></span> <span data-ttu-id="df171-110">Indiquez un [DirectoryInfo](/dotnet/api/system.io.directoryinfo) pointant vers le référentiel où les clés doivent être stockées :</span><span class="sxs-lookup"><span data-stu-id="df171-110">Provide a [DirectoryInfo](/dotnet/api/system.io.directoryinfo) pointing to the repository where keys should be stored:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"c:\temp-keys\"));
}
```

<span data-ttu-id="df171-111">Le `DirectoryInfo` peut pointer vers un répertoire sur l’ordinateur local, ou il peut pointer vers un dossier sur un partage réseau.</span><span class="sxs-lookup"><span data-stu-id="df171-111">The `DirectoryInfo` can point to a directory on the local machine, or it can point to a folder on a network share.</span></span> <span data-ttu-id="df171-112">Si vous pointez vers un répertoire sur l’ordinateur local (et que seules les applications sur l’ordinateur local requièrent l’accès pour utiliser ce référentiel), envisagez d’utiliser [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (sur Windows) pour chiffrer les clés au repos.</span><span class="sxs-lookup"><span data-stu-id="df171-112">If pointing to a directory on the local machine (and the scenario is that only apps on the local machine require access to use this repository), consider using [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (on Windows) to encrypt the keys at rest.</span></span> <span data-ttu-id="df171-113">Sinon, envisagez d’utiliser un [certificat X. 509](xref:security/data-protection/implementation/key-encryption-at-rest) pour chiffrer les clés au repos.</span><span class="sxs-lookup"><span data-stu-id="df171-113">Otherwise, consider using an [X.509 certificate](xref:security/data-protection/implementation/key-encryption-at-rest) to encrypt keys at rest.</span></span>

## <a name="azure-storage"></a><span data-ttu-id="df171-114">Stockage Azure</span><span class="sxs-lookup"><span data-stu-id="df171-114">Azure Storage</span></span>

<span data-ttu-id="df171-115">Le package [Microsoft. AspNetCore. dataprotection. AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/) permet de stocker les clés de protection des données dans le stockage d’objets BLOB Azure.</span><span class="sxs-lookup"><span data-stu-id="df171-115">The [Microsoft.AspNetCore.DataProtection.AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/) package allows storing data protection keys in Azure Blob Storage.</span></span> <span data-ttu-id="df171-116">Les clés peuvent être partagées entre plusieurs instances d’une application Web.</span><span class="sxs-lookup"><span data-stu-id="df171-116">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="df171-117">Les applications peuvent partager l’authentification cookie ou la protection CSRF sur plusieurs serveurs.</span><span class="sxs-lookup"><span data-stu-id="df171-117">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

<span data-ttu-id="df171-118">Pour configurer le fournisseur de stockage d’objets BLOB Azure, appelez l’une des surcharges [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) .</span><span class="sxs-lookup"><span data-stu-id="df171-118">To configure the Azure Blob Storage provider, call one of the [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) overloads.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blob URI including SAS token>"));
}
```

<span data-ttu-id="df171-119">Si l’application Web s’exécute en tant que service Azure, les jetons d’authentification peuvent être créés automatiquement à l’aide de [Microsoft. Azure. services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/).</span><span class="sxs-lookup"><span data-stu-id="df171-119">If the web app is running as an Azure service, authentication tokens can be automatically created using [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/).</span></span>

```csharp
var tokenProvider = new AzureServiceTokenProvider();
var token = await tokenProvider.GetAccessTokenAsync("https://storage.azure.com/");
var credentials = new StorageCredentials(new TokenCredential(token));
var storageAccount = new CloudStorageAccount(credentials, "mystorageaccount", "core.windows.net", useHttps: true);
var client = storageAccount.CreateCloudBlobClient();
var container = client.GetContainerReference("my-key-container");

// optional - provision the container automatically
await container.CreateIfNotExistsAsync();

services.AddDataProtection()
    .PersistKeysToAzureBlobStorage(container, "keys.xml");
```

<span data-ttu-id="df171-120">[Pour plus d’informations sur la configuration de l’authentification de service à service](/azure/key-vault/service-to-service-authentication) , consultez.</span><span class="sxs-lookup"><span data-stu-id="df171-120">See [more details about configuring service-to-service authentication.](/azure/key-vault/service-to-service-authentication)</span></span>

## <a name="redis"></a><span data-ttu-id="df171-121">Redis</span><span class="sxs-lookup"><span data-stu-id="df171-121">Redis</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="df171-122">Le package [Microsoft. AspNetCore. dataprotection. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) permet de stocker les clés de protection des données dans un cache ReDim.</span><span class="sxs-lookup"><span data-stu-id="df171-122">The [Microsoft.AspNetCore.DataProtection.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) package allows storing data protection keys in a Redis cache.</span></span> <span data-ttu-id="df171-123">Les clés peuvent être partagées entre plusieurs instances d’une application Web.</span><span class="sxs-lookup"><span data-stu-id="df171-123">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="df171-124">Les applications peuvent partager l’authentification cookie ou la protection CSRF sur plusieurs serveurs.</span><span class="sxs-lookup"><span data-stu-id="df171-124">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="df171-125">Le package [Microsoft. AspNetCore. dataprotection. redims](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) permet de stocker les clés de protection des données dans un cache ReDim.</span><span class="sxs-lookup"><span data-stu-id="df171-125">The [Microsoft.AspNetCore.DataProtection.Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) package allows storing data protection keys in a Redis cache.</span></span> <span data-ttu-id="df171-126">Les clés peuvent être partagées entre plusieurs instances d’une application Web.</span><span class="sxs-lookup"><span data-stu-id="df171-126">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="df171-127">Les applications peuvent partager l’authentification cookie ou la protection CSRF sur plusieurs serveurs.</span><span class="sxs-lookup"><span data-stu-id="df171-127">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="df171-128">Pour configurer sur les ReDim, appelez l’une des surcharges [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) :</span><span class="sxs-lookup"><span data-stu-id="df171-128">To configure on Redis, call one of the [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) overloads:</span></span>

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

<span data-ttu-id="df171-129">Pour configurer sur les ReDim, appelez l’une des surcharges [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) :</span><span class="sxs-lookup"><span data-stu-id="df171-129">To configure on Redis, call one of the [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) overloads:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

<span data-ttu-id="df171-130">Pour plus d'informations, consultez les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="df171-130">For more information, see the following topics:</span></span>

* [<span data-ttu-id="df171-131">StackExchange. Redims ConnectionMultiplexer</span><span class="sxs-lookup"><span data-stu-id="df171-131">StackExchange.Redis ConnectionMultiplexer</span></span>](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Basics.md)
* [<span data-ttu-id="df171-132">Cache Redis Azure</span><span class="sxs-lookup"><span data-stu-id="df171-132">Azure Redis Cache</span></span>](/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache#connect-to-the-cache)
* [<span data-ttu-id="df171-133">Exemples de DataProtection ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="df171-133">ASP.NET Core DataProtection samples</span></span>](https://github.com/dotnet/AspNetCore/tree/2.2.0/src/DataProtection/samples)

## <a name="registry"></a><span data-ttu-id="df171-134">Registre</span><span class="sxs-lookup"><span data-stu-id="df171-134">Registry</span></span>

<span data-ttu-id="df171-135">**S’applique uniquement aux déploiements Windows.**</span><span class="sxs-lookup"><span data-stu-id="df171-135">**Only applies to Windows deployments.**</span></span>

<span data-ttu-id="df171-136">Il arrive parfois que l’application ne dispose pas d’un accès en écriture au système de fichiers.</span><span class="sxs-lookup"><span data-stu-id="df171-136">Sometimes the app might not have write access to the file system.</span></span> <span data-ttu-id="df171-137">Imaginez un scénario dans lequel une application s’exécute en tant que compte de service virtuel (par exemple, l’identité du pool d’applications de *w3wp.exe* ).</span><span class="sxs-lookup"><span data-stu-id="df171-137">Consider a scenario where an app is running as a virtual service account (such as *w3wp.exe* 's app pool identity).</span></span> <span data-ttu-id="df171-138">Dans ce cas, l’administrateur peut configurer une clé de Registre accessible par l’identité du compte de service.</span><span class="sxs-lookup"><span data-stu-id="df171-138">In these cases, the administrator can provision a registry key that's accessible by the service account identity.</span></span> <span data-ttu-id="df171-139">Appelez la méthode d’extension [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) comme indiqué ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="df171-139">Call the [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) extension method as shown below.</span></span> <span data-ttu-id="df171-140">Fournissez un [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) pointant vers l’emplacement où les clés de chiffrement doivent être stockées :</span><span class="sxs-lookup"><span data-stu-id="df171-140">Provide a [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) pointing to the location where cryptographic keys should be stored:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToRegistry(Registry.CurrentUser.OpenSubKey(@"SOFTWARE\Sample\keys"));
}
```

> [!IMPORTANT]
> <span data-ttu-id="df171-141">Nous vous recommandons d’utiliser [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) pour chiffrer les clés au repos.</span><span class="sxs-lookup"><span data-stu-id="df171-141">We recommend using [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) to encrypt the keys at rest.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="entity-framework-core"></a><span data-ttu-id="df171-142">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="df171-142">Entity Framework Core</span></span>

<span data-ttu-id="df171-143">Le package [Microsoft. AspNetCore. dataprotection. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) fournit un mécanisme permettant de stocker les clés de protection des données dans une base de données à l’aide d’Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="df171-143">The [Microsoft.AspNetCore.DataProtection.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) package provides a mechanism for storing data protection keys to a database using Entity Framework Core.</span></span> <span data-ttu-id="df171-144">Le `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` package NuGet doit être ajouté au fichier projet, car il ne fait pas partie du [AspNetCore](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="df171-144">The `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` NuGet package must be added to the project file, it's not part of the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="df171-145">Avec ce package, les clés peuvent être partagées entre plusieurs instances d’une application Web.</span><span class="sxs-lookup"><span data-stu-id="df171-145">With this package, keys can be shared across multiple instances of a web app.</span></span>

<span data-ttu-id="df171-146">Pour configurer le fournisseur de EF Core, appelez la méthode [PersistKeysToDbContext \<TContext> ](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) :</span><span class="sxs-lookup"><span data-stu-id="df171-146">To configure the EF Core provider, call the [PersistKeysToDbContext\<TContext>](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) method:</span></span>

[!code-csharp[Main](key-storage-providers/sample/Startup.cs?name=snippet&highlight=13-20)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="df171-147">Le paramètre générique, `TContext` , doit hériter de [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) et implémenter [IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):</span><span class="sxs-lookup"><span data-stu-id="df171-147">The generic parameter, `TContext`, must inherit from [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and implement [IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):</span></span>

[!code-csharp[Main](key-storage-providers/sample/MyKeysContext.cs)]

<span data-ttu-id="df171-148">Créez la table `DataProtectionKeys`.</span><span class="sxs-lookup"><span data-stu-id="df171-148">Create the `DataProtectionKeys` table.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="df171-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="df171-149">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="df171-150">Exécutez les commandes suivantes dans la fenêtre **console du gestionnaire de package** (PMC) :</span><span class="sxs-lookup"><span data-stu-id="df171-150">Execute the following commands in the **Package Manager Console** (PMC) window:</span></span>

```powershell
Add-Migration AddDataProtectionKeys -Context MyKeysContext
Update-Database -Context MyKeysContext
```

# <a name="net-core-cli"></a>[<span data-ttu-id="df171-151">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="df171-151">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="df171-152">Exécutez les commandes suivantes dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="df171-152">Execute the following commands in a command shell:</span></span>

```dotnetcli
dotnet ef migrations add AddDataProtectionKeys --context MyKeysContext
dotnet ef database update --context MyKeysContext
```

---

<span data-ttu-id="df171-153">`MyKeysContext` est le `DbContext` défini dans l’exemple de code précédent.</span><span class="sxs-lookup"><span data-stu-id="df171-153">`MyKeysContext` is the `DbContext` defined in the preceding code sample.</span></span> <span data-ttu-id="df171-154">Si vous utilisez un `DbContext` avec un nom différent, remplacez par votre `DbContext` nom `MyKeysContext` .</span><span class="sxs-lookup"><span data-stu-id="df171-154">If you're using a `DbContext` with a different name, substitute your `DbContext` name for `MyKeysContext`.</span></span>

<span data-ttu-id="df171-155">La `DataProtectionKeys` classe/entité adopte la structure indiquée dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="df171-155">The `DataProtectionKeys` class/entity adopts the structure shown in the following table.</span></span>

| <span data-ttu-id="df171-156">Propriété/champ</span><span class="sxs-lookup"><span data-stu-id="df171-156">Property/Field</span></span> | <span data-ttu-id="df171-157">Type CLR</span><span class="sxs-lookup"><span data-stu-id="df171-157">CLR Type</span></span> | <span data-ttu-id="df171-158">Type SQL</span><span class="sxs-lookup"><span data-stu-id="df171-158">SQL Type</span></span>              |
| -------------- | -------- | --------------------- |
| `Id`           | `int`    | <span data-ttu-id="df171-159">`int`, PK, `IDENTITY(1,1)` , non null</span><span class="sxs-lookup"><span data-stu-id="df171-159">`int`, PK, `IDENTITY(1,1)`, not null</span></span>   |
| `FriendlyName` | `string` | <span data-ttu-id="df171-160">`nvarchar(MAX)`, null</span><span class="sxs-lookup"><span data-stu-id="df171-160">`nvarchar(MAX)`, null</span></span> |
| `Xml`          | `string` | <span data-ttu-id="df171-161">`nvarchar(MAX)`, null</span><span class="sxs-lookup"><span data-stu-id="df171-161">`nvarchar(MAX)`, null</span></span> |

::: moniker-end

## <a name="custom-key-repository"></a><span data-ttu-id="df171-162">Référentiel de clés personnalisé</span><span class="sxs-lookup"><span data-stu-id="df171-162">Custom key repository</span></span>

<span data-ttu-id="df171-163">Si les mécanismes intégrés ne sont pas appropriés, le développeur peut spécifier son propre mécanisme de persistance de clé en fournissant un [IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository)personnalisé.</span><span class="sxs-lookup"><span data-stu-id="df171-163">If the in-box mechanisms aren't appropriate, the developer can specify their own key persistence mechanism by providing a custom [IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository).</span></span>
