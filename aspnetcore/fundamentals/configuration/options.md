---
title: Modèle d’options dans ASP.NET Core
author: rick-anderson
description: Découvrez comment utiliser le modèle d’options pour représenter des groupes de paramètres associés dans les applications ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/20/2020
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
uid: fundamentals/configuration/options
ms.openlocfilehash: dedc17d7d793a6fd2eac1c8017b704d98a86f1cb
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061091"
---
# <a name="options-pattern-in-aspnet-core"></a>Modèle d’options dans ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Par [Kirk Larkin](https://twitter.com/serpent5) et [Rick Anderson](https://twitter.com/RickAndMSFT).

Le modèle d’options utilise des classes pour fournir un accès fortement typé aux groupes de paramètres associés. Quand les [paramètres de configuration](xref:fundamentals/configuration/index) sont isolés par scénario dans des classes distinctes, l’application est conforme à deux principes d’ingénierie logicielle importants :

* Le [principe de séparation d’interface (ISP) ou l’encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): les scénarios (classes) qui dépendent des paramètres de configuration dépendent uniquement des paramètres de configuration qu’ils utilisent.
* [Séparation des préoccupations](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) : les paramètres des différentes parties de l’application ne sont pas dépendants ou associés les uns aux autres.

Ces options fournissent également un mécanisme de validation des données de configuration. Pour plus d'informations, reportez-vous à la section [Validation des options](#options-validation).

Cette rubrique fournit des informations sur le modèle d’options dans ASP.NET Core. Pour plus d’informations sur l’utilisation du modèle options dans les applications console, consultez [modèle d’options dans .net](/dotnet/core/extensions/options).

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

<a name="optpat"></a>

## <a name="bind-hierarchical-configuration"></a>Liaison de la configuration hiérarchique

[!INCLUDE[](~/includes/bind.md)]

<a name="oi"></a>

## <a name="options-interfaces"></a>Interfaces d’options

<xref:Microsoft.Extensions.Options.IOptions%601>:

* Ne prend **pas** en charge : _ la lecture des données de configuration après le démarrage de l’application.
  * [Options nommées](#named)
* Est inscrit en tant que [Singleton](xref:fundamentals/dependency-injection#singleton) et peut être injecté dans n’importe quelle [durée de vie de service](xref:fundamentals/dependency-injection#service-lifetimes).

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:

* Est utile dans les scénarios où les options doivent être recalculées à chaque demande. Pour plus d’informations, consultez [utiliser IOptionsSnapshot pour lire des données mises à jour](#ios).
* Est inscrit en tant qu' [étendue](xref:fundamentals/dependency-injection#scoped) et ne peut donc pas être injecté dans un service Singleton.
* Prend en charge les [options nommées](#named)

<xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:

* Est utilisé pour récupérer des options et gérer les notifications d’options pour les `TOptions` instances.
* Est inscrit en tant que [Singleton](xref:fundamentals/dependency-injection#singleton) et peut être injecté dans n’importe quelle [durée de vie de service](xref:fundamentals/dependency-injection#service-lifetimes).
* Prend en charge :
  * Notifications de modifications
  * [Options nommées](#named-options-support-with-iconfigurenamedoptions)
  * [Configuration rechargeable](#ios)
  * Invalidation sélective des options (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)
  
Les scénarios [postérieurs à la configuration](#options-post-configuration) permettent de définir ou de modifier des options après l’exécution de toutes les <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configurations.

<xref:Microsoft.Extensions.Options.IOptionsFactory%601> est chargée de créer les instances d’options. Elle dispose d’une seule méthode <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>. L’implémentation par défaut prend toutes les <xref:Microsoft.Extensions.Options.IConfigureOptions%601> et <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> inscrites et exécute toutes les configurations, puis les post-configurations. Elle fait la distinction entre <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> et <xref:Microsoft.Extensions.Options.IConfigureOptions%601> et n’appelle que l’interface appropriée.

<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> est utilisée par <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pour mettre en cache les instances `TOptions`. <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalide les instances des options dans le moniteur afin que la valeur soit recalculée (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>). Les valeurs peuvent aussi être introduites manuellement avec <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>. La méthode <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> est utilisée quand toutes les instances nommées doivent être recréées à la demande.

<a name="ios"></a>

## <a name="use-ioptionssnapshot-to-read-updated-data"></a>Utiliser IOptionsSnapshot pour lire les données mises à jour

À l’aide de <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> , les options sont calculées une fois par demande, en cas d’accès et de mise en cache pour la durée de vie de la demande. Les modifications apportées à la configuration sont lues après le démarrage de l’application lors de l’utilisation de fournisseurs de configuration qui prennent en charge la lecture des valeurs de configuration mises

La différence entre `IOptionsMonitor` et `IOptionsSnapshot` est que :

* `IOptionsMonitor` est un [service Singleton](xref:fundamentals/dependency-injection#singleton) qui récupère les valeurs d’option actuelles à tout moment, ce qui est particulièrement utile dans les dépendances Singleton.
* `IOptionsSnapshot` est un [service étendu](xref:fundamentals/dependency-injection#scoped) et fournit un instantané des options au moment de la construction de l' `IOptionsSnapshot<T>` objet. Les instantanés d’options sont conçus pour être utilisés avec des dépendances transitoires et délimitées.

Le code suivant utilise <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> .

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestSnap.cshtml.cs?name=snippet)]

Le code suivant inscrit une instance de configuration qui se `MyOptions` lie à :

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

Dans le code précédent, les modifications apportées au fichier de configuration JSON après le démarrage de l’application sont lues.

## <a name="ioptionsmonitor"></a>IOptionsMonitor

Le code suivant inscrit une instance de configuration qui est `MyOptions` liée à.

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

L'exemple suivant utilise <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> :

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestMonitor.cshtml.cs?name=snippet)]

Dans le code précédent, par défaut, les modifications apportées au fichier de configuration JSON après le démarrage de l’application sont lues.

<a name="named"></a>

## <a name="named-options-support-using-iconfigurenamedoptions"></a>Prise en charge des options nommées à l’aide de IConfigureNamedOptions

Options nommées :

* Sont utiles lorsque plusieurs sections de configuration sont liées aux mêmes propriétés.
* Respecte la casse.

Prenons le *appsettings.json* fichier suivant :

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/appsettings.NO.json)]

Au lieu de créer deux classes pour lier `TopItem:Month` et `TopItem:Year` , la classe suivante est utilisée pour chaque section :

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Models/TopItemSettings.cs)]

Le code suivant configure les options nommées :

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/StartupNO.cs?name=snippet_Example2)]

