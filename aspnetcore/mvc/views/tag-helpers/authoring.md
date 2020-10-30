---
title: Créer des Tag Helpers dans ASP.NET Core
author: rick-anderson
description: Découvrez comment créer des Tag Helpers dans ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: mvc/views/tag-helpers/authoring
ms.openlocfilehash: 306416db3d9ae0219f859c3cf459eb08a5b778cf
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060922"
---
# <a name="author-tag-helpers-in-aspnet-core"></a><span data-ttu-id="4613f-103">Créer des Tag Helpers dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4613f-103">Author Tag Helpers in ASP.NET Core</span></span>

<span data-ttu-id="4613f-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4613f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4613f-105">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/authoring/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4613f-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/authoring/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="get-started-with-tag-helpers"></a><span data-ttu-id="4613f-106">Bien démarrer avec les Tag Helpers</span><span class="sxs-lookup"><span data-stu-id="4613f-106">Get started with Tag Helpers</span></span>

<span data-ttu-id="4613f-107">Ce didacticiel fournit une introduction à la programmation des Tag Helpers.</span><span class="sxs-lookup"><span data-stu-id="4613f-107">This tutorial provides an introduction to programming Tag Helpers.</span></span> <span data-ttu-id="4613f-108">[Introduction aux Tag Helpers](intro.md) décrit les avantages offerts par les Tag Helpers.</span><span class="sxs-lookup"><span data-stu-id="4613f-108">[Introduction to Tag Helpers](intro.md) describes the benefits that Tag Helpers provide.</span></span>

<span data-ttu-id="4613f-109">Un Tag Helper est toute classe qui implémente l’interface `ITagHelper`.</span><span class="sxs-lookup"><span data-stu-id="4613f-109">A tag helper is any class that implements the `ITagHelper` interface.</span></span> <span data-ttu-id="4613f-110">Toutefois, quand vous créez un Tag Helper, vous dérivez généralement de `TagHelper`, ce qui vous permet d’accéder à la méthode `Process`.</span><span class="sxs-lookup"><span data-stu-id="4613f-110">However, when you author a tag helper, you generally derive from `TagHelper`, doing so gives you access to the `Process` method.</span></span>

1. <span data-ttu-id="4613f-111">Créez un projet ASP.NET Core nommé **AuthoringTagHelpers** .</span><span class="sxs-lookup"><span data-stu-id="4613f-111">Create a new ASP.NET Core project called **AuthoringTagHelpers** .</span></span> <span data-ttu-id="4613f-112">Vous n’aurez pas besoin d’authentification pour ce projet.</span><span class="sxs-lookup"><span data-stu-id="4613f-112">You won't need authentication for this project.</span></span>

1. <span data-ttu-id="4613f-113">Créez un dossier pour stocker les Tag Helpers appelé *TagHelpers* .</span><span class="sxs-lookup"><span data-stu-id="4613f-113">Create a folder to hold the Tag Helpers called *TagHelpers* .</span></span> <span data-ttu-id="4613f-114">Le dossier *TagHelpers* n’est *pas* obligatoire, mais il est judicieux de le créer.</span><span class="sxs-lookup"><span data-stu-id="4613f-114">The *TagHelpers* folder is *not* required, but it's a reasonable convention.</span></span> <span data-ttu-id="4613f-115">Commençons à présent à écrire quelques Tag Helpers simples.</span><span class="sxs-lookup"><span data-stu-id="4613f-115">Now let's get started writing some simple tag helpers.</span></span>

## <a name="a-minimal-tag-helper"></a><span data-ttu-id="4613f-116">Tag Helper minimal</span><span class="sxs-lookup"><span data-stu-id="4613f-116">A minimal Tag Helper</span></span>

<span data-ttu-id="4613f-117">Dans cette section, vous écrivez un Tag Helper qui met à jour une balise e-mail.</span><span class="sxs-lookup"><span data-stu-id="4613f-117">In this section, you write a tag helper that updates an email tag.</span></span> <span data-ttu-id="4613f-118">Exemple :</span><span class="sxs-lookup"><span data-stu-id="4613f-118">For example:</span></span>

```html
<email>Support</email>
```

<span data-ttu-id="4613f-119">Le serveur utilise notre Tag Helper e-mail pour convertir ce balisage en code suivant :</span><span class="sxs-lookup"><span data-stu-id="4613f-119">The server will use our email tag helper to convert that markup into the following:</span></span>

