---
title: Détecter les modifications avec des jetons de modification dans ASP.NET Core
author: rick-anderson
description: Découvrez comment utiliser des jetons de modification pour effectuer le suivi des modifications.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 10/07/2019
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
uid: fundamentals/change-tokens
ms.openlocfilehash: f20d44c7767b284f727ce19a46224dae0cf6a5e1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053772"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a>Détecter les modifications avec des jetons de modification dans ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Un *jeton de modification* est un module à usage général de bas niveau, utilisé pour effectuer le suivi des modifications de l’état.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="ichangetoken-interface"></a>Interface d’IChangeToken

<xref:Microsoft.Extensions.Primitives.IChangeToken> propage des notifications indiquant qu’une modification s’est produite. `IChangeToken` réside dans l’espace de noms <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>. Le package NuGet [Microsoft. extensions. Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) est fourni implicitement aux applications ASP.net core.

`IChangeToken` a deux propriétés :

* <xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indique si le jeton déclenche des rappels de façon proactive. Si `ActiveChangedCallbacks` est défini sur `false`, un rappel n’est jamais appelé, et l’application doit interroger `HasChanged` pour connaître les modifications. Il est également possible qu’un jeton ne soit jamais annulé si aucune modification ne se produit, ou si l’écouteur de modifications sous-jacent est supprimé ou désactivé.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> obtient une valeur qui indique si une modification a eu lieu.

L' `IChangeToken` interface inclut la méthode [RegisterChangeCallback (action \<Object> , Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) , qui inscrit un rappel appelé lorsque le jeton a changé. `HasChanged` doit être défini avant que le rappel soit appelé.

## <a name="changetoken-class"></a>Classe ChangeToken

<xref:Microsoft.Extensions.Primitives.ChangeToken> est une classe statique utilisée pour propager des notifications indiquant qu’une modification s’est produite. `ChangeToken` réside dans l’espace de noms <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>. Le package NuGet [Microsoft. extensions. Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) est fourni implicitement aux applications ASP.net core.

