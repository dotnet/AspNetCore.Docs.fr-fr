---
title: Utiliser les analyseurs d’API web
author: pranavkm
description: En savoir plus sur le package d’analyseur d’API Web ASP.NET Core MVC.
monikerRange: '>= aspnetcore-2.2'
ms.author: prkrishn
ms.custom: mvc
ms.date: 09/05/2019
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
uid: web-api/advanced/analyzers
ms.openlocfilehash: cf0415e7d72e21a48db8bbeb4540f05e0b0a4198
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057919"
---
# <a name="use-web-api-analyzers"></a><span data-ttu-id="33105-103">Utiliser les analyseurs d’API web</span><span class="sxs-lookup"><span data-stu-id="33105-103">Use web API analyzers</span></span>

<span data-ttu-id="33105-104">ASP.NET Core 2,2 et versions ultérieures fournissent un package d’analyseurs MVC destiné à être utilisé avec des projets d’API Web.</span><span class="sxs-lookup"><span data-stu-id="33105-104">ASP.NET Core 2.2 and later provides an MVC analyzers package intended for use with web API projects.</span></span> <span data-ttu-id="33105-105">Les analyseurs fonctionnent avec les contrôleurs annotés avec <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> , tout en s’appuyant sur les [conventions d’API Web](xref:web-api/advanced/conventions).</span><span class="sxs-lookup"><span data-stu-id="33105-105">The analyzers work with controllers annotated with <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>, while building on [web API conventions](xref:web-api/advanced/conventions).</span></span>

<span data-ttu-id="33105-106">Le package des analyseurs vous avertit des actions du contrôleur qui :</span><span class="sxs-lookup"><span data-stu-id="33105-106">The analyzers package notifies you of any controller action that:</span></span>

* <span data-ttu-id="33105-107">Retourne un code d’État non déclaré.</span><span class="sxs-lookup"><span data-stu-id="33105-107">Returns an undeclared status code.</span></span>
* <span data-ttu-id="33105-108">Retourne un résultat de réussite non déclaré.</span><span class="sxs-lookup"><span data-stu-id="33105-108">Returns an undeclared success result.</span></span>
* <span data-ttu-id="33105-109">Documente un code d’État qui n’est pas retourné.</span><span class="sxs-lookup"><span data-stu-id="33105-109">Documents a status code that isn't returned.</span></span>
* <span data-ttu-id="33105-110">Comprend un contrôle de validation de modèle explicite.</span><span class="sxs-lookup"><span data-stu-id="33105-110">Includes an explicit model validation check.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="reference-the-analyzer-package"></a><span data-ttu-id="33105-111">Référencer le package de l’analyseur</span><span class="sxs-lookup"><span data-stu-id="33105-111">Reference the analyzer package</span></span>

<span data-ttu-id="33105-112">Dans ASP.NET Core 3,0 ou version ultérieure, les analyseurs sont inclus dans le kit SDK .NET Core.</span><span class="sxs-lookup"><span data-stu-id="33105-112">In ASP.NET Core 3.0 or later, the analyzers are included in the .NET Core SDK.</span></span> <span data-ttu-id="33105-113">Pour activer l’analyseur dans votre projet, incluez la `IncludeOpenAPIAnalyzers` propriété dans le fichier projet :</span><span class="sxs-lookup"><span data-stu-id="33105-113">To enable the analyzer in your project, include the `IncludeOpenAPIAnalyzers` property in the project file:</span></span>

