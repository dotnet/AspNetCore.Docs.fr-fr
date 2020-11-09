---
title: Configurer la protection des données ASP.NET Core
author: rick-anderson
description: Découvrez comment configurer la protection des données dans ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 11/02/2020
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
uid: security/data-protection/configuration/overview
ms.openlocfilehash: 048f6d6d57d3cc5d64004e18b18a3347ee92e450
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234567"
---
# <a name="configure-aspnet-core-data-protection"></a><span data-ttu-id="62d65-103">Configurer la protection des données ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="62d65-103">Configure ASP.NET Core Data Protection</span></span>

<span data-ttu-id="62d65-104">Lorsque le système de protection des données est initialisé, il applique les [paramètres par défaut](xref:security/data-protection/configuration/default-settings) en fonction de l’environnement opérationnel.</span><span class="sxs-lookup"><span data-stu-id="62d65-104">When the Data Protection system is initialized, it applies [default settings](xref:security/data-protection/configuration/default-settings) based on the operational environment.</span></span> <span data-ttu-id="62d65-105">Ces paramètres sont généralement appropriés pour les applications qui s’exécutent sur un seul ordinateur.</span><span class="sxs-lookup"><span data-stu-id="62d65-105">These settings are generally appropriate for apps running on a single machine.</span></span> <span data-ttu-id="62d65-106">Dans certains cas, un développeur peut souhaiter modifier les paramètres par défaut :</span><span class="sxs-lookup"><span data-stu-id="62d65-106">There are cases where a developer may want to change the default settings:</span></span>

* <span data-ttu-id="62d65-107">L’application est répartie sur plusieurs ordinateurs.</span><span class="sxs-lookup"><span data-stu-id="62d65-107">The app is spread across multiple machines.</span></span>
* <span data-ttu-id="62d65-108">Pour des raisons de conformité.</span><span class="sxs-lookup"><span data-stu-id="62d65-108">For compliance reasons.</span></span>

<span data-ttu-id="62d65-109">Pour ces scénarios, le système de protection des données offre une API de configuration riche.</span><span class="sxs-lookup"><span data-stu-id="62d65-109">For these scenarios, the Data Protection system offers a rich configuration API.</span></span>

