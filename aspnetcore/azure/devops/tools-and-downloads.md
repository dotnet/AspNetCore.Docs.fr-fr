---
title: Outils et téléchargements-DevOps avec ASP.NET Core et Azure
author: CamSoper
description: Outils et téléchargements requis pour DevOps avec ASP.NET Core et Azure.
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18
ms.date: 10/24/2018
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
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: 8c7f10a6b6f8504efaba6054533aba034982820c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056567"
---
# <a name="tools-and-downloads"></a>Outils et téléchargements

Azure dispose de plusieurs interfaces pour la configuration et la gestion des ressources, telles que les [portail Azure](https://portal.azure.com), les [Azure CLI](/cli/azure/), les [Azure PowerShell](/powershell/azure/overview), les [Azure Cloud Shell](https://shell.azure.com/bash)et Visual Studio. Ce guide prend une approche minimaliste et utilise le Azure Cloud Shell chaque fois que cela est possible pour réduire les étapes requises. Toutefois, le Portail Azure doit être utilisé pour certaines parties.

## <a name="prerequisites"></a>Prérequis

Les abonnements suivants sont requis :

* Azure &mdash; si vous n’avez pas de compte, [procurez-vous un essai gratuit](https://azure.microsoft.com/free/dotnet/).
* Azure DevOps Services &mdash; votre abonnement et votre organisation Azure DevOps sont créés dans le chapitre 4.
* GitHub &mdash; si vous n’avez pas de compte, inscrivez-vous [gratuitement](https://github.com/join).

Les outils suivants sont requis :

* [Git](https://git-scm.com/downloads) &mdash; Il est recommandé d’avoir une compréhension fondamentale de Git pour ce guide. Consultez la [documentation git](https://git-scm.com/doc), en particulier [git remote](https://git-scm.com/docs/git-remote) et [git push](https://git-scm.com/docs/git-push).
* [Kit SDK .net Core](https://dotnet.microsoft.com/download/) &mdash; La version 2.1.300 ou ultérieure est requise pour générer et exécuter l’exemple d’application. Si Visual Studio est installé avec la charge de travail de **développement multiplateforme .net Core** , le kit SDK .net Core est déjà installé.

    Vérifiez votre installation de kit SDK .NET Core. Ouvrez une interface de commande, puis exécutez la commande suivante :

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a>Outils recommandés (Windows uniquement)

* Les outils Azure robustes de [Visual Studio](https://visualstudio.microsoft.com)fournissent une interface utilisateur graphique pour la plupart des fonctionnalités décrites dans ce guide. Toutes les éditions de Visual Studio fonctionnent, y compris la version gratuite de Visual Studio Community Edition. Les didacticiels sont rédigés pour illustrer le développement, le déploiement et la DevOps à la fois avec et sans Visual Studio.

  Vérifiez que les [charges de travail](/visualstudio/install/modify-visual-studio) suivantes sont installées sur Visual Studio :

  * Développement web et ASP.NET
  * Développement Azure
  * Développement multiplateforme .NET Core
