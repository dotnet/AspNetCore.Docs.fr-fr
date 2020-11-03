---
title: Configurer la protection des données ASP.NET Core
author: rick-anderson
description: Découvrez comment configurer la protection des données dans ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 11/02/2020
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
uid: security/data-protection/configuration/overview
ms.openlocfilehash: 048f6d6d57d3cc5d64004e18b18a3347ee92e450
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234567"
---
# <a name="configure-aspnet-core-data-protection"></a>Configurer la protection des données ASP.NET Core

Lorsque le système de protection des données est initialisé, il applique les [paramètres par défaut](xref:security/data-protection/configuration/default-settings) en fonction de l’environnement opérationnel. Ces paramètres sont généralement appropriés pour les applications qui s’exécutent sur un seul ordinateur. Dans certains cas, un développeur peut souhaiter modifier les paramètres par défaut :

* L’application est répartie sur plusieurs ordinateurs.
* Pour des raisons de conformité.

Pour ces scénarios, le système de protection des données offre une API de configuration riche.

> [!WARNING]
> Comme pour les fichiers de configuration, l’anneau de clé de protection des données doit être protégé à l’aide des autorisations appropriées. Vous pouvez choisir de chiffrer les clés au repos, mais cela n’empêche pas les attaquants de créer de nouvelles clés. Par conséquent, la sécurité de votre application est affectée. L’accès à l’emplacement de stockage configuré avec la protection des données doit être limité à l’application elle-même, comme dans le cas où vous protégeriez les fichiers de configuration. Par exemple, si vous choisissez de stocker votre clé Ring sur disque, utilisez les autorisations du système de fichiers. Assurez-vous que l’identité sous laquelle votre application Web s’exécute dispose de l’accès en lecture, en écriture et en création à ce répertoire. Si vous utilisez le stockage d’objets BLOB Azure, seule l’application Web doit avoir la possibilité de lire, d’écrire ou de créer de nouvelles entrées dans le magasin d’objets BLOB, etc.
>
> La méthode d’extension [AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) retourne un [IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder). `IDataProtectionBuilder` expose les méthodes d’extension que vous pouvez lier pour configurer les options de protection des données.

::: moniker range=">= aspnetcore-3.0"

Les packages NuGet suivants sont requis pour les extensions de protection des données utilisées dans cet article :

* [Azure. extensions. AspNetCore. DataProtection. blob](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Blobs)
* [Azure. extensions. AspNetCore. DataProtection. Keys](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Keys)

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="protectkeyswithazurekeyvault"></a>ProtectKeysWithAzureKeyVault

Connectez-vous à Azure à l’aide de l’interface CLI, par exemple :

```azurecli
az login
``` 

Pour stocker des clés dans [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configurez le système avec [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) dans la `Startup` classe. `blobUriWithSasToken` URI complet dans lequel le fichier de clé doit être stocké. L’URI doit contenir le jeton SAS en tant que paramètre de chaîne de requête :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault(new Uri("<keyIdentifier>"), new DefaultAzureCredential());
}
```

Définissez l’emplacement de stockage de la sonnerie de clé (par exemple, [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)). L’emplacement doit être défini, car l’appel de `ProtectKeysWithAzureKeyVault` implémente un [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) qui désactive les paramètres de protection automatique des données, y compris l’emplacement de stockage de la sonnerie de clé. L’exemple précédent utilise le stockage d’objets BLOB Azure pour conserver l’anneau de clé. Pour plus d’informations, consultez [fournisseurs de stockage de clés : stockage Azure](xref:security/data-protection/implementation/key-storage-providers#azure-storage). Vous pouvez également conserver l’anneau de clé localement avec [PersistKeysToFileSystem](xref:security/data-protection/implementation/key-storage-providers#file-system).

`keyIdentifier`Est l’identificateur de clé du coffre de clés utilisé pour le chiffrement à clé. Par exemple, une clé créée dans Key Vault nommée `dataprotection` dans `contosokeyvault` a l’identificateur de clé `https://contosokeyvault.vault.azure.net/keys/dataprotection/` . Fournissez l’application avec les autorisations de clé de **désencapsulage** et de retour à la **ligne** pour le coffre de clés.

`ProtectKeysWithAzureKeyVault` surcharges

* [ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, Uri, TokenCredential)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionkeyvaultkeybuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionKeyVaultKeyBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_Uri_Azure_Core_TokenCredential_) autorise l’utilisation d’un URI KeyIdentifier et d’un TokenCredential pour permettre au système de protection des données d’utiliser le coffre de clés.
* [ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, String, IKeyEncryptionKeyResolver)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionkeyvaultkeybuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionKeyVaultKeyBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_Uri_Azure_Core_TokenCredential_) autorise l’utilisation d’une chaîne KeyIdentifier et IKeyEncryptionKeyResolver pour permettre au système de protection des données d’utiliser le coffre de clés.

