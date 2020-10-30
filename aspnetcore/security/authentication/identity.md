---
title: Présentation de Identity sur ASP.net Core
author: rick-anderson
description: Utilisez Identity avec une application ASP.net core. Découvrez comment définir les exigences de mot de passe (RequireDigit, RequiredLength, RequiredUniqueChars, etc.).
ms.author: riande
ms.date: 7/15/2020
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
uid: security/authentication/identity
ms.openlocfilehash: bfcef860beb07ab81dda1a10a1648491ae187bef
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052017"
---
# <a name="introduction-to-no-locidentity-on-aspnet-core"></a>Présentation de Identity sur ASP.net Core

::: moniker range=">= aspnetcore-3.0"

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core Identity:

* Est une API qui prend en charge la fonctionnalité de connexion de l’interface utilisateur.
* Gère les utilisateurs, les mots de passe, les données de profil, les rôles, les revendications, les jetons, la confirmation par e-mail et bien plus encore.

Les utilisateurs peuvent créer un compte avec les informations de connexion stockées dans Identity ou ils peuvent utiliser un fournisseur de connexion externe. Les fournisseurs de connexion externes pris en charge incluent [Facebook, Google, Microsoft Account et Twitter](xref:security/authentication/social/index).

[!INCLUDE[](~/includes/requireAuth.md)]

Le [ Identity code source](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) est disponible sur GitHub. [Structure Identity ](xref:security/authentication/scaffold-identity) et affichez les fichiers générés pour examiner l’interaction du modèle avec Identity .

Identity est généralement configurée à l’aide d’une base de données SQL Server pour stocker les noms d’utilisateur, les mots de passe et les données de profil. Vous pouvez également utiliser un autre magasin persistant, par exemple, le stockage table Azure.

Dans cette rubrique, vous allez apprendre à utiliser Identity pour inscrire, se connecter et déconnecter un utilisateur. Remarque : les modèles traitent le nom d’utilisateur et l’adresse de messagerie comme les utilisateurs. Pour obtenir des instructions plus détaillées sur la création d’applications qui utilisent Identity , consultez [étapes suivantes](#next).

[Plateforme d’identité Microsoft](/azure/active-directory/develop/) :

* Évolution de la plateforme de développement Azure Active Directory (Azure AD).
* Non lié à ASP.NET Core Identity .

[!INCLUDE[](~/includes/IdentityServer4.md)]

[Affichez ou téléchargez l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a>Créer une application Web avec l’authentification

Créez un projet d’application Web ASP.NET Core avec des comptes d’utilisateur individuels.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Sélectionnez **fichier** > **nouveau** > **projet** .
* Sélectionnez **Application web ASP.NET Core** . Nommez le projet **application Web 1** pour avoir le même espace de noms que le projet à télécharger. Cliquez sur **OK** .
* Sélectionnez une **application Web** ASP.net Core, puis sélectionnez **modifier l’authentification** .
* Sélectionnez **comptes d’utilisateur individuels** , puis cliquez sur **OK** .

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

La commande précédente crée une Razor application Web à l’aide de sqlite. Pour créer l’application Web avec la base de données locale, exécutez la commande suivante :

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

Le projet généré fournit [ASP.NET Core Identity](xref:security/authentication/identity) une [ Razor bibliothèque de classes](xref:razor-pages/ui-class). La Identity Razor bibliothèque de classes expose des points de terminaison avec la `Identity` zone. Exemple :

* /Identity/Account/Login
* /Identity/Account/Logout
* /Identity/Account/Manage

### <a name="apply-migrations"></a>Appliquer des migrations

Appliquez les migrations pour initialiser la base de données.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Exécutez la commande suivante dans la console du gestionnaire de package (PMC) :

`PM> Update-Database`

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

Les migrations ne sont pas nécessaires pour cette étape lors de l’utilisation de SQLite.

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

Pour la base de données locale, exécutez la commande suivante :

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a>Registre de test et connexion

Exécutez l’application et inscrivez un utilisateur. Selon la taille de votre écran, vous devrez peut-être sélectionner le bouton bascule de navigation pour afficher les liens de **connexion** et de **Registre** .

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a>Configurer les Identity services

Les services sont ajoutés dans `ConfigureServices` . Le modèle par défaut consiste à appeler toutes les méthodes `Add{Service}`, puis toutes les méthodes `services.Configure{Service}`.

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=11-99)]

