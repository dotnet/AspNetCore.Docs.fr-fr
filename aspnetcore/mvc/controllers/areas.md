---
title: Zones dans ASP.NET Core
author: rick-anderson
description: Découvrez les zones, fonctionnalité d’ASP.NET MVC utilisée pour organiser des fonctionnalités connexes dans un groupe sous la forme d’un espace de noms distinct (pour le routage) et d’une structure de dossiers (pour les vues).
ms.author: riande
ms.date: 03/21/2019
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
uid: mvc/controllers/areas
ms.openlocfilehash: f3d76f612e67aeabf1f7fef694199332c732c593
ms.sourcegitcommit: 53e01d6e9b70a18a05618f0011cf115a16633c21
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97878410"
---
# <a name="areas-in-aspnet-core"></a>Zones dans ASP.NET Core

Par [dhananjay Kumar](https://twitter.com/debug_mode) et [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Les zones sont une fonctionnalité ASP.NET qui permet d’organiser les fonctionnalités associées dans un groupe séparément :

* Espace de noms pour le routage.
* Structure de dossiers pour les affichages et les Razor pages.

L’utilisation de zones crée une hiérarchie à des fins de routage en ajoutant un autre paramètre de route, `area` , à `controller` et à `action` une Razor page `page` .

Les zones permettent de partitionner une application Web ASP.NET Core en groupes fonctionnels plus petits, chacun avec son propre ensemble de Razor pages, de contrôleurs, de vues et de modèles. Une zone est en réalité une structure au sein d’une application. Dans un projet web ASP.NET Core, les composants logiques comme Pages, Controller et View sont conservés dans différents dossiers. Le runtime ASP.NET Core utilise des conventions de nommage pour créer la relation entre ces composants. Pour une application volumineuse, il peut être avantageux de partitionner l’application en différentes zones de fonctionnalités de premier niveau. Par exemple, pour une application d’e-commerce à plusieurs entités, il peut s’agir de la caisse, de la facturation et de la recherche. Chacune de ces unités a leur propre zone pour contenir des vues, des contrôleurs, des Razor pages et des modèles.

Envisagez d’utiliser des zones dans un projet quand :

* L’application est constituée de plusieurs composants fonctionnels globaux qui peuvent être logiquement séparés.
* Vous pouvez partitionner l’application de façon à pouvoir travailler indépendamment sur chaque zone fonctionnelle.

[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample)). L’exemple de code téléchargeable fournit une application de base pour tester les zones.

