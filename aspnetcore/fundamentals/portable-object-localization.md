---
title: Configurer la localisation d’objets portables dans ASP.NET Core
author: sebastienros
description: Cet article présente les fichiers d’objets portables et décrit les étapes à suivre pour les utiliser dans une application ASP.NET Core avec le framework Orchard Core.
ms.author: scaddie
ms.date: 09/26/2017
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
uid: fundamentals/portable-object-localization
ms.openlocfilehash: ed41657b364c7f845491b3e452db8a4d5c5aa389
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587175"
---
# <a name="configure-portable-object-localization-in-aspnet-core"></a><span data-ttu-id="9a145-103">Configurer la localisation d’objets portables dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9a145-103">Configure portable object localization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9a145-104">Par [Sébastien Ros](https://github.com/sebastienros), [Scott Addie](https://twitter.com/Scott_Addie) et [Hisham bin Ateya](https://github.com/hishamco)</span><span class="sxs-lookup"><span data-stu-id="9a145-104">By [Sébastien Ros](https://github.com/sebastienros), [Scott Addie](https://twitter.com/Scott_Addie) and [Hisham Bin Ateya](https://github.com/hishamco)</span></span>

<span data-ttu-id="9a145-105">Cet article présente les étapes d’utilisation d’objets portables (PO, Portable Object) dans une application ASP.NET Core avec le framework [Orchard Core](https://github.com/OrchardCMS/OrchardCore).</span><span class="sxs-lookup"><span data-stu-id="9a145-105">This article walks through the steps for using Portable Object (PO) files in an ASP.NET Core application with the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) framework.</span></span>

<span data-ttu-id="9a145-106">**Remarque :** Orchard Core n’est pas un produit Microsoft.</span><span class="sxs-lookup"><span data-stu-id="9a145-106">**Note:** Orchard Core isn't a Microsoft product.</span></span> <span data-ttu-id="9a145-107">Par conséquent, Microsoft ne fournit aucun support pour cette fonctionnalité.</span><span class="sxs-lookup"><span data-stu-id="9a145-107">Consequently, Microsoft provides no support for this feature.</span></span>

<span data-ttu-id="9a145-108">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/localization/sample/3.x/POLocalization) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9a145-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/localization/sample/3.x/POLocalization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="what-is-a-po-file"></a><span data-ttu-id="9a145-109">Qu’est-ce qu’un fichier PO ?</span><span class="sxs-lookup"><span data-stu-id="9a145-109">What is a PO file?</span></span>

<span data-ttu-id="9a145-110">Les fichiers PO sont distribués sous forme de fichiers texte contenant les chaînes traduites pour une langue donnée.</span><span class="sxs-lookup"><span data-stu-id="9a145-110">PO files are distributed as text files containing the translated strings for a given language.</span></span> <span data-ttu-id="9a145-111">L’utilisation de fichiers PO au lieu de fichiers *. resx* présente les avantages suivants :</span><span class="sxs-lookup"><span data-stu-id="9a145-111">Some advantages of using PO files instead of *.resx* files include:</span></span>
- <span data-ttu-id="9a145-112">Les fichiers PO prennent en charge la pluralisation, contrairement aux fichiers *.resx*.</span><span class="sxs-lookup"><span data-stu-id="9a145-112">PO files support pluralization; *.resx* files don't support pluralization.</span></span>
- <span data-ttu-id="9a145-113">Les fichiers PO ne sont pas compilés comme les fichiers *.resx*.</span><span class="sxs-lookup"><span data-stu-id="9a145-113">PO files aren't compiled like *.resx* files.</span></span> <span data-ttu-id="9a145-114">De ce fait, il n’est pas nécessaire d’effectuer les étapes relatives aux outils et à la génération.</span><span class="sxs-lookup"><span data-stu-id="9a145-114">As such, specialized tooling and build steps aren't required.</span></span>
- <span data-ttu-id="9a145-115">Les fichiers PO fonctionnent correctement avec les outils d’édition collaboratifs en ligne.</span><span class="sxs-lookup"><span data-stu-id="9a145-115">PO files work well with collaborative online editing tools.</span></span>

### <a name="example"></a><span data-ttu-id="9a145-116">Exemple</span><span class="sxs-lookup"><span data-stu-id="9a145-116">Example</span></span>

<span data-ttu-id="9a145-117">Voici un exemple de fichier PO contenant la traduction de deux chaînes en français, dont une avec sa forme au pluriel :</span><span class="sxs-lookup"><span data-stu-id="9a145-117">Here is a sample PO file containing the translation for two strings in French, including one with its plural form:</span></span>

<span data-ttu-id="9a145-118">*fr.po*</span><span class="sxs-lookup"><span data-stu-id="9a145-118">*fr.po*</span></span>

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

<span data-ttu-id="9a145-119">Cet exemple utilise la syntaxe suivante :</span><span class="sxs-lookup"><span data-stu-id="9a145-119">This example uses the following syntax:</span></span>

- <span data-ttu-id="9a145-120">`#:` : commentaire indiquant le contexte de la chaîne à traduire.</span><span class="sxs-lookup"><span data-stu-id="9a145-120">`#:`: A comment indicating the context of the string to be translated.</span></span> <span data-ttu-id="9a145-121">La même chaîne peut être traduite différemment selon l’endroit où elle est utilisée.</span><span class="sxs-lookup"><span data-stu-id="9a145-121">The same string might be translated differently depending on where it's being used.</span></span>
- <span data-ttu-id="9a145-122">`msgid` : la chaîne non traduite.</span><span class="sxs-lookup"><span data-stu-id="9a145-122">`msgid`: The untranslated string.</span></span>
- <span data-ttu-id="9a145-123">`msgstr` : la chaîne traduite.</span><span class="sxs-lookup"><span data-stu-id="9a145-123">`msgstr`: The translated string.</span></span>

<span data-ttu-id="9a145-124">Dans le cas de la prise en charge de la pluralisation, des entrées supplémentaires peuvent être définies.</span><span class="sxs-lookup"><span data-stu-id="9a145-124">In the case of pluralization support, more entries can be defined.</span></span>

- <span data-ttu-id="9a145-125">`msgid_plural` : la chaîne au pluriel non traduite.</span><span class="sxs-lookup"><span data-stu-id="9a145-125">`msgid_plural`: The untranslated plural string.</span></span>
- <span data-ttu-id="9a145-126">`msgstr[0]` : la chaîne traduite pour le cas 0.</span><span class="sxs-lookup"><span data-stu-id="9a145-126">`msgstr[0]`: The translated string for the case 0.</span></span>
- <span data-ttu-id="9a145-127">`msgstr[N]` : la chaîne traduite pour le cas N.</span><span class="sxs-lookup"><span data-stu-id="9a145-127">`msgstr[N]`: The translated string for the case N.</span></span>

