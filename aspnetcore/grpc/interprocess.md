---
title: Communication entre processus avec gRPC
author: jamesnk
description: Découvrez comment utiliser gRPC pour la communication entre processus.
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.date: 09/16/2020
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
uid: grpc/interprocess
ms.openlocfilehash: d806a340d8540fce8af6ccc6ff68325e4b733922
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059882"
---
# <a name="inter-process-communication-with-grpc"></a><span data-ttu-id="f9706-103">Communication entre processus avec gRPC</span><span class="sxs-lookup"><span data-stu-id="f9706-103">Inter-process communication with gRPC</span></span>

<span data-ttu-id="f9706-104">Par [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="f9706-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="f9706-105">les appels gRPC entre un client et un service sont généralement envoyés via des sockets TCP.</span><span class="sxs-lookup"><span data-stu-id="f9706-105">gRPC calls between a client and service are usually sent over TCP sockets.</span></span> <span data-ttu-id="f9706-106">TCP a été conçu pour communiquer sur un réseau.</span><span class="sxs-lookup"><span data-stu-id="f9706-106">TCP was designed for communicating across a network.</span></span> <span data-ttu-id="f9706-107">La [communication entre processus (IPC)](https://wikipedia.org/wiki/Inter-process_communication) est plus efficace que TCP lorsque le client et le service se trouvent sur le même ordinateur.</span><span class="sxs-lookup"><span data-stu-id="f9706-107">[Inter-process communication (IPC)](https://wikipedia.org/wiki/Inter-process_communication) is more efficient than TCP when the client and service are on the same machine.</span></span> <span data-ttu-id="f9706-108">Ce document explique comment utiliser gRPC avec des transports personnalisés dans des scénarios IPC.</span><span class="sxs-lookup"><span data-stu-id="f9706-108">This document explains how to use gRPC with custom transports in IPC scenarios.</span></span>

## <a name="server-configuration"></a><span data-ttu-id="f9706-109">Configurer le serveur</span><span class="sxs-lookup"><span data-stu-id="f9706-109">Server configuration</span></span>

<span data-ttu-id="f9706-110">Les transports personnalisés sont pris en charge par [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="f9706-110">Custom transports are supported by [Kestrel](xref:fundamentals/servers/kestrel).</span></span> <span data-ttu-id="f9706-111">Kestrel est configuré dans *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="f9706-111">Kestrel is configured in *Program.cs*:</span></span>

```csharp
public static readonly string SocketPath = Path.Combine(Path.GetTempPath(), "socket.tmp");

public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
            webBuilder.ConfigureKestrel(options =>
            {
                if (File.Exists(SocketPath))
                {
                    File.Delete(SocketPath);
                }
                options.ListenUnixSocket(SocketPath);
            });
        });
```

<span data-ttu-id="f9706-112">L’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="f9706-112">The preceding example:</span></span>

* <span data-ttu-id="f9706-113">Configure les points de terminaison de Kestrel dans `ConfigureKestrel` .</span><span class="sxs-lookup"><span data-stu-id="f9706-113">Configures Kestrel's endpoints in `ConfigureKestrel`.</span></span>
* <span data-ttu-id="f9706-114">Appelle <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> pour écouter un [Socket de domaine UNIX (UDS)](https://wikipedia.org/wiki/Unix_domain_socket) avec le chemin d’accès spécifié.</span><span class="sxs-lookup"><span data-stu-id="f9706-114">Calls <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> to listen to a [Unix domain socket (UDS)](https://wikipedia.org/wiki/Unix_domain_socket) with the specified path.</span></span>

<span data-ttu-id="f9706-115">Kestrel offre une prise en charge intégrée des points de terminaison UDS.</span><span class="sxs-lookup"><span data-stu-id="f9706-115">Kestrel has built-in support for UDS endpoints.</span></span> <span data-ttu-id="f9706-116">Les UDS sont pris en charge sur Linux, macOS et les [versions modernes de Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).</span><span class="sxs-lookup"><span data-stu-id="f9706-116">UDS are supported on Linux, macOS and [modern versions of Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).</span></span>

## <a name="client-configuration"></a><span data-ttu-id="f9706-117">Configuration de client</span><span class="sxs-lookup"><span data-stu-id="f9706-117">Client configuration</span></span>

<span data-ttu-id="f9706-118">`GrpcChannel` prend en charge l’exécution d’appels gRPC sur des transports personnalisés.</span><span class="sxs-lookup"><span data-stu-id="f9706-118">`GrpcChannel` supports making gRPC calls over custom transports.</span></span> <span data-ttu-id="f9706-119">Lorsqu’un canal est créé, il peut être configuré avec un `SocketsHttpHandler` qui a un personnalisé `ConnectCallback` .</span><span class="sxs-lookup"><span data-stu-id="f9706-119">When a channel is created, it can be configured with a `SocketsHttpHandler` that has a custom `ConnectCallback`.</span></span> <span data-ttu-id="f9706-120">Le rappel permet au client d’établir des connexions sur des transports personnalisés, puis d’envoyer des requêtes HTTP sur ce transport.</span><span class="sxs-lookup"><span data-stu-id="f9706-120">The callback allows the client to make connections over custom transports and then send HTTP requests over that transport.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="f9706-121">`SocketsHttpHandler.ConnectCallback` est une nouvelle API dans .NET 5 Release Candidate 2.</span><span class="sxs-lookup"><span data-stu-id="f9706-121">`SocketsHttpHandler.ConnectCallback` is a new API in .NET 5 release candidate 2.</span></span>

<span data-ttu-id="f9706-122">Exemple de fabrique de connexion de sockets de domaine UNIX :</span><span class="sxs-lookup"><span data-stu-id="f9706-122">Unix domain sockets connection factory example:</span></span>

```csharp
public class UnixDomainSocketConnectionFactory
{
    private readonly EndPoint _endPoint;

    public UnixDomainSocketConnectionFactory(EndPoint endPoint)
    {
        _endPoint = endPoint;
    }

    public async ValueTask<Stream> ConnectAsync(SocketsHttpConnectionContext _,
        CancellationToken cancellationToken = default)
    {
        var socket = new Socket(AddressFamily.Unix, SocketType.Stream, ProtocolType.Unspecified);

        try
        {
            await socket.ConnectAsync(_endPoint, cancellationToken).ConfigureAwait(false);
            return new NetworkStream(socket, true);
        }
        catch
        {
            socket.Dispose();
            throw;
        }
    }
}
```

<span data-ttu-id="f9706-123">Utilisation de la fabrique de connexion personnalisée pour créer un canal :</span><span class="sxs-lookup"><span data-stu-id="f9706-123">Using the custom connection factory to create a channel:</span></span>

```csharp
public static readonly string SocketPath = Path.Combine(Path.GetTempPath(), "socket.tmp");

public static GrpcChannel CreateChannel()
{
    var udsEndPoint = new UnixDomainSocketEndPoint(SocketPath);
    var connectionFactory = new UnixDomainSocketConnectionFactory(udsEndPoint);
    var socketsHttpHandler = new SocketsHttpHandler
    {
        ConnectCallback = connectionFactory.ConnectAsync
    };

    return GrpcChannel.ForAddress("http://localhost", new GrpcChannelOptions
    {
        HttpHandler = socketsHttpHandler
    });
}
```

<span data-ttu-id="f9706-124">Les canaux créés à l’aide du code précédent envoient des appels gRPC sur des sockets de domaine UNIX.</span><span class="sxs-lookup"><span data-stu-id="f9706-124">Channels created using the preceding code send gRPC calls over Unix domain sockets.</span></span> <span data-ttu-id="f9706-125">La prise en charge d’autres technologies IPC peut être implémentée à l’aide de l’extensibilité dans Kestrel et `SocketsHttpHandler` .</span><span class="sxs-lookup"><span data-stu-id="f9706-125">Support for other IPC technologies can be implemented using the extensibility in Kestrel and `SocketsHttpHandler`.</span></span>
