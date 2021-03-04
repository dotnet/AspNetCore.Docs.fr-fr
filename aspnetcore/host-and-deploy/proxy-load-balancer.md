---
title: Configurer ASP.NET Core pour l’utilisation de serveurs proxy et d’équilibreurs de charge
author: rick-anderson
description: Découvrez la configuration des applications hébergées derrière des serveurs proxy et des équilibreurs de charge, qui masquent souvent des informations de requête importantes.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: 28a802414fd59f684a56e2b735140438d33be740
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109947"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a>Configurer ASP.NET Core pour l’utilisation de serveurs proxy et d’équilibreurs de charge

Par [Chris Ross](https://github.com/Tratcher)

::: moniker range=">= aspnetcore-3.0"

Dans la configuration recommandée d’ASP.NET Core, l’application est hébergée à l’aide du module IIS/ASP.NET Core, de Nginx ou d’Apache. Les serveurs proxy, les équilibreurs de charge et autres dispositifs réseau masquent souvent les informations sur la requête avant qu’elle n’atteigne l’application :

* Quand les requêtes HTTPS sont transmises par proxy via HTTP, le schéma d’origine (HTTPS) est perdu et doit être transféré dans un en-tête.
* Étant donné qu’une requête adressée à une application transite par le proxy au lieu de provenir directement de sa source sur Internet ou le réseau d’entreprise, l’adresse IP cliente d’origine doit également être transférée dans un en-tête.

Ces informations peuvent être importantes pour traiter les requêtes, par exemple dans les redirections, l’authentification, la génération de lien, l’évaluation des stratégies et la géolocalisation des clients.

## <a name="forwarded-headers"></a>En-têtes transférés

Par convention, les proxys transfèrent les informations dans des en-têtes HTTP.

| En-tête | Description |
| ------ | ----------- |
| X-Forwarded-For | Contient des informations sur le client à l’origine de la requête et les proxys suivants dans une chaîne de proxys. Ce paramètre peut contenir des adresses IP (et, éventuellement, des numéros de port). Dans une chaîne de serveurs proxy, le premier paramètre indique le client sur lequel la requête a été effectuée pour la première fois. Viennent ensuite les identificateurs des proxy suivants. Le dernier proxy dans la chaîne ne figure pas dans la liste des paramètres. L’adresse IP du dernier proxy et éventuellement un numéro de port sont disponibles en tant qu’adresse IP distante au niveau de la couche transport. |
| X-Forwarded-Proto | Valeur du schéma d’origine (HTTP/HTTPS). La valeur peut également être une liste de schémas si la requête a franchi plusieurs proxys. |
| X-Forwarded-Host | Valeur d’origine du champ d’en-tête de l’hôte. En règle générale, les proxys ne modifient pas l’en-tête de l’hôte. Consultez le document [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) pour plus d’informations sur une vulnérabilité par élévation de privilèges qui affecte les systèmes où le proxy ne valide pas les en-têtes d’hôte ou ne les limite pas à des valeurs correctes connues. |

L’intergiciel d’en-têtes transféré ( <xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersMiddleware> ) lit ces en-têtes et remplit les champs associés sur <xref:Microsoft.AspNetCore.Http.HttpContext> .

Le middleware met à jour les éléments suivants :

* [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): défini à l’aide de la `X-Forwarded-For` valeur d’en-tête. Des paramètres supplémentaires déterminent la façon dont le middleware définit `RemoteIpAddress`. Pour plus d’informations, consultez les [options du middleware des en-têtes transférés](#forwarded-headers-middleware-options).
* [HttpContext. Request. Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): défini à l’aide de la `X-Forwarded-Proto` valeur d’en-tête.
* [HttpContext. Request. Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): défini à l’aide de la `X-Forwarded-Host` valeur d’en-tête.

Pour plus d’informations sur le précédent, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/21615).

Vous pouvez configurer les [paramètres par défaut](#forwarded-headers-middleware-options) du middleware des en-têtes transférés. Les paramètres par défaut sont :

* Il y a *un seul proxy* entre l’application et la source des requêtes.
* Seules des adresses de bouclage sont configurées pour les proxys connus et les réseaux connus.
* Les en-têtes transférés sont nommés `X-Forwarded-For` et `X-Forwarded-Proto`.

Certaines appliances réseau nécessitent une configuration supplémentaire pour ajouter les en-têtes `X-Forwarded-For` et `X-Forwarded-Proto`. Consultez les instructions du fabricant de votre appliance si des requêtes en proxy ne contiennent pas ces en-têtes quand elles atteignent l’application. Si l’appliance utilise des noms d’en-têtes autres que `X-Forwarded-For` et `X-Forwarded-Proto`, définissez les options <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> et <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> pour faire correspondre les noms d’en-têtes utilisés par l’appliance. Pour plus d’informations, consultez [Options du middleware des en-têtes transférés](#forwarded-headers-middleware-options) et [Configuration d’un proxy qui utilise des noms d’en-têtes différents](#configuration-for-a-proxy-that-uses-different-header-names).

## <a name="iisiis-express-and-aspnet-core-module"></a>Module ASP.NET Core et IIS/IIS Express

Le middleware des en-têtes transférés est activé par défaut par le [middleware d’intégration IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) lorsque l’application est hébergée [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) derrière IIS et le module ASP.NET Core. Le middleware des en-têtes transférés est activé pour s’exécuter en premier dans le pipeline des middlewares avec une configuration limitée propre au module ASP.NET Core en raison de problèmes d’approbation liés aux en-têtes transférés (par exemple, [usurpation d’adresse IP](https://www.iplocation.net/ip-spoofing)). Le middleware est configuré pour transférer les en-têtes `X-Forwarded-For` et `X-Forwarded-Proto` et est limité à un proxy localhost unique. Si une configuration supplémentaire est requise, consultez les [options du middleware des en-têtes transférés](#forwarded-headers-middleware-options).

## <a name="other-proxy-server-and-load-balancer-scenarios"></a>Autres scénarios avec un serveur proxy et un équilibreur de charge

En dehors de l’utilisation de [l’intégration IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) lors de l’hébergement [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), le middleware des en-têtes transférés n’est pas activé par défaut. L’intergiciel des en-têtes transférés doit être activé pour qu’une application puisse traiter les en-têtes transférés avec <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>. Une fois l’intergiciel activé, si aucune option <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> ne lui est spécifiée, les valeurs par défaut [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) ont pour valeur [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).

Configurez l’intergiciel avec <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> pour transférer les en-têtes `X-Forwarded-For` et `X-Forwarded-Proto` dans `Startup.ConfigureServices`.

<a name="fhmo"></a>

### <a name="forwarded-headers-middleware-order"></a>Ordre intergiciel des en-têtes transférés

L’intergiciel (middleware) d’en-têtes transférés doit s’exécuter avant tout autre intergiciel. Cet ordre permet au middleware qui repose sur les informations des en-têtes transférés d’utiliser les valeurs d’en-tête pour le traitement. L’intergiciel d’en-têtes transférés peut s’exécuter après les diagnostics et la gestion des erreurs, mais il doit être exécuté avant d’appeler `UseHsts` :

[!code-csharp[](~/host-and-deploy/proxy-load-balancer/3.1samples/Startup.cs?name=snippet&highlight=13-17,25,30)]

Vous pouvez également appeler `UseForwardedHeaders` avant les Diagnostics :

[!code-csharp[](~/host-and-deploy/proxy-load-balancer/3.1samples/Startup2.cs?name=snippet)]

> [!NOTE]
> Si aucun <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> n’est spécifié dans `Startup.ConfigureServices` ou directement sur la méthode d’extension avec <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, les en-têtes par défaut à transférer sont [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders). La propriété <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> doit être configurée avec les en-têtes à transférer.

## <a name="nginx-configuration"></a>Configuration de Nginx

Pour transférer les en-têtes `X-Forwarded-For` et `X-Forwarded-Proto`, consultez <xref:host-and-deploy/linux-nginx#configure-nginx>. Pour plus d’informations, consultez [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/) (NGINX : utilisation de l’en-tête Forwarded).

## <a name="apache-configuration"></a>Configuration d’Apache

`X-Forwarded-For` est ajouté automatiquement (consultez [Module Apache mod_proxy : en-têtes de requête du mandataire inverse](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)). Pour plus d’informations sur la façon de transférer l’en-tête `X-Forwarded-Proto`, consultez <xref:host-and-deploy/linux-apache#configure-apache>.

## <a name="forwarded-headers-middleware-options"></a>Options du middleware des en-têtes transférés

<xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> contrôlent le comportement de l’intergiciel des en-têtes transférés. L’exemple suivant change les valeurs par défaut :

* Limitez le nombre d’entrées dans les en-têtes transférés à `2`.
* Ajoutez l’adresse de proxy connue `127.0.10.1`.
* Changez le nom de l’en-tête transféré en remplaçant la valeur par défaut `X-Forwarded-For` par `X-Forwarded-For-My-Custom-Header-Name`.

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| Option | Description |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Limite les hôtes par l’en-tête `X-Forwarded-Host` aux valeurs fournies.<ul><li>Les valeurs sont comparées à l’aide de l’option ordinal-ignore-case.</li><li>Les numéros de port doivent être exclus.</li><li>Si la liste est vide, tous les hôtes sont autorisés.</li><li>Un caractère générique de niveau supérieur `*` autorise tous les hôtes non vides.</li><li>Les caractères génériques de sous-domaine sont autorisés, mais ne correspondent pas au domaine racine. Par exemple, `*.contoso.com` correspond au sous-domaine `foo.contoso.com`, mais pas au domaine racine `contoso.com`.</li><li>Les noms d’hôte Unicode sont autorisés, mais sont convertis en [Punycode](https://tools.ietf.org/html/rfc3492) à des fins de correspondance.</li><li>Les [adresses IPv6](https://tools.ietf.org/html/rfc4291) doivent inclure des crochets de délimitation et adopter une [forme conventionnelle](https://tools.ietf.org/html/rfc4291#section-2.2) (par exemple, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`). Les adresses IPv6 ne sont pas les seules à rechercher une égalité logique entre différents formats, et aucune canonicalisation n’est effectuée.</li><li>La non-restriction des hôtes autorisés peut permettre à un attaquant d’usurper les liens générés par le service.</li></ul>La valeur par défaut est un `IList<string>` vide. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Utilisez l’en-tête spécifié par cette propriété à la place de celui spécifié par [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName). Cette option est utilisée quand le proxy/redirecteur utilise un en-tête autre que l’en-tête `X-Forwarded-For` pour transférer les informations.<br><br>Par défaut, il s’agit de `X-Forwarded-For`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Identifie les redirecteurs à traiter. Consultez [l’énumération ForwardedHeaders](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) pour obtenir la liste des champs qui s’appliquent. En règle générale, les valeurs attribuées à cette propriété sont `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.<br><br>La valeur par défaut est [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders). |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Utilisez l’en-tête spécifié par cette propriété à la place de celui spécifié par [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName). Cette option est utilisée quand le proxy/redirecteur utilise un en-tête autre que l’en-tête `X-Forwarded-Host` pour transférer les informations.<br><br>Par défaut, il s’agit de `X-Forwarded-Host`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Utilisez l’en-tête spécifié par cette propriété à la place de celui spécifié par [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName). Cette option est utilisée quand le proxy/redirecteur utilise un en-tête autre que l’en-tête `X-Forwarded-Proto` pour transférer les informations.<br><br>Par défaut, il s’agit de `X-Forwarded-Proto`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Limite le nombre d’entrées dans les en-têtes qui sont traités. Définissez cette option sur `null` pour désactiver la limite, mais seulement si `KnownProxies` ou `KnownNetworks` sont configurés. Définir une valeur non `null` est une précaution (mais pas une garantie) pour vous prémunir contre les proxys mal configurés et les requêtes malveillantes provenant de canaux latéraux sur le réseau.<br><br>Le middleware des en-têtes transférés traite les en-têtes dans l’ordre inverse de droite à gauche. Si la valeur par défaut (`1`) est utilisée, seule la valeur la plus à droite dans les en-têtes est traitée, sauf si la valeur de `ForwardLimit` est augmentée.<br><br>Par défaut, il s’agit de `1`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Plages d’adresses des réseaux connus à partir desquels les en-têtes transférés peuvent être acceptés. Indiquez les plages d’adresses IP à l’aide de la notation CIDR (Classless Interdomain Routing).<br><br>Si le serveur utilise des sockets en mode double, les adresses IPv4 sont fournies dans un format IPv6 (par exemple, `10.0.0.1` dans IPv4 représentée dans IPv6 en tant que `::ffff:10.0.0.1`). Consultez [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). Déterminez si ce format est nécessaire en examinant [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).<br><br>La valeur par défaut est une `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> contenant une seule entrée pour `IPAddress.Loopback`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Adresses des proxy connus à partir desquels les en-têtes transférés peuvent être acceptés. Utilisez `KnownProxies` pour spécifier des correspondances d’adresse IP exactes.<br><br>Si le serveur utilise des sockets en mode double, les adresses IPv4 sont fournies dans un format IPv6 (par exemple, `10.0.0.1` dans IPv4 représentée dans IPv6 en tant que `::ffff:10.0.0.1`). Consultez [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). Déterminez si ce format est nécessaire en examinant [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).<br><br>La valeur par défaut est une `IList`\<<xref:System.Net.IPAddress>> contenant une seule entrée pour `IPAddress.IPv6Loopback`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Utilisez l’en-tête spécifié par cette propriété à la place de celui spécifié par [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).<br><br>Par défaut, il s’agit de `X-Original-For`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Utilisez l’en-tête spécifié par cette propriété à la place de celui spécifié par [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).<br><br>Par défaut, il s’agit de `X-Original-Host`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Utilisez l’en-tête spécifié par cette propriété à la place de celui spécifié par [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).<br><br>Par défaut, il s’agit de `X-Original-Proto`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Cette option impose que le nombre de valeurs d’en-tête soit synchronisé avec les [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) en cours de traitement.<br><br>La valeur par défaut dans ASP.NET Core 1.x est `true`. La valeur par défaut dans ASP.NET Core versions 2.0 ou ultérieures est `false`. |

## <a name="scenarios-and-use-cases"></a>Scénarios et cas d’usage

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a>Quand il est impossible d’ajouter des en-têtes transférés et que toutes les requêtes sont sécurisées

Dans certains cas, il peut être impossible d’ajouter des en-têtes transférés aux requêtes qui sont transmises par proxy à l’application. Si le proxy impose que toutes les requêtes externes publiques soient de type HTTPS, vous pouvez définir le schéma manuellement dans `Startup.Configure` avant d’utiliser tout type de middleware :

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

Vous pouvez désactiver ce code avec une variable d’environnement ou un autre paramètre de configuration dans un environnement de préproduction ou de développement.

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a>Traiter la base du chemin et les proxys qui changent le chemin de la requête

Certains proxys passent le chemin tel quel, mais avec un chemin de base d’application qui doit être supprimé afin que le routage fonctionne correctement. Le middleware [UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) fractionne le chemin en [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) et le chemin de base d’application en [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).

Si `/foo` est le chemin de base d’application pour un chemin de proxy passé en tant que `/foo/api/1`, le middleware définit `Request.PathBase` sur `/foo` et `Request.Path` sur `/api/1` avec la commande suivante :

```csharp
app.UsePathBase("/foo");
```

Le chemin et la base du chemin d’origine sont réappliqués quand le middleware est rappelé dans l’ordre inverse. Pour plus d’informations sur le traitement des commandes de middleware, consultez le site <xref:fundamentals/middleware/index>.

Si le proxy supprime le chemin (par exemple, en transférant `/foo/api/1` vers `/api/1`), corrigez les redirections et les liens en définissant la propriété [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) de la requête :

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

Si le proxy ajoute des données de chemin, abandonnez la partie du chemin pour corriger les redirections et les liens en utilisant <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> et en l’attribuant à la propriété <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> :

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a>Configuration d’un proxy qui utilise des noms d’en-têtes différents

Si le proxy n’utilise pas d’en-têtes nommés `X-Forwarded-For` et `X-Forwarded-Proto` pour transférer l’adresse/le port du proxy ainsi que les informations du schéma d’origine, définissez les options <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> et <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> pour faire correspondre les noms d’en-têtes utilisés par le proxy :

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a>Transférer le schéma pour les serveurs proxy inverses Linux et non IIS

Les applications qui appellent <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> et <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> permettent de placer un site dans une boucle infinie, s’il est déployé sur Azure Linux App Service, une machine virtuelle Azure Linux ou derrière tout autre proxy inverse en dehors d’IIS. Le proxy inverse met fin à TLS, et Kestrel n’a pas connaissance du schéma de requête approprié. OAuth et OIDC sont également défaillants dans cette configuration, car ils génèrent des redirections incorrectes. <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> ajoute et configure le middleware (intergiciel) des en-têtes transférés durant l’exécution avec IIS, mais il n’existe aucune configuration automatique correspondante pour Linux (intégration d’Apache ou de Nginx).

Pour transférer le schéma à partir du proxy dans les scénarios non basés sur IIS, ajoutez et configurez le middleware des en-têtes transférés. Dans `Startup.ConfigureServices`, utilisez le code suivant :

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="certificate-forwarding"></a>Transfert de certificat 

### <a name="azure"></a>Azure

Pour configurer Azure App Service pour le transfert de certificats, consultez [configurer l’authentification mutuelle TLS pour les Azure App service](/azure/app-service/app-service-web-configure-tls-mutual-auth). Les instructions suivantes concernent la configuration de l’application ASP.NET Core.

Dans `Startup.Configure` , ajoutez le code suivant avant l’appel à `app.UseAuthentication();` :

```csharp
app.UseCertificateForwarding();
```


Configurez l’intergiciel (middleware) de transfert de certificats pour spécifier le nom d’en-tête utilisé par Azure. Dans `Startup.ConfigureServices` , ajoutez le code suivant pour configurer l’en-tête à partir duquel l’intergiciel génère un certificat :

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a>Autres proxys Web

Si vous utilisez un proxy qui n’est pas IIS ou le Application Request Routing d’Azure App Service (ARR), configurez le proxy pour transférer le certificat qu’il a reçu dans un en-tête HTTP. Dans `Startup.Configure` , ajoutez le code suivant avant l’appel à `app.UseAuthentication();` :

```csharp
app.UseCertificateForwarding();
```

Configurez l’intergiciel (middleware) de transfert de certificat pour spécifier le nom d’en-tête. Dans `Startup.ConfigureServices` , ajoutez le code suivant pour configurer l’en-tête à partir duquel l’intergiciel génère un certificat :

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

Si le proxy n’encodage pas en base64 du certificat (comme c’est le cas avec Nginx), définissez l' `HeaderConverter` option. Prenons l’exemple suivant dans `Startup.ConfigureServices` :

```csharp
services.AddCertificateForwarding(options =>
{
    options.CertificateHeader = "YOUR_CUSTOM_HEADER_NAME";
    options.HeaderConverter = (headerValue) => 
    {
        var clientCertificate = 
           /* some conversion logic to create an X509Certificate2 */
        return clientCertificate;
    }
});
```

## <a name="troubleshoot"></a>Dépanner

Quand des en-têtes ne sont pas transférés comme prévu, activez la [journalisation](xref:fundamentals/logging/index). Si les journaux ne fournissent pas suffisamment d’informations pour résoudre le problème, énumérez les en-têtes de requête reçus par le serveur. Utilisez le middleware inclus pour écrire les en-têtes de requête dans une réponse d’application, ou consignez les en-têtes dans le fichier journal. 

Pour écrire les en-têtes dans la réponse de l’application, placez le middleware inline de terminal suivant juste après l’appel à <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> dans `Startup.Configure` :

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

Il est possible d’écrire dans les journaux plutôt que dans le corps de la réponse ; ainsi, le site fonctionnera normalement pendant le débogage.

Pour écrire dans les journaux plutôt que dans le corps de la réponse :

* Injectez `ILogger<Startup>` dans la classe `Startup`, en suivant les instructions de [Créer des journaux dans Startup](xref:fundamentals/logging/index#create-logs-in-startup).
* Placez le middleware inline suivant juste après l’appel à <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> dans `Startup.Configure`.

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

Lors du traitement, les valeurs `X-Forwarded-{For|Proto|Host}` sont déplacées vers `X-Original-{For|Proto|Host}`. S’il existe plusieurs valeurs dans un en-tête donné, le middleware des en-têtes transférés traite les en-têtes dans l’ordre inverse de droite à gauche. La valeur par défaut de `ForwardLimit` est `1` si bien que seule la valeur la plus à droite dans les en-têtes est traitée, sauf si la valeur de `ForwardLimit` est augmentée.

L’adresse IP distante d’origine de la requête doit correspondre à une entrée dans les listes `KnownProxies` ou `KnownNetworks` pour que les en-têtes transférés soient traités. L’usurpation des en-têtes s’en trouve limitée, car les redirecteurs émanant de proxys non approuvés ne sont pas acceptés. Lorsqu’un proxy inconnu est détecté, la journalisation indique l’adresse du proxy :

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

Dans l’exemple précédent, 10.0.0.100 est un serveur proxy. Si le serveur est un proxy approuvé, ajoutez l’adresse IP du serveur à la liste `KnownProxies` (ou ajoutez un réseau approuvé à la liste `KnownNetworks`) dans `Startup.ConfigureServices`. Pour plus d’informations, consultez la section [Options du middleware des en-têtes transférés](#forwarded-headers-middleware-options).

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> Autorisez uniquement des réseaux et des proxies approuvés pour transférer des en-têtes. Sinon, des attaques d’[usurpation d’adresse IP](https://www.iplocation.net/ip-spoofing) sont possibles.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:host-and-deploy/web-farm>
* [Microsoft Security Advisory CVE-2018-0787 : vulnérabilité d’élévation de privilèges ASP.NET Core](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Dans la configuration recommandée d’ASP.NET Core, l’application est hébergée à l’aide du module IIS/ASP.NET Core, de Nginx ou d’Apache. Les serveurs proxy, les équilibreurs de charge et autres dispositifs réseau masquent souvent les informations sur la requête avant qu’elle n’atteigne l’application :

* Quand les requêtes HTTPS sont transmises par proxy via HTTP, le schéma d’origine (HTTPS) est perdu et doit être transféré dans un en-tête.
* Étant donné qu’une requête adressée à une application transite par le proxy au lieu de provenir directement de sa source sur Internet ou le réseau d’entreprise, l’adresse IP cliente d’origine doit également être transférée dans un en-tête.

Ces informations peuvent être importantes pour traiter les requêtes, par exemple dans les redirections, l’authentification, la génération de lien, l’évaluation des stratégies et la géolocalisation des clients.

## <a name="forwarded-headers"></a>En-têtes transférés

Par convention, les proxys transfèrent les informations dans des en-têtes HTTP.

| En-tête | Description |
| ------ | ----------- |
| X-Forwarded-For | Contient des informations sur le client à l’origine de la requête et les proxys suivants dans une chaîne de proxys. Ce paramètre peut contenir des adresses IP (et, éventuellement, des numéros de port). Dans une chaîne de serveurs proxy, le premier paramètre indique le client sur lequel la requête a été effectuée pour la première fois. Viennent ensuite les identificateurs des proxy suivants. Le dernier proxy dans la chaîne ne figure pas dans la liste des paramètres. L’adresse IP du dernier proxy et éventuellement un numéro de port sont disponibles en tant qu’adresse IP distante au niveau de la couche transport. |
| X-Forwarded-Proto | Valeur du schéma d’origine (HTTP/HTTPS). La valeur peut également être une liste de schémas si la requête a franchi plusieurs proxys. |
| X-Forwarded-Host | Valeur d’origine du champ d’en-tête de l’hôte. En règle générale, les proxys ne modifient pas l’en-tête de l’hôte. Consultez le document [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) pour plus d’informations sur une vulnérabilité par élévation de privilèges qui affecte les systèmes où le proxy ne valide pas les en-têtes d’hôte ou ne les limite pas à des valeurs correctes connues. |

L’intergiciel des en-têtes transférés, dans le package [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/), lit ces en-têtes et renseigne les champs associés sur <xref:Microsoft.AspNetCore.Http.HttpContext>.

Le middleware met à jour les éléments suivants :

* [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): défini à l’aide de la `X-Forwarded-For` valeur d’en-tête. Des paramètres supplémentaires déterminent la façon dont le middleware définit `RemoteIpAddress`. Pour plus d’informations, consultez les [options du middleware des en-têtes transférés](#forwarded-headers-middleware-options).
* [HttpContext. Request. Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): défini à l’aide de la `X-Forwarded-Proto` valeur d’en-tête.
* [HttpContext. Request. Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): défini à l’aide de la `X-Forwarded-Host` valeur d’en-tête.

Vous pouvez configurer les [paramètres par défaut](#forwarded-headers-middleware-options) du middleware des en-têtes transférés. Les paramètres par défaut sont :

* Il y a *un seul proxy* entre l’application et la source des requêtes.
* Seules des adresses de bouclage sont configurées pour les proxys connus et les réseaux connus.
* Les en-têtes transférés sont nommés `X-Forwarded-For` et `X-Forwarded-Proto`.

Certaines appliances réseau nécessitent une configuration supplémentaire pour ajouter les en-têtes `X-Forwarded-For` et `X-Forwarded-Proto`. Consultez les instructions du fabricant de votre appliance si des requêtes en proxy ne contiennent pas ces en-têtes quand elles atteignent l’application. Si l’appliance utilise des noms d’en-têtes autres que `X-Forwarded-For` et `X-Forwarded-Proto`, définissez les options <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> et <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> pour faire correspondre les noms d’en-têtes utilisés par l’appliance. Pour plus d’informations, consultez [Options du middleware des en-têtes transférés](#forwarded-headers-middleware-options) et [Configuration d’un proxy qui utilise des noms d’en-têtes différents](#configuration-for-a-proxy-that-uses-different-header-names).

## <a name="iisiis-express-and-aspnet-core-module"></a>Module ASP.NET Core et IIS/IIS Express

Le middleware des en-têtes transférés est activé par défaut par le [middleware d’intégration IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) lorsque l’application est hébergée [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) derrière IIS et le module ASP.NET Core. Le middleware des en-têtes transférés est activé pour s’exécuter en premier dans le pipeline des middlewares avec une configuration limitée propre au module ASP.NET Core en raison de problèmes d’approbation liés aux en-têtes transférés (par exemple, [usurpation d’adresse IP](https://www.iplocation.net/ip-spoofing)). Le middleware est configuré pour transférer les en-têtes `X-Forwarded-For` et `X-Forwarded-Proto` et est limité à un proxy localhost unique. Si une configuration supplémentaire est requise, consultez les [options du middleware des en-têtes transférés](#forwarded-headers-middleware-options).

## <a name="other-proxy-server-and-load-balancer-scenarios"></a>Autres scénarios avec un serveur proxy et un équilibreur de charge

En dehors de l’utilisation de [l’intégration IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) lors de l’hébergement [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), le middleware des en-têtes transférés n’est pas activé par défaut. L’intergiciel des en-têtes transférés doit être activé pour qu’une application puisse traiter les en-têtes transférés avec <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>. Une fois l’intergiciel activé, si aucune option <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> ne lui est spécifiée, les valeurs par défaut [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) ont pour valeur [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).

Configurez l’intergiciel avec <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> pour transférer les en-têtes `X-Forwarded-For` et `X-Forwarded-Proto` dans `Startup.ConfigureServices`. Appelez la méthode <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> dans `Startup.Configure` avant d’appeler d’autres intergiciels :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = 
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
    }

    app.UseStaticFiles();
    // In ASP.NET Core 1.x, replace the following line with: app.UseIdentity();
    app.UseAuthentication();
    app.UseMvc();
}
```

> [!NOTE]
> Si aucun <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> n’est spécifié dans `Startup.ConfigureServices` ou directement sur la méthode d’extension avec <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, les en-têtes par défaut à transférer sont [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders). La propriété <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> doit être configurée avec les en-têtes à transférer.

## <a name="nginx-configuration"></a>Configuration de Nginx

Pour transférer les en-têtes `X-Forwarded-For` et `X-Forwarded-Proto`, consultez <xref:host-and-deploy/linux-nginx#configure-nginx>. Pour plus d’informations, consultez [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/) (NGINX : utilisation de l’en-tête Forwarded).

## <a name="apache-configuration"></a>Configuration d’Apache

`X-Forwarded-For` est ajouté automatiquement (consultez [Module Apache mod_proxy : en-têtes de requête du mandataire inverse](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)). Pour plus d’informations sur la façon de transférer l’en-tête `X-Forwarded-Proto`, consultez <xref:host-and-deploy/linux-apache#configure-apache>.

## <a name="forwarded-headers-middleware-options"></a>Options du middleware des en-têtes transférés

<xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> contrôlent le comportement de l’intergiciel des en-têtes transférés. L’exemple suivant change les valeurs par défaut :

* Limitez le nombre d’entrées dans les en-têtes transférés à `2`.
* Ajoutez l’adresse de proxy connue `127.0.10.1`.
* Changez le nom de l’en-tête transféré en remplaçant la valeur par défaut `X-Forwarded-For` par `X-Forwarded-For-My-Custom-Header-Name`.

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| Option | Description |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Limite les hôtes par l’en-tête `X-Forwarded-Host` aux valeurs fournies.<ul><li>Les valeurs sont comparées à l’aide de l’option ordinal-ignore-case.</li><li>Les numéros de port doivent être exclus.</li><li>Si la liste est vide, tous les hôtes sont autorisés.</li><li>Un caractère générique de niveau supérieur `*` autorise tous les hôtes non vides.</li><li>Les caractères génériques de sous-domaine sont autorisés, mais ne correspondent pas au domaine racine. Par exemple, `*.contoso.com` correspond au sous-domaine `foo.contoso.com`, mais pas au domaine racine `contoso.com`.</li><li>Les noms d’hôte Unicode sont autorisés, mais sont convertis en [Punycode](https://tools.ietf.org/html/rfc3492) à des fins de correspondance.</li><li>Les [adresses IPv6](https://tools.ietf.org/html/rfc4291) doivent inclure des crochets de délimitation et adopter une [forme conventionnelle](https://tools.ietf.org/html/rfc4291#section-2.2) (par exemple, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`). Les adresses IPv6 ne sont pas les seules à rechercher une égalité logique entre différents formats, et aucune canonicalisation n’est effectuée.</li><li>La non-restriction des hôtes autorisés peut permettre à un attaquant d’usurper les liens générés par le service.</li></ul>La valeur par défaut est un `IList<string>` vide. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Utilisez l’en-tête spécifié par cette propriété à la place de celui spécifié par [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName). Cette option est utilisée quand le proxy/redirecteur utilise un en-tête autre que l’en-tête `X-Forwarded-For` pour transférer les informations.<br><br>Par défaut, il s’agit de `X-Forwarded-For`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Identifie les redirecteurs à traiter. Consultez [l’énumération ForwardedHeaders](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) pour obtenir la liste des champs qui s’appliquent. En règle générale, les valeurs attribuées à cette propriété sont `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.<br><br>La valeur par défaut est [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders). |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Utilisez l’en-tête spécifié par cette propriété à la place de celui spécifié par [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName). Cette option est utilisée quand le proxy/redirecteur utilise un en-tête autre que l’en-tête `X-Forwarded-Host` pour transférer les informations.<br><br>Par défaut, il s’agit de `X-Forwarded-Host`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Utilisez l’en-tête spécifié par cette propriété à la place de celui spécifié par [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName). Cette option est utilisée quand le proxy/redirecteur utilise un en-tête autre que l’en-tête `X-Forwarded-Proto` pour transférer les informations.<br><br>Par défaut, il s’agit de `X-Forwarded-Proto`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Limite le nombre d’entrées dans les en-têtes qui sont traités. Définissez cette option sur `null` pour désactiver la limite, mais seulement si `KnownProxies` ou `KnownNetworks` sont configurés. Définir une valeur non `null` est une précaution (mais pas une garantie) pour vous prémunir contre les proxys mal configurés et les requêtes malveillantes provenant de canaux latéraux sur le réseau.<br><br>Le middleware des en-têtes transférés traite les en-têtes dans l’ordre inverse de droite à gauche. Si la valeur par défaut (`1`) est utilisée, seule la valeur la plus à droite dans les en-têtes est traitée, sauf si la valeur de `ForwardLimit` est augmentée.<br><br>Par défaut, il s’agit de `1`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Plages d’adresses des réseaux connus à partir desquels les en-têtes transférés peuvent être acceptés. Indiquez les plages d’adresses IP à l’aide de la notation CIDR (Classless Interdomain Routing).<br><br>Si le serveur utilise des sockets en mode double, les adresses IPv4 sont fournies dans un format IPv6 (par exemple, `10.0.0.1` dans IPv4 représentée dans IPv6 en tant que `::ffff:10.0.0.1`). Consultez [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). Déterminez si ce format est nécessaire en examinant [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*). Pour plus d’informations, consultez la section [Configuration pour une adresse IPv4 représentée sous la forme d’une adresse IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).<br><br>La valeur par défaut est une `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> contenant une seule entrée pour `IPAddress.Loopback`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Adresses des proxy connus à partir desquels les en-têtes transférés peuvent être acceptés. Utilisez `KnownProxies` pour spécifier des correspondances d’adresse IP exactes.<br><br>Si le serveur utilise des sockets en mode double, les adresses IPv4 sont fournies dans un format IPv6 (par exemple, `10.0.0.1` dans IPv4 représentée dans IPv6 en tant que `::ffff:10.0.0.1`). Consultez [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). Déterminez si ce format est nécessaire en examinant [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*). Pour plus d’informations, consultez la section [Configuration pour une adresse IPv4 représentée sous la forme d’une adresse IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).<br><br>La valeur par défaut est une `IList`\<<xref:System.Net.IPAddress>> contenant une seule entrée pour `IPAddress.IPv6Loopback`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Utilisez l’en-tête spécifié par cette propriété à la place de celui spécifié par [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).<br><br>Par défaut, il s’agit de `X-Original-For`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Utilisez l’en-tête spécifié par cette propriété à la place de celui spécifié par [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).<br><br>Par défaut, il s’agit de `X-Original-Host`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Utilisez l’en-tête spécifié par cette propriété à la place de celui spécifié par [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).<br><br>Par défaut, il s’agit de `X-Original-Proto`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Cette option impose que le nombre de valeurs d’en-tête soit synchronisé avec les [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) en cours de traitement.<br><br>La valeur par défaut dans ASP.NET Core 1.x est `true`. La valeur par défaut dans ASP.NET Core versions 2.0 ou ultérieures est `false`. |

## <a name="scenarios-and-use-cases"></a>Scénarios et cas d’usage

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a>Quand il est impossible d’ajouter des en-têtes transférés et que toutes les requêtes sont sécurisées

Dans certains cas, il peut être impossible d’ajouter des en-têtes transférés aux requêtes qui sont transmises par proxy à l’application. Si le proxy impose que toutes les requêtes externes publiques soient de type HTTPS, vous pouvez définir le schéma manuellement dans `Startup.Configure` avant d’utiliser tout type de middleware :

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

Vous pouvez désactiver ce code avec une variable d’environnement ou un autre paramètre de configuration dans un environnement de préproduction ou de développement.

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a>Traiter la base du chemin et les proxys qui changent le chemin de la requête

Certains proxys passent le chemin tel quel, mais avec un chemin de base d’application qui doit être supprimé afin que le routage fonctionne correctement. Le middleware [UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) fractionne le chemin en [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) et le chemin de base d’application en [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).

Si `/foo` est le chemin de base d’application pour un chemin de proxy passé en tant que `/foo/api/1`, le middleware définit `Request.PathBase` sur `/foo` et `Request.Path` sur `/api/1` avec la commande suivante :

```csharp
app.UsePathBase("/foo");
```

Le chemin et la base du chemin d’origine sont réappliqués quand le middleware est rappelé dans l’ordre inverse. Pour plus d’informations sur le traitement des commandes de middleware, consultez le site <xref:fundamentals/middleware/index>.

Si le proxy supprime le chemin (par exemple, en transférant `/foo/api/1` vers `/api/1`), corrigez les redirections et les liens en définissant la propriété [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) de la requête :

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

Si le proxy ajoute des données de chemin, abandonnez la partie du chemin pour corriger les redirections et les liens en utilisant <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> et en l’attribuant à la propriété <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> :

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a>Configuration d’un proxy qui utilise des noms d’en-têtes différents

Si le proxy n’utilise pas d’en-têtes nommés `X-Forwarded-For` et `X-Forwarded-Proto` pour transférer l’adresse/le port du proxy ainsi que les informations du schéma d’origine, définissez les options <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> et <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> pour faire correspondre les noms d’en-têtes utilisés par le proxy :

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a>Configuration pour une adresse IPv4 représentée sous la forme d’une adresse IPv6

Si le serveur utilise des sockets en mode double, les adresses IPv4 sont fournies dans un format IPv6 (par exemple, `10.0.0.1` dans IPv4 représentée dans IPv6 en tant que `::ffff:10.0.0.1` ou `::ffff:a00:1`). Consultez [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). Déterminez si ce format est nécessaire en examinant [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).

Dans l’exemple suivant, une adresse réseau qui fournit les en-têtes transférés est ajoutée à la liste `KnownNetworks` au format IPv6.

Adresse IPv4 : `10.11.12.1/8`

Adresse IPv6 convertie : `::ffff:10.11.12.1`  
Longueur de préfixe converti : 104

Vous pouvez également fournir l’adresse au format hexadécimal (`10.11.12.1` représenté dans IPv6 en tant que `::ffff:0a0b:0c01`). Lors de la conversion d’une adresse IPv4 vers IPv6, ajoutez 96 à la longueur du préfixe CIDR (`8` dans l’exemple) pour prendre en compte le préfixe IPv6 `::ffff:` supplémentaire (8 + 96 = 104). 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a>Transférer le schéma pour les serveurs proxy inverses Linux et non IIS

Les applications qui appellent <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> et <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> permettent de placer un site dans une boucle infinie, s’il est déployé sur Azure Linux App Service, une machine virtuelle Azure Linux ou derrière tout autre proxy inverse en dehors d’IIS. Le proxy inverse met fin à TLS, et Kestrel n’a pas connaissance du schéma de requête approprié. OAuth et OIDC sont également défaillants dans cette configuration, car ils génèrent des redirections incorrectes. <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> ajoute et configure le middleware (intergiciel) des en-têtes transférés durant l’exécution avec IIS, mais il n’existe aucune configuration automatique correspondante pour Linux (intégration d’Apache ou de Nginx).

Pour transférer le schéma à partir du proxy dans les scénarios non basés sur IIS, ajoutez et configurez le middleware des en-têtes transférés. Dans `Startup.ConfigureServices`, utilisez le code suivant :

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="troubleshoot"></a>Dépanner

Quand des en-têtes ne sont pas transférés comme prévu, activez la [journalisation](xref:fundamentals/logging/index). Si les journaux ne fournissent pas suffisamment d’informations pour résoudre le problème, énumérez les en-têtes de requête reçus par le serveur. Utilisez le middleware inclus pour écrire les en-têtes de requête dans une réponse d’application, ou consignez les en-têtes dans le fichier journal. 

Pour écrire les en-têtes dans la réponse de l’application, placez le middleware inline de terminal suivant juste après l’appel à <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> dans `Startup.Configure` :

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

Il est possible d’écrire dans les journaux plutôt que dans le corps de la réponse ; ainsi, le site fonctionnera normalement pendant le débogage.

Pour écrire dans les journaux plutôt que dans le corps de la réponse :

* Injectez `ILogger<Startup>` dans la classe `Startup`, en suivant les instructions de [Créer des journaux dans Startup](xref:fundamentals/logging/index#create-logs-in-startup).
* Placez le middleware inline suivant juste après l’appel à <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> dans `Startup.Configure`.

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

Lors du traitement, les valeurs `X-Forwarded-{For|Proto|Host}` sont déplacées vers `X-Original-{For|Proto|Host}`. S’il existe plusieurs valeurs dans un en-tête donné, le middleware des en-têtes transférés traite les en-têtes dans l’ordre inverse de droite à gauche. La valeur par défaut de `ForwardLimit` est `1` si bien que seule la valeur la plus à droite dans les en-têtes est traitée, sauf si la valeur de `ForwardLimit` est augmentée.

L’adresse IP distante d’origine de la requête doit correspondre à une entrée dans les listes `KnownProxies` ou `KnownNetworks` pour que les en-têtes transférés soient traités. L’usurpation des en-têtes s’en trouve limitée, car les redirecteurs émanant de proxys non approuvés ne sont pas acceptés. Lorsqu’un proxy inconnu est détecté, la journalisation indique l’adresse du proxy :

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

Dans l’exemple précédent, 10.0.0.100 est un serveur proxy. Si le serveur est un proxy approuvé, ajoutez l’adresse IP du serveur à la liste `KnownProxies` (ou ajoutez un réseau approuvé à la liste `KnownNetworks`) dans `Startup.ConfigureServices`. Pour plus d’informations, consultez la section [Options du middleware des en-têtes transférés](#forwarded-headers-middleware-options).

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> Autorisez uniquement des réseaux et des proxies approuvés pour transférer des en-têtes. Sinon, des attaques d’[usurpation d’adresse IP](https://www.iplocation.net/ip-spoofing) sont possibles.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:host-and-deploy/web-farm>
* [Microsoft Security Advisory CVE-2018-0787 : vulnérabilité d’élévation de privilèges ASP.NET Core](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end
