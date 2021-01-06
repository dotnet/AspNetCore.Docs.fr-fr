---
title: RazorBibliothèques de classes des composants ASP.net Core
author: guardrex
description: Découvrez comment les composants peuvent être inclus dans des Blazor applications à partir d’une bibliothèque de composants externes.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/27/2020
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
uid: blazor/components/class-libraries
ms.openlocfilehash: 24a5b93a18cfe36c50d9739ba56d12aca41615c0
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "94570157"
---
# <a name="aspnet-core-no-locrazor-components-class-libraries"></a><span data-ttu-id="cd413-103">RazorBibliothèques de classes des composants ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="cd413-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="cd413-104">Par [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="cd413-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="cd413-105">Les composants peuvent être partagés dans une [ Razor bibliothèque de classes (RCL)](xref:razor-pages/ui-class) d’un projet à l’autre.</span><span class="sxs-lookup"><span data-stu-id="cd413-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="cd413-106">Vous pouvez inclure une *Razor bibliothèque de classes de composants* à partir de :</span><span class="sxs-lookup"><span data-stu-id="cd413-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="cd413-107">Un autre projet dans la solution.</span><span class="sxs-lookup"><span data-stu-id="cd413-107">Another project in the solution.</span></span>
* <span data-ttu-id="cd413-108">Package NuGet.</span><span class="sxs-lookup"><span data-stu-id="cd413-108">A NuGet package.</span></span>
* <span data-ttu-id="cd413-109">Bibliothèque .NET référencée.</span><span class="sxs-lookup"><span data-stu-id="cd413-109">A referenced .NET library.</span></span>

