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
# <a name="configure-portable-object-localization-in-aspnet-core"></a>Configurer la localisation d’objets portables dans ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Par [Sébastien Ros](https://github.com/sebastienros), [Scott Addie](https://twitter.com/Scott_Addie) et [Hisham bin Ateya](https://github.com/hishamco)

Cet article présente les étapes d’utilisation d’objets portables (PO, Portable Object) dans une application ASP.NET Core avec le framework [Orchard Core](https://github.com/OrchardCMS/OrchardCore).

**Remarque :** Orchard Core n’est pas un produit Microsoft. Par conséquent, Microsoft ne fournit aucun support pour cette fonctionnalité.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/localization/sample/3.x/POLocalization) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="what-is-a-po-file"></a>Qu’est-ce qu’un fichier PO ?

Les fichiers PO sont distribués sous forme de fichiers texte contenant les chaînes traduites pour une langue donnée. L’utilisation de fichiers PO au lieu de fichiers *. resx* présente les avantages suivants :
- Les fichiers PO prennent en charge la pluralisation, contrairement aux fichiers *.resx*.
- Les fichiers PO ne sont pas compilés comme les fichiers *.resx*. De ce fait, il n’est pas nécessaire d’effectuer les étapes relatives aux outils et à la génération.
- Les fichiers PO fonctionnent correctement avec les outils d’édition collaboratifs en ligne.

### <a name="example"></a>Exemple

Voici un exemple de fichier PO contenant la traduction de deux chaînes en français, dont une avec sa forme au pluriel :

*fr.po*

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

Cet exemple utilise la syntaxe suivante :

- `#:` : commentaire indiquant le contexte de la chaîne à traduire. La même chaîne peut être traduite différemment selon l’endroit où elle est utilisée.
- `msgid` : la chaîne non traduite.
- `msgstr` : la chaîne traduite.

Dans le cas de la prise en charge de la pluralisation, des entrées supplémentaires peuvent être définies.

- `msgid_plural` : la chaîne au pluriel non traduite.
- `msgstr[0]` : la chaîne traduite pour le cas 0.
- `msgstr[N]` : la chaîne traduite pour le cas N.

La spécification du fichier PO se trouve [ici](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).

## <a name="configuring-po-file-support-in-aspnet-core"></a>Configuration de la prise en charge du fichier PO dans ASP.NET Core

Cet exemple est basé sur une application ASP.NET Core MVC générée à partir d’un modèle de projet Visual Studio 2017.

### <a name="referencing-the-package"></a>Référencement du package

Ajoutez une référence au package NuGet `OrchardCore.Localization.Core`.

Le fichier *.csproj* contient maintenant une ligne semblable à la suivante (le numéro de version peut varier) :

[!code-xml[](localization/sample/3.x/POLocalization/POLocalization.csproj?range=8)]

### <a name="registering-the-service"></a>Inscription du service

Ajoutez les services nécessaires à la méthode `ConfigureServices` de *Startup.cs* :

