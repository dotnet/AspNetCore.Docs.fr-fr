---
title: Utiliser ASP.NET Core SignalR avec Blazor
author: guardrex
description: Créez une application de conversation qui utilise ASP.NET Core SignalR avec Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/25/2021
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
uid: tutorials/signalr-blazor
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 355db5ad5462747be0058096bc0132ed1071f290
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280991"
---
# <a name="use-aspnet-core-signalr-with-blazor"></a>Utiliser ASP.NET Core SignalR avec Blazor

Ce didacticiel enseigne les bases de la création d’une application en temps réel à l’aide de SignalR avec Blazor . Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un Blazor projet
> * Ajouter la SignalR bibliothèque cliente
> * Ajouter un SignalR Hub
> * Ajouter des SignalR services et un point de terminaison pour le SignalR Hub
> * Ajouter Razor du code de composant pour la conversation

À la fin de ce didacticiel, vous disposerez d’une application de conversation de travail.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Prérequis

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019 16,8 ou version ultérieure](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) avec la charge de travail de **développement ASP.net et Web**
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* [Visual Studio pour Mac version 8,8 ou ultérieure](https://visualstudio.microsoft.com/vs/mac/)
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

::: zone pivot="webassembly"

## <a name="create-a-hosted-blazor-webassembly-app"></a>Créer une application hébergée Blazor WebAssembly

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

1. Sélectionnez **Blazor application** , puis sélectionnez **suivant**.

1. Tapez `BlazorWebAssemblySignalRApp` dans le champ **nom du projet** . Confirmez que l’entrée d' **emplacement** est correcte ou indiquez un emplacement pour le projet. Sélectionnez **Create** (Créer).

1. Choisissez le modèle d' **Blazor WebAssembly application** .

1. Sous **avancé**, activez la case à cocher **ASP.net Core hébergé** .

1. Sélectionnez **Create** (Créer).

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Dans une interface de commande, exécutez la commande suivante :

   ```dotnetcli
   dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
   ```

   L' `-ho|--hosted` option crée une solution hébergée Blazor WebAssembly .

   L' `-o|--output` option crée un dossier pour la solution. Si vous avez créé un dossier pour la solution et que l’interface de commande est ouverte dans ce dossier, omettez l' `-o|--output` option et la valeur pour créer la solution.

1. Dans Visual Studio Code, ouvrez le dossier du projet de l’application.

1. Quand la boîte de dialogue semble ajouter des éléments multimédias pour générer et déboguer l’application, sélectionnez **Oui**. Visual Studio Code ajoute automatiquement le `.vscode` dossier avec les `launch.json` fichiers et générés `tasks.json` .

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

1. Installez la dernière version de [Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac/) et procédez comme suit :

1. Sélectionnez **fichier**  >  **nouvelle solution** ou créer un **nouveau** projet à partir de la **fenêtre démarrer**.

1. Dans la barre latérale, sélectionnez application **Web et console**  >  .

1. Choisissez le modèle d' **Blazor WebAssembly application** . Sélectionnez **Suivant**.

1. Vérifiez que **l’authentification** est définie sur **aucune authentification**. Activez la case à cocher **ASP.net Core hébergé** . Sélectionnez **Suivant**.

1. Dans le champ **nom du projet** , nommez l’application `BlazorWebAssemblySignalRApp` . Sélectionnez **Create** (Créer).

   Si une invite s’affiche pour faire confiance au certificat de développement, approuvez le certificat et continuez. Les mots de passe utilisateur et trousseau sont requis pour approuver le certificat.

1. Ouvrez le projet en accédant au dossier du projet et en ouvrant le fichier solution du projet ( `.sln` ).

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli/)

Dans une interface de commande, exécutez la commande suivante :

```dotnetcli
dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
```

L' `-ho|--hosted` option crée une solution hébergée Blazor WebAssembly .

