---
title: Tester l’intergiciel (middleware) ASP.NET Core
author: tratcher
description: Découvrez comment tester ASP.NET Core intergiciel avec TestServer.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/12/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: test/middleware
ms.openlocfilehash: 2dd5fa127af4432c612bb654d50eb4147aea6868
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051432"
---
# <a name="test-aspnet-core-middleware"></a><span data-ttu-id="dcd83-103">Tester l’intergiciel (middleware) ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dcd83-103">Test ASP.NET Core middleware</span></span>

<span data-ttu-id="dcd83-104">Par [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="dcd83-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="dcd83-105">L’intergiciel peut être testé en isolation avec <xref:Microsoft.AspNetCore.TestHost.TestServer> .</span><span class="sxs-lookup"><span data-stu-id="dcd83-105">Middleware can be tested in isolation with <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span> <span data-ttu-id="dcd83-106">Vous pouvez ainsi :</span><span class="sxs-lookup"><span data-stu-id="dcd83-106">It allows you to:</span></span>

* <span data-ttu-id="dcd83-107">Instanciez un pipeline d’application contenant uniquement les composants que vous devez tester.</span><span class="sxs-lookup"><span data-stu-id="dcd83-107">Instantiate an app pipeline containing only the components that you need to test.</span></span>
* <span data-ttu-id="dcd83-108">Envoyer des demandes personnalisées pour vérifier le comportement de l’intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="dcd83-108">Send custom requests to verify middleware behavior.</span></span>

<span data-ttu-id="dcd83-109">Avantages :</span><span class="sxs-lookup"><span data-stu-id="dcd83-109">Advantages:</span></span>

* <span data-ttu-id="dcd83-110">Les demandes sont envoyées en mémoire au lieu d’être sérialisées sur le réseau.</span><span class="sxs-lookup"><span data-stu-id="dcd83-110">Requests are sent in-memory rather than being serialized over the network.</span></span>
* <span data-ttu-id="dcd83-111">Cela évite des préoccupations supplémentaires, telles que la gestion des ports et les certificats HTTPs.</span><span class="sxs-lookup"><span data-stu-id="dcd83-111">This avoids additional concerns, such as port management and HTTPS certificates.</span></span>
* <span data-ttu-id="dcd83-112">Les exceptions dans l’intergiciel (middleware) peuvent être directement transmises au test appelant.</span><span class="sxs-lookup"><span data-stu-id="dcd83-112">Exceptions in the middleware can flow directly back to the calling test.</span></span>
* <span data-ttu-id="dcd83-113">Il est possible de personnaliser les structures de données du serveur, telles que <xref:Microsoft.AspNetCore.Http.HttpContext> , directement dans le test.</span><span class="sxs-lookup"><span data-stu-id="dcd83-113">It's possible to customize server data structures, such as <xref:Microsoft.AspNetCore.Http.HttpContext>, directly in the test.</span></span>

## <a name="set-up-the-testserver"></a><span data-ttu-id="dcd83-114">Configurer TestServer</span><span class="sxs-lookup"><span data-stu-id="dcd83-114">Set up the TestServer</span></span>

<span data-ttu-id="dcd83-115">Dans le projet de test, créez un test :</span><span class="sxs-lookup"><span data-stu-id="dcd83-115">In the test project, create a test:</span></span>

* <span data-ttu-id="dcd83-116">Créez et démarrez un hôte qui utilise <xref:Microsoft.AspNetCore.TestHost.TestServer> .</span><span class="sxs-lookup"><span data-stu-id="dcd83-116">Build and start a host that uses <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span>
* <span data-ttu-id="dcd83-117">Ajoutez les services requis que l’intergiciel utilise.</span><span class="sxs-lookup"><span data-stu-id="dcd83-117">Add any required services that the middleware uses.</span></span>
* <span data-ttu-id="dcd83-118">Ajoutez le package NuGet [Microsoft. AspNetCore. TestHost](https://www.nuget.org/packages/Microsoft.AspNetCore.TestHost/) au projet :</span><span class="sxs-lookup"><span data-stu-id="dcd83-118">Add the [Microsoft.AspNetCore.TestHost](https://www.nuget.org/packages/Microsoft.AspNetCore.TestHost/) NuGet package to the project:</span></span>
  
  ```dotnetcli
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.TestHost" Version="3.1.*" />
  </ItemGroup>
  ```

* <span data-ttu-id="dcd83-119">Configurez le pipeline de traitement pour utiliser l’intergiciel (middleware) pour le test.</span><span class="sxs-lookup"><span data-stu-id="dcd83-119">Configure the processing pipeline to use the middleware for the test.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/setup.cs?highlight=4-18)]

