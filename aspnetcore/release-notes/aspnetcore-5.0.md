---
title: Nouveautés de ASP.NET Core 5,0
author: rick-anderson
description: Découvrez les nouvelles fonctionnalités de ASP.NET Core 5,0.
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
no-loc:
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
- Kestrel
uid: aspnetcore-5.0
ms.openlocfilehash: 1f377f3be54ed8837d2857aed64c2d055ed9f582
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422585"
---
# <a name="whats-new-in-aspnet-core-50"></a>Nouveautés de ASP.NET Core 5,0

Cet article met en évidence les modifications les plus importantes apportées à ASP.NET Core 5,0 avec des liens vers la documentation appropriée.

## <a name="aspnet-core-mvc-and-no-locrazor-improvements"></a>ASP.NET Core MVC et Razor améliorations

### <a name="model-binding-datetime-as-utc"></a>Date et heure de liaison de modèle UTC

La liaison de modèle prend désormais en charge la liaison de chaînes de temps UTC à `DateTime` . Si la requête contient une chaîne d’heure UTC, la liaison de modèle la lie à un UTC `DateTime` . Par exemple, la chaîne de temps suivante est liée au temps universel (UTC) `DateTime` : `https://example.com/mycontroller/myaction?time=2019-06-14T02%3A30%3A04.0576719Z`

### <a name="model-binding-and-validation-with-c-9-record-types"></a>Liaison de modèle et validation avec les types d’enregistrements C# 9

