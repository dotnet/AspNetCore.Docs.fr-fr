---
title: Meilleures pratiques en matière de performances de ASP.NET Core
author: mjrousos
description: Conseils pour améliorer les performances dans les applications ASP.NET Core et éviter les problèmes de performances courants.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 04/06/2020
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
uid: performance/performance-best-practices
ms.openlocfilehash: a3fc398569fafefc0b4634e80433a5d4e0e1b4ff
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061000"
---
# <a name="aspnet-core-performance-best-practices"></a>Meilleures pratiques en matière de performances de ASP.NET Core

Par [Mike Rousos](https://github.com/mjrousos)

Cet article fournit des instructions pour les meilleures pratiques en matière de performances avec ASP.NET Core.

## <a name="cache-aggressively"></a>Mettre en cache de façon agressive

La mise en cache est présentée dans plusieurs sections de ce document. Pour plus d'informations, consultez <xref:performance/caching/response>.

## <a name="understand-hot-code-paths"></a>Comprendre les chemins de code à chaud

Dans ce document, un *chemin de code réactif* est défini comme un chemin de code qui est fréquemment appelé et où la majeure partie du temps d’exécution se produit. Les chemins de code à chaud limitent généralement l’évolutivité et les performances des applications et sont abordés dans plusieurs parties de ce document.

## <a name="avoid-blocking-calls"></a>Éviter les appels de blocage

ASP.NET Core applications doivent être conçues pour traiter plusieurs demandes simultanément. Les API asynchrones permettent à un petit pool de threads de gérer des milliers de demandes simultanées en n’attendant pas les appels de blocage. Au lieu d’attendre la fin d’une tâche synchrone de longue durée, le thread peut travailler sur une autre requête.

Un problème de performances courant dans les applications de ASP.NET Core consiste à bloquer les appels qui pourraient être asynchrones. De nombreux appels de blocage synchrones conduisent à la [privation de pool de threads](/archive/blogs/vancem/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall) et aux temps de réponse dégradés.

**Ne pas** :

* Bloquer l’exécution asynchrone en appelant [Task. Wait](/dotnet/api/system.threading.tasks.task.wait) ou [Task. Result](/dotnet/api/system.threading.tasks.task-1.result).
* Acquérir des verrous dans les chemins de code communs. Les applications ASP.NET Core sont plus performantes quand elles sont conçues pour exécuter du code en parallèle.
* Appelez [Task. Run](/dotnet/api/system.threading.tasks.task.run) et attendez-y immédiatement. ASP.NET Core exécute déjà le code d’application sur des threads de pool de threads normaux, donc l’appel de Task. Run entraîne uniquement une planification de pool de threads superflue. Même si le code planifié bloque un thread, Task. Run n’empêche pas cela.

**Procédez** comme suit :

* Rendez les [chemins de code à chaud](#understand-hot-code-paths) asynchrones.
* Appeler l’accès aux données, les e/s et les API d’opérations de longue durée de manière asynchrone si une API asynchrone est disponible. N' **not** utilisez pas [Task. Run](/dotnet/api/system.threading.tasks.task.run) pour rendre une API synchrone asynchrone.
* Rendez les actions de contrôleur/ Razor page asynchrones. L’ensemble de la pile des appels est asynchrone afin de tirer parti des modèles [Async/await](/dotnet/csharp/programming-guide/concepts/async/) .

Un profileur, tel que [PerfView](https://github.com/Microsoft/perfview), peut être utilisé pour rechercher les threads ajoutés fréquemment au [pool de threads](/windows/desktop/procthread/thread-pools). L' `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start` événement indique qu’un thread a été ajouté au pool de threads. <!--  For more information, see [async guidance docs](TBD-Link_To_Davifowl_Doc)  -->

## <a name="return-ienumerablet-or-iasyncenumerablet"></a>Retourne IEnumerable \<T> ou IAsyncEnumerable\<T>

`IEnumerable<T>`Le retour à partir d’une action entraîne une itération de collection synchrone par le sérialiseur. Le résultat est le blocage des appels et un potentiel pour la privation du pool de threads. Pour éviter l’énumération synchrone, utilisez `ToListAsync` avant de retourner l’énumérable.

À partir de ASP.NET Core 3,0, `IAsyncEnumerable<T>` peut être utilisé comme alternative à l' `IEnumerable<T>` énumération asynchrone. Pour plus d’informations, consultez [types de retours d’action de contrôleur](xref:web-api/action-return-types#return-ienumerablet-or-iasyncenumerablet).

## <a name="minimize-large-object-allocations"></a>Réduire les allocations d’objets volumineux

Le [garbage collector .net Core](/dotnet/standard/garbage-collection/) gère l’allocation et la libération de mémoire automatiquement dans les applications ASP.net core. La garbage collection automatique signifie généralement que les développeurs n’ont pas besoin de se soucier de la libération ou de la libération de la mémoire. Toutefois, le nettoyage des objets non référencés prend du temps processeur, ce qui permet aux développeurs de réduire l’allocation des objets dans les [chemins de code à chaud](#understand-hot-code-paths). Le garbage collection est particulièrement onéreux sur les objets volumineux (> de 85 Ko). Les objets volumineux sont stockés sur le [tas d’objets volumineux](/dotnet/standard/garbage-collection/large-object-heap) et nécessitent une garbage collection complète (génération 2) à nettoyer. Contrairement aux collections de génération 0 et de génération 1, une collection de génération 2 requiert une suspension temporaire de l’exécution de l’application. L’allocation et la désallocation fréquentes d’objets volumineux peuvent entraîner des performances incohérentes.

Recommandations :

* **Envisagez** de mettre en cache des objets volumineux fréquemment utilisés. La mise en cache des objets volumineux empêche les allocations coûteuses.
* **Effectuez** des tampons de pool à l’aide d’un [ArrayPool \<T>](/dotnet/api/system.buffers.arraypool-1) pour stocker de grands tableaux.
* **N'** allouez pas de nombreux objets volumineux à courte durée de vie sur les [chemins de code à chaud](#understand-hot-code-paths).

Les problèmes de mémoire, tels que le précédent, peuvent être diagnostiqués en examinant les statistiques de garbage collection (GC) dans [PerfView](https://github.com/Microsoft/perfview) et en examinant :

* Temps de pause de garbage collection.
* Pourcentage du temps processeur passé dans garbage collection.
* Nombre de garbage collection de génération 0, 1 et 2.

Pour plus d’informations, consultez [garbage collection et performance](/dotnet/standard/garbage-collection/performance).

## <a name="optimize-data-access-and-io"></a>Optimiser l’accès aux données et les e/s

Les interactions avec un magasin de données et d’autres services distants sont souvent les parties les plus lentes d’une application ASP.NET Core. La lecture et l’écriture de données efficaces sont essentielles pour de bonnes performances.

Recommandations :

* **Appelez toutes** les API d’accès aux données de manière asynchrone.
* **Ne** récupérez pas plus de données que nécessaire. Écrivez des requêtes pour retourner uniquement les données nécessaires pour la requête HTTP actuelle.
* **Envisagez** de mettre en cache les données fréquemment sollicitées récupérées à partir d’une base de données ou d’un service distant, si les données obsolètes sont légèrement obsolètes. Selon le scénario, utilisez un [MemoryCache](xref:performance/caching/memory) ou un [DistributedCache](xref:performance/caching/distributed). Pour plus d'informations, consultez <xref:performance/caching/response>.
* Réduisez les allers **-** retours réseau. L’objectif est de récupérer les données requises dans un appel unique plutôt que plusieurs appels.
* **Utilisez** des [requêtes sans suivi](/ef/core/querying/tracking#no-tracking-queries) dans Entity Framework Core lors de l’accès à des données en lecture seule. EF Core pouvez retourner les résultats des requêtes sans suivi plus efficacement.
* **Filtrez** et regroupez des requêtes LINQ (avec des `.Where` `.Select` instructions, ou `.Sum` , par exemple) pour que le filtrage soit effectué par la base de données.
* **Supposons** que EF Core résout certains opérateurs de requête sur le client, ce qui peut entraîner une exécution inefficace de la requête. Pour plus d’informations, consultez problèmes de performances de l' [évaluation du client](/ef/core/querying/client-eval#client-evaluation-performance-issues).
* **N’utilisez pas** de requêtes de projection sur des collections, ce qui peut entraîner l’exécution de requêtes SQL « N + 1 ». Pour plus d’informations, consultez [optimisation des sous-requêtes corrélées](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries).

Consultez [EF High performance](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries) pour les approches susceptibles d’améliorer les performances dans les applications à grande échelle :

* [Regroupement DbContext](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)
* [Requêtes compilées explicitement](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)

Nous vous recommandons de mesurer l’impact de l’approche haute performance précédente avant de valider la base de code. La complexité supplémentaire des requêtes compilées peut ne pas justifier l’amélioration des performances.

Les problèmes de requête peuvent être détectés en examinant le temps passé à accéder aux données avec [application Insights](/azure/application-insights/app-insights-overview) ou avec les outils de profilage. La plupart des bases de données rendent également les statistiques disponibles concernant les requêtes fréquemment exécutées.

## <a name="pool-http-connections-with-httpclientfactory"></a>Connexions HTTP de pool avec HttpClientFactory

Bien que [httpclient](/dotnet/api/system.net.http.httpclient) implémente l' `IDisposable` interface, il est conçu pour être réutilisé. `HttpClient`Les instances fermées laissent les sockets ouverts dans l' `TIME_WAIT` État pendant une brève période de temps. Si un chemin de code qui crée et supprime des `HttpClient` objets est fréquemment utilisé, l’application peut épuiser les sockets disponibles. [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) a été introduite dans ASP.net Core 2,1 en tant que solution à ce problème. Il gère le regroupement des connexions HTTP pour optimiser les performances et la fiabilité.

Recommandations :

* **Ne** créez et ne supprimez pas `HttpClient` directement des instances.
* **Utilisez** [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) pour récupérer des `HttpClient` instances. Pour plus d’informations, consultez [Utiliser HttpClientFactory pour implémenter des requêtes HTTP résilientes](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests).

## <a name="keep-common-code-paths-fast"></a>Conserver les chemins de code communs rapidement

Vous souhaitez que tout votre code soit rapide. Les chemins de code fréquemment appelés sont les plus importants à optimiser. Elles incluent notamment :

* Composants de l’intergiciel (middleware) dans le pipeline de traitement des demandes de l’application, en particulier les intergiciels (middleware) exécutés au début du pipeline. Ces composants ont un impact important sur les performances.
* Code qui est exécuté pour chaque demande ou plusieurs fois par demande. Par exemple, la journalisation personnalisée, les gestionnaires d’autorisation ou l’initialisation de services temporaires.

Recommandations :

* **N’utilisez pas** de composants d’intergiciel (middleware) personnalisés avec des tâches de longue durée.
* **Utilisez les** outils de profilage des performances, tels que [Visual Studio outils de diagnostic](/visualstudio/profiling/profiling-feature-tour) ou [PerfView](https://github.com/Microsoft/perfview)), pour identifier les [chemins de code à chaud](#understand-hot-code-paths).

## <a name="complete-long-running-tasks-outside-of-http-requests"></a>Exécuter des tâches de longue durée en dehors des requêtes HTTP

La plupart des demandes adressées à une application ASP.NET Core peuvent être gérées par un modèle de contrôleur ou de page appelant les services nécessaires et retournant une réponse HTTP. Pour certaines demandes qui impliquent des tâches de longue durée, il est préférable de faire en sorte que l’ensemble du processus de requête-réponse soit asynchrone.

Recommandations :

* **N’attendez pas** la fin des tâches de longue durée dans le cadre du traitement de requête http ordinaire.
* **Envisagez** de gérer les demandes de longue durée avec des [services en arrière-plan](xref:fundamentals/host/hosted-services) ou hors processus avec une [fonction Azure](/azure/azure-functions/). L’exécution d’un travail hors processus est particulièrement avantageuse pour les tâches nécessitant beaucoup de ressources processeur.
* **Utilisez des** options de communication en temps réel, telles que [SignalR](xref:signalr/introduction) , pour communiquer de façon asynchrone avec les clients.

## <a name="minify-client-assets"></a>Ressources du client réduire

ASP.NET Core applications avec des serveurs frontaux complexes servent souvent de nombreux fichiers JavaScript, CSS ou image. Les performances des demandes de chargement initiales peuvent être améliorées par :

* Regroupement, qui combine plusieurs fichiers en un seul.
* Minimisation, qui réduit la taille des fichiers en supprimant les espaces blancs et les commentaires.

Recommandations :

* **Utilisez la** [prise en charge intégrée](xref:client-side/bundling-and-minification) de ASP.net Core pour le regroupement et les ressources client minimisation.
* **Envisagez** d’autres outils tiers, tels que [WebPack](https://webpack.js.org/), pour la gestion des ressources client complexes.

## <a name="compress-responses"></a>Compresser les réponses

 La réduction de la taille de la réponse augmente généralement la réactivité d’une application, souvent considérablement. Une façon de réduire les tailles de charge utile consiste à compresser les réponses d’une application. Pour plus d’informations, consultez [compression des réponses](xref:performance/response-compression).

## <a name="use-the-latest-aspnet-core-release"></a>Utiliser la dernière version de ASP.NET Core

Chaque nouvelle version de ASP.NET Core comprend des améliorations des performances. Les optimisations dans .NET Core et ASP.NET Core signifient que les versions plus récentes sont généralement plus performantes que les anciennes. Par exemple, .NET Core 2,1 a ajouté la prise en charge des expressions régulières compilées et bénéficié à partir de [span \<T> ](/archive/msdn-magazine/2018/january/csharp-all-about-span-exploring-a-new-net-mainstay). ASP.NET Core 2,2 a ajouté la prise en charge de HTTP/2. [ASP.NET Core 3,0 ajoute de nombreuses améliorations](xref:aspnetcore-3.0) qui réduisent l’utilisation de la mémoire et améliorent le débit. Si les performances sont prioritaires, envisagez une mise à niveau vers la version actuelle de ASP.NET Core.

## <a name="minimize-exceptions"></a>Réduire les exceptions

Les exceptions doivent être rares. La levée et l’interception des exceptions sont lentes par rapport aux autres modèles de Flow. Pour cette raison, les exceptions ne doivent pas être utilisées pour contrôler le déroulement normal du programme.

Recommandations :

* **N’utilisez pas** la levée ou l’interception d’exceptions comme un moyen de workflow de programme normal, en particulier dans les [chemins de code à chaud](#understand-hot-code-paths).
* **Incluez** la logique dans l’application pour détecter et gérer les conditions qui provoqueraient une exception.
* **Levez ou** interceptez des exceptions pour des conditions inhabituelles ou inattendues.

Les outils de diagnostic d’application, tels que les Application Insights, peuvent aider à identifier les exceptions courantes dans une application qui peuvent affecter les performances.

## <a name="performance-and-reliability"></a>Performances et fiabilité

Les sections suivantes fournissent des conseils en matière de performances et des solutions et des problèmes de fiabilité connus.

## <a name="avoid-synchronous-read-or-write-on-httprequesthttpresponse-body"></a>Éviter la lecture ou l’écriture synchrone sur le corps HttpRequest/HttpResponse

Toutes les e/s dans ASP.NET Core sont asynchrones. Les serveurs implémentent l' `Stream` interface, qui a à la fois des surcharges synchrones et asynchrones. Les plus asynchrones doivent être préférables pour éviter le blocage des threads de pool de threads. Les threads de blocage peuvent entraîner une insuffisance du pool de threads.

N' **effectuez pas cette opération :** L’exemple suivant utilise le <xref:System.IO.StreamReader.ReadToEnd*> . Il bloque le thread actuel pour attendre le résultat. Il s’agit d’un exemple de [synchronisation sur Async](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
).

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet1)]

Dans le code précédent, `Get` lit de manière synchrone l’intégralité du corps de la requête HTTP dans la mémoire. Si le chargement du client est lent, l’application synchronise sur Async. L’application effectue une synchronisation via Async, car Kestrel ne prend **pas** en charge les lectures synchrones.

**Procédez comme suit :** L’exemple suivant utilise <xref:System.IO.StreamReader.ReadToEndAsync*> et ne bloque pas le thread pendant la lecture.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet2)]

Le code précédent lit de façon asynchrone l’intégralité du corps de la requête HTTP dans la mémoire.

> [!WARNING]
> Si la demande est volumineuse, la lecture de l’intégralité du corps de la requête HTTP dans la mémoire peut entraîner une condition de mémoire insuffisante (insuffisance). INSUFFISANCE peut entraîner un déni de service.  Pour plus d’informations, consultez [éviter de lire des corps de requête ou des corps de réponse volumineux](#arlb) dans la mémoire de ce document.

**Procédez comme suit :** L’exemple suivant est entièrement asynchrone à l’aide d’un corps de requête non mis en mémoire tampon :

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet3)]

Le code précédent désérialise de manière asynchrone le corps de la requête dans un objet C#.

## <a name="prefer-readformasync-over-requestform"></a>Préférer ReadFormAsync sur Request. Form

Utilisez `HttpContext.Request.ReadFormAsync` au lieu de `HttpContext.Request.Form`.
`HttpContext.Request.Form` peut être lu en toute sécurité uniquement avec les conditions suivantes :

* Le formulaire a été lu par un appel à `ReadFormAsync` , et
* La valeur du formulaire mis en cache est lue à l’aide de `HttpContext.Request.Form`

N' **effectuez pas cette opération :** L’exemple suivant utilise `HttpContext.Request.Form` .  `HttpContext.Request.Form` utilise la [synchronisation sur Async](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
) et peut entraîner une insuffisance du pool de threads.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet1)]

