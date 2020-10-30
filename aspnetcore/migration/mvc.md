---
title: Apprenez à migrer de ASP.NET MVC vers ASP.NET Core MVC
author: wadepickett
description: Découvrez comment démarrer la migration d’un projet ASP.NET MVC vers ASP.NET Core MVC.
ms.author: wpickett
ms.date: 06/18/2020
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
uid: migration/mvc
ms.openlocfilehash: 226ac6da508378c7b3c81779d38dd2e0840f1fed
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051511"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a>Migrer ASP.NET MVC vers ASP.NET Core MVC

::: moniker range=">= aspnetcore-3.0"

Cet article explique comment commencer à migrer un projet ASP.NET MVC vers [ASP.net Core MVC](xref:mvc/overview). Dans le processus, il met en évidence les modifications associées de ASP.NET MVC.

La migration à partir de ASP.NET MVC est un processus en plusieurs étapes. Cet article couvre les points suivants :

* Configuration initiale.
* Contrôleurs et vues de base.
* Contenu statique.
* Dépendances côté client.

Pour la migration de la configuration et du Identity code, consultez [migrer la configuration vers ASP.net Core](xref:migration/configuration) et [migrer l’authentification et Identity vers ASP.net Core](xref:migration/identity).

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="create-the-starter-aspnet-mvc-project"></a>Créer le projet MVC ASP.NET de démarrage

Créer un exemple de projet MVC ASP.NET dans Visual Studio à migrer :

1. Dans le menu **fichier** , sélectionnez **nouveau** > **projet** .
1. Sélectionnez **application Web ASP.net (.NET Framework)** , puis sélectionnez **suivant** .
1. Nommez le projet *application Web 1* pour que l’espace de noms corresponde au projet ASP.net Core créé à l’étape suivante. Sélectionnez **Create** (Créer).
1. Sélectionnez **MVC** , puis sélectionnez **créer** .

## <a name="create-the-aspnet-core-project"></a>Créer le projet ASP.NET Core

Créer une solution avec un nouveau ASP.NET Core projet à migrer vers :

1. Lancez une deuxième instance de Visual Studio.
1. Dans le menu **fichier** , sélectionnez **nouveau** > **projet** .
1. Sélectionnez **Application web ASP.NET Core** , puis **Suivant** .
1. Dans la boîte de dialogue **configurer votre nouveau projet** , nommez le projet *application Web 1* .
1. Définissez l’emplacement sur un répertoire différent de celui du projet précédent pour qu’il utilise le même nom de projet. L’utilisation du même espace de noms facilite la copie du code entre les deux projets. Sélectionnez **Create** (Créer).
1. Dans la boîte de dialogue **créer une application Web ASP.net Core** , vérifiez que **.net Core** et **ASP.net Core 3,1** sont sélectionnés. Sélectionnez le modèle **de projet application Web (Model-View-Controller)** , puis sélectionnez **créer** .

## <a name="configure-the-aspnet-core-site-to-use-mvc"></a>Configurer le site ASP.NET Core pour utiliser MVC

Dans ASP.NET Core projets 3,0 et versions ultérieures, .NET Framework n’est plus une version cible de .NET Framework prise en charge. Votre projet doit cibler .NET Core. Le ASP.NET Core Framework partagé, qui comprend MVC, fait partie de l’installation du Runtime .NET Core. Le Framework partagé est automatiquement référencé lors de l’utilisation du `Microsoft.NET.Sdk.Web` Kit de développement logiciel (SDK) dans le fichier projet :

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Pour plus d’informations, consultez Référence de l' [infrastructure](xref:migration/22-to-30#framework-reference).

Dans ASP.NET Core, la `Startup` classe :

* Remplace *global. asax* .
* Gère toutes les tâches de démarrage de l’application.

Pour plus d'informations, consultez <xref:fundamentals/startup>.

Dans le projet ASP.NET Core, ouvrez le fichier *Startup.cs* :

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=13,30,32&name=snippet)]

Les applications ASP.NET Core doivent accepter les fonctionnalités d’infrastructure avec l’intergiciel (middleware). Le code précédent généré par le modèle ajoute les services et l’intergiciel (middleware) suivants :

