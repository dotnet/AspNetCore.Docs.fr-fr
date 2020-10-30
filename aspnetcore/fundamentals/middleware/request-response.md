---
title: Opérations de demande et de réponse dans ASP.NET Core
author: jkotalik
description: Découvrez comment lire le corps de demande et écrire le corps de la réponse dans ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jukotali
ms.custom: mvc
ms.date: 5/29/2019
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
uid: fundamentals/middleware/request-response
ms.openlocfilehash: cc701343cb3859f0f76ebc62bd54aa2e4431d522
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061026"
---
# <a name="request-and-response-operations-in-aspnet-core"></a><span data-ttu-id="4228f-103">Opérations de demande et de réponse dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4228f-103">Request and response operations in ASP.NET Core</span></span>

<span data-ttu-id="4228f-104">Par [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="4228f-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="4228f-105">Cet article explique comment lire à partir du corps de la demande et écrire dans le corps de la réponse.</span><span class="sxs-lookup"><span data-stu-id="4228f-105">This article explains how to read from the request body and write to the response body.</span></span> <span data-ttu-id="4228f-106">Le code de ces opérations peut être nécessaire lors de l’écriture d’un intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="4228f-106">Code for these operations might be required when writing middleware.</span></span> <span data-ttu-id="4228f-107">En dehors de l’intergiciel d’écriture, le code personnalisé n’est généralement pas obligatoire, car les opérations sont gérées par MVC et les :::no-loc(Razor)::: pages.</span><span class="sxs-lookup"><span data-stu-id="4228f-107">Outside of writing middleware, custom code isn't generally required because the operations are handled by MVC and :::no-loc(Razor)::: Pages.</span></span>

<span data-ttu-id="4228f-108">Il existe deux abstractions pour les corps de demande et de réponse : <xref:System.IO.Stream> et <xref:System.IO.Pipelines.Pipe> .</span><span class="sxs-lookup"><span data-stu-id="4228f-108">There are two abstractions for the request and response bodies: <xref:System.IO.Stream> and <xref:System.IO.Pipelines.Pipe>.</span></span> <span data-ttu-id="4228f-109">Pour la lecture de <xref:Microsoft.AspNetCore.Http.HttpRequest.Body?displayProperty=nameWithType> la demande, est un <xref:System.IO.Stream> , et `HttpRequest.BodyReader` est un <xref:System.IO.Pipelines.PipeReader> .</span><span class="sxs-lookup"><span data-stu-id="4228f-109">For request reading, <xref:Microsoft.AspNetCore.Http.HttpRequest.Body?displayProperty=nameWithType> is a <xref:System.IO.Stream>, and `HttpRequest.BodyReader` is a <xref:System.IO.Pipelines.PipeReader>.</span></span> <span data-ttu-id="4228f-110">Pour l’écriture de réponse, <xref:Microsoft.AspNetCore.Http.HttpResponse.Body?displayProperty=nameWithType> est un <xref:System.IO.Stream> , et `HttpResponse.BodyWriter` est un <xref:System.IO.Pipelines.PipeWriter> .</span><span class="sxs-lookup"><span data-stu-id="4228f-110">For response writing, <xref:Microsoft.AspNetCore.Http.HttpResponse.Body?displayProperty=nameWithType> is a <xref:System.IO.Stream>, and `HttpResponse.BodyWriter` is a <xref:System.IO.Pipelines.PipeWriter>.</span></span>

<span data-ttu-id="4228f-111">Les [pipelines](/dotnet/standard/io/pipelines) sont recommandés par rapport aux flux.</span><span class="sxs-lookup"><span data-stu-id="4228f-111">[Pipelines](/dotnet/standard/io/pipelines) are recommended over streams.</span></span> <span data-ttu-id="4228f-112">Les flux peuvent être plus faciles à utiliser pour des opérations simples, mais les pipelines présentent un avantage de performances et sont plus faciles à utiliser dans la plupart des scénarios.</span><span class="sxs-lookup"><span data-stu-id="4228f-112">Streams can be easier to use for some simple operations, but pipelines have a performance advantage and are easier to use in most scenarios.</span></span> <span data-ttu-id="4228f-113">ASP.NET Core commence à utiliser des pipelines plutôt que des flux en interne.</span><span class="sxs-lookup"><span data-stu-id="4228f-113">ASP.NET Core is starting to use pipelines instead of streams internally.</span></span> <span data-ttu-id="4228f-114">Voici quelques exemples :</span><span class="sxs-lookup"><span data-stu-id="4228f-114">Examples include:</span></span>

* `FormReader`
* `TextReader`
* `TextWriter`
* `HttpResponse.WriteAsync`

<span data-ttu-id="4228f-115">Les flux ne sont pas supprimés de l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="4228f-115">Streams aren't being removed from the framework.</span></span> <span data-ttu-id="4228f-116">Les flux continuent à être utilisés dans .NET, et de nombreux types de flux n’ont pas d’équivalents de canal, tels que `FileStreams` et `ResponseCompression` .</span><span class="sxs-lookup"><span data-stu-id="4228f-116">Streams continue to be used throughout .NET, and many stream types don't have pipe equivalents, such as `FileStreams` and `ResponseCompression`.</span></span>

## <a name="stream-examples"></a><span data-ttu-id="4228f-117">Exemples de flux</span><span class="sxs-lookup"><span data-stu-id="4228f-117">Stream examples</span></span>

<span data-ttu-id="4228f-118">Supposons que l’objectif est de créer un intergiciel qui lit l’intégralité du corps de la requête sous la forme d’une liste de chaînes, en la fractionnant sur de nouvelles lignes.</span><span class="sxs-lookup"><span data-stu-id="4228f-118">Suppose the goal is to create a middleware that reads the entire request body as a list of strings, splitting on new lines.</span></span> <span data-ttu-id="4228f-119">Une implémentation simple de flux peut se présenter comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="4228f-119">A simple stream implementation might look like the following example:</span></span>

> [!WARNING]
> <span data-ttu-id="4228f-120">Le code suivant :</span><span class="sxs-lookup"><span data-stu-id="4228f-120">The following code:</span></span>
> * <span data-ttu-id="4228f-121">Permet d’illustrer les problèmes liés à l’absence d’utilisation d’un canal pour lire le corps de la requête.</span><span class="sxs-lookup"><span data-stu-id="4228f-121">Is used to demonstrate the problems with not using a pipe to read the request body.</span></span>
> * <span data-ttu-id="4228f-122">N’est pas destiné à être utilisé dans les applications de production.</span><span class="sxs-lookup"><span data-stu-id="4228f-122">Is not intended to be used in production apps.</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="4228f-123">Ce code fonctionne, mais il existe certains problèmes :</span><span class="sxs-lookup"><span data-stu-id="4228f-123">This code works, but there are some issues:</span></span>

* <span data-ttu-id="4228f-124">Avant d’ajouter à `StringBuilder`, l’exemple crée une autre chaîne (`encodedString`) qui est immédiatement rejetée.</span><span class="sxs-lookup"><span data-stu-id="4228f-124">Before appending to the `StringBuilder`, the example creates another string (`encodedString`) that is thrown away immediately.</span></span> <span data-ttu-id="4228f-125">Ce processus se produit pour tous les octets dans le flux, il en résulte une allocation de mémoire supplémentaire de la taille de la totalité du corps de la demande.</span><span class="sxs-lookup"><span data-stu-id="4228f-125">This process occurs for all bytes in the stream, so the result is extra memory allocation the size of the entire request body.</span></span>
* <span data-ttu-id="4228f-126">L’exemple lit la chaîne entière avant de fractionner sur les nouvelles lignes.</span><span class="sxs-lookup"><span data-stu-id="4228f-126">The example reads the entire string before splitting on new lines.</span></span> <span data-ttu-id="4228f-127">Il est plus efficace de rechercher de nouvelles lignes dans le tableau d’octets.</span><span class="sxs-lookup"><span data-stu-id="4228f-127">It's more efficient to check for new lines in the byte array.</span></span>

<span data-ttu-id="4228f-128">Voici un exemple qui résout certains des problèmes précédents :</span><span class="sxs-lookup"><span data-stu-id="4228f-128">Here's an example that fixes some of the preceding issues:</span></span>

> [!WARNING]
> <span data-ttu-id="4228f-129">Le code suivant :</span><span class="sxs-lookup"><span data-stu-id="4228f-129">The following code:</span></span>
> * <span data-ttu-id="4228f-130">Est utilisé pour illustrer les solutions à certains problèmes dans le code précédent, tout en ne résolvant aucun problème.</span><span class="sxs-lookup"><span data-stu-id="4228f-130">Is used to demonstrate the solutions to some problems in the preceding code while not solving all the problems.</span></span>
> * <span data-ttu-id="4228f-131">N’est pas destiné à être utilisé dans les applications de production.</span><span class="sxs-lookup"><span data-stu-id="4228f-131">Is not intended to be used in production apps.</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

<span data-ttu-id="4228f-132">L’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="4228f-132">This preceding example:</span></span>

* <span data-ttu-id="4228f-133">Ne met pas en mémoire tampon le corps entier de la demande dans un `StringBuilder` , sauf s’il n’y a pas de caractère de nouvelle ligne.</span><span class="sxs-lookup"><span data-stu-id="4228f-133">Doesn't buffer the entire request body in a `StringBuilder` unless there aren't any newline characters.</span></span>
* <span data-ttu-id="4228f-134">N’appelle pas `Split` sur la chaîne.</span><span class="sxs-lookup"><span data-stu-id="4228f-134">Doesn't call `Split` on the string.</span></span>

<span data-ttu-id="4228f-135">Toutefois, il existe toujours quelques problèmes :</span><span class="sxs-lookup"><span data-stu-id="4228f-135">However, there are still are a few issues:</span></span>

* <span data-ttu-id="4228f-136">Si les caractères de saut de ligne sont éparpillés, la majeure partie du corps de la demande est mise en mémoire tampon dans la chaîne.</span><span class="sxs-lookup"><span data-stu-id="4228f-136">If newline characters are sparse, much of the request body is buffered in the string.</span></span>
* <span data-ttu-id="4228f-137">Le code continue à créer des chaînes ( `remainingString` ) et les ajoute à la mémoire tampon de chaîne, ce qui entraîne une allocation supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="4228f-137">The code continues to create strings (`remainingString`) and adds them to the string buffer, which results in an extra allocation.</span></span>

<span data-ttu-id="4228f-138">Ces problèmes sont réparables, mais le code devient progressivement plus complexe avec une amélioration minime.</span><span class="sxs-lookup"><span data-stu-id="4228f-138">These issues are fixable, but the code is becoming progressively more complicated with little improvement.</span></span> <span data-ttu-id="4228f-139">Les pipelines permettent de résoudre ces problèmes avec un code peu compliqué.</span><span class="sxs-lookup"><span data-stu-id="4228f-139">Pipelines provide a way to solve these problems with minimal code complexity.</span></span>

## <a name="pipelines"></a><span data-ttu-id="4228f-140">Pipelines</span><span class="sxs-lookup"><span data-stu-id="4228f-140">Pipelines</span></span>

<span data-ttu-id="4228f-141">L’exemple suivant montre comment le même scénario peut être géré à l’aide d’un [PipeReader](/dotnet/standard/io/pipelines#pipe):</span><span class="sxs-lookup"><span data-stu-id="4228f-141">The following example shows how the same scenario can be handled using a [PipeReader](/dotnet/standard/io/pipelines#pipe):</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

<span data-ttu-id="4228f-142">Cet exemple résout de nombreux problèmes trouvés dans les implémentations de flux :</span><span class="sxs-lookup"><span data-stu-id="4228f-142">This example fixes many issues that the streams implementations had:</span></span>

* <span data-ttu-id="4228f-143">Il n’est pas nécessaire de disposer d’une mémoire tampon de chaîne, car le `PipeReader` gère les octets qui n’ont pas été utilisés.</span><span class="sxs-lookup"><span data-stu-id="4228f-143">There's no need for a string buffer because the `PipeReader` handles bytes that haven't been used.</span></span>
* <span data-ttu-id="4228f-144">Les chaînes codées sont ajoutées directement à la liste des chaînes retournées.</span><span class="sxs-lookup"><span data-stu-id="4228f-144">Encoded strings are directly added to the list of returned strings.</span></span>
* <span data-ttu-id="4228f-145">En dehors de l' `ToArray` appel, et de la mémoire utilisée par la chaîne, la création de chaînes est une allocation libre.</span><span class="sxs-lookup"><span data-stu-id="4228f-145">Other than the `ToArray` call, and the memory used by the string, string creation is allocation free.</span></span>

## <a name="adapters"></a><span data-ttu-id="4228f-146">Adaptateurs</span><span class="sxs-lookup"><span data-stu-id="4228f-146">Adapters</span></span>

<span data-ttu-id="4228f-147">Les `Body` `BodyReader` Propriétés, et `BodyWriter` sont disponibles pour `HttpRequest` et `HttpResponse` .</span><span class="sxs-lookup"><span data-stu-id="4228f-147">The `Body`, `BodyReader`, and `BodyWriter` properties are available for `HttpRequest` and `HttpResponse`.</span></span> <span data-ttu-id="4228f-148">Lorsque vous définissez `Body` sur un autre flux, un nouvel ensemble d’adaptateurs adapte automatiquement chaque type à l’autre.</span><span class="sxs-lookup"><span data-stu-id="4228f-148">When you set `Body` to a different stream, a new set of adapters automatically adapt each type to the other.</span></span> <span data-ttu-id="4228f-149">Si vous définissez `HttpRequest.Body` sur un nouveau flux, `HttpRequest.BodyReader` est automatiquement défini sur un nouveau `PipeReader` qui encapsule `HttpRequest.Body` .</span><span class="sxs-lookup"><span data-stu-id="4228f-149">If you set `HttpRequest.Body` to a new stream, `HttpRequest.BodyReader` is automatically set to a new `PipeReader` that wraps `HttpRequest.Body`.</span></span>

## <a name="startasync"></a><span data-ttu-id="4228f-150">StartAsync</span><span class="sxs-lookup"><span data-stu-id="4228f-150">StartAsync</span></span>

<span data-ttu-id="4228f-151">`HttpResponse.StartAsync` est utilisé pour indiquer que les en-têtes ne sont pas modifiables et pour exécuter des `OnStarting` rappels.</span><span class="sxs-lookup"><span data-stu-id="4228f-151">`HttpResponse.StartAsync` is used to indicate that headers are unmodifiable and to run `OnStarting` callbacks.</span></span> <span data-ttu-id="4228f-152">Lors de l’utilisation de Kestrel en tant que serveur, l’appel de `StartAsync` avant d’utiliser la `PipeReader` garantie que la mémoire retournée par `GetMemory` appartient au interne de Kestrel <xref:System.IO.Pipelines.Pipe> plutôt qu’à une mémoire tampon externe.</span><span class="sxs-lookup"><span data-stu-id="4228f-152">When using Kestrel as a server, calling `StartAsync` before using the `PipeReader` guarantees that memory returned by `GetMemory` belongs to Kestrel's internal <xref:System.IO.Pipelines.Pipe> rather than an external buffer.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4228f-153">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="4228f-153">Additional resources</span></span>

* [<span data-ttu-id="4228f-154">System. IO. pipelines dans .NET</span><span class="sxs-lookup"><span data-stu-id="4228f-154">System.IO.Pipelines in .NET</span></span>](/dotnet/standard/io/pipelines)
* <xref:fundamentals/middleware/write>

<!-- Test with Postman or other tool. See image in static directory. -->