> [!WARNING]
> <span data-ttu-id="62d65-110">Comme pour les fichiers de configuration, l’anneau de clé de protection des données doit être protégé à l’aide des autorisations appropriées.</span><span class="sxs-lookup"><span data-stu-id="62d65-110">Similar to configuration files, the data protection key ring should be protected using appropriate permissions.</span></span> <span data-ttu-id="62d65-111">Vous pouvez choisir de chiffrer les clés au repos, mais cela n’empêche pas les attaquants de créer de nouvelles clés.</span><span class="sxs-lookup"><span data-stu-id="62d65-111">You can choose to encrypt keys at rest, but this doesn't prevent attackers from creating new keys.</span></span> <span data-ttu-id="62d65-112">Par conséquent, la sécurité de votre application est affectée.</span><span class="sxs-lookup"><span data-stu-id="62d65-112">Consequently, your app's security is impacted.</span></span> <span data-ttu-id="62d65-113">L’accès à l’emplacement de stockage configuré avec la protection des données doit être limité à l’application elle-même, comme dans le cas où vous protégeriez les fichiers de configuration.</span><span class="sxs-lookup"><span data-stu-id="62d65-113">The storage location configured with Data Protection should have its access limited to the app itself, similar to the way you would protect configuration files.</span></span> <span data-ttu-id="62d65-114">Par exemple, si vous choisissez de stocker votre clé Ring sur disque, utilisez les autorisations du système de fichiers.</span><span class="sxs-lookup"><span data-stu-id="62d65-114">For example, if you choose to store your key ring on disk, use file system permissions.</span></span> <span data-ttu-id="62d65-115">Assurez-vous que l’identité sous laquelle votre application Web s’exécute dispose de l’accès en lecture, en écriture et en création à ce répertoire.</span><span class="sxs-lookup"><span data-stu-id="62d65-115">Ensure only the identity under which your web app runs has read, write, and create access to that directory.</span></span> <span data-ttu-id="62d65-116">Si vous utilisez le stockage d’objets BLOB Azure, seule l’application Web doit avoir la possibilité de lire, d’écrire ou de créer de nouvelles entrées dans le magasin d’objets BLOB, etc.</span><span class="sxs-lookup"><span data-stu-id="62d65-116">If you use Azure Blob Storage, only the web app should have the ability to read, write, or create new entries in the blob store, etc.</span></span>
>
> <span data-ttu-id="62d65-117">La méthode d’extension [AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) retourne un [IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="62d65-117">The extension method [AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) returns an [IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder).</span></span> <span data-ttu-id="62d65-118">`IDataProtectionBuilder` expose les méthodes d’extension que vous pouvez lier pour configurer les options de protection des données.</span><span class="sxs-lookup"><span data-stu-id="62d65-118">`IDataProtectionBuilder` exposes extension methods that you can chain together to configure Data Protection options.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="62d65-119">Les packages NuGet suivants sont requis pour les extensions de protection des données utilisées dans cet article :</span><span class="sxs-lookup"><span data-stu-id="62d65-119">The following NuGet packages are required for the Data Protection extensions used in this article:</span></span>

* [<span data-ttu-id="62d65-120">Azure. extensions. AspNetCore. DataProtection. blob</span><span class="sxs-lookup"><span data-stu-id="62d65-120">Azure.Extensions.AspNetCore.DataProtection.Blobs</span></span>](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Blobs)
* [<span data-ttu-id="62d65-121">Azure. extensions. AspNetCore. DataProtection. Keys</span><span class="sxs-lookup"><span data-stu-id="62d65-121">Azure.Extensions.AspNetCore.DataProtection.Keys</span></span>](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Keys)

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="protectkeyswithazurekeyvault"></a><span data-ttu-id="62d65-122">ProtectKeysWithAzureKeyVault</span><span class="sxs-lookup"><span data-stu-id="62d65-122">ProtectKeysWithAzureKeyVault</span></span>

<span data-ttu-id="62d65-123">Connectez-vous à Azure à l’aide de l’interface CLI, par exemple :</span><span class="sxs-lookup"><span data-stu-id="62d65-123">Log in to Azure using the CLI, for example:</span></span>

```azurecli
az login
``` 

<span data-ttu-id="62d65-124">Pour stocker des clés dans [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configurez le système avec [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) dans la `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="62d65-124">To store keys in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure the system with [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) in the `Startup` class.</span></span> <span data-ttu-id="62d65-125">`blobUriWithSasToken` URI complet dans lequel le fichier de clé doit être stocké.</span><span class="sxs-lookup"><span data-stu-id="62d65-125">`blobUriWithSasToken` is the full URI where the key file should be stored.</span></span> <span data-ttu-id="62d65-126">L’URI doit contenir le jeton SAS en tant que paramètre de chaîne de requête :</span><span class="sxs-lookup"><span data-stu-id="62d65-126">The URI must contain the SAS token as a query string parameter:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault(new Uri("<keyIdentifier>"), new DefaultAzureCredential());
}
```

<span data-ttu-id="62d65-127">Définissez l’emplacement de stockage de la sonnerie de clé (par exemple, [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)).</span><span class="sxs-lookup"><span data-stu-id="62d65-127">Set the key ring storage location (for example, [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)).</span></span> <span data-ttu-id="62d65-128">L’emplacement doit être défini, car l’appel de `ProtectKeysWithAzureKeyVault` implémente un [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) qui désactive les paramètres de protection automatique des données, y compris l’emplacement de stockage de la sonnerie de clé.</span><span class="sxs-lookup"><span data-stu-id="62d65-128">The location must be set because calling `ProtectKeysWithAzureKeyVault` implements an [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) that disables automatic data protection settings, including the key ring storage location.</span></span> <span data-ttu-id="62d65-129">L’exemple précédent utilise le stockage d’objets BLOB Azure pour conserver l’anneau de clé.</span><span class="sxs-lookup"><span data-stu-id="62d65-129">The preceding example uses Azure Blob Storage to persist the key ring.</span></span> <span data-ttu-id="62d65-130">Pour plus d’informations, consultez [fournisseurs de stockage de clés : stockage Azure](xref:security/data-protection/implementation/key-storage-providers#azure-storage).</span><span class="sxs-lookup"><span data-stu-id="62d65-130">For more information, see [Key storage providers: Azure Storage](xref:security/data-protection/implementation/key-storage-providers#azure-storage).</span></span> <span data-ttu-id="62d65-131">Vous pouvez également conserver l’anneau de clé localement avec [PersistKeysToFileSystem](xref:security/data-protection/implementation/key-storage-providers#file-system).</span><span class="sxs-lookup"><span data-stu-id="62d65-131">You can also persist the key ring locally with [PersistKeysToFileSystem](xref:security/data-protection/implementation/key-storage-providers#file-system).</span></span>

<span data-ttu-id="62d65-132">`keyIdentifier`Est l’identificateur de clé du coffre de clés utilisé pour le chiffrement à clé.</span><span class="sxs-lookup"><span data-stu-id="62d65-132">The `keyIdentifier` is the key vault key identifier used for key encryption.</span></span> <span data-ttu-id="62d65-133">Par exemple, une clé créée dans Key Vault nommée `dataprotection` dans `contosokeyvault` a l’identificateur de clé `https://contosokeyvault.vault.azure.net/keys/dataprotection/` .</span><span class="sxs-lookup"><span data-stu-id="62d65-133">For example, a key created in key vault named `dataprotection` in the `contosokeyvault` has the key identifier `https://contosokeyvault.vault.azure.net/keys/dataprotection/`.</span></span> <span data-ttu-id="62d65-134">Fournissez l’application avec les autorisations de clé de **désencapsulage** et de retour à la **ligne** pour le coffre de clés.</span><span class="sxs-lookup"><span data-stu-id="62d65-134">Provide the app with **Unwrap Key** and **Wrap Key** permissions to the key vault.</span></span>

<span data-ttu-id="62d65-135">`ProtectKeysWithAzureKeyVault` surcharges</span><span class="sxs-lookup"><span data-stu-id="62d65-135">`ProtectKeysWithAzureKeyVault` overloads:</span></span>

* <span data-ttu-id="62d65-136">[ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, Uri, TokenCredential)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionkeyvaultkeybuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionKeyVaultKeyBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_Uri_Azure_Core_TokenCredential_) autorise l’utilisation d’un URI KeyIdentifier et d’un TokenCredential pour permettre au système de protection des données d’utiliser le coffre de clés.</span><span class="sxs-lookup"><span data-stu-id="62d65-136">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, Uri, TokenCredential)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionkeyvaultkeybuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionKeyVaultKeyBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_Uri_Azure_Core_TokenCredential_) permits the use of a keyIdentifier Uri and a tokenCredential to enable the data protection system to use the key vault.</span></span>
* <span data-ttu-id="62d65-137">[ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, String, IKeyEncryptionKeyResolver)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionkeyvaultkeybuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionKeyVaultKeyBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_Uri_Azure_Core_TokenCredential_) autorise l’utilisation d’une chaîne KeyIdentifier et IKeyEncryptionKeyResolver pour permettre au système de protection des données d’utiliser le coffre de clés.</span><span class="sxs-lookup"><span data-stu-id="62d65-137">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, string, IKeyEncryptionKeyResolver)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionkeyvaultkeybuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionKeyVaultKeyBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_Uri_Azure_Core_TokenCredential_) permits the use of a keyIdentifier string and IKeyEncryptionKeyResolver to enable the data protection system to use the key vault.</span></span>

