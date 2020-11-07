---
title: 'Didacticiel : prise en main des Razor pages dans ASP.net Core'
author: rick-anderson
description: Il s’agit du premier didacticiel d’une série qui enseigne les bases de la création d’une Razor application web ASP.net Core pages.
ms.author: riande
ms.date: 09/15/2020
no-loc:
- Index
- Create
- Delete
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
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: b4dcbe9536107cdc5b0342782abc4bad0b89a8dc
ms.sourcegitcommit: 342588e10ae0054a6d6dc0fd11dae481006be099
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94360930"
---
# <a name="tutorial-get-started-with-no-locrazor-pages-in-aspnet-core"></a>Didacticiel : prise en main des Razor pages dans ASP.net Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"
Il s’agit du premier didacticiel d’une série qui enseigne les bases de la création d’une Razor application web ASP.net Core pages.

Pour obtenir une présentation plus avancée destinée aux développeurs qui connaissent bien les contrôleurs et les vues, consultez [Présentation des Razor pages](xref:razor-pages/index).

À la fin de la série, vous disposez d’une application qui gère une base de données de films.  

[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).

Dans ce tutoriel, vous avez :

> [!div class="checklist"]
> * CreateRazorapplication Web pages.
> * Exécutez l’application.
> * Examiner les fichiers projet.

À la fin de ce didacticiel, vous disposerez d’une Razor application Web pages de travail que vous allez améliorer dans les didacticiels ultérieurs.

![Orig ou :: No-Loc (index) ::: page](razor-pages-start/_static/5/home5.png)

## <a name="prerequisites"></a>Prérequis

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="no-loccreate-a-no-locrazor-pages-web-app"></a>Create une Razor application Web pages

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Démarrez Visual Studio et sélectionnez **Create un nouveau projet**. Pour plus d’informations, consultez [ Create un nouveau projet dans Visual Studio](/visualstudio/ide/create-new-project).

   ![::: No-Loc (créer) ::: un nouveau projet à partir de la fenêtre de démarrage](razor-pages-start/_static/5/start-window-create-new-project.png)

1. Dans la boîte de dialogue **Create nouveau projet** , sélectionnez **ASP.net Core application Web** , puis sélectionnez **suivant**.

    ![Nouvelle application web ASP.NET Core](razor-pages-start/_static/5/np.png)
    
1. Dans la boîte de dialogue **configurer votre nouveau projet** , entrez `RazorPagesMovie` pour **nom du projet**. Il est important de nommer le projet *Razor PagesMovie* , y compris la mise en majuscules, afin que les espaces de noms correspondent quand vous copiez et collez l’exemple de code.

1. Sélectionnez **Create**.

    ![Nouvelle application web ASP.NET Core](razor-pages-start/_static/config.png)

