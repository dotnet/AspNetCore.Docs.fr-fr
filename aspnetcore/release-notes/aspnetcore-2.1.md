---
title: Nouveautés d’ASP.NET Core 2.1
author: isaac2004
description: Découvrez les nouvelles fonctionnalités d’ASP.NET Core 2.1.
ms.author: riande
ms.custom: mvc
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
uid: aspnetcore-2.1
ms.openlocfilehash: 62fc9d866adcf05ff024501db68cce8bb8b11a98
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059713"
---
# <a name="whats-new-in-aspnet-core-21"></a>Nouveautés d’ASP.NET Core 2.1

Cet article met en évidence les modifications les plus importantes dans ASP.NET 2.1 Core et fournit des liens vers la documentation appropriée.

## SignalR

SignalR a été réécrite pour ASP.NET Core 2,1. ASP.NET Core SignalR comprend un certain nombre d’améliorations :

* Un modèle simplifié de montée en puissance parallèle.
* Un nouveau client JavaScript sans dépendance de jQuery.
* Un nouveau protocole binaire compact basé sur MessagePack.
* Prise en charge des protocoles personnalisés.
* Un nouveau modèle réponse de streaming.
* Prise en charge des clients basés sur des WebSocket nus.

Pour plus d’informations, [consultez SignalR ASP.net Core ](xref:signalr/introduction).

## <a name="no-locrazor-class-libraries"></a>Razor bibliothèques de classes

ASP.NET Core 2,1 facilite la génération et Razor l’inclusion de l’interface utilisateur basée sur dans une bibliothèque et la partage entre plusieurs projets. Le nouveau Razor Kit de développement logiciel (SDK) permet de générer des Razor fichiers dans un projet de bibliothèque de classes qui peut être empaqueté dans un package NuGet. Les vues et les pages dans les bibliothèques sont automatiquement découvertes et peuvent être remplacées par l’application. En intégrant Razor la compilation dans la build :

* Le temps de démarrage de l’application est nettement plus rapide.
* Les mises à jour rapides Razor des affichages et des pages au moment de l’exécution sont toujours disponibles dans le cadre d’un flux de travail de développement itératif.

Pour plus d’informations, consultez [créer une interface utilisateur réutilisable à l’aide du Razor projet de bibliothèque de classes](xref:razor-pages/ui-class).

## <a name="no-locidentity-ui-library--scaffolding"></a>Identity Génération de modèles automatique & bibliothèque d’interface utilisateur

ASP.NET Core 2,1 fournit [ASP.NET Core Identity](xref:security/authentication/identity) comme [ Razor bibliothèque de classes](xref:razor-pages/ui-class). Les applications qui incluent Identity peuvent appliquer le nouveau générateur Identity de modèles automatique pour ajouter de manière sélective le code source contenu dans la Identity Razor bibliothèque de classes (RCL). Vous pouvez souhaiter générer le code source afin de pouvoir modifier le code et changer le comportement. Par exemple, vous pouvez demander au générateur de modèles automatique de générer le code utilisé dans l’inscription. Le code généré est prioritaire sur le même code dans le Identity RCL.

Les applications qui n’incluent **pas** l’authentification peuvent appliquer l' Identity échafaudage pour ajouter le Identity package RCL. Vous avez la possibilité de sélectionner du Identity code à générer.

Pour plus d’informations, consultez [structure Identity dans les projets ASP.net Core](xref:security/authentication/scaffold-identity).

## <a name="https"></a>HTTPS

L’importance croissante accordée à la sécurité et à la confidentialité justifie l’activation du protocole HTTPS pour les applications web. La mise en œuvre du protocole HTTPS devient de plus en plus stricte sur le web. Les sites qui n’utilisent pas le protocole HTTPs sont considérés comme non sécurisés. Les navigateurs (Chrome, Mozilla) commencent à imposer l’utilisation des fonctionnalités web dans un contexte sécurisé. Le [RGPD](xref:security/gdpr) exige l’utilisation du protocole HTTPS pour protéger la confidentialité des utilisateurs. L’utilisation du protocole HTTPS en production est critique et son utilisation en développement peut aider à éviter les problèmes liés au déploiement (tels que les liens non sécurisés). ASP.NET Core 2.1 inclut un certain nombre d’améliorations qui facilitent l’utilisation du protocole HTTPS pendant le développement et sa configuration en production. Pour plus d’informations, consultez [Appliquer le protocole HTTPS](xref:security/enforcing-ssl).

