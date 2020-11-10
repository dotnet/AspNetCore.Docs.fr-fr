---
title: 'Partie 3 : ajouter une vue à une application ASP.NET Core MVC'
author: rick-anderson
description: Partie 3 de la série de didacticiels sur ASP.NET Core MVC.
ms.author: riande
ms.date: 8/04/2019
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
uid: tutorials/first-mvc-app/adding-view
ms.openlocfilehash: 56f39643ccd69c6508148374033208eb3b2f25ab
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422715"
---
# <a name="part-3-add-a-view-to-an-aspnet-core-mvc-app"></a>Partie 3 : ajouter une vue à une application ASP.NET Core MVC

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Dans cette section, vous allez modifier la `HelloWorldController` classe pour utiliser [Razor](xref:mvc/views/razor) des fichiers de vue afin d’encapsuler correctement le processus de génération de réponses html sur un client.

Vous créez un fichier de modèle de vue à l’aide de Razor . Razorles modèles de vue basés sur utilisent une extension de fichier *. cshtml* . Ils offrent un moyen élégant pour créer une sortie HTML avec C#.

Actuellement, la méthode `Index` retourne une chaîne avec un message qui est codé en dur dans la classe du contrôleur. Dans la classe `HelloWorldController`, remplacez la méthode `Index` par le code suivant :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

Le code précédent appelle la méthode <xref:Microsoft.AspNetCore.Mvc.Controller.View*> du contrôleur. Il utilise un modèle de vue pour générer une réponse HTML. Les méthodes du contrôleur (également appelées *méthodes d’action* ), comme la méthode `Index` ci-dessus, retournent généralement un <xref:Microsoft.AspNetCore.Mvc.IActionResult> (ou une classe dérivée de <xref:Microsoft.AspNetCore.Mvc.ActionResult>), et non un type comme `string`.

## <a name="add-a-view"></a>Ajouter une vue

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Cliquez avec le bouton droit sur le dossier *Vues* , cliquez sur **Ajouter > Nouveau dossier** , puis nommez le dossier *HelloWorld*.

* Cliquez avec le bouton droit sur le dossier *Vues/HelloWorld* , puis cliquez sur **Ajouter > Nouvel élément**.

* Dans la boîte de dialogue **Ajouter un nouvel élément - MvcMovie**

  * Dans la zone de recherche située en haut à droite, entrez *vue*

  * Sélectionner une **Razor vue**

  * Conservez la valeur de la zone **Nom** , *Index.cshtml*.

  * Sélectionnez **Ajouter**

![Boîte de dialogue Ajouter un nouvel élément](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Ajoutez une vue `Index` pour `HelloWorldController`.

* Ajoutez un nouveau dossier nommé *Views/HelloWorld*.
* Ajoutez un nouveau fichier à la *vues/HelloWorld* nom du dossier *Index.cshtml*.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* Cliquez avec le bouton droit sur le dossier *Vues* , cliquez sur **Ajouter > Nouveau dossier** , puis nommez le dossier *HelloWorld*.
* Cliquez avec le bouton droit sur le dossier *Vues/HelloWorld* , puis cliquez sur **Ajouter > Nouveau fichier**.
* Dans la boîte de dialogue **Nouveau fichier** :

  * Sélectionnez **ASP .net Core** dans le volet gauche.
  * Sélectionnez la **page vue MVC** dans le volet central.
  * Dans la zone **nom** , tapez *index* .
  * Sélectionnez **Nouveau**.

![Boîte de dialogue Ajouter un nouvel élément](adding-view/_static/add_view_mac.png)

---

Remplacez le contenu du fichier de vue *views/HelloWorld/index. cshtml* Razor par ce qui suit :

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

Accédez à `https://localhost:{PORT}/HelloWorld`. La méthode `Index` dans `HelloWorldController` n’a pas accompli beaucoup d’actions. Elle a exécuté l’instruction `return View();`, laquelle spécifiait que la méthode doit utiliser un fichier de modèle de vue pour restituer une réponse au navigateur. Comme aucun nom de fichier de modèle de vue n’a été spécifié, MVC utilise le fichier d’affichage par défaut. Le fichier d’affichage par défaut porte le même nom que la méthode ( `Index` ), le modèle de vue dans */views/HelloWorld/index.cshtml* est donc utilisé. L’image ci-dessous montre la chaîne « Hello from our View Template! » codée en dur dans la vue.

![Fenêtre du navigateur](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a>Changer les vues et les pages de disposition

Sélectionnez les liens du menu ( **MvcMovie** , **Accueil** et **Confidentialité** ). Chaque page affiche la même disposition de menu. La disposition du menu est implémentée dans le fichier *Views/Shared/_Layout.cshtml*. Ouvrez le fichier *Views/Shared/_Layout.cshtml*.

Les modèles de [disposition](xref:mvc/views/layout) vous permettent de spécifier la disposition du conteneur HTML de votre site dans un emplacement unique, puis de l’appliquer sur plusieurs pages de votre site. Recherchez la ligne `@RenderBody()`. `RenderBody` est un espace réservé dans lequel toutes les pages spécifiques aux vues que vous créez s’affichent, *encapsulées* dans la page de disposition. Par exemple, si vous sélectionnez le lien **Confidentialité** , la vue **Views/Home/Privacy.cshtml** est restituée dans la méthode `RenderBody`.

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a>Changer le lien de titre, de pied de page et de menu dans le fichier de disposition

Remplacez le contenu du fichier *Views/Shared/_Layout. cshtml* par le balisage suivant. Les modifications apparaissent en surbrillance :
::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie5/Views/Shared/_Layout.cshtml?highlight=6,14,40)]
::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Le balisage précédent apporte les modifications suivantes :

