---
title: Créer des Tag Helpers dans ASP.NET Core
author: rick-anderson
description: Découvrez comment créer des Tag Helpers dans ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: mvc/views/tag-helpers/authoring
ms.openlocfilehash: e0c9f450e4eded49694cbbb0e9fa2614a221ab14
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586824"
---
# <a name="author-tag-helpers-in-aspnet-core"></a>Créer des Tag Helpers dans ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/tag-helpers/authoring/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="get-started-with-tag-helpers"></a>Bien démarrer avec les Tag Helpers

Ce didacticiel fournit une introduction à la programmation des Tag Helpers. [Introduction aux Tag Helpers](intro.md) décrit les avantages offerts par les Tag Helpers.

Un Tag Helper est toute classe qui implémente l’interface `ITagHelper`. Toutefois, quand vous créez un Tag Helper, vous dérivez généralement de `TagHelper`, ce qui vous permet d’accéder à la méthode `Process`.

1. Créez un projet ASP.NET Core nommé **AuthoringTagHelpers**. Vous n’aurez pas besoin d’authentification pour ce projet.

1. Créez un dossier pour stocker les Tag Helpers appelé *TagHelpers*. Le dossier *TagHelpers* n’est *pas* obligatoire, mais il est judicieux de le créer. Commençons à présent à écrire quelques Tag Helpers simples.

## <a name="a-minimal-tag-helper"></a>Tag Helper minimal

Dans cette section, vous écrivez un Tag Helper qui met à jour une balise e-mail. Par exemple :

```html
<email>Support</email>
```

Le serveur utilise notre Tag Helper e-mail pour convertir ce balisage en code suivant :

```html
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

Autrement dit, une balise d’ancrage qui en fait un lien e-mail. Vous pouvez effectuer cette opération si vous écrivez un moteur de blog qui doit envoyer des e-mails pour le marketing, le support et d’autres contacts, tous dans le même domaine.

1. Ajoutez la classe `EmailTagHelper` suivante au dossier *TagHelpers*.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1EmailTagHelperCopy.cs)]

   * Les Tag Helpers utilisent une convention de nommage qui cible des éléments du nom de classe racine (moins la partie *TagHelper* du nom de classe). Dans cet exemple, le nom racine de **EmailTagHelper** est *email*, donc la `<email>` balise est ciblée. Cette convention de nommage doit fonctionner pour la plupart des Tag Helpers et je vous montrerai plus tard comment la remplacer.

   * La classe `EmailTagHelper` est dérivée de `TagHelper`. La classe `TagHelper` fournit des méthodes et propriétés pour l’écriture des Tag Helpers.

   * La méthode `Process` substituée contrôle ce que fait le Tag Helper lors de son exécution. La classe `TagHelper` fournit également une version asynchrone (`ProcessAsync`) avec les mêmes paramètres.

   * Le paramètre de contexte pour `Process` (et `ProcessAsync`) contient des informations associées à l’exécution de la balise HTML actuelle.

   * Le paramètre de sortie pour `Process` (et `ProcessAsync`) contient un élément HTML avec état représentatif de la source d’origine utilisée pour générer une balise HTML et le contenu.

   * Le nom de notre classe comporte un suffixe **TagHelper**, ce qui n’est *pas* obligatoire, mais est considéré comme une bonne pratique. Vous pouvez déclarer la classe comme suit :

   ```csharp
   public class Email : TagHelper
   ```

1. Pour rendre la `EmailTagHelper` classe disponible pour toutes nos Razor vues, ajoutez la `addTagHelper` directive au fichier *views/_ViewImports. cshtml* :

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopyEmail.cshtml?highlight=2,3)]

   Le code ci-dessus utilise la syntaxe d’expressions génériques pour spécifier que tous les Tag Helpers dans notre assembly seront disponibles. La première chaîne après `@addTagHelper` désigne le Tag Helper à charger (utilisez « * » pour tous les Tag Helpers), et la deuxième chaîne « AuthoringTagHelpers » indique l’assembly dans lequel se trouve le Tag Helper. En outre, Notez que la deuxième ligne insère les ASP.NET Core de balises MVC à l’aide de la syntaxe avec caractères génériques (ces informations sont présentées dans [Introduction aux balises d’aide](intro.md)). Il s’agit de la `@addTagHelper` directive qui met le tag Helper à la disposition de l' Razor affichage. Sinon, vous pouvez fournir le nom qualifié complet d’un Tag Helper comme indiqué ci-dessous :

```csharp
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

