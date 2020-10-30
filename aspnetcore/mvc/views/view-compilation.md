---
title: Razor compilation de fichiers dans ASP.NET Core
author: rick-anderson
description: Découvrez comment la compilation des Razor fichiers se produit dans une application ASP.net core.
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
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
uid: mvc/views/view-compilation
ms.openlocfilehash: 77ca96b329136ee044ab6fc5f6b5ebb5b67fe64c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059076"
---
# <a name="no-locrazor-file-compilation-in-aspnet-core"></a>Razor compilation de fichiers dans ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.1"

Razorles fichiers avec une extension *. cshtml* sont compilés au moment de la génération et de la publication à l’aide du [ Razor Kit de développement logiciel (SDK)](xref:razor-pages/sdk). La compilation du runtime peut éventuellement être activée en configurant votre projet.

## <a name="no-locrazor-compilation"></a>Razor élaboration

La compilation des fichiers au moment de la génération et de Razor la publication est activée par défaut par le Razor Kit de développement logiciel (SDK). Lorsqu’elle est activée, la compilation du runtime complète la compilation au moment de la génération, ce qui permet la Razor mise à jour des fichiers si elles sont modifiées.

## <a name="enable-runtime-compilation-at-project-creation"></a>Activer la compilation du Runtime au moment de la création du projet

Les Razor pages et les modèles de projet MVC incluent une option permettant d’activer la compilation au moment de la création du projet. Cette option est prise en charge dans ASP.NET Core 3,1 et versions ultérieures.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Dans la boîte de dialogue **créer une nouvelle application web ASP.net Core** :

1. Sélectionnez l' **application Web** ou le modèle de projet **application Web (Model-View-Controller)** .
1. Activez la case à cocher **activer la Razor compilation au moment** de l’exécution.

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

Utilisez l' `-rrc` `--razor-runtime-compilation` option de modèle ou. Par exemple, la commande suivante crée un Razor projet de pages avec la compilation d’exécution activée :

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a>Activer la compilation au moment de l’exécution dans un projet existant

Pour activer la compilation au moment de l’exécution pour tous les environnements d’un projet existant :

1. Installez [Microsoft. AspNetCore. Mvc. Razor . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) Package NuGet RuntimeCompilation.
1. Mettez à jour la `Startup.ConfigureServices` méthode du projet pour inclure un appel à <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> . Exemple :

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a>Activer la compilation d’exécution de manière conditionnelle dans un projet existant

La compilation au moment de l’exécution peut être activée de sorte qu’elle ne soit disponible que pour le développement local. L’activation conditionnelle de cette manière garantit que la sortie publiée :

* Utilise des vues compilées.
* N’active pas les observateurs de fichiers en production.

Pour activer la compilation au moment de l’exécution uniquement dans l’environnement de développement :

