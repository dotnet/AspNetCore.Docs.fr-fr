---
title: Utiliser ASP.NET Core SignalR avec Blazor WebAssembly
author: guardrex
description: Créez une application de conversation qui utilise ASP.NET Core SignalR avec Blazor WebAssembly .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2020
no-loc:
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
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 6086c422b2d0598af309bbf8b900b9e03a3fe147
ms.sourcegitcommit: d5ecad1103306fac8d5468128d3e24e529f1472c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92491585"
---
# <a name="use-aspnet-core-no-locsignalr-with-no-locblazor-webassembly"></a>Utiliser ASP.NET Core SignalR avec Blazor WebAssembly

Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)

Ce didacticiel enseigne les bases de la création d’une application en temps réel à l’aide de SignalR avec Blazor WebAssembly . Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un Blazor WebAssembly projet d’application hébergée
> * Ajouter la SignalR bibliothèque cliente
> * Ajouter un SignalR Hub
> * Ajouter des SignalR services et un point de terminaison pour le SignalR Hub
> * Ajouter Razor du code de composant pour la conversation

À la fin de ce didacticiel, vous disposerez d’une application de conversation de travail.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Prérequis

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

<!-- * [Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload -->
* [Visual Studio 2019 16,8 ou version ultérieure (en version préliminaire)](https://visualstudio.microsoft.com/vs/preview/) avec la charge de travail **développement Web et ASP.net**
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

<!-- * [Visual Studio for Mac version 8.8 or later (in preview)](https://visualstudio.microsoft.com/vs/mac/) -->
* [Visual Studio pour Mac version 8,8 ou ultérieure (en préversion)](/visualstudio/releasenotes/vs2019-mac-preview-relnotes)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019 16,6 ou version ultérieure](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) avec la charge de travail de **développement ASP.net et Web**
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* [Visual Studio pour Mac version 8,6 ou ultérieure](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

## <a name="create-a-hosted-no-locblazor-webassembly-app-project"></a>Créer un projet d’application hébergée Blazor WebAssembly

Suivez les instructions de votre choix d’outils :

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> Visual Studio 16,8 ou version ultérieure et kit SDK .NET Core 5.0.0 ou version ultérieure sont requis.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> Visual Studio 16,6 ou version ultérieure et kit SDK .NET Core 3.1.300 ou version ultérieure sont requis.

::: moniker-end

1. Créez un projet.

1. Sélectionnez ** Blazor application** , puis sélectionnez **suivant**.

1. Tapez `BlazorSignalRApp` dans le champ **nom du projet** . Confirmez que l’entrée d' **emplacement** est correcte ou indiquez un emplacement pour le projet. Sélectionnez **Create** (Créer).

1. Choisissez le modèle d' ** Blazor WebAssembly application** .

1. Sous **avancé**, activez la case à cocher **ASP.net Core hébergé** .

1. Sélectionnez **Create** (Créer).

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Dans une interface de commande, exécutez la commande suivante :

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. Dans Visual Studio Code, ouvrez le dossier du projet de l’application.

1. Quand la boîte de dialogue semble ajouter des éléments multimédias pour générer et déboguer l’application, sélectionnez **Oui**. Visual Studio Code ajoute automatiquement le `.vscode` dossier avec les `launch.json` fichiers et générés `tasks.json` .

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

1. Installez la dernière version de [Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac/) et procédez comme suit :

1. Sélectionnez **fichier**  >  **nouvelle solution** ou créer un **nouveau** projet à partir de la **fenêtre démarrer**.

1. Dans la barre latérale, sélectionnez application **Web et console**  >  **App**.

1. Choisissez le modèle d' ** Blazor WebAssembly application** . Sélectionnez **Suivant**.

1. Vérifiez que **l’authentification** est définie sur **aucune authentification**. Activez la case à cocher **ASP.net Core hébergé** . Sélectionnez **Suivant**.

1. Dans le champ **nom du projet** , nommez l’application `BlazorSignalRApp` . Sélectionnez **Create** (Créer).

   Si une invite s’affiche pour faire confiance au certificat de développement, approuvez le certificat et continuez. Les mots de passe utilisateur et trousseau sont requis pour approuver le certificat.

1. Ouvrez le projet en accédant au dossier du projet et en ouvrant le fichier solution du projet ( `.sln` ).

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli/)

Dans une interface de commande, exécutez la commande suivante :

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-no-locsignalr-client-library"></a>Ajouter la SignalR bibliothèque cliente

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le `BlazorSignalRApp.Client` projet et sélectionnez **gérer les packages NuGet**.

1. Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la **source du package** est définie sur `nuget.org` .

1. Avec l’option **Parcourir** sélectionnée, tapez `Microsoft.AspNetCore.SignalR.Client` dans la zone de recherche.

1. Dans les résultats de la recherche, sélectionnez le [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package et sélectionnez **installer**.

1. Si la boîte de dialogue **aperçu des modifications** s’affiche, sélectionnez **OK**.

1. Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **J’accepte** si vous acceptez les termes du contrat de licence.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

Dans le **Terminal intégré** (**Afficher**  >  **Terminal** à partir de la barre d’outils), exécutez les commandes suivantes :

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

1. Dans la barre latérale **solution** , cliquez avec le bouton droit sur le `BlazorSignalRApp.Client` projet et sélectionnez **gérer les packages NuGet**.

1. Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la liste déroulante source a la valeur `nuget.org` .

1. Avec l’option **Parcourir** sélectionnée, tapez `Microsoft.AspNetCore.SignalR.Client` dans la zone de recherche.

1. Dans les résultats de la recherche, activez la case à cocher en regard du [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package, puis sélectionnez **Ajouter un package**.

1. Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **accepter** si vous acceptez les termes du contrat de licence.

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli/)

Dans une interface de commande, exécutez les commandes suivantes :

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-no-locsignalr-hub"></a>Ajouter un SignalR Hub

Dans le `BlazorSignalRApp.Server` projet, créez un `Hubs` dossier (pluriel) et ajoutez la `ChatHub` classe suivante ( `Hubs/ChatHub.cs` ) :

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-no-locsignalr-hub"></a>Ajouter des services et un point de terminaison pour le SignalR Hub

1. Dans le projet `BlazorSignalRApp.Server`, ouvrez le fichier `Startup.cs`.

1. Ajoutez l’espace de noms de la `ChatHub` classe au début du fichier :

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. Ajoutez SignalR et répondez les services d’intergiciel (middleware) de compression à `Startup.ConfigureServices` :

::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

::: moniker-end

1. Dans `Startup.Configure` :

   * Utilisez l’intergiciel de compression de réponse en haut de la configuration du pipeline de traitement.
   * Entre les points de terminaison pour les contrôleurs et le secours côté client, ajoutez un point de terminaison pour le Hub.

::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-no-locrazor-component-code-for-chat"></a>Ajouter Razor du code de composant pour la conversation

1. Dans le projet `BlazorSignalRApp.Client`, ouvrez le fichier `Pages/Index.razor`.

1. Remplacez le balisage par le code suivant :

::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a>Exécuter l’application

1. Suivez les instructions pour vos outils :

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Dans **Explorateur de solutions**, sélectionnez le `BlazorSignalRApp.Server` projet. Appuyez sur <kbd>F5</kbd> pour exécuter l’application avec débogage ou sur <kbd>CTRL</kbd> + <kbd>F5</kbd> pour exécuter l’application sans débogage.

1. Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.

1. Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton pour envoyer le message. Le nom et le message s’affichent instantanément sur les deux pages :

   ![::: No-Loc (Signalr) :::::: No-Loc (éblouissant webassembly) ::: exemple d’application ouverte dans deux fenêtres de navigateur présentant les messages échangés.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Lorsque VS Code propose de créer un profil de lancement pour l’application serveur ( `.vscode/launch.json` ), l' `program` entrée ressemble à ce qui suit pour pointer vers l’assembly de l’application ( `{APPLICATION NAME}.Server.dll` ) :

::: moniker range=">= aspnetcore-5.0"

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/net5.0/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

1. Appuyez sur <kbd>F5</kbd> pour exécuter l’application avec débogage ou sur <kbd>CTRL</kbd> + <kbd>F5</kbd> pour exécuter l’application sans débogage.

1. Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.

1. Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton pour envoyer le message. Le nom et le message s’affichent instantanément sur les deux pages :

   ![::: No-Loc (Signalr) :::::: No-Loc (éblouissant webassembly) ::: exemple d’application ouverte dans deux fenêtres de navigateur présentant les messages échangés.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

1. Dans la barre latérale **solution** , sélectionnez le `BlazorSignalRApp.Server` projet. Appuyez sur <kbd>⌘</kbd> + <kbd>↩</kbd> pour exécuter l’application avec débogage ou <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> pour exécuter l’application sans débogage.

1. Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.

1. Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton pour envoyer le message. Le nom et le message s’affichent instantanément sur les deux pages :

   ![::: No-Loc (Signalr) :::::: No-Loc (éblouissant webassembly) ::: exemple d’application ouverte dans deux fenêtres de navigateur présentant les messages échangés.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli/)

1. Dans une interface de commande, exécutez les commandes suivantes :

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.

1. Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton pour envoyer le message. Le nom et le message s’affichent instantanément sur les deux pages :

   ![::: No-Loc (Signalr) :::::: No-Loc (éblouissant webassembly) ::: exemple d’application ouverte dans deux fenêtres de navigateur présentant les messages échangés.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer un Blazor WebAssembly projet d’application hébergée
> * Ajouter la SignalR bibliothèque cliente
> * Ajouter un SignalR Hub
> * Ajouter des SignalR services et un point de terminaison pour le SignalR Hub
> * Ajouter Razor du code de composant pour la conversation

Pour en savoir plus sur Blazor la création d’applications, consultez la Blazor documentation :

> [!div class="nextstepaction"]
> <xref:blazor/index>
> [Authentification du jeton du porteur avec les Identity événements serveur, WebSocket et Server-Sent](xref:signalr/authn-and-authz#bearer-token-authentication)

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:signalr/introduction>
* [SignalR négociation Cross-Origin pour l’authentification](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
