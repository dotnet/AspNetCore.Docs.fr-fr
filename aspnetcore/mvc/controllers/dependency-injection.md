---
title: Injection de dépendances dans les contrôleurs dans ASP.NET Core
author: ardalis
description: Découvrez comment les contrôleurs ASP.NET Core MVC demandent explicitement leurs dépendances par le biais de leurs constructeurs avec l’injection de dépendances dans ASP.NET Core.
ms.author: riande
ms.date: 02/24/2019
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
uid: mvc/controllers/dependency-injection
ms.openlocfilehash: f3654e008733c57b4cd4cd34a52b747af2258be1
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589073"
---
# <a name="dependency-injection-into-controllers-in-aspnet-core"></a>Injection de dépendances dans les contrôleurs dans ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Par [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT) et [Steve Smith](https://github.com/ardalis)

Les contrôleurs ASP.NET Core MVC demandent les dépendances explicitement via des constructeurs. ASP.NET Core offre une prise en charge intégrée de l’[injection de dépendances](xref:fundamentals/dependency-injection). L’injection de dépendances facilite le test et la maintenance des applications.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/dependency-injection/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="constructor-injection"></a>Injection de constructeurs

Les services sont ajoutés sous forme de paramètre de constructeur, et le runtime résout les services à partir du conteneur de services. Les services sont généralement définis à partir d’interfaces. Par exemple, prenons le cas d’une application qui a besoin de l’heure actuelle. L’interface suivante expose le service `IDateTime` :

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

Le code suivant implémente l’interface `IDateTime` :

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

Ajoutez le service au conteneur de services :

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

Pour plus d’informations sur <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, consultez [Durée de vie des services d’injonction de dépendances](xref:fundamentals/dependency-injection#service-lifetimes).

Le code suivant adresse une salutation à l’utilisateur qui varie en fonction de l’heure du jour :

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

Exécutez l’application et un message s’affiche en fonction de l’heure.

## <a name="action-injection-with-fromservices"></a>Injection d’action avec FromServices

<xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> permet d’injecter un service directement dans une méthode d’action sans utiliser l’injection de constructeurs :

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a>Accéder aux paramètres à partir d’un contrôleur

L’accès aux paramètres de configuration ou d’application à partir d’un contrôleur est un modèle commun. Le *modèle options* décrit dans <xref:fundamentals/configuration/options> est l’approche à privilégier pour gérer les paramètres. En règle générale, n’injectez pas directement <xref:Microsoft.Extensions.Configuration.IConfiguration> dans un contrôleur.

Créez une classe qui représente les options. Par exemple :

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

Ajoutez la classe de configuration à la collection de services :

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

Configurez l’application pour qu’elle lise les paramètres à partir d’un fichier au format JSON :

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

Le code suivant demande les paramètres `IOptions<SampleWebSettings>` au conteneur de services et les utilise dans la méthode `Index` :

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a>Ressources supplémentaires

* Consultez <xref:mvc/controllers/testing> pour savoir comment rendre le code plus facile à tester en demandant explicitement des dépendances dans les contrôleurs.

* [Remplacez le conteneur d’injection de dépendances par défaut par une implémentation tierce](xref:fundamentals/dependency-injection#default-service-container-replacement).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Par [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT) et [Steve Smith](https://github.com/ardalis)

Les contrôleurs ASP.NET Core MVC demandent les dépendances explicitement via des constructeurs. ASP.NET Core offre une prise en charge intégrée de l’[injection de dépendances](xref:fundamentals/dependency-injection). L’injection de dépendances facilite le test et la maintenance des applications.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/dependency-injection/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="constructor-injection"></a>Injection de constructeurs

Les services sont ajoutés sous forme de paramètre de constructeur, et le runtime résout les services à partir du conteneur de services. Les services sont généralement définis à partir d’interfaces. Par exemple, prenons le cas d’une application qui a besoin de l’heure actuelle. L’interface suivante expose le service `IDateTime` :

[!code-csharp[](dependency-injection/sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

Le code suivant implémente l’interface `IDateTime` :

[!code-csharp[](dependency-injection/sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

Ajoutez le service au conteneur de services :

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

Pour plus d’informations sur <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, consultez [Durée de vie des services d’injonction de dépendances](xref:fundamentals/dependency-injection#service-lifetimes).

Le code suivant adresse une salutation à l’utilisateur qui varie en fonction de l’heure du jour :

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

Exécutez l’application et un message s’affiche en fonction de l’heure.

## <a name="action-injection-with-fromservices"></a>Injection d’action avec FromServices

<xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> permet d’injecter un service directement dans une méthode d’action sans utiliser l’injection de constructeurs :

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a>Accéder aux paramètres à partir d’un contrôleur

L’accès aux paramètres de configuration ou d’application à partir d’un contrôleur est un modèle commun. Le *modèle options* décrit dans <xref:fundamentals/configuration/options> est l’approche à privilégier pour gérer les paramètres. En règle générale, n’injectez pas directement <xref:Microsoft.Extensions.Configuration.IConfiguration> dans un contrôleur.

Créez une classe qui représente les options. Par exemple :

[!code-csharp[](dependency-injection/sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

Ajoutez la classe de configuration à la collection de services :

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

Configurez l’application pour qu’elle lise les paramètres à partir d’un fichier au format JSON :

[!code-csharp[](dependency-injection/sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

Le code suivant demande les paramètres `IOptions<SampleWebSettings>` au conteneur de services et les utilise dans la méthode `Index` :

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a>Ressources supplémentaires

* Consultez <xref:mvc/controllers/testing> pour savoir comment rendre le code plus facile à tester en demandant explicitement des dépendances dans les contrôleurs.

* [Remplacez le conteneur d’injection de dépendances par défaut par une implémentation tierce](xref:fundamentals/dependency-injection#default-service-container-replacement).

::: moniker-end