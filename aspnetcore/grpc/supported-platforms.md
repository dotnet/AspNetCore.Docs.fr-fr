---
title: gRPC sur les plateformes prises en charge par .NET
author: jamesnk
description: En savoir plus sur les plateformes prises en charge pour gRPC sur .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/22/2021
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
ms.openlocfilehash: 6e48a19027f79b75edeebde9c584419871fba533
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530162"
---
# <a name="grpc-on-net-supported-platforms"></a>gRPC sur les plateformes prises en charge par .NET

Par [James Newton-King](https://twitter.com/jamesnk)

Cet article décrit la configuration requise et les plateformes prises en charge pour l’utilisation de gRPC avec .NET.

gRPC tire parti des fonctionnalités avancées disponibles dans HTTP/2. HTTP/2 n’est pas pris en charge dans tous les pays, mais un deuxième format filaire à l’aide de HTTP/1.1 est disponible pour gRPC :

* [`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) -gRPC sur HTTP/2 indique comment gRPC est généralement utilisé.
* [`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) -gRPC-Web modifie le protocole gRPC pour qu’il soit compatible avec HTTP/1.1. gRPC-Web peut être utilisé à d’autres endroits, notamment s’il peut être appelé par des applications de navigateur. Deux fonctionnalités gRPC avancées ne sont plus prises en charge : la diffusion en continu du client et la diffusion bidirectionnelle.

gRPC sur .NET prend en charge les formats câblés. gRPC sur HTTP/2 est utilisé par défaut. Pour plus d’informations sur la configuration de gRPC-Web, consultez <xref:grpc/browser> .

## <a name="device-requirements"></a>Exigences relatives aux appareils

gRPC sur .NET prend en charge tous les appareils que .NET Core prend en charge.

> [!div class="checklist"]
>
> * Windows
> * Linux
> * macOS&dagger;
> * Navigateurs&Dagger;

&dagger;[MacOS ne prend pas en charge l’hébergement d’applications ASP.net core avec HTTPS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos). les clients gRPC sur macOS peuvent appeler des services distants qui utilisent le protocole HTTPs.

&Dagger;Blazor WebAssembly les applications peuvent appeler gRPC services avec gRPC-Web.

## <a name="aspnet-core-server-requirements"></a>Configuration requise pour ASP.NET Core Server

les services gRPC peuvent être hébergés sur tous les serveurs ASP.NET Core intégrés.

> [!div class="checklist"]
>
> * Kestrel
> * TestServer
> * IIS&dagger;
> * HTTP.sys&Dagger;

&dagger;IIS requiert .NET 5 et Windows 10 Build 20241 ou version ultérieure.

&Dagger;HTTP.sys nécessite .NET 5 et Windows 10 Build 19529 ou version ultérieure.

Pour plus d’informations sur la configuration des serveurs ASP.NET Core pour exécuter gRPC, consultez <xref:grpc/aspnetcore#server-options> .

## <a name="net-version-requirements"></a>Configuration requise pour la version .NET

gRPC sur .NET prend en charge .NET Core 3 et .NET 5 ou version ultérieure.

> [!div class="checklist"]
>
> * .NET 5 ou version ultérieure
> * .NET Core 3

gRPC sur .NET ne prend pas en charge l’exécution sur .NET Framework et Xamarin. [GRPC C# Core-Library](https://grpc.io/docs/languages/csharp/quickstart/) est une bibliothèque tierce qui prend en charge .NET Framework et Xamarin. gRPC C-Core n’est pas pris en charge par Microsoft.

## <a name="azure-services"></a>Services Azure

> [!div class="checklist"]
>
> * [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/)
> * [Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;

&dagger;Azure App Service ne prend pas en charge l’hébergement de gRPC sur HTTP/2. gRPC-Web est une alternative compatible.

Le travail est en cours pour améliorer la prise en charge de gRPC avec HTTP/2 dans Azure App Service. Pour plus d’informations, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore/issues/9020).

## <a name="additional-resources"></a>Ressources supplémentaires

* [gRPC C# Core-bibliothèque](https://grpc.io/docs/languages/csharp/quickstart/)
