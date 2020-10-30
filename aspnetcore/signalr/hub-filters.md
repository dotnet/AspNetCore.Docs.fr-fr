---
title: 'Utiliser des filtres de Hub dans ASP.NET Core :::no-loc(SignalR):::'
author: brecon
description: 'Découvrez comment utiliser les filtres de Hub dans ASP.NET Core :::no-loc(SignalR)::: .'
monikerRange: '>= aspnetcore-5.0'
ms.author: brecon
ms.custom: mvc
ms.date: 05/22/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: signalr/hub-filters
ms.openlocfilehash: 5a4cb5122080b72875ac11cf2e682162d017d7b9
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052719"
---
# <a name="use-hub-filters-in-aspnet-core-no-locsignalr"></a><span data-ttu-id="9e9d7-103">Utiliser des filtres de Hub dans ASP.NET Core :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="9e9d7-103">Use hub filters in ASP.NET Core :::no-loc(SignalR):::</span></span>

<span data-ttu-id="9e9d7-104">Filtres de concentrateur :</span><span class="sxs-lookup"><span data-stu-id="9e9d7-104">Hub filters:</span></span>

* <span data-ttu-id="9e9d7-105">Sont disponibles dans ASP.NET Core 5,0 ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="9e9d7-105">Are available in ASP.NET Core 5.0 or later.</span></span>
* <span data-ttu-id="9e9d7-106">Autorisez l’exécution de la logique avant et après que les méthodes de concentrateur soient appelées par les clients.</span><span class="sxs-lookup"><span data-stu-id="9e9d7-106">Allow logic to run before and after hub methods are invoked by clients.</span></span>

<span data-ttu-id="9e9d7-107">Cet article fournit des conseils pour l’écriture et l’utilisation de filtres Hub.</span><span class="sxs-lookup"><span data-stu-id="9e9d7-107">This article provides guidance for writing and using hub filters.</span></span>

## <a name="configure-hub-filters"></a><span data-ttu-id="9e9d7-108">Configurer des filtres de Hub</span><span class="sxs-lookup"><span data-stu-id="9e9d7-108">Configure hub filters</span></span>

