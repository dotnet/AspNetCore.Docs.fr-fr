---
title: Bien démarrer avec ASP.NET Core MVC
author: rick-anderson
description: Découvrez comment bien démarrer avec ASP.NET Core MVC.
ms.author: riande
ms.date: 01/20/2021
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
ms.custom: contperf-fy21q3
ms.openlocfilehash: aaf930eee351ed757be60f648bce88b182d52799
ms.sourcegitcommit: da5a5bed5718a9f8db59356ef8890b4b60ced6e9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98710767"
---
# <a name="get-started-with-aspnet-core-mvc"></a>Bien démarrer avec ASP.NET Core MVC

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

Il s’agit du premier didacticiel d’une série qui enseigne ASP.NET Core développement Web MVC avec des contrôleurs et des vues.

À la fin de la série, vous disposerez d’une application qui gère et affiche des données de film. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créez une application web.
> * Ajouter et structurer un modèle.
> * Utiliser une base de données.
> * Ajouter une fonctionnalité de recherche et de validation.

[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).

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

* Démarrez Visual Studio et sélectionnez **Créer un projet**.
* Dans la boîte de dialogue **créer un nouveau projet** , sélectionnez **ASP.net Core application Web** > **suivant**.
* Dans la boîte de dialogue **configurer votre nouveau projet** , entrez `MvcMovie` pour **nom du projet**. Il est important de nommer le projet *MvcMovie*. La mise en majuscules doit correspondre à chaque `namespace` correspondance lorsque le code est copié.
* Sélectionnez **Create** (Créer).
* Dans la boîte de dialogue **créer une application web ASP.net Core** , sélectionnez :
  * **.Net Core** et **ASP.net Core 5,0** dans les listes déroulantes.
  * **ASP.net Core application Web (Model-View-Controller)**.
  * **Créer**.

![Créer une application Web de ASP.NET Core ](start-mvc/_static/mvcVS19v16.9.png)

Pour obtenir d’autres approches de création du projet, consultez [créer un projet dans Visual Studio](/visualstudio/ide/create-new-project).

Visual Studio a utilisé le modèle de projet par défaut pour le projet MVC créé. Le projet créé :

