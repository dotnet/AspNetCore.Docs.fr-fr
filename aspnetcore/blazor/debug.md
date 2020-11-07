---
title: ASP.NET Core de débogage Blazor WebAssembly
author: guardrex
description: Découvrez comment déboguer des Blazor applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2020
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
uid: blazor/debug
ms.openlocfilehash: b7e246c20bf12f8ddf07cff54864836cb535aa60
ms.sourcegitcommit: bb475e69cb647f22cf6d2c6f93d0836c160080d7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2020
ms.locfileid: "94339995"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a>ASP.NET Core de débogage Blazor WebAssembly

[Daniel Roth](https://github.com/danroth27)

Blazor WebAssembly les applications peuvent être déboguées à l’aide des outils de développement de navigateur dans les navigateurs basés sur le chrome (Edge/chrome). Vous pouvez également déboguer votre application à l’aide des environnements de développement intégré (IDE) suivants :

* Visual Studio
* Visual Studio pour Mac
* Visual Studio Code

Les scénarios disponibles sont les suivants :

* Définir et supprimer des points d’arrêt.
* Exécutez l’application avec prise en charge du débogage dans les IDE.
* Pas à pas détaillé dans le code.
* Reprendre l’exécution du code à l’aide d’un raccourci clavier dans IDE.
* Dans la fenêtre variables *locales* , observez les valeurs des variables locales.
* Consultez la pile des appels, y compris les chaînes d’appels entre JavaScript et .NET.

Pour le moment, vous *ne pouvez pas* :

* Arrêt sur les exceptions non gérées.
* Atteindre les points d’arrêt pendant le démarrage de l’application avant l’exécution du proxy de débogage. Cela comprend les points d’arrêt dans `Program.Main` ( `Program.cs` ) et les points d’arrêt dans les [ `OnInitialized{Async}` méthodes](xref:blazor/components/lifecycle#component-initialization-methods) des composants qui sont chargés par la première page demandée à partir de l’application.

## <a name="prerequisites"></a>Prérequis

Le débogage requiert l’un des navigateurs suivants :

* Google Chrome (version 70 ou ultérieure) (par défaut)
* Microsoft Edge (version 80 ou ultérieure)

Assurez-vous que les pare-feu ou les proxys ne bloquent pas la communication avec le proxy de débogage ( `NodeJS` processus). Pour plus d’informations, consultez la section [configuration du pare-feu](#firewall-configuration) .

Visual Studio pour Mac nécessite la version 8,8 (Build 1532) ou version ultérieure :

1. Installez la dernière version de Visual Studio pour Mac en sélectionnant le bouton **télécharger Visual Studio pour Mac** sur [Microsoft : Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac/).
1. Sélectionnez le canal de l' *Aperçu* dans Visual Studio. Pour plus d’informations, consultez [installer une version préliminaire de Visual Studio pour Mac](/visualstudio/mac/install-preview).

> [!NOTE]
> Apple Safari sur macOS n’est pas pris en charge actuellement.

## <a name="enable-debugging"></a>Activer le débogage

Pour activer le débogage d’une Blazor WebAssembly application existante, mettez à jour le `launchSettings.json` fichier dans le projet de démarrage pour inclure la `inspectUri` propriété suivante dans chaque profil de lancement :

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

Une fois mis à jour, le `launchSettings.json` fichier doit ressembler à l’exemple suivant :

[!code-json[](debug/launchSettings.json?highlight=14,22)]

La `inspectUri` propriété :

* Permet à l’IDE de détecter que l’application est une Blazor WebAssembly application.
* Indique à l’infrastructure de débogage de script de se connecter au navigateur via le Blazor proxy de débogage de.

Les valeurs d’espace réservé pour le protocole WebSockets ( `wsProtocol` ), l’hôte ( `url.hostname` ), le port ( `url.port` ) et l’URI de l’inspecteur sur le navigateur lancé ( `browserInspectUri` ) sont fournies par l’infrastructure.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Pour déboguer une Blazor WebAssembly application dans Visual Studio :

1. Créez une nouvelle ASP.NET Core application hébergée Blazor WebAssembly .
1. Appuyez sur <kbd>F5</kbd> pour exécuter l’application dans le débogueur.

   > [!NOTE]
   > **Exécuter sans débogage** ( <kbd>CTRL</kbd> + <kbd>F5</kbd>) n’est pas pris en charge. Lorsque l’application est exécutée dans la configuration Debug, le débogage entraîne toujours une réduction des performances minime.

1. Dans l' `*Client*` application, définissez un point d’arrêt sur la `currentCount++;` ligne dans `Pages/Counter.razor` .
1. Dans le navigateur, accédez à la `Counter` page et sélectionnez le bouton **Click Me** pour atteindre le point d’arrêt.
1. Dans Visual Studio, examinez la valeur du `currentCount` champ dans la fenêtre **variables locales** .
1. Appuyez sur <kbd>F5</kbd> pour poursuivre l’exécution.

Lors du débogage d’une Blazor WebAssembly application, vous pouvez également déboguer le code serveur :

1. Définissez un point d’arrêt dans la `Pages/FetchData.razor` page de <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .
1. Définissez un point d’arrêt dans la `WeatherForecastController` `Get` méthode d’action.
1. Accédez à la `Fetch Data` page pour atteindre le premier point d’arrêt dans le `FetchData` composant juste avant qu’il ne envoie une requête HTTP au serveur.
1. Appuyez sur <kbd>F5</kbd> pour poursuivre l’exécution, puis appuyez sur le point d’arrêt sur le serveur dans le `WeatherForecastController` .
1. Appuyez de nouveau sur <kbd>F5</kbd> pour permettre à l’exécution de se poursuivre et consultez le tableau prévisions météo rendu dans le navigateur.

> [!NOTE]
> Les points d’arrêt ne sont **pas** atteints pendant le démarrage de l’application avant l’exécution du proxy de débogage. Cela comprend les points d’arrêt dans `Program.Main` ( `Program.cs` ) et les points d’arrêt dans les [ `OnInitialized{Async}` méthodes](xref:blazor/components/lifecycle#component-initialization-methods) des composants qui sont chargés par la première page demandée à partir de l’application.

Si l’application est hébergée dans un [chemin d’accès de base](xref:blazor/host-and-deploy/index#app-base-path) différent de `/` , mettez à jour les propriétés suivantes dans `Properties/launchSettings.json` pour refléter le chemin de base de l’application :

* `applicationUrl`:

  ```json
  "iisSettings": {
    ...
    "iisExpress": {
      "applicationUrl": "http://localhost:{INSECURE PORT}/{APP BASE PATH}/",
      "sslPort": {SECURE PORT}
    }
  },
  ```

* `inspectUri` de chaque profil :

  ```json
  "profiles": {
    ...
    "{PROFILE 1, 2, ... N}": {
      ...
      "inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/{APP BASE PATH}/_framework/debug/ws-proxy?browser={browserInspectUri}",
      ...
    }
  }
  ```

Les espaces réservés dans les paramètres précédents :

* `{INSECURE PORT}`: Le port non sécurisé. Une valeur aléatoire est fournie par défaut, mais un port personnalisé est autorisé.
* `{APP BASE PATH}`: Chemin d’accès de base de l’application.
* `{SECURE PORT}`: Le port sécurisé. Une valeur aléatoire est fournie par défaut, mais un port personnalisé est autorisé.
* `{PROFILE 1, 2, ... N}`: Lance les profils de paramètres. En règle générale, une application spécifie plusieurs profils par défaut (par exemple, un profil pour IIS Express et un profil de projet, qui est utilisé par le serveur Kestrel).

Dans les exemples suivants, l’application est hébergée à l' `/OAT` aide d’un chemin d’accès de base d’application configuré dans `wwwroot/index.html` en tant que `<base href="/OAT/">` :

```json
"applicationUrl": "http://localhost:{INSECURE PORT}/OAT/",
```

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/OAT/_framework/debug/ws-proxy?browser={browserInspectUri}",
```

Pour plus d’informations sur l’utilisation d’un chemin d’accès de base d’application personnalisé pour les Blazor WebAssembly applications, consultez <xref:blazor/host-and-deploy/index#app-base-path> .

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<h2 id="vscode">Déboguer autonome Blazor WebAssembly</h2>

1. Ouvrez l' Blazor WebAssembly application autonome dans vs code.

   Vous pouvez recevoir une notification indiquant qu’une configuration supplémentaire est requise pour activer le débogage :

   > Une configuration supplémentaire est requise pour déboguer des Blazor WebAssembly applications.

   Si vous recevez la notification :

   * Vérifiez que la dernière [extension C# pour Visual Studio code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) est installée. Pour inspecter les extensions installées, ouvrez **Afficher** les  >  **Extensions** à partir de la barre de menus ou sélectionnez l’icône **Extensions** dans l’encadré **activité** .
   * Confirmez que le débogage de l’aperçu JavaScript est activé. Ouvrez les paramètres à partir de la barre de menus (paramètres préférences de **fichiers**  >  **Preferences**  >  **Settings** ). Recherchez à l’aide des mots clés `debug preview` . Dans les résultats de la recherche, vérifiez que la case à cocher **Déboguer > JavaScript : utiliser l’aperçu** est activée. Si l’option permettant d’activer le débogage de l’aperçu n’est pas présente, effectuez une mise à niveau vers la dernière version de VS Code ou installez l' [extension de débogueur JavaScript](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code versions 1,46 ou antérieures).
   * Rechargez la fenêtre.

1. Démarrez le débogage à l’aide du raccourci clavier <kbd>F5</kbd> ou de l’élément de menu.

   > [!NOTE]
   > **Exécuter sans débogage** ( <kbd>CTRL</kbd> + <kbd>F5</kbd>) n’est pas pris en charge. Lorsque l’application est exécutée dans la configuration Debug, le débogage entraîne toujours une réduction des performances minime.

1. Quand vous y êtes invité, sélectionnez l’option de **Blazor WebAssembly débogage** pour démarrer le débogage.

1. L’application autonome est lancée et un navigateur de débogage est ouvert.

1. Dans l' `*Client*` application, définissez un point d’arrêt sur la `currentCount++;` ligne dans `Pages/Counter.razor` .

1. Dans le navigateur, accédez à la `Counter` page et sélectionnez le bouton **Click Me** pour atteindre le point d’arrêt.

> [!NOTE]
> Les points d’arrêt ne sont **pas** atteints pendant le démarrage de l’application avant l’exécution du proxy de débogage. Cela comprend les points d’arrêt dans `Program.Main` ( `Program.cs` ) et les points d’arrêt dans les [ `OnInitialized{Async}` méthodes](xref:blazor/components/lifecycle#component-initialization-methods) des composants qui sont chargés par la première page demandée à partir de l’application.

## <a name="debug-hosted-no-locblazor-webassembly"></a>Débogage hébergé Blazor WebAssembly

1. Ouvrez le Blazor WebAssembly dossier de solution de l’application hébergée dans vs code.

1. Si aucune configuration de lancement n’est définie pour le projet, la notification suivante s’affiche. Sélectionnez **Oui**.

   > Les ressources requises pour la génération et le débogage sont manquantes dans « {nom de l’APPLICATION} ». Faut-il les ajouter ?  »

1. Dans la palette de commandes en haut de la fenêtre, sélectionnez le projet *serveur* dans la solution hébergée.

Un `launch.json` fichier est généré à l’aide de la configuration de lancement pour le lancement du débogueur.

## <a name="attach-to-an-existing-debugging-session"></a>Attacher à une session de débogage existante

Pour attacher une application en cours d’exécution Blazor , créez un `launch.json` fichier avec la configuration suivante :

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> L’attachement à une session de débogage est pris en charge uniquement pour les applications autonomes. Pour utiliser le débogage de pile complète, vous devez lancer l’application à partir de VS Code.

## <a name="launch-configuration-options"></a>Lancer les options de configuration

Les options de configuration de lancement suivantes sont prises en charge pour le `blazorwasm` type de débogage ( `.vscode/launch.json` ).

| Option    | Description |
| --------- | ----------- |
| `request` | Utilisez `launch` pour lancer et attacher une session de débogage à une Blazor WebAssembly application ou `attach` pour attacher une session de débogage à une application déjà en cours d’exécution. |
| `url`     | URL à ouvrir dans le navigateur lors du débogage. La valeur par défaut est `https://localhost:5001`. |
| `browser` | Navigateur à lancer pour la session de débogage. A la valeur `edge` ou `chrome`. La valeur par défaut est `chrome`. |
| `trace`   | Utilisé pour générer des journaux à partir du débogueur JS. Définissez sur `true` pour générer des journaux. |
| `hosted`  | Doit avoir la valeur en `true` cas de lancement et de débogage d’une application hébergée Blazor WebAssembly . |
| `webRoot` | Spécifie le chemin d’accès absolu du serveur Web. Doit être défini si une application est servie à partir d’un sous-itinéraire. |
| `timeout` | Nombre de millisecondes d’attente de l’attachement de la session de débogage. La valeur par défaut est 30 000 millisecondes (30 secondes). |
| `program` | Référence au fichier exécutable pour exécuter le serveur de l’application hébergée. Doit être défini si `hosted` a la valeur `true` . |
| `cwd`     | Répertoire de travail dans lequel l’application doit être lancée. Doit être défini si `hosted` a la valeur `true` . |
| `env`     | Variables d’environnement à fournir au processus lancé. Applicable uniquement si `hosted` a la valeur `true` . |

## <a name="example-launch-configurations"></a>Exemples de configurations de lancement

### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a>Lancer et déboguer une Blazor WebAssembly application autonome

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

### <a name="attach-to-a-running-app-at-a-specified-url"></a>Attacher à une application en cours d’exécution à une URL spécifiée

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a>Lancer et déboguer une application hébergée Blazor WebAssembly avec Microsoft Edge

La configuration du navigateur est par défaut Google Chrome. Lorsque vous utilisez Microsoft Edge pour le débogage, affectez à la valeur `browser` `edge` . Pour utiliser Google Chrome, vous ne devez pas définir l' `browser` option ou définir la valeur de l’option sur `chrome` .

```json
{
  "name": "Launch and Debug Hosted Blazor WebAssembly App",
  "type": "blazorwasm",
  "request": "launch",
  "hosted": true,
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}/Server",
  "browser": "edge"
}
```

Dans l’exemple précédent, `MyHostedApp.Server.dll` est l’assembly de l’application *serveur* . Le `.vscode` dossier se trouve dans le dossier de la solution, en regard des `Client` `Server` dossiers, et `Shared` .

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

Pour déboguer une Blazor WebAssembly application dans Visual Studio pour Mac :

1. Créez une nouvelle ASP.NET Core application hébergée Blazor WebAssembly .
1. Appuyez sur <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> pour exécuter l’application dans le débogueur.

   > [!NOTE]
   > **Exécuter sans débogage** ( <kbd>&#8997;</kbd> + <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd>) n’est pas pris en charge. Lorsque l’application est exécutée dans la configuration Debug, le débogage entraîne toujours une réduction des performances minime.

   > [!IMPORTANT]
   > Google Chrome ou Microsoft Edge doit être le navigateur sélectionné pour la session de débogage.

1. Dans l' `*Client*` application, définissez un point d’arrêt sur la `currentCount++;` ligne dans `Pages/Counter.razor` .
1. Dans le navigateur, accédez à la `Counter` page, puis sélectionnez le bouton **Click Me** pour atteindre le point d’arrêt :
1. Dans Visual Studio, examinez la valeur du `currentCount` champ dans la fenêtre **variables locales** .
1. Appuyez sur <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> pour poursuivre l’exécution.

Lors du débogage d’une Blazor WebAssembly application, vous pouvez également déboguer le code serveur :

1. Définissez un point d’arrêt dans la `Pages/FetchData.razor` page de <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .
1. Définissez un point d’arrêt dans la `WeatherForecastController` `Get` méthode d’action.
1. Accédez à la `Fetch Data` page pour atteindre le premier point d’arrêt dans le `FetchData` composant juste avant qu’il ne envoie une requête HTTP au serveur.
1. Appuyez sur <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> pour poursuivre l’exécution, puis appuyez sur le point d’arrêt sur le serveur dans le `WeatherForecastController` .
1. Appuyez de nouveau sur <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> pour permettre à l’exécution de se poursuivre et consultez le tableau prévisions météorologiques rendu dans le navigateur.

> [!NOTE]
> Les points d’arrêt ne sont **pas** atteints pendant le démarrage de l’application avant l’exécution du proxy de débogage. Cela comprend les points d’arrêt dans `Program.Main` ( `Program.cs` ) et les points d’arrêt dans les [ `OnInitialized{Async}` méthodes](xref:blazor/components/lifecycle#component-initialization-methods) des composants qui sont chargés par la première page demandée à partir de l’application.

Pour plus d’informations, consultez [débogage avec Visual Studio pour Mac](/visualstudio/mac/debugging).

---

## <a name="debug-in-the-browser"></a>Déboguer dans le navigateur

*Les instructions de cette section s’appliquent à Google Chrome et à Microsoft Edge s’exécutant sur Windows.*

1. Exécutez une version Debug de l’application dans l’environnement de développement.

1. Lancez un navigateur et accédez à l’URL de l’application (par exemple, `https://localhost:5001` ).

1. Dans le navigateur, essayez de commencer le débogage à distance en appuyant sur <kbd>MAJ</kbd> + <kbd>ALT</kbd> + <kbd>d</kbd>.

   Le navigateur doit s’exécuter avec le débogage à distance activé, qui n’est pas le paramètre par défaut. Si le débogage distant est désactivé, une page d’erreur **Impossible de trouver un onglet de navigateur pouvant être débogué** est affichée avec des instructions pour lancer le navigateur avec le port de débogage ouvert. Suivez les instructions de votre navigateur pour ouvrir une nouvelle fenêtre de navigateur. Fermez la fenêtre de navigateur précédente.

<!-- HOLD 
1. In the browser, attempt to commence remote debugging by pressing:

   * <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd> on Windows.
   * <kbd>Shift</kbd>+<kbd>&#8984;</kbd>+<kbd>d</kbd> on macOS.

   The browser must be running with remote debugging enabled, which isn't the default. If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open. Follow the instructions for your browser, which opens a new browser window. Close the previous browser window.
-->

1. Une fois que le navigateur est en cours d’exécution avec le débogage distant activé, le raccourci clavier de débogage de l’étape précédente ouvre un nouvel onglet du débogueur.

1. Après un moment, l’onglet **sources** affiche une liste des assemblys .net de l’application dans le `file://` nœud.

1. Dans le code du composant ( `.razor` fichiers) et les fichiers de code C# ( `.cs` ), les points d’arrêt que vous définissez sont atteints lors de l’exécution du code. Une fois le point d’arrêt atteint, une seule étape (<kbd>F10</kbd>) passe par l’exécution du code ou de la reprise (<kbd>F8</kbd>).

Blazor fournit un proxy de débogage qui implémente le [protocole chrome devtools](https://chromedevtools.github.io/devtools-protocol/) et augmente le protocole avec. Informations spécifiques à .net. Quand le raccourci clavier de débogage est enfoncé, Blazor pointe le devtools chrome au niveau du proxy. Le proxy se connecte à la fenêtre du navigateur que vous cherchez à déboguer (par conséquent, il est nécessaire d’activer le débogage distant).

## <a name="browser-source-maps"></a>Mappages des sources du navigateur

Les mappages de source de navigateur permettent au navigateur de mapper les fichiers compilés à leurs fichiers sources d’origine et sont couramment utilisés pour le débogage côté client. Toutefois, Blazor ne mappe actuellement pas C# directement à JavaScript/WASM. Au lieu de cela, Blazor fait l’interprétation du langage intermédiaire dans le navigateur, les mappages de source ne sont donc pas pertinents.

## <a name="firewall-configuration"></a>Configuration du pare-feu

Si un pare-feu bloque la communication avec le proxy de débogage, créez une règle d’exception de pare-feu qui autorise la communication entre le navigateur et le `NodeJS` processus.

> [!WARNING]
> La modification d’une configuration de pare-feu doit être effectuée avec précaution pour éviter de créer des vulnerablities de sécurité. Appliquez avec prudence des conseils de sécurité, suivez les meilleures pratiques de sécurité et respectez les avertissements émis par le fabricant du pare-feu.
>
> Autorisation de la communication ouverte avec le `NodeJS` processus :
>
> * Ouvre le serveur de nœud à toute connexion, en fonction des capacités et de la configuration du pare-feu.
> * Peut être risqué en fonction de votre réseau.
> * **Est recommandé uniquement sur les ordinateurs de développement.**
>
> Si possible, autorisez uniquement la communication ouverte avec le `NodeJS` processus **sur des réseaux approuvés ou privés**.

Pour obtenir des instructions de configuration du [pare-feu Windows](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) , consultez [créer une règle de service ou de programme entrant](/windows/security/threat-protection/windows-firewall/create-an-inbound-program-or-service-rule). Pour plus d’informations, consultez [pare-feu Windows Defender avec fonctions avancées de sécurité](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) et Articles connexes dans la documentation du pare-feu Windows.

## <a name="troubleshoot"></a>Dépanner

Si vous rencontrez des erreurs, les conseils suivants peuvent vous aider :

* Dans l’onglet **débogueur** , ouvrez les outils de développement de votre navigateur. Dans la console, exécutez `localStorage.clear()` pour supprimer tous les points d’arrêt.
* Confirmez que vous avez installé et approuvé le certificat de développement ASP.NET Core HTTPs. Pour plus d'informations, consultez <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.
* Visual Studio requiert l’option **activer le débogage JavaScript pour ASP.net (chrome, Edge et IE)** dans **Outils**  >  **options**  >  **débogage**  >  **général**. Il s’agit du paramètre par défaut pour Visual Studio. Si le débogage ne fonctionne pas, vérifiez que l’option est sélectionnée.
* Si votre environnement utilise un proxy HTTP, assurez-vous qu' `localhost` il est inclus dans les paramètres de contournement du proxy. Pour ce faire, vous pouvez définir la `NO_PROXY` variable d’environnement dans l’un ou l’autre des éléments suivants :
  * `launchSettings.json`Fichier pour le projet.
  * Au niveau des variables d’environnement système ou utilisateur pour qu’il s’applique à toutes les applications. Quand vous utilisez une variable d’environnement, redémarrez Visual Studio pour que la modification prenne effet.
* Assurez-vous que les pare-feu ou les proxys ne bloquent pas la communication avec le proxy de débogage ( `NodeJS` processus). Pour plus d’informations, consultez la section [configuration du pare-feu](#firewall-configuration) .

### <a name="breakpoints-in-oninitializedasync-not-hit"></a>Points d’arrêt `OnInitialized{Async}` non atteints

Le Blazor proxy de débogage du Framework prend un peu de temps, il est donc possible que les points d’arrêt dans la [ `OnInitialized{Async}` méthode Lifecycle](xref:blazor/components/lifecycle#component-initialization-methods) ne soient pas atteints. Nous vous recommandons d’ajouter un délai au début du corps de la méthode pour permettre au proxy de débogage de se lancer avant que le point d’arrêt ne soit atteint. Vous pouvez inclure le délai en fonction d’une [ `if` directive de compilateur](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) pour vous assurer que le délai n’est pas présent pour une version Release de l’application.

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:

```csharp
protected override void OnInitialized()
{
#if DEBUG
    Thread.Sleep(10000)
#endif

    ...
}
```

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:

```csharp
protected override async Task OnInitializedAsync()
{
#if DEBUG
    await Task.Delay(10000)
#endif

    ...
}
```

### <a name="visual-studio-windows-timeout"></a>Délai d’attente de Visual Studio (Windows)

Si Visual Studio lève une exception indiquant que l’adaptateur de débogage n’a pas pu lancer la mention indiquant que le délai d’attente a été atteint, vous pouvez ajuster le délai d’expiration à l’aide d’un paramètre de Registre :

```console
VsRegEdit.exe set "<VSInstallFolder>" HKCU JSDebugger\Options\Debugging "BlazorTimeoutInMilliseconds" dword {TIMEOUT}
```

L' `{TIMEOUT}` espace réservé dans la commande précédente est exprimé en millisecondes. Par exemple, une minute est affectée en tant que `60000` .