<span data-ttu-id="9e9d7-109">Les filtres de concentrateur peuvent être appliqués globalement ou par type de Hub.</span><span class="sxs-lookup"><span data-stu-id="9e9d7-109">Hub filters can be applied globally or per hub type.</span></span> <span data-ttu-id="9e9d7-110">L’ordre dans lequel les filtres sont ajoutés est l’ordre dans lequel les filtres s’exécutent.</span><span class="sxs-lookup"><span data-stu-id="9e9d7-110">The order in which filters are added is the order in which the filters run.</span></span> <span data-ttu-id="9e9d7-111">Les filtres de concentrateur globaux s’exécutent avant les filtres de concentrateur local.</span><span class="sxs-lookup"><span data-stu-id="9e9d7-111">Global hub filters run before local hub filters.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Add:::no-loc(SignalR):::(options =>
    {
        // Global filters will run first
        options.AddFilter<CustomFilter>();
    }).AddHubOptions<ChatHub>(options =>
    {
        // Local filters will run second
        options.AddFilter<CustomFilter2>();
    });
}
```

<span data-ttu-id="9e9d7-112">Un filtre de concentrateur peut être ajouté de l’une des manières suivantes :</span><span class="sxs-lookup"><span data-stu-id="9e9d7-112">A hub filter can be added in one of the following ways:</span></span>

* <span data-ttu-id="9e9d7-113">Ajoutez un filtre par type concret :</span><span class="sxs-lookup"><span data-stu-id="9e9d7-113">Add a filter by concrete type:</span></span>

    ```csharp
    hubOptions.AddFilter<TFilter>();
    ```

    <span data-ttu-id="9e9d7-114">Ce sera résolu à partir de l’injection de dépendances (DI) ou du type activé.</span><span class="sxs-lookup"><span data-stu-id="9e9d7-114">This will be resolved from dependency injection (DI) or type activated.</span></span>

* <span data-ttu-id="9e9d7-115">Ajoutez un filtre par type d’exécution :</span><span class="sxs-lookup"><span data-stu-id="9e9d7-115">Add a filter by runtime type:</span></span>

    ```csharp
    hubOptions.AddFilter(typeof(TFilter));
    ```

    <span data-ttu-id="9e9d7-116">Ce sera résolu à partir de DI ou du type activé.</span><span class="sxs-lookup"><span data-stu-id="9e9d7-116">This will be resolved from DI or type activated.</span></span>

* <span data-ttu-id="9e9d7-117">Ajoutez un filtre par instance :</span><span class="sxs-lookup"><span data-stu-id="9e9d7-117">Add a filter by instance:</span></span>

    ```csharp
    hubOptions.AddFilter(new MyFilter());
    ```

    <span data-ttu-id="9e9d7-118">Cette instance sera utilisée comme un singleton.</span><span class="sxs-lookup"><span data-stu-id="9e9d7-118">This instance will be used like a singleton.</span></span> <span data-ttu-id="9e9d7-119">Tous les appels de méthode de concentrateur utiliseront la même instance.</span><span class="sxs-lookup"><span data-stu-id="9e9d7-119">All hub method invocations will use the same instance.</span></span>

<span data-ttu-id="9e9d7-120">Les filtres de concentrateur sont créés et supprimés par appel de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="9e9d7-120">Hub filters are created and disposed per hub invocation.</span></span> <span data-ttu-id="9e9d7-121">Si vous souhaitez stocker l’état global dans le filtre, ou aucun État, ajoutez le type de filtre Hub à DI comme singleton pour obtenir de meilleures performances.</span><span class="sxs-lookup"><span data-stu-id="9e9d7-121">If you want to store global state in the filter, or no state, add the hub filter type to DI as a singleton for better performance.</span></span> <span data-ttu-id="9e9d7-122">Vous pouvez également ajouter le filtre en tant qu’instance si vous le pouvez.</span><span class="sxs-lookup"><span data-stu-id="9e9d7-122">Alternatively, add the filter as an instance if you can.</span></span>

## <a name="create-hub-filters"></a><span data-ttu-id="9e9d7-123">Créer des filtres de Hub</span><span class="sxs-lookup"><span data-stu-id="9e9d7-123">Create hub filters</span></span>

<span data-ttu-id="9e9d7-124">Créez un filtre en déclarant une classe qui hérite de `IHubFilter` et ajoutez la `InvokeMethodAsync` méthode.</span><span class="sxs-lookup"><span data-stu-id="9e9d7-124">Create a filter by declaring a class that inherits from `IHubFilter`, and add the `InvokeMethodAsync` method.</span></span> <span data-ttu-id="9e9d7-125">Il existe également `OnConnectedAsync` et `OnDisconnectedAsync` peuvent éventuellement être implémentés pour encapsuler `OnConnectedAsync` les `OnDisconnectedAsync` méthodes de concentrateur et, respectivement.</span><span class="sxs-lookup"><span data-stu-id="9e9d7-125">There is also `OnConnectedAsync` and `OnDisconnectedAsync` that can optionally be implemented to wrap the `OnConnectedAsync` and `OnDisconnectedAsync` hub methods respectively.</span></span>

```csharp
public class CustomFilter : IHubFilter
{
    public async ValueTask<object> InvokeMethodAsync(
        HubInvocationContext invocationContext, Func<HubInvocationContext, ValueTask<object>> next)
    {
        Console.WriteLine($"Calling hub method '{invocationContext.HubMethodName}'");
        try
        {
            return await next(invocationContext);
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Exception calling '{invocationContext.HubMethodName}'");
            throw ex;
        }
    }

    // Optional method
    public Task OnConnectedAsync(HubLifetimeContext context, Func<HubLifetimeContext, Task> next)
    {
        return next(context);
    }