<span data-ttu-id="cd413-110">Tout comme les composants sont des types .NET standard, les composants fournis par un RCL sont des assemblys .NET normaux.</span><span class="sxs-lookup"><span data-stu-id="cd413-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="cd413-111">Créer un RCL</span><span class="sxs-lookup"><span data-stu-id="cd413-111">Create an RCL</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cd413-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cd413-112">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="cd413-113">Créez un projet.</span><span class="sxs-lookup"><span data-stu-id="cd413-113">Create a new project.</span></span>
1. <span data-ttu-id="cd413-114">Sélectionnez **Razor bibliothèque de classes**.</span><span class="sxs-lookup"><span data-stu-id="cd413-114">Select **Razor Class Library**.</span></span> <span data-ttu-id="cd413-115">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="cd413-115">Select **Next**.</span></span>
1. <span data-ttu-id="cd413-116">Dans la boîte de dialogue **créer une nouvelle Razor bibliothèque de classes** , sélectionnez **créer**.</span><span class="sxs-lookup"><span data-stu-id="cd413-116">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="cd413-117">Indiquez un nom de projet dans le champ **Nom du projet**, ou acceptez le nom de projet par défaut.</span><span class="sxs-lookup"><span data-stu-id="cd413-117">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="cd413-118">Les exemples de cette rubrique utilisent le nom du projet `ComponentLibrary` .</span><span class="sxs-lookup"><span data-stu-id="cd413-118">The examples in this topic use the project name `ComponentLibrary`.</span></span> <span data-ttu-id="cd413-119">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="cd413-119">Select **Create**.</span></span>
1. <span data-ttu-id="cd413-120">Ajouter RCL à une solution :</span><span class="sxs-lookup"><span data-stu-id="cd413-120">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="cd413-121">Cliquez avec le bouton droit sur la solution.</span><span class="sxs-lookup"><span data-stu-id="cd413-121">Right-click the solution.</span></span> <span data-ttu-id="cd413-122">Sélectionnez **Ajouter** > **un projet existant**.</span><span class="sxs-lookup"><span data-stu-id="cd413-122">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="cd413-123">Accédez au fichier projet de RCL.</span><span class="sxs-lookup"><span data-stu-id="cd413-123">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="cd413-124">Sélectionnez le fichier projet de RCL ( `.csproj` ).</span><span class="sxs-lookup"><span data-stu-id="cd413-124">Select the RCL's project file (`.csproj`).</span></span>
1. <span data-ttu-id="cd413-125">Ajoutez une référence à l’RCL à partir de l’application :</span><span class="sxs-lookup"><span data-stu-id="cd413-125">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="cd413-126">Cliquez avec le bouton droit sur le projet d’application.</span><span class="sxs-lookup"><span data-stu-id="cd413-126">Right-click the app project.</span></span> <span data-ttu-id="cd413-127">Sélectionnez **Ajouter** une  >  **référence**.</span><span class="sxs-lookup"><span data-stu-id="cd413-127">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="cd413-128">Sélectionnez le projet RCL.</span><span class="sxs-lookup"><span data-stu-id="cd413-128">Select the RCL project.</span></span> <span data-ttu-id="cd413-129">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="cd413-129">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="cd413-130">Si la case à cocher **pages et vues de support** est activée lors de la génération du RCL à partir du modèle, ajoutez également un `_Imports.razor` fichier à la racine du projet généré avec le contenu suivant pour activer la Razor création de composants :</span><span class="sxs-lookup"><span data-stu-id="cd413-130">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="cd413-131">Ajoutez manuellement le fichier à la racine du projet généré.</span><span class="sxs-lookup"><span data-stu-id="cd413-131">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="cd413-132">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="cd413-132">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="cd413-133">Utilisez le modèle de **Razor bibliothèque de classes** ( `razorclasslib` ) avec la [`dotnet new`](/dotnet/core/tools/dotnet-new) commande dans un interpréteur de commandes.</span><span class="sxs-lookup"><span data-stu-id="cd413-133">Use the **Razor Class Library** template (`razorclasslib`) with the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="cd413-134">Dans l’exemple suivant, un RCL est créé nommé `ComponentLibrary` .</span><span class="sxs-lookup"><span data-stu-id="cd413-134">In the following example, an RCL is created named `ComponentLibrary`.</span></span> <span data-ttu-id="cd413-135">Le dossier qui contient `ComponentLibrary` est créé automatiquement lors de l’exécution de la commande :</span><span class="sxs-lookup"><span data-stu-id="cd413-135">The folder that holds `ComponentLibrary` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o ComponentLibrary
   ```

   > [!NOTE]
   > <span data-ttu-id="cd413-136">Si le `-s|--support-pages-and-views` commutateur est utilisé lors de la génération du RCL à partir du modèle, ajoutez également un `_Imports.razor` fichier à la racine du projet généré avec le contenu suivant pour activer la Razor création de composants :</span><span class="sxs-lookup"><span data-stu-id="cd413-136">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="cd413-137">Ajoutez manuellement le fichier à la racine du projet généré.</span><span class="sxs-lookup"><span data-stu-id="cd413-137">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="cd413-138">Pour ajouter la bibliothèque à un projet existant, utilisez la [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) commande dans un interpréteur de commandes.</span><span class="sxs-lookup"><span data-stu-id="cd413-138">To add the library to an existing project, use the [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="cd413-139">Dans l’exemple suivant, le RCL est ajouté à l’application.</span><span class="sxs-lookup"><span data-stu-id="cd413-139">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="cd413-140">Exécutez la commande suivante à partir du dossier du projet de l’application avec le chemin d’accès à la bibliothèque :</span><span class="sxs-lookup"><span data-stu-id="cd413-140">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="cd413-141">Consommer un composant de bibliothèque</span><span class="sxs-lookup"><span data-stu-id="cd413-141">Consume a library component</span></span>

<span data-ttu-id="cd413-142">Pour utiliser des composants définis dans une bibliothèque dans un autre projet, utilisez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="cd413-142">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="cd413-143">Utilisez le nom de type complet avec l’espace de noms.</span><span class="sxs-lookup"><span data-stu-id="cd413-143">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="cd413-144">Utilisez Razor [`@using`](xref:mvc/views/razor#using) la directive de.</span><span class="sxs-lookup"><span data-stu-id="cd413-144">Use Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="cd413-145">Des composants individuels peuvent être ajoutés par nom.</span><span class="sxs-lookup"><span data-stu-id="cd413-145">Individual components can be added by name.</span></span>

<span data-ttu-id="cd413-146">Dans les exemples suivants, `ComponentLibrary` est une bibliothèque de composants contenant le `Component1` composant ( `Component1.razor` ).</span><span class="sxs-lookup"><span data-stu-id="cd413-146">In the following examples, `ComponentLibrary` is a component library containing the `Component1` component (`Component1.razor`).</span></span> <span data-ttu-id="cd413-147">Le `Component1` composant est un exemple de composant automatiquement ajouté par le modèle de projet RCL lors de la création de la bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="cd413-147">The `Component1` component is an example component automatically added by the RCL project template when the library is created.</span></span>

<span data-ttu-id="cd413-148">Référencez le `Component1` composant à l’aide de son espace de noms :</span><span class="sxs-lookup"><span data-stu-id="cd413-148">Reference the `Component1` component using its namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<ComponentLibrary.Component1 />
```

