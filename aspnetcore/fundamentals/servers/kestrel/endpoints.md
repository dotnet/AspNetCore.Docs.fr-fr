---
title: Configurer des points de terminaison pour le serveur Web ASP.NET Core Kestrel
author: rick-anderson
description: En savoir plus sur la configuration des points de terminaison avec Kestrel, le serveur Web multiplateforme pour ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel/endpoints
ms.openlocfilehash: 780250feab456fa3eedee4e023c9bc774e748291
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253958"
---
# <a name="configure-endpoints-for-the-aspnet-core-kestrel-web-server"></a>Configurer des points de terminaison pour le serveur Web ASP.NET Core Kestrel

Par défaut, ASP.NET Core se lie à :

* `http://localhost:5000`
* `https://localhost:5001` (quand un certificat de développement local est présent)

Spécifiez les URL avec :

* La variable d’environnement `ASPNETCORE_URLS`.
* L’argument de ligne de commande `--urls`.
* La clé de configuration d’hôte `urls`.
* La méthode d’extension `UseUrls`.

La valeur fournie avec ces approches peut être un ou plusieurs points de terminaison HTTP et HTTPS (HTTPS si un certificat par défaut est disponible). Configurez la valeur sous forme de liste délimitée par des points-virgules (par exemple `"Urls": "http://localhost:8000;http://localhost:8001"`).

Pour plus d’informations sur ces approches, voir [URL de serveur](xref:fundamentals/host/web-host#server-urls) et [Remplacer la configuration](xref:fundamentals/host/web-host#override-configuration).

Un certificat de développement est créé :

* Lorsque le [Kit de développement logiciel (SDK) .net](/dotnet/core/sdk) est installé.
* [L’outil dev-certs](xref:aspnetcore-2.1#https) est utilisé pour créer un certificat.

Certains navigateurs requièrent l’octroi d’une autorisation explicite pour approuver le certificat de développement local.

Les modèles de projet configurent les applications à exécuter sur HTTPs par défaut et incluent [la redirection https et la prise en charge de HSTS](xref:security/enforcing-ssl).

Appelez les méthodes <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> ou <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> sur <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> pour configurer les préfixes et les ports d’URL pour Kestrel.

`UseUrls`, l’argument de ligne de commande `--urls`, la clé de configuration d’hôte `urls` et la variable d’environnement `ASPNETCORE_URLS` fonctionnent également, mais ils présentent les limitations indiquées plus loin dans cette section (un certificat par défaut doit être disponible pour la configuration du point de terminaison HTTPS).

`KestrelServerOptions` configuré

## <a name="configureendpointdefaultsactionlistenoptions"></a>ConfigureEndpointDefaults (action \<ListenOptions> )

Spécifie une `Action` de configuration à exécuter pour chaque point de terminaison spécifié. Le fait d’appeler `ConfigureEndpointDefaults` plusieurs fois remplace les `Action`s précédentes par la dernière `Action` spécifiée.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A>  <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults%2A> Les valeurs par défaut sont appliquées aux points de terminaison créés en appelant avant d’appeler.

## <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a>ConfigureHttpsDefaults (action \<HttpsConnectionAdapterOptions> )

Spécifie une `Action` de configuration à exécuter pour chaque point de terminaison HTTPS. Le fait d’appeler `ConfigureHttpsDefaults` plusieurs fois remplace les `Action`s précédentes par la dernière `Action` spécifiée.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        // certificate is an X509Certificate2
        listenOptions.ServerCertificate = certificate;
    });
});
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A>  <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults%2A> Les valeurs par défaut sont appliquées aux points de terminaison créés en appelant avant d’appeler.

## <a name="configureiconfiguration"></a>Configure(IConfiguration)

Crée un chargeur de configuration pour configurer Kestrel, qui prend en entrée une <xref:Microsoft.Extensions.Configuration.IConfiguration>. La configuration doit être limitée à la section de configuration pour Kestrel.

