---
title: Effectuer des requêtes HTTP en utilisant IHttpClientFactory dans ASP.NET Core
author: stevejgordon
description: Découvrez plus d’informations sur l’utilisation de l’interface IHttpClientFactory pour gérer des instances HttpClient logiques dans ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/09/2020
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
ms.openlocfilehash: 34c35daac3da845bac9156fe96078df7902a4cd0
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059492"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="7adf0-103">Effectuer des requêtes HTTP en utilisant IHttpClientFactory dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7adf0-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7adf0-104">Par [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT)et [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="7adf0-104">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="7adf0-105">Une <xref:System.Net.Http.IHttpClientFactory> peut être inscrite et utilisée pour configurer et créer des instances de <xref:System.Net.Http.HttpClient> dans une application.</span><span class="sxs-lookup"><span data-stu-id="7adf0-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="7adf0-106">`IHttpClientFactory` offre les avantages suivants :</span><span class="sxs-lookup"><span data-stu-id="7adf0-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="7adf0-107">Fournit un emplacement central pour le nommage et la configuration d’instance de `HttpClient` logiques.</span><span class="sxs-lookup"><span data-stu-id="7adf0-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="7adf0-108">Par exemple, un client nommé  *GitHub* peut être inscrit et configuré pour accéder à [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="7adf0-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="7adf0-109">Un client par défaut peut être inscrit pour un accès général.</span><span class="sxs-lookup"><span data-stu-id="7adf0-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="7adf0-110">Codifie le concept d’intergiciel (middleware) sortant via la délégation de gestionnaires dans `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="7adf0-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="7adf0-111">Fournit des extensions pour l’intergiciel (middleware) basé sur Polly pour tirer parti des gestionnaires de délégation dans `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="7adf0-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="7adf0-112">Gère le regroupement et la durée de vie des instances sous-jacentes `HttpClientMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="7adf0-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="7adf0-113">La gestion automatique évite les problèmes courants liés au DNS (Domain Name System) qui se produisent lors de la gestion manuelle des `HttpClient` durées de vie.</span><span class="sxs-lookup"><span data-stu-id="7adf0-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="7adf0-114">Ajoute une expérience de journalisation configurable (via `ILogger`) pour toutes les requêtes envoyées via des clients créés par la fabrique.</span><span class="sxs-lookup"><span data-stu-id="7adf0-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="7adf0-115">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="7adf0-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="7adf0-116">L’exemple de code de cette rubrique utilise <xref:System.Text.Json> pour désérialiser le contenu JSON renvoyé dans les réponses http.</span><span class="sxs-lookup"><span data-stu-id="7adf0-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="7adf0-117">Pour obtenir des exemples qui utilisent `Json.NET` et `ReadAsAsync<T>` , utilisez le sélecteur de version pour sélectionner une version 2. x de cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="7adf0-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="7adf0-118">Modèles de consommation</span><span class="sxs-lookup"><span data-stu-id="7adf0-118">Consumption patterns</span></span>

<span data-ttu-id="7adf0-119">Vous pouvez utiliser `IHttpClientFactory` dans une application de plusieurs façons :</span><span class="sxs-lookup"><span data-stu-id="7adf0-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="7adf0-120">Utilisation de base</span><span class="sxs-lookup"><span data-stu-id="7adf0-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="7adf0-121">Clients nommés</span><span class="sxs-lookup"><span data-stu-id="7adf0-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="7adf0-122">Clients typés</span><span class="sxs-lookup"><span data-stu-id="7adf0-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="7adf0-123">Clients générés</span><span class="sxs-lookup"><span data-stu-id="7adf0-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="7adf0-124">La meilleure approche dépend des exigences de l’application.</span><span class="sxs-lookup"><span data-stu-id="7adf0-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="7adf0-125">Utilisation de base</span><span class="sxs-lookup"><span data-stu-id="7adf0-125">Basic usage</span></span>

<span data-ttu-id="7adf0-126">`IHttpClientFactory` peut être inscrit en appelant `AddHttpClient` :</span><span class="sxs-lookup"><span data-stu-id="7adf0-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="7adf0-127">Un `IHttpClientFactory` peut être demandé à l’aide [de l’injection de dépendances (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="7adf0-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="7adf0-128">Le code suivant utilise `IHttpClientFactory` pour créer une `HttpClient` instance :</span><span class="sxs-lookup"><span data-stu-id="7adf0-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="7adf0-129">L’utilisation `IHttpClientFactory` de like dans l’exemple précédent est un bon moyen de refactoriser une application existante.</span><span class="sxs-lookup"><span data-stu-id="7adf0-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="7adf0-130">Elle n’a aucun impact sur la façon dont `HttpClient` est utilisé.</span><span class="sxs-lookup"><span data-stu-id="7adf0-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="7adf0-131">Dans les emplacements où les `HttpClient` instances sont créées dans une application existante, remplacez-les par des appels à <xref:System.Net.Http.IHttpClientFactory.CreateClient*> .</span><span class="sxs-lookup"><span data-stu-id="7adf0-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="7adf0-132">Clients nommés</span><span class="sxs-lookup"><span data-stu-id="7adf0-132">Named clients</span></span>

<span data-ttu-id="7adf0-133">Les clients nommés sont un bon choix lorsque :</span><span class="sxs-lookup"><span data-stu-id="7adf0-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="7adf0-134">L’application requiert de nombreuses utilisations distinctes de `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="7adf0-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="7adf0-135">De nombreux `HttpClient` s ont une configuration différente.</span><span class="sxs-lookup"><span data-stu-id="7adf0-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="7adf0-136">La configuration d’un nom `HttpClient` peut être spécifiée lors de l’inscription dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="7adf0-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="7adf0-137">Dans le code précédent, le client est configuré avec :</span><span class="sxs-lookup"><span data-stu-id="7adf0-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="7adf0-138">Adresse de base `https://api.github.com/` .</span><span class="sxs-lookup"><span data-stu-id="7adf0-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="7adf0-139">Deux en-têtes nécessaires à l’utilisation de l’API GitHub.</span><span class="sxs-lookup"><span data-stu-id="7adf0-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="7adf0-140">CreateClient</span><span class="sxs-lookup"><span data-stu-id="7adf0-140">CreateClient</span></span>

<span data-ttu-id="7adf0-141">Chaque fois que <xref:System.Net.Http.IHttpClientFactory.CreateClient*> est appelé :</span><span class="sxs-lookup"><span data-stu-id="7adf0-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="7adf0-142">Une nouvelle instance de `HttpClient` est créée.</span><span class="sxs-lookup"><span data-stu-id="7adf0-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="7adf0-143">L’action de configuration est appelée.</span><span class="sxs-lookup"><span data-stu-id="7adf0-143">The configuration action is called.</span></span>

<span data-ttu-id="7adf0-144">Pour créer un client nommé, transmettez son nom à `CreateClient` :</span><span class="sxs-lookup"><span data-stu-id="7adf0-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="7adf0-145">Dans le code précédent, la requête n’a pas besoin de spécifier un nom d’hôte.</span><span class="sxs-lookup"><span data-stu-id="7adf0-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="7adf0-146">Le code peut passer uniquement le chemin d’accès, dans la mesure où l’adresse de base configurée pour le client est utilisée.</span><span class="sxs-lookup"><span data-stu-id="7adf0-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="7adf0-147">Clients typés</span><span class="sxs-lookup"><span data-stu-id="7adf0-147">Typed clients</span></span>

<span data-ttu-id="7adf0-148">Clients typés :</span><span class="sxs-lookup"><span data-stu-id="7adf0-148">Typed clients:</span></span>

* <span data-ttu-id="7adf0-149">Fournissent les mêmes fonctionnalités que les clients nommés, sans qu’il soit nécessaire d’utiliser des chaînes comme clés.</span><span class="sxs-lookup"><span data-stu-id="7adf0-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="7adf0-150">Bénéficie de l’aide d’IntelliSense et du compilateur lors de l’utilisation des clients.</span><span class="sxs-lookup"><span data-stu-id="7adf0-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="7adf0-151">Fournissent un emplacement unique pour configurer et interagir avec un `HttpClient` particulier.</span><span class="sxs-lookup"><span data-stu-id="7adf0-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="7adf0-152">Par exemple, un seul client typé peut être utilisé :</span><span class="sxs-lookup"><span data-stu-id="7adf0-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="7adf0-153">Pour un seul point de terminaison backend.</span><span class="sxs-lookup"><span data-stu-id="7adf0-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="7adf0-154">Pour encapsuler toute la logique traitant le point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="7adf0-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="7adf0-155">Utilisez DI et pouvez les injecter lorsque cela est requis dans l’application.</span><span class="sxs-lookup"><span data-stu-id="7adf0-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="7adf0-156">Un client typé accepte un `HttpClient` paramètre dans son constructeur :</span><span class="sxs-lookup"><span data-stu-id="7adf0-156">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="7adf0-157">Dans le code précédent :</span><span class="sxs-lookup"><span data-stu-id="7adf0-157">In the preceding code:</span></span>

* <span data-ttu-id="7adf0-158">La configuration est déplacée vers le client typé.</span><span class="sxs-lookup"><span data-stu-id="7adf0-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="7adf0-159">L’objet `HttpClient` est exposé en tant que propriété publique.</span><span class="sxs-lookup"><span data-stu-id="7adf0-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="7adf0-160">Vous pouvez créer des méthodes spécifiques à l’API qui exposent les `HttpClient` fonctionnalités.</span><span class="sxs-lookup"><span data-stu-id="7adf0-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="7adf0-161">Par exemple, la `GetAspNetDocsIssues` méthode encapsule du code pour récupérer des problèmes ouverts.</span><span class="sxs-lookup"><span data-stu-id="7adf0-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="7adf0-162">Le code suivant appelle <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> dans `Startup.ConfigureServices` pour inscrire une classe de client typée :</span><span class="sxs-lookup"><span data-stu-id="7adf0-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="7adf0-163">Le client typé est inscrit comme étant transitoire avec injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="7adf0-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="7adf0-164">Dans le code précédent, `AddHttpClient` s’inscrit `GitHubService` en tant que service temporaire.</span><span class="sxs-lookup"><span data-stu-id="7adf0-164">In the preceding code, `AddHttpClient` registers `GitHubService` as a transient service.</span></span> <span data-ttu-id="7adf0-165">Cette inscription utilise une méthode de fabrique pour :</span><span class="sxs-lookup"><span data-stu-id="7adf0-165">This registration uses a factory method to:</span></span>

1. <span data-ttu-id="7adf0-166">Créez une instance de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-166">Create an instance of `HttpClient`.</span></span>
1. <span data-ttu-id="7adf0-167">Crée une instance de `GitHubService` , en passant l’instance de `HttpClient` à son constructeur.</span><span class="sxs-lookup"><span data-stu-id="7adf0-167">Create an instance of `GitHubService`, passing in the instance of `HttpClient` to its constructor.</span></span>

<span data-ttu-id="7adf0-168">Le client typé peut être injecté et utilisé directement :</span><span class="sxs-lookup"><span data-stu-id="7adf0-168">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="7adf0-169">La configuration d’un client typé peut être spécifiée lors de l’inscription dans `Startup.ConfigureServices` , plutôt que dans le constructeur du client typé :</span><span class="sxs-lookup"><span data-stu-id="7adf0-169">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="7adf0-170">`HttpClient`Peut être encapsulé dans un client typé.</span><span class="sxs-lookup"><span data-stu-id="7adf0-170">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="7adf0-171">Au lieu de l’exposer en tant que propriété, définissez une méthode qui appelle l' `HttpClient` instance en interne :</span><span class="sxs-lookup"><span data-stu-id="7adf0-171">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="7adf0-172">Dans le code précédent, le `HttpClient` est stocké dans un champ privé.</span><span class="sxs-lookup"><span data-stu-id="7adf0-172">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="7adf0-173">L’accès à `HttpClient` est par la `GetRepos` méthode publique.</span><span class="sxs-lookup"><span data-stu-id="7adf0-173">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="7adf0-174">Clients générés</span><span class="sxs-lookup"><span data-stu-id="7adf0-174">Generated clients</span></span>

<span data-ttu-id="7adf0-175">`IHttpClientFactory` peut être utilisé en association avec des bibliothèques tierces telles que la [réajuster](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="7adf0-175">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="7adf0-176">Refit est une bibliothèque REST pour .NET.</span><span class="sxs-lookup"><span data-stu-id="7adf0-176">Refit is a REST library for .NET.</span></span> <span data-ttu-id="7adf0-177">Il convertit les API REST en interfaces dynamiques.</span><span class="sxs-lookup"><span data-stu-id="7adf0-177">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="7adf0-178">Une implémentation de l’interface est générée dynamiquement par le `RestService`, avec `HttpClient` pour faire les appels HTTP externes.</span><span class="sxs-lookup"><span data-stu-id="7adf0-178">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="7adf0-179">Une interface et une réponse sont définies pour représenter l’API externe et sa réponse :</span><span class="sxs-lookup"><span data-stu-id="7adf0-179">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="7adf0-180">Vous pouvez ajouter un client typé en utilisant Refit pour générer l’implémentation :</span><span class="sxs-lookup"><span data-stu-id="7adf0-180">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="7adf0-181">L’interface définie peut être utilisée quand c’est nécessaire, avec l’implémentation fournie par l’injection de dépendances et Refit :</span><span class="sxs-lookup"><span data-stu-id="7adf0-181">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="make-post-put-and-delete-requests"></a><span data-ttu-id="7adf0-182">Créer des demandes de publication, de placement et de suppression</span><span class="sxs-lookup"><span data-stu-id="7adf0-182">Make POST, PUT, and DELETE requests</span></span>

<span data-ttu-id="7adf0-183">Dans les exemples précédents, toutes les requêtes HTTP utilisent le verbe HTTP.</span><span class="sxs-lookup"><span data-stu-id="7adf0-183">In the preceding examples, all HTTP requests use the GET HTTP verb.</span></span> <span data-ttu-id="7adf0-184">`HttpClient` prend également en charge d’autres verbes HTTP, notamment :</span><span class="sxs-lookup"><span data-stu-id="7adf0-184">`HttpClient` also supports other HTTP verbs, including:</span></span>

* <span data-ttu-id="7adf0-185">POST</span><span class="sxs-lookup"><span data-stu-id="7adf0-185">POST</span></span>
* <span data-ttu-id="7adf0-186">PUT</span><span class="sxs-lookup"><span data-stu-id="7adf0-186">PUT</span></span>
* <span data-ttu-id="7adf0-187">DELETE</span><span class="sxs-lookup"><span data-stu-id="7adf0-187">DELETE</span></span>
* <span data-ttu-id="7adf0-188">PATCH</span><span class="sxs-lookup"><span data-stu-id="7adf0-188">PATCH</span></span>

<span data-ttu-id="7adf0-189">Pour obtenir la liste complète des verbes HTTP pris en charge, consultez <xref:System.Net.Http.HttpMethod> .</span><span class="sxs-lookup"><span data-stu-id="7adf0-189">For a complete list of supported HTTP verbs, see <xref:System.Net.Http.HttpMethod>.</span></span>

<span data-ttu-id="7adf0-190">L’exemple suivant montre comment effectuer une requête HTTP après :</span><span class="sxs-lookup"><span data-stu-id="7adf0-190">The following example shows how to make an HTTP POST request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_POST)]

