---
title: Créer des services backend pour les applications mobiles natives avec ASP.NET Core
author: ardalis
description: Découvrez comment créer des services backend en utilisant ASP.NET Core MVC pour prendre en charge des applications mobiles natives.
ms.author: riande
ms.date: 12/05/2019
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
uid: mobile/native-mobile-backend
ms.openlocfilehash: 4e86241771e884ba9079bcdf9a09eebc6acd62c8
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530214"
---
# <a name="create-backend-services-for-native-mobile-apps-with-aspnet-core"></a>Créer des services backend pour les applications mobiles natives avec ASP.NET Core

Par [Steve Smith](https://ardalis.com/)

Les applications mobiles peuvent communiquer avec les services back-end ASP.NET Core. Pour obtenir des instructions sur la connexion de services web locaux à partir de simulateurs iOS et d’émulateurs Android, consultez [Se connecter à des services web locaux à partir de simulateurs iOS et d’émulateurs Android](/xamarin/cross-platform/deploy-test/connect-to-local-web-services).

[Afficher ou télécharger l’exemple de code de services backend](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mobile/native-mobile-backend/sample)

## <a name="the-sample-native-mobile-app"></a>Exemple d’application mobile native

Ce didacticiel montre comment créer des services backend en utilisant ASP.NET Core MVC pour prendre en charge des applications mobiles natives. Il utilise [l’application Xamarin.Forms TodoREST](/xamarin/xamarin-forms/data-cloud/consuming/rest) comme client natif, qui inclut des clients natifs distincts pour les appareils Android, iOS, Windows universel et Windows Phone. Vous pouvez suivre le didacticiel lié pour créer l’application native (et installer les outils Xamarin gratuits nécessaires), ainsi que télécharger l’exemple de solution Xamarin. L’exemple Xamarin inclut un projet de services ASP.NET API web 2, que l’application ASP.NET Core de cet article remplace (sans que des modifications soient requises par le client).

![Application TodoREST s’exécutant sur un smartphone Android](native-mobile-backend/_static/todo-android.png)

### <a name="features"></a>Fonctionnalités

L’application TodoREST prend en charge l’affichage, l’ajout, la suppression et la mise à jour d’éléments de tâche à effectuer. Chaque élément a un ID, un nom, des notes et une propriété qui indique si elle est déjà effectuée.

La vue principale des éléments, reproduite ci-dessus, montre le nom de chaque élément et indique si la tâche est effectuée avec une marque.

Le fait d’appuyer sur l'icône`+` ouvre une boîte de dialogue permettant l’ajout d’un élément :

![Boîte de dialogue pour ajouter un élément](native-mobile-backend/_static/todo-android-new-item.png)

Le fait de cliquer sur un élément de l’écran de la liste principale ouvre une boîte de dialogue où les valeurs pour Name, Notes et Done peuvent être modifiées, et où vous pouvez supprimer l’élément :

![Boîte de dialogue pour modifier un élément](native-mobile-backend/_static/todo-android-edit-item.png)

Cet exemple est configuré par défaut pour utiliser les services backend hébergés sur developer.xamarin.com, qui autorisent des opérations en lecture seule. Pour le tester vous-même par rapport à l’application ASP.NET Core créée dans la section suivante et exécuté sur votre ordinateur, vous devez mettre à jour la constante `RestUrl` de l’application. Accédez au projet `ToDoREST` et ouvrez le fichier *Constants.cs*. Remplacez `RestUrl` par une URL qui inclut l’adresse IP de votre ordinateur (pas localhost ni 127.0.0.1, car cette adresse est utilisée depuis l’émulateur d’appareil, et non pas depuis votre ordinateur). Incluez également le numéro de port (5000). Pour tester que vos services fonctionnent avec un appareil, vérifiez que vous n’avez pas un pare-feu actif bloquant l’accès à ce port.

```csharp
// URL of REST service (Xamarin ReadOnly Service)
//public static string RestUrl = "http://developer.xamarin.com:8081/api/todoitems{0}";

// use your machine's IP address
public static string RestUrl = "http://192.168.1.207:5000/api/todoitems/{0}";
```

## <a name="creating-the-aspnet-core-project"></a>Création du projet ASP.NET Core

réez une application web ASP.NET Core dans Visual Studio. Choisissez le modèle "API web" et Pas d’authentification. Nommez le projet *ToDoApi*.

![Boîte de dialogue Nouvelle application web ASP.NET avec le modèle de projet API web sélectionné](native-mobile-backend/_static/web-api-template.png)

L’application doit répondre à toutes les demandes adressées au port 5000. Pour cela, mettez à jour *Program.cs* en y incluant `.UseUrls("http://*:5000")` :

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Program.cs?range=10-16&highlight=3)]