Par défaut, `CreateDefaultBuilder` appelle `Configure(context.Configuration.GetSection("Kestrel"))` pour charger la configuration de Kestrel.

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "Https": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    }
  }
}
```

## <a name="listenoptionsusehttps"></a>ListenOptions.UseHttps

Configure Kestrel pour l’utilisation de HTTPS.

Extensions de `ListenOptions.UseHttps` :

* `UseHttps`: Configurez Kestrel pour utiliser le protocole HTTPs avec le certificat par défaut. Lève une exception si aucun certificat par défaut n’est configuré.
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

Paramètres de `ListenOptions.UseHttps` :

* `filename` est le chemin et le nom d’un fichier de certificat, relatif au répertoire qui contient les fichiers de contenu de l’application.
* `password` est le mot de passe nécessaire pour accéder aux données du certificat X.509.
* `configureOptions` est une `Action` pour configurer les `HttpsConnectionAdapterOptions`. Retourne l'`ListenOptions`.
* `storeName` est le magasin de certificats à partir duquel charger le certificat.
* `subject` est le nom du sujet du certificat.
* `allowInvalid` indique si les certificats non valides doivent être considérés, comme les certificats auto-signés.
* `location` est l’emplacement du magasin à partir duquel charger le certificat.
* `serverCertificate` est le certificat X.509.

En production, HTTPS doit être explicitement configuré. Au minimum, un certificat par défaut doit être fourni.

Configurations prises en charge décrites dans la suite :

* Pas de configuration
* Remplacer le certificat par défaut dans la configuration
* Changer les valeurs par défaut dans le code

### <a name="no-configuration"></a>Pas de configuration

Kestrel écoute sur `http://localhost:5000` et sur `https://localhost:5001` (si un certificat par défaut est disponible).

<a name="configuration"></a>

### <a name="replace-the-default-certificate-from-configuration"></a>Remplacer le certificat par défaut dans la configuration

Un schéma de configuration des paramètres d’application HTTPS par défaut est disponible pour Kestrel. Configurez plusieurs points de terminaison, notamment les URL et les certificats à utiliser, à partir d’un fichier sur disque ou d’un magasin de certificats.

Dans l' *appsettings.json* exemple suivant :

* Affectez `AllowInvalid` `true` la valeur pour autoriser l’utilisation de certificats non valides (par exemple, des certificats auto-signés).
* Tout point de terminaison HTTPS qui ne spécifie pas de certificat ( `HttpsDefaultCert` dans l’exemple ci-dessous) revient au certificat défini sous `Certificates`  >  `Default` ou au certificat de développement.

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },
      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },
      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },
      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

Une alternative à l’utilisation de `Path` et `Password` pour n’importe quel nœud de certificat consiste à spécifier le certificat à l’aide des champs du magasin de certificats. Par exemple, le `Certificates`  >  `Default` certificat peut être spécifié comme suit :

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

Notes de schéma :

* Les noms des points de terminaison ne respectent pas la casse. Par exemple, `HTTPS` et `Https` sont valides.
* Le paramètre `Url` est obligatoire pour chaque point de terminaison. Le format de ce paramètre est le même que celui du paramètre de configuration `Urls` du plus haut niveau, sauf qu’il est limité à une seule valeur.
* Ces points de terminaison remplacent ceux qui sont définis dans le paramètre de configuration `Urls` du plus haut niveau configuration, au lieu de s’y ajouter. Les points de terminaison définis dans le code via `Listen` sont cumulatifs avec les points de terminaison définis dans la section de configuration.
* La section `Certificate` est facultative. Si la section `Certificate` n’est pas spécifiée, les valeurs par défaut définies dans les scénarios précédents sont utilisées. Si aucune valeur par défaut n’est disponible, le serveur lève une exception et son démarrage échoue.
* La `Certificate` section prend en charge les `Path` &ndash; `Password` `Subject` &ndash; `Store` certificats et.
* Vous pouvez définir un nombre quelconque de points de terminaison de cette façon, pour autant qu’ils ne provoquent pas de conflits de port.
* `options.Configure(context.Configuration.GetSection("{SECTION}"))` retourne un `KestrelConfigurationLoader` avec une méthode `.Endpoint(string name, listenOptions => { })` qui peut être utilisée pour compléter les paramètres d’un point de terminaison configuré :

```csharp
webBuilder.UseKestrel((context, serverOptions) =>
{
    serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
        .Endpoint("HTTPS", listenOptions =>
        {
            listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
        });
});
```

`KestrelServerOptions.ConfigurationLoader` peut être directement accessible pour poursuivre l’itération sur le chargeur existant, tel que celui fourni par <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> .

* La section de configuration pour chaque point de terminaison est disponible sur les options de la `Endpoint` méthode afin que les paramètres personnalisés puissent être lus.
* Plusieurs configurations peuvent être chargées en rappelant `options.Configure(context.Configuration.GetSection("{SECTION}"))` avec une autre section. Seule la dernière configuration est utilisée, à moins que `Load` soit explicitement appelé sur les instances précédentes. Le métapackage n’appelle pas `Load` : sa section de configuration par défaut peut donc être remplacée.
* `KestrelConfigurationLoader` reflète la famille d’API `Listen` de `KestrelServerOptions` sous forme de surcharges de `Endpoint` : le code et les points de terminaison de configuration peuvent donc être configurés au même emplacement. Ces surcharges n’utilisent pas de noms et consomment seulement les paramètres par défaut de la configuration.