Si vous utilisez des Razor pages, consultez [zones avec Razor pages](#areas-with-razor-pages) dans ce document.

## <a name="areas-for-controllers-with-views"></a>Zones pour contrôleurs avec vues

Une application web ASP.NET Core type qui utilise des zones, des contrôleurs et des vues est constituée des éléments suivants :

* Une [structure de dossiers Zone](#area-folder-structure).
* Contrôleurs avec l' [`[Area]`](#attribute) attribut pour associer le contrôleur à la zone :

  [!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* [Itinéraire de zone ajouté au démarrage](#add-area-route):

  [!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a>Structure de dossiers Zone

Imaginez une application qui contient deux groupes logiques, *Produits* et *Services*. En utilisant des zones, la structure de dossiers se présenterait comme suit :

* Nom du projet
  * Régions
    * Produits
      * Controllers
        * HomeController.cs
        * ManageController.cs
      * Les vues
        * Accueil
          * Index.cshtml
        * Gérer
          * Index.cshtml
          * About.cshtml
    * Services
      * Controllers
        * HomeController.cs
      * Les vues
        * Accueil
          * Index.cshtml

Si la disposition précédente est classique dans les cas où des zones sont utilisées, seuls les fichiers de vues sont nécessaires pour utiliser cette structure de dossiers. La détection de vues recherche un fichier de vue de zone correspondant dans l’ordre suivant :

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a>Associer le contrôleur à une zone

Les contrôleurs de zone sont désignés avec l’attribut [ &lbrack; Area &rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) :

[!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a>Ajouter une route de zone

Les itinéraires de zone utilisent généralement le  [routage conventionnel](xref:mvc/controllers/routing#cr) plutôt que le [routage d’attributs](xref:mvc/controllers/routing#ar). Le routage conventionnel est dépendant de l’ordre. En général, les routes avec des zones doivent être placées plus haut dans la table de routage, car elles sont plus spécifiques que les routes sans zone.

`{area:...}` peut être utilisé comme jeton dans les modèles de route si l’espace d’url est uniforme dans toutes les zones :

[!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet&highlight=21-23)]

Dans le code précédent, `exists` applique une contrainte qui impose à la route de correspondre à une zone. Utilisation `{area:...}` de avec `MapControllerRoute` :

* Est le mécanisme le moins compliqué pour ajouter le routage à des zones.
* Correspond à tous les contrôleurs avec l' `[Area("Area name")]` attribut.

Le code suivant utilise <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> pour créer deux routes de zone nommées :

[!code-csharp[](areas/31samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=21-29)]

Pour plus d’informations, consultez [Routage de zones](xref:mvc/controllers/routing#areas).

### <a name="link-generation-with-mvc-areas"></a>Génération de liens avec zones MVC

Le code suivant tiré de l’[exemple de code téléchargeable](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) illustre une génération de liens avec la zone spécifiée :

[!code-cshtml[](areas/31samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

L’exemple de téléchargement inclut une [vue partielle](xref:mvc/views/partial) qui contient les éléments suivants :

* Liens précédents.
* Les liens similaires à l’exception précédente `area` ne sont pas spécifiés.

La vue partielle étant référencée dans le [fichier de disposition](xref:mvc/views/layout), chaque page de l’application affiche les liens générés. Les liens générés sans spécification de la zone sont valides uniquement quand ils sont référencés dans une page contenue dans la même zone et le même contrôleur.

Quand la zone ou le contrôleur n’est pas spécifié, le routage dépend des valeurs [ambiantes](xref:mvc/controllers/routing#ambient). Les valeurs de route actuelles de la requête actuelle sont considérées comme des valeurs ambiantes pour la génération de liens. Dans de nombreux cas pour l’exemple d’application, l’utilisation des valeurs ambiantes génère des liens incorrects avec le balisage qui ne spécifie pas la zone.

Pour plus d’informations, consultez [Routage vers des actions de contrôleur](xref:mvc/controllers/routing).

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a>Disposition partagée pour les zones en utilisant le fichier _ViewStart.cshtml

Pour partager une disposition commune pour l’ensemble de l’application, conservez le *_ViewStart. cshtml* dans le [dossier racine](#arf)de l’application. Pour plus d'informations, consultez <xref:mvc/views/layout>

<a name="arf"></a>

### <a name="application-root-folder"></a>Dossier racine de l’application

Le dossier racine de l’application est le dossier qui contient *Startup.cs* dans l’application Web créée avec les modèles ASP.net core.

### <a name="_viewimportscshtml"></a>_ViewImports.cshtml

 */Views/_ViewImports. cshtml*, pour MVC et */pages/_ViewImports. cshtml* pour les Razor pages, n’est pas importé dans les vues dans les zones. Utilisez l’une des approches suivantes pour fournir des importations d’affichage à tous les affichages :

* Ajoutez *_ViewImports. cshtml* au [dossier racine](#arf)de l’application. Un *_ViewImports. cshtml* dans le dossier racine de l’application s’applique à toutes les vues de l’application.
* Copiez le fichier *_ViewImports. cshtml* dans le dossier d’affichage approprié sous zones.

Le fichier *_ViewImports. cshtml* [contient généralement](xref:mvc/views/tag-helpers/intro) les instructions Imports, `@using` et `@inject` . Pour plus d’informations, consultez [importation de directives partagées](xref:mvc/views/layout#importing-shared-directives).

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a>Changer le dossier de zone par défaut où sont stockées les vues

Le code suivant remplace le dossier de zone par défaut `"Areas"` par `"MyAreas"` :

[!code-csharp[](areas/31samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-no-locrazor-pages"></a>Zones avec Razor pages

Les zones avec Razor pages requièrent un `Areas/<area name>/Pages` dossier à la racine de l’application. La structure de dossiers suivante est utilisée avec [l’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) :

* Nom du projet
  * Régions
    * Produits
      * Pages
        * _ViewImports
        * À propos de
        * Index
    * Services
      * Pages
        * Gérer
          * À propos de
          * Index

### <a name="link-generation-with-no-locrazor-pages-and-areas"></a>Génération de liens avec des Razor pages et des zones

Le code suivant tiré de [l’exemple de code téléchargeable](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) illustre une génération de liens avec la zone spécifiée (par exemple, `asp-area="Products"`) :

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

L’exemple de code téléchargeable comprend une [vue partielle](xref:mvc/views/partial) qui contient les liens précédents et les mêmes liens sans spécification de la zone. La vue partielle étant référencée dans le [fichier de disposition](xref:mvc/views/layout), chaque page de l’application affiche les liens générés. Les liens générés sans spécification de la zone sont valides uniquement quand ils sont référencés dans une page contenue dans la même zone.

Quand la zone n’est pas spécifiée, le routage dépend des valeurs *ambiantes*. Les valeurs de route actuelles de la requête actuelle sont considérées comme des valeurs ambiantes pour la génération de liens. Dans de nombreux cas, pour l’exemple d’application, l’utilisation des valeurs ambiantes génère des liens incorrects. Prenons l’exemple des liens générés à partir de l’extrait de code suivant :

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

Pour le code précédent :

* Le lien généré à partir de `<a asp-page="/Manage/About">` est correct uniquement lorsque la dernière requête concernait une page dans la zone `Services`. Par exemple, `/Services/Manage/`, `/Services/Manage/Index` ou `/Services/Manage/About`.
* Le lien généré à partir de `<a asp-page="/About">` est correct uniquement lorsque la dernière requête concernait une page dans `/Home`.
* Le code est issu du [téléchargement de l’exemple](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a>Importer l’espace de noms et les Tag Helpers avec le fichier _ViewImports

Un fichier *_ViewImports. cshtml* peut être ajouté à chaque dossier des *pages* de zone pour importer l’espace de noms et les tag helpers sur chaque Razor page du dossier.

Considérez la zone *Services* de l’exemple de code, qui ne contient pas de fichier *_ViewImports.cshtml*. La balise suivante affiche la page */services/Manage/about* Razor :

[!code-cshtml[](areas/31samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

Dans le balisage précédent :

* Le nom de classe complet doit être utilisé pour spécifier le modèle ( `@model RPareas.Areas.Services.Pages.Manage.AboutModel` ).
* Les [Tag Helpers](xref:mvc/views/tag-helpers/intro) sont activés par `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`

Dans l’exemple de téléchargement, la zone Products contient le fichier *_ViewImports.cshtml* suivant :

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

La balise suivante affiche la page */Products/about* Razor :

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/About.cshtml)]

Dans le fichier précédent, l’espace de noms et la `@addTagHelper` directive sont importés dans le fichier par le fichier *Areas/Products/Pages/_ViewImports. cshtml* .

Pour plus d’informations, consultez [Gestion de l’étendue des Tag Helpers](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) et [Importation de directives partagées](xref:mvc/views/layout#importing-shared-directives).

### <a name="shared-layout-for-no-locrazor-pages-areas"></a>Disposition partagée pour les Razor zones de pages

Pour partager une disposition commune pour l’ensemble de l’application, déplacez *_ViewStart.cshtml* dans le dossier racine de l’application.

### <a name="publishing-areas"></a>Zones de publication

Tous les fichiers *.cshtml et autres fichiers dans le répertoire *wwwroot* sont publiés en sortie quand `<Project Sdk="Microsoft.NET.Sdk.Web">` est inclus dans le fichier *.csproj.
::: moniker-end

::: moniker range="< aspnetcore-3.0"

Les zones sont une fonctionnalité d’ASP.NET qui servent à organiser les fonctionnalités associées dans un groupe sous la forme d’un espace de noms (pour le routage) et d’une structure de dossiers (pour les vues) distincts. L’utilisation de zones crée une hiérarchie à des fins de routage en ajoutant un autre paramètre de route, `area` , à `controller` et à `action` une Razor page `page` .

Les zones permettent de partitionner une application Web ASP.NET Core en groupes fonctionnels plus petits, chacun avec son propre ensemble de Razor pages, de contrôleurs, de vues et de modèles. Une zone est en réalité une structure au sein d’une application. Dans un projet web ASP.NET Core, les composants logiques comme Pages, Controller et View sont conservés dans différents dossiers. Le runtime ASP.NET Core utilise des conventions de nommage pour créer la relation entre ces composants. Pour une application volumineuse, il peut être avantageux de partitionner l’application en différentes zones de fonctionnalités de premier niveau. Par exemple, pour une application d’e-commerce à plusieurs entités, il peut s’agir de la caisse, de la facturation et de la recherche. Chacune de ces unités a leur propre zone pour contenir des vues, des contrôleurs, des Razor pages et des modèles.

Envisagez d’utiliser des zones dans un projet quand :

* L’application est constituée de plusieurs composants fonctionnels globaux qui peuvent être logiquement séparés.
* Vous pouvez partitionner l’application de façon à pouvoir travailler indépendamment sur chaque zone fonctionnelle.

[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample)). L’exemple de code téléchargeable fournit une application de base pour tester les zones.

Si vous utilisez des Razor pages, consultez [zones avec Razor pages](#areas-with-razor-pages) dans ce document.

## <a name="areas-for-controllers-with-views"></a>Zones pour contrôleurs avec vues

Une application web ASP.NET Core type qui utilise des zones, des contrôleurs et des vues est constituée des éléments suivants :

* Une [structure de dossiers Zone](#area-folder-structure).
* Contrôleurs avec l' [`[Area]`](#attribute) attribut pour associer le contrôleur à la zone :

  [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* [Itinéraire de zone ajouté au démarrage](#add-area-route):

  [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a>Structure de dossiers Zone

Imaginez une application qui contient deux groupes logiques, *Produits* et *Services*. En utilisant des zones, la structure de dossiers se présenterait comme suit :

* Nom du projet
  * Régions
    * Produits
      * Controllers
        * HomeController.cs
        * ManageController.cs
      * Les vues
        * Accueil
          * Index.cshtml
        * Gérer
          * Index.cshtml
          * About.cshtml
    * Services
      * Controllers
        * HomeController.cs
      * Les vues
        * Accueil
          * Index.cshtml

Si la disposition précédente est classique dans les cas où des zones sont utilisées, seuls les fichiers de vues sont nécessaires pour utiliser cette structure de dossiers. La détection de vues recherche un fichier de vue de zone correspondant dans l’ordre suivant :

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a>Associer le contrôleur à une zone

Les contrôleurs de zone sont désignés avec l’attribut [ &lbrack; Area &rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) :

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a>Ajouter une route de zone

Les routes de zones utilisent généralement un routage conventionnel plutôt qu’un routage par attributs. Le routage conventionnel est dépendant de l’ordre. En général, les routes avec des zones doivent être placées plus haut dans la table de routage, car elles sont plus spécifiques que les routes sans zone.

`{area:...}` peut être utilisé comme jeton dans les modèles de route si l’espace d’url est uniforme dans toutes les zones :

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

Dans le code précédent, `exists` applique une contrainte qui impose à la route de correspondre à une zone. Pour ajouter un routage à des zones, le mécanisme le moins compliqué consiste à utiliser `{area:...}`.

Le code suivant utilise <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> pour créer deux routes de zone nommées :

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

Si vous utilisez `MapAreaRoute` avec ASP.NET Core 2.2, prenez connaissance de [ce problème sur GitHub](https://github.com/dotnet/AspNetCore/issues/7772).

Pour plus d’informations, consultez [Routage de zones](xref:mvc/controllers/routing#areas).

### <a name="link-generation-with-mvc-areas"></a>Génération de liens avec zones MVC

Le code suivant tiré de l’[exemple de code téléchargeable](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) illustre une génération de liens avec la zone spécifiée :

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

Les liens générés par le code précédent sont valides où que ce soit dans l’application.

L’exemple de code téléchargeable comprend une [vue partielle](xref:mvc/views/partial) qui contient les liens précédents et les mêmes liens sans spécification de la zone. La vue partielle étant référencée dans le [fichier de disposition](xref:mvc/views/layout), chaque page de l’application affiche les liens générés. Les liens générés sans spécification de la zone sont valides uniquement quand ils sont référencés dans une page contenue dans la même zone et le même contrôleur.

Quand la zone ou le contrôleur n’est pas spécifié, le routage dépend des valeurs *ambiantes*. Les valeurs de route actuelles de la requête actuelle sont considérées comme des valeurs ambiantes pour la génération de liens. Dans de nombreux cas, pour l’exemple d’application, l’utilisation des valeurs ambiantes génère des liens incorrects.

Pour plus d’informations, consultez [Routage vers des actions de contrôleur](xref:mvc/controllers/routing).

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a>Disposition partagée pour les zones en utilisant le fichier _ViewStart.cshtml

Pour partager une disposition commune pour l’ensemble de l’application, déplacez *_ViewStart.cshtml* dans le dossier racine de l’application.

### <a name="_viewimportscshtml"></a>_ViewImports.cshtml

Dans son emplacement standard, */Views/_ViewImports.cshtml* ne s’applique pas aux zones. Pour utiliser les [balises](xref:mvc/views/tag-helpers/intro)courantes, `@using` ou `@inject` dans votre zone, assurez-vous qu’un fichier *_ViewImports. cshtml* approprié [s’applique à vos vues de zone](xref:mvc/views/layout#importing-shared-directives). Si vous souhaitez obtenir le même comportement dans toutes vos vues, déplacez */Views/_ViewImports.cshtml* vers la racine de l’application.

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a>Changer le dossier de zone par défaut où sont stockées les vues

Le code suivant remplace le dossier de zone par défaut `"Areas"` par `"MyAreas"` :

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-no-locrazor-pages"></a>Zones avec Razor pages

Les zones avec Razor pages requièrent un `Areas/<area name>/Pages` dossier à la racine de l’application. La structure de dossiers suivante est utilisée avec [l’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) :

* Nom du projet
  * Régions
    * Produits
      * Pages
        * _ViewImports
        * À propos de
        * Index
    * Services
      * Pages
        * Gérer
          * À propos de
          * Index

### <a name="link-generation-with-no-locrazor-pages-and-areas"></a>Génération de liens avec des Razor pages et des zones

Le code suivant tiré de [l’exemple de code téléchargeable](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) illustre une génération de liens avec la zone spécifiée (par exemple, `asp-area="Products"`) :

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

Les liens générés par le code précédent sont valides où que ce soit dans l’application.

L’exemple de code téléchargeable comprend une [vue partielle](xref:mvc/views/partial) qui contient les liens précédents et les mêmes liens sans spécification de la zone. La vue partielle étant référencée dans le [fichier de disposition](xref:mvc/views/layout), chaque page de l’application affiche les liens générés. Les liens générés sans spécification de la zone sont valides uniquement quand ils sont référencés dans une page contenue dans la même zone.

Quand la zone n’est pas spécifiée, le routage dépend des valeurs *ambiantes*. Les valeurs de route actuelles de la requête actuelle sont considérées comme des valeurs ambiantes pour la génération de liens. Dans de nombreux cas, pour l’exemple d’application, l’utilisation des valeurs ambiantes génère des liens incorrects. Prenons l’exemple des liens générés à partir de l’extrait de code suivant :

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

Pour le code précédent :

* Le lien généré à partir de `<a asp-page="/Manage/About">` est correct uniquement lorsque la dernière requête concernait une page dans la zone `Services`. Par exemple, `/Services/Manage/`, `/Services/Manage/Index` ou `/Services/Manage/About`.
* Le lien généré à partir de `<a asp-page="/About">` est correct uniquement lorsque la dernière requête concernait une page dans `/Home`.
* Le code est issu du [téléchargement de l’exemple](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a>Importer l’espace de noms et les Tag Helpers avec le fichier _ViewImports

Un fichier *_ViewImports. cshtml* peut être ajouté à chaque dossier des *pages* de zone pour importer l’espace de noms et les tag helpers sur chaque Razor page du dossier.

Considérez la zone *Services* de l’exemple de code, qui ne contient pas de fichier *_ViewImports.cshtml*. La balise suivante affiche la page */services/Manage/about* Razor :

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

Dans le balisage précédent :

* Le nom de domaine complet doit être utilisé pour spécifier le modèle (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).
* Les [Tag Helpers](xref:mvc/views/tag-helpers/intro) sont activés par `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`

Dans l’exemple de téléchargement, la zone Products contient le fichier *_ViewImports.cshtml* suivant :

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

La balise suivante affiche la page */Products/about* Razor :

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

Dans le fichier précédent, l’espace de noms et la `@addTagHelper` directive sont importés dans le fichier par le fichier *Areas/Products/Pages/_ViewImports. cshtml* .

Pour plus d’informations, consultez [Gestion de l’étendue des Tag Helpers](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) et [Importation de directives partagées](xref:mvc/views/layout#importing-shared-directives).

### <a name="shared-layout-for-no-locrazor-pages-areas"></a>Disposition partagée pour les Razor zones de pages

Pour partager une disposition commune pour l’ensemble de l’application, déplacez *_ViewStart.cshtml* dans le dossier racine de l’application.

### <a name="publishing-areas"></a>Zones de publication

Tous les fichiers *.cshtml et autres fichiers dans le répertoire *wwwroot* sont publiés en sortie quand `<Project Sdk="Microsoft.NET.Sdk.Web">` est inclus dans le fichier *.csproj.
::: moniker-end