**Procédez comme suit :** L’exemple suivant utilise `HttpContext.Request.ReadFormAsync` pour lire le corps du formulaire de manière asynchrone.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet2)]

<a name="arlb"></a>

## <a name="avoid-reading-large-request-bodies-or-response-bodies-into-memory"></a>Éviter de lire des corps de requête ou des corps de réponse volumineux en mémoire

Dans .NET, chaque allocation d’objet supérieure à 85 Ko se termine dans le tas d’objets volumineux ([Loh](https://blogs.msdn.microsoft.com/maoni/2006/04/19/large-object-heap/)). Les objets volumineux sont coûteux de deux manières :

* Le coût d’allocation est élevé, car la mémoire pour un objet volumineux qui vient d’être alloué doit être effacée. Le CLR garantit que la mémoire de tous les objets alloués récemment est effacée.
* LOH est collecté avec le reste du tas. LOH requiert une collection complète de [garbage collection](/dotnet/standard/garbage-collection/fundamentals) ou [Gen2](/dotnet/standard/garbage-collection/fundamentals#generations).

Ce billet de [blog](https://adamsitnik.com/Array-Pool/#the-problem) décrit succinctement le problème :

> Lorsqu’un objet volumineux est alloué, il est marqué comme objet Gen 2. Non Gen 0 comme pour les petits objets. Les conséquences sont que si vous ne disposez pas de suffisamment de mémoire dans LOH, le garbage collector nettoie l’intégralité du tas managé, pas seulement LOH. Il nettoie donc la génération 0, la génération 1 et la génération 2, y compris LOH. C’est ce que l’on appelle la garbage collection complète et est le garbage collection le plus long. Pour de nombreuses applications, cela peut être acceptable. Mais ce n’est pas vraiment le cas pour les serveurs Web à hautes performances, où peu de mémoires tampons volumineuses sont nécessaires pour gérer une requête Web moyenne (lecture à partir d’un socket, décompression, décodage JSON & plus).

Naïvement stockant un grand corps de requête ou de réponse dans un seul `byte[]` ou `string` :

* Peut entraîner une insuffisance de l’espace dans le LOH.
* Peut entraîner des problèmes de performances pour l’application en raison de l’exécution complète des catalogues globaux.

## <a name="working-with-a-synchronous-data-processing-api"></a>Utilisation d’une API de traitement de données synchrone

Lors de l’utilisation d’un sérialiseur/désérialiseur qui prend uniquement en charge les lectures et écritures synchrones (par exemple,  [JSON.net](https://www.newtonsoft.com/json/help/html/Introduction.htm)) :

* Mettez les données en mémoire tampon de manière asynchrone avant de les transmettre au sérialiseur/désérialiseur.

> [!WARNING]
> Si la demande est volumineuse, cela peut entraîner une condition de mémoire insuffisante (insuffisance). INSUFFISANCE peut entraîner un déni de service.  Pour plus d’informations, consultez [éviter de lire des corps de requête ou des corps de réponse volumineux](#arlb) dans la mémoire de ce document.

ASP.NET Core 3,0 utilise par <xref:System.Text.Json> défaut pour la SÉRIALISATION JSON. <xref:System.Text.Json>:

* Lit et écrit JSON de manière asynchrone.
* Est optimisé pour le texte UTF-8.
* En général, performances supérieures à `Newtonsoft.Json` .

## <a name="do-not-store-ihttpcontextaccessorhttpcontext-in-a-field"></a>Ne pas stocker IHttpContextAccessor. HttpContext dans un champ

[IHttpContextAccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext) retourne le `HttpContext` de la requête active en cas d’accès à partir du thread de demande. Le ne `IHttpContextAccessor.HttpContext` doit **pas** être stocké dans un champ ou une variable.

N' **effectuez pas cette opération :** L’exemple suivant stocke `HttpContext` dans un champ, puis essaie de l’utiliser ultérieurement.

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet1)]

Le code précédent capture souvent une valeur null ou incorrecte `HttpContext` dans le constructeur.

**Procédez comme suit :** L’exemple suivant :

* Stocke <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> dans un champ.
* Utilise le `HttpContext` champ à l’heure correcte et recherche `null` .

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet2)]

## <a name="do-not-access-httpcontext-from-multiple-threads"></a>N’accédez pas à HttpContext à partir de plusieurs threads

`HttpContext` n’est *pas* thread-safe. L’accès `HttpContext` à partir de plusieurs threads en parallèle peut entraîner un comportement indéfini, tel que les blocages, les blocages et les données endommagées.

N' **effectuez pas cette opération :** L’exemple suivant effectue trois demandes parallèles et journalise le chemin d’accès de la requête entrante avant et après la requête HTTP sortante. Le chemin d’accès de la requête est accessible à partir de plusieurs threads, éventuellement en parallèle.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet1&highlight=25,28)]

**Procédez comme suit :** L’exemple suivant copie toutes les données de la demande entrante avant d’effectuer les trois requêtes parallèles.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet2&highlight=6,8,22,28)]

## <a name="do-not-use-the-httpcontext-after-the-request-is-complete"></a>N’utilisez pas HttpContext une fois la demande terminée

`HttpContext` n’est valide qu’à condition qu’il y ait une requête HTTP active dans le pipeline ASP.NET Core. L’ensemble du pipeline ASP.NET Core est une chaîne asynchrone de délégués qui exécute chaque requête. Lorsque le `Task` retourné à partir de cette chaîne se termine, le `HttpContext` est recyclé.

N' **effectuez pas cette opération :** L’exemple suivant utilise `async void` qui rend la requête http terminée lorsque le premier `await` est atteint :

* C’est **toujours** une mauvaise pratique dans les applications ASP.net core.
* Accède à `HttpResponse` après la fin de la requête http.
* Arrête le processus.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncBadVoidController.cs?name=snippet1)]