<span data-ttu-id="cd413-149">Vous pouvez également placer la bibliothèque dans la portée avec une [`@using`](xref:mvc/views/razor#using) directive et utiliser le composant sans son espace de noms :</span><span class="sxs-lookup"><span data-stu-id="cd413-149">Alternatively, bring the library into scope with an [`@using`](xref:mvc/views/razor#using) directive and use the component without its namespace:</span></span>

```razor
@using ComponentLibrary

<h1>Hello, world!</h1>

Welcome to your new app.

<Component1 />
```

<span data-ttu-id="cd413-150">Si vous le souhaitez, incluez la `@using ComponentLibrary` directive dans le fichier de niveau supérieur `_Import.razor` pour mettre les composants de la bibliothèque à la disposition d’un projet entier.</span><span class="sxs-lookup"><span data-stu-id="cd413-150">Optionally, include the `@using ComponentLibrary` directive in the top-level `_Import.razor` file to make the library's components available to an entire project.</span></span> <span data-ttu-id="cd413-151">Ajoutez la directive à un `_Import.razor` fichier à tout niveau pour appliquer l’espace de noms à un composant unique ou à un ensemble de composants dans un dossier.</span><span class="sxs-lookup"><span data-stu-id="cd413-151">Add the directive to an `_Import.razor` file at any level to apply the namespace to a single component or set of components within a folder.</span></span>

<!-- HOLD for reactivation at 5.x

::: moniker range=">= aspnetcore-5.0"

To provide `Component1`'s `my-component` CSS class to the component, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) in `Component1.razor`:

```razor
<div class="my-component">
    <Link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />

    <p>
        This Blazor component is defined in the <strong>ComponentLibrary</strong> package.
    </p>
</div>
```

To provide the stylesheet across the app, you can alternatively link to the library's stylesheet in the app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

When the `Link` component is used in a child component, the linked asset is also available to any other child component of the parent component as long as the child with the `Link` component is rendered. The distinction between using the `Link` component in a child component and placing a `<link>` HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:

* Can be modified by application state. A hard-coded `<link>` HTML tag can't be modified by application state.
* Is removed from the HTML `<head>` when the parent component is no longer rendered.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

-->

<span data-ttu-id="cd413-152">Pour fournir `Component1` la `my-component` classe CSS de, liez-la à la feuille de style de la bibliothèque dans le fichier `wwwroot/index.html` () ou le fichier de l’application Blazor WebAssembly `Pages/_Host.cshtml` ( Blazor Server ) :</span><span class="sxs-lookup"><span data-stu-id="cd413-152">To provide `Component1`'s `my-component` CSS class, link to the library's stylesheet in the app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):</span></span>

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

<!-- HOLD for reactivation at 5.x

::: moniker-end

-->

## <a name="create-a-no-locrazor-components-class-library-with-static-assets"></a><span data-ttu-id="cd413-153">Créer une Razor bibliothèque de classes de composants avec des ressources statiques</span><span class="sxs-lookup"><span data-stu-id="cd413-153">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="cd413-154">Un RCL peut inclure des ressources statiques.</span><span class="sxs-lookup"><span data-stu-id="cd413-154">An RCL can include static assets.</span></span> <span data-ttu-id="cd413-155">Les ressources statiques sont disponibles pour toutes les applications qui consomment la bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="cd413-155">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="cd413-156">Pour plus d’informations, consultez <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span><span class="sxs-lookup"><span data-stu-id="cd413-156">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="supply-components-and-static-assets-to-multiple-hosted-no-locblazor-apps"></a><span data-ttu-id="cd413-157">Fournir des composants et des ressources statiques à plusieurs applications hébergées Blazor</span><span class="sxs-lookup"><span data-stu-id="cd413-157">Supply components and static assets to multiple hosted Blazor apps</span></span>

