---
title: Prise en main de ASP.NET Core SignalR
author: bradygaster
description: Dans ce didacticiel, vous allez créer une application de conversation qui utilise ASP.NET Core SignalR .
ms.author: bradyg
ms.custom: mvc
ms.date: 11/21/2019
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
uid: tutorials/signalr
ms.openlocfilehash: 1dad2264250bf43ec6c1df679b8754a82a5addfd
ms.sourcegitcommit: aa85f2911792a1e4783bcabf0da3b3e7e218f63a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95417667"
---
# <a name="tutorial-get-started-with-aspnet-core-no-locsignalr"></a>Didacticiel : prise en main de ASP.NET Core SignalR

::: moniker range=">= aspnetcore-3.0"

Ce didacticiel enseigne les bases de la création d’une application en temps réel à l’aide de SignalR . Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créez un projet web.
> * Ajoutez la SignalR bibliothèque cliente.
> * Créez un SignalR concentrateur.
> * Configurez le projet à utiliser SignalR .
> * Ajouter du code qui envoie des messages de n’importe quel client vers tous les clients connectés.

À la fin, vous disposerez d’une application de conversation opérationnelle :

![::: No-Loc (Signalr) ::: Sample App](signalr/_static/3.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a>Prérequis

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app-project"></a>Créer un projet d’application web

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

* Dans le menu, sélectionnez **Fichier > Nouveau projet**.

* Dans la boîte de dialogue **Créer un projet**, sélectionnez **Application web ASP.NET Core**, puis **Suivant**.

* Dans la boîte de dialogue **configurer votre nouveau projet** , nommez la *SignalR conversation* de projet, puis sélectionnez **créer**.

* Dans la boîte de dialogue **créer une application web ASP.net Core** , sélectionnez **.net Core** et **ASP.net Core 3,1**. 

* Sélectionnez **application Web** pour créer un projet qui utilise des Razor pages, puis sélectionnez **créer**.

  ![Boîte de dialogue Nouveau projet dans Visual Studio](signalr/_static/3.x/signalr-new-project-dialog.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

* Ouvrez le [terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal) dans le dossier dans lequel le nouveau dossier de projet va être créé.

* Exécutez les commandes suivantes :

   ```dotnetcli
   dotnet new webapp -o SignalRChat
   cd SignalRChat
   code -r .
   ```

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* Dans le menu, sélectionnez **Fichier > Nouvelle solution**.

* Sélectionnez **.NET Core > Application > Application web** (ne sélectionnez pas **Application web (modèle-vue-contrôleur)**), puis **Suivant**.

* Assurez-vous que la version cible du .NET **Framework** est définie sur **.net Core 3,1**, puis sélectionnez **suivant**.

* Nommez la *SignalR conversation* de projet, puis sélectionnez **créer**.

---

## <a name="add-the-no-locsignalr-client-library"></a>Ajouter la SignalR bibliothèque cliente

La SignalR bibliothèque serveur est incluse dans l’infrastructure partagée ASP.NET Core 3,1. La bibliothèque cliente JavaScript n’est pas incluse automatiquement dans le projet. Pour ce tutoriel, vous utilisez le Gestionnaire de bibliothèque (LibMan) pour obtenir la bibliothèque cliente à partir de *unpkg*. unpkg est un réseau de distribution de contenu (CDN) qui peut fournir tout ce qui se trouve dans NPM, le gestionnaire de package Node.js.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

* Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Ajouter** > **Bibliothèque côté client**.

* Dans la boîte de dialogue **Ajouter une bibliothèque côté Client**, pour **Fournisseur** sélectionnez **unpkg**.

* Pour **Bibliothèque**, entrez `@microsoft/signalr@latest`.

* Sélectionnez **Choisir des fichiers spécifiques**, développez le dossier *dist/browser*, puis sélectionnez *signalr.js* et *signalr.min.js*.

* Définissez **emplacement cible** sur *wwwroot/js/signalr/*, puis sélectionnez **installer**.

  ![Boîte de dialogue Ajouter une bibliothèque côté client - sélectionner la bibliothèque](signalr/_static/3.x/find-signalr-client-libs-select-files.png)

  LibMan crée un dossier *wwwroot/js/signalr* et y copie les fichiers sélectionnés.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

* Dans le terminal intégré, exécutez la commande suivante pour installer LibMan.

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* Exécutez la commande suivante pour récupérer la SignalR bibliothèque cliente à l’aide de LibMan. Vous devrez peut-être attendre quelques secondes avant que la sortie ne s’affiche.

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  Les paramètres spécifient les options suivantes :
  * Utilisez le fournisseur unpkg.
  * Copiez les fichiers vers la destination *wwwroot/js/signalr* .
  * Copiez uniquement les fichiers spécifiés.

  La sortie ressemble à l’exemple suivant :

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* Dans le **Terminal**, exécutez la commande suivante pour installer LibMan.

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* Accédez au dossier du projet (celui qui contient le fichier *SignalR chat. csproj* ).

* Exécutez la commande suivante pour récupérer la SignalR bibliothèque cliente à l’aide de LibMan.

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  Les paramètres spécifient les options suivantes :
  * Utilisez le fournisseur unpkg.
  * Copiez les fichiers vers la destination *wwwroot/js/signalr* .
  * Copiez uniquement les fichiers spécifiés.

  La sortie ressemble à l’exemple suivant :

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

---

## <a name="create-a-no-locsignalr-hub"></a>Créer un SignalR Hub

Un *hub* est une classe servant de pipeline global qui gère les communications client-serveur.

* Dans le SignalR dossier de projet de conversation, créez un dossier *hubs* .

* Dans le dossier *Hubs*, créez un fichier *ChatHub.cs* avec le code suivant :

  [!code-csharp[ChatHub](signalr/sample-snapshot/3.x/ChatHub.cs)]

  La classe `ChatHub` hérite de la classe SignalR `Hub`. La classe `Hub` gère les connexions, les groupes et la messagerie.

  La méthode `SendMessage` peut être appelée par un client connecté afin d’envoyer un message à tous les clients. Le code client JavaScript qui appelle la méthode est indiqué plus loin dans le didacticiel. SignalR le code est asynchrone pour fournir une évolutivité maximale.

## <a name="configure-no-locsignalr"></a>Configurer SignalR

Le SignalR serveur doit être configuré pour transmettre les SignalR demandes à SignalR .

* Ajoutez le code en surbrillance suivant au fichier *Startup.cs*.

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/Startup.cs?highlight=11,28,55)]

  Ces modifications sont ajoutées SignalR aux systèmes de routage et d’injection de dépendances ASP.net core.

## <a name="add-no-locsignalr-client-code"></a>Ajouter du SignalR code client

* Remplacez le contenu de *Pages\Index.cshtml* par le code suivant :

  [!code-cshtml[Index](signalr/sample-snapshot/3.x/Index.cshtml)]

  Le code précédent :

  * Crée des zones de texte pour le nom et le texte du message, ainsi qu’un bouton Envoyer.
  * Crée une liste avec `id="messagesList"` pour afficher les messages reçus du SignalR Hub.
  * Comprend des références de script à SignalR et le code d’application *chat.js* que vous créez à l’étape suivante.

* Dans le dossier *wwwroot/js*, créez un fichier *chat.js* avec le code suivant :

  [!code-javascript[chat](signalr/sample-snapshot/3.x/chat.js)]

  Le code précédent :

  * Crée et lance une connexion.
  * Ajoute au bouton Envoyer un gestionnaire qui envoie des messages au hub.
  * Ajoute à l’objet de connexion un gestionnaire qui reçoit des messages à partir du hub et les ajoute à la liste.

## <a name="run-the-app"></a>Exécuter l’application

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Appuyez sur **Ctrl+F5** pour exécuter l’application sans débogage.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Dans le terminal intégré, exécutez la commande suivante :

  ```dotnetcli
  dotnet watch run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* Dans le menu, sélectionnez **Exécuter > Démarrer sans débogage**.

---

* Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.

* Choisissez un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton **Envoyer le message**.

  Le nom et le message sont affichés instantanément dans les deux pages.

  ![::: No-Loc (Signalr) ::: Sample App](signalr/_static/3.x/signalr-get-started-finished.png)

> [!TIP]
> * Si l’application ne fonctionne pas, ouvrez vos outils de développement (F12) de navigateur et accédez à la console. Vous pouvez observer des erreurs liées à votre code HTML et JavaScript. Par exemple, supposez que vous placez *signalr.js* dans un dossier autre que celui stipulé. Dans ce cas, la référence à ce fichier ne fonctionnera pas et vous verrez une erreur 404 dans la console.
>   ![Erreur de fichier SignalR.js introuvable](signalr/_static/3.x/f12-console.png)
> * Si vous recevez l’erreur ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY dans Chrome, exécutez les commandes suivantes pour mettre à jour votre certificat de développement :
>
>   ```dotnetcli
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Ce didacticiel enseigne les bases de la création d’une application en temps réel à l’aide de SignalR . Vous allez apprendre à effectuer les actions suivantes : 

> [!div class="checklist"]  
> * Créez un projet web.   
> * Ajoutez la SignalR bibliothèque cliente.   
> * Créez un SignalR concentrateur. 
> * Configurez le projet à utiliser SignalR . 
> * Ajouter du code qui envoie des messages de n’importe quel client vers tous les clients connectés.  
À la fin, vous disposerez d’une application de conversation active :::: ![ No-Loc (signalr) ::: Sample App](signalr/_static/2.x/signalr-get-started-finished.png)   

## <a name="prerequisites"></a>Prérequis    

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)   

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)] 

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]    

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)   

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]    

--- 

## <a name="create-a-web-project"></a>Créer un projet web 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)  

* Dans le menu, sélectionnez **Fichier > Nouveau projet**. 

* Dans la boîte de dialogue **Nouveau projet**, sélectionnez **Installé > Visual C# > Web > Application web ASP.NET Core**. Nommez la *SignalR conversation* de projet.   

  ![Boîte de dialogue Nouveau projet dans Visual Studio](signalr/_static/2.x/signalr-new-project-dialog.png)    

* Sélectionnez **application Web** pour créer un projet qui utilise des Razor pages.   

* Sélectionnez **.NET Core** comme framework cible, sélectionnez **ASP.NET Core 2.2**, puis cliquez sur **OK**.    

  ![Boîte de dialogue Nouveau projet dans Visual Studio](signalr/_static/2.x/signalr-new-project-choose-type.png)   

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)    

* Ouvrez le [terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal) dans le dossier dans lequel le nouveau dossier de projet va être créé.  

* Exécutez les commandes suivantes :   

   ```dotnetcli 
   dotnet new webapp -o SignalRChat   
   code -r SignalRChat    
   ```  

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)   

* Dans le menu, sélectionnez **Fichier > Nouvelle solution**.    

* Sélectionnez **.NET Core > Application > Application web ASP.NET Core** (ne sélectionnez pas **Application web ASP.NET Core (MVC)**).  

* Sélectionnez **Suivant**.  

* Nommez la *SignalR conversation* de projet, puis sélectionnez **créer**. 

--- 

## <a name="add-the-no-locsignalr-client-library"></a>Ajouter la SignalR bibliothèque cliente 

La SignalR bibliothèque serveur est incluse dans le `Microsoft.AspNetCore.App` Package. La bibliothèque cliente JavaScript n’est pas incluse automatiquement dans le projet. Pour ce tutoriel, vous utilisez le Gestionnaire de bibliothèque (LibMan) pour obtenir la bibliothèque cliente à partir de *unpkg*. unpkg est un réseau de distribution de contenu (CDN) qui peut fournir tout ce qui se trouve dans NPM, le gestionnaire de package Node.js.   

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)  

* Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Ajouter** > **Bibliothèque côté client**.  

* Dans la boîte de dialogue **Ajouter une bibliothèque côté Client**, pour **Fournisseur** sélectionnez **unpkg**. 

* Pour **Bibliothèque**, entrez `@microsoft/signalr@3`, puis sélectionnez la version la plus récente qui n’est pas en préversion.  

  ![Boîte de dialogue Ajouter une bibliothèque côté client - sélectionner la bibliothèque](signalr/_static/2.x/libman1.png)   

* Sélectionnez **Choisir des fichiers spécifiques**, développez le dossier *dist/browser*, puis sélectionnez *signalr.js* et *signalr.min.js*. 

* Définissez **Emplacement cible** sur *wwwroot/lib/signalr/*, puis sélectionnez **Installer**.    

  ![Boîte de dialogue Ajouter une bibliothèque côté client - sélectionner les fichiers et la destination](signalr/_static/2.x/libman2.png) 

  LibMan crée un dossier *wwwroot/lib/signalr* et y copie les fichiers sélectionnés.    

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)    

* Dans le terminal intégré, exécutez la commande suivante pour installer LibMan.  

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* Exécutez la commande suivante pour récupérer la SignalR bibliothèque cliente à l’aide de LibMan. Vous devrez peut-être attendre quelques secondes avant que la sortie ne s’affiche. 

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  Les paramètres spécifient les options suivantes : 
  * Utilisez le fournisseur unpkg. 
  * Copiez les fichiers vers la destination *wwwroot/lib/signalr*.    
  * Copiez uniquement les fichiers spécifiés.  

  La sortie ressemble à l’exemple suivant :  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.0.1" to "wwwroot/lib/signalr" 
  ```   

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)   

* Dans le **Terminal**, exécutez la commande suivante pour installer LibMan. 

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* Accédez au dossier du projet (celui qui contient le fichier *SignalR chat. csproj* ).   

* Exécutez la commande suivante pour récupérer la SignalR bibliothèque cliente à l’aide de LibMan.    

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  Les paramètres spécifient les options suivantes : 
  * Utilisez le fournisseur unpkg. 
  * Copiez les fichiers vers la destination *wwwroot/lib/signalr*.    
  * Copiez uniquement les fichiers spécifiés.  

  La sortie ressemble à l’exemple suivant :  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.x.x" to "wwwroot/lib/signalr" 
  ```   

--- 

## <a name="create-a-no-locsignalr-hub"></a>Créer un SignalR Hub   

Un *hub* est une classe servant de pipeline global qui gère les communications client-serveur.   

* Dans le SignalR dossier de projet de conversation, créez un dossier *hubs* .  

* Dans le dossier *Hubs*, créez un fichier *ChatHub.cs* avec le code suivant : 

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/ChatHub.cs)]   

  La classe `ChatHub` hérite de la classe SignalR `Hub`. La classe `Hub` gère les connexions, les groupes et la messagerie.  

  La méthode `SendMessage` peut être appelée par un client connecté afin d’envoyer un message à tous les clients. Le code client JavaScript qui appelle la méthode est indiqué plus loin dans le didacticiel. SignalR le code est asynchrone pour fournir une évolutivité maximale.    

## <a name="configure-no-locsignalr"></a>Configurer SignalR  

Le SignalR serveur doit être configuré pour transmettre les SignalR demandes à SignalR .    

* Ajoutez le code en surbrillance suivant au fichier *Startup.cs*.  

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/Startup.cs?highlight=7,33,52-55)]  

  Ces modifications sont ajoutées SignalR au système d’injection de dépendances ASP.net Core et au pipeline de l’intergiciel (middleware).  

## <a name="add-no-locsignalr-client-code"></a>Ajouter du SignalR code client    

* Remplacez le contenu de *Pages\Index.cshtml* par le code suivant :  

  [!code-cshtml[Index](signalr/sample-snapshot/2.x/Index.cshtml)]   

  Le code précédent :   

  * Crée des zones de texte pour le nom et le texte du message, ainsi qu’un bouton Envoyer.  
  * Crée une liste avec `id="messagesList"` pour afficher les messages reçus du SignalR Hub.   
  * Comprend des références de script à SignalR et le code d’application *chat.js* que vous créez à l’étape suivante.    

* Dans le dossier *wwwroot/js*, créez un fichier *chat.js* avec le code suivant :  

  [!code-javascript[Index](signalr/sample-snapshot/2.x/chat.js)]    

  Le code précédent :   

  * Crée et lance une connexion.    
  * Ajoute au bouton Envoyer un gestionnaire qui envoie des messages au hub. 
  * Ajoute à l’objet de connexion un gestionnaire qui reçoit des messages à partir du hub et les ajoute à la liste.  

## <a name="run-the-app"></a>Exécuter l’application  

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)   

* Appuyez sur **Ctrl+F5** pour exécuter l’application sans débogage.   

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

* Dans le terminal intégré, exécutez la commande suivante :    

  ```dotnetcli
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* Dans le menu, sélectionnez **Exécuter > Démarrer sans débogage**.

---

* Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.

* Choisissez un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton **Envoyer le message**.  

  Le nom et le message sont affichés instantanément dans les deux pages.   

  ![::: No-Loc (Signalr) ::: Sample App](signalr/_static/2.x/signalr-get-started-finished.png) 

> [!TIP]    
> Si l’application ne fonctionne pas, ouvrez vos outils de développement (F12) de navigateur et accédez à la console. Vous pouvez observer des erreurs liées à votre code HTML et JavaScript. Par exemple, supposez que vous placez *signalr.js* dans un dossier autre que celui stipulé. Dans ce cas, la référence à ce fichier ne fonctionnera pas et vous verrez une erreur 404 dans la console.   
> ![Erreur de fichier SignalR.js introuvable](signalr/_static/2.x/f12-console.png)    
## <a name="additional-resources"></a>Ressources supplémentaires 
* [Version YouTube de ce didacticiel](https://www.youtube.com/watch?v=iKlVmu-r0JQ)   

::: moniker-end
