---
title: Génération Identity de modèles automatique dans les projets ASP.net Core
author: rick-anderson
description: Découvrez comment générer Identity une structure dans un projet ASP.net core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
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
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 813dd7837c265c78c584d66dd51bc23399d12fbe
ms.sourcegitcommit: 5156eab2118584405eb663e1fcd82f8bd7764504
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/31/2020
ms.locfileid: "93141493"
---
# <a name="scaffold-no-locidentity-in-aspnet-core-projects"></a>Génération Identity de modèles automatique dans les projets ASP.net Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core fournit [ASP.NET Core Identity](xref:security/authentication/identity) comme [ Razor bibliothèque de classes](xref:razor-pages/ui-class). Les applications qui incluent Identity peuvent appliquer l’échafaudage pour ajouter de manière sélective le code source contenu dans la Identity Razor bibliothèque de classes (RCL). Vous pouvez souhaiter générer le code source afin de pouvoir modifier le code et changer le comportement. Par exemple, vous pouvez demander au générateur de modèles automatique de générer le code utilisé dans l’inscription. Le code généré est prioritaire sur le même code dans le Identity RCL. Pour obtenir le contrôle total de l’interface utilisateur et ne pas utiliser le RCL par défaut, consultez la section [créer une Identity source d’interface utilisateur complète](#full).

Les applications qui n’incluent **pas** l’authentification peuvent appliquer l’échafaudage pour ajouter le Identity package RCL. Vous avez la possibilité de sélectionner du Identity code à générer.

Bien que le générateur de modèles génère la majeure partie du code nécessaire, vous devez mettre à jour votre projet pour terminer le processus. Ce document explique les étapes nécessaires à la réalisation d’une Identity mise à jour de génération de modèles automatique.

Nous vous recommandons d’utiliser un système de contrôle de code source qui affiche les différences entre les fichiers et vous permet d’annuler les modifications. Inspectez les modifications après avoir exécuté le générateur de Identity modèles.

Les services sont requis lorsque vous utilisez l' [authentification à deux facteurs](xref:security/authentication/identity-enable-qrcodes), la [confirmation de compte et la récupération de mot de passe](xref:security/authentication/accconfirm), ainsi que d’autres fonctionnalités de sécurité avec Identity . Les services ou stubs de service ne sont pas générés lors de la génération de modèles automatique Identity . Les services pour activer ces fonctionnalités doivent être ajoutés manuellement. Par exemple, consultez [exiger une confirmation par courrier électronique](xref:security/authentication/accconfirm#require-email-confirmation).

Lors de la génération de modèles automatique Identity avec un nouveau contexte de données dans un projet avec des comptes individuels existants :

* Dans `Startup.ConfigureServices` , supprimez les appels à :
  * `AddDbContext`
  * `AddDefaultIdentity`

Par exemple, `AddDbContext` et `AddDefaultIdentity` sont commentés dans le code suivant :

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

Le code précédent fait un commentaire sur le code qui est dupliqué dans *Areas/ Identity / Identity HostingStartup.cs*

En règle générale, les applications créées avec des comptes individuels ne doivent **pas** créer un nouveau contexte de données.

## <a name="scaffold-no-locidentity-into-an-empty-project"></a>Génération de modèles automatique Identity dans un projet vide

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Mettez à jour la `Startup` classe avec un code similaire à ce qui suit :

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a>Générer Identity une structure dans un Razor projet sans autorisation existante

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef database drop
dotnet ef migrations add CreateIdentitySchema0
dotnet ef database update
-->

<!-- ERROR
There is already an object named 'AspNetRoles' in the database.

