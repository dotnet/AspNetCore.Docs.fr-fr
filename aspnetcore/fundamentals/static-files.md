---
title: Fichiers statiques dans ASP.NET Core
author: rick-anderson
description: Découvrez comment délivrer et sécuriser des fichiers statiques, et comment configurer le comportement d’un intergiciel (middleware) hébergeant des fichiers statiques dans une application web ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 6/23/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/static-files
ms.openlocfilehash: 807cffb2f9b3bf89ff06c62e76d51d4040b8d91a
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589008"
---
# <a name="static-files-in-aspnet-core"></a>Fichiers statiques dans ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Kirk Larkin](https://twitter.com/serpent5)

Les fichiers statiques, tels que HTML, CSS, images et JavaScript, sont des ressources qu’une ASP.NET Core application sert directement aux clients par défaut.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/static-files/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="serve-static-files"></a>Délivrer des fichiers statiques

Les fichiers statiques sont stockés dans le répertoire [racine Web](xref:fundamentals/index#web-root) du projet. Le répertoire par défaut est `{content root}/wwwroot` , mais il peut être modifié à l’aide de la <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> méthode. Pour plus d’informations, consultez [racine du contenu](xref:fundamentals/index#content-root) et [racine Web](xref:fundamentals/index#web-root).

La méthode <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> définit le répertoire actif comme racine du contenu :

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Program2.cs?name=snippet_Program)]

Le code précédent a été créé avec le modèle d’application Web.

Les fichiers statiques sont accessibles via un chemin d’accès relatif à la [racine Web](xref:fundamentals/index#web-root). Par exemple, les modèles de projet d' **application Web** contiennent plusieurs dossiers dans le `wwwroot` dossier :

* `wwwroot`
  * `css`
  * `js`
  * `lib`

Envisagez de créer le dossier *wwwroot/images* et d’ajouter le fichier *wwwroot/images/MyImage.jpg* . Le format d’URI pour accéder à un fichier dans le `images` dossier est `https://<hostname>/images/<image_file_name>` . Par exemple : `https://localhost:5001/images/MyImage.jpg`

### <a name="serve-files-in-web-root"></a>Traiter les fichiers dans la racine Web

Les modèles d’application Web par défaut appellent la <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> méthode dans, qui permet la prise en charge des `Startup.Configure` fichiers statiques :

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Startup.cs?name=snippet_Configure&highlight=15)]

La `UseStaticFiles` surcharge de méthode sans paramètre marque les fichiers dans la [racine Web](xref:fundamentals/index#web-root) comme étant reservables. Le balisage suivant référence *wwwroot/images/MyImage.jpg*:

```html
<img src="~/images/MyImage.jpg" class="img" alt="My image" />
```

Dans le code précédent, le caractère tilde `~/` pointe vers la [racine Web](xref:fundamentals/index#web-root).

### <a name="serve-files-outside-of-web-root"></a>Délivrer des fichiers en dehors de la racine web

Prenons l’exemple d’une hiérarchie de répertoires dans laquelle les fichiers statiques à prendre en charge se trouvent en dehors de la [racine Web](xref:fundamentals/index#web-root):

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `red-rose.jpg`

Une demande peut accéder au `red-rose.jpg` fichier en configurant l’intergiciel de fichiers statiques comme suit :

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupRose.cs?name=snippet_Configure&highlight=15-22)]

Dans le code précédent, la hiérarchie de répertoires *MyStaticFiles* est exposée publiquement via le segment d’URI *StaticFiles*. Demande de `https://<hostname>/StaticFiles/images/red-rose.jpg` servir le fichier *red-rose.jpg* .

Le balisage suivant référence *MyStaticFiles/images/red-rose.jpg*:

```html
<img src="~/StaticFiles/images/red-rose.jpg" class="img" alt="A red rose" />
```

### <a name="set-http-response-headers"></a>Définir des en-têtes de réponse HTTP

Un <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> objet peut être utilisé pour définir des en-têtes de réponse http. En plus de configurer le service de fichiers statiques à partir de la [racine Web](xref:fundamentals/index#web-root), le code suivant définit l' `Cache-Control` en-tête :

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_Configure&highlight=15-24)]

