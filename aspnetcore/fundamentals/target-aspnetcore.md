---
title: Utiliser des API ASP.NET Core dans une bibliothèque de classes
author: scottaddie
description: Découvrez comment utiliser des API ASP.NET Core dans une bibliothèque de classes.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/16/2019
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
uid: fundamentals/target-aspnetcore
ms.openlocfilehash: c012658a6f48247af60c8bfd56a7d987f6aa8a68
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93061507"
---
# <a name="use-aspnet-core-apis-in-a-class-library"></a>Utiliser des API ASP.NET Core dans une bibliothèque de classes

Par [Scott Addie](https://github.com/scottaddie)

Ce document fournit des conseils pour l’utilisation d’API ASP.NET Core dans une bibliothèque de classes. Pour toutes les autres instructions de bibliothèque, consultez l’aide de la [bibliothèque open source](/dotnet/standard/library-guidance/).

## <a name="determine-which-aspnet-core-versions-to-support"></a>Déterminer les versions de ASP.NET Core à prendre en charge

ASP.NET Core adhère à la [stratégie de prise en charge de .net Core](https://dotnet.microsoft.com/platform/support/policy/dotnet-core). Consultez la stratégie de prise en charge lors de la détermination des versions de ASP.NET Core à prendre en charge dans une bibliothèque. Une bibliothèque doit :

* Prenez le temps de prendre en charge toutes les versions de ASP.NET Core classées en tant que *support à long terme* (LTS).
* Il n’est pas obligatoire de prendre en charge les versions de ASP.NET Core classées en *fin de vie* (EOL).

À mesure que les versions préliminaires des ASP.NET Core sont disponibles, les modifications avec rupture sont publiées dans le référentiel GitHub [ASPNET/announcements](https://github.com/aspnet/Announcements/issues) . Les tests de compatibilité des bibliothèques peuvent être effectués lors du développement des fonctionnalités de l’infrastructure.

## <a name="use-the-aspnet-core-shared-framework"></a>Utiliser le ASP.NET Core Framework partagé

Avec la sortie de .NET Core 3,0, de nombreux ASP.NET Core assemblys ne sont plus publiés dans NuGet en tant que packages. Au lieu de cela, les assemblys sont inclus dans le `Microsoft.AspNetCore.App` Framework partagé, qui est installé avec le kit SDK .net Core et les programmes d’installation du Runtime. Pour obtenir la liste des packages qui ne sont plus publiés, consultez [Supprimer les références de package obsolètes](xref:migration/22-to-30#remove-obsolete-package-references).

À compter de .NET Core 3,0, les projets qui utilisent le `Microsoft.NET.Sdk.Web` Kit de développement logiciel (SDK) MSBuild référencent implicitement le Framework partagé. Les projets qui utilisent le `Microsoft.NET.Sdk` `Microsoft.NET.Sdk.Razor` Kit de développement logiciel (SDK) ou doivent référencer ASP.net Core pour utiliser des API ASP.net core dans le Framework partagé.

Pour référencer ASP.NET Core, ajoutez l' `<FrameworkReference>` élément suivant à votre fichier projet :

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj?highlight=8)]

Le fait de référencer ASP.NET Core de cette manière est pris en charge uniquement pour les projets ciblant .NET Core 3. x.

## <a name="include-no-locblazor-extensibility"></a>Inclure l' Blazor extensibilité

Blazor prend en charge webassembly (WASM) et les [modèles d’hébergement](xref:blazor/hosting-models)de serveur. À moins qu’il y ait une raison spécifique de ne pas le faire, une bibliothèque de [ Razor composants](xref:blazor/components/index) doit prendre en charge les deux modèles d’hébergement. Une Razor bibliothèque de composants doit utiliser le [Kit de développement logiciel (SDK) Razor Microsoft. net. SDK](xref:razor-pages/sdk).

### <a name="support-both-hosting-models"></a>Prendre en charge les modèles d’hébergement

Pour prendre en charge la Razor consommation des composants à partir des [Blazor Server](xref:blazor/hosting-models#blazor-server) projets et [ Blazor WASM](xref:blazor/hosting-models#blazor-webassembly) , utilisez les instructions suivantes pour votre éditeur.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Utilisez le modèle de projet **Razor bibliothèque de classes** . La case à cocher **pages de prise en charge et vues** du modèle doit être désélectionnée.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Exécutez la commande suivante dans le terminal intégré :

```dotnetcli
dotnet new razorclasslib
```

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

Utilisez le modèle de projet **Razor bibliothèque de classes** .

---

Le projet généré à partir du modèle effectue les opérations suivantes :

* Cible .NET Standard 2,0.
* Affecte la valeur `RazorLangVersion` à la propriété `3.0`. `3.0` est la valeur par défaut pour .NET Core 3. x.
* Ajoute les références de package suivantes :
  * [Microsoft. AspNetCore. Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components)
  * [Microsoft. AspNetCore. Components. Web](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web)

Par exemple :

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-components-library.csproj)]

### <a name="support-a-specific-hosting-model"></a>Prendre en charge un modèle d’hébergement spécifique

La prise en charge d’un modèle d’hébergement unique est beaucoup moins courante Blazor . Par exemple, pour prendre en charge Razor la consommation de composants à partir de [Blazor Server](xref:blazor/hosting-models#blazor-server) projets uniquement :

* Ciblez .NET Core 3. x.
* Ajoutez un `<FrameworkReference>` élément pour le Framework partagé.

Par exemple :

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-components-library.csproj)]

