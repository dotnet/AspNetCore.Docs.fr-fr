---
title: Exemple de SameSite ASP.NET Core MVC 2,1 cookie
author: rick-anderson
description: Exemple de SameSite ASP.NET Core MVC 2,1 cookie
monikerRange: '>= aspnetcore-2.1 < aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
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
uid: security/samesite/mvc21
ms.openlocfilehash: a7b7ce0d0fff2953097f29a2b6d2c5a539cac3a5
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586525"
---
# <a name="aspnet-core-21-mvc-samesite-cookie-sample"></a><span data-ttu-id="b5d16-103">Exemple de SameSite ASP.NET Core MVC 2,1 cookie</span><span class="sxs-lookup"><span data-stu-id="b5d16-103">ASP.NET Core 2.1 MVC SameSite cookie sample</span></span>

<span data-ttu-id="b5d16-104">ASP.NET Core 2,1 dispose d’une prise en charge intégrée de l’attribut [SameSite](https://www.owasp.org/index.php/SameSite) , mais il a été écrit dans la norme d’origine.</span><span class="sxs-lookup"><span data-stu-id="b5d16-104">ASP.NET Core 2.1 has built-in support for the [SameSite](https://www.owasp.org/index.php/SameSite) attribute, but it was written to the original standard.</span></span> <span data-ttu-id="b5d16-105">Le [comportement corrigé](https://github.com/dotnet/aspnetcore/issues/8212) a modifié la signification de `SameSite.None` pour émettre l’attribut sameSite avec une valeur de `None` , au lieu de ne pas émettre la valeur du tout.</span><span class="sxs-lookup"><span data-stu-id="b5d16-105">The [patched behavior](https://github.com/dotnet/aspnetcore/issues/8212) changed the meaning of `SameSite.None` to emit the sameSite attribute with a value of `None`, rather than not emit the value at all.</span></span> <span data-ttu-id="b5d16-106">Si vous ne souhaitez pas émettre la valeur, vous pouvez définir la `SameSite` propriété sur cookie -1.</span><span class="sxs-lookup"><span data-stu-id="b5d16-106">If you want to not emit the value you can set the `SameSite` property on a cookie to -1.</span></span>

[!INCLUDE[](~/includes/SameSiteIdentity.md)]

## <a name="writing-the-samesite-attribute"></a><a name="sampleCode"></a><span data-ttu-id="b5d16-107">Écriture de l’attribut SameSite</span><span class="sxs-lookup"><span data-stu-id="b5d16-107">Writing the SameSite attribute</span></span>

<span data-ttu-id="b5d16-108">Voici un exemple d’écriture d’un attribut SameSite sur un cookie :</span><span class="sxs-lookup"><span data-stu-id="b5d16-108">Following is an example of how to write a SameSite attribute on a cookie:</span></span>

```csharp
var cookieOptions = new CookieOptions
{
    // Set the secure flag, which Chrome's changes will require for SameSite none.
    // Note this will also require you to be running on HTTPS
    Secure = true,

    // Set the cookie to HTTP only which is good practice unless you really do need
    // to access it client side in scripts.
    HttpOnly = true,

    // Add the SameSite attribute, this will emit the attribute with a value of none.
    // To not emit the attribute at all set the SameSite property to (SameSiteMode)(-1).
    SameSite = SameSiteMode.None
};

// Add the cookie to the response cookie collection
Response.Cookies.Append(CookieName, "cookieValue", cookieOptions);
```

## <a name="setting-cookie-authentication-and-session-state-cookies"></a><span data-ttu-id="b5d16-109">Définition Cookie des États d’authentification et de session cookie</span><span class="sxs-lookup"><span data-stu-id="b5d16-109">Setting Cookie Authentication and Session State cookies</span></span>

<span data-ttu-id="b5d16-110">Cookie l’authentification, l’état de session et [divers autres composants](../samesite.md?view=aspnetcore-2.1) définissent leurs options sameSite via Cookie des options, par exemple</span><span class="sxs-lookup"><span data-stu-id="b5d16-110">Cookie authentication, session state and [various other components](../samesite.md?view=aspnetcore-2.1) set their sameSite options via Cookie options, for example</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.Cookie.SameSite = SameSiteMode.None;
        options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
        options.Cookie.IsEssential = true;
    });