<span data-ttu-id="7adf0-191">Dans le code précédent, la `CreateItemAsync` méthode :</span><span class="sxs-lookup"><span data-stu-id="7adf0-191">In the preceding code, the `CreateItemAsync` method:</span></span>

* <span data-ttu-id="7adf0-192">Sérialise le `TodoItem` paramètre au format JSON à l’aide de `System.Text.Json` .</span><span class="sxs-lookup"><span data-stu-id="7adf0-192">Serializes the `TodoItem` parameter to JSON using `System.Text.Json`.</span></span> <span data-ttu-id="7adf0-193">Utilise une instance de <xref:System.Text.Json.JsonSerializerOptions> pour configurer le processus de sérialisation.</span><span class="sxs-lookup"><span data-stu-id="7adf0-193">This uses an instance of <xref:System.Text.Json.JsonSerializerOptions> to configure the serialization process.</span></span>
* <span data-ttu-id="7adf0-194">Crée une instance de <xref:System.Net.Http.StringContent> pour empaqueter le JSON sérialisé à envoyer dans le corps de la requête http.</span><span class="sxs-lookup"><span data-stu-id="7adf0-194">Creates an instance of <xref:System.Net.Http.StringContent> to package the serialized JSON for sending in the HTTP request's body.</span></span>
* <span data-ttu-id="7adf0-195">Appelle <xref:System.Net.Http.HttpClient.PostAsync%2A> pour envoyer le contenu JSON à l’URL spécifiée.</span><span class="sxs-lookup"><span data-stu-id="7adf0-195">Calls <xref:System.Net.Http.HttpClient.PostAsync%2A> to send the JSON content to the specified URL.</span></span> <span data-ttu-id="7adf0-196">Il s’agit d’une URL relative qui est ajoutée à [httpclient. BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress).</span><span class="sxs-lookup"><span data-stu-id="7adf0-196">This is a relative URL that gets added to the [HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress).</span></span>
* <span data-ttu-id="7adf0-197">Appelle <xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A> pour lever une exception si le code d’état de réponse n’indique pas la réussite.</span><span class="sxs-lookup"><span data-stu-id="7adf0-197">Calls <xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A> to throw an exception if the response status code does not indicate success.</span></span>

<span data-ttu-id="7adf0-198">`HttpClient` prend également en charge d’autres types de contenu.</span><span class="sxs-lookup"><span data-stu-id="7adf0-198">`HttpClient` also supports other types of content.</span></span> <span data-ttu-id="7adf0-199">Par exemple, <xref:System.Net.Http.MultipartContent> et <xref:System.Net.Http.StreamContent>.</span><span class="sxs-lookup"><span data-stu-id="7adf0-199">For example, <xref:System.Net.Http.MultipartContent> and <xref:System.Net.Http.StreamContent>.</span></span> <span data-ttu-id="7adf0-200">Pour obtenir la liste complète du contenu pris en charge, consultez <xref:System.Net.Http.HttpContent> .</span><span class="sxs-lookup"><span data-stu-id="7adf0-200">For a complete list of supported content, see <xref:System.Net.Http.HttpContent>.</span></span>

<span data-ttu-id="7adf0-201">L’exemple suivant illustre une requête HTTP PUT :</span><span class="sxs-lookup"><span data-stu-id="7adf0-201">The following example shows an HTTP PUT request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_PUT)]

<span data-ttu-id="7adf0-202">Le code précédent est très similaire à l’exemple de billet.</span><span class="sxs-lookup"><span data-stu-id="7adf0-202">The preceding code is very similar to the POST example.</span></span> <span data-ttu-id="7adf0-203">La `SaveItemAsync` méthode appelle à la <xref:System.Net.Http.HttpClient.PutAsync%2A> place de `PostAsync` .</span><span class="sxs-lookup"><span data-stu-id="7adf0-203">The `SaveItemAsync` method calls <xref:System.Net.Http.HttpClient.PutAsync%2A> instead of `PostAsync`.</span></span>

<span data-ttu-id="7adf0-204">L’exemple suivant illustre une requête HTTP DELETE :</span><span class="sxs-lookup"><span data-stu-id="7adf0-204">The following example shows an HTTP DELETE request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_DELETE)]

<span data-ttu-id="7adf0-205">Dans le code précédent, la `DeleteItemAsync` méthode appelle <xref:System.Net.Http.HttpClient.DeleteAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="7adf0-205">In the preceding code, the `DeleteItemAsync` method calls <xref:System.Net.Http.HttpClient.DeleteAsync%2A>.</span></span> <span data-ttu-id="7adf0-206">Étant donné que les requêtes HTTP DELETE ne contiennent généralement pas de corps, la `DeleteAsync` méthode ne fournit pas de surcharge qui accepte une instance de `HttpContent` .</span><span class="sxs-lookup"><span data-stu-id="7adf0-206">Because HTTP DELETE requests typically contain no body, the `DeleteAsync` method doesn't provide an overload that accepts an instance of `HttpContent`.</span></span>

<span data-ttu-id="7adf0-207">Pour en savoir plus sur l’utilisation de différents verbes HTTP avec `HttpClient` , consultez <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="7adf0-207">To learn more about using different HTTP verbs with `HttpClient`, see <xref:System.Net.Http.HttpClient>.</span></span>

## <a name="outgoing-request-middleware"></a><span data-ttu-id="7adf0-208">Middleware pour les requêtes sortantes</span><span class="sxs-lookup"><span data-stu-id="7adf0-208">Outgoing request middleware</span></span>

<span data-ttu-id="7adf0-209">`HttpClient` présente le concept de délégation de gestionnaires qui peuvent être liés entre eux pour les requêtes HTTP sortantes.</span><span class="sxs-lookup"><span data-stu-id="7adf0-209">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="7adf0-210">`IHttpClientFactory`:</span><span class="sxs-lookup"><span data-stu-id="7adf0-210">`IHttpClientFactory`:</span></span>

* <span data-ttu-id="7adf0-211">Simplifie la définition des gestionnaires à appliquer pour chaque client nommé.</span><span class="sxs-lookup"><span data-stu-id="7adf0-211">Simplifies defining the handlers to apply for each named client.</span></span>
* <span data-ttu-id="7adf0-212">Prend en charge l’inscription et le chaînage de plusieurs gestionnaires pour générer un pipeline d’intergiciel (middleware) de demande sortante.</span><span class="sxs-lookup"><span data-stu-id="7adf0-212">Supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="7adf0-213">Chacun de ces gestionnaires peut effectuer un travail avant et après la requête sortante.</span><span class="sxs-lookup"><span data-stu-id="7adf0-213">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="7adf0-214">Ce modèle :</span><span class="sxs-lookup"><span data-stu-id="7adf0-214">This pattern:</span></span>

  * <span data-ttu-id="7adf0-215">Est semblable au pipeline d’intergiciel (middleware) entrant dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7adf0-215">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
  * <span data-ttu-id="7adf0-216">Fournit un mécanisme permettant de gérer les problèmes transversaux autour des requêtes HTTP, par exemple :</span><span class="sxs-lookup"><span data-stu-id="7adf0-216">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>

    * <span data-ttu-id="7adf0-217">caching</span><span class="sxs-lookup"><span data-stu-id="7adf0-217">caching</span></span>
    * <span data-ttu-id="7adf0-218">gestion des erreurs</span><span class="sxs-lookup"><span data-stu-id="7adf0-218">error handling</span></span>
    * <span data-ttu-id="7adf0-219">sérialisation</span><span class="sxs-lookup"><span data-stu-id="7adf0-219">serialization</span></span>
    * <span data-ttu-id="7adf0-220">journalisation</span><span class="sxs-lookup"><span data-stu-id="7adf0-220">logging</span></span>

<span data-ttu-id="7adf0-221">Pour créer un gestionnaire de délégation :</span><span class="sxs-lookup"><span data-stu-id="7adf0-221">To create a delegating handler:</span></span>

* <span data-ttu-id="7adf0-222">Dériver de <xref:System.Net.Http.DelegatingHandler> .</span><span class="sxs-lookup"><span data-stu-id="7adf0-222">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="7adf0-223">Remplacez <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span><span class="sxs-lookup"><span data-stu-id="7adf0-223">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="7adf0-224">Exécutez le code avant de passer la requête au gestionnaire suivant dans le pipeline :</span><span class="sxs-lookup"><span data-stu-id="7adf0-224">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="7adf0-225">Le code précédent vérifie si l' `X-API-KEY` en-tête est dans la demande.</span><span class="sxs-lookup"><span data-stu-id="7adf0-225">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="7adf0-226">Si `X-API-KEY` est manquant, <xref:System.Net.HttpStatusCode.BadRequest> est retourné.</span><span class="sxs-lookup"><span data-stu-id="7adf0-226">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="7adf0-227">Plusieurs gestionnaires peuvent être ajoutés à la configuration d’un `HttpClient` avec <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName> :</span><span class="sxs-lookup"><span data-stu-id="7adf0-227">More than one handler can be added to the configuration for an `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="7adf0-228">Dans le code précédent, le `ValidateHeaderHandler` est inscrit avec une injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="7adf0-228">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="7adf0-229">`IHttpClientFactory` crée une étendue DI distincte pour chaque gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="7adf0-229">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="7adf0-230">Les gestionnaires peuvent dépendre des services de toute portée.</span><span class="sxs-lookup"><span data-stu-id="7adf0-230">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="7adf0-231">Les services dont dépendent les gestionnaires sont supprimés lorsque le gestionnaire est supprimé.</span><span class="sxs-lookup"><span data-stu-id="7adf0-231">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="7adf0-232">Une fois inscrit, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> peut être appelé en passant en entrée le type pour le gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="7adf0-232">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="7adf0-233">Vous pouvez inscrire plusieurs gestionnaires dans l’ordre où ils doivent être exécutés.</span><span class="sxs-lookup"><span data-stu-id="7adf0-233">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="7adf0-234">Chaque gestionnaire wrappe le gestionnaire suivant jusqu’à ce que le dernier `HttpClientHandler` exécute la requête :</span><span class="sxs-lookup"><span data-stu-id="7adf0-234">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="7adf0-235">Utilisez l’une des approches suivantes pour partager l’état de chaque requête avec les gestionnaires de messages :</span><span class="sxs-lookup"><span data-stu-id="7adf0-235">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="7adf0-236">Transmettez les données dans le gestionnaire à l’aide de [HttpRequestMessage. Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span><span class="sxs-lookup"><span data-stu-id="7adf0-236">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="7adf0-237">Utilisez <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> pour accéder à la requête en cours.</span><span class="sxs-lookup"><span data-stu-id="7adf0-237">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="7adf0-238">Créez un objet de stockage <xref:System.Threading.AsyncLocal`1> personnalisé pour passer les données.</span><span class="sxs-lookup"><span data-stu-id="7adf0-238">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="7adf0-239">Utiliser les gestionnaires Polly</span><span class="sxs-lookup"><span data-stu-id="7adf0-239">Use Polly-based handlers</span></span>

