---
title: 'Partie 3 : ajouter une vue à une application ASP.NET Core MVC'
author: rick-anderson
description: Partie 3 de la série de didacticiels sur ASP.NET Core MVC.
ms.author: riande
ms.date: 01/28/2021
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
ms.custom: contperf-fy21q3
ms.openlocfilehash: 1542e44fcc6d0ae22fb1a759ea3a3ed1d866cbc7
ms.sourcegitcommit: 19a004ff2be73876a9ef0f1ac44d0331849ad159
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2021
ms.locfileid: "99804567"
---
# <a name="part-3-add-a-view-to-an-aspnet-core-mvc-app"></a>Partie 3 : ajouter une vue à une application ASP.NET Core MVC

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Dans cette section, vous allez modifier la `HelloWorldController` classe pour utiliser des [Razor](xref:mvc/views/razor) fichiers de vue. Cela encapsule correctement le processus de génération de réponses HTML sur un client.

Les modèles de vue sont créés à l’aide de Razor . Razormodèles de vue basés sur :

* Avoir une extension de fichier *. cshtml* .
* Fournissez un moyen élégant de créer une sortie HTML avec C#.

Actuellement `Index` , la méthode retourne une chaîne avec un message dans la classe Controller. Dans la classe `HelloWorldController`, remplacez la méthode `Index` par le code suivant :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

Le code précédent :

* Appelle la méthode du contrôleur <xref:Microsoft.AspNetCore.Mvc.Controller.View*> .
* Utilise un modèle de vue pour générer une réponse HTML.

Méthodes de contrôleur :

* Sont appelées *méthodes d’action*.  Par exemple, la `Index` méthode d’action dans le code précédent.
* Retourne généralement un <xref:Microsoft.AspNetCore.Mvc.IActionResult> ou une classe dérivée de <xref:Microsoft.AspNetCore.Mvc.ActionResult> , et non un type comme `string` .

## <a name="add-a-view"></a>Ajouter une vue

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Cliquez avec le bouton droit sur le dossier *views* , puis **Ajoutez > nouveau dossier** et nommez le dossier *HelloWorld*.

Cliquez avec le bouton droit sur le dossier *views/HelloWorld* , puis **Ajoutez > nouvel élément**.

Dans la boîte de dialogue **Ajouter un nouvel élément-MvcMovie** :

* Dans la zone de recherche située en haut à droite, entrez *vue*
* Sélectionner une **Razor vue**
* Conservez la valeur de la zone **Nom**, *Index.cshtml*.
* Sélectionnez **Ajouter**

