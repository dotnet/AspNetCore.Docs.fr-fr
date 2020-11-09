---
title: Chiffrement à clé au repos dans Windows et Azure à l’aide de ASP.NET Core
author: rick-anderson
description: Découvrez les détails de l’implémentation du chiffrement à clé de protection des données ASP.NET Core au repos.
ms.author: riande
ms.date: 07/16/2018
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
uid: security/data-protection/implementation/key-encryption-at-rest
ms.openlocfilehash: e2841278e8262e6dbbfcf172e0b6599bb19f1d6c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061273"
---
# <a name="key-encryption-at-rest-in-windows-and-azure-using-aspnet-core"></a><span data-ttu-id="10e5b-103">Chiffrement à clé au repos dans Windows et Azure à l’aide de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="10e5b-103">Key encryption at rest in Windows and Azure using ASP.NET Core</span></span>

<span data-ttu-id="10e5b-104">Le système de protection [des données utilise par défaut un mécanisme de découverte](xref:security/data-protection/configuration/default-settings) pour déterminer comment les clés de chiffrement doivent être chiffrées au repos.</span><span class="sxs-lookup"><span data-stu-id="10e5b-104">The data protection system [employs a discovery mechanism by default](xref:security/data-protection/configuration/default-settings) to determine how cryptographic keys should be encrypted at rest.</span></span> <span data-ttu-id="10e5b-105">Le développeur peut remplacer le mécanisme de découverte et spécifier manuellement comment les clés doivent être chiffrées au repos.</span><span class="sxs-lookup"><span data-stu-id="10e5b-105">The developer can override the discovery mechanism and manually specify how keys should be encrypted at rest.</span></span>

> [!WARNING]
> <span data-ttu-id="10e5b-106">Si vous spécifiez un [emplacement de persistance de clé](xref:security/data-protection/implementation/key-storage-providers)explicite, le système de protection des données annule l’inscription du mécanisme de chiffrement à clé par défaut au repos.</span><span class="sxs-lookup"><span data-stu-id="10e5b-106">If you specify an explicit [key persistence location](xref:security/data-protection/implementation/key-storage-providers), the data protection system deregisters the default key encryption at rest mechanism.</span></span> <span data-ttu-id="10e5b-107">Par conséquent, les clés ne sont plus chiffrées au repos.</span><span class="sxs-lookup"><span data-stu-id="10e5b-107">Consequently, keys are no longer encrypted at rest.</span></span> <span data-ttu-id="10e5b-108">Nous vous recommandons de [spécifier un mécanisme de chiffrement à clé explicite](xref:security/data-protection/implementation/key-encryption-at-rest) pour les déploiements de production.</span><span class="sxs-lookup"><span data-stu-id="10e5b-108">We recommend that you [specify an explicit key encryption mechanism](xref:security/data-protection/implementation/key-encryption-at-rest) for production deployments.</span></span> <span data-ttu-id="10e5b-109">Les options du mécanisme de chiffrement au repos sont décrites dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="10e5b-109">The encryption-at-rest mechanism options are described in this topic.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="azure-key-vault"></a><span data-ttu-id="10e5b-110">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="10e5b-110">Azure Key Vault</span></span>