<span data-ttu-id="cd413-158">Pour plus d’informations, consultez <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="cd413-158">For more information, see <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="browser-compatibility-analyzer-for-no-locblazor-webassembly"></a><span data-ttu-id="cd413-159">Analyseur de compatibilité des navigateurs pour Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="cd413-159">Browser compatibility analyzer for Blazor WebAssembly</span></span>

<span data-ttu-id="cd413-160">Blazor WebAssembly les applications ciblent la surface d’exposition complète de l’API .NET, mais toutes les API .NET ne sont pas prises en charge sur webassembly en raison des contraintes du bac à sable (sandbox).</span><span class="sxs-lookup"><span data-stu-id="cd413-160">Blazor WebAssembly apps target the full .NET API surface area, but not all .NET APIs are supported on WebAssembly due to browser sandbox constraints.</span></span> <span data-ttu-id="cd413-161">Les API non prises en charge sont levées <xref:System.PlatformNotSupportedException> lors de l’exécution sur Webassembly.</span><span class="sxs-lookup"><span data-stu-id="cd413-161">Unsupported APIs throw <xref:System.PlatformNotSupportedException> when running on WebAssembly.</span></span> <span data-ttu-id="cd413-162">Un analyseur de compatibilité de plateforme avertit le développeur lorsque l’application utilise des API qui ne sont pas prises en charge par les plateformes cibles de l’application.</span><span class="sxs-lookup"><span data-stu-id="cd413-162">A platform compatibility analyzer warns the developer when the app uses APIs that aren't supported by the app's target platforms.</span></span> <span data-ttu-id="cd413-163">Pour les Blazor WebAssembly applications, cela signifie que les API sont prises en charge dans les navigateurs.</span><span class="sxs-lookup"><span data-stu-id="cd413-163">For Blazor WebAssembly apps, this means checking that APIs are supported in browsers.</span></span> <span data-ttu-id="cd413-164">L’annotation des API .NET Framework pour l’analyseur de compatibilité est un processus continu, donc toutes les API .NET Framework ne sont pas annotées actuellement.</span><span class="sxs-lookup"><span data-stu-id="cd413-164">Annotating .NET framework APIs for the compatibility analyzer is an on-going process, so not all .NET framework API is currently annotated.</span></span>

<span data-ttu-id="cd413-165">Blazor WebAssembly et Razor les projets de bibliothèque de classes activent *automatiquement* les contrôles de compatibilité du navigateur en ajoutant `browser` comme plateforme prise en charge avec l' `SupportedPlatform` élément MSBuild.</span><span class="sxs-lookup"><span data-stu-id="cd413-165">Blazor WebAssembly and Razor class library projects *automatically* enable browser compatibilty checks by adding `browser` as a supported platform with the `SupportedPlatform` MSBuild item.</span></span> <span data-ttu-id="cd413-166">Les développeurs de bibliothèques peuvent ajouter manuellement l' `SupportedPlatform` élément au fichier projet d’une bibliothèque pour activer la fonctionnalité :</span><span class="sxs-lookup"><span data-stu-id="cd413-166">Library developers can manually add the `SupportedPlatform` item to a library's project file to enable the feature:</span></span>

```xml
<ItemGroup>
  <SupportedPlatform Include="browser" />
</ItemGroup>
```

<span data-ttu-id="cd413-167">Lors de la création d’une bibliothèque, indiquez qu’une API particulière n’est pas prise en charge dans les navigateurs en spécifiant `browser` à <xref:System.Runtime.Versioning.UnsupportedOSPlatformAttribute> :</span><span class="sxs-lookup"><span data-stu-id="cd413-167">When authoring a library, indicate that a particular API isn't supported in browsers by specifying `browser` to <xref:System.Runtime.Versioning.UnsupportedOSPlatformAttribute>:</span></span>

