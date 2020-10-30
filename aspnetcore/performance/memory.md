---
title: Gestion de la mémoire et modèles dans ASP.NET Core
author: rick-anderson
description: Découvrez comment fonctionne la gestion de la mémoire dans ASP.NET Core et comment le garbage collector (GC) fonctionne.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
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
uid: performance/memory
ms.openlocfilehash: 6d2a89ec7c64728bc585ad235293f2277f9a66f7
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061481"
---
# <a name="memory-management-and-garbage-collection-gc-in-aspnet-core"></a>Gestion de la mémoire et garbage collection (GC) dans ASP.NET Core

Par [Sébastien Ros](https://github.com/sebastienros) et [Rick Anderson](https://twitter.com/RickAndMSFT)

La gestion de la mémoire est complexe, même dans une infrastructure gérée telle que .NET. L’analyse et la compréhension des problèmes de mémoire peuvent être difficiles. Cet article :

* A été motivée par de nombreuses *fuites de mémoire* et les problèmes de *GC ne fonctionnaient pas* . La plupart de ces problèmes ont été provoqués par le fait de ne pas comprendre comment fonctionne la consommation de mémoire dans .NET Core, ou de ne pas comprendre comment elle est mesurée.
* Illustre l’utilisation de la mémoire problématique et suggère d’autres approches.

## <a name="how-garbage-collection-gc-works-in-net-core"></a>Fonctionnement d’garbage collection (GC) dans .NET Core

Le garbage collector alloue des segments de tas où chaque segment est une plage contiguë de mémoire. Les objets placés dans le tas sont classés en trois générations : 0, 1 ou 2. La génération détermine la fréquence à laquelle le garbage collector tente de libérer de la mémoire sur les objets gérés qui ne sont plus référencés par l’application. Les générations numérotées inférieures sont plus fréquentes.

Les objets sont déplacés d’une génération à l’autre en fonction de leur durée de vie. À mesure que les objets vivent plus longtemps, ils sont déplacés vers une génération plus élevée. Comme mentionné précédemment, les générations plus élevées sont moins souvent GC. Les objets éphémères à courte durée restent toujours dans la génération 0. Par exemple, les objets qui sont référencés pendant la durée de vie d’une requête Web sont à courte durée de vie. Les [singletons](xref:fundamentals/dependency-injection#service-lifetimes) au niveau de l’application migrent généralement vers la génération 2.

Lors du démarrage d’une application ASP.NET Core, le garbage collector :

* Réserve de la mémoire pour les segments de tas initiaux.
* Valide une petite partie de la mémoire lors du chargement du Runtime.

Les allocations de mémoire précédentes sont effectuées pour des raisons de performances. L’avantage en termes de performances provient des segments de segment de mémoire contigus.

### <a name="call-gccollect"></a>Appelez GC. Rassembler

Appel de [gc. Collecter](xref:System.GC.Collect*) explicitement :

* **Ne doivent pas** être effectuées par les applications de production ASP.net core.
* Est utile lors de l’examen des fuites de mémoire.
* Lors de l’examen, vérifie que le GC a supprimé tous les objets en suspens de la mémoire afin que la mémoire puisse être mesurée.

## <a name="analyzing-the-memory-usage-of-an-app"></a>Analyse de l’utilisation de la mémoire d’une application

Les outils dédiés peuvent vous aider à analyser l’utilisation de la mémoire :

- Comptage des références d’objets
- Mesure de l’impact du GC sur l’utilisation du processeur
- Mesure de l’espace mémoire utilisé pour chaque génération

Utilisez les outils suivants pour analyser l’utilisation de la mémoire :

* [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace): peut être utilisé sur des ordinateurs de production.
* [Analyser l’utilisation de la mémoire sans le débogueur Visual Studio](/visualstudio/profiling/memory-usage-without-debugging2)
* [Profiler l’utilisation de la mémoire dans Visual Studio](/visualstudio/profiling/memory-usage)

### <a name="detecting-memory-issues"></a>Détection des problèmes de mémoire

Le gestionnaire des tâches peut être utilisé pour avoir une idée de la quantité de mémoire utilisée par une application ASP.NET. Valeur de la mémoire du gestionnaire des tâches :

* Représente la quantité de mémoire utilisée par le processus ASP.NET.
* Comprend les objets vivants et autres consommateurs de mémoire de l’application, tels que l’utilisation de la mémoire native.

Si la valeur de la mémoire du gestionnaire des tâches augmente indéfiniment et n’est jamais aplatie, l’application présente une fuite de mémoire. Les sections suivantes montrent et expliquent plusieurs modèles d’utilisation de la mémoire.

## <a name="sample-display-memory-usage-app"></a>Exemple d’application de l’utilisation de la mémoire

L' [exemple d’application MemoryLeak](https://github.com/sebastienros/memoryleak) est disponible sur GitHub. L’application MemoryLeak :

* Comprend un contrôleur de diagnostic qui collecte la mémoire en temps réel et les données de catalogue global pour l’application.
* Contient une page d’index qui affiche la mémoire et les données de catalogue global. La page d’index est actualisée chaque seconde.
* Contient un contrôleur d’API qui fournit divers modèles de charge de mémoire.
* N’est pas un outil pris en charge, mais il peut être utilisé pour afficher les modèles d’utilisation de la mémoire des applications ASP.NET Core.

Exécutez MemoryLeak. La mémoire allouée augmente lentement jusqu’à ce qu’un GC se produise. La mémoire augmente, car l’outil alloue un objet personnalisé pour capturer les données. L’illustration suivante montre la page d’index MemoryLeak lorsqu’un GC de génération 0 se produit. Le graphique affiche 0 RPS (demandes par seconde) car aucun point de terminaison d’API du contrôleur d’API n’a été appelé.

![graphique précédent](memory/_static/0RPS.png)

Le graphique affiche deux valeurs pour l’utilisation de la mémoire :

- Allouée : quantité de mémoire occupée par les objets managés
- Plage de [travail](/windows/win32/memory/working-set): ensemble de pages dans l’espace d’adressage virtuel du processus qui résident actuellement dans la mémoire physique. La plage de travail affichée correspond à la même valeur que celle affichée par le gestionnaire des tâches.

### <a name="transient-objects"></a>Objets temporaires

L’API suivante crée une instance de chaîne de 10 Ko et la retourne au client. À chaque demande, un nouvel objet est alloué en mémoire et écrit dans la réponse. Les chaînes sont stockées en tant que caractères UTF-16 dans .NET, de sorte que chaque caractère occupe 2 octets en mémoire.

```csharp
[HttpGet("bigstring")]
public ActionResult<string> GetBigString()
{
    return new String('x', 10 * 1024);
}
```

Le graphique suivant est généré avec une charge relativement faible dans pour montrer comment les allocations de mémoire sont affectées par le GC.

![graphique précédent](memory/_static/bigstring.png)

Le graphique précédent montre les éléments suivants :

* 4 Ko (requêtes par seconde).
* Les collections GC de génération 0 se produisent toutes les deux secondes.
* La plage de travail est constante à environ 500 Mo.
* L’UC est de 12%.
* La consommation et la libération de la mémoire (via GC) sont stables.

Le graphique suivant est pris au débit maximal qui peut être géré par l’ordinateur.

![graphique précédent](memory/_static/bigstring2.png)

Le graphique précédent montre les éléments suivants :

* 22K RPS
* Les collections GC de génération 0 se produisent plusieurs fois par seconde.
* Les collections de génération 1 sont déclenchées, car l’application a alloué beaucoup plus de mémoire par seconde.
* La plage de travail est constante à environ 500 Mo.
* L’UC est de 33%.
* La consommation et la libération de la mémoire (via GC) sont stables.
* UC (33%) n’est pas surexploitée, par conséquent le garbage collection peut suivre un nombre élevé d’allocations.

### <a name="workstation-gc-vs-server-gc"></a>GC de station de travail et GC de serveur

Le garbage collector .NET a deux modes différents :

* **GC de station de travail** : optimisé pour le bureau.
* **GC du serveur** . GC par défaut pour les applications ASP.NET Core. Optimisé pour le serveur.

Le mode GC peut être défini explicitement dans le fichier projet ou dans le *runtimeconfig.js* fichier de l’application publiée. Le balisage suivant illustre la définition `ServerGarbageCollection` dans le fichier projet :

```xml
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
</PropertyGroup>
```

La modification du `ServerGarbageCollection` fichier projet nécessite que l’application soit reconstruite.

**Remarque :** Le garbage collection serveur n’est **pas** disponible sur les machines avec un seul cœur. Pour plus d'informations, consultez <xref:System.Runtime.GCSettings.IsServerGC>.

L’illustration suivante montre le profil de mémoire sous un RPS de 5 Ko à l’aide du GC de station de travail.

![graphique précédent](memory/_static/workstation.png)

Les différences entre ce graphique et la version du serveur sont significatives :

- La plage de travail passe de 500 Mo à 70 Mo.
- Le GC effectue des collections de génération 0 plusieurs fois par seconde, plutôt que toutes les deux secondes.
- GC passe de 300 Mo à 10 Mo.

Dans un environnement de serveur Web classique, l’utilisation du processeur est plus importante que la mémoire, par conséquent, le garbage collector du serveur est préférable. Si l’utilisation de la mémoire est élevée et que l’utilisation du processeur est relativement faible, le garbage collector de la station de travail peut être plus performant. Par exemple, une haute densité hébergeant plusieurs applications Web où la mémoire est rare.

<a name="sc"></a>

### <a name="gc-using-docker-and-small-containers"></a>GC avec l’ancrage et les petits conteneurs

Lorsque plusieurs applications en conteneur s’exécutent sur un ordinateur, le garbage collector de station de travail peut être plus conforme que le GC de serveur. Pour plus d’informations, consultez [exécution avec le garbage collection de serveur dans un petit conteneur](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) et [exécution avec le garbage collection de serveur dans un petit scénario de conteneur, partie 1 : limite inconditionnelle pour le tas GC](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/).

### <a name="persistent-object-references"></a>Références d’objets persistants

Le GC ne peut pas libérer des objets référencés. Les objets référencés, mais qui ne sont plus nécessaires, provoquent une fuite de mémoire. Si l’application alloue fréquemment des objets et ne parvient pas à les libérer une fois qu’ils ne sont plus nécessaires, l’utilisation de la mémoire augmente au fil du temps.

L’API suivante crée une instance de chaîne de 10 Ko et la retourne au client. La différence avec l’exemple précédent est que cette instance est référencée par un membre statique, ce qui signifie qu’elle n’est jamais disponible pour la collection.

```csharp
private static ConcurrentBag<string> _staticStrings = new ConcurrentBag<string>();

[HttpGet("staticstring")]
public ActionResult<string> GetStaticString()
{
    var bigString = new String('x', 10 * 1024);
    _staticStrings.Add(bigString);
    return bigString;
}
```

Le code précédent :

* Est un exemple de fuite de mémoire classique.
* Avec des appels fréquents, provoque l’augmentation de la mémoire de l’application jusqu’à ce que le processus se bloque avec une `OutOfMemory` exception.

![graphique précédent](memory/_static/eternal.png)

Dans l’image précédente :

* Le test de charge du `/api/staticstring` point de terminaison provoque une augmentation linéaire de la mémoire.
* Le GC tente de libérer de la mémoire à mesure que la sollicitation de la mémoire augmente, en appelant une collection de génération 2.
* Le GC ne peut pas libérer la mémoire perdue. Allouée et plage de travail augmentent avec le temps.

Certains scénarios, tels que la mise en cache, requièrent que les références d’objets soient maintenues jusqu’à ce que la sollicitation de la mémoire les force à libérer. La <xref:System.WeakReference> classe peut être utilisée pour ce type de code de mise en cache. Un `WeakReference` objet est collecté en conditions de mémoire. L’implémentation par défaut de <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> utilise `WeakReference` .

### <a name="native-memory"></a>Mémoire Native

Certains objets .NET Core s’appuient sur la mémoire native. La mémoire native **ne peut pas** être collectée par le gc. L’objet .NET utilisant la mémoire Native doit le libérer à l’aide du code natif.

.NET fournit l' <xref:System.IDisposable> interface pour permettre aux développeurs de libérer de la mémoire native. Même si <xref:System.IDisposable.Dispose*> n’est pas appelé, les classes correctement implémentées appellent `Dispose` lorsque le [finaliseur](/dotnet/csharp/programming-guide/classes-and-structs/destructors) s’exécute.

Considérez le code suivant :

```csharp
[HttpGet("fileprovider")]
public void GetFileProvider()
{
    var fp = new PhysicalFileProvider(TempPath);
    fp.Watch("*.*");
}
```

[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) étant une classe managée, toute instance est collectée à la fin de la demande.

L’illustration suivante montre le profil de mémoire lors de l’appel de l' `fileprovider` API en continu.

![graphique précédent](memory/_static/fileprovider.png)

Le graphique précédent présente un problème évident avec l’implémentation de cette classe, car elle continue d’améliorer l’utilisation de la mémoire. Il s’agit d’un problème connu qui fait l’objet d’un suivi dans [ce numéro](https://github.com/dotnet/aspnetcore/issues/3110).

La même fuite peut se produire dans le code utilisateur, par l’un des éléments suivants :

* Impossible de libérer la classe correctement.
* Oublier d’appeler la `Dispose` méthode des objets dépendants qui doivent être supprimés.

### <a name="large-objects-heap"></a>Tas d’objets volumineux

Les cycles d’allocation de mémoire/libre fréquents peuvent fragmenter la mémoire, en particulier lors de l’allocation de gros blocs de mémoire. Les objets sont alloués dans des blocs de mémoire contigus. Pour limiter la fragmentation, lorsque le garbage collector libère de la mémoire, il tente de le défragmenter. Ce processus est appelé **compactage** . Le compactage implique le déplacement d’objets. Le déplacement d’objets volumineux impose une baisse des performances. Pour cette raison, le GC crée une zone de mémoire spéciale pour les objets _volumineux_ , appelée tas d’objets [volumineux](/dotnet/standard/garbage-collection/large-object-heap) (LOH). Les objets dont la taille est supérieure à 85 000 octets (environ 83 Ko) sont :

* Placé sur le LOH.
* Non compacté.
* Collecté au cours de la génération 2 gc.

Lorsque le LOH est plein, le GC déclenche une collection de génération 2. Collections de génération 2 :

* Sont par nature lentes.
* Surchargez également le coût du déclenchement d’une collection sur toutes les autres générations.

Le code suivant compacte le LOH immédiatement :

```csharp
GCSettings.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactOnce;
GC.Collect();
```

<xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode>Pour plus d’informations sur le compactage du Loh, consultez.

Dans les conteneurs à l’aide de .NET Core 3,0 et versions ultérieures, le LOH est automatiquement compacté.

L’API suivante illustre ce comportement :

```csharp
[HttpGet("loh/{size=85000}")]
public int GetLOH1(int size)
{
   return new byte[size].Length;
}
```

Le graphique suivant montre le profil de mémoire de l’appel du `/api/loh/84975` point de terminaison, sous charge maximale :

![graphique précédent](memory/_static/loh1.png)

Le graphique suivant montre le profil de mémoire de l’appel du `/api/loh/84976` point de terminaison, en allouant *juste un octet de plus* :

![graphique précédent](memory/_static/loh2.png)

Remarque : la `byte[]` structure contient des octets de surcharge. C’est pourquoi 84 976 octets déclenche la limite de 85 000.

Comparaison des deux graphiques précédents :

* La plage de travail est similaire pour les deux scénarios, environ 450 Mo.
* Les requêtes sous LOH (84 975 octets) affichent principalement des collections de génération 0.
* Les requêtes sur LOH génèrent des collections de génération constante 2. Les collections de génération 2 sont coûteuses. Une plus grande UC est requise et le débit chute presque 50%.

Les objets volumineux temporaires sont particulièrement problématiques, car ils génèrent des catalogues globaux Gen.

Pour des performances optimales, l’utilisation d’objets volumineux doit être réduite. Si possible, fractionnez les objets volumineux. Par exemple, l’intergiciel (middleware) de [mise en cache des réponses](xref:performance/caching/response) dans ASP.net Core fractionner les entrées de cache en blocs d’une taille inférieure à 85 000 octets.

Les liens suivants présentent l’approche ASP.NET Core pour la conservation des objets dans le cadre de la limite LOH :

* [ResponseCaching/Streams/StreamUtilities. cs](https://github.com/dotnet/AspNetCore/blob/v3.0.0/src/Middleware/ResponseCaching/src/Streams/StreamUtilities.cs#L16)
* [ResponseCaching/MemoryResponseCache. cs](https://github.com/aspnet/ResponseCaching/blob/c1cb7576a0b86e32aec990c22df29c780af29ca5/src/Microsoft.AspNetCore.ResponseCaching/Internal/MemoryResponseCache.cs#L55)

Pour plus d'informations, consultez les pages suivantes :

* [Segment de mémoire Large Object non couvert](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [Tas d’objets volumineux](/dotnet/standard/garbage-collection/large-object-heap)

### <a name="httpclient"></a>HttpClient

Une utilisation incorrecte <xref:System.Net.Http.HttpClient> de peut entraîner une fuite de ressources. Les ressources système, telles que les connexions de base de données, les sockets, les handles de fichiers, etc. :

* Sont plus rares que la mémoire.
* Sont plus problématiques quand la mémoire est perdue.

Les développeurs .NET expérimentés savent appeler <xref:System.IDisposable.Dispose*> sur les objets qui implémentent <xref:System.IDisposable> . Ne pas supprimer des objets qui implémentent `IDisposable` en général entraîne une fuite de mémoire ou une fuite de ressources système.

`HttpClient` implémente `IDisposable` , mais ne doit **pas** être supprimé à chaque appel. Au lieu de cela, `HttpClient` doit être réutilisé.

Le point de terminaison suivant crée et supprime une nouvelle  `HttpClient` instance à chaque demande :

```csharp
[HttpGet("httpclient1")]
public async Task<int> GetHttpClient1(string url)
{
    using (var httpClient = new HttpClient())
    {
        var result = await httpClient.GetAsync(url);
        return (int)result.StatusCode;
    }
}
```

En cours de chargement, les messages d’erreur suivants sont enregistrés :

```
fail: Microsoft.AspNetCore.Server.Kestrel[13]
      Connection id "0HLG70PBE1CR1", Request id "0HLG70PBE1CR1:00000031":
      An unhandled exception was thrown by the application.
System.Net.Http.HttpRequestException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted --->
    System.Net.Sockets.SocketException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted
   at System.Net.Http.ConnectHelper.ConnectAsync(String host, Int32 port,
    CancellationToken cancellationToken)
```

Même si les `HttpClient` instances sont supprimées, la connexion réseau réelle prend un certain temps pour être libérée par le système d’exploitation. En créant continuellement de nouvelles connexions, l' _épuisement des ports_ se produit. Chaque connexion cliente requiert son propre port client.

Pour empêcher l’épuisement de port, il est possible de réutiliser la même `HttpClient` instance de :

```csharp
private static readonly HttpClient _httpClient = new HttpClient();

[HttpGet("httpclient2")]
public async Task<int> GetHttpClient2(string url)
{
    var result = await _httpClient.GetAsync(url);
    return (int)result.StatusCode;
}
```

L' `HttpClient` instance est libérée lorsque l’application s’arrête. Cet exemple montre que toutes les ressources jetables ne doivent pas être supprimées après chaque utilisation.

Pour plus d’informations sur la façon de gérer la durée de vie d’une instance, consultez les rubriques suivantes `HttpClient` :

* [HttpClient et gestion de la durée de vie](../fundamentals/http-requests.md#httpclient-and-lifetime-management)
* [Blog de la fabrique HTTPClient](https://devblogs.microsoft.com/aspnet/asp-net-core-2-1-preview1-introducing-httpclient-factory/)
 
### <a name="object-pooling"></a>Mise en pool d’objets

L’exemple précédent a montré comment l' `HttpClient` instance peut être rendue statique et réutilisée par toutes les demandes. La réutilisation empêche les ressources de manquer de ressources.

Mise en pool d’objets :

* Utilise le modèle de réutilisation.
* Est conçu pour les objets chers à créer.

Un pool est une collection d’objets préinitialisés qui peuvent être réservés et libérés entre les threads. Les pools peuvent définir des règles d’allocation, telles que les limites, les tailles prédéfinies ou le taux de croissance.

Le package NuGet [Microsoft. extensions. ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) contient des classes qui permettent de gérer de tels pools.

Le point de terminaison d’API suivant instancie une `byte` mémoire tampon remplie avec des nombres aléatoires pour chaque requête :

```csharp
        [HttpGet("array/{size}")]
        public byte[] GetArray(int size)
        {
            var random = new Random();
            var array = new byte[size];
            random.NextBytes(array);

            return array;
        }
```

Le graphique suivant affiche l’appel de l’API précédente avec une charge modérée :

![graphique précédent](memory/_static/array.png)

Dans le graphique précédent, les collections de génération 0 se produisent environ une fois par seconde.

Le code précédent peut être optimisé en regroupant la `byte` mémoire tampon à l’aide de [ \<T> ArrayPool](xref:System.Buffers.ArrayPool`1). Une instance statique est réutilisée entre les requêtes.

Ce qui diffère avec cette approche, c’est qu’un objet regroupé est retourné à partir de l’API. Cela signifie que :

* L’objet est hors de votre contrôle dès que vous retournez à partir de la méthode.
* Vous ne pouvez pas libérer l’objet.

Pour configurer la suppression de l’objet :

* Encapsulez le tableau mis en pool dans un objet jetable.
* Enregistrez l’objet regroupé avec [HttpContext. Response. RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).

`RegisterForDispose` prend en charge l’appel de `Dispose` sur l’objet cible afin qu’il ne soit libéré qu’à la fin de la requête http.

```csharp
private static ArrayPool<byte> _arrayPool = ArrayPool<byte>.Create();

private class PooledArray : IDisposable
{
    public byte[] Array { get; private set; }

    public PooledArray(int size)
    {
        Array = _arrayPool.Rent(size);
    }

    public void Dispose()
    {
        _arrayPool.Return(Array);
    }
}

[HttpGet("pooledarray/{size}")]
public byte[] GetPooledArray(int size)
{
    var pooledArray = new PooledArray(size);

    var random = new Random();
    random.NextBytes(pooledArray.Array);

    HttpContext.Response.RegisterForDispose(pooledArray);

    return pooledArray.Array;
}
```

L’application de la même charge que la version non regroupée produit le graphique suivant :

![graphique précédent](memory/_static/pooledarray.png)

La principale différence est le nombre d’octets alloués et, par conséquent, beaucoup moins de collections de génération 0.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Garbage collection](/dotnet/standard/garbage-collection/)
* [Fonctionnement de différents modes GC avec le visualiseur concurrentiel](https://blogs.msdn.microsoft.com/seteplia/2017/01/05/understanding-different-gc-modes-with-concurrency-visualizer/)
* [Segment de mémoire Large Object non couvert](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [Tas d’objets volumineux](/dotnet/standard/garbage-collection/large-object-heap)