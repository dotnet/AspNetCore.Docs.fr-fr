---
title: Créer une application ASP.NET Core avec les données utilisateur protégées par l’autorisation
author: rick-anderson
description: 'Découvrez comment créer une application Web ASP.NET Core avec les données utilisateur protégées par l’autorisation. Comprend le protocole HTTPs, l’authentification, la sécurité ASP.NET Core Identity .'
ms.author: riande
ms.date: 7/18/2020
ms.custom: mvc, seodec18
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authorization/secure-data
ms.openlocfilehash: accfd46fa72c33976f8af2a39267c993447e036e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051939"
---
# <a name="create-an-aspnet-core-web-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="1c7dc-104">Créer une application Web ASP.NET Core avec les données utilisateur protégées par l’autorisation</span><span class="sxs-lookup"><span data-stu-id="1c7dc-104">Create an ASP.NET Core web app with user data protected by authorization</span></span>

<span data-ttu-id="1c7dc-105">Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="1c7dc-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="1c7dc-106">Voir [ce PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span><span class="sxs-lookup"><span data-stu-id="1c7dc-106">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1c7dc-107">Ce didacticiel montre comment créer une application Web ASP.NET Core avec les données utilisateur protégées par l’autorisation.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-107">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="1c7dc-108">Il affiche la liste des contacts que les utilisateurs authentifiés (inscrits) ont créés.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-108">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="1c7dc-109">Il existe trois groupes de sécurité :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-109">There are three security groups:</span></span>

* <span data-ttu-id="1c7dc-110">Les **utilisateurs inscrits** peuvent afficher toutes les données approuvées et peuvent modifier/supprimer leurs propres données.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-110">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="1c7dc-111">Les **responsables** peuvent approuver ou rejeter les données de contact.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-111">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="1c7dc-112">Seuls les contacts approuvés sont visibles pour les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-112">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="1c7dc-113">**Les administrateurs** peuvent approuver/refuser et modifier/supprimer des données.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-113">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="1c7dc-114">Les images de ce document ne correspondent pas exactement aux modèles les plus récents.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-114">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="1c7dc-115">Dans l’image suivante, l’utilisateur Rick ( `rick@example.com` ) est connecté.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-115">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="1c7dc-116">Rick peut uniquement afficher les contacts approuvés et **modifier** / **supprimer** / **créer** des liens pour ses contacts.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-116">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="1c7dc-117">Seul le dernier enregistrement créé par Rick affiche des liens **modifier** et **supprimer** .</span><span class="sxs-lookup"><span data-stu-id="1c7dc-117">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="1c7dc-118">Les autres utilisateurs ne verront pas le dernier enregistrement jusqu’à ce qu’un responsable ou un administrateur change l’État en « approuvé ».</span><span class="sxs-lookup"><span data-stu-id="1c7dc-118">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Capture d’écran montrant que Rick est connecté](secure-data/_static/rick.png)

<span data-ttu-id="1c7dc-120">Dans l’image suivante, `manager@contoso.com` est connecté et dans le rôle du responsable :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-120">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Capture d’écran montrant la manager@contoso.com connexion](secure-data/_static/manager1.png)

<span data-ttu-id="1c7dc-122">L’illustration suivante montre la vue des détails du gestionnaire d’un contact :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-122">The following image shows the managers details view of a contact:</span></span>

![Vue du gestionnaire d’un contact](secure-data/_static/manager.png)

<span data-ttu-id="1c7dc-124">Les boutons **approuver** et **rejeter** s’affichent uniquement pour les responsables et les administrateurs.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-124">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="1c7dc-125">Dans l’image suivante, `admin@contoso.com` est connecté et dans le rôle de l’administrateur :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-125">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Capture d’écran montrant la admin@contoso.com connexion](secure-data/_static/admin.png)

<span data-ttu-id="1c7dc-127">L’administrateur dispose de tous les privilèges.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-127">The administrator has all privileges.</span></span> <span data-ttu-id="1c7dc-128">Elle peut lire/modifier/supprimer un contact et modifier l’état des contacts.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-128">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="1c7dc-129">L’application a été créée par la [génération](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) de modèles automatique du `Contact` modèle suivant :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-129">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="1c7dc-130">L’exemple contient les gestionnaires d’autorisations suivants :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-130">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="1c7dc-131">`ContactIsOwnerAuthorizationHandler`: Garantit qu’un utilisateur peut uniquement modifier ses données.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-131">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="1c7dc-132">`ContactManagerAuthorizationHandler`: Permet aux gestionnaires d’approuver ou de rejeter les contacts.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-132">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="1c7dc-133">`ContactAdministratorsAuthorizationHandler`: Permet aux administrateurs d’approuver ou de refuser des contacts, ainsi que de modifier/supprimer des contacts.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-133">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1c7dc-134">Prérequis</span><span class="sxs-lookup"><span data-stu-id="1c7dc-134">Prerequisites</span></span>

<span data-ttu-id="1c7dc-135">Ce didacticiel est avancé.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-135">This tutorial is advanced.</span></span> <span data-ttu-id="1c7dc-136">Vous devez connaître les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-136">You should be familiar with:</span></span>

