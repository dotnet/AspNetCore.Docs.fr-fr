---
title: BlazorGlobalisation et localisation ASP.net Core
author: guardrex
description: Découvrez comment rendre Razor des composants accessibles aux utilisateurs dans plusieurs cultures et langages.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
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
uid: blazor/globalization-localization
ms.openlocfilehash: 8baee9bc0a8e569174f33dac6a406b2162d09552
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279915"
---
# <a name="aspnet-core-blazor-globalization-and-localization"></a><span data-ttu-id="60b31-103">BlazorGlobalisation et localisation ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="60b31-103">ASP.NET Core Blazor globalization and localization</span></span>

<span data-ttu-id="60b31-104">Razor les composants peuvent être rendus accessibles aux utilisateurs dans plusieurs cultures et langues.</span><span class="sxs-lookup"><span data-stu-id="60b31-104">Razor components can be made accessible to users in multiple cultures and languages.</span></span> <span data-ttu-id="60b31-105">Les scénarios de globalisation et de localisation .NET suivants sont disponibles :</span><span class="sxs-lookup"><span data-stu-id="60b31-105">The following .NET globalization and localization scenarios are available:</span></span>

* <span data-ttu-id="60b31-106">. Système de ressources du réseau</span><span class="sxs-lookup"><span data-stu-id="60b31-106">.NET's resources system</span></span>
* <span data-ttu-id="60b31-107">Mise en forme des nombres et des dates spécifiques à la culture</span><span class="sxs-lookup"><span data-stu-id="60b31-107">Culture-specific number and date formatting</span></span>

<span data-ttu-id="60b31-108">Un ensemble limité de scénarios de localisation de ASP.NET Core est actuellement pris en charge :</span><span class="sxs-lookup"><span data-stu-id="60b31-108">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="60b31-109"><xref:Microsoft.Extensions.Localization.IStringLocalizer> et <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> *sont pris en charge* dans les Blazor applications.</span><span class="sxs-lookup"><span data-stu-id="60b31-109"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> *are supported* in Blazor apps.</span></span>
* <span data-ttu-id="60b31-110"><xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>la <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer> localisation des annotations de données, et est ASP.net Core les scénarios MVC et **non pris en charge** dans les Blazor applications.</span><span class="sxs-lookup"><span data-stu-id="60b31-110"><xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>, <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer>, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="60b31-111">Pour plus d’informations, consultez <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="60b31-111">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="globalization"></a><span data-ttu-id="60b31-112">Globalisation</span><span class="sxs-lookup"><span data-stu-id="60b31-112">Globalization</span></span>

