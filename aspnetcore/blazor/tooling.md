---
title: Outils pour ASP.NET Core Blazor
author: guardrex
description: En savoir plus sur les outils disponibles pour créer des Blazor applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/11/2021
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
uid: blazor/tooling
zone_pivot_groups: operating-systems
ms.openlocfilehash: 19270bb74326dccfee9466b7c1fa61daeab805a2
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2021
ms.locfileid: "102394458"
---
# <a name="tooling-for-aspnet-core-blazor"></a>Outils pour ASP.NET Core Blazor

::: zone pivot="windows"

1. Installez la dernière version de [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) avec la charge de travail **développement Web et ASP.net** .

1. Créez un projet.

1. Sélectionnez **Blazor application**. Sélectionnez **Suivant**.

1. Indiquez un nom de projet dans le champ **Nom du projet**, ou acceptez le nom de projet par défaut. Confirmez que l’entrée d' **emplacement** est correcte ou indiquez un emplacement pour le projet. Sélectionnez **Create** (Créer).

1. Pour une Blazor WebAssembly expérience, choisissez le modèle d' **Blazor WebAssembly application** . Pour une Blazor Server expérience, choisissez le modèle d' **Blazor Server application** . Sélectionnez **Create** (Créer).

   Pour une expérience hébergée Blazor WebAssembly , activez la case à cocher **ASP.net Core hébergée** .

   Pour plus d’informations sur les deux Blazor modèles d’hébergement *Blazor WebAssembly* (autonome et hébergé) et *Blazor Server* , consultez <xref:blazor/hosting-models> .

1. Appuyez sur <kbd>CTRL</kbd> + <kbd>F5</kbd> pour exécuter l’application.

Pour plus d’informations sur l’approbation du certificat de développement HTTPs ASP.NET Core, consultez <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> .

Lors de l’exécution d’une application hébergée Blazor WebAssembly , exécutez l’application à partir du projet de la solution **`Server`** .

::: zone-end

::: zone pivot="linux"

