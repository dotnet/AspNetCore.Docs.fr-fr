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
ms.openlocfilehash: 10a949831c180f51bc6bb9b8294150a558f9343c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060129"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a>Fournisseur de configuration Azure Key Vault dans ASP.NET Core

Par [Andrew Stanton-infirmière](https://github.com/anurse)

::: moniker range=">= aspnetcore-3.0"

Ce document explique comment utiliser le fournisseur de configuration [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pour charger des valeurs de configuration d’application à partir de Azure Key Vault secrets. Azure Key Vault est un service basé sur le Cloud qui permet de protéger les clés de chiffrement et les secrets utilisés par les applications et les services. Les scénarios courants d’utilisation de Azure Key Vault avec les applications ASP.NET Core sont les suivants :

* Contrôle de l’accès aux données de configuration sensibles.
* Respect de la configuration requise pour les modules de sécurité matériels (HSM) certifiés FIPS 140-2 de niveau 2 lors du stockage des données de configuration.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="packages"></a>Paquets

Ajoutez une référence de package à la [Microsoft.Extensions.Configfiguration. Package AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) .

## <a name="sample-app"></a>Exemple d’application

L’exemple d’application s’exécute dans l’un des deux modes déterminés par l' `#define` instruction en haut du fichier *Program.cs* :

* `Certificate`: Illustre l’utilisation d’un ID client Azure Key Vault et d’un certificat X. 509 pour accéder aux secrets stockés dans Azure Key Vault. Cette version de l’exemple peut être exécutée à partir de n’importe quel emplacement, déployée sur Azure App Service ou n’importe quel hôte capable de servir une application ASP.NET Core.
* `Managed`: Montre comment utiliser [des identités gérées pour les ressources Azure](/azure/active-directory/managed-identities-azure-resources/overview) pour authentifier l’application afin de Azure Key Vault avec l’authentification Azure ad sans les informations d’identification stockées dans le code ou la configuration de l’application. Lorsque vous utilisez des identités gérées pour l’authentification, un ID d’application et un mot de passe de Azure AD (clé secrète client) ne sont pas requis. La `Managed` version de l’exemple doit être déployée sur Azure. Suivez les instructions de la section [utiliser les identités gérées pour les ressources Azure](#use-managed-identities-for-azure-resources) .

Pour plus d’informations sur la configuration d’un exemple d’application à l’aide de directives de préprocesseur ( `#define` ), consultez <xref:index#preprocessor-directives-in-sample-code> .

## <a name="secret-storage-in-the-development-environment"></a>Stockage secret dans l’environnement de développement

Définissez les secrets localement à l’aide de l' [outil Gestionnaire de secret](xref:security/app-secrets). Lorsque l’exemple d’application s’exécute sur l’ordinateur local dans l’environnement de développement, les secrets sont chargés à partir du magasin du gestionnaire de secret local.

L’outil Gestionnaire de secret nécessite une `<UserSecretsId>` propriété dans le fichier projet de l’application. Définissez la valeur de la propriété ( `{GUID}` ) sur un GUID unique :

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

Les secrets sont créés en tant que paires nom-valeur. Les valeurs hiérarchiques (sections de configuration) utilisent un `:` (deux-points) comme séparateur dans ASP.net Core noms de clé de [configuration](xref:fundamentals/configuration/index) .

Le gestionnaire de secret est utilisé à partir d’une interface de commande ouverte à la [racine de contenu](xref:fundamentals/index#content-root)du projet, où `{SECRET NAME}` est le nom et `{SECRET VALUE}` est la valeur :

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

Exécutez les commandes suivantes dans une interface de commande à partir de la [racine de contenu](xref:fundamentals/index#content-root) du projet pour définir les secrets de l’exemple d’application :

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

Lorsque ces secrets sont stockés dans Azure Key Vault dans le [stockage secret dans l’environnement de production avec Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, le `_dev` suffixe est remplacé par `_prod` . Le suffixe fournit un signal visuel dans la sortie de l’application qui indique la source des valeurs de configuration.

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Stockage secret dans l’environnement de production avec Azure Key Vault

Les instructions fournies par le Guide de [démarrage rapide : définir et récupérer un secret à partir de Azure Key Vault à l’aide de Azure CLI](/azure/key-vault/quick-create-cli) rubrique sont résumées ici pour créer un Azure Key Vault et stocker des secrets utilisés par l’exemple d’application. Pour plus d’informations, reportez-vous à la rubrique.

1. Ouvrez Azure Cloud Shell à l’aide de l’une des méthodes suivantes dans la [portail Azure](https://portal.azure.com/):

   * Sélectionnez **Essayer** dans le coin supérieur droit d’un bloc de code. Utilisez la chaîne de recherche « Azure CLI » dans la zone de texte.
   * Ouvrez Cloud Shell dans votre navigateur à l’aide du bouton **Launch Cloud Shell** .
   * Sélectionnez le bouton **Cloud Shell** dans le menu situé dans l’angle supérieur droit du portail Azure.

   Pour plus d’informations, consultez [Azure CLI](/cli/azure/) et [Présentation des Azure Cloud Shell](/azure/cloud-shell/overview).

1. Si vous n’êtes pas déjà authentifié, connectez-vous à l’aide de la `az login` commande.

1. Créez un groupe de ressources à l’aide de la commande suivante, où `{RESOURCE GROUP NAME}` est le nom du groupe de ressources pour le nouveau groupe de ressources et `{LOCATION}` est la région Azure (Datacenter) :

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Créez un coffre de clés dans le groupe de ressources à l’aide de la commande suivante, où `{KEY VAULT NAME}` est le nom du nouveau coffre de clés et `{LOCATION}` est la région Azure (Datacenter) :

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Créez des secrets dans le coffre de clés en tant que paires nom-valeur.

   Azure Key Vault noms de secrets sont limités aux caractères alphanumériques et aux tirets. Les valeurs hiérarchiques (sections de configuration) utilisent `--` (deux tirets) comme séparateur. Les signes deux-points, qui sont normalement utilisés pour délimiter une section d’une sous-clé dans [ASP.net Core configuration](xref:fundamentals/configuration/index), ne sont pas autorisés dans les noms de secrets du coffre de clés. Par conséquent, deux tirets sont utilisés et permutés pour un signe deux-points lorsque les secrets sont chargés dans la configuration de l’application.

   Les secrets suivants sont destinés à être utilisés avec l’exemple d’application. Les valeurs incluent un `_prod` suffixe pour les distinguer des `_dev` valeurs de suffixe chargées dans l’environnement de développement des secrets d’utilisateur. Remplacez `{KEY VAULT NAME}` par le nom du coffre de clés que vous avez créé à l’étape précédente :

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>Utiliser l’ID d’application et le certificat X. 509 pour les applications non hébergées sur Azure

Configurez Azure AD, Azure Key Vault et l’application pour qu’elle utilise un ID d’application Azure Active Directory et un certificat X. 509 pour s’authentifier auprès d’un coffre de clés **lorsque l’application est hébergée en dehors d’Azure** . Pour plus d’informations, consultez [À propos des clés, des secrets et des certificats](/azure/key-vault/about-keys-secrets-and-certificates).

> [!NOTE]
> Bien que l’utilisation d’un ID d’application et d’un certificat X. 509 soit prise en charge pour les applications hébergées dans Azure, nous vous recommandons [d’utiliser des identités gérées pour les ressources Azure](#use-managed-identities-for-azure-resources) lors de l’hébergement d’une application dans Azure. Les identités gérées ne nécessitent pas le stockage d’un certificat dans l’application ou dans l’environnement de développement.

L’exemple d’application utilise un ID d’application et un certificat X. 509 lorsque l' `#define` instruction en haut du fichier *Program.cs* a la valeur `Certificate` .

1. Créez un certificat d’archive PKCS # 12 ( *. pfx* ). Les options de création de certificats incluent [Makecert sur Windows](/windows/desktop/seccrypto/makecert) et [OpenSSL](https://www.openssl.org/).
1. Installez le certificat dans le magasin de certificats personnel de l’utilisateur actuel. Le marquage de la clé comme étant exportable est facultatif. Notez l’empreinte numérique du certificat, qui est utilisé plus loin dans ce processus.
1. Exportez le certificat PKCS # 12 ( *. pfx* ) sous la forme d’un certificat encodé der ( *. cer* ).
1. Inscrire l’application auprès de Azure AD ( **inscriptions d’applications** ).
1. Chargez le certificat codé DER ( *. cer* ) dans Azure ad :
   1. Sélectionnez l’application dans Azure AD.
   1. Accédez à **certificats & secrets** .
   1. Sélectionnez **Télécharger le certificat** pour télécharger le certificat, qui contient la clé publique. Un certificat. *CER* , *. pem* ou *. CRT* est acceptable.
1. Stockez le nom du coffre de clés, l’ID de l’application et l’empreinte numérique du certificat dans le fichier de l’application *appsettings.json* .
1. Accédez à **coffres de clés** dans le portail Azure.
1. Sélectionnez le coffre de clés que vous avez créé dans la section [stockage secret dans l’environnement de production avec Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) .
1. Sélectionnez **Stratégies d’accès** .
1. Sélectionnez **Ajouter une stratégie d’accès** .
1. Ouvrez les **autorisations secret** et fournissez à l’application les autorisations **obtenir** et **liste** .
1. Sélectionnez **Sélectionner un principal** et sélectionnez l’application inscrite par son nom. Sélectionnez le bouton **Sélectionner** .
1. Sélectionnez **OK** .
1. Sélectionnez **Enregistrer** .
1. Déployez l’application.

L' `Certificate` exemple d’application obtient ses valeurs de configuration à partir du `IConfigurationRoot` même nom que le nom de la clé secrète :

* Valeurs non hiérarchiques : la valeur de `SecretName` est obtenue avec `config["SecretName"]` .
* Valeurs hiérarchiques (sections) : utilisez `:` la notation (deux-points) ou la `GetSection` méthode d’extension. Utilisez l’une des approches suivantes pour obtenir la valeur de configuration :
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

Le certificat X. 509 est géré par le système d’exploitation. L’application appelle <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> avec les valeurs fournies par le *appsettings.json* fichier :

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

Exemples de valeurs

* Nom du coffre de clés : `contosovault`
* ID de l’application : `627e911e-43cc-61d4-992e-12db9c81b413`
* Empreinte numérique du certificat : `fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appsettings.json* :

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

Quand vous exécutez l’application, une page Web affiche les valeurs de secret chargées. Dans l’environnement de développement, les valeurs secrètes sont chargées avec le `_dev` suffixe. Dans l’environnement de production, les valeurs sont chargées avec le `_prod` suffixe.

## <a name="use-managed-identities-for-azure-resources"></a>Utiliser des identités gérées pour les ressources Azure

**Une application déployée sur Azure** peut tirer parti des [identités gérées pour les ressources Azure](/azure/active-directory/managed-identities-azure-resources/overview), ce qui permet à l’application de s’authentifier avec Azure Key Vault à l’aide de l’authentification Azure ad sans informations d’identification (ID d’application et mot de passe/clé secrète client) stockées dans l’application.

L’exemple d’application utilise des identités gérées pour les ressources Azure lorsque l' `#define` instruction en haut du fichier *Program.cs* a la valeur `Managed` .

Entrez le nom du coffre dans le fichier de l’application *appsettings.json* . L’exemple d’application n’a pas besoin d’un ID d’application et d’un mot de passe (clé secrète client) lorsqu’il est défini sur la `Managed` version. vous pouvez donc ignorer ces entrées de configuration. L’application est déployée sur Azure et Azure authentifie l’application pour accéder à Azure Key Vault uniquement à l’aide du nom de coffre stocké dans le *appsettings.json* fichier.

Déployez l’exemple d’application sur Azure App Service.

Une application déployée sur Azure App Service est automatiquement inscrite auprès de Azure AD lors de la création du service. Obtenez l’ID d’objet à partir du déploiement pour l’utiliser dans la commande suivante. L’ID d’objet est affiché dans le Portail Azure sur le **Identity** panneau du App service.

À l’aide de Azure CLI et de l’ID d’objet de l’application, fournissez l’application avec `list` et les `get` autorisations d’accès au coffre de clés :

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

**Redémarrez l’application** à l’aide de Azure CLI, de PowerShell ou de l’portail Azure.

L’exemple d’application :

* Crée une instance de la `AzureServiceTokenProvider` classe sans chaîne de connexion. Lorsqu’une chaîne de connexion n’est pas fournie, le fournisseur tente d’obtenir un jeton d’accès à partir des identités gérées pour les ressources Azure.
* Une nouvelle <xref:Microsoft.Azure.KeyVault.KeyVaultClient> est créée avec le `AzureServiceTokenProvider` rappel de jeton d’instance.
* L' <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance est utilisée avec une implémentation par défaut de <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> qui charge toutes les valeurs de secret et remplace les doubles tirets ( `--` ) par des signes deux-points ( `:` ) dans les noms de clés.

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

Exemple de valeur de nom de coffre de clés : `contosovault`
    
*appsettings.json* :

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

Quand vous exécutez l’application, une page Web affiche les valeurs de secret chargées. Dans l’environnement de développement, les valeurs secrètes ont le `_dev` suffixe, car elles sont fournies par des secrets d’utilisateur. Dans l’environnement de production, les valeurs sont chargées avec le `_prod` suffixe, car elles sont fournies par Azure Key Vault.

Si vous recevez une `Access denied` erreur, vérifiez que l’application est inscrite auprès de Azure ad et que vous avez accès au coffre de clés. Confirmez que vous avez redémarré le service dans Azure.

Pour plus d’informations sur l’utilisation du fournisseur avec une identité gérée et un pipeline Azure DevOps, consultez [créer une connexion de service Azure Resource Manager à une machine virtuelle avec une identité de service managée](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).

## <a name="configuration-options"></a>Options de configuration

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> peut accepter un <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions> :

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| Propriété         | Description |
| ---------------- | ----------- |
| `Client`         | <xref:Microsoft.Azure.KeyVault.KeyVaultClient> à utiliser pour récupérer des valeurs. |
| `Manager`        | <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> instance utilisée pour contrôler le chargement du secret. |
| `ReloadInterval` | `Timespan` pour attendre entre les tentatives d’interrogation du coffre de clés pour les modifications. La valeur par défaut est `null` (la configuration n’est pas rechargée). |
| `Vault`          | URI du coffre de clés. |

## <a name="use-a-key-name-prefix"></a>Utiliser un préfixe de nom de clé

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> fournit une surcharge qui accepte une implémentation de <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , qui vous permet de contrôler la façon dont les secrets de coffre de clés sont convertis en clés de configuration. Par exemple, vous pouvez implémenter l’interface pour charger des valeurs de secret en fonction d’une valeur de préfixe que vous fournissez au démarrage de l’application. Cela vous permet, par exemple, de charger des secrets basés sur la version de l’application.

> [!WARNING]
> N’utilisez pas de préfixes sur des secrets de coffre de clés pour placer des secrets pour plusieurs applications dans le même coffre de clés ou pour placer des secrets d’environnement (par exemple, le *développement* et les secrets de *production* ) dans le même coffre. Nous recommandons que les applications et les environnements de développement/production utilisent des coffres de clés distincts pour isoler les environnements d’application pour le plus haut niveau de sécurité.

Dans l’exemple suivant, un secret est établi dans le coffre de clés (et à l’aide de l’outil secret Manager pour l’environnement de développement) pour `5000-AppSecret` (les périodes ne sont pas autorisées dans les noms de secret de coffre de clés). Ce secret représente un secret d’application pour la version 5.0.0.0 de l’application. Pour une autre version de l’application, 5.1.0.0, un secret est ajouté au coffre de clés (et à l’aide de l’outil secret Manager) pour `5100-AppSecret` . Chaque version de l’application charge sa valeur secrète avec version dans sa configuration en tant que `AppSecret` , en éliminant la version au fur et à mesure qu’elle charge le secret.

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> est appelé avec un personnalisé <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> :

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

L' <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implémentation réagit aux préfixes de version des secrets pour charger le secret approprié dans la configuration :

* `Load` charge un secret lorsque son nom commence par le préfixe. Les autres secrets ne sont pas chargés.
* `GetKey`:
  * Supprime le préfixe du nom de la clé secrète.
  * Remplace deux tirets dans n’importe quel nom par le `KeyDelimiter` , qui est le délimiteur utilisé dans la configuration (généralement un signe deux-points). Azure Key Vault n’autorise pas les deux-points dans les noms de secrets.

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

La `Load` méthode est appelée par un algorithme de fournisseur qui itère au sein des secrets du coffre pour trouver ceux qui ont le préfixe de version. Lorsqu’un préfixe de version est trouvé avec `Load` , l’algorithme utilise la `GetKey` méthode pour retourner le nom de configuration du nom de la clé secrète. Il supprime le préfixe de version du nom du secret et retourne le reste du nom de secret pour le chargement dans les paires nom-valeur de la configuration de l’application.

Lorsque cette approche est implémentée :

1. Version de l’application spécifiée dans le fichier projet de l’application. Dans l’exemple suivant, la version de l’application est définie sur `5.0.0.0` :

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. Vérifiez qu’une `<UserSecretsId>` propriété est présente dans le fichier projet de l’application, où `{GUID}` est un GUID fourni par l’utilisateur :

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   Enregistrez les secrets suivants localement à l’aide de l' [outil secret Manager](xref:security/app-secrets):

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. Les secrets sont enregistrés dans Azure Key Vault à l’aide des commandes de Azure CLI suivantes :

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. Lorsque l’application est exécutée, les secrets du coffre de clés sont chargés. Le secret de chaîne de `5000-AppSecret` est mis en correspondance avec la version de l’application spécifiée dans le fichier projet de l’application ( `5.0.0.0` ).

1. La version `5000` (avec le tiret) est supprimée du nom de la clé. Tout au long de l’application, la lecture de la configuration avec la clé `AppSecret` charge la valeur du secret.

1. Si la version de l’application est modifiée dans le fichier projet `5.1.0.0` et que l’application est à nouveau exécutée, la valeur secrète renvoyée se trouve `5.1.0.0_secret_value_dev` dans l’environnement de développement et `5.1.0.0_secret_value_prod` en production.

> [!NOTE]
> Vous pouvez également fournir votre propre <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implémentation à <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> . Un client personnalisé permet de partager une seule instance du client dans l’application.

## <a name="bind-an-array-to-a-class"></a>Lier un tableau à une classe

Le fournisseur est en mesure de lire les valeurs de configuration dans un tableau pour la liaison à un tableau POCO.

Lors de la lecture à partir d’une source de configuration qui autorise les clés à contenir des séparateurs de deux-points ( `:` ), un segment de clé numérique est utilisé pour distinguer les clés qui composent un tableau ( `:0:` , `:1:` , &hellip; `:{n}:` ). Pour plus d’informations, consultez [Configuration : lier un tableau à une classe](xref:fundamentals/configuration/index#bind-an-array-to-a-class).

Les clés de Azure Key Vault ne peuvent pas utiliser un signe deux-points comme séparateur. L’approche décrite dans cette rubrique utilise des doubles tirets ( `--` ) comme séparateur pour les valeurs hiérarchiques (sections). Les clés de tableau sont stockées dans Azure Key Vault avec des doubles tirets et des segments de clé numériques ( `--0--` , `--1--` , &hellip; `--{n}--` ).

Examinez la configuration de fournisseur de journalisation [Serilog](https://serilog.net/) suivante fournie par un fichier JSON. Deux littéraux d’objet définis dans le `WriteTo` tableau reflètent deux *récepteurs* Serilog, qui décrivent les destinations pour la sortie de journalisation :

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

La configuration indiquée dans le fichier JSON précédent est stockée dans Azure Key Vault à l’aide de la notation à deux tirets ( `--` ) et des segments numériques :

| Clé | Valeur |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>Recharger les secrets

Les secrets sont mis en cache jusqu’à ce que `IConfigurationRoot.Reload()` soit appelé. Les secrets ayant expiré, désactivés et mis à jour dans le coffre de clés ne sont pas respectés par l’application tant qu' `Reload` elle n’est pas exécutée.

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>Secrets désactivés et arrivés à expiration

Les secrets désactivés et arrivés à expiration lèvent un <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> . Pour empêcher l’application de se déclencher, fournissez la configuration à l’aide d’un autre fournisseur de configuration ou mettez à jour le secret désactivé ou expiré.

## <a name="troubleshoot"></a>Dépanner

Lorsque l’application ne parvient pas à charger la configuration à l’aide du fournisseur, un message d’erreur est écrit dans l' [infrastructure de journalisation ASP.net Core](xref:fundamentals/logging/index). Les conditions suivantes empêchent le chargement de la configuration :

* L’application ou le certificat n’est pas configuré correctement dans Azure Active Directory.
* Le coffre de clés n’existe pas dans Azure Key Vault.
* L’application n’est pas autorisée à accéder au coffre de clés.
* La stratégie d’accès n’inclut pas les `Get` `List` autorisations et.
* Dans le coffre de clés, les données de configuration (paire nom-valeur) sont incorrectement nommées, manquantes, désactivées ou expirées.
* L’application a un nom de coffre de clés ( `KeyVaultName` ), un ID d’Application Azure ad ( `AzureADApplicationId` ) ou une empreinte de certificat Azure ad () incorrect `AzureADCertThumbprint` .
* La clé de configuration (nom) est incorrecte dans l’application pour la valeur que vous essayez de charger.
* Lors de l’ajout de la stratégie d’accès pour l’application au coffre de clés, la stratégie a été créée, mais le bouton **Enregistrer** n’a pas été sélectionné dans l’interface utilisateur des **stratégies d’accès** .

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:fundamentals/configuration/index>
* [Microsoft Azure : Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure : documentation Key Vault](/azure/key-vault/)
* [Génération et transfert de clés protégées par HSM pour Azure Key Vault](/azure/key-vault/key-vault-hsm-protected-keys)
* [KeyVaultClient, classe](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [Démarrage rapide : définir et récupérer un secret depuis Azure Key Vault à l’aide d’une application web .NET](/azure/key-vault/quick-create-net)
* [Tutoriel - Utiliser Azure Key Vault avec une machine virtuelle Windows Azure et le langage .NET](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Ce document explique comment utiliser le fournisseur de configuration [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pour charger des valeurs de configuration d’application à partir de Azure Key Vault secrets. Azure Key Vault est un service basé sur le Cloud qui permet de protéger les clés de chiffrement et les secrets utilisés par les applications et les services. Les scénarios courants d’utilisation de Azure Key Vault avec les applications ASP.NET Core sont les suivants :

* Contrôle de l’accès aux données de configuration sensibles.
* Respect de la configuration requise pour les modules de sécurité matériels (HSM) certifiés FIPS 140-2 de niveau 2 lors du stockage des données de configuration.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="packages"></a>Paquets

Ajoutez une référence de package à la [Microsoft.Extensions.Configfiguration. Package AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) .

## <a name="sample-app"></a>Exemple d’application

L’exemple d’application s’exécute dans l’un des deux modes déterminés par l' `#define` instruction en haut du fichier *Program.cs* :

* `Certificate`: Illustre l’utilisation d’un ID client Azure Key Vault et d’un certificat X. 509 pour accéder aux secrets stockés dans Azure Key Vault. Cette version de l’exemple peut être exécutée à partir de n’importe quel emplacement, déployée sur Azure App Service ou n’importe quel hôte capable de servir une application ASP.NET Core.
* `Managed`: Montre comment utiliser [des identités gérées pour les ressources Azure](/azure/active-directory/managed-identities-azure-resources/overview) pour authentifier l’application afin de Azure Key Vault avec l’authentification Azure ad sans les informations d’identification stockées dans le code ou la configuration de l’application. Lorsque vous utilisez des identités gérées pour l’authentification, un ID d’application et un mot de passe de Azure AD (clé secrète client) ne sont pas requis. La `Managed` version de l’exemple doit être déployée sur Azure. Suivez les instructions de la section [utiliser les identités gérées pour les ressources Azure](#use-managed-identities-for-azure-resources) .

Pour plus d’informations sur la configuration d’un exemple d’application à l’aide de directives de préprocesseur ( `#define` ), consultez <xref:index#preprocessor-directives-in-sample-code> .

## <a name="secret-storage-in-the-development-environment"></a>Stockage secret dans l’environnement de développement

Définissez les secrets localement à l’aide de l' [outil Gestionnaire de secret](xref:security/app-secrets). Lorsque l’exemple d’application s’exécute sur l’ordinateur local dans l’environnement de développement, les secrets sont chargés à partir du magasin du gestionnaire de secret local.

L’outil Gestionnaire de secret nécessite une `<UserSecretsId>` propriété dans le fichier projet de l’application. Définissez la valeur de la propriété ( `{GUID}` ) sur un GUID unique :

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

Les secrets sont créés en tant que paires nom-valeur. Les valeurs hiérarchiques (sections de configuration) utilisent un `:` (deux-points) comme séparateur dans ASP.net Core noms de clé de [configuration](xref:fundamentals/configuration/index) .

Le gestionnaire de secret est utilisé à partir d’une interface de commande ouverte à la [racine de contenu](xref:fundamentals/index#content-root)du projet, où `{SECRET NAME}` est le nom et `{SECRET VALUE}` est la valeur :

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

Exécutez les commandes suivantes dans une interface de commande à partir de la [racine de contenu](xref:fundamentals/index#content-root) du projet pour définir les secrets de l’exemple d’application :

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

Lorsque ces secrets sont stockés dans Azure Key Vault dans le [stockage secret dans l’environnement de production avec Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, le `_dev` suffixe est remplacé par `_prod` . Le suffixe fournit un signal visuel dans la sortie de l’application qui indique la source des valeurs de configuration.

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Stockage secret dans l’environnement de production avec Azure Key Vault

Les instructions fournies par le Guide de [démarrage rapide : définir et récupérer un secret à partir de Azure Key Vault à l’aide de Azure CLI](/azure/key-vault/quick-create-cli) rubrique sont résumées ici pour créer un Azure Key Vault et stocker des secrets utilisés par l’exemple d’application. Pour plus d’informations, reportez-vous à la rubrique.

1. Ouvrez Azure Cloud Shell à l’aide de l’une des méthodes suivantes dans la [portail Azure](https://portal.azure.com/):

   * Sélectionnez **Essayer** dans le coin supérieur droit d’un bloc de code. Utilisez la chaîne de recherche « Azure CLI » dans la zone de texte.
   * Ouvrez Cloud Shell dans votre navigateur à l’aide du bouton **Launch Cloud Shell** .
   * Sélectionnez le bouton **Cloud Shell** dans le menu situé dans l’angle supérieur droit du portail Azure.

   Pour plus d’informations, consultez [Azure CLI](/cli/azure/) et [Présentation des Azure Cloud Shell](/azure/cloud-shell/overview).

1. Si vous n’êtes pas déjà authentifié, connectez-vous à l’aide de la `az login` commande.

1. Créez un groupe de ressources à l’aide de la commande suivante, où `{RESOURCE GROUP NAME}` est le nom du groupe de ressources pour le nouveau groupe de ressources et `{LOCATION}` est la région Azure (Datacenter) :

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Créez un coffre de clés dans le groupe de ressources à l’aide de la commande suivante, où `{KEY VAULT NAME}` est le nom du nouveau coffre de clés et `{LOCATION}` est la région Azure (Datacenter) :

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Créez des secrets dans le coffre de clés en tant que paires nom-valeur.

   Azure Key Vault noms de secrets sont limités aux caractères alphanumériques et aux tirets. Les valeurs hiérarchiques (sections de configuration) utilisent `--` (deux tirets) comme séparateur. Les signes deux-points, qui sont normalement utilisés pour délimiter une section d’une sous-clé dans [ASP.net Core configuration](xref:fundamentals/configuration/index), ne sont pas autorisés dans les noms de secrets du coffre de clés. Par conséquent, deux tirets sont utilisés et permutés pour un signe deux-points lorsque les secrets sont chargés dans la configuration de l’application.

   Les secrets suivants sont destinés à être utilisés avec l’exemple d’application. Les valeurs incluent un `_prod` suffixe pour les distinguer des `_dev` valeurs de suffixe chargées dans l’environnement de développement des secrets d’utilisateur. Remplacez `{KEY VAULT NAME}` par le nom du coffre de clés que vous avez créé à l’étape précédente :

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>Utiliser l’ID d’application et le certificat X. 509 pour les applications non hébergées sur Azure

Configurez Azure AD, Azure Key Vault et l’application pour qu’elle utilise un ID d’application Azure Active Directory et un certificat X. 509 pour s’authentifier auprès d’un coffre de clés **lorsque l’application est hébergée en dehors d’Azure** . Pour plus d’informations, consultez [À propos des clés, des secrets et des certificats](/azure/key-vault/about-keys-secrets-and-certificates).

> [!NOTE]
> Bien que l’utilisation d’un ID d’application et d’un certificat X. 509 soit prise en charge pour les applications hébergées dans Azure, nous vous recommandons [d’utiliser des identités gérées pour les ressources Azure](#use-managed-identities-for-azure-resources) lors de l’hébergement d’une application dans Azure. Les identités gérées ne nécessitent pas le stockage d’un certificat dans l’application ou dans l’environnement de développement.

L’exemple d’application utilise un ID d’application et un certificat X. 509 lorsque l' `#define` instruction en haut du fichier *Program.cs* a la valeur `Certificate` .

1. Créez un certificat d’archive PKCS # 12 ( *. pfx* ). Les options de création de certificats incluent [Makecert sur Windows](/windows/desktop/seccrypto/makecert) et [OpenSSL](https://www.openssl.org/).
1. Installez le certificat dans le magasin de certificats personnel de l’utilisateur actuel. Le marquage de la clé comme étant exportable est facultatif. Notez l’empreinte numérique du certificat, qui est utilisé plus loin dans ce processus.
1. Exportez le certificat PKCS # 12 ( *. pfx* ) sous la forme d’un certificat encodé der ( *. cer* ).
1. Inscrire l’application auprès de Azure AD ( **inscriptions d’applications** ).
1. Chargez le certificat codé DER ( *. cer* ) dans Azure ad :
   1. Sélectionnez l’application dans Azure AD.
   1. Accédez à **certificats & secrets** .
   1. Sélectionnez **Télécharger le certificat** pour télécharger le certificat, qui contient la clé publique. Un certificat. *CER* , *. pem* ou *. CRT* est acceptable.
1. Stockez le nom du coffre de clés, l’ID de l’application et l’empreinte numérique du certificat dans le fichier de l’application *appsettings.json* .
1. Accédez à **coffres de clés** dans le portail Azure.
1. Sélectionnez le coffre de clés que vous avez créé dans la section [stockage secret dans l’environnement de production avec Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) .
1. Sélectionnez **Stratégies d’accès** .
1. Sélectionnez **Ajouter une stratégie d’accès** .
1. Ouvrez les **autorisations secret** et fournissez à l’application les autorisations **obtenir** et **liste** .
1. Sélectionnez **Sélectionner un principal** et sélectionnez l’application inscrite par son nom. Sélectionnez le bouton **Sélectionner** .
1. Sélectionnez **OK** .
1. Sélectionnez **Enregistrer** .
1. Déployez l’application.

L' `Certificate` exemple d’application obtient ses valeurs de configuration à partir du `IConfigurationRoot` même nom que le nom de la clé secrète :

* Valeurs non hiérarchiques : la valeur de `SecretName` est obtenue avec `config["SecretName"]` .
* Valeurs hiérarchiques (sections) : utilisez `:` la notation (deux-points) ou la `GetSection` méthode d’extension. Utilisez l’une des approches suivantes pour obtenir la valeur de configuration :
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

Le certificat X. 509 est géré par le système d’exploitation. L’application appelle <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> avec les valeurs fournies par le *appsettings.json* fichier :

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

Exemples de valeurs

* Nom du coffre de clés : `contosovault`
* ID de l’application : `627e911e-43cc-61d4-992e-12db9c81b413`
* Empreinte numérique du certificat : `fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appsettings.json* :

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

Quand vous exécutez l’application, une page Web affiche les valeurs de secret chargées. Dans l’environnement de développement, les valeurs secrètes sont chargées avec le `_dev` suffixe. Dans l’environnement de production, les valeurs sont chargées avec le `_prod` suffixe.

## <a name="use-managed-identities-for-azure-resources"></a>Utiliser des identités gérées pour les ressources Azure

**Une application déployée sur Azure** peut tirer parti des [identités gérées pour les ressources Azure](/azure/active-directory/managed-identities-azure-resources/overview), ce qui permet à l’application de s’authentifier avec Azure Key Vault à l’aide de l’authentification Azure ad sans informations d’identification (ID d’application et mot de passe/clé secrète client) stockées dans l’application.

L’exemple d’application utilise des identités gérées pour les ressources Azure lorsque l' `#define` instruction en haut du fichier *Program.cs* a la valeur `Managed` .

Entrez le nom du coffre dans le fichier de l’application *appsettings.json* . L’exemple d’application n’a pas besoin d’un ID d’application et d’un mot de passe (clé secrète client) lorsqu’il est défini sur la `Managed` version. vous pouvez donc ignorer ces entrées de configuration. L’application est déployée sur Azure et Azure authentifie l’application pour accéder à Azure Key Vault uniquement à l’aide du nom de coffre stocké dans le *appsettings.json* fichier.

Déployez l’exemple d’application sur Azure App Service.

Une application déployée sur Azure App Service est automatiquement inscrite auprès de Azure AD lors de la création du service. Obtenez l’ID d’objet à partir du déploiement pour l’utiliser dans la commande suivante. L’ID d’objet est affiché dans le Portail Azure sur le **Identity** panneau du App service.

À l’aide de Azure CLI et de l’ID d’objet de l’application, fournissez l’application avec `list` et les `get` autorisations d’accès au coffre de clés :

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

**Redémarrez l’application** à l’aide de Azure CLI, de PowerShell ou de l’portail Azure.

L’exemple d’application :

* Crée une instance de la `AzureServiceTokenProvider` classe sans chaîne de connexion. Lorsqu’une chaîne de connexion n’est pas fournie, le fournisseur tente d’obtenir un jeton d’accès à partir des identités gérées pour les ressources Azure.
* Une nouvelle <xref:Microsoft.Azure.KeyVault.KeyVaultClient> est créée avec le `AzureServiceTokenProvider` rappel de jeton d’instance.
* L' <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance est utilisée avec une implémentation par défaut de <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> qui charge toutes les valeurs de secret et remplace les doubles tirets ( `--` ) par des signes deux-points ( `:` ) dans les noms de clés.

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

Exemple de valeur de nom de coffre de clés : `contosovault`
    
*appsettings.json* :

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

Quand vous exécutez l’application, une page Web affiche les valeurs de secret chargées. Dans l’environnement de développement, les valeurs secrètes ont le `_dev` suffixe, car elles sont fournies par des secrets d’utilisateur. Dans l’environnement de production, les valeurs sont chargées avec le `_prod` suffixe, car elles sont fournies par Azure Key Vault.

Si vous recevez une `Access denied` erreur, vérifiez que l’application est inscrite auprès de Azure ad et que vous avez accès au coffre de clés. Confirmez que vous avez redémarré le service dans Azure.

Pour plus d’informations sur l’utilisation du fournisseur avec une identité gérée et un pipeline Azure DevOps, consultez [créer une connexion de service Azure Resource Manager à une machine virtuelle avec une identité de service managée](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).

## <a name="use-a-key-name-prefix"></a>Utiliser un préfixe de nom de clé

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> fournit une surcharge qui accepte une implémentation de <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , qui vous permet de contrôler la façon dont les secrets de coffre de clés sont convertis en clés de configuration. Par exemple, vous pouvez implémenter l’interface pour charger des valeurs de secret en fonction d’une valeur de préfixe que vous fournissez au démarrage de l’application. Cela vous permet, par exemple, de charger des secrets basés sur la version de l’application.

> [!WARNING]
> N’utilisez pas de préfixes sur des secrets de coffre de clés pour placer des secrets pour plusieurs applications dans le même coffre de clés ou pour placer des secrets d’environnement (par exemple, le *développement* et les secrets de *production* ) dans le même coffre. Nous recommandons que les applications et les environnements de développement/production utilisent des coffres de clés distincts pour isoler les environnements d’application pour le plus haut niveau de sécurité.

Dans l’exemple suivant, un secret est établi dans le coffre de clés (et à l’aide de l’outil secret Manager pour l’environnement de développement) pour `5000-AppSecret` (les périodes ne sont pas autorisées dans les noms de secret de coffre de clés). Ce secret représente un secret d’application pour la version 5.0.0.0 de l’application. Pour une autre version de l’application, 5.1.0.0, un secret est ajouté au coffre de clés (et à l’aide de l’outil secret Manager) pour `5100-AppSecret` . Chaque version de l’application charge sa valeur secrète avec version dans sa configuration en tant que `AppSecret` , en éliminant la version au fur et à mesure qu’elle charge le secret.

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> est appelé avec un personnalisé <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> :

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

L' <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implémentation réagit aux préfixes de version des secrets pour charger le secret approprié dans la configuration :

* `Load` charge un secret lorsque son nom commence par le préfixe. Les autres secrets ne sont pas chargés.
* `GetKey`:
  * Supprime le préfixe du nom de la clé secrète.
  * Remplace deux tirets dans n’importe quel nom par le `KeyDelimiter` , qui est le délimiteur utilisé dans la configuration (généralement un signe deux-points). Azure Key Vault n’autorise pas les deux-points dans les noms de secrets.

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

La `Load` méthode est appelée par un algorithme de fournisseur qui itère au sein des secrets du coffre pour trouver ceux qui ont le préfixe de version. Lorsqu’un préfixe de version est trouvé avec `Load` , l’algorithme utilise la `GetKey` méthode pour retourner le nom de configuration du nom de la clé secrète. Il supprime le préfixe de version du nom du secret et retourne le reste du nom de secret pour le chargement dans les paires nom-valeur de la configuration de l’application.

Lorsque cette approche est implémentée :

1. Version de l’application spécifiée dans le fichier projet de l’application. Dans l’exemple suivant, la version de l’application est définie sur `5.0.0.0` :

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. Vérifiez qu’une `<UserSecretsId>` propriété est présente dans le fichier projet de l’application, où `{GUID}` est un GUID fourni par l’utilisateur :

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   Enregistrez les secrets suivants localement à l’aide de l' [outil secret Manager](xref:security/app-secrets):

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. Les secrets sont enregistrés dans Azure Key Vault à l’aide des commandes de Azure CLI suivantes :

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. Lorsque l’application est exécutée, les secrets du coffre de clés sont chargés. Le secret de chaîne de `5000-AppSecret` est mis en correspondance avec la version de l’application spécifiée dans le fichier projet de l’application ( `5.0.0.0` ).

1. La version `5000` (avec le tiret) est supprimée du nom de la clé. Tout au long de l’application, la lecture de la configuration avec la clé `AppSecret` charge la valeur du secret.

1. Si la version de l’application est modifiée dans le fichier projet `5.1.0.0` et que l’application est à nouveau exécutée, la valeur secrète renvoyée se trouve `5.1.0.0_secret_value_dev` dans l’environnement de développement et `5.1.0.0_secret_value_prod` en production.

> [!NOTE]
> Vous pouvez également fournir votre propre <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implémentation à <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> . Un client personnalisé permet de partager une seule instance du client dans l’application.

## <a name="bind-an-array-to-a-class"></a>Lier un tableau à une classe

Le fournisseur est en mesure de lire les valeurs de configuration dans un tableau pour la liaison à un tableau POCO.

Lors de la lecture à partir d’une source de configuration qui autorise les clés à contenir des séparateurs de deux-points ( `:` ), un segment de clé numérique est utilisé pour distinguer les clés qui composent un tableau ( `:0:` , `:1:` , &hellip; `:{n}:` ). Pour plus d’informations, consultez [Configuration : lier un tableau à une classe](xref:fundamentals/configuration/index#bind-an-array-to-a-class).

Les clés de Azure Key Vault ne peuvent pas utiliser un signe deux-points comme séparateur. L’approche décrite dans cette rubrique utilise des doubles tirets ( `--` ) comme séparateur pour les valeurs hiérarchiques (sections). Les clés de tableau sont stockées dans Azure Key Vault avec des doubles tirets et des segments de clé numériques ( `--0--` , `--1--` , &hellip; `--{n}--` ).

Examinez la configuration de fournisseur de journalisation [Serilog](https://serilog.net/) suivante fournie par un fichier JSON. Deux littéraux d’objet définis dans le `WriteTo` tableau reflètent deux *récepteurs* Serilog, qui décrivent les destinations pour la sortie de journalisation :

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

La configuration indiquée dans le fichier JSON précédent est stockée dans Azure Key Vault à l’aide de la notation à deux tirets ( `--` ) et des segments numériques :

| Clé | Valeur |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>Recharger les secrets

Les secrets sont mis en cache jusqu’à ce que `IConfigurationRoot.Reload()` soit appelé. Les secrets ayant expiré, désactivés et mis à jour dans le coffre de clés ne sont pas respectés par l’application tant qu' `Reload` elle n’est pas exécutée.

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>Secrets désactivés et arrivés à expiration

Les secrets désactivés et arrivés à expiration lèvent un <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> . Pour empêcher l’application de se déclencher, fournissez la configuration à l’aide d’un autre fournisseur de configuration ou mettez à jour le secret désactivé ou expiré.

## <a name="troubleshoot"></a>Dépanner

Lorsque l’application ne parvient pas à charger la configuration à l’aide du fournisseur, un message d’erreur est écrit dans l' [infrastructure de journalisation ASP.net Core](xref:fundamentals/logging/index). Les conditions suivantes empêchent le chargement de la configuration :

* L’application ou le certificat n’est pas configuré correctement dans Azure Active Directory.
* Le coffre de clés n’existe pas dans Azure Key Vault.
* L’application n’est pas autorisée à accéder au coffre de clés.
* La stratégie d’accès n’inclut pas les `Get` `List` autorisations et.
* Dans le coffre de clés, les données de configuration (paire nom-valeur) sont incorrectement nommées, manquantes, désactivées ou expirées.
* L’application a un nom de coffre de clés ( `KeyVaultName` ), un ID d’Application Azure ad ( `AzureADApplicationId` ) ou une empreinte de certificat Azure ad () incorrect `AzureADCertThumbprint` .
* La clé de configuration (nom) est incorrecte dans l’application pour la valeur que vous essayez de charger.
* Lors de l’ajout de la stratégie d’accès pour l’application au coffre de clés, la stratégie a été créée, mais le bouton **Enregistrer** n’a pas été sélectionné dans l’interface utilisateur des **stratégies d’accès** .

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:fundamentals/configuration/index>
* [Microsoft Azure : Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure : documentation Key Vault](/azure/key-vault/)
* [Génération et transfert de clés protégées par HSM pour Azure Key Vault](/azure/key-vault/key-vault-hsm-protected-keys)
* [KeyVaultClient, classe](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [Démarrage rapide : définir et récupérer un secret depuis Azure Key Vault à l’aide d’une application web .NET](/azure/key-vault/quick-create-net)
* [Tutoriel - Utiliser Azure Key Vault avec une machine virtuelle Windows Azure et le langage .NET](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

