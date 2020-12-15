---
title: Héberger et déployer des ASP.NET Core Blazor
author: guardrex
description: Découvrez comment héberger et déployer des Blazor applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2020
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
uid: blazor/host-and-deploy/index
ms.openlocfilehash: a23bee120611ee603305a88dabac76566481fa4a
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97485886"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor"></a>Héberger et déployer des ASP.NET Core Blazor

Par [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) et [Daniel Roth](https://github.com/danroth27)

## <a name="publish-the-app"></a>Publier l’application

Les applications sont publiées pour le déploiement dans la configuration Release.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Sélectionnez **générer**  >  **publier {application}** dans la barre de navigation.
1. Sélectionnez l’onglet *Cible de publication*. Pour publier localement, sélectionnez **Dossier**.
1. Acceptez l’emplacement par défaut dans le champ **Choisir un dossier** ou spécifiez un autre emplacement. Sélectionner le bouton **`Publish`**.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

1. Sélectionnez **générer**  >  **publier dans le dossier**.
1. Confirmez le dossier pour recevoir les ressources publiées et sélectionnez **`Publish`** .

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

Utilisez la [`dotnet publish`](/dotnet/core/tools/dotnet-publish) commande pour publier l’application avec une configuration Release :

```dotnetcli
dotnet publish -c Release
```

---

La publication de l’application déclenche une [restauration](/dotnet/core/tools/dotnet-restore) des dépendances du projet et [crée](/dotnet/core/tools/dotnet-build) le projet avant de créer les ressources pour le déploiement. Dans le cadre du processus de génération, les assemblys et méthodes inutilisés sont supprimés pour réduire la durée du chargement et la taille du téléchargement de l’application.

Emplacements de publication :

* Blazor WebAssembly
  * Autonome : l’application est publiée dans le `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` dossier. Pour déployer l’application en tant que site statique, copiez le contenu du `wwwroot` dossier sur l’hôte de site statique.
  * Hébergé : l’application cliente Blazor WebAssembly est publiée dans le `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` dossier de l’application serveur, ainsi que les autres ressources Web statiques de l’application serveur. Déployez le contenu du `publish` dossier sur l’hôte.
* Blazor Server: L’application est publiée dans le `/bin/Release/{TARGET FRAMEWORK}/publish` dossier. Déployez le contenu du `publish` dossier sur l’hôte.

Les ressources du dossier sont déployées sur le serveur web. Le déploiement peut être un processus manuel ou automatisé, en fonction des outils de développement utilisés.

## <a name="app-base-path"></a>Chemin de base de l’application

Le *chemin d’accès de base* de l’application est le chemin de l’URL racine de l’application. Considérez les éléments suivants ASP.NET Core application et Blazor sous-application :

* L’application ASP.NET Core est nommée `MyApp` :
  * L’application réside physiquement à l’adresse `d:/MyApp` .
  * Les demandes sont reçues à l’adresse `https://www.contoso.com/{MYAPP RESOURCE}` .
* Une Blazor application nommée `CoolApp` est une sous-application de `MyApp` :
  * La sous-application réside physiquement à l’adresse `d:/MyApp/CoolApp` .
  * Les demandes sont reçues à l’adresse `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}` .

Si vous ne spécifiez pas de configuration supplémentaire pour `CoolApp` , la sous-application dans ce scénario n’a aucune connaissance de son emplacement sur le serveur. Par exemple, l’application ne peut pas construire des URL relatives correctes pour ses ressources sans savoir qu’elle réside sur le chemin d’URL relatif `/CoolApp/` .

Pour fournir la configuration du Blazor chemin d’accès de base de l’application `https://www.contoso.com/CoolApp/` , l’attribut de la `<base>` balise `href` est défini sur le chemin d’accès racine relatif dans le `wwwroot/index.html` fichier ( Blazor WebAssembly ) ou le `Pages/_Host.cshtml` fichier ( Blazor Server ).

Blazor WebAssembly (`wwwroot/index.html`):

```html
<base href="/CoolApp/">
```

Blazor Server (`Pages/_Host.cshtml`):

```html
<base href="~/CoolApp/">
```

Blazor Server les applications définissent également le chemin d’accès de base côté serveur en appelant <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> dans le pipeline de demande de l’application `Startup.Configure` :

```csharp
app.UsePathBase("/CoolApp");
```

En fournissant le chemin d’URL relatif, un composant qui ne se trouve pas dans le répertoire racine peut construire des URL relatives au chemin d’accès racine de l’application. Les composants à différents niveaux de la structure de répertoires peuvent créer des liens vers d’autres ressources à des emplacements de l’application. Le chemin d’accès de base de l’application est également utilisé pour intercepter les liens hypertexte sélectionnés où la `href` cible du lien se trouve dans l’espace URI du chemin d’accès de base de l’application. Le Blazor routeur gère la navigation interne.

Dans de nombreux scénarios d’hébergement, le chemin d’URL relatif à l’application est la racine de l’application. Dans ce cas, le chemin d’accès de base de l’URL relative de l’application est une barre oblique ( `<base href="/" />` ), qui est la configuration par défaut pour une Blazor application. Dans d’autres scénarios d’hébergement, tels que les pages GitHub et les sous-applications IIS, le chemin d’accès de base de l’application doit être défini sur le chemin d’URL relatif du serveur de l’application.

Pour définir le chemin d’accès de base de l’application, mettez à jour la `<base>` balise dans les `<head>` éléments tag du `Pages/_Host.cshtml` fichier ( Blazor Server ) ou du `wwwroot/index.html` fichier ( Blazor WebAssembly ). Affectez `href` à l’attribut la valeur `/{RELATIVE URL PATH}/` ( Blazor WebAssembly ) ou `~/{RELATIVE URL PATH}/` ( Blazor Server ). **La barre oblique de fin est obligatoire.** L’espace réservé `{RELATIVE URL PATH}` est le chemin d’URL relatif complet de l’application.

Pour une Blazor WebAssembly application avec un chemin d’URL relatif non racine (par exemple, `<base href="/CoolApp/">` ), l’application ne parvient pas à trouver ses ressources lorsqu’elle est *exécutée localement*. Pour surmonter ce problème pendant le développement local et les tests, vous pouvez fournir un argument de *base de chemin* qui correspond à la valeur `href` de la balise `<base>` au moment de l’exécution. **N’incluez pas de barre oblique finale.** Pour passer l’argument de base Path lors de l’exécution locale de l’application, exécutez la `dotnet run` commande à partir du répertoire de l’application avec l' `--pathbase` option :

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

Pour une Blazor WebAssembly application avec un chemin d’URL relatif `/CoolApp/` ( `<base href="/CoolApp/">` ), la commande est la suivante :

```dotnetcli
dotnet run --pathbase=/CoolApp
```

L' Blazor WebAssembly application répond localement à l’adresse `http://localhost:port/CoolApp` .

**Blazor Server`MapFallbackToPage`configuration de**

Transmettez le chemin d’accès suivant à <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> dans `Startup.Configure` :

```csharp
endpoints.MapFallbackToPage("/{RELATIVE PATH}/{**path:nonfile}");
```

L’espace réservé `{RELATIVE PATH}` est le chemin d’accès non racine sur le serveur. Par exemple, `CoolApp` est le segment de l’espace réservé si l’URL non racine de l’application est `https://{HOST}:{PORT}/CoolApp/` :

```csharp
endpoints.MapFallbackToPage("/CoolApp/{**path:nonfile}");
```

**Héberger plusieurs Blazor WebAssembly applications**

Pour plus d’informations sur l’hébergement de plusieurs Blazor WebAssembly applications dans une solution hébergée Blazor , consultez <xref:blazor/host-and-deploy/webassembly#hosted-deployment-with-multiple-blazor-webassembly-apps> .

## <a name="deployment"></a>Déploiement

Pour obtenir des conseils de déploiement, consultez les rubriques suivantes :

* <xref:blazor/host-and-deploy/webassembly>
* <xref:blazor/host-and-deploy/server>
