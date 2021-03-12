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
ms.openlocfilehash: 662456af59c453df66ca48139a6de40d0e2cbf0d
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589190"
---
# <a name="create-an-aspnet-core-web-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="eb529-104">Créer une application Web ASP.NET Core avec les données utilisateur protégées par l’autorisation</span><span class="sxs-lookup"><span data-stu-id="eb529-104">Create an ASP.NET Core web app with user data protected by authorization</span></span>

<span data-ttu-id="eb529-105">Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="eb529-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="eb529-106">Voir [ce PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span><span class="sxs-lookup"><span data-stu-id="eb529-106">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="eb529-107">Ce didacticiel montre comment créer une application Web ASP.NET Core avec les données utilisateur protégées par l’autorisation.</span><span class="sxs-lookup"><span data-stu-id="eb529-107">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="eb529-108">Il affiche la liste des contacts que les utilisateurs authentifiés (inscrits) ont créés.</span><span class="sxs-lookup"><span data-stu-id="eb529-108">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="eb529-109">Il existe trois groupes de sécurité :</span><span class="sxs-lookup"><span data-stu-id="eb529-109">There are three security groups:</span></span>

* <span data-ttu-id="eb529-110">Les **utilisateurs inscrits** peuvent afficher toutes les données approuvées et peuvent modifier/supprimer leurs propres données.</span><span class="sxs-lookup"><span data-stu-id="eb529-110">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="eb529-111">Les **responsables** peuvent approuver ou rejeter les données de contact.</span><span class="sxs-lookup"><span data-stu-id="eb529-111">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="eb529-112">Seuls les contacts approuvés sont visibles pour les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="eb529-112">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="eb529-113">**Les administrateurs** peuvent approuver/refuser et modifier/supprimer des données.</span><span class="sxs-lookup"><span data-stu-id="eb529-113">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="eb529-114">Les images de ce document ne correspondent pas exactement aux modèles les plus récents.</span><span class="sxs-lookup"><span data-stu-id="eb529-114">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="eb529-115">Dans l’image suivante, l’utilisateur Rick ( `rick@example.com` ) est connecté.</span><span class="sxs-lookup"><span data-stu-id="eb529-115">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="eb529-116">Rick peut uniquement afficher les contacts approuvés et **modifier** / **supprimer** / **créer** des liens pour ses contacts.</span><span class="sxs-lookup"><span data-stu-id="eb529-116">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="eb529-117">Seul le dernier enregistrement créé par Rick affiche des liens **modifier** et **supprimer** .</span><span class="sxs-lookup"><span data-stu-id="eb529-117">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="eb529-118">Les autres utilisateurs ne verront pas le dernier enregistrement jusqu’à ce qu’un responsable ou un administrateur change l’État en « approuvé ».</span><span class="sxs-lookup"><span data-stu-id="eb529-118">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Capture d’écran montrant que Rick est connecté](secure-data/_static/rick.png)

<span data-ttu-id="eb529-120">Dans l’image suivante, `manager@contoso.com` est connecté et dans le rôle du responsable :</span><span class="sxs-lookup"><span data-stu-id="eb529-120">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Capture d’écran montrant la manager@contoso.com connexion](secure-data/_static/manager1.png)

<span data-ttu-id="eb529-122">L’illustration suivante montre la vue des détails du gestionnaire d’un contact :</span><span class="sxs-lookup"><span data-stu-id="eb529-122">The following image shows the managers details view of a contact:</span></span>

![Vue du gestionnaire d’un contact](secure-data/_static/manager.png)

<span data-ttu-id="eb529-124">Les boutons **approuver** et **rejeter** s’affichent uniquement pour les responsables et les administrateurs.</span><span class="sxs-lookup"><span data-stu-id="eb529-124">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="eb529-125">Dans l’image suivante, `admin@contoso.com` est connecté et dans le rôle de l’administrateur :</span><span class="sxs-lookup"><span data-stu-id="eb529-125">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Capture d’écran montrant la admin@contoso.com connexion](secure-data/_static/admin.png)

<span data-ttu-id="eb529-127">L’administrateur dispose de tous les privilèges.</span><span class="sxs-lookup"><span data-stu-id="eb529-127">The administrator has all privileges.</span></span> <span data-ttu-id="eb529-128">Elle peut lire/modifier/supprimer un contact et modifier l’état des contacts.</span><span class="sxs-lookup"><span data-stu-id="eb529-128">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="eb529-129">L’application a été créée par la [génération](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) de modèles automatique du `Contact` modèle suivant :</span><span class="sxs-lookup"><span data-stu-id="eb529-129">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="eb529-130">L’exemple contient les gestionnaires d’autorisations suivants :</span><span class="sxs-lookup"><span data-stu-id="eb529-130">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="eb529-131">`ContactIsOwnerAuthorizationHandler`: Garantit qu’un utilisateur peut uniquement modifier ses données.</span><span class="sxs-lookup"><span data-stu-id="eb529-131">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="eb529-132">`ContactManagerAuthorizationHandler`: Permet aux gestionnaires d’approuver ou de rejeter les contacts.</span><span class="sxs-lookup"><span data-stu-id="eb529-132">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="eb529-133">`ContactAdministratorsAuthorizationHandler`: Permet aux administrateurs d’approuver ou de refuser des contacts, ainsi que de modifier/supprimer des contacts.</span><span class="sxs-lookup"><span data-stu-id="eb529-133">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="eb529-134">Prérequis</span><span class="sxs-lookup"><span data-stu-id="eb529-134">Prerequisites</span></span>

<span data-ttu-id="eb529-135">Ce didacticiel est avancé.</span><span class="sxs-lookup"><span data-stu-id="eb529-135">This tutorial is advanced.</span></span> <span data-ttu-id="eb529-136">Vous devez connaître les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="eb529-136">You should be familiar with:</span></span>