```csharp
[UnsupportedOSPlatform("browser")]
private static string GetLoggingDirectory()
{
    ...
}
```

<span data-ttu-id="cd413-168">Pour plus d’informations, consultez [annotation des API comme non prises en charge sur des plateformes spécifiques (référentiel GitHub dotnet/conceptions](https://github.com/dotnet/designs/blob/main/accepted/2020/platform-exclusion/platform-exclusion.md#build-configuration-for-platforms)).</span><span class="sxs-lookup"><span data-stu-id="cd413-168">For more information, see [Annotating APIs as unsupported on specific platforms (dotnet/designs GitHub repository](https://github.com/dotnet/designs/blob/main/accepted/2020/platform-exclusion/platform-exclusion.md#build-configuration-for-platforms).</span></span>

## <a name="no-locblazor-javascript-isolation-and-object-references"></a><span data-ttu-id="cd413-169">Blazor Isolation JavaScript et références d’objets</span><span class="sxs-lookup"><span data-stu-id="cd413-169">Blazor JavaScript isolation and object references</span></span>

<span data-ttu-id="cd413-170">Blazor active l’isolation JavaScript dans les [modules JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)standard.</span><span class="sxs-lookup"><span data-stu-id="cd413-170">Blazor enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="cd413-171">L’isolation JavaScript offre les avantages suivants :</span><span class="sxs-lookup"><span data-stu-id="cd413-171">JavaScript isolation provides the following benefits:</span></span>

* <span data-ttu-id="cd413-172">Le JavaScript importé ne pollue plus l’espace de noms global.</span><span class="sxs-lookup"><span data-stu-id="cd413-172">Imported JavaScript no longer pollutes the global namespace.</span></span>
* <span data-ttu-id="cd413-173">Les consommateurs de la bibliothèque et des composants ne sont pas requis pour importer manuellement le code JavaScript associé.</span><span class="sxs-lookup"><span data-stu-id="cd413-173">Consumers of the library and components aren't required to manually import the related JavaScript.</span></span>

<span data-ttu-id="cd413-174">Pour plus d’informations, consultez <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.</span><span class="sxs-lookup"><span data-stu-id="cd413-174">For more information, see <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.</span></span>

::: moniker-end

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="cd413-175">Générer, empaqueter et envoyer à NuGet</span><span class="sxs-lookup"><span data-stu-id="cd413-175">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="cd413-176">Étant donné que les bibliothèques de composants sont des bibliothèques .NET standard, leur empaquetage et leur envoi à NuGet ne sont pas différents de l’empaquetage et de l’expédition d’une bibliothèque à NuGet.</span><span class="sxs-lookup"><span data-stu-id="cd413-176">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="cd413-177">L’empaquetage est effectué à l’aide [`dotnet pack`](/dotnet/core/tools/dotnet-pack) de la commande dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="cd413-177">Packaging is performed using the [`dotnet pack`](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="cd413-178">Téléchargez le package dans NuGet à l’aide de la [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) commande dans un interpréteur de commandes.</span><span class="sxs-lookup"><span data-stu-id="cd413-178">Upload the package to NuGet using the [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cd413-179">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="cd413-179">Additional resources</span></span>

::: moniker range=">= aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="cd413-180">Ajouter un fichier de configuration de découpage du langage intermédiaire (IL) XML à une bibliothèque</span><span class="sxs-lookup"><span data-stu-id="cd413-180">Add an XML Intermediate Language (IL) Trimmer configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-trimmer)
* [<span data-ttu-id="cd413-181">Prise en charge de l’isolation CSS avec les Razor bibliothèques de classes</span><span class="sxs-lookup"><span data-stu-id="cd413-181">CSS isolation support with Razor class libraries</span></span>](xref:blazor/components/css-isolation#razor-class-library-rcl-support)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="cd413-182">Ajouter un fichier de configuration de l’éditeur de liens en langage intermédiaire (IL) à une bibliothèque</span><span class="sxs-lookup"><span data-stu-id="cd413-182">Add an XML Intermediate Language (IL) Linker configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)

::: moniker-end
