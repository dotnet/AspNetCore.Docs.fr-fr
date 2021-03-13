---
title: Utiliser le client gRPC avec .NET Standard 2,0
author: jamesnk
description: Découvrez comment utiliser le client .NET gRPC dans les applications et les bibliothèques qui prennent en charge .NET Standard 2,0.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 3/11/2021
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
uid: grpc/netstandard
ms.openlocfilehash: a6b066979dcdcdf648b8b0326bef47fe0e466266
ms.sourcegitcommit: 07e7ee573fe4e12be93249a385db745d714ff6ae
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103422500"
---
# <a name="use-grpc-client-with-net-standard-20"></a>Utiliser le client gRPC avec .NET Standard 2,0

Par [James Newton-King](https://twitter.com/jamesnk)

Cet article explique comment utiliser le client .NET gRPC avec des implémentations .NET qui prennent en charge [.NET Standard 2,0](/dotnet/standard/net-standard).

## <a name="net-implementations"></a>Implémentations de .NET

Les implémentations .NET suivantes (ou ultérieures) prennent en charge [GRPC .net. client](https://www.nuget.org/packages/Grpc.Net.Client/) , mais ne prennent pas en charge le protocole http/2 :

* .NET Core 2.1
* .NET Framework 4.6.1
* Mono 5.4
* Xamarin.iOS 10.14
* Xamarin.Android 8.0
* Plateforme Windows universelle 10.0.16299
* Unity 2018.1

Le client .NET gRPC peut appeler des services à partir de ces implémentations .NET avec une configuration supplémentaire.

## <a name="httphandler-configuration"></a>Configuration de HttpHandler

Un fournisseur HTTP doit être configuré à l’aide de `GrpcChannelOptions.HttpHandler` . Si un gestionnaire n’est pas configuré, une erreur est générée :

> `System.PlatformNotSupportedException`: gRPC requiert une configuration supplémentaire pour effectuer correctement les appels RPC sur les implémentations .NET qui n’ont pas de prise en charge de gRPC sur HTTP/2. Un fournisseur HTTP doit être spécifié à l’aide de `GrpcChannelOptions.HttpHandler` . Le fournisseur HTTP configuré doit prendre en charge HTTP/2 ou être configuré pour utiliser gRPC-Web.

Les implémentations .NET qui ne prennent pas en charge HTTP/2, telles que UWP, Xamarin et Unity, peuvent utiliser gRPC-Web comme alternative.

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
    {
        HttpHandler = new GrpcWebHandler(new HttpClientHandler())
    });

var client = new Greeter.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = ".NET" });
```

Pour plus d’informations, consultez [configurer gRPC-Web avec le client .net gRPC](xref:grpc/browser#configure-grpc-web-with-the-net-grpc-client).

## <a name="net-framework"></a>.NET Framework

.NET Framework a une prise en charge limitée de gRPC sur HTTP/2. Pour activer gRPC sur HTTP/2 sur .NET Framework, configurez le canal à utiliser <xref:System.Net.Http.WinHttpHandler> .

Exigences et restrictions relatives à l’utilisation de `WinHttpHandler` :

* Windows 10 Build 19622 ou version ultérieure.
* Référence au package NuGet [System .net. http. WinHttpHandler](https://www.nuget.org/packages/System.Net.Http.WinHttpHandler/) .
* Seuls les appels unaires et gRPC Server streaming sont pris en charge.
* Seuls les appels gRPC sur TLS sont pris en charge.

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
    {
        HttpHandler = new WinHttpHandler()
    });

var client = new Greeter.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = ".NET" });
```

> [!NOTE]
> La prise en charge de .NET Framework est en phase préliminaire et requiert l’utilisation d’un logiciel en version préliminaire.
> * Windows 10 Build 19622 ou version ultérieure est disponible en tant que Build [Windows Insiders](https://insider.windows.com/) .
> * La version requise de `System.Net.Http.WinHttpHandler` n’est pas disponible actuellement sur NuGet.org. La dernière version préliminaire est disponible sur [ce flux NuGet](https://pkgs.dev.azure.com/dnceng/public/_packaging/dotnet6/nuget/v3/index.json) doit être utilisé.

## <a name="grpc-c-core-library"></a>gRPC C# Core-bibliothèque

Une autre option pour .NET Framework et Xamarin consiste à utiliser [GRPC C# Core-Library](https://grpc.io/docs/languages/csharp/quickstart/) pour effectuer des appels gRPC. Il s’agit d’une bibliothèque tierce qui prend en charge l’exécution d’appels gRPC sur HTTP/2 sur .NET Framework et Xamarin. gRPC C-Core n’est pas pris en charge par Microsoft.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:grpc/client>
* <xref:grpc/browser>
* [gRPC C# Core-bibliothèque](https://grpc.io/docs/languages/csharp/quickstart/)
