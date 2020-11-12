---
title: Télémétrie HttpRepl
author: scottaddie
description: En savoir plus sur les données de télémétrie collectées par le HttpRepl.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.date: 11/11/2020
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
uid: web-api/http-repl/telemetry
ms.openlocfilehash: 5ff22753f566c494e51dae67c8c4f6371211be78
ms.sourcegitcommit: 202144092067ea81be1dbb229329518d781dbdfb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550606"
---
# <a name="httprepl-telemetry"></a>Télémétrie HttpRepl

[HttpRepl](xref:web-api/http-repl) comprend une fonctionnalité de télémétrie qui collecte les données d’utilisation. Il est important que l’équipe HttpRepl comprenne comment l’outil est utilisé pour qu’il puisse être amélioré.

## <a name="how-to-opt-out"></a>Comment désactiver la fonctionnalité

La fonctionnalité de télémétrie HttpRepl est activée par défaut. Pour désactiver la fonctionnalité de télémétrie, définissez la variable d’environnement `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` sur `1` ou `true`.

## <a name="disclosure"></a>Divulgation d’informations

Le HttpRepl affiche un texte similaire à ce qui suit lorsque vous exécutez pour la première fois l’outil. Le texte peut varier légèrement en fonction de la version de l’outil que vous exécutez. Lors de cette première exécution, Microsoft vous informe sur la collecte de données.

```console
Telemetry
---------
The .NET tools collect usage data in order to help us improve your experience. It is collected by Microsoft and shared with the community. You can opt-out of telemetry by setting the DOTNET_HTTPREPL_TELEMETRY_OPTOUT environment variable to '1' or 'true' using your favorite shell.
```

Pour supprimer le texte d’expérience « première exécution », définissez la `DOTNET_HTTPREPL_SKIP_FIRST_TIME_EXPERIENCE` variable d’environnement sur `1` ou `true` .

## <a name="data-points"></a>Points de données

La fonctionnalité de télémétrie n’est pas :

* Recueillez des données personnelles, telles que des noms d’utilisateur, des adresses de messagerie ou des URL.
* Analyser vos requêtes ou réponses HTTP.

Les données sont envoyées en toute sécurité aux serveurs Microsoft et conservées sous accès restreint.

Nous prenons la protection de vos données au sérieux. Si vous pensez que la fonctionnalité de télémétrie collecte des données sensibles ou que les données sont gérées de manière inappropriée ou non, effectuez l’une des actions suivantes :

* Déposez un problème dans le référentiel [dotnet/httprepl](https://github.com/dotnet/httprepl/issues) .
* Envoyer un e-mail à [dotnet@microsoft.com](mailto:dotnet@microsoft.com) pour investigation.

La fonctionnalité de télémétrie collecte les données suivantes.

| Versions du SDK .NET | Données |
|--------------|------|
| >= 5,0        | Horodatage de l’appel. |
| >= 5,0        | Adresse IP de trois octets utilisée pour déterminer l’emplacement géographique. |
| >= 5,0        | Système d’exploitation et version. |
| >= 5,0        | ID d’exécution (RID) sur lequel l’outil est exécuté. |
| >= 5,0        | Indique si l’outil s’exécute dans un conteneur. |
| >= 5,0        | Adresse du Access Control de média haché (MAC) : un ID (SHA256) haché et unique pour un ordinateur. |
| >= 5,0        | Version du noyau. |
| >= 5,0        | Version de HttpRepl. |
| >= 5,0        | Si l’outil a été démarré avec les `help` `run` arguments, ou `connect` . Les valeurs d’argument réelles ne sont pas collectées. |
| >= 5,0        | Commande appelée (par exemple, `get` ) et si elle a réussi. |
| >= 5,0        | Pour la `connect` commande, si les `root` `base` arguments, ou `openapi` ont été fournis. Les valeurs d’argument réelles ne sont pas collectées. |
| >= 5,0        | Pour la `pref` commande, si un `get` ou `set` a été émis et quelles préférences ont été consultées. S’il ne s’agit pas d’une préférence connue, le nom est haché. La valeur n’est pas collectée. |
| >= 5,0        | Pour la `set header` commande, le nom d’en-tête est défini. Si ce n’est pas un en-tête bien connu, le nom est haché. La valeur n’est pas collectée. |
| >= 5,0        | Pour la `connect` commande, si un cas spécial pour `dotnet new webapi` a été utilisé et, s’il a été contourné par la préférence. |
| >= 5,0        | Pour toutes les commandes HTTP (par exemple, obtenir, poster, PUT), si chacune des options a été spécifiée. Les valeurs des options ne sont pas collectées. |

## <a name="additional-resources"></a>Ressources supplémentaires

* [Télémétrie du kit SDK .NET Core](/dotnet/core/tools/telemetry)
* [CLI .NET Core des données de télémétrie](https://dotnet.microsoft.com/platform/telemetry)
