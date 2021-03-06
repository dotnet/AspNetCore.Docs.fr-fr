---
title: Sécuriser les Blazor Server applications ASP.net Core
author: guardrex
description: Découvrez comment sécuriser des applications Blazor Server en tant qu’applications ASP.net core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
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
uid: blazor/security/server/index
ms.openlocfilehash: 147ebbeb84e1755307d627ef428d92d1b0248c74
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2021
ms.locfileid: "102394861"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a>Sécuriser les Blazor Server applications ASP.net Core

Blazor Server les applications sont configurées pour la sécurité de la même façon que les applications ASP.NET Core. Pour plus d’informations, consultez les articles sous <xref:security/index> . Les rubriques de cette vue d’ensemble s’appliquent spécifiquement à Blazor Server .

## <a name="blazor-server-project-template"></a>Blazor Server modèle de projet

Le [ Blazor Server modèle de projet](xref:blazor/project-structure) peut être configuré pour l’authentification lors de la création du projet.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Suivez les instructions de Visual Studio dans <xref:blazor/tooling> pour créer un nouveau Blazor Server projet avec un mécanisme d’authentification.

Après avoir choisi le modèle d' **Blazor Server application** dans la boîte de dialogue **créer une application Web ASP.net Core** , sélectionnez **modifier** sous **authentification**.

Une boîte de dialogue s’ouvre pour offrir le même ensemble de mécanismes d’authentification que ceux disponibles pour les autres projets ASP.NET Core :

* **Aucune authentification**
* **Comptes d’utilisateur individuels : les** comptes d’utilisateur peuvent être stockés :
  * Au sein de l’application à l’aide du système de ASP.NET Core [Identity](xref:security/authentication/identity) .
  * Avec [Azure ad B2C](xref:security/authentication/azure-ad-b2c).
* **Comptes professionnels ou scolaires**
* **Authentification Windows**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Suivez les instructions de Visual Studio Code dans <xref:blazor/tooling> pour créer un nouveau Blazor Server projet avec un mécanisme d’authentification :

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Les valeurs autorisées d’authentification (`{AUTHENTICATION}`) sont présentées dans le tableau suivant.

| Mécanisme d’authentification | Description |
| ------------------------ | ----------- |
| `None` (valeur par défaut)         | Aucune authentification |
| `Individual`             | Utilisateurs stockés dans l’application avec ASP.NET Core Identity |
| `IndividualB2C`          | Utilisateurs stockés dans [Azure ad B2C](xref:security/authentication/azure-ad-b2c) |
| `SingleOrg`              | Authentification d’organisation pour un seul locataire |
| `MultiOrg`               | Authentification d’organisation pour plusieurs locataires |
| `Windows`                | Authentification Windows |

À l’aide de l' `-o|--output` option, la commande utilise la valeur fournie pour l' `{APP NAME}` espace réservé à :

* Créez un dossier pour le projet.
* Nommez ce projet.

Pour plus d’informations, consultez la [`dotnet new`](/dotnet/core/tools/dotnet-new) commande dans le guide .net core.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

1. Suivez les instructions de Visual Studio pour Mac dans <xref:blazor/tooling> .

1. Dans l’étape **configurer votre nouvelle Blazor Server application** , sélectionnez **authentification individuelle (dans l’application)** dans la liste déroulante **authentification** .

1. L’application est créée pour les utilisateurs individuels stockés dans l’application avec ASP.NET Core Identity .

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli/)

Créez un Blazor Server projet avec un mécanisme d’authentification à l’aide de la commande suivante dans une interface de commande :

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Les valeurs autorisées d’authentification (`{AUTHENTICATION}`) sont présentées dans le tableau suivant.

| Mécanisme d’authentification | Description |
| ------------------------ | ----------- |
| `None` (valeur par défaut)         | Aucune authentification |
| `Individual`             | Utilisateurs stockés dans l’application avec ASP.NET Core Identity |
| `IndividualB2C`          | Utilisateurs stockés dans [Azure ad B2C](xref:security/authentication/azure-ad-b2c) |
| `SingleOrg`              | Authentification d’organisation pour un seul locataire |
| `MultiOrg`               | Authentification d’organisation pour plusieurs locataires |
| `Windows`                | Authentification Windows |

À l’aide de l' `-o|--output` option, la commande utilise la valeur fournie pour l' `{APP NAME}` espace réservé à :

* Créez un dossier pour le projet.
* Nommez ce projet.

Pour plus d'informations :

* Consultez la [`dotnet new`](/dotnet/core/tools/dotnet-new) commande dans le guide .net core.
* Exécutez la commande help pour le Blazor Server modèle ( `blazorserver` ) dans une interface de commande :

  ```dotnetcli
  dotnet new blazorserver --help
  ```

---

## <a name="scaffold-identity"></a>Destin Identity

Génération Identity de modèles automatique dans un Blazor Server projet :

* [Sans autorisation existante](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).
* [Avec autorisation](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).

## <a name="additional-claims-and-tokens-from-external-providers"></a>Revendications et jetons supplémentaires des fournisseurs externes

Pour stocker des revendications supplémentaires de fournisseurs externes, consultez <xref:security/authentication/social/additional-claims> .

## <a name="azure-app-service-on-linux-with-identity-server"></a>Azure App Service sur Linux avec Identity serveur

Spécifiez l’émetteur de manière explicite lors du déploiement sur Azure App Service sur Linux avec le Identity serveur. Pour plus d’informations, consultez <xref:security/authentication/identity/spa#azure-app-service-on-linux>.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Démarrage rapide : Ajouter la connexion avec Microsoft à une application web ASP.NET Core](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [Démarrage rapide : Protéger une API web ASP.NET Core avec la plateforme d’identités Microsoft](/azure/active-directory/develop/quickstart-v2-aspnet-core-web-api)
* <xref:host-and-deploy/proxy-load-balancer>: Fournit des conseils sur :
  * Utilisation de l’intergiciel d’en-têtes transférés pour conserver les informations de schéma HTTPs entre les serveurs proxy et les réseaux internes.
  * Scénarios et cas d’utilisation supplémentaires, y compris la configuration manuelle du schéma, les modifications du chemin des demandes pour le routage des demandes correct et le transfert du modèle de demande pour les proxys inversés Linux et non-IIS.