* La <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> méthode d’extension enregistre la prise en charge du service MVC pour les contrôleurs, les fonctionnalités liées à l’API et les vues. Pour plus d’informations sur les options d’inscription du service MVC, consultez [inscription du service MVC](xref:migration/22-to-30#mvc-service-registration)
* La <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> méthode d’extension ajoute le gestionnaire de fichiers statiques `Microsoft.AspNetCore.StaticFiles` . La `UseStaticFiles` méthode d’extension doit être appelée avant `UseRouting` . Pour plus d'informations, consultez <xref:fundamentals/static-files>.
* La <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> méthode d’extension ajoute le routage. Pour plus d'informations, consultez <xref:fundamentals/routing>.

Cette configuration existante comprend ce qui est nécessaire pour migrer l’exemple de projet MVC ASP.NET. Pour plus d’informations sur les options de l’intergiciel (middleware) ASP.NET Core, consultez <xref:fundamentals/startup> .

## <a name="migrate-controllers-and-views"></a>Migrer des contrôleurs et des vues

Dans le projet ASP.NET Core, une nouvelle classe de contrôleur vide et une nouvelle classe de vue seraient ajoutées pour servir d’espaces réservés en utilisant les mêmes noms que les classes de contrôleur et de vue dans tout projet MVC ASP.NET à partir duquel effectuer la migration.

Le projet ASP.NET Core *application Web 1* contient déjà un exemple de contrôleur et un affichage minimaux du même nom que le projet MVC ASP.net. Ils serviront donc d’espaces réservés pour le contrôleur MVC ASP.NET et les vues à migrer à partir du projet ASP.NET MVC *application Web 1* .

1. Copiez les méthodes à partir du MVC ASP.NET `HomeController` pour remplacer les nouvelles `HomeController` méthodes ASP.net core. Il n’est pas nécessaire de modifier le type de retour des méthodes d’action. Le type de retour de la méthode d’action du contrôleur du modèle intégré ASP.NET MVC est <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> ; dans ASP.net Core MVC, les méthodes d’action retournent à la `IActionResult` place. L'objet `ActionResult` implémente l'objet `IActionResult`.
1. Dans le projet ASP.net Core, cliquez avec le bouton droit sur le répertoire *views/racine* , puis sélectionnez **Ajouter** un > **élément existant** .
1. Dans la boîte de dialogue **Ajouter un élément existant** , accédez au répertoire *views/* ASP.net du projet *application Web 1* Mvc.
1. Sélectionnez les fichiers *de vue about. cshtml* , *contact. cshtml* et *index. cshtml* Razor , puis sélectionnez **Ajouter** , en remplaçant les fichiers existants.

Pour plus d’informations, consultez <xref:mvc/controllers/actions> et <xref:mvc/views/overview>.

## <a name="test-each-method"></a>Tester chaque méthode

Chaque point de terminaison de contrôleur peut être testé ; Toutefois, la disposition et les styles sont traités plus loin dans le document.

1. Exécutez l’application ASP.NET Core.
1. Appelez les vues rendues à partir du navigateur sur l’application ASP.NET Core en cours en remplaçant le numéro de port actuel par le numéro de port utilisé dans le projet ASP.NET Core. Par exemple : `https://localhost:44375/home/about`.

## <a name="migrate-static-content"></a>Migrer le contenu statique

Dans ASP.NET MVC 5 et versions antérieures, le contenu statique était hébergé à partir du répertoire racine du projet Web et a été mélangé aux fichiers côté serveur. Dans ASP.NET Core, les fichiers statiques sont stockés dans le répertoire [racine Web](xref:fundamentals/index#web-root) du projet. Le répertoire par défaut est *{content root}/wwwroot* , mais il peut être modifié. Pour plus d’informations, consultez [Fichiers statiques dans ASP.NET Core](xref:fundamentals/static-files#serve-static-files).

Copiez le contenu statique du projet *application Web 1* MVC ASP.net dans le répertoire *wwwroot* dans le projet ASP.net Core *application Web 1* :

1. Dans le projet ASP.net Core, cliquez avec le bouton droit sur le répertoire *wwwroot* , puis sélectionnez **Ajouter** un > **élément existant** .
1. Dans la boîte de dialogue **Ajouter un élément existant** , accédez au projet ASP.NET MVC *application Web 1* .
1. Sélectionnez le fichier *favicon. ico* , puis sélectionnez **Ajouter** , en remplaçant le fichier existant.

## <a name="migrate-the-layout-files"></a>Migrer les fichiers de disposition

Copiez les fichiers de disposition de projet MVC ASP.NET dans le projet ASP.NET Core :

1. Dans le projet ASP.net Core, cliquez avec le bouton droit sur le répertoire *views* , puis sélectionnez **Ajouter** un > **élément existant** .
1. Dans la boîte de dialogue **Ajouter un élément existant** , accédez au répertoire *Views* du projet ASP.NET MVC *application Web 1* .
1. Sélectionnez le fichier *_ViewStart. cshtml* , puis sélectionnez **Ajouter** .

Copiez les fichiers de disposition partagée du projet MVC ASP.NET dans le projet ASP.NET Core :

1. Dans le projet ASP.net Core, cliquez avec le bouton droit sur le répertoire *Views/Shared* , puis sélectionnez **Ajouter** un > **élément existant** .
1. Dans la boîte de dialogue **Ajouter un élément existant** , accédez au répertoire *Views/Shared* ASP.net du projet *application Web 1* Mvc.
1. Sélectionnez le fichier *_Layout. cshtml* , puis sélectionnez **Ajouter** , en remplaçant le fichier existant.

Dans le projet ASP.NET Core, ouvrez le fichier *_Layout. cshtml* . Apportez les modifications suivantes pour correspondre au code complet indiqué ci-dessous :

Mettez à jour l’inclusion CSS de démarrage pour qu’elle corresponde au code complet ci-dessous :

1. Remplacez `@Styles.Render("~/Content/css")` par un `<link>` élément pour charger *bootstrap. CSS* (voir ci-dessous).
1. Supprimez `@Scripts.Render("~/bundles/modernizr")`.

Balisage de remplacement terminé pour l’inclusion CSS de démarrage :

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

Mettez à jour l’inclusion Java jQuery et bootstrap pour qu’elle corresponde au code complet ci-dessous :

1. Remplacez `@Scripts.Render("~/bundles/jquery")` par un `<script>` élément (voir ci-dessous).
1. Remplacez `@Scripts.Render("~/bundles/bootstrap")` par un `<script>` élément (voir ci-dessous).

Balisage de remplacement terminé pour l’inclusion de jQuery et du code d’amorçage JavaScript :

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

Le fichier *_Layout. cshtml* mis à jour est affiché ci-dessous :

[!code-cshtml[](mvc/samples/3.x/Views/Shared/_Layout.cshtml?highlight=7-10,40-42)]

Affichez le site dans le navigateur. Il doit être rendu avec les styles attendus sur place.

## <a name="configure-bundling-and-minification"></a>Configurer le regroupement et la minimisation

ASP.NET Core est compatible avec plusieurs solutions de réduction et de regroupement Open source, telles que [weboptimizer](https://github.com/ligershark/WebOptimizer) et d’autres bibliothèques similaires. ASP.NET Core ne fournit pas de solution de minimisation et de regroupement native. Pour plus d’informations sur la configuration du regroupement et de la minimisation, consultez [regroupement et minimisation](xref:client-side/bundling-and-minification).

## <a name="solve-http-500-errors"></a>Résoudre les erreurs HTTP 500

De nombreux problèmes peuvent provoquer un message d’erreur HTTP 500 qui ne contient aucune information sur la source du problème. Par exemple, si le fichier *views/_ViewImports. cshtml* contient un espace de noms qui n’existe pas dans le projet, une erreur HTTP 500 est générée. Par défaut, dans ASP.NET Core Apps, l' `UseDeveloperExceptionPage` extension est ajoutée au `IApplicationBuilder` et exécutée lorsque l’environnement est en cours de *développement* . Ce code est détaillé dans le code suivant :

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=17-21&name=snippet)]

ASP.NET Core convertit les exceptions non gérées en réponses d’erreur HTTP 500. Normalement, les détails de l’erreur ne sont pas inclus dans ces réponses pour empêcher la divulgation d’informations potentiellement sensibles sur le serveur. Pour plus d’informations, consultez la [page exception du développeur](xref:fundamentals/error-handling#developer-exception-page).

## <a name="next-steps"></a>Étapes suivantes

* <xref:migration/identity>

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Cet article explique comment commencer à migrer un projet ASP.NET MVC vers [ASP.net Core mvc](xref:mvc/overview) 2,2. Dans le processus, il met en évidence un grand nombre des éléments qui ont été modifiés par rapport à ASP.NET MVC. La migration à partir de ASP.NET MVC est un processus en plusieurs étapes. Cet article couvre les points suivants :

* Configuration initiale
* Contrôleurs et vues de base
* Contenu statique
* Dépendances côté client.

Pour la migration de la configuration et du Identity code, consultez <xref:migration/configuration> et <xref:migration/identity> .

> [!NOTE]
> Les numéros de version dans les exemples ne sont peut-être pas à jour, mettez à jour les projets en conséquence.

## <a name="create-the-starter-aspnet-mvc-project"></a>Créer le projet MVC ASP.NET de démarrage

Pour illustrer la mise à niveau, nous allons commencer par créer une application ASP.NET MVC. Créez-le avec le nom *application Web 1* pour que l’espace de noms corresponde au projet de ASP.net Core créé à l’étape suivante.

![Boîte de dialogue Nouveau projet Visual Studio](mvc/_static/new-project.png)

![Boîte de dialogue nouvelle application Web : modèle de projet MVC sélectionné dans le panneau modèles ASP.NET](mvc/_static/new-project-select-mvc-template.png)

*Facultatif :* Remplacez le nom de la solution *application Web 1* par *Mvc5* . Visual Studio affiche le nouveau nom de la solution ( *Mvc5* ), ce qui vous permet d’indiquer plus facilement ce projet à partir du projet suivant.

## <a name="create-the-aspnet-core-project"></a>Créer le projet ASP.NET Core

Créez une application Web ASP.NET Core *vide* portant le même nom que le projet précédent ( *application Web 1* ) de sorte que les espaces de noms des deux projets correspondent. Le fait d’avoir le même espace de noms facilite la copie du code entre les deux projets. Créez ce projet dans un répertoire différent de celui du projet précédent pour utiliser le même nom.

![Boîte de dialogue Nouveau projet](mvc/_static/new_core.png)

![Boîte de dialogue nouvelle application Web ASP.NET : modèle de projet vide sélectionné dans ASP.NET Core panneau modèles](mvc/_static/new-project-select-empty-aspnet5-template.png)

* *Facultatif :* Créez une nouvelle ASP.NET Core application à l’aide du modèle de projet d' *application Web* . Nommez le projet *application Web 1* , puis sélectionnez l’option d’authentification des **comptes d’utilisateur individuels** . Renommez cette application en *FullAspNetCore* . La création de ce projet permet de gagner du temps lors de la conversion. Le résultat final peut être affiché dans le code généré par le modèle, le code peut être copié dans le projet de conversion ou comparé au projet généré par le modèle.

## <a name="configure-the-site-to-use-mvc"></a>Configurer le site pour utiliser MVC

* Quand vous ciblez .NET Core, le [AspNetCore Microsoft.. app](xref:fundamentals/metapackage-app) est référencé par défaut. Ce package contient des packages couramment utilisés par les applications MVC. Si vous ciblez .NET Framework, les références de package doivent être listées individuellement dans le fichier projet.

`Microsoft.AspNetCore.Mvc` est l’infrastructure MVC ASP.NET Core. `Microsoft.AspNetCore.StaticFiles` est le gestionnaire de fichiers statiques. ASP.NET Core applications s’abonnent explicitement à des intergiciels (middleware), comme pour le traitement des fichiers statiques. Pour plus d’informations, consultez [Fichiers statiques](xref:fundamentals/static-files).

* Ouvrez le fichier *Startup.cs* et modifiez le code pour qu’il corresponde à ce qui suit :

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

La <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> méthode d’extension ajoute le gestionnaire de fichiers statiques. Pour plus d’informations, consultez démarrage et [routage](xref:fundamentals/routing)de l' [application](xref:fundamentals/startup) .

## <a name="add-a-controller-and-view"></a>Ajouter un contrôleur et une vue

Dans cette section, vous ajoutez un contrôleur et une vue minimaux pour servir d’espaces réservés pour le contrôleur MVC ASP.NET et les vues migrées dans la section suivante.

* Ajoutez un répertoire *Controllers* .

* Ajoutez une **classe de contrôleur** nommée *HomeController.cs* au répertoire *Controllers* .

![Boîte de dialogue Ajouter un nouvel élément](mvc/_static/add_mvc_ctl.png)

* Ajoutez un répertoire *views* .

* Ajoutez un répertoire *views/racine* .

* Ajoutez un **Razor affichage** nommé *index. cshtml* au répertoire *views/de départ* .

![Boîte de dialogue Ajouter un nouvel élément](mvc/_static/view.png)

La structure du projet est indiquée ci-dessous :

![Explorateur de solutions de l’indication des fichiers et répertoires de application Web 1](mvc/_static/project-structure-controller-view.png)

Remplacez le contenu du fichier *Views/Home/Index.cshtml* par le balisage suivant :

```html
<h1>Hello world!</h1>
```

Exécutez l’application.

![Application Web ouverte dans Microsoft Edge](mvc/_static/hello-world.png)

Pour plus d’informations, consultez [contrôleurs](xref:mvc/controllers/actions) et [vues](xref:mvc/views/overview).

Les fonctionnalités suivantes requièrent la migration à partir de l’exemple de projet MVC ASP.NET vers le projet ASP.NET Core :

* contenu côté client (CSS, polices et scripts)

* controllers

* views

* modèles

* regroupement

* filtres

* Connectez-vous, Identity (cette opération est effectuée dans le didacticiel suivant).

## <a name="controllers-and-views"></a>Contrôleurs et vues

* Copiez chacune des méthodes du MVC ASP.NET `HomeController` vers le nouveau `HomeController` . Dans ASP.NET MVC, le type de retour de la méthode d’action du contrôleur intégré au modèle est <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> ; dans ASP.net Core MVC, les méthodes d’action retournent à la `IActionResult` place. `ActionResult` implémente `IActionResult` , il n’est donc pas nécessaire de modifier le type de retour des méthodes d’action.

* Copiez les fichiers de vue *about. cshtml* , *contact. cshtml* et *index. cshtml* Razor du projet MVC ASP.net dans le projet ASP.net core.

## <a name="test-each-method"></a>Tester chaque méthode

Le fichier de disposition et les styles n’ont pas encore été migrés, donc les vues rendues contiennent uniquement le contenu des fichiers de vue. Les liens générés par le fichier de disposition pour les `About` vues et ne sont `Contact` pas encore disponibles.

Appelez les vues rendues à partir du navigateur sur l’application ASP.NET Core en cours d’exécution en remplaçant le numéro de port actuel par le numéro de port utilisé dans le projet ASP.NET Core. Par exemple : `https://localhost:44375/home/about`.

![Page de contact](mvc/_static/contact-page.png)

Notez l’absence de styles et d’éléments de menu. Le style sera corrigé dans la section suivante.

## <a name="static-content"></a>Contenu statique

Dans ASP.NET MVC 5 et versions antérieures, le contenu statique était hébergé à partir de la racine du projet Web et a été mélangé aux fichiers côté serveur. Dans ASP.NET Core, le contenu statique est hébergé dans le répertoire *wwwroot* . Copiez le contenu statique de l’application ASP.NET MVC dans le répertoire *wwwroot* dans le projet ASP.net core. Dans cet exemple de conversion :

* Copiez le fichier *favicon. ico* à partir du projet MVC ASP.net dans le répertoire *wwwroot* dans le projet ASP.net core.

Le projet MVC ASP.NET utilise [bootstrap](https://getbootstrap.com/) pour son style et stocke les fichiers de démarrage dans les répertoires de *contenu* et de *scripts* . Le modèle, qui a généré le projet MVC ASP.NET, référence le bootstrap dans le fichier de disposition ( *Views/Shared/_Layout. cshtml* ). Les fichiers *bootstrap.js* et *bootstrap. CSS* peuvent être copiés du projet MVC ASP.net vers le répertoire *wwwroot* dans le nouveau projet. Au lieu de cela, ce document ajoute la prise en charge des démarrages (et d’autres bibliothèques côté client) à l’aide de CDN, dans la section suivante.

## <a name="migrate-the-layout-file"></a>Migrer le fichier de disposition

* Copiez le fichier *_ViewStart. cshtml* à partir du répertoire *views* du projet MVC ASP.net dans le répertoire *views* du projet ASP.net core. Le fichier *_ViewStart. cshtml* n’a pas été modifié dans ASP.net Core Mvc.

* Créez un répertoire *Views/Shared* .

* *Facultatif :* Copiez *_ViewImports. cshtml* du répertoire *views* du projet MVC *FullAspNetCore* dans le répertoire *views* du projet ASP.net core. Supprimez toute déclaration d’espace de noms dans le fichier *_ViewImports. cshtml* . Le fichier *_ViewImports. cshtml* fournit des espaces de noms pour tous les fichiers de vue et insère les [balises](xref:mvc/views/tag-helpers/intro). Les tag helpers sont utilisés dans le nouveau fichier de disposition. Le fichier *_ViewImports. cshtml* est nouveau pour ASP.net core.

* Copiez le fichier *_Layout. cshtml* à partir du répertoire *Views/Shared* du projet MVC ASP.net dans le répertoire *Views/Shared* du projet ASP.net core.

Ouvrez *_Layout fichier. cshtml* et apportez les modifications suivantes (le code complet est indiqué ci-dessous) :

* Remplacez `@Styles.Render("~/Content/css")` par un `<link>` élément pour charger *bootstrap. CSS* (voir ci-dessous).

* Supprimez `@Scripts.Render("~/bundles/modernizr")`.

* Commentez la `@Html.Partial("_LoginPartial")` ligne (entourez la ligne de `@*...*@` ). Pour plus d’informations, consultez [migrer l’authentification et Identity vers ASP.net Core](xref:migration/identity)

* Remplacez `@Scripts.Render("~/bundles/jquery")` par un `<script>` élément (voir ci-dessous).

* Remplacez `@Scripts.Render("~/bundles/bootstrap")` par un `<script>` élément (voir ci-dessous).

Balisage de remplacement pour l’inclusion CSS de démarrage :

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

Balisage de remplacement pour l’inclusion de jQuery et du code d’amorçage JavaScript :

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

Le fichier *_Layout. cshtml* mis à jour est affiché ci-dessous :

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

Affichez le site dans le navigateur. Elle doit maintenant se charger correctement, avec les styles attendus en place.

* *Facultatif :* Essayez d’utiliser le nouveau fichier de disposition. Copiez le fichier de disposition à partir du projet *FullAspNetCore* . Le nouveau fichier de disposition utilise [tag Helper](xref:mvc/views/tag-helpers/intro) et présente d’autres améliorations.

## <a name="configure-bundling-and-minification"></a>Configurer le regroupement et la minimisation

Pour plus d’informations sur la configuration du regroupement et de la minimisation, consultez [regroupement et minimisation](xref:client-side/bundling-and-minification).

## <a name="solve-http-500-errors"></a>Résoudre les erreurs HTTP 500

De nombreux problèmes peuvent provoquer des messages d’erreur HTTP 500 qui ne contiennent aucune information sur la source du problème. Par exemple, si le fichier *views/_ViewImports. cshtml* contient un espace de noms qui n’existe pas dans le projet, une erreur HTTP 500 est générée. Par défaut, dans ASP.NET Core Apps, l' `UseDeveloperExceptionPage` extension est ajoutée au `IApplicationBuilder` et exécutée lorsque la configuration est en cours de *développement* . Consultez un exemple dans le code suivant :

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

ASP.NET Core convertit les exceptions non gérées en réponses d’erreur HTTP 500. Normalement, les détails de l’erreur ne sont pas inclus dans ces réponses pour empêcher la divulgation d’informations potentiellement sensibles sur le serveur. Pour plus d’informations, consultez la [page exception du développeur](xref:fundamentals/error-handling#developer-exception-page).

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

Cet article explique comment commencer à migrer un projet ASP.NET MVC vers [ASP.net Core mvc](xref:mvc/overview) 2,1. Dans le processus, il met en évidence un grand nombre des éléments qui ont été modifiés par rapport à ASP.NET MVC. La migration à partir de ASP.NET MVC est un processus en plusieurs étapes. Cet article couvre les points suivants :

* Configuration initiale
* Contrôleurs et vues de base
* Contenu statique
* Dépendances côté client.

Pour la migration de la configuration et du Identity code, consultez [migrer la configuration vers ASP.net Core](xref:migration/configuration) et [migrer l’authentification et Identity vers ASP.net Core](xref:migration/identity).

> [!NOTE]
> Les numéros de version dans les exemples ne sont peut-être pas à jour, mettez à jour les projets en conséquence.

## <a name="create-the-starter-aspnet-mvc-project"></a>Créer le projet MVC ASP.NET de démarrage

Pour illustrer la mise à niveau, nous allons commencer par créer une application ASP.NET MVC. Créez-le avec le nom *application Web 1* pour que l’espace de noms corresponde au projet de ASP.net Core créé à l’étape suivante.

![Boîte de dialogue Nouveau projet Visual Studio](mvc/_static/new-project.png)

![Boîte de dialogue nouvelle application Web : modèle de projet MVC sélectionné dans le panneau modèles ASP.NET](mvc/_static/new-project-select-mvc-template.png)

*Facultatif :* Remplacez le nom de la solution *application Web 1* par *Mvc5* . Visual Studio affiche le nouveau nom de la solution ( *Mvc5* ), ce qui vous permet d’indiquer plus facilement ce projet à partir du projet suivant.

## <a name="create-the-aspnet-core-project"></a>Créer le projet ASP.NET Core

Créez une application Web ASP.NET Core *vide* portant le même nom que le projet précédent ( *application Web 1* ) de sorte que les espaces de noms des deux projets correspondent. Le fait d’avoir le même espace de noms facilite la copie du code entre les deux projets. Créez ce projet dans un répertoire différent de celui du projet précédent pour utiliser le même nom.

![Boîte de dialogue Nouveau projet](mvc/_static/new_core.png)

![Boîte de dialogue nouvelle application Web ASP.NET : modèle de projet vide sélectionné dans ASP.NET Core panneau modèles](mvc/_static/new-project-select-empty-aspnet5-template.png)

* *Facultatif :* Créez une nouvelle ASP.NET Core application à l’aide du modèle de projet d' *application Web* . Nommez le projet *application Web 1* , puis sélectionnez l’option d’authentification des **comptes d’utilisateur individuels** . Renommez cette application en *FullAspNetCore* . La création de ce projet permet de gagner du temps lors de la conversion. Le résultat final peut être affiché dans le code généré par le modèle, le code peut être copié dans le projet de conversion ou comparé au projet généré par le modèle.

## <a name="configure-the-site-to-use-mvc"></a>Configurer le site pour utiliser MVC

* Quand vous ciblez .NET Core, le [AspNetCore Microsoft.. app](xref:fundamentals/metapackage-app) est référencé par défaut. Ce package contient des packages couramment utilisés par les applications MVC. Si vous ciblez .NET Framework, les références de package doivent être listées individuellement dans le fichier projet.

`Microsoft.AspNetCore.Mvc` est l’infrastructure MVC ASP.NET Core. `Microsoft.AspNetCore.StaticFiles` est le gestionnaire de fichiers statiques. ASP.NET Core applications s’abonnent explicitement à des intergiciels (middleware), comme pour le traitement des fichiers statiques. Pour plus d’informations, consultez [Fichiers statiques](xref:fundamentals/static-files).

* Ouvrez le fichier *Startup.cs* et modifiez le code pour qu’il corresponde à ce qui suit :

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

La <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> méthode d’extension ajoute le gestionnaire de fichiers statiques. La `UseMvc` méthode d’extension ajoute le routage. Pour plus d’informations, consultez démarrage et [routage](xref:fundamentals/routing)de l' [application](xref:fundamentals/startup) .

## <a name="add-a-controller-and-view"></a>Ajouter un contrôleur et une vue

Dans cette section, vous ajoutez un contrôleur et une vue minimaux pour servir d’espaces réservés pour le contrôleur MVC ASP.NET et les vues migrées dans la section suivante.

* Ajoutez un répertoire *Controllers* .

* Ajoutez une **classe de contrôleur** nommée *HomeController.cs* au répertoire *Controllers* .

![Boîte de dialogue Ajouter un nouvel élément](mvc/_static/add_mvc_ctl.png)

* Ajoutez un répertoire *views* .

* Ajoutez un répertoire *views/racine* .

* Ajoutez un **Razor affichage** nommé *index. cshtml* au répertoire *views/de départ* .

![Boîte de dialogue Ajouter un nouvel élément](mvc/_static/view.png)

La structure du projet est indiquée ci-dessous :

![Explorateur de solutions de l’indication des fichiers et répertoires de application Web 1](mvc/_static/project-structure-controller-view.png)

Remplacez le contenu du fichier *Views/Home/Index.cshtml* par le balisage suivant :

```html
<h1>Hello world!</h1>
```

Exécutez l’application.

![Application Web ouverte dans Microsoft Edge](mvc/_static/hello-world.png)

Pour plus d’informations, consultez [contrôleurs](xref:mvc/controllers/actions) et [vues](xref:mvc/views/overview).

Les fonctionnalités suivantes requièrent la migration à partir de l’exemple de projet MVC ASP.NET vers le projet ASP.NET Core :

* contenu côté client (CSS, polices et scripts)

* controllers

* views

* modèles

* regroupement

* filtres

* Connectez-vous, Identity (cette opération est effectuée dans le didacticiel suivant).

## <a name="controllers-and-views"></a>Contrôleurs et vues

* Copiez chacune des méthodes du MVC ASP.NET `HomeController` vers le nouveau `HomeController` . Dans ASP.NET MVC, le type de retour de la méthode d’action du contrôleur intégré au modèle est <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> ; dans ASP.net Core MVC, les méthodes d’action retournent à la `IActionResult` place. `ActionResult` implémente `IActionResult` , il n’est donc pas nécessaire de modifier le type de retour des méthodes d’action.

* Copiez les fichiers de vue *about. cshtml* , *contact. cshtml* et *index. cshtml* Razor du projet MVC ASP.net dans le projet ASP.net core.

## <a name="test-each-method"></a>Tester chaque méthode

Le fichier de disposition et les styles n’ont pas encore été migrés, donc les vues rendues contiennent uniquement le contenu des fichiers de vue. Les liens générés par le fichier de disposition pour les `About` vues et ne sont `Contact` pas encore disponibles.

* Appelez les vues rendues à partir du navigateur sur l’application ASP.NET Core en cours d’exécution en remplaçant le numéro de port actuel par le numéro de port utilisé dans le projet ASP.NET Core. Par exemple : `https://localhost:44375/home/about`.

![Page de contact](mvc/_static/contact-page.png)

Notez l’absence de styles et d’éléments de menu. Le style sera corrigé dans la section suivante.

## <a name="static-content"></a>Contenu statique

Dans ASP.NET MVC 5 et versions antérieures, le contenu statique était hébergé à partir de la racine du projet Web et a été mélangé aux fichiers côté serveur. Dans ASP.NET Core, le contenu statique est hébergé dans le répertoire *wwwroot* . Copiez le contenu statique de l’application ASP.NET MVC dans le répertoire *wwwroot* dans le projet ASP.net core. Dans cet exemple de conversion :

* Copiez le fichier *favicon. ico* à partir du projet MVC ASP.net dans le répertoire *wwwroot* dans le projet ASP.net core.

Le projet MVC ASP.NET utilise [bootstrap](https://getbootstrap.com/) pour son style et stocke les fichiers de démarrage dans les répertoires de *contenu* et de *scripts* . Le modèle, qui a généré le projet MVC ASP.NET, référence le bootstrap dans le fichier de disposition ( *Views/Shared/_Layout. cshtml* ). Les fichiers *bootstrap.js* et *bootstrap. CSS* peuvent être copiés du projet MVC ASP.net vers le répertoire *wwwroot* dans le nouveau projet. Au lieu de cela, ce document ajoute la prise en charge des démarrages (et d’autres bibliothèques côté client) à l’aide de CDN, dans la section suivante.

## <a name="migrate-the-layout-file"></a>Migrer le fichier de disposition

* Copiez le fichier *_ViewStart. cshtml* à partir du répertoire *views* du projet MVC ASP.net dans le répertoire *views* du projet ASP.net core. Le fichier *_ViewStart. cshtml* n’a pas été modifié dans ASP.net Core Mvc.

* Créez un répertoire *Views/Shared* .

* *Facultatif :* Copiez *_ViewImports. cshtml* du répertoire *views* du projet MVC *FullAspNetCore* dans le répertoire *views* du projet ASP.net core. Supprimez toute déclaration d’espace de noms dans le fichier *_ViewImports. cshtml* . Le fichier *_ViewImports. cshtml* fournit des espaces de noms pour tous les fichiers de vue et insère les [balises](xref:mvc/views/tag-helpers/intro). Les tag helpers sont utilisés dans le nouveau fichier de disposition. Le fichier *_ViewImports. cshtml* est nouveau pour ASP.net core.

* Copiez le fichier *_Layout. cshtml* à partir du répertoire *Views/Shared* du projet MVC ASP.net dans le répertoire *Views/Shared* du projet ASP.net core.

Ouvrez *_Layout fichier. cshtml* et apportez les modifications suivantes (le code complet est indiqué ci-dessous) :

* Remplacez `@Styles.Render("~/Content/css")` par un `<link>` élément pour charger *bootstrap. CSS* (voir ci-dessous).

* Supprimez `@Scripts.Render("~/bundles/modernizr")`.

* Commentez la `@Html.Partial("_LoginPartial")` ligne (entourez la ligne de `@*...*@` ). Pour plus d’informations, consultez [migrer l’authentification et Identity vers ASP.net Core](xref:migration/identity)

* Remplacez `@Scripts.Render("~/bundles/jquery")` par un `<script>` élément (voir ci-dessous).

* Remplacez `@Scripts.Render("~/bundles/bootstrap")` par un `<script>` élément (voir ci-dessous).

Balisage de remplacement pour l’inclusion CSS de démarrage :

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

Balisage de remplacement pour l’inclusion de jQuery et du code d’amorçage JavaScript :

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

Le fichier *_Layout. cshtml* mis à jour est affiché ci-dessous :

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

Affichez le site dans le navigateur. Elle doit maintenant se charger correctement, avec les styles attendus en place.

* *Facultatif :* Essayez d’utiliser le nouveau fichier de disposition. Copiez le fichier de disposition à partir du projet *FullAspNetCore* . Le nouveau fichier de disposition utilise [tag Helper](xref:mvc/views/tag-helpers/intro) et présente d’autres améliorations.

## <a name="configure-bundling-and-minification"></a>Configurer le regroupement et la minimisation

Pour plus d’informations sur la configuration du regroupement et de la minimisation, consultez [regroupement et minimisation](xref:client-side/bundling-and-minification).

## <a name="solve-http-500-errors"></a>Résoudre les erreurs HTTP 500

De nombreux problèmes peuvent provoquer des messages d’erreur HTTP 500 qui ne contiennent aucune information sur la source du problème. Par exemple, si le fichier *views/_ViewImports. cshtml* contient un espace de noms qui n’existe pas dans le projet, une erreur HTTP 500 est générée. Par défaut, dans ASP.NET Core Apps, l' `UseDeveloperExceptionPage` extension est ajoutée au `IApplicationBuilder` et exécutée lorsque la configuration est en cours de *développement* . Consultez un exemple dans le code suivant :

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

ASP.NET Core convertit les exceptions non gérées en réponses d’erreur HTTP 500. Normalement, les détails de l’erreur ne sont pas inclus dans ces réponses pour empêcher la divulgation d’informations potentiellement sensibles sur le serveur. Pour plus d’informations, consultez la [page exception du développeur](xref:fundamentals/error-handling#developer-exception-page).

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end
