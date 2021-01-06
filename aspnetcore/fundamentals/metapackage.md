---
title: Métapackage Microsoft.AspNetCore.All pour ASP.NET Core 2.0
author: Rick-Anderson
description: Le métapackage Microsoft.AspNetCore.All n’est pas recommandé pour ASP.NET Core 2.1 et versions ultérieures.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/25/2018
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
uid: fundamentals/metapackage
ms.openlocfilehash: b739398c2a440f21c8bdfdc1f4d8e25412358a6a
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060688"
---
# <a name="microsoftaspnetcoreall-metapackage-for-aspnet-core-20"></a>Métapackage Microsoft.AspNetCore.All pour ASP.NET Core 2.0

::: moniker range=">= aspnetcore-3.0"

Le `Microsoft.AspNetCore.All` package n’est pas inclus dans ASP.NET Core 3,0 et versions ultérieures. Pour plus d’informations, consultez [ce problème GitHub](https://github.com/aspnet/Announcements/issues/314).

::: moniker-end

> [!NOTE]
> Nous recommandons que les applications qui ciblent ASP.NET Core 2.1 et ultérieur utilisent le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) au lieu de ce package. Consultez [Migration de Microsoft.AspNetCore.All vers Microsoft.AspNetCore.App](#migrate) dans cet article.

Cette fonctionnalité nécessite ASP.NET Core 2.x ciblant .NET Core 2.x.

[Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) est un métapaquet qui fait référence à un framework partagé. Un *framework partagé* est un ensemble d’assemblys (fichiers *.dll*) qui ne sont pas dans les dossiers de l’application. Le framework partagé doit être installé sur l’ordinateur pour exécuter l’application. Pour plus d’informations, consultez [Le framework partagé](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

Le framework partagé auquel `Microsoft.AspNetCore.All` fait référence inclut :

* Tous les packages pris en charge par l’équipe ASP.NET Core.
* Tous les packages pris en charge par Entity Framework Core.
* Les dépendances internes et tierces utilisées par ASP.NET Core et Entity Framework Core.

Toutes les fonctionnalités d’ASP.NET Core 2.x et d’Entity Framework Core 2.x sont incluses dans le package `Microsoft.AspNetCore.All`. Les modèles de projet par défaut ciblant ASP.NET Core 2.0 utilisent ce package.

Le numéro de version du métapaquet `Microsoft.AspNetCore.All` représente la version minimale d’ASP.NET Core et la version d’Entity Framework Core.

Le fichier *.csproj* suivant fait référence au métapackage `Microsoft.AspNetCore.All` pour ASP.NET Core :

[!code-xml[](metapackage/samples/Metapackage.All.Example.csproj?highlight=8)]

::: moniker range=">= aspnetcore-2.1"

## <a name="implicit-versioning"></a>Gestion des versions implicites

Dans ASP.NET Core 2.1 ou ultérieur, vous pouvez spécifier la référence de package `Microsoft.AspNetCore.All` sans version. Lorsque la version n’est pas spécifiée, une version implicite est spécifiée par le SDK (`Microsoft.NET.Sdk.Web`). Nous vous recommandons de garder la version implicite spécifiée par le kit SDK et de ne pas définir de façon explicite le numéro de version sur la référence de package. Si vous avez des questions au sujet de cette approche, envoyez vos commentaires GitHub dans la [Discussion concernant la version implicite de Microsoft.AspNetCore.App](https://github.com/dotnet/AspNetCore.Docs/issues/6430).

La version implicite est définie sur `major.minor.0` pour les applications portables. Le mécanisme de restauration par progression des frameworks partagés exécute l’application sur la dernière version compatible parmi les frameworks partagés installés. Pour garantir l’utilisation de la même version en développement, test et production, vérifiez que la même version du framework partagé est installée dans tous les environnements. Pour les applications autonomes, le numéro de version implicite est défini sur le `major.minor.patch` du framework partagé inclus dans le SDK installé.

La spécification d’un numéro de version sur la référence de package `Microsoft.AspNetCore.All` ne garantit **pas** que la version du framework partagé sera choisie. Par exemple, supposons que la version « 2.1.1 » est spécifiée, mais que la version « 2.1.3 » est installée. Dans ce cas, l’application utilise « 2.1.3 ». Même si ce n’est pas recommandé, vous pouvez désactiver la restauration par progression (correctif et/ou mineur). Pour plus d’informations sur la restauration par progression de l’hôte dotnet et sur la configuration de son comportement, consultez [dotnet host roll forward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).

Le SDK du projet doit être défini sur `Microsoft.NET.Sdk.Web` dans le fichier projet pour utiliser la version implicite de `Microsoft.AspNetCore.All`. Lorsque le SDK `Microsoft.NET.Sdk` est spécifié (`<Project Sdk="Microsoft.NET.Sdk">` en haut du fichier projet), l’avertissement suivant est généré :

*AVERTISSEMENT NU1604 : dépendance de projet Microsoft. AspNetCore. All ne contient pas de limite inférieure inclusive. Incluez une limite inférieure dans la version de dépendance pour garantir la cohérence des résultats de la restauration.*

C’est un problème connu avec le kit SDK .NET Core 2.1 ; il sera corrigé dans le kit SDK .NET Core 2.2.

::: moniker-end

<a name="migrate"></a>

## <a name="migrating-from-microsoftaspnetcoreall-to-microsoftaspnetcoreapp"></a>Migration de Microsoft.AspNetCore.All vers Microsoft.AspNetCore.App

Les packages suivants sont inclus dans `Microsoft.AspNetCore.All` mais pas le package `Microsoft.AspNetCore.App`.

* `Microsoft.AspNetCore.ApplicationInsights.HostingStartup`
* `Microsoft.AspNetCore.AzureAppServices.HostingStartup`
* `Microsoft.AspNetCore.AzureAppServicesIntegration`
* `Microsoft.AspNetCore.DataProtection.AzureKeyVault`
* `Microsoft.AspNetCore.DataProtection.AzureStorage`
* `Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv`
* `Microsoft.AspNetCore.SignalR.Redis`
* `Microsoft.Data.Sqlite`
* `Microsoft.Data.Sqlite.Core`
* `Microsoft.EntityFrameworkCore.Sqlite`
* `Microsoft.EntityFrameworkCore.Sqlite.Core`
* `Microsoft.Extensions.Caching.Redis`
* `Microsoft.Extensions.Configuration.AzureKeyVault`
* `Microsoft.Extensions.Logging.AzureAppServices`
* `Microsoft.VisualStudio.Web.BrowserLink`

Pour passer de `Microsoft.AspNetCore.All` à `Microsoft.AspNetCore.App`, si votre application utilise les API des packages ci-dessus, ou les packages apportés par ces packages, ajoutez des références à ces packages dans votre projet.

Toutes les dépendances des packages précédents qui ne sont pas des dépendances de `Microsoft.AspNetCore.App` ne sont pas incluses de manière implicite. Par exemple :

* `StackExchange.Redis` comme dépendance de `Microsoft.Extensions.Caching.Redis`
* `Microsoft.ApplicationInsights` comme dépendance de `Microsoft.AspNetCore.ApplicationInsights.HostingStartup`

## <a name="update-aspnet-core-21"></a>Mettre à jour ASP.NET Core 2.1

Nous vous recommandons de migrer vers le métapackage `Microsoft.AspNetCore.App` pour 2.1 et versions ultérieures. Pour continuer à utiliser le métapackage `Microsoft.AspNetCore.All` et vérifier que la dernière version corrective est déployée :

* Sur les machines de développement et les serveurs de builds, installez le [kit SDK .NET Core](https://dotnet.microsoft.com/download) le plus récent.
* Sur les serveurs de déploiement, installez le [Runtime .NET Core](https://dotnet.microsoft.com/download) le plus récent.
 Votre application est restaurée par progression jusqu’à la version installée la plus récente au moment de son redémarrage.
