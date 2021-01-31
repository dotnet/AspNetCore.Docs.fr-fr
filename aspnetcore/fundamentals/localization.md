---
title: Globalisation et localisation dans ASP.NET Core
author: rick-anderson
description: Découvrez les services et intergiciels (middleware) fournis par ASP.NET Core pour localiser du contenu dans différentes langues et cultures.
ms.author: riande
ms.date: 11/30/2019
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
uid: fundamentals/localization
ms.openlocfilehash: 67f245b7f4e4aa97b30c5318c73732617aea44c7
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217568"
---
# <a name="globalization-and-localization-in-aspnet-core"></a>Globalisation et localisation dans ASP.NET Core

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/) et [Hisham Bin Ateya](https://twitter.com/hishambinateya)

Un site Web multilingue permet au site d’atteindre un public plus vaste. ASP.NET Core offre des services et des intergiciels (middleware) de traduction dans différentes langues et cultures.

L’internationalisation implique la [globalisation](/dotnet/api/system.globalization) et la [localisation](/dotnet/standard/globalization-localization/localization). La globalisation est le processus de conception d’applications qui prennent en charge des cultures différentes. La globalisation ajoute la prise en charge de l’entrée, de l’affichage et de la sortie d’un ensemble défini de jeux de caractères liés à des zones géographiques spécifiques.

La localisation est le processus d’adaptation d’une application globalisée, dont vous avez déjà traitée l’adaptabilité, à une culture/des paramètres régionaux spécifiques. Pour plus d’informations, consultez **Terminologie de la globalisation et de la localisation** à la fin du présent document.

La localisation d’une application implique les étapes suivantes :

1. Rendre le contenu de l’application localisable
1. Fournir des ressources localisées aux langues et cultures prises en charge
1. Implémenter une stratégie de sélection de la langue/culture pour chaque requête

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/3.x/Localization) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="make-the-apps-content-localizable"></a>Rendre le contenu de l’application localisable

<xref:Microsoft.Extensions.Localization.IStringLocalizer> et <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> ont été conçus pour améliorer la productivité lors du développement d’applications localisées. `IStringLocalizer` utilise <xref:System.Resources.ResourceManager> et <xref:System.Resources.ResourceReader> pour fournir des ressources spécifiques à la culture au moment de l’exécution. L’interface possède un indexeur et un `IEnumerable` pour retourner des chaînes localisées. `IStringLocalizer` ne nécessite pas le stockage des chaînes de langue par défaut dans un fichier de ressources. Vous pouvez développer une application ciblée pour la localisation sans avoir besoin de créer des fichiers de ressources au tout début du développement. Le code ci-dessous montre comment inclure dans un wrapper la chaîne « About Title » à des fins de localisation.

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

Dans le code précédent, l' `IStringLocalizer<T>` implémentation provient de l' [injection de dépendances](dependency-injection.md). Si la valeur localisée de « About Title » est introuvable, alors la clé de l’indexeur est retournée, autrement dit, la chaîne « About Title ». Vous pouvez laisser les chaînes littérales de la langue par défaut dans l’application et les inclure dans un wrapper dans le localisateur, afin de pouvoir vous concentrer sur le développement de l’application. Vous développez votre application avec votre langue par défaut et vous la préparez à l’étape de localisation sans créer au préalable un fichier de ressources par défaut. Vous pouvez également utiliser l’approche traditionnelle et fournir une clé pour récupérer la chaîne de la langue par défaut. Pour de nombreux développeurs, le nouveau workflow qui n’inclut pas de fichier *.resx* de langue par défaut et qui consiste à simplement inclure dans un wrapper les littéraux de chaîne permet de réduire la surcharge de la localisation d’une application. D’autres développeurs préfèrent le workflow traditionnel, car il facilite l’utilisation de longs littéraux de chaîne ainsi que la mise à jour des chaînes localisées.

Utilisez l’implémentation de `IHtmlLocalizer<T>` pour les ressources qui contiennent du HTML. `IHtmlLocalizer` encode en HTML les arguments formatés dans la chaîne de ressource, mais pas la chaîne de ressource elle-même. Dans l’exemple mis en surbrillance ci-dessous, seule la valeur du paramètre `name` est encodé en HTML.

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

> [!NOTE]
> En général, localisez uniquement le texte, et non le format HTML.

Au niveau le plus bas, vous pouvez sortir `IStringLocalizerFactory` de l’[injection de dépendances](dependency-injection.md) :

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

Le code ci-dessus illustre chacune des deux méthodes de création.

Vous pouvez partitionner vos chaînes localisées par contrôleur, par zone ou avoir un seul conteneur. Dans l’exemple d’application, une classe fictive nommée `SharedResource` est utilisée pour les ressources partagées.

[!code-csharp[](localization/sample/3.x/Localization/SharedResource.cs)]

Certains développeurs utilisent la classe `Startup` pour contenir des chaînes globales ou partagées. Dans l’exemple ci-dessous, les localiseurs `InfoController` et `SharedResource` sont utilisés :

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a>Localisation de l’affichage

Le service `IViewLocalizer` fournit des chaînes localisées à une [vue](xref:mvc/views/overview). La classe `ViewLocalizer` implémente cette interface et recherche l’emplacement de la ressource à partir du chemin du fichier de la vue. Le code suivant montre comment utiliser l’implémentation par défaut de `IViewLocalizer` :

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

L’implémentation par défaut de `IViewLocalizer` recherche le fichier de ressources en fonction du nom de fichier de l’affichage. Il n’existe aucune option pour utiliser un fichier de ressources partagées globales. `ViewLocalizer` implémente le localisateur à l’aide `IHtmlLocalizer` de, donc Razor n’encode pas le code HTML de la chaîne localisée. Vous pouvez paramétrer des chaînes de ressources pour que `IViewLocalizer` encode en HTML les paramètres, mais pas les chaînes de ressources. Examinez le Razor balisage suivant :

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

Un fichier de ressources en français peut contenir ce qui suit :

| Clé | Valeur |
| --- | ----- |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

L’affichage contient le balisage HTML provenant du fichier de ressources.

> [!NOTE]
> En général, localisez uniquement le texte, et non le format HTML.

Pour utiliser un fichier de ressources partagées dans un affichage, injectez `IHtmlLocalizer<T>` :

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a>Localisation de DataAnnotations

Les messages d’erreur DataAnnotations sont localisés avec `IStringLocalizer<T>`. En utilisant l’option `ResourcesPath = "Resources"`, les messages d’erreur inclus dans `RegisterViewModel` peuvent être stockés dans les chemins suivants :

* *Ressources/ViewModels. Account. RegisterViewModel. fr. resx*
* *Resources/ViewModels/Account/RegisterViewModel.fr.resx*

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

Dans ASP.NET Core MVC 1.1.0 et version supérieure, les attributs de non-validation sont localisés. ASP.NET Core MVC 1.0 ne recherche **pas** de chaînes localisées pour des attributs de non-validation.

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a>Utilisation d’une seule chaîne de ressource pour plusieurs classes

Le code suivant montre comment utiliser une seule chaîne de ressource pour les attributs de validation avec plusieurs classes :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

Dans le code précédent, `SharedResource` est la classe correspondant au resx où sont stockés vos messages de validation. Cette approche permet à DataAnnotations d’utiliser uniquement `SharedResource`, au lieu de la ressource de chaque classe.

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a>Fournir des ressources localisées aux langues et cultures prises en charge

### <a name="supportedcultures-and-supporteduicultures"></a>SupportedCultures et SupportedUICultures

ASP.NET Core vous permet de spécifier deux valeurs de culture, <xref:Microsoft.AspNetCore.Builder.RequestLocalizationOptions.SupportedCultures> et <xref:Microsoft.AspNetCore.Builder.RequestLocalizationOptions.SupportedUICultures>. L' <xref:System.Globalization.CultureInfo> objet pour `SupportedCultures` détermine les résultats des fonctions dépendantes de la culture, telles que la mise en forme des dates, des heures, des nombres et des devises. `SupportedCultures` détermine également l’ordre de tri du texte, les conventions de casse et les comparaisons de chaînes. Pour plus d’informations sur la façon dont le serveur obtient la culture, consultez <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=nameWithType> et <xref:System.Globalization.CultureInfo.CurrentUICulture?displayProperty=nameWithType> . Le `SupportedUICultures` détermine les chaînes traduites (à partir des `.resx` fichiers) qui sont recherchées par le <xref:System.Resources.ResourceManager> . `ResourceManager`Recherche les chaînes spécifiques à la culture qui sont déterminées par `CurrentUICulture` . Chaque thread dans .NET a des objets `CurrentCulture` et `CurrentUICulture`. Le Framework inspecte ces valeurs lors du rendu des fonctions dépendantes de la culture. Si la culture du thread actuel a la valeur `en-US` (anglais, États-Unis), `DateTime.Now.ToLongDateString()` affiche, `Thursday, February 18, 2016` mais si `CurrentCulture` a la valeur `es-ES` (espagnol, Espagne), la sortie est `jueves, 18 de febrero de 2016` .

## <a name="resource-files"></a>Fichiers de ressources

Un fichier de ressources est un mécanisme utile pour séparer les chaînes localisables du code. Les chaînes traduites pour la langue non définie par défaut sont isolées dans les fichiers de ressources *. resx* . Par exemple, vous pouvez avoir besoin de créer un fichier de ressources nommé *Welcome.es.resx* qui contient des chaînes traduites. « es » correspond au code de langue pour l’espagnol. Pour créer ce fichier de ressources dans Visual Studio :

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier qui contient le fichier de ressources > **Ajouter** > **Nouvel élément**.

   ![Menu contextuel imbriqué : dans l’Explorateur de solutions, un menu contextuel est ouvert pour les ressources. Un second menu contextuel est ouvert pour l’option Ajouter, avec la commande Nouvel élément mise en surbrillance.](localization/_static/newi.png)