    // Optional method
    public Task OnDisconnectedAsync(
        HubLifetimeContext context, Exception exception, Func<HubLifetimeContext, Exception, Task> next)
    {
        return next(context, exception);
    }
}
```

<span data-ttu-id="9e9d7-126">Les filtres sont très similaires à l’intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="9e9d7-126">Filters are very similar to middleware.</span></span> <span data-ttu-id="9e9d7-127">La `next` méthode appelle le filtre suivant.</span><span class="sxs-lookup"><span data-stu-id="9e9d7-127">The `next` method invokes the next filter.</span></span> <span data-ttu-id="9e9d7-128">Le filtre final appellera la méthode de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="9e9d7-128">The final filter will invoke the hub method.</span></span> <span data-ttu-id="9e9d7-129">Les filtres peuvent également stocker le résultat de la `next` logique en attente et à l’exécution une fois que la méthode de concentrateur a été appelée avant de retourner le résultat de `next` .</span><span class="sxs-lookup"><span data-stu-id="9e9d7-129">Filters can also store the result from awaiting `next` and run logic after the hub method has been called before returning the result from `next`.</span></span>

<span data-ttu-id="9e9d7-130">Pour ignorer un appel de méthode de concentrateur dans un filtre, levez une exception de type `HubException` au lieu d’appeler `next` .</span><span class="sxs-lookup"><span data-stu-id="9e9d7-130">To skip a hub method invocation in a filter, throw an exception of type `HubException` instead of calling `next`.</span></span> <span data-ttu-id="9e9d7-131">Le client recevra une erreur s’il attendait un résultat.</span><span class="sxs-lookup"><span data-stu-id="9e9d7-131">The client will receive an error if it was expecting a result.</span></span>

## <a name="use-hub-filters"></a><span data-ttu-id="9e9d7-132">Utiliser des filtres de Hub</span><span class="sxs-lookup"><span data-stu-id="9e9d7-132">Use hub filters</span></span>

<span data-ttu-id="9e9d7-133">Lors de l’écriture de la logique de filtre, essayez de le rendre générique en utilisant des attributs sur les méthodes de concentrateur au lieu de vérifier les noms de méthode de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="9e9d7-133">When writing the filter logic, try to make it generic by using attributes on hub methods instead of checking for hub method names.</span></span>

<span data-ttu-id="9e9d7-134">Imaginez un filtre qui vérifie un argument de méthode de concentrateur pour les expressions interdites et remplace toute expression avec laquelle il trouve `***` .</span><span class="sxs-lookup"><span data-stu-id="9e9d7-134">Consider a filter that will check a hub method argument for banned phrases and replace any phrases it finds with `***`.</span></span>
<span data-ttu-id="9e9d7-135">Pour cet exemple, supposons qu’une `LanguageFilterAttribute` classe soit définie.</span><span class="sxs-lookup"><span data-stu-id="9e9d7-135">For this example, assume a `LanguageFilterAttribute` class is defined.</span></span> <span data-ttu-id="9e9d7-136">La classe a une propriété nommée `FilterArgument` qui peut être définie lors de l’utilisation de l’attribut.</span><span class="sxs-lookup"><span data-stu-id="9e9d7-136">The class has a property named `FilterArgument` that can be set when using the attribute.</span></span>

1. <span data-ttu-id="9e9d7-137">Placez l’attribut sur la méthode de concentrateur qui a un argument de chaîne à nettoyer :</span><span class="sxs-lookup"><span data-stu-id="9e9d7-137">Place the attribute on the hub method that has a string argument to be cleaned:</span></span>

    ```csharp
    public class ChatHub
    {
        [LanguageFilter(filterArgument: 0)]
        public async Task SendMessage(string message, string username)
        {
            await Clients.All.SendAsync("SendMessage", $"{username} says: {message}");
        }
    }
    ```

1. <span data-ttu-id="9e9d7-138">Définissez un filtre de concentrateur pour vérifier l’attribut et remplacer les expressions interdites dans un argument de méthode de concentrateur par `**_` :</span><span class="sxs-lookup"><span data-stu-id="9e9d7-138">Define a hub filter to check for the attribute and replace banned phrases in a hub method argument with `**_`:</span></span>

    ```csharp
    public class LanguageFilter : IHubFilter
    {
        // populated from a file or inline
        private List<string> bannedPhrases = new List<string> { "async void", ".Result" };

        public async ValueTask<object> InvokeMethodAsync(HubInvocationContext invocationContext, 
            Func<HubInvocationContext, ValueTask<object>> next)
        {
            var languageFilter = (LanguageFilterAttribute)Attribute.GetCustomAttribute(
                invocationContext.HubMethod, typeof(LanguageFilterAttribute));
            if (languageFilter != null &&
                invocationContext.HubMethodArguments.Count > languageFilter.FilterArgument &&
                invocationContext.HubMethodArguments[languageFilter.FilterArgument] is string str)
            {
                foreach (var bannedPhrase in bannedPhrases)
                {
                    str = str.Replace(bannedPhrase, "_**");
                }

                arguments = invocationContext.HubMethodArguments.ToArray();
                arguments[languageFilter.FilterArgument] = str;
                invocationContext = new HubInvocationContext(invocationContext.Context,
                    invocationContext.ServiceProvider,
                    invocationContext.Hub,
                    invocationContext.HubMethod,
                    arguments);
            }

            return await next(invocationContext);
        }
    }
    ```

1. <span data-ttu-id="9e9d7-139">Inscrivez le filtre de concentrateur dans la `Startup.ConfigureServices` méthode.</span><span class="sxs-lookup"><span data-stu-id="9e9d7-139">Register the hub filter in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="9e9d7-140">Pour éviter de réinitialiser la liste des expressions interdites pour chaque appel, le filtre de concentrateur est inscrit en tant que singleton :</span><span class="sxs-lookup"><span data-stu-id="9e9d7-140">To avoid reinitializing the banned phrases list for every invocation, the hub filter is registered as a singleton:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Add:::no-loc(SignalR):::(hubOptions =>
        {
            hubOptions.AddFilter<LanguageFilter>();
        });
    
        services.AddSingleton<LanguageFilter>();
    }
    ```

## <a name="the-hubinvocationcontext-object"></a><span data-ttu-id="9e9d7-141">Objet HubInvocationContext</span><span class="sxs-lookup"><span data-stu-id="9e9d7-141">The HubInvocationContext object</span></span>

