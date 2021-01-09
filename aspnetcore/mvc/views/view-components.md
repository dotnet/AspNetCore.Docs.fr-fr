---
title: Composants de vue dans ASP.NET Core
author: rick-anderson
description: Découvrez comment les composants de vue sont utilisés dans ASP.NET Core et comment les ajouter à des applications.
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
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
uid: mvc/views/view-components
ms.openlocfilehash: 61fcc07aeb30db15014fb716194328d366f27859
ms.sourcegitcommit: 97243663fd46c721660e77ef652fe2190a461f81
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2021
ms.locfileid: "98058335"
---
# <a name="view-components-in-aspnet-core"></a>Composants de vue dans ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="view-components"></a>Composants de vue

Les composants de vue sont similaires aux vues partielles, mais ils sont beaucoup plus puissants. Les composants de vue n’utilisent pas la liaison de données ; ils dépendent uniquement des données fournies en réponse à l’appel d’un composant de vue. Cet article a été écrit à l’aide de contrôleurs et de vues, mais les composants de vue fonctionnent également avec les Razor pages.

Un composant de vue a les caractéristiques suivantes :

* Il effectue le rendu d’un bloc de code au lieu d’une réponse entière.
* Il garantit la même « séparation des préoccupations » et offre les mêmes avantages de testabilité qu’entre un contrôleur et une vue.
* Il peut avoir des paramètres et une logique métier.
* Il est généralement appelé à partir d’une page de disposition.

Les composants de vue sont conçus pour être utilisés là où vous avez une logique de rendu réutilisable qui est trop complexe pour une vue partielle. Ils ciblent les vues suivantes, par exemple :

* Menus de navigation dynamiques
* Nuage de mots clés (pour l’interrogation de la base de données)
* Panneau de connexion
* Panier d’achat
* Articles récemment publiés
* Contenu de la barre latérale dans un blog standard
* Panneau de connexion inclus dans chaque page pour afficher les liens de connexion ou déconnexion, en fonction de l’état de connexion de l’utilisateur

Un composant de vue a deux éléments : sa classe (généralement dérivée de [ViewComponent](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponent)) et le résultat qu’il retourne (en général, une vue). Comme les contrôleurs, un composant de vue peut être un OCT, mais la plupart des développeurs préfèrent utiliser les méthodes et propriétés dérivées de `ViewComponent`.

Si vous envisagez que les composants de vue répondent aux spécifications d’une application, envisagez plutôt d’utiliser des Razor composants. Razor les composants combinent également le balisage avec du code C# pour produire des unités d’interface utilisateur réutilisables. Razor les composants sont conçus pour la productivité des développeurs lorsqu’ils fournissent la logique de l’interface utilisateur côté client et la composition. Pour plus d'informations, consultez <xref:blazor/components/index>. Pour plus d’informations sur la façon d’incorporer des Razor composants dans une application MVC ou Razor pages, consultez <xref:blazor/components/prerendering-and-integration?pivots=server> .

## <a name="creating-a-view-component"></a>Création d’un composant de vue

Cette section présente les exigences générales relatives à la création d’un composant de vue. Plus loin dans cet article, nous décrirons chaque étape en détail et nous créerons un composant de vue.

### <a name="the-view-component-class"></a>Classe de composant de vue

Vous pouvez créer une classe de composant de vue à l’aide d’une des méthodes suivantes :

* En la dérivant de *ViewComponent*
* En décorant une classe avec l’attribut `[ViewComponent]` ou en dérivant la classe d’une classe définie avec l’attribut `[ViewComponent]`
* En créant une classe dont le nom se termine par le suffixe *ViewComponent*

Comme les contrôleurs, les composants de vue doivent être des classes publiques, non imbriquées et non abstraites. Le nom du composant de vue correspond au nom de la classe sans le suffixe ViewComponent. Il peut également être spécifié explicitement à l’aide de la propriété `ViewComponentAttribute.Name`.

Une classe de composant de vue :

* Prend entièrement en charge [l’injection de dépendances](../../fundamentals/dependency-injection.md) dans le constructeur

