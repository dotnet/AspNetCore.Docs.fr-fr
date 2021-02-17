---
title: Créer des services backend pour les applications mobiles natives avec ASP.NET Core
author: rick-anderson
description: Découvrez comment créer des services backend en utilisant ASP.NET Core MVC pour prendre en charge des applications mobiles natives.
ms.author: riande
ms.date: 2/12/2021
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
ms.openlocfilehash: e496b7811cc534b6f0f6dfdb857f6e462b38049e
ms.sourcegitcommit: f77a7467651bab61b24261da9dc5c1dd75fc1fa9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100564038"
---
# <a name="create-backend-services-for-native-mobile-apps-with-aspnet-core"></a>Créer des services backend pour les applications mobiles natives avec ASP.NET Core

Par [James Montemagno](https://twitter.com/JamesMontemagno)

Les applications mobiles peuvent communiquer avec les services back-end ASP.NET Core. Pour obtenir des instructions sur la connexion de services web locaux à partir de simulateurs iOS et d’émulateurs Android, consultez [Se connecter à des services web locaux à partir de simulateurs iOS et d’émulateurs Android](/xamarin/cross-platform/deploy-test/connect-to-local-web-services).

[Afficher ou télécharger l’exemple de code de services backend](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST)

## <a name="the-sample-native-mobile-app"></a>Exemple d’application mobile native

Ce didacticiel montre comment créer des services principaux à l’aide de ASP.NET Core pour prendre en charge les applications mobiles natives. Elle utilise l' [application Xamarin. Forms TodoRest](/xamarin/xamarin-forms/data-cloud/consuming/rest) en tant que client natif, qui comprend des clients natifs distincts pour Android, iOS et Windows. Vous pouvez suivre le didacticiel lié pour créer l’application native (et installer les outils Xamarin gratuits nécessaires), ainsi que télécharger l’exemple de solution Xamarin. L’exemple Xamarin comprend un projet de services d’API Web ASP.NET Core, que cet article ASP.NET Core application remplace (sans aucune modification requise par le client).

![Application TodoREST s’exécutant sur un smartphone Android](native-mobile-backend/_static/todo-android.png)

### <a name="features"></a>Fonctionnalités

L' [application TodoREST](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST) prend en charge l’affichage, l’ajout, la suppression et la mise à jour des éléments de To-Do. Chaque élément a un ID, un nom, des notes et une propriété qui indique si elle est déjà effectuée.

La vue principale des éléments, reproduite ci-dessus, montre le nom de chaque élément et indique si la tâche est effectuée avec une marque.

Le fait d’appuyer sur l'icône`+` ouvre une boîte de dialogue permettant l’ajout d’un élément :

![Boîte de dialogue pour ajouter un élément](native-mobile-backend/_static/todo-android-new-item.png)

Le fait de cliquer sur un élément de l’écran de la liste principale ouvre une boîte de dialogue où les valeurs pour Name, Notes et Done peuvent être modifiées, et où vous pouvez supprimer l’élément :

![Boîte de dialogue pour modifier un élément](native-mobile-backend/_static/todo-android-edit-item.png)

Pour tester vous-même l’application ASP.NET Core créée dans la section suivante en cours d’exécution sur votre ordinateur, mettez à jour la constante de l’application [`RestUrl`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs#L13) .

Les émulateurs Android ne s’exécutent pas sur l’ordinateur local et utilisent une adresse IP de bouclage (10.0.2.2) pour communiquer avec l’ordinateur local. Tirez parti de [Xamarin. Essentials DeviceInfo](/xamarin/essentials/device-information/) pour détecter les opérations exécutées par le système afin d’utiliser l’URL correcte.

Accédez au [`TodoREST`](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoREST) projet et ouvrez le [`Constants.cs`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs) fichier. Le fichier *constants.cs* contient la configuration suivante.

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoREST/Constants.cs" highlight="13":::

Vous pouvez éventuellement déployer le service Web sur un service Cloud tel qu’Azure et mettre à jour le `RestUrl` .

## <a name="creating-the-aspnet-core-project"></a>Création du projet ASP.NET Core

réez une application web ASP.NET Core dans Visual Studio. Choisissez le modèle API Web. Nommez le projet *TodoAPI*.

![Boîte de dialogue Nouvelle application web ASP.NET avec le modèle de projet API web sélectionné](native-mobile-backend/_static/web-api-template.png)

L’application doit répondre à toutes les demandes adressées au port 5000, y compris le trafic HTTP en texte clair pour notre client mobile. Mettez à jour *Startup.cs* de sorte qu’il <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> ne s’exécute pas dans le développement :

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Startup.cs" id="snippet" highlight="7-11":::

> [!NOTE]
> Exécutez l’application directement, plutôt qu’en arrière-plan IIS Express. IIS Express ignore par défaut les demandes non locales. Exécutez [dotnet exécuter](/dotnet/core/tools/dotnet-run) à partir d’une invite de commandes, ou choisissez le profil de nom d’application dans la liste déroulante cible de débogage de la barre d’outils de Visual Studio.

Ajoutez une classe de modèle pour représenter des éléments de tâche à effectuer. Marquez les champs obligatoires avec l' `[Required]` attribut :

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Models/TodoItem.cs":::

Les méthodes d’API requièrent un moyen d’utiliser des données. Utilisez la même interface `ITodoRepository` que celle utilisée par l’exemple Xamarin d’origine :

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Interfaces/ITodoRepository.cs":::

Pour cet exemple, l’implémentation utilise simplement une collection privée d’éléments :

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Services/TodoRepository.cs":::

Configurez l’implémentation dans *Startup.cs* :

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Startup.cs" id="snippet2" highlight="3":::

## <a name="creating-the-controller"></a>Création du contrôleur

Ajoutez un nouveau contrôleur au projet, [TodoItemsController](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs). Il doit hériter de <xref:Microsoft.AspNetCore.Mvc.ControllerBase> . Ajoutez un attribut `Route` pour indiquer que le contrôleur gère les demandes effectuées via des chemins commençant par `api/todoitems`. Le jeton `[controller]` de la route est remplacé par le nom du contrôleur (en omettant le suffixe `Controller`) et est particulièrement pratique pour les routes globales. Découvrez plus d’informations sur le [routage](../fundamentals/routing.md).

Le contrôleur nécessite un `ITodoRepository` pour fonctionner ; demandez une instance de ce type via le constructeur du contrôleur. À l’exécution, cette instance est fournie via la prise en charge par l’infrastructure de [l’injection de dépendances](../fundamentals/dependency-injection.md).

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetDI":::

Cette API prend en charge quatre verbes HTTP différents pour effectuer des opérations CRUD (création, lecture, mise à jour, suppression) sur la source de données. La plus simple d’entre elles est l’opération de lecture, qui correspond à une requête HTTP GET.

### <a name="reading-items"></a>Lecture d’éléments

Demander une liste d’éléments se fait via une requête GET à la méthode `List`. L’attribut `[HttpGet]` sur la méthode `List` indique que cette action doit gérer seulement les requêtes GET. La route pour cette action est la route spécifiée sur le contrôleur. Le nom de l’action ne doit pas nécessairement constituer une partie de la route. Il vous suffit de faire en sorte que chaque action ait une route unique et non ambiguë. Des attributs de routage peuvent être appliqués aux niveaux du contrôleur et des méthodes pour créer des routes spécifiques.

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippet":::

La `List` méthode retourne un code de réponse 200 OK et tous les éléments todo sérialisés au format JSON.

Vous pouvez tester votre nouvelle méthode d’API via différents outils, comme [Postman](https://www.getpostman.com/docs/), qui est montré ici :

![Console de Postman montrant une requête GET pour les éléments de tâche à effectuer (todoitems) et le corps de la réponse montrant le JSON pour les trois éléments retournés](native-mobile-backend/_static/postman-get.png)

### <a name="creating-items"></a>Création d’éléments

Par convention, la création d’éléments de données est mappée au verbe HTTP POST. `Create`Un `[HttpPost]` attribut est appliqué à la méthode et accepte une `TodoItem` instance. Étant donné que l' `item` argument est passé dans le corps de la publication, ce paramètre spécifie l' `[FromBody]` attribut.

À l’intérieur de la méthode, la validité et l’existence préalable de l’élément dans le magasin de données sont vérifiées et, si aucun problème ne se produit, il est ajouté via le référentiel. La vérification `ModelState.IsValid` effectue la [validation du modèle](../mvc/models/validation.md) et doit être effectuée dans chaque méthode d’API qui accepte une entrée utilisateur.

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetCreate":::

L’exemple utilise un `enum` code d’erreur contenant qui est transmis au client mobile :

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetErrorCode":::

Testez en ajoutant de nouveaux éléments avec Postman, en choisissant le verbe POST qui fournit le nouvel objet au format JSON dans le corps de la requête. Vous devez également ajouter un en-tête de requête spécifiant un `Content-Type` de `application/json`.

![Console de Postman montrant un POST et la réponse](native-mobile-backend/_static/postman-post.png)

La méthode retourne l’élément qui vient d’être créé dans la réponse.

### <a name="updating-items"></a>Mise à jour d’éléments

La modification des enregistrements est effectuée via des requêtes HTTP PUT. Outre cette modification, la méthode `Edit` est presque identique à `Create`. Notez que si l’enregistrement n’est pas trouvé, l’action `Edit` retourne une réponse `NotFound` (404).

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetEdit":::

Pour tester avec Postman, changez le verbe en PUT. Spécifiez les données de l’objet mis à jour dans le corps de la requête.

![Console de Postman montrant un PUT et la réponse](native-mobile-backend/_static/postman-put.png)

Cette méthode retourne une réponse `NoContent` (204) en cas de réussite, pour des raisons de cohérence avec l’API préexistante.

### <a name="deleting-items"></a>Suppression d’éléments

La suppression d’enregistrements est effectuée via des requêtes DELETE adressées au service, en passant l’ID de l’élément à supprimer. Comme pour les mises à jour, les requêtes pour des éléments qui n’existent pas reçoivent des réponses `NotFound`. Sinon, une requête qui réussit obtient une réponse `NoContent` (204).

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetDelete":::

Notez que quand vous testez les fonctionnalités de suppression, rien n’est obligatoire dans le corps de la requête.

![Console de Postman montrant un DELETE et la réponse](native-mobile-backend/_static/postman-delete.png)

## <a name="prevent-over-posting"></a>Empêcher la post-validation

Actuellement, l’exemple d’application expose l' `TodoItem` objet entier. En général, les applications de production limitent les données entrées et retournées à l’aide d’un sous-ensemble du modèle. Il existe plusieurs raisons à cela, et la sécurité est essentielle. Le sous-ensemble d’un modèle est généralement appelé un objet Transfert de données (DTO), un modèle d’entrée ou un modèle de vue. Le **DTO** est utilisé dans cet article.

Un DTO peut être utilisé pour :

* Empêcher la survalidation.
* Masquer les propriétés que les clients ne sont pas censés afficher.
* Omettez certaines propriétés afin de réduire la taille de la charge utile.
* Aplatir les graphiques d’objets qui contiennent des objets imbriqués. Les graphiques d’objets aplatis peuvent être plus pratiques pour les clients.

Pour illustrer l’approche DTO, consultez [empêcher la survalidation](xref:tutorials/first-web-api#prevent-over-posting)

## <a name="common-web-api-conventions"></a>Conventions des API web courantes

Quand vous développez des services backend pour votre application, vous souhaitez obtenir un ensemble cohérent de conventions ou de stratégies pour gérer les problèmes transversaux. Par exemple, dans le service montré ci-dessus, les requêtes pour des enregistrements spécifiques qui n’ont pas été trouvés ont reçu une réponse `NotFound` et non pas une réponse `BadRequest`. De même, les commandes envoyées à ce service qui ont passé des types liés au modèle ont toujours vérifié `ModelState.IsValid` et retourné un `BadRequest` pour les types de modèle non valide.

Une fois que vous avez identifié une stratégie commune pour vos API, vous pouvez en général l’encapsuler dans un [filtre](../mvc/controllers/filters.md). Découvrez plus d’informations sur [la façon d’encapsuler des stratégies d’API courantes dans les applications ASP.NET Core MVC](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Xamarin. Forms : authentification du service Web](/xamarin/xamarin-forms/data-cloud/authentication/)
- [Xamarin. Forms : utiliser un service Web RESTful](/xamarin/xamarin-forms/data-cloud/web-services/rest)
- [Microsoft Learn : utilisation des services Web REST dans les applications Xamarin](/learn/modules/consume-rest-services/)
- [Microsoft Learn : créer une API Web avec ASP.NET Core](/learn/modules/build-web-api-aspnet-core/)