<span data-ttu-id="9a145-128">La spécification du fichier PO se trouve [ici](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span><span class="sxs-lookup"><span data-stu-id="9a145-128">The PO file specification can be found [here](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span></span>

## <a name="configuring-po-file-support-in-aspnet-core"></a><span data-ttu-id="9a145-129">Configuration de la prise en charge du fichier PO dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9a145-129">Configuring PO file support in ASP.NET Core</span></span>

<span data-ttu-id="9a145-130">Cet exemple est basé sur une application ASP.NET Core MVC générée à partir d’un modèle de projet Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="9a145-130">This example is based on an ASP.NET Core MVC application generated from a Visual Studio 2017 project template.</span></span>

### <a name="referencing-the-package"></a><span data-ttu-id="9a145-131">Référencement du package</span><span class="sxs-lookup"><span data-stu-id="9a145-131">Referencing the package</span></span>

<span data-ttu-id="9a145-132">Ajoutez une référence au package NuGet `OrchardCore.Localization.Core`.</span><span class="sxs-lookup"><span data-stu-id="9a145-132">Add a reference to the `OrchardCore.Localization.Core` NuGet package.</span></span>

<span data-ttu-id="9a145-133">Le fichier *.csproj* contient maintenant une ligne semblable à la suivante (le numéro de version peut varier) :</span><span class="sxs-lookup"><span data-stu-id="9a145-133">The *.csproj* file now contains a line similar to the following (version number may vary):</span></span>

[!code-xml[](localization/sample/3.x/POLocalization/POLocalization.csproj?range=8)]

### <a name="registering-the-service"></a><span data-ttu-id="9a145-134">Inscription du service</span><span class="sxs-lookup"><span data-stu-id="9a145-134">Registering the service</span></span>

<span data-ttu-id="9a145-135">Ajoutez les services nécessaires à la méthode `ConfigureServices` de *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="9a145-135">Add the required services to the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/3.x/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

<span data-ttu-id="9a145-136">Ajoutez l’intergiciel (middleware) nécessaire à la méthode `Configure` de *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="9a145-136">Add the required middleware to the `Configure` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/3.x/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="9a145-137">Ajoutez le code suivant à votre Razor vue de votre choix.</span><span class="sxs-lookup"><span data-stu-id="9a145-137">Add the following code to your Razor view of choice.</span></span> <span data-ttu-id="9a145-138">*About.cshtml* est utilisé dans cet exemple.</span><span class="sxs-lookup"><span data-stu-id="9a145-138">*About.cshtml* is used in this example.</span></span>

[!code-cshtml[](localization/sample/3.x/POLocalization/Views/Home/About.cshtml)]

<span data-ttu-id="9a145-139">Une instance `IViewLocalizer` est injectée et utilisée pour traduire le texte « Hello world! ».</span><span class="sxs-lookup"><span data-stu-id="9a145-139">An `IViewLocalizer` instance is injected and used to translate the text "Hello world!".</span></span>

### <a name="creating-a-po-file"></a><span data-ttu-id="9a145-140">Création d’un fichier PO</span><span class="sxs-lookup"><span data-stu-id="9a145-140">Creating a PO file</span></span>

<span data-ttu-id="9a145-141">Créez un fichier nommé *\<culture code> . po* dans le dossier racine de votre application.</span><span class="sxs-lookup"><span data-stu-id="9a145-141">Create a file named *\<culture code>.po* in your application root folder.</span></span> <span data-ttu-id="9a145-142">Dans cet exemple, le nom de fichier est *fr.po*, car le français est utilisé :</span><span class="sxs-lookup"><span data-stu-id="9a145-142">In this example, the file name is *fr.po* because the French language is used:</span></span>

[!code-text[](localization/sample/3.x/POLocalization/fr.po)]

<span data-ttu-id="9a145-143">Ce fichier stocke à la fois la chaîne à traduire et la chaîne traduite en français.</span><span class="sxs-lookup"><span data-stu-id="9a145-143">This file stores both the string to translate and the French-translated string.</span></span> <span data-ttu-id="9a145-144">La culture parente des traductions peut être rétablie, si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="9a145-144">Translations revert to their parent culture, if necessary.</span></span> <span data-ttu-id="9a145-145">Dans cet exemple, le fichier *fr.po* est utilisé si la culture demandée est `fr-FR` ou `fr-CA`.</span><span class="sxs-lookup"><span data-stu-id="9a145-145">In this example, the *fr.po* file is used if the requested culture is `fr-FR` or `fr-CA`.</span></span>

### <a name="testing-the-application"></a><span data-ttu-id="9a145-146">Test de l’application</span><span class="sxs-lookup"><span data-stu-id="9a145-146">Testing the application</span></span>

<span data-ttu-id="9a145-147">Exécutez votre application, puis accédez à l’URL `/Home/About`.</span><span class="sxs-lookup"><span data-stu-id="9a145-147">Run your application, and navigate to the URL `/Home/About`.</span></span> <span data-ttu-id="9a145-148">Le texte **Hello world!**</span><span class="sxs-lookup"><span data-stu-id="9a145-148">The text **Hello world!**</span></span> <span data-ttu-id="9a145-149">ne s'affiche.</span><span class="sxs-lookup"><span data-stu-id="9a145-149">is displayed.</span></span>

<span data-ttu-id="9a145-150">Accédez à l’URL `/Home/About?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="9a145-150">Navigate to the URL `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="9a145-151">Le texte **Bonjour le monde !**</span><span class="sxs-lookup"><span data-stu-id="9a145-151">The text **Bonjour le monde!**</span></span> <span data-ttu-id="9a145-152">ne s'affiche.</span><span class="sxs-lookup"><span data-stu-id="9a145-152">is displayed.</span></span>

## <a name="pluralization"></a><span data-ttu-id="9a145-153">Forme plurielle</span><span class="sxs-lookup"><span data-stu-id="9a145-153">Pluralization</span></span>

<span data-ttu-id="9a145-154">Les fichiers PO prennent en charge les formes de pluralisation, ce qui est utile quand la même chaîne doit être traduite différemment en fonction d’une cardinalité.</span><span class="sxs-lookup"><span data-stu-id="9a145-154">PO files support pluralization forms, which is useful when the same string needs to be translated differently based on a cardinality.</span></span> <span data-ttu-id="9a145-155">Cette tâche est rendue compliquée par le fait que chaque langue définit des règles personnalisées pour sélectionner la chaîne à utiliser en fonction de la cardinalité.</span><span class="sxs-lookup"><span data-stu-id="9a145-155">This task is made complicated by the fact that each language defines custom rules to select which string to use based on the cardinality.</span></span>

<span data-ttu-id="9a145-156">Le package de localisation Orchard fournit une API pour appeler automatiquement ces différentes formes plurielles.</span><span class="sxs-lookup"><span data-stu-id="9a145-156">The Orchard Localization package provides an API to invoke these different plural forms automatically.</span></span>

### <a name="creating-pluralization-po-files"></a><span data-ttu-id="9a145-157">Création de fichiers PO de pluralisation</span><span class="sxs-lookup"><span data-stu-id="9a145-157">Creating pluralization PO files</span></span>

<span data-ttu-id="9a145-158">Ajoutez le contenu suivant au fichier *fr.po* mentionné précédemment :</span><span class="sxs-lookup"><span data-stu-id="9a145-158">Add the following content to the previously mentioned *fr.po* file:</span></span>

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

<span data-ttu-id="9a145-159">Consultez [Qu’est-ce qu’un fichier PO ?](#what-is-a-po-file) pour obtenir une explication de ce que représente chaque entrée de cet exemple.</span><span class="sxs-lookup"><span data-stu-id="9a145-159">See [What is a PO file?](#what-is-a-po-file) for an explanation of what each entry in this example represents.</span></span>

### <a name="adding-a-language-using-different-pluralization-forms"></a><span data-ttu-id="9a145-160">Ajout d’une langue utilisant différentes formes de pluralisation</span><span class="sxs-lookup"><span data-stu-id="9a145-160">Adding a language using different pluralization forms</span></span>

<span data-ttu-id="9a145-161">Des chaînes en anglais et en français ont été utilisées dans l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="9a145-161">English and French strings were used in the previous example.</span></span> <span data-ttu-id="9a145-162">L’anglais et le français n’ont que deux formes de pluralisation et partagent les mêmes règles de forme, qui est qu’une cardinalité de 1 est mappée à la première forme plurielle.</span><span class="sxs-lookup"><span data-stu-id="9a145-162">English and French have only two pluralization forms and share the same form rules, which is that a cardinality of one is mapped to the first plural form.</span></span> <span data-ttu-id="9a145-163">N’importe quelle autre cardinalité est mappée à la seconde forme plurielle.</span><span class="sxs-lookup"><span data-stu-id="9a145-163">Any other cardinality is mapped to the second plural form.</span></span>

<span data-ttu-id="9a145-164">Toutes les langues ne partagent pas les mêmes règles.</span><span class="sxs-lookup"><span data-stu-id="9a145-164">Not all languages share the same rules.</span></span> <span data-ttu-id="9a145-165">À titre d’exemple, il y a la langue tchèque qui a trois formes plurielles.</span><span class="sxs-lookup"><span data-stu-id="9a145-165">This is illustrated with the Czech language, which has three plural forms.</span></span>

<span data-ttu-id="9a145-166">Créez le fichier `cs.po` comme suit, puis notez comment la pluralisation a besoin de trois traductions différentes :</span><span class="sxs-lookup"><span data-stu-id="9a145-166">Create the `cs.po` file as follows, and note how the pluralization needs three different translations:</span></span>

[!code-text[](localization/sample/3.x/POLocalization/cs.po)]

<span data-ttu-id="9a145-167">Pour accepter les localisations tchèques, ajoutez `"cs"` à la liste des cultures prises en charge dans la méthode `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9a145-167">To accept Czech localizations, add `"cs"` to the list of supported cultures in the `ConfigureServices` method:</span></span>

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

<span data-ttu-id="9a145-168">Modifiez le fichier *Views/Home/About.cshtml* pour restituer des chaînes localisées au pluriel pour plusieurs cardinalités :</span><span class="sxs-lookup"><span data-stu-id="9a145-168">Edit the *Views/Home/About.cshtml* file to render localized, plural strings for several cardinalities:</span></span>

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

<span data-ttu-id="9a145-169">**Remarque :** Dans un scénario réel, une variable serait utilisée pour représenter le nombre.</span><span class="sxs-lookup"><span data-stu-id="9a145-169">**Note:** In a real world scenario, a variable would be used to represent the count.</span></span> <span data-ttu-id="9a145-170">Ici, nous répétons le même code avec trois valeurs différentes pour exposer un cas très spécifique.</span><span class="sxs-lookup"><span data-stu-id="9a145-170">Here, we repeat the same code with three different values to expose a very specific case.</span></span>

<span data-ttu-id="9a145-171">Quand vous passez d’une culture à l’autre, vous voyez ceci :</span><span class="sxs-lookup"><span data-stu-id="9a145-171">Upon switching cultures, you see the following:</span></span>

<span data-ttu-id="9a145-172">Pour `/Home/About` :</span><span class="sxs-lookup"><span data-stu-id="9a145-172">For `/Home/About`:</span></span>

```html
There is one item.
There are 2 items.
There are 5 items.
```

<span data-ttu-id="9a145-173">Pour `/Home/About?culture=fr` :</span><span class="sxs-lookup"><span data-stu-id="9a145-173">For `/Home/About?culture=fr`:</span></span>

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

<span data-ttu-id="9a145-174">Pour `/Home/About?culture=cs` :</span><span class="sxs-lookup"><span data-stu-id="9a145-174">For `/Home/About?culture=cs`:</span></span>

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

<span data-ttu-id="9a145-175">Notez que pour la culture tchèque, les trois traductions sont différentes.</span><span class="sxs-lookup"><span data-stu-id="9a145-175">Note that for the Czech culture, the three translations are different.</span></span> <span data-ttu-id="9a145-176">Les cultures anglaise et française partagent la même construction pour les deux dernières chaînes traduites.</span><span class="sxs-lookup"><span data-stu-id="9a145-176">The French and English cultures share the same construction for the two last translated strings.</span></span>

## <a name="advanced-tasks"></a><span data-ttu-id="9a145-177">Tâches avancées</span><span class="sxs-lookup"><span data-stu-id="9a145-177">Advanced tasks</span></span>

### <a name="contextualizing-strings"></a><span data-ttu-id="9a145-178">Contextualisation de chaînes</span><span class="sxs-lookup"><span data-stu-id="9a145-178">Contextualizing strings</span></span>

<span data-ttu-id="9a145-179">Les applications contiennent souvent les chaînes à traduire dans plusieurs emplacements.</span><span class="sxs-lookup"><span data-stu-id="9a145-179">Applications often contain the strings to be translated in several places.</span></span> <span data-ttu-id="9a145-180">La même chaîne peut avoir une traduction différente dans certains emplacements d’une application ( Razor vues ou fichiers de classe).</span><span class="sxs-lookup"><span data-stu-id="9a145-180">The same string may have a different translation in certain locations within an app (Razor views or class files).</span></span> <span data-ttu-id="9a145-181">Un fichier PO prend en charge la notion d’un contexte de fichier, qui peut être utilisé pour catégoriser la chaîne représentée.</span><span class="sxs-lookup"><span data-stu-id="9a145-181">A PO file supports the notion of a file context, which can be used to categorize the string being represented.</span></span> <span data-ttu-id="9a145-182">L’utilisation d’un contexte de fichier permet de traduire une chaîne différemment en fonction du contexte du fichier (ou de l’absence de contexte de fichier).</span><span class="sxs-lookup"><span data-stu-id="9a145-182">Using a file context, a string can be translated differently, depending on the file context (or lack of a file context).</span></span>

<span data-ttu-id="9a145-183">Les services de localisation de PO utilisent le nom de la classe complète ou de la vue utilisée lors de la traduction d’une chaîne.</span><span class="sxs-lookup"><span data-stu-id="9a145-183">The PO localization services use the name of the full class or the view that's used when translating a string.</span></span> <span data-ttu-id="9a145-184">Pour ce faire, il vous suffit de définir la valeur sur l’entrée `msgctxt`.</span><span class="sxs-lookup"><span data-stu-id="9a145-184">This is accomplished by setting the value on the `msgctxt` entry.</span></span>

<span data-ttu-id="9a145-185">Examinons un ajout mineur à l’exemple *fr.po* précédent.</span><span class="sxs-lookup"><span data-stu-id="9a145-185">Consider a minor addition to the previous *fr.po* example.</span></span> <span data-ttu-id="9a145-186">Vous Razor pouvez définir une vue située dans *views/orig/about. cshtml* comme contexte de fichier en définissant la `msgctxt` valeur de l’entrée réservée :</span><span class="sxs-lookup"><span data-stu-id="9a145-186">A Razor view located at *Views/Home/About.cshtml* can be defined as the file context by setting the reserved `msgctxt` entry's value:</span></span>

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

<span data-ttu-id="9a145-187">Avec `msgctxt` ainsi définie, la traduction de texte se produit lors de la navigation vers `/Home/About?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="9a145-187">With the `msgctxt` set as such, text translation occurs when navigating to `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="9a145-188">La traduction ne se produit pas lors de la navigation vers `/Home/Contact?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="9a145-188">The translation won't occur when navigating to `/Home/Contact?culture=fr-FR`.</span></span>

<span data-ttu-id="9a145-189">Quand aucune entrée spécifique ne correspond à un contexte de fichier donné, le mécanisme de secours d’Orchard Core recherche un fichier PO approprié sans contexte.</span><span class="sxs-lookup"><span data-stu-id="9a145-189">When no specific entry is matched with a given file context, Orchard Core's fallback mechanism looks for an appropriate PO file without a context.</span></span> <span data-ttu-id="9a145-190">En supposant qu’aucun contexte de fichier spécifique n’est défini pour *Views/Home/Contact.cshtml*, la navigation vers `/Home/Contact?culture=fr-FR` charge un fichier PO tel que le suivant :</span><span class="sxs-lookup"><span data-stu-id="9a145-190">Assuming there's no specific file context defined for *Views/Home/Contact.cshtml*, navigating to `/Home/Contact?culture=fr-FR` loads a PO file such as:</span></span>

[!code-text[](localization/sample/3.x/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a><span data-ttu-id="9a145-191">Changement de l’emplacement des fichiers PO</span><span class="sxs-lookup"><span data-stu-id="9a145-191">Changing the location of PO files</span></span>

<span data-ttu-id="9a145-192">L’emplacement par défaut des fichiers PO peut être changé dans `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9a145-192">The default location of PO files can be changed in `ConfigureServices`:</span></span>

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

<span data-ttu-id="9a145-193">Dans cet exemple, les fichiers PO sont chargés à partir du dossier *Localisation*.</span><span class="sxs-lookup"><span data-stu-id="9a145-193">In this example, the PO files are loaded from the *Localization* folder.</span></span>

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a><span data-ttu-id="9a145-194">Implémentation d’une logique personnalisée pour la recherche de fichiers de localisation</span><span class="sxs-lookup"><span data-stu-id="9a145-194">Implementing a custom logic for finding localization files</span></span>

<span data-ttu-id="9a145-195">Quand une logique plus complexe est nécessaire pour localiser des fichiers PO, l’interface `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` peut être implémentée et inscrite comme service.</span><span class="sxs-lookup"><span data-stu-id="9a145-195">When more complex logic is needed to locate PO files, the `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` interface can be implemented and registered as a service.</span></span> <span data-ttu-id="9a145-196">Cela est utile quand les fichiers PO peuvent être stockés dans différents emplacements ou quand la recherche des fichiers doit être effectuée dans une hiérarchie de dossiers.</span><span class="sxs-lookup"><span data-stu-id="9a145-196">This is useful when PO files can be stored in varying locations or when the files have to be found within a hierarchy of folders.</span></span>

### <a name="using-a-different-default-pluralized-language"></a><span data-ttu-id="9a145-197">Utilisation d’une autre langue pluralisée par défaut</span><span class="sxs-lookup"><span data-stu-id="9a145-197">Using a different default pluralized language</span></span>

<span data-ttu-id="9a145-198">Le package inclut une méthode d’extension `Plural` spécifique à deux formes plurielles.</span><span class="sxs-lookup"><span data-stu-id="9a145-198">The package includes a `Plural` extension method that's specific to two plural forms.</span></span> <span data-ttu-id="9a145-199">Pour les langues nécessitant plus de formes plurielles, créez une méthode d’extension.</span><span class="sxs-lookup"><span data-stu-id="9a145-199">For languages requiring more plural forms, create an extension method.</span></span> <span data-ttu-id="9a145-200">Avec une méthode d’extension, vous n’avez pas à fournir de fichier de localisation pour la langue par défaut : en effet, les chaînes d’origine sont déjà disponibles directement dans le code.</span><span class="sxs-lookup"><span data-stu-id="9a145-200">With an extension method, you won't need to provide any localization file for the default language &mdash; the original strings are already available directly in the code.</span></span>

<span data-ttu-id="9a145-201">Vous pouvez utiliser la surcharge `Plural(int count, string[] pluralForms, params object[] arguments)` plus générique qui accepte un tableau de chaînes des traductions.</span><span class="sxs-lookup"><span data-stu-id="9a145-201">You can use the more generic `Plural(int count, string[] pluralForms, params object[] arguments)` overload which accepts a string array of translations.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9a145-202">Par [Sébastien Ros](https://github.com/sebastienros) et [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="9a145-202">By [Sébastien Ros](https://github.com/sebastienros) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="9a145-203">Cet article présente les étapes d’utilisation d’objets portables (PO, Portable Object) dans une application ASP.NET Core avec le framework [Orchard Core](https://github.com/OrchardCMS/OrchardCore).</span><span class="sxs-lookup"><span data-stu-id="9a145-203">This article walks through the steps for using Portable Object (PO) files in an ASP.NET Core application with the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) framework.</span></span>

<span data-ttu-id="9a145-204">**Remarque :** Orchard Core n’est pas un produit Microsoft.</span><span class="sxs-lookup"><span data-stu-id="9a145-204">**Note:** Orchard Core isn't a Microsoft product.</span></span> <span data-ttu-id="9a145-205">Par conséquent, Microsoft ne fournit aucun support pour cette fonctionnalité.</span><span class="sxs-lookup"><span data-stu-id="9a145-205">Consequently, Microsoft provides no support for this feature.</span></span>

<span data-ttu-id="9a145-206">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/localization/sample/2.x/POLocalization) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9a145-206">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/localization/sample/2.x/POLocalization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="what-is-a-po-file"></a><span data-ttu-id="9a145-207">Qu’est-ce qu’un fichier PO ?</span><span class="sxs-lookup"><span data-stu-id="9a145-207">What is a PO file?</span></span>

<span data-ttu-id="9a145-208">Les fichiers PO sont distribués sous forme de fichiers texte contenant les chaînes traduites pour une langue donnée.</span><span class="sxs-lookup"><span data-stu-id="9a145-208">PO files are distributed as text files containing the translated strings for a given language.</span></span> <span data-ttu-id="9a145-209">L’utilisation de fichiers PO plutôt que des fichiers *.resx* présente certains avantages, notamment les suivants :</span><span class="sxs-lookup"><span data-stu-id="9a145-209">Some advantages of using PO files instead *.resx* files include:</span></span>
- <span data-ttu-id="9a145-210">Les fichiers PO prennent en charge la pluralisation, contrairement aux fichiers *.resx*.</span><span class="sxs-lookup"><span data-stu-id="9a145-210">PO files support pluralization; *.resx* files don't support pluralization.</span></span>
- <span data-ttu-id="9a145-211">Les fichiers PO ne sont pas compilés comme les fichiers *.resx*.</span><span class="sxs-lookup"><span data-stu-id="9a145-211">PO files aren't compiled like *.resx* files.</span></span> <span data-ttu-id="9a145-212">De ce fait, il n’est pas nécessaire d’effectuer les étapes relatives aux outils et à la génération.</span><span class="sxs-lookup"><span data-stu-id="9a145-212">As such, specialized tooling and build steps aren't required.</span></span>
- <span data-ttu-id="9a145-213">Les fichiers PO fonctionnent correctement avec les outils d’édition collaboratifs en ligne.</span><span class="sxs-lookup"><span data-stu-id="9a145-213">PO files work well with collaborative online editing tools.</span></span>

### <a name="example"></a><span data-ttu-id="9a145-214">Exemple</span><span class="sxs-lookup"><span data-stu-id="9a145-214">Example</span></span>

<span data-ttu-id="9a145-215">Voici un exemple de fichier PO contenant la traduction de deux chaînes en français, dont une avec sa forme au pluriel :</span><span class="sxs-lookup"><span data-stu-id="9a145-215">Here is a sample PO file containing the translation for two strings in French, including one with its plural form:</span></span>

<span data-ttu-id="9a145-216">*fr.po*</span><span class="sxs-lookup"><span data-stu-id="9a145-216">*fr.po*</span></span>

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

<span data-ttu-id="9a145-217">Cet exemple utilise la syntaxe suivante :</span><span class="sxs-lookup"><span data-stu-id="9a145-217">This example uses the following syntax:</span></span>

- <span data-ttu-id="9a145-218">`#:` : commentaire indiquant le contexte de la chaîne à traduire.</span><span class="sxs-lookup"><span data-stu-id="9a145-218">`#:`: A comment indicating the context of the string to be translated.</span></span> <span data-ttu-id="9a145-219">La même chaîne peut être traduite différemment selon l’endroit où elle est utilisée.</span><span class="sxs-lookup"><span data-stu-id="9a145-219">The same string might be translated differently depending on where it's being used.</span></span>
- <span data-ttu-id="9a145-220">`msgid` : la chaîne non traduite.</span><span class="sxs-lookup"><span data-stu-id="9a145-220">`msgid`: The untranslated string.</span></span>
- <span data-ttu-id="9a145-221">`msgstr` : la chaîne traduite.</span><span class="sxs-lookup"><span data-stu-id="9a145-221">`msgstr`: The translated string.</span></span>

<span data-ttu-id="9a145-222">Dans le cas de la prise en charge de la pluralisation, des entrées supplémentaires peuvent être définies.</span><span class="sxs-lookup"><span data-stu-id="9a145-222">In the case of pluralization support, more entries can be defined.</span></span>

- <span data-ttu-id="9a145-223">`msgid_plural` : la chaîne au pluriel non traduite.</span><span class="sxs-lookup"><span data-stu-id="9a145-223">`msgid_plural`: The untranslated plural string.</span></span>
- <span data-ttu-id="9a145-224">`msgstr[0]` : la chaîne traduite pour le cas 0.</span><span class="sxs-lookup"><span data-stu-id="9a145-224">`msgstr[0]`: The translated string for the case 0.</span></span>
- <span data-ttu-id="9a145-225">`msgstr[N]` : la chaîne traduite pour le cas N.</span><span class="sxs-lookup"><span data-stu-id="9a145-225">`msgstr[N]`: The translated string for the case N.</span></span>

<span data-ttu-id="9a145-226">La spécification du fichier PO se trouve [ici](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span><span class="sxs-lookup"><span data-stu-id="9a145-226">The PO file specification can be found [here](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span></span>

## <a name="configuring-po-file-support-in-aspnet-core"></a><span data-ttu-id="9a145-227">Configuration de la prise en charge du fichier PO dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9a145-227">Configuring PO file support in ASP.NET Core</span></span>

<span data-ttu-id="9a145-228">Cet exemple est basé sur une application ASP.NET Core MVC générée à partir d’un modèle de projet Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="9a145-228">This example is based on an ASP.NET Core MVC application generated from a Visual Studio 2017 project template.</span></span>

### <a name="referencing-the-package"></a><span data-ttu-id="9a145-229">Référencement du package</span><span class="sxs-lookup"><span data-stu-id="9a145-229">Referencing the package</span></span>

<span data-ttu-id="9a145-230">Ajoutez une référence au package NuGet `OrchardCore.Localization.Core`.</span><span class="sxs-lookup"><span data-stu-id="9a145-230">Add a reference to the `OrchardCore.Localization.Core` NuGet package.</span></span>

<span data-ttu-id="9a145-231">Le fichier *.csproj* contient maintenant une ligne semblable à la suivante (le numéro de version peut varier) :</span><span class="sxs-lookup"><span data-stu-id="9a145-231">The *.csproj* file now contains a line similar to the following (version number may vary):</span></span>

[!code-xml[](localization/sample/2.x/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a><span data-ttu-id="9a145-232">Inscription du service</span><span class="sxs-lookup"><span data-stu-id="9a145-232">Registering the service</span></span>

<span data-ttu-id="9a145-233">Ajoutez les services nécessaires à la méthode `ConfigureServices` de *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="9a145-233">Add the required services to the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

<span data-ttu-id="9a145-234">Ajoutez l’intergiciel (middleware) nécessaire à la méthode `Configure` de *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="9a145-234">Add the required middleware to the `Configure` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="9a145-235">Ajoutez le code suivant à votre Razor vue de votre choix.</span><span class="sxs-lookup"><span data-stu-id="9a145-235">Add the following code to your Razor view of choice.</span></span> <span data-ttu-id="9a145-236">*About.cshtml* est utilisé dans cet exemple.</span><span class="sxs-lookup"><span data-stu-id="9a145-236">*About.cshtml* is used in this example.</span></span>

[!code-cshtml[](localization/sample/2.x/POLocalization/Views/Home/About.cshtml)]

<span data-ttu-id="9a145-237">Une instance `IViewLocalizer` est injectée et utilisée pour traduire le texte « Hello world! ».</span><span class="sxs-lookup"><span data-stu-id="9a145-237">An `IViewLocalizer` instance is injected and used to translate the text "Hello world!".</span></span>

### <a name="creating-a-po-file"></a><span data-ttu-id="9a145-238">Création d’un fichier PO</span><span class="sxs-lookup"><span data-stu-id="9a145-238">Creating a PO file</span></span>

<span data-ttu-id="9a145-239">Créez un fichier nommé *\<culture code> . po* dans le dossier racine de votre application.</span><span class="sxs-lookup"><span data-stu-id="9a145-239">Create a file named *\<culture code>.po* in your application root folder.</span></span> <span data-ttu-id="9a145-240">Dans cet exemple, le nom de fichier est *fr.po*, car le français est utilisé :</span><span class="sxs-lookup"><span data-stu-id="9a145-240">In this example, the file name is *fr.po* because the French language is used:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

<span data-ttu-id="9a145-241">Ce fichier stocke à la fois la chaîne à traduire et la chaîne traduite en français.</span><span class="sxs-lookup"><span data-stu-id="9a145-241">This file stores both the string to translate and the French-translated string.</span></span> <span data-ttu-id="9a145-242">La culture parente des traductions peut être rétablie, si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="9a145-242">Translations revert to their parent culture, if necessary.</span></span> <span data-ttu-id="9a145-243">Dans cet exemple, le fichier *fr.po* est utilisé si la culture demandée est `fr-FR` ou `fr-CA`.</span><span class="sxs-lookup"><span data-stu-id="9a145-243">In this example, the *fr.po* file is used if the requested culture is `fr-FR` or `fr-CA`.</span></span>

### <a name="testing-the-application"></a><span data-ttu-id="9a145-244">Test de l’application</span><span class="sxs-lookup"><span data-stu-id="9a145-244">Testing the application</span></span>

<span data-ttu-id="9a145-245">Exécutez votre application, puis accédez à l’URL `/Home/About`.</span><span class="sxs-lookup"><span data-stu-id="9a145-245">Run your application, and navigate to the URL `/Home/About`.</span></span> <span data-ttu-id="9a145-246">Le texte **Hello world!**</span><span class="sxs-lookup"><span data-stu-id="9a145-246">The text **Hello world!**</span></span> <span data-ttu-id="9a145-247">ne s'affiche.</span><span class="sxs-lookup"><span data-stu-id="9a145-247">is displayed.</span></span>

<span data-ttu-id="9a145-248">Accédez à l’URL `/Home/About?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="9a145-248">Navigate to the URL `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="9a145-249">Le texte **Bonjour le monde !**</span><span class="sxs-lookup"><span data-stu-id="9a145-249">The text **Bonjour le monde!**</span></span> <span data-ttu-id="9a145-250">ne s'affiche.</span><span class="sxs-lookup"><span data-stu-id="9a145-250">is displayed.</span></span>

## <a name="pluralization"></a><span data-ttu-id="9a145-251">Forme plurielle</span><span class="sxs-lookup"><span data-stu-id="9a145-251">Pluralization</span></span>

<span data-ttu-id="9a145-252">Les fichiers PO prennent en charge les formes de pluralisation, ce qui est utile quand la même chaîne doit être traduite différemment en fonction d’une cardinalité.</span><span class="sxs-lookup"><span data-stu-id="9a145-252">PO files support pluralization forms, which is useful when the same string needs to be translated differently based on a cardinality.</span></span> <span data-ttu-id="9a145-253">Cette tâche est rendue compliquée par le fait que chaque langue définit des règles personnalisées pour sélectionner la chaîne à utiliser en fonction de la cardinalité.</span><span class="sxs-lookup"><span data-stu-id="9a145-253">This task is made complicated by the fact that each language defines custom rules to select which string to use based on the cardinality.</span></span>

<span data-ttu-id="9a145-254">Le package de localisation Orchard fournit une API pour appeler automatiquement ces différentes formes plurielles.</span><span class="sxs-lookup"><span data-stu-id="9a145-254">The Orchard Localization package provides an API to invoke these different plural forms automatically.</span></span>

### <a name="creating-pluralization-po-files"></a><span data-ttu-id="9a145-255">Création de fichiers PO de pluralisation</span><span class="sxs-lookup"><span data-stu-id="9a145-255">Creating pluralization PO files</span></span>

<span data-ttu-id="9a145-256">Ajoutez le contenu suivant au fichier *fr.po* mentionné précédemment :</span><span class="sxs-lookup"><span data-stu-id="9a145-256">Add the following content to the previously mentioned *fr.po* file:</span></span>

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

<span data-ttu-id="9a145-257">Consultez [Qu’est-ce qu’un fichier PO ?](#what-is-a-po-file) pour obtenir une explication de ce que représente chaque entrée de cet exemple.</span><span class="sxs-lookup"><span data-stu-id="9a145-257">See [What is a PO file?](#what-is-a-po-file) for an explanation of what each entry in this example represents.</span></span>

### <a name="adding-a-language-using-different-pluralization-forms"></a><span data-ttu-id="9a145-258">Ajout d’une langue utilisant différentes formes de pluralisation</span><span class="sxs-lookup"><span data-stu-id="9a145-258">Adding a language using different pluralization forms</span></span>

<span data-ttu-id="9a145-259">Des chaînes en anglais et en français ont été utilisées dans l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="9a145-259">English and French strings were used in the previous example.</span></span> <span data-ttu-id="9a145-260">L’anglais et le français n’ont que deux formes de pluralisation et partagent les mêmes règles de forme, qui est qu’une cardinalité de 1 est mappée à la première forme plurielle.</span><span class="sxs-lookup"><span data-stu-id="9a145-260">English and French have only two pluralization forms and share the same form rules, which is that a cardinality of one is mapped to the first plural form.</span></span> <span data-ttu-id="9a145-261">N’importe quelle autre cardinalité est mappée à la seconde forme plurielle.</span><span class="sxs-lookup"><span data-stu-id="9a145-261">Any other cardinality is mapped to the second plural form.</span></span>

<span data-ttu-id="9a145-262">Toutes les langues ne partagent pas les mêmes règles.</span><span class="sxs-lookup"><span data-stu-id="9a145-262">Not all languages share the same rules.</span></span> <span data-ttu-id="9a145-263">À titre d’exemple, il y a la langue tchèque qui a trois formes plurielles.</span><span class="sxs-lookup"><span data-stu-id="9a145-263">This is illustrated with the Czech language, which has three plural forms.</span></span>

<span data-ttu-id="9a145-264">Créez le fichier `cs.po` comme suit, puis notez comment la pluralisation a besoin de trois traductions différentes :</span><span class="sxs-lookup"><span data-stu-id="9a145-264">Create the `cs.po` file as follows, and note how the pluralization needs three different translations:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/cs.po)]

<span data-ttu-id="9a145-265">Pour accepter les localisations tchèques, ajoutez `"cs"` à la liste des cultures prises en charge dans la méthode `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9a145-265">To accept Czech localizations, add `"cs"` to the list of supported cultures in the `ConfigureServices` method:</span></span>

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

<span data-ttu-id="9a145-266">Modifiez le fichier *Views/Home/About.cshtml* pour restituer des chaînes localisées au pluriel pour plusieurs cardinalités :</span><span class="sxs-lookup"><span data-stu-id="9a145-266">Edit the *Views/Home/About.cshtml* file to render localized, plural strings for several cardinalities:</span></span>

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

<span data-ttu-id="9a145-267">**Remarque :** Dans un scénario réel, une variable serait utilisée pour représenter le nombre.</span><span class="sxs-lookup"><span data-stu-id="9a145-267">**Note:** In a real world scenario, a variable would be used to represent the count.</span></span> <span data-ttu-id="9a145-268">Ici, nous répétons le même code avec trois valeurs différentes pour exposer un cas très spécifique.</span><span class="sxs-lookup"><span data-stu-id="9a145-268">Here, we repeat the same code with three different values to expose a very specific case.</span></span>

<span data-ttu-id="9a145-269">Quand vous passez d’une culture à l’autre, vous voyez ceci :</span><span class="sxs-lookup"><span data-stu-id="9a145-269">Upon switching cultures, you see the following:</span></span>

<span data-ttu-id="9a145-270">Pour `/Home/About` :</span><span class="sxs-lookup"><span data-stu-id="9a145-270">For `/Home/About`:</span></span>

```html
There is one item.
There are 2 items.
There are 5 items.
```

<span data-ttu-id="9a145-271">Pour `/Home/About?culture=fr` :</span><span class="sxs-lookup"><span data-stu-id="9a145-271">For `/Home/About?culture=fr`:</span></span>

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

<span data-ttu-id="9a145-272">Pour `/Home/About?culture=cs` :</span><span class="sxs-lookup"><span data-stu-id="9a145-272">For `/Home/About?culture=cs`:</span></span>

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

<span data-ttu-id="9a145-273">Notez que pour la culture tchèque, les trois traductions sont différentes.</span><span class="sxs-lookup"><span data-stu-id="9a145-273">Note that for the Czech culture, the three translations are different.</span></span> <span data-ttu-id="9a145-274">Les cultures anglaise et française partagent la même construction pour les deux dernières chaînes traduites.</span><span class="sxs-lookup"><span data-stu-id="9a145-274">The French and English cultures share the same construction for the two last translated strings.</span></span>

## <a name="advanced-tasks"></a><span data-ttu-id="9a145-275">Tâches avancées</span><span class="sxs-lookup"><span data-stu-id="9a145-275">Advanced tasks</span></span>

### <a name="contextualizing-strings"></a><span data-ttu-id="9a145-276">Contextualisation de chaînes</span><span class="sxs-lookup"><span data-stu-id="9a145-276">Contextualizing strings</span></span>

<span data-ttu-id="9a145-277">Les applications contiennent souvent les chaînes à traduire dans plusieurs emplacements.</span><span class="sxs-lookup"><span data-stu-id="9a145-277">Applications often contain the strings to be translated in several places.</span></span> <span data-ttu-id="9a145-278">La même chaîne peut avoir une traduction différente dans certains emplacements d’une application ( Razor vues ou fichiers de classe).</span><span class="sxs-lookup"><span data-stu-id="9a145-278">The same string may have a different translation in certain locations within an app (Razor views or class files).</span></span> <span data-ttu-id="9a145-279">Un fichier PO prend en charge la notion d’un contexte de fichier, qui peut être utilisé pour catégoriser la chaîne représentée.</span><span class="sxs-lookup"><span data-stu-id="9a145-279">A PO file supports the notion of a file context, which can be used to categorize the string being represented.</span></span> <span data-ttu-id="9a145-280">L’utilisation d’un contexte de fichier permet de traduire une chaîne différemment en fonction du contexte du fichier (ou de l’absence de contexte de fichier).</span><span class="sxs-lookup"><span data-stu-id="9a145-280">Using a file context, a string can be translated differently, depending on the file context (or lack of a file context).</span></span>

<span data-ttu-id="9a145-281">Les services de localisation de PO utilisent le nom de la classe complète ou de la vue utilisée lors de la traduction d’une chaîne.</span><span class="sxs-lookup"><span data-stu-id="9a145-281">The PO localization services use the name of the full class or the view that's used when translating a string.</span></span> <span data-ttu-id="9a145-282">Pour ce faire, il vous suffit de définir la valeur sur l’entrée `msgctxt`.</span><span class="sxs-lookup"><span data-stu-id="9a145-282">This is accomplished by setting the value on the `msgctxt` entry.</span></span>

<span data-ttu-id="9a145-283">Examinons un ajout mineur à l’exemple *fr.po* précédent.</span><span class="sxs-lookup"><span data-stu-id="9a145-283">Consider a minor addition to the previous *fr.po* example.</span></span> <span data-ttu-id="9a145-284">Vous Razor pouvez définir une vue située dans *views/orig/about. cshtml* comme contexte de fichier en définissant la `msgctxt` valeur de l’entrée réservée :</span><span class="sxs-lookup"><span data-stu-id="9a145-284">A Razor view located at *Views/Home/About.cshtml* can be defined as the file context by setting the reserved `msgctxt` entry's value:</span></span>

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

<span data-ttu-id="9a145-285">Avec `msgctxt` ainsi définie, la traduction de texte se produit lors de la navigation vers `/Home/About?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="9a145-285">With the `msgctxt` set as such, text translation occurs when navigating to `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="9a145-286">La traduction ne se produit pas lors de la navigation vers `/Home/Contact?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="9a145-286">The translation won't occur when navigating to `/Home/Contact?culture=fr-FR`.</span></span>

<span data-ttu-id="9a145-287">Quand aucune entrée spécifique ne correspond à un contexte de fichier donné, le mécanisme de secours d’Orchard Core recherche un fichier PO approprié sans contexte.</span><span class="sxs-lookup"><span data-stu-id="9a145-287">When no specific entry is matched with a given file context, Orchard Core's fallback mechanism looks for an appropriate PO file without a context.</span></span> <span data-ttu-id="9a145-288">En supposant qu’aucun contexte de fichier spécifique n’est défini pour *Views/Home/Contact.cshtml*, la navigation vers `/Home/Contact?culture=fr-FR` charge un fichier PO tel que le suivant :</span><span class="sxs-lookup"><span data-stu-id="9a145-288">Assuming there's no specific file context defined for *Views/Home/Contact.cshtml*, navigating to `/Home/Contact?culture=fr-FR` loads a PO file such as:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a><span data-ttu-id="9a145-289">Changement de l’emplacement des fichiers PO</span><span class="sxs-lookup"><span data-stu-id="9a145-289">Changing the location of PO files</span></span>

<span data-ttu-id="9a145-290">L’emplacement par défaut des fichiers PO peut être changé dans `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9a145-290">The default location of PO files can be changed in `ConfigureServices`:</span></span>

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

<span data-ttu-id="9a145-291">Dans cet exemple, les fichiers PO sont chargés à partir du dossier *Localisation*.</span><span class="sxs-lookup"><span data-stu-id="9a145-291">In this example, the PO files are loaded from the *Localization* folder.</span></span>

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a><span data-ttu-id="9a145-292">Implémentation d’une logique personnalisée pour la recherche de fichiers de localisation</span><span class="sxs-lookup"><span data-stu-id="9a145-292">Implementing a custom logic for finding localization files</span></span>

<span data-ttu-id="9a145-293">Quand une logique plus complexe est nécessaire pour localiser des fichiers PO, l’interface `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` peut être implémentée et inscrite comme service.</span><span class="sxs-lookup"><span data-stu-id="9a145-293">When more complex logic is needed to locate PO files, the `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` interface can be implemented and registered as a service.</span></span> <span data-ttu-id="9a145-294">Cela est utile quand les fichiers PO peuvent être stockés dans différents emplacements ou quand la recherche des fichiers doit être effectuée dans une hiérarchie de dossiers.</span><span class="sxs-lookup"><span data-stu-id="9a145-294">This is useful when PO files can be stored in varying locations or when the files have to be found within a hierarchy of folders.</span></span>

### <a name="using-a-different-default-pluralized-language"></a><span data-ttu-id="9a145-295">Utilisation d’une autre langue pluralisée par défaut</span><span class="sxs-lookup"><span data-stu-id="9a145-295">Using a different default pluralized language</span></span>

<span data-ttu-id="9a145-296">Le package inclut une méthode d’extension `Plural` spécifique à deux formes plurielles.</span><span class="sxs-lookup"><span data-stu-id="9a145-296">The package includes a `Plural` extension method that's specific to two plural forms.</span></span> <span data-ttu-id="9a145-297">Pour les langues nécessitant plus de formes plurielles, créez une méthode d’extension.</span><span class="sxs-lookup"><span data-stu-id="9a145-297">For languages requiring more plural forms, create an extension method.</span></span> <span data-ttu-id="9a145-298">Avec une méthode d’extension, vous n’avez pas à fournir de fichier de localisation pour la langue par défaut : en effet, les chaînes d’origine sont déjà disponibles directement dans le code.</span><span class="sxs-lookup"><span data-stu-id="9a145-298">With an extension method, you won't need to provide any localization file for the default language &mdash; the original strings are already available directly in the code.</span></span>

<span data-ttu-id="9a145-299">Vous pouvez utiliser la surcharge `Plural(int count, string[] pluralForms, params object[] arguments)` plus générique qui accepte un tableau de chaînes des traductions.</span><span class="sxs-lookup"><span data-stu-id="9a145-299">You can use the more generic `Plural(int count, string[] pluralForms, params object[] arguments)` overload which accepts a string array of translations.</span></span>

::: moniker-end