<span data-ttu-id="62d65-138">Si l’application utilise les packages Azure précédents ( [`Microsoft.AspNetCore.DataProtection.AzureStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage) et [`Microsoft.AspNetCore.DataProtection.AzureKeyVault`](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureKeyVault) ) et une combinaison de Azure Key Vault et de stockage Azure pour stocker et protéger des clés, <xref:System.UriFormatException?displayProperty=nameWithType> est levée si l’objet BLOB pour le stockage de clés n’existe pas.</span><span class="sxs-lookup"><span data-stu-id="62d65-138">If the app uses the prior Azure packages ([`Microsoft.AspNetCore.DataProtection.AzureStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage) and [`Microsoft.AspNetCore.DataProtection.AzureKeyVault`](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureKeyVault)) and a combination of Azure Key Vault and Azure Storage to store and protect keys, <xref:System.UriFormatException?displayProperty=nameWithType> is thrown if the blob for key storage doesn't exist.</span></span> <span data-ttu-id="62d65-139">L’objet BLOB peut être créé manuellement avant d’exécuter l’application dans le Portail Azure, ou utilisez la procédure suivante :</span><span class="sxs-lookup"><span data-stu-id="62d65-139">The blob can be manually created ahead of running the app in the Azure portal, or use the following procedure:</span></span>

1. <span data-ttu-id="62d65-140">Supprimez l’appel à `ProtectKeysWithAzureKeyVault` pour la première exécution afin de créer l’objet BLOB sur place.</span><span class="sxs-lookup"><span data-stu-id="62d65-140">Remove the call to `ProtectKeysWithAzureKeyVault` for the first run to create the blob in place.</span></span>
1. <span data-ttu-id="62d65-141">Ajoutez l’appel à `ProtectKeysWithAzureKeyVault` pour les exécutions suivantes.</span><span class="sxs-lookup"><span data-stu-id="62d65-141">Add the call to `ProtectKeysWithAzureKeyVault` for subsequent runs.</span></span>

<span data-ttu-id="62d65-142">La suppression `ProtectKeysWithAzureKeyVault` de la première exécution est recommandée, car elle permet de s’assurer que le fichier est créé avec le schéma et les valeurs appropriés.</span><span class="sxs-lookup"><span data-stu-id="62d65-142">Removing `ProtectKeysWithAzureKeyVault` for the first run is advised, as it ensures that the file is created with the proper schema and values in place.</span></span> 

<span data-ttu-id="62d65-143">Nous vous recommandons d’effectuer la mise à niveau vers les packages [Azure. extensions. AspNetCore. dataprotection. blobs](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Blobs) et [Azure. extensions. AspNetCore. dataprotection. Keys](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Keys) , car l’API fournie crée automatiquement l’objet BLOB s’il n’existe pas.</span><span class="sxs-lookup"><span data-stu-id="62d65-143">We recommended upgrading to the [Azure.Extensions.AspNetCore.DataProtection.Blobs](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Blobs) and [Azure.Extensions.AspNetCore.DataProtection.Keys](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Keys) packages because the API provided automatically creates the blob if it doesn't exist.</span></span>

```csharp
services.AddDataProtection()
    //This blob must already exist before the application is run
    .PersistKeysToAzureBlobStorage("<storage account connection string", "<key store container name>", "<key store blob name>")
    //Removing this line below for an initial run will ensure the file is created correctly
    .ProtectKeysWithAzureKeyVault(new Uri("<keyIdentifier>"), new DefaultAzureCredential());
```

::: moniker-end

## <a name="persistkeystofilesystem"></a><span data-ttu-id="62d65-144">PersistKeysToFileSystem</span><span class="sxs-lookup"><span data-stu-id="62d65-144">PersistKeysToFileSystem</span></span>

