---
title: Bien démarrer avec ASP.NET Core MVC
author: rick-anderson
description: Découvrez comment bien démarrer avec ASP.NET Core MVC.
ms.author: riande
ms.date: 11/16/2020
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
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 1c703cdbd168c2e83d09c40f7740689df8938dad
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422784"
---
# <a name="get-started-with-aspnet-core-mvc"></a>Bien démarrer avec ASP.NET Core MVC

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

Ce didacticiel décrit les principes fondamentaux liés à la génération d’une application web dans ASP.NET Core MVC.

L’application gère une base de données de titres de films. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créez une application web.
> * Ajouter et structurer un modèle.
> * Utiliser une base de données.
> * Ajouter une fonctionnalité de recherche et de validation.

À la fin, vous obtenez une application qui peut gérer et afficher des données de films.

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a>Prérequis

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-app"></a>Créer une application web

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Démarrez Visual Studio et sélectionnez **Créer un projet**.
1. Dans la boîte de dialogue **créer un nouveau projet** , sélectionnez **ASP.net Core application Web** > **suivant**.
1. Dans la boîte de dialogue **configurer votre nouveau projet** , entrez `MvcMovie` pour **nom du projet**. Il est important d’utiliser ce nom exact, y compris la mise en majuscules, donc chaque correspond à la `namespace` copie du code.
1. Sélectionnez **Create** (Créer).
1. Dans la boîte de dialogue **créer une application web ASP.net Core** , sélectionnez :
    1. **.Net Core** et **ASP.net Core 5,0** dans les listes déroulantes.
    1. **ASP.net Core application Web (Model-View-Controller)**.
    1. **Créer**

![Créer une application Web de ASP.NET Core ](start-mvc/_static/5/mvc.png)

Pour obtenir d’autres approches de création du projet, consultez [créer un projet dans Visual Studio](/visualstudio/ide/create-new-project).