> [!NOTE]
> Vérifiez que vous exécutez l’application directement et non pas derrière IIS Express, qui ignore par défaut les demandes non locales. Exécutez [dotnet run](/dotnet/core/tools/dotnet-run) à partir d’une invite de commandes, ou choisissez le profil du nom d’application dans la liste déroulante Cible de débogage dans la barre d’outils de Visual Studio.

Ajoutez une classe de modèle pour représenter des éléments de tâche à effectuer. Marquez les champs obligatoires avec l' `[Required]` attribut :

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Models/ToDoItem.cs)]

Les méthodes d’API requièrent un moyen d’utiliser des données. Utilisez la même interface `IToDoRepository` que celle utilisée par l’exemple Xamarin d’origine :

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Interfaces/IToDoRepository.cs)]

Pour cet exemple, l’implémentation utilise simplement une collection privée d’éléments :

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Services/ToDoRepository.cs)]

Configurez l’implémentation dans *Startup.cs* :

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Startup.cs?highlight=6&range=29-35)]

À ce stade, vous êtes prêt à créer le *ToDoItemsController*.

> [!TIP]
> Découvrez plus en détail comment créer des API web dans [Créer votre première API web avec ASP.NET Core MVC et Visual Studio](../tutorials/first-web-api.md).

## <a name="creating-the-controller"></a>Création du contrôleur

Ajoutez un nouveau contrôleur au projet, *ToDoItemsController*. Il doit hériter de Microsoft.AspNetCore.Mvc.Controller. Ajoutez un attribut `Route` pour indiquer que le contrôleur gère les demandes effectuées via des chemins commençant par `api/todoitems`. Le jeton `[controller]` de la route est remplacé par le nom du contrôleur (en omettant le suffixe `Controller`) et est particulièrement pratique pour les routes globales. Découvrez plus d’informations sur le [routage](../fundamentals/routing.md).

Le contrôleur nécessite un `IToDoRepository` pour fonctionner ; demandez une instance de ce type via le constructeur du contrôleur. À l’exécution, cette instance est fournie via la prise en charge par l’infrastructure de [l’injection de dépendances](../fundamentals/dependency-injection.md).

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=1-17&highlight=9,14)]

Cette API prend en charge quatre verbes HTTP différents pour effectuer des opérations CRUD (création, lecture, mise à jour, suppression) sur la source de données. La plus simple d’entre elles est l’opération de lecture, qui correspond à une requête HTTP GET.

### <a name="reading-items"></a>Lecture d’éléments

Demander une liste d’éléments se fait via une requête GET à la méthode `List`. L’attribut `[HttpGet]` sur la méthode `List` indique que cette action doit gérer seulement les requêtes GET. La route pour cette action est la route spécifiée sur le contrôleur. Le nom de l’action ne doit pas nécessairement constituer une partie de la route. Il vous suffit de faire en sorte que chaque action ait une route unique et non ambiguë. Des attributs de routage peuvent être appliqués aux niveaux du contrôleur et des méthodes pour créer des routes spécifiques.

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=19-23)]

La méthode `List` retourne un code de réponse 200 OK et tous les éléments de tâche à effectuer, sérialisés au format JSON.