services.AddSession(options =>
{
    options.Cookie.SameSite = SameSiteMode.None;
    options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
    options.Cookie.IsEssential = true;
});
```

<span data-ttu-id="b5d16-111">Dans le code précédent, cookie l’authentification et l’état de session définissent l’attribut sameSite sur `None` , en émettant l’attribut avec une `None` valeur, et attribuent également la valeur true à l’attribut Secure.</span><span class="sxs-lookup"><span data-stu-id="b5d16-111">In the preceding code, both cookie authentication and session state set their sameSite attribute to `None`, emitting the attribute with a `None` value, and also set the Secure attribute to true.</span></span>

### <a name="run-the-sample"></a><span data-ttu-id="b5d16-112">Exécution de l'exemple</span><span class="sxs-lookup"><span data-stu-id="b5d16-112">Run the sample</span></span>

<span data-ttu-id="b5d16-113">Si vous exécutez l' [exemple de projet](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC), chargez votre débogueur de navigateur sur la page initiale et utilisez-le pour afficher le cookie regroupement du site.</span><span class="sxs-lookup"><span data-stu-id="b5d16-113">If you run the [sample project](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC), load your browser debugger on the initial page and use it to view the cookie collection for the site.</span></span> <span data-ttu-id="b5d16-114">Pour ce faire, dans Edge et chrome, appuyez sur l' `F12` `Application` onglet, puis cliquez sur l’URL du site sous l' `Cookies` option dans la `Storage` section.</span><span class="sxs-lookup"><span data-stu-id="b5d16-114">To do so in Edge and Chrome press `F12` then select the `Application` tab and click the site URL under the `Cookies` option in the `Storage` section.</span></span>

![Débogueur de navigateur ::: No-Loc (cookie) ::: List](BrowserDebugger.png)

<span data-ttu-id="b5d16-116">Vous pouvez voir à partir de l’image ci-dessus que le cookie créé par l’exemple lorsque vous cliquez sur le bouton « Create SameSite Cookie » a une valeur d’attribut SameSite égale `Lax` à, qui correspond à la valeur définie dans l' [exemple de code](#sampleCode).</span><span class="sxs-lookup"><span data-stu-id="b5d16-116">You can see from the image above that the cookie created by the sample when you click the "Create SameSite Cookie" button has a SameSite attribute value of `Lax`, matching the value set in the [sample code](#sampleCode).</span></span>

## <a name="intercepting-cookies"></a><a name="interception"></a><span data-ttu-id="b5d16-117">Interception des cookie s</span><span class="sxs-lookup"><span data-stu-id="b5d16-117">Intercepting cookies</span></span>

<span data-ttu-id="b5d16-118">Afin d’intercepter des cookie s, pour ajuster la valeur None en fonction de sa prise en charge dans l’agent Browser de l’utilisateur, vous devez utiliser l' `CookiePolicy` intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="b5d16-118">In order to intercept cookies, to adjust the none value according to its support in the user's browser agent you must use the `CookiePolicy` middleware.</span></span> <span data-ttu-id="b5d16-119">Celui-ci doit être placé dans le pipeline de requête HTTP **avant** tous les composants qui écrivent des cookie et configurés dans `ConfigureServices()` .</span><span class="sxs-lookup"><span data-stu-id="b5d16-119">This must be placed into the http request pipeline **before** any components that write cookies and configured within `ConfigureServices()`.</span></span>

<span data-ttu-id="b5d16-120">Pour l’insérer dans le pipeline `app.UseCookiePolicy()` , utilisez la `Configure(IApplicationBuilder, IHostingEnvironment)` méthode dans [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs).</span><span class="sxs-lookup"><span data-stu-id="b5d16-120">To insert it into the pipeline use `app.UseCookiePolicy()` in the `Configure(IApplicationBuilder, IHostingEnvironment)` method in [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs).</span></span> <span data-ttu-id="b5d16-121">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="b5d16-121">For example:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
       app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseAuthentication();
    app.UseSession();

    app.UseMvc(routes =>
    {
        routes.MapRoute(
            name: "default",
            template: "{controller=Home}/{action=Index}/{id?}");
    });
}
```

<span data-ttu-id="b5d16-122">Ensuite, dans la, `ConfigureServices(IServiceCollection services)` configurez la cookie stratégie pour appeler une classe d’assistance lorsque les cookie s sont ajoutés ou supprimés.</span><span class="sxs-lookup"><span data-stu-id="b5d16-122">Then in the `ConfigureServices(IServiceCollection services)` configure the cookie policy to call out to a helper class when cookies are appended or deleted.</span></span> <span data-ttu-id="b5d16-123">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="b5d16-123">For example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<CookiePolicyOptions>(options =>
    {
        options.CheckConsentNeeded = context => true;
        options.MinimumSameSitePolicy = SameSiteMode.None;
        options.OnAppendCookie = cookieContext =>
            CheckSameSite(cookieContext.Context, cookieContext.CookieOptions);
        options.OnDeleteCookie = cookieContext =>
            CheckSameSite(cookieContext.Context, cookieContext.CookieOptions);
    });
}

