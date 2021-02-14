---
title: ASP.NET Core Blazor WebAssembly avec des groupes et des rôles Azure Active Directory
author: guardrex
description: Découvrez comment configurer Blazor WebAssembly pour utiliser des groupes et des rôles Azure Active Directory.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 01/24/2021
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
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: c180580ec56313e444f2daf2b7d08c4d909b498a
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280520"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-app-roles"></a><span data-ttu-id="16cae-103">Groupes Azure Active Directory (AAD), rôles d’administrateur et rôles d’application</span><span class="sxs-lookup"><span data-stu-id="16cae-103">Azure Active Directory (AAD) groups, Administrator Roles, and App Roles</span></span>

<span data-ttu-id="16cae-104">Azure Active Directory (AAD) fournit plusieurs approches d’autorisation qui peuvent être combinées avec ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="16cae-104">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="16cae-105">Groupes</span><span class="sxs-lookup"><span data-stu-id="16cae-105">Groups</span></span>
  * <span data-ttu-id="16cae-106">Sécurité</span><span class="sxs-lookup"><span data-stu-id="16cae-106">Security</span></span>
  * <span data-ttu-id="16cae-107">Microsoft 365</span><span class="sxs-lookup"><span data-stu-id="16cae-107">Microsoft 365</span></span>
  * <span data-ttu-id="16cae-108">Distribution</span><span class="sxs-lookup"><span data-stu-id="16cae-108">Distribution</span></span>
* <span data-ttu-id="16cae-109">Rôles</span><span class="sxs-lookup"><span data-stu-id="16cae-109">Roles</span></span>
  * <span data-ttu-id="16cae-110">Rôles d’administrateur AAD</span><span class="sxs-lookup"><span data-stu-id="16cae-110">AAD Administrator Roles</span></span>
  * <span data-ttu-id="16cae-111">Rôles d’application</span><span class="sxs-lookup"><span data-stu-id="16cae-111">App Roles</span></span>

<span data-ttu-id="16cae-112">Les instructions de cet article s’appliquent aux Blazor WebAssembly scénarios de déploiement AAD décrits dans les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="16cae-112">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="16cae-113">Autonome avec des comptes Microsoft</span><span class="sxs-lookup"><span data-stu-id="16cae-113">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="16cae-114">Autonome avec AAD</span><span class="sxs-lookup"><span data-stu-id="16cae-114">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="16cae-115">Hébergé avec AAD</span><span class="sxs-lookup"><span data-stu-id="16cae-115">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

<span data-ttu-id="16cae-116">L’aide de l’article fournit des instructions pour les applications client et serveur :</span><span class="sxs-lookup"><span data-stu-id="16cae-116">The article's guidance provides instructions for client and server apps:</span></span>

* <span data-ttu-id="16cae-117">**Client**: applications autonomes Blazor WebAssembly ou *`Client`* application d’une solution hébergée Blazor .</span><span class="sxs-lookup"><span data-stu-id="16cae-117">**CLIENT**: Standalone Blazor WebAssembly apps or the *`Client`* app of a hosted Blazor solution.</span></span>
* <span data-ttu-id="16cae-118">**Serveur**: autonome ASP.net Core les applications API serveur/API Web ou l' *`Server`* application d’une Blazor solution hébergée.</span><span class="sxs-lookup"><span data-stu-id="16cae-118">**SERVER**: Standalone ASP.NET Core server API/web API apps or the *`Server`* app of a hosted Blazor solution.</span></span>

## <a name="scopes"></a><span data-ttu-id="16cae-119">Étendues</span><span class="sxs-lookup"><span data-stu-id="16cae-119">Scopes</span></span>

<span data-ttu-id="16cae-120">Pour autoriser [Microsoft Graph](/graph/use-the-api) appels d’API pour les données de profil utilisateur, d’attribution de rôle et d’appartenance au groupe, le **client** est configuré avec ( `https://graph.microsoft.com/User.Read` ) [API Graph autorisation (étendue)](/graph/permissions-reference) dans le portail Azure.</span><span class="sxs-lookup"><span data-stu-id="16cae-120">To permit [Microsoft Graph API](/graph/use-the-api) calls for user profile, role assignment, and group membership data, the **CLIENT** is configured with (`https://graph.microsoft.com/User.Read`) [Graph API permission (scope)](/graph/permissions-reference) in the Azure portal.</span></span>

<span data-ttu-id="16cae-121">Une application **serveur** qui appelle API Graph pour les données d’appartenance aux rôles et aux groupes est fournie `GroupMember.Read.All` ( `https://graph.microsoft.com/GroupMember.Read.All` ) [API Graph autorisation (étendue)](/graph/permissions-reference) dans le portail Azure.</span><span class="sxs-lookup"><span data-stu-id="16cae-121">A **SERVER** app that calls Graph API for role and group membership data is provided `GroupMember.Read.All` (`https://graph.microsoft.com/GroupMember.Read.All`) [Graph API permission (scope)](/graph/permissions-reference) in the Azure portal.</span></span>

<span data-ttu-id="16cae-122">Ces étendues sont requises en plus des étendues requises dans les scénarios de déploiement AAD décrits dans les rubriques répertoriées dans la première section de cet article.</span><span class="sxs-lookup"><span data-stu-id="16cae-122">These scopes are required in addition to the scopes required in AAD deployment scenarios described by the topics listed in the first section of this article.</span></span>

