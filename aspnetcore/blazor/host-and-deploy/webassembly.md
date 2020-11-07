---
title: Héberger et déployer des ASP.NET Core Blazor WebAssembly
author: guardrex
description: Découvrez comment héberger et déployer une Blazor application à l’aide de ASP.net Core, de réseaux de distribution de contenu (CDN), de serveurs de fichiers et de pages github.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/09/2020
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
uid: blazor/host-and-deploy/webassembly
ms.openlocfilehash: 7ae462ff9abd06fe4ab4b3e00a71515b76b0ee7d
ms.sourcegitcommit: bb475e69cb647f22cf6d2c6f93d0836c160080d7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2020
ms.locfileid: "94339982"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor-webassembly"></a>Héberger et déployer des ASP.NET Core Blazor WebAssembly

Par [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams)et [safia Abdalla](https://safia.rocks)

Avec le [ Blazor WebAssembly modèle d’hébergement](xref:blazor/hosting-models#blazor-webassembly):

* L' Blazor application, ses dépendances et le Runtime .net sont téléchargés dans le navigateur en parallèle.
* L’application est exécutée directement sur le thread d’interface utilisateur du navigateur.

Les stratégies de déploiement suivantes sont prises en charge :

* L' Blazor application est traitée par une application ASP.net core. Cette stratégie est abordée dans la section [Déploiement hébergé avec ASP.NET Core](#hosted-deployment-with-aspnet-core).
* L' Blazor application est placée sur un service ou un serveur Web d’hébergement statique, où .net n’est pas utilisé pour traiter l' Blazor application. Cette stratégie est traitée dans la section [Déploiement autonome](#standalone-deployment) , qui comprend des informations sur l’hébergement d’une Blazor WebAssembly application en tant que sous-application IIS.

## <a name="compression"></a>Compression

Quand une Blazor WebAssembly application est publiée, la sortie est compressée de manière statique lors de la publication afin de réduire la taille de l’application et de supprimer la surcharge liée à la compression du Runtime. Les algorithmes de compression suivants sont utilisés :

* [Brotli](https://tools.ietf.org/html/rfc7932) (niveau le plus élevé)
* [Gzip](https://tools.ietf.org/html/rfc1952)

Blazor s’appuie sur l’hôte pour servir les fichiers compressés appropriés. Lors de l’utilisation d’un projet hébergé ASP.NET Core, le projet hôte peut effectuer la négociation de contenu et traiter les fichiers compressés statiquement. Lors de l’hébergement d’une Blazor WebAssembly application autonome, un travail supplémentaire peut être nécessaire pour s’assurer que les fichiers compressés statiquement sont pris en charge :

* Pour `web.config` la configuration de la compression IIS, consultez la section [IIS : Brotli et compression gzip](#brotli-and-gzip-compression) . 
* Lors de l’hébergement sur des solutions d’hébergement statiques qui ne prennent pas en charge la négociation de contenu de fichier compressée statiquement, telles que les pages GitHub, envisagez de configurer l’application pour extraire et décoder les fichiers compressés Brotli :

  * Obtenez le décodeur Brotli JavaScript à partir du [référentiel GitHub Google/Brotli](https://github.com/google/brotli). À compter de septembre 2020, le fichier de décodeur est nommé `decode.js` et se trouve dans le [ `js` dossier](https://github.com/google/brotli/tree/master/js)du référentiel.
  
    > [!NOTE]
    > Une régression est présente dans la version minimisés du `decode.js` script ( `decode.min.js` ) dans le [référentiel GitHub Google/brotli](https://github.com/google/brotli). Vous pouvez soit réduire le script par vous-même, soit utiliser le [package NPM](https://www.npmjs.com/package/brotli) jusqu’à la fenêtre de problème [. BrotliDecode n’est pas défini dans decode.min.js (google/brotli #844)](https://github.com/google/brotli/issues/844) est résolu. L’exemple de code dans cette section utilise la version **unminified** du script.

  * Mettez à jour l’application pour utiliser le décodeur. Remplacez le balisage dans la `<body>` balise de fermeture par `wwwroot/index.html` ce qui suit :
  
    ```html
    <script src="decode.js"></script>
    <script src="_framework/blazor.webassembly.js" autostart="false"></script>
    <script>
      Blazor.start({
        loadBootResource: function (type, name, defaultUri, integrity) {
          if (type !== 'dotnetjs' && location.hostname !== 'localhost') {
            return (async function () {
              const response = await fetch(defaultUri + '.br', { cache: 'no-cache' });
              if (!response.ok) {
                throw new Error(response.statusText);
              }
              const originalResponseBuffer = await response.arrayBuffer();
              const originalResponseArray = new Int8Array(originalResponseBuffer);
              const decompressedResponseArray = BrotliDecode(originalResponseArray);
              const contentType = type === 
                'dotnetwasm' ? 'application/wasm' : 'application/octet-stream';
              return new Response(decompressedResponseArray, 
                { headers: { 'content-type': contentType } });
            })();
          }
        }
      });
    </script>
    ```
 
Pour désactiver la compression, ajoutez la `BlazorEnableCompression` propriété MSBuild au fichier projet de l’application et définissez la valeur sur `false` :

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

La `BlazorEnableCompression` propriété peut être passée à la [`dotnet publish`](/dotnet/core/tools/dotnet-publish) commande avec la syntaxe suivante dans une interface de commande :

```dotnetcli
dotnet publish -p:BlazorEnableCompression=false
```

## <a name="rewrite-urls-for-correct-routing"></a>Réécriture d’URL pour un routage correct

Le routage des requêtes pour les composants de page dans une Blazor WebAssembly application n’est pas aussi simple que les demandes de routage dans une Blazor Server application hébergée. Prenons l’exemple d’une Blazor WebAssembly application avec deux composants :

* `Main.razor`: Charge à la racine de l’application et contient un lien vers le `About` composant ( `href="About"` ).
* `About.razor`: `About` composant.

Quand le document par défaut de l’application est demandé à l’aide de la barre d’adresses du navigateur (par exemple, `https://www.contoso.com/`) :

1. Le navigateur effectue une requête.
1. La page par défaut est retournée, qui est généralement `index.html` .
1. `index.html` amorce l’application.
1. Blazorle routeur est chargé et le Razor `Main` composant est rendu.

Dans la page principale, le fait de sélectionner le lien vers le `About` composant fonctionne sur le client car le Blazor routeur empêche le navigateur d’effectuer une requête sur Internet pour `www.contoso.com` `About` et sert le `About` composant rendu lui-même. Toutes les demandes de points de terminaison internes *au sein de l' Blazor WebAssembly application* fonctionnent de la même façon : les demandes ne déclenchent pas de requêtes basées sur un navigateur vers des ressources hébergées sur le serveur sur Internet. Le routeur gère les requêtes en interne.

Si une requête pour `www.contoso.com/About` est effectuée à l’aide de la barre d’adresses du navigateur, elle échoue. Comme cette ressource n’existe pas sur l’hôte Internet de l’application, une réponse *404 – Non trouvé* est retournée.

Étant donné que les navigateurs effectuent des demandes aux hôtes basés sur Internet pour les pages côté client, les serveurs Web et les services d’hébergement doivent réécrire toutes les demandes de ressources qui ne sont pas physiquement sur le serveur sur la `index.html` page. Lorsque `index.html` est retourné, le routeur de l’application Blazor prend le relais et répond avec la ressource correcte.

Lors du déploiement sur un serveur IIS, vous pouvez utiliser le module de réécriture d’URL avec le fichier publié de l’application `web.config` . Pour plus d’informations, consultez la section [IIS](#iis) .

## <a name="hosted-deployment-with-aspnet-core"></a>Déploiement hébergé avec ASP.NET Core

Un *déploiement hébergé* sert l' Blazor WebAssembly application aux navigateurs à partir d’une [application ASP.net Core](xref:index) qui s’exécute sur un serveur Web.

L’application cliente Blazor WebAssembly est publiée dans le `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` dossier de l’application serveur, ainsi que les autres ressources Web statiques de l’application serveur. Les deux applications sont déployées ensemble. Un serveur web capable d’héberger une application ASP.NET Core est nécessaire. Pour un déploiement hébergé, Visual Studio comprend le modèle de projet d' **Blazor WebAssembly application** ( `blazorwasm` modèle lors de l’utilisation de la [`dotnet new`](/dotnet/core/tools/dotnet-new) commande) avec l' **`Hosted`** option sélectionnée (lors de l' `-ho|--hosted` utilisation de la `dotnet new` commande).

Pour plus d’informations sur l’hébergement et le déploiement d’applications ASP.NET Core, consultez <xref:host-and-deploy/index>.

Pour plus d’informations concernant le déploiement sur Azure App Service, consultez <xref:tutorials/publish-to-azure-webapp-using-vs>.

## <a name="hosted-deployment-with-multiple-no-locblazor-webassembly-apps"></a>Déploiement hébergé avec plusieurs Blazor WebAssembly applications

### <a name="app-configuration"></a>la configuration d’une application ;

Pour configurer une solution hébergée Blazor pour servir plusieurs Blazor WebAssembly applications :

* Utilisez une solution hébergée existante Blazor ou créez une nouvelle solution à partir du Blazor modèle de projet hébergé.

* Dans le fichier projet de l’application cliente, ajoutez une `<StaticWebAssetBasePath>` propriété à `<PropertyGroup>` avec une valeur `FirstApp` pour définir le chemin d’accès de base pour les ressources statiques du projet :

  ```xml
  <PropertyGroup>
    ...
    <StaticWebAssetBasePath>FirstApp</StaticWebAssetBasePath>
  </PropertyGroup>
  ```

* Ajoutez une deuxième application cliente à la solution :

  * Ajoutez un dossier nommé `SecondClient` au dossier de la solution.
  * Créez une Blazor WebAssembly application nommée `SecondBlazorApp.Client` dans le `SecondClient` dossier à partir du Blazor WebAssembly modèle de projet.
  * Dans le fichier projet de l’application :

    * Ajoutez une `<StaticWebAssetBasePath>` propriété à `<PropertyGroup>` avec la valeur `SecondApp` :

      ```xml
      <PropertyGroup>
        ...
        <StaticWebAssetBasePath>SecondApp</StaticWebAssetBasePath>
      </PropertyGroup>
      ```

    * Ajoutez une référence de projet au `Shared` projet :

      ```xml
      <ItemGroup>
        <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
      </ItemGroup>
      ```

      L’espace réservé `{SOLUTION NAME}` est le nom de la solution.

* Dans le fichier projet de l’application serveur, créez une référence de projet pour l’application cliente ajoutée :

  ```xml
  <ItemGroup>
    ...
    <ProjectReference Include="..\SecondClient\SecondBlazorApp.Client.csproj" />
  </ItemGroup>
  ```

* Dans le fichier de l’application serveur `Properties/launchSettings.json` , configurez le `applicationUrl` du profil Kestrel ( `{SOLUTION NAME}.Server` ) pour accéder aux applications clientes aux ports 5001 et 5002 :

  ```json
  "applicationUrl": "https://localhost:5001;https://localhost:5002",
  ```

* Dans la méthode de l’application serveur `Startup.Configure` ( `Startup.cs` ), supprimez les lignes suivantes, qui apparaissent après l’appel à <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> :

  ```csharp
  app.UseBlazorFrameworkFiles();
  app.UseStaticFiles();

  app.UseRouting();

  app.UseEndpoints(endpoints =>
  {
      endpoints.MapRazorPages();
      endpoints.MapControllers();
      endpoints.MapFallbackToFile("index.html");
  });
  ```

  Ajoutez un intergiciel (middleware) qui mappe les demandes aux applications clientes. L’exemple suivant configure l’intergiciel (middleware) à exécuter lorsque :

  * Le port de la demande est soit 5001 pour l’application cliente d’origine, soit 5002 pour l’application cliente ajoutée.
  * L’hôte de la requête est soit `firstapp.com` pour l’application cliente d’origine, soit `secondapp.com` pour l’application cliente ajoutée.

    > [!NOTE]
    > L’exemple présenté dans cette section requiert une configuration supplémentaire pour :
    >
    > * L’accès aux applications des exemples de domaines hôtes, `firstapp.com` et `secondapp.com` .
    > * Certificats pour les applications clientes afin d’activer la sécurité TLS (HTTPs).
    >
    > La configuration requise dépasse le cadre de cet article et dépend de la façon dont la solution est hébergée. Pour plus d’informations, consultez les [Articles Host et deploy](xref:host-and-deploy/index).

  Placez le code suivant dans lequel les lignes ont été supprimées précédemment :

  ```csharp
  app.MapWhen(ctx => ctx.Request.Host.Port == 5001 || 
      ctx.Request.Host.Equals("firstapp.com"), first =>
  {
      first.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/FirstApp" + ctx.Request.Path;
          return nxt();
      });

      first.UseBlazorFrameworkFiles("/FirstApp");
      first.UseStaticFiles();
      first.UseStaticFiles("/FirstApp");
      first.UseRouting();

      first.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.MapFallbackToFile("/FirstApp/{*path:nonfile}", 
              "FirstApp/index.html");
      });
  });
  
  app.MapWhen(ctx => ctx.Request.Host.Port == 5002 || 
      ctx.Request.Host.Equals("secondapp.com"), second =>
  {
      second.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/SecondApp" + ctx.Request.Path;
          return nxt();
      });

      second.UseBlazorFrameworkFiles("/SecondApp");
      second.UseStaticFiles();
      second.UseStaticFiles("/SecondApp");
      second.UseRouting();

      second.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.MapFallbackToFile("/SecondApp/{*path:nonfile}", 
              "SecondApp/index.html");
      });
  });
  ```

* Dans le contrôleur de prévisions météorologiques de l’application serveur ( `Controllers/WeatherForecastController.cs` ), remplacez l’itinéraire existant ( `[Route("[controller]")]` ) `WeatherForecastController` par les itinéraires suivants :

  ```csharp
  [Route("FirstApp/[controller]")]
  [Route("SecondApp/[controller]")]
  ```

  L’intergiciel ajouté à la méthode de l’application serveur `Startup.Configure` modifie précédemment les demandes entrantes vers `/WeatherForecast` `/FirstApp/WeatherForecast` ou, `/SecondApp/WeatherForecast` selon le port (5001/5002) ou le domaine ( `firstapp.com` / `secondapp.com` ). Les itinéraires de contrôleur précédents sont requis pour retourner les données météorologiques de l’application serveur aux applications clientes.

### <a name="static-assets-and-class-libraries"></a>Ressources statiques et bibliothèques de classes

Utilisez les approches suivantes pour les ressources statiques :

* Lorsque la ressource se trouve dans le dossier de l’application cliente `wwwroot` , indiquez les chemins d’accès normalement :

  ```razor
  <img alt="..." src="/{ASSET FILE NAME}" />
  ```

* Lorsque la ressource se trouve dans le `wwwroot` dossier d’une [ Razor bibliothèque de classes (RCL)](xref:blazor/components/class-libraries), référencez la ressource statique dans l’application cliente conformément aux instructions de l' [article RCL](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):

  ```razor
  <img alt="..." src="_content/{LIBRARY NAME}/{ASSET FILE NAME}" />
  ```

<!-- HOLD for reactivation at 5.x

::: moniker range=">= aspnetcore-5.0"

Components provided to a client app by a class library are referenced normally. If any components require stylesheets or JavaScript files, use either of the following approaches to obtain the static assets:

* The client app's `wwwroot/index.html` file can link (`<link>`) to the static assets.
* The component can use the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) to obtain the static assets.

The preceding approaches are demonstrated in the following examples.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

-->

Les composants fournis à une application cliente par une bibliothèque de classes sont référencés normalement. Si des composants requièrent des feuilles de style ou des fichiers JavaScript, le fichier de l’application cliente `wwwroot/index.html` doit inclure les liens d’éléments statiques appropriés. Ces approches sont illustrées dans les exemples suivants.

<!-- HOLD for reactivation at 5.x

::: moniker-end

-->

Ajoutez le `Jeep` composant suivant à l’une des applications clientes. Le `Jeep` composant utilise :

* Image du dossier de l’application cliente `wwwroot` ( `jeep-cj.png` ).
* Image d’un dossier [de Razor bibliothèque de composants](xref:blazor/components/class-libraries) ( `JeepImage` ) ajouté () `wwwroot` `jeep-yj.png` .
* L’exemple de composant ( `Component1` ) est créé automatiquement par le modèle de projet RCL lorsque la `JeepImage` bibliothèque est ajoutée à la solution.

```razor
@page "/Jeep"

<h1>1979 Jeep CJ-5&trade;</h1>

<p>
    <img alt="1979 Jeep CJ-5&trade;" src="/jeep-cj.png" />
</p>

<h1>1991 Jeep YJ&trade;</h1>

<p>
    <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
</p>

<p>
    <em>Jeep CJ-5</em> and <em>Jeep YJ</em> are a trademarks of 
    <a href="https://www.fcagroup.com">Fiat Chrysler Automobiles</a>.
</p>

<JeepImage.Component1 />
```

> [!WARNING]
> Ne publiez **pas** d’images de véhicules publiquement, sauf si vous possédez les images. Dans le cas contraire, vous risquez de violation des droits d’auteur.

<!-- HOLD for reactivation at 5.x

::: moniker range=">= aspnetcore-5.0"

The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`). To provide the `my-component` CSS class to the client app's page, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements):

```razor
<div class="my-component">
    <Link href="_content/JeepImage/styles.css" rel="stylesheet" />

    <h1>JeepImage.Component1</h1>

    <p>
        This Blazor component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

An alternative to using the [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) is to load the stylesheet from the client app's `wwwroot/index.html` file. This approach makes the stylesheet available to all of the components in the client app:

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

-->

L’image de la bibliothèque `jeep-yj.png` peut également être ajoutée au composant de la bibliothèque `Component1` ( `Component1.razor` ) :

```razor
<div class="my-component">
    <h1>JeepImage.Component1</h1>

    <p>
        This Blazor component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

Le fichier de l’application cliente `wwwroot/index.html` demande la feuille de style de la bibliothèque avec la `<link>` balise ajoutée suivante :

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

<!-- HOLD for reactivation at 5.x

::: moniker-end

-->

Ajoutez la navigation au `Jeep` composant dans le composant de l’application cliente `NavMenu` ( `Shared/NavMenu.razor` ) :

```razor
<li class="nav-item px-3">
    <NavLink class="nav-link" href="Jeep">
        <span class="oi oi-list-rich" aria-hidden="true"></span> Jeep
    </NavLink>
</li>
```

Pour plus d’informations sur RCLs, consultez :

* <xref:blazor/components/class-libraries>
* <xref:razor-pages/ui-class>

## <a name="standalone-deployment"></a>Déploiement autonome

Un *Déploiement autonome* sert l' Blazor WebAssembly application sous la forme d’un ensemble de fichiers statiques demandés directement par les clients. Tout serveur de fichiers statique peut traiter l' Blazor application.

Les ressources de déploiement autonomes sont publiées dans le `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` dossier.

### <a name="azure-app-service"></a>Azure App Service

Blazor WebAssembly les applications peuvent être déployées sur Azure App services sur Windows, qui héberge l’application sur [IIS](#iis).

Le déploiement d’une Blazor WebAssembly application autonome sur Azure App service pour Linux n’est pas pris en charge actuellement. Une image de serveur Linux pour héberger l’application n’est pas disponible pour l’instant. Le travail est en cours pour activer ce scénario.

### <a name="iis"></a>IIS

IIS est un serveur de fichiers statiques et puissant pour les Blazor applications. Pour configurer IIS pour héberger Blazor , consultez [créer un site Web statique sur IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).

Les ressources publiées sont créées dans le `/bin/Release/{TARGET FRAMEWORK}/publish` dossier. Hébergez le contenu du `publish` dossier sur le serveur Web ou le service d’hébergement.

#### <a name="webconfig"></a>web.config

Lorsqu’un Blazor projet est publié, un `web.config` fichier est créé avec la configuration IIS suivante :

* Les types MIME sont définis pour les extensions de fichiers suivantes :
  * `.dll`: `application/octet-stream`
  * `.json`: `application/json`
  * `.wasm`: `application/wasm`
  * `.woff`: `application/font-woff`
  * `.woff2`: `application/font-woff`
* La compression HTTP est activée pour les types MIME suivants :
  * `application/octet-stream`
  * `application/wasm`
* Des règles du module de réécriture d’URL sont établies :
  * Servez-vous du sous-répertoire dans lequel résident les ressources statiques de l’application ( `wwwroot/{PATH REQUESTED}` ).
  * Créez le routage de secours SPA afin que les demandes pour les ressources non-fichier soient redirigées vers le document par défaut de l’application dans son dossier ressources statiques ( `wwwroot/index.html` ).
  
#### <a name="use-a-custom-webconfig"></a>Utiliser un web.config personnalisé

Pour utiliser un `web.config` fichier personnalisé, placez le `web.config` fichier personnalisé à la racine du dossier du projet. Configurez le projet pour publier des ressources spécifiques à IIS à l’aide `PublishIISAssets` de dans le fichier projet de l’application et publiez le projet :

```xml
<PropertyGroup>
  <PublishIISAssets>true</PublishIISAssets>
</PropertyGroup>
```

#### <a name="install-the-url-rewrite-module"></a>Installer le module de réécriture d’URL

Le [module de réécriture d’URL](https://www.iis.net/downloads/microsoft/url-rewrite) est nécessaire pour réécrire les URL. Il n’est pas installé par défaut, et ne peut pas l’être en tant que fonctionnalité de service de rôle Serveur Web (IIS). Vous devez le télécharger à partir du site web IIS. Utilisez Web Platform Installer pour installer le module :

1. Localement, accédez à la [page des téléchargements du Module de réécriture d’URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads). Pour la version anglaise, sélectionnez **WebPI** pour télécharger le programme d’installation WebPI. Pour les autres langues, sélectionnez l’architecture appropriée pour le serveur (x86/x64) afin de télécharger le programme d’installation.
1. Copiez le programme d’installation sur le serveur. Exécutez le programme d’installation. Sélectionnez le bouton **Installer** et acceptez les termes du contrat de licence. Il n’est pas nécessaire de redémarrer le serveur après l’installation.

#### <a name="configure-the-website"></a>Configurer le site web

Affectez le dossier de l’application comme **chemin physique** du site web. Le dossier contient les éléments suivants :

* `web.config`Fichier utilisé par IIS pour configurer le site Web, y compris les règles de redirection et les types de contenu de fichier requis.
* Le dossier de ressources statiques de l’application

#### <a name="host-as-an-iis-sub-app"></a>Héberger en tant que sous-application IIS

Si une application autonome est hébergée en tant que sous-application IIS, effectuez l’une des opérations suivantes :

* Désactivez le gestionnaire de module ASP.NET Core hérité.

  Supprimez le gestionnaire dans le Blazor fichier publié de l’application `web.config` en ajoutant une `<handlers>` section au fichier :

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* Désactivez l’héritage de la section de l’application racine (parent) `<system.webServer>` à l’aide d’un `<location>` élément dont la `inheritInChildApplications` valeur est `false` :

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <handlers>
          <add name="aspNetCore" ... />
        </handlers>
        <aspNetCore ... />
      </system.webServer>
    </location>
  </configuration>
  ```

La suppression du gestionnaire ou la désactivation de l’héritage est effectuée en plus de [la configuration du chemin d’accès de base de l’application](xref:blazor/host-and-deploy/index#app-base-path). Définissez le chemin d’accès de base de l’application dans le fichier de l’application `index.html` sur l’alias IIS utilisé lors de la configuration de la sous-application dans IIS.

#### <a name="brotli-and-gzip-compression"></a>Compression Brotli et gzip

*Cette section s’applique uniquement aux Blazor WebAssembly applications autonomes. Blazor les applications hébergées utilisent un fichier d’application ASP.net Core par défaut `web.config` , et non le fichier lié dans cette section.*

IIS peut être configuré via `web.config` pour servir des ressources compressées Brotli ou gzip Blazor pour des applications autonomes Blazor WebAssembly . Pour obtenir un exemple de fichier de configuration, consultez [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true) .

Une configuration supplémentaire de l’exemple de `web.config` fichier peut être nécessaire dans les scénarios suivants :

* La spécification de l’application appelle l’un des éléments suivants :
  * Service des fichiers compressés qui ne sont pas configurés par l’exemple de `web.config` fichier.
  * Service des fichiers compressés configurés par l’exemple `web.config` de fichier dans un format non compressé.
* La configuration IIS du serveur (par exemple, `applicationHost.config` ) fournit des paramètres IIS par défaut au niveau du serveur. Selon la configuration au niveau du serveur, l’application peut nécessiter une configuration IIS différente de celle contenue dans l’exemple de `web.config` fichier.

#### <a name="troubleshooting"></a>Dépannage

Si vous recevez un message *500 – Erreur interne du serveur* et que le Gestionnaire IIS lève des erreurs quand vous tentez d’accéder à la configuration du site web, vérifiez que le module de réécriture d’URL est installé. Lorsque le module n’est pas installé, le `web.config` fichier ne peut pas être analysé par IIS. Cela empêche le gestionnaire des services Internet de charger la configuration du site Web et le site Web à partir des Blazor fichiers statiques de service.

Pour plus d’informations sur le dépannage des déploiements sur IIS, consultez <xref:test/troubleshoot-azure-iis>.

### <a name="azure-storage"></a>Stockage Azure

L’hébergement de fichiers statiques [Azure Storage](/azure/storage/) permet l’hébergement d’applications sans serveur Blazor . Les noms de domaine personnalisé, le réseau de distribution de contenu Azure (CDN) et HTTPS sont pris en charge.

Lorsque le service blob est activé pour l’hébergement de site Web statique sur un compte de stockage :

* Définissez le **nom du document d’index** sur `index.html`.
* Définissez le **chemin d’accès au document d’erreur** sur `index.html`. Razor les composants et autres points de terminaison non-fichier ne résident pas sur des chemins d’accès physiques dans le contenu statique stocké par le service BLOB. Lorsqu’une demande pour l’une de ces ressources est reçue que le Blazor routeur doit gérer, l’erreur *404-introuvable* générée par le service BLOB achemine la requête vers le **chemin du document d’erreur**. L' `index.html` objet blob est retourné et le Blazor routeur charge et traite le chemin d’accès.

Si les fichiers ne sont pas chargés au moment de l’exécution en raison de types MIME inappropriés dans les `Content-Type` en-têtes des fichiers, effectuez l’une des actions suivantes :

* Configurez vos outils pour définir les types MIME corrects ( `Content-Type` en-têtes) lors du déploiement des fichiers.
* Modifiez les types MIME ( `Content-Type` en-têtes) des fichiers après le déploiement de l’application.

  En Explorateur Stockage (Portail Azure) pour chaque fichier :
  
  1. Cliquez avec le bouton droit sur le fichier et sélectionnez **Propriétés**.
  1. Définissez le **ContentType** et sélectionnez le bouton **Enregistrer** .

Pour plus d’informations, consultez [Hébergement de sites web statiques dans le service Stockage Azure](/azure/storage/blobs/storage-blob-static-website).

### <a name="nginx"></a>Nginx

Le `nginx.conf` fichier suivant est simplifié pour montrer comment configurer Nginx pour envoyer le `index.html` fichier lorsqu’il ne peut pas trouver de fichier correspondant sur le disque.

```
events { }
http {
    server {
        listen 80;

        location / {
            root      /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

Lors de la définition de la [limite de débit de rafales Nginx](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) avec [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req) , Blazor WebAssembly les applications peuvent nécessiter une `burst` valeur de paramètre élevée pour prendre en charge le nombre relativement élevé de demandes effectuées par une application. Au départ, définissez la valeur sur au moins 60 :

```
http {
    server {
        ...

        location / {
            ...

            limit_req zone=one burst=60 nodelay;
        }
    }
}
```

Augmentez la valeur si outils de développement de navigateur ou outil de trafic réseau indique que les demandes reçoivent un code d’état *503-Service non disponible* .

Pour plus d’informations sur la configuration du serveur web Nginx de production, consultez [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) (Création de fichiers de configuration NGINX et NGINX Plus).

### <a name="nginx-in-docker"></a>Nginx dans Docker

Pour héberger Blazor dans l’ancrage à l’aide de nginx, configurez le fichier dockerfile pour utiliser l’image Nginx alpine. Mettez à jour le fichier dockerfile pour copier le `nginx.config` fichier dans le conteneur.

Ajoutez une ligne au fichier Dockerfile, comme indiqué dans l’exemple suivant :

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a>Apache

Pour déployer une Blazor WebAssembly application sur CentOS 7 ou version ultérieure :

1. Créez le fichier de configuration Apache. L’exemple suivant est un fichier de configuration simplifié ( `blazorapp.config` ) :

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType application/wasm .wasm
       AddType application/octet-stream .dll
   
       <Directory "/var/www/blazorapp">
           Options -Indexes
           AllowOverride None
       </Directory>

       <IfModule mod_deflate.c>
           AddOutputFilterByType DEFLATE text/css
           AddOutputFilterByType DEFLATE application/javascript
           AddOutputFilterByType DEFLATE text/html
           AddOutputFilterByType DEFLATE application/octet-stream
           AddOutputFilterByType DEFLATE application/wasm
           <IfModule mod_setenvif.c>
           BrowserMatch ^Mozilla/4 gzip-only-text/html
           BrowserMatch ^Mozilla/4.0[678] no-gzip
           BrowserMatch bMSIE !no-gzip !gzip-only-text/html
       </IfModule>
       </IfModule>

       ErrorLog /var/log/httpd/blazorapp-error.log
       CustomLog /var/log/httpd/blazorapp-access.log common
   </VirtualHost>
   ```

1. Placez le fichier de configuration Apache dans le `/etc/httpd/conf.d/` répertoire, qui est le répertoire de configuration Apache par défaut dans CentOS 7.

1. Placez les fichiers de l’application dans le `/var/www/blazorapp` répertoire (l’emplacement spécifié `DocumentRoot` dans le fichier de configuration).

1. Redémarrez le service Apache.

Pour plus d’informations, consultez [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) et [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).

### <a name="github-pages"></a>GitHub Pages

Pour gérer la réécriture d’URL, ajoutez un `wwwroot/404.html` fichier avec un script qui gère la redirection de la requête vers la `index.html` page. Pour obtenir un exemple, consultez le [ Blazor référentiel GitHub SteveSandersonMS/OnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):

* [`wwwroot/404.html`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/wwwroot/404.html)
* [Site actif](https://stevesandersonms.github.io/BlazorOnGitHubPages/))

Lorsque vous utilisez un site de projet plutôt qu’un site d’organisation, mettez à jour la `<base>` balise dans `wwwroot/index.html` . Définissez la `href` valeur de l’attribut sur le nom du dépôt github avec une barre oblique finale (par exemple, `/my-repository/` ). Dans le [ Blazor référentiel GitHub SteveSandersonMS/OnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages), la base `href` est mise à jour lors de la publication par le [ `.github/workflows/main.yml` fichier de configuration](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml).

> [!NOTE]
> Le [ Blazor référentiel GitHub SteveSandersonMS/OnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) n’est pas détenu, géré ou pris en charge par .net Foundation ou Microsoft.

## <a name="host-configuration-values"></a>Valeurs de configuration de l’hôte

les [ Blazor WebAssembly applications](xref:blazor/hosting-models#blazor-webassembly) peuvent accepter les valeurs de configuration d’hôte suivantes comme arguments de ligne de commande au moment de l’exécution dans l’environnement de développement.

### <a name="content-root"></a>Racine de contenu

L' `--contentroot` argument définit le chemin d’accès absolu au répertoire qui contient les fichiers de contenu de l’application ([racine du contenu](xref:fundamentals/index#content-root)). Dans les exemples suivants, `/content-root-path` est le chemin racine du contenu de l’application.

* Passez l’argument lors de l’exécution de l’application localement à une invite de commandes. À partir du répertoire de l’application, exécutez :

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* Ajoutez une entrée au fichier de l’application `launchSettings.json` dans le profil **IIS Express** . Ce paramètre est utilisé en cas d’exécution de l’application avec le débogueur Visual Studio et dans une invite de commandes avec `dotnet run`.

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* Dans Visual Studio, spécifiez l’argument dans **Propriétés**  >  **Déboguer** les arguments de l'  >  **application**. La définition de l’argument dans la page de propriétés de Visual Studio ajoute l’argument au `launchSettings.json` fichier.

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a>Base du chemin

L' `--pathbase` argument définit le chemin d’accès de base d’application pour une application exécutée localement avec un chemin d’URL relatif non racine (la `<base>` balise `href` est définie sur un chemin d’accès autre que `/` pour la mise en lots et la production). Dans les exemples suivants, `/relative-URL-path` est la base du chemin de l’application. Pour plus d’informations, consultez chemin de la base de l' [application](xref:blazor/host-and-deploy/index#app-base-path).

> [!IMPORTANT]
> Contrairement au chemin fourni au `href` de la balise `<base>`, n’incluez pas de barre oblique (`/`) quand vous passez la valeur d’argument `--pathbase`. Si vous spécifiez `<base href="/CoolApp/">` (inclut une barre oblique) comme chemin de base de l’application dans la balise `<base>`, passez `--pathbase=/CoolApp` (aucune barre oblique de fin) comme valeur d’argument de ligne de commande.

* Passez l’argument lors de l’exécution de l’application localement à une invite de commandes. À partir du répertoire de l’application, exécutez :

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* Ajoutez une entrée au fichier de l’application `launchSettings.json` dans le profil **IIS Express** . Ce paramètre est utilisé en cas d’exécution de l’application avec le débogueur Visual Studio et dans une invite de commandes avec `dotnet run`.

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* Dans Visual Studio, spécifiez l’argument dans **Propriétés**  >  **Déboguer** les arguments de l'  >  **application**. La définition de l’argument dans la page de propriétés de Visual Studio ajoute l’argument au `launchSettings.json` fichier.

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a>URLs

L’argument `--urls` définit les adresses IP ou les adresses d’hôtes avec les ports et protocoles sur lesquels il faut écouter les demandes.

* Passez l’argument lors de l’exécution de l’application localement à une invite de commandes. À partir du répertoire de l’application, exécutez :

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* Ajoutez une entrée au fichier de l’application `launchSettings.json` dans le profil **IIS Express** . Ce paramètre est utilisé en cas d’exécution de l’application avec le débogueur Visual Studio et dans une invite de commandes avec `dotnet run`.

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* Dans Visual Studio, spécifiez l’argument dans **Propriétés**  >  **Déboguer** les arguments de l'  >  **application**. La définition de l’argument dans la page de propriétés de Visual Studio ajoute l’argument au `launchSettings.json` fichier.

  ```console
  --urls=http://127.0.0.1:0
  ```

::: moniker range=">= aspnetcore-5.0"

## <a name="configure-the-trimmer"></a>Configurer l’outil de découpage

Blazor effectue un découpage en langage intermédiaire sur chaque version de mise en production pour supprimer l’IL inutile des assemblys de sortie. Pour plus d'informations, consultez <xref:blazor/host-and-deploy/configure-trimmer>.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="configure-the-linker"></a>Configurer l'éditeur de liens

Blazor effectue une liaison IL (Intermediate Language) sur chaque version de mise en production pour supprimer l’IL inutile des assemblys de sortie. Pour plus d'informations, consultez <xref:blazor/host-and-deploy/configure-linker>.

::: moniker-end

## <a name="custom-boot-resource-loading"></a>Chargement des ressources de démarrage personnalisé

Une Blazor WebAssembly application peut être initialisée avec la `loadBootResource` fonction pour remplacer le mécanisme de chargement des ressources de démarrage intégré. Utilisez `loadBootResource` pour les scénarios suivants :

* Autorisez les utilisateurs à charger des ressources statiques, telles que des données de fuseau horaire ou `dotnet.wasm` à partir d’un CDN.
* Chargez les assemblys compressés à l’aide d’une requête HTTP et décompressez-les sur le client pour les hôtes qui ne prennent pas en charge l’extraction du contenu compressé à partir du serveur.
* Alias des ressources à un autre nom en redirigeant chaque `fetch` requête vers un nouveau nom.

`loadBootResource` les paramètres s’affichent dans le tableau suivant.

| Paramètre    | Description |
| ------------ | ----------- |
| `type`       | Type de la ressource. Types permissables : `assembly` , `pdb` , `dotnetjs` , `dotnetwasm` , `timezonedata` |
| `name`       | Nom de la ressource. |
| `defaultUri` | URI relatif ou absolu de la ressource. |
| `integrity`  | Chaîne d’intégrité représentant le contenu attendu dans la réponse. |

`loadBootResource` retourne l’un des éléments suivants pour remplacer le processus de chargement :

* Chaîne d’URI. Dans l’exemple suivant ( `wwwroot/index.html` ), les fichiers suivants sont pris en charge à partir d’un CDN à l’adresse `https://my-awesome-cdn.com/` :

  * `dotnet.*.js`
  * `dotnet.wasm`
  * Données de fuseau horaire

  ```html
  ...

  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        console.log(`Loading: '${type}', '${name}', '${defaultUri}', '${integrity}'`);
        switch (type) {
          case 'dotnetjs':
          case 'dotnetwasm':
          case 'timezonedata':
            return `https://my-awesome-cdn.com/blazorwebassembly/3.2.0/${name}`;
        }
      }
    });
  </script>
  ```

* `Promise<Response>`. Transmettez le `integrity` paramètre dans un en-tête pour conserver le comportement de contrôle d’intégrité par défaut.

  L’exemple suivant ( `wwwroot/index.html` ) ajoute un en-tête HTTP personnalisé aux demandes sortantes et passe `integrity` le paramètre à l' `fetch` appel :
  
  ```html
  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        return fetch(defaultUri, { 
          cache: 'no-cache',
          integrity: integrity,
          headers: { 'MyCustomHeader': 'My custom value' }
        });
      }
    });
  </script>
  ```

* `null`/`undefined`, ce qui entraîne le comportement de chargement par défaut.

Les sources externes doivent retourner les en-têtes CORS requis pour les navigateurs afin d’autoriser le chargement des ressources Cross-Origin. Les CDN fournissent généralement les en-têtes requis par défaut.

Il vous suffit de spécifier des types pour les comportements personnalisés. Les types non spécifiés à `loadBootResource` sont chargés par le Framework en fonction de leurs comportements de chargement par défaut.

## <a name="change-the-filename-extension-of-dll-files"></a>Modifier l’extension de nom de fichier des fichiers DLL

Si vous avez besoin de modifier les extensions de nom de fichier des fichiers publiés de l’application `.dll` , suivez les instructions de cette section.

Après avoir publié l’application, utilisez un script d’interpréteur de commandes ou un pipeline de build DevOps pour renommer les `.dll` fichiers afin d’utiliser une extension de fichier différente. Ciblez les `.dll` fichiers dans le `wwwroot` Répertoire de la sortie publiée de l’application (par exemple, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot` ).

Dans les exemples suivants, les `.dll` fichiers sont renommés pour utiliser l' `.bin` extension de fichier.

Sur Windows :

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

Si des ressources de service Worker sont également utilisées, ajoutez la commande suivante :

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

Sur Linux ou macOS :

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

Si des ressources de service Worker sont également utilisées, ajoutez la commande suivante :

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
Pour utiliser une extension de fichier différente de `.bin` , remplacez `.bin` dans les commandes précédentes.

Pour résoudre les fichiers et compressés `blazor.boot.json.gz` `blazor.boot.json.br` , adoptez l’une des approches suivantes :

* Supprimez les `blazor.boot.json.gz` fichiers et compressés `blazor.boot.json.br` . La compression est désactivée avec cette approche.
* Recompressez le fichier mis à jour `blazor.boot.json` .

Les instructions précédentes s’appliquent également lorsque les ressources de service Worker sont en cours d’utilisation. Supprimez ou recompressez `wwwroot/service-worker-assets.js.br` et `wwwroot/service-worker-assets.js.gz` . Dans le cas contraire, les vérifications de l’intégrité des fichiers échouent dans le navigateur.

L’exemple Windows suivant utilise un script PowerShell placé à la racine du projet.

`ChangeDLLExtensions.ps1:`:

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

Si des ressources de service Worker sont également utilisées, ajoutez la commande suivante :

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

Dans le fichier projet, le script est exécuté après la publication de l’application :

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

> [!NOTE]
> Lorsque vous renommez et chargez en différé les mêmes assemblys, consultez les instructions dans <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files> .

## <a name="resolve-integrity-check-failures"></a>Résoudre les échecs de vérification de l’intégrité

Lorsque Blazor WebAssembly télécharge les fichiers de démarrage d’une application, il demande au navigateur d’effectuer des contrôles d’intégrité sur les réponses. Elle utilise les informations du `blazor.boot.json` fichier pour spécifier les valeurs de hachage SHA-256 attendues pour `.dll` , `.wasm` et d’autres fichiers. Cela est bénéfique pour les raisons suivantes :

* Cela vous garantit que vous ne risquez pas de charger un ensemble incohérent de fichiers, par exemple si un nouveau déploiement est appliqué à votre serveur Web pendant que l’utilisateur est en train de télécharger les fichiers d’application. Des fichiers incohérents peuvent entraîner un comportement indéfini.
* Il garantit que le navigateur de l’utilisateur ne met jamais en cache les réponses incohérentes ou non valides, ce qui peut empêcher le démarrage de l’application, même s’il actualise la page manuellement.
* Cela permet de mettre en cache les réponses et de ne pas même vérifier les modifications côté serveur tant que les hachages SHA-256 attendus eux-mêmes ne sont pas modifiés, de sorte que les chargements de page suivants impliquent moins de demandes et s’exécutent beaucoup plus rapidement.

Si votre serveur Web renvoie des réponses qui ne correspondent pas aux hachages SHA-256 attendus, vous verrez une erreur semblable à celle qui suit s’afficher dans la console de développeur du navigateur :

```
Failed to find a valid digest in the 'integrity' attribute for resource 'https://myapp.example.com/_framework/MyBlazorApp.dll' with computed SHA-256 integrity 'IIa70iwvmEg5WiDV17OpQ5eCztNYqL186J56852RpJY='. The resource has been blocked.
```

Dans la plupart des cas, il ne s’agit *pas* d’un problème de vérification de l’intégrité. Au lieu de cela, cela signifie qu’il existe un autre problème et que la vérification de l’intégrité vous avertit de cet autre problème.

### <a name="diagnosing-integrity-problems"></a>Diagnostic des problèmes d’intégrité

Quand une application est générée, le `blazor.boot.json` manifeste généré décrit les hachages SHA-256 de vos ressources de démarrage (par exemple,, `.dll` et d' `.wasm` autres fichiers) au moment où la sortie de la génération est générée. Le contrôle d’intégrité réussit tant que les hachages SHA-256 dans `blazor.boot.json` correspondent aux fichiers remis au navigateur.

Causes courantes de cet échec :

 * La réponse du serveur Web est une erreur (par exemple, *404-introuvable* ou une *erreur de serveur 500-Internal* ) au lieu du fichier demandé par le navigateur. Cela est signalé par le navigateur comme un échec de vérification de l’intégrité et non comme un échec de réponse.
 * Une modification a été apportée au contenu des fichiers entre la génération et la remise des fichiers dans le navigateur. Cela peut se produire :
   * Si vous ou les outils de génération modifiez manuellement la sortie de la génération.
   * Si certains aspects du processus de déploiement ont modifié les fichiers. Par exemple, si vous utilisez un mécanisme de déploiement basé sur git, gardez à l’esprit que git convertit en toute transparence les fins de ligne de style Windows en terminaisons de ligne de style UNIX si vous validez des fichiers sur Windows et que vous les consultez sur Linux. La modification des fins de ligne de fichier modifie les hachages SHA-256. Pour éviter ce problème, envisagez [ `.gitattributes` d’utiliser pour traiter les artefacts de build comme des `binary` fichiers](https://git-scm.com/book/en/v2/Customizing-Git-Git-Attributes).
   * Le serveur Web modifie le contenu du fichier dans le cadre de ses services. Par exemple, certains réseaux de distribution de contenu (CDN) tentent automatiquement de [réduire](xref:client-side/bundling-and-minification#minification) html, ce qui les modifie. Vous devrez peut-être désactiver ces fonctionnalités.

Pour diagnostiquer les éléments suivants dans votre cas :

 1. Notez le fichier qui déclenche l’erreur en lisant le message d’erreur.
 1. Ouvrez les outils de développement de votre navigateur et recherchez dans l’onglet *réseau* . Si nécessaire, rechargez la page pour afficher la liste des demandes et des réponses. Recherchez le fichier qui déclenche l’erreur dans cette liste.
 1. Vérifiez le code d’état HTTP dans la réponse. Si le serveur retourne une valeur autre que *200-OK* (ou un autre code d’État 2xx), vous pouvez diagnostiquer un problème côté serveur. Par exemple, le code d’état 403 signifie qu’il y a un problème d’autorisation, alors que le code d’état 500 signifie que le serveur échoue de manière non spécifiée. Consultez les journaux côté serveur pour diagnostiquer et corriger l’application.
 1. Si le code d’État est *200-OK* pour la ressource, examinez le contenu de la réponse dans les outils de développement du navigateur et vérifiez que le contenu correspond aux données attendues. Par exemple, un problème courant consiste à configurer le routage de manière inhabituelle afin que les demandes retournent vos `index.html` données même pour d’autres fichiers. Assurez-vous que `.wasm` les réponses aux requêtes sont des binaires Webassembly et que les réponses aux `.dll` requêtes sont des binaires d’assembly .net. Si ce n’est pas le cas, vous pouvez diagnostiquer un problème de routage côté serveur.

Si vous confirmez que le serveur retourne des données correctes plausibly, il doit y avoir une autre modification du contenu entre la génération et la remise du fichier. Pour examiner ce qui suit :

 * Examinez le chaîne d’outils de build et le mécanisme de déploiement en cas de modification des fichiers après la génération des fichiers. C’est le cas, par exemple, lorsque git transforme les fins de ligne de fichier, comme décrit précédemment.
 * Examinez le serveur Web ou la configuration CDN au cas où ils seraient configurés pour modifier les réponses de manière dynamique (par exemple, en tentant de réduire HTML). Il convient que le serveur Web implémente la compression HTTP (par exemple, en retournant `content-encoding: br` ou `content-encoding: gzip` ), car cela n’affecte pas le résultat après la décompression. Toutefois, il *n’est pas correct* pour le serveur Web de modifier les données non compressées.

### <a name="disable-integrity-checking-for-non-pwa-apps"></a>Désactiver le contrôle d’intégrité pour les applications non-PWA

Dans la plupart des cas, ne désactivez pas la vérification de l’intégrité. La désactivation de la vérification de l’intégrité ne résout pas le problème sous-jacent qui a provoqué les réponses inattendues et entraîne la perte des avantages répertoriés plus haut.

Dans certains cas, le serveur Web ne peut pas être reporté pour retourner des réponses cohérentes et vous n’avez aucun choix, mais vous pouvez désactiver les contrôles d’intégrité. Pour désactiver les contrôles d’intégrité, ajoutez ce qui suit à un groupe de propriétés dans le Blazor WebAssembly fichier du projet `.csproj` :

```xml
<BlazorCacheBootResources>false</BlazorCacheBootResources>
```

`BlazorCacheBootResources` désactive également Blazor le comportement par défaut de la mise en cache des `.dll` `.wasm` fichiers, et d’autres fichiers en fonction de leurs hachages SHA-256, car la propriété indique que les hachages SHA-256 ne peuvent pas être basés sur l’exactitude. Même avec ce paramètre, le cache HTTP normal du navigateur peut toujours mettre en cache ces fichiers, mais cela dépend de la configuration de votre serveur Web et des `cache-control` en-têtes qu’il dessert.

> [!NOTE]
> La `BlazorCacheBootResources` propriété ne désactive pas les vérifications de l’intégrité des [applications Web progressives (PWA)](xref:blazor/progressive-web-app). Pour plus d’informations sur PWA, consultez la section [Disable Integrity checking for PWA](#disable-integrity-checking-for-pwas) .

### <a name="disable-integrity-checking-for-pwas"></a>Désactiver la vérification de l’intégrité pour PWA

Blazorle modèle d’application Web progressive (PWA) contient un `service-worker.published.js` fichier suggéré qui est responsable de l’extraction et du stockage des fichiers d’application en vue d’une utilisation hors connexion. Il s’agit d’un processus distinct du mécanisme normal de démarrage de l’application et d’une logique de vérification de l’intégrité distincte.

À l’intérieur du `service-worker.published.js` fichier, la ligne suivante est présente :

```javascript
.map(asset => new Request(asset.url, { integrity: asset.hash }));
```

Pour désactiver la vérification de l’intégrité, supprimez le `integrity` paramètre en remplaçant la ligne par ce qui suit :

```javascript
.map(asset => new Request(asset.url));
```

Là encore, la désactivation de la vérification de l’intégrité signifie que vous perdez les garanties de sécurité offertes par le contrôle d’intégrité. Par exemple, il existe un risque que si le navigateur de l’utilisateur met en cache l’application au moment précis où vous déployez une nouvelle version, il peut mettre en cache certains fichiers de l’ancien déploiement et d’autres du nouveau déploiement. Si cela se produit, l’application est bloquée dans un état endommagé jusqu’à ce que vous déployiez une autre mise à jour.