Le code suivant affiche les options nommées :

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestNO.cshtml.cs?name=snippet)]

Toutes les options sont des instances nommées. <xref:Microsoft.Extensions.Options.IConfigureOptions%601> les instances sont traitées comme ciblant l' `Options.DefaultName` instance, qui est `string.Empty` . En outre, <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> implémente <xref:Microsoft.Extensions.Options.IConfigureOptions%601>. L’implémentation par défaut de <xref:Microsoft.Extensions.Options.IOptionsFactory%601> possède une logique qui utilise chaque élément de manière appropriée. L' `null` option nommée est utilisée pour cibler toutes les instances nommées au lieu d’une instance nommée spécifique. <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> et <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> utilisent cette Convention.

## <a name="optionsbuilder-api"></a>API OptionsBuilder

<xref:Microsoft.Extensions.Options.OptionsBuilder%601> permet de configurer des instances `TOptions`. `OptionsBuilder` simplifie la création d’options nommées. En effet, il est le seul paramètre de l’appel `AddOptions<TOptions>(string optionsName)` initial et n’apparaît pas dans les appels ultérieurs. La validation des options et les surcharges `ConfigureOptions` qui acceptent des dépendances de service sont uniquement disponibles avec `OptionsBuilder`.

`OptionsBuilder` est utilisé dans la section des [options de validation](#val) .

## <a name="use-di-services-to-configure-options"></a>Utiliser les services d’injection de dépendances (DI) pour configurer des options

Les services sont accessibles à partir de l’injection de dépendances lors de la configuration des options de deux manières :

* Transmettez un délégué de configuration à [configurer](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) sur [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1). `OptionsBuilder<TOptions>` fournit des surcharges de la [configuration](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) qui autorisent l’utilisation de cinq services au maximum pour configurer des options :

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* Créez un type qui implémente <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ou <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> et inscrit le type en tant que service.

Nous vous recommandons de transmettre un délégué de configuration à [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), car il est plus complexe de créer un service. La création d’un type équivaut à ce que fait l’infrastructure lors de l’appel de [configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*). L’appel de [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) a pour effet d’inscrire une instance générique temporaire de <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, dont l’un des constructeurs accepte les types de service génériques spécifiés. 

<a name="val"></a>

## <a name="options-validation"></a>Validation des options

La validation des options permet de valider les valeurs d’option.

Prenons le *appsettings.json* fichier suivant :

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsValidationSample/appsettings.Dev2.json)]

La classe suivante crée une liaison avec la `"MyConfig"` section de configuration et applique deux `DataAnnotations` règles :

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigOptions.cs?name=snippet)]

Le code suivant :

* Appelle <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A> pour recevoir un [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1) qui crée une liaison avec la `MyConfigOptions` classe.
* Appelle <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations%2A> pour activer la validation à l’aide de `DataAnnotations` .

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup.cs?name=snippet)]

La `ValidateDataAnnotations` méthode d’extension est définie dans le package NuGet [Microsoft. extensions. options. DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) . Pour les applications Web qui utilisent le `Microsoft.NET.Sdk.Web` Kit de développement logiciel (SDK), ce package est référencé implicitement à partir de l’infrastructure partagée.

Le code suivant affiche les valeurs de configuration ou les erreurs de validation :

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Controllers/HomeController.cs?name=snippet)]

Le code suivant applique une règle de validation plus complexe à l’aide d’un délégué :

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup2.cs?name=snippet)]

### <a name="ivalidateoptions-for-complex-validation"></a>IValidateOptions pour la validation complexe

La classe suivante implémente <xref:Microsoft.Extensions.Options.IValidateOptions`1> :

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigValidation.cs?name=snippet)]

`IValidateOptions` permet de déplacer le code de validation de `StartUp` et vers une classe.

À l’aide du code précédent, la validation est activée dans `Startup.ConfigureServices` avec le code suivant :

[!code-csharp[](options/samples/3.x/OptionsValidationSample/StartupValidation.cs?name=snippet)]

<!-- The following comment doesn't seem that useful 
Options validation doesn't guard against options modifications after the options instance is created. For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed. The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.

The `Validate` method accepts a `Func<TOptions, bool>`. To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:

* Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`
* Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`

`IValidateOptions` validates:

* A specific named options instance.
* All options when `name` is `null`.

Return a `ValidateOptionsResult` from your implementation of the interface:

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`. `Microsoft.Extensions.Options.DataAnnotations` is implicitly referenced in ASP.NET Core apps.

