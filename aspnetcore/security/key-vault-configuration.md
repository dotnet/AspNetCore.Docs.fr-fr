---
title: Fournisseur de configuration Azure Key Vault dans ASP.NET Core
author: rick-anderson
description: Découvrez comment utiliser le fournisseur de configuration Azure Key Vault pour configurer une application à l’aide de paires nom-valeur chargées lors de l’exécution.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, devx-track-azurecli
ms.date: 02/07/2020
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
uid: security/key-vault-configuration
ms.openlocfilehash: 10a949831c180f51bc6bb9b8294150a558f9343c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060129"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="a0f07-103">Fournisseur de configuration Azure Key Vault dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a0f07-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="a0f07-104">Par [Andrew Stanton-infirmière](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="a0f07-104">By [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a0f07-105">Ce document explique comment utiliser le fournisseur de configuration [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pour charger des valeurs de configuration d’application à partir de Azure Key Vault secrets.</span><span class="sxs-lookup"><span data-stu-id="a0f07-105">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="a0f07-106">Azure Key Vault est un service basé sur le Cloud qui permet de protéger les clés de chiffrement et les secrets utilisés par les applications et les services.</span><span class="sxs-lookup"><span data-stu-id="a0f07-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="a0f07-107">Les scénarios courants d’utilisation de Azure Key Vault avec les applications ASP.NET Core sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="a0f07-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="a0f07-108">Contrôle de l’accès aux données de configuration sensibles.</span><span class="sxs-lookup"><span data-stu-id="a0f07-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="a0f07-109">Respect de la configuration requise pour les modules de sécurité matériels (HSM) certifiés FIPS 140-2 de niveau 2 lors du stockage des données de configuration.</span><span class="sxs-lookup"><span data-stu-id="a0f07-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="a0f07-110">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a0f07-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="a0f07-111">Paquets</span><span class="sxs-lookup"><span data-stu-id="a0f07-111">Packages</span></span>

<span data-ttu-id="a0f07-112">Ajoutez une référence de package à la [Microsoft.Extensions.Configfiguration. Package AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) .</span><span class="sxs-lookup"><span data-stu-id="a0f07-112">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="a0f07-113">Exemple d’application</span><span class="sxs-lookup"><span data-stu-id="a0f07-113">Sample app</span></span>

<span data-ttu-id="a0f07-114">L’exemple d’application s’exécute dans l’un des deux modes déterminés par l' `#define` instruction en haut du fichier *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="a0f07-114">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="a0f07-115">`Certificate`: Illustre l’utilisation d’un ID client Azure Key Vault et d’un certificat X. 509 pour accéder aux secrets stockés dans Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="a0f07-115">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="a0f07-116">Cette version de l’exemple peut être exécutée à partir de n’importe quel emplacement, déployée sur Azure App Service ou n’importe quel hôte capable de servir une application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a0f07-116">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="a0f07-117">`Managed`: Montre comment utiliser [des identités gérées pour les ressources Azure](/azure/active-directory/managed-identities-azure-resources/overview) pour authentifier l’application afin de Azure Key Vault avec l’authentification Azure ad sans les informations d’identification stockées dans le code ou la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="a0f07-117">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="a0f07-118">Lorsque vous utilisez des identités gérées pour l’authentification, un ID d’application et un mot de passe de Azure AD (clé secrète client) ne sont pas requis.</span><span class="sxs-lookup"><span data-stu-id="a0f07-118">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="a0f07-119">La `Managed` version de l’exemple doit être déployée sur Azure.</span><span class="sxs-lookup"><span data-stu-id="a0f07-119">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="a0f07-120">Suivez les instructions de la section [utiliser les identités gérées pour les ressources Azure](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="a0f07-120">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="a0f07-121">Pour plus d’informations sur la configuration d’un exemple d’application à l’aide de directives de préprocesseur ( `#define` ), consultez <xref:index#preprocessor-directives-in-sample-code> .</span><span class="sxs-lookup"><span data-stu-id="a0f07-121">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="a0f07-122">Stockage secret dans l’environnement de développement</span><span class="sxs-lookup"><span data-stu-id="a0f07-122">Secret storage in the Development environment</span></span>

<span data-ttu-id="a0f07-123">Définissez les secrets localement à l’aide de l' [outil Gestionnaire de secret](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="a0f07-123">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="a0f07-124">Lorsque l’exemple d’application s’exécute sur l’ordinateur local dans l’environnement de développement, les secrets sont chargés à partir du magasin du gestionnaire de secret local.</span><span class="sxs-lookup"><span data-stu-id="a0f07-124">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="a0f07-125">L’outil Gestionnaire de secret nécessite une `<UserSecretsId>` propriété dans le fichier projet de l’application.</span><span class="sxs-lookup"><span data-stu-id="a0f07-125">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="a0f07-126">Définissez la valeur de la propriété ( `{GUID}` ) sur un GUID unique :</span><span class="sxs-lookup"><span data-stu-id="a0f07-126">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="a0f07-127">Les secrets sont créés en tant que paires nom-valeur.</span><span class="sxs-lookup"><span data-stu-id="a0f07-127">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="a0f07-128">Les valeurs hiérarchiques (sections de configuration) utilisent un `:` (deux-points) comme séparateur dans ASP.net Core noms de clé de [configuration](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="a0f07-128">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="a0f07-129">Le gestionnaire de secret est utilisé à partir d’une interface de commande ouverte à la [racine de contenu](xref:fundamentals/index#content-root)du projet, où `{SECRET NAME}` est le nom et `{SECRET VALUE}` est la valeur :</span><span class="sxs-lookup"><span data-stu-id="a0f07-129">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="a0f07-130">Exécutez les commandes suivantes dans une interface de commande à partir de la [racine de contenu](xref:fundamentals/index#content-root) du projet pour définir les secrets de l’exemple d’application :</span><span class="sxs-lookup"><span data-stu-id="a0f07-130">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="a0f07-131">Lorsque ces secrets sont stockés dans Azure Key Vault dans le [stockage secret dans l’environnement de production avec Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, le `_dev` suffixe est remplacé par `_prod` .</span><span class="sxs-lookup"><span data-stu-id="a0f07-131">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="a0f07-132">Le suffixe fournit un signal visuel dans la sortie de l’application qui indique la source des valeurs de configuration.</span><span class="sxs-lookup"><span data-stu-id="a0f07-132">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="a0f07-133">Stockage secret dans l’environnement de production avec Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="a0f07-133">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="a0f07-134">Les instructions fournies par le Guide de [démarrage rapide : définir et récupérer un secret à partir de Azure Key Vault à l’aide de Azure CLI](/azure/key-vault/quick-create-cli) rubrique sont résumées ici pour créer un Azure Key Vault et stocker des secrets utilisés par l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="a0f07-134">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="a0f07-135">Pour plus d’informations, reportez-vous à la rubrique.</span><span class="sxs-lookup"><span data-stu-id="a0f07-135">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="a0f07-136">Ouvrez Azure Cloud Shell à l’aide de l’une des méthodes suivantes dans la [portail Azure](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="a0f07-136">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="a0f07-137">Sélectionnez **Essayer** dans le coin supérieur droit d’un bloc de code.</span><span class="sxs-lookup"><span data-stu-id="a0f07-137">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="a0f07-138">Utilisez la chaîne de recherche « Azure CLI » dans la zone de texte.</span><span class="sxs-lookup"><span data-stu-id="a0f07-138">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="a0f07-139">Ouvrez Cloud Shell dans votre navigateur à l’aide du bouton **Launch Cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="a0f07-139">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="a0f07-140">Sélectionnez le bouton **Cloud Shell** dans le menu situé dans l’angle supérieur droit du portail Azure.</span><span class="sxs-lookup"><span data-stu-id="a0f07-140">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="a0f07-141">Pour plus d’informations, consultez [Azure CLI](/cli/azure/) et [Présentation des Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="a0f07-141">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="a0f07-142">Si vous n’êtes pas déjà authentifié, connectez-vous à l’aide de la `az login` commande.</span><span class="sxs-lookup"><span data-stu-id="a0f07-142">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="a0f07-143">Créez un groupe de ressources à l’aide de la commande suivante, où `{RESOURCE GROUP NAME}` est le nom du groupe de ressources pour le nouveau groupe de ressources et `{LOCATION}` est la région Azure (Datacenter) :</span><span class="sxs-lookup"><span data-stu-id="a0f07-143">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="a0f07-144">Créez un coffre de clés dans le groupe de ressources à l’aide de la commande suivante, où `{KEY VAULT NAME}` est le nom du nouveau coffre de clés et `{LOCATION}` est la région Azure (Datacenter) :</span><span class="sxs-lookup"><span data-stu-id="a0f07-144">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="a0f07-145">Créez des secrets dans le coffre de clés en tant que paires nom-valeur.</span><span class="sxs-lookup"><span data-stu-id="a0f07-145">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="a0f07-146">Azure Key Vault noms de secrets sont limités aux caractères alphanumériques et aux tirets.</span><span class="sxs-lookup"><span data-stu-id="a0f07-146">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="a0f07-147">Les valeurs hiérarchiques (sections de configuration) utilisent `--` (deux tirets) comme séparateur.</span><span class="sxs-lookup"><span data-stu-id="a0f07-147">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="a0f07-148">Les signes deux-points, qui sont normalement utilisés pour délimiter une section d’une sous-clé dans [ASP.net Core configuration](xref:fundamentals/configuration/index), ne sont pas autorisés dans les noms de secrets du coffre de clés.</span><span class="sxs-lookup"><span data-stu-id="a0f07-148">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="a0f07-149">Par conséquent, deux tirets sont utilisés et permutés pour un signe deux-points lorsque les secrets sont chargés dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="a0f07-149">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="a0f07-150">Les secrets suivants sont destinés à être utilisés avec l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="a0f07-150">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="a0f07-151">Les valeurs incluent un `_prod` suffixe pour les distinguer des `_dev` valeurs de suffixe chargées dans l’environnement de développement des secrets d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="a0f07-151">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="a0f07-152">Remplacez `{KEY VAULT NAME}` par le nom du coffre de clés que vous avez créé à l’étape précédente :</span><span class="sxs-lookup"><span data-stu-id="a0f07-152">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="a0f07-153">Utiliser l’ID d’application et le certificat X. 509 pour les applications non hébergées sur Azure</span><span class="sxs-lookup"><span data-stu-id="a0f07-153">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="a0f07-154">Configurez Azure AD, Azure Key Vault et l’application pour qu’elle utilise un ID d’application Azure Active Directory et un certificat X. 509 pour s’authentifier auprès d’un coffre de clés **lorsque l’application est hébergée en dehors d’Azure** .</span><span class="sxs-lookup"><span data-stu-id="a0f07-154">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure** .</span></span> <span data-ttu-id="a0f07-155">Pour plus d’informations, consultez [À propos des clés, des secrets et des certificats](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="a0f07-155">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="a0f07-156">Bien que l’utilisation d’un ID d’application et d’un certificat X. 509 soit prise en charge pour les applications hébergées dans Azure, nous vous recommandons [d’utiliser des identités gérées pour les ressources Azure](#use-managed-identities-for-azure-resources) lors de l’hébergement d’une application dans Azure.</span><span class="sxs-lookup"><span data-stu-id="a0f07-156">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="a0f07-157">Les identités gérées ne nécessitent pas le stockage d’un certificat dans l’application ou dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="a0f07-157">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="a0f07-158">L’exemple d’application utilise un ID d’application et un certificat X. 509 lorsque l' `#define` instruction en haut du fichier *Program.cs* a la valeur `Certificate` .</span><span class="sxs-lookup"><span data-stu-id="a0f07-158">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="a0f07-159">Créez un certificat d’archive PKCS # 12 ( *. pfx* ).</span><span class="sxs-lookup"><span data-stu-id="a0f07-159">Create a PKCS#12 archive ( *.pfx* ) certificate.</span></span> <span data-ttu-id="a0f07-160">Les options de création de certificats incluent [Makecert sur Windows](/windows/desktop/seccrypto/makecert) et [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="a0f07-160">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="a0f07-161">Installez le certificat dans le magasin de certificats personnel de l’utilisateur actuel.</span><span class="sxs-lookup"><span data-stu-id="a0f07-161">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="a0f07-162">Le marquage de la clé comme étant exportable est facultatif.</span><span class="sxs-lookup"><span data-stu-id="a0f07-162">Marking the key as exportable is optional.</span></span> <span data-ttu-id="a0f07-163">Notez l’empreinte numérique du certificat, qui est utilisé plus loin dans ce processus.</span><span class="sxs-lookup"><span data-stu-id="a0f07-163">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="a0f07-164">Exportez le certificat PKCS # 12 ( *. pfx* ) sous la forme d’un certificat encodé der ( *. cer* ).</span><span class="sxs-lookup"><span data-stu-id="a0f07-164">Export the PKCS#12 archive ( *.pfx* ) certificate as a DER-encoded certificate ( *.cer* ).</span></span>
1. <span data-ttu-id="a0f07-165">Inscrire l’application auprès de Azure AD ( **inscriptions d’applications** ).</span><span class="sxs-lookup"><span data-stu-id="a0f07-165">Register the app with Azure AD ( **App registrations** ).</span></span>
1. <span data-ttu-id="a0f07-166">Chargez le certificat codé DER ( *. cer* ) dans Azure ad :</span><span class="sxs-lookup"><span data-stu-id="a0f07-166">Upload the DER-encoded certificate ( *.cer* ) to Azure AD:</span></span>
   1. <span data-ttu-id="a0f07-167">Sélectionnez l’application dans Azure AD.</span><span class="sxs-lookup"><span data-stu-id="a0f07-167">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="a0f07-168">Accédez à **certificats & secrets** .</span><span class="sxs-lookup"><span data-stu-id="a0f07-168">Navigate to **Certificates & secrets** .</span></span>
   1. <span data-ttu-id="a0f07-169">Sélectionnez **Télécharger le certificat** pour télécharger le certificat, qui contient la clé publique.</span><span class="sxs-lookup"><span data-stu-id="a0f07-169">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="a0f07-170">Un certificat. *CER* , *. pem* ou *. CRT* est acceptable.</span><span class="sxs-lookup"><span data-stu-id="a0f07-170">A *.cer* , *.pem* , or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="a0f07-171">Stockez le nom du coffre de clés, l’ID de l’application et l’empreinte numérique du certificat dans le fichier de l’application *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="a0f07-171">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="a0f07-172">Accédez à **coffres de clés** dans le portail Azure.</span><span class="sxs-lookup"><span data-stu-id="a0f07-172">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="a0f07-173">Sélectionnez le coffre de clés que vous avez créé dans la section [stockage secret dans l’environnement de production avec Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) .</span><span class="sxs-lookup"><span data-stu-id="a0f07-173">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="a0f07-174">Sélectionnez **Stratégies d’accès** .</span><span class="sxs-lookup"><span data-stu-id="a0f07-174">Select **Access policies** .</span></span>
1. <span data-ttu-id="a0f07-175">Sélectionnez **Ajouter une stratégie d’accès** .</span><span class="sxs-lookup"><span data-stu-id="a0f07-175">Select **Add Access Policy** .</span></span>
1. <span data-ttu-id="a0f07-176">Ouvrez les **autorisations secret** et fournissez à l’application les autorisations **obtenir** et **liste** .</span><span class="sxs-lookup"><span data-stu-id="a0f07-176">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="a0f07-177">Sélectionnez **Sélectionner un principal** et sélectionnez l’application inscrite par son nom.</span><span class="sxs-lookup"><span data-stu-id="a0f07-177">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="a0f07-178">Sélectionnez le bouton **Sélectionner** .</span><span class="sxs-lookup"><span data-stu-id="a0f07-178">Select the **Select** button.</span></span>
1. <span data-ttu-id="a0f07-179">Sélectionnez **OK** .</span><span class="sxs-lookup"><span data-stu-id="a0f07-179">Select **OK** .</span></span>
1. <span data-ttu-id="a0f07-180">Sélectionnez **Enregistrer** .</span><span class="sxs-lookup"><span data-stu-id="a0f07-180">Select **Save** .</span></span>
1. <span data-ttu-id="a0f07-181">Déployez l’application.</span><span class="sxs-lookup"><span data-stu-id="a0f07-181">Deploy the app.</span></span>

<span data-ttu-id="a0f07-182">L' `Certificate` exemple d’application obtient ses valeurs de configuration à partir du `IConfigurationRoot` même nom que le nom de la clé secrète :</span><span class="sxs-lookup"><span data-stu-id="a0f07-182">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="a0f07-183">Valeurs non hiérarchiques : la valeur de `SecretName` est obtenue avec `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="a0f07-183">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="a0f07-184">Valeurs hiérarchiques (sections) : utilisez `:` la notation (deux-points) ou la `GetSection` méthode d’extension.</span><span class="sxs-lookup"><span data-stu-id="a0f07-184">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="a0f07-185">Utilisez l’une des approches suivantes pour obtenir la valeur de configuration :</span><span class="sxs-lookup"><span data-stu-id="a0f07-185">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="a0f07-186">Le certificat X. 509 est géré par le système d’exploitation.</span><span class="sxs-lookup"><span data-stu-id="a0f07-186">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="a0f07-187">L’application appelle <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> avec les valeurs fournies par le *appsettings.json* fichier :</span><span class="sxs-lookup"><span data-stu-id="a0f07-187">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="a0f07-188">Exemples de valeurs</span><span class="sxs-lookup"><span data-stu-id="a0f07-188">Example values:</span></span>

* <span data-ttu-id="a0f07-189">Nom du coffre de clés : `contosovault`</span><span class="sxs-lookup"><span data-stu-id="a0f07-189">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="a0f07-190">ID de l’application : `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="a0f07-190">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="a0f07-191">Empreinte numérique du certificat : `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="a0f07-191">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="a0f07-192">*appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="a0f07-192">*appsettings.json* :</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="a0f07-193">Quand vous exécutez l’application, une page Web affiche les valeurs de secret chargées.</span><span class="sxs-lookup"><span data-stu-id="a0f07-193">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="a0f07-194">Dans l’environnement de développement, les valeurs secrètes sont chargées avec le `_dev` suffixe.</span><span class="sxs-lookup"><span data-stu-id="a0f07-194">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="a0f07-195">Dans l’environnement de production, les valeurs sont chargées avec le `_prod` suffixe.</span><span class="sxs-lookup"><span data-stu-id="a0f07-195">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="a0f07-196">Utiliser des identités gérées pour les ressources Azure</span><span class="sxs-lookup"><span data-stu-id="a0f07-196">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="a0f07-197">**Une application déployée sur Azure** peut tirer parti des [identités gérées pour les ressources Azure](/azure/active-directory/managed-identities-azure-resources/overview), ce qui permet à l’application de s’authentifier avec Azure Key Vault à l’aide de l’authentification Azure ad sans informations d’identification (ID d’application et mot de passe/clé secrète client) stockées dans l’application.</span><span class="sxs-lookup"><span data-stu-id="a0f07-197">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="a0f07-198">L’exemple d’application utilise des identités gérées pour les ressources Azure lorsque l' `#define` instruction en haut du fichier *Program.cs* a la valeur `Managed` .</span><span class="sxs-lookup"><span data-stu-id="a0f07-198">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="a0f07-199">Entrez le nom du coffre dans le fichier de l’application *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="a0f07-199">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="a0f07-200">L’exemple d’application n’a pas besoin d’un ID d’application et d’un mot de passe (clé secrète client) lorsqu’il est défini sur la `Managed` version. vous pouvez donc ignorer ces entrées de configuration.</span><span class="sxs-lookup"><span data-stu-id="a0f07-200">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="a0f07-201">L’application est déployée sur Azure et Azure authentifie l’application pour accéder à Azure Key Vault uniquement à l’aide du nom de coffre stocké dans le *appsettings.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="a0f07-201">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="a0f07-202">Déployez l’exemple d’application sur Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="a0f07-202">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="a0f07-203">Une application déployée sur Azure App Service est automatiquement inscrite auprès de Azure AD lors de la création du service.</span><span class="sxs-lookup"><span data-stu-id="a0f07-203">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="a0f07-204">Obtenez l’ID d’objet à partir du déploiement pour l’utiliser dans la commande suivante.</span><span class="sxs-lookup"><span data-stu-id="a0f07-204">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="a0f07-205">L’ID d’objet est affiché dans le Portail Azure sur le **Identity** panneau du App service.</span><span class="sxs-lookup"><span data-stu-id="a0f07-205">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="a0f07-206">À l’aide de Azure CLI et de l’ID d’objet de l’application, fournissez l’application avec `list` et les `get` autorisations d’accès au coffre de clés :</span><span class="sxs-lookup"><span data-stu-id="a0f07-206">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="a0f07-207">**Redémarrez l’application** à l’aide de Azure CLI, de PowerShell ou de l’portail Azure.</span><span class="sxs-lookup"><span data-stu-id="a0f07-207">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="a0f07-208">L’exemple d’application :</span><span class="sxs-lookup"><span data-stu-id="a0f07-208">The sample app:</span></span>

* <span data-ttu-id="a0f07-209">Crée une instance de la `AzureServiceTokenProvider` classe sans chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="a0f07-209">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="a0f07-210">Lorsqu’une chaîne de connexion n’est pas fournie, le fournisseur tente d’obtenir un jeton d’accès à partir des identités gérées pour les ressources Azure.</span><span class="sxs-lookup"><span data-stu-id="a0f07-210">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="a0f07-211">Une nouvelle <xref:Microsoft.Azure.KeyVault.KeyVaultClient> est créée avec le `AzureServiceTokenProvider` rappel de jeton d’instance.</span><span class="sxs-lookup"><span data-stu-id="a0f07-211">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="a0f07-212">L' <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance est utilisée avec une implémentation par défaut de <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> qui charge toutes les valeurs de secret et remplace les doubles tirets ( `--` ) par des signes deux-points ( `:` ) dans les noms de clés.</span><span class="sxs-lookup"><span data-stu-id="a0f07-212">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="a0f07-213">Exemple de valeur de nom de coffre de clés : `contosovault`</span><span class="sxs-lookup"><span data-stu-id="a0f07-213">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="a0f07-214">*appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="a0f07-214">*appsettings.json* :</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="a0f07-215">Quand vous exécutez l’application, une page Web affiche les valeurs de secret chargées.</span><span class="sxs-lookup"><span data-stu-id="a0f07-215">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="a0f07-216">Dans l’environnement de développement, les valeurs secrètes ont le `_dev` suffixe, car elles sont fournies par des secrets d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="a0f07-216">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="a0f07-217">Dans l’environnement de production, les valeurs sont chargées avec le `_prod` suffixe, car elles sont fournies par Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="a0f07-217">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="a0f07-218">Si vous recevez une `Access denied` erreur, vérifiez que l’application est inscrite auprès de Azure ad et que vous avez accès au coffre de clés.</span><span class="sxs-lookup"><span data-stu-id="a0f07-218">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="a0f07-219">Confirmez que vous avez redémarré le service dans Azure.</span><span class="sxs-lookup"><span data-stu-id="a0f07-219">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="a0f07-220">Pour plus d’informations sur l’utilisation du fournisseur avec une identité gérée et un pipeline Azure DevOps, consultez [créer une connexion de service Azure Resource Manager à une machine virtuelle avec une identité de service managée](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="a0f07-220">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="a0f07-221">Options de configuration</span><span class="sxs-lookup"><span data-stu-id="a0f07-221">Configuration options</span></span>

<span data-ttu-id="a0f07-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> peut accepter un <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions> :</span><span class="sxs-lookup"><span data-stu-id="a0f07-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> can accept an <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>:</span></span>

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| <span data-ttu-id="a0f07-223">Propriété</span><span class="sxs-lookup"><span data-stu-id="a0f07-223">Property</span></span>         | <span data-ttu-id="a0f07-224">Description</span><span class="sxs-lookup"><span data-stu-id="a0f07-224">Description</span></span> |
| ---------------- | ----------- |
| `Client`         | <span data-ttu-id="a0f07-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient> à utiliser pour récupérer des valeurs.</span><span class="sxs-lookup"><span data-stu-id="a0f07-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient> to use for retrieving values.</span></span> |
| `Manager`        | <span data-ttu-id="a0f07-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> instance utilisée pour contrôler le chargement du secret.</span><span class="sxs-lookup"><span data-stu-id="a0f07-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> instance used to control secret loading.</span></span> |
| `ReloadInterval` | <span data-ttu-id="a0f07-227">`Timespan` pour attendre entre les tentatives d’interrogation du coffre de clés pour les modifications.</span><span class="sxs-lookup"><span data-stu-id="a0f07-227">`Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="a0f07-228">La valeur par défaut est `null` (la configuration n’est pas rechargée).</span><span class="sxs-lookup"><span data-stu-id="a0f07-228">The default value is `null` (configuration isn't reloaded).</span></span> |
| `Vault`          | <span data-ttu-id="a0f07-229">URI du coffre de clés.</span><span class="sxs-lookup"><span data-stu-id="a0f07-229">Key vault URI.</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="a0f07-230">Utiliser un préfixe de nom de clé</span><span class="sxs-lookup"><span data-stu-id="a0f07-230">Use a key name prefix</span></span>

<span data-ttu-id="a0f07-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> fournit une surcharge qui accepte une implémentation de <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , qui vous permet de contrôler la façon dont les secrets de coffre de clés sont convertis en clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="a0f07-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="a0f07-232">Par exemple, vous pouvez implémenter l’interface pour charger des valeurs de secret en fonction d’une valeur de préfixe que vous fournissez au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="a0f07-232">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="a0f07-233">Cela vous permet, par exemple, de charger des secrets basés sur la version de l’application.</span><span class="sxs-lookup"><span data-stu-id="a0f07-233">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="a0f07-234">N’utilisez pas de préfixes sur des secrets de coffre de clés pour placer des secrets pour plusieurs applications dans le même coffre de clés ou pour placer des secrets d’environnement (par exemple, le *développement* et les secrets de *production* ) dans le même coffre.</span><span class="sxs-lookup"><span data-stu-id="a0f07-234">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="a0f07-235">Nous recommandons que les applications et les environnements de développement/production utilisent des coffres de clés distincts pour isoler les environnements d’application pour le plus haut niveau de sécurité.</span><span class="sxs-lookup"><span data-stu-id="a0f07-235">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="a0f07-236">Dans l’exemple suivant, un secret est établi dans le coffre de clés (et à l’aide de l’outil secret Manager pour l’environnement de développement) pour `5000-AppSecret` (les périodes ne sont pas autorisées dans les noms de secret de coffre de clés).</span><span class="sxs-lookup"><span data-stu-id="a0f07-236">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="a0f07-237">Ce secret représente un secret d’application pour la version 5.0.0.0 de l’application.</span><span class="sxs-lookup"><span data-stu-id="a0f07-237">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="a0f07-238">Pour une autre version de l’application, 5.1.0.0, un secret est ajouté au coffre de clés (et à l’aide de l’outil secret Manager) pour `5100-AppSecret` .</span><span class="sxs-lookup"><span data-stu-id="a0f07-238">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="a0f07-239">Chaque version de l’application charge sa valeur secrète avec version dans sa configuration en tant que `AppSecret` , en éliminant la version au fur et à mesure qu’elle charge le secret.</span><span class="sxs-lookup"><span data-stu-id="a0f07-239">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="a0f07-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> est appelé avec un personnalisé <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> :</span><span class="sxs-lookup"><span data-stu-id="a0f07-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="a0f07-241">L' <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implémentation réagit aux préfixes de version des secrets pour charger le secret approprié dans la configuration :</span><span class="sxs-lookup"><span data-stu-id="a0f07-241">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="a0f07-242">`Load` charge un secret lorsque son nom commence par le préfixe.</span><span class="sxs-lookup"><span data-stu-id="a0f07-242">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="a0f07-243">Les autres secrets ne sont pas chargés.</span><span class="sxs-lookup"><span data-stu-id="a0f07-243">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="a0f07-244">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="a0f07-244">`GetKey`:</span></span>
  * <span data-ttu-id="a0f07-245">Supprime le préfixe du nom de la clé secrète.</span><span class="sxs-lookup"><span data-stu-id="a0f07-245">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="a0f07-246">Remplace deux tirets dans n’importe quel nom par le `KeyDelimiter` , qui est le délimiteur utilisé dans la configuration (généralement un signe deux-points).</span><span class="sxs-lookup"><span data-stu-id="a0f07-246">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="a0f07-247">Azure Key Vault n’autorise pas les deux-points dans les noms de secrets.</span><span class="sxs-lookup"><span data-stu-id="a0f07-247">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="a0f07-248">La `Load` méthode est appelée par un algorithme de fournisseur qui itère au sein des secrets du coffre pour trouver ceux qui ont le préfixe de version.</span><span class="sxs-lookup"><span data-stu-id="a0f07-248">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="a0f07-249">Lorsqu’un préfixe de version est trouvé avec `Load` , l’algorithme utilise la `GetKey` méthode pour retourner le nom de configuration du nom de la clé secrète.</span><span class="sxs-lookup"><span data-stu-id="a0f07-249">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="a0f07-250">Il supprime le préfixe de version du nom du secret et retourne le reste du nom de secret pour le chargement dans les paires nom-valeur de la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="a0f07-250">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="a0f07-251">Lorsque cette approche est implémentée :</span><span class="sxs-lookup"><span data-stu-id="a0f07-251">When this approach is implemented:</span></span>

1. <span data-ttu-id="a0f07-252">Version de l’application spécifiée dans le fichier projet de l’application.</span><span class="sxs-lookup"><span data-stu-id="a0f07-252">The app's version specified in the app's project file.</span></span> <span data-ttu-id="a0f07-253">Dans l’exemple suivant, la version de l’application est définie sur `5.0.0.0` :</span><span class="sxs-lookup"><span data-stu-id="a0f07-253">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="a0f07-254">Vérifiez qu’une `<UserSecretsId>` propriété est présente dans le fichier projet de l’application, où `{GUID}` est un GUID fourni par l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="a0f07-254">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="a0f07-255">Enregistrez les secrets suivants localement à l’aide de l' [outil secret Manager](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="a0f07-255">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="a0f07-256">Les secrets sont enregistrés dans Azure Key Vault à l’aide des commandes de Azure CLI suivantes :</span><span class="sxs-lookup"><span data-stu-id="a0f07-256">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="a0f07-257">Lorsque l’application est exécutée, les secrets du coffre de clés sont chargés.</span><span class="sxs-lookup"><span data-stu-id="a0f07-257">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="a0f07-258">Le secret de chaîne de `5000-AppSecret` est mis en correspondance avec la version de l’application spécifiée dans le fichier projet de l’application ( `5.0.0.0` ).</span><span class="sxs-lookup"><span data-stu-id="a0f07-258">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="a0f07-259">La version `5000` (avec le tiret) est supprimée du nom de la clé.</span><span class="sxs-lookup"><span data-stu-id="a0f07-259">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="a0f07-260">Tout au long de l’application, la lecture de la configuration avec la clé `AppSecret` charge la valeur du secret.</span><span class="sxs-lookup"><span data-stu-id="a0f07-260">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="a0f07-261">Si la version de l’application est modifiée dans le fichier projet `5.1.0.0` et que l’application est à nouveau exécutée, la valeur secrète renvoyée se trouve `5.1.0.0_secret_value_dev` dans l’environnement de développement et `5.1.0.0_secret_value_prod` en production.</span><span class="sxs-lookup"><span data-stu-id="a0f07-261">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="a0f07-262">Vous pouvez également fournir votre propre <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implémentation à <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> .</span><span class="sxs-lookup"><span data-stu-id="a0f07-262">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="a0f07-263">Un client personnalisé permet de partager une seule instance du client dans l’application.</span><span class="sxs-lookup"><span data-stu-id="a0f07-263">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="a0f07-264">Lier un tableau à une classe</span><span class="sxs-lookup"><span data-stu-id="a0f07-264">Bind an array to a class</span></span>

<span data-ttu-id="a0f07-265">Le fournisseur est en mesure de lire les valeurs de configuration dans un tableau pour la liaison à un tableau POCO.</span><span class="sxs-lookup"><span data-stu-id="a0f07-265">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="a0f07-266">Lors de la lecture à partir d’une source de configuration qui autorise les clés à contenir des séparateurs de deux-points ( `:` ), un segment de clé numérique est utilisé pour distinguer les clés qui composent un tableau ( `:0:` , `:1:` , &hellip; `:{n}:` ).</span><span class="sxs-lookup"><span data-stu-id="a0f07-266">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="a0f07-267">Pour plus d’informations, consultez [Configuration : lier un tableau à une classe](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="a0f07-267">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="a0f07-268">Les clés de Azure Key Vault ne peuvent pas utiliser un signe deux-points comme séparateur.</span><span class="sxs-lookup"><span data-stu-id="a0f07-268">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="a0f07-269">L’approche décrite dans cette rubrique utilise des doubles tirets ( `--` ) comme séparateur pour les valeurs hiérarchiques (sections).</span><span class="sxs-lookup"><span data-stu-id="a0f07-269">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="a0f07-270">Les clés de tableau sont stockées dans Azure Key Vault avec des doubles tirets et des segments de clé numériques ( `--0--` , `--1--` , &hellip; `--{n}--` ).</span><span class="sxs-lookup"><span data-stu-id="a0f07-270">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="a0f07-271">Examinez la configuration de fournisseur de journalisation [Serilog](https://serilog.net/) suivante fournie par un fichier JSON.</span><span class="sxs-lookup"><span data-stu-id="a0f07-271">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="a0f07-272">Deux littéraux d’objet définis dans le `WriteTo` tableau reflètent deux *récepteurs* Serilog, qui décrivent les destinations pour la sortie de journalisation :</span><span class="sxs-lookup"><span data-stu-id="a0f07-272">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks* , which describe destinations for logging output:</span></span>

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

<span data-ttu-id="a0f07-273">La configuration indiquée dans le fichier JSON précédent est stockée dans Azure Key Vault à l’aide de la notation à deux tirets ( `--` ) et des segments numériques :</span><span class="sxs-lookup"><span data-stu-id="a0f07-273">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="a0f07-274">Clé</span><span class="sxs-lookup"><span data-stu-id="a0f07-274">Key</span></span> | <span data-ttu-id="a0f07-275">Valeur</span><span class="sxs-lookup"><span data-stu-id="a0f07-275">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="a0f07-276">Recharger les secrets</span><span class="sxs-lookup"><span data-stu-id="a0f07-276">Reload secrets</span></span>

<span data-ttu-id="a0f07-277">Les secrets sont mis en cache jusqu’à ce que `IConfigurationRoot.Reload()` soit appelé.</span><span class="sxs-lookup"><span data-stu-id="a0f07-277">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="a0f07-278">Les secrets ayant expiré, désactivés et mis à jour dans le coffre de clés ne sont pas respectés par l’application tant qu' `Reload` elle n’est pas exécutée.</span><span class="sxs-lookup"><span data-stu-id="a0f07-278">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="a0f07-279">Secrets désactivés et arrivés à expiration</span><span class="sxs-lookup"><span data-stu-id="a0f07-279">Disabled and expired secrets</span></span>

<span data-ttu-id="a0f07-280">Les secrets désactivés et arrivés à expiration lèvent un <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> .</span><span class="sxs-lookup"><span data-stu-id="a0f07-280">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="a0f07-281">Pour empêcher l’application de se déclencher, fournissez la configuration à l’aide d’un autre fournisseur de configuration ou mettez à jour le secret désactivé ou expiré.</span><span class="sxs-lookup"><span data-stu-id="a0f07-281">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="a0f07-282">Dépanner</span><span class="sxs-lookup"><span data-stu-id="a0f07-282">Troubleshoot</span></span>

<span data-ttu-id="a0f07-283">Lorsque l’application ne parvient pas à charger la configuration à l’aide du fournisseur, un message d’erreur est écrit dans l' [infrastructure de journalisation ASP.net Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="a0f07-283">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="a0f07-284">Les conditions suivantes empêchent le chargement de la configuration :</span><span class="sxs-lookup"><span data-stu-id="a0f07-284">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="a0f07-285">L’application ou le certificat n’est pas configuré correctement dans Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="a0f07-285">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="a0f07-286">Le coffre de clés n’existe pas dans Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="a0f07-286">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="a0f07-287">L’application n’est pas autorisée à accéder au coffre de clés.</span><span class="sxs-lookup"><span data-stu-id="a0f07-287">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="a0f07-288">La stratégie d’accès n’inclut pas les `Get` `List` autorisations et.</span><span class="sxs-lookup"><span data-stu-id="a0f07-288">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="a0f07-289">Dans le coffre de clés, les données de configuration (paire nom-valeur) sont incorrectement nommées, manquantes, désactivées ou expirées.</span><span class="sxs-lookup"><span data-stu-id="a0f07-289">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="a0f07-290">L’application a un nom de coffre de clés ( `KeyVaultName` ), un ID d’Application Azure ad ( `AzureADApplicationId` ) ou une empreinte de certificat Azure ad () incorrect `AzureADCertThumbprint` .</span><span class="sxs-lookup"><span data-stu-id="a0f07-290">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="a0f07-291">La clé de configuration (nom) est incorrecte dans l’application pour la valeur que vous essayez de charger.</span><span class="sxs-lookup"><span data-stu-id="a0f07-291">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="a0f07-292">Lors de l’ajout de la stratégie d’accès pour l’application au coffre de clés, la stratégie a été créée, mais le bouton **Enregistrer** n’a pas été sélectionné dans l’interface utilisateur des **stratégies d’accès** .</span><span class="sxs-lookup"><span data-stu-id="a0f07-292">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a0f07-293">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="a0f07-293">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="a0f07-294">Microsoft Azure : Key Vault</span><span class="sxs-lookup"><span data-stu-id="a0f07-294">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="a0f07-295">Microsoft Azure : documentation Key Vault</span><span class="sxs-lookup"><span data-stu-id="a0f07-295">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="a0f07-296">Génération et transfert de clés protégées par HSM pour Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="a0f07-296">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="a0f07-297">KeyVaultClient, classe</span><span class="sxs-lookup"><span data-stu-id="a0f07-297">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="a0f07-298">Démarrage rapide : définir et récupérer un secret depuis Azure Key Vault à l’aide d’une application web .NET</span><span class="sxs-lookup"><span data-stu-id="a0f07-298">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="a0f07-299">Tutoriel - Utiliser Azure Key Vault avec une machine virtuelle Windows Azure et le langage .NET</span><span class="sxs-lookup"><span data-stu-id="a0f07-299">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a0f07-300">Ce document explique comment utiliser le fournisseur de configuration [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pour charger des valeurs de configuration d’application à partir de Azure Key Vault secrets.</span><span class="sxs-lookup"><span data-stu-id="a0f07-300">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="a0f07-301">Azure Key Vault est un service basé sur le Cloud qui permet de protéger les clés de chiffrement et les secrets utilisés par les applications et les services.</span><span class="sxs-lookup"><span data-stu-id="a0f07-301">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="a0f07-302">Les scénarios courants d’utilisation de Azure Key Vault avec les applications ASP.NET Core sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="a0f07-302">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="a0f07-303">Contrôle de l’accès aux données de configuration sensibles.</span><span class="sxs-lookup"><span data-stu-id="a0f07-303">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="a0f07-304">Respect de la configuration requise pour les modules de sécurité matériels (HSM) certifiés FIPS 140-2 de niveau 2 lors du stockage des données de configuration.</span><span class="sxs-lookup"><span data-stu-id="a0f07-304">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="a0f07-305">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a0f07-305">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="a0f07-306">Paquets</span><span class="sxs-lookup"><span data-stu-id="a0f07-306">Packages</span></span>

<span data-ttu-id="a0f07-307">Ajoutez une référence de package à la [Microsoft.Extensions.Configfiguration. Package AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) .</span><span class="sxs-lookup"><span data-stu-id="a0f07-307">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="a0f07-308">Exemple d’application</span><span class="sxs-lookup"><span data-stu-id="a0f07-308">Sample app</span></span>

<span data-ttu-id="a0f07-309">L’exemple d’application s’exécute dans l’un des deux modes déterminés par l' `#define` instruction en haut du fichier *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="a0f07-309">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="a0f07-310">`Certificate`: Illustre l’utilisation d’un ID client Azure Key Vault et d’un certificat X. 509 pour accéder aux secrets stockés dans Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="a0f07-310">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="a0f07-311">Cette version de l’exemple peut être exécutée à partir de n’importe quel emplacement, déployée sur Azure App Service ou n’importe quel hôte capable de servir une application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a0f07-311">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="a0f07-312">`Managed`: Montre comment utiliser [des identités gérées pour les ressources Azure](/azure/active-directory/managed-identities-azure-resources/overview) pour authentifier l’application afin de Azure Key Vault avec l’authentification Azure ad sans les informations d’identification stockées dans le code ou la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="a0f07-312">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="a0f07-313">Lorsque vous utilisez des identités gérées pour l’authentification, un ID d’application et un mot de passe de Azure AD (clé secrète client) ne sont pas requis.</span><span class="sxs-lookup"><span data-stu-id="a0f07-313">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="a0f07-314">La `Managed` version de l’exemple doit être déployée sur Azure.</span><span class="sxs-lookup"><span data-stu-id="a0f07-314">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="a0f07-315">Suivez les instructions de la section [utiliser les identités gérées pour les ressources Azure](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="a0f07-315">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="a0f07-316">Pour plus d’informations sur la configuration d’un exemple d’application à l’aide de directives de préprocesseur ( `#define` ), consultez <xref:index#preprocessor-directives-in-sample-code> .</span><span class="sxs-lookup"><span data-stu-id="a0f07-316">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="a0f07-317">Stockage secret dans l’environnement de développement</span><span class="sxs-lookup"><span data-stu-id="a0f07-317">Secret storage in the Development environment</span></span>

<span data-ttu-id="a0f07-318">Définissez les secrets localement à l’aide de l' [outil Gestionnaire de secret](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="a0f07-318">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="a0f07-319">Lorsque l’exemple d’application s’exécute sur l’ordinateur local dans l’environnement de développement, les secrets sont chargés à partir du magasin du gestionnaire de secret local.</span><span class="sxs-lookup"><span data-stu-id="a0f07-319">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="a0f07-320">L’outil Gestionnaire de secret nécessite une `<UserSecretsId>` propriété dans le fichier projet de l’application.</span><span class="sxs-lookup"><span data-stu-id="a0f07-320">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="a0f07-321">Définissez la valeur de la propriété ( `{GUID}` ) sur un GUID unique :</span><span class="sxs-lookup"><span data-stu-id="a0f07-321">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="a0f07-322">Les secrets sont créés en tant que paires nom-valeur.</span><span class="sxs-lookup"><span data-stu-id="a0f07-322">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="a0f07-323">Les valeurs hiérarchiques (sections de configuration) utilisent un `:` (deux-points) comme séparateur dans ASP.net Core noms de clé de [configuration](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="a0f07-323">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="a0f07-324">Le gestionnaire de secret est utilisé à partir d’une interface de commande ouverte à la [racine de contenu](xref:fundamentals/index#content-root)du projet, où `{SECRET NAME}` est le nom et `{SECRET VALUE}` est la valeur :</span><span class="sxs-lookup"><span data-stu-id="a0f07-324">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="a0f07-325">Exécutez les commandes suivantes dans une interface de commande à partir de la [racine de contenu](xref:fundamentals/index#content-root) du projet pour définir les secrets de l’exemple d’application :</span><span class="sxs-lookup"><span data-stu-id="a0f07-325">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="a0f07-326">Lorsque ces secrets sont stockés dans Azure Key Vault dans le [stockage secret dans l’environnement de production avec Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, le `_dev` suffixe est remplacé par `_prod` .</span><span class="sxs-lookup"><span data-stu-id="a0f07-326">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="a0f07-327">Le suffixe fournit un signal visuel dans la sortie de l’application qui indique la source des valeurs de configuration.</span><span class="sxs-lookup"><span data-stu-id="a0f07-327">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="a0f07-328">Stockage secret dans l’environnement de production avec Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="a0f07-328">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="a0f07-329">Les instructions fournies par le Guide de [démarrage rapide : définir et récupérer un secret à partir de Azure Key Vault à l’aide de Azure CLI](/azure/key-vault/quick-create-cli) rubrique sont résumées ici pour créer un Azure Key Vault et stocker des secrets utilisés par l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="a0f07-329">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="a0f07-330">Pour plus d’informations, reportez-vous à la rubrique.</span><span class="sxs-lookup"><span data-stu-id="a0f07-330">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="a0f07-331">Ouvrez Azure Cloud Shell à l’aide de l’une des méthodes suivantes dans la [portail Azure](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="a0f07-331">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="a0f07-332">Sélectionnez **Essayer** dans le coin supérieur droit d’un bloc de code.</span><span class="sxs-lookup"><span data-stu-id="a0f07-332">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="a0f07-333">Utilisez la chaîne de recherche « Azure CLI » dans la zone de texte.</span><span class="sxs-lookup"><span data-stu-id="a0f07-333">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="a0f07-334">Ouvrez Cloud Shell dans votre navigateur à l’aide du bouton **Launch Cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="a0f07-334">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="a0f07-335">Sélectionnez le bouton **Cloud Shell** dans le menu situé dans l’angle supérieur droit du portail Azure.</span><span class="sxs-lookup"><span data-stu-id="a0f07-335">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="a0f07-336">Pour plus d’informations, consultez [Azure CLI](/cli/azure/) et [Présentation des Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="a0f07-336">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="a0f07-337">Si vous n’êtes pas déjà authentifié, connectez-vous à l’aide de la `az login` commande.</span><span class="sxs-lookup"><span data-stu-id="a0f07-337">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="a0f07-338">Créez un groupe de ressources à l’aide de la commande suivante, où `{RESOURCE GROUP NAME}` est le nom du groupe de ressources pour le nouveau groupe de ressources et `{LOCATION}` est la région Azure (Datacenter) :</span><span class="sxs-lookup"><span data-stu-id="a0f07-338">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="a0f07-339">Créez un coffre de clés dans le groupe de ressources à l’aide de la commande suivante, où `{KEY VAULT NAME}` est le nom du nouveau coffre de clés et `{LOCATION}` est la région Azure (Datacenter) :</span><span class="sxs-lookup"><span data-stu-id="a0f07-339">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="a0f07-340">Créez des secrets dans le coffre de clés en tant que paires nom-valeur.</span><span class="sxs-lookup"><span data-stu-id="a0f07-340">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="a0f07-341">Azure Key Vault noms de secrets sont limités aux caractères alphanumériques et aux tirets.</span><span class="sxs-lookup"><span data-stu-id="a0f07-341">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="a0f07-342">Les valeurs hiérarchiques (sections de configuration) utilisent `--` (deux tirets) comme séparateur.</span><span class="sxs-lookup"><span data-stu-id="a0f07-342">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="a0f07-343">Les signes deux-points, qui sont normalement utilisés pour délimiter une section d’une sous-clé dans [ASP.net Core configuration](xref:fundamentals/configuration/index), ne sont pas autorisés dans les noms de secrets du coffre de clés.</span><span class="sxs-lookup"><span data-stu-id="a0f07-343">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="a0f07-344">Par conséquent, deux tirets sont utilisés et permutés pour un signe deux-points lorsque les secrets sont chargés dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="a0f07-344">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="a0f07-345">Les secrets suivants sont destinés à être utilisés avec l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="a0f07-345">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="a0f07-346">Les valeurs incluent un `_prod` suffixe pour les distinguer des `_dev` valeurs de suffixe chargées dans l’environnement de développement des secrets d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="a0f07-346">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="a0f07-347">Remplacez `{KEY VAULT NAME}` par le nom du coffre de clés que vous avez créé à l’étape précédente :</span><span class="sxs-lookup"><span data-stu-id="a0f07-347">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="a0f07-348">Utiliser l’ID d’application et le certificat X. 509 pour les applications non hébergées sur Azure</span><span class="sxs-lookup"><span data-stu-id="a0f07-348">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="a0f07-349">Configurez Azure AD, Azure Key Vault et l’application pour qu’elle utilise un ID d’application Azure Active Directory et un certificat X. 509 pour s’authentifier auprès d’un coffre de clés **lorsque l’application est hébergée en dehors d’Azure** .</span><span class="sxs-lookup"><span data-stu-id="a0f07-349">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure** .</span></span> <span data-ttu-id="a0f07-350">Pour plus d’informations, consultez [À propos des clés, des secrets et des certificats](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="a0f07-350">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="a0f07-351">Bien que l’utilisation d’un ID d’application et d’un certificat X. 509 soit prise en charge pour les applications hébergées dans Azure, nous vous recommandons [d’utiliser des identités gérées pour les ressources Azure](#use-managed-identities-for-azure-resources) lors de l’hébergement d’une application dans Azure.</span><span class="sxs-lookup"><span data-stu-id="a0f07-351">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="a0f07-352">Les identités gérées ne nécessitent pas le stockage d’un certificat dans l’application ou dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="a0f07-352">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="a0f07-353">L’exemple d’application utilise un ID d’application et un certificat X. 509 lorsque l' `#define` instruction en haut du fichier *Program.cs* a la valeur `Certificate` .</span><span class="sxs-lookup"><span data-stu-id="a0f07-353">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="a0f07-354">Créez un certificat d’archive PKCS # 12 ( *. pfx* ).</span><span class="sxs-lookup"><span data-stu-id="a0f07-354">Create a PKCS#12 archive ( *.pfx* ) certificate.</span></span> <span data-ttu-id="a0f07-355">Les options de création de certificats incluent [Makecert sur Windows](/windows/desktop/seccrypto/makecert) et [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="a0f07-355">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="a0f07-356">Installez le certificat dans le magasin de certificats personnel de l’utilisateur actuel.</span><span class="sxs-lookup"><span data-stu-id="a0f07-356">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="a0f07-357">Le marquage de la clé comme étant exportable est facultatif.</span><span class="sxs-lookup"><span data-stu-id="a0f07-357">Marking the key as exportable is optional.</span></span> <span data-ttu-id="a0f07-358">Notez l’empreinte numérique du certificat, qui est utilisé plus loin dans ce processus.</span><span class="sxs-lookup"><span data-stu-id="a0f07-358">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="a0f07-359">Exportez le certificat PKCS # 12 ( *. pfx* ) sous la forme d’un certificat encodé der ( *. cer* ).</span><span class="sxs-lookup"><span data-stu-id="a0f07-359">Export the PKCS#12 archive ( *.pfx* ) certificate as a DER-encoded certificate ( *.cer* ).</span></span>
1. <span data-ttu-id="a0f07-360">Inscrire l’application auprès de Azure AD ( **inscriptions d’applications** ).</span><span class="sxs-lookup"><span data-stu-id="a0f07-360">Register the app with Azure AD ( **App registrations** ).</span></span>
1. <span data-ttu-id="a0f07-361">Chargez le certificat codé DER ( *. cer* ) dans Azure ad :</span><span class="sxs-lookup"><span data-stu-id="a0f07-361">Upload the DER-encoded certificate ( *.cer* ) to Azure AD:</span></span>
   1. <span data-ttu-id="a0f07-362">Sélectionnez l’application dans Azure AD.</span><span class="sxs-lookup"><span data-stu-id="a0f07-362">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="a0f07-363">Accédez à **certificats & secrets** .</span><span class="sxs-lookup"><span data-stu-id="a0f07-363">Navigate to **Certificates & secrets** .</span></span>
   1. <span data-ttu-id="a0f07-364">Sélectionnez **Télécharger le certificat** pour télécharger le certificat, qui contient la clé publique.</span><span class="sxs-lookup"><span data-stu-id="a0f07-364">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="a0f07-365">Un certificat. *CER* , *. pem* ou *. CRT* est acceptable.</span><span class="sxs-lookup"><span data-stu-id="a0f07-365">A *.cer* , *.pem* , or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="a0f07-366">Stockez le nom du coffre de clés, l’ID de l’application et l’empreinte numérique du certificat dans le fichier de l’application *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="a0f07-366">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="a0f07-367">Accédez à **coffres de clés** dans le portail Azure.</span><span class="sxs-lookup"><span data-stu-id="a0f07-367">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="a0f07-368">Sélectionnez le coffre de clés que vous avez créé dans la section [stockage secret dans l’environnement de production avec Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) .</span><span class="sxs-lookup"><span data-stu-id="a0f07-368">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="a0f07-369">Sélectionnez **Stratégies d’accès** .</span><span class="sxs-lookup"><span data-stu-id="a0f07-369">Select **Access policies** .</span></span>
1. <span data-ttu-id="a0f07-370">Sélectionnez **Ajouter une stratégie d’accès** .</span><span class="sxs-lookup"><span data-stu-id="a0f07-370">Select **Add Access Policy** .</span></span>
1. <span data-ttu-id="a0f07-371">Ouvrez les **autorisations secret** et fournissez à l’application les autorisations **obtenir** et **liste** .</span><span class="sxs-lookup"><span data-stu-id="a0f07-371">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="a0f07-372">Sélectionnez **Sélectionner un principal** et sélectionnez l’application inscrite par son nom.</span><span class="sxs-lookup"><span data-stu-id="a0f07-372">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="a0f07-373">Sélectionnez le bouton **Sélectionner** .</span><span class="sxs-lookup"><span data-stu-id="a0f07-373">Select the **Select** button.</span></span>
1. <span data-ttu-id="a0f07-374">Sélectionnez **OK** .</span><span class="sxs-lookup"><span data-stu-id="a0f07-374">Select **OK** .</span></span>
1. <span data-ttu-id="a0f07-375">Sélectionnez **Enregistrer** .</span><span class="sxs-lookup"><span data-stu-id="a0f07-375">Select **Save** .</span></span>
1. <span data-ttu-id="a0f07-376">Déployez l’application.</span><span class="sxs-lookup"><span data-stu-id="a0f07-376">Deploy the app.</span></span>

<span data-ttu-id="a0f07-377">L' `Certificate` exemple d’application obtient ses valeurs de configuration à partir du `IConfigurationRoot` même nom que le nom de la clé secrète :</span><span class="sxs-lookup"><span data-stu-id="a0f07-377">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="a0f07-378">Valeurs non hiérarchiques : la valeur de `SecretName` est obtenue avec `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="a0f07-378">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="a0f07-379">Valeurs hiérarchiques (sections) : utilisez `:` la notation (deux-points) ou la `GetSection` méthode d’extension.</span><span class="sxs-lookup"><span data-stu-id="a0f07-379">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="a0f07-380">Utilisez l’une des approches suivantes pour obtenir la valeur de configuration :</span><span class="sxs-lookup"><span data-stu-id="a0f07-380">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="a0f07-381">Le certificat X. 509 est géré par le système d’exploitation.</span><span class="sxs-lookup"><span data-stu-id="a0f07-381">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="a0f07-382">L’application appelle <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> avec les valeurs fournies par le *appsettings.json* fichier :</span><span class="sxs-lookup"><span data-stu-id="a0f07-382">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="a0f07-383">Exemples de valeurs</span><span class="sxs-lookup"><span data-stu-id="a0f07-383">Example values:</span></span>

* <span data-ttu-id="a0f07-384">Nom du coffre de clés : `contosovault`</span><span class="sxs-lookup"><span data-stu-id="a0f07-384">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="a0f07-385">ID de l’application : `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="a0f07-385">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="a0f07-386">Empreinte numérique du certificat : `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="a0f07-386">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="a0f07-387">*appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="a0f07-387">*appsettings.json* :</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="a0f07-388">Quand vous exécutez l’application, une page Web affiche les valeurs de secret chargées.</span><span class="sxs-lookup"><span data-stu-id="a0f07-388">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="a0f07-389">Dans l’environnement de développement, les valeurs secrètes sont chargées avec le `_dev` suffixe.</span><span class="sxs-lookup"><span data-stu-id="a0f07-389">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="a0f07-390">Dans l’environnement de production, les valeurs sont chargées avec le `_prod` suffixe.</span><span class="sxs-lookup"><span data-stu-id="a0f07-390">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="a0f07-391">Utiliser des identités gérées pour les ressources Azure</span><span class="sxs-lookup"><span data-stu-id="a0f07-391">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="a0f07-392">**Une application déployée sur Azure** peut tirer parti des [identités gérées pour les ressources Azure](/azure/active-directory/managed-identities-azure-resources/overview), ce qui permet à l’application de s’authentifier avec Azure Key Vault à l’aide de l’authentification Azure ad sans informations d’identification (ID d’application et mot de passe/clé secrète client) stockées dans l’application.</span><span class="sxs-lookup"><span data-stu-id="a0f07-392">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="a0f07-393">L’exemple d’application utilise des identités gérées pour les ressources Azure lorsque l' `#define` instruction en haut du fichier *Program.cs* a la valeur `Managed` .</span><span class="sxs-lookup"><span data-stu-id="a0f07-393">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="a0f07-394">Entrez le nom du coffre dans le fichier de l’application *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="a0f07-394">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="a0f07-395">L’exemple d’application n’a pas besoin d’un ID d’application et d’un mot de passe (clé secrète client) lorsqu’il est défini sur la `Managed` version. vous pouvez donc ignorer ces entrées de configuration.</span><span class="sxs-lookup"><span data-stu-id="a0f07-395">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="a0f07-396">L’application est déployée sur Azure et Azure authentifie l’application pour accéder à Azure Key Vault uniquement à l’aide du nom de coffre stocké dans le *appsettings.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="a0f07-396">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="a0f07-397">Déployez l’exemple d’application sur Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="a0f07-397">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="a0f07-398">Une application déployée sur Azure App Service est automatiquement inscrite auprès de Azure AD lors de la création du service.</span><span class="sxs-lookup"><span data-stu-id="a0f07-398">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="a0f07-399">Obtenez l’ID d’objet à partir du déploiement pour l’utiliser dans la commande suivante.</span><span class="sxs-lookup"><span data-stu-id="a0f07-399">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="a0f07-400">L’ID d’objet est affiché dans le Portail Azure sur le **Identity** panneau du App service.</span><span class="sxs-lookup"><span data-stu-id="a0f07-400">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="a0f07-401">À l’aide de Azure CLI et de l’ID d’objet de l’application, fournissez l’application avec `list` et les `get` autorisations d’accès au coffre de clés :</span><span class="sxs-lookup"><span data-stu-id="a0f07-401">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="a0f07-402">**Redémarrez l’application** à l’aide de Azure CLI, de PowerShell ou de l’portail Azure.</span><span class="sxs-lookup"><span data-stu-id="a0f07-402">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="a0f07-403">L’exemple d’application :</span><span class="sxs-lookup"><span data-stu-id="a0f07-403">The sample app:</span></span>

* <span data-ttu-id="a0f07-404">Crée une instance de la `AzureServiceTokenProvider` classe sans chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="a0f07-404">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="a0f07-405">Lorsqu’une chaîne de connexion n’est pas fournie, le fournisseur tente d’obtenir un jeton d’accès à partir des identités gérées pour les ressources Azure.</span><span class="sxs-lookup"><span data-stu-id="a0f07-405">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="a0f07-406">Une nouvelle <xref:Microsoft.Azure.KeyVault.KeyVaultClient> est créée avec le `AzureServiceTokenProvider` rappel de jeton d’instance.</span><span class="sxs-lookup"><span data-stu-id="a0f07-406">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="a0f07-407">L' <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance est utilisée avec une implémentation par défaut de <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> qui charge toutes les valeurs de secret et remplace les doubles tirets ( `--` ) par des signes deux-points ( `:` ) dans les noms de clés.</span><span class="sxs-lookup"><span data-stu-id="a0f07-407">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="a0f07-408">Exemple de valeur de nom de coffre de clés : `contosovault`</span><span class="sxs-lookup"><span data-stu-id="a0f07-408">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="a0f07-409">*appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="a0f07-409">*appsettings.json* :</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="a0f07-410">Quand vous exécutez l’application, une page Web affiche les valeurs de secret chargées.</span><span class="sxs-lookup"><span data-stu-id="a0f07-410">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="a0f07-411">Dans l’environnement de développement, les valeurs secrètes ont le `_dev` suffixe, car elles sont fournies par des secrets d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="a0f07-411">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="a0f07-412">Dans l’environnement de production, les valeurs sont chargées avec le `_prod` suffixe, car elles sont fournies par Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="a0f07-412">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="a0f07-413">Si vous recevez une `Access denied` erreur, vérifiez que l’application est inscrite auprès de Azure ad et que vous avez accès au coffre de clés.</span><span class="sxs-lookup"><span data-stu-id="a0f07-413">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="a0f07-414">Confirmez que vous avez redémarré le service dans Azure.</span><span class="sxs-lookup"><span data-stu-id="a0f07-414">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="a0f07-415">Pour plus d’informations sur l’utilisation du fournisseur avec une identité gérée et un pipeline Azure DevOps, consultez [créer une connexion de service Azure Resource Manager à une machine virtuelle avec une identité de service managée](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="a0f07-415">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="a0f07-416">Utiliser un préfixe de nom de clé</span><span class="sxs-lookup"><span data-stu-id="a0f07-416">Use a key name prefix</span></span>

<span data-ttu-id="a0f07-417"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> fournit une surcharge qui accepte une implémentation de <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , qui vous permet de contrôler la façon dont les secrets de coffre de clés sont convertis en clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="a0f07-417"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="a0f07-418">Par exemple, vous pouvez implémenter l’interface pour charger des valeurs de secret en fonction d’une valeur de préfixe que vous fournissez au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="a0f07-418">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="a0f07-419">Cela vous permet, par exemple, de charger des secrets basés sur la version de l’application.</span><span class="sxs-lookup"><span data-stu-id="a0f07-419">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="a0f07-420">N’utilisez pas de préfixes sur des secrets de coffre de clés pour placer des secrets pour plusieurs applications dans le même coffre de clés ou pour placer des secrets d’environnement (par exemple, le *développement* et les secrets de *production* ) dans le même coffre.</span><span class="sxs-lookup"><span data-stu-id="a0f07-420">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="a0f07-421">Nous recommandons que les applications et les environnements de développement/production utilisent des coffres de clés distincts pour isoler les environnements d’application pour le plus haut niveau de sécurité.</span><span class="sxs-lookup"><span data-stu-id="a0f07-421">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="a0f07-422">Dans l’exemple suivant, un secret est établi dans le coffre de clés (et à l’aide de l’outil secret Manager pour l’environnement de développement) pour `5000-AppSecret` (les périodes ne sont pas autorisées dans les noms de secret de coffre de clés).</span><span class="sxs-lookup"><span data-stu-id="a0f07-422">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="a0f07-423">Ce secret représente un secret d’application pour la version 5.0.0.0 de l’application.</span><span class="sxs-lookup"><span data-stu-id="a0f07-423">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="a0f07-424">Pour une autre version de l’application, 5.1.0.0, un secret est ajouté au coffre de clés (et à l’aide de l’outil secret Manager) pour `5100-AppSecret` .</span><span class="sxs-lookup"><span data-stu-id="a0f07-424">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="a0f07-425">Chaque version de l’application charge sa valeur secrète avec version dans sa configuration en tant que `AppSecret` , en éliminant la version au fur et à mesure qu’elle charge le secret.</span><span class="sxs-lookup"><span data-stu-id="a0f07-425">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="a0f07-426"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> est appelé avec un personnalisé <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> :</span><span class="sxs-lookup"><span data-stu-id="a0f07-426"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="a0f07-427">L' <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implémentation réagit aux préfixes de version des secrets pour charger le secret approprié dans la configuration :</span><span class="sxs-lookup"><span data-stu-id="a0f07-427">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="a0f07-428">`Load` charge un secret lorsque son nom commence par le préfixe.</span><span class="sxs-lookup"><span data-stu-id="a0f07-428">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="a0f07-429">Les autres secrets ne sont pas chargés.</span><span class="sxs-lookup"><span data-stu-id="a0f07-429">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="a0f07-430">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="a0f07-430">`GetKey`:</span></span>
  * <span data-ttu-id="a0f07-431">Supprime le préfixe du nom de la clé secrète.</span><span class="sxs-lookup"><span data-stu-id="a0f07-431">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="a0f07-432">Remplace deux tirets dans n’importe quel nom par le `KeyDelimiter` , qui est le délimiteur utilisé dans la configuration (généralement un signe deux-points).</span><span class="sxs-lookup"><span data-stu-id="a0f07-432">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="a0f07-433">Azure Key Vault n’autorise pas les deux-points dans les noms de secrets.</span><span class="sxs-lookup"><span data-stu-id="a0f07-433">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="a0f07-434">La `Load` méthode est appelée par un algorithme de fournisseur qui itère au sein des secrets du coffre pour trouver ceux qui ont le préfixe de version.</span><span class="sxs-lookup"><span data-stu-id="a0f07-434">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="a0f07-435">Lorsqu’un préfixe de version est trouvé avec `Load` , l’algorithme utilise la `GetKey` méthode pour retourner le nom de configuration du nom de la clé secrète.</span><span class="sxs-lookup"><span data-stu-id="a0f07-435">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="a0f07-436">Il supprime le préfixe de version du nom du secret et retourne le reste du nom de secret pour le chargement dans les paires nom-valeur de la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="a0f07-436">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="a0f07-437">Lorsque cette approche est implémentée :</span><span class="sxs-lookup"><span data-stu-id="a0f07-437">When this approach is implemented:</span></span>

1. <span data-ttu-id="a0f07-438">Version de l’application spécifiée dans le fichier projet de l’application.</span><span class="sxs-lookup"><span data-stu-id="a0f07-438">The app's version specified in the app's project file.</span></span> <span data-ttu-id="a0f07-439">Dans l’exemple suivant, la version de l’application est définie sur `5.0.0.0` :</span><span class="sxs-lookup"><span data-stu-id="a0f07-439">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="a0f07-440">Vérifiez qu’une `<UserSecretsId>` propriété est présente dans le fichier projet de l’application, où `{GUID}` est un GUID fourni par l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="a0f07-440">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="a0f07-441">Enregistrez les secrets suivants localement à l’aide de l' [outil secret Manager](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="a0f07-441">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="a0f07-442">Les secrets sont enregistrés dans Azure Key Vault à l’aide des commandes de Azure CLI suivantes :</span><span class="sxs-lookup"><span data-stu-id="a0f07-442">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="a0f07-443">Lorsque l’application est exécutée, les secrets du coffre de clés sont chargés.</span><span class="sxs-lookup"><span data-stu-id="a0f07-443">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="a0f07-444">Le secret de chaîne de `5000-AppSecret` est mis en correspondance avec la version de l’application spécifiée dans le fichier projet de l’application ( `5.0.0.0` ).</span><span class="sxs-lookup"><span data-stu-id="a0f07-444">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="a0f07-445">La version `5000` (avec le tiret) est supprimée du nom de la clé.</span><span class="sxs-lookup"><span data-stu-id="a0f07-445">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="a0f07-446">Tout au long de l’application, la lecture de la configuration avec la clé `AppSecret` charge la valeur du secret.</span><span class="sxs-lookup"><span data-stu-id="a0f07-446">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="a0f07-447">Si la version de l’application est modifiée dans le fichier projet `5.1.0.0` et que l’application est à nouveau exécutée, la valeur secrète renvoyée se trouve `5.1.0.0_secret_value_dev` dans l’environnement de développement et `5.1.0.0_secret_value_prod` en production.</span><span class="sxs-lookup"><span data-stu-id="a0f07-447">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="a0f07-448">Vous pouvez également fournir votre propre <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implémentation à <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> .</span><span class="sxs-lookup"><span data-stu-id="a0f07-448">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="a0f07-449">Un client personnalisé permet de partager une seule instance du client dans l’application.</span><span class="sxs-lookup"><span data-stu-id="a0f07-449">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="a0f07-450">Lier un tableau à une classe</span><span class="sxs-lookup"><span data-stu-id="a0f07-450">Bind an array to a class</span></span>

<span data-ttu-id="a0f07-451">Le fournisseur est en mesure de lire les valeurs de configuration dans un tableau pour la liaison à un tableau POCO.</span><span class="sxs-lookup"><span data-stu-id="a0f07-451">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="a0f07-452">Lors de la lecture à partir d’une source de configuration qui autorise les clés à contenir des séparateurs de deux-points ( `:` ), un segment de clé numérique est utilisé pour distinguer les clés qui composent un tableau ( `:0:` , `:1:` , &hellip; `:{n}:` ).</span><span class="sxs-lookup"><span data-stu-id="a0f07-452">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="a0f07-453">Pour plus d’informations, consultez [Configuration : lier un tableau à une classe](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="a0f07-453">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="a0f07-454">Les clés de Azure Key Vault ne peuvent pas utiliser un signe deux-points comme séparateur.</span><span class="sxs-lookup"><span data-stu-id="a0f07-454">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="a0f07-455">L’approche décrite dans cette rubrique utilise des doubles tirets ( `--` ) comme séparateur pour les valeurs hiérarchiques (sections).</span><span class="sxs-lookup"><span data-stu-id="a0f07-455">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="a0f07-456">Les clés de tableau sont stockées dans Azure Key Vault avec des doubles tirets et des segments de clé numériques ( `--0--` , `--1--` , &hellip; `--{n}--` ).</span><span class="sxs-lookup"><span data-stu-id="a0f07-456">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="a0f07-457">Examinez la configuration de fournisseur de journalisation [Serilog](https://serilog.net/) suivante fournie par un fichier JSON.</span><span class="sxs-lookup"><span data-stu-id="a0f07-457">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="a0f07-458">Deux littéraux d’objet définis dans le `WriteTo` tableau reflètent deux *récepteurs* Serilog, qui décrivent les destinations pour la sortie de journalisation :</span><span class="sxs-lookup"><span data-stu-id="a0f07-458">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks* , which describe destinations for logging output:</span></span>

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

<span data-ttu-id="a0f07-459">La configuration indiquée dans le fichier JSON précédent est stockée dans Azure Key Vault à l’aide de la notation à deux tirets ( `--` ) et des segments numériques :</span><span class="sxs-lookup"><span data-stu-id="a0f07-459">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="a0f07-460">Clé</span><span class="sxs-lookup"><span data-stu-id="a0f07-460">Key</span></span> | <span data-ttu-id="a0f07-461">Valeur</span><span class="sxs-lookup"><span data-stu-id="a0f07-461">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="a0f07-462">Recharger les secrets</span><span class="sxs-lookup"><span data-stu-id="a0f07-462">Reload secrets</span></span>

<span data-ttu-id="a0f07-463">Les secrets sont mis en cache jusqu’à ce que `IConfigurationRoot.Reload()` soit appelé.</span><span class="sxs-lookup"><span data-stu-id="a0f07-463">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="a0f07-464">Les secrets ayant expiré, désactivés et mis à jour dans le coffre de clés ne sont pas respectés par l’application tant qu' `Reload` elle n’est pas exécutée.</span><span class="sxs-lookup"><span data-stu-id="a0f07-464">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="a0f07-465">Secrets désactivés et arrivés à expiration</span><span class="sxs-lookup"><span data-stu-id="a0f07-465">Disabled and expired secrets</span></span>

<span data-ttu-id="a0f07-466">Les secrets désactivés et arrivés à expiration lèvent un <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> .</span><span class="sxs-lookup"><span data-stu-id="a0f07-466">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="a0f07-467">Pour empêcher l’application de se déclencher, fournissez la configuration à l’aide d’un autre fournisseur de configuration ou mettez à jour le secret désactivé ou expiré.</span><span class="sxs-lookup"><span data-stu-id="a0f07-467">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="a0f07-468">Dépanner</span><span class="sxs-lookup"><span data-stu-id="a0f07-468">Troubleshoot</span></span>

<span data-ttu-id="a0f07-469">Lorsque l’application ne parvient pas à charger la configuration à l’aide du fournisseur, un message d’erreur est écrit dans l' [infrastructure de journalisation ASP.net Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="a0f07-469">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="a0f07-470">Les conditions suivantes empêchent le chargement de la configuration :</span><span class="sxs-lookup"><span data-stu-id="a0f07-470">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="a0f07-471">L’application ou le certificat n’est pas configuré correctement dans Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="a0f07-471">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="a0f07-472">Le coffre de clés n’existe pas dans Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="a0f07-472">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="a0f07-473">L’application n’est pas autorisée à accéder au coffre de clés.</span><span class="sxs-lookup"><span data-stu-id="a0f07-473">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="a0f07-474">La stratégie d’accès n’inclut pas les `Get` `List` autorisations et.</span><span class="sxs-lookup"><span data-stu-id="a0f07-474">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="a0f07-475">Dans le coffre de clés, les données de configuration (paire nom-valeur) sont incorrectement nommées, manquantes, désactivées ou expirées.</span><span class="sxs-lookup"><span data-stu-id="a0f07-475">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="a0f07-476">L’application a un nom de coffre de clés ( `KeyVaultName` ), un ID d’Application Azure ad ( `AzureADApplicationId` ) ou une empreinte de certificat Azure ad () incorrect `AzureADCertThumbprint` .</span><span class="sxs-lookup"><span data-stu-id="a0f07-476">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="a0f07-477">La clé de configuration (nom) est incorrecte dans l’application pour la valeur que vous essayez de charger.</span><span class="sxs-lookup"><span data-stu-id="a0f07-477">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="a0f07-478">Lors de l’ajout de la stratégie d’accès pour l’application au coffre de clés, la stratégie a été créée, mais le bouton **Enregistrer** n’a pas été sélectionné dans l’interface utilisateur des **stratégies d’accès** .</span><span class="sxs-lookup"><span data-stu-id="a0f07-478">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a0f07-479">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="a0f07-479">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="a0f07-480">Microsoft Azure : Key Vault</span><span class="sxs-lookup"><span data-stu-id="a0f07-480">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="a0f07-481">Microsoft Azure : documentation Key Vault</span><span class="sxs-lookup"><span data-stu-id="a0f07-481">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="a0f07-482">Génération et transfert de clés protégées par HSM pour Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="a0f07-482">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="a0f07-483">KeyVaultClient, classe</span><span class="sxs-lookup"><span data-stu-id="a0f07-483">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="a0f07-484">Démarrage rapide : définir et récupérer un secret depuis Azure Key Vault à l’aide d’une application web .NET</span><span class="sxs-lookup"><span data-stu-id="a0f07-484">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="a0f07-485">Tutoriel - Utiliser Azure Key Vault avec une machine virtuelle Windows Azure et le langage .NET</span><span class="sxs-lookup"><span data-stu-id="a0f07-485">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

