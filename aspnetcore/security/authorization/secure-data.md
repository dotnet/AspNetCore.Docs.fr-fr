---
title: Créer une application ASP.NET Core avec les données utilisateur protégées par l’autorisation
author: rick-anderson
description: Découvrez comment créer une application Web ASP.NET Core avec les données utilisateur protégées par l’autorisation. Comprend le protocole HTTPs, l’authentification, la sécurité ASP.NET Core Identity .
ms.author: riande
ms.date: 7/18/2020
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
uid: security/authorization/secure-data
ms.openlocfilehash: ebd3c0dc9baa63b30f142773d7a3d621ce4082d9
ms.sourcegitcommit: ebc5beccba5f3f7619de20baa58ad727d2a3d18c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98689303"
---
# <a name="create-an-aspnet-core-web-app-with-user-data-protected-by-authorization"></a>Créer une application Web ASP.NET Core avec les données utilisateur protégées par l’autorisation

Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Joe Audette](https://twitter.com/joeaudette)

::: moniker range="= aspnetcore-2.0"

Voir [ce PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Ce didacticiel montre comment créer une application Web ASP.NET Core avec les données utilisateur protégées par l’autorisation. Il affiche la liste des contacts que les utilisateurs authentifiés (inscrits) ont créés. Il existe trois groupes de sécurité :

* Les **utilisateurs inscrits** peuvent afficher toutes les données approuvées et peuvent modifier/supprimer leurs propres données.
* Les **responsables** peuvent approuver ou rejeter les données de contact. Seuls les contacts approuvés sont visibles pour les utilisateurs.
* **Les administrateurs** peuvent approuver/refuser et modifier/supprimer des données.

Les images de ce document ne correspondent pas exactement aux modèles les plus récents.

Dans l’image suivante, l’utilisateur Rick ( `rick@example.com` ) est connecté. Rick peut uniquement afficher les contacts approuvés et **modifier** / **supprimer** / **créer** des liens pour ses contacts. Seul le dernier enregistrement créé par Rick affiche des liens **modifier** et **supprimer** . Les autres utilisateurs ne verront pas le dernier enregistrement jusqu’à ce qu’un responsable ou un administrateur change l’État en « approuvé ».

![Capture d’écran montrant que Rick est connecté](secure-data/_static/rick.png)

Dans l’image suivante, `manager@contoso.com` est connecté et dans le rôle du responsable :

![Capture d’écran montrant la manager@contoso.com connexion](secure-data/_static/manager1.png)

L’illustration suivante montre la vue des détails du gestionnaire d’un contact :

![Vue du gestionnaire d’un contact](secure-data/_static/manager.png)

Les boutons **approuver** et **rejeter** s’affichent uniquement pour les responsables et les administrateurs.

Dans l’image suivante, `admin@contoso.com` est connecté et dans le rôle de l’administrateur :

![Capture d’écran montrant la admin@contoso.com connexion](secure-data/_static/admin.png)

L’administrateur dispose de tous les privilèges. Elle peut lire/modifier/supprimer un contact et modifier l’état des contacts.

L’application a été créée par la [génération](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) de modèles automatique du `Contact` modèle suivant :

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

L’exemple contient les gestionnaires d’autorisations suivants :

* `ContactIsOwnerAuthorizationHandler`: Garantit qu’un utilisateur peut uniquement modifier ses données.
* `ContactManagerAuthorizationHandler`: Permet aux gestionnaires d’approuver ou de rejeter les contacts.
* `ContactAdministratorsAuthorizationHandler`: Permet aux administrateurs d’approuver ou de refuser des contacts, ainsi que de modifier/supprimer des contacts.

## <a name="prerequisites"></a>Prérequis

Ce didacticiel est avancé. Vous devez connaître les éléments suivants :

* [ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc)
* [Authentification](xref:security/authentication/identity)
* [Confirmation de compte et récupération de mot de passe](xref:security/authentication/accconfirm)
* [Autorisation](xref:security/authorization/introduction)
* [Entity Framework Core](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a>L’application de démarrage et la fin de l’application

[Téléchargez](xref:index#how-to-download-a-sample) l’application [terminée](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) . [Testez](#test-the-completed-app) l’application terminée afin de vous familiariser avec ses fonctionnalités de sécurité.

### <a name="the-starter-app"></a>L’application de démarrage

[Téléchargez](xref:index#how-to-download-a-sample) l’application de [démarrage](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .

Exécutez l’application, appuyez sur le lien **ContactManager** et vérifiez que vous pouvez créer, modifier et supprimer un contact. Pour créer l’application de démarrage, consultez [créer l’application de démarrage](#create-the-starter-app).

## <a name="secure-user-data"></a>Sécuriser les données utilisateur

Les sections suivantes présentent les principales étapes à suivre pour créer l’application de données utilisateur sécurisé. Il peut s’avérer utile de faire référence au projet terminé.

### <a name="tie-the-contact-data-to-the-user"></a>Lier les données de contact à l’utilisateur

Utilisez l' [Identity](xref:security/authentication/identity) ID d’utilisateur ASP.net pour vous assurer que les utilisateurs peuvent modifier leurs données, mais pas les données d’autres utilisateurs. Ajoutez `OwnerID` et `ContactStatus` au `Contact` modèle :

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

`OwnerID` ID de l’utilisateur de la `AspNetUser` table dans la [Identity](xref:security/authentication/identity) base de données. Le `Status` champ détermine si un contact est visible par les utilisateurs généraux.

Créez une nouvelle migration et mettez à jour la base de données :

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a>Ajouter des services de rôle à Identity

Ajoutez [rôles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) pour ajouter des services de rôle :

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

<a name="rau"></a>

### <a name="require-authenticated-users"></a>Exiger des utilisateurs authentifiés

Définissez la stratégie d’authentification de secours pour exiger que les utilisateurs soient authentifiés :

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=13-99)]

Le code en surbrillance précédent définit la [stratégie d’authentification de secours](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy). La stratégie d’authentification de secours exige que **_tous_* les utilisateurs soient authentifiés, à l’exception des Razor pages, des contrôleurs ou des méthodes d’action avec un attribut d’authentification. Par exemple, Razor les pages, les contrôleurs ou les méthodes d’action avec `[AllowAnonymous]` ou `[Authorize(PolicyName="MyPolicy")]` utilisent l’attribut d’authentification appliqué plutôt que la stratégie d’authentification de secours.

<xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> Ajoute <xref:Microsoft.AspNetCore.Authorization.Infrastructure.DenyAnonymousAuthorizationRequirement> à l’instance actuelle, ce qui garantit que l’utilisateur actuel est authentifié.

Stratégie d’authentification de secours :

_ Est appliqué à toutes les demandes qui ne spécifient pas explicitement une stratégie d’authentification. Pour les demandes traitées par le routage de point de terminaison, cela inclut tout point de terminaison qui ne spécifie pas d’attribut d’autorisation. Pour les demandes servies par un autre intergiciel après l’intergiciel (middleware) d’autorisation, par exemple des [fichiers statiques](xref:fundamentals/static-files), cela applique la stratégie à toutes les demandes.

La définition de la stratégie d’authentification de secours pour exiger que les utilisateurs soient authentifiés protège les pages et les contrôleurs qui viennent d’être ajoutés Razor . L’authentification requise par défaut est plus sécurisée que le fait de s’appuyer sur de nouveaux contrôleurs et Razor pages pour inclure l' `[Authorize]` attribut.

La <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> classe contient également <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType> . `DefaultPolicy`Est la stratégie utilisée avec l' `[Authorize]` attribut quand aucune stratégie n’est spécifiée. `[Authorize]` ne contient pas de stratégie nommée, contrairement à `[Authorize(PolicyName="MyPolicy")]` .

Pour plus d’informations sur les stratégies, consultez <xref:security/authorization/policies> .

Pour que les contrôleurs et Razor les pages MVC requièrent que tous les utilisateurs soient authentifiés, vous pouvez également ajouter un filtre d’autorisation :

[!code-csharp[](secure-data/samples/final3/Startup2.cs?name=snippet&highlight=14-99)]

Le code précédent utilise un filtre d’autorisation, la définition de la stratégie de secours utilise le routage du point de terminaison. La définition de la stratégie de secours est la méthode recommandée pour exiger que tous les utilisateurs soient authentifiés.

Ajoutez [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) aux `Index` pages et `Privacy` afin que les utilisateurs anonymes puissent obtenir des informations sur le site avant de s’inscrire :

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a>Configurer le compte de test

La `SeedData` classe crée deux comptes : administrateur et gestionnaire. Utilisez l' [outil secret Manager](xref:security/app-secrets) pour définir un mot de passe pour ces comptes. Définissez le mot de passe à partir du répertoire du projet (répertoire contenant *Program.cs*) :

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

Si aucun mot de passe fort n’est spécifié, une exception est levée lorsque la `SeedData.Initialize` méthode est appelée.

Mettre à jour `Main` pour utiliser le mot de passe de test :

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a>Créer les comptes de test et mettre à jour les contacts

Mettez à jour la `Initialize` méthode dans la `SeedData` classe pour créer les comptes de test :

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

Ajoutez l’ID d’utilisateur administrateur et `ContactStatus` les contacts. Faites de l’un des contacts « soumis » et un « rejeté ». Ajoutez l’ID d’utilisateur et l’État à tous les contacts. Un seul contact est affiché :

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>Créer des gestionnaires d’autorisation propriétaire, responsable et administrateur

Créez une `ContactIsOwnerAuthorizationHandler` classe dans le dossier *authorization* . Le `ContactIsOwnerAuthorizationHandler` vérifie que l’utilisateur agissant sur une ressource possède la ressource.

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

`ContactIsOwnerAuthorizationHandler`Contexte des appels [. Réussie](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) si l’utilisateur authentifié actuel est le propriétaire du contact. Les gestionnaires d’autorisations sont généralement :

* Retourne `context.Succeed` lorsque les spécifications sont satisfaites.
* Retourne `Task.CompletedTask` lorsque les spécifications ne sont pas remplies. `Task.CompletedTask` n’est pas un succès ou un échec &mdash; . il permet l’exécution d’autres gestionnaires d’autorisations.

Si vous devez faire échouer explicitement, retournez le [contexte. Échoue](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).

L’application permet aux propriétaires de contacts de modifier/supprimer/créer leurs propres données. `ContactIsOwnerAuthorizationHandler` n’a pas besoin de vérifier l’opération passée dans le paramètre d’exigence.

### <a name="create-a-manager-authorization-handler"></a>Créer un gestionnaire d’autorisations de gestionnaire

Créez une `ContactManagerAuthorizationHandler` classe dans le dossier *authorization* . Le `ContactManagerAuthorizationHandler` vérifie que l’utilisateur agissant sur la ressource est un responsable. Seuls les responsables peuvent approuver ou rejeter les modifications de contenu (nouveaux ou modifiés).

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>Créer un gestionnaire d’autorisations d’administrateur

Créez une `ContactAdministratorsAuthorizationHandler` classe dans le dossier *authorization* . Le `ContactAdministratorsAuthorizationHandler` vérifie que l’utilisateur agissant sur la ressource est un administrateur. L’administrateur peut effectuer toutes les opérations.

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>Inscrire les gestionnaires d’autorisations

Les services qui utilisent Entity Framework Core doivent être inscrits pour l' [injection de dépendances](xref:fundamentals/dependency-injection) à l’aide de [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions). `ContactIsOwnerAuthorizationHandler`Utilise ASP.net Core [Identity](xref:security/authentication/identity) , qui repose sur Entity Framework Core. Inscrire les gestionnaires auprès de la collection de services afin qu’ils soient disponibles pour le `ContactsController` via l' [injection de dépendances](xref:fundamentals/dependency-injection). Ajoutez le code suivant à la fin de `ConfigureServices` :

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

`ContactAdministratorsAuthorizationHandler` et `ContactManagerAuthorizationHandler` sont ajoutés en tant que singletons. Il s’agit de singletons, car ils n’utilisent pas EF et toutes les informations nécessaires sont dans le `Context` paramètre de la `HandleRequirementAsync` méthode.

## <a name="support-authorization"></a>Autorisation du support

Dans cette section, vous allez mettre à jour les Razor pages et ajouter une classe d’exigences d’opérations.

### <a name="review-the-contact-operations-requirements-class"></a>Examiner la classe des exigences relatives aux opérations de contact

Passez en revue la `ContactOperations` classe. Cette classe contient les spécifications prises en charge par l’application :

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a>Créer une classe de base pour les Razor pages contacts

Créez une classe de base qui contient les services utilisés dans les Razor pages de contacts. La classe de base place le code d’initialisation à un emplacement :

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

Le code précédent :

* Ajoute le `IAuthorizationService` service pour accéder aux gestionnaires d’autorisations.
* Ajoute le Identity `UserManager` service.
* Ajoutez la `ApplicationDbContext`.

### <a name="update-the-createmodel"></a>Mettre à jour CreateModel

Mettez à jour le constructeur de modèle de page de création pour utiliser la `DI_BasePageModel` classe de base :

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

Mettez à jour la `CreateModel.OnPostAsync` méthode pour :

* Ajoutez l’ID utilisateur au `Contact` modèle.
* Appelez le gestionnaire d’autorisations pour vérifier que l’utilisateur a l’autorisation de créer des contacts.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a>Mettre à jour IndexModel

Mettez à jour la `OnGetAsync` méthode afin que seuls les contacts approuvés soient affichés pour les utilisateurs généraux :

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a>Mettre à jour EditModel

Ajoutez un gestionnaire d’autorisations pour vérifier que l’utilisateur possède le contact. Étant donné que l’autorisation des ressources est en cours de validation, l' `[Authorize]` attribut n’est pas suffisant. L’application n’a pas accès à la ressource lors de l’évaluation des attributs. L’autorisation basée sur les ressources doit être impérative. Les vérifications doivent être effectuées une fois que l’application a accès à la ressource, soit en la chargeant dans le modèle de page, soit en la chargeant dans le gestionnaire lui-même. Vous accédez fréquemment à la ressource en passant la clé de ressource.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a>Mettre à jour DeleteModel

Mettez à jour le modèle de page de suppression pour utiliser le gestionnaire d’autorisations afin de vérifier que l’utilisateur dispose de l’autorisation de suppression sur le contact.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a>Injecter le service d’autorisation dans les vues

Actuellement, l’interface utilisateur affiche les liens modifier et supprimer pour les contacts que l’utilisateur ne peut pas modifier.

Injecter le service d’autorisation dans le fichier *pages/_ViewImports. cshtml* afin qu’il soit disponible pour tous les affichages :

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

Le balisage précédent ajoute plusieurs `using` instructions.

Mettez à jour les liens **modifier** et **supprimer** dans *pages/contacts/index. cshtml* afin qu’ils soient affichés uniquement pour les utilisateurs disposant des autorisations appropriées :

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> Le masquage des liens des utilisateurs qui n’ont pas l’autorisation de modifier des données ne sécurise pas l’application. Le masquage des liens rend l’application plus conviviale en affichant uniquement des liens valides. Les utilisateurs peuvent pirater les URL générées pour appeler des opérations de modification et de suppression sur les données qu’ils ne possèdent pas. La Razor page ou le contrôleur doit appliquer les vérifications d’accès pour sécuriser les données.

### <a name="update-details"></a>Mettre à jour les détails

Mettez à jour la vue Détails pour permettre aux responsables d’approuver ou de rejeter les contacts :

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

Mettez à jour le modèle de page de détails :

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a>Ajouter ou supprimer un utilisateur dans un rôle

Consultez [ce numéro](https://github.com/dotnet/AspNetCore.Docs/issues/8502) pour plus d’informations sur :

* Suppression des privilèges d’un utilisateur. Par exemple, la désactivation d’un utilisateur dans une application de conversation.
* Ajout de privilèges à un utilisateur.

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a>Différences entre Challenge et interdire

Cette application définit la stratégie par défaut pour [exiger des utilisateurs authentifiés](#rau). Le code suivant autorise les utilisateurs anonymes. Les utilisateurs anonymes sont autorisés à afficher les différences entre la stimulation et l’interdiction.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

Dans le code précédent :

* Lorsque l’utilisateur n’est **pas** authentifié, un `ChallengeResult` est retourné. Lorsqu’un `ChallengeResult` est retourné, l’utilisateur est redirigé vers la page de connexion.
* Lorsque l’utilisateur est authentifié, mais n’est pas autorisé, un `ForbidResult` est retourné. Lorsqu’un `ForbidResult` est retourné, l’utilisateur est redirigé vers la page accès refusé.

## <a name="test-the-completed-app"></a>Tester l’application terminée

Si vous n’avez pas encore défini de mot de passe pour les comptes d’utilisateurs amorcés, utilisez l' [outil secret Manager](xref:security/app-secrets#secret-manager) pour définir un mot de passe :

* Choisissez un mot de passe fort : utilisez au moins huit caractères et au moins un caractère majuscule, un chiffre et un symbole. Par exemple, `Passw0rd!` répond aux exigences de mot de passe fort.
* Exécutez la commande suivante à partir du dossier du projet, où `<PW>` est le mot de passe :

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

Si l’application a des contacts :

* Supprimez tous les enregistrements de la `Contact` table.
* Redémarrez l’application pour amorcer la base de données.

Un moyen simple de tester l’application terminée consiste à lancer trois navigateurs différents (ou des sessions incognito/InPrivate). Dans un navigateur, inscrivez un nouvel utilisateur (par exemple, `test@example.com` ). Connectez-vous à chaque navigateur avec un autre utilisateur. Vérifiez les opérations suivantes :

* Les utilisateurs inscrits peuvent afficher toutes les données de contact approuvées.
* Les utilisateurs inscrits peuvent modifier/supprimer leurs propres données.
* Les responsables peuvent approuver/rejeter les données de contact. La `Details` vue affiche les boutons **approuver** et **rejeter** .
* Les administrateurs peuvent approuver/refuser et modifier/supprimer toutes les données.

| Utilisateur                | Amorcé par l’application | Options                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | Non                | Modifiez/supprimez les données.                |
| manager@contoso.com | Oui               | Approuver/refuser et modifier/supprimer des données. |
| admin@contoso.com   | Oui               | Approuver/refuser et modifier/supprimer toutes les données. |

Créez un contact dans le navigateur de l’administrateur. Copiez l’URL de la suppression et de la modification à partir du contact de l’administrateur. Collez ces liens dans le navigateur de l’utilisateur de test pour vérifier que l’utilisateur de test ne peut pas effectuer ces opérations.

## <a name="create-the-starter-app"></a>Créer l’application de démarrage

* Créer une Razor application pages nommée « ContactManager »
  * Créez l’application avec des **comptes d’utilisateur individuels**.
  * Nommez-la « ContactManager » pour que l’espace de noms corresponde à l’espace de noms utilisé dans l’exemple.
  * `-uld` spécifie la base de données locale au lieu de SQLite

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* Ajouter des *modèles/contact. cs*:

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* Structurez le `Contact` modèle.
* Créez la migration initiale et mettez à jour la base de données :

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

Si vous rencontrez un bogue avec la `dotnet aspnet-codegenerator razorpage` commande, consultez [ce problème GitHub](https://github.com/aspnet/Scaffolding/issues/984).

* Mettez à jour l’ancre **ContactManager** dans le fichier *pages/shared/_Layout. cshtml* :

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* Tester l’application en créant, en modifiant et en supprimant un contact

### <a name="seed-the-database"></a>Amorcer la base de données

Ajoutez la classe [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) au dossier *Data* :

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

Appeler `SeedData.Initialize` à partir de `Main` :

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

Vérifiez que l’application a amorcé la base de données. Si la base de coordonnées contient des lignes, la méthode Seed ne s’exécute pas.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

Ce didacticiel montre comment créer une application Web ASP.NET Core avec les données utilisateur protégées par l’autorisation. Il affiche la liste des contacts que les utilisateurs authentifiés (inscrits) ont créés. Il existe trois groupes de sécurité :

* Les **utilisateurs inscrits** peuvent afficher toutes les données approuvées et peuvent modifier/supprimer leurs propres données.
* Les **responsables** peuvent approuver ou rejeter les données de contact. Seuls les contacts approuvés sont visibles pour les utilisateurs.
* **Les administrateurs** peuvent approuver/refuser et modifier/supprimer des données.

Dans l’image suivante, l’utilisateur Rick ( `rick@example.com` ) est connecté. Rick peut uniquement afficher les contacts approuvés et **modifier** / **supprimer** / **créer** des liens pour ses contacts. Seul le dernier enregistrement créé par Rick affiche des liens **modifier** et **supprimer** . Les autres utilisateurs ne verront pas le dernier enregistrement jusqu’à ce qu’un responsable ou un administrateur change l’État en « approuvé ».

![Capture d’écran montrant que Rick est connecté](secure-data/_static/rick.png)

Dans l’image suivante, `manager@contoso.com` est connecté et dans le rôle du responsable :

![Capture d’écran montrant la manager@contoso.com connexion](secure-data/_static/manager1.png)

L’illustration suivante montre la vue des détails du gestionnaire d’un contact :

![Vue du gestionnaire d’un contact](secure-data/_static/manager.png)

Les boutons **approuver** et **rejeter** s’affichent uniquement pour les responsables et les administrateurs.

Dans l’image suivante, `admin@contoso.com` est connecté et dans le rôle de l’administrateur :

![Capture d’écran montrant la admin@contoso.com connexion](secure-data/_static/admin.png)

L’administrateur dispose de tous les privilèges. Elle peut lire/modifier/supprimer un contact et modifier l’état des contacts.

L’application a été créée par la [génération](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) de modèles automatique du `Contact` modèle suivant :

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

L’exemple contient les gestionnaires d’autorisations suivants :

* `ContactIsOwnerAuthorizationHandler`: Garantit qu’un utilisateur peut uniquement modifier ses données.
* `ContactManagerAuthorizationHandler`: Permet aux gestionnaires d’approuver ou de rejeter les contacts.
* `ContactAdministratorsAuthorizationHandler`: Permet aux administrateurs d’approuver ou de refuser des contacts, ainsi que de modifier/supprimer des contacts.

## <a name="prerequisites"></a>Prérequis

Ce didacticiel est avancé. Vous devez connaître les éléments suivants :

* [ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc)
* [Authentification](xref:security/authentication/identity)
* [Confirmation de compte et récupération de mot de passe](xref:security/authentication/accconfirm)
* [Autorisation](xref:security/authorization/introduction)
* [Entity Framework Core](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a>L’application de démarrage et la fin de l’application

[Téléchargez](xref:index#how-to-download-a-sample) l’application [terminée](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) . [Testez](#test-the-completed-app) l’application terminée afin de vous familiariser avec ses fonctionnalités de sécurité.

### <a name="the-starter-app"></a>L’application de démarrage

[Téléchargez](xref:index#how-to-download-a-sample) l’application de [démarrage](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .

Exécutez l’application, appuyez sur le lien **ContactManager** et vérifiez que vous pouvez créer, modifier et supprimer un contact.

## <a name="secure-user-data"></a>Sécuriser les données utilisateur

Les sections suivantes présentent les principales étapes à suivre pour créer l’application de données utilisateur sécurisé. Il peut s’avérer utile de faire référence au projet terminé.

### <a name="tie-the-contact-data-to-the-user"></a>Lier les données de contact à l’utilisateur

Utilisez l' [Identity](xref:security/authentication/identity) ID d’utilisateur ASP.net pour vous assurer que les utilisateurs peuvent modifier leurs données, mais pas les données d’autres utilisateurs. Ajoutez `OwnerID` et `ContactStatus` au `Contact` modèle :

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

`OwnerID` ID de l’utilisateur de la `AspNetUser` table dans la [Identity](xref:security/authentication/identity) base de données. Le `Status` champ détermine si un contact est visible par les utilisateurs généraux.

Créez une nouvelle migration et mettez à jour la base de données :

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a>Ajouter des services de rôle à Identity

Ajoutez [rôles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) pour ajouter des services de rôle :

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=11)]

### <a name="require-authenticated-users"></a>Exiger des utilisateurs authentifiés

Définissez la stratégie d’authentification par défaut pour exiger que les utilisateurs soient authentifiés :

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 Vous pouvez refuser l’authentification au niveau de la Razor page, du contrôleur ou de la méthode d’action avec l' `[AllowAnonymous]` attribut. La définition de la stratégie d’authentification par défaut pour exiger que les utilisateurs soient authentifiés protège les pages et les contrôleurs qui viennent d’être ajoutés Razor . L’authentification requise par défaut est plus sécurisée que le fait de s’appuyer sur de nouveaux contrôleurs et Razor pages pour inclure l' `[Authorize]` attribut.

Ajoutez [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) aux pages d’index, à propos de et contact afin que les utilisateurs anonymes puissent obtenir des informations sur le site avant de s’inscrire.

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a>Configurer le compte de test

La `SeedData` classe crée deux comptes : administrateur et gestionnaire. Utilisez l' [outil secret Manager](xref:security/app-secrets) pour définir un mot de passe pour ces comptes. Définissez le mot de passe à partir du répertoire du projet (répertoire contenant *Program.cs*) :

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

Si aucun mot de passe fort n’est spécifié, une exception est levée lorsque la `SeedData.Initialize` méthode est appelée.

Mettre à jour `Main` pour utiliser le mot de passe de test :

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a>Créer les comptes de test et mettre à jour les contacts

Mettez à jour la `Initialize` méthode dans la `SeedData` classe pour créer les comptes de test :

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

Ajoutez l’ID d’utilisateur administrateur et `ContactStatus` les contacts. Faites de l’un des contacts « soumis » et un « rejeté ». Ajoutez l’ID d’utilisateur et l’État à tous les contacts. Un seul contact est affiché :

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>Créer des gestionnaires d’autorisation propriétaire, responsable et administrateur

Créez un dossier *authorization* et créez une `ContactIsOwnerAuthorizationHandler` classe dans celui-ci. Le `ContactIsOwnerAuthorizationHandler` vérifie que l’utilisateur agissant sur une ressource possède la ressource.

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

`ContactIsOwnerAuthorizationHandler`Contexte des appels [. Réussie](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) si l’utilisateur authentifié actuel est le propriétaire du contact. Les gestionnaires d’autorisations sont généralement :

* Retourne `context.Succeed` lorsque les spécifications sont satisfaites.
* Retourne `Task.CompletedTask` lorsque les spécifications ne sont pas remplies. `Task.CompletedTask` n’est pas un succès ou un échec &mdash; . il permet l’exécution d’autres gestionnaires d’autorisations.

Si vous devez faire échouer explicitement, retournez le [contexte. Échoue](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).

L’application permet aux propriétaires de contacts de modifier/supprimer/créer leurs propres données. `ContactIsOwnerAuthorizationHandler` n’a pas besoin de vérifier l’opération passée dans le paramètre d’exigence.

### <a name="create-a-manager-authorization-handler"></a>Créer un gestionnaire d’autorisations de gestionnaire

Créez une `ContactManagerAuthorizationHandler` classe dans le dossier *authorization* . Le `ContactManagerAuthorizationHandler` vérifie que l’utilisateur agissant sur la ressource est un responsable. Seuls les responsables peuvent approuver ou rejeter les modifications de contenu (nouveaux ou modifiés).

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>Créer un gestionnaire d’autorisations d’administrateur

Créez une `ContactAdministratorsAuthorizationHandler` classe dans le dossier *authorization* . Le `ContactAdministratorsAuthorizationHandler` vérifie que l’utilisateur agissant sur la ressource est un administrateur. L’administrateur peut effectuer toutes les opérations.

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>Inscrire les gestionnaires d’autorisations

Les services qui utilisent Entity Framework Core doivent être inscrits pour l' [injection de dépendances](xref:fundamentals/dependency-injection) à l’aide de [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions). `ContactIsOwnerAuthorizationHandler`Utilise ASP.net Core [Identity](xref:security/authentication/identity) , qui repose sur Entity Framework Core. Inscrire les gestionnaires auprès de la collection de services afin qu’ils soient disponibles pour le `ContactsController` via l' [injection de dépendances](xref:fundamentals/dependency-injection). Ajoutez le code suivant à la fin de `ConfigureServices` :

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

`ContactAdministratorsAuthorizationHandler` et `ContactManagerAuthorizationHandler` sont ajoutés en tant que singletons. Il s’agit de singletons, car ils n’utilisent pas EF et toutes les informations nécessaires sont dans le `Context` paramètre de la `HandleRequirementAsync` méthode.

## <a name="support-authorization"></a>Autorisation du support

Dans cette section, vous allez mettre à jour les Razor pages et ajouter une classe d’exigences d’opérations.

### <a name="review-the-contact-operations-requirements-class"></a>Examiner la classe des exigences relatives aux opérations de contact

Passez en revue la `ContactOperations` classe. Cette classe contient les spécifications prises en charge par l’application :

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a>Créer une classe de base pour les Razor pages contacts

Créez une classe de base qui contient les services utilisés dans les Razor pages de contacts. La classe de base place le code d’initialisation à un emplacement :

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

Le code précédent :

* Ajoute le `IAuthorizationService` service pour accéder aux gestionnaires d’autorisations.
* Ajoute le Identity `UserManager` service.
* Ajoutez la `ApplicationDbContext`.

### <a name="update-the-createmodel"></a>Mettre à jour CreateModel

Mettez à jour le constructeur de modèle de page de création pour utiliser la `DI_BasePageModel` classe de base :

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

Mettez à jour la `CreateModel.OnPostAsync` méthode pour :

* Ajoutez l’ID utilisateur au `Contact` modèle.
* Appelez le gestionnaire d’autorisations pour vérifier que l’utilisateur a l’autorisation de créer des contacts.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a>Mettre à jour IndexModel

Mettez à jour la `OnGetAsync` méthode afin que seuls les contacts approuvés soient affichés pour les utilisateurs généraux :

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a>Mettre à jour EditModel

Ajoutez un gestionnaire d’autorisations pour vérifier que l’utilisateur possède le contact. Étant donné que l’autorisation des ressources est en cours de validation, l' `[Authorize]` attribut n’est pas suffisant. L’application n’a pas accès à la ressource lors de l’évaluation des attributs. L’autorisation basée sur les ressources doit être impérative. Les vérifications doivent être effectuées une fois que l’application a accès à la ressource, soit en la chargeant dans le modèle de page, soit en la chargeant dans le gestionnaire lui-même. Vous accédez fréquemment à la ressource en passant la clé de ressource.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a>Mettre à jour DeleteModel

Mettez à jour le modèle de page de suppression pour utiliser le gestionnaire d’autorisations afin de vérifier que l’utilisateur dispose de l’autorisation de suppression sur le contact.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a>Injecter le service d’autorisation dans les vues

Actuellement, l’interface utilisateur affiche les liens modifier et supprimer pour les contacts que l’utilisateur ne peut pas modifier.

Injecter le service d’autorisation dans le fichier *views/_ViewImports. cshtml* afin qu’il soit disponible pour tous les affichages :

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

Le balisage précédent ajoute plusieurs `using` instructions.

Mettez à jour les liens **modifier** et **supprimer** dans *pages/contacts/index. cshtml* afin qu’ils soient affichés uniquement pour les utilisateurs disposant des autorisations appropriées :

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> Le masquage des liens des utilisateurs qui n’ont pas l’autorisation de modifier des données ne sécurise pas l’application. Le masquage des liens rend l’application plus conviviale en affichant uniquement des liens valides. Les utilisateurs peuvent pirater les URL générées pour appeler des opérations de modification et de suppression sur les données qu’ils ne possèdent pas. La Razor page ou le contrôleur doit appliquer les vérifications d’accès pour sécuriser les données.

### <a name="update-details"></a>Mettre à jour les détails

Mettez à jour la vue Détails pour permettre aux responsables d’approuver ou de rejeter les contacts :

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

Mettez à jour le modèle de page de détails :

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a>Ajouter ou supprimer un utilisateur dans un rôle

Consultez [ce numéro](https://github.com/dotnet/AspNetCore.Docs/issues/8502) pour plus d’informations sur :

* Suppression des privilèges d’un utilisateur. Par exemple, la désactivation d’un utilisateur dans une application de conversation.
* Ajout de privilèges à un utilisateur.

## <a name="test-the-completed-app"></a>Tester l’application terminée

Si vous n’avez pas encore défini de mot de passe pour les comptes d’utilisateurs amorcés, utilisez l' [outil secret Manager](xref:security/app-secrets#secret-manager) pour définir un mot de passe :

* Choisissez un mot de passe fort : utilisez au moins huit caractères et au moins un caractère majuscule, un chiffre et un symbole. Par exemple, `Passw0rd!` répond aux exigences de mot de passe fort.
* Exécutez la commande suivante à partir du dossier du projet, où `<PW>` est le mot de passe :

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* Supprimer et mettre à jour la base de données

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* Redémarrez l’application pour amorcer la base de données.

Un moyen simple de tester l’application terminée consiste à lancer trois navigateurs différents (ou des sessions incognito/InPrivate). Dans un navigateur, inscrivez un nouvel utilisateur (par exemple, `test@example.com` ). Connectez-vous à chaque navigateur avec un autre utilisateur. Vérifiez les opérations suivantes :

* Les utilisateurs inscrits peuvent afficher toutes les données de contact approuvées.
* Les utilisateurs inscrits peuvent modifier/supprimer leurs propres données.
* Les responsables peuvent approuver/rejeter les données de contact. La `Details` vue affiche les boutons **approuver** et **rejeter** .
* Les administrateurs peuvent approuver/refuser et modifier/supprimer toutes les données.

| Utilisateur                | Amorcé par l’application | Options                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | Non                | Modifiez/supprimez les données.                |
| manager@contoso.com | Oui               | Approuver/refuser et modifier/supprimer des données. |
| admin@contoso.com   | Oui               | Approuver/refuser et modifier/supprimer toutes les données. |

Créez un contact dans le navigateur de l’administrateur. Copiez l’URL de la suppression et de la modification à partir du contact de l’administrateur. Collez ces liens dans le navigateur de l’utilisateur de test pour vérifier que l’utilisateur de test ne peut pas effectuer ces opérations.

## <a name="create-the-starter-app"></a>Créer l’application de démarrage

* Créer une Razor application pages nommée « ContactManager »
  * Créez l’application avec des **comptes d’utilisateur individuels**.
  * Nommez-la « ContactManager » pour que l’espace de noms corresponde à l’espace de noms utilisé dans l’exemple.
  * `-uld` spécifie la base de données locale au lieu de SQLite

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* Ajouter des *modèles/contact. cs*:

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* Structurez le `Contact` modèle.
* Créez la migration initiale et mettez à jour la base de données :

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* Mettez à jour l’ancre **ContactManager** dans le fichier *pages/_Layout. cshtml* :

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* Tester l’application en créant, en modifiant et en supprimant un contact

### <a name="seed-the-database"></a>Amorcer la base de données

Ajoutez la classe [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) au dossier de *données* .

Appeler `SeedData.Initialize` à partir de `Main` :

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

Vérifiez que l’application a amorcé la base de données. Si la base de coordonnées contient des lignes, la méthode Seed ne s’exécute pas.

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a>Ressources supplémentaires

* [Créer une application Web .NET Core et SQL Database dans Azure App Service](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [ASP.net Core laboratoire d’autorisation](https://github.com/blowdart/AspNetAuthorizationWorkshop). Ce laboratoire aborde plus en détail les fonctionnalités de sécurité présentées dans ce didacticiel.
* <xref:security/authorization/introduction>
* [Autorisation basée sur une stratégie personnalisée](xref:security/authorization/policies)