### <a name="change-the-defaults-in-code"></a>Changer les valeurs par défaut dans le code

`ConfigureEndpointDefaults` et `ConfigureHttpsDefaults` peuvent être utilisés pour modifier les paramètres par défaut pour `ListenOptions` et `HttpsConnectionAdapterOptions`, notamment en remplaçant le certificat par défaut spécifié dans le scénario précédent. `ConfigureEndpointDefaults` et `ConfigureHttpsDefaults` doivent être appelés avant que des points de terminaison soient configurés.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });

    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls12;
    });
});
```

## <a name="configure-endpoints-using-server-name-indication"></a>Configurer des points de terminaison à l’aide de Indication du nom du serveur

[L’indication du nom de serveur (SNI, Server Name Indication)](https://tools.ietf.org/html/rfc6066#section-3) peut être utilisée pour héberger plusieurs domaines sur la même adresse IP et le même port. Pour que SNI fonctionne, le client envoie le nom d’hôte pour la session sécurisée au serveur pendant la négociation TLS afin que le serveur puisse fournir le certificat correct. Le client utilise le certificat fourni pour la communication chiffrée avec le serveur pendant la session sécurisée qui suit la négociation TLS.

Kestrel prend en charge SNI via le rappel de `ServerCertificateSelector`. Le rappel est appelé une fois par connexion pour permettre à l’application d’inspecter le nom d’hôte et de sélectionner le certificat approprié. Le code de rappel suivant peut être utilisé dans l' `ConfigureWebHostDefaults` appel de méthode du fichier *Program.cs* d’un projet :

```csharp
//using System.Security.Cryptography.X509Certificates;
//using Microsoft.AspNetCore.Server.Kestrel.Https;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ListenAnyIP(5005, listenOptions =>
    {
        listenOptions.UseHttps(httpsOptions =>
        {
            var localhostCert = CertificateLoader.LoadFromStoreCert(
                "localhost", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var exampleCert = CertificateLoader.LoadFromStoreCert(
                "example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var subExampleCert = CertificateLoader.LoadFromStoreCert(
                "sub.example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var certs = new Dictionary<string, X509Certificate2>(StringComparer.OrdinalIgnoreCase)
            {
                { "localhost", localhostCert },
                { "example.com", exampleCert },
                { "sub.example.com", subExampleCert },
            };            

            httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
            {
                if (name != null && certs.TryGetValue(name, out var cert))
                {
                    return cert;
                }

                return exampleCert;
            };
        });
    });
});
```

La prise en charge de SNI nécessite les points suivants :

* Exécution sur la version cible de .NET Framework `netcoreapp2.1` ou version ultérieure. Sur `net461` ou version ultérieure, le rappel est appelé, mais `name` est toujours `null` . `name` est également `null` si le client ne fournit pas le paramètre du nom d’hôte dans la négociation TLS.
* Tous les sites web s’exécutent sur la même instance Kestrel. Kestrel ne prend pas en charge le partage d’une adresse IP et d’un port entre plusieurs instances sans un proxy inverse.

## <a name="connection-logging"></a>Journalisation des connexions

Appelez <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging%2A> pour émettre des journaux au niveau du débogage pour la communication au niveau de l’octet sur une connexion. La journalisation des connexions est utile pour résoudre les problèmes de communication de bas niveau, par exemple lors du chiffrement TLS et derrière les proxies. Si `UseConnectionLogging` est placé avant `UseHttps` , le trafic chiffré est journalisé. Si `UseConnectionLogging` est placé après `UseHttps` , le trafic déchiffré est journalisé. Il s’agit d’un [intergiciel de connexion](#connection-middleware)intégré.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

## <a name="bind-to-a-tcp-socket"></a>Lier à un socket TCP

La méthode <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> se lie à un socket TCP, et une expression lambda options permet la configuration d’un certificat X.509 :

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

L’exemple configure HTTPS pour un point de terminaison avec <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>. Utilisez la même API afin de configurer d’autres paramètres Kestrel pour des points de terminaison spécifiques.

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

## <a name="bind-to-a-unix-socket"></a>Lier à un socket Unix

Écoutez sur un socket Unix avec <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> pour améliorer les performances avec Nginx, comme illustré dans cet exemple :

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* Dans le fichier de configuration nginx, définissez l' `server`  >  `location`  >  `proxy_pass` entrée sur `http://unix:/tmp/{KESTREL SOCKET}:/;` . `{KESTREL SOCKET}` nom du Socket fourni à <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> (par exemple, `kestrel-test.sock` dans l’exemple précédent).
* Assurez-vous que le socket est accessible en écriture par Nginx (par exemple, `chmod go+w /tmp/kestrel-test.sock` ).