La méthode [ChangeToken. OnChange (Func \<IChangeToken> , action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) inscrit un `Action` à appeler chaque fois que le jeton change :

* `Func<IChangeToken>` produit le jeton.
* `Action` est appelée quand le jeton change.

La surcharge [ChangeToken. OnChange \<TState> (Func \<IChangeToken> , action \<TState> , TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) prend un paramètre supplémentaire `TState` passé dans le consommateur de jetons `Action` .

`OnChange`Retourne un <xref:System.IDisposable>. L’appel de <xref:System.IDisposable.Dispose*> fait que le jeton cesse d’être à l’écoute des modifications suivantes et libère les ressources du jeton.

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a>Exemples d’utilisation de jetons de modification dans ASP.NET Core

Les jetons de modification sont utilisés dans des zones importantes d’ASP.NET Core pour surveiller les modifications apportées aux objets :

* Pour surveiller les modifications apportées aux fichiers, la méthode <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> de <xref:Microsoft.Extensions.FileProviders.IFileProvider> crée un `IChangeToken` pour les fichiers ou le dossier à surveiller.
* Les jetons `IChangeToken` peuvent être ajoutés aux entrées du cache pour déclencher des suppressions dans le cache en cas de modification.
* Pour les modifications `TOptions`, l’implémentation <xref:Microsoft.Extensions.Options.OptionsMonitor`1> par défaut de <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> a une surcharge qui accepte une ou plusieurs instances <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1>. Chaque instance retourne un `IChangeToken` pour inscrire un rappel de notification de modification pour les modifications des options de suivi.

## <a name="monitor-for-configuration-changes"></a>Surveiller les modifications de configuration

Par défaut, les modèles de ASP.net Core utilisent des [fichiers de configuration JSON](xref:fundamentals/configuration/index#json-configuration-provider) ( *appsettings.json* , *appsettings.Development.jssous* et *appsettings.Production.js* ) pour charger les paramètres de configuration des applications.

Ces fichiers sont configurés avec la méthode d’extension [AddJsonFile(IConfigurationBuilder, chaîne, booléen, booléen)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) sur <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> qui accepte un paramètre `reloadOnChange`. `reloadOnChange` indique si la configuration doit être rechargée en cas de modification d’un fichier. Ce paramètre s’affiche dans la méthode pratique <xref:Microsoft.Extensions.Hosting.Host><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> :

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

La configuration basée sur les fichiers est représentée par <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>. `FileConfigurationSource` utilise <xref:Microsoft.Extensions.FileProviders.IFileProvider> pour surveiller les fichiers.

Par défaut, `IFileMonitor` est fourni par un <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, qui utilise <xref:System.IO.FileSystemWatcher> pour surveiller les modifications des fichiers de configuration.

L’exemple d’application montre les deux implémentations de la surveillance des modifications de configuration. Si l’un des fichiers *appsettings* change, les deux implémentations d’analyse du fichier exécutent du code personnalisé&mdash;l’exemple d’application écrit un message dans la console.

Le `FileSystemWatcher` d’un fichier de configuration peut déclencher plusieurs rappels de jeton pour une même modification du fichier de configuration. Pour vous assurer que le code personnalisé n’est exécuté qu’une seule fois lors du déclenchement de plusieurs rappels de jeton, l’implémentation de l’exemple vérifie les hachages des fichiers. L’exemple utilise le hachage de fichier SHA1. Une nouvelle tentative est implémentée avec une interruption d’une durée exponentielle. Une nouvelle tentative est effectuée, car il peut se produire un verrouillage du fichier qui empêche temporairement le calcul d’un nouveau hachage sur un fichier.

*Utilities/Utilities.cs* :

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a>Jeton de modification de démarrage simple

Inscrivez un rappel `Action` de consommateur de jeton pour les notifications de modification au jeton de rechargement de configuration.

Dans `Startup.Configure` :

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

`config.GetReloadToken()` fournit le jeton. Le rappel est la méthode `InvokeChanged` :

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

Le `state` du rappel est utilisé pour transmettre le `IWebHostEnvironment`, ce qui est utile pour spécifier le bon fichier de configuration *appsettings* à surveiller (par exemple, *appsettings.Development.json* dans l’environnement de développement). Les hachages de fichier sont utilisés pour empêcher l’instruction `WriteConsole` d’être exécutée plusieurs fois en raison de plusieurs rappels de jeton alors que le fichier de configuration n’a été modifié qu’une seule fois.

Ce système s’exécute tant que l’application est en cours d’exécution et ne peut pas être désactivé par l’utilisateur.

### <a name="monitor-configuration-changes-as-a-service"></a>Surveiller les modifications de configuration en tant que service

L’exemple implémente :

* Surveillance de jeton du démarrage de base.
* Surveillance en tant que service.
* Un mécanisme pour activer et désactiver la surveillance.

L’exemple établit une interface `IConfigurationMonitor`.

*Extensions/Configurationmonitor.cs*  :

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

Le constructeur de la classe implémentée, `ConfigurationMonitor`, inscrit un rappel pour les notifications de modification :

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

`config.GetReloadToken()` fournit le jeton. `InvokeChanged` est la méthode de rappel. Le `state` dans cette instance est une référence à l’instance `IConfigurationMonitor` qui est utilisée pour accéder à l’état du monitoring. Deux propriétés sont utilisées :

* `MonitoringEnabled`: Indique si le rappel doit exécuter son code personnalisé.
* `CurrentState`: Décrit l’état d’analyse actuel pour une utilisation dans l’interface utilisateur.

La méthode `InvokeChanged` est similaire à l’approche précédente, excepté que :

* Elle n’exécute pas son code, sauf si `MonitoringEnabled` est `true`.
* Elle indique le `state` actuel dans sa sortie `WriteConsole`.

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

Une instance `ConfigurationMonitor` est inscrite en tant que service dans `Startup.ConfigureServices` :

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

La page Index permet à l’utilisateur de contrôler la surveillance de la configuration. L’instance de `IConfigurationMonitor` est injectée dans le `IndexModel`.

*Pages/Index.cshtml.cs* :

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

Le moniteur de configuration (`_monitor`) est utilisé pour activer ou désactiver l’analyse et définir l’état actuel pour les commentaires sur l’interface utilisateur :

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

Quand `OnPostStartMonitoring` est déclenché, la surveillance est activée et l’état actuel est effacé. Quand `OnPostStopMonitoring` est déclenché, la surveillance est désactivée et l’état est défini de façon à indiquer que la surveillance n’est pas effectuée.

Des boutons dans l’IU activent et désactivent la surveillance.

*Pages/index. cshtml* :

[!code-cshtml[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a>Surveiller les modifications de fichiers mis en cache

Le contenu des fichiers peut être mis en cache en mémoire avec <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>. La mise en cache en mémoire est décrite dans la rubrique [Mise en cache en mémoire](xref:performance/caching/memory). Sans actions supplémentaires, comme l’implémentation décrite ci-dessous, les données *périmées* (obsolètes) sont retournées depuis un cache si la source de données change.

Par exemple, le fait de ne pas prendre en compte l’état d’un fichier source en cache lors du renouvellement d’une période [d’expiration décalée](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) rend obsolètes les données du fichier mis cache. Chaque demande de données renouvelle la période d’expiration décalée, mais le fichier n’est jamais rechargé dans le cache. Toutes les fonctionnalités d’une application qui utilisent le contenu mis en cache d’un fichier sont exposées au risque de recevoir du contenu obsolète.

L’utilisation de jetons de modification dans un scénario de mise en cache de fichier empêche la présence de contenu obsolète dans le cache. L’exemple d’application montre une implémentation de l’approche.

L’exemple utilise `GetFileContent` pour :

* Retourner le contenu du fichier.
* Implémenter un algorithme de nouvelle tentative avec interruption exponentielle pour couvrir les cas où un verrou de fichier empêche temporairement la lecture d’un fichier.

*Utilities/Utilities.cs* :

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

Un `FileService` est créé pour gérer les recherches des fichiers mis en cache. L’appel de la méthode `GetFileContent` du service tente d’obtenir le contenu du fichier à partir du cache en mémoire et le retourne à l’appelant ( *Services/FileService.cs* ).

Si le contenu en cache n’est pas trouvé avec la clé du cache, les actions suivantes sont effectuées :

1. Le contenu du fichier est obtenu à l’aide de `GetFileContent`.
1. Un jeton de modification est obtenu auprès du fournisseur du fichier avec [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*). Le rappel du jeton est déclenché quand le fichier est modifié.
1. Le contenu du fichier est mis en cache avec une période [d’expiration décalée](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration). Le jeton de modification est attaché avec [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) pour supprimer l’entrée du cache si le fichier change alors qu’il est mis en cache.

Dans l’exemple suivant, les fichiers sont stockés dans la [racine de contenu](xref:fundamentals/index#content-root)de l’application. `IWebHostEnvironment.ContentRootFileProvider` est utilisé pour obtenir un <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointage au niveau de l’application `IWebHostEnvironment.ContentRootPath` . `filePath` est obtenue avec [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Services/FileService.cs?name=snippet1)]

`FileService` est inscrit dans le conteneur de service, ainsi que le service de mise en cache en mémoire.

Dans `Startup.ConfigureServices` :

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

Le modèle de page charge le contenu du fichier en utilisant le service.

Dans la méthode `OnGet` de la page Index ( *Pages/Index.cshtml.cs* ) :

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a>Classe CompositeChangeToken

Pour représenter une ou plusieurs instances de `IChangeToken` dans un même objet, utilisez la classe <xref:Microsoft.Extensions.Primitives.CompositeChangeToken>.

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

`HasChanged` sur le jeton composite indique `true` si l’élément `HasChanged` d’un jeton représenté est `true`. `ActiveChangeCallbacks` sur le jeton composite indique `true` si l’élément `ActiveChangeCallbacks` d’un jeton représenté est `true`. Si plusieurs modifications simultanées se produisent, le rappel de modification composite n’est appelé qu’une fois.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Un *jeton de modification* est un module à usage général de bas niveau, utilisé pour effectuer le suivi des modifications de l’état.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="ichangetoken-interface"></a>Interface d’IChangeToken

<xref:Microsoft.Extensions.Primitives.IChangeToken> propage des notifications indiquant qu’une modification s’est produite. `IChangeToken` réside dans l’espace de noms <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>. Pour les applications qui n’utilisent pas le [métapaquet Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), créez une référence au package NuGet [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/).

`IChangeToken` a deux propriétés :

* <xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indique si le jeton déclenche des rappels de façon proactive. Si `ActiveChangedCallbacks` est défini sur `false`, un rappel n’est jamais appelé, et l’application doit interroger `HasChanged` pour connaître les modifications. Il est également possible qu’un jeton ne soit jamais annulé si aucune modification ne se produit, ou si l’écouteur de modifications sous-jacent est supprimé ou désactivé.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> obtient une valeur qui indique si une modification a eu lieu.

L' `IChangeToken` interface inclut la méthode [RegisterChangeCallback (action \<Object> , Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) , qui inscrit un rappel appelé lorsque le jeton a changé. `HasChanged` doit être défini avant que le rappel soit appelé.

## <a name="changetoken-class"></a>Classe ChangeToken

<xref:Microsoft.Extensions.Primitives.ChangeToken> est une classe statique utilisée pour propager des notifications indiquant qu’une modification s’est produite. `ChangeToken` réside dans l’espace de noms <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>. Pour les applications qui n’utilisent pas le [métapaquet Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), créez une référence au package NuGet [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/).

La méthode [ChangeToken. OnChange (Func \<IChangeToken> , action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) inscrit un `Action` à appeler chaque fois que le jeton change :

* `Func<IChangeToken>` produit le jeton.
* `Action` est appelée quand le jeton change.

La surcharge [ChangeToken. OnChange \<TState> (Func \<IChangeToken> , action \<TState> , TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) prend un paramètre supplémentaire `TState` passé dans le consommateur de jetons `Action` .

`OnChange`Retourne un <xref:System.IDisposable>. L’appel de <xref:System.IDisposable.Dispose*> fait que le jeton cesse d’être à l’écoute des modifications suivantes et libère les ressources du jeton.

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a>Exemples d’utilisation de jetons de modification dans ASP.NET Core

Les jetons de modification sont utilisés dans des zones importantes d’ASP.NET Core pour surveiller les modifications apportées aux objets :

* Pour surveiller les modifications apportées aux fichiers, la méthode <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> de <xref:Microsoft.Extensions.FileProviders.IFileProvider> crée un `IChangeToken` pour les fichiers ou le dossier à surveiller.
* Les jetons `IChangeToken` peuvent être ajoutés aux entrées du cache pour déclencher des suppressions dans le cache en cas de modification.
* Pour les modifications `TOptions`, l’implémentation <xref:Microsoft.Extensions.Options.OptionsMonitor`1> par défaut de <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> a une surcharge qui accepte une ou plusieurs instances <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1>. Chaque instance retourne un `IChangeToken` pour inscrire un rappel de notification de modification pour les modifications des options de suivi.

## <a name="monitor-for-configuration-changes"></a>Surveiller les modifications de configuration

Par défaut, les modèles de ASP.net Core utilisent des [fichiers de configuration JSON](xref:fundamentals/configuration/index#json-configuration-provider) ( *appsettings.json* , *appsettings.Development.jssous* et *appsettings.Production.js* ) pour charger les paramètres de configuration des applications.

Ces fichiers sont configurés avec la méthode d’extension [AddJsonFile(IConfigurationBuilder, chaîne, booléen, booléen)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) sur <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> qui accepte un paramètre `reloadOnChange`. `reloadOnChange` indique si la configuration doit être rechargée en cas de modification d’un fichier. Ce paramètre s’affiche dans la méthode pratique <xref:Microsoft.AspNetCore.WebHost><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> :

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

La configuration basée sur les fichiers est représentée par <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>. `FileConfigurationSource` utilise <xref:Microsoft.Extensions.FileProviders.IFileProvider> pour surveiller les fichiers.

Par défaut, `IFileMonitor` est fourni par un <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, qui utilise <xref:System.IO.FileSystemWatcher> pour surveiller les modifications des fichiers de configuration.

L’exemple d’application montre les deux implémentations de la surveillance des modifications de configuration. Si l’un des fichiers *appsettings* change, les deux implémentations d’analyse du fichier exécutent du code personnalisé&mdash;l’exemple d’application écrit un message dans la console.

Le `FileSystemWatcher` d’un fichier de configuration peut déclencher plusieurs rappels de jeton pour une même modification du fichier de configuration. Pour vous assurer que le code personnalisé n’est exécuté qu’une seule fois lors du déclenchement de plusieurs rappels de jeton, l’implémentation de l’exemple vérifie les hachages des fichiers. L’exemple utilise le hachage de fichier SHA1. Une nouvelle tentative est implémentée avec une interruption d’une durée exponentielle. Une nouvelle tentative est effectuée, car il peut se produire un verrouillage du fichier qui empêche temporairement le calcul d’un nouveau hachage sur un fichier.

*Utilities/Utilities.cs* :

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a>Jeton de modification de démarrage simple

Inscrivez un rappel `Action` de consommateur de jeton pour les notifications de modification au jeton de rechargement de configuration.

Dans `Startup.Configure` :

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

`config.GetReloadToken()` fournit le jeton. Le rappel est la méthode `InvokeChanged` :

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

Le `state` du rappel est utilisé pour transmettre le `IHostingEnvironment`, ce qui est utile pour spécifier le bon fichier de configuration *appsettings* à surveiller (par exemple, *appsettings.Development.json* dans l’environnement de développement). Les hachages de fichier sont utilisés pour empêcher l’instruction `WriteConsole` d’être exécutée plusieurs fois en raison de plusieurs rappels de jeton alors que le fichier de configuration n’a été modifié qu’une seule fois.

Ce système s’exécute tant que l’application est en cours d’exécution et ne peut pas être désactivé par l’utilisateur.

### <a name="monitor-configuration-changes-as-a-service"></a>Surveiller les modifications de configuration en tant que service

L’exemple implémente :

* Surveillance de jeton du démarrage de base.
* Surveillance en tant que service.
* Un mécanisme pour activer et désactiver la surveillance.

L’exemple établit une interface `IConfigurationMonitor`.

*Extensions/Configurationmonitor.cs*  :

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

Le constructeur de la classe implémentée, `ConfigurationMonitor`, inscrit un rappel pour les notifications de modification :

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

`config.GetReloadToken()` fournit le jeton. `InvokeChanged` est la méthode de rappel. Le `state` dans cette instance est une référence à l’instance `IConfigurationMonitor` qui est utilisée pour accéder à l’état du monitoring. Deux propriétés sont utilisées :

* `MonitoringEnabled`: Indique si le rappel doit exécuter son code personnalisé.
* `CurrentState`: Décrit l’état d’analyse actuel pour une utilisation dans l’interface utilisateur.

La méthode `InvokeChanged` est similaire à l’approche précédente, excepté que :

* Elle n’exécute pas son code, sauf si `MonitoringEnabled` est `true`.
* Elle indique le `state` actuel dans sa sortie `WriteConsole`.

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

Une instance `ConfigurationMonitor` est inscrite en tant que service dans `Startup.ConfigureServices` :

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

La page Index permet à l’utilisateur de contrôler la surveillance de la configuration. L’instance de `IConfigurationMonitor` est injectée dans le `IndexModel`.

*Pages/Index.cshtml.cs* :

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

Le moniteur de configuration (`_monitor`) est utilisé pour activer ou désactiver l’analyse et définir l’état actuel pour les commentaires sur l’interface utilisateur :

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

Quand `OnPostStartMonitoring` est déclenché, la surveillance est activée et l’état actuel est effacé. Quand `OnPostStopMonitoring` est déclenché, la surveillance est désactivée et l’état est défini de façon à indiquer que la surveillance n’est pas effectuée.

Des boutons dans l’IU activent et désactivent la surveillance.

*Pages/index. cshtml* :

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a>Surveiller les modifications de fichiers mis en cache

Le contenu des fichiers peut être mis en cache en mémoire avec <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>. La mise en cache en mémoire est décrite dans la rubrique [Mise en cache en mémoire](xref:performance/caching/memory). Sans actions supplémentaires, comme l’implémentation décrite ci-dessous, les données *périmées* (obsolètes) sont retournées depuis un cache si la source de données change.

Par exemple, le fait de ne pas prendre en compte l’état d’un fichier source en cache lors du renouvellement d’une période [d’expiration décalée](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) rend obsolètes les données du fichier mis cache. Chaque demande de données renouvelle la période d’expiration décalée, mais le fichier n’est jamais rechargé dans le cache. Toutes les fonctionnalités d’une application qui utilisent le contenu mis en cache d’un fichier sont exposées au risque de recevoir du contenu obsolète.

L’utilisation de jetons de modification dans un scénario de mise en cache de fichier empêche la présence de contenu obsolète dans le cache. L’exemple d’application montre une implémentation de l’approche.

L’exemple utilise `GetFileContent` pour :

* Retourner le contenu du fichier.
* Implémenter un algorithme de nouvelle tentative avec interruption exponentielle pour couvrir les cas où un verrou de fichier empêche temporairement la lecture d’un fichier.

*Utilities/Utilities.cs* :

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

Un `FileService` est créé pour gérer les recherches des fichiers mis en cache. L’appel de la méthode `GetFileContent` du service tente d’obtenir le contenu du fichier à partir du cache en mémoire et le retourne à l’appelant ( *Services/FileService.cs* ).

Si le contenu en cache n’est pas trouvé avec la clé du cache, les actions suivantes sont effectuées :

1. Le contenu du fichier est obtenu à l’aide de `GetFileContent`.
1. Un jeton de modification est obtenu auprès du fournisseur du fichier avec [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*). Le rappel du jeton est déclenché quand le fichier est modifié.
1. Le contenu du fichier est mis en cache avec une période [d’expiration décalée](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration). Le jeton de modification est attaché avec [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) pour supprimer l’entrée du cache si le fichier change alors qu’il est mis en cache.

Dans l’exemple suivant, les fichiers sont stockés dans la [racine de contenu](xref:fundamentals/index#content-root)de l’application. [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) est utilisé pour obtenir un <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointant vers <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> de l’application. `filePath` est obtenue avec [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

`FileService` est inscrit dans le conteneur de service, ainsi que le service de mise en cache en mémoire.

Dans `Startup.ConfigureServices` :

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

Le modèle de page charge le contenu du fichier en utilisant le service.

Dans la méthode `OnGet` de la page Index ( *Pages/Index.cshtml.cs* ) :

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a>Classe CompositeChangeToken

Pour représenter une ou plusieurs instances de `IChangeToken` dans un même objet, utilisez la classe <xref:Microsoft.Extensions.Primitives.CompositeChangeToken>.

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

`HasChanged` sur le jeton composite indique `true` si l’élément `HasChanged` d’un jeton représenté est `true`. `ActiveChangeCallbacks` sur le jeton composite indique `true` si l’élément `ActiveChangeCallbacks` d’un jeton représenté est `true`. Si plusieurs modifications simultanées se produisent, le rappel de modification composite n’est appelé qu’une fois.

::: moniker-end

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
