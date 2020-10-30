---
title: Migrer l’authentification et Identity vers ASP.net Core
author: ardalis
description: Découvrez comment migrer l’authentification et l’identité d’un projet MVC ASP.NET vers un projet ASP.NET Core MVC.
ms.author: riande
ms.date: 3/22/2020
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
uid: migration/identity
ms.openlocfilehash: 8ceff0596c069d815c38b9bb526477a9d1430951
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060649"
---
# <a name="migrate-authentication-and-no-locidentity-to-aspnet-core"></a>Migrer l’authentification et Identity vers ASP.net Core

Par [Steve Smith](https://ardalis.com/)

Dans l’article précédent, nous avons [migré la configuration d’un projet mvc ASP.net vers ASP.net Core MVC](xref:migration/configuration). Dans cet article, nous migrons les fonctionnalités d’inscription, de connexion et de gestion des utilisateurs.

## <a name="configure-no-locidentity-and-membership"></a>Configurer Identity et appartenance

Dans ASP.NET MVC, les fonctionnalités d’authentification et d’identité sont configurées à l’aide Identity de ASP.net dans *Startup.Auth.cs* et *Identity config.cs* , situées dans le dossier *App_Start* . Dans ASP.NET Core MVC, ces fonctionnalités sont configurées dans *Startup.cs* .

Installez les packages NuGet suivants :

* `Microsoft.AspNetCore.Identity.EntityFrameworkCore`
* `Microsoft.AspNetCore.Authentication.Cookies`
* `Microsoft.EntityFrameworkCore.SqlServer`

Dans *Startup.cs* , mettez à jour la `Startup.ConfigureServices` méthode pour utiliser Entity Framework et les Identity services :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add EF services to the services container.
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

     services.AddMvc();
}
```

À ce stade, il existe deux types référencés dans le code ci-dessus, que nous n’avons pas encore migrés à partir du projet MVC ASP.NET : `ApplicationDbContext` et `ApplicationUser` . Créez un nouveau dossier *Models* dans le projet ASP.net Core et ajoutez-y deux classes correspondant à ces types. Les versions ASP.NET MVC de ces classes sont disponibles dans */Models/ Identity Models.cs* , mais nous allons utiliser un fichier par classe dans le projet migré, car cela est plus clair.

*ApplicationUser.cs* :

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;

namespace NewMvcProject.Models
{
  public class ApplicationUser : IdentityUser
  {
  }
}
```

*ApplicationDbContext.cs* :

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;
using Microsoft.Data.Entity;

namespace NewMvcProject.Models
{
    public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }

        protected override void OnModelCreating(ModelBuilder builder)
        {
            base.OnModelCreating(builder);
            // Customize the ASP.NET Core Identity model and override the defaults if needed.
            // For example, you can rename the ASP.NET Core Identity table names and more.
            // Add your customizations after calling base.OnModelCreating(builder);
        }
    }
}
```

Le projet Web de démarrage de ASP.NET Core MVC n’inclut pas une grande quantité de personnalisation des utilisateurs ou le `ApplicationDbContext` . Lors de la migration d’une application réelle, vous devez également migrer toutes les propriétés et méthodes personnalisées de l’utilisateur et des classes de votre application `DbContext` , ainsi que toute autre classe de modèle utilisée par votre application. Par exemple, si `DbContext` vous avez un `DbSet<Album>` , vous devez migrer la `Album` classe.

Une fois ces fichiers mis en place, le fichier *Startup.cs* peut être compilé en mettant à jour ses `using` instructions :

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Hosting;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
```

Notre application est maintenant prête à prendre en charge l’authentification et les Identity services. Ces fonctionnalités doivent simplement être exposées aux utilisateurs.

## <a name="migrate-registration-and-login-logic"></a>Migrer la logique de connexion et d’inscription

Avec Identity les services configurés pour l’application et l’accès aux données configurés à l’aide de Entity Framework et SQL Server, nous sommes prêts à ajouter la prise en charge de l’inscription et de la connexion à l’application. Rappelez-vous qu' [auparavant dans le processus de migration](xref:migration/mvc#migrate-the-layout-file) , nous avons commenté une référence à *_LoginPartial* dans *_Layout. cshtml* . Il est maintenant temps de revenir à ce code, de supprimer les marques de commentaire et d’ajouter les contrôleurs et les vues nécessaires pour prendre en charge la fonctionnalité de connexion.

Supprimez les marques `@Html.Partial` de commentaire de la ligne dans *_Layout. cshtml* :

```cshtml
      <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
    </ul>
    @*@Html.Partial("_LoginPartial")*@
  </div>
</div>
```

À présent, ajoutez une nouvelle Razor vue nommée *_LoginPartial* au dossier *Views/Shared* :

Mettez à jour *_LoginPartial. cshtml* avec le code suivant (remplacez tout son contenu) :

```cshtml
@inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager

@if (SignInManager.IsSignedIn(User))
{
    <form asp-area="" asp-controller="Account" asp-action="Logout" method="post" id="logoutForm" class="navbar-right">
        <ul class="nav navbar-nav navbar-right">
            <li>
                <a asp-area="" asp-controller="Manage" asp-action="Index" title="Manage">Hello @UserManager.GetUserName(User)!</a>
            </li>
            <li>
                <button type="submit" class="btn btn-link navbar-btn navbar-link">Log out</button>
            </li>
        </ul>
    </form>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="" asp-controller="Account" asp-action="Register">Register</a></li>
        <li><a asp-area="" asp-controller="Account" asp-action="Login">Log in</a></li>
    </ul>
}
```

À ce stade, vous devez être en mesure d’actualiser le site dans votre navigateur.

## <a name="summary"></a>Résumé

ASP.NET Core introduit les modifications apportées aux Identity fonctionnalités ASP.net. Dans cet article, vous avez vu comment migrer les fonctionnalités d’authentification et de gestion des utilisateurs de ASP.NET Identity vers ASP.net core.