* N’intervient pas dans le cycle de vie du contrôleur, ce qui signifie que vous ne pouvez pas utiliser de [filtres](../controllers/filters.md) dans un composant de vue

### <a name="view-component-methods"></a>Méthodes d’un composant de vue

Un composant de vue définit sa logique dans une méthode `InvokeAsync` qui retourne un `Task<IViewComponentResult>`, ou dans une méthode `Invoke` synchrone qui retourne un `IViewComponentResult`. Les paramètres sont fournis directement en réponse à l’appel du composant de vue ; ils ne proviennent pas de la liaison de données. Un composant de vue ne traite jamais une requête directement. En règle générale, un composant de vue initialise un modèle et le passe à une vue en appelant la méthode `View`. En résumé, les méthodes d’un composant de vue :

* Définissent une `InvokeAsync` méthode qui retourne un `Task<IViewComponentResult>` ou une méthode `Invoke` synchrone qui retourne un `IViewComponentResult`.
* Initialise généralement un modèle et le passe à une vue en appelant la `ViewComponent` `View` méthode.
* Les paramètres proviennent de la méthode appelante, et non pas de HTTP. Il n’y a pas de liaison de modèle.
* Ne sont pas accessibles directement comme point de terminaison HTTP. Elles sont appelées depuis votre code (généralement dans une vue). Un composant de vue ne traite jamais une requête.
* Sont surchargées sur la signature, plutôt que sur des détails de la requête HTTP en cours.

### <a name="view-search-path"></a>Chemin de recherche de la vue

Le Runtime recherche la vue dans les chemins suivants :

* /Views/{Controller Name}/Components/{View Component Name}/{View Name}
* /Views/Shared/Components/{View Component Name}/{View Name}
* /Pages/Shared/Components/{View Component Name}/{View Name}

Le chemin de recherche s’applique aux projets qui utilisent des contrôleurs, des vues et des Razor pages.

Le nom de la vue par défaut pour un composant de vue est *Default*. Votre fichier de vue est donc normalement appelé *Default.cshtml*. Vous pouvez spécifier un nom de vue différent quand vous créez le résultat du composant de vue ou quand vous appelez la méthode `View`.

Nous vous recommandons de nommer le fichier de vue *Default.cshtml* et d’utiliser le chemin *Views/Shared/Components/{View Component Name}/{View Name}*. Le composant de vue `PriorityList` montré dans cet exemple utilise le chemin *Views/Shared/Components/PriorityList/Default.cshtml* pour la vue.

### <a name="customize-the-view-search-path"></a>Personnaliser le chemin de recherche d’affichage

Pour personnaliser le chemin de recherche d’affichage, la Razor collection de la modification <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.ViewLocationFormats> . Par exemple, pour rechercher des affichages dans le chemin d’accès « /Components/{View Component Name}/{View Name} », ajoutez un nouvel élément à la collection :

[!code-csharp[](view-components/samples_snapshot/2.x/Startup.cs?name=snippet_ViewLocationFormats&highlight=4)]

Dans le code précédent, l’espace réservé « {0} » représente le chemin d’accès « Components/{View Component Name}/{view Name} ».

## <a name="invoking-a-view-component"></a>Appel d’un composant de vue

Pour utiliser le composant de vue, appelez-le dans une vue, comme ci-dessous :

```cshtml
@await Component.InvokeAsync("Name of view component", {Anonymous Type Containing Parameters})
```

Les paramètres sont passés à la méthode `InvokeAsync`. Le composant de vue `PriorityList` décrit dans cet article est appelé du fichier de vue *Views/ToDo/Index.cshtml*. Dans le code suivant, la méthode `InvokeAsync` est appelée avec deux paramètres :

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

::: moniker range=">= aspnetcore-1.1"

## <a name="invoking-a-view-component-as-a-tag-helper"></a>Appel d’un composant de vue en tant que Tag Helper

Dans ASP.NET Core 1.1 et les versions ultérieures, vous pouvez appeler un composant de vue en tant que [Tag Helper](xref:mvc/views/tag-helpers/intro) :

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

