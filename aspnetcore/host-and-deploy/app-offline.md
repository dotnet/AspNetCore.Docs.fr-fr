---
title: Fichier hors connexion de l’application (app_offline.htm)
author: rick-anderson
description: Découvrez comment le fichier hors connexion de l’application ( `app_offline.htm` ) fonctionne avec le Module ASP.net core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: host-and-deploy/iis/app-offline
ms.openlocfilehash: 4d71b95680a9b160ebb25116e35096495a2eaf93
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058647"
---
# <a name="app-offline-file-app_offlinehtm"></a><span data-ttu-id="bfdd6-103">Fichier hors connexion de l’application ( `app_offline.htm` )</span><span class="sxs-lookup"><span data-stu-id="bfdd6-103">App Offline file (`app_offline.htm`)</span></span>

<span data-ttu-id="bfdd6-104">Le fichier hors connexion de l’application ( `app_offline.htm` ) est utilisé par le Module ASP.net Core pour arrêter une application.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-104">The App Offline file (`app_offline.htm`) is used by the ASP.NET Core Module to shut down an app.</span></span>

<span data-ttu-id="bfdd6-105">Si un fichier portant le nom `app_offline.htm` est détecté dans le répertoire racine d’une application, le Module ASP.net Core tente d’arrêter correctement l’application et d’arrêter le traitement des demandes entrantes.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-105">If a file with the name `app_offline.htm` is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shut down the app and stop processing incoming requests.</span></span> <span data-ttu-id="bfdd6-106">Si l’application est toujours en cours d’exécution après le nombre de secondes défini dans `shutdownTimeLimit` , le Module ASP.net Core arrête le processus en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-106">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module stops the running process.</span></span>

<span data-ttu-id="bfdd6-107">Pendant que le `app_offline.htm` fichier est présent, le Module ASP.net Core répond aux demandes en renvoyant le contenu du `app_offline.htm` fichier.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-107">While the `app_offline.htm` file is present, the ASP.NET Core Module responds to requests by sending back the contents of the `app_offline.htm` file.</span></span> <span data-ttu-id="bfdd6-108">Lorsque le `app_offline.htm` fichier est supprimé, la requête suivante démarre l’application.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-108">When the `app_offline.htm` file is removed, the next request starts the app.</span></span>

<span data-ttu-id="bfdd6-109">Lorsque vous utilisez le modèle d’hébergement out-of-process, l’application peut ne pas s’arrêter immédiatement s’il existe une connexion ouverte.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-109">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="bfdd6-110">Par exemple, une connexion WebSocket peut retarder l’arrêt de l’application.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-110">For example, a WebSocket connection may delay app shut down.</span></span>

## <a name="locked-deployment-files"></a><span data-ttu-id="bfdd6-111">Fichiers de déploiement verrouillés</span><span class="sxs-lookup"><span data-stu-id="bfdd6-111">Locked deployment files</span></span>

<span data-ttu-id="bfdd6-112">Les fichiers dans le dossier de déploiement sont verrouillés quand l’application est en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-112">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="bfdd6-113">Les fichiers verrouillés ne peuvent pas être remplacés au cours du déploiement.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-113">Locked files can't be overwritten during deployment.</span></span>

<span data-ttu-id="bfdd6-114">`app_offline.htm` est le mécanisme principal pour libérer des fichiers verrouillés.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-114">`app_offline.htm` is the primary mechanism to release locked files.</span></span> <span data-ttu-id="bfdd6-115">`app_offline.htm` est utilisé par Web Deploy pour arrêter et démarrer correctement l’application.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-115">`app_offline.htm` is used by Web Deploy to properly stop and start the app.</span></span>

<span data-ttu-id="bfdd6-116">`app_offline.htm` peut être utilisé manuellement pour démarrer et arrêter l’application (nécessite PowerShell 5 ou version ultérieure) :</span><span class="sxs-lookup"><span data-stu-id="bfdd6-116">`app_offline.htm` can be manually used to start and stop the app (requires PowerShell 5 or later):</span></span>

```powershell
$pathToApp = '{PATH TO APP}'

New-Item -Path $pathToApp app_offline.htm

# Provide script commands here to deploy the app

Remove-Item -Path $pathToApp app_offline.htm
```

<span data-ttu-id="bfdd6-117">Dans le script PowerShell précédent :</span><span class="sxs-lookup"><span data-stu-id="bfdd6-117">In the preceding PowerShell script:</span></span>

* <span data-ttu-id="bfdd6-118">L’espace réservé `{PATH TO APP}` est le chemin d’accès à l’application.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-118">The placeholder `{PATH TO APP}` is the path to the app.</span></span>
* <span data-ttu-id="bfdd6-119">La `New-Item` commande arrête le pool d’applications.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-119">The `New-Item` command stops the app pool.</span></span>
* <span data-ttu-id="bfdd6-120">La `Remove-Item` commande démarre le pool d’applications.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-120">The `Remove-Item` command starts the app pool.</span></span>
* <span data-ttu-id="bfdd6-121">Les commandes entre la `New-Item` commande et la `Remove-Item` commande sont fournies par le développeur pour déployer l’application.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-121">Commands between the `New-Item` command and the `Remove-Item` command are provided by the developer to deploy the app.</span></span>

<span data-ttu-id="bfdd6-122">Les fichiers peuvent également être déverrouillés en arrêtant manuellement le pool d’applications dans le gestionnaire IIS sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-122">Files can also be unlocked by manually stopping the app pool in the IIS Manager on the server.</span></span> <span data-ttu-id="bfdd6-123">N’utilisez pas le `app_offine.htm` fichier lorsque vous utilisez le gestionnaire des services Internet pour arrêter et redémarrer le pool d’applications.</span><span class="sxs-lookup"><span data-stu-id="bfdd6-123">Don't use the `app_offine.htm` file when using the IIS Manager to stop and restart the app pool.</span></span>
