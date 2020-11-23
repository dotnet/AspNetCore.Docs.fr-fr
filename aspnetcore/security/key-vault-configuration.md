---
title: Fournisseur de configuration Azure Key Vault dans ASP.NET Core
author: rick-anderson
description: Découvrez comment utiliser le fournisseur de configuration Azure Key Vault pour configurer une application à l’aide de paires nom-valeur chargées lors de l’exécution.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, devx-track-azurecli
ms.date: 02/07/2020
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
uid: security/key-vault-configuration
ms.openlocfilehash: fcd5524bed11cca2380ffd8956f437f742729b55
ms.sourcegitcommit: aa85f2911792a1e4783bcabf0da3b3e7e218f63a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95417607"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="24e05-103">Fournisseur de configuration Azure Key Vault dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="24e05-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="24e05-104">Par [Andrew Stanton-infirmière](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="24e05-104">By [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="24e05-105">Ce document explique comment utiliser le fournisseur de configuration [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pour charger des valeurs de configuration d’application à partir de Azure Key Vault secrets.</span><span class="sxs-lookup"><span data-stu-id="24e05-105">This document explains how to use the [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="24e05-106">Azure Key Vault est un service basé sur le Cloud qui permet de protéger les clés de chiffrement et les secrets utilisés par les applications et les services.</span><span class="sxs-lookup"><span data-stu-id="24e05-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="24e05-107">Les scénarios courants d’utilisation de Azure Key Vault avec les applications ASP.NET Core sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="24e05-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="24e05-108">Contrôle de l’accès aux données de configuration sensibles.</span><span class="sxs-lookup"><span data-stu-id="24e05-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="24e05-109">Respect de la configuration requise pour les modules de sécurité matériels (HSM) certifiés FIPS 140-2 de niveau 2 lors du stockage des données de configuration.</span><span class="sxs-lookup"><span data-stu-id="24e05-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="24e05-110">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="24e05-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="24e05-111">.</span><span class="sxs-lookup"><span data-stu-id="24e05-111">Packages</span></span>

<span data-ttu-id="24e05-112">Ajoutez une référence de package à la [Azure.Extensions.AspNetCore.Configfiguration. ](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.Configuration.Secrets/) Package de secrets.</span><span class="sxs-lookup"><span data-stu-id="24e05-112">Add a package reference to the [Azure.Extensions.AspNetCore.Configuration.Secrets](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.Configuration.Secrets/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="24e05-113">Exemple d’application</span><span class="sxs-lookup"><span data-stu-id="24e05-113">Sample app</span></span>

<span data-ttu-id="24e05-114">L’exemple d’application s’exécute dans l’un des deux modes déterminés par l' `#define` instruction en haut du fichier *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="24e05-114">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="24e05-115">`Certificate`: Illustre l’utilisation d’un ID client Azure Key Vault et d’un certificat X. 509 pour accéder aux secrets stockés dans Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="24e05-115">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="24e05-116">Cette version de l’exemple peut être exécutée à partir de n’importe quel emplacement, déployée sur Azure App Service ou n’importe quel hôte capable de servir une application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="24e05-116">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="24e05-117">`Managed`: Montre comment utiliser [des identités gérées pour les ressources Azure](/azure/active-directory/managed-identities-azure-resources/overview) pour authentifier l’application afin de Azure Key Vault avec l’authentification Azure ad sans les informations d’identification stockées dans le code ou la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="24e05-117">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="24e05-118">Lorsque vous utilisez des identités gérées pour l’authentification, un ID d’application et un mot de passe de Azure AD (clé secrète client) ne sont pas requis.</span><span class="sxs-lookup"><span data-stu-id="24e05-118">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="24e05-119">La `Managed` version de l’exemple doit être déployée sur Azure.</span><span class="sxs-lookup"><span data-stu-id="24e05-119">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="24e05-120">Suivez les instructions de la section [utiliser les identités gérées pour les ressources Azure](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="24e05-120">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="24e05-121">Pour plus d’informations sur la configuration d’un exemple d’application à l’aide de directives de préprocesseur ( `#define` ), consultez <xref:index#preprocessor-directives-in-sample-code> .</span><span class="sxs-lookup"><span data-stu-id="24e05-121">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="24e05-122">Stockage secret dans l’environnement de développement</span><span class="sxs-lookup"><span data-stu-id="24e05-122">Secret storage in the Development environment</span></span>

<span data-ttu-id="24e05-123">Définissez les secrets localement à l’aide de l' [outil Gestionnaire de secret](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="24e05-123">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="24e05-124">Lorsque l’exemple d’application s’exécute sur l’ordinateur local dans l’environnement de développement, les secrets sont chargés à partir du magasin du gestionnaire de secret local.</span><span class="sxs-lookup"><span data-stu-id="24e05-124">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="24e05-125">L’outil Gestionnaire de secret nécessite une `<UserSecretsId>` propriété dans le fichier projet de l’application.</span><span class="sxs-lookup"><span data-stu-id="24e05-125">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="24e05-126">Définissez la valeur de la propriété ( `{GUID}` ) sur un GUID unique :</span><span class="sxs-lookup"><span data-stu-id="24e05-126">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="24e05-127">Les secrets sont créés en tant que paires nom-valeur.</span><span class="sxs-lookup"><span data-stu-id="24e05-127">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="24e05-128">Les valeurs hiérarchiques (sections de configuration) utilisent un `:` (deux-points) comme séparateur dans ASP.net Core noms de clé de [configuration](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="24e05-128">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="24e05-129">Le gestionnaire de secret est utilisé à partir d’une interface de commande ouverte à la [racine de contenu](xref:fundamentals/index#content-root)du projet, où `{SECRET NAME}` est le nom et `{SECRET VALUE}` est la valeur :</span><span class="sxs-lookup"><span data-stu-id="24e05-129">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="24e05-130">Exécutez les commandes suivantes dans une interface de commande à partir de la [racine de contenu](xref:fundamentals/index#content-root) du projet pour définir les secrets de l’exemple d’application :</span><span class="sxs-lookup"><span data-stu-id="24e05-130">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="24e05-131">Lorsque ces secrets sont stockés dans Azure Key Vault dans le [stockage secret dans l’environnement de production avec Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, le `_dev` suffixe est remplacé par `_prod` .</span><span class="sxs-lookup"><span data-stu-id="24e05-131">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="24e05-132">Le suffixe fournit un signal visuel dans la sortie de l’application qui indique la source des valeurs de configuration.</span><span class="sxs-lookup"><span data-stu-id="24e05-132">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="24e05-133">Stockage secret dans l’environnement de production avec Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="24e05-133">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="24e05-134">Les instructions fournies par le Guide de [démarrage rapide : définir et récupérer un secret à partir de Azure Key Vault à l’aide de Azure CLI](/azure/key-vault/quick-create-cli) rubrique sont résumées ici pour créer un Azure Key Vault et stocker des secrets utilisés par l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="24e05-134">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="24e05-135">Pour plus d’informations, reportez-vous à la rubrique.</span><span class="sxs-lookup"><span data-stu-id="24e05-135">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="24e05-136">Ouvrez Azure Cloud Shell à l’aide de l’une des méthodes suivantes dans la [portail Azure](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="24e05-136">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="24e05-137">Sélectionnez **Essayer** dans le coin supérieur droit d’un bloc de code.</span><span class="sxs-lookup"><span data-stu-id="24e05-137">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="24e05-138">Utilisez la chaîne de recherche « Azure CLI » dans la zone de texte.</span><span class="sxs-lookup"><span data-stu-id="24e05-138">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="24e05-139">Ouvrez Cloud Shell dans votre navigateur à l’aide du bouton **Launch Cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="24e05-139">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="24e05-140">Sélectionnez le bouton **Cloud Shell** dans le menu situé dans l’angle supérieur droit du portail Azure.</span><span class="sxs-lookup"><span data-stu-id="24e05-140">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="24e05-141">Pour plus d’informations, consultez [Azure CLI](/cli/azure/) et [Présentation des Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="24e05-141">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="24e05-142">Si vous n’êtes pas déjà authentifié, connectez-vous à l’aide de la `az login` commande.</span><span class="sxs-lookup"><span data-stu-id="24e05-142">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="24e05-143">Créez un groupe de ressources à l’aide de la commande suivante, où `{RESOURCE GROUP NAME}` est le nom du groupe de ressources pour le nouveau groupe de ressources et `{LOCATION}` est la région Azure (Datacenter) :</span><span class="sxs-lookup"><span data-stu-id="24e05-143">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="24e05-144">Créez un coffre de clés dans le groupe de ressources à l’aide de la commande suivante, où `{KEY VAULT NAME}` est le nom du nouveau coffre de clés et `{LOCATION}` est la région Azure (Datacenter) :</span><span class="sxs-lookup"><span data-stu-id="24e05-144">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="24e05-145">Créez des secrets dans le coffre de clés en tant que paires nom-valeur.</span><span class="sxs-lookup"><span data-stu-id="24e05-145">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="24e05-146">Azure Key Vault noms de secrets sont limités aux caractères alphanumériques et aux tirets.</span><span class="sxs-lookup"><span data-stu-id="24e05-146">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="24e05-147">Les valeurs hiérarchiques (sections de configuration) utilisent `--` (deux tirets) comme séparateur.</span><span class="sxs-lookup"><span data-stu-id="24e05-147">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="24e05-148">Les signes deux-points, qui sont normalement utilisés pour délimiter une section d’une sous-clé dans [ASP.net Core configuration](xref:fundamentals/configuration/index), ne sont pas autorisés dans les noms de secrets du coffre de clés.</span><span class="sxs-lookup"><span data-stu-id="24e05-148">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="24e05-149">Par conséquent, deux tirets sont utilisés et permutés pour un signe deux-points lorsque les secrets sont chargés dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="24e05-149">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="24e05-150">Les secrets suivants sont destinés à être utilisés avec l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="24e05-150">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="24e05-151">Les valeurs incluent un `_prod` suffixe pour les distinguer des `_dev` valeurs de suffixe chargées dans l’environnement de développement des secrets d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="24e05-151">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="24e05-152">Remplacez `{KEY VAULT NAME}` par le nom du coffre de clés que vous avez créé à l’étape précédente :</span><span class="sxs-lookup"><span data-stu-id="24e05-152">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="24e05-153">Utiliser l’ID d’application et le certificat X. 509 pour les applications non hébergées sur Azure</span><span class="sxs-lookup"><span data-stu-id="24e05-153">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="24e05-154">Configurez Azure AD, Azure Key Vault et l’application pour qu’elle utilise un ID d’application Azure Active Directory et un certificat X. 509 pour s’authentifier auprès d’un coffre de clés **lorsque l’application est hébergée en dehors d’Azure**.</span><span class="sxs-lookup"><span data-stu-id="24e05-154">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="24e05-155">Pour plus d’informations, consultez [À propos des clés, des secrets et des certificats](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="24e05-155">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="24e05-156">Bien que l’utilisation d’un ID d’application et d’un certificat X. 509 soit prise en charge pour les applications hébergées dans Azure, nous vous recommandons [d’utiliser des identités gérées pour les ressources Azure](#use-managed-identities-for-azure-resources) lors de l’hébergement d’une application dans Azure.</span><span class="sxs-lookup"><span data-stu-id="24e05-156">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="24e05-157">Les identités gérées ne nécessitent pas le stockage d’un certificat dans l’application ou dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="24e05-157">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="24e05-158">L’exemple d’application utilise un ID d’application et un certificat X. 509 lorsque l' `#define` instruction en haut du fichier *Program.cs* a la valeur `Certificate` .</span><span class="sxs-lookup"><span data-stu-id="24e05-158">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="24e05-159">Créez un certificat d’archive PKCS # 12 (*. pfx*).</span><span class="sxs-lookup"><span data-stu-id="24e05-159">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="24e05-160">Les options de création de certificats incluent [Makecert sur Windows](/windows/desktop/seccrypto/makecert) et [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="24e05-160">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="24e05-161">Installez le certificat dans le magasin de certificats personnel de l’utilisateur actuel.</span><span class="sxs-lookup"><span data-stu-id="24e05-161">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="24e05-162">Le marquage de la clé comme étant exportable est facultatif.</span><span class="sxs-lookup"><span data-stu-id="24e05-162">Marking the key as exportable is optional.</span></span> <span data-ttu-id="24e05-163">Notez l’empreinte numérique du certificat, qui est utilisé plus loin dans ce processus.</span><span class="sxs-lookup"><span data-stu-id="24e05-163">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="24e05-164">Exportez le certificat PKCS # 12 (*. pfx*) sous la forme d’un certificat encodé der (*. cer*).</span><span class="sxs-lookup"><span data-stu-id="24e05-164">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="24e05-165">Inscrire l’application auprès de Azure AD (**inscriptions d’applications**).</span><span class="sxs-lookup"><span data-stu-id="24e05-165">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="24e05-166">Chargez le certificat codé DER (*. cer*) dans Azure ad :</span><span class="sxs-lookup"><span data-stu-id="24e05-166">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="24e05-167">Sélectionnez l’application dans Azure AD.</span><span class="sxs-lookup"><span data-stu-id="24e05-167">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="24e05-168">Accédez à **certificats & secrets**.</span><span class="sxs-lookup"><span data-stu-id="24e05-168">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="24e05-169">Sélectionnez **Télécharger le certificat** pour télécharger le certificat, qui contient la clé publique.</span><span class="sxs-lookup"><span data-stu-id="24e05-169">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="24e05-170">Un certificat. *CER*, *. pem* ou *. CRT* est acceptable.</span><span class="sxs-lookup"><span data-stu-id="24e05-170">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="24e05-171">Stockez le nom du coffre de clés, l’ID de l’application et l’empreinte numérique du certificat dans le fichier de l’application *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="24e05-171">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="24e05-172">Accédez à **coffres de clés** dans le portail Azure.</span><span class="sxs-lookup"><span data-stu-id="24e05-172">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="24e05-173">Sélectionnez le coffre de clés que vous avez créé dans la section [stockage secret dans l’environnement de production avec Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) .</span><span class="sxs-lookup"><span data-stu-id="24e05-173">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="24e05-174">Sélectionnez **Stratégies d’accès**.</span><span class="sxs-lookup"><span data-stu-id="24e05-174">Select **Access policies**.</span></span>
1. <span data-ttu-id="24e05-175">Sélectionnez **Ajouter une stratégie d’accès**.</span><span class="sxs-lookup"><span data-stu-id="24e05-175">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="24e05-176">Ouvrez les **autorisations secret** et fournissez à l’application les autorisations **obtenir** et **liste** .</span><span class="sxs-lookup"><span data-stu-id="24e05-176">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="24e05-177">Sélectionnez **Sélectionner un principal** et sélectionnez l’application inscrite par son nom.</span><span class="sxs-lookup"><span data-stu-id="24e05-177">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="24e05-178">Sélectionnez le bouton **Sélectionner**.</span><span class="sxs-lookup"><span data-stu-id="24e05-178">Select the **Select** button.</span></span>
1. <span data-ttu-id="24e05-179">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="24e05-179">Select **OK**.</span></span>
1. <span data-ttu-id="24e05-180">Sélectionnez **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="24e05-180">Select **Save**.</span></span>
1. <span data-ttu-id="24e05-181">Déployez l’application.</span><span class="sxs-lookup"><span data-stu-id="24e05-181">Deploy the app.</span></span>

<span data-ttu-id="24e05-182">L' `Certificate` exemple d’application obtient ses valeurs de configuration à partir du `IConfigurationRoot` même nom que le nom de la clé secrète :</span><span class="sxs-lookup"><span data-stu-id="24e05-182">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="24e05-183">Valeurs non hiérarchiques : la valeur de `SecretName` est obtenue avec `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="24e05-183">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="24e05-184">Valeurs hiérarchiques (sections) : utilisez `:` la notation (deux-points) ou la `GetSection` méthode d’extension.</span><span class="sxs-lookup"><span data-stu-id="24e05-184">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="24e05-185">Utilisez l’une des approches suivantes pour obtenir la valeur de configuration :</span><span class="sxs-lookup"><span data-stu-id="24e05-185">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="24e05-186">Le certificat X. 509 est géré par le système d’exploitation.</span><span class="sxs-lookup"><span data-stu-id="24e05-186">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="24e05-187">L’application appelle **AddAzureKeyVault** avec les valeurs fournies par le *appsettings.json* fichier :</span><span class="sxs-lookup"><span data-stu-id="24e05-187">The app calls **AddAzureKeyVault** with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=46-49)]

<span data-ttu-id="24e05-188">Exemples de valeurs</span><span class="sxs-lookup"><span data-stu-id="24e05-188">Example values:</span></span>

* <span data-ttu-id="24e05-189">Nom du coffre de clés : `contosovault`</span><span class="sxs-lookup"><span data-stu-id="24e05-189">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="24e05-190">ID de l’application : `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="24e05-190">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="24e05-191">Empreinte numérique du certificat : `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="24e05-191">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="24e05-192">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="24e05-192">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="24e05-193">Quand vous exécutez l’application, une page Web affiche les valeurs de secret chargées.</span><span class="sxs-lookup"><span data-stu-id="24e05-193">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="24e05-194">Dans l’environnement de développement, les valeurs secrètes sont chargées avec le `_dev` suffixe.</span><span class="sxs-lookup"><span data-stu-id="24e05-194">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="24e05-195">Dans l’environnement de production, les valeurs sont chargées avec le `_prod` suffixe.</span><span class="sxs-lookup"><span data-stu-id="24e05-195">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="24e05-196">Utiliser des identités gérées pour les ressources Azure</span><span class="sxs-lookup"><span data-stu-id="24e05-196">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="24e05-197">**Une application déployée sur Azure** peut tirer parti des [identités gérées pour les ressources Azure](/azure/active-directory/managed-identities-azure-resources/overview), ce qui permet à l’application de s’authentifier avec Azure Key Vault à l’aide de l’authentification Azure ad sans informations d’identification (ID d’application et mot de passe/clé secrète client) stockées dans l’application.</span><span class="sxs-lookup"><span data-stu-id="24e05-197">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="24e05-198">L’exemple d’application utilise des identités gérées pour les ressources Azure lorsque l' `#define` instruction en haut du fichier *Program.cs* a la valeur `Managed` .</span><span class="sxs-lookup"><span data-stu-id="24e05-198">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="24e05-199">Entrez le nom du coffre dans le fichier de l’application *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="24e05-199">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="24e05-200">L’exemple d’application n’a pas besoin d’un ID d’application et d’un mot de passe (clé secrète client) lorsqu’il est défini sur la `Managed` version. vous pouvez donc ignorer ces entrées de configuration.</span><span class="sxs-lookup"><span data-stu-id="24e05-200">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="24e05-201">L’application est déployée sur Azure et Azure authentifie l’application pour accéder à Azure Key Vault uniquement à l’aide du nom de coffre stocké dans le *appsettings.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="24e05-201">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="24e05-202">Déployez l’exemple d’application sur Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="24e05-202">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="24e05-203">Une application déployée sur Azure App Service est automatiquement inscrite auprès de Azure AD lors de la création du service.</span><span class="sxs-lookup"><span data-stu-id="24e05-203">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="24e05-204">Obtenez l’ID d’objet à partir du déploiement pour l’utiliser dans la commande suivante.</span><span class="sxs-lookup"><span data-stu-id="24e05-204">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="24e05-205">L’ID d’objet est affiché dans le Portail Azure sur le **Identity** panneau du App service.</span><span class="sxs-lookup"><span data-stu-id="24e05-205">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="24e05-206">À l’aide de Azure CLI et de l’ID d’objet de l’application, fournissez l’application avec `list` et les `get` autorisations d’accès au coffre de clés :</span><span class="sxs-lookup"><span data-stu-id="24e05-206">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="24e05-207">**Redémarrez l’application** à l’aide de Azure CLI, de PowerShell ou de l’portail Azure.</span><span class="sxs-lookup"><span data-stu-id="24e05-207">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="24e05-208">L’exemple d’application :</span><span class="sxs-lookup"><span data-stu-id="24e05-208">The sample app:</span></span>

* <span data-ttu-id="24e05-209">Crée une instance de la `DefaultAzureCredential` classe, les informations d’identification tente d’obtenir un jeton d’accès à partir de l’environnement pour les ressources Azure.</span><span class="sxs-lookup"><span data-stu-id="24e05-209">Creates an instance of the `DefaultAzureCredential` class, the credential attempts to obtain an access token from environment for Azure resources.</span></span>
* <span data-ttu-id="24e05-210">Une nouvelle [`Azure.Security.KeyVault.Secrets.Secrets`](/dotnet/api/azure.security.keyvault.secrets) est créée avec l' `DefaultAzureCredential` instance.</span><span class="sxs-lookup"><span data-stu-id="24e05-210">A new [`Azure.Security.KeyVault.Secrets.Secrets`](/dotnet/api/azure.security.keyvault.secrets) is created with the `DefaultAzureCredential` instance.</span></span>
* <span data-ttu-id="24e05-211">L' `Azure.Security.KeyVault.Secrets.Secrets` instance est utilisée avec une implémentation par défaut de `Azure.Extensions.Aspnetcore.Configuration.Secrets` qui charge toutes les valeurs de secret et remplace les doubles tirets ( `--` ) par des signes deux-points ( `:` ) dans les noms de clés.</span><span class="sxs-lookup"><span data-stu-id="24e05-211">The `Azure.Security.KeyVault.Secrets.Secrets` instance is used with a default implementation of `Azure.Extensions.Aspnetcore.Configuration.Secrets` that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=12-14)]

<span data-ttu-id="24e05-212">Exemple de valeur de nom de coffre de clés : `contosovault`</span><span class="sxs-lookup"><span data-stu-id="24e05-212">Key vault name example value: `contosovault`</span></span>

<span data-ttu-id="24e05-213">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="24e05-213">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="24e05-214">Quand vous exécutez l’application, une page Web affiche les valeurs de secret chargées.</span><span class="sxs-lookup"><span data-stu-id="24e05-214">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="24e05-215">Dans l’environnement de développement, les valeurs secrètes ont le `_dev` suffixe, car elles sont fournies par des secrets d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="24e05-215">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="24e05-216">Dans l’environnement de production, les valeurs sont chargées avec le `_prod` suffixe, car elles sont fournies par Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="24e05-216">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="24e05-217">Si vous recevez une `Access denied` erreur, vérifiez que l’application est inscrite auprès de Azure ad et que vous avez accès au coffre de clés.</span><span class="sxs-lookup"><span data-stu-id="24e05-217">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="24e05-218">Confirmez que vous avez redémarré le service dans Azure.</span><span class="sxs-lookup"><span data-stu-id="24e05-218">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="24e05-219">Pour plus d’informations sur l’utilisation du fournisseur avec une identité gérée et un pipeline Azure DevOps, consultez [créer une connexion de service Azure Resource Manager à une machine virtuelle avec une identité de service managée](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="24e05-219">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="24e05-220">Options de configuration</span><span class="sxs-lookup"><span data-stu-id="24e05-220">Configuration options</span></span>

<span data-ttu-id="24e05-221">AddAzureKeyVault peut accepter un AzureKeyVaultConfigurationOptions :</span><span class="sxs-lookup"><span data-stu-id="24e05-221">AddAzureKeyVault can accept an AzureKeyVaultConfigurationOptions:</span></span>

```csharp
config.AddAzureKeyVault(new SecretClient(new URI("Your Key Vault Endpoint"), new DefaultAzureCredential()),
                        new AzureKeyVaultConfigurationOptions())
    {
        ...
    });
```

| <span data-ttu-id="24e05-222">Propriété</span><span class="sxs-lookup"><span data-stu-id="24e05-222">Property</span></span>         | <span data-ttu-id="24e05-223">Description</span><span class="sxs-lookup"><span data-stu-id="24e05-223">Description</span></span> |
| ---------------- | ----------- |
| `Manager`        | <span data-ttu-id="24e05-224">`Azure.Extensions.Aspnetcore.Configuration.Secrets` instance utilisée pour contrôler le chargement du secret.</span><span class="sxs-lookup"><span data-stu-id="24e05-224">`Azure.Extensions.Aspnetcore.Configuration.Secrets` instance used to control secret loading.</span></span> |
| `ReloadInterval` | <span data-ttu-id="24e05-225">`Timespan` pour attendre entre les tentatives d’interrogation du coffre de clés pour les modifications.</span><span class="sxs-lookup"><span data-stu-id="24e05-225">`Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="24e05-226">La valeur par défaut est `null` (la configuration n’est pas rechargée).</span><span class="sxs-lookup"><span data-stu-id="24e05-226">The default value is `null` (configuration isn't reloaded).</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="24e05-227">Utiliser un préfixe de nom de clé</span><span class="sxs-lookup"><span data-stu-id="24e05-227">Use a key name prefix</span></span>

<span data-ttu-id="24e05-228">AddAzureKeyVault fournit une surcharge qui accepte une implémentation de `Azure.Extensions.Aspnetcore.Configuration.Secrets` , qui vous permet de contrôler la façon dont les secrets de coffre de clés sont convertis en clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="24e05-228">AddAzureKeyVault provides an overload that accepts an implementation of `Azure.Extensions.Aspnetcore.Configuration.Secrets`, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="24e05-229">Par exemple, vous pouvez implémenter l’interface pour charger des valeurs de secret en fonction d’une valeur de préfixe que vous fournissez au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="24e05-229">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="24e05-230">Cela vous permet, par exemple, de charger des secrets basés sur la version de l’application.</span><span class="sxs-lookup"><span data-stu-id="24e05-230">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="24e05-231">N’utilisez pas de préfixes sur des secrets de coffre de clés pour placer des secrets pour plusieurs applications dans le même coffre de clés ou pour placer des secrets d’environnement (par exemple, le *développement* et les secrets de *production* ) dans le même coffre.</span><span class="sxs-lookup"><span data-stu-id="24e05-231">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="24e05-232">Nous recommandons que les applications et les environnements de développement/production utilisent des coffres de clés distincts pour isoler les environnements d’application pour le plus haut niveau de sécurité.</span><span class="sxs-lookup"><span data-stu-id="24e05-232">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="24e05-233">Dans l’exemple suivant, un secret est établi dans le coffre de clés (et à l’aide de l’outil secret Manager pour l’environnement de développement) pour `5000-AppSecret` (les périodes ne sont pas autorisées dans les noms de secret de coffre de clés).</span><span class="sxs-lookup"><span data-stu-id="24e05-233">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="24e05-234">Ce secret représente un secret d’application pour la version 5.0.0.0 de l’application.</span><span class="sxs-lookup"><span data-stu-id="24e05-234">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="24e05-235">Pour une autre version de l’application, 5.1.0.0, un secret est ajouté au coffre de clés (et à l’aide de l’outil secret Manager) pour `5100-AppSecret` .</span><span class="sxs-lookup"><span data-stu-id="24e05-235">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="24e05-236">Chaque version de l’application charge sa valeur secrète avec version dans sa configuration en tant que `AppSecret` , en éliminant la version au fur et à mesure qu’elle charge le secret.</span><span class="sxs-lookup"><span data-stu-id="24e05-236">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="24e05-237">AddAzureKeyVault est appelé avec un personnalisé `Azure.Extensions.Aspnetcore.Configuration.Secrets` :</span><span class="sxs-lookup"><span data-stu-id="24e05-237">AddAzureKeyVault is called with a custom `Azure.Extensions.Aspnetcore.Configuration.Secrets`:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="24e05-238">L' `Azure.Extensions.Aspnetcore.Configuration.Secrets` implémentation réagit aux préfixes de version des secrets pour charger le secret approprié dans la configuration :</span><span class="sxs-lookup"><span data-stu-id="24e05-238">The `Azure.Extensions.Aspnetcore.Configuration.Secrets` implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="24e05-239">`Load` charge un secret lorsque son nom commence par le préfixe.</span><span class="sxs-lookup"><span data-stu-id="24e05-239">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="24e05-240">Les autres secrets ne sont pas chargés.</span><span class="sxs-lookup"><span data-stu-id="24e05-240">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="24e05-241">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="24e05-241">`GetKey`:</span></span>
  * <span data-ttu-id="24e05-242">Supprime le préfixe du nom de la clé secrète.</span><span class="sxs-lookup"><span data-stu-id="24e05-242">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="24e05-243">Remplace deux tirets dans n’importe quel nom par le `KeyDelimiter` , qui est le délimiteur utilisé dans la configuration (généralement un signe deux-points).</span><span class="sxs-lookup"><span data-stu-id="24e05-243">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="24e05-244">Azure Key Vault n’autorise pas les deux-points dans les noms de secrets.</span><span class="sxs-lookup"><span data-stu-id="24e05-244">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="24e05-245">La `Load` méthode est appelée par un algorithme de fournisseur qui itère au sein des secrets du coffre pour trouver ceux qui ont le préfixe de version.</span><span class="sxs-lookup"><span data-stu-id="24e05-245">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="24e05-246">Lorsqu’un préfixe de version est trouvé avec `Load` , l’algorithme utilise la `GetKey` méthode pour retourner le nom de configuration du nom de la clé secrète.</span><span class="sxs-lookup"><span data-stu-id="24e05-246">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="24e05-247">Il supprime le préfixe de version du nom du secret et retourne le reste du nom de secret pour le chargement dans les paires nom-valeur de la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="24e05-247">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="24e05-248">Lorsque cette approche est implémentée :</span><span class="sxs-lookup"><span data-stu-id="24e05-248">When this approach is implemented:</span></span>

1. <span data-ttu-id="24e05-249">Version de l’application spécifiée dans le fichier projet de l’application.</span><span class="sxs-lookup"><span data-stu-id="24e05-249">The app's version specified in the app's project file.</span></span> <span data-ttu-id="24e05-250">Dans l’exemple suivant, la version de l’application est définie sur `5.0.0.0` :</span><span class="sxs-lookup"><span data-stu-id="24e05-250">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="24e05-251">Vérifiez qu’une `<UserSecretsId>` propriété est présente dans le fichier projet de l’application, où `{GUID}` est un GUID fourni par l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="24e05-251">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="24e05-252">Enregistrez les secrets suivants localement à l’aide de l' [outil secret Manager](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="24e05-252">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="24e05-253">Les secrets sont enregistrés dans Azure Key Vault à l’aide des commandes de Azure CLI suivantes :</span><span class="sxs-lookup"><span data-stu-id="24e05-253">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="24e05-254">Lorsque l’application est exécutée, les secrets du coffre de clés sont chargés.</span><span class="sxs-lookup"><span data-stu-id="24e05-254">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="24e05-255">Le secret de chaîne de `5000-AppSecret` est mis en correspondance avec la version de l’application spécifiée dans le fichier projet de l’application ( `5.0.0.0` ).</span><span class="sxs-lookup"><span data-stu-id="24e05-255">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="24e05-256">La version `5000` (avec le tiret) est supprimée du nom de la clé.</span><span class="sxs-lookup"><span data-stu-id="24e05-256">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="24e05-257">Tout au long de l’application, la lecture de la configuration avec la clé `AppSecret` charge la valeur du secret.</span><span class="sxs-lookup"><span data-stu-id="24e05-257">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="24e05-258">Si la version de l’application est modifiée dans le fichier projet `5.1.0.0` et que l’application est à nouveau exécutée, la valeur secrète renvoyée se trouve `5.1.0.0_secret_value_dev` dans l’environnement de développement et `5.1.0.0_secret_value_prod` en production.</span><span class="sxs-lookup"><span data-stu-id="24e05-258">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="24e05-259">Vous pouvez également fournir votre propre <xref:Azure.Security.KeyVault.Secrets.SecretClient> implémentation à AddAzureKeyVault.</span><span class="sxs-lookup"><span data-stu-id="24e05-259">You can also provide your own <xref:Azure.Security.KeyVault.Secrets.SecretClient> implementation to AddAzureKeyVault.</span></span> <span data-ttu-id="24e05-260">Un client personnalisé permet de partager une seule instance du client dans l’application.</span><span class="sxs-lookup"><span data-stu-id="24e05-260">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="24e05-261">Lier un tableau à une classe</span><span class="sxs-lookup"><span data-stu-id="24e05-261">Bind an array to a class</span></span>

<span data-ttu-id="24e05-262">Le fournisseur est en mesure de lire les valeurs de configuration dans un tableau pour la liaison à un tableau POCO.</span><span class="sxs-lookup"><span data-stu-id="24e05-262">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="24e05-263">Lors de la lecture à partir d’une source de configuration qui autorise les clés à contenir des séparateurs de deux-points ( `:` ), un segment de clé numérique est utilisé pour distinguer les clés qui composent un tableau ( `:0:` , `:1:` , &hellip; `:{n}:` ).</span><span class="sxs-lookup"><span data-stu-id="24e05-263">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="24e05-264">Pour plus d’informations, consultez [Configuration : lier un tableau à une classe](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="24e05-264">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="24e05-265">Les clés de Azure Key Vault ne peuvent pas utiliser un signe deux-points comme séparateur.</span><span class="sxs-lookup"><span data-stu-id="24e05-265">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="24e05-266">L’approche décrite dans cette rubrique utilise des doubles tirets ( `--` ) comme séparateur pour les valeurs hiérarchiques (sections).</span><span class="sxs-lookup"><span data-stu-id="24e05-266">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="24e05-267">Les clés de tableau sont stockées dans Azure Key Vault avec des doubles tirets et des segments de clé numériques ( `--0--` , `--1--` , &hellip; `--{n}--` ).</span><span class="sxs-lookup"><span data-stu-id="24e05-267">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="24e05-268">Examinez la configuration de fournisseur de journalisation [Serilog](https://serilog.net/) suivante fournie par un fichier JSON.</span><span class="sxs-lookup"><span data-stu-id="24e05-268">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="24e05-269">Deux littéraux d’objet définis dans le `WriteTo` tableau reflètent deux *récepteurs* Serilog, qui décrivent les destinations pour la sortie de journalisation :</span><span class="sxs-lookup"><span data-stu-id="24e05-269">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="24e05-270">La configuration indiquée dans le fichier JSON précédent est stockée dans Azure Key Vault à l’aide de la notation à deux tirets ( `--` ) et des segments numériques :</span><span class="sxs-lookup"><span data-stu-id="24e05-270">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="24e05-271">Clé</span><span class="sxs-lookup"><span data-stu-id="24e05-271">Key</span></span> | <span data-ttu-id="24e05-272">Valeur</span><span class="sxs-lookup"><span data-stu-id="24e05-272">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="24e05-273">Recharger les secrets</span><span class="sxs-lookup"><span data-stu-id="24e05-273">Reload secrets</span></span>

<span data-ttu-id="24e05-274">Les secrets sont mis en cache jusqu’à ce que `IConfigurationRoot.Reload()` soit appelé.</span><span class="sxs-lookup"><span data-stu-id="24e05-274">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="24e05-275">Les secrets ayant expiré, désactivés et mis à jour dans le coffre de clés ne sont pas respectés par l’application tant qu' `Reload` elle n’est pas exécutée.</span><span class="sxs-lookup"><span data-stu-id="24e05-275">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="24e05-276">Secrets désactivés et arrivés à expiration</span><span class="sxs-lookup"><span data-stu-id="24e05-276">Disabled and expired secrets</span></span>

<span data-ttu-id="24e05-277">Les secrets désactivés et arrivés à expiration lèvent un <xref:Azure.RequestFailedException> .</span><span class="sxs-lookup"><span data-stu-id="24e05-277">Disabled and expired secrets throw a <xref:Azure.RequestFailedException>.</span></span> <span data-ttu-id="24e05-278">Pour empêcher l’application de se déclencher, fournissez la configuration à l’aide d’un autre fournisseur de configuration ou mettez à jour le secret désactivé ou expiré.</span><span class="sxs-lookup"><span data-stu-id="24e05-278">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="24e05-279">Dépanner</span><span class="sxs-lookup"><span data-stu-id="24e05-279">Troubleshoot</span></span>

<span data-ttu-id="24e05-280">Lorsque l’application ne parvient pas à charger la configuration à l’aide du fournisseur, un message d’erreur est écrit dans l' [infrastructure de journalisation ASP.net Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="24e05-280">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="24e05-281">Les conditions suivantes empêchent le chargement de la configuration :</span><span class="sxs-lookup"><span data-stu-id="24e05-281">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="24e05-282">L’application ou le certificat n’est pas configuré correctement dans Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="24e05-282">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="24e05-283">Le coffre de clés n’existe pas dans Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="24e05-283">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="24e05-284">L’application n’est pas autorisée à accéder au coffre de clés.</span><span class="sxs-lookup"><span data-stu-id="24e05-284">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="24e05-285">La stratégie d’accès n’inclut pas les `Get` `List` autorisations et.</span><span class="sxs-lookup"><span data-stu-id="24e05-285">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="24e05-286">Dans le coffre de clés, les données de configuration (paire nom-valeur) sont incorrectement nommées, manquantes, désactivées ou expirées.</span><span class="sxs-lookup"><span data-stu-id="24e05-286">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="24e05-287">L’application a un nom de coffre de clés ( `KeyVaultName` ), un ID d’Application Azure ad ( `AzureADApplicationId` ) ou un Azure ad d’empreinte de certificat () incorrect, `AzureADCertThumbprint` ou Azure ad DirectoryId ( `AzureADDirectoryId` ).</span><span class="sxs-lookup"><span data-stu-id="24e05-287">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`), or Azure AD DirectoryId (`AzureADDirectoryId`).</span></span>
* <span data-ttu-id="24e05-288">La clé de configuration (nom) est incorrecte dans l’application pour la valeur que vous essayez de charger.</span><span class="sxs-lookup"><span data-stu-id="24e05-288">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="24e05-289">Lors de l’ajout de la stratégie d’accès pour l’application au coffre de clés, la stratégie a été créée, mais le bouton **Enregistrer** n’a pas été sélectionné dans l’interface utilisateur des **stratégies d’accès** .</span><span class="sxs-lookup"><span data-stu-id="24e05-289">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="24e05-290">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="24e05-290">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="24e05-291">Microsoft Azure : Key Vault</span><span class="sxs-lookup"><span data-stu-id="24e05-291">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="24e05-292">Microsoft Azure : documentation Key Vault</span><span class="sxs-lookup"><span data-stu-id="24e05-292">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="24e05-293">Génération et transfert de clés protégées par HSM pour Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="24e05-293">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="24e05-294">KeyVaultClient, classe</span><span class="sxs-lookup"><span data-stu-id="24e05-294">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="24e05-295">Démarrage rapide : définir et récupérer un secret depuis Azure Key Vault à l’aide d’une application web .NET</span><span class="sxs-lookup"><span data-stu-id="24e05-295">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="24e05-296">Tutoriel - Utiliser Azure Key Vault avec une machine virtuelle Windows Azure et le langage .NET</span><span class="sxs-lookup"><span data-stu-id="24e05-296">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="24e05-297">Ce document explique comment utiliser le fournisseur de configuration [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pour charger des valeurs de configuration d’application à partir de Azure Key Vault secrets.</span><span class="sxs-lookup"><span data-stu-id="24e05-297">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="24e05-298">Azure Key Vault est un service basé sur le Cloud qui permet de protéger les clés de chiffrement et les secrets utilisés par les applications et les services.</span><span class="sxs-lookup"><span data-stu-id="24e05-298">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="24e05-299">Les scénarios courants d’utilisation de Azure Key Vault avec les applications ASP.NET Core sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="24e05-299">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="24e05-300">Contrôle de l’accès aux données de configuration sensibles.</span><span class="sxs-lookup"><span data-stu-id="24e05-300">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="24e05-301">Respect de la configuration requise pour les modules de sécurité matériels (HSM) certifiés FIPS 140-2 de niveau 2 lors du stockage des données de configuration.</span><span class="sxs-lookup"><span data-stu-id="24e05-301">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="24e05-302">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="24e05-302">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="24e05-303">.</span><span class="sxs-lookup"><span data-stu-id="24e05-303">Packages</span></span>

<span data-ttu-id="24e05-304">Ajoutez une référence de package à la [Microsoft.Extensions.Configfiguration. Package AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) .</span><span class="sxs-lookup"><span data-stu-id="24e05-304">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="24e05-305">Exemple d’application</span><span class="sxs-lookup"><span data-stu-id="24e05-305">Sample app</span></span>

<span data-ttu-id="24e05-306">L’exemple d’application s’exécute dans l’un des deux modes déterminés par l' `#define` instruction en haut du fichier *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="24e05-306">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="24e05-307">`Certificate`: Illustre l’utilisation d’un ID client Azure Key Vault et d’un certificat X. 509 pour accéder aux secrets stockés dans Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="24e05-307">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="24e05-308">Cette version de l’exemple peut être exécutée à partir de n’importe quel emplacement, déployée sur Azure App Service ou n’importe quel hôte capable de servir une application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="24e05-308">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="24e05-309">`Managed`: Montre comment utiliser [des identités gérées pour les ressources Azure](/azure/active-directory/managed-identities-azure-resources/overview) pour authentifier l’application afin de Azure Key Vault avec l’authentification Azure ad sans les informations d’identification stockées dans le code ou la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="24e05-309">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="24e05-310">Lorsque vous utilisez des identités gérées pour l’authentification, un ID d’application et un mot de passe de Azure AD (clé secrète client) ne sont pas requis.</span><span class="sxs-lookup"><span data-stu-id="24e05-310">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="24e05-311">La `Managed` version de l’exemple doit être déployée sur Azure.</span><span class="sxs-lookup"><span data-stu-id="24e05-311">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="24e05-312">Suivez les instructions de la section [utiliser les identités gérées pour les ressources Azure](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="24e05-312">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="24e05-313">Pour plus d’informations sur la configuration d’un exemple d’application à l’aide de directives de préprocesseur ( `#define` ), consultez <xref:index#preprocessor-directives-in-sample-code> .</span><span class="sxs-lookup"><span data-stu-id="24e05-313">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="24e05-314">Stockage secret dans l’environnement de développement</span><span class="sxs-lookup"><span data-stu-id="24e05-314">Secret storage in the Development environment</span></span>

<span data-ttu-id="24e05-315">Définissez les secrets localement à l’aide de l' [outil Gestionnaire de secret](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="24e05-315">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="24e05-316">Lorsque l’exemple d’application s’exécute sur l’ordinateur local dans l’environnement de développement, les secrets sont chargés à partir du magasin du gestionnaire de secret local.</span><span class="sxs-lookup"><span data-stu-id="24e05-316">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="24e05-317">L’outil Gestionnaire de secret nécessite une `<UserSecretsId>` propriété dans le fichier projet de l’application.</span><span class="sxs-lookup"><span data-stu-id="24e05-317">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="24e05-318">Définissez la valeur de la propriété ( `{GUID}` ) sur un GUID unique :</span><span class="sxs-lookup"><span data-stu-id="24e05-318">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="24e05-319">Les secrets sont créés en tant que paires nom-valeur.</span><span class="sxs-lookup"><span data-stu-id="24e05-319">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="24e05-320">Les valeurs hiérarchiques (sections de configuration) utilisent un `:` (deux-points) comme séparateur dans ASP.net Core noms de clé de [configuration](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="24e05-320">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="24e05-321">Le gestionnaire de secret est utilisé à partir d’une interface de commande ouverte à la [racine de contenu](xref:fundamentals/index#content-root)du projet, où `{SECRET NAME}` est le nom et `{SECRET VALUE}` est la valeur :</span><span class="sxs-lookup"><span data-stu-id="24e05-321">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="24e05-322">Exécutez les commandes suivantes dans une interface de commande à partir de la [racine de contenu](xref:fundamentals/index#content-root) du projet pour définir les secrets de l’exemple d’application :</span><span class="sxs-lookup"><span data-stu-id="24e05-322">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="24e05-323">Lorsque ces secrets sont stockés dans Azure Key Vault dans le [stockage secret dans l’environnement de production avec Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, le `_dev` suffixe est remplacé par `_prod` .</span><span class="sxs-lookup"><span data-stu-id="24e05-323">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="24e05-324">Le suffixe fournit un signal visuel dans la sortie de l’application qui indique la source des valeurs de configuration.</span><span class="sxs-lookup"><span data-stu-id="24e05-324">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="24e05-325">Stockage secret dans l’environnement de production avec Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="24e05-325">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="24e05-326">Les instructions fournies par le Guide de [démarrage rapide : définir et récupérer un secret à partir de Azure Key Vault à l’aide de Azure CLI](/azure/key-vault/quick-create-cli) rubrique sont résumées ici pour créer un Azure Key Vault et stocker des secrets utilisés par l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="24e05-326">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="24e05-327">Pour plus d’informations, reportez-vous à la rubrique.</span><span class="sxs-lookup"><span data-stu-id="24e05-327">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="24e05-328">Ouvrez Azure Cloud Shell à l’aide de l’une des méthodes suivantes dans la [portail Azure](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="24e05-328">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="24e05-329">Sélectionnez **Essayer** dans le coin supérieur droit d’un bloc de code.</span><span class="sxs-lookup"><span data-stu-id="24e05-329">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="24e05-330">Utilisez la chaîne de recherche « Azure CLI » dans la zone de texte.</span><span class="sxs-lookup"><span data-stu-id="24e05-330">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="24e05-331">Ouvrez Cloud Shell dans votre navigateur à l’aide du bouton **Launch Cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="24e05-331">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="24e05-332">Sélectionnez le bouton **Cloud Shell** dans le menu situé dans l’angle supérieur droit du portail Azure.</span><span class="sxs-lookup"><span data-stu-id="24e05-332">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="24e05-333">Pour plus d’informations, consultez [Azure CLI](/cli/azure/) et [Présentation des Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="24e05-333">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="24e05-334">Si vous n’êtes pas déjà authentifié, connectez-vous à l’aide de la `az login` commande.</span><span class="sxs-lookup"><span data-stu-id="24e05-334">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="24e05-335">Créez un groupe de ressources à l’aide de la commande suivante, où `{RESOURCE GROUP NAME}` est le nom du groupe de ressources pour le nouveau groupe de ressources et `{LOCATION}` est la région Azure (Datacenter) :</span><span class="sxs-lookup"><span data-stu-id="24e05-335">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="24e05-336">Créez un coffre de clés dans le groupe de ressources à l’aide de la commande suivante, où `{KEY VAULT NAME}` est le nom du nouveau coffre de clés et `{LOCATION}` est la région Azure (Datacenter) :</span><span class="sxs-lookup"><span data-stu-id="24e05-336">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="24e05-337">Créez des secrets dans le coffre de clés en tant que paires nom-valeur.</span><span class="sxs-lookup"><span data-stu-id="24e05-337">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="24e05-338">Azure Key Vault noms de secrets sont limités aux caractères alphanumériques et aux tirets.</span><span class="sxs-lookup"><span data-stu-id="24e05-338">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="24e05-339">Les valeurs hiérarchiques (sections de configuration) utilisent `--` (deux tirets) comme séparateur.</span><span class="sxs-lookup"><span data-stu-id="24e05-339">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="24e05-340">Les signes deux-points, qui sont normalement utilisés pour délimiter une section d’une sous-clé dans [ASP.net Core configuration](xref:fundamentals/configuration/index), ne sont pas autorisés dans les noms de secrets du coffre de clés.</span><span class="sxs-lookup"><span data-stu-id="24e05-340">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="24e05-341">Par conséquent, deux tirets sont utilisés et permutés pour un signe deux-points lorsque les secrets sont chargés dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="24e05-341">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="24e05-342">Les secrets suivants sont destinés à être utilisés avec l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="24e05-342">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="24e05-343">Les valeurs incluent un `_prod` suffixe pour les distinguer des `_dev` valeurs de suffixe chargées dans l’environnement de développement des secrets d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="24e05-343">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="24e05-344">Remplacez `{KEY VAULT NAME}` par le nom du coffre de clés que vous avez créé à l’étape précédente :</span><span class="sxs-lookup"><span data-stu-id="24e05-344">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="24e05-345">Utiliser l’ID d’application et le certificat X. 509 pour les applications non hébergées sur Azure</span><span class="sxs-lookup"><span data-stu-id="24e05-345">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="24e05-346">Configurez Azure AD, Azure Key Vault et l’application pour qu’elle utilise un ID d’application Azure Active Directory et un certificat X. 509 pour s’authentifier auprès d’un coffre de clés **lorsque l’application est hébergée en dehors d’Azure**.</span><span class="sxs-lookup"><span data-stu-id="24e05-346">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="24e05-347">Pour plus d’informations, consultez [À propos des clés, des secrets et des certificats](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="24e05-347">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="24e05-348">Bien que l’utilisation d’un ID d’application et d’un certificat X. 509 soit prise en charge pour les applications hébergées dans Azure, nous vous recommandons [d’utiliser des identités gérées pour les ressources Azure](#use-managed-identities-for-azure-resources) lors de l’hébergement d’une application dans Azure.</span><span class="sxs-lookup"><span data-stu-id="24e05-348">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="24e05-349">Les identités gérées ne nécessitent pas le stockage d’un certificat dans l’application ou dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="24e05-349">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="24e05-350">L’exemple d’application utilise un ID d’application et un certificat X. 509 lorsque l' `#define` instruction en haut du fichier *Program.cs* a la valeur `Certificate` .</span><span class="sxs-lookup"><span data-stu-id="24e05-350">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="24e05-351">Créez un certificat d’archive PKCS # 12 (*. pfx*).</span><span class="sxs-lookup"><span data-stu-id="24e05-351">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="24e05-352">Les options de création de certificats incluent [Makecert sur Windows](/windows/desktop/seccrypto/makecert) et [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="24e05-352">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="24e05-353">Installez le certificat dans le magasin de certificats personnel de l’utilisateur actuel.</span><span class="sxs-lookup"><span data-stu-id="24e05-353">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="24e05-354">Le marquage de la clé comme étant exportable est facultatif.</span><span class="sxs-lookup"><span data-stu-id="24e05-354">Marking the key as exportable is optional.</span></span> <span data-ttu-id="24e05-355">Notez l’empreinte numérique du certificat, qui est utilisé plus loin dans ce processus.</span><span class="sxs-lookup"><span data-stu-id="24e05-355">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="24e05-356">Exportez le certificat PKCS # 12 (*. pfx*) sous la forme d’un certificat encodé der (*. cer*).</span><span class="sxs-lookup"><span data-stu-id="24e05-356">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="24e05-357">Inscrire l’application auprès de Azure AD (**inscriptions d’applications**).</span><span class="sxs-lookup"><span data-stu-id="24e05-357">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="24e05-358">Chargez le certificat codé DER (*. cer*) dans Azure ad :</span><span class="sxs-lookup"><span data-stu-id="24e05-358">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="24e05-359">Sélectionnez l’application dans Azure AD.</span><span class="sxs-lookup"><span data-stu-id="24e05-359">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="24e05-360">Accédez à **certificats & secrets**.</span><span class="sxs-lookup"><span data-stu-id="24e05-360">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="24e05-361">Sélectionnez **Télécharger le certificat** pour télécharger le certificat, qui contient la clé publique.</span><span class="sxs-lookup"><span data-stu-id="24e05-361">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="24e05-362">Un certificat. *CER*, *. pem* ou *. CRT* est acceptable.</span><span class="sxs-lookup"><span data-stu-id="24e05-362">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="24e05-363">Stockez le nom du coffre de clés, l’ID de l’application et l’empreinte numérique du certificat dans le fichier de l’application *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="24e05-363">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="24e05-364">Accédez à **coffres de clés** dans le portail Azure.</span><span class="sxs-lookup"><span data-stu-id="24e05-364">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="24e05-365">Sélectionnez le coffre de clés que vous avez créé dans la section [stockage secret dans l’environnement de production avec Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) .</span><span class="sxs-lookup"><span data-stu-id="24e05-365">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="24e05-366">Sélectionnez **Stratégies d’accès**.</span><span class="sxs-lookup"><span data-stu-id="24e05-366">Select **Access policies**.</span></span>
1. <span data-ttu-id="24e05-367">Sélectionnez **Ajouter une stratégie d’accès**.</span><span class="sxs-lookup"><span data-stu-id="24e05-367">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="24e05-368">Ouvrez les **autorisations secret** et fournissez à l’application les autorisations **obtenir** et **liste** .</span><span class="sxs-lookup"><span data-stu-id="24e05-368">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="24e05-369">Sélectionnez **Sélectionner un principal** et sélectionnez l’application inscrite par son nom.</span><span class="sxs-lookup"><span data-stu-id="24e05-369">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="24e05-370">Sélectionnez le bouton **Sélectionner**.</span><span class="sxs-lookup"><span data-stu-id="24e05-370">Select the **Select** button.</span></span>
1. <span data-ttu-id="24e05-371">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="24e05-371">Select **OK**.</span></span>
1. <span data-ttu-id="24e05-372">Sélectionnez **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="24e05-372">Select **Save**.</span></span>
1. <span data-ttu-id="24e05-373">Déployez l’application.</span><span class="sxs-lookup"><span data-stu-id="24e05-373">Deploy the app.</span></span>

<span data-ttu-id="24e05-374">L' `Certificate` exemple d’application obtient ses valeurs de configuration à partir du `IConfigurationRoot` même nom que le nom de la clé secrète :</span><span class="sxs-lookup"><span data-stu-id="24e05-374">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="24e05-375">Valeurs non hiérarchiques : la valeur de `SecretName` est obtenue avec `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="24e05-375">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="24e05-376">Valeurs hiérarchiques (sections) : utilisez `:` la notation (deux-points) ou la `GetSection` méthode d’extension.</span><span class="sxs-lookup"><span data-stu-id="24e05-376">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="24e05-377">Utilisez l’une des approches suivantes pour obtenir la valeur de configuration :</span><span class="sxs-lookup"><span data-stu-id="24e05-377">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="24e05-378">Le certificat X. 509 est géré par le système d’exploitation.</span><span class="sxs-lookup"><span data-stu-id="24e05-378">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="24e05-379">L’application appelle <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> avec les valeurs fournies par le *appsettings.json* fichier :</span><span class="sxs-lookup"><span data-stu-id="24e05-379">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="24e05-380">Exemples de valeurs</span><span class="sxs-lookup"><span data-stu-id="24e05-380">Example values:</span></span>

* <span data-ttu-id="24e05-381">Nom du coffre de clés : `contosovault`</span><span class="sxs-lookup"><span data-stu-id="24e05-381">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="24e05-382">ID de l’application : `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="24e05-382">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="24e05-383">Empreinte numérique du certificat : `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="24e05-383">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="24e05-384">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="24e05-384">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="24e05-385">Quand vous exécutez l’application, une page Web affiche les valeurs de secret chargées.</span><span class="sxs-lookup"><span data-stu-id="24e05-385">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="24e05-386">Dans l’environnement de développement, les valeurs secrètes sont chargées avec le `_dev` suffixe.</span><span class="sxs-lookup"><span data-stu-id="24e05-386">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="24e05-387">Dans l’environnement de production, les valeurs sont chargées avec le `_prod` suffixe.</span><span class="sxs-lookup"><span data-stu-id="24e05-387">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="24e05-388">Utiliser des identités gérées pour les ressources Azure</span><span class="sxs-lookup"><span data-stu-id="24e05-388">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="24e05-389">**Une application déployée sur Azure** peut tirer parti des [identités gérées pour les ressources Azure](/azure/active-directory/managed-identities-azure-resources/overview), ce qui permet à l’application de s’authentifier avec Azure Key Vault à l’aide de l’authentification Azure ad sans informations d’identification (ID d’application et mot de passe/clé secrète client) stockées dans l’application.</span><span class="sxs-lookup"><span data-stu-id="24e05-389">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="24e05-390">L’exemple d’application utilise des identités gérées pour les ressources Azure lorsque l' `#define` instruction en haut du fichier *Program.cs* a la valeur `Managed` .</span><span class="sxs-lookup"><span data-stu-id="24e05-390">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="24e05-391">Entrez le nom du coffre dans le fichier de l’application *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="24e05-391">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="24e05-392">L’exemple d’application n’a pas besoin d’un ID d’application et d’un mot de passe (clé secrète client) lorsqu’il est défini sur la `Managed` version. vous pouvez donc ignorer ces entrées de configuration.</span><span class="sxs-lookup"><span data-stu-id="24e05-392">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="24e05-393">L’application est déployée sur Azure et Azure authentifie l’application pour accéder à Azure Key Vault uniquement à l’aide du nom de coffre stocké dans le *appsettings.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="24e05-393">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="24e05-394">Déployez l’exemple d’application sur Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="24e05-394">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="24e05-395">Une application déployée sur Azure App Service est automatiquement inscrite auprès de Azure AD lors de la création du service.</span><span class="sxs-lookup"><span data-stu-id="24e05-395">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="24e05-396">Obtenez l’ID d’objet à partir du déploiement pour l’utiliser dans la commande suivante.</span><span class="sxs-lookup"><span data-stu-id="24e05-396">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="24e05-397">L’ID d’objet est affiché dans le Portail Azure sur le **Identity** panneau du App service.</span><span class="sxs-lookup"><span data-stu-id="24e05-397">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="24e05-398">À l’aide de Azure CLI et de l’ID d’objet de l’application, fournissez l’application avec `list` et les `get` autorisations d’accès au coffre de clés :</span><span class="sxs-lookup"><span data-stu-id="24e05-398">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="24e05-399">**Redémarrez l’application** à l’aide de Azure CLI, de PowerShell ou de l’portail Azure.</span><span class="sxs-lookup"><span data-stu-id="24e05-399">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="24e05-400">L’exemple d’application :</span><span class="sxs-lookup"><span data-stu-id="24e05-400">The sample app:</span></span>

* <span data-ttu-id="24e05-401">Crée une instance de la `AzureServiceTokenProvider` classe sans chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="24e05-401">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="24e05-402">Lorsqu’une chaîne de connexion n’est pas fournie, le fournisseur tente d’obtenir un jeton d’accès à partir des identités gérées pour les ressources Azure.</span><span class="sxs-lookup"><span data-stu-id="24e05-402">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="24e05-403">Une nouvelle <xref:Microsoft.Azure.KeyVault.KeyVaultClient> est créée avec le `AzureServiceTokenProvider` rappel de jeton d’instance.</span><span class="sxs-lookup"><span data-stu-id="24e05-403">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="24e05-404">L' <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance est utilisée avec une implémentation par défaut de <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> qui charge toutes les valeurs de secret et remplace les doubles tirets ( `--` ) par des signes deux-points ( `:` ) dans les noms de clés.</span><span class="sxs-lookup"><span data-stu-id="24e05-404">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="24e05-405">Exemple de valeur de nom de coffre de clés : `contosovault`</span><span class="sxs-lookup"><span data-stu-id="24e05-405">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="24e05-406">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="24e05-406">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="24e05-407">Quand vous exécutez l’application, une page Web affiche les valeurs de secret chargées.</span><span class="sxs-lookup"><span data-stu-id="24e05-407">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="24e05-408">Dans l’environnement de développement, les valeurs secrètes ont le `_dev` suffixe, car elles sont fournies par des secrets d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="24e05-408">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="24e05-409">Dans l’environnement de production, les valeurs sont chargées avec le `_prod` suffixe, car elles sont fournies par Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="24e05-409">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="24e05-410">Si vous recevez une `Access denied` erreur, vérifiez que l’application est inscrite auprès de Azure ad et que vous avez accès au coffre de clés.</span><span class="sxs-lookup"><span data-stu-id="24e05-410">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="24e05-411">Confirmez que vous avez redémarré le service dans Azure.</span><span class="sxs-lookup"><span data-stu-id="24e05-411">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="24e05-412">Pour plus d’informations sur l’utilisation du fournisseur avec une identité gérée et un pipeline Azure DevOps, consultez [créer une connexion de service Azure Resource Manager à une machine virtuelle avec une identité de service managée](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="24e05-412">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="24e05-413">Utiliser un préfixe de nom de clé</span><span class="sxs-lookup"><span data-stu-id="24e05-413">Use a key name prefix</span></span>

<span data-ttu-id="24e05-414"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> fournit une surcharge qui accepte une implémentation de <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , qui vous permet de contrôler la façon dont les secrets de coffre de clés sont convertis en clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="24e05-414"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="24e05-415">Par exemple, vous pouvez implémenter l’interface pour charger des valeurs de secret en fonction d’une valeur de préfixe que vous fournissez au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="24e05-415">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="24e05-416">Cela vous permet, par exemple, de charger des secrets basés sur la version de l’application.</span><span class="sxs-lookup"><span data-stu-id="24e05-416">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="24e05-417">N’utilisez pas de préfixes sur des secrets de coffre de clés pour placer des secrets pour plusieurs applications dans le même coffre de clés ou pour placer des secrets d’environnement (par exemple, le *développement* et les secrets de *production* ) dans le même coffre.</span><span class="sxs-lookup"><span data-stu-id="24e05-417">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="24e05-418">Nous recommandons que les applications et les environnements de développement/production utilisent des coffres de clés distincts pour isoler les environnements d’application pour le plus haut niveau de sécurité.</span><span class="sxs-lookup"><span data-stu-id="24e05-418">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="24e05-419">Dans l’exemple suivant, un secret est établi dans le coffre de clés (et à l’aide de l’outil secret Manager pour l’environnement de développement) pour `5000-AppSecret` (les périodes ne sont pas autorisées dans les noms de secret de coffre de clés).</span><span class="sxs-lookup"><span data-stu-id="24e05-419">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="24e05-420">Ce secret représente un secret d’application pour la version 5.0.0.0 de l’application.</span><span class="sxs-lookup"><span data-stu-id="24e05-420">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="24e05-421">Pour une autre version de l’application, 5.1.0.0, un secret est ajouté au coffre de clés (et à l’aide de l’outil secret Manager) pour `5100-AppSecret` .</span><span class="sxs-lookup"><span data-stu-id="24e05-421">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="24e05-422">Chaque version de l’application charge sa valeur secrète avec version dans sa configuration en tant que `AppSecret` , en éliminant la version au fur et à mesure qu’elle charge le secret.</span><span class="sxs-lookup"><span data-stu-id="24e05-422">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="24e05-423"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> est appelé avec un personnalisé <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> :</span><span class="sxs-lookup"><span data-stu-id="24e05-423"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="24e05-424">L' <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implémentation réagit aux préfixes de version des secrets pour charger le secret approprié dans la configuration :</span><span class="sxs-lookup"><span data-stu-id="24e05-424">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="24e05-425">`Load` charge un secret lorsque son nom commence par le préfixe.</span><span class="sxs-lookup"><span data-stu-id="24e05-425">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="24e05-426">Les autres secrets ne sont pas chargés.</span><span class="sxs-lookup"><span data-stu-id="24e05-426">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="24e05-427">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="24e05-427">`GetKey`:</span></span>
  * <span data-ttu-id="24e05-428">Supprime le préfixe du nom de la clé secrète.</span><span class="sxs-lookup"><span data-stu-id="24e05-428">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="24e05-429">Remplace deux tirets dans n’importe quel nom par le `KeyDelimiter` , qui est le délimiteur utilisé dans la configuration (généralement un signe deux-points).</span><span class="sxs-lookup"><span data-stu-id="24e05-429">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="24e05-430">Azure Key Vault n’autorise pas les deux-points dans les noms de secrets.</span><span class="sxs-lookup"><span data-stu-id="24e05-430">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="24e05-431">La `Load` méthode est appelée par un algorithme de fournisseur qui itère au sein des secrets du coffre pour trouver ceux qui ont le préfixe de version.</span><span class="sxs-lookup"><span data-stu-id="24e05-431">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="24e05-432">Lorsqu’un préfixe de version est trouvé avec `Load` , l’algorithme utilise la `GetKey` méthode pour retourner le nom de configuration du nom de la clé secrète.</span><span class="sxs-lookup"><span data-stu-id="24e05-432">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="24e05-433">Il supprime le préfixe de version du nom du secret et retourne le reste du nom de secret pour le chargement dans les paires nom-valeur de la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="24e05-433">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="24e05-434">Lorsque cette approche est implémentée :</span><span class="sxs-lookup"><span data-stu-id="24e05-434">When this approach is implemented:</span></span>

1. <span data-ttu-id="24e05-435">Version de l’application spécifiée dans le fichier projet de l’application.</span><span class="sxs-lookup"><span data-stu-id="24e05-435">The app's version specified in the app's project file.</span></span> <span data-ttu-id="24e05-436">Dans l’exemple suivant, la version de l’application est définie sur `5.0.0.0` :</span><span class="sxs-lookup"><span data-stu-id="24e05-436">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="24e05-437">Vérifiez qu’une `<UserSecretsId>` propriété est présente dans le fichier projet de l’application, où `{GUID}` est un GUID fourni par l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="24e05-437">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="24e05-438">Enregistrez les secrets suivants localement à l’aide de l' [outil secret Manager](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="24e05-438">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="24e05-439">Les secrets sont enregistrés dans Azure Key Vault à l’aide des commandes de Azure CLI suivantes :</span><span class="sxs-lookup"><span data-stu-id="24e05-439">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="24e05-440">Lorsque l’application est exécutée, les secrets du coffre de clés sont chargés.</span><span class="sxs-lookup"><span data-stu-id="24e05-440">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="24e05-441">Le secret de chaîne de `5000-AppSecret` est mis en correspondance avec la version de l’application spécifiée dans le fichier projet de l’application ( `5.0.0.0` ).</span><span class="sxs-lookup"><span data-stu-id="24e05-441">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="24e05-442">La version `5000` (avec le tiret) est supprimée du nom de la clé.</span><span class="sxs-lookup"><span data-stu-id="24e05-442">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="24e05-443">Tout au long de l’application, la lecture de la configuration avec la clé `AppSecret` charge la valeur du secret.</span><span class="sxs-lookup"><span data-stu-id="24e05-443">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="24e05-444">Si la version de l’application est modifiée dans le fichier projet `5.1.0.0` et que l’application est à nouveau exécutée, la valeur secrète renvoyée se trouve `5.1.0.0_secret_value_dev` dans l’environnement de développement et `5.1.0.0_secret_value_prod` en production.</span><span class="sxs-lookup"><span data-stu-id="24e05-444">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="24e05-445">Vous pouvez également fournir votre propre <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implémentation à <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> .</span><span class="sxs-lookup"><span data-stu-id="24e05-445">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="24e05-446">Un client personnalisé permet de partager une seule instance du client dans l’application.</span><span class="sxs-lookup"><span data-stu-id="24e05-446">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="24e05-447">Lier un tableau à une classe</span><span class="sxs-lookup"><span data-stu-id="24e05-447">Bind an array to a class</span></span>

<span data-ttu-id="24e05-448">Le fournisseur est en mesure de lire les valeurs de configuration dans un tableau pour la liaison à un tableau POCO.</span><span class="sxs-lookup"><span data-stu-id="24e05-448">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="24e05-449">Lors de la lecture à partir d’une source de configuration qui autorise les clés à contenir des séparateurs de deux-points ( `:` ), un segment de clé numérique est utilisé pour distinguer les clés qui composent un tableau ( `:0:` , `:1:` , &hellip; `:{n}:` ).</span><span class="sxs-lookup"><span data-stu-id="24e05-449">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="24e05-450">Pour plus d’informations, consultez [Configuration : lier un tableau à une classe](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="24e05-450">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="24e05-451">Les clés de Azure Key Vault ne peuvent pas utiliser un signe deux-points comme séparateur.</span><span class="sxs-lookup"><span data-stu-id="24e05-451">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="24e05-452">L’approche décrite dans cette rubrique utilise des doubles tirets ( `--` ) comme séparateur pour les valeurs hiérarchiques (sections).</span><span class="sxs-lookup"><span data-stu-id="24e05-452">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="24e05-453">Les clés de tableau sont stockées dans Azure Key Vault avec des doubles tirets et des segments de clé numériques ( `--0--` , `--1--` , &hellip; `--{n}--` ).</span><span class="sxs-lookup"><span data-stu-id="24e05-453">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="24e05-454">Examinez la configuration de fournisseur de journalisation [Serilog](https://serilog.net/) suivante fournie par un fichier JSON.</span><span class="sxs-lookup"><span data-stu-id="24e05-454">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="24e05-455">Deux littéraux d’objet définis dans le `WriteTo` tableau reflètent deux *récepteurs* Serilog, qui décrivent les destinations pour la sortie de journalisation :</span><span class="sxs-lookup"><span data-stu-id="24e05-455">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="24e05-456">La configuration indiquée dans le fichier JSON précédent est stockée dans Azure Key Vault à l’aide de la notation à deux tirets ( `--` ) et des segments numériques :</span><span class="sxs-lookup"><span data-stu-id="24e05-456">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="24e05-457">Clé</span><span class="sxs-lookup"><span data-stu-id="24e05-457">Key</span></span> | <span data-ttu-id="24e05-458">Valeur</span><span class="sxs-lookup"><span data-stu-id="24e05-458">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="24e05-459">Recharger les secrets</span><span class="sxs-lookup"><span data-stu-id="24e05-459">Reload secrets</span></span>

<span data-ttu-id="24e05-460">Les secrets sont mis en cache jusqu’à ce que `IConfigurationRoot.Reload()` soit appelé.</span><span class="sxs-lookup"><span data-stu-id="24e05-460">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="24e05-461">Les secrets ayant expiré, désactivés et mis à jour dans le coffre de clés ne sont pas respectés par l’application tant qu' `Reload` elle n’est pas exécutée.</span><span class="sxs-lookup"><span data-stu-id="24e05-461">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="24e05-462">Secrets désactivés et arrivés à expiration</span><span class="sxs-lookup"><span data-stu-id="24e05-462">Disabled and expired secrets</span></span>

<span data-ttu-id="24e05-463">Les secrets désactivés et arrivés à expiration lèvent un <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> .</span><span class="sxs-lookup"><span data-stu-id="24e05-463">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="24e05-464">Pour empêcher l’application de se déclencher, fournissez la configuration à l’aide d’un autre fournisseur de configuration ou mettez à jour le secret désactivé ou expiré.</span><span class="sxs-lookup"><span data-stu-id="24e05-464">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="24e05-465">Dépanner</span><span class="sxs-lookup"><span data-stu-id="24e05-465">Troubleshoot</span></span>

<span data-ttu-id="24e05-466">Lorsque l’application ne parvient pas à charger la configuration à l’aide du fournisseur, un message d’erreur est écrit dans l' [infrastructure de journalisation ASP.net Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="24e05-466">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="24e05-467">Les conditions suivantes empêchent le chargement de la configuration :</span><span class="sxs-lookup"><span data-stu-id="24e05-467">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="24e05-468">L’application ou le certificat n’est pas configuré correctement dans Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="24e05-468">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="24e05-469">Le coffre de clés n’existe pas dans Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="24e05-469">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="24e05-470">L’application n’est pas autorisée à accéder au coffre de clés.</span><span class="sxs-lookup"><span data-stu-id="24e05-470">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="24e05-471">La stratégie d’accès n’inclut pas les `Get` `List` autorisations et.</span><span class="sxs-lookup"><span data-stu-id="24e05-471">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="24e05-472">Dans le coffre de clés, les données de configuration (paire nom-valeur) sont incorrectement nommées, manquantes, désactivées ou expirées.</span><span class="sxs-lookup"><span data-stu-id="24e05-472">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="24e05-473">L’application a un nom de coffre de clés ( `KeyVaultName` ), un ID d’Application Azure ad ( `AzureADApplicationId` ) ou une empreinte de certificat Azure ad () incorrect `AzureADCertThumbprint` .</span><span class="sxs-lookup"><span data-stu-id="24e05-473">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="24e05-474">La clé de configuration (nom) est incorrecte dans l’application pour la valeur que vous essayez de charger.</span><span class="sxs-lookup"><span data-stu-id="24e05-474">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="24e05-475">Lors de l’ajout de la stratégie d’accès pour l’application au coffre de clés, la stratégie a été créée, mais le bouton **Enregistrer** n’a pas été sélectionné dans l’interface utilisateur des **stratégies d’accès** .</span><span class="sxs-lookup"><span data-stu-id="24e05-475">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="24e05-476">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="24e05-476">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="24e05-477">Microsoft Azure : Key Vault</span><span class="sxs-lookup"><span data-stu-id="24e05-477">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="24e05-478">Microsoft Azure : documentation Key Vault</span><span class="sxs-lookup"><span data-stu-id="24e05-478">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="24e05-479">Génération et transfert de clés protégées par HSM pour Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="24e05-479">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="24e05-480">KeyVaultClient, classe</span><span class="sxs-lookup"><span data-stu-id="24e05-480">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="24e05-481">Démarrage rapide : définir et récupérer un secret depuis Azure Key Vault à l’aide d’une application web .NET</span><span class="sxs-lookup"><span data-stu-id="24e05-481">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="24e05-482">Tutoriel - Utiliser Azure Key Vault avec une machine virtuelle Windows Azure et le langage .NET</span><span class="sxs-lookup"><span data-stu-id="24e05-482">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end