**Procédez comme suit :** L’exemple suivant retourne un `Task` à l’infrastructure, de sorte que la requête http ne se termine pas tant que l’action n’est pas terminée.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncSecondController.cs?name=snippet1)]

## <a name="do-not-capture-the-httpcontext-in-background-threads"></a>Ne pas capturer le HttpContext dans les threads d’arrière-plan

N' **effectuez pas cette opération :** L’exemple suivant montre qu’une fermeture capture le `HttpContext` à partir de la `Controller` propriété. Il s’agit d’une mauvaise pratique, car l’élément de travail peut :

* Exécution en dehors de l’étendue de la requête.
* Essayez de lire le problème `HttpContext` .

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet1)]

**Procédez comme suit :** L’exemple suivant :

* Copie les données requises dans la tâche en arrière-plan pendant la requête.
* Ne fait référence à rien du contrôleur.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet2)]

Les tâches en arrière-plan doivent être implémentées en tant que services hébergés. Pour plus d’informations, consultez [Tâches en arrière-plan avec services hébergés](xref:fundamentals/host/hosted-services).

## <a name="do-not-capture-services-injected-into-the-controllers-on-background-threads"></a>Ne capturez pas les services injectés dans les contrôleurs sur les threads d’arrière-plan

N' **effectuez pas cette opération :** L’exemple suivant montre qu’une fermeture capture le `DbContext` à partir du `Controller` paramètre d’action. Il s’agit d’une mauvaise pratique.  L’élément de travail peut s’exécuter en dehors de l’étendue de la requête. La `ContosoDbContext` portée est limitée à la requête, ce qui génère un `ObjectDisposedException` .

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet1)]