<!-- Q: The preceding code sets max-age to 604800 seconds (7 days), so what does the following mean? -->

Les fichiers statiques sont publiquement mis en cache pendant 600 secondes :

![En-têtes de réponse montrant que l’en-tête Cache-Control a été ajouté](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a>Autorisations des fichiers statiques

Les modèles ASP.NET Core appellent <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> avant d’appeler <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> . La plupart des applications suivent ce modèle. Lorsque l’intergiciel de fichiers statiques est appelé avant l’intergiciel (middleware) d’autorisation :

  * Aucune vérification d’autorisation n’est effectuée sur les fichiers statiques.
  * Les fichiers statiques pris en charge par l’intergiciel de fichiers statiques, tels que ceux sous `wwwroot` , sont accessibles publiquement.
  
Pour traiter les fichiers statiques en fonction de l’autorisation :

  * Stockez-les en dehors de `wwwroot` .
  * Appelez `UseStaticFiles` , en spécifiant un chemin d’accès, après avoir appelé `UseAuthorization` .
  * Définissez la [stratégie d’autorisation de secours](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).

  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet2&highlight=24-29)]
  
  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet1&highlight=20-25)]

  Dans le code précédent, la stratégie d’autorisation de secours exige que ***tous*** les utilisateurs soient authentifiés. Les points de terminaison tels que les contrôleurs, Razor les pages, etc. qui spécifient leurs propres exigences d’autorisation n’utilisent pas la stratégie d’autorisation de secours. Par exemple, les Razor pages, les contrôleurs ou les méthodes d’action avec `[AllowAnonymous]` ou `[Authorize(PolicyName="MyPolicy")]` utilisent l’attribut d’autorisation appliqué plutôt que la stratégie d’autorisation de secours.

  <xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> Ajoute <xref:Microsoft.AspNetCore.Authorization.Infrastructure.DenyAnonymousAuthorizationRequirement> à l’instance actuelle, ce qui garantit que l’utilisateur actuel est authentifié.

  Les ressources statiques sous `wwwroot` sont accessibles publiquement, car l’intergiciel de fichiers statiques par défaut ( `app.UseStaticFiles();` ) est appelé avant `UseAuthentication` . Les ressources statiques dans le dossier *MyStaticFiles* nécessitent une authentification. L' [exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/static-files/samples) illustre cela.

Une autre approche pour traiter les fichiers en fonction de l’autorisation consiste à :

  * Stockez-les en dehors de `wwwroot` et de tout répertoire accessible à l’intergiciel de fichiers statiques.
  * Servez-vous via une méthode d’action à laquelle l’autorisation est appliquée et retournez un <xref:Microsoft.AspNetCore.Mvc.FileResult> objet :

  [!code-csharp[](static-files/samples/3.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImage)]

## <a name="directory-browsing"></a>Exploration de répertoires

L’exploration des répertoires permet la liste des répertoires dans les répertoires spécifiés.

L’exploration des répertoires est désactivée par défaut pour des raisons de sécurité. Pour plus d’informations, consultez [considérations](#considerations).

Activer l’exploration des répertoires avec :

* <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> dans `Startup.ConfigureServices` .
* <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> dans `Startup.Configure` .

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ClassMembers&highlight=4,21-35)]

Le code précédent autorise l’exploration des répertoires du dossier *wwwroot/images* à l’aide de l’URL `https://<hostname>/MyImages` , avec des liens vers chaque fichier et dossier :

![exploration des répertoires](static-files/_static/dir-browse.png)

## <a name="serve-default-documents"></a>Traiter les documents par défaut

La définition d’une page par défaut fournit aux visiteurs un point de départ sur un site. Pour traiter une page par défaut `wwwroot` sans URI complet, appelez la <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A> méthode :

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty.cs?name=snippet_Configure&highlight=15)]

`UseDefaultFiles` doit être appelé avant `UseStaticFiles` pour délivrer le fichier par défaut. `UseDefaultFiles` est un ReWriter d’URL qui ne traite pas le fichier.

