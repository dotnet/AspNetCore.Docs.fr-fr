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
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059882"
---
# <a name="inter-process-communication-with-grpc"></a>Communication entre processus avec gRPC

Par [James Newton-King](https://twitter.com/jamesnk)

les appels gRPC entre un client et un service sont généralement envoyés via des sockets TCP. TCP a été conçu pour communiquer sur un réseau. La [communication entre processus (IPC)](https://wikipedia.org/wiki/Inter-process_communication) est plus efficace que TCP lorsque le client et le service se trouvent sur le même ordinateur. Ce document explique comment utiliser gRPC avec des transports personnalisés dans des scénarios IPC.

## <a name="server-configuration"></a>Configurer le serveur

Les transports personnalisés sont pris en charge par [Kestrel](xref:fundamentals/servers/kestrel). Kestrel est configuré dans *Program.cs* :

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

L’exemple précédent :

* Configure les points de terminaison de Kestrel dans `ConfigureKestrel` .
* Appelle <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> pour écouter un [Socket de domaine UNIX (UDS)](https://wikipedia.org/wiki/Unix_domain_socket) avec le chemin d’accès spécifié.

Kestrel offre une prise en charge intégrée des points de terminaison UDS. Les UDS sont pris en charge sur Linux, macOS et les [versions modernes de Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).

## <a name="client-configuration"></a>Configuration de client

`GrpcChannel` prend en charge l’exécution d’appels gRPC sur des transports personnalisés. Lorsqu’un canal est créé, il peut être configuré avec un `SocketsHttpHandler` qui a un personnalisé `ConnectCallback` . Le rappel permet au client d’établir des connexions sur des transports personnalisés, puis d’envoyer des requêtes HTTP sur ce transport.

> [!IMPORTANT]
> `SocketsHttpHandler.ConnectCallback` est une nouvelle API dans .NET 5 Release Candidate 2.

Exemple de fabrique de connexion de sockets de domaine UNIX :

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

Utilisation de la fabrique de connexion personnalisée pour créer un canal :

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

Les canaux créés à l’aide du code précédent envoient des appels gRPC sur des sockets de domaine UNIX. La prise en charge d’autres technologies IPC peut être implémentée à l’aide de l’extensibilité dans Kestrel et `SocketsHttpHandler` .