1. Installez la dernière version du [Kit SDK .net Core](https://dotnet.microsoft.com/download). Si vous avez déjà installé le kit de développement logiciel (SDK), vous pouvez déterminer la version installée en exécutant la commande suivante dans une interface de commande :

   ```dotnetcli
   dotnet --version
   ```

1. Installez la dernière version de [Visual Studio code](https://code.visualstudio.com).

1. Installez la dernière [extension C# for Visual Studio code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

1. Pour une Blazor WebAssembly expérience, exécutez la commande suivante dans une interface de commande :

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   Pour une expérience hébergée Blazor WebAssembly , ajoutez l’option de l’option Hosted ( `-ho` ou `--hosted` ) à la commande :

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1 -ho
   ```

   Pour une Blazor Server expérience, exécutez la commande suivante dans une interface de commande :

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   Pour plus d’informations sur les deux Blazor modèles d’hébergement *Blazor WebAssembly* (autonome et hébergé) et *Blazor Server* , consultez <xref:blazor/hosting-models> .

1. Ouvrez le dossier `WebApplication1` dans Visual Studio Code.

1. L’IDE demande que vous ajoutiez des ressources pour générer et déboguer le projet. Sélectionnez **Oui**.

   **Blazor WebAssemblyConfiguration de lancement et de tâche hébergée**

   Pour les Blazor WebAssembly solutions hébergées, ajoutez (ou déplacez) le `.vscode` dossier avec les `launch.json` `tasks.json` fichiers et dans le dossier parent de la solution, qui est le dossier qui contient les noms de dossier de projet standard `Client` , `Server` et `Shared` . Mettez à jour ou confirmez que la configuration dans les `launch.json` `tasks.json` fichiers et exécute une application hébergée Blazor WebAssembly à partir du **`Server`** projet.

   **`.vscode/launch.json`** ( `launch` Configuration) :

   ```json
   ...
   "cwd": "${workspaceFolder}/{SERVER APP FOLDER}",
   ...
   ```

   Dans la configuration précédente du répertoire de travail actuel ( `cwd` ), l' `{SERVER APP FOLDER}` espace réservé est le **`Server`** dossier du projet, généralement « `Server` ».

   Si Microsoft Edge est utilisé et que Google Chrome n’est pas installé sur le système, ajoutez une propriété supplémentaire de `"browser": "edge"` à la configuration.

   Exemple de dossier de projet de `Server` et qui génère Microsoft Edge comme navigateur pour les exécutions de débogage au lieu du navigateur par défaut Google Chrome :

   ```json
   ...
   "cwd": "${workspaceFolder}/Server",
   "browser": "edge"
   ...
   ```

   **`.vscode/tasks.json`**(arguments de [ `dotnet` commande](/dotnet/core/tools/dotnet) ) :

   ```json
   ...
   "${workspaceFolder}/{SERVER APP FOLDER}/{PROJECT NAME}.csproj",
   ...
   ```

   Dans l’argument précédent :

   * L' `{SERVER APP FOLDER}` espace réservé est le **`Server`** dossier du projet, généralement « `Server` ».
   * L' `{PROJECT NAME}` espace réservé est le nom de l’application, généralement basé sur le nom de la solution suivi de « `.Server` » dans une application générée à partir du [ Blazor modèle de projet](xref:blazor/project-structure).

   L’exemple suivant du [didacticiel sur l’utilisation de SignalR avec une Blazor WebAssembly application](xref:tutorials/signalr-blazor) utilise un nom de dossier de projet `Server` et un nom de projet `BlazorWebAssemblySignalRApp.Server` :

   ```json
   ...
   "args": [
     "build",
       "${workspaceFolder}/Server/BlazorWebAssemblySignalRApp.Server.csproj",
       "/property:GenerateFullPaths=true",
       "/consoleloggerparameters:NoSummary"
   ],
   ...
   ```

1. Appuyez sur <kbd>CTRL</kbd> + <kbd>F5</kbd> pour exécuter l’application.

## <a name="trust-a-development-certificate"></a>Approuver un certificat de développement

Il n’existe pas de méthode centralisée pour approuver un certificat sur Linux. En règle générale, l’une des approches suivantes est adoptée :

* Excluez l’URL de l’application dans la liste d’exclusion du navigateur.
* Faites confiance à tous les certificats auto-signés pour `localhost` .
* Ajoutez le certificat à la liste des certificats approuvés dans le navigateur.

Pour plus d’informations, consultez les conseils fournis par le fabricant de votre navigateur et la distribution Linux.

::: zone-end

::: zone pivot="macos"

1. Installez [Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac/).

1. Sélectionnez **fichier**  >  **nouvelle solution** ou créer un **nouveau** projet à partir de la **fenêtre démarrer**.

1. Dans la barre latérale, sélectionnez application **Web et console**  >  .

   Pour une Blazor WebAssembly expérience, choisissez le modèle d' **Blazor WebAssembly application** . Pour une Blazor Server expérience, choisissez le modèle d' **Blazor Server application** . Sélectionnez **Suivant**.

   Pour plus d’informations sur les deux Blazor modèles d’hébergement *Blazor WebAssembly* (autonome et hébergé) et *Blazor Server* , consultez <xref:blazor/hosting-models> .

1. Vérifiez que **l’authentification** est définie sur **aucune authentification**. Sélectionnez **Suivant**.

1. Pour une expérience hébergée Blazor WebAssembly , activez la case à cocher **ASP.net Core hébergée** .

1. Dans le champ **nom du projet** , nommez l’application `WebApplication1` . Sélectionnez **Create** (Créer).

1. Sélectionnez **exécuter**  >  **Démarrer sans débogage** pour exécuter l’application *sans le débogueur*. Exécutez l’application avec   >  le bouton exécuter **Démarrer le débogage** ou exécuter (&#9654;) pour exécuter l’application *avec le débogueur*.

Si une invite s’affiche pour faire confiance au certificat de développement, approuvez le certificat et continuez. Les mots de passe utilisateur et trousseau sont requis pour approuver le certificat. Pour plus d’informations sur l’approbation du certificat de développement HTTPs ASP.NET Core, consultez <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> .

Lors de l’exécution d’une application hébergée Blazor WebAssembly , exécutez l’application à partir du projet de la solution **`Server`** .

::: zone-end

## <a name="use-visual-studio-code-for-cross-platform-blazor-development"></a>Utiliser Visual Studio Code pour le développement multiplateforme Blazor

[Visual Studio code](https://code.visualstudio.com/) est un environnement de développement intégré (IDE) Open source et multiplateforme qui peut être utilisé pour développer des Blazor applications. Utilisez l’interface CLI .NET pour créer une nouvelle Blazor application en vue du développement avec Visual Studio code. Pour plus d’informations, consultez la [version Linux de cet article](?pivots=linux).

## <a name="blazor-template-options"></a>Blazor options de modèle

Le Blazor Framework fournit des modèles pour créer des applications pour chacun des deux Blazor modèles d’hébergement. Les modèles sont utilisés pour créer des Blazor projets et des solutions, quels que soient les outils que vous sélectionnez pour le Blazor développement (Visual Studio, Visual Studio pour Mac, Visual Studio code ou l’interface de commande .net) :

* Blazor WebAssembly modèle de projet : `blazorwasm`
* Blazor Server modèle de projet : `blazorserver`

Pour plus d’informations sur les Blazor modèles d’hébergement de, consultez <xref:blazor/hosting-models> . Pour plus d’informations sur les Blazor modèles de projet, consultez <xref:blazor/project-structure> .

Les options de modèle sont disponibles en passant l’option d’aide ( `-h` ou `--help` ) à la [`dotnet new`](/dotnet/core/tools/dotnet-new) commande CLI dans une interface de commande :

```dotnetcli
dotnet new blazorwasm -h
dotnet new blazorserver -h
```

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:blazor/hosting-models>
* <xref:blazor/project-structure>