## <a name="send-requests-with-httpclient"></a><span data-ttu-id="dcd83-120">Envoyer des demandes avec HttpClient</span><span class="sxs-lookup"><span data-stu-id="dcd83-120">Send requests with HttpClient</span></span>

<span data-ttu-id="dcd83-121">Envoyer une demande à l’aide de <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="dcd83-121">Send a request using <xref:System.Net.Http.HttpClient>:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/request.cs?highlight=20)]

<span data-ttu-id="dcd83-122">Déclarez le résultat.</span><span class="sxs-lookup"><span data-stu-id="dcd83-122">Assert the result.</span></span> <span data-ttu-id="dcd83-123">Tout d’abord, faites d’une assertion l’inverse du résultat attendu.</span><span class="sxs-lookup"><span data-stu-id="dcd83-123">First, make an assertion the opposite of the result that you expect.</span></span> <span data-ttu-id="dcd83-124">Une exécution initiale avec une assertion false positive confirme que le test échoue quand l’intergiciel s’exécute correctement.</span><span class="sxs-lookup"><span data-stu-id="dcd83-124">An initial run with a false positive assertion confirms that the test fails when the middleware is performing correctly.</span></span> <span data-ttu-id="dcd83-125">Exécutez le test et vérifiez que le test échoue.</span><span class="sxs-lookup"><span data-stu-id="dcd83-125">Run the test and confirm that the test fails.</span></span>

<span data-ttu-id="dcd83-126">Dans l’exemple suivant, l’intergiciel (middleware) doit retourner un code d’État 404 ( *introuvable* ) quand le point de terminaison racine est demandé.</span><span class="sxs-lookup"><span data-stu-id="dcd83-126">In the following example, the middleware should return a 404 status code ( *Not Found* ) when the root endpoint is requested.</span></span> <span data-ttu-id="dcd83-127">Effectuez la première série de tests avec `Assert.NotEqual( ... );` , qui doit échouer :</span><span class="sxs-lookup"><span data-stu-id="dcd83-127">Make the first test run with `Assert.NotEqual( ... );`, which should fail:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/false-failure-check.cs?highlight=22)]

<span data-ttu-id="dcd83-128">Modifiez l’assertion pour tester l’intergiciel dans des conditions de fonctionnement normales.</span><span class="sxs-lookup"><span data-stu-id="dcd83-128">Change the assertion to test the middleware under normal operating conditions.</span></span> <span data-ttu-id="dcd83-129">Le test final utilise `Assert.Equal( ... );` .</span><span class="sxs-lookup"><span data-stu-id="dcd83-129">The final test uses `Assert.Equal( ... );`.</span></span> <span data-ttu-id="dcd83-130">Exécutez à nouveau le test pour confirmer qu’il réussit.</span><span class="sxs-lookup"><span data-stu-id="dcd83-130">Run the test again to confirm that it passes.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/final-test.cs?highlight=22)]

## <a name="send-requests-with-httpcontext"></a><span data-ttu-id="dcd83-131">Envoyer des demandes avec HttpContext</span><span class="sxs-lookup"><span data-stu-id="dcd83-131">Send requests with HttpContext</span></span>