* [<span data-ttu-id="1c7dc-137">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1c7dc-137">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="1c7dc-138">Authentification</span><span class="sxs-lookup"><span data-stu-id="1c7dc-138">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="1c7dc-139">Confirmation de compte et récupération de mot de passe</span><span class="sxs-lookup"><span data-stu-id="1c7dc-139">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="1c7dc-140">Autorisation</span><span class="sxs-lookup"><span data-stu-id="1c7dc-140">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="1c7dc-141">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="1c7dc-141">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="1c7dc-142">L’application de démarrage et la fin de l’application</span><span class="sxs-lookup"><span data-stu-id="1c7dc-142">The starter and completed app</span></span>

<span data-ttu-id="1c7dc-143">[Téléchargez](xref:index#how-to-download-a-sample) l’application [terminée](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) .</span><span class="sxs-lookup"><span data-stu-id="1c7dc-143">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="1c7dc-144">[Testez](#test-the-completed-app) l’application terminée afin de vous familiariser avec ses fonctionnalités de sécurité.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-144">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="1c7dc-145">L’application de démarrage</span><span class="sxs-lookup"><span data-stu-id="1c7dc-145">The starter app</span></span>

<span data-ttu-id="1c7dc-146">[Téléchargez](xref:index#how-to-download-a-sample) l’application de [démarrage](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .</span><span class="sxs-lookup"><span data-stu-id="1c7dc-146">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="1c7dc-147">Exécutez l’application, appuyez sur le lien **ContactManager** et vérifiez que vous pouvez créer, modifier et supprimer un contact.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-147">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="1c7dc-148">Sécuriser les données utilisateur</span><span class="sxs-lookup"><span data-stu-id="1c7dc-148">Secure user data</span></span>

<span data-ttu-id="1c7dc-149">Les sections suivantes présentent les principales étapes à suivre pour créer l’application de données utilisateur sécurisé.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-149">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="1c7dc-150">Il peut s’avérer utile de faire référence au projet terminé.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-150">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="1c7dc-151">Lier les données de contact à l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="1c7dc-151">Tie the contact data to the user</span></span>

<span data-ttu-id="1c7dc-152">Utilisez l' [Identity](xref:security/authentication/identity) ID d’utilisateur ASP.net pour vous assurer que les utilisateurs peuvent modifier leurs données, mais pas les données d’autres utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-152">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="1c7dc-153">Ajoutez `OwnerID` et `ContactStatus` au `Contact` modèle :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-153">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="1c7dc-154">`OwnerID` ID de l’utilisateur de la `AspNetUser` table dans la [Identity](xref:security/authentication/identity) base de données.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-154">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="1c7dc-155">Le `Status` champ détermine si un contact est visible par les utilisateurs généraux.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-155">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="1c7dc-156">Créez une nouvelle migration et mettez à jour la base de données :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-156">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="1c7dc-157">Ajouter des services de rôle à Identity</span><span class="sxs-lookup"><span data-stu-id="1c7dc-157">Add Role services to Identity</span></span>

<span data-ttu-id="1c7dc-158">Ajoutez [rôles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) pour ajouter des services de rôle :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-158">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

<a name="rau"></a>

### <a name="require-authenticated-users"></a><span data-ttu-id="1c7dc-159">Exiger des utilisateurs authentifiés</span><span class="sxs-lookup"><span data-stu-id="1c7dc-159">Require authenticated users</span></span>

<span data-ttu-id="1c7dc-160">Définissez la stratégie d’authentification de secours pour exiger que les utilisateurs soient authentifiés :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-160">Set the fallback authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=13-99)]

<span data-ttu-id="1c7dc-161">Le code en surbrillance précédent définit la [stratégie d’authentification de secours](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span><span class="sxs-lookup"><span data-stu-id="1c7dc-161">The preceding highlighted code sets the [fallback authentication policy](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span></span> <span data-ttu-id="1c7dc-162">La stratégie d’authentification de secours exige que \* *_tous_* les utilisateurs soient authentifiés, à l’exception des Razor pages, des contrôleurs ou des méthodes d’action avec un attribut d’authentification.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-162">The fallback authentication policy requires \* *_all_* _ users to be authenticated, except for Razor Pages, controllers, or action methods with an authentication attribute.</span></span> <span data-ttu-id="1c7dc-163">Par exemple, Razor les pages, les contrôleurs ou les méthodes d’action avec `[AllowAnonymous]` ou `[Authorize(PolicyName="MyPolicy")]` utilisent l’attribut d’authentification appliqué plutôt que la stratégie d’authentification de secours.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-163">For example, Razor Pages, controllers, or action methods with `[AllowAnonymous]` or `[Authorize(PolicyName="MyPolicy")]` use the applied authentication attribute rather than the fallback authentication policy.</span></span>

<span data-ttu-id="1c7dc-164">Stratégie d’authentification de secours :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-164">The fallback authentication policy:</span></span>

<span data-ttu-id="1c7dc-165">_ Est appliqué à toutes les demandes qui ne spécifient pas explicitement une stratégie d’authentification.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-165">_ Is applied to all requests that do not explicitly specify an authentication policy.</span></span> <span data-ttu-id="1c7dc-166">Pour les demandes traitées par le routage de point de terminaison, cela inclut tout point de terminaison qui ne spécifie pas d’attribut d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-166">For requests served by endpoint routing, this would include any endpoint that does not specify an authorization attribute.</span></span> <span data-ttu-id="1c7dc-167">Pour les demandes servies par un autre intergiciel après l’intergiciel (middleware) d’autorisation, par exemple des [fichiers statiques](xref:fundamentals/static-files), cela applique la stratégie à toutes les demandes.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-167">For requests served by other middleware after the authorization middleware, such as [static files](xref:fundamentals/static-files), this would apply the policy to all requests.</span></span>

<span data-ttu-id="1c7dc-168">La définition de la stratégie d’authentification de secours pour exiger que les utilisateurs soient authentifiés protège les pages et les contrôleurs qui viennent d’être ajoutés Razor .</span><span class="sxs-lookup"><span data-stu-id="1c7dc-168">Setting the fallback authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="1c7dc-169">L’authentification requise par défaut est plus sécurisée que le fait de s’appuyer sur de nouveaux contrôleurs et Razor pages pour inclure l' `[Authorize]` attribut.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-169">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="1c7dc-170">La <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> classe contient également <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="1c7dc-170">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> class also contains <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType>.</span></span> <span data-ttu-id="1c7dc-171">`DefaultPolicy`Est la stratégie utilisée avec l' `[Authorize]` attribut quand aucune stratégie n’est spécifiée.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-171">The `DefaultPolicy` is the policy used with the `[Authorize]` attribute when no policy is specified.</span></span> <span data-ttu-id="1c7dc-172">`[Authorize]` ne contient pas de stratégie nommée, contrairement à `[Authorize(PolicyName="MyPolicy")]` .</span><span class="sxs-lookup"><span data-stu-id="1c7dc-172">`[Authorize]` doesn't contain a named policy, unlike `[Authorize(PolicyName="MyPolicy")]`.</span></span>

<span data-ttu-id="1c7dc-173">Pour plus d’informations sur les stratégies, consultez <xref:security/authorization/policies> .</span><span class="sxs-lookup"><span data-stu-id="1c7dc-173">For more information on policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="1c7dc-174">Pour que les contrôleurs et Razor les pages MVC requièrent que tous les utilisateurs soient authentifiés, vous pouvez également ajouter un filtre d’autorisation :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-174">An alternative way for MVC controllers and Razor Pages to require all users be authenticated is adding an authorization filter:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup2.cs?name=snippet&highlight=14-99)]

<span data-ttu-id="1c7dc-175">Le code précédent utilise un filtre d’autorisation, la définition de la stratégie de secours utilise le routage du point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-175">The preceding code uses an authorization filter, setting the fallback policy uses endpoint routing.</span></span> <span data-ttu-id="1c7dc-176">La définition de la stratégie de secours est la méthode recommandée pour exiger que tous les utilisateurs soient authentifiés.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-176">Setting the fallback policy is the preferred way to require all users be authenticated.</span></span>

<span data-ttu-id="1c7dc-177">Ajoutez [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) aux `Index` pages et `Privacy` afin que les utilisateurs anonymes puissent obtenir des informations sur le site avant de s’inscrire :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-177">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the `Index` and `Privacy` pages so anonymous users can get information about the site before they register:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="1c7dc-178">Configurer le compte de test</span><span class="sxs-lookup"><span data-stu-id="1c7dc-178">Configure the test account</span></span>

<span data-ttu-id="1c7dc-179">La `SeedData` classe crée deux comptes : administrateur et gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-179">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="1c7dc-180">Utilisez l' [outil secret Manager](xref:security/app-secrets) pour définir un mot de passe pour ces comptes.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-180">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="1c7dc-181">Définissez le mot de passe à partir du répertoire du projet (répertoire contenant *Program.cs* ) :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-181">Set the password from the project directory (the directory containing *Program.cs* ):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="1c7dc-182">Si aucun mot de passe fort n’est spécifié, une exception est levée lorsque la `SeedData.Initialize` méthode est appelée.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-182">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="1c7dc-183">Mettre à jour `Main` pour utiliser le mot de passe de test :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-183">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="1c7dc-184">Créer les comptes de test et mettre à jour les contacts</span><span class="sxs-lookup"><span data-stu-id="1c7dc-184">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="1c7dc-185">Mettez à jour la `Initialize` méthode dans la `SeedData` classe pour créer les comptes de test :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-185">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="1c7dc-186">Ajoutez l’ID d’utilisateur administrateur et `ContactStatus` les contacts.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-186">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="1c7dc-187">Faites de l’un des contacts « soumis » et un « rejeté ».</span><span class="sxs-lookup"><span data-stu-id="1c7dc-187">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="1c7dc-188">Ajoutez l’ID d’utilisateur et l’État à tous les contacts.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-188">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="1c7dc-189">Un seul contact est affiché :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-189">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="1c7dc-190">Créer des gestionnaires d’autorisation propriétaire, responsable et administrateur</span><span class="sxs-lookup"><span data-stu-id="1c7dc-190">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="1c7dc-191">Créez une `ContactIsOwnerAuthorizationHandler` classe dans le dossier *authorization* .</span><span class="sxs-lookup"><span data-stu-id="1c7dc-191">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="1c7dc-192">Le `ContactIsOwnerAuthorizationHandler` vérifie que l’utilisateur agissant sur une ressource possède la ressource.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-192">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="1c7dc-193">`ContactIsOwnerAuthorizationHandler`Contexte des appels [. Réussie](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) si l’utilisateur authentifié actuel est le propriétaire du contact.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-193">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="1c7dc-194">Les gestionnaires d’autorisations sont généralement :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-194">Authorization handlers generally:</span></span>

* <span data-ttu-id="1c7dc-195">Retourne `context.Succeed` lorsque les spécifications sont satisfaites.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-195">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="1c7dc-196">Retourne `Task.CompletedTask` lorsque les spécifications ne sont pas remplies.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-196">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="1c7dc-197">`Task.CompletedTask` n’est pas un succès ou un échec &mdash; . il permet l’exécution d’autres gestionnaires d’autorisations.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-197">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="1c7dc-198">Si vous devez faire échouer explicitement, retournez le [contexte. Échoue](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="1c7dc-198">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="1c7dc-199">L’application permet aux propriétaires de contacts de modifier/supprimer/créer leurs propres données.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-199">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="1c7dc-200">`ContactIsOwnerAuthorizationHandler` n’a pas besoin de vérifier l’opération passée dans le paramètre d’exigence.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-200">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="1c7dc-201">Créer un gestionnaire d’autorisations de gestionnaire</span><span class="sxs-lookup"><span data-stu-id="1c7dc-201">Create a manager authorization handler</span></span>

<span data-ttu-id="1c7dc-202">Créez une `ContactManagerAuthorizationHandler` classe dans le dossier *authorization* .</span><span class="sxs-lookup"><span data-stu-id="1c7dc-202">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="1c7dc-203">Le `ContactManagerAuthorizationHandler` vérifie que l’utilisateur agissant sur la ressource est un responsable.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-203">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="1c7dc-204">Seuls les responsables peuvent approuver ou rejeter les modifications de contenu (nouveaux ou modifiés).</span><span class="sxs-lookup"><span data-stu-id="1c7dc-204">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="1c7dc-205">Créer un gestionnaire d’autorisations d’administrateur</span><span class="sxs-lookup"><span data-stu-id="1c7dc-205">Create an administrator authorization handler</span></span>

<span data-ttu-id="1c7dc-206">Créez une `ContactAdministratorsAuthorizationHandler` classe dans le dossier *authorization* .</span><span class="sxs-lookup"><span data-stu-id="1c7dc-206">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="1c7dc-207">Le `ContactAdministratorsAuthorizationHandler` vérifie que l’utilisateur agissant sur la ressource est un administrateur.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-207">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="1c7dc-208">L’administrateur peut effectuer toutes les opérations.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-208">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="1c7dc-209">Inscrire les gestionnaires d’autorisations</span><span class="sxs-lookup"><span data-stu-id="1c7dc-209">Register the authorization handlers</span></span>

<span data-ttu-id="1c7dc-210">Les services qui utilisent Entity Framework Core doivent être inscrits pour l' [injection de dépendances](xref:fundamentals/dependency-injection) à l’aide de [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="1c7dc-210">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="1c7dc-211">`ContactIsOwnerAuthorizationHandler`Utilise ASP.net Core [Identity](xref:security/authentication/identity) , qui repose sur Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-211">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="1c7dc-212">Inscrire les gestionnaires auprès de la collection de services afin qu’ils soient disponibles pour le `ContactsController` via l' [injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="1c7dc-212">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="1c7dc-213">Ajoutez le code suivant à la fin de `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-213">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="1c7dc-214">`ContactAdministratorsAuthorizationHandler` et `ContactManagerAuthorizationHandler` sont ajoutés en tant que singletons.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-214">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="1c7dc-215">Il s’agit de singletons, car ils n’utilisent pas EF et toutes les informations nécessaires sont dans le `Context` paramètre de la `HandleRequirementAsync` méthode.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-215">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="1c7dc-216">Autorisation du support</span><span class="sxs-lookup"><span data-stu-id="1c7dc-216">Support authorization</span></span>

<span data-ttu-id="1c7dc-217">Dans cette section, vous allez mettre à jour les Razor pages et ajouter une classe d’exigences d’opérations.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-217">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="1c7dc-218">Examiner la classe des exigences relatives aux opérations de contact</span><span class="sxs-lookup"><span data-stu-id="1c7dc-218">Review the contact operations requirements class</span></span>

<span data-ttu-id="1c7dc-219">Passez en revue la `ContactOperations` classe.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-219">Review the `ContactOperations` class.</span></span> <span data-ttu-id="1c7dc-220">Cette classe contient les spécifications prises en charge par l’application :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-220">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="1c7dc-221">Créer une classe de base pour les Razor pages contacts</span><span class="sxs-lookup"><span data-stu-id="1c7dc-221">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="1c7dc-222">Créez une classe de base qui contient les services utilisés dans les Razor pages de contacts.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-222">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="1c7dc-223">La classe de base place le code d’initialisation à un emplacement :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-223">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="1c7dc-224">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-224">The preceding code:</span></span>

* <span data-ttu-id="1c7dc-225">Ajoute le `IAuthorizationService` service pour accéder aux gestionnaires d’autorisations.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-225">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="1c7dc-226">Ajoute le Identity `UserManager` service.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-226">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="1c7dc-227">Ajoutez la `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-227">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="1c7dc-228">Mettre à jour CreateModel</span><span class="sxs-lookup"><span data-stu-id="1c7dc-228">Update the CreateModel</span></span>

<span data-ttu-id="1c7dc-229">Mettez à jour le constructeur de modèle de page de création pour utiliser la `DI_BasePageModel` classe de base :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-229">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="1c7dc-230">Mettez à jour la `CreateModel.OnPostAsync` méthode pour :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-230">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="1c7dc-231">Ajoutez l’ID utilisateur au `Contact` modèle.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-231">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="1c7dc-232">Appelez le gestionnaire d’autorisations pour vérifier que l’utilisateur a l’autorisation de créer des contacts.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-232">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="1c7dc-233">Mettre à jour IndexModel</span><span class="sxs-lookup"><span data-stu-id="1c7dc-233">Update the IndexModel</span></span>

<span data-ttu-id="1c7dc-234">Mettez à jour la `OnGetAsync` méthode afin que seuls les contacts approuvés soient affichés pour les utilisateurs généraux :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-234">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="1c7dc-235">Mettre à jour EditModel</span><span class="sxs-lookup"><span data-stu-id="1c7dc-235">Update the EditModel</span></span>

<span data-ttu-id="1c7dc-236">Ajoutez un gestionnaire d’autorisations pour vérifier que l’utilisateur possède le contact.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-236">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="1c7dc-237">Étant donné que l’autorisation des ressources est en cours de validation, l' `[Authorize]` attribut n’est pas suffisant.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-237">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="1c7dc-238">L’application n’a pas accès à la ressource lors de l’évaluation des attributs.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-238">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="1c7dc-239">L’autorisation basée sur les ressources doit être impérative.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-239">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="1c7dc-240">Les vérifications doivent être effectuées une fois que l’application a accès à la ressource, soit en la chargeant dans le modèle de page, soit en la chargeant dans le gestionnaire lui-même.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-240">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="1c7dc-241">Vous accédez fréquemment à la ressource en passant la clé de ressource.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-241">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="1c7dc-242">Mettre à jour DeleteModel</span><span class="sxs-lookup"><span data-stu-id="1c7dc-242">Update the DeleteModel</span></span>

<span data-ttu-id="1c7dc-243">Mettez à jour le modèle de page de suppression pour utiliser le gestionnaire d’autorisations afin de vérifier que l’utilisateur dispose de l’autorisation de suppression sur le contact.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-243">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="1c7dc-244">Injecter le service d’autorisation dans les vues</span><span class="sxs-lookup"><span data-stu-id="1c7dc-244">Inject the authorization service into the views</span></span>

<span data-ttu-id="1c7dc-245">Actuellement, l’interface utilisateur affiche les liens modifier et supprimer pour les contacts que l’utilisateur ne peut pas modifier.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-245">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="1c7dc-246">Injecter le service d’autorisation dans le fichier *pages/_ViewImports. cshtml* afin qu’il soit disponible pour tous les affichages :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-246">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="1c7dc-247">Le balisage précédent ajoute plusieurs `using` instructions.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-247">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="1c7dc-248">Mettez à jour les liens **modifier** et **supprimer** dans *pages/contacts/index. cshtml* afin qu’ils soient affichés uniquement pour les utilisateurs disposant des autorisations appropriées :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-248">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="1c7dc-249">Le masquage des liens des utilisateurs qui n’ont pas l’autorisation de modifier des données ne sécurise pas l’application.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-249">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="1c7dc-250">Le masquage des liens rend l’application plus conviviale en affichant uniquement des liens valides.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-250">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="1c7dc-251">Les utilisateurs peuvent pirater les URL générées pour appeler des opérations de modification et de suppression sur les données qu’ils ne possèdent pas.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-251">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="1c7dc-252">La Razor page ou le contrôleur doit appliquer les vérifications d’accès pour sécuriser les données.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-252">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="1c7dc-253">Mettre à jour les détails</span><span class="sxs-lookup"><span data-stu-id="1c7dc-253">Update Details</span></span>

<span data-ttu-id="1c7dc-254">Mettez à jour la vue Détails pour permettre aux responsables d’approuver ou de rejeter les contacts :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-254">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="1c7dc-255">Mettez à jour le modèle de page de détails :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-255">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="1c7dc-256">Ajouter ou supprimer un utilisateur dans un rôle</span><span class="sxs-lookup"><span data-stu-id="1c7dc-256">Add or remove a user to a role</span></span>

<span data-ttu-id="1c7dc-257">Consultez [ce numéro](https://github.com/dotnet/AspNetCore.Docs/issues/8502) pour plus d’informations sur :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-257">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="1c7dc-258">Suppression des privilèges d’un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-258">Removing privileges from a user.</span></span> <span data-ttu-id="1c7dc-259">Par exemple, la désactivation d’un utilisateur dans une application de conversation.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-259">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="1c7dc-260">Ajout de privilèges à un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-260">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="1c7dc-261">Différences entre Challenge et interdire</span><span class="sxs-lookup"><span data-stu-id="1c7dc-261">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="1c7dc-262">Cette application définit la stratégie par défaut pour [exiger des utilisateurs authentifiés](#rau).</span><span class="sxs-lookup"><span data-stu-id="1c7dc-262">This app sets the default policy to [require authenticated users](#rau).</span></span> <span data-ttu-id="1c7dc-263">Le code suivant autorise les utilisateurs anonymes.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-263">The following code allows anonymous users.</span></span> <span data-ttu-id="1c7dc-264">Les utilisateurs anonymes sont autorisés à afficher les différences entre la stimulation et l’interdiction.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-264">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="1c7dc-265">Dans le code précédent :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-265">In the preceding code:</span></span>

* <span data-ttu-id="1c7dc-266">Lorsque l’utilisateur n’est **pas** authentifié, un `ChallengeResult` est retourné.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-266">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="1c7dc-267">Lorsqu’un `ChallengeResult` est retourné, l’utilisateur est redirigé vers la page de connexion.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-267">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="1c7dc-268">Lorsque l’utilisateur est authentifié, mais n’est pas autorisé, un `ForbidResult` est retourné.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-268">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="1c7dc-269">Lorsqu’un `ForbidResult` est retourné, l’utilisateur est redirigé vers la page accès refusé.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-269">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="1c7dc-270">Tester l’application terminée</span><span class="sxs-lookup"><span data-stu-id="1c7dc-270">Test the completed app</span></span>

<span data-ttu-id="1c7dc-271">Si vous n’avez pas encore défini de mot de passe pour les comptes d’utilisateurs amorcés, utilisez l' [outil secret Manager](xref:security/app-secrets#secret-manager) pour définir un mot de passe :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-271">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="1c7dc-272">Choisissez un mot de passe fort : utilisez au moins huit caractères et au moins un caractère majuscule, un chiffre et un symbole.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-272">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="1c7dc-273">Par exemple, `Passw0rd!` répond aux exigences de mot de passe fort.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-273">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="1c7dc-274">Exécutez la commande suivante à partir du dossier du projet, où `<PW>` est le mot de passe :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-274">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="1c7dc-275">Si l’application a des contacts :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-275">If the app has contacts:</span></span>

* <span data-ttu-id="1c7dc-276">Supprimez tous les enregistrements de la `Contact` table.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-276">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="1c7dc-277">Redémarrez l’application pour amorcer la base de données.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-277">Restart the app to seed the database.</span></span>

<span data-ttu-id="1c7dc-278">Un moyen simple de tester l’application terminée consiste à lancer trois navigateurs différents (ou des sessions incognito/InPrivate).</span><span class="sxs-lookup"><span data-stu-id="1c7dc-278">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="1c7dc-279">Dans un navigateur, inscrivez un nouvel utilisateur (par exemple, `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="1c7dc-279">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="1c7dc-280">Connectez-vous à chaque navigateur avec un autre utilisateur.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-280">Sign in to each browser with a different user.</span></span> <span data-ttu-id="1c7dc-281">Vérifiez les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-281">Verify the following operations:</span></span>

* <span data-ttu-id="1c7dc-282">Les utilisateurs inscrits peuvent afficher toutes les données de contact approuvées.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-282">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="1c7dc-283">Les utilisateurs inscrits peuvent modifier/supprimer leurs propres données.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-283">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="1c7dc-284">Les responsables peuvent approuver/rejeter les données de contact.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-284">Managers can approve/reject contact data.</span></span> <span data-ttu-id="1c7dc-285">La `Details` vue affiche les boutons **approuver** et **rejeter** .</span><span class="sxs-lookup"><span data-stu-id="1c7dc-285">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="1c7dc-286">Les administrateurs peuvent approuver/refuser et modifier/supprimer toutes les données.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-286">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="1c7dc-287">Utilisateur</span><span class="sxs-lookup"><span data-stu-id="1c7dc-287">User</span></span>                | <span data-ttu-id="1c7dc-288">Amorcé par l’application</span><span class="sxs-lookup"><span data-stu-id="1c7dc-288">Seeded by the app</span></span> | <span data-ttu-id="1c7dc-289">Options</span><span class="sxs-lookup"><span data-stu-id="1c7dc-289">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="1c7dc-290">No</span><span class="sxs-lookup"><span data-stu-id="1c7dc-290">No</span></span>                | <span data-ttu-id="1c7dc-291">Modifiez/supprimez les données.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-291">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="1c7dc-292">Yes</span><span class="sxs-lookup"><span data-stu-id="1c7dc-292">Yes</span></span>               | <span data-ttu-id="1c7dc-293">Approuver/refuser et modifier/supprimer des données.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-293">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="1c7dc-294">Yes</span><span class="sxs-lookup"><span data-stu-id="1c7dc-294">Yes</span></span>               | <span data-ttu-id="1c7dc-295">Approuver/refuser et modifier/supprimer toutes les données.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-295">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="1c7dc-296">Créez un contact dans le navigateur de l’administrateur.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-296">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="1c7dc-297">Copiez l’URL de la suppression et de la modification à partir du contact de l’administrateur.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-297">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="1c7dc-298">Collez ces liens dans le navigateur de l’utilisateur de test pour vérifier que l’utilisateur de test ne peut pas effectuer ces opérations.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-298">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="1c7dc-299">Créer l’application de démarrage</span><span class="sxs-lookup"><span data-stu-id="1c7dc-299">Create the starter app</span></span>

* <span data-ttu-id="1c7dc-300">Créer une Razor application pages nommée « ContactManager »</span><span class="sxs-lookup"><span data-stu-id="1c7dc-300">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="1c7dc-301">Créez l’application avec des **comptes d’utilisateur individuels** .</span><span class="sxs-lookup"><span data-stu-id="1c7dc-301">Create the app with **Individual User Accounts** .</span></span>
  * <span data-ttu-id="1c7dc-302">Nommez-la « ContactManager » pour que l’espace de noms corresponde à l’espace de noms utilisé dans l’exemple.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-302">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="1c7dc-303">`-uld` spécifie la base de données locale au lieu de SQLite</span><span class="sxs-lookup"><span data-stu-id="1c7dc-303">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="1c7dc-304">Ajouter des *modèles/contact. cs* :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-304">Add *Models/Contact.cs* :</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="1c7dc-305">Structurez le `Contact` modèle.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-305">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="1c7dc-306">Créez la migration initiale et mettez à jour la base de données :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-306">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="1c7dc-307">Si vous rencontrez un bogue avec la `dotnet aspnet-codegenerator razorpage` commande, consultez [ce problème GitHub](https://github.com/aspnet/Scaffolding/issues/984).</span><span class="sxs-lookup"><span data-stu-id="1c7dc-307">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="1c7dc-308">Mettez à jour l’ancre **ContactManager** dans le fichier *pages/shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-308">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="1c7dc-309">Tester l’application en créant, en modifiant et en supprimant un contact</span><span class="sxs-lookup"><span data-stu-id="1c7dc-309">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="1c7dc-310">Amorcer la base de données</span><span class="sxs-lookup"><span data-stu-id="1c7dc-310">Seed the database</span></span>

<span data-ttu-id="1c7dc-311">Ajoutez la classe [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) au dossier *Data* :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-311">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="1c7dc-312">Appeler `SeedData.Initialize` à partir de `Main` :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-312">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="1c7dc-313">Vérifiez que l’application a amorcé la base de données.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-313">Test that the app seeded the database.</span></span> <span data-ttu-id="1c7dc-314">Si la base de coordonnées contient des lignes, la méthode Seed ne s’exécute pas.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-314">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="1c7dc-315">Ce didacticiel montre comment créer une application Web ASP.NET Core avec les données utilisateur protégées par l’autorisation.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-315">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="1c7dc-316">Il affiche la liste des contacts que les utilisateurs authentifiés (inscrits) ont créés.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-316">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="1c7dc-317">Il existe trois groupes de sécurité :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-317">There are three security groups:</span></span>

* <span data-ttu-id="1c7dc-318">Les **utilisateurs inscrits** peuvent afficher toutes les données approuvées et peuvent modifier/supprimer leurs propres données.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-318">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="1c7dc-319">Les **responsables** peuvent approuver ou rejeter les données de contact.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-319">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="1c7dc-320">Seuls les contacts approuvés sont visibles pour les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-320">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="1c7dc-321">**Les administrateurs** peuvent approuver/refuser et modifier/supprimer des données.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-321">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="1c7dc-322">Dans l’image suivante, l’utilisateur Rick ( `rick@example.com` ) est connecté.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-322">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="1c7dc-323">Rick peut uniquement afficher les contacts approuvés et **modifier** / **supprimer** / **créer** des liens pour ses contacts.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-323">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="1c7dc-324">Seul le dernier enregistrement créé par Rick affiche des liens **modifier** et **supprimer** .</span><span class="sxs-lookup"><span data-stu-id="1c7dc-324">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="1c7dc-325">Les autres utilisateurs ne verront pas le dernier enregistrement jusqu’à ce qu’un responsable ou un administrateur change l’État en « approuvé ».</span><span class="sxs-lookup"><span data-stu-id="1c7dc-325">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Capture d’écran montrant que Rick est connecté](secure-data/_static/rick.png)

<span data-ttu-id="1c7dc-327">Dans l’image suivante, `manager@contoso.com` est connecté et dans le rôle du responsable :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-327">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Capture d’écran montrant la manager@contoso.com connexion](secure-data/_static/manager1.png)

<span data-ttu-id="1c7dc-329">L’illustration suivante montre la vue des détails du gestionnaire d’un contact :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-329">The following image shows the managers details view of a contact:</span></span>

![Vue du gestionnaire d’un contact](secure-data/_static/manager.png)

<span data-ttu-id="1c7dc-331">Les boutons **approuver** et **rejeter** s’affichent uniquement pour les responsables et les administrateurs.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-331">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="1c7dc-332">Dans l’image suivante, `admin@contoso.com` est connecté et dans le rôle de l’administrateur :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-332">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Capture d’écran montrant la admin@contoso.com connexion](secure-data/_static/admin.png)

<span data-ttu-id="1c7dc-334">L’administrateur dispose de tous les privilèges.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-334">The administrator has all privileges.</span></span> <span data-ttu-id="1c7dc-335">Elle peut lire/modifier/supprimer un contact et modifier l’état des contacts.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-335">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="1c7dc-336">L’application a été créée par la [génération](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) de modèles automatique du `Contact` modèle suivant :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-336">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="1c7dc-337">L’exemple contient les gestionnaires d’autorisations suivants :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-337">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="1c7dc-338">`ContactIsOwnerAuthorizationHandler`: Garantit qu’un utilisateur peut uniquement modifier ses données.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-338">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="1c7dc-339">`ContactManagerAuthorizationHandler`: Permet aux gestionnaires d’approuver ou de rejeter les contacts.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-339">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="1c7dc-340">`ContactAdministratorsAuthorizationHandler`: Permet aux administrateurs d’approuver ou de refuser des contacts, ainsi que de modifier/supprimer des contacts.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-340">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1c7dc-341">Prérequis</span><span class="sxs-lookup"><span data-stu-id="1c7dc-341">Prerequisites</span></span>

<span data-ttu-id="1c7dc-342">Ce didacticiel est avancé.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-342">This tutorial is advanced.</span></span> <span data-ttu-id="1c7dc-343">Vous devez connaître les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-343">You should be familiar with:</span></span>

* [<span data-ttu-id="1c7dc-344">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1c7dc-344">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="1c7dc-345">Authentification</span><span class="sxs-lookup"><span data-stu-id="1c7dc-345">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="1c7dc-346">Confirmation de compte et récupération de mot de passe</span><span class="sxs-lookup"><span data-stu-id="1c7dc-346">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="1c7dc-347">Autorisation</span><span class="sxs-lookup"><span data-stu-id="1c7dc-347">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="1c7dc-348">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="1c7dc-348">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="1c7dc-349">L’application de démarrage et la fin de l’application</span><span class="sxs-lookup"><span data-stu-id="1c7dc-349">The starter and completed app</span></span>

<span data-ttu-id="1c7dc-350">[Téléchargez](xref:index#how-to-download-a-sample) l’application [terminée](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) .</span><span class="sxs-lookup"><span data-stu-id="1c7dc-350">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="1c7dc-351">[Testez](#test-the-completed-app) l’application terminée afin de vous familiariser avec ses fonctionnalités de sécurité.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-351">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="1c7dc-352">L’application de démarrage</span><span class="sxs-lookup"><span data-stu-id="1c7dc-352">The starter app</span></span>

<span data-ttu-id="1c7dc-353">[Téléchargez](xref:index#how-to-download-a-sample) l’application de [démarrage](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .</span><span class="sxs-lookup"><span data-stu-id="1c7dc-353">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="1c7dc-354">Exécutez l’application, appuyez sur le lien **ContactManager** et vérifiez que vous pouvez créer, modifier et supprimer un contact.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-354">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="1c7dc-355">Sécuriser les données utilisateur</span><span class="sxs-lookup"><span data-stu-id="1c7dc-355">Secure user data</span></span>

<span data-ttu-id="1c7dc-356">Les sections suivantes présentent les principales étapes à suivre pour créer l’application de données utilisateur sécurisé.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-356">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="1c7dc-357">Il peut s’avérer utile de faire référence au projet terminé.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-357">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="1c7dc-358">Lier les données de contact à l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="1c7dc-358">Tie the contact data to the user</span></span>

<span data-ttu-id="1c7dc-359">Utilisez l' [Identity](xref:security/authentication/identity) ID d’utilisateur ASP.net pour vous assurer que les utilisateurs peuvent modifier leurs données, mais pas les données d’autres utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-359">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="1c7dc-360">Ajoutez `OwnerID` et `ContactStatus` au `Contact` modèle :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-360">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="1c7dc-361">`OwnerID` ID de l’utilisateur de la `AspNetUser` table dans la [Identity](xref:security/authentication/identity) base de données.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-361">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="1c7dc-362">Le `Status` champ détermine si un contact est visible par les utilisateurs généraux.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-362">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="1c7dc-363">Créez une nouvelle migration et mettez à jour la base de données :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-363">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="1c7dc-364">Ajouter des services de rôle à Identity</span><span class="sxs-lookup"><span data-stu-id="1c7dc-364">Add Role services to Identity</span></span>

<span data-ttu-id="1c7dc-365">Ajoutez [rôles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) pour ajouter des services de rôle :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-365">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=11)]

### <a name="require-authenticated-users"></a><span data-ttu-id="1c7dc-366">Exiger des utilisateurs authentifiés</span><span class="sxs-lookup"><span data-stu-id="1c7dc-366">Require authenticated users</span></span>

<span data-ttu-id="1c7dc-367">Définissez la stratégie d’authentification par défaut pour exiger que les utilisateurs soient authentifiés :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-367">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="1c7dc-368">Vous pouvez refuser l’authentification au niveau de la Razor page, du contrôleur ou de la méthode d’action avec l' `[AllowAnonymous]` attribut.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-368">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="1c7dc-369">La définition de la stratégie d’authentification par défaut pour exiger que les utilisateurs soient authentifiés protège les pages et les contrôleurs qui viennent d’être ajoutés Razor .</span><span class="sxs-lookup"><span data-stu-id="1c7dc-369">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="1c7dc-370">L’authentification requise par défaut est plus sécurisée que le fait de s’appuyer sur de nouveaux contrôleurs et Razor pages pour inclure l' `[Authorize]` attribut.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-370">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="1c7dc-371">Ajoutez [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) aux pages d’index, à propos de et contact afin que les utilisateurs anonymes puissent obtenir des informations sur le site avant de s’inscrire.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-371">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="1c7dc-372">Configurer le compte de test</span><span class="sxs-lookup"><span data-stu-id="1c7dc-372">Configure the test account</span></span>

<span data-ttu-id="1c7dc-373">La `SeedData` classe crée deux comptes : administrateur et gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-373">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="1c7dc-374">Utilisez l' [outil secret Manager](xref:security/app-secrets) pour définir un mot de passe pour ces comptes.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-374">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="1c7dc-375">Définissez le mot de passe à partir du répertoire du projet (répertoire contenant *Program.cs* ) :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-375">Set the password from the project directory (the directory containing *Program.cs* ):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="1c7dc-376">Si aucun mot de passe fort n’est spécifié, une exception est levée lorsque la `SeedData.Initialize` méthode est appelée.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-376">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="1c7dc-377">Mettre à jour `Main` pour utiliser le mot de passe de test :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-377">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="1c7dc-378">Créer les comptes de test et mettre à jour les contacts</span><span class="sxs-lookup"><span data-stu-id="1c7dc-378">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="1c7dc-379">Mettez à jour la `Initialize` méthode dans la `SeedData` classe pour créer les comptes de test :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-379">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="1c7dc-380">Ajoutez l’ID d’utilisateur administrateur et `ContactStatus` les contacts.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-380">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="1c7dc-381">Faites de l’un des contacts « soumis » et un « rejeté ».</span><span class="sxs-lookup"><span data-stu-id="1c7dc-381">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="1c7dc-382">Ajoutez l’ID d’utilisateur et l’État à tous les contacts.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-382">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="1c7dc-383">Un seul contact est affiché :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-383">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="1c7dc-384">Créer des gestionnaires d’autorisation propriétaire, responsable et administrateur</span><span class="sxs-lookup"><span data-stu-id="1c7dc-384">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="1c7dc-385">Créez un dossier *authorization* et créez une `ContactIsOwnerAuthorizationHandler` classe dans celui-ci.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-385">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="1c7dc-386">Le `ContactIsOwnerAuthorizationHandler` vérifie que l’utilisateur agissant sur une ressource possède la ressource.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-386">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="1c7dc-387">`ContactIsOwnerAuthorizationHandler`Contexte des appels [. Réussie](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) si l’utilisateur authentifié actuel est le propriétaire du contact.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-387">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="1c7dc-388">Les gestionnaires d’autorisations sont généralement :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-388">Authorization handlers generally:</span></span>

* <span data-ttu-id="1c7dc-389">Retourne `context.Succeed` lorsque les spécifications sont satisfaites.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-389">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="1c7dc-390">Retourne `Task.CompletedTask` lorsque les spécifications ne sont pas remplies.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-390">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="1c7dc-391">`Task.CompletedTask` n’est pas un succès ou un échec &mdash; . il permet l’exécution d’autres gestionnaires d’autorisations.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-391">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="1c7dc-392">Si vous devez faire échouer explicitement, retournez le [contexte. Échoue](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="1c7dc-392">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="1c7dc-393">L’application permet aux propriétaires de contacts de modifier/supprimer/créer leurs propres données.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-393">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="1c7dc-394">`ContactIsOwnerAuthorizationHandler` n’a pas besoin de vérifier l’opération passée dans le paramètre d’exigence.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-394">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="1c7dc-395">Créer un gestionnaire d’autorisations de gestionnaire</span><span class="sxs-lookup"><span data-stu-id="1c7dc-395">Create a manager authorization handler</span></span>

<span data-ttu-id="1c7dc-396">Créez une `ContactManagerAuthorizationHandler` classe dans le dossier *authorization* .</span><span class="sxs-lookup"><span data-stu-id="1c7dc-396">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="1c7dc-397">Le `ContactManagerAuthorizationHandler` vérifie que l’utilisateur agissant sur la ressource est un responsable.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-397">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="1c7dc-398">Seuls les responsables peuvent approuver ou rejeter les modifications de contenu (nouveaux ou modifiés).</span><span class="sxs-lookup"><span data-stu-id="1c7dc-398">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="1c7dc-399">Créer un gestionnaire d’autorisations d’administrateur</span><span class="sxs-lookup"><span data-stu-id="1c7dc-399">Create an administrator authorization handler</span></span>

<span data-ttu-id="1c7dc-400">Créez une `ContactAdministratorsAuthorizationHandler` classe dans le dossier *authorization* .</span><span class="sxs-lookup"><span data-stu-id="1c7dc-400">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="1c7dc-401">Le `ContactAdministratorsAuthorizationHandler` vérifie que l’utilisateur agissant sur la ressource est un administrateur.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-401">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="1c7dc-402">L’administrateur peut effectuer toutes les opérations.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-402">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="1c7dc-403">Inscrire les gestionnaires d’autorisations</span><span class="sxs-lookup"><span data-stu-id="1c7dc-403">Register the authorization handlers</span></span>

<span data-ttu-id="1c7dc-404">Les services qui utilisent Entity Framework Core doivent être inscrits pour l' [injection de dépendances](xref:fundamentals/dependency-injection) à l’aide de [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="1c7dc-404">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="1c7dc-405">`ContactIsOwnerAuthorizationHandler`Utilise ASP.net Core [Identity](xref:security/authentication/identity) , qui repose sur Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-405">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="1c7dc-406">Inscrire les gestionnaires auprès de la collection de services afin qu’ils soient disponibles pour le `ContactsController` via l' [injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="1c7dc-406">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="1c7dc-407">Ajoutez le code suivant à la fin de `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-407">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="1c7dc-408">`ContactAdministratorsAuthorizationHandler` et `ContactManagerAuthorizationHandler` sont ajoutés en tant que singletons.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-408">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="1c7dc-409">Il s’agit de singletons, car ils n’utilisent pas EF et toutes les informations nécessaires sont dans le `Context` paramètre de la `HandleRequirementAsync` méthode.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-409">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="1c7dc-410">Autorisation du support</span><span class="sxs-lookup"><span data-stu-id="1c7dc-410">Support authorization</span></span>

<span data-ttu-id="1c7dc-411">Dans cette section, vous allez mettre à jour les Razor pages et ajouter une classe d’exigences d’opérations.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-411">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="1c7dc-412">Examiner la classe des exigences relatives aux opérations de contact</span><span class="sxs-lookup"><span data-stu-id="1c7dc-412">Review the contact operations requirements class</span></span>

<span data-ttu-id="1c7dc-413">Passez en revue la `ContactOperations` classe.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-413">Review the `ContactOperations` class.</span></span> <span data-ttu-id="1c7dc-414">Cette classe contient les spécifications prises en charge par l’application :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-414">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="1c7dc-415">Créer une classe de base pour les Razor pages contacts</span><span class="sxs-lookup"><span data-stu-id="1c7dc-415">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="1c7dc-416">Créez une classe de base qui contient les services utilisés dans les Razor pages de contacts.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-416">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="1c7dc-417">La classe de base place le code d’initialisation à un emplacement :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-417">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="1c7dc-418">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-418">The preceding code:</span></span>

* <span data-ttu-id="1c7dc-419">Ajoute le `IAuthorizationService` service pour accéder aux gestionnaires d’autorisations.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-419">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="1c7dc-420">Ajoute le Identity `UserManager` service.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-420">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="1c7dc-421">Ajoutez la `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-421">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="1c7dc-422">Mettre à jour CreateModel</span><span class="sxs-lookup"><span data-stu-id="1c7dc-422">Update the CreateModel</span></span>

<span data-ttu-id="1c7dc-423">Mettez à jour le constructeur de modèle de page de création pour utiliser la `DI_BasePageModel` classe de base :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-423">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="1c7dc-424">Mettez à jour la `CreateModel.OnPostAsync` méthode pour :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-424">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="1c7dc-425">Ajoutez l’ID utilisateur au `Contact` modèle.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-425">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="1c7dc-426">Appelez le gestionnaire d’autorisations pour vérifier que l’utilisateur a l’autorisation de créer des contacts.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-426">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="1c7dc-427">Mettre à jour IndexModel</span><span class="sxs-lookup"><span data-stu-id="1c7dc-427">Update the IndexModel</span></span>

<span data-ttu-id="1c7dc-428">Mettez à jour la `OnGetAsync` méthode afin que seuls les contacts approuvés soient affichés pour les utilisateurs généraux :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-428">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="1c7dc-429">Mettre à jour EditModel</span><span class="sxs-lookup"><span data-stu-id="1c7dc-429">Update the EditModel</span></span>

<span data-ttu-id="1c7dc-430">Ajoutez un gestionnaire d’autorisations pour vérifier que l’utilisateur possède le contact.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-430">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="1c7dc-431">Étant donné que l’autorisation des ressources est en cours de validation, l' `[Authorize]` attribut n’est pas suffisant.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-431">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="1c7dc-432">L’application n’a pas accès à la ressource lors de l’évaluation des attributs.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-432">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="1c7dc-433">L’autorisation basée sur les ressources doit être impérative.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-433">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="1c7dc-434">Les vérifications doivent être effectuées une fois que l’application a accès à la ressource, soit en la chargeant dans le modèle de page, soit en la chargeant dans le gestionnaire lui-même.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-434">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="1c7dc-435">Vous accédez fréquemment à la ressource en passant la clé de ressource.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-435">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="1c7dc-436">Mettre à jour DeleteModel</span><span class="sxs-lookup"><span data-stu-id="1c7dc-436">Update the DeleteModel</span></span>

<span data-ttu-id="1c7dc-437">Mettez à jour le modèle de page de suppression pour utiliser le gestionnaire d’autorisations afin de vérifier que l’utilisateur dispose de l’autorisation de suppression sur le contact.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-437">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="1c7dc-438">Injecter le service d’autorisation dans les vues</span><span class="sxs-lookup"><span data-stu-id="1c7dc-438">Inject the authorization service into the views</span></span>

<span data-ttu-id="1c7dc-439">Actuellement, l’interface utilisateur affiche les liens modifier et supprimer pour les contacts que l’utilisateur ne peut pas modifier.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-439">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="1c7dc-440">Injecter le service d’autorisation dans le fichier *views/_ViewImports. cshtml* afin qu’il soit disponible pour tous les affichages :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-440">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="1c7dc-441">Le balisage précédent ajoute plusieurs `using` instructions.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-441">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="1c7dc-442">Mettez à jour les liens **modifier** et **supprimer** dans *pages/contacts/index. cshtml* afin qu’ils soient affichés uniquement pour les utilisateurs disposant des autorisations appropriées :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-442">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="1c7dc-443">Le masquage des liens des utilisateurs qui n’ont pas l’autorisation de modifier des données ne sécurise pas l’application.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-443">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="1c7dc-444">Le masquage des liens rend l’application plus conviviale en affichant uniquement des liens valides.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-444">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="1c7dc-445">Les utilisateurs peuvent pirater les URL générées pour appeler des opérations de modification et de suppression sur les données qu’ils ne possèdent pas.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-445">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="1c7dc-446">La Razor page ou le contrôleur doit appliquer les vérifications d’accès pour sécuriser les données.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-446">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="1c7dc-447">Mettre à jour les détails</span><span class="sxs-lookup"><span data-stu-id="1c7dc-447">Update Details</span></span>

<span data-ttu-id="1c7dc-448">Mettez à jour la vue Détails pour permettre aux responsables d’approuver ou de rejeter les contacts :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-448">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="1c7dc-449">Mettez à jour le modèle de page de détails :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-449">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="1c7dc-450">Ajouter ou supprimer un utilisateur dans un rôle</span><span class="sxs-lookup"><span data-stu-id="1c7dc-450">Add or remove a user to a role</span></span>

<span data-ttu-id="1c7dc-451">Consultez [ce numéro](https://github.com/dotnet/AspNetCore.Docs/issues/8502) pour plus d’informations sur :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-451">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="1c7dc-452">Suppression des privilèges d’un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-452">Removing privileges from a user.</span></span> <span data-ttu-id="1c7dc-453">Par exemple, la désactivation d’un utilisateur dans une application de conversation.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-453">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="1c7dc-454">Ajout de privilèges à un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-454">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="1c7dc-455">Tester l’application terminée</span><span class="sxs-lookup"><span data-stu-id="1c7dc-455">Test the completed app</span></span>

<span data-ttu-id="1c7dc-456">Si vous n’avez pas encore défini de mot de passe pour les comptes d’utilisateurs amorcés, utilisez l' [outil secret Manager](xref:security/app-secrets#secret-manager) pour définir un mot de passe :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-456">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="1c7dc-457">Choisissez un mot de passe fort : utilisez au moins huit caractères et au moins un caractère majuscule, un chiffre et un symbole.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-457">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="1c7dc-458">Par exemple, `Passw0rd!` répond aux exigences de mot de passe fort.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-458">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="1c7dc-459">Exécutez la commande suivante à partir du dossier du projet, où `<PW>` est le mot de passe :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-459">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="1c7dc-460">Supprimer et mettre à jour la base de données</span><span class="sxs-lookup"><span data-stu-id="1c7dc-460">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="1c7dc-461">Redémarrez l’application pour amorcer la base de données.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-461">Restart the app to seed the database.</span></span>

<span data-ttu-id="1c7dc-462">Un moyen simple de tester l’application terminée consiste à lancer trois navigateurs différents (ou des sessions incognito/InPrivate).</span><span class="sxs-lookup"><span data-stu-id="1c7dc-462">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="1c7dc-463">Dans un navigateur, inscrivez un nouvel utilisateur (par exemple, `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="1c7dc-463">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="1c7dc-464">Connectez-vous à chaque navigateur avec un autre utilisateur.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-464">Sign in to each browser with a different user.</span></span> <span data-ttu-id="1c7dc-465">Vérifiez les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-465">Verify the following operations:</span></span>

* <span data-ttu-id="1c7dc-466">Les utilisateurs inscrits peuvent afficher toutes les données de contact approuvées.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-466">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="1c7dc-467">Les utilisateurs inscrits peuvent modifier/supprimer leurs propres données.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-467">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="1c7dc-468">Les responsables peuvent approuver/rejeter les données de contact.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-468">Managers can approve/reject contact data.</span></span> <span data-ttu-id="1c7dc-469">La `Details` vue affiche les boutons **approuver** et **rejeter** .</span><span class="sxs-lookup"><span data-stu-id="1c7dc-469">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="1c7dc-470">Les administrateurs peuvent approuver/refuser et modifier/supprimer toutes les données.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-470">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="1c7dc-471">Utilisateur</span><span class="sxs-lookup"><span data-stu-id="1c7dc-471">User</span></span>                | <span data-ttu-id="1c7dc-472">Amorcé par l’application</span><span class="sxs-lookup"><span data-stu-id="1c7dc-472">Seeded by the app</span></span> | <span data-ttu-id="1c7dc-473">Options</span><span class="sxs-lookup"><span data-stu-id="1c7dc-473">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="1c7dc-474">No</span><span class="sxs-lookup"><span data-stu-id="1c7dc-474">No</span></span>                | <span data-ttu-id="1c7dc-475">Modifiez/supprimez les données.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-475">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="1c7dc-476">Yes</span><span class="sxs-lookup"><span data-stu-id="1c7dc-476">Yes</span></span>               | <span data-ttu-id="1c7dc-477">Approuver/refuser et modifier/supprimer des données.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-477">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="1c7dc-478">Yes</span><span class="sxs-lookup"><span data-stu-id="1c7dc-478">Yes</span></span>               | <span data-ttu-id="1c7dc-479">Approuver/refuser et modifier/supprimer toutes les données.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-479">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="1c7dc-480">Créez un contact dans le navigateur de l’administrateur.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-480">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="1c7dc-481">Copiez l’URL de la suppression et de la modification à partir du contact de l’administrateur.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-481">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="1c7dc-482">Collez ces liens dans le navigateur de l’utilisateur de test pour vérifier que l’utilisateur de test ne peut pas effectuer ces opérations.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-482">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="1c7dc-483">Créer l’application de démarrage</span><span class="sxs-lookup"><span data-stu-id="1c7dc-483">Create the starter app</span></span>

* <span data-ttu-id="1c7dc-484">Créer une Razor application pages nommée « ContactManager »</span><span class="sxs-lookup"><span data-stu-id="1c7dc-484">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="1c7dc-485">Créez l’application avec des **comptes d’utilisateur individuels** .</span><span class="sxs-lookup"><span data-stu-id="1c7dc-485">Create the app with **Individual User Accounts** .</span></span>
  * <span data-ttu-id="1c7dc-486">Nommez-la « ContactManager » pour que l’espace de noms corresponde à l’espace de noms utilisé dans l’exemple.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-486">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="1c7dc-487">`-uld` spécifie la base de données locale au lieu de SQLite</span><span class="sxs-lookup"><span data-stu-id="1c7dc-487">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="1c7dc-488">Ajouter des *modèles/contact. cs* :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-488">Add *Models/Contact.cs* :</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="1c7dc-489">Structurez le `Contact` modèle.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-489">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="1c7dc-490">Créez la migration initiale et mettez à jour la base de données :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-490">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="1c7dc-491">Mettez à jour l’ancre **ContactManager** dans le fichier *pages/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-491">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="1c7dc-492">Tester l’application en créant, en modifiant et en supprimant un contact</span><span class="sxs-lookup"><span data-stu-id="1c7dc-492">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="1c7dc-493">Amorcer la base de données</span><span class="sxs-lookup"><span data-stu-id="1c7dc-493">Seed the database</span></span>

<span data-ttu-id="1c7dc-494">Ajoutez la classe [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) au dossier de *données* .</span><span class="sxs-lookup"><span data-stu-id="1c7dc-494">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="1c7dc-495">Appeler `SeedData.Initialize` à partir de `Main` :</span><span class="sxs-lookup"><span data-stu-id="1c7dc-495">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="1c7dc-496">Vérifiez que l’application a amorcé la base de données.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-496">Test that the app seeded the database.</span></span> <span data-ttu-id="1c7dc-497">Si la base de coordonnées contient des lignes, la méthode Seed ne s’exécute pas.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-497">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="1c7dc-498">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="1c7dc-498">Additional resources</span></span>

* [<span data-ttu-id="1c7dc-499">Créer une application Web .NET Core et SQL Database dans Azure App Service</span><span class="sxs-lookup"><span data-stu-id="1c7dc-499">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="1c7dc-500">[ASP.net Core laboratoire d’autorisation](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="1c7dc-500">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="1c7dc-501">Ce laboratoire aborde plus en détail les fonctionnalités de sécurité présentées dans ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="1c7dc-501">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="1c7dc-502">Autorisation basée sur une stratégie personnalisée</span><span class="sxs-lookup"><span data-stu-id="1c7dc-502">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