L' `-o|--output` option crée un dossier pour la solution. Si vous avez créé un dossier pour la solution et que l’interface de commande est ouverte dans ce dossier, omettez l' `-o|--output` option et la valeur pour créer la solution.

---

## <a name="add-the-signalr-client-library"></a>Ajouter la SignalR bibliothèque cliente

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le `BlazorWebAssemblySignalRApp.Client` projet et sélectionnez **gérer les packages NuGet**.

1. Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la **source du package** est définie sur `nuget.org` .

1. Avec l’option **Parcourir** sélectionnée, tapez `Microsoft.AspNetCore.SignalR.Client` dans la zone de recherche.

1. Dans les résultats de la recherche, sélectionnez le [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) Package. Définissez la version pour qu’elle corresponde à l’infrastructure partagée de l’application. Sélectionnez **Installer**.

1. Si la boîte de dialogue **aperçu des modifications** s’affiche, sélectionnez **OK**.

1. Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **J’accepte** si vous acceptez les termes du contrat de licence.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

Dans le **Terminal intégré** (**Afficher**  >  **Terminal** à partir de la barre d’outils), exécutez la commande suivante :

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

Pour ajouter une version antérieure du package, fournissez l' `--version {VERSION}` option, où l' `{VERSION}` espace réservé correspond à la version du package à ajouter.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

1. Dans la barre latérale **solution** , cliquez avec le bouton droit sur le `BlazorWebAssemblySignalRApp.Client` projet et sélectionnez **gérer les packages NuGet**.

1. Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la liste déroulante source a la valeur `nuget.org` .

1. Avec l’option **Parcourir** sélectionnée, tapez `Microsoft.AspNetCore.SignalR.Client` dans la zone de recherche.

1. Dans les résultats de la recherche, activez la case à cocher en regard du [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) Package. Définissez la version pour qu’elle corresponde à l’infrastructure partagée de l’application. Sélectionnez **Ajouter un package**.

1. Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **accepter** si vous acceptez les termes du contrat de licence.

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli/)

Dans une interface de commande à partir du dossier de la solution, exécutez la commande suivante :

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

Pour ajouter une version antérieure du package, fournissez l' `--version {VERSION}` option, où l' `{VERSION}` espace réservé correspond à la version du package à ajouter.

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a>Ajouter le package System. Text. encoders. Web

*Cette section s’applique uniquement aux applications pour ASP.NET Core version 3. x.*