Les [types d’enregistrements C# 9](/dotnet/csharp/whats-new/csharp-9#record-types) peuvent être utilisés avec la liaison de modèle dans un contrôleur MVC ou une Razor page. Les types d’enregistrements sont un bon moyen de modéliser les données transmises sur le réseau.

Par exemple, le code suivant `PersonController` utilise le `Person` type d’enregistrement avec la liaison de modèle et la validation de formulaire :

```csharp
public record Person([Required] string Name, [Range(0, 150)] int Age);

public class PersonController
{
   public IActionResult Index() => View();

   [HttpPost]
   public IActionResult Index(Person person)
   {
          // ...
   }
}
```

Le fichier `Person/Index.cshtml` :

```cshtml
@model Person

Name: <input asp-for="Model.Name" />
<span asp-validation-for="Model.Name" />

Age: <input asp-for="Model.Age" />
<span asp-validation-for="Model.Age" />
```

### <a name="improvements-to-dynamicroutevaluetransformer"></a>Améliorations apportées à DynamicRouteValueTransformer

ASP.NET Core 3,1 introduit <xref:Microsoft.AspNetCore.Mvc.Routing.DynamicRouteValueTransformer> comme un moyen d’utiliser un point de terminaison personnalisé pour sélectionner dynamiquement une action ou une page du contrôleur MVC Razor . ASP.NET Core applications 5,0 peuvent passer l’État à un `DynamicRouteValueTransformer` et filtrer l’ensemble des points de terminaison choisis.

### <a name="miscellaneous"></a>Divers

* L’attribut [[compare]](xref:System.ComponentModel.DataAnnotations.CompareAttribute) peut être appliqué aux propriétés sur un Razor modèle de page.
* Les paramètres et les propriétés liés à partir du corps sont considérés comme étant requis par défaut. <!-- Review: How is this different from 3.1
The validation system in .NET Core 3.0 and later treats non-nullable parameters or bound properties as if they had a [Required] attribute.
see https://docs.microsoft.com/aspnet/core/mvc/models/validation?view=aspnetcore-3.1   
-->

## <a name="web-api"></a>API web

### <a name="openapi-specification-on-by-default"></a>Spécification OpenAPI activée par défaut

La [spécification openapi](http://spec.openapis.org/oas/v3.0.3) est une norme du secteur pour décrire les API http et les intégrer dans des processus métier complexes ou avec des tiers. OpenAPI est largement pris en charge par tous les fournisseurs de Cloud et de nombreux registres d’API. Les applications qui émettent des documents OpenAPI à partir d’API Web ont une variété de nouvelles opportunités dans lesquelles ces API peuvent être utilisées. En partenariat avec les maintenateurs du projet open source [Swashbuckle. AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/), le modèle d’API ASP.net Core contient une dépendance NuGet sur [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore). Swashbuckle est un package NuGet Open source populaire qui émet dynamiquement des documents OpenAPI. Swashbuckle fait cela en passant par les contrôleurs d’API et en générant le document OpenAPI au moment de l’exécution, ou au moment de la génération à l’aide de l’interface CLI Swashbuckle.

Dans ASP.NET Core 5,0, les modèles d’API Web activent la prise en charge de OpenAPI par défaut. Pour désactiver OpenAPI :

* Depuis la ligne de commande :

    ```dotnetcli
    dotnet new webapi --no-openapi true
    ```
* Dans Visual Studio : décochez la case **activer la prise en charge de openapi**.

Tous les fichiers *. csproj* créés pour les projets d’API Web contiennent la référence de package NuGet [Swashbuckle. AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/) .

```xml
<ItemGroup>
    <PackageReference Include="Swashbuckle.AspNetCore" Version="5.5.1" />
</ItemGroup>
```

Le code généré par le modèle contient du code dans `Startup.ConfigureServices` qui active la génération de documents openapi :

[!code-csharp[](~/release-notes/sample/StartupSwagger.cs?name=snippet)]

La `Startup.Configure` méthode ajoute l’intergiciel Swashbuckle, qui permet :

* Processus de génération de documents.
* Page d’interface utilisateur Swagger par défaut en mode de développement.

Le code généré par le modèle n’exposera pas accidentellement la description de l’API lors de la publication en production.

[!code-csharp[](~/release-notes/sample/StartupSwagger.cs?name=snippet2)]

#### <a name="azure-api-management-import"></a>Importation de gestion des API Azure

Quand ASP.NET Core projets d’API activent OpenAPI, Visual Studio 2019, version 16,8 et ultérieure, publient automatiquement une étape supplémentaire dans le processus de publication. Les développeurs qui utilisent la [gestion des API Azure](xref:tutorials/publish-to-azure-api-management-using-vs) ont la possibilité d’importer automatiquement les API dans gestion des API Azure pendant le processus de publication :

![Importation et publication de gestion des API Azure](~/release-notes/static/publish-to-apim.png)

### <a name="better-launch-experience-for-web-api-projects"></a>Meilleure expérience de lancement pour les projets d’API Web

Avec OpenAPI activé par défaut, l’expérience de lancement d’application (F5) pour les développeurs d’API Web augmente considérablement. Avec ASP.NET Core 5,0, le modèle d’API Web est préconfiguré pour charger la page d’interface utilisateur Swagger. La page d’interface utilisateur Swagger fournit la documentation ajoutée pour l’API publiée et permet de tester les API en un seul clic.

![vue Swagger/index.html](~/release-notes/static/swagger-ui-page-rc1.png)

## Blazor

### <a name="performance-improvements"></a>Optimisation des performances

Pour .NET 5, nous avons apporté des améliorations significatives aux Blazor WebAssembly performances de l’exécution avec un focus spécifique sur le rendu de l’interface utilisateur complexe et la SÉRIALISATION JSON. Dans nos tests de performances, Blazor WebAssembly dans .net 5, il s’agit de deux à trois fois plus rapides pour la plupart des scénarios. Pour plus d’informations, consultez le [Blog ASP.net : ASP.net Core des mises à jour dans .net 5 Release Candidate 1](https://devblogs.microsoft.com/aspnet/asp-net-core-updates-in-net-5-release-candidate-1/#blazor-webassembly-performance-improvements).

### <a name="css-isolation"></a>Isolation CSS

Blazor prend désormais en charge la définition de styles CSS dont la portée est limitée à un composant donné. Les styles CSS spécifiques aux composants permettent de mieux comprendre les styles dans une application et d’éviter les effets secondaires involontaires des styles globaux. Pour plus d'informations, consultez <xref:blazor/components/css-isolation>.

### <a name="new-inputfile-component"></a>Nouveau `InputFile` composant

Le `InputFile` composant permet de lire un ou plusieurs fichiers sélectionnés par un utilisateur pour le téléchargement. Pour plus d'informations, consultez <xref:blazor/file-uploads>.

### <a name="new-inputradio-and-inputradiogroup-components"></a>Nouveaux `InputRadio` `InputRadioGroup` composants et

Blazor a des composants intégrés `InputRadio` et `InputRadioGroup` qui simplifient la liaison de données aux groupes de cases d’option avec validation intégrée. Pour plus d'informations, consultez <xref:blazor/forms-validation>.

### <a name="component-virtualization"></a>Virtualisation de composant

Améliorez les performances perçues du rendu des composants à l’aide de la Blazor prise en charge intégrée de la virtualisation du Framework. Pour plus d'informations, consultez <xref:blazor/forms-validation#radio-buttons>.

### <a name="ontoggle-event-support"></a>`ontoggle` prise en charge des événements

Blazor les événements prennent désormais en charge l' `ontoggle` événement DOM. Pour plus d'informations, consultez <xref:blazor/components/event-handling#event-argument-types>.

### <a name="set-ui-focus-in-no-locblazor-apps"></a>Définir le focus de l’interface utilisateur dans les Blazor applications

Utilisez la `FocusAsync` méthode pratique sur les références d’éléments pour définir le focus de l’interface utilisateur sur cet élément. Pour plus d'informations, consultez <xref:blazor/components/event-handling#focus-an-element>.

### <a name="custom-validation-class-attributes"></a>Attributs de classe de validation personnalisée

Les noms des classes de validation personnalisées sont utiles lors de l’intégration avec des frameworks CSS, tels que bootstrap. Pour plus d'informations, consultez <xref:blazor/forms-validation#custom-validation-class-attributes>.

### <a name="iasyncdisposable-support"></a>Support IAsyncDisposable

Blazor les composants prennent désormais en charge l' <xref:System.IAsyncDisposable> interface pour la version asynchrone des ressources allouées.

### <a name="javascript-isolation-and-object-references"></a>Isolation JavaScript et références d’objets

Blazor active l’isolation JavaScript dans les [modules JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)standard. Pour plus d'informations, consultez <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.

### <a name="form-components-support-display-name"></a>Nom complet de la prise en charge des composants de formulaire

Les composants intégrés suivants prennent en charge les noms complets avec le `DisplayName` paramètre :

* `InputDate`
* `InputNumber`
* `InputSelect`

Pour plus d'informations, consultez <xref:blazor/forms-validation#display-name-support>.

### <a name="catch-all-route-parameters"></a>Paramètres d’itinéraire de rattrapage

Les paramètres d’itinéraire Catch-All, qui capturent les chemins d’accès dans plusieurs limites de dossiers, sont pris en charge dans les composants. Pour plus d'informations, consultez <xref:blazor/fundamentals/routing#catch-all-route-parameters>.

### <a name="debugging-improvements"></a>Améliorations du débogage

Le débogage Blazor WebAssembly des applications est amélioré dans ASP.NET Core 5,0. En outre, le débogage est désormais pris en charge sur Visual Studio pour Mac. Pour plus d'informations, consultez <xref:blazor/debug>.

### <a name="microsoft-no-locidentity-v20-and-msal-v20"></a>Microsoft Identity v 2.0 et MSAL v 2.0

Blazor la sécurité utilise désormais Microsoft Identity v 2.0 ( [`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web) et [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI) ) et MSAL v 2.0. Pour plus d’informations, consultez les rubriques relatives à la [ Blazor sécurité et au Identity nœud](xref:blazor/security/index).

### <a name="protected-browser-storage-for-no-locblazor-server"></a>Stockage du navigateur protégé pour Blazor Server

Blazor Server les applications peuvent désormais utiliser la prise en charge intégrée pour le stockage de l’état de l’application dans le navigateur qui a été protégé contre la falsification à l’aide de la protection des données ASP.NET Core. Les données peuvent être stockées dans le stockage du navigateur local ou dans un stockage de session. Pour plus d'informations, consultez <xref:blazor/state-management>.

### <a name="no-locblazor-webassembly-prerendering"></a>Blazor WebAssembly préaffichant

L’intégration des composants est améliorée entre les modèles d’hébergement, et les Blazor WebAssembly applications peuvent désormais prérestituer la sortie sur le serveur. <!-- UNCOMMENT AFTER https://github.com/dotnet/AspNetCore.Docs/pull/19887 MERGES: For more information, see <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps> and <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>. -->

### <a name="trimminglinking-improvements"></a>Améliorations de la limitation/liaison

Blazor WebAssembly effectue le découpage/la liaison de langage intermédiaire au cours d’une génération pour supprimer l’IL inutile des assemblys de sortie de l’application. Avec la sortie de ASP.NET Core 5,0, Blazor WebAssembly effectue une suppression améliorée avec des options de configuration supplémentaires. Pour plus d’informations, consultez <xref:blazor/host-and-deploy/configure-trimmer> et [options de suppression](/dotnet/core/deploying/trimming-options).

### <a name="browser-compatibility-analyzer"></a>Analyseur de compatibilité des navigateurs

Blazor WebAssembly les applications ciblent la surface d’exposition complète de l’API .NET, mais toutes les API .NET ne sont pas prises en charge sur webassembly en raison des contraintes du bac à sable (sandbox). Les API non prises en charge sont levées <xref:System.PlatformNotSupportedException> lors de l’exécution sur Webassembly. Un analyseur de compatibilité de plateforme avertit le développeur lorsque l’application utilise des API qui ne sont pas prises en charge par les plateformes cibles de l’application. Pour plus d'informations, consultez <xref:blazor/components/class-libraries#browser-compatibility-analyzer-for-blazor-webassembly>.

### <a name="lazy-load-assemblies"></a>Charger des assemblys en différé

Blazor WebAssembly les performances de démarrage de l’application peuvent être améliorées en différant le chargement de certains assemblys d’application jusqu’à ce qu’ils soient nécessaires. Pour plus d'informations, consultez <xref:blazor/webassembly-lazy-load-assemblies>.

### <a name="updated-globalization-support"></a>Support de globalisation mis à jour

La prise en charge de la globalisation est disponible pour Blazor WebAssembly basée sur les composants internationaux pour Unicode (ICU). Pour plus d'informations, consultez <xref:blazor/globalization-localization>.

## <a name="grpc"></a>gRPC

De nombreuses améliorations de la préformation ont été apportées dans [gRPC](https://grpc.io/). Pour plus d’informations, consultez [améliorations des performances de gRPC dans .net 5](https://devblogs.microsoft.com/aspnet/grpc-performance-improvements-in-net-5/).

Pour plus d’informations sur les gRPC, consultez <xref:grpc/index> .

## SignalR

### <a name="no-locsignalr-hub-filters"></a>SignalR Filtres de concentrateur

SignalR Les filtres de concentrateur, appelés pipelines Hub dans ASP.NET SignalR , sont une fonctionnalité qui permet au code de s’exécuter avant et après l’appel des méthodes de concentrateur. L’exécution de code avant et après l’appel de méthodes de concentrateur est semblable à la façon dont l’intergiciel peut exécuter du code avant et après une requête HTTP. Les utilisations courantes incluent la journalisation, la gestion des erreurs et la validation d’argument.

Pour plus d’informations, consultez [utiliser des filtres de SignalR concentrateur dans ASP.net Core ](xref:signalr/hub-filters).

### <a name="no-locsignalr-parallel-hub-invocations"></a>SignalR appels de concentrateur parallèles

ASP.NET Core SignalR est désormais en capacité de gérer les appels de concentrateur parallèle. Le comportement par défaut peut être modifié pour permettre aux clients d’appeler plusieurs méthodes de concentrateur à la fois :

[!code-csharp[](~/release-notes/sample/StartupSignalRhubs.cs?name=snippet)]

### <a name="added-messagepack-support-in-no-locsignalr-java-client"></a>Ajout de la prise en charge de Messagepack dans SignalR java client

Un nouveau package, [com. Microsoft. signalr. messagepack](https://mvnrepository.com/artifact/com.microsoft.signalr.messagepack), ajoute la prise en charge de messagepack au SignalR client Java. Pour utiliser le protocole MessagePack Hub, ajoutez `.withHubProtocol(new MessagePackHubProtocol())` au générateur de connexions :

```java
HubConnection hubConnection = HubConnectionBuilder.create(
                           "http://localhost:53353/MyHub")
               .withHubProtocol(new MessagePackHubProtocol())
               .build();
```

<!--
See [Update SignalR code](xref:migration/31-to-50#signalr) for migration instructions.
-->

## Kestrel

* Points de terminaison rechargeables via la configuration : Kestrel peut détecter les modifications apportées à la configuration passée à [ KestrelServerOptions.Configurer](xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Configure%2A) et les dissocier des points de terminaison existants et les lier aux nouveaux points de terminaison sans nécessiter le redémarrage de l’application lorsque le `reloadOnChange` paramètre a la valeur `true` . Par défaut, lorsque <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults%2A> vous utilisez ou <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> , est Kestrel lié à la sous-section de configuration [« Kestrel »](https://github.com/dotnet/aspnetcore/blob/7e9e03b70124784b1de5564c573bd65cdaccbfcc/src/DefaultBuilder/src/WebHost.cs#L226) avec l' `reloadOnChange` option activé. Les applications doivent réussir `reloadOnChange: true` lors `KestrelServerOptions.Configure` d’un appel manuel pour accéder aux points de terminaison rechargeables.
* Améliorations des en-têtes de réponse HTTP/2. Pour plus d’informations, consultez [améliorations des performances](#performance-improvements) dans la section suivante.
* Prise en charge des types de points de terminaison supplémentaires dans le transport de Sockets : ajout à la nouvelle API introduite dans <xref:System.Net.Sockets?displayProperty=nameWithType> , le transport par défaut des sockets dans [Kestrel](xref:fundamentals/servers/kestrel) permet la liaison aux descripteurs de fichiers existants et aux sockets de domaine UNIX. La prise en charge de la liaison aux descripteurs de fichiers existants permet l’utilisation de l' `Systemd` intégration existante sans nécessiter le `libuv` transport.
* Décodage d’en-tête personnalisé dans Kestrel : les applications peuvent spécifier celles <xref:System.Text.Encoding> à utiliser pour interpréter les en-têtes entrants en fonction du nom d’en-tête au lieu de la valeur par défaut UTF-8. Définissez l'option  <!--<xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.RequestHeaderEncodingSelector> --> `Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.RequestHeaderEncodingSelector` propriété permettant de spécifier l’encodage à utiliser :
 
  ```csharp
  public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                options.RequestHeaderEncodingSelector = encoding =>
                {
                      return encoding switch
                      {
                          "Host" => System.Text.Encoding.Latin1,
                          _ => System.Text.Encoding.UTF8,
                      };
                };
            });
            webBuilder.UseStartup<Startup>();
        });
  ```

### <a name="no-lockestrel-endpoint-specific-options-via-configuration"></a>Kestrel options spécifiques au point de terminaison via la configuration

Une prise en charge a été ajoutée pour la configuration des Kestrel options spécifiques aux points de terminaison par [configuration](xref:fundamentals/configuration/index). Les configurations spécifiques aux points de terminaison incluent les éléments suivants :

* Protocoles HTTP utilisés
* Protocoles TLS utilisés
* Certificat sélectionné
* Mode de certificat client

La configuration permet de spécifier le certificat qui est sélectionné en fonction du nom de serveur spécifié. Le nom du serveur fait partie de l’extension de Indication du nom du serveur (SNI) au protocole TLS, comme indiqué par le client. Kestrella configuration de prend également en charge un préfixe générique dans le nom d’hôte.

L’exemple suivant montre comment spécifier un point de terminaison spécifique à l’aide d’un fichier de configuration :

```json
{
  "Kestrel": {
    "Endpoints": {
      "EndpointName": {
        "Url": "https://*",
        "Sni": {
          "a.example.org": {
            "Protocols": "Http1AndHttp2",
            "SslProtocols": [ "Tls11", "Tls12"],
            "Certificate": {
              "Path": "testCert.pfx",
              "Password": "testPassword"
            },
            "ClientCertificateMode" : "NoCertificate"
          },
          "*.example.org": {
            "Certificate": {
              "Path": "testCert2.pfx",
              "Password": "testPassword"
            }
          },
          "*": {
            // At least one sub-property needs to exist per
            // SNI section or it cannot be discovered via
            // IConfiguration
            "Protocols": "Http1",
          }
        }
      }
    }
  }
}
```

Indication du nom du serveur (SNI) est une extension TLS pour inclure un domaine virtuel dans le cadre de la négociation SSL. Cela signifie que le nom de domaine virtuel, ou un nom d’hôte, peut être utilisé pour identifier le point de terminaison réseau.

## <a name="performance-improvements"></a>Optimisation des performances

### <a name="http2"></a>HTTP/2

* Réductions significatives des allocations dans le chemin de code HTTP/2.
* Prise en charge de la [compression dynamique HPack](https://tools.ietf.org/html/rfc7541) des en-têtes de réponse http/2 dans [Kestrel](xref:fundamentals/servers/kestrel) . Pour plus d’informations, consultez [taille de table d’en-tête](xref:fundamentals/servers/kestrel#header-table-size) et [HPACK : la fonctionnalité de désinstallation sans assistance (fonctionnalité) de http/2](https://blog.cloudflare.com/hpack-the-silent-killer-feature-of-http-2/).
* Envoi de trames PING HTTP/2 : HTTP/2 est un mécanisme permettant d’envoyer des trames PING pour s’assurer qu’une connexion inactive est toujours fonctionnelle. Garantir une connexion viable est particulièrement utile lorsque vous travaillez avec des flux à long terme qui sont souvent inactifs, mais qui ne voient que l’activité de façon intermittente, par exemple, les flux gRPC. Les applications peuvent envoyer des images PING périodiques dans [Kestrel](xref:fundamentals/servers/kestrel) en définissant des limites sur <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions> :

   ```csharp
   public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                options.Limits.Http2.KeepAlivePingInterval =
                                               TimeSpan.FromSeconds(10);
                options.Limits.Http2.KeepAlivePingTimeout =
                                               TimeSpan.FromSeconds(1);
            });
            webBuilder.UseStartup<Startup>();
        });
   ```
   <!-- review: KeepAlivePingInterval not found in RC1. Try testing with RC1. See https://github.com/dotnet/aspnetcore/pull/22565/files see C:/Users/riande/source/repos/WebApplication128/WebApplication128 -->

### <a name="containers"></a>Containers

Avant .NET 5,0, la génération et la publication d’un *fichier dockerfile* pour une application ASP.net Core nécessitait l’extraction de l’ensemble des kit SDK .net Core et de l’image ASP.net core. Avec cette version, l’extraction des octets des images du SDK est réduite et les octets tirés pour l’image ASP.NET Core sont en grande partie éliminés. Pour plus d’informations, consultez [ce commentaire de problème GitHub](https://github.com/dotnet/dotnet-docker/issues/1814#issuecomment-625294750).

## <a name="authentication-and-authorization"></a>Authentification et autorisation

### <a name="azure-active-directory-authentication-with-microsoftno-locidentityweb"></a>Azure Active Directory l’authentification auprès de Microsoft. Identity . Internet

Les modèles de projet ASP.NET Core s’intègrent désormais <xref:Microsoft.Identity.Web?displayProperty=fullName> à pour gérer l’authentification avec le [répertoire d’activité Azure](/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD). [Microsoft. Identity . Le package Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) fournit les éléments suivants :

* Une meilleure expérience pour l’authentification via Azure AD.
* Un moyen plus simple d’accéder aux ressources Azure pour le compte de vos utilisateurs, y compris [Microsoft Graph](/graph/overview). Consultez le [Microsoft. Identity Exemple Web](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2), qui commence par une connexion de base et passe par une architecture mutualisée, à l’aide d’API Azure, à l’aide d’Microsoft Graph et à la protection de vos propres API. `Microsoft.Identity.Web` est disponible avec .NET 5.

### <a name="allow-anonymous-access-to-an-endpoint"></a>Autoriser l’accès anonyme à un point de terminaison

La `AllowAnonymous` méthode d’extension autorise un accès anonyme à un point de terminaison :

[!code-csharp[](~/release-notes/sample/StartupAnonEndpoint.cs?name=snippet)]

### <a name="custom-handling-of-authorization-failures"></a>Gestion personnalisée des échecs d’autorisation

La gestion personnalisée des échecs d’autorisation est désormais plus facile avec la nouvelle interface [IAuthorizationMiddlewareResultHandler](https://github.com/dotnet/aspnetcore/blob/v5.0.0-rc.1.20451.17/src/Security/Authorization/Policy/src/IAuthorizationMiddlewareResultHandler.cs) appelée par l' [intergiciel (middleware](xref:fundamentals/middleware/index)) [d’autorisation](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A) . L’implémentation par défaut reste la même, mais un gestionnaire personnalisé peut être inscrit dans [injection de dépendances, qui autorise les réponses HTTP personnalisées en fonction de la raison de l’échec de l’autorisation. Consultez [cet exemple](https://github.com/dotnet/aspnetcore/blob/master/src/Security/samples/CustomAuthorizationFailureResponse/Authorization/SampleAuthorizationMiddlewareResultHandler.cs) qui illustre l’utilisation de `IAuthorizationMiddlewareResultHandler` .

### <a name="authorization-when-using-endpoint-routing"></a>Autorisation lors de l’utilisation du routage de point de terminaison

L’autorisation lors de l’utilisation du routage de point de terminaison reçoit désormais le au `HttpContext` lieu de l’instance de point de terminaison. Cela permet à l’intergiciel (middleware) d’autorisation d’accéder au `RouteData` et à d’autres propriétés de `HttpContext` qui n’étaient pas accessibles via la <xref:Microsoft.AspNetCore.Http.Endpoint> classe. Le point de terminaison peut être extrait du contexte à l’aide du [contexte. GetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint%2A).

### <a name="role-based-access-control-with-kerberos-authentication-and-ldap-on-linux"></a>Contrôle d’accès en fonction du rôle avec l’authentification Kerberos et LDAP sur Linux

Voir [authentification Kerberos et contrôle d’accès en fonction du rôle (RBAC)](xref:security/authentication/windowsauth#rbac)

## <a name="api-improvements"></a>Améliorations des API

### <a name="json-extension-methods-for-httprequest-and-httpresponse"></a>Méthodes d’extension JSON pour HttpRequest et HttpResponse

Les données JSON peuvent être lues et écrites dans à partir d’un `HttpRequest` et `HttpResponse` à l’aide des <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> méthodes d’extension et nouvelles `WriteAsJsonAsync` . Ces méthodes d’extension utilisent l' [System.Text.Jssur](xref:System.Text.Json) le sérialiseur pour gérer les données JSON. La nouvelle `HasJsonContentType` méthode d’extension peut également vérifier si une demande a un type de contenu JSON.

Les méthodes d’extension JSON peuvent être combinées avec le [routage de point de terminaison](xref:fundamentals/routing) pour créer des API JSON dans un style de programmation que nous appelons * **router vers le code** _. C’est une nouvelle option pour les développeurs qui souhaitent créer des API JSON de base de façon légère. Par exemple, une application Web qui n’a que quelques points de terminaison peut choisir d’utiliser l’itinéraire vers du code plutôt que les fonctionnalités complètes de ASP.NET Core MVC :

```csharp
endpoints.MapGet("/weather/{city:alpha}", async context =>
{
    var city = (string)context.Request.RouteValues["city"];
    var weather = GetFromDatabase(city);

    await context.Response.WriteAsJsonAsync(weather);
});
```

Pour plus d’informations sur les nouvelles méthodes d’extension JSON et sur l’acheminement vers du code, consultez [ce .net Show](https://channel9.msdn.com/Shows/On-NET/ASPNET-Core-Series-Route-to-Code).

### <a name="systemdiagnosticsactivity"></a>System. Diagnostics. Activity

Le format par défaut pour <xref:System.Diagnostics.Activity?displayProperty=fullName> Now est désormais le format W3C. Par défaut, la prise en charge du traçage distribué dans ASP.NET Core interopérable avec davantage d’infrastructures.

### <a name="frombodyattribute"></a>FromBodyAttribute

<xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute> prend en charge la configuration d’une option qui permet à ces paramètres ou propriétés d’être considérés comme facultatifs :

```csharp
public IActionResult Post([FromBody(EmptyBodyBehavior = EmptyBodyBehavior.Allow)]
                           MyModel model) {
     ...
     }