<span data-ttu-id="62d65-145">Pour stocker des clés sur un partage UNC au lieu de l’emplacement *% LocalAppData%* par défaut, configurez le système avec [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem):</span><span class="sxs-lookup"><span data-stu-id="62d65-145">To store keys on a UNC share instead of at the *%LOCALAPPDATA%* default location, configure the system with [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"));
}
```

> [!WARNING]
> <span data-ttu-id="62d65-146">Si vous modifiez l’emplacement de persistance des clés, le système ne chiffre plus automatiquement les clés au repos, car il ne sait pas si DPAPI est un mécanisme de chiffrement approprié.</span><span class="sxs-lookup"><span data-stu-id="62d65-146">If you change the key persistence location, the system no longer automatically encrypts keys at rest, since it doesn't know whether DPAPI is an appropriate encryption mechanism.</span></span>

## <a name="protectkeyswith"></a><span data-ttu-id="62d65-147">ProtectKeysWith\*</span><span class="sxs-lookup"><span data-stu-id="62d65-147">ProtectKeysWith\*</span></span>

<span data-ttu-id="62d65-148">Vous pouvez configurer le système pour protéger les clés au repos en appelant l’une des API de configuration [ProtectKeysWith \* ](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) .</span><span class="sxs-lookup"><span data-stu-id="62d65-148">You can configure the system to protect keys at rest by calling any of the [ProtectKeysWith\*](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) configuration APIs.</span></span> <span data-ttu-id="62d65-149">Prenons l’exemple ci-dessous, qui stocke les clés sur un partage UNC et chiffre ces clés au repos avec un certificat X. 509 spécifique :</span><span class="sxs-lookup"><span data-stu-id="62d65-149">Consider the example below, which stores keys on a UNC share and encrypts those keys at rest with a specific X.509 certificate:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate("thumbprint");
}
```

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="62d65-150">Dans ASP.NET Core 2,1 ou version ultérieure, vous pouvez fournir un [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) à [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate), tel qu’un certificat chargé à partir d’un fichier :</span><span class="sxs-lookup"><span data-stu-id="62d65-150">In ASP.NET Core 2.1 or later, you can provide an [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) to [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate), such as a certificate loaded from a file:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate(
            new X509Certificate2("certificate.pfx", "password"));
}
```

::: moniker-end

<span data-ttu-id="62d65-151">Pour obtenir plus d’exemples et de discussion sur les mécanismes de chiffrement à clé intégrés, consultez [chiffrement de clé au repos](xref:security/data-protection/implementation/key-encryption-at-rest) .</span><span class="sxs-lookup"><span data-stu-id="62d65-151">See [Key Encryption At Rest](xref:security/data-protection/implementation/key-encryption-at-rest) for more examples and discussion on the built-in key encryption mechanisms.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="unprotectkeyswithanycertificate"></a><span data-ttu-id="62d65-152">UnprotectKeysWithAnyCertificate</span><span class="sxs-lookup"><span data-stu-id="62d65-152">UnprotectKeysWithAnyCertificate</span></span>

<span data-ttu-id="62d65-153">Dans ASP.NET Core 2,1 ou version ultérieure, vous pouvez faire pivoter des certificats et déchiffrer des clés au repos à l’aide d’un tableau de certificats [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) avec [UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate):</span><span class="sxs-lookup"><span data-stu-id="62d65-153">In ASP.NET Core 2.1 or later, you can rotate certificates and decrypt keys at rest using an array of [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) certificates with [UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate(
            new X509Certificate2("certificate.pfx", "password"));
        .UnprotectKeysWithAnyCertificate(
            new X509Certificate2("certificate_old_1.pfx", "password_1"),
            new X509Certificate2("certificate_old_2.pfx", "password_2"));
}
```

::: moniker-end

## <a name="setdefaultkeylifetime"></a><span data-ttu-id="62d65-154">SetDefaultKeyLifetime</span><span class="sxs-lookup"><span data-stu-id="62d65-154">SetDefaultKeyLifetime</span></span>

<span data-ttu-id="62d65-155">Pour configurer le système pour qu’il utilise une durée de vie de clé de 14 jours au lieu des jours par défaut de 90, utilisez [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime):</span><span class="sxs-lookup"><span data-stu-id="62d65-155">To configure the system to use a key lifetime of 14 days instead of the default 90 days, use [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
}
```

## <a name="setapplicationname"></a><span data-ttu-id="62d65-156">SetApplicationName</span><span class="sxs-lookup"><span data-stu-id="62d65-156">SetApplicationName</span></span>

<span data-ttu-id="62d65-157">Par défaut, le système de protection des données isole les applications les unes des autres en fonction de leurs chemins [racine de contenu](xref:fundamentals/index#content-root) , même s’ils partagent le même référentiel de clé physique.</span><span class="sxs-lookup"><span data-stu-id="62d65-157">By default, the Data Protection system isolates apps from one another based on their [content root](xref:fundamentals/index#content-root) paths, even if they're sharing the same physical key repository.</span></span> <span data-ttu-id="62d65-158">Cela empêche les applications de comprendre les charges utiles protégées par les autres.</span><span class="sxs-lookup"><span data-stu-id="62d65-158">This prevents the apps from understanding each other's protected payloads.</span></span>

<span data-ttu-id="62d65-159">Pour partager des charges utiles protégées entre les applications :</span><span class="sxs-lookup"><span data-stu-id="62d65-159">To share protected payloads among apps:</span></span>

* <span data-ttu-id="62d65-160">Configurez <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> dans chaque application avec la même valeur.</span><span class="sxs-lookup"><span data-stu-id="62d65-160">Configure <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> in each app with the same value.</span></span>
* <span data-ttu-id="62d65-161">Utilisez la même version de la pile de l’API de protection des données dans les applications.</span><span class="sxs-lookup"><span data-stu-id="62d65-161">Use the same version of the Data Protection API stack across the apps.</span></span> <span data-ttu-id="62d65-162">Effectuez l' **une** des opérations suivantes dans les fichiers projet des applications :</span><span class="sxs-lookup"><span data-stu-id="62d65-162">Perform **either** of the following in the apps' project files:</span></span>
  * <span data-ttu-id="62d65-163">Référencez la même version du Framework partagé par le biais du sous- [package Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="62d65-163">Reference the same shared framework version via the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="62d65-164">Référencez la même version du [package de protection des données](xref:security/data-protection/introduction#package-layout) .</span><span class="sxs-lookup"><span data-stu-id="62d65-164">Reference the same [Data Protection package](xref:security/data-protection/introduction#package-layout) version.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetApplicationName("shared app name");
}
```

## <a name="disableautomatickeygeneration"></a><span data-ttu-id="62d65-165">DisableAutomaticKeyGeneration</span><span class="sxs-lookup"><span data-stu-id="62d65-165">DisableAutomaticKeyGeneration</span></span>