Visual Studio a utilisé le modèle par défaut pour le projet MVC que vous venez de créer. Vous disposez maintenant d’une application fonctionnelle en entrant un nom de projet et en sélectionnant quelques options. Il s’agit d’un projet de démarrage de base.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Ce didacticiel part du principe que vous êtes familiarisé avec VS Code. Pour plus d’informations, consultez [Bien démarrer avec VS Code](https://code.visualstudio.com/docs) et [Aide de Visual Studio Code](#visual-studio-code-help).

* Ouvrez le [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Accédez à un répertoire (`cd`) destiné à contenir le projet.
* Exécutez la commande suivante :

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * Une boîte de dialogue s’affiche avec les **ressources requises pour la génération et le débogage dans’MvcMovie'. Ajoutez-les ?**  Sélectionnez **Oui**

  * `dotnet new mvc -o MvcMovie` : crée un nouveau projet ASP.NET Core MVC dans le dossier *MvcMovie*.
  * `code -r MvcMovie`: Charge le fichier projet *MvcMovie. csproj* dans Visual Studio code.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* Sélectionnez **Fichier** > **Nouvelle solution**.

  ![macOS - Nouvelle solution](start-mvc/_static/new_project_vsmac.png)

* Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez application Web de l’application **.net Core**  >  **App**  >  **(Model-View-Controller)**  >  **suivant**. Dans la version 8,6 ou une version ultérieure, sélectionnez application Web **et**  >  **App**  >  **application console (Model-View-Controller)**  >  **suivant**.

  ![sélection du modèle d’application Web macOS](start-mvc/_static/web_app_template_vsmac.png)

* Dans la boîte de dialogue **configurer votre nouvelle application Web** :

  * Vérifiez que **l’authentification** est définie sur **aucune authentification**.
  * Si vous avez présenté une option permettant de sélectionner un **Framework cible** , sélectionnez la version la plus récente de 5. x.

  Sélectionnez **Suivant**.

* Nommez le projet **MvcMovie** , puis sélectionnez **Créer**.

  ![macOS nom du projet](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a>Exécuter l’application

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Sélectionnez **Ctrl-F5** pour exécuter l'application en mode non-débogage.

[!INCLUDE[](~/includes/trustCertVS.md)]

* Visual Studio démarre [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) et exécute l’application. Notez que la barre d’adresse affiche `localhost:port#`, et non quelque chose comme `example.com`. En effet, `localhost` est le nom d’hôte standard de votre ordinateur local. Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.
* Si vous lancez l’application avec Ctrl+F5 (mode sans débogage), vous pouvez apporter des modifications au code, enregistrer le fichier, actualiser le navigateur et examiner les modifications apportées au code. Beaucoup de développeurs préfèrent utiliser ce mode pour lancer rapidement l’application et voir les modifications.
* Vous pouvez lancer l’application en mode débogage ou non-débogage à partir de l’élément de menu **Déboguer** :

  ![Menu Déboguer](start-mvc/_static/debug_menu.png)

* Vous pouvez déboguer l’application en sélectionnant le bouton **IIS Express**

  ![IIS Express](start-mvc/_static/iis_express.png)

  L’image suivante montre l’application :

  ![Page d’accueil ou d’index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Appuyez sur Ctrl+F5 pour exécuter sans le débogueur.

[!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code démarre [Kestrel](xref:fundamentals/servers/kestrel), lance un navigateur, puis accède à `https://localhost:5001`. La barre d’adresses affiche `localhost:port:5001` au lieu de quelque chose qui ressemble à `example.com`. La raison en est que `localhost` est le nom d’hôte standard de l’ordinateur local. Localhost traite uniquement les requêtes web de l’ordinateur local.

  Si vous lancez l’application avec Ctrl+F5 (mode sans débogage), vous pouvez apporter des modifications au code, enregistrer le fichier, actualiser le navigateur et examiner les modifications apportées au code. De nombreux développeurs préfèrent utiliser le mode sans débogage pour actualiser les modifications des pages et des vues.

  ![Page d’accueil ou d’index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

Sélectionnez **exécuter**  >  **Démarrer sans débogage** pour lancer l’application. Visual Studio pour Mac démarre le serveur [Kestrel](xref:fundamentals/servers/index#kestrel), lance un navigateur et accède à `http://localhost:port`, où *port* est un numéro de port choisi de façon aléatoire.

[!INCLUDE[](~/includes/trustCertMac.md)]

* La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`. En effet, `localhost` est le nom d’hôte standard de votre ordinateur local. Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web. Quand vous exécutez l’application, vous voyez un autre numéro de port.
* Vous pouvez lancer l’application en mode débogage ou non-débogage à partir du menu **Exécuter**.

  L’image suivante montre l’application :

  ![Page d’accueil ou d’index](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

Dans la prochaine partie de ce didacticiel, vous allez découvrir MVC et commencer à écrire du code.

> [!div class="step-by-step"]
> [Next](adding-controller.md)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

Ce didacticiel décrit les principes fondamentaux liés à la génération d’une application web dans ASP.NET Core MVC.

L’application gère une base de données de titres de films. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créez une application web.
> * Ajouter et structurer un modèle.
> * Utiliser une base de données.
> * Ajouter une fonctionnalité de recherche et de validation.

À la fin, vous obtenez une application qui peut gérer et afficher des données de films.

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a>Prérequis

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a>Créer une application web

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Dans Visual Studio, sélectionnez **Créer un projet**.

* Sélectionnez **ASP.net Core application Web** > **suivant**.

![Nouvelle application web ASP.NET Core](start-mvc/_static/np_2.1.png)

* Nommez le projet **MvcMovie** , puis sélectionnez **Créer**. Il est important de nommer le projet **MvcMovie** pour que l’espace de noms corresponde quand vous copiez du code.

  ![Nouvelle application web ASP.NET Core](start-mvc/_static/config.png)

* Sélectionnez **application Web (Model-View-Controller)**. Dans les zones de liste déroulante, sélectionnez **.net Core** et **ASP.net Core 3,1** , puis sélectionnez **créer**.

![Boîte de dialogue Nouveau projet, .NET Core dans le volet gauche, web ASP.NET Core ](start-mvc/_static/new_project30.png)

Visual Studio a utilisé le modèle par défaut pour le projet MVC que vous venez de créer. Vous disposez maintenant d’une application fonctionnelle en entrant un nom de projet et en sélectionnant quelques options. Il s’agit d’un projet de démarrage de base.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Il part du principe que vous connaissez déjà VS Code. Pour plus d’informations, consultez [Bien démarrer avec VS Code](https://code.visualstudio.com/docs) et [Aide de Visual Studio Code](#visual-studio-code-help).

* Ouvrez le [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Accédez à un répertoire (`cd`) destiné à contenir le projet.
* Exécutez la commande suivante :

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * Une boîte de dialogue s’affiche avec les **ressources requises pour la génération et le débogage dans’MvcMovie'. Ajoutez-les ?**  Sélectionnez **Oui**

  * `dotnet new mvc -o MvcMovie` : crée un nouveau projet ASP.NET Core MVC dans le dossier *MvcMovie*.
  * `code -r MvcMovie`: Charge le fichier projet *MvcMovie. csproj* dans Visual Studio code.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* Sélectionnez **Fichier** > **Nouvelle solution**.

  ![macOS - Nouvelle solution](start-mvc/_static/new_project_vsmac.png)

* Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez application Web de l’application **.net Core**  >  **App**  >  **(Model-View-Controller)**  >  **suivant**. Dans la version 8,6 ou une version ultérieure, sélectionnez application Web **et**  >  **App**  >  **application console (Model-View-Controller)**  >  **suivant**.

  ![sélection du modèle d’application Web macOS](start-mvc/_static/web_app_template_vsmac.png)

* Dans la boîte de dialogue **configurer votre nouvelle application Web** :

  * Vérifiez que **l’authentification** est définie sur **aucune authentification**.
  * Si vous avez présenté une option permettant de sélectionner un **Framework cible** , sélectionnez la version la plus récente de 3. x.

  Sélectionnez **Suivant**.

* Nommez le projet **MvcMovie** , puis sélectionnez **Créer**.

  ![macOS nom du projet](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a>Exécuter l’application

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Sélectionnez **Ctrl-F5** pour exécuter l'application en mode non-débogage.

[!INCLUDE[](~/includes/trustCertVS.md)]

* Visual Studio démarre [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) et exécute l’application. Notez que la barre d’adresse affiche `localhost:port#`, et non quelque chose comme `example.com`. En effet, `localhost` est le nom d’hôte standard de votre ordinateur local. Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.
* Si vous lancez l’application avec Ctrl+F5 (mode sans débogage), vous pouvez apporter des modifications au code, enregistrer le fichier, actualiser le navigateur et examiner les modifications apportées au code. Beaucoup de développeurs préfèrent utiliser ce mode pour lancer rapidement l’application et voir les modifications.
* Vous pouvez lancer l’application en mode débogage ou non-débogage à partir de l’élément de menu **Déboguer** :

  ![Menu Déboguer](start-mvc/_static/debug_menu.png)

* Vous pouvez déboguer l’application en sélectionnant le bouton **IIS Express**

  ![IIS Express](start-mvc/_static/iis_express.png)

  L’image suivante montre l’application :

  ![Page d’accueil ou d’index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Appuyez sur Ctrl+F5 pour exécuter sans le débogueur.

[!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code démarre [Kestrel](xref:fundamentals/servers/kestrel), lance un navigateur, puis accède à `https://localhost:5001`. La barre d’adresses affiche `localhost:port:5001` au lieu de quelque chose qui ressemble à `example.com`. La raison en est que `localhost` est le nom d’hôte standard de l’ordinateur local. Localhost traite uniquement les requêtes web de l’ordinateur local.

  Si vous lancez l’application avec Ctrl+F5 (mode sans débogage), vous pouvez apporter des modifications au code, enregistrer le fichier, actualiser le navigateur et examiner les modifications apportées au code. De nombreux développeurs préfèrent utiliser le mode sans débogage pour actualiser les modifications des pages et des vues.

  ![Page d’accueil ou d’index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

Sélectionnez **exécuter**  >  **Démarrer sans débogage** pour lancer l’application. Visual Studio pour Mac démarre le serveur [Kestrel](xref:fundamentals/servers/index#kestrel), lance un navigateur et accède à `http://localhost:port`, où *port* est un numéro de port choisi de façon aléatoire.

[!INCLUDE[](~/includes/trustCertMac.md)]

* La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`. En effet, `localhost` est le nom d’hôte standard de votre ordinateur local. Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web. Quand vous exécutez l’application, vous voyez un autre numéro de port.
* Vous pouvez lancer l’application en mode débogage ou non-débogage à partir du menu **Exécuter**.

  L’image suivante montre l’application :

  ![Page d’accueil ou d’index](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

Dans la prochaine partie de ce didacticiel, vous allez découvrir MVC et commencer à écrire du code.

> [!div class="step-by-step"]
> [Next](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

Ce didacticiel décrit les principes fondamentaux liés à la génération d’une application web dans ASP.NET Core MVC.

L’application gère une base de données de titres de films. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créez une application web.
> * Ajouter et structurer un modèle.
> * Utiliser une base de données.
> * Ajouter une fonctionnalité de recherche et de validation.

À la fin, vous obtenez une application qui peut gérer et afficher des données de films.

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a>Prérequis

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a>Créer une application web

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Dans Visual Studio, sélectionnez **Créer un projet**.

* Sélectionnez **Application web ASP.NET Core** , puis **Suivant**.

![Nouvelle application web ASP.NET Core](start-mvc/_static/np_2.1.png)

* Nommez le projet **MvcMovie** , puis sélectionnez **Créer**. Il est important de nommer le projet **MvcMovie** pour que l’espace de noms corresponde quand vous copiez du code.

  ![Nouvelle application web ASP.NET Core](start-mvc/_static/config.png)


* Sélectionnez **Application web (modèle-vue-contrôleur)** , puis **Créer**.

![Boîte de dialogue Nouveau projet, .NET Core dans le volet gauche, web ASP.NET Core ](start-mvc/_static/new_project22-21.png)

Visual Studio a utilisé le modèle par défaut pour le projet MVC que vous venez de créer. Vous disposez maintenant d’une application fonctionnelle en entrant un nom de projet et en sélectionnant quelques options. Il s’agit d’un projet de démarrage de base qui constitue un bon point de départ.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Il part du principe que vous connaissez déjà VS Code. Pour plus d’informations, consultez [Bien démarrer avec VS Code](https://code.visualstudio.com/docs) et [Aide de Visual Studio Code](#visual-studio-code-help).

* Ouvrez le [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Accédez à un répertoire (`cd`) destiné à contenir le projet.
* Exécutez la commande suivante :

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * Une boîte de dialogue s’affiche avec les **ressources requises pour la génération et le débogage dans’MvcMovie'. Ajoutez-les ?**  Sélectionnez **Oui**

  * `dotnet new mvc -o MvcMovie` : crée un nouveau projet ASP.NET Core MVC dans le dossier *MvcMovie*.
  * `code -r MvcMovie`: Charge le fichier projet *MvcMovie. csproj* dans Visual Studio code.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* Sélectionnez **Fichier** > **Nouvelle solution**.

  ![macOS - Nouvelle solution](./start-mvc/_static/new_project_vsmac.png)

* Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez application Web de l’application **.net Core**  >  **App**  >  **(Model-View-Controller)**  >  **suivant**. Dans la version 8,6 ou une version ultérieure, sélectionnez application Web **et**  >  **App**  >  **application console (Model-View-Controller)**  >  **suivant**.

* Dans la boîte de dialogue **configurer votre nouvelle application Web** :

  * Vérifiez que **l’authentification** est définie sur **aucune authentification**.
  * Si vous avez présenté une option permettant de sélectionner un **Framework cible** , sélectionnez la version la plus récente de 2. x.

  Sélectionnez **Suivant**.

* Nommez le projet **MvcMovie** , puis sélectionnez **Créer**.

---

### <a name="run-the-app"></a>Exécuter l’application

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Sélectionnez **Ctrl-F5** pour exécuter l'application en mode non-débogage.

[!INCLUDE[](~/includes/trustCertVS.md)]

* Visual Studio démarre [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) et exécute l’application. Notez que la barre d’adresse affiche `localhost:port#`, et non quelque chose comme `example.com`. En effet, `localhost` est le nom d’hôte standard de votre ordinateur local. Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.
* Si vous lancez l’application avec Ctrl+F5 (mode sans débogage), vous pouvez apporter des modifications au code, enregistrer le fichier, actualiser le navigateur et examiner les modifications apportées au code. Beaucoup de développeurs préfèrent utiliser ce mode pour lancer rapidement l’application et voir les modifications.
* Vous pouvez lancer l’application en mode débogage ou non-débogage à partir de l’élément de menu **Déboguer** :

  ![Menu Déboguer](start-mvc/_static/debug_menu.png)

* Vous pouvez déboguer l’application en sélectionnant le bouton **IIS Express**

  ![IIS Express](start-mvc/_static/iis_express.png)

* Sélectionnez **Accepter** pour consentir au suivi. Cette application ne suit pas les informations personnelles. Le code généré par le modèle inclut des ressources qui aident à satisfaire au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr).

  ![Page d’accueil ou d’index](start-mvc/_static/privacy.png)

  L’illustration suivante montre l’application une fois le suivi accepté :

  ![Page d’accueil ou d’index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Appuyez sur Ctrl+F5 pour exécuter sans le débogueur.

[!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code démarre [Kestrel](xref:fundamentals/servers/kestrel), lance un navigateur, puis accède à `https://localhost:5001`. La barre d’adresses affiche `localhost:port:5001` au lieu de quelque chose qui ressemble à `example.com`. La raison en est que `localhost` est le nom d’hôte standard de l’ordinateur local. Localhost traite uniquement les requêtes web de l’ordinateur local.

  Si vous lancez l’application avec Ctrl+F5 (mode sans débogage), vous pouvez apporter des modifications au code, enregistrer le fichier, actualiser le navigateur et examiner les modifications apportées au code. De nombreux développeurs préfèrent utiliser le mode sans débogage pour actualiser les modifications des pages et des vues.

* Sélectionnez **Accepter** pour consentir au suivi. Cette application ne suit pas les informations personnelles. Le code généré par le modèle inclut des ressources qui aident à satisfaire au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr).

  ![Page d’accueil ou d’index](start-mvc/_static/privacy.png)

  L’illustration suivante montre l’application une fois le suivi accepté :

  ![Page d’accueil ou d’index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

Sélectionnez **exécuter**  >  **Démarrer sans débogage** pour lancer l’application. Visual Studio pour Mac démarre le serveur [Kestrel](xref:fundamentals/servers/index#kestrel), lance un navigateur et accède à `http://localhost:port`, où *port* est un numéro de port choisi de façon aléatoire.

[!INCLUDE[](~/includes/trustCertMac.md)]

* La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`. En effet, `localhost` est le nom d’hôte standard de votre ordinateur local. Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web. Quand vous exécutez l’application, vous voyez un autre numéro de port.
* Vous pouvez lancer l’application en mode débogage ou non-débogage à partir du menu **Exécuter**.

* Sélectionnez **Accepter** pour consentir au suivi. Cette application ne suit pas les informations personnelles. Le code généré par le modèle inclut des ressources qui aident à satisfaire au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr).

  ![Page d’accueil ou d’index](./start-mvc/_static/output_privacy_macos.png)

  L’illustration suivante montre l’application une fois le suivi accepté :

  ![Page d’accueil ou d’index](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

Dans la prochaine partie de ce didacticiel, vous allez découvrir MVC et commencer à écrire du code.

> [!div class="step-by-step"]
> [Next](adding-controller.md)

::: moniker-end
