---
title: Empêcher les attaques de falsification de requête intersites (XSRF/CSRF) dans ASP.NET Core
author: steve-smith
description: Découvrez comment empêcher les attaques contre les applications Web où un site Web malveillant peut influencer l’interaction entre un navigateur client et l’application.
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 12/05/2019
no-loc:
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
uid: security/anti-request-forgery
ms.openlocfilehash: 201ffe692c1ded3661a5e1ac566f90b29d61ce9e
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690346"
---
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a>Empêcher les attaques de falsification de requête intersites (XSRF/CSRF) dans ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan)et [Steve Smith](https://ardalis.com/)

La falsification de requête intersite (également appelée XSRF ou CSRF) est une attaque contre les applications hébergées sur le Web, dans laquelle une application Web malveillante peut influencer l’interaction entre un navigateur client et une application Web qui approuve ce navigateur. Ces attaques sont possibles, car les navigateurs Web envoient automatiquement certains types de jetons d’authentification à chaque demande adressée à un site Web. Cette forme d’exploitation est également connue sous le nom d' *attaque en un clic* ou d’une *session* , car l’attaque tire parti de la session précédemment authentifiée de l’utilisateur.

Exemple d’attaque CSRF :

1. Un utilisateur se connecte `www.good-banking-site.com` à à l’aide de l’authentification par formulaire. Le serveur authentifie l’utilisateur et émet une réponse qui comprend une authentification cookie . Le site est vulnérable aux attaques, car il approuve les demandes qu’il reçoit avec une authentification valide cookie .
1. L’utilisateur visite un site malveillant, `www.bad-crook-site.com` .

   Le site malveillant, `www.bad-crook-site.com` , contient un formulaire HTML semblable au suivant :

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   Notez que le formulaire est `action` publié sur le site vulnérable, et non sur le site malveillant. Il s’agit de la partie « inter-sites » de CSRF.

1. L’utilisateur sélectionne le bouton Envoyer. Le navigateur effectue la demande et intègre automatiquement l’authentification cookie pour le domaine demandé, `www.good-banking-site.com` .
1. La demande s’exécute sur le `www.good-banking-site.com` serveur avec le contexte d’authentification de l’utilisateur et peut effectuer toute action qu’un utilisateur authentifié est autorisé à effectuer.

En plus du scénario dans lequel l’utilisateur sélectionne le bouton pour envoyer le formulaire, le site malveillant peut :

* Exécutez un script qui envoie automatiquement le formulaire.
* Envoyer l’envoi de formulaire en tant que requête AJAX.
* Masquez le formulaire en utilisant CSS.

Ces scénarios alternatifs ne nécessitent aucune action ou entrée de la part de l’utilisateur autre que la visite initiale du site malveillant.

L’utilisation de HTTPs n’empêche pas une attaque CSRF. Le site malveillant peut envoyer une `https://www.good-banking-site.com/` demande tout aussi facilement qu’il peut envoyer une demande non sécurisée.

Certaines attaques ciblent des points de terminaison qui répondent aux demandes d’extraction, auquel cas une balise d’image peut être utilisée pour effectuer l’action. Cette forme d’attaque est courante sur les sites de forum qui autorisent les images, mais qui bloquent JavaScript. Les applications qui changent d’État sur les demandes d’extraction, où les variables ou les ressources sont modifiées, sont vulnérables aux attaques malveillantes. **Demandes d’obtention dont l’état de modification n’est pas sécurisé. Une meilleure pratique consiste à ne jamais modifier l’état d’une requête d’extraction.**

Les attaques CSRF sont possibles pour les applications Web qui utilisent cookie des s pour l’authentification, car :

* Magasins de navigateurs cookie émis par une application Web.
* cookieLes s stockées incluent cookie des sessions pour les utilisateurs authentifiés.
* Les navigateurs envoient tous les objets cookie associés à un domaine à l’application Web à chaque demande, quelle que soit la façon dont la demande à l’application a été générée dans le navigateur.

Toutefois, les attaques CSRF ne sont pas limitées à l’exploitation de cookie . Par exemple, l’authentification de base et Digest est également vulnérable. Une fois qu’un utilisateur se connecte avec l’authentification de base ou Digest, le navigateur envoie automatiquement les informations d’identification jusqu’à la fin de la session &dagger; .

&dagger;Dans ce contexte, la *session* fait référence à la session côté client au cours de laquelle l’utilisateur est authentifié. Elle n’est pas liée aux sessions côté serveur ou aux intergiciels (middleware) de [Session ASP.net Core](xref:fundamentals/app-state).

Les utilisateurs peuvent se protéger contre les vulnérabilités de CSRF en acceptant les précautions suivantes :

* Déconnectez-vous de Web Apps une fois que vous avez fini de les utiliser.
* Effacez cookie régulièrement le navigateur.

Toutefois, les vulnérabilités CSRF sont fondamentalement un problème avec l’application Web, et non l’utilisateur final.

## <a name="authentication-fundamentals"></a>Concepts de base de l’authentification

Cookiel’authentification basée sur est une forme d’authentification courante. Les systèmes d’authentification basés sur les jetons augmentent en popularité, en particulier pour les applications à page unique (SPAs).

### <a name="no-loccookie-based-authentication"></a>Cookieauthentification basée sur

Lorsqu’un utilisateur s’authentifie à l’aide de son nom d’utilisateur et de son mot de passe, il reçoit un jeton qui contient un ticket d’authentification pouvant être utilisé pour l’authentification et l’autorisation. Le jeton est stocké en tant cookie que qui accompagne chaque demande que le client effectue. La génération et la validation de cette opération cookie sont effectuées par l' Cookie intergiciel (middleware) d’authentification. L' [intergiciel](xref:fundamentals/middleware/index) sérialise un principal d’utilisateur dans un chiffrement cookie . Lors des demandes suivantes, l’intergiciel valide le cookie , recrée le principal et attribue le principal à la propriété [User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) de [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).

### <a name="token-based-authentication"></a>Authentification basée sur un jeton

Lorsqu’un utilisateur est authentifié, il reçoit un jeton (et non un jeton anti-contrefaçon). Le jeton contient des informations utilisateur sous la forme de [revendications](/dotnet/framework/security/claims-based-identity-model) ou d’un jeton de référence qui fait pointer l’application vers l’état utilisateur géré dans l’application. Lorsqu’un utilisateur tente d’accéder à une ressource nécessitant une authentification, le jeton est envoyé à l’application avec un en-tête d’autorisation supplémentaire sous forme de jeton du porteur. Cela rend l’application sans État. Dans chaque requête suivante, le jeton est transmis dans la demande de validation côté serveur. Ce jeton n’est pas *chiffré* . elle est *encodée* . Sur le serveur, le jeton est décodé pour accéder à ses informations. Pour envoyer le jeton sur les demandes suivantes, stockez le jeton dans le stockage local du navigateur. Ne vous inquiétez pas de la vulnérabilité CSRF si le jeton est stocké dans le stockage local du navigateur. CSRF est un problème lorsque le jeton est stocké dans un cookie . Pour plus d’informations, consultez l’exemple de code GitHub problème [Spa ajoute deux cookie s](https://github.com/dotnet/AspNetCore.Docs/issues/13369).

### <a name="multiple-apps-hosted-at-one-domain"></a>Plusieurs applications hébergées sur un domaine

Les environnements d’hébergement partagés sont vulnérables au détournement de session, à la CSRF de connexion et à d’autres attaques.

Bien que `example1.contoso.net` et `example2.contoso.net` soient des hôtes différents, il existe une relation d’approbation implicite entre les hôtes sous le `*.contoso.net` domaine. Cette relation d’approbation implicite permet aux hôtes potentiellement non approuvés d’affecter les cookie s (les stratégies de même origine qui régissent les demandes Ajax ne s’appliquent pas nécessairement à http cookie s).

Les attaques qui exploitent cookie des s de confiance entre des applications hébergées sur le même domaine peuvent être évitées en ne partageant pas de domaines. Lorsque chaque application est hébergée sur son propre domaine, il n’existe aucune cookie relation d’approbation implicite à exploiter.

## <a name="aspnet-core-antiforgery-configuration"></a>Configuration de l’anti-contrefaçon ASP.NET Core

> [!WARNING]
> ASP.NET Core implémente l’anti-falsification à l’aide de la [protection des données ASP.net Core](xref:security/data-protection/introduction). La pile de protection des données doit être configurée pour fonctionner dans une batterie de serveurs. Pour plus d’informations, consultez Configuration de la [protection des données](xref:security/data-protection/configuration/overview) .

::: moniker range=">= aspnetcore-3.0"

L’intergiciel anti-contrefaçon est ajouté au conteneur d' [injection de dépendances](xref:fundamentals/dependency-injection) lorsque l’une des API suivantes est appelée dans `Startup.ConfigureServices` :

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>
* <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*>
* <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>
* <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Un intergiciel anti-contrefaçon est ajouté au conteneur d' [injection de dépendances](xref:fundamentals/dependency-injection) lorsque <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> est appelé dans `Startup.ConfigureServices`

::: moniker-end

Dans ASP.NET Core 2,0 ou version ultérieure, [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injecte des jetons anti-contrefaçon dans des éléments de formulaire HTML. Le balisage suivant dans un Razor fichier génère automatiquement des jetons anti-contrefaçon :

```cshtml
<form method="post">
    ...
</form>
```

De même, [IHtmlHelper. BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) génère des jetons anti-contrefaçon par défaut si la méthode du formulaire n’est pas obtenue.

La génération automatique de jetons anti-contrefaçon pour les éléments de formulaire HTML se produit lorsque la `<form>` balise contient l' `method="post"` attribut et que l’une des conditions suivantes est vraie :

* L’attribut action est vide ( `action=""` ).
* L’attribut action n’est pas fourni ( `<form method="post">` ).

La génération automatique de jetons anti-contrefaçon pour les éléments de formulaire HTML peut être désactivée :

* Désactivez explicitement les jetons anti-contrefaçon avec l' `asp-antiforgery` attribut :

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* L’élément de formulaire est exclu du balisage tag à l’aide du tag Helper [! symbol opt-out](xref:mvc/views/tag-helpers/intro#opt-out):

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* Supprimez `FormTagHelper` de la vue. Le `FormTagHelper` peut être supprimé d’une vue en ajoutant la directive suivante à la Razor vue :

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> Les [ Razor pages](xref:razor-pages/index) sont automatiquement protégées à partir de XSRF/CSRF. Pour plus d’informations, consultez [XSRF/CSRF et Razor pages](xref:razor-pages/index#xsrf).

L’approche la plus courante pour la protection contre les attaques CSRF consiste à utiliser le *modèle de jeton du synchronisateur* (STP). Le protocole STP est utilisé lorsque l’utilisateur demande une page avec des données de formulaire :

1. Le serveur envoie un jeton associé à l’identité de l’utilisateur actuel au client.
1. Le client renvoie le jeton au serveur pour vérification.
1. Si le serveur reçoit un jeton qui ne correspond pas à l’identité de l’utilisateur authentifié, la demande est rejetée.

Le jeton est unique et imprévisible. Le jeton peut également être utilisé pour garantir le séquencement correct d’une série de requêtes (par exemple, en vérifiant la séquence de demande de : page 1 > page 2 > page 3). Tous les formulaires dans ASP.NET Core modèles MVC et Razor pages génèrent des jetons anti-contrefaçon. Les deux exemples de vue suivants génèrent des jetons anti-contrefaçon :

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

Ajoutez explicitement un jeton anti-contrefaçon à un `<form>` élément sans utiliser de balise tag avec le programme d’assistance HTML [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken) :

```cshtml
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

Dans chacun des cas précédents, ASP.NET Core ajoute un champ de formulaire masqué semblable au suivant :

```cshtml
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkS ... s2-m9Yw">
```

ASP.NET Core comprend trois [filtres](xref:mvc/controllers/filters) pour l’utilisation des jetons anti-contrefaçon :

* [ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)
* [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)
* [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)

## <a name="antiforgery-options"></a>Options anti-contrefaçon

Personnaliser les options anti- [contrefaçon](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) dans `Startup.ConfigureServices` :

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    // Set Cookie properties using CookieBuilder properties†.
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.SuppressXFrameOptionsHeader = false;
});
```

&dagger;Définissez les propriétés anti-contrefaçon `Cookie` à l’aide des propriétés de la classe de [ Cookie Générateur](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) .

| Option | Description |
| ------ | ----------- |
| [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Détermine les paramètres utilisés pour créer les anti-falsifications cookie . |
| [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | Nom du champ de formulaire masqué utilisé par le système anti-contrefaçon pour le rendu des jetons anti-contrefaçon dans les vues. |
| [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | Nom de l’en-tête utilisé par le système anti-contrefaçon. Si `null` la condition est, le système considère uniquement les données de formulaire. |
| [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Spécifie s’il faut supprimer la génération de l' `X-Frame-Options` en-tête. Par défaut, l’en-tête est généré avec la valeur « SAMEORIGIN ». La valeur par défaut est `false`. |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    options.CookieDomain = "contoso.com";
    options.CookieName = "X-CSRF-TOKEN-COOKIENAME";
    options.CookiePath = "Path";
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.RequireSsl = false;
    options.SuppressXFrameOptionsHeader = false;
});
```

| Option | Description |
| ------ | ----------- |
| [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Détermine les paramètres utilisés pour créer les anti-falsifications cookie . |
| [CookieDomain](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) | Domaine du cookie . La valeur par défaut est `null`. Cette propriété est obsolète et sera supprimée dans une version ultérieure. L’alternative recommandée est Cookie . Domain. |
| [CookieNomme](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) | Nom de l'objet cookie. Si la valeur n’est pas définie, le système génère un nom unique commençant par le [ Cookie préfixe par défaut](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) («». AspNetCore. anti-contrefaçon.»). Cette propriété est obsolète et sera supprimée dans une version ultérieure. L’alternative recommandée est Cookie . Nomme. |
| [CookieChemin d’accès](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) | Chemin d’accès défini sur le cookie . Cette propriété est obsolète et sera supprimée dans une version ultérieure. L’alternative recommandée est Cookie . D. |
| [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | Nom du champ de formulaire masqué utilisé par le système anti-contrefaçon pour le rendu des jetons anti-contrefaçon dans les vues. |
| [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | Nom de l’en-tête utilisé par le système anti-contrefaçon. Si `null` la condition est, le système considère uniquement les données de formulaire. |
| [RequireSsl](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | Spécifie si le système anti-contrefaçon exige le protocole HTTPs. Si la `true` , les demandes non-HTTPS échouent. La valeur par défaut est `false`. Cette propriété est obsolète et sera supprimée dans une version ultérieure. L’alternative recommandée consiste à définir Cookie . SecurePolicy. |
| [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Spécifie s’il faut supprimer la génération de l' `X-Frame-Options` en-tête. Par défaut, l’en-tête est généré avec la valeur « SAMEORIGIN ». La valeur par défaut est `false`. |

::: moniker-end

Pour plus d’informations, consultez [ Cookie AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).

## <a name="configure-antiforgery-features-with-iantiforgery"></a>Configurer des fonctionnalités anti-contrefaçon avec IAntiforgery

[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) fournit l’API pour configurer les fonctionnalités anti-contrefaçon. `IAntiforgery` peut être demandé dans la `Configure` méthode de la `Startup` classe. L’exemple suivant utilise l’intergiciel (middleware) de la page d’hébergement de l’application pour générer un jeton anti-contrefaçon et l’envoyer dans la réponse en tant que cookie (à l’aide de la Convention d’affectation de noms angulaire par défaut décrite plus loin dans cette rubrique) :

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}
```

### <a name="require-antiforgery-validation"></a>Exiger une validation anti-contrefaçon

[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) est un filtre d’action qui peut être appliqué à une action individuelle, à un contrôleur ou globalement. Les demandes adressées aux actions auxquelles ce filtre est appliqué sont bloquées, sauf si la demande comprend un jeton anti-contrefaçon valide.

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> RemoveLogin(RemoveLoginViewModel account)
{
    ManageMessageId? message = ManageMessageId.Error;
    var user = await GetCurrentUserAsync();

    if (user != null)
    {
        var result = 
            await _userManager.RemoveLoginAsync(
                user, account.LoginProvider, account.ProviderKey);

        if (result.Succeeded)
        {
            await _signInManager.SignInAsync(user, isPersistent: false);
            message = ManageMessageId.RemoveLoginSuccess;
        }
    }

    return RedirectToAction(nameof(ManageLogins), new { Message = message });
}
```

L' `ValidateAntiForgeryToken` attribut requiert un jeton pour les demandes aux méthodes d’action qu’il marque, y compris les requêtes http d’extraction. Si l' `ValidateAntiForgeryToken` attribut est appliqué sur les contrôleurs de l’application, il peut être substitué par `IgnoreAntiforgeryToken` l’attribut.

> [!NOTE]
> ASP.NET Core ne prend pas en charge l’ajout de jetons anti-contrefaçon pour la récupération automatique des demandes.

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a>Valider automatiquement les jetons anti-contrefaçon pour les méthodes HTTP non sécurisées uniquement

ASP.NET Core applications ne génèrent pas de jetons anti-contrefaçon pour les méthodes HTTP sécurisées (obtenir, tête, OPTIONS et TRACE). Au lieu d’appliquer globalement l' `ValidateAntiForgeryToken` attribut, puis de le remplacer par des `IgnoreAntiforgeryToken` attributs, l’attribut [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) peut être utilisé. Cet attribut fonctionne de la même façon `ValidateAntiForgeryToken` que l’attribut, sauf qu’il ne requiert pas de jetons pour les demandes effectuées à l’aide des méthodes http suivantes :

* GET
* TÊTE
* OPTIONS
* TRACE

Nous vous recommandons d’utiliser `AutoValidateAntiforgeryToken` large pour les scénarios non-API. Cela garantit que les actions de publication sont protégées par défaut. L’alternative consiste à ignorer les jetons anti-contrefaçon par défaut, sauf si `ValidateAntiForgeryToken` est appliqué à des méthodes d’action individuelles. Dans ce scénario, il est plus probable qu’une méthode d’action de publication reste non protégée par erreur, laissant l’application vulnérable aux attaques CSRF. Toutes les publications doivent envoyer le jeton anti-contrefaçon.

Les API ne disposent pas d’un mécanisme automatique pour l’envoi de l' cookie élément ne faisant pas partie du jeton. L’implémentation dépend probablement de l’implémentation du code client. Voici quelques exemples :

Exemple de niveau de classe :

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

Exemple global :

::: moniker range="< aspnetcore-3.0"

services. AddMvc (options = options de>. Filters. Add (New AutoValidateAntiforgeryTokenAttribute ()));

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

```csharp
services.AddControllersWithViews(options =>
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

::: moniker-end

### <a name="override-global-or-controller-antiforgery-attributes"></a>Remplacer les attributs globaux ou anti-contrefaçon de contrôleur

Le filtre [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) est utilisé pour éliminer la nécessité d’un jeton anti-contrefaçon pour une action donnée (ou un contrôleur). Lorsqu’il est appliqué, ce filtre remplace `ValidateAntiForgeryToken` les `AutoValidateAntiforgeryToken` filtres et spécifiés à un niveau supérieur (globalement ou sur un contrôleur).

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
    [HttpPost]
    [IgnoreAntiforgeryToken]
    public async Task<IActionResult> DoSomethingSafe(SomeViewModel model)
    {
        // no antiforgery token required
    }
}
```

## <a name="refresh-tokens-after-authentication"></a>Actualiser les jetons après l’authentification

Les jetons doivent être actualisés une fois l’utilisateur authentifié, en redirigeant l’utilisateur vers une page d’affichage ou de Razor pages.

## <a name="javascript-ajax-and-spas"></a>JavaScript, AJAX et SPAs

Dans les applications HTML traditionnelles, les jetons anti-contrefaçon sont transmis au serveur à l’aide de champs de formulaire masqués. Dans les applications basées sur JavaScript modernes et en SPAs, de nombreuses demandes sont effectuées par programme. Ces demandes AJAX peuvent utiliser d’autres techniques (comme les en-têtes ou les en-têtes cookie de demande) pour envoyer le jeton.

Si cookie des s sont utilisés pour stocker des jetons d’authentification et pour authentifier les demandes d’API sur le serveur, CSRF est un problème potentiel. Si le stockage local est utilisé pour stocker le jeton, la vulnérabilité CSRF peut être atténuée car les valeurs du stockage local ne sont pas envoyées automatiquement au serveur avec chaque demande. Par conséquent, l’utilisation du stockage local pour stocker le jeton anti-contrefaçon sur le client et l’envoi du jeton en tant qu’en-tête de demande est une approche recommandée.

### <a name="javascript"></a>JavaScript

À l’aide de JavaScript avec des vues, le jeton peut être créé à l’aide d’un service à partir de la vue. Injectez le service [Microsoft. AspNetCore. anticontrefaçon. IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) dans la vue et appelez [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):

[!code-cshtml[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

Cette approche élimine la nécessité de traiter directement les paramètres cookie à partir du serveur ou de les lire à partir du client.

L’exemple précédent utilise JavaScript pour lire la valeur du champ masqué pour l’en-tête de publication AJAX.

JavaScript peut également accéder à des jetons dans cookie s et utiliser le cookie contenu de pour créer un en-tête avec la valeur du jeton.

```csharp
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

En supposant que le script demande à envoyer le jeton dans un en-tête appelé `X-CSRF-TOKEN` , configurez le service anti-contrefaçon pour Rechercher l' `X-CSRF-TOKEN` en-tête :

```csharp
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

L’exemple suivant utilise JavaScript pour effectuer une demande AJAX avec l’en-tête approprié :

```javascript
function getCookie(cname) {
    var name = cname + "=";
    var decodedCookie = decodeURIComponent(document.cookie);
    var ca = decodedCookie.split(';');
    for(var i = 0; i <ca.length; i++) {
        var c = ca[i];
        while (c.charAt(0) == ' ') {
            c = c.substring(1);
        }
        if (c.indexOf(name) == 0) {
            return c.substring(name.length, c.length);
        }
    }
    return "";
}

var csrfToken = getCookie("CSRF-TOKEN");

var xhttp = new XMLHttpRequest();
xhttp.onreadystatechange = function() {
    if (xhttp.readyState == XMLHttpRequest.DONE) {
        if (xhttp.status == 200) {
            alert(xhttp.responseText);
        } else {
            alert('There was an error processing the AJAX request.');
        }
    }
};
xhttp.open('POST', '/api/password/changepassword', true);
xhttp.setRequestHeader("Content-type", "application/json");
xhttp.setRequestHeader("X-CSRF-TOKEN", csrfToken);
xhttp.send(JSON.stringify({ "newPassword": "ReallySecurePassword999$$$" }));
```

### <a name="angularjs"></a>AngularJS

AngularJS utilise une convention pour traiter CSRF. Si le serveur envoie un cookie avec le nom `XSRF-TOKEN` , le `$http` service AngularJS ajoute la cookie valeur à un en-tête lorsqu’il envoie une demande au serveur. Ce processus est automatique. L’en-tête n’a pas besoin d’être défini explicitement dans le client. Le nom d’en-tête est `X-XSRF-TOKEN` . Le serveur doit détecter cet en-tête et valider son contenu.

Pour que ASP.NET Core API fonctionne avec cette Convention au démarrage de votre application :

* Configurez votre application pour fournir un jeton dans une cookie appelée `XSRF-TOKEN` .
* Configurez le service anti-contrefaçon pour rechercher un en-tête nommé `X-XSRF-TOKEN` .

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}

public void ConfigureServices(IServiceCollection services)
{
    // Angular's default header name for sending the XSRF token.
    services.AddAntiforgery(options => options.HeaderName = "X-XSRF-TOKEN");
}
```

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="extend-antiforgery"></a>Étendre l’anti-contrefaçon

Le type [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) permet aux développeurs d’étendre le comportement du système anti-CSRF en arrondissant les données supplémentaires dans chaque jeton. La méthode [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) est appelée chaque fois qu’un jeton de champ est généré, et la valeur de retour est incorporée dans le jeton généré. Un responsable de l’implémentation peut retourner un horodateur, une valeur à usage unique ou toute autre valeur, puis appeler [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) pour valider ces données lors de la validation du jeton. Le nom d’utilisateur du client étant déjà incorporé dans les jetons générés, il n’est pas nécessaire d’inclure ces informations. Si un jeton contient des données supplémentaires, mais qu’aucun `IAntiForgeryAdditionalDataProvider` n’est configuré, les données supplémentaires ne sont pas validées.

## <a name="additional-resources"></a>Ressources supplémentaires

* [CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) sur le projet OWASP ( [Open Web Application Security](https://www.owasp.org/index.php/Main_Page) ).
* <xref:host-and-deploy/web-farm>
