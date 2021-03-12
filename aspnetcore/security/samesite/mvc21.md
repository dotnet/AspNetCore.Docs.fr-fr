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
# <a name="aspnet-core-21-mvc-samesite-cookie-sample"></a>Exemple de SameSite ASP.NET Core MVC 2,1 cookie

ASP.NET Core 2,1 dispose d’une prise en charge intégrée de l’attribut [SameSite](https://www.owasp.org/index.php/SameSite) , mais il a été écrit dans la norme d’origine. Le [comportement corrigé](https://github.com/dotnet/aspnetcore/issues/8212) a modifié la signification de `SameSite.None` pour émettre l’attribut sameSite avec une valeur de `None` , au lieu de ne pas émettre la valeur du tout. Si vous ne souhaitez pas émettre la valeur, vous pouvez définir la `SameSite` propriété sur cookie -1.

[!INCLUDE[](~/includes/SameSiteIdentity.md)]

## <a name="writing-the-samesite-attribute"></a><a name="sampleCode"></a>Écriture de l’attribut SameSite

Voici un exemple d’écriture d’un attribut SameSite sur un cookie :

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

## <a name="setting-cookie-authentication-and-session-state-cookies"></a>Définition Cookie des États d’authentification et de session cookie

Cookie l’authentification, l’état de session et [divers autres composants](../samesite.md?view=aspnetcore-2.1) définissent leurs options sameSite via Cookie des options, par exemple

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

Dans le code précédent, cookie l’authentification et l’état de session définissent l’attribut sameSite sur `None` , en émettant l’attribut avec une `None` valeur, et attribuent également la valeur true à l’attribut Secure.

### <a name="run-the-sample"></a>Exécution de l'exemple

Si vous exécutez l' [exemple de projet](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC), chargez votre débogueur de navigateur sur la page initiale et utilisez-le pour afficher le cookie regroupement du site. Pour ce faire, dans Edge et chrome, appuyez sur l' `F12` `Application` onglet, puis cliquez sur l’URL du site sous l' `Cookies` option dans la `Storage` section.

![Débogueur de navigateur ::: No-Loc (cookie) ::: List](BrowserDebugger.png)

Vous pouvez voir à partir de l’image ci-dessus que le cookie créé par l’exemple lorsque vous cliquez sur le bouton « Create SameSite Cookie » a une valeur d’attribut SameSite égale `Lax` à, qui correspond à la valeur définie dans l' [exemple de code](#sampleCode).

## <a name="intercepting-cookies"></a><a name="interception"></a>Interception des cookie s

Afin d’intercepter des cookie s, pour ajuster la valeur None en fonction de sa prise en charge dans l’agent Browser de l’utilisateur, vous devez utiliser l' `CookiePolicy` intergiciel (middleware). Celui-ci doit être placé dans le pipeline de requête HTTP **avant** tous les composants qui écrivent des cookie et configurés dans `ConfigureServices()` .

Pour l’insérer dans le pipeline `app.UseCookiePolicy()` , utilisez la `Configure(IApplicationBuilder, IHostingEnvironment)` méthode dans [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs). Par exemple :

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

Ensuite, dans la, `ConfigureServices(IServiceCollection services)` configurez la cookie stratégie pour appeler une classe d’assistance lorsque les cookie s sont ajoutés ou supprimés. Par exemple :

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

Fonction d’assistance `CheckSameSite(HttpContext, CookieOptions)` :

* Est appelé quand les cookie s sont ajoutés à la demande ou supprimés de la requête.
* Vérifie si la `SameSite` propriété a la valeur `None` .
* Si `SameSite` a la valeur `None` et que l’agent utilisateur actuel est connu pour ne pas prendre en charge la valeur d’attribut None. La vérification s’effectue à l’aide de la classe [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) :
  * Définit `SameSite` pour ne pas émettre la valeur en affectant à la propriété la valeur `(SameSiteMode)(-1)`

## <a name="targeting-net-framework"></a>Ciblage .NET Framework

ASP.NET Core et System. Web (ASP.NET 4. x) ont des implémentations indépendantes de SameSite. Les correctifs de SameSite KB pour .NET Framework ne sont pas requis si vous utilisez ASP.NET Core ni la version minimale requise de l’infrastructure System. Web SameSite (.NET Framework 4.7.2) s’appliquent à ASP.NET Core.

ASP.NET Core sur .NET requiert la mise à jour des dépendances de package NuGet pour récupérer les correctifs appropriés.

Pour obtenir les modifications de ASP.NET Core pour .NET Framework Vérifiez que vous disposez d’une référence directe aux packages et versions corrigés (2.1.14 ou versions ultérieures 2,1).

```xml
<PackageReference Include="Microsoft.Net.Http.Headers" Version="2.1.14" />
<PackageReference Include="Microsoft.AspNetCore.CookiePolicy" Version="2.1.14" />
```

### <a name="more-information"></a>Informations complémentaires
 
[Mises à jour chrome](https://www.chromium.org/updates/same-site) 
 [Documentation](../samesite.md?view=aspnetcore-2.1) 
 ASP.net Core SameSite [Annonce de modification de ASP.NET Core 2,1 SameSite](https://github.com/dotnet/aspnetcore/issues/8212)