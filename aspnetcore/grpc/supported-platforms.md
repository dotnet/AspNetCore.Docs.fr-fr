---
title: gRPC sur les plateformes prises en charge par .NET
author: jamesnk
description: En savoir plus sur les plateformes prises en charge pour gRPC sur .NET.
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
uid: grpc/supported-platforms
ms.openlocfilehash: c2bd808d16f11077e39aada829d79e8aedf2755b
ms.sourcegitcommit: 07e7ee573fe4e12be93249a385db745d714ff6ae
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103413416"
---
# <a name="grpc-on-net-supported-platforms"></a>gRPC sur les plateformes prises en charge par .NET

Par [James Newton-King](https://twitter.com/jamesnk)

Cet article décrit la configuration requise et les plateformes prises en charge pour l’utilisation de gRPC avec .NET. Il existe différentes exigences pour les deux charges de travail gRPC majeures :

* [Hébergement des services gRPC dans ASP.NET Core](#aspnet-core-grpc-server-requirements)
* [Appel de gRPC à partir d’applications clientes .NET](#net-grpc-client-requirements)

## <a name="wire-formats"></a>Formats filaires

gRPC tire parti des fonctionnalités avancées disponibles dans HTTP/2. HTTP/2 n’est pas pris en charge dans tous les pays, mais un deuxième format filaire à l’aide de HTTP/1.1 est disponible pour gRPC :

* [`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) -gRPC sur HTTP/2 indique comment gRPC est généralement utilisé.
* [`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) -gRPC-Web modifie le protocole gRPC pour qu’il soit compatible avec HTTP/1.1. gRPC-Web peut être utilisé à d’autres endroits. gRPC-Web peut être utilisé par les applications de navigateur et les réseaux sans prise en charge complète de HTTP/2. Deux fonctionnalités gRPC avancées ne sont plus prises en charge : la diffusion en continu du client et la diffusion bidirectionnelle.

gRPC sur .NET prend en charge les formats câblés. `application/grpc` est utilisé par défaut. gRPC-Web doit être configuré sur le client et sur le serveur pour les appels gRPC-Web réussis. Pour plus d’informations sur la configuration de gRPC-Web, consultez <xref:grpc/browser> .

## <a name="aspnet-core-grpc-server-requirements"></a>ASP.NET Core configuration requise du serveur gRPC

L’hébergement des services gRPC avec ASP.NET Core nécessite .NET Core 3. x ou une version ultérieure.

> [!div class="checklist"]
>
> * .NET 5 ou version ultérieure
> * .NET Core 3

ASP.NET Core Services gRPC peuvent être hébergés sur tous les systèmes d’exploitation pris en charge par .NET Core.

> [!div class="checklist"]
>
> * Windows
> * Linux
> * macOS&dagger;

&dagger;[MacOS ne prend pas en charge l’hébergement d’applications ASP.net core avec HTTPS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).

### <a name="supported-aspnet-core-servers"></a>Serveurs de ASP.NET Core pris en charge

Tous les serveurs ASP.NET Core intégrés sont pris en charge.

> [!div class="checklist"]
>
> * Kestrel
> * TestServer
> * IIS&dagger;
> * HTTP.sys&Dagger;

&dagger;IIS requiert .NET 5 et Windows 10 Build 20241 ou version ultérieure.

&Dagger;HTTP.sys nécessite .NET 5 et Windows 10 Build 19529 ou version ultérieure.

Pour plus d’informations sur la configuration des serveurs ASP.NET Core pour exécuter gRPC, consultez <xref:grpc/aspnetcore#server-options> .

### <a name="azure-services"></a>Services Azure

> [!div class="checklist"]
>
> * [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/)
> * [Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;

&dagger;Azure App Service ne prend pas en charge l’hébergement de gRPC sur HTTP/2. gRPC-Web est une alternative compatible.

Le travail est en cours pour améliorer la prise en charge de gRPC avec HTTP/2 dans Azure App Service. Pour plus d’informations, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore/issues/9020).

## <a name="net-grpc-client-requirements"></a>Configuration requise du client .NET gRPC

Le package [GRPC .net. client](https://www.nuget.org/packages/Grpc.Net.Client/) prend en charge les appels GRPC sur http/2 sur .net Core 3 et .net 5 ou version ultérieure.

Une prise en charge limitée est disponible pour gRPC sur HTTP/2 sur .NET Framework. D’autres versions de .NET telles que UWP, Xamarin et Unity n’ont pas la prise en charge HTTP/2 requise, et doivent utiliser gRPC-Web à la place.

Le tableau suivant répertorie les implémentations .NET et leur prise en charge du client gRPC :

| Implémentation de .NET                          | gRPC sur HTTP/2   | gRPC-Web   |
|----------------------------------------------|--------------------|------------|
| .NET 5 ou version ultérieure                              | ✔️                | ✔️         |
| .NET Core 3                                  | ✔️                | ✔️         |
| .NET Core 2.1                                | ❌                | ✔️         |
| .NET Framework 4.6.1                         | ⚠️&dagger;        | ✔️         |
| Blazor WebAssembly                           | ❌                | ✔️         |
| Mono 5.4                                     | ❌                | ✔️         |
| Xamarin.iOS 10.14                            | ❌                | ✔️         |
| Xamarin.Android 8.0                          | ❌                | ✔️         |
| Plateforme Windows universelle 10.0.16299        | ❌                | ✔️         |
| Unity 2018.1                                 | ❌                | ✔️         |

&dagger;.NET Framework nécessite <xref:System.Net.Http.WinHttpHandler> d’être configuré et Windows 10 Build 19622 ou version ultérieure.

L’utilisation `Grpc.Net.Client` de sur .NET Framework ou avec gRPC-Web requiert une configuration supplémentaire. Pour plus d’informations, consultez <xref:grpc/netstandard>.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:grpc/netstandard>
* [gRPC C# Core-bibliothèque](https://grpc.io/docs/languages/csharp/quickstart/)
