---
title: 'Didacticiel : créer une API Web avec ASP.NET Core'
author: rick-anderson
description: Apprendre à créer une API web avec ASP.NET Core.
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 08/13/2020
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
- Models
uid: tutorials/first-web-api
ms.openlocfilehash: ccbfc27eb89e23938a69f0ab4cb306d6a4136889
ms.sourcegitcommit: fe2e3174c34bee1e425c6e52dd8f663fe52b8756
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96175050"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a>Didacticiel : créer une API Web avec ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5)et [Mike Wasson](https://github.com/mikewasson)

Ce tutoriel décrit les principes fondamentaux liés à la génération d’une API web avec ASP.NET Core.

::: moniker range=">= aspnetcore-5.0"

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer un projet d’API web.
> * Ajouter une classe de modèle et un contexte de base de données.
> * Générer automatiquement des modèles pour un contrôleur avec des méthodes CRUD.
> * Configurer le routage, les chemins d’URL et les valeurs de retour.
> * Appeler l’API web avec Postman

À la fin, vous disposez d’une API web qui peut gérer des tâches stockées dans une base de données.

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel crée l’API suivante :

|API | Description | Corps de la demande | Response body |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | Obtenir toutes les tâches | None | Tableau de tâches|
|`GET /api/TodoItems/{id}` | Obtenir un élément par ID | None | Tâche|
|`POST /api/TodoItems` | Ajouter un nouvel élément | Tâche | Tâche |
|`PUT /api/TodoItems/{id}` | Mettre à jour un élément existant &nbsp; | Tâche | None |
|`DELETE /api/TodoItems/{id}` &nbsp; &nbsp; | Supprimer un élément &nbsp;&nbsp; | None | None|

Le diagramme suivant illustre la conception de l’application.

