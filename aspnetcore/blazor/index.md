---
title: Présentation de ASP.NET Core Blazor
author: guardrex
description: Explorez ASP.NET Core Blazor , un moyen de créer une interface utilisateur Web interactive côté client avec .net dans une application ASP.net core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, seoapril2019, devx-track-js
ms.date: 09/25/2020
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
uid: blazor/index
ms.openlocfilehash: bd42ba41c6f599d7c0308febbbde71e0d060cc84
ms.sourcegitcommit: 20a41c8e40a2e69e99291e2fe18caa04c02e7109
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99578293"
---
# <a name="introduction-to-aspnet-core-blazor"></a><span data-ttu-id="84cc4-103">Présentation de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="84cc4-103">Introduction to ASP.NET Core Blazor</span></span>

<span data-ttu-id="84cc4-104">Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="84cc4-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="84cc4-105">*Bienvenue Blazor !*</span><span class="sxs-lookup"><span data-stu-id="84cc4-105">*Welcome to Blazor!*</span></span>

<span data-ttu-id="84cc4-106">Blazor est une infrastructure permettant de créer une interface utilisateur Web interactive côté client avec [.net](/dotnet/standard/tour):</span><span class="sxs-lookup"><span data-stu-id="84cc4-106">Blazor is a framework for building interactive client-side web UI with [.NET](/dotnet/standard/tour):</span></span>

