---
title: Tag Helper Cache dans ASP.NET Core MVC
author: pkellner
description: Découvrez comment utiliser le Tag Helper Cache.
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
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
uid: mvc/views/tag-helpers/builtin-th/cache-tag-helper
ms.openlocfilehash: a87f91255bd1f280b1567f522423a6f4e88a6dd8
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060883"
---
# <a name="cache-tag-helper-in-aspnet-core-mvc"></a><span data-ttu-id="3c6b0-103">Tag Helper Cache dans ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="3c6b0-103">Cache Tag Helper in ASP.NET Core MVC</span></span>

<span data-ttu-id="3c6b0-104">Par [Peter Kellner](https://peterkellner.net)</span><span class="sxs-lookup"><span data-stu-id="3c6b0-104">By [Peter Kellner](https://peterkellner.net)</span></span>

<span data-ttu-id="3c6b0-105">Le Tag Helper Cache permet d’améliorer les performances de votre application ASP.NET Core en mettant en cache son contenu dans le fournisseur de caches ASP.NET Core interne.</span><span class="sxs-lookup"><span data-stu-id="3c6b0-105">The Cache Tag Helper provides the ability to improve the performance of your ASP.NET Core app by caching its content to the internal ASP.NET Core cache provider.</span></span>

<span data-ttu-id="3c6b0-106">Pour avoir une vue d’ensemble de Tag Helpers, consultez <xref:mvc/views/tag-helpers/intro>.</span><span class="sxs-lookup"><span data-stu-id="3c6b0-106">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="3c6b0-107">Le balisage suivant met en :::no-loc(Razor)::: cache la date actuelle :</span><span class="sxs-lookup"><span data-stu-id="3c6b0-107">The following :::no-loc(Razor)::: markup caches the current date:</span></span>

```cshtml
<cache>@DateTime.Now</cache>
```

<span data-ttu-id="3c6b0-108">La première requête à la page qui contient le Tag Helper affiche la date actuelle.</span><span class="sxs-lookup"><span data-stu-id="3c6b0-108">The first request to the page that contains the Tag Helper displays the current date.</span></span> <span data-ttu-id="3c6b0-109">Les autres requêtes affichent la valeur mise en cache jusqu’à ce que le cache expire (par défaut, 20 minutes) ou jusqu’à ce que la date du cache soit supprimée du cache.</span><span class="sxs-lookup"><span data-stu-id="3c6b0-109">Additional requests show the cached value until the cache expires (default 20 minutes) or until the cached date is evicted from the cache.</span></span>

## <a name="cache-tag-helper-attributes"></a><span data-ttu-id="3c6b0-110">Attributs de Tag Helper Cache</span><span class="sxs-lookup"><span data-stu-id="3c6b0-110">Cache Tag Helper Attributes</span></span>

### <a name="enabled"></a><span data-ttu-id="3c6b0-111">enabled</span><span class="sxs-lookup"><span data-stu-id="3c6b0-111">enabled</span></span>

| <span data-ttu-id="3c6b0-112">Type d’attribut</span><span class="sxs-lookup"><span data-stu-id="3c6b0-112">Attribute Type</span></span>  | <span data-ttu-id="3c6b0-113">Exemples</span><span class="sxs-lookup"><span data-stu-id="3c6b0-113">Examples</span></span>        | <span data-ttu-id="3c6b0-114">Default</span><span class="sxs-lookup"><span data-stu-id="3c6b0-114">Default</span></span> |
| --------------- | --------------- | ------- |
| <span data-ttu-id="3c6b0-115">Boolean</span><span class="sxs-lookup"><span data-stu-id="3c6b0-115">Boolean</span></span>         | <span data-ttu-id="3c6b0-116">`true`, `false`</span><span class="sxs-lookup"><span data-stu-id="3c6b0-116">`true`, `false`</span></span> | `true`  |

<span data-ttu-id="3c6b0-117">`enabled` détermine si le contenu joint par le Tag Helper Cache est mis en cache.</span><span class="sxs-lookup"><span data-stu-id="3c6b0-117">`enabled` determines if the content enclosed by the Cache Tag Helper is cached.</span></span> <span data-ttu-id="3c6b0-118">La valeur par défaut est `true`.</span><span class="sxs-lookup"><span data-stu-id="3c6b0-118">The default is `true`.</span></span> <span data-ttu-id="3c6b0-119">Si la valeur est `false`, la sortie rendue n’est **pas** mise en cache.</span><span class="sxs-lookup"><span data-stu-id="3c6b0-119">If set to `false`, the rendered output is **not** cached.</span></span>

<span data-ttu-id="3c6b0-120">Exemple :</span><span class="sxs-lookup"><span data-stu-id="3c6b0-120">Example:</span></span>

```cshtml
<cache enabled="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-on"></a><span data-ttu-id="3c6b0-121">expires-on</span><span class="sxs-lookup"><span data-stu-id="3c6b0-121">expires-on</span></span>

| <span data-ttu-id="3c6b0-122">Type d’attribut</span><span class="sxs-lookup"><span data-stu-id="3c6b0-122">Attribute Type</span></span>   | <span data-ttu-id="3c6b0-123">Exemple</span><span class="sxs-lookup"><span data-stu-id="3c6b0-123">Example</span></span>                            |
| ---------------- | ---------------------------------- |
| `DateTimeOffset` | `@new DateTime(2025,1,29,17,02,0)` |

<span data-ttu-id="3c6b0-124">`expires-on` définit une date d’expiration absolue pour l’élément mis en cache.</span><span class="sxs-lookup"><span data-stu-id="3c6b0-124">`expires-on` sets an absolute expiration date for the cached item.</span></span>

<span data-ttu-id="3c6b0-125">L’exemple suivant met en cache le contenu du Tag Helper Cache jusqu’à 17:02 le 29 janvier 2025 :</span><span class="sxs-lookup"><span data-stu-id="3c6b0-125">The following example caches the contents of the Cache Tag Helper until 5:02 PM on January 29, 2025:</span></span>

```cshtml
<cache expires-on="@new DateTime(2025,1,29,17,02,0)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-after"></a><span data-ttu-id="3c6b0-126">expires-after</span><span class="sxs-lookup"><span data-stu-id="3c6b0-126">expires-after</span></span>

| <span data-ttu-id="3c6b0-127">Type d’attribut</span><span class="sxs-lookup"><span data-stu-id="3c6b0-127">Attribute Type</span></span> | <span data-ttu-id="3c6b0-128">Exemple</span><span class="sxs-lookup"><span data-stu-id="3c6b0-128">Example</span></span>                      | <span data-ttu-id="3c6b0-129">Default</span><span class="sxs-lookup"><span data-stu-id="3c6b0-129">Default</span></span>    |
| -------------- | ---------------------------- | ---------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(120)` | <span data-ttu-id="3c6b0-130">20 minutes</span><span class="sxs-lookup"><span data-stu-id="3c6b0-130">20 minutes</span></span> |

<span data-ttu-id="3c6b0-131">`expires-after` définit la durée à partir de l’heure de la première demande pour mettre en cache le contenu.</span><span class="sxs-lookup"><span data-stu-id="3c6b0-131">`expires-after` sets the length of time from the first request time to cache the contents.</span></span>

<span data-ttu-id="3c6b0-132">Exemple :</span><span class="sxs-lookup"><span data-stu-id="3c6b0-132">Example:</span></span>

```cshtml
<cache expires-after="@TimeSpan.FromSeconds(120)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

<span data-ttu-id="3c6b0-133">Le :::no-loc(Razor)::: moteur d’affichage définit la `expires-after` valeur par défaut sur vingt minutes.</span><span class="sxs-lookup"><span data-stu-id="3c6b0-133">The :::no-loc(Razor)::: View Engine sets the default `expires-after` value to twenty minutes.</span></span>

### <a name="expires-sliding"></a><span data-ttu-id="3c6b0-134">expires-sliding</span><span class="sxs-lookup"><span data-stu-id="3c6b0-134">expires-sliding</span></span>

| <span data-ttu-id="3c6b0-135">Type d’attribut</span><span class="sxs-lookup"><span data-stu-id="3c6b0-135">Attribute Type</span></span> | <span data-ttu-id="3c6b0-136">Exemple</span><span class="sxs-lookup"><span data-stu-id="3c6b0-136">Example</span></span>                     |
| -------------- | --------------------------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(60)` |

<span data-ttu-id="3c6b0-137">Définit l’heure à laquelle une entrée de cache doit être supprimée si sa valeur n’a fait l’objet d’aucun accès.</span><span class="sxs-lookup"><span data-stu-id="3c6b0-137">Sets the time that a cache entry should be evicted if its value hasn't been accessed.</span></span>

<span data-ttu-id="3c6b0-138">Exemple :</span><span class="sxs-lookup"><span data-stu-id="3c6b0-138">Example:</span></span>

```cshtml
<cache expires-sliding="@TimeSpan.FromSeconds(60)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-header"></a><span data-ttu-id="3c6b0-139">vary-by-header</span><span class="sxs-lookup"><span data-stu-id="3c6b0-139">vary-by-header</span></span>

| <span data-ttu-id="3c6b0-140">Type d’attribut</span><span class="sxs-lookup"><span data-stu-id="3c6b0-140">Attribute Type</span></span> | <span data-ttu-id="3c6b0-141">Exemples</span><span class="sxs-lookup"><span data-stu-id="3c6b0-141">Examples</span></span>                                    |
| -------------- | ------------------------------------------- |
| <span data-ttu-id="3c6b0-142">String</span><span class="sxs-lookup"><span data-stu-id="3c6b0-142">String</span></span>         | <span data-ttu-id="3c6b0-143">`User-Agent`, `User-Agent,content-encoding`</span><span class="sxs-lookup"><span data-stu-id="3c6b0-143">`User-Agent`, `User-Agent,content-encoding`</span></span> |

<span data-ttu-id="3c6b0-144">`vary-by-header` accepte une liste séparée par des virgules de valeurs d’en-tête qui déclenchent une actualisation du cache quand elles changent.</span><span class="sxs-lookup"><span data-stu-id="3c6b0-144">`vary-by-header` accepts a comma-delimited list of header values that trigger a cache refresh when they change.</span></span>

<span data-ttu-id="3c6b0-145">L’exemple suivant analyse la valeur d’en-tête `User-Agent`.</span><span class="sxs-lookup"><span data-stu-id="3c6b0-145">The following example monitors the header value `User-Agent`.</span></span> <span data-ttu-id="3c6b0-146">L’exemple met en cache le contenu de chaque valeur `User-Agent` différente présentée au serveur web :</span><span class="sxs-lookup"><span data-stu-id="3c6b0-146">The example caches the content for every different `User-Agent` presented to the web server:</span></span>

```cshtml
<cache vary-by-header="User-Agent">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-query"></a><span data-ttu-id="3c6b0-147">vary-by-query</span><span class="sxs-lookup"><span data-stu-id="3c6b0-147">vary-by-query</span></span>

| <span data-ttu-id="3c6b0-148">Type d’attribut</span><span class="sxs-lookup"><span data-stu-id="3c6b0-148">Attribute Type</span></span> | <span data-ttu-id="3c6b0-149">Exemples</span><span class="sxs-lookup"><span data-stu-id="3c6b0-149">Examples</span></span>             |
| -------------- | -------------------- |
| <span data-ttu-id="3c6b0-150">String</span><span class="sxs-lookup"><span data-stu-id="3c6b0-150">String</span></span>         | <span data-ttu-id="3c6b0-151">`Make`, `Make,Model`</span><span class="sxs-lookup"><span data-stu-id="3c6b0-151">`Make`, `Make,Model`</span></span> |

<span data-ttu-id="3c6b0-152">`vary-by-query` accepte une liste de <xref:Microsoft.AspNetCore.Http.IQueryCollection.Keys*> séparées par des virgules dans une chaîne de requête (<xref:Microsoft.AspNetCore.Http.HttpRequest.Query*>) déclenchant une actualisation du cache quand la valeur de l’une des clés répertoriées est modifiée.</span><span class="sxs-lookup"><span data-stu-id="3c6b0-152">`vary-by-query` accepts a comma-delimited list of <xref:Microsoft.AspNetCore.Http.IQueryCollection.Keys*> in a query string (<xref:Microsoft.AspNetCore.Http.HttpRequest.Query*>) that trigger a cache refresh when the value of any listed key changes.</span></span>

<span data-ttu-id="3c6b0-153">L’exemple suivant analyse les valeurs de `Make` et `Model`.</span><span class="sxs-lookup"><span data-stu-id="3c6b0-153">The following example monitors the values of `Make` and `Model`.</span></span> <span data-ttu-id="3c6b0-154">L’exemple met en cache le contenu de chaque valeur `Make` et `Model` différente présentée au serveur web :</span><span class="sxs-lookup"><span data-stu-id="3c6b0-154">The example caches the content for every different `Make` and `Model` presented to the web server:</span></span>

```cshtml
<cache vary-by-query="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-route"></a><span data-ttu-id="3c6b0-155">vary-by-route</span><span class="sxs-lookup"><span data-stu-id="3c6b0-155">vary-by-route</span></span>

| <span data-ttu-id="3c6b0-156">Type d’attribut</span><span class="sxs-lookup"><span data-stu-id="3c6b0-156">Attribute Type</span></span> | <span data-ttu-id="3c6b0-157">Exemples</span><span class="sxs-lookup"><span data-stu-id="3c6b0-157">Examples</span></span>             |
| -------------- | -------------------- |
| <span data-ttu-id="3c6b0-158">String</span><span class="sxs-lookup"><span data-stu-id="3c6b0-158">String</span></span>         | <span data-ttu-id="3c6b0-159">`Make`, `Make,Model`</span><span class="sxs-lookup"><span data-stu-id="3c6b0-159">`Make`, `Make,Model`</span></span> |

<span data-ttu-id="3c6b0-160">`vary-by-route` accepte une liste séparée par des virgules de noms de paramètre de route qui déclenchent une actualisation du cache quand la valeur du paramètre des données de route change.</span><span class="sxs-lookup"><span data-stu-id="3c6b0-160">`vary-by-route` accepts a comma-delimited list of route parameter names that trigger a cache refresh when the route data parameter value changes.</span></span>

<span data-ttu-id="3c6b0-161">Exemple :</span><span class="sxs-lookup"><span data-stu-id="3c6b0-161">Example:</span></span>

<span data-ttu-id="3c6b0-162">*Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="3c6b0-162">*Startup.cs* :</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{Make?}/{Model?}");
```

<span data-ttu-id="3c6b0-163">*Index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="3c6b0-163">*Index.cshtml* :</span></span>

```cshtml
<cache vary-by-route="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-no-loccookie"></a><span data-ttu-id="3c6b0-164">variation par:::no-loc(cookie):::</span><span class="sxs-lookup"><span data-stu-id="3c6b0-164">vary-by-:::no-loc(cookie):::</span></span>

| <span data-ttu-id="3c6b0-165">Type d’attribut</span><span class="sxs-lookup"><span data-stu-id="3c6b0-165">Attribute Type</span></span> | <span data-ttu-id="3c6b0-166">Exemples</span><span class="sxs-lookup"><span data-stu-id="3c6b0-166">Examples</span></span>                                                                         |
| -------------- | -------------------------------------------------------------------------------- |
| <span data-ttu-id="3c6b0-167">String</span><span class="sxs-lookup"><span data-stu-id="3c6b0-167">String</span></span>         | <span data-ttu-id="3c6b0-168">`.AspNetCore.:::no-loc(Identity):::.Application`, `.AspNetCore.:::no-loc(Identity):::.Application,HairColor`</span><span class="sxs-lookup"><span data-stu-id="3c6b0-168">`.AspNetCore.:::no-loc(Identity):::.Application`, `.AspNetCore.:::no-loc(Identity):::.Application,HairColor`</span></span> |

<span data-ttu-id="3c6b0-169">`vary-by-:::no-loc(cookie):::` accepte une liste de noms délimités par des virgules :::no-loc(cookie)::: qui déclenchent une actualisation du cache lorsque les :::no-loc(cookie)::: valeurs changent.</span><span class="sxs-lookup"><span data-stu-id="3c6b0-169">`vary-by-:::no-loc(cookie):::` accepts a comma-delimited list of :::no-loc(cookie)::: names that trigger a cache refresh when the :::no-loc(cookie)::: values change.</span></span>

<span data-ttu-id="3c6b0-170">L’exemple suivant analyse le :::no-loc(cookie)::: associé à :::no-loc(ASP.NET Core Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="3c6b0-170">The following example monitors the :::no-loc(cookie)::: associated with :::no-loc(ASP.NET Core Identity):::.</span></span> <span data-ttu-id="3c6b0-171">Lorsqu’un utilisateur est authentifié, une modification du :::no-loc(Identity)::: :::no-loc(cookie)::: déclenche une actualisation du cache :</span><span class="sxs-lookup"><span data-stu-id="3c6b0-171">When a user is authenticated, a change in the :::no-loc(Identity)::: :::no-loc(cookie)::: triggers a cache refresh:</span></span>

```cshtml
<cache vary-by-:::no-loc(cookie):::=".AspNetCore.:::no-loc(Identity):::.Application">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-user"></a><span data-ttu-id="3c6b0-172">vary-by-user</span><span class="sxs-lookup"><span data-stu-id="3c6b0-172">vary-by-user</span></span>

| <span data-ttu-id="3c6b0-173">Type d’attribut</span><span class="sxs-lookup"><span data-stu-id="3c6b0-173">Attribute Type</span></span>  | <span data-ttu-id="3c6b0-174">Exemples</span><span class="sxs-lookup"><span data-stu-id="3c6b0-174">Examples</span></span>        | <span data-ttu-id="3c6b0-175">Default</span><span class="sxs-lookup"><span data-stu-id="3c6b0-175">Default</span></span> |
| --------------- | --------------- | ------- |
| <span data-ttu-id="3c6b0-176">Boolean</span><span class="sxs-lookup"><span data-stu-id="3c6b0-176">Boolean</span></span>         | <span data-ttu-id="3c6b0-177">`true`, `false`</span><span class="sxs-lookup"><span data-stu-id="3c6b0-177">`true`, `false`</span></span> | `true`  |

<span data-ttu-id="3c6b0-178">`vary-by-user` spécifie si le cache se réinitialise ou pas quand l’utilisateur connecté (ou principal du contexte) change.</span><span class="sxs-lookup"><span data-stu-id="3c6b0-178">`vary-by-user` specifies whether or not the cache resets when the signed-in user (or Context Principal) changes.</span></span> <span data-ttu-id="3c6b0-179">L’utilisateur actuel est également appelé principal du contexte de la demande et peut être affiché dans une :::no-loc(Razor)::: vue en référençant `@User.:::no-loc(Identity):::.Name` .</span><span class="sxs-lookup"><span data-stu-id="3c6b0-179">The current user is also known as the Request Context Principal and can be viewed in a :::no-loc(Razor)::: view by referencing `@User.:::no-loc(Identity):::.Name`.</span></span>

<span data-ttu-id="3c6b0-180">L’exemple suivant analyse l’utilisateur actuellement connecté pour déclencher une actualisation du cache :</span><span class="sxs-lookup"><span data-stu-id="3c6b0-180">The following example monitors the current logged in user to trigger a cache refresh:</span></span>

```cshtml
<cache vary-by-user="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

<span data-ttu-id="3c6b0-181">L’utilisation de cet attribut permet de conserver le contenu dans le cache lors d’un cycle de connexion et de déconnexion.</span><span class="sxs-lookup"><span data-stu-id="3c6b0-181">Using this attribute maintains the contents in cache through a sign-in and sign-out cycle.</span></span> <span data-ttu-id="3c6b0-182">Lorsque la valeur est définie sur `true`, un cycle d’authentification invalide le cache pour l’utilisateur authentifié.</span><span class="sxs-lookup"><span data-stu-id="3c6b0-182">When the value is set to `true`, an authentication cycle invalidates the cache for the authenticated user.</span></span> <span data-ttu-id="3c6b0-183">Le cache est invalidé, car une nouvelle valeur unique :::no-loc(cookie)::: est générée lors de l’authentification d’un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="3c6b0-183">The cache is invalidated because a new unique :::no-loc(cookie)::: value is generated when a user is authenticated.</span></span> <span data-ttu-id="3c6b0-184">Le cache est conservé pour l’État anonyme quand aucun :::no-loc(cookie)::: n’est présent ou que :::no-loc(cookie)::: a expiré.</span><span class="sxs-lookup"><span data-stu-id="3c6b0-184">Cache is maintained for the anonymous state when no :::no-loc(cookie)::: is present or the :::no-loc(cookie)::: has expired.</span></span> <span data-ttu-id="3c6b0-185">Si l’utilisateur n’est **pas** authentifié, le cache est conservé.</span><span class="sxs-lookup"><span data-stu-id="3c6b0-185">If the user is **not** authenticated, the cache is maintained.</span></span>

### <a name="vary-by"></a><span data-ttu-id="3c6b0-186">vary-by</span><span class="sxs-lookup"><span data-stu-id="3c6b0-186">vary-by</span></span>

| <span data-ttu-id="3c6b0-187">Type d’attribut</span><span class="sxs-lookup"><span data-stu-id="3c6b0-187">Attribute Type</span></span> | <span data-ttu-id="3c6b0-188">Exemple</span><span class="sxs-lookup"><span data-stu-id="3c6b0-188">Example</span></span>  |
| -------------- | -------- |
| <span data-ttu-id="3c6b0-189">String</span><span class="sxs-lookup"><span data-stu-id="3c6b0-189">String</span></span>         | `@Model` |

<span data-ttu-id="3c6b0-190">`vary-by` permet la personnalisation des données mises en cache.</span><span class="sxs-lookup"><span data-stu-id="3c6b0-190">`vary-by` allows for customization of what data is cached.</span></span> <span data-ttu-id="3c6b0-191">Quand l’objet référencé par la valeur de la chaîne de l’attribut change, le contenu du Tag Helper Cache est mis à jour.</span><span class="sxs-lookup"><span data-stu-id="3c6b0-191">When the object referenced by the attribute's string value changes, the content of the Cache Tag Helper is updated.</span></span> <span data-ttu-id="3c6b0-192">Souvent, une concaténation de chaîne des valeurs de modèle est affectée à cet attribut.</span><span class="sxs-lookup"><span data-stu-id="3c6b0-192">Often, a string-concatenation of model values are assigned to this attribute.</span></span> <span data-ttu-id="3c6b0-193">En effet, cela entraîne un scénario où une mise à jour de l’une des valeurs concaténées invalide le cache.</span><span class="sxs-lookup"><span data-stu-id="3c6b0-193">Effectively, this results in a scenario where an update to any of the concatenated values invalidates the cache.</span></span>

<span data-ttu-id="3c6b0-194">L’exemple suivant suppose que la méthode de contrôleur restituant la vue additionne la valeur entière des deux paramètres de routage, `myParam1` et `myParam2`, et retourne la somme en tant que propriété de modèle unique.</span><span class="sxs-lookup"><span data-stu-id="3c6b0-194">The following example assumes the controller method rendering the view sums the integer value of the two route parameters, `myParam1` and `myParam2`, and returns the sum as the single model property.</span></span> <span data-ttu-id="3c6b0-195">Quand cette somme est modifiée, le contenu du Tag Helper Cache est rendu et mis en cache à nouveau.</span><span class="sxs-lookup"><span data-stu-id="3c6b0-195">When this sum changes, the content of the Cache Tag Helper is rendered and cached again.</span></span>  

<span data-ttu-id="3c6b0-196">Action :</span><span class="sxs-lookup"><span data-stu-id="3c6b0-196">Action:</span></span>

```csharp
public IActionResult Index(string myParam1, string myParam2, string myParam3)
{
    int num1;
    int num2;
    int.TryParse(myParam1, out num1);
    int.TryParse(myParam2, out num2);
    return View(viewName, num1 + num2);
}
```

<span data-ttu-id="3c6b0-197">*Index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="3c6b0-197">*Index.cshtml* :</span></span>

```cshtml
<cache vary-by="@Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="priority"></a><span data-ttu-id="3c6b0-198">priority</span><span class="sxs-lookup"><span data-stu-id="3c6b0-198">priority</span></span>

| <span data-ttu-id="3c6b0-199">Type d’attribut</span><span class="sxs-lookup"><span data-stu-id="3c6b0-199">Attribute Type</span></span>      | <span data-ttu-id="3c6b0-200">Exemples</span><span class="sxs-lookup"><span data-stu-id="3c6b0-200">Examples</span></span>                               | <span data-ttu-id="3c6b0-201">Default</span><span class="sxs-lookup"><span data-stu-id="3c6b0-201">Default</span></span>  |
| ------------------- | -------------------------------------- | -------- |
| `CacheItemPriority` | <span data-ttu-id="3c6b0-202">`High`, `Low`, `NeverRemove`, `Normal`</span><span class="sxs-lookup"><span data-stu-id="3c6b0-202">`High`, `Low`, `NeverRemove`, `Normal`</span></span> | `Normal` |

<span data-ttu-id="3c6b0-203">`priority` fournit des instructions de suppression de cache au fournisseur de caches intégré.</span><span class="sxs-lookup"><span data-stu-id="3c6b0-203">`priority` provides cache eviction guidance to the built-in cache provider.</span></span> <span data-ttu-id="3c6b0-204">Le serveur web supprime d’abord les entrées de cache `Low` en cas de sollicitation de la mémoire.</span><span class="sxs-lookup"><span data-stu-id="3c6b0-204">The web server evicts `Low` cache entries first when it's under memory pressure.</span></span>

<span data-ttu-id="3c6b0-205">Exemple :</span><span class="sxs-lookup"><span data-stu-id="3c6b0-205">Example:</span></span>

```cshtml
<cache priority="High">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

<span data-ttu-id="3c6b0-206">L’attribut `priority` ne garantit pas un niveau spécifique de rétention du cache.</span><span class="sxs-lookup"><span data-stu-id="3c6b0-206">The `priority` attribute doesn't guarantee a specific level of cache retention.</span></span> <span data-ttu-id="3c6b0-207">`CacheItemPriority` est uniquement une suggestion.</span><span class="sxs-lookup"><span data-stu-id="3c6b0-207">`CacheItemPriority` is only a suggestion.</span></span> <span data-ttu-id="3c6b0-208">Si vous affectez à cet attribut la valeur `NeverRemove`, il n’est pas garanti que les éléments du cache soient toujours conservés.</span><span class="sxs-lookup"><span data-stu-id="3c6b0-208">Setting this attribute to `NeverRemove` doesn't guarantee that cached items are always retained.</span></span> <span data-ttu-id="3c6b0-209">Pour plus d’informations, consultez les rubriques de la section [Ressources supplémentaires](#additional-resources).</span><span class="sxs-lookup"><span data-stu-id="3c6b0-209">See the topics in the [Additional Resources](#additional-resources) section for more information.</span></span>

<span data-ttu-id="3c6b0-210">Le Tag Helper Cache dépend du [service de cache en mémoire](xref:performance/caching/memory).</span><span class="sxs-lookup"><span data-stu-id="3c6b0-210">The Cache Tag Helper is dependent on the [memory cache service](xref:performance/caching/memory).</span></span> <span data-ttu-id="3c6b0-211">Le Tag Helper Cache ajoute le service s’il ne l’a pas encore été.</span><span class="sxs-lookup"><span data-stu-id="3c6b0-211">The Cache Tag Helper adds the service if it hasn't been added.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3c6b0-212">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="3c6b0-212">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