Si l’application utilise les packages Azure précédents ( [`Microsoft.AspNetCore.DataProtection.AzureStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage) et [`Microsoft.AspNetCore.DataProtection.AzureKeyVault`](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureKeyVault) ) et une combinaison de Azure Key Vault et de stockage Azure pour stocker et protéger des clés, <xref:System.UriFormatException?displayProperty=nameWithType> est levée si l’objet BLOB pour le stockage de clés n’existe pas. L’objet BLOB peut être créé manuellement avant d’exécuter l’application dans le Portail Azure, ou utilisez la procédure suivante :

1. Supprimez l’appel à `ProtectKeysWithAzureKeyVault` pour la première exécution afin de créer l’objet BLOB sur place.
1. Ajoutez l’appel à `ProtectKeysWithAzureKeyVault` pour les exécutions suivantes.

La suppression `ProtectKeysWithAzureKeyVault` de la première exécution est recommandée, car elle permet de s’assurer que le fichier est créé avec le schéma et les valeurs appropriés. 

Nous vous recommandons d’effectuer la mise à niveau vers les packages [Azure. extensions. AspNetCore. dataprotection. blobs](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Blobs) et [Azure. extensions. AspNetCore. dataprotection. Keys](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Keys) , car l’API fournie crée automatiquement l’objet BLOB s’il n’existe pas.

```csharp
services.AddDataProtection()
    //This blob must already exist before the application is run
    .PersistKeysToAzureBlobStorage("<storage account connection string", "<key store container name>", "<key store blob name>")
    //Removing this line below for an initial run will ensure the file is created correctly
    .ProtectKeysWithAzureKeyVault(new Uri("<keyIdentifier>"), new DefaultAzureCredential());
```

::: moniker-end

## <a name="persistkeystofilesystem"></a>PersistKeysToFileSystem

Pour stocker des clés sur un partage UNC au lieu de l’emplacement *% LocalAppData%* par défaut, configurez le système avec [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem):

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"));
}
```

> [!WARNING]
> Si vous modifiez l’emplacement de persistance des clés, le système ne chiffre plus automatiquement les clés au repos, car il ne sait pas si DPAPI est un mécanisme de chiffrement approprié.

## <a name="protectkeyswith"></a>ProtectKeysWith\*

Vous pouvez configurer le système pour protéger les clés au repos en appelant l’une des API de configuration [ProtectKeysWith \* ](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) . Prenons l’exemple ci-dessous, qui stocke les clés sur un partage UNC et chiffre ces clés au repos avec un certificat X. 509 spécifique :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate("thumbprint");
}
```

::: moniker range=">= aspnetcore-2.1"

Dans ASP.NET Core 2,1 ou version ultérieure, vous pouvez fournir un [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) à [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate), tel qu’un certificat chargé à partir d’un fichier :

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

Pour obtenir plus d’exemples et de discussion sur les mécanismes de chiffrement à clé intégrés, consultez [chiffrement de clé au repos](xref:security/data-protection/implementation/key-encryption-at-rest) .

::: moniker range=">= aspnetcore-2.1"

## <a name="unprotectkeyswithanycertificate"></a>UnprotectKeysWithAnyCertificate

Dans ASP.NET Core 2,1 ou version ultérieure, vous pouvez faire pivoter des certificats et déchiffrer des clés au repos à l’aide d’un tableau de certificats [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) avec [UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate):

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

## <a name="setdefaultkeylifetime"></a>SetDefaultKeyLifetime

Pour configurer le système pour qu’il utilise une durée de vie de clé de 14 jours au lieu des jours par défaut de 90, utilisez [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime):

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
}
```

## <a name="setapplicationname"></a>SetApplicationName

Par défaut, le système de protection des données isole les applications les unes des autres en fonction de leurs chemins [racine de contenu](xref:fundamentals/index#content-root) , même s’ils partagent le même référentiel de clé physique. Cela empêche les applications de comprendre les charges utiles protégées par les autres.

Pour partager des charges utiles protégées entre les applications :

* Configurez <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> dans chaque application avec la même valeur.
* Utilisez la même version de la pile de l’API de protection des données dans les applications. Effectuez l' **une** des opérations suivantes dans les fichiers projet des applications :
  * Référencez la même version du Framework partagé par le biais du sous- [package Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).
  * Référencez la même version du [package de protection des données](xref:security/data-protection/introduction#package-layout) .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetApplicationName("shared app name");
}
```

## <a name="disableautomatickeygeneration"></a>DisableAutomaticKeyGeneration

Vous pouvez avoir un scénario dans lequel vous ne voulez pas qu’une application passe automatiquement des clés (créer de nouvelles clés), car elles approchent de l’expiration. Par exemple, les applications peuvent être configurées dans une relation principale/secondaire, où seule l’application principale est responsable des problèmes de gestion des clés et les applications secondaires disposent simplement d’une vue en lecture seule de l’anneau de clé. Les applications secondaires peuvent être configurées pour traiter l’anneau clé en lecture seule en configurant le système avec <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*> :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .DisableAutomaticKeyGeneration();
}
```

## <a name="per-application-isolation"></a>Isolation par application

Lorsque le système de protection des données est fourni par un hôte ASP.NET Core, il isole automatiquement les applications les unes des autres, même si celles-ci s’exécutent sous le même compte de processus de travail et utilisent le même support de gestion de la clé principale. Cela est quelque peu similaire au modificateur IsolateApps de l’élément System. Web `<machineKey>` .

Le mécanisme d’isolation considère chaque application sur l’ordinateur local comme un locataire unique, de sorte que la <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> racine d’une application donnée comprend automatiquement l’ID d’application en tant que discriminateur. L’ID unique de l’application est le chemin d’accès physique de l’application :

* Pour les applications hébergées dans IIS, l’ID unique est le chemin d’accès physique IIS de l’application. Si une application est déployée dans un environnement de batterie de serveurs Web, cette valeur est stable en supposant que les environnements IIS sont configurés de façon similaire sur tous les ordinateurs de la batterie de serveurs Web.
* Pour les applications auto-hébergées exécutées sur le [serveur Kestrel](xref:fundamentals/servers/index#kestrel), l’ID unique est le chemin d’accès physique à l’application sur le disque.

L’identificateur unique est conçu pour survivre aux réinitialisations &mdash; de l’application individuelle et de la machine elle-même.

Ce mécanisme d’isolation suppose que les applications ne sont pas malveillantes. Une application malveillante peut toujours avoir un impact sur les autres applications qui s’exécutent sous le même compte de processus de travail. Dans un environnement d’hébergement partagé où les applications sont mutuellement non approuvées, le fournisseur d’hébergement doit prendre des mesures pour garantir l’isolement au niveau du système d’exploitation entre les applications, y compris la séparation des dépôts de clé sous-jacents des applications.

Si le système de protection des données n’est pas fourni par un hôte ASP.NET Core (par exemple, si vous l’instanciez via le `DataProtectionProvider` type concret), l’isolation des applications est désactivée par défaut. Lorsque l’isolation des applications est désactivée, toutes les applications sauvegardées par le même support de génération de clé peuvent partager des charges utiles tant qu’elles fournissent les [objectifs](xref:security/data-protection/consumer-apis/purpose-strings)appropriés. Pour fournir l’isolation des applications dans cet environnement, appelez la méthode [SetApplicationName](#setapplicationname) sur l’objet de configuration et fournissez un nom unique pour chaque application.

## <a name="changing-algorithms-with-usecryptographicalgorithms"></a>Modification des algorithmes avec UseCryptographicAlgorithms

La pile de protection des données vous permet de modifier l’algorithme par défaut utilisé par les clés nouvellement générées. La façon la plus simple de procéder consiste à appeler [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) à partir du rappel de configuration :

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

Le EncryptionAlgorithm par défaut est AES-256-CBC et le ValidationAlgorithm par défaut est HMACSHA256. La stratégie par défaut peut être définie par un administrateur système via une stratégie à l’ensemble de l' [ordinateur](xref:security/data-protection/configuration/machine-wide-policy), mais un appel explicite à `UseCryptographicAlgorithms` remplace la stratégie par défaut.

L’appel de `UseCryptographicAlgorithms` vous permet de spécifier l’algorithme souhaité à partir d’une liste prédéfinie prédéfinie. Vous n’avez pas besoin de vous soucier de l’implémentation de l’algorithme. Dans le scénario ci-dessus, le système de protection des données tente d’utiliser l’implémentation CNG d’AES s’il s’exécute sur Windows. Dans le cas contraire, elle revient à la classe gérée [System. Security. Cryptography. AES](/dotnet/api/system.security.cryptography.aes) .

Vous pouvez spécifier manuellement une implémentation à l’aide d’un appel à [UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms).

> [!TIP]
> La modification des algorithmes n’affecte pas les clés existantes dans l’anneau de clé. Il affecte uniquement les clés nouvellement générées.

### <a name="specifying-custom-managed-algorithms"></a>Spécification d’algorithmes managés personnalisés

::: moniker range=">= aspnetcore-2.0"

Pour spécifier des algorithmes managés personnalisés, créez une instance [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) qui pointe vers les types d’implémentation :

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

Pour spécifier des algorithmes managés personnalisés, créez une instance [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) qui pointe vers les types d’implémentation :

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

En général \* , les propriétés de type doivent pointer vers concrète, instanciable (via une implémentation de constructeur sans paramètre public) de [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) et [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm), bien que les cas spéciaux du système soient des valeurs comme `typeof(Aes)` pour des raisons pratiques.

> [!NOTE]
> L’SymmetricAlgorithm doit avoir une longueur de clé de ≥ 128 bits et une taille de bloc de ≥ 64 bits, et doit prendre en charge le chiffrement en mode CBC avec un remplissage de #7 PKCS. L’opération KeyedHashAlgorithm doit avoir une taille Digest de >= 128 bits, et elle doit prendre en charge des clés de longueur égale à la longueur Digest de l’algorithme de hachage. KeyedHashAlgorithm n’est pas strictement requis pour être HMAC.

### <a name="specifying-custom-windows-cng-algorithms"></a>Spécification d’algorithmes CNG Windows personnalisés

::: moniker range=">= aspnetcore-2.0"

Pour spécifier un algorithme CNG Windows personnalisé à l’aide du chiffrement en mode CBC avec validation HMAC, créez une instance [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) qui contient les informations algorithmiques :

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

Pour spécifier un algorithme CNG Windows personnalisé à l’aide du chiffrement en mode CBC avec validation HMAC, créez une instance [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) qui contient les informations algorithmiques :

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
> L’algorithme de chiffrement par bloc symétrique doit avoir une longueur de clé de >= 128 bits, une taille de bloc de >= 64 bits et doit prendre en charge le chiffrement en mode CBC avec un remplissage de #7 PKCS. L’algorithme de hachage doit avoir une taille Digest de >= 128 bits et doit prendre en charge l’ouverture avec l’indicateur de l' \_ \_ indicateur HMAC de handle de BCRYPT \_ \_ . Les \* Propriétés du fournisseur peuvent avoir la valeur null pour utiliser le fournisseur par défaut pour l’algorithme spécifié. Pour plus d’informations, consultez la documentation de [BCryptOpenAlgorithmProvider](/windows/win32/api/bcrypt/nf-bcrypt-bcryptopenalgorithmprovider) .

::: moniker range=">= aspnetcore-2.0"

Pour spécifier un algorithme CNG Windows personnalisé à l’aide du chiffrement du mode Galois/Counter avec validation, créez une instance [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) qui contient les informations algorithmiques :

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

Pour spécifier un algorithme CNG Windows personnalisé à l’aide du chiffrement du mode Galois/Counter avec validation, créez une instance [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) qui contient les informations algorithmiques :

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
> L’algorithme de chiffrement par bloc symétrique doit avoir une longueur de clé de >= 128 bits, une taille de bloc de 128 bits exactement et le chiffrement GCM doit être pris en charge. Vous pouvez définir la propriété [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) sur la valeur null pour utiliser le fournisseur par défaut pour l’algorithme spécifié. Pour plus d’informations, consultez la documentation de [BCryptOpenAlgorithmProvider](/windows/win32/api/bcrypt/nf-bcrypt-bcryptopenalgorithmprovider) .

### <a name="specifying-other-custom-algorithms"></a>Spécification d’autres algorithmes personnalisés

Bien qu’elles ne soient pas exposées comme API de première classe, le système de protection des données est suffisamment extensible pour permettre la spécification de presque n’importe quel type d’algorithme. Par exemple, il est possible de conserver toutes les clés contenues dans un module de sécurité matériel (HSM) et de fournir une implémentation personnalisée des routines de chiffrement et de déchiffrement de base. Pour plus d’informations, consultez [IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) dans [Core Cryptography Extensibility](xref:security/data-protection/extensibility/core-crypto) .

## <a name="persisting-keys-when-hosting-in-a-docker-container"></a>Persistance des clés lors de l’hébergement dans un conteneur d’ancrage

Lors de l’hébergement dans un conteneur d' [ancrage](/dotnet/standard/microservices-architecture/container-docker-introduction/) , les clés doivent être gérées dans :

* Dossier qui est un volume d’ancrage qui persiste au-delà de la durée de vie du conteneur, par exemple un volume partagé ou un volume monté sur un ordinateur hôte.
* Fournisseur externe, tel que [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) ou [redims](https://redis.io/).

## <a name="persisting-keys-with-redis"></a>Persistance des clés avec des ReDim

Seules les versions de ReDim qui prennent en charge la [persistance des données redims](/azure/azure-cache-for-redis/cache-how-to-premium-persistence) doivent être utilisées pour stocker des clés. Le [stockage d’objets BLOB Azure](/azure/storage/blobs/storage-blobs-introduction) est persistant et peut être utilisé pour stocker des clés. Pour plus d’informations, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore/issues/13476).

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:security/data-protection/configuration/non-di-scenarios>
* <xref:security/data-protection/configuration/machine-wide-policy>
* <xref:host-and-deploy/web-farm>
* <xref:security/data-protection/implementation/key-storage-providers>