Les paramètres de méthode et de classe de casse Pascal pour les Tag Helpers sont convertis en [casse kebab](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101) (mots séparés par des tirets). Le Tag Helper utilisé pour appeler un composant de vue contient l’élément `<vc></vc>`. Le composant de vue est spécifié de la façon suivante :

```cshtml
<vc:[view-component-name]
  parameter1="parameter1 value"
  parameter2="parameter2 value">
</vc:[view-component-name]>
```

Pour utiliser un composant de vue en tant que Tag Helper, inscrivez l’assembly contenant le composant de vue à l’aide de la directive `@addTagHelper`. Si votre composant de vue se trouve dans un assembly appelé `MyWebApp`, ajoutez la directive suivante dans le fichier *_ViewImports.cshtml* :

```cshtml
@addTagHelper *, MyWebApp
```

Vous pouvez inscrire un composant de vue en tant que Tag Helper dans n’importe quel fichier qui référence ce composant. Pour plus d’informations sur l’inscription des Tag Helpers, consultez [Gestion de l’étendue des Tag Helpers](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope).

Méthode `InvokeAsync` utilisée dans ce didacticiel :

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

Dans le balisage Tag Helper :

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

Dans l’exemple ci-dessus, le composant de vue `PriorityList` devient `priority-list`. Les paramètres sont passés au composant de vue sous forme d’attributs dans la casse kebab.

::: moniker-end

### <a name="invoking-a-view-component-directly-from-a-controller"></a>Appel d’un composant de vue directement à partir d’un contrôleur

Les composants de vue sont généralement appelés depuis une vue, mais ils peuvent aussi être appelés directement d’une méthode de contrôleur. Les composants de vue ne définissent pas de points de terminaison comme le font les contrôleurs, mais vous pouvez facilement implémenter une action de contrôleur qui retourne le contenu d’un `ViewComponentResult`.

Dans cet exemple, le composant de vue est appelé directement du contrôleur :

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

## <a name="walkthrough-creating-a-simple-view-component"></a>Procédure pas à pas : Création d’un composant de vue simple

[Téléchargez](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample), générez et testez le code de démarrage. Il s’agit d’un projet simple avec un contrôleur `ToDo` qui affiche une liste de tâches *ToDo*.

![Liste des tâches Todo](view-components/_static/2dos.png)

### <a name="add-a-viewcomponent-class"></a>Ajouter une classe ViewComponent

Créez un dossier *ViewComponents* et ajoutez la classe `PriorityListViewComponent` suivante :

[!code-csharp[](view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponent1.cs?name=snippet1)]

Remarques sur le code :

* Les classes ViewComponent peuvent être ajoutées à **n’importe** quel dossier dans le projet.
* Étant donné que le nom de classe PriorityList **ViewComponent** se termine par le suffixe **ViewComponent**, le Runtime utilise la chaîne « PriorityList » pour référencer le composant de la classe à partir d’une vue. J’expliquerai ce point plus en détail plus tard.
* L’attribut `[ViewComponent]` peut changer le nom utilisé pour faire référence à un composant de vue. Par exemple, nous pourrions avoir nommé la classe `XYZ` et appliqué l’attribut `ViewComponent` :

  ```csharp
  [ViewComponent(Name = "PriorityList")]
     public class XYZ : ViewComponent
     ```

* L’attribut `[ViewComponent]` ci-dessus indique au sélecteur du composant de vue d’utiliser le nom `PriorityList` pour rechercher les vues associées au composant et d’utiliser la chaîne « PriorityList » pour faire référence au composant de la classe à partir d’une vue. J’expliquerai ce point plus en détail plus tard.
* Le composant utilise [l’injection de dépendances](../../fundamentals/dependency-injection.md) pour rendre le contexte de données disponible.
* `InvokeAsync` expose une méthode qui peut être appelée à partir d’une vue et qui peut prendre un nombre arbitraire d’arguments.
* La méthode `InvokeAsync` retourne l’ensemble des tâches `ToDo` qui correspondent aux paramètres `isDone` et `maxPriority` spécifiés.

