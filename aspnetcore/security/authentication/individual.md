---
title: Articles basés sur des projets de ASP.NET Core créés avec des comptes d’utilisateur individuels
author: rick-anderson
description: Découvrez des articles basés sur des projets ASP.NET Core créés avec des comptes d’utilisateur individuels.
ms.author: riande
ms.date: 12/11/2019
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
uid: security/authentication/individual
ms.openlocfilehash: 656006396de120b7feae6f2e08b5dad3b5a170b5
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053343"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a>Articles basés sur des projets de ASP.NET Core créés avec des comptes d’utilisateur individuels

ASP.NET Core Identity est inclus dans les modèles de projet dans Visual Studio avec l’option « comptes d’utilisateur individuels ».

Les modèles d’authentification sont disponibles dans CLI .NET Core avec `-au Individual` :

::: moniker range=">= aspnetcore-2.1"

```dotnetcli
dotnet new mvc -au Individual
dotnet new webapp -au Individual
```

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```dotnetcli
dotnet new mvc -au Individual
dotnet new razor -au Individual
```

::: moniker-end

Consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore/issues/5833) pour l’authentification de l’API Web.

<a name="no"></a>

## <a name="no-authentication"></a>Aucune authentification

L’authentification est spécifiée dans le CLI .NET Core avec l' `-au` option. Dans Visual Studio, la boîte de dialogue **modifier l’authentification** est disponible pour les nouvelles applications Web. La valeur par défaut pour les nouvelles applications Web dans Visual Studio n’est **pas une authentification** .

Projets créés sans authentification :

* Ne contiennent pas les pages Web et l’interface utilisateur pour la connexion et la déconnexion.
* Ne contiennent pas de code d’authentification.

<a name="win"></a>

## <a name="windows-authentication"></a>Authentification Windows

L’authentification Windows est spécifiée pour les nouvelles applications Web dans le CLI .NET Core avec l' `-au Windows` option. Dans Visual Studio, la boîte de dialogue **modifier l’authentification** fournit les options **d’authentification Windows** .

Si l’authentification Windows est sélectionnée, l’application est configurée pour utiliser le [module IIS d’authentification Windows](xref:host-and-deploy/iis/modules). L’authentification Windows est destinée aux sites Web intranet.

## <a name="dotnet-new-webapp-authentication-options"></a>nouvelles options d’authentification webapp de dotnet

Le tableau suivant présente les options d’authentification disponibles pour les nouvelles applications Web :

| Option | Type d'authentification | Lien vers plus d’informations |
 | ----------------- | ------------ | ---------- |
| Aucun            |  Aucune authentification | | 
| Individuel      |  Authentification individuelle | <xref:security/authentication/identity>
| IndividualB2C   |  Authentification individuelle hébergée dans le Cloud avec Azure AD B2C | [Azure AD B2C](/azure/active-directory-b2c/) |
| SingleOrg       |  Authentification d’organisation pour un seul locataire | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| MultiOrg        |  Authentification d’organisation pour plusieurs locataires | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| Windows         |  Authentification Windows | [Authentification Windows](xref:security/authentication/windowsauth)

## <a name="visual-studio-new-webapp-authentication-options"></a>Nouvelles options d’authentification webapp de Visual Studio

Le tableau suivant présente les options d’authentification disponibles lors de la création d’une nouvelle application Web avec Visual Studio :

| Option | Type d'authentification | Lien vers plus d’informations |
 | ----------------- | ------------ | ---------- |
| Aucun            |  Aucune authentification | | 
| Comptes d’utilisateur individuels/stocker les comptes d’utilisateur dans l’application |  Authentification individuelle | <xref:security/authentication/identity> |
| Comptes d’utilisateur individuels/se connecter à un magasin d’utilisateurs existant dans le Cloud |  Authentification individuelle hébergée dans le Cloud avec Azure AD B2C | [Azure AD B2C](/azure/active-directory-b2c/) |
| Cloud professionnel ou scolaire/organisation unique  |  Authentification d’organisation pour un seul locataire | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| Cloud professionnel ou scolaire/organisation multiple |  Authentification d’organisation pour plusieurs locataires | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| Windows         |  Authentification Windows | [Authentification Windows](xref:security/authentication/windowsauth)

## <a name="additional-resources"></a>Ressources supplémentaires

Les articles suivants montrent comment utiliser le code généré dans ASP.NET Core modèles qui utilisent des comptes d’utilisateur individuels :

* [Account confirmation and password recovery in ASP.NET Core (Confirmation de compte et récupération de mot de passe dans ASP.NET Core)](xref:security/authentication/accconfirm)
* [Créer une application ASP.NET Core avec les données utilisateur protégées par l’autorisation](xref:security/authorization/secure-data)
