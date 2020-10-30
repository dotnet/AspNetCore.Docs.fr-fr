---
title: fichier web.config
author: rick-anderson
description: Découvrez ce qui se trouve dans le fichier web.config et comment configurer différentes options de module ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
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
uid: host-and-deploy/iis/web-config
ms.openlocfilehash: edeef31042547db79fcec98f1236787f78e187a5
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057282"
---
# <a name="webconfig-file"></a>Fichier `web.config`

Le `web.config` est un fichier qui est lu par IIS et le [module ASP.net Core](xref:host-and-deploy/aspnet-core-module) pour configurer une application hébergée avec IIS.

## <a name="webconfig-file-location"></a>`web.config` emplacement du fichier

Pour configurer correctement le [Module ASP.net Core](xref:host-and-deploy/aspnet-core-module) , le `web.config` fichier doit être présent au chemin d’accès [racine du contenu](xref:fundamentals/index#content-root) (en général, le chemin d’accès de base de l’application) de l’application déployée. Il s’agit du même emplacement que le chemin physique du site Web fourni à IIS. Le `web.config` fichier est requis à la racine de l’application pour permettre la publication de plusieurs applications à l’aide de Web Deploy.

Des fichiers sensibles existent sur le chemin d’accès physique de l’application, par exemple `{ASSEMBLY}.runtimeconfig.json` , `{ASSEMBLY}.xml` (commentaires de documentation XML) et `{ASSEMBLY}.deps.json` , où l’espace réservé `{ASSEMBLY}` est le nom de l’assembly. Lorsque le `web.config` fichier est présent et que le site démarre normalement, IIS ne traite pas ces fichiers sensibles s’ils sont demandés. Si le `web.config` fichier est manquant, nommé de façon incorrecte ou ne peut pas configurer le site pour un démarrage normal, IIS peut traiter les fichiers sensibles publiquement.

**Le `web.config` fichier doit être présent dans le déploiement à tout moment, correctement nommé et capable de configurer le site pour un démarrage normal. Ne supprimez jamais le `web.config` fichier d’un déploiement de production.**

Si un `web.config` fichier n’est pas présent dans le projet, le fichier est créé avec les et corrects `processPath` `arguments` pour configurer le module ASP.net Core, puis déplacé vers la [sortie publiée](xref:host-and-deploy/directory-structure).

Si un `web.config` fichier est présent dans le projet, le fichier est transformé avec les et corrects `processPath` `arguments` pour configurer le module ASP.net Core, puis déplacé vers la sortie publiée. La transformation ne modifie pas les paramètres de configuration IIS dans le fichier.

Le `web.config` fichier peut fournir des paramètres de configuration IIS supplémentaires qui contrôlent les modules IIS actifs. Pour plus d’informations sur les modules IIS capables de traiter les requêtes avec des applications ASP.NET Core, consultez la rubrique [Modules IIS](xref:host-and-deploy/iis/modules).

La création, la transformation et la publication du `web.config` fichier sont gérées par une cible MSBuild ( `_TransformWebConfig` ) lors de la publication du projet. Cette cible est présente dans les cibles du SDK web (`Microsoft.NET.Sdk.Web`). Le SDK est défini en haut du fichier projet :

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Pour empêcher le kit de développement logiciel (SDK) Web de transformer le `web.config` fichier, utilisez la `<IsTransformWebConfigDisabled>` propriété dans le fichier projet :

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Lorsque vous désactivez le kit de développement logiciel (SDK) Web pour transformer le fichier, `processPath` et `arguments` doit être défini manuellement par le développeur. Pour plus d'informations, consultez <xref:host-and-deploy/aspnet-core-module>.

## <a name="configuration-of-aspnet-core-module-with-webconfig"></a>Configuration de ASP.NET Core Module avec `web.config`

Le module ASP.NET Core est configuré avec la `aspNetCore` section du `system.webServer` nœud dans le fichier du site `web.config` .

Le `web.config` fichier suivant est publié pour un [déploiement dépendant du Framework](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) et configure le module ASP.net Core pour gérer les demandes de site :

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

Les éléments suivants `web.config` sont publiés pour un [Déploiement autonome](/dotnet/articles/core/deploying/#self-contained-deployments-scd):

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

La <xref:System.Configuration.SectionInformation.InheritInChildApplications%2A> propriété a la valeur `false` pour indiquer que les paramètres spécifiés dans l' [`<location>`](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) élément ne sont pas hérités par les applications qui résident dans un sous-répertoire de l’application.

Lorsqu’une application est déployée sur [Azure App Service](https://azure.microsoft.com/services/app-service/), le chemin d’accès `stdoutLogFile` est défini sur `\\?\%home%\LogFiles\stdout`. Le chemin d’accès enregistre les journaux stdout dans le `LogFiles` dossier, qui est un emplacement créé automatiquement par le service.

Pour plus d’informations sur la configuration d’une sous-application IIS, consultez <xref:host-and-deploy/iis/index#sub-applications>.

### <a name="attributes-of-the-aspnetcore-element"></a>Attributs de l' `aspNetCore` élément

| Attribut | Description | Default |
| --------- | ----------- | :-----: |
| `arguments` | <p>Attribut de chaîne facultatif.</p><p>Arguments de l’exécutable spécifié dans `processPath` .</p> | |
| `disableStartUpErrorPage` | <p>Attribut booléen facultatif.</p><p>Si la valeur est true, la page d' **échec du processus 502,5** est supprimée et la page de codes d’État 502 configurée dans le est `web.config` prioritaire.</p> | `false` |
| `forwardWindowsAuthToken` | <p>Attribut booléen facultatif.</p><p>Si la valeur est true, le jeton est transmis au processus enfant qui écoute `%ASPNETCORE_PORT%` en tant qu’en-tête « MS-ASPNETCORE-WINAUTHTOKEN » par demande. Il incombe à ce processus d’appeler CloseHandle sur ce jeton par demande.</p> | `true` |
| `hostingModel` | <p>Attribut de chaîne facultatif.</p><p>Spécifie le modèle d’hébergement comme in-process ( `InProcess` / `inprocess` ) ou out-of-process ( `OutOfProcess` / `outofprocess` ).</p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p>Attribut entier facultatif.</p><p>Spécifie le nombre d’instances du processus spécifié dans le `processPath` paramètre qui peut être lancé par application.</p><p>&dagger;Pour l’hébergement in-process, la valeur est limitée à `1`.</p><p>Il est déconseillé de définir `processesPerApplication`. Cet attribut sera supprimé dans une version ultérieure.</p> | Valeur par défaut : `1`<br>Min : `1`<br>Max : `100`&dagger; |
| `processPath` | <p>Attribut de chaîne requis.</p><p>Chemin d’accès au fichier exécutable lançant un processus d’écoute des requêtes HTTP. Les chemins d’accès relatifs sont pris en charge. Si le chemin d’accès commence par `.`, il est considéré comme étant relatif par rapport à la racine du site.</p> | |
| `rapidFailsPerMinute` | <p>Attribut entier facultatif.</p><p>Spécifie le nombre de fois où le processus spécifié dans `processPath` est autorisé à se bloquer par minute. Si cette limite est dépassée, le module arrête le lancement du processus pour le reste de la minute.</p><p>Non pris en charge avec hébergement in-process.</p> | Valeur par défaut : `10`<br>Min : `0`<br>Max : `100` |
| `requestTimeout` | <p>Attribut timespan facultatif.</p><p>Spécifie la durée pendant laquelle le module ASP.NET Core attend une réponse de la part du processus à l’écoute sur %ASPNETCORE_PORT%.</p><p>Dans les versions du module ASP.NET Core fournies avec ASP.NET Core 2.1 ou version ultérieure, la valeur `requestTimeout` est spécifiée en heures, minutes et secondes.</p><p>Ne s’applique pas à l’hébergement in-process. Pour l’hébergement in-process, le module attend que l’application traite la requête.</p><p>Les valeurs valides pour les segments de minutes et secondes de la chaîne sont dans la plage de 0 à 59. L’utilisation de `60` dans la valeur de minutes ou de secondes génère une *erreur de serveur 500-Internal* .</p> | Valeur par défaut : `00:02:00`<br>Min : `00:00:00`<br>Max : `360:00:00` |
| `shutdownTimeLimit` | <p>Attribut entier facultatif.</p><p>Durée en secondes pendant laquelle le module attend que l’exécutable s’arrête correctement lorsque le `app_offline.htm` fichier est détecté.</p> | Valeur par défaut : `10`<br>Min : `0`<br>Max : `600` |
| `startupTimeLimit` | <p>Attribut entier facultatif.</p><p>Durée en secondes pendant laquelle le module attend que le fichier exécutable démarre un processus à l’écoute sur le port. Si cette limite de temps est dépassée, le module met fin au processus.</p><p>Lors de l’hébergement *in-process* : le processus n’est **pas** redémarré et n’utilise **pas** le `rapidFailsPerMinute` paramètre.</p><p>En cas *d’hébergement out-of-process* : le module tente de relancer le processus lorsqu’il reçoit une nouvelle demande et continue de tenter de redémarrer le processus lors des demandes entrantes suivantes, à moins que l’application ne démarre pas le `rapidFailsPerMinute` nombre de fois au cours de la dernière minute.</p><p>La valeur 0 (zéro) n’est **pas** considérée comme un délai infini.</p> | Valeur par défaut : `120`<br>Min : `0`<br>Max : `3600` |
| `stdoutLogEnabled` | <p>Attribut booléen facultatif.</p><p>Si la valeur `stdout` est true, et `stderr` pour le processus spécifié dans `processPath` sont redirigés vers le fichier spécifié dans `stdoutLogFile` .</p> | `false` |
| `stdoutLogFile` | <p>Attribut de chaîne facultatif.</p><p>Spécifie le chemin d’accès relatif ou absolu pour lequel `stdout` et `stderr` à partir du processus spécifié dans `processPath` sont journalisés. Les chemins d’accès relatifs sont relatifs par rapport à la racine du site. Tout chemin d’accès commençant par `.` est relatif par rapport à la racine du site et tous les autres chemins d’accès sont traités comme des chemins d’accès absolus. Tous les dossiers fournis dans le chemin sont créés par le module au moment de la création du fichier journal. À l’aide de délimiteurs de trait de soulignement, un horodateur, un ID de processus et une extension de fichier ( `.log` ) sont ajoutés au dernier segment du `stdoutLogFile` chemin d’accès. Si `.\logs\stdout` est fourni en tant que valeur, un exemple de journal stdout est enregistré en tant que `stdout_20180205194132_1934.log` dans le `logs` dossier lorsqu’il est enregistré sur 2/5/2018 à 19:41:32 avec l’ID de processus 1934.</p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a>Définir des variables d’environnement

Des variables d’environnement peuvent être spécifiées pour le processus dans l’attribut `processPath`. Spécifiez une variable d’environnement à l’aide de l’élément enfant `<environmentVariable>` d’un élément de collection `<environmentVariables>`. Les variables d’environnement définies dans cette section prévalent sur les variables d’environnement système.

L’exemple suivant définit deux variables d’environnement dans `web.config` . `ASPNETCORE_ENVIRONMENT` définit l’environnement de l’application sur `Development`. Un développeur peut définir temporairement cette valeur dans le `web.config` fichier afin de forcer le chargement de la [page d’exception du développeur](xref:fundamentals/error-handling) lors du débogage d’une exception d’application. `CONFIG_DIR` est un exemple de variable d’environnement définie par l’utilisateur, dans laquelle le développeur a écrit du code qui lit la valeur de démarrage afin de former un chemin d’accès pour le chargement du fichier de configuration de l’application.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> Une alternative à la définition de l’environnement directement dans `web.config` consiste à inclure la `<EnvironmentName>` propriété dans le [profil de publication ( `.pubxml` )](xref:host-and-deploy/visual-studio-publish-profiles) ou le fichier projet. Cette approche définit l’environnement dans le `web.config` moment où le projet est publié :
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> Affectez uniquement `Development` à la variable d’environnement `ASPNETCORE_ENVIRONMENT` sur les serveurs de test et de préproduction non accessibles aux réseaux non approuvés, par exemple Internet.

## <a name="configuration-of-iis-with-webconfig"></a>Configuration d’IIS avec `web.config`

La configuration d’IIS est influencée par la `<system.webServer>` section de `web.config` pour les scénarios IIS qui sont fonctionnels pour ASP.net Core applications avec le module ASP.net core. Par exemple, la configuration IIS est fonctionnelle pour la compression dynamique. Si IIS est configuré au niveau du serveur pour utiliser la compression dynamique, l' `<urlCompression>` élément dans le fichier de l’application `web.config` peut le désactiver pour une application ASP.net core.

Pour plus d'informations, consultez les rubriques suivantes :

* [Référence de configuration pour `<system.webServer>`](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

Pour définir des variables d’environnement pour des applications individuelles s’exécutant dans des pools d’applications isolés (pris en charge pour IIS 10,0 ou version ultérieure), consultez la section *`AppCmd.exe` commande* de la rubrique [variables `<environmentVariables>` d’environnement](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) dans la documentation de référence IIS.

## <a name="configuration-sections-of-webconfig"></a>Sections de configuration de `web.config`

Les sections de configuration des applications ASP.NET 4. x dans `web.config` ne sont pas utilisées par les applications ASP.net Core pour la configuration :

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

Les applications ASP.NET Core sont configurées à l’aide d’autres fournisseurs de configuration. Pour plus d’informations, consultez [configuration](xref:fundamentals/configuration/index).

## <a name="transform-webconfig"></a>Transformer web.config

Si vous devez effectuer une transformation `web.config` lors de la publication, consultez <xref:host-and-deploy/iis/transform-webconfig> . Vous devrez peut-être effectuer une transformation `web.config` sur la publication pour définir des variables d’environnement en fonction de la configuration, du profil ou de l’environnement.

## <a name="additional-resources"></a>Ressources supplémentaires

* [INFORMATION \<system.webServer>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/iis/modules>
* <xref:host-and-deploy/iis/transform-webconfig>