* Remplacement de 3 occurrences de `MvcMovie` par `Movie App`.
* Remplacement de l’élément d’ancrage `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` par `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.

Dans le balisage précédent, l’ [attribut Tag Helper d’ancrage `asp-area=""`](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) et la valeur d’attribut ont été omis, car cette application n’utilise pas de [zones](xref:mvc/controllers/areas).

**Remarque** : le `Movies` contrôleur n’a pas été implémenté. À ce stade, le lien `Movie App` ne fonctionne pas.

Enregistrer vos modifications et sélectionnez le lien **Confidentialité**. Notez comment le titre sur l’onglet du navigateur affiche **Stratégie de confidentialité - Movie App** au lieu de **Stratégie de confidentialité - Mvc Movie** :

![Onglet Confidentialité](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

Sélectionnez le lien **Accueil** et notez que le titre et le texte d’ancrage affichent également **Movie App**. Nous avons pu effectuer ce changement une fois dans le modèle de disposition et avoir le nouveau texte de lien et le nouveau titre reflétés sur toutes les pages du site.

Examinez le fichier *Views/_ViewStart.cshtml* :

```cshtml
@{
    Layout = "_Layout";
}
```

Le fichier *Views/_ViewStart.cshtml* introduit le fichier *Views/Shared/_Layout.cshtml* dans chaque vue. La propriété `Layout` peut être utilisée pour définir un mode de disposition différent ou lui affecter la valeur `null`. Aucun fichier de disposition n’est donc utilisé.

Modifiez le titre et l’élément `<h2>` de le fichier de vue *Views/HelloWorld/Index.cshtml* :

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

Le titre et l’élément `<h2>` sont légèrement différents afin que vous puissiez voir quel morceau du code modifie l’affichage.

Dans le code ci-dessus, `ViewData["Title"] = "Movie List";` définit la propriété `Title` du dictionnaire `ViewData` sur « Movie List ». La propriété `Title` est utilisée dans l’élément HTML `<title>` dans la page de disposition :

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

Enregistrez la modification et accédez à `https://localhost:{PORT}/HelloWorld`. Notez que le titre du navigateur, l’en-tête principal et les en-têtes secondaires ont changé. (Si vous ne voyez pas les changements dans le navigateur, vous voyez peut-être le contenu mis en cache. Appuyez sur CTRL + F5 dans votre navigateur pour forcer le chargement de la réponse du serveur.) Le titre du navigateur est créé avec la `ViewData["Title"]` valeur définie dans le modèle d’affichage *index. cshtml* et l’application « -Movie » supplémentaire ajoutée au fichier de disposition.