### <a name="on-by-default"></a>Activé par défaut

Pour faciliter le développement de sites web sécurisés, le protocole HTTPS est maintenant activé par défaut. À compter de la version 2.1, Kestrel écoute sur `https://localhost:5001` quand un certificat de développement local est présent. Un certificat de développement est créé :

* Dans le cadre de la première exécution du kit SDK .NET Core, quand vous utilisez celui-ci pour la première fois.
* Manuellement à l’aide du nouvel outil `dev-certs`.

Exécutez `dotnet dev-certs https --trust` pour approuver le certificat.

### <a name="https-redirection-and-enforcement"></a>Redirection et application du protocole HTTPS

En règle générale, les applications web doivent écouter sur les protocoles HTTP et HTTPS, mais ensuite rediriger tout le trafic HTTP vers HTTPS. Dans la version 2.1 a été introduit le middleware (intergiciel) de redirection HTTPS spécialisé, qui redirige intelligemment en fonction de la présence de ports de serveur lié ou de configuration.

Vous pouvez renforcer l’utilisation du protocole HTTPS en utilisant le protocole [HSTS (HTTP Strict Transport Security Protocol)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts). Le protocole HSTS indique aux navigateurs de toujours accéder au site via HTTPS. ASP.NET Core 2.1 ajoute un middleware HSTS qui prend en charge des options pour l’âge maximal, les sous-domaines et la liste de préchargement HSTS.

### <a name="configuration-for-production"></a>Configuration pour la production

En production, HTTPS doit être explicitement configuré. Dans la version 2.1, le schéma de configuration par défaut pour la configuration HTTPS pour Kestrel a été ajouté. Les applications peuvent être configurées pour utiliser :