1. Dans la zone **Rechercher dans les modèles installés**, entrez « ressource » et nommez le fichier.

   ![Boîte de dialogue Ajouter un nouvel élément](localization/_static/res.png)

1. Entrez la valeur de la clé (chaîne native) dans la colonne **Nom** et la chaîne traduite dans la colonne **Valeur**.

   ![Le fichier Welcome.es.resx (le fichier de ressources Welcome pour l’espagnol) avec le mot Hello dans la colonne Nom et le mot Hola (bonjour en espagnol) dans la colonne Valeur](localization/_static/hola.png)

   Visual Studio présente le fichier *Welcome.es.resx*.

   ![L’Explorateur de solutions montrant le fichier de ressources Welcome pour l’espagnol (es)](localization/_static/se.png)

## <a name="resource-file-naming"></a>Nommage du fichier de ressources

Les ressources sont nommées selon le nom de type complet de leur classe moins le nom de l’assembly. Par exemple, une ressource en français dans un projet dont l’assembly principal est `LocalizationWebsite.Web.dll` pour la classe `LocalizationWebsite.Web.Startup` serait nommée *Startup.fr.resx*. Une ressource pour la classe `LocalizationWebsite.Web.Controllers.HomeController` serait nommée *Controllers.HomeController.fr.resx*. Si l’espace de noms de votre classe ciblée n’est pas identique au nom de l’assembly, vous aurez besoin du nom de type complet. Par exemple, dans l’exemple de projet, une ressource pour le type `ExtraNamespace.Tools` serait nommée *ExtraNamespace.Tools.fr.resx*.

Dans l’exemple de projet, la méthode `ConfigureServices` affecte à `ResourcesPath` la valeur « Resources », si bien que le chemin relatif du projet pour le fichier de ressources en français du contrôleur Home est *Resources/Controllers.HomeController.fr.resx*. Vous pouvez également utiliser des dossiers pour organiser les fichiers de ressources. Pour le contrôleur Home, le chemin serait *Resources/Controllers/HomeController.fr.resx*. Si vous n’utilisez pas l’option `ResourcesPath`, le fichier *.resx* se trouve dans le répertoire de base du projet. Le fichier de ressources pour `HomeController` serait nommé *Controllers.HomeController.fr.resx*. Le choix de l’utilisation de la convention d’affectation de noms avec des points ou un chemin dépend de la façon dont vous souhaitez organiser vos fichiers de ressources.

| Nom de la ressource | Affectation de noms avec des points ou un chemin |
| ------------   | ------------- |
| Resources/Controllers.HomeController.fr.resx | Points  |
| Resources/Controllers/HomeController.fr.resx  | Chemin d’accès |

Les fichiers de ressources utilisant `@inject IViewLocalizer` dans les Razor vues suivent un modèle similaire. Le fichier de ressources d’une vue peut être nommé selon la convention avec des points ou un chemin. Razor les fichiers de ressources d’affichage imitent le chemin d’accès de leur fichier de vue associé. Si nous affectons à `ResourcesPath` la valeur « Resources », le fichier de ressources en français associé à l’affichage *Views/Home/About.cshtml* peut porter l’un des noms suivants :

* Resources/Views/Home/About.fr.resx

* Resources/Views.Home.About.fr.resx

Si vous n’utilisez pas l’option `ResourcesPath`, le fichier *.resx* d’un affichage se trouve dans le même dossier que l’affichage.

### <a name="rootnamespaceattribute"></a>RootNamespaceAttribute 

L' <xref:Microsoft.Extensions.Localization.RootNamespaceAttribute> attribut fournit l’espace de noms racine d’un assembly lorsque l’espace de noms racine d’un assembly est différent du nom de l’assembly. 

> [!WARNING]
> Cela peut se produire lorsque le nom d’un projet n’est pas un identificateur .NET valide. Par exemple, `my-project-name.csproj` utilisera l’espace de noms racine `my_project_name` et le nom de l’assembly `my-project-name` menant à cette erreur. 

Si l’espace de noms racine d’un assembly est différent du nom de l’assembly :

* La localisation ne fonctionne pas par défaut.
* La localisation échoue en raison du mode de recherche des ressources dans l’assembly. `RootNamespace` est une valeur de build qui n’est pas accessible au processus exécutant. 

Si `RootNamespace` est différent de `AssemblyName`, incluez ce qui suit dans *AssemblyInfo.cs* (en remplaçant les valeurs des paramètres par les valeurs réelles) :

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

Le code précédent permet de résoudre correctement les fichiers resx.

## <a name="culture-fallback-behavior"></a>Comportement de secours pour la culture

Lors de la recherche d’une ressource, la localisation met en œuvre une « alternative de secours pour la culture ». Elle commence par la culture demandée : si elle est introuvable, il revient à la culture parente de cette culture. Par ailleurs, la propriété [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) représente la culture parente. Cela signifie généralement (mais pas toujours) supprimer l’indicateur national du code ISO. Par exemple, le dialecte de l’espagnol parlé au Mexique est « es-MX ». Il contient l’espagnol parent « es », qui n’est spécifique à aucun pays.

Imaginez que votre site reçoive une demande pour une ressource « Bienvenue » avec la culture « fr-CA ». Le système de localisation recherche les ressources suivantes, dans l’ordre, et sélectionne la première correspondance :

* *Welcome.fr-CA.resx*
* *Welcome.fr.resx*
* *Welcome.resx* (si `NeutralResourcesLanguage` est « fr-CA »)

Par exemple, si vous supprimez l’indicateur de culture « .fr » alors que la culture définie est le français, le fichier de ressources par défaut est lu et les chaînes sont localisées. Le gestionnaire de ressources désigne une ressource par défaut ou de secours pour les cas où rien ne correspond à la culture demandée. Si vous voulez simplement retourner la clé quand une ressource est manquante pour la culture demandée, vous ne devez pas avoir de fichier de ressources par défaut.

### <a name="generate-resource-files-with-visual-studio"></a>Générer des fichiers de ressources avec Visual Studio

Si vous créez un fichier de ressources dans Visual Studio sans culture dans le nom de fichier (par exemple, *Welcome.resx*), Visual Studio crée une classe C# avec une propriété pour chaque chaîne. Ce n’est généralement pas ce que vous souhaitez avec ASP.NET Core. Vous n’avez habituellement pas de fichier de ressources *.resx* par défaut (un fichier *.resx* sans nom de culture). Nous vous suggérons donc de créer le fichier *.resx* avec un nom de culture (par exemple, *Welcome.fr.resx*). Lorsque vous créez un fichier *.resx* avec un nom de culture, Visual Studio ne génère pas le fichier de classe.

### <a name="add-other-cultures"></a>Ajouter d’autres cultures

Chaque combinaison de langue et de culture (autres que la langue par défaut) nécessite un fichier de ressources unique. Vous créez des fichiers de ressources pour différentes cultures et différents paramètres régionaux en créant des fichiers de ressources dans lesquels les codes de langue ISO font partie du nom de fichier (par exemple, **en-us**, **fr-ca** et **en gb**). Ces codes ISO sont placés entre le nom de fichier et l’extension de fichier *.resx*, comme dans *Welcome.es-MX.resx* (Espagnol/Mexique).

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a>Implémenter une stratégie de sélection de la langue/culture pour chaque requête

### <a name="configure-localization"></a>Configurer la localisation

La localisation est configurée dans la méthode `Startup.ConfigureServices` :

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* `AddLocalization` Ajoute les services de localisation au conteneur de services. Le code ci-dessus affecte également au chemin des ressources la valeur « Resources ».

* `AddViewLocalization` Ajoute la prise en charge des fichiers de vue localisés. Dans cet exemple d’affichage, la localisation se base sur le suffixe du fichier d’affichage. Par exemple, « fr » dans le fichier *Index.fr.cshtml*.

* `AddDataAnnotationsLocalization` Ajoute la prise en charge des messages de validation localisés `DataAnnotations` via des `IStringLocalizer` abstractions.

### <a name="localization-middleware"></a>Intergiciel (middleware) de localisation

La culture actuelle sur une requête est définie dans l’[intergiciel (middleware)](xref:fundamentals/middleware/index) de localisation. L’intergiciel de localisation est activé dans la méthode `Startup.Configure`. L’intergiciel de localisation doit être configuré avant tout intergiciel susceptible de vérifier la culture de la requête (par exemple, `app.UseMvcWithDefaultRoute()`).

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

`UseRequestLocalization` initialise un objet `RequestLocalizationOptions`. Sur chaque requête, la liste de `RequestCultureProvider` dans `RequestLocalizationOptions` est énumérée et le premier fournisseur capable de déterminer correctement la culture de la requête est utilisé. Les fournisseurs par défaut proviennent de la classe `RequestLocalizationOptions` :

1. `QueryStringRequestCultureProvider`
1. `CookieRequestCultureProvider`
1. `AcceptLanguageHeaderRequestCultureProvider`

La liste par défaut va du plus spécifique au moins spécifique. Plus loin dans l’article, nous verrons comment vous pouvez modifier l’ordre et même ajouter un fournisseur de culture personnalisé. Si aucun des fournisseurs ne peut déterminer la culture de la requête, `DefaultRequestCulture` est utilisé.

### <a name="querystringrequestcultureprovider"></a>QueryStringRequestCultureProvider

Certaines applications utiliseront une chaîne de requête pour définir <https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo?view=netcore-3.1> . Pour les applications qui utilisent l' cookie approche d’en-tête ou Accept-Language, l’ajout d’une chaîne de requête à l’URL est utile pour déboguer et tester le code. Par défaut, `QueryStringRequestCultureProvider` est inscrit en tant que premier fournisseur de localisation dans la liste `RequestCultureProvider`. Vous passez les paramètres de chaîne de requête `culture` et `ui-culture`. L’exemple suivant affecte à la culture spécifique (langue et région) la valeur espagnol/Mexique :

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