<!--
the following snippet uses TagHelpers3 and should use TagHelpers (not the 3)
    [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImports.cshtml?highlight=3&range=1-3)]
-->

Pour ajouter un Tag Helper à une vue à l’aide d’un nom qualifié complet, ajoutez d’abord ce nom (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), puis le **nom d’assembly** (*AuthoringTagHelpers*, pas nécessairement `namespace`). La plupart des développeurs préfèrent utiliser la syntaxe d’expressions génériques. [Introduction aux Tag Helpers](intro.md) décrit en détail l’ajout et la suppression de Tag Helpers, la hiérarchie et la syntaxe d’expressions génériques.

1. Mettez à jour le balisage dans le fichier *Views/Home/Contact.cshtml* avec les modifications suivantes :

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

1. Exécutez l’application et utilisez votre navigateur favori pour afficher la source HTML afin de vérifier que les balises e-mail sont remplacées par un balisage d’ancrage (par exemple, `<a>Support</a>`). *Support* et *Marketing* s’affichent sous forme de liens, mais sans l’attribut `href` pour les rendre fonctionnels. Nous le corrigerons dans la section suivante.

## <a name="setattribute-and-setcontent"></a>SetAttribute et SetContent

Dans cette section, nous allons mettre à jour la classe `EmailTagHelper` afin qu’elle crée une balise d’ancrage valide pour les e-mails. Nous allons le mettre à jour pour prendre les informations d’une Razor vue (sous la forme d’un `mail-to` attribut) et l’utiliser pour générer l’ancre.

Mettez à jour la classe `EmailTagHelper` avec le code suivant :

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?range=6-22)]

* Les noms de propriété et de classe de casse Pascal pour les Tag Helpers sont convertis en [casse kebab](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101) (mots séparés par des tirets). Par conséquent, pour utiliser l’attribut `MailTo`, vous employez l’équivalent `<email mail-to="value"/>`.

* La dernière ligne définit le contenu terminé pour notre Tag Helper fonctionnel au minimum.

* La ligne en surbrillance montre la syntaxe pour l’ajout d’attributs :

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?highlight=6&range=14-21)]

Cette approche fonctionne pour l’attribut « href » tant qu’il n’existe pas dans la collection d’attributs. Vous pouvez également utiliser la méthode `output.Attributes.Add` pour ajouter un attribut de Tag Helper à la fin de la collection des attributs de balise.

1. Mettez à jour le balisage dans le fichier *Views/Home/Contact.cshtml* avec les modifications suivantes :

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/ContactCopy.cshtml?highlight=15,16)]

1. Exécutez l’application et vérifiez qu’elle génère les liens corrects.

<a name="self-closing"></a>

   > [!NOTE]
   > Si vous deviez écrire la fermeture automatique de la balise e-mail (`<email mail-to="Rick" />`), la sortie finale se fermerait aussi automatiquement. Pour permettre l’écriture de la balise avec uniquement une balise de début ( `<email mail-to="Rick">` ), vous devez marquer la classe avec ce qui suit :
   >
   > [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailVoid.cs?highlight=1&range=6-10)]

   Avec un Tag Helper e-mail de fermeture automatique, la sortie serait `<a href="mailto:Rick@contoso.com" />`. Comme les balises d’ancrage de fermeture automatique ne sont pas du code HTML valide, vous n’allez pas en créer, mais vous pouvez peut-être créer un Tag Helper qui se ferme automatiquement. Les Tag Helpers définissent le type de la propriété `TagMode` après la lecture d’une balise.

### <a name="processasync"></a>ProcessAsync

Dans cette section, nous allons écrire un Tag Helper e-mail asynchrone.

1. Remplacez la classe `EmailTagHelper` par le code suivant :

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelper.cs?range=6-17)]

   **Remarques :**

   * Cette version utilise la méthode `ProcessAsync` asynchrone. L’élément `GetChildContentAsync` asynchrone retourne un élément `Task` contenant `TagHelperContent`.

   * Utilisez le paramètre `output` pour obtenir le contenu de l’élément HTML.

