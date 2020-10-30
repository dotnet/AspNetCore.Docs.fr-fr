---
title: Apprenez à migrer de ASP.NET MVC vers ASP.NET Core MVC
author: wadepickett
description: Découvrez comment démarrer la migration d’un projet ASP.NET MVC vers ASP.NET Core MVC.
ms.author: wpickett
ms.date: 06/18/2020
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
uid: migration/mvc
ms.openlocfilehash: 226ac6da508378c7b3c81779d38dd2e0840f1fed
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051511"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a><span data-ttu-id="78b55-103">Migrer ASP.NET MVC vers ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="78b55-103">Migrate from ASP.NET MVC to ASP.NET Core MVC</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="78b55-104">Cet article explique comment commencer à migrer un projet ASP.NET MVC vers [ASP.net Core MVC](xref:mvc/overview).</span><span class="sxs-lookup"><span data-stu-id="78b55-104">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview).</span></span> <span data-ttu-id="78b55-105">Dans le processus, il met en évidence les modifications associées de ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="78b55-105">In the process, it highlights related changes from ASP.NET MVC.</span></span>

<span data-ttu-id="78b55-106">La migration à partir de ASP.NET MVC est un processus en plusieurs étapes.</span><span class="sxs-lookup"><span data-stu-id="78b55-106">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="78b55-107">Cet article couvre les points suivants :</span><span class="sxs-lookup"><span data-stu-id="78b55-107">This article covers:</span></span>

* <span data-ttu-id="78b55-108">Configuration initiale.</span><span class="sxs-lookup"><span data-stu-id="78b55-108">Initial setup.</span></span>
* <span data-ttu-id="78b55-109">Contrôleurs et vues de base.</span><span class="sxs-lookup"><span data-stu-id="78b55-109">Basic controllers and views.</span></span>
* <span data-ttu-id="78b55-110">Contenu statique.</span><span class="sxs-lookup"><span data-stu-id="78b55-110">Static content.</span></span>
* <span data-ttu-id="78b55-111">Dépendances côté client.</span><span class="sxs-lookup"><span data-stu-id="78b55-111">Client-side dependencies.</span></span>

<span data-ttu-id="78b55-112">Pour la migration de la configuration et du :::no-loc(Identity)::: code, consultez [migrer la configuration vers ASP.net Core](xref:migration/configuration) et [migrer l’authentification et :::no-loc(Identity)::: vers ASP.net Core](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="78b55-112">For migrating configuration and :::no-loc(Identity)::: code, see [Migrate configuration to ASP.NET Core](xref:migration/configuration) and [Migrate Authentication and :::no-loc(Identity)::: to ASP.NET Core](xref:migration/identity).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="78b55-113">Prérequis</span><span class="sxs-lookup"><span data-stu-id="78b55-113">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="78b55-114">Créer le projet MVC ASP.NET de démarrage</span><span class="sxs-lookup"><span data-stu-id="78b55-114">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="78b55-115">Créer un exemple de projet MVC ASP.NET dans Visual Studio à migrer :</span><span class="sxs-lookup"><span data-stu-id="78b55-115">Create an example ASP.NET MVC project in Visual Studio to migrate:</span></span>

1. <span data-ttu-id="78b55-116">Dans le menu **fichier** , sélectionnez **nouveau** > **projet** .</span><span class="sxs-lookup"><span data-stu-id="78b55-116">From the **File** menu, select **New** > **Project** .</span></span>
1. <span data-ttu-id="78b55-117">Sélectionnez **application Web ASP.net (.NET Framework)** , puis sélectionnez **suivant** .</span><span class="sxs-lookup"><span data-stu-id="78b55-117">Select **ASP.NET Web Application (.NET Framework)** and then select **Next** .</span></span>
1. <span data-ttu-id="78b55-118">Nommez le projet *application Web 1* pour que l’espace de noms corresponde au projet ASP.net Core créé à l’étape suivante.</span><span class="sxs-lookup"><span data-stu-id="78b55-118">Name the project *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span> <span data-ttu-id="78b55-119">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="78b55-119">Select **Create** .</span></span>
1. <span data-ttu-id="78b55-120">Sélectionnez **MVC** , puis sélectionnez **créer** .</span><span class="sxs-lookup"><span data-stu-id="78b55-120">Select **MVC** , and then select **Create** .</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="78b55-121">Créer le projet ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="78b55-121">Create the ASP.NET Core project</span></span>

<span data-ttu-id="78b55-122">Créer une solution avec un nouveau ASP.NET Core projet à migrer vers :</span><span class="sxs-lookup"><span data-stu-id="78b55-122">Create a new solution with a new ASP.NET Core project to migrate to:</span></span>

1. <span data-ttu-id="78b55-123">Lancez une deuxième instance de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="78b55-123">Launch a second instance of Visual Studio.</span></span>
1. <span data-ttu-id="78b55-124">Dans le menu **fichier** , sélectionnez **nouveau** > **projet** .</span><span class="sxs-lookup"><span data-stu-id="78b55-124">From the **File** menu, select **New** > **Project** .</span></span>
1. <span data-ttu-id="78b55-125">Sélectionnez **Application web ASP.NET Core** , puis **Suivant** .</span><span class="sxs-lookup"><span data-stu-id="78b55-125">Select **ASP.NET Core Web Application** and then select **Next** .</span></span>
1. <span data-ttu-id="78b55-126">Dans la boîte de dialogue **configurer votre nouveau projet** , nommez le projet *application Web 1* .</span><span class="sxs-lookup"><span data-stu-id="78b55-126">In the **Configure your new project** dialog, Name the project *WebApp1* .</span></span>
1. <span data-ttu-id="78b55-127">Définissez l’emplacement sur un répertoire différent de celui du projet précédent pour qu’il utilise le même nom de projet.</span><span class="sxs-lookup"><span data-stu-id="78b55-127">Set the location to a different directory than the previous project to use the same project name.</span></span> <span data-ttu-id="78b55-128">L’utilisation du même espace de noms facilite la copie du code entre les deux projets.</span><span class="sxs-lookup"><span data-stu-id="78b55-128">Using the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="78b55-129">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="78b55-129">Select **Create** .</span></span>
1. <span data-ttu-id="78b55-130">Dans la boîte de dialogue **créer une application Web ASP.net Core** , vérifiez que **.net Core** et **ASP.net Core 3,1** sont sélectionnés.</span><span class="sxs-lookup"><span data-stu-id="78b55-130">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="78b55-131">Sélectionnez le modèle **de projet application Web (Model-View-Controller)** , puis sélectionnez **créer** .</span><span class="sxs-lookup"><span data-stu-id="78b55-131">Select the **Web Application (Model-View-Controller)** project template, and select **Create** .</span></span>

## <a name="configure-the-aspnet-core-site-to-use-mvc"></a><span data-ttu-id="78b55-132">Configurer le site ASP.NET Core pour utiliser MVC</span><span class="sxs-lookup"><span data-stu-id="78b55-132">Configure the ASP.NET Core site to use MVC</span></span>

<span data-ttu-id="78b55-133">Dans ASP.NET Core projets 3,0 et versions ultérieures, .NET Framework n’est plus une version cible de .NET Framework prise en charge.</span><span class="sxs-lookup"><span data-stu-id="78b55-133">In ASP.NET Core 3.0 and later projects, .NET Framework is no longer a supported target framework.</span></span> <span data-ttu-id="78b55-134">Votre projet doit cibler .NET Core.</span><span class="sxs-lookup"><span data-stu-id="78b55-134">Your project must target .NET Core.</span></span> <span data-ttu-id="78b55-135">Le ASP.NET Core Framework partagé, qui comprend MVC, fait partie de l’installation du Runtime .NET Core.</span><span class="sxs-lookup"><span data-stu-id="78b55-135">The ASP.NET Core shared framework, which includes MVC, is part of the .NET Core runtime installation.</span></span> <span data-ttu-id="78b55-136">Le Framework partagé est automatiquement référencé lors de l’utilisation du `Microsoft.NET.Sdk.Web` Kit de développement logiciel (SDK) dans le fichier projet :</span><span class="sxs-lookup"><span data-stu-id="78b55-136">The shared framework is automatically referenced when using the `Microsoft.NET.Sdk.Web` SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="78b55-137">Pour plus d’informations, consultez Référence de l' [infrastructure](xref:migration/22-to-30#framework-reference).</span><span class="sxs-lookup"><span data-stu-id="78b55-137">For more information, see [Framework reference](xref:migration/22-to-30#framework-reference).</span></span>

<span data-ttu-id="78b55-138">Dans ASP.NET Core, la `Startup` classe :</span><span class="sxs-lookup"><span data-stu-id="78b55-138">In ASP.NET Core, the `Startup` class:</span></span>

* <span data-ttu-id="78b55-139">Remplace *global. asax* .</span><span class="sxs-lookup"><span data-stu-id="78b55-139">Replaces *Global.asax* .</span></span>
* <span data-ttu-id="78b55-140">Gère toutes les tâches de démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="78b55-140">Handles all app startup tasks.</span></span>

<span data-ttu-id="78b55-141">Pour plus d'informations, consultez <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="78b55-141">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="78b55-142">Dans le projet ASP.NET Core, ouvrez le fichier *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="78b55-142">In the ASP.NET Core project, open the *Startup.cs* file:</span></span>

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=13,30,32&name=snippet)]

<span data-ttu-id="78b55-143">Les applications ASP.NET Core doivent accepter les fonctionnalités d’infrastructure avec l’intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="78b55-143">ASP.NET Core apps must opt in to framework features with middleware.</span></span> <span data-ttu-id="78b55-144">Le code précédent généré par le modèle ajoute les services et l’intergiciel (middleware) suivants :</span><span class="sxs-lookup"><span data-stu-id="78b55-144">The previous template-generated code adds the following services and middleware:</span></span>