Fixed via dotnet ef database drop
before dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identityest configuré dans _Areas/ Identity / Identity HostingStartup.cs *. Pour plus d’informations, consultez [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>Migrations, UseAuthentication et disposition

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>Activer l’authentification

Mettez à jour la `Startup` classe avec un code similaire à ce qui suit :

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>Modifications de la disposition

Facultatif : ajoutez la connexion partielle ( `_LoginPartial` ) au fichier de disposition :

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a>Génération de modèles automatique Identity dans un Razor projet avec autorisation

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

Certaines Identity options sont configurées dans *Areas/ Identity / Identity HostingStartup.cs* . Pour plus d’informations, consultez [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a>Génération de modèles automatique Identity dans un projet MVC sans autorisation existante

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Facultatif : ajoutez la connexion partielle ( `_LoginPartial` ) au fichier *Views/Shared/_Layout. cshtml* :

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* Déplacez le fichier *pages/Shared/_LoginPartial. cshtml* vers *views/shared/_LoginPartial. cshtml*

Identityest configuré dans *Areas/ Identity / Identity HostingStartup.cs* . Pour plus d’informations, consultez IHostingStartup.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

Mettez à jour la `Startup` classe avec un code similaire à ce qui suit :

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a>Génération Identity de modèles automatique dans un projet MVC avec autorisation

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-without-existing-authorization"></a>Générer Identity une structure dans un Blazor Server projet sans autorisation existante

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identityest configuré dans *Areas/ Identity / Identity HostingStartup.cs* . Pour plus d’informations, consultez [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

### <a name="migrations"></a>Migrations

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a>Passer un jeton XSRF à l’application

Les jetons peuvent être passés à des composants :

* Lorsque les jetons d’authentification sont approvisionnés et enregistrés dans l’authentification cookie , ils peuvent être passés à des composants.
* Razor les composants ne peuvent pas être utilisés `HttpContext` directement. il n’existe donc aucun moyen d’obtenir un [jeton XSRF (contrefaçon anti-request)](xref:security/anti-request-forgery) pour effectuer une publication sur le Identity point de terminaison de déconnexion de `/Identity/Account/Logout` . Un jeton XSRF peut être passé à des composants.

Pour plus d'informations, consultez <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.

Dans le fichier *pages/_Host. cshtml* , établissez le jeton après l’avoir ajouté `InitialApplicationState` aux `TokenProvider` classes et :

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

Mettez à jour le `App` composant ( *app. Razor* ) pour affecter le `InitialState.XsrfToken` :

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

Le `TokenProvider` service présenté dans la rubrique est utilisé dans le `LoginDisplay` composant dans la section [mise en page et changements de workflow d’authentification](#layout-and-authentication-flow-changes) suivants.

### <a name="enable-authentication"></a>Activer l’authentification

Dans la `Startup` classe :

* Confirmez que Razor les services de pages sont ajoutés dans `Startup.ConfigureServices` .
* Si vous utilisez [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), inscrivez le service.
* Appelez `UseDatabaseErrorPage` sur le générateur d’applications dans `Startup.Configure` pour l’environnement de développement.
* Appelez `UseAuthentication` et `UseAuthorization` after `UseRouting` .
* Ajoutez un point de terminaison pour les Razor pages.

[!code-csharp[](scaffold-identity/3.1sample/StartupBlazor.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a>Modifications de la disposition et du workflow d’authentification

Ajoutez un `RedirectToLogin` composant ( *RedirectToLogin. Razor* ) au dossier *partagé* de l’application dans la racine du projet :

```razor
@inject NavigationManager Navigation
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo("Identity/Account/Login?returnUrl=" +
            Uri.EscapeDataString(Navigation.Uri), true);
    }
}
```

Ajoutez un `LoginDisplay` composant ( *LoginDisplay. Razor* ) au dossier *partagé* de l’application. Le [service TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) fournit le jeton XSRF pour le formulaire HTML qui publie sur le Identity point de terminaison logout de déconnexion :

```razor
@using Microsoft.AspNetCore.Components.Authorization
@inject NavigationManager Navigation
@inject TokenProvider TokenProvider

<AuthorizeView>
    <Authorized>
        <a href="Identity/Account/Manage/Index">
            Hello, @context.User.Identity.Name!
        </a>
        <form action="/Identity/Account/Logout?returnUrl=%2F" method="post">
            <button class="nav-link btn btn-link" type="submit">Logout</button>
            <input name="__RequestVerificationToken" type="hidden" 
                value="@TokenProvider.XsrfToken">
        </form>
    </Authorized>
    <NotAuthorized>
        <a href="Identity/Account/Register">Register</a>
        <a href="Identity/Account/Login">Login</a>
    </NotAuthorized>
</AuthorizeView>
```

Dans le `MainLayout` composant ( *Shared/MainLayout. Razor* ), ajoutez le `LoginDisplay` composant au contenu de l’élément de ligne de plus haut niveau `<div>` :

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a>Points de terminaison d’authentification de style

Étant donné que Blazor Server utilise Razor Identity les pages pages, le style de l’interface utilisateur change lorsqu’un visiteur navigue entre Identity les pages et les composants. Vous avez deux options pour traiter les styles Incongruous :

#### <a name="build-no-locidentity-components"></a>Composants de build Identity

Une approche de l’utilisation de composants Identity au lieu de pages consiste à créer des Identity composants. Étant donné que `SignInManager` et `UserManager` ne sont pas pris en charge dans Razor les composants, utilisez les points de terminaison d’API dans l' Blazor Server application pour traiter les actions de compte d’utilisateur.

#### <a name="use-a-custom-layout-with-no-locblazor-app-styles"></a>Utiliser une disposition personnalisée avec les Blazor styles d’application

La Identity disposition et les styles des pages peuvent être modifiés pour produire des pages qui utilisent le thème par défaut Blazor .

> [!NOTE]
> L’exemple de cette section est simplement un point de départ pour la personnalisation. Un travail supplémentaire est probablement requis pour une expérience utilisateur optimale.

Créez un nouveau `NavMenu_IdentityLayout` composant ( *Shared/NavMenu_ Identity Layout. Razor* ). Pour le balisage et le code du composant, utilisez le même contenu du composant de l’application `NavMenu` ( *Shared/NavMenu. Razor* ). Supprimez les `NavLink` composants de tous les composants qui ne sont pas accessibles de façon anonyme, car les redirections automatiques du `RedirectToLogin` composant échouent pour les composants nécessitant une authentification ou une autorisation.

Dans le fichier *pages/Shared/Layout. cshtml* , apportez les modifications suivantes :

* Ajoutez Razor des directives en haut du fichier pour utiliser tag Helper et les composants de l’application dans le dossier *partagé* :

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  Remplacez `{APPLICATION ASSEMBLY}` par le nom de l’assembly de l’application.

* Ajoutez une `<base>` balise et une Blazor feuille `<link>` de style au `<head>` contenu :

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* Remplacez le contenu de la `<body>` balise par ce qui suit :

  ```cshtml
  <div class="sidebar" style="float:left">
      <component type="typeof(NavMenu_IdentityLayout)" 
          render-mode="ServerPrerendered" />
  </div>

  <div class="main" style="padding-left:250px">
      <div class="top-row px-4">
          @{
              var result = Engine.FindView(ViewContext, "_LoginPartial", 
                  isMainPage: false);
          }
          @if (result.Success)
          {
              await Html.RenderPartialAsync("_LoginPartial");
          }
          else
          {
              throw new InvalidOperationException("The default Identity UI " +
                  "layout requires a partial view '_LoginPartial'.");
          }
          <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
      </div>

      <div class="content px-4">
          @RenderBody()
      </div>
  </div>

  <script src="~/Identity/lib/jquery/dist/jquery.min.js"></script>
  <script src="~/Identity/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
  <script src="~/Identity/js/site.js" asp-append-version="true"></script>
  @RenderSection("Scripts", required: false)
  <script src="_framework/blazor.server.js"></script>
  ```

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-with-authorization"></a>Génération de modèles automatique Identity dans un Blazor Server projet avec autorisation

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

Certaines Identity options sont configurées dans *Areas/ Identity / Identity HostingStartup.cs* . Pour plus d’informations, consultez [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

## <a name="standalone-or-hosted-no-locblazor-webassembly-apps"></a>Applications autonomes ou hébergées Blazor WebAssembly

Blazor WebAssemblyLes applications côté client utilisent leurs propres Identity approches de l’interface utilisateur et ne peuvent pas utiliser la ASP.NET Core Identity génération de modèles automatique. Les applications ASP.NET Core côté serveur des solutions hébergées Blazor peuvent suivre les Razor instructions des pages/MVC de cet article et sont configurées comme n’importe quel autre type d’application ASP.net core qui prend en charge Identity .

Le Blazor Framework n’inclut pas les Razor versions de composants des Identity pages d’interface utilisateur. Identity Les Razor composants d’interface utilisateur peuvent être générés de façon personnalisée ou obtenus à partir de sources tierces non prises en charge.

Pour plus d’informations, consultez la section [ Blazor sécurité et Identity Articles](xref:blazor/security/index).

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a>Créer une Identity source d’interface utilisateur complète

Pour conserver le contrôle total de l' Identity interface utilisateur, exécutez le générateur de Identity modèles automatique et sélectionnez **remplacer tous les fichiers** .

Le code en surbrillance suivant montre les modifications pour remplacer l’interface utilisateur par défaut par Identity Identity dans une application web ASP.net Core 2,1. Vous pouvez le faire pour avoir le contrôle total de l' Identity interface utilisateur.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

La valeur par défaut Identity est remplacée dans le code suivant :

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

Le code suivant définit les [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)et [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

Inscrire une `IEmailSender` implémentation, par exemple :

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a>Configuration du mot de passe

Si <xref:Microsoft.AspNetCore.Identity.PasswordOptions> est configuré dans `Startup.ConfigureServices` , la configuration de l' [ `[StringLength]` attribut](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) peut être requise pour la `Password` propriété dans les pages de génération de modèles automatique Identity . `InputModel``Password`les propriétés se trouvent dans les fichiers suivants :

* `Areas/Identity/Pages/Account/Register.cshtml.cs`
* `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-a-page"></a>Désactiver une page

Cette section montre comment désactiver la page de Registre, mais l’approche peut être utilisée pour désactiver n’importe quelle page.

Pour désactiver l’inscription des utilisateurs :

* Génération de modèles automatique Identity . Incluez Account. Register, Account. Login et Account. RegisterConfirmation. Exemple :

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* Mettre à jour les *zones/ Identity /pages/Account/Register.cshtml.cs* afin que les utilisateurs ne puissent pas s’inscrire depuis ce point de terminaison :

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* Mettez à jour les *zones/ Identity /pages/Account/Register.cshtml* pour qu’ils soient cohérents avec les modifications précédentes :

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* Commentez ou supprimez le lien d’inscription de *Areas/ Identity /pages/Account/login.cshtml*

  ```cshtml
  @*
  <p>
      <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
  </p>
  *@
  ```

* Mettez à jour la page *zones/ Identity /pages/Account/RegisterConfirmation* .

  * Supprimez le code et les liens du fichier cshtml.
  * Supprimez le code de confirmation du `PageModel` :

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a>Utiliser une autre application pour ajouter des utilisateurs

Fournissez un mécanisme pour ajouter des utilisateurs en dehors de l’application Web. Les options permettant d’ajouter des utilisateurs sont les suivantes :

* Une application Web d’administration dédiée.
* Application console.

Le code suivant décrit une approche de l’ajout d’utilisateurs :

* Une liste d’utilisateurs est lue en mémoire.
* Un mot de passe fort unique est généré pour chaque utilisateur.
* L’utilisateur est ajouté à la Identity base de données.
* L’utilisateur est averti et a demandé à modifier le mot de passe.

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

L’exemple de code suivant présente l’ajout d’un utilisateur :

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

Une approche similaire peut être suivie pour les scénarios de production.

## <a name="prevent-publish-of-static-no-locidentity-assets"></a>Empêcher la publication d' Identity actifs statiques

Pour empêcher la publication Identity des ressources statiques sur la racine Web, consultez <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> .

## <a name="additional-resources"></a>Ressources supplémentaires

* [Modifications apportées au code d’authentification pour ASP.NET Core 2,1 et versions ultérieures](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 2,1 et versions ultérieures fournissent [ASP.NET Core Identity](xref:security/authentication/identity) comme [ Razor bibliothèque de classes](xref:razor-pages/ui-class). Les applications qui incluent Identity peuvent appliquer l’échafaudage pour ajouter de manière sélective le code source contenu dans la Identity Razor bibliothèque de classes (RCL). Vous pouvez souhaiter générer le code source afin de pouvoir modifier le code et changer le comportement. Par exemple, vous pouvez demander au générateur de modèles automatique de générer le code utilisé dans l’inscription. Le code généré est prioritaire sur le même code dans le Identity RCL. Pour obtenir le contrôle total de l’interface utilisateur et ne pas utiliser le RCL par défaut, consultez la section [créer une source d’interface utilisateur d’identité complète](#full).

Les applications qui n’incluent **pas** l’authentification peuvent appliquer l’échafaudage pour ajouter le Identity package RCL. Vous avez la possibilité de sélectionner du Identity code à générer.

Bien que le générateur de modèles génère la plus grande partie du code nécessaire, vous devez mettre à jour votre projet pour terminer le processus. Ce document explique les étapes nécessaires à la réalisation d’une Identity mise à jour de génération de modèles automatique.

Quand vous exécutez le générateur de Identity modèles, un fichier de *ScaffoldingReadme.txt* est créé dans le répertoire du projet. Le fichier *ScaffoldingReadme.txt* contient des instructions générales sur ce qui est nécessaire pour effectuer la Identity mise à jour de la génération de modèles automatique. Ce document contient des instructions plus complètes que le fichier *ScaffoldingReadme.txt* .

Nous vous recommandons d’utiliser un système de contrôle de code source qui affiche les différences entre les fichiers et vous permet d’annuler les modifications. Inspectez les modifications après avoir exécuté le générateur de Identity modèles.

> [!NOTE]
> Les services sont requis lorsque vous utilisez l' [authentification à deux facteurs](xref:security/authentication/identity-enable-qrcodes), la [confirmation de compte et la récupération de mot de passe](xref:security/authentication/accconfirm), ainsi que d’autres fonctionnalités de sécurité avec Identity . Les services ou stubs de service ne sont pas générés lors de la génération de modèles automatique Identity . Les services pour activer ces fonctionnalités doivent être ajoutés manuellement. Par exemple, consultez [exiger une confirmation par courrier électronique](xref:security/authentication/accconfirm#require-email-confirmation).

## <a name="scaffold-no-locidentity-into-an-empty-project"></a>Génération de modèles automatique Identity dans un projet vide

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Ajoutez les appels en surbrillance suivants à la `Startup` classe :

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a>Générer Identity une structure dans un Razor projet sans autorisation existante

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identityest configuré dans *Areas/ Identity / Identity HostingStartup.cs* . Pour plus d’informations, consultez [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>Migrations, UseAuthentication et disposition

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>Activer l’authentification

Dans la `Configure` méthode de la `Startup` classe, appelez <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> après `UseStaticFiles` :

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>Modifications de la disposition

Facultatif : ajoutez la connexion partielle ( `_LoginPartial` ) au fichier de disposition :

[!code-cshtml[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a>Génération de modèles automatique Identity dans un Razor projet avec autorisation

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

Certaines Identity options sont configurées dans *Areas/ Identity / Identity HostingStartup.cs* . Pour plus d’informations, consultez [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a>Génération de modèles automatique Identity dans un projet MVC sans autorisation existante

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Facultatif : ajoutez la connexion partielle ( `_LoginPartial` ) au fichier *Views/Shared/_Layout. cshtml* :

[!code-cshtml[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* Déplacez le fichier *pages/Shared/_LoginPartial. cshtml* vers *views/shared/_LoginPartial. cshtml*

Identityest configuré dans *Areas/ Identity / Identity HostingStartup.cs* . Pour plus d’informations, consultez IHostingStartup.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

Appeler <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> après `UseStaticFiles` :

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a>Génération Identity de modèles automatique dans un projet MVC avec autorisation

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

Supprimez le dossier *pages/Shared* et les fichiers de ce dossier.

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a>Créer une Identity source d’interface utilisateur complète

Pour conserver le contrôle total de l' Identity interface utilisateur, exécutez le générateur de Identity modèles automatique et sélectionnez **remplacer tous les fichiers** .

Le code en surbrillance suivant montre les modifications pour remplacer l’interface utilisateur par défaut par Identity Identity dans une application web ASP.net Core 2,1. Vous pouvez le faire pour avoir le contrôle total de l' Identity interface utilisateur.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

La valeur par défaut Identity est remplacée dans le code suivant :

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

Le code suivant définit les [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)et [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

Inscrire une `IEmailSender` implémentation, par exemple :

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a>Configuration du mot de passe

Si <xref:Microsoft.AspNetCore.Identity.PasswordOptions> est configuré dans `Startup.ConfigureServices` , la configuration de l' [ `[StringLength]` attribut](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) peut être requise pour la `Password` propriété dans les pages de génération de modèles automatique Identity . `InputModel``Password`les propriétés se trouvent dans les fichiers suivants :

* `Areas/Identity/Pages/Account/Register.cshtml.cs`
* `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-register-page"></a>Désactiver la page de Registre

Pour désactiver l’inscription des utilisateurs :

* Génération de modèles automatique Identity . Incluez Account. Register, Account. Login et Account. RegisterConfirmation. Exemple :

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* Mettre à jour les *zones/ Identity /pages/Account/Register.cshtml.cs* afin que les utilisateurs ne puissent pas s’inscrire depuis ce point de terminaison :

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* Mettez à jour les *zones/ Identity /pages/Account/Register.cshtml* pour qu’ils soient cohérents avec les modifications précédentes :

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* Commentez ou supprimez le lien d’inscription de *Areas/ Identity /pages/Account/login.cshtml*

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* Mettez à jour la page *zones/ Identity /pages/Account/RegisterConfirmation* .

  * Supprimez le code et les liens du fichier cshtml.
  * Supprimez le code de confirmation du `PageModel` :

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a>Utiliser une autre application pour ajouter des utilisateurs

Fournissez un mécanisme pour ajouter des utilisateurs en dehors de l’application Web. Les options permettant d’ajouter des utilisateurs sont les suivantes :

* Une application Web d’administration dédiée.
* Application console.

Le code suivant décrit une approche de l’ajout d’utilisateurs :

* Une liste d’utilisateurs est lue en mémoire.
* Un mot de passe fort unique est généré pour chaque utilisateur.
* L’utilisateur est ajouté à la Identity base de données.
* L’utilisateur est averti et a demandé à modifier le mot de passe.

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

L’exemple de code suivant présente l’ajout d’un utilisateur :

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

Une approche similaire peut être suivie pour les scénarios de production.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Modifications apportées au code d’authentification pour ASP.NET Core 2,1 et versions ultérieures](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end
