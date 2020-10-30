---
title: Ajouter, télécharger et supprimer des données utilisateur Identity dans un projet ASP.net Core
author: rick-anderson
description: Découvrez comment ajouter des données utilisateur personnalisées à Identity dans un projet ASP.net core. Supprimez les données par RGPD.
ms.author: riande
ms.date: 03/26/2020
ms.custom: mvc, seodec18
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
uid: security/authentication/add-user-data
ms.openlocfilehash: a4e1fd780947cfa5f09fb1e03964595fa09f0f18
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061416"
---
# <a name="add-download-and-delete-custom-user-data-to-no-locidentity-in-an-aspnet-core-project"></a>Ajouter, télécharger et supprimer des données utilisateur personnalisées Identity dans un projet ASP.net Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

Cet article montre comment :

* Ajouter des données utilisateur personnalisées à une application Web ASP.NET Core.
* Marquez le modèle de données utilisateur personnalisé avec l' <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> attribut afin qu’il soit automatiquement disponible au téléchargement et à la suppression. La possibilité de télécharger et de supprimer les données vous aide à répondre aux exigences de [RGPD](xref:security/gdpr) .

L’exemple de projet est créé à partir d’une Razor application Web pages, mais les instructions sont similaires pour une asp.net Core application Web MVC.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Prérequis

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-no-locrazor-web-app"></a>Créer une Razor application Web

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* Dans Visual Studio, dans le menu **Fichier** , sélectionnez **Nouveau** > **Projet** . Nommez le projet **application Web 1** si vous souhaitez qu’il corresponde à l’espace de noms de l’exemple de code de [Téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) .
* Sélectionnez **ASP.net Core application Web** > **OK**
* Sélectionnez **ASP.NET Core 3,0** dans la liste déroulante
* Sélectionner l' **application Web** > **OK**
* Générez et exécutez le projet.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Dans Visual Studio, dans le menu **Fichier** , sélectionnez **Nouveau** > **Projet** . Nommez le projet **application Web 1** si vous souhaitez qu’il corresponde à l’espace de noms de l’exemple de code de [Téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) .
* Sélectionnez **ASP.net Core application Web** > **OK**
* Sélectionnez **ASP.NET Core 2,2** dans la liste déroulante
* Sélectionner l' **application Web** > **OK**
* Générez et exécutez le projet.

::: moniker-end


# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-no-locidentity-scaffolder"></a>Exécuter le générateur de Identity modèles

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Dans **Explorateur de solutions** , cliquez avec le bouton droit sur le projet > **Ajouter**  >  **un nouvel élément de génération de modèles** automatique.
* Dans le volet gauche de la boîte de dialogue **Ajouter une structure** , sélectionnez **Identity**  >  **Ajouter** .
* Dans la boîte de dialogue **ajouter Identity** , les options suivantes sont disponibles :
  * Sélectionner le fichier de disposition existant  *~/Pages/Shared/_Layout. cshtml*
  * Sélectionnez les fichiers suivants à remplacer :
    * **Compte/inscription**
    * **Compte/gestion/index**
  * Sélectionnez le **+** bouton pour créer une **classe de contexte de données** . Acceptez le type ( **application Web 1. Models. WebApp1Context** si le projet est nommé **application Web 1** ).
  * Sélectionnez le **+** bouton pour créer une nouvelle **classe d’utilisateur** . Acceptez le type ( **WebApp1User** si le projet est nommé **application Web 1** ) > **Ajouter** .
* Sélectionnez **Ajouter** .

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

Si vous n’avez pas déjà installé le générateur de modèles automatique ASP.NET Core, installez-le maintenant :

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Ajoutez une référence de package à [Microsoft. VisualStudio. Web. CodeGeneration. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) dans le fichier projet (. csproj). Exécutez la commande suivante dans le répertoire du projet :

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

Exécutez la commande suivante pour répertorier les options de l' Identity échafaudage :

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

Dans le dossier du projet, exécutez le générateur de Identity modèles :

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