> [!NOTE]
> <span data-ttu-id="16cae-123">Les mots « autorisation » et « étendue » sont utilisés de manière interchangeable dans le Portail Azure et dans divers ensembles de documentation Microsoft et externes.</span><span class="sxs-lookup"><span data-stu-id="16cae-123">The words "permission" and "scope" are used interchangeably in the Azure portal and in various Microsoft and external documentation sets.</span></span> <span data-ttu-id="16cae-124">Cet article utilise le mot « Scope » dans les limites des autorisations affectées à une application dans le Portail Azure.</span><span class="sxs-lookup"><span data-stu-id="16cae-124">This article uses the word "scope" throughout for the permissions assigned to an app in the Azure portal.</span></span>

## <a name="group-membership-claims-attribute"></a><span data-ttu-id="16cae-125">Attribut revendications d’appartenance au groupe</span><span class="sxs-lookup"><span data-stu-id="16cae-125">Group Membership Claims attribute</span></span>

<span data-ttu-id="16cae-126">Dans le manifeste de l’application dans le Portail Azure pour les applications **clientes** et **serveur** , affectez à l' [ `groupMembershipClaims` attribut](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute) la valeur `All` .</span><span class="sxs-lookup"><span data-stu-id="16cae-126">In the app's manifest in the the Azure portal for **CLIENT** and **SERVER** apps, set the [`groupMembershipClaims` attribute](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute) to `All`.</span></span> <span data-ttu-id="16cae-127">La valeur `All` permet d’obtenir tous les groupes de sécurité, groupes de distribution et rôles dont l’utilisateur connecté est membre.</span><span class="sxs-lookup"><span data-stu-id="16cae-127">A value of `All` results in obtaining all of the security groups, distribution groups, and roles that the signed-in user is a member of.</span></span>

1. <span data-ttu-id="16cae-128">Ouvrez l’inscription de l’application Portail Azure.</span><span class="sxs-lookup"><span data-stu-id="16cae-128">Open the app's Azure portal registration.</span></span>
1. <span data-ttu-id="16cae-129">Sélectionnez **gérer**  >  le **manifeste** dans la barre latérale.</span><span class="sxs-lookup"><span data-stu-id="16cae-129">Select **Manage** > **Manifest** in the sidebar.</span></span>
1. <span data-ttu-id="16cae-130">Recherchez l' `groupMembershipClaims` attribut.</span><span class="sxs-lookup"><span data-stu-id="16cae-130">Find the `groupMembershipClaims` attribute.</span></span>
1. <span data-ttu-id="16cae-131">Définissez la valeur sur `All`.</span><span class="sxs-lookup"><span data-stu-id="16cae-131">Set the value to `All`.</span></span>
1. <span data-ttu-id="16cae-132">Sélectionnez le bouton **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="16cae-132">Select the **Save** button.</span></span>

```json
"groupMembershipClaims": "All",
```

## <a name="custom-user-account"></a><span data-ttu-id="16cae-133">Compte d’utilisateur personnalisé</span><span class="sxs-lookup"><span data-stu-id="16cae-133">Custom user account</span></span>

<span data-ttu-id="16cae-134">Affectez des utilisateurs aux groupes de sécurité AAD et aux rôles d’administrateur AAD dans le Portail Azure.</span><span class="sxs-lookup"><span data-stu-id="16cae-134">Assign users to AAD security groups and AAD Administrator Roles in the Azure portal.</span></span>

<span data-ttu-id="16cae-135">Les exemples de cet article sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="16cae-135">The examples in this article:</span></span>

* <span data-ttu-id="16cae-136">Supposons qu’un utilisateur est affecté au rôle d' *administrateur de facturation* AAD dans le locataire portail Azure AAD pour l’autorisation d’accéder aux données de l’API du serveur.</span><span class="sxs-lookup"><span data-stu-id="16cae-136">Assume that a user is assigned to the AAD *Billing Administrator* role in the Azure portal AAD tenant for authorization to access server API data.</span></span>
* <span data-ttu-id="16cae-137">Utilisez des [stratégies d’autorisation](xref:security/authorization/policies) pour contrôler l’accès au sein des applications **clientes** et **serveur** .</span><span class="sxs-lookup"><span data-stu-id="16cae-137">Use [authorization policies](xref:security/authorization/policies) to control access within the **CLIENT** and **SERVER** apps.</span></span>

<span data-ttu-id="16cae-138">Dans l’application **cliente** , étendez <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> pour inclure les propriétés de :</span><span class="sxs-lookup"><span data-stu-id="16cae-138">In the **CLIENT** app, extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include properties for:</span></span>