<span data-ttu-id="62d65-166">Vous pouvez avoir un scénario dans lequel vous ne voulez pas qu’une application passe automatiquement des clés (créer de nouvelles clés), car elles approchent de l’expiration.</span><span class="sxs-lookup"><span data-stu-id="62d65-166">You may have a scenario where you don't want an app to automatically roll keys (create new keys) as they approach expiration.</span></span> <span data-ttu-id="62d65-167">Par exemple, les applications peuvent être configurées dans une relation principale/secondaire, où seule l’application principale est responsable des problèmes de gestion des clés et les applications secondaires disposent simplement d’une vue en lecture seule de l’anneau de clé.</span><span class="sxs-lookup"><span data-stu-id="62d65-167">One example of this might be apps set up in a primary/secondary relationship, where only the primary app is responsible for key management concerns and secondary apps simply have a read-only view of the key ring.</span></span> <span data-ttu-id="62d65-168">Les applications secondaires peuvent être configurées pour traiter l’anneau clé en lecture seule en configurant le système avec <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*> :</span><span class="sxs-lookup"><span data-stu-id="62d65-168">The secondary apps can be configured to treat the key ring as read-only by configuring the system with <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .DisableAutomaticKeyGeneration();
}
```

## <a name="per-application-isolation"></a><span data-ttu-id="62d65-169">Isolation par application</span><span class="sxs-lookup"><span data-stu-id="62d65-169">Per-application isolation</span></span>

<span data-ttu-id="62d65-170">Lorsque le système de protection des données est fourni par un hôte ASP.NET Core, il isole automatiquement les applications les unes des autres, même si celles-ci s’exécutent sous le même compte de processus de travail et utilisent le même support de gestion de la clé principale.</span><span class="sxs-lookup"><span data-stu-id="62d65-170">When the Data Protection system is provided by an ASP.NET Core host, it automatically isolates apps from one another, even if those apps are running under the same worker process account and are using the same master keying material.</span></span> <span data-ttu-id="62d65-171">Cela est quelque peu similaire au modificateur IsolateApps de l’élément System. Web `<machineKey>` .</span><span class="sxs-lookup"><span data-stu-id="62d65-171">This is somewhat similar to the IsolateApps modifier from System.Web's `<machineKey>` element.</span></span>

<span data-ttu-id="62d65-172">Le mécanisme d’isolation considère chaque application sur l’ordinateur local comme un locataire unique, de sorte que la <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> racine d’une application donnée comprend automatiquement l’ID d’application en tant que discriminateur.</span><span class="sxs-lookup"><span data-stu-id="62d65-172">The isolation mechanism works by considering each app on the local machine as a unique tenant, thus the <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> rooted for any given app automatically includes the app ID as a discriminator.</span></span> <span data-ttu-id="62d65-173">L’ID unique de l’application est le chemin d’accès physique de l’application :</span><span class="sxs-lookup"><span data-stu-id="62d65-173">The app's unique ID is the app's physical path:</span></span>

* <span data-ttu-id="62d65-174">Pour les applications hébergées dans IIS, l’ID unique est le chemin d’accès physique IIS de l’application.</span><span class="sxs-lookup"><span data-stu-id="62d65-174">For apps hosted in IIS, the unique ID is the IIS physical path of the app.</span></span> <span data-ttu-id="62d65-175">Si une application est déployée dans un environnement de batterie de serveurs Web, cette valeur est stable en supposant que les environnements IIS sont configurés de façon similaire sur tous les ordinateurs de la batterie de serveurs Web.</span><span class="sxs-lookup"><span data-stu-id="62d65-175">If an app is deployed in a web farm environment, this value is stable assuming that the IIS environments are configured similarly across all machines in the web farm.</span></span>
* <span data-ttu-id="62d65-176">Pour les applications auto-hébergées exécutées sur le [serveur Kestrel](xref:fundamentals/servers/index#kestrel), l’ID unique est le chemin d’accès physique à l’application sur le disque.</span><span class="sxs-lookup"><span data-stu-id="62d65-176">For self-hosted apps running on the [Kestrel server](xref:fundamentals/servers/index#kestrel), the unique ID is the physical path to the app on disk.</span></span>

<span data-ttu-id="62d65-177">L’identificateur unique est conçu pour survivre aux réinitialisations &mdash; de l’application individuelle et de la machine elle-même.</span><span class="sxs-lookup"><span data-stu-id="62d65-177">The unique identifier is designed to survive resets&mdash;both of the individual app and of the machine itself.</span></span>

<span data-ttu-id="62d65-178">Ce mécanisme d’isolation suppose que les applications ne sont pas malveillantes.</span><span class="sxs-lookup"><span data-stu-id="62d65-178">This isolation mechanism assumes that the apps are not malicious.</span></span> <span data-ttu-id="62d65-179">Une application malveillante peut toujours avoir un impact sur les autres applications qui s’exécutent sous le même compte de processus de travail.</span><span class="sxs-lookup"><span data-stu-id="62d65-179">A malicious app can always impact any other app running under the same worker process account.</span></span> <span data-ttu-id="62d65-180">Dans un environnement d’hébergement partagé où les applications sont mutuellement non approuvées, le fournisseur d’hébergement doit prendre des mesures pour garantir l’isolement au niveau du système d’exploitation entre les applications, y compris la séparation des dépôts de clé sous-jacents des applications.</span><span class="sxs-lookup"><span data-stu-id="62d65-180">In a shared hosting environment where apps are mutually untrusted, the hosting provider should take steps to ensure OS-level isolation between apps, including separating the apps' underlying key repositories.</span></span>

<span data-ttu-id="62d65-181">Si le système de protection des données n’est pas fourni par un hôte ASP.NET Core (par exemple, si vous l’instanciez via le `DataProtectionProvider` type concret), l’isolation des applications est désactivée par défaut.</span><span class="sxs-lookup"><span data-stu-id="62d65-181">If the Data Protection system isn't provided by an ASP.NET Core host (for example, if you instantiate it via the `DataProtectionProvider` concrete type) app isolation is disabled by default.</span></span> <span data-ttu-id="62d65-182">Lorsque l’isolation des applications est désactivée, toutes les applications sauvegardées par le même support de génération de clé peuvent partager des charges utiles tant qu’elles fournissent les [objectifs](xref:security/data-protection/consumer-apis/purpose-strings)appropriés.</span><span class="sxs-lookup"><span data-stu-id="62d65-182">When app isolation is disabled, all apps backed by the same keying material can share payloads as long as they provide the appropriate [purposes](xref:security/data-protection/consumer-apis/purpose-strings).</span></span> <span data-ttu-id="62d65-183">Pour fournir l’isolation des applications dans cet environnement, appelez la méthode [SetApplicationName](#setapplicationname) sur l’objet de configuration et fournissez un nom unique pour chaque application.</span><span class="sxs-lookup"><span data-stu-id="62d65-183">To provide app isolation in this environment, call the [SetApplicationName](#setapplicationname) method on the configuration object and provide a unique name for each app.</span></span>

## <a name="changing-algorithms-with-usecryptographicalgorithms"></a><span data-ttu-id="62d65-184">Modification des algorithmes avec UseCryptographicAlgorithms</span><span class="sxs-lookup"><span data-stu-id="62d65-184">Changing algorithms with UseCryptographicAlgorithms</span></span>

<span data-ttu-id="62d65-185">La pile de protection des données vous permet de modifier l’algorithme par défaut utilisé par les clés nouvellement générées.</span><span class="sxs-lookup"><span data-stu-id="62d65-185">The Data Protection stack allows you to change the default algorithm used by newly-generated keys.</span></span> <span data-ttu-id="62d65-186">La façon la plus simple de procéder consiste à appeler [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) à partir du rappel de configuration :</span><span class="sxs-lookup"><span data-stu-id="62d65-186">The simplest way to do this is to call [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) from the configuration callback:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(
        new AuthenticatedEncryptorConfiguration()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(
        new AuthenticatedEncryptionSettings()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

::: moniker-end

<span data-ttu-id="62d65-187">Le EncryptionAlgorithm par défaut est AES-256-CBC et le ValidationAlgorithm par défaut est HMACSHA256.</span><span class="sxs-lookup"><span data-stu-id="62d65-187">The default EncryptionAlgorithm is AES-256-CBC, and the default ValidationAlgorithm is HMACSHA256.</span></span> <span data-ttu-id="62d65-188">La stratégie par défaut peut être définie par un administrateur système via une stratégie à l’ensemble de l' [ordinateur](xref:security/data-protection/configuration/machine-wide-policy), mais un appel explicite à `UseCryptographicAlgorithms` remplace la stratégie par défaut.</span><span class="sxs-lookup"><span data-stu-id="62d65-188">The default policy can be set by a system administrator via a [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy), but an explicit call to `UseCryptographicAlgorithms` overrides the default policy.</span></span>

<span data-ttu-id="62d65-189">L’appel de `UseCryptographicAlgorithms` vous permet de spécifier l’algorithme souhaité à partir d’une liste prédéfinie prédéfinie.</span><span class="sxs-lookup"><span data-stu-id="62d65-189">Calling `UseCryptographicAlgorithms` allows you to specify the desired algorithm from a predefined built-in list.</span></span> <span data-ttu-id="62d65-190">Vous n’avez pas besoin de vous soucier de l’implémentation de l’algorithme.</span><span class="sxs-lookup"><span data-stu-id="62d65-190">You don't need to worry about the implementation of the algorithm.</span></span> <span data-ttu-id="62d65-191">Dans le scénario ci-dessus, le système de protection des données tente d’utiliser l’implémentation CNG d’AES s’il s’exécute sur Windows.</span><span class="sxs-lookup"><span data-stu-id="62d65-191">In the scenario above, the Data Protection system attempts to use the CNG implementation of AES if running on Windows.</span></span> <span data-ttu-id="62d65-192">Dans le cas contraire, elle revient à la classe gérée [System. Security. Cryptography. AES](/dotnet/api/system.security.cryptography.aes) .</span><span class="sxs-lookup"><span data-stu-id="62d65-192">Otherwise, it falls back to the managed [System.Security.Cryptography.Aes](/dotnet/api/system.security.cryptography.aes) class.</span></span>

<span data-ttu-id="62d65-193">Vous pouvez spécifier manuellement une implémentation à l’aide d’un appel à [UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms).</span><span class="sxs-lookup"><span data-stu-id="62d65-193">You can manually specify an implementation via a call to [UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms).</span></span>

> [!TIP]
> <span data-ttu-id="62d65-194">La modification des algorithmes n’affecte pas les clés existantes dans l’anneau de clé.</span><span class="sxs-lookup"><span data-stu-id="62d65-194">Changing algorithms doesn't affect existing keys in the key ring.</span></span> <span data-ttu-id="62d65-195">Il affecte uniquement les clés nouvellement générées.</span><span class="sxs-lookup"><span data-stu-id="62d65-195">It only affects newly-generated keys.</span></span>

### <a name="specifying-custom-managed-algorithms"></a><span data-ttu-id="62d65-196">Spécification d’algorithmes managés personnalisés</span><span class="sxs-lookup"><span data-stu-id="62d65-196">Specifying custom managed algorithms</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="62d65-197">Pour spécifier des algorithmes managés personnalisés, créez une instance [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) qui pointe vers les types d’implémentation :</span><span class="sxs-lookup"><span data-stu-id="62d65-197">To specify custom managed algorithms, create a [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) instance that points to the implementation types:</span></span>

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new ManagedAuthenticatedEncryptorConfiguration()
    {
        // A type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // A type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="62d65-198">Pour spécifier des algorithmes managés personnalisés, créez une instance [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) qui pointe vers les types d’implémentation :</span><span class="sxs-lookup"><span data-stu-id="62d65-198">To specify custom managed algorithms, create a [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) instance that points to the implementation types:</span></span>

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new ManagedAuthenticatedEncryptionSettings()
    {
        // A type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // A type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

::: moniker-end

<span data-ttu-id="62d65-199">En général \* , les propriétés de type doivent pointer vers concrète, instanciable (via une implémentation de constructeur sans paramètre public) de [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) et [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm), bien que les cas spéciaux du système soient des valeurs comme `typeof(Aes)` pour des raisons pratiques.</span><span class="sxs-lookup"><span data-stu-id="62d65-199">Generally the \*Type properties must point to concrete, instantiable (via a public parameterless ctor) implementations of [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) and [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm), though the system special-cases some values like `typeof(Aes)` for convenience.</span></span>

> [!NOTE]
> <span data-ttu-id="62d65-200">L’SymmetricAlgorithm doit avoir une longueur de clé de ≥ 128 bits et une taille de bloc de ≥ 64 bits, et doit prendre en charge le chiffrement en mode CBC avec un remplissage de #7 PKCS.</span><span class="sxs-lookup"><span data-stu-id="62d65-200">The SymmetricAlgorithm must have a key length of ≥ 128 bits and a block size of ≥ 64 bits, and it must support CBC-mode encryption with PKCS #7 padding.</span></span> <span data-ttu-id="62d65-201">L’opération KeyedHashAlgorithm doit avoir une taille Digest de >= 128 bits, et elle doit prendre en charge des clés de longueur égale à la longueur Digest de l’algorithme de hachage.</span><span class="sxs-lookup"><span data-stu-id="62d65-201">The KeyedHashAlgorithm must have a digest size of >= 128 bits, and it must support keys of length equal to the hash algorithm's digest length.</span></span> <span data-ttu-id="62d65-202">KeyedHashAlgorithm n’est pas strictement requis pour être HMAC.</span><span class="sxs-lookup"><span data-stu-id="62d65-202">The KeyedHashAlgorithm isn't strictly required to be HMAC.</span></span>

### <a name="specifying-custom-windows-cng-algorithms"></a><span data-ttu-id="62d65-203">Spécification d’algorithmes CNG Windows personnalisés</span><span class="sxs-lookup"><span data-stu-id="62d65-203">Specifying custom Windows CNG algorithms</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="62d65-204">Pour spécifier un algorithme CNG Windows personnalisé à l’aide du chiffrement en mode CBC avec validation HMAC, créez une instance [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) qui contient les informations algorithmiques :</span><span class="sxs-lookup"><span data-stu-id="62d65-204">To specify a custom Windows CNG algorithm using CBC-mode encryption with HMAC validation, create a [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) instance that contains the algorithmic information:</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngCbcAuthenticatedEncryptorConfiguration()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // Passed to BCryptOpenAlgorithmProvider
        HashAlgorithm = "SHA256",
        HashAlgorithmProvider = null
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="62d65-205">Pour spécifier un algorithme CNG Windows personnalisé à l’aide du chiffrement en mode CBC avec validation HMAC, créez une instance [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) qui contient les informations algorithmiques :</span><span class="sxs-lookup"><span data-stu-id="62d65-205">To specify a custom Windows CNG algorithm using CBC-mode encryption with HMAC validation, create a [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) instance that contains the algorithmic information:</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngCbcAuthenticatedEncryptionSettings()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // Passed to BCryptOpenAlgorithmProvider
        HashAlgorithm = "SHA256",
        HashAlgorithmProvider = null
    });
```

