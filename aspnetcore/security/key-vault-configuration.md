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
ms.openlocfilehash: 4b035fe59b8576eb387ddce67943386ccab55492
ms.sourcegitcommit: 8dfcd2b4be936950c228b4d98430622a04254cd7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/26/2020
ms.locfileid: "97792084"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="6ab6b-103">Fournisseur de configuration Azure Key Vault dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6ab6b-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="6ab6b-104">Par [Andrew Stanton-infirmière](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="6ab6b-104">By [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6ab6b-105">Ce document explique comment utiliser le fournisseur de configuration [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pour charger des valeurs de configuration d’application à partir de Azure Key Vault secrets.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-105">This document explains how to use the [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="6ab6b-106">Azure Key Vault est un service basé sur le Cloud qui permet de protéger les clés de chiffrement et les secrets utilisés par les applications et les services.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="6ab6b-107">Les scénarios courants d’utilisation de Azure Key Vault avec les applications ASP.NET Core sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="6ab6b-108">Contrôle de l’accès aux données de configuration sensibles.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="6ab6b-109">Respect de la configuration requise pour les modules de sécurité matériels (HSM) certifiés FIPS 140-2 de niveau 2 lors du stockage des données de configuration.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="6ab6b-110">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6ab6b-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="6ab6b-111">Paquets</span><span class="sxs-lookup"><span data-stu-id="6ab6b-111">Packages</span></span>

<span data-ttu-id="6ab6b-112">Ajoutez des références de package pour les packages suivants :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-112">Add package references for the following packages:</span></span>

* [<span data-ttu-id="6ab6b-113">Azure.Extensions.AspNetCore.Configfiguration. Secrète</span><span class="sxs-lookup"><span data-stu-id="6ab6b-113">Azure.Extensions.AspNetCore.Configuration.Secrets</span></span>](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.Configuration.Secrets)
* <span data-ttu-id="6ab6b-114">[Bleu.Identity](https://www.nuget.org/packages/Azure.Identity)</span><span class="sxs-lookup"><span data-stu-id="6ab6b-114">[Azure.Identity](https://www.nuget.org/packages/Azure.Identity)</span></span>

## <a name="sample-app"></a><span data-ttu-id="6ab6b-115">Exemple d'application</span><span class="sxs-lookup"><span data-stu-id="6ab6b-115">Sample app</span></span>

<span data-ttu-id="6ab6b-116">L’exemple d’application s’exécute dans l’un des deux modes déterminés par l' `#define` instruction en haut du fichier *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-116">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="6ab6b-117">`Certificate`: Illustre l’utilisation d’un ID client Azure Key Vault et d’un certificat X. 509 pour accéder aux secrets stockés dans Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-117">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="6ab6b-118">Cette version de l’exemple peut être exécutée à partir de n’importe quel emplacement, déployée sur Azure App Service ou n’importe quel hôte capable de servir une application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-118">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="6ab6b-119">`Managed`: Montre comment utiliser [des identités gérées pour les ressources Azure](/azure/active-directory/managed-identities-azure-resources/overview) pour authentifier l’application afin de Azure Key Vault avec l’authentification Azure ad sans les informations d’identification stockées dans le code ou la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-119">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="6ab6b-120">Lorsque vous utilisez des identités gérées pour l’authentification, un ID d’application et un mot de passe de Azure AD (clé secrète client) ne sont pas requis.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-120">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="6ab6b-121">La `Managed` version de l’exemple doit être déployée sur Azure.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-121">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="6ab6b-122">Suivez les instructions de la section [utiliser les identités gérées pour les ressources Azure](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="6ab6b-122">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="6ab6b-123">Pour plus d’informations sur la configuration d’un exemple d’application à l’aide de directives de préprocesseur ( `#define` ), consultez <xref:index#preprocessor-directives-in-sample-code> .</span><span class="sxs-lookup"><span data-stu-id="6ab6b-123">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="6ab6b-124">Stockage secret dans l’environnement de développement</span><span class="sxs-lookup"><span data-stu-id="6ab6b-124">Secret storage in the Development environment</span></span>

<span data-ttu-id="6ab6b-125">Définissez les secrets localement à l’aide de l' [outil Gestionnaire de secret](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="6ab6b-125">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="6ab6b-126">Lorsque l’exemple d’application s’exécute sur l’ordinateur local dans l’environnement de développement, les secrets sont chargés à partir du magasin de secrets d’utilisateur local.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-126">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local user secrets store.</span></span>

<span data-ttu-id="6ab6b-127">L’outil Gestionnaire de secret nécessite une `<UserSecretsId>` propriété dans le fichier projet de l’application.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-127">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="6ab6b-128">Définissez la valeur de la propriété ( `{GUID}` ) sur un GUID unique :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-128">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="6ab6b-129">Les secrets sont créés en tant que paires nom-valeur.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-129">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="6ab6b-130">Les valeurs hiérarchiques (sections de configuration) utilisent un `:` (deux-points) comme séparateur dans ASP.net Core noms de clé de [configuration](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="6ab6b-130">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="6ab6b-131">Le gestionnaire de secret est utilisé à partir d’une interface de commande ouverte à la [racine de contenu](xref:fundamentals/index#content-root)du projet, où `{SECRET NAME}` est le nom et `{SECRET VALUE}` est la valeur :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-131">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="6ab6b-132">Exécutez les commandes suivantes dans une interface de commande à partir de la [racine de contenu](xref:fundamentals/index#content-root) du projet pour définir les secrets de l’exemple d’application :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-132">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="6ab6b-133">Lorsque ces secrets sont stockés dans Azure Key Vault dans le [stockage secret dans l’environnement de production avec Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, le `_dev` suffixe est remplacé par `_prod` .</span><span class="sxs-lookup"><span data-stu-id="6ab6b-133">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="6ab6b-134">Le suffixe fournit un signal visuel dans la sortie de l’application qui indique la source des valeurs de configuration.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-134">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="6ab6b-135">Stockage secret dans l’environnement de production avec Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="6ab6b-135">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="6ab6b-136">Les instructions fournies par le Guide de [démarrage rapide : définir et récupérer un secret à partir de Azure Key Vault à l’aide de Azure CLI](/azure/key-vault/quick-create-cli) rubrique sont résumées ici pour créer un Azure Key Vault et stocker des secrets utilisés par l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-136">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="6ab6b-137">Pour plus d’informations, reportez-vous à la rubrique.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-137">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="6ab6b-138">Ouvrez Azure Cloud Shell à l’aide de l’une des méthodes suivantes dans la [portail Azure](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="6ab6b-138">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="6ab6b-139">Sélectionnez **Essayer** dans le coin supérieur droit d’un bloc de code.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-139">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="6ab6b-140">Utilisez la chaîne de recherche « Azure CLI » dans la zone de texte.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-140">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="6ab6b-141">Ouvrez Cloud Shell dans votre navigateur à l’aide du bouton **Launch Cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="6ab6b-141">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="6ab6b-142">Sélectionnez le bouton **Cloud Shell** dans le menu situé dans l’angle supérieur droit du portail Azure.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-142">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="6ab6b-143">Pour plus d’informations, consultez [Azure CLI](/cli/azure/) et [Présentation des Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="6ab6b-143">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="6ab6b-144">Si vous n’êtes pas déjà authentifié, connectez-vous à l’aide de la `az login` commande.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-144">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="6ab6b-145">Créez un groupe de ressources à l’aide de la commande suivante, où `{RESOURCE GROUP NAME}` est le nom du groupe de ressources pour le nouveau groupe de ressources et `{LOCATION}` est la région Azure (Datacenter) :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-145">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="6ab6b-146">Créez un coffre de clés dans le groupe de ressources à l’aide de la commande suivante, où `{KEY VAULT NAME}` est le nom du nouveau coffre de clés et `{LOCATION}` est la région Azure (Datacenter) :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-146">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="6ab6b-147">Créez des secrets dans le coffre de clés en tant que paires nom-valeur.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-147">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="6ab6b-148">Azure Key Vault noms de secrets sont limités aux caractères alphanumériques et aux tirets.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-148">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="6ab6b-149">Les valeurs hiérarchiques (sections de configuration) utilisent `--` (deux tirets) comme séparateur.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-149">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="6ab6b-150">Les signes deux-points, qui sont normalement utilisés pour délimiter une section d’une sous-clé dans [ASP.net Core configuration](xref:fundamentals/configuration/index), ne sont pas autorisés dans les noms de secrets du coffre de clés.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-150">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="6ab6b-151">Par conséquent, deux tirets sont utilisés et permutés pour un signe deux-points lorsque les secrets sont chargés dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-151">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="6ab6b-152">Les secrets suivants sont destinés à être utilisés avec l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-152">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="6ab6b-153">Les valeurs incluent un `_prod` suffixe pour les distinguer des `_dev` valeurs de suffixe chargées dans l’environnement de développement des secrets d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-153">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="6ab6b-154">Remplacez `{KEY VAULT NAME}` par le nom du coffre de clés que vous avez créé à l’étape précédente :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-154">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="6ab6b-155">Utiliser l’ID d’application et le certificat X. 509 pour les applications non hébergées sur Azure</span><span class="sxs-lookup"><span data-stu-id="6ab6b-155">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="6ab6b-156">Configurez Azure AD, Azure Key Vault et l’application pour qu’elle utilise un ID d’application Azure Active Directory et un certificat X. 509 pour s’authentifier auprès d’un coffre de clés **lorsque l’application est hébergée en dehors d’Azure**.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-156">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="6ab6b-157">Pour plus d’informations, consultez [À propos des clés, des secrets et des certificats](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="6ab6b-157">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="6ab6b-158">Bien que l’utilisation d’un ID d’application et d’un certificat X. 509 soit prise en charge pour les applications hébergées dans Azure, nous vous recommandons [d’utiliser des identités gérées pour les ressources Azure](#use-managed-identities-for-azure-resources) lors de l’hébergement d’une application dans Azure.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-158">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="6ab6b-159">Les identités gérées ne nécessitent pas le stockage d’un certificat dans l’application ou dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-159">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="6ab6b-160">L’exemple d’application utilise un ID d’application et un certificat X. 509 lorsque l' `#define` instruction en haut du fichier *Program.cs* a la valeur `Certificate` .</span><span class="sxs-lookup"><span data-stu-id="6ab6b-160">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="6ab6b-161">Créez un certificat d’archive PKCS # 12 (*. pfx*).</span><span class="sxs-lookup"><span data-stu-id="6ab6b-161">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="6ab6b-162">Les options de création de certificats incluent [Makecert sur Windows](/windows/desktop/seccrypto/makecert) et [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="6ab6b-162">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="6ab6b-163">Installez le certificat dans le magasin de certificats personnel de l’utilisateur actuel.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-163">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="6ab6b-164">Le marquage de la clé comme étant exportable est facultatif.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-164">Marking the key as exportable is optional.</span></span> <span data-ttu-id="6ab6b-165">Notez l’empreinte numérique du certificat, qui est utilisé plus loin dans ce processus.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-165">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="6ab6b-166">Exportez le certificat PKCS # 12 (*. pfx*) sous la forme d’un certificat encodé der (*. cer*).</span><span class="sxs-lookup"><span data-stu-id="6ab6b-166">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="6ab6b-167">Inscrire l’application auprès de Azure AD (**inscriptions d’applications**).</span><span class="sxs-lookup"><span data-stu-id="6ab6b-167">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="6ab6b-168">Chargez le certificat codé DER (*. cer*) dans Azure ad :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-168">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="6ab6b-169">Sélectionnez l’application dans Azure AD.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-169">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="6ab6b-170">Accédez à **certificats & secrets**.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-170">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="6ab6b-171">Sélectionnez **Télécharger le certificat** pour télécharger le certificat, qui contient la clé publique.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-171">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="6ab6b-172">Un certificat. *CER*, *. pem* ou *. CRT* est acceptable.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-172">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="6ab6b-173">Stockez le nom du coffre de clés, l’ID de l’application et l’empreinte numérique du certificat dans le fichier de l’application *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="6ab6b-173">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="6ab6b-174">Accédez à **coffres de clés** dans le portail Azure.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-174">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="6ab6b-175">Sélectionnez le coffre de clés que vous avez créé dans la section [stockage secret dans l’environnement de production avec Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) .</span><span class="sxs-lookup"><span data-stu-id="6ab6b-175">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="6ab6b-176">Sélectionnez **Stratégies d’accès**.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-176">Select **Access policies**.</span></span>
1. <span data-ttu-id="6ab6b-177">Sélectionnez **Ajouter une stratégie d’accès**.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-177">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="6ab6b-178">Ouvrez les **autorisations secret** et fournissez à l’application les autorisations **obtenir** et **liste** .</span><span class="sxs-lookup"><span data-stu-id="6ab6b-178">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="6ab6b-179">Sélectionnez **Sélectionner un principal** et sélectionnez l’application inscrite par son nom.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-179">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="6ab6b-180">Sélectionnez le bouton **Sélectionner**.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-180">Select the **Select** button.</span></span>
1. <span data-ttu-id="6ab6b-181">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-181">Select **OK**.</span></span>
1. <span data-ttu-id="6ab6b-182">Sélectionnez **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-182">Select **Save**.</span></span>
1. <span data-ttu-id="6ab6b-183">Déployez l’application.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-183">Deploy the app.</span></span>

<span data-ttu-id="6ab6b-184">L' `Certificate` exemple d’application obtient ses valeurs de configuration à partir du `IConfigurationRoot` même nom que le nom de la clé secrète :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-184">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="6ab6b-185">Valeurs non hiérarchiques : la valeur de `SecretName` est obtenue avec `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="6ab6b-185">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="6ab6b-186">Valeurs hiérarchiques (sections) : utilisez `:` la notation (deux-points) ou la `GetSection` méthode d’extension.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-186">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="6ab6b-187">Utilisez l’une des approches suivantes pour obtenir la valeur de configuration :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-187">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="6ab6b-188">Le certificat X. 509 est géré par le système d’exploitation.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-188">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="6ab6b-189">L’application appelle **AddAzureKeyVault** avec les valeurs fournies par le *appsettings.json* fichier :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-189">The app calls **AddAzureKeyVault** with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=46-49)]

<span data-ttu-id="6ab6b-190">Exemples de valeurs</span><span class="sxs-lookup"><span data-stu-id="6ab6b-190">Example values:</span></span>

* <span data-ttu-id="6ab6b-191">Nom du coffre de clés : `contosovault`</span><span class="sxs-lookup"><span data-stu-id="6ab6b-191">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="6ab6b-192">ID de l’application : `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="6ab6b-192">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="6ab6b-193">Empreinte numérique du certificat : `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="6ab6b-193">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="6ab6b-194">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="6ab6b-194">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="6ab6b-195">Quand vous exécutez l’application, une page Web affiche les valeurs de secret chargées.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-195">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="6ab6b-196">Dans l’environnement de développement, les valeurs secrètes sont chargées avec le `_dev` suffixe.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-196">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="6ab6b-197">Dans l’environnement de production, les valeurs sont chargées avec le `_prod` suffixe.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-197">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="6ab6b-198">Utiliser des identités gérées pour les ressources Azure</span><span class="sxs-lookup"><span data-stu-id="6ab6b-198">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="6ab6b-199">**Une application déployée sur Azure** peut tirer parti des [identités gérées pour les ressources Azure](/azure/active-directory/managed-identities-azure-resources/overview), ce qui permet à l’application de s’authentifier avec Azure Key Vault à l’aide de l’authentification Azure ad sans informations d’identification (ID d’application et mot de passe/clé secrète client) stockées dans l’application.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-199">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="6ab6b-200">L’exemple d’application utilise des identités gérées pour les ressources Azure lorsque l' `#define` instruction en haut du fichier *Program.cs* a la valeur `Managed` .</span><span class="sxs-lookup"><span data-stu-id="6ab6b-200">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="6ab6b-201">Entrez le nom du coffre dans le fichier de l’application *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="6ab6b-201">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="6ab6b-202">L’exemple d’application n’a pas besoin d’un ID d’application et d’un mot de passe (clé secrète client) lorsqu’il est défini sur la `Managed` version. vous pouvez donc ignorer ces entrées de configuration.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-202">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="6ab6b-203">L’application est déployée sur Azure et Azure authentifie l’application pour accéder à Azure Key Vault uniquement à l’aide du nom de coffre stocké dans le *appsettings.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-203">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="6ab6b-204">Déployez l’exemple d’application sur Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-204">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="6ab6b-205">Une application déployée sur Azure App Service est automatiquement inscrite auprès de Azure AD lors de la création du service.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-205">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="6ab6b-206">Obtenez l’ID d’objet à partir du déploiement pour l’utiliser dans la commande suivante.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-206">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="6ab6b-207">L’ID d’objet est affiché dans le Portail Azure sur le **Identity** panneau du App service.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-207">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="6ab6b-208">À l’aide de Azure CLI et de l’ID d’objet de l’application, fournissez l’application avec `list` et les `get` autorisations d’accès au coffre de clés :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-208">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="6ab6b-209">**Redémarrez l’application** à l’aide de Azure CLI, de PowerShell ou de l’portail Azure.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-209">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="6ab6b-210">L’exemple d’application :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-210">The sample app:</span></span>

* <span data-ttu-id="6ab6b-211">Crée une instance de la `DefaultAzureCredential` classe, les informations d’identification tente d’obtenir un jeton d’accès à partir de l’environnement pour les ressources Azure.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-211">Creates an instance of the `DefaultAzureCredential` class, the credential attempts to obtain an access token from environment for Azure resources.</span></span>
* <span data-ttu-id="6ab6b-212">Une nouvelle [`Azure.Security.KeyVault.Secrets.Secrets`](/dotnet/api/azure.security.keyvault.secrets) est créée avec l' `DefaultAzureCredential` instance.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-212">A new [`Azure.Security.KeyVault.Secrets.Secrets`](/dotnet/api/azure.security.keyvault.secrets) is created with the `DefaultAzureCredential` instance.</span></span>
* <span data-ttu-id="6ab6b-213">L' `Azure.Security.KeyVault.Secrets.Secrets` instance est utilisée avec une implémentation par défaut de `Azure.Extensions.AspNetCore.Configuration.Secrets` qui charge toutes les valeurs de secret et remplace les doubles tirets ( `--` ) par des signes deux-points ( `:` ) dans les noms de clés.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-213">The `Azure.Security.KeyVault.Secrets.Secrets` instance is used with a default implementation of `Azure.Extensions.AspNetCore.Configuration.Secrets` that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=12-14)]

<span data-ttu-id="6ab6b-214">Exemple de valeur de nom de coffre de clés : `contosovault`</span><span class="sxs-lookup"><span data-stu-id="6ab6b-214">Key vault name example value: `contosovault`</span></span>

<span data-ttu-id="6ab6b-215">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="6ab6b-215">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="6ab6b-216">Quand vous exécutez l’application, une page Web affiche les valeurs de secret chargées.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-216">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="6ab6b-217">Dans l’environnement de développement, les valeurs secrètes ont le `_dev` suffixe, car elles sont fournies par des secrets d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-217">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="6ab6b-218">Dans l’environnement de production, les valeurs sont chargées avec le `_prod` suffixe, car elles sont fournies par Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-218">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="6ab6b-219">Si vous recevez une `Access denied` erreur, vérifiez que l’application est inscrite auprès de Azure ad et que vous avez accès au coffre de clés.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-219">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="6ab6b-220">Confirmez que vous avez redémarré le service dans Azure.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-220">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="6ab6b-221">Pour plus d’informations sur l’utilisation du fournisseur avec une identité gérée et un pipeline Azure DevOps, consultez [créer une connexion de service Azure Resource Manager à une machine virtuelle avec une identité de service managée](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="6ab6b-221">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="6ab6b-222">Options de configuration</span><span class="sxs-lookup"><span data-stu-id="6ab6b-222">Configuration options</span></span>

<span data-ttu-id="6ab6b-223">AddAzureKeyVault peut accepter un AzureKeyVaultConfigurationOptions :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-223">AddAzureKeyVault can accept an AzureKeyVaultConfigurationOptions:</span></span>

```csharp
config.AddAzureKeyVault(new SecretClient(new URI("Your Key Vault Endpoint"), new DefaultAzureCredential()),
                        new AzureKeyVaultConfigurationOptions())
    {
        ...
    });
```

| <span data-ttu-id="6ab6b-224">Propriété</span><span class="sxs-lookup"><span data-stu-id="6ab6b-224">Property</span></span>         | <span data-ttu-id="6ab6b-225">Description</span><span class="sxs-lookup"><span data-stu-id="6ab6b-225">Description</span></span> |
| ---------------- | ----------- |
| `Manager`        | <span data-ttu-id="6ab6b-226">`Azure.Extensions.AspNetCore.Configuration.Secrets` instance utilisée pour contrôler le chargement du secret.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-226">`Azure.Extensions.AspNetCore.Configuration.Secrets` instance used to control secret loading.</span></span> |
| `ReloadInterval` | <span data-ttu-id="6ab6b-227">`Timespan` pour attendre entre les tentatives d’interrogation du coffre de clés pour les modifications.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-227">`Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="6ab6b-228">La valeur par défaut est `null` (la configuration n’est pas rechargée).</span><span class="sxs-lookup"><span data-stu-id="6ab6b-228">The default value is `null` (configuration isn't reloaded).</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="6ab6b-229">Utiliser un préfixe de nom de clé</span><span class="sxs-lookup"><span data-stu-id="6ab6b-229">Use a key name prefix</span></span>

<span data-ttu-id="6ab6b-230">AddAzureKeyVault fournit une surcharge qui accepte une implémentation de `Azure.Extensions.AspNetCore.Configuration.Secrets` , qui vous permet de contrôler la façon dont les secrets de coffre de clés sont convertis en clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-230">AddAzureKeyVault provides an overload that accepts an implementation of `Azure.Extensions.AspNetCore.Configuration.Secrets`, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="6ab6b-231">Par exemple, vous pouvez implémenter l’interface pour charger des valeurs de secret en fonction d’une valeur de préfixe que vous fournissez au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-231">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="6ab6b-232">Cela vous permet, par exemple, de charger des secrets basés sur la version de l’application.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-232">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="6ab6b-233">N’utilisez pas de préfixes sur des secrets de coffre de clés pour placer des secrets pour plusieurs applications dans le même coffre de clés ou pour placer des secrets d’environnement (par exemple, le *développement* et les secrets de *production* ) dans le même coffre.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-233">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="6ab6b-234">Nous recommandons que les applications et les environnements de développement/production utilisent des coffres de clés distincts pour isoler les environnements d’application pour le plus haut niveau de sécurité.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-234">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="6ab6b-235">Dans l’exemple suivant, un secret est établi dans le coffre de clés (et à l’aide de l’outil secret Manager pour l’environnement de développement) pour `5000-AppSecret` (les périodes ne sont pas autorisées dans les noms de secret de coffre de clés).</span><span class="sxs-lookup"><span data-stu-id="6ab6b-235">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="6ab6b-236">Ce secret représente un secret d’application pour la version 5.0.0.0 de l’application.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-236">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="6ab6b-237">Pour une autre version de l’application, 5.1.0.0, un secret est ajouté au coffre de clés (et à l’aide de l’outil secret Manager) pour `5100-AppSecret` .</span><span class="sxs-lookup"><span data-stu-id="6ab6b-237">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="6ab6b-238">Chaque version de l’application charge sa valeur secrète avec version dans sa configuration en tant que `AppSecret` , en éliminant la version au fur et à mesure qu’elle charge le secret.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-238">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="6ab6b-239">AddAzureKeyVault est appelé avec un personnalisé `Azure.Extensions.AspNetCore.Configuration.Secrets` :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-239">AddAzureKeyVault is called with a custom `Azure.Extensions.AspNetCore.Configuration.Secrets`:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="6ab6b-240">L' `Azure.Extensions.AspNetCore.Configuration.Secrets` implémentation réagit aux préfixes de version des secrets pour charger le secret approprié dans la configuration :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-240">The `Azure.Extensions.AspNetCore.Configuration.Secrets` implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="6ab6b-241">`Load` charge un secret lorsque son nom commence par le préfixe.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-241">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="6ab6b-242">Les autres secrets ne sont pas chargés.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-242">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="6ab6b-243">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="6ab6b-243">`GetKey`:</span></span>
  * <span data-ttu-id="6ab6b-244">Supprime le préfixe du nom de la clé secrète.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-244">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="6ab6b-245">Remplace deux tirets dans n’importe quel nom par le `KeyDelimiter` , qui est le délimiteur utilisé dans la configuration (généralement un signe deux-points).</span><span class="sxs-lookup"><span data-stu-id="6ab6b-245">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="6ab6b-246">Azure Key Vault n’autorise pas les deux-points dans les noms de secrets.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-246">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="6ab6b-247">La `Load` méthode est appelée par un algorithme de fournisseur qui itère au sein des secrets du coffre pour trouver ceux qui ont le préfixe de version.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-247">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="6ab6b-248">Lorsqu’un préfixe de version est trouvé avec `Load` , l’algorithme utilise la `GetKey` méthode pour retourner le nom de configuration du nom de la clé secrète.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-248">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="6ab6b-249">Il supprime le préfixe de version du nom du secret et retourne le reste du nom de secret pour le chargement dans les paires nom-valeur de la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-249">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="6ab6b-250">Lorsque cette approche est implémentée :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-250">When this approach is implemented:</span></span>

1. <span data-ttu-id="6ab6b-251">Version de l’application spécifiée dans le fichier projet de l’application.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-251">The app's version specified in the app's project file.</span></span> <span data-ttu-id="6ab6b-252">Dans l’exemple suivant, la version de l’application est définie sur `5.0.0.0` :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-252">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="6ab6b-253">Vérifiez qu’une `<UserSecretsId>` propriété est présente dans le fichier projet de l’application, où `{GUID}` est un GUID fourni par l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-253">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="6ab6b-254">Enregistrez les secrets suivants localement à l’aide de l' [outil secret Manager](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="6ab6b-254">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="6ab6b-255">Les secrets sont enregistrés dans Azure Key Vault à l’aide des commandes de Azure CLI suivantes :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-255">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="6ab6b-256">Lorsque l’application est exécutée, les secrets du coffre de clés sont chargés.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-256">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="6ab6b-257">Le secret de chaîne de `5000-AppSecret` est mis en correspondance avec la version de l’application spécifiée dans le fichier projet de l’application ( `5.0.0.0` ).</span><span class="sxs-lookup"><span data-stu-id="6ab6b-257">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="6ab6b-258">La version `5000` (avec le tiret) est supprimée du nom de la clé.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-258">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="6ab6b-259">Tout au long de l’application, la lecture de la configuration avec la clé `AppSecret` charge la valeur du secret.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-259">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="6ab6b-260">Si la version de l’application est modifiée dans le fichier projet `5.1.0.0` et que l’application est à nouveau exécutée, la valeur secrète renvoyée se trouve `5.1.0.0_secret_value_dev` dans l’environnement de développement et `5.1.0.0_secret_value_prod` en production.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-260">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="6ab6b-261">Vous pouvez également fournir votre propre <xref:Azure.Security.KeyVault.Secrets.SecretClient> implémentation à AddAzureKeyVault.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-261">You can also provide your own <xref:Azure.Security.KeyVault.Secrets.SecretClient> implementation to AddAzureKeyVault.</span></span> <span data-ttu-id="6ab6b-262">Un client personnalisé permet de partager une seule instance du client dans l’application.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-262">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="6ab6b-263">Lier un tableau à une classe</span><span class="sxs-lookup"><span data-stu-id="6ab6b-263">Bind an array to a class</span></span>

<span data-ttu-id="6ab6b-264">Le fournisseur est en mesure de lire les valeurs de configuration dans un tableau pour la liaison à un tableau POCO.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-264">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="6ab6b-265">Lors de la lecture à partir d’une source de configuration qui autorise les clés à contenir des séparateurs de deux-points ( `:` ), un segment de clé numérique est utilisé pour distinguer les clés qui composent un tableau ( `:0:` , `:1:` , &hellip; `:{n}:` ).</span><span class="sxs-lookup"><span data-stu-id="6ab6b-265">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="6ab6b-266">Pour plus d’informations, consultez [Configuration : lier un tableau à une classe](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="6ab6b-266">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="6ab6b-267">Les clés de Azure Key Vault ne peuvent pas utiliser un signe deux-points comme séparateur.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-267">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="6ab6b-268">L’approche décrite dans cette rubrique utilise des doubles tirets ( `--` ) comme séparateur pour les valeurs hiérarchiques (sections).</span><span class="sxs-lookup"><span data-stu-id="6ab6b-268">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="6ab6b-269">Les clés de tableau sont stockées dans Azure Key Vault avec des doubles tirets et des segments de clé numériques ( `--0--` , `--1--` , &hellip; `--{n}--` ).</span><span class="sxs-lookup"><span data-stu-id="6ab6b-269">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="6ab6b-270">Examinez la configuration de fournisseur de journalisation [Serilog](https://serilog.net/) suivante fournie par un fichier JSON.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-270">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="6ab6b-271">Deux littéraux d’objet définis dans le `WriteTo` tableau reflètent deux *récepteurs* Serilog, qui décrivent les destinations pour la sortie de journalisation :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-271">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="6ab6b-272">La configuration indiquée dans le fichier JSON précédent est stockée dans Azure Key Vault à l’aide de la notation à deux tirets ( `--` ) et des segments numériques :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-272">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="6ab6b-273">Clé</span><span class="sxs-lookup"><span data-stu-id="6ab6b-273">Key</span></span> | <span data-ttu-id="6ab6b-274">Valeur</span><span class="sxs-lookup"><span data-stu-id="6ab6b-274">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="6ab6b-275">Recharger les secrets</span><span class="sxs-lookup"><span data-stu-id="6ab6b-275">Reload secrets</span></span>

<span data-ttu-id="6ab6b-276">Les secrets sont mis en cache jusqu’à ce que `IConfigurationRoot.Reload()` soit appelé.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-276">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="6ab6b-277">Les secrets ayant expiré, désactivés et mis à jour dans le coffre de clés ne sont pas respectés par l’application tant qu' `Reload` elle n’est pas exécutée.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-277">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="6ab6b-278">Secrets désactivés et arrivés à expiration</span><span class="sxs-lookup"><span data-stu-id="6ab6b-278">Disabled and expired secrets</span></span>

<span data-ttu-id="6ab6b-279">Les secrets désactivés et arrivés à expiration lèvent un <xref:Azure.RequestFailedException> .</span><span class="sxs-lookup"><span data-stu-id="6ab6b-279">Disabled and expired secrets throw a <xref:Azure.RequestFailedException>.</span></span> <span data-ttu-id="6ab6b-280">Pour empêcher l’application de se déclencher, fournissez la configuration à l’aide d’un autre fournisseur de configuration ou mettez à jour le secret désactivé ou expiré.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-280">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="6ab6b-281">Dépanner</span><span class="sxs-lookup"><span data-stu-id="6ab6b-281">Troubleshoot</span></span>

<span data-ttu-id="6ab6b-282">Lorsque l’application ne parvient pas à charger la configuration à l’aide du fournisseur, un message d’erreur est écrit dans l' [infrastructure de journalisation ASP.net Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="6ab6b-282">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="6ab6b-283">Les conditions suivantes empêchent le chargement de la configuration :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-283">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="6ab6b-284">L’application ou le certificat n’est pas configuré correctement dans Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-284">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="6ab6b-285">Le coffre de clés n’existe pas dans Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-285">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="6ab6b-286">L’application n’est pas autorisée à accéder au coffre de clés.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-286">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="6ab6b-287">La stratégie d’accès n’inclut pas les `Get` `List` autorisations et.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-287">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="6ab6b-288">Dans le coffre de clés, les données de configuration (paire nom-valeur) sont incorrectement nommées, manquantes, désactivées ou expirées.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-288">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="6ab6b-289">L’application a un nom de coffre de clés ( `KeyVaultName` ), un ID d’Application Azure ad ( `AzureADApplicationId` ) ou un Azure ad d’empreinte de certificat () incorrect, `AzureADCertThumbprint` ou Azure ad DirectoryId ( `AzureADDirectoryId` ).</span><span class="sxs-lookup"><span data-stu-id="6ab6b-289">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`), or Azure AD DirectoryId (`AzureADDirectoryId`).</span></span>
* <span data-ttu-id="6ab6b-290">La clé de configuration (nom) est incorrecte dans l’application pour la valeur que vous essayez de charger.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-290">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="6ab6b-291">Lors de l’ajout de la stratégie d’accès pour l’application au coffre de clés, la stratégie a été créée, mais le bouton **Enregistrer** n’a pas été sélectionné dans l’interface utilisateur des **stratégies d’accès** .</span><span class="sxs-lookup"><span data-stu-id="6ab6b-291">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6ab6b-292">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="6ab6b-292">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="6ab6b-293">Microsoft Azure : Key Vault</span><span class="sxs-lookup"><span data-stu-id="6ab6b-293">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="6ab6b-294">Microsoft Azure : documentation Key Vault</span><span class="sxs-lookup"><span data-stu-id="6ab6b-294">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="6ab6b-295">Génération et transfert de clés protégées par HSM pour Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="6ab6b-295">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="6ab6b-296">KeyVaultClient, classe</span><span class="sxs-lookup"><span data-stu-id="6ab6b-296">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="6ab6b-297">Démarrage rapide : définir et récupérer un secret depuis Azure Key Vault à l’aide d’une application web .NET</span><span class="sxs-lookup"><span data-stu-id="6ab6b-297">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="6ab6b-298">Tutoriel - Utiliser Azure Key Vault avec une machine virtuelle Windows Azure et le langage .NET</span><span class="sxs-lookup"><span data-stu-id="6ab6b-298">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6ab6b-299">Ce document explique comment utiliser le fournisseur de configuration [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pour charger des valeurs de configuration d’application à partir de Azure Key Vault secrets.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-299">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="6ab6b-300">Azure Key Vault est un service basé sur le Cloud qui permet de protéger les clés de chiffrement et les secrets utilisés par les applications et les services.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-300">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="6ab6b-301">Les scénarios courants d’utilisation de Azure Key Vault avec les applications ASP.NET Core sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-301">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="6ab6b-302">Contrôle de l’accès aux données de configuration sensibles.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-302">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="6ab6b-303">Respect de la configuration requise pour les modules de sécurité matériels (HSM) certifiés FIPS 140-2 de niveau 2 lors du stockage des données de configuration.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-303">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="6ab6b-304">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6ab6b-304">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="6ab6b-305">Paquets</span><span class="sxs-lookup"><span data-stu-id="6ab6b-305">Packages</span></span>

<span data-ttu-id="6ab6b-306">Ajoutez une référence de package à la [Microsoft.Extensions.Configfiguration. Package AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) .</span><span class="sxs-lookup"><span data-stu-id="6ab6b-306">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="6ab6b-307">Exemple d'application</span><span class="sxs-lookup"><span data-stu-id="6ab6b-307">Sample app</span></span>

<span data-ttu-id="6ab6b-308">L’exemple d’application s’exécute dans l’un des deux modes déterminés par l' `#define` instruction en haut du fichier *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-308">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="6ab6b-309">`Certificate`: Illustre l’utilisation d’un ID client Azure Key Vault et d’un certificat X. 509 pour accéder aux secrets stockés dans Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-309">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="6ab6b-310">Cette version de l’exemple peut être exécutée à partir de n’importe quel emplacement, déployée sur Azure App Service ou n’importe quel hôte capable de servir une application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-310">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="6ab6b-311">`Managed`: Montre comment utiliser [des identités gérées pour les ressources Azure](/azure/active-directory/managed-identities-azure-resources/overview) pour authentifier l’application afin de Azure Key Vault avec l’authentification Azure ad sans les informations d’identification stockées dans le code ou la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-311">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="6ab6b-312">Lorsque vous utilisez des identités gérées pour l’authentification, un ID d’application et un mot de passe de Azure AD (clé secrète client) ne sont pas requis.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-312">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="6ab6b-313">La `Managed` version de l’exemple doit être déployée sur Azure.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-313">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="6ab6b-314">Suivez les instructions de la section [utiliser les identités gérées pour les ressources Azure](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="6ab6b-314">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="6ab6b-315">Pour plus d’informations sur la configuration d’un exemple d’application à l’aide de directives de préprocesseur ( `#define` ), consultez <xref:index#preprocessor-directives-in-sample-code> .</span><span class="sxs-lookup"><span data-stu-id="6ab6b-315">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="6ab6b-316">Stockage secret dans l’environnement de développement</span><span class="sxs-lookup"><span data-stu-id="6ab6b-316">Secret storage in the Development environment</span></span>

<span data-ttu-id="6ab6b-317">Définissez les secrets localement à l’aide de l' [outil Gestionnaire de secret](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="6ab6b-317">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="6ab6b-318">Lorsque l’exemple d’application s’exécute sur l’ordinateur local dans l’environnement de développement, les secrets sont chargés à partir du magasin de secrets d’utilisateur local.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-318">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local user secrets store.</span></span>

<span data-ttu-id="6ab6b-319">L’outil Gestionnaire de secret nécessite une `<UserSecretsId>` propriété dans le fichier projet de l’application.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-319">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="6ab6b-320">Définissez la valeur de la propriété ( `{GUID}` ) sur un GUID unique :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-320">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="6ab6b-321">Les secrets sont créés en tant que paires nom-valeur.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-321">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="6ab6b-322">Les valeurs hiérarchiques (sections de configuration) utilisent un `:` (deux-points) comme séparateur dans ASP.net Core noms de clé de [configuration](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="6ab6b-322">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="6ab6b-323">Le gestionnaire de secret est utilisé à partir d’une interface de commande ouverte à la [racine de contenu](xref:fundamentals/index#content-root)du projet, où `{SECRET NAME}` est le nom et `{SECRET VALUE}` est la valeur :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-323">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="6ab6b-324">Exécutez les commandes suivantes dans une interface de commande à partir de la [racine de contenu](xref:fundamentals/index#content-root) du projet pour définir les secrets de l’exemple d’application :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-324">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="6ab6b-325">Lorsque ces secrets sont stockés dans Azure Key Vault dans le [stockage secret dans l’environnement de production avec Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, le `_dev` suffixe est remplacé par `_prod` .</span><span class="sxs-lookup"><span data-stu-id="6ab6b-325">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="6ab6b-326">Le suffixe fournit un signal visuel dans la sortie de l’application qui indique la source des valeurs de configuration.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-326">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="6ab6b-327">Stockage secret dans l’environnement de production avec Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="6ab6b-327">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="6ab6b-328">Les instructions fournies par le Guide de [démarrage rapide : définir et récupérer un secret à partir de Azure Key Vault à l’aide de Azure CLI](/azure/key-vault/quick-create-cli) rubrique sont résumées ici pour créer un Azure Key Vault et stocker des secrets utilisés par l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-328">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="6ab6b-329">Pour plus d’informations, reportez-vous à la rubrique.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-329">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="6ab6b-330">Ouvrez Azure Cloud Shell à l’aide de l’une des méthodes suivantes dans la [portail Azure](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="6ab6b-330">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="6ab6b-331">Sélectionnez **Essayer** dans le coin supérieur droit d’un bloc de code.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-331">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="6ab6b-332">Utilisez la chaîne de recherche « Azure CLI » dans la zone de texte.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-332">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="6ab6b-333">Ouvrez Cloud Shell dans votre navigateur à l’aide du bouton **Launch Cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="6ab6b-333">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="6ab6b-334">Sélectionnez le bouton **Cloud Shell** dans le menu situé dans l’angle supérieur droit du portail Azure.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-334">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="6ab6b-335">Pour plus d’informations, consultez [Azure CLI](/cli/azure/) et [Présentation des Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="6ab6b-335">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="6ab6b-336">Si vous n’êtes pas déjà authentifié, connectez-vous à l’aide de la `az login` commande.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-336">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="6ab6b-337">Créez un groupe de ressources à l’aide de la commande suivante, où `{RESOURCE GROUP NAME}` est le nom du groupe de ressources pour le nouveau groupe de ressources et `{LOCATION}` est la région Azure (Datacenter) :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-337">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="6ab6b-338">Créez un coffre de clés dans le groupe de ressources à l’aide de la commande suivante, où `{KEY VAULT NAME}` est le nom du nouveau coffre de clés et `{LOCATION}` est la région Azure (Datacenter) :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-338">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="6ab6b-339">Créez des secrets dans le coffre de clés en tant que paires nom-valeur.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-339">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="6ab6b-340">Azure Key Vault noms de secrets sont limités aux caractères alphanumériques et aux tirets.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-340">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="6ab6b-341">Les valeurs hiérarchiques (sections de configuration) utilisent `--` (deux tirets) comme séparateur.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-341">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="6ab6b-342">Les signes deux-points, qui sont normalement utilisés pour délimiter une section d’une sous-clé dans [ASP.net Core configuration](xref:fundamentals/configuration/index), ne sont pas autorisés dans les noms de secrets du coffre de clés.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-342">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="6ab6b-343">Par conséquent, deux tirets sont utilisés et permutés pour un signe deux-points lorsque les secrets sont chargés dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-343">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="6ab6b-344">Les secrets suivants sont destinés à être utilisés avec l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-344">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="6ab6b-345">Les valeurs incluent un `_prod` suffixe pour les distinguer des `_dev` valeurs de suffixe chargées dans l’environnement de développement des secrets d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-345">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="6ab6b-346">Remplacez `{KEY VAULT NAME}` par le nom du coffre de clés que vous avez créé à l’étape précédente :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-346">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="6ab6b-347">Utiliser l’ID d’application et le certificat X. 509 pour les applications non hébergées sur Azure</span><span class="sxs-lookup"><span data-stu-id="6ab6b-347">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="6ab6b-348">Configurez Azure AD, Azure Key Vault et l’application pour qu’elle utilise un ID d’application Azure Active Directory et un certificat X. 509 pour s’authentifier auprès d’un coffre de clés **lorsque l’application est hébergée en dehors d’Azure**.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-348">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="6ab6b-349">Pour plus d’informations, consultez [À propos des clés, des secrets et des certificats](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="6ab6b-349">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="6ab6b-350">Bien que l’utilisation d’un ID d’application et d’un certificat X. 509 soit prise en charge pour les applications hébergées dans Azure, nous vous recommandons [d’utiliser des identités gérées pour les ressources Azure](#use-managed-identities-for-azure-resources) lors de l’hébergement d’une application dans Azure.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-350">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="6ab6b-351">Les identités gérées ne nécessitent pas le stockage d’un certificat dans l’application ou dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-351">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="6ab6b-352">L’exemple d’application utilise un ID d’application et un certificat X. 509 lorsque l' `#define` instruction en haut du fichier *Program.cs* a la valeur `Certificate` .</span><span class="sxs-lookup"><span data-stu-id="6ab6b-352">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="6ab6b-353">Créez un certificat d’archive PKCS # 12 (*. pfx*).</span><span class="sxs-lookup"><span data-stu-id="6ab6b-353">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="6ab6b-354">Les options de création de certificats incluent [Makecert sur Windows](/windows/desktop/seccrypto/makecert) et [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="6ab6b-354">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="6ab6b-355">Installez le certificat dans le magasin de certificats personnel de l’utilisateur actuel.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-355">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="6ab6b-356">Le marquage de la clé comme étant exportable est facultatif.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-356">Marking the key as exportable is optional.</span></span> <span data-ttu-id="6ab6b-357">Notez l’empreinte numérique du certificat, qui est utilisé plus loin dans ce processus.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-357">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="6ab6b-358">Exportez le certificat PKCS # 12 (*. pfx*) sous la forme d’un certificat encodé der (*. cer*).</span><span class="sxs-lookup"><span data-stu-id="6ab6b-358">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="6ab6b-359">Inscrire l’application auprès de Azure AD (**inscriptions d’applications**).</span><span class="sxs-lookup"><span data-stu-id="6ab6b-359">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="6ab6b-360">Chargez le certificat codé DER (*. cer*) dans Azure ad :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-360">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="6ab6b-361">Sélectionnez l’application dans Azure AD.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-361">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="6ab6b-362">Accédez à **certificats & secrets**.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-362">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="6ab6b-363">Sélectionnez **Télécharger le certificat** pour télécharger le certificat, qui contient la clé publique.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-363">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="6ab6b-364">Un certificat. *CER*, *. pem* ou *. CRT* est acceptable.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-364">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="6ab6b-365">Stockez le nom du coffre de clés, l’ID de l’application et l’empreinte numérique du certificat dans le fichier de l’application *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="6ab6b-365">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="6ab6b-366">Accédez à **coffres de clés** dans le portail Azure.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-366">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="6ab6b-367">Sélectionnez le coffre de clés que vous avez créé dans la section [stockage secret dans l’environnement de production avec Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) .</span><span class="sxs-lookup"><span data-stu-id="6ab6b-367">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="6ab6b-368">Sélectionnez **Stratégies d’accès**.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-368">Select **Access policies**.</span></span>
1. <span data-ttu-id="6ab6b-369">Sélectionnez **Ajouter une stratégie d’accès**.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-369">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="6ab6b-370">Ouvrez les **autorisations secret** et fournissez à l’application les autorisations **obtenir** et **liste** .</span><span class="sxs-lookup"><span data-stu-id="6ab6b-370">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="6ab6b-371">Sélectionnez **Sélectionner un principal** et sélectionnez l’application inscrite par son nom.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-371">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="6ab6b-372">Sélectionnez le bouton **Sélectionner**.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-372">Select the **Select** button.</span></span>
1. <span data-ttu-id="6ab6b-373">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-373">Select **OK**.</span></span>
1. <span data-ttu-id="6ab6b-374">Sélectionnez **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-374">Select **Save**.</span></span>
1. <span data-ttu-id="6ab6b-375">Déployez l’application.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-375">Deploy the app.</span></span>

<span data-ttu-id="6ab6b-376">L' `Certificate` exemple d’application obtient ses valeurs de configuration à partir du `IConfigurationRoot` même nom que le nom de la clé secrète :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-376">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="6ab6b-377">Valeurs non hiérarchiques : la valeur de `SecretName` est obtenue avec `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="6ab6b-377">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="6ab6b-378">Valeurs hiérarchiques (sections) : utilisez `:` la notation (deux-points) ou la `GetSection` méthode d’extension.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-378">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="6ab6b-379">Utilisez l’une des approches suivantes pour obtenir la valeur de configuration :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-379">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="6ab6b-380">Le certificat X. 509 est géré par le système d’exploitation.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-380">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="6ab6b-381">L’application appelle <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> avec les valeurs fournies par le *appsettings.json* fichier :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-381">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="6ab6b-382">Exemples de valeurs</span><span class="sxs-lookup"><span data-stu-id="6ab6b-382">Example values:</span></span>

* <span data-ttu-id="6ab6b-383">Nom du coffre de clés : `contosovault`</span><span class="sxs-lookup"><span data-stu-id="6ab6b-383">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="6ab6b-384">ID de l’application : `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="6ab6b-384">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="6ab6b-385">Empreinte numérique du certificat : `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="6ab6b-385">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="6ab6b-386">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="6ab6b-386">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="6ab6b-387">Quand vous exécutez l’application, une page Web affiche les valeurs de secret chargées.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-387">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="6ab6b-388">Dans l’environnement de développement, les valeurs secrètes sont chargées avec le `_dev` suffixe.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-388">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="6ab6b-389">Dans l’environnement de production, les valeurs sont chargées avec le `_prod` suffixe.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-389">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="6ab6b-390">Utiliser des identités gérées pour les ressources Azure</span><span class="sxs-lookup"><span data-stu-id="6ab6b-390">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="6ab6b-391">**Une application déployée sur Azure** peut tirer parti des [identités gérées pour les ressources Azure](/azure/active-directory/managed-identities-azure-resources/overview), ce qui permet à l’application de s’authentifier avec Azure Key Vault à l’aide de l’authentification Azure ad sans informations d’identification (ID d’application et mot de passe/clé secrète client) stockées dans l’application.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-391">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="6ab6b-392">L’exemple d’application utilise des identités gérées pour les ressources Azure lorsque l' `#define` instruction en haut du fichier *Program.cs* a la valeur `Managed` .</span><span class="sxs-lookup"><span data-stu-id="6ab6b-392">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="6ab6b-393">Entrez le nom du coffre dans le fichier de l’application *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="6ab6b-393">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="6ab6b-394">L’exemple d’application n’a pas besoin d’un ID d’application et d’un mot de passe (clé secrète client) lorsqu’il est défini sur la `Managed` version. vous pouvez donc ignorer ces entrées de configuration.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-394">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="6ab6b-395">L’application est déployée sur Azure et Azure authentifie l’application pour accéder à Azure Key Vault uniquement à l’aide du nom de coffre stocké dans le *appsettings.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-395">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="6ab6b-396">Déployez l’exemple d’application sur Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-396">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="6ab6b-397">Une application déployée sur Azure App Service est automatiquement inscrite auprès de Azure AD lors de la création du service.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-397">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="6ab6b-398">Obtenez l’ID d’objet à partir du déploiement pour l’utiliser dans la commande suivante.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-398">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="6ab6b-399">L’ID d’objet est affiché dans le Portail Azure sur le **Identity** panneau du App service.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-399">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="6ab6b-400">À l’aide de Azure CLI et de l’ID d’objet de l’application, fournissez l’application avec `list` et les `get` autorisations d’accès au coffre de clés :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-400">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="6ab6b-401">**Redémarrez l’application** à l’aide de Azure CLI, de PowerShell ou de l’portail Azure.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-401">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="6ab6b-402">L’exemple d’application :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-402">The sample app:</span></span>

* <span data-ttu-id="6ab6b-403">Crée une instance de la `AzureServiceTokenProvider` classe sans chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-403">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="6ab6b-404">Lorsqu’une chaîne de connexion n’est pas fournie, le fournisseur tente d’obtenir un jeton d’accès à partir des identités gérées pour les ressources Azure.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-404">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="6ab6b-405">Une nouvelle <xref:Microsoft.Azure.KeyVault.KeyVaultClient> est créée avec le `AzureServiceTokenProvider` rappel de jeton d’instance.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-405">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="6ab6b-406">L' <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance est utilisée avec une implémentation par défaut de <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> qui charge toutes les valeurs de secret et remplace les doubles tirets ( `--` ) par des signes deux-points ( `:` ) dans les noms de clés.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-406">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="6ab6b-407">Exemple de valeur de nom de coffre de clés : `contosovault`</span><span class="sxs-lookup"><span data-stu-id="6ab6b-407">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="6ab6b-408">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="6ab6b-408">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="6ab6b-409">Quand vous exécutez l’application, une page Web affiche les valeurs de secret chargées.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-409">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="6ab6b-410">Dans l’environnement de développement, les valeurs secrètes ont le `_dev` suffixe, car elles sont fournies par des secrets d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-410">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="6ab6b-411">Dans l’environnement de production, les valeurs sont chargées avec le `_prod` suffixe, car elles sont fournies par Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-411">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="6ab6b-412">Si vous recevez une `Access denied` erreur, vérifiez que l’application est inscrite auprès de Azure ad et que vous avez accès au coffre de clés.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-412">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="6ab6b-413">Confirmez que vous avez redémarré le service dans Azure.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-413">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="6ab6b-414">Pour plus d’informations sur l’utilisation du fournisseur avec une identité gérée et un pipeline Azure DevOps, consultez [créer une connexion de service Azure Resource Manager à une machine virtuelle avec une identité de service managée](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="6ab6b-414">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="6ab6b-415">Utiliser un préfixe de nom de clé</span><span class="sxs-lookup"><span data-stu-id="6ab6b-415">Use a key name prefix</span></span>

<span data-ttu-id="6ab6b-416"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> fournit une surcharge qui accepte une implémentation de <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , qui vous permet de contrôler la façon dont les secrets de coffre de clés sont convertis en clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-416"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="6ab6b-417">Par exemple, vous pouvez implémenter l’interface pour charger des valeurs de secret en fonction d’une valeur de préfixe que vous fournissez au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-417">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="6ab6b-418">Cela vous permet, par exemple, de charger des secrets basés sur la version de l’application.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-418">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="6ab6b-419">N’utilisez pas de préfixes sur des secrets de coffre de clés pour placer des secrets pour plusieurs applications dans le même coffre de clés ou pour placer des secrets d’environnement (par exemple, le *développement* et les secrets de *production* ) dans le même coffre.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-419">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="6ab6b-420">Nous recommandons que les applications et les environnements de développement/production utilisent des coffres de clés distincts pour isoler les environnements d’application pour le plus haut niveau de sécurité.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-420">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="6ab6b-421">Dans l’exemple suivant, un secret est établi dans le coffre de clés (et à l’aide de l’outil secret Manager pour l’environnement de développement) pour `5000-AppSecret` (les périodes ne sont pas autorisées dans les noms de secret de coffre de clés).</span><span class="sxs-lookup"><span data-stu-id="6ab6b-421">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="6ab6b-422">Ce secret représente un secret d’application pour la version 5.0.0.0 de l’application.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-422">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="6ab6b-423">Pour une autre version de l’application, 5.1.0.0, un secret est ajouté au coffre de clés (et à l’aide de l’outil secret Manager) pour `5100-AppSecret` .</span><span class="sxs-lookup"><span data-stu-id="6ab6b-423">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="6ab6b-424">Chaque version de l’application charge sa valeur secrète avec version dans sa configuration en tant que `AppSecret` , en éliminant la version au fur et à mesure qu’elle charge le secret.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-424">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="6ab6b-425"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> est appelé avec un personnalisé <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-425"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="6ab6b-426">L' <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implémentation réagit aux préfixes de version des secrets pour charger le secret approprié dans la configuration :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-426">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="6ab6b-427">`Load` charge un secret lorsque son nom commence par le préfixe.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-427">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="6ab6b-428">Les autres secrets ne sont pas chargés.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-428">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="6ab6b-429">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="6ab6b-429">`GetKey`:</span></span>
  * <span data-ttu-id="6ab6b-430">Supprime le préfixe du nom de la clé secrète.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-430">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="6ab6b-431">Remplace deux tirets dans n’importe quel nom par le `KeyDelimiter` , qui est le délimiteur utilisé dans la configuration (généralement un signe deux-points).</span><span class="sxs-lookup"><span data-stu-id="6ab6b-431">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="6ab6b-432">Azure Key Vault n’autorise pas les deux-points dans les noms de secrets.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-432">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="6ab6b-433">La `Load` méthode est appelée par un algorithme de fournisseur qui itère au sein des secrets du coffre pour trouver ceux qui ont le préfixe de version.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-433">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="6ab6b-434">Lorsqu’un préfixe de version est trouvé avec `Load` , l’algorithme utilise la `GetKey` méthode pour retourner le nom de configuration du nom de la clé secrète.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-434">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="6ab6b-435">Il supprime le préfixe de version du nom du secret et retourne le reste du nom de secret pour le chargement dans les paires nom-valeur de la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-435">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="6ab6b-436">Lorsque cette approche est implémentée :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-436">When this approach is implemented:</span></span>

1. <span data-ttu-id="6ab6b-437">Version de l’application spécifiée dans le fichier projet de l’application.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-437">The app's version specified in the app's project file.</span></span> <span data-ttu-id="6ab6b-438">Dans l’exemple suivant, la version de l’application est définie sur `5.0.0.0` :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-438">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="6ab6b-439">Vérifiez qu’une `<UserSecretsId>` propriété est présente dans le fichier projet de l’application, où `{GUID}` est un GUID fourni par l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-439">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="6ab6b-440">Enregistrez les secrets suivants localement à l’aide de l' [outil secret Manager](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="6ab6b-440">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="6ab6b-441">Les secrets sont enregistrés dans Azure Key Vault à l’aide des commandes de Azure CLI suivantes :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-441">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="6ab6b-442">Lorsque l’application est exécutée, les secrets du coffre de clés sont chargés.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-442">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="6ab6b-443">Le secret de chaîne de `5000-AppSecret` est mis en correspondance avec la version de l’application spécifiée dans le fichier projet de l’application ( `5.0.0.0` ).</span><span class="sxs-lookup"><span data-stu-id="6ab6b-443">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="6ab6b-444">La version `5000` (avec le tiret) est supprimée du nom de la clé.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-444">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="6ab6b-445">Tout au long de l’application, la lecture de la configuration avec la clé `AppSecret` charge la valeur du secret.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-445">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="6ab6b-446">Si la version de l’application est modifiée dans le fichier projet `5.1.0.0` et que l’application est à nouveau exécutée, la valeur secrète renvoyée se trouve `5.1.0.0_secret_value_dev` dans l’environnement de développement et `5.1.0.0_secret_value_prod` en production.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-446">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="6ab6b-447">Vous pouvez également fournir votre propre <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implémentation à <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> .</span><span class="sxs-lookup"><span data-stu-id="6ab6b-447">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="6ab6b-448">Un client personnalisé permet de partager une seule instance du client dans l’application.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-448">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="6ab6b-449">Lier un tableau à une classe</span><span class="sxs-lookup"><span data-stu-id="6ab6b-449">Bind an array to a class</span></span>

<span data-ttu-id="6ab6b-450">Le fournisseur est en mesure de lire les valeurs de configuration dans un tableau pour la liaison à un tableau POCO.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-450">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="6ab6b-451">Lors de la lecture à partir d’une source de configuration qui autorise les clés à contenir des séparateurs de deux-points ( `:` ), un segment de clé numérique est utilisé pour distinguer les clés qui composent un tableau ( `:0:` , `:1:` , &hellip; `:{n}:` ).</span><span class="sxs-lookup"><span data-stu-id="6ab6b-451">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="6ab6b-452">Pour plus d’informations, consultez [Configuration : lier un tableau à une classe](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="6ab6b-452">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="6ab6b-453">Les clés de Azure Key Vault ne peuvent pas utiliser un signe deux-points comme séparateur.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-453">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="6ab6b-454">L’approche décrite dans cette rubrique utilise des doubles tirets ( `--` ) comme séparateur pour les valeurs hiérarchiques (sections).</span><span class="sxs-lookup"><span data-stu-id="6ab6b-454">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="6ab6b-455">Les clés de tableau sont stockées dans Azure Key Vault avec des doubles tirets et des segments de clé numériques ( `--0--` , `--1--` , &hellip; `--{n}--` ).</span><span class="sxs-lookup"><span data-stu-id="6ab6b-455">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="6ab6b-456">Examinez la configuration de fournisseur de journalisation [Serilog](https://serilog.net/) suivante fournie par un fichier JSON.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-456">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="6ab6b-457">Deux littéraux d’objet définis dans le `WriteTo` tableau reflètent deux *récepteurs* Serilog, qui décrivent les destinations pour la sortie de journalisation :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-457">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="6ab6b-458">La configuration indiquée dans le fichier JSON précédent est stockée dans Azure Key Vault à l’aide de la notation à deux tirets ( `--` ) et des segments numériques :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-458">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="6ab6b-459">Clé</span><span class="sxs-lookup"><span data-stu-id="6ab6b-459">Key</span></span> | <span data-ttu-id="6ab6b-460">Valeur</span><span class="sxs-lookup"><span data-stu-id="6ab6b-460">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="6ab6b-461">Recharger les secrets</span><span class="sxs-lookup"><span data-stu-id="6ab6b-461">Reload secrets</span></span>

<span data-ttu-id="6ab6b-462">Les secrets sont mis en cache jusqu’à ce que `IConfigurationRoot.Reload()` soit appelé.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-462">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="6ab6b-463">Les secrets ayant expiré, désactivés et mis à jour dans le coffre de clés ne sont pas respectés par l’application tant qu' `Reload` elle n’est pas exécutée.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-463">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="6ab6b-464">Secrets désactivés et arrivés à expiration</span><span class="sxs-lookup"><span data-stu-id="6ab6b-464">Disabled and expired secrets</span></span>

<span data-ttu-id="6ab6b-465">Les secrets désactivés et arrivés à expiration lèvent un <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> .</span><span class="sxs-lookup"><span data-stu-id="6ab6b-465">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="6ab6b-466">Pour empêcher l’application de se déclencher, fournissez la configuration à l’aide d’un autre fournisseur de configuration ou mettez à jour le secret désactivé ou expiré.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-466">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="6ab6b-467">Dépanner</span><span class="sxs-lookup"><span data-stu-id="6ab6b-467">Troubleshoot</span></span>

<span data-ttu-id="6ab6b-468">Lorsque l’application ne parvient pas à charger la configuration à l’aide du fournisseur, un message d’erreur est écrit dans l' [infrastructure de journalisation ASP.net Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="6ab6b-468">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="6ab6b-469">Les conditions suivantes empêchent le chargement de la configuration :</span><span class="sxs-lookup"><span data-stu-id="6ab6b-469">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="6ab6b-470">L’application ou le certificat n’est pas configuré correctement dans Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-470">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="6ab6b-471">Le coffre de clés n’existe pas dans Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-471">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="6ab6b-472">L’application n’est pas autorisée à accéder au coffre de clés.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-472">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="6ab6b-473">La stratégie d’accès n’inclut pas les `Get` `List` autorisations et.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-473">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="6ab6b-474">Dans le coffre de clés, les données de configuration (paire nom-valeur) sont incorrectement nommées, manquantes, désactivées ou expirées.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-474">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="6ab6b-475">L’application a un nom de coffre de clés ( `KeyVaultName` ), un ID d’Application Azure ad ( `AzureADApplicationId` ) ou une empreinte de certificat Azure ad () incorrect `AzureADCertThumbprint` .</span><span class="sxs-lookup"><span data-stu-id="6ab6b-475">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="6ab6b-476">La clé de configuration (nom) est incorrecte dans l’application pour la valeur que vous essayez de charger.</span><span class="sxs-lookup"><span data-stu-id="6ab6b-476">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="6ab6b-477">Lors de l’ajout de la stratégie d’accès pour l’application au coffre de clés, la stratégie a été créée, mais le bouton **Enregistrer** n’a pas été sélectionné dans l’interface utilisateur des **stratégies d’accès** .</span><span class="sxs-lookup"><span data-stu-id="6ab6b-477">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6ab6b-478">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="6ab6b-478">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="6ab6b-479">Microsoft Azure : Key Vault</span><span class="sxs-lookup"><span data-stu-id="6ab6b-479">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="6ab6b-480">Microsoft Azure : documentation Key Vault</span><span class="sxs-lookup"><span data-stu-id="6ab6b-480">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="6ab6b-481">Génération et transfert de clés protégées par HSM pour Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="6ab6b-481">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="6ab6b-482">KeyVaultClient, classe</span><span class="sxs-lookup"><span data-stu-id="6ab6b-482">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="6ab6b-483">Démarrage rapide : définir et récupérer un secret depuis Azure Key Vault à l’aide d’une application web .NET</span><span class="sxs-lookup"><span data-stu-id="6ab6b-483">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="6ab6b-484">Tutoriel - Utiliser Azure Key Vault avec une machine virtuelle Windows Azure et le langage .NET</span><span class="sxs-lookup"><span data-stu-id="6ab6b-484">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end