![Boîte de dialogue Ajouter un nouvel élément](adding-view/_static/add_view50.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Ajoutez une `Index` vue pour le `HelloWorldController` :

* Ajoutez un nouveau dossier nommé *Views/HelloWorld*.
* Ajoutez un nouveau fichier à la *vues/HelloWorld* nom du dossier *Index.cshtml*.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

Cliquez avec le contrôle sur le dossier *views* , puis **Ajoutez > nouveau dossier** et nommez le dossier *HelloWorld*.

Cliquez avec le contrôle sur le dossier *views/HelloWorld* , puis **Ajoutez > nouveau fichier**.

Dans la boîte de dialogue **Nouveau fichier** :

* Dans le volet gauche, sélectionnez **ASP.net Core** .
* Sélectionnez **Razor affichage-vide** dans le volet central.
* Dans la zone **nom** , tapez *index* .
* Sélectionnez **Nouveau**.

  ![Boîte de dialogue Ajouter un nouvel élément](adding-view/_static/add_view_macVSM8.9.png)

---

Remplacez le contenu du fichier de vue *views/HelloWorld/index. cshtml* Razor par ce qui suit :

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

Accédez à `https://localhost:{PORT}/HelloWorld` :

* La `Index` méthode dans le a `HelloWorldController` exécuté l’instruction `return View();` , qui spécifie que la méthode doit utiliser un fichier de modèle de vue pour afficher une réponse dans le navigateur.
* Comme un nom de fichier de modèle de vue n’A pas été spécifié, MVC utilise par défaut le fichier de vue par défaut. Lorsque le nom du fichier de vue n’est pas spécifié, la vue par défaut est retournée. L’affichage par défaut porte le même nom que la méthode d’action, `Index` dans cet exemple. Le modèle de vue */views/HelloWorld/index.cshtml* est utilisé.
* L’image suivante montre la chaîne « Hello from the View template ! » codé en dur dans la vue :

  ![Fenêtre du navigateur](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a>Changer les vues et les pages de disposition

Sélectionnez le menu liens **MvcMovie**, **orig** et **Privacy**. Chaque page affiche la même disposition de menu. La disposition du menu est implémentée dans le fichier *Views/Shared/_Layout.cshtml*.

Ouvrez le fichier *Views/Shared/_Layout.cshtml*.

Les modèles de [disposition](xref:mvc/views/layout) permettent :

* Spécification de la disposition de conteneur HTML d’un site dans un emplacement unique.
* Application de la disposition de conteneur HTML sur plusieurs pages du site.

Recherchez la ligne `@RenderBody()`. `RenderBody` est un espace réservé dans lequel toutes les pages spécifiques aux vues que vous créez s’affichent, *encapsulées* dans la page de disposition. Par exemple, si vous sélectionnez le lien **Confidentialité**, la vue **Views/Home/Privacy.cshtml** est restituée dans la méthode `RenderBody`.

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

* Trois occurrences de `MvcMovie` à `Movie App` .
* Remplacement de l’élément d’ancrage `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` par `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.

Dans le balisage précédent, l' `asp-area=""` attribut et la valeur [d’attribut d’ancre de balise d’ancrage](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) ont été omis, car cette application n’utilise pas de [zones](xref:mvc/controllers/areas).

**Remarque**: le `Movies` contrôleur n’a pas été implémenté. À ce stade, le `Movie App` lien n’est pas fonctionnel.

Enregistrez les modifications et sélectionnez le lien **confidentialité** . Notez comment le titre sur l’onglet du navigateur affiche **Stratégie de confidentialité - Movie App** au lieu de **Stratégie de confidentialité - Mvc Movie** :

![Onglet Confidentialité](~/tutorials/first-mvc-app/adding-view/_static/privacy50.png)

Sélectionnez le lien **Home** (Accueil).

Notez que le titre et le texte d’ancrage affichent l' **application vidéo**. Les modifications ont été apportées une fois dans le modèle de disposition et toutes les pages du site reflètent le nouveau texte de lien et le nouveau titre.

Examinez le fichier *Views/_ViewStart.cshtml* :

```cshtml
@{
    Layout = "_Layout";
}
```

Le fichier *Views/_ViewStart.cshtml* introduit le fichier *Views/Shared/_Layout.cshtml* dans chaque vue. La propriété `Layout` peut être utilisée pour définir un mode de disposition différent ou lui affecter la valeur `null`. Aucun fichier de disposition n’est donc utilisé.

Ouvrez le fichier de vue *views/HelloWorld/index. cshtml* .

Modifiez le titre et l' `<h2>` élément comme indiqué dans les éléments suivants :

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

Le titre et l' `<h2>` élément sont légèrement différents. il est donc clair qu’une partie du code modifie l’affichage.

Dans le code ci-dessus, `ViewData["Title"] = "Movie List";` définit la propriété `Title` du dictionnaire `ViewData` sur « Movie List ». La propriété `Title` est utilisée dans l’élément HTML `<title>` dans la page de disposition :

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

Enregistrez la modification et accédez à `https://localhost:{PORT}/HelloWorld`.

Notez que les éléments suivants ont été modifiés :

* Titre du navigateur.
* Titre principal.
* Titres secondaires.

S’il n’y a aucune modification dans le navigateur, il peut s’agir d’un contenu mis en cache affiché. Appuyez sur CTRL + F5 dans le navigateur pour forcer le chargement de la réponse du serveur. Le titre du navigateur est créé avec la valeur `ViewData["Title"]` que nous avons définie dans le modèle de vue *Index.cshtml* et la chaîne « - Movie App » ajoutée dans le fichier de disposition.

Le contenu du modèle de vue *Index.cshtml* est fusionné avec le modèle de vue *Views/Shared/_Layout.cshtml*. Une réponse HTML unique est envoyée au navigateur. Les modèles de disposition permettent d’apporter facilement des modifications qui s’appliquent à toutes les pages d’une application. Pour en savoir plus, consultez [Disposition](xref:mvc/views/layout).

![Vue Movie List](~/tutorials/first-mvc-app/adding-view/_static/hell50.png)

Le petit peu de « données », le « Bonjour de notre modèle de vue ! » message, est toutefois codé en dur. L’application MVC a un « V » (affichage), un « C » (contrôleur), mais pas encore de « M » (modèle).

## <a name="passing-data-from-the-controller-to-the-view"></a>Passage de données du contrôleur vers la vue

Les actions du contrôleur sont appelées en réponse à une demande d’URL entrante. Une classe de contrôleur est l’endroit où le code est écrit et qui gère les demandes du navigateur entrantes. Le contrôleur récupère les données d’une source de données et détermine le type de réponse à envoyer au navigateur. Il est possible d’utiliser des modèles de vue à partir d’un contrôleur pour générer et mettre en forme une réponse HTML au navigateur.

Les contrôleurs sont chargés de fournir les données nécessaires pour qu’un modèle de vue restitue une réponse.

Les modèles de vue ne doivent **pas**:

* Logique métier
* Interagir directement avec une base de données.

Un modèle de vue doit fonctionner uniquement avec les données qui lui sont fournies par le contrôleur. Le maintien de cette « séparation des préoccupations » vous aide à conserver le code :

* Claire.
* Susceptible.
* Facile à gérer.

Actuellement, le `Welcome` méthode présente dans la classe `HelloWorldController` prend un paramètre `name` et un paramètre `ID`, puis sort les valeurs directement dans le navigateur.

Au lieu que le contrôleur restitue cette réponse sous forme de chaîne, changez le contrôleur pour qu’il utilise un modèle de vue à la place. Le modèle de vue génère une réponse dynamique, ce qui signifie que les données appropriées doivent être transmises du contrôleur à la vue pour générer la réponse. Pour ce faire, le contrôleur doit placer les données dynamiques (paramètres) dont le modèle de vue a besoin dans un `ViewData` dictionnaire. Le modèle de vue peut ensuite accéder aux données dynamiques.

Dans *HelloWorldController.cs*, modifiez la méthode `Welcome` pour ajouter une valeur `Message` et `NumTimes` au dictionnaire `ViewData`.

Le `ViewData` dictionnaire est un objet dynamique, ce qui signifie que tout type peut être utilisé. L' `ViewData` objet n’a pas de propriétés définies tant qu’un objet n’est pas ajouté. Le [système de liaison de modèle MVC](xref:mvc/models/model-binding) mappe automatiquement les paramètres nommés `name` et `numTimes` de la chaîne de requête aux paramètres de la méthode. L’ensemble complet `HelloWorldController` :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5&highlight=13-19)]

L’objet dictionnaire `ViewData` contient des données qui seront passées à la vue.

Créez un modèle de vue Welcome nommé *Views/HelloWorld/Welcome.cshtml*.

Vous allez créer une boucle dans le modèle de vue *Welcome.cshtml* qui affiche « Hello » `NumTimes`. Remplacez le contenu de *Views/HelloWorld/Welcome.cshtml* avec le code suivant :

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

Enregistrez vos modifications et accédez à l’URL suivante :

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

Les données sont extraites de l’URL et transmises au contrôleur à l’aide du [Binder de modèle MVC](xref:mvc/models/model-binding). Le contrôleur empaquette les données dans un dictionnaire `ViewData` et passe cet objet à la vue. La vue restitue ensuite les données au format HTML dans le navigateur.

![Vue Confidentialité affichant une étiquette Welcome et l’expression « Hello Rick » affichée quatre fois](~/tutorials/first-mvc-app/adding-view/_static/rick2_50.png)

Dans l’exemple précédent, le `ViewData` dictionnaire a été utilisé pour passer des données du contrôleur à une vue. Plus loin dans ce didacticiel, un modèle de vue est utilisé pour passer les données d’un contrôleur à une vue. L’approche du modèle de vue pour passer des données est préférable à l' `ViewData` approche de dictionnaire.

Dans le didacticiel suivant, une base de données de films est créée.

> [!div class="step-by-step"]
> [Précédent : ajouter un contrôleur](adding-controller.md) 
>  [Suivant : ajouter un modèle](adding-model.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Dans cette section, la `HelloWorldController` classe est modifiée de façon à utiliser [Razor](xref:mvc/views/razor) des fichiers de vue pour encapsuler correctement le processus de génération de réponses html sur un client.

Vous créez un fichier de modèle de vue à l’aide de Razor . Razorles modèles de vue basés sur utilisent une extension de fichier *. cshtml* . Ils offrent un moyen élégant pour créer une sortie HTML avec C#.

Actuellement, la méthode `Index` retourne une chaîne avec un message qui est codé en dur dans la classe du contrôleur. Dans la classe `HelloWorldController`, remplacez la méthode `Index` par le code suivant :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

Le code précédent appelle la méthode <xref:Microsoft.AspNetCore.Mvc.Controller.View*> du contrôleur. Il utilise un modèle de vue pour générer une réponse HTML. Les méthodes du contrôleur (également appelées *méthodes d’action*), comme la méthode `Index` ci-dessus, retournent généralement un <xref:Microsoft.AspNetCore.Mvc.IActionResult> (ou une classe dérivée de <xref:Microsoft.AspNetCore.Mvc.ActionResult>), et non un type comme `string`.

## <a name="add-a-view"></a>Ajouter une vue

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Cliquez avec le bouton droit sur le dossier *views* , puis **Ajoutez > nouveau dossier** et nommez le dossier *HelloWorld*.

* Cliquez avec le bouton droit sur le dossier *views/HelloWorld* , puis **Ajoutez > nouvel élément**.

* Dans la boîte de dialogue **Ajouter un nouvel élément - MvcMovie**

  * Dans la zone de recherche située en haut à droite, entrez *vue*

  * Sélectionner une **Razor vue**

  * Conservez la valeur de la zone **Nom**, *Index.cshtml*.

  * Sélectionnez **Ajouter**

![Boîte de dialogue Ajouter un nouvel élément](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Ajoutez une vue `Index` pour `HelloWorldController`.

* Ajoutez un nouveau dossier nommé *Views/HelloWorld*.
* Ajoutez un nouveau fichier à la *vues/HelloWorld* nom du dossier *Index.cshtml*.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* Cliquez avec le bouton droit sur le dossier *Vues*, cliquez sur **Ajouter > Nouveau dossier**, puis nommez le dossier *HelloWorld*.
* Cliquez avec le bouton droit sur le dossier *Vues/HelloWorld*, puis cliquez sur **Ajouter > Nouveau fichier**.
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

Sélectionnez les liens du menu (**MvcMovie**, **Accueil** et **Confidentialité**). Chaque page affiche la même disposition de menu. La disposition du menu est implémentée dans le fichier *Views/Shared/_Layout.cshtml*. Ouvrez le fichier *Views/Shared/_Layout.cshtml*.

Les modèles de [disposition](xref:mvc/views/layout) vous permettent de spécifier la disposition du conteneur HTML de votre site dans un emplacement unique, puis de l’appliquer sur plusieurs pages de votre site. Recherchez la ligne `@RenderBody()`. `RenderBody` est un espace réservé dans lequel toutes les pages spécifiques aux vues que vous créez s’affichent, *encapsulées* dans la page de disposition. Par exemple, si vous sélectionnez le lien **Confidentialité**, la vue **Views/Home/Privacy.cshtml** est restituée dans la méthode `RenderBody`.

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

**Remarque**: le `Movies` contrôleur n’a pas été implémenté. À ce stade, le lien `Movie App` ne fonctionne pas.

Enregistrer vos modifications et sélectionnez le lien **Confidentialité**. Notez comment le titre sur l’onglet du navigateur affiche **Stratégie de confidentialité - Movie App** au lieu de **Stratégie de confidentialité - Mvc Movie** :

![Onglet Confidentialité](~/tutorials/first-mvc-app/adding-view/_static/privacy50.png)

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

Dans *HelloWorldController.cs*, modifiez la méthode `Welcome` pour ajouter une valeur `Message` et `NumTimes` au dictionnaire `ViewData`. Le dictionnaire `ViewData` est un objet dynamique, ce qui signifie que n’importe quel type peut être utilisé, l’objet `ViewData` ne possède aucune propriété définie tant que vous ne placez pas d’élément dedans. Le [système de liaison de données MVC](xref:mvc/models/model-binding) mappe automatiquement les paramètres nommés (`name` et `numTimes`) provenant de la chaîne de requête dans la barre d’adresse aux paramètres de votre méthode. Le fichier *HelloWorldController.cs* complet ressemble à ceci :

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