Avec `UseDefaultFiles` , les demandes à un dossier dans `wwwroot` Rechercher sont les suivantes :

* *default.htm*
* *default.html*
* *index.htm*
* *index.html*

Le premier fichier trouvé dans la liste est délivré comme si la requête était l’URI qualifié complet. L’URL du navigateur continue de refléter l’URI demandé.

Le code suivant change le nom de fichier par défaut en *mydefault.html* :

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_DefaultFiles)]

Le code suivant est affiché `Startup.Configure` avec le code précédent :

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_Configure&highlight=15-19)]

### <a name="usefileserver-for-default-documents"></a>UseFileServer pour les documents par défaut

<xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combine les fonctionnalités de `UseStaticFiles` , `UseDefaultFiles` et éventuellement `UseDirectoryBrowser` .

Appelez `app.UseFileServer` pour activer le service des fichiers statiques et le fichier par défaut. L’exploration des répertoires n’est pas activée. Le code suivant est affiché `Startup.Configure` avec `UseFileServer` :

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty2.cs?name=snippet_Configure&highlight=15)]

Le code suivant permet le service des fichiers statiques, le fichier par défaut et l’exploration des répertoires :

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

Le code suivant est affiché `Startup.Configure` avec le code précédent :

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty3.cs?name=snippet_Configure&highlight=15)]

Considérez la hiérarchie de répertoires suivante :

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `MyImage.jpg`
  * `default.html`

Le code suivant permet le service des fichiers statiques, le fichier par défaut et l’exploration des répertoires de `MyStaticFiles` :

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileServer.cs?name=snippet_ClassMembers&highlight=4,21-31)]

<xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> doit être appelé lorsque la `EnableDirectoryBrowsing` valeur de la propriété est `true` .

En utilisant la hiérarchie de fichiers et le code précédent, les URL sont résolues comme suit :

| URI            |      response  |
| ------- | ------|
| `https://<hostname>/StaticFiles/images/MyImage.jpg` | *MyStaticFiles/images/MyImage.jpg* |
| `https://<hostname>/StaticFiles` | *MyStaticFiles/default.html* |

Si aucun fichier nommé par défaut n’existe dans le répertoire *MyStaticFiles* , `https://<hostname>/StaticFiles` retourne la liste des répertoires avec des liens interactives :

![Liste des fichiers statiques](static-files/_static/db2.png)

<xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> et <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> effectuent une redirection côté client à partir de l’URI cible sans fin `/`  vers l’URI cible avec une fin `/` . Par exemple, de `https://<hostname>/StaticFiles` à `https://<hostname>/StaticFiles/` . Les URL relatives dans le répertoire *StaticFiles* ne sont pas valides sans barre oblique finale ( `/` ).

## <a name="fileextensioncontenttypeprovider"></a>FileExtensionContentTypeProvider

La <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> classe contient une `Mappings` propriété qui sert de mappage des extensions de fichier aux types de contenu MIME. Dans l’exemple suivant, plusieurs extensions de fichier sont mappées à des types MIME connus. L’extension *. rtf* est remplacée et *. MP4* est supprimé :

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Provider)]

Le code suivant est affiché `Startup.Configure` avec le code précédent :

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Configure&highlight=15-43)]

Consultez [Types de contenu MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).

## <a name="non-standard-content-types"></a>Types de contenu non standard

L’intergiciel de fichiers statiques comprend presque 400 types de contenu de fichier connus. Si l’utilisateur demande un fichier dont le type de fichier est inconnu, l’intergiciel (middleware) de fichiers statique transmet la demande à l’intergiciel (middleware) suivant dans le pipeline. Si aucun intergiciel ne gère la requête, une réponse *404 introuvable* est retournée. Si l’exploration des répertoires est activée, un lien vers le fichier est affiché dans la liste de répertoires.

Le code suivant permet de délivrer des types inconnus et rend le fichier inconnu en tant qu’image :

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_UseStaticFiles)]

