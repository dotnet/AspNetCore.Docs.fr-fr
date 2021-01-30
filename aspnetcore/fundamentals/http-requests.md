---
title: Effectuer des requêtes HTTP en utilisant IHttpClientFactory dans ASP.NET Core
author: stevejgordon
description: Découvrez plus d’informations sur l’utilisation de l’interface IHttpClientFactory pour gérer des instances HttpClient logiques dans ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 1/21/2021
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
uid: fundamentals/http-requests
ms.openlocfilehash: 1cf3029452f87a396847f969f0f3136a75874752
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057328"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="c61e8-103">Effectuer des requêtes HTTP en utilisant IHttpClientFactory dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c61e8-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c61e8-104">Par [Kirk Larkin](https://github.com/serpent5), [Steve Gordon](https://github.com/stevejgordon), [Glenn Condron](https://github.com/glennc)et [Ryan Nowak](https://github.com/rynowak).</span><span class="sxs-lookup"><span data-stu-id="c61e8-104">By [Kirk Larkin](https://github.com/serpent5), [Steve Gordon](https://github.com/stevejgordon), [Glenn Condron](https://github.com/glennc), and [Ryan Nowak](https://github.com/rynowak).</span></span>

<span data-ttu-id="c61e8-105">Une <xref:System.Net.Http.IHttpClientFactory> peut être inscrite et utilisée pour configurer et créer des instances de <xref:System.Net.Http.HttpClient> dans une application.</span><span class="sxs-lookup"><span data-stu-id="c61e8-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="c61e8-106">`IHttpClientFactory` offre les avantages suivants :</span><span class="sxs-lookup"><span data-stu-id="c61e8-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="c61e8-107">Fournit un emplacement central pour le nommage et la configuration d’instance de `HttpClient` logiques.</span><span class="sxs-lookup"><span data-stu-id="c61e8-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="c61e8-108">Par exemple, un client nommé  *GitHub* peut être inscrit et configuré pour accéder à [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="c61e8-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="c61e8-109">Un client par défaut peut être inscrit pour un accès général.</span><span class="sxs-lookup"><span data-stu-id="c61e8-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="c61e8-110">Codifie le concept d’intergiciel (middleware) sortant via la délégation de gestionnaires dans `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="c61e8-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="c61e8-111">Fournit des extensions pour l’intergiciel (middleware) basé sur Polly pour tirer parti des gestionnaires de délégation dans `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="c61e8-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="c61e8-112">Gère le regroupement et la durée de vie des instances sous-jacentes `HttpClientMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="c61e8-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="c61e8-113">La gestion automatique évite les problèmes courants liés au DNS (Domain Name System) qui se produisent lors de la gestion manuelle des `HttpClient` durées de vie.</span><span class="sxs-lookup"><span data-stu-id="c61e8-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="c61e8-114">Ajoute une expérience de journalisation configurable (via `ILogger`) pour toutes les requêtes envoyées via des clients créés par la fabrique.</span><span class="sxs-lookup"><span data-stu-id="c61e8-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="c61e8-115">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="c61e8-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="c61e8-116">L’exemple de code de cette rubrique utilise <xref:System.Text.Json> pour désérialiser le contenu JSON renvoyé dans les réponses http.</span><span class="sxs-lookup"><span data-stu-id="c61e8-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="c61e8-117">Pour obtenir des exemples qui utilisent `Json.NET` et `ReadAsAsync<T>` , utilisez le sélecteur de version pour sélectionner une version 2. x de cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="c61e8-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="c61e8-118">Modèles de consommation</span><span class="sxs-lookup"><span data-stu-id="c61e8-118">Consumption patterns</span></span>

<span data-ttu-id="c61e8-119">Vous pouvez utiliser `IHttpClientFactory` dans une application de plusieurs façons :</span><span class="sxs-lookup"><span data-stu-id="c61e8-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="c61e8-120">Utilisation de base</span><span class="sxs-lookup"><span data-stu-id="c61e8-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="c61e8-121">Clients nommés</span><span class="sxs-lookup"><span data-stu-id="c61e8-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="c61e8-122">Clients typés</span><span class="sxs-lookup"><span data-stu-id="c61e8-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="c61e8-123">Clients générés</span><span class="sxs-lookup"><span data-stu-id="c61e8-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="c61e8-124">La meilleure approche dépend des exigences de l’application.</span><span class="sxs-lookup"><span data-stu-id="c61e8-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="c61e8-125">Utilisation de base</span><span class="sxs-lookup"><span data-stu-id="c61e8-125">Basic usage</span></span>

<span data-ttu-id="c61e8-126">`IHttpClientFactory` peut être inscrit en appelant `AddHttpClient` :</span><span class="sxs-lookup"><span data-stu-id="c61e8-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1&highlight=13)]

<span data-ttu-id="c61e8-127">Un `IHttpClientFactory` peut être demandé à l’aide [de l’injection de dépendances (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="c61e8-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="c61e8-128">Le code suivant utilise `IHttpClientFactory` pour créer une `HttpClient` instance :</span><span class="sxs-lookup"><span data-stu-id="c61e8-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="c61e8-129">L’utilisation `IHttpClientFactory` de like dans l’exemple précédent est un bon moyen de refactoriser une application existante.</span><span class="sxs-lookup"><span data-stu-id="c61e8-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="c61e8-130">Elle n’a aucun impact sur la façon dont `HttpClient` est utilisé.</span><span class="sxs-lookup"><span data-stu-id="c61e8-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="c61e8-131">Dans les emplacements où les `HttpClient` instances sont créées dans une application existante, remplacez-les par des appels à <xref:System.Net.Http.IHttpClientFactory.CreateClient*> .</span><span class="sxs-lookup"><span data-stu-id="c61e8-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="c61e8-132">Clients nommés</span><span class="sxs-lookup"><span data-stu-id="c61e8-132">Named clients</span></span>

<span data-ttu-id="c61e8-133">Les clients nommés sont un bon choix lorsque :</span><span class="sxs-lookup"><span data-stu-id="c61e8-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="c61e8-134">L’application requiert de nombreuses utilisations distinctes de `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="c61e8-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="c61e8-135">De nombreux `HttpClient` s ont une configuration différente.</span><span class="sxs-lookup"><span data-stu-id="c61e8-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="c61e8-136">La configuration d’un nom `HttpClient` peut être spécifiée lors de l’inscription dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c61e8-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="c61e8-137">Dans le code précédent, le client est configuré avec :</span><span class="sxs-lookup"><span data-stu-id="c61e8-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="c61e8-138">Adresse de base `https://api.github.com/` .</span><span class="sxs-lookup"><span data-stu-id="c61e8-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="c61e8-139">Deux en-têtes nécessaires à l’utilisation de l’API GitHub.</span><span class="sxs-lookup"><span data-stu-id="c61e8-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="c61e8-140">CreateClient</span><span class="sxs-lookup"><span data-stu-id="c61e8-140">CreateClient</span></span>

<span data-ttu-id="c61e8-141">Chaque fois que <xref:System.Net.Http.IHttpClientFactory.CreateClient*> est appelé :</span><span class="sxs-lookup"><span data-stu-id="c61e8-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="c61e8-142">Une nouvelle instance de `HttpClient` est créée.</span><span class="sxs-lookup"><span data-stu-id="c61e8-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="c61e8-143">L’action de configuration est appelée.</span><span class="sxs-lookup"><span data-stu-id="c61e8-143">The configuration action is called.</span></span>

<span data-ttu-id="c61e8-144">Pour créer un client nommé, transmettez son nom à `CreateClient` :</span><span class="sxs-lookup"><span data-stu-id="c61e8-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="c61e8-145">Dans le code précédent, la requête n’a pas besoin de spécifier un nom d’hôte.</span><span class="sxs-lookup"><span data-stu-id="c61e8-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="c61e8-146">Le code peut passer uniquement le chemin d’accès, dans la mesure où l’adresse de base configurée pour le client est utilisée.</span><span class="sxs-lookup"><span data-stu-id="c61e8-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="c61e8-147">Clients typés</span><span class="sxs-lookup"><span data-stu-id="c61e8-147">Typed clients</span></span>

<span data-ttu-id="c61e8-148">Clients typés :</span><span class="sxs-lookup"><span data-stu-id="c61e8-148">Typed clients:</span></span>

* <span data-ttu-id="c61e8-149">Fournissent les mêmes fonctionnalités que les clients nommés, sans qu’il soit nécessaire d’utiliser des chaînes comme clés.</span><span class="sxs-lookup"><span data-stu-id="c61e8-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="c61e8-150">Bénéficie de l’aide d’IntelliSense et du compilateur lors de l’utilisation des clients.</span><span class="sxs-lookup"><span data-stu-id="c61e8-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="c61e8-151">Fournissent un emplacement unique pour configurer et interagir avec un `HttpClient` particulier.</span><span class="sxs-lookup"><span data-stu-id="c61e8-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="c61e8-152">Par exemple, un seul client typé peut être utilisé :</span><span class="sxs-lookup"><span data-stu-id="c61e8-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="c61e8-153">Pour un seul point de terminaison backend.</span><span class="sxs-lookup"><span data-stu-id="c61e8-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="c61e8-154">Pour encapsuler toute la logique traitant le point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="c61e8-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="c61e8-155">Utilisez DI et pouvez les injecter lorsque cela est requis dans l’application.</span><span class="sxs-lookup"><span data-stu-id="c61e8-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="c61e8-156">Un client typé accepte un `HttpClient` paramètre dans son constructeur :</span><span class="sxs-lookup"><span data-stu-id="c61e8-156">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="c61e8-157">Dans le code précédent :</span><span class="sxs-lookup"><span data-stu-id="c61e8-157">In the preceding code:</span></span>

* <span data-ttu-id="c61e8-158">La configuration est déplacée vers le client typé.</span><span class="sxs-lookup"><span data-stu-id="c61e8-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="c61e8-159">L’objet `HttpClient` est exposé en tant que propriété publique.</span><span class="sxs-lookup"><span data-stu-id="c61e8-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="c61e8-160">Vous pouvez créer des méthodes spécifiques à l’API qui exposent les `HttpClient` fonctionnalités.</span><span class="sxs-lookup"><span data-stu-id="c61e8-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="c61e8-161">Par exemple, la `GetAspNetDocsIssues` méthode encapsule du code pour récupérer des problèmes ouverts.</span><span class="sxs-lookup"><span data-stu-id="c61e8-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="c61e8-162">Le code suivant appelle <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> dans `Startup.ConfigureServices` pour inscrire une classe de client typée :</span><span class="sxs-lookup"><span data-stu-id="c61e8-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="c61e8-163">Le client typé est inscrit comme étant transitoire avec injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="c61e8-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="c61e8-164">Dans le code précédent, `AddHttpClient` s’inscrit `GitHubService` en tant que service temporaire.</span><span class="sxs-lookup"><span data-stu-id="c61e8-164">In the preceding code, `AddHttpClient` registers `GitHubService` as a transient service.</span></span> <span data-ttu-id="c61e8-165">Cette inscription utilise une méthode de fabrique pour :</span><span class="sxs-lookup"><span data-stu-id="c61e8-165">This registration uses a factory method to:</span></span>

1. <span data-ttu-id="c61e8-166">Créez une instance de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-166">Create an instance of `HttpClient`.</span></span>
1. <span data-ttu-id="c61e8-167">Crée une instance de `GitHubService` , en passant l’instance de `HttpClient` à son constructeur.</span><span class="sxs-lookup"><span data-stu-id="c61e8-167">Create an instance of `GitHubService`, passing in the instance of `HttpClient` to its constructor.</span></span>

<span data-ttu-id="c61e8-168">Le client typé peut être injecté et utilisé directement :</span><span class="sxs-lookup"><span data-stu-id="c61e8-168">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="c61e8-169">La configuration d’un client typé peut être spécifiée lors de l’inscription dans `Startup.ConfigureServices` , plutôt que dans le constructeur du client typé :</span><span class="sxs-lookup"><span data-stu-id="c61e8-169">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="c61e8-170">`HttpClient`Peut être encapsulé dans un client typé.</span><span class="sxs-lookup"><span data-stu-id="c61e8-170">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="c61e8-171">Au lieu de l’exposer en tant que propriété, définissez une méthode qui appelle l' `HttpClient` instance en interne :</span><span class="sxs-lookup"><span data-stu-id="c61e8-171">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="c61e8-172">Dans le code précédent, le `HttpClient` est stocké dans un champ privé.</span><span class="sxs-lookup"><span data-stu-id="c61e8-172">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="c61e8-173">L’accès à `HttpClient` est par la `GetRepos` méthode publique.</span><span class="sxs-lookup"><span data-stu-id="c61e8-173">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="c61e8-174">Clients générés</span><span class="sxs-lookup"><span data-stu-id="c61e8-174">Generated clients</span></span>

<span data-ttu-id="c61e8-175">`IHttpClientFactory` peut être utilisé en association avec des bibliothèques tierces telles que la [réajuster](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="c61e8-175">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="c61e8-176">Refit est une bibliothèque REST pour .NET.</span><span class="sxs-lookup"><span data-stu-id="c61e8-176">Refit is a REST library for .NET.</span></span> <span data-ttu-id="c61e8-177">Il convertit les API REST en interfaces dynamiques.</span><span class="sxs-lookup"><span data-stu-id="c61e8-177">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="c61e8-178">Une implémentation de l’interface est générée dynamiquement par le `RestService`, avec `HttpClient` pour faire les appels HTTP externes.</span><span class="sxs-lookup"><span data-stu-id="c61e8-178">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="c61e8-179">Une interface et une réponse sont définies pour représenter l’API externe et sa réponse :</span><span class="sxs-lookup"><span data-stu-id="c61e8-179">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="c61e8-180">Vous pouvez ajouter un client typé en utilisant Refit pour générer l’implémentation :</span><span class="sxs-lookup"><span data-stu-id="c61e8-180">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddControllers();
}
```

<span data-ttu-id="c61e8-181">L’interface définie peut être utilisée quand c’est nécessaire, avec l’implémentation fournie par l’injection de dépendances et Refit :</span><span class="sxs-lookup"><span data-stu-id="c61e8-181">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="make-post-put-and-delete-requests"></a><span data-ttu-id="c61e8-182">Créer des demandes de publication, de placement et de suppression</span><span class="sxs-lookup"><span data-stu-id="c61e8-182">Make POST, PUT, and DELETE requests</span></span>

<span data-ttu-id="c61e8-183">Dans les exemples précédents, toutes les requêtes HTTP utilisent le verbe HTTP.</span><span class="sxs-lookup"><span data-stu-id="c61e8-183">In the preceding examples, all HTTP requests use the GET HTTP verb.</span></span> <span data-ttu-id="c61e8-184">`HttpClient` prend également en charge d’autres verbes HTTP, notamment :</span><span class="sxs-lookup"><span data-stu-id="c61e8-184">`HttpClient` also supports other HTTP verbs, including:</span></span>

* <span data-ttu-id="c61e8-185">POST</span><span class="sxs-lookup"><span data-stu-id="c61e8-185">POST</span></span>
* <span data-ttu-id="c61e8-186">PUT</span><span class="sxs-lookup"><span data-stu-id="c61e8-186">PUT</span></span>
* <span data-ttu-id="c61e8-187">DELETE</span><span class="sxs-lookup"><span data-stu-id="c61e8-187">DELETE</span></span>
* <span data-ttu-id="c61e8-188">PATCH</span><span class="sxs-lookup"><span data-stu-id="c61e8-188">PATCH</span></span>

<span data-ttu-id="c61e8-189">Pour obtenir la liste complète des verbes HTTP pris en charge, consultez <xref:System.Net.Http.HttpMethod> .</span><span class="sxs-lookup"><span data-stu-id="c61e8-189">For a complete list of supported HTTP verbs, see <xref:System.Net.Http.HttpMethod>.</span></span>

<span data-ttu-id="c61e8-190">L’exemple suivant montre comment effectuer une requête HTTP après :</span><span class="sxs-lookup"><span data-stu-id="c61e8-190">The following example shows how to make an HTTP POST request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_POST)]

<span data-ttu-id="c61e8-191">Dans le code précédent, la `CreateItemAsync` méthode :</span><span class="sxs-lookup"><span data-stu-id="c61e8-191">In the preceding code, the `CreateItemAsync` method:</span></span>

* <span data-ttu-id="c61e8-192">Sérialise le `TodoItem` paramètre au format JSON à l’aide de `System.Text.Json` .</span><span class="sxs-lookup"><span data-stu-id="c61e8-192">Serializes the `TodoItem` parameter to JSON using `System.Text.Json`.</span></span> <span data-ttu-id="c61e8-193">Utilise une instance de <xref:System.Text.Json.JsonSerializerOptions> pour configurer le processus de sérialisation.</span><span class="sxs-lookup"><span data-stu-id="c61e8-193">This uses an instance of <xref:System.Text.Json.JsonSerializerOptions> to configure the serialization process.</span></span>
* <span data-ttu-id="c61e8-194">Crée une instance de <xref:System.Net.Http.StringContent> pour empaqueter le JSON sérialisé à envoyer dans le corps de la requête http.</span><span class="sxs-lookup"><span data-stu-id="c61e8-194">Creates an instance of <xref:System.Net.Http.StringContent> to package the serialized JSON for sending in the HTTP request's body.</span></span>
* <span data-ttu-id="c61e8-195">Appelle <xref:System.Net.Http.HttpClient.PostAsync%2A> pour envoyer le contenu JSON à l’URL spécifiée.</span><span class="sxs-lookup"><span data-stu-id="c61e8-195">Calls <xref:System.Net.Http.HttpClient.PostAsync%2A> to send the JSON content to the specified URL.</span></span> <span data-ttu-id="c61e8-196">Il s’agit d’une URL relative qui est ajoutée à [httpclient. BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress).</span><span class="sxs-lookup"><span data-stu-id="c61e8-196">This is a relative URL that gets added to the [HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress).</span></span>
* <span data-ttu-id="c61e8-197">Appelle <xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A> pour lever une exception si le code d’état de réponse n’indique pas la réussite.</span><span class="sxs-lookup"><span data-stu-id="c61e8-197">Calls <xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A> to throw an exception if the response status code does not indicate success.</span></span>

<span data-ttu-id="c61e8-198">`HttpClient` prend également en charge d’autres types de contenu.</span><span class="sxs-lookup"><span data-stu-id="c61e8-198">`HttpClient` also supports other types of content.</span></span> <span data-ttu-id="c61e8-199">Par exemple, <xref:System.Net.Http.MultipartContent> et <xref:System.Net.Http.StreamContent>.</span><span class="sxs-lookup"><span data-stu-id="c61e8-199">For example, <xref:System.Net.Http.MultipartContent> and <xref:System.Net.Http.StreamContent>.</span></span> <span data-ttu-id="c61e8-200">Pour obtenir la liste complète du contenu pris en charge, consultez <xref:System.Net.Http.HttpContent> .</span><span class="sxs-lookup"><span data-stu-id="c61e8-200">For a complete list of supported content, see <xref:System.Net.Http.HttpContent>.</span></span>

<span data-ttu-id="c61e8-201">L’exemple suivant illustre une requête HTTP PUT :</span><span class="sxs-lookup"><span data-stu-id="c61e8-201">The following example shows an HTTP PUT request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_PUT)]

<span data-ttu-id="c61e8-202">Le code précédent est très similaire à l’exemple de billet.</span><span class="sxs-lookup"><span data-stu-id="c61e8-202">The preceding code is very similar to the POST example.</span></span> <span data-ttu-id="c61e8-203">La `SaveItemAsync` méthode appelle à la <xref:System.Net.Http.HttpClient.PutAsync%2A> place de `PostAsync` .</span><span class="sxs-lookup"><span data-stu-id="c61e8-203">The `SaveItemAsync` method calls <xref:System.Net.Http.HttpClient.PutAsync%2A> instead of `PostAsync`.</span></span>

<span data-ttu-id="c61e8-204">L’exemple suivant illustre une requête HTTP DELETE :</span><span class="sxs-lookup"><span data-stu-id="c61e8-204">The following example shows an HTTP DELETE request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_DELETE)]

<span data-ttu-id="c61e8-205">Dans le code précédent, la `DeleteItemAsync` méthode appelle <xref:System.Net.Http.HttpClient.DeleteAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="c61e8-205">In the preceding code, the `DeleteItemAsync` method calls <xref:System.Net.Http.HttpClient.DeleteAsync%2A>.</span></span> <span data-ttu-id="c61e8-206">Étant donné que les requêtes HTTP DELETE ne contiennent généralement pas de corps, la `DeleteAsync` méthode ne fournit pas de surcharge qui accepte une instance de `HttpContent` .</span><span class="sxs-lookup"><span data-stu-id="c61e8-206">Because HTTP DELETE requests typically contain no body, the `DeleteAsync` method doesn't provide an overload that accepts an instance of `HttpContent`.</span></span>

<span data-ttu-id="c61e8-207">Pour en savoir plus sur l’utilisation de différents verbes HTTP avec `HttpClient` , consultez <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="c61e8-207">To learn more about using different HTTP verbs with `HttpClient`, see <xref:System.Net.Http.HttpClient>.</span></span>

## <a name="outgoing-request-middleware"></a><span data-ttu-id="c61e8-208">Middleware pour les requêtes sortantes</span><span class="sxs-lookup"><span data-stu-id="c61e8-208">Outgoing request middleware</span></span>

<span data-ttu-id="c61e8-209">`HttpClient` présente le concept de délégation de gestionnaires qui peuvent être liés entre eux pour les requêtes HTTP sortantes.</span><span class="sxs-lookup"><span data-stu-id="c61e8-209">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="c61e8-210">`IHttpClientFactory`:</span><span class="sxs-lookup"><span data-stu-id="c61e8-210">`IHttpClientFactory`:</span></span>

  * <span data-ttu-id="c61e8-211">Simplifie la définition des gestionnaires à appliquer pour chaque client nommé.</span><span class="sxs-lookup"><span data-stu-id="c61e8-211">Simplifies defining the handlers to apply for each named client.</span></span>
  * <span data-ttu-id="c61e8-212">Prend en charge l’inscription et le chaînage de plusieurs gestionnaires pour générer un pipeline d’intergiciel (middleware) de demande sortante.</span><span class="sxs-lookup"><span data-stu-id="c61e8-212">Supports registration and chaining of multiple handlers to build an outgoing request   middleware pipeline.</span></span> <span data-ttu-id="c61e8-213">Chacun de ces gestionnaires est en mesure d’effectuer un travail avant et après la demande sortante.</span><span class="sxs-lookup"><span data-stu-id="c61e8-213">Each of these handlers is able to perform work before and after the   outgoing request.</span></span> <span data-ttu-id="c61e8-214">Ce modèle :</span><span class="sxs-lookup"><span data-stu-id="c61e8-214">This pattern:</span></span>
  
    * <span data-ttu-id="c61e8-215">Est semblable au pipeline d’intergiciel (middleware) entrant dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c61e8-215">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
    * <span data-ttu-id="c61e8-216">Fournit un mécanisme permettant de gérer les problèmes transversaux autour des requêtes HTTP, par exemple :</span><span class="sxs-lookup"><span data-stu-id="c61e8-216">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>
      * <span data-ttu-id="c61e8-217">caching</span><span class="sxs-lookup"><span data-stu-id="c61e8-217">caching</span></span>
      * <span data-ttu-id="c61e8-218">gestion des erreurs</span><span class="sxs-lookup"><span data-stu-id="c61e8-218">error handling</span></span>
      * <span data-ttu-id="c61e8-219">sérialisation</span><span class="sxs-lookup"><span data-stu-id="c61e8-219">serialization</span></span>
      * <span data-ttu-id="c61e8-220">journalisation</span><span class="sxs-lookup"><span data-stu-id="c61e8-220">logging</span></span>

<span data-ttu-id="c61e8-221">Pour créer un gestionnaire de délégation :</span><span class="sxs-lookup"><span data-stu-id="c61e8-221">To create a delegating handler:</span></span>

* <span data-ttu-id="c61e8-222">Dériver de <xref:System.Net.Http.DelegatingHandler> .</span><span class="sxs-lookup"><span data-stu-id="c61e8-222">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="c61e8-223">Remplacez <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span><span class="sxs-lookup"><span data-stu-id="c61e8-223">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="c61e8-224">Exécutez le code avant de passer la requête au gestionnaire suivant dans le pipeline :</span><span class="sxs-lookup"><span data-stu-id="c61e8-224">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="c61e8-225">Le code précédent vérifie si l' `X-API-KEY` en-tête est dans la demande.</span><span class="sxs-lookup"><span data-stu-id="c61e8-225">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="c61e8-226">Si `X-API-KEY` est manquant, <xref:System.Net.HttpStatusCode.BadRequest> est retourné.</span><span class="sxs-lookup"><span data-stu-id="c61e8-226">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="c61e8-227">Plusieurs gestionnaires peuvent être ajoutés à la configuration d’un `HttpClient` avec <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName> :</span><span class="sxs-lookup"><span data-stu-id="c61e8-227">More than one handler can be added to the configuration for an `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="c61e8-228">Dans le code précédent, le `ValidateHeaderHandler` est inscrit avec une injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="c61e8-228">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="c61e8-229">Une fois inscrit, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> peut être appelé en passant en entrée le type pour le gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="c61e8-229">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="c61e8-230">Vous pouvez inscrire plusieurs gestionnaires dans l’ordre où ils doivent être exécutés.</span><span class="sxs-lookup"><span data-stu-id="c61e8-230">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="c61e8-231">Chaque gestionnaire wrappe le gestionnaire suivant jusqu’à ce que le dernier `HttpClientHandler` exécute la requête :</span><span class="sxs-lookup"><span data-stu-id="c61e8-231">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

### <a name="use-di-in-outgoing-request-middleware"></a><span data-ttu-id="c61e8-232">Utiliser l’injection de trafic dans l’intergiciel (middleware) des demandes sortantes</span><span class="sxs-lookup"><span data-stu-id="c61e8-232">Use DI in outgoing request middleware</span></span>

<span data-ttu-id="c61e8-233">Lorsque `IHttpClientFactory` crée un gestionnaire de délégation, il utilise di pour accomplir les paramètres du constructeur du gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="c61e8-233">When `IHttpClientFactory` creates a new delegating handler, it uses DI to fulfill the handler's constructor parameters.</span></span> <span data-ttu-id="c61e8-234">`IHttpClientFactory` crée une étendue di **distincte** pour chaque gestionnaire, ce qui peut entraîner un comportement étonnant lorsqu’un gestionnaire consomme un service *étendu* .</span><span class="sxs-lookup"><span data-stu-id="c61e8-234">`IHttpClientFactory` creates a **separate** DI scope for each handler, which can lead to surprising behavior when a handler consumes a *scoped* service.</span></span>

<span data-ttu-id="c61e8-235">Par exemple, considérez l’interface suivante et son implémentation, qui représente une tâche comme une opération avec un identificateur, `OperationId` :</span><span class="sxs-lookup"><span data-stu-id="c61e8-235">For example, consider the following interface and its implementation, which represents a task as an operation with an identifier, `OperationId`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/OperationScoped.cs?name=snippet_Types)]

<span data-ttu-id="c61e8-236">Comme son nom l' `IOperationScoped` indique, est inscrit avec di à l’aide d’une durée de vie *limitée* :</span><span class="sxs-lookup"><span data-stu-id="c61e8-236">As its name suggests, `IOperationScoped` is registered with DI using a *scoped* lifetime:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Startup.cs?name=snippet_IOperationScoped&highlight=18,26)]

<span data-ttu-id="c61e8-237">Le gestionnaire de délégation suivant consomme et utilise `IOperationScoped` pour définir l' `X-OPERATION-ID` en-tête de la demande sortante :</span><span class="sxs-lookup"><span data-stu-id="c61e8-237">The following delegating handler consumes and uses `IOperationScoped` to set the `X-OPERATION-ID` header for the outgoing request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Handlers/OperationHandler.cs?name=snippet_Class&highlight=13)]

<span data-ttu-id="c61e8-238">Dans le [ `HttpRequestsSample` Téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples/3.x/HttpRequestsSample)], accédez à `/Operation` la page et actualisez-la.</span><span class="sxs-lookup"><span data-stu-id="c61e8-238">In the [`HttpRequestsSample` download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples/3.x/HttpRequestsSample)], navigate to `/Operation` and refresh the page.</span></span> <span data-ttu-id="c61e8-239">La valeur de l’étendue de la demande change pour chaque demande, mais la valeur de la portée du gestionnaire change uniquement toutes les 5 secondes.</span><span class="sxs-lookup"><span data-stu-id="c61e8-239">The request scope value changes for each request, but the handler scope value only changes every 5 seconds.</span></span>

