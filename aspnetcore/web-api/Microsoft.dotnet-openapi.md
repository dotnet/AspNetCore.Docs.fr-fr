---
title: Développer des applications ASP.NET Core à l’aide de OpenAPI
author: ryanbrandenburg
description: Montre comment utiliser l’outil « Microsoft. dotnet-openapi » pour ajouter des références aux fichiers OpenAPI.
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
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
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: 5d9f1684aa333c38c73673138a703b04d318c6df
ms.sourcegitcommit: b64c44ba5e3abb4ad4d50de93b7e282bf0f251e4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/07/2021
ms.locfileid: "97972026"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a><span data-ttu-id="d8311-103">Développer des applications ASP.NET Core à l’aide des outils OpenAPI</span><span class="sxs-lookup"><span data-stu-id="d8311-103">Develop ASP.NET Core apps using OpenAPI tools</span></span>

<span data-ttu-id="d8311-104">Par Ryan Brandenburg</span><span class="sxs-lookup"><span data-stu-id="d8311-104">By Ryan Brandenburg</span></span>

<span data-ttu-id="d8311-105">[Microsoft. dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) est un [outil Global .net Core](/dotnet/core/tools/global-tools) pour la gestion des références [openapi](https://github.com/OAI/OpenAPI-Specification) dans un projet.</span><span class="sxs-lookup"><span data-stu-id="d8311-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) is a [.NET Core Global Tool](/dotnet/core/tools/global-tools) for managing [OpenAPI](https://github.com/OAI/OpenAPI-Specification) references within a project.</span></span>

## <a name="installation"></a><span data-ttu-id="d8311-106">Installation</span><span class="sxs-lookup"><span data-stu-id="d8311-106">Installation</span></span>

<span data-ttu-id="d8311-107">Pour installer `Microsoft.dotnet-openapi` , exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="d8311-107">To install `Microsoft.dotnet-openapi`, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a><span data-ttu-id="d8311-108">Ajouter</span><span class="sxs-lookup"><span data-stu-id="d8311-108">Add</span></span>

<span data-ttu-id="d8311-109">L’ajout d’une référence OpenAPI à l’aide de l’une des commandes de cette page ajoute un `<OpenApiReference />` élément semblable au suivant au fichier *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="d8311-109">Adding an OpenAPI reference using any of the commands on this page adds an `<OpenApiReference />` element similar to the following to the *.csproj* file:</span></span>

```xml
<OpenApiReference Include="openapi.json" />
```

<span data-ttu-id="d8311-110">La référence précédente est requise pour que l’application appelle le code client généré.</span><span class="sxs-lookup"><span data-stu-id="d8311-110">The preceding reference is required for the app to call the generated client code.</span></span>

<!-- TODO: Restore after https://github.com/dotnet/AspNetCore/issues/12738
### Add Project

#### Options

| Short option | Long option | Description | Example |
|-------|------|-------|---------|
| -p|--project | The project to operate on. |dotnet openapi add project *--project .\Ref.csproj* ../Ref/ProjRef.csproj |

#### Arguments

|  Argument  | Description | Example |
|-------------|-------------|---------|
| source-file | The source to create a reference from. Must be a project file. |dotnet openapi add project *../Ref/ProjRef.csproj* | -->

### <a name="add-file"></a><span data-ttu-id="d8311-111">Ajouter un fichier</span><span class="sxs-lookup"><span data-stu-id="d8311-111">Add File</span></span>

#### <a name="options"></a><span data-ttu-id="d8311-112">Options</span><span class="sxs-lookup"><span data-stu-id="d8311-112">Options</span></span>

| <span data-ttu-id="d8311-113">Option Short</span><span class="sxs-lookup"><span data-stu-id="d8311-113">Short option</span></span>| <span data-ttu-id="d8311-114">Option longue</span><span class="sxs-lookup"><span data-stu-id="d8311-114">Long option</span></span>| <span data-ttu-id="d8311-115">Description</span><span class="sxs-lookup"><span data-stu-id="d8311-115">Description</span></span> | <span data-ttu-id="d8311-116">Exemple</span><span class="sxs-lookup"><span data-stu-id="d8311-116">Example</span></span> |
|-------|------|-------|---------|
| <span data-ttu-id="d8311-117">-p</span><span class="sxs-lookup"><span data-stu-id="d8311-117">-p</span></span>|<span data-ttu-id="d8311-118">--updateProject</span><span class="sxs-lookup"><span data-stu-id="d8311-118">--updateProject</span></span> | <span data-ttu-id="d8311-119">Projet sur lequel effectuer l’opération.</span><span class="sxs-lookup"><span data-stu-id="d8311-119">The project to operate on.</span></span> |<span data-ttu-id="d8311-120">dotnet openapi ajouter *un fichier--updateProject .\Ref.csproj* .\OpenAPI.jssur</span><span class="sxs-lookup"><span data-stu-id="d8311-120">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="d8311-121">-c</span><span class="sxs-lookup"><span data-stu-id="d8311-121">-c</span></span>|<span data-ttu-id="d8311-122">--Code-Generator</span><span class="sxs-lookup"><span data-stu-id="d8311-122">--code-generator</span></span>| <span data-ttu-id="d8311-123">Générateur de code à appliquer à la référence.</span><span class="sxs-lookup"><span data-stu-id="d8311-123">The code generator to apply to the reference.</span></span> <span data-ttu-id="d8311-124">Les options sont `NSwagCSharp` et `NSwagTypeScript`.</span><span class="sxs-lookup"><span data-stu-id="d8311-124">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> <span data-ttu-id="d8311-125">Si `--code-generator` n’est pas spécifié, l’outil prend par défaut la valeur `NSwagCSharp` .</span><span class="sxs-lookup"><span data-stu-id="d8311-125">If `--code-generator` is not specified the tooling defaults to `NSwagCSharp`.</span></span>|<span data-ttu-id="d8311-126">.\OpenApi.jsdotnet openapi ajouter un fichier sur--Code-Generator</span><span class="sxs-lookup"><span data-stu-id="d8311-126">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="d8311-127">-H</span><span class="sxs-lookup"><span data-stu-id="d8311-127">-h</span></span>|<span data-ttu-id="d8311-128">--help</span><span class="sxs-lookup"><span data-stu-id="d8311-128">--help</span></span>|<span data-ttu-id="d8311-129">Afficher les informations d’aide</span><span class="sxs-lookup"><span data-stu-id="d8311-129">Show help information</span></span>|<span data-ttu-id="d8311-130">Ajouter un fichier Dotnet openapi--Help</span><span class="sxs-lookup"><span data-stu-id="d8311-130">dotnet openapi add file --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="d8311-131">Arguments</span><span class="sxs-lookup"><span data-stu-id="d8311-131">Arguments</span></span>

|  <span data-ttu-id="d8311-132">Argument</span><span class="sxs-lookup"><span data-stu-id="d8311-132">Argument</span></span>  | <span data-ttu-id="d8311-133">Description</span><span class="sxs-lookup"><span data-stu-id="d8311-133">Description</span></span> | <span data-ttu-id="d8311-134">Exemple</span><span class="sxs-lookup"><span data-stu-id="d8311-134">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="d8311-135">fichier source</span><span class="sxs-lookup"><span data-stu-id="d8311-135">source-file</span></span> | <span data-ttu-id="d8311-136">Source à partir de laquelle créer une référence.</span><span class="sxs-lookup"><span data-stu-id="d8311-136">The source to create a reference from.</span></span> <span data-ttu-id="d8311-137">Doit être un fichier OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="d8311-137">Must be an OpenAPI file.</span></span> |<span data-ttu-id="d8311-138">*.\OpenAPI.js* dotnet openapi ajouter un fichier</span><span class="sxs-lookup"><span data-stu-id="d8311-138">dotnet openapi add file *.\OpenAPI.json*</span></span> |

### <a name="add-url"></a><span data-ttu-id="d8311-139">Ajouter une URL</span><span class="sxs-lookup"><span data-stu-id="d8311-139">Add URL</span></span>

#### <a name="options"></a><span data-ttu-id="d8311-140">Options</span><span class="sxs-lookup"><span data-stu-id="d8311-140">Options</span></span>

| <span data-ttu-id="d8311-141">Option Short</span><span class="sxs-lookup"><span data-stu-id="d8311-141">Short option</span></span>| <span data-ttu-id="d8311-142">Option longue</span><span class="sxs-lookup"><span data-stu-id="d8311-142">Long option</span></span>| <span data-ttu-id="d8311-143">Description</span><span class="sxs-lookup"><span data-stu-id="d8311-143">Description</span></span> | <span data-ttu-id="d8311-144">Exemple</span><span class="sxs-lookup"><span data-stu-id="d8311-144">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="d8311-145">-p</span><span class="sxs-lookup"><span data-stu-id="d8311-145">-p</span></span>|<span data-ttu-id="d8311-146">--updateProject</span><span class="sxs-lookup"><span data-stu-id="d8311-146">--updateProject</span></span> | <span data-ttu-id="d8311-147">Projet sur lequel effectuer l’opération.</span><span class="sxs-lookup"><span data-stu-id="d8311-147">The project to operate on.</span></span> |<span data-ttu-id="d8311-148">Ajouter une URL dotnet openapi *--updateProject .\Ref.csproj*`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="d8311-148">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="d8311-149">-o</span><span class="sxs-lookup"><span data-stu-id="d8311-149">-o</span></span>|<span data-ttu-id="d8311-150">--fichier de sortie</span><span class="sxs-lookup"><span data-stu-id="d8311-150">--output-file</span></span> | <span data-ttu-id="d8311-151">Où placer la copie locale du fichier OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="d8311-151">Where to place the local copy of the OpenAPI file.</span></span> |<span data-ttu-id="d8311-152">dotnet openapi Add URL `https://contoso.com/openapi.json` *--output-file myclient.json*</span><span class="sxs-lookup"><span data-stu-id="d8311-152">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span></span> |
| <span data-ttu-id="d8311-153">-c</span><span class="sxs-lookup"><span data-stu-id="d8311-153">-c</span></span>|<span data-ttu-id="d8311-154">--Code-Generator</span><span class="sxs-lookup"><span data-stu-id="d8311-154">--code-generator</span></span>| <span data-ttu-id="d8311-155">Générateur de code à appliquer à la référence.</span><span class="sxs-lookup"><span data-stu-id="d8311-155">The code generator to apply to the reference.</span></span> <span data-ttu-id="d8311-156">Les options sont `NSwagCSharp` et `NSwagTypeScript`.</span><span class="sxs-lookup"><span data-stu-id="d8311-156">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> |<span data-ttu-id="d8311-157">Ajouter une URL dotnet openapi `https://contoso.com/openapi.json` --Code-Generator</span><span class="sxs-lookup"><span data-stu-id="d8311-157">dotnet openapi add url `https://contoso.com/openapi.json` --code-generator</span></span>
| <span data-ttu-id="d8311-158">-H</span><span class="sxs-lookup"><span data-stu-id="d8311-158">-h</span></span>|<span data-ttu-id="d8311-159">--help</span><span class="sxs-lookup"><span data-stu-id="d8311-159">--help</span></span>|<span data-ttu-id="d8311-160">Afficher les informations d’aide</span><span class="sxs-lookup"><span data-stu-id="d8311-160">Show help information</span></span>|<span data-ttu-id="d8311-161">Ajouter une URL dotnet openapi--Help</span><span class="sxs-lookup"><span data-stu-id="d8311-161">dotnet openapi add url --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="d8311-162">Arguments</span><span class="sxs-lookup"><span data-stu-id="d8311-162">Arguments</span></span>

|  <span data-ttu-id="d8311-163">Argument</span><span class="sxs-lookup"><span data-stu-id="d8311-163">Argument</span></span>  | <span data-ttu-id="d8311-164">Description</span><span class="sxs-lookup"><span data-stu-id="d8311-164">Description</span></span> | <span data-ttu-id="d8311-165">Exemple</span><span class="sxs-lookup"><span data-stu-id="d8311-165">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="d8311-166">URL source</span><span class="sxs-lookup"><span data-stu-id="d8311-166">source-URL</span></span> | <span data-ttu-id="d8311-167">Source à partir de laquelle créer une référence.</span><span class="sxs-lookup"><span data-stu-id="d8311-167">The source to create a reference from.</span></span> <span data-ttu-id="d8311-168">Doit être une URL.</span><span class="sxs-lookup"><span data-stu-id="d8311-168">Must be a URL.</span></span> |<span data-ttu-id="d8311-169">URL d’ajout de dotnet openapi `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="d8311-169">dotnet openapi add url `https://contoso.com/openapi.json`</span></span> |

## <a name="remove"></a><span data-ttu-id="d8311-170">Remove</span><span class="sxs-lookup"><span data-stu-id="d8311-170">Remove</span></span>

<span data-ttu-id="d8311-171">Supprime la référence OpenAPI correspondant au nom de fichier donné du fichier *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="d8311-171">Removes the OpenAPI reference matching the given filename from the *.csproj* file.</span></span> <span data-ttu-id="d8311-172">Lorsque la référence OpenAPI est supprimée, les clients ne sont pas générés.</span><span class="sxs-lookup"><span data-stu-id="d8311-172">When the OpenAPI reference is removed, clients won't be generated.</span></span> <span data-ttu-id="d8311-173">Les fichiers local *. JSON* et *. YAML* sont supprimés.</span><span class="sxs-lookup"><span data-stu-id="d8311-173">Local *.json* and *.yaml* files are deleted.</span></span>

### <a name="options"></a><span data-ttu-id="d8311-174">Options</span><span class="sxs-lookup"><span data-stu-id="d8311-174">Options</span></span>

| <span data-ttu-id="d8311-175">Option Short</span><span class="sxs-lookup"><span data-stu-id="d8311-175">Short option</span></span>| <span data-ttu-id="d8311-176">Option longue</span><span class="sxs-lookup"><span data-stu-id="d8311-176">Long option</span></span>| <span data-ttu-id="d8311-177">Description</span><span class="sxs-lookup"><span data-stu-id="d8311-177">Description</span></span>| <span data-ttu-id="d8311-178">Exemple</span><span class="sxs-lookup"><span data-stu-id="d8311-178">Example</span></span> |
|-------|------|------------|---------|
| <span data-ttu-id="d8311-179">-p</span><span class="sxs-lookup"><span data-stu-id="d8311-179">-p</span></span>|<span data-ttu-id="d8311-180">--updateProject</span><span class="sxs-lookup"><span data-stu-id="d8311-180">--updateProject</span></span> | <span data-ttu-id="d8311-181">Projet sur lequel effectuer l’opération.</span><span class="sxs-lookup"><span data-stu-id="d8311-181">The project to operate on.</span></span> |<span data-ttu-id="d8311-182">dotnet openapi Remove *--updateProject .\Ref.csproj* .\OpenAPI.jssur</span><span class="sxs-lookup"><span data-stu-id="d8311-182">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="d8311-183">-H</span><span class="sxs-lookup"><span data-stu-id="d8311-183">-h</span></span>|<span data-ttu-id="d8311-184">--help</span><span class="sxs-lookup"><span data-stu-id="d8311-184">--help</span></span>|<span data-ttu-id="d8311-185">Afficher les informations d’aide</span><span class="sxs-lookup"><span data-stu-id="d8311-185">Show help information</span></span>|<span data-ttu-id="d8311-186">openapi de la suppression dotnet--Help</span><span class="sxs-lookup"><span data-stu-id="d8311-186">dotnet openapi remove --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="d8311-187">Arguments</span><span class="sxs-lookup"><span data-stu-id="d8311-187">Arguments</span></span>

|  <span data-ttu-id="d8311-188">Argument</span><span class="sxs-lookup"><span data-stu-id="d8311-188">Argument</span></span>  | <span data-ttu-id="d8311-189">Description</span><span class="sxs-lookup"><span data-stu-id="d8311-189">Description</span></span>| <span data-ttu-id="d8311-190">Exemple</span><span class="sxs-lookup"><span data-stu-id="d8311-190">Example</span></span> |
| ------------|------------|---------|
| <span data-ttu-id="d8311-191">fichier source</span><span class="sxs-lookup"><span data-stu-id="d8311-191">source-file</span></span> | <span data-ttu-id="d8311-192">Source à laquelle supprimer la référence.</span><span class="sxs-lookup"><span data-stu-id="d8311-192">The source to remove the reference to.</span></span> |<span data-ttu-id="d8311-193">dotnet openapi supprimer *.\OpenAPI.jssur*</span><span class="sxs-lookup"><span data-stu-id="d8311-193">dotnet openapi remove *.\OpenAPI.json*</span></span> |

## <a name="refresh"></a><span data-ttu-id="d8311-194">Actualiser</span><span class="sxs-lookup"><span data-stu-id="d8311-194">Refresh</span></span>

<span data-ttu-id="d8311-195">Actualise la version locale d’un fichier qui a été téléchargé à l’aide du contenu le plus récent à partir de l’URL de téléchargement.</span><span class="sxs-lookup"><span data-stu-id="d8311-195">Refreshes the local version of a file that was downloaded using the latest content from the download URL.</span></span>

### <a name="options"></a><span data-ttu-id="d8311-196">Options</span><span class="sxs-lookup"><span data-stu-id="d8311-196">Options</span></span>

| <span data-ttu-id="d8311-197">Option Short</span><span class="sxs-lookup"><span data-stu-id="d8311-197">Short option</span></span>| <span data-ttu-id="d8311-198">Option longue</span><span class="sxs-lookup"><span data-stu-id="d8311-198">Long option</span></span>| <span data-ttu-id="d8311-199">Description</span><span class="sxs-lookup"><span data-stu-id="d8311-199">Description</span></span> | <span data-ttu-id="d8311-200">Exemple</span><span class="sxs-lookup"><span data-stu-id="d8311-200">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="d8311-201">-p</span><span class="sxs-lookup"><span data-stu-id="d8311-201">-p</span></span>|<span data-ttu-id="d8311-202">--updateProject</span><span class="sxs-lookup"><span data-stu-id="d8311-202">--updateProject</span></span> | <span data-ttu-id="d8311-203">Projet sur lequel effectuer l’opération.</span><span class="sxs-lookup"><span data-stu-id="d8311-203">The project to operate on.</span></span> | <span data-ttu-id="d8311-204">actualisation de dotnet openapi *--updateProject .\Ref.csproj*`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="d8311-204">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="d8311-205">-H</span><span class="sxs-lookup"><span data-stu-id="d8311-205">-h</span></span>|<span data-ttu-id="d8311-206">--help</span><span class="sxs-lookup"><span data-stu-id="d8311-206">--help</span></span>|<span data-ttu-id="d8311-207">Afficher les informations d’aide</span><span class="sxs-lookup"><span data-stu-id="d8311-207">Show help information</span></span>|<span data-ttu-id="d8311-208">actualisation de dotnet openapi--aide</span><span class="sxs-lookup"><span data-stu-id="d8311-208">dotnet openapi refresh --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="d8311-209">Arguments</span><span class="sxs-lookup"><span data-stu-id="d8311-209">Arguments</span></span>

|  <span data-ttu-id="d8311-210">Argument</span><span class="sxs-lookup"><span data-stu-id="d8311-210">Argument</span></span>  | <span data-ttu-id="d8311-211">Description</span><span class="sxs-lookup"><span data-stu-id="d8311-211">Description</span></span> | <span data-ttu-id="d8311-212">Exemple</span><span class="sxs-lookup"><span data-stu-id="d8311-212">Example</span></span> |
| ------------|-------------|---------|
| <span data-ttu-id="d8311-213">URL source</span><span class="sxs-lookup"><span data-stu-id="d8311-213">source-URL</span></span> | <span data-ttu-id="d8311-214">URL à partir de laquelle actualiser la référence.</span><span class="sxs-lookup"><span data-stu-id="d8311-214">The URL to refresh the reference from.</span></span> | <span data-ttu-id="d8311-215">actualisation de dotnet openapi `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="d8311-215">dotnet openapi refresh `https://contoso.com/openapi.json`</span></span> |
