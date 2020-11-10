---
title: Héberger ASP.NET Core sur Linux avec Nginx
author: rick-anderson
description: Découvrez comment configurer Nginx comme proxy inverse sur Ubuntu 16.04 pour transférer le trafic HTTP vers une application web ASP.NET Core s’exécutant sur Kestrel.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/30/2020
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
uid: host-and-deploy/linux-nginx
ms.openlocfilehash: c4e0d70b41221f272bb4b1fe82cfa531ec6fcf15
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431062"
---
# <a name="host-aspnet-core-on-linux-with-nginx"></a>Héberger ASP.NET Core sur Linux avec Nginx

De [Sourabh Shirhatti](https://twitter.com/sshirhatti)

Ce guide explique comment configurer un environnement ASP.NET Core prêt pour la production sur un serveur Ubuntu 16.04. Ces instructions fonctionnent normalement avec les versions d’Ubuntu plus récentes, mais elles n’ont pas fait l’objet de tests avec ces versions.

Pour plus d’informations sur les autres distributions Linux prises en charge par ASP.NET Core, consultez [Prérequis pour .NET Core sur Linux](/dotnet/core/linux-prerequisites).

> [!NOTE]
> Pour Ubuntu 14,04, `supervisord` est recommandé en tant que solution pour surveiller le processus Kestrel. `systemd` n’est pas disponible sur Ubuntu 14,04. Pour obtenir des instructions pour Ubuntu 14.04, consultez la [version précédente de cette rubrique](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md).

Ce guide montre comment effectuer les opérations suivantes :

* Placer une application ASP.NET Core existante derrière un serveur proxy inverse.
* Configurer le serveur proxy inverse pour transférer les requêtes au serveur web Kestrel.
* S’assurer que l’application web s’exécute au démarrage en tant que démon.
* Configurer un outil de gestion des processus pour aider à redémarrer l’application web.

## <a name="prerequisites"></a>Prérequis

1. Accédez à un serveur Ubuntu 16.04 avec un compte d’utilisateur standard disposant de privilèges sudo.
1. Installez le runtime .NET Core sur le serveur.
   1. Visitez la [page Télécharger .net Core](https://dotnet.microsoft.com/download/dotnet-core).
   1. Sélectionnez la dernière version non préliminaire de .NET Core.
   1. Téléchargez le dernier Runtime non Preview dans le tableau sous **exécuter des applications-Runtime**.
   1. Sélectionnez le lien **des instructions du gestionnaire de package** Linux et suivez les instructions Ubuntu pour votre version d’Ubuntu.
1. Une application ASP.NET Core existante.

À tout moment après la mise à niveau de l’infrastructure partagée, redémarrez le ASP.NET Core les applications hébergées par le serveur.

## <a name="publish-and-copy-over-the-app"></a>Publier et copier sur l’application

Configurez l’application pour un [déploiement dépendant du framework](/dotnet/core/deploying/#framework-dependent-deployments-fdd).

Si l’application est exécutée localement et n’est pas configurée pour établir des connexions sécurisées (HTTPS), adoptez l’une des approches suivantes :

* Configurez l’application pour gérer les connexions locales sécurisées. Pour plus d’informations, consultez la section [Configuration HTTPS](#https-configuration).
* Supprimez `https://localhost:5001` (le cas échéant) de la `applicationUrl` propriété dans le `Properties/launchSettings.json` fichier.

Exécutez [dotnet Publish](/dotnet/core/tools/dotnet-publish) à partir de l’environnement de développement pour empaqueter une application dans un répertoire (par exemple, `bin/Release/{TARGET FRAMEWORK MONIKER}/publish` , où l’espace réservé `{TARGET FRAMEWORK MONIKER}` est le moniker du Framework cible/TFM) qui peut s’exécuter sur le serveur :

```dotnetcli
dotnet publish --configuration Release
```

L’application peut également être publiée en tant que [déploiement autonome](/dotnet/core/deploying/#self-contained-deployments-scd) si vous préférez ne pas gérer le runtime .NET Core sur le serveur.

Copiez l’application ASP.NET Core sur le serveur à l’aide d’un outil qui s’intègre dans le flux de travail de l’organisation (par exemple, `SCP` `SFTP` ). Il est courant de rechercher des applications Web dans le `var` répertoire (par exemple, `var/www/helloapp` ).

> [!NOTE]
> Dans un scénario de déploiement en production, un workflow d’intégration continue effectue le travail de publication de l’application et de copie des composants sur le serveur.

Testez l’application :

1. À partir de la ligne de commande, exécutez l’application : `dotnet <app_assembly>.dll`.
1. Dans un navigateur, accédez à `http://<serveraddress>:<port>` pour vérifier que l’application fonctionne sur Linux localement.

## <a name="configure-a-reverse-proxy-server"></a>Configurer un serveur proxy inverse

Un proxy inverse est une configuration courante pour traiter les applications web dynamiques. Un proxy inverse met fin à la requête HTTP et la transfère à l’application ASP.NET Core.

### <a name="use-a-reverse-proxy-server"></a>Utiliser un serveur proxy inverse

Kestrel est idéal pour servir du contenu dynamique à partir d’ASP.NET Core. Toutefois, les fonctionnalités de service web ne sont pas aussi complètes que les serveurs tels qu’IIS, Apache ou Nginx. Un serveur proxy inverse peut décharger du travail tel que le traitement du contenu statique, la mise en cache des requêtes, la compression des requêtes et l’arrêt HTTPS à partir du serveur HTTP. Un serveur proxy inverse peut résider sur un ordinateur dédié ou peut être déployé à côté d’un serveur HTTP.

Pour les besoins de ce guide, nous utilisons une seule instance de Nginx. Elle s’exécute sur le même serveur, en plus du serveur HTTP. Selon les exigences, un paramétrage différent peut être choisi.

Étant donné que les demandes sont transférées par le proxy inverse, utilisez l' [intergiciel d’en-têtes transmis](xref:host-and-deploy/proxy-load-balancer) à partir du [`Microsoft.AspNetCore.HttpOverrides`](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides) Package. Le middleware met à jour le `Request.Scheme`, à l’aide de l’en-tête `X-Forwarded-Proto`, afin que les URI de redirection et d’autres stratégies de sécurité fonctionnent correctement.

[!INCLUDE[](~/includes/ForwardedHeaders.md)]

Appelez la <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> méthode en haut de `Startup.Configure` avant d’appeler un autre intergiciel. Configurez le middleware pour transférer les en-têtes `X-Forwarded-For` et `X-Forwarded-Proto` :

```csharp
using Microsoft.AspNetCore.HttpOverrides;

...

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

Si aucune option <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> n’est spécifiée au middleware, les en-têtes par défaut à transférer sont `None`.

Les proxies s’exécutant sur des adresses de bouclage ( `127.0.0.0/8` , `[::1]` ), y compris l’adresse localhost standard ( `127.0.0.1` ), sont approuvés par défaut. Si d’autres proxys ou réseaux approuvés au sein de l’organisation gèrent les requêtes entre Internet et le serveur web, ajoutez-les à la liste des <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> ou des <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> avec <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>. L’exemple suivant ajoute un serveur proxy approuvé avec l’adresse IP 10.0.0.100 au middleware des en-têtes transférés `KnownProxies` dans `Startup.ConfigureServices` :

```csharp
using System.Net;

...

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

Pour plus d'informations, consultez <xref:host-and-deploy/proxy-load-balancer>.

### <a name="install-nginx"></a>Installer Nginx

Utilisez `apt-get` pour installer Nginx. Le programme d’installation crée un `systemd` script init qui exécute Nginx en tant que démon au démarrage du système. Suivez les instructions d’installation pour Ubuntu sur le site [Nginx : Les packages officiels Debian/Ubuntu](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages).

> [!NOTE]
> Si des modules Nginx facultatifs sont requis, il peut s’avérer nécessaire de configurer Nginx à partir de la source.

Comme il s’agit de l’installation initiale de Nginx, vous devez le démarrer explicitement en exécutant :

```bash
sudo service nginx start
```

Vérifiez qu’un navigateur affiche la page d’accueil par défaut de Nginx. La page d’accueil est accessible à l’adresse `http://<server_IP_address>/index.nginx-debian.html`.

### <a name="configure-nginx"></a>Configurer Nginx

Pour configurer Nginx en tant que proxy inverse pour transférer les requêtes HTTP à votre application ASP.NET Core, modifiez `/etc/nginx/sites-available/default` . Ouvrez-le dans un éditeur de texte et remplacez le contenu par ce qui suit :

```nginx
server {
    listen        80;
    server_name   example.com *.example.com;
    location / {
        proxy_pass         http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header   Upgrade $http_upgrade;
        proxy_set_header   Connection keep-alive;
        proxy_set_header   Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header   X-Forwarded-Proto $scheme;
    }
}
```

Si l’application est une SignalR Blazor Server application ou <xref:signalr/scale#linux-with-nginx> , consultez et, <xref:blazor/host-and-deploy/server#linux-with-nginx> respectivement, pour plus d’informations.

Si aucun `server_name` ne correspond, Nginx utilise le serveur par défaut. Si aucun serveur par défaut n’est défini, le premier serveur dans le fichier de configuration est le serveur par défaut. En guise de bonne pratique, ajoutez un serveur par défaut spécifique qui retourne un code d’état 444 dans votre fichier de configuration. Voici un exemple de configuration de serveur par défaut :

```nginx
server {
    listen   80 default_server;
    # listen [::]:80 default_server deferred;
    return   444;
}
```

Avec les fichier de configuration et le serveur par défaut précédents, Nginx accepte le trafic public sur le port 80 avec un en-tête d’hôte `example.com` ou `*.example.com`. Les requêtes qui ne correspondent pas à ces hôtes ne sont pas transférées à Kestrel. Nginx transfère les requêtes correspondantes à Kestrel à l’adresse `http://localhost:5000`. Consultez [How nginx processes a request](https://nginx.org/docs/http/request_processing.html) (Comment nginx traite une requête) pour plus d’informations. Pour changer le port/l’adresse IP Kestrel, consultez [Kestrel : configuration du point de terminaison](xref:fundamentals/servers/kestrel#endpoint-configuration).

> [!WARNING]
> La spécification d’une [directive server_name](https://nginx.org/docs/http/server_names.html) incorrecte expose votre application à des failles de sécurité. Une liaison générique de sous-domaine (par exemple, `*.example.com`) ne présente pas ce risque de sécurité si vous contrôlez le domaine parent en entier (par opposition à `*.com`, qui est vulnérable). Consultez la [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) pour plus d’informations.

Une fois la configuration de Nginx établie, exécutez `sudo nginx -t` pour vérifier la syntaxe des fichiers de configuration. Si le test de fichier de configuration réussit, forcez Nginx à appliquer les modifications en exécutant `sudo nginx -s reload`.

Pour exécuter directement l’application sur le serveur :

1. Accédez au répertoire de l’application.
1. Exécutez l’application : `dotnet <app_assembly.dll>`, où `app_assembly.dll` est le nom de fichier d’assembly de l’application.

Si l’application s’exécute sur le serveur, mais ne répond pas sur Internet, vérifiez que le port 80 est ouvert sur le pare-feu du serveur. Si vous utilisez une machine virtuelle Azure Ubuntu, ajoutez une règle de groupe de sécurité réseau (NSG) qui autorise le trafic entrant sur le port 80. Il est inutile d’activer une règle de trafic sortant sur le port 80, car le trafic sortant est accordé automatiquement quand la règle de trafic entrant est activée.

Quand vous avez terminé de tester l’application, arrêtez-la avec `Ctrl+C` depuis l’invite de commandes.

## <a name="monitor-the-app"></a>Surveiller l’application

Le serveur est configuré pour transférer les requêtes faites à `http://<serveraddress>:80` à l’application ASP.NET Core s’exécutant sur Kestrel à l’adresse `http://127.0.0.1:5000`. Toutefois, Nginx n’est pas configuré pour gérer le processus Kestrel. `systemd` peut être utilisé pour créer un fichier de service pour démarrer et surveiller l’application Web sous-jacente. `systemd` est un système init qui fournit de nombreuses fonctionnalités puissantes pour le démarrage, l’arrêt et la gestion des processus. 

### <a name="create-the-service-file"></a>Créer le fichier de service

Créez le fichier de définition de service :

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

Voici un exemple de fichier de service pour l’application :

```ini
[Unit]
Description=Example .NET Web API App running on Ubuntu

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=www-data
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false

[Install]
WantedBy=multi-user.target
```

Dans l’exemple précédent, l’utilisateur qui gère le service est spécifié par l' `User` option. L’utilisateur ( `www-data` ) doit exister et avoir la propriété correcte des fichiers de l’application.

Utilisez `TimeoutStopSec` pour configurer la durée d’attente de l’arrêt de l’application après la réception du signal d’interruption initial. Si l’application ne s’arrête pas pendant cette période, le signal SIGKILL est émis pour mettre fin à l’application. Indiquez la valeur en secondes sans unité (par exemple, `150`), une valeur d’intervalle de temps (par exemple, `2min 30s`) ou `infinity` pour désactiver le délai d’attente. `TimeoutStopSec` la valeur par défaut est la valeur de `DefaultTimeoutStopSec` dans le fichier de configuration du gestionnaire ( `systemd-system.conf` , `system.conf.d` , `systemd-user.conf` , `user.conf.d` ). Le délai d’expiration par défaut pour la plupart des distributions est de 90 secondes.

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

Linux possède un système de fichiers respectant la casse. Si `ASPNETCORE_ENVIRONMENT` la valeur est `Production` , la recherche du fichier de configuration `appsettings.Production.json` n’a pas lieu `appsettings.production.json` .

Certaines valeurs (par exemple, les chaînes de connexion SQL) doivent être placées dans une séquence d’échappement afin que les fournisseurs de configuration puissent lire les variables d’environnement. Utilisez la commande suivante pour générer une valeur correctement placée dans une séquence d’échappement en vue de son utilisation dans le fichier de configuration :

```console
systemd-escape "<value-to-escape>"
```

::: moniker range=">= aspnetcore-3.0"

Les séparateurs `:` (signe deux-points) ne sont pas pris en charge dans les noms de variables d’environnement. Utilisez un double trait de soulignement (`__`) à la place d’un signe deux-points. Le [fournisseur de configuration de variables d’environnement](xref:fundamentals/configuration/index#environment-variables) convertit les doubles traits de soulignement en signes deux-points quand les variables d’environnement sont lues dans la configuration. Dans l’exemple suivant, la clé de chaîne de connexion `ConnectionStrings:DefaultConnection` est définie dans le fichier de définition de service en tant que `ConnectionStrings__DefaultConnection` :

::: moniker-end
::: moniker range="< aspnetcore-3.0"

Les séparateurs `:` (signe deux-points) ne sont pas pris en charge dans les noms de variables d’environnement. Utilisez un double trait de soulignement (`__`) à la place d’un signe deux-points. Le [fournisseur de configuration de variables d’environnement](xref:fundamentals/configuration/index#environment-variables-configuration-provider) convertit les doubles traits de soulignement en signes deux-points quand les variables d’environnement sont lues dans la configuration. Dans l’exemple suivant, la clé de chaîne de connexion `ConnectionStrings:DefaultConnection` est définie dans le fichier de définition de service en tant que `ConnectionStrings__DefaultConnection` :

::: moniker-end

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

Enregistrez le fichier et activez le service.

```bash
sudo systemctl enable kestrel-helloapp.service
```

Démarrez le service et vérifiez qu’il s’exécute.

```
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

◝ kestrel-helloapp.service - Example .NET Web API App running on Ubuntu
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

Avec le proxy inverse configuré et Kestrel géré via `systemd` , l’application Web est entièrement configurée et accessible à partir d’un navigateur sur l’ordinateur local à l’adresse `http://localhost` . Elle est également accessible à partir d’un ordinateur distant, sauf en cas de blocage par un pare-feu. Si l’on inspecte les en-têtes de réponse, on constate que l’en-tête `Server` indique que l’application ASP.NET Core est traitée par Kestrel.

```text
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a>Afficher les journaux d’activité

Puisque l’application web utilisant Kestrel est gérée à l’aide de `systemd`, tous les processus et les événements sont enregistrés dans un journal centralisé. Toutefois, ce journal inclut toutes les entrées pour tous les services et les processus gérés par `systemd`. Pour afficher les éléments propres à `kestrel-helloapp.service`, utilisez la commande suivante :

```bash
sudo journalctl -fu kestrel-helloapp.service
```

Si vous voulez appliquer un filtrage supplémentaire, des options chronologiques, comme `--since today`, `--until 1 hour ago` ou une combinaison de ces options, peuvent réduire la quantité d’entrées retournées.

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a>Protection de données

La [pile de protection des données ASP.net Core](xref:security/data-protection/introduction) est utilisée par plusieurs ASP.net Core [intergiciels](xref:fundamentals/middleware/index), y compris l’intergiciel (middleware) d’authentification (par exemple, cookie intergiciel) et les protections CSRF (cross-site request falsification). Même si les API de protection des données ne sont pas appelées par le code de l’utilisateur, la protection des données doit être configurée pour créer un [magasin de clés](xref:security/data-protection/implementation/key-management) de chiffrement persistantes. Si la protection des données n’est pas configurée, les clés sont conservées en mémoire et ignorées au redémarrage de l’application.

Si le Key Ring est stocké en mémoire, au redémarrage de l’application :

* Tous les cookie jetons d’authentification de base sont invalidés.
* Les utilisateurs doivent se reconnecter pour envoyer leur prochaine demande.
* toutes les données protégées par le Key Ring ne peuvent plus être déchiffrées. Cela peut inclure des [jetons CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) et des ASP.NET Core les de la [MVC cookie ](xref:fundamentals/app-state#tempdata).

Pour configurer la protection des données de façon à conserver et chiffrer le porte-clés (Key Ring), consultez :

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="long-request-header-fields"></a>Longs champs d'en-tête de demande

Les paramètres par défaut du serveur proxy limitent généralement les champs d’en-tête de demande à 4 K ou 8 K en fonction de la plateforme. Une application peut nécessiter des champs plus longs que la valeur par défaut (par exemple, les applications qui utilisent [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)). Si des champs plus longs sont requis, les paramètres par défaut du serveur proxy doivent être ajustés. Les valeurs à appliquer dépendent du scénario. Pour plus d'informations, voir la documentation du serveur.

* [proxy_buffer_size](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffer_size)
* [proxy_buffers](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffers)
* [proxy_busy_buffers_size](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_busy_buffers_size)
* [large_client_header_buffers](https://nginx.org/docs/http/ngx_http_core_module.html#large_client_header_buffers)

> [!WARNING]
> N’augmentez pas les valeurs par défaut des mémoires tampons de proxy à moins que ce ne soit nécessaire. Leur augmentation augmente le risque de dépassement de mémoire tampon (dépassement de capacité) et d’attaques par déni de service (DoS) par des utilisateurs malveillants.

## <a name="secure-the-app"></a>Sécuriser l’application

### <a name="enable-apparmor"></a>Activer AppArmor

Linux Security Modules (LSM) est un framework qui fait partie du noyau Linux depuis Linux 2.6. LSM prend en charge différentes implémentations de modules de sécurité. [AppArmor](https://wiki.ubuntu.com/AppArmor) est un LSM qui implémente un système de contrôle d’accès obligatoire permettant de confiner le programme à un ensemble limité de ressources. Vérifiez qu’AppArmor est activé et configuré correctement.

### <a name="configure-the-firewall"></a>Configurer le pare-feu

Fermez tous les ports externes qui ne sont pas en cours d’utilisation. Le pare-feu UFW fournit une interface de commande pour `iptables` la configuration du pare-feu.

> [!WARNING]
> Un pare-feu mal configuré bloque l’accès à l’ensemble du système. Faute d’avoir spécifié le port SSH approprié, vous ne pourrez pas accéder au système si vous utilisez SSH pour vous y connecter. Le numéro de port par défaut est 22. Pour plus d’informations, consultez la [présentation d’ufw](https://help.ubuntu.com/community/UFW) et le [manuel](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).

Installez `ufw` et configurez-le de façon à autoriser le trafic sur les ports nécessaires.

```bash
sudo apt-get install ufw

sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

sudo ufw enable
```

### <a name="secure-nginx"></a>Sécuriser Nginx

#### <a name="change-the-nginx-response-name"></a>Changer le nom de la réponse Nginx

Modifiez `src/http/ngx_http_header_filter_module.c`:

```
static char ngx_http_server_string[] = "Server: Web Server" CRLF;
static char ngx_http_server_full_string[] = "Server: Web Server" CRLF;
```

#### <a name="configure-options"></a>Configurer les options

Configurez le serveur avec les modules nécessaires supplémentaires. Pour renforcer l’application, vous pouvez utiliser un pare-feu d’application web tel que [ModSecurity](https://www.modsecurity.org/).

#### <a name="https-configuration"></a>Configuration HTTPS

**Configurer l’application pour les connexions locales sécurisées (HTTPS)**

La commande [dotnet Run](/dotnet/core/tools/dotnet-run) utilise le fichier de l’application `Properties/launchSettings.json` , qui configure l’application pour écouter les URL fournies par la `applicationUrl` propriété (par exemple, `https://localhost:5001;http://localhost:5000` ).

Configurez l’application pour qu’elle utilise un certificat en développement pour la `dotnet run` commande ou l’environnement de développement (<kbd>F5</kbd> ou <kbd>CTRL</kbd> + <kbd>F5</kbd> dans Visual Studio code) à l’aide de l’une des approches suivantes :

* [Remplacer le certificat par défaut dans la configuration](xref:fundamentals/servers/kestrel#configuration) ( *recommandé* )
* [KestrelServerOptions.ConfigureHttpsDefaults](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

**Configurer le proxy inverse pour les connexions clientes sécurisées (HTTPS)**

* Configurez le serveur pour qu’il écoute le trafic HTTPS sur le port `443` en spécifiant un certificat valide émis par une autorité de certificat approuvée.

* Renforcez la sécurité en utilisant certaines des pratiques décrites dans le `/etc/nginx/nginx.conf` fichier suivant. Vous pouvez par exemple choisir un chiffrement plus fort et rediriger tout le trafic sur HTTP vers HTTPS.

  > [!NOTE]
  > Pour les environnements de développement, nous vous recommandons d’utiliser des redirections temporaires (302) au lieu de redirections permanentes (301). La mise en cache des liens peut provoquer un comportement instable dans les environnements de développement.

* L’ajout d’un en-tête `HTTP Strict-Transport-Security` (HSTS) garantit que toutes les demandes ultérieures du client s’effectuent par le biais du protocole HTTPS.

  Pour obtenir des instructions importantes sur HSTS, consultez <xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts> .

* Si le protocole HTTPs est désactivé à l’avenir, utilisez l’une des approches suivantes :

  * N’ajoutez pas l’en-tête HSTS.
  * Choisissez une valeur abrégée `max-age` .

Ajoutez le `/etc/nginx/proxy.conf` fichier de configuration :

[!code-nginx[](linux-nginx/proxy.conf)]

**Remplacez** le contenu du `/etc/nginx/nginx.conf` fichier de configuration par le fichier suivant. Dans l’exemple, les sections `http` et `server` figurent dans un même fichier de configuration.

[!code-nginx[](linux-nginx/nginx.conf?highlight=2)]

> [!NOTE]
> Blazor WebAssembly les applications requièrent une plus grande `burst` valeur de paramètre pour prendre en charge le plus grand nombre de requêtes effectuées par une application. Pour plus d'informations, consultez <xref:blazor/host-and-deploy/webassembly#nginx>.

#### <a name="secure-nginx-from-clickjacking"></a>Sécuriser Nginx contre le détournement de clic

Le [détournement de clic](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), également appelé *UI redress attack* , est une attaque malveillante qui amène le visiteur d’un site web à cliquer sur un lien ou un bouton sur une page différente de celle qu’il est en train de visiter. Utilisez `X-FRAME-OPTIONS` pour sécuriser le site.

Pour atténuer les attaques par détournement de clic :

1. Modifiez le `nginx.conf` fichier :

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   Ajoutez la ligne : `add_header X-Frame-Options "SAMEORIGIN";`

1. Enregistrez le fichier.
1. Redémarrez Nginx.

#### <a name="mime-type-sniffing"></a>Détection de type MIME

Cet en-tête empêche la plupart des navigateurs de détourner le type MIME d’une réponse et de remplacer le type de contenu déclaré, car l’en-tête indique au navigateur qu’il ne doit pas substituer le type de contenu de la réponse. Avec l' `nosniff` option, si le serveur indique que le contenu est `text/html` , le navigateur le restitue sous la forme `text/html` .

1. Modifiez le `nginx.conf` fichier :

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   Ajoutez la ligne : `add_header X-Content-Type-Options "nosniff";`

1. Enregistrez le fichier.
1. Redémarrez Nginx.

## <a name="additional-nginx-suggestions"></a>Suggestions supplémentaires pour Nginx

Après la mise à niveau de l’infrastructure partagée sur le serveur, redémarrez le ASP.NET Core les applications hébergées par le serveur.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Prérequis pour .NET Core sur Linux](/dotnet/core/linux-prerequisites)
* [Nginx: Binary Releases: Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages) (Nginx : versions binaires : packages Debian/Ubuntu officiels).
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
* [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/) (NGINX : utilisation de l’en-tête Forwarded)