* <span data-ttu-id="84cc4-107">Créez des interfaces utilisateur riches et interactives en [C#](/dotnet/csharp/) au lieu de [JavaScript](https://www.javascript.com).</span><span class="sxs-lookup"><span data-stu-id="84cc4-107">Create rich interactive UIs using [C#](/dotnet/csharp/) instead of [JavaScript](https://www.javascript.com).</span></span>
* <span data-ttu-id="84cc4-108">Partagez la logique d’application côté serveur et côté client écrite dans .NET.</span><span class="sxs-lookup"><span data-stu-id="84cc4-108">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="84cc4-109">Affichez l’interface utilisateur en langage HTML et CSS pour une large prise en charge des navigateurs, y compris les navigateurs mobiles.</span><span class="sxs-lookup"><span data-stu-id="84cc4-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>
* <span data-ttu-id="84cc4-110">Intégrez les plateformes d’hébergement modernes, telles que l' [amarrage](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span><span class="sxs-lookup"><span data-stu-id="84cc4-110">Integrate with modern hosting platforms, such as [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span></span>

<span data-ttu-id="84cc4-111">L’utilisation de .NET dans le développement web côté client offre les avantages suivants :</span><span class="sxs-lookup"><span data-stu-id="84cc4-111">Using .NET for client-side web development offers the following advantages:</span></span>

* <span data-ttu-id="84cc4-112">Écriture de code dans C# plutôt que dans JavaScript.</span><span class="sxs-lookup"><span data-stu-id="84cc4-112">Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="84cc4-113">Tirez parti de l’écosystème .NET existant des [bibliothèques .net](/dotnet/standard/class-libraries).</span><span class="sxs-lookup"><span data-stu-id="84cc4-113">Leverage the existing .NET ecosystem of [.NET libraries](/dotnet/standard/class-libraries).</span></span>
* <span data-ttu-id="84cc4-114">Partagez la logique de l’application sur le serveur et le client.</span><span class="sxs-lookup"><span data-stu-id="84cc4-114">Share app logic across server and client.</span></span>
* <span data-ttu-id="84cc4-115">Bénéficiez des performances, de la fiabilité et de la sécurité de .NET.</span><span class="sxs-lookup"><span data-stu-id="84cc4-115">Benefit from .NET's performance, reliability, and security.</span></span>
* <span data-ttu-id="84cc4-116">Restez productif avec [Visual Studio](https://visualstudio.microsoft.com) sur Windows, Linux et MacOS.</span><span class="sxs-lookup"><span data-stu-id="84cc4-116">Stay productive with [Visual Studio](https://visualstudio.microsoft.com) on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="84cc4-117">Développez avec un ensemble commun de langages, de frameworks et d’outils stables, riches en fonctionnalités et faciles à utiliser.</span><span class="sxs-lookup"><span data-stu-id="84cc4-117">Build on a common set of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

## <a name="components"></a><span data-ttu-id="84cc4-118">Components</span><span class="sxs-lookup"><span data-stu-id="84cc4-118">Components</span></span>

<span data-ttu-id="84cc4-119">Blazor les applications sont basées sur des *composants*.</span><span class="sxs-lookup"><span data-stu-id="84cc4-119">Blazor apps are based on *components*.</span></span> <span data-ttu-id="84cc4-120">Un composant dans Blazor est un élément de l’interface utilisateur, tel qu’une page, une boîte de dialogue ou un formulaire de saisie de données.</span><span class="sxs-lookup"><span data-stu-id="84cc4-120">A component in Blazor is an element of UI, such as a page, dialog, or data entry form.</span></span>

<span data-ttu-id="84cc4-121">Les composants sont des classes .NET C# intégrées à des [assemblys .net](/dotnet/standard/assembly/) qui :</span><span class="sxs-lookup"><span data-stu-id="84cc4-121">Components are .NET C# classes built into [.NET assemblies](/dotnet/standard/assembly/) that:</span></span>

* <span data-ttu-id="84cc4-122">Définissent la logique de rendu de l’interface utilisateur flexible.</span><span class="sxs-lookup"><span data-stu-id="84cc4-122">Define flexible UI rendering logic.</span></span>
* <span data-ttu-id="84cc4-123">Gèrent les événements de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="84cc4-123">Handle user events.</span></span>
* <span data-ttu-id="84cc4-124">Peuvent être imbriqués et réutilisés.</span><span class="sxs-lookup"><span data-stu-id="84cc4-124">Can be nested and reused.</span></span>
* <span data-ttu-id="84cc4-125">Peut être partagé et distribué en tant que [ Razor bibliothèques de classes](xref:razor-pages/ui-class) ou [packages NuGet](/nuget/what-is-nuget).</span><span class="sxs-lookup"><span data-stu-id="84cc4-125">Can be shared and distributed as [Razor class libraries](xref:razor-pages/ui-class) or [NuGet packages](/nuget/what-is-nuget).</span></span>

<span data-ttu-id="84cc4-126">La classe Component est généralement écrite sous la forme d’une [Razor](xref:mvc/views/razor) page de balisage avec une `.razor` extension de fichier.</span><span class="sxs-lookup"><span data-stu-id="84cc4-126">The component class is usually written in the form of a [Razor](xref:mvc/views/razor) markup page with a `.razor` file extension.</span></span> <span data-ttu-id="84cc4-127">Les composants dans Blazor sont officiellement désignés sous le terme de *Razor composants*.</span><span class="sxs-lookup"><span data-stu-id="84cc4-127">Components in Blazor are formally referred to as *Razor components*.</span></span> <span data-ttu-id="84cc4-128">Razor est une syntaxe pour combiner le balisage HTML avec du code C# conçu pour la productivité des développeurs.</span><span class="sxs-lookup"><span data-stu-id="84cc4-128">Razor is a syntax for combining HTML markup with C# code designed for developer productivity.</span></span> <span data-ttu-id="84cc4-129">Razor vous permet de basculer entre le balisage HTML et C# dans le même fichier avec la prise en charge de la programmation [IntelliSense](/visualstudio/ide/using-intellisense) dans Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="84cc4-129">Razor allows you to switch between HTML markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) programming support in Visual Studio.</span></span> <span data-ttu-id="84cc4-130">Razor Les pages et MVC utilisent également Razor .</span><span class="sxs-lookup"><span data-stu-id="84cc4-130">Razor Pages and MVC also use Razor.</span></span> <span data-ttu-id="84cc4-131">Contrairement aux Razor pages et MVC, qui sont générés autour d’un modèle de demande/réponse, les composants sont utilisés spécifiquement pour la logique et la composition de l’interface utilisateur côté client.</span><span class="sxs-lookup"><span data-stu-id="84cc4-131">Unlike Razor Pages and MVC, which are built around a request/response model, components are used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="84cc4-132">Blazor utilise des balises HTML naturelles pour la composition de l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="84cc4-132">Blazor uses natural HTML tags for UI composition.</span></span> <span data-ttu-id="84cc4-133">Le Razor balisage suivant illustre un composant ( `Dialog.razor` ) qui affiche une boîte de dialogue et traite un événement lorsque l’utilisateur sélectionne un bouton :</span><span class="sxs-lookup"><span data-stu-id="84cc4-133">The following Razor markup demonstrates a component (`Dialog.razor`) that displays a dialog and processes an event when the user selects a button:</span></span>

```razor
<div class="card" style="width:22rem">
    <div class="card-body">
        <h3 class="card-title">@Title</h3>
        <p class="card-text">@ChildContent</p>
        <button @onclick="OnYes">Yes!</button>
    </div>
</div>

@code {
    [Parameter]
    public RenderFragment ChildContent { get; set; }

    [Parameter]
    public string Title { get; set; }

    private void OnYes()
    {
        Console.WriteLine("Write to the console in C#! 'Yes' button selected.");
    }
}
```

<span data-ttu-id="84cc4-134">Dans l’exemple précédent, `OnYes` est une méthode C# déclenchée par l’événement du bouton `onclick` .</span><span class="sxs-lookup"><span data-stu-id="84cc4-134">In the preceding example, `OnYes` is a C# method triggered by the button's `onclick` event.</span></span> <span data-ttu-id="84cc4-135">Le texte de la boîte de dialogue ( `ChildContent` ) et le titre ( `Title` ) sont fournis par le composant suivant qui utilise ce composant dans son interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="84cc4-135">The dialog's text (`ChildContent`) and title (`Title`) are provided by the following component that uses this component in its UI.</span></span>

<span data-ttu-id="84cc4-136">Le `Dialog` composant est imbriqué dans un autre composant à l’aide d’une balise html.</span><span class="sxs-lookup"><span data-stu-id="84cc4-136">The `Dialog` component is nested within another component using an HTML tag.</span></span> <span data-ttu-id="84cc4-137">Dans l’exemple suivant, le `Index` composant ( `Pages/Index.razor` ) utilise le `Dialog` composant précédent.</span><span class="sxs-lookup"><span data-stu-id="84cc4-137">In the following example, the `Index` component (`Pages/Index.razor`) uses the preceding `Dialog` component.</span></span> <span data-ttu-id="84cc4-138">L’attribut de la balise `Title` passe une valeur pour le titre à la `Dialog` propriété du composant `Title` .</span><span class="sxs-lookup"><span data-stu-id="84cc4-138">The tag's `Title` attribute passes a value for the title to the `Dialog` component's `Title` property.</span></span>  <span data-ttu-id="84cc4-139">Le `Dialog` texte du composant ( `ChildContent` ) est défini par le contenu de l' `<Dialog>` élément.</span><span class="sxs-lookup"><span data-stu-id="84cc4-139">The `Dialog` component's text (`ChildContent`) are set by the content of the `<Dialog>` element.</span></span> <span data-ttu-id="84cc4-140">Quand le `Dialog` composant est ajouté au `Index` composant, [IntelliSense dans Visual Studio](/visualstudio/ide/using-intellisense) accélère le développement avec la syntaxe et la saisie semi-automatique des paramètres.</span><span class="sxs-lookup"><span data-stu-id="84cc4-140">When the `Dialog` component is added to the `Index` component, [IntelliSense in Visual Studio](/visualstudio/ide/using-intellisense) speeds development with syntax and parameter completion.</span></span>

```razor
@page "/"

<h1>Hello, world!</h1>

<p>
    Welcome to your new app.
</p>

<Dialog Title="Learn More">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

<span data-ttu-id="84cc4-141">La boîte de dialogue s’affiche lorsque vous `Index` accédez au composant dans un navigateur.</span><span class="sxs-lookup"><span data-stu-id="84cc4-141">The dialog is rendered when the `Index` component is accessed in a browser.</span></span> <span data-ttu-id="84cc4-142">Lorsque le bouton est sélectionné par l’utilisateur, la console outils de développement du navigateur affiche le message écrit par la `OnYes` méthode :</span><span class="sxs-lookup"><span data-stu-id="84cc4-142">When the button is selected by the user, the browser's developer tools console shows the message written by the `OnYes` method:</span></span>

![Composant de boîte de dialogue rendu dans le navigateur imbriqué dans le composant d’index.](index/_static/dialog.png)

<span data-ttu-id="84cc4-146">Les composants sont rendus dans une représentation en mémoire de la [Document Object Model du navigateur (DOM)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) , appelée *arborescence de rendu*, qui est utilisée pour mettre à jour l’interface utilisateur de manière flexible et efficace.</span><span class="sxs-lookup"><span data-stu-id="84cc4-146">Components render into an in-memory representation of the browser's [Document Object Model (DOM)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) called a *render tree*, which is used to update the UI in a flexible and efficient way.</span></span>

## Blazor WebAssembly

<span data-ttu-id="84cc4-147">Blazor WebAssembly est une [infrastructure d’application à page unique (Spa)](/dotnet/architecture/modern-web-apps-azure/choose-between-traditional-web-and-single-page-apps) pour la création d’applications Web interactives côté client avec .net.</span><span class="sxs-lookup"><span data-stu-id="84cc4-147">Blazor WebAssembly is a [single-page app (SPA) framework](/dotnet/architecture/modern-web-apps-azure/choose-between-traditional-web-and-single-page-apps) for building interactive client-side web apps with .NET.</span></span> <span data-ttu-id="84cc4-148">Blazor WebAssembly utilise des normes Web ouvertes sans plug-ins ni recompilation du code dans d’autres langages.</span><span class="sxs-lookup"><span data-stu-id="84cc4-148">Blazor WebAssembly uses open web standards without plugins or recompiling code into other languages.</span></span> <span data-ttu-id="84cc4-149">Blazor WebAssembly fonctionne dans tous les navigateurs Web modernes, y compris les navigateurs mobiles.</span><span class="sxs-lookup"><span data-stu-id="84cc4-149">Blazor WebAssembly works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="84cc4-150">L’exécution de code .NET dans des navigateurs Web est rendue possible par [Webassembly](https://webassembly.org) (abrégé `wasm` ).</span><span class="sxs-lookup"><span data-stu-id="84cc4-150">Running .NET code inside web browsers is made possible by [WebAssembly](https://webassembly.org) (abbreviated `wasm`).</span></span> <span data-ttu-id="84cc4-151">WebAssembly est un format bytecode compact optimisé pour un téléchargement rapide et une vitesse d’exécution maximale.</span><span class="sxs-lookup"><span data-stu-id="84cc4-151">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span> <span data-ttu-id="84cc4-152">WebAssembly est un standard web ouvert pris en charge dans les navigateurs web sans plug-in.</span><span class="sxs-lookup"><span data-stu-id="84cc4-152">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span>

<span data-ttu-id="84cc4-153">Le code webassembly peut accéder aux fonctionnalités complètes du navigateur via JavaScript, appelé *interopérabilité JavaScript*, souvent abrégé en com *Interop* ou l’interopérabilité *js*.</span><span class="sxs-lookup"><span data-stu-id="84cc4-153">WebAssembly code can access the full functionality of the browser via JavaScript, called *JavaScript interoperability*, often shortened to *JavaScript interop* or *JS interop*.</span></span> <span data-ttu-id="84cc4-154">Le code .NET exécuté via WebAssembly dans le navigateur s’exécute dans le bac à sable JavaScript du navigateur avec les protections offertes par le bac à sable contre les actions malveillantes sur l’ordinateur client.</span><span class="sxs-lookup"><span data-stu-id="84cc4-154">.NET code executed via WebAssembly in the browser runs in the browser's JavaScript sandbox with the protections that the sandbox provides against malicious actions on the client machine.</span></span>

![::: No-Loc (éblouissant webassembly) ::: exécute le code .NET dans le navigateur avec webassembly.](index/_static/blazor-webassembly.png)

<span data-ttu-id="84cc4-156">Quand une Blazor WebAssembly application est générée et exécutée dans un navigateur :</span><span class="sxs-lookup"><span data-stu-id="84cc4-156">When a Blazor WebAssembly app is built and run in a browser:</span></span>

* <span data-ttu-id="84cc4-157">Les fichiers de code C# et les Razor fichiers sont compilés dans des assemblys .net.</span><span class="sxs-lookup"><span data-stu-id="84cc4-157">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="84cc4-158">Les assemblys et le [Runtime .net](/dotnet/framework/get-started/overview) sont téléchargés dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="84cc4-158">The assemblies and the [.NET runtime](/dotnet/framework/get-started/overview) are downloaded to the browser.</span></span>
* <span data-ttu-id="84cc4-159">Blazor WebAssembly amorce le Runtime .NET et configure le runtime pour charger les assemblys de l’application.</span><span class="sxs-lookup"><span data-stu-id="84cc4-159">Blazor WebAssembly bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="84cc4-160">Le Blazor WebAssembly Runtime utilise l’interopérabilité JavaScript pour gérer les appels de l’API de manipulation et du navigateur DOM.</span><span class="sxs-lookup"><span data-stu-id="84cc4-160">The Blazor WebAssembly runtime uses JavaScript interop to handle DOM manipulation and browser API calls.</span></span>

<span data-ttu-id="84cc4-161">La taille de l’application publiée, sa *taille de charge utile*, est un facteur de performance critique pour l’utilisation d’une application.</span><span class="sxs-lookup"><span data-stu-id="84cc4-161">The size of the published app, its *payload size*, is a critical performance factor for an app's usability.</span></span> <span data-ttu-id="84cc4-162">Le téléchargement d’une application volumineuse dans un navigateur prend un certain temps, ce qui nuit à l’expérience utilisateur.</span><span class="sxs-lookup"><span data-stu-id="84cc4-162">A large app takes a relatively long time to download to a browser, which diminishes the user experience.</span></span> <span data-ttu-id="84cc4-163">Blazor WebAssembly optimise la taille de la charge utile pour réduire les temps de téléchargement :</span><span class="sxs-lookup"><span data-stu-id="84cc4-163">Blazor WebAssembly optimizes payload size to reduce download times:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="84cc4-164">Le code inutilisé est supprimé de l’application lorsqu’elle est publiée par le [découpage en langage intermédiaire (il)](xref:blazor/host-and-deploy/configure-trimmer).</span><span class="sxs-lookup"><span data-stu-id="84cc4-164">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Trimmer](xref:blazor/host-and-deploy/configure-trimmer).</span></span>
* <span data-ttu-id="84cc4-165">Réponses HTTP compressées.</span><span class="sxs-lookup"><span data-stu-id="84cc4-165">HTTP responses are compressed.</span></span>
* <span data-ttu-id="84cc4-166">Le runtime .NET et les assemblys sont mis en cache dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="84cc4-166">The .NET runtime and assemblies are cached in the browser.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="84cc4-167">Du code inutilisé est extrait de l’application lorsqu’elle est publiée par l’[éditeur de liens de langage intermédiaire (IL)](xref:blazor/host-and-deploy/configure-linker).</span><span class="sxs-lookup"><span data-stu-id="84cc4-167">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:blazor/host-and-deploy/configure-linker).</span></span>
* <span data-ttu-id="84cc4-168">Réponses HTTP compressées.</span><span class="sxs-lookup"><span data-stu-id="84cc4-168">HTTP responses are compressed.</span></span>
* <span data-ttu-id="84cc4-169">Le runtime .NET et les assemblys sont mis en cache dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="84cc4-169">The .NET runtime and assemblies are cached in the browser.</span></span>

::: moniker-end

## Blazor Server

<span data-ttu-id="84cc4-170">Blazor dissocie la logique de rendu des composants de l’application des mises à jour de l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="84cc4-170">Blazor decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="84cc4-171">*Blazor Server* prend en charge l’hébergement de Razor composants sur le serveur dans une application ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="84cc4-171">*Blazor Server* provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="84cc4-172">Les mises à jour de l’interface utilisateur sont gérées via une [SignalR](xref:signalr/introduction) connexion.</span><span class="sxs-lookup"><span data-stu-id="84cc4-172">UI updates are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="84cc4-173">Le Runtime gère :</span><span class="sxs-lookup"><span data-stu-id="84cc4-173">The runtime handles:</span></span>

* <span data-ttu-id="84cc4-174">Envoi d’événements d’interface utilisateur du navigateur au serveur.</span><span class="sxs-lookup"><span data-stu-id="84cc4-174">Sending UI events from the browser to the server.</span></span>
* <span data-ttu-id="84cc4-175">Application des mises à jour de l’interface utilisateur au composant rendu qui sont renvoyées par le serveur.</span><span class="sxs-lookup"><span data-stu-id="84cc4-175">Applying UI updates to the rendered component that are sent back by the server.</span></span>

<span data-ttu-id="84cc4-176">La connexion utilisée par Blazor Server pour communiquer avec le navigateur est également utilisée pour gérer les appels Interop JavaScript.</span><span class="sxs-lookup"><span data-stu-id="84cc4-176">The connection used by Blazor Server to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

![::: No-Loc (serveur éblouissant) ::: exécute le code .NET sur le serveur et interagit avec le Document Object Model sur le client via un ::: No-Loc (Signalr) ::: Connection](index/_static/blazor-server.png)

## <a name="javascript-interop"></a><span data-ttu-id="84cc4-178">Interopérabilité JavaScript</span><span class="sxs-lookup"><span data-stu-id="84cc4-178">JavaScript interop</span></span>

<span data-ttu-id="84cc4-179">Pour les applications qui nécessitent des bibliothèques JavaScript tierces et l’accès à des API de navigateur, les composants interagissent avec JavaScript.</span><span class="sxs-lookup"><span data-stu-id="84cc4-179">For apps that require third-party JavaScript libraries and access to browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="84cc4-180">Les composants peuvent utiliser les mêmes API ou bibliothèques que JavaScript.</span><span class="sxs-lookup"><span data-stu-id="84cc4-180">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="84cc4-181">Le code c# peut appeler du code [JavaScript](xref:blazor/call-javascript-from-dotnet)et le code JavaScript peut faire [appel à](xref:blazor/call-dotnet-from-javascript)du code c#.</span><span class="sxs-lookup"><span data-stu-id="84cc4-181">C# code can [call into JavaScript code](xref:blazor/call-javascript-from-dotnet), and JavaScript code can [call into C# code](xref:blazor/call-dotnet-from-javascript).</span></span>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="84cc4-182">Partage de code et .NET Standard</span><span class="sxs-lookup"><span data-stu-id="84cc4-182">Code sharing and .NET Standard</span></span>

<span data-ttu-id="84cc4-183">Blazor implémente le [.NET standard](/dotnet/standard/net-standard), qui permet Blazor aux projets de référencer des bibliothèques qui se conforment aux spécifications de .NET standard.</span><span class="sxs-lookup"><span data-stu-id="84cc4-183">Blazor implements the [.NET Standard](/dotnet/standard/net-standard), which enables Blazor projects to reference libraries that conform to .NET Standard specifications.</span></span> <span data-ttu-id="84cc4-184">.NET Standard est une spécification formelle d’API .NET qui sont communes aux implémentations .NET.</span><span class="sxs-lookup"><span data-stu-id="84cc4-184">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="84cc4-185">.NET Standard bibliothèques de classes peuvent être partagées entre différentes plateformes .NET, telles que Blazor , .NET Framework, .net Core, Xamarin, mono et Unity.</span><span class="sxs-lookup"><span data-stu-id="84cc4-185">.NET Standard class libraries can be shared across different .NET platforms, such as Blazor, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

<span data-ttu-id="84cc4-186">Les API qui ne sont pas applicables à l’intérieur d’un navigateur web (par exemple l’accès au système de fichiers, l’ouverture d’un socket et le threading) lèvent une <xref:System.PlatformNotSupportedException>.</span><span class="sxs-lookup"><span data-stu-id="84cc4-186">APIs that aren't applicable inside of a web browser (for example, accessing the file system, opening a socket, and threading) throw a <xref:System.PlatformNotSupportedException>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="84cc4-187">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="84cc4-187">Additional resources</span></span>

* [<span data-ttu-id="84cc4-188">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="84cc4-188">WebAssembly</span></span>](https://webassembly.org)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor>
* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="84cc4-189">C# Guide</span><span class="sxs-lookup"><span data-stu-id="84cc4-189">C# Guide</span></span>](/dotnet/csharp/)
* [<span data-ttu-id="84cc4-190">Razor Référence de syntaxe pour ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="84cc4-190">Razor syntax reference for ASP.NET Core</span></span>](xref:mvc/views/razor)
* [<span data-ttu-id="84cc4-191">HTML</span><span class="sxs-lookup"><span data-stu-id="84cc4-191">HTML</span></span>](https://www.w3.org/html/)
* <span data-ttu-id="84cc4-192">[Isard Blazor ](https://github.com/AdrienTorris/awesome-blazor) Liens vers la communauté</span><span class="sxs-lookup"><span data-stu-id="84cc4-192">[Awesome Blazor](https://github.com/AdrienTorris/awesome-blazor) community links</span></span>