En raison d’un problème de résolution de package lors de l’utilisation [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) de 5. x dans une application ASP.net Core 3. x, le `BlazorWebAssemblySignalRApp.Server` projet requiert une référence de package pour [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) . Le problème sous-jacent sera résolu dans une prochaine version de correctif de .NET 5. Pour plus d’informations, consultez [System.Text.Jssur définit netcoreapp 3.0 sans dépendances (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).

Pour ajouter [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) au `BlazorWebAssemblySignalRApp.Server` projet de la solution ASP.net Core hébergée 3,1 Blazor , suivez les instructions de votre choix d’outils :

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le `BlazorWebAssemblySignalRApp.Server` projet et sélectionnez **gérer les packages NuGet**.

1. Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la **source du package** est définie sur `nuget.org` .

1. Avec l’option **Parcourir** sélectionnée, tapez `System.Text.Encodings.Web` dans la zone de recherche.

1. Dans les résultats de la recherche, sélectionnez le [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) Package. Sélectionnez la version du package qui correspond à l’infrastructure partagée en cours d’utilisation. Sélectionnez **Installer**.

1. Si la boîte de dialogue **aperçu des modifications** s’affiche, sélectionnez **OK**.

1. Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **J’accepte** si vous acceptez les termes du contrat de licence.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

Dans le **Terminal intégré** (**Afficher** > **Terminal** à partir de la barre d’outils), exécutez les commandes suivantes :

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

Pour ajouter une version antérieure du package, fournissez l' `--version {VERSION}` option, où l' `{VERSION}` espace réservé correspond à la version du package à ajouter.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

1. Dans la barre latérale **solution** , cliquez avec le bouton droit sur le `BlazorWebAssemblySignalRApp.Server` projet et sélectionnez **gérer les packages NuGet**.

1. Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la liste déroulante source a la valeur `nuget.org` .

1. Avec l’option **Parcourir** sélectionnée, tapez `System.Text.Encodings.Web` dans la zone de recherche.

1. Dans les résultats de la recherche, activez la case à cocher en regard du [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, sélectionnez la version appropriée du package qui correspond à l’infrastructure partagée en cours d’utilisation, puis sélectionnez **Ajouter un package**.

1. Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **accepter** si vous acceptez les termes du contrat de licence.

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli/)

Dans une interface de commande à partir du dossier de la solution, exécutez la commande suivante :

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

Pour ajouter une version antérieure du package, fournissez l' `--version {VERSION}` option, où l' `{VERSION}` espace réservé correspond à la version du package à ajouter.

---

::: moniker-end

## <a name="add-a-signalr-hub"></a>Ajouter un SignalR Hub

Dans le `BlazorWebAssemblySignalRApp.Server` projet, créez un `Hubs` dossier (pluriel) et ajoutez la `ChatHub` classe suivante ( `Hubs/ChatHub.cs` ) :

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a>Ajouter des services et un point de terminaison pour le SignalR Hub

1. Dans le projet `BlazorWebAssemblySignalRApp.Server`, ouvrez le fichier `Startup.cs`.

1. Ajoutez l’espace de noms de la `ChatHub` classe au début du fichier :

   ```csharp
   using BlazorWebAssemblySignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. Ajoutez SignalR et répondez les services d’intergiciel (middleware) de compression à `Startup.ConfigureServices` :

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,6-10)]

1. Dans `Startup.Configure` :

   * Utilisez l’intergiciel de compression de réponse en haut de la configuration du pipeline de traitement.
   * Entre les points de terminaison pour les contrôleurs et le secours côté client, ajoutez un point de terminaison pour le Hub.

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. Ajoutez SignalR et répondez les services d’intergiciel (middleware) de compression à `Startup.ConfigureServices` :

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. Dans `Startup.Configure` :

   * Utilisez l’intergiciel de compression de réponse en haut de la configuration du pipeline de traitement.
   * Entre les points de terminaison pour les contrôleurs et le secours côté client, ajoutez un point de terminaison pour le Hub.

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a>Ajouter Razor du code de composant pour la conversation

1. Dans le projet `BlazorWebAssemblySignalRApp.Client`, ouvrez le fichier `Pages/Index.razor`.

::: moniker range=">= aspnetcore-5.0"

1. Remplacez le balisage par le code suivant :

   [!code-razor[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. Remplacez le balisage par le code suivant :

   [!code-razor[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a>Exécuter l’application

Suivez les instructions pour vos outils :

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Dans **Explorateur de solutions**, sélectionnez le `BlazorWebAssemblySignalRApp.Server` projet. Appuyez sur <kbd>F5</kbd> pour exécuter l’application avec débogage ou sur <kbd>CTRL</kbd> + <kbd>F5</kbd> pour exécuter l’application sans débogage.

1. Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.

1. Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton pour envoyer le message. Le nom et le message s’affichent instantanément sur les deux pages :

   ![::: No-Loc (Signalr) :::::: No-Loc (éblouissant) ::: Sample App Open dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor/_static/signalr-blazor-finished.png)

   Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Appuyez sur <kbd>F5</kbd> pour exécuter l’application avec débogage ou sur <kbd>CTRL</kbd> + <kbd>F5</kbd> pour exécuter l’application sans débogage.

1. Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.

1. Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton pour envoyer le message. Le nom et le message s’affichent instantanément sur les deux pages :

   ![::: No-Loc (Signalr) :::::: No-Loc (éblouissant) ::: Sample App Open dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor/_static/signalr-blazor-finished.png)

   Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

1. Dans la barre latérale **solution** , sélectionnez le `BlazorWebAssemblySignalRApp.Server` projet. Appuyez sur <kbd>⌘</kbd> + <kbd>↩</kbd> pour exécuter l’application avec débogage ou <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> pour exécuter l’application sans débogage.

1. Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.

1. Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton pour envoyer le message. Le nom et le message s’affichent instantanément sur les deux pages :

   ![::: No-Loc (Signalr) :::::: No-Loc (éblouissant) ::: Sample App Open dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor/_static/signalr-blazor-finished.png)

   Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli/)

1. Dans une interface de commande à partir du dossier de la solution, exécutez les commandes suivantes :

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.

1. Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton pour envoyer le message. Le nom et le message s’affichent instantanément sur les deux pages :

   ![::: No-Loc (Signalr) :::::: No-Loc (éblouissant) ::: Sample App Open dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor/_static/signalr-blazor-finished.png)

   Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

::: zone-end

::: zone pivot="server"

## <a name="create-a-blazor-server-app"></a>Créer une Blazor Server application

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

1. Sélectionnez **Blazor application** , puis sélectionnez **suivant**.

1. Tapez `BlazorServerSignalRApp` dans le champ **nom du projet** . Confirmez que l’entrée d' **emplacement** est correcte ou indiquez un emplacement pour le projet. Sélectionnez **Create** (Créer).

1. Choisissez le modèle d' **Blazor Server application** .

1. Sélectionnez **Create** (Créer).

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Dans une interface de commande, exécutez la commande suivante :

   ```dotnetcli
   dotnet new blazorserver -o BlazorServerSignalRApp
   ```

   L' `-o|--output` option crée un dossier pour le projet. Si vous avez créé un dossier pour le projet et que l’interface de commande est ouverte dans ce dossier, omettez l' `-o|--output` option et la valeur pour créer le projet.

1. Dans Visual Studio Code, ouvrez le dossier du projet de l’application.

1. Quand la boîte de dialogue semble ajouter des éléments multimédias pour générer et déboguer l’application, sélectionnez **Oui**. Visual Studio Code ajoute automatiquement le `.vscode` dossier avec les `launch.json` fichiers et générés `tasks.json` .

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

1. Installez la dernière version de [Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac/) et procédez comme suit :

1. Sélectionnez **fichier**  >  **nouvelle solution** ou créer un **nouveau** projet à partir de la **fenêtre démarrer**.

1. Dans la barre latérale, sélectionnez application **Web et console**  >  .

1. Choisissez le modèle d' **Blazor Server application** . Sélectionnez **Suivant**.

1. Vérifiez que **l’authentification** est définie sur **aucune authentification**. Sélectionnez **Suivant**.

1. Dans le champ **nom du projet** , nommez l’application `BlazorServerSignalRApp` . Sélectionnez **Create** (Créer).

   Si une invite s’affiche pour faire confiance au certificat de développement, approuvez le certificat et continuez. Les mots de passe utilisateur et trousseau sont requis pour approuver le certificat.

1. Ouvrez le projet en accédant au dossier du projet et en ouvrant le fichier solution du projet ( `.sln` ).

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli/)

Dans une interface de commande, exécutez la commande suivante :

```dotnetcli
dotnet new blazorserver -o BlazorServerSignalRApp
```

L' `-o|--output` option crée un dossier pour le projet. Si vous avez créé un dossier pour le projet et que l’interface de commande est ouverte dans ce dossier, omettez l' `-o|--output` option et la valeur pour créer le projet.

---

## <a name="add-the-signalr-client-library"></a>Ajouter la SignalR bibliothèque cliente

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le `BlazorServerSignalRApp` projet et sélectionnez **gérer les packages NuGet**.

1. Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la **source du package** est définie sur `nuget.org` .

1. Avec l’option **Parcourir** sélectionnée, tapez `Microsoft.AspNetCore.SignalR.Client` dans la zone de recherche.

1. Dans les résultats de la recherche, sélectionnez le [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) Package. Définissez la version pour qu’elle corresponde à l’infrastructure partagée de l’application. Sélectionnez **Installer**.

1. Si la boîte de dialogue **aperçu des modifications** s’affiche, sélectionnez **OK**.

1. Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **J’accepte** si vous acceptez les termes du contrat de licence.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

Dans le **Terminal intégré** (**Afficher**  >  **Terminal** à partir de la barre d’outils), exécutez la commande suivante :

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

Pour ajouter une version antérieure du package, fournissez l' `--version {VERSION}` option, où l' `{VERSION}` espace réservé correspond à la version du package à ajouter.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

1. Dans la barre latérale **solution** , cliquez avec le bouton droit sur le `BlazorServerSignalRApp` projet et sélectionnez **gérer les packages NuGet**.

1. Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la liste déroulante source a la valeur `nuget.org` .

1. Avec l’option **Parcourir** sélectionnée, tapez `Microsoft.AspNetCore.SignalR.Client` dans la zone de recherche.

1. Dans les résultats de la recherche, activez la case à cocher en regard du [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) Package. Définissez la version pour qu’elle corresponde à l’infrastructure partagée de l’application. Sélectionnez **Ajouter un package**.

1. Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **accepter** si vous acceptez les termes du contrat de licence.

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli/)

Dans une interface de commande à partir du dossier du projet, exécutez la commande suivante :

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

Pour ajouter une version antérieure du package, fournissez l' `--version {VERSION}` option, où l' `{VERSION}` espace réservé correspond à la version du package à ajouter.

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a>Ajouter le package System. Text. encoders. Web

*Cette section s’applique uniquement aux applications pour ASP.NET Core version 3. x.*

En raison d’un problème de résolution de package lors de l’utilisation [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) de 5. x dans une application ASP.net Core 3. x, le projet requiert une référence de package pour [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) . Le problème sous-jacent sera résolu dans une prochaine version de correctif de .NET 5. Pour plus d’informations, consultez [System.Text.Jssur définit netcoreapp 3.0 sans dépendances (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).

Pour ajouter [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) au projet, suivez les instructions de votre choix d’outils :

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le `BlazorServerSignalRApp` projet et sélectionnez **gérer les packages NuGet**.

1. Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la **source du package** est définie sur `nuget.org` .

1. Avec l’option **Parcourir** sélectionnée, tapez `System.Text.Encodings.Web` dans la zone de recherche.

1. Dans les résultats de la recherche, sélectionnez le [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) Package. Sélectionnez la version du package qui correspond à l’infrastructure partagée en cours d’utilisation. Sélectionnez **Installer**.

1. Si la boîte de dialogue **aperçu des modifications** s’affiche, sélectionnez **OK**.

1. Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **J’accepte** si vous acceptez les termes du contrat de licence.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

Dans le **Terminal intégré** (**Afficher** > **Terminal** à partir de la barre d’outils), exécutez les commandes suivantes :

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

Pour ajouter une version antérieure du package, fournissez l' `--version {VERSION}` option, où l' `{VERSION}` espace réservé correspond à la version du package à ajouter.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

1. Dans la barre latérale **solution** , cliquez avec le bouton droit sur le `BlazorServerSignalRApp` projet et sélectionnez **gérer les packages NuGet**.

1. Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la liste déroulante source a la valeur `nuget.org` .

1. Avec l’option **Parcourir** sélectionnée, tapez `System.Text.Encodings.Web` dans la zone de recherche.

1. Dans les résultats de la recherche, activez la case à cocher en regard du [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, sélectionnez la version appropriée du package qui correspond à l’infrastructure partagée en cours d’utilisation, puis sélectionnez **Ajouter un package**.

1. Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **accepter** si vous acceptez les termes du contrat de licence.

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli/)

Dans une interface de commande à partir du dossier du projet, exécutez la commande suivante :

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

Pour ajouter une version antérieure du package, fournissez l' `--version {VERSION}` option, où l' `{VERSION}` espace réservé correspond à la version du package à ajouter.

---

::: moniker-end

## <a name="add-a-signalr-hub"></a>Ajouter un SignalR Hub

Créez un `Hubs` dossier (pluriel) et ajoutez la `ChatHub` classe suivante ( `Hubs/ChatHub.cs` ) :

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a>Ajouter des services et un point de terminaison pour le SignalR Hub

1. Ouvrez le fichier `Startup.cs` .

1. Ajoutez les espaces de noms pour <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> et la `ChatHub` classe au début du fichier :

   ```csharp
   using Microsoft.AspNetCore.ResponseCompression;
   using BlazorServerSignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. Ajoutez des services d’intergiciels (middleware) de compression des réponses à `Startup.ConfigureServices` :

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. Dans `Startup.Configure` :

   * Utilisez l’intergiciel de compression de réponse en haut de la configuration du pipeline de traitement.
   * Entre les points de terminaison pour le mappage du Blazor Hub et le secours côté client, ajoutez un point de terminaison pour le Hub.

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. Ajoutez des services d’intergiciels (middleware) de compression des réponses à `Startup.ConfigureServices` :

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. Dans `Startup.Configure` :

   * Utilisez l’intergiciel de compression de réponse en haut de la configuration du pipeline de traitement.
   * Entre les points de terminaison pour le mappage du Blazor Hub et le secours côté client, ajoutez un point de terminaison pour le Hub.

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a>Ajouter Razor du code de composant pour la conversation

1. Ouvrez le fichier `Pages/Index.razor` .

::: moniker range=">= aspnetcore-5.0"

1. Remplacez le balisage par le code suivant :

   [!code-razor[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. Remplacez le balisage par le code suivant :

   [!code-razor[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a>Exécuter l’application

Suivez les instructions pour vos outils :

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Appuyez sur <kbd>F5</kbd> pour exécuter l’application avec débogage ou sur <kbd>CTRL</kbd> + <kbd>F5</kbd> pour exécuter l’application sans débogage.

1. Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.

1. Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton pour envoyer le message. Le nom et le message s’affichent instantanément sur les deux pages :

   ![::: No-Loc (Signalr) :::::: No-Loc (éblouissant) ::: Sample App Open dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor/_static/signalr-blazor-finished.png)

   Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Appuyez sur <kbd>F5</kbd> pour exécuter l’application avec débogage ou sur <kbd>CTRL</kbd> + <kbd>F5</kbd> pour exécuter l’application sans débogage.

1. Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.

1. Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton pour envoyer le message. Le nom et le message s’affichent instantanément sur les deux pages :

   ![::: No-Loc (Signalr) :::::: No-Loc (éblouissant) ::: Sample App Open dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor/_static/signalr-blazor-finished.png)

   Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

1. Appuyez sur <kbd>⌘</kbd> + <kbd>↩</kbd> pour exécuter l’application avec débogage ou <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> pour exécuter l’application sans débogage.

1. Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.

1. Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton pour envoyer le message. Le nom et le message s’affichent instantanément sur les deux pages :

   ![::: No-Loc (Signalr) :::::: No-Loc (éblouissant) ::: Sample App Open dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor/_static/signalr-blazor-finished.png)

   Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli/)

1. Dans une interface de commande à partir du dossier du projet, exécutez les commandes suivantes :

   ```dotnetcli
   dotnet run
   ```

1. Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.

1. Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton pour envoyer le message. Le nom et le message s’affichent instantanément sur les deux pages :

   ![::: No-Loc (Signalr) :::::: No-Loc (éblouissant) ::: Sample App Open dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor/_static/signalr-blazor-finished.png)

   Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

::: zone-end

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer un Blazor projet
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
* [SignalR négociation Cross-Origin pour l’authentification](xref:blazor/fundamentals/signalr#signalr-cross-origin-negotiation-for-authentication)
* <xref:blazor/debug>
* <xref:blazor/security/server/threat-mitigation>