<span data-ttu-id="10e5b-111">Pour stocker des clés dans [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configurez le système avec [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) dans la `Startup` classe :</span><span class="sxs-lookup"><span data-stu-id="10e5b-111">To store keys in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure the system with [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) in the `Startup` class:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

<span data-ttu-id="10e5b-112">Pour plus d’informations, consultez [configurer la protection des données ASP.net Core : ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault).</span><span class="sxs-lookup"><span data-stu-id="10e5b-112">For more information, see [Configure ASP.NET Core Data Protection: ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault).</span></span>

::: moniker-end

## <a name="windows-dpapi"></a><span data-ttu-id="10e5b-113">DPAPI Windows</span><span class="sxs-lookup"><span data-stu-id="10e5b-113">Windows DPAPI</span></span>

<span data-ttu-id="10e5b-114">**S’applique uniquement aux déploiements Windows.**</span><span class="sxs-lookup"><span data-stu-id="10e5b-114">**Only applies to Windows deployments.**</span></span>

<span data-ttu-id="10e5b-115">Lorsque Windows DPAPI est utilisé, le matériel de clé est chiffré avec [CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata) avant d’être conservé dans le stockage.</span><span class="sxs-lookup"><span data-stu-id="10e5b-115">When Windows DPAPI is used, key material is encrypted with [CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata) before being persisted to storage.</span></span> <span data-ttu-id="10e5b-116">DPAPI est un mécanisme de chiffrement approprié pour les données qui ne sont jamais lues en dehors de l’ordinateur actuel (bien qu’il soit possible de sauvegarder ces clés jusqu’à Active Directory. consultez [DPAPI et profils itinérants](https://support.microsoft.com/kb/309408/#6)).</span><span class="sxs-lookup"><span data-stu-id="10e5b-116">DPAPI is an appropriate encryption mechanism for data that's never read outside of the current machine (though it's possible to back these keys up to Active Directory; see [DPAPI and Roaming Profiles](https://support.microsoft.com/kb/309408/#6)).</span></span> <span data-ttu-id="10e5b-117">Pour configurer le chiffrement de la clé au repos DPAPI, appelez l’une des méthodes d’extension [ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi) :</span><span class="sxs-lookup"><span data-stu-id="10e5b-117">To configure DPAPI key-at-rest encryption, call one of the [ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi) extension methods:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Only the local user account can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi();
}
```

<span data-ttu-id="10e5b-118">Si `ProtectKeysWithDpapi` est appelé sans paramètre, seul le compte d’utilisateur Windows actuel peut déchiffrer l’anneau de clé persistant.</span><span class="sxs-lookup"><span data-stu-id="10e5b-118">If `ProtectKeysWithDpapi` is called with no parameters, only the current Windows user account can decipher the persisted key ring.</span></span> <span data-ttu-id="10e5b-119">Vous pouvez éventuellement spécifier que n’importe quel compte d’utilisateur sur l’ordinateur (pas seulement le compte d’utilisateur actuel) soit en mesure de déchiffrer l’anneau de clé :</span><span class="sxs-lookup"><span data-stu-id="10e5b-119">You can optionally specify that any user account on the machine (not just the current user account) be able to decipher the key ring:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // All user accounts on the machine can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi(protectToLocalMachine: true);
}
```

::: moniker range=">= aspnetcore-2.0"

## <a name="x509-certificate"></a><span data-ttu-id="10e5b-120">Certificat X.509</span><span class="sxs-lookup"><span data-stu-id="10e5b-120">X.509 certificate</span></span>

<span data-ttu-id="10e5b-121">Si l’application est répartie sur plusieurs ordinateurs, il peut être pratique de distribuer un certificat X. 509 partagé sur les ordinateurs et de configurer les applications hébergées pour utiliser le certificat pour le chiffrement des clés au repos :</span><span class="sxs-lookup"><span data-stu-id="10e5b-121">If the app is spread across multiple machines, it may be convenient to distribute a shared X.509 certificate across the machines and configure the hosted apps to use the certificate for encryption of keys at rest:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithCertificate("3BCE558E2AD3E0E34A7743EAB5AEA2A9BD2575A0");
}
```

<span data-ttu-id="10e5b-122">En raison des limitations de .NET Framework, seuls les certificats avec des clés privées CAPI sont pris en charge.</span><span class="sxs-lookup"><span data-stu-id="10e5b-122">Due to .NET Framework limitations, only certificates with CAPI private keys are supported.</span></span> <span data-ttu-id="10e5b-123">Consultez le contenu ci-dessous pour obtenir des solutions possibles à ces limitations.</span><span class="sxs-lookup"><span data-stu-id="10e5b-123">See the content below for possible workarounds to these limitations.</span></span>

::: moniker-end

## <a name="windows-dpapi-ng"></a><span data-ttu-id="10e5b-124">Windows DPAPI-NG</span><span class="sxs-lookup"><span data-stu-id="10e5b-124">Windows DPAPI-NG</span></span>

<span data-ttu-id="10e5b-125">**Ce mécanisme est disponible uniquement sur Windows 8/Windows Server 2012 ou version ultérieure.**</span><span class="sxs-lookup"><span data-stu-id="10e5b-125">**This mechanism is available only on Windows 8/Windows Server 2012 or later.**</span></span>

<span data-ttu-id="10e5b-126">À compter de Windows 8, le système d’exploitation Windows prend en charge DPAPI-GN (également appelé CNG DPAPI).</span><span class="sxs-lookup"><span data-stu-id="10e5b-126">Beginning with Windows 8, Windows OS supports DPAPI-NG (also called CNG DPAPI).</span></span> <span data-ttu-id="10e5b-127">Pour plus d’informations, consultez [à propos de CNG DPAPI](/windows/desktop/SecCNG/cng-dpapi).</span><span class="sxs-lookup"><span data-stu-id="10e5b-127">For more information, see [About CNG DPAPI](/windows/desktop/SecCNG/cng-dpapi).</span></span>

<span data-ttu-id="10e5b-128">Le principal est encodé sous la forme d’une règle de descripteur de protection.</span><span class="sxs-lookup"><span data-stu-id="10e5b-128">The principal is encoded as a protection descriptor rule.</span></span> <span data-ttu-id="10e5b-129">Dans l’exemple suivant qui appelle [ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping), seul l’utilisateur joint au domaine avec le SID spécifié peut déchiffrer l’anneau de clé :</span><span class="sxs-lookup"><span data-stu-id="10e5b-129">In the following example that calls [ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping), only the domain-joined user with the specified SID can decrypt the key ring:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Uses the descriptor rule "SID=S-1-5-21-..."
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("SID=S-1-5-21-...",
        flags: DpapiNGProtectionDescriptorFlags.None);
}
```

<span data-ttu-id="10e5b-130">Il y a également une surcharge sans paramètre de `ProtectKeysWithDpapiNG` .</span><span class="sxs-lookup"><span data-stu-id="10e5b-130">There's also a parameterless overload of `ProtectKeysWithDpapiNG`.</span></span> <span data-ttu-id="10e5b-131">Utilisez cette méthode pratique pour spécifier la règle « SID = {CURRENT_ACCOUNT_SID} », où *CURRENT_ACCOUNT_SID* est le SID du compte d’utilisateur Windows actuel :</span><span class="sxs-lookup"><span data-stu-id="10e5b-131">Use this convenience method to specify the rule "SID={CURRENT_ACCOUNT_SID}", where *CURRENT_ACCOUNT_SID* is the SID of the current Windows user account:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Use the descriptor rule "SID={current account SID}"
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG();
}
```

<span data-ttu-id="10e5b-132">Dans ce scénario, le contrôleur de domaine Active Directory est responsable de la distribution des clés de chiffrement utilisées par les opérations DPAPI-GN.</span><span class="sxs-lookup"><span data-stu-id="10e5b-132">In this scenario, the AD domain controller is responsible for distributing the encryption keys used by the DPAPI-NG operations.</span></span> <span data-ttu-id="10e5b-133">L’utilisateur cible peut déchiffrer la charge utile chiffrée à partir de n’importe quel ordinateur appartenant à un domaine (à condition que le processus s’exécute sous son identité).</span><span class="sxs-lookup"><span data-stu-id="10e5b-133">The target user can decipher the encrypted payload from any domain-joined machine (provided that the process is running under their identity).</span></span>

## <a name="certificate-based-encryption-with-windows-dpapi-ng"></a><span data-ttu-id="10e5b-134">Chiffrement basé sur les certificats avec Windows DPAPI-GN</span><span class="sxs-lookup"><span data-stu-id="10e5b-134">Certificate-based encryption with Windows DPAPI-NG</span></span>

<span data-ttu-id="10e5b-135">Si l’application s’exécute sur Windows 8.1/Windows Server 2012 R2 ou version ultérieure, vous pouvez utiliser Windows DPAPI-GN pour effectuer un chiffrement basé sur les certificats.</span><span class="sxs-lookup"><span data-stu-id="10e5b-135">If the app is running on Windows 8.1/Windows Server 2012 R2 or later, you can use Windows DPAPI-NG to perform certificate-based encryption.</span></span> <span data-ttu-id="10e5b-136">Utilisez la chaîne de descripteur de règle « CERTIFICATe = HashId : empreinte numérique », où *empreinte* est l’empreinte numérique SHA1 codée au format hexadécimal du certificat :</span><span class="sxs-lookup"><span data-stu-id="10e5b-136">Use the rule descriptor string "CERTIFICATE=HashId:THUMBPRINT", where *THUMBPRINT* is the hex-encoded SHA1 thumbprint of the certificate:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("CERTIFICATE=HashId:3BCE558E2...B5AEA2A9BD2575A0",
            flags: DpapiNGProtectionDescriptorFlags.None);
}
```

<span data-ttu-id="10e5b-137">Toute application pointée sur ce référentiel doit s’exécuter sur Windows 8.1/Windows Server 2012 R2 ou version ultérieure pour déchiffrer les clés.</span><span class="sxs-lookup"><span data-stu-id="10e5b-137">Any app pointed at this repository must be running on Windows 8.1/Windows Server 2012 R2 or later to decipher the keys.</span></span>

## <a name="custom-key-encryption"></a><span data-ttu-id="10e5b-138">Chiffrement à clé personnalisée</span><span class="sxs-lookup"><span data-stu-id="10e5b-138">Custom key encryption</span></span>

<span data-ttu-id="10e5b-139">Si les mécanismes intégrés ne sont pas appropriés, le développeur peut spécifier son propre mécanisme de chiffrement à clé en fournissant un [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor)personnalisé.</span><span class="sxs-lookup"><span data-stu-id="10e5b-139">If the in-box mechanisms aren't appropriate, the developer can specify their own key encryption mechanism by providing a custom [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor).</span></span>