Si vous passez uniquement l’une des deux (`culture` ou `ui-culture`), le fournisseur de chaîne de requête définit les deux valeurs à l’aide de celle que vous avez passée. Par exemple, la seule définition de la culture définit à la fois `Culture` et `UICulture` :

```
http://localhost:5000/?culture=es-MX
```

### <a name="no-loccookierequestcultureprovider"></a>CookieRequestCultureProvider

Les applications de production fournissent souvent un mécanisme pour définir la culture avec la culture ASP.NET Core cookie . Utilisez la `MakeCookieValue` méthode pour créer un cookie .

`CookieRequestCultureProvider` `DefaultCookieName` Retourne le nom par défaut cookie utilisé pour suivre les informations de culture préférées de l’utilisateur. Le nom par défaut cookie est `.AspNetCore.Culture` .

Le cookie format est `c=%LANGCODE%|uic=%LANGCODE%` , où `c` est `Culture` et `uic` est `UICulture` , par exemple :

```
c=en-UK|uic=en-US
```

Si vous spécifiez uniquement les informations de culture ou la culture d’interface utilisateur, la culture spécifiée est utilisée à la fois pour les informations de culture et la culture d’interface utilisateur.

### <a name="the-accept-language-http-header"></a>En-tête HTTP Accept-Language

