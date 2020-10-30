---
title: Razor Conventions d’autorisation des pages dans ASP.NET Core
author: rick-anderson
description: Découvrez comment contrôler l’accès aux pages à l’aide de conventions qui autorisent les utilisateurs et autorisent les utilisateurs anonymes à accéder aux pages ou aux dossiers de pages.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2019
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
uid: security/authorization/razor-pages-authorization
ms.openlocfilehash: 69e1d639aeb55ae64cc54b1cda402ed6bcbb04ab
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060181"
---
# <a name="no-locrazor-pages-authorization-conventions-in-aspnet-core"></a>Razor Conventions d’autorisation des pages dans ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Une façon de contrôler l’accès dans votre Razor application pages consiste à utiliser des conventions d’autorisation au démarrage. Ces conventions vous permettent d’autoriser les utilisateurs et d’autoriser les utilisateurs anonymes à accéder à des pages ou des dossiers de pages individuels. Les conventions décrites dans cette rubrique appliquent automatiquement des [filtres d’autorisation](xref:mvc/controllers/filters#authorization-filters) pour contrôler l’accès.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

L’exemple d’application utilise [ cookie l' ASP.NET Core Identity authentification sans ](xref:security/authentication/cookie). Les concepts et les exemples présentés dans cette rubrique s’appliquent également aux applications qui utilisent ASP.NET Core Identity . Pour utiliser ASP.NET Core Identity , suivez les instructions de la <xref:security/authentication/identity> .

## <a name="require-authorization-to-access-a-page"></a>Demander l’autorisation d’accéder à une page

Utilisez la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> Convention pour ajouter un <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> à la page à l’emplacement spécifié :

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=3)]

Le chemin d’accès spécifié est le chemin d’accès du moteur d’affichage, qui est le Razor chemin d’accès relatif à la racine des pages sans extension et qui contient uniquement des barres obliques.

Pour spécifier une [stratégie d’autorisation](xref:security/authorization/policies), utilisez une [surcharge AuthorizePage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> Un <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> peut être appliqué à une classe de modèle de page avec l' `[Authorize]` attribut de filtre. Pour plus d’informations, consultez [Authorize Filter attribute](xref:razor-pages/filter#authorize-filter-attribute).

## <a name="require-authorization-to-access-a-folder-of-pages"></a>Demander l’autorisation d’accéder à un dossier de pages

Utilisez la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> Convention pour ajouter un <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> à toutes les pages d’un dossier à l’emplacement spécifié :

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=4)]

Le chemin d’accès spécifié est le chemin d’accès du moteur d’affichage, qui est le Razor chemin d’accès relatif à la racine des pages.

Pour spécifier une [stratégie d’autorisation](xref:security/authorization/policies), utilisez une [surcharge AuthorizeFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a>Exiger une autorisation pour accéder à une page de zone

Utilisez la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> Convention pour ajouter un <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> à la page de zone à l’emplacement spécifié :

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

Le nom de la page est le chemin d’accès du fichier sans extension par rapport au répertoire racine des pages pour la zone spécifiée. Par exemple, le nom de la page zones de fichier */ Identity /pages/manage/Accounts.cshtml* est */Manage/Accounts* .

Pour spécifier une [stratégie d’autorisation](xref:security/authorization/policies), utilisez une [surcharge AuthorizeAreaPage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a>Demander l’autorisation d’accéder à un dossier de zones

Utilisez la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> Convention pour ajouter un <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> à toutes les zones d’un dossier au chemin d’accès spécifié :

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

Le chemin d’accès au dossier est le chemin d’accès du dossier relatif au répertoire racine des pages pour la zone spécifiée. Par exemple, le chemin d’accès au dossier pour les fichiers sous *Areas/ Identity /pages/manage/* est */Manage* .

Pour spécifier une [stratégie d’autorisation](xref:security/authorization/policies), utilisez une [surcharge AuthorizeAreaFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a>Autoriser l’accès anonyme à une page

Utilisez la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> Convention pour ajouter un <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> à une page à l’emplacement spécifié :

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=5)]

Le chemin d’accès spécifié est le chemin d’accès du moteur d’affichage, qui est le Razor chemin d’accès relatif à la racine des pages sans extension et qui contient uniquement des barres obliques.

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a>Autoriser l’accès anonyme à un dossier de pages

Utilisez la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> Convention pour ajouter un <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> à toutes les pages d’un dossier à l’emplacement spécifié :

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=6)]

Le chemin d’accès spécifié est le chemin d’accès du moteur d’affichage, qui est le Razor chemin d’accès relatif à la racine des pages.

## <a name="note-on-combining-authorized-and-anonymous-access"></a>Remarque sur la combinaison des accès autorisés et anonymes

Il est possible de spécifier qu’un dossier de pages requiert une autorisation, puis de spécifier qu’une page de ce dossier autorise un accès anonyme :

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