Vous pouvez tester votre nouvelle méthode d’API via différents outils, comme [Postman](https://www.getpostman.com/docs/), qui est montré ici :

![Console de Postman montrant une requête GET pour les éléments de tâche à effectuer (todoitems) et le corps de la réponse montrant le JSON pour les trois éléments retournés](native-mobile-backend/_static/postman-get.png)

### <a name="creating-items"></a>Création d’éléments

Par convention, la création d’éléments de données est mappée au verbe HTTP POST. `Create`Un `[HttpPost]` attribut est appliqué à la méthode et accepte une `ToDoItem` instance. Étant donné que l' `item` argument est passé dans le corps de la publication, ce paramètre spécifie l' `[FromBody]` attribut.

À l’intérieur de la méthode, la validité et l’existence préalable de l’élément dans le magasin de données sont vérifiées et, si aucun problème ne se produit, il est ajouté via le référentiel. La vérification `ModelState.IsValid` effectue la [validation du modèle](../mvc/models/validation.md) et doit être effectuée dans chaque méthode d’API qui accepte une entrée utilisateur.

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=25-46)]

L’exemple utilise une énumération contenant les codes d’erreur qui sont passés au client mobile :

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=91-99)]

Testez en ajoutant de nouveaux éléments avec Postman, en choisissant le verbe POST qui fournit le nouvel objet au format JSON dans le corps de la requête. Vous devez également ajouter un en-tête de requête spécifiant un `Content-Type` de `application/json`.

![Console de Postman montrant un POST et la réponse](native-mobile-backend/_static/postman-post.png)

La méthode retourne l’élément qui vient d’être créé dans la réponse.

### <a name="updating-items"></a>Mise à jour d’éléments

La modification des enregistrements est effectuée via des requêtes HTTP PUT. Outre cette modification, la méthode `Edit` est presque identique à `Create`. Notez que si l’enregistrement n’est pas trouvé, l’action `Edit` retourne une réponse `NotFound` (404).

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=48-69)]

Pour tester avec Postman, changez le verbe en PUT. Spécifiez les données de l’objet mis à jour dans le corps de la requête.

![Console de Postman montrant un PUT et la réponse](native-mobile-backend/_static/postman-put.png)

Cette méthode retourne une réponse `NoContent` (204) en cas de réussite, pour des raisons de cohérence avec l’API préexistante.

### <a name="deleting-items"></a>Suppression d’éléments

La suppression d’enregistrements est effectuée via des requêtes DELETE adressées au service, en passant l’ID de l’élément à supprimer. Comme pour les mises à jour, les requêtes pour des éléments qui n’existent pas reçoivent des réponses `NotFound`. Sinon, une requête qui réussit obtient une réponse `NoContent` (204).

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=71-88)]

Notez que quand vous testez les fonctionnalités de suppression, rien n’est obligatoire dans le corps de la requête.

![Console de Postman montrant un DELETE et la réponse](native-mobile-backend/_static/postman-delete.png)

## <a name="common-web-api-conventions"></a>Conventions des API web courantes

Quand vous développez des services backend pour votre application, vous souhaitez obtenir un ensemble cohérent de conventions ou de stratégies pour gérer les problèmes transversaux. Par exemple, dans le service montré ci-dessus, les requêtes pour des enregistrements spécifiques qui n’ont pas été trouvés ont reçu une réponse `NotFound` et non pas une réponse `BadRequest`. De même, les commandes envoyées à ce service qui ont passé des types liés au modèle ont toujours vérifié `ModelState.IsValid` et retourné un `BadRequest` pour les types de modèle non valide.

Une fois que vous avez identifié une stratégie commune pour vos API, vous pouvez en général l’encapsuler dans un [filtre](../mvc/controllers/filters.md). Découvrez plus d’informations sur [la façon d’encapsuler des stratégies d’API courantes dans les applications ASP.NET Core MVC](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Xamarin. Forms : authentification du service Web](/xamarin/xamarin-forms/data-cloud/authentication/)
- [Xamarin. Forms : utiliser un service Web RESTful](/xamarin/xamarin-forms/data-cloud/web-services/rest)
- [Microsoft Learn : utilisation des services Web REST dans les applications Xamarin](/learn/modules/consume-rest-services/)
- [Microsoft Learn : créer une API Web avec ASP.NET Core](/learn/modules/build-web-api-aspnet-core/)