* Est une application active.
* Est un projet de démarrage de base.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Ce didacticiel part du principe que vous êtes familiarisé avec VS Code. Pour plus d’informations, consultez [prise en main de vs code](https://code.visualstudio.com/docs) et [Visual Studio code de l’aide](#visual-studio-code-help).

* Ouvrez le [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Accédez au répertoire ( `cd` ) qui contiendra le projet.
* Exécutez la commande suivante :

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * Si une boîte de dialogue s’affiche avec les **ressources requises pour la génération et le débogage, il manque la valeur « MvcMovie ». Ajoutez-les ?**, sélectionnez **Oui**

  * `dotnet new mvc -o MvcMovie`: Crée un nouveau ASP.NET Core projet MVC dans le dossier *MvcMovie* .
  * `code -r MvcMovie`: Charge le fichier projet *MvcMovie. csproj* dans Visual Studio code.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* Sélectionnez **Fichier** > **Nouvelle solution**.

  ![macOS - Nouvelle solution](start-mvc/_static/new_project_vsmac.png)

* Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez application Web de l’application **.net Core**  >    >  **(Model-View-Controller)**  >  **suivant**. Dans la version 8,6 ou une version ultérieure, sélectionnez application Web **et**  >    >  **application console (Model-View-Controller)**  >  **suivant**.

  ![sélection du modèle d’application Web macOS](start-mvc/_static/web_app_template_vsmac.png)

* Dans la boîte de dialogue **configurer votre nouvelle application Web** :

  * Vérifiez que **l’authentification** est définie sur **aucune authentification**.
  * Si une option permettant de sélectionner une version **cible du .NET Framework** est présentée, sélectionnez la version la plus récente de 5. x.
  * Sélectionnez **Suivant**.

* Nommez le projet **MvcMovie**, puis sélectionnez **Créer**.

  ![macOS nom du projet](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a>Exécuter l’application

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Appuyez sur CTRL + F5 pour exécuter l’application sans le débogueur.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio :

  * Démarre [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).
  * Exécute l’application.

  La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`. Le nom d’hôte standard de votre ordinateur local est `localhost` . Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.

Le lancement de l’application sans débogage en sélectionnant CTRL + F5 vous permet d’effectuer les opérations suivantes :

* Modifiez le code.
* Enregistrez le fichier.
* Actualisez rapidement le navigateur et consultez les modifications de code.

Vous pouvez lancer l’application en mode débogage ou non-débogage à partir de l’élément de menu **Déboguer** :

![Menu Déboguer](start-mvc/_static/debug_menu50.png)

Vous pouvez déboguer l’application en sélectionnant le bouton **IIS Express**

![IIS Express](start-mvc/_static/iis_express50.png)

L’image suivante montre l’application :

![Page d’accueil ou d’index](start-mvc/_static/home50-vs.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Appuyez sur CTRL + F5 pour exécuter sans le débogueur.

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code :

  * Démarre [Kestrel](xref:fundamentals/servers/kestrel)
  * Lance un navigateur.
  * Accède à `https://localhost:5001` .

  La barre d’adresses affiche `localhost:port:5001` au lieu de quelque chose qui ressemble à `example.com`. Le nom d’hôte standard de votre ordinateur local est `localhost` . Localhost traite uniquement les requêtes web de l’ordinateur local.

Le lancement de l’application sans débogage en sélectionnant CTRL + F5 vous permet d’effectuer les opérations suivantes :

* Modifiez le code.
* Enregistrez le fichier.
* Actualisez rapidement le navigateur et consultez les modifications de code.

  ![Page d’accueil ou d’index](start-mvc/_static/home50-port5001.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* Sélectionnez **Exécuter** > **Exécuter sans débogage** pour lancer l’application.

  Visual Studio pour Mac:

  * Démarre le serveur [Kestrel](xref:fundamentals/servers/index#kestrel) .
  * Lance un navigateur.
  * Accède à `http://localhost:port` , où *port* est un numéro de port choisi au hasard.

  [!INCLUDE[](~/includes/trustCertMac.md)]

  La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`. Le nom d’hôte standard de votre ordinateur local est `localhost` . Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.

Vous pouvez lancer l’application en mode débogage ou non-débogage à partir du menu **Exécuter**.

L’image suivante montre l’application :

![Page d’accueil ou d’index](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

Dans la prochaine partie de ce didacticiel, vous allez découvrir MVC et commencer à écrire du code.

> [!div class="step-by-step"]
> [Étape suivante : ajouter un contrôleur](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

Il s’agit du premier didacticiel d’une série qui enseigne ASP.NET Core développement Web MVC avec des contrôleurs et des vues.

À la fin de la série, vous disposerez d’une application qui gère et affiche des données de film. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créez une application web.
> * Ajouter et structurer un modèle.
> * Utiliser une base de données.
> * Ajouter une fonctionnalité de recherche et de validation.

[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).

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

* Dans Visual Studio, sélectionnez **créer un nouveau projet**.

* Sélectionnez **ASP.net Core application Web** > **suivant**.

  ![Créer un projet d’application Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* Nommez le projet **MvcMovie**, puis sélectionnez **Créer**. Il est important de nommer le projet **MvcMovie** pour que l’espace de noms corresponde quand vous copiez du code.

  ![Configurer votre nouveau projet](start-mvc/_static/config.png)

* Sélectionnez **application Web (Model-View-Controller)**. Dans les zones de liste déroulante, sélectionnez **.net Core** et **ASP.net Core 3,1**, puis sélectionnez **créer**.

  ![Boîte de dialogue Nouveau projet, .NET Core dans le volet gauche, web ASP.NET Core ](start-mvc/_static/new_project30.png)

Visual Studio a utilisé le modèle de projet par défaut pour le projet MVC créé. Le projet créé :

* Est une application active.
* Est un projet de démarrage de base.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Ce didacticiel part du principe que vous êtes familiarisé avec VS Code. Pour plus d’informations, consultez [prise en main de vs code](https://code.visualstudio.com/docs) et [Visual Studio code de l’aide](#visual-studio-code-help).

* Ouvrez le [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Remplacez répertoires ( `cd` ) par un dossier qui contiendra le projet.
* Exécutez la commande suivante :

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * Une boîte de dialogue s’affiche avec les **ressources requises pour la génération et le débogage dans’MvcMovie'. Ajoutez-les ?**, sélectionnez **Oui**.

  * `dotnet new mvc -o MvcMovie`: Crée un nouveau ASP.NET Core projet MVC dans le dossier *MvcMovie* .
  * `code -r MvcMovie`: Charge le fichier projet *MvcMovie. csproj* dans Visual Studio code.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* Sélectionnez **Fichier** > **Nouvelle solution**.

  ![macOS - Nouvelle solution](start-mvc/_static/new_project_vsmac.png)

* Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez application Web de l’application **.net Core**  >    >  **(Model-View-Controller)**  >  **suivant**. Dans la version 8,6 ou une version ultérieure, sélectionnez application Web **et**  >    >  **application console (Model-View-Controller)**  >  **suivant**.

  ![sélection du modèle d’application Web macOS](start-mvc/_static/web_app_template_vsmac.png)

* Dans la boîte de dialogue **configurer votre nouvelle application Web** :

  * Vérifiez que **l’authentification** est définie sur **aucune authentification**.
  * Si une option permettant de sélectionner un **Framework cible** s’affiche, sélectionnez la dernière version de 3. x.
  * Sélectionnez **Suivant**.

* Nommez le projet **MvcMovie**, puis sélectionnez **Créer**.

  ![macOS nom du projet](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a>Exécuter l’application

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Appuyez sur CTRL + F5 pour exécuter l’application sans débogage.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio :

  * Démarre [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).
  * Exécute l’application.

  La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`. Le nom d’hôte standard de votre ordinateur local est `localhost` . Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web.

Le lancement de l’application sans débogage en sélectionnant CTRL + F5 vous permet d’effectuer les opérations suivantes :

* Modifiez le code.
* Enregistrez le fichier.
* Actualisez rapidement le navigateur et consultez les modifications de code.

Vous pouvez lancer l’application en mode débogage ou non-débogage à partir de l’élément de menu **Déboguer** :

![Menu Déboguer](start-mvc/_static/debug_menu.png)

Vous pouvez déboguer l’application en sélectionnant le bouton **IIS Express**

![IIS Express](start-mvc/_static/iis_express.png)

L’image suivante montre l’application :

![Page d’accueil ou d’index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Appuyez sur CTRL + F5 pour exécuter l’application sans débogage.

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code :

  * Démarre [Kestrel](xref:fundamentals/servers/kestrel)
  * Lance un navigateur.
  * Accède à `https://localhost:5001` .

  La barre d’adresses affiche `localhost:port:5001` au lieu de quelque chose qui ressemble à `example.com`. Le nom d’hôte standard de votre ordinateur local est `localhost` . Localhost traite uniquement les requêtes web de l’ordinateur local.

Le lancement de l’application sans débogage en sélectionnant CTRL + F5 vous permet d’effectuer les opérations suivantes :

* Modifiez le code.
* Enregistrez le fichier.
* Actualisez rapidement le navigateur et consultez les modifications de code.

  ![Page d’accueil ou d’index](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* Sélectionnez **Exécuter** > **Exécuter sans débogage** pour lancer l’application.

  Visual Studio pour Mac : démarre [Kestrel](xref:fundamentals/servers/index#kestrel) Server, lance un navigateur et accède à `http://localhost:port` , où *port* est un numéro de port choisi au hasard.

[!INCLUDE[](~/includes/trustCertMac.md)]

La barre d’adresses affiche `localhost:port#` au lieu de quelque chose qui ressemble à `example.com`. Le nom d’hôte standard de votre ordinateur local est `localhost` . Quand Visual Studio crée un projet web, un port aléatoire est utilisé pour le serveur web. Quand vous exécutez l’application, vous voyez un autre numéro de port.

Vous pouvez lancer l’application en mode débogage ou non-débogage à partir du menu **Exécuter**.

L’image suivante montre l’application :

![Page d’accueil ou d’index](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

Dans la prochaine partie de ce didacticiel, vous allez découvrir MVC et commencer à écrire du code.

> [!div class="step-by-step"]
> [Next](adding-controller.md)

::: moniker-end