<span data-ttu-id="9e9d7-142">`HubInvocationContext`Contient des informations pour l’appel de méthode de concentrateur actuel.</span><span class="sxs-lookup"><span data-stu-id="9e9d7-142">The `HubInvocationContext` contains information for the current hub method invocation.</span></span>

| <span data-ttu-id="9e9d7-143">Propriété</span><span class="sxs-lookup"><span data-stu-id="9e9d7-143">Property</span></span> | <span data-ttu-id="9e9d7-144">Description</span><span class="sxs-lookup"><span data-stu-id="9e9d7-144">Description</span></span> | <span data-ttu-id="9e9d7-145">Type</span><span class="sxs-lookup"><span data-stu-id="9e9d7-145">Type</span></span> |
| ------ | ------ | ----------- |
| `Context ` | <span data-ttu-id="9e9d7-146">`HubCallerContext`Contient des informations sur la connexion.</span><span class="sxs-lookup"><span data-stu-id="9e9d7-146">The `HubCallerContext` contains information about the connection.</span></span> | `HubCallerContext` |
| `Hub` | <span data-ttu-id="9e9d7-147">Instance du Hub utilisé pour cet appel de méthode de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="9e9d7-147">The instance of the Hub being used for this hub method invocation.</span></span> | `Hub` |
| `HubMethodName` | <span data-ttu-id="9e9d7-148">Nom de la méthode de concentrateur appelée.</span><span class="sxs-lookup"><span data-stu-id="9e9d7-148">The name of the hub method being invoked.</span></span> | `string` |
| `HubMethodArguments` | <span data-ttu-id="9e9d7-149">Liste des arguments passés à la méthode de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="9e9d7-149">The list of arguments being passed to the hub method.</span></span> | `IReadOnlyList<string>` |
| `ServiceProvider` | <span data-ttu-id="9e9d7-150">Fournisseur de services étendus pour cet appel de méthode de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="9e9d7-150">The scoped service provider for this hub method invocation.</span></span> | `IServiceProvider` |
| `HubMethod` | <span data-ttu-id="9e9d7-151">Informations de méthode de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="9e9d7-151">The hub method information.</span></span> | `MethodInfo` |

## <a name="the-hublifetimecontext-object"></a><span data-ttu-id="9e9d7-152">Objet HubLifetimeContext</span><span class="sxs-lookup"><span data-stu-id="9e9d7-152">The HubLifetimeContext object</span></span>

<span data-ttu-id="9e9d7-153">`HubLifetimeContext`Contient des informations sur les `OnConnectedAsync` `OnDisconnectedAsync` méthodes de concentrateur et.</span><span class="sxs-lookup"><span data-stu-id="9e9d7-153">The `HubLifetimeContext` contains information for the `OnConnectedAsync` and `OnDisconnectedAsync` hub methods.</span></span>

| <span data-ttu-id="9e9d7-154">Propriété</span><span class="sxs-lookup"><span data-stu-id="9e9d7-154">Property</span></span> | <span data-ttu-id="9e9d7-155">Description</span><span class="sxs-lookup"><span data-stu-id="9e9d7-155">Description</span></span> | <span data-ttu-id="9e9d7-156">Type</span><span class="sxs-lookup"><span data-stu-id="9e9d7-156">Type</span></span> |
| ------ | ------ | ----------- |
| `Context ` | <span data-ttu-id="9e9d7-157">`HubCallerContext`Contient des informations sur la connexion.</span><span class="sxs-lookup"><span data-stu-id="9e9d7-157">The `HubCallerContext` contains information about the connection.</span></span> | `HubCallerContext` |
| `Hub` | <span data-ttu-id="9e9d7-158">Instance du Hub utilisé pour cet appel de méthode de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="9e9d7-158">The instance of the Hub being used for this hub method invocation.</span></span> | `Hub` |
| `ServiceProvider` | <span data-ttu-id="9e9d7-159">Fournisseur de services étendus pour cet appel de méthode de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="9e9d7-159">The scoped service provider for this hub method invocation.</span></span> | `IServiceProvider` |

## <a name="authorization-and-filters"></a><span data-ttu-id="9e9d7-160">Autorisations et filtres</span><span class="sxs-lookup"><span data-stu-id="9e9d7-160">Authorization and filters</span></span>

<span data-ttu-id="9e9d7-161">Les [attributs d’autorisation sur les méthodes de concentrateur](xref:signalr/authn-and-authz#use-authorization-handlers-to-customize-hub-method-authorization) s’exécutent avant les filtres de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="9e9d7-161">[Authorize attributes on hub methods](xref:signalr/authn-and-authz#use-authorization-handlers-to-customize-hub-method-authorization) run before hub filters.</span></span>
