---
title: Implémentation du serveur web HTTP.sys dans ASP.NET Core
author: rick-anderson
description: Découvrez HTTP.sys, un serveur web pour ASP.NET Core sous Windows. Basé sur le pilote en mode noyau HTTP.sys, HTTP.sys est une solution qui permet d’établir une connexion directe à Internet sans IIS ni Kestrel.
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
uid: fundamentals/servers/httpsys
ms.openlocfilehash: 9c65abd5a055bb677a14921296316e7e03760bc2
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "96855363"
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a>Implémentation du serveur web HTTP.sys dans ASP.NET Core

Par [Tom Dykstra](https://github.com/tdykstra) et [Chris Ross](https://github.com/Tratcher)

::: moniker range=">= aspnetcore-3.1"

[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) est un [serveur web pour ASP.NET Core](xref:fundamentals/servers/index) qui s’exécute uniquement sous Windows. HTTP.sys est une alternative au serveur [Kestrel](xref:fundamentals/servers/kestrel) et offre des fonctionnalités supplémentaires par rapport à celui-ci.

> [!IMPORTANT]
> HTTP.sys est incompatible avec le [Module ASP.NET Core](xref:host-and-deploy/aspnet-core-module) et n’est pas utilisable avec IIS ni IIS Express.

HTTP.sys prend en charge les fonctionnalités suivantes :

* [Authentification Windows](xref:security/authentication/windowsauth)
* Partage de port
* HTTPS avec SNI
* HTTP/2 sur TLS (Windows 10 et versions ultérieures)
* Transmission de fichier directe
* Mise en cache des réponses
* WebSockets (Windows 8 et versions ultérieures)

Versions de Windows prises en charge :

* Windows 7 ou version ultérieure
* Windows Server 2008 R2 ou version ultérieure

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="when-to-use-httpsys"></a>Quand utiliser HTTP.sys

HTTP.sys est utile pour les déploiements lorsque :

* Il est nécessaire d’exposer directement le serveur à Internet sans passer par IIS.

  ![HTTP.sys communique directement avec Internet](httpsys/_static/httpsys-to-internet.png)

* Un déploiement interne implique une fonctionnalité qui n’est pas disponible dans Kestrel, par exemple, [l’authentification Windows](xref:security/authentication/windowsauth).

  ![HTTP.sys communique directement avec le réseau interne](httpsys/_static/httpsys-to-internal.png)

HTTP.sys est une technologie aboutie qui assure une protection contre de nombreux types d’attaques et offre la robustesse, la sécurité et l’extensibilité d’un serveur web haut de gamme. Pour sa part, IIS s’exécute en tant qu’écouteur HTTP sur HTTP.sys.

## <a name="http2-support"></a>Assistance HTTP/2

[HTTP/2](https://httpwg.org/specs/rfc7540.html) est activé pour les applications ASP.NET Core si les conditions de base suivantes sont remplies :

* Windows Server 2016/Windows 10 ou version ultérieure
* Connexion [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)
* TLS 1.2 ou connexion ultérieure

Si une connexion HTTP/2 est établie, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) retourne `HTTP/2`.

HTTP/2 est activé par défaut. Si une connexion HTTP/2 n’est pas établie, la connexion revient à HTTP/1.1. Dans une prochaine version de Windows, les indicateurs de configuration HTTP/2 seront disponibles, y compris la possibilité de désactivation de HTTP/2 avec HTTP.sys.

## <a name="kernel-mode-authentication-with-kerberos"></a>Authentification en mode noyau avec Kerberos

HTTP.sys délègue l’authentification en mode noyau avec le protocole d’authentification Kerberos. L’authentification en mode utilisateur n’est pas prise en charge avec Kerberos et HTTP.sys. Le compte d’ordinateur doit être utilisé pour déchiffrer le ticket/jeton Kerberos obtenu à partir d’Active Directory et transféré par le client au serveur afin d’authentifier l’utilisateur. Inscrivez le nom de principal du service (SPN) pour l’hôte, et non l’utilisateur de l’application.

## <a name="how-to-use-httpsys"></a>Comment utiliser HTTP.sys

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a>Configurer l’application ASP.NET Core afin d’utiliser HTTP.sys

Appelez la méthode d’extension <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> au moment de générer l’hôte, en spécifiant les options <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> nécessaires. L’exemple suivant définit les options sur leurs valeurs par défaut :

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

Le reste de la configuration de HTTP.sys est géré par le biais des [paramètres du Registre](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).

**Options HTTP.sys**

| Propriété | Description | Default |
| -------- | ----------- | :-----: |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO> | Contrôle si l’entrée/sortie synchrone est autorisée pour le `HttpContext.Request.Body` et le `HttpContext.Response.Body`. | `false` |
| [Authentication.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Autorise les requêtes anonymes. | `true` |
| [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | Spécifie les schémas d’authentification autorisés. Peut être modifié à tout moment avant la suppression de l’écouteur. Les valeurs sont fournies par l' [énumération AuthenticationSchemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic` , `Kerberos` ,, `Negotiate` `None` et `NTLM` . | `None` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching> | Tente la mise en cache [en mode noyau](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) pour les réponses comportant un en-tête compatible. La réponse peut ne pas inclure d’en-tête `Set-Cookie`, `Vary` ou `Pragma`. Elle doit comporter un en-tête `Cache-Control``public` et soit une valeur `shared-max-age` ou `max-age`, soit un en-tête `Expires`. | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Http503Verbosity> | Le comportement de HTTP.sys lors du rejet des demandes en raison de conditions de limitation. | [Http503VerbosityLevel. <br> Bases](xref:Microsoft.AspNetCore.Server.HttpSys.Http503VerbosityLevel) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | Nombre maximal d'acceptations simultanées. | 5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | Nombre maximum de connexions simultanées à accepter. Utilisez `-1` pour signifier l’infini, et `null` pour utiliser le paramètre du Registre qui s’applique à l’ordinateur dans son ensemble. | `null`<br>(au niveau de l’ordinateur<br>défini |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | Consultez la section <a href="#maxrequestbodysize">MaxRequestBodySize</a>. | 30 000 000 octets<br>(env. 28,6 Mo) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | Nombre maximal de demandes pouvant être placées en file d'attente. | 1 000 |
| `RequestQueueMode` | Cela indique si le serveur est responsable de la création et de la configuration de la file d’attente des demandes, ou s’il doit être attaché à une file d’attente existante.<br>La plupart des options de configuration existantes ne s’appliquent pas lors de l’attachement à une file d’attente existante. | `RequestQueueMode.Create` |
| `RequestQueueName` | Nom de la file d’attente de demandes de HTTP.sys. | `null` (File d’attente anonyme) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Indique si les écritures dans le corps de la réponse qui échouent en raison d’une déconnexion du client doivent lever des exceptions ou se terminer normalement. | `false`<br>(se terminer normalement) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Expose la configuration <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> de HTTP.sys, également paramétrable dans le Registre. Suivez les liens de l’API pour en savoir plus sur chaque paramètre, y compris les valeurs par défaut :<ul><li><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody?displayProperty=nameWithType>: Temps accordé à l’API du serveur HTTP pour vider le corps d’entité sur une connexion Keep-Alive.</li><li><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody?displayProperty=nameWithType>: Temps autorisé pour l’arrivée du corps d’entité de la requête.</li><li><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait?displayProperty=nameWithType>: Temps accordé à l’API du serveur HTTP pour analyser l’en-tête de la demande.</li><li><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection?displayProperty=nameWithType>: Durée autorisée pour une connexion inactive.</li><li><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond?displayProperty=nameWithType>: Taux d’envoi minimal pour la réponse.</li><li><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue?displayProperty=nameWithType>: Durée pendant laquelle la demande doit rester dans la file d’attente des demandes avant que l’application la récupère.</li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | Spécifiez <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> à inscrire auprès de HTTP.sys. Le plus utile est <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add%2A?displayProperty=nameWithType> , qui est utilisé pour ajouter un préfixe à la collection. Ces choix peuvent être modifiés à tout moment avant la suppression de l’écouteur. |  |

<a name="maxrequestbodysize"></a>

**MaxRequestBodySize**

Taille maximale autorisée pour le corps d’une demande, en octets. Lorsque la valeur est `null`, elle est illimitée. Cette limite est sans effet sur les connexions mises à niveau, qui sont illimitées.

Pour remplacer la limite d’un seul `IActionResult` dans une application MVC ASP.NET Core, nous vous recommandons d’utiliser l’attribut <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> sur une méthode d’action :

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Une exception est levée si l’application tente de configurer la limite sur une demande dont l’application a commencé la lecture. La propriété `IsReadOnly` indique si la propriété `MaxRequestBodySize` est en lecture seule, et donc s’il est trop tard pour configurer la limite.

Si l’application doit remplacer <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> par requête, utilisez <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature> :

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

Si vous utilisez Visual Studio, vérifiez que l’application n’est pas configurée pour exécuter IIS ou IIS Express.

Dans Visual Studio, le profil de démarrage par défaut est destiné à IIS Express. Pour exécuter le projet en tant qu’application console, changez manuellement le profil sélectionné, comme dans la capture d’écran suivante :

![Sélectionner le profil d’application console](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a>Configurer Windows Server

1. Identifiez les ports à ouvrir pour l’application et utilisez le [pare-feu Windows](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) ou les applets de commande PowerShell [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) pour ouvrir les ports du pare-feu et ainsi permettre au trafic d’atteindre HTTP.sys. Dans les commandes et la configuration de l’application suivantes, le port 443 est utilisé.

1. Lors du déploiement sur une machine virtuelle Azure, ouvrez les ports dans le [groupe de sécurité réseau](/azure/virtual-machines/windows/nsg-quickstart-portal). Dans les commandes et la configuration de l’application suivantes, le port 443 est utilisé.

1. Obtenez et installez des certificats X.509, si nécessaire.

   Sous Windows, créez des certificats auto-signés à l’aide de la [cmdlet PowerShell New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate). Vous trouverez un exemple non pris en charge sur la page [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).

   Installez des certificats auto-signés ou signés par l’autorité de certification dans le magasin **Ordinateur Local** > **Personnel** du serveur.

1. Si l’application est un [déploiement dépendant de .NET Framework](/dotnet/core/deploying/#framework-dependent-deployments-fdd), installez .NET Core, .NET Framework ou les deux (si l’application est une application .NET Core ciblant .NET Framework).

   * **.Net Core**: si l’application requiert .net Core, obtenez et exécutez le programme d’installation du **Runtime .net Core** à partir des [téléchargements .net Core](https://dotnet.microsoft.com/download). N’installez pas le Kit SDK complet sur le serveur.
   * **.NET Framework**: si l’application requiert des .NET Framework, consultez le [Guide d’installation de .NET Framework](/dotnet/framework/install/). Installez la version requise de .NET Framework. Le programme d’installation du .NET Framework le plus récent est disponible depuis la page [Téléchargements .NET Core](https://dotnet.microsoft.com/download).

   Si l’application est un [déploiement autonome](/dotnet/core/deploying/#self-contained-deployments-scd), elle inclut le runtime dans son déploiement. Aucune installation de framework n’est requise sur le serveur.

1. Configurez les ports et les URL de l’application.

   Par défaut, ASP.NET Core est lié à `http://localhost:5000`. Pour configurer les ports et les préfixes d’URL, il existe plusieurs possibilités :

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * Arguments de ligne de commande `urls`
   * `ASPNETCORE_URLS` variable d’environnement
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   L’exemple de code suivant montre comment utiliser <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> avec l’adresse IP locale du serveur `10.0.0.4` sur le port 443 :

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   `UrlPrefixes` présente l’avantage de générer immédiatement un message d’erreur en cas de préfixe mal formé.

   Les paramètres de `UrlPrefixes` remplacent les paramètres `UseUrls`/`urls`/`ASPNETCORE_URLS`. Par conséquent, avec `UseUrls`, `urls` et la variable d’environnement `ASPNETCORE_URLS`, il est plus facile de basculer entre Kestrel et HTTP.sys.

   HTTP.sys utilise les [formats de chaîne UrlPrefix de l’API de serveur HTTP](/windows/win32/http/urlprefix-strings).

   > [!WARNING]
   > Les liaisons génériques de niveau supérieur (`http://*:80/` et `http://+:80`) ne doivent **pas** être utilisées. Les liaisons génériques de niveau supérieur créent des failles de sécurité dans l’application. Cela s’applique aux caractères génériques forts et faibles. Utilisez des noms d’hôte ou des adresses IP explicites plutôt que des caractères génériques. Une liaison générique de sous-domaine (par exemple, `*.mysub.com`) ne constitue pas un risque de sécurité si vous contrôlez le domaine parent en entier (par opposition à `*.com`, qui est vulnérable). Pour plus d’informations, consultez [RFC 7230 : Section 5,4 : Host](https://tools.ietf.org/html/rfc7230#section-5.4).

1. Préenregistrez les préfixes d’URL sur le serveur.

   L’outil intégré pour configurer HTTP.sys est *netsh.exe*. *netsh.exe* permet de réserver des préfixes d’URL et d’assigner des certificats X.509. L’outil requiert des privilèges Administrateur.

   Utilisez l’outil *netsh.exe* pour enregistrer les URL pour l’application :

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * `<URL>`: Le Uniform Resource Locator qualifié complet (URL). N’utilisez pas une liaison de caractère générique. Utilisez un nom d’hôte ou une adresse IP locale valide. *L’URL doit inclure une barre oblique de fin.*
   * `<USER>`: Spécifie le nom d’utilisateur ou de groupe d’utilisateurs.

   Dans l’exemple suivant, l’adresse IP locale du serveur est `10.0.0.4` :

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   Lorsqu’une URL est inscrite, l’outil répond avec `URL reservation successfully added`.

   Pour supprimer une URL inscrite, utilisez la commande `delete urlacl` :

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. Inscrivez les certificats X.509 sur le serveur.

   Utilisez l’outil *netsh.exe* pour enregistrer les certificats pour l’application :

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * `<IP>`: Spécifie l’adresse IP locale pour la liaison. N’utilisez pas une liaison de caractère générique. Utilisez une adresse IP valide.
   * `<PORT>`: Spécifie le port pour la liaison.
   * `<THUMBPRINT>`: Empreinte numérique du certificat X. 509.
   * `<GUID>`: GUID généré par le développeur pour représenter l’application à des fins d’information.

   À titre de référence, stockez le GUID dans l’application en tant que balise de package :

   * Dans Visual Studio :
     * Ouvrez les propriétés du projet de l’application en cliquant avec le bouton droit sur l’application dans **l’Explorateur de solutions** et en sélectionnant **Propriétés**.
     * Sélectionnez l’onglet **Package**.
     * Entrez le GUID que vous avez créé dans le champ **Balises**.
   * Si vous n’utilisez pas Visual Studio :
     * Ouvrez le chemin d’accès vers le fichier de projet de l’application.
     * Ajoutez une propriété `<PackageTags>` à un `<PropertyGroup>` nouveau ou existant avec le GUID que vous avez créé :

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   Dans l’exemple suivant :

   * L’adresse IP locale du serveur est `10.0.0.4`.
   * Un générateur de GUID aléatoire en ligne fournit la valeur `appid`.

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   Lorsqu’un certificat est inscrit, l’outil répond avec `SSL Certificate successfully added`.

   Pour supprimer l’inscription d’un certificat, utilisez la commande `delete sslcert` :

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   Documentation de référence de *netsh.exe* :

   * [Commandes netsh pour le protocole HTTP (Hypertext Transfer Protocol)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))
   * [Chaînes UrlPrefix](/windows/win32/http/urlprefix-strings)

1. Exécutez l'application.

   Les privilèges administrateur ne sont pas requis pour exécuter l’application lors d’une liaison à localhost à l’aide de HTTP (pas HTTPS) avec un numéro de port supérieur à 1024. Pour les autres configurations (par exemple, l’utilisation d’une adresse IP locale ou la liaison au port 443), exécutez l’application avec des privilèges administrateur.

   L’application répond à l’adresse IP publique du serveur. Dans cet exemple, le serveur est contacté à partir d’Internet à son adresse IP publique `104.214.79.47`.

   Un certificat de développement est utilisé dans cet exemple. La page est chargée en toute sécurité après le contournement de l’avertissement de certificat non approuvé du navigateur.

   ![Fenêtre de navigateur affichant la page Index de l’application chargée](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scénarios avec un serveur proxy et un équilibreur de charge

Pour les applications hébergées par HTTP.sys qui interagissent avec les demandes provenant d’Internet ou d’un réseau d’entreprise, une configuration supplémentaire peut être nécessaire en cas d’hébergement derrière des serveurs proxy et des équilibreurs de charge. Pour plus d’informations, consultez l’article [Configurer ASP.NET Core pour l’utilisation de serveurs proxy et d’équilibreurs de charge](xref:host-and-deploy/proxy-load-balancer).

## <a name="advanced-http2-features-to-support-grpc"></a>Fonctionnalités HTTP/2 avancées pour la prise en charge de gRPC

Des fonctionnalités HTTP/2 supplémentaires dans HTTP.sys prennent en charge gRPC, notamment la prise en charge des codes de fin de réponse et l’envoi d’images de réinitialisation.

Configuration requise pour exécuter gRPC avec HTTP.SYS :

* Windows 10, version 19041,508 ou ultérieure du système d’exploitation
* TLS 1.2 ou connexion ultérieure

### <a name="trailers"></a>Bandes-annonce

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a>Réinitialiser

[!INCLUDE[](~/includes/reset.md)]

## <a name="additional-resources"></a>Ressources supplémentaires

* [Activer l’authentification Windows avec HTTP.sys](xref:security/authentication/windowsauth#httpsys)
* [API de serveur HTTP](/windows/win32/http/http-api-start-page)
* [Référentiel GitHub aspnet/HttpSysServer (code source)](https://github.com/aspnet/HttpSysServer/)
* [L’hôte](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) est un [serveur web pour ASP.NET Core](xref:fundamentals/servers/index) qui s’exécute uniquement sous Windows. HTTP.sys est une alternative au serveur [Kestrel](xref:fundamentals/servers/kestrel) et offre des fonctionnalités supplémentaires par rapport à celui-ci.

> [!IMPORTANT]
> HTTP.sys est incompatible avec le [Module ASP.NET Core](xref:host-and-deploy/aspnet-core-module) et n’est pas utilisable avec IIS ni IIS Express.

HTTP.sys prend en charge les fonctionnalités suivantes :

* [Authentification Windows](xref:security/authentication/windowsauth)
* Partage de port
* HTTPS avec SNI
* HTTP/2 sur TLS (Windows 10 et versions ultérieures)
* Transmission de fichier directe
* Mise en cache des réponses
* WebSockets (Windows 8 et versions ultérieures)

Versions de Windows prises en charge :

* Windows 7 ou version ultérieure
* Windows Server 2008 R2 ou version ultérieure

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="when-to-use-httpsys"></a>Quand utiliser HTTP.sys

HTTP.sys est utile pour les déploiements lorsque :

* Il est nécessaire d’exposer directement le serveur à Internet sans passer par IIS.

  ![HTTP.sys communique directement avec Internet](httpsys/_static/httpsys-to-internet.png)

* Un déploiement interne implique une fonctionnalité qui n’est pas disponible dans Kestrel, par exemple, [l’authentification Windows](xref:security/authentication/windowsauth).

  ![HTTP.sys communique directement avec le réseau interne](httpsys/_static/httpsys-to-internal.png)

HTTP.sys est une technologie aboutie qui assure une protection contre de nombreux types d’attaques et offre la robustesse, la sécurité et l’extensibilité d’un serveur web haut de gamme. Pour sa part, IIS s’exécute en tant qu’écouteur HTTP sur HTTP.sys.

## <a name="http2-support"></a>Assistance HTTP/2

[HTTP/2](https://httpwg.org/specs/rfc7540.html) est activé pour les applications ASP.NET Core si les conditions de base suivantes sont remplies :

* Windows Server 2016/Windows 10 ou version ultérieure
* Connexion [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)
* TLS 1.2 ou connexion ultérieure

Si une connexion HTTP/2 est établie, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) retourne `HTTP/2`.

HTTP/2 est activé par défaut. Si une connexion HTTP/2 n’est pas établie, la connexion revient à HTTP/1.1. Dans une prochaine version de Windows, les indicateurs de configuration HTTP/2 seront disponibles, y compris la possibilité de désactivation de HTTP/2 avec HTTP.sys.

## <a name="kernel-mode-authentication-with-kerberos"></a>Authentification en mode noyau avec Kerberos

HTTP.sys délègue l’authentification en mode noyau avec le protocole d’authentification Kerberos. L’authentification en mode utilisateur n’est pas prise en charge avec Kerberos et HTTP.sys. Le compte d’ordinateur doit être utilisé pour déchiffrer le ticket/jeton Kerberos obtenu à partir d’Active Directory et transféré par le client au serveur afin d’authentifier l’utilisateur. Inscrivez le nom de principal du service (SPN) pour l’hôte, et non l’utilisateur de l’application.

## <a name="how-to-use-httpsys"></a>Comment utiliser HTTP.sys

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a>Configurer l’application ASP.NET Core afin d’utiliser HTTP.sys

Appelez la méthode d’extension <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> au moment de générer l’hôte, en spécifiant les options <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> nécessaires. L’exemple suivant définit les options sur leurs valeurs par défaut :

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

Le reste de la configuration de HTTP.sys est géré par le biais des [paramètres du Registre](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).

**Options HTTP.sys**

| Propriété | Description | Default |
| -------- | ----------- | :-----: |
| [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Contrôle si l’entrée/sortie synchrone est autorisée pour le `HttpContext.Request.Body` et le `HttpContext.Response.Body`. | `false` |
| [Authentication.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Autorise les requêtes anonymes. | `true` |
| [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | Spécifie les schémas d’authentification autorisés. Peut être modifié à tout moment avant la suppression de l’écouteur. Les valeurs sont fournies par l' [énumération AuthenticationSchemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic` , `Kerberos` ,, `Negotiate` `None` et `NTLM` . | `None` |
| [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Tente la mise en cache [en mode noyau](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) pour les réponses comportant un en-tête compatible. La réponse peut ne pas inclure d’en-tête `Set-Cookie`, `Vary` ou `Pragma`. Elle doit comporter un en-tête `Cache-Control``public` et soit une valeur `shared-max-age` ou `max-age`, soit un en-tête `Expires`. | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | Nombre maximal d'acceptations simultanées. | 5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | Nombre maximum de connexions simultanées à accepter. Utilisez `-1` pour signifier l’infini, et `null` pour utiliser le paramètre du Registre qui s’applique à l’ordinateur dans son ensemble. | `null`<br>(au niveau de l’ordinateur<br>défini |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | Consultez la section <a href="#maxrequestbodysize">MaxRequestBodySize</a>. | 30 000 000 octets<br>(env. 28,6 Mo) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | Nombre maximal de demandes pouvant être placées en file d'attente. | 1 000 |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Indique si les écritures dans le corps de la réponse qui échouent en raison d’une déconnexion du client doivent lever des exceptions ou se terminer normalement. | `false`<br>(se terminer normalement) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Expose la configuration <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> de HTTP.sys, également paramétrable dans le Registre. Suivez les liens de l’API pour en savoir plus sur chaque paramètre, y compris les valeurs par défaut :<ul><li>[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): temps accordé à l’API du serveur http pour vider le corps d’entité sur une connexion Keep-Alive.</li><li>[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): temps autorisé pour l’arrivée du corps d’entité de la requête.</li><li>[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): temps accordé à l’API du serveur http pour analyser l’en-tête de la demande.</li><li>[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): temps autorisé pour une connexion inactive.</li><li>[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): taux d’envoi minimal pour la réponse.</li><li>[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): temps pendant lequel la demande doit rester dans la file d’attente des demandes avant que l’application la récupère.</li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | Spécifiez <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> à inscrire auprès de HTTP.sys. La plus utile est [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), qui permet d’ajouter un préfixe à la collection. Ces choix peuvent être modifiés à tout moment avant la suppression de l’écouteur. |  |

<a name="maxrequestbodysize"></a>

**MaxRequestBodySize**

Taille maximale autorisée pour le corps d’une demande, en octets. Lorsque la valeur est `null`, elle est illimitée. Cette limite est sans effet sur les connexions mises à niveau, qui sont illimitées.

Pour remplacer la limite d’un seul `IActionResult` dans une application MVC ASP.NET Core, nous vous recommandons d’utiliser l’attribut <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> sur une méthode d’action :

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Une exception est levée si l’application tente de configurer la limite sur une demande dont l’application a commencé la lecture. La propriété `IsReadOnly` indique si la propriété `MaxRequestBodySize` est en lecture seule, et donc s’il est trop tard pour configurer la limite.

Si l’application doit remplacer <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> par requête, utilisez <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature> :

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

Si vous utilisez Visual Studio, vérifiez que l’application n’est pas configurée pour exécuter IIS ou IIS Express.

Dans Visual Studio, le profil de démarrage par défaut est destiné à IIS Express. Pour exécuter le projet en tant qu’application console, changez manuellement le profil sélectionné, comme dans la capture d’écran suivante :

![Sélectionner le profil d’application console](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a>Configurer Windows Server

1. Identifiez les ports à ouvrir pour l’application et utilisez le [pare-feu Windows](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) ou les applets de commande PowerShell [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) pour ouvrir les ports du pare-feu et ainsi permettre au trafic d’atteindre HTTP.sys. Dans les commandes et la configuration de l’application suivantes, le port 443 est utilisé.

1. Lors du déploiement sur une machine virtuelle Azure, ouvrez les ports dans le [groupe de sécurité réseau](/azure/virtual-machines/windows/nsg-quickstart-portal). Dans les commandes et la configuration de l’application suivantes, le port 443 est utilisé.

1. Obtenez et installez des certificats X.509, si nécessaire.

   Sous Windows, créez des certificats auto-signés à l’aide de la [cmdlet PowerShell New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate). Vous trouverez un exemple non pris en charge sur la page [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).

   Installez des certificats auto-signés ou signés par l’autorité de certification dans le magasin **Ordinateur Local** > **Personnel** du serveur.

1. Si l’application est un [déploiement dépendant de .NET Framework](/dotnet/core/deploying/#framework-dependent-deployments-fdd), installez .NET Core, .NET Framework ou les deux (si l’application est une application .NET Core ciblant .NET Framework).

   * **.Net Core**: si l’application requiert .net Core, obtenez et exécutez le programme d’installation du **Runtime .net Core** à partir des [téléchargements .net Core](https://dotnet.microsoft.com/download). N’installez pas le Kit SDK complet sur le serveur.
   * **.NET Framework**: si l’application requiert des .NET Framework, consultez le [Guide d’installation de .NET Framework](/dotnet/framework/install/). Installez la version requise de .NET Framework. Le programme d’installation du .NET Framework le plus récent est disponible depuis la page [Téléchargements .NET Core](https://dotnet.microsoft.com/download).

   Si l’application est un [déploiement autonome](/dotnet/core/deploying/#self-contained-deployments-scd), elle inclut le runtime dans son déploiement. Aucune installation de framework n’est requise sur le serveur.

1. Configurez les ports et les URL de l’application.

   Par défaut, ASP.NET Core est lié à `http://localhost:5000`. Pour configurer les ports et les préfixes d’URL, il existe plusieurs possibilités :

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * Arguments de ligne de commande `urls`
   * `ASPNETCORE_URLS` variable d’environnement
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   L’exemple de code suivant montre comment utiliser <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> avec l’adresse IP locale du serveur `10.0.0.4` sur le port 443 :

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   `UrlPrefixes` présente l’avantage de générer immédiatement un message d’erreur en cas de préfixe mal formé.

   Les paramètres de `UrlPrefixes` remplacent les paramètres `UseUrls`/`urls`/`ASPNETCORE_URLS`. Par conséquent, avec `UseUrls`, `urls` et la variable d’environnement `ASPNETCORE_URLS`, il est plus facile de basculer entre Kestrel et HTTP.sys.

   HTTP.sys utilise les [formats de chaîne UrlPrefix de l’API de serveur HTTP](/windows/win32/http/urlprefix-strings).

   > [!WARNING]
   > Les liaisons génériques de niveau supérieur (`http://*:80/` et `http://+:80`) ne doivent **pas** être utilisées. Les liaisons génériques de niveau supérieur créent des failles de sécurité dans l’application. Cela s’applique aux caractères génériques forts et faibles. Utilisez des noms d’hôte ou des adresses IP explicites plutôt que des caractères génériques. Une liaison générique de sous-domaine (par exemple, `*.mysub.com`) ne constitue pas un risque de sécurité si vous contrôlez le domaine parent en entier (par opposition à `*.com`, qui est vulnérable). Pour plus d’informations, consultez [RFC 7230 : Section 5,4 : Host](https://tools.ietf.org/html/rfc7230#section-5.4).

1. Préenregistrez les préfixes d’URL sur le serveur.

   L’outil intégré pour configurer HTTP.sys est *netsh.exe*. *netsh.exe* permet de réserver des préfixes d’URL et d’assigner des certificats X.509. L’outil requiert des privilèges Administrateur.

   Utilisez l’outil *netsh.exe* pour enregistrer les URL pour l’application :

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * `<URL>`: Le Uniform Resource Locator qualifié complet (URL). N’utilisez pas une liaison de caractère générique. Utilisez un nom d’hôte ou une adresse IP locale valide. *L’URL doit inclure une barre oblique de fin.*
   * `<USER>`: Spécifie le nom d’utilisateur ou de groupe d’utilisateurs.

   Dans l’exemple suivant, l’adresse IP locale du serveur est `10.0.0.4` :

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   Lorsqu’une URL est inscrite, l’outil répond avec `URL reservation successfully added`.

   Pour supprimer une URL inscrite, utilisez la commande `delete urlacl` :

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. Inscrivez les certificats X.509 sur le serveur.

   Utilisez l’outil *netsh.exe* pour enregistrer les certificats pour l’application :

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * `<IP>`: Spécifie l’adresse IP locale pour la liaison. N’utilisez pas une liaison de caractère générique. Utilisez une adresse IP valide.
   * `<PORT>`: Spécifie le port pour la liaison.
   * `<THUMBPRINT>`: Empreinte numérique du certificat X. 509.
   * `<GUID>`: GUID généré par le développeur pour représenter l’application à des fins d’information.

   À titre de référence, stockez le GUID dans l’application en tant que balise de package :

   * Dans Visual Studio :
     * Ouvrez les propriétés du projet de l’application en cliquant avec le bouton droit sur l’application dans **l’Explorateur de solutions** et en sélectionnant **Propriétés**.
     * Sélectionnez l’onglet **Package**.
     * Entrez le GUID que vous avez créé dans le champ **Balises**.
   * Si vous n’utilisez pas Visual Studio :
     * Ouvrez le chemin d’accès vers le fichier de projet de l’application.
     * Ajoutez une propriété `<PackageTags>` à un `<PropertyGroup>` nouveau ou existant avec le GUID que vous avez créé :

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   Dans l’exemple suivant :

   * L’adresse IP locale du serveur est `10.0.0.4`.
   * Un générateur de GUID aléatoire en ligne fournit la valeur `appid`.

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   Lorsqu’un certificat est inscrit, l’outil répond avec `SSL Certificate successfully added`.

   Pour supprimer l’inscription d’un certificat, utilisez la commande `delete sslcert` :

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   Documentation de référence de *netsh.exe* :

   * [Commandes netsh pour le protocole HTTP (Hypertext Transfer Protocol)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))
   * [Chaînes UrlPrefix](/windows/win32/http/urlprefix-strings)

1. Exécutez l'application.

   Les privilèges administrateur ne sont pas requis pour exécuter l’application lors d’une liaison à localhost à l’aide de HTTP (pas HTTPS) avec un numéro de port supérieur à 1024. Pour les autres configurations (par exemple, l’utilisation d’une adresse IP locale ou la liaison au port 443), exécutez l’application avec des privilèges administrateur.

   L’application répond à l’adresse IP publique du serveur. Dans cet exemple, le serveur est contacté à partir d’Internet à son adresse IP publique `104.214.79.47`.

   Un certificat de développement est utilisé dans cet exemple. La page est chargée en toute sécurité après le contournement de l’avertissement de certificat non approuvé du navigateur.

   ![Fenêtre de navigateur affichant la page Index de l’application chargée](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scénarios avec un serveur proxy et un équilibreur de charge

Pour les applications hébergées par HTTP.sys qui interagissent avec les demandes provenant d’Internet ou d’un réseau d’entreprise, une configuration supplémentaire peut être nécessaire en cas d’hébergement derrière des serveurs proxy et des équilibreurs de charge. Pour plus d’informations, consultez l’article [Configurer ASP.NET Core pour l’utilisation de serveurs proxy et d’équilibreurs de charge](xref:host-and-deploy/proxy-load-balancer).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Activer l’authentification Windows avec HTTP.sys](xref:security/authentication/windowsauth#httpsys)
* [API de serveur HTTP](/windows/win32/http/http-api-start-page)
* [Référentiel GitHub aspnet/HttpSysServer (code source)](https://github.com/aspnet/HttpSysServer/)
* [L’hôte](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) est un [serveur web pour ASP.NET Core](xref:fundamentals/servers/index) qui s’exécute uniquement sous Windows. HTTP.sys est une alternative au serveur [Kestrel](xref:fundamentals/servers/kestrel) et offre des fonctionnalités supplémentaires par rapport à celui-ci.

> [!IMPORTANT]
> HTTP.sys est incompatible avec le [Module ASP.NET Core](xref:host-and-deploy/aspnet-core-module) et n’est pas utilisable avec IIS ni IIS Express.

HTTP.sys prend en charge les fonctionnalités suivantes :

* [Authentification Windows](xref:security/authentication/windowsauth)
* Partage de port
* HTTPS avec SNI
* HTTP/2 sur TLS (Windows 10 et versions ultérieures)
* Transmission de fichier directe
* Mise en cache des réponses
* WebSockets (Windows 8 et versions ultérieures)

Versions de Windows prises en charge :

* Windows 7 ou version ultérieure
* Windows Server 2008 R2 ou version ultérieure

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="when-to-use-httpsys"></a>Quand utiliser HTTP.sys

HTTP.sys est utile pour les déploiements lorsque :

* Il est nécessaire d’exposer directement le serveur à Internet sans passer par IIS.

  ![HTTP.sys communique directement avec Internet](httpsys/_static/httpsys-to-internet.png)

* Un déploiement interne implique une fonctionnalité qui n’est pas disponible dans Kestrel, par exemple, [l’authentification Windows](xref:security/authentication/windowsauth).

  ![HTTP.sys communique directement avec le réseau interne](httpsys/_static/httpsys-to-internal.png)

HTTP.sys est une technologie aboutie qui assure une protection contre de nombreux types d’attaques et offre la robustesse, la sécurité et l’extensibilité d’un serveur web haut de gamme. Pour sa part, IIS s’exécute en tant qu’écouteur HTTP sur HTTP.sys.

## <a name="http2-support"></a>Assistance HTTP/2

[HTTP/2](https://httpwg.org/specs/rfc7540.html) est activé pour les applications ASP.NET Core si les conditions de base suivantes sont remplies :

* Windows Server 2016/Windows 10 ou version ultérieure
* Connexion [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)
* TLS 1.2 ou connexion ultérieure

Si une connexion HTTP/2 est établie, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) retourne `HTTP/2`.

HTTP/2 est activé par défaut. Si une connexion HTTP/2 n’est pas établie, la connexion revient à HTTP/1.1. Dans une prochaine version de Windows, les indicateurs de configuration HTTP/2 seront disponibles, y compris la possibilité de désactivation de HTTP/2 avec HTTP.sys.

## <a name="kernel-mode-authentication-with-kerberos"></a>Authentification en mode noyau avec Kerberos

HTTP.sys délègue l’authentification en mode noyau avec le protocole d’authentification Kerberos. L’authentification en mode utilisateur n’est pas prise en charge avec Kerberos et HTTP.sys. Le compte d’ordinateur doit être utilisé pour déchiffrer le ticket/jeton Kerberos obtenu à partir d’Active Directory et transféré par le client au serveur afin d’authentifier l’utilisateur. Inscrivez le nom de principal du service (SPN) pour l’hôte, et non l’utilisateur de l’application.

## <a name="how-to-use-httpsys"></a>Comment utiliser HTTP.sys

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a>Configurer l’application ASP.NET Core afin d’utiliser HTTP.sys

Une référence de package dans le fichier projet n’est pas requise lors de l’utilisation de l' [offre Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) ([NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)). Si vous n'utilisez pas le métapaquet `Microsoft.AspNetCore.App`, ajoutez une référence de package à [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).

Appelez la méthode d’extension <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> au moment de générer l’hôte, en spécifiant les options <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> nécessaires. L’exemple suivant définit les options sur leurs valeurs par défaut :

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

Le reste de la configuration de HTTP.sys est géré par le biais des [paramètres du Registre](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).

**Options HTTP.sys**

| Propriété | Description | Default |
| -------- | ----------- | :-----: |
| [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Contrôle si l’entrée/sortie synchrone est autorisée pour le `HttpContext.Request.Body` et le `HttpContext.Response.Body`. | `true` |
| [Authentication.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Autorise les requêtes anonymes. | `true` |
| [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | Spécifie les schémas d’authentification autorisés. Peut être modifié à tout moment avant la suppression de l’écouteur. Les valeurs sont fournies par l' [énumération AuthenticationSchemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic` , `Kerberos` ,, `Negotiate` `None` et `NTLM` . | `None` |
| [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Tente la mise en cache [en mode noyau](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) pour les réponses comportant un en-tête compatible. La réponse peut ne pas inclure d’en-tête `Set-Cookie`, `Vary` ou `Pragma`. Elle doit comporter un en-tête `Cache-Control``public` et soit une valeur `shared-max-age` ou `max-age`, soit un en-tête `Expires`. | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | Nombre maximal d'acceptations simultanées. | 5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | Nombre maximum de connexions simultanées à accepter. Utilisez `-1` pour signifier l’infini, et `null` pour utiliser le paramètre du Registre qui s’applique à l’ordinateur dans son ensemble. | `null`<br>(au niveau de l’ordinateur<br>défini |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | Consultez la section <a href="#maxrequestbodysize">MaxRequestBodySize</a>. | 30 000 000 octets<br>(env. 28,6 Mo) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | Nombre maximal de demandes pouvant être placées en file d'attente. | 1 000 |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Indique si les écritures dans le corps de la réponse qui échouent en raison d’une déconnexion du client doivent lever des exceptions ou se terminer normalement. | `false`<br>(se terminer normalement) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Expose la configuration <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> de HTTP.sys, également paramétrable dans le Registre. Suivez les liens de l’API pour en savoir plus sur chaque paramètre, y compris les valeurs par défaut :<ul><li>[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): temps accordé à l’API du serveur http pour vider le corps d’entité sur une connexion Keep-Alive.</li><li>[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): temps autorisé pour l’arrivée du corps d’entité de la requête.</li><li>[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): temps accordé à l’API du serveur http pour analyser l’en-tête de la demande.</li><li>[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): temps autorisé pour une connexion inactive.</li><li>[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): taux d’envoi minimal pour la réponse.</li><li>[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): temps pendant lequel la demande doit rester dans la file d’attente des demandes avant que l’application la récupère.</li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | Spécifiez <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> à inscrire auprès de HTTP.sys. La plus utile est [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), qui permet d’ajouter un préfixe à la collection. Ces choix peuvent être modifiés à tout moment avant la suppression de l’écouteur. |  |

<a name="maxrequestbodysize"></a>

**MaxRequestBodySize**

Taille maximale autorisée pour le corps d’une demande, en octets. Lorsque la valeur est `null`, elle est illimitée. Cette limite est sans effet sur les connexions mises à niveau, qui sont illimitées.

Pour remplacer la limite d’un seul `IActionResult` dans une application MVC ASP.NET Core, nous vous recommandons d’utiliser l’attribut <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> sur une méthode d’action :

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Une exception est levée si l’application tente de configurer la limite sur une demande dont l’application a commencé la lecture. La propriété `IsReadOnly` indique si la propriété `MaxRequestBodySize` est en lecture seule, et donc s’il est trop tard pour configurer la limite.

Si l’application doit remplacer <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> par requête, utilisez <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature> :

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

Si vous utilisez Visual Studio, vérifiez que l’application n’est pas configurée pour exécuter IIS ou IIS Express.

Dans Visual Studio, le profil de démarrage par défaut est destiné à IIS Express. Pour exécuter le projet en tant qu’application console, changez manuellement le profil sélectionné, comme dans la capture d’écran suivante :

![Sélectionner le profil d’application console](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a>Configurer Windows Server

1. Identifiez les ports à ouvrir pour l’application et utilisez le [pare-feu Windows](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) ou les applets de commande PowerShell [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) pour ouvrir les ports du pare-feu et ainsi permettre au trafic d’atteindre HTTP.sys. Dans les commandes et la configuration de l’application suivantes, le port 443 est utilisé.

1. Lors du déploiement sur une machine virtuelle Azure, ouvrez les ports dans le [groupe de sécurité réseau](/azure/virtual-machines/windows/nsg-quickstart-portal). Dans les commandes et la configuration de l’application suivantes, le port 443 est utilisé.

1. Obtenez et installez des certificats X.509, si nécessaire.

   Sous Windows, créez des certificats auto-signés à l’aide de la [cmdlet PowerShell New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate). Vous trouverez un exemple non pris en charge sur la page [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).

   Installez des certificats auto-signés ou signés par l’autorité de certification dans le magasin **Ordinateur Local** > **Personnel** du serveur.

1. Si l’application est un [déploiement dépendant de .NET Framework](/dotnet/core/deploying/#framework-dependent-deployments-fdd), installez .NET Core, .NET Framework ou les deux (si l’application est une application .NET Core ciblant .NET Framework).

   * **.Net Core**: si l’application requiert .net Core, obtenez et exécutez le programme d’installation du **Runtime .net Core** à partir des [téléchargements .net Core](https://dotnet.microsoft.com/download). N’installez pas le Kit SDK complet sur le serveur.
   * **.NET Framework**: si l’application requiert des .NET Framework, consultez le [Guide d’installation de .NET Framework](/dotnet/framework/install/). Installez la version requise de .NET Framework. Le programme d’installation du .NET Framework le plus récent est disponible depuis la page [Téléchargements .NET Core](https://dotnet.microsoft.com/download).

   Si l’application est un [déploiement autonome](/dotnet/core/deploying/#self-contained-deployments-scd), elle inclut le runtime dans son déploiement. Aucune installation de framework n’est requise sur le serveur.

1. Configurez les ports et les URL de l’application.

   Par défaut, ASP.NET Core est lié à `http://localhost:5000`. Pour configurer les ports et les préfixes d’URL, il existe plusieurs possibilités :

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * Arguments de ligne de commande `urls`
   * `ASPNETCORE_URLS` variable d’environnement
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   L’exemple de code suivant montre comment utiliser <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> avec l’adresse IP locale du serveur `10.0.0.4` sur le port 443 :

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   `UrlPrefixes` présente l’avantage de générer immédiatement un message d’erreur en cas de préfixe mal formé.

   Les paramètres de `UrlPrefixes` remplacent les paramètres `UseUrls`/`urls`/`ASPNETCORE_URLS`. Par conséquent, avec `UseUrls`, `urls` et la variable d’environnement `ASPNETCORE_URLS`, il est plus facile de basculer entre Kestrel et HTTP.sys.

   HTTP.sys utilise les [formats de chaîne UrlPrefix de l’API de serveur HTTP](/windows/win32/http/urlprefix-strings).

   > [!WARNING]
   > Les liaisons génériques de niveau supérieur (`http://*:80/` et `http://+:80`) ne doivent **pas** être utilisées. Les liaisons génériques de niveau supérieur créent des failles de sécurité dans l’application. Cela s’applique aux caractères génériques forts et faibles. Utilisez des noms d’hôte ou des adresses IP explicites plutôt que des caractères génériques. Une liaison générique de sous-domaine (par exemple, `*.mysub.com`) ne constitue pas un risque de sécurité si vous contrôlez le domaine parent en entier (par opposition à `*.com`, qui est vulnérable). Pour plus d’informations, consultez [RFC 7230 : Section 5,4 : Host](https://tools.ietf.org/html/rfc7230#section-5.4).

1. Préenregistrez les préfixes d’URL sur le serveur.

   L’outil intégré pour configurer HTTP.sys est *netsh.exe*. *netsh.exe* permet de réserver des préfixes d’URL et d’assigner des certificats X.509. L’outil requiert des privilèges Administrateur.

   Utilisez l’outil *netsh.exe* pour enregistrer les URL pour l’application :

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * `<URL>`: Le Uniform Resource Locator qualifié complet (URL). N’utilisez pas une liaison de caractère générique. Utilisez un nom d’hôte ou une adresse IP locale valide. *L’URL doit inclure une barre oblique de fin.*
   * `<USER>`: Spécifie le nom d’utilisateur ou de groupe d’utilisateurs.

   Dans l’exemple suivant, l’adresse IP locale du serveur est `10.0.0.4` :

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   Lorsqu’une URL est inscrite, l’outil répond avec `URL reservation successfully added`.

   Pour supprimer une URL inscrite, utilisez la commande `delete urlacl` :

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. Inscrivez les certificats X.509 sur le serveur.

   Utilisez l’outil *netsh.exe* pour enregistrer les certificats pour l’application :

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * `<IP>`: Spécifie l’adresse IP locale pour la liaison. N’utilisez pas une liaison de caractère générique. Utilisez une adresse IP valide.
   * `<PORT>`: Spécifie le port pour la liaison.
   * `<THUMBPRINT>`: Empreinte numérique du certificat X. 509.
   * `<GUID>`: GUID généré par le développeur pour représenter l’application à des fins d’information.

   À titre de référence, stockez le GUID dans l’application en tant que balise de package :

   * Dans Visual Studio :
     * Ouvrez les propriétés du projet de l’application en cliquant avec le bouton droit sur l’application dans **l’Explorateur de solutions** et en sélectionnant **Propriétés**.
     * Sélectionnez l’onglet **Package**.
     * Entrez le GUID que vous avez créé dans le champ **Balises**.
   * Si vous n’utilisez pas Visual Studio :
     * Ouvrez le chemin d’accès vers le fichier de projet de l’application.
     * Ajoutez une propriété `<PackageTags>` à un `<PropertyGroup>` nouveau ou existant avec le GUID que vous avez créé :

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   Dans l’exemple suivant :

   * L’adresse IP locale du serveur est `10.0.0.4`.
   * Un générateur de GUID aléatoire en ligne fournit la valeur `appid`.

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   Lorsqu’un certificat est inscrit, l’outil répond avec `SSL Certificate successfully added`.

   Pour supprimer l’inscription d’un certificat, utilisez la commande `delete sslcert` :

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   Documentation de référence de *netsh.exe* :

   * [Commandes netsh pour le protocole HTTP (Hypertext Transfer Protocol)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))
   * [Chaînes UrlPrefix](/windows/win32/http/urlprefix-strings)

1. Exécutez l'application.

   Les privilèges administrateur ne sont pas requis pour exécuter l’application lors d’une liaison à localhost à l’aide de HTTP (pas HTTPS) avec un numéro de port supérieur à 1024. Pour les autres configurations (par exemple, l’utilisation d’une adresse IP locale ou la liaison au port 443), exécutez l’application avec des privilèges administrateur.

   L’application répond à l’adresse IP publique du serveur. Dans cet exemple, le serveur est contacté à partir d’Internet à son adresse IP publique `104.214.79.47`.

   Un certificat de développement est utilisé dans cet exemple. La page est chargée en toute sécurité après le contournement de l’avertissement de certificat non approuvé du navigateur.

   ![Fenêtre de navigateur affichant la page Index de l’application chargée](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scénarios avec un serveur proxy et un équilibreur de charge

Pour les applications hébergées par HTTP.sys qui interagissent avec les demandes provenant d’Internet ou d’un réseau d’entreprise, une configuration supplémentaire peut être nécessaire en cas d’hébergement derrière des serveurs proxy et des équilibreurs de charge. Pour plus d’informations, consultez l’article [Configurer ASP.NET Core pour l’utilisation de serveurs proxy et d’équilibreurs de charge](xref:host-and-deploy/proxy-load-balancer).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Activer l’authentification Windows avec HTTP.sys](xref:security/authentication/windowsauth#httpsys)
* [API de serveur HTTP](/windows/win32/http/http-api-start-page)
* [Référentiel GitHub aspnet/HttpSysServer (code source)](https://github.com/aspnet/HttpSysServer/)
* [L’hôte](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) est un [serveur web pour ASP.NET Core](xref:fundamentals/servers/index) qui s’exécute uniquement sous Windows. HTTP.sys est une alternative au serveur [Kestrel](xref:fundamentals/servers/kestrel) et offre des fonctionnalités supplémentaires par rapport à celui-ci.

> [!IMPORTANT]
> HTTP.sys est incompatible avec le [Module ASP.NET Core](xref:host-and-deploy/aspnet-core-module) et n’est pas utilisable avec IIS ni IIS Express.

HTTP.sys prend en charge les fonctionnalités suivantes :

* [Authentification Windows](xref:security/authentication/windowsauth)
* Partage de port
* HTTPS avec SNI
* HTTP/2 sur TLS (Windows 10 et versions ultérieures)
* Transmission de fichier directe
* Mise en cache des réponses
* WebSockets (Windows 8 et versions ultérieures)

Versions de Windows prises en charge :

* Windows 7 ou version ultérieure
* Windows Server 2008 R2 ou version ultérieure

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="when-to-use-httpsys"></a>Quand utiliser HTTP.sys

HTTP.sys est utile pour les déploiements lorsque :

* Il est nécessaire d’exposer directement le serveur à Internet sans passer par IIS.

  ![HTTP.sys communique directement avec Internet](httpsys/_static/httpsys-to-internet.png)

* Un déploiement interne implique une fonctionnalité qui n’est pas disponible dans Kestrel, par exemple, [l’authentification Windows](xref:security/authentication/windowsauth).

  ![HTTP.sys communique directement avec le réseau interne](httpsys/_static/httpsys-to-internal.png)

HTTP.sys est une technologie aboutie qui assure une protection contre de nombreux types d’attaques et offre la robustesse, la sécurité et l’extensibilité d’un serveur web haut de gamme. Pour sa part, IIS s’exécute en tant qu’écouteur HTTP sur HTTP.sys.

## <a name="http2-support"></a>Assistance HTTP/2

[HTTP/2](https://httpwg.org/specs/rfc7540.html) est activé pour les applications ASP.NET Core si les conditions de base suivantes sont remplies :

* Windows Server 2016/Windows 10 ou version ultérieure
* Connexion [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)
* TLS 1.2 ou connexion ultérieure

Si une connexion HTTP/2 est établie, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) retourne `HTTP/1.1`.

HTTP/2 est activé par défaut. Si une connexion HTTP/2 n’est pas établie, la connexion revient à HTTP/1.1. Dans une prochaine version de Windows, les indicateurs de configuration HTTP/2 seront disponibles, y compris la possibilité de désactivation de HTTP/2 avec HTTP.sys.

## <a name="kernel-mode-authentication-with-kerberos"></a>Authentification en mode noyau avec Kerberos

HTTP.sys délègue l’authentification en mode noyau avec le protocole d’authentification Kerberos. L’authentification en mode utilisateur n’est pas prise en charge avec Kerberos et HTTP.sys. Le compte d’ordinateur doit être utilisé pour déchiffrer le ticket/jeton Kerberos obtenu à partir d’Active Directory et transféré par le client au serveur afin d’authentifier l’utilisateur. Inscrivez le nom de principal du service (SPN) pour l’hôte, et non l’utilisateur de l’application.

## <a name="how-to-use-httpsys"></a>Comment utiliser HTTP.sys

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a>Configurer l’application ASP.NET Core afin d’utiliser HTTP.sys

Une référence de package dans le fichier projet n’est pas requise lors de l’utilisation de l' [offre Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) ([NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)). Si vous n'utilisez pas le métapaquet `Microsoft.AspNetCore.App`, ajoutez une référence de package à [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).

Appelez la méthode d’extension <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> au moment de générer l’hôte, en spécifiant les options <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> nécessaires. L’exemple suivant définit les options sur leurs valeurs par défaut :

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

Le reste de la configuration de HTTP.sys est géré par le biais des [paramètres du Registre](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).

**Options HTTP.sys**

| Propriété | Description | Default |
| -------- | ----------- | :-----: |
| [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Contrôle si l’entrée/sortie synchrone est autorisée pour le `HttpContext.Request.Body` et le `HttpContext.Response.Body`. | `true` |
| [Authentication.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Autorise les requêtes anonymes. | `true` |
| [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | Spécifie les schémas d’authentification autorisés. Peut être modifié à tout moment avant la suppression de l’écouteur. Les valeurs sont fournies par l' [énumération AuthenticationSchemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic` , `Kerberos` ,, `Negotiate` `None` et `NTLM` . | `None` |
| [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Tente la mise en cache [en mode noyau](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) pour les réponses comportant un en-tête compatible. La réponse peut ne pas inclure d’en-tête `Set-Cookie`, `Vary` ou `Pragma`. Elle doit comporter un en-tête `Cache-Control``public` et soit une valeur `shared-max-age` ou `max-age`, soit un en-tête `Expires`. | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | Nombre maximal d'acceptations simultanées. | 5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | Nombre maximum de connexions simultanées à accepter. Utilisez `-1` pour signifier l’infini, et `null` pour utiliser le paramètre du Registre qui s’applique à l’ordinateur dans son ensemble. | `null`<br>(au niveau de l’ordinateur<br>défini |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | Consultez la section <a href="#maxrequestbodysize">MaxRequestBodySize</a>. | 30 000 000 octets<br>(env. 28,6 Mo) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | Nombre maximal de demandes pouvant être placées en file d'attente. | 1 000 |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Indique si les écritures dans le corps de la réponse qui échouent en raison d’une déconnexion du client doivent lever des exceptions ou se terminer normalement. | `false`<br>(se terminer normalement) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Expose la configuration <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> de HTTP.sys, également paramétrable dans le Registre. Suivez les liens de l’API pour en savoir plus sur chaque paramètre, y compris les valeurs par défaut :<ul><li>[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): temps accordé à l’API du serveur http pour vider le corps d’entité sur une connexion Keep-Alive.</li><li>[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): temps autorisé pour l’arrivée du corps d’entité de la requête.</li><li>[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): temps accordé à l’API du serveur http pour analyser l’en-tête de la demande.</li><li>[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): temps autorisé pour une connexion inactive.</li><li>[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): taux d’envoi minimal pour la réponse.</li><li>[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): temps pendant lequel la demande doit rester dans la file d’attente des demandes avant que l’application la récupère.</li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | Spécifiez <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> à inscrire auprès de HTTP.sys. La plus utile est [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), qui permet d’ajouter un préfixe à la collection. Ces choix peuvent être modifiés à tout moment avant la suppression de l’écouteur. |  |

<a name="maxrequestbodysize"></a>

**MaxRequestBodySize**

Taille maximale autorisée pour le corps d’une demande, en octets. Lorsque la valeur est `null`, elle est illimitée. Cette limite est sans effet sur les connexions mises à niveau, qui sont illimitées.

Pour remplacer la limite d’un seul `IActionResult` dans une application MVC ASP.NET Core, nous vous recommandons d’utiliser l’attribut <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> sur une méthode d’action :

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Une exception est levée si l’application tente de configurer la limite sur une demande dont l’application a commencé la lecture. La propriété `IsReadOnly` indique si la propriété `MaxRequestBodySize` est en lecture seule, et donc s’il est trop tard pour configurer la limite.

Si l’application doit remplacer <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> par requête, utilisez <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature> :

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

Si vous utilisez Visual Studio, vérifiez que l’application n’est pas configurée pour exécuter IIS ou IIS Express.

Dans Visual Studio, le profil de démarrage par défaut est destiné à IIS Express. Pour exécuter le projet en tant qu’application console, changez manuellement le profil sélectionné, comme dans la capture d’écran suivante :

![Sélectionner le profil d’application console](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a>Configurer Windows Server

1. Identifiez les ports à ouvrir pour l’application et utilisez le [pare-feu Windows](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) ou les applets de commande PowerShell [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) pour ouvrir les ports du pare-feu et ainsi permettre au trafic d’atteindre HTTP.sys. Dans les commandes et la configuration de l’application suivantes, le port 443 est utilisé.

1. Lors du déploiement sur une machine virtuelle Azure, ouvrez les ports dans le [groupe de sécurité réseau](/azure/virtual-machines/windows/nsg-quickstart-portal). Dans les commandes et la configuration de l’application suivantes, le port 443 est utilisé.

1. Obtenez et installez des certificats X.509, si nécessaire.

   Sous Windows, créez des certificats auto-signés à l’aide de la [cmdlet PowerShell New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate). Vous trouverez un exemple non pris en charge sur la page [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).

   Installez des certificats auto-signés ou signés par l’autorité de certification dans le magasin **Ordinateur Local** > **Personnel** du serveur.

1. Si l’application est un [déploiement dépendant de .NET Framework](/dotnet/core/deploying/#framework-dependent-deployments-fdd), installez .NET Core, .NET Framework ou les deux (si l’application est une application .NET Core ciblant .NET Framework).

   * **.Net Core**: si l’application requiert .net Core, obtenez et exécutez le programme d’installation du **Runtime .net Core** à partir des [téléchargements .net Core](https://dotnet.microsoft.com/download). N’installez pas le Kit SDK complet sur le serveur.
   * **.NET Framework**: si l’application requiert des .NET Framework, consultez le [Guide d’installation de .NET Framework](/dotnet/framework/install/). Installez la version requise de .NET Framework. Le programme d’installation du .NET Framework le plus récent est disponible depuis la page [Téléchargements .NET Core](https://dotnet.microsoft.com/download).

   Si l’application est un [déploiement autonome](/dotnet/core/deploying/#self-contained-deployments-scd), elle inclut le runtime dans son déploiement. Aucune installation de framework n’est requise sur le serveur.

1. Configurez les ports et les URL de l’application.

   Par défaut, ASP.NET Core est lié à `http://localhost:5000`. Pour configurer les ports et les préfixes d’URL, il existe plusieurs possibilités :

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * Arguments de ligne de commande `urls`
   * `ASPNETCORE_URLS` variable d’environnement
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   L’exemple de code suivant montre comment utiliser <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> avec l’adresse IP locale du serveur `10.0.0.4` sur le port 443 :

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   `UrlPrefixes` présente l’avantage de générer immédiatement un message d’erreur en cas de préfixe mal formé.

   Les paramètres de `UrlPrefixes` remplacent les paramètres `UseUrls`/`urls`/`ASPNETCORE_URLS`. Par conséquent, avec `UseUrls`, `urls` et la variable d’environnement `ASPNETCORE_URLS`, il est plus facile de basculer entre Kestrel et HTTP.sys.

   HTTP.sys utilise les [formats de chaîne UrlPrefix de l’API de serveur HTTP](/windows/win32/http/urlprefix-strings).

   > [!WARNING]
   > Les liaisons génériques de niveau supérieur (`http://*:80/` et `http://+:80`) ne doivent **pas** être utilisées. Les liaisons génériques de niveau supérieur créent des failles de sécurité dans l’application. Cela s’applique aux caractères génériques forts et faibles. Utilisez des noms d’hôte ou des adresses IP explicites plutôt que des caractères génériques. Une liaison générique de sous-domaine (par exemple, `*.mysub.com`) ne constitue pas un risque de sécurité si vous contrôlez le domaine parent en entier (par opposition à `*.com`, qui est vulnérable). Pour plus d’informations, consultez [RFC 7230 : Section 5,4 : Host](https://tools.ietf.org/html/rfc7230#section-5.4).

1. Préenregistrez les préfixes d’URL sur le serveur.

   L’outil intégré pour configurer HTTP.sys est *netsh.exe*. *netsh.exe* permet de réserver des préfixes d’URL et d’assigner des certificats X.509. L’outil requiert des privilèges Administrateur.

   Utilisez l’outil *netsh.exe* pour enregistrer les URL pour l’application :

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * `<URL>`: Le Uniform Resource Locator qualifié complet (URL). N’utilisez pas une liaison de caractère générique. Utilisez un nom d’hôte ou une adresse IP locale valide. *L’URL doit inclure une barre oblique de fin.*
   * `<USER>`: Spécifie le nom d’utilisateur ou de groupe d’utilisateurs.

   Dans l’exemple suivant, l’adresse IP locale du serveur est `10.0.0.4` :

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   Lorsqu’une URL est inscrite, l’outil répond avec `URL reservation successfully added`.

   Pour supprimer une URL inscrite, utilisez la commande `delete urlacl` :

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. Inscrivez les certificats X.509 sur le serveur.

   Utilisez l’outil *netsh.exe* pour enregistrer les certificats pour l’application :

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * `<IP>`: Spécifie l’adresse IP locale pour la liaison. N’utilisez pas une liaison de caractère générique. Utilisez une adresse IP valide.
   * `<PORT>`: Spécifie le port pour la liaison.
   * `<THUMBPRINT>`: Empreinte numérique du certificat X. 509.
   * `<GUID>`: GUID généré par le développeur pour représenter l’application à des fins d’information.

   À titre de référence, stockez le GUID dans l’application en tant que balise de package :

   * Dans Visual Studio :
     * Ouvrez les propriétés du projet de l’application en cliquant avec le bouton droit sur l’application dans **l’Explorateur de solutions** et en sélectionnant **Propriétés**.
     * Sélectionnez l’onglet **Package**.
     * Entrez le GUID que vous avez créé dans le champ **Balises**.
   * Si vous n’utilisez pas Visual Studio :
     * Ouvrez le chemin d’accès vers le fichier de projet de l’application.
     * Ajoutez une propriété `<PackageTags>` à un `<PropertyGroup>` nouveau ou existant avec le GUID que vous avez créé :

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   Dans l’exemple suivant :

   * L’adresse IP locale du serveur est `10.0.0.4`.
   * Un générateur de GUID aléatoire en ligne fournit la valeur `appid`.

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   Lorsqu’un certificat est inscrit, l’outil répond avec `SSL Certificate successfully added`.

   Pour supprimer l’inscription d’un certificat, utilisez la commande `delete sslcert` :

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   Documentation de référence de *netsh.exe* :

   * [Commandes netsh pour le protocole HTTP (Hypertext Transfer Protocol)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))
   * [Chaînes UrlPrefix](/windows/win32/http/urlprefix-strings)

1. Exécutez l'application.

   Les privilèges administrateur ne sont pas requis pour exécuter l’application lors d’une liaison à localhost à l’aide de HTTP (pas HTTPS) avec un numéro de port supérieur à 1024. Pour les autres configurations (par exemple, l’utilisation d’une adresse IP locale ou la liaison au port 443), exécutez l’application avec des privilèges administrateur.

   L’application répond à l’adresse IP publique du serveur. Dans cet exemple, le serveur est contacté à partir d’Internet à son adresse IP publique `104.214.79.47`.

   Un certificat de développement est utilisé dans cet exemple. La page est chargée en toute sécurité après le contournement de l’avertissement de certificat non approuvé du navigateur.

   ![Fenêtre de navigateur affichant la page Index de l’application chargée](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scénarios avec un serveur proxy et un équilibreur de charge

Pour les applications hébergées par HTTP.sys qui interagissent avec les demandes provenant d’Internet ou d’un réseau d’entreprise, une configuration supplémentaire peut être nécessaire en cas d’hébergement derrière des serveurs proxy et des équilibreurs de charge. Pour plus d’informations, consultez l’article [Configurer ASP.NET Core pour l’utilisation de serveurs proxy et d’équilibreurs de charge](xref:host-and-deploy/proxy-load-balancer).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Activer l’authentification Windows avec HTTP.sys](xref:security/authentication/windowsauth#httpsys)
* [API de serveur HTTP](/windows/win32/http/http-api-start-page)
* [Référentiel GitHub aspnet/HttpSysServer (code source)](https://github.com/aspnet/HttpSysServer/)
* [L’hôte](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end