L’inverse, toutefois, n’est pas valide. Vous ne pouvez pas déclarer un dossier de pages pour un accès anonyme, puis spécifier une page dans ce dossier qui nécessite une autorisation :

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

La demande d’autorisation sur la page privée échoue. Lorsque <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> et <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> sont appliqués à la page, le <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> est prioritaire et contrôle l’accès.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Une façon de contrôler l’accès dans votre Razor application pages consiste à utiliser des conventions d’autorisation au démarrage. Ces conventions vous permettent d’autoriser les utilisateurs et d’autoriser les utilisateurs anonymes à accéder à des pages ou des dossiers de pages individuels. Les conventions décrites dans cette rubrique appliquent automatiquement des [filtres d’autorisation](xref:mvc/controllers/filters#authorization-filters) pour contrôler l’accès.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

L’exemple d’application utilise [ cookie l' ASP.NET Core Identity authentification sans ](xref:security/authentication/cookie). Les concepts et les exemples présentés dans cette rubrique s’appliquent également aux applications qui utilisent ASP.NET Core Identity . Pour utiliser ASP.NET Core Identity , suivez les instructions de la <xref:security/authentication/identity> .

## <a name="require-authorization-to-access-a-page"></a>Demander l’autorisation d’accéder à une page

Utilisez la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> Convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> pour ajouter un <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> à la page à l’emplacement spécifié :

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

Le chemin d’accès spécifié est le chemin d’accès du moteur d’affichage, qui est le Razor chemin d’accès relatif à la racine des pages sans extension et qui contient uniquement des barres obliques.

Pour spécifier une [stratégie d’autorisation](xref:security/authorization/policies), utilisez une [surcharge AuthorizePage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> Un <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> peut être appliqué à une classe de modèle de page avec l' `[Authorize]` attribut de filtre. Pour plus d’informations, consultez [Authorize Filter attribute](xref:razor-pages/filter#authorize-filter-attribute).

## <a name="require-authorization-to-access-a-folder-of-pages"></a>Demander l’autorisation d’accéder à un dossier de pages

Utilisez la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> Convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> pour ajouter un <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> à toutes les pages d’un dossier à l’emplacement spécifié :

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

Le chemin d’accès spécifié est le chemin d’accès du moteur d’affichage, qui est le Razor chemin d’accès relatif à la racine des pages.

Pour spécifier une [stratégie d’autorisation](xref:security/authorization/policies), utilisez une [surcharge AuthorizeFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a>Exiger une autorisation pour accéder à une page de zone

Utilisez la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> Convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> pour ajouter un <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> à la page de zone à l’emplacement spécifié :

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

Le nom de la page est le chemin d’accès du fichier sans extension par rapport au répertoire racine des pages pour la zone spécifiée. Par exemple, le nom de la page zones de fichier */ Identity /pages/manage/Accounts.cshtml* est */Manage/Accounts* .

Pour spécifier une [stratégie d’autorisation](xref:security/authorization/policies), utilisez une [surcharge AuthorizeAreaPage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a>Demander l’autorisation d’accéder à un dossier de zones

Utilisez la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> Convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> pour ajouter un <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> à toutes les zones d’un dossier au chemin d’accès spécifié :

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

Le chemin d’accès au dossier est le chemin d’accès du dossier relatif au répertoire racine des pages pour la zone spécifiée. Par exemple, le chemin d’accès au dossier pour les fichiers sous *Areas/ Identity /pages/manage/* est */Manage* .

Pour spécifier une [stratégie d’autorisation](xref:security/authorization/policies), utilisez une [surcharge AuthorizeAreaFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a>Autoriser l’accès anonyme à une page

Utilisez la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> Convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> pour ajouter un <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> à une page à l’emplacement spécifié :

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

Le chemin d’accès spécifié est le chemin d’accès du moteur d’affichage, qui est le Razor chemin d’accès relatif à la racine des pages sans extension et qui contient uniquement des barres obliques.

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a>Autoriser l’accès anonyme à un dossier de pages

Utilisez la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> Convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> pour ajouter un <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> à toutes les pages d’un dossier à l’emplacement spécifié :

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

Le chemin d’accès spécifié est le chemin d’accès du moteur d’affichage, qui est le Razor chemin d’accès relatif à la racine des pages.

## <a name="note-on-combining-authorized-and-anonymous-access"></a>Remarque sur la combinaison des accès autorisés et anonymes

Il est possible de spécifier qu’un dossier de pages nécessitant une autorisation et de spécifier qu’une page de ce dossier autorise un accès anonyme :

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

L’inverse, toutefois, n’est pas valide. Vous ne pouvez pas déclarer un dossier de pages pour un accès anonyme, puis spécifier une page dans ce dossier qui nécessite une autorisation :

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

La demande d’autorisation sur la page privée échoue. Lorsque <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> et <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> sont appliqués à la page, le <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> est prioritaire et contrôle l’accès.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end
