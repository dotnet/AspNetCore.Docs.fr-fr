---
title: Configurer la localisation d’objets portables dans ASP.NET Core
author: sebastienros
description: Cet article présente les fichiers d’objets portables et décrit les étapes à suivre pour les utiliser dans une application ASP.NET Core avec le framework Orchard Core.
ms.author: scaddie
ms.date: 09/26/2017
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: fundamentals/portable-object-localization
ms.openlocfilehash: 2e28ebaf1962ebd834c43f1cfbc28929b1937c40
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053720"
---
# <a name="configure-portable-object-localization-in-aspnet-core"></a><span data-ttu-id="2c067-103">Configurer la localisation d’objets portables dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2c067-103">Configure portable object localization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2c067-104">Par [Sébastien Ros](https://github.com/sebastienros), [Scott Addie](https://twitter.com/Scott_Addie) et [Hisham bin Ateya](https://github.com/hishamco)</span><span class="sxs-lookup"><span data-stu-id="2c067-104">By [Sébastien Ros](https://github.com/sebastienros), [Scott Addie](https://twitter.com/Scott_Addie) and [Hisham Bin Ateya](https://github.com/hishamco)</span></span>

<span data-ttu-id="2c067-105">Cet article présente les étapes d’utilisation d’objets portables (PO, Portable Object) dans une application ASP.NET Core avec le framework [Orchard Core](https://github.com/OrchardCMS/OrchardCore).</span><span class="sxs-lookup"><span data-stu-id="2c067-105">This article walks through the steps for using Portable Object (PO) files in an ASP.NET Core application with the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) framework.</span></span>

<span data-ttu-id="2c067-106">**Remarque :** Orchard Core n’est pas un produit Microsoft.</span><span class="sxs-lookup"><span data-stu-id="2c067-106">**Note:** Orchard Core isn't a Microsoft product.</span></span> <span data-ttu-id="2c067-107">Par conséquent, Microsoft ne fournit aucun support pour cette fonctionnalité.</span><span class="sxs-lookup"><span data-stu-id="2c067-107">Consequently, Microsoft provides no support for this feature.</span></span>

<span data-ttu-id="2c067-108">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/3.x/POLocalization) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2c067-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/3.x/POLocalization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="what-is-a-po-file"></a><span data-ttu-id="2c067-109">Qu’est-ce qu’un fichier PO ?</span><span class="sxs-lookup"><span data-stu-id="2c067-109">What is a PO file?</span></span>

<span data-ttu-id="2c067-110">Les fichiers PO sont distribués sous forme de fichiers texte contenant les chaînes traduites pour une langue donnée.</span><span class="sxs-lookup"><span data-stu-id="2c067-110">PO files are distributed as text files containing the translated strings for a given language.</span></span> <span data-ttu-id="2c067-111">L’utilisation de fichiers PO plutôt que des fichiers *.resx* présente certains avantages, notamment les suivants :</span><span class="sxs-lookup"><span data-stu-id="2c067-111">Some advantages of using PO files instead *.resx* files include:</span></span>
- <span data-ttu-id="2c067-112">Les fichiers PO prennent en charge la pluralisation, contrairement aux fichiers *.resx* .</span><span class="sxs-lookup"><span data-stu-id="2c067-112">PO files support pluralization; *.resx* files don't support pluralization.</span></span>
- <span data-ttu-id="2c067-113">Les fichiers PO ne sont pas compilés comme les fichiers *.resx* .</span><span class="sxs-lookup"><span data-stu-id="2c067-113">PO files aren't compiled like *.resx* files.</span></span> <span data-ttu-id="2c067-114">De ce fait, il n’est pas nécessaire d’effectuer les étapes relatives aux outils et à la génération.</span><span class="sxs-lookup"><span data-stu-id="2c067-114">As such, specialized tooling and build steps aren't required.</span></span>
- <span data-ttu-id="2c067-115">Les fichiers PO fonctionnent correctement avec les outils d’édition collaboratifs en ligne.</span><span class="sxs-lookup"><span data-stu-id="2c067-115">PO files work well with collaborative online editing tools.</span></span>

### <a name="example"></a><span data-ttu-id="2c067-116">Exemple</span><span class="sxs-lookup"><span data-stu-id="2c067-116">Example</span></span>

<span data-ttu-id="2c067-117">Voici un exemple de fichier PO contenant la traduction de deux chaînes en français, dont une avec sa forme au pluriel :</span><span class="sxs-lookup"><span data-stu-id="2c067-117">Here is a sample PO file containing the translation for two strings in French, including one with its plural form:</span></span>

<span data-ttu-id="2c067-118">*fr.po*</span><span class="sxs-lookup"><span data-stu-id="2c067-118">*fr.po*</span></span>

```text
#: Services/EmailService.cs:29
msgid "Enter a comma separated list of email addresses."
msgstr "Entrez une liste d'emails séparés par une virgule."

#: Views/Email.cshtml:112
msgid "The email address is \"{0}\"."
msgid_plural "The email addresses are \"{0}\"."
msgstr[0] "L'adresse email est \"{0}\"."
msgstr[1] "Les adresses email sont \"{0}\""
```

<span data-ttu-id="2c067-119">Cet exemple utilise la syntaxe suivante :</span><span class="sxs-lookup"><span data-stu-id="2c067-119">This example uses the following syntax:</span></span>

- <span data-ttu-id="2c067-120">`#:` : commentaire indiquant le contexte de la chaîne à traduire.</span><span class="sxs-lookup"><span data-stu-id="2c067-120">`#:`: A comment indicating the context of the string to be translated.</span></span> <span data-ttu-id="2c067-121">La même chaîne peut être traduite différemment selon l’endroit où elle est utilisée.</span><span class="sxs-lookup"><span data-stu-id="2c067-121">The same string might be translated differently depending on where it's being used.</span></span>
- <span data-ttu-id="2c067-122">`msgid` : la chaîne non traduite.</span><span class="sxs-lookup"><span data-stu-id="2c067-122">`msgid`: The untranslated string.</span></span>
- <span data-ttu-id="2c067-123">`msgstr` : la chaîne traduite.</span><span class="sxs-lookup"><span data-stu-id="2c067-123">`msgstr`: The translated string.</span></span>

<span data-ttu-id="2c067-124">Dans le cas de la prise en charge de la pluralisation, des entrées supplémentaires peuvent être définies.</span><span class="sxs-lookup"><span data-stu-id="2c067-124">In the case of pluralization support, more entries can be defined.</span></span>