::: moniker-end

> [!NOTE]
> <span data-ttu-id="62d65-206">L’algorithme de chiffrement par bloc symétrique doit avoir une longueur de clé de >= 128 bits, une taille de bloc de >= 64 bits et doit prendre en charge le chiffrement en mode CBC avec un remplissage de #7 PKCS.</span><span class="sxs-lookup"><span data-stu-id="62d65-206">The symmetric block cipher algorithm must have a key length of >= 128 bits, a block size of >= 64 bits, and it must support CBC-mode encryption with PKCS #7 padding.</span></span> <span data-ttu-id="62d65-207">L’algorithme de hachage doit avoir une taille Digest de >= 128 bits et doit prendre en charge l’ouverture avec l’indicateur de l' \_ \_ indicateur HMAC de handle de BCRYPT \_ \_ .</span><span class="sxs-lookup"><span data-stu-id="62d65-207">The hash algorithm must have a digest size of >= 128 bits and must support being opened with the BCRYPT\_ALG\_HANDLE\_HMAC\_FLAG flag.</span></span> <span data-ttu-id="62d65-208">Les \* Propriétés du fournisseur peuvent avoir la valeur null pour utiliser le fournisseur par défaut pour l’algorithme spécifié.</span><span class="sxs-lookup"><span data-stu-id="62d65-208">The \*Provider properties can be set to null to use the default provider for the specified algorithm.</span></span> <span data-ttu-id="62d65-209">Pour plus d’informations, consultez la documentation de [BCryptOpenAlgorithmProvider](/windows/win32/api/bcrypt/nf-bcrypt-bcryptopenalgorithmprovider) .</span><span class="sxs-lookup"><span data-stu-id="62d65-209">See the [BCryptOpenAlgorithmProvider](/windows/win32/api/bcrypt/nf-bcrypt-bcryptopenalgorithmprovider) documentation for more information.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="62d65-210">Pour spécifier un algorithme CNG Windows personnalisé à l’aide du chiffrement du mode Galois/Counter avec validation, créez une instance [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) qui contient les informations algorithmiques :</span><span class="sxs-lookup"><span data-stu-id="62d65-210">To specify a custom Windows CNG algorithm using Galois/Counter Mode encryption with validation, create a [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) instance that contains the algorithmic information:</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngGcmAuthenticatedEncryptorConfiguration()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="62d65-211">Pour spécifier un algorithme CNG Windows personnalisé à l’aide du chiffrement du mode Galois/Counter avec validation, créez une instance [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) qui contient les informations algorithmiques :</span><span class="sxs-lookup"><span data-stu-id="62d65-211">To specify a custom Windows CNG algorithm using Galois/Counter Mode encryption with validation, create a [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) instance that contains the algorithmic information:</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngGcmAuthenticatedEncryptionSettings()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