```

## <a name="miscellaneous-improvements"></a>Améliorations diverses

Nous avons commencé à appliquer des [Annotations Nullable](/dotnet/csharp/nullable-references#attributes-describe-apis) aux assemblys ASP.net core. Nous prévoyons d’annoter la majeure partie de la surface d’API publique commune de l’infrastructure .NET 5. <!-- Review: what's the impact of this? How does it work? Need more info.  Check the link I added -->

### <a name="control-startup-class-activation"></a>Contrôler l’activation de la classe de démarrage

Une <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseStartup%2A> surcharge supplémentaire a été ajoutée, ce qui permet à une application de fournir une méthode de fabrique pour contrôler `Startup` l’activation de la classe. Le contrôle de `Startup` l’activation de la classe est utile pour passer des paramètres supplémentaires à `Startup` qui sont initialisés avec l’hôte :

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var logger = CreateLogger();
        var host = Host.CreateDefaultBuilder()
            .ConfigureWebHost(builder =>
            {
                builder.UseStartup(context => new Startup(logger));
            })
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="auto-refresh-with-dotnet-watch"></a>Actualisation automatique avec dotnet Watch

Dans .NET 5, l’exécution de [dotnet Watch](xref:tutorials/dotnet-watch) sur un projet de ASP.net Core lance le navigateur par défaut et actualise automatiquement le navigateur à mesure que des modifications sont apportées au code. Cela signifie que vous pouvez :

_ Ouvrir un projet de ASP.NET Core dans un éditeur de texte.
* Exécuter `dotnet watch`.
* Concentrez-vous sur les modifications de code, tandis que les outils gèrent la reconstruction, le redémarrage et le rechargement de l’application.

Nous espérons que la fonctionnalité d’actualisation automatique de Visual Studio est à l’avenir.

### <a name="console-logger-formatter"></a>Formateur d’enregistreur de console

Des améliorations ont été apportées au module fournisseur d’informations de la console dans la `Microsoft.Extensions.Logging` bibliothèque. Les développeurs peuvent désormais implémenter un personnalisé `ConsoleFormatter` pour exercer un contrôle total sur la mise en forme et la coloration de la sortie de la console. Les API de formateur permettent une mise en forme enrichie en implémentant un sous-ensemble des séquences d’échappement VT-100. VT-100 est pris en charge par la plupart des terminaux modernes. Le journal de la console peut analyser les séquences d’échappement sur des terminaux non pris en charge, ce qui permet aux développeurs de créer un seul formateur pour tous les terminaux.

### <a name="json-console-logger"></a>Journal de la console JSON

En plus de la prise en charge des formateurs personnalisés, nous avons également ajouté un formateur JSON intégré qui émet des journaux JSON structurés dans la console. Le code suivant montre comment passer du journal par défaut au format JSON :

[!code-csharp[](~/release-notes/sample/ProgramJsonLog.cs?name=snippet)]

Les messages de journal émis dans la console sont au format JSON :

```json
{
  "EventId": 0,
  "LogLevel": "Information",
  "Category": "Microsoft.Hosting.Lifetime",
  "Message": "Now listening on: https://localhost:5001",
  "State": {
    "Message": "Now listening on: https://localhost:5001",
    "address": "https://localhost:5001",
    "{OriginalFormat}": "Now listening on: {address}"
  }
}
```