-->

## <a name="options-post-configuration"></a>Options de post-configuration

Définissez la post-configuration avec <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>. La post-configuration s’exécute après chaque configuration de <xref:Microsoft.Extensions.Options.IConfigureOptions%601> :

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> permet de post-configurer les options nommées :

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

Utilisez <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> pour post-configurer toutes les instances de configuration :

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a>Accès aux options au démarrage

<xref:Microsoft.Extensions.Options.IOptions%601> et <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> peuvent être utilisées dans `Startup.Configure`, dans la mesure où les services sont générés avant que la méthode `Configure` ne s’exécute.

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

N’utilisez pas <xref:Microsoft.Extensions.Options.IOptions%601> ou <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> dans `Startup.ConfigureServices`. Un état d’options incohérent peut exister en raison de l’ordre des inscriptions de service.

## <a name="optionsconfigurationextensions-nuget-package"></a>Package NuGet Options.ConfigurationExtensions

Le package [ urationExtensionsMicrosoft.Extensions.Options.Config](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) est implicitement référencé dans les applications ASP.net core.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Le modèle d’options utilise des classes pour représenter les groupes de paramètres associés. Quand les [paramètres de configuration](xref:fundamentals/configuration/index) sont isolés par scénario dans des classes distinctes, l’application est conforme à deux principes d’ingénierie logicielle importants :

* Le [principe de séparation d’interface (ISP) ou l’encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): les scénarios (classes) qui dépendent des paramètres de configuration dépendent uniquement des paramètres de configuration qu’ils utilisent.
* [Séparation des préoccupations](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) : les paramètres des différentes parties de l’application ne sont pas dépendants ou associés les uns aux autres.

Ces options fournissent également un mécanisme de validation des données de configuration. Pour plus d'informations, reportez-vous à la section [Validation des options](#options-validation).

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Prérequis

Référencer le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ou ajouter une référence de package au package [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/).

## <a name="options-interfaces"></a>Interfaces d’options

<xref:Microsoft.Extensions.Options.IOptionsMonitor%601> permet de récupérer des options et de gérer les notifications d’options pour les instances `TOptions`. <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> prend en charge les scénarios suivants :

