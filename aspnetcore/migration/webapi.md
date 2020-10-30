---
title: Migrer de API Web ASP.NET vers ASP.NET Core
author: ardalis
description: Découvrez comment migrer une implémentation d’API Web à partir de l’API Web ASP.NET 4. x vers ASP.NET Core MVC.
ms.author: scaddie
ms.custom: mvc
ms.date: 05/26/2020
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
uid: migration/webapi
ms.openlocfilehash: 320805c0d40bf06cee384e6d98caea5c420d45bc
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061468"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a>Migrer de API Web ASP.NET vers ASP.NET Core

Par [Scott Addie](https://twitter.com/scott_addie) et [Steve Smith](https://ardalis.com/)

Une API Web ASP.NET 4. x est un service HTTP qui atteint un large éventail de clients, y compris des navigateurs et des appareils mobiles. ASP.NET Core associe des modèles d’application MVC et API Web ASP.NET 4. x dans un modèle de programmation unique appelé ASP.NET Core MVC. Cet article décrit les étapes nécessaires à la migration de l’API Web ASP.NET 4. x vers ASP.NET Core MVC.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="review-aspnet-4x-web-api-project"></a>Examiner le projet d’API Web ASP.NET 4. x

Cet article utilise le projet *ProductsApp* créé dans [prise en main avec API Web ASP.NET 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api). Dans ce projet, un projet d’API Web ASP.NET 4. x de base est configuré comme suit.

Dans *global.asax.cs* , un appel est effectué à `WebApiConfig.Register` :

[!code-csharp[](webapi/sample/3.x/ProductsApp/Global.asax.cs?highlight=14)]

La `WebApiConfig` classe se trouve dans le dossier *App_Start* et a une `Register` méthode statique :

[!code-csharp[](webapi/sample/3.x/ProductsApp/App_Start/WebApiConfig.cs)]

La classe précédente :

* Configure le [routage des attributs](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), bien qu’il ne soit pas réellement utilisé.
* Configure la table de routage.
L’exemple de code s’attend à ce que les URL correspondent au format `/api/{controller}/{id}` , avec `{id}` facultatif.

Les sections suivantes montrent comment migrer le projet d’API Web vers ASP.NET Core MVC.

## <a name="create-the-destination-project"></a>Créer le projet de destination

Créez une nouvelle solution vide dans Visual Studio et ajoutez le projet d’API Web ASP.NET 4. x à migrer :

1. Dans le menu **fichier** , sélectionnez **nouveau** > **projet** .
1. Sélectionnez le modèle de **solution vide** , puis cliquez sur **suivant** .
1. Nommez la solution *WebAPIMigration* . Sélectionnez **Create** (Créer).
1. Ajoutez le projet *ProductsApp* existant à la solution.

Ajoutez un nouveau projet d’API à migrer vers :

1. Ajoutez un nouveau **ASP.net Core projet d’application Web** à la solution.
1. Dans la boîte de dialogue **configurer votre nouveau projet** , nommez le projet *ProductsCore* , puis sélectionnez **créer** .
1. Dans la boîte de dialogue **créer une application Web ASP.net Core** , vérifiez que **.net Core** et **ASP.net Core 3,1** sont sélectionnés. Sélectionnez le modèle de projet **API** , puis sélectionnez **Créer** .
1. Supprimez les fichiers d’exemple *WeatherForecast.cs* et *Controllers/WeatherForecastController. cs* du nouveau projet *ProductsCore* .

La solution contient maintenant deux projets. Les sections suivantes expliquent comment migrer le contenu du projet *ProductsApp* vers le projet *ProductsCore* .

## <a name="migrate-configuration"></a>Migrer une configuration

ASP.NET Core n’utilise pas le dossier *App_Start* ou le fichier *global. asax* . En outre, le fichier *web.config* est ajouté au moment de la publication.

La classe `Startup` :

* Remplace *global. asax* .
* Gère toutes les tâches de démarrage de l’application.

Pour plus d'informations, consultez <xref:fundamentals/startup>.

## <a name="migrate-models-and-controllers"></a>Migrer les modèles et les contrôleurs

Le code suivant montre le `ProductsController` à mettre à jour pour ASP.net Core :

[!code-csharp[](webapi/sample/3.x/ProductsApp/Controllers/ProductsController.cs)]

Mettez à jour le `ProductsController` pour ASP.net Core :

1. Copiez les *contrôleurs/ProductsController. cs* et le dossier *modèles* du projet d’origine vers le nouveau.
1. Remplacez l’espace de noms racine des fichiers copiés par `ProductsCore` .
1. Mettez à jour l' `using ProductsApp.Models;` instruction avec la valeur `using ProductsCore.Models;` .

Les composants suivants n’existent pas dans ASP.NET Core :

* Classe `ApiController`
* Espace de noms `System.Web.Http`
* Interface `IHttpActionResult`

Effectuez les modifications suivantes :

1. Remplacez `ApiController` par <xref:Microsoft.AspNetCore.Mvc.ControllerBase>. Ajoutez `using Microsoft.AspNetCore.Mvc;` pour résoudre la `ControllerBase` référence.
1. Supprimez `using System.Web.Http;`.
1. Remplacez le `GetProduct` type de retour de l’action par `IHttpActionResult` `ActionResult<Product>` .
1. Simplifiez l’instruction de l' `GetProduct` action comme `return` suit :

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a>Configurer le routage

Le modèle de projet d' *API* ASP.net core comprend la configuration du routage de point de terminaison dans le code généré.

Les éléments suivants <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> et <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> appellent :

* Inscrire la correspondance d’itinéraire et l’exécution du point de terminaison dans le pipeline du [middleware](xref:fundamentals/middleware/index) .
* Remplacez le fichier *App_Start/webapiconfig.cs* du projet *ProductsApp* .

[!code-csharp[](webapi/sample/3.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=10,14)]

Configurez le routage comme suit :

1. Marquez la `ProductsController` classe avec les attributs suivants :

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    L' [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribut précédent configure le modèle de routage des attributs du contrôleur. L' [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribut rend l’acheminement des attributs obligatoire pour toutes les actions dans ce contrôleur.

    Le routage d’attributs prend en charge les jetons, tels que `[controller]` et `[action]` . Lors de l’exécution, chaque jeton est remplacé par le nom du contrôleur ou de l’action, respectivement, auquel l’attribut a été appliqué. Les jetons :
    * Réduisez le nombre de chaînes magiques dans le projet.
    * Assurez-vous que les itinéraires restent synchronisés avec les contrôleurs et actions correspondants lorsque les refactorisations de renommage automatique sont appliquées.
1. Activez les demandes HTTP d’extraction aux `ProductController` actions :
    * Appliquez l' [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribut à l' `GetAllProducts` action.
    * Appliquez l' `[HttpGet("{id}")]` attribut à l' `GetProduct` action.

Exécutez le projet migré et accédez à `/api/products` . La liste complète des trois produits s’affiche. Accédez à `/api/products/1`. Le premier produit s’affiche.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"
## <a name="prerequisites"></a>Prérequis

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a>Examiner le projet d’API Web ASP.NET 4. x

Cet article utilise le projet *ProductsApp* créé dans [prise en main avec API Web ASP.NET 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api). Dans ce projet, un projet d’API Web ASP.NET 4. x de base est configuré comme suit.

Dans *global.asax.cs* , un appel est effectué à `WebApiConfig.Register` :

[!code-csharp[](webapi/sample/2.x/ProductsApp/Global.asax.cs?highlight=14)]

La `WebApiConfig` classe se trouve dans le dossier *App_Start* et a une `Register` méthode statique :

[!code-csharp[](webapi/sample/2.x/ProductsApp/App_Start/WebApiConfig.cs)]

Cette classe configure le [routage des attributs](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), bien qu’elle ne soit pas réellement utilisée dans le projet. Elle configure également la table de routage, qui est utilisée par API Web ASP.NET. Dans ce cas, l’API Web ASP.NET 4. x s’attend à ce que les URL correspondent au format `/api/{controller}/{id}` , avec `{id}` facultatif.

Les sections suivantes montrent comment migrer le projet d’API Web vers ASP.NET Core MVC.

## <a name="create-the-destination-project"></a>Créer le projet de destination

Effectuez les étapes suivantes dans Visual Studio :

* Accédez à **fichier**  >  **nouveau**  >  **projet**  >  **autres types de projets**  >  **Visual Studio solutions** . Sélectionnez **solution vide** et nommez la solution *WebAPIMigration* . Cliquez sur le bouton **OK** .
* Ajoutez le projet *ProductsApp* existant à la solution.
* Ajoutez un nouveau **ASP.net Core projet d’application Web** à la solution. Sélectionnez le Framework cible **.net Core** dans la liste déroulante, puis sélectionnez le modèle de projet **API** . Nommez le projet *ProductsCore* , puis cliquez sur le bouton **OK** .

La solution contient maintenant deux projets. Les sections suivantes expliquent comment migrer le contenu du projet *ProductsApp* vers le projet *ProductsCore* .

## <a name="migrate-configuration"></a>Migrer une configuration

ASP.NET Core n’utilise pas :

* *App_Start* dossier ou le fichier *global. asax*
* *web.config* fichier est ajouté au moment de la publication.

La classe `Startup` :

* Remplace *global. asax* .
* Gère toutes les tâches de démarrage de l’application.

Pour plus d'informations, consultez <xref:fundamentals/startup>.

Dans ASP.NET Core MVC, le routage des attributs est inclus par défaut quand <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> est appelé dans `Startup.Configure` . L' `UseMvc` appel suivant remplace le fichier *App_Start/webapiconfig.cs* du projet *ProductsApp* :

[!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13)]

## <a name="migrate-models-and-controllers"></a>Migrer les modèles et les contrôleurs

Le code suivant illustre la `ProductsController` mise à jour de ASP.net Core : [!code-csharp[](webapi/sample/2.x/ProductsApp/Controllers/ProductsController.cs)]

Mettez à jour le `ProductsController` pour ASP.net Core :

1. Copiez les *contrôleurs/ProductsController. cs* du projet d’origine vers le nouveau.
1. Copiez le dossier *Models* du projet d’origine vers le nouveau.
1. Remplacez l’espace de noms racine des fichiers copiés par `ProductsCore` .
1. Mettez à jour l' `using ProductsApp.Models;` instruction avec la valeur `using ProductsCore.Models;` .

Les composants suivants n’existent pas dans ASP.NET Core :

* Classe `ApiController`
* Espace de noms `System.Web.Http`
* Interface `IHttpActionResult`

Effectuez les modifications suivantes :

1. Remplacez `ApiController` par <xref:Microsoft.AspNetCore.Mvc.ControllerBase>. Ajoutez `using Microsoft.AspNetCore.Mvc;` pour résoudre la `ControllerBase` référence.
1. Supprimez `using System.Web.Http;`.
1. Remplacez le `GetProduct` type de retour de l’action par `IHttpActionResult` `ActionResult<Product>` .
1. Simplifiez l’instruction de l' `GetProduct` action comme `return` suit :

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a>Configurer le routage

Configurez le routage comme suit :

1. Marquez la `ProductsController` classe avec les attributs suivants :

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    L' [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribut précédent configure le modèle de routage des attributs du contrôleur. L' [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribut rend l’acheminement des attributs obligatoire pour toutes les actions dans ce contrôleur.

    Le routage d’attributs prend en charge les jetons, tels que `[controller]` et `[action]` . Lors de l’exécution, chaque jeton est remplacé par le nom du contrôleur ou de l’action, respectivement, auquel l’attribut a été appliqué. Les jetons réduisent le nombre de chaînes magiques dans le projet. Les jetons garantissent également que les itinéraires restent synchronisés avec les contrôleurs et actions correspondants lorsque les refactorisations de renommage automatique sont appliquées.
1. Définissez le mode de compatibilité du projet sur ASP.NET Core 2,2 :

    [!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    Modification précédente :

    * Est requis pour utiliser l' `[ApiController]` attribut au niveau du contrôleur.
    * Opte pour l’interruption potentielle des comportements introduits dans ASP.NET Core 2,2.
1. Activez les demandes HTTP d’extraction aux `ProductController` actions :
    * Appliquez l' [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribut à l' `GetAllProducts` action.
    * Appliquez l' `[HttpGet("{id}")]` attribut à l' `GetProduct` action.

Exécutez le projet migré et accédez à `/api/products` . La liste complète des trois produits s’affiche. Accédez à `/api/products/1`. Le premier produit s’affiche.

## <a name="compatibility-shim"></a>Shim de compatibilité

La bibliothèque [Microsoft. AspNetCore. Mvc. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) fournit un shim de compatibilité pour déplacer des projets d’API Web ASP.net 4. x vers ASP.net core. Le shim de compatibilité étend ASP.NET Core pour prendre en charge un certain nombre de conventions de l’API Web 2 de ASP.NET 4. x. L’exemple porté précédemment dans ce document est suffisamment basique pour que le shim de compatibilité soit inutile. Pour les projets de grande taille, l’utilisation du shim de compatibilité peut être utile pour combler temporairement le fossé entre les API ASP.NET Core et ASP.NET 4. x Web API 2.

Le shim de compatibilité de l’API Web est destiné à être utilisé comme mesure temporaire pour prendre en charge la migration de grands projets d’API Web ASP.NET 4. x vers ASP.NET Core. Au fil du temps, les projets doivent être mis à jour pour utiliser des modèles de ASP.NET Core au lieu de s’appuyer sur le shim de compatibilité.

Les fonctionnalités de compatibilité incluses dans `Microsoft.AspNetCore.Mvc.WebApiCompatShim` sont les suivantes :

* Ajoute un `ApiController` type afin que les types de base des contrôleurs n’aient pas besoin d’être mis à jour.
* Active la liaison de modèle de style API Web. ASP.NET Core liaison de modèle MVC fonctionne de façon similaire à celle de ASP.NET 4. x MVC 5, par défaut. Le shim de compatibilité modifie la liaison de modèle pour qu’elle soit plus semblable aux conventions de liaison de modèle d’API Web 2 ASP.NET 4. x. Par exemple, les types complexes sont automatiquement liés à partir du corps de la demande.
* Étend la liaison de modèle afin que les actions du contrôleur puissent prendre des paramètres de type `HttpRequestMessage` .
* Ajoute des formateurs de message permettant aux actions de retourner des résultats de type `HttpResponseMessage` .
* Ajoute des méthodes de réponse supplémentaires que les actions de l’API Web 2 peuvent avoir utilisées pour répondre aux réponses :
  * `HttpResponseMessage` générateurs
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * Méthodes de résultat d’action :
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* Ajoute une instance de `IContentNegotiator` au conteneur d’injection de dépendances de l’application et rend disponibles les types liés à la négociation de contenu à partir de [Microsoft. Aspnet. WebApi. client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/). Voici quelques exemples de ces types : `DefaultContentNegotiator` et `MediaTypeFormatter` .

Pour utiliser le shim de compatibilité :

1. Installez le package NuGet [Microsoft. AspNetCore. Mvc. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) .
1. Enregistrez les services du shim de compatibilité avec le conteneur DI de l’application en appelant `services.AddMvc().AddWebApiConventions()` dans `Startup.ConfigureServices` .
1. Définissez des itinéraires spécifiques à l’API Web à l’aide `MapWebApiRoute` de sur le `IRouteBuilder` dans l’appel de l’application `IApplicationBuilder.UseMvc` .

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
::: moniker-end