1. Apportez la modification suivante au fichier *Views/Home/Contact.cshtml* pour que le Tag Helper puisse obtenir l’e-mail cible.

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

1. Exécutez l’application et vérifiez qu’elle génère des liens e-mail valides.

### <a name="removeall-precontentsethtmlcontent-and-postcontentsethtmlcontent"></a>RemoveAll, PreContent.SetHtmlContent et PostContent.SetHtmlContent

1. Ajoutez la classe `BoldTagHelper` suivante au dossier *TagHelpers*.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/BoldTagHelper.cs)]

   * L’attribut `[HtmlTargetElement]` passe un paramètre d’attribut qui spécifie qu’un élément HTML qui contient un attribut HTML nommé « bold » correspond, et la méthode override `Process` dans la classe s’exécute. Dans notre exemple, la méthode `Process` supprime l’attribut « bold » et entoure le balisage contenant avec `<strong></strong>`.

   * Comme vous ne voulez pas remplacer le contenu de la balise, vous devez écrire la balise `<strong>` d’ouverture avec la méthode `PreContent.SetHtmlContent` et la balise `</strong>` de fermeture avec la méthode `PostContent.SetHtmlContent`.

1. Modifiez la vue *About.cshtml* pour qu’elle contienne une valeur d’attribut `bold`. Le code terminé est indiqué ci-dessous.

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutBoldOnly.cshtml?highlight=7)]

1. Exécutez l'application. Vous pouvez utiliser votre navigateur favori pour inspecter la source et vérifier le balisage.

   L’attribut `[HtmlTargetElement]` ci-dessus cible uniquement le balisage HTML qui fournit le nom d’attribut « bold ». L’élément `<bold>` n’a pas été modifié par le Tag Helper.

1. Commentez la ligne de l’attribut `[HtmlTargetElement]` et il ciblera par défaut les balises `<bold>`, autrement dit le balisage HTML sous la forme `<bold>`. Gardez à l’esprit que la convention de nommage par défaut associe le nom de classe **Bold** TagHelper aux balises `<bold>`.

1. Exécutez l’application et vérifiez que la balise `<bold>` est traitée par le Tag Helper.

Le fait de décorer une classe avec plusieurs attributs `[HtmlTargetElement]` aboutit à une opération OR logique des cibles. Par exemple, avec le code ci-dessous, une balise en gras ou un attribut gras correspondent.

[!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zBoldTagHelperCopy.cs?highlight=1,2&range=5-15)]

Quand plusieurs attributs sont ajoutés à la même instruction, le runtime les traite comme une opération AND logique. Par exemple, dans le code ci-dessous, un élément HTML doit être nommé « bold » avec un attribut nommé « bold » (`<bold bold />`) pour la mise en correspondance.

```csharp
[HtmlTargetElement("bold", Attributes = "bold")]
   ```

Vous pouvez également utiliser l’attribut `[HtmlTargetElement]` pour modifier le nom de l’élément ciblé. Par exemple, si vous souhaitez que `BoldTagHelper` cible les balises `<MyBold>`, vous utilisez l’attribut suivant :

```csharp
[HtmlTargetElement("MyBold")]
```

## <a name="pass-a-model-to-a-tag-helper"></a>Passer un modèle à un Tag Helper

1. Ajoutez un dossier *Models*.

1. Ajoutez la classe `WebsiteContext` suivante au dossier *Models* :

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Models/WebsiteContext.cs)]

