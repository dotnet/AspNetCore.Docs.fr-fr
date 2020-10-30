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
ms.openlocfilehash: ecdf4f59aca0fe71bbfcfe61a99109127c8b92df
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055710"
---
# <a name="introduction-to-aspnet-core-no-locblazor"></a>Présentation de ASP.NET Core Blazor

Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)

*Bienvenue Blazor !*

Blazor est une infrastructure permettant de créer une interface utilisateur Web interactive côté client avec [.net](/dotnet/standard/tour):

* Créez des interfaces utilisateur riches et interactives en [C#](/dotnet/csharp/) au lieu de [JavaScript](https://www.javascript.com).
* Partagez la logique d’application côté serveur et côté client écrite dans .NET.
* Affichez l’interface utilisateur en langage HTML et CSS pour une large prise en charge des navigateurs, y compris les navigateurs mobiles.
* Intégrez les plateformes d’hébergement modernes, telles que l' [amarrage](/dotnet/standard/microservices-architecture/container-docker-introduction/index).

L’utilisation de .NET dans le développement web côté client offre les avantages suivants :

* Écriture de code dans C# plutôt que dans JavaScript.
* Tirez parti de l’écosystème .NET existant des [bibliothèques .net](/dotnet/standard/class-libraries).
* Partagez la logique de l’application sur le serveur et le client.
* Bénéficiez des performances, de la fiabilité et de la sécurité de .NET.
* Restez productif avec [Visual Studio](https://visualstudio.microsoft.com) sur Windows, Linux et MacOS.
* Développez avec un ensemble commun de langages, de frameworks et d’outils stables, riches en fonctionnalités et faciles à utiliser.

## <a name="components"></a>Components

Blazor les applications sont basées sur des *composants* . Un composant dans Blazor est un élément de l’interface utilisateur, tel qu’une page, une boîte de dialogue ou un formulaire de saisie de données.

Les composants sont des classes .NET C# intégrées à des [assemblys .net](/dotnet/standard/assembly/) qui :

* Définissent la logique de rendu de l’interface utilisateur flexible.
* Gèrent les événements de l’utilisateur.
* Peuvent être imbriqués et réutilisés.
* Peut être partagé et distribué en tant que [ Razor bibliothèques de classes](xref:razor-pages/ui-class) ou [packages NuGet](/nuget/what-is-nuget).

La classe Component est généralement écrite sous la forme d’une [Razor](xref:mvc/views/razor) page de balisage avec une `.razor` extension de fichier. Les composants dans Blazor sont officiellement désignés sous le terme de *Razor composants* . Razor est une syntaxe pour combiner le balisage HTML avec du code C# conçu pour la productivité des développeurs. Razor vous permet de basculer entre le balisage HTML et C# dans le même fichier avec la prise en charge de la programmation [IntelliSense](/visualstudio/ide/using-intellisense) dans Visual Studio. Razor Les pages et MVC utilisent également Razor . Contrairement aux Razor pages et MVC, qui sont générés autour d’un modèle de demande/réponse, les composants sont utilisés spécifiquement pour la logique et la composition de l’interface utilisateur côté client.

Blazor utilise des balises HTML naturelles pour la composition de l’interface utilisateur. Le Razor balisage suivant illustre un composant ( `Dialog.razor` ) qui affiche une boîte de dialogue et traite un événement lorsque l’utilisateur sélectionne un bouton :

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

Dans l’exemple précédent, `OnYes` est une méthode C# déclenchée par l’événement du bouton `onclick` . Le texte de la boîte de dialogue ( `ChildContent` ) et le titre ( `Title` ) sont fournis par le composant suivant qui utilise ce composant dans son interface utilisateur.

Le `Dialog` composant est imbriqué dans un autre composant à l’aide d’une balise html. Dans l’exemple suivant, le `Index` composant ( `Pages/Index.razor` ) utilise le `Dialog` composant précédent. L’attribut de la balise `Title` passe une valeur pour le titre à la `Dialog` propriété du composant `Title` .  Le `Dialog` texte du composant ( `ChildContent` ) est défini par le contenu de l' `<Dialog>` élément. Quand le `Dialog` composant est ajouté au `Index` composant, [IntelliSense dans Visual Studio](/visualstudio/ide/using-intellisense) accélère le développement avec la syntaxe et la saisie semi-automatique des paramètres.

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

La boîte de dialogue s’affiche lorsque vous `Index` accédez au composant dans un navigateur. Lorsque le bouton est sélectionné par l’utilisateur, la console outils de développement du navigateur affiche le message écrit par la `OnYes` méthode :

![Composant de boîte de dialogue rendu dans le navigateur imbriqué dans le composant d’index. La console outils de développement de navigateur affiche le message écrit par le code C# lorsque l’utilisateur sélectionne l’outil Oui ! dans l’interface utilisateur.](index/_static/dialog.png)

Les composants sont rendus dans une représentation en mémoire de la [Document Object Model du navigateur (DOM)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) , appelée *arborescence de rendu* , qui est utilisée pour mettre à jour l’interface utilisateur de manière flexible et efficace.

## Blazor WebAssembly

Blazor WebAssembly est une [infrastructure d’application à page unique (Spa)](/dotnet/architecture/modern-web-apps-azure/choose-between-traditional-web-and-single-page-apps) pour la création d’applications Web interactives côté client avec .net. Blazor WebAssembly utilise des normes Web ouvertes sans plug-ins ni recompilation du code dans d’autres langages. Blazor WebAssembly fonctionne dans tous les navigateurs Web modernes, y compris les navigateurs mobiles.

L’exécution de code .NET dans des navigateurs Web est rendue possible par [Webassembly](https://webassembly.org) (abrégé `wasm` ). WebAssembly est un format bytecode compact optimisé pour un téléchargement rapide et une vitesse d’exécution maximale. WebAssembly est un standard web ouvert pris en charge dans les navigateurs web sans plug-in.

Le code webassembly peut accéder aux fonctionnalités complètes du navigateur via JavaScript, appelé *interopérabilité JavaScript* , souvent abrégé en com *Interop* ou l’interopérabilité *js* . Le code .NET exécuté via WebAssembly dans le navigateur s’exécute dans le bac à sable JavaScript du navigateur avec les protections offertes par le bac à sable contre les actions malveillantes sur l’ordinateur client.

![::: No-Loc (éblouissant webassembly) ::: exécute le code .NET dans le navigateur avec webassembly.](index/_static/blazor-webassembly.png)

Quand une Blazor WebAssembly application est générée et exécutée dans un navigateur :

* Les fichiers de code C# et les Razor fichiers sont compilés dans des assemblys .net.
* Les assemblys et le [Runtime .net](/dotnet/framework/get-started/overview) sont téléchargés dans le navigateur.
* Blazor WebAssembly amorce le Runtime .NET et configure le runtime pour charger les assemblys de l’application. Le Blazor WebAssembly Runtime utilise l’interopérabilité JavaScript pour gérer les appels de l’API de manipulation et du navigateur DOM.

La taille de l’application publiée, sa *taille de charge utile* , est un facteur de performance essentiel pour qu’une application soit facile à utiliser. Le téléchargement d’une application volumineuse dans un navigateur prend un certain temps, ce qui nuit à l’expérience utilisateur. Blazor WebAssembly optimise la taille de la charge utile pour réduire les temps de téléchargement :

::: moniker range=">= aspnetcore-5.0"

* Le code inutilisé est supprimé de l’application lorsqu’elle est publiée par le [découpage en langage intermédiaire (il)](xref:blazor/host-and-deploy/configure-trimmer).
* Réponses HTTP compressées.
* Le runtime .NET et les assemblys sont mis en cache dans le navigateur.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* Du code inutilisé est extrait de l’application lorsqu’elle est publiée par l’[éditeur de liens de langage intermédiaire (IL)](xref:blazor/host-and-deploy/configure-linker).
* Réponses HTTP compressées.
* Le runtime .NET et les assemblys sont mis en cache dans le navigateur.

::: moniker-end

## Blazor Server

Blazor dissocie la logique de rendu des composants de l’application des mises à jour de l’interface utilisateur. *Blazor Server* prend en charge l’hébergement de Razor composants sur le serveur dans une application ASP.net core. Les mises à jour de l’interface utilisateur sont gérées via une [SignalR](xref:signalr/introduction) connexion.

Le Runtime gère :

* Envoi d’événements d’interface utilisateur du navigateur au serveur.
* Application des mises à jour de l’interface utilisateur au composant rendu qui sont renvoyées par le serveur.

La connexion utilisée par Blazor Server pour communiquer avec le navigateur est également utilisée pour gérer les appels Interop JavaScript.

![::: No-Loc (serveur éblouissant) ::: exécute le code .NET sur le serveur et interagit avec le Document Object Model sur le client via un ::: No-Loc (Signalr) ::: Connection](index/_static/blazor-server.png)

## <a name="javascript-interop"></a>Interopérabilité JavaScript

Pour les applications qui nécessitent des bibliothèques JavaScript tierces et l’accès à des API de navigateur, les composants interagissent avec JavaScript. Les composants peuvent utiliser les mêmes API ou bibliothèques que JavaScript. Le code c# peut appeler du code [JavaScript](xref:blazor/call-javascript-from-dotnet)et le code JavaScript peut faire [appel à](xref:blazor/call-dotnet-from-javascript)du code c#.

## <a name="code-sharing-and-net-standard"></a>Partage de code et .NET Standard

Blazor implémente le [.NET standard](/dotnet/standard/net-standard), qui permet Blazor aux projets de référencer des bibliothèques qui se conforment aux spécifications de .NET standard. .NET Standard est une spécification formelle d’API .NET qui sont communes aux implémentations .NET. .NET Standard bibliothèques de classes peuvent être partagées entre différentes plateformes .NET, telles que Blazor , .NET Framework, .net Core, Xamarin, mono et Unity.

Les API qui ne sont pas applicables à l’intérieur d’un navigateur web (par exemple l’accès au système de fichiers, l’ouverture d’un socket et le threading) lèvent une <xref:System.PlatformNotSupportedException>.

## <a name="additional-resources"></a>Ressources supplémentaires

* [WebAssembly](https://webassembly.org)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor-webassembly>
* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>
* [C# Guide](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [HTML](https://www.w3.org/html/)
* [Isard Blazor ](https://github.com/AdrienTorris/awesome-blazor) Liens vers la communauté