**Procédez comme suit :** L’exemple suivant :

* Injecte un <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> afin de créer une portée dans l’élément de travail d’arrière-plan. `IServiceScopeFactory` est un singleton.
* Crée une nouvelle étendue d’injection de dépendance dans le thread d’arrière-plan.
* Ne fait référence à rien du contrôleur.
* Ne capture pas le `ContosoDbContext` de la requête entrante.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2)]

Le code en surbrillance suivant :

* Crée une portée pour la durée de vie de l’opération d’arrière-plan et résout des services à partir de celle-ci.
* Utilise `ContosoDbContext` à partir de l’étendue correcte.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2&highlight=9-16)]

## <a name="do-not-modify-the-status-code-or-headers-after-the-response-body-has-started"></a>Ne modifiez pas le code d’État ou les en-têtes une fois que le corps de la réponse a démarré

ASP.NET Core ne met pas en mémoire tampon le corps de la réponse HTTP. La première fois que la réponse est écrite :

* Les en-têtes sont envoyés avec ce segment du corps au client.
* Il n’est plus possible de modifier les en-têtes de réponse.

N' **effectuez pas cette opération :** Le code suivant tente d’ajouter des en-têtes de réponse une fois que la réponse a déjà démarré :

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet1)]

Dans le code précédent, `context.Response.Headers["test"] = "test value";` lèvera une exception si `next()` a écrit dans la réponse.