Le code en surbrillance précédent configure Identity avec les valeurs d’option par défaut. Les services sont mis à la disposition de l’application via l' [injection de dépendances](xref:fundamentals/dependency-injection).

Identity est activé en appelant <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> . `UseAuthentication` Ajoute l' [intergiciel (middleware](xref:fundamentals/middleware/index) ) d’authentification au pipeline de requête.

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

L’application générée par un modèle n’utilise pas [d’autorisation](xref:security/authorization/secure-data). `app.UseAuthorization` est inclus pour s’assurer qu’il est ajouté dans le bon ordre si l’application ajoute une autorisation. `UseRouting`, `UseAuthentication` , `UseAuthorization` et `UseEndpoints` doivent être appelés dans l’ordre indiqué dans le code précédent.

Pour plus d’informations sur `IdentityOptions` et `Startup` , consultez <xref:Microsoft.AspNetCore.Identity.IdentityOptions> et démarrage de l' [application](xref:fundamentals/startup).

## <a name="scaffold-register-login-logout-and-registerconfirmation"></a>Registre de génération de modèles, connexion, déconnexion et RegisterConfirmation

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Ajoutez les `Register` fichiers,, `Login` `LogOut` et `RegisterConfirmation` . Suivez l' [identité de l’échafaudage dans un Razor projet avec des instructions d’autorisation](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) pour générer le code présenté dans cette section.

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

Si vous avez créé le projet avec le nom **application Web 1** , exécutez les commandes suivantes. Sinon, utilisez l’espace de noms correct pour `ApplicationDbContext` :

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.RegisterConfirmation"
```

PowerShell utilise un point-virgule comme séparateur de commande. Quand vous utilisez PowerShell, échappez les points-virgules dans la liste de fichiers ou placez la liste de fichiers entre guillemets doubles, comme le montre l’exemple précédent.

Pour plus d’informations sur la génération de modèles automatique Identity , consultez identification de l' [échafaudage dans un Razor projet avec autorisation](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).

---

### <a name="examine-register"></a>Examiner le registre

Quand un utilisateur clique sur le bouton **Register** de la `Register` page, l' `RegisterModel.OnPostAsync` action est appelée. L’utilisateur est créé par [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) sur l' `_userManager` objet :

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/Identity/UI/src/Areas/Identity/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->
[!INCLUDE[](~/includes/disableVer.md)]

### <a name="log-in"></a>Se connecter

Le formulaire de connexion s’affiche lorsque :

* Le lien **connexion** est sélectionné.
* Un utilisateur tente d’accéder à une page restreinte à laquelle il n’est pas autorisé à accéder **ou** lorsqu’il n’a pas été authentifié par le système.

Lorsque le formulaire de la page de connexion est envoyé, l' `OnPostAsync` action est appelée. `PasswordSignInAsync` est appelé sur l' `_signInManager` objet.

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

Pour plus d’informations sur la façon de prendre des décisions d’autorisation, consultez <xref:security/authorization/introduction> .

### <a name="log-out"></a>Se déconnecter

Le lien de **déconnexion** appelle l' `LogoutModel.OnPost` action. 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

Dans le code précédent, le code `return RedirectToPage();` doit être une redirection afin que le navigateur exécute une nouvelle demande et que l’identité de l’utilisateur soit mise à jour.

[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) efface les revendications de l’utilisateur stockées dans un cookie .

La publication est spécifiée dans *pages/Shared/_LoginPartial. cshtml* :

[!code-cshtml[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-no-locidentity"></a>Test Identity

Les modèles de projet Web par défaut autorisent l’accès anonyme aux pages d’hébergement. Pour tester Identity , ajoutez [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) :

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

Si vous êtes connecté, déconnectez-vous. Exécutez l’application et sélectionnez le lien **confidentialité** . Vous êtes redirigé vers la page de connexion.

### <a name="explore-no-locidentity"></a>Explorer Identity

Pour explorer Identity plus en détail :

* [Créer une source d’interface utilisateur d’identité complète](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* Examinez la source de chaque page et parcourez le débogueur.

## <a name="no-locidentity-components"></a>Identity -

Tous les Identity packages NuGet dépendants sont inclus dans le [ASP.net Core Framework partagé](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).

Le package principal pour Identity est [Microsoft. AspNetCore. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/) Ce package contient l’ensemble principal d’interfaces pour ASP.NET Core Identity et est inclus dans `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .

## <a name="migrating-to-no-locaspnet-core-identity"></a>Migration vers ASP.NET Core Identity