::: moniker-end

> [!NOTE]
> <span data-ttu-id="62d65-212">L’algorithme de chiffrement par bloc symétrique doit avoir une longueur de clé de >= 128 bits, une taille de bloc de 128 bits exactement et le chiffrement GCM doit être pris en charge.</span><span class="sxs-lookup"><span data-stu-id="62d65-212">The symmetric block cipher algorithm must have a key length of >= 128 bits, a block size of exactly 128 bits, and it must support GCM encryption.</span></span> <span data-ttu-id="62d65-213">Vous pouvez définir la propriété [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) sur la valeur null pour utiliser le fournisseur par défaut pour l’algorithme spécifié.</span><span class="sxs-lookup"><span data-stu-id="62d65-213">You can set the [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) property to null to use the default provider for the specified algorithm.</span></span> <span data-ttu-id="62d65-214">Pour plus d’informations, consultez la documentation de [BCryptOpenAlgorithmProvider](/windows/win32/api/bcrypt/nf-bcrypt-bcryptopenalgorithmprovider) .</span><span class="sxs-lookup"><span data-stu-id="62d65-214">See the [BCryptOpenAlgorithmProvider](/windows/win32/api/bcrypt/nf-bcrypt-bcryptopenalgorithmprovider) documentation for more information.</span></span>

