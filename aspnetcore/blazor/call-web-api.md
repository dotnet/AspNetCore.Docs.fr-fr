---
title: Appeler une API Web à partir de ASP.NET Core Blazor WebAssembly
author: guardrex
description: Découvrez comment appeler une API Web à partir d’une Blazor WebAssembly application à l’aide des applications auxiliaires JSON, y compris la création de demandes de partage des ressources Cross-Origin (cors).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/24/2020
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
uid: blazor/call-web-api
ms.openlocfilehash: 9ac17e7c22b23ced7a8f12a6ef0d456f6244318b
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586759"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a><span data-ttu-id="b704b-103">Appeler une API Web à partir de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="b704b-103">Call a web API from ASP.NET Core Blazor</span></span>

> [!NOTE]
> <span data-ttu-id="b704b-104">Cette rubrique s’applique à Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="b704b-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="b704b-105">[Blazor Server](xref:blazor/hosting-models#blazor-server) les applications appellent des API Web à l’aide d' <xref:System.Net.Http.HttpClient> instances, généralement créées à l’aide de <xref:System.Net.Http.IHttpClientFactory> .</span><span class="sxs-lookup"><span data-stu-id="b704b-105">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="b704b-106">Pour obtenir des instructions qui s’appliquent à Blazor Server , consultez <xref:fundamentals/http-requests> .</span><span class="sxs-lookup"><span data-stu-id="b704b-106">For guidance that applies to Blazor Server, see <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="b704b-107">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) les applications appellent des API Web à l’aide d’un <xref:System.Net.Http.HttpClient> service préconfiguré.</span><span class="sxs-lookup"><span data-stu-id="b704b-107">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured <xref:System.Net.Http.HttpClient> service.</span></span> <span data-ttu-id="b704b-108">Composez des requêtes, qui peuvent inclure des options de l' [API d’extraction](https://developer.mozilla.org/docs/Web/API/Fetch_API) JavaScript, à l’aide Blazor des applications auxiliaires JSON ou de <xref:System.Net.Http.HttpRequestMessage> .</span><span class="sxs-lookup"><span data-stu-id="b704b-108">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="b704b-109">Le <xref:System.Net.Http.HttpClient> service dans les Blazor WebAssembly applications est axé sur l’exécution de requêtes sur le serveur d’origine.</span><span class="sxs-lookup"><span data-stu-id="b704b-109">The <xref:System.Net.Http.HttpClient> service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="b704b-110">Les instructions de cette rubrique concernent uniquement les Blazor WebAssembly applications.</span><span class="sxs-lookup"><span data-stu-id="b704b-110">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="b704b-111">[Affichez ou téléchargez l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample)) : sélectionnez l' `BlazorWebAssemblySample` application.</span><span class="sxs-lookup"><span data-stu-id="b704b-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)): Select the `BlazorWebAssemblySample` app.</span></span>

<span data-ttu-id="b704b-112">Consultez les composants suivants dans l' `BlazorWebAssemblySample` exemple d’application :</span><span class="sxs-lookup"><span data-stu-id="b704b-112">See the following components in the `BlazorWebAssemblySample` sample app:</span></span>

* <span data-ttu-id="b704b-113">Appeler l’API Web ( `Pages/CallWebAPI.razor` )</span><span class="sxs-lookup"><span data-stu-id="b704b-113">Call Web API (`Pages/CallWebAPI.razor`)</span></span>
* <span data-ttu-id="b704b-114">Testeur de requêtes HTTP ( `Components/HTTPRequestTester.razor` )</span><span class="sxs-lookup"><span data-stu-id="b704b-114">HTTP Request Tester (`Components/HTTPRequestTester.razor`)</span></span>

## <a name="packages"></a><span data-ttu-id="b704b-115">Paquets</span><span class="sxs-lookup"><span data-stu-id="b704b-115">Packages</span></span>