```html
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

<span data-ttu-id="4613f-120">Autrement dit, une balise d’ancrage qui en fait un lien e-mail.</span><span class="sxs-lookup"><span data-stu-id="4613f-120">That is, an anchor tag that makes this an email link.</span></span> <span data-ttu-id="4613f-121">Vous pouvez effectuer cette opération si vous écrivez un moteur de blog qui doit envoyer des e-mails pour le marketing, le support et d’autres contacts, tous dans le même domaine.</span><span class="sxs-lookup"><span data-stu-id="4613f-121">You might want to do this if you are writing a blog engine and need it to send email for marketing, support, and other contacts, all to the same domain.</span></span>

1. <span data-ttu-id="4613f-122">Ajoutez la classe `EmailTagHelper` suivante au dossier *TagHelpers* .</span><span class="sxs-lookup"><span data-stu-id="4613f-122">Add the following `EmailTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1EmailTagHelperCopy.cs)]

   * <span data-ttu-id="4613f-123">Les Tag Helpers utilisent une convention de nommage qui cible des éléments du nom de classe racine (moins la partie *TagHelper* du nom de classe).</span><span class="sxs-lookup"><span data-stu-id="4613f-123">Tag helpers use a naming convention that targets elements of the root class name (minus the *TagHelper* portion of the class name).</span></span> <span data-ttu-id="4613f-124">Dans cet exemple, le nom racine de **EmailTagHelper** est *email* , donc la `<email>` balise est ciblée.</span><span class="sxs-lookup"><span data-stu-id="4613f-124">In this example, the root name of **EmailTagHelper** is *email* , so the `<email>` tag will be targeted.</span></span> <span data-ttu-id="4613f-125">Cette convention de nommage doit fonctionner pour la plupart des Tag Helpers et je vous montrerai plus tard comment la remplacer.</span><span class="sxs-lookup"><span data-stu-id="4613f-125">This naming convention should work for most tag helpers, later on I'll show how to override it.</span></span>

   * <span data-ttu-id="4613f-126">La classe `EmailTagHelper` est dérivée de `TagHelper`.</span><span class="sxs-lookup"><span data-stu-id="4613f-126">The `EmailTagHelper` class derives from `TagHelper`.</span></span> <span data-ttu-id="4613f-127">La classe `TagHelper` fournit des méthodes et propriétés pour l’écriture des Tag Helpers.</span><span class="sxs-lookup"><span data-stu-id="4613f-127">The `TagHelper` class provides methods and properties for writing Tag Helpers.</span></span>

   * <span data-ttu-id="4613f-128">La méthode `Process` substituée contrôle ce que fait le Tag Helper lors de son exécution.</span><span class="sxs-lookup"><span data-stu-id="4613f-128">The overridden `Process` method controls what the tag helper does when executed.</span></span> <span data-ttu-id="4613f-129">La classe `TagHelper` fournit également une version asynchrone (`ProcessAsync`) avec les mêmes paramètres.</span><span class="sxs-lookup"><span data-stu-id="4613f-129">The `TagHelper` class also provides an asynchronous version (`ProcessAsync`) with the same parameters.</span></span>

   * <span data-ttu-id="4613f-130">Le paramètre de contexte pour `Process` (et `ProcessAsync`) contient des informations associées à l’exécution de la balise HTML actuelle.</span><span class="sxs-lookup"><span data-stu-id="4613f-130">The context parameter to `Process` (and `ProcessAsync`) contains information associated with the execution of the current HTML tag.</span></span>

   * <span data-ttu-id="4613f-131">Le paramètre de sortie pour `Process` (et `ProcessAsync`) contient un élément HTML avec état représentatif de la source d’origine utilisée pour générer une balise HTML et le contenu.</span><span class="sxs-lookup"><span data-stu-id="4613f-131">The output parameter to `Process` (and `ProcessAsync`) contains a stateful HTML element representative of the original source used to generate an HTML tag and content.</span></span>

   * <span data-ttu-id="4613f-132">Le nom de notre classe comporte un suffixe **TagHelper** , ce qui n’est *pas* obligatoire, mais est considéré comme une bonne pratique.</span><span class="sxs-lookup"><span data-stu-id="4613f-132">Our class name has a suffix of **TagHelper** , which is *not* required, but it's considered a best practice convention.</span></span> <span data-ttu-id="4613f-133">Vous pouvez déclarer la classe comme suit :</span><span class="sxs-lookup"><span data-stu-id="4613f-133">You could declare the class as:</span></span>

   ```csharp
   public class Email : TagHelper
   ```

1. <span data-ttu-id="4613f-134">Pour rendre la `EmailTagHelper` classe disponible pour toutes nos :::no-loc(Razor)::: vues, ajoutez la `addTagHelper` directive au fichier *views/_ViewImports. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="4613f-134">To make the `EmailTagHelper` class available to all our :::no-loc(Razor)::: views, add the `addTagHelper` directive to the *Views/_ViewImports.cshtml* file:</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopyEmail.cshtml?highlight=2,3)]

   <span data-ttu-id="4613f-135">Le code ci-dessus utilise la syntaxe d’expressions génériques pour spécifier que tous les Tag Helpers dans notre assembly seront disponibles.</span><span class="sxs-lookup"><span data-stu-id="4613f-135">The code above uses the wildcard syntax to specify all the tag helpers in our assembly will be available.</span></span> <span data-ttu-id="4613f-136">La première chaîne après `@addTagHelper` désigne le Tag Helper à charger (utilisez « \* » pour tous les Tag Helpers), et la deuxième chaîne « AuthoringTagHelpers » indique l’assembly dans lequel se trouve le Tag Helper.</span><span class="sxs-lookup"><span data-stu-id="4613f-136">The first string after `@addTagHelper` specifies the tag helper to load (Use "\*" for all tag helpers), and the second string "AuthoringTagHelpers" specifies the assembly the tag helper is in.</span></span> <span data-ttu-id="4613f-137">En outre, Notez que la deuxième ligne insère les ASP.NET Core de balises MVC à l’aide de la syntaxe avec caractères génériques (ces informations sont présentées dans [Introduction aux balises d’aide](intro.md)). Il s’agit de la `@addTagHelper` directive qui met le tag Helper à la disposition de l' :::no-loc(Razor)::: affichage.</span><span class="sxs-lookup"><span data-stu-id="4613f-137">Also, note that the second line brings in the ASP.NET Core MVC tag helpers using the wildcard syntax (those helpers are discussed in [Introduction to Tag Helpers](intro.md).) It's the `@addTagHelper` directive that makes the tag helper available to the :::no-loc(Razor)::: view.</span></span> <span data-ttu-id="4613f-138">Sinon, vous pouvez fournir le nom qualifié complet d’un Tag Helper comme indiqué ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="4613f-138">Alternatively, you can provide the fully qualified name (FQN) of a tag helper as shown below:</span></span>

