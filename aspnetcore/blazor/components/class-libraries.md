---
title: RazorBibliothèques de classes des composants ASP.net Core
author: guardrex
description: Découvrez comment les composants peuvent être inclus dans des Blazor applications à partir d’une bibliothèque de composants externes.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/12/2021
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
ms.openlocfilehash: fed5d26ecd73a4710ee794c413fd51e0b4cb4913
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280205"
---
# <a name="aspnet-core-razor-components-class-libraries"></a>RazorBibliothèques de classes des composants ASP.net Core

Les composants peuvent être partagés dans une [ Razor bibliothèque de classes (RCL)](xref:razor-pages/ui-class) d’un projet à l’autre. Vous pouvez inclure une *Razor bibliothèque de classes de composants* à partir de :

* Un autre projet dans la solution.
* Package NuGet.
* Bibliothèque .NET référencée.

Tout comme les composants sont des types .NET standard, les composants fournis par un RCL sont des assemblys .NET normaux.

## <a name="create-an-rcl"></a>Créer un RCL

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Créez un projet.
1. Sélectionnez **Razor bibliothèque de classes**. Sélectionnez **Suivant**.
1. Dans la boîte de dialogue **créer une nouvelle Razor bibliothèque de classes** , sélectionnez **créer**.
1. Indiquez un nom de projet dans le champ **Nom du projet**, ou acceptez le nom de projet par défaut. Les exemples de cette rubrique utilisent le nom du projet `ComponentLibrary` . Sélectionnez **Create** (Créer).
1. Ajouter RCL à une solution :
   1. Cliquez avec le bouton droit sur la solution. Sélectionnez **Ajouter** > **un projet existant**.
   1. Accédez au fichier projet de RCL.
   1. Sélectionnez le fichier projet de RCL ( `.csproj` ).
1. Ajoutez une référence à RCL à partir de l’application :
   1. Cliquez avec le bouton droit sur le projet d’application. Sélectionnez **Ajouter** une  >  **référence**.
   1. Sélectionnez le projet RCL. Sélectionnez **OK**.

> [!NOTE]
> Si la case à cocher **pages et vues de support** est activée lors de la génération du RCL à partir du modèle, ajoutez également un `_Imports.razor` fichier à la racine du projet généré avec le contenu suivant pour activer la Razor création de composants :
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> Ajoutez manuellement le fichier à la racine du projet généré.

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

1. Utilisez le modèle de **Razor bibliothèque de classes** ( `razorclasslib` ) avec la [`dotnet new`](/dotnet/core/tools/dotnet-new) commande dans un interpréteur de commandes. Dans l’exemple suivant, un RCL est créé nommé `ComponentLibrary` . Le dossier qui contient `ComponentLibrary` est créé automatiquement lors de l’exécution de la commande :

   ```dotnetcli
   dotnet new razorclasslib -o ComponentLibrary
   ```

   > [!NOTE]
   > Si le `-s|--support-pages-and-views` commutateur est utilisé lors de la génération du RCL à partir du modèle, ajoutez également un `_Imports.razor` fichier à la racine du projet généré avec le contenu suivant pour activer la Razor création de composants :
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > Ajoutez manuellement le fichier à la racine du projet généré.

1. Pour ajouter la bibliothèque à un projet existant, utilisez la [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) commande dans un interpréteur de commandes. Dans l’exemple suivant, le RCL est ajouté à l’application. Exécutez la commande suivante à partir du dossier du projet de l’application avec le chemin d’accès à la bibliothèque :

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a>Consommer un composant de bibliothèque

Pour utiliser des composants définis dans une bibliothèque dans un autre projet, utilisez l’une des approches suivantes :