## <a name="port-0"></a>Port 0

Si vous spécifiez le numéro de port `0`, Kestrel se lie dynamiquement à un port disponible. L’exemple suivant montre comment déterminer le port Kestrel lié au moment de l’exécution :

[!code-csharp[](samples/5.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

Quand l’application est exécutée, la sortie de la fenêtre de console indique le port dynamique où l’application peut être atteinte :

```console
Listening on the following addresses: http://127.0.0.1:48508
```

## <a name="limitations"></a>Limitations

Configurez des points de terminaison avec les approches suivantes :

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls%2A>
* Arguments de ligne de commande `--urls`
* Clé de configuration d’hôte `urls`.
* `ASPNETCORE_URLS` variable d’environnement

Ces méthodes sont utiles si vous voulez que votre code fonctionne avec des serveurs autres que Kestrel. Toutefois, soyez conscient des limitations suivantes :

* Le protocole HTTPs ne peut pas être utilisé avec ces approches, sauf si un certificat par défaut est fourni dans la configuration du point de terminaison HTTPs (par exemple, à l’aide de la `KestrelServerOptions` configuration ou d’un fichier de configuration, comme indiqué plus haut dans cet article).
* Quand les deux approches `Listen` et `UseUrls` sont utilisées simultanément, les points de terminaison `Listen` remplacent les points de terminaison `UseUrls`.

## <a name="iis-endpoint-configuration"></a>Configuration de point de terminaison IIS

Quand vous utilisez IIS, les liaisons d’URL pour IIS remplacent les liaisons qui sont définies par `Listen` ou par `UseUrls`. Pour plus d’informations, consultez [ASP.net Core Module](xref:host-and-deploy/aspnet-core-module).

## <a name="listenoptionsprotocols"></a>ListenOptions.Protocols

La propriété `Protocols` établit les protocoles HTTP (`HttpProtocols`) activés sur un point de terminaison de connexion ou pour le serveur. Affectez une valeur à la propriété `Protocols` à partir de l’énumération `HttpProtocols`.

| Valeur enum `HttpProtocols` | Protocole de connexion autorisé |
| -------------------------- | ----------------------------- |
| `Http1`                    | HTTP/1.1 uniquement. Peut être utilisé avec ou sans TLS. |
| `Http2`                    | HTTP/2 uniquement. Peut être utilisé sans TLS, uniquement si le client prend en charge un [mode de connaissance préalable (Prior Knowledge)](https://tools.ietf.org/html/rfc7540#section-3.4). |
| `Http1AndHttp2`            | HTTP/1.1 et HTTP/2. HTTP/2 nécessite que le client sélectionne HTTP/2 dans le protocole de transfert de [négociation de protocole de couche d’application (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) TLS. dans le cas contraire, la connexion par défaut est HTTP/1.1. |

La valeur par défaut `ListenOptions.Protocols` d’un point de terminaison est `HttpProtocols.Http1AndHttp2` .

Restrictions TLS pour HTTP/2 :

* TLS version 1.2 ou ultérieure
* Renégociation désactivée
* Compression désactivée
* Tailles minimales de l’échange de clé éphémère :
  * Elliptic Curve Diffie-Hellman (ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; : 224 bits minimum
  * Diffie-Hellman de champ fini (dhe) &lbrack; `TLS12` &rbrack; : 2048 bits minimum
* Suite de chiffrement non interdite. 

`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack; avec la courbe elliptique P-256 &lbrack; `FIPS186` &rbrack; est pris en charge par défaut.

L’exemple suivant autorise les connexions HTTP/1.1 et HTTP/2 sur le port 8000. Les connexions sont sécurisées par TLS avec un certificat fourni :

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

Sur Linux, <xref:System.Net.Security.CipherSuitesPolicy> peut être utilisé pour filtrer les négociations TLS en fonction de la connexion :

```csharp
// using System.Net.Security;
// using Microsoft.AspNetCore.Hosting;
// using Microsoft.AspNetCore.Server.Kestrel.Core;
// using Microsoft.Extensions.DependencyInjection;
// using Microsoft.Extensions.Hosting;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.OnAuthenticate = (context, sslOptions) =>
        {
            sslOptions.CipherSuitesPolicy = new CipherSuitesPolicy(
                new[]
                {
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,
                    // ...
                });
        };
    });
});
```

## <a name="connection-middleware"></a>Intergiciel de connexion

Un intergiciel (middleware) de connexion personnalisé peut filtrer les négociations TLS en fonction de la connexion pour des chiffrements spécifiques, si nécessaire.

L’exemple suivant lève <xref:System.NotSupportedException> une exception pour tous les algorithmes de chiffrement que l’application ne prend pas en charge. Vous pouvez également définir et comparer [ITlsHandshakeFeature. CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) à une liste de suites de chiffrement acceptables.

Aucun chiffrement n’est utilisé avec un algorithme de chiffrement [CipherAlgorithmType. null](xref:System.Security.Authentication.CipherAlgorithmType) .

```csharp
// using System.Net;
// using Microsoft.AspNetCore.Connections;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.UseTlsFilter();
    });
});
```

```csharp
using System;
using System.Security.Authentication;
using Microsoft.AspNetCore.Connections.Features;