- <span data-ttu-id="2c067-125">`msgid_plural` : la chaîne au pluriel non traduite.</span><span class="sxs-lookup"><span data-stu-id="2c067-125">`msgid_plural`: The untranslated plural string.</span></span>
- <span data-ttu-id="2c067-126">`msgstr[0]` : la chaîne traduite pour le cas 0.</span><span class="sxs-lookup"><span data-stu-id="2c067-126">`msgstr[0]`: The translated string for the case 0.</span></span>
- <span data-ttu-id="2c067-127">`msgstr[N]` : la chaîne traduite pour le cas N.</span><span class="sxs-lookup"><span data-stu-id="2c067-127">`msgstr[N]`: The translated string for the case N.</span></span>

<span data-ttu-id="2c067-128">La spécification du fichier PO se trouve [ici](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span><span class="sxs-lookup"><span data-stu-id="2c067-128">The PO file specification can be found [here](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span></span>

## <a name="configuring-po-file-support-in-aspnet-core"></a><span data-ttu-id="2c067-129">Configuration de la prise en charge du fichier PO dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2c067-129">Configuring PO file support in ASP.NET Core</span></span>

<span data-ttu-id="2c067-130">Cet exemple est basé sur une application ASP.NET Core MVC générée à partir d’un modèle de projet Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="2c067-130">This example is based on an ASP.NET Core MVC application generated from a Visual Studio 2017 project template.</span></span>

### <a name="referencing-the-package"></a><span data-ttu-id="2c067-131">Référencement du package</span><span class="sxs-lookup"><span data-stu-id="2c067-131">Referencing the package</span></span>

<span data-ttu-id="2c067-132">Ajoutez une référence au package NuGet `OrchardCore.Localization.Core`.</span><span class="sxs-lookup"><span data-stu-id="2c067-132">Add a reference to the `OrchardCore.Localization.Core` NuGet package.</span></span> <span data-ttu-id="2c067-133">Il est disponible sur [MyGet](https://www.myget.org/) dans la source de package suivante : https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span><span class="sxs-lookup"><span data-stu-id="2c067-133">It's available on [MyGet](https://www.myget.org/) at the following package source: https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span></span>

<span data-ttu-id="2c067-134">Le fichier *.csproj* contient maintenant une ligne semblable à la suivante (le numéro de version peut varier) :</span><span class="sxs-lookup"><span data-stu-id="2c067-134">The *.csproj* file now contains a line similar to the following (version number may vary):</span></span>

[!code-xml[](localization/sample/3.x/POLocalization/POLocalization.csproj?range=8)]

### <a name="registering-the-service"></a><span data-ttu-id="2c067-135">Inscription du service</span><span class="sxs-lookup"><span data-stu-id="2c067-135">Registering the service</span></span>

<span data-ttu-id="2c067-136">Ajoutez les services nécessaires à la méthode `ConfigureServices` de *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="2c067-136">Add the required services to the `ConfigureServices` method of *Startup.cs* :</span></span>

[!code-csharp[](localization/sample/3.x/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

<span data-ttu-id="2c067-137">Ajoutez l’intergiciel (middleware) nécessaire à la méthode `Configure` de *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="2c067-137">Add the required middleware to the `Configure` method of *Startup.cs* :</span></span>

[!code-csharp[](localization/sample/3.x/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="2c067-138">Ajoutez le code suivant à votre Razor vue de votre choix.</span><span class="sxs-lookup"><span data-stu-id="2c067-138">Add the following code to your Razor view of choice.</span></span> <span data-ttu-id="2c067-139">*About.cshtml* est utilisé dans cet exemple.</span><span class="sxs-lookup"><span data-stu-id="2c067-139">*About.cshtml* is used in this example.</span></span>

[!code-cshtml[](localization/sample/3.x/POLocalization/Views/Home/About.cshtml)]

<span data-ttu-id="2c067-140">Une instance `IViewLocalizer` est injectée et utilisée pour traduire le texte « Hello world! ».</span><span class="sxs-lookup"><span data-stu-id="2c067-140">An `IViewLocalizer` instance is injected and used to translate the text "Hello world!".</span></span>

### <a name="creating-a-po-file"></a><span data-ttu-id="2c067-141">Création d’un fichier PO</span><span class="sxs-lookup"><span data-stu-id="2c067-141">Creating a PO file</span></span>

<span data-ttu-id="2c067-142">Créez un fichier nommé *\<culture code> . po* dans le dossier racine de votre application.</span><span class="sxs-lookup"><span data-stu-id="2c067-142">Create a file named *\<culture code>.po* in your application root folder.</span></span> <span data-ttu-id="2c067-143">Dans cet exemple, le nom de fichier est *fr.po* , car le français est utilisé :</span><span class="sxs-lookup"><span data-stu-id="2c067-143">In this example, the file name is *fr.po* because the French language is used:</span></span>

[!code-text[](localization/sample/3.x/POLocalization/fr.po)]

<span data-ttu-id="2c067-144">Ce fichier stocke à la fois la chaîne à traduire et la chaîne traduite en français.</span><span class="sxs-lookup"><span data-stu-id="2c067-144">This file stores both the string to translate and the French-translated string.</span></span> <span data-ttu-id="2c067-145">La culture parente des traductions peut être rétablie, si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="2c067-145">Translations revert to their parent culture, if necessary.</span></span> <span data-ttu-id="2c067-146">Dans cet exemple, le fichier *fr.po* est utilisé si la culture demandée est `fr-FR` ou `fr-CA`.</span><span class="sxs-lookup"><span data-stu-id="2c067-146">In this example, the *fr.po* file is used if the requested culture is `fr-FR` or `fr-CA`.</span></span>

### <a name="testing-the-application"></a><span data-ttu-id="2c067-147">Test de l’application</span><span class="sxs-lookup"><span data-stu-id="2c067-147">Testing the application</span></span>

<span data-ttu-id="2c067-148">Exécutez votre application, puis accédez à l’URL `/Home/About`.</span><span class="sxs-lookup"><span data-stu-id="2c067-148">Run your application, and navigate to the URL `/Home/About`.</span></span> <span data-ttu-id="2c067-149">Le texte **Hello world!**</span><span class="sxs-lookup"><span data-stu-id="2c067-149">The text **Hello world!**</span></span> <span data-ttu-id="2c067-150">ne s'affiche.</span><span class="sxs-lookup"><span data-stu-id="2c067-150">is displayed.</span></span>

<span data-ttu-id="2c067-151">Accédez à l’URL `/Home/About?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="2c067-151">Navigate to the URL `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="2c067-152">Le texte **Bonjour le monde !**</span><span class="sxs-lookup"><span data-stu-id="2c067-152">The text **Bonjour le monde!**</span></span> <span data-ttu-id="2c067-153">ne s'affiche.</span><span class="sxs-lookup"><span data-stu-id="2c067-153">is displayed.</span></span>

## <a name="pluralization"></a><span data-ttu-id="2c067-154">Forme plurielle</span><span class="sxs-lookup"><span data-stu-id="2c067-154">Pluralization</span></span>

<span data-ttu-id="2c067-155">Les fichiers PO prennent en charge les formes de pluralisation, ce qui est utile quand la même chaîne doit être traduite différemment en fonction d’une cardinalité.</span><span class="sxs-lookup"><span data-stu-id="2c067-155">PO files support pluralization forms, which is useful when the same string needs to be translated differently based on a cardinality.</span></span> <span data-ttu-id="2c067-156">Cette tâche est rendue compliquée par le fait que chaque langue définit des règles personnalisées pour sélectionner la chaîne à utiliser en fonction de la cardinalité.</span><span class="sxs-lookup"><span data-stu-id="2c067-156">This task is made complicated by the fact that each language defines custom rules to select which string to use based on the cardinality.</span></span>

<span data-ttu-id="2c067-157">Le package de localisation Orchard fournit une API pour appeler automatiquement ces différentes formes plurielles.</span><span class="sxs-lookup"><span data-stu-id="2c067-157">The Orchard Localization package provides an API to invoke these different plural forms automatically.</span></span>

### <a name="creating-pluralization-po-files"></a><span data-ttu-id="2c067-158">Création de fichiers PO de pluralisation</span><span class="sxs-lookup"><span data-stu-id="2c067-158">Creating pluralization PO files</span></span>

<span data-ttu-id="2c067-159">Ajoutez le contenu suivant au fichier *fr.po* mentionné précédemment :</span><span class="sxs-lookup"><span data-stu-id="2c067-159">Add the following content to the previously mentioned *fr.po* file:</span></span>

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

<span data-ttu-id="2c067-160">Consultez [Qu’est-ce qu’un fichier PO ?](#what-is-a-po-file) pour obtenir une explication de ce que représente chaque entrée de cet exemple.</span><span class="sxs-lookup"><span data-stu-id="2c067-160">See [What is a PO file?](#what-is-a-po-file) for an explanation of what each entry in this example represents.</span></span>

### <a name="adding-a-language-using-different-pluralization-forms"></a><span data-ttu-id="2c067-161">Ajout d’une langue utilisant différentes formes de pluralisation</span><span class="sxs-lookup"><span data-stu-id="2c067-161">Adding a language using different pluralization forms</span></span>

<span data-ttu-id="2c067-162">Des chaînes en anglais et en français ont été utilisées dans l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="2c067-162">English and French strings were used in the previous example.</span></span> <span data-ttu-id="2c067-163">L’anglais et le français n’ont que deux formes de pluralisation et partagent les mêmes règles de forme, qui est qu’une cardinalité de 1 est mappée à la première forme plurielle.</span><span class="sxs-lookup"><span data-stu-id="2c067-163">English and French have only two pluralization forms and share the same form rules, which is that a cardinality of one is mapped to the first plural form.</span></span> <span data-ttu-id="2c067-164">N’importe quelle autre cardinalité est mappée à la seconde forme plurielle.</span><span class="sxs-lookup"><span data-stu-id="2c067-164">Any other cardinality is mapped to the second plural form.</span></span>

<span data-ttu-id="2c067-165">Toutes les langues ne partagent pas les mêmes règles.</span><span class="sxs-lookup"><span data-stu-id="2c067-165">Not all languages share the same rules.</span></span> <span data-ttu-id="2c067-166">À titre d’exemple, il y a la langue tchèque qui a trois formes plurielles.</span><span class="sxs-lookup"><span data-stu-id="2c067-166">This is illustrated with the Czech language, which has three plural forms.</span></span>

<span data-ttu-id="2c067-167">Créez le fichier `cs.po` comme suit, puis notez comment la pluralisation a besoin de trois traductions différentes :</span><span class="sxs-lookup"><span data-stu-id="2c067-167">Create the `cs.po` file as follows, and note how the pluralization needs three different translations:</span></span>

[!code-text[](localization/sample/3.x/POLocalization/cs.po)]

<span data-ttu-id="2c067-168">Pour accepter les localisations tchèques, ajoutez `"cs"` à la liste des cultures prises en charge dans la méthode `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2c067-168">To accept Czech localizations, add `"cs"` to the list of supported cultures in the `ConfigureServices` method:</span></span>

```csharp
var supportedCultures = new List<CultureInfo>
{
    new CultureInfo("en-US"),
    new CultureInfo("en"),
    new CultureInfo("fr-FR"),
    new CultureInfo("fr"),
    new CultureInfo("cs")
};
```

<span data-ttu-id="2c067-169">Modifiez le fichier *Views/Home/About.cshtml* pour restituer des chaînes localisées au pluriel pour plusieurs cardinalités :</span><span class="sxs-lookup"><span data-stu-id="2c067-169">Edit the *Views/Home/About.cshtml* file to render localized, plural strings for several cardinalities:</span></span>

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

<span data-ttu-id="2c067-170">**Remarque :** Dans un scénario réel, une variable serait utilisée pour représenter le nombre.</span><span class="sxs-lookup"><span data-stu-id="2c067-170">**Note:** In a real world scenario, a variable would be used to represent the count.</span></span> <span data-ttu-id="2c067-171">Ici, nous répétons le même code avec trois valeurs différentes pour exposer un cas très spécifique.</span><span class="sxs-lookup"><span data-stu-id="2c067-171">Here, we repeat the same code with three different values to expose a very specific case.</span></span>

<span data-ttu-id="2c067-172">Quand vous passez d’une culture à l’autre, vous voyez ceci :</span><span class="sxs-lookup"><span data-stu-id="2c067-172">Upon switching cultures, you see the following:</span></span>

<span data-ttu-id="2c067-173">Pour `/Home/About`:</span><span class="sxs-lookup"><span data-stu-id="2c067-173">For `/Home/About`:</span></span>

```html
There is one item.
There are 2 items.
There are 5 items.
```

<span data-ttu-id="2c067-174">Pour `/Home/About?culture=fr`:</span><span class="sxs-lookup"><span data-stu-id="2c067-174">For `/Home/About?culture=fr`:</span></span>

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

<span data-ttu-id="2c067-175">Pour `/Home/About?culture=cs`:</span><span class="sxs-lookup"><span data-stu-id="2c067-175">For `/Home/About?culture=cs`:</span></span>

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

<span data-ttu-id="2c067-176">Notez que pour la culture tchèque, les trois traductions sont différentes.</span><span class="sxs-lookup"><span data-stu-id="2c067-176">Note that for the Czech culture, the three translations are different.</span></span> <span data-ttu-id="2c067-177">Les cultures anglaise et française partagent la même construction pour les deux dernières chaînes traduites.</span><span class="sxs-lookup"><span data-stu-id="2c067-177">The French and English cultures share the same construction for the two last translated strings.</span></span>

## <a name="advanced-tasks"></a><span data-ttu-id="2c067-178">Tâches avancées</span><span class="sxs-lookup"><span data-stu-id="2c067-178">Advanced tasks</span></span>

### <a name="contextualizing-strings"></a><span data-ttu-id="2c067-179">Contextualisation de chaînes</span><span class="sxs-lookup"><span data-stu-id="2c067-179">Contextualizing strings</span></span>

<span data-ttu-id="2c067-180">Les applications contiennent souvent les chaînes à traduire dans plusieurs emplacements.</span><span class="sxs-lookup"><span data-stu-id="2c067-180">Applications often contain the strings to be translated in several places.</span></span> <span data-ttu-id="2c067-181">La même chaîne peut avoir une traduction différente dans certains emplacements d’une application ( Razor vues ou fichiers de classe).</span><span class="sxs-lookup"><span data-stu-id="2c067-181">The same string may have a different translation in certain locations within an app (Razor views or class files).</span></span> <span data-ttu-id="2c067-182">Un fichier PO prend en charge la notion d’un contexte de fichier, qui peut être utilisé pour catégoriser la chaîne représentée.</span><span class="sxs-lookup"><span data-stu-id="2c067-182">A PO file supports the notion of a file context, which can be used to categorize the string being represented.</span></span> <span data-ttu-id="2c067-183">L’utilisation d’un contexte de fichier permet de traduire une chaîne différemment en fonction du contexte du fichier (ou de l’absence de contexte de fichier).</span><span class="sxs-lookup"><span data-stu-id="2c067-183">Using a file context, a string can be translated differently, depending on the file context (or lack of a file context).</span></span>

<span data-ttu-id="2c067-184">Les services de localisation de PO utilisent le nom de la classe complète ou de la vue utilisée lors de la traduction d’une chaîne.</span><span class="sxs-lookup"><span data-stu-id="2c067-184">The PO localization services use the name of the full class or the view that's used when translating a string.</span></span> <span data-ttu-id="2c067-185">Pour ce faire, il vous suffit de définir la valeur sur l’entrée `msgctxt`.</span><span class="sxs-lookup"><span data-stu-id="2c067-185">This is accomplished by setting the value on the `msgctxt` entry.</span></span>

<span data-ttu-id="2c067-186">Examinons un ajout mineur à l’exemple *fr.po* précédent.</span><span class="sxs-lookup"><span data-stu-id="2c067-186">Consider a minor addition to the previous *fr.po* example.</span></span> <span data-ttu-id="2c067-187">Vous Razor pouvez définir une vue située dans *views/orig/about. cshtml* comme contexte de fichier en définissant la `msgctxt` valeur de l’entrée réservée :</span><span class="sxs-lookup"><span data-stu-id="2c067-187">A Razor view located at *Views/Home/About.cshtml* can be defined as the file context by setting the reserved `msgctxt` entry's value:</span></span>

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

<span data-ttu-id="2c067-188">Avec `msgctxt` ainsi définie, la traduction de texte se produit lors de la navigation vers `/Home/About?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="2c067-188">With the `msgctxt` set as such, text translation occurs when navigating to `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="2c067-189">La traduction ne se produit pas lors de la navigation vers `/Home/Contact?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="2c067-189">The translation won't occur when navigating to `/Home/Contact?culture=fr-FR`.</span></span>

<span data-ttu-id="2c067-190">Quand aucune entrée spécifique ne correspond à un contexte de fichier donné, le mécanisme de secours d’Orchard Core recherche un fichier PO approprié sans contexte.</span><span class="sxs-lookup"><span data-stu-id="2c067-190">When no specific entry is matched with a given file context, Orchard Core's fallback mechanism looks for an appropriate PO file without a context.</span></span> <span data-ttu-id="2c067-191">En supposant qu’aucun contexte de fichier spécifique n’est défini pour *Views/Home/Contact.cshtml* , la navigation vers `/Home/Contact?culture=fr-FR` charge un fichier PO tel que le suivant :</span><span class="sxs-lookup"><span data-stu-id="2c067-191">Assuming there's no specific file context defined for *Views/Home/Contact.cshtml* , navigating to `/Home/Contact?culture=fr-FR` loads a PO file such as:</span></span>

[!code-text[](localization/sample/3.x/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a><span data-ttu-id="2c067-192">Changement de l’emplacement des fichiers PO</span><span class="sxs-lookup"><span data-stu-id="2c067-192">Changing the location of PO files</span></span>

<span data-ttu-id="2c067-193">L’emplacement par défaut des fichiers PO peut être changé dans `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2c067-193">The default location of PO files can be changed in `ConfigureServices`:</span></span>

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

<span data-ttu-id="2c067-194">Dans cet exemple, les fichiers PO sont chargés à partir du dossier *Localisation* .</span><span class="sxs-lookup"><span data-stu-id="2c067-194">In this example, the PO files are loaded from the *Localization* folder.</span></span>

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a><span data-ttu-id="2c067-195">Implémentation d’une logique personnalisée pour la recherche de fichiers de localisation</span><span class="sxs-lookup"><span data-stu-id="2c067-195">Implementing a custom logic for finding localization files</span></span>

<span data-ttu-id="2c067-196">Quand une logique plus complexe est nécessaire pour localiser des fichiers PO, l’interface `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` peut être implémentée et inscrite comme service.</span><span class="sxs-lookup"><span data-stu-id="2c067-196">When more complex logic is needed to locate PO files, the `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` interface can be implemented and registered as a service.</span></span> <span data-ttu-id="2c067-197">Cela est utile quand les fichiers PO peuvent être stockés dans différents emplacements ou quand la recherche des fichiers doit être effectuée dans une hiérarchie de dossiers.</span><span class="sxs-lookup"><span data-stu-id="2c067-197">This is useful when PO files can be stored in varying locations or when the files have to be found within a hierarchy of folders.</span></span>

### <a name="using-a-different-default-pluralized-language"></a><span data-ttu-id="2c067-198">Utilisation d’une autre langue pluralisée par défaut</span><span class="sxs-lookup"><span data-stu-id="2c067-198">Using a different default pluralized language</span></span>

<span data-ttu-id="2c067-199">Le package inclut une méthode d’extension `Plural` spécifique à deux formes plurielles.</span><span class="sxs-lookup"><span data-stu-id="2c067-199">The package includes a `Plural` extension method that's specific to two plural forms.</span></span> <span data-ttu-id="2c067-200">Pour les langues nécessitant plus de formes plurielles, créez une méthode d’extension.</span><span class="sxs-lookup"><span data-stu-id="2c067-200">For languages requiring more plural forms, create an extension method.</span></span> <span data-ttu-id="2c067-201">Avec une méthode d’extension, vous n’avez pas à fournir de fichier de localisation pour la langue par défaut : en effet, les chaînes d’origine sont déjà disponibles directement dans le code.</span><span class="sxs-lookup"><span data-stu-id="2c067-201">With an extension method, you won't need to provide any localization file for the default language &mdash; the original strings are already available directly in the code.</span></span>

<span data-ttu-id="2c067-202">Vous pouvez utiliser la surcharge `Plural(int count, string[] pluralForms, params object[] arguments)` plus générique qui accepte un tableau de chaînes des traductions.</span><span class="sxs-lookup"><span data-stu-id="2c067-202">You can use the more generic `Plural(int count, string[] pluralForms, params object[] arguments)` overload which accepts a string array of translations.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2c067-203">Par [Sébastien Ros](https://github.com/sebastienros) et [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="2c067-203">By [Sébastien Ros](https://github.com/sebastienros) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="2c067-204">Cet article présente les étapes d’utilisation d’objets portables (PO, Portable Object) dans une application ASP.NET Core avec le framework [Orchard Core](https://github.com/OrchardCMS/OrchardCore).</span><span class="sxs-lookup"><span data-stu-id="2c067-204">This article walks through the steps for using Portable Object (PO) files in an ASP.NET Core application with the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) framework.</span></span>

<span data-ttu-id="2c067-205">**Remarque :** Orchard Core n’est pas un produit Microsoft.</span><span class="sxs-lookup"><span data-stu-id="2c067-205">**Note:** Orchard Core isn't a Microsoft product.</span></span> <span data-ttu-id="2c067-206">Par conséquent, Microsoft ne fournit aucun support pour cette fonctionnalité.</span><span class="sxs-lookup"><span data-stu-id="2c067-206">Consequently, Microsoft provides no support for this feature.</span></span>

<span data-ttu-id="2c067-207">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/2.x/POLocalization) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2c067-207">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/2.x/POLocalization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="what-is-a-po-file"></a><span data-ttu-id="2c067-208">Qu’est-ce qu’un fichier PO ?</span><span class="sxs-lookup"><span data-stu-id="2c067-208">What is a PO file?</span></span>

<span data-ttu-id="2c067-209">Les fichiers PO sont distribués sous forme de fichiers texte contenant les chaînes traduites pour une langue donnée.</span><span class="sxs-lookup"><span data-stu-id="2c067-209">PO files are distributed as text files containing the translated strings for a given language.</span></span> <span data-ttu-id="2c067-210">L’utilisation de fichiers PO plutôt que des fichiers *.resx* présente certains avantages, notamment les suivants :</span><span class="sxs-lookup"><span data-stu-id="2c067-210">Some advantages of using PO files instead *.resx* files include:</span></span>
- <span data-ttu-id="2c067-211">Les fichiers PO prennent en charge la pluralisation, contrairement aux fichiers *.resx* .</span><span class="sxs-lookup"><span data-stu-id="2c067-211">PO files support pluralization; *.resx* files don't support pluralization.</span></span>
- <span data-ttu-id="2c067-212">Les fichiers PO ne sont pas compilés comme les fichiers *.resx* .</span><span class="sxs-lookup"><span data-stu-id="2c067-212">PO files aren't compiled like *.resx* files.</span></span> <span data-ttu-id="2c067-213">De ce fait, il n’est pas nécessaire d’effectuer les étapes relatives aux outils et à la génération.</span><span class="sxs-lookup"><span data-stu-id="2c067-213">As such, specialized tooling and build steps aren't required.</span></span>
- <span data-ttu-id="2c067-214">Les fichiers PO fonctionnent correctement avec les outils d’édition collaboratifs en ligne.</span><span class="sxs-lookup"><span data-stu-id="2c067-214">PO files work well with collaborative online editing tools.</span></span>

### <a name="example"></a><span data-ttu-id="2c067-215">Exemple</span><span class="sxs-lookup"><span data-stu-id="2c067-215">Example</span></span>

<span data-ttu-id="2c067-216">Voici un exemple de fichier PO contenant la traduction de deux chaînes en français, dont une avec sa forme au pluriel :</span><span class="sxs-lookup"><span data-stu-id="2c067-216">Here is a sample PO file containing the translation for two strings in French, including one with its plural form:</span></span>

<span data-ttu-id="2c067-217">*fr.po*</span><span class="sxs-lookup"><span data-stu-id="2c067-217">*fr.po*</span></span>

```text
#: Services/EmailService.cs:29
msgid "Enter a comma separated list of email addresses."
msgstr "Entrez une liste d'emails séparés par une virgule."

#: Views/Email.cshtml:112
msgid "The email address is \"{0}\"."
msgid_plural "The email addresses are \"{0}\"."
msgstr[0] "L'adresse email est \"{0}\"."
msgstr[1] "Les adresses email sont \"{0}\""
```

<span data-ttu-id="2c067-218">Cet exemple utilise la syntaxe suivante :</span><span class="sxs-lookup"><span data-stu-id="2c067-218">This example uses the following syntax:</span></span>

- <span data-ttu-id="2c067-219">`#:` : commentaire indiquant le contexte de la chaîne à traduire.</span><span class="sxs-lookup"><span data-stu-id="2c067-219">`#:`: A comment indicating the context of the string to be translated.</span></span> <span data-ttu-id="2c067-220">La même chaîne peut être traduite différemment selon l’endroit où elle est utilisée.</span><span class="sxs-lookup"><span data-stu-id="2c067-220">The same string might be translated differently depending on where it's being used.</span></span>
- <span data-ttu-id="2c067-221">`msgid` : la chaîne non traduite.</span><span class="sxs-lookup"><span data-stu-id="2c067-221">`msgid`: The untranslated string.</span></span>
- <span data-ttu-id="2c067-222">`msgstr` : la chaîne traduite.</span><span class="sxs-lookup"><span data-stu-id="2c067-222">`msgstr`: The translated string.</span></span>

<span data-ttu-id="2c067-223">Dans le cas de la prise en charge de la pluralisation, des entrées supplémentaires peuvent être définies.</span><span class="sxs-lookup"><span data-stu-id="2c067-223">In the case of pluralization support, more entries can be defined.</span></span>

- <span data-ttu-id="2c067-224">`msgid_plural` : la chaîne au pluriel non traduite.</span><span class="sxs-lookup"><span data-stu-id="2c067-224">`msgid_plural`: The untranslated plural string.</span></span>
- <span data-ttu-id="2c067-225">`msgstr[0]` : la chaîne traduite pour le cas 0.</span><span class="sxs-lookup"><span data-stu-id="2c067-225">`msgstr[0]`: The translated string for the case 0.</span></span>
- <span data-ttu-id="2c067-226">`msgstr[N]` : la chaîne traduite pour le cas N.</span><span class="sxs-lookup"><span data-stu-id="2c067-226">`msgstr[N]`: The translated string for the case N.</span></span>

<span data-ttu-id="2c067-227">La spécification du fichier PO se trouve [ici](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span><span class="sxs-lookup"><span data-stu-id="2c067-227">The PO file specification can be found [here](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span></span>

## <a name="configuring-po-file-support-in-aspnet-core"></a><span data-ttu-id="2c067-228">Configuration de la prise en charge du fichier PO dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2c067-228">Configuring PO file support in ASP.NET Core</span></span>

<span data-ttu-id="2c067-229">Cet exemple est basé sur une application ASP.NET Core MVC générée à partir d’un modèle de projet Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="2c067-229">This example is based on an ASP.NET Core MVC application generated from a Visual Studio 2017 project template.</span></span>

### <a name="referencing-the-package"></a><span data-ttu-id="2c067-230">Référencement du package</span><span class="sxs-lookup"><span data-stu-id="2c067-230">Referencing the package</span></span>

<span data-ttu-id="2c067-231">Ajoutez une référence au package NuGet `OrchardCore.Localization.Core`.</span><span class="sxs-lookup"><span data-stu-id="2c067-231">Add a reference to the `OrchardCore.Localization.Core` NuGet package.</span></span> <span data-ttu-id="2c067-232">Il est disponible sur [MyGet](https://www.myget.org/) dans la source de package suivante : https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span><span class="sxs-lookup"><span data-stu-id="2c067-232">It's available on [MyGet](https://www.myget.org/) at the following package source: https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span></span>

<span data-ttu-id="2c067-233">Le fichier *.csproj* contient maintenant une ligne semblable à la suivante (le numéro de version peut varier) :</span><span class="sxs-lookup"><span data-stu-id="2c067-233">The *.csproj* file now contains a line similar to the following (version number may vary):</span></span>

[!code-xml[](localization/sample/2.x/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a><span data-ttu-id="2c067-234">Inscription du service</span><span class="sxs-lookup"><span data-stu-id="2c067-234">Registering the service</span></span>

<span data-ttu-id="2c067-235">Ajoutez les services nécessaires à la méthode `ConfigureServices` de *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="2c067-235">Add the required services to the `ConfigureServices` method of *Startup.cs* :</span></span>

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

<span data-ttu-id="2c067-236">Ajoutez l’intergiciel (middleware) nécessaire à la méthode `Configure` de *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="2c067-236">Add the required middleware to the `Configure` method of *Startup.cs* :</span></span>

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="2c067-237">Ajoutez le code suivant à votre Razor vue de votre choix.</span><span class="sxs-lookup"><span data-stu-id="2c067-237">Add the following code to your Razor view of choice.</span></span> <span data-ttu-id="2c067-238">*About.cshtml* est utilisé dans cet exemple.</span><span class="sxs-lookup"><span data-stu-id="2c067-238">*About.cshtml* is used in this example.</span></span>

[!code-cshtml[](localization/sample/2.x/POLocalization/Views/Home/About.cshtml)]

<span data-ttu-id="2c067-239">Une instance `IViewLocalizer` est injectée et utilisée pour traduire le texte « Hello world! ».</span><span class="sxs-lookup"><span data-stu-id="2c067-239">An `IViewLocalizer` instance is injected and used to translate the text "Hello world!".</span></span>

### <a name="creating-a-po-file"></a><span data-ttu-id="2c067-240">Création d’un fichier PO</span><span class="sxs-lookup"><span data-stu-id="2c067-240">Creating a PO file</span></span>

<span data-ttu-id="2c067-241">Créez un fichier nommé *\<culture code> . po* dans le dossier racine de votre application.</span><span class="sxs-lookup"><span data-stu-id="2c067-241">Create a file named *\<culture code>.po* in your application root folder.</span></span> <span data-ttu-id="2c067-242">Dans cet exemple, le nom de fichier est *fr.po* , car le français est utilisé :</span><span class="sxs-lookup"><span data-stu-id="2c067-242">In this example, the file name is *fr.po* because the French language is used:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

<span data-ttu-id="2c067-243">Ce fichier stocke à la fois la chaîne à traduire et la chaîne traduite en français.</span><span class="sxs-lookup"><span data-stu-id="2c067-243">This file stores both the string to translate and the French-translated string.</span></span> <span data-ttu-id="2c067-244">La culture parente des traductions peut être rétablie, si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="2c067-244">Translations revert to their parent culture, if necessary.</span></span> <span data-ttu-id="2c067-245">Dans cet exemple, le fichier *fr.po* est utilisé si la culture demandée est `fr-FR` ou `fr-CA`.</span><span class="sxs-lookup"><span data-stu-id="2c067-245">In this example, the *fr.po* file is used if the requested culture is `fr-FR` or `fr-CA`.</span></span>

### <a name="testing-the-application"></a><span data-ttu-id="2c067-246">Test de l’application</span><span class="sxs-lookup"><span data-stu-id="2c067-246">Testing the application</span></span>

<span data-ttu-id="2c067-247">Exécutez votre application, puis accédez à l’URL `/Home/About`.</span><span class="sxs-lookup"><span data-stu-id="2c067-247">Run your application, and navigate to the URL `/Home/About`.</span></span> <span data-ttu-id="2c067-248">Le texte **Hello world!**</span><span class="sxs-lookup"><span data-stu-id="2c067-248">The text **Hello world!**</span></span> <span data-ttu-id="2c067-249">ne s'affiche.</span><span class="sxs-lookup"><span data-stu-id="2c067-249">is displayed.</span></span>

<span data-ttu-id="2c067-250">Accédez à l’URL `/Home/About?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="2c067-250">Navigate to the URL `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="2c067-251">Le texte **Bonjour le monde !**</span><span class="sxs-lookup"><span data-stu-id="2c067-251">The text **Bonjour le monde!**</span></span> <span data-ttu-id="2c067-252">ne s'affiche.</span><span class="sxs-lookup"><span data-stu-id="2c067-252">is displayed.</span></span>

## <a name="pluralization"></a><span data-ttu-id="2c067-253">Forme plurielle</span><span class="sxs-lookup"><span data-stu-id="2c067-253">Pluralization</span></span>

<span data-ttu-id="2c067-254">Les fichiers PO prennent en charge les formes de pluralisation, ce qui est utile quand la même chaîne doit être traduite différemment en fonction d’une cardinalité.</span><span class="sxs-lookup"><span data-stu-id="2c067-254">PO files support pluralization forms, which is useful when the same string needs to be translated differently based on a cardinality.</span></span> <span data-ttu-id="2c067-255">Cette tâche est rendue compliquée par le fait que chaque langue définit des règles personnalisées pour sélectionner la chaîne à utiliser en fonction de la cardinalité.</span><span class="sxs-lookup"><span data-stu-id="2c067-255">This task is made complicated by the fact that each language defines custom rules to select which string to use based on the cardinality.</span></span>

<span data-ttu-id="2c067-256">Le package de localisation Orchard fournit une API pour appeler automatiquement ces différentes formes plurielles.</span><span class="sxs-lookup"><span data-stu-id="2c067-256">The Orchard Localization package provides an API to invoke these different plural forms automatically.</span></span>

### <a name="creating-pluralization-po-files"></a><span data-ttu-id="2c067-257">Création de fichiers PO de pluralisation</span><span class="sxs-lookup"><span data-stu-id="2c067-257">Creating pluralization PO files</span></span>

<span data-ttu-id="2c067-258">Ajoutez le contenu suivant au fichier *fr.po* mentionné précédemment :</span><span class="sxs-lookup"><span data-stu-id="2c067-258">Add the following content to the previously mentioned *fr.po* file:</span></span>

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

<span data-ttu-id="2c067-259">Consultez [Qu’est-ce qu’un fichier PO ?](#what-is-a-po-file) pour obtenir une explication de ce que représente chaque entrée de cet exemple.</span><span class="sxs-lookup"><span data-stu-id="2c067-259">See [What is a PO file?](#what-is-a-po-file) for an explanation of what each entry in this example represents.</span></span>

### <a name="adding-a-language-using-different-pluralization-forms"></a><span data-ttu-id="2c067-260">Ajout d’une langue utilisant différentes formes de pluralisation</span><span class="sxs-lookup"><span data-stu-id="2c067-260">Adding a language using different pluralization forms</span></span>

<span data-ttu-id="2c067-261">Des chaînes en anglais et en français ont été utilisées dans l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="2c067-261">English and French strings were used in the previous example.</span></span> <span data-ttu-id="2c067-262">L’anglais et le français n’ont que deux formes de pluralisation et partagent les mêmes règles de forme, qui est qu’une cardinalité de 1 est mappée à la première forme plurielle.</span><span class="sxs-lookup"><span data-stu-id="2c067-262">English and French have only two pluralization forms and share the same form rules, which is that a cardinality of one is mapped to the first plural form.</span></span> <span data-ttu-id="2c067-263">N’importe quelle autre cardinalité est mappée à la seconde forme plurielle.</span><span class="sxs-lookup"><span data-stu-id="2c067-263">Any other cardinality is mapped to the second plural form.</span></span>

<span data-ttu-id="2c067-264">Toutes les langues ne partagent pas les mêmes règles.</span><span class="sxs-lookup"><span data-stu-id="2c067-264">Not all languages share the same rules.</span></span> <span data-ttu-id="2c067-265">À titre d’exemple, il y a la langue tchèque qui a trois formes plurielles.</span><span class="sxs-lookup"><span data-stu-id="2c067-265">This is illustrated with the Czech language, which has three plural forms.</span></span>

<span data-ttu-id="2c067-266">Créez le fichier `cs.po` comme suit, puis notez comment la pluralisation a besoin de trois traductions différentes :</span><span class="sxs-lookup"><span data-stu-id="2c067-266">Create the `cs.po` file as follows, and note how the pluralization needs three different translations:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/cs.po)]

<span data-ttu-id="2c067-267">Pour accepter les localisations tchèques, ajoutez `"cs"` à la liste des cultures prises en charge dans la méthode `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2c067-267">To accept Czech localizations, add `"cs"` to the list of supported cultures in the `ConfigureServices` method:</span></span>

```csharp
var supportedCultures = new List<CultureInfo>
{
    new CultureInfo("en-US"),
    new CultureInfo("en"),
    new CultureInfo("fr-FR"),
    new CultureInfo("fr"),
    new CultureInfo("cs")
};
```

<span data-ttu-id="2c067-268">Modifiez le fichier *Views/Home/About.cshtml* pour restituer des chaînes localisées au pluriel pour plusieurs cardinalités :</span><span class="sxs-lookup"><span data-stu-id="2c067-268">Edit the *Views/Home/About.cshtml* file to render localized, plural strings for several cardinalities:</span></span>

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

<span data-ttu-id="2c067-269">**Remarque :** Dans un scénario réel, une variable serait utilisée pour représenter le nombre.</span><span class="sxs-lookup"><span data-stu-id="2c067-269">**Note:** In a real world scenario, a variable would be used to represent the count.</span></span> <span data-ttu-id="2c067-270">Ici, nous répétons le même code avec trois valeurs différentes pour exposer un cas très spécifique.</span><span class="sxs-lookup"><span data-stu-id="2c067-270">Here, we repeat the same code with three different values to expose a very specific case.</span></span>

<span data-ttu-id="2c067-271">Quand vous passez d’une culture à l’autre, vous voyez ceci :</span><span class="sxs-lookup"><span data-stu-id="2c067-271">Upon switching cultures, you see the following:</span></span>

<span data-ttu-id="2c067-272">Pour `/Home/About`:</span><span class="sxs-lookup"><span data-stu-id="2c067-272">For `/Home/About`:</span></span>

```html
There is one item.
There are 2 items.
There are 5 items.
```

<span data-ttu-id="2c067-273">Pour `/Home/About?culture=fr`:</span><span class="sxs-lookup"><span data-stu-id="2c067-273">For `/Home/About?culture=fr`:</span></span>

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

<span data-ttu-id="2c067-274">Pour `/Home/About?culture=cs`:</span><span class="sxs-lookup"><span data-stu-id="2c067-274">For `/Home/About?culture=cs`:</span></span>

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

<span data-ttu-id="2c067-275">Notez que pour la culture tchèque, les trois traductions sont différentes.</span><span class="sxs-lookup"><span data-stu-id="2c067-275">Note that for the Czech culture, the three translations are different.</span></span> <span data-ttu-id="2c067-276">Les cultures anglaise et française partagent la même construction pour les deux dernières chaînes traduites.</span><span class="sxs-lookup"><span data-stu-id="2c067-276">The French and English cultures share the same construction for the two last translated strings.</span></span>

## <a name="advanced-tasks"></a><span data-ttu-id="2c067-277">Tâches avancées</span><span class="sxs-lookup"><span data-stu-id="2c067-277">Advanced tasks</span></span>

### <a name="contextualizing-strings"></a><span data-ttu-id="2c067-278">Contextualisation de chaînes</span><span class="sxs-lookup"><span data-stu-id="2c067-278">Contextualizing strings</span></span>

<span data-ttu-id="2c067-279">Les applications contiennent souvent les chaînes à traduire dans plusieurs emplacements.</span><span class="sxs-lookup"><span data-stu-id="2c067-279">Applications often contain the strings to be translated in several places.</span></span> <span data-ttu-id="2c067-280">La même chaîne peut avoir une traduction différente dans certains emplacements d’une application ( Razor vues ou fichiers de classe).</span><span class="sxs-lookup"><span data-stu-id="2c067-280">The same string may have a different translation in certain locations within an app (Razor views or class files).</span></span> <span data-ttu-id="2c067-281">Un fichier PO prend en charge la notion d’un contexte de fichier, qui peut être utilisé pour catégoriser la chaîne représentée.</span><span class="sxs-lookup"><span data-stu-id="2c067-281">A PO file supports the notion of a file context, which can be used to categorize the string being represented.</span></span> <span data-ttu-id="2c067-282">L’utilisation d’un contexte de fichier permet de traduire une chaîne différemment en fonction du contexte du fichier (ou de l’absence de contexte de fichier).</span><span class="sxs-lookup"><span data-stu-id="2c067-282">Using a file context, a string can be translated differently, depending on the file context (or lack of a file context).</span></span>

<span data-ttu-id="2c067-283">Les services de localisation de PO utilisent le nom de la classe complète ou de la vue utilisée lors de la traduction d’une chaîne.</span><span class="sxs-lookup"><span data-stu-id="2c067-283">The PO localization services use the name of the full class or the view that's used when translating a string.</span></span> <span data-ttu-id="2c067-284">Pour ce faire, il vous suffit de définir la valeur sur l’entrée `msgctxt`.</span><span class="sxs-lookup"><span data-stu-id="2c067-284">This is accomplished by setting the value on the `msgctxt` entry.</span></span>

<span data-ttu-id="2c067-285">Examinons un ajout mineur à l’exemple *fr.po* précédent.</span><span class="sxs-lookup"><span data-stu-id="2c067-285">Consider a minor addition to the previous *fr.po* example.</span></span> <span data-ttu-id="2c067-286">Vous Razor pouvez définir une vue située dans *views/orig/about. cshtml* comme contexte de fichier en définissant la `msgctxt` valeur de l’entrée réservée :</span><span class="sxs-lookup"><span data-stu-id="2c067-286">A Razor view located at *Views/Home/About.cshtml* can be defined as the file context by setting the reserved `msgctxt` entry's value:</span></span>

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

<span data-ttu-id="2c067-287">Avec `msgctxt` ainsi définie, la traduction de texte se produit lors de la navigation vers `/Home/About?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="2c067-287">With the `msgctxt` set as such, text translation occurs when navigating to `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="2c067-288">La traduction ne se produit pas lors de la navigation vers `/Home/Contact?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="2c067-288">The translation won't occur when navigating to `/Home/Contact?culture=fr-FR`.</span></span>

<span data-ttu-id="2c067-289">Quand aucune entrée spécifique ne correspond à un contexte de fichier donné, le mécanisme de secours d’Orchard Core recherche un fichier PO approprié sans contexte.</span><span class="sxs-lookup"><span data-stu-id="2c067-289">When no specific entry is matched with a given file context, Orchard Core's fallback mechanism looks for an appropriate PO file without a context.</span></span> <span data-ttu-id="2c067-290">En supposant qu’aucun contexte de fichier spécifique n’est défini pour *Views/Home/Contact.cshtml* , la navigation vers `/Home/Contact?culture=fr-FR` charge un fichier PO tel que le suivant :</span><span class="sxs-lookup"><span data-stu-id="2c067-290">Assuming there's no specific file context defined for *Views/Home/Contact.cshtml* , navigating to `/Home/Contact?culture=fr-FR` loads a PO file such as:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a><span data-ttu-id="2c067-291">Changement de l’emplacement des fichiers PO</span><span class="sxs-lookup"><span data-stu-id="2c067-291">Changing the location of PO files</span></span>

<span data-ttu-id="2c067-292">L’emplacement par défaut des fichiers PO peut être changé dans `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2c067-292">The default location of PO files can be changed in `ConfigureServices`:</span></span>

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

<span data-ttu-id="2c067-293">Dans cet exemple, les fichiers PO sont chargés à partir du dossier *Localisation* .</span><span class="sxs-lookup"><span data-stu-id="2c067-293">In this example, the PO files are loaded from the *Localization* folder.</span></span>

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a><span data-ttu-id="2c067-294">Implémentation d’une logique personnalisée pour la recherche de fichiers de localisation</span><span class="sxs-lookup"><span data-stu-id="2c067-294">Implementing a custom logic for finding localization files</span></span>

<span data-ttu-id="2c067-295">Quand une logique plus complexe est nécessaire pour localiser des fichiers PO, l’interface `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` peut être implémentée et inscrite comme service.</span><span class="sxs-lookup"><span data-stu-id="2c067-295">When more complex logic is needed to locate PO files, the `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` interface can be implemented and registered as a service.</span></span> <span data-ttu-id="2c067-296">Cela est utile quand les fichiers PO peuvent être stockés dans différents emplacements ou quand la recherche des fichiers doit être effectuée dans une hiérarchie de dossiers.</span><span class="sxs-lookup"><span data-stu-id="2c067-296">This is useful when PO files can be stored in varying locations or when the files have to be found within a hierarchy of folders.</span></span>

### <a name="using-a-different-default-pluralized-language"></a><span data-ttu-id="2c067-297">Utilisation d’une autre langue pluralisée par défaut</span><span class="sxs-lookup"><span data-stu-id="2c067-297">Using a different default pluralized language</span></span>

<span data-ttu-id="2c067-298">Le package inclut une méthode d’extension `Plural` spécifique à deux formes plurielles.</span><span class="sxs-lookup"><span data-stu-id="2c067-298">The package includes a `Plural` extension method that's specific to two plural forms.</span></span> <span data-ttu-id="2c067-299">Pour les langues nécessitant plus de formes plurielles, créez une méthode d’extension.</span><span class="sxs-lookup"><span data-stu-id="2c067-299">For languages requiring more plural forms, create an extension method.</span></span> <span data-ttu-id="2c067-300">Avec une méthode d’extension, vous n’avez pas à fournir de fichier de localisation pour la langue par défaut : en effet, les chaînes d’origine sont déjà disponibles directement dans le code.</span><span class="sxs-lookup"><span data-stu-id="2c067-300">With an extension method, you won't need to provide any localization file for the default language &mdash; the original strings are already available directly in the code.</span></span>

<span data-ttu-id="2c067-301">Vous pouvez utiliser la surcharge `Plural(int count, string[] pluralForms, params object[] arguments)` plus générique qui accepte un tableau de chaînes des traductions.</span><span class="sxs-lookup"><span data-stu-id="2c067-301">You can use the more generic `Plural(int count, string[] pluralForms, params object[] arguments)` overload which accepts a string array of translations.</span></span>

::: moniker-end