**Procédez comme suit :** L’exemple suivant vérifie si la réponse HTTP a démarré avant de modifier les en-têtes.

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet2)]

**Procédez comme suit :** L’exemple suivant utilise `HttpResponse.OnStarting` pour définir les en-têtes avant que les en-têtes de réponse soient vidés sur le client.

Le fait de vérifier si la réponse n’a pas démarré permet d’inscrire un rappel qui sera appelé juste avant l’écriture des en-têtes de réponse. Vérification de l’absence de démarrage de la réponse :

* Offre la possibilité d’ajouter ou de remplacer des en-têtes juste-à-temps.
* N’a pas besoin de connaître l’intergiciel suivant dans le pipeline.

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet3)]

## <a name="do-not-call-next-if-you-have-already-started-writing-to-the-response-body"></a>N’appelez pas Next () si vous avez déjà commencé à écrire dans le corps de la réponse

Les composants s’attendent à être appelés uniquement s’ils peuvent gérer et manipuler la réponse.

## <a name="use-in-process-hosting-with-iis"></a>Utiliser l’hébergement in-process avec IIS

En utilisant l’hébergement in-process, une application ASP.NET Core s’exécute dans le même processus que son processus de travail IIS. L’hébergement en cours offre des performances améliorées par rapport à l’hébergement hors processus, car les demandes ne sont pas transmises par proxy sur la carte de bouclage. La carte de bouclage est une interface réseau qui renvoie le trafic réseau sortant vers le même ordinateur. IIS s’occupe de la gestion des processus par l’intermédiaire du [service d’activation des processus Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Les projets sont par défaut du modèle d’hébergement in-process dans ASP.NET Core 3,0 et versions ultérieures.

Pour plus d’informations, consultez [ASP.net core d’hôte sur Windows avec IIS](xref:host-and-deploy/iis/index) .