```csharp
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

<!--
the following snippet uses TagHelpers3 and should use TagHelpers (not the 3)
    [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImports.cshtml?highlight=3&range=1-3)]
-->

<span data-ttu-id="4613f-139">Pour ajouter un Tag Helper à une vue à l’aide d’un nom qualifié complet, ajoutez d’abord ce nom (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), puis le **nom d’assembly** ( *AuthoringTagHelpers* , pas nécessairement `namespace`).</span><span class="sxs-lookup"><span data-stu-id="4613f-139">To add a tag helper to a view using a FQN, you first add the FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), and then the **assembly name** ( *AuthoringTagHelpers* , not necessarily the `namespace`).</span></span> <span data-ttu-id="4613f-140">La plupart des développeurs préfèrent utiliser la syntaxe d’expressions génériques.</span><span class="sxs-lookup"><span data-stu-id="4613f-140">Most developers will prefer to use the wildcard syntax.</span></span> <span data-ttu-id="4613f-141">[Introduction aux Tag Helpers](intro.md) décrit en détail l’ajout et la suppression de Tag Helpers, la hiérarchie et la syntaxe d’expressions génériques.</span><span class="sxs-lookup"><span data-stu-id="4613f-141">[Introduction to Tag Helpers](intro.md) goes into detail on tag helper adding, removing, hierarchy, and wildcard syntax.</span></span>

1. <span data-ttu-id="4613f-142">Mettez à jour le balisage dans le fichier *Views/Home/Contact.cshtml* avec les modifications suivantes :</span><span class="sxs-lookup"><span data-stu-id="4613f-142">Update the markup in the *Views/Home/Contact.cshtml* file with these changes:</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

1. <span data-ttu-id="4613f-143">Exécutez l’application et utilisez votre navigateur favori pour afficher la source HTML afin de vérifier que les balises e-mail sont remplacées par un balisage d’ancrage (par exemple, `<a>Support</a>`).</span><span class="sxs-lookup"><span data-stu-id="4613f-143">Run the app and use your favorite browser to view the HTML source so you can verify that the email tags are replaced with anchor markup (For example, `<a>Support</a>`).</span></span> <span data-ttu-id="4613f-144">*Support* et *Marketing* s’affichent sous forme de liens, mais sans l’attribut `href` pour les rendre fonctionnels.</span><span class="sxs-lookup"><span data-stu-id="4613f-144">*Support* and *Marketing* are rendered as a links, but they don't have an `href` attribute to make them functional.</span></span> <span data-ttu-id="4613f-145">Nous le corrigerons dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="4613f-145">We'll fix that in the next section.</span></span>

## <a name="setattribute-and-setcontent"></a><span data-ttu-id="4613f-146">SetAttribute et SetContent</span><span class="sxs-lookup"><span data-stu-id="4613f-146">SetAttribute and SetContent</span></span>

<span data-ttu-id="4613f-147">Dans cette section, nous allons mettre à jour la classe `EmailTagHelper` afin qu’elle crée une balise d’ancrage valide pour les e-mails.</span><span class="sxs-lookup"><span data-stu-id="4613f-147">In this section, we'll update the `EmailTagHelper` so that it will create a valid anchor tag for email.</span></span> <span data-ttu-id="4613f-148">Nous allons le mettre à jour pour prendre les informations d’une :::no-loc(Razor)::: vue (sous la forme d’un `mail-to` attribut) et l’utiliser pour générer l’ancre.</span><span class="sxs-lookup"><span data-stu-id="4613f-148">We'll update it to take information from a :::no-loc(Razor)::: view (in the form of a `mail-to` attribute) and use that in generating the anchor.</span></span>

<span data-ttu-id="4613f-149">Mettez à jour la classe `EmailTagHelper` avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="4613f-149">Update the `EmailTagHelper` class with the following:</span></span>

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?range=6-22)]

* <span data-ttu-id="4613f-150">Les noms de propriété et de classe de casse Pascal pour les Tag Helpers sont convertis en [casse kebab](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101) (mots séparés par des tirets).</span><span class="sxs-lookup"><span data-stu-id="4613f-150">Pascal-cased class and property names for tag helpers are translated into their [kebab case](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span></span> <span data-ttu-id="4613f-151">Par conséquent, pour utiliser l’attribut `MailTo`, vous employez l’équivalent `<email mail-to="value"/>`.</span><span class="sxs-lookup"><span data-stu-id="4613f-151">Therefore, to use the `MailTo` attribute, you'll use `<email mail-to="value"/>` equivalent.</span></span>

* <span data-ttu-id="4613f-152">La dernière ligne définit le contenu terminé pour notre Tag Helper fonctionnel au minimum.</span><span class="sxs-lookup"><span data-stu-id="4613f-152">The last line sets the completed content for our minimally functional tag helper.</span></span>

* <span data-ttu-id="4613f-153">La ligne en surbrillance montre la syntaxe pour l’ajout d’attributs :</span><span class="sxs-lookup"><span data-stu-id="4613f-153">The highlighted line shows the syntax for adding attributes:</span></span>

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?highlight=6&range=14-21)]

<span data-ttu-id="4613f-154">Cette approche fonctionne pour l’attribut « href » tant qu’il n’existe pas dans la collection d’attributs.</span><span class="sxs-lookup"><span data-stu-id="4613f-154">That approach works for the attribute "href" as long as it doesn't currently exist in the attributes collection.</span></span> <span data-ttu-id="4613f-155">Vous pouvez également utiliser la méthode `output.Attributes.Add` pour ajouter un attribut de Tag Helper à la fin de la collection des attributs de balise.</span><span class="sxs-lookup"><span data-stu-id="4613f-155">You can also use the `output.Attributes.Add` method to add a tag helper attribute to the end of the collection of tag attributes.</span></span>

1. <span data-ttu-id="4613f-156">Mettez à jour le balisage dans le fichier *Views/Home/Contact.cshtml* avec les modifications suivantes :</span><span class="sxs-lookup"><span data-stu-id="4613f-156">Update the markup in the *Views/Home/Contact.cshtml* file with these changes:</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/ContactCopy.cshtml?highlight=15,16)]

1. <span data-ttu-id="4613f-157">Exécutez l’application et vérifiez qu’elle génère les liens corrects.</span><span class="sxs-lookup"><span data-stu-id="4613f-157">Run the app and verify that it generates the correct links.</span></span>

<a name="self-closing"></a>

   > [!NOTE]
   > <span data-ttu-id="4613f-158">Si vous deviez écrire la fermeture automatique de la balise e-mail (`<email mail-to="Rick" />`), la sortie finale se fermerait aussi automatiquement.</span><span class="sxs-lookup"><span data-stu-id="4613f-158">If you were to write the email tag self-closing (`<email mail-to="Rick" />`), the final output would also be self-closing.</span></span> <span data-ttu-id="4613f-159">Pour permettre l’écriture de la balise avec uniquement une balise de début ( `<email mail-to="Rick">` ), vous devez marquer la classe avec ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="4613f-159">To enable the ability to write the tag with only a start tag (`<email mail-to="Rick">`) you must mark the class with the following:</span></span>
   >
   > [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailVoid.cs?highlight=1&range=6-10)]

   <span data-ttu-id="4613f-160">Avec un Tag Helper e-mail de fermeture automatique, la sortie serait `<a href="mailto:Rick@contoso.com" />`.</span><span class="sxs-lookup"><span data-stu-id="4613f-160">With a self-closing email tag helper, the output would be `<a href="mailto:Rick@contoso.com" />`.</span></span> <span data-ttu-id="4613f-161">Comme les balises d’ancrage de fermeture automatique ne sont pas du code HTML valide, vous n’allez pas en créer, mais vous pouvez peut-être créer un Tag Helper qui se ferme automatiquement.</span><span class="sxs-lookup"><span data-stu-id="4613f-161">Self-closing anchor tags are not valid HTML, so you wouldn't want to create one, but you might want to create a tag helper that's self-closing.</span></span> <span data-ttu-id="4613f-162">Les Tag Helpers définissent le type de la propriété `TagMode` après la lecture d’une balise.</span><span class="sxs-lookup"><span data-stu-id="4613f-162">Tag helpers set the type of the `TagMode` property after reading a tag.</span></span>

### <a name="processasync"></a><span data-ttu-id="4613f-163">ProcessAsync</span><span class="sxs-lookup"><span data-stu-id="4613f-163">ProcessAsync</span></span>

<span data-ttu-id="4613f-164">Dans cette section, nous allons écrire un Tag Helper e-mail asynchrone.</span><span class="sxs-lookup"><span data-stu-id="4613f-164">In this section, we'll write an asynchronous email helper.</span></span>

1. <span data-ttu-id="4613f-165">Remplacez la classe `EmailTagHelper` par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="4613f-165">Replace the `EmailTagHelper` class with the following code:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelper.cs?range=6-17)]

   <span data-ttu-id="4613f-166">**Remarques**  :</span><span class="sxs-lookup"><span data-stu-id="4613f-166">**Notes:**</span></span>

   * <span data-ttu-id="4613f-167">Cette version utilise la méthode `ProcessAsync` asynchrone.</span><span class="sxs-lookup"><span data-stu-id="4613f-167">This version uses the asynchronous `ProcessAsync` method.</span></span> <span data-ttu-id="4613f-168">L’élément `GetChildContentAsync` asynchrone retourne un élément `Task` contenant `TagHelperContent`.</span><span class="sxs-lookup"><span data-stu-id="4613f-168">The asynchronous `GetChildContentAsync` returns a `Task` containing the `TagHelperContent`.</span></span>

   * <span data-ttu-id="4613f-169">Utilisez le paramètre `output` pour obtenir le contenu de l’élément HTML.</span><span class="sxs-lookup"><span data-stu-id="4613f-169">Use the `output` parameter to get contents of the HTML element.</span></span>

1. <span data-ttu-id="4613f-170">Apportez la modification suivante au fichier *Views/Home/Contact.cshtml* pour que le Tag Helper puisse obtenir l’e-mail cible.</span><span class="sxs-lookup"><span data-stu-id="4613f-170">Make the following change to the *Views/Home/Contact.cshtml* file so the tag helper can get the target email.</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

1. <span data-ttu-id="4613f-171">Exécutez l’application et vérifiez qu’elle génère des liens e-mail valides.</span><span class="sxs-lookup"><span data-stu-id="4613f-171">Run the app and verify that it generates valid email links.</span></span>

### <a name="removeall-precontentsethtmlcontent-and-postcontentsethtmlcontent"></a><span data-ttu-id="4613f-172">RemoveAll, PreContent.SetHtmlContent et PostContent.SetHtmlContent</span><span class="sxs-lookup"><span data-stu-id="4613f-172">RemoveAll, PreContent.SetHtmlContent and PostContent.SetHtmlContent</span></span>

1. <span data-ttu-id="4613f-173">Ajoutez la classe `BoldTagHelper` suivante au dossier *TagHelpers* .</span><span class="sxs-lookup"><span data-stu-id="4613f-173">Add the following `BoldTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/BoldTagHelper.cs)]

   * <span data-ttu-id="4613f-174">L’attribut `[HtmlTargetElement]` passe un paramètre d’attribut qui spécifie qu’un élément HTML qui contient un attribut HTML nommé « bold » correspond, et la méthode override `Process` dans la classe s’exécute.</span><span class="sxs-lookup"><span data-stu-id="4613f-174">The `[HtmlTargetElement]` attribute passes an attribute parameter that specifies that any HTML element that contains an HTML attribute named "bold" will match, and the `Process` override method in the class will run.</span></span> <span data-ttu-id="4613f-175">Dans notre exemple, la méthode `Process` supprime l’attribut « bold » et entoure le balisage contenant avec `<strong></strong>`.</span><span class="sxs-lookup"><span data-stu-id="4613f-175">In our sample, the `Process` method removes the "bold" attribute and surrounds the containing markup with `<strong></strong>`.</span></span>

   * <span data-ttu-id="4613f-176">Comme vous ne voulez pas remplacer le contenu de la balise, vous devez écrire la balise `<strong>` d’ouverture avec la méthode `PreContent.SetHtmlContent` et la balise `</strong>` de fermeture avec la méthode `PostContent.SetHtmlContent`.</span><span class="sxs-lookup"><span data-stu-id="4613f-176">Because you don't want to replace the existing tag content, you must write the opening `<strong>` tag with the `PreContent.SetHtmlContent` method and the closing `</strong>` tag with the `PostContent.SetHtmlContent` method.</span></span>