L’[en-tête Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) peut être défini dans la plupart des navigateurs et a d’abord été conçu pour spécifier la langue de l’utilisateur. Ce paramètre indique ce que le navigateur doit envoyer ou ce dont il a hérité du système d’exploitation sous-jacent. L’en-tête HTTP Accept-Language d’une requête de navigateur n’est pas un moyen infaillible de détecter la langue préférée de l’utilisateur (consultez [Définition des préférences de langue dans un navigateur](https://www.w3.org/International/questions/qa-lang-priorities.en.php)). Une application de production doit inclure un moyen permettant à l’utilisateur de personnaliser son choix de culture.

### <a name="set-the-accept-language-http-header-in-ie"></a>Définir l’en-tête HTTP Accept-Language dans IE

1. À partir de l’icône d’engrenage, appuyez sur **Options Internet**.

1. Appuyez sur **Langues**.

   ![Options Internet](localization/_static/lang.png)

1. Appuyez sur **Définir les langues**.

1. Appuyez sur **Ajouter une langue**.

1. Ajoutez la langue.

1. Tapez sur la langue, puis sur **Monter**.

### <a name="use-a-custom-provider"></a>Utiliser un fournisseur personnalisé

Supposons que vous vouliez permettre à vos clients de stocker leur langue et leur culture dans vos bases de données. Vous pouvez écrire un fournisseur pour rechercher ces valeurs pour l’utilisateur. Le code suivant illustre l’ajout d’un fournisseur personnalisé :

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

Utilisez `RequestLocalizationOptions` pour ajouter ou supprimer des fournisseurs de localisation.

### <a name="set-the-culture-programmatically"></a>Définir la culture par programmation

Cet exemple de projet **Localization.StarterWeb** sur [GitHub](https://github.com/aspnet/entropy) contient l’interface utilisateur permettant de définir `Culture`. Le fichier *Views/Shared/_SelectLanguagePartial.cshtml* vous permet de sélectionner la culture dans la liste des cultures prises en charge :

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

Le fichier *Views/Shared/_SelectLanguagePartial.cshtml* est ajouté à la section `footer` du fichier de disposition afin d’être disponible pour tous les affichages :

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

La `SetLanguage` méthode définit la culture cookie .

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

Vous ne pouvez pas incorporer *_SelectLanguagePartial.cshtml* dans l’exemple de code de ce projet. Le projet **Localization. StarterWeb** sur [GitHub](https://github.com/aspnet/entropy) contient du code pour transmettre le `RequestLocalizationOptions` à un en Razor partiel via le conteneur d' [injection de dépendance](dependency-injection.md) .

## <a name="model-binding-route-data-and-query-strings"></a>Données d’itinéraire de liaison de modèle et chaînes de requête

Consultez [comportement de la globalisation des données de routage de liaison de modèle et des chaînes de requête](xref:mvc/models/model-binding#glob).

## <a name="globalization-and-localization-terms"></a>Terminologie de la globalisation et de la localisation

Le processus de localisation de votre application exige également une compréhension élémentaire des jeux de caractères appropriés couramment utilisés dans le développement de logiciels moderne ainsi qu’une compréhension des problèmes qui y sont associés. Bien que tous les ordinateurs stockent le texte sous forme de nombres (codes), les différents systèmes stockent ce même texte en utilisant des nombres différents. Le processus de localisation consiste à traduire l’interface utilisateur de l’application pour une culture/des paramètres régionaux spécifiques.

L’[adaptabilité](/dotnet/standard/globalization-localization/localizability-review) est un processus intermédiaire permettant de vérifier qu’une application globalisée est prête à être localisée.

Le format [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) du nom de la culture est `<languagecode2>-<country/regioncode2>`, où `<languagecode2>` correspond au code de la langue et `<country/regioncode2>` au code de la sous-culture. Par exemple, `es-CL` pour l’espagnol (Chili), `en-US` pour l’anglais (États-Unis) et `en-AU` pour l’anglais (Australie). Le format [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) est la combinaison d’un code de culture à deux lettres minuscules de norme ISO 639 associé à une langue et d’un code de sous-culture à deux lettres majuscules de norme ISO 3166 associé à un pays ou une région. Consultez <https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>.

L’internationalisation est souvent abrégée par « I18N ». Cette abréviation prend les première et dernière lettres du mot ainsi que le nombre de lettres entre elles, 18 représentant ainsi le nombre de lettres le « I » initial et le « N » final. Il en va de même pour la globalisation (G11N) et la localisation (L10N).

Termes :

* Globalisation (G11N) : Processus permettant de faire prendre en charge différentes langues et régions à une application.
* Localisation (L10N) : Processus permettant de personnaliser une application pour une langue et une région données.
* Internationalisation (I18N) : Décrit à la fois la globalisation et la localisation.
* Culture : Correspond à une langue et éventuellement à une région.
* Culture neutre : Culture dont la langue est spécifiée, mais pas la région. (Exemples : « en », « es ».)
* Culture spécifique : Culture dont la langue et la région sont spécifiées. (Exemples : « en-US », « en-GB », « es-CL ».)
* Culture parent : Culture neutre qui contient une culture spécifique. (Exemple : « en » est la culture parent de « en-US » et « en-GB ».)
* Paramètres régionaux : Synonyme de culture.

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* [Projet Localization.StarterWeb](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) utilisé dans l’article.
* [Internationalisation et localisation d’applications .NET](/dotnet/standard/globalization-localization/index)
* [Ressources dans les fichiers. resx](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [Kit de ressources pour application multilingue Microsoft](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [Localisation et classes génériques](http://hishambinateya.com/localization-and-generics)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/) et [Hisham Bin Ateya](https://twitter.com/hishambinateya)

Un site Web multilingue permet au site d’atteindre un public plus vaste. ASP.NET Core offre des services et des intergiciels (middleware) de traduction dans différentes langues et cultures.

L’internationalisation implique la [globalisation](/dotnet/api/system.globalization) et la [localisation](/dotnet/standard/globalization-localization/localization). La globalisation est le processus de conception d’applications qui prennent en charge des cultures différentes. La globalisation ajoute la prise en charge de l’entrée, de l’affichage et de la sortie d’un ensemble défini de jeux de caractères liés à des zones géographiques spécifiques.

La localisation est le processus d’adaptation d’une application globalisée, dont vous avez déjà traitée l’adaptabilité, à une culture/des paramètres régionaux spécifiques. Pour plus d’informations, consultez **Terminologie de la globalisation et de la localisation** à la fin du présent document.

La localisation d’une application implique les étapes suivantes :

1. Rendre le contenu de l’application localisable
1. Fournir des ressources localisées aux langues et cultures prises en charge
1. Implémenter une stratégie de sélection de la langue/culture pour chaque requête

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="make-the-apps-content-localizable"></a>Rendre le contenu de l’application localisable

<xref:Microsoft.Extensions.Localization.IStringLocalizer> et <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> ont été conçus pour améliorer la productivité lors du développement d’applications localisées. `IStringLocalizer` utilise <xref:System.Resources.ResourceManager> et <xref:System.Resources.ResourceReader> pour fournir des ressources spécifiques à la culture au moment de l’exécution. L’interface possède un indexeur et un `IEnumerable` pour retourner des chaînes localisées. `IStringLocalizer` ne nécessite pas le stockage des chaînes de langue par défaut dans un fichier de ressources. Vous pouvez développer une application ciblée pour la localisation sans avoir besoin de créer des fichiers de ressources au tout début du développement. Le code ci-dessous montre comment inclure dans un wrapper la chaîne « About Title » à des fins de localisation.

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

Dans le code précédent, l' `IStringLocalizer<T>` implémentation provient de l' [injection de dépendances](dependency-injection.md). Si la valeur localisée de « About Title » est introuvable, alors la clé de l’indexeur est retournée, autrement dit, la chaîne « About Title ». Vous pouvez laisser les chaînes littérales de la langue par défaut dans l’application et les inclure dans un wrapper dans le localisateur, afin de pouvoir vous concentrer sur le développement de l’application. Vous développez votre application avec votre langue par défaut et vous la préparez à l’étape de localisation sans créer au préalable un fichier de ressources par défaut. Vous pouvez également utiliser l’approche traditionnelle et fournir une clé pour récupérer la chaîne de la langue par défaut. Pour de nombreux développeurs, le nouveau workflow qui n’inclut pas de fichier *.resx* de langue par défaut et qui consiste à simplement inclure dans un wrapper les littéraux de chaîne permet de réduire la surcharge de la localisation d’une application. D’autres développeurs préfèrent le workflow traditionnel, car il facilite l’utilisation de longs littéraux de chaîne ainsi que la mise à jour des chaînes localisées.

Utilisez l’implémentation de `IHtmlLocalizer<T>` pour les ressources qui contiennent du HTML. `IHtmlLocalizer` encode en HTML les arguments formatés dans la chaîne de ressource, mais pas la chaîne de ressource elle-même. Dans l’exemple mis en surbrillance ci-dessous, seule la valeur du paramètre `name` est encodé en HTML.

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

> [!NOTE]
> En général, localisez uniquement le texte, et non le format HTML.

Au niveau le plus bas, vous pouvez sortir `IStringLocalizerFactory` de l’[injection de dépendances](dependency-injection.md) :

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

Le code ci-dessus illustre chacune des deux méthodes de création.

Vous pouvez partitionner vos chaînes localisées par contrôleur, par zone ou avoir un seul conteneur. Dans l’exemple d’application, une classe fictive nommée `SharedResource` est utilisée pour les ressources partagées.

[!code-csharp[](localization/sample/3.x/Localization/SharedResource.cs)]

Certains développeurs utilisent la classe `Startup` pour contenir des chaînes globales ou partagées. Dans l’exemple ci-dessous, les localiseurs `InfoController` et `SharedResource` sont utilisés :

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a>Localisation de l’affichage

Le service `IViewLocalizer` fournit des chaînes localisées à une [vue](xref:mvc/views/overview). La classe `ViewLocalizer` implémente cette interface et recherche l’emplacement de la ressource à partir du chemin du fichier de la vue. Le code suivant montre comment utiliser l’implémentation par défaut de `IViewLocalizer` :

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

L’implémentation par défaut de `IViewLocalizer` recherche le fichier de ressources en fonction du nom de fichier de l’affichage. Il n’existe aucune option pour utiliser un fichier de ressources partagées globales. `ViewLocalizer` implémente le localisateur à l’aide `IHtmlLocalizer` de, donc Razor n’encode pas le code HTML de la chaîne localisée. Vous pouvez paramétrer des chaînes de ressources pour que `IViewLocalizer` encode en HTML les paramètres, mais pas les chaînes de ressources. Examinez le Razor balisage suivant :

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

Un fichier de ressources en français peut contenir ce qui suit :

| Clé | Valeur |
| --- | ----- |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

L’affichage contient le balisage HTML provenant du fichier de ressources.

> [!NOTE]
> En général, localisez uniquement le texte, et non le format HTML.

Pour utiliser un fichier de ressources partagées dans un affichage, injectez `IHtmlLocalizer<T>` :

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a>Localisation de DataAnnotations

Les messages d’erreur DataAnnotations sont localisés avec `IStringLocalizer<T>`. En utilisant l’option `ResourcesPath = "Resources"`, les messages d’erreur inclus dans `RegisterViewModel` peuvent être stockés dans les chemins suivants :

* *Ressources/ViewModels. Account. RegisterViewModel. fr. resx*
* *Resources/ViewModels/Account/RegisterViewModel.fr.resx*

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

Dans ASP.NET Core MVC 1.1.0 et version supérieure, les attributs de non-validation sont localisés. ASP.NET Core MVC 1.0 ne recherche **pas** de chaînes localisées pour des attributs de non-validation.

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a>Utilisation d’une seule chaîne de ressource pour plusieurs classes

Le code suivant montre comment utiliser une seule chaîne de ressource pour les attributs de validation avec plusieurs classes :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

Dans le code précédent, `SharedResource` est la classe correspondant au resx où sont stockés vos messages de validation. Cette approche permet à DataAnnotations d’utiliser uniquement `SharedResource`, au lieu de la ressource de chaque classe.

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a>Fournir des ressources localisées aux langues et cultures prises en charge

### <a name="supportedcultures-and-supporteduicultures"></a>SupportedCultures et SupportedUICultures

ASP.NET Core vous permet de spécifier deux valeurs de culture, `SupportedCultures` et `SupportedUICultures`. L’objet [CultureInfo](/dotnet/api/system.globalization.cultureinfo) de `SupportedCultures` détermine les résultats des fonctions qui dépendent de la culture, comme la mise en forme des dates, de l’heure, des nombres et des devises. `SupportedCultures` détermine également l’ordre de tri du texte, les conventions de casse et les comparaisons de chaînes. Consultez [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) pour plus d’informations sur la façon dont le serveur obtient la culture. Le `SupportedUICultures` détermine les chaînes traduites (à partir des fichiers *. resx* ) qui sont recherchées par le [ResourceManager](/dotnet/api/system.resources.resourcemanager). `ResourceManager` recherche simplement les chaînes propres à la culture déterminée par `CurrentUICulture`. Chaque thread dans .NET a des objets `CurrentCulture` et `CurrentUICulture`. ASP.NET Core inspecte ces valeurs lors du rendu des fonctions qui dépendent de la culture. Par exemple, si la culture du thread actuel a la valeur « en-US » (anglais, États-Unis), `DateTime.Now.ToLongDateString()` affiche « Thursday, February 18, 2016 », mais si `CurrentCulture` a la valeur « es-ES » (espagnol, Espagne), la sortie est « jueves, 18 de febrero de 2016 ».

## <a name="resource-files"></a>Fichiers de ressources

Un fichier de ressources est un mécanisme utile pour séparer les chaînes localisables du code. Les chaînes traduites pour la langue non définie par défaut sont isolées dans les fichiers de ressources *. resx* . Par exemple, vous pouvez avoir besoin de créer un fichier de ressources nommé *Welcome.es.resx* qui contient des chaînes traduites. « es » correspond au code de langue pour l’espagnol. Pour créer ce fichier de ressources dans Visual Studio :

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier qui contient le fichier de ressources > **Ajouter** > **Nouvel élément**.

   ![Menu contextuel imbriqué : dans l’Explorateur de solutions, un menu contextuel est ouvert pour les ressources. Un second menu contextuel est ouvert pour l’option Ajouter, avec la commande Nouvel élément mise en surbrillance.](localization/_static/newi.png)

1. Dans la zone **Rechercher dans les modèles installés**, entrez « ressource » et nommez le fichier.

   ![Boîte de dialogue Ajouter un nouvel élément](localization/_static/res.png)

1. Entrez la valeur de la clé (chaîne native) dans la colonne **Nom** et la chaîne traduite dans la colonne **Valeur**.

   ![Le fichier Welcome.es.resx (le fichier de ressources Welcome pour l’espagnol) avec le mot Hello dans la colonne Nom et le mot Hola (bonjour en espagnol) dans la colonne Valeur](localization/_static/hola.png)

   Visual Studio présente le fichier *Welcome.es.resx*.

   ![L’Explorateur de solutions montrant le fichier de ressources Welcome pour l’espagnol (es)](localization/_static/se.png)

## <a name="resource-file-naming"></a>Nommage du fichier de ressources

Les ressources sont nommées selon le nom de type complet de leur classe moins le nom de l’assembly. Par exemple, une ressource en français dans un projet dont l’assembly principal est `LocalizationWebsite.Web.dll` pour la classe `LocalizationWebsite.Web.Startup` serait nommée *Startup.fr.resx*. Une ressource pour la classe `LocalizationWebsite.Web.Controllers.HomeController` serait nommée *Controllers.HomeController.fr.resx*. Si l’espace de noms de votre classe ciblée n’est pas identique au nom de l’assembly, vous aurez besoin du nom de type complet. Par exemple, dans l’exemple de projet, une ressource pour le type `ExtraNamespace.Tools` serait nommée *ExtraNamespace.Tools.fr.resx*.

Dans l’exemple de projet, la méthode `ConfigureServices` affecte à `ResourcesPath` la valeur « Resources », si bien que le chemin relatif du projet pour le fichier de ressources en français du contrôleur Home est *Resources/Controllers.HomeController.fr.resx*. Vous pouvez également utiliser des dossiers pour organiser les fichiers de ressources. Pour le contrôleur Home, le chemin serait *Resources/Controllers/HomeController.fr.resx*. Si vous n’utilisez pas l’option `ResourcesPath`, le fichier *.resx* se trouve dans le répertoire de base du projet. Le fichier de ressources pour `HomeController` serait nommé *Controllers.HomeController.fr.resx*. Le choix de l’utilisation de la convention d’affectation de noms avec des points ou un chemin dépend de la façon dont vous souhaitez organiser vos fichiers de ressources.

| Nom de la ressource | Affectation de noms avec des points ou un chemin |
| ------------   | ------------- |
| Resources/Controllers.HomeController.fr.resx | Points  |
| Resources/Controllers/HomeController.fr.resx  | Chemin d’accès |

Les fichiers de ressources utilisant `@inject IViewLocalizer` dans les Razor vues suivent un modèle similaire. Le fichier de ressources d’une vue peut être nommé selon la convention avec des points ou un chemin. Razor les fichiers de ressources d’affichage imitent le chemin d’accès de leur fichier de vue associé. Si nous affectons à `ResourcesPath` la valeur « Resources », le fichier de ressources en français associé à l’affichage *Views/Home/About.cshtml* peut porter l’un des noms suivants :

* Resources/Views/Home/About.fr.resx

* Resources/Views.Home.About.fr.resx

Si vous n’utilisez pas l’option `ResourcesPath`, le fichier *.resx* d’un affichage se trouve dans le même dossier que l’affichage.

### <a name="rootnamespaceattribute"></a>RootNamespaceAttribute 

L' <xref:Microsoft.Extensions.Localization.RootNamespaceAttribute> attribut fournit l’espace de noms racine d’un assembly lorsque l’espace de noms racine d’un assembly est différent du nom de l’assembly. 

> [!WARNING]
> Cela peut se produire lorsque le nom d’un projet n’est pas un identificateur .NET valide. Par exemple, `my-project-name.csproj` utilisera l’espace de noms racine `my_project_name` et le nom de l’assembly `my-project-name` menant à cette erreur. 

Si l’espace de noms racine d’un assembly est différent du nom de l’assembly :

* La localisation ne fonctionne pas par défaut.
* La localisation échoue en raison du mode de recherche des ressources dans l’assembly. `RootNamespace` est une valeur de build qui n’est pas accessible au processus exécutant. 

Si `RootNamespace` est différent de `AssemblyName`, incluez ce qui suit dans *AssemblyInfo.cs* (en remplaçant les valeurs des paramètres par les valeurs réelles) :

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

Le code précédent permet de résoudre correctement les fichiers resx.

## <a name="culture-fallback-behavior"></a>Comportement de secours pour la culture

Lors de la recherche d’une ressource, la localisation met en œuvre une « alternative de secours pour la culture ». Elle commence par la culture demandée : si elle est introuvable, il revient à la culture parente de cette culture. Par ailleurs, la propriété [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) représente la culture parente. Cela signifie généralement (mais pas toujours) supprimer l’indicateur national du code ISO. Par exemple, le dialecte de l’espagnol parlé au Mexique est « es-MX ». Il contient l’espagnol parent « es », qui n’est spécifique à aucun pays.

Imaginez que votre site reçoive une demande pour une ressource « Bienvenue » avec la culture « fr-CA ». Le système de localisation recherche les ressources suivantes, dans l’ordre, et sélectionne la première correspondance :

* *Welcome.fr-CA.resx*
* *Welcome.fr.resx*
* *Welcome.resx* (si `NeutralResourcesLanguage` est « fr-CA »)

Par exemple, si vous supprimez l’indicateur de culture « .fr » alors que la culture définie est le français, le fichier de ressources par défaut est lu et les chaînes sont localisées. Le gestionnaire de ressources désigne une ressource par défaut ou de secours pour les cas où rien ne correspond à la culture demandée. Si vous voulez simplement retourner la clé quand une ressource est manquante pour la culture demandée, vous ne devez pas avoir de fichier de ressources par défaut.

### <a name="generate-resource-files-with-visual-studio"></a>Générer des fichiers de ressources avec Visual Studio

Si vous créez un fichier de ressources dans Visual Studio sans culture dans le nom de fichier (par exemple, *Welcome.resx*), Visual Studio crée une classe C# avec une propriété pour chaque chaîne. Ce n’est généralement pas ce que vous souhaitez avec ASP.NET Core. Vous n’avez habituellement pas de fichier de ressources *.resx* par défaut (un fichier *.resx* sans nom de culture). Nous vous suggérons donc de créer le fichier *.resx* avec un nom de culture (par exemple, *Welcome.fr.resx*). Lorsque vous créez un fichier *.resx* avec un nom de culture, Visual Studio ne génère pas le fichier de classe.

### <a name="add-other-cultures"></a>Ajouter d’autres cultures

Chaque combinaison de langue et de culture (autres que la langue par défaut) nécessite un fichier de ressources unique. Vous créez des fichiers de ressources pour différentes cultures et différents paramètres régionaux en créant des fichiers de ressources dans lesquels les codes de langue ISO font partie du nom de fichier (par exemple, **en-us**, **fr-ca** et **en gb**). Ces codes ISO sont placés entre le nom de fichier et l’extension de fichier *.resx*, comme dans *Welcome.es-MX.resx* (Espagnol/Mexique).

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a>Implémenter une stratégie de sélection de la langue/culture pour chaque requête

### <a name="configure-localization"></a>Configurer la localisation

La localisation est configurée dans la méthode `Startup.ConfigureServices` :

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* `AddLocalization` Ajoute les services de localisation au conteneur de services. Le code ci-dessus affecte également au chemin des ressources la valeur « Resources ».

* `AddViewLocalization` Ajoute la prise en charge des fichiers de vue localisés. Dans cet exemple d’affichage, la localisation se base sur le suffixe du fichier d’affichage. Par exemple, « fr » dans le fichier *Index.fr.cshtml*.

* `AddDataAnnotationsLocalization` Ajoute la prise en charge des messages de validation localisés `DataAnnotations` via des `IStringLocalizer` abstractions.

### <a name="localization-middleware"></a>Intergiciel (middleware) de localisation

La culture actuelle sur une requête est définie dans l’[intergiciel (middleware)](xref:fundamentals/middleware/index) de localisation. L’intergiciel de localisation est activé dans la méthode `Startup.Configure`. L’intergiciel de localisation doit être configuré avant tout intergiciel susceptible de vérifier la culture de la requête (par exemple, `app.UseMvcWithDefaultRoute()`).

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

`UseRequestLocalization` initialise un objet `RequestLocalizationOptions`. Sur chaque requête, la liste de `RequestCultureProvider` dans `RequestLocalizationOptions` est énumérée et le premier fournisseur capable de déterminer correctement la culture de la requête est utilisé. Les fournisseurs par défaut proviennent de la classe `RequestLocalizationOptions` :

1. `QueryStringRequestCultureProvider`
1. `CookieRequestCultureProvider`
1. `AcceptLanguageHeaderRequestCultureProvider`

La liste par défaut va du plus spécifique au moins spécifique. Plus loin dans l’article, nous verrons comment vous pouvez modifier l’ordre et même ajouter un fournisseur de culture personnalisé. Si aucun des fournisseurs ne peut déterminer la culture de la requête, `DefaultRequestCulture` est utilisé.

### <a name="querystringrequestcultureprovider"></a>QueryStringRequestCultureProvider

Certaines applications utiliseront une chaîne de requête pour définir <https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo?view=netcore-3.1> . Pour les applications qui utilisent l' cookie approche d’en-tête ou Accept-Language, l’ajout d’une chaîne de requête à l’URL est utile pour déboguer et tester le code. Par défaut, `QueryStringRequestCultureProvider` est inscrit en tant que premier fournisseur de localisation dans la liste `RequestCultureProvider`. Vous passez les paramètres de chaîne de requête `culture` et `ui-culture`. L’exemple suivant affecte à la culture spécifique (langue et région) la valeur espagnol/Mexique :

```
http://localhost:5000/?culture=es-MX&ui-culture=es-MX
```

Si vous passez uniquement l’une des deux (`culture` ou `ui-culture`), le fournisseur de chaîne de requête définit les deux valeurs à l’aide de celle que vous avez passée. Par exemple, la seule définition de la culture définit à la fois `Culture` et `UICulture` :

```
http://localhost:5000/?culture=es-MX
```

### <a name="no-loccookierequestcultureprovider"></a>CookieRequestCultureProvider

Les applications de production fournissent souvent un mécanisme pour définir la culture avec la culture ASP.NET Core cookie . Utilisez la `MakeCookieValue` méthode pour créer un cookie .

`CookieRequestCultureProvider` `DefaultCookieName` Retourne le nom par défaut cookie utilisé pour suivre les informations de culture préférées de l’utilisateur. Le nom par défaut cookie est `.AspNetCore.Culture` .

Le cookie format est `c=%LANGCODE%|uic=%LANGCODE%` , où `c` est `Culture` et `uic` est `UICulture` , par exemple :

```
c=en-UK|uic=en-US
```

Si vous spécifiez uniquement les informations de culture ou la culture d’interface utilisateur, la culture spécifiée est utilisée à la fois pour les informations de culture et la culture d’interface utilisateur.

### <a name="the-accept-language-http-header"></a>En-tête HTTP Accept-Language

L’[en-tête Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) peut être défini dans la plupart des navigateurs et a d’abord été conçu pour spécifier la langue de l’utilisateur. Ce paramètre indique ce que le navigateur doit envoyer ou ce dont il a hérité du système d’exploitation sous-jacent. L’en-tête HTTP Accept-Language d’une requête de navigateur n’est pas un moyen infaillible de détecter la langue préférée de l’utilisateur (consultez [Définition des préférences de langue dans un navigateur](https://www.w3.org/International/questions/qa-lang-priorities.en.php)). Une application de production doit inclure un moyen permettant à l’utilisateur de personnaliser son choix de culture.

### <a name="set-the-accept-language-http-header-in-ie"></a>Définir l’en-tête HTTP Accept-Language dans IE

1. À partir de l’icône d’engrenage, appuyez sur **Options Internet**.

1. Appuyez sur **Langues**.

   ![Options Internet](localization/_static/lang.png)

1. Appuyez sur **Définir les langues**.

1. Appuyez sur **Ajouter une langue**.

1. Ajoutez la langue.

1. Tapez sur la langue, puis sur **Monter**.

### <a name="use-a-custom-provider"></a>Utiliser un fournisseur personnalisé

Supposons que vous vouliez permettre à vos clients de stocker leur langue et leur culture dans vos bases de données. Vous pouvez écrire un fournisseur pour rechercher ces valeurs pour l’utilisateur. Le code suivant illustre l’ajout d’un fournisseur personnalisé :

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

Utilisez `RequestLocalizationOptions` pour ajouter ou supprimer des fournisseurs de localisation.

### <a name="set-the-culture-programmatically"></a>Définir la culture par programmation

Cet exemple de projet **Localization.StarterWeb** sur [GitHub](https://github.com/aspnet/entropy) contient l’interface utilisateur permettant de définir `Culture`. Le fichier *Views/Shared/_SelectLanguagePartial.cshtml* vous permet de sélectionner la culture dans la liste des cultures prises en charge :

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

Le fichier *Views/Shared/_SelectLanguagePartial.cshtml* est ajouté à la section `footer` du fichier de disposition afin d’être disponible pour tous les affichages :

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

La `SetLanguage` méthode définit la culture cookie .

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

Vous ne pouvez pas incorporer *_SelectLanguagePartial.cshtml* dans l’exemple de code de ce projet. Le projet **Localization. StarterWeb** sur [GitHub](https://github.com/aspnet/entropy) contient du code pour transmettre le `RequestLocalizationOptions` à un en Razor partiel via le conteneur d' [injection de dépendance](dependency-injection.md) .

## <a name="model-binding-route-data-and-query-strings"></a>Données d’itinéraire de liaison de modèle et chaînes de requête

Consultez [comportement de la globalisation des données de routage de liaison de modèle et des chaînes de requête](xref:mvc/models/model-binding#glob).

## <a name="globalization-and-localization-terms"></a>Terminologie de la globalisation et de la localisation

Le processus de localisation de votre application exige également une compréhension élémentaire des jeux de caractères appropriés couramment utilisés dans le développement de logiciels moderne ainsi qu’une compréhension des problèmes qui y sont associés. Bien que tous les ordinateurs stockent le texte sous forme de nombres (codes), les différents systèmes stockent ce même texte en utilisant des nombres différents. Le processus de localisation consiste à traduire l’interface utilisateur de l’application pour une culture/des paramètres régionaux spécifiques.

L’[adaptabilité](/dotnet/standard/globalization-localization/localizability-review) est un processus intermédiaire permettant de vérifier qu’une application globalisée est prête à être localisée.

Le format [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) du nom de la culture est `<languagecode2>-<country/regioncode2>`, où `<languagecode2>` correspond au code de la langue et `<country/regioncode2>` au code de la sous-culture. Par exemple, `es-CL` pour l’espagnol (Chili), `en-US` pour l’anglais (États-Unis) et `en-AU` pour l’anglais (Australie). Le format [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) est la combinaison d’un code de culture à deux lettres minuscules de norme ISO 639 associé à une langue et d’un code de sous-culture à deux lettres majuscules de norme ISO 3166 associé à un pays ou une région. Consultez <https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>.

L’internationalisation est souvent abrégée par « I18N ». Cette abréviation prend les première et dernière lettres du mot ainsi que le nombre de lettres entre elles, 18 représentant ainsi le nombre de lettres le « I » initial et le « N » final. Il en va de même pour la globalisation (G11N) et la localisation (L10N).

Termes :

* Globalisation (G11N) : Processus permettant de faire prendre en charge différentes langues et régions à une application.
* Localisation (L10N) : Processus permettant de personnaliser une application pour une langue et une région données.
* Internationalisation (I18N) : Décrit à la fois la globalisation et la localisation.
* Culture : Correspond à une langue et éventuellement à une région.
* Culture neutre : Culture dont la langue est spécifiée, mais pas la région. (Exemples : « en », « es ».)
* Culture spécifique : Culture dont la langue et la région sont spécifiées. (Exemples : « en-US », « en-GB », « es-CL ».)
* Culture parent : Culture neutre qui contient une culture spécifique. (Exemple : « en » est la culture parent de « en-US » et « en-GB ».)
* Paramètres régionaux : Synonyme de culture.

[!INCLUDE[](~/includes/localization/currency.md)]

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* [Projet Localization.StarterWeb](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) utilisé dans l’article.
* [Internationalisation et localisation d’applications .NET](/dotnet/standard/globalization-localization/index)
* [Ressources dans les fichiers. resx](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [Kit de ressources pour application multilingue Microsoft](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [Localisation et classes génériques](http://hishambinateya.com/localization-and-generics)

::: moniker-end

<!-- ASP.NET Core 5.x starts here -->
::: moniker range="> aspnetcore-3.1"

Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/) et [Hisham Bin Ateya](https://twitter.com/hishambinateya)

Un site Web multilingue permet au site d’atteindre un public plus vaste. ASP.NET Core offre des services et des intergiciels (middleware) de traduction dans différentes langues et cultures.

L’internationalisation implique la [globalisation](/dotnet/api/system.globalization) et la [localisation](/dotnet/standard/globalization-localization/localization). La globalisation est le processus de conception d’applications qui prennent en charge des cultures différentes. La globalisation ajoute la prise en charge de l’entrée, de l’affichage et de la sortie d’un ensemble défini de jeux de caractères liés à des zones géographiques spécifiques.

La localisation est le processus d’adaptation d’une application globalisée, dont vous avez déjà traitée l’adaptabilité, à une culture/des paramètres régionaux spécifiques. Pour plus d’informations, consultez **Terminologie de la globalisation et de la localisation** à la fin du présent document.

La localisation d’une application implique les étapes suivantes :

1. Rendre le contenu de l’application localisable
1. Fournir des ressources localisées aux langues et cultures prises en charge
1. Implémenter une stratégie de sélection de la langue/culture pour chaque requête

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/2.x/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="make-the-apps-content-localizable"></a>Rendre le contenu de l’application localisable

<xref:Microsoft.Extensions.Localization.IStringLocalizer> et <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> ont été conçus pour améliorer la productivité lors du développement d’applications localisées. `IStringLocalizer` utilise <xref:System.Resources.ResourceManager> et <xref:System.Resources.ResourceReader> pour fournir des ressources spécifiques à la culture au moment de l’exécution. L’interface possède un indexeur et un `IEnumerable` pour retourner des chaînes localisées. `IStringLocalizer` ne nécessite pas le stockage des chaînes de langue par défaut dans un fichier de ressources. Vous pouvez développer une application ciblée pour la localisation sans avoir besoin de créer des fichiers de ressources au tout début du développement. Le code ci-dessous montre comment inclure dans un wrapper la chaîne « About Title » à des fins de localisation.

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

Dans le code précédent, l' `IStringLocalizer<T>` implémentation provient de l' [injection de dépendances](dependency-injection.md). Si la valeur localisée de « About Title » est introuvable, alors la clé de l’indexeur est retournée, autrement dit, la chaîne « About Title ». Vous pouvez laisser les chaînes littérales de la langue par défaut dans l’application et les inclure dans un wrapper dans le localisateur, afin de pouvoir vous concentrer sur le développement de l’application. Vous développez votre application avec votre langue par défaut et vous la préparez à l’étape de localisation sans créer au préalable un fichier de ressources par défaut. Vous pouvez également utiliser l’approche traditionnelle et fournir une clé pour récupérer la chaîne de la langue par défaut. Pour de nombreux développeurs, le nouveau workflow qui n’inclut pas de fichier *.resx* de langue par défaut et qui consiste à simplement inclure dans un wrapper les littéraux de chaîne permet de réduire la surcharge de la localisation d’une application. D’autres développeurs préfèrent le workflow traditionnel, car il facilite l’utilisation de longs littéraux de chaîne ainsi que la mise à jour des chaînes localisées.

Utilisez l’implémentation de `IHtmlLocalizer<T>` pour les ressources qui contiennent du HTML. `IHtmlLocalizer` encode en HTML les arguments formatés dans la chaîne de ressource, mais pas la chaîne de ressource elle-même. Dans l’exemple mis en surbrillance ci-dessous, seule la valeur du paramètre `name` est encodé en HTML.

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

> [!NOTE]
> En général, localisez uniquement le texte, et non le format HTML.

Au niveau le plus bas, vous pouvez sortir `IStringLocalizerFactory` de l’[injection de dépendances](dependency-injection.md) :

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

Le code ci-dessus illustre chacune des deux méthodes de création.

Vous pouvez partitionner vos chaînes localisées par contrôleur, par zone ou avoir un seul conteneur. Dans l’exemple d’application, une classe fictive nommée `SharedResource` est utilisée pour les ressources partagées.

[!code-csharp[](localization/sample/3.x/Localization/SharedResource.cs)]

Certains développeurs utilisent la classe `Startup` pour contenir des chaînes globales ou partagées. Dans l’exemple ci-dessous, les localiseurs `InfoController` et `SharedResource` sont utilisés :

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a>Localisation de l’affichage

Le service `IViewLocalizer` fournit des chaînes localisées à une [vue](xref:mvc/views/overview). La classe `ViewLocalizer` implémente cette interface et recherche l’emplacement de la ressource à partir du chemin du fichier de la vue. Le code suivant montre comment utiliser l’implémentation par défaut de `IViewLocalizer` :

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

L’implémentation par défaut de `IViewLocalizer` recherche le fichier de ressources en fonction du nom de fichier de l’affichage. Il n’existe aucune option pour utiliser un fichier de ressources partagées globales. `ViewLocalizer` implémente le localisateur à l’aide `IHtmlLocalizer` de, donc Razor n’encode pas le code HTML de la chaîne localisée. Vous pouvez paramétrer des chaînes de ressources pour que `IViewLocalizer` encode en HTML les paramètres, mais pas les chaînes de ressources. Examinez le Razor balisage suivant :

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

Un fichier de ressources en français peut contenir ce qui suit :

| Clé | Valeur |
| --- | ----- |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

L’affichage contient le balisage HTML provenant du fichier de ressources.

> [!NOTE]
> En général, localisez uniquement le texte, et non le format HTML.

Pour utiliser un fichier de ressources partagées dans un affichage, injectez `IHtmlLocalizer<T>` :

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a>Localisation de DataAnnotations

Les messages d’erreur DataAnnotations sont localisés avec `IStringLocalizer<T>`. En utilisant l’option `ResourcesPath = "Resources"`, les messages d’erreur inclus dans `RegisterViewModel` peuvent être stockés dans les chemins suivants :

* *Ressources/ViewModels. Account. RegisterViewModel. fr. resx*
* *Resources/ViewModels/Account/RegisterViewModel.fr.resx*

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

Dans ASP.NET Core MVC 1.1.0 et version supérieure, les attributs de non-validation sont localisés. ASP.NET Core MVC 1.0 ne recherche **pas** de chaînes localisées pour des attributs de non-validation.

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a>Utilisation d’une seule chaîne de ressource pour plusieurs classes

Le code suivant montre comment utiliser une seule chaîne de ressource pour les attributs de validation avec plusieurs classes :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

Dans le code précédent, `SharedResource` est la classe correspondant au resx où sont stockés vos messages de validation. Cette approche permet à DataAnnotations d’utiliser uniquement `SharedResource`, au lieu de la ressource de chaque classe.

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a>Fournir des ressources localisées aux langues et cultures prises en charge

### <a name="supportedcultures-and-supporteduicultures"></a>SupportedCultures et SupportedUICultures

ASP.NET Core vous permet de spécifier deux valeurs de culture, `SupportedCultures` et `SupportedUICultures`. L’objet [CultureInfo](/dotnet/api/system.globalization.cultureinfo) de `SupportedCultures` détermine les résultats des fonctions qui dépendent de la culture, comme la mise en forme des dates, de l’heure, des nombres et des devises. `SupportedCultures` détermine également l’ordre de tri du texte, les conventions de casse et les comparaisons de chaînes. Consultez [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) pour plus d’informations sur la façon dont le serveur obtient la culture. Le `SupportedUICultures` détermine les chaînes traduites (à partir des fichiers *. resx* ) qui sont recherchées par le [ResourceManager](/dotnet/api/system.resources.resourcemanager). `ResourceManager` recherche simplement les chaînes propres à la culture déterminée par `CurrentUICulture`. Chaque thread dans .NET a des objets `CurrentCulture` et `CurrentUICulture`. ASP.NET Core inspecte ces valeurs lors du rendu des fonctions qui dépendent de la culture. Par exemple, si la culture du thread actuel a la valeur « en-US » (anglais, États-Unis), `DateTime.Now.ToLongDateString()` affiche « Thursday, February 18, 2016 », mais si `CurrentCulture` a la valeur « es-ES » (espagnol, Espagne), la sortie est « jueves, 18 de febrero de 2016 ».

## <a name="resource-files"></a>Fichiers de ressources

Un fichier de ressources est un mécanisme utile pour séparer les chaînes localisables du code. Les chaînes traduites pour la langue non définie par défaut sont isolées dans les fichiers de ressources *. resx* . Par exemple, vous pouvez avoir besoin de créer un fichier de ressources nommé *Welcome.es.resx* qui contient des chaînes traduites. « es » correspond au code de langue pour l’espagnol. Pour créer ce fichier de ressources dans Visual Studio :

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier qui contient le fichier de ressources > **Ajouter** > **Nouvel élément**.

   ![Menu contextuel imbriqué : dans l’Explorateur de solutions, un menu contextuel est ouvert pour les ressources. Un second menu contextuel est ouvert pour l’option Ajouter, avec la commande Nouvel élément mise en surbrillance.](localization/_static/newi.png)

1. Dans la zone **Rechercher dans les modèles installés**, entrez « ressource » et nommez le fichier.

   ![Boîte de dialogue Ajouter un nouvel élément](localization/_static/res.png)

1. Entrez la valeur de la clé (chaîne native) dans la colonne **Nom** et la chaîne traduite dans la colonne **Valeur**.

   ![Le fichier Welcome.es.resx (le fichier de ressources Welcome pour l’espagnol) avec le mot Hello dans la colonne Nom et le mot Hola (bonjour en espagnol) dans la colonne Valeur](localization/_static/hola.png)

   Visual Studio présente le fichier *Welcome.es.resx*.

   ![L’Explorateur de solutions montrant le fichier de ressources Welcome pour l’espagnol (es)](localization/_static/se.png)

## <a name="resource-file-naming"></a>Nommage du fichier de ressources

Les ressources sont nommées selon le nom de type complet de leur classe moins le nom de l’assembly. Par exemple, une ressource en français dans un projet dont l’assembly principal est `LocalizationWebsite.Web.dll` pour la classe `LocalizationWebsite.Web.Startup` serait nommée *Startup.fr.resx*. Une ressource pour la classe `LocalizationWebsite.Web.Controllers.HomeController` serait nommée *Controllers.HomeController.fr.resx*. Si l’espace de noms de votre classe ciblée n’est pas identique au nom de l’assembly, vous aurez besoin du nom de type complet. Par exemple, dans l’exemple de projet, une ressource pour le type `ExtraNamespace.Tools` serait nommée *ExtraNamespace.Tools.fr.resx*.

Dans l’exemple de projet, la méthode `ConfigureServices` affecte à `ResourcesPath` la valeur « Resources », si bien que le chemin relatif du projet pour le fichier de ressources en français du contrôleur Home est *Resources/Controllers.HomeController.fr.resx*. Vous pouvez également utiliser des dossiers pour organiser les fichiers de ressources. Pour le contrôleur Home, le chemin serait *Resources/Controllers/HomeController.fr.resx*. Si vous n’utilisez pas l’option `ResourcesPath`, le fichier *.resx* se trouve dans le répertoire de base du projet. Le fichier de ressources pour `HomeController` serait nommé *Controllers.HomeController.fr.resx*. Le choix de l’utilisation de la convention d’affectation de noms avec des points ou un chemin dépend de la façon dont vous souhaitez organiser vos fichiers de ressources.

| Nom de la ressource | Affectation de noms avec des points ou un chemin |
| ------------   | ------------- |
| Resources/Controllers.HomeController.fr.resx | Points  |
| Resources/Controllers/HomeController.fr.resx  | Chemin d’accès |

Les fichiers de ressources utilisant `@inject IViewLocalizer` dans les Razor vues suivent un modèle similaire. Le fichier de ressources d’une vue peut être nommé selon la convention avec des points ou un chemin. Razor les fichiers de ressources d’affichage imitent le chemin d’accès de leur fichier de vue associé. Si nous affectons à `ResourcesPath` la valeur « Resources », le fichier de ressources en français associé à l’affichage *Views/Home/About.cshtml* peut porter l’un des noms suivants :

* Resources/Views/Home/About.fr.resx

* Resources/Views.Home.About.fr.resx

Si vous n’utilisez pas l’option `ResourcesPath`, le fichier *.resx* d’un affichage se trouve dans le même dossier que l’affichage.

### <a name="rootnamespaceattribute"></a>RootNamespaceAttribute 

L' <xref:Microsoft.Extensions.Localization.RootNamespaceAttribute> attribut fournit l’espace de noms racine d’un assembly lorsque l’espace de noms racine d’un assembly est différent du nom de l’assembly. 

> [!WARNING]
> Cela peut se produire lorsque le nom d’un projet n’est pas un identificateur .NET valide. Par exemple, `my-project-name.csproj` utilisera l’espace de noms racine `my_project_name` et le nom de l’assembly `my-project-name` menant à cette erreur. 

Si l’espace de noms racine d’un assembly est différent du nom de l’assembly :

* La localisation ne fonctionne pas par défaut.
* La localisation échoue en raison du mode de recherche des ressources dans l’assembly. `RootNamespace` est une valeur de build qui n’est pas accessible au processus exécutant. 

Si `RootNamespace` est différent de `AssemblyName`, incluez ce qui suit dans *AssemblyInfo.cs* (en remplaçant les valeurs des paramètres par les valeurs réelles) :

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

Le code précédent permet de résoudre correctement les fichiers resx.

## <a name="culture-fallback-behavior"></a>Comportement de secours pour la culture

Lors de la recherche d’une ressource, la localisation met en œuvre une « alternative de secours pour la culture ». Elle commence par la culture demandée : si elle est introuvable, il revient à la culture parente de cette culture. Par ailleurs, la propriété [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) représente la culture parente. Cela signifie généralement (mais pas toujours) supprimer l’indicateur national du code ISO. Par exemple, le dialecte de l’espagnol parlé au Mexique est « es-MX ». Il contient l’espagnol parent « es », qui n’est spécifique à aucun pays.

Imaginez que votre site reçoive une demande pour une ressource « Bienvenue » avec la culture « fr-CA ». Le système de localisation recherche les ressources suivantes, dans l’ordre, et sélectionne la première correspondance :

* *Welcome.fr-CA.resx*
* *Welcome.fr.resx*
* *Welcome.resx* (si `NeutralResourcesLanguage` est « fr-CA »)

Par exemple, si vous supprimez l’indicateur de culture « .fr » alors que la culture définie est le français, le fichier de ressources par défaut est lu et les chaînes sont localisées. Le gestionnaire de ressources désigne une ressource par défaut ou de secours pour les cas où rien ne correspond à la culture demandée. Si vous voulez simplement retourner la clé quand une ressource est manquante pour la culture demandée, vous ne devez pas avoir de fichier de ressources par défaut.

### <a name="generate-resource-files-with-visual-studio"></a>Générer des fichiers de ressources avec Visual Studio

Si vous créez un fichier de ressources dans Visual Studio sans culture dans le nom de fichier (par exemple, *Welcome.resx*), Visual Studio crée une classe C# avec une propriété pour chaque chaîne. Ce n’est généralement pas ce que vous souhaitez avec ASP.NET Core. Vous n’avez habituellement pas de fichier de ressources *.resx* par défaut (un fichier *.resx* sans nom de culture). Nous vous suggérons donc de créer le fichier *.resx* avec un nom de culture (par exemple, *Welcome.fr.resx*). Lorsque vous créez un fichier *.resx* avec un nom de culture, Visual Studio ne génère pas le fichier de classe.

### <a name="add-other-cultures"></a>Ajouter d’autres cultures

Chaque combinaison de langue et de culture (autres que la langue par défaut) nécessite un fichier de ressources unique. Vous créez des fichiers de ressources pour différentes cultures et différents paramètres régionaux en créant des fichiers de ressources dans lesquels les codes de langue ISO font partie du nom de fichier (par exemple, **en-us**, **fr-ca** et **en gb**). Ces codes ISO sont placés entre le nom de fichier et l’extension de fichier *.resx*, comme dans *Welcome.es-MX.resx* (Espagnol/Mexique).

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a>Implémenter une stratégie de sélection de la langue/culture pour chaque requête

### <a name="configure-localization"></a>Configurer la localisation

La localisation est configurée dans la méthode `Startup.ConfigureServices` :

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* `AddLocalization` Ajoute les services de localisation au conteneur de services. Le code ci-dessus affecte également au chemin des ressources la valeur « Resources ».

* `AddViewLocalization` Ajoute la prise en charge des fichiers de vue localisés. Dans cet exemple d’affichage, la localisation se base sur le suffixe du fichier d’affichage. Par exemple, « fr » dans le fichier *Index.fr.cshtml*.

* `AddDataAnnotationsLocalization` Ajoute la prise en charge des messages de validation localisés `DataAnnotations` via des `IStringLocalizer` abstractions.

### <a name="localization-middleware"></a>Intergiciel (middleware) de localisation

La culture actuelle sur une requête est définie dans l’[intergiciel (middleware)](xref:fundamentals/middleware/index) de localisation. L’intergiciel de localisation est activé dans la méthode `Startup.Configure`. L’intergiciel de localisation doit être configuré avant tout intergiciel susceptible de vérifier la culture de la requête (par exemple, `app.UseMvcWithDefaultRoute()`).

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

`UseRequestLocalization` initialise un objet `RequestLocalizationOptions`. Sur chaque requête, la liste de `RequestCultureProvider` dans `RequestLocalizationOptions` est énumérée et le premier fournisseur capable de déterminer correctement la culture de la requête est utilisé. Les fournisseurs par défaut proviennent de la classe `RequestLocalizationOptions` :

1. `QueryStringRequestCultureProvider`
1. `CookieRequestCultureProvider`
1. `AcceptLanguageHeaderRequestCultureProvider`

La liste par défaut va du plus spécifique au moins spécifique. Plus loin dans l’article, nous verrons comment vous pouvez modifier l’ordre et même ajouter un fournisseur de culture personnalisé. Si aucun des fournisseurs ne peut déterminer la culture de la requête, `DefaultRequestCulture` est utilisé.

### <a name="querystringrequestcultureprovider"></a>QueryStringRequestCultureProvider

Certaines applications utiliseront une chaîne de requête pour définir <xref:System.Globalization.CultureInfo> . Pour les applications qui utilisent l' cookie approche d’en-tête ou Accept-Language, l’ajout d’une chaîne de requête à l’URL est utile pour déboguer et tester le code. Par défaut, `QueryStringRequestCultureProvider` est inscrit en tant que premier fournisseur de localisation dans la liste `RequestCultureProvider`. Vous passez les paramètres de chaîne de requête `culture` et `ui-culture`. L’exemple suivant affecte à la culture spécifique (langue et région) la valeur espagnol/Mexique :

```
http://localhost:5000/?culture=es-MX&ui-culture=es-MX
```

Si vous passez uniquement l’une des deux (`culture` ou `ui-culture`), le fournisseur de chaîne de requête définit les deux valeurs à l’aide de celle que vous avez passée. Par exemple, la seule définition de la culture définit à la fois `Culture` et `UICulture` :

```
http://localhost:5000/?culture=es-MX
```

### <a name="no-loccookierequestcultureprovider"></a>CookieRequestCultureProvider

Les applications de production fournissent souvent un mécanisme pour définir la culture avec la culture ASP.NET Core cookie . Utilisez la `MakeCookieValue` méthode pour créer un cookie .

`CookieRequestCultureProvider` `DefaultCookieName` Retourne le nom par défaut cookie utilisé pour suivre les informations de culture préférées de l’utilisateur. Le nom par défaut cookie est `.AspNetCore.Culture` .

Le cookie format est `c=%LANGCODE%|uic=%LANGCODE%` , où `c` est `Culture` et `uic` est `UICulture` , par exemple :

```
c=en-UK|uic=en-US
```

Si vous spécifiez uniquement les informations de culture ou la culture d’interface utilisateur, la culture spécifiée est utilisée à la fois pour les informations de culture et la culture d’interface utilisateur.

### <a name="the-accept-language-http-header"></a>En-tête HTTP Accept-Language

L’[en-tête Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) peut être défini dans la plupart des navigateurs et a d’abord été conçu pour spécifier la langue de l’utilisateur. Ce paramètre indique ce que le navigateur doit envoyer ou ce dont il a hérité du système d’exploitation sous-jacent. L’en-tête HTTP Accept-Language d’une requête de navigateur n’est pas un moyen infaillible de détecter la langue préférée de l’utilisateur (consultez [Définition des préférences de langue dans un navigateur](https://www.w3.org/International/questions/qa-lang-priorities.en.php)). Une application de production doit inclure un moyen permettant à l’utilisateur de personnaliser son choix de culture.

### <a name="set-the-accept-language-http-header-in-ie"></a>Définir l’en-tête HTTP Accept-Language dans IE

1. À partir de l’icône d’engrenage, appuyez sur **Options Internet**.

1. Appuyez sur **Langues**.

   ![Options Internet](localization/_static/lang.png)

1. Appuyez sur **Définir les langues**.

1. Appuyez sur **Ajouter une langue**.

1. Ajoutez la langue.

1. Tapez sur la langue, puis sur **Monter**.

### <a name="the-content-language-http-header"></a>En-tête HTTP Content-Language

En-tête d’entité [Content-Language](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Language) :

* Est utilisé pour décrire la ou les langues destinées au public.
* Permet à un utilisateur de faire la distinction en fonction de la langue préférée de l’utilisateur.

Les en-têtes d’entité sont utilisés dans les requêtes et les réponses HTTP.

Vous `Content-Language` pouvez ajouter l’en-tête en définissant la propriété `ApplyCurrentCultureToResponseHeaders` .

Ajout de l' `Content-Language` en-tête :

* Permet à RequestLocalizationMiddleware de définir l' `Content-Language` en-tête avec `CurrentUICulture` .
* Élimine la nécessité de définir explicitement l’en-tête de réponse `Content-Language` .

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```

### <a name="use-a-custom-provider"></a>Utiliser un fournisseur personnalisé

Supposons que vous vouliez permettre à vos clients de stocker leur langue et leur culture dans vos bases de données. Vous pouvez écrire un fournisseur pour rechercher ces valeurs pour l’utilisateur. Le code suivant illustre l’ajout d’un fournisseur personnalisé :

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

Utilisez `RequestLocalizationOptions` pour ajouter ou supprimer des fournisseurs de localisation.

### <a name="set-the-culture-programmatically"></a>Définir la culture par programmation

Cet exemple de projet **Localization.StarterWeb** sur [GitHub](https://github.com/aspnet/entropy) contient l’interface utilisateur permettant de définir `Culture`. Le fichier *Views/Shared/_SelectLanguagePartial.cshtml* vous permet de sélectionner la culture dans la liste des cultures prises en charge :

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

Le fichier *Views/Shared/_SelectLanguagePartial.cshtml* est ajouté à la section `footer` du fichier de disposition afin d’être disponible pour tous les affichages :

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

La `SetLanguage` méthode définit la culture cookie .

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

Vous ne pouvez pas incorporer *_SelectLanguagePartial.cshtml* dans l’exemple de code de ce projet. Le projet **Localization. StarterWeb** sur [GitHub](https://github.com/aspnet/entropy) contient du code pour transmettre le `RequestLocalizationOptions` à un en Razor partiel via le conteneur d' [injection de dépendance](dependency-injection.md) .

## <a name="model-binding-route-data-and-query-strings"></a>Données d’itinéraire de liaison de modèle et chaînes de requête

Consultez [comportement de la globalisation des données de routage de liaison de modèle et des chaînes de requête](xref:mvc/models/model-binding#glob).

## <a name="globalization-and-localization-terms"></a>Terminologie de la globalisation et de la localisation

Le processus de localisation de votre application exige également une compréhension élémentaire des jeux de caractères appropriés couramment utilisés dans le développement de logiciels moderne ainsi qu’une compréhension des problèmes qui y sont associés. Bien que tous les ordinateurs stockent le texte sous forme de nombres (codes), les différents systèmes stockent ce même texte en utilisant des nombres différents. Le processus de localisation consiste à traduire l’interface utilisateur de l’application pour une culture/des paramètres régionaux spécifiques.

L’[adaptabilité](/dotnet/standard/globalization-localization/localizability-review) est un processus intermédiaire permettant de vérifier qu’une application globalisée est prête à être localisée.

Le format [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) du nom de la culture est `<languagecode2>-<country/regioncode2>`, où `<languagecode2>` correspond au code de la langue et `<country/regioncode2>` au code de la sous-culture. Par exemple, `es-CL` pour l’espagnol (Chili), `en-US` pour l’anglais (États-Unis) et `en-AU` pour l’anglais (Australie). Le format [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) est la combinaison d’un code de culture à deux lettres minuscules de norme ISO 639 associé à une langue et d’un code de sous-culture à deux lettres majuscules de norme ISO 3166 associé à un pays ou une région. Consultez <https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>.

L’internationalisation est souvent abrégée par « I18N ». Cette abréviation prend les première et dernière lettres du mot ainsi que le nombre de lettres entre elles, 18 représentant ainsi le nombre de lettres le « I » initial et le « N » final. Il en va de même pour la globalisation (G11N) et la localisation (L10N).

Termes :

* Globalisation (G11N) : Processus permettant de faire prendre en charge différentes langues et régions à une application.
* Localisation (L10N) : Processus permettant de personnaliser une application pour une langue et une région données.
* Internationalisation (I18N) : Décrit à la fois la globalisation et la localisation.
* Culture : Correspond à une langue et éventuellement à une région.
* Culture neutre : Culture dont la langue est spécifiée, mais pas la région. (Exemples : « en », « es ».)
* Culture spécifique : Culture dont la langue et la région sont spécifiées. (Exemples : « en-US », « en-GB », « es-CL ».)
* Culture parent : Culture neutre qui contient une culture spécifique. (Exemple : « en » est la culture parent de « en-US » et « en-GB ».)
* Paramètres régionaux : Synonyme de culture.

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* [Projet Localization.StarterWeb](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) utilisé dans l’article.
* [Internationalisation et localisation d’applications .NET](/dotnet/standard/globalization-localization/index)
* [Ressources dans les fichiers. resx](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [Kit de ressources pour application multilingue Microsoft](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [Localisation et classes génériques](http://hishambinateya.com/localization-and-generics)

::: moniker-end
