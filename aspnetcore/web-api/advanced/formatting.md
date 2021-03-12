---
title: Mettre en forme les données des réponses dans l’API web ASP.NET Core
author: rick-anderson
description: Découvrez comment mettre en forme les données des réponses dans l’API web ASP.NET Core.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 1/28/2021
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
uid: web-api/advanced/formatting
ms.openlocfilehash: e3e176072032494f94f120efabb95d6c127fb5e6
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587812"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="eb7b2-103">Mettre en forme les données des réponses dans l’API web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="eb7b2-103">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="eb7b2-104">De [Rick Anderson](https://twitter.com/RickAndMSFT) et [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="eb7b2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="eb7b2-105">ASP.NET Core MVC prend en charge la mise en forme des données de réponse.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-105">ASP.NET Core MVC has support for formatting response data.</span></span> <span data-ttu-id="eb7b2-106">Les données de réponse peuvent être formatées à l’aide de formats spécifiques ou en réponse au format demandé par le client.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-106">Response data can be formatted using specific formats or in response to client requested format.</span></span>

<span data-ttu-id="eb7b2-107">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/advanced/formatting) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="eb7b2-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/advanced/formatting) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="eb7b2-108">Résultats des actions spécifiques au format</span><span class="sxs-lookup"><span data-stu-id="eb7b2-108">Format-specific Action Results</span></span>

<span data-ttu-id="eb7b2-109">Certains types de résultats d’action sont spécifiques à un format particulier, comme <xref:Microsoft.AspNetCore.Mvc.JsonResult> et <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-109">Some action result types are specific to a particular format, such as <xref:Microsoft.AspNetCore.Mvc.JsonResult> and <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span></span> <span data-ttu-id="eb7b2-110">Les actions peuvent retourner des résultats mis en forme dans un format particulier, indépendamment des préférences du client.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-110">Actions can return results that are formatted in a particular format, regardless of client preferences.</span></span> <span data-ttu-id="eb7b2-111">Par exemple, le retour de `JsonResult` retourne des données au format JSON.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-111">For example, returning `JsonResult` returns JSON-formatted data.</span></span> <span data-ttu-id="eb7b2-112">Le retour de `ContentResult` ou une chaîne retourne des données de chaîne au format texte brut.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-112">Returning `ContentResult` or a string returns plain-text-formatted string data.</span></span>

<span data-ttu-id="eb7b2-113">Une action n’est pas requise pour retourner un type spécifique.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-113">An action isn't required to return any specific type.</span></span> <span data-ttu-id="eb7b2-114">ASP.NET Core prend en charge toute valeur de retour d’objet.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-114">ASP.NET Core supports any object return value.</span></span>  <span data-ttu-id="eb7b2-115">Les résultats des actions qui retournent des objets qui ne sont pas des <xref:Microsoft.AspNetCore.Mvc.IActionResult> types sont sérialisés à l’aide de l' <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implémentation appropriée.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-115">Results from actions that return objects that are not <xref:Microsoft.AspNetCore.Mvc.IActionResult> types are serialized using the appropriate <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementation.</span></span> <span data-ttu-id="eb7b2-116">Pour plus d’informations, consultez <xref:web-api/action-return-types>.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-116">For more information, see <xref:web-api/action-return-types>.</span></span>