* Notifications de modifications
* [Options nommées](#named-options-support-with-iconfigurenamedoptions)
* [Configuration rechargeable](#reload-configuration-data-with-ioptionssnapshot)
* Invalidation sélective des options (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)

Des scénarios [post-configuration](#options-post-configuration) vous permettent de définir ou de modifier les options après chaque configuration de <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.

<xref:Microsoft.Extensions.Options.IOptionsFactory%601> est chargée de créer les instances d’options. Elle dispose d’une seule méthode <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>. L’implémentation par défaut prend toutes les <xref:Microsoft.Extensions.Options.IConfigureOptions%601> et <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> inscrites et exécute toutes les configurations, puis les post-configurations. Elle fait la distinction entre <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> et <xref:Microsoft.Extensions.Options.IConfigureOptions%601> et n’appelle que l’interface appropriée.

<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> est utilisée par <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pour mettre en cache les instances `TOptions`. <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalide les instances des options dans le moniteur afin que la valeur soit recalculée (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>). Les valeurs peuvent aussi être introduites manuellement avec <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>. La méthode <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> est utilisée quand toutes les instances nommées doivent être recréées à la demande.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> est utile dans les scénarios où des options doivent être recalculées à chaque requête. Pour plus d’informations, consultez la section [Recharger les données de configuration avec IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot).

<xref:Microsoft.Extensions.Options.IOptions%601> peut être utilisée pour prendre en charge des options. Toutefois, <xref:Microsoft.Extensions.Options.IOptions%601> ne prend pas en charge les scénarios <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> précédents. Vous pouvez continuer à utiliser <xref:Microsoft.Extensions.Options.IOptions%601> dans des infrastructures et bibliothèques existantes qui utilisent déjà l’interface <xref:Microsoft.Extensions.Options.IOptions%601> mais ne nécessitent pas les scénarios fournis par <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.

## <a name="general-options-configuration"></a>Configuration des options générales

La configuration des options générales est illustrée dans l’exemple 1 de l’exemple d’application.

Une classe d’options doit être non abstraite avec un constructeur public sans paramètre. La classe suivante, `MyOptions`, a deux propriétés : `Option1` et `Option2`. Définir des valeurs par défaut est facultatif, mais le constructeur de classe dans l’exemple suivant définit la valeur par défaut `Option1`. `Option2` a une valeur par défaut définie par l’initialisation directe de la propriété ( *Models/MyOptions.cs* ) :

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

La classe `MyOptions` est ajoutée au conteneur de service avec <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> et liée à la configuration :

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

Le modèle de page suivant utilise une [injection de dépendances de constructeur](xref:mvc/controllers/dependency-injection) avec <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pour accéder aux paramètres ( *Pages/Index.cshtml.cs* ) :

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

Le fichier de l’exemple *appsettings.json* spécifie des valeurs pour `option1` et `option2` :

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

Quand l’application est exécutée, la méthode `OnGet` du modèle de page retourne une chaîne indiquant les valeurs de la classe d’options :

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> Quand vous utilisez un <xref:System.Configuration.ConfigurationBuilder> personnalisé pour charger une configuration d’options à partir d’un fichier de paramètres, vérifiez que le chemin de base est correctement défini :
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> Vous n’avez pas besoin de définir explicitement le chemin de base quand vous chargez une configuration d’options à partir du fichier de paramètres via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.

## <a name="configure-simple-options-with-a-delegate"></a>Configurer des options simples avec un délégué

La configuration d’options simples avec un délégué est illustrée dans l’exemple 2 de l’exemple d’application.

Utilisez un délégué pour définir les valeurs des options. L’exemple d’application utilise la classe `MyOptionsWithDelegateConfig` ( *Models/MyOptionsWithDelegateConfig.cs* ) :

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

Dans le code suivant, un second service <xref:Microsoft.Extensions.Options.IConfigureOptions%601> est ajouté au conteneur de services. Il utilise un délégué pour configurer la liaison avec `MyOptionsWithDelegateConfig` :

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

*Index.cshtml.cs* :

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

Vous pouvez ajouter plusieurs fournisseurs de configuration. Des fournisseurs de configuration sont disponibles à partir de packages NuGet et sont appliqués dans l’ordre de leur inscription. Pour plus d'informations, consultez <xref:fundamentals/configuration/index>.

Chaque appel à <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> ajoute un service <xref:Microsoft.Extensions.Options.IConfigureOptions%601> au conteneur de services. Dans l’exemple précédent, les valeurs de `Option1` et `Option2` sont toutes deux spécifiées dans *appsettings.json* , mais les valeurs de `Option1` et `Option2` sont remplacées par le délégué configuré.

Quand plusieurs services de configuration sont activés, la dernière source de configuration spécifiée *gagne* et définit la valeur de configuration. Quand l’application est exécutée, la méthode `OnGet` du modèle de page retourne une chaîne indiquant les valeurs de la classe d’options :

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a>Configuration des sous-options

La configuration des sous-options est illustrée dans l’exemple 3 de l’exemple d’application.

Les applications doivent créer des classes d’options qui appartiennent à des groupes de scénarios spécifiques (classes) dans l’application. Les parties de l’application qui requièrent des valeurs de configuration ne doivent avoir accès qu’aux valeurs de configuration qu’elles utilisent.

Au moment de la liaison des options à la configuration, chaque propriété du type options est liée à une clé de configuration sous la forme `property[:sub-property:]`. Par exemple, la `MyOptions.Option1` propriété est liée à la clé `Option1` , qui est lue à partir de la `option1` propriété dans *appsettings.json* .

Dans le code suivant, un troisième service <xref:Microsoft.Extensions.Options.IConfigureOptions%601> est ajouté au conteneur de services. Il crée une liaison `MySubOptions` avec la section `subsection` du *appsettings.json* fichier :

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

La `GetSection` méthode requiert l' <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> espace de noms.

Le fichier de l’exemple *appsettings.json* définit un `subsection` membre avec des clés pour `suboption1` et `suboption2` :

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

La classe `MySubOptions` définit des propriétés, `SubOption1` et `SubOption2`, pour conserver les valeurs des options ( *Models/MySubOptions.cs* ) :

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

La méthode `OnGet` du modèle de page retourne une chaîne avec les valeurs des options ( *Pages/Index.cshtml.cs* ) :

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

Quand l’application est exécutée, la méthode `OnGet` retourne une chaîne indiquant les valeurs de la classe de sous-options :

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a>Injection d’options

L’injection d’options est illustrée dans l’exemple 4 de l’exemple d’application.

Injecter <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> dans :

* Une Razor page ou une vue MVC avec la [`@inject`](xref:mvc/views/razor#inject) Razor directive.
* Modèle de page ou de vue.

L’exemple suivant tiré de l’exemple d’application injecte <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> dans un modèle de page ( *pages/index. cshtml. cs* ) :

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

L’exemple d’application montre comment injecter `IOptionsMonitor<MyOptions>` avec une directive `@inject` :

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

Quand l’application est exécutée, les valeurs d’options sont affichées dans la page rendue :

![Les valeurs d’options Option1: value1_from_json et Option2: -1 sont chargées à partir du modèle et par injection dans la vue.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a>Recharger les données de configuration avec IOptionsSnapshot

Le rechargement des données de configuration avec <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> est illustré dans l’exemple 5 de l’exemple d’application.

À l’aide de <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> , les options sont calculées une fois par demande, en cas d’accès et de mise en cache pour la durée de vie de la demande.

La différence entre `IOptionsMonitor` et `IOptionsSnapshot` est que :

* `IOptionsMonitor` est un [service Singleton](xref:fundamentals/dependency-injection#singleton) qui récupère les valeurs d’option actuelles à tout moment, ce qui est particulièrement utile dans les dépendances Singleton.
* `IOptionsSnapshot` est un [service étendu](xref:fundamentals/dependency-injection#scoped) et fournit un instantané des options au moment de la construction de l' `IOptionsSnapshot<T>` objet. Les instantanés d’options sont conçus pour être utilisés avec des dépendances transitoires et délimitées.

L’exemple suivant montre comment un nouveau <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> est créé après *appsettings.json* les modifications ( *pages/index. cshtml. cs* ). Plusieurs demandes au serveur retournent des valeurs constantes fournies par le *appsettings.json* fichier jusqu’à ce que le fichier soit modifié et que la configuration recharge.

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

L’illustration suivante montre les `option1` valeurs initiales et `option2` chargées à partir du *appsettings.json* fichier :

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

Remplacez les valeurs du *appsettings.json* fichier par `value1_from_json UPDATED` et `200` . Enregistrez le fichier *appsettings.json* . Actualisez le navigateur pour constater la mise à jour des valeurs des options :

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a>Prise en charge des options nommées avec IConfigureNamedOptions

La prise en charge des options nommées avec <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> est illustrée dans l’exemple 6 de l’exemple d’application.

La prise en charge des options nommées permet à l’application de faire la distinction entre les configurations d’options nommées. Dans l’exemple d’application, les options nommées sont déclarées avec [OptionsServiceCollectionExtensions.Configurer](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), qui appelle [ConfigureNamedOptions \<TOptions> . Configurez](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) la méthode d’extension. Les options nommées respectent la casse.

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

L’exemple d’application accède aux options nommées avec <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> ( *Pages/Index.cshtml.cs* ) :

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

Quand l’exemple d’application est exécuté, les options nommées sont retournées :

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

`named_options_1` les valeurs sont fournies à partir de la configuration, qui sont chargées à partir du *appsettings.json* fichier. Les valeurs `named_options_2` sont fournies par :

* Le délégué `named_options_2` dans `ConfigureServices` pour `Option1`.
* La valeur par défaut `Option2` fournie par la classe `MyOptions`.

## <a name="configure-all-options-with-the-configureall-method"></a>Configurer toutes les options avec la méthode ConfigureAll

Configurez toutes les instances d’options avec la méthode <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>. Le code suivant configure `Option1` pour toutes les instances de configuration ayant une valeur commune. Ajoutez le code suivant manuellement à la méthode `Startup.ConfigureServices` :

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

Exécuter l’exemple d’application après avoir ajouté le code produit le résultat suivant :

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> Toutes les options sont des instances nommées. Les instances <xref:Microsoft.Extensions.Options.IConfigureOptions%601> existantes sont traitées comme ciblant l’instance `Options.DefaultName`, qui est `string.Empty`. En outre, <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> implémente <xref:Microsoft.Extensions.Options.IConfigureOptions%601>. L’implémentation par défaut de <xref:Microsoft.Extensions.Options.IOptionsFactory%601> possède une logique qui utilise chaque élément de manière appropriée. L’option nommée `null` est utilisée pour cibler toutes les instances nommées au lieu d’une instance nommée spécifique (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> et <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> utilisent cette convention).

## <a name="optionsbuilder-api"></a>API OptionsBuilder

<xref:Microsoft.Extensions.Options.OptionsBuilder%601> permet de configurer des instances `TOptions`. `OptionsBuilder` simplifie la création d’options nommées. En effet, il est le seul paramètre de l’appel `AddOptions<TOptions>(string optionsName)` initial et n’apparaît pas dans les appels ultérieurs. La validation des options et les surcharges `ConfigureOptions` qui acceptent des dépendances de service sont uniquement disponibles avec `OptionsBuilder`.

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a>Utiliser les services d’injection de dépendances (DI) pour configurer des options

Vous pouvez accéder à d’autres services à partir de l’injection de dépendances pendant que vous configurez des options de deux manières différentes :

* Transmettez un délégué de configuration à [configurer](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) sur [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1). [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1) fournit des surcharges de la [configuration](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) qui vous permettent d’utiliser jusqu’à cinq services pour configurer des options :

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* Créez votre propre type qui implémente <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ou <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> et inscrit le type en tant que service.

Nous vous recommandons de transmettre un délégué de configuration à [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), car il est plus complexe de créer un service. Créer votre propre type équivaut à ce que fait automatiquement le framework quand vous utilisez [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*). L’appel de [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) a pour effet d’inscrire une instance générique temporaire de <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, dont l’un des constructeurs accepte les types de service génériques spécifiés. 

## <a name="options-validation"></a>Validation des options

La validation des options vous permet de valider les options lorsque celles-ci sont configurées. Appelez `Validate` avec une méthode de validation qui retourne `true` si les options sont valides et `false` si elles ne sont pas valides :

```csharp
// Registration
services.AddOptions<MyOptions>("optionalOptionsName")
    .Configure(o => { }) // Configure the options
    .Validate(o => YourValidationShouldReturnTrueIfValid(o), 
        "custom error");

// Consumption
var monitor = services.BuildServiceProvider()
    .GetService<IOptionsMonitor<MyOptions>>();
  
try
{
    var options = monitor.Get("optionalOptionsName");
}
catch (OptionsValidationException e) 
{
   // e.OptionsName returns "optionalOptionsName"
   // e.OptionsType returns typeof(MyOptions)
   // e.Failures returns a list of errors, which would contain 
   //     "custom error"
}
```

L’exemple précédent définit l’instance d’options nommée sur `optionalOptionsName`. L'instance d’options par défaut est `Options.DefaultName`.

La validation s’exécute lorsque l’instance d’options est créée. La validation d’une instance options est garantie lors de sa première accès.

> [!IMPORTANT]
> La validation des options ne protège pas contre les modifications apportées aux options une fois l’instance des options créée. Par exemple, `IOptionsSnapshot` les options sont créées et validées une fois par demande lorsque les options y accèdent pour la première fois. Les `IOptionsSnapshot` options ne sont pas validées à nouveau lors des tentatives d’accès suivantes *pour la même requête* .

La méthode `Validate` accepte un `Func<TOptions, bool>`. Pour personnaliser entièrement la validation, implémentez `IValidateOptions<TOptions>`, ce qui permet :

* Validation de plusieurs types d’options : `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`
* Validation qui dépend d’un autre type d’option : `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`

`IValidateOptions` valide :

* Une instance d’options nommée spécifique.
* Toutes les options quand `name` est `null`.

Retourne `ValidateOptionsResult` à partir de votre implémentation de l’interface :

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

La validation basée sur les annotations de données est disponible à partir du package [Microsoft. extensions. options. DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) en appelant la <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> méthode sur `OptionsBuilder<TOptions>` . `Microsoft.Extensions.Options.DataAnnotations` est inclus dans le sous- [package Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).

```csharp
using Microsoft.Extensions.DependencyInjection;

private class AnnotatedOptions
{
    [Required]
    public string Required { get; set; }

    [StringLength(5, ErrorMessage = "Too long.")]
    public string StringLength { get; set; }

    [Range(-5, 5, ErrorMessage = "Out of range.")]
    public int IntRange { get; set; }
}

[Fact]
public void CanValidateDataAnnotations()
{
    var services = new ServiceCollection();
    services.AddOptions<AnnotatedOptions>()
        .Configure(o =>
        {
            o.StringLength = "111111";
            o.IntRange = 10;
            o.Custom = "nowhere";
        })
        .ValidateDataAnnotations();

    var sp = services.BuildServiceProvider();

    var error = Assert.Throws<OptionsValidationException>(() => 
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().CurrentValue);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}
```

La validation hâtive (échec rapide au démarrage) est à l’étude pour une version ultérieure.

## <a name="options-post-configuration"></a>Options de post-configuration

Définissez la post-configuration avec <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>. La post-configuration s’exécute après chaque configuration de <xref:Microsoft.Extensions.Options.IConfigureOptions%601> :

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> permet de post-configurer les options nommées :

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

Utilisez <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> pour post-configurer toutes les instances de configuration :

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a>Accès aux options au démarrage

<xref:Microsoft.Extensions.Options.IOptions%601> et <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> peuvent être utilisées dans `Startup.Configure`, dans la mesure où les services sont générés avant que la méthode `Configure` ne s’exécute.

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

N’utilisez pas <xref:Microsoft.Extensions.Options.IOptions%601> ou <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> dans `Startup.ConfigureServices`. Un état d’options incohérent peut exister en raison de l’ordre des inscriptions de service.

::: moniker-end

::: moniker range="= aspnetcore-2.1"

Le modèle d’options utilise des classes pour représenter les groupes de paramètres associés. Quand les [paramètres de configuration](xref:fundamentals/configuration/index) sont isolés par scénario dans des classes distinctes, l’application est conforme à deux principes d’ingénierie logicielle importants :

* Le [principe de séparation d’interface (ISP) ou l’encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): les scénarios (classes) qui dépendent des paramètres de configuration dépendent uniquement des paramètres de configuration qu’ils utilisent.
* [Séparation des préoccupations](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) : les paramètres des différentes parties de l’application ne sont pas dépendants ou associés les uns aux autres.

Ces options fournissent également un mécanisme de validation des données de configuration. Pour plus d'informations, reportez-vous à la section [Validation des options](#options-validation).

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Prérequis

Référencer le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ou ajouter une référence de package au package [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/).

## <a name="options-interfaces"></a>Interfaces d’options

<xref:Microsoft.Extensions.Options.IOptionsMonitor%601> permet de récupérer des options et de gérer les notifications d’options pour les instances `TOptions`. <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> prend en charge les scénarios suivants :

* Notifications de modifications
* [Options nommées](#named-options-support-with-iconfigurenamedoptions)
* [Configuration rechargeable](#reload-configuration-data-with-ioptionssnapshot)
* Invalidation sélective des options (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)

Des scénarios [post-configuration](#options-post-configuration) vous permettent de définir ou de modifier les options après chaque configuration de <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.

<xref:Microsoft.Extensions.Options.IOptionsFactory%601> est chargée de créer les instances d’options. Elle dispose d’une seule méthode <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>. L’implémentation par défaut prend toutes les <xref:Microsoft.Extensions.Options.IConfigureOptions%601> et <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> inscrites et exécute toutes les configurations, puis les post-configurations. Elle fait la distinction entre <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> et <xref:Microsoft.Extensions.Options.IConfigureOptions%601> et n’appelle que l’interface appropriée.

<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> est utilisée par <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pour mettre en cache les instances `TOptions`. <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalide les instances des options dans le moniteur afin que la valeur soit recalculée (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>). Les valeurs peuvent aussi être introduites manuellement avec <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>. La méthode <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> est utilisée quand toutes les instances nommées doivent être recréées à la demande.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> est utile dans les scénarios où des options doivent être recalculées à chaque requête. Pour plus d’informations, consultez la section [Recharger les données de configuration avec IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot).

<xref:Microsoft.Extensions.Options.IOptions%601> peut être utilisée pour prendre en charge des options. Toutefois, <xref:Microsoft.Extensions.Options.IOptions%601> ne prend pas en charge les scénarios <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> précédents. Vous pouvez continuer à utiliser <xref:Microsoft.Extensions.Options.IOptions%601> dans des infrastructures et bibliothèques existantes qui utilisent déjà l’interface <xref:Microsoft.Extensions.Options.IOptions%601> mais ne nécessitent pas les scénarios fournis par <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.

## <a name="general-options-configuration"></a>Configuration des options générales

La configuration des options générales est illustrée dans l’exemple 1 de l’exemple d’application.

Une classe d’options doit être non abstraite avec un constructeur public sans paramètre. La classe suivante, `MyOptions`, a deux propriétés : `Option1` et `Option2`. Définir des valeurs par défaut est facultatif, mais le constructeur de classe dans l’exemple suivant définit la valeur par défaut `Option1`. `Option2` a une valeur par défaut définie par l’initialisation directe de la propriété ( *Models/MyOptions.cs* ) :

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

La classe `MyOptions` est ajoutée au conteneur de service avec <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> et liée à la configuration :

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

Le modèle de page suivant utilise une [injection de dépendances de constructeur](xref:mvc/controllers/dependency-injection) avec <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pour accéder aux paramètres ( *Pages/Index.cshtml.cs* ) :

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

Le fichier de l’exemple *appsettings.json* spécifie des valeurs pour `option1` et `option2` :

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

Quand l’application est exécutée, la méthode `OnGet` du modèle de page retourne une chaîne indiquant les valeurs de la classe d’options :

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> Quand vous utilisez un <xref:System.Configuration.ConfigurationBuilder> personnalisé pour charger une configuration d’options à partir d’un fichier de paramètres, vérifiez que le chemin de base est correctement défini :
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> Vous n’avez pas besoin de définir explicitement le chemin de base quand vous chargez une configuration d’options à partir du fichier de paramètres via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.

## <a name="configure-simple-options-with-a-delegate"></a>Configurer des options simples avec un délégué

La configuration d’options simples avec un délégué est illustrée dans l’exemple 2 de l’exemple d’application.

Utilisez un délégué pour définir les valeurs des options. L’exemple d’application utilise la classe `MyOptionsWithDelegateConfig` ( *Models/MyOptionsWithDelegateConfig.cs* ) :

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

Dans le code suivant, un second service <xref:Microsoft.Extensions.Options.IConfigureOptions%601> est ajouté au conteneur de services. Il utilise un délégué pour configurer la liaison avec `MyOptionsWithDelegateConfig` :

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

*Index.cshtml.cs* :

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

Vous pouvez ajouter plusieurs fournisseurs de configuration. Des fournisseurs de configuration sont disponibles à partir de packages NuGet et sont appliqués dans l’ordre de leur inscription. Pour plus d'informations, consultez <xref:fundamentals/configuration/index>.

Chaque appel à <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> ajoute un service <xref:Microsoft.Extensions.Options.IConfigureOptions%601> au conteneur de services. Dans l’exemple précédent, les valeurs de `Option1` et `Option2` sont toutes deux spécifiées dans *appsettings.json* , mais les valeurs de `Option1` et `Option2` sont remplacées par le délégué configuré.

Quand plusieurs services de configuration sont activés, la dernière source de configuration spécifiée *gagne* et définit la valeur de configuration. Quand l’application est exécutée, la méthode `OnGet` du modèle de page retourne une chaîne indiquant les valeurs de la classe d’options :

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a>Configuration des sous-options

La configuration des sous-options est illustrée dans l’exemple 3 de l’exemple d’application.

Les applications doivent créer des classes d’options qui appartiennent à des groupes de scénarios spécifiques (classes) dans l’application. Les parties de l’application qui requièrent des valeurs de configuration ne doivent avoir accès qu’aux valeurs de configuration qu’elles utilisent.

Au moment de la liaison des options à la configuration, chaque propriété du type options est liée à une clé de configuration sous la forme `property[:sub-property:]`. Par exemple, la `MyOptions.Option1` propriété est liée à la clé `Option1` , qui est lue à partir de la `option1` propriété dans *appsettings.json* .

Dans le code suivant, un troisième service <xref:Microsoft.Extensions.Options.IConfigureOptions%601> est ajouté au conteneur de services. Il crée une liaison `MySubOptions` avec la section `subsection` du *appsettings.json* fichier :

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

La `GetSection` méthode requiert l' <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> espace de noms.

Le fichier de l’exemple *appsettings.json* définit un `subsection` membre avec des clés pour `suboption1` et `suboption2` :

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

La classe `MySubOptions` définit des propriétés, `SubOption1` et `SubOption2`, pour conserver les valeurs des options ( *Models/MySubOptions.cs* ) :

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

La méthode `OnGet` du modèle de page retourne une chaîne avec les valeurs des options ( *Pages/Index.cshtml.cs* ) :

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

Quand l’application est exécutée, la méthode `OnGet` retourne une chaîne indiquant les valeurs de la classe de sous-options :

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a>Options fournies par un modèle d’affichage ou avec une injection de vue directe

Les options fournies par un modèle d’affichage ou avec une injection de vue directe sont illustrées dans l’exemple 4 de l’exemple d’application.

Vous pouvez fournir les options dans un modèle d’affichage ou en injectant <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directement dans une vue ( *Pages/Index.cshtml.cs* ) :

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

L’exemple d’application montre comment injecter `IOptionsMonitor<MyOptions>` avec une directive `@inject` :

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

Quand l’application est exécutée, les valeurs d’options sont affichées dans la page rendue :

![Les valeurs d’options Option1: value1_from_json et Option2: -1 sont chargées à partir du modèle et par injection dans la vue.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a>Recharger les données de configuration avec IOptionsSnapshot

Le rechargement des données de configuration avec <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> est illustré dans l’exemple 5 de l’exemple d’application.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> prend en charge le rechargement des options avec une charge de traitement minimale.

Les options sont calculées une fois par requête quand le système y accède et sont mises en cache pour toute la durée de vie de la requête.

L’exemple suivant montre comment un nouveau <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> est créé après *appsettings.json* les modifications ( *pages/index. cshtml. cs* ). Plusieurs demandes au serveur retournent des valeurs constantes fournies par le *appsettings.json* fichier jusqu’à ce que le fichier soit modifié et que la configuration recharge.

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

L’illustration suivante montre les `option1` valeurs initiales et `option2` chargées à partir du *appsettings.json* fichier :

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

Remplacez les valeurs du *appsettings.json* fichier par `value1_from_json UPDATED` et `200` . Enregistrez le fichier *appsettings.json* . Actualisez le navigateur pour constater la mise à jour des valeurs des options :

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a>Prise en charge des options nommées avec IConfigureNamedOptions

La prise en charge des options nommées avec <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> est illustrée dans l’exemple 6 de l’exemple d’application.

La prise en charge des options nommées permet à l’application de faire la distinction entre les configurations d’options nommées. Dans l’exemple d’application, les options nommées sont déclarées avec [OptionsServiceCollectionExtensions.Configurer](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), qui appelle [ConfigureNamedOptions \<TOptions> . Configurez](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) la méthode d’extension. Les options nommées respectent la casse.

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

L’exemple d’application accède aux options nommées avec <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> ( *Pages/Index.cshtml.cs* ) :

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

Quand l’exemple d’application est exécuté, les options nommées sont retournées :

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

`named_options_1` les valeurs sont fournies à partir de la configuration, qui sont chargées à partir du *appsettings.json* fichier. Les valeurs `named_options_2` sont fournies par :

* Le délégué `named_options_2` dans `ConfigureServices` pour `Option1`.
* La valeur par défaut `Option2` fournie par la classe `MyOptions`.

## <a name="configure-all-options-with-the-configureall-method"></a>Configurer toutes les options avec la méthode ConfigureAll

Configurez toutes les instances d’options avec la méthode <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>. Le code suivant configure `Option1` pour toutes les instances de configuration ayant une valeur commune. Ajoutez le code suivant manuellement à la méthode `Startup.ConfigureServices` :

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

Exécuter l’exemple d’application après avoir ajouté le code produit le résultat suivant :

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> Toutes les options sont des instances nommées. Les instances <xref:Microsoft.Extensions.Options.IConfigureOptions%601> existantes sont traitées comme ciblant l’instance `Options.DefaultName`, qui est `string.Empty`. En outre, <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> implémente <xref:Microsoft.Extensions.Options.IConfigureOptions%601>. L’implémentation par défaut de <xref:Microsoft.Extensions.Options.IOptionsFactory%601> possède une logique qui utilise chaque élément de manière appropriée. L’option nommée `null` est utilisée pour cibler toutes les instances nommées au lieu d’une instance nommée spécifique (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> et <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> utilisent cette convention).

## <a name="optionsbuilder-api"></a>API OptionsBuilder

<xref:Microsoft.Extensions.Options.OptionsBuilder%601> permet de configurer des instances `TOptions`. `OptionsBuilder` simplifie la création d’options nommées. En effet, il est le seul paramètre de l’appel `AddOptions<TOptions>(string optionsName)` initial et n’apparaît pas dans les appels ultérieurs. La validation des options et les surcharges `ConfigureOptions` qui acceptent des dépendances de service sont uniquement disponibles avec `OptionsBuilder`.

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a>Utiliser les services d’injection de dépendances (DI) pour configurer des options

Vous pouvez accéder à d’autres services à partir de l’injection de dépendances pendant que vous configurez des options de deux manières différentes :

* Transmettez un délégué de configuration à [configurer](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) sur [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1). [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1) fournit des surcharges de la [configuration](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) qui vous permettent d’utiliser jusqu’à cinq services pour configurer des options :

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* Créez votre propre type qui implémente <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ou <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> et inscrit le type en tant que service.

Nous vous recommandons de transmettre un délégué de configuration à [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), car il est plus complexe de créer un service. Créer votre propre type équivaut à ce que fait automatiquement le framework quand vous utilisez [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*). L’appel de [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) a pour effet d’inscrire une instance générique temporaire de <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, dont l’un des constructeurs accepte les types de service génériques spécifiés. 

## <a name="options-post-configuration"></a>Options de post-configuration

Définissez la post-configuration avec <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>. La post-configuration s’exécute après chaque configuration de <xref:Microsoft.Extensions.Options.IConfigureOptions%601> :

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> permet de post-configurer les options nommées :

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

Utilisez <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> pour post-configurer toutes les instances de configuration :

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a>Accès aux options au démarrage

<xref:Microsoft.Extensions.Options.IOptions%601> et <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> peuvent être utilisées dans `Startup.Configure`, dans la mesure où les services sont générés avant que la méthode `Configure` ne s’exécute.

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

N’utilisez pas <xref:Microsoft.Extensions.Options.IOptions%601> ou <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> dans `Startup.ConfigureServices`. Un état d’options incohérent peut exister en raison de l’ordre des inscriptions de service.

::: moniker-end

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:fundamentals/configuration/index>