* Plusieurs points de terminaison, y compris les URL. Pour plus d’informations, consultez [Implémentation du serveur web Kestrel : configuration de point de terminaison](xref:fundamentals/servers/kestrel#endpoint-configuration).
* Le certificat à utiliser pour le protocole HTTPS à partir d’un fichier sur disque ou d’un magasin de certificats.

## <a name="gdpr"></a>RGPD

ASP.NET Core fournit des API et des modèles qui aident à satisfaire à certaines des exigences du [Règlement général sur la protection des données (RGPD)](https://www.eugdpr.org/). Pour plus d’informations, consultez [Prise en charge du RGPD dans ASP.NET Core](xref:security/gdpr). Un [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) montre comment utiliser et tester la plupart des API et points d’extension RGPD ajoutés aux modèles ASP.NET Core 2.1.

## <a name="integration-tests"></a>Tests d’intégration

Un nouveau package est introduit qui simplifie la création et l’exécution de tests. Le package [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) gère les tâches suivantes :

* Copie le fichier de dépendance ( *\* . DEPS* ) de l’application testée dans le dossier *bin* du projet de test.
* Il définit la racine du contenu sur la racine du projet de l’application testée afin que soient trouvés les pages/vues et fichiers statiques quand les tests sont exécutés.
* Il fournit la classe [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) afin de simplifier l’amorçage de l’application testée avec [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).

Le test suivant utilise [xUnit](https://xunit.github.io/) pour vérifier que la page Index se charge avec un code d’état de réussite et avec l’en-tête Content-Type correct :

```csharp
public class BasicTests
    : IClassFixture<WebApplicationFactory<RazorPagesProject.Startup>>
{
    private readonly HttpClient _client;

    public BasicTests(WebApplicationFactory<RazorPagesProject.Startup> factory)
    {
        _client = factory.CreateClient();
    }

    [Fact]
    public async Task GetHomePage()
    {
        // Act
        var response = await _client.GetAsync("/");

        // Assert
        response.EnsureSuccessStatusCode(); // Status Code 200-299
        Assert.Equal("text/html; charset=utf-8",
            response.Content.Headers.ContentType.ToString());
    }
}
```

Pour plus d’informations, consultez la rubrique [Tests d’intégration](xref:test/integration-tests).

## <a name="apicontroller-actionresultt"></a>[ApiController], ActionResult\<T>

ASP.NET Core 2.1 ajoute de nouvelles conventions de programmation qui facilitent la génération d’API web propres et descriptives. `ActionResult<T>` est un nouveau type qui permet à une application de retourner soit un type de réponse, soit tout autre résultat d’action (à l’image d’IActionResult) tout en indiquant toujours le type de réponse. L’attribut `[ApiController]` a également été ajouté comme moyen d’accepter des comportements et des conventions propres aux API web.

Pour plus d’informations, consultez [Créer des API web avec ASP.NET Core](xref:web-api/index).

## <a name="ihttpclientfactory"></a>IHttpClientFactory

ASP.NET Core 2.1 inclut un nouveau service `IHttpClientFactory` qui facilite la configuration et l’utilisation d’instances de `HttpClient` dans les applications. `HttpClient` intègre déjà le concept de délégation des gestionnaires qui pourraient être liés ensemble pour les requêtes HTTP sortantes. La fabrique :

* Rend plus intuitive l’inscription des instances de `HttpClient` par client nommé.
* Implémente un gestionnaire Polly qui permet d’utiliser des stratégies Polly pour des fonctionnalités telles que Retry (nouvelle tentative) ou CircuitBreaker (disjoncteur).

Pour plus d’informations, consultez [Lancer des requêtes HTTP](xref:fundamentals/http-requests).

## <a name="kestrel-transport-configuration"></a>Configuration du transport Kestrel

Dans ASP.NET Core 2.1, le transport par défaut de Kestrel n’est plus basé sur Libuv, mais sur des sockets managés. Pour plus d’informations, consultez [Implémentation du serveur web Kestrel : configuration du transport](xref:fundamentals/servers/kestrel#transport-configuration).

## <a name="generic-host-builder"></a>Générateur d’hôte générique

Le générateur d’hôte générique (`HostBuilder`) a été introduit. Ce générateur peut être utilisé pour les applications qui ne traitent pas les requêtes HTTP (messagerie, les tâches en arrière-plan, etc.).

Pour plus d’informations, consultez [Hôte générique .NET](xref:fundamentals/host/generic-host).

## <a name="updated-spa-templates"></a>Modèles SPA mis à jour

Les modèles d’applications monopages pour Angular, React et React avec Redux sont mis à jour pour utiliser les systèmes de génération et les structures de projet standard pour chaque framework.

Le modèle Angular est basé sur l’interface CLI Angular, tandis que les modèles React sont basés sur create-react-app.

Pour plus d'informations, consultez les pages suivantes :

* <xref:spa/angular>
* <xref:spa/react>
* <xref:spa/react-with-redux>

## <a name="no-locrazor-pages-search-for-no-locrazor-assets"></a>Razor Recherche de pages pour les Razor ressources

Dans 2,1, les Razor pages recherchent les Razor ressources (telles que les mises en page et les éléments partiels) dans les répertoires suivants dans l’ordre indiqué :

1. Dossier Pages en cours.
1. */Pages/Shared/*
1. */Views/Shared/*

## <a name="no-locrazor-pages-in-an-area"></a>Razor Pages dans une zone

Razor Les pages prennent désormais en charge les [zones](xref:mvc/controllers/areas). Pour voir un exemple de zones, créez une Razor application Web de pages avec des comptes d’utilisateur individuels. Une Razor application Web de pages avec des comptes d’utilisateur individuels comprend */Areas/ Identity /pages* .

## <a name="mvc-compatibility-version"></a>Version de compatibilité MVC

La méthode <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> permet à une application d’accepter ou de refuser les changements de comportement potentiellement cassants introduits dans ASP.NET Core MVC 2.1 ou version ultérieure.

Pour plus d'informations, consultez <xref:mvc/compatibility-version>.

## <a name="migrate-from-20-to-21"></a>Migrer depuis la version 2.0 vers la version 2.1

Consultez [Migrer depuis ASP.NET Core 2.0 vers 2.1](xref:migration/20_21).

## <a name="additional-information"></a>Informations supplémentaires

Pour obtenir la liste complète des modifications, consultez les [Notes de publication d’ASP.NET Core 2.1 ](https://github.com/dotnet/aspnetcore/releases/tag/2.1.0).