* <span data-ttu-id="16cae-139">`Roles`: Tableau des rôles d’application AAD (abordé dans la section [rôles d’application](#app-roles) )</span><span class="sxs-lookup"><span data-stu-id="16cae-139">`Roles`: AAD App Roles array (covered in the [App Roles](#app-roles) section)</span></span>
* <span data-ttu-id="16cae-140">`Wids`: Rôles d’administrateur AAD dans des [revendications d’ID bien connues ( `wids` )](/azure/active-directory/develop/access-tokens#payload-claims)</span><span class="sxs-lookup"><span data-stu-id="16cae-140">`Wids`: AAD Administrator Roles in [well-known IDs claims (`wids`)](/azure/active-directory/develop/access-tokens#payload-claims)</span></span>
* <span data-ttu-id="16cae-141">`Oid`: Revendication d' [identificateur d’objet immuable ( `oid` )](/azure/active-directory/develop/id-tokens#payload-claims) (identifie de façon unique un utilisateur dans et entre les locataires)</span><span class="sxs-lookup"><span data-stu-id="16cae-141">`Oid`: Immutable [object identifier claim (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) (uniquely identifies a user within and across tenants)</span></span>

<span data-ttu-id="16cae-142">Assignez un tableau vide à chaque propriété de tableau afin que la vérification de `null` ne soit pas requise lorsque ces propriétés sont utilisées dans les `foreach` boucles.</span><span class="sxs-lookup"><span data-stu-id="16cae-142">Assign an empty array to each array property so that checking for `null` isn't required when these properties are used in `foreach` loops.</span></span>

<span data-ttu-id="16cae-143">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="16cae-143">`CustomUserAccount.cs`:</span></span>

```csharp
using System;
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("roles")]
    public string[] Roles { get; set; } = Array.Empty<string>();

    [JsonPropertyName("wids")]
    public string[] Wids { get; set; } = Array.Empty<string>();

    [JsonPropertyName("oid")]
    public string Oid { get; set; }
}
```

<span data-ttu-id="16cae-144">Ajoutez une référence de package au fichier projet de l’application **cliente** pour [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph) .</span><span class="sxs-lookup"><span data-stu-id="16cae-144">Add a package reference to the **CLIENT** app's project file for [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph).</span></span>

<span data-ttu-id="16cae-145">Ajoutez les classes et la configuration de l’utilitaire du SDK Graph dans la section *Kit de développement logiciel (SDK) Graph* de l' <xref:blazor/security/webassembly/graph-api#graph-sdk> article.</span><span class="sxs-lookup"><span data-stu-id="16cae-145">Add the Graph SDK utility classes and configuration in the *Graph SDK* section of the <xref:blazor/security/webassembly/graph-api#graph-sdk> article.</span></span> <span data-ttu-id="16cae-146">Dans la `GraphClientExtensions` classe, spécifiez l' `User.Read` étendue du jeton d’accès dans la `AuthenticateRequestAsync` méthode :</span><span class="sxs-lookup"><span data-stu-id="16cae-146">In the `GraphClientExtensions` class, specify the `User.Read` scope for the access token in the `AuthenticateRequestAsync` method:</span></span>

```csharp
var result = await TokenProvider.RequestAccessToken(
    new AccessTokenRequestOptions()
    {
        Scopes = new[] { "https://graph.microsoft.com/User.Read" }
    });
```

<span data-ttu-id="16cae-147">Ajoutez la fabrique de comptes d’utilisateur personnalisée suivante à l’application **cliente** .</span><span class="sxs-lookup"><span data-stu-id="16cae-147">Add the following custom user account factory to the **CLIENT** app.</span></span> <span data-ttu-id="16cae-148">La fabrique d’utilisateurs personnalisée est utilisée pour établir les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="16cae-148">The custom user factory is used to establish:</span></span>

* <span data-ttu-id="16cae-149">Revendications de rôle d’application ( `appRole` ) (traitées dans la section [rôles d’application](#app-roles) )</span><span class="sxs-lookup"><span data-stu-id="16cae-149">App Role claims (`appRole`) (covered in the [App Roles](#app-roles) section)</span></span>
* <span data-ttu-id="16cae-150">Revendications de rôle d’administrateur AAD ( `directoryRole` )</span><span class="sxs-lookup"><span data-stu-id="16cae-150">AAD Administrator Role claims (`directoryRole`)</span></span>
* <span data-ttu-id="16cae-151">Exemple de revendication de données de profil utilisateur pour le numéro de téléphone mobile de l’utilisateur ( `mobilePhone` )</span><span class="sxs-lookup"><span data-stu-id="16cae-151">An example user profile data claim for the user's mobile phone number (`mobilePhone`)</span></span>
* <span data-ttu-id="16cae-152">Revendications de groupe AAD ( `directoryGroup` )</span><span class="sxs-lookup"><span data-stu-id="16cae-152">AAD Group claims (`directoryGroup`)</span></span>

<span data-ttu-id="16cae-153">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="16cae-153">`CustomAccountFactory.cs`:</span></span>

```csharp
using System;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using Microsoft.Graph;

public class CustomAccountFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    private readonly ILogger<CustomAccountFactory> logger;
    private readonly IServiceProvider serviceProvider;

    public CustomAccountFactory(IAccessTokenProviderAccessor accessor,
        IServiceProvider serviceProvider,
        ILogger<CustomAccountFactory> logger)
        : base(accessor)
    {
        this.serviceProvider = serviceProvider;
        this.logger = logger;
    }
    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            foreach (var role in account.Roles)
            {
                userIdentity.AddClaim(new Claim("appRole", role));
            }

            foreach (var wid in account.Wids)
            {
                userIdentity.AddClaim(new Claim("directoryRole", wid));
            }

            try
            {
                var graphClient = ActivatorUtilities
                    .CreateInstance<GraphServiceClient>(serviceProvider);

                var requestMe = graphClient.Me.Request();
                var user = await requestMe.GetAsync();

                if (user != null)
                {
                    userIdentity.AddClaim(new Claim("mobilePhone",
                        user.MobilePhone));
                }

                var requestMemberOf = graphClient.Users[account.Oid].MemberOf;
                var memberships = await requestMemberOf.Request().GetAsync();

                if (memberships != null)
                {
                    foreach (var entry in memberships)
                    {
                        if (entry.ODataType == "#microsoft.graph.group")
                        {
                            userIdentity.AddClaim(
                                new Claim("directoryGroup", entry.Id));
                        }
                    }
                }
            }
            catch (ServiceException exception)
            {
                logger.LogError("Graph API service failure: {Message}",
                    exception.Message);
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="16cae-154">Le code précédent n’inclut pas les appartenances transitives.</span><span class="sxs-lookup"><span data-stu-id="16cae-154">The preceding code doesn't include transitive memberships.</span></span> <span data-ttu-id="16cae-155">Si l’application requiert des revendications d’appartenance de groupe directe et transitive, remplacez la `MemberOf` propriété ( `IUserMemberOfCollectionWithReferencesRequestBuilder` ) par `TransitiveMemberOf` ( `IUserTransitiveMemberOfCollectionWithReferencesRequestBuilder` ).</span><span class="sxs-lookup"><span data-stu-id="16cae-155">If the app requires direct and transitive group membership claims, replace the `MemberOf` property (`IUserMemberOfCollectionWithReferencesRequestBuilder`) with `TransitiveMemberOf` (`IUserTransitiveMemberOfCollectionWithReferencesRequestBuilder`).</span></span>

<span data-ttu-id="16cae-156">Le code précédent ignore les revendications d’appartenance à un groupe ( `groups` ) qui sont des rôles d’administrateur AAD ( `#microsoft.graph.directoryRole` type), car les valeurs GUID retournées par la Identity plateforme Microsoft 2,0 sont des **ID d’entité** de rôle d’administrateur AAD et non des ID de modèle de [**rôle**](/azure/active-directory/roles/permissions-reference#role-template-ids).</span><span class="sxs-lookup"><span data-stu-id="16cae-156">The preceding code ignores group membership claims (`groups`) that are AAD Administrator Roles (`#microsoft.graph.directoryRole` type) because the GUID values returned by the Microsoft Identity Platform 2.0 are AAD Administrator Role **entity IDs** and not [**Role Template IDs**](/azure/active-directory/roles/permissions-reference#role-template-ids).</span></span> <span data-ttu-id="16cae-157">Les ID d’entité ne sont pas stables entre les locataires dans la Identity plate-forme Microsoft 2,0 et ne doivent pas être utilisés pour créer des stratégies d’autorisation pour les utilisateurs dans les applications.</span><span class="sxs-lookup"><span data-stu-id="16cae-157">Entity IDs aren't stable across tenants in Microsoft Identity Platform 2.0 and shouldn't be used to create authorization policies for users in apps.</span></span> <span data-ttu-id="16cae-158">Utilisez toujours les **ID de modèle de rôle** pour les rôles d’administrateur AAD **fournis par les `wids` revendications**.</span><span class="sxs-lookup"><span data-stu-id="16cae-158">Always use **Role Template IDs** for AAD Administrator Roles **provided by `wids` claims**.</span></span>

<span data-ttu-id="16cae-159">Dans `Program.Main` de l’application **cliente** , configurez l’authentification MSAL pour utiliser la fabrique de comptes d’utilisateurs personnalisée.</span><span class="sxs-lookup"><span data-stu-id="16cae-159">In `Program.Main` of the **CLIENT** app, configure the MSAL authentication to use the custom user account factory.</span></span>

<span data-ttu-id="16cae-160">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="16cae-160">`Program.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState,
    CustomUserAccount>(options =>
{
    ...
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount,
    CustomAccountFactory>();

...

builder.Services.AddGraphClient();
```

## <a name="authorization-configuration"></a><span data-ttu-id="16cae-161">Configuration de l’autorisation</span><span class="sxs-lookup"><span data-stu-id="16cae-161">Authorization configuration</span></span>

<span data-ttu-id="16cae-162">Dans l’application **cliente** , créez une [stratégie](xref:security/authorization/policies) pour chaque [rôle d’application](#app-roles), rôle d’administrateur AAD ou groupe de sécurité dans `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="16cae-162">In the **CLIENT** app, create a [policy](xref:security/authorization/policies) for each [App Role](#app-roles), AAD Administrator Role, or security group in `Program.Main`.</span></span> <span data-ttu-id="16cae-163">L’exemple suivant crée une stratégie pour le rôle d' *administrateur de facturation* AAD :</span><span class="sxs-lookup"><span data-stu-id="16cae-163">The following example creates a policy for the AAD *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("directoryRole", 
            "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

<span data-ttu-id="16cae-164">Pour obtenir la liste complète des ID des rôles d’administrateur AAD, consultez [ID de modèle de rôle](/azure/active-directory/roles/permissions-reference#role-template-ids) dans la documentation Azure.</span><span class="sxs-lookup"><span data-stu-id="16cae-164">For the complete list of IDs for AAD Administrator Roles, see [Role template IDs](/azure/active-directory/roles/permissions-reference#role-template-ids) in the Azure documentation.</span></span> <span data-ttu-id="16cae-165">Pour plus d’informations sur les stratégies d’autorisation, consultez <xref:security/authorization/policies> .</span><span class="sxs-lookup"><span data-stu-id="16cae-165">For more information on authorization policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="16cae-166">Dans les exemples suivants, l’application **cliente** utilise la stratégie précédente pour autoriser l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="16cae-166">In the following examples, the **CLIENT** app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="16cae-167">Le [ `AuthorizeView` composant](xref:blazor/security/index#authorizeview-component) fonctionne avec la stratégie :</span><span class="sxs-lookup"><span data-stu-id="16cae-167">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

```razor
<AuthorizeView Policy="BillingAdministrator">
    <Authorized>
        <p>
            The user is in the 'Billing Administrator' AAD Administrator Role
            and can see this content.
        </p>
    </Authorized>
    <NotAuthorized>
        <p>
            The user is NOT in the 'Billing Administrator' role and sees this
            content.
        </p>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="16cae-168">L’accès à un composant entier peut être basé sur la stratégie à l’aide d’une [ `[Authorize]` directive d’attribut](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) :</span><span class="sxs-lookup"><span data-stu-id="16cae-168">Access to an entire component can be based on the policy using an [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="16cae-169">Si l’utilisateur n’est pas connecté, il est redirigé vers la page de connexion AAD, puis renvoyé au composant une fois qu’il se connecte.</span><span class="sxs-lookup"><span data-stu-id="16cae-169">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="16cae-170">Une vérification de stratégie peut également être [effectuée dans le code avec la logique procédurale](xref:blazor/security/index#procedural-logic).</span><span class="sxs-lookup"><span data-stu-id="16cae-170">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic).</span></span>

<span data-ttu-id="16cae-171">`Pages/CheckPolicy.razor`:</span><span class="sxs-lookup"><span data-stu-id="16cae-171">`Pages/CheckPolicy.razor`:</span></span>

```razor
@page "/checkpolicy"
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<h1>Check Policy</h1>

<p>This component checks a policy in code.</p>

<button @onclick="CheckPolicy">Check 'BillingAdministrator' policy</button>

<p>Policy Message: @policyMessage</p>

@code {
    private string policyMessage = "Check hasn't been made yet.";

    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async void CheckPolicy()
    {
        var user = (await authenticationStateTask).User;

        if ((await AuthorizationService.AuthorizeAsync(user, 
            "BillingAdministrator")).Succeeded)
        {
            policyMessage = "Yes! The 'BillingAdministrator' policy is met.";
        }
        else
        {
            policyMessage = "No! 'BillingAdministrator' policy is NOT met.";
        }
    }
}
```

## <a name="authorize-server-apiweb-api-access"></a><span data-ttu-id="16cae-172">Autoriser l’accès à l’API serveur/API Web</span><span class="sxs-lookup"><span data-stu-id="16cae-172">Authorize server API/web API access</span></span>

<span data-ttu-id="16cae-173">Une application API **serveur** peut autoriser les utilisateurs à accéder à des points de terminaison d’API sécurisés avec des [stratégies d’autorisation](xref:security/authorization/policies) pour les groupes de sécurité, les rôles d’administrateur AAD et les rôles d’application lorsqu’un jeton d’accès contient des `groups` `wids` revendications, et `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` .</span><span class="sxs-lookup"><span data-stu-id="16cae-173">A **SERVER** API app can authorize users to access secure API endpoints with [authorization policies](xref:security/authorization/policies) for security groups, AAD Administrator Roles, and App Roles when an access token contains `groups`, `wids`, and `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` claims.</span></span> <span data-ttu-id="16cae-174">L’exemple suivant crée une stratégie pour le rôle d' *administrateur de facturation* AAD dans `Startup.ConfigureServices` à l’aide des `wids` revendications (ID de modèle de rôle/ID de rôle) :</span><span class="sxs-lookup"><span data-stu-id="16cae-174">The following example creates a policy for the AAD *Billing Administrator* role in `Startup.ConfigureServices` using the `wids` (well-known IDs/Role Template IDs) claims:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("wids", "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

<span data-ttu-id="16cae-175">Pour obtenir la liste complète des ID des rôles d’administrateur AAD, consultez [ID de modèle de rôle](/azure/active-directory/roles/permissions-reference#role-template-ids) dans la documentation Azure.</span><span class="sxs-lookup"><span data-stu-id="16cae-175">For the complete list of IDs for AAD Administrator Roles, see [Role template IDs](/azure/active-directory/roles/permissions-reference#role-template-ids) in the Azure documentation.</span></span> <span data-ttu-id="16cae-176">Pour plus d’informations sur les stratégies d’autorisation, consultez <xref:security/authorization/policies> .</span><span class="sxs-lookup"><span data-stu-id="16cae-176">For more information on authorization policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="16cae-177">L’accès à un contrôleur dans l’application **serveur** peut être basé sur l’utilisation d’un [ `[Authorize]` attribut](xref:security/authorization/simple) avec le nom de la stratégie (documentation de l’API : <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ).</span><span class="sxs-lookup"><span data-stu-id="16cae-177">Access to a controller in the **SERVER** app can be based on using an [`[Authorize]` attribute](xref:security/authorization/simple) with the name of the policy (API documentation: <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>).</span></span>

<span data-ttu-id="16cae-178">L’exemple suivant limite l’accès aux données de facturation des `BillingDataController` administrateurs de facturation Azure avec le nom de stratégie `BillingAdministrator` :</span><span class="sxs-lookup"><span data-stu-id="16cae-178">The following example limits access to billing data from the `BillingDataController` to Azure Billing Administrators with a policy name of `BillingAdministrator`:</span></span>

```csharp
...
using Microsoft.AspNetCore.Authorization;

[Authorize(Policy = "BillingAdministrator")]
[ApiController]
[Route("[controller]")]
public class BillingDataController : ControllerBase
{
    ...
}
```

<span data-ttu-id="16cae-179">Pour plus d’informations, consultez <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="16cae-179">For more information, see <xref:security/authorization/policies>.</span></span>

## <a name="app-roles"></a><span data-ttu-id="16cae-180">Rôles d’application</span><span class="sxs-lookup"><span data-stu-id="16cae-180">App Roles</span></span>

<span data-ttu-id="16cae-181">Pour configurer l’application dans le Portail Azure pour fournir des revendications d’appartenance à des rôles d’application, consultez [Comment : ajouter des rôles d’application dans votre application et les recevoir dans le jeton](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) dans la documentation Azure.</span><span class="sxs-lookup"><span data-stu-id="16cae-181">To configure the app in the Azure portal to provide App Roles membership claims, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="16cae-182">L’exemple suivant suppose que les applications **client** et **serveur** sont configurées avec deux rôles, et que les rôles sont attribués à un utilisateur de test :</span><span class="sxs-lookup"><span data-stu-id="16cae-182">The following example assumes that the **CLIENT** and **SERVER** apps are configured with two roles, and the roles are assigned to a test user:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="16cae-183">Lors du développement d’une application hébergée Blazor WebAssembly ou d’une paire client-serveur d’applications autonomes (une Blazor WebAssembly application autonome et une application API Web/API ASP.net Core Server autonome), la `appRoles` propriété de manifeste du client et du serveur portail Azure inscriptions d’applications doit inclure les mêmes rôles configurés.</span><span class="sxs-lookup"><span data-stu-id="16cae-183">When developing a hosted Blazor WebAssembly app or a client-server pair of standalone apps (a standalone Blazor WebAssembly app and a standalone ASP.NET Core server API/web API app), the `appRoles` manifest property of both the client and the server Azure portal app registrations must include the same configured roles.</span></span> <span data-ttu-id="16cae-184">Après avoir établi les rôles dans le manifeste de l’application cliente, copiez-les dans leur intégralité dans le manifeste de l’application serveur.</span><span class="sxs-lookup"><span data-stu-id="16cae-184">After establishing the roles in the client app's manifest, copy them in their entirety to the server app's manifest.</span></span> <span data-ttu-id="16cae-185">Si vous ne mettez pas en miroir le manifeste `appRoles` entre les inscriptions d’applications clientes et serveur, les revendications de rôle ne sont pas établies pour les utilisateurs authentifiés de l’API serveur/API Web, même si leur jeton d’accès a les revendications de rôles appropriées.</span><span class="sxs-lookup"><span data-stu-id="16cae-185">If you don't mirror the manifest `appRoles` between the client and server app registrations, role claims aren't established for authenticated users of the server API/web API, even if their access token has the correct roles claims.</span></span>

> [!NOTE]
> <span data-ttu-id="16cae-186">Bien que vous ne puissiez pas attribuer des rôles à des groupes sans compte Azure AD Premium, vous pouvez attribuer des rôles aux utilisateurs et recevoir une revendication de rôle pour les utilisateurs disposant d’un compte Azure standard.</span><span class="sxs-lookup"><span data-stu-id="16cae-186">Although you can't assign roles to groups without an Azure AD Premium account, you can assign roles to users and receive a roles claim for users with a standard Azure account.</span></span> <span data-ttu-id="16cae-187">Les instructions de cette section ne nécessitent pas de compte AAD Premium.</span><span class="sxs-lookup"><span data-stu-id="16cae-187">The guidance in this section doesn't require an AAD Premium account.</span></span>
>
> <span data-ttu-id="16cae-188">Plusieurs rôles sont affectés dans le Portail Azure en **_rajoutant un utilisateur_** pour chaque attribution de rôle supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="16cae-188">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="16cae-189">Le `CustomAccountFactory` affiché dans la section [compte d’utilisateur personnalisé](#custom-user-account) est configuré pour agir sur une `roles` revendication avec une valeur de tableau JSON.</span><span class="sxs-lookup"><span data-stu-id="16cae-189">The `CustomAccountFactory` shown in the [Custom user account](#custom-user-account) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="16cae-190">Ajoutez et inscrivez `CustomAccountFactory` dans l’application **cliente** , comme indiqué dans la section [compte d’utilisateur personnalisé](#custom-user-account) .</span><span class="sxs-lookup"><span data-stu-id="16cae-190">Add and register the `CustomAccountFactory` in the **CLIENT** app as shown in the [Custom user account](#custom-user-account) section.</span></span> <span data-ttu-id="16cae-191">Il n’est pas nécessaire de fournir du code pour supprimer la revendication d’origine `roles` , car elle est automatiquement supprimée par l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="16cae-191">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="16cae-192">Dans `Program.Main` une application **cliente** , spécifiez la revendication nommée « `appRole` » comme revendication de rôle pour les <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> Vérifications :</span><span class="sxs-lookup"><span data-stu-id="16cae-192">In `Program.Main` of a **CLIENT** app, specify the claim named "`appRole`" as the role claim for <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> checks:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "appRole";
});
```

> [!NOTE]
> <span data-ttu-id="16cae-193">Si vous préférez utiliser la `directoryRoles` revendication (ajouter des rôles d’administrateur), assignez « `directoryRoles` » à <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationUserOptions.RoleClaim?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="16cae-193">If you prefer to use the `directoryRoles` claim (ADD Administrator Roles), assign "`directoryRoles`" to the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationUserOptions.RoleClaim?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="16cae-194">Dans `Startup.ConfigureServices` une application **serveur** , spécifiez la revendication nommée « `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` » comme revendication de rôle pour les <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> Vérifications :</span><span class="sxs-lookup"><span data-stu-id="16cae-194">In `Startup.ConfigureServices` of a **SERVER** app, specify the claim named "`http://schemas.microsoft.com/ws/2008/06/identity/claims/role`" as the role claim for <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> checks:</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(options =>
    {
        Configuration.Bind("AzureAd", options);
        options.TokenValidationParameters.RoleClaimType = 
            "http://schemas.microsoft.com/ws/2008/06/identity/claims/role";
    },
    options => { Configuration.Bind("AzureAd", options); });
```

> [!NOTE]
> <span data-ttu-id="16cae-195">Si vous préférez utiliser la `wids` revendication (ajouter des rôles d’administrateur), assignez « `wids` » à <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.RoleClaimType?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="16cae-195">If you prefer to use the `wids` claim (ADD Administrator Roles), assign "`wids`" to the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.RoleClaimType?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="16cae-196">Les approches d’autorisation des composants sont fonctionnelles à ce stade.</span><span class="sxs-lookup"><span data-stu-id="16cae-196">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="16cae-197">L’un des mécanismes d’autorisation des composants de l’application **cliente** peut utiliser le `admin` rôle pour autoriser l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="16cae-197">Any of the authorization mechanisms in components of the **CLIENT** app can use the `admin` role to authorize the user:</span></span>

* [<span data-ttu-id="16cae-198">`AuthorizeView` -</span><span class="sxs-lookup"><span data-stu-id="16cae-198">`AuthorizeView` component</span></span>](xref:blazor/security/index#authorizeview-component)

  ```razor
  <AuthorizeView Roles="admin">
  ```

* <span data-ttu-id="16cae-199">[ `[Authorize]` directive d’attribut](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> )</span><span class="sxs-lookup"><span data-stu-id="16cae-199">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)</span></span>

  ```razor
  @attribute [Authorize(Roles = "admin")]
  ```

* [<span data-ttu-id="16cae-200">Logique procédurale</span><span class="sxs-lookup"><span data-stu-id="16cae-200">Procedural logic</span></span>](xref:blazor/security/index#procedural-logic)

  ```csharp
  var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
  var user = authState.User;

  if (user.IsInRole("admin")) { ... }
  ```

<span data-ttu-id="16cae-201">Plusieurs tests de rôle sont pris en charge :</span><span class="sxs-lookup"><span data-stu-id="16cae-201">Multiple role tests are supported:</span></span>

* <span data-ttu-id="16cae-202">Exigez **que l’utilisateur soit dans** le `admin` **rôle ou** `developer` avec le `AuthorizeView` composant :</span><span class="sxs-lookup"><span data-stu-id="16cae-202">Require that the user be in **either** the `admin` **or** `developer` role with the `AuthorizeView` component:</span></span>

  ```razor
  <AuthorizeView Roles="admin, developer">
      ...
  </AuthorizeView>
  ```

* <span data-ttu-id="16cae-203">Exiger que l’utilisateur se trouve à la **fois** dans les `admin`  `developer` rôles et avec le `AuthorizeView` composant :</span><span class="sxs-lookup"><span data-stu-id="16cae-203">Require that the user be in **both** the `admin` **and** `developer` roles with the `AuthorizeView` component:</span></span>

  ```razor
  <AuthorizeView Roles="admin">
      <AuthorizeView Roles="developer">
          ...
      </AuthorizeView>
  </AuthorizeView>
  ```

* <span data-ttu-id="16cae-204">Exigez **que l’utilisateur soit dans** le `admin` **rôle ou** `developer` avec l' `[Authorize]` attribut :</span><span class="sxs-lookup"><span data-stu-id="16cae-204">Require that the user be in **either** the `admin` **or** `developer` role with the `[Authorize]` attribute:</span></span>

  ```razor
  @attribute [Authorize(Roles = "admin, developer")]
  ```

* <span data-ttu-id="16cae-205">Exiger que l’utilisateur soit à la **fois** dans les `admin`  `developer` rôles et avec l' `[Authorize]` attribut :</span><span class="sxs-lookup"><span data-stu-id="16cae-205">Require that the user be in **both** the `admin` **and** `developer` roles with the `[Authorize]` attribute:</span></span>

  ```razor
  @attribute [Authorize(Roles = "admin")]
  @attribute [Authorize(Roles = "developer")]
  ```

* <span data-ttu-id="16cae-206">Exiger **que l’utilisateur soit dans le** rôle `admin` **ou** `developer` avec le code procédural :</span><span class="sxs-lookup"><span data-stu-id="16cae-206">Require that the user be in **either** the `admin` **or** `developer` role with procedural code:</span></span>

  ```razor
  @code {
      private async Task DoSomething()
      {
          var authState = await AuthenticationStateProvider
              .GetAuthenticationStateAsync();
          var user = authState.User;

          if (user.IsInRole("admin") || user.IsInRole("developer"))
          {
              ...
          }
          else
          {
              ...
          }
      }
  }
  ```

* <span data-ttu-id="16cae-207">Exigez que l’utilisateur se trouve à la **fois** dans les `admin` rôles **et** `developer` avec le code procédural en remplaçant le [ou conditionnel ( `||` )](/dotnet/csharp/language-reference/operators/boolean-logical-operators) par un [conditionnel and ( `&&` )](/dotnet/csharp/language-reference/operators/boolean-logical-operators) dans l’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="16cae-207">Require that the user be in **both** the `admin` **and** `developer` roles with procedural code by changing the [conditional OR (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) to a [conditional AND (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) in the preceding example:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  ```

<span data-ttu-id="16cae-208">L’un des mécanismes d’autorisation des contrôleurs de l’application **serveur** peut utiliser le `admin` rôle pour autoriser l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="16cae-208">Any of the authorization mechanisms in controllers of the **SERVER** app can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="16cae-209">[ `[Authorize]` directive d’attribut](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> )</span><span class="sxs-lookup"><span data-stu-id="16cae-209">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)</span></span>

  ```csharp
  [Authorize(Roles = "admin")]
  ```

* [<span data-ttu-id="16cae-210">Logique procédurale</span><span class="sxs-lookup"><span data-stu-id="16cae-210">Procedural logic</span></span>](xref:blazor/security/index#procedural-logic)

  ```csharp
  if (User.IsInRole("admin")) { ... }
  ```

<span data-ttu-id="16cae-211">Plusieurs tests de rôle sont pris en charge :</span><span class="sxs-lookup"><span data-stu-id="16cae-211">Multiple role tests are supported:</span></span>

* <span data-ttu-id="16cae-212">Exigez **que l’utilisateur soit dans** le `admin` **rôle ou** `developer` avec l' `[Authorize]` attribut :</span><span class="sxs-lookup"><span data-stu-id="16cae-212">Require that the user be in **either** the `admin` **or** `developer` role with the `[Authorize]` attribute:</span></span>

  ```csharp
  [Authorize(Roles = "admin, developer")]
  ```

* <span data-ttu-id="16cae-213">Exiger que l’utilisateur soit à la **fois** dans les `admin`  `developer` rôles et avec l' `[Authorize]` attribut :</span><span class="sxs-lookup"><span data-stu-id="16cae-213">Require that the user be in **both** the `admin` **and** `developer` roles with the `[Authorize]` attribute:</span></span>

  ```csharp
  [Authorize(Roles = "admin")]
  [Authorize(Roles = "developer")]
  ```

* <span data-ttu-id="16cae-214">Exiger **que l’utilisateur soit dans le** rôle `admin` **ou** `developer` avec le code procédural :</span><span class="sxs-lookup"><span data-stu-id="16cae-214">Require that the user be in **either** the `admin` **or** `developer` role with procedural code:</span></span>

  ```csharp
  static readonly string[] scopeRequiredByApi = new string[] { "API.Access" };

  ...

  [HttpGet]
  public IEnumerable<ReturnType> Get()
  {
      HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

      if (User.IsInRole("admin") || User.IsInRole("developer"))
      {
          ...
      }
      else
      {
          ...
      }

      return ...
  }
  ```

* <span data-ttu-id="16cae-215">Exigez que l’utilisateur se trouve à la **fois** dans les `admin` rôles **et** `developer` avec le code procédural en remplaçant le [ou conditionnel ( `||` )](/dotnet/csharp/language-reference/operators/boolean-logical-operators) par un [conditionnel and ( `&&` )](/dotnet/csharp/language-reference/operators/boolean-logical-operators) dans l’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="16cae-215">Require that the user be in **both** the `admin` **and** `developer` roles with procedural code by changing the [conditional OR (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) to a [conditional AND (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) in the preceding example:</span></span>

  ```csharp
  if (User.IsInRole("admin") && User.IsInRole("developer"))
  ```

## <a name="additional-resources"></a><span data-ttu-id="16cae-216">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="16cae-216">Additional resources</span></span>

* [<span data-ttu-id="16cae-217">ID de modèle de rôle (documentation Azure)</span><span class="sxs-lookup"><span data-stu-id="16cae-217">Role template IDs (Azure documentation)</span></span>](/azure/active-directory/roles/permissions-reference#role-template-ids)
* [<span data-ttu-id="16cae-218">`groupMembershipClaims` attribute (documentation Azure)</span><span class="sxs-lookup"><span data-stu-id="16cae-218">`groupMembershipClaims` attribute (Azure documentation)</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)
* [<span data-ttu-id="16cae-219">Comment : ajouter des rôles d’application dans votre application et les recevoir dans le jeton (documentation Azure)</span><span class="sxs-lookup"><span data-stu-id="16cae-219">How to: Add app roles in your application and receive them in the token (Azure documentation)</span></span>](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)
* [<span data-ttu-id="16cae-220">Rôles d’application (documentation Azure)</span><span class="sxs-lookup"><span data-stu-id="16cae-220">Application roles (Azure documentation)</span></span>](/azure/architecture/multitenant-identity/app-roles)
* <xref:security/authorization/claims>
* <xref:security/authorization/roles>
* <xref:blazor/security/index>