private void CheckSameSite(HttpContext httpContext, CookieOptions options)
{
    if (options.SameSite == SameSiteMode.None)
    {
        var userAgent = httpContext.Request.Headers["User-Agent"].ToString();
        if (SameSite.BrowserDetection.DisallowsSameSiteNone(userAgent))
        {
            options.SameSite = (SameSiteMode)(-1);
        }
    }
}
```

<span data-ttu-id="b5d16-124">Fonction d’assistance `CheckSameSite(HttpContext, CookieOptions)` :</span><span class="sxs-lookup"><span data-stu-id="b5d16-124">The helper function `CheckSameSite(HttpContext, CookieOptions)`:</span></span>

* <span data-ttu-id="b5d16-125">Est appelé quand les cookie s sont ajoutés à la demande ou supprimés de la requête.</span><span class="sxs-lookup"><span data-stu-id="b5d16-125">Is called when cookies are appended to the request or deleted from the request.</span></span>
* <span data-ttu-id="b5d16-126">Vérifie si la `SameSite` propriété a la valeur `None` .</span><span class="sxs-lookup"><span data-stu-id="b5d16-126">Checks to see if the `SameSite` property is set to `None`.</span></span>
* <span data-ttu-id="b5d16-127">Si `SameSite` a la valeur `None` et que l’agent utilisateur actuel est connu pour ne pas prendre en charge la valeur d’attribut None.</span><span class="sxs-lookup"><span data-stu-id="b5d16-127">If `SameSite` is set to `None` and the current user agent is known to not support the none attribute value.</span></span> <span data-ttu-id="b5d16-128">La vérification s’effectue à l’aide de la classe [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) :</span><span class="sxs-lookup"><span data-stu-id="b5d16-128">The check is done using the [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) class:</span></span>
  * <span data-ttu-id="b5d16-129">Définit `SameSite` pour ne pas émettre la valeur en affectant à la propriété la valeur `(SameSiteMode)(-1)`</span><span class="sxs-lookup"><span data-stu-id="b5d16-129">Sets `SameSite` to not emit the value by setting the property to `(SameSiteMode)(-1)`</span></span>

## <a name="targeting-net-framework"></a><span data-ttu-id="b5d16-130">Ciblage .NET Framework</span><span class="sxs-lookup"><span data-stu-id="b5d16-130">Targeting .NET Framework</span></span>

<span data-ttu-id="b5d16-131">ASP.NET Core et System. Web (ASP.NET 4. x) ont des implémentations indépendantes de SameSite.</span><span class="sxs-lookup"><span data-stu-id="b5d16-131">ASP.NET Core and System.Web (ASP.NET 4.x) have independent implementations of SameSite.</span></span> <span data-ttu-id="b5d16-132">Les correctifs de SameSite KB pour .NET Framework ne sont pas requis si vous utilisez ASP.NET Core ni la version minimale requise de l’infrastructure System. Web SameSite (.NET Framework 4.7.2) s’appliquent à ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b5d16-132">The SameSite KB patches for .NET Framework are not required if using ASP.NET Core nor does the System.Web SameSite minimum framework version requirement (.NET Framework 4.7.2) apply to ASP.NET Core.</span></span>

<span data-ttu-id="b5d16-133">ASP.NET Core sur .NET requiert la mise à jour des dépendances de package NuGet pour récupérer les correctifs appropriés.</span><span class="sxs-lookup"><span data-stu-id="b5d16-133">ASP.NET Core on .NET requires updating NuGet package dependencies to get the appropriate fixes.</span></span>

<span data-ttu-id="b5d16-134">Pour obtenir les modifications de ASP.NET Core pour .NET Framework Vérifiez que vous disposez d’une référence directe aux packages et versions corrigés (2.1.14 ou versions ultérieures 2,1).</span><span class="sxs-lookup"><span data-stu-id="b5d16-134">To get the ASP.NET Core changes for .NET Framework ensure that you have a direct reference to the patched packages and versions (2.1.14 or later 2.1 versions).</span></span>

```xml
<PackageReference Include="Microsoft.Net.Http.Headers" Version="2.1.14" />
<PackageReference Include="Microsoft.AspNetCore.CookiePolicy" Version="2.1.14" />
```

### <a name="more-information"></a><span data-ttu-id="b5d16-135">Informations complémentaires</span><span class="sxs-lookup"><span data-stu-id="b5d16-135">More Information</span></span>
 
<span data-ttu-id="b5d16-136">[Mises à jour chrome](https://www.chromium.org/updates/same-site) 
 [Documentation](../samesite.md?view=aspnetcore-2.1) 
 ASP.net Core SameSite [Annonce de modification de ASP.NET Core 2,1 SameSite](https://github.com/dotnet/aspnetcore/issues/8212)</span><span class="sxs-lookup"><span data-stu-id="b5d16-136">[Chrome Updates](https://www.chromium.org/updates/same-site)
[ASP.NET Core SameSite Documentation](../samesite.md?view=aspnetcore-2.1)
[ASP.NET Core 2.1 SameSite Change Announcement](https://github.com/dotnet/aspnetcore/issues/8212)</span></span>