1. <span data-ttu-id="4613f-177">Modifiez la vue *About.cshtml* pour qu’elle contienne une valeur d’attribut `bold`.</span><span class="sxs-lookup"><span data-stu-id="4613f-177">Modify the *About.cshtml* view to contain a `bold` attribute value.</span></span> <span data-ttu-id="4613f-178">Le code terminé est indiqué ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="4613f-178">The completed code is shown below.</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutBoldOnly.cshtml?highlight=7)]

1. <span data-ttu-id="4613f-179">Exécutez l’application.</span><span class="sxs-lookup"><span data-stu-id="4613f-179">Run the app.</span></span> <span data-ttu-id="4613f-180">Vous pouvez utiliser votre navigateur favori pour inspecter la source et vérifier le balisage.</span><span class="sxs-lookup"><span data-stu-id="4613f-180">You can use your favorite browser to inspect the source and verify the markup.</span></span>

   <span data-ttu-id="4613f-181">L’attribut `[HtmlTargetElement]` ci-dessus cible uniquement le balisage HTML qui fournit le nom d’attribut « bold ».</span><span class="sxs-lookup"><span data-stu-id="4613f-181">The `[HtmlTargetElement]` attribute above only targets HTML markup that provides an attribute name of "bold".</span></span> <span data-ttu-id="4613f-182">L’élément `<bold>` n’a pas été modifié par le Tag Helper.</span><span class="sxs-lookup"><span data-stu-id="4613f-182">The `<bold>` element wasn't modified by the tag helper.</span></span>

1. <span data-ttu-id="4613f-183">Commentez la ligne de l’attribut `[HtmlTargetElement]` et il ciblera par défaut les balises `<bold>`, autrement dit le balisage HTML sous la forme `<bold>`.</span><span class="sxs-lookup"><span data-stu-id="4613f-183">Comment out the `[HtmlTargetElement]` attribute line and it will default to targeting `<bold>` tags, that is, HTML markup of the form `<bold>`.</span></span> <span data-ttu-id="4613f-184">Gardez à l’esprit que la convention de nommage par défaut associe le nom de classe **Bold** TagHelper aux balises `<bold>`.</span><span class="sxs-lookup"><span data-stu-id="4613f-184">Remember, the default naming convention will match the class name **Bold** TagHelper to `<bold>` tags.</span></span>