Le code suivant est affiché `Startup.Configure` avec le code précédent :

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_Configure&highlight=15-19)]

Avec le code précédent, une requête pour un fichier avec un type de contenu inconnu est retournée en tant qu’image.

> [!WARNING]
> L’activation <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> de est un risque pour la sécurité. Il est désactivé par défaut et son utilisation est déconseillée. [FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) fournit une alternative plus sûre pour délivrer des fichiers avec des extensions non standard.

## <a name="serve-files-from-multiple-locations"></a>Servir des fichiers à partir de plusieurs emplacements

`UseStaticFiles` et `UseFileServer` par défaut, le fournisseur de fichiers pointe sur `wwwroot` . Des instances supplémentaires de `UseStaticFiles` et `UseFileServer` peuvent être fournies avec d’autres fournisseurs de fichiers pour servir des fichiers à partir d’autres emplacements. Pour plus d’informations, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/15578).

<a name="sc"></a>

### <a name="security-considerations-for-static-files"></a>Considérations sur la sécurité des fichiers statiques

> [!WARNING]
> `UseDirectoryBrowser` et `UseStaticFiles` peuvent entraîner une fuite de secrets. La désactivation de l’exploration de répertoires est fortement recommandée en production. Examinez attentivement les répertoires qui sont activés via `UseStaticFiles` ou `UseDirectoryBrowser`. L’ensemble du répertoire et de ses sous-répertoires deviennent accessibles publiquement. Stocker les fichiers pouvant être affectés au public dans un répertoire dédié, tel que `<content_root>/wwwroot` . Séparez ces fichiers des vues MVC, des Razor pages, des fichiers de configuration, etc.

* Les URL pour le contenu exposé avec `UseDirectoryBrowser` et `UseStaticFiles` sont soumises aux restrictions de respect de la casse et de caractères du système de fichiers sous-jacent. Par exemple, Windows ne respecte pas la casse, contrairement à macOS et Linux.

* Les applications ASP.NET Core hébergées dans IIS utilisent le [module ASP.NET Core](xref:host-and-deploy/aspnet-core-module) pour transférer toutes les requêtes à l’application, notamment les requêtes de fichiers statiques. Le gestionnaire de fichiers statiques IIS n’est pas utilisé et n’a pas la possibilité de traiter les demandes.

* Effectuez les étapes suivantes dans le Gestionnaire des services Internet (IIS) pour supprimer le gestionnaire de fichiers statiques d’IIS au niveau du serveur ou du site web :
    1. Accédez à la fonctionnalité **Modules**.
    1. Sélectionnez **StaticFileModule** dans la liste.
    1. Cliquez sur **Supprimer** dans l’encadré **Actions**.

> [!WARNING]
> Si le gestionnaire de fichiers statiques d’IIS est activé **et** que le module ASP.NET Core est incorrectement configuré, les fichiers statiques peuvent être délivrés. Cela se produit par exemple si le fichier *web.config* n’est pas déployé.