1. Ajoutez la classe `WebsiteInformationTagHelper` suivante au dossier *TagHelpers*.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/WebsiteInformationTagHelper.cs)]

   * Comme mentionné précédemment, les Tag Helpers convertissent les propriétés et noms de classe C# de casse Pascal en [casse kebab](https://wiki.c2.com/?KebabCase) (mots séparés par des tirets). Par conséquent, pour utiliser `WebsiteInformationTagHelper` dans Razor , vous allez écrire `<website-information />` .

   * Comme vous n’identifiez pas de manière explicite l’élément cible avec l’attribut `[HtmlTargetElement]`, la valeur par défaut de `website-information` est ciblée. Si vous avez appliqué l’attribut suivant (notez que la casse n’est pas kebab, mais il correspond au nom de la classe) :

   ```csharp
   [HtmlTargetElement("WebsiteInformation")]
   ```

   La balise en casse kebab `<website-information />` ne correspond pas. Si vous voulez utiliser l’attribut `[HtmlTargetElement]`, vous employez la casse kebab comme indiqué ci-dessous :

   ```csharp
   [HtmlTargetElement("Website-Information")]
   ```

   * Les éléments de fermeture automatique n’ont aucun contenu. Pour cet exemple, le Razor balisage utilise une balise de fermeture automatique, mais le tag Helper crée un élément [section](https://www.w3.org/TR/html5/sections.html#the-section-element) (qui n’est pas auto-fermé et vous écrivez du contenu à l’intérieur de l' `section` élément). Par conséquent, vous devez affecter à `TagMode` la valeur `StartTagAndEndTag` pour écrire la sortie. Sinon, vous pouvez commenter le paramètre de ligne `TagMode` et écrire le balisage avec une balise de fermeture. (Un exemple de balisage est fourni plus loin dans ce didacticiel.)

   * Le signe `$` (signe dollar) de la ligne suivante utilise une [chaîne interpolée](/dotnet/csharp/language-reference/keywords/interpolated-strings) :

   ```cshtml
   $@"<ul><li><strong>Version:</strong> {Info.Version}</li>
   ```

1. Ajoutez le balisage suivant à la vue *About.cshtml*. Le balisage en surbrillance affiche les informations de site web.

   [!code-cshtml[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?highlight=1,4-8, 18-999)]

   > [!NOTE]
   > Dans le Razor balisage illustré ci-dessous :
   >
   > [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?range=18-18)]
   >
   > Razor sait que l' `info` attribut est une classe, et non une chaîne, et que vous souhaitez écrire du code C#. N’importe quel attribut de Tag Helper autre qu’une chaîne doit être écrit sans le caractère `@`.

1. Exécutez l’application et accédez à la vue About (À propos de) pour afficher les informations de site web.

   > [!NOTE]
   > Vous pouvez utiliser le balisage suivant avec une balise de fermeture et supprimer la ligne avec `TagMode.StartTagAndEndTag` dans le Tag Helper :
   >
   > [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutNotSelfClosing.cshtml?range=20-21)]

## <a name="condition-tag-helper"></a>Tag Helper Condition

Le Tag Helper Condition restitue la sortie quand une valeur true lui est transmise.

1. Ajoutez la classe `ConditionTagHelper` suivante au dossier *TagHelpers*.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/ConditionTagHelper.cs)]

1. Remplacez le contenu du fichier *Views/Home/Index.cshtml* par le balisage suivant :

   [!code-cshtml[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Index.cshtml)]

1. Remplacez la méthode `Index` dans le contrôleur `Home` par le code suivant :

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Controllers/HomeController.cs?range=9-18)]

1. Exécutez l’application et accédez à la page d’accueil. Le balisage de l’élément `div` conditionnel ne sera pas rendu. Ajoutez la chaîne de requête `?approved=true` à l’URL (par exemple, `http://localhost:1235/Home/Index?approved=true`). `approved` a la valeur true et le balisage conditionnel s’affichera.

> [!NOTE]
> Utilisez l’opérateur [nameof](/dotnet/csharp/language-reference/keywords/nameof) pour spécifier l’attribut à cibler plutôt qu’une chaîne comme vous le faisiez avec le Tag Helper Bold :
>
> [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zConditionTagHelperCopy.cs?highlight=1,2,5&range=5-18)]
>
> L’opérateur [nameof](/dotnet/csharp/language-reference/keywords/nameof) protégera le code s’il devait jamais être refactorisé (le nom peut être changé en `RedCondition`).

### <a name="avoid-tag-helper-conflicts"></a>Éviter les conflits de Tag Helpers

Dans cette section, vous écrivez une paire de Tag Helpers de liaison automatique. Le premier remplace le balisage contenant une URL commençant par HTTP par une balise d’ancrage HTML contenant la même URL (et produisant ainsi un lien vers l’URL). Le second effectue la même opération pour une URL commençant par WWW.

Comme ces deux Tag Helpers sont étroitement liés et que vous pouvez les refactoriser à l’avenir, nous allons les conserver dans le même fichier.