1. <span data-ttu-id="4613f-185">Exécutez l’application et vérifiez que la balise `<bold>` est traitée par le Tag Helper.</span><span class="sxs-lookup"><span data-stu-id="4613f-185">Run the app and verify that the `<bold>` tag is processed by the tag helper.</span></span>

<span data-ttu-id="4613f-186">Le fait de décorer une classe avec plusieurs attributs `[HtmlTargetElement]` aboutit à une opération OR logique des cibles.</span><span class="sxs-lookup"><span data-stu-id="4613f-186">Decorating a class with multiple `[HtmlTargetElement]` attributes results in a logical-OR of the targets.</span></span> <span data-ttu-id="4613f-187">Par exemple, avec le code ci-dessous, une balise en gras ou un attribut gras correspondent.</span><span class="sxs-lookup"><span data-stu-id="4613f-187">For example, using the code below, a bold tag or a bold attribute will match.</span></span>

[!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zBoldTagHelperCopy.cs?highlight=1,2&range=5-15)]

<span data-ttu-id="4613f-188">Quand plusieurs attributs sont ajoutés à la même instruction, le runtime les traite comme une opération AND logique.</span><span class="sxs-lookup"><span data-stu-id="4613f-188">When multiple attributes are added to the same statement, the runtime treats them as a logical-AND.</span></span> <span data-ttu-id="4613f-189">Par exemple, dans le code ci-dessous, un élément HTML doit être nommé « bold » avec un attribut nommé « bold » (`<bold bold />`) pour la mise en correspondance.</span><span class="sxs-lookup"><span data-stu-id="4613f-189">For example, in the code below, an HTML element must be named "bold" with an attribute named "bold" (`<bold bold />`) to match.</span></span>

```csharp
[HtmlTargetElement("bold", Attributes = "bold")]
   ```

<span data-ttu-id="4613f-190">Vous pouvez également utiliser l’attribut `[HtmlTargetElement]` pour modifier le nom de l’élément ciblé.</span><span class="sxs-lookup"><span data-stu-id="4613f-190">You can also use the `[HtmlTargetElement]` to change the name of the targeted element.</span></span> <span data-ttu-id="4613f-191">Par exemple, si vous souhaitez que `BoldTagHelper` cible les balises `<MyBold>`, vous utilisez l’attribut suivant :</span><span class="sxs-lookup"><span data-stu-id="4613f-191">For example if you wanted the `BoldTagHelper` to target `<MyBold>` tags, you would use the following attribute:</span></span>

```csharp
[HtmlTargetElement("MyBold")]
```

## <a name="pass-a-model-to-a-tag-helper"></a><span data-ttu-id="4613f-192">Passer un modèle à un Tag Helper</span><span class="sxs-lookup"><span data-stu-id="4613f-192">Pass a model to a Tag Helper</span></span>

1. <span data-ttu-id="4613f-193">Ajoutez un dossier *Models* .</span><span class="sxs-lookup"><span data-stu-id="4613f-193">Add a *Models* folder.</span></span>

1. <span data-ttu-id="4613f-194">Ajoutez la classe `WebsiteContext` suivante au dossier *Models* :</span><span class="sxs-lookup"><span data-stu-id="4613f-194">Add the following `WebsiteContext` class to the *Models* folder:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Models/WebsiteContext.cs)]