* Placez les fichiers de code, y compris les fichiers *. cs* et *. cshtml*, en dehors de la [racine Web](xref:fundamentals/index#web-root)du projet d’application. Par conséquent, une séparation logique est créée entre le contenu côté client et le code basé sur le serveur de l’application. Ceci empêche la fuite de code côté serveur.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Middleware](xref:fundamentals/middleware/index)
* [Introduction à ASP.NET Core](xref:index)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Scott Addie](https://twitter.com/Scott_Addie)

Les fichiers statiques, comme les fichiers HTML, CSS, images et JavaScript, sont des ressources qu’une application ASP.NET Core délivre directement aux clients. Une configuration est nécessaire pour pouvoir délivrer ces fichiers.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/static-files/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="serve-static-files"></a>Délivrer des fichiers statiques

Les fichiers statiques sont stockés dans le répertoire [racine Web](xref:fundamentals/index#web-root) du projet. Le répertoire par défaut est *{content root}/wwwroot*, mais il peut être modifié à l’aide de la <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> méthode. Pour plus d’informations, consultez [Racine du contenu](xref:fundamentals/index#content-root) et [Racine web](xref:fundamentals/index#web-root).

L’hôte web de l’application doit être informé du répertoire racine du contenu.

La méthode `WebHost.CreateDefaultBuilder` définit le répertoire actif comme racine du contenu :

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

Les fichiers statiques sont accessibles via un chemin d’accès relatif à la [racine Web](xref:fundamentals/index#web-root). Par exemple, le modèle de projet d' **application Web** contient plusieurs dossiers dans le `wwwroot` dossier :

* `wwwroot`
  * `css`
  * `images`
  * `js`

Le format d’URI permettant d’accéder à un fichier dans le sous-dossier *images* est *http:// \<server_address> /images/ \<image_file_name>*. Par exemple : *http://localhost:9189/images/banner3.svg*.

Si vous ciblez .NET Framework, ajoutez le package [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) au projet. Si vous ciblez .NET Core, le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) comprend ce package.

Configurez l' [intergiciel (middleware](xref:fundamentals/middleware/index)), qui permet de servir des fichiers statiques.

### <a name="serve-files-inside-of-web-root"></a>Délivrer des fichiers dans la racine web

Appelez la <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> méthode dans `Startup.Configure` :

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

La `UseStaticFiles` surcharge de méthode sans paramètre marque les fichiers dans la [racine Web](xref:fundamentals/index#web-root) comme étant reservables. Le balisage suivant référence *wwwroot/images/banner1.svg* :

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

Dans le code précédent, le caractère tilde `~/` pointe vers la [racine Web](xref:fundamentals/index#web-root).

### <a name="serve-files-outside-of-web-root"></a>Délivrer des fichiers en dehors de la racine web

Prenons l’exemple d’une hiérarchie de répertoires dans laquelle les fichiers statiques à prendre en charge se trouvent en dehors de la [racine Web](xref:fundamentals/index#web-root):

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `banner1.svg`

Vous permettez à une requête d’accéder au fichier *banner1.svg* en configurant le middleware (intergiciel) des fichiers statiques comme suit :

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

Dans le code précédent, la hiérarchie de répertoires *MyStaticFiles* est exposée publiquement via le segment d’URI *StaticFiles*. Une demande à *http:// \<server_address> /StaticFiles/images/banner1.svg* sert le fichier *banner1. svg* .

Le balisage suivant référence *MyStaticFiles/images/banner1.svg* :

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a>Définir des en-têtes de réponse HTTP

Un <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> objet peut être utilisé pour définir des en-têtes de réponse http. En plus de configurer le service de fichiers statiques à partir de la [racine Web](xref:fundamentals/index#web-root), le code suivant définit l' `Cache-Control` en-tête :

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

La <xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType> méthode existe dans le package [Microsoft. AspNetCore. http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) .

Les fichiers peuvent être mis en cache publiquement pendant 10 minutes (600 secondes) dans l’environnement de développement :

![En-têtes de réponse montrant que l’en-tête Cache-Control a été ajouté](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a>Autorisations des fichiers statiques

Le middleware de fichiers statiques ne fournit pas de vérification des autorisations. Tous les fichiers qu’il délivre, notamment ceux sous *wwwroot*, sont accessibles publiquement. Pour délivrer des fichiers en fonction d’une autorisation :

* Stockez-les en dehors de *wwwroot* et de tout répertoire accessible au middleware de fichiers statiques.
* Délivrez-les via une méthode d’action à laquelle une autorisation est appliquée. Retourne un <xref:Microsoft.AspNetCore.Mvc.FileResult> objet :

  [!code-csharp[](static-files/samples/1.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a>Activer l’exploration des répertoires

L’exploration des répertoires permet aux utilisateurs de votre application web de voir une liste des répertoires et des fichiers dans un répertoire spécifié. L’exploration des répertoires est désactivée par défaut pour des raisons de sécurité (consultez [Considérations](#considerations)). Activez l’exploration des répertoires en appelant la <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> méthode dans `Startup.Configure` :

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

Ajoutez les services requis en appelant la <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> méthode à partir de `Startup.ConfigureServices` :

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

Le code précédent autorise l’exploration des répertoires du dossier *wwwroot/images* à l’aide de l’URL *http:// \<server_address> /myImages*, avec des liens vers chaque fichier et dossier :

![exploration des répertoires](static-files/_static/dir-browse.png)

Consultez [Considérations](#considerations) sur les risques de sécurité lors de l’activation de l’exploration.

Notez les deux appels de `UseStaticFiles` dans l’exemple suivant. Le premier appel permet de délivrer des fichiers statiques dans le dossier *wwwroot*. Le deuxième appel active l’exploration des répertoires du dossier *wwwroot/images* à l’aide de l’URL *http:// \<server_address> /myImages*:

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a>Délivrer un document par défaut

La définition d’une page d’accueil par défaut donne aux visiteurs un point de départ logique lors de la visite de votre site. Pour traiter une page par défaut sans que l’utilisateur ne qualifie complètement l’URI, appelez la <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> méthode à partir de `Startup.Configure` :

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> `UseDefaultFiles` doit être appelé avant `UseStaticFiles` pour délivrer le fichier par défaut. `UseDefaultFiles` est un module de réécriture d’URL qui ne délivre pas réellement le fichier. Activez le middleware de fichiers statiques via `UseStaticFiles` pour délivrer le fichier.

Avec `UseDefaultFiles`, les requêtes sur un dossier recherchent :

* *default.htm*
* *default.html*
* *index.htm*
* *index.html*

Le premier fichier trouvé dans la liste est délivré comme si la requête était l’URI qualifié complet. L’URL du navigateur continue de refléter l’URI demandé.

Le code suivant change le nom de fichier par défaut en *mydefault.html* :

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a>UseFileServer

<xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combine les fonctionnalités de `UseStaticFiles` , `UseDefaultFiles` et éventuellement `UseDirectoryBrowser` .

Le code suivant active la possibilité de délivrer des fichiers statiques et le fichier par défaut. L’exploration des répertoires n’est pas activée.

```csharp
app.UseFileServer();
```

Le code suivant s’appuie sur la surcharge sans paramètre en activant l’exploration des répertoires :

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

Considérez la hiérarchie de répertoires suivante :

* **wwwroot**
  * **format**
  * **images**
  * **js**
* **MyStaticFiles**
  * **images**
    * *banner1.svg*
  * *default.html*

Le code suivant active les fichiers statiques, les fichiers par défaut et l’exploration des répertoires de `MyStaticFiles` :

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

`AddDirectoryBrowser` doit être appelé quand la valeur de la propriété `EnableDirectoryBrowsing` est `true` :

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

En utilisant la hiérarchie de fichiers et le code précédent, les URL sont résolues comme suit :

| URI            |                             response  |
| ------- | ------|
| *http:// \<server_address> /StaticFiles/images/banner1.svg*    |      MyStaticFiles/images/banner1.svg |
| *http:// \<server_address> /StaticFiles*             |     MyStaticFiles/default.html |

Si aucun fichier nommé par défaut n’existe dans le répertoire *MyStaticFiles* , *http:// \<server_address> /StaticFiles* retourne la liste des répertoires avec des liens interactives :

![Liste des fichiers statiques](static-files/_static/db2.png)

> [!NOTE]
> <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> et <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> effectuent une redirection côté client à partir de `http://{SERVER ADDRESS}/StaticFiles` (sans barre oblique) vers `http://{SERVER ADDRESS}/StaticFiles/` (avec barre oblique). Les URL relatives du répertoire *StaticFiles* ne sont pas valides sans barre oblique de fin.

## <a name="fileextensioncontenttypeprovider"></a>FileExtensionContentTypeProvider

La <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> classe contient une `Mappings` propriété servant de mappage d’extensions de fichier à des types de contenu MIME. Dans l’exemple suivant, plusieurs extensions de fichiers sont inscrites avec des types MIME connus. L’extension *.rtf* est remplacée et *.mp4* est supprimée.

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

Consultez [Types de contenu MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).

Pour plus d’informations sur l’utilisation d’un personnalisé <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> ou pour configurer d’autres <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> Blazor applications serveur, consultez <xref:blazor/fundamentals/static-files> .

## <a name="non-standard-content-types"></a>Types de contenu non standard

Static File Middleware comprend près de 400 types connus de contenu de fichier. Si l’utilisateur demande un fichier d’un type inconnu, Static File Middleware transmet la requête à l’intergiciel (middleware) suivant dans le pipeline. Si aucun intergiciel ne gère la requête, une réponse *404 introuvable* est retournée. Si l’exploration des répertoires est activée, un lien vers le fichier est affiché dans la liste de répertoires.

Le code suivant permet de délivrer des types inconnus et rend le fichier inconnu en tant qu’image :

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

Avec le code précédent, une requête pour un fichier avec un type de contenu inconnu est retournée en tant qu’image.

> [!WARNING]
> L’activation <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> de est un risque pour la sécurité. Il est désactivé par défaut et son utilisation est déconseillée. [FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) fournit une alternative plus sûre pour délivrer des fichiers avec des extensions non standard.

## <a name="serve-files-from-multiple-locations"></a>Servir des fichiers à partir de plusieurs emplacements

`UseStaticFiles``UseFileServer`la valeur par défaut est le fournisseur de fichiers qui pointe vers *wwwroot*. Vous pouvez fournir des instances supplémentaires de `UseStaticFiles` et `UseFileServer` avec d’autres fournisseurs de fichiers pour servir des fichiers à partir d’autres emplacements. Pour plus d’informations, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/15578).

### <a name="considerations"></a>Considérations

> [!WARNING]
> `UseDirectoryBrowser` et `UseStaticFiles` peuvent entraîner une fuite de secrets. La désactivation de l’exploration de répertoires est fortement recommandée en production. Examinez attentivement les répertoires qui sont activés via `UseStaticFiles` ou `UseDirectoryBrowser`. L’ensemble du répertoire et de ses sous-répertoires deviennent accessibles publiquement. Stocker les fichiers pouvant être affectés au public dans un répertoire dédié, par exemple *\<content_root> /wwwroot*. Séparez ces fichiers des vues MVC, des Razor pages (2. x uniquement), des fichiers de configuration, etc.

* Les URL pour le contenu exposé avec `UseDirectoryBrowser` et `UseStaticFiles` sont soumises aux restrictions de respect de la casse et de caractères du système de fichiers sous-jacent. Par exemple, Windows ne respecte pas la casse, mais macOS et Linux la respectent.

* Les applications ASP.NET Core hébergées dans IIS utilisent le [module ASP.NET Core](xref:host-and-deploy/aspnet-core-module) pour transférer toutes les requêtes à l’application, notamment les requêtes de fichiers statiques. Le gestionnaire de fichiers statiques d’IIS n’est pas utilisé. Il ne lui est pas permis de traiter les requêtes avant qu’elles soient gérées par le module.

* Effectuez les étapes suivantes dans le Gestionnaire des services Internet (IIS) pour supprimer le gestionnaire de fichiers statiques d’IIS au niveau du serveur ou du site web :
    1. Accédez à la fonctionnalité **Modules**.
    1. Sélectionnez **StaticFileModule** dans la liste.
    1. Cliquez sur **Supprimer** dans l’encadré **Actions**.

> [!WARNING]
> Si le gestionnaire de fichiers statiques d’IIS est activé **et** que le module ASP.NET Core est incorrectement configuré, les fichiers statiques peuvent être délivrés. Cela se produit par exemple si le fichier *web.config* n’est pas déployé.

* Placez les fichiers de code (y compris les fichiers *. cs* et *. cshtml*) en dehors de la [racine Web](xref:fundamentals/index#web-root)du projet d’application. Par conséquent, une séparation logique est créée entre le contenu côté client et le code basé sur le serveur de l’application. Ceci empêche la fuite de code côté serveur.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Middleware](xref:fundamentals/middleware/index)
* [Introduction à ASP.NET Core](xref:index)

::: moniker-end