1. Installez [Microsoft. AspNetCore. Mvc. Razor . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) Package NuGet RuntimeCompilation.
1. Modifiez la section Launch Profile `environmentVariables` dans *launchSettings.jssur* :
    * Vérifiez que `ASPNETCORE_ENVIRONMENT` a la valeur `"Development"` .
    * Affectez la valeur `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` à `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.

Dans l’exemple suivant, la compilation au moment de l’exécution est activée dans l’environnement de développement pour les `IIS Express` `RazorPagesApp` profils de lancement et :

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

Aucune modification de code n’est nécessaire dans la `Startup` classe du projet. Au moment de l’exécution, ASP.NET Core recherche un [attribut HostingStartup au niveau de l’assembly](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) dans `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` . L' `HostingStartup` attribut spécifie le code de démarrage de l’application à exécuter. Ce code de démarrage permet la compilation au moment de l’exécution.

## <a name="enable-runtime-compilation-for-a-no-locrazor-class-library"></a>Activer la compilation au moment de l’exécution pour une Razor bibliothèque de classes

Imaginez un scénario dans lequel un Razor projet de pages fait référence à une [ Razor bibliothèque de classes (RCL)](xref:razor-pages/ui-class) nommée *MyClassLib* . Le RCL contient un fichier *_Layout. cshtml* que tous les projets MVC et pages de votre équipe Razor consomment. Vous souhaitez activer la compilation au moment de l’exécution pour le fichier *_Layout. cshtml* dans ce RCL. Apportez les modifications suivantes dans le Razor projet pages :

1. Activez la compilation au moment de l’exécution avec les instructions en [activant de manière conditionnelle la compilation au moment de l’exécution dans un projet existant](#conditionally-enable-runtime-compilation-in-an-existing-project).
1. Configurez les options de compilation du runtime dans `Startup.ConfigureServices` :

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.1/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

    Dans le code précédent, un chemin d’accès absolu au RCL *MyClassLib* est construit. L' [API PhysicalFileProvider](xref:fundamentals/file-providers#physicalfileprovider) est utilisée pour localiser des répertoires et des fichiers au niveau de ce chemin d’accès absolu. Enfin, l' `PhysicalFileProvider` instance est ajoutée à une collection de fournisseurs de fichiers, qui autorise l’accès aux fichiers *. cshtml* du RCL.

## <a name="additional-resources"></a>Ressources supplémentaires

* Propriétés [ Razor CompileOnBuild et Razor CompileOnPublish](xref:razor-pages/sdk#properties) .
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

Razorles fichiers avec une extension *. cshtml* sont compilés au moment de la génération et de la publication à l’aide du [ Razor Kit de développement logiciel (SDK)](xref:razor-pages/sdk). La compilation au moment de l’exécution peut éventuellement être activée en configurant votre application.

## <a name="no-locrazor-compilation"></a>Razor élaboration

La compilation des fichiers au moment de la génération et de Razor la publication est activée par défaut par le Razor Kit de développement logiciel (SDK). Lorsqu’elle est activée, la compilation du runtime complète la compilation au moment de la génération, ce qui permet la Razor mise à jour des fichiers si elles sont modifiées.

## <a name="runtime-compilation"></a>Compilation du runtime

Pour activer la compilation au moment de l’exécution pour tous les environnements et modes de configuration :

1. Installez [Microsoft. AspNetCore. Mvc. Razor . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) Package NuGet RuntimeCompilation.

1. Mettez à jour la `Startup.ConfigureServices` méthode du projet pour inclure un appel à <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> . Exemple :

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a>Activation conditionnelle de la compilation du Runtime

La compilation au moment de l’exécution peut être activée de sorte qu’elle ne soit disponible que pour le développement local. L’activation conditionnelle de cette manière garantit que la sortie publiée :

* Utilise des vues compilées.
* Est de taille inférieure.
* N’active pas les observateurs de fichiers en production.

Pour activer la compilation au moment de l’exécution en fonction de l’environnement et du mode de configuration :

1. Référencez de manière conditionnelle le [Microsoft. AspNetCore. Mvc. Razor . Package RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) basé sur la `Configuration` valeur active :

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. Mettez à jour la `Startup.ConfigureServices` méthode du projet pour inclure un appel à `AddRazorRuntimeCompilation` . Exécuter `AddRazorRuntimeCompilation` de manière conditionnelle, de telle sorte qu’elle s’exécute uniquement en mode débogage lorsque la `ASPNETCORE_ENVIRONMENT` variable est définie sur `Development` :

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a>Ressources supplémentaires

* Propriétés [ Razor CompileOnBuild et Razor CompileOnPublish](xref:razor-pages/sdk#properties) .
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* Consultez l' [exemple de compilation du Runtime sur GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) pour obtenir un exemple qui montre comment utiliser la compilation d’exécution entre les projets.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Un Razor fichier est compilé au moment de l’exécution, lorsque la Razor page associée ou la vue MVC est appelée. Razorles fichiers sont compilés au moment de la génération et de la publication à l’aide du [ Razor Kit de développement logiciel](xref:razor-pages/sdk).

## <a name="no-locrazor-compilation"></a>Razor élaboration

La compilation des fichiers Build-and-Publish-Time Razor est activée par défaut par le Razor Kit de développement logiciel (SDK). La modification des Razor fichiers après leur mise à jour est prise en charge au moment de la génération. Par défaut, seuls le *Views.dll* compilé et aucun fichier *. cshtml* ou référence les assemblys requis pour compiler les Razor fichiers sont déployés avec votre application.

> [!IMPORTANT]
> L’outil de précompilation est désormais déprécié et sera supprimé dans ASP.NET Core 3.0. Nous vous recommandons de migrer vers le [ Razor SDK](xref:razor-pages/sdk).
>
> Le Razor Kit de développement logiciel (SDK) est effectif uniquement quand aucune propriété spécifique à la précompilation n’est définie dans le fichier projet. Par exemple, la définition de la propriété du fichier *. csproj* `MvcRazorCompileOnPublish` pour `true` désactive le Razor Kit de développement logiciel (SDK).

## <a name="runtime-compilation"></a>Compilation du runtime

La compilation au moment de la génération est complétée par la compilation des fichiers au moment de l’exécution Razor . ASP.NET Core MVC RECOMPILE Razor les fichiers lorsque le contenu d’un fichier *. cshtml* est modifié.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