Pour plus d’informations sur les bibliothèques contenant des Razor composants, consultez [ASP.net Core les bibliothèques de Razor classes](xref:blazor/components/class-libraries).

## <a name="include-mvc-extensibility"></a>Inclure l’extensibilité MVC

Cette section décrit les recommandations pour les bibliothèques qui incluent :

* [Razor vues ou Razor pages](#razor-views-or-razor-pages)
* [Tag Helpers](#tag-helpers)
* [Composants de vue](#view-components)

Cette section n’aborde pas le multi-ciblage pour prendre en charge plusieurs versions de MVC. Pour obtenir des conseils sur la prise en charge de plusieurs versions de ASP.NET Core, consultez [prise en charge de plusieurs versions de ASP.net Core](#support-multiple-aspnet-core-versions).

### <a name="no-locrazor-views-or-no-locrazor-pages"></a>Razor vues ou Razor pages

Un projet qui comprend des [ Razor affichages](xref:mvc/views/overview) ou des [ Razor pages](xref:razor-pages/index) doit utiliser le [Kit de développement logiciel (SDK) Razor Microsoft. net. SDK](xref:razor-pages/sdk).

Si le projet cible .NET Core 3. x, il requiert :

* `AddRazorSupportForMvc`Propriété MSBuild ayant la valeur `true` .
* `<FrameworkReference>`Élément pour le Framework partagé.

Le modèle de projet **Razor bibliothèque de classes** remplit les conditions précédentes pour les projets ciblant .net Core 3. x. Utilisez les instructions suivantes pour votre éditeur.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Utilisez le modèle de projet **Razor bibliothèque de classes** . La case à cocher **pages de prise en charge et vues** du modèle doit être activée.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Exécutez la commande suivante dans le terminal intégré :

```dotnetcli
dotnet new razorclasslib -s
```

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

Aucun modèle de projet n’est pris en charge pour le moment.

---

Par exemple :

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-views-pages-library.csproj)]

Si le projet cible .NET Standard à la place, une référence de package [Microsoft. AspNetCore. Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) est requise. Le `Microsoft.AspNetCore.Mvc` package a été déplacé dans le Framework partagé dans ASP.NET Core 3,0 et n’est donc plus publié. Par exemple :

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-views-pages-library.csproj?highlight=8)]

### <a name="tag-helpers"></a>Tag Helpers

Le kit de développement logiciel (SDK) doit être utilisé dans un projet qui comprend des [tag Helper](xref:mvc/views/tag-helpers/intro) `Microsoft.NET.Sdk` . Si vous ciblez .NET Core 3. x, ajoutez un `<FrameworkReference>` élément pour le Framework partagé. Par exemple :

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

Si vous ciblez .NET Standard (pour prendre en charge les versions antérieures à ASP.NET Core 3. x), ajoutez une référence de package à [Microsoft. AspNetCore. Mvc. Razor ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor) Le `Microsoft.AspNetCore.Mvc.Razor` package a été déplacé dans le Framework partagé et n’est donc plus publié. Par exemple :

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-tag-helpers-library.csproj)]

### <a name="view-components"></a>Composants de vue