### <a name="create-the-view-component-no-locrazor-view"></a>Créer la vue de composant de vue Razor

* Créez le dossier *Views/Shared/Components*. Ce dossier **doit** être nommé *Components*.

* Créez le dossier *Views/Shared/Components/PriorityList*. Ce nom de dossier doit correspondre au nom de la classe du composant de vue, ou au nom de la classe sans le suffixe (dans le cas où vous avez suivi la convention et utilisé le suffixe *ViewComponent* dans le nom de la classe). Si vous avez utilisé l’attribut `ViewComponent`, le nom de la classe doit correspondre à la désignation de l’attribut.

* Créer une vue *Views/Shared/Components/PriorityList/default. cshtml* Razor :


  [!code-cshtml[](view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/Default1.cshtml)]

   La Razor vue prend la liste `TodoItem` et les affiche. Si la méthode `InvokeAsync` du composant de vue ne passe pas le nom de la vue (comme dans notre exemple), le nom de vue *Default* est utilisé par convention. Je vous montrerai comment passer le nom de la vue plus tard dans ce didacticiel. Pour substituer le style par défaut d’un contrôleur spécifique, ajoutez une vue dans le dossier des vues du contrôleur (par exemple, *Views/ToDo/Components/PriorityList/Default.cshtml)*.

    Si le composant de vue est spécifique au contrôleur, vous pouvez l’ajouter au dossier du contrôleur (*Views/ToDo/Components/PriorityList/Default.cshtml*).

* Ajoutez une balise `div` contenant un appel au composant de liste des priorités à la fin du fichier *Views/ToDo/index.cshtml* :

    [!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFirst.cshtml?range=34-38)]

Le balisage `@await Component.InvokeAsync` montre la syntaxe utilisée pour appeler un composant de vue. Le premier argument est le nom du composant à appeler. Les paramètres suivants sont passés au composant. `InvokeAsync` peut prendre un nombre arbitraire d’arguments.

Tester l'application. L’image suivante montre la liste des tâches ToDo et les tâches prioritaires :

![liste des tâches ToDo et tâches prioritaires](view-components/_static/pi.png)

Vous pouvez également appeler le composant de vue directement à partir du contrôleur :

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

![tâches prioritaires retournées par IndexVC](view-components/_static/indexvc.png)

### <a name="specifying-a-view-name"></a>Spécification d’un nom de vue

Un composant de vue complexe peut avoir besoin de spécifier une autre vue que la vue par défaut dans certaines conditions. Le code suivant montre comment spécifier la vue « PVC » à l’aide de la méthode `InvokeAsync`. Mettez à jour la méthode `InvokeAsync` dans la classe `PriorityListViewComponent`.

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponentFinal.cs?highlight=4,5,6,7,8,9&range=28-39)]

Copiez le fichier *Views/Shared/Components/PriorityList/Default.cshtml* dans une vue nommée *Views/Shared/Components/PriorityList/PVC.cshtml*. Ajoutez un titre pour indiquer que la vue PVC est utilisée.

[!code-cshtml[](../../mvc/views/view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/PVC.cshtml?highlight=3)]

Mettez à jour *Views/ToDo/Index.cshtml* :

<!-- Views/ToDo/Index.cshtml is never imported, so change to test tutorial -->

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

Exécutez l’application et vérifiez la vue PVC.

![Composant de vue des priorités](view-components/_static/pvc.png)

Si la vue PVC ne s’affiche pas, vérifiez que vous appelez le composant de vue avec une priorité supérieure ou égale à 4.

### <a name="examine-the-view-path"></a>Vérifier le chemin de la vue

* Changez le paramètre de priorité à une priorité inférieure ou égale à 3 pour empêcher l’affichage de la vue des priorités.
* Renommez temporairement *Views/ToDo/Components/PriorityList/Default.cshtml* en *1Default.cshtml*.
* Testez l’application. Vous obtenez l’erreur suivante :

   ```
   An unhandled exception occurred while processing the request.
   InvalidOperationException: The view 'Components/PriorityList/Default' wasn't found. The following locations were searched:
   /Views/ToDo/Components/PriorityList/Default.cshtml
   /Views/Shared/Components/PriorityList/Default.cshtml
   EnsureSuccessful
   ```

