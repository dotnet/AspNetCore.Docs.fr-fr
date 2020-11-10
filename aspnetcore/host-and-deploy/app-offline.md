---
title: Fichier hors connexion de l’application (app_offline.htm)
author: rick-anderson
description: Découvrez comment le fichier hors connexion de l’application ( `app_offline.htm` ) fonctionne avec le Module ASP.net core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/iis/app-offline
ms.openlocfilehash: 29f3fc5ecd18196d914a46629bc9eb50b183bf61
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431028"
---
# <a name="app-offline-file-app_offlinehtm"></a>Fichier hors connexion de l’application ( `app_offline.htm` )

Le fichier hors connexion de l’application ( `app_offline.htm` ) est utilisé par le Module ASP.net Core pour arrêter une application.

Si un fichier portant le nom `app_offline.htm` est détecté dans le répertoire racine d’une application, le Module ASP.net Core tente d’arrêter correctement l’application et d’arrêter le traitement des demandes entrantes. Si l’application est toujours en cours d’exécution après le nombre de secondes défini dans `shutdownTimeLimit` , le Module ASP.net Core arrête le processus en cours d’exécution.

Pendant que le `app_offline.htm` fichier est présent, le Module ASP.net Core répond aux demandes en renvoyant le contenu du `app_offline.htm` fichier. Lorsque le `app_offline.htm` fichier est supprimé, la requête suivante démarre l’application.

Lorsque vous utilisez le modèle d’hébergement out-of-process, l’application peut ne pas s’arrêter immédiatement s’il existe une connexion ouverte. Par exemple, une connexion WebSocket peut retarder l’arrêt de l’application.

## <a name="locked-deployment-files"></a>Fichiers de déploiement verrouillés

Les fichiers dans le dossier de déploiement sont verrouillés quand l’application est en cours d’exécution. Les fichiers verrouillés ne peuvent pas être remplacés au cours du déploiement.

`app_offline.htm` est le mécanisme principal pour libérer des fichiers verrouillés. `app_offline.htm` est utilisé par Web Deploy pour arrêter et démarrer correctement l’application.

`app_offline.htm` peut être utilisé manuellement pour démarrer et arrêter l’application (nécessite PowerShell 5 ou version ultérieure) :

```powershell
$pathToApp = '{PATH TO APP}'


New-Item -Path $pathToApp -Name "app_offline.htm" -ItemType "file"

# Provide script commands here to deploy the app

Remove-Item -Path $pathToApp\app_offline.htm
```

Dans le script PowerShell précédent :

* L’espace réservé `{PATH TO APP}` est le chemin d’accès à l’application.
* La `New-Item` commande arrête le pool d’applications.
* La `Remove-Item` commande démarre le pool d’applications.
* Les commandes entre la `New-Item` commande et la `Remove-Item` commande sont fournies par le développeur pour déployer l’application.

Les fichiers peuvent également être déverrouillés en arrêtant manuellement le pool d’applications dans le gestionnaire IIS sur le serveur. N’utilisez pas le `app_offine.htm` fichier lorsque vous utilisez le gestionnaire des services Internet pour arrêter et redémarrer le pool d’applications.