<span data-ttu-id="c61e8-240">Les gestionnaires peuvent dépendre des services de toute portée.</span><span class="sxs-lookup"><span data-stu-id="c61e8-240">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="c61e8-241">Les services dont dépendent les gestionnaires sont supprimés lorsque le gestionnaire est supprimé.</span><span class="sxs-lookup"><span data-stu-id="c61e8-241">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="c61e8-242">Utilisez l’une des approches suivantes pour partager l’état de chaque requête avec les gestionnaires de messages :</span><span class="sxs-lookup"><span data-stu-id="c61e8-242">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="c61e8-243">Transmettez les données dans le gestionnaire à l’aide de [HttpRequestMessage. Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span><span class="sxs-lookup"><span data-stu-id="c61e8-243">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="c61e8-244">Utilisez <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> pour accéder à la requête en cours.</span><span class="sxs-lookup"><span data-stu-id="c61e8-244">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="c61e8-245">Créez un objet de stockage <xref:System.Threading.AsyncLocal`1> personnalisé pour passer les données.</span><span class="sxs-lookup"><span data-stu-id="c61e8-245">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="c61e8-246">Utiliser les gestionnaires Polly</span><span class="sxs-lookup"><span data-stu-id="c61e8-246">Use Polly-based handlers</span></span>

<span data-ttu-id="c61e8-247">`IHttpClientFactory` s’intègre à la bibliothèque tierce [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="c61e8-247">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="c61e8-248">Polly est une bibliothèque complète de gestion des erreurs transitoires et de résilience pour .NET.</span><span class="sxs-lookup"><span data-stu-id="c61e8-248">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="c61e8-249">Elle permet aux développeurs de formuler facilement et de façon thread-safe des stratégies, comme Retry (Nouvelle tentative), Circuit Breaker (Disjoncteur), Timeout (Délai d’attente), Bulkhead Isolation (Isolation par cloisonnement) et Fallback (Alternative de repli).</span><span class="sxs-lookup"><span data-stu-id="c61e8-249">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="c61e8-250">Des méthodes d’extension sont fournies pour permettre l’utilisation de stratégies Polly avec les instances configurées de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-250">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="c61e8-251">Les extensions Polly prennent en charge l’ajout de gestionnaires basés sur Polly aux clients.</span><span class="sxs-lookup"><span data-stu-id="c61e8-251">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="c61e8-252">Polly requiert le package NuGet [Microsoft. extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .</span><span class="sxs-lookup"><span data-stu-id="c61e8-252">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="c61e8-253">Gérer les erreurs temporaires</span><span class="sxs-lookup"><span data-stu-id="c61e8-253">Handle transient faults</span></span>

<span data-ttu-id="c61e8-254">Les erreurs se produisent généralement lorsque les appels HTTP externes sont temporaires.</span><span class="sxs-lookup"><span data-stu-id="c61e8-254">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="c61e8-255"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> permet de définir une stratégie pour gérer les erreurs temporaires.</span><span class="sxs-lookup"><span data-stu-id="c61e8-255"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="c61e8-256">Les stratégies configurées avec `AddTransientHttpErrorPolicy` gèrent les réponses suivantes :</span><span class="sxs-lookup"><span data-stu-id="c61e8-256">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="c61e8-257">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="c61e8-257">HTTP 5xx</span></span>
* <span data-ttu-id="c61e8-258">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="c61e8-258">HTTP 408</span></span>

<span data-ttu-id="c61e8-259">`AddTransientHttpErrorPolicy` fournit l’accès à un `PolicyBuilder` objet configuré pour gérer les erreurs qui représentent une erreur temporaire possible :</span><span class="sxs-lookup"><span data-stu-id="c61e8-259">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="c61e8-260">Dans le code précédent, une stratégie `WaitAndRetryAsync` est définie.</span><span class="sxs-lookup"><span data-stu-id="c61e8-260">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="c61e8-261">Les requêtes qui ont échoué sont retentées jusqu’à trois fois avec un délai de 600 ms entre les tentatives.</span><span class="sxs-lookup"><span data-stu-id="c61e8-261">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="c61e8-262">Sélectionner dynamiquement des stratégies</span><span class="sxs-lookup"><span data-stu-id="c61e8-262">Dynamically select policies</span></span>

<span data-ttu-id="c61e8-263">Les méthodes d’extension sont fournies pour ajouter des gestionnaires basés sur Polly, par exemple, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*> .</span><span class="sxs-lookup"><span data-stu-id="c61e8-263">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="c61e8-264">La `AddPolicyHandler` surcharge suivante inspecte la demande pour décider de la stratégie à appliquer :</span><span class="sxs-lookup"><span data-stu-id="c61e8-264">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="c61e8-265">Dans le code précédent, si la requête sortante est un HTTP GET, un délai d’attente de 10 secondes est appliqué.</span><span class="sxs-lookup"><span data-stu-id="c61e8-265">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="c61e8-266">Pour toutes les autres méthodes HTTP, un délai d’attente de 30 secondes est utilisé.</span><span class="sxs-lookup"><span data-stu-id="c61e8-266">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="c61e8-267">Ajouter plusieurs gestionnaires Polly</span><span class="sxs-lookup"><span data-stu-id="c61e8-267">Add multiple Polly handlers</span></span>

<span data-ttu-id="c61e8-268">Il est courant d’imbriquer les stratégies Polly :</span><span class="sxs-lookup"><span data-stu-id="c61e8-268">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="c61e8-269">Dans l’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="c61e8-269">In the preceding example:</span></span>

* <span data-ttu-id="c61e8-270">Deux gestionnaires sont ajoutés.</span><span class="sxs-lookup"><span data-stu-id="c61e8-270">Two handlers are added.</span></span>
* <span data-ttu-id="c61e8-271">Le premier gestionnaire utilise <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> pour ajouter une stratégie de nouvelle tentative.</span><span class="sxs-lookup"><span data-stu-id="c61e8-271">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="c61e8-272">Les requêtes qui ont échoué sont retentées jusqu’à trois fois.</span><span class="sxs-lookup"><span data-stu-id="c61e8-272">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="c61e8-273">Le deuxième `AddTransientHttpErrorPolicy` appel ajoute une stratégie de disjoncteur.</span><span class="sxs-lookup"><span data-stu-id="c61e8-273">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="c61e8-274">Les demandes externes supplémentaires sont bloquées pendant 30 secondes si 5 tentatives ayant échoué se produisent de façon séquentielle.</span><span class="sxs-lookup"><span data-stu-id="c61e8-274">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="c61e8-275">Les stratégies de disjoncteur sont avec état.</span><span class="sxs-lookup"><span data-stu-id="c61e8-275">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="c61e8-276">Tous les appels effectués via ce client partagent le même état du circuit.</span><span class="sxs-lookup"><span data-stu-id="c61e8-276">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="c61e8-277">Ajouter des stratégies à partir du Registre Polly</span><span class="sxs-lookup"><span data-stu-id="c61e8-277">Add policies from the Polly registry</span></span>

<span data-ttu-id="c61e8-278">Une approche de la gestion des stratégies régulièrement utilisées consiste à les définir une seule fois et à les inscrire avec un `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-278">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="c61e8-279">Dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="c61e8-279">In the following code:</span></span>

* <span data-ttu-id="c61e8-280">Les stratégies « standard » et « longues » sont ajoutées.</span><span class="sxs-lookup"><span data-stu-id="c61e8-280">The "regular" and "long" policies are added.</span></span>
* <span data-ttu-id="c61e8-281"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> Ajoute les stratégies « standard » et « longues » à partir du Registre.</span><span class="sxs-lookup"><span data-stu-id="c61e8-281"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="c61e8-282">Pour plus d’informations sur `IHttpClientFactory` les intégrations de et de Polly, consultez le [wiki Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="c61e8-282">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="c61e8-283">HttpClient et gestion de la durée de vie</span><span class="sxs-lookup"><span data-stu-id="c61e8-283">HttpClient and lifetime management</span></span>

<span data-ttu-id="c61e8-284">Une nouvelle instance `HttpClient` est retournée à chaque fois que `CreateClient` est appelé sur `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-284">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="c61e8-285">Un <xref:System.Net.Http.HttpMessageHandler> est créé par client nommé.</span><span class="sxs-lookup"><span data-stu-id="c61e8-285">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="c61e8-286">La fabrique gère les durées de vie des instances `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-286">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="c61e8-287">`IHttpClientFactory` regroupe dans un pool les instances de `HttpMessageHandler` créées par la fabrique pour réduire la consommation des ressources.</span><span class="sxs-lookup"><span data-stu-id="c61e8-287">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="c61e8-288">Une instance de `HttpMessageHandler` peut être réutilisée à partir du pool quand vous créez une instance de `HttpClient` si sa durée de vie n’a pas expiré.</span><span class="sxs-lookup"><span data-stu-id="c61e8-288">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="c61e8-289">Le regroupement de gestionnaires en pools est souhaitable, car chaque gestionnaire gère habituellement ses propres connexions HTTP sous-jacentes.</span><span class="sxs-lookup"><span data-stu-id="c61e8-289">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="c61e8-290">La création de plus de gestionnaires que nécessaire peut entraîner des délais de connexion.</span><span class="sxs-lookup"><span data-stu-id="c61e8-290">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="c61e8-291">Certains gestionnaires maintiennent également les connexions ouvertes indéfiniment, ce qui peut empêcher le gestionnaire de réagir aux modifications DNS (Domain Name System).</span><span class="sxs-lookup"><span data-stu-id="c61e8-291">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="c61e8-292">La durée de vie par défaut d’un gestionnaire est de deux minutes.</span><span class="sxs-lookup"><span data-stu-id="c61e8-292">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="c61e8-293">La valeur par défaut peut être substituée pour chaque client nommé :</span><span class="sxs-lookup"><span data-stu-id="c61e8-293">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="c61e8-294">`HttpClient` les instances peuvent généralement être traitées comme des objets .NET qui **ne nécessitent pas** de suppression.</span><span class="sxs-lookup"><span data-stu-id="c61e8-294">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="c61e8-295">La suppression annule les requêtes sortantes et garantit que l’instance `HttpClient` donnée ne peut pas être utilisée après avoir appelé <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="c61e8-295">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="c61e8-296">`IHttpClientFactory` effectue le suivi et libère les ressources utilisées par les instances `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-296">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="c61e8-297">Le fait de conserver une seule instance `HttpClient` active pendant une longue durée est un modèle commun utilisé avant le lancement de `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-297">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="c61e8-298">Ce modèle devient inutile après la migration vers `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-298">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="c61e8-299">Alternatives à IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="c61e8-299">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="c61e8-300">L’utilisation `IHttpClientFactory` de dans une application di-Enabled évite les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="c61e8-300">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="c61e8-301">Problèmes d’épuisement des ressources par les instances de regroupement `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="c61e8-301">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="c61e8-302">Problèmes DNS périmés par les instances cycliques `HttpMessageHandler` à intervalles réguliers.</span><span class="sxs-lookup"><span data-stu-id="c61e8-302">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="c61e8-303">Il existe d’autres façons de résoudre les problèmes précédents à l’aide d’une instance de longue durée <xref:System.Net.Http.SocketsHttpHandler> .</span><span class="sxs-lookup"><span data-stu-id="c61e8-303">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="c61e8-304">Créez une instance de `SocketsHttpHandler` lorsque l’application démarre et utilisez-la pendant toute la durée de vie de l’application.</span><span class="sxs-lookup"><span data-stu-id="c61e8-304">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="c61e8-305">Configurez <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> avec une valeur appropriée en fonction des temps d’actualisation DNS.</span><span class="sxs-lookup"><span data-stu-id="c61e8-305">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="c61e8-306">Créez des `HttpClient` instances en utilisant `new HttpClient(handler, disposeHandler: false)` si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="c61e8-306">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="c61e8-307">Les approches précédentes résolvent les problèmes de gestion des ressources qui sont `IHttpClientFactory` résolus de la même façon.</span><span class="sxs-lookup"><span data-stu-id="c61e8-307">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="c61e8-308">Le `SocketsHttpHandler` partage les connexions entre les `HttpClient` instances.</span><span class="sxs-lookup"><span data-stu-id="c61e8-308">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="c61e8-309">Ce partage empêche l’épuisement des sockets.</span><span class="sxs-lookup"><span data-stu-id="c61e8-309">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="c61e8-310">Le `SocketsHttpHandler` cycle des connexions en fonction de `PooledConnectionLifetime` pour éviter les problèmes DNS périmés.</span><span class="sxs-lookup"><span data-stu-id="c61e8-310">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="no-loccookies"></a><span data-ttu-id="c61e8-311">Cookies</span><span class="sxs-lookup"><span data-stu-id="c61e8-311">Cookies</span></span>

<span data-ttu-id="c61e8-312">Les instances regroupées `HttpMessageHandler` entraînent le `CookieContainer` partage des objets.</span><span class="sxs-lookup"><span data-stu-id="c61e8-312">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="c61e8-313">Le `CookieContainer` partage d’objets imprévus aboutit souvent à un code incorrect.</span><span class="sxs-lookup"><span data-stu-id="c61e8-313">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="c61e8-314">Pour les applications qui nécessitent des cookie , envisagez l’une des deux opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="c61e8-314">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="c61e8-315">Désactivation de la cookie gestion automatique</span><span class="sxs-lookup"><span data-stu-id="c61e8-315">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="c61e8-316">Éviter `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="c61e8-316">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="c61e8-317">Appelez <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> pour désactiver la cookie gestion automatique :</span><span class="sxs-lookup"><span data-stu-id="c61e8-317">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="c61e8-318">Journalisation</span><span class="sxs-lookup"><span data-stu-id="c61e8-318">Logging</span></span>

<span data-ttu-id="c61e8-319">Les clients créés via `IHttpClientFactory` enregistrent les messages de journalisation pour toutes les requêtes.</span><span class="sxs-lookup"><span data-stu-id="c61e8-319">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="c61e8-320">Activez le niveau d’information approprié dans la configuration de la journalisation pour voir les messages du journal par défaut.</span><span class="sxs-lookup"><span data-stu-id="c61e8-320">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="c61e8-321">Une journalisation supplémentaire, comme celle des en-têtes des requêtes, est incluse seulement au niveau de trace.</span><span class="sxs-lookup"><span data-stu-id="c61e8-321">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="c61e8-322">La catégorie de journal utilisée pour chaque client comprend le nom du client.</span><span class="sxs-lookup"><span data-stu-id="c61e8-322">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="c61e8-323">Un client nommé *MyNamedClient*, par exemple, journalise des messages avec la catégorie « System .net. http. httpclient ». **MyNamedClient**. LogicalHandler".</span><span class="sxs-lookup"><span data-stu-id="c61e8-323">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="c61e8-324">Les messages avec le suffixe *LogicalHandler* se produisent en dehors du pipeline du gestionnaire de requêtes.</span><span class="sxs-lookup"><span data-stu-id="c61e8-324">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="c61e8-325">Lors d’une requête, les messages sont journalisés avant que d’autres gestionnaires du pipeline l’aient traitée.</span><span class="sxs-lookup"><span data-stu-id="c61e8-325">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="c61e8-326">Lors d’une réponse, les messages sont journalisés une fois que tous les autres gestionnaires du pipeline ont reçu la réponse.</span><span class="sxs-lookup"><span data-stu-id="c61e8-326">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="c61e8-327">La journalisation se produit également à l’intérieur du pipeline du gestionnaire de requêtes.</span><span class="sxs-lookup"><span data-stu-id="c61e8-327">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="c61e8-328">Dans l’exemple *MyNamedClient* , ces messages sont journalisés avec la catégorie de journal « System .net. http. httpclient ». **MyNamedClient**. ClientHandler".</span><span class="sxs-lookup"><span data-stu-id="c61e8-328">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="c61e8-329">Pour la requête, cela se produit une fois que tous les autres gestionnaires ont été exécutés et immédiatement avant l’envoi de la demande.</span><span class="sxs-lookup"><span data-stu-id="c61e8-329">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="c61e8-330">Lors de la réponse, cette journalisation inclut l’état de la réponse avant qu’elle repasse à travers le pipeline de gestionnaires.</span><span class="sxs-lookup"><span data-stu-id="c61e8-330">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="c61e8-331">L’activation de la journalisation à l’extérieur et à l’intérieur du pipeline permet l’inspection des changements apportés par les autres gestionnaires du pipeline.</span><span class="sxs-lookup"><span data-stu-id="c61e8-331">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="c61e8-332">Cela peut inclure des modifications apportées aux en-têtes de demande ou au code d’état de réponse.</span><span class="sxs-lookup"><span data-stu-id="c61e8-332">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="c61e8-333">L’inclusion du nom du client dans la catégorie journal active le filtrage des journaux pour des clients nommés spécifiques.</span><span class="sxs-lookup"><span data-stu-id="c61e8-333">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="c61e8-334">Configurer le HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="c61e8-334">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="c61e8-335">Il peut être nécessaire de contrôler la configuration du `HttpMessageHandler` interne utilisé par un client.</span><span class="sxs-lookup"><span data-stu-id="c61e8-335">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="c61e8-336">Un `IHttpClientBuilder` est retourné quand vous ajoutez des clients nommés ou typés.</span><span class="sxs-lookup"><span data-stu-id="c61e8-336">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="c61e8-337">La méthode d’extension <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> peut être utilisée pour définir un délégué.</span><span class="sxs-lookup"><span data-stu-id="c61e8-337">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="c61e8-338">Le délégué est utilisé pour créer et configurer le `HttpMessageHandler` principal utilisé par ce client :</span><span class="sxs-lookup"><span data-stu-id="c61e8-338">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="c61e8-339">Utiliser IHttpClientFactory dans une application console</span><span class="sxs-lookup"><span data-stu-id="c61e8-339">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="c61e8-340">Dans une application console, ajoutez les références de package suivantes au projet :</span><span class="sxs-lookup"><span data-stu-id="c61e8-340">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="c61e8-341">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="c61e8-341">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="c61e8-342">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="c61e8-342">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="c61e8-343">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="c61e8-343">In the following example:</span></span>

* <span data-ttu-id="c61e8-344"><xref:System.Net.Http.IHttpClientFactory> est inscrit dans le conteneur de service dans de l’[hôte générique](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="c61e8-344"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="c61e8-345">`MyService` crée une instance de fabrique cliente à partir du service, qui est utilisée pour créer un `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-345">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="c61e8-346">`HttpClient` est utilisé pour récupérer une page web.</span><span class="sxs-lookup"><span data-stu-id="c61e8-346">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="c61e8-347">`Main` crée une étendue pour exécuter la méthode `GetPage` du service et écrire les 500 premiers caractères du contenu de la page web dans la console.</span><span class="sxs-lookup"><span data-stu-id="c61e8-347">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="c61e8-348">Intergiciel (middleware) de propagation d’en-tête</span><span class="sxs-lookup"><span data-stu-id="c61e8-348">Header propagation middleware</span></span>

<span data-ttu-id="c61e8-349">La propagation d’en-tête est un intergiciel (middleware) ASP.NET Core pour propager les en-têtes HTTP de la requête entrante vers les demandes du client HTTP sortantes.</span><span class="sxs-lookup"><span data-stu-id="c61e8-349">Header propagation is an ASP.NET Core middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="c61e8-350">Pour utiliser la propagation d’en-tête :</span><span class="sxs-lookup"><span data-stu-id="c61e8-350">To use header propagation:</span></span>

* <span data-ttu-id="c61e8-351">Référencez le package [Microsoft. AspNetCore. HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) .</span><span class="sxs-lookup"><span data-stu-id="c61e8-351">Reference the [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) package.</span></span>
* <span data-ttu-id="c61e8-352">Configurer l’intergiciel et `HttpClient` dans `Startup` :</span><span class="sxs-lookup"><span data-stu-id="c61e8-352">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* <span data-ttu-id="c61e8-353">Le client comprend les en-têtes configurés pour les demandes sortantes :</span><span class="sxs-lookup"><span data-stu-id="c61e8-353">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="c61e8-354">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="c61e8-354">Additional resources</span></span>

* [<span data-ttu-id="c61e8-355">Utilisez HttpClientFactory pour implémenter des requêtes HTTP résilientes</span><span class="sxs-lookup"><span data-stu-id="c61e8-355">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="c61e8-356">Implémenter de nouvelles tentatives d’appel HTTP avec interruption exponentielle avec des stratégies Polly et HttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="c61e8-356">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="c61e8-357">Implémenter le modèle Disjoncteur</span><span class="sxs-lookup"><span data-stu-id="c61e8-357">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [<span data-ttu-id="c61e8-358">Comment sérialiser et désérialiser JSON dans .NET</span><span class="sxs-lookup"><span data-stu-id="c61e8-358">How to serialize and deserialize JSON in .NET</span></span>](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="c61e8-359">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak) et [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="c61e8-359">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="c61e8-360">Une <xref:System.Net.Http.IHttpClientFactory> peut être inscrite et utilisée pour configurer et créer des instances de <xref:System.Net.Http.HttpClient> dans une application.</span><span class="sxs-lookup"><span data-stu-id="c61e8-360">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="c61e8-361">Elle offre les avantages suivants :</span><span class="sxs-lookup"><span data-stu-id="c61e8-361">It offers the following benefits:</span></span>

* <span data-ttu-id="c61e8-362">Fournit un emplacement central pour le nommage et la configuration d’instance de `HttpClient` logiques.</span><span class="sxs-lookup"><span data-stu-id="c61e8-362">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="c61e8-363">Par exemple, un client *github* peut être inscrit et configuré pour accéder à [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="c61e8-363">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="c61e8-364">Un client par défaut peut être inscrit à d’autres fins.</span><span class="sxs-lookup"><span data-stu-id="c61e8-364">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="c61e8-365">Codifie le concept de middleware (intergiciel) sortant via la délégation de gestionnaires dans `HttpClient` et fournit des extensions permettant au middleware Polly d’en tirer parti.</span><span class="sxs-lookup"><span data-stu-id="c61e8-365">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="c61e8-366">Gère le regroupement et la durée de vie des instances de `HttpClientMessageHandler` sous-jacentes pour éviter les problèmes DNS courants qui se produisent lors de la gestion manuelle des durées de vie de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-366">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="c61e8-367">Ajoute une expérience de journalisation configurable (via `ILogger`) pour toutes les requêtes envoyées via des clients créés par la fabrique.</span><span class="sxs-lookup"><span data-stu-id="c61e8-367">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="c61e8-368">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c61e8-368">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="c61e8-369">Modèles de consommation</span><span class="sxs-lookup"><span data-stu-id="c61e8-369">Consumption patterns</span></span>

<span data-ttu-id="c61e8-370">Vous pouvez utiliser `IHttpClientFactory` dans une application de plusieurs façons :</span><span class="sxs-lookup"><span data-stu-id="c61e8-370">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="c61e8-371">Utilisation de base</span><span class="sxs-lookup"><span data-stu-id="c61e8-371">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="c61e8-372">Clients nommés</span><span class="sxs-lookup"><span data-stu-id="c61e8-372">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="c61e8-373">Clients typés</span><span class="sxs-lookup"><span data-stu-id="c61e8-373">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="c61e8-374">Clients générés</span><span class="sxs-lookup"><span data-stu-id="c61e8-374">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="c61e8-375">Aucune d’entre elles n’est meilleure qu’une autre.</span><span class="sxs-lookup"><span data-stu-id="c61e8-375">None of them are strictly superior to another.</span></span> <span data-ttu-id="c61e8-376">La meilleure approche dépend des contraintes de l’application.</span><span class="sxs-lookup"><span data-stu-id="c61e8-376">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="c61e8-377">Utilisation de base</span><span class="sxs-lookup"><span data-stu-id="c61e8-377">Basic usage</span></span>

<span data-ttu-id="c61e8-378">Vous pouvez inscrire la `IHttpClientFactory` en appelant la méthode d’extension `AddHttpClient` sur la `IServiceCollection`, à l’intérieur la méthode `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-378">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="c61e8-379">Une fois inscrit, le code peut accepter un `IHttpClientFactory` partout où des services peuvent être injectés avec [une injection de dépendance](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="c61e8-379">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="c61e8-380">Le `IHttpClientFactory` peut être utilisé pour créer une `HttpClient` instance de :</span><span class="sxs-lookup"><span data-stu-id="c61e8-380">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="c61e8-381">L’utilisation de `IHttpClientFactory` de cette façon est un excellent moyen de refactoriser une application existante.</span><span class="sxs-lookup"><span data-stu-id="c61e8-381">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="c61e8-382">Elle n’a aucun impact sur la façon dont `HttpClient` est utilisé.</span><span class="sxs-lookup"><span data-stu-id="c61e8-382">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="c61e8-383">Dans les endroits où les instances de `HttpClient` sont actuellement créées, remplacez ces occurrences par un appel à <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="c61e8-383">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="c61e8-384">Clients nommés</span><span class="sxs-lookup"><span data-stu-id="c61e8-384">Named clients</span></span>

<span data-ttu-id="c61e8-385">Si une application nécessite plusieurs utilisations distinctes de `HttpClient`, chacune avec une configuration différente, une option consiste à utiliser des **clients nommés**.</span><span class="sxs-lookup"><span data-stu-id="c61e8-385">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="c61e8-386">La configuration d’un `HttpClient` nommé peut être spécifiée lors de l’inscription dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-386">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="c61e8-387">Dans le code précédent, `AddHttpClient` est appelé en fournissant le nom *github*.</span><span class="sxs-lookup"><span data-stu-id="c61e8-387">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="c61e8-388">Une configuration par défaut est appliquée à ce client : l’adresse de base et deux en-têtes nécessaires pour utiliser l’API GitHub.</span><span class="sxs-lookup"><span data-stu-id="c61e8-388">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="c61e8-389">Chaque fois que `CreateClient` est appelée, une nouvelle instance de `HttpClient` est créée et l’action de configuration est appelée.</span><span class="sxs-lookup"><span data-stu-id="c61e8-389">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="c61e8-390">Pour utiliser un client nommé, un paramètre de chaîne peut être passé à `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-390">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="c61e8-391">Spécifiez le nom du client à créer :</span><span class="sxs-lookup"><span data-stu-id="c61e8-391">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="c61e8-392">Dans le code précédent, la requête n’a pas besoin de spécifier un nom d’hôte.</span><span class="sxs-lookup"><span data-stu-id="c61e8-392">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="c61e8-393">Elle peut simplement passer le chemin, car l’adresse de base configurée pour le client est utilisée.</span><span class="sxs-lookup"><span data-stu-id="c61e8-393">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="c61e8-394">Clients typés</span><span class="sxs-lookup"><span data-stu-id="c61e8-394">Typed clients</span></span>

<span data-ttu-id="c61e8-395">Clients typés :</span><span class="sxs-lookup"><span data-stu-id="c61e8-395">Typed clients:</span></span>

* <span data-ttu-id="c61e8-396">Fournissent les mêmes fonctionnalités que les clients nommés, sans qu’il soit nécessaire d’utiliser des chaînes comme clés.</span><span class="sxs-lookup"><span data-stu-id="c61e8-396">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="c61e8-397">Bénéficie de l’aide d’IntelliSense et du compilateur lors de l’utilisation des clients.</span><span class="sxs-lookup"><span data-stu-id="c61e8-397">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="c61e8-398">Fournissent un emplacement unique pour configurer et interagir avec un `HttpClient` particulier.</span><span class="sxs-lookup"><span data-stu-id="c61e8-398">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="c61e8-399">Par exemple, un même client typé peut être utilisé pour un point de terminaison de backend et pour encapsuler la logique relative à ce point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="c61e8-399">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="c61e8-400">Fonctionnent avec l’injection de dépendances et peuvent être injectés là où c’est nécessaire dans votre application.</span><span class="sxs-lookup"><span data-stu-id="c61e8-400">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="c61e8-401">Un client typé accepte un `HttpClient` paramètre dans son constructeur :</span><span class="sxs-lookup"><span data-stu-id="c61e8-401">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="c61e8-402">Dans le code précédent, la configuration est déplacée dans le client typé.</span><span class="sxs-lookup"><span data-stu-id="c61e8-402">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="c61e8-403">L’objet `HttpClient` est exposé en tant que propriété publique.</span><span class="sxs-lookup"><span data-stu-id="c61e8-403">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="c61e8-404">Il est possible de définir des méthodes d’API spécifiques qui exposent les fonctionnalités de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-404">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="c61e8-405">La méthode `GetAspNetDocsIssues` encapsule le code nécessaire pour interroger et analyser les problèmes ouverts les plus récents d’un dépôt GitHub.</span><span class="sxs-lookup"><span data-stu-id="c61e8-405">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="c61e8-406">Pour inscrire un client typé, la méthode d’extension <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> générique peut être utilisée dans `Startup.ConfigureServices`, en spécifiant la classe du client typé :</span><span class="sxs-lookup"><span data-stu-id="c61e8-406">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="c61e8-407">Le client typé est inscrit comme étant transitoire avec injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="c61e8-407">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="c61e8-408">Le client typé peut être injecté et utilisé directement :</span><span class="sxs-lookup"><span data-stu-id="c61e8-408">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="c61e8-409">Si vous préférez, vous pouvez spécifier la configuration d’un client typé lors de l’inscription dans `Startup.ConfigureServices` au lieu de le faire dans le constructeur du client typé :</span><span class="sxs-lookup"><span data-stu-id="c61e8-409">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="c61e8-410">Il est possible d’encapsuler entièrement le `HttpClient` dans un client typé.</span><span class="sxs-lookup"><span data-stu-id="c61e8-410">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="c61e8-411">Au lieu de l’exposer en tant que propriété, vous pouvez fournir des méthodes publiques qui appellent l’instance de `HttpClient` en interne.</span><span class="sxs-lookup"><span data-stu-id="c61e8-411">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="c61e8-412">Dans le code précédent, le `HttpClient` est stocké en tant que champ privé.</span><span class="sxs-lookup"><span data-stu-id="c61e8-412">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="c61e8-413">Tous les accès nécessaires pour effectuer des appels externes passent par la méthode `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-413">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="c61e8-414">Clients générés</span><span class="sxs-lookup"><span data-stu-id="c61e8-414">Generated clients</span></span>

<span data-ttu-id="c61e8-415">`IHttpClientFactory` peut être utilisé en combinaison avec d’autres bibliothèques tierces, comme [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="c61e8-415">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="c61e8-416">Refit est une bibliothèque REST pour .NET.</span><span class="sxs-lookup"><span data-stu-id="c61e8-416">Refit is a REST library for .NET.</span></span> <span data-ttu-id="c61e8-417">Il convertit les API REST en interfaces dynamiques.</span><span class="sxs-lookup"><span data-stu-id="c61e8-417">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="c61e8-418">Une implémentation de l’interface est générée dynamiquement par le `RestService`, avec `HttpClient` pour faire les appels HTTP externes.</span><span class="sxs-lookup"><span data-stu-id="c61e8-418">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="c61e8-419">Une interface et une réponse sont définies pour représenter l’API externe et sa réponse :</span><span class="sxs-lookup"><span data-stu-id="c61e8-419">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="c61e8-420">Vous pouvez ajouter un client typé en utilisant Refit pour générer l’implémentation :</span><span class="sxs-lookup"><span data-stu-id="c61e8-420">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("https://localhost:5001");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

<span data-ttu-id="c61e8-421">L’interface définie peut être utilisée quand c’est nécessaire, avec l’implémentation fournie par l’injection de dépendances et Refit :</span><span class="sxs-lookup"><span data-stu-id="c61e8-421">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="c61e8-422">Middleware pour les requêtes sortantes</span><span class="sxs-lookup"><span data-stu-id="c61e8-422">Outgoing request middleware</span></span>

<span data-ttu-id="c61e8-423">`HttpClient` intègre déjà le concept de délégation des gestionnaires qui peuvent être liés ensemble pour les requêtes HTTP sortantes.</span><span class="sxs-lookup"><span data-stu-id="c61e8-423">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="c61e8-424">Le `IHttpClientFactory` permet de définir facilement les gestionnaires à appliquer pour chaque client nommé.</span><span class="sxs-lookup"><span data-stu-id="c61e8-424">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="c61e8-425">Il prend en charge l’inscription et le chaînage de plusieurs gestionnaires pour créer un pipeline de middlewares pour les requêtes sortantes.</span><span class="sxs-lookup"><span data-stu-id="c61e8-425">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="c61e8-426">Chacun de ces gestionnaires peut effectuer un travail avant et après la requête sortante.</span><span class="sxs-lookup"><span data-stu-id="c61e8-426">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="c61e8-427">Ce modèle est similaire au pipeline de middlewares entrants dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c61e8-427">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="c61e8-428">Le modèle fournit un mécanisme permettant de gérer les problèmes transversaux liés aux des requêtes HTTP, notamment la mise en cache, la gestion des erreurs, la sérialisation et la journalisation.</span><span class="sxs-lookup"><span data-stu-id="c61e8-428">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="c61e8-429">Pour créer un gestionnaire, définissez une classe dérivant de <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="c61e8-429">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="c61e8-430">Remplacez la méthode `SendAsync` de façon à exécuter du code avant de passer la requête au gestionnaire suivant dans le pipeline :</span><span class="sxs-lookup"><span data-stu-id="c61e8-430">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="c61e8-431">Le code précédent définit un gestionnaire de base.</span><span class="sxs-lookup"><span data-stu-id="c61e8-431">The preceding code defines a basic handler.</span></span> <span data-ttu-id="c61e8-432">Il vérifie si un en-tête `X-API-KEY` a été inclus dans la requête.</span><span class="sxs-lookup"><span data-stu-id="c61e8-432">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="c61e8-433">Si l’en-tête est manquant, il peut éviter l’appel HTTP et retourner une réponse appropriée.</span><span class="sxs-lookup"><span data-stu-id="c61e8-433">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="c61e8-434">Au cours de l’inscription, un ou plusieurs gestionnaires peuvent être ajoutés à la configuration d’un `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="c61e8-434">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="c61e8-435">Cette tâche est accomplie via des méthodes d’extension sur le <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span><span class="sxs-lookup"><span data-stu-id="c61e8-435">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="c61e8-436">Dans le code précédent, le `ValidateHeaderHandler` est inscrit avec une injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="c61e8-436">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="c61e8-437">`IHttpClientFactory` crée une étendue DI distincte pour chaque gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="c61e8-437">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="c61e8-438">Les gestionnaires peuvent librement dépendre des services de n’importe quelle étendue.</span><span class="sxs-lookup"><span data-stu-id="c61e8-438">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="c61e8-439">Les services dont dépendent les gestionnaires sont supprimés lorsque le gestionnaire est supprimé.</span><span class="sxs-lookup"><span data-stu-id="c61e8-439">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="c61e8-440">Une fois inscrit, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> peut être appelé en passant en entrée le type pour le gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="c61e8-440">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="c61e8-441">Vous pouvez inscrire plusieurs gestionnaires dans l’ordre où ils doivent être exécutés.</span><span class="sxs-lookup"><span data-stu-id="c61e8-441">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="c61e8-442">Chaque gestionnaire wrappe le gestionnaire suivant jusqu’à ce que le dernier `HttpClientHandler` exécute la requête :</span><span class="sxs-lookup"><span data-stu-id="c61e8-442">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="c61e8-443">Utilisez l’une des approches suivantes pour partager l’état de chaque requête avec les gestionnaires de messages :</span><span class="sxs-lookup"><span data-stu-id="c61e8-443">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="c61e8-444">Passez des données dans le gestionnaire en utilisant `HttpRequestMessage.Properties`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-444">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="c61e8-445">Utilisez `IHttpContextAccessor` pour accéder à la requête en cours.</span><span class="sxs-lookup"><span data-stu-id="c61e8-445">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="c61e8-446">Créez un objet de stockage `AsyncLocal` personnalisé pour passer les données.</span><span class="sxs-lookup"><span data-stu-id="c61e8-446">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="c61e8-447">Utiliser les gestionnaires Polly</span><span class="sxs-lookup"><span data-stu-id="c61e8-447">Use Polly-based handlers</span></span>

<span data-ttu-id="c61e8-448">`IHttpClientFactory` s’intègre à une bibliothèque tierce très utilisée, appelée [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="c61e8-448">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="c61e8-449">Polly est une bibliothèque complète de gestion des erreurs transitoires et de résilience pour .NET.</span><span class="sxs-lookup"><span data-stu-id="c61e8-449">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="c61e8-450">Elle permet aux développeurs de formuler facilement et de façon thread-safe des stratégies, comme Retry (Nouvelle tentative), Circuit Breaker (Disjoncteur), Timeout (Délai d’attente), Bulkhead Isolation (Isolation par cloisonnement) et Fallback (Alternative de repli).</span><span class="sxs-lookup"><span data-stu-id="c61e8-450">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="c61e8-451">Des méthodes d’extension sont fournies pour permettre l’utilisation de stratégies Polly avec les instances configurées de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-451">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="c61e8-452">Les extensions Polly :</span><span class="sxs-lookup"><span data-stu-id="c61e8-452">The Polly extensions:</span></span>

* <span data-ttu-id="c61e8-453">Prennent en charge l’ajout de gestionnaires Polly à des clients.</span><span class="sxs-lookup"><span data-stu-id="c61e8-453">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="c61e8-454">Peuvent être utilisées après l’installation du package NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/).</span><span class="sxs-lookup"><span data-stu-id="c61e8-454">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="c61e8-455">Ce package n’est pas inclus dans le framework partagé ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c61e8-455">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="c61e8-456">Gérer les erreurs temporaires</span><span class="sxs-lookup"><span data-stu-id="c61e8-456">Handle transient faults</span></span>

<span data-ttu-id="c61e8-457">Les erreurs courantes se produisent lorsque des appels HTTP externes sont temporaires.</span><span class="sxs-lookup"><span data-stu-id="c61e8-457">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="c61e8-458">Une méthode d’extension pratique nommée `AddTransientHttpErrorPolicy` est incluse : elle permet de définir une stratégie pour gérer les erreurs temporaires.</span><span class="sxs-lookup"><span data-stu-id="c61e8-458">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="c61e8-459">Les stratégies configurées avec cette méthode d’extension gèrent `HttpRequestException`, les réponses HTTP 5xx et les réponses HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="c61e8-459">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="c61e8-460">L’extension `AddTransientHttpErrorPolicy` peut être utilisée dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-460">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c61e8-461">L’extension fournit l’accès à un objet `PolicyBuilder` configuré pour gérer les erreurs représentant une erreur temporaire possible :</span><span class="sxs-lookup"><span data-stu-id="c61e8-461">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="c61e8-462">Dans le code précédent, une stratégie `WaitAndRetryAsync` est définie.</span><span class="sxs-lookup"><span data-stu-id="c61e8-462">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="c61e8-463">Les requêtes qui ont échoué sont retentées jusqu’à trois fois avec un délai de 600 ms entre les tentatives.</span><span class="sxs-lookup"><span data-stu-id="c61e8-463">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="c61e8-464">Sélectionner dynamiquement des stratégies</span><span class="sxs-lookup"><span data-stu-id="c61e8-464">Dynamically select policies</span></span>

<span data-ttu-id="c61e8-465">Il existe d’autres méthodes d’extension que vous pouvez utiliser pour ajouter des gestionnaires Polly.</span><span class="sxs-lookup"><span data-stu-id="c61e8-465">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="c61e8-466">Une de ces extensions est `AddPolicyHandler`, qui a plusieurs surcharges.</span><span class="sxs-lookup"><span data-stu-id="c61e8-466">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="c61e8-467">Une de ces surcharges permet l’inspection de la requête lors de la définition de la stratégie à appliquer :</span><span class="sxs-lookup"><span data-stu-id="c61e8-467">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="c61e8-468">Dans le code précédent, si la requête sortante est un HTTP GET, un délai d’attente de 10 secondes est appliqué.</span><span class="sxs-lookup"><span data-stu-id="c61e8-468">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="c61e8-469">Pour toutes les autres méthodes HTTP, un délai d’attente de 30 secondes est utilisé.</span><span class="sxs-lookup"><span data-stu-id="c61e8-469">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="c61e8-470">Ajouter plusieurs gestionnaires Polly</span><span class="sxs-lookup"><span data-stu-id="c61e8-470">Add multiple Polly handlers</span></span>

<span data-ttu-id="c61e8-471">Il est courant d’imbriquer des stratégies Polly pour fournir des fonctionnalités améliorées :</span><span class="sxs-lookup"><span data-stu-id="c61e8-471">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="c61e8-472">Dans l’exemple précédent, deux gestionnaires sont ajoutés.</span><span class="sxs-lookup"><span data-stu-id="c61e8-472">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="c61e8-473">Le premier utilise l’extension `AddTransientHttpErrorPolicy` pour ajouter une stratégie de nouvelle tentative.</span><span class="sxs-lookup"><span data-stu-id="c61e8-473">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="c61e8-474">Les requêtes qui ont échoué sont retentées jusqu’à trois fois.</span><span class="sxs-lookup"><span data-stu-id="c61e8-474">Failed requests are retried up to three times.</span></span> <span data-ttu-id="c61e8-475">Le deuxième appel à `AddTransientHttpErrorPolicy` ajoute une stratégie de disjoncteur.</span><span class="sxs-lookup"><span data-stu-id="c61e8-475">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="c61e8-476">Les requêtes externes supplémentaires sont bloquées pendant 30 secondes si cinq tentatives successives échouent.</span><span class="sxs-lookup"><span data-stu-id="c61e8-476">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="c61e8-477">Les stratégies de disjoncteur sont avec état.</span><span class="sxs-lookup"><span data-stu-id="c61e8-477">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="c61e8-478">Tous les appels effectués via ce client partagent le même état du circuit.</span><span class="sxs-lookup"><span data-stu-id="c61e8-478">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="c61e8-479">Ajouter des stratégies à partir du Registre Polly</span><span class="sxs-lookup"><span data-stu-id="c61e8-479">Add policies from the Polly registry</span></span>

<span data-ttu-id="c61e8-480">Une approche de la gestion des stratégies régulièrement utilisées consiste à les définir une seule fois et à les inscrire avec un `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-480">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="c61e8-481">Il existe une méthode d’extension qui permet l’ajout d’un gestionnaire avec une stratégie du Registre :</span><span class="sxs-lookup"><span data-stu-id="c61e8-481">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="c61e8-482">Dans le code précédent, deux stratégies sont inscrites lorsque `PolicyRegistry` est ajouté à `ServiceCollection`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-482">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="c61e8-483">Pour utiliser une stratégie du Registre, la méthode `AddPolicyHandlerFromRegistry` est utilisée, en passant le nom de la stratégie à appliquer.</span><span class="sxs-lookup"><span data-stu-id="c61e8-483">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="c61e8-484">Vous trouverez plus d’informations sur les intégrations de `IHttpClientFactory` et de Polly dans le [wiki Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="c61e8-484">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="c61e8-485">HttpClient et gestion de la durée de vie</span><span class="sxs-lookup"><span data-stu-id="c61e8-485">HttpClient and lifetime management</span></span>

<span data-ttu-id="c61e8-486">Une nouvelle instance `HttpClient` est retournée à chaque fois que `CreateClient` est appelé sur `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-486">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="c61e8-487">Il existe un <xref:System.Net.Http.HttpMessageHandler> par client nommé.</span><span class="sxs-lookup"><span data-stu-id="c61e8-487">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="c61e8-488">La fabrique gère les durées de vie des instances `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-488">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="c61e8-489">`IHttpClientFactory` regroupe dans un pool les instances de `HttpMessageHandler` créées par la fabrique pour réduire la consommation des ressources.</span><span class="sxs-lookup"><span data-stu-id="c61e8-489">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="c61e8-490">Une instance de `HttpMessageHandler` peut être réutilisée à partir du pool quand vous créez une instance de `HttpClient` si sa durée de vie n’a pas expiré.</span><span class="sxs-lookup"><span data-stu-id="c61e8-490">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="c61e8-491">Le regroupement de gestionnaires en pools est souhaitable, car chaque gestionnaire gère habituellement ses propres connexions HTTP sous-jacentes.</span><span class="sxs-lookup"><span data-stu-id="c61e8-491">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="c61e8-492">La création de plus de gestionnaires que nécessaire peut entraîner des délais de connexion.</span><span class="sxs-lookup"><span data-stu-id="c61e8-492">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="c61e8-493">Certains gestionnaires conservent aussi les connexions indéfiniment ouvertes, ce qui peut empêcher le gestionnaire de réagir aux changements du DNS.</span><span class="sxs-lookup"><span data-stu-id="c61e8-493">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="c61e8-494">La durée de vie par défaut d’un gestionnaire est de deux minutes.</span><span class="sxs-lookup"><span data-stu-id="c61e8-494">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="c61e8-495">La valeur par défaut peut être remplacée pour chaque client nommé.</span><span class="sxs-lookup"><span data-stu-id="c61e8-495">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="c61e8-496">Pour la remplacer, appelez <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> sur le `IHttpClientBuilder` qui est retourné lors de la création du client :</span><span class="sxs-lookup"><span data-stu-id="c61e8-496">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="c61e8-497">La suppression du client n’est pas nécessaire.</span><span class="sxs-lookup"><span data-stu-id="c61e8-497">Disposal of the client isn't required.</span></span> <span data-ttu-id="c61e8-498">La suppression annule les requêtes sortantes et garantit que l’instance `HttpClient` donnée ne peut pas être utilisée après avoir appelé <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="c61e8-498">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="c61e8-499">`IHttpClientFactory` effectue le suivi et libère les ressources utilisées par les instances `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-499">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="c61e8-500">Les instances `HttpClient` peuvent généralement être traitées en tant qu’objets .NET ne nécessitant pas une suppression.</span><span class="sxs-lookup"><span data-stu-id="c61e8-500">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="c61e8-501">Le fait de conserver une seule instance `HttpClient` active pendant une longue durée est un modèle commun utilisé avant le lancement de `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-501">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="c61e8-502">Ce modèle devient inutile après la migration vers `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-502">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="c61e8-503">Alternatives à IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="c61e8-503">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="c61e8-504">L’utilisation `IHttpClientFactory` de dans une application di-Enabled évite les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="c61e8-504">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="c61e8-505">Problèmes d’épuisement des ressources par les instances de regroupement `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="c61e8-505">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="c61e8-506">Problèmes DNS périmés par les instances cycliques `HttpMessageHandler` à intervalles réguliers.</span><span class="sxs-lookup"><span data-stu-id="c61e8-506">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="c61e8-507">Il existe d’autres façons de résoudre les problèmes précédents à l’aide d’une instance de longue durée <xref:System.Net.Http.SocketsHttpHandler> .</span><span class="sxs-lookup"><span data-stu-id="c61e8-507">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="c61e8-508">Créez une instance de `SocketsHttpHandler` lorsque l’application démarre et utilisez-la pendant toute la durée de vie de l’application.</span><span class="sxs-lookup"><span data-stu-id="c61e8-508">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="c61e8-509">Configurez <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> avec une valeur appropriée en fonction des temps d’actualisation DNS.</span><span class="sxs-lookup"><span data-stu-id="c61e8-509">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="c61e8-510">Créez des `HttpClient` instances en utilisant `new HttpClient(handler, disposeHandler: false)` si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="c61e8-510">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="c61e8-511">Les approches précédentes résolvent les problèmes de gestion des ressources qui sont `IHttpClientFactory` résolus de la même façon.</span><span class="sxs-lookup"><span data-stu-id="c61e8-511">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="c61e8-512">Le `SocketsHttpHandler` partage les connexions entre les `HttpClient` instances.</span><span class="sxs-lookup"><span data-stu-id="c61e8-512">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="c61e8-513">Ce partage empêche l’épuisement des sockets.</span><span class="sxs-lookup"><span data-stu-id="c61e8-513">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="c61e8-514">Le `SocketsHttpHandler` cycle des connexions en fonction de `PooledConnectionLifetime` pour éviter les problèmes DNS périmés.</span><span class="sxs-lookup"><span data-stu-id="c61e8-514">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="no-loccookies"></a><span data-ttu-id="c61e8-515">Cookies</span><span class="sxs-lookup"><span data-stu-id="c61e8-515">Cookies</span></span>

<span data-ttu-id="c61e8-516">Les instances regroupées `HttpMessageHandler` entraînent le `CookieContainer` partage des objets.</span><span class="sxs-lookup"><span data-stu-id="c61e8-516">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="c61e8-517">Le `CookieContainer` partage d’objets imprévus aboutit souvent à un code incorrect.</span><span class="sxs-lookup"><span data-stu-id="c61e8-517">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="c61e8-518">Pour les applications qui nécessitent des cookie , envisagez l’une des deux opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="c61e8-518">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="c61e8-519">Désactivation de la cookie gestion automatique</span><span class="sxs-lookup"><span data-stu-id="c61e8-519">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="c61e8-520">Éviter `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="c61e8-520">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="c61e8-521">Appelez <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> pour désactiver la cookie gestion automatique :</span><span class="sxs-lookup"><span data-stu-id="c61e8-521">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="c61e8-522">Journalisation</span><span class="sxs-lookup"><span data-stu-id="c61e8-522">Logging</span></span>

<span data-ttu-id="c61e8-523">Les clients créés via `IHttpClientFactory` enregistrent les messages de journalisation pour toutes les requêtes.</span><span class="sxs-lookup"><span data-stu-id="c61e8-523">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="c61e8-524">Activez le niveau d’informations approprié dans votre configuration de journalisation pour voir les messages de journalisation par défaut.</span><span class="sxs-lookup"><span data-stu-id="c61e8-524">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="c61e8-525">Une journalisation supplémentaire, comme celle des en-têtes des requêtes, est incluse seulement au niveau de trace.</span><span class="sxs-lookup"><span data-stu-id="c61e8-525">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="c61e8-526">La catégorie de journal utilisée pour chaque client comprend le nom du client.</span><span class="sxs-lookup"><span data-stu-id="c61e8-526">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="c61e8-527">Par exemple, un client nommé *MyNamedClient* journalise les messages avec la catégorie `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-527">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="c61e8-528">Les messages avec le suffixe *LogicalHandler* se produisent en dehors du pipeline du gestionnaire de requêtes.</span><span class="sxs-lookup"><span data-stu-id="c61e8-528">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="c61e8-529">Lors d’une requête, les messages sont journalisés avant que d’autres gestionnaires du pipeline l’aient traitée.</span><span class="sxs-lookup"><span data-stu-id="c61e8-529">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="c61e8-530">Lors d’une réponse, les messages sont journalisés une fois que tous les autres gestionnaires du pipeline ont reçu la réponse.</span><span class="sxs-lookup"><span data-stu-id="c61e8-530">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="c61e8-531">La journalisation se produit également à l’intérieur du pipeline du gestionnaire de requêtes.</span><span class="sxs-lookup"><span data-stu-id="c61e8-531">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="c61e8-532">Dans l’exemple *MyNamedClient*, ces messages sont journalisés avec la catégorie de journalisation `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-532">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="c61e8-533">Pour la requête, cela se produit après que tous les autres gestionnaires ont été exécutés et immédiatement avant l’envoi de la requête sur le réseau.</span><span class="sxs-lookup"><span data-stu-id="c61e8-533">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="c61e8-534">Lors de la réponse, cette journalisation inclut l’état de la réponse avant qu’elle repasse à travers le pipeline de gestionnaires.</span><span class="sxs-lookup"><span data-stu-id="c61e8-534">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="c61e8-535">L’activation de la journalisation à l’extérieur et à l’intérieur du pipeline permet l’inspection des changements apportés par les autres gestionnaires du pipeline.</span><span class="sxs-lookup"><span data-stu-id="c61e8-535">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="c61e8-536">Par exemple, cela peut comprendre des changements apportés aux en-têtes des requêtes ou au code d’état de la réponse.</span><span class="sxs-lookup"><span data-stu-id="c61e8-536">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="c61e8-537">L’ajout du nom du client dans la catégorie de journalisation permet si nécessaire de filtrer le journal pour des clients nommés spécifiques.</span><span class="sxs-lookup"><span data-stu-id="c61e8-537">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="c61e8-538">Configurer le HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="c61e8-538">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="c61e8-539">Il peut être nécessaire de contrôler la configuration du `HttpMessageHandler` interne utilisé par un client.</span><span class="sxs-lookup"><span data-stu-id="c61e8-539">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="c61e8-540">Un `IHttpClientBuilder` est retourné quand vous ajoutez des clients nommés ou typés.</span><span class="sxs-lookup"><span data-stu-id="c61e8-540">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="c61e8-541">La méthode d’extension <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> peut être utilisée pour définir un délégué.</span><span class="sxs-lookup"><span data-stu-id="c61e8-541">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="c61e8-542">Le délégué est utilisé pour créer et configurer le `HttpMessageHandler` principal utilisé par ce client :</span><span class="sxs-lookup"><span data-stu-id="c61e8-542">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="c61e8-543">Utiliser IHttpClientFactory dans une application console</span><span class="sxs-lookup"><span data-stu-id="c61e8-543">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="c61e8-544">Dans une application console, ajoutez les références de package suivantes au projet :</span><span class="sxs-lookup"><span data-stu-id="c61e8-544">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="c61e8-545">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="c61e8-545">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="c61e8-546">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="c61e8-546">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="c61e8-547">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="c61e8-547">In the following example:</span></span>

* <span data-ttu-id="c61e8-548"><xref:System.Net.Http.IHttpClientFactory> est inscrit dans le conteneur de service dans de l’[hôte générique](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="c61e8-548"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="c61e8-549">`MyService` crée une instance de fabrique cliente à partir du service, qui est utilisée pour créer un `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-549">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="c61e8-550">`HttpClient` est utilisé pour récupérer une page web.</span><span class="sxs-lookup"><span data-stu-id="c61e8-550">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="c61e8-551">`Main` crée une étendue pour exécuter la méthode `GetPage` du service et écrire les 500 premiers caractères du contenu de la page web dans la console.</span><span class="sxs-lookup"><span data-stu-id="c61e8-551">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="c61e8-552">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="c61e8-552">Additional resources</span></span>

* [<span data-ttu-id="c61e8-553">Utilisez HttpClientFactory pour implémenter des requêtes HTTP résilientes</span><span class="sxs-lookup"><span data-stu-id="c61e8-553">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="c61e8-554">Implémenter de nouvelles tentatives d’appel HTTP avec interruption exponentielle avec des stratégies Polly et HttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="c61e8-554">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="c61e8-555">Implémenter le modèle Disjoncteur</span><span class="sxs-lookup"><span data-stu-id="c61e8-555">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="c61e8-556">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak) et [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="c61e8-556">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="c61e8-557">Une <xref:System.Net.Http.IHttpClientFactory> peut être inscrite et utilisée pour configurer et créer des instances de <xref:System.Net.Http.HttpClient> dans une application.</span><span class="sxs-lookup"><span data-stu-id="c61e8-557">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="c61e8-558">Elle offre les avantages suivants :</span><span class="sxs-lookup"><span data-stu-id="c61e8-558">It offers the following benefits:</span></span>

* <span data-ttu-id="c61e8-559">Fournit un emplacement central pour le nommage et la configuration d’instance de `HttpClient` logiques.</span><span class="sxs-lookup"><span data-stu-id="c61e8-559">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="c61e8-560">Par exemple, un client *github* peut être inscrit et configuré pour accéder à [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="c61e8-560">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="c61e8-561">Un client par défaut peut être inscrit à d’autres fins.</span><span class="sxs-lookup"><span data-stu-id="c61e8-561">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="c61e8-562">Codifie le concept de middleware (intergiciel) sortant via la délégation de gestionnaires dans `HttpClient` et fournit des extensions permettant au middleware Polly d’en tirer parti.</span><span class="sxs-lookup"><span data-stu-id="c61e8-562">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="c61e8-563">Gère le regroupement et la durée de vie des instances de `HttpClientMessageHandler` sous-jacentes pour éviter les problèmes DNS courants qui se produisent lors de la gestion manuelle des durées de vie de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-563">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="c61e8-564">Ajoute une expérience de journalisation configurable (via `ILogger`) pour toutes les requêtes envoyées via des clients créés par la fabrique.</span><span class="sxs-lookup"><span data-stu-id="c61e8-564">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="c61e8-565">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c61e8-565">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c61e8-566">Prérequis</span><span class="sxs-lookup"><span data-stu-id="c61e8-566">Prerequisites</span></span>

<span data-ttu-id="c61e8-567">Les projets ciblant .NET Framework nécessitent l’installation du package NuGet [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/).</span><span class="sxs-lookup"><span data-stu-id="c61e8-567">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="c61e8-568">Les projets qui ciblent .NET Core et référencent le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) incluent déjà le package `Microsoft.Extensions.Http`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-568">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="c61e8-569">Modèles de consommation</span><span class="sxs-lookup"><span data-stu-id="c61e8-569">Consumption patterns</span></span>

<span data-ttu-id="c61e8-570">Vous pouvez utiliser `IHttpClientFactory` dans une application de plusieurs façons :</span><span class="sxs-lookup"><span data-stu-id="c61e8-570">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="c61e8-571">Utilisation de base</span><span class="sxs-lookup"><span data-stu-id="c61e8-571">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="c61e8-572">Clients nommés</span><span class="sxs-lookup"><span data-stu-id="c61e8-572">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="c61e8-573">Clients typés</span><span class="sxs-lookup"><span data-stu-id="c61e8-573">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="c61e8-574">Clients générés</span><span class="sxs-lookup"><span data-stu-id="c61e8-574">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="c61e8-575">Aucune d’entre elles n’est meilleure qu’une autre.</span><span class="sxs-lookup"><span data-stu-id="c61e8-575">None of them are strictly superior to another.</span></span> <span data-ttu-id="c61e8-576">La meilleure approche dépend des contraintes de l’application.</span><span class="sxs-lookup"><span data-stu-id="c61e8-576">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="c61e8-577">Utilisation de base</span><span class="sxs-lookup"><span data-stu-id="c61e8-577">Basic usage</span></span>

<span data-ttu-id="c61e8-578">Vous pouvez inscrire la `IHttpClientFactory` en appelant la méthode d’extension `AddHttpClient` sur la `IServiceCollection`, à l’intérieur la méthode `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-578">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="c61e8-579">Une fois inscrit, le code peut accepter un `IHttpClientFactory` partout où des services peuvent être injectés avec [une injection de dépendance](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="c61e8-579">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="c61e8-580">Le `IHttpClientFactory` peut être utilisé pour créer une `HttpClient` instance de :</span><span class="sxs-lookup"><span data-stu-id="c61e8-580">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="c61e8-581">L’utilisation de `IHttpClientFactory` de cette façon est un excellent moyen de refactoriser une application existante.</span><span class="sxs-lookup"><span data-stu-id="c61e8-581">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="c61e8-582">Elle n’a aucun impact sur la façon dont `HttpClient` est utilisé.</span><span class="sxs-lookup"><span data-stu-id="c61e8-582">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="c61e8-583">Dans les endroits où les instances de `HttpClient` sont actuellement créées, remplacez ces occurrences par un appel à <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="c61e8-583">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="c61e8-584">Clients nommés</span><span class="sxs-lookup"><span data-stu-id="c61e8-584">Named clients</span></span>

<span data-ttu-id="c61e8-585">Si une application nécessite plusieurs utilisations distinctes de `HttpClient`, chacune avec une configuration différente, une option consiste à utiliser des **clients nommés**.</span><span class="sxs-lookup"><span data-stu-id="c61e8-585">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="c61e8-586">La configuration d’un `HttpClient` nommé peut être spécifiée lors de l’inscription dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-586">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="c61e8-587">Dans le code précédent, `AddHttpClient` est appelé en fournissant le nom *github*.</span><span class="sxs-lookup"><span data-stu-id="c61e8-587">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="c61e8-588">Une configuration par défaut est appliquée à ce client : l’adresse de base et deux en-têtes nécessaires pour utiliser l’API GitHub.</span><span class="sxs-lookup"><span data-stu-id="c61e8-588">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="c61e8-589">Chaque fois que `CreateClient` est appelée, une nouvelle instance de `HttpClient` est créée et l’action de configuration est appelée.</span><span class="sxs-lookup"><span data-stu-id="c61e8-589">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="c61e8-590">Pour utiliser un client nommé, un paramètre de chaîne peut être passé à `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-590">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="c61e8-591">Spécifiez le nom du client à créer :</span><span class="sxs-lookup"><span data-stu-id="c61e8-591">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="c61e8-592">Dans le code précédent, la requête n’a pas besoin de spécifier un nom d’hôte.</span><span class="sxs-lookup"><span data-stu-id="c61e8-592">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="c61e8-593">Elle peut simplement passer le chemin, car l’adresse de base configurée pour le client est utilisée.</span><span class="sxs-lookup"><span data-stu-id="c61e8-593">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="c61e8-594">Clients typés</span><span class="sxs-lookup"><span data-stu-id="c61e8-594">Typed clients</span></span>

<span data-ttu-id="c61e8-595">Clients typés :</span><span class="sxs-lookup"><span data-stu-id="c61e8-595">Typed clients:</span></span>

* <span data-ttu-id="c61e8-596">Fournissent les mêmes fonctionnalités que les clients nommés, sans qu’il soit nécessaire d’utiliser des chaînes comme clés.</span><span class="sxs-lookup"><span data-stu-id="c61e8-596">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="c61e8-597">Bénéficie de l’aide d’IntelliSense et du compilateur lors de l’utilisation des clients.</span><span class="sxs-lookup"><span data-stu-id="c61e8-597">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="c61e8-598">Fournissent un emplacement unique pour configurer et interagir avec un `HttpClient` particulier.</span><span class="sxs-lookup"><span data-stu-id="c61e8-598">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="c61e8-599">Par exemple, un même client typé peut être utilisé pour un point de terminaison de backend et pour encapsuler la logique relative à ce point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="c61e8-599">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="c61e8-600">Fonctionnent avec l’injection de dépendances et peuvent être injectés là où c’est nécessaire dans votre application.</span><span class="sxs-lookup"><span data-stu-id="c61e8-600">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="c61e8-601">Un client typé accepte un `HttpClient` paramètre dans son constructeur :</span><span class="sxs-lookup"><span data-stu-id="c61e8-601">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="c61e8-602">Dans le code précédent, la configuration est déplacée dans le client typé.</span><span class="sxs-lookup"><span data-stu-id="c61e8-602">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="c61e8-603">L’objet `HttpClient` est exposé en tant que propriété publique.</span><span class="sxs-lookup"><span data-stu-id="c61e8-603">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="c61e8-604">Il est possible de définir des méthodes d’API spécifiques qui exposent les fonctionnalités de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-604">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="c61e8-605">La méthode `GetAspNetDocsIssues` encapsule le code nécessaire pour interroger et analyser les problèmes ouverts les plus récents d’un dépôt GitHub.</span><span class="sxs-lookup"><span data-stu-id="c61e8-605">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="c61e8-606">Pour inscrire un client typé, la méthode d’extension <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> générique peut être utilisée dans `Startup.ConfigureServices`, en spécifiant la classe du client typé :</span><span class="sxs-lookup"><span data-stu-id="c61e8-606">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="c61e8-607">Le client typé est inscrit comme étant transitoire avec injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="c61e8-607">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="c61e8-608">Le client typé peut être injecté et utilisé directement :</span><span class="sxs-lookup"><span data-stu-id="c61e8-608">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="c61e8-609">Si vous préférez, vous pouvez spécifier la configuration d’un client typé lors de l’inscription dans `Startup.ConfigureServices` au lieu de le faire dans le constructeur du client typé :</span><span class="sxs-lookup"><span data-stu-id="c61e8-609">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="c61e8-610">Il est possible d’encapsuler entièrement le `HttpClient` dans un client typé.</span><span class="sxs-lookup"><span data-stu-id="c61e8-610">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="c61e8-611">Au lieu de l’exposer en tant que propriété, vous pouvez fournir des méthodes publiques qui appellent l’instance de `HttpClient` en interne.</span><span class="sxs-lookup"><span data-stu-id="c61e8-611">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="c61e8-612">Dans le code précédent, le `HttpClient` est stocké en tant que champ privé.</span><span class="sxs-lookup"><span data-stu-id="c61e8-612">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="c61e8-613">Tous les accès nécessaires pour effectuer des appels externes passent par la méthode `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-613">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="c61e8-614">Clients générés</span><span class="sxs-lookup"><span data-stu-id="c61e8-614">Generated clients</span></span>

<span data-ttu-id="c61e8-615">`IHttpClientFactory` peut être utilisé en combinaison avec d’autres bibliothèques tierces, comme [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="c61e8-615">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="c61e8-616">Refit est une bibliothèque REST pour .NET.</span><span class="sxs-lookup"><span data-stu-id="c61e8-616">Refit is a REST library for .NET.</span></span> <span data-ttu-id="c61e8-617">Il convertit les API REST en interfaces dynamiques.</span><span class="sxs-lookup"><span data-stu-id="c61e8-617">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="c61e8-618">Une implémentation de l’interface est générée dynamiquement par le `RestService`, avec `HttpClient` pour faire les appels HTTP externes.</span><span class="sxs-lookup"><span data-stu-id="c61e8-618">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="c61e8-619">Une interface et une réponse sont définies pour représenter l’API externe et sa réponse :</span><span class="sxs-lookup"><span data-stu-id="c61e8-619">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="c61e8-620">Vous pouvez ajouter un client typé en utilisant Refit pour générer l’implémentation :</span><span class="sxs-lookup"><span data-stu-id="c61e8-620">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

<span data-ttu-id="c61e8-621">L’interface définie peut être utilisée quand c’est nécessaire, avec l’implémentation fournie par l’injection de dépendances et Refit :</span><span class="sxs-lookup"><span data-stu-id="c61e8-621">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="c61e8-622">Middleware pour les requêtes sortantes</span><span class="sxs-lookup"><span data-stu-id="c61e8-622">Outgoing request middleware</span></span>

<span data-ttu-id="c61e8-623">`HttpClient` intègre déjà le concept de délégation des gestionnaires qui peuvent être liés ensemble pour les requêtes HTTP sortantes.</span><span class="sxs-lookup"><span data-stu-id="c61e8-623">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="c61e8-624">Le `IHttpClientFactory` permet de définir facilement les gestionnaires à appliquer pour chaque client nommé.</span><span class="sxs-lookup"><span data-stu-id="c61e8-624">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="c61e8-625">Il prend en charge l’inscription et le chaînage de plusieurs gestionnaires pour créer un pipeline de middlewares pour les requêtes sortantes.</span><span class="sxs-lookup"><span data-stu-id="c61e8-625">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="c61e8-626">Chacun de ces gestionnaires peut effectuer un travail avant et après la requête sortante.</span><span class="sxs-lookup"><span data-stu-id="c61e8-626">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="c61e8-627">Ce modèle est similaire au pipeline de middlewares entrants dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c61e8-627">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="c61e8-628">Le modèle fournit un mécanisme permettant de gérer les problèmes transversaux liés aux des requêtes HTTP, notamment la mise en cache, la gestion des erreurs, la sérialisation et la journalisation.</span><span class="sxs-lookup"><span data-stu-id="c61e8-628">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="c61e8-629">Pour créer un gestionnaire, définissez une classe dérivant de <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="c61e8-629">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="c61e8-630">Remplacez la méthode `SendAsync` de façon à exécuter du code avant de passer la requête au gestionnaire suivant dans le pipeline :</span><span class="sxs-lookup"><span data-stu-id="c61e8-630">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="c61e8-631">Le code précédent définit un gestionnaire de base.</span><span class="sxs-lookup"><span data-stu-id="c61e8-631">The preceding code defines a basic handler.</span></span> <span data-ttu-id="c61e8-632">Il vérifie si un en-tête `X-API-KEY` a été inclus dans la requête.</span><span class="sxs-lookup"><span data-stu-id="c61e8-632">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="c61e8-633">Si l’en-tête est manquant, il peut éviter l’appel HTTP et retourner une réponse appropriée.</span><span class="sxs-lookup"><span data-stu-id="c61e8-633">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="c61e8-634">Au cours de l’inscription, un ou plusieurs gestionnaires peuvent être ajoutés à la configuration d’un `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="c61e8-634">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="c61e8-635">Cette tâche est accomplie via des méthodes d’extension sur le <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span><span class="sxs-lookup"><span data-stu-id="c61e8-635">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="c61e8-636">Dans le code précédent, le `ValidateHeaderHandler` est inscrit avec une injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="c61e8-636">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="c61e8-637">Le gestionnaire **doit** être inscrit dans l’injection de dépendances en tant que service temporaire, sans étendue.</span><span class="sxs-lookup"><span data-stu-id="c61e8-637">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="c61e8-638">Si le gestionnaire est inscrit en tant que service étendu et que des services dont dépend le gestionnaire peuvent être supprimés :</span><span class="sxs-lookup"><span data-stu-id="c61e8-638">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="c61e8-639">Les services du gestionnaire peuvent être supprimés avant que le gestionnaire ne soit hors de portée.</span><span class="sxs-lookup"><span data-stu-id="c61e8-639">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="c61e8-640">Les services du gestionnaire supprimés entraînent un échec du gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="c61e8-640">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="c61e8-641">Une fois inscrit, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> peut être appelé en passant en entrée le type du gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="c61e8-641">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="c61e8-642">Vous pouvez inscrire plusieurs gestionnaires dans l’ordre où ils doivent être exécutés.</span><span class="sxs-lookup"><span data-stu-id="c61e8-642">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="c61e8-643">Chaque gestionnaire wrappe le gestionnaire suivant jusqu’à ce que le dernier `HttpClientHandler` exécute la requête :</span><span class="sxs-lookup"><span data-stu-id="c61e8-643">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="c61e8-644">Utilisez l’une des approches suivantes pour partager l’état de chaque requête avec les gestionnaires de messages :</span><span class="sxs-lookup"><span data-stu-id="c61e8-644">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="c61e8-645">Passez des données dans le gestionnaire en utilisant `HttpRequestMessage.Properties`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-645">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="c61e8-646">Utilisez `IHttpContextAccessor` pour accéder à la requête en cours.</span><span class="sxs-lookup"><span data-stu-id="c61e8-646">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="c61e8-647">Créez un objet de stockage `AsyncLocal` personnalisé pour passer les données.</span><span class="sxs-lookup"><span data-stu-id="c61e8-647">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="c61e8-648">Utiliser les gestionnaires Polly</span><span class="sxs-lookup"><span data-stu-id="c61e8-648">Use Polly-based handlers</span></span>

<span data-ttu-id="c61e8-649">`IHttpClientFactory` s’intègre à une bibliothèque tierce très utilisée, appelée [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="c61e8-649">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="c61e8-650">Polly est une bibliothèque complète de gestion des erreurs transitoires et de résilience pour .NET.</span><span class="sxs-lookup"><span data-stu-id="c61e8-650">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="c61e8-651">Elle permet aux développeurs de formuler facilement et de façon thread-safe des stratégies, comme Retry (Nouvelle tentative), Circuit Breaker (Disjoncteur), Timeout (Délai d’attente), Bulkhead Isolation (Isolation par cloisonnement) et Fallback (Alternative de repli).</span><span class="sxs-lookup"><span data-stu-id="c61e8-651">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="c61e8-652">Des méthodes d’extension sont fournies pour permettre l’utilisation de stratégies Polly avec les instances configurées de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-652">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="c61e8-653">Les extensions Polly :</span><span class="sxs-lookup"><span data-stu-id="c61e8-653">The Polly extensions:</span></span>

* <span data-ttu-id="c61e8-654">Prennent en charge l’ajout de gestionnaires Polly à des clients.</span><span class="sxs-lookup"><span data-stu-id="c61e8-654">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="c61e8-655">Peuvent être utilisées après l’installation du package NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/).</span><span class="sxs-lookup"><span data-stu-id="c61e8-655">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="c61e8-656">Ce package n’est pas inclus dans le framework partagé ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c61e8-656">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="c61e8-657">Gérer les erreurs temporaires</span><span class="sxs-lookup"><span data-stu-id="c61e8-657">Handle transient faults</span></span>

<span data-ttu-id="c61e8-658">Les erreurs courantes se produisent lorsque des appels HTTP externes sont temporaires.</span><span class="sxs-lookup"><span data-stu-id="c61e8-658">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="c61e8-659">Une méthode d’extension pratique nommée `AddTransientHttpErrorPolicy` est incluse : elle permet de définir une stratégie pour gérer les erreurs temporaires.</span><span class="sxs-lookup"><span data-stu-id="c61e8-659">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="c61e8-660">Les stratégies configurées avec cette méthode d’extension gèrent `HttpRequestException`, les réponses HTTP 5xx et les réponses HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="c61e8-660">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="c61e8-661">L’extension `AddTransientHttpErrorPolicy` peut être utilisée dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-661">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c61e8-662">L’extension fournit l’accès à un objet `PolicyBuilder` configuré pour gérer les erreurs représentant une erreur temporaire possible :</span><span class="sxs-lookup"><span data-stu-id="c61e8-662">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="c61e8-663">Dans le code précédent, une stratégie `WaitAndRetryAsync` est définie.</span><span class="sxs-lookup"><span data-stu-id="c61e8-663">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="c61e8-664">Les requêtes qui ont échoué sont retentées jusqu’à trois fois avec un délai de 600 ms entre les tentatives.</span><span class="sxs-lookup"><span data-stu-id="c61e8-664">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="c61e8-665">Sélectionner dynamiquement des stratégies</span><span class="sxs-lookup"><span data-stu-id="c61e8-665">Dynamically select policies</span></span>

<span data-ttu-id="c61e8-666">Il existe d’autres méthodes d’extension que vous pouvez utiliser pour ajouter des gestionnaires Polly.</span><span class="sxs-lookup"><span data-stu-id="c61e8-666">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="c61e8-667">Une de ces extensions est `AddPolicyHandler`, qui a plusieurs surcharges.</span><span class="sxs-lookup"><span data-stu-id="c61e8-667">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="c61e8-668">Une de ces surcharges permet l’inspection de la requête lors de la définition de la stratégie à appliquer :</span><span class="sxs-lookup"><span data-stu-id="c61e8-668">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="c61e8-669">Dans le code précédent, si la requête sortante est un HTTP GET, un délai d’attente de 10 secondes est appliqué.</span><span class="sxs-lookup"><span data-stu-id="c61e8-669">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="c61e8-670">Pour toutes les autres méthodes HTTP, un délai d’attente de 30 secondes est utilisé.</span><span class="sxs-lookup"><span data-stu-id="c61e8-670">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="c61e8-671">Ajouter plusieurs gestionnaires Polly</span><span class="sxs-lookup"><span data-stu-id="c61e8-671">Add multiple Polly handlers</span></span>

<span data-ttu-id="c61e8-672">Il est courant d’imbriquer des stratégies Polly pour fournir des fonctionnalités améliorées :</span><span class="sxs-lookup"><span data-stu-id="c61e8-672">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="c61e8-673">Dans l’exemple précédent, deux gestionnaires sont ajoutés.</span><span class="sxs-lookup"><span data-stu-id="c61e8-673">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="c61e8-674">Le premier utilise l’extension `AddTransientHttpErrorPolicy` pour ajouter une stratégie de nouvelle tentative.</span><span class="sxs-lookup"><span data-stu-id="c61e8-674">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="c61e8-675">Les requêtes qui ont échoué sont retentées jusqu’à trois fois.</span><span class="sxs-lookup"><span data-stu-id="c61e8-675">Failed requests are retried up to three times.</span></span> <span data-ttu-id="c61e8-676">Le deuxième appel à `AddTransientHttpErrorPolicy` ajoute une stratégie de disjoncteur.</span><span class="sxs-lookup"><span data-stu-id="c61e8-676">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="c61e8-677">Les requêtes externes supplémentaires sont bloquées pendant 30 secondes si cinq tentatives successives échouent.</span><span class="sxs-lookup"><span data-stu-id="c61e8-677">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="c61e8-678">Les stratégies de disjoncteur sont avec état.</span><span class="sxs-lookup"><span data-stu-id="c61e8-678">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="c61e8-679">Tous les appels effectués via ce client partagent le même état du circuit.</span><span class="sxs-lookup"><span data-stu-id="c61e8-679">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="c61e8-680">Ajouter des stratégies à partir du Registre Polly</span><span class="sxs-lookup"><span data-stu-id="c61e8-680">Add policies from the Polly registry</span></span>

<span data-ttu-id="c61e8-681">Une approche de la gestion des stratégies régulièrement utilisées consiste à les définir une seule fois et à les inscrire avec un `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-681">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="c61e8-682">Il existe une méthode d’extension qui permet l’ajout d’un gestionnaire avec une stratégie du Registre :</span><span class="sxs-lookup"><span data-stu-id="c61e8-682">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="c61e8-683">Dans le code précédent, deux stratégies sont inscrites lorsque `PolicyRegistry` est ajouté à `ServiceCollection`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-683">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="c61e8-684">Pour utiliser une stratégie du Registre, la méthode `AddPolicyHandlerFromRegistry` est utilisée, en passant le nom de la stratégie à appliquer.</span><span class="sxs-lookup"><span data-stu-id="c61e8-684">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="c61e8-685">Vous trouverez plus d’informations sur les intégrations de `IHttpClientFactory` et de Polly dans le [wiki Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="c61e8-685">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="c61e8-686">HttpClient et gestion de la durée de vie</span><span class="sxs-lookup"><span data-stu-id="c61e8-686">HttpClient and lifetime management</span></span>

<span data-ttu-id="c61e8-687">Une nouvelle instance `HttpClient` est retournée à chaque fois que `CreateClient` est appelé sur `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-687">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="c61e8-688">Il existe un <xref:System.Net.Http.HttpMessageHandler> par client nommé.</span><span class="sxs-lookup"><span data-stu-id="c61e8-688">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="c61e8-689">La fabrique gère les durées de vie des instances `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-689">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="c61e8-690">`IHttpClientFactory` regroupe dans un pool les instances de `HttpMessageHandler` créées par la fabrique pour réduire la consommation des ressources.</span><span class="sxs-lookup"><span data-stu-id="c61e8-690">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="c61e8-691">Une instance de `HttpMessageHandler` peut être réutilisée à partir du pool quand vous créez une instance de `HttpClient` si sa durée de vie n’a pas expiré.</span><span class="sxs-lookup"><span data-stu-id="c61e8-691">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="c61e8-692">Le regroupement de gestionnaires en pools est souhaitable, car chaque gestionnaire gère habituellement ses propres connexions HTTP sous-jacentes.</span><span class="sxs-lookup"><span data-stu-id="c61e8-692">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="c61e8-693">La création de plus de gestionnaires que nécessaire peut entraîner des délais de connexion.</span><span class="sxs-lookup"><span data-stu-id="c61e8-693">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="c61e8-694">Certains gestionnaires conservent aussi les connexions indéfiniment ouvertes, ce qui peut empêcher le gestionnaire de réagir aux changements du DNS.</span><span class="sxs-lookup"><span data-stu-id="c61e8-694">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="c61e8-695">La durée de vie par défaut d’un gestionnaire est de deux minutes.</span><span class="sxs-lookup"><span data-stu-id="c61e8-695">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="c61e8-696">La valeur par défaut peut être remplacée pour chaque client nommé.</span><span class="sxs-lookup"><span data-stu-id="c61e8-696">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="c61e8-697">Pour la remplacer, appelez <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> sur le `IHttpClientBuilder` qui est retourné lors de la création du client :</span><span class="sxs-lookup"><span data-stu-id="c61e8-697">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="c61e8-698">La suppression du client n’est pas nécessaire.</span><span class="sxs-lookup"><span data-stu-id="c61e8-698">Disposal of the client isn't required.</span></span> <span data-ttu-id="c61e8-699">La suppression annule les requêtes sortantes et garantit que l’instance `HttpClient` donnée ne peut pas être utilisée après avoir appelé <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="c61e8-699">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="c61e8-700">`IHttpClientFactory` effectue le suivi et libère les ressources utilisées par les instances `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-700">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="c61e8-701">Les instances `HttpClient` peuvent généralement être traitées en tant qu’objets .NET ne nécessitant pas une suppression.</span><span class="sxs-lookup"><span data-stu-id="c61e8-701">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="c61e8-702">Le fait de conserver une seule instance `HttpClient` active pendant une longue durée est un modèle commun utilisé avant le lancement de `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-702">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="c61e8-703">Ce modèle devient inutile après la migration vers `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-703">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="c61e8-704">Alternatives à IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="c61e8-704">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="c61e8-705">L’utilisation `IHttpClientFactory` de dans une application di-Enabled évite les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="c61e8-705">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="c61e8-706">Problèmes d’épuisement des ressources par les instances de regroupement `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="c61e8-706">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="c61e8-707">Problèmes DNS périmés par les instances cycliques `HttpMessageHandler` à intervalles réguliers.</span><span class="sxs-lookup"><span data-stu-id="c61e8-707">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="c61e8-708">Il existe d’autres façons de résoudre les problèmes précédents à l’aide d’une instance de longue durée <xref:System.Net.Http.SocketsHttpHandler> .</span><span class="sxs-lookup"><span data-stu-id="c61e8-708">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="c61e8-709">Créez une instance de `SocketsHttpHandler` lorsque l’application démarre et utilisez-la pendant toute la durée de vie de l’application.</span><span class="sxs-lookup"><span data-stu-id="c61e8-709">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="c61e8-710">Configurez <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> avec une valeur appropriée en fonction des temps d’actualisation DNS.</span><span class="sxs-lookup"><span data-stu-id="c61e8-710">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="c61e8-711">Créez des `HttpClient` instances en utilisant `new HttpClient(handler, disposeHandler: false)` si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="c61e8-711">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="c61e8-712">Les approches précédentes résolvent les problèmes de gestion des ressources qui sont `IHttpClientFactory` résolus de la même façon.</span><span class="sxs-lookup"><span data-stu-id="c61e8-712">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="c61e8-713">Le `SocketsHttpHandler` partage les connexions entre les `HttpClient` instances.</span><span class="sxs-lookup"><span data-stu-id="c61e8-713">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="c61e8-714">Ce partage empêche l’épuisement des sockets.</span><span class="sxs-lookup"><span data-stu-id="c61e8-714">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="c61e8-715">Le `SocketsHttpHandler` cycle des connexions en fonction de `PooledConnectionLifetime` pour éviter les problèmes DNS périmés.</span><span class="sxs-lookup"><span data-stu-id="c61e8-715">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="no-loccookies"></a><span data-ttu-id="c61e8-716">Cookies</span><span class="sxs-lookup"><span data-stu-id="c61e8-716">Cookies</span></span>

<span data-ttu-id="c61e8-717">Les instances regroupées `HttpMessageHandler` entraînent le `CookieContainer` partage des objets.</span><span class="sxs-lookup"><span data-stu-id="c61e8-717">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="c61e8-718">Le `CookieContainer` partage d’objets imprévus aboutit souvent à un code incorrect.</span><span class="sxs-lookup"><span data-stu-id="c61e8-718">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="c61e8-719">Pour les applications qui nécessitent des cookie , envisagez l’une des deux opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="c61e8-719">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="c61e8-720">Désactivation de la cookie gestion automatique</span><span class="sxs-lookup"><span data-stu-id="c61e8-720">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="c61e8-721">Éviter `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="c61e8-721">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="c61e8-722">Appelez <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> pour désactiver la cookie gestion automatique :</span><span class="sxs-lookup"><span data-stu-id="c61e8-722">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="c61e8-723">Journalisation</span><span class="sxs-lookup"><span data-stu-id="c61e8-723">Logging</span></span>

<span data-ttu-id="c61e8-724">Les clients créés via `IHttpClientFactory` enregistrent les messages de journalisation pour toutes les requêtes.</span><span class="sxs-lookup"><span data-stu-id="c61e8-724">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="c61e8-725">Activez le niveau d’informations approprié dans votre configuration de journalisation pour voir les messages de journalisation par défaut.</span><span class="sxs-lookup"><span data-stu-id="c61e8-725">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="c61e8-726">Une journalisation supplémentaire, comme celle des en-têtes des requêtes, est incluse seulement au niveau de trace.</span><span class="sxs-lookup"><span data-stu-id="c61e8-726">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="c61e8-727">La catégorie de journal utilisée pour chaque client comprend le nom du client.</span><span class="sxs-lookup"><span data-stu-id="c61e8-727">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="c61e8-728">Par exemple, un client nommé *MyNamedClient* journalise les messages avec la catégorie `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-728">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="c61e8-729">Les messages avec le suffixe *LogicalHandler* se produisent en dehors du pipeline du gestionnaire de requêtes.</span><span class="sxs-lookup"><span data-stu-id="c61e8-729">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="c61e8-730">Lors d’une requête, les messages sont journalisés avant que d’autres gestionnaires du pipeline l’aient traitée.</span><span class="sxs-lookup"><span data-stu-id="c61e8-730">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="c61e8-731">Lors d’une réponse, les messages sont journalisés une fois que tous les autres gestionnaires du pipeline ont reçu la réponse.</span><span class="sxs-lookup"><span data-stu-id="c61e8-731">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="c61e8-732">La journalisation se produit également à l’intérieur du pipeline du gestionnaire de requêtes.</span><span class="sxs-lookup"><span data-stu-id="c61e8-732">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="c61e8-733">Dans l’exemple *MyNamedClient*, ces messages sont journalisés avec la catégorie de journalisation `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-733">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="c61e8-734">Pour la requête, cela se produit après que tous les autres gestionnaires ont été exécutés et immédiatement avant l’envoi de la requête sur le réseau.</span><span class="sxs-lookup"><span data-stu-id="c61e8-734">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="c61e8-735">Lors de la réponse, cette journalisation inclut l’état de la réponse avant qu’elle repasse à travers le pipeline de gestionnaires.</span><span class="sxs-lookup"><span data-stu-id="c61e8-735">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="c61e8-736">L’activation de la journalisation à l’extérieur et à l’intérieur du pipeline permet l’inspection des changements apportés par les autres gestionnaires du pipeline.</span><span class="sxs-lookup"><span data-stu-id="c61e8-736">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="c61e8-737">Par exemple, cela peut comprendre des changements apportés aux en-têtes des requêtes ou au code d’état de la réponse.</span><span class="sxs-lookup"><span data-stu-id="c61e8-737">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="c61e8-738">L’ajout du nom du client dans la catégorie de journalisation permet si nécessaire de filtrer le journal pour des clients nommés spécifiques.</span><span class="sxs-lookup"><span data-stu-id="c61e8-738">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="c61e8-739">Configurer le HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="c61e8-739">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="c61e8-740">Il peut être nécessaire de contrôler la configuration du `HttpMessageHandler` interne utilisé par un client.</span><span class="sxs-lookup"><span data-stu-id="c61e8-740">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="c61e8-741">Un `IHttpClientBuilder` est retourné quand vous ajoutez des clients nommés ou typés.</span><span class="sxs-lookup"><span data-stu-id="c61e8-741">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="c61e8-742">La méthode d’extension <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> peut être utilisée pour définir un délégué.</span><span class="sxs-lookup"><span data-stu-id="c61e8-742">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="c61e8-743">Le délégué est utilisé pour créer et configurer le `HttpMessageHandler` principal utilisé par ce client :</span><span class="sxs-lookup"><span data-stu-id="c61e8-743">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="c61e8-744">Utiliser IHttpClientFactory dans une application console</span><span class="sxs-lookup"><span data-stu-id="c61e8-744">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="c61e8-745">Dans une application console, ajoutez les références de package suivantes au projet :</span><span class="sxs-lookup"><span data-stu-id="c61e8-745">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="c61e8-746">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="c61e8-746">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="c61e8-747">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="c61e8-747">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="c61e8-748">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="c61e8-748">In the following example:</span></span>

* <span data-ttu-id="c61e8-749"><xref:System.Net.Http.IHttpClientFactory> est inscrit dans le conteneur de service dans de l’[hôte générique](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="c61e8-749"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="c61e8-750">`MyService` crée une instance de fabrique cliente à partir du service, qui est utilisée pour créer un `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="c61e8-750">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="c61e8-751">`HttpClient` est utilisé pour récupérer une page web.</span><span class="sxs-lookup"><span data-stu-id="c61e8-751">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="c61e8-752">`Main` crée une étendue pour exécuter la méthode `GetPage` du service et écrire les 500 premiers caractères du contenu de la page web dans la console.</span><span class="sxs-lookup"><span data-stu-id="c61e8-752">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="c61e8-753">Intergiciel (middleware) de propagation d’en-tête</span><span class="sxs-lookup"><span data-stu-id="c61e8-753">Header propagation middleware</span></span>

<span data-ttu-id="c61e8-754">La propagation d’en-tête est un intergiciel (middleware) pris en charge par la communauté pour propager les en-têtes HTTP de la demande entrante aux demandes du client HTTP sortantes.</span><span class="sxs-lookup"><span data-stu-id="c61e8-754">Header propagation is a community supported middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="c61e8-755">Pour utiliser la propagation d’en-tête :</span><span class="sxs-lookup"><span data-stu-id="c61e8-755">To use header propagation:</span></span>

* <span data-ttu-id="c61e8-756">Référencez le port de la communauté pris en charge du package [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="c61e8-756">Reference the community supported port of the package [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span></span> <span data-ttu-id="c61e8-757">ASP.NET Core 3,1 et versions ultérieures prennent en charge [Microsoft. AspNetCore. HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="c61e8-757">ASP.NET Core 3.1 and later supports [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span></span>

* <span data-ttu-id="c61e8-758">Configurer l’intergiciel et `HttpClient` dans `Startup` :</span><span class="sxs-lookup"><span data-stu-id="c61e8-758">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* <span data-ttu-id="c61e8-759">Le client comprend les en-têtes configurés pour les demandes sortantes :</span><span class="sxs-lookup"><span data-stu-id="c61e8-759">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="c61e8-760">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="c61e8-760">Additional resources</span></span>

* [<span data-ttu-id="c61e8-761">Utilisez HttpClientFactory pour implémenter des requêtes HTTP résilientes</span><span class="sxs-lookup"><span data-stu-id="c61e8-761">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="c61e8-762">Implémenter de nouvelles tentatives d’appel HTTP avec interruption exponentielle avec des stratégies Polly et HttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="c61e8-762">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="c61e8-763">Implémenter le modèle Disjoncteur</span><span class="sxs-lookup"><span data-stu-id="c61e8-763">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
