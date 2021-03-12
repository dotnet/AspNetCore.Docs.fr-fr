---
title: Créer un serveur et un client gRPC .NET Core dans ASP.NET Core
author: juntaoluo
description: Ce tutoriel montre comment créer un service gRPC et un client gRPC sur ASP.NET Core. Découvrez comment créer un projet de service gRPC, modifier un fichier proto et ajouter un appel duplex de streaming.
ms.author: johluo
ms.date: 10/23/2020
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
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: fed25eeacf57504810d41fcf002dcaa9927a21af
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588599"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a>Didacticiel : créer un client et un serveur gRPC dans ASP.NET Core

Par [John Luo](https://github.com/juntaoluo)

Ce tutoriel montre comment créer un client [gRPC](https://grpc.io/docs/guides/) .NET Core et un serveur gRPC ASP.NET Core.

À la fin, vous disposerez d’un client gRPC qui communique avec le service Greeter gRPC.

[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/grpc/grpc-start/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Créer un serveur gRPC.
> * Créez un client gRPC.
> * Tester le service du client gRPC avec le service Greeter gRPC.

## <a name="prerequisites"></a>Prérequis

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* [Visual Studio pour Mac version 8,7 ou ultérieure](/visualstudio/releasenotes/vs2019-mac-relnotes)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]
---

## <a name="create-a-grpc-service"></a>Créer un service gRPC

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Démarrez Visual Studio et sélectionnez **Créer un projet**. Vous pouvez également, dans le menu **Fichier** de Visual Studio, sélectionner **Nouveau** > **Projet**.
* Dans la boîte de dialogue **créer un nouveau projet** , sélectionnez **gRPC service** , puis cliquez sur **suivant**:

  ![Boîte de dialogue créer un nouveau projet dans Visual Studio](~/tutorials/grpc/grpc-start/static/cnp.png)

* Nommez le projet **GrpcGreeter**. Il est important de nommer le projet *GrpcGreeter* afin que les espaces de noms correspondent quand vous copiez et collez du code.
* Sélectionnez **Create** (Créer).
* Dans la boîte de dialogue **Créer un service gRPC** :
  * Le modèle **Service gRPC** est sélectionné.
  * Sélectionnez **Create** (Créer).

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Ouvrez le [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Remplacez répertoires ( `cd` ) par un dossier pour le projet.
* Exécutez les commandes suivantes :

  ```dotnetcli
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * La commande `dotnet new` crée un nouveau service gRPC dans le dossier *GrpcGreeter*.
  * La commande `code` ouvre le dossier *GrpcGreeter* dans une nouvelle instance de Visual Studio Code.

  Une boîte de dialogue s’affiche avec les **ressources requises pour la génération et le débogage dans’GrpcGreeter'. Ajoutez-les ?**
* Sélectionnez **Oui**.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* Démarrez Visual Studio pour Mac et sélectionnez **créer un nouveau projet**. Vous pouvez également, dans le menu **Fichier** de Visual Studio, sélectionner **Nouveau** > **Projet**.
* Dans la boîte de dialogue **créer un nouveau projet** , sélectionnez **Web et**  >  **application** console  >  **gRPC service** , puis sélectionnez **suivant**:

  ![Boîte de dialogue créer un nouveau projet sur macOS](~/tutorials/grpc/grpc-start/static/cnp-mac.png)

* Sélectionnez **.net Core 3,1** pour la version cible de .NET Framework, puis sélectionnez **suivant**.
* Nommez le projet **GrpcGreeter**. Il est important de nommer le projet *GrpcGreeter* afin que les espaces de noms correspondent quand vous copiez et collez du code.
* Sélectionnez **Create** (Créer).
---

### <a name="run-the-service"></a>Exécuter le service

  [!INCLUDE[](~/includes/run-the-app.md)]

Les journaux indiquent que le service écoute sur `https://localhost:5001`.

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> Le modèle gRPC est configuré pour utiliser le protocole [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246). Les clients gRPC doivent utiliser le protocole HTTPS pour appeler le serveur.
>
> MacOS ne prend pas en charge ASP.NET Core gRPC avec TLS. Une configuration supplémentaire est nécessaire pour exécuter correctement les services gRPC sur MacOS. Pour plus d’informations, consultez [Impossible de démarrer l’application ASP.NET Core gRPC sur MacOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).

### <a name="examine-the-project-files"></a>Examiner les fichiers projet

Fichiers projet *GrpcGreeter* :

* *Greeter. proto*: le fichier *protos/Greeter. proto* définit le `Greeter` gRPC et est utilisé pour générer les ressources du serveur gRPC. Pour plus d’informations, consultez [Introduction à gRPC](xref:grpc/index).
* Dossier *services* : contient l’implémentation du `Greeter` service.
* *appSettings.jssur*: contient des données de configuration, telles que le protocole utilisé par Kestrel. Pour plus d’informations, consultez <xref:fundamentals/configuration/index>.
* *Program.cs*: contient le point d’entrée pour le service gRPC. Pour plus d’informations, consultez <xref:fundamentals/host/generic-host>.
* *Startup.cs*: contient le code qui configure le comportement de l’application. Pour plus d’informations, consultez [Démarrage des applications](xref:fundamentals/startup).

## <a name="create-the-grpc-client-in-a-net-console-app"></a>Créer le client gRPC dans une application console .NET

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Ouvrez une deuxième instance de Visual Studio et sélectionnez **Créer un projet**.
* Dans la boîte de dialogue **Créer un projet**, sélectionnez **Application console (.NET Core)**, puis sélectionnez **Suivant**.
* Dans la zone de texte **nom du projet** , entrez **GrpcGreeterClient** , puis sélectionnez **créer**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Ouvrez le [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Remplacez répertoires ( `cd` ) par un dossier pour le projet.
* Exécutez les commandes suivantes :

  ```dotnetcli
  dotnet new console -o GrpcGreeterClient
  code -r GrpcGreeterClient
  ```

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

Suivez les instructions de [Création d’une solution .NET Core complète sur macOS à l’aide de Visual Studio pour Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) pour créer une application console appelée *GrpcGreeterClient*.

---

### <a name="add-required-packages"></a>Ajouter les packages nécessaires

Le projet client gRPC requiert les packages suivants :

* [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), qui contient le client .NET Core.
* [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), qui contient des API de messages protobuf pour C#.
* [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), qui contient la prise en charge des outils C# pour les fichiers protobuf. Le package d’outils n’est pas nécessaire lors de l’exécution. La dépendance est donc marquée avec `PrivateAssets="All"`.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Installez les packages à l’aide de la console PMC (console du Gestionnaire de package) ou à partir de Gérer les packages NuGet.

#### <a name="pmc-option-to-install-packages"></a>Option de la console du Gestionnaire de package pour installer des packages

* Dans Visual Studio, sélectionnez **Outils**  >  **Gestionnaire de package NuGet**  >  **console du gestionnaire** de package
* Dans la fenêtre **Gestionnaire de package**, exécutez `cd GrpcGreeterClient` pour accéder au dossier contenant les fichiers *GrpcGreeterClient.csproj*.
* Exécutez les commandes suivantes :

  ```powershell
  Install-Package Grpc.Net.Client
  Install-Package Google.Protobuf
  Install-Package Grpc.Tools
  ```

#### <a name="manage-nuget-packages-option-to-install-packages"></a>Option Gérer les packages NuGet pour installer les packages

* Cliquez avec le bouton droit sur le projet dans **Explorateur de solutions**  >  **gérer les packages NuGet**.
* Sélectionnez l’onglet **Parcourir**.
* Entrez **Grpc.Net.Client** dans la zone de recherche.
* Sélectionnez le package **Grpc.Net.Client** sous l’onglet **Parcourir** et sélectionnez **Installer**.
* Répétez ces étapes pour `Google.Protobuf` et `Grpc.Tools`.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Exécutez les commandes suivantes à partir du **Terminal intégré** :

```dotnetcli
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* Cliquez avec le bouton droit sur le projet **GrpcGreeterClient** dans le **panneau solutions** , puis sélectionnez **gérer les packages NuGet**.
* Entrez **Grpc.Net.Client** dans la zone de recherche.
* Sélectionnez le package **.net. client GRPC** dans le volet des résultats, puis sélectionnez **Ajouter un package**.
* Sélectionnez le bouton **accepter** dans la boîte de dialogue **accepter la licence** .
* Répétez ces étapes pour `Google.Protobuf` et `Grpc.Tools`.

---

### <a name="add-greetproto"></a>Ajouter greet.proto

* Créez un dossier *Protos* dans le projet du client gRPC.
* Copiez le fichier *Protos\greet.proto* du service Greeter gRPC vers le projet du client gRPC.
* Mettez à jour l’espace de noms à l’intérieur du `greet.proto` fichier vers l’espace de noms du projet :

  ```
  option csharp_namespace = "GrpcGreeterClient";
  ```

* Modifiez le fichier projet *GrpcGreeterClient.csproj* :

  # <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

  Cliquez avec le bouton droit sur le projet et sélectionnez **Modifier le fichier de projet**.

  # <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  Sélectionnez le fichier *GrpcGreeterClient.csproj*.

  # <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

  Cliquez avec le bouton droit sur le projet et sélectionnez **Modifier le fichier de projet**.

  ---

* Ajoutez un groupe d’éléments avec un élément `<Protobuf>` qui fait référence au fichier *greet.proto* :

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a>Créer le client Greeter

Générez le projet client pour créer les types dans l' `GrpcGreeter` espace de noms. Les types `GrpcGreeter` sont générés automatiquement par le processus de génération.

Mettez à jour le fichier *Program.cs* du client gRPC par le code suivant :

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

*Program.cs* contient le point d’entrée et la logique du client gRPC.

Le client Greeter est créé en :

* Instanciant un `GrpcChannel` contenant les informations de création de la connexion au service gRPC.
* Utilisant le `GrpcChannel` pour construire le client Greeter :

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-5)]

Le client Greeter appelle la méthode `SayHello` asynchrone. Le résultat de l’appel `SayHello` s’affiche :

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=6-8)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a>Tester le client gRPC avec le service Greeter gRPC

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Dans le service Greeter, appuyez sur `Ctrl+F5` pour démarrer le serveur sans le débogueur.
* Dans le projet `GrpcGreeterClient`, appuyez sur `Ctrl+F5` pour démarrer le client sans le débogueur.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Démarrez le service Greeter.
* Démarrez le client.


# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* En raison du [problème http/2 TLS mentionné précédemment sur la solution de contournement MacOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos), vous devez mettre à jour l’adresse du canal dans le client en « http://localhost:5000 ». Mettez à jour la ligne 13 de **GrpcGreeterClient/Program. cs** pour lire :
  ```csharp
  using var channel = GrpcChannel.ForAddress("http://localhost:5000");
  ``` 
* Démarrez le service Greeter.
* Démarrez le client.

---

Le client envoie un message d’accueil au service avec un message contenant son nom, *GreeterClient*. Le service envoie le message « Hello GreeterClient » comme réponse. La réponse « Hello GreeterClient » s’affiche dans l’invite de commandes :

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

Le service gRPC enregistre les détails de l’appel réussi dans les journaux écrits dans l’invite de commandes :

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\GH\aspnet\docs\4\Docs\aspnetcore\tutorials\grpc\grpc-start\sample\GrpcGreeter
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 78.32260000000001ms 200 application/grpc
```

> [!NOTE]
> Le code de cet article requiert le certificat de développement ASP.NET Core HTTPS pour sécuriser le service gRPC. Si le client .NET gRPC échoue avec le message `The remote certificate is invalid according to the validation procedure.` ou `The SSL connection could not be established.` , le certificat de développement n’est pas approuvé. Pour résoudre ce problème, consultez [appeler un service gRPC avec un certificat non approuvé/non valide](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate).

[!INCLUDE[](~/includes/gRPCazure.md)]

### <a name="next-steps"></a>Étapes suivantes

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
