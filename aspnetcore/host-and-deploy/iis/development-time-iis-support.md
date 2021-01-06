---
title: Prise en charge d’IIS pendant le développement dans Visual Studio pour ASP.NET Core
author: rick-anderson
description: Découvrez la prise en charge du débogage des applications ASP.NET Core en cas d’exécution avec IIS sur Windows Server.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
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
uid: host-and-deploy/iis/development-time-iis-support
ms.openlocfilehash: ab892b2cdfa61378ac7328c0380c8a6cffc6d376
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058452"
---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a>Prise en charge d’IIS pendant le développement dans Visual Studio pour ASP.NET Core

De [Sourabh Shirhatti](https://twitter.com/sshirhatti)

::: moniker range=">= aspnetcore-3.0"

Cet article décrit la prise en charge de [Visual Studio](https://visualstudio.microsoft.com) pour le débogage des applications ASP.NET Core s’exécutant avec IIS sur Windows Server. Cette rubrique présente ce scénario et la configuration d’un projet.

## <a name="prerequisites"></a>Prérequis

* [Visual Studio pour Windows](https://visualstudio.microsoft.com/downloads/)
* Charge de travail **Développement web et ASP.NET**
* Charge de travail **Développement multiplateforme .NET Core**
* Certificat de sécurité X.509 (pour la prise en charge HTTPS)

## <a name="enable-iis"></a>Activer IIS

1. Dans Windows, accédez à **Panneau de configuration** > **Programmes** > **Programmes et fonctionnalités** > **Activer ou désactiver des fonctionnalités Windows** (à gauche de l’écran).
1. Cochez la case **Services IIS (Internet Information Services)**. Sélectionnez **OK**.

L’installation d’IIS peut nécessiter un redémarrage du système.

## <a name="configure-iis"></a>Configurer IIS

IIS doit avoir un site web configuré avec les éléments suivants :

* **Nom d’hôte**: en général, le **site Web par défaut** est utilisé avec un **nom d’hôte** `localhost` . Toutefois, n’importe quel site web IIS valide avec un nom d’hôte unique fonctionnera.
* **Liaison de site**
  * Pour les applications qui exigent le protocole HTTPS, créez une liaison au port 443 avec un certificat. On utilise en général le **certificat de développement IIS Express**, mais tous les certificats valides conviennent.
  * Pour les applications qui utilisent le protocole HTTP, vérifiez l’existence d’une liaison au port 80 ou créez-en une pour un nouveau site.
  * Utilisez une liaison unique pour HTTP ou HTTPS. **La liaison simultanée aux ports HTTP et aux ports HTTPS n’est pas prise en charge.**

## <a name="enable-development-time-iis-support-in-visual-studio"></a>Activer la prise en charge d’IIS pendant le développement dans Visual Studio

1. Lancez le programme d’installation de Visual Studio.
1. Sélectionnez **Modifier** pour l’installation de Visual Studio que vous souhaitez utiliser dans le cadre de la prise en charge IIS lors du développement.
1. Pour la charge de travail **ASP.NET et développement web**, recherchez et installez le composant **Prise en charge IIS lors du développement**.

   Le composant se trouve dans la section **Facultatif**, sous **Prise en charge IIS lors du développement**, dans le volet **Détails de l’installation** à droite des charges de travail. Ce composant installe le [module ASP.NET Core](xref:host-and-deploy/aspnet-core-module), qui est un module IIS natif nécessaire à l’exécution des applications ASP.NET Core avec IIS.

## <a name="configure-the-project"></a>Configurer le projet

### <a name="https-redirection"></a>Redirection HTTPS

Pour un nouveau projet qui exige le protocole HTTPS, cochez la case **Configurer pour HTTPS** dans la fenêtre **Créer une application web ASP.NET Core** afin d’ajouter [Redirection HTTPS et middleware HSTS](xref:security/enforcing-ssl) à l’application lors de sa création.

Pour un projet existant qui exige le protocole HTTPS, utilisez Redirection HTTPS et middleware HSTS dans `Startup.Configure`. Pour plus d’informations, consultez <xref:security/enforcing-ssl>.

Pour un projet qui utilise le protocole HTTP, [Redirection HTTPS et middleware HSTS](xref:security/enforcing-ssl) ne sont pas ajoutés à l’application. Aucune configuration de l’application n’est nécessaire.

### <a name="iis-launch-profile"></a>Profil de lancement IIS

Créez un profil de lancement pour ajouter la prise en charge d’IIS pendant le développement :

1. Cliquez avec le bouton droit sur le projet dans **l’Explorateur de solutions**. Sélectionner **Propriétés**. Ouvrez l’onglet **Déboguer** .
1. Pour **Profil**, sélectionnez le bouton **Nouveau**. Nommez le profil « IIS » dans la fenêtre contextuelle. Sélectionnez **OK** pour créer le profil.
1. Pour le paramètre **Lancer**, sélectionnez **IIS** dans la liste.
1. Cochez la case **Lancer le navigateur** et indiquez l’URL du point de terminaison.

   Lorsque le protocole HTTPS est requis par l’application, utilisez un point de terminaison HTTPS (`https://`). Pour HTTP, utilisez un point de terminaison HTTP (`http://`).

   Indiquez le nom d’hôte et le port utilisés par la [configuration IIS spécifiée précédemment](#configure-iis), en général `localhost`.

   Indiquez le nom de l’application à la fin de l’URL.

   Par exemple, `https://localhost/WebApplication1` (HTTPS) ou `http://localhost/WebApplication1` (HTTP) sont des URL de point de terminaison valide.
1. Dans la section **Variables d’environnement**, sélectionnez le bouton **Ajouter**. Indiquez une variable d’environnement ayant pour **Nom**`ASPNETCORE_ENVIRONMENT` et pour **Valeur**`Development`.
1. Dans la zone **Paramètres de serveur web**, donnez à **URL de l’application** la valeur utilisée pour l’URL du point de terminaison **Lancer le navigateur**.
1. Pour le paramètre **Modèle d’hébergement** dans Visual Studio 2019 (ou version ultérieure), sélectionnez **Par défaut** afin d’utiliser le modèle d’hébergement du projet. C’est la valeur de la propriété `<AspNetCoreHostingModel>` (`InProcess` ou `OutOfProcess`) qui est employée si elle est définie par le projet dans son fichier de projet. En l’absence de cette propriété, le modèle d’hébergement par défaut de l’application est utilisé in-process. Si l’application réclame un paramètre de modèle d’hébergement explicite autre que son modèle normal, définissez le **Modèle d’hébergement** sur `In Process` ou `Out Of Process` en fonction des besoins.
1. Enregistrez le profil.

Si vous n’utilisez pas Visual Studio, ajoutez manuellement un profil de lancement au fichier [launchSettings.json](https://json.schemastore.org/launchsettings) dans le dossier *Propriétés*. L’exemple suivant configure le profil de façon à utiliser le protocole HTTPS :

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

Vérifiez que les points de terminaison `applicationUrl` et `launchUrl` coïncident et utilisent le même protocole que la configuration de liaison IIS, c’est-à-dire HTTP ou HTTPS.

## <a name="run-the-project"></a>Exécuter le projet

Exécutez Visual Studio en tant qu’administrateur :

* Vérifiez que la liste déroulante des configurations de build est définie sur **Déboguer**.
* Définissez le [bouton Démarrer le débogage](/visualstudio/debugger/debugger-feature-tour) sur le profil **IIS** , puis sélectionnez le bouton pour démarrer l’application.

Visual Studio peut demander un redémarrage si vous ne l’exécutez pas en tant qu’administrateur. Si vous y êtes invité, redémarrez Visual Studio.

Si vous utilisez un certificat de développement non approuvé, le navigateur peut vous amener à créer une exception pour ce certificat.

> [!NOTE]
> Le débogage d’une configuration de build de mise en production avec [Uniquement mon code](/visualstudio/debugger/just-my-code) et des optimisations du compilateur entraîne une baisse des résultats. Par exemple, les points d’arrêt ne sont pas atteints.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Bien démarrer avec le Gestionnaire IIS dans IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Cet article décrit la prise en charge de [Visual Studio](https://visualstudio.microsoft.com) pour le débogage des applications ASP.NET Core s’exécutant avec IIS sur Windows Server. Cette rubrique présente ce scénario et la configuration d’un projet.

## <a name="prerequisites"></a>Prérequis

* [Visual Studio pour Windows](https://visualstudio.microsoft.com/downloads/)
* Charge de travail **Développement web et ASP.NET**
* Charge de travail **Développement multiplateforme .NET Core**
* Certificat de sécurité X.509 (pour la prise en charge HTTPS)

## <a name="enable-iis"></a>Activer IIS

1. Dans Windows, accédez à **Panneau de configuration** > **Programmes** > **Programmes et fonctionnalités** > **Activer ou désactiver des fonctionnalités Windows** (à gauche de l’écran).
1. Cochez la case **Services IIS (Internet Information Services)**. Sélectionnez **OK**.

L’installation d’IIS peut nécessiter un redémarrage du système.

## <a name="configure-iis"></a>Configurer IIS

IIS doit avoir un site web configuré avec les éléments suivants :

* **Nom d’hôte**: en général, le **site Web par défaut** est utilisé avec un **nom d’hôte** `localhost` . Toutefois, n’importe quel site web IIS valide avec un nom d’hôte unique fonctionnera.
* **Liaison de site**
  * Pour les applications qui exigent le protocole HTTPS, créez une liaison au port 443 avec un certificat. On utilise en général le **certificat de développement IIS Express**, mais tous les certificats valides conviennent.
  * Pour les applications qui utilisent le protocole HTTP, vérifiez l’existence d’une liaison au port 80 ou créez-en une pour un nouveau site.
  * Utilisez une liaison unique pour HTTP ou HTTPS. **La liaison simultanée aux ports HTTP et aux ports HTTPS n’est pas prise en charge.**

## <a name="enable-development-time-iis-support-in-visual-studio"></a>Activer la prise en charge d’IIS pendant le développement dans Visual Studio

1. Lancez le programme d’installation de Visual Studio.
1. Sélectionnez **Modifier** pour l’installation de Visual Studio que vous souhaitez utiliser dans le cadre de la prise en charge IIS lors du développement.
1. Pour la charge de travail **ASP.NET et développement web**, recherchez et installez le composant **Prise en charge IIS lors du développement**.

   Le composant se trouve dans la section **Facultatif**, sous **Prise en charge IIS lors du développement**, dans le volet **Détails de l’installation** à droite des charges de travail. Ce composant installe le [module ASP.NET Core](xref:host-and-deploy/aspnet-core-module), qui est un module IIS natif nécessaire à l’exécution des applications ASP.NET Core avec IIS.

## <a name="configure-the-project"></a>Configurer le projet

### <a name="https-redirection"></a>Redirection HTTPS

Pour un nouveau projet qui exige le protocole HTTPS, cochez la case **Configurer pour HTTPS** dans la fenêtre **Créer une application web ASP.NET Core** afin d’ajouter [Redirection HTTPS et middleware HSTS](xref:security/enforcing-ssl) à l’application lors de sa création.

Pour un projet existant qui exige le protocole HTTPS, utilisez Redirection HTTPS et middleware HSTS dans `Startup.Configure`. Pour plus d’informations, consultez <xref:security/enforcing-ssl>.

Pour un projet qui utilise le protocole HTTP, [Redirection HTTPS et middleware HSTS](xref:security/enforcing-ssl) ne sont pas ajoutés à l’application. Aucune configuration de l’application n’est nécessaire.

### <a name="iis-launch-profile"></a>Profil de lancement IIS

Créez un profil de lancement pour ajouter la prise en charge d’IIS pendant le développement :

1. Cliquez avec le bouton droit sur le projet dans **l’Explorateur de solutions**. Sélectionner **Propriétés**. Ouvrez l’onglet **Déboguer** .
1. Pour **Profil**, sélectionnez le bouton **Nouveau**. Nommez le profil « IIS » dans la fenêtre contextuelle. Sélectionnez **OK** pour créer le profil.
1. Pour le paramètre **Lancer**, sélectionnez **IIS** dans la liste.
1. Cochez la case **Lancer le navigateur** et indiquez l’URL du point de terminaison.

   Lorsque le protocole HTTPS est requis par l’application, utilisez un point de terminaison HTTPS (`https://`). Pour HTTP, utilisez un point de terminaison HTTP (`http://`).

   Indiquez le nom d’hôte et le port utilisés par la [configuration IIS spécifiée précédemment](#configure-iis), en général `localhost`.

   Indiquez le nom de l’application à la fin de l’URL.

   Par exemple, `https://localhost/WebApplication1` (HTTPS) ou `http://localhost/WebApplication1` (HTTP) sont des URL de point de terminaison valide.
1. Dans la section **Variables d’environnement**, sélectionnez le bouton **Ajouter**. Indiquez une variable d’environnement ayant pour **Nom**`ASPNETCORE_ENVIRONMENT` et pour **Valeur**`Development`.
1. Dans la zone **Paramètres de serveur web**, donnez à **URL de l’application** la valeur utilisée pour l’URL du point de terminaison **Lancer le navigateur**.
1. Pour le paramètre **Modèle d’hébergement** dans Visual Studio 2019 (ou version ultérieure), sélectionnez **Par défaut** afin d’utiliser le modèle d’hébergement du projet. C’est la valeur de la propriété `<AspNetCoreHostingModel>` (`InProcess` ou `OutOfProcess`) qui est employée si elle est définie par le projet dans son fichier de projet. En l’absence de cette propriété, le modèle d’hébergement par défaut de l’application est utilisé hors processus. Si l’application réclame un paramètre de modèle d’hébergement explicite autre que son modèle normal, définissez le **Modèle d’hébergement** sur `In Process` ou `Out Of Process` en fonction des besoins.
1. Enregistrez le profil.

Si vous n’utilisez pas Visual Studio, ajoutez manuellement un profil de lancement au fichier [launchSettings.json](https://json.schemastore.org/launchsettings) dans le dossier *Propriétés*. L’exemple suivant configure le profil de façon à utiliser le protocole HTTPS :

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

Vérifiez que les points de terminaison `applicationUrl` et `launchUrl` coïncident et utilisent le même protocole que la configuration de liaison IIS, c’est-à-dire HTTP ou HTTPS.

## <a name="run-the-project"></a>Exécuter le projet

Exécutez Visual Studio en tant qu’administrateur :

* Vérifiez que la liste déroulante des configurations de build est définie sur **Déboguer**.
* Définissez le [bouton Démarrer le débogage](/visualstudio/debugger/debugger-feature-tour) sur le profil **IIS** , puis sélectionnez le bouton pour démarrer l’application.

Visual Studio peut demander un redémarrage si vous ne l’exécutez pas en tant qu’administrateur. Si vous y êtes invité, redémarrez Visual Studio.

Si vous utilisez un certificat de développement non approuvé, le navigateur peut vous amener à créer une exception pour ce certificat.

> [!NOTE]
> Le débogage d’une configuration de build de mise en production avec [Uniquement mon code](/visualstudio/debugger/just-my-code) et des optimisations du compilateur entraîne une baisse des résultats. Par exemple, les points d’arrêt ne sont pas atteints.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Bien démarrer avec le Gestionnaire IIS dans IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end