<span data-ttu-id="eb7b2-117">La méthode d’assistance intégrée retourne des <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> données au format JSON : [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span><span class="sxs-lookup"><span data-stu-id="eb7b2-117">The built-in helper method <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> returns JSON-formatted data: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span></span>

<span data-ttu-id="eb7b2-118">L’exemple de téléchargement retourne la liste des auteurs.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-118">The sample download returns the list of authors.</span></span> <span data-ttu-id="eb7b2-119">À l’aide des outils de développement du navigateur F12 ou du [poster](https://www.getpostman.com/tools) avec le code précédent :</span><span class="sxs-lookup"><span data-stu-id="eb7b2-119">Using the F12 browser developer tools or [Postman](https://www.getpostman.com/tools) with the previous code:</span></span>

* <span data-ttu-id="eb7b2-120">L’en-tête de réponse contenant **Content-type :** `application/json; charset=utf-8` est affiché.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-120">The response header containing **content-type:** `application/json; charset=utf-8` is displayed.</span></span>
* <span data-ttu-id="eb7b2-121">Les en-têtes de demande s’affichent.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-121">The request headers are displayed.</span></span> <span data-ttu-id="eb7b2-122">Par exemple, l' `Accept` en-tête.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-122">For example, the `Accept` header.</span></span> <span data-ttu-id="eb7b2-123">L' `Accept` en-tête est ignoré par le code précédent.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-123">The `Accept` header is ignored by the preceding code.</span></span>

<span data-ttu-id="eb7b2-124">Pour retourner des données mises en forme en texte brut, utilisez <xref:Microsoft.AspNetCore.Mvc.ContentResult> et le helper <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Content%2A> :</span><span class="sxs-lookup"><span data-stu-id="eb7b2-124">To return plain text formatted data, use <xref:Microsoft.AspNetCore.Mvc.ContentResult> and the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Content%2A> helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

<span data-ttu-id="eb7b2-125">Dans le code précédent, le `Content-Type` retourné est `text/plain` .</span><span class="sxs-lookup"><span data-stu-id="eb7b2-125">In the preceding code, the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="eb7b2-126">Retour d’une chaîne `Content-Type` `text/plain` :</span><span class="sxs-lookup"><span data-stu-id="eb7b2-126">Returning a string delivers `Content-Type` of `text/plain`:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

<span data-ttu-id="eb7b2-127">Pour les actions avec plusieurs types de retour, retournez `IActionResult` .</span><span class="sxs-lookup"><span data-stu-id="eb7b2-127">For actions with multiple return types, return `IActionResult`.</span></span> <span data-ttu-id="eb7b2-128">Par exemple, le retour de codes d’état HTTP différents en fonction du résultat des opérations effectuées.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-128">For example, returning different HTTP status codes based on the result of operations performed.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="eb7b2-129">Négociation de contenu</span><span class="sxs-lookup"><span data-stu-id="eb7b2-129">Content negotiation</span></span>

<span data-ttu-id="eb7b2-130">La négociation de contenu se produit lorsque le client spécifie un [en-tête Accept](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span><span class="sxs-lookup"><span data-stu-id="eb7b2-130">Content negotiation occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="eb7b2-131">Le format par défaut utilisé par ASP.NET Core est [JSON](https://json.org/).</span><span class="sxs-lookup"><span data-stu-id="eb7b2-131">The default format used by ASP.NET Core is [JSON](https://json.org/).</span></span> <span data-ttu-id="eb7b2-132">La négociation de contenu est :</span><span class="sxs-lookup"><span data-stu-id="eb7b2-132">Content negotiation is:</span></span>

* <span data-ttu-id="eb7b2-133">Implémenté par <xref:Microsoft.AspNetCore.Mvc.ObjectResult> .</span><span class="sxs-lookup"><span data-stu-id="eb7b2-133">Implemented by <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span></span>
* <span data-ttu-id="eb7b2-134">Intégré aux résultats d’action spécifiques au code d’État retournés par les méthodes d’assistance.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-134">Built into the status code-specific action results returned from the helper methods.</span></span> <span data-ttu-id="eb7b2-135">Les méthodes d’assistance des résultats d’action sont basées sur `ObjectResult` .</span><span class="sxs-lookup"><span data-stu-id="eb7b2-135">The action results helper methods are based on `ObjectResult`.</span></span>

<span data-ttu-id="eb7b2-136">Quand un type de modèle est retourné, le type de retour est `ObjectResult` .</span><span class="sxs-lookup"><span data-stu-id="eb7b2-136">When a model type is returned,  the return type is `ObjectResult`.</span></span>

<span data-ttu-id="eb7b2-137">La méthode d’action suivante utilise les méthodes helper `Ok` et `NotFound` :</span><span class="sxs-lookup"><span data-stu-id="eb7b2-137">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

<span data-ttu-id="eb7b2-138">Par défaut, ASP.NET Core prend en charge `application/json` `text/json` les `text/plain` types de média, et.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-138">By default, ASP.NET Core supports `application/json`, `text/json`, and `text/plain` media types.</span></span> <span data-ttu-id="eb7b2-139">Les outils tels que [Fiddler](https://www.telerik.com/fiddler) ou [postal](https://www.getpostman.com/tools) peuvent définir l' `Accept` en-tête de demande pour spécifier le format de retour.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-139">Tools such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/tools) can set the `Accept` request header to specify the return format.</span></span> <span data-ttu-id="eb7b2-140">Lorsque l' `Accept` en-tête contient un type pris en charge par le serveur, ce type est retourné.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-140">When the `Accept` header contains a type the server supports, that type is returned.</span></span> <span data-ttu-id="eb7b2-141">La section suivante montre comment ajouter des formateurs supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-141">The next section shows how to add additional formatters.</span></span>

<span data-ttu-id="eb7b2-142">Les actions de contrôleur peuvent retourner des POCO (Plain Old CLR Objects).</span><span class="sxs-lookup"><span data-stu-id="eb7b2-142">Controller actions can return POCOs (Plain Old CLR Objects).</span></span> <span data-ttu-id="eb7b2-143">Lorsqu’un POCO est retourné, le runtime crée automatiquement un `ObjectResult` objet qui encapsule l’objet.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-143">When a POCO is returned, the runtime automatically creates an `ObjectResult` that wraps the object.</span></span> <span data-ttu-id="eb7b2-144">Le client obtient l’objet sérialisé mis en forme.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-144">The client gets the formatted serialized object.</span></span> <span data-ttu-id="eb7b2-145">Si l’objet retourné est `null` , une `204 No Content` réponse est retournée.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-145">If the object being returned is `null`, a `204 No Content` response is returned.</span></span>

<span data-ttu-id="eb7b2-146">Retour d’un type d’objet :</span><span class="sxs-lookup"><span data-stu-id="eb7b2-146">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

<span data-ttu-id="eb7b2-147">Dans le code précédent, une demande d’alias d’auteur valide retourne une `200 OK` réponse avec les données de l’auteur.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-147">In the preceding code, a request for a valid author alias returns a `200 OK` response with the author's data.</span></span> <span data-ttu-id="eb7b2-148">Une demande d’alias non valide retourne une `204 No Content` réponse.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-148">A request for an invalid alias returns a `204 No Content` response.</span></span>

### <a name="the-accept-header"></a><span data-ttu-id="eb7b2-149">En-tête Accept</span><span class="sxs-lookup"><span data-stu-id="eb7b2-149">The Accept header</span></span>

<span data-ttu-id="eb7b2-150">La *négociation* de contenu a lieu lorsqu’un `Accept` en-tête apparaît dans la demande.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-150">Content *negotiation* takes place when an `Accept` header appears in the request.</span></span> <span data-ttu-id="eb7b2-151">Quand une demande contient un en-tête Accept, ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="eb7b2-151">When a request contains an accept header, ASP.NET Core:</span></span>

* <span data-ttu-id="eb7b2-152">Énumère les types de médias dans l’en-tête Accept dans l’ordre de préférence.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-152">Enumerates the media types in the accept header in preference order.</span></span>
* <span data-ttu-id="eb7b2-153">Tente de trouver un formateur capable de produire une réponse dans l’un des formats spécifiés.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-153">Tries to find a formatter that can produce a response in one of the formats specified.</span></span>

<span data-ttu-id="eb7b2-154">Si aucun formateur trouvé pouvant satisfaire la demande du client, ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="eb7b2-154">If no formatter is found that can satisfy the client's request, ASP.NET Core:</span></span>

* <span data-ttu-id="eb7b2-155">Retourne `406 Not Acceptable` si <xref:Microsoft.AspNetCore.Mvc.MvcOptions.ReturnHttpNotAcceptable?displayProperty=nameWithType> a la valeur `true` , ou-</span><span class="sxs-lookup"><span data-stu-id="eb7b2-155">Returns `406 Not Acceptable` if <xref:Microsoft.AspNetCore.Mvc.MvcOptions.ReturnHttpNotAcceptable?displayProperty=nameWithType> is set to `true`, or -</span></span>
* <span data-ttu-id="eb7b2-156">Tente de trouver le premier formateur qui peut produire une réponse.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-156">Tries to find the first formatter that can produce a response.</span></span>

<span data-ttu-id="eb7b2-157">Si aucun formateur n’est configuré pour le format demandé, le premier formateur qui peut mettre en forme l’objet est utilisé.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-157">If no formatter is configured for the requested format, the first formatter that can format the object is used.</span></span> <span data-ttu-id="eb7b2-158">Si aucun `Accept` en-tête n’apparaît dans la requête :</span><span class="sxs-lookup"><span data-stu-id="eb7b2-158">If no `Accept` header appears in the request:</span></span>

* <span data-ttu-id="eb7b2-159">Le premier formateur qui peut gérer l’objet est utilisé pour sérialiser la réponse.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-159">The first formatter that can handle the object is used to serialize the response.</span></span>
* <span data-ttu-id="eb7b2-160">Aucune négociation n’a lieu.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-160">There isn't any negotiation taking place.</span></span> <span data-ttu-id="eb7b2-161">Le serveur détermine le format à retourner.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-161">The server is determining what format to return.</span></span>

<span data-ttu-id="eb7b2-162">Si l’en-tête Accept contient `*/*` , l’en-tête est ignoré, sauf si a la valeur `RespectBrowserAcceptHeader` true on <xref:Microsoft.AspNetCore.Mvc.MvcOptions> .</span><span class="sxs-lookup"><span data-stu-id="eb7b2-162">If the Accept header contains `*/*`, the Header is ignored unless `RespectBrowserAcceptHeader` is set to true on <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="eb7b2-163">Navigateurs et négociation de contenu</span><span class="sxs-lookup"><span data-stu-id="eb7b2-163">Browsers and content negotiation</span></span>

<span data-ttu-id="eb7b2-164">Contrairement aux clients d’API typiques, les navigateurs Web fournissent des `Accept` en-têtes.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-164">Unlike typical API clients, web browsers supply `Accept` headers.</span></span> <span data-ttu-id="eb7b2-165">Le navigateur Web spécifie de nombreux formats, y compris des caractères génériques.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-165">Web browser specify many formats, including wildcards.</span></span> <span data-ttu-id="eb7b2-166">Par défaut, lorsque le Framework détecte que la demande provient d’un navigateur :</span><span class="sxs-lookup"><span data-stu-id="eb7b2-166">By default, when the framework detects that the request is coming from a browser:</span></span>

* <span data-ttu-id="eb7b2-167">L' `Accept` en-tête est ignoré.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-167">The `Accept` header is ignored.</span></span>
* <span data-ttu-id="eb7b2-168">Le contenu est renvoyé au format JSON, sauf s’il a été configuré autrement.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-168">The content is returned in JSON, unless otherwise configured.</span></span>

<span data-ttu-id="eb7b2-169">Cela offre une expérience plus cohérente entre les navigateurs lors de l’utilisation des API.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-169">This provides a more consistent experience across browsers when consuming APIs.</span></span>

<span data-ttu-id="eb7b2-170">Pour configurer une application pour honorer les en-têtes Accept du navigateur, définissez <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> sur `true` :</span><span class="sxs-lookup"><span data-stu-id="eb7b2-170">To configure an app to honor browser accept headers, set <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> to `true`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a><span data-ttu-id="eb7b2-171">Configurer les formateurs</span><span class="sxs-lookup"><span data-stu-id="eb7b2-171">Configure formatters</span></span>

<span data-ttu-id="eb7b2-172">Les applications qui doivent prendre en charge des formats supplémentaires peuvent ajouter les packages NuGet appropriés et configurer la prise en charge.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-172">Apps that need to support additional formats can add the appropriate NuGet packages and configure support.</span></span> <span data-ttu-id="eb7b2-173">Il existe des formateurs distincts pour les entrées et pour les sorties.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-173">There are separate formatters for input and output.</span></span> <span data-ttu-id="eb7b2-174">Les formateurs d’entrée sont utilisés par la [liaison de modèle](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="eb7b2-174">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="eb7b2-175">Les formateurs de sortie sont utilisés pour mettre en forme les réponses.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-175">Output formatters are used to format responses.</span></span> <span data-ttu-id="eb7b2-176">Pour plus d’informations sur la création d’un formateur personnalisé, consultez [formateurs personnalisés](xref:web-api/advanced/custom-formatters).</span><span class="sxs-lookup"><span data-stu-id="eb7b2-176">For information on creating a custom formatter, see [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a><span data-ttu-id="eb7b2-177">Ajouter la prise en charge du format XML</span><span class="sxs-lookup"><span data-stu-id="eb7b2-177">Add XML format support</span></span>

<span data-ttu-id="eb7b2-178">Les formateurs XML implémentés à l’aide <xref:System.Xml.Serialization.XmlSerializer> de sont configurés en appelant <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> :</span><span class="sxs-lookup"><span data-stu-id="eb7b2-178">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

<span data-ttu-id="eb7b2-179">Le code précédent sérialise les résultats à l’aide de `XmlSerializer` .</span><span class="sxs-lookup"><span data-stu-id="eb7b2-179">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="eb7b2-180">Lorsque vous utilisez le code précédent, les méthodes de contrôleur retournent le format approprié en fonction de l' `Accept` en-tête de la demande.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-180">When using the preceding code, controller methods return the appropriate format based on the request's `Accept` header.</span></span>

### <a name="configure-systemtextjson-based-formatters"></a><span data-ttu-id="eb7b2-181">Configurer des `System.Text.Json` formateurs basés sur</span><span class="sxs-lookup"><span data-stu-id="eb7b2-181">Configure `System.Text.Json` based formatters</span></span>

<span data-ttu-id="eb7b2-182">Les fonctionnalités des `System.Text.Json` formateurs basés sur peuvent être configurées à l’aide de <xref:Microsoft.AspNetCore.Mvc.JsonOptions.JsonSerializerOptions?displayProperty=fullName> .</span><span class="sxs-lookup"><span data-stu-id="eb7b2-182">Features for the `System.Text.Json` based formatters can be configured using <xref:Microsoft.AspNetCore.Mvc.JsonOptions.JsonSerializerOptions?displayProperty=fullName>.</span></span> <span data-ttu-id="eb7b2-183">La mise en forme par défaut est la casse mixte.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-183">The default formatting is camelCase.</span></span> <span data-ttu-id="eb7b2-184">Le code en surbrillance suivant définit la mise en forme casse Pascal :</span><span class="sxs-lookup"><span data-stu-id="eb7b2-184">The following highlighted code sets PascalCase formatting:</span></span>

[!code-csharp[](./formatting/5.0samples/WebAPI5PascalCase/Startup.cs?name=snippet&highlight=4-5)]

<span data-ttu-id="eb7b2-185">La méthode d’action suivante appelle [ControllerBase. Problem](xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A) pour créer une <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> réponse :</span><span class="sxs-lookup"><span data-stu-id="eb7b2-185">The following action method calls [ControllerBase.Problem](xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A) to create a <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> response:</span></span>

[!code-csharp[](formatting/5.0samples/WebAPI5PascalCase/Controllers/WeatherForecastController.cs?name=snippet&highlight=4)]

<span data-ttu-id="eb7b2-186">Avec le code précédent :</span><span class="sxs-lookup"><span data-stu-id="eb7b2-186">With the preceding code:</span></span>

  * <span data-ttu-id="eb7b2-187">`https://localhost:5001/WeatherForecast/temperature` retourne casse Pascal.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-187">`https://localhost:5001/WeatherForecast/temperature` returns PascalCase.</span></span>
  * <span data-ttu-id="eb7b2-188">`https://localhost:5001/WeatherForecast/error` retourne la casse mixte.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-188">`https://localhost:5001/WeatherForecast/error` returns camelCase.</span></span> <span data-ttu-id="eb7b2-189">La réponse d’erreur est toujours la casse mixte, même lorsque l’application définit le format sur casse Pascal.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-189">The error response is always camelCase, even when the app sets the format to PascalCase.</span></span> <span data-ttu-id="eb7b2-190">`ProblemDetails` suit [RFC 7807](https://tools.ietf.org/html/rfc7807#appendix-A), qui spécifie en minuscules</span><span class="sxs-lookup"><span data-stu-id="eb7b2-190">`ProblemDetails` follows [RFC 7807](https://tools.ietf.org/html/rfc7807#appendix-A), which specifies lower case</span></span>

<span data-ttu-id="eb7b2-191">Le code suivant définit casse Pascal et ajoute un convertisseur personnalisé :</span><span class="sxs-lookup"><span data-stu-id="eb7b2-191">The following code sets PascalCase and adds a custom converter:</span></span>

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="eb7b2-192">Les options de sérialisation de sortie, en fonction de l’action, peuvent être configurées à l’aide de `JsonResult` .</span><span class="sxs-lookup"><span data-stu-id="eb7b2-192">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="eb7b2-193">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="eb7b2-193">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a><span data-ttu-id="eb7b2-194">Ajouter la prise en charge du format JSON basé sur Newtonsoft.Json</span><span class="sxs-lookup"><span data-stu-id="eb7b2-194">Add Newtonsoft.Json-based JSON format support</span></span>

<span data-ttu-id="eb7b2-195">Avant ASP.NET Core 3,0, les formateurs JSON utilisés par défaut ont été implémentés à l’aide du `Newtonsoft.Json` Package.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-195">Prior to ASP.NET Core 3.0, the default used JSON formatters implemented using the `Newtonsoft.Json` package.</span></span> <span data-ttu-id="eb7b2-196">Dans ASP.NET Core 3.0 ou version ultérieure, les formateurs JSON par défaut sont basés sur `System.Text.Json`.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-196">In ASP.NET Core 3.0 or later, the default JSON formatters are based on `System.Text.Json`.</span></span> <span data-ttu-id="eb7b2-197">La prise en charge des `Newtonsoft.Json` formateurs et des fonctionnalités de base est disponible en installant le [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) package NuGet et en le configurant dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="eb7b2-197">Support for `Newtonsoft.Json` based formatters and features is available by installing the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package and configuring it in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

<span data-ttu-id="eb7b2-198">Dans le code précédent, l’appel à `AddNewtonsoftJson` configure les fonctionnalités suivantes de l’API Web, du MVC et des Razor pages à utiliser `Newtonsoft.Json` :</span><span class="sxs-lookup"><span data-stu-id="eb7b2-198">In the preceding code, the call to `AddNewtonsoftJson` configures the following Web API, MVC, and Razor Pages features to use `Newtonsoft.Json`:</span></span>

* <span data-ttu-id="eb7b2-199">Formateurs d’entrée et de sortie qui lisent et écrivent du code JSON</span><span class="sxs-lookup"><span data-stu-id="eb7b2-199">Input and output formatters that read and write JSON</span></span>
* <xref:Microsoft.AspNetCore.Mvc.JsonResult>
* [<span data-ttu-id="eb7b2-200">JSON Patch</span><span class="sxs-lookup"><span data-stu-id="eb7b2-200">JSON Patch</span></span>](xref:web-api/jsonpatch)
* <xref:Microsoft.AspNetCore.Mvc.Rendering.IJsonHelper>
* [<span data-ttu-id="eb7b2-201">TempData</span><span class="sxs-lookup"><span data-stu-id="eb7b2-201">TempData</span></span>](xref:fundamentals/app-state#tempdata)

<span data-ttu-id="eb7b2-202">Certaines fonctionnalités peuvent ne pas fonctionner correctement avec `System.Text.Json` les formateurs basés sur et nécessitent une référence aux `Newtonsoft.Json` formateurs basés sur.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-202">Some features may not work well with `System.Text.Json`-based formatters and require a reference to the `Newtonsoft.Json`-based formatters.</span></span> <span data-ttu-id="eb7b2-203">Continuer à utiliser les `Newtonsoft.Json` formateurs basés sur, si l’application :</span><span class="sxs-lookup"><span data-stu-id="eb7b2-203">Continue using the `Newtonsoft.Json`-based formatters if the app:</span></span>

* <span data-ttu-id="eb7b2-204">Utilise des `Newtonsoft.Json` attributs.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-204">Uses `Newtonsoft.Json` attributes.</span></span> <span data-ttu-id="eb7b2-205">Par exemple, `[JsonProperty]` ou `[JsonIgnore]`.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-205">For example, `[JsonProperty]` or `[JsonIgnore]`.</span></span>
* <span data-ttu-id="eb7b2-206">Personnalise les paramètres de sérialisation.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-206">Customizes the serialization settings.</span></span>
* <span data-ttu-id="eb7b2-207">S’appuie sur les fonctionnalités `Newtonsoft.Json` fournies par.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-207">Relies on features that `Newtonsoft.Json` provides.</span></span>
* <span data-ttu-id="eb7b2-208">Configure `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-208">Configures `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span></span> <span data-ttu-id="eb7b2-209">Avant ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepte une instance de `JsonSerializerSettings` spécifique à `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-209">Prior to ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepts an instance of `JsonSerializerSettings` that is specific to `Newtonsoft.Json`.</span></span>
* <span data-ttu-id="eb7b2-210">Génère une documentation [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>).</span><span class="sxs-lookup"><span data-stu-id="eb7b2-210">Generates [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) documentation.</span></span>

<span data-ttu-id="eb7b2-211">Les fonctionnalités des `Newtonsoft.Json` formateurs basés sur peuvent être configurées à l’aide de `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings` :</span><span class="sxs-lookup"><span data-stu-id="eb7b2-211">Features for the `Newtonsoft.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span></span>

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="eb7b2-212">Les options de sérialisation de sortie, en fonction de l’action, peuvent être configurées à l’aide de `JsonResult` .</span><span class="sxs-lookup"><span data-stu-id="eb7b2-212">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="eb7b2-213">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="eb7b2-213">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

### <a name="add-xml-format-support"></a><span data-ttu-id="eb7b2-214">Ajouter la prise en charge du format XML</span><span class="sxs-lookup"><span data-stu-id="eb7b2-214">Add XML format support</span></span>

<span data-ttu-id="eb7b2-215">La mise en forme XML requiert le package NuGet [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) .</span><span class="sxs-lookup"><span data-stu-id="eb7b2-215">XML formatting requires the [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet package.</span></span>

<span data-ttu-id="eb7b2-216">Les formateurs XML implémentés à l’aide <xref:System.Xml.Serialization.XmlSerializer> de sont configurés en appelant <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> :</span><span class="sxs-lookup"><span data-stu-id="eb7b2-216">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

<span data-ttu-id="eb7b2-217">Le code précédent sérialise les résultats à l’aide de `XmlSerializer` .</span><span class="sxs-lookup"><span data-stu-id="eb7b2-217">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="eb7b2-218">Lorsque vous utilisez le code précédent, les méthodes de contrôleur doivent retourner le format approprié en fonction de l' `Accept` en-tête de la requête.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-218">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

::: moniker-end

### <a name="specify-a-format"></a><span data-ttu-id="eb7b2-219">Spécifier un format</span><span class="sxs-lookup"><span data-stu-id="eb7b2-219">Specify a format</span></span>

<span data-ttu-id="eb7b2-220">Pour restreindre les formats de réponse, appliquez le [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtre.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-220">To restrict the response formats, apply the [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter.</span></span> <span data-ttu-id="eb7b2-221">Comme la plupart des [filtres](xref:mvc/controllers/filters), `[Produces]` peut être appliqué au niveau de l’action, du contrôleur ou de l’étendue globale :</span><span class="sxs-lookup"><span data-stu-id="eb7b2-221">Like most [Filters](xref:mvc/controllers/filters), `[Produces]` can be applied at the action, controller, or global scope:</span></span>

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

<span data-ttu-id="eb7b2-222">Le [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtre précédent :</span><span class="sxs-lookup"><span data-stu-id="eb7b2-222">The preceding [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter:</span></span>

* <span data-ttu-id="eb7b2-223">Force toutes les actions du contrôleur à retourner des réponses au format JSON.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-223">Forces all actions within the controller to return JSON-formatted responses.</span></span>
* <span data-ttu-id="eb7b2-224">Si d’autres formateurs sont configurés et que le client spécifie un format différent, JSON est retourné.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-224">If other formatters are configured and the client specifies a different format, JSON is returned.</span></span>

<span data-ttu-id="eb7b2-225">Pour plus d’informations, consultez [filtres](xref:mvc/controllers/filters).</span><span class="sxs-lookup"><span data-stu-id="eb7b2-225">For more information, see [Filters](xref:mvc/controllers/filters).</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="eb7b2-226">Formateurs de cas spéciaux</span><span class="sxs-lookup"><span data-stu-id="eb7b2-226">Special case formatters</span></span>

<span data-ttu-id="eb7b2-227">Certains cas spéciaux sont implémentés avec des formateurs intégrés.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-227">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="eb7b2-228">Par défaut, `string` les types de retour sont mis en forme en tant que *texte/plain* (*texte/html* si demandé via l' `Accept` en-tête).</span><span class="sxs-lookup"><span data-stu-id="eb7b2-228">By default, `string` return types are formatted as *text/plain* (*text/html* if requested via the `Accept` header).</span></span> <span data-ttu-id="eb7b2-229">Ce comportement peut être supprimé en supprimant le <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter> .</span><span class="sxs-lookup"><span data-stu-id="eb7b2-229">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>.</span></span> <span data-ttu-id="eb7b2-230">Les formateurs sont supprimés de la `ConfigureServices` méthode.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-230">Formatters are removed in the `ConfigureServices` method.</span></span> <span data-ttu-id="eb7b2-231">Les actions qui ont un type de retour d’objet de modèle retournent `204 No Content` lors du retour de `null` .</span><span class="sxs-lookup"><span data-stu-id="eb7b2-231">Actions that have a model object return type return `204 No Content` when returning `null`.</span></span> <span data-ttu-id="eb7b2-232">Ce comportement peut être supprimé en supprimant le <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter> .</span><span class="sxs-lookup"><span data-stu-id="eb7b2-232">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span></span> <span data-ttu-id="eb7b2-233">Le code suivant supprime `StringOutputFormatter` et `HttpNoContentOutputFormatter`.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-233">The following code removes the `StringOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

<span data-ttu-id="eb7b2-234">Sans le `StringOutputFormatter` , le formateur JSON intégré met en forme les `string` types de retour.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-234">Without the `StringOutputFormatter`, the built-in JSON formatter formats `string` return types.</span></span> <span data-ttu-id="eb7b2-235">Si le formateur JSON intégré est supprimé et qu’un formateur XML est disponible, le formateur XML met en forme les `string` types de retour.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-235">If the built-in JSON formatter is removed and an XML formatter is available, the XML formatter formats `string` return types.</span></span> <span data-ttu-id="eb7b2-236">Sinon, les `string` types de retour retournent `406 Not Acceptable` .</span><span class="sxs-lookup"><span data-stu-id="eb7b2-236">Otherwise, `string` return types return `406 Not Acceptable`.</span></span>

<span data-ttu-id="eb7b2-237">Sans `HttpNoContentOutputFormatter`, les objets null sont mis en forme avec le formateur configuré.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-237">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="eb7b2-238">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="eb7b2-238">For example:</span></span>

* <span data-ttu-id="eb7b2-239">Le formateur JSON retourne une réponse avec un corps de `null` .</span><span class="sxs-lookup"><span data-stu-id="eb7b2-239">The JSON formatter returns a response with a body of `null`.</span></span>
* <span data-ttu-id="eb7b2-240">Le formateur XML retourne un élément XML vide avec l’ensemble d’attributs `xsi:nil="true"` .</span><span class="sxs-lookup"><span data-stu-id="eb7b2-240">The XML formatter returns an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="eb7b2-241">Mappages d’URL de format de réponse</span><span class="sxs-lookup"><span data-stu-id="eb7b2-241">Response format URL mappings</span></span>

<span data-ttu-id="eb7b2-242">Les clients peuvent demander un format particulier dans le cadre de l’URL, par exemple :</span><span class="sxs-lookup"><span data-stu-id="eb7b2-242">Clients can request a particular format as part of the URL, for example:</span></span>

* <span data-ttu-id="eb7b2-243">Dans la chaîne de requête ou dans une partie du chemin d’accès.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-243">In the query string or part of the path.</span></span>
* <span data-ttu-id="eb7b2-244">En utilisant une extension de fichier spécifique au format, par exemple. XML ou. JSON.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-244">By using a format-specific file extension such as .xml or .json.</span></span>

<span data-ttu-id="eb7b2-245">Le mappage du chemin de la requête doit être spécifié dans la route utilisée par l’API.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-245">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="eb7b2-246">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="eb7b2-246">For example:</span></span>

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="eb7b2-247">L’itinéraire précédent permet de spécifier le format demandé en tant qu’extension de fichier facultative.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-247">The preceding route allows the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="eb7b2-248">L' [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribut vérifie l’existence de la valeur de format dans le `RouteData` et mappe le format de réponse au formateur approprié lorsque la réponse est créée.</span><span class="sxs-lookup"><span data-stu-id="eb7b2-248">The [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribute checks for the existence of the format value in the `RouteData` and maps the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="eb7b2-249">Routage</span><span class="sxs-lookup"><span data-stu-id="eb7b2-249">Route</span></span>        |             <span data-ttu-id="eb7b2-250">Formateur</span><span class="sxs-lookup"><span data-stu-id="eb7b2-250">Formatter</span></span>              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    <span data-ttu-id="eb7b2-251">Le formateur de sortie par défaut</span><span class="sxs-lookup"><span data-stu-id="eb7b2-251">The default output formatter</span></span>    |
| `/api/products/5.json` | <span data-ttu-id="eb7b2-252">Le formateur JSON (s’il est configuré)</span><span class="sxs-lookup"><span data-stu-id="eb7b2-252">The JSON formatter (if configured)</span></span> |
| `/api/products/5.xml`  | <span data-ttu-id="eb7b2-253">Le formateur XML (s’il est configuré)</span><span class="sxs-lookup"><span data-stu-id="eb7b2-253">The XML formatter (if configured)</span></span>  |
