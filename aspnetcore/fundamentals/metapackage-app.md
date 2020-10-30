---
title: AspNetCore Microsoft.. app pour ASP.NET Core
author: Rick-Anderson
description: Infrastructure partagée Microsoft. AspNetCore. app
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 09/24/2019
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
uid: fundamentals/metapackage-app
ms.openlocfilehash: 225bb1f55c099a476319191726c5a661f9a4893a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057802"
---
# <a name="microsoftaspnetcoreapp-for-aspnet-core"></a>Microsoft. AspNetCore. app pour ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

 Le ASP.NET Core Framework partagé ( `Microsoft.AspNetCore.App` ) contient des assemblys développés et pris en charge par Microsoft. `Microsoft.AspNetCore.App` est installé lors de l’installation du [Kit de développement logiciel (SDK) .net Core 3,0 ou version ultérieure](https://dotnet.microsoft.com/download/dotnet-core/3.0) . L' *infrastructure partagée* est l’ensemble d’assemblys (fichiers *. dll* ) qui sont installés sur l’ordinateur et comprend un composant d’exécution et un pack de ciblage. Pour plus d’informations, consultez [Le framework partagé](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

* Les projets qui ciblent le `Microsoft.NET.Sdk.Web` Kit de développement logiciel (SDK) référencent implicitement le `Microsoft.AspNetCore.App` Framework.

Aucune référence supplémentaire n’est requise pour ces projets :

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>
    ...
</Project>
```

Framework partagé ASP.NET Core :

* N’inclut pas les dépendances tierces.
* Comprend tous les packages pris en charge par l’équipe ASP.NET Core.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Cette fonctionnalité nécessite ASP.NET Core 2.x ciblant .NET Core 2.x.

Le [AspNetCore Microsoft.. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App) [pour ASP.net Core](/dotnet/core/packages#metapackages) :

* N’inclut pas les dépendances tierces sauf pour [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/), [Remotion.Linq](https://www.nuget.org/packages/Remotion.Linq/) et [IX-Async](https://www.nuget.org/packages/System.Interactive.Async/). Ces dépendances tierces sont considérées comme nécessaires pour assurer le fonctionnement des principales fonctionnalités des frameworks.
* Inclut tous les packages pris en charge par l’équipe ASP.NET Core, sauf ceux qui contiennent des dépendances tierces (autres que celles mentionnées au-dessus).
* Inclut tous les packages pris en charge par l’équipe Entity Framework Core, sauf ceux qui contiennent des dépendances tierces (autres que celles mentionnées au-dessus).

Toutes les fonctionnalités d’ASP.NET Core 2.x et d’Entity Framework Core 2.x sont incluses dans le package `Microsoft.AspNetCore.App`. Les modèles de projet par défaut ciblant ASP.NET Core 2. x utilisent ce package. Nous recommandons aux applications ciblant ASP.NET Core 2. x et Entity Framework Core 2. x d’utiliser le `Microsoft.AspNetCore.App` Package.

Le numéro de version du métapaquet `Microsoft.AspNetCore.App` représente la version minimale d’ASP.NET Core et la version d’Entity Framework Core.

L’utilisation du métapackage `Microsoft.AspNetCore.App` offre des restrictions de version qui protègent votre application :

* Si un package inclus a une dépendance transitive (non directe) sur un package dans `Microsoft.AspNetCore.App` et que ces numéros de version diffèrent, NuGet génère une erreur.
* Les autres packages ajoutés à votre application ne peuvent pas changer la version des packages inclus dans `Microsoft.AspNetCore.App`.
* La cohérence des versions garantit une expérience fiable. `Microsoft.AspNetCore.App` a été conçu pour empêcher des combinaisons de versions non testées de bits associés d’être utilisées ensemble dans la même application.

Les applications qui utilisent le métapackage `Microsoft.AspNetCore.App` tirent automatiquement parti du framework partagé ASP.NET Core. Quand vous utilisez le métapackage `Microsoft.AspNetCore.App`,  aucune&mdash; des ressources des packages NuGet ASP.NET Core référencés n’est déployée avec l’application : le framework partagé ASP.NET Core contient ces ressources. Les ressources présentes dans le framework partagé sont précompilées afin d’améliorer la vitesse de démarrage des applications. Pour plus d’informations, consultez [Le framework partagé](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

Le fichier projet suivant fait référence au « `Microsoft.AspNetCore.App` package » pour ASP.net Core et représente un modèle ASP.NET Core 2,2 standard :

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.2</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

</Project>
```

Le balisage précédent représente un modèle ASP.NET Core 2. x classique. Il ne spécifie pas de numéro de version pour la référence du package `Microsoft.AspNetCore.App`. Lorsque la version n’est pas spécifiée, une version [implicite](https://github.com/dotnet/core/blob/master/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md) est spécifiée par le kit SDK, autrement dit, `Microsoft.NET.Sdk.Web`. Nous vous recommandons de garder la version implicite spécifiée par le kit SDK et de ne pas définir de façon explicite le numéro de version sur la référence de package. Si vous avez des questions par rapport à cette approche, laissez un commentaire GitHub à la page [Discussion concernant la version implicite de Microsoft.AspNetCore.App](https://github.com/dotnet/AspNetCore.Docs/issues/6430).

La version implicite est définie sur `major.minor.0` pour les applications portables. Le mécanisme de restauration par progression des frameworks partagés exécute l’application sur la dernière version compatible parmi les frameworks partagés installés. Pour garantir l’utilisation de la même version en développement, test et production, vérifiez que la même version du framework partagé est installée dans tous les environnements. Pour les applications autonomes, le numéro de version implicite est défini sur le `major.minor.patch` du framework partagé inclus dans le kit SDK installé.

La spécification d’un numéro de version sur la référence `Microsoft.AspNetCore.App` ne garantit **pas** que la version du framework partagé sera choisie. Par exemple, supposez que la version « 2.2.1 » est spécifiée, mais que « 2.2.3 » est installé. Dans ce cas, l’application utilisera « 2.2.3 ». Même si ce n’est pas recommandé, vous pouvez désactiver la restauration par progression (correctif et/ou mineur). Pour plus d’informations sur la restauration par progression de l’hôte dotnet et sur la configuration de son comportement, consultez [dotnet host roll forward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).

::: moniker-end

::: moniker range="= aspnetcore-2.1"

`<Project Sdk` doit avoir la valeur `Microsoft.NET.Sdk.Web` pour utiliser la version implicite `Microsoft.AspNetCore.App`. Lorsque `<Project Sdk="Microsoft.NET.Sdk">` (sans la fin `.Web`) est utilisé :

* L’avertissement suivant est généré :

  *AVERTISSEMENT NU1604 : dépendance de projet Microsoft. AspNetCore. app ne contient pas de limite inférieure inclusive. Incluez une limite inférieure dans la version de dépendance pour garantir la cohérence des résultats de la restauration.*

* Il s’agit d’un problème connu avec le kit de développement logiciel (SDK) .NET Core 2.1.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<a name="update"></a>

## <a name="update-aspnet-core"></a>Mettre à jour ASP.NET Core

Le  [métapackage](/dotnet/core/packages#metapackages)`Microsoft.AspNetCore.App` n’est pas un package traditionnel qui est mis à jour à partir de NuGet. Semblable à `Microsoft.NETCore.App`, `Microsoft.AspNetCore.App` représente un runtime partagé avec une sémantique de version spéciale gérée en dehors de NuGet. Pour plus d’informations, consultez [Packages, métapackages et frameworks](/dotnet/core/packages).

Pour mettre à jour ASP.NET Core :

* Sur les machines de développement et les serveurs de builds, téléchargez et installez le [SDK .NET Core](https://dotnet.microsoft.com/download).
* Sur les serveurs de déploiement, téléchargez et installez le [Runtime .NET Core](https://dotnet.microsoft.com/download).

 Les applications seront restaurées par progression jusqu’à la version installée la plus récente au moment de leur redémarrage. Vous n’avez pas besoin de mettre à jour le numéro de version `Microsoft.AspNetCore.App` dans le fichier projet. Pour plus d’informations, consultez [Restaurer par progression des applications dépendantes du framework](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward).

Si votre application utilisait `Microsoft.AspNetCore.All`, consultez [Migration de Microsoft.AspNetCore.All vers Microsoft.AspNetCore.App](xref:fundamentals/metapackage#migrate).

::: moniker-end
