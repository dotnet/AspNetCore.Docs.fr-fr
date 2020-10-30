---
title: Empêcher les attaques par redirection ouverte dans ASP.NET Core
author: ardalis
description: Montre comment empêcher les attaques de redirection ouvertes contre une application ASP.NET Core
ms.author: riande
ms.date: 07/07/2017
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
uid: security/preventing-open-redirects
ms.openlocfilehash: e546cd852367921c7c694db3639f7a233f606e75
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058387"
---
# <a name="prevent-open-redirect-attacks-in-aspnet-core"></a>Empêcher les attaques par redirection ouverte dans ASP.NET Core

Une application Web qui effectue une redirection vers une URL spécifiée via la requête telle que la chaîne de requête ou les données de formulaire peut potentiellement être falsifiée pour rediriger les utilisateurs vers une URL externe et malveillante. Cette falsification s’appelle une attaque de redirection ouverte.

Chaque fois que votre logique d’application redirige vers une URL spécifiée, vous devez vérifier que l’URL de redirection n’a pas été falsifiée. ASP.NET Core offre des fonctionnalités intégrées permettant de protéger les applications contre les attaques par redirection ouverte (également appelées « redirections ouvertes »).

## <a name="what-is-an-open-redirect-attack"></a>Qu’est-ce qu’une attaque de redirection ouverte ?

Les applications Web redirigent fréquemment les utilisateurs vers une page de connexion quand ils accèdent aux ressources qui requièrent une authentification. La redirection comprend généralement un `returnUrl` paramètre QueryString afin que l’utilisateur puisse être renvoyé à l’URL demandée à l’origine une fois qu’il a réussi à se connecter. Une fois que l’utilisateur s’est authentifié, il est redirigé vers l’URL demandée à l’origine.

Étant donné que l’URL de destination est spécifiée dans la chaîne de requête de la demande, un utilisateur malveillant peut falsifier la chaîne de requête. Une QueryString falsifiée peut permettre au site de rediriger l’utilisateur vers un site externe malveillant. Cette technique s’appelle une attaque de redirection (ou de redirection) ouverte.

### <a name="an-example-attack"></a>Exemple d’attaque

Un utilisateur malveillant peut développer une attaque destinée à autoriser l’utilisateur malveillant à accéder aux informations d’identification d’un utilisateur ou à des informations sensibles. Pour lancer l’attaque, l’utilisateur malveillant persuade l’utilisateur de cliquer sur un lien vers la page de connexion de votre site avec une `returnUrl` valeur QueryString ajoutée à l’URL. Par exemple, considérez une application sur `contoso.com` qui comprend une page de connexion à l’adresse `http://contoso.com/Account/LogOn?returnUrl=/Home/About` . L’attaque suit les étapes suivantes :

1. L’utilisateur clique sur un lien malveillant vers `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (la deuxième URL est « contoso **1** . com », et non pas « contoso.com »).
2. L’utilisateur se connecte avec succès.
3. L’utilisateur est redirigé (par le site) vers `http://contoso1.com/Account/LogOn` (un site malveillant qui ressemble exactement à un site réel).
4. L’utilisateur se reconnecte (ce qui donne des informations d’identification au site malveillant) et est redirigé vers le site réel.

L’utilisateur pense probablement que sa première tentative de connexion a échoué et que la deuxième tentative est réussie. L’utilisateur n’est probablement pas conscient que leurs informations d’identification sont compromises.

![Processus d’attaque de redirection ouvert](preventing-open-redirects/_static/open-redirection-attack-process.png)

En plus des pages de connexion, certains sites fournissent des pages de redirection ou des points de terminaison. Imaginez que votre application comporte une page avec une redirection ouverte, `/Home/Redirect` . Un attaquant pourrait créer, par exemple, un lien dans un message électronique qui accède à `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login` . Un utilisateur classique regardera l’URL pour voir qu’elle commence par le nom de votre site. Pour ce faire, ils cliquent sur le lien. La redirection ouverte envoie ensuite l’utilisateur au site de hameçonnage, qui ressemble à la vôtre, et l’utilisateur se connecte probablement à ce que votre site estime être.

## <a name="protecting-against-open-redirect-attacks"></a>Protection contre les attaques par redirection ouverte

Lors du développement d’applications Web, traitez toutes les données fournies par l’utilisateur comme non fiables. Si votre application a une fonctionnalité qui redirige l’utilisateur en fonction du contenu de l’URL, assurez-vous que ces redirections sont effectuées uniquement localement dans votre application (ou sur une URL connue, et non dans une URL qui peut être fournie dans la chaîne de chaîne).

### <a name="localredirect"></a>LocalRedirect

Utilisez la `LocalRedirect` méthode d’assistance de la classe de base `Controller` :

```csharp
public IActionResult SomeAction(string redirectUrl)
{
    return LocalRedirect(redirectUrl);
}
```

`LocalRedirect` lèvera une exception si une URL non locale est spécifiée. Dans le cas contraire, il se comporte comme la `Redirect` méthode.

### <a name="islocalurl"></a>IsLocalUrl

Utilisez la méthode [IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) pour tester des URL avant de rediriger :

L’exemple suivant montre comment vérifier si une URL est locale avant la redirection.

```csharp
private IActionResult RedirectToLocal(string returnUrl)
{
    if (Url.IsLocalUrl(returnUrl))
    {
        return Redirect(returnUrl);
    }
    else
    {
        return RedirectToAction(nameof(HomeController.Index), "Home");
    }
}
```

La `IsLocalUrl` méthode empêche les utilisateurs d’être redirigés par inadvertance vers un site malveillant. Vous pouvez consigner les détails de l’URL qui a été fournie quand une URL non locale est fournie dans une situation où vous attendiez une URL locale. Les URL de redirection de la journalisation peuvent aider à diagnostiquer les attaques de redirection.