1. <span data-ttu-id="4613f-195">Ajoutez la classe `WebsiteInformationTagHelper` suivante au dossier *TagHelpers* .</span><span class="sxs-lookup"><span data-stu-id="4613f-195">Add the following `WebsiteInformationTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/WebsiteInformationTagHelper.cs)]

   * <span data-ttu-id="4613f-196">Comme mentionné précédemment, les Tag Helpers convertissent les propriétés et noms de classe C# de casse Pascal en [casse kebab](https://wiki.c2.com/?KebabCase) (mots séparés par des tirets).</span><span class="sxs-lookup"><span data-stu-id="4613f-196">As mentioned previously, tag helpers translates Pascal-cased C# class names and properties for tag helpers into [kebab case](https://wiki.c2.com/?KebabCase).</span></span> <span data-ttu-id="4613f-197">Par conséquent, pour utiliser `WebsiteInformationTagHelper` dans :::no-loc(Razor)::: , vous allez écrire `<website-information />` .</span><span class="sxs-lookup"><span data-stu-id="4613f-197">Therefore, to use the `WebsiteInformationTagHelper` in :::no-loc(Razor):::, you'll write `<website-information />`.</span></span>

   * <span data-ttu-id="4613f-198">Comme vous n’identifiez pas de manière explicite l’élément cible avec l’attribut `[HtmlTargetElement]`, la valeur par défaut de `website-information` est ciblée.</span><span class="sxs-lookup"><span data-stu-id="4613f-198">You are not explicitly identifying the target element with the `[HtmlTargetElement]` attribute, so the default of `website-information` will be targeted.</span></span> <span data-ttu-id="4613f-199">Si vous avez appliqué l’attribut suivant (notez que la casse n’est pas kebab, mais il correspond au nom de la classe) :</span><span class="sxs-lookup"><span data-stu-id="4613f-199">If you applied the following attribute (note it's not kebab case but matches the class name):</span></span>

   ```csharp
   [HtmlTargetElement("WebsiteInformation")]
   ```

   <span data-ttu-id="4613f-200">La balise en casse kebab `<website-information />` ne correspond pas.</span><span class="sxs-lookup"><span data-stu-id="4613f-200">The kebab case tag `<website-information />` wouldn't match.</span></span> <span data-ttu-id="4613f-201">Si vous voulez utiliser l’attribut `[HtmlTargetElement]`, vous employez la casse kebab comme indiqué ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="4613f-201">If you want use the `[HtmlTargetElement]` attribute, you would use kebab case as shown below:</span></span>

   ```csharp
   [HtmlTargetElement("Website-Information")]
   ```

   * <span data-ttu-id="4613f-202">Les éléments de fermeture automatique n’ont aucun contenu.</span><span class="sxs-lookup"><span data-stu-id="4613f-202">Elements that are self-closing have no content.</span></span> <span data-ttu-id="4613f-203">Pour cet exemple, le :::no-loc(Razor)::: balisage utilise une balise de fermeture automatique, mais le tag Helper crée un élément [section](https://www.w3.org/TR/html5/sections.html#the-section-element) (qui n’est pas auto-fermé et vous écrivez du contenu à l’intérieur de l' `section` élément).</span><span class="sxs-lookup"><span data-stu-id="4613f-203">For this example, the :::no-loc(Razor)::: markup will use a self-closing tag, but the tag helper will be creating a [section](https://www.w3.org/TR/html5/sections.html#the-section-element) element (which isn't self-closing and you are writing content inside the `section` element).</span></span> <span data-ttu-id="4613f-204">Par conséquent, vous devez affecter à `TagMode` la valeur `StartTagAndEndTag` pour écrire la sortie.</span><span class="sxs-lookup"><span data-stu-id="4613f-204">Therefore, you need to set `TagMode` to `StartTagAndEndTag` to write output.</span></span> <span data-ttu-id="4613f-205">Sinon, vous pouvez commenter le paramètre de ligne `TagMode` et écrire le balisage avec une balise de fermeture.</span><span class="sxs-lookup"><span data-stu-id="4613f-205">Alternatively, you can comment out the line setting `TagMode` and write markup with a closing tag.</span></span> <span data-ttu-id="4613f-206">(Un exemple de balisage est fourni plus loin dans ce didacticiel.)</span><span class="sxs-lookup"><span data-stu-id="4613f-206">(Example markup is provided later in this tutorial.)</span></span>

   * <span data-ttu-id="4613f-207">Le signe `$` (signe dollar) de la ligne suivante utilise une [chaîne interpolée](/dotnet/csharp/language-reference/keywords/interpolated-strings) :</span><span class="sxs-lookup"><span data-stu-id="4613f-207">The `$` (dollar sign) in the following line uses an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings):</span></span>

   ```cshtml
   $@"<ul><li><strong>Version:</strong> {Info.Version}</li>
   ```

1. <span data-ttu-id="4613f-208">Ajoutez le balisage suivant à la vue *About.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="4613f-208">Add the following markup to the *About.cshtml* view.</span></span> <span data-ttu-id="4613f-209">Le balisage en surbrillance affiche les informations de site web.</span><span class="sxs-lookup"><span data-stu-id="4613f-209">The highlighted markup displays the web site information.</span></span>

   [!code-cshtml[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?highlight=1,4-8, 18-999)]

   > [!NOTE]
   > <span data-ttu-id="4613f-210">Dans le :::no-loc(Razor)::: balisage illustré ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="4613f-210">In the :::no-loc(Razor)::: markup shown below:</span></span>
   >
   > [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?range=18-18)]
   >
   > <span data-ttu-id="4613f-211">:::no-loc(Razor)::: sait que l' `info` attribut est une classe, et non une chaîne, et que vous souhaitez écrire du code C#.</span><span class="sxs-lookup"><span data-stu-id="4613f-211">:::no-loc(Razor)::: knows the `info` attribute is a class, not a string, and you want to write C# code.</span></span> <span data-ttu-id="4613f-212">N’importe quel attribut de Tag Helper autre qu’une chaîne doit être écrit sans le caractère `@`.</span><span class="sxs-lookup"><span data-stu-id="4613f-212">Any non-string tag helper attribute should be written without the `@` character.</span></span>

1. <span data-ttu-id="4613f-213">Exécutez l’application et accédez à la vue About (À propos de) pour afficher les informations de site web.</span><span class="sxs-lookup"><span data-stu-id="4613f-213">Run the app, and navigate to the About view to see the web site information.</span></span>

   > [!NOTE]
   > <span data-ttu-id="4613f-214">Vous pouvez utiliser le balisage suivant avec une balise de fermeture et supprimer la ligne avec `TagMode.StartTagAndEndTag` dans le Tag Helper :</span><span class="sxs-lookup"><span data-stu-id="4613f-214">You can use the following markup with a closing tag and remove the line with `TagMode.StartTagAndEndTag` in the tag helper:</span></span>
   >
   > [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutNotSelfClosing.cshtml?range=20-21)]

## <a name="condition-tag-helper"></a><span data-ttu-id="4613f-215">Tag Helper Condition</span><span class="sxs-lookup"><span data-stu-id="4613f-215">Condition Tag Helper</span></span>

<span data-ttu-id="4613f-216">Le Tag Helper Condition restitue la sortie quand une valeur true lui est transmise.</span><span class="sxs-lookup"><span data-stu-id="4613f-216">The condition tag helper renders output when passed a true value.</span></span>

1. <span data-ttu-id="4613f-217">Ajoutez la classe `ConditionTagHelper` suivante au dossier *TagHelpers* .</span><span class="sxs-lookup"><span data-stu-id="4613f-217">Add the following `ConditionTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/ConditionTagHelper.cs)]

1. <span data-ttu-id="4613f-218">Remplacez le contenu du fichier *Views/Home/Index.cshtml* par le balisage suivant :</span><span class="sxs-lookup"><span data-stu-id="4613f-218">Replace the contents of the *Views/Home/Index.cshtml* file with the following markup:</span></span>

   [!code-cshtml[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Index.cshtml)]

1. <span data-ttu-id="4613f-219">Remplacez la méthode `Index` dans le contrôleur `Home` par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="4613f-219">Replace the `Index` method in the `Home` controller with the following code:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Controllers/HomeController.cs?range=9-18)]

1. <span data-ttu-id="4613f-220">Exécutez l’application et accédez à la page d’accueil.</span><span class="sxs-lookup"><span data-stu-id="4613f-220">Run the app and browse to the home page.</span></span> <span data-ttu-id="4613f-221">Le balisage de l’élément `div` conditionnel ne sera pas rendu.</span><span class="sxs-lookup"><span data-stu-id="4613f-221">The markup in the conditional `div` won't be rendered.</span></span> <span data-ttu-id="4613f-222">Ajoutez la chaîne de requête `?approved=true` à l’URL (par exemple, `http://localhost:1235/Home/Index?approved=true`).</span><span class="sxs-lookup"><span data-stu-id="4613f-222">Append the query string `?approved=true` to the URL (for example, `http://localhost:1235/Home/Index?approved=true`).</span></span> <span data-ttu-id="4613f-223">`approved` a la valeur true et le balisage conditionnel s’affichera.</span><span class="sxs-lookup"><span data-stu-id="4613f-223">`approved` is set to true and the conditional markup will be displayed.</span></span>

