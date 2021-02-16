---
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
ms.openlocfilehash: 30baab0649268f4abf0dbd6c99dfeef3f43d0054
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536288"
---
<span data-ttu-id="655f5-101">La méthode recommandée pour lire les valeurs de configuration associées utilise le [modèle d’options](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="655f5-101">The preferred way to read related configuration values is using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="655f5-102">Par exemple, pour lire les valeurs de configuration suivantes :</span><span class="sxs-lookup"><span data-stu-id="655f5-102">For example, to read the following configuration values:</span></span>

```json
  "Position": {
    "Title": "Editor",
    "Name": "Joe Smith"
  }
```

<span data-ttu-id="655f5-103">Créez la `PositionOptions` classe suivante :</span><span class="sxs-lookup"><span data-stu-id="655f5-103">Create the following `PositionOptions` class:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/PositionOptions.cs?name=snippet)]

<span data-ttu-id="655f5-104">Une classe d’options :</span><span class="sxs-lookup"><span data-stu-id="655f5-104">An options class:</span></span>

* <span data-ttu-id="655f5-105">Doit être non abstract avec un constructeur sans paramètre public.</span><span class="sxs-lookup"><span data-stu-id="655f5-105">Must be non-abstract with a public parameterless constructor.</span></span>
* <span data-ttu-id="655f5-106">Toutes les propriétés publiques en lecture/écriture du type sont liées.</span><span class="sxs-lookup"><span data-stu-id="655f5-106">All public read-write properties of the type are bound.</span></span>
* <span data-ttu-id="655f5-107">Les champs ne sont ***pas*** liés.</span><span class="sxs-lookup"><span data-stu-id="655f5-107">Fields are ***not*** bound.</span></span> <span data-ttu-id="655f5-108">Dans le code précédent, `Position` n’est pas lié.</span><span class="sxs-lookup"><span data-stu-id="655f5-108">In the preceding code, `Position` is not bound.</span></span> <span data-ttu-id="655f5-109">La `Position` propriété est utilisée afin que la chaîne `"Position"` ne doive pas être codée en dur dans l’application lors de la liaison de la classe à un fournisseur de configuration.</span><span class="sxs-lookup"><span data-stu-id="655f5-109">The `Position` property is used so the string `"Position"` doesn't need to be hard coded in the app when binding the class to a configuration provider.</span></span>

<span data-ttu-id="655f5-110">Le code suivant :</span><span class="sxs-lookup"><span data-stu-id="655f5-110">The following code:</span></span>

* <span data-ttu-id="655f5-111">Appelle [ConfigurationBinder. bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) pour lier la `PositionOptions` classe à la `Position` section.</span><span class="sxs-lookup"><span data-stu-id="655f5-111">Calls [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) to bind the `PositionOptions` class to the `Position` section.</span></span>
* <span data-ttu-id="655f5-112">Affiche les `Position` données de configuration.</span><span class="sxs-lookup"><span data-stu-id="655f5-112">Displays the `Position` configuration data.</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test22.cshtml.cs?name=snippet)]

<span data-ttu-id="655f5-113">Dans le code précédent, par défaut, les modifications apportées au fichier de configuration JSON après le démarrage de l’application sont lues.</span><span class="sxs-lookup"><span data-stu-id="655f5-113">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<span data-ttu-id="655f5-114">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) lie et retourne le type spécifié.</span><span class="sxs-lookup"><span data-stu-id="655f5-114">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="655f5-115">`ConfigurationBinder.Get<T>` peut être plus pratique que l’utilisation de `ConfigurationBinder.Bind` .</span><span class="sxs-lookup"><span data-stu-id="655f5-115">`ConfigurationBinder.Get<T>` may be more convenient than using `ConfigurationBinder.Bind`.</span></span> <span data-ttu-id="655f5-116">Le code suivant montre comment utiliser `ConfigurationBinder.Get<T>` avec la `PositionOptions` classe :</span><span class="sxs-lookup"><span data-stu-id="655f5-116">The following code shows how to use `ConfigurationBinder.Get<T>` with the `PositionOptions` class:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test21.cshtml.cs?name=snippet)]

<span data-ttu-id="655f5-117">Dans le code précédent, par défaut, les modifications apportées au fichier de configuration JSON après le démarrage de l’application sont lues.</span><span class="sxs-lookup"><span data-stu-id="655f5-117">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<span data-ttu-id="655f5-118">Une autre approche qui consiste à utiliser le **modèle d’options**\* consiste à lier la `Position` section et à l’ajouter au [conteneur du service d’injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="655f5-118">An alternative approach when using the \***options pattern** _ is to bind the `Position` section and add it to the [dependency injection service container](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="655f5-119">Dans le code suivant, `PositionOptions` est ajouté au conteneur de services avec <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure_> et lié à la configuration :</span><span class="sxs-lookup"><span data-stu-id="655f5-119">In the following code, `PositionOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure_> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup.cs?name=snippet)]

<span data-ttu-id="655f5-120">À l’aide du code précédent, le code suivant lit les options de position :</span><span class="sxs-lookup"><span data-stu-id="655f5-120">Using the preceding code, the following code reads the position options:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="655f5-121">Dans le code précédent, les modifications apportées au fichier de configuration JSON après le démarrage de l’application ne sont ***pas*** lues.</span><span class="sxs-lookup"><span data-stu-id="655f5-121">In the preceding code, changes to the JSON configuration file after the app has started are ***not*** read.</span></span> <span data-ttu-id="655f5-122">Pour lire les modifications une fois l’application démarrée, utilisez [IOptionsSnapshot](xref:fundamentals/configuration/options#ios).</span><span class="sxs-lookup"><span data-stu-id="655f5-122">To read changes after the app has started, use [IOptionsSnapshot](xref:fundamentals/configuration/options#ios).</span></span>
