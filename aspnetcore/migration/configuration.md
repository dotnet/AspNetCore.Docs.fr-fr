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
ms.openlocfilehash: c3957bf45dddcead24f7bb0f2702bf1a08950bdd
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587942"
---
# <a name="migrate-configuration-to-aspnet-core"></a><span data-ttu-id="05f82-103">Migrer la configuration vers ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="05f82-103">Migrate configuration to ASP.NET Core</span></span>

<span data-ttu-id="05f82-104">Par [Steve Smith](https://ardalis.com/) et [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="05f82-104">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

<span data-ttu-id="05f82-105">Dans l’article précédent, nous avons commencé à [migrer un projet mvc ASP.net vers ASP.net Core MVC](xref:migration/mvc).</span><span class="sxs-lookup"><span data-stu-id="05f82-105">In the previous article, we began to [migrate an ASP.NET MVC project to ASP.NET Core MVC](xref:migration/mvc).</span></span> <span data-ttu-id="05f82-106">Dans cet article, nous allons migrer la configuration.</span><span class="sxs-lookup"><span data-stu-id="05f82-106">In this article, we migrate configuration.</span></span>

<span data-ttu-id="05f82-107">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/migration/configuration/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="05f82-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/migration/configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="setup-configuration"></a><span data-ttu-id="05f82-108">Configuration</span><span class="sxs-lookup"><span data-stu-id="05f82-108">Setup configuration</span></span>

<span data-ttu-id="05f82-109">ASP.NET Core n’utilise plus les fichiers *global. asax* et *web.config* utilisés par les versions précédentes de ASP.net.</span><span class="sxs-lookup"><span data-stu-id="05f82-109">ASP.NET Core no longer uses the *Global.asax* and *web.config* files that previous versions of ASP.NET utilized.</span></span> <span data-ttu-id="05f82-110">Dans les versions antérieures de ASP.NET, la logique de démarrage de l’application était placée dans une `Application_StartUp` méthode au sein de *global. asax*.</span><span class="sxs-lookup"><span data-stu-id="05f82-110">In the earlier versions of ASP.NET, application startup logic was placed in an `Application_StartUp` method within *Global.asax*.</span></span> <span data-ttu-id="05f82-111">Plus tard, dans ASP.NET MVC, un fichier *Startup.cs* était inclus à la racine du projet. et, elle a été appelée au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="05f82-111">Later, in ASP.NET MVC, a *Startup.cs* file was included in the root of the project; and, it was called when the application started.</span></span> <span data-ttu-id="05f82-112">ASP.NET Core a entièrement adopté cette approche en plaçant toutes les logiques de démarrage dans le fichier *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="05f82-112">ASP.NET Core has adopted this approach completely by placing all startup logic in the *Startup.cs* file.</span></span>

<span data-ttu-id="05f82-113">Le fichier *web.config* a également été remplacé dans ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="05f82-113">The *web.config* file has also been replaced in ASP.NET Core.</span></span> <span data-ttu-id="05f82-114">La configuration elle-même peut désormais être configurée dans le cadre de la procédure de démarrage de l’application décrite dans *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="05f82-114">Configuration itself can now be configured, as part of the application startup procedure described in *Startup.cs*.</span></span> <span data-ttu-id="05f82-115">La configuration peut toujours utiliser des fichiers XML, mais en général ASP.NET Core les projets placent les valeurs de configuration dans un fichier au format JSON, tel que *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="05f82-115">Configuration can still utilize XML files, but typically ASP.NET Core projects will place configuration values in a JSON-formatted file, such as *appsettings.json*.</span></span> <span data-ttu-id="05f82-116">Le système de configuration de ASP.NET Core peut également accéder facilement à des variables d’environnement, ce qui peut fournir un [emplacement plus sécurisé et plus robuste](xref:security/app-secrets) pour les valeurs propres à l’environnement.</span><span class="sxs-lookup"><span data-stu-id="05f82-116">ASP.NET Core's configuration system can also easily access environment variables, which can provide a [more secure and robust location](xref:security/app-secrets) for environment-specific values.</span></span> <span data-ttu-id="05f82-117">Cela est particulièrement vrai pour les secrets comme les chaînes de connexion et les clés API qui ne doivent pas être archivées dans le contrôle de code source.</span><span class="sxs-lookup"><span data-stu-id="05f82-117">This is especially true for secrets like connection strings and API keys that shouldn't be checked into source control.</span></span> <span data-ttu-id="05f82-118">Consultez [configuration](xref:fundamentals/configuration/index) pour en savoir plus sur la configuration dans ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="05f82-118">See [Configuration](xref:fundamentals/configuration/index) to learn more about configuration in ASP.NET Core.</span></span>

<span data-ttu-id="05f82-119">Pour cet article, nous commençons par le projet ASP.NET Core partiellement migré de [l’article précédent](xref:migration/mvc).</span><span class="sxs-lookup"><span data-stu-id="05f82-119">For this article, we are starting with the partially migrated ASP.NET Core project from [the previous article](xref:migration/mvc).</span></span> <span data-ttu-id="05f82-120">Pour configurer la configuration, ajoutez le constructeur et la propriété suivants au fichier *Startup.cs* situé à la racine du projet :</span><span class="sxs-lookup"><span data-stu-id="05f82-120">To setup configuration, add the following constructor and property to the *Startup.cs* file located in the root of the project:</span></span>

[!code-csharp[](configuration/samples/WebApp1/src/WebApp1/Startup.cs?range=11-16)]

<span data-ttu-id="05f82-121">Notez qu’à ce stade, le fichier *Startup.cs* ne se compile pas, car nous devons toujours ajouter l' `using` instruction suivante :</span><span class="sxs-lookup"><span data-stu-id="05f82-121">Note that at this point, the *Startup.cs* file won't compile, as we still need to add the following `using` statement:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="05f82-122">Ajoutez un *appsettings.json* fichier à la racine du projet à l’aide du modèle d’élément approprié :</span><span class="sxs-lookup"><span data-stu-id="05f82-122">Add an *appsettings.json* file to the root of the project using the appropriate item template:</span></span>

![Ajouter le JSON AppSettings](configuration/_static/add-appsettings-json.png)

## <a name="migrate-configuration-settings-from-webconfig"></a><span data-ttu-id="05f82-124">Migrer les paramètres de configuration à partir de web.config</span><span class="sxs-lookup"><span data-stu-id="05f82-124">Migrate configuration settings from web.config</span></span>

<span data-ttu-id="05f82-125">Notre projet MVC ASP.NET incluait la chaîne de connexion de base de données requise dans *web.config*, dans l' `<connectionStrings>` élément.</span><span class="sxs-lookup"><span data-stu-id="05f82-125">Our ASP.NET MVC project included the required database connection string in *web.config*, in the `<connectionStrings>` element.</span></span> <span data-ttu-id="05f82-126">Dans notre projet de ASP.NET Core, nous allons stocker ces informations dans le *appsettings.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="05f82-126">In our ASP.NET Core project, we are going to store this information in the *appsettings.json* file.</span></span> <span data-ttu-id="05f82-127">Ouvrez *appsettings.json* et Notez qu’il contient déjà les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="05f82-127">Open *appsettings.json*, and note that it already includes the following:</span></span>

[!code-json[](../migration/configuration/samples/WebApp1/src/WebApp1/appsettings.json?highlight=4)]

<span data-ttu-id="05f82-128">Dans la ligne en surbrillance décrite ci-dessus, remplacez le nom de la base de données **_CHANGE_ME** par le nom de votre base de données.</span><span class="sxs-lookup"><span data-stu-id="05f82-128">In the highlighted line depicted above, change the name of the database from **_CHANGE_ME** to the name of your database.</span></span>

## <a name="summary"></a><span data-ttu-id="05f82-129">Résumé</span><span class="sxs-lookup"><span data-stu-id="05f82-129">Summary</span></span>

<span data-ttu-id="05f82-130">ASP.NET Core place toute la logique de démarrage de l’application dans un fichier unique, dans lequel les services et dépendances nécessaires peuvent être définis et configurés.</span><span class="sxs-lookup"><span data-stu-id="05f82-130">ASP.NET Core places all startup logic for the application in a single file, in which the necessary services and dependencies can be defined and configured.</span></span> <span data-ttu-id="05f82-131">Il remplace le fichier *web.config* par une fonctionnalité de configuration flexible qui peut tirer parti d’un large éventail de formats de fichiers, tels que JSON, ainsi que des variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="05f82-131">It replaces the *web.config* file with a flexible configuration feature that can leverage a variety of file formats, such as JSON, as well as environment variables.</span></span>