<span data-ttu-id="dcd83-132">Une application de test peut également envoyer une demande à l’aide de [SendAsync (action \<HttpContext> , CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A).</span><span class="sxs-lookup"><span data-stu-id="dcd83-132">A test app can also send a request using [SendAsync(Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A).</span></span> <span data-ttu-id="dcd83-133">Dans l’exemple suivant, plusieurs vérifications sont effectuées lorsque `https://example.com/A/Path/?and=query` est traité par l’intergiciel (middleware) :</span><span class="sxs-lookup"><span data-stu-id="dcd83-133">In the following example, several checks are made when `https://example.com/A/Path/?and=query` is processed by the middleware:</span></span>

```csharp
[Fact]
public async Task TestMiddleware_ExpectedResponse()
{
    using var host = await new HostBuilder()
        .ConfigureWebHost(webBuilder =>
        {
            webBuilder
                .UseTestServer()
                .ConfigureServices(services =>
                {
                    services.AddMyServices();
                })
                .Configure(app =>
                {
                    app.UseMiddleware<MyMiddleware>();
                });
        })
        .StartAsync();

    var server = host.GetTestServer();
    server.BaseAddress = new Uri("https://example.com/A/Path/");

    var context = await server.SendAsync(c =>
    {
        c.Request.Method = HttpMethods.Post;
        c.Request.Path = "/and/file.txt";
        c.Request.QueryString = new QueryString("?and=query");
    });

    Assert.True(context.RequestAborted.CanBeCanceled);
    Assert.Equal(HttpProtocol.Http11, context.Request.Protocol);
    Assert.Equal("POST", context.Request.Method);
    Assert.Equal("https", context.Request.Scheme);
    Assert.Equal("example.com", context.Request.Host.Value);
    Assert.Equal("/A/Path", context.Request.PathBase.Value);
    Assert.Equal("/and/file.txt", context.Request.Path.Value);
    Assert.Equal("?and=query", context.Request.QueryString.Value);
    Assert.NotNull(context.Request.Body);
    Assert.NotNull(context.Request.Headers);
    Assert.NotNull(context.Response.Headers);
    Assert.NotNull(context.Response.Body);
    Assert.Equal(404, context.Response.StatusCode);
    Assert.Null(context.Features.Get<IHttpResponseFeature>().ReasonPhrase);
}
```

<span data-ttu-id="dcd83-134"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> autorise la configuration directe d’un <xref:Microsoft.AspNetCore.Http.HttpContext> objet plutôt que l’utilisation des <xref:System.Net.Http.HttpClient> abstractions.</span><span class="sxs-lookup"><span data-stu-id="dcd83-134"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> permits direct configuration of an <xref:Microsoft.AspNetCore.Http.HttpContext> object rather than using the <xref:System.Net.Http.HttpClient> abstractions.</span></span> <span data-ttu-id="dcd83-135">Utilisez <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> pour manipuler des structures uniquement disponibles sur le serveur, telles que [HttpContext. Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) ou [HttpContext. Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).</span><span class="sxs-lookup"><span data-stu-id="dcd83-135">Use <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> to manipulate structures only available on the server, such as [HttpContext.Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) or [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).</span></span>

<span data-ttu-id="dcd83-136">Comme dans l’exemple précédent qui a testé une réponse *404-introuvable* , vérifiez l’inverse pour chaque `Assert` instruction du test précédent.</span><span class="sxs-lookup"><span data-stu-id="dcd83-136">As with the earlier example that tested for a *404 - Not Found* response, check the opposite for each `Assert` statement in the preceding test.</span></span> <span data-ttu-id="dcd83-137">La vérification confirme que le test échoue correctement lorsque l’intergiciel fonctionne normalement.</span><span class="sxs-lookup"><span data-stu-id="dcd83-137">The check confirms that the test fails correctly when the middleware is operating normally.</span></span> <span data-ttu-id="dcd83-138">Une fois que vous avez confirmé que le test de faux positif fonctionne, définissez les `Assert` instructions finales pour les conditions et les valeurs attendues du test.</span><span class="sxs-lookup"><span data-stu-id="dcd83-138">After you've confirmed that the false positive test works, set the final `Assert` statements for the expected conditions and values of the test.</span></span> <span data-ttu-id="dcd83-139">Exécutez-le à nouveau pour confirmer que le test réussit.</span><span class="sxs-lookup"><span data-stu-id="dcd83-139">Run it again to confirm that the test passes.</span></span>

## <a name="testserver-limitations"></a><span data-ttu-id="dcd83-140">Limitations de TestServer</span><span class="sxs-lookup"><span data-stu-id="dcd83-140">TestServer limitations</span></span>

<span data-ttu-id="dcd83-141">TestServer</span><span class="sxs-lookup"><span data-stu-id="dcd83-141">TestServer:</span></span>

* <span data-ttu-id="dcd83-142">A été créé pour répliquer des comportements de serveur pour tester l’intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="dcd83-142">Was created to replicate server behaviors to test middleware.</span></span>
* <span data-ttu-id="dcd83-143">Do \* **not** _ tente de répliquer tous les <xref:System.Net.Http.HttpClient> comportements.</span><span class="sxs-lookup"><span data-stu-id="dcd83-143">Does \* **not** _ try to replicate all <xref:System.Net.Http.HttpClient> behaviors.</span></span>
<span data-ttu-id="dcd83-144">_ Tente d’accorder au client un accès au plus grand contrôle du serveur que possible et avec autant de visibilité sur ce qui se passe sur le serveur que possible.</span><span class="sxs-lookup"><span data-stu-id="dcd83-144">_ Attempts to give the client access to as much control over the server as possible, and with as much visibility into what's happening on the server as possible.</span></span> <span data-ttu-id="dcd83-145">Par exemple, il peut lever des exceptions qui ne sont normalement pas levées par afin `HttpClient` de communiquer directement l’état du serveur.</span><span class="sxs-lookup"><span data-stu-id="dcd83-145">For example it may throw exceptions not normally thrown by `HttpClient` in order to directly communicate server state.</span></span>
* <span data-ttu-id="dcd83-146">Ne définit pas certains en-têtes de transport par défaut, car ceux-ci ne sont généralement pas pertinents pour l’intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="dcd83-146">Doesn't set some transport specific headers by default as those are not usually relevant to middleware.</span></span> <span data-ttu-id="dcd83-147">Pour plus d'informations, consultez la section suivante.</span><span class="sxs-lookup"><span data-stu-id="dcd83-147">For more information, see the next section.</span></span>

### <a name="content-length-and-transfer-encoding-headers"></a><span data-ttu-id="dcd83-148">En-têtes Content-Length et Transfer-Encoding</span><span class="sxs-lookup"><span data-stu-id="dcd83-148">Content-Length and Transfer-Encoding headers</span></span>

<span data-ttu-id="dcd83-149">TestServer ne permet **pas** de définir des en-têtes de demande ou de réponse de transport tels que [Content-Length](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Length) ou [Transfer-Encoding](https://developer.mozilla.org/docs/Web/HTTP/Headers/Transfer-Encoding).</span><span class="sxs-lookup"><span data-stu-id="dcd83-149">TestServer does \* **not** _ set transport related request or response headers such as [Content-Length](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Length) or [Transfer-Encoding](https://developer.mozilla.org/docs/Web/HTTP/Headers/Transfer-Encoding).</span></span> <span data-ttu-id="dcd83-150">Les applications doivent éviter en fonction de ces en-têtes, car leur utilisation varie selon le client, le scénario et le protocole.</span><span class="sxs-lookup"><span data-stu-id="dcd83-150">Applications should avoid depending on these headers because their usage varies by client, scenario, and protocol.</span></span> <span data-ttu-id="dcd83-151">Si `Content-Length` et `Transfer-Encoding` sont nécessaires pour tester un scénario spécifique, ils peuvent être spécifiés dans le test lors de la composition de <xref:System.Net.Http.HttpRequestMessage> ou de <xref:Microsoft.AspNetCore.Http.HttpContext> .</span><span class="sxs-lookup"><span data-stu-id="dcd83-151">If `Content-Length` and `Transfer-Encoding` are necessary to test a specific scenario, they can be specified in the test when composing the <xref:System.Net.Http.HttpRequestMessage> or <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span> <span data-ttu-id="dcd83-152">Pour plus d’informations, consultez les problèmes GitHub suivants :</span><span class="sxs-lookup"><span data-stu-id="dcd83-152">For more information, see the following GitHub issues:</span></span>

<span data-ttu-id="dcd83-153">_ [dotnet/aspnetcore # 21677](https://github.com/dotnet/aspnetcore/issues/21677)</span><span class="sxs-lookup"><span data-stu-id="dcd83-153">_ [dotnet/aspnetcore#21677](https://github.com/dotnet/aspnetcore/issues/21677)</span></span>
* [<span data-ttu-id="dcd83-154">dotnet/aspnetcore # 18463</span><span class="sxs-lookup"><span data-stu-id="dcd83-154">dotnet/aspnetcore#18463</span></span>](https://github.com/dotnet/aspnetcore/issues/18463)
* [<span data-ttu-id="dcd83-155">dotnet/aspnetcore # 13273</span><span class="sxs-lookup"><span data-stu-id="dcd83-155">dotnet/aspnetcore#13273</span></span>](https://github.com/dotnet/aspnetcore/issues/13273)