Un projet qui comprend des [composants de vue](xref:mvc/views/view-components) doit utiliser le kit de `Microsoft.NET.Sdk` développement logiciel (SDK). Si vous ciblez .NET Core 3. x, ajoutez un `<FrameworkReference>` élément pour le Framework partagé. Par exemple :

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

Si vous ciblez .NET Standard (pour prendre en charge les versions antérieures à ASP.NET Core 3. x), ajoutez une référence de package à [Microsoft. AspNetCore. Mvc. ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures). Le `Microsoft.AspNetCore.Mvc.ViewFeatures` package a été déplacé dans le Framework partagé et n’est donc plus publié. Par exemple :

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-view-components-library.csproj)]

## <a name="support-multiple-aspnet-core-versions"></a>Prendre en charge plusieurs versions de ASP.NET Core

Le multi-ciblage est nécessaire pour créer une bibliothèque qui prend en charge plusieurs variantes de ASP.NET Core. Imaginez un scénario dans lequel une bibliothèque d’aide pour les balises doit prendre en charge les variantes de ASP.NET Core suivantes :

* ASP.NET Core 2,1 ciblant .NET Framework 4.6.1
* ASP.NET Core 2. x ciblant .NET Core 2. x
* ASP.NET Core 3. x ciblant .NET Core 3. x

Le fichier projet suivant prend en charge ces variantes via la `TargetFrameworks` propriété :

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

Avec le fichier projet précédent :

* Le `Markdig` package est ajouté pour tous les consommateurs.
* Référence à [Microsoft. AspNetCore. Mvc. Razor ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor) est ajouté pour les consommateurs ciblant .NET Framework 4.6.1 ou version ultérieure ou .NET Core 2. x. La version 2.1.0 du package fonctionne avec ASP.NET Core 2,2 en raison d’une compatibilité descendante.
* L’infrastructure partagée est référencée pour les consommateurs ciblant .NET Core 3. x. Le `Microsoft.AspNetCore.Mvc.Razor` package est inclus dans le Framework partagé.

.NET Standard 2,0 peut également être ciblé au lieu de cibler à la fois .NET Core 2,1 et .NET Framework 4.6.1 :

[!code-xml[](target-aspnetcore/samples/multi-tfm/alternative-tag-helpers-library.csproj?highlight=4)]

Avec le fichier projet précédent, les avertissements suivants existent :

* Étant donné que la bibliothèque ne contient que des balises d’aide, il est plus simple de cibler les plateformes spécifiques sur lesquelles ASP.NET Core s’exécute : .NET Core et .NET Framework. Les tag helpers ne peuvent pas être utilisés par d’autres frameworks cibles compatibles .NET Standard 2,0 tels que Unity, UWP et Xamarin.
* L’utilisation de .NET Standard 2.0 dans .NET Framework entraîne certains problèmes qui ont été résolus dans .NET Framework 4.7.2. Vous pouvez améliorer l’expérience des consommateurs à l’aide de .NET Framework 4.6.1 à 4.7.1 en ciblant .NET Framework 4.6.1.

Si votre bibliothèque doit appeler des API spécifiques à la plateforme, ciblez des implémentations .NET spécifiques au lieu de .NET Standard. Pour plus d’informations, consultez [multi-ciblage](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting).

## <a name="use-an-api-that-hasnt-changed"></a>Utiliser une API qui n’a pas changé

Imaginez un scénario dans lequel vous effectuez la mise à niveau d’une bibliothèque middleware de .NET Core 2,2 vers 3,0. Les API de ASP.NET Core middleware utilisées dans la bibliothèque n’ont pas changé entre ASP.NET Core 2,2 et 3,0. Pour continuer à prendre en charge la bibliothèque middleware dans .NET Core 3,0, procédez comme suit :

* Suivez les [instructions de la bibliothèque standard](/dotnet/standard/library-guidance/).
* Ajoutez une référence de package pour chaque package NuGet de l’API si l’assembly correspondant n’existe pas dans le Framework partagé.

## <a name="use-an-api-that-changed"></a>Utiliser une API qui a changé

Imaginez un scénario dans lequel vous effectuez la mise à niveau d’une bibliothèque de .NET Core 2,2 vers .NET Core 3,0. Une API ASP.NET Core en cours d’utilisation dans la bibliothèque a une [modification avec rupture](/dotnet/core/compatibility/breaking-changes) dans ASP.net Core 3,0. Déterminez si la bibliothèque peut être réécrite de façon à ne pas utiliser l’API endommagée dans toutes les versions.