[!code-csharp[](localization/sample/3.x/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

Ajoutez l’intergiciel (middleware) nécessaire à la méthode `Configure` de *Startup.cs* :

[!code-csharp[](localization/sample/3.x/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

Ajoutez le code suivant à votre Razor vue de votre choix. *About.cshtml* est utilisé dans cet exemple.

[!code-cshtml[](localization/sample/3.x/POLocalization/Views/Home/About.cshtml)]

Une instance `IViewLocalizer` est injectée et utilisée pour traduire le texte « Hello world! ».

### <a name="creating-a-po-file"></a>Création d’un fichier PO

Créez un fichier nommé *\<culture code> . po* dans le dossier racine de votre application. Dans cet exemple, le nom de fichier est *fr.po*, car le français est utilisé :

[!code-text[](localization/sample/3.x/POLocalization/fr.po)]

Ce fichier stocke à la fois la chaîne à traduire et la chaîne traduite en français. La culture parente des traductions peut être rétablie, si nécessaire. Dans cet exemple, le fichier *fr.po* est utilisé si la culture demandée est `fr-FR` ou `fr-CA`.

### <a name="testing-the-application"></a>Test de l’application

Exécutez votre application, puis accédez à l’URL `/Home/About`. Le texte **Hello world!** ne s'affiche.

Accédez à l’URL `/Home/About?culture=fr-FR`. Le texte **Bonjour le monde !** ne s'affiche.

## <a name="pluralization"></a>Forme plurielle

Les fichiers PO prennent en charge les formes de pluralisation, ce qui est utile quand la même chaîne doit être traduite différemment en fonction d’une cardinalité. Cette tâche est rendue compliquée par le fait que chaque langue définit des règles personnalisées pour sélectionner la chaîne à utiliser en fonction de la cardinalité.

Le package de localisation Orchard fournit une API pour appeler automatiquement ces différentes formes plurielles.

### <a name="creating-pluralization-po-files"></a>Création de fichiers PO de pluralisation

Ajoutez le contenu suivant au fichier *fr.po* mentionné précédemment :

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

Consultez [Qu’est-ce qu’un fichier PO ?](#what-is-a-po-file) pour obtenir une explication de ce que représente chaque entrée de cet exemple.

### <a name="adding-a-language-using-different-pluralization-forms"></a>Ajout d’une langue utilisant différentes formes de pluralisation

Des chaînes en anglais et en français ont été utilisées dans l’exemple précédent. L’anglais et le français n’ont que deux formes de pluralisation et partagent les mêmes règles de forme, qui est qu’une cardinalité de 1 est mappée à la première forme plurielle. N’importe quelle autre cardinalité est mappée à la seconde forme plurielle.

Toutes les langues ne partagent pas les mêmes règles. À titre d’exemple, il y a la langue tchèque qui a trois formes plurielles.

Créez le fichier `cs.po` comme suit, puis notez comment la pluralisation a besoin de trois traductions différentes :

[!code-text[](localization/sample/3.x/POLocalization/cs.po)]

Pour accepter les localisations tchèques, ajoutez `"cs"` à la liste des cultures prises en charge dans la méthode `ConfigureServices` :

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

Modifiez le fichier *Views/Home/About.cshtml* pour restituer des chaînes localisées au pluriel pour plusieurs cardinalités :

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

**Remarque :** Dans un scénario réel, une variable serait utilisée pour représenter le nombre. Ici, nous répétons le même code avec trois valeurs différentes pour exposer un cas très spécifique.

Quand vous passez d’une culture à l’autre, vous voyez ceci :

Pour `/Home/About` :

```html
There is one item.
There are 2 items.
There are 5 items.
```

Pour `/Home/About?culture=fr` :

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

Pour `/Home/About?culture=cs` :

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

Notez que pour la culture tchèque, les trois traductions sont différentes. Les cultures anglaise et française partagent la même construction pour les deux dernières chaînes traduites.

## <a name="advanced-tasks"></a>Tâches avancées

### <a name="contextualizing-strings"></a>Contextualisation de chaînes

Les applications contiennent souvent les chaînes à traduire dans plusieurs emplacements. La même chaîne peut avoir une traduction différente dans certains emplacements d’une application ( Razor vues ou fichiers de classe). Un fichier PO prend en charge la notion d’un contexte de fichier, qui peut être utilisé pour catégoriser la chaîne représentée. L’utilisation d’un contexte de fichier permet de traduire une chaîne différemment en fonction du contexte du fichier (ou de l’absence de contexte de fichier).

Les services de localisation de PO utilisent le nom de la classe complète ou de la vue utilisée lors de la traduction d’une chaîne. Pour ce faire, il vous suffit de définir la valeur sur l’entrée `msgctxt`.

Examinons un ajout mineur à l’exemple *fr.po* précédent. Vous Razor pouvez définir une vue située dans *views/orig/about. cshtml* comme contexte de fichier en définissant la `msgctxt` valeur de l’entrée réservée :

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

Avec `msgctxt` ainsi définie, la traduction de texte se produit lors de la navigation vers `/Home/About?culture=fr-FR`. La traduction ne se produit pas lors de la navigation vers `/Home/Contact?culture=fr-FR`.

Quand aucune entrée spécifique ne correspond à un contexte de fichier donné, le mécanisme de secours d’Orchard Core recherche un fichier PO approprié sans contexte. En supposant qu’aucun contexte de fichier spécifique n’est défini pour *Views/Home/Contact.cshtml*, la navigation vers `/Home/Contact?culture=fr-FR` charge un fichier PO tel que le suivant :

[!code-text[](localization/sample/3.x/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a>Changement de l’emplacement des fichiers PO

L’emplacement par défaut des fichiers PO peut être changé dans `ConfigureServices` :

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

Dans cet exemple, les fichiers PO sont chargés à partir du dossier *Localisation*.

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a>Implémentation d’une logique personnalisée pour la recherche de fichiers de localisation

Quand une logique plus complexe est nécessaire pour localiser des fichiers PO, l’interface `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` peut être implémentée et inscrite comme service. Cela est utile quand les fichiers PO peuvent être stockés dans différents emplacements ou quand la recherche des fichiers doit être effectuée dans une hiérarchie de dossiers.

### <a name="using-a-different-default-pluralized-language"></a>Utilisation d’une autre langue pluralisée par défaut

Le package inclut une méthode d’extension `Plural` spécifique à deux formes plurielles. Pour les langues nécessitant plus de formes plurielles, créez une méthode d’extension. Avec une méthode d’extension, vous n’avez pas à fournir de fichier de localisation pour la langue par défaut : en effet, les chaînes d’origine sont déjà disponibles directement dans le code.

Vous pouvez utiliser la surcharge `Plural(int count, string[] pluralForms, params object[] arguments)` plus générique qui accepte un tableau de chaînes des traductions.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Par [Sébastien Ros](https://github.com/sebastienros) et [Scott Addie](https://twitter.com/Scott_Addie)

Cet article présente les étapes d’utilisation d’objets portables (PO, Portable Object) dans une application ASP.NET Core avec le framework [Orchard Core](https://github.com/OrchardCMS/OrchardCore).

**Remarque :** Orchard Core n’est pas un produit Microsoft. Par conséquent, Microsoft ne fournit aucun support pour cette fonctionnalité.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/localization/sample/2.x/POLocalization) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="what-is-a-po-file"></a>Qu’est-ce qu’un fichier PO ?

Les fichiers PO sont distribués sous forme de fichiers texte contenant les chaînes traduites pour une langue donnée. L’utilisation de fichiers PO plutôt que des fichiers *.resx* présente certains avantages, notamment les suivants :
- Les fichiers PO prennent en charge la pluralisation, contrairement aux fichiers *.resx*.
- Les fichiers PO ne sont pas compilés comme les fichiers *.resx*. De ce fait, il n’est pas nécessaire d’effectuer les étapes relatives aux outils et à la génération.
- Les fichiers PO fonctionnent correctement avec les outils d’édition collaboratifs en ligne.

### <a name="example"></a>Exemple

Voici un exemple de fichier PO contenant la traduction de deux chaînes en français, dont une avec sa forme au pluriel :

*fr.po*

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

Cet exemple utilise la syntaxe suivante :

- `#:` : commentaire indiquant le contexte de la chaîne à traduire. La même chaîne peut être traduite différemment selon l’endroit où elle est utilisée.
- `msgid` : la chaîne non traduite.
- `msgstr` : la chaîne traduite.

Dans le cas de la prise en charge de la pluralisation, des entrées supplémentaires peuvent être définies.

- `msgid_plural` : la chaîne au pluriel non traduite.
- `msgstr[0]` : la chaîne traduite pour le cas 0.
- `msgstr[N]` : la chaîne traduite pour le cas N.

La spécification du fichier PO se trouve [ici](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).

## <a name="configuring-po-file-support-in-aspnet-core"></a>Configuration de la prise en charge du fichier PO dans ASP.NET Core

Cet exemple est basé sur une application ASP.NET Core MVC générée à partir d’un modèle de projet Visual Studio 2017.

### <a name="referencing-the-package"></a>Référencement du package

Ajoutez une référence au package NuGet `OrchardCore.Localization.Core`.

Le fichier *.csproj* contient maintenant une ligne semblable à la suivante (le numéro de version peut varier) :

[!code-xml[](localization/sample/2.x/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a>Inscription du service

Ajoutez les services nécessaires à la méthode `ConfigureServices` de *Startup.cs* :

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

Ajoutez l’intergiciel (middleware) nécessaire à la méthode `Configure` de *Startup.cs* :

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

Ajoutez le code suivant à votre Razor vue de votre choix. *About.cshtml* est utilisé dans cet exemple.

[!code-cshtml[](localization/sample/2.x/POLocalization/Views/Home/About.cshtml)]

Une instance `IViewLocalizer` est injectée et utilisée pour traduire le texte « Hello world! ».

### <a name="creating-a-po-file"></a>Création d’un fichier PO

Créez un fichier nommé *\<culture code> . po* dans le dossier racine de votre application. Dans cet exemple, le nom de fichier est *fr.po*, car le français est utilisé :

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

Ce fichier stocke à la fois la chaîne à traduire et la chaîne traduite en français. La culture parente des traductions peut être rétablie, si nécessaire. Dans cet exemple, le fichier *fr.po* est utilisé si la culture demandée est `fr-FR` ou `fr-CA`.

### <a name="testing-the-application"></a>Test de l’application

Exécutez votre application, puis accédez à l’URL `/Home/About`. Le texte **Hello world!** ne s'affiche.

Accédez à l’URL `/Home/About?culture=fr-FR`. Le texte **Bonjour le monde !** ne s'affiche.

## <a name="pluralization"></a>Forme plurielle

Les fichiers PO prennent en charge les formes de pluralisation, ce qui est utile quand la même chaîne doit être traduite différemment en fonction d’une cardinalité. Cette tâche est rendue compliquée par le fait que chaque langue définit des règles personnalisées pour sélectionner la chaîne à utiliser en fonction de la cardinalité.

Le package de localisation Orchard fournit une API pour appeler automatiquement ces différentes formes plurielles.

### <a name="creating-pluralization-po-files"></a>Création de fichiers PO de pluralisation

Ajoutez le contenu suivant au fichier *fr.po* mentionné précédemment :

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

Consultez [Qu’est-ce qu’un fichier PO ?](#what-is-a-po-file) pour obtenir une explication de ce que représente chaque entrée de cet exemple.

### <a name="adding-a-language-using-different-pluralization-forms"></a>Ajout d’une langue utilisant différentes formes de pluralisation

Des chaînes en anglais et en français ont été utilisées dans l’exemple précédent. L’anglais et le français n’ont que deux formes de pluralisation et partagent les mêmes règles de forme, qui est qu’une cardinalité de 1 est mappée à la première forme plurielle. N’importe quelle autre cardinalité est mappée à la seconde forme plurielle.

Toutes les langues ne partagent pas les mêmes règles. À titre d’exemple, il y a la langue tchèque qui a trois formes plurielles.

Créez le fichier `cs.po` comme suit, puis notez comment la pluralisation a besoin de trois traductions différentes :

[!code-text[](localization/sample/2.x/POLocalization/cs.po)]

Pour accepter les localisations tchèques, ajoutez `"cs"` à la liste des cultures prises en charge dans la méthode `ConfigureServices` :

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

Modifiez le fichier *Views/Home/About.cshtml* pour restituer des chaînes localisées au pluriel pour plusieurs cardinalités :

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

**Remarque :** Dans un scénario réel, une variable serait utilisée pour représenter le nombre. Ici, nous répétons le même code avec trois valeurs différentes pour exposer un cas très spécifique.

Quand vous passez d’une culture à l’autre, vous voyez ceci :

Pour `/Home/About` :

```html
There is one item.
There are 2 items.
There are 5 items.
```

Pour `/Home/About?culture=fr` :

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

Pour `/Home/About?culture=cs` :

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

Notez que pour la culture tchèque, les trois traductions sont différentes. Les cultures anglaise et française partagent la même construction pour les deux dernières chaînes traduites.

## <a name="advanced-tasks"></a>Tâches avancées

### <a name="contextualizing-strings"></a>Contextualisation de chaînes

Les applications contiennent souvent les chaînes à traduire dans plusieurs emplacements. La même chaîne peut avoir une traduction différente dans certains emplacements d’une application ( Razor vues ou fichiers de classe). Un fichier PO prend en charge la notion d’un contexte de fichier, qui peut être utilisé pour catégoriser la chaîne représentée. L’utilisation d’un contexte de fichier permet de traduire une chaîne différemment en fonction du contexte du fichier (ou de l’absence de contexte de fichier).

Les services de localisation de PO utilisent le nom de la classe complète ou de la vue utilisée lors de la traduction d’une chaîne. Pour ce faire, il vous suffit de définir la valeur sur l’entrée `msgctxt`.

Examinons un ajout mineur à l’exemple *fr.po* précédent. Vous Razor pouvez définir une vue située dans *views/orig/about. cshtml* comme contexte de fichier en définissant la `msgctxt` valeur de l’entrée réservée :

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

Avec `msgctxt` ainsi définie, la traduction de texte se produit lors de la navigation vers `/Home/About?culture=fr-FR`. La traduction ne se produit pas lors de la navigation vers `/Home/Contact?culture=fr-FR`.

Quand aucune entrée spécifique ne correspond à un contexte de fichier donné, le mécanisme de secours d’Orchard Core recherche un fichier PO approprié sans contexte. En supposant qu’aucun contexte de fichier spécifique n’est défini pour *Views/Home/Contact.cshtml*, la navigation vers `/Home/Contact?culture=fr-FR` charge un fichier PO tel que le suivant :

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a>Changement de l’emplacement des fichiers PO

L’emplacement par défaut des fichiers PO peut être changé dans `ConfigureServices` :

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

Dans cet exemple, les fichiers PO sont chargés à partir du dossier *Localisation*.

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a>Implémentation d’une logique personnalisée pour la recherche de fichiers de localisation

Quand une logique plus complexe est nécessaire pour localiser des fichiers PO, l’interface `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` peut être implémentée et inscrite comme service. Cela est utile quand les fichiers PO peuvent être stockés dans différents emplacements ou quand la recherche des fichiers doit être effectuée dans une hiérarchie de dossiers.

### <a name="using-a-different-default-pluralized-language"></a>Utilisation d’une autre langue pluralisée par défaut

Le package inclut une méthode d’extension `Plural` spécifique à deux formes plurielles. Pour les langues nécessitant plus de formes plurielles, créez une méthode d’extension. Avec une méthode d’extension, vous n’avez pas à fournir de fichier de localisation pour la langue par défaut : en effet, les chaînes d’origine sont déjà disponibles directement dans le code.

Vous pouvez utiliser la surcharge `Plural(int count, string[] pluralForms, params object[] arguments)` plus générique qui accepte un tableau de chaînes des traductions.

::: moniker-end
