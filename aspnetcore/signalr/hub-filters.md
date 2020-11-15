---
title: Utiliser des filtres de Hub dans ASP.NET Core SignalR
author: brecon
description: Découvrez comment utiliser les filtres de Hub dans ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-5.0'
ms.author: brecon
ms.custom: mvc
ms.date: 05/22/2020
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
uid: signalr/hub-filters
ms.openlocfilehash: 9b131d8ec13204525f39263afaf506e336373a7c
ms.sourcegitcommit: 827e8be18cebbcc09b467c089e17fa6f5e430cb2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2020
ms.locfileid: "94634572"
---
# <a name="use-hub-filters-in-aspnet-core-no-locsignalr"></a>Utiliser des filtres de Hub dans ASP.NET Core SignalR

Filtres de concentrateur :

* Sont disponibles dans ASP.NET Core 5,0 ou version ultérieure.
* Autorisez l’exécution de la logique avant et après que les méthodes de concentrateur soient appelées par les clients.

Cet article fournit des conseils pour l’écriture et l’utilisation de filtres Hub.

## <a name="configure-hub-filters"></a>Configurer des filtres de Hub

Les filtres de concentrateur peuvent être appliqués globalement ou par type de Hub. L’ordre dans lequel les filtres sont ajoutés est l’ordre dans lequel les filtres s’exécutent. Les filtres de concentrateur globaux s’exécutent avant les filtres de concentrateur local.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(options =>
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

Un filtre de concentrateur peut être ajouté de l’une des manières suivantes :

* Ajoutez un filtre par type concret :

    ```csharp
    hubOptions.AddFilter<TFilter>();
    ```

    Ce sera résolu à partir de l’injection de dépendances (DI) ou du type activé.

* Ajoutez un filtre par type d’exécution :

    ```csharp
    hubOptions.AddFilter(typeof(TFilter));
    ```

    Ce sera résolu à partir de DI ou du type activé.

* Ajoutez un filtre par instance :

    ```csharp
    hubOptions.AddFilter(new MyFilter());
    ```

    Cette instance sera utilisée comme un singleton. Tous les appels de méthode de concentrateur utiliseront la même instance.

Les filtres de concentrateur sont créés et supprimés par appel de concentrateur. Si vous souhaitez stocker l’état global dans le filtre, ou aucun État, ajoutez le type de filtre Hub à DI comme singleton pour obtenir de meilleures performances. Vous pouvez également ajouter le filtre en tant qu’instance si vous le pouvez.

## <a name="create-hub-filters"></a>Créer des filtres de Hub

Créez un filtre en déclarant une classe qui hérite de `IHubFilter` et ajoutez la `InvokeMethodAsync` méthode. Il existe également `OnConnectedAsync` et `OnDisconnectedAsync` peuvent éventuellement être implémentés pour encapsuler `OnConnectedAsync` les `OnDisconnectedAsync` méthodes de concentrateur et, respectivement.

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
            Console.WriteLine($"Exception calling '{invocationContext.HubMethodName}': {ex}");
            throw;
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

Les filtres sont très similaires à l’intergiciel (middleware). La `next` méthode appelle le filtre suivant. Le filtre final appellera la méthode de concentrateur. Les filtres peuvent également stocker le résultat de la `next` logique en attente et à l’exécution une fois que la méthode de concentrateur a été appelée avant de retourner le résultat de `next` .

Pour ignorer un appel de méthode de concentrateur dans un filtre, levez une exception de type `HubException` au lieu d’appeler `next` . Le client recevra une erreur s’il attendait un résultat.

## <a name="use-hub-filters"></a>Utiliser des filtres de Hub

Lors de l’écriture de la logique de filtre, essayez de le rendre générique en utilisant des attributs sur les méthodes de concentrateur au lieu de vérifier les noms de méthode de concentrateur.

Imaginez un filtre qui vérifie un argument de méthode de concentrateur pour les expressions interdites et remplace toute expression avec laquelle il trouve `***` .
Pour cet exemple, supposons qu’une `LanguageFilterAttribute` classe soit définie. La classe a une propriété nommée `FilterArgument` qui peut être définie lors de l’utilisation de l’attribut.

1. Placez l’attribut sur la méthode de concentrateur qui a un argument de chaîne à nettoyer :

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

1. Définissez un filtre de concentrateur pour vérifier l’attribut et remplacer les expressions interdites dans un argument de méthode de concentrateur par `**_` :

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

1. Inscrivez le filtre de concentrateur dans la `Startup.ConfigureServices` méthode. Pour éviter de réinitialiser la liste des expressions interdites pour chaque appel, le filtre de concentrateur est inscrit en tant que singleton :

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSignalR(hubOptions =>
        {
            hubOptions.AddFilter<LanguageFilter>();
        });
    
        services.AddSingleton<LanguageFilter>();
    }
    ```

## <a name="the-hubinvocationcontext-object"></a>Objet HubInvocationContext

`HubInvocationContext`Contient des informations pour l’appel de méthode de concentrateur actuel.

| Propriété | Description | Type |
| ------ | ------ | ----------- |
| `Context ` | `HubCallerContext`Contient des informations sur la connexion. | `HubCallerContext` |
| `Hub` | Instance du Hub utilisé pour cet appel de méthode de concentrateur. | `Hub` |
| `HubMethodName` | Nom de la méthode de concentrateur appelée. | `string` |
| `HubMethodArguments` | Liste des arguments passés à la méthode de concentrateur. | `IReadOnlyList<string>` |
| `ServiceProvider` | Fournisseur de services étendus pour cet appel de méthode de concentrateur. | `IServiceProvider` |
| `HubMethod` | Informations de méthode de concentrateur. | `MethodInfo` |

## <a name="the-hublifetimecontext-object"></a>Objet HubLifetimeContext

`HubLifetimeContext`Contient des informations sur les `OnConnectedAsync` `OnDisconnectedAsync` méthodes de concentrateur et.

| Propriété | Description | Type |
| ------ | ------ | ----------- |
| `Context ` | `HubCallerContext`Contient des informations sur la connexion. | `HubCallerContext` |
| `Hub` | Instance du Hub utilisé pour cet appel de méthode de concentrateur. | `Hub` |
| `ServiceProvider` | Fournisseur de services étendus pour cet appel de méthode de concentrateur. | `IServiceProvider` |

## <a name="authorization-and-filters"></a>Autorisations et filtres

Les [attributs d’autorisation sur les méthodes de concentrateur](xref:signalr/authn-and-authz#use-authorization-handlers-to-customize-hub-method-authorization) s’exécutent avant les filtres de concentrateur.