Pour plus d’informations et pour obtenir des conseils sur la migration de votre Identity magasin existant, consultez [ Identity migrer l’authentification et ](xref:migration/identity).

## <a name="setting-password-strength"></a>Définition de la force du mot de passe

Consultez [configuration](#pw) d’un exemple qui définit la configuration minimale requise pour le mot de passe.

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a>AddDefault Identity et ajouterIdentity

<xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> a été introduite dans ASP.NET Core 2,1. L’appel de `AddDefaultIdentity` est similaire à l’appel de ce qui suit :

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

Pour plus d’informations, consultez [ Identity source AddDefault](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .

## <a name="prevent-publish-of-static-no-locidentity-assets"></a>Empêcher la publication d' Identity actifs statiques

Pour empêcher la publication Identity des ressources statiques (feuilles de style et fichiers JavaScript pour l' Identity interface utilisateur) sur la racine Web, ajoutez la `ResolveStaticWebAssetsInputsDependsOn` propriété et la `RemoveIdentityAssets` cible suivantes au fichier projet de l’application :

```xml
<PropertyGroup>
  <ResolveStaticWebAssetsInputsDependsOn>RemoveIdentityAssets</ResolveStaticWebAssetsInputsDependsOn>
</PropertyGroup>

<Target Name="RemoveIdentityAssets">
  <ItemGroup>
    <StaticWebAsset Remove="@(StaticWebAsset)" Condition="%(SourceId) == 'Microsoft.AspNetCore.Identity.UI'" />
  </ItemGroup>
</Target>
```

<a name="next"></a>

## <a name="next-steps"></a>Étapes suivantes

* [ASP.NET Core Identity code source](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)
* Pour plus d’informations sur la configuration de à l’aide de SQLite, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) Identity .
* [Configurer Identity](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core Identity est un système d’appartenance qui ajoute des fonctionnalités de connexion aux applications ASP.NET Core. Les utilisateurs peuvent créer un compte avec les informations de connexion stockées dans Identity ou ils peuvent utiliser un fournisseur de connexion externe. Les fournisseurs de connexion externes pris en charge incluent [Facebook, Google, Microsoft Account et Twitter](xref:security/authentication/social/index).

Identity peut être configuré à l’aide d’une base de données SQL Server pour stocker les noms d’utilisateur, les mots de passe et les données de profil. Vous pouvez également utiliser un autre magasin persistant, par exemple, le stockage table Azure.

[Affichez ou téléchargez l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).

Dans cette rubrique, vous allez apprendre à utiliser Identity pour inscrire, se connecter et déconnecter un utilisateur. Pour obtenir des instructions plus détaillées sur la création d’applications qui utilisent Identity , consultez la section étapes suivantes à la fin de cet article.

<a name="adi"></a>

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a>AddDefault Identity et ajouterIdentity

<xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> a été introduite dans ASP.NET Core 2,1. L’appel de `AddDefaultIdentity` est similaire à l’appel de ce qui suit :

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

Pour plus d’informations, consultez [ Identity source AddDefault](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .

## <a name="create-a-web-app-with-authentication"></a>Créer une application Web avec l’authentification

Créez un projet d’application Web ASP.NET Core avec des comptes d’utilisateur individuels.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Sélectionnez **fichier** > **nouveau** > **projet** .
* Sélectionnez **Application web ASP.NET Core** . Nommez le projet **application Web 1** pour avoir le même espace de noms que le projet à télécharger. Cliquez sur **OK** .
* Sélectionnez une **application Web** ASP.net Core, puis sélectionnez **modifier l’authentification** .
* Sélectionnez **comptes d’utilisateur individuels** , puis cliquez sur **OK** .

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

Le projet généré fournit [ASP.NET Core Identity](xref:security/authentication/identity) une [ Razor bibliothèque de classes](xref:razor-pages/ui-class). La Identity Razor bibliothèque de classes expose des points de terminaison avec la `Identity` zone. Exemple :

* /Identity/Account/Login
* /Identity/Account/Logout
* /Identity/Account/Manage

### <a name="apply-migrations"></a>Appliquer des migrations

Appliquez les migrations pour initialiser la base de données.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Exécutez la commande suivante dans la console du gestionnaire de package (PMC) :

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a>Registre de test et connexion

Exécutez l’application et inscrivez un utilisateur. Selon la taille de votre écran, vous devrez peut-être sélectionner le bouton bascule de navigation pour afficher les liens de **connexion** et de **Registre** .

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a>Configurer les Identity services

Les services sont ajoutés dans `ConfigureServices` . Le modèle par défaut consiste à appeler toutes les méthodes `Add{Service}`, puis toutes les méthodes `services.Configure{Service}`.

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

Le code précédent configure Identity avec les valeurs d’option par défaut. Les services sont mis à la disposition de l’application via l' [injection de dépendances](xref:fundamentals/dependency-injection).

Identity est activé en appelant [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_). `UseAuthentication` Ajoute l' [intergiciel (middleware](xref:fundamentals/middleware/index) ) d’authentification au pipeline de requête.

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

Pour plus d’informations, consultez la [ Identity classe options](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) et le démarrage de l' [application](xref:fundamentals/startup).

## <a name="scaffold-register-login-and-logout"></a>Registre de génération de modèles, connexion et déconnexion

Suivez l' [identité de l’échafaudage dans un Razor projet avec des instructions d’autorisation](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) pour générer le code présenté dans cette section.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Ajoutez les fichiers Register, login et LogOut.

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

Si vous avez créé le projet avec le nom **application Web 1** , exécutez les commandes suivantes. Sinon, utilisez l’espace de noms correct pour `ApplicationDbContext` :

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

PowerShell utilise un point-virgule comme séparateur de commande. Quand vous utilisez PowerShell, échappez les points-virgules dans la liste de fichiers ou placez la liste de fichiers entre guillemets doubles, comme le montre l’exemple précédent.

---

### <a name="examine-register"></a>Examiner le registre

Quand un utilisateur clique sur le lien **Register** , l' `RegisterModel.OnPostAsync` action est appelée. L’utilisateur est créé par [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) sur l' `_userManager` objet :

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

Si l’utilisateur a été créé avec succès, l’utilisateur est connecté par l’appel à `_signInManager.SignInAsync` .

**Remarque :** Consultez [confirmation du compte](xref:security/authentication/accconfirm#prevent-login-at-registration) pour connaître les étapes permettant d’empêcher la connexion immédiate lors de l’inscription.

### <a name="log-in"></a>Se connecter

Le formulaire de connexion s’affiche lorsque :

* Le lien **connexion** est sélectionné.
* Un utilisateur tente d’accéder à une page restreinte à laquelle il n’est pas autorisé à accéder **ou** lorsqu’il n’a pas été authentifié par le système.

Lorsque le formulaire de la page de connexion est envoyé, l' `OnPostAsync` action est appelée. `PasswordSignInAsync` est appelé sur l' `_signInManager` objet.

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

Pour plus d’informations sur la façon de prendre des décisions d’autorisation, consultez <xref:security/authorization/introduction> .

### <a name="log-out"></a>Se déconnecter

Le lien de **déconnexion** appelle l' `LogoutModel.OnPost` action. 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) efface les revendications de l’utilisateur stockées dans un cookie .

La publication est spécifiée dans *pages/Shared/_LoginPartial. cshtml* :

[!code-cshtml[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-no-locidentity"></a>Test Identity

Les modèles de projet Web par défaut autorisent l’accès anonyme aux pages d’hébergement. Pour tester Identity , ajoutez [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) à la page confidentialité.

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

Si vous êtes connecté, déconnectez-vous. Exécutez l’application et sélectionnez le lien **confidentialité** . Vous êtes redirigé vers la page de connexion.

### <a name="explore-no-locidentity"></a>Explorer Identity

Pour explorer Identity plus en détail :

* [Créer une source d’interface utilisateur d’identité complète](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* Examinez la source de chaque page et parcourez le débogueur.

## <a name="no-locidentity-components"></a>Identity -

Tous les Identity packages NuGet dépendants sont inclus dans le sous- [package Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).

Le package principal pour Identity est [Microsoft. AspNetCore. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/) Ce package contient l’ensemble principal d’interfaces pour ASP.NET Core Identity et est inclus dans `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .

## <a name="migrating-to-no-locaspnet-core-identity"></a>Migration vers ASP.NET Core Identity

Pour plus d’informations et pour obtenir des conseils sur la migration de votre Identity magasin existant, consultez [ Identity migrer l’authentification et ](xref:migration/identity).

## <a name="setting-password-strength"></a>Définition de la force du mot de passe

Consultez [configuration](#pw) d’un exemple qui définit la configuration minimale requise pour le mot de passe.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur la configuration de à l’aide de SQLite, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) Identity .
* [Configurer Identity](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
