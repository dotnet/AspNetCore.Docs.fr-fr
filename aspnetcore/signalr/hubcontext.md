---
title: ':::no-loc(SignalR)::: HubContext'
author: bradygaster
description: 'Découvrez comment utiliser le service ASP.NET Core :::no-loc(SignalR)::: HubContext pour envoyer des notifications à des clients en dehors d’un concentrateur.'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/hubcontext
ms.openlocfilehash: 91d02ea9e15a2c3910c3b10159bf5b1523c8e271
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058179"
---
# <a name="send-messages-from-outside-a-hub"></a><span data-ttu-id="51e07-103">Envoyer des messages provenant de l’extérieur d’un concentrateur</span><span class="sxs-lookup"><span data-stu-id="51e07-103">Send messages from outside a hub</span></span>

<span data-ttu-id="51e07-104">Par [Mikael Mengistu](https://twitter.com/MikaelM_12)</span><span class="sxs-lookup"><span data-stu-id="51e07-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="51e07-105">Le :::no-loc(SignalR)::: Hub est l’abstraction principale pour l’envoi de messages aux clients connectés au :::no-loc(SignalR)::: serveur.</span><span class="sxs-lookup"><span data-stu-id="51e07-105">The :::no-loc(SignalR)::: hub is the core abstraction for sending messages to clients connected to the :::no-loc(SignalR)::: server.</span></span> <span data-ttu-id="51e07-106">Il est également possible d’envoyer des messages à partir d’autres emplacements dans votre application à l’aide du `IHubContext` service.</span><span class="sxs-lookup"><span data-stu-id="51e07-106">It's also possible to send messages from other places in your app using the `IHubContext` service.</span></span> <span data-ttu-id="51e07-107">Cet article explique comment accéder à un :::no-loc(SignalR)::: `IHubContext` pour envoyer des notifications à des clients en dehors d’un concentrateur.</span><span class="sxs-lookup"><span data-stu-id="51e07-107">This article explains how to access a :::no-loc(SignalR)::: `IHubContext` to send notifications to clients from outside a hub.</span></span>

<span data-ttu-id="51e07-108">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(procédure de téléchargement)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="51e07-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="get-an-instance-of-ihubcontext"></a><span data-ttu-id="51e07-109">Obtient une instance de IHubContext</span><span class="sxs-lookup"><span data-stu-id="51e07-109">Get an instance of IHubContext</span></span>

<span data-ttu-id="51e07-110">Dans ASP.NET Core :::no-loc(SignalR)::: , vous pouvez accéder à une instance de `IHubContext` via l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="51e07-110">In ASP.NET Core :::no-loc(SignalR):::, you can access an instance of `IHubContext` via dependency injection.</span></span> <span data-ttu-id="51e07-111">Vous pouvez injecter une instance de `IHubContext` dans un contrôleur, un intergiciel ou un autre service d’injection de contrôle.</span><span class="sxs-lookup"><span data-stu-id="51e07-111">You can inject an instance of `IHubContext` into a controller, middleware, or other DI service.</span></span> <span data-ttu-id="51e07-112">Utilisez l’instance pour envoyer des messages aux clients.</span><span class="sxs-lookup"><span data-stu-id="51e07-112">Use the instance to send messages to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="51e07-113">Cela diffère de ASP.NET 4. x :::no-loc(SignalR)::: qui utilisait GlobalHost pour fournir l’accès à `IHubContext` .</span><span class="sxs-lookup"><span data-stu-id="51e07-113">This differs from ASP.NET 4.x :::no-loc(SignalR)::: which used GlobalHost to provide access to the `IHubContext`.</span></span> <span data-ttu-id="51e07-114">ASP.NET Core a une infrastructure d’injection de dépendances qui supprime la nécessité de ce Singleton global.</span><span class="sxs-lookup"><span data-stu-id="51e07-114">ASP.NET Core has a dependency injection framework that removes the need for this global singleton.</span></span>

### <a name="inject-an-instance-of-ihubcontext-in-a-controller"></a><span data-ttu-id="51e07-115">Injecter une instance de IHubContext dans un contrôleur</span><span class="sxs-lookup"><span data-stu-id="51e07-115">Inject an instance of IHubContext in a controller</span></span>

<span data-ttu-id="51e07-116">Vous pouvez injecter une instance de `IHubContext` dans un contrôleur en l’ajoutant à votre constructeur :</span><span class="sxs-lookup"><span data-stu-id="51e07-116">You can inject an instance of `IHubContext` into a controller by adding it to your constructor:</span></span>

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=12-19,57)]

<span data-ttu-id="51e07-117">Désormais, avec l’accès à une instance de `IHubContext` , vous pouvez appeler les méthodes de concentrateur comme si vous étiez dans le Hub lui-même.</span><span class="sxs-lookup"><span data-stu-id="51e07-117">Now, with access to an instance of `IHubContext`, you can call hub methods as if you were in the hub itself.</span></span>

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=21-25)]