<span data-ttu-id="60b31-113">Blazor[`@bind`](xref:mvc/views/razor#bind)la fonctionnalité de effectue des mises en forme et analyse les valeurs pour l’affichage en fonction de la culture actuelle de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="60b31-113">Blazor's [`@bind`](xref:mvc/views/razor#bind) functionality performs formats and parses values for display based on the user's current culture.</span></span>

<span data-ttu-id="60b31-114">La culture actuelle est accessible à partir de la <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> propriété.</span><span class="sxs-lookup"><span data-stu-id="60b31-114">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="60b31-115"><xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> est utilisé pour les types de champ suivants ( `<input type="{TYPE}" />` ) :</span><span class="sxs-lookup"><span data-stu-id="60b31-115"><xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="60b31-116">Les types de champ précédents :</span><span class="sxs-lookup"><span data-stu-id="60b31-116">The preceding field types:</span></span>

* <span data-ttu-id="60b31-117">Sont affichés à l’aide des règles de mise en forme appropriées basées sur le navigateur.</span><span class="sxs-lookup"><span data-stu-id="60b31-117">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="60b31-118">Ne peut pas contenir de texte de forme libre.</span><span class="sxs-lookup"><span data-stu-id="60b31-118">Can't contain free-form text.</span></span>
* <span data-ttu-id="60b31-119">Fournir des caractéristiques d’interaction de l’utilisateur en fonction de l’implémentation du navigateur.</span><span class="sxs-lookup"><span data-stu-id="60b31-119">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="60b31-120">Les types de champs suivants ont des exigences de mise en forme spécifiques et ne sont pas actuellement pris en charge par Blazor , car ils ne sont pas pris en charge par tous les principaux navigateurs :</span><span class="sxs-lookup"><span data-stu-id="60b31-120">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="60b31-121">[`@bind`](xref:mvc/views/razor#bind) prend en charge le `@bind:culture` paramètre pour fournir un <xref:System.Globalization.CultureInfo?displayProperty=fullName> pour l’analyse et la mise en forme d’une valeur.</span><span class="sxs-lookup"><span data-stu-id="60b31-121">[`@bind`](xref:mvc/views/razor#bind) supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="60b31-122">La spécification d’une culture n’est pas recommandée lors de l’utilisation des `date` `number` types de champ et.</span><span class="sxs-lookup"><span data-stu-id="60b31-122">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="60b31-123">`date` et `number` disposent d’une Blazor prise en charge intégrée qui fournit la culture requise.</span><span class="sxs-lookup"><span data-stu-id="60b31-123">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

## <a name="localization"></a><span data-ttu-id="60b31-124">Localisation</span><span class="sxs-lookup"><span data-stu-id="60b31-124">Localization</span></span>

### Blazor WebAssembly

<span data-ttu-id="60b31-125">Blazor WebAssembly les applications définissent la culture à l’aide de la [préférence de langue](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages)de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="60b31-125">Blazor WebAssembly apps set the culture using the user's [language preference](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages).</span></span>

<span data-ttu-id="60b31-126">Pour configurer explicitement la culture, définissez <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> et <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> dans `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="60b31-126">To explicitly configure the culture, set <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> and <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> in `Program.Main`.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="60b31-127">Par défaut, Blazor WebAssembly comporte des ressources de globalisation minimales requises pour afficher des valeurs, telles que les dates et les devises, dans la culture de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="60b31-127">By default, Blazor WebAssembly carries minimal globalization resources required to display values, such as dates and currency, in the user's culture.</span></span> <span data-ttu-id="60b31-128">Les applications qui doivent prendre en charge la modification dynamique de la culture doivent être configurées `BlazorWebAssemblyLoadAllGlobalizationData` dans le fichier projet :</span><span class="sxs-lookup"><span data-stu-id="60b31-128">Applications that must support dynamically changing the culture should configure `BlazorWebAssemblyLoadAllGlobalizationData` in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyLoadAllGlobalizationData>true</BlazorWebAssemblyLoadAllGlobalizationData>
</PropertyGroup>
```

<span data-ttu-id="60b31-129">Blazor WebAssembly peut également être configuré pour démarrer à l’aide d’une culture d’application spécifique à l’aide des options passées à `Blazor.start` .</span><span class="sxs-lookup"><span data-stu-id="60b31-129">Blazor WebAssembly can also be configured to launch using a specific application culture using options passed to `Blazor.start`.</span></span> <span data-ttu-id="60b31-130">Par exemple, l’exemple ci-dessous montre une application configurée pour être lancée à l’aide de la `en-GB` culture :</span><span class="sxs-lookup"><span data-stu-id="60b31-130">For instance, the sample below shows an app configured to launch using the `en-GB` culture:</span></span>

```html
<script src="_framework/blazor.webassembly.js" autostart="false"></script>
<script>
  Blazor.start({
    applicationCulture: 'en-GB'
  });
</script>
```

<span data-ttu-id="60b31-131">La valeur de `applicationCulture` doit être conforme au [format de balise de langage BCP-47](https://tools.ietf.org/html/bcp47).</span><span class="sxs-lookup"><span data-stu-id="60b31-131">The value for `applicationCulture` should conform to the [BCP-47 language tag format](https://tools.ietf.org/html/bcp47).</span></span>

<span data-ttu-id="60b31-132">Si l’application ne nécessite pas de localisation, vous pouvez configurer l’application pour qu’elle prenne en charge la culture dite indifférente, qui est basée sur la `en-US` culture :</span><span class="sxs-lookup"><span data-stu-id="60b31-132">If the app doesn't require localization, you may configure the app to support the invariant culture, which is based on the `en-US` culture:</span></span>

```xml
<PropertyGroup>
  <InvariantGlobalization>true</InvariantGlobalization>
</PropertyGroup>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="60b31-133">Par défaut, la configuration de l’éditeur de liens en langage intermédiaire (IL) pour les Blazor WebAssembly applications supprime les informations d’internationalisation, à l’exception des paramètres régionaux demandés explicitement.</span><span class="sxs-lookup"><span data-stu-id="60b31-133">By default, the Intermediate Language (IL) Linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="60b31-134">Pour plus d’informations, consultez <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>.</span><span class="sxs-lookup"><span data-stu-id="60b31-134">For more information, see <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>.</span></span>

::: moniker-end

<span data-ttu-id="60b31-135">Alors que la culture qui Blazor sélectionne par défaut peut être suffisante pour la plupart des utilisateurs, envisagez d’offrir aux utilisateurs un moyen de spécifier leurs paramètres régionaux préférés.</span><span class="sxs-lookup"><span data-stu-id="60b31-135">While the culture that Blazor selects by default might be sufficient for most users, consider offering a way for users to specify their preferred locale.</span></span> <span data-ttu-id="60b31-136">Pour obtenir un Blazor WebAssembly exemple d’application avec un sélecteur de culture, consultez l' [`LocSample`](https://github.com/pranavkm/LocSample) exemple d’application localisation.</span><span class="sxs-lookup"><span data-stu-id="60b31-136">For a Blazor WebAssembly sample app with a culture picker, see the [`LocSample`](https://github.com/pranavkm/LocSample) localization sample app.</span></span>

### Blazor Server

<span data-ttu-id="60b31-137">Blazor Server les applications sont localisées à l’aide de l' [intergiciel (middleware](xref:fundamentals/localization#localization-middleware)) de localisation.</span><span class="sxs-lookup"><span data-stu-id="60b31-137">Blazor Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="60b31-138">L’intergiciel sélectionne la culture appropriée pour les utilisateurs qui demandent des ressources à partir de l’application.</span><span class="sxs-lookup"><span data-stu-id="60b31-138">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="60b31-139">La culture peut être définie à l’aide de l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="60b31-139">The culture can be set using one of the following approaches:</span></span>

* <span data-ttu-id="60b31-140">[Cookies](#cookies)</span><span class="sxs-lookup"><span data-stu-id="60b31-140">[Cookies](#cookies)</span></span>
* [<span data-ttu-id="60b31-141">Fournir l’interface utilisateur pour choisir la culture</span><span class="sxs-lookup"><span data-stu-id="60b31-141">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="60b31-142">Pour plus d’informations et d’exemples, consultez <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="60b31-142">For more information and examples, see <xref:fundamentals/localization>.</span></span>

#### <a name="cookies"></a><span data-ttu-id="60b31-143">Cookies</span><span class="sxs-lookup"><span data-stu-id="60b31-143">Cookies</span></span>

<span data-ttu-id="60b31-144">Une culture cookie de localisation peut conserver la culture de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="60b31-144">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="60b31-145">L’intergiciel (middleware) de localisation lit les cookie sur les demandes suivantes pour définir la culture de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="60b31-145">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="60b31-146">L’utilisation d’un cookie s’assure que la connexion WebSocket peut propager correctement la culture.</span><span class="sxs-lookup"><span data-stu-id="60b31-146">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="60b31-147">Si les schémas de localisation sont basés sur le chemin d’URL ou la chaîne de requête, il est possible que le schéma ne soit pas en mesure de fonctionner avec les WebSockets, et donc de ne pas conserver la culture.</span><span class="sxs-lookup"><span data-stu-id="60b31-147">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="60b31-148">Par conséquent, l’utilisation d’une culture de localisation cookie est l’approche recommandée.</span><span class="sxs-lookup"><span data-stu-id="60b31-148">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="60b31-149">Toute technique peut être utilisée pour assigner une culture si la culture est rendue persistante dans une localisation cookie .</span><span class="sxs-lookup"><span data-stu-id="60b31-149">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="60b31-150">Si l’application a déjà un schéma de localisation établi pour ASP.NET Core côté serveur, continuez à utiliser l’infrastructure de localisation existante de l’application et à définir la culture de localisation cookie dans le schéma de l’application.</span><span class="sxs-lookup"><span data-stu-id="60b31-150">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="60b31-151">L’exemple suivant montre comment définir la culture actuelle dans une cookie qui peut être lue par l’intergiciel (middleware) de localisation.</span><span class="sxs-lookup"><span data-stu-id="60b31-151">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="60b31-152">Créer une Razor expression dans le `Pages/_Host.cshtml` fichier immédiatement à l’intérieur de la `<body>` balise d’ouverture :</span><span class="sxs-lookup"><span data-stu-id="60b31-152">Create a Razor expression in the `Pages/_Host.cshtml` file immediately inside the opening `<body>` tag:</span></span>

```cshtml
@using System.Globalization
@using Microsoft.AspNetCore.Localization

...

<body>
    @{
        this.HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }

    ...
</body>
```

<span data-ttu-id="60b31-153">La localisation est gérée par l’application dans la séquence d’événements suivante :</span><span class="sxs-lookup"><span data-stu-id="60b31-153">Localization is handled by the app in the following sequence of events:</span></span>

1. <span data-ttu-id="60b31-154">Le navigateur envoie une requête HTTP initiale à l’application.</span><span class="sxs-lookup"><span data-stu-id="60b31-154">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="60b31-155">La culture est affectée par l’intergiciel (middleware) de localisation.</span><span class="sxs-lookup"><span data-stu-id="60b31-155">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="60b31-156">L' Razor expression dans la `_Host` page ( `_Host.cshtml` ) conserve la culture dans un dans cookie le cadre de la réponse.</span><span class="sxs-lookup"><span data-stu-id="60b31-156">The Razor expression in the `_Host` page (`_Host.cshtml`) persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="60b31-157">Le navigateur ouvre une connexion WebSocket pour créer une Blazor Server session interactive.</span><span class="sxs-lookup"><span data-stu-id="60b31-157">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="60b31-158">L’intergiciel (middleware) de localisation lit cookie et assigne la culture.</span><span class="sxs-lookup"><span data-stu-id="60b31-158">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="60b31-159">La Blazor Server session commence par la culture correcte.</span><span class="sxs-lookup"><span data-stu-id="60b31-159">The Blazor Server session begins with the correct culture.</span></span>

<span data-ttu-id="60b31-160">Lorsque vous travaillez avec un <xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage> , utilisez la <xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context> propriété :</span><span class="sxs-lookup"><span data-stu-id="60b31-160">When working with a <xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage>, use the <xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context> property:</span></span>

```razor
@{
    this.Context.Response.Cookies.Append(
        CookieRequestCultureProvider.DefaultCookieName,
        CookieRequestCultureProvider.MakeCookieValue(
            new RequestCulture(
                CultureInfo.CurrentCulture,
                CultureInfo.CurrentUICulture)));
}
```

#### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="60b31-161">Fournir l’interface utilisateur pour choisir la culture</span><span class="sxs-lookup"><span data-stu-id="60b31-161">Provide UI to choose the culture</span></span>

<span data-ttu-id="60b31-162">Pour fournir une interface utilisateur permettant à un utilisateur de sélectionner une culture, il est recommandé d’effectuer une *approche basée sur la redirection* .</span><span class="sxs-lookup"><span data-stu-id="60b31-162">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="60b31-163">Le processus est similaire à ce qui se produit dans une application Web lorsqu’un utilisateur tente d’accéder à une ressource sécurisée.</span><span class="sxs-lookup"><span data-stu-id="60b31-163">The process is similar to what happens in a web app when a user attempts to access a secure resource.</span></span> <span data-ttu-id="60b31-164">L’utilisateur est redirigé vers une page de connexion, puis redirigé vers la ressource d’origine.</span><span class="sxs-lookup"><span data-stu-id="60b31-164">The user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="60b31-165">L’application conserve la culture sélectionnée de l’utilisateur via une redirection vers un contrôleur.</span><span class="sxs-lookup"><span data-stu-id="60b31-165">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="60b31-166">Le contrôleur définit la culture sélectionnée de l’utilisateur dans un cookie et redirige l’utilisateur vers l’URI d’origine.</span><span class="sxs-lookup"><span data-stu-id="60b31-166">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="60b31-167">Établissez un point de terminaison HTTP sur le serveur pour définir la culture sélectionnée de l’utilisateur dans un cookie et effectuer la redirection vers l’URI d’origine :</span><span class="sxs-lookup"><span data-stu-id="60b31-167">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> <span data-ttu-id="60b31-168">Utilisez le <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> résultat de l’action pour empêcher les attaques de redirection ouvertes.</span><span class="sxs-lookup"><span data-stu-id="60b31-168">Use the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> action result to prevent open redirect attacks.</span></span> <span data-ttu-id="60b31-169">Pour plus d’informations, consultez <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="60b31-169">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="60b31-170">Si l’application n’est pas configurée pour traiter les actions du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="60b31-170">If the app isn't configured to process controller actions:</span></span>

* <span data-ttu-id="60b31-171">Ajoutez des services MVC à la collection de services dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="60b31-171">Add MVC services to the service collection in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddControllers();
  ```

* <span data-ttu-id="60b31-172">Ajouter un routage de point de terminaison de contrôleur dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="60b31-172">Add controller endpoint routing in `Startup.Configure`:</span></span>

  ```csharp
  app.UseEndpoints(endpoints =>
  {
      endpoints.MapControllers();
      endpoints.MapBlazorHub();
      endpoints.MapFallbackToPage("/_Host");
  });
  ```

<span data-ttu-id="60b31-173">Le composant suivant montre un exemple d’exécution de la redirection initiale lorsque l’utilisateur sélectionne une culture :</span><span class="sxs-lookup"><span data-stu-id="60b31-173">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

```razor
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private void OnSelected(ChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri)
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="60b31-174">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="60b31-174">Additional resources</span></span>

* <xref:fundamentals/localization>