![Le client est représenté par une zone située à gauche. Il envoie une demande et reçoit une réponse de l’application, représentée par une zone dessinée à droite. Dans la zone de l’application, trois zones représentent le contrôleur, le modèle et la couche d’accès aux données. La requête provient du contrôleur de l’application, et les opérations de lecture/écriture se produisent entre le contrôleur et la couche d’accès aux données. Le modèle est sérialisé et retourné au client dans la réponse.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a>Prérequis

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-project"></a>Créer un projet web

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Dans le menu **fichier** , sélectionnez **nouveau** > **projet**.
* Sélectionnez le modèle **Application web ASP.NET Core** et cliquez sur **Suivant**.
* Nommez le projet *TodoApi* et cliquez sur **Créer**.
* Dans la boîte de dialogue **créer une application Web ASP.net Core** , vérifiez que **.net Core** et **ASP.net Core 5,0** sont sélectionnés. Sélectionnez le modèle **API** et cliquez sur **Créer**.

![Boîte de dialogue de nouveau projet dans VS](first-web-api/_static/5/vs.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Ouvrez le [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Définissez les répertoires (`cd`) sur le dossier destiné à contenir le dossier du projet.
* Exécutez les commandes suivantes :

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* Quand une boîte de dialogue vous demande si vous souhaitez ajouter les composants nécessaires au projet, sélectionnez **Oui**.

  Les commandes précédentes :

  * Créent un projet API Web et l’ouvrent dans Visual Studio Code.
  * Ajoutent les packages NuGet qui sont requis dans la section suivante.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* Sélectionnez **Fichier** > **Nouvelle solution**.

  ![macOS - Nouvelle solution](first-web-api-mac/_static/sln.png)

* Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez API de l’application **.net Core**  >  **App**  >  **API**  >  **suivant**. Dans la version 8,6 ou une version ultérieure, sélectionnez application **Web et**  >  **App**  >  **API** application console  >  **suivant**.

  ![sélection du modèle d’API macOS](first-web-api-mac/_static/api_template.png)

* Dans la boîte de dialogue **configurer la nouvelle API Web ASP.net Core** , sélectionnez la version la plus récente de .net Core 5. x **Target Framework**. Sélectionnez **Suivant**.

* Entrez *TodoApi* comme **Nom du projet**, puis sélectionnez **Créer**.

  ![boîte de dialogue de configuration](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

Ouvrez un terminal de commande dans le dossier de projet, puis exécutez les commandes suivantes :

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-project"></a>Tester le projet

Le modèle de projet crée une `WeatherForecast` API avec prise en charge de [Swagger](xref:tutorials/web-api-help-pages-using-swagger).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Appuyez sur Ctrl+F5 pour exécuter sans le débogueur.

[!INCLUDE[](~/includes/trustCertVS.md)]

  Lancements de Visual Studio :

* Serveur Web IIS Express.
* Le navigateur par défaut et navigue vers `https://localhost:<port>/swagger/index.html` , où `<port>` est un numéro de port choisi de façon aléatoire.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/trustCertVSC.md)]

Appuyez sur Ctrl+F5 pour exécuter l’application. Dans un navigateur, accédez à l’URL suivante : [https://localhost:5001/swagger](https://localhost:5001/swagger)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

Sélectionnez **exécuter**  >  **Démarrer le débogage** pour lancer l’application. Visual Studio pour Mac lance un navigateur et accède à `https://localhost:<port>`, où `<port>` est un numéro de port choisi de manière aléatoire. Une erreur HTTP 404 (introuvable) est retournée. Ajoutez `/swagger` à l’URL (définissez-la sur `https://localhost:<port>/swagger`).

---

La page Swagger `/swagger/index.html` s’affiche. Sélectionnez **faire**  >  **essayer** l'  >  **exécution**. La page affiche :

* Commande de [boucle](https://curl.haxx.se/) pour tester l’API WeatherForecast.
* URL pour tester l’API WeatherForecast.
* Code, corps et en-têtes de la réponse.
* Zone de liste déroulante avec les types de média et l’exemple de valeur et de schéma.

<!-- Review: Do we care the IE generates several errors. It shows the data, but with  Unrecognized response type; displaying content as text.
-->
Swagger est utilisé pour générer une documentation utile et des pages d’aide pour les API Web. Ce didacticiel se concentre sur la création d’une API Web. Pour plus d’informations sur Swagger, consultez <xref:tutorials/web-api-help-pages-using-swagger> .

Copiez et collez l’URL de la **requête** dans le navigateur :  `https://localhost:<port>/WeatherForecast`

Un code JSON similaire au suivant est retourné :

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

### <a name="update-the-launchurl"></a>Mettre à jour launchUrl

Dans *Properties\launchSettings.js*, mettez à jour `launchUrl` de `"swagger"` vers `"api/TodoItems"` :

```json
"launchUrl": "api/TodoItems",
```

Comme Swagger a été supprimé, le balisage précédent modifie l’URL qui est lancée vers la méthode d’extraction du contrôleur ajoutée dans les sections suivantes.

## <a name="add-a-model-class"></a>Ajouter une classe de modèle

Un *modèle* est un ensemble de classes qui représentent les données gérées par l’application. Le modèle pour cette application est une classe `TodoItem` unique.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet. Sélectionnez **Ajouter**  >  **un nouveau dossier**. Nommez le dossier *Models* .

* Cliquez avec le bouton droit sur le *Models* dossier et sélectionnez **Ajouter** une  >  **classe**. Nommez la classe *TodoItem* et sélectionnez sur **Ajouter**.

* Remplacez le code du modèle par ce qui suit :

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Ajoutez un dossier nommé *Models* .

* Ajoutez une `TodoItem` classe au dossier à l' *Models* aide du code suivant :

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* Cliquez avec le bouton droit sur le projet. Sélectionnez **Ajouter**  >  **un nouveau dossier**. Nommez le dossier *Models* .

  ![nouveau dossier](first-web-api-mac/_static/folder.png)

* Cliquez avec le bouton droit sur le *Models* dossier, puis sélectionnez **Ajouter** > **un nouveau fichier** > **General** > **classe générale vide**.

* Nommez la classe *TodoItem* et cliquez sur **Nouveau**.

* Remplacez le code du modèle par ce qui suit :

---

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoItem.cs?name=snippet)]

La propriété `Id` fonctionne comme la clé unique dans une base de données relationnelle.

Les classes de modèle peuvent se trouver n’importe où dans le projet, mais le *Models* dossier est utilisé par Convention.

## <a name="add-a-database-context"></a>Ajouter un contexte de base de données

Le *contexte de base de données* est la classe principale qui coordonne les fonctionnalités d’Entity Framework pour un modèle de données. Cette classe est créée en dérivant de la classe <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="add-nuget-packages"></a>Ajouter des packages NuGet

* Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet > Gérer les packages NuGet pour la solution**.
* Sélectionnez l’onglet **Parcourir**, puis entrez **Microsoft.EntityFrameworkCore.SqlServer** dans la zone de recherche.
<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Delete this line at RTM -->
* Dans le volet gauche, sélectionnez **Microsoft. EntityFrameworkCore. SqlServer** .
* Cochez la case **Projet** dans le volet droit, puis sélectionnez **Installer**.
* Utilisez les instructions précédentes pour ajouter le package NuGet **Microsoft. EntityFrameworkCore. InMemory** .

<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Update this image at RTM -->
![Gestionnaire de package NuGet](first-web-api/_static/5/vsNuGet.png)

## <a name="add-the-todocontext-database-context"></a>Ajouter le contexte de base de données TodoContext

* Cliquez avec le bouton droit sur le *Models* dossier et sélectionnez **Ajouter** une  >  **classe**. Nommez la classe *TodoContext* et cliquez sur **Ajouter**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

* Ajoutez une `TodoContext` classe au *Models* dossier.

---

* Entrez le code suivant :

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a>Inscrire le contexte de base de données

Dans ASP.NET Core, les services tels que le contexte de base de données doivent être inscrits auprès du [conteneur d’injection de dépendances](xref:fundamentals/dependency-injection). Le conteneur fournit le service aux contrôleurs.

Mettez à jour *Startup.cs* avec le code suivant :

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

Le code précédent :

* Supprime les appels Swagger.
* Supprime les déclarations `using` inutilisées.
* Ajoute le contexte de base de données au conteneur d’injection de dépendances.
* Spécifie que le contexte de base de données utilise une base de données en mémoire.

## <a name="scaffold-a-controller"></a>Générer automatiquement des modèles pour un contrôleur

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Cliquez avec le bouton droit sur le dossier *Contrôleurs*.
* Sélectionnez **Ajouter** > **Nouvel élément généré automatiquement**.
* Sélectionnez **Contrôleur d’API avec actions, utilisant Entity Framework**, puis **Ajouter**.
* Dans la boîte de dialogue **Contrôleur d’API avec actions, utilisant Entity Framework** :

  * Sélectionnez **TodoItem (TodoApi. Models )** dans la **classe de modèle**.
  * Sélectionnez **TodoContext (TodoApi. Models )** dans la **classe de contexte de données**.
  * Sélectionnez **Ajouter**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

Exécutez les commandes suivantes :

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet tool update -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

Les commandes précédentes :

* Ajoutent les packages NuGet nécessaires à la génération de modèles automatique.
* Installent le moteur de génération de modèles automatique (`dotnet-aspnet-codegenerator`).
* Génèrent automatiquement des modèles pour `TodoItemsController`.

---

Le code généré :

* Marque la classe avec l' [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribut. Cet attribut indique que le contrôleur répond aux requêtes de l’API web. Pour plus d’informations sur les comportements spécifiques que permet l’attribut, consultez <xref:web-api/index>.
* Utilise l’injection de dépendances pour injecter le contexte de base de données (`TodoContext`) dans le contrôleur. Le contexte de base de données est utilisé dans chacune des méthodes la [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) du contrôleur.

Les modèles de ASP.NET Core pour :

* Les contrôleurs avec des vues sont inclus `[action]` dans le modèle de routage.
* Les contrôleurs d’API n’incluent pas `[action]` dans le modèle de routage.

Lorsque le `[action]` jeton n’est pas dans le modèle de routage, le nom de l' [action](xref:mvc/controllers/routing#action) est exclu de l’itinéraire. Autrement dit, le nom de la méthode associée à l’action n’est pas utilisé dans l’itinéraire correspondant.

## <a name="update-the-posttodoitem-create-method"></a>Mettre à jour la méthode de création de PostTodoItem

Remplacez l’instruction return dans `PostTodoItem` pour utiliser l’opérateur [nameof](/dotnet/csharp/language-reference/operators/nameof) :

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

Le code précédent est une méthode HTTP Après, comme indiqué par l' [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribut. La méthode obtient la valeur de la tâche dans le corps de la requête HTTP.

Pour plus d’informations, consultez [Routage par attributs avec des attributs Http[Verbe]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

La méthode <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> :

* Retourne un [code d’état HTTP 201](https://developer.mozilla.org/docs/Web/HTTP/Status/201) en cas de réussite. HTTP 201 est la réponse standard d’une méthode HTTP POST qui crée une ressource sur le serveur.
* Ajoute un en-tête [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) à la réponse. L’en-tête `Location` spécifie l’[URI](https://developer.mozilla.org/docs/Glossary/URI) de l’élément d’action qui vient d’être créé. Pour plus d’informations, consultez la section [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).
* Fait référence à l’action `GetTodoItem` pour créer l’URI `Location` de l’en-tête. Le mot clé `nameof` C# est utilisé pour éviter de coder en dur le nom de l’action dans l’appel `CreatedAtAction`.

### <a name="install-postman"></a>Installer Postman

Ce tutoriel utilise Postman pour tester l’API web.

* Installer le [poste](https://www.getpostman.com/downloads/)
* Démarrez l’application web.
* Démarrez Postman.
* Désactivez la **vérification du certificat SSL**.
  * À partir de **Fichier** > **Paramètres** (onglet **Général**), désactivez **Vérification du certificat SSL**.
    > [!WARNING]
    > Réactivez la vérification du certificat SSL après avoir testé le contrôleur.

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a>Tester PostTodoItem avec Postman

* Créez une requête.
* Affectez `POST` à la méthode HTTP.
* Définissez l’URI sur `https://localhost:<port>/api/TodoItems` . Par exemple : `https://localhost:5001/api/TodoItems`.
* Sélectionnez l’onglet **Corps** .
* Sélectionnez la case d’option **raw** (données brutes).
* Définissez le type sur **JSON (application/json)**.
* Dans le corps de la demande, entrez la syntaxe JSON d’une tâche :

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* Sélectionnez **Envoyer**.

  ![Postman avec requête de création](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a>Tester l’URI de l’en-tête d’emplacement

L’URI d’en-tête d’emplacement peut être testé dans le navigateur. Copiez et collez l’URI d’en-tête d’emplacement dans le navigateur.

Pour effectuer un test dans le poster :

* Sélectionnez l’onglet **Headers** (En-têtes) dans le volet **Response** (Réponse).
* Copiez la valeur d’en-tête **Location** (Emplacement) :

  ![Onglet Headers de la console Postman](first-web-api/_static/3/create.png)

* Affectez `GET` à la méthode HTTP.
* Définissez l’URI sur `https://localhost:<port>/api/TodoItems/1` . Par exemple : `https://localhost:5001/api/TodoItems/1`.
* Sélectionnez **Envoyer**.

## <a name="examine-the-get-methods"></a>Examiner les méthodes GET

Deux points de terminaison d’extraction sont implémentés :

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

Testez l’application en appelant les deux points de terminaison à partir d’un navigateur ou de Postman. Exemple :

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

Une réponse semblable à la suivante est produite par l’appel à `GetTodoItems` :

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a>Tester Get avec Postman

* Créez une requête.
* Définissez la méthode HTTP sur **GET**.
* Définissez l’URI de la demande sur `https://localhost:<port>/api/TodoItems` . Par exemple : `https://localhost:5001/api/TodoItems`.
* Définissez l’**affichage à deux volets** dans Postman.
* Sélectionnez **Envoyer**.

Cette application utilise une base de données en mémoire. Si l’application est arrêtée et démarrée, la requête GET précédente ne retourne aucune donnée. Si aucune donnée n’est retournée, publiez ([POST](#post)) les données dans l’application.

## <a name="routing-and-url-paths"></a>Routage et chemins d’URL

L' [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribut désigne une méthode qui répond à une requête http. Le chemin d’URL pour chaque méthode est construit comme suit :

* Partez de la chaîne de modèle dans l’attribut `Route` du contrôleur :

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* Remplacez `[controller]` par le nom du contrôleur qui, par convention, est le nom de la classe du contrôleur sans le suffixe « Controller ». Pour cet exemple, le nom de la classe du contrôleur étant **TodoItems** Controller, le nom du contrôleur est « TodoItems ». Le [routage](xref:mvc/controllers/routing) d’ASP.NET Core ne respecte pas la casse.
* Si l’attribut `[HttpGet]` a un modèle de route (par exemple, `[HttpGet("products")]`), ajoutez-le au chemin. Cet exemple n’utilise pas de modèle. Pour plus d’informations, consultez [Routage par attributs avec des attributs Http[Verbe]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

Dans la méthode `GetTodoItem` suivante, `"{id}"` est une variable d’espace réservé pour l’identificateur unique de la tâche. Lorsque `GetTodoItem` est appelé, la valeur de `"{id}"` dans l’URL est fournie à la méthode dans son `id` paramètre.

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a>Valeurs retournées

Le type de retour des `GetTodoItems` `GetTodoItem` méthodes et est [ActionResult \<T> type](xref:web-api/action-return-types#actionresultt-type). ASP.NET Core sérialise automatiquement l’objet en [JSON](https://www.json.org/) et écrit le JSON dans le corps du message de réponse. Le code de réponse pour ce type de retour est [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), en supposant qu’il n’existe aucune exception non gérée. Les exceptions non gérées sont converties en erreurs 5xx.

Les types de retour `ActionResult` peuvent représenter une large plage de codes d’état HTTP. Par exemple, `GetTodoItem` peut retourner deux valeurs d’état différentes :

* Si aucun élément ne correspond à l’ID demandé, la méthode retourne un code d’erreur d' [état 404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> .
* Sinon, la méthode retourne 200 avec un corps de réponse JSON. Le retour de `item` entraîne une réponse HTTP 200.

## <a name="the-puttodoitem-method"></a>Méthode PutTodoItem

Examinez la méthode `PutTodoItem` :

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

`PutTodoItem` est similaire à `PostTodoItem`, à la différence près qu’il utilise HTTP PUT. La réponse est [204 (aucun contenu)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html). D’après la spécification HTTP, une requête PUT nécessite que le client envoie toute l’entité mise à jour, et pas seulement les changements. Pour prendre en charge les mises à jour partielles, utilisez [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).

Si vous obtenez une erreur en appelant `PutTodoItem`, appelez `GET` pour vérifier que la base de données contient un élément.

### <a name="test-the-puttodoitem-method"></a>Tester la méthode PutTodoItem

Cet exemple utilise une base de données en mémoire qui doit être initialisée chaque fois que l’application est démarrée. La base de données doit contenir un élément avant que vous ne passiez un appel PUT. Appelez obtenir pour vous assurer qu’il y a un élément dans la base de données avant d’effectuer un appel PUT.

Mettez à jour l’élément de tâche dont l’ID est égal à 1 et affectez-lui comme nom `"feed fish"` :

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

L’image suivante montre la mise à jour Postman :

![Console Postman montrant la réponse 204 (Pas de contenu)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a>Méthode DeleteTodoItem

Examinez la méthode `DeleteTodoItem` :

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a>Tester la méthode DeleteTodoItem

Utilisez Postman pour supprimer une tâche :

* Définissez la méthode sur `DELETE`.
* Définissez l’URI de l’objet à supprimer (par exemple `https://localhost:5001/api/TodoItems/1` ).
* Sélectionnez **Envoyer**.

<a name="over-post-v5"></a>

## <a name="prevent-over-posting"></a>Empêcher la post-validation

Actuellement, l’exemple d’application expose l' `TodoItem` objet entier. En général, les applications de production limitent les données entrées et retournées à l’aide d’un sous-ensemble du modèle. Il existe plusieurs raisons à cela, et la sécurité est essentielle. Le sous-ensemble d’un modèle est généralement appelé un objet Transfert de données (DTO), un modèle d’entrée ou un modèle de vue. Le **DTO** est utilisé dans cet article.

Un DTO peut être utilisé pour :

* Empêcher la survalidation.
* Masquer les propriétés que les clients ne sont pas censés afficher.
* Omettez certaines propriétés afin de réduire la taille de la charge utile.
* Aplatir les graphiques d’objets qui contiennent des objets imbriqués. Les graphiques d’objets aplatis peuvent être plus pratiques pour les clients.

Pour illustrer l’approche DTO, mettez à jour la `TodoItem` classe pour inclure un champ secret :

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=8)]

Le champ secret doit être masqué à partir de cette application, mais une application administrative peut choisir de l’exposer.

Vérifiez que vous pouvez poster et recevoir le champ secret.

Créer un modèle DTO :

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

Mettez à jour le `TodoItemsController` pour utiliser `TodoItemDTO` :

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

Vérifiez que vous ne pouvez pas poster ou recevoir le champ secret.

## <a name="call-the-web-api-with-javascript"></a>Appelez l’API web avec JavaScript

Consultez [Didacticiel : appeler une API web ASP.net core avec JavaScript](xref:tutorials/web-api-javascript).

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer un projet d’API web.
> * Ajouter une classe de modèle et un contexte de base de données.
> * Générer automatiquement des modèles pour un contrôleur avec des méthodes CRUD.
> * Configurer le routage, les chemins d’URL et les valeurs de retour.
> * Appeler l’API web avec Postman

À la fin, vous disposez d’une API web qui peut gérer des tâches stockées dans une base de données.

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel crée l’API suivante :

|API | Description | Corps de la demande | Response body |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | Obtenir toutes les tâches | None | Tableau de tâches|
|`GET /api/TodoItems/{id}` | Obtenir un élément par ID | None | Tâche|
|`POST /api/TodoItems` | Ajouter un nouvel élément | Tâche | Tâche |
|`PUT /api/TodoItems/{id}` | Mettre à jour un élément existant &nbsp; | Tâche | None |
|`DELETE /api/TodoItems/{id}` &nbsp; &nbsp; | Supprimer un élément &nbsp;&nbsp; | None | None|

Le diagramme suivant illustre la conception de l’application.

![Le client est représenté par une zone située à gauche. Il envoie une demande et reçoit une réponse de l’application, représentée par une zone dessinée à droite. Dans la zone de l’application, trois zones représentent le contrôleur, le modèle et la couche d’accès aux données. La requête provient du contrôleur de l’application, et les opérations de lecture/écriture se produisent entre le contrôleur et la couche d’accès aux données. Le modèle est sérialisé et retourné au client dans la réponse.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a>Prérequis

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a>Créer un projet web

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Dans le menu **fichier** , sélectionnez **nouveau** > **projet**.
* Sélectionnez le modèle **Application web ASP.NET Core** et cliquez sur **Suivant**.
* Nommez le projet *TodoApi* et cliquez sur **Créer**.
* Dans la boîte de dialogue **créer une application Web ASP.net Core** , vérifiez que **.net Core** et **ASP.net Core 3,1** sont sélectionnés. Sélectionnez le modèle **API** et cliquez sur **Créer**.

![Boîte de dialogue de nouveau projet dans VS](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Ouvrez le [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Définissez les répertoires (`cd`) sur le dossier destiné à contenir le dossier du projet.
* Exécutez les commandes suivantes :

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* Quand une boîte de dialogue vous demande si vous souhaitez ajouter les composants nécessaires au projet, sélectionnez **Oui**.

  Les commandes précédentes :

  * Créent un projet API Web et l’ouvrent dans Visual Studio Code.
  * Ajoutent les packages NuGet qui sont requis dans la section suivante.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* Sélectionnez **Fichier** > **Nouvelle solution**.

  ![macOS - Nouvelle solution](first-web-api-mac/_static/sln.png)

* Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez API de l’application **.net Core**  >  **App**  >  **API**  >  **suivant**. Dans la version 8,6 ou une version ultérieure, sélectionnez application **Web et**  >  **App**  >  **API** application console  >  **suivant**.

  ![sélection du modèle d’API macOS](first-web-api-mac/_static/api_template.png)

* Dans la boîte de dialogue **configurer la nouvelle API Web ASP.net Core** , sélectionnez la version la plus récente de .net Core 3. x **Target Framework**. Sélectionnez **Suivant**.

* Entrez *TodoApi* comme **Nom du projet**, puis sélectionnez **Créer**.

  ![boîte de dialogue de configuration](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

Ouvrez un terminal de commande dans le dossier de projet, puis exécutez les commandes suivantes :

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a>Tester l’API

Le modèle de projet crée une API `WeatherForecast`. Appelez la méthode `Get` à partir d’un navigateur pour tester l’application.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Appuyez sur Ctrl+F5 pour exécuter l’application. Visual Studio lance un navigateur et accède à `https://localhost:<port>/WeatherForecast`, où `<port>` est un numéro de port choisi de manière aléatoire.

Si une boîte de dialogue apparaît vous demandant si vous devez approuver le certificat IIS Express, sélectionnez **Oui**. Dans la boîte de dialogue **Avertissement de sécurité** qui s’affiche ensuite, sélectionnez **Oui**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Appuyez sur Ctrl+F5 pour exécuter l’application. Dans un navigateur, accédez à l’URL suivante : `https://localhost:5001/WeatherForecast`.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

Sélectionnez **exécuter**  >  **Démarrer le débogage** pour lancer l’application. Visual Studio pour Mac lance un navigateur et accède à `https://localhost:<port>`, où `<port>` est un numéro de port choisi de manière aléatoire. Une erreur HTTP 404 (introuvable) est retournée. Ajoutez `/WeatherForecast` à l’URL (définissez-la sur `https://localhost:<port>/WeatherForecast`).

---

Un code JSON similaire au suivant est retourné :

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

## <a name="add-a-model-class"></a>Ajouter une classe de modèle

Un *modèle* est un ensemble de classes qui représentent les données gérées par l’application. Le modèle pour cette application est une classe `TodoItem` unique.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet. Sélectionnez **Ajouter**  >  **un nouveau dossier**. Nommez le dossier *Models* .

* Cliquez avec le bouton droit sur le *Models* dossier et sélectionnez **Ajouter** une  >  **classe**. Nommez la classe *TodoItem* et sélectionnez sur **Ajouter**.

* Remplacez le code du modèle par le code suivant :

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Ajoutez un dossier nommé *Models* .

* Ajoutez une `TodoItem` classe au dossier à l' *Models* aide du code suivant :

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* Cliquez avec le bouton droit sur le projet. Sélectionnez **Ajouter**  >  **un nouveau dossier**. Nommez le dossier *Models* .

  ![nouveau dossier](first-web-api-mac/_static/folder.png)

* Cliquez avec le bouton droit sur le *Models* dossier, puis sélectionnez **Ajouter** > **un nouveau fichier** > **General** > **classe générale vide**.

* Nommez la classe *TodoItem* et cliquez sur **Nouveau**.

* Remplacez le code du modèle par le code suivant :

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

La propriété `Id` fonctionne comme la clé unique dans une base de données relationnelle.

Les classes de modèle peuvent se trouver n’importe où dans le projet, mais le *Models* dossier est utilisé par Convention.

## <a name="add-a-database-context"></a>Ajouter un contexte de base de données

Le *contexte de base de données* est la classe principale qui coordonne les fonctionnalités d’Entity Framework pour un modèle de données. Cette classe est créée en dérivant de la classe `Microsoft.EntityFrameworkCore.DbContext`.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="add-nuget-packages"></a>Ajouter des packages NuGet

* Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet > Gérer les packages NuGet pour la solution**.
* Sélectionnez l’onglet **Parcourir**, puis entrez **Microsoft.EntityFrameworkCore.SqlServer** dans la zone de recherche.
* Dans le volet gauche, sélectionnez **Microsoft. EntityFrameworkCore. SqlServer** .
* Cochez la case **Projet** dans le volet droit, puis sélectionnez **Installer**.
* Utilisez les instructions précédentes pour ajouter le package NuGet **Microsoft. EntityFrameworkCore. InMemory** .

![Gestionnaire de package NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a>Ajouter le contexte de base de données TodoContext

* Cliquez avec le bouton droit sur le *Models* dossier et sélectionnez **Ajouter** une  >  **classe**. Nommez la classe *TodoContext* et cliquez sur **Ajouter**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

* Ajoutez une `TodoContext` classe au *Models* dossier.

---

* Entrez le code suivant :

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a>Inscrire le contexte de base de données

Dans ASP.NET Core, les services tels que le contexte de base de données doivent être inscrits auprès du [conteneur d’injection de dépendances](xref:fundamentals/dependency-injection). Le conteneur fournit le service aux contrôleurs.

Mettez à jour *Startup.cs* avec le code en surbrillance suivant :

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

Le code précédent :

* Supprime les déclarations `using` inutilisées.
* Ajoute le contexte de base de données au conteneur d’injection de dépendances.
* Spécifie que le contexte de base de données utilise une base de données en mémoire.

## <a name="scaffold-a-controller"></a>Générer automatiquement des modèles pour un contrôleur

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Cliquez avec le bouton droit sur le dossier *Contrôleurs*.
* Sélectionnez **Ajouter** > **Nouvel élément généré automatiquement**.
* Sélectionnez **Contrôleur d’API avec actions, utilisant Entity Framework**, puis **Ajouter**.
* Dans la boîte de dialogue **Contrôleur d’API avec actions, utilisant Entity Framework** :

  * Sélectionnez **TodoItem (TodoApi. Models )** dans la **classe de modèle**.
  * Sélectionnez **TodoContext (TodoApi. Models )** dans la **classe de contexte de données**.
  * Sélectionnez **Ajouter**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

Exécutez les commandes suivantes :

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

Les commandes précédentes :

* Ajoutent les packages NuGet nécessaires à la génération de modèles automatique.
* Installent le moteur de génération de modèles automatique (`dotnet-aspnet-codegenerator`).
* Génèrent automatiquement des modèles pour `TodoItemsController`.

---

Le code généré :

* Marque la classe avec l' [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribut. Cet attribut indique que le contrôleur répond aux requêtes de l’API web. Pour plus d’informations sur les comportements spécifiques que permet l’attribut, consultez <xref:web-api/index>.
* Utilise l’injection de dépendances pour injecter le contexte de base de données (`TodoContext`) dans le contrôleur. Le contexte de base de données est utilisé dans chacune des méthodes la [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) du contrôleur.

Les modèles de ASP.NET Core pour :

* Les contrôleurs avec des vues sont inclus `[action]` dans le modèle de routage.
* Les contrôleurs d’API n’incluent pas `[action]` dans le modèle de routage.

Lorsque le `[action]` jeton n’est pas dans le modèle de routage, le nom de l' [action](xref:mvc/controllers/routing#action) est exclu de l’itinéraire. Autrement dit, le nom de la méthode associée à l’action n’est pas utilisé dans l’itinéraire correspondant.

## <a name="examine-the-posttodoitem-create-method"></a>Examiner la méthode de création de PostTodoItem

Remplacez l’instruction return dans `PostTodoItem` pour utiliser l’opérateur [nameof](/dotnet/csharp/language-reference/operators/nameof) :

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

Le code précédent est une méthode HTTP Après, comme indiqué par l' [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribut. La méthode obtient la valeur de la tâche dans le corps de la requête HTTP.

Pour plus d’informations, consultez [Routage par attributs avec des attributs Http[Verbe]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

La méthode <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> :

* Retourne un code d’état HTTP 201 en cas de réussite. HTTP 201 est la réponse standard d’une méthode HTTP POST qui crée une ressource sur le serveur.
* Ajoute un en-tête [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) à la réponse. L’en-tête `Location` spécifie l’[URI](https://developer.mozilla.org/docs/Glossary/URI) de l’élément d’action qui vient d’être créé. Pour plus d’informations, consultez la section [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).
* Fait référence à l’action `GetTodoItem` pour créer l’URI `Location` de l’en-tête. Le mot clé `nameof` C# est utilisé pour éviter de coder en dur le nom de l’action dans l’appel `CreatedAtAction`.

### <a name="install-postman"></a>Installer Postman

Ce tutoriel utilise Postman pour tester l’API web.

* Installer le [poste](https://www.getpostman.com/downloads/)
* Démarrez l’application web.
* Démarrez Postman.
* Désactivez la **vérification du certificat SSL**.
  * À partir de **Fichier** > **Paramètres** (onglet **Général**), désactivez **Vérification du certificat SSL**.
    > [!WARNING]
    > Réactivez la vérification du certificat SSL après avoir testé le contrôleur.

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a>Tester PostTodoItem avec Postman

* Créez une requête.
* Affectez `POST` à la méthode HTTP.
* Définissez l’URI sur `https://localhost:<port>/api/TodoItems` . Par exemple : `https://localhost:5001/api/TodoItems`.
* Sélectionnez l’onglet **Corps** .
* Sélectionnez la case d’option **raw** (données brutes).
* Définissez le type sur **JSON (application/json)**.
* Dans le corps de la demande, entrez la syntaxe JSON d’une tâche :

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* Sélectionnez **Envoyer**.

  ![Postman avec requête de création](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a>Tester l’URI d’en-tête d’emplacement avec le poster

* Sélectionnez l’onglet **Headers** (En-têtes) dans le volet **Response** (Réponse).
* Copiez la valeur d’en-tête **Location** (Emplacement) :

  ![Onglet Headers de la console Postman](first-web-api/_static/3/create.png)

* Affectez `GET` à la méthode HTTP.
* Définissez l’URI sur `https://localhost:<port>/api/TodoItems/1` . Par exemple : `https://localhost:5001/api/TodoItems/1`.
* Sélectionnez **Envoyer**.

## <a name="examine-the-get-methods"></a>Examiner les méthodes GET

Ces méthodes implémentent deux points de terminaison GET :

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

Testez l’application en appelant les deux points de terminaison à partir d’un navigateur ou de Postman. Exemple :

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

Une réponse semblable à la suivante est produite par l’appel à `GetTodoItems` :

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a>Tester Get avec Postman

* Créez une requête.
* Définissez la méthode HTTP sur **GET**.
* Définissez l’URI de la demande sur `https://localhost:<port>/api/TodoItems` . Par exemple : `https://localhost:5001/api/TodoItems`.
* Définissez l’**affichage à deux volets** dans Postman.
* Sélectionnez **Envoyer**.

Cette application utilise une base de données en mémoire. Si l’application est arrêtée et démarrée, la requête GET précédente ne retourne aucune donnée. Si aucune donnée n’est retournée, publiez ([POST](#post)) les données dans l’application.

## <a name="routing-and-url-paths"></a>Routage et chemins d’URL

L' [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribut désigne une méthode qui répond à une requête http. Le chemin d’URL pour chaque méthode est construit comme suit :

* Partez de la chaîne de modèle dans l’attribut `Route` du contrôleur :

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* Remplacez `[controller]` par le nom du contrôleur qui, par convention, est le nom de la classe du contrôleur sans le suffixe « Controller ». Pour cet exemple, le nom de la classe du contrôleur étant **TodoItems** Controller, le nom du contrôleur est « TodoItems ». Le [routage](xref:mvc/controllers/routing) d’ASP.NET Core ne respecte pas la casse.
* Si l’attribut `[HttpGet]` a un modèle de route (par exemple, `[HttpGet("products")]`), ajoutez-le au chemin. Cet exemple n’utilise pas de modèle. Pour plus d’informations, consultez [Routage par attributs avec des attributs Http[Verbe]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

Dans la méthode `GetTodoItem` suivante, `"{id}"` est une variable d’espace réservé pour l’identificateur unique de la tâche. Lorsque `GetTodoItem` est appelé, la valeur de `"{id}"` dans l’URL est fournie à la méthode dans son `id` paramètre.

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a>Valeurs retournées 

Le type de retour des `GetTodoItems` `GetTodoItem` méthodes et est [ActionResult \<T> type](xref:web-api/action-return-types#actionresultt-type). ASP.NET Core sérialise automatiquement l’objet en [JSON](https://www.json.org/) et écrit le JSON dans le corps du message de réponse. Le code de réponse pour ce type de retour est 200, en supposant qu’il n’existe pas d’exception non gérée. Les exceptions non gérées sont converties en erreurs 5xx.

Les types de retour `ActionResult` peuvent représenter une large plage de codes d’état HTTP. Par exemple, `GetTodoItem` peut retourner deux valeurs d’état différentes :

* Si aucun élément ne correspond à l’ID demandé, la méthode retourne un <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> code d’erreur 404.
* Sinon, la méthode retourne 200 avec un corps de réponse JSON. Le retour de `item` entraîne une réponse HTTP 200.

## <a name="the-puttodoitem-method"></a>Méthode PutTodoItem

Examinez la méthode `PutTodoItem` :

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

`PutTodoItem` est similaire à `PostTodoItem`, à la différence près qu’il utilise HTTP PUT. La réponse est [204 (aucun contenu)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html). D’après la spécification HTTP, une requête PUT nécessite que le client envoie toute l’entité mise à jour, et pas seulement les changements. Pour prendre en charge les mises à jour partielles, utilisez [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).

Si vous obtenez une erreur en appelant `PutTodoItem`, appelez `GET` pour vérifier que la base de données contient un élément.

### <a name="test-the-puttodoitem-method"></a>Tester la méthode PutTodoItem

Cet exemple utilise une base de données en mémoire qui doit être initialisée chaque fois que l’application est démarrée. La base de données doit contenir un élément avant que vous ne passiez un appel PUT. Appelez obtenir pour vous assurer qu’il y a un élément dans la base de données avant d’effectuer un appel PUT.

Mettez à jour l’élément de tâche dont l’ID est égal à 1 et affectez-lui le nom « flux de poisson » :

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

L’image suivante montre la mise à jour Postman :

![Console Postman montrant la réponse 204 (Pas de contenu)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a>Méthode DeleteTodoItem

Examinez la méthode `DeleteTodoItem` :

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a>Tester la méthode DeleteTodoItem

Utilisez Postman pour supprimer une tâche :

* Définissez la méthode sur `DELETE`.
* Définissez l’URI de l’objet à supprimer (par exemple `https://localhost:5001/api/TodoItems/1` ).
* Sélectionnez **Envoyer**.

<a name="over-post"></a>
<a name="over-post-v3"></a>

## <a name="prevent-over-posting"></a>Empêcher la post-validation

Actuellement, l’exemple d’application expose l' `TodoItem` objet entier. En général, les applications de production limitent les données entrées et retournées à l’aide d’un sous-ensemble du modèle. Il existe plusieurs raisons à cela, et la sécurité est essentielle. Le sous-ensemble d’un modèle est généralement appelé un objet Transfert de données (DTO), un modèle d’entrée ou un modèle de vue. Le **DTO** est utilisé dans cet article.

Un DTO peut être utilisé pour :

* Empêcher la survalidation.
* Masquer les propriétés que les clients ne sont pas censés afficher.
* Omettez certaines propriétés afin de réduire la taille de la charge utile.
* Aplatir les graphiques d’objets qui contiennent des objets imbriqués. Les graphiques d’objets aplatis peuvent être plus pratiques pour les clients.

Pour illustrer l’approche DTO, mettez à jour la `TodoItem` classe pour inclure un champ secret :

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

Le champ secret doit être masqué à partir de cette application, mais une application administrative peut choisir de l’exposer.

Vérifiez que vous pouvez poster et recevoir le champ secret.

Créer un modèle DTO :

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

Mettez à jour le `TodoItemsController` pour utiliser `TodoItemDTO` :

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

Vérifiez que vous ne pouvez pas poster ou recevoir le champ secret.

## <a name="call-the-web-api-with-javascript"></a>Appelez l’API web avec JavaScript

Consultez [Didacticiel : appeler une API web ASP.net core avec JavaScript](xref:tutorials/web-api-javascript).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer un projet d’API web.
> * Ajouter une classe de modèle et un contexte de base de données.
> * Ajouter un contrôleur
> * Ajouter les méthodes CRUD
> * Configurer le routage et les chemins d’URL
> * Spécifier des valeurs de retour
> * Appeler l’API web avec Postman
> * Appeler l’API web avec JavaScript.

À la fin, vous disposez d’une API web qui peut gérer des tâches stockées dans une base de données relationnelle.

## <a name="overview-21"></a>Vue d’ensemble 2,1

Ce didacticiel crée l’API suivante :

|API | Description | Corps de la demande | Response body |
|--- | ---- | ---- | ---- |
|GET /api/TodoItems | Obtenir toutes les tâches | None | Tableau de tâches|
|GET /api/TodoItems/{id} | Obtenir un élément par ID | None | Tâche|
|POST /api/TodoItems | Ajouter un nouvel élément | Tâche | Tâche |
|PUT /api/TodoItems/{id} | Mettre à jour un élément existant &nbsp; | Tâche | None |
|SUPPRIMER/api/TodoItems/{id} &nbsp;&nbsp; | Supprimer un élément &nbsp;&nbsp; | None | None|

Le diagramme suivant illustre la conception de l’application.

![Le client est représenté par une zone située à gauche. Il envoie une demande et reçoit une réponse de l’application, représentée par une zone dessinée à droite. Dans la zone de l’application, trois zones représentent le contrôleur, le modèle et la couche d’accès aux données. La requête provient du contrôleur de l’application, et les opérations de lecture/écriture se produisent entre le contrôleur et la couche d’accès aux données. Le modèle est sérialisé et retourné au client dans la réponse.](first-web-api/_static/architecture.png)

## <a name="prerequisites-21"></a>Conditions préalables 2,1

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project-21"></a>Créer un projet Web 2,1

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Dans le menu **fichier** , sélectionnez **nouveau** > **projet**.
* Sélectionnez le modèle **Application web ASP.NET Core** et cliquez sur **Suivant**.
* Nommez le projet *TodoApi* et cliquez sur **Créer**.
* Dans la boîte de dialogue **Créer une application web ASP.NET Core**, vérifiez que **.NET Core** et **ASP.NET Core 2.2** sont sélectionnés. Sélectionnez le modèle **API** et cliquez sur **Créer**. Ne sélectionnez **pas****Activer la prise en charge de Docker**.

![Boîte de dialogue de nouveau projet dans VS](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Ouvrez le [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Définissez les répertoires (`cd`) sur le dossier destiné à contenir le dossier du projet.
* Exécutez les commandes suivantes :

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  Ces commandes créent un projet d’API web et ouvrent une nouvelle instance de Visual Studio Code dans le nouveau dossier de projet.

* Quand une boîte de dialogue vous demande si vous souhaitez ajouter les composants nécessaires au projet, sélectionnez **Oui**.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* Sélectionnez **Fichier** > **Nouvelle solution**.

  ![macOS - Nouvelle solution](first-web-api-mac/_static/sln.png)

* Dans Visual Studio pour Mac antérieure à la version 8,6, sélectionnez API de l’application **.net Core**  >  **App**  >  **API**  >  **suivant**. Dans la version 8,6 ou une version ultérieure, sélectionnez application **Web et**  >  **App**  >  **API** application console  >  **suivant**.
  
* Dans la boîte de dialogue **configurer la nouvelle API Web ASP.net Core** , sélectionnez la version la plus récente de .net Core 2. x **Target Framework**. Sélectionnez **Suivant**.

* Entrez *TodoApi* comme **Nom du projet**, puis sélectionnez **Créer**.

  ![boîte de dialogue de configuration](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api-21"></a>Tester l’API 2,1

Le modèle de projet crée une API `values`. Appelez la méthode `Get` à partir d’un navigateur pour tester l’application.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Appuyez sur Ctrl+F5 pour exécuter l’application. Visual Studio lance un navigateur et accède à `https://localhost:<port>/api/values`, où `<port>` est un numéro de port choisi de manière aléatoire.

Si une boîte de dialogue apparaît vous demandant si vous devez approuver le certificat IIS Express, sélectionnez **Oui**. Dans la boîte de dialogue **Avertissement de sécurité** qui s’affiche ensuite, sélectionnez **Oui**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Appuyez sur Ctrl+F5 pour exécuter l’application. Dans un navigateur, accédez à l’URL suivante : `https://localhost:5001/api/values`.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

Sélectionnez **exécuter**  >  **Démarrer le débogage** pour lancer l’application. Visual Studio pour Mac lance un navigateur et accède à `https://localhost:<port>`, où `<port>` est un numéro de port choisi de manière aléatoire. Une erreur HTTP 404 (introuvable) est retournée. Ajoutez `/api/values` à l’URL (définissez-la sur `https://localhost:<port>/api/values`).

---

Le code JSON suivant est retourné :

```json
["value1","value2"]
```

## <a name="add-a-model-class-21"></a>Ajouter une classe de modèle 2,1

Un *modèle* est un ensemble de classes qui représentent les données gérées par l’application. Le modèle pour cette application est une classe `TodoItem` unique.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet. Sélectionnez **Ajouter**  >  **un nouveau dossier**. Nommez le dossier *Models* .

* Cliquez avec le bouton droit sur le *Models* dossier et sélectionnez **Ajouter** une  >  **classe**. Nommez la classe *TodoItem* et sélectionnez sur **Ajouter**.

* Remplacez le code du modèle par le code suivant :

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Ajoutez un dossier nommé *Models* .

* Ajoutez une `TodoItem` classe au dossier à l' *Models* aide du code suivant :

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* Cliquez avec le bouton droit sur le projet. Sélectionnez **Ajouter**  >  **un nouveau dossier**. Nommez le dossier *Models* .

  ![nouveau dossier](first-web-api-mac/_static/folder.png)

* Cliquez avec le bouton droit sur le *Models* dossier, puis sélectionnez **Ajouter** > **un nouveau fichier** > **General** > **classe générale vide**.

* Nommez la classe *TodoItem* et cliquez sur **Nouveau**.

* Remplacez le code du modèle par le code suivant :

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

La propriété `Id` fonctionne comme la clé unique dans une base de données relationnelle.

Les classes de modèle peuvent se trouver n’importe où dans le projet, mais le *Models* dossier est utilisé par Convention.

## <a name="add-a-database-context-21"></a>Ajouter un contexte de base de données 2,1

Le *contexte de base de données* est la classe principale qui coordonne les fonctionnalités d’Entity Framework pour un modèle de données. Cette classe est créée en dérivant de la classe `Microsoft.EntityFrameworkCore.DbContext`.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Cliquez avec le bouton droit sur le *Models* dossier et sélectionnez **Ajouter** une  >  **classe**. Nommez la classe *TodoContext* et cliquez sur **Ajouter**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

* Ajoutez une `TodoContext` classe au *Models* dossier.

---

* Remplacez le code du modèle par le code suivant :

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context-21"></a>Inscrire le contexte de base de données 2,1

Dans ASP.NET Core, les services tels que le contexte de base de données doivent être inscrits auprès du [conteneur d’injection de dépendances](xref:fundamentals/dependency-injection). Le conteneur fournit le service aux contrôleurs.

Mettez à jour *Startup.cs* avec le code en surbrillance suivant :

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

Le code précédent :

* Supprime les déclarations `using` inutilisées.
* Ajoute le contexte de base de données au conteneur d’injection de dépendances.
* Spécifie que le contexte de base de données utilise une base de données en mémoire.

## <a name="add-a-controller-21"></a>Ajouter un contrôleur 2,1

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Cliquez avec le bouton droit sur le dossier *Contrôleurs*.
* Sélectionnez **Ajouter** > **un nouvel élément**.
* Dans la boîte de dialogue **Ajouter un nouvel élément**, sélectionnez le modèle **Classe de contrôleur d’API**.
* Nommez la classe *TodoController* et sélectionnez **Ajouter**.

  ![Boîte de dialogue Ajouter un nouvel élément avec contrôleur dans la zone de recherche et le contrôleur des API web sélectionné](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

* Dans le dossier *Contrôleurs*, créez une classe nommée `TodoController`.

---

* Remplacez le code du modèle par le code suivant :

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

Le code précédent :

* Définit une classe de contrôleur d’API sans méthodes.
* Marque la classe avec l' [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribut. Cet attribut indique que le contrôleur répond aux requêtes de l’API web. Pour plus d’informations sur les comportements spécifiques que permet l’attribut, consultez <xref:web-api/index>.
* Utilise l’injection de dépendances pour injecter le contexte de base de données (`TodoContext`) dans le contrôleur. Le contexte de base de données est utilisé dans chacune des méthodes la [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) du contrôleur.
* Ajoute un élément nommé `Item1` à la base de données si celle-ci est vide. Ce code se trouvant dans le constructeur, il s’exécute chaque fois qu’une nouvelle requête HTTP existe. Si vous supprimez tous les éléments, le constructeur recrée `Item1` au prochain appel d’une méthode d’API. Ainsi, il peut vous sembler à tort que la suppression n’a pas fonctionné.

## <a name="add-get-methods-21"></a>Ajouter des méthodes de récupération 2,1

Pour fournir une API qui récupère les tâches, ajoutez les méthodes suivantes à la classe `TodoController` :

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

Ces méthodes implémentent deux points de terminaison GET :

* `GET /api/todo`
* `GET /api/todo/{id}`

Arrêtez l’application si elle est toujours en cours d’exécution. Ensuite, réexécutez-la pour inclure les dernières modifications.

Testez l’application en appelant les deux points de terminaison à partir d’un navigateur. Exemple :

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

La réponse HTTP suivante est générée par l’appel à `GetTodoItems` :

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths-21"></a>Routage et chemins d’URL 2,1

L' [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribut désigne une méthode qui répond à une requête http. Le chemin d’URL pour chaque méthode est construit comme suit :

* Partez de la chaîne de modèle dans l’attribut `Route` du contrôleur :

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* Remplacez `[controller]` par le nom du contrôleur qui, par convention, est le nom de la classe du contrôleur sans le suffixe « Controller ». Pour cet exemple, le nom de classe du contrôleur étant **Todo** Controller, le nom du contrôleur est « todo ». Le [routage](xref:mvc/controllers/routing) d’ASP.NET Core ne respecte pas la casse.
* Si l’attribut `[HttpGet]` a un modèle de route (par exemple, `[HttpGet("products")]`), ajoutez-le au chemin. Cet exemple n’utilise pas de modèle. Pour plus d’informations, consultez [Routage par attributs avec des attributs Http[Verbe]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

Dans la méthode `GetTodoItem` suivante, `"{id}"` est une variable d’espace réservé pour l’identificateur unique de la tâche. Quand `GetTodoItem` est appelée, la valeur de `"{id}"` dans l’URL est fournie à la méthode dans son paramètre `id`.

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values-21"></a>Valeurs de retour 2,1

Le type de retour des `GetTodoItems` `GetTodoItem` méthodes et est [ActionResult \<T> type](xref:web-api/action-return-types#actionresultt-type). ASP.NET Core sérialise automatiquement l’objet en [JSON](https://www.json.org/) et écrit le JSON dans le corps du message de réponse. Le code de réponse pour ce type de retour est 200, en supposant qu’il n’existe pas d’exception non gérée. Les exceptions non gérées sont converties en erreurs 5xx.

Les types de retour `ActionResult` peuvent représenter une large plage de codes d’état HTTP. Par exemple, `GetTodoItem` peut retourner deux valeurs d’état différentes :

* Si aucun élément ne correspond à l’ID demandé, la méthode retourne un <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> code d’erreur 404.
* Sinon, la méthode retourne 200 avec un corps de réponse JSON. Le retour de `item` entraîne une réponse HTTP 200.

## <a name="test-the-gettodoitems-method-21"></a>Tester la méthode GetTodoItems 2,1

Ce tutoriel utilise Postman pour tester l’API web.

* Installer [Postman](https://www.getpostman.com/downloads/)
* Démarrez l’application web.
* Démarrez Postman.
* Désactivez la **vérification du certificat SSL**.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* À partir de **Fichier** > **Paramètres** (onglet **Général**), désactivez **Vérification du certificat SSL**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

* Dans **Postman**  >  **Préférences** de publication (onglet **général** ), désactivez la vérification de **certificat SSL**. Vous pouvez également sélectionner la clé et sélectionner **Paramètres**, puis désactiver la vérification du certificat SSL.

---
  
> [!WARNING]
> Réactivez la vérification du certificat SSL après avoir testé le contrôleur.

* Créez une requête.
  * Définissez la méthode HTTP sur **GET**.
  * Définissez l’URI de la demande sur `https://localhost:<port>/api/todo` . Par exemple : `https://localhost:5001/api/todo`.
* Définissez l’**affichage à deux volets** dans Postman.
* Sélectionnez **Envoyer**.

![Postman avec requête Get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method-21"></a>Ajouter une méthode de création 2,1

Ajoutez la méthode `PostTodoItem` suivante à *Controllers/TodoController.cs* : 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

Le code précédent est une méthode HTTP Après, comme indiqué par l' [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribut. La méthode obtient la valeur de la tâche dans le corps de la requête HTTP.

La méthode `CreatedAtAction` :

* Retourne un code d’état HTTP 201 en cas de réussite. HTTP 201 est la réponse standard d’une méthode HTTP POST qui crée une ressource sur le serveur.
* Ajoute un en-tête `Location` à la réponse. L’en-tête `Location` spécifie l’URI de l’élément d’action qui vient d’être créé. Pour plus d’informations, consultez la section [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).
* Fait référence à l’action `GetTodoItem` pour créer l’URI `Location` de l’en-tête. Le mot clé `nameof` C# est utilisé pour éviter de coder en dur le nom de l’action dans l’appel `CreatedAtAction`.

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method-21"></a>Tester la méthode PostTodoItem 2,1

* Créez le projet.
* Dans Postman, définissez la méthode HTTP sur `POST`.
* Définissez l’URI sur `https://localhost:<port>/api/Todo` . Par exemple : `https://localhost:5001/api/Todo`.
* Sélectionnez l’onglet **Corps** .
* Sélectionnez la case d’option **raw** (données brutes).
* Définissez le type sur **JSON (application/json)**.
* Dans le corps de la demande, entrez la syntaxe JSON d’une tâche :

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* Sélectionnez **Envoyer**.

  ![Postman avec requête de création](first-web-api/_static/create.png)

  Si vous obtenez une erreur 405 Méthode non autorisée, il est probable que le projet n’ait pas été compilé après l’ajout de la méthode `PostTodoItem`.

### <a name="test-the-location-header-uri-21"></a>Tester l’URI d’en-tête d’emplacement 2,1

* Sélectionnez l’onglet **Headers** (En-têtes) dans le volet **Response** (Réponse).
* Copiez la valeur d’en-tête **Location** (Emplacement) :

  ![Onglet Headers de la console Postman](first-web-api/_static/pmc2.png)

* Définissez la méthode sur GET.
* Définissez l’URI sur `https://localhost:<port>/api/TodoItems/2` . Par exemple : `https://localhost:5001/api/TodoItems/2`.
* Sélectionnez **Envoyer**.

## <a name="add-a-puttodoitem-method-21"></a>Ajouter une méthode PutTodoItem 2,1

Ajoutez la méthode `PutTodoItem` suivante :

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

`PutTodoItem` est similaire à `PostTodoItem`, à la différence près qu’il utilise HTTP PUT. La réponse est [204 (aucun contenu)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html). D’après la spécification HTTP, une requête PUT nécessite que le client envoie toute l’entité mise à jour, et pas seulement les changements. Pour prendre en charge les mises à jour partielles, utilisez [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).

Si vous obtenez une erreur en appelant `PutTodoItem`, appelez `GET` pour vérifier que la base de données contient un élément.

### <a name="test-the-puttodoitem-method-21"></a>Tester la méthode PutTodoItem 2,1

Cet exemple utilise une base de données en mémoire qui doit être initialisée chaque fois que l’application est démarrée. La base de données doit contenir un élément avant que vous ne passiez un appel PUT. Appelez obtenir pour vous assurer qu’il y a un élément dans la base de données avant d’effectuer un appel PUT.

Mettez à jour l’élément de tâche dont l’ID est égal à 1 et affectez-lui le nom « flux de poisson » :

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

L’image suivante montre la mise à jour Postman :

![Console Postman montrant la réponse 204 (Pas de contenu)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method-21"></a>Ajouter une méthode DeleteTodoItem 2,1

Ajoutez la méthode `DeleteTodoItem` suivante :

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

La réponse `DeleteTodoItem` est [204 (Pas de contenu)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).

### <a name="test-the-deletetodoitem-method-21"></a>Tester la méthode DeleteTodoItem 2,1

Utilisez Postman pour supprimer une tâche :

* Définissez la méthode sur `DELETE`.
* Définissez l’URI de l’objet à supprimer (par exemple, `https://localhost:5001/api/todo/1` ).
* Sélectionnez **Envoyer**.

L’exemple d’application vous permet de supprimer tous les éléments. Toutefois, quand le dernier élément est supprimé, un autre est créé par le constructeur de classe de modèle au prochain appel de l’API.

## <a name="call-the-web-api-with-javascript-21"></a>Appeler l’API Web avec JavaScript 2,1

Dans cette section, une page HTML qui utilise JavaScript pour appeler l’API web est ajoutée. jQuery lance la demande. JavaScript met à jour la page avec les détails de la réponse de l’API Web.

Configurez l’application pour [traiter les fichiers statiques](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) et [activer le mappage de fichier par défaut](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) en mettant à jour *Startup.cs* avec le code en surbrillance suivant :

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

Créez un dossier *wwwroot* dans le répertoire du projet.

Ajoutez un fichier HTML nommé *index.html* au répertoire *wwwroot*. Remplacez son contenu par le balisage suivant :

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

Ajoutez un fichier JavaScript nommé *site.js* au répertoire *wwwroot*. Remplacez son contenu par le code ci-dessous :

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

Vous devrez peut-être changer les paramètres de lancement du projet ASP.NET Core pour tester la page HTML localement :

* Ouvrez *Properties\launchSettings.json*.
* Supprimez la `launchUrl` propriété pour forcer l’ouverture de l’application à *index.html* &mdash; fichier par défaut du projet.

Cet exemple appelle toutes les méthodes CRUD de l’API web. Les explications suivantes traitent des appels à l’API.

### <a name="get-a-list-of-to-do-items-21"></a>Obtenir la liste des éléments de tâche 2,1

jQuery envoie une requête HTTP obtenir à l’API Web, qui retourne JSON représentant un tableau d’éléments de tâche. La fonction de rappel `success` est appelée si la requête réussit. Dans le rappel, le DOM est mis à jour avec les informations des tâches.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item-21"></a>Ajouter un élément de tâche 2,1

jQuery envoie une requête HTTP POSTÉRIEURe avec l’élément de tâche dans le corps de la demande. Les options `accepts` et `contentType` sont définies avec la valeur `application/json` pour spécifier le type de média qui est reçu et envoyé. La tâche est convertie au format JSON à l’aide de [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify). Quand l’API retourne un code d’état de réussite, la fonction `getData` est appelée pour mettre à jour la table HTML.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item-21"></a>Mettre à jour un élément de tâche 2,1

La mise à jour d’une tâche est similaire à l’ajout d’une tâche. L’identificateur unique de la tâche est ajouté à l’`url` et le `type` est `PUT`.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item-21"></a>Supprimer un élément de tâche 2,1

Pour supprimer une tâche, vous devez définir le `type` sur l’appel AJAX avec la valeur `DELETE` et spécifier l’identificateur unique de l’élément dans l’URL.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api-21"></a>Ajouter la prise en charge de l’authentification à une API Web 2,1

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources-21"></a>Ressources supplémentaires 2,1

[Affichez ou téléchargez l’exemple de code de ce tutoriel](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples). Consultez [Guide pratique pour télécharger](xref:index#how-to-download-a-sample).

Pour plus d’informations, consultez les ressources suivantes :

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [Version YouTube de ce tutoriel](https://www.youtube.com/watch?v=TTkhEyGBfAk)
