---
title: ':::no-loc(Razor)::: compilation de fichiers dans ASP.NET Core'
author: rick-anderson
description: 'Découvrez comment la compilation des :::no-loc(Razor)::: fichiers se produit dans une application ASP.net core.'
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: mvc/views/view-compilation
ms.openlocfilehash: 77ca96b329136ee044ab6fc5f6b5ebb5b67fe64c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059076"
---
# <a name="no-locrazor-file-compilation-in-aspnet-core"></a><span data-ttu-id="37845-103">:::no-loc(Razor)::: compilation de fichiers dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="37845-103">:::no-loc(Razor)::: file compilation in ASP.NET Core</span></span>

<span data-ttu-id="37845-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="37845-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="37845-105">:::no-loc(Razor):::les fichiers avec une extension *. cshtml* sont compilés au moment de la génération et de la publication à l’aide du [ :::no-loc(Razor)::: Kit de développement logiciel (SDK)](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="37845-105">:::no-loc(Razor)::: files with a *.cshtml* extension are compiled at both build and publish time using the [:::no-loc(Razor)::: SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="37845-106">La compilation du runtime peut éventuellement être activée en configurant votre projet.</span><span class="sxs-lookup"><span data-stu-id="37845-106">Runtime compilation may be optionally enabled by configuring your project.</span></span>

## <a name="no-locrazor-compilation"></a><span data-ttu-id="37845-107">:::no-loc(Razor)::: élaboration</span><span class="sxs-lookup"><span data-stu-id="37845-107">:::no-loc(Razor)::: compilation</span></span>

<span data-ttu-id="37845-108">La compilation des fichiers au moment de la génération et de :::no-loc(Razor)::: la publication est activée par défaut par le :::no-loc(Razor)::: Kit de développement logiciel (SDK).</span><span class="sxs-lookup"><span data-stu-id="37845-108">Build-time and publish-time compilation of :::no-loc(Razor)::: files is enabled by default by the :::no-loc(Razor)::: SDK.</span></span> <span data-ttu-id="37845-109">Lorsqu’elle est activée, la compilation du runtime complète la compilation au moment de la génération, ce qui permet la :::no-loc(Razor)::: mise à jour des fichiers si elles sont modifiées.</span><span class="sxs-lookup"><span data-stu-id="37845-109">When enabled, runtime compilation complements build-time compilation, allowing :::no-loc(Razor)::: files to be updated if they're edited.</span></span>

## <a name="enable-runtime-compilation-at-project-creation"></a><span data-ttu-id="37845-110">Activer la compilation du Runtime au moment de la création du projet</span><span class="sxs-lookup"><span data-stu-id="37845-110">Enable runtime compilation at project creation</span></span>

<span data-ttu-id="37845-111">Les :::no-loc(Razor)::: pages et les modèles de projet MVC incluent une option permettant d’activer la compilation au moment de la création du projet.</span><span class="sxs-lookup"><span data-stu-id="37845-111">The :::no-loc(Razor)::: Pages and MVC project templates include an option to enable runtime compilation when the project is created.</span></span> <span data-ttu-id="37845-112">Cette option est prise en charge dans ASP.NET Core 3,1 et versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="37845-112">This option is supported in ASP.NET Core 3.1 and later.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="37845-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="37845-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="37845-114">Dans la boîte de dialogue **créer une nouvelle application web ASP.net Core** :</span><span class="sxs-lookup"><span data-stu-id="37845-114">In the **Create a new ASP.NET Core web application** dialog:</span></span>

1. <span data-ttu-id="37845-115">Sélectionnez l' **application Web** ou le modèle de projet **application Web (Model-View-Controller)** .</span><span class="sxs-lookup"><span data-stu-id="37845-115">Select either the **Web Application** or the **Web Application (Model-View-Controller)** project template.</span></span>
1. <span data-ttu-id="37845-116">Activez la case à cocher **activer la :::no-loc(Razor)::: compilation au moment** de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="37845-116">Select the **Enable :::no-loc(Razor)::: runtime compilation** check box.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="37845-117">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="37845-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="37845-118">Utilisez l' `-rrc` `--razor-runtime-compilation` option de modèle ou.</span><span class="sxs-lookup"><span data-stu-id="37845-118">Use the `-rrc` or `--razor-runtime-compilation` template option.</span></span> <span data-ttu-id="37845-119">Par exemple, la commande suivante crée un :::no-loc(Razor)::: projet de pages avec la compilation d’exécution activée :</span><span class="sxs-lookup"><span data-stu-id="37845-119">For example, the following command creates a new :::no-loc(Razor)::: Pages project with runtime compilation enabled:</span></span>

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="37845-120">Activer la compilation au moment de l’exécution dans un projet existant</span><span class="sxs-lookup"><span data-stu-id="37845-120">Enable runtime compilation in an existing project</span></span>

<span data-ttu-id="37845-121">Pour activer la compilation au moment de l’exécution pour tous les environnements d’un projet existant :</span><span class="sxs-lookup"><span data-stu-id="37845-121">To enable runtime compilation for all environments in an existing project:</span></span>

1. <span data-ttu-id="37845-122">Installez [Microsoft. AspNetCore. Mvc. :::no-loc(Razor)::: . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation/) Package NuGet RuntimeCompilation.</span><span class="sxs-lookup"><span data-stu-id="37845-122">Install the [Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="37845-123">Mettez à jour la `Startup.ConfigureServices` méthode du projet pour inclure un appel à <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Razor):::RuntimeCompilationMvcBuilderExtensions.Add:::no-loc(Razor):::RuntimeCompilation*> .</span><span class="sxs-lookup"><span data-stu-id="37845-123">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Razor):::RuntimeCompilationMvcBuilderExtensions.Add:::no-loc(Razor):::RuntimeCompilation*>.</span></span> <span data-ttu-id="37845-124">Exemple :</span><span class="sxs-lookup"><span data-stu-id="37845-124">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Add:::no-loc(Razor):::Pages()
            .Add:::no-loc(Razor):::RuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="37845-125">Activer la compilation d’exécution de manière conditionnelle dans un projet existant</span><span class="sxs-lookup"><span data-stu-id="37845-125">Conditionally enable runtime compilation in an existing project</span></span>

<span data-ttu-id="37845-126">La compilation au moment de l’exécution peut être activée de sorte qu’elle ne soit disponible que pour le développement local.</span><span class="sxs-lookup"><span data-stu-id="37845-126">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="37845-127">L’activation conditionnelle de cette manière garantit que la sortie publiée :</span><span class="sxs-lookup"><span data-stu-id="37845-127">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="37845-128">Utilise des vues compilées.</span><span class="sxs-lookup"><span data-stu-id="37845-128">Uses compiled views.</span></span>
* <span data-ttu-id="37845-129">N’active pas les observateurs de fichiers en production.</span><span class="sxs-lookup"><span data-stu-id="37845-129">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="37845-130">Pour activer la compilation au moment de l’exécution uniquement dans l’environnement de développement :</span><span class="sxs-lookup"><span data-stu-id="37845-130">To enable runtime compilation only in the Development environment:</span></span>

1. <span data-ttu-id="37845-131">Installez [Microsoft. AspNetCore. Mvc. :::no-loc(Razor)::: . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation/) Package NuGet RuntimeCompilation.</span><span class="sxs-lookup"><span data-stu-id="37845-131">Install the [Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="37845-132">Modifiez la section Launch Profile `environmentVariables` dans *launchSettings.jssur* :</span><span class="sxs-lookup"><span data-stu-id="37845-132">Modify the launch profile `environmentVariables` section in *launchSettings.json* :</span></span>
    * <span data-ttu-id="37845-133">Vérifiez que `ASPNETCORE_ENVIRONMENT` a la valeur `"Development"` .</span><span class="sxs-lookup"><span data-stu-id="37845-133">Verify `ASPNETCORE_ENVIRONMENT` is set to `"Development"`.</span></span>
    * <span data-ttu-id="37845-134">Affectez la valeur `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` à `"Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation"`.</span><span class="sxs-lookup"><span data-stu-id="37845-134">Set `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` to `"Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation"`.</span></span>

<span data-ttu-id="37845-135">Dans l’exemple suivant, la compilation au moment de l’exécution est activée dans l’environnement de développement pour les `IIS Express` `:::no-loc(Razor):::PagesApp` profils de lancement et :</span><span class="sxs-lookup"><span data-stu-id="37845-135">In the following example, runtime compilation is enabled in the Development environment for the `IIS Express` and `:::no-loc(Razor):::PagesApp` launch profiles:</span></span>

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

<span data-ttu-id="37845-136">Aucune modification de code n’est nécessaire dans la `Startup` classe du projet.</span><span class="sxs-lookup"><span data-stu-id="37845-136">No code changes are needed in the project's `Startup` class.</span></span> <span data-ttu-id="37845-137">Au moment de l’exécution, ASP.NET Core recherche un [attribut HostingStartup au niveau de l’assembly](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) dans `Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation` .</span><span class="sxs-lookup"><span data-stu-id="37845-137">At runtime, ASP.NET Core searches for an [assembly-level HostingStartup attribute](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) in `Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation`.</span></span> <span data-ttu-id="37845-138">L' `HostingStartup` attribut spécifie le code de démarrage de l’application à exécuter.</span><span class="sxs-lookup"><span data-stu-id="37845-138">The `HostingStartup` attribute specifies the app startup code to execute.</span></span> <span data-ttu-id="37845-139">Ce code de démarrage permet la compilation au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="37845-139">That startup code enables runtime compilation.</span></span>

## <a name="enable-runtime-compilation-for-a-no-locrazor-class-library"></a><span data-ttu-id="37845-140">Activer la compilation au moment de l’exécution pour une :::no-loc(Razor)::: bibliothèque de classes</span><span class="sxs-lookup"><span data-stu-id="37845-140">Enable runtime compilation for a :::no-loc(Razor)::: Class Library</span></span>

<span data-ttu-id="37845-141">Imaginez un scénario dans lequel un :::no-loc(Razor)::: projet de pages fait référence à une [ :::no-loc(Razor)::: bibliothèque de classes (RCL)](xref:razor-pages/ui-class) nommée *MyClassLib* .</span><span class="sxs-lookup"><span data-stu-id="37845-141">Consider a scenario in which a :::no-loc(Razor)::: Pages project references a [:::no-loc(Razor)::: Class Library (RCL)](xref:razor-pages/ui-class) named *MyClassLib* .</span></span> <span data-ttu-id="37845-142">Le RCL contient un fichier *_Layout. cshtml* que tous les projets MVC et pages de votre équipe :::no-loc(Razor)::: consomment.</span><span class="sxs-lookup"><span data-stu-id="37845-142">The RCL contains a *_Layout.cshtml* file that all of your team's MVC and :::no-loc(Razor)::: Pages projects consume.</span></span> <span data-ttu-id="37845-143">Vous souhaitez activer la compilation au moment de l’exécution pour le fichier *_Layout. cshtml* dans ce RCL.</span><span class="sxs-lookup"><span data-stu-id="37845-143">You want to enable runtime compilation for the *_Layout.cshtml* file in that RCL.</span></span> <span data-ttu-id="37845-144">Apportez les modifications suivantes dans le :::no-loc(Razor)::: projet pages :</span><span class="sxs-lookup"><span data-stu-id="37845-144">Make the following changes in the :::no-loc(Razor)::: Pages project:</span></span>

1. <span data-ttu-id="37845-145">Activez la compilation au moment de l’exécution avec les instructions en [activant de manière conditionnelle la compilation au moment de l’exécution dans un projet existant](#conditionally-enable-runtime-compilation-in-an-existing-project).</span><span class="sxs-lookup"><span data-stu-id="37845-145">Enable runtime compilation with the instructions at [Conditionally enable runtime compilation in an existing project](#conditionally-enable-runtime-compilation-in-an-existing-project).</span></span>
1. <span data-ttu-id="37845-146">Configurez les options de compilation du runtime dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="37845-146">Configure the runtime compilation options in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.1/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

    <span data-ttu-id="37845-147">Dans le code précédent, un chemin d’accès absolu au RCL *MyClassLib* est construit.</span><span class="sxs-lookup"><span data-stu-id="37845-147">In the preceding code, an absolute path to the *MyClassLib* RCL is constructed.</span></span> <span data-ttu-id="37845-148">L' [API PhysicalFileProvider](xref:fundamentals/file-providers#physicalfileprovider) est utilisée pour localiser des répertoires et des fichiers au niveau de ce chemin d’accès absolu.</span><span class="sxs-lookup"><span data-stu-id="37845-148">The [PhysicalFileProvider API](xref:fundamentals/file-providers#physicalfileprovider) is used to locate directories and files at that absolute path.</span></span> <span data-ttu-id="37845-149">Enfin, l' `PhysicalFileProvider` instance est ajoutée à une collection de fournisseurs de fichiers, qui autorise l’accès aux fichiers *. cshtml* du RCL.</span><span class="sxs-lookup"><span data-stu-id="37845-149">Finally, the `PhysicalFileProvider` instance is added to a file providers collection, which allows access to the RCL's *.cshtml* files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="37845-150">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="37845-150">Additional resources</span></span>

* <span data-ttu-id="37845-151">Propriétés [ :::no-loc(Razor)::: CompileOnBuild et :::no-loc(Razor)::: CompileOnPublish](xref:razor-pages/sdk#properties) .</span><span class="sxs-lookup"><span data-stu-id="37845-151">[:::no-loc(Razor):::CompileOnBuild and :::no-loc(Razor):::CompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="37845-152">:::no-loc(Razor):::les fichiers avec une extension *. cshtml* sont compilés au moment de la génération et de la publication à l’aide du [ :::no-loc(Razor)::: Kit de développement logiciel (SDK)](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="37845-152">:::no-loc(Razor)::: files with a *.cshtml* extension are compiled at both build and publish time using the [:::no-loc(Razor)::: SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="37845-153">La compilation au moment de l’exécution peut éventuellement être activée en configurant votre application.</span><span class="sxs-lookup"><span data-stu-id="37845-153">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="no-locrazor-compilation"></a><span data-ttu-id="37845-154">:::no-loc(Razor)::: élaboration</span><span class="sxs-lookup"><span data-stu-id="37845-154">:::no-loc(Razor)::: compilation</span></span>

<span data-ttu-id="37845-155">La compilation des fichiers au moment de la génération et de :::no-loc(Razor)::: la publication est activée par défaut par le :::no-loc(Razor)::: Kit de développement logiciel (SDK).</span><span class="sxs-lookup"><span data-stu-id="37845-155">Build-time and publish-time compilation of :::no-loc(Razor)::: files is enabled by default by the :::no-loc(Razor)::: SDK.</span></span> <span data-ttu-id="37845-156">Lorsqu’elle est activée, la compilation du runtime complète la compilation au moment de la génération, ce qui permet la :::no-loc(Razor)::: mise à jour des fichiers si elles sont modifiées.</span><span class="sxs-lookup"><span data-stu-id="37845-156">When enabled, runtime compilation complements build-time compilation, allowing :::no-loc(Razor)::: files to be updated if they're edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="37845-157">Compilation du runtime</span><span class="sxs-lookup"><span data-stu-id="37845-157">Runtime compilation</span></span>

<span data-ttu-id="37845-158">Pour activer la compilation au moment de l’exécution pour tous les environnements et modes de configuration :</span><span class="sxs-lookup"><span data-stu-id="37845-158">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="37845-159">Installez [Microsoft. AspNetCore. Mvc. :::no-loc(Razor)::: . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation/) Package NuGet RuntimeCompilation.</span><span class="sxs-lookup"><span data-stu-id="37845-159">Install the [Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="37845-160">Mettez à jour la `Startup.ConfigureServices` méthode du projet pour inclure un appel à <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Razor):::RuntimeCompilationMvcBuilderExtensions.Add:::no-loc(Razor):::RuntimeCompilation*> .</span><span class="sxs-lookup"><span data-stu-id="37845-160">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Razor):::RuntimeCompilationMvcBuilderExtensions.Add:::no-loc(Razor):::RuntimeCompilation*>.</span></span> <span data-ttu-id="37845-161">Exemple :</span><span class="sxs-lookup"><span data-stu-id="37845-161">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Add:::no-loc(Razor):::Pages()
            .Add:::no-loc(Razor):::RuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="37845-162">Activation conditionnelle de la compilation du Runtime</span><span class="sxs-lookup"><span data-stu-id="37845-162">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="37845-163">La compilation au moment de l’exécution peut être activée de sorte qu’elle ne soit disponible que pour le développement local.</span><span class="sxs-lookup"><span data-stu-id="37845-163">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="37845-164">L’activation conditionnelle de cette manière garantit que la sortie publiée :</span><span class="sxs-lookup"><span data-stu-id="37845-164">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="37845-165">Utilise des vues compilées.</span><span class="sxs-lookup"><span data-stu-id="37845-165">Uses compiled views.</span></span>
* <span data-ttu-id="37845-166">Est de taille inférieure.</span><span class="sxs-lookup"><span data-stu-id="37845-166">Is smaller in size.</span></span>
* <span data-ttu-id="37845-167">N’active pas les observateurs de fichiers en production.</span><span class="sxs-lookup"><span data-stu-id="37845-167">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="37845-168">Pour activer la compilation au moment de l’exécution en fonction de l’environnement et du mode de configuration :</span><span class="sxs-lookup"><span data-stu-id="37845-168">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="37845-169">Référencez de manière conditionnelle le [Microsoft. AspNetCore. Mvc. :::no-loc(Razor)::: . Package RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation/) basé sur la `Configuration` valeur active :</span><span class="sxs-lookup"><span data-stu-id="37845-169">Conditionally reference the [Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="37845-170">Mettez à jour la `Startup.ConfigureServices` méthode du projet pour inclure un appel à `Add:::no-loc(Razor):::RuntimeCompilation` .</span><span class="sxs-lookup"><span data-stu-id="37845-170">Update the project's `Startup.ConfigureServices` method to include a call to `Add:::no-loc(Razor):::RuntimeCompilation`.</span></span> <span data-ttu-id="37845-171">Exécuter `Add:::no-loc(Razor):::RuntimeCompilation` de manière conditionnelle, de telle sorte qu’elle s’exécute uniquement en mode débogage lorsque la `ASPNETCORE_ENVIRONMENT` variable est définie sur `Development` :</span><span class="sxs-lookup"><span data-stu-id="37845-171">Conditionally execute `Add:::no-loc(Razor):::RuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="37845-172">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="37845-172">Additional resources</span></span>

* <span data-ttu-id="37845-173">Propriétés [ :::no-loc(Razor)::: CompileOnBuild et :::no-loc(Razor)::: CompileOnPublish](xref:razor-pages/sdk#properties) .</span><span class="sxs-lookup"><span data-stu-id="37845-173">[:::no-loc(Razor):::CompileOnBuild and :::no-loc(Razor):::CompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="37845-174">Consultez l' [exemple de compilation du Runtime sur GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) pour obtenir un exemple qui montre comment utiliser la compilation d’exécution entre les projets.</span><span class="sxs-lookup"><span data-stu-id="37845-174">See the [runtime compilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="37845-175">Un :::no-loc(Razor)::: fichier est compilé au moment de l’exécution, lorsque la :::no-loc(Razor)::: page associée ou la vue MVC est appelée.</span><span class="sxs-lookup"><span data-stu-id="37845-175">A :::no-loc(Razor)::: file is compiled at runtime, when the associated :::no-loc(Razor)::: Page or MVC view is invoked.</span></span> <span data-ttu-id="37845-176">:::no-loc(Razor):::les fichiers sont compilés au moment de la génération et de la publication à l’aide du [ :::no-loc(Razor)::: Kit de développement logiciel](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="37845-176">:::no-loc(Razor)::: files are compiled at both build and publish time using the [:::no-loc(Razor)::: SDK](xref:razor-pages/sdk).</span></span>

## <a name="no-locrazor-compilation"></a><span data-ttu-id="37845-177">:::no-loc(Razor)::: élaboration</span><span class="sxs-lookup"><span data-stu-id="37845-177">:::no-loc(Razor)::: compilation</span></span>

<span data-ttu-id="37845-178">La compilation des fichiers Build-and-Publish-Time :::no-loc(Razor)::: est activée par défaut par le :::no-loc(Razor)::: Kit de développement logiciel (SDK).</span><span class="sxs-lookup"><span data-stu-id="37845-178">Build- and publish-time compilation of :::no-loc(Razor)::: files is enabled by default by the :::no-loc(Razor)::: SDK.</span></span> <span data-ttu-id="37845-179">La modification des :::no-loc(Razor)::: fichiers après leur mise à jour est prise en charge au moment de la génération.</span><span class="sxs-lookup"><span data-stu-id="37845-179">Editing :::no-loc(Razor)::: files after they're updated is supported at build time.</span></span> <span data-ttu-id="37845-180">Par défaut, seuls le *Views.dll* compilé et aucun fichier *. cshtml* ou référence les assemblys requis pour compiler les :::no-loc(Razor)::: fichiers sont déployés avec votre application.</span><span class="sxs-lookup"><span data-stu-id="37845-180">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile :::no-loc(Razor)::: files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="37845-181">L’outil de précompilation est désormais déprécié et sera supprimé dans ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="37845-181">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="37845-182">Nous vous recommandons de migrer vers le [ :::no-loc(Razor)::: SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="37845-182">We recommend migrating to [:::no-loc(Razor)::: Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="37845-183">Le :::no-loc(Razor)::: Kit de développement logiciel (SDK) est effectif uniquement quand aucune propriété spécifique à la précompilation n’est définie dans le fichier projet.</span><span class="sxs-lookup"><span data-stu-id="37845-183">The :::no-loc(Razor)::: SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="37845-184">Par exemple, la définition de la propriété du fichier *. csproj* `Mvc:::no-loc(Razor):::CompileOnPublish` pour `true` désactive le :::no-loc(Razor)::: Kit de développement logiciel (SDK).</span><span class="sxs-lookup"><span data-stu-id="37845-184">For instance, setting the *.csproj* file's `Mvc:::no-loc(Razor):::CompileOnPublish` property to `true` disables the :::no-loc(Razor)::: SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="37845-185">Compilation du runtime</span><span class="sxs-lookup"><span data-stu-id="37845-185">Runtime compilation</span></span>

<span data-ttu-id="37845-186">La compilation au moment de la génération est complétée par la compilation des fichiers au moment de l’exécution :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="37845-186">Build-time compilation is supplemented by runtime compilation of :::no-loc(Razor)::: files.</span></span> <span data-ttu-id="37845-187">ASP.NET Core MVC RECOMPILE :::no-loc(Razor)::: les fichiers lorsque le contenu d’un fichier *. cshtml* est modifié.</span><span class="sxs-lookup"><span data-stu-id="37845-187">ASP.NET Core MVC will recompile :::no-loc(Razor)::: files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="37845-188">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="37845-188">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