* [<span data-ttu-id="eb529-137">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="eb529-137">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="eb529-138">Authentification</span><span class="sxs-lookup"><span data-stu-id="eb529-138">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="eb529-139">Confirmation de compte et récupération de mot de passe</span><span class="sxs-lookup"><span data-stu-id="eb529-139">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="eb529-140">Autorisation</span><span class="sxs-lookup"><span data-stu-id="eb529-140">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="eb529-141">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="eb529-141">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="eb529-142">L’application de démarrage et la fin de l’application</span><span class="sxs-lookup"><span data-stu-id="eb529-142">The starter and completed app</span></span>

<span data-ttu-id="eb529-143">[Téléchargez](xref:index#how-to-download-a-sample) l’application [terminée](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/secure-data/samples) .</span><span class="sxs-lookup"><span data-stu-id="eb529-143">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="eb529-144">[Testez](#test-the-completed-app) l’application terminée afin de vous familiariser avec ses fonctionnalités de sécurité.</span><span class="sxs-lookup"><span data-stu-id="eb529-144">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="eb529-145">L’application de démarrage</span><span class="sxs-lookup"><span data-stu-id="eb529-145">The starter app</span></span>

<span data-ttu-id="eb529-146">[Téléchargez](xref:index#how-to-download-a-sample) l’application de [démarrage](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/secure-data/samples/) .</span><span class="sxs-lookup"><span data-stu-id="eb529-146">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="eb529-147">Exécutez l’application, appuyez sur le lien **ContactManager** et vérifiez que vous pouvez créer, modifier et supprimer un contact.</span><span class="sxs-lookup"><span data-stu-id="eb529-147">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span> <span data-ttu-id="eb529-148">Pour créer l’application de démarrage, consultez [créer l’application de démarrage](#create-the-starter-app).</span><span class="sxs-lookup"><span data-stu-id="eb529-148">To create the starter app, see [Create the starter app](#create-the-starter-app).</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="eb529-149">Sécuriser les données utilisateur</span><span class="sxs-lookup"><span data-stu-id="eb529-149">Secure user data</span></span>

<span data-ttu-id="eb529-150">Les sections suivantes présentent les principales étapes à suivre pour créer l’application de données utilisateur sécurisé.</span><span class="sxs-lookup"><span data-stu-id="eb529-150">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="eb529-151">Il peut s’avérer utile de faire référence au projet terminé.</span><span class="sxs-lookup"><span data-stu-id="eb529-151">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="eb529-152">Lier les données de contact à l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="eb529-152">Tie the contact data to the user</span></span>

<span data-ttu-id="eb529-153">Utilisez l' [Identity](xref:security/authentication/identity) ID d’utilisateur ASP.net pour vous assurer que les utilisateurs peuvent modifier leurs données, mais pas les données d’autres utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="eb529-153">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="eb529-154">Ajoutez `OwnerID` et `ContactStatus` au `Contact` modèle :</span><span class="sxs-lookup"><span data-stu-id="eb529-154">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="eb529-155">`OwnerID` ID de l’utilisateur de la `AspNetUser` table dans la [Identity](xref:security/authentication/identity) base de données.</span><span class="sxs-lookup"><span data-stu-id="eb529-155">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="eb529-156">Le `Status` champ détermine si un contact est visible par les utilisateurs généraux.</span><span class="sxs-lookup"><span data-stu-id="eb529-156">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="eb529-157">Créez une nouvelle migration et mettez à jour la base de données :</span><span class="sxs-lookup"><span data-stu-id="eb529-157">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="eb529-158">Ajouter des services de rôle à Identity</span><span class="sxs-lookup"><span data-stu-id="eb529-158">Add Role services to Identity</span></span>

<span data-ttu-id="eb529-159">Ajoutez [rôles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) pour ajouter des services de rôle :</span><span class="sxs-lookup"><span data-stu-id="eb529-159">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

<a name="rau"></a>

### <a name="require-authenticated-users"></a><span data-ttu-id="eb529-160">Exiger des utilisateurs authentifiés</span><span class="sxs-lookup"><span data-stu-id="eb529-160">Require authenticated users</span></span>

<span data-ttu-id="eb529-161">Définissez la stratégie d’authentification de secours pour exiger que les utilisateurs soient authentifiés :</span><span class="sxs-lookup"><span data-stu-id="eb529-161">Set the fallback authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=13-99)]

<span data-ttu-id="eb529-162">Le code en surbrillance précédent définit la [stratégie d’authentification de secours](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span><span class="sxs-lookup"><span data-stu-id="eb529-162">The preceding highlighted code sets the [fallback authentication policy](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span></span> <span data-ttu-id="eb529-163">La stratégie d’authentification de secours exige que ***tous*** les utilisateurs soient authentifiés, à l’exception des Razor pages, des contrôleurs ou des méthodes d’action avec un attribut d’authentification.</span><span class="sxs-lookup"><span data-stu-id="eb529-163">The fallback authentication policy requires ***all*** users to be authenticated, except for Razor Pages, controllers, or action methods with an authentication attribute.</span></span> <span data-ttu-id="eb529-164">Par exemple, Razor les pages, les contrôleurs ou les méthodes d’action avec `[AllowAnonymous]` ou `[Authorize(PolicyName="MyPolicy")]` utilisent l’attribut d’authentification appliqué plutôt que la stratégie d’authentification de secours.</span><span class="sxs-lookup"><span data-stu-id="eb529-164">For example, Razor Pages, controllers, or action methods with `[AllowAnonymous]` or `[Authorize(PolicyName="MyPolicy")]` use the applied authentication attribute rather than the fallback authentication policy.</span></span>

<span data-ttu-id="eb529-165"><xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> Ajoute <xref:Microsoft.AspNetCore.Authorization.Infrastructure.DenyAnonymousAuthorizationRequirement> à l’instance actuelle, ce qui garantit que l’utilisateur actuel est authentifié.</span><span class="sxs-lookup"><span data-stu-id="eb529-165"><xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> adds <xref:Microsoft.AspNetCore.Authorization.Infrastructure.DenyAnonymousAuthorizationRequirement> to the current instance, which enforces that the current user is authenticated.</span></span>

<span data-ttu-id="eb529-166">Stratégie d’authentification de secours :</span><span class="sxs-lookup"><span data-stu-id="eb529-166">The fallback authentication policy:</span></span>

* <span data-ttu-id="eb529-167">Est appliqué à toutes les demandes qui ne spécifient pas explicitement une stratégie d’authentification.</span><span class="sxs-lookup"><span data-stu-id="eb529-167">Is applied to all requests that do not explicitly specify an authentication policy.</span></span> <span data-ttu-id="eb529-168">Pour les demandes traitées par le routage de point de terminaison, cela inclut tout point de terminaison qui ne spécifie pas d’attribut d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="eb529-168">For requests served by endpoint routing, this would include any endpoint that does not specify an authorization attribute.</span></span> <span data-ttu-id="eb529-169">Pour les demandes servies par un autre intergiciel après l’intergiciel (middleware) d’autorisation, par exemple des [fichiers statiques](xref:fundamentals/static-files), cela applique la stratégie à toutes les demandes.</span><span class="sxs-lookup"><span data-stu-id="eb529-169">For requests served by other middleware after the authorization middleware, such as [static files](xref:fundamentals/static-files), this would apply the policy to all requests.</span></span>

<span data-ttu-id="eb529-170">La définition de la stratégie d’authentification de secours pour exiger que les utilisateurs soient authentifiés protège les pages et les contrôleurs qui viennent d’être ajoutés Razor .</span><span class="sxs-lookup"><span data-stu-id="eb529-170">Setting the fallback authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="eb529-171">L’authentification requise par défaut est plus sécurisée que le fait de s’appuyer sur de nouveaux contrôleurs et Razor pages pour inclure l' `[Authorize]` attribut.</span><span class="sxs-lookup"><span data-stu-id="eb529-171">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="eb529-172">La <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> classe contient également <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="eb529-172">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> class also contains <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType>.</span></span> <span data-ttu-id="eb529-173">`DefaultPolicy`Est la stratégie utilisée avec l' `[Authorize]` attribut quand aucune stratégie n’est spécifiée.</span><span class="sxs-lookup"><span data-stu-id="eb529-173">The `DefaultPolicy` is the policy used with the `[Authorize]` attribute when no policy is specified.</span></span> <span data-ttu-id="eb529-174">`[Authorize]` ne contient pas de stratégie nommée, contrairement à `[Authorize(PolicyName="MyPolicy")]` .</span><span class="sxs-lookup"><span data-stu-id="eb529-174">`[Authorize]` doesn't contain a named policy, unlike `[Authorize(PolicyName="MyPolicy")]`.</span></span>

<span data-ttu-id="eb529-175">Pour plus d’informations sur les stratégies, consultez <xref:security/authorization/policies> .</span><span class="sxs-lookup"><span data-stu-id="eb529-175">For more information on policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="eb529-176">Pour que les contrôleurs et Razor les pages MVC requièrent que tous les utilisateurs soient authentifiés, vous pouvez également ajouter un filtre d’autorisation :</span><span class="sxs-lookup"><span data-stu-id="eb529-176">An alternative way for MVC controllers and Razor Pages to require all users be authenticated is adding an authorization filter:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup2.cs?name=snippet&highlight=14-99)]

<span data-ttu-id="eb529-177">Le code précédent utilise un filtre d’autorisation, la définition de la stratégie de secours utilise le routage du point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="eb529-177">The preceding code uses an authorization filter, setting the fallback policy uses endpoint routing.</span></span> <span data-ttu-id="eb529-178">La définition de la stratégie de secours est la méthode recommandée pour exiger que tous les utilisateurs soient authentifiés.</span><span class="sxs-lookup"><span data-stu-id="eb529-178">Setting the fallback policy is the preferred way to require all users be authenticated.</span></span>

<span data-ttu-id="eb529-179">Ajoutez [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) aux `Index` pages et `Privacy` afin que les utilisateurs anonymes puissent obtenir des informations sur le site avant de s’inscrire :</span><span class="sxs-lookup"><span data-stu-id="eb529-179">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the `Index` and `Privacy` pages so anonymous users can get information about the site before they register:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="eb529-180">Configurer le compte de test</span><span class="sxs-lookup"><span data-stu-id="eb529-180">Configure the test account</span></span>

<span data-ttu-id="eb529-181">La `SeedData` classe crée deux comptes : administrateur et gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="eb529-181">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="eb529-182">Utilisez l' [outil secret Manager](xref:security/app-secrets) pour définir un mot de passe pour ces comptes.</span><span class="sxs-lookup"><span data-stu-id="eb529-182">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="eb529-183">Définissez le mot de passe à partir du répertoire du projet (répertoire contenant *Program.cs*) :</span><span class="sxs-lookup"><span data-stu-id="eb529-183">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="eb529-184">Si aucun mot de passe fort n’est spécifié, une exception est levée lorsque la `SeedData.Initialize` méthode est appelée.</span><span class="sxs-lookup"><span data-stu-id="eb529-184">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="eb529-185">Mettre à jour `Main` pour utiliser le mot de passe de test :</span><span class="sxs-lookup"><span data-stu-id="eb529-185">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="eb529-186">Créer les comptes de test et mettre à jour les contacts</span><span class="sxs-lookup"><span data-stu-id="eb529-186">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="eb529-187">Mettez à jour la `Initialize` méthode dans la `SeedData` classe pour créer les comptes de test :</span><span class="sxs-lookup"><span data-stu-id="eb529-187">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="eb529-188">Ajoutez l’ID d’utilisateur administrateur et `ContactStatus` les contacts.</span><span class="sxs-lookup"><span data-stu-id="eb529-188">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="eb529-189">Faites de l’un des contacts « soumis » et un « rejeté ».</span><span class="sxs-lookup"><span data-stu-id="eb529-189">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="eb529-190">Ajoutez l’ID d’utilisateur et l’État à tous les contacts.</span><span class="sxs-lookup"><span data-stu-id="eb529-190">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="eb529-191">Un seul contact est affiché :</span><span class="sxs-lookup"><span data-stu-id="eb529-191">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="eb529-192">Créer des gestionnaires d’autorisation propriétaire, responsable et administrateur</span><span class="sxs-lookup"><span data-stu-id="eb529-192">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="eb529-193">Créez une `ContactIsOwnerAuthorizationHandler` classe dans le dossier *authorization* .</span><span class="sxs-lookup"><span data-stu-id="eb529-193">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="eb529-194">Le `ContactIsOwnerAuthorizationHandler` vérifie que l’utilisateur agissant sur une ressource possède la ressource.</span><span class="sxs-lookup"><span data-stu-id="eb529-194">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="eb529-195">`ContactIsOwnerAuthorizationHandler`Contexte des appels [. Réussie](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) si l’utilisateur authentifié actuel est le propriétaire du contact.</span><span class="sxs-lookup"><span data-stu-id="eb529-195">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="eb529-196">Les gestionnaires d’autorisations sont généralement :</span><span class="sxs-lookup"><span data-stu-id="eb529-196">Authorization handlers generally:</span></span>

* <span data-ttu-id="eb529-197">Retourne `context.Succeed` lorsque les spécifications sont satisfaites.</span><span class="sxs-lookup"><span data-stu-id="eb529-197">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="eb529-198">Retourne `Task.CompletedTask` lorsque les spécifications ne sont pas remplies.</span><span class="sxs-lookup"><span data-stu-id="eb529-198">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="eb529-199">`Task.CompletedTask` n’est pas un succès ou un échec &mdash; . il permet l’exécution d’autres gestionnaires d’autorisations.</span><span class="sxs-lookup"><span data-stu-id="eb529-199">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="eb529-200">Si vous devez faire échouer explicitement, retournez le [contexte. Échoue](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="eb529-200">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="eb529-201">L’application permet aux propriétaires de contacts de modifier/supprimer/créer leurs propres données.</span><span class="sxs-lookup"><span data-stu-id="eb529-201">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="eb529-202">`ContactIsOwnerAuthorizationHandler` n’a pas besoin de vérifier l’opération passée dans le paramètre d’exigence.</span><span class="sxs-lookup"><span data-stu-id="eb529-202">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="eb529-203">Créer un gestionnaire d’autorisations de gestionnaire</span><span class="sxs-lookup"><span data-stu-id="eb529-203">Create a manager authorization handler</span></span>

<span data-ttu-id="eb529-204">Créez une `ContactManagerAuthorizationHandler` classe dans le dossier *authorization* .</span><span class="sxs-lookup"><span data-stu-id="eb529-204">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="eb529-205">Le `ContactManagerAuthorizationHandler` vérifie que l’utilisateur agissant sur la ressource est un responsable.</span><span class="sxs-lookup"><span data-stu-id="eb529-205">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="eb529-206">Seuls les responsables peuvent approuver ou rejeter les modifications de contenu (nouveaux ou modifiés).</span><span class="sxs-lookup"><span data-stu-id="eb529-206">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="eb529-207">Créer un gestionnaire d’autorisations d’administrateur</span><span class="sxs-lookup"><span data-stu-id="eb529-207">Create an administrator authorization handler</span></span>

<span data-ttu-id="eb529-208">Créez une `ContactAdministratorsAuthorizationHandler` classe dans le dossier *authorization* .</span><span class="sxs-lookup"><span data-stu-id="eb529-208">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="eb529-209">Le `ContactAdministratorsAuthorizationHandler` vérifie que l’utilisateur agissant sur la ressource est un administrateur.</span><span class="sxs-lookup"><span data-stu-id="eb529-209">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="eb529-210">L’administrateur peut effectuer toutes les opérations.</span><span class="sxs-lookup"><span data-stu-id="eb529-210">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="eb529-211">Inscrire les gestionnaires d’autorisations</span><span class="sxs-lookup"><span data-stu-id="eb529-211">Register the authorization handlers</span></span>

<span data-ttu-id="eb529-212">Les services qui utilisent Entity Framework Core doivent être inscrits pour l' [injection de dépendances](xref:fundamentals/dependency-injection) à l’aide de [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="eb529-212">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="eb529-213">`ContactIsOwnerAuthorizationHandler`Utilise ASP.net Core [Identity](xref:security/authentication/identity) , qui repose sur Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="eb529-213">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="eb529-214">Inscrire les gestionnaires auprès de la collection de services afin qu’ils soient disponibles pour le `ContactsController` via l' [injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="eb529-214">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="eb529-215">Ajoutez le code suivant à la fin de `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="eb529-215">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="eb529-216">`ContactAdministratorsAuthorizationHandler` et `ContactManagerAuthorizationHandler` sont ajoutés en tant que singletons.</span><span class="sxs-lookup"><span data-stu-id="eb529-216">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="eb529-217">Il s’agit de singletons, car ils n’utilisent pas EF et toutes les informations nécessaires sont dans le `Context` paramètre de la `HandleRequirementAsync` méthode.</span><span class="sxs-lookup"><span data-stu-id="eb529-217">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="eb529-218">Autorisation du support</span><span class="sxs-lookup"><span data-stu-id="eb529-218">Support authorization</span></span>

<span data-ttu-id="eb529-219">Dans cette section, vous allez mettre à jour les Razor pages et ajouter une classe d’exigences d’opérations.</span><span class="sxs-lookup"><span data-stu-id="eb529-219">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="eb529-220">Examiner la classe des exigences relatives aux opérations de contact</span><span class="sxs-lookup"><span data-stu-id="eb529-220">Review the contact operations requirements class</span></span>

<span data-ttu-id="eb529-221">Passez en revue la `ContactOperations` classe.</span><span class="sxs-lookup"><span data-stu-id="eb529-221">Review the `ContactOperations` class.</span></span> <span data-ttu-id="eb529-222">Cette classe contient les spécifications prises en charge par l’application :</span><span class="sxs-lookup"><span data-stu-id="eb529-222">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="eb529-223">Créer une classe de base pour les Razor pages contacts</span><span class="sxs-lookup"><span data-stu-id="eb529-223">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="eb529-224">Créez une classe de base qui contient les services utilisés dans les Razor pages de contacts.</span><span class="sxs-lookup"><span data-stu-id="eb529-224">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="eb529-225">La classe de base place le code d’initialisation à un emplacement :</span><span class="sxs-lookup"><span data-stu-id="eb529-225">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="eb529-226">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="eb529-226">The preceding code:</span></span>

* <span data-ttu-id="eb529-227">Ajoute le `IAuthorizationService` service pour accéder aux gestionnaires d’autorisations.</span><span class="sxs-lookup"><span data-stu-id="eb529-227">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="eb529-228">Ajoute le Identity `UserManager` service.</span><span class="sxs-lookup"><span data-stu-id="eb529-228">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="eb529-229">Ajoutez la `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="eb529-229">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="eb529-230">Mettre à jour CreateModel</span><span class="sxs-lookup"><span data-stu-id="eb529-230">Update the CreateModel</span></span>

<span data-ttu-id="eb529-231">Mettez à jour le constructeur de modèle de page de création pour utiliser la `DI_BasePageModel` classe de base :</span><span class="sxs-lookup"><span data-stu-id="eb529-231">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="eb529-232">Mettez à jour la `CreateModel.OnPostAsync` méthode pour :</span><span class="sxs-lookup"><span data-stu-id="eb529-232">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="eb529-233">Ajoutez l’ID utilisateur au `Contact` modèle.</span><span class="sxs-lookup"><span data-stu-id="eb529-233">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="eb529-234">Appelez le gestionnaire d’autorisations pour vérifier que l’utilisateur a l’autorisation de créer des contacts.</span><span class="sxs-lookup"><span data-stu-id="eb529-234">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="eb529-235">Mettre à jour IndexModel</span><span class="sxs-lookup"><span data-stu-id="eb529-235">Update the IndexModel</span></span>

<span data-ttu-id="eb529-236">Mettez à jour la `OnGetAsync` méthode afin que seuls les contacts approuvés soient affichés pour les utilisateurs généraux :</span><span class="sxs-lookup"><span data-stu-id="eb529-236">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="eb529-237">Mettre à jour EditModel</span><span class="sxs-lookup"><span data-stu-id="eb529-237">Update the EditModel</span></span>

<span data-ttu-id="eb529-238">Ajoutez un gestionnaire d’autorisations pour vérifier que l’utilisateur possède le contact.</span><span class="sxs-lookup"><span data-stu-id="eb529-238">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="eb529-239">Étant donné que l’autorisation des ressources est en cours de validation, l' `[Authorize]` attribut n’est pas suffisant.</span><span class="sxs-lookup"><span data-stu-id="eb529-239">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="eb529-240">L’application n’a pas accès à la ressource lors de l’évaluation des attributs.</span><span class="sxs-lookup"><span data-stu-id="eb529-240">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="eb529-241">L’autorisation basée sur les ressources doit être impérative.</span><span class="sxs-lookup"><span data-stu-id="eb529-241">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="eb529-242">Les vérifications doivent être effectuées une fois que l’application a accès à la ressource, soit en la chargeant dans le modèle de page, soit en la chargeant dans le gestionnaire lui-même.</span><span class="sxs-lookup"><span data-stu-id="eb529-242">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="eb529-243">Vous accédez fréquemment à la ressource en passant la clé de ressource.</span><span class="sxs-lookup"><span data-stu-id="eb529-243">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="eb529-244">Mettre à jour DeleteModel</span><span class="sxs-lookup"><span data-stu-id="eb529-244">Update the DeleteModel</span></span>

<span data-ttu-id="eb529-245">Mettez à jour le modèle de page de suppression pour utiliser le gestionnaire d’autorisations afin de vérifier que l’utilisateur dispose de l’autorisation de suppression sur le contact.</span><span class="sxs-lookup"><span data-stu-id="eb529-245">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="eb529-246">Injecter le service d’autorisation dans les vues</span><span class="sxs-lookup"><span data-stu-id="eb529-246">Inject the authorization service into the views</span></span>

<span data-ttu-id="eb529-247">Actuellement, l’interface utilisateur affiche les liens modifier et supprimer pour les contacts que l’utilisateur ne peut pas modifier.</span><span class="sxs-lookup"><span data-stu-id="eb529-247">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="eb529-248">Injecter le service d’autorisation dans le fichier *pages/_ViewImports. cshtml* afin qu’il soit disponible pour tous les affichages :</span><span class="sxs-lookup"><span data-stu-id="eb529-248">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="eb529-249">Le balisage précédent ajoute plusieurs `using` instructions.</span><span class="sxs-lookup"><span data-stu-id="eb529-249">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="eb529-250">Mettez à jour les liens **modifier** et **supprimer** dans *pages/contacts/index. cshtml* afin qu’ils soient affichés uniquement pour les utilisateurs disposant des autorisations appropriées :</span><span class="sxs-lookup"><span data-stu-id="eb529-250">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="eb529-251">Le masquage des liens des utilisateurs qui n’ont pas l’autorisation de modifier des données ne sécurise pas l’application.</span><span class="sxs-lookup"><span data-stu-id="eb529-251">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="eb529-252">Le masquage des liens rend l’application plus conviviale en affichant uniquement des liens valides.</span><span class="sxs-lookup"><span data-stu-id="eb529-252">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="eb529-253">Les utilisateurs peuvent pirater les URL générées pour appeler des opérations de modification et de suppression sur les données qu’ils ne possèdent pas.</span><span class="sxs-lookup"><span data-stu-id="eb529-253">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="eb529-254">La Razor page ou le contrôleur doit appliquer les vérifications d’accès pour sécuriser les données.</span><span class="sxs-lookup"><span data-stu-id="eb529-254">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="eb529-255">Mettre à jour les détails</span><span class="sxs-lookup"><span data-stu-id="eb529-255">Update Details</span></span>

<span data-ttu-id="eb529-256">Mettez à jour la vue Détails pour permettre aux responsables d’approuver ou de rejeter les contacts :</span><span class="sxs-lookup"><span data-stu-id="eb529-256">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="eb529-257">Mettez à jour le modèle de page de détails :</span><span class="sxs-lookup"><span data-stu-id="eb529-257">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="eb529-258">Ajouter ou supprimer un utilisateur dans un rôle</span><span class="sxs-lookup"><span data-stu-id="eb529-258">Add or remove a user to a role</span></span>

<span data-ttu-id="eb529-259">Consultez [ce numéro](https://github.com/dotnet/AspNetCore.Docs/issues/8502) pour plus d’informations sur :</span><span class="sxs-lookup"><span data-stu-id="eb529-259">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="eb529-260">Suppression des privilèges d’un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="eb529-260">Removing privileges from a user.</span></span> <span data-ttu-id="eb529-261">Par exemple, la désactivation d’un utilisateur dans une application de conversation.</span><span class="sxs-lookup"><span data-stu-id="eb529-261">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="eb529-262">Ajout de privilèges à un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="eb529-262">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="eb529-263">Différences entre Challenge et interdire</span><span class="sxs-lookup"><span data-stu-id="eb529-263">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="eb529-264">Cette application définit la stratégie par défaut pour [exiger des utilisateurs authentifiés](#rau).</span><span class="sxs-lookup"><span data-stu-id="eb529-264">This app sets the default policy to [require authenticated users](#rau).</span></span> <span data-ttu-id="eb529-265">Le code suivant autorise les utilisateurs anonymes.</span><span class="sxs-lookup"><span data-stu-id="eb529-265">The following code allows anonymous users.</span></span> <span data-ttu-id="eb529-266">Les utilisateurs anonymes sont autorisés à afficher les différences entre la stimulation et l’interdiction.</span><span class="sxs-lookup"><span data-stu-id="eb529-266">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="eb529-267">Dans le code précédent :</span><span class="sxs-lookup"><span data-stu-id="eb529-267">In the preceding code:</span></span>

* <span data-ttu-id="eb529-268">Lorsque l’utilisateur n’est **pas** authentifié, un `ChallengeResult` est retourné.</span><span class="sxs-lookup"><span data-stu-id="eb529-268">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="eb529-269">Lorsqu’un `ChallengeResult` est retourné, l’utilisateur est redirigé vers la page de connexion.</span><span class="sxs-lookup"><span data-stu-id="eb529-269">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="eb529-270">Lorsque l’utilisateur est authentifié, mais n’est pas autorisé, un `ForbidResult` est retourné.</span><span class="sxs-lookup"><span data-stu-id="eb529-270">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="eb529-271">Lorsqu’un `ForbidResult` est retourné, l’utilisateur est redirigé vers la page accès refusé.</span><span class="sxs-lookup"><span data-stu-id="eb529-271">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="eb529-272">Tester l’application terminée</span><span class="sxs-lookup"><span data-stu-id="eb529-272">Test the completed app</span></span>

<span data-ttu-id="eb529-273">Si vous n’avez pas encore défini de mot de passe pour les comptes d’utilisateurs amorcés, utilisez l' [outil secret Manager](xref:security/app-secrets#secret-manager) pour définir un mot de passe :</span><span class="sxs-lookup"><span data-stu-id="eb529-273">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="eb529-274">Choisissez un mot de passe fort : utilisez au moins huit caractères et au moins un caractère majuscule, un chiffre et un symbole.</span><span class="sxs-lookup"><span data-stu-id="eb529-274">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="eb529-275">Par exemple, `Passw0rd!` répond aux exigences de mot de passe fort.</span><span class="sxs-lookup"><span data-stu-id="eb529-275">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="eb529-276">Exécutez la commande suivante à partir du dossier du projet, où `<PW>` est le mot de passe :</span><span class="sxs-lookup"><span data-stu-id="eb529-276">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="eb529-277">Si l’application a des contacts :</span><span class="sxs-lookup"><span data-stu-id="eb529-277">If the app has contacts:</span></span>

* <span data-ttu-id="eb529-278">Supprimez tous les enregistrements de la `Contact` table.</span><span class="sxs-lookup"><span data-stu-id="eb529-278">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="eb529-279">Redémarrez l’application pour amorcer la base de données.</span><span class="sxs-lookup"><span data-stu-id="eb529-279">Restart the app to seed the database.</span></span>

<span data-ttu-id="eb529-280">Un moyen simple de tester l’application terminée consiste à lancer trois navigateurs différents (ou des sessions incognito/InPrivate).</span><span class="sxs-lookup"><span data-stu-id="eb529-280">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="eb529-281">Dans un navigateur, inscrivez un nouvel utilisateur (par exemple, `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="eb529-281">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="eb529-282">Connectez-vous à chaque navigateur avec un autre utilisateur.</span><span class="sxs-lookup"><span data-stu-id="eb529-282">Sign in to each browser with a different user.</span></span> <span data-ttu-id="eb529-283">Vérifiez les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="eb529-283">Verify the following operations:</span></span>

* <span data-ttu-id="eb529-284">Les utilisateurs inscrits peuvent afficher toutes les données de contact approuvées.</span><span class="sxs-lookup"><span data-stu-id="eb529-284">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="eb529-285">Les utilisateurs inscrits peuvent modifier/supprimer leurs propres données.</span><span class="sxs-lookup"><span data-stu-id="eb529-285">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="eb529-286">Les responsables peuvent approuver/rejeter les données de contact.</span><span class="sxs-lookup"><span data-stu-id="eb529-286">Managers can approve/reject contact data.</span></span> <span data-ttu-id="eb529-287">La `Details` vue affiche les boutons **approuver** et **rejeter** .</span><span class="sxs-lookup"><span data-stu-id="eb529-287">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="eb529-288">Les administrateurs peuvent approuver/refuser et modifier/supprimer toutes les données.</span><span class="sxs-lookup"><span data-stu-id="eb529-288">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="eb529-289">Utilisateur</span><span class="sxs-lookup"><span data-stu-id="eb529-289">User</span></span>                | <span data-ttu-id="eb529-290">Amorcé par l’application</span><span class="sxs-lookup"><span data-stu-id="eb529-290">Seeded by the app</span></span> | <span data-ttu-id="eb529-291">Options</span><span class="sxs-lookup"><span data-stu-id="eb529-291">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="eb529-292">Non</span><span class="sxs-lookup"><span data-stu-id="eb529-292">No</span></span>                | <span data-ttu-id="eb529-293">Modifiez/supprimez les données.</span><span class="sxs-lookup"><span data-stu-id="eb529-293">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="eb529-294">Oui</span><span class="sxs-lookup"><span data-stu-id="eb529-294">Yes</span></span>               | <span data-ttu-id="eb529-295">Approuver/refuser et modifier/supprimer des données.</span><span class="sxs-lookup"><span data-stu-id="eb529-295">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="eb529-296">Oui</span><span class="sxs-lookup"><span data-stu-id="eb529-296">Yes</span></span>               | <span data-ttu-id="eb529-297">Approuver/refuser et modifier/supprimer toutes les données.</span><span class="sxs-lookup"><span data-stu-id="eb529-297">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="eb529-298">Créez un contact dans le navigateur de l’administrateur.</span><span class="sxs-lookup"><span data-stu-id="eb529-298">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="eb529-299">Copiez l’URL de la suppression et de la modification à partir du contact de l’administrateur.</span><span class="sxs-lookup"><span data-stu-id="eb529-299">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="eb529-300">Collez ces liens dans le navigateur de l’utilisateur de test pour vérifier que l’utilisateur de test ne peut pas effectuer ces opérations.</span><span class="sxs-lookup"><span data-stu-id="eb529-300">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="eb529-301">Créer l’application de démarrage</span><span class="sxs-lookup"><span data-stu-id="eb529-301">Create the starter app</span></span>

* <span data-ttu-id="eb529-302">Créer une Razor application pages nommée « ContactManager »</span><span class="sxs-lookup"><span data-stu-id="eb529-302">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="eb529-303">Créez l’application avec des **comptes d’utilisateur individuels**.</span><span class="sxs-lookup"><span data-stu-id="eb529-303">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="eb529-304">Nommez-la « ContactManager » pour que l’espace de noms corresponde à l’espace de noms utilisé dans l’exemple.</span><span class="sxs-lookup"><span data-stu-id="eb529-304">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="eb529-305">`-uld` spécifie la base de données locale au lieu de SQLite</span><span class="sxs-lookup"><span data-stu-id="eb529-305">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="eb529-306">Ajouter des *modèles/contact. cs*:</span><span class="sxs-lookup"><span data-stu-id="eb529-306">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="eb529-307">Structurez le `Contact` modèle.</span><span class="sxs-lookup"><span data-stu-id="eb529-307">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="eb529-308">Créez la migration initiale et mettez à jour la base de données :</span><span class="sxs-lookup"><span data-stu-id="eb529-308">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="eb529-309">Si vous rencontrez un bogue avec la `dotnet aspnet-codegenerator razorpage` commande, consultez [ce problème GitHub](https://github.com/aspnet/Scaffolding/issues/984).</span><span class="sxs-lookup"><span data-stu-id="eb529-309">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="eb529-310">Mettez à jour l’ancre **ContactManager** dans le fichier *pages/shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="eb529-310">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="eb529-311">Tester l’application en créant, en modifiant et en supprimant un contact</span><span class="sxs-lookup"><span data-stu-id="eb529-311">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="eb529-312">Amorcer la base de données</span><span class="sxs-lookup"><span data-stu-id="eb529-312">Seed the database</span></span>

<span data-ttu-id="eb529-313">Ajoutez la classe [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) au dossier *Data* :</span><span class="sxs-lookup"><span data-stu-id="eb529-313">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="eb529-314">Appeler `SeedData.Initialize` à partir de `Main` :</span><span class="sxs-lookup"><span data-stu-id="eb529-314">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="eb529-315">Vérifiez que l’application a amorcé la base de données.</span><span class="sxs-lookup"><span data-stu-id="eb529-315">Test that the app seeded the database.</span></span> <span data-ttu-id="eb529-316">Si la base de coordonnées contient des lignes, la méthode Seed ne s’exécute pas.</span><span class="sxs-lookup"><span data-stu-id="eb529-316">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="eb529-317">Ce didacticiel montre comment créer une application Web ASP.NET Core avec les données utilisateur protégées par l’autorisation.</span><span class="sxs-lookup"><span data-stu-id="eb529-317">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="eb529-318">Il affiche la liste des contacts que les utilisateurs authentifiés (inscrits) ont créés.</span><span class="sxs-lookup"><span data-stu-id="eb529-318">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="eb529-319">Il existe trois groupes de sécurité :</span><span class="sxs-lookup"><span data-stu-id="eb529-319">There are three security groups:</span></span>

* <span data-ttu-id="eb529-320">Les **utilisateurs inscrits** peuvent afficher toutes les données approuvées et peuvent modifier/supprimer leurs propres données.</span><span class="sxs-lookup"><span data-stu-id="eb529-320">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="eb529-321">Les **responsables** peuvent approuver ou rejeter les données de contact.</span><span class="sxs-lookup"><span data-stu-id="eb529-321">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="eb529-322">Seuls les contacts approuvés sont visibles pour les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="eb529-322">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="eb529-323">**Les administrateurs** peuvent approuver/refuser et modifier/supprimer des données.</span><span class="sxs-lookup"><span data-stu-id="eb529-323">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="eb529-324">Dans l’image suivante, l’utilisateur Rick ( `rick@example.com` ) est connecté.</span><span class="sxs-lookup"><span data-stu-id="eb529-324">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="eb529-325">Rick peut uniquement afficher les contacts approuvés et **modifier** / **supprimer** / **créer** des liens pour ses contacts.</span><span class="sxs-lookup"><span data-stu-id="eb529-325">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="eb529-326">Seul le dernier enregistrement créé par Rick affiche des liens **modifier** et **supprimer** .</span><span class="sxs-lookup"><span data-stu-id="eb529-326">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="eb529-327">Les autres utilisateurs ne verront pas le dernier enregistrement jusqu’à ce qu’un responsable ou un administrateur change l’État en « approuvé ».</span><span class="sxs-lookup"><span data-stu-id="eb529-327">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Capture d’écran montrant que Rick est connecté](secure-data/_static/rick.png)

<span data-ttu-id="eb529-329">Dans l’image suivante, `manager@contoso.com` est connecté et dans le rôle du responsable :</span><span class="sxs-lookup"><span data-stu-id="eb529-329">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Capture d’écran montrant la manager@contoso.com connexion](secure-data/_static/manager1.png)

<span data-ttu-id="eb529-331">L’illustration suivante montre la vue des détails du gestionnaire d’un contact :</span><span class="sxs-lookup"><span data-stu-id="eb529-331">The following image shows the managers details view of a contact:</span></span>

![Vue du gestionnaire d’un contact](secure-data/_static/manager.png)

<span data-ttu-id="eb529-333">Les boutons **approuver** et **rejeter** s’affichent uniquement pour les responsables et les administrateurs.</span><span class="sxs-lookup"><span data-stu-id="eb529-333">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="eb529-334">Dans l’image suivante, `admin@contoso.com` est connecté et dans le rôle de l’administrateur :</span><span class="sxs-lookup"><span data-stu-id="eb529-334">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Capture d’écran montrant la admin@contoso.com connexion](secure-data/_static/admin.png)

<span data-ttu-id="eb529-336">L’administrateur dispose de tous les privilèges.</span><span class="sxs-lookup"><span data-stu-id="eb529-336">The administrator has all privileges.</span></span> <span data-ttu-id="eb529-337">Elle peut lire/modifier/supprimer un contact et modifier l’état des contacts.</span><span class="sxs-lookup"><span data-stu-id="eb529-337">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="eb529-338">L’application a été créée par la [génération](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) de modèles automatique du `Contact` modèle suivant :</span><span class="sxs-lookup"><span data-stu-id="eb529-338">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="eb529-339">L’exemple contient les gestionnaires d’autorisations suivants :</span><span class="sxs-lookup"><span data-stu-id="eb529-339">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="eb529-340">`ContactIsOwnerAuthorizationHandler`: Garantit qu’un utilisateur peut uniquement modifier ses données.</span><span class="sxs-lookup"><span data-stu-id="eb529-340">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="eb529-341">`ContactManagerAuthorizationHandler`: Permet aux gestionnaires d’approuver ou de rejeter les contacts.</span><span class="sxs-lookup"><span data-stu-id="eb529-341">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="eb529-342">`ContactAdministratorsAuthorizationHandler`: Permet aux administrateurs d’approuver ou de refuser des contacts, ainsi que de modifier/supprimer des contacts.</span><span class="sxs-lookup"><span data-stu-id="eb529-342">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="eb529-343">Prérequis</span><span class="sxs-lookup"><span data-stu-id="eb529-343">Prerequisites</span></span>

<span data-ttu-id="eb529-344">Ce didacticiel est avancé.</span><span class="sxs-lookup"><span data-stu-id="eb529-344">This tutorial is advanced.</span></span> <span data-ttu-id="eb529-345">Vous devez connaître les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="eb529-345">You should be familiar with:</span></span>

* [<span data-ttu-id="eb529-346">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="eb529-346">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="eb529-347">Authentification</span><span class="sxs-lookup"><span data-stu-id="eb529-347">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="eb529-348">Confirmation de compte et récupération de mot de passe</span><span class="sxs-lookup"><span data-stu-id="eb529-348">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="eb529-349">Autorisation</span><span class="sxs-lookup"><span data-stu-id="eb529-349">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="eb529-350">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="eb529-350">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="eb529-351">L’application de démarrage et la fin de l’application</span><span class="sxs-lookup"><span data-stu-id="eb529-351">The starter and completed app</span></span>

<span data-ttu-id="eb529-352">[Téléchargez](xref:index#how-to-download-a-sample) l’application [terminée](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/secure-data/samples) .</span><span class="sxs-lookup"><span data-stu-id="eb529-352">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="eb529-353">[Testez](#test-the-completed-app) l’application terminée afin de vous familiariser avec ses fonctionnalités de sécurité.</span><span class="sxs-lookup"><span data-stu-id="eb529-353">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="eb529-354">L’application de démarrage</span><span class="sxs-lookup"><span data-stu-id="eb529-354">The starter app</span></span>

<span data-ttu-id="eb529-355">[Téléchargez](xref:index#how-to-download-a-sample) l’application de [démarrage](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/secure-data/samples/) .</span><span class="sxs-lookup"><span data-stu-id="eb529-355">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="eb529-356">Exécutez l’application, appuyez sur le lien **ContactManager** et vérifiez que vous pouvez créer, modifier et supprimer un contact.</span><span class="sxs-lookup"><span data-stu-id="eb529-356">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="eb529-357">Sécuriser les données utilisateur</span><span class="sxs-lookup"><span data-stu-id="eb529-357">Secure user data</span></span>

<span data-ttu-id="eb529-358">Les sections suivantes présentent les principales étapes à suivre pour créer l’application de données utilisateur sécurisé.</span><span class="sxs-lookup"><span data-stu-id="eb529-358">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="eb529-359">Il peut s’avérer utile de faire référence au projet terminé.</span><span class="sxs-lookup"><span data-stu-id="eb529-359">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="eb529-360">Lier les données de contact à l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="eb529-360">Tie the contact data to the user</span></span>

<span data-ttu-id="eb529-361">Utilisez l' [Identity](xref:security/authentication/identity) ID d’utilisateur ASP.net pour vous assurer que les utilisateurs peuvent modifier leurs données, mais pas les données d’autres utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="eb529-361">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="eb529-362">Ajoutez `OwnerID` et `ContactStatus` au `Contact` modèle :</span><span class="sxs-lookup"><span data-stu-id="eb529-362">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="eb529-363">`OwnerID` ID de l’utilisateur de la `AspNetUser` table dans la [Identity](xref:security/authentication/identity) base de données.</span><span class="sxs-lookup"><span data-stu-id="eb529-363">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="eb529-364">Le `Status` champ détermine si un contact est visible par les utilisateurs généraux.</span><span class="sxs-lookup"><span data-stu-id="eb529-364">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="eb529-365">Créez une nouvelle migration et mettez à jour la base de données :</span><span class="sxs-lookup"><span data-stu-id="eb529-365">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="eb529-366">Ajouter des services de rôle à Identity</span><span class="sxs-lookup"><span data-stu-id="eb529-366">Add Role services to Identity</span></span>

<span data-ttu-id="eb529-367">Ajoutez [rôles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) pour ajouter des services de rôle :</span><span class="sxs-lookup"><span data-stu-id="eb529-367">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=11)]

### <a name="require-authenticated-users"></a><span data-ttu-id="eb529-368">Exiger des utilisateurs authentifiés</span><span class="sxs-lookup"><span data-stu-id="eb529-368">Require authenticated users</span></span>

<span data-ttu-id="eb529-369">Définissez la stratégie d’authentification par défaut pour exiger que les utilisateurs soient authentifiés :</span><span class="sxs-lookup"><span data-stu-id="eb529-369">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="eb529-370">Vous pouvez refuser l’authentification au niveau de la Razor page, du contrôleur ou de la méthode d’action avec l' `[AllowAnonymous]` attribut.</span><span class="sxs-lookup"><span data-stu-id="eb529-370">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="eb529-371">La définition de la stratégie d’authentification par défaut pour exiger que les utilisateurs soient authentifiés protège les pages et les contrôleurs qui viennent d’être ajoutés Razor .</span><span class="sxs-lookup"><span data-stu-id="eb529-371">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="eb529-372">L’authentification requise par défaut est plus sécurisée que le fait de s’appuyer sur de nouveaux contrôleurs et Razor pages pour inclure l' `[Authorize]` attribut.</span><span class="sxs-lookup"><span data-stu-id="eb529-372">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="eb529-373">Ajoutez [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) aux pages d’index, à propos de et contact afin que les utilisateurs anonymes puissent obtenir des informations sur le site avant de s’inscrire.</span><span class="sxs-lookup"><span data-stu-id="eb529-373">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="eb529-374">Configurer le compte de test</span><span class="sxs-lookup"><span data-stu-id="eb529-374">Configure the test account</span></span>

<span data-ttu-id="eb529-375">La `SeedData` classe crée deux comptes : administrateur et gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="eb529-375">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="eb529-376">Utilisez l' [outil secret Manager](xref:security/app-secrets) pour définir un mot de passe pour ces comptes.</span><span class="sxs-lookup"><span data-stu-id="eb529-376">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="eb529-377">Définissez le mot de passe à partir du répertoire du projet (répertoire contenant *Program.cs*) :</span><span class="sxs-lookup"><span data-stu-id="eb529-377">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="eb529-378">Si aucun mot de passe fort n’est spécifié, une exception est levée lorsque la `SeedData.Initialize` méthode est appelée.</span><span class="sxs-lookup"><span data-stu-id="eb529-378">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="eb529-379">Mettre à jour `Main` pour utiliser le mot de passe de test :</span><span class="sxs-lookup"><span data-stu-id="eb529-379">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="eb529-380">Créer les comptes de test et mettre à jour les contacts</span><span class="sxs-lookup"><span data-stu-id="eb529-380">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="eb529-381">Mettez à jour la `Initialize` méthode dans la `SeedData` classe pour créer les comptes de test :</span><span class="sxs-lookup"><span data-stu-id="eb529-381">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="eb529-382">Ajoutez l’ID d’utilisateur administrateur et `ContactStatus` les contacts.</span><span class="sxs-lookup"><span data-stu-id="eb529-382">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="eb529-383">Faites de l’un des contacts « soumis » et un « rejeté ».</span><span class="sxs-lookup"><span data-stu-id="eb529-383">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="eb529-384">Ajoutez l’ID d’utilisateur et l’État à tous les contacts.</span><span class="sxs-lookup"><span data-stu-id="eb529-384">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="eb529-385">Un seul contact est affiché :</span><span class="sxs-lookup"><span data-stu-id="eb529-385">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="eb529-386">Créer des gestionnaires d’autorisation propriétaire, responsable et administrateur</span><span class="sxs-lookup"><span data-stu-id="eb529-386">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="eb529-387">Créez un dossier *authorization* et créez une `ContactIsOwnerAuthorizationHandler` classe dans celui-ci.</span><span class="sxs-lookup"><span data-stu-id="eb529-387">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="eb529-388">Le `ContactIsOwnerAuthorizationHandler` vérifie que l’utilisateur agissant sur une ressource possède la ressource.</span><span class="sxs-lookup"><span data-stu-id="eb529-388">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="eb529-389">`ContactIsOwnerAuthorizationHandler`Contexte des appels [. Réussie](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) si l’utilisateur authentifié actuel est le propriétaire du contact.</span><span class="sxs-lookup"><span data-stu-id="eb529-389">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="eb529-390">Les gestionnaires d’autorisations sont généralement :</span><span class="sxs-lookup"><span data-stu-id="eb529-390">Authorization handlers generally:</span></span>

* <span data-ttu-id="eb529-391">Retourne `context.Succeed` lorsque les spécifications sont satisfaites.</span><span class="sxs-lookup"><span data-stu-id="eb529-391">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="eb529-392">Retourne `Task.CompletedTask` lorsque les spécifications ne sont pas remplies.</span><span class="sxs-lookup"><span data-stu-id="eb529-392">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="eb529-393">`Task.CompletedTask` n’est pas un succès ou un échec &mdash; . il permet l’exécution d’autres gestionnaires d’autorisations.</span><span class="sxs-lookup"><span data-stu-id="eb529-393">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="eb529-394">Si vous devez faire échouer explicitement, retournez le [contexte. Échoue](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="eb529-394">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="eb529-395">L’application permet aux propriétaires de contacts de modifier/supprimer/créer leurs propres données.</span><span class="sxs-lookup"><span data-stu-id="eb529-395">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="eb529-396">`ContactIsOwnerAuthorizationHandler` n’a pas besoin de vérifier l’opération passée dans le paramètre d’exigence.</span><span class="sxs-lookup"><span data-stu-id="eb529-396">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="eb529-397">Créer un gestionnaire d’autorisations de gestionnaire</span><span class="sxs-lookup"><span data-stu-id="eb529-397">Create a manager authorization handler</span></span>

<span data-ttu-id="eb529-398">Créez une `ContactManagerAuthorizationHandler` classe dans le dossier *authorization* .</span><span class="sxs-lookup"><span data-stu-id="eb529-398">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="eb529-399">Le `ContactManagerAuthorizationHandler` vérifie que l’utilisateur agissant sur la ressource est un responsable.</span><span class="sxs-lookup"><span data-stu-id="eb529-399">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="eb529-400">Seuls les responsables peuvent approuver ou rejeter les modifications de contenu (nouveaux ou modifiés).</span><span class="sxs-lookup"><span data-stu-id="eb529-400">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="eb529-401">Créer un gestionnaire d’autorisations d’administrateur</span><span class="sxs-lookup"><span data-stu-id="eb529-401">Create an administrator authorization handler</span></span>

<span data-ttu-id="eb529-402">Créez une `ContactAdministratorsAuthorizationHandler` classe dans le dossier *authorization* .</span><span class="sxs-lookup"><span data-stu-id="eb529-402">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="eb529-403">Le `ContactAdministratorsAuthorizationHandler` vérifie que l’utilisateur agissant sur la ressource est un administrateur.</span><span class="sxs-lookup"><span data-stu-id="eb529-403">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="eb529-404">L’administrateur peut effectuer toutes les opérations.</span><span class="sxs-lookup"><span data-stu-id="eb529-404">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="eb529-405">Inscrire les gestionnaires d’autorisations</span><span class="sxs-lookup"><span data-stu-id="eb529-405">Register the authorization handlers</span></span>

<span data-ttu-id="eb529-406">Les services qui utilisent Entity Framework Core doivent être inscrits pour l' [injection de dépendances](xref:fundamentals/dependency-injection) à l’aide de [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="eb529-406">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="eb529-407">`ContactIsOwnerAuthorizationHandler`Utilise ASP.net Core [Identity](xref:security/authentication/identity) , qui repose sur Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="eb529-407">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="eb529-408">Inscrire les gestionnaires auprès de la collection de services afin qu’ils soient disponibles pour le `ContactsController` via l' [injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="eb529-408">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="eb529-409">Ajoutez le code suivant à la fin de `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="eb529-409">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="eb529-410">`ContactAdministratorsAuthorizationHandler` et `ContactManagerAuthorizationHandler` sont ajoutés en tant que singletons.</span><span class="sxs-lookup"><span data-stu-id="eb529-410">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="eb529-411">Il s’agit de singletons, car ils n’utilisent pas EF et toutes les informations nécessaires sont dans le `Context` paramètre de la `HandleRequirementAsync` méthode.</span><span class="sxs-lookup"><span data-stu-id="eb529-411">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="eb529-412">Autorisation du support</span><span class="sxs-lookup"><span data-stu-id="eb529-412">Support authorization</span></span>

<span data-ttu-id="eb529-413">Dans cette section, vous allez mettre à jour les Razor pages et ajouter une classe d’exigences d’opérations.</span><span class="sxs-lookup"><span data-stu-id="eb529-413">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="eb529-414">Examiner la classe des exigences relatives aux opérations de contact</span><span class="sxs-lookup"><span data-stu-id="eb529-414">Review the contact operations requirements class</span></span>

<span data-ttu-id="eb529-415">Passez en revue la `ContactOperations` classe.</span><span class="sxs-lookup"><span data-stu-id="eb529-415">Review the `ContactOperations` class.</span></span> <span data-ttu-id="eb529-416">Cette classe contient les spécifications prises en charge par l’application :</span><span class="sxs-lookup"><span data-stu-id="eb529-416">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="eb529-417">Créer une classe de base pour les Razor pages contacts</span><span class="sxs-lookup"><span data-stu-id="eb529-417">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="eb529-418">Créez une classe de base qui contient les services utilisés dans les Razor pages de contacts.</span><span class="sxs-lookup"><span data-stu-id="eb529-418">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="eb529-419">La classe de base place le code d’initialisation à un emplacement :</span><span class="sxs-lookup"><span data-stu-id="eb529-419">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="eb529-420">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="eb529-420">The preceding code:</span></span>

* <span data-ttu-id="eb529-421">Ajoute le `IAuthorizationService` service pour accéder aux gestionnaires d’autorisations.</span><span class="sxs-lookup"><span data-stu-id="eb529-421">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="eb529-422">Ajoute le Identity `UserManager` service.</span><span class="sxs-lookup"><span data-stu-id="eb529-422">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="eb529-423">Ajoutez la `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="eb529-423">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="eb529-424">Mettre à jour CreateModel</span><span class="sxs-lookup"><span data-stu-id="eb529-424">Update the CreateModel</span></span>

<span data-ttu-id="eb529-425">Mettez à jour le constructeur de modèle de page de création pour utiliser la `DI_BasePageModel` classe de base :</span><span class="sxs-lookup"><span data-stu-id="eb529-425">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="eb529-426">Mettez à jour la `CreateModel.OnPostAsync` méthode pour :</span><span class="sxs-lookup"><span data-stu-id="eb529-426">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="eb529-427">Ajoutez l’ID utilisateur au `Contact` modèle.</span><span class="sxs-lookup"><span data-stu-id="eb529-427">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="eb529-428">Appelez le gestionnaire d’autorisations pour vérifier que l’utilisateur a l’autorisation de créer des contacts.</span><span class="sxs-lookup"><span data-stu-id="eb529-428">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="eb529-429">Mettre à jour IndexModel</span><span class="sxs-lookup"><span data-stu-id="eb529-429">Update the IndexModel</span></span>

<span data-ttu-id="eb529-430">Mettez à jour la `OnGetAsync` méthode afin que seuls les contacts approuvés soient affichés pour les utilisateurs généraux :</span><span class="sxs-lookup"><span data-stu-id="eb529-430">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="eb529-431">Mettre à jour EditModel</span><span class="sxs-lookup"><span data-stu-id="eb529-431">Update the EditModel</span></span>

<span data-ttu-id="eb529-432">Ajoutez un gestionnaire d’autorisations pour vérifier que l’utilisateur possède le contact.</span><span class="sxs-lookup"><span data-stu-id="eb529-432">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="eb529-433">Étant donné que l’autorisation des ressources est en cours de validation, l' `[Authorize]` attribut n’est pas suffisant.</span><span class="sxs-lookup"><span data-stu-id="eb529-433">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="eb529-434">L’application n’a pas accès à la ressource lors de l’évaluation des attributs.</span><span class="sxs-lookup"><span data-stu-id="eb529-434">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="eb529-435">L’autorisation basée sur les ressources doit être impérative.</span><span class="sxs-lookup"><span data-stu-id="eb529-435">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="eb529-436">Les vérifications doivent être effectuées une fois que l’application a accès à la ressource, soit en la chargeant dans le modèle de page, soit en la chargeant dans le gestionnaire lui-même.</span><span class="sxs-lookup"><span data-stu-id="eb529-436">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="eb529-437">Vous accédez fréquemment à la ressource en passant la clé de ressource.</span><span class="sxs-lookup"><span data-stu-id="eb529-437">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="eb529-438">Mettre à jour DeleteModel</span><span class="sxs-lookup"><span data-stu-id="eb529-438">Update the DeleteModel</span></span>

<span data-ttu-id="eb529-439">Mettez à jour le modèle de page de suppression pour utiliser le gestionnaire d’autorisations afin de vérifier que l’utilisateur dispose de l’autorisation de suppression sur le contact.</span><span class="sxs-lookup"><span data-stu-id="eb529-439">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="eb529-440">Injecter le service d’autorisation dans les vues</span><span class="sxs-lookup"><span data-stu-id="eb529-440">Inject the authorization service into the views</span></span>

<span data-ttu-id="eb529-441">Actuellement, l’interface utilisateur affiche les liens modifier et supprimer pour les contacts que l’utilisateur ne peut pas modifier.</span><span class="sxs-lookup"><span data-stu-id="eb529-441">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="eb529-442">Injecter le service d’autorisation dans le fichier *views/_ViewImports. cshtml* afin qu’il soit disponible pour tous les affichages :</span><span class="sxs-lookup"><span data-stu-id="eb529-442">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="eb529-443">Le balisage précédent ajoute plusieurs `using` instructions.</span><span class="sxs-lookup"><span data-stu-id="eb529-443">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="eb529-444">Mettez à jour les liens **modifier** et **supprimer** dans *pages/contacts/index. cshtml* afin qu’ils soient affichés uniquement pour les utilisateurs disposant des autorisations appropriées :</span><span class="sxs-lookup"><span data-stu-id="eb529-444">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="eb529-445">Le masquage des liens des utilisateurs qui n’ont pas l’autorisation de modifier des données ne sécurise pas l’application.</span><span class="sxs-lookup"><span data-stu-id="eb529-445">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="eb529-446">Le masquage des liens rend l’application plus conviviale en affichant uniquement des liens valides.</span><span class="sxs-lookup"><span data-stu-id="eb529-446">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="eb529-447">Les utilisateurs peuvent pirater les URL générées pour appeler des opérations de modification et de suppression sur les données qu’ils ne possèdent pas.</span><span class="sxs-lookup"><span data-stu-id="eb529-447">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="eb529-448">La Razor page ou le contrôleur doit appliquer les vérifications d’accès pour sécuriser les données.</span><span class="sxs-lookup"><span data-stu-id="eb529-448">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="eb529-449">Mettre à jour les détails</span><span class="sxs-lookup"><span data-stu-id="eb529-449">Update Details</span></span>

<span data-ttu-id="eb529-450">Mettez à jour la vue Détails pour permettre aux responsables d’approuver ou de rejeter les contacts :</span><span class="sxs-lookup"><span data-stu-id="eb529-450">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="eb529-451">Mettez à jour le modèle de page de détails :</span><span class="sxs-lookup"><span data-stu-id="eb529-451">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="eb529-452">Ajouter ou supprimer un utilisateur dans un rôle</span><span class="sxs-lookup"><span data-stu-id="eb529-452">Add or remove a user to a role</span></span>

<span data-ttu-id="eb529-453">Consultez [ce numéro](https://github.com/dotnet/AspNetCore.Docs/issues/8502) pour plus d’informations sur :</span><span class="sxs-lookup"><span data-stu-id="eb529-453">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="eb529-454">Suppression des privilèges d’un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="eb529-454">Removing privileges from a user.</span></span> <span data-ttu-id="eb529-455">Par exemple, la désactivation d’un utilisateur dans une application de conversation.</span><span class="sxs-lookup"><span data-stu-id="eb529-455">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="eb529-456">Ajout de privilèges à un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="eb529-456">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="eb529-457">Tester l’application terminée</span><span class="sxs-lookup"><span data-stu-id="eb529-457">Test the completed app</span></span>

<span data-ttu-id="eb529-458">Si vous n’avez pas encore défini de mot de passe pour les comptes d’utilisateurs amorcés, utilisez l' [outil secret Manager](xref:security/app-secrets#secret-manager) pour définir un mot de passe :</span><span class="sxs-lookup"><span data-stu-id="eb529-458">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="eb529-459">Choisissez un mot de passe fort : utilisez au moins huit caractères et au moins un caractère majuscule, un chiffre et un symbole.</span><span class="sxs-lookup"><span data-stu-id="eb529-459">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="eb529-460">Par exemple, `Passw0rd!` répond aux exigences de mot de passe fort.</span><span class="sxs-lookup"><span data-stu-id="eb529-460">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="eb529-461">Exécutez la commande suivante à partir du dossier du projet, où `<PW>` est le mot de passe :</span><span class="sxs-lookup"><span data-stu-id="eb529-461">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="eb529-462">Supprimer et mettre à jour la base de données</span><span class="sxs-lookup"><span data-stu-id="eb529-462">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="eb529-463">Redémarrez l’application pour amorcer la base de données.</span><span class="sxs-lookup"><span data-stu-id="eb529-463">Restart the app to seed the database.</span></span>

<span data-ttu-id="eb529-464">Un moyen simple de tester l’application terminée consiste à lancer trois navigateurs différents (ou des sessions incognito/InPrivate).</span><span class="sxs-lookup"><span data-stu-id="eb529-464">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="eb529-465">Dans un navigateur, inscrivez un nouvel utilisateur (par exemple, `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="eb529-465">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="eb529-466">Connectez-vous à chaque navigateur avec un autre utilisateur.</span><span class="sxs-lookup"><span data-stu-id="eb529-466">Sign in to each browser with a different user.</span></span> <span data-ttu-id="eb529-467">Vérifiez les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="eb529-467">Verify the following operations:</span></span>

* <span data-ttu-id="eb529-468">Les utilisateurs inscrits peuvent afficher toutes les données de contact approuvées.</span><span class="sxs-lookup"><span data-stu-id="eb529-468">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="eb529-469">Les utilisateurs inscrits peuvent modifier/supprimer leurs propres données.</span><span class="sxs-lookup"><span data-stu-id="eb529-469">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="eb529-470">Les responsables peuvent approuver/rejeter les données de contact.</span><span class="sxs-lookup"><span data-stu-id="eb529-470">Managers can approve/reject contact data.</span></span> <span data-ttu-id="eb529-471">La `Details` vue affiche les boutons **approuver** et **rejeter** .</span><span class="sxs-lookup"><span data-stu-id="eb529-471">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="eb529-472">Les administrateurs peuvent approuver/refuser et modifier/supprimer toutes les données.</span><span class="sxs-lookup"><span data-stu-id="eb529-472">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="eb529-473">Utilisateur</span><span class="sxs-lookup"><span data-stu-id="eb529-473">User</span></span>                | <span data-ttu-id="eb529-474">Amorcé par l’application</span><span class="sxs-lookup"><span data-stu-id="eb529-474">Seeded by the app</span></span> | <span data-ttu-id="eb529-475">Options</span><span class="sxs-lookup"><span data-stu-id="eb529-475">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="eb529-476">Non</span><span class="sxs-lookup"><span data-stu-id="eb529-476">No</span></span>                | <span data-ttu-id="eb529-477">Modifiez/supprimez les données.</span><span class="sxs-lookup"><span data-stu-id="eb529-477">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="eb529-478">Oui</span><span class="sxs-lookup"><span data-stu-id="eb529-478">Yes</span></span>               | <span data-ttu-id="eb529-479">Approuver/refuser et modifier/supprimer des données.</span><span class="sxs-lookup"><span data-stu-id="eb529-479">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="eb529-480">Oui</span><span class="sxs-lookup"><span data-stu-id="eb529-480">Yes</span></span>               | <span data-ttu-id="eb529-481">Approuver/refuser et modifier/supprimer toutes les données.</span><span class="sxs-lookup"><span data-stu-id="eb529-481">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="eb529-482">Créez un contact dans le navigateur de l’administrateur.</span><span class="sxs-lookup"><span data-stu-id="eb529-482">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="eb529-483">Copiez l’URL de la suppression et de la modification à partir du contact de l’administrateur.</span><span class="sxs-lookup"><span data-stu-id="eb529-483">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="eb529-484">Collez ces liens dans le navigateur de l’utilisateur de test pour vérifier que l’utilisateur de test ne peut pas effectuer ces opérations.</span><span class="sxs-lookup"><span data-stu-id="eb529-484">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="eb529-485">Créer l’application de démarrage</span><span class="sxs-lookup"><span data-stu-id="eb529-485">Create the starter app</span></span>

* <span data-ttu-id="eb529-486">Créer une Razor application pages nommée « ContactManager »</span><span class="sxs-lookup"><span data-stu-id="eb529-486">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="eb529-487">Créez l’application avec des **comptes d’utilisateur individuels**.</span><span class="sxs-lookup"><span data-stu-id="eb529-487">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="eb529-488">Nommez-la « ContactManager » pour que l’espace de noms corresponde à l’espace de noms utilisé dans l’exemple.</span><span class="sxs-lookup"><span data-stu-id="eb529-488">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="eb529-489">`-uld` spécifie la base de données locale au lieu de SQLite</span><span class="sxs-lookup"><span data-stu-id="eb529-489">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="eb529-490">Ajouter des *modèles/contact. cs*:</span><span class="sxs-lookup"><span data-stu-id="eb529-490">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="eb529-491">Structurez le `Contact` modèle.</span><span class="sxs-lookup"><span data-stu-id="eb529-491">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="eb529-492">Créez la migration initiale et mettez à jour la base de données :</span><span class="sxs-lookup"><span data-stu-id="eb529-492">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="eb529-493">Mettez à jour l’ancre **ContactManager** dans le fichier *pages/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="eb529-493">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="eb529-494">Tester l’application en créant, en modifiant et en supprimant un contact</span><span class="sxs-lookup"><span data-stu-id="eb529-494">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="eb529-495">Amorcer la base de données</span><span class="sxs-lookup"><span data-stu-id="eb529-495">Seed the database</span></span>

<span data-ttu-id="eb529-496">Ajoutez la classe [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) au dossier de *données* .</span><span class="sxs-lookup"><span data-stu-id="eb529-496">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="eb529-497">Appeler `SeedData.Initialize` à partir de `Main` :</span><span class="sxs-lookup"><span data-stu-id="eb529-497">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="eb529-498">Vérifiez que l’application a amorcé la base de données.</span><span class="sxs-lookup"><span data-stu-id="eb529-498">Test that the app seeded the database.</span></span> <span data-ttu-id="eb529-499">Si la base de coordonnées contient des lignes, la méthode Seed ne s’exécute pas.</span><span class="sxs-lookup"><span data-stu-id="eb529-499">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="eb529-500">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="eb529-500">Additional resources</span></span>

* [<span data-ttu-id="eb529-501">Créer une application Web .NET Core et SQL Database dans Azure App Service</span><span class="sxs-lookup"><span data-stu-id="eb529-501">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="eb529-502">[ASP.net Core laboratoire d’autorisation](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="eb529-502">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="eb529-503">Ce laboratoire aborde plus en détail les fonctionnalités de sécurité présentées dans ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="eb529-503">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="eb529-504">Autorisation basée sur une stratégie personnalisée</span><span class="sxs-lookup"><span data-stu-id="eb529-504">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