1. Ajoutez la classe `AutoLinkerHttpTagHelper` suivante au dossier *TagHelpers*.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=7-19)]

   >[!NOTE]
   >La classe `AutoLinkerHttpTagHelper` cible les éléments `p` et utilise [Regex](/dotnet/standard/base-types/regular-expression-language-quick-reference) pour créer l’ancre.

1. Ajoutez le balisage suivant à la fin du fichier *Views/Home/Contact.cshtml* :

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=19)]

1. Exécutez l’application et vérifiez que le Tag Helper restitue correctement l’ancre.

1. Mettez à jour la classe `AutoLinker` pour inclure `AutoLinkerWwwTagHelper` qui convertira le texte www en une balise d’ancrage qui contient également le texte www d’origine. Le code mis à jour est en surbrillance ci-dessous :

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?highlight=15-34&range=7-34)]

1. Exécutez l'application. Notez que le texte www est affiché sous forme de lien, contrairement au texte HTTP. Si vous placez un point d’arrêt dans les deux classes, vous pouvez voir que la classe du Tag Helper HTTP s’exécute en premier. Le problème est que la sortie du Tag Helper est mise en cache et, quand le Tag Helper WWW est exécuté, il remplace la sortie mise en cache du Tag Helper HTTP. Plus loin dans ce didacticiel, nous verrons comment contrôler l’ordre d’exécution des Tag Helpers. Nous allons corriger le code avec les éléments suivants :

   [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10,21,22,26&range=8-37)]

   > [!NOTE]
   > Dans la première édition des Tag Helpers de liaison automatique, vous avez obtenu le contenu de la cible avec le code suivant :
   >
   > [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=12)]
   >
   > Autrement dit, vous appelez `GetChildContentAsync` à l’aide de l’élément `TagHelperOutput` passé dans la méthode `ProcessAsync`. Comme mentionné précédemment, étant donné que la sortie est mise en cache, le dernier Tag Helper qui est exécuté l’emporte. Vous avez résolu ce problème avec le code suivant :
   >
   > [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?range=34-35)]
   >
   > Le code ci-dessus vérifie si le contenu a été modifié et, le cas échéant, obtient le contenu à partir de la mémoire tampon de sortie.

1. Exécutez l’application et vérifiez que les deux liens fonctionnent comme prévu. Alors qu’il peut sembler que notre Tag Helper de liaison automatique est correct et terminé, il pose un problème subtil. Si le Tag Helper WWW est exécuté en premier, les liens www ne sont pas corrects. Mettez à jour le code en ajoutant la surcharge `Order` pour contrôler l’ordre d’exécution des Tag Helpers. La propriété `Order` détermine l’ordre d’exécution par rapport à d’autres Tag Helpers ciblant le même élément. La valeur d’ordre par défaut est égale à zéro et les instances ayant des valeurs inférieures sont exécutées en premier.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?highlight=5,6,7,8&range=8-15)]

   Le code précédent garantit que le Tag Helper HTTP s’exécute avant le Tag Helper WWW. Remplacez `Order` par `MaxValue` et vérifiez que le balisage généré pour la balise WWW est incorrect.

## <a name="inspect-and-retrieve-child-content"></a>Examiner et récupérer le contenu enfant

Les Tag Helpers fournissent plusieurs propriétés permettant de récupérer le contenu.

* Le résultat de `GetChildContentAsync` peut être ajouté à `output.Content`.
* Vous pouvez examiner le résultat de `GetChildContentAsync` avec `GetContent`.
* Si vous modifiez `output.Content`, le corps TagHelper n’est pas exécuté ni restitué, sauf si vous appelez `GetChildContentAsync` comme dans notre exemple de liaison automatique :

[!code-csharp[](../../views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10&range=8-21)]

* Plusieurs appels à `GetChildContentAsync` retournent la même valeur et ne réexécutent pas le corps `TagHelper`, sauf si vous passez un paramètre false indiquant de ne pas utiliser le résultat mis en cache.

## <a name="load-minified-partial-view-taghelper"></a>Charger TagHelper avec une vue partielle minimisée

Dans les environnements de production, les performances peuvent être améliorées en chargeant des vues partielles minimisées. Pour tirer parti de la vue partielle minimisée en production :

* Créez/configurez un processus pré-build qui minimise les vues partielles.
* Utilisez le code suivant pour charger des vues partielles minimisées dans des environnements non liés au développement.

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/MinifiedVersionTagHelper.cs?name=snippet)]