```xml
<PropertyGroup>
 <IncludeOpenAPIAnalyzers>true</IncludeOpenAPIAnalyzers>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

## <a name="package-installation"></a><span data-ttu-id="33105-114">Installation de package</span><span class="sxs-lookup"><span data-stu-id="33105-114">Package installation</span></span>

<span data-ttu-id="33105-115">Installez le package NuGet [Microsoft. AspNetCore. Mvc. API. Analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers) avec l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="33105-115">Install the [Microsoft.AspNetCore.Mvc.Api.Analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers) NuGet package with one of the following approaches:</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="33105-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="33105-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="33105-117">À partir de la fenêtre **Console du Gestionnaire de package** :</span><span class="sxs-lookup"><span data-stu-id="33105-117">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="33105-118">Accédez à **Affichage** > **Autres fenêtres** > **Console du Gestionnaire de package** .</span><span class="sxs-lookup"><span data-stu-id="33105-118">Go to **View** > **Other Windows** > **Package Manager Console** .</span></span>
  * <span data-ttu-id="33105-119">Accédez au répertoire où se trouve le fichier *ApiConventions.csproj* .</span><span class="sxs-lookup"><span data-stu-id="33105-119">Navigate to the directory in which the *ApiConventions.csproj* file exists.</span></span>
  * <span data-ttu-id="33105-120">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="33105-120">Execute the following command:</span></span>

    ```powershell
    Install-Package Microsoft.AspNetCore.Mvc.Api.Analyzers
    ```

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="33105-121">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="33105-121">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="33105-122">Cliquez avec le bouton droit sur le dossier *packages* dans **panneau solutions** > **Ajouter des packages...** .</span><span class="sxs-lookup"><span data-stu-id="33105-122">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...** .</span></span>
* <span data-ttu-id="33105-123">Définissez la liste déroulante **source** de la fenêtre **Ajouter des Packages** sur « NuGet.org ».</span><span class="sxs-lookup"><span data-stu-id="33105-123">Set the **Add Packages** window's **Source** drop-down to "nuget.org".</span></span>
* <span data-ttu-id="33105-124">Entrez « Microsoft.AspNetCore.Mvc.Api.Analyzers » dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="33105-124">Enter "Microsoft.AspNetCore.Mvc.Api.Analyzers" in the search box.</span></span>
* <span data-ttu-id="33105-125">Sélectionnez le package « Microsoft.AspNetCore.Mvc.Api.Analyzers » dans le volet de résultats, puis cliquez sur **Ajouter un package** .</span><span class="sxs-lookup"><span data-stu-id="33105-125">Select the "Microsoft.AspNetCore.Mvc.Api.Analyzers" package from the results pane and click **Add Package** .</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="33105-126">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="33105-126">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="33105-127">Exécutez la commande suivante à partir du **Terminal intégré** :</span><span class="sxs-lookup"><span data-stu-id="33105-127">Run the following command from the **Integrated Terminal** :</span></span>

```dotnetcli
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

### <a name="net-core-cli"></a>[<span data-ttu-id="33105-128">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="33105-128">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="33105-129">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="33105-129">Run the following command:</span></span>

```dotnetcli
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

---

::: moniker-end

## <a name="analyzers-for-web-api-conventions"></a><span data-ttu-id="33105-130">Analyseurs pour les conventions d’API Web</span><span class="sxs-lookup"><span data-stu-id="33105-130">Analyzers for web API conventions</span></span>

<span data-ttu-id="33105-131">Les documents sur les API ouvertes contiennent les codes d’état et les types de réponse qu’une action peut retourner.</span><span class="sxs-lookup"><span data-stu-id="33105-131">OpenAPI documents contain status codes and response types that an action may return.</span></span> <span data-ttu-id="33105-132">Dans ASP.NET Core MVC, des attributs comme <xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> et <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute> sont utilisés pour documenter une action.</span><span class="sxs-lookup"><span data-stu-id="33105-132">In ASP.NET Core MVC, attributes such as <xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> and <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute> are used to document an action.</span></span> <span data-ttu-id="33105-133"><xref:tutorials/web-api-help-pages-using-swagger> Pour plus d’informations sur la documentation de votre API Web.</span><span class="sxs-lookup"><span data-stu-id="33105-133"><xref:tutorials/web-api-help-pages-using-swagger> goes into further detail on documenting your web API.</span></span>

<span data-ttu-id="33105-134">Un des analyseurs du package inspecte les contrôleurs annotés avec <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> et identifie les actions qui ne document pas entièrement leurs réponses.</span><span class="sxs-lookup"><span data-stu-id="33105-134">One of the analyzers in the package inspects controllers annotated with <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> and identifies actions that don't entirely document their responses.</span></span> <span data-ttu-id="33105-135">Prenons l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="33105-135">Consider the following example:</span></span>

[!code-csharp[](conventions/sample/Controllers/ContactsController.cs?name=missing404docs&highlight=10)]

<span data-ttu-id="33105-136">L’action précédente documente le type de retour avec réussite HTTP 200, mais ne documente pas le code d’état d’échec HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="33105-136">The preceding action documents the HTTP 200 success return type but doesn't document the HTTP 404 failure status code.</span></span> <span data-ttu-id="33105-137">L’analyseur signale la documentation manquante pour le code d’état HTTP 404 sous la forme d’un avertissement.</span><span class="sxs-lookup"><span data-stu-id="33105-137">The analyzer reports the missing documentation for the HTTP 404 status code as a warning.</span></span> <span data-ttu-id="33105-138">Une option pour résoudre le problème est fournie.</span><span class="sxs-lookup"><span data-stu-id="33105-138">An option to fix the problem is provided.</span></span>

![analyseur rapportant un avertissement](conventions/_static/Analyzer.gif)

## <a name="additional-resources"></a><span data-ttu-id="33105-140">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="33105-140">Additional resources</span></span>

* <xref:web-api/advanced/conventions>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:web-api/index>