### <a name="specifying-other-custom-algorithms"></a><span data-ttu-id="62d65-215">Spécification d’autres algorithmes personnalisés</span><span class="sxs-lookup"><span data-stu-id="62d65-215">Specifying other custom algorithms</span></span>

<span data-ttu-id="62d65-216">Bien qu’elles ne soient pas exposées comme API de première classe, le système de protection des données est suffisamment extensible pour permettre la spécification de presque n’importe quel type d’algorithme.</span><span class="sxs-lookup"><span data-stu-id="62d65-216">Though not exposed as a first-class API, the Data Protection system is extensible enough to allow specifying almost any kind of algorithm.</span></span> <span data-ttu-id="62d65-217">Par exemple, il est possible de conserver toutes les clés contenues dans un module de sécurité matériel (HSM) et de fournir une implémentation personnalisée des routines de chiffrement et de déchiffrement de base.</span><span class="sxs-lookup"><span data-stu-id="62d65-217">For example, it's possible to keep all keys contained within a Hardware Security Module (HSM) and to provide a custom implementation of the core encryption and decryption routines.</span></span> <span data-ttu-id="62d65-218">Pour plus d’informations, consultez [IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) dans [Core Cryptography Extensibility](xref:security/data-protection/extensibility/core-crypto) .</span><span class="sxs-lookup"><span data-stu-id="62d65-218">See [IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) in [Core cryptography extensibility](xref:security/data-protection/extensibility/core-crypto) for more information.</span></span>

## <a name="persisting-keys-when-hosting-in-a-docker-container"></a><span data-ttu-id="62d65-219">Persistance des clés lors de l’hébergement dans un conteneur d’ancrage</span><span class="sxs-lookup"><span data-stu-id="62d65-219">Persisting keys when hosting in a Docker container</span></span>

<span data-ttu-id="62d65-220">Lors de l’hébergement dans un conteneur d' [ancrage](/dotnet/standard/microservices-architecture/container-docker-introduction/) , les clés doivent être gérées dans :</span><span class="sxs-lookup"><span data-stu-id="62d65-220">When hosting in a [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) container, keys should be maintained in either:</span></span>

* <span data-ttu-id="62d65-221">Dossier qui est un volume d’ancrage qui persiste au-delà de la durée de vie du conteneur, par exemple un volume partagé ou un volume monté sur un ordinateur hôte.</span><span class="sxs-lookup"><span data-stu-id="62d65-221">A folder that's a Docker volume that persists beyond the container's lifetime, such as a shared volume or a host-mounted volume.</span></span>
* <span data-ttu-id="62d65-222">Fournisseur externe, tel que [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) ou [redims](https://redis.io/).</span><span class="sxs-lookup"><span data-stu-id="62d65-222">An external provider, such as [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) or [Redis](https://redis.io/).</span></span>

## <a name="persisting-keys-with-redis"></a><span data-ttu-id="62d65-223">Persistance des clés avec des ReDim</span><span class="sxs-lookup"><span data-stu-id="62d65-223">Persisting keys with Redis</span></span>

<span data-ttu-id="62d65-224">Seules les versions de ReDim qui prennent en charge la [persistance des données redims](/azure/azure-cache-for-redis/cache-how-to-premium-persistence) doivent être utilisées pour stocker des clés.</span><span class="sxs-lookup"><span data-stu-id="62d65-224">Only Redis versions supporting [Redis Data Persistence](/azure/azure-cache-for-redis/cache-how-to-premium-persistence) should be used to store keys.</span></span> <span data-ttu-id="62d65-225">Le [stockage d’objets BLOB Azure](/azure/storage/blobs/storage-blobs-introduction) est persistant et peut être utilisé pour stocker des clés.</span><span class="sxs-lookup"><span data-stu-id="62d65-225">[Azure Blob storage](/azure/storage/blobs/storage-blobs-introduction) is persistent and can be used to store keys.</span></span> <span data-ttu-id="62d65-226">Pour plus d’informations, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore/issues/13476).</span><span class="sxs-lookup"><span data-stu-id="62d65-226">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/13476).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="62d65-227">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="62d65-227">Additional resources</span></span>

* <xref:security/data-protection/configuration/non-di-scenarios>
* <xref:security/data-protection/configuration/machine-wide-policy>
* <xref:host-and-deploy/web-farm>
* <xref:security/data-protection/implementation/key-storage-providers>