<span data-ttu-id="7adf0-240">`IHttpClientFactory` s’intègre à la bibliothèque tierce [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="7adf0-240">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="7adf0-241">Polly est une bibliothèque complète de gestion des erreurs transitoires et de résilience pour .NET.</span><span class="sxs-lookup"><span data-stu-id="7adf0-241">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="7adf0-242">Elle permet aux développeurs de formuler facilement et de façon thread-safe des stratégies, comme Retry (Nouvelle tentative), Circuit Breaker (Disjoncteur), Timeout (Délai d’attente), Bulkhead Isolation (Isolation par cloisonnement) et Fallback (Alternative de repli).</span><span class="sxs-lookup"><span data-stu-id="7adf0-242">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="7adf0-243">Des méthodes d’extension sont fournies pour permettre l’utilisation de stratégies Polly avec les instances configurées de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-243">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="7adf0-244">Les extensions Polly prennent en charge l’ajout de gestionnaires basés sur Polly aux clients.</span><span class="sxs-lookup"><span data-stu-id="7adf0-244">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="7adf0-245">Polly requiert le package NuGet [Microsoft. extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .</span><span class="sxs-lookup"><span data-stu-id="7adf0-245">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="7adf0-246">Gérer les erreurs temporaires</span><span class="sxs-lookup"><span data-stu-id="7adf0-246">Handle transient faults</span></span>

<span data-ttu-id="7adf0-247">Les erreurs se produisent généralement lorsque les appels HTTP externes sont temporaires.</span><span class="sxs-lookup"><span data-stu-id="7adf0-247">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="7adf0-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> permet de définir une stratégie pour gérer les erreurs temporaires.</span><span class="sxs-lookup"><span data-stu-id="7adf0-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="7adf0-249">Les stratégies configurées avec `AddTransientHttpErrorPolicy` gèrent les réponses suivantes :</span><span class="sxs-lookup"><span data-stu-id="7adf0-249">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="7adf0-250">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="7adf0-250">HTTP 5xx</span></span>
* <span data-ttu-id="7adf0-251">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="7adf0-251">HTTP 408</span></span>

<span data-ttu-id="7adf0-252">`AddTransientHttpErrorPolicy` fournit l’accès à un `PolicyBuilder` objet configuré pour gérer les erreurs qui représentent une erreur temporaire possible :</span><span class="sxs-lookup"><span data-stu-id="7adf0-252">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="7adf0-253">Dans le code précédent, une stratégie `WaitAndRetryAsync` est définie.</span><span class="sxs-lookup"><span data-stu-id="7adf0-253">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="7adf0-254">Les requêtes qui ont échoué sont retentées jusqu’à trois fois avec un délai de 600 ms entre les tentatives.</span><span class="sxs-lookup"><span data-stu-id="7adf0-254">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="7adf0-255">Sélectionner dynamiquement des stratégies</span><span class="sxs-lookup"><span data-stu-id="7adf0-255">Dynamically select policies</span></span>

<span data-ttu-id="7adf0-256">Les méthodes d’extension sont fournies pour ajouter des gestionnaires basés sur Polly, par exemple, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*> .</span><span class="sxs-lookup"><span data-stu-id="7adf0-256">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="7adf0-257">La `AddPolicyHandler` surcharge suivante inspecte la demande pour décider de la stratégie à appliquer :</span><span class="sxs-lookup"><span data-stu-id="7adf0-257">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="7adf0-258">Dans le code précédent, si la requête sortante est un HTTP GET, un délai d’attente de 10 secondes est appliqué.</span><span class="sxs-lookup"><span data-stu-id="7adf0-258">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="7adf0-259">Pour toutes les autres méthodes HTTP, un délai d’attente de 30 secondes est utilisé.</span><span class="sxs-lookup"><span data-stu-id="7adf0-259">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="7adf0-260">Ajouter plusieurs gestionnaires Polly</span><span class="sxs-lookup"><span data-stu-id="7adf0-260">Add multiple Polly handlers</span></span>

<span data-ttu-id="7adf0-261">Il est courant d’imbriquer les stratégies Polly :</span><span class="sxs-lookup"><span data-stu-id="7adf0-261">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="7adf0-262">Dans l’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="7adf0-262">In the preceding example:</span></span>

* <span data-ttu-id="7adf0-263">Deux gestionnaires sont ajoutés.</span><span class="sxs-lookup"><span data-stu-id="7adf0-263">Two handlers are added.</span></span>
* <span data-ttu-id="7adf0-264">Le premier gestionnaire utilise <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> pour ajouter une stratégie de nouvelle tentative.</span><span class="sxs-lookup"><span data-stu-id="7adf0-264">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="7adf0-265">Les requêtes qui ont échoué sont retentées jusqu’à trois fois.</span><span class="sxs-lookup"><span data-stu-id="7adf0-265">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="7adf0-266">Le deuxième `AddTransientHttpErrorPolicy` appel ajoute une stratégie de disjoncteur.</span><span class="sxs-lookup"><span data-stu-id="7adf0-266">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="7adf0-267">Les demandes externes supplémentaires sont bloquées pendant 30 secondes si 5 tentatives ayant échoué se produisent de façon séquentielle.</span><span class="sxs-lookup"><span data-stu-id="7adf0-267">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="7adf0-268">Les stratégies de disjoncteur sont avec état.</span><span class="sxs-lookup"><span data-stu-id="7adf0-268">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="7adf0-269">Tous les appels effectués via ce client partagent le même état du circuit.</span><span class="sxs-lookup"><span data-stu-id="7adf0-269">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="7adf0-270">Ajouter des stratégies à partir du Registre Polly</span><span class="sxs-lookup"><span data-stu-id="7adf0-270">Add policies from the Polly registry</span></span>

<span data-ttu-id="7adf0-271">Une approche de la gestion des stratégies régulièrement utilisées consiste à les définir une seule fois et à les inscrire avec un `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-271">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="7adf0-272">Dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="7adf0-272">In the following code:</span></span>

* <span data-ttu-id="7adf0-273">Les stratégies « standard » et « longues » sont ajoutées.</span><span class="sxs-lookup"><span data-stu-id="7adf0-273">The "regular" and "long" policies are added.</span></span>
* <span data-ttu-id="7adf0-274"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> Ajoute les stratégies « standard » et « longues » à partir du Registre.</span><span class="sxs-lookup"><span data-stu-id="7adf0-274"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="7adf0-275">Pour plus d’informations sur `IHttpClientFactory` les intégrations de et de Polly, consultez le [wiki Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="7adf0-275">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="7adf0-276">HttpClient et gestion de la durée de vie</span><span class="sxs-lookup"><span data-stu-id="7adf0-276">HttpClient and lifetime management</span></span>

<span data-ttu-id="7adf0-277">Une nouvelle instance `HttpClient` est retournée à chaque fois que `CreateClient` est appelé sur `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-277">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="7adf0-278">Un <xref:System.Net.Http.HttpMessageHandler> est créé par client nommé.</span><span class="sxs-lookup"><span data-stu-id="7adf0-278">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="7adf0-279">La fabrique gère les durées de vie des instances `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-279">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="7adf0-280">`IHttpClientFactory` regroupe dans un pool les instances de `HttpMessageHandler` créées par la fabrique pour réduire la consommation des ressources.</span><span class="sxs-lookup"><span data-stu-id="7adf0-280">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="7adf0-281">Une instance de `HttpMessageHandler` peut être réutilisée à partir du pool quand vous créez une instance de `HttpClient` si sa durée de vie n’a pas expiré.</span><span class="sxs-lookup"><span data-stu-id="7adf0-281">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="7adf0-282">Le regroupement de gestionnaires en pools est souhaitable, car chaque gestionnaire gère habituellement ses propres connexions HTTP sous-jacentes.</span><span class="sxs-lookup"><span data-stu-id="7adf0-282">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="7adf0-283">La création de plus de gestionnaires que nécessaire peut entraîner des délais de connexion.</span><span class="sxs-lookup"><span data-stu-id="7adf0-283">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="7adf0-284">Certains gestionnaires maintiennent également les connexions ouvertes indéfiniment, ce qui peut empêcher le gestionnaire de réagir aux modifications DNS (Domain Name System).</span><span class="sxs-lookup"><span data-stu-id="7adf0-284">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="7adf0-285">La durée de vie par défaut d’un gestionnaire est de deux minutes.</span><span class="sxs-lookup"><span data-stu-id="7adf0-285">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="7adf0-286">La valeur par défaut peut être substituée pour chaque client nommé :</span><span class="sxs-lookup"><span data-stu-id="7adf0-286">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="7adf0-287">`HttpClient` les instances peuvent généralement être traitées comme des objets .NET qui **ne nécessitent pas** de suppression.</span><span class="sxs-lookup"><span data-stu-id="7adf0-287">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="7adf0-288">La suppression annule les requêtes sortantes et garantit que l’instance `HttpClient` donnée ne peut pas être utilisée après avoir appelé <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="7adf0-288">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="7adf0-289">`IHttpClientFactory` effectue le suivi et libère les ressources utilisées par les instances `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-289">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="7adf0-290">Le fait de conserver une seule instance `HttpClient` active pendant une longue durée est un modèle commun utilisé avant le lancement de `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-290">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="7adf0-291">Ce modèle devient inutile après la migration vers `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-291">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="7adf0-292">Alternatives à IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="7adf0-292">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="7adf0-293">L’utilisation `IHttpClientFactory` de dans une application di-Enabled évite les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="7adf0-293">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="7adf0-294">Problèmes d’épuisement des ressources par les instances de regroupement `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="7adf0-294">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="7adf0-295">Problèmes DNS périmés par les instances cycliques `HttpMessageHandler` à intervalles réguliers.</span><span class="sxs-lookup"><span data-stu-id="7adf0-295">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="7adf0-296">Il existe d’autres façons de résoudre les problèmes précédents à l’aide d’une instance de longue durée <xref:System.Net.Http.SocketsHttpHandler> .</span><span class="sxs-lookup"><span data-stu-id="7adf0-296">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="7adf0-297">Créez une instance de `SocketsHttpHandler` lorsque l’application démarre et utilisez-la pendant toute la durée de vie de l’application.</span><span class="sxs-lookup"><span data-stu-id="7adf0-297">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="7adf0-298">Configurez <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> avec une valeur appropriée en fonction des temps d’actualisation DNS.</span><span class="sxs-lookup"><span data-stu-id="7adf0-298">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="7adf0-299">Créez des `HttpClient` instances en utilisant `new HttpClient(handler, disposeHandler: false)` si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="7adf0-299">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="7adf0-300">Les approches précédentes résolvent les problèmes de gestion des ressources qui sont `IHttpClientFactory` résolus de la même façon.</span><span class="sxs-lookup"><span data-stu-id="7adf0-300">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="7adf0-301">Le `SocketsHttpHandler` partage les connexions entre les `HttpClient` instances.</span><span class="sxs-lookup"><span data-stu-id="7adf0-301">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="7adf0-302">Ce partage empêche l’épuisement des sockets.</span><span class="sxs-lookup"><span data-stu-id="7adf0-302">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="7adf0-303">Le `SocketsHttpHandler` cycle des connexions en fonction de `PooledConnectionLifetime` pour éviter les problèmes DNS périmés.</span><span class="sxs-lookup"><span data-stu-id="7adf0-303">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="no-loccookies"></a><span data-ttu-id="7adf0-304">Cookies</span><span class="sxs-lookup"><span data-stu-id="7adf0-304">Cookies</span></span>

<span data-ttu-id="7adf0-305">Les instances regroupées `HttpMessageHandler` entraînent le `CookieContainer` partage des objets.</span><span class="sxs-lookup"><span data-stu-id="7adf0-305">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="7adf0-306">Le `CookieContainer` partage d’objets imprévus aboutit souvent à un code incorrect.</span><span class="sxs-lookup"><span data-stu-id="7adf0-306">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="7adf0-307">Pour les applications qui nécessitent des cookie , envisagez l’une des deux opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="7adf0-307">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="7adf0-308">Désactivation de la cookie gestion automatique</span><span class="sxs-lookup"><span data-stu-id="7adf0-308">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="7adf0-309">Éviter `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="7adf0-309">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="7adf0-310">Appelez <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> pour désactiver la cookie gestion automatique :</span><span class="sxs-lookup"><span data-stu-id="7adf0-310">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="7adf0-311">Journalisation</span><span class="sxs-lookup"><span data-stu-id="7adf0-311">Logging</span></span>

<span data-ttu-id="7adf0-312">Les clients créés via `IHttpClientFactory` enregistrent les messages de journalisation pour toutes les requêtes.</span><span class="sxs-lookup"><span data-stu-id="7adf0-312">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="7adf0-313">Activez le niveau d’information approprié dans la configuration de la journalisation pour voir les messages du journal par défaut.</span><span class="sxs-lookup"><span data-stu-id="7adf0-313">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="7adf0-314">Une journalisation supplémentaire, comme celle des en-têtes des requêtes, est incluse seulement au niveau de trace.</span><span class="sxs-lookup"><span data-stu-id="7adf0-314">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="7adf0-315">La catégorie de journal utilisée pour chaque client comprend le nom du client.</span><span class="sxs-lookup"><span data-stu-id="7adf0-315">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="7adf0-316">Un client nommé *MyNamedClient*, par exemple, journalise des messages avec la catégorie « System .net. http. httpclient ». **MyNamedClient**. LogicalHandler".</span><span class="sxs-lookup"><span data-stu-id="7adf0-316">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="7adf0-317">Les messages avec le suffixe *LogicalHandler* se produisent en dehors du pipeline du gestionnaire de requêtes.</span><span class="sxs-lookup"><span data-stu-id="7adf0-317">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="7adf0-318">Lors d’une requête, les messages sont journalisés avant que d’autres gestionnaires du pipeline l’aient traitée.</span><span class="sxs-lookup"><span data-stu-id="7adf0-318">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="7adf0-319">Lors d’une réponse, les messages sont journalisés une fois que tous les autres gestionnaires du pipeline ont reçu la réponse.</span><span class="sxs-lookup"><span data-stu-id="7adf0-319">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="7adf0-320">La journalisation se produit également à l’intérieur du pipeline du gestionnaire de requêtes.</span><span class="sxs-lookup"><span data-stu-id="7adf0-320">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="7adf0-321">Dans l’exemple *MyNamedClient* , ces messages sont journalisés avec la catégorie de journal « System .net. http. httpclient ». **MyNamedClient**. ClientHandler".</span><span class="sxs-lookup"><span data-stu-id="7adf0-321">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="7adf0-322">Pour la requête, cela se produit une fois que tous les autres gestionnaires ont été exécutés et immédiatement avant l’envoi de la demande.</span><span class="sxs-lookup"><span data-stu-id="7adf0-322">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="7adf0-323">Lors de la réponse, cette journalisation inclut l’état de la réponse avant qu’elle repasse à travers le pipeline de gestionnaires.</span><span class="sxs-lookup"><span data-stu-id="7adf0-323">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="7adf0-324">L’activation de la journalisation à l’extérieur et à l’intérieur du pipeline permet l’inspection des changements apportés par les autres gestionnaires du pipeline.</span><span class="sxs-lookup"><span data-stu-id="7adf0-324">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="7adf0-325">Cela peut inclure des modifications apportées aux en-têtes de demande ou au code d’état de réponse.</span><span class="sxs-lookup"><span data-stu-id="7adf0-325">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="7adf0-326">L’inclusion du nom du client dans la catégorie journal active le filtrage des journaux pour des clients nommés spécifiques.</span><span class="sxs-lookup"><span data-stu-id="7adf0-326">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="7adf0-327">Configurer le HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="7adf0-327">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="7adf0-328">Il peut être nécessaire de contrôler la configuration du `HttpMessageHandler` interne utilisé par un client.</span><span class="sxs-lookup"><span data-stu-id="7adf0-328">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="7adf0-329">Un `IHttpClientBuilder` est retourné quand vous ajoutez des clients nommés ou typés.</span><span class="sxs-lookup"><span data-stu-id="7adf0-329">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="7adf0-330">La méthode d’extension <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> peut être utilisée pour définir un délégué.</span><span class="sxs-lookup"><span data-stu-id="7adf0-330">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="7adf0-331">Le délégué est utilisé pour créer et configurer le `HttpMessageHandler` principal utilisé par ce client :</span><span class="sxs-lookup"><span data-stu-id="7adf0-331">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="7adf0-332">Utiliser IHttpClientFactory dans une application console</span><span class="sxs-lookup"><span data-stu-id="7adf0-332">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="7adf0-333">Dans une application console, ajoutez les références de package suivantes au projet :</span><span class="sxs-lookup"><span data-stu-id="7adf0-333">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="7adf0-334">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="7adf0-334">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="7adf0-335">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="7adf0-335">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="7adf0-336">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="7adf0-336">In the following example:</span></span>

* <span data-ttu-id="7adf0-337"><xref:System.Net.Http.IHttpClientFactory> est inscrit dans le conteneur de service dans de l’[hôte générique](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="7adf0-337"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="7adf0-338">`MyService` crée une instance de fabrique cliente à partir du service, qui est utilisée pour créer un `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-338">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="7adf0-339">`HttpClient` est utilisé pour récupérer une page web.</span><span class="sxs-lookup"><span data-stu-id="7adf0-339">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="7adf0-340">`Main` crée une étendue pour exécuter la méthode `GetPage` du service et écrire les 500 premiers caractères du contenu de la page web dans la console.</span><span class="sxs-lookup"><span data-stu-id="7adf0-340">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="7adf0-341">Intergiciel (middleware) de propagation d’en-tête</span><span class="sxs-lookup"><span data-stu-id="7adf0-341">Header propagation middleware</span></span>

<span data-ttu-id="7adf0-342">La propagation d’en-tête est un intergiciel (middleware) ASP.NET Core pour propager les en-têtes HTTP de la requête entrante vers les demandes du client HTTP sortantes.</span><span class="sxs-lookup"><span data-stu-id="7adf0-342">Header propagation is an ASP.NET Core middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="7adf0-343">Pour utiliser la propagation d’en-tête :</span><span class="sxs-lookup"><span data-stu-id="7adf0-343">To use header propagation:</span></span>

* <span data-ttu-id="7adf0-344">Référencez le package [Microsoft. AspNetCore. HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) .</span><span class="sxs-lookup"><span data-stu-id="7adf0-344">Reference the [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) package.</span></span>
* <span data-ttu-id="7adf0-345">Configurer l’intergiciel et `HttpClient` dans `Startup` :</span><span class="sxs-lookup"><span data-stu-id="7adf0-345">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* <span data-ttu-id="7adf0-346">Le client comprend les en-têtes configurés pour les demandes sortantes :</span><span class="sxs-lookup"><span data-stu-id="7adf0-346">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="7adf0-347">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="7adf0-347">Additional resources</span></span>

* [<span data-ttu-id="7adf0-348">Utilisez HttpClientFactory pour implémenter des requêtes HTTP résilientes</span><span class="sxs-lookup"><span data-stu-id="7adf0-348">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="7adf0-349">Implémenter de nouvelles tentatives d’appel HTTP avec interruption exponentielle avec des stratégies Polly et HttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="7adf0-349">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="7adf0-350">Implémenter le modèle Disjoncteur</span><span class="sxs-lookup"><span data-stu-id="7adf0-350">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [<span data-ttu-id="7adf0-351">Comment sérialiser et désérialiser JSON dans .NET</span><span class="sxs-lookup"><span data-stu-id="7adf0-351">How to serialize and deserialize JSON in .NET</span></span>](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="7adf0-352">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak) et [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="7adf0-352">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="7adf0-353">Une <xref:System.Net.Http.IHttpClientFactory> peut être inscrite et utilisée pour configurer et créer des instances de <xref:System.Net.Http.HttpClient> dans une application.</span><span class="sxs-lookup"><span data-stu-id="7adf0-353">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="7adf0-354">Elle offre les avantages suivants :</span><span class="sxs-lookup"><span data-stu-id="7adf0-354">It offers the following benefits:</span></span>

* <span data-ttu-id="7adf0-355">Fournit un emplacement central pour le nommage et la configuration d’instance de `HttpClient` logiques.</span><span class="sxs-lookup"><span data-stu-id="7adf0-355">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="7adf0-356">Par exemple, un client *github* peut être inscrit et configuré pour accéder à [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="7adf0-356">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="7adf0-357">Un client par défaut peut être inscrit à d’autres fins.</span><span class="sxs-lookup"><span data-stu-id="7adf0-357">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="7adf0-358">Codifie le concept de middleware (intergiciel) sortant via la délégation de gestionnaires dans `HttpClient` et fournit des extensions permettant au middleware Polly d’en tirer parti.</span><span class="sxs-lookup"><span data-stu-id="7adf0-358">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="7adf0-359">Gère le regroupement et la durée de vie des instances de `HttpClientMessageHandler` sous-jacentes pour éviter les problèmes DNS courants qui se produisent lors de la gestion manuelle des durées de vie de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-359">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="7adf0-360">Ajoute une expérience de journalisation configurable (via `ILogger`) pour toutes les requêtes envoyées via des clients créés par la fabrique.</span><span class="sxs-lookup"><span data-stu-id="7adf0-360">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="7adf0-361">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7adf0-361">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="7adf0-362">Modèles de consommation</span><span class="sxs-lookup"><span data-stu-id="7adf0-362">Consumption patterns</span></span>

<span data-ttu-id="7adf0-363">Vous pouvez utiliser `IHttpClientFactory` dans une application de plusieurs façons :</span><span class="sxs-lookup"><span data-stu-id="7adf0-363">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="7adf0-364">Utilisation de base</span><span class="sxs-lookup"><span data-stu-id="7adf0-364">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="7adf0-365">Clients nommés</span><span class="sxs-lookup"><span data-stu-id="7adf0-365">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="7adf0-366">Clients typés</span><span class="sxs-lookup"><span data-stu-id="7adf0-366">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="7adf0-367">Clients générés</span><span class="sxs-lookup"><span data-stu-id="7adf0-367">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="7adf0-368">Aucune d’entre elles n’est meilleure qu’une autre.</span><span class="sxs-lookup"><span data-stu-id="7adf0-368">None of them are strictly superior to another.</span></span> <span data-ttu-id="7adf0-369">La meilleure approche dépend des contraintes de l’application.</span><span class="sxs-lookup"><span data-stu-id="7adf0-369">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="7adf0-370">Utilisation de base</span><span class="sxs-lookup"><span data-stu-id="7adf0-370">Basic usage</span></span>

<span data-ttu-id="7adf0-371">Vous pouvez inscrire la `IHttpClientFactory` en appelant la méthode d’extension `AddHttpClient` sur la `IServiceCollection`, à l’intérieur la méthode `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-371">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="7adf0-372">Une fois inscrit, le code peut accepter un `IHttpClientFactory` partout où des services peuvent être injectés avec [une injection de dépendance](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="7adf0-372">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="7adf0-373">Le `IHttpClientFactory` peut être utilisé pour créer une `HttpClient` instance de :</span><span class="sxs-lookup"><span data-stu-id="7adf0-373">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="7adf0-374">L’utilisation de `IHttpClientFactory` de cette façon est un excellent moyen de refactoriser une application existante.</span><span class="sxs-lookup"><span data-stu-id="7adf0-374">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="7adf0-375">Elle n’a aucun impact sur la façon dont `HttpClient` est utilisé.</span><span class="sxs-lookup"><span data-stu-id="7adf0-375">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="7adf0-376">Dans les endroits où les instances de `HttpClient` sont actuellement créées, remplacez ces occurrences par un appel à <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="7adf0-376">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="7adf0-377">Clients nommés</span><span class="sxs-lookup"><span data-stu-id="7adf0-377">Named clients</span></span>

<span data-ttu-id="7adf0-378">Si une application nécessite plusieurs utilisations distinctes de `HttpClient`, chacune avec une configuration différente, une option consiste à utiliser des **clients nommés**.</span><span class="sxs-lookup"><span data-stu-id="7adf0-378">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="7adf0-379">La configuration d’un `HttpClient` nommé peut être spécifiée lors de l’inscription dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-379">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="7adf0-380">Dans le code précédent, `AddHttpClient` est appelé en fournissant le nom *github*.</span><span class="sxs-lookup"><span data-stu-id="7adf0-380">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="7adf0-381">Une configuration par défaut est appliquée à ce client : l’adresse de base et deux en-têtes nécessaires pour utiliser l’API GitHub.</span><span class="sxs-lookup"><span data-stu-id="7adf0-381">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="7adf0-382">Chaque fois que `CreateClient` est appelée, une nouvelle instance de `HttpClient` est créée et l’action de configuration est appelée.</span><span class="sxs-lookup"><span data-stu-id="7adf0-382">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="7adf0-383">Pour utiliser un client nommé, un paramètre de chaîne peut être passé à `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-383">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="7adf0-384">Spécifiez le nom du client à créer :</span><span class="sxs-lookup"><span data-stu-id="7adf0-384">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="7adf0-385">Dans le code précédent, la requête n’a pas besoin de spécifier un nom d’hôte.</span><span class="sxs-lookup"><span data-stu-id="7adf0-385">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="7adf0-386">Elle peut simplement passer le chemin, car l’adresse de base configurée pour le client est utilisée.</span><span class="sxs-lookup"><span data-stu-id="7adf0-386">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="7adf0-387">Clients typés</span><span class="sxs-lookup"><span data-stu-id="7adf0-387">Typed clients</span></span>

<span data-ttu-id="7adf0-388">Clients typés :</span><span class="sxs-lookup"><span data-stu-id="7adf0-388">Typed clients:</span></span>

* <span data-ttu-id="7adf0-389">Fournissent les mêmes fonctionnalités que les clients nommés, sans qu’il soit nécessaire d’utiliser des chaînes comme clés.</span><span class="sxs-lookup"><span data-stu-id="7adf0-389">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="7adf0-390">Bénéficie de l’aide d’IntelliSense et du compilateur lors de l’utilisation des clients.</span><span class="sxs-lookup"><span data-stu-id="7adf0-390">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="7adf0-391">Fournissent un emplacement unique pour configurer et interagir avec un `HttpClient` particulier.</span><span class="sxs-lookup"><span data-stu-id="7adf0-391">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="7adf0-392">Par exemple, un même client typé peut être utilisé pour un point de terminaison de backend et pour encapsuler la logique relative à ce point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="7adf0-392">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="7adf0-393">Fonctionnent avec l’injection de dépendances et peuvent être injectés là où c’est nécessaire dans votre application.</span><span class="sxs-lookup"><span data-stu-id="7adf0-393">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="7adf0-394">Un client typé accepte un `HttpClient` paramètre dans son constructeur :</span><span class="sxs-lookup"><span data-stu-id="7adf0-394">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="7adf0-395">Dans le code précédent, la configuration est déplacée dans le client typé.</span><span class="sxs-lookup"><span data-stu-id="7adf0-395">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="7adf0-396">L’objet `HttpClient` est exposé en tant que propriété publique.</span><span class="sxs-lookup"><span data-stu-id="7adf0-396">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="7adf0-397">Il est possible de définir des méthodes d’API spécifiques qui exposent les fonctionnalités de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-397">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="7adf0-398">La méthode `GetAspNetDocsIssues` encapsule le code nécessaire pour interroger et analyser les problèmes ouverts les plus récents d’un dépôt GitHub.</span><span class="sxs-lookup"><span data-stu-id="7adf0-398">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="7adf0-399">Pour inscrire un client typé, la méthode d’extension <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> générique peut être utilisée dans `Startup.ConfigureServices`, en spécifiant la classe du client typé :</span><span class="sxs-lookup"><span data-stu-id="7adf0-399">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="7adf0-400">Le client typé est inscrit comme étant transitoire avec injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="7adf0-400">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="7adf0-401">Le client typé peut être injecté et utilisé directement :</span><span class="sxs-lookup"><span data-stu-id="7adf0-401">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="7adf0-402">Si vous préférez, vous pouvez spécifier la configuration d’un client typé lors de l’inscription dans `Startup.ConfigureServices` au lieu de le faire dans le constructeur du client typé :</span><span class="sxs-lookup"><span data-stu-id="7adf0-402">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="7adf0-403">Il est possible d’encapsuler entièrement le `HttpClient` dans un client typé.</span><span class="sxs-lookup"><span data-stu-id="7adf0-403">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="7adf0-404">Au lieu de l’exposer en tant que propriété, vous pouvez fournir des méthodes publiques qui appellent l’instance de `HttpClient` en interne.</span><span class="sxs-lookup"><span data-stu-id="7adf0-404">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="7adf0-405">Dans le code précédent, le `HttpClient` est stocké en tant que champ privé.</span><span class="sxs-lookup"><span data-stu-id="7adf0-405">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="7adf0-406">Tous les accès nécessaires pour effectuer des appels externes passent par la méthode `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-406">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="7adf0-407">Clients générés</span><span class="sxs-lookup"><span data-stu-id="7adf0-407">Generated clients</span></span>

<span data-ttu-id="7adf0-408">`IHttpClientFactory` peut être utilisé en combinaison avec d’autres bibliothèques tierces, comme [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="7adf0-408">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="7adf0-409">Refit est une bibliothèque REST pour .NET.</span><span class="sxs-lookup"><span data-stu-id="7adf0-409">Refit is a REST library for .NET.</span></span> <span data-ttu-id="7adf0-410">Il convertit les API REST en interfaces dynamiques.</span><span class="sxs-lookup"><span data-stu-id="7adf0-410">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="7adf0-411">Une implémentation de l’interface est générée dynamiquement par le `RestService`, avec `HttpClient` pour faire les appels HTTP externes.</span><span class="sxs-lookup"><span data-stu-id="7adf0-411">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="7adf0-412">Une interface et une réponse sont définies pour représenter l’API externe et sa réponse :</span><span class="sxs-lookup"><span data-stu-id="7adf0-412">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="7adf0-413">Vous pouvez ajouter un client typé en utilisant Refit pour générer l’implémentation :</span><span class="sxs-lookup"><span data-stu-id="7adf0-413">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="7adf0-414">L’interface définie peut être utilisée quand c’est nécessaire, avec l’implémentation fournie par l’injection de dépendances et Refit :</span><span class="sxs-lookup"><span data-stu-id="7adf0-414">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="7adf0-415">Middleware pour les requêtes sortantes</span><span class="sxs-lookup"><span data-stu-id="7adf0-415">Outgoing request middleware</span></span>

<span data-ttu-id="7adf0-416">`HttpClient` intègre déjà le concept de délégation des gestionnaires qui peuvent être liés ensemble pour les requêtes HTTP sortantes.</span><span class="sxs-lookup"><span data-stu-id="7adf0-416">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="7adf0-417">Le `IHttpClientFactory` permet de définir facilement les gestionnaires à appliquer pour chaque client nommé.</span><span class="sxs-lookup"><span data-stu-id="7adf0-417">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="7adf0-418">Il prend en charge l’inscription et le chaînage de plusieurs gestionnaires pour créer un pipeline de middlewares pour les requêtes sortantes.</span><span class="sxs-lookup"><span data-stu-id="7adf0-418">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="7adf0-419">Chacun de ces gestionnaires peut effectuer un travail avant et après la requête sortante.</span><span class="sxs-lookup"><span data-stu-id="7adf0-419">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="7adf0-420">Ce modèle est similaire au pipeline de middlewares entrants dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7adf0-420">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="7adf0-421">Le modèle fournit un mécanisme permettant de gérer les problèmes transversaux liés aux des requêtes HTTP, notamment la mise en cache, la gestion des erreurs, la sérialisation et la journalisation.</span><span class="sxs-lookup"><span data-stu-id="7adf0-421">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="7adf0-422">Pour créer un gestionnaire, définissez une classe dérivant de <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="7adf0-422">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="7adf0-423">Remplacez la méthode `SendAsync` de façon à exécuter du code avant de passer la requête au gestionnaire suivant dans le pipeline :</span><span class="sxs-lookup"><span data-stu-id="7adf0-423">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="7adf0-424">Le code précédent définit un gestionnaire de base.</span><span class="sxs-lookup"><span data-stu-id="7adf0-424">The preceding code defines a basic handler.</span></span> <span data-ttu-id="7adf0-425">Il vérifie si un en-tête `X-API-KEY` a été inclus dans la requête.</span><span class="sxs-lookup"><span data-stu-id="7adf0-425">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="7adf0-426">Si l’en-tête est manquant, il peut éviter l’appel HTTP et retourner une réponse appropriée.</span><span class="sxs-lookup"><span data-stu-id="7adf0-426">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="7adf0-427">Au cours de l’inscription, un ou plusieurs gestionnaires peuvent être ajoutés à la configuration d’un `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="7adf0-427">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="7adf0-428">Cette tâche est accomplie via des méthodes d’extension sur le <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span><span class="sxs-lookup"><span data-stu-id="7adf0-428">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="7adf0-429">Dans le code précédent, le `ValidateHeaderHandler` est inscrit avec une injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="7adf0-429">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="7adf0-430">`IHttpClientFactory` crée une étendue DI distincte pour chaque gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="7adf0-430">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="7adf0-431">Les gestionnaires peuvent librement dépendre des services de n’importe quelle étendue.</span><span class="sxs-lookup"><span data-stu-id="7adf0-431">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="7adf0-432">Les services dont dépendent les gestionnaires sont supprimés lorsque le gestionnaire est supprimé.</span><span class="sxs-lookup"><span data-stu-id="7adf0-432">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="7adf0-433">Une fois inscrit, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> peut être appelé en passant en entrée le type pour le gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="7adf0-433">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="7adf0-434">Vous pouvez inscrire plusieurs gestionnaires dans l’ordre où ils doivent être exécutés.</span><span class="sxs-lookup"><span data-stu-id="7adf0-434">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="7adf0-435">Chaque gestionnaire wrappe le gestionnaire suivant jusqu’à ce que le dernier `HttpClientHandler` exécute la requête :</span><span class="sxs-lookup"><span data-stu-id="7adf0-435">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="7adf0-436">Utilisez l’une des approches suivantes pour partager l’état de chaque requête avec les gestionnaires de messages :</span><span class="sxs-lookup"><span data-stu-id="7adf0-436">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="7adf0-437">Passez des données dans le gestionnaire en utilisant `HttpRequestMessage.Properties`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-437">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="7adf0-438">Utilisez `IHttpContextAccessor` pour accéder à la requête en cours.</span><span class="sxs-lookup"><span data-stu-id="7adf0-438">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="7adf0-439">Créez un objet de stockage `AsyncLocal` personnalisé pour passer les données.</span><span class="sxs-lookup"><span data-stu-id="7adf0-439">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="7adf0-440">Utiliser les gestionnaires Polly</span><span class="sxs-lookup"><span data-stu-id="7adf0-440">Use Polly-based handlers</span></span>

<span data-ttu-id="7adf0-441">`IHttpClientFactory` s’intègre à une bibliothèque tierce très utilisée, appelée [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="7adf0-441">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="7adf0-442">Polly est une bibliothèque complète de gestion des erreurs transitoires et de résilience pour .NET.</span><span class="sxs-lookup"><span data-stu-id="7adf0-442">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="7adf0-443">Elle permet aux développeurs de formuler facilement et de façon thread-safe des stratégies, comme Retry (Nouvelle tentative), Circuit Breaker (Disjoncteur), Timeout (Délai d’attente), Bulkhead Isolation (Isolation par cloisonnement) et Fallback (Alternative de repli).</span><span class="sxs-lookup"><span data-stu-id="7adf0-443">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="7adf0-444">Des méthodes d’extension sont fournies pour permettre l’utilisation de stratégies Polly avec les instances configurées de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-444">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="7adf0-445">Les extensions Polly :</span><span class="sxs-lookup"><span data-stu-id="7adf0-445">The Polly extensions:</span></span>

* <span data-ttu-id="7adf0-446">Prennent en charge l’ajout de gestionnaires Polly à des clients.</span><span class="sxs-lookup"><span data-stu-id="7adf0-446">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="7adf0-447">Peuvent être utilisées après l’installation du package NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/).</span><span class="sxs-lookup"><span data-stu-id="7adf0-447">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="7adf0-448">Ce package n’est pas inclus dans le framework partagé ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7adf0-448">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="7adf0-449">Gérer les erreurs temporaires</span><span class="sxs-lookup"><span data-stu-id="7adf0-449">Handle transient faults</span></span>

<span data-ttu-id="7adf0-450">Les erreurs courantes se produisent lorsque des appels HTTP externes sont temporaires.</span><span class="sxs-lookup"><span data-stu-id="7adf0-450">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="7adf0-451">Une méthode d’extension pratique nommée `AddTransientHttpErrorPolicy` est incluse : elle permet de définir une stratégie pour gérer les erreurs temporaires.</span><span class="sxs-lookup"><span data-stu-id="7adf0-451">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="7adf0-452">Les stratégies configurées avec cette méthode d’extension gèrent `HttpRequestException`, les réponses HTTP 5xx et les réponses HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="7adf0-452">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="7adf0-453">L’extension `AddTransientHttpErrorPolicy` peut être utilisée dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-453">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7adf0-454">L’extension fournit l’accès à un objet `PolicyBuilder` configuré pour gérer les erreurs représentant une erreur temporaire possible :</span><span class="sxs-lookup"><span data-stu-id="7adf0-454">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="7adf0-455">Dans le code précédent, une stratégie `WaitAndRetryAsync` est définie.</span><span class="sxs-lookup"><span data-stu-id="7adf0-455">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="7adf0-456">Les requêtes qui ont échoué sont retentées jusqu’à trois fois avec un délai de 600 ms entre les tentatives.</span><span class="sxs-lookup"><span data-stu-id="7adf0-456">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="7adf0-457">Sélectionner dynamiquement des stratégies</span><span class="sxs-lookup"><span data-stu-id="7adf0-457">Dynamically select policies</span></span>

<span data-ttu-id="7adf0-458">Il existe d’autres méthodes d’extension que vous pouvez utiliser pour ajouter des gestionnaires Polly.</span><span class="sxs-lookup"><span data-stu-id="7adf0-458">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="7adf0-459">Une de ces extensions est `AddPolicyHandler`, qui a plusieurs surcharges.</span><span class="sxs-lookup"><span data-stu-id="7adf0-459">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="7adf0-460">Une de ces surcharges permet l’inspection de la requête lors de la définition de la stratégie à appliquer :</span><span class="sxs-lookup"><span data-stu-id="7adf0-460">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="7adf0-461">Dans le code précédent, si la requête sortante est un HTTP GET, un délai d’attente de 10 secondes est appliqué.</span><span class="sxs-lookup"><span data-stu-id="7adf0-461">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="7adf0-462">Pour toutes les autres méthodes HTTP, un délai d’attente de 30 secondes est utilisé.</span><span class="sxs-lookup"><span data-stu-id="7adf0-462">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="7adf0-463">Ajouter plusieurs gestionnaires Polly</span><span class="sxs-lookup"><span data-stu-id="7adf0-463">Add multiple Polly handlers</span></span>

<span data-ttu-id="7adf0-464">Il est courant d’imbriquer des stratégies Polly pour fournir des fonctionnalités améliorées :</span><span class="sxs-lookup"><span data-stu-id="7adf0-464">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="7adf0-465">Dans l’exemple précédent, deux gestionnaires sont ajoutés.</span><span class="sxs-lookup"><span data-stu-id="7adf0-465">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="7adf0-466">Le premier utilise l’extension `AddTransientHttpErrorPolicy` pour ajouter une stratégie de nouvelle tentative.</span><span class="sxs-lookup"><span data-stu-id="7adf0-466">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="7adf0-467">Les requêtes qui ont échoué sont retentées jusqu’à trois fois.</span><span class="sxs-lookup"><span data-stu-id="7adf0-467">Failed requests are retried up to three times.</span></span> <span data-ttu-id="7adf0-468">Le deuxième appel à `AddTransientHttpErrorPolicy` ajoute une stratégie de disjoncteur.</span><span class="sxs-lookup"><span data-stu-id="7adf0-468">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="7adf0-469">Les requêtes externes supplémentaires sont bloquées pendant 30 secondes si cinq tentatives successives échouent.</span><span class="sxs-lookup"><span data-stu-id="7adf0-469">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="7adf0-470">Les stratégies de disjoncteur sont avec état.</span><span class="sxs-lookup"><span data-stu-id="7adf0-470">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="7adf0-471">Tous les appels effectués via ce client partagent le même état du circuit.</span><span class="sxs-lookup"><span data-stu-id="7adf0-471">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="7adf0-472">Ajouter des stratégies à partir du Registre Polly</span><span class="sxs-lookup"><span data-stu-id="7adf0-472">Add policies from the Polly registry</span></span>

<span data-ttu-id="7adf0-473">Une approche de la gestion des stratégies régulièrement utilisées consiste à les définir une seule fois et à les inscrire avec un `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-473">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="7adf0-474">Il existe une méthode d’extension qui permet l’ajout d’un gestionnaire avec une stratégie du Registre :</span><span class="sxs-lookup"><span data-stu-id="7adf0-474">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="7adf0-475">Dans le code précédent, deux stratégies sont inscrites lorsque `PolicyRegistry` est ajouté à `ServiceCollection`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-475">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="7adf0-476">Pour utiliser une stratégie du Registre, la méthode `AddPolicyHandlerFromRegistry` est utilisée, en passant le nom de la stratégie à appliquer.</span><span class="sxs-lookup"><span data-stu-id="7adf0-476">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="7adf0-477">Vous trouverez plus d’informations sur les intégrations de `IHttpClientFactory` et de Polly dans le [wiki Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="7adf0-477">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="7adf0-478">HttpClient et gestion de la durée de vie</span><span class="sxs-lookup"><span data-stu-id="7adf0-478">HttpClient and lifetime management</span></span>

<span data-ttu-id="7adf0-479">Une nouvelle instance `HttpClient` est retournée à chaque fois que `CreateClient` est appelé sur `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-479">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="7adf0-480">Il existe un <xref:System.Net.Http.HttpMessageHandler> par client nommé.</span><span class="sxs-lookup"><span data-stu-id="7adf0-480">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="7adf0-481">La fabrique gère les durées de vie des instances `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-481">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="7adf0-482">`IHttpClientFactory` regroupe dans un pool les instances de `HttpMessageHandler` créées par la fabrique pour réduire la consommation des ressources.</span><span class="sxs-lookup"><span data-stu-id="7adf0-482">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="7adf0-483">Une instance de `HttpMessageHandler` peut être réutilisée à partir du pool quand vous créez une instance de `HttpClient` si sa durée de vie n’a pas expiré.</span><span class="sxs-lookup"><span data-stu-id="7adf0-483">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="7adf0-484">Le regroupement de gestionnaires en pools est souhaitable, car chaque gestionnaire gère habituellement ses propres connexions HTTP sous-jacentes.</span><span class="sxs-lookup"><span data-stu-id="7adf0-484">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="7adf0-485">La création de plus de gestionnaires que nécessaire peut entraîner des délais de connexion.</span><span class="sxs-lookup"><span data-stu-id="7adf0-485">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="7adf0-486">Certains gestionnaires conservent aussi les connexions indéfiniment ouvertes, ce qui peut empêcher le gestionnaire de réagir aux changements du DNS.</span><span class="sxs-lookup"><span data-stu-id="7adf0-486">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="7adf0-487">La durée de vie par défaut d’un gestionnaire est de deux minutes.</span><span class="sxs-lookup"><span data-stu-id="7adf0-487">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="7adf0-488">La valeur par défaut peut être remplacée pour chaque client nommé.</span><span class="sxs-lookup"><span data-stu-id="7adf0-488">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="7adf0-489">Pour la remplacer, appelez <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> sur le `IHttpClientBuilder` qui est retourné lors de la création du client :</span><span class="sxs-lookup"><span data-stu-id="7adf0-489">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="7adf0-490">La suppression du client n’est pas nécessaire.</span><span class="sxs-lookup"><span data-stu-id="7adf0-490">Disposal of the client isn't required.</span></span> <span data-ttu-id="7adf0-491">La suppression annule les requêtes sortantes et garantit que l’instance `HttpClient` donnée ne peut pas être utilisée après avoir appelé <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="7adf0-491">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="7adf0-492">`IHttpClientFactory` effectue le suivi et libère les ressources utilisées par les instances `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-492">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="7adf0-493">Les instances `HttpClient` peuvent généralement être traitées en tant qu’objets .NET ne nécessitant pas une suppression.</span><span class="sxs-lookup"><span data-stu-id="7adf0-493">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="7adf0-494">Le fait de conserver une seule instance `HttpClient` active pendant une longue durée est un modèle commun utilisé avant le lancement de `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-494">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="7adf0-495">Ce modèle devient inutile après la migration vers `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-495">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="7adf0-496">Alternatives à IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="7adf0-496">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="7adf0-497">L’utilisation `IHttpClientFactory` de dans une application di-Enabled évite les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="7adf0-497">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="7adf0-498">Problèmes d’épuisement des ressources par les instances de regroupement `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="7adf0-498">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="7adf0-499">Problèmes DNS périmés par les instances cycliques `HttpMessageHandler` à intervalles réguliers.</span><span class="sxs-lookup"><span data-stu-id="7adf0-499">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="7adf0-500">Il existe d’autres façons de résoudre les problèmes précédents à l’aide d’une instance de longue durée <xref:System.Net.Http.SocketsHttpHandler> .</span><span class="sxs-lookup"><span data-stu-id="7adf0-500">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="7adf0-501">Créez une instance de `SocketsHttpHandler` lorsque l’application démarre et utilisez-la pendant toute la durée de vie de l’application.</span><span class="sxs-lookup"><span data-stu-id="7adf0-501">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="7adf0-502">Configurez <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> avec une valeur appropriée en fonction des temps d’actualisation DNS.</span><span class="sxs-lookup"><span data-stu-id="7adf0-502">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="7adf0-503">Créez des `HttpClient` instances en utilisant `new HttpClient(handler, disposeHandler: false)` si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="7adf0-503">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="7adf0-504">Les approches précédentes résolvent les problèmes de gestion des ressources qui sont `IHttpClientFactory` résolus de la même façon.</span><span class="sxs-lookup"><span data-stu-id="7adf0-504">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="7adf0-505">Le `SocketsHttpHandler` partage les connexions entre les `HttpClient` instances.</span><span class="sxs-lookup"><span data-stu-id="7adf0-505">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="7adf0-506">Ce partage empêche l’épuisement des sockets.</span><span class="sxs-lookup"><span data-stu-id="7adf0-506">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="7adf0-507">Le `SocketsHttpHandler` cycle des connexions en fonction de `PooledConnectionLifetime` pour éviter les problèmes DNS périmés.</span><span class="sxs-lookup"><span data-stu-id="7adf0-507">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="no-loccookies"></a><span data-ttu-id="7adf0-508">Cookies</span><span class="sxs-lookup"><span data-stu-id="7adf0-508">Cookies</span></span>

<span data-ttu-id="7adf0-509">Les instances regroupées `HttpMessageHandler` entraînent le `CookieContainer` partage des objets.</span><span class="sxs-lookup"><span data-stu-id="7adf0-509">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="7adf0-510">Le `CookieContainer` partage d’objets imprévus aboutit souvent à un code incorrect.</span><span class="sxs-lookup"><span data-stu-id="7adf0-510">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="7adf0-511">Pour les applications qui nécessitent des cookie , envisagez l’une des deux opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="7adf0-511">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="7adf0-512">Désactivation de la cookie gestion automatique</span><span class="sxs-lookup"><span data-stu-id="7adf0-512">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="7adf0-513">Éviter `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="7adf0-513">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="7adf0-514">Appelez <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> pour désactiver la cookie gestion automatique :</span><span class="sxs-lookup"><span data-stu-id="7adf0-514">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="7adf0-515">Journalisation</span><span class="sxs-lookup"><span data-stu-id="7adf0-515">Logging</span></span>

<span data-ttu-id="7adf0-516">Les clients créés via `IHttpClientFactory` enregistrent les messages de journalisation pour toutes les requêtes.</span><span class="sxs-lookup"><span data-stu-id="7adf0-516">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="7adf0-517">Activez le niveau d’informations approprié dans votre configuration de journalisation pour voir les messages de journalisation par défaut.</span><span class="sxs-lookup"><span data-stu-id="7adf0-517">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="7adf0-518">Une journalisation supplémentaire, comme celle des en-têtes des requêtes, est incluse seulement au niveau de trace.</span><span class="sxs-lookup"><span data-stu-id="7adf0-518">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="7adf0-519">La catégorie de journal utilisée pour chaque client comprend le nom du client.</span><span class="sxs-lookup"><span data-stu-id="7adf0-519">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="7adf0-520">Par exemple, un client nommé *MyNamedClient* journalise les messages avec la catégorie `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-520">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="7adf0-521">Les messages avec le suffixe *LogicalHandler* se produisent en dehors du pipeline du gestionnaire de requêtes.</span><span class="sxs-lookup"><span data-stu-id="7adf0-521">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="7adf0-522">Lors d’une requête, les messages sont journalisés avant que d’autres gestionnaires du pipeline l’aient traitée.</span><span class="sxs-lookup"><span data-stu-id="7adf0-522">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="7adf0-523">Lors d’une réponse, les messages sont journalisés une fois que tous les autres gestionnaires du pipeline ont reçu la réponse.</span><span class="sxs-lookup"><span data-stu-id="7adf0-523">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="7adf0-524">La journalisation se produit également à l’intérieur du pipeline du gestionnaire de requêtes.</span><span class="sxs-lookup"><span data-stu-id="7adf0-524">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="7adf0-525">Dans l’exemple *MyNamedClient*, ces messages sont journalisés avec la catégorie de journalisation `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-525">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="7adf0-526">Pour la requête, cela se produit après que tous les autres gestionnaires ont été exécutés et immédiatement avant l’envoi de la requête sur le réseau.</span><span class="sxs-lookup"><span data-stu-id="7adf0-526">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="7adf0-527">Lors de la réponse, cette journalisation inclut l’état de la réponse avant qu’elle repasse à travers le pipeline de gestionnaires.</span><span class="sxs-lookup"><span data-stu-id="7adf0-527">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="7adf0-528">L’activation de la journalisation à l’extérieur et à l’intérieur du pipeline permet l’inspection des changements apportés par les autres gestionnaires du pipeline.</span><span class="sxs-lookup"><span data-stu-id="7adf0-528">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="7adf0-529">Par exemple, cela peut comprendre des changements apportés aux en-têtes des requêtes ou au code d’état de la réponse.</span><span class="sxs-lookup"><span data-stu-id="7adf0-529">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="7adf0-530">L’ajout du nom du client dans la catégorie de journalisation permet si nécessaire de filtrer le journal pour des clients nommés spécifiques.</span><span class="sxs-lookup"><span data-stu-id="7adf0-530">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="7adf0-531">Configurer le HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="7adf0-531">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="7adf0-532">Il peut être nécessaire de contrôler la configuration du `HttpMessageHandler` interne utilisé par un client.</span><span class="sxs-lookup"><span data-stu-id="7adf0-532">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="7adf0-533">Un `IHttpClientBuilder` est retourné quand vous ajoutez des clients nommés ou typés.</span><span class="sxs-lookup"><span data-stu-id="7adf0-533">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="7adf0-534">La méthode d’extension <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> peut être utilisée pour définir un délégué.</span><span class="sxs-lookup"><span data-stu-id="7adf0-534">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="7adf0-535">Le délégué est utilisé pour créer et configurer le `HttpMessageHandler` principal utilisé par ce client :</span><span class="sxs-lookup"><span data-stu-id="7adf0-535">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="7adf0-536">Utiliser IHttpClientFactory dans une application console</span><span class="sxs-lookup"><span data-stu-id="7adf0-536">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="7adf0-537">Dans une application console, ajoutez les références de package suivantes au projet :</span><span class="sxs-lookup"><span data-stu-id="7adf0-537">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="7adf0-538">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="7adf0-538">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="7adf0-539">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="7adf0-539">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="7adf0-540">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="7adf0-540">In the following example:</span></span>

* <span data-ttu-id="7adf0-541"><xref:System.Net.Http.IHttpClientFactory> est inscrit dans le conteneur de service dans de l’[hôte générique](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="7adf0-541"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="7adf0-542">`MyService` crée une instance de fabrique cliente à partir du service, qui est utilisée pour créer un `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-542">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="7adf0-543">`HttpClient` est utilisé pour récupérer une page web.</span><span class="sxs-lookup"><span data-stu-id="7adf0-543">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="7adf0-544">`Main` crée une étendue pour exécuter la méthode `GetPage` du service et écrire les 500 premiers caractères du contenu de la page web dans la console.</span><span class="sxs-lookup"><span data-stu-id="7adf0-544">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="7adf0-545">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="7adf0-545">Additional resources</span></span>

* [<span data-ttu-id="7adf0-546">Utilisez HttpClientFactory pour implémenter des requêtes HTTP résilientes</span><span class="sxs-lookup"><span data-stu-id="7adf0-546">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="7adf0-547">Implémenter de nouvelles tentatives d’appel HTTP avec interruption exponentielle avec des stratégies Polly et HttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="7adf0-547">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="7adf0-548">Implémenter le modèle Disjoncteur</span><span class="sxs-lookup"><span data-stu-id="7adf0-548">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="7adf0-549">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak) et [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="7adf0-549">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="7adf0-550">Une <xref:System.Net.Http.IHttpClientFactory> peut être inscrite et utilisée pour configurer et créer des instances de <xref:System.Net.Http.HttpClient> dans une application.</span><span class="sxs-lookup"><span data-stu-id="7adf0-550">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="7adf0-551">Elle offre les avantages suivants :</span><span class="sxs-lookup"><span data-stu-id="7adf0-551">It offers the following benefits:</span></span>

* <span data-ttu-id="7adf0-552">Fournit un emplacement central pour le nommage et la configuration d’instance de `HttpClient` logiques.</span><span class="sxs-lookup"><span data-stu-id="7adf0-552">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="7adf0-553">Par exemple, un client *github* peut être inscrit et configuré pour accéder à [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="7adf0-553">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="7adf0-554">Un client par défaut peut être inscrit à d’autres fins.</span><span class="sxs-lookup"><span data-stu-id="7adf0-554">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="7adf0-555">Codifie le concept de middleware (intergiciel) sortant via la délégation de gestionnaires dans `HttpClient` et fournit des extensions permettant au middleware Polly d’en tirer parti.</span><span class="sxs-lookup"><span data-stu-id="7adf0-555">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="7adf0-556">Gère le regroupement et la durée de vie des instances de `HttpClientMessageHandler` sous-jacentes pour éviter les problèmes DNS courants qui se produisent lors de la gestion manuelle des durées de vie de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-556">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="7adf0-557">Ajoute une expérience de journalisation configurable (via `ILogger`) pour toutes les requêtes envoyées via des clients créés par la fabrique.</span><span class="sxs-lookup"><span data-stu-id="7adf0-557">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="7adf0-558">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7adf0-558">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7adf0-559">Prérequis</span><span class="sxs-lookup"><span data-stu-id="7adf0-559">Prerequisites</span></span>

<span data-ttu-id="7adf0-560">Les projets ciblant .NET Framework nécessitent l’installation du package NuGet [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/).</span><span class="sxs-lookup"><span data-stu-id="7adf0-560">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="7adf0-561">Les projets qui ciblent .NET Core et référencent le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) incluent déjà le package `Microsoft.Extensions.Http`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-561">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="7adf0-562">Modèles de consommation</span><span class="sxs-lookup"><span data-stu-id="7adf0-562">Consumption patterns</span></span>

<span data-ttu-id="7adf0-563">Vous pouvez utiliser `IHttpClientFactory` dans une application de plusieurs façons :</span><span class="sxs-lookup"><span data-stu-id="7adf0-563">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="7adf0-564">Utilisation de base</span><span class="sxs-lookup"><span data-stu-id="7adf0-564">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="7adf0-565">Clients nommés</span><span class="sxs-lookup"><span data-stu-id="7adf0-565">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="7adf0-566">Clients typés</span><span class="sxs-lookup"><span data-stu-id="7adf0-566">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="7adf0-567">Clients générés</span><span class="sxs-lookup"><span data-stu-id="7adf0-567">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="7adf0-568">Aucune d’entre elles n’est meilleure qu’une autre.</span><span class="sxs-lookup"><span data-stu-id="7adf0-568">None of them are strictly superior to another.</span></span> <span data-ttu-id="7adf0-569">La meilleure approche dépend des contraintes de l’application.</span><span class="sxs-lookup"><span data-stu-id="7adf0-569">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="7adf0-570">Utilisation de base</span><span class="sxs-lookup"><span data-stu-id="7adf0-570">Basic usage</span></span>

<span data-ttu-id="7adf0-571">Vous pouvez inscrire la `IHttpClientFactory` en appelant la méthode d’extension `AddHttpClient` sur la `IServiceCollection`, à l’intérieur la méthode `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-571">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="7adf0-572">Une fois inscrit, le code peut accepter un `IHttpClientFactory` partout où des services peuvent être injectés avec [une injection de dépendance](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="7adf0-572">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="7adf0-573">Le `IHttpClientFactory` peut être utilisé pour créer une `HttpClient` instance de :</span><span class="sxs-lookup"><span data-stu-id="7adf0-573">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="7adf0-574">L’utilisation de `IHttpClientFactory` de cette façon est un excellent moyen de refactoriser une application existante.</span><span class="sxs-lookup"><span data-stu-id="7adf0-574">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="7adf0-575">Elle n’a aucun impact sur la façon dont `HttpClient` est utilisé.</span><span class="sxs-lookup"><span data-stu-id="7adf0-575">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="7adf0-576">Dans les endroits où les instances de `HttpClient` sont actuellement créées, remplacez ces occurrences par un appel à <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="7adf0-576">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="7adf0-577">Clients nommés</span><span class="sxs-lookup"><span data-stu-id="7adf0-577">Named clients</span></span>

<span data-ttu-id="7adf0-578">Si une application nécessite plusieurs utilisations distinctes de `HttpClient`, chacune avec une configuration différente, une option consiste à utiliser des **clients nommés**.</span><span class="sxs-lookup"><span data-stu-id="7adf0-578">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="7adf0-579">La configuration d’un `HttpClient` nommé peut être spécifiée lors de l’inscription dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-579">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="7adf0-580">Dans le code précédent, `AddHttpClient` est appelé en fournissant le nom *github*.</span><span class="sxs-lookup"><span data-stu-id="7adf0-580">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="7adf0-581">Une configuration par défaut est appliquée à ce client : l’adresse de base et deux en-têtes nécessaires pour utiliser l’API GitHub.</span><span class="sxs-lookup"><span data-stu-id="7adf0-581">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="7adf0-582">Chaque fois que `CreateClient` est appelée, une nouvelle instance de `HttpClient` est créée et l’action de configuration est appelée.</span><span class="sxs-lookup"><span data-stu-id="7adf0-582">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="7adf0-583">Pour utiliser un client nommé, un paramètre de chaîne peut être passé à `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-583">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="7adf0-584">Spécifiez le nom du client à créer :</span><span class="sxs-lookup"><span data-stu-id="7adf0-584">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="7adf0-585">Dans le code précédent, la requête n’a pas besoin de spécifier un nom d’hôte.</span><span class="sxs-lookup"><span data-stu-id="7adf0-585">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="7adf0-586">Elle peut simplement passer le chemin, car l’adresse de base configurée pour le client est utilisée.</span><span class="sxs-lookup"><span data-stu-id="7adf0-586">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="7adf0-587">Clients typés</span><span class="sxs-lookup"><span data-stu-id="7adf0-587">Typed clients</span></span>

<span data-ttu-id="7adf0-588">Clients typés :</span><span class="sxs-lookup"><span data-stu-id="7adf0-588">Typed clients:</span></span>

* <span data-ttu-id="7adf0-589">Fournissent les mêmes fonctionnalités que les clients nommés, sans qu’il soit nécessaire d’utiliser des chaînes comme clés.</span><span class="sxs-lookup"><span data-stu-id="7adf0-589">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="7adf0-590">Bénéficie de l’aide d’IntelliSense et du compilateur lors de l’utilisation des clients.</span><span class="sxs-lookup"><span data-stu-id="7adf0-590">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="7adf0-591">Fournissent un emplacement unique pour configurer et interagir avec un `HttpClient` particulier.</span><span class="sxs-lookup"><span data-stu-id="7adf0-591">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="7adf0-592">Par exemple, un même client typé peut être utilisé pour un point de terminaison de backend et pour encapsuler la logique relative à ce point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="7adf0-592">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="7adf0-593">Fonctionnent avec l’injection de dépendances et peuvent être injectés là où c’est nécessaire dans votre application.</span><span class="sxs-lookup"><span data-stu-id="7adf0-593">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="7adf0-594">Un client typé accepte un `HttpClient` paramètre dans son constructeur :</span><span class="sxs-lookup"><span data-stu-id="7adf0-594">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="7adf0-595">Dans le code précédent, la configuration est déplacée dans le client typé.</span><span class="sxs-lookup"><span data-stu-id="7adf0-595">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="7adf0-596">L’objet `HttpClient` est exposé en tant que propriété publique.</span><span class="sxs-lookup"><span data-stu-id="7adf0-596">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="7adf0-597">Il est possible de définir des méthodes d’API spécifiques qui exposent les fonctionnalités de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-597">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="7adf0-598">La méthode `GetAspNetDocsIssues` encapsule le code nécessaire pour interroger et analyser les problèmes ouverts les plus récents d’un dépôt GitHub.</span><span class="sxs-lookup"><span data-stu-id="7adf0-598">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="7adf0-599">Pour inscrire un client typé, la méthode d’extension <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> générique peut être utilisée dans `Startup.ConfigureServices`, en spécifiant la classe du client typé :</span><span class="sxs-lookup"><span data-stu-id="7adf0-599">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="7adf0-600">Le client typé est inscrit comme étant transitoire avec injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="7adf0-600">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="7adf0-601">Le client typé peut être injecté et utilisé directement :</span><span class="sxs-lookup"><span data-stu-id="7adf0-601">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="7adf0-602">Si vous préférez, vous pouvez spécifier la configuration d’un client typé lors de l’inscription dans `Startup.ConfigureServices` au lieu de le faire dans le constructeur du client typé :</span><span class="sxs-lookup"><span data-stu-id="7adf0-602">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="7adf0-603">Il est possible d’encapsuler entièrement le `HttpClient` dans un client typé.</span><span class="sxs-lookup"><span data-stu-id="7adf0-603">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="7adf0-604">Au lieu de l’exposer en tant que propriété, vous pouvez fournir des méthodes publiques qui appellent l’instance de `HttpClient` en interne.</span><span class="sxs-lookup"><span data-stu-id="7adf0-604">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="7adf0-605">Dans le code précédent, le `HttpClient` est stocké en tant que champ privé.</span><span class="sxs-lookup"><span data-stu-id="7adf0-605">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="7adf0-606">Tous les accès nécessaires pour effectuer des appels externes passent par la méthode `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-606">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="7adf0-607">Clients générés</span><span class="sxs-lookup"><span data-stu-id="7adf0-607">Generated clients</span></span>

<span data-ttu-id="7adf0-608">`IHttpClientFactory` peut être utilisé en combinaison avec d’autres bibliothèques tierces, comme [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="7adf0-608">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="7adf0-609">Refit est une bibliothèque REST pour .NET.</span><span class="sxs-lookup"><span data-stu-id="7adf0-609">Refit is a REST library for .NET.</span></span> <span data-ttu-id="7adf0-610">Il convertit les API REST en interfaces dynamiques.</span><span class="sxs-lookup"><span data-stu-id="7adf0-610">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="7adf0-611">Une implémentation de l’interface est générée dynamiquement par le `RestService`, avec `HttpClient` pour faire les appels HTTP externes.</span><span class="sxs-lookup"><span data-stu-id="7adf0-611">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="7adf0-612">Une interface et une réponse sont définies pour représenter l’API externe et sa réponse :</span><span class="sxs-lookup"><span data-stu-id="7adf0-612">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="7adf0-613">Vous pouvez ajouter un client typé en utilisant Refit pour générer l’implémentation :</span><span class="sxs-lookup"><span data-stu-id="7adf0-613">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="7adf0-614">L’interface définie peut être utilisée quand c’est nécessaire, avec l’implémentation fournie par l’injection de dépendances et Refit :</span><span class="sxs-lookup"><span data-stu-id="7adf0-614">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="7adf0-615">Middleware pour les requêtes sortantes</span><span class="sxs-lookup"><span data-stu-id="7adf0-615">Outgoing request middleware</span></span>

<span data-ttu-id="7adf0-616">`HttpClient` intègre déjà le concept de délégation des gestionnaires qui peuvent être liés ensemble pour les requêtes HTTP sortantes.</span><span class="sxs-lookup"><span data-stu-id="7adf0-616">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="7adf0-617">Le `IHttpClientFactory` permet de définir facilement les gestionnaires à appliquer pour chaque client nommé.</span><span class="sxs-lookup"><span data-stu-id="7adf0-617">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="7adf0-618">Il prend en charge l’inscription et le chaînage de plusieurs gestionnaires pour créer un pipeline de middlewares pour les requêtes sortantes.</span><span class="sxs-lookup"><span data-stu-id="7adf0-618">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="7adf0-619">Chacun de ces gestionnaires peut effectuer un travail avant et après la requête sortante.</span><span class="sxs-lookup"><span data-stu-id="7adf0-619">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="7adf0-620">Ce modèle est similaire au pipeline de middlewares entrants dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7adf0-620">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="7adf0-621">Le modèle fournit un mécanisme permettant de gérer les problèmes transversaux liés aux des requêtes HTTP, notamment la mise en cache, la gestion des erreurs, la sérialisation et la journalisation.</span><span class="sxs-lookup"><span data-stu-id="7adf0-621">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="7adf0-622">Pour créer un gestionnaire, définissez une classe dérivant de <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="7adf0-622">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="7adf0-623">Remplacez la méthode `SendAsync` de façon à exécuter du code avant de passer la requête au gestionnaire suivant dans le pipeline :</span><span class="sxs-lookup"><span data-stu-id="7adf0-623">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="7adf0-624">Le code précédent définit un gestionnaire de base.</span><span class="sxs-lookup"><span data-stu-id="7adf0-624">The preceding code defines a basic handler.</span></span> <span data-ttu-id="7adf0-625">Il vérifie si un en-tête `X-API-KEY` a été inclus dans la requête.</span><span class="sxs-lookup"><span data-stu-id="7adf0-625">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="7adf0-626">Si l’en-tête est manquant, il peut éviter l’appel HTTP et retourner une réponse appropriée.</span><span class="sxs-lookup"><span data-stu-id="7adf0-626">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="7adf0-627">Au cours de l’inscription, un ou plusieurs gestionnaires peuvent être ajoutés à la configuration d’un `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="7adf0-627">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="7adf0-628">Cette tâche est accomplie via des méthodes d’extension sur le <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span><span class="sxs-lookup"><span data-stu-id="7adf0-628">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="7adf0-629">Dans le code précédent, le `ValidateHeaderHandler` est inscrit avec une injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="7adf0-629">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="7adf0-630">Le gestionnaire **doit** être inscrit dans l’injection de dépendances en tant que service temporaire, sans étendue.</span><span class="sxs-lookup"><span data-stu-id="7adf0-630">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="7adf0-631">Si le gestionnaire est inscrit en tant que service étendu et que des services dont dépend le gestionnaire peuvent être supprimés :</span><span class="sxs-lookup"><span data-stu-id="7adf0-631">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="7adf0-632">Les services du gestionnaire peuvent être supprimés avant que le gestionnaire ne soit hors de portée.</span><span class="sxs-lookup"><span data-stu-id="7adf0-632">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="7adf0-633">Les services du gestionnaire supprimés entraînent un échec du gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="7adf0-633">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="7adf0-634">Une fois inscrit, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> peut être appelé en passant en entrée le type du gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="7adf0-634">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="7adf0-635">Vous pouvez inscrire plusieurs gestionnaires dans l’ordre où ils doivent être exécutés.</span><span class="sxs-lookup"><span data-stu-id="7adf0-635">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="7adf0-636">Chaque gestionnaire wrappe le gestionnaire suivant jusqu’à ce que le dernier `HttpClientHandler` exécute la requête :</span><span class="sxs-lookup"><span data-stu-id="7adf0-636">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="7adf0-637">Utilisez l’une des approches suivantes pour partager l’état de chaque requête avec les gestionnaires de messages :</span><span class="sxs-lookup"><span data-stu-id="7adf0-637">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="7adf0-638">Passez des données dans le gestionnaire en utilisant `HttpRequestMessage.Properties`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-638">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="7adf0-639">Utilisez `IHttpContextAccessor` pour accéder à la requête en cours.</span><span class="sxs-lookup"><span data-stu-id="7adf0-639">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="7adf0-640">Créez un objet de stockage `AsyncLocal` personnalisé pour passer les données.</span><span class="sxs-lookup"><span data-stu-id="7adf0-640">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="7adf0-641">Utiliser les gestionnaires Polly</span><span class="sxs-lookup"><span data-stu-id="7adf0-641">Use Polly-based handlers</span></span>

<span data-ttu-id="7adf0-642">`IHttpClientFactory` s’intègre à une bibliothèque tierce très utilisée, appelée [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="7adf0-642">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="7adf0-643">Polly est une bibliothèque complète de gestion des erreurs transitoires et de résilience pour .NET.</span><span class="sxs-lookup"><span data-stu-id="7adf0-643">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="7adf0-644">Elle permet aux développeurs de formuler facilement et de façon thread-safe des stratégies, comme Retry (Nouvelle tentative), Circuit Breaker (Disjoncteur), Timeout (Délai d’attente), Bulkhead Isolation (Isolation par cloisonnement) et Fallback (Alternative de repli).</span><span class="sxs-lookup"><span data-stu-id="7adf0-644">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="7adf0-645">Des méthodes d’extension sont fournies pour permettre l’utilisation de stratégies Polly avec les instances configurées de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-645">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="7adf0-646">Les extensions Polly :</span><span class="sxs-lookup"><span data-stu-id="7adf0-646">The Polly extensions:</span></span>

* <span data-ttu-id="7adf0-647">Prennent en charge l’ajout de gestionnaires Polly à des clients.</span><span class="sxs-lookup"><span data-stu-id="7adf0-647">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="7adf0-648">Peuvent être utilisées après l’installation du package NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/).</span><span class="sxs-lookup"><span data-stu-id="7adf0-648">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="7adf0-649">Ce package n’est pas inclus dans le framework partagé ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7adf0-649">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="7adf0-650">Gérer les erreurs temporaires</span><span class="sxs-lookup"><span data-stu-id="7adf0-650">Handle transient faults</span></span>

<span data-ttu-id="7adf0-651">Les erreurs courantes se produisent lorsque des appels HTTP externes sont temporaires.</span><span class="sxs-lookup"><span data-stu-id="7adf0-651">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="7adf0-652">Une méthode d’extension pratique nommée `AddTransientHttpErrorPolicy` est incluse : elle permet de définir une stratégie pour gérer les erreurs temporaires.</span><span class="sxs-lookup"><span data-stu-id="7adf0-652">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="7adf0-653">Les stratégies configurées avec cette méthode d’extension gèrent `HttpRequestException`, les réponses HTTP 5xx et les réponses HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="7adf0-653">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="7adf0-654">L’extension `AddTransientHttpErrorPolicy` peut être utilisée dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-654">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7adf0-655">L’extension fournit l’accès à un objet `PolicyBuilder` configuré pour gérer les erreurs représentant une erreur temporaire possible :</span><span class="sxs-lookup"><span data-stu-id="7adf0-655">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="7adf0-656">Dans le code précédent, une stratégie `WaitAndRetryAsync` est définie.</span><span class="sxs-lookup"><span data-stu-id="7adf0-656">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="7adf0-657">Les requêtes qui ont échoué sont retentées jusqu’à trois fois avec un délai de 600 ms entre les tentatives.</span><span class="sxs-lookup"><span data-stu-id="7adf0-657">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="7adf0-658">Sélectionner dynamiquement des stratégies</span><span class="sxs-lookup"><span data-stu-id="7adf0-658">Dynamically select policies</span></span>

<span data-ttu-id="7adf0-659">Il existe d’autres méthodes d’extension que vous pouvez utiliser pour ajouter des gestionnaires Polly.</span><span class="sxs-lookup"><span data-stu-id="7adf0-659">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="7adf0-660">Une de ces extensions est `AddPolicyHandler`, qui a plusieurs surcharges.</span><span class="sxs-lookup"><span data-stu-id="7adf0-660">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="7adf0-661">Une de ces surcharges permet l’inspection de la requête lors de la définition de la stratégie à appliquer :</span><span class="sxs-lookup"><span data-stu-id="7adf0-661">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="7adf0-662">Dans le code précédent, si la requête sortante est un HTTP GET, un délai d’attente de 10 secondes est appliqué.</span><span class="sxs-lookup"><span data-stu-id="7adf0-662">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="7adf0-663">Pour toutes les autres méthodes HTTP, un délai d’attente de 30 secondes est utilisé.</span><span class="sxs-lookup"><span data-stu-id="7adf0-663">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="7adf0-664">Ajouter plusieurs gestionnaires Polly</span><span class="sxs-lookup"><span data-stu-id="7adf0-664">Add multiple Polly handlers</span></span>

<span data-ttu-id="7adf0-665">Il est courant d’imbriquer des stratégies Polly pour fournir des fonctionnalités améliorées :</span><span class="sxs-lookup"><span data-stu-id="7adf0-665">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="7adf0-666">Dans l’exemple précédent, deux gestionnaires sont ajoutés.</span><span class="sxs-lookup"><span data-stu-id="7adf0-666">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="7adf0-667">Le premier utilise l’extension `AddTransientHttpErrorPolicy` pour ajouter une stratégie de nouvelle tentative.</span><span class="sxs-lookup"><span data-stu-id="7adf0-667">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="7adf0-668">Les requêtes qui ont échoué sont retentées jusqu’à trois fois.</span><span class="sxs-lookup"><span data-stu-id="7adf0-668">Failed requests are retried up to three times.</span></span> <span data-ttu-id="7adf0-669">Le deuxième appel à `AddTransientHttpErrorPolicy` ajoute une stratégie de disjoncteur.</span><span class="sxs-lookup"><span data-stu-id="7adf0-669">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="7adf0-670">Les requêtes externes supplémentaires sont bloquées pendant 30 secondes si cinq tentatives successives échouent.</span><span class="sxs-lookup"><span data-stu-id="7adf0-670">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="7adf0-671">Les stratégies de disjoncteur sont avec état.</span><span class="sxs-lookup"><span data-stu-id="7adf0-671">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="7adf0-672">Tous les appels effectués via ce client partagent le même état du circuit.</span><span class="sxs-lookup"><span data-stu-id="7adf0-672">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="7adf0-673">Ajouter des stratégies à partir du Registre Polly</span><span class="sxs-lookup"><span data-stu-id="7adf0-673">Add policies from the Polly registry</span></span>

<span data-ttu-id="7adf0-674">Une approche de la gestion des stratégies régulièrement utilisées consiste à les définir une seule fois et à les inscrire avec un `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-674">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="7adf0-675">Il existe une méthode d’extension qui permet l’ajout d’un gestionnaire avec une stratégie du Registre :</span><span class="sxs-lookup"><span data-stu-id="7adf0-675">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="7adf0-676">Dans le code précédent, deux stratégies sont inscrites lorsque `PolicyRegistry` est ajouté à `ServiceCollection`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-676">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="7adf0-677">Pour utiliser une stratégie du Registre, la méthode `AddPolicyHandlerFromRegistry` est utilisée, en passant le nom de la stratégie à appliquer.</span><span class="sxs-lookup"><span data-stu-id="7adf0-677">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="7adf0-678">Vous trouverez plus d’informations sur les intégrations de `IHttpClientFactory` et de Polly dans le [wiki Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="7adf0-678">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="7adf0-679">HttpClient et gestion de la durée de vie</span><span class="sxs-lookup"><span data-stu-id="7adf0-679">HttpClient and lifetime management</span></span>

<span data-ttu-id="7adf0-680">Une nouvelle instance `HttpClient` est retournée à chaque fois que `CreateClient` est appelé sur `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-680">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="7adf0-681">Il existe un <xref:System.Net.Http.HttpMessageHandler> par client nommé.</span><span class="sxs-lookup"><span data-stu-id="7adf0-681">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="7adf0-682">La fabrique gère les durées de vie des instances `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-682">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="7adf0-683">`IHttpClientFactory` regroupe dans un pool les instances de `HttpMessageHandler` créées par la fabrique pour réduire la consommation des ressources.</span><span class="sxs-lookup"><span data-stu-id="7adf0-683">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="7adf0-684">Une instance de `HttpMessageHandler` peut être réutilisée à partir du pool quand vous créez une instance de `HttpClient` si sa durée de vie n’a pas expiré.</span><span class="sxs-lookup"><span data-stu-id="7adf0-684">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="7adf0-685">Le regroupement de gestionnaires en pools est souhaitable, car chaque gestionnaire gère habituellement ses propres connexions HTTP sous-jacentes.</span><span class="sxs-lookup"><span data-stu-id="7adf0-685">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="7adf0-686">La création de plus de gestionnaires que nécessaire peut entraîner des délais de connexion.</span><span class="sxs-lookup"><span data-stu-id="7adf0-686">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="7adf0-687">Certains gestionnaires conservent aussi les connexions indéfiniment ouvertes, ce qui peut empêcher le gestionnaire de réagir aux changements du DNS.</span><span class="sxs-lookup"><span data-stu-id="7adf0-687">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="7adf0-688">La durée de vie par défaut d’un gestionnaire est de deux minutes.</span><span class="sxs-lookup"><span data-stu-id="7adf0-688">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="7adf0-689">La valeur par défaut peut être remplacée pour chaque client nommé.</span><span class="sxs-lookup"><span data-stu-id="7adf0-689">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="7adf0-690">Pour la remplacer, appelez <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> sur le `IHttpClientBuilder` qui est retourné lors de la création du client :</span><span class="sxs-lookup"><span data-stu-id="7adf0-690">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="7adf0-691">La suppression du client n’est pas nécessaire.</span><span class="sxs-lookup"><span data-stu-id="7adf0-691">Disposal of the client isn't required.</span></span> <span data-ttu-id="7adf0-692">La suppression annule les requêtes sortantes et garantit que l’instance `HttpClient` donnée ne peut pas être utilisée après avoir appelé <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="7adf0-692">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="7adf0-693">`IHttpClientFactory` effectue le suivi et libère les ressources utilisées par les instances `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-693">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="7adf0-694">Les instances `HttpClient` peuvent généralement être traitées en tant qu’objets .NET ne nécessitant pas une suppression.</span><span class="sxs-lookup"><span data-stu-id="7adf0-694">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="7adf0-695">Le fait de conserver une seule instance `HttpClient` active pendant une longue durée est un modèle commun utilisé avant le lancement de `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-695">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="7adf0-696">Ce modèle devient inutile après la migration vers `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-696">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="7adf0-697">Alternatives à IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="7adf0-697">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="7adf0-698">L’utilisation `IHttpClientFactory` de dans une application di-Enabled évite les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="7adf0-698">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="7adf0-699">Problèmes d’épuisement des ressources par les instances de regroupement `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="7adf0-699">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="7adf0-700">Problèmes DNS périmés par les instances cycliques `HttpMessageHandler` à intervalles réguliers.</span><span class="sxs-lookup"><span data-stu-id="7adf0-700">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="7adf0-701">Il existe d’autres façons de résoudre les problèmes précédents à l’aide d’une instance de longue durée <xref:System.Net.Http.SocketsHttpHandler> .</span><span class="sxs-lookup"><span data-stu-id="7adf0-701">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="7adf0-702">Créez une instance de `SocketsHttpHandler` lorsque l’application démarre et utilisez-la pendant toute la durée de vie de l’application.</span><span class="sxs-lookup"><span data-stu-id="7adf0-702">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="7adf0-703">Configurez <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> avec une valeur appropriée en fonction des temps d’actualisation DNS.</span><span class="sxs-lookup"><span data-stu-id="7adf0-703">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="7adf0-704">Créez des `HttpClient` instances en utilisant `new HttpClient(handler, disposeHandler: false)` si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="7adf0-704">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="7adf0-705">Les approches précédentes résolvent les problèmes de gestion des ressources qui sont `IHttpClientFactory` résolus de la même façon.</span><span class="sxs-lookup"><span data-stu-id="7adf0-705">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="7adf0-706">Le `SocketsHttpHandler` partage les connexions entre les `HttpClient` instances.</span><span class="sxs-lookup"><span data-stu-id="7adf0-706">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="7adf0-707">Ce partage empêche l’épuisement des sockets.</span><span class="sxs-lookup"><span data-stu-id="7adf0-707">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="7adf0-708">Le `SocketsHttpHandler` cycle des connexions en fonction de `PooledConnectionLifetime` pour éviter les problèmes DNS périmés.</span><span class="sxs-lookup"><span data-stu-id="7adf0-708">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="no-loccookies"></a><span data-ttu-id="7adf0-709">Cookies</span><span class="sxs-lookup"><span data-stu-id="7adf0-709">Cookies</span></span>

<span data-ttu-id="7adf0-710">Les instances regroupées `HttpMessageHandler` entraînent le `CookieContainer` partage des objets.</span><span class="sxs-lookup"><span data-stu-id="7adf0-710">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="7adf0-711">Le `CookieContainer` partage d’objets imprévus aboutit souvent à un code incorrect.</span><span class="sxs-lookup"><span data-stu-id="7adf0-711">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="7adf0-712">Pour les applications qui nécessitent des cookie , envisagez l’une des deux opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="7adf0-712">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="7adf0-713">Désactivation de la cookie gestion automatique</span><span class="sxs-lookup"><span data-stu-id="7adf0-713">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="7adf0-714">Éviter `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="7adf0-714">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="7adf0-715">Appelez <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> pour désactiver la cookie gestion automatique :</span><span class="sxs-lookup"><span data-stu-id="7adf0-715">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="7adf0-716">Journalisation</span><span class="sxs-lookup"><span data-stu-id="7adf0-716">Logging</span></span>

<span data-ttu-id="7adf0-717">Les clients créés via `IHttpClientFactory` enregistrent les messages de journalisation pour toutes les requêtes.</span><span class="sxs-lookup"><span data-stu-id="7adf0-717">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="7adf0-718">Activez le niveau d’informations approprié dans votre configuration de journalisation pour voir les messages de journalisation par défaut.</span><span class="sxs-lookup"><span data-stu-id="7adf0-718">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="7adf0-719">Une journalisation supplémentaire, comme celle des en-têtes des requêtes, est incluse seulement au niveau de trace.</span><span class="sxs-lookup"><span data-stu-id="7adf0-719">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="7adf0-720">La catégorie de journal utilisée pour chaque client comprend le nom du client.</span><span class="sxs-lookup"><span data-stu-id="7adf0-720">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="7adf0-721">Par exemple, un client nommé *MyNamedClient* journalise les messages avec la catégorie `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-721">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="7adf0-722">Les messages avec le suffixe *LogicalHandler* se produisent en dehors du pipeline du gestionnaire de requêtes.</span><span class="sxs-lookup"><span data-stu-id="7adf0-722">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="7adf0-723">Lors d’une requête, les messages sont journalisés avant que d’autres gestionnaires du pipeline l’aient traitée.</span><span class="sxs-lookup"><span data-stu-id="7adf0-723">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="7adf0-724">Lors d’une réponse, les messages sont journalisés une fois que tous les autres gestionnaires du pipeline ont reçu la réponse.</span><span class="sxs-lookup"><span data-stu-id="7adf0-724">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="7adf0-725">La journalisation se produit également à l’intérieur du pipeline du gestionnaire de requêtes.</span><span class="sxs-lookup"><span data-stu-id="7adf0-725">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="7adf0-726">Dans l’exemple *MyNamedClient*, ces messages sont journalisés avec la catégorie de journalisation `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-726">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="7adf0-727">Pour la requête, cela se produit après que tous les autres gestionnaires ont été exécutés et immédiatement avant l’envoi de la requête sur le réseau.</span><span class="sxs-lookup"><span data-stu-id="7adf0-727">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="7adf0-728">Lors de la réponse, cette journalisation inclut l’état de la réponse avant qu’elle repasse à travers le pipeline de gestionnaires.</span><span class="sxs-lookup"><span data-stu-id="7adf0-728">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="7adf0-729">L’activation de la journalisation à l’extérieur et à l’intérieur du pipeline permet l’inspection des changements apportés par les autres gestionnaires du pipeline.</span><span class="sxs-lookup"><span data-stu-id="7adf0-729">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="7adf0-730">Par exemple, cela peut comprendre des changements apportés aux en-têtes des requêtes ou au code d’état de la réponse.</span><span class="sxs-lookup"><span data-stu-id="7adf0-730">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="7adf0-731">L’ajout du nom du client dans la catégorie de journalisation permet si nécessaire de filtrer le journal pour des clients nommés spécifiques.</span><span class="sxs-lookup"><span data-stu-id="7adf0-731">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="7adf0-732">Configurer le HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="7adf0-732">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="7adf0-733">Il peut être nécessaire de contrôler la configuration du `HttpMessageHandler` interne utilisé par un client.</span><span class="sxs-lookup"><span data-stu-id="7adf0-733">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="7adf0-734">Un `IHttpClientBuilder` est retourné quand vous ajoutez des clients nommés ou typés.</span><span class="sxs-lookup"><span data-stu-id="7adf0-734">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="7adf0-735">La méthode d’extension <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> peut être utilisée pour définir un délégué.</span><span class="sxs-lookup"><span data-stu-id="7adf0-735">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="7adf0-736">Le délégué est utilisé pour créer et configurer le `HttpMessageHandler` principal utilisé par ce client :</span><span class="sxs-lookup"><span data-stu-id="7adf0-736">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="7adf0-737">Utiliser IHttpClientFactory dans une application console</span><span class="sxs-lookup"><span data-stu-id="7adf0-737">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="7adf0-738">Dans une application console, ajoutez les références de package suivantes au projet :</span><span class="sxs-lookup"><span data-stu-id="7adf0-738">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="7adf0-739">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="7adf0-739">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="7adf0-740">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="7adf0-740">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="7adf0-741">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="7adf0-741">In the following example:</span></span>

* <span data-ttu-id="7adf0-742"><xref:System.Net.Http.IHttpClientFactory> est inscrit dans le conteneur de service dans de l’[hôte générique](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="7adf0-742"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="7adf0-743">`MyService` crée une instance de fabrique cliente à partir du service, qui est utilisée pour créer un `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="7adf0-743">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="7adf0-744">`HttpClient` est utilisé pour récupérer une page web.</span><span class="sxs-lookup"><span data-stu-id="7adf0-744">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="7adf0-745">`Main` crée une étendue pour exécuter la méthode `GetPage` du service et écrire les 500 premiers caractères du contenu de la page web dans la console.</span><span class="sxs-lookup"><span data-stu-id="7adf0-745">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="7adf0-746">Intergiciel (middleware) de propagation d’en-tête</span><span class="sxs-lookup"><span data-stu-id="7adf0-746">Header propagation middleware</span></span>

<span data-ttu-id="7adf0-747">La propagation d’en-tête est un intergiciel (middleware) pris en charge par la communauté pour propager les en-têtes HTTP de la demande entrante aux demandes du client HTTP sortantes.</span><span class="sxs-lookup"><span data-stu-id="7adf0-747">Header propagation is a community supported middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="7adf0-748">Pour utiliser la propagation d’en-tête :</span><span class="sxs-lookup"><span data-stu-id="7adf0-748">To use header propagation:</span></span>

* <span data-ttu-id="7adf0-749">Référencez le port de la communauté pris en charge du package [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="7adf0-749">Reference the community supported port of the package [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span></span> <span data-ttu-id="7adf0-750">ASP.NET Core 3,1 et versions ultérieures prennent en charge [Microsoft. AspNetCore. HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="7adf0-750">ASP.NET Core 3.1 and later supports [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span></span>

* <span data-ttu-id="7adf0-751">Configurer l’intergiciel et `HttpClient` dans `Startup` :</span><span class="sxs-lookup"><span data-stu-id="7adf0-751">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* <span data-ttu-id="7adf0-752">Le client comprend les en-têtes configurés pour les demandes sortantes :</span><span class="sxs-lookup"><span data-stu-id="7adf0-752">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="7adf0-753">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="7adf0-753">Additional resources</span></span>

* [<span data-ttu-id="7adf0-754">Utilisez HttpClientFactory pour implémenter des requêtes HTTP résilientes</span><span class="sxs-lookup"><span data-stu-id="7adf0-754">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="7adf0-755">Implémenter de nouvelles tentatives d’appel HTTP avec interruption exponentielle avec des stratégies Polly et HttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="7adf0-755">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="7adf0-756">Implémenter le modèle Disjoncteur</span><span class="sxs-lookup"><span data-stu-id="7adf0-756">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
