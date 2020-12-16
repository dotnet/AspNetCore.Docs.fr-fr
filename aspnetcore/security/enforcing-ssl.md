---
title: Appliquer le protocole HTTPs en ASP.NET Core
author: rick-anderson
description: Découvrez comment exiger le protocole HTTPs/TLS dans une application Web ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2019
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
uid: security/enforcing-ssl
ms.openlocfilehash: 3277fda0d1dcb5121a2172b3fc1e4869ed6f8430
ms.sourcegitcommit: fc4cce2767e34f81079510f34bd54e9d0aa86497
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592867"
---
# <a name="enforce-https-in-aspnet-core"></a>Appliquer le protocole HTTPs en ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

Ce document montre comment :

* Exiger le protocole HTTPs pour toutes les demandes.
* Redirige toutes les requêtes HTTP vers HTTPS.

Aucune API ne peut empêcher un client d’envoyer des données sensibles à la première demande.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> ## <a name="api-projects"></a>Projets d’API
>
> N'  utilisez pas [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) sur les API Web qui reçoivent des informations sensibles. `RequireHttpsAttribute` utilise des codes d’état HTTP pour rediriger les navigateurs de HTTP vers HTTPs. Les clients d’API peuvent ne pas comprendre ou respecter les redirections du protocole HTTP vers HTTPs. Ces clients peuvent envoyer des informations via HTTP. Les API Web doivent être les suivantes :
>
> * Ne pas écouter sur HTTP.
> * Fermez la connexion avec le code d’état 400 (requête incorrecte) et ne traitez pas la requête.
>
> ## <a name="hsts-and-api-projects"></a>Projets HSTS et API
>
> Les projets d’API par défaut n’incluent pas [HSTS](#hsts) , car HSTS est généralement une instruction de navigateur uniquement. Les autres appelants, tels que les applications de bureau ou de téléphone, ne respectent **pas** l’instruction. Même dans les navigateurs, un seul appel authentifié à une API sur HTTP présente des risques sur les réseaux non sécurisés. L’approche sécurisée consiste à configurer des projets d’API pour écouter et répondre uniquement au protocole HTTPs.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

> [!WARNING]
> ## <a name="api-projects"></a>Projets d’API
>
> N'  utilisez pas [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) sur les API Web qui reçoivent des informations sensibles. `RequireHttpsAttribute` utilise des codes d’état HTTP pour rediriger les navigateurs de HTTP vers HTTPs. Les clients d’API peuvent ne pas comprendre ou respecter les redirections du protocole HTTP vers HTTPs. Ces clients peuvent envoyer des informations via HTTP. Les API Web doivent être les suivantes :
>
> * Ne pas écouter sur HTTP.
> * Fermez la connexion avec le code d’état 400 (requête incorrecte) et ne traitez pas la requête.

::: moniker-end

## <a name="require-https"></a>Exiger HTTPS

Nous vous recommandons d’utiliser les applications Web de production ASP.NET Core :

* Intergiciel (middleware) de redirection HTTPs ( <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> ) pour rediriger les requêtes http vers HTTPS.
* Intergiciel (middleware) HSTS ([UseHsts](#http-strict-transport-security-protocol-hsts)) pour envoyer des en-têtes HSTS (protocole de sécurité de transport strict) aux clients.

> [!NOTE]
> Les applications déployées dans une configuration de proxy inverse autorisent le proxy à gérer la sécurité de la connexion (HTTPs). Si le proxy gère également la redirection HTTPs, il n’est pas nécessaire d’utiliser l’intergiciel (middleware) de redirection HTTPs. Si le serveur proxy gère également l’écriture d’en-têtes HSTS (par exemple, la [prise en charge native de HSTS dans IIS 10,0 (1709) ou version ultérieure](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)), l’application ne nécessite pas d’intergiciel HSTS. Pour plus d’informations, consultez [opt-out de https/HSTS lors de la création du projet](#opt-out-of-httpshsts-on-project-creation).

### <a name="usehttpsredirection"></a>UseHttpsRedirection

Le code suivant appelle `UseHttpsRedirection` dans la `Startup` classe :

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=13)]

::: moniker-end

Code mis en surbrillance précédent :

* Utilise la valeur par défaut [HttpsRedirectionOptions. RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).
* Utilise la valeur par défaut [HttpsRedirectionOptions. HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null), sauf si elle est remplacée par la `ASPNETCORE_HTTPS_PORT` variable d’environnement ou [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature).

Nous vous recommandons d’utiliser des redirections temporaires plutôt que des redirections permanentes. La mise en cache des liens peut provoquer un comportement instable dans les environnements de développement. Si vous préférez envoyer un code d’état de redirection permanent lorsque l’application se trouve dans un environnement de non-développement, consultez la section [configurer des redirections permanentes en production](#configure-permanent-redirects-in-production) . Nous vous recommandons d’utiliser [HSTS](#http-strict-transport-security-protocol-hsts) pour signaler aux clients que seules les demandes de ressources sécurisées doivent être envoyées à l’application (uniquement en production).

### <a name="port-configuration"></a>Configuration du port

Un port doit être disponible pour que l’intergiciel redirige une requête non sécurisée vers HTTPs. Si aucun port n’est disponible :

* La redirection vers HTTPs ne se produit pas.
* L’intergiciel enregistre l’avertissement « échec de la détermination du port HTTPS pour la redirection ».

Spécifiez le port HTTPs à l’aide de l’une des approches suivantes :

* Définissez [HttpsRedirectionOptions. HttpsPort](#options).

::: moniker range=">= aspnetcore-3.0"

* Définissez le `https_port` [paramètre d’ordinateur hôte](../fundamentals/host/generic-host.md?view=aspnetcore-3.0#https_port):

  * Dans la configuration de l’hôte.
  * En définissant la `ASPNETCORE_HTTPS_PORT` variable d’environnement.
  * En ajoutant une entrée de niveau supérieur dans *appsettings.json* :

    [!code-json[](enforcing-ssl/sample-snapshot/3.x/appsettings.json?highlight=2)]

* Indiquez un port avec le schéma sécurisé à l’aide de la [variable d’environnement ASPNETCORE_URLS](../fundamentals/host/generic-host.md?view=aspnetcore-3.0#urls). La variable d’environnement configure le serveur. L’intergiciel Découvre indirectement le port HTTPs via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> . Cette approche ne fonctionne pas dans les déploiements de proxy inverse.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

* Définissez le `https_port` [paramètre d’ordinateur hôte](xref:fundamentals/host/web-host#https-port):

  * Dans la configuration de l’hôte.
  * En définissant la `ASPNETCORE_HTTPS_PORT` variable d’environnement.
  * En ajoutant une entrée de niveau supérieur dans *appsettings.json* :

    [!code-json[](enforcing-ssl/sample-snapshot/2.x/appsettings.json?highlight=2)]

* Indiquez un port avec le schéma sécurisé à l’aide de la [variable d’environnement ASPNETCORE_URLS](xref:fundamentals/host/web-host#server-urls). La variable d’environnement configure le serveur. L’intergiciel Découvre indirectement le port HTTPs via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> . Cette approche ne fonctionne pas dans les déploiements de proxy inverse.

::: moniker-end

* Dans développement, définissez une URL HTTPs dans *launchsettings.js*. Activez le protocole HTTPs lorsque IIS Express est utilisé.

* Configurez un point de terminaison d’URL HTTPs pour un déploiement de périphérie public d’un serveur [Kestrel](xref:fundamentals/servers/kestrel) ou d’un serveur [HTTP.sys](xref:fundamentals/servers/httpsys) . **Un seul port HTTPS** est utilisé par l’application. L’intergiciel Découvre le port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> .

> [!NOTE]
> Lorsqu’une application est exécutée dans une configuration de proxy inverse, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> n’est pas disponible. Définissez le port à l’aide de l’une des autres approches décrites dans cette section.

### <a name="edge-deployments"></a>Déploiements de périphérie 

Quand Kestrel ou HTTP.sys est utilisé en tant que serveur Edge public, Kestrel ou HTTP.sys doit être configuré pour écouter les deux :

* Le port sécurisé où le client est redirigé (en général, 443 en production et 5001 en développement).
* Le port non sécurisé (en général, 80 en production et 5000 en développement).

Le port non sécurisé doit être accessible par le client afin que l’application puisse recevoir une demande non sécurisée et rediriger le client vers le port sécurisé.

Pour plus d’informations, consultez [configuration du point de terminaison Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) ou <xref:fundamentals/servers/httpsys> .

### <a name="deployment-scenarios"></a>Scénarios de déploiement

Tout pare-feu entre le client et le serveur doit également disposer de ports de communication ouverts pour le trafic.

Si les demandes sont transférées dans une configuration de proxy inverse, utilisez l' [intergiciel d’en-tête transféré](xref:host-and-deploy/proxy-load-balancer) avant d’appeler l’intergiciel (middleware) de REdirection HTTPS. L’intergiciel d’en-têtes transférés met à jour le `Request.Scheme` , à l’aide de l' `X-Forwarded-Proto` en-tête. L’intergiciel permet de rediriger les URI et d’autres stratégies de sécurité pour fonctionner correctement. Lorsque l’intergiciel d’en-têtes transférés n’est pas utilisé, l’application principale peut ne pas recevoir le schéma correct et se terminer dans une boucle de redirection. Un message d’erreur de l’utilisateur final commun est qu’un trop grand nombre de redirections se sont produites.

Lors du déploiement sur Azure App Service, suivez les instructions du [Didacticiel : lier un certificat SSL personnalisé existant à Azure Web Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).

### <a name="options"></a>Options

Le code en surbrillance suivant appelle [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) pour configurer les options de l’intergiciel :


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end


`AddHttpsRedirection`L’appel de est nécessaire uniquement pour modifier les valeurs de `HttpsPort` ou `RedirectStatusCode` .

Code mis en surbrillance précédent :

* Définit [HttpsRedirectionOptions. RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) sur <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect> , qui est la valeur par défaut. Utilisez les champs de la <xref:Microsoft.AspNetCore.Http.StatusCodes> classe pour les assignations à `RedirectStatusCode` .
* Définit le port HTTPs sur 5001.

#### <a name="configure-permanent-redirects-in-production"></a>Configurer des redirections permanentes en production

Par défaut, l’intergiciel envoie un [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) avec toutes les redirections. Si vous préférez envoyer un code d’état de redirection permanent lorsque l’application se trouve dans un environnement de non-développement, encapsulez la configuration des options d’intergiciel dans une vérification conditionnelle pour un environnement de non-développement.

::: moniker range=">= aspnetcore-3.0"

Lors de la configuration des services dans *Startup.cs*:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IWebHostEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Lors de la configuration des services dans *Startup.cs*:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IHostingEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

::: moniker-end


## <a name="https-redirection-middleware-alternative-approach"></a>Approche alternative de l’intergiciel (middleware) de redirection HTTPs

Une alternative à l’utilisation de l’intergiciel () de redirection HTTPs ( `UseHttpsRedirection` ) consiste à utiliser l’intergiciel () de réécriture d’URL `AddRedirectToHttps` . `AddRedirectToHttps` peut également définir le code et le port d’état lors de l’exécution de la redirection. Pour plus d’informations, consultez intergiciel (middleware) de [réécriture d’URL](xref:fundamentals/url-rewriting).

Lorsque vous redirigez vers le protocole HTTPs sans avoir besoin de règles de redirection supplémentaires, nous vous recommandons d’utiliser l’intergiciel () de redirection HTTPs ( `UseHttpsRedirection` ) décrit dans cette rubrique.

<a name="hsts"></a>

## <a name="http-strict-transport-security-protocol-hsts"></a>Protocole HTTP strict transport Security (HSTS)

Par [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project), [http strict transport Security (HSTS)](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) est une amélioration de sécurité qui est spécifiée par une application Web via l’utilisation d’un en-tête de réponse. Quand un [navigateur prenant en charge HSTS](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html#browser-support) reçoit cet en-tête :

* Le navigateur stocke la configuration pour le domaine qui empêche l’envoi de toute communication sur HTTP. Le navigateur force toutes les communications sur HTTPs.
* Le navigateur empêche l’utilisateur d’utiliser des certificats non approuvés ou non valides. Le navigateur désactive les invites qui permettent à un utilisateur d’approuver temporairement ce type de certificat.

Étant donné que HSTS est appliqué par le client, il présente certaines limitations :

* Le client doit prendre en charge HSTS.
* HSTS nécessite au moins une demande HTTPs réussie pour établir la stratégie HSTS.
* L’application doit vérifier chaque requête HTTP et rediriger ou rejeter la requête HTTP.

ASP.NET Core 2,1 et versions ultérieures implémentent HSTS avec la `UseHsts` méthode d’extension. Le code suivant appelle `UseHsts` lorsque l’application n’est pas en [mode de développement](xref:fundamentals/environments):

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=10)]

::: moniker-end

`UseHsts` n’est pas recommandé dans le développement, car les paramètres HSTS sont très facilement mis en cache par les navigateurs. Par défaut, `UseHsts` exclut l’adresse de bouclage locale.

Pour les environnements de production qui implémentent le protocole HTTPs pour la première fois, définissez [HstsOptions. MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) initiale sur une petite valeur à l’aide de l’une des <xref:System.TimeSpan> méthodes. Définissez la valeur des heures sur un seul jour si vous devez rétablir l’infrastructure HTTPs sur HTTP. Une fois que vous êtes certain de la durabilité de la configuration HTTPs, augmentez la valeur de HSTS `max-age` ; une valeur couramment utilisée est d’un an.

Le code suivant :


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end


* Définit le paramètre de préchargement de l' `Strict-Transport-Security` en-tête. Le préchargement ne fait pas partie de la [spécification RFC HSTS](https://tools.ietf.org/html/rfc6797), mais est pris en charge par les navigateurs Web pour précharger des sites HSTS sur une nouvelle installation. Pour plus d’informations, consultez [https://hstspreload.org/](https://hstspreload.org/).
* Active [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), qui applique la stratégie HSTS pour héberger des sous-domaines.
* Définit explicitement le `max-age` paramètre de l' `Strict-Transport-Security` en-tête à 60 jours. S’il n’est pas défini, la valeur par défaut est 30 jours. Pour plus d’informations, consultez la [directive max-age](https://tools.ietf.org/html/rfc6797#section-6.1.1).
* Ajoute `example.com` à la liste des hôtes à exclure.

`UseHsts` exclut les hôtes de bouclage suivants :

* `localhost` : Adresse de bouclage IPv4.
* `127.0.0.1` : Adresse de bouclage IPv4.
* `[::1]` : Adresse de bouclage IPv6.

## <a name="opt-out-of-httpshsts-on-project-creation"></a>Désactiver HTTPs/HSTS lors de la création du projet

Dans certains scénarios de service backend où la sécurité de connexion est gérée au niveau du périmètre public du réseau, la configuration de la sécurité des connexions sur chaque nœud n’est pas obligatoire. Les applications Web générées à partir des modèles dans Visual Studio ou à partir de la commande [dotnet New](/dotnet/core/tools/dotnet-new) activent la [redirection https](#require-https) et [HSTS](#http-strict-transport-security-protocol-hsts). Pour les déploiements qui n’ont pas besoin de ces scénarios, vous pouvez désactiver HTTPs/HSTS quand l’application est créée à partir du modèle.

Pour désactiver HTTPs/HSTS :

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio) 

Désactivez la case à cocher **configurer pour HTTPS** .

::: moniker range=">= aspnetcore-3.0"

![Nouvelle boîte de dialogue d’application Web ASP.NET Core avec la case à cocher configurer pour HTTPs désélectionnée.](enforcing-ssl/_static/out-vs2019.png)

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

![Nouvelle boîte de dialogue d’application Web ASP.NET Core avec la case à cocher configurer pour HTTPs désélectionnée.](enforcing-ssl/_static/out.png)

::: moniker-end


# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli) 

Utilisez l'option `--no-https`. Par exemple

```dotnetcli
dotnet new webapp --no-https
```

---

<a name="trust"></a>

## <a name="trust-the-aspnet-core-https-development-certificate-on-windows-and-macos"></a>Approuver le certificat de développement HTTPs ASP.NET Core sur Windows et macOS

Le kit SDK .NET Core comprend un certificat de développement HTTPs. Le certificat est installé dans le cadre de la première exécution. Par exemple, `dotnet --info` produit une variation de la sortie suivante :

```
ASP.NET Core
------------
Successfully installed the ASP.NET Core HTTPS Development Certificate.
To trust the certificate run 'dotnet dev-certs https --trust' (Windows and macOS only).
For establishing trust on other platforms refer to the platform specific documentation.
For more information on configuring HTTPS see https://go.microsoft.com/fwlink/?linkid=848054.
```

L’installation du kit SDK .NET Core installe le certificat de développement ASP.NET Core HTTPS dans le magasin de certificats de l’utilisateur local. Le certificat a été installé, mais il n’est pas approuvé. Pour approuver le certificat, effectuez l’étape unique pour exécuter l' `dev-certs` outil dotnet :

```dotnetcli
dotnet dev-certs https --trust
```

La commande suivante fournit de l’aide sur l’outil `dev-certs` :

```dotnetcli
dotnet dev-certs https --help
```

## <a name="how-to-set-up-a-developer-certificate-for-docker"></a>Comment configurer un certificat de développeur pour l’arrimeur

Consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/6199).

<a name="ssl-linux"></a>

## <a name="trust-https-certificate-on-linux"></a>Approuver le certificat HTTPs sur Linux

<!-- Instructions to be updated by engineering team after 5.0 RTM. -->

Pour obtenir des instructions sur Linux, reportez-vous à la documentation relative à la distribution.

<a name="wsl"></a>

## <a name="trust-https-certificate-from-windows-subsystem-for-linux"></a>Approuver le certificat HTTPs à partir du sous-système Windows pour Linux

Le sous-système Windows pour Linux (WSL) génère un certificat auto-signé HTTPs. Pour configurer le magasin de certificats Windows de façon à ce qu’il approuve le certificat WSL :

* Exécutez la commande suivante pour exporter le certificat généré par WSL :

  ```
  dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>
  ```
* Dans une fenêtre WSL, exécutez la commande suivante :

  ```
    ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" 
    ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx
    dotnet watch run
  ```

  La commande précédente définit les variables d’environnement pour que Linux utilise le certificat de confiance Windows.

## <a name="troubleshoot-certificate-problems"></a>Résoudre les problèmes de certificat

Cette section fournit de l’aide lorsque le certificat de développement HTTPs ASP.NET Core a été [installé et approuvé](#trust), mais que vous avez encore des avertissements de navigateur indiquant que le certificat n’est pas approuvé. Le certificat de développement HTTPs ASP.NET Core est utilisé par [Kestrel](xref:fundamentals/servers/kestrel).

Pour réparer le certificat de IIS Express, consultez [ce problème StackOverflow](https://stackoverflow.com/a/20048613/502537) .

### <a name="all-platforms---certificate-not-trusted"></a>Toutes les plateformes-certificat non approuvé

Exécutez les commandes suivantes :

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

Fermez toutes les instances de navigateur ouvertes. Ouvrez une nouvelle fenêtre de navigateur pour l’application. L’approbation de certificat est mise en cache par les navigateurs.

Les commandes précédentes résolvent la plupart des problèmes d’approbation du navigateur. Si le navigateur n’approuve toujours pas le certificat, suivez les suggestions spécifiques à la plateforme qui suivent.

### <a name="docker---certificate-not-trusted"></a>Dockr-certificat non approuvé

* Supprimez le dossier *C:\Users \{ User} \AppData\Roaming\ASP.NET\Https* .
* Nettoyez la solution. Supprimez les dossiers *bin* et *obj*.
* Redémarrez l’outil de développement. Par exemple, Visual Studio, Visual Studio Code ou Visual Studio pour Mac.

### <a name="windows---certificate-not-trusted"></a>Windows-certificat non approuvé

* Vérifiez les certificats dans le magasin de certificats. Il doit y avoir un `localhost` certificat avec le `ASP.NET Core HTTPS development certificate` nom convivial à la fois sous `Current User > Personal > Certificates` et `Current User > Trusted root certification authorities > Certificates`
* Supprimez tous les certificats détectés des autorités de certification racines personnelles et de confiance. Ne supprimez **pas** le certificat IIS Express localhost.
* Exécutez les commandes suivantes :

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

Fermez toutes les instances de navigateur ouvertes. Ouvrez une nouvelle fenêtre de navigateur pour l’application.

### <a name="os-x---certificate-not-trusted"></a>OS X-certificat non approuvé

* Ouvrez le trousseau d’accès.
* Sélectionnez le trousseau système.
* Vérifiez la présence d’un certificat localhost.
* Vérifiez qu’il contient un `+` symbole sur l’icône pour indiquer qu’il est approuvé pour tous les utilisateurs.
* Supprimez le certificat du trousseau système.
* Exécutez les commandes suivantes :

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

Fermez toutes les instances de navigateur ouvertes. Ouvrez une nouvelle fenêtre de navigateur pour l’application.

Consultez [erreur HTTPS à l’aide de IIS Express (dotnet/AspNetCore #16892)](https://github.com/dotnet/AspNetCore/issues/16892) pour résoudre les problèmes liés aux certificats dans Visual Studio.

### <a name="iis-express-ssl-certificate-used-with-visual-studio"></a>IIS Express certificat SSL utilisé avec Visual Studio

Pour résoudre les problèmes liés au certificat de IIS Express, sélectionnez **réparer** dans le programme d’installation de Visual Studio. Pour plus d’informations, consultez [ce problème GitHub](https://github.com/dotnet/aspnetcore/issues/16892).

<a name="trust-ff"></a>

### <a name="firefox-sec_error_inadequate_key_usage-certificate-error"></a>Erreur de certificat de SEC_ERROR_INADEQUATE_KEY_USAGE Firefox

Le navigateur Firefox utilise son propre magasin de certificats. par conséquent, il n’approuve pas les certificats de développeur [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) ou [Kestrel](xref:fundamentals/servers/kestrel) .

Pour utiliser Firefox avec IIS Express ou Kestrel, définissez  `security.enterprise_roots.enabled` = `true`

1. Entrez `about:config` dans le navigateur Firefox.
1. Sélectionnez **accepter le risque et continuer** si vous acceptez le risque.
1. Sélectionner **Afficher tout**
1. Définie `security.enterprise_roots.enabled` = `true`
1. Quitter et redémarrer Firefox

Pour plus d’informations, consultez [Configuration des autorités de certification (ca) dans Firefox](https://support.mozilla.org/kb/setting-certificate-authorities-firefox).

## <a name="additional-information"></a>Informations supplémentaires

* <xref:host-and-deploy/proxy-load-balancer>
* [ASP.NET Core d’hôte sur Linux avec Apache : configuration HTTPs](xref:host-and-deploy/linux-apache#https-configuration)
* [ASP.NET Core d’hôte sur Linux avec Nginx : configuration HTTPs](xref:host-and-deploy/linux-nginx#https-configuration)
* [Configuration du protocole SSL sur IIS](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)
* [Prise en charge du navigateur OWASP HSTS](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support)