### <a name="get-an-instance-of-ihubcontext-in-middleware"></a><span data-ttu-id="51e07-118">Obtient une instance de IHubContext dans l’intergiciel (middleware)</span><span class="sxs-lookup"><span data-stu-id="51e07-118">Get an instance of IHubContext in middleware</span></span>

<span data-ttu-id="51e07-119">Accédez à `IHubContext` dans le pipeline de l’intergiciel (middleware) comme suit :</span><span class="sxs-lookup"><span data-stu-id="51e07-119">Access the `IHubContext` within the middleware pipeline like so:</span></span>

```csharp
app.Use(async (context, next) =>
{
    var hubContext = context.RequestServices
                            .GetRequiredService<IHubContext<ChatHub>>();
    //...
    
    if (next != null)
    {
        await next.Invoke();
    }
});
```

> [!NOTE]
> <span data-ttu-id="51e07-120">Lorsque les méthodes de concentrateur sont appelées à partir de l’extérieur de la `Hub` classe, aucun appelant n’est associé à l’appel.</span><span class="sxs-lookup"><span data-stu-id="51e07-120">When hub methods are called from outside of the `Hub` class, there's no caller associated with the invocation.</span></span> <span data-ttu-id="51e07-121">Par conséquent, il n’existe aucun accès `ConnectionId` aux `Caller` Propriétés, et `Others` .</span><span class="sxs-lookup"><span data-stu-id="51e07-121">Therefore, there's no access to the `ConnectionId`, `Caller`, and `Others` properties.</span></span>

### <a name="get-an-instance-of-ihubcontext-from-ihost"></a><span data-ttu-id="51e07-122">Obtient une instance de IHubContext à partir de IHost</span><span class="sxs-lookup"><span data-stu-id="51e07-122">Get an instance of IHubContext from IHost</span></span>

<span data-ttu-id="51e07-123">L’accès `IHubContext` à un à partir de l’hôte Web est utile pour l’intégration à des zones en dehors de ASP.net Core, par exemple, à l’aide d’infrastructures d’injection de dépendances tierces :</span><span class="sxs-lookup"><span data-stu-id="51e07-123">Accessing an `IHubContext` from the web host is useful for integrating with areas outside of ASP.NET Core, for example, using third-party dependency injection frameworks:</span></span>

```csharp
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = CreateHostBuilder(args).Build();
            var hubContext = host.Services.GetService(typeof(IHubContext<ChatHub>));
            host.Run();
        }

        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureWebHostDefaults(webBuilder => {
                    webBuilder.UseStartup<Startup>();
                });
    }
```

### <a name="inject-a-strongly-typed-hubcontext"></a><span data-ttu-id="51e07-124">Injecter un HubContext fortement typé</span><span class="sxs-lookup"><span data-stu-id="51e07-124">Inject a strongly-typed HubContext</span></span>

<span data-ttu-id="51e07-125">Pour injecter un HubContext fortement typé, vérifiez que votre Hub hérite de `Hub<T>` .</span><span class="sxs-lookup"><span data-stu-id="51e07-125">To inject a strongly-typed HubContext, ensure your Hub inherits from `Hub<T>`.</span></span> <span data-ttu-id="51e07-126">Injectez-le à l’aide de l' `IHubContext<THub, T>` interface plutôt que `IHubContext<THub>` .</span><span class="sxs-lookup"><span data-stu-id="51e07-126">Inject it using the `IHubContext<THub, T>` interface rather than `IHubContext<THub>`.</span></span>

```csharp
public class ChatController : Controller
{
    public IHubContext<ChatHub, IChatClient> _strongChatHubContext { get; }

    public ChatController(IHubContext<ChatHub, IChatClient> chatHubContext)
    {
        _strongChatHubContext = chatHubContext;
    }

    public async Task SendMessage(string user, string message)
    {
        await _strongChatHubContext.Clients.All.ReceiveMessage(user, message);
    }
}
```

<span data-ttu-id="51e07-127">Pour plus d’informations, consultez [hubs fortement typés](xref:signalr/hubs#strongly-typed-hubs) .</span><span class="sxs-lookup"><span data-stu-id="51e07-127">See [Strongly typed hubs](xref:signalr/hubs#strongly-typed-hubs) for more information.</span></span>

## <a name="related-resources"></a><span data-ttu-id="51e07-128">Ressources associées</span><span class="sxs-lookup"><span data-stu-id="51e07-128">Related resources</span></span>

* [<span data-ttu-id="51e07-129">Prise en main</span><span class="sxs-lookup"><span data-stu-id="51e07-129">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="51e07-130">Hubs</span><span class="sxs-lookup"><span data-stu-id="51e07-130">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="51e07-131">Publication dans Azure</span><span class="sxs-lookup"><span data-stu-id="51e07-131">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