<span data-ttu-id="b704b-116">Référencez le [`System.Net.Http.Json`](https://www.nuget.org/packages/System.Net.Http.Json) package NuGet dans le fichier projet.</span><span class="sxs-lookup"><span data-stu-id="b704b-116">Reference the [`System.Net.Http.Json`](https://www.nuget.org/packages/System.Net.Http.Json) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="b704b-117">Ajouter le service HttpClient</span><span class="sxs-lookup"><span data-stu-id="b704b-117">Add the HttpClient service</span></span>

<span data-ttu-id="b704b-118">Dans `Program.Main` , ajoutez un <xref:System.Net.Http.HttpClient> service s’il n’existe pas déjà :</span><span class="sxs-lookup"><span data-stu-id="b704b-118">In `Program.Main`, add an <xref:System.Net.Http.HttpClient> service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddScoped(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="b704b-119">Applications auxiliaires HttpClient et JSON</span><span class="sxs-lookup"><span data-stu-id="b704b-119">HttpClient and JSON helpers</span></span>

<span data-ttu-id="b704b-120">Dans une Blazor WebAssembly application, [`HttpClient`](xref:fundamentals/http-requests) est disponible en tant que service préconfiguré pour effectuer des demandes auprès du serveur d’origine.</span><span class="sxs-lookup"><span data-stu-id="b704b-120">In a Blazor WebAssembly app, [`HttpClient`](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="b704b-121">Une Blazor Server application n’inclut pas <xref:System.Net.Http.HttpClient> de service par défaut.</span><span class="sxs-lookup"><span data-stu-id="b704b-121">A Blazor Server app doesn't include an <xref:System.Net.Http.HttpClient> service by default.</span></span> <span data-ttu-id="b704b-122">Fournissez un <xref:System.Net.Http.HttpClient> à l’application à l’aide de l' [ `HttpClient` infrastructure de fabrique](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="b704b-122">Provide an <xref:System.Net.Http.HttpClient> to the app using the [`HttpClient` factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="b704b-123"><xref:System.Net.Http.HttpClient> et les auxiliaires JSON sont également utilisés pour appeler des points de terminaison d’API Web tiers.</span><span class="sxs-lookup"><span data-stu-id="b704b-123"><xref:System.Net.Http.HttpClient> and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="b704b-124"><xref:System.Net.Http.HttpClient> est implémenté à l’aide de l' [API FETCH](https://developer.mozilla.org/docs/Web/API/Fetch_API) du navigateur et est soumis à ses limitations, y compris l’application de la même stratégie d’origine.</span><span class="sxs-lookup"><span data-stu-id="b704b-124"><xref:System.Net.Http.HttpClient> is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="b704b-125">L’adresse de base du client est définie sur l’adresse du serveur d’origine.</span><span class="sxs-lookup"><span data-stu-id="b704b-125">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="b704b-126">Injecter une <xref:System.Net.Http.HttpClient> instance à l’aide de la [`@inject`](xref:mvc/views/razor#inject) directive :</span><span class="sxs-lookup"><span data-stu-id="b704b-126">Inject an <xref:System.Net.Http.HttpClient> instance using the [`@inject`](xref:mvc/views/razor#inject) directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="b704b-127">Dans les exemples suivants, une API Web todo traite les opérations de création, lecture, mise à jour et suppression (CRUD).</span><span class="sxs-lookup"><span data-stu-id="b704b-127">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="b704b-128">Les exemples sont basés sur une `TodoItem` classe qui stocke les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="b704b-128">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="b704b-129">ID ( `Id` , `long` ) : ID unique de l’élément.</span><span class="sxs-lookup"><span data-stu-id="b704b-129">ID (`Id`, `long`): Unique ID of the item.</span></span>
* <span data-ttu-id="b704b-130">Nom ( `Name` , `string` ) : nom de l’élément.</span><span class="sxs-lookup"><span data-stu-id="b704b-130">Name (`Name`, `string`): Name of the item.</span></span>
* <span data-ttu-id="b704b-131">Status ( `IsComplete` , `bool` ) : indique si l’élément TODO est terminé.</span><span class="sxs-lookup"><span data-stu-id="b704b-131">Status (`IsComplete`, `bool`): Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="b704b-132">Les méthodes d’assistance JSON envoient des demandes à un URI (une API Web dans les exemples suivants) et traitent la réponse :</span><span class="sxs-lookup"><span data-stu-id="b704b-132">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="b704b-133"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>: Envoie une requête HTTP obtenir et analyse le corps de la réponse JSON pour créer un objet.</span><span class="sxs-lookup"><span data-stu-id="b704b-133"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>: Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="b704b-134">Dans le code suivant, les `todoItems` sont affichés par le composant.</span><span class="sxs-lookup"><span data-stu-id="b704b-134">In the following code, the `todoItems` are displayed by the component.</span></span> <span data-ttu-id="b704b-135">La `GetTodoItems` méthode est déclenchée lorsque le rendu du composant est terminé ( [`OnInitializedAsync`](xref:blazor/components/lifecycle#component-initialization-methods) ).</span><span class="sxs-lookup"><span data-stu-id="b704b-135">The `GetTodoItems` method is triggered when the component is finished rendering ([`OnInitializedAsync`](xref:blazor/components/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="b704b-136">Pour obtenir un exemple complet, consultez l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="b704b-136">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] todoItems;

      protected override async Task OnInitializedAsync() => 
          todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="b704b-137"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>: Envoie une requête HTTP POSTÉRIEURe, y compris du contenu encodé JSON, et analyse le corps de la réponse JSON pour créer un objet.</span><span class="sxs-lookup"><span data-stu-id="b704b-137"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>: Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="b704b-138">Dans le code suivant, `newItemName` est fourni par un élément lié du composant.</span><span class="sxs-lookup"><span data-stu-id="b704b-138">In the following code, `newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="b704b-139">La `AddItem` méthode est déclenchée par la sélection d’un `<button>` élément.</span><span class="sxs-lookup"><span data-stu-id="b704b-139">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="b704b-140">Pour obtenir un exemple complet, consultez l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="b704b-140">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input @bind="newItemName" placeholder="New Todo Item" />
  <button @onclick="@AddItem">Add</button>

  @code {
      private string newItemName;

      private async Task AddItem()
      {
          var addItem = new TodoItem { Name = newItemName, IsComplete = false };
          await Http.PostAsJsonAsync("api/TodoItems", addItem);
      }
  }
  ```
  
  <span data-ttu-id="b704b-141">Appelle pour <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> retourner un <xref:System.Net.Http.HttpResponseMessage> .</span><span class="sxs-lookup"><span data-stu-id="b704b-141">Calls to <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="b704b-142">Pour désérialiser le contenu JSON du message de réponse, utilisez la `ReadFromJsonAsync<T>` méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="b704b-142">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <span data-ttu-id="b704b-143"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>: Envoie une requête HTTP PUT, y compris le contenu encodé JSON.</span><span class="sxs-lookup"><span data-stu-id="b704b-143"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>: Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="b704b-144">Dans le code suivant, les `editItem` valeurs pour `Name` et `IsCompleted` sont fournies par les éléments dépendants du composant.</span><span class="sxs-lookup"><span data-stu-id="b704b-144">In the following code, `editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="b704b-145">L’élément `Id` est défini lorsque l’élément est sélectionné dans une autre partie de l’interface utilisateur et `EditItem` est appelé.</span><span class="sxs-lookup"><span data-stu-id="b704b-145">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="b704b-146">La `SaveItem` méthode est déclenchée par la sélection de l' `<button>` élément Save.</span><span class="sxs-lookup"><span data-stu-id="b704b-146">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="b704b-147">Pour obtenir un exemple complet, consultez l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="b704b-147">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input type="checkbox" @bind="editItem.IsComplete" />
  <input @bind="editItem.Name" />
  <button @onclick="@SaveItem">Save</button>

  @code {
      private TodoItem editItem = new TodoItem();

      private void EditItem(long id)
      {
          editItem = todoItems.Single(i => i.Id == id);
      }

      private async Task SaveItem() =>
          await Http.PutAsJsonAsync($"api/TodoItems/{editItem.Id}", editItem);
  }
  ```
  
  <span data-ttu-id="b704b-148">Appelle pour <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> retourner un <xref:System.Net.Http.HttpResponseMessage> .</span><span class="sxs-lookup"><span data-stu-id="b704b-148">Calls to <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="b704b-149">Pour désérialiser le contenu JSON du message de réponse, utilisez la <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="b704b-149">To deserialize the JSON content from the response message, use the <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> extension method:</span></span>
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

<span data-ttu-id="b704b-150"><xref:System.Net.Http> contient des méthodes d’extension supplémentaires pour envoyer des requêtes HTTP et recevoir des réponses HTTP.</span><span class="sxs-lookup"><span data-stu-id="b704b-150"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="b704b-151"><xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType> est utilisé pour envoyer une requête HTTP DELETE à une API Web.</span><span class="sxs-lookup"><span data-stu-id="b704b-151"><xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType> is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="b704b-152">Dans le code suivant, l' `<button>` élément delete appelle la `DeleteItem` méthode.</span><span class="sxs-lookup"><span data-stu-id="b704b-152">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="b704b-153">L' `<input>` élément lié fournit le `id` de l’élément à supprimer.</span><span class="sxs-lookup"><span data-stu-id="b704b-153">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="b704b-154">Pour obtenir un exemple complet, consultez l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="b704b-154">See the sample app for a complete example.</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http

<input @bind="id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/TodoItems/{id}");
}
```

## <a name="named-httpclient-with-ihttpclientfactory"></a><span data-ttu-id="b704b-155">Nommé HttpClient avec IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="b704b-155">Named HttpClient with IHttpClientFactory</span></span>

<span data-ttu-id="b704b-156"><xref:System.Net.Http.IHttpClientFactory> les services et la configuration d’un nommé <xref:System.Net.Http.HttpClient> sont pris en charge.</span><span class="sxs-lookup"><span data-stu-id="b704b-156"><xref:System.Net.Http.IHttpClientFactory> services and the configuration of a named <xref:System.Net.Http.HttpClient> are supported.</span></span>

<span data-ttu-id="b704b-157">Référencez le [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package NuGet dans le fichier projet.</span><span class="sxs-lookup"><span data-stu-id="b704b-157">Reference the [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) NuGet package in the project file.</span></span>

<span data-ttu-id="b704b-158">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="b704b-158">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="b704b-159">`FetchData` composant ( `Pages/FetchData.razor` ) :</span><span class="sxs-lookup"><span data-stu-id="b704b-159">`FetchData` component (`Pages/FetchData.razor`):</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var client = ClientFactory.CreateClient("ServerAPI");

        forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForecast");
    }
}
```

## <a name="typed-httpclient"></a><span data-ttu-id="b704b-160">HttpClient typé</span><span class="sxs-lookup"><span data-stu-id="b704b-160">Typed HttpClient</span></span>

<span data-ttu-id="b704b-161">Typé <xref:System.Net.Http.HttpClient> utilise une ou plusieurs instances de l’application <xref:System.Net.Http.HttpClient> , default ou named, pour retourner des données à partir d’un ou plusieurs points de terminaison d’API Web.</span><span class="sxs-lookup"><span data-stu-id="b704b-161">Typed <xref:System.Net.Http.HttpClient> uses one or more of the app's <xref:System.Net.Http.HttpClient> instances, default or named, to return data from one or more web API endpoints.</span></span>

<span data-ttu-id="b704b-162">`WeatherForecastClient.cs`:</span><span class="sxs-lookup"><span data-stu-id="b704b-162">`WeatherForecastClient.cs`:</span></span>

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;

public class WeatherForecastHttpClient
{
    private readonly HttpClient http;

    public WeatherForecastHttpClient(HttpClient http)
    {
        this.http = http;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var forecasts = new WeatherForecast[0];

        try
        {
            forecasts = await http.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        catch
        {
            ...
        }

        return forecasts;
    }
}
```

<span data-ttu-id="b704b-163">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="b704b-163">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastHttpClient>(client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="b704b-164">Les composants injectent le typé <xref:System.Net.Http.HttpClient> pour appeler l’API Web.</span><span class="sxs-lookup"><span data-stu-id="b704b-164">Components inject the typed <xref:System.Net.Http.HttpClient> to call the web API.</span></span>

<span data-ttu-id="b704b-165">`FetchData` composant ( `Pages/FetchData.razor` ) :</span><span class="sxs-lookup"><span data-stu-id="b704b-165">`FetchData` component (`Pages/FetchData.razor`):</span></span>

```razor
@inject WeatherForecastHttpClient Http

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        forecasts = await Http.GetForecastAsync();
    }
}
```

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="b704b-166">`HttpClient` et `HttpRequestMessage` avec les options de requête de l’API Fetch</span><span class="sxs-lookup"><span data-stu-id="b704b-166">`HttpClient` and `HttpRequestMessage` with Fetch API request options</span></span>

<span data-ttu-id="b704b-167">Lors de l’exécution sur webassembly dans une Blazor WebAssembly application, [`HttpClient`](xref:fundamentals/http-requests) (documentation de l'[API](xref:System.Net.Http.HttpClient)) et <xref:System.Net.Http.HttpRequestMessage> peut être utilisé pour personnaliser les demandes.</span><span class="sxs-lookup"><span data-stu-id="b704b-167">When running on WebAssembly in a Blazor WebAssembly app, [`HttpClient`](xref:fundamentals/http-requests) ([API documentation](xref:System.Net.Http.HttpClient)) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="b704b-168">Par exemple, vous pouvez spécifier la méthode HTTP et les en-têtes de demande.</span><span class="sxs-lookup"><span data-stu-id="b704b-168">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="b704b-169">Le composant suivant envoie une `POST` demande à un point de terminaison d’API de liste de tâches sur le serveur et affiche le corps de la réponse :</span><span class="sxs-lookup"><span data-stu-id="b704b-169">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

```razor
@page "/todorequest"
@using System.Net.Http
@using System.Net.Http.Headers
@using System.Net.Http.Json
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Http
@inject IAccessTokenProvider TokenProvider

<h1>ToDo Request</h1>

<button @onclick="PostRequest">Submit POST request</button>

<p>Response body returned by the server:</p>

<p>@responseBody</p>

@code {
    private string responseBody;

    private async Task PostRequest()
    {
        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content =
                JsonContent.Create(new TodoItem
                {
                    Name = "My New Todo Item",
                    IsComplete = false
                })
        };

        var tokenResult = await TokenProvider.RequestAccessToken();

        if (tokenResult.TryGetToken(out var token))
        {
            requestMessage.Headers.Authorization =
                new AuthenticationHeaderValue("Bearer", token.Value);

            requestMessage.Content.Headers.TryAddWithoutValidation(
                "x-custom-header", "value");

            var response = await Http.SendAsync(requestMessage);
            var responseStatusCode = response.StatusCode;

            responseBody = await response.Content.ReadAsStringAsync();
        }
    }

    public class TodoItem
    {
        public long Id { get; set; }
        public string Name { get; set; }
        public bool IsComplete { get; set; }
    }
}
```

<span data-ttu-id="b704b-170">L’implémentation de .NET webassembly de <xref:System.Net.Http.HttpClient> utilise [WindowOrWorkerGlobalScope. Fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span><span class="sxs-lookup"><span data-stu-id="b704b-170">.NET WebAssembly's implementation of <xref:System.Net.Http.HttpClient> uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="b704b-171">L’extraction permet de configurer plusieurs [options spécifiques à la demande](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="b704b-171">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="b704b-172">Les options de requête HTTP FETCH peuvent être configurées avec <xref:System.Net.Http.HttpRequestMessage> les méthodes d’extension indiquées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="b704b-172">HTTP fetch request options can be configured with <xref:System.Net.Http.HttpRequestMessage> extension methods shown in the following table.</span></span>

| <span data-ttu-id="b704b-173">Méthode d’extension</span><span class="sxs-lookup"><span data-stu-id="b704b-173">Extension method</span></span> | <span data-ttu-id="b704b-174">Propriété de requête Fetch</span><span class="sxs-lookup"><span data-stu-id="b704b-174">Fetch request property</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [`credentials`](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [`cache`](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [`mode`](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [`integrity`](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="b704b-175">Vous pouvez définir des options supplémentaires à l’aide de la méthode d’extension plus générique <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> .</span><span class="sxs-lookup"><span data-stu-id="b704b-175">You can set additional options using the more generic <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extension method.</span></span>
 
<span data-ttu-id="b704b-176">La réponse HTTP est généralement mise en mémoire tampon dans une Blazor WebAssembly application pour permettre la prise en charge des lectures de synchronisation sur le contenu de la réponse.</span><span class="sxs-lookup"><span data-stu-id="b704b-176">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="b704b-177">Pour activer la prise en charge de la diffusion en continu de réponse, utilisez la <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> méthode d’extension sur la demande.</span><span class="sxs-lookup"><span data-stu-id="b704b-177">To enable support for response streaming, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> extension method on the request.</span></span>

<span data-ttu-id="b704b-178">Pour inclure des informations d’identification dans une demande Cross-Origin, utilisez la <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="b704b-178">To include credentials in a cross-origin request, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="b704b-179">Pour plus d’informations sur les options de l’API FETCH, consultez [MDN Web docs : WindowOrWorkerGlobalScope. Fetch () :P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="b704b-179">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

## <a name="handle-errors"></a><span data-ttu-id="b704b-180">des erreurs</span><span class="sxs-lookup"><span data-stu-id="b704b-180">Handle errors</span></span>

<span data-ttu-id="b704b-181">Lorsque des erreurs se produisent lors de l’interaction avec une API Web, elles peuvent être gérées par le code du développeur.</span><span class="sxs-lookup"><span data-stu-id="b704b-181">When errors occur while interacting with a web API, they can be handled by developer code.</span></span> <span data-ttu-id="b704b-182">Par exemple, <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> attend une réponse JSON de l’API du serveur avec un `Content-Type` de `application/json` .</span><span class="sxs-lookup"><span data-stu-id="b704b-182">For example, <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> expects a JSON response from the server API with a `Content-Type` of `application/json`.</span></span> <span data-ttu-id="b704b-183">Si la réponse n’est pas au format JSON, la validation du contenu lève une exception <xref:System.NotSupportedException> .</span><span class="sxs-lookup"><span data-stu-id="b704b-183">If the response isn't in JSON format, content validation throws a <xref:System.NotSupportedException>.</span></span>

<span data-ttu-id="b704b-184">Dans l’exemple suivant, le point de terminaison d’URI pour la demande de données de prévision météo est mal orthographié.</span><span class="sxs-lookup"><span data-stu-id="b704b-184">In the following example, the URI endpoint for the weather forecast data request is misspelled.</span></span> <span data-ttu-id="b704b-185">L’URI doit être à, `WeatherForecast` mais apparaît dans l’appel en tant que `WeatherForcast` (« e » manquant).</span><span class="sxs-lookup"><span data-stu-id="b704b-185">The URI should be to `WeatherForecast` but appears in the call as `WeatherForcast` (missing "e").</span></span>

<span data-ttu-id="b704b-186">L' <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> appel attend que JSON soit retourné, mais le serveur retourne du code HTML pour une exception non gérée sur le serveur avec un `Content-Type` de `text/html` .</span><span class="sxs-lookup"><span data-stu-id="b704b-186">The <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> call expects JSON to be returned, but the server returns HTML for an unhandled exception on the server with a `Content-Type` of `text/html`.</span></span> <span data-ttu-id="b704b-187">L’exception non gérée se produit sur le serveur, car le chemin d’accès est introuvable et l’intergiciel ne peut pas traiter une page ou une vue pour la demande.</span><span class="sxs-lookup"><span data-stu-id="b704b-187">The unhandled exception occurs on the server because the path isn't found and middleware can't serve a page or view for the request.</span></span>

<span data-ttu-id="b704b-188">Dans <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> sur le client, <xref:System.NotSupportedException> est levée lorsque le contenu de la réponse est validé en tant que non-JSON.</span><span class="sxs-lookup"><span data-stu-id="b704b-188">In <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> on the client, <xref:System.NotSupportedException> is thrown when the response content is validated as non-JSON.</span></span> <span data-ttu-id="b704b-189">L’exception est interceptée dans le `catch` bloc, où la logique personnalisée peut consigner l’erreur ou présenter un message d’erreur convivial à l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="b704b-189">The exception is caught in the `catch` block, where custom logic could log the error or present a friendly error message to the user:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForcast");
    }
    catch (NotSupportedException exception)
    {
        ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="b704b-190">L’exemple précédent est fourni à des fins de démonstration.</span><span class="sxs-lookup"><span data-stu-id="b704b-190">The preceding example is for demonstration purposes.</span></span> <span data-ttu-id="b704b-191">Une application serveur d’API Web peut être configurée pour retourner JSON même lorsqu’un point de terminaison n’existe pas ou qu’une exception non gérée s’est produite sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="b704b-191">A web API server app can be configured to return JSON even when an endpoint doesn't exist or an unhandled exception on the server occurs.</span></span>

<span data-ttu-id="b704b-192">Pour plus d’informations, consultez <xref:blazor/fundamentals/handle-errors>.</span><span class="sxs-lookup"><span data-stu-id="b704b-192">For more information, see <xref:blazor/fundamentals/handle-errors>.</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="b704b-193">Partage des ressources cross-origin (CORS)</span><span class="sxs-lookup"><span data-stu-id="b704b-193">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="b704b-194">La sécurité du navigateur empêche une page Web d’effectuer des demandes vers un autre domaine que celui qui a servi la page Web.</span><span class="sxs-lookup"><span data-stu-id="b704b-194">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="b704b-195">Cette restriction est appelée *stratégie de même origine*.</span><span class="sxs-lookup"><span data-stu-id="b704b-195">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="b704b-196">La stratégie de même origine empêche un site malveillant de lire des données sensibles à partir d’un autre site.</span><span class="sxs-lookup"><span data-stu-id="b704b-196">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="b704b-197">Pour effectuer des demandes à partir du navigateur vers un point de terminaison avec une origine différente, le *point de terminaison* doit activer le [partage des ressources Cross-Origin (cors)](https://www.w3.org/TR/cors/).</span><span class="sxs-lookup"><span data-stu-id="b704b-197">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="b704b-198">L' [ Blazor WebAssembly exemple d’application ( Blazor WebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/) illustre l’utilisation de cors dans le composant appeler l’API Web ( `Pages/CallWebAPI.razor` ).</span><span class="sxs-lookup"><span data-stu-id="b704b-198">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (`Pages/CallWebAPI.razor`).</span></span>

<span data-ttu-id="b704b-199">Pour plus d’informations sur CORS avec des demandes sécurisées dans les Blazor applications, consultez <xref:blazor/security/webassembly/additional-scenarios#cross-origin-resource-sharing-cors> .</span><span class="sxs-lookup"><span data-stu-id="b704b-199">For more information on CORS with secure requests in Blazor apps, see <xref:blazor/security/webassembly/additional-scenarios#cross-origin-resource-sharing-cors>.</span></span>

<span data-ttu-id="b704b-200">Pour obtenir des informations générales sur CORS avec les applications ASP.NET Core, consultez <xref:security/cors> .</span><span class="sxs-lookup"><span data-stu-id="b704b-200">For general information on CORS with ASP.NET Core apps, see <xref:security/cors>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b704b-201">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="b704b-201">Additional resources</span></span>

* <span data-ttu-id="b704b-202"><xref:blazor/security/webassembly/additional-scenarios>: Comprend la couverture de l’utilisation <xref:System.Net.Http.HttpClient> de pour créer des demandes d’API Web sécurisées.</span><span class="sxs-lookup"><span data-stu-id="b704b-202"><xref:blazor/security/webassembly/additional-scenarios>: Includes coverage on using <xref:System.Net.Http.HttpClient> to make secure web API requests.</span></span>
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
::: moniker range=">= aspnetcore-5.0"
* [<span data-ttu-id="b704b-203">Configuration du point de terminaison HTTPs Kestrel</span><span class="sxs-lookup"><span data-stu-id="b704b-203">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel/endpoints)
::: moniker-end
::: moniker range="< aspnetcore-5.0"
* [<span data-ttu-id="b704b-204">Configuration du point de terminaison HTTPs Kestrel</span><span class="sxs-lookup"><span data-stu-id="b704b-204">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
::: moniker-end
* [<span data-ttu-id="b704b-205">Cross Origin Resource Sharing (CORS) au W3C</span><span class="sxs-lookup"><span data-stu-id="b704b-205">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
