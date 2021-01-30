---
title: gRPC pour les plateformes .NET prises en charge
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
ms.openlocfilehash: 92ca38875c6618c8630a66af16548d32bc469a62
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057714"
---
# <a name="grpc-for-net-supported-platforms"></a>gRPC pour les plateformes .NET prises en charge

Par [James Newton-King](https://twitter.com/jamesnk)

Cet article décrit la configuration requise et les plateformes prises en charge pour l’utilisation de gRPC avec .NET.

gRPC est conçu pour utiliser HTTP/2 pour certaines de ses fonctionnalités plus avancées. HTTP/2 n’est pas pris en charge partout où il est possible d’empêcher l’utilisation de gRPC. C’est la seconde qui est compatible avec HTTP/1.1 pour l’envoi des appels gRPC entre les clients et les serveurs :

* [`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) -gRPC sur HTTP/2 indique comment gRPC est généralement utilisé.
* [`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) -gRPC-Web modifie le protocole gRPC pour qu’il soit compatible avec HTTP/1.1. gRPC-Web peut être utilisé à d’autres endroits, notamment s’il peut être appelé par des applications de navigateur. Deux fonctionnalités gRPC avancées ne sont plus prises en charge : la diffusion en continu du client et la diffusion bidirectionnelle.

gRPC pour .NET prend en charge les formats câblés. Pour plus d’informations sur la configuration de gRPC-Web, consultez <xref:grpc/browser> .

## <a name="device-requirements"></a>Exigences relatives aux appareils

gRPC pour .NET prend en charge tous les appareils que .NET Core prend en charge.

> [!div class="checklist"]
>
> * Windows
> * Linux
> * macOS&dagger;
> * Navigateurs&Dagger;

&dagger;Les applications ASP.NET Core hébergées sur macOS ne prennent pas en charge le protocole HTTPs. les clients gRPC sur macOS peuvent toujours utiliser le protocole HTTPs lors de l’appel de services distants.

&Dagger;Blazor WebAssembly les applications peuvent appeler gRPC services avec gRPC-Web.

## <a name="aspnet-core-server-requirements"></a>Configuration requise pour ASP.NET Core Server

les services gRPC peuvent être hébergés sur tous les serveurs ASP.NET Core intégrés.

> [!div class="checklist"]
>
> * Kestrel
> * TestServer
> * IIS&dagger;
> * HTTP.sys&dagger;

&dagger;IIS et HTTP.sys requièrent .NET 5 et Windows 10 Build 20241 ou version ultérieure.

Pour plus d’informations, consultez <xref:grpc/aspnetcore>.

## <a name="net-version-requirements"></a>Configuration requise pour la version .NET

gRPC pour .NET prend en charge .NET Core 3 et .NET 5 ou version ultérieure.

> [!div class="checklist"]
>
> * .NET 5 ou version ultérieure
> * .NET Core 3

gRPC pour .NET ne prend pas en charge l’exécution sur .NET Framework et Xamarin. [GRPC C# Core-Library](https://grpc.io/docs/languages/csharp/quickstart/) est une bibliothèque tierce qui prend en charge .NET Framework et Xamarin. gRPC C-Core n’est pas pris en charge par Microsoft.

## <a name="azure-services"></a>Services Azure

> [!div class="checklist"]
>
> * [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/)
> * [Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;

&dagger;Azure App Service ne prend pas en charge l’hébergement de gRPC sur HTTP/2. gRPC-Web est une alternative compatible.

Le travail est en cours pour améliorer la prise en charge de gRPC avec HTTP/2 dans Azure App Service. Pour plus d’informations, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore/issues/9020).

## <a name="additional-resources"></a>Ressources supplémentaires

* [gRPC C# Core-bibliothèque](https://grpc.io/docs/languages/csharp/quickstart/)
