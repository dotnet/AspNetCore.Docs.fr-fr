---
title: Héberger ASP.NET Core sur Linux avec Apache
author: rick-anderson
description: Découvrez comment configurer Apache comme serveur proxy inverse sur CentOS pour rediriger le trafic HTTP vers une application web ASP.NET Core s’exécutant sur Kestrel.
monikerRange: '>= aspnetcore-2.1'
ms.author: shboyer
ms.custom: mvc
ms.date: 04/10/2020
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
uid: host-and-deploy/linux-apache
ms.openlocfilehash: 0bae3f888a1b7a3c2860b85754779189c636d86f
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057698"
---
# <a name="host-aspnet-core-on-linux-with-apache"></a>Héberger ASP.NET Core sur Linux avec Apache

Par [Shayne Boyer](https://github.com/spboyer)

À l’aide de ce guide, découvrez comment configurer [Apache](https://httpd.apache.org/) comme serveur proxy inverse sur [CentOS 7](https://www.centos.org/) pour rediriger le trafic HTTP vers une application web ASP.NET Core s’exécutant sur un serveur [Kestrel](xref:fundamentals/servers/kestrel). [L’extension mod_proxy](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html) et les modules associés créent le proxy inverse du serveur.

## <a name="prerequisites"></a>Prérequis

* Serveur exécutant CentOS 7 avec un compte d’utilisateur standard disposant du privilège sudo.
* Installez le runtime .NET Core sur le serveur.
   1. Visitez la [page Télécharger .net Core](https://dotnet.microsoft.com/download/dotnet-core).
   1. Sélectionnez la dernière version non préliminaire de .NET Core.
   1. Téléchargez le dernier Runtime non Preview dans le tableau sous **exécuter des applications-Runtime**.
   1. Sélectionnez le lien **des instructions du gestionnaire de package** Linux et suivez les instructions CentOS.
* Une application ASP.NET Core existante.

À tout moment après la mise à niveau de l’infrastructure partagée, redémarrez le ASP.NET Core les applications hébergées par le serveur.

## <a name="publish-and-copy-over-the-app"></a>Publier et copier sur l’application

Configurez l’application pour un [déploiement dépendant du framework](/dotnet/core/deploying/#framework-dependent-deployments-fdd).

Si l’application est exécutée localement et n’est pas configurée pour établir des connexions sécurisées (HTTPS), adoptez l’une des approches suivantes :

* Configurez l’application pour gérer les connexions locales sécurisées. Pour plus d’informations, consultez la section [Configuration HTTPS](#https-configuration).
* Supprimez `https://localhost:5001` (le cas échéant) de la propriété `applicationUrl` dans le fichier *Properties/launchSettings.json*.

Exécutez [dotnet publish](/dotnet/core/tools/dotnet-publish) à partir de l’environnement de développement pour empaqueter une application dans un répertoire (par exemple, *bin/Release/&lt;moniker_framework_target&gt;/publish*) exécutable sur le serveur :

```dotnetcli
dotnet publish --configuration Release
```

L’application peut également être publiée en tant que [déploiement autonome](/dotnet/core/deploying/#self-contained-deployments-scd) si vous préférez ne pas gérer le runtime .NET Core sur le serveur.

Copiez l’application ASP.NET Core sur le serveur à l’aide d’un outil qui s’intègre au flux de travail de l’organisation (par exemple, SCP ou SFTP). Il est courant de placer les applications web sous le répertoire *var* (par exemple, *var/www/helloapp*).

> [!NOTE]
> Dans un scénario de déploiement en production, un workflow d’intégration continue effectue le travail de publication de l’application et de copie des composants sur le serveur.

## <a name="configure-a-proxy-server"></a>Configurer un serveur proxy

Un proxy inverse est une configuration courante pour traiter les applications web dynamiques. Le proxy inverse met fin à la requête HTTP et la transfère à l’application ASP.NET.

Un serveur proxy est un serveur qui transfère les requêtes des clients à un autre serveur, au lieu de traiter celles-ci lui-même. Un proxy inverse transfère à une destination fixe, en général pour le compte de clients arbitraires. Dans ce guide, Apache est configuré comme proxy inverse s’exécutant sur le même serveur où Kestrel traite l’application ASP.NET Core.

Étant donné que les demandes sont transférées par le proxy inverse, utilisez l' [intergiciel d’en-têtes transmis](xref:host-and-deploy/proxy-load-balancer) à partir du package [Microsoft. AspNetCore. HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) . Le middleware met à jour le `Request.Scheme`, à l’aide de l’en-tête `X-Forwarded-Proto`, afin que les URI de redirection et d’autres stratégies de sécurité fonctionnent correctement.

Tout composant qui dépend du schéma, tel que l’authentification, la génération de lien, les redirections et la géolocalisation, doit être placé après l’appel du middleware des en-têtes transférés.

[!INCLUDE[](~/includes/ForwardedHeaders.md)]

Appelez la <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> méthode en haut de `Startup.Configure` avant d’appeler un autre intergiciel. Configurez le middleware pour transférer les en-têtes `X-Forwarded-For` et `X-Forwarded-Proto` :

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

Si aucune option <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> n’est spécifiée au middleware, les en-têtes par défaut à transférer sont `None`.

Les serveurs proxy exécutés sur les adresses de bouclage (127.0.0.0/8, [ :: 1]), notamment l’adresse localhost standard (127.0.0.1), sont approuvés par défaut. Si d’autres proxys ou réseaux approuvés au sein de l’organisation gèrent les requêtes entre Internet et le serveur web, ajoutez-les à la liste des <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> ou des <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> avec <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>. L’exemple suivant ajoute un serveur proxy approuvé avec l’adresse IP 10.0.0.100 au middleware des en-têtes transférés `KnownProxies` dans `Startup.ConfigureServices` :

```csharp
// using System.Net;

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

Pour plus d’informations, consultez <xref:host-and-deploy/proxy-load-balancer>.

### <a name="install-apache"></a>Installer Apache

Mettez à jour les packages CentOS vers leur dernière version stable :

```bash
sudo yum update -y
```

Installez le serveur web Apache sur CentOS avec une seule commande `yum` :

```bash
sudo yum -y install httpd mod_ssl
```

Exemple de sortie après exécution de la commande :

```bash
Downloading packages:
httpd-2.4.6-40.el7.centos.4.x86_64.rpm               | 2.7 MB  00:00:01
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
Installing : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 
Verifying  : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 

Installed:
httpd.x86_64 0:2.4.6-40.el7.centos.4

Complete!
```

> [!NOTE]
> Dans cet exemple, la sortie correspond à httpd.86_64, car la version de CentOS 7 est en 64 bits. Pour vérifier où Apache est installé, exécutez `whereis httpd` à partir d’une invite de commandes.

### <a name="configure-apache"></a>Configurer Apache

Les fichiers de configuration pour Apache se trouvent dans le répertoire `/etc/httpd/conf.d/`. Les fichiers avec l’extension *.conf* sont traités par ordre alphabétique en plus des fichiers de configuration des modules dans `/etc/httpd/conf.modules.d/`, qui contient tous les fichiers de configuration nécessaires au chargement des modules.

Créez un fichier de configuration nommé *helloapp.conf*, pour l’application :

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ServerName www.example.com
    ServerAlias *.example.com
    ErrorLog ${APACHE_LOG_DIR}helloapp-error.log
    CustomLog ${APACHE_LOG_DIR}helloapp-access.log common
</VirtualHost>
```

Le bloc `VirtualHost` peut apparaître plusieurs fois, dans un ou plusieurs fichiers sur un serveur. Dans le fichier de configuration précédent, Apache accepte le trafic public sur le port 80. Le domaine `www.example.com` est pris en charge, et l’alias `*.example.com` aboutit au même site web. Pour plus d’informations, consultez [Name-based virtual host support](https://httpd.apache.org/docs/current/vhosts/name-based.html) (Prise en charge des hôtes virtuels par nom). Les requêtes sont transmises par proxy à la racine vers le port 5000 du serveur sur 127.0.0.1. Pour la communication bidirectionnelle, `ProxyPass` et `ProxyPassReverse` sont requis. Pour changer le port/l’adresse IP Kestrel, consultez [Kestrel : configuration du point de terminaison](xref:fundamentals/servers/kestrel#endpoint-configuration).

> [!WARNING]
> La spécification d’une [directive ServerName](https://httpd.apache.org/docs/current/mod/core.html#servername) incorrecte dans le bloc **VirtualHost** expose votre application à des failles de sécurité. Une liaison générique de sous-domaine (par exemple, `*.example.com`) ne présente pas ce risque de sécurité si vous contrôlez le domaine parent en entier (par opposition à `*.com`, qui est vulnérable). Consultez la [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) pour plus d’informations.

Vous pouvez configurer la journalisation par `VirtualHost` à l’aide des directives `ErrorLog` et `CustomLog`. `ErrorLog` est l’emplacement où le serveur journalise les erreurs, tandis que `CustomLog` définit le nom de fichier et le format du fichier journal. Dans ce cas, c’est l’endroit où les informations des requêtes sont journalisées. Il y a une ligne pour chaque requête.

Enregistrez le fichier et testez la configuration. Si tout réussit, la réponse doit être `Syntax [OK]`.

```bash
sudo service httpd configtest
```

Redémarrez Apache :

```bash
sudo systemctl restart httpd
sudo systemctl enable httpd
```

## <a name="monitor-the-app"></a>Surveiller l’application

Apache est désormais configuré pour transférer les requêtes faites à `http://localhost:80` vers l’application ASP.NET Core s’exécutant sur Kestrel à l’adresse `http://127.0.0.1:5000`. Cependant, Apache n’est pas configuré pour gérer le processus Kestrel. Utilisez *systemd* pour créer un fichier de service afin de démarrer et surveiller l’application web sous-jacente. *systemd* est un système d’initialisation qui fournit de nombreuses et puissantes fonctionnalités pour le démarrage, l’arrêt et la gestion des processus.

### <a name="create-the-service-file"></a>Créer le fichier de service

Créez le fichier de définition de service :

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

Exemple de fichier de service pour l’application :

```
[Unit]
Description=Example .NET Web API App running on CentOS 7

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=apache
Environment=ASPNETCORE_ENVIRONMENT=Production 

[Install]
WantedBy=multi-user.target
```

Dans l’exemple précédent, l’utilisateur qui gère le service est spécifié par l' `User` option. L’utilisateur ( `apache` ) doit exister et avoir la propriété correcte des fichiers de l’application.

Utilisez `TimeoutStopSec` pour configurer la durée d’attente de l’arrêt de l’application après la réception du signal d’interruption initial. Si l’application ne s’arrête pas pendant cette période, le signal SIGKILL est émis pour mettre fin à l’application. Indiquez la valeur en secondes sans unité (par exemple, `150`), une valeur d’intervalle de temps (par exemple, `2min 30s`) ou `infinity` pour désactiver le délai d’attente. `TimeoutStopSec` prend la valeur par défaut de `DefaultTimeoutStopSec` dans le fichier de configuration du gestionnaire (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*). Le délai d’expiration par défaut pour la plupart des distributions est de 90 secondes.

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

Certaines valeurs (par exemple, les chaînes de connexion SQL) doivent être placées dans une séquence d’échappement afin que les fournisseurs de configuration puissent lire les variables d’environnement. Utilisez la commande suivante pour générer une valeur correctement placée dans une séquence d’échappement en vue de son utilisation dans le fichier de configuration :

```console
systemd-escape "<value-to-escape>"
```

::: moniker range=">= aspnetcore-3.0"

Les séparateurs `:` (signe deux-points) ne sont pas pris en charge dans les noms de variables d’environnement. Utilisez un double trait de soulignement (`__`) à la place d’un signe deux-points. Le [fournisseur de configuration de variables d’environnement](xref:fundamentals/configuration/index#environment-variables-configuration-provider) convertit les doubles traits de soulignement en signes deux-points quand les variables d’environnement sont lues dans la configuration. Dans l’exemple suivant, la clé de chaîne de connexion `ConnectionStrings:DefaultConnection` est définie dans le fichier de définition de service en tant que `ConnectionStrings__DefaultConnection` :

::: moniker-end
::: moniker range="< aspnetcore-3.0"

Les séparateurs `:` (signe deux-points) ne sont pas pris en charge dans les noms de variables d’environnement. Utilisez un double trait de soulignement (`__`) à la place d’un signe deux-points. Le [fournisseur de configuration de variables d’environnement](xref:fundamentals/configuration/index#environment-variables) convertit les doubles traits de soulignement en signes deux-points quand les variables d’environnement sont lues dans la configuration. Dans l’exemple suivant, la clé de chaîne de connexion `ConnectionStrings:DefaultConnection` est définie dans le fichier de définition de service en tant que `ConnectionStrings__DefaultConnection` :

::: moniker-end

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

Enregistrez le fichier et activez le service :

```bash
sudo systemctl enable kestrel-helloapp.service
```

Démarrez le service et vérifiez qu’il s’exécute :

```bash
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

◝ kestrel-helloapp.service - Example .NET Web API App running on CentOS 7
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

Le proxy inverse étant configuré et Kestrel étant géré via *systemd*, l’application web est maintenant entièrement configurée et accessible à partir d’un navigateur sur la machine locale à l’adresse `http://localhost`. Comme l’indiquent les en-têtes de réponse, l’en-tête **Server** montre que l’application ASP.NET Core est traitée par Kestrel :

```
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a>Afficher les journaux d’activité

Puisque l’application web utilisant Kestrel est gérée à l’aide de *systemd*, les processus et les événements sont enregistrés dans un journal centralisé. Cependant, ce journal comprend les entrées de tous les services et processus gérés par *systemd*. Pour afficher les éléments propres à `kestrel-helloapp.service`, utilisez la commande suivante :

```bash
sudo journalctl -fu kestrel-helloapp.service
```

Pour le filtrage du temps, spécifiez les options appropriées dans la commande. Par exemple, utilisez `--since today` pour filtrer en fonction du jour actuel ou `--until 1 hour ago` pour voir les entrées de l’heure précédente. Pour plus d’informations, consultez la [page man pour journalctl](https://www.unix.com/man-page/centos/1/journalctl/).

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

## <a name="secure-the-app"></a>Sécuriser l’application

### <a name="configure-firewall"></a>Configurer le pare-feu

*Firewalld* est un démon dynamique pour gérer le pare-feu avec prise en charge des zones réseau. Les ports et le filtrage des paquets peuvent toujours être gérés par iptables. *Firewalld* doit être installé par défaut. Vous pouvez utiliser `yum` pour installer le package ou vérifier qu’il est installé.

```bash
sudo yum install firewalld -y
```

Utilisez `firewalld` pour ouvrir seulement les ports nécessaires à l’application. Dans ce cas, les ports 80 et 443 sont utilisés. Les commandes suivantes définissent l’ouverture des ports 80 et 443 de façon permanente :

```bash
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --add-port=443/tcp --permanent
```

Rechargez les paramètres du pare-feu. Vérifiez les services et les ports disponibles dans la zone par défaut. Vous voyez les options disponibles en examinant le résultat de `firewall-cmd -h`.

```bash
sudo firewall-cmd --reload
sudo firewall-cmd --list-all
```

```bash
public (default, active)
interfaces: eth0
sources: 
services: dhcpv6-client
ports: 443/tcp 80/tcp
masquerade: no
forward-ports: 
icmp-blocks: 
rich rules: 
```

### <a name="https-configuration"></a>Configuration HTTPS

**Configurer l’application pour les connexions locales sécurisées (HTTPS)**

La commande [dotnet run](/dotnet/core/tools/dotnet-run) utilise le fichier *Properties/launchSettings.json* de l’application, qui configure l’application pour l’écoute sur les URL fournies par la propriété `applicationUrl` (par exemple `https://localhost:5001;http://localhost:5000`).

Configurez l’application pour utiliser un certificat en développement pour la commande `dotnet run` ou l’environnement de développement (F5 ou Ctrl+F5 dans Visual Studio Code) en adoptant l’une de ces approches :

* [Remplacer le certificat par défaut dans la configuration](xref:fundamentals/servers/kestrel#configuration) (*recommandé*)
* [KestrelServerOptions.ConfigureHttpsDefaults](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

**Configurer le proxy inverse pour les connexions clientes sécurisées (HTTPS)**

Pour configurer Apache pour HTTPS, vous utilisez le module *mod_ssl*. Le module *mod_ssl* a été installé parallèlement au module *httpd*. S’il n’a pas été installé, utilisez `yum` pour l’ajouter à la configuration.

```bash
sudo yum install mod_ssl
```

Pour mettre en œuvre HTTPS, installez le module `mod_rewrite` afin de permettre la réécriture d’URL :

```bash
sudo yum install mod_rewrite
```

Modifiez le fichier *helloapp.conf* pour permettre la réécriture d’URL et sécuriser les communications sur le port 443 :

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

> [!NOTE]
> Cet exemple utilise un certificat généré localement. **SSLCertificateFile** doit être le fichier de certificat principal pour le nom de domaine. **SSLCertificateKeyFile** doit être le fichier de clé généré quand la demande de signature de certificat est créée. **SSLCertificateChainFile** doit être le fichier de certificat intermédiaire (le cas échéant) qui a été fourni par l’autorité de certification.

Enregistrez le fichier et testez la configuration :

```bash
sudo service httpd configtest
```

Redémarrez Apache :

```bash
sudo systemctl restart httpd
```

## <a name="additional-apache-suggestions"></a>Autres suggestions pour Apache

### <a name="restart-apps-with-shared-framework-updates"></a>Redémarrer des applications avec des mises à jour de Framework partagé

Après la mise à niveau de l’infrastructure partagée sur le serveur, redémarrez le ASP.NET Core les applications hébergées par le serveur.

### <a name="additional-headers"></a>En-têtes supplémentaires

Afin d’assurer une protection contre les attaques malveillantes, vous devez modifier ou ajouter quelques en-têtes. Vérifiez que le module `mod_headers` est installé :

```bash
sudo yum install mod_headers
```

#### <a name="secure-apache-from-clickjacking-attacks"></a>Sécuriser Apache contre les attaques par détournement de clic

Le [détournement de clic](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), également appelé *UI redress attack*, est une attaque malveillante qui amène le visiteur d’un site web à cliquer sur un lien ou un bouton sur une page différente de celle qu’il est en train de visiter. Utilisez `X-FRAME-OPTIONS` pour sécuriser le site.

Pour atténuer les attaques par détournement de clic :

1. Modifiez le fichier *httpd.conf* :

   ```bash
   sudo nano /etc/httpd/conf/httpd.conf
   ```

   Ajoutez la ligne `Header append X-FRAME-OPTIONS "SAMEORIGIN"`.
1. Enregistrez le fichier .
1. Redémarrez Apache.

#### <a name="mime-type-sniffing"></a>Détection de type MIME

L’en-tête `X-Content-Type-Options` empêche Internet Explorer d’effectuer une *détection de données MIME* (détermination du `Content-Type` d’un fichier à partir de son contenu). Si le serveur définit l’en-tête `Content-Type` sur `text/html` et que l’option `nosniff` est définie, Internet Explorer restitue le contenu en tant que `text/html`, quel que soit le contenu du fichier.

Modifiez le fichier *httpd.conf* :

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

Ajoutez la ligne `Header set X-Content-Type-Options "nosniff"`. Enregistrez le fichier . Redémarrez Apache.

### <a name="load-balancing"></a>Équilibrage de la charge.

Cet exemple montre comment installer et configurer Apache sur CentOS 7 et Kestrel sur la même machine de l’instance. Afin de multiplier les instances en cas de défaillance, l’utilisation de *mod_proxy_balancer* et la modification du **VirtualHost** permettent de gérer plusieurs instances des applications web derrière le serveur proxy Apache.

```bash
sudo yum install mod_proxy_balancer
```

Dans le fichier de configuration illustré ci-dessous, une instance supplémentaire de `helloapp` est configurée pour s’exécuter sur le port 5001. La section *Proxy* est définie avec une configuration d’équilibreur qui comprend deux membres pour équilibrer la charge selon la méthode *byrequests*.

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPass / balancer://mycluster/ 

    ProxyPassReverse / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5001/

    <Proxy balancer://mycluster>
        BalancerMember http://127.0.0.1:5000
        BalancerMember http://127.0.0.1:5001 
        ProxySet lbmethod=byrequests
    </Proxy>

    <Location />
        SetHandler balancer
    </Location>
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

### <a name="rate-limits"></a>Limites du débit

Vous pouvez utiliser *mod_ratelimit*, qui est inclus dans le module *httpd*, pour limiter la bande passante des clients :

```bash
sudo nano /etc/httpd/conf.d/ratelimit.conf
```

L’exemple de fichier limite la bande passante à 600 Ko/s sous l’emplacement racine :

```
<IfModule mod_ratelimit.c>
    <Location />
        SetOutputFilter RATE_LIMIT
        SetEnv rate-limit 600
    </Location>
</IfModule>
```

### <a name="long-request-header-fields"></a>Longs champs d'en-tête de demande

Les paramètres par défaut du serveur proxy limitent généralement les champs d’en-tête de demande à 8 190 octets. Une application peut nécessiter des champs plus longs que la valeur par défaut (par exemple, les applications qui utilisent [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)). Si des champs plus longs sont requis, la directive [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) du serveur proxy doit être ajustée. La valeur à appliquer dépend du scénario. Pour plus d'informations, voir la documentation du serveur.

> [!WARNING]
> N’augmentez pas la valeur par défaut de `LimitRequestFieldSize` à moins que ce ne soit nécessaire. Son augmentation augmente le risque de dépassement de mémoire tampon (dépassement de capacité) et d’attaques par déni de service (DoS) par des utilisateurs malveillants.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Prérequis pour .NET Core sur Linux](/dotnet/core/linux-prerequisites)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