Si vous pouvez réécrire la bibliothèque, faites-le et continuez à cibler un Framework cible antérieur (par exemple, .NET Standard 2,0 ou .NET Framework 4.6.1) avec des références de package.

Si vous ne pouvez pas réécrire la bibliothèque, procédez comme suit :

* Ajoutez une cible pour .NET Core 3,0.
* Ajoutez un `<FrameworkReference>` élément pour le Framework partagé.
* Utilisez la [directive de préprocesseur #if](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) avec le symbole de Framework cible approprié pour compiler le code de façon conditionnelle.

Par exemple, les lectures et écritures synchrones sur les flux de requête et de réponse HTTP sont désactivées par défaut à partir de ASP.NET Core 3,0. ASP.NET Core 2,2 prend en charge le comportement synchrone par défaut. Prenons l’exemple d’une bibliothèque middleware dans laquelle les lectures et écritures synchrones doivent être activées là où l’e/s se produit. La bibliothèque doit encadrer le code pour activer les fonctionnalités synchrones dans la directive de préprocesseur appropriée. Par exemple :

[!code-csharp[](target-aspnetcore/samples/middleware.cs?highlight=9-24)]

## <a name="use-an-api-introduced-in-30"></a>Utiliser une API introduite dans 3,0

Imaginez que vous souhaitez utiliser une API ASP.NET Core qui a été introduite dans ASP.NET Core 3,0. Considérez les questions suivantes :

1. La bibliothèque a-t-elle besoin de la nouvelle API ?
1. La bibliothèque peut-elle implémenter cette fonctionnalité de manière différente ?

Si la bibliothèque requiert un fonctionnement de l’API et qu’il n’existe aucun moyen de l’implémenter de manière descendante :

* Cible .NET Core 3. x uniquement.
* Ajoutez un `<FrameworkReference>` élément pour le Framework partagé.

Si la bibliothèque peut implémenter la fonctionnalité d’une autre façon :

* Ajoutez .NET Core 3. x comme Framework cible.
* Ajoutez un `<FrameworkReference>` élément pour le Framework partagé.
* Utilisez la [directive de préprocesseur #if](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) avec le symbole de Framework cible approprié pour compiler le code de façon conditionnelle.

Par exemple, le tag Helper suivant utilise l' <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> interface introduite dans ASP.NET Core 3,0. Les consommateurs ciblant .NET Core 3,0 exécutent le chemin de code défini par le `NETCOREAPP3_0` symbole de Framework cible. Le type de paramètre du constructeur de tag Helper devient <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> pour .net Core 2,1 et .NET Framework 4.6.1 Consumers. Cette modification était nécessaire, car ASP.NET Core 3,0 marquée `IHostingEnvironment` comme obsolète et recommandée `IWebHostEnvironment` comme remplacement.

```csharp
[HtmlTargetElement("script", Attributes = "asp-inline")]
public class ScriptInliningTagHelper : TagHelper
{
    private readonly IFileProvider _wwwroot;

#if NETCOREAPP3_0
    public ScriptInliningTagHelper(IWebHostEnvironment env)
#else
    public ScriptInliningTagHelper(IHostingEnvironment env)
#endif
    {
        _wwwroot = env.WebRootFileProvider;
    }

    // code omitted for brevity
}
```

Le fichier de projet multi-ciblé suivant prend en charge ce scénario d’assistance de balise :

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

## <a name="use-an-api-removed-from-the-shared-framework"></a>Utiliser une API supprimée de l’infrastructure partagée

Pour utiliser un assembly ASP.NET Core qui a été supprimé de l’infrastructure partagée, ajoutez la référence de package appropriée. Pour obtenir la liste des packages supprimés de l’infrastructure partagée dans ASP.NET Core 3,0, consultez [Supprimer les références de package obsolètes](xref:migration/22-to-30#remove-obsolete-package-references).

Par exemple, pour ajouter le client d’API Web :

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <FrameworkReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNet.WebApi.Client" Version="5.2.7" />
  </ItemGroup>

</Project>
```

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:razor-pages/ui-class>
* <xref:blazor/components/class-libraries>
* [Prise en charge des implémentations de .NET](/dotnet/standard/net-standard#net-implementation-support)
* [Stratégies de support .NET](https://dotnet.microsoft.com/platform/support/policy)