* Utilisez le nom de type complet avec l’espace de noms.
* Utilisez Razor [`@using`](xref:mvc/views/razor#using) la directive de. Des composants individuels peuvent être ajoutés par nom.

Dans les exemples suivants, `ComponentLibrary` est une bibliothèque de composants contenant le `Component1` composant ( `Component1.razor` ). Le `Component1` composant est un exemple de composant automatiquement ajouté par le modèle de projet RCL lors de la création de la bibliothèque.

Référencez le `Component1` composant à l’aide de son espace de noms :

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<ComponentLibrary.Component1 />
```

Vous pouvez également placer la bibliothèque dans la portée avec une [`@using`](xref:mvc/views/razor#using) directive et utiliser le composant sans son espace de noms :

```razor
@using ComponentLibrary

<h1>Hello, world!</h1>

Welcome to your new app.

<Component1 />
```

Si vous le souhaitez, incluez la `@using ComponentLibrary` directive dans le fichier de niveau supérieur `_Import.razor` pour mettre les composants de la bibliothèque à la disposition d’un projet entier. Ajoutez la directive à un `_Import.razor` fichier à tout niveau pour appliquer l’espace de noms à un composant unique ou à un ensemble de composants dans un dossier.

::: moniker range=">= aspnetcore-5.0"

Pour les composants de bibliothèque qui utilisent l' [isolation CSS](xref:blazor/components/css-isolation), il n’est pas nécessaire de lier explicitement les feuilles de style des composants individuels de la bibliothèque dans l’application qui consomme la bibliothèque. Les styles de composant sont automatiquement mis à la disposition de l’application consommatrice.

<!-- REACTIVATE WHEN HEAD COMPONENTS COME BACK AT 6.0

To provide additional library component styles from stylesheets in the library's `wwwroot` folder, link the stylesheets using the framework's [`Link` component](xref:blazor/fundamentals/signalr#influence-html-head-tag-elements) in `Component1.razor`:

```razor
<div class="my-component">
    <Link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />

    <p>
        This Blazor component is defined in the <strong>ComponentLibrary</strong> package.
    </p>
</div>
```

NEXT PARAGRAPH: RECAST TO 'CAN ALSO ADOPT ...'

-->

Pour fournir des styles de composant de bibliothèque supplémentaires à partir de feuilles de style dans le dossier de la bibliothèque `wwwroot` , liez les feuilles de style dans le fichier () ou le fichier de l’application consommatrice `wwwroot/index.html` Blazor WebAssembly `Pages/_Host.cshtml` ( Blazor Server ) :

```html
<head>
    ...
    <link href="_content/ComponentLibrary/additionalStyles.css" rel="stylesheet" />
</head>
```

<!-- REACTIVATE WHEN HEAD COMPONENTS COME BACK AT 6.0

When the `Link` component is used in a child component, the linked asset is also available to any other child component of the parent component as long as the child with the `Link` component is rendered. The distinction between using the `Link` component in a child component and placing a `<link>` HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:

* Can be modified by application state. A hard-coded `<link>` HTML tag can't be modified by application state.
* Is removed from the HTML `<head>` when the parent component is no longer rendered.

-->

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Pour fournir `Component1` la `my-component` classe CSS de, liez-la à la feuille de style de la bibliothèque dans le fichier `wwwroot/index.html` () ou le fichier de l’application Blazor WebAssembly `Pages/_Host.cshtml` ( Blazor Server ) :

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

## <a name="create-a-razor-components-class-library-with-static-assets"></a>Créer une Razor bibliothèque de classes de composants avec des ressources statiques

Un RCL peut inclure des ressources statiques. Les ressources statiques sont disponibles pour toutes les applications qui consomment la bibliothèque. Pour plus d’informations, consultez <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.

## <a name="supply-components-and-static-assets-to-multiple-hosted-blazor-apps"></a>Fournir des composants et des ressources statiques à plusieurs applications hébergées Blazor

Pour plus d’informations, consultez <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.

::: moniker range=">= aspnetcore-5.0"

## <a name="browser-compatibility-analyzer-for-blazor-webassembly"></a>Analyseur de compatibilité des navigateurs pour Blazor WebAssembly

Blazor WebAssembly les applications ciblent la surface d’exposition complète de l’API .NET, mais toutes les API .NET ne sont pas prises en charge sur webassembly en raison des contraintes du bac à sable (sandbox). Les API non prises en charge sont levées <xref:System.PlatformNotSupportedException> lors de l’exécution sur Webassembly. Un analyseur de compatibilité de plateforme avertit le développeur lorsque l’application utilise des API qui ne sont pas prises en charge par les plateformes cibles de l’application. Pour les Blazor WebAssembly applications, cela signifie que les API sont prises en charge dans les navigateurs. L’annotation des API .NET Framework pour l’analyseur de compatibilité est un processus continu, donc toutes les API .NET Framework ne sont pas annotées actuellement.

Blazor WebAssembly et Razor les projets de bibliothèque de classes activent *automatiquement* les contrôles de compatibilité du navigateur en ajoutant `browser` comme plateforme prise en charge avec l' `SupportedPlatform` élément MSBuild. Les développeurs de bibliothèques peuvent ajouter manuellement l' `SupportedPlatform` élément au fichier projet d’une bibliothèque pour activer la fonctionnalité :

```xml
<ItemGroup>
  <SupportedPlatform Include="browser" />
</ItemGroup>
```

Lors de la création d’une bibliothèque, indiquez qu’une API particulière n’est pas prise en charge dans les navigateurs en spécifiant `browser` à <xref:System.Runtime.Versioning.UnsupportedOSPlatformAttribute> :

```csharp
[UnsupportedOSPlatform("browser")]
private static string GetLoggingDirectory()
{
    ...
}
```

Pour plus d’informations, consultez [annotation des API comme non prises en charge sur des plateformes spécifiques (référentiel GitHub dotnet/conceptions](https://github.com/dotnet/designs/blob/main/accepted/2020/platform-exclusion/platform-exclusion.md#build-configuration-for-platforms)).

## <a name="blazor-javascript-isolation-and-object-references"></a>Blazor Isolation JavaScript et références d’objets

Blazor active l’isolation JavaScript dans les [modules JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)standard. L’isolation JavaScript offre les avantages suivants :

* Le JavaScript importé ne pollue plus l’espace de noms global.
* Les consommateurs de la bibliothèque et des composants ne sont pas requis pour importer manuellement le code JavaScript associé.

Pour plus d’informations, consultez <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.

::: moniker-end

## <a name="build-pack-and-ship-to-nuget"></a>Générer, empaqueter et envoyer à NuGet

Étant donné que les bibliothèques de composants sont des bibliothèques .NET standard, leur empaquetage et leur envoi à NuGet ne sont pas différents de l’empaquetage et de l’expédition d’une bibliothèque à NuGet. L’empaquetage est effectué à l’aide [`dotnet pack`](/dotnet/core/tools/dotnet-pack) de la commande dans une interface de commande :

```dotnetcli
dotnet pack
```

Téléchargez le package dans NuGet à l’aide de la [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) commande dans un interpréteur de commandes.

## <a name="additional-resources"></a>Ressources supplémentaires

::: moniker range=">= aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [Ajouter un fichier de configuration de découpage du langage intermédiaire (IL) XML à une bibliothèque](xref:blazor/host-and-deploy/configure-trimmer)
* [Prise en charge de l’isolation CSS avec les Razor bibliothèques de classes](xref:blazor/components/css-isolation#razor-class-library-rcl-support)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [Ajouter un fichier de configuration de l’éditeur de liens en langage intermédiaire (IL) à une bibliothèque](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)

::: moniker-end