* Copiez *Views/ToDo/Components/PriorityList/1Default.cshtml* vers *Views/Shared/Components/PriorityList/Default.cshtml*.
* Ajoutez du balisage à la vue ToDo *Shared* du composant de vue pour indiquer que la vue provient du dossier *Shared*.
* Testez la vue de composant **Shared**.

![Affichage de tâches ToDo avec la vue de composant Shared](view-components/_static/shared.png)

### <a name="avoiding-hard-coded-strings"></a>Éviter les chaînes codées en dur

Pour éviter d’éventuels problèmes au moment de la compilation, vous pouvez remplacer le nom du composant de vue codé en dur par le nom de la classe. Créez le composant de vue sans le suffixe « ViewComponent » :

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityList.cs?highlight=10&range=5-35)]

Ajoutez une `using` instruction à votre Razor fichier de vue et utilisez l' `nameof` opérateur :

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexNameof.cshtml?range=1-6,35-)]

## <a name="perform-synchronous-work"></a>Effectuer un travail synchrone

Le framework gère l’appel d’une méthode `Invoke` synchrone si vous n’avez pas besoin d’effectuer un travail asynchrone. La méthode suivante crée un composant de vue `Invoke` synchrone :

```csharp
public class PriorityList : ViewComponent
{
    public IViewComponentResult Invoke(int maxPriority, bool isDone)
    {
        var items = new List<string> { $"maxPriority: {maxPriority}", $"isDone: {isDone}" };
        return View(items);
    }
}
```

Le fichier du composant de vue Razor répertorie les chaînes passées à la `Invoke` méthode (*views/domotique/Components/PriorityList/default. cshtml*) :

```cshtml
@model List<string>

<h3>Priority Items</h3>
<ul>
    @foreach (var item in Model)
    {
        <li>@item</li>
    }
</ul>
```

::: moniker range=">= aspnetcore-1.1"

Le composant de vue est appelé dans un Razor fichier (par exemple, *views/orig/index. cshtml*) à l’aide de l’une des approches suivantes :

* <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>
* [Tag Helper](xref:mvc/views/tag-helpers/intro)

Pour utiliser l’approche <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>, appelez `Component.InvokeAsync` :

::: moniker-end

::: moniker range="< aspnetcore-1.1"

Le composant de vue est appelé dans un Razor fichier (par exemple, *views/orig/index. cshtml*) avec <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> .

Appelez `Component.InvokeAsync` :

::: moniker-end

```cshtml
@await Component.InvokeAsync(nameof(PriorityList), new { maxPriority = 4, isDone = true })
```

::: moniker range=">= aspnetcore-1.1"

Pour utiliser le Tag Helper, inscrivez l’assembly contenant le composant de vue à l’aide de la directive `@addTagHelper` (le composant de vue se trouve dans un assembly appelé `MyWebApp`) :

```cshtml
@addTagHelper *, MyWebApp
```

Utilisez le tag Helper du composant View dans le Razor fichier de balisage :

```cshtml
<vc:priority-list max-priority="999" is-done="false">
</vc:priority-list>
```

::: moniker-end

La signature de méthode de `PriorityList.Invoke` est synchrone, mais elle Razor recherche et appelle la méthode avec `Component.InvokeAsync` dans le fichier de balisage.

## <a name="all-view-component-parameters-are-required"></a>Tous les paramètres du composant de vue sont requis.

Chaque paramètre d’un composant de vue est un attribut requis. Consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore/issues/5011). Si un paramètre est omis :

* La signature de la méthode `InvokeAsync` ne correspond pas, et la méthode n’est pas exécutée.
* ViewComponent n’affiche aucun balisage.
* Aucune erreur n’est levée.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Injection de dépendances dans les vues](xref:mvc/views/dependency-injection)