Suivez les instructions dans [migrations, UseAuthentication et Layout](xref:security/authentication/scaffold-identity#efm) pour effectuer les étapes suivantes :

* Créer une migration et mettre à jour la base de données.
* Ajout de `UseAuthentication` à `Startup.Configure`.
* Ajoutez `<partial name="_LoginPartial" />` au fichier de disposition.
* Testez l’application :
  * Inscrire un utilisateur
  * Sélectionnez le nouveau nom d’utilisateur (à côté du lien de **déconnexion** ). Vous devrez peut-être développer la fenêtre ou sélectionner l’icône de barre de navigation pour afficher le nom d’utilisateur et d’autres liens.
  * Sélectionnez l’onglet **données personnelles** .
  * Sélectionnez le bouton **Télécharger** et examinez le *PersonalData.js* fichier.
  * Testez le bouton **supprimer** , qui supprime l’utilisateur connecté.

## <a name="add-custom-user-data-to-the-no-locidentity-db"></a>Ajouter des données utilisateur personnalisées à la base de données Identity

Mettez à jour la `IdentityUser` classe dérivée avec des propriétés personnalisées. Si vous avez nommé le projet application Web 1, le fichier est nommé *Areas/ Identity /Data/WebApp1User.cs* . Mettez à jour le fichier avec le code suivant :

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

Les propriétés avec l’attribut [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) sont les suivantes :

* Supprimé lorsque la page *zones/ Identity /pages/Account/Manage/DeletePersonalData.cshtml* Razor appelle `UserManager.Delete` .
* Inclus dans les données téléchargées par la page *zones/ Identity /pages/Account/Manage/DownloadPersonalData.cshtml* Razor .

### <a name="update-the-accountmanageindexcshtml-page"></a>Mettre à jour la page Account/Manage/index. cshtml

Mettez à jour `InputModel` dans *Areas/ Identity /pages/Account/Manage/index.cshtml.cs* avec le code en surbrillance suivant :

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

Mettez à jour les *zones/ Identity /pages/Account/Manage/index.cshtml* avec le balisage en surbrillance suivant :

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

Mettez à jour les *zones/ Identity /pages/Account/Manage/index.cshtml* avec le balisage en surbrillance suivant :

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a>Mettre à jour la page Account/Register. cshtml

Mettez à jour `InputModel` dans *Areas/ Identity /pages/Account/Register.cshtml.cs* avec le code en surbrillance suivant :

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

Mettez à jour les *zones/ Identity /pages/Account/Register.cshtml* avec le balisage en surbrillance suivant :

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

Mettez à jour les *zones/ Identity /pages/Account/Register.cshtml* avec le balisage en surbrillance suivant :

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


Créez le projet.

### <a name="add-a-migration-for-the-custom-user-data"></a>Ajouter une migration pour les données utilisateur personnalisées

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Dans la console du **Gestionnaire de package** Visual Studio :

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a>Test créer, afficher, télécharger, supprimer des données utilisateur personnalisées

Testez l’application :

* Inscrire un nouvel utilisateur.
* Affichez les données utilisateur personnalisées sur la `/Identity/Account/Manage` page.
* Téléchargez et affichez les données personnelles des utilisateurs à partir de la `/Identity/Account/Manage/PersonalData` page.

## <a name="add-claims-to-no-locidentity-using-iuserclaimsprincipalfactoryapplicationuser"></a>Ajouter des revendications à à Identity l’aide de IUserClaimsPrincipalFactory<ApplicationUser>

> [!NOTE]
> Cette section n’est pas une extension du didacticiel précédent. Pour appliquer les étapes suivantes à l’application générée à l’aide du didacticiel, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/18797).

Des revendications supplémentaires peuvent être ajoutées à à ASP.NET Core Identity l’aide de l' `IUserClaimsPrincipalFactory<T>` interface. Cette classe peut être ajoutée à l’application dans la `Startup.ConfigureServices` méthode. Ajoutez l’implémentation personnalisée de la classe comme suit :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddScoped<IUserClaimsPrincipalFactory<ApplicationUser>, 
        AdditionalUserClaimsPrincipalFactory>();
```

Le code de démonstration utilise la `ApplicationUser` classe. Cette classe ajoute une `IsAdmin` propriété qui est utilisée pour ajouter la revendication supplémentaire.

```csharp
public class ApplicationUser : IdentityUser
{
    public bool IsAdmin { get; set; }
}
```

`AdditionalUserClaimsPrincipalFactory` implémente l'interface `UserClaimsPrincipalFactory`. Une nouvelle revendication de rôle est ajoutée au `ClaimsPrincipal` .

```csharp
public class AdditionalUserClaimsPrincipalFactory 
        : UserClaimsPrincipalFactory<ApplicationUser, IdentityRole>
{
    public AdditionalUserClaimsPrincipalFactory( 
        UserManager<ApplicationUser> userManager,
        RoleManager<IdentityRole> roleManager, 
        IOptions<IdentityOptions> optionsAccessor) 
        : base(userManager, roleManager, optionsAccessor)
    {}

    public async override Task<ClaimsPrincipal> CreateAsync(ApplicationUser user)
    {
        var principal = await base.CreateAsync(user);
        var identity = (ClaimsIdentity)principal.Identity;

        var claims = new List<Claim>();
        if (user.IsAdmin)
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "admin"));
        }
        else
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "user"));
        }

        identity.AddClaims(claims);
        return principal;
    }
}
```

La revendication supplémentaire peut ensuite être utilisée dans l’application. Dans une Razor page, l' `IAuthorizationService` instance peut être utilisée pour accéder à la valeur de revendication.

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

@if ((await AuthorizationService.AuthorizeAsync(User, "IsAdmin")).Succeeded)
{
    <ul class="mr-auto navbar-nav">
        <li class="nav-item">
            <a class="nav-link" asp-controller="Admin" asp-action="Index">ADMIN</a>
        </li>
    </ul>
}
```