1. Dans la boîte de dialogue **Create nouvelle ASP.net Core application Web** , sélectionnez :
    1. **.Net Core** et **ASP.net Core 5,0** dans les listes déroulantes.
    1. **Application Web**.
    1. **Create**.

     ![Nouvelle application web ASP.NET Core](razor-pages-start/_static/5/npx.png)

    Le projet de démarrage suivant est créé :

    ![Explorateur de solutions](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Ouvrez le [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).

1. Accédez au répertoire (`cd`) qui contiendra le projet.

1. Exécutez les commandes suivantes :

   ```dotnetcli
   dotnet new webapp -o RazorPagesMovie
   code -r RazorPagesMovie
   ```

   * La `dotnet new` commande crée un Razor projet de pages dans le dossier *Razor PagesMovie* .
   * La `code` commande ouvre le dossier *Razor PagesMovie* dans l’instance actuelle de Visual Studio code.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

1. Sélectionnez **Fichier** > **Nouvelle solution**.

    ![macOS - Nouvelle solution](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

1. Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez application Web de l’application **.net Core**  >  **App**  >  **Web Application**  >  **suivant**. Dans la version 8,6 ou une version ultérieure, sélectionnez application Web **et**  >  **App**  >  **application Web application** console  >  **suivant**.

    ![sélection du modèle d’application Web macOS](razor-pages-start/_static/web_app_template_vsmac.png)

1. Dans la boîte de dialogue **configurer la nouvelle application Web** :

    1. Vérifiez que **l’authentification** est définie sur **aucune authentification**.
    1. Si vous avez présenté une option permettant de sélectionner un **Framework cible** , sélectionnez la version la plus récente de .net 5. x.
    1. Sélectionnez **Suivant**.

1. Nommez le projet *Razor PagesMovie* et sélectionnez **Create** .

    ![macOS nom du projet](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Exécuter l’application

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a>Examiner les fichiers projet

Voici une vue d’ensemble des principaux dossiers et fichiers projet que vous allez utiliser dans les didacticiels suivants.

### <a name="pages-folder"></a>Dossier Pages

Contient Razor des pages et des fichiers de prise en charge. Chaque Razor page est une paire de fichiers :

* Un fichier *. cshtml* contenant du balisage HTML avec du code C# à l’aide de la Razor syntaxe.
* Un fichier *. cshtml.cs* qui contient le code C# qui gère les événements de page.

Les fichiers de prise en charge ont des noms commençant par un trait de soulignement. Par exemple, le fichier *_Layout. cshtml* configure les éléments d’interface utilisateur communs à toutes les pages. Ce fichier définit le menu de navigation en haut de la page et la mention de copyright au bas de la page. Pour plus d'informations, consultez <xref:mvc/views/layout>.

### <a name="wwwroot-folder"></a>Dossier racine

Contient des ressources statiques, telles que des fichiers HTML, des fichiers JavaScript et des fichiers CSS. Pour plus d'informations, consultez <xref:fundamentals/static-files>.

### appsettings.json

Contient des données de configuration, telles que des chaînes de connexion. Pour plus d'informations, consultez <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Program.cs

Contient le point d’entrée de l’application. Pour plus d'informations, consultez <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

contient le code qui configure le comportement de l’application. Pour plus d'informations, consultez <xref:fundamentals/startup>.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="step-by-step"]
> [Suivant : ajouter un modèle](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-5.0" -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

Il s’agit du premier didacticiel d’une série qui enseigne les bases de la création d’une Razor application web ASP.net Core pages.

Pour obtenir une présentation plus avancée destinée aux développeurs qui connaissent bien les contrôleurs et les vues, consultez [Présentation des Razor pages](xref:razor-pages/index).

À la fin de la série, vous disposez d’une application qui gère une base de données de films.  

[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).

Dans ce tutoriel, vous avez :

> [!div class="checklist"]
> * CreateRazorapplication Web pages.
> * Exécutez l’application.
> * Examiner les fichiers projet.

À la fin de ce didacticiel, vous disposerez d’une Razor application Web pages de travail sur laquelle vous allez vous appuyer dans des didacticiels ultérieurs.

![Orig ou :: No-Loc (index) ::: page](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>Prérequis

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="no-loccreate-a-no-locrazor-pages-web-app"></a>Create une Razor application Web pages

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Dans Visual Studio, dans le menu **Fichier** , sélectionnez **Nouveau** > **Projet**.
* Create une nouvelle ASP.NET Core application Web, puis sélectionnez **suivant**.
  ![Nouvelle application web ASP.NET Core](razor-pages-start/_static/np_2.1.png)
* Nommez le projet **Razor PagesMovie**. Il est important de nommer le projet *Razor PagesMovie* afin que les espaces de noms correspondent quand vous copiez et collez du code.
  ![Nouvelle application web ASP.NET Core](razor-pages-start/_static/config.png)

* Sélectionnez **ASP.NET Core 3,1** dans la liste déroulante, **application Web** , puis sélectionnez **Create** .

![Nouvelle application web ASP.NET Core](razor-pages-start/_static/3/npx.png)

  Le projet de démarrage suivant est créé :

  ![Explorateur de solutions](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Ouvrez le [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Accédez au répertoire (`cd`) qui contiendra le projet.

* Exécutez les commandes suivantes :

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * La `dotnet new` commande crée un Razor projet de pages dans le dossier *Razor PagesMovie* .
  * La `code` commande ouvre le dossier *Razor PagesMovie* dans l’instance actuelle de Visual Studio code.

* Une fois que l’icône de flamme OmniSharp de la barre d’État devient verte, une boîte de dialogue demande les **ressources requises à générer et à déboguer manquantes dans « Razor PagesMovie ». Ajoutez-les ?** Sélectionnez **Oui**.

  Un répertoire *.vscode* contenant des fichiers *launch.json* et *tasks.json* est ajouté au répertoire racine du projet.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* Sélectionnez **Fichier** > **Nouvelle solution**.

  ![macOS - Nouvelle solution](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez application Web de l’application **.net Core**  >  **App**  >  **Web Application**  >  **suivant**. Dans la version 8,6 ou une version ultérieure, sélectionnez application Web **et**  >  **App**  >  **application Web application** console  >  **suivant**.

  ![sélection du modèle d’application Web macOS](razor-pages-start/_static/web_app_template_vsmac.png)

* Dans la boîte de dialogue **configurer la nouvelle application Web** :

  * Vérifiez que **l’authentification** est définie sur **aucune authentification**.
  * Si vous avez présenté une option permettant de sélectionner un **Framework cible** , sélectionnez la version la plus récente de 3. x.

  Sélectionnez **Suivant**.

* Nommez le projet **Razor PagesMovie** , puis sélectionnez **Create** .

  ![macOS nom du projet](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Exécuter l’application

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a>Examiner les fichiers projet

Voici une vue d’ensemble des principaux dossiers et fichiers projet que vous allez utiliser dans les didacticiels suivants.

### <a name="pages-folder"></a>Dossier Pages

Contient Razor des pages et des fichiers de prise en charge. Chaque Razor page est une paire de fichiers :

* Un fichier *. cshtml* contenant du balisage HTML avec du code C# à l’aide de la Razor syntaxe.
* Un fichier *. cshtml.cs* qui contient le code C# qui gère les événements de page.

Les fichiers de prise en charge ont des noms commençant par un trait de soulignement. Par exemple, le fichier *_Layout. cshtml* configure les éléments d’interface utilisateur communs à toutes les pages. Ce fichier définit le menu de navigation en haut de la page et la mention de copyright au bas de la page. Pour plus d'informations, consultez <xref:mvc/views/layout>.

### <a name="wwwroot-folder"></a>Dossier racine

Contient des fichiers statiques, tels que des fichiers HTML, des fichiers JavaScript et des fichiers CSS. Pour plus d'informations, consultez <xref:fundamentals/static-files>.

### <a name="appsettingsjson"></a>appSettings.json

Contient des données de configuration, telles que des chaînes de connexion. Pour plus d'informations, consultez <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Program.cs

Contient le point d’entrée pour le programme. Pour plus d'informations, consultez <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

contient le code qui configure le comportement de l’application. Pour plus d'informations, consultez <xref:fundamentals/startup>.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="step-by-step"]
> [Suivant : ajouter un modèle](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

Ceci est le premier didacticiel d’une série. [La série](xref:tutorials/razor-pages/index) enseigne les bases de la création d’une Razor application Web ASP.net Core pages.

Pour obtenir une présentation plus avancée destinée aux développeurs qui connaissent bien les contrôleurs et les vues, consultez [Présentation des Razor pages](xref:razor-pages/index).

À la fin de la série, vous disposez d’une application qui gère une base de données de films.  

[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).

Dans ce tutoriel, vous avez :

> [!div class="checklist"]
> * CreateRazorapplication Web pages.
> * Exécutez l’application.
> * Examiner les fichiers projet.

À la fin de ce didacticiel, vous disposerez d’une Razor application Web pages de travail sur laquelle vous allez vous appuyer dans des didacticiels ultérieurs.

![Orig ou :: No-Loc (index) ::: page](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>Prérequis

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="no-loccreate-a-no-locrazor-pages-web-app"></a>Create une Razor application Web pages

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Dans Visual Studio, dans le menu **Fichier** , sélectionnez **Nouveau** > **Projet**.

* Create une nouvelle ASP.NET Core application Web, puis sélectionnez **suivant**.

  ![Nouvelle application web ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* Nommez le projet **Razor PagesMovie**. Il est important de nommer le projet *Razor PagesMovie* afin que les espaces de noms correspondent quand vous copiez et collez du code.

  ![Nouvelle application web ASP.NET Core](razor-pages-start/_static/config.png)

* Sélectionnez **ASP.NET Core 2,2** dans la liste déroulante, **application Web** , puis sélectionnez **Create** .

![Nouvelle application web ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  Le projet de démarrage suivant est créé :

  ![Explorateur de solutions](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Ouvrez le [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Accédez au répertoire (`cd`) qui contiendra le projet.

* Exécutez les commandes suivantes :

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * La `dotnet new` commande crée un Razor projet de pages dans le dossier *Razor PagesMovie* .
  * La `code` commande ouvre le dossier *Razor PagesMovie* dans l’instance actuelle de Visual Studio code.

* Une fois que l’icône de flamme OmniSharp de la barre d’État devient verte, une boîte de dialogue demande les **ressources requises à générer et à déboguer manquantes dans « Razor PagesMovie ». Ajoutez-les ?** Sélectionnez **Oui**.

  Un répertoire *.vscode* contenant des fichiers *launch.json* et *tasks.json* est ajouté au répertoire racine du projet.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* Sélectionnez **Fichier** > **Nouvelle solution**.

![macOS - Nouvelle solution](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez application Web de l’application **.net Core**  >  **App**  >  **Web Application**  >  **suivant**. Dans la version 8,6 ou une version ultérieure, sélectionnez application Web **et**  >  **App**  >  **application Web application** console  >  **suivant**.

* Dans la boîte de dialogue **configurer la nouvelle application Web** :

  * Vérifiez que **l’authentification** est définie sur **aucune authentification**.
  * Si vous avez présenté une option permettant de sélectionner un **Framework cible** , sélectionnez la version la plus récente de 2. x.

  Sélectionnez **Suivant**.

* Nommez le projet **Razor PagesMovie** , puis sélectionnez **Create** .

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Exécuter l’application

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Appuyez sur Ctrl+F5 pour exécuter sans le débogueur.

  Le lancement de l’application avec <kbd>CTRL + F5</kbd> (mode non débogage) vous permet d’apporter des modifications au code, d’enregistrer le fichier, d’actualiser le navigateur et de voir les modifications du code. Beaucoup de développeurs préfèrent utiliser ce mode pour lancer rapidement l’application et voir les modifications.


  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio démarre [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) et exécute l’application. La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`. La raison en est que `localhost` est le nom d’hôte standard de l’ordinateur local. Localhost traite uniquement les requêtes web de l’ordinateur local. Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.

* Sur la page d'accueil de l’application, sélectionnez **Accepter** pour accepter le suivi.

  Cette application ne suit pas les informations personnelles, mais le modèle de projet inclut la fonctionnalité de consentement en cas de besoin pour vous conformer au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr) de l’Union européenne.

  ![Orig ou :: No-Loc (index) ::: page](razor-pages-start/_static/homeGDPR2.2.png)

  L’illustration suivante montre l’application après avoir donné son consentement au suivi :

  ![Orig ou :: No-Loc (index) ::: page](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* Appuyez sur <kbd>CTRL + F5</kbd> pour exécuter sans le débogueur.

  Le lancement de l’application avec <kbd>CTRL + F5</kbd> (mode non débogage) vous permet d’apporter des modifications au code, d’enregistrer le fichier, d’actualiser le navigateur et de voir les modifications du code. Beaucoup de développeurs préfèrent utiliser ce mode pour lancer rapidement l’application et voir les modifications.

  Visual Studio Code démarre [Kestrel](xref:fundamentals/servers/kestrel), lance un navigateur et accède à `http://localhost:5001` . La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`. La raison en est que `localhost` est le nom d’hôte standard de l’ordinateur local. Localhost traite uniquement les requêtes web de l’ordinateur local.

* Sur la page d'accueil de l’application, sélectionnez **Accepter** pour accepter le suivi.

  Cette application ne suit pas les informations personnelles, mais le modèle de projet inclut la fonctionnalité de consentement en cas de besoin pour vous conformer au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr) de l’Union européenne.

  ![Orig ou :: No-Loc (index) ::: page](razor-pages-start/_static/homeGDPR2.2.png)

  L’illustration suivante montre l’application après avoir consenti au suivi :

  ![Orig ou :: No-Loc (index) ::: page](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* Appuyez sur **Cmd+Opt+F5** pour lancer l’exécution sans le débogueur.

  Le lancement de l’application avec <kbd>cmd + opt + F5</kbd> (mode non débogage) vous permet d’apporter des modifications au code, d’enregistrer le fichier, d’actualiser le navigateur et de voir les modifications du code. Beaucoup de développeurs préfèrent utiliser ce mode pour lancer rapidement l’application et voir les modifications.

  Visual Studio démarre [Kestrel](xref:fundamentals/servers/kestrel), lance un navigateur et accède à `http://localhost:5001` .

* Sur la page d'accueil de l’application, sélectionnez **Accepter** pour accepter le suivi.

  Cette application ne suit pas les informations personnelles, mais le modèle de projet inclut la fonctionnalité de consentement en cas de besoin pour vous conformer au [Règlement général sur la protection des données (RGPD)](xref:security/gdpr) de l’Union européenne.

  ![Orig ou :: No-Loc (index) ::: page](razor-pages-start/_static/homeGDPR2.2_safari.png)

  L’illustration suivante montre l’application après avoir donné son consentement au suivi :

  ![Orig ou :: No-Loc (index) ::: page](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a>Examiner les fichiers projet

Voici une vue d’ensemble des principaux dossiers et fichiers projet que vous allez utiliser dans les didacticiels suivants.

### <a name="pages-folder"></a>Dossier Pages

Contient Razor des pages et des fichiers de prise en charge. Chaque Razor page est une paire de fichiers :

* Un fichier *. cshtml* contenant du balisage HTML avec du code C# à l’aide de la Razor syntaxe.
* Un fichier *. cshtml.cs* qui contient le code C# qui gère les événements de page.

Les fichiers de prise en charge ont des noms commençant par un trait de soulignement. Par exemple, le fichier *_Layout. cshtml* configure les éléments d’interface utilisateur communs à toutes les pages. Ce fichier définit le menu de navigation en haut de la page et la mention de copyright au bas de la page. Pour plus d'informations, consultez <xref:mvc/views/layout>.

Razor Les pages sont dérivées de `PageModel` . Par Convention, la `PageModel` classe dérivée de est nommée `<PageName>Model` .

### <a name="wwwroot-folder"></a>Dossier racine

Contient des fichiers statiques, tels que des fichiers HTML, des fichiers JavaScript et des fichiers CSS. Pour plus d'informations, consultez <xref:fundamentals/static-files>.

### <a name="appsettingsjson"></a>appSettings.json

Contient des données de configuration, telles que des chaînes de connexion. Pour plus d'informations, consultez <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Program.cs

Contient le point d’entrée pour le programme. Pour plus d'informations, consultez <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

Contient le code qui configure le comportement de l’application, par exemple s’il requiert le consentement pour les cookie s. Pour plus d'informations, consultez <xref:fundamentals/startup>.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Version YouTube de ce tutoriel](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="step-by-step"]
> [Suivant : ajouter un modèle](xref:tutorials/razor-pages/model)

::: moniker-end