Le contenu du modèle de vue *Index.cshtml* est fusionné avec le modèle de vue *Views/Shared/_Layout.cshtml*. Une réponse HTML unique est envoyée au navigateur. Les modèles de disposition permettent d’apporter facilement des modifications qui s’appliquent à toutes les pages d’une application. Pour en savoir plus, consultez [Disposition](xref:mvc/views/layout).

![Vue Movie List](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

Nos quelques « données » (dans le cas présent, le message « Hello from our View Template! » ) sont toutefois codées en dur. L’application MVC a une vue (« V») et vous avez un contrôleur (« C »), mais pas encore de modèle (« M »).

## <a name="passing-data-from-the-controller-to-the-view"></a>Passage de données du contrôleur vers la vue

Les actions du contrôleur sont appelées en réponse à une demande d’URL entrante. Une classe de contrôleur est l’endroit où le code est écrit et qui gère les demandes du navigateur entrantes. Le contrôleur récupère les données d’une source de données et détermine le type de réponse à envoyer au navigateur. Il est possible d’utiliser des modèles de vue à partir d’un contrôleur pour générer et mettre en forme une réponse HTML au navigateur.

Les contrôleurs sont chargés de fournir les données nécessaires pour qu’un modèle de vue restitue une réponse. N’oubliez pas que les modèles de vue ne doivent **pas** exécuter de logique métier ou interagir directement avec une base de données. Au lieu de cela, un modèle de vue doit fonctionner uniquement avec les données que le contrôleur lui fournit. Préserver cette « séparation des intérêts » permet de maintenir le code clair, testable et facile à gérer.

Actuellement, le `Welcome` méthode présente dans la classe `HelloWorldController` prend un paramètre `name` et un paramètre `ID`, puis sort les valeurs directement dans le navigateur. Au lieu que le contrôleur restitue cette réponse sous forme de chaîne, changez le contrôleur pour qu’il utilise un modèle de vue à la place. Comme le modèle de vue génère une réponse dynamique, les bits de données appropriés doivent être passés du contrôleur à la vue pour générer la réponse. Pour cela, le contrôleur doit placer les données dynamiques (paramètres) dont le modèle de vue a besoin dans un dictionnaire `ViewData` auquel le modèle de vue peut ensuite accéder.

Dans *HelloWorldController.cs* , modifiez la méthode `Welcome` pour ajouter une valeur `Message` et `NumTimes` au dictionnaire `ViewData`. Le dictionnaire `ViewData` est un objet dynamique, ce qui signifie que n’importe quel type peut être utilisé, l’objet `ViewData` ne possède aucune propriété définie tant que vous ne placez pas d’élément dedans. Le [système de liaison de données MVC](xref:mvc/models/model-binding) mappe automatiquement les paramètres nommés (`name` et `numTimes`) provenant de la chaîne de requête dans la barre d’adresse aux paramètres de votre méthode. Le fichier *HelloWorldController.cs* complet ressemble à ceci :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

L’objet dictionnaire `ViewData` contient des données qui seront passées à la vue.

Créez un modèle de vue Welcome nommé *Views/HelloWorld/Welcome.cshtml*.

Vous allez créer une boucle dans le modèle de vue *Welcome.cshtml* qui affiche « Hello » `NumTimes`. Remplacez le contenu de *Views/HelloWorld/Welcome.cshtml* avec le code suivant :

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

Enregistrez vos modifications et accédez à l’URL suivante :

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

Les données sont extraites de l’URL et passées au contrôleur à l’aide du [classeur de modèles MVC](xref:mvc/models/model-binding). Le contrôleur empaquette les données dans un dictionnaire `ViewData` et passe cet objet à la vue. La vue restitue ensuite les données au format HTML dans le navigateur.

![Vue Confidentialité affichant une étiquette Welcome et l’expression « Hello Rick » affichée quatre fois](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

Dans l’exemple ci-dessus, le dictionnaire `ViewData` a été utilisé pour passer des données du contrôleur à une vue. Plus loin dans ce didacticiel, un modèle de vue est utilisé pour passer les données d’un contrôleur à une vue. L’approche basée sur le modèle de vue pour passer des données est généralement préférée à l’approche basée sur le dictionnaire `ViewData`. Pour plus d’informations, consultez [Quand utiliser ViewBag, ViewData ou TempData ](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/).

Dans le didacticiel suivant, une base de données de films est créée.

> [!div class="step-by-step"]
> [Précédent](adding-controller.md) 
>  [Suivant](adding-model.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Dans cette section, vous allez modifier la `HelloWorldController` classe pour utiliser [Razor](xref:mvc/views/razor) des fichiers de vue afin d’encapsuler correctement le processus de génération de réponses html sur un client.

Vous créez un fichier de modèle de vue à l’aide de Razor . Razorles modèles de vue basés sur utilisent une extension de fichier *. cshtml* . Ils offrent un moyen élégant pour créer une sortie HTML avec C#.

Actuellement, la méthode `Index` retourne une chaîne avec un message qui est codé en dur dans la classe du contrôleur. Dans la classe `HelloWorldController`, remplacez la méthode `Index` par le code suivant :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

Le code précédent appelle la méthode <xref:Microsoft.AspNetCore.Mvc.Controller.View*> du contrôleur. Il utilise un modèle de vue pour générer une réponse HTML. Les méthodes du contrôleur (également appelées *méthodes d’action* ), comme la méthode `Index` ci-dessus, retournent généralement un <xref:Microsoft.AspNetCore.Mvc.IActionResult> (ou une classe dérivée de <xref:Microsoft.AspNetCore.Mvc.ActionResult>), et non un type comme `string`.

## <a name="add-a-view"></a>Ajouter une vue

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Cliquez avec le bouton droit sur le dossier *Vues* , cliquez sur **Ajouter > Nouveau dossier** , puis nommez le dossier *HelloWorld*.

* Cliquez avec le bouton droit sur le dossier *Vues/HelloWorld* , puis cliquez sur **Ajouter > Nouvel élément**.

* Dans la boîte de dialogue **Ajouter un nouvel élément - MvcMovie**

  * Dans la zone de recherche située en haut à droite, entrez *vue*

  * Sélectionner une **Razor vue**

  * Conservez la valeur de la zone **Nom** , *Index.cshtml*.

  * Sélectionnez **Ajouter**

![Boîte de dialogue Ajouter un nouvel élément](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Ajoutez une vue `Index` pour `HelloWorldController`.

* Ajoutez un nouveau dossier nommé *Views/HelloWorld*.
* Ajoutez un nouveau fichier à la *vues/HelloWorld* nom du dossier *Index.cshtml*.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* Cliquez avec le bouton droit sur le dossier *Vues* , cliquez sur **Ajouter > Nouveau dossier** , puis nommez le dossier *HelloWorld*.
* Cliquez avec le bouton droit sur le dossier *Vues/HelloWorld* , puis cliquez sur **Ajouter > Nouveau fichier**.
* Dans la boîte de dialogue **Nouveau fichier** :

  * Sélectionnez **Web** dans le volet gauche.
  * Sélectionnez **Fichier HTML vide** dans le volet central.
  * Tapez *Index.cshtml* dans la zone **Nom**.
  * Sélectionnez **Nouveau**.

![Boîte de dialogue Ajouter un nouvel élément](adding-view/_static/add_view_mac.png)

---

Remplacez le contenu du fichier de vue *views/HelloWorld/index. cshtml* Razor par ce qui suit :

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

Accédez à `https://localhost:{PORT}/HelloWorld`. La méthode `Index` dans `HelloWorldController` n’a pas accompli beaucoup d’actions. Elle a exécuté l’instruction `return View();`, laquelle spécifiait que la méthode doit utiliser un fichier de modèle de vue pour restituer une réponse au navigateur. Comme aucun nom de fichier de modèle de vue n’a été spécifié, MVC utilise le fichier d’affichage par défaut. Le fichier d’affichage par défaut a le même nom que la méthode (`Index`), donc */Views/HelloWorld/Index.cshtml* est utilisé. L’image ci-dessous montre la chaîne « Hello from our View Template! » codée en dur dans la vue.

![Fenêtre du navigateur](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a>Changer les vues et les pages de disposition

Sélectionnez les liens du menu ( **MvcMovie** , **Accueil** et **Confidentialité** ). Chaque page affiche la même disposition de menu. La disposition du menu est implémentée dans le fichier *Views/Shared/_Layout.cshtml*. Ouvrez le fichier *Views/Shared/_Layout.cshtml*.

Les modèles de [disposition](xref:mvc/views/layout) vous permettent de spécifier la disposition du conteneur HTML de votre site dans un emplacement unique, puis de l’appliquer sur plusieurs pages de votre site. Recherchez la ligne `@RenderBody()`. `RenderBody` est un espace réservé dans lequel toutes les pages spécifiques aux vues que vous créez s’affichent, *encapsulées* dans la page de disposition. Par exemple, si vous sélectionnez le lien **Confidentialité** , la vue **Views/Home/Privacy.cshtml** est restituée dans la méthode `RenderBody`.

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a>Changer le lien de titre, de pied de page et de menu dans le fichier de disposition

* Dans les éléments de titre et de pied de page, remplacez `MvcMovie` par `Movie App`.
* Modifier l’élément d’ancrage `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` par `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.

Le balisage suivant illustre les changements en surbrillance :

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24,51)]

Dans le balisage précédent, l’ [attribut Tag Helper Ancre](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)`asp-area` a été omis, car cette application n’utilise pas de [zones](xref:mvc/controllers/areas).

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

**Remarque** : le `Movies` contrôleur n’a pas été implémenté. À ce stade, le lien `Movie App` ne fonctionne pas.

Enregistrer vos modifications et sélectionnez le lien **Confidentialité**. Notez comment le titre sur l’onglet du navigateur affiche **Stratégie de confidentialité - Movie App** au lieu de **Stratégie de confidentialité - Mvc Movie** :

![Onglet Confidentialité](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

Sélectionnez le lien **Accueil** et notez que le titre et le texte d’ancrage affichent également **Movie App**. Nous avons pu effectuer ce changement une fois dans le modèle de disposition et avoir le nouveau texte de lien et le nouveau titre reflétés sur toutes les pages du site.

Examinez le fichier *Views/_ViewStart.cshtml* :

```cshtml
@{
    Layout = "_Layout";
}
```

Le fichier *Views/_ViewStart.cshtml* introduit le fichier *Views/Shared/_Layout.cshtml* dans chaque vue. La propriété `Layout` peut être utilisée pour définir un mode de disposition différent ou lui affecter la valeur `null`. Aucun fichier de disposition n’est donc utilisé.

Modifiez le titre et l’élément `<h2>` de le fichier de vue *Views/HelloWorld/Index.cshtml* :

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

Le titre et l’élément `<h2>` sont légèrement différents afin que vous puissiez voir quel morceau du code modifie l’affichage.

Dans le code ci-dessus, `ViewData["Title"] = "Movie List";` définit la propriété `Title` du dictionnaire `ViewData` sur « Movie List ». La propriété `Title` est utilisée dans l’élément HTML `<title>` dans la page de disposition :

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

Enregistrez la modification et accédez à `https://localhost:{PORT}/HelloWorld`. Notez que le titre du navigateur, l’en-tête principal et les en-têtes secondaires ont changé. (Si vous ne voyez pas les changements dans le navigateur, vous voyez peut-être le contenu mis en cache. Appuyez sur CTRL + F5 dans votre navigateur pour forcer le chargement de la réponse du serveur.) Le titre du navigateur est créé avec la `ViewData["Title"]` valeur définie dans le modèle d’affichage *index. cshtml* et l’application « -Movie » supplémentaire ajoutée au fichier de disposition.

Notez également comment le contenu du modèle de vue *Index.cshtml* a été fusionné avec le modèle de vue *Views/Shared/_Layout.cshtml* et qu’une seule réponse HTML a été envoyée au navigateur. Les modèles de disposition permettent d’apporter facilement des modifications qui s’appliquent à toutes les pages de votre application. Pour en savoir plus, consultez [disposition](xref:mvc/views/layout).

![Vue Movie List](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

Nos quelques « données » (dans le cas présent, le message « Hello from our View Template! » ) sont toutefois codées en dur. L’application MVC a une vue (« V») et vous avez un contrôleur (« C »), mais pas encore de modèle (« M »).

## <a name="passing-data-from-the-controller-to-the-view"></a>Passage de données du contrôleur vers la vue

Les actions du contrôleur sont appelées en réponse à une demande d’URL entrante. Une classe de contrôleur est l’endroit où le code est écrit et qui gère les demandes du navigateur entrantes. Le contrôleur récupère les données d’une source de données et détermine le type de réponse à envoyer au navigateur. Il est possible d’utiliser des modèles de vue à partir d’un contrôleur pour générer et mettre en forme une réponse HTML au navigateur.

Les contrôleurs sont chargés de fournir les données nécessaires pour qu’un modèle de vue restitue une réponse. N’oubliez pas que les modèles de vue ne doivent **pas** exécuter de logique métier ou interagir directement avec une base de données. Au lieu de cela, un modèle de vue doit fonctionner uniquement avec les données que le contrôleur lui fournit. Préserver cette « séparation des intérêts » permet de maintenir le code clair, testable et facile à gérer.

Actuellement, le `Welcome` méthode présente dans la classe `HelloWorldController` prend un paramètre `name` et un paramètre `ID`, puis sort les valeurs directement dans le navigateur. Au lieu que le contrôleur restitue cette réponse sous forme de chaîne, changez le contrôleur pour qu’il utilise un modèle de vue à la place. Comme le modèle de vue génère une réponse dynamique, les bits de données appropriés doivent être passés du contrôleur à la vue pour générer la réponse. Pour cela, le contrôleur doit placer les données dynamiques (paramètres) dont le modèle de vue a besoin dans un dictionnaire `ViewData` auquel le modèle de vue peut ensuite accéder.

Dans *HelloWorldController.cs* , modifiez la méthode `Welcome` pour ajouter une valeur `Message` et `NumTimes` au dictionnaire `ViewData`. Le dictionnaire `ViewData` est un objet dynamique, ce qui signifie que n’importe quel type peut être utilisé, l’objet `ViewData` ne possède aucune propriété définie tant que vous ne placez pas d’élément dedans. Le [système de liaison de données MVC](xref:mvc/models/model-binding) mappe automatiquement les paramètres nommés (`name` et `numTimes`) provenant de la chaîne de requête dans la barre d’adresse aux paramètres de votre méthode. Le fichier *HelloWorldController.cs* complet ressemble à ceci :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

L’objet dictionnaire `ViewData` contient des données qui seront passées à la vue.

Créez un modèle de vue Welcome nommé *Views/HelloWorld/Welcome.cshtml*.

Vous allez créer une boucle dans le modèle de vue *Welcome.cshtml* qui affiche « Hello » `NumTimes`. Remplacez le contenu de *Views/HelloWorld/Welcome.cshtml* avec le code suivant :

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

Enregistrez vos modifications et accédez à l’URL suivante :

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

Les données sont extraites de l’URL et passées au contrôleur à l’aide du [classeur de modèles MVC](xref:mvc/models/model-binding). Le contrôleur empaquette les données dans un dictionnaire `ViewData` et passe cet objet à la vue. La vue restitue ensuite les données au format HTML dans le navigateur.

![Vue Confidentialité affichant une étiquette Welcome et l’expression « Hello Rick » affichée quatre fois](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

Dans l’exemple ci-dessus, le dictionnaire `ViewData` a été utilisé pour passer des données du contrôleur à une vue. Plus loin dans ce didacticiel, un modèle de vue est utilisé pour passer les données d’un contrôleur à une vue. L’approche basée sur le modèle de vue pour passer des données est généralement préférée à l’approche basée sur le dictionnaire `ViewData`. Pour plus d’informations, consultez [Quand utiliser ViewBag, ViewData ou TempData ](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/).

Dans le didacticiel suivant, une base de données de films est créée.

> [!div class="step-by-step"]
> [Précédent](adding-controller.md) 
>  [Suivant](adding-model.md)

::: moniker-end