namespace Microsoft.AspNetCore.Connections
{
    public static class TlsFilterConnectionMiddlewareExtensions
    {
        public static IConnectionBuilder UseTlsFilter(
            this IConnectionBuilder builder)
        {
            return builder.Use((connection, next) =>
            {
                var tlsFeature = connection.Features.Get<ITlsHandshakeFeature>();

                if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
                {
                    throw new NotSupportedException("Prohibited cipher: " +
                        tlsFeature.CipherAlgorithm);
                }

                return next();
            });
        }
    }
}
```

Le filtrage des connexions peut également être configuré par le biais d’une expression <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda :

```csharp
// using System;
// using System.Net;
// using System.Security.Authentication;
// using Microsoft.AspNetCore.Connections;
// using Microsoft.AspNetCore.Connections.Features;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.Use((context, next) =>
        {
            var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

            if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
            {
                throw new NotSupportedException(
                    $"Prohibited cipher: {tlsFeature.CipherAlgorithm}");
            }

            return next();
        });
    });
});
```

## <a name="set-the-http-protocol-from-configuration"></a>Définir le protocole HTTP à partir de la configuration

Par défaut, `CreateDefaultBuilder` appelle `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` pour charger la configuration de Kestrel.

L' *appsettings.json* exemple suivant établit http/1.1 comme protocole de connexion par défaut pour tous les points de terminaison :

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

L' *appsettings.json* exemple suivant établit le protocole de connexion http/1.1 pour un point de terminaison spécifique :

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1"
      }
    }
  }
}
```

Les protocoles spécifiés dans le code remplacent les valeurs définies par configuration.

## <a name="url-prefixes"></a>Préfixes d’URL

Quand vous utilisez `UseUrls`, l’argument de ligne de commande `--urls`, la clé de configuration d’hôte `urls` ou la variable d’environnement `ASPNETCORE_URLS`, les préfixes d’URL peuvent être dans un des formats suivants.

Seuls les préfixes d’URL HTTP sont valides. Kestrel ne prend pas en charge HTTPS lors de la configuration de liaisons d’URL avec `UseUrls`.

* Adresse IPv4 avec numéro de port

  ```
  http://65.55.39.10:80/
  ```

  `0.0.0.0` est un cas spécial qui se lie à toutes les adresses IPv4.

* Adresse IPv6 avec numéro de port

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  `[::]` est l’équivalent IPv6 de `0.0.0.0` dans IPv4.

* Nom d’hôte avec numéro de port

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  Les noms d’hôte, `*` et `+` ne sont pas spéciaux. Tout ce qui n’est pas reconnu comme adresse IP valide ou `localhost` se lie à toutes les adresses IP IPv4 et IPv6. Pour lier différents noms d’hôte à différentes ASP.NET Core applications sur le même port, utilisez [HTTP.sys](xref:fundamentals/servers/httpsys) ou un serveur proxy inverse. Les exemples de serveur proxy inverse incluent IIS, Nginx ou Apache.

  > [!WARNING]
  > L’hébergement dans une configuration de proxy inverse nécessite le [filtrage d’hôte](xref:fundamentals/servers/kestrel/host-filtering).

* Nom `localhost` de l’hôte avec numéro de port ou adresse IP de bouclage avec numéro de port

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  Quand `localhost` est spécifié, Kestrel tente de se lier aux interfaces de bouclage IPv4 et IPv6. Si le port demandé est en cours d’utilisation par un autre service sur l’une des interfaces de bouclage, Kestrel ne démarre pas. Si l’une des interfaces de bouclage n’est pas disponible pour une raison quelconque (généralement du fait de la non-prise en charge d’IPv6), Kestrel journalise un avertissement.
