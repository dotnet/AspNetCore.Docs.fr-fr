---
title: Lien du navigateur dans ASP.NET Core
author: ncarandini
description: Explique comment le lien du navigateur est une fonctionnalité de Visual Studio qui lie l’environnement de développement à un ou plusieurs navigateurs Web.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 01/09/2020
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
uid: client-side/using-browserlink
ms.openlocfilehash: 80f05acab55af973faf08b5db79ea4cbaf896b14
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054487"
---
# <a name="browser-link-in-aspnet-core"></a>Lien du navigateur dans ASP.NET Core

Par [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson)et [Tom Dykstra](https://github.com/tdykstra)

Le lien du navigateur est une fonctionnalité de Visual Studio. Il crée un canal de communication entre l’environnement de développement et un ou plusieurs navigateurs Web. Vous pouvez utiliser le lien du navigateur pour actualiser votre application Web dans plusieurs navigateurs à la fois, ce qui est utile pour les tests entre navigateurs.

## <a name="browser-link-setup"></a>Configuration des liens du navigateur

::: moniker range=">= aspnetcore-3.0"

Ajoutez le package [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) à votre projet. Pour les Razor Pages ASP.net Core ou les projets MVC, activez également la compilation du runtime des Razor fichiers (*. cshtml*) comme décrit dans <xref:mvc/views/view-compilation> . Razor les modifications de syntaxe sont appliquées uniquement lorsque la compilation du runtime a été activée.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

Lors de la conversion d’un projet ASP.NET Core 2,0 en ASP.NET Core 2,1 et de la transition vers le [AspNetCore Microsoft.. app](xref:fundamentals/metapackage-app), installez le package [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) pour la fonctionnalité de lien du navigateur. Les modèles de projet ASP.NET Core 2,1 utilisent le `Microsoft.AspNetCore.App` package par défaut.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Les modèles de projet **application web** ASP.net Core 2,0, **vide** et **API Web** utilisent le sous- [package Microsoft. AspNetCore. All](xref:fundamentals/metapackage), qui contient une référence de package pour [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/). Par conséquent, l’utilisation du `Microsoft.AspNetCore.All` package ne nécessite aucune action supplémentaire pour rendre le lien de navigateur disponible.

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

Le modèle de projet d' **application Web** ASP.net Core 1. x a une référence de package pour le package [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) . D’autres types de projets nécessitent l’ajout d’une référence de package à `Microsoft.VisualStudio.Web.BrowserLink` .

::: moniker-end

### <a name="configuration"></a>Configuration

Appelez `UseBrowserLink` dans la méthode `Startup.Configure` :

```csharp
app.UseBrowserLink();
```

L' `UseBrowserLink` appel est généralement placé à l’intérieur d’un `if` bloc qui active uniquement le lien de navigateur dans l’environnement de développement. Par exemple :

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

Pour plus d’informations, consultez <xref:fundamentals/environments>.

## <a name="how-to-use-browser-link"></a>Procédure d’utilisation d’un lien de navigateur

Quand un projet de ASP.NET Core est ouvert, Visual Studio affiche le contrôle de barre d’outils lien de navigateur en regard du contrôle de barre d’outils **cible de débogage** :

![Menu déroulant lien du navigateur](using-browserlink/_static/browserLink-dropdown-menu.png)

Dans le contrôle de barre d’outils lien de navigateur, vous pouvez :

* Actualisez l’application Web dans plusieurs navigateurs à la fois.
* Ouvrez le **tableau de bord du lien de navigateur**.
* Activez ou désactivez le **lien du navigateur**. Remarque : le lien du navigateur est désactivé par défaut dans Visual Studio.
* Activez ou désactivez la [synchronisation automatique CSS](#enable-or-disable-css-auto-sync).

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a>Actualiser l’application Web dans plusieurs navigateurs à la fois

Pour choisir un seul navigateur Web à lancer au démarrage du projet, utilisez le menu déroulant du contrôle de barre d’outils de la **cible de débogage** :

![Menu déroulant F5](using-browserlink/_static/debug-target-dropdown-menu.png)

Pour ouvrir plusieurs navigateurs à la fois, choisissez **Parcourir avec...** dans la même liste déroulante. Maintenez la touche <kbd>CTRL</kbd> enfoncée pour sélectionner les navigateurs de votre choix, puis cliquez sur **Parcourir**:

![Ouvrir plusieurs navigateurs à la fois](using-browserlink/_static/open-many-browsers-at-once.png)

La capture d’écran suivante montre Visual Studio avec la vue d’index ouverte et deux navigateurs ouverts :

![Exemple de synchronisation avec deux navigateurs](using-browserlink/_static/sync-with-two-browsers-example.png)

Pointez sur le contrôle de barre d’outils lien du navigateur pour afficher les navigateurs qui sont connectés au projet :

![Pointe pointage](using-browserlink/_static/hoover-tip.png)

Modifiez la vue index et tous les navigateurs connectés sont mis à jour lorsque vous cliquez sur le bouton actualiser le lien du navigateur :

![navigateurs-synchronisation-à-modification](using-browserlink/_static/browsers-sync-to-changes.png)

Le lien du navigateur fonctionne également avec les navigateurs que vous lancez en dehors de Visual Studio et accédez à l’URL de l’application.

### <a name="the-browser-link-dashboard"></a>Tableau de bord du lien de navigateur

Ouvrez la fenêtre **tableau de bord du lien de navigateur** dans le menu déroulant de lien du navigateur pour gérer la connexion avec les navigateurs ouverts :

![ouvrir-browserslink-tableau de bord](using-browserlink/_static/open-browserlink-dashboard.png)

Si aucun navigateur n’est connecté, vous pouvez démarrer une session de non-débogage en sélectionnant le lien **afficher dans le navigateur** :

![browserlink-tableau de bord-sans-connexions](using-browserlink/_static/browserlink-dashboard-no-connections.png)

Dans le cas contraire, les navigateurs connectés affichent le chemin d’accès à la page affichée par chaque navigateur :

![browserlink-tableau de bord-deux-connexions](using-browserlink/_static/browserlink-dashboard-two-connections.png)

Vous pouvez également cliquer sur un nom de navigateur individuel pour actualiser uniquement ce navigateur.

### <a name="enable-or-disable-browser-link"></a>Activer ou désactiver le lien du navigateur

Lorsque vous réactivez le lien de navigateur après l’avoir désactivé, vous devez actualiser les navigateurs pour les reconnecter.

### <a name="enable-or-disable-css-auto-sync"></a>Activer ou désactiver la synchronisation automatique CSS

Lorsque la synchronisation automatique CSS est activée, les navigateurs connectés sont actualisés automatiquement lorsque vous apportez des modifications aux fichiers CSS.

## <a name="how-it-works"></a>Fonctionnement

Le lien du navigateur utilise [SignalR](xref:signalr/introduction) pour créer un canal de communication entre Visual Studio et le navigateur. Lorsque le lien du navigateur est activé, Visual Studio agit comme un SignalR serveur auquel plusieurs clients (navigateurs) peuvent se connecter. Le lien du navigateur inscrit également un composant d’intergiciel dans le pipeline de demande ASP.NET Core. Ce composant injecte `<script>` des références spéciales dans chaque demande de page à partir du serveur. Vous pouvez voir les références de script en sélectionnant **afficher la source** dans le navigateur et en faisant défiler jusqu’à la fin du contenu de la `<body>` balise :

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

Vos fichiers sources ne sont pas modifiés. Le composant intergiciel (middleware) injecte les références de script de manière dynamique.

Étant donné que le code côté navigateur est tout JavaScript, il fonctionne sur tous les navigateurs qui SignalR prennent en charge sans nécessiter de plug-in de navigateur.