* <span data-ttu-id="78b55-145">La <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> méthode d’extension enregistre la prise en charge du service MVC pour les contrôleurs, les fonctionnalités liées à l’API et les vues.</span><span class="sxs-lookup"><span data-stu-id="78b55-145">The <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> extension method registers MVC service support for controllers, API-related features, and views.</span></span> <span data-ttu-id="78b55-146">Pour plus d’informations sur les options d’inscription du service MVC, consultez [inscription du service MVC](xref:migration/22-to-30#mvc-service-registration)</span><span class="sxs-lookup"><span data-stu-id="78b55-146">For more information on MVC service registration options, see [MVC service registration](xref:migration/22-to-30#mvc-service-registration)</span></span>
* <span data-ttu-id="78b55-147">La <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> méthode d’extension ajoute le gestionnaire de fichiers statiques `Microsoft.AspNetCore.StaticFiles` .</span><span class="sxs-lookup"><span data-stu-id="78b55-147">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> extension method adds the static file handler `Microsoft.AspNetCore.StaticFiles`.</span></span> <span data-ttu-id="78b55-148">La `UseStaticFiles` méthode d’extension doit être appelée avant `UseRouting` .</span><span class="sxs-lookup"><span data-stu-id="78b55-148">The `UseStaticFiles` extension method must be called before `UseRouting`.</span></span> <span data-ttu-id="78b55-149">Pour plus d'informations, consultez <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="78b55-149">For more information, see <xref:fundamentals/static-files>.</span></span>
* <span data-ttu-id="78b55-150">La <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> méthode d’extension ajoute le routage.</span><span class="sxs-lookup"><span data-stu-id="78b55-150">The <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> extension method adds routing.</span></span> <span data-ttu-id="78b55-151">Pour plus d'informations, consultez <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="78b55-151">For more information, see <xref:fundamentals/routing>.</span></span>

<span data-ttu-id="78b55-152">Cette configuration existante comprend ce qui est nécessaire pour migrer l’exemple de projet MVC ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="78b55-152">This existing configuration includes what is needed to migrate the example ASP.NET MVC project.</span></span> <span data-ttu-id="78b55-153">Pour plus d’informations sur les options de l’intergiciel (middleware) ASP.NET Core, consultez <xref:fundamentals/startup> .</span><span class="sxs-lookup"><span data-stu-id="78b55-153">For more information on ASP.NET Core middleware options, see <xref:fundamentals/startup>.</span></span>

## <a name="migrate-controllers-and-views"></a><span data-ttu-id="78b55-154">Migrer des contrôleurs et des vues</span><span class="sxs-lookup"><span data-stu-id="78b55-154">Migrate controllers and views</span></span>

<span data-ttu-id="78b55-155">Dans le projet ASP.NET Core, une nouvelle classe de contrôleur vide et une nouvelle classe de vue seraient ajoutées pour servir d’espaces réservés en utilisant les mêmes noms que les classes de contrôleur et de vue dans tout projet MVC ASP.NET à partir duquel effectuer la migration.</span><span class="sxs-lookup"><span data-stu-id="78b55-155">In the ASP.NET Core project, a new empty controller class and view class would be added to serve as placeholders using the same names as the controller and view classes in any ASP.NET MVC project to migrate from.</span></span>

<span data-ttu-id="78b55-156">Le projet ASP.NET Core *application Web 1* contient déjà un exemple de contrôleur et un affichage minimaux du même nom que le projet MVC ASP.net.</span><span class="sxs-lookup"><span data-stu-id="78b55-156">The ASP.NET Core *WebApp1* project already includes a minimal example controller and view by the same name as the ASP.NET MVC project.</span></span> <span data-ttu-id="78b55-157">Ils serviront donc d’espaces réservés pour le contrôleur MVC ASP.NET et les vues à migrer à partir du projet ASP.NET MVC *application Web 1* .</span><span class="sxs-lookup"><span data-stu-id="78b55-157">So those will serve as placeholders for the ASP.NET MVC controller and views to be migrated from the ASP.NET MVC *WebApp1* project.</span></span>

1. <span data-ttu-id="78b55-158">Copiez les méthodes à partir du MVC ASP.NET `HomeController` pour remplacer les nouvelles `HomeController` méthodes ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="78b55-158">Copy the methods from the ASP.NET MVC `HomeController` to replace the new ASP.NET Core `HomeController` methods.</span></span> <span data-ttu-id="78b55-159">Il n’est pas nécessaire de modifier le type de retour des méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="78b55-159">There's no need to change the return type of the action methods.</span></span> <span data-ttu-id="78b55-160">Le type de retour de la méthode d’action du contrôleur du modèle intégré ASP.NET MVC est <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> ; dans ASP.net Core MVC, les méthodes d’action retournent à la `IActionResult` place.</span><span class="sxs-lookup"><span data-stu-id="78b55-160">The ASP.NET MVC built-in template's controller action method return type is <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2>; in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="78b55-161">L'objet `ActionResult` implémente l'objet `IActionResult`.</span><span class="sxs-lookup"><span data-stu-id="78b55-161">`ActionResult` implements `IActionResult`.</span></span>
1. <span data-ttu-id="78b55-162">Dans le projet ASP.net Core, cliquez avec le bouton droit sur le répertoire *views/racine* , puis sélectionnez **Ajouter** un > **élément existant** .</span><span class="sxs-lookup"><span data-stu-id="78b55-162">In the ASP.NET Core project, right-click the *Views/Home* directory, select **Add** > **Existing Item** .</span></span>
1. <span data-ttu-id="78b55-163">Dans la boîte de dialogue **Ajouter un élément existant** , accédez au répertoire *views/* ASP.net du projet *application Web 1* Mvc.</span><span class="sxs-lookup"><span data-stu-id="78b55-163">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views/Home* directory.</span></span>
1. <span data-ttu-id="78b55-164">Sélectionnez les fichiers *de vue about. cshtml* , *contact. cshtml* et *index. cshtml* :::no-loc(Razor)::: , puis sélectionnez **Ajouter** , en remplaçant les fichiers existants.</span><span class="sxs-lookup"><span data-stu-id="78b55-164">Select the *About.cshtml* , *Contact.cshtml* , and *Index.cshtml* :::no-loc(Razor)::: view files, then select **Add** , replacing the existing files.</span></span>

<span data-ttu-id="78b55-165">Pour plus d’informations, consultez <xref:mvc/controllers/actions> et <xref:mvc/views/overview>.</span><span class="sxs-lookup"><span data-stu-id="78b55-165">For more information, see <xref:mvc/controllers/actions> and <xref:mvc/views/overview>.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="78b55-166">Tester chaque méthode</span><span class="sxs-lookup"><span data-stu-id="78b55-166">Test each method</span></span>

<span data-ttu-id="78b55-167">Chaque point de terminaison de contrôleur peut être testé ; Toutefois, la disposition et les styles sont traités plus loin dans le document.</span><span class="sxs-lookup"><span data-stu-id="78b55-167">Each controller endpoint can be tested, however, layout and styles are covered later in the document.</span></span>

1. <span data-ttu-id="78b55-168">Exécutez l’application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="78b55-168">Run the ASP.NET Core app.</span></span>
1. <span data-ttu-id="78b55-169">Appelez les vues rendues à partir du navigateur sur l’application ASP.NET Core en cours en remplaçant le numéro de port actuel par le numéro de port utilisé dans le projet ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="78b55-169">Invoke the rendered views from the browser on the running ASP.NET Core app by replacing the current port number with the port number used in the ASP.NET Core project.</span></span> <span data-ttu-id="78b55-170">Par exemple : `https://localhost:44375/home/about`.</span><span class="sxs-lookup"><span data-stu-id="78b55-170">For example, `https://localhost:44375/home/about`.</span></span>

## <a name="migrate-static-content"></a><span data-ttu-id="78b55-171">Migrer le contenu statique</span><span class="sxs-lookup"><span data-stu-id="78b55-171">Migrate static content</span></span>

<span data-ttu-id="78b55-172">Dans ASP.NET MVC 5 et versions antérieures, le contenu statique était hébergé à partir du répertoire racine du projet Web et a été mélangé aux fichiers côté serveur.</span><span class="sxs-lookup"><span data-stu-id="78b55-172">In ASP.NET MVC 5 and earlier, static content was hosted from the web project's root directory and was intermixed with server-side files.</span></span> <span data-ttu-id="78b55-173">Dans ASP.NET Core, les fichiers statiques sont stockés dans le répertoire [racine Web](xref:fundamentals/index#web-root) du projet.</span><span class="sxs-lookup"><span data-stu-id="78b55-173">In ASP.NET Core, static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="78b55-174">Le répertoire par défaut est *{content root}/wwwroot* , mais il peut être modifié.</span><span class="sxs-lookup"><span data-stu-id="78b55-174">The default directory is *{content root}/wwwroot* , but it can be changed.</span></span> <span data-ttu-id="78b55-175">Pour plus d’informations, consultez [Fichiers statiques dans ASP.NET Core](xref:fundamentals/static-files#serve-static-files).</span><span class="sxs-lookup"><span data-stu-id="78b55-175">For more information, see [Static files in ASP.NET Core](xref:fundamentals/static-files#serve-static-files).</span></span>

<span data-ttu-id="78b55-176">Copiez le contenu statique du projet *application Web 1* MVC ASP.net dans le répertoire *wwwroot* dans le projet ASP.net Core *application Web 1* :</span><span class="sxs-lookup"><span data-stu-id="78b55-176">Copy the static content from the ASP.NET MVC *WebApp1* project to the *wwwroot* directory in the ASP.NET Core *WebApp1* project:</span></span>

1. <span data-ttu-id="78b55-177">Dans le projet ASP.net Core, cliquez avec le bouton droit sur le répertoire *wwwroot* , puis sélectionnez **Ajouter** un > **élément existant** .</span><span class="sxs-lookup"><span data-stu-id="78b55-177">In the ASP.NET Core project, right-click the *wwwroot* directory, select **Add** > **Existing Item** .</span></span>
1. <span data-ttu-id="78b55-178">Dans la boîte de dialogue **Ajouter un élément existant** , accédez au projet ASP.NET MVC *application Web 1* .</span><span class="sxs-lookup"><span data-stu-id="78b55-178">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project.</span></span>
1. <span data-ttu-id="78b55-179">Sélectionnez le fichier *favicon. ico* , puis sélectionnez **Ajouter** , en remplaçant le fichier existant.</span><span class="sxs-lookup"><span data-stu-id="78b55-179">Select the *favicon.ico* file, then select **Add** , replacing the existing file.</span></span>

## <a name="migrate-the-layout-files"></a><span data-ttu-id="78b55-180">Migrer les fichiers de disposition</span><span class="sxs-lookup"><span data-stu-id="78b55-180">Migrate the layout files</span></span>

<span data-ttu-id="78b55-181">Copiez les fichiers de disposition de projet MVC ASP.NET dans le projet ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="78b55-181">Copy the ASP.NET MVC project layout files to the ASP.NET Core project:</span></span>

1. <span data-ttu-id="78b55-182">Dans le projet ASP.net Core, cliquez avec le bouton droit sur le répertoire *views* , puis sélectionnez **Ajouter** un > **élément existant** .</span><span class="sxs-lookup"><span data-stu-id="78b55-182">In the ASP.NET Core project, right-click the *Views* directory, select **Add** > **Existing Item** .</span></span>
1. <span data-ttu-id="78b55-183">Dans la boîte de dialogue **Ajouter un élément existant** , accédez au répertoire *Views* du projet ASP.NET MVC *application Web 1* .</span><span class="sxs-lookup"><span data-stu-id="78b55-183">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views* directory.</span></span>
1. <span data-ttu-id="78b55-184">Sélectionnez le fichier *_ViewStart. cshtml* , puis sélectionnez **Ajouter** .</span><span class="sxs-lookup"><span data-stu-id="78b55-184">Select the *_ViewStart.cshtml* file then select **Add** .</span></span>

<span data-ttu-id="78b55-185">Copiez les fichiers de disposition partagée du projet MVC ASP.NET dans le projet ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="78b55-185">Copy the ASP.NET MVC project shared layout files to the ASP.NET Core project:</span></span>

1. <span data-ttu-id="78b55-186">Dans le projet ASP.net Core, cliquez avec le bouton droit sur le répertoire *Views/Shared* , puis sélectionnez **Ajouter** un > **élément existant** .</span><span class="sxs-lookup"><span data-stu-id="78b55-186">In the ASP.NET Core project, right-click the *Views/Shared* directory, select **Add** > **Existing Item** .</span></span>
1. <span data-ttu-id="78b55-187">Dans la boîte de dialogue **Ajouter un élément existant** , accédez au répertoire *Views/Shared* ASP.net du projet *application Web 1* Mvc.</span><span class="sxs-lookup"><span data-stu-id="78b55-187">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views/Shared* directory.</span></span>
1. <span data-ttu-id="78b55-188">Sélectionnez le fichier *_Layout. cshtml* , puis sélectionnez **Ajouter** , en remplaçant le fichier existant.</span><span class="sxs-lookup"><span data-stu-id="78b55-188">Select the *_Layout.cshtml* file, then select **Add** , replacing the existing file.</span></span>

<span data-ttu-id="78b55-189">Dans le projet ASP.NET Core, ouvrez le fichier *_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="78b55-189">In the ASP.NET Core project, open the *_Layout.cshtml* file.</span></span> <span data-ttu-id="78b55-190">Apportez les modifications suivantes pour correspondre au code complet indiqué ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="78b55-190">Make the following changes to match the completed code shown below:</span></span>

<span data-ttu-id="78b55-191">Mettez à jour l’inclusion CSS de démarrage pour qu’elle corresponde au code complet ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="78b55-191">Update the Bootstrap CSS inclusion to match the completed code below:</span></span>

1. <span data-ttu-id="78b55-192">Remplacez `@Styles.Render("~/Content/css")` par un `<link>` élément pour charger *bootstrap. CSS* (voir ci-dessous).</span><span class="sxs-lookup"><span data-stu-id="78b55-192">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>
1. <span data-ttu-id="78b55-193">Supprimez `@Scripts.Render("~/bundles/modernizr")`.</span><span class="sxs-lookup"><span data-stu-id="78b55-193">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

<span data-ttu-id="78b55-194">Balisage de remplacement terminé pour l’inclusion CSS de démarrage :</span><span class="sxs-lookup"><span data-stu-id="78b55-194">The completed replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="78b55-195">Mettez à jour l’inclusion Java jQuery et bootstrap pour qu’elle corresponde au code complet ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="78b55-195">Update the jQuery and Bootstrap JavaScript inclusion to match the completed code below:</span></span>

1. <span data-ttu-id="78b55-196">Remplacez `@Scripts.Render("~/bundles/jquery")` par un `<script>` élément (voir ci-dessous).</span><span class="sxs-lookup"><span data-stu-id="78b55-196">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>
1. <span data-ttu-id="78b55-197">Remplacez `@Scripts.Render("~/bundles/bootstrap")` par un `<script>` élément (voir ci-dessous).</span><span class="sxs-lookup"><span data-stu-id="78b55-197">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="78b55-198">Balisage de remplacement terminé pour l’inclusion de jQuery et du code d’amorçage JavaScript :</span><span class="sxs-lookup"><span data-stu-id="78b55-198">The completed replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="78b55-199">Le fichier *_Layout. cshtml* mis à jour est affiché ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="78b55-199">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/3.x/Views/Shared/_Layout.cshtml?highlight=7-10,40-42)]

<span data-ttu-id="78b55-200">Affichez le site dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="78b55-200">View the site in the browser.</span></span> <span data-ttu-id="78b55-201">Il doit être rendu avec les styles attendus sur place.</span><span class="sxs-lookup"><span data-stu-id="78b55-201">It should render with the expected styles in place.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="78b55-202">Configurer le regroupement et la minimisation</span><span class="sxs-lookup"><span data-stu-id="78b55-202">Configure bundling and minification</span></span>

<span data-ttu-id="78b55-203">ASP.NET Core est compatible avec plusieurs solutions de réduction et de regroupement Open source, telles que [weboptimizer](https://github.com/ligershark/WebOptimizer) et d’autres bibliothèques similaires.</span><span class="sxs-lookup"><span data-stu-id="78b55-203">ASP.NET Core is compatible with several open-source bundling and minification solutions such as [WebOptimizer](https://github.com/ligershark/WebOptimizer) and other similar libraries.</span></span> <span data-ttu-id="78b55-204">ASP.NET Core ne fournit pas de solution de minimisation et de regroupement native.</span><span class="sxs-lookup"><span data-stu-id="78b55-204">ASP.NET Core doesn't provide a native bundling and minification solution.</span></span> <span data-ttu-id="78b55-205">Pour plus d’informations sur la configuration du regroupement et de la minimisation, consultez [regroupement et minimisation](xref:client-side/bundling-and-minification).</span><span class="sxs-lookup"><span data-stu-id="78b55-205">For information on configuring bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="78b55-206">Résoudre les erreurs HTTP 500</span><span class="sxs-lookup"><span data-stu-id="78b55-206">Solve HTTP 500 errors</span></span>

<span data-ttu-id="78b55-207">De nombreux problèmes peuvent provoquer un message d’erreur HTTP 500 qui ne contient aucune information sur la source du problème.</span><span class="sxs-lookup"><span data-stu-id="78b55-207">There are many problems that can cause an HTTP 500 error message that contains no information on the source of the problem.</span></span> <span data-ttu-id="78b55-208">Par exemple, si le fichier *views/_ViewImports. cshtml* contient un espace de noms qui n’existe pas dans le projet, une erreur HTTP 500 est générée.</span><span class="sxs-lookup"><span data-stu-id="78b55-208">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, an HTTP 500 error is generated.</span></span> <span data-ttu-id="78b55-209">Par défaut, dans ASP.NET Core Apps, l' `UseDeveloperExceptionPage` extension est ajoutée au `IApplicationBuilder` et exécutée lorsque l’environnement est en cours de *développement* .</span><span class="sxs-lookup"><span data-stu-id="78b55-209">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the environment is *Development* .</span></span> <span data-ttu-id="78b55-210">Ce code est détaillé dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="78b55-210">This is detailed in the following code:</span></span>

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=17-21&name=snippet)]

<span data-ttu-id="78b55-211">ASP.NET Core convertit les exceptions non gérées en réponses d’erreur HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="78b55-211">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="78b55-212">Normalement, les détails de l’erreur ne sont pas inclus dans ces réponses pour empêcher la divulgation d’informations potentiellement sensibles sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="78b55-212">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="78b55-213">Pour plus d’informations, consultez la [page exception du développeur](xref:fundamentals/error-handling#developer-exception-page).</span><span class="sxs-lookup"><span data-stu-id="78b55-213">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="next-steps"></a><span data-ttu-id="78b55-214">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="78b55-214">Next steps</span></span>

* <xref:migration/identity>

## <a name="additional-resources"></a><span data-ttu-id="78b55-215">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="78b55-215">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="78b55-216">Cet article explique comment commencer à migrer un projet ASP.NET MVC vers [ASP.net Core mvc](xref:mvc/overview) 2,2.</span><span class="sxs-lookup"><span data-stu-id="78b55-216">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview) 2.2.</span></span> <span data-ttu-id="78b55-217">Dans le processus, il met en évidence un grand nombre des éléments qui ont été modifiés par rapport à ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="78b55-217">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="78b55-218">La migration à partir de ASP.NET MVC est un processus en plusieurs étapes.</span><span class="sxs-lookup"><span data-stu-id="78b55-218">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="78b55-219">Cet article couvre les points suivants :</span><span class="sxs-lookup"><span data-stu-id="78b55-219">This article covers:</span></span>

* <span data-ttu-id="78b55-220">Configuration initiale</span><span class="sxs-lookup"><span data-stu-id="78b55-220">Initial setup</span></span>
* <span data-ttu-id="78b55-221">Contrôleurs et vues de base</span><span class="sxs-lookup"><span data-stu-id="78b55-221">Basic controllers and views</span></span>
* <span data-ttu-id="78b55-222">Contenu statique</span><span class="sxs-lookup"><span data-stu-id="78b55-222">Static content</span></span>
* <span data-ttu-id="78b55-223">Dépendances côté client.</span><span class="sxs-lookup"><span data-stu-id="78b55-223">Client-side dependencies.</span></span>

<span data-ttu-id="78b55-224">Pour la migration de la configuration et du :::no-loc(Identity)::: code, consultez <xref:migration/configuration> et <xref:migration/identity> .</span><span class="sxs-lookup"><span data-stu-id="78b55-224">For migrating configuration and :::no-loc(Identity)::: code, see <xref:migration/configuration> and <xref:migration/identity>.</span></span>

> [!NOTE]
> <span data-ttu-id="78b55-225">Les numéros de version dans les exemples ne sont peut-être pas à jour, mettez à jour les projets en conséquence.</span><span class="sxs-lookup"><span data-stu-id="78b55-225">The version numbers in the samples might not be current, update the projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="78b55-226">Créer le projet MVC ASP.NET de démarrage</span><span class="sxs-lookup"><span data-stu-id="78b55-226">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="78b55-227">Pour illustrer la mise à niveau, nous allons commencer par créer une application ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="78b55-227">To demonstrate the upgrade, we'll start by creating an ASP.NET MVC app.</span></span> <span data-ttu-id="78b55-228">Créez-le avec le nom *application Web 1* pour que l’espace de noms corresponde au projet de ASP.net Core créé à l’étape suivante.</span><span class="sxs-lookup"><span data-stu-id="78b55-228">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span>

![Boîte de dialogue Nouveau projet Visual Studio](mvc/_static/new-project.png)

![Boîte de dialogue nouvelle application Web : modèle de projet MVC sélectionné dans le panneau modèles ASP.NET](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="78b55-231">*Facultatif :* Remplacez le nom de la solution *application Web 1* par *Mvc5* .</span><span class="sxs-lookup"><span data-stu-id="78b55-231">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5* .</span></span> <span data-ttu-id="78b55-232">Visual Studio affiche le nouveau nom de la solution ( *Mvc5* ), ce qui vous permet d’indiquer plus facilement ce projet à partir du projet suivant.</span><span class="sxs-lookup"><span data-stu-id="78b55-232">Visual Studio displays the new solution name ( *Mvc5* ), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="78b55-233">Créer le projet ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="78b55-233">Create the ASP.NET Core project</span></span>

<span data-ttu-id="78b55-234">Créez une application Web ASP.NET Core *vide* portant le même nom que le projet précédent ( *application Web 1* ) de sorte que les espaces de noms des deux projets correspondent.</span><span class="sxs-lookup"><span data-stu-id="78b55-234">Create a new *empty* ASP.NET Core web app with the same name as the previous project ( *WebApp1* ) so the namespaces in the two projects match.</span></span> <span data-ttu-id="78b55-235">Le fait d’avoir le même espace de noms facilite la copie du code entre les deux projets.</span><span class="sxs-lookup"><span data-stu-id="78b55-235">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="78b55-236">Créez ce projet dans un répertoire différent de celui du projet précédent pour utiliser le même nom.</span><span class="sxs-lookup"><span data-stu-id="78b55-236">Create this project in a different directory than the previous project to use the same name.</span></span>

![Boîte de dialogue Nouveau projet](mvc/_static/new_core.png)

![Boîte de dialogue nouvelle application Web ASP.NET : modèle de projet vide sélectionné dans ASP.NET Core panneau modèles](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="78b55-239">*Facultatif :* Créez une nouvelle ASP.NET Core application à l’aide du modèle de projet d' *application Web* .</span><span class="sxs-lookup"><span data-stu-id="78b55-239">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="78b55-240">Nommez le projet *application Web 1* , puis sélectionnez l’option d’authentification des **comptes d’utilisateur individuels** .</span><span class="sxs-lookup"><span data-stu-id="78b55-240">Name the project *WebApp1* , and select an authentication option of **Individual User Accounts** .</span></span> <span data-ttu-id="78b55-241">Renommez cette application en *FullAspNetCore* .</span><span class="sxs-lookup"><span data-stu-id="78b55-241">Rename this app to *FullAspNetCore* .</span></span> <span data-ttu-id="78b55-242">La création de ce projet permet de gagner du temps lors de la conversion.</span><span class="sxs-lookup"><span data-stu-id="78b55-242">Creating this project saves time in the conversion.</span></span> <span data-ttu-id="78b55-243">Le résultat final peut être affiché dans le code généré par le modèle, le code peut être copié dans le projet de conversion ou comparé au projet généré par le modèle.</span><span class="sxs-lookup"><span data-stu-id="78b55-243">The end result can be viewed in the template-generated code, code can be copied to the conversion project, or compared with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="78b55-244">Configurer le site pour utiliser MVC</span><span class="sxs-lookup"><span data-stu-id="78b55-244">Configure the site to use MVC</span></span>

* <span data-ttu-id="78b55-245">Quand vous ciblez .NET Core, le [AspNetCore Microsoft.. app](xref:fundamentals/metapackage-app) est référencé par défaut.</span><span class="sxs-lookup"><span data-stu-id="78b55-245">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="78b55-246">Ce package contient des packages couramment utilisés par les applications MVC.</span><span class="sxs-lookup"><span data-stu-id="78b55-246">This package contains packages commonly used by MVC apps.</span></span> <span data-ttu-id="78b55-247">Si vous ciblez .NET Framework, les références de package doivent être listées individuellement dans le fichier projet.</span><span class="sxs-lookup"><span data-stu-id="78b55-247">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

<span data-ttu-id="78b55-248">`Microsoft.AspNetCore.Mvc` est l’infrastructure MVC ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="78b55-248">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="78b55-249">`Microsoft.AspNetCore.StaticFiles` est le gestionnaire de fichiers statiques.</span><span class="sxs-lookup"><span data-stu-id="78b55-249">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="78b55-250">ASP.NET Core applications s’abonnent explicitement à des intergiciels (middleware), comme pour le traitement des fichiers statiques.</span><span class="sxs-lookup"><span data-stu-id="78b55-250">ASP.NET Core apps explicitly opt in for middleware, such as for serving static files.</span></span> <span data-ttu-id="78b55-251">Pour plus d’informations, consultez [Fichiers statiques](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="78b55-251">For more information, see [Static files](xref:fundamentals/static-files).</span></span>

* <span data-ttu-id="78b55-252">Ouvrez le fichier *Startup.cs* et modifiez le code pour qu’il corresponde à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="78b55-252">Open the *Startup.cs* file and change the code to match the following:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

<span data-ttu-id="78b55-253">La <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> méthode d’extension ajoute le gestionnaire de fichiers statiques.</span><span class="sxs-lookup"><span data-stu-id="78b55-253">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> extension method adds the static file handler.</span></span> <span data-ttu-id="78b55-254">Pour plus d’informations, consultez démarrage et [routage](xref:fundamentals/routing)de l' [application](xref:fundamentals/startup) .</span><span class="sxs-lookup"><span data-stu-id="78b55-254">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="78b55-255">Ajouter un contrôleur et une vue</span><span class="sxs-lookup"><span data-stu-id="78b55-255">Add a controller and view</span></span>

<span data-ttu-id="78b55-256">Dans cette section, vous ajoutez un contrôleur et une vue minimaux pour servir d’espaces réservés pour le contrôleur MVC ASP.NET et les vues migrées dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="78b55-256">In this section, a minimal controller and view are added to serve as placeholders for the ASP.NET MVC controller and views migrated in the next section.</span></span>

* <span data-ttu-id="78b55-257">Ajoutez un répertoire *Controllers* .</span><span class="sxs-lookup"><span data-stu-id="78b55-257">Add a *Controllers* directory.</span></span>

* <span data-ttu-id="78b55-258">Ajoutez une **classe de contrôleur** nommée *HomeController.cs* au répertoire *Controllers* .</span><span class="sxs-lookup"><span data-stu-id="78b55-258">Add a **Controller Class** named *HomeController.cs* to the *Controllers* directory.</span></span>

![Boîte de dialogue Ajouter un nouvel élément](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="78b55-260">Ajoutez un répertoire *views* .</span><span class="sxs-lookup"><span data-stu-id="78b55-260">Add a *Views* directory.</span></span>

* <span data-ttu-id="78b55-261">Ajoutez un répertoire *views/racine* .</span><span class="sxs-lookup"><span data-stu-id="78b55-261">Add a *Views/Home* directory.</span></span>

* <span data-ttu-id="78b55-262">Ajoutez un **:::no-loc(Razor)::: affichage** nommé *index. cshtml* au répertoire *views/de départ* .</span><span class="sxs-lookup"><span data-stu-id="78b55-262">Add a **:::no-loc(Razor)::: View** named *Index.cshtml* to the *Views/Home* directory.</span></span>

![Boîte de dialogue Ajouter un nouvel élément](mvc/_static/view.png)

<span data-ttu-id="78b55-264">La structure du projet est indiquée ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="78b55-264">The project structure is shown below:</span></span>

![Explorateur de solutions de l’indication des fichiers et répertoires de application Web 1](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="78b55-266">Remplacez le contenu du fichier *Views/Home/Index.cshtml* par le balisage suivant :</span><span class="sxs-lookup"><span data-stu-id="78b55-266">Replace the contents of the *Views/Home/Index.cshtml* file with the following markup:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="78b55-267">Exécutez l’application.</span><span class="sxs-lookup"><span data-stu-id="78b55-267">Run the app.</span></span>

![Application Web ouverte dans Microsoft Edge](mvc/_static/hello-world.png)

<span data-ttu-id="78b55-269">Pour plus d’informations, consultez [contrôleurs](xref:mvc/controllers/actions) et [vues](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="78b55-269">For more information, see [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview).</span></span>

<span data-ttu-id="78b55-270">Les fonctionnalités suivantes requièrent la migration à partir de l’exemple de projet MVC ASP.NET vers le projet ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="78b55-270">The following functionality requires migration from the example ASP.NET MVC project to the ASP.NET Core project:</span></span>

* <span data-ttu-id="78b55-271">contenu côté client (CSS, polices et scripts)</span><span class="sxs-lookup"><span data-stu-id="78b55-271">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="78b55-272">controllers</span><span class="sxs-lookup"><span data-stu-id="78b55-272">controllers</span></span>

* <span data-ttu-id="78b55-273">views</span><span class="sxs-lookup"><span data-stu-id="78b55-273">views</span></span>

* <span data-ttu-id="78b55-274">modèles</span><span class="sxs-lookup"><span data-stu-id="78b55-274">models</span></span>

* <span data-ttu-id="78b55-275">regroupement</span><span class="sxs-lookup"><span data-stu-id="78b55-275">bundling</span></span>

* <span data-ttu-id="78b55-276">filtres</span><span class="sxs-lookup"><span data-stu-id="78b55-276">filters</span></span>

* <span data-ttu-id="78b55-277">Connectez-vous, :::no-loc(Identity)::: (cette opération est effectuée dans le didacticiel suivant).</span><span class="sxs-lookup"><span data-stu-id="78b55-277">Log in/out, :::no-loc(Identity)::: (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="78b55-278">Contrôleurs et vues</span><span class="sxs-lookup"><span data-stu-id="78b55-278">Controllers and views</span></span>

* <span data-ttu-id="78b55-279">Copiez chacune des méthodes du MVC ASP.NET `HomeController` vers le nouveau `HomeController` .</span><span class="sxs-lookup"><span data-stu-id="78b55-279">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="78b55-280">Dans ASP.NET MVC, le type de retour de la méthode d’action du contrôleur intégré au modèle est <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> ; dans ASP.net Core MVC, les méthodes d’action retournent à la `IActionResult` place.</span><span class="sxs-lookup"><span data-stu-id="78b55-280">In ASP.NET MVC, the built-in template's controller action method return type is <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2>; in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="78b55-281">`ActionResult` implémente `IActionResult` , il n’est donc pas nécessaire de modifier le type de retour des méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="78b55-281">`ActionResult` implements `IActionResult`, so there's no need to change the return type of the action methods.</span></span>

* <span data-ttu-id="78b55-282">Copiez les fichiers de vue *about. cshtml* , *contact. cshtml* et *index. cshtml* :::no-loc(Razor)::: du projet MVC ASP.net dans le projet ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="78b55-282">Copy the *About.cshtml* , *Contact.cshtml* , and *Index.cshtml* :::no-loc(Razor)::: view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="78b55-283">Tester chaque méthode</span><span class="sxs-lookup"><span data-stu-id="78b55-283">Test each method</span></span>

<span data-ttu-id="78b55-284">Le fichier de disposition et les styles n’ont pas encore été migrés, donc les vues rendues contiennent uniquement le contenu des fichiers de vue.</span><span class="sxs-lookup"><span data-stu-id="78b55-284">The layout file and styles have not been migrated yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="78b55-285">Les liens générés par le fichier de disposition pour les `About` vues et ne sont `Contact` pas encore disponibles.</span><span class="sxs-lookup"><span data-stu-id="78b55-285">The layout file generated links for the `About` and `Contact` views will not be available yet.</span></span>

<span data-ttu-id="78b55-286">Appelez les vues rendues à partir du navigateur sur l’application ASP.NET Core en cours d’exécution en remplaçant le numéro de port actuel par le numéro de port utilisé dans le projet ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="78b55-286">Invoke the rendered views from the browser on the running ASP.NET core app by replacing the current port number with the port number used in the ASP.NET core project.</span></span> <span data-ttu-id="78b55-287">Par exemple : `https://localhost:44375/home/about`.</span><span class="sxs-lookup"><span data-stu-id="78b55-287">For example: `https://localhost:44375/home/about`.</span></span>

![Page de contact](mvc/_static/contact-page.png)

<span data-ttu-id="78b55-289">Notez l’absence de styles et d’éléments de menu.</span><span class="sxs-lookup"><span data-stu-id="78b55-289">Note the lack of styling and menu items.</span></span> <span data-ttu-id="78b55-290">Le style sera corrigé dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="78b55-290">The styling will be fixed in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="78b55-291">Contenu statique</span><span class="sxs-lookup"><span data-stu-id="78b55-291">Static content</span></span>

<span data-ttu-id="78b55-292">Dans ASP.NET MVC 5 et versions antérieures, le contenu statique était hébergé à partir de la racine du projet Web et a été mélangé aux fichiers côté serveur.</span><span class="sxs-lookup"><span data-stu-id="78b55-292">In ASP.NET MVC 5 and earlier, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="78b55-293">Dans ASP.NET Core, le contenu statique est hébergé dans le répertoire *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="78b55-293">In ASP.NET Core, static content is hosted in the *wwwroot* directory.</span></span> <span data-ttu-id="78b55-294">Copiez le contenu statique de l’application ASP.NET MVC dans le répertoire *wwwroot* dans le projet ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="78b55-294">Copy the static content from the ASP.NET MVC app to the *wwwroot* directory in the ASP.NET Core project.</span></span> <span data-ttu-id="78b55-295">Dans cet exemple de conversion :</span><span class="sxs-lookup"><span data-stu-id="78b55-295">In this sample conversion:</span></span>

* <span data-ttu-id="78b55-296">Copiez le fichier *favicon. ico* à partir du projet MVC ASP.net dans le répertoire *wwwroot* dans le projet ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="78b55-296">Copy the *favicon.ico* file from the ASP.NET MVC project to the *wwwroot* directory in the ASP.NET Core project.</span></span>

<span data-ttu-id="78b55-297">Le projet MVC ASP.NET utilise [bootstrap](https://getbootstrap.com/) pour son style et stocke les fichiers de démarrage dans les répertoires de *contenu* et de *scripts* .</span><span class="sxs-lookup"><span data-stu-id="78b55-297">The ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* directories.</span></span> <span data-ttu-id="78b55-298">Le modèle, qui a généré le projet MVC ASP.NET, référence le bootstrap dans le fichier de disposition ( *Views/Shared/_Layout. cshtml* ).</span><span class="sxs-lookup"><span data-stu-id="78b55-298">The template, which generated the ASP.NET MVC project, references Bootstrap in the layout file ( *Views/Shared/_Layout.cshtml* ).</span></span> <span data-ttu-id="78b55-299">Les fichiers *bootstrap.js* et *bootstrap. CSS* peuvent être copiés du projet MVC ASP.net vers le répertoire *wwwroot* dans le nouveau projet.</span><span class="sxs-lookup"><span data-stu-id="78b55-299">The *bootstrap.js* and *bootstrap.css* files could be copied from the ASP.NET MVC project to the *wwwroot* directory in the new project.</span></span> <span data-ttu-id="78b55-300">Au lieu de cela, ce document ajoute la prise en charge des démarrages (et d’autres bibliothèques côté client) à l’aide de CDN, dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="78b55-300">Instead, this document adds support for Bootstrap (and other client-side libraries) using CDNs, in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="78b55-301">Migrer le fichier de disposition</span><span class="sxs-lookup"><span data-stu-id="78b55-301">Migrate the layout file</span></span>

* <span data-ttu-id="78b55-302">Copiez le fichier *_ViewStart. cshtml* à partir du répertoire *views* du projet MVC ASP.net dans le répertoire *views* du projet ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="78b55-302">Copy the *_ViewStart.cshtml* file from the ASP.NET MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="78b55-303">Le fichier *_ViewStart. cshtml* n’a pas été modifié dans ASP.net Core Mvc.</span><span class="sxs-lookup"><span data-stu-id="78b55-303">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="78b55-304">Créez un répertoire *Views/Shared* .</span><span class="sxs-lookup"><span data-stu-id="78b55-304">Create a *Views/Shared* directory.</span></span>

* <span data-ttu-id="78b55-305">*Facultatif :* Copiez *_ViewImports. cshtml* du répertoire *views* du projet MVC *FullAspNetCore* dans le répertoire *views* du projet ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="78b55-305">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="78b55-306">Supprimez toute déclaration d’espace de noms dans le fichier *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="78b55-306">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="78b55-307">Le fichier *_ViewImports. cshtml* fournit des espaces de noms pour tous les fichiers de vue et insère les [balises](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="78b55-307">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="78b55-308">Les tag helpers sont utilisés dans le nouveau fichier de disposition.</span><span class="sxs-lookup"><span data-stu-id="78b55-308">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="78b55-309">Le fichier *_ViewImports. cshtml* est nouveau pour ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="78b55-309">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="78b55-310">Copiez le fichier *_Layout. cshtml* à partir du répertoire *Views/Shared* du projet MVC ASP.net dans le répertoire *Views/Shared* du projet ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="78b55-310">Copy the *_Layout.cshtml* file from the ASP.NET MVC project's *Views/Shared* directory into the ASP.NET Core project's *Views/Shared* directory.</span></span>

<span data-ttu-id="78b55-311">Ouvrez *_Layout fichier. cshtml* et apportez les modifications suivantes (le code complet est indiqué ci-dessous) :</span><span class="sxs-lookup"><span data-stu-id="78b55-311">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="78b55-312">Remplacez `@Styles.Render("~/Content/css")` par un `<link>` élément pour charger *bootstrap. CSS* (voir ci-dessous).</span><span class="sxs-lookup"><span data-stu-id="78b55-312">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="78b55-313">Supprimez `@Scripts.Render("~/bundles/modernizr")`.</span><span class="sxs-lookup"><span data-stu-id="78b55-313">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="78b55-314">Commentez la `@Html.Partial("_LoginPartial")` ligne (entourez la ligne de `@*...*@` ).</span><span class="sxs-lookup"><span data-stu-id="78b55-314">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="78b55-315">Pour plus d’informations, consultez [migrer l’authentification et :::no-loc(Identity)::: vers ASP.net Core](xref:migration/identity)</span><span class="sxs-lookup"><span data-stu-id="78b55-315">For more information, see [Migrate Authentication and :::no-loc(Identity)::: to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="78b55-316">Remplacez `@Scripts.Render("~/bundles/jquery")` par un `<script>` élément (voir ci-dessous).</span><span class="sxs-lookup"><span data-stu-id="78b55-316">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="78b55-317">Remplacez `@Scripts.Render("~/bundles/bootstrap")` par un `<script>` élément (voir ci-dessous).</span><span class="sxs-lookup"><span data-stu-id="78b55-317">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="78b55-318">Balisage de remplacement pour l’inclusion CSS de démarrage :</span><span class="sxs-lookup"><span data-stu-id="78b55-318">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="78b55-319">Balisage de remplacement pour l’inclusion de jQuery et du code d’amorçage JavaScript :</span><span class="sxs-lookup"><span data-stu-id="78b55-319">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="78b55-320">Le fichier *_Layout. cshtml* mis à jour est affiché ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="78b55-320">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="78b55-321">Affichez le site dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="78b55-321">View the site in the browser.</span></span> <span data-ttu-id="78b55-322">Elle doit maintenant se charger correctement, avec les styles attendus en place.</span><span class="sxs-lookup"><span data-stu-id="78b55-322">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="78b55-323">*Facultatif :* Essayez d’utiliser le nouveau fichier de disposition.</span><span class="sxs-lookup"><span data-stu-id="78b55-323">*Optional:* Try using the new layout file.</span></span> <span data-ttu-id="78b55-324">Copiez le fichier de disposition à partir du projet *FullAspNetCore* .</span><span class="sxs-lookup"><span data-stu-id="78b55-324">Copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="78b55-325">Le nouveau fichier de disposition utilise [tag Helper](xref:mvc/views/tag-helpers/intro) et présente d’autres améliorations.</span><span class="sxs-lookup"><span data-stu-id="78b55-325">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="78b55-326">Configurer le regroupement et la minimisation</span><span class="sxs-lookup"><span data-stu-id="78b55-326">Configure bundling and minification</span></span>

<span data-ttu-id="78b55-327">Pour plus d’informations sur la configuration du regroupement et de la minimisation, consultez [regroupement et minimisation](xref:client-side/bundling-and-minification).</span><span class="sxs-lookup"><span data-stu-id="78b55-327">For information about how to configure bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="78b55-328">Résoudre les erreurs HTTP 500</span><span class="sxs-lookup"><span data-stu-id="78b55-328">Solve HTTP 500 errors</span></span>

<span data-ttu-id="78b55-329">De nombreux problèmes peuvent provoquer des messages d’erreur HTTP 500 qui ne contiennent aucune information sur la source du problème.</span><span class="sxs-lookup"><span data-stu-id="78b55-329">There are many problems that can cause an HTTP 500 error messages that contain no information on the source of the problem.</span></span> <span data-ttu-id="78b55-330">Par exemple, si le fichier *views/_ViewImports. cshtml* contient un espace de noms qui n’existe pas dans le projet, une erreur HTTP 500 est générée.</span><span class="sxs-lookup"><span data-stu-id="78b55-330">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, a HTTP 500 error is generated.</span></span> <span data-ttu-id="78b55-331">Par défaut, dans ASP.NET Core Apps, l' `UseDeveloperExceptionPage` extension est ajoutée au `IApplicationBuilder` et exécutée lorsque la configuration est en cours de *développement* .</span><span class="sxs-lookup"><span data-stu-id="78b55-331">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development* .</span></span> <span data-ttu-id="78b55-332">Consultez un exemple dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="78b55-332">See an example in the following code:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

<span data-ttu-id="78b55-333">ASP.NET Core convertit les exceptions non gérées en réponses d’erreur HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="78b55-333">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="78b55-334">Normalement, les détails de l’erreur ne sont pas inclus dans ces réponses pour empêcher la divulgation d’informations potentiellement sensibles sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="78b55-334">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="78b55-335">Pour plus d’informations, consultez la [page exception du développeur](xref:fundamentals/error-handling#developer-exception-page).</span><span class="sxs-lookup"><span data-stu-id="78b55-335">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="78b55-336">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="78b55-336">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

<span data-ttu-id="78b55-337">Cet article explique comment commencer à migrer un projet ASP.NET MVC vers [ASP.net Core mvc](xref:mvc/overview) 2,1.</span><span class="sxs-lookup"><span data-stu-id="78b55-337">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview) 2.1.</span></span> <span data-ttu-id="78b55-338">Dans le processus, il met en évidence un grand nombre des éléments qui ont été modifiés par rapport à ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="78b55-338">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="78b55-339">La migration à partir de ASP.NET MVC est un processus en plusieurs étapes.</span><span class="sxs-lookup"><span data-stu-id="78b55-339">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="78b55-340">Cet article couvre les points suivants :</span><span class="sxs-lookup"><span data-stu-id="78b55-340">This article covers:</span></span>

* <span data-ttu-id="78b55-341">Configuration initiale</span><span class="sxs-lookup"><span data-stu-id="78b55-341">Initial setup</span></span>
* <span data-ttu-id="78b55-342">Contrôleurs et vues de base</span><span class="sxs-lookup"><span data-stu-id="78b55-342">Basic controllers and views</span></span>
* <span data-ttu-id="78b55-343">Contenu statique</span><span class="sxs-lookup"><span data-stu-id="78b55-343">Static content</span></span>
* <span data-ttu-id="78b55-344">Dépendances côté client.</span><span class="sxs-lookup"><span data-stu-id="78b55-344">Client-side dependencies.</span></span>

<span data-ttu-id="78b55-345">Pour la migration de la configuration et du :::no-loc(Identity)::: code, consultez [migrer la configuration vers ASP.net Core](xref:migration/configuration) et [migrer l’authentification et :::no-loc(Identity)::: vers ASP.net Core](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="78b55-345">For migrating configuration and :::no-loc(Identity)::: code, see [Migrate configuration to ASP.NET Core](xref:migration/configuration) and [Migrate Authentication and :::no-loc(Identity)::: to ASP.NET Core](xref:migration/identity).</span></span>

> [!NOTE]
> <span data-ttu-id="78b55-346">Les numéros de version dans les exemples ne sont peut-être pas à jour, mettez à jour les projets en conséquence.</span><span class="sxs-lookup"><span data-stu-id="78b55-346">The version numbers in the samples might not be current, update the projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="78b55-347">Créer le projet MVC ASP.NET de démarrage</span><span class="sxs-lookup"><span data-stu-id="78b55-347">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="78b55-348">Pour illustrer la mise à niveau, nous allons commencer par créer une application ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="78b55-348">To demonstrate the upgrade, we'll start by creating a ASP.NET MVC app.</span></span> <span data-ttu-id="78b55-349">Créez-le avec le nom *application Web 1* pour que l’espace de noms corresponde au projet de ASP.net Core créé à l’étape suivante.</span><span class="sxs-lookup"><span data-stu-id="78b55-349">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span>

![Boîte de dialogue Nouveau projet Visual Studio](mvc/_static/new-project.png)

![Boîte de dialogue nouvelle application Web : modèle de projet MVC sélectionné dans le panneau modèles ASP.NET](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="78b55-352">*Facultatif :* Remplacez le nom de la solution *application Web 1* par *Mvc5* .</span><span class="sxs-lookup"><span data-stu-id="78b55-352">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5* .</span></span> <span data-ttu-id="78b55-353">Visual Studio affiche le nouveau nom de la solution ( *Mvc5* ), ce qui vous permet d’indiquer plus facilement ce projet à partir du projet suivant.</span><span class="sxs-lookup"><span data-stu-id="78b55-353">Visual Studio displays the new solution name ( *Mvc5* ), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="78b55-354">Créer le projet ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="78b55-354">Create the ASP.NET Core project</span></span>

<span data-ttu-id="78b55-355">Créez une application Web ASP.NET Core *vide* portant le même nom que le projet précédent ( *application Web 1* ) de sorte que les espaces de noms des deux projets correspondent.</span><span class="sxs-lookup"><span data-stu-id="78b55-355">Create a new *empty* ASP.NET Core web app with the same name as the previous project ( *WebApp1* ) so the namespaces in the two projects match.</span></span> <span data-ttu-id="78b55-356">Le fait d’avoir le même espace de noms facilite la copie du code entre les deux projets.</span><span class="sxs-lookup"><span data-stu-id="78b55-356">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="78b55-357">Créez ce projet dans un répertoire différent de celui du projet précédent pour utiliser le même nom.</span><span class="sxs-lookup"><span data-stu-id="78b55-357">Create this project in a different directory than the previous project to use the same name.</span></span>

![Boîte de dialogue Nouveau projet](mvc/_static/new_core.png)

![Boîte de dialogue nouvelle application Web ASP.NET : modèle de projet vide sélectionné dans ASP.NET Core panneau modèles](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="78b55-360">*Facultatif :* Créez une nouvelle ASP.NET Core application à l’aide du modèle de projet d' *application Web* .</span><span class="sxs-lookup"><span data-stu-id="78b55-360">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="78b55-361">Nommez le projet *application Web 1* , puis sélectionnez l’option d’authentification des **comptes d’utilisateur individuels** .</span><span class="sxs-lookup"><span data-stu-id="78b55-361">Name the project *WebApp1* , and select an authentication option of **Individual User Accounts** .</span></span> <span data-ttu-id="78b55-362">Renommez cette application en *FullAspNetCore* .</span><span class="sxs-lookup"><span data-stu-id="78b55-362">Rename this app to *FullAspNetCore* .</span></span> <span data-ttu-id="78b55-363">La création de ce projet permet de gagner du temps lors de la conversion.</span><span class="sxs-lookup"><span data-stu-id="78b55-363">Creating this project saves time in the conversion.</span></span> <span data-ttu-id="78b55-364">Le résultat final peut être affiché dans le code généré par le modèle, le code peut être copié dans le projet de conversion ou comparé au projet généré par le modèle.</span><span class="sxs-lookup"><span data-stu-id="78b55-364">The end result can be viewed in the template-generated code, code can be copied to the conversion project, or compared with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="78b55-365">Configurer le site pour utiliser MVC</span><span class="sxs-lookup"><span data-stu-id="78b55-365">Configure the site to use MVC</span></span>

* <span data-ttu-id="78b55-366">Quand vous ciblez .NET Core, le [AspNetCore Microsoft.. app](xref:fundamentals/metapackage-app) est référencé par défaut.</span><span class="sxs-lookup"><span data-stu-id="78b55-366">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="78b55-367">Ce package contient des packages couramment utilisés par les applications MVC.</span><span class="sxs-lookup"><span data-stu-id="78b55-367">This package contains packages commonly used by MVC apps.</span></span> <span data-ttu-id="78b55-368">Si vous ciblez .NET Framework, les références de package doivent être listées individuellement dans le fichier projet.</span><span class="sxs-lookup"><span data-stu-id="78b55-368">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

<span data-ttu-id="78b55-369">`Microsoft.AspNetCore.Mvc` est l’infrastructure MVC ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="78b55-369">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="78b55-370">`Microsoft.AspNetCore.StaticFiles` est le gestionnaire de fichiers statiques.</span><span class="sxs-lookup"><span data-stu-id="78b55-370">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="78b55-371">ASP.NET Core applications s’abonnent explicitement à des intergiciels (middleware), comme pour le traitement des fichiers statiques.</span><span class="sxs-lookup"><span data-stu-id="78b55-371">ASP.NET Core apps explicitly opt in for middleware, such as for serving static files.</span></span> <span data-ttu-id="78b55-372">Pour plus d’informations, consultez [Fichiers statiques](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="78b55-372">For more information, see [Static files](xref:fundamentals/static-files).</span></span>

* <span data-ttu-id="78b55-373">Ouvrez le fichier *Startup.cs* et modifiez le code pour qu’il corresponde à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="78b55-373">Open the *Startup.cs* file and change the code to match the following:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

<span data-ttu-id="78b55-374">La <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> méthode d’extension ajoute le gestionnaire de fichiers statiques.</span><span class="sxs-lookup"><span data-stu-id="78b55-374">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> extension method adds the static file handler.</span></span> <span data-ttu-id="78b55-375">La `UseMvc` méthode d’extension ajoute le routage.</span><span class="sxs-lookup"><span data-stu-id="78b55-375">The `UseMvc` extension method adds routing.</span></span> <span data-ttu-id="78b55-376">Pour plus d’informations, consultez démarrage et [routage](xref:fundamentals/routing)de l' [application](xref:fundamentals/startup) .</span><span class="sxs-lookup"><span data-stu-id="78b55-376">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="78b55-377">Ajouter un contrôleur et une vue</span><span class="sxs-lookup"><span data-stu-id="78b55-377">Add a controller and view</span></span>

<span data-ttu-id="78b55-378">Dans cette section, vous ajoutez un contrôleur et une vue minimaux pour servir d’espaces réservés pour le contrôleur MVC ASP.NET et les vues migrées dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="78b55-378">In this section, a minimal controller and view are added to serve as placeholders for the ASP.NET MVC controller and views migrated in the next section.</span></span>

* <span data-ttu-id="78b55-379">Ajoutez un répertoire *Controllers* .</span><span class="sxs-lookup"><span data-stu-id="78b55-379">Add a *Controllers* directory.</span></span>

* <span data-ttu-id="78b55-380">Ajoutez une **classe de contrôleur** nommée *HomeController.cs* au répertoire *Controllers* .</span><span class="sxs-lookup"><span data-stu-id="78b55-380">Add a **Controller Class** named *HomeController.cs* to the *Controllers* directory.</span></span>

![Boîte de dialogue Ajouter un nouvel élément](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="78b55-382">Ajoutez un répertoire *views* .</span><span class="sxs-lookup"><span data-stu-id="78b55-382">Add a *Views* directory.</span></span>

* <span data-ttu-id="78b55-383">Ajoutez un répertoire *views/racine* .</span><span class="sxs-lookup"><span data-stu-id="78b55-383">Add a *Views/Home* directory.</span></span>

* <span data-ttu-id="78b55-384">Ajoutez un **:::no-loc(Razor)::: affichage** nommé *index. cshtml* au répertoire *views/de départ* .</span><span class="sxs-lookup"><span data-stu-id="78b55-384">Add a **:::no-loc(Razor)::: View** named *Index.cshtml* to the *Views/Home* directory.</span></span>

![Boîte de dialogue Ajouter un nouvel élément](mvc/_static/view.png)

<span data-ttu-id="78b55-386">La structure du projet est indiquée ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="78b55-386">The project structure is shown below:</span></span>

![Explorateur de solutions de l’indication des fichiers et répertoires de application Web 1](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="78b55-388">Remplacez le contenu du fichier *Views/Home/Index.cshtml* par le balisage suivant :</span><span class="sxs-lookup"><span data-stu-id="78b55-388">Replace the contents of the *Views/Home/Index.cshtml* file with the following markup:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="78b55-389">Exécutez l’application.</span><span class="sxs-lookup"><span data-stu-id="78b55-389">Run the app.</span></span>

![Application Web ouverte dans Microsoft Edge](mvc/_static/hello-world.png)

<span data-ttu-id="78b55-391">Pour plus d’informations, consultez [contrôleurs](xref:mvc/controllers/actions) et [vues](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="78b55-391">For more information, see [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview).</span></span>

<span data-ttu-id="78b55-392">Les fonctionnalités suivantes requièrent la migration à partir de l’exemple de projet MVC ASP.NET vers le projet ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="78b55-392">The following functionality requires migration from the example ASP.NET MVC project to the ASP.NET Core project:</span></span>

* <span data-ttu-id="78b55-393">contenu côté client (CSS, polices et scripts)</span><span class="sxs-lookup"><span data-stu-id="78b55-393">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="78b55-394">controllers</span><span class="sxs-lookup"><span data-stu-id="78b55-394">controllers</span></span>

* <span data-ttu-id="78b55-395">views</span><span class="sxs-lookup"><span data-stu-id="78b55-395">views</span></span>

* <span data-ttu-id="78b55-396">modèles</span><span class="sxs-lookup"><span data-stu-id="78b55-396">models</span></span>

* <span data-ttu-id="78b55-397">regroupement</span><span class="sxs-lookup"><span data-stu-id="78b55-397">bundling</span></span>

* <span data-ttu-id="78b55-398">filtres</span><span class="sxs-lookup"><span data-stu-id="78b55-398">filters</span></span>

* <span data-ttu-id="78b55-399">Connectez-vous, :::no-loc(Identity)::: (cette opération est effectuée dans le didacticiel suivant).</span><span class="sxs-lookup"><span data-stu-id="78b55-399">Log in/out, :::no-loc(Identity)::: (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="78b55-400">Contrôleurs et vues</span><span class="sxs-lookup"><span data-stu-id="78b55-400">Controllers and views</span></span>

* <span data-ttu-id="78b55-401">Copiez chacune des méthodes du MVC ASP.NET `HomeController` vers le nouveau `HomeController` .</span><span class="sxs-lookup"><span data-stu-id="78b55-401">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="78b55-402">Dans ASP.NET MVC, le type de retour de la méthode d’action du contrôleur intégré au modèle est <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> ; dans ASP.net Core MVC, les méthodes d’action retournent à la `IActionResult` place.</span><span class="sxs-lookup"><span data-stu-id="78b55-402">In ASP.NET MVC, the built-in template's controller action method return type is <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2>; in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="78b55-403">`ActionResult` implémente `IActionResult` , il n’est donc pas nécessaire de modifier le type de retour des méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="78b55-403">`ActionResult` implements `IActionResult`, so there's no need to change the return type of the action methods.</span></span>

* <span data-ttu-id="78b55-404">Copiez les fichiers de vue *about. cshtml* , *contact. cshtml* et *index. cshtml* :::no-loc(Razor)::: du projet MVC ASP.net dans le projet ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="78b55-404">Copy the *About.cshtml* , *Contact.cshtml* , and *Index.cshtml* :::no-loc(Razor)::: view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="78b55-405">Tester chaque méthode</span><span class="sxs-lookup"><span data-stu-id="78b55-405">Test each method</span></span>

<span data-ttu-id="78b55-406">Le fichier de disposition et les styles n’ont pas encore été migrés, donc les vues rendues contiennent uniquement le contenu des fichiers de vue.</span><span class="sxs-lookup"><span data-stu-id="78b55-406">The layout file and styles have not been migrated yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="78b55-407">Les liens générés par le fichier de disposition pour les `About` vues et ne sont `Contact` pas encore disponibles.</span><span class="sxs-lookup"><span data-stu-id="78b55-407">The layout file generated links for the `About` and `Contact` views will not be available yet.</span></span>

* <span data-ttu-id="78b55-408">Appelez les vues rendues à partir du navigateur sur l’application ASP.NET Core en cours d’exécution en remplaçant le numéro de port actuel par le numéro de port utilisé dans le projet ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="78b55-408">Invoke the rendered views from the browser on the running ASP.NET core app by replacing the current port number with the port number used in the ASP.NET core project.</span></span> <span data-ttu-id="78b55-409">Par exemple : `https://localhost:44375/home/about`.</span><span class="sxs-lookup"><span data-stu-id="78b55-409">For example: `https://localhost:44375/home/about`.</span></span>

![Page de contact](mvc/_static/contact-page.png)

<span data-ttu-id="78b55-411">Notez l’absence de styles et d’éléments de menu.</span><span class="sxs-lookup"><span data-stu-id="78b55-411">Note the lack of styling and menu items.</span></span> <span data-ttu-id="78b55-412">Le style sera corrigé dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="78b55-412">The styling will be fixed in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="78b55-413">Contenu statique</span><span class="sxs-lookup"><span data-stu-id="78b55-413">Static content</span></span>

<span data-ttu-id="78b55-414">Dans ASP.NET MVC 5 et versions antérieures, le contenu statique était hébergé à partir de la racine du projet Web et a été mélangé aux fichiers côté serveur.</span><span class="sxs-lookup"><span data-stu-id="78b55-414">In ASP.NET MVC 5 and earlier, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="78b55-415">Dans ASP.NET Core, le contenu statique est hébergé dans le répertoire *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="78b55-415">In ASP.NET Core, static content is hosted in the *wwwroot* directory.</span></span> <span data-ttu-id="78b55-416">Copiez le contenu statique de l’application ASP.NET MVC dans le répertoire *wwwroot* dans le projet ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="78b55-416">Copy the static content from the ASP.NET MVC app to the *wwwroot* directory in the ASP.NET Core project.</span></span> <span data-ttu-id="78b55-417">Dans cet exemple de conversion :</span><span class="sxs-lookup"><span data-stu-id="78b55-417">In this sample conversion:</span></span>

* <span data-ttu-id="78b55-418">Copiez le fichier *favicon. ico* à partir du projet MVC ASP.net dans le répertoire *wwwroot* dans le projet ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="78b55-418">Copy the *favicon.ico* file from the ASP.NET MVC project to the *wwwroot* directory in the ASP.NET Core project.</span></span>

<span data-ttu-id="78b55-419">Le projet MVC ASP.NET utilise [bootstrap](https://getbootstrap.com/) pour son style et stocke les fichiers de démarrage dans les répertoires de *contenu* et de *scripts* .</span><span class="sxs-lookup"><span data-stu-id="78b55-419">The ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* directories.</span></span> <span data-ttu-id="78b55-420">Le modèle, qui a généré le projet MVC ASP.NET, référence le bootstrap dans le fichier de disposition ( *Views/Shared/_Layout. cshtml* ).</span><span class="sxs-lookup"><span data-stu-id="78b55-420">The template, which generated the ASP.NET MVC project, references Bootstrap in the layout file ( *Views/Shared/_Layout.cshtml* ).</span></span> <span data-ttu-id="78b55-421">Les fichiers *bootstrap.js* et *bootstrap. CSS* peuvent être copiés du projet MVC ASP.net vers le répertoire *wwwroot* dans le nouveau projet.</span><span class="sxs-lookup"><span data-stu-id="78b55-421">The *bootstrap.js* and *bootstrap.css* files could be copied from the ASP.NET MVC project to the *wwwroot* directory in the new project.</span></span> <span data-ttu-id="78b55-422">Au lieu de cela, ce document ajoute la prise en charge des démarrages (et d’autres bibliothèques côté client) à l’aide de CDN, dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="78b55-422">Instead, this document adds support for Bootstrap (and other client-side libraries) using CDNs, in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="78b55-423">Migrer le fichier de disposition</span><span class="sxs-lookup"><span data-stu-id="78b55-423">Migrate the layout file</span></span>

* <span data-ttu-id="78b55-424">Copiez le fichier *_ViewStart. cshtml* à partir du répertoire *views* du projet MVC ASP.net dans le répertoire *views* du projet ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="78b55-424">Copy the *_ViewStart.cshtml* file from the ASP.NET MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="78b55-425">Le fichier *_ViewStart. cshtml* n’a pas été modifié dans ASP.net Core Mvc.</span><span class="sxs-lookup"><span data-stu-id="78b55-425">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="78b55-426">Créez un répertoire *Views/Shared* .</span><span class="sxs-lookup"><span data-stu-id="78b55-426">Create a *Views/Shared* directory.</span></span>

* <span data-ttu-id="78b55-427">*Facultatif :* Copiez *_ViewImports. cshtml* du répertoire *views* du projet MVC *FullAspNetCore* dans le répertoire *views* du projet ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="78b55-427">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="78b55-428">Supprimez toute déclaration d’espace de noms dans le fichier *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="78b55-428">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="78b55-429">Le fichier *_ViewImports. cshtml* fournit des espaces de noms pour tous les fichiers de vue et insère les [balises](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="78b55-429">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="78b55-430">Les tag helpers sont utilisés dans le nouveau fichier de disposition.</span><span class="sxs-lookup"><span data-stu-id="78b55-430">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="78b55-431">Le fichier *_ViewImports. cshtml* est nouveau pour ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="78b55-431">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="78b55-432">Copiez le fichier *_Layout. cshtml* à partir du répertoire *Views/Shared* du projet MVC ASP.net dans le répertoire *Views/Shared* du projet ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="78b55-432">Copy the *_Layout.cshtml* file from the ASP.NET MVC project's *Views/Shared* directory into the ASP.NET Core project's *Views/Shared* directory.</span></span>

<span data-ttu-id="78b55-433">Ouvrez *_Layout fichier. cshtml* et apportez les modifications suivantes (le code complet est indiqué ci-dessous) :</span><span class="sxs-lookup"><span data-stu-id="78b55-433">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="78b55-434">Remplacez `@Styles.Render("~/Content/css")` par un `<link>` élément pour charger *bootstrap. CSS* (voir ci-dessous).</span><span class="sxs-lookup"><span data-stu-id="78b55-434">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="78b55-435">Supprimez `@Scripts.Render("~/bundles/modernizr")`.</span><span class="sxs-lookup"><span data-stu-id="78b55-435">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="78b55-436">Commentez la `@Html.Partial("_LoginPartial")` ligne (entourez la ligne de `@*...*@` ).</span><span class="sxs-lookup"><span data-stu-id="78b55-436">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="78b55-437">Pour plus d’informations, consultez [migrer l’authentification et :::no-loc(Identity)::: vers ASP.net Core](xref:migration/identity)</span><span class="sxs-lookup"><span data-stu-id="78b55-437">For more information, see [Migrate Authentication and :::no-loc(Identity)::: to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="78b55-438">Remplacez `@Scripts.Render("~/bundles/jquery")` par un `<script>` élément (voir ci-dessous).</span><span class="sxs-lookup"><span data-stu-id="78b55-438">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="78b55-439">Remplacez `@Scripts.Render("~/bundles/bootstrap")` par un `<script>` élément (voir ci-dessous).</span><span class="sxs-lookup"><span data-stu-id="78b55-439">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="78b55-440">Balisage de remplacement pour l’inclusion CSS de démarrage :</span><span class="sxs-lookup"><span data-stu-id="78b55-440">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="78b55-441">Balisage de remplacement pour l’inclusion de jQuery et du code d’amorçage JavaScript :</span><span class="sxs-lookup"><span data-stu-id="78b55-441">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="78b55-442">Le fichier *_Layout. cshtml* mis à jour est affiché ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="78b55-442">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="78b55-443">Affichez le site dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="78b55-443">View the site in the browser.</span></span> <span data-ttu-id="78b55-444">Elle doit maintenant se charger correctement, avec les styles attendus en place.</span><span class="sxs-lookup"><span data-stu-id="78b55-444">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="78b55-445">*Facultatif :* Essayez d’utiliser le nouveau fichier de disposition.</span><span class="sxs-lookup"><span data-stu-id="78b55-445">*Optional:* Try using the new layout file.</span></span> <span data-ttu-id="78b55-446">Copiez le fichier de disposition à partir du projet *FullAspNetCore* .</span><span class="sxs-lookup"><span data-stu-id="78b55-446">Copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="78b55-447">Le nouveau fichier de disposition utilise [tag Helper](xref:mvc/views/tag-helpers/intro) et présente d’autres améliorations.</span><span class="sxs-lookup"><span data-stu-id="78b55-447">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="78b55-448">Configurer le regroupement et la minimisation</span><span class="sxs-lookup"><span data-stu-id="78b55-448">Configure bundling and minification</span></span>

<span data-ttu-id="78b55-449">Pour plus d’informations sur la configuration du regroupement et de la minimisation, consultez [regroupement et minimisation](xref:client-side/bundling-and-minification).</span><span class="sxs-lookup"><span data-stu-id="78b55-449">For information about how to configure bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="78b55-450">Résoudre les erreurs HTTP 500</span><span class="sxs-lookup"><span data-stu-id="78b55-450">Solve HTTP 500 errors</span></span>

<span data-ttu-id="78b55-451">De nombreux problèmes peuvent provoquer des messages d’erreur HTTP 500 qui ne contiennent aucune information sur la source du problème.</span><span class="sxs-lookup"><span data-stu-id="78b55-451">There are many problems that can cause an HTTP 500 error messages that contain no information on the source of the problem.</span></span> <span data-ttu-id="78b55-452">Par exemple, si le fichier *views/_ViewImports. cshtml* contient un espace de noms qui n’existe pas dans le projet, une erreur HTTP 500 est générée.</span><span class="sxs-lookup"><span data-stu-id="78b55-452">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, a HTTP 500 error is generated.</span></span> <span data-ttu-id="78b55-453">Par défaut, dans ASP.NET Core Apps, l' `UseDeveloperExceptionPage` extension est ajoutée au `IApplicationBuilder` et exécutée lorsque la configuration est en cours de *développement* .</span><span class="sxs-lookup"><span data-stu-id="78b55-453">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development* .</span></span> <span data-ttu-id="78b55-454">Consultez un exemple dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="78b55-454">See an example in the following code:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

<span data-ttu-id="78b55-455">ASP.NET Core convertit les exceptions non gérées en réponses d’erreur HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="78b55-455">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="78b55-456">Normalement, les détails de l’erreur ne sont pas inclus dans ces réponses pour empêcher la divulgation d’informations potentiellement sensibles sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="78b55-456">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="78b55-457">Pour plus d’informations, consultez la [page exception du développeur](xref:fundamentals/error-handling#developer-exception-page).</span><span class="sxs-lookup"><span data-stu-id="78b55-457">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="78b55-458">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="78b55-458">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end
