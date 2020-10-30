---
title: Migrer la configuration vers ASP.NET Core
author: ardalis
description: Découvrez comment migrer la configuration d’un projet MVC ASP.NET vers un projet ASP.NET Core MVC.
ms.author: riande
ms.date: 10/14/2016
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
uid: migration/configuration
ms.openlocfilehash: d84204c8c791bfaf36432462cde3a42c294c7966
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059791"
---
# <a name="migrate-configuration-to-aspnet-core"></a>Migrer la configuration vers ASP.NET Core

Par [Steve Smith](https://ardalis.com/) et [Scott Addie](https://scottaddie.com)

Dans l’article précédent, nous avons commencé à [migrer un projet mvc ASP.net vers ASP.net Core MVC](xref:migration/mvc). Dans cet article, nous allons migrer la configuration.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/configuration/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="setup-configuration"></a>Configuration

ASP.NET Core n’utilise plus les fichiers *global. asax* et *web.config* utilisés par les versions précédentes de ASP.net. Dans les versions antérieures de ASP.NET, la logique de démarrage de l’application était placée dans une `Application_StartUp` méthode au sein de *global. asax* . Plus tard, dans ASP.NET MVC, un fichier *Startup.cs* était inclus à la racine du projet. et, elle a été appelée au démarrage de l’application. ASP.NET Core a entièrement adopté cette approche en plaçant toutes les logiques de démarrage dans le fichier *Startup.cs* .

Le fichier *web.config* a également été remplacé dans ASP.net core. La configuration elle-même peut désormais être configurée dans le cadre de la procédure de démarrage de l’application décrite dans *Startup.cs* . La configuration peut toujours utiliser des fichiers XML, mais en général ASP.NET Core les projets placent les valeurs de configuration dans un fichier au format JSON, tel que *appsettings.json* . Le système de configuration de ASP.NET Core peut également accéder facilement à des variables d’environnement, ce qui peut fournir un [emplacement plus sécurisé et plus robuste](xref:security/app-secrets) pour les valeurs propres à l’environnement. Cela est particulièrement vrai pour les secrets comme les chaînes de connexion et les clés API qui ne doivent pas être archivées dans le contrôle de code source. Consultez [configuration](xref:fundamentals/configuration/index) pour en savoir plus sur la configuration dans ASP.net core.

Pour cet article, nous commençons par le projet ASP.NET Core partiellement migré de [l’article précédent](xref:migration/mvc). Pour configurer la configuration, ajoutez le constructeur et la propriété suivants au fichier *Startup.cs* situé à la racine du projet :

[!code-csharp[](configuration/samples/WebApp1/src/WebApp1/Startup.cs?range=11-16)]

Notez qu’à ce stade, le fichier *Startup.cs* ne se compile pas, car nous devons toujours ajouter l' `using` instruction suivante :

```csharp
using Microsoft.Extensions.Configuration;
```

Ajoutez un *appsettings.json* fichier à la racine du projet à l’aide du modèle d’élément approprié :

![Ajouter le JSON AppSettings](configuration/_static/add-appsettings-json.png)

## <a name="migrate-configuration-settings-from-webconfig"></a>Migrer les paramètres de configuration à partir de web.config

Notre projet MVC ASP.NET incluait la chaîne de connexion de base de données requise dans *web.config* , dans l' `<connectionStrings>` élément. Dans notre projet de ASP.NET Core, nous allons stocker ces informations dans le *appsettings.json* fichier. Ouvrez *appsettings.json* et Notez qu’il contient déjà les éléments suivants :

[!code-json[](../migration/configuration/samples/WebApp1/src/WebApp1/appsettings.json?highlight=4)]

Dans la ligne en surbrillance décrite ci-dessus, remplacez le nom de la base de données **_CHANGE_ME** par le nom de votre base de données.

## <a name="summary"></a>Résumé

ASP.NET Core place toute la logique de démarrage de l’application dans un fichier unique, dans lequel les services et dépendances nécessaires peuvent être définis et configurés. Il remplace le fichier *web.config* par une fonctionnalité de configuration flexible qui peut tirer parti d’un large éventail de formats de fichiers, tels que JSON, ainsi que des variables d’environnement.