> [!NOTE]
> <span data-ttu-id="4613f-224">Utilisez l’opérateur [nameof](/dotnet/csharp/language-reference/keywords/nameof) pour spécifier l’attribut à cibler plutôt qu’une chaîne comme vous le faisiez avec le Tag Helper Bold :</span><span class="sxs-lookup"><span data-stu-id="4613f-224">Use the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator to specify the attribute to target rather than specifying a string as you did with the bold tag helper:</span></span>
>
> [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zConditionTagHelperCopy.cs?highlight=1,2,5&range=5-18)]
>
> <span data-ttu-id="4613f-225">L’opérateur [nameof](/dotnet/csharp/language-reference/keywords/nameof) protégera le code s’il devait jamais être refactorisé (le nom peut être changé en `RedCondition`).</span><span class="sxs-lookup"><span data-stu-id="4613f-225">The [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator will protect the code should it ever be refactored (we might want to change the name to `RedCondition`).</span></span>

### <a name="avoid-tag-helper-conflicts"></a><span data-ttu-id="4613f-226">Éviter les conflits de Tag Helpers</span><span class="sxs-lookup"><span data-stu-id="4613f-226">Avoid Tag Helper conflicts</span></span>

<span data-ttu-id="4613f-227">Dans cette section, vous écrivez une paire de Tag Helpers de liaison automatique.</span><span class="sxs-lookup"><span data-stu-id="4613f-227">In this section, you write a pair of auto-linking tag helpers.</span></span> <span data-ttu-id="4613f-228">Le premier remplace le balisage contenant une URL commençant par HTTP par une balise d’ancrage HTML contenant la même URL (et produisant ainsi un lien vers l’URL).</span><span class="sxs-lookup"><span data-stu-id="4613f-228">The first will replace markup containing a URL starting with HTTP to an HTML anchor tag containing the same URL (and thus yielding a link to the URL).</span></span> <span data-ttu-id="4613f-229">Le second effectue la même opération pour une URL commençant par WWW.</span><span class="sxs-lookup"><span data-stu-id="4613f-229">The second will do the same for a URL starting with WWW.</span></span>

<span data-ttu-id="4613f-230">Comme ces deux Tag Helpers sont étroitement liés et que vous pouvez les refactoriser à l’avenir, nous allons les conserver dans le même fichier.</span><span class="sxs-lookup"><span data-stu-id="4613f-230">Because these two helpers are closely related and you may refactor them in the future, we'll keep them in the same file.</span></span>

1. <span data-ttu-id="4613f-231">Ajoutez la classe `AutoLinkerHttpTagHelper` suivante au dossier *TagHelpers* .</span><span class="sxs-lookup"><span data-stu-id="4613f-231">Add the following `AutoLinkerHttpTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=7-19)]

   >[!NOTE]
   ><span data-ttu-id="4613f-232">La classe `AutoLinkerHttpTagHelper` cible les éléments `p` et utilise [Regex](/dotnet/standard/base-types/regular-expression-language-quick-reference) pour créer l’ancre.</span><span class="sxs-lookup"><span data-stu-id="4613f-232">The `AutoLinkerHttpTagHelper` class targets `p` elements and uses [Regex](/dotnet/standard/base-types/regular-expression-language-quick-reference) to create the anchor.</span></span>

1. <span data-ttu-id="4613f-233">Ajoutez le balisage suivant à la fin du fichier *Views/Home/Contact.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="4613f-233">Add the following markup to the end of the *Views/Home/Contact.cshtml* file:</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=19)]

1. <span data-ttu-id="4613f-234">Exécutez l’application et vérifiez que le Tag Helper restitue correctement l’ancre.</span><span class="sxs-lookup"><span data-stu-id="4613f-234">Run the app and verify that the tag helper renders the anchor correctly.</span></span>

1. <span data-ttu-id="4613f-235">Mettez à jour la classe `AutoLinker` pour inclure `AutoLinkerWwwTagHelper` qui convertira le texte www en une balise d’ancrage qui contient également le texte www d’origine.</span><span class="sxs-lookup"><span data-stu-id="4613f-235">Update the `AutoLinker` class to include the `AutoLinkerWwwTagHelper` which will convert www text to an anchor tag that also contains the original www text.</span></span> <span data-ttu-id="4613f-236">Le code mis à jour est en surbrillance ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="4613f-236">The updated code is highlighted below:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?highlight=15-34&range=7-34)]

1. <span data-ttu-id="4613f-237">Exécutez l’application.</span><span class="sxs-lookup"><span data-stu-id="4613f-237">Run the app.</span></span> <span data-ttu-id="4613f-238">Notez que le texte www est affiché sous forme de lien, contrairement au texte HTTP.</span><span class="sxs-lookup"><span data-stu-id="4613f-238">Notice the www text is rendered as a link but the HTTP text isn't.</span></span> <span data-ttu-id="4613f-239">Si vous placez un point d’arrêt dans les deux classes, vous pouvez voir que la classe du Tag Helper HTTP s’exécute en premier.</span><span class="sxs-lookup"><span data-stu-id="4613f-239">If you put a break point in both classes, you can see that the HTTP tag helper class runs first.</span></span> <span data-ttu-id="4613f-240">Le problème est que la sortie du Tag Helper est mise en cache et, quand le Tag Helper WWW est exécuté, il remplace la sortie mise en cache du Tag Helper HTTP.</span><span class="sxs-lookup"><span data-stu-id="4613f-240">The problem is that the tag helper output is cached, and when the WWW tag helper is run, it overwrites the cached output from the HTTP tag helper.</span></span> <span data-ttu-id="4613f-241">Plus loin dans ce didacticiel, nous verrons comment contrôler l’ordre d’exécution des Tag Helpers.</span><span class="sxs-lookup"><span data-stu-id="4613f-241">Later in the tutorial we'll see how to control the order that tag helpers run in.</span></span> <span data-ttu-id="4613f-242">Nous allons corriger le code avec les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="4613f-242">We'll fix the code with the following:</span></span>

   [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10,21,22,26&range=8-37)]

   > [!NOTE]
   > <span data-ttu-id="4613f-243">Dans la première édition des Tag Helpers de liaison automatique, vous avez obtenu le contenu de la cible avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="4613f-243">In the first edition of the auto-linking tag helpers, you got the content of the target with the following code:</span></span>
   >
   > [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=12)]
   >
   > <span data-ttu-id="4613f-244">Autrement dit, vous appelez `GetChildContentAsync` à l’aide de l’élément `TagHelperOutput` passé dans la méthode `ProcessAsync`.</span><span class="sxs-lookup"><span data-stu-id="4613f-244">That is, you call `GetChildContentAsync` using the `TagHelperOutput` passed into the `ProcessAsync` method.</span></span> <span data-ttu-id="4613f-245">Comme mentionné précédemment, étant donné que la sortie est mise en cache, le dernier Tag Helper qui est exécuté l’emporte.</span><span class="sxs-lookup"><span data-stu-id="4613f-245">As mentioned previously, because the output is cached, the last tag helper to run wins.</span></span> <span data-ttu-id="4613f-246">Vous avez résolu ce problème avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="4613f-246">You fixed that problem with the following code:</span></span>
   >
   > [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?range=34-35)]
   >
   > <span data-ttu-id="4613f-247">Le code ci-dessus vérifie si le contenu a été modifié et, le cas échéant, obtient le contenu à partir de la mémoire tampon de sortie.</span><span class="sxs-lookup"><span data-stu-id="4613f-247">The code above checks to see if the content has been modified, and if it has, it gets the content from the output buffer.</span></span>

1. <span data-ttu-id="4613f-248">Exécutez l’application et vérifiez que les deux liens fonctionnent comme prévu.</span><span class="sxs-lookup"><span data-stu-id="4613f-248">Run the app and verify that the two links work as expected.</span></span> <span data-ttu-id="4613f-249">Alors qu’il peut sembler que notre Tag Helper de liaison automatique est correct et terminé, il pose un problème subtil.</span><span class="sxs-lookup"><span data-stu-id="4613f-249">While it might appear our auto linker tag helper is correct and complete, it has a subtle problem.</span></span> <span data-ttu-id="4613f-250">Si le Tag Helper WWW est exécuté en premier, les liens www ne sont pas corrects.</span><span class="sxs-lookup"><span data-stu-id="4613f-250">If the WWW tag helper runs first, the www links won't be correct.</span></span> <span data-ttu-id="4613f-251">Mettez à jour le code en ajoutant la surcharge `Order` pour contrôler l’ordre d’exécution des Tag Helpers.</span><span class="sxs-lookup"><span data-stu-id="4613f-251">Update the code by adding the `Order` overload to control the order that the tag runs in.</span></span> <span data-ttu-id="4613f-252">La propriété `Order` détermine l’ordre d’exécution par rapport à d’autres Tag Helpers ciblant le même élément.</span><span class="sxs-lookup"><span data-stu-id="4613f-252">The `Order` property determines the execution order relative to other tag helpers targeting the same element.</span></span> <span data-ttu-id="4613f-253">La valeur d’ordre par défaut est égale à zéro et les instances ayant des valeurs inférieures sont exécutées en premier.</span><span class="sxs-lookup"><span data-stu-id="4613f-253">The default order value is zero and instances with lower values are executed first.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?highlight=5,6,7,8&range=8-15)]

   <span data-ttu-id="4613f-254">Le code précédent garantit que le Tag Helper HTTP s’exécute avant le Tag Helper WWW.</span><span class="sxs-lookup"><span data-stu-id="4613f-254">The preceding code guarantees that the HTTP tag helper runs before the WWW tag helper.</span></span> <span data-ttu-id="4613f-255">Remplacez `Order` par `MaxValue` et vérifiez que le balisage généré pour la balise WWW est incorrect.</span><span class="sxs-lookup"><span data-stu-id="4613f-255">Change `Order` to `MaxValue` and verify that the markup generated for the WWW tag is incorrect.</span></span>

## <a name="inspect-and-retrieve-child-content"></a><span data-ttu-id="4613f-256">Examiner et récupérer le contenu enfant</span><span class="sxs-lookup"><span data-stu-id="4613f-256">Inspect and retrieve child content</span></span>

<span data-ttu-id="4613f-257">Les Tag Helpers fournissent plusieurs propriétés permettant de récupérer le contenu.</span><span class="sxs-lookup"><span data-stu-id="4613f-257">The tag helpers provide several properties to retrieve content.</span></span>

* <span data-ttu-id="4613f-258">Le résultat de `GetChildContentAsync` peut être ajouté à `output.Content`.</span><span class="sxs-lookup"><span data-stu-id="4613f-258">The result of `GetChildContentAsync` can be appended to `output.Content`.</span></span>
* <span data-ttu-id="4613f-259">Vous pouvez examiner le résultat de `GetChildContentAsync` avec `GetContent`.</span><span class="sxs-lookup"><span data-stu-id="4613f-259">You can inspect the result of `GetChildContentAsync` with `GetContent`.</span></span>
* <span data-ttu-id="4613f-260">Si vous modifiez `output.Content`, le corps TagHelper n’est pas exécuté ni restitué, sauf si vous appelez `GetChildContentAsync` comme dans notre exemple de liaison automatique :</span><span class="sxs-lookup"><span data-stu-id="4613f-260">If you modify `output.Content`, the TagHelper body won't be executed or rendered unless you call `GetChildContentAsync` as in our auto-linker sample:</span></span>

[!code-csharp[](../../views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10&range=8-21)]

* <span data-ttu-id="4613f-261">Plusieurs appels à `GetChildContentAsync` retournent la même valeur et ne réexécutent pas le corps `TagHelper`, sauf si vous passez un paramètre false indiquant de ne pas utiliser le résultat mis en cache.</span><span class="sxs-lookup"><span data-stu-id="4613f-261">Multiple calls to `GetChildContentAsync` returns the same value and doesn't re-execute the `TagHelper` body unless you pass in a false parameter indicating not to use the cached result.</span></span>

## <a name="load-minified-partial-view-taghelper"></a><span data-ttu-id="4613f-262">Charger TagHelper avec une vue partielle minimisée</span><span class="sxs-lookup"><span data-stu-id="4613f-262">Load minified partial view TagHelper</span></span>

<span data-ttu-id="4613f-263">Dans les environnements de production, les performances peuvent être améliorées en chargeant des vues partielles minimisées.</span><span class="sxs-lookup"><span data-stu-id="4613f-263">In production environments, performance can be improved by loading minified partial views.</span></span> <span data-ttu-id="4613f-264">Pour tirer parti de la vue partielle minimisée en production :</span><span class="sxs-lookup"><span data-stu-id="4613f-264">To take advantage of minified partial view in production:</span></span>

* <span data-ttu-id="4613f-265">Créez/configurez un processus pré-build qui minimise les vues partielles.</span><span class="sxs-lookup"><span data-stu-id="4613f-265">Create/set up a pre-build process that minifies partial views.</span></span>
* <span data-ttu-id="4613f-266">Utilisez le code suivant pour charger des vues partielles minimisées dans des environnements non liés au développement.</span><span class="sxs-lookup"><span data-stu-id="4613f-266">Use the following code to load  minified partial views in non-development environments.</span></span>

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/MinifiedVersionTagHelper.cs?name=snippet)]
