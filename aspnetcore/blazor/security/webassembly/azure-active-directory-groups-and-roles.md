---
title: ASP.NET Core Blazor WebAssembly avec des groupes et des rôles Azure Active Directory
author: guardrex
description: Découvrez comment configurer Blazor WebAssembly pour utiliser des groupes et des rôles Azure Active Directory.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 07/28/2020
no-loc:
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
ms.openlocfilehash: ac666a4c7493140d4ae93047e18202c3d8314c7b
ms.sourcegitcommit: daa9ccf580df531254da9dce8593441ac963c674
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91900698"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-user-defined-roles"></a><span data-ttu-id="93894-103">Groupes Azure Active Directory (AAD), rôles d’administrateur et rôles définis par l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="93894-103">Azure Active Directory (AAD) groups, Administrator Roles, and user-defined roles</span></span>

<span data-ttu-id="93894-104">Par [Luke Latham](https://github.com/guardrex) et [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="93894-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="93894-105">Azure Active Directory (AAD) fournit plusieurs approches d’autorisation qui peuvent être combinées avec ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="93894-105">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="93894-106">Groupes définis par l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="93894-106">User-defined groups</span></span>
  * <span data-ttu-id="93894-107">Sécurité</span><span class="sxs-lookup"><span data-stu-id="93894-107">Security</span></span>
  * <span data-ttu-id="93894-108">Microsoft 365</span><span class="sxs-lookup"><span data-stu-id="93894-108">Microsoft 365</span></span>
  * <span data-ttu-id="93894-109">Distribution</span><span class="sxs-lookup"><span data-stu-id="93894-109">Distribution</span></span>
* <span data-ttu-id="93894-110">Rôles</span><span class="sxs-lookup"><span data-stu-id="93894-110">Roles</span></span>
  * <span data-ttu-id="93894-111">Rôles d’administrateur AAD</span><span class="sxs-lookup"><span data-stu-id="93894-111">AAD Administrator Roles</span></span>
  * <span data-ttu-id="93894-112">Rôles définis par l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="93894-112">User-defined roles</span></span>

<span data-ttu-id="93894-113">Les instructions de cet article s’appliquent aux Blazor WebAssembly scénarios de déploiement AAD décrits dans les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="93894-113">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="93894-114">Autonome avec des comptes Microsoft</span><span class="sxs-lookup"><span data-stu-id="93894-114">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="93894-115">Autonome avec AAD</span><span class="sxs-lookup"><span data-stu-id="93894-115">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="93894-116">Hébergé avec AAD</span><span class="sxs-lookup"><span data-stu-id="93894-116">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="microsoft-graph-api-permission"></a><span data-ttu-id="93894-117">Microsoft Graph l’autorisation de l’API</span><span class="sxs-lookup"><span data-stu-id="93894-117">Microsoft Graph API permission</span></span>

<span data-ttu-id="93894-118">Un appel d' [API Microsoft Graph](/graph/use-the-api) est requis pour tout utilisateur d’application disposant de plus de cinq appartenances de groupe de sécurité et de rôle d’administrateur AAD.</span><span class="sxs-lookup"><span data-stu-id="93894-118">A [Microsoft Graph API](/graph/use-the-api) call is required for any app user with more than five AAD Administrator role and security group memberships.</span></span>

<span data-ttu-id="93894-119">Pour autoriser les appels API Graph, attribuez à l’application autonome ou à *`Client`* une solution hébergée l' Blazor une des [autorisations API Graph](/graph/permissions-reference) suivantes dans la portail Azure :</span><span class="sxs-lookup"><span data-stu-id="93894-119">To permit Graph API calls, give the standalone or *`Client`* app of a hosted Blazor solution any of the following [Graph API permissions](/graph/permissions-reference) in the Azure portal:</span></span>

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

<span data-ttu-id="93894-120">`Directory.Read.All` est l’autorisation à privilèges minimaux et est l’autorisation utilisée pour l’exemple décrit dans cet article.</span><span class="sxs-lookup"><span data-stu-id="93894-120">`Directory.Read.All` is the least-privileged permission and is the permission used for the example described in this article.</span></span>

## <a name="user-defined-groups-and-administrator-roles"></a><span data-ttu-id="93894-121">Groupes définis par l’utilisateur et rôles d’administrateur</span><span class="sxs-lookup"><span data-stu-id="93894-121">User-defined groups and Administrator Roles</span></span>

<span data-ttu-id="93894-122">Pour configurer l’application dans le Portail Azure pour fournir une `groups` revendication d’appartenance, consultez les articles Azure suivants.</span><span class="sxs-lookup"><span data-stu-id="93894-122">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="93894-123">Affecter des utilisateurs à des groupes AAD définis par l’utilisateur et des rôles d’administrateur AAD.</span><span class="sxs-lookup"><span data-stu-id="93894-123">Assign users to user-defined AAD groups and AAD Administrator Roles.</span></span>

* [<span data-ttu-id="93894-124">Rôles utilisant les groupes de sécurité Azure AD</span><span class="sxs-lookup"><span data-stu-id="93894-124">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="93894-125">`groupMembershipClaims` attribut</span><span class="sxs-lookup"><span data-stu-id="93894-125">`groupMembershipClaims` attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="93894-126">Les exemples suivants partent du principe qu’un utilisateur est affecté au rôle d' *administrateur de facturation* AAD.</span><span class="sxs-lookup"><span data-stu-id="93894-126">The following examples assume that a user is assigned to the AAD *Billing Administrator* role.</span></span>

<span data-ttu-id="93894-127">La `groups` revendication unique envoyée par AAD présente les groupes et les rôles de l’utilisateur en tant qu’ID d’objet (Guid) dans un tableau JSON.</span><span class="sxs-lookup"><span data-stu-id="93894-127">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="93894-128">L’application doit convertir le tableau JSON de groupes et de rôles en `group` revendications individuelles pour lesquelles l’application peut créer des [stratégies](xref:security/authorization/policies) .</span><span class="sxs-lookup"><span data-stu-id="93894-128">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="93894-129">Lorsque le nombre de rôles d’administrateur AAD attribués et de groupes définis par l’utilisateur est supérieur à cinq, AAD envoie une `hasgroups` revendication avec une `true` valeur au lieu d’envoyer une `groups` revendication.</span><span class="sxs-lookup"><span data-stu-id="93894-129">When the number of assigned AAD Administrator Roles and user-defined groups exceeds five, AAD sends a `hasgroups` claim with a `true` value instead of sending a `groups` claim.</span></span> <span data-ttu-id="93894-130">Toute application pouvant comporter plus de cinq rôles et groupes affectés à ses utilisateurs doit effectuer un appel de API Graph séparé pour obtenir les rôles et les groupes d’un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="93894-130">Any app that may have more than five roles and groups assigned to its users must make a separate Graph API call to obtain a user's roles and groups.</span></span> <span data-ttu-id="93894-131">L’exemple d’implémentation fourni dans cet article s’adresse à ce scénario.</span><span class="sxs-lookup"><span data-stu-id="93894-131">The example implementation provided in this article addresses this scenario.</span></span> <span data-ttu-id="93894-132">Pour plus d’informations, consultez `groups` l' `hasgroups` article et les informations sur les revendications dans les jetons d’accès de la [plateforme Microsoft Identity : revendications de charge utile](/azure/active-directory/develop/access-tokens#payload-claims) .</span><span class="sxs-lookup"><span data-stu-id="93894-132">For more information, see the `groups` and `hasgroups` claims information in [Microsoft identity platform access tokens: Payload claims](/azure/active-directory/develop/access-tokens#payload-claims) article.</span></span>

<span data-ttu-id="93894-133">Étendez <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> pour inclure les propriétés de tableau pour les groupes et les rôles.</span><span class="sxs-lookup"><span data-stu-id="93894-133">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span> <span data-ttu-id="93894-134">Assignez un tableau vide à chaque propriété afin qu' `null` il ne soit pas nécessaire de vérifier si ces propriétés sont utilisées ultérieurement dans les `foreach` boucles.</span><span class="sxs-lookup"><span data-stu-id="93894-134">Assign an empty array to each property so that checking for `null` isn't required when these properties are used in `foreach` loops later.</span></span>

<span data-ttu-id="93894-135">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="93894-135">`CustomUserAccount.cs`:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("groups")]
    public string[] Groups { get; set; } = new string[] { };

    [JsonPropertyName("roles")]
    public string[] Roles { get; set; } = new string[] { };
}
```

<span data-ttu-id="93894-136">Dans l’application autonome ou l' *`Client`* application d’une solution hébergée Blazor , créez une <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> classe personnalisée.</span><span class="sxs-lookup"><span data-stu-id="93894-136">In the standalone app or the *`Client`* app of a hosted Blazor solution, create a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class.</span></span> <span data-ttu-id="93894-137">Utilisez l’étendue (autorisation) appropriée pour les appels API Graph qui obtiennent des informations sur les rôles et les groupes.</span><span class="sxs-lookup"><span data-stu-id="93894-137">Use the correct scope (permission) for Graph API calls that obtain role and group information.</span></span>

<span data-ttu-id="93894-138">`GraphAPIAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="93894-138">`GraphAPIAuthorizationMessageHandler.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class GraphAPIAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public GraphAPIAuthorizationMessageHandler(IAccessTokenProvider provider,
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://graph.microsoft.com" },
            scopes: new[] { "https://graph.microsoft.com/Directory.Read.All" });
    }
}
```

<span data-ttu-id="93894-139">Dans `Program.Main` ( `Program.cs` ), ajoutez le <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> service d’implémentation et ajoutez un nommé <xref:System.Net.Http.HttpClient> pour effectuer des demandes de API Graph.</span><span class="sxs-lookup"><span data-stu-id="93894-139">In `Program.Main` (`Program.cs`), add the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> implementation service and add a named <xref:System.Net.Http.HttpClient> for making Graph API requests.</span></span> <span data-ttu-id="93894-140">L’exemple suivant nomme le client `GraphAPI` :</span><span class="sxs-lookup"><span data-stu-id="93894-140">The following example names the client `GraphAPI`:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

<span data-ttu-id="93894-141">Créez des classes d’objets d’annuaire AAD pour recevoir les groupes et rôles Open Data Protocol (OData) d’un appel de API Graph.</span><span class="sxs-lookup"><span data-stu-id="93894-141">Create AAD directory objects classes to receive the Open Data Protocol (OData) roles and groups from a Graph API call.</span></span> <span data-ttu-id="93894-142">OData arrive au format JSON, et un appel à <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> remplit une instance de la `DirectoryObjects` classe.</span><span class="sxs-lookup"><span data-stu-id="93894-142">The OData arrives in JSON format, and a call to <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> populates an instance of the `DirectoryObjects` class.</span></span>

<span data-ttu-id="93894-143">`DirectoryObjects.cs`:</span><span class="sxs-lookup"><span data-stu-id="93894-143">`DirectoryObjects.cs`:</span></span>

```csharp
using System.Collections.Generic;
using System.Text.Json.Serialization;

public class DirectoryObjects
{
    [JsonPropertyName("@odata.context")]
    public string Context { get; set; }

    [JsonPropertyName("value")]
    public List<Value> Values { get; set; }
}

public class Value
{
    [JsonPropertyName("@odata.type")]
    public string Type { get; set; }

    [JsonPropertyName("id")]
    public string Id { get; set; }
}
```

<span data-ttu-id="93894-144">Créez une fabrique d’utilisateurs personnalisée pour traiter les revendications de rôles et de groupes.</span><span class="sxs-lookup"><span data-stu-id="93894-144">Create a custom user factory to process roles and groups claims.</span></span> <span data-ttu-id="93894-145">L’exemple d’implémentation suivant gère également le `roles` tableau de revendications, qui est abordé dans la section [rôles définis par l’utilisateur](#user-defined-roles) .</span><span class="sxs-lookup"><span data-stu-id="93894-145">The following example implementation also handles the `roles` claim array, which is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="93894-146">Si la `hasgroups` revendication est présente, le nommé <xref:System.Net.Http.HttpClient> est utilisé pour effectuer une demande autorisée à API Graph pour obtenir les rôles et les groupes de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="93894-146">If the `hasgroups` claim is present, the named <xref:System.Net.Http.HttpClient> is used to make an authorized request to Graph API to obtain the user's roles and groups.</span></span> <span data-ttu-id="93894-147">Cette implémentation utilise le Identity point de terminaison Microsoft Platform v 1.0 `https://graph.microsoft.com/v1.0/me/memberOf` (documentation de l'[API](/graph/api/user-list-memberof)).</span><span class="sxs-lookup"><span data-stu-id="93894-147">This implementation uses the Microsoft Identity Platform v1.0 endpoint `https://graph.microsoft.com/v1.0/me/memberOf` ([API documentation](/graph/api/user-list-memberof)).</span></span> <span data-ttu-id="93894-148">Les instructions de cette rubrique seront mises à jour pour Identity v 2.0 lorsque les packages MSAL seront mis à niveau pour v 2.0.</span><span class="sxs-lookup"><span data-stu-id="93894-148">The guidance in this topic will be updated for Identity v2.0 when the MSAL packages are upgraded for v2.0.</span></span>

<span data-ttu-id="93894-149">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="93894-149">`CustomAccountFactory.cs`:</span></span>

```csharp
using System.Linq;
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.Logging;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    private readonly ILogger<CustomUserFactory> logger;
    private readonly IHttpClientFactory clientFactory;

    public CustomUserFactory(IAccessTokenProviderAccessor accessor, 
        IHttpClientFactory clientFactory, 
        ILogger<CustomUserFactory> logger)
        : base(accessor)
    {
        this.clientFactory = clientFactory;
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
                userIdentity.AddClaim(new Claim("role", role));
            }

            if (userIdentity.HasClaim(c => c.Type == "hasgroups"))
            {
                try
                {
                    var client = clientFactory.CreateClient("GraphAPI");

                    var response = await client.GetAsync("v1.0/me/memberOf");

                    if (response.IsSuccessStatusCode)
                    {
                        var userObjects = await response.Content
                            .ReadFromJsonAsync<DirectoryObjects>();

                        foreach (var obj in userObjects?.Values)
                        {
                            userIdentity.AddClaim(new Claim("group", obj.Id));
                        }

                        var claim = userIdentity.Claims.FirstOrDefault(
                            c => c.Type == "hasgroups");

                        userIdentity.RemoveClaim(claim);
                    }
                    else
                    {
                        logger.LogError("Graph API request failure: {REASON}", 
                            response.ReasonPhrase);
                    }
                }
                catch (AccessTokenNotAvailableException exception)
                {
                    logger.LogError("Graph API access token failure: {MESSAGE}", 
                        exception.Message);
                }
            }
            else
            {
                foreach (var group in account.Groups)
                {
                    userIdentity.AddClaim(new Claim("group", group));
                }
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="93894-150">Il n’est pas nécessaire de fournir du code pour supprimer la revendication d’origine `groups` , le cas échéant, car elle est automatiquement supprimée par l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="93894-150">There's no need to provide code to remove the original `groups` claim, if present, because it's automatically removed by the framework.</span></span>

> [!NOTE]
> <span data-ttu-id="93894-151">L’approche dans cet exemple :</span><span class="sxs-lookup"><span data-stu-id="93894-151">The approach in this example:</span></span>
>
> * <span data-ttu-id="93894-152">Ajoute une <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> classe personnalisée pour joindre des jetons d’accès aux demandes sortantes.</span><span class="sxs-lookup"><span data-stu-id="93894-152">Adds a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class to attach access tokens to outgoing requests.</span></span>
> * <span data-ttu-id="93894-153">Ajoute un nommé <xref:System.Net.Http.HttpClient> pour effectuer des demandes d’API Web à un point de terminaison d’API Web sécurisé et externe.</span><span class="sxs-lookup"><span data-stu-id="93894-153">Adds a named <xref:System.Net.Http.HttpClient> for making web API requests to a secure, external web API endpoint.</span></span>
> * <span data-ttu-id="93894-154">Utilise le nommé <xref:System.Net.Http.HttpClient> pour effectuer des demandes autorisées.</span><span class="sxs-lookup"><span data-stu-id="93894-154">Uses the named <xref:System.Net.Http.HttpClient> to make authorized requests.</span></span>
>
> <span data-ttu-id="93894-155">La couverture générale de cette approche est disponible dans l' <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> article.</span><span class="sxs-lookup"><span data-stu-id="93894-155">General coverage for this approach is found in the <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> article.</span></span>

<span data-ttu-id="93894-156">Inscrire la fabrique dans `Program.Main` ( `Program.cs` ) de l’application autonome ou *`Client`* de l’application d’une solution hébergée Blazor .</span><span class="sxs-lookup"><span data-stu-id="93894-156">Register the factory in `Program.Main` (`Program.cs`) of the standalone app or *`Client`* app of a hosted Blazor solution.</span></span> <span data-ttu-id="93894-157">Consentement de l' `Directory.Read.All` étendue d’autorisation en tant qu’étendue supplémentaire pour l’application :</span><span class="sxs-lookup"><span data-stu-id="93894-157">Consent to the `Directory.Read.All` permission scope as an additional scope for the app:</span></span>

```csharp
builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    CustomUserAccount>(options =>
{
    builder.Configuration.Bind("AzureAd", 
        options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("...");

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Directory.Read.All");
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount, 
    CustomUserFactory>();
```

<span data-ttu-id="93894-158">Créez une [stratégie](xref:security/authorization/policies) pour chaque groupe ou rôle dans `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="93894-158">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="93894-159">L’exemple suivant crée une stratégie pour le rôle d' *administrateur de facturation* AAD :</span><span class="sxs-lookup"><span data-stu-id="93894-159">The following example creates a policy for the AAD *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="93894-160">Pour obtenir la liste complète des ID d’objet de rôle AAD, consultez la section [ID d’objet de rôle d’administrateur AAD](#aad-administrator-role-object-ids) .</span><span class="sxs-lookup"><span data-stu-id="93894-160">For the complete list of AAD role Object IDs, see the [AAD Administrator Role Object IDs](#aad-administrator-role-object-ids) section.</span></span>

<span data-ttu-id="93894-161">Dans les exemples suivants, l’application utilise la stratégie précédente pour autoriser l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="93894-161">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="93894-162">Le [ `AuthorizeView` composant](xref:blazor/security/index#authorizeview-component) fonctionne avec la stratégie :</span><span class="sxs-lookup"><span data-stu-id="93894-162">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

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

<span data-ttu-id="93894-163">L’accès à un composant entier peut être basé sur la stratégie à l’aide de la [ `[Authorize]` directive d’attribut](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) :</span><span class="sxs-lookup"><span data-stu-id="93894-163">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="93894-164">Si l’utilisateur n’est pas connecté, il est redirigé vers la page de connexion AAD, puis renvoyé au composant une fois qu’il se connecte.</span><span class="sxs-lookup"><span data-stu-id="93894-164">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="93894-165">Une vérification de stratégie peut également être [effectuée dans le code avec la logique procédurale](xref:blazor/security/index#procedural-logic):</span><span class="sxs-lookup"><span data-stu-id="93894-165">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic):</span></span>

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

## <a name="authorize-server-api-access-for-user-defined-groups-and-administrator-roles"></a><span data-ttu-id="93894-166">Autoriser l’accès à l’API serveur pour les groupes définis par l’utilisateur et les rôles d’administrateur</span><span class="sxs-lookup"><span data-stu-id="93894-166">Authorize server API access for user-defined groups and Administrator Roles</span></span>

<span data-ttu-id="93894-167">Outre l’autorisation des utilisateurs dans l’application webassembly côté client pour accéder aux pages et aux ressources, l’API serveur peut autoriser les utilisateurs à accéder aux points de terminaison d’API sécurisés.</span><span class="sxs-lookup"><span data-stu-id="93894-167">In addition to authorizing users in the client-side WebAssembly app to access pages and resources, the server API can authorize users for access to secure API endpoints.</span></span> <span data-ttu-id="93894-168">Une fois que l’application *serveur* a validé le jeton d’accès de l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="93894-168">After the *Server* app validates the user's access token:</span></span>

* <span data-ttu-id="93894-169">L’application utilise la revendication d’identificateur d' [objet immuable ( `oid` )](/azure/active-directory/develop/id-tokens#payload-claims) de l’utilisateur à partir du JWT ( `id_token` ) pour obtenir un jeton d’accès pour API Graph.</span><span class="sxs-lookup"><span data-stu-id="93894-169">The app uses the user's immutable [object identifier claim (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) from the JWT (`id_token`) to obtain an access token for Graph API.</span></span>
* <span data-ttu-id="93894-170">Un appel de API Graph obtient le groupe de sécurité défini par l’utilisateur Azure et les appartenances au rôle d’administrateur.</span><span class="sxs-lookup"><span data-stu-id="93894-170">A Graph API call obtains the user's Azure user-defined security group and Administrator Role memberships.</span></span>
* <span data-ttu-id="93894-171">Les appartenances sont utilisées pour établir des `group` revendications.</span><span class="sxs-lookup"><span data-stu-id="93894-171">Memberships are used to establish `group` claims.</span></span>
* <span data-ttu-id="93894-172">Les [stratégies d’autorisation](xref:security/authorization/policies) peuvent être utilisées pour limiter l’accès utilisateur aux points de terminaison de l’API serveur.</span><span class="sxs-lookup"><span data-stu-id="93894-172">[Authorization policies](xref:security/authorization/policies) can be used to limit user access to server API endpoints.</span></span>

> [!NOTE]
> <span data-ttu-id="93894-173">Ce guide n’inclut pas actuellement l’autorisation des utilisateurs sur la base de leurs [rôles AAD définis par l’utilisateur](#user-defined-roles).</span><span class="sxs-lookup"><span data-stu-id="93894-173">This guidance doesn't currently include authorizing users on the basis of their [AAD user-defined roles](#user-defined-roles).</span></span>

### <a name="packages"></a><span data-ttu-id="93894-174">Paquets</span><span class="sxs-lookup"><span data-stu-id="93894-174">Packages</span></span>

<span data-ttu-id="93894-175">Ajoutez des références de package à l’application *serveur* pour les packages suivants :</span><span class="sxs-lookup"><span data-stu-id="93894-175">Add package references to the *Server* app for the following packages:</span></span>

* [<span data-ttu-id="93894-176">Microsoft.Graph</span><span class="sxs-lookup"><span data-stu-id="93894-176">Microsoft.Graph</span></span>](https://www.nuget.org/packages/Microsoft.Graph)
* <span data-ttu-id="93894-177">[Microsoft. Identity Model. clients. ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)</span><span class="sxs-lookup"><span data-stu-id="93894-177">[Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)</span></span>

### <a name="azure-configuration"></a><span data-ttu-id="93894-178">Configuration Azure</span><span class="sxs-lookup"><span data-stu-id="93894-178">Azure configuration</span></span>

* <span data-ttu-id="93894-179">Vérifiez que l’inscription de l’application *serveur* est donnée à l’accès d’API à l’autorisation API Graph pour `Directory.Read.All` , qui est le niveau d’accès le moins privilégié pour les groupes de sécurité.</span><span class="sxs-lookup"><span data-stu-id="93894-179">Confirm that the *Server* app registration is given API access to the Graph API permission for `Directory.Read.All`, which is the least-privileged access level for security groups.</span></span> <span data-ttu-id="93894-180">Confirmez que le consentement de l’administrateur est appliqué à l’autorisation après avoir effectué l’attribution d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="93894-180">Confirm that admin consent is applied to the permission after making the permission assignment.</span></span>
* <span data-ttu-id="93894-181">Attribuez une nouvelle clé secrète client à l’application *serveur* .</span><span class="sxs-lookup"><span data-stu-id="93894-181">Assign a new client secret to the *Server* app.</span></span> <span data-ttu-id="93894-182">Notez le secret de la configuration de l’application dans la section paramètres de l' [application](#app-settings) .</span><span class="sxs-lookup"><span data-stu-id="93894-182">Note the secret for the app's configuration in the [App settings](#app-settings) section.</span></span>

### <a name="app-settings"></a><span data-ttu-id="93894-183">Paramètres de l’application</span><span class="sxs-lookup"><span data-stu-id="93894-183">App settings</span></span>

<span data-ttu-id="93894-184">Dans le fichier de paramètres d’application ( `appsettings.json` ou `appsettings.Production.json` ), créez une `ClientSecret` entrée avec la clé secrète client de l’application *serveur* à partir de la portail Azure :</span><span class="sxs-lookup"><span data-stu-id="93894-184">In the app settings file (`appsettings.json` or `appsettings.Production.json`), create a `ClientSecret` entry with the *Server* app's client secret from the Azure portal:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "XXXXXXXXXXXX.onmicrosoft.com",
  "TenantId": "{GUID}",
  "ClientId": "{GUID}",
  "ClientSecret": "{CLIENT SECRET}"
},
```

<span data-ttu-id="93894-185">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="93894-185">For example:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "contoso.onmicrosoft.com",
  "TenantId": "34bf0ec1-7aeb-4b5d-ba42-82b059b3abe8",
  "ClientId": "05d198e0-38c6-4efc-a67c-8ee87ed9bd3d",
  "ClientSecret": "54uE~9a.-wW91fe8cRR25ag~-I5gEq_92~"
},
```

### <a name="authorization-policies"></a><span data-ttu-id="93894-186">Vous pouvez également utiliser les stratégies d'autorisation</span><span class="sxs-lookup"><span data-stu-id="93894-186">Authorization policies</span></span>

<span data-ttu-id="93894-187">Créer des [stratégies d’autorisation](xref:security/authorization/policies) pour les groupes de sécurité AAD et les rôles d’administrateur AAD dans les applications *serveur* `Startup.ConfigureServices` ( `Startup.cs` ) en fonction des ID d’objet de groupe et des [ID d’objet de rôle d’administrateur AAD](#aad-administrator-role-object-ids).</span><span class="sxs-lookup"><span data-stu-id="93894-187">Create [authorization policies](xref:security/authorization/policies) for AAD security groups and AAD Administrator Roles in the *Server* app's `Startup.ConfigureServices` (`Startup.cs`) based on group object IDs and [AAD Administrator Role object IDs](#aad-administrator-role-object-ids).</span></span>

<span data-ttu-id="93894-188">Par exemple, une stratégie de rôle d’administrateur de facturation Azure a la configuration suivante :</span><span class="sxs-lookup"><span data-stu-id="93894-188">For example, an Azure Billing Administrator Role policy has the following configuration:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdmin", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="93894-189">Pour plus d'informations, consultez <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="93894-189">For more information, see <xref:security/authorization/policies>.</span></span>

### <a name="controller-access"></a><span data-ttu-id="93894-190">Accès du contrôleur</span><span class="sxs-lookup"><span data-stu-id="93894-190">Controller access</span></span>

<span data-ttu-id="93894-191">Exiger des stratégies sur les contrôleurs de l’application *serveur* .</span><span class="sxs-lookup"><span data-stu-id="93894-191">Require policies on the *Server* app's controllers.</span></span>

<span data-ttu-id="93894-192">L’exemple suivant limite l’accès aux données de facturation du `BillingDataController` à des administrateurs de facturation Azure avec le nom de stratégie `BillingAdmin` , tel que configuré dans la section [stratégies d’autorisation](#authorization-policies) :</span><span class="sxs-lookup"><span data-stu-id="93894-192">The following example limits access to billing data from the `BillingDataController` to Azure Billing Administrators with a policy name of `BillingAdmin`, as configured in the [Authorization policies](#authorization-policies) section:</span></span>

```csharp
[Authorize(Policy = "BillingAdmin")]
[ApiController]
[Route("[controller]")]
public class BillingDataController : ControllerBase
{
    ...
}
```

### <a name="service-configuration"></a><span data-ttu-id="93894-193">Configuration de service</span><span class="sxs-lookup"><span data-stu-id="93894-193">Service configuration</span></span>

<span data-ttu-id="93894-194">Dans la méthode de l’application *serveur* , `Startup.ConfigureServices` Ajoutez une logique pour que les API Graph appellent et établissent `group` des revendications d’utilisateur pour les groupes de sécurité et les rôles de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="93894-194">In the *Server* app's `Startup.ConfigureServices` method add logic to make the Graph API call and establish user `group` claims for the user's security groups and roles.</span></span>

> [!NOTE]
> <span data-ttu-id="93894-195">L’exemple de code dans cette section utilise le Bibliothèque d’authentification Active Directory (ADAL), qui est basé sur Microsoft Identity Platform v 1.0.</span><span class="sxs-lookup"><span data-stu-id="93894-195">The example code in this section uses the Active Directory Authentication Library (ADAL), which is based on Microsoft Identity Platform v1.0.</span></span> <span data-ttu-id="93894-196">Cette rubrique sera mise à jour pour la Identity version 2.0 de .net 5.</span><span class="sxs-lookup"><span data-stu-id="93894-196">This topic will be updated for Identity v2.0 for .NET 5.</span></span> <span data-ttu-id="93894-197">Suivez la progression de ce travail en surveillant [[RC1] Microsoft Identity Platform 2,0 pour Blazor (dotnet/AspNetCore.Docs #19503)](https://github.com/dotnet/AspNetCore.Docs/issues/19503).</span><span class="sxs-lookup"><span data-stu-id="93894-197">Track progress on this work by monitoring [[RC1] Microsoft Identity Platform 2.0 for Blazor (dotnet/AspNetCore.Docs #19503)](https://github.com/dotnet/AspNetCore.Docs/issues/19503).</span></span>

<span data-ttu-id="93894-198">Des espaces de noms supplémentaires sont requis pour le code de la `Startup` classe de l’application *serveur* .</span><span class="sxs-lookup"><span data-stu-id="93894-198">Additional namespaces are required for the code in the `Startup` class of the *Server* app.</span></span> <span data-ttu-id="93894-199">L’ensemble d' `using` instructions suivant contient les espaces de noms requis pour le code qui suit dans cette section :</span><span class="sxs-lookup"><span data-stu-id="93894-199">The following set of `using` statements includes the required namespaces for the code that follows in this section:</span></span>

```csharp
using System;
using System.IdentityModel.Tokens.Jwt;
using System.Linq;
using System.Net.Http.Headers;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.AzureAD.UI;
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Graph;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.IdentityModel.Logging;
```

<span data-ttu-id="93894-200">Lors de la configuration <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents> :</span><span class="sxs-lookup"><span data-stu-id="93894-200">When configuring <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span></span>

* <span data-ttu-id="93894-201">Incluez éventuellement le traitement de <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="93894-201">Optionally include processing for <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span></span> <span data-ttu-id="93894-202">Par exemple, l’application peut consigner l’échec de l’authentification.</span><span class="sxs-lookup"><span data-stu-id="93894-202">For example, the app can log failed authentication.</span></span>
* <span data-ttu-id="93894-203">Dans <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType> , effectuez un appel API Graph pour obtenir les groupes et les rôles de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="93894-203">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>, make a Graph API call to obtain the user's groups and roles.</span></span>

> [!WARNING]
> <span data-ttu-id="93894-204"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> fournit des informations d’identification personnelle (PII) dans la journalisation des messages.</span><span class="sxs-lookup"><span data-stu-id="93894-204"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> provides Personally Identifiable Information (PII) in logging messages.</span></span> <span data-ttu-id="93894-205">Activez uniquement les PII pour le débogage avec les comptes d’utilisateur de test.</span><span class="sxs-lookup"><span data-stu-id="93894-205">Only activate PII for debugging with test user accounts.</span></span>

<span data-ttu-id="93894-206">Dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="93894-206">In `Startup.ConfigureServices`:</span></span>

```csharp
#if DEBUG
IdentityModelEventSource.ShowPII = true;
#endif

services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));

services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
{
    options.Events = new JwtBearerEvents()
    {
        OnAuthenticationFailed = context =>
        {
            // Optional: Log the exception

#if DEBUG
            Console.WriteLine($"OnAuthenticationFailed: {context.Exception}");
#endif

            return Task.FromResult(0);
        },
        OnTokenValidated = async context =>
        {
            var accessToken = context.SecurityToken as JwtSecurityToken;
            var oid = accessToken.Claims.FirstOrDefault(x => x.Type == "oid")?
                .Value;

            if (!string.IsNullOrEmpty(oid))
            {
                var authContext = new AuthenticationContext(
                    Configuration["AzureAd:Instance"] +
                    Configuration["AzureAd:TenantId"]);
                AuthenticationResult authResult = null;

                try
                {
                    authResult = await authContext.AcquireTokenSilentAsync(
                        "https://graph.microsoft.com", 
                        Configuration["AzureAd:ClientId"]);
                }
                catch (AdalException adalException)
                {
                    if (adalException.ErrorCode == 
                        AdalError.FailedToAcquireTokenSilently || 
                        adalException.ErrorCode == 
                        AdalError.UserInteractionRequired)
                    {
                        var userAssertion = new UserAssertion(accessToken.RawData,
                            "urn:ietf:params:oauth:grant-type:jwt-bearer", oid);
                        var clientCredential = new ClientCredential(
                            Configuration["AzureAd:ClientId"],
                            Configuration["AzureAd:ClientSecret"]);
                        authResult = await authContext.AcquireTokenAsync(
                            "https://graph.microsoft.com", clientCredential, 
                            userAssertion);
                    }
                }

                var graphClient = new GraphServiceClient(
                    new DelegateAuthenticationProvider(async requestMessage => {
                        requestMessage.Headers.Authorization =
                            new AuthenticationHeaderValue("Bearer", 
                                authResult.AccessToken);

                        await Task.CompletedTask;
                    }));

                var userIdentity = (ClaimsIdentity)context.Principal.Identity;

                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    groupsAndAzureRoles = await graphClient.Users[oid].MemberOf
                        .Request().GetAsync();
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the error

#if DEBUG
                    Console.WriteLine(
                        "OnTokenValidated: Service Exception: " +
                        $"{serviceException.Message}");
#endif
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }
            }
            else
            {
                // Optional: Log missing OID claim

#if DEBUG
                Console.WriteLine($"OnTokenValidated: OID missing: " +
                    $"{accessToken.RawData}");
#endif
            }

            await Task.FromResult(0);
        }
    };
});
```

<span data-ttu-id="93894-207">Dans l’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="93894-207">In the preceding example:</span></span>

* <span data-ttu-id="93894-208">L’acquisition du jeton silencieux ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A> ) est tentée en premier, car le jeton d’accès a peut-être déjà été stocké dans le cache de jetons Adal.</span><span class="sxs-lookup"><span data-stu-id="93894-208">Silent token acquisition (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>) is attempted first because the access token may have already been stored in the ADAL token cache.</span></span> <span data-ttu-id="93894-209">Il est plus rapide d’obtenir le jeton à partir du cache que de demander un nouveau jeton.</span><span class="sxs-lookup"><span data-stu-id="93894-209">It's faster to obtain the token from cache than to request a new token.</span></span>
* <span data-ttu-id="93894-210">Si le jeton d’accès n’est pas acquis à partir du cache ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> ou <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> est levé), une assertion utilisateur ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion> ) est effectuée avec les informations d’identification du client ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential> ) pour obtenir le jeton pour le compte de l’utilisateur ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A> ).</span><span class="sxs-lookup"><span data-stu-id="93894-210">If the access token isn't acquired from cache (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> or <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> is thrown), a user assertion (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion>) is made with the client credential (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential>) to obtain the token on behalf of the user (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A>).</span></span> <span data-ttu-id="93894-211">Ensuite, le `Microsoft.Graph.GraphServiceClient` peut continuer à utiliser le jeton pour effectuer l’appel de API Graph.</span><span class="sxs-lookup"><span data-stu-id="93894-211">Next, the `Microsoft.Graph.GraphServiceClient` can proceed to use the token to make the Graph API call.</span></span> <span data-ttu-id="93894-212">Le jeton est placé dans le cache de jetons ADAL.</span><span class="sxs-lookup"><span data-stu-id="93894-212">The token is placed into the ADAL token cache.</span></span> <span data-ttu-id="93894-213">Pour les appels de API Graph ultérieurs pour le même utilisateur, le jeton est acquis à partir du cache en mode silencieux avec <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="93894-213">For future Graph API calls for the same user, the token is acquired from cache silently with <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>.</span></span>

<span data-ttu-id="93894-214">Le code de <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> n’obtient pas les appartenances transitives.</span><span class="sxs-lookup"><span data-stu-id="93894-214">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't obtain transitive memberships.</span></span> <span data-ttu-id="93894-215">Pour modifier le code afin d’obtenir les appartenances directes et transitives :</span><span class="sxs-lookup"><span data-stu-id="93894-215">To change the code to obtain direct and transitive memberships:</span></span>

* <span data-ttu-id="93894-216">Pour la ligne de code :</span><span class="sxs-lookup"><span data-stu-id="93894-216">For the code line:</span></span>

  ```csharp
  IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

  <span data-ttu-id="93894-217">Remplacez la ligne précédente par :</span><span class="sxs-lookup"><span data-stu-id="93894-217">Replace the preceding line with:</span></span>

  ```csharp
  IUserTransitiveMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

* <span data-ttu-id="93894-218">Pour la ligne de code :</span><span class="sxs-lookup"><span data-stu-id="93894-218">For the code line:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request().GetAsync();
  ```

  <span data-ttu-id="93894-219">Remplacez la ligne précédente par :</span><span class="sxs-lookup"><span data-stu-id="93894-219">Replace the preceding line with:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].TransitiveMemberOf.Request()
      .GetAsync();
  ```

<span data-ttu-id="93894-220">Le code dans <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> ne fait pas la distinction entre les groupes de sécurité AAD et les rôles d’administrateur AAD lors de la création de revendications.</span><span class="sxs-lookup"><span data-stu-id="93894-220">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't distinguish between AAD security groups and AAD Administrator Roles when creating claims.</span></span> <span data-ttu-id="93894-221">Pour que l’application fasse la distinction entre les groupes et les rôles, activez la case à cocher lors de l' `entry.ODataType` itération des groupes et des rôles.</span><span class="sxs-lookup"><span data-stu-id="93894-221">For the app to distinguish between groups and roles, check the `entry.ODataType` when iterating through the groups and roles.</span></span> <span data-ttu-id="93894-222">Pour créer des revendications de rôle et de groupe de sécurité distinctes, utilisez un code similaire à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="93894-222">To create separate security group and role claims, use code similar to the following:</span></span>

```csharp
foreach (var entry in groupsAndAzureRoles)
{
    if (entry.ODataType == "#microsoft.graph.group")
    {
        userIdentity.AddClaim(new Claim("group", entry.Id));
    }
    else
    {
        // entry.ODataType == "#microsoft.graph.directoryRole"
        userIdentity.AddClaim(new Claim("role", entry.Id));
    }
}
```

## <a name="user-defined-roles"></a><span data-ttu-id="93894-223">Rôles définis par l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="93894-223">User-defined roles</span></span>

<span data-ttu-id="93894-224">Une application inscrite à AAD peut également être configurée pour utiliser des rôles définis par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="93894-224">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="93894-225">Pour configurer l’application dans le Portail Azure pour fournir une `roles` revendication d’appartenance, consultez [Comment : ajouter des rôles d’application dans votre application et les recevoir dans le jeton](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) de la documentation Azure.</span><span class="sxs-lookup"><span data-stu-id="93894-225">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="93894-226">L’exemple suivant suppose qu’une application est configurée avec deux rôles :</span><span class="sxs-lookup"><span data-stu-id="93894-226">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="93894-227">Bien que vous ne puissiez pas attribuer des rôles à des groupes de sécurité sans compte Azure AD Premium, vous pouvez affecter des utilisateurs à des rôles et recevoir une `roles` revendication pour les utilisateurs disposant d’un compte Azure standard.</span><span class="sxs-lookup"><span data-stu-id="93894-227">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="93894-228">Les instructions de cette section ne nécessitent pas de compte Azure AD Premium.</span><span class="sxs-lookup"><span data-stu-id="93894-228">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="93894-229">Plusieurs rôles sont affectés dans le Portail Azure en **_rajoutant un utilisateur_** pour chaque attribution de rôle supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="93894-229">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="93894-230">La `roles` revendication unique envoyée par AAD présente les rôles définis par l’utilisateur en tant que `appRoles` `value` s dans un tableau JSON.</span><span class="sxs-lookup"><span data-stu-id="93894-230">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="93894-231">L’application doit convertir le tableau JSON de rôles en `role` revendications individuelles.</span><span class="sxs-lookup"><span data-stu-id="93894-231">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="93894-232">La `CustomUserFactory` section des [groupes définis par l’utilisateur et des rôles d’administrateur AAD](#user-defined-groups-and-administrator-roles) est configurée pour agir sur une `roles` revendication avec une valeur de tableau JSON.</span><span class="sxs-lookup"><span data-stu-id="93894-232">The `CustomUserFactory` shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="93894-233">Ajoutez et inscrivez `CustomUserFactory` dans l’application autonome ou dans l' *`Client`* application d’une Blazor solution hébergée, comme indiqué dans la section [groupes définis par l’utilisateur et rôles d’administrateur AAD](#user-defined-groups-and-administrator-roles) .</span><span class="sxs-lookup"><span data-stu-id="93894-233">Add and register the `CustomUserFactory` in the standalone app or *`Client`* app of a hosted Blazor solution as shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span> <span data-ttu-id="93894-234">Il n’est pas nécessaire de fournir du code pour supprimer la revendication d’origine `roles` , car elle est automatiquement supprimée par l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="93894-234">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="93894-235">Dans `Program.Main` l’application autonome ou l' *`Client`* application d’une solution hébergée Blazor , spécifiez la revendication nommée « `role` » comme revendication de rôle :</span><span class="sxs-lookup"><span data-stu-id="93894-235">In `Program.Main` of the standalone app or *`Client`* app of a hosted Blazor solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="93894-236">Les approches d’autorisation des composants sont fonctionnelles à ce stade.</span><span class="sxs-lookup"><span data-stu-id="93894-236">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="93894-237">L’un des mécanismes d’autorisation des composants peut utiliser le `admin` rôle pour autoriser l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="93894-237">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="93894-238">[ `AuthorizeView` composant](xref:blazor/security/index#authorizeview-component) (exemple : `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="93894-238">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="93894-239">[ `[Authorize]` directive d’attribut](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (exemple : `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="93894-239">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="93894-240">[Logique procédurale](xref:blazor/security/index#procedural-logic) (exemple : `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="93894-240">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="93894-241">Plusieurs tests de rôle sont pris en charge :</span><span class="sxs-lookup"><span data-stu-id="93894-241">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-administrator-role-object-ids"></a><span data-ttu-id="93894-242">ID d’objet du rôle administrateur AAD</span><span class="sxs-lookup"><span data-stu-id="93894-242">AAD Administrator Role Object IDs</span></span>

<span data-ttu-id="93894-243">Les ID d’objet présentés dans le tableau suivant sont utilisés pour créer des [stratégies](xref:security/authorization/policies) pour les `group` revendications.</span><span class="sxs-lookup"><span data-stu-id="93894-243">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="93894-244">Les stratégies permettent à une application d’autoriser les utilisateurs à effectuer différentes activités dans une application.</span><span class="sxs-lookup"><span data-stu-id="93894-244">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="93894-245">Pour plus d’informations, consultez la section [groupes définis par l’utilisateur et rôles d’administrateur AAD](#user-defined-groups-and-administrator-roles) .</span><span class="sxs-lookup"><span data-stu-id="93894-245">For more information, see the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span>

<span data-ttu-id="93894-246">Rôle administrateur AAD</span><span class="sxs-lookup"><span data-stu-id="93894-246">AAD Administrator Role</span></span> | <span data-ttu-id="93894-247">ID de l'objet</span><span class="sxs-lookup"><span data-stu-id="93894-247">Object ID</span></span>
--- | ---
<span data-ttu-id="93894-248">Administrateur d’application</span><span class="sxs-lookup"><span data-stu-id="93894-248">Application administrator</span></span> | <span data-ttu-id="93894-249">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="93894-249">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="93894-250">Développeur d’applications</span><span class="sxs-lookup"><span data-stu-id="93894-250">Application developer</span></span> | <span data-ttu-id="93894-251">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="93894-251">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="93894-252">Administrateur d’authentification</span><span class="sxs-lookup"><span data-stu-id="93894-252">Authentication administrator</span></span> | <span data-ttu-id="93894-253">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="93894-253">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="93894-254">Administrateur Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="93894-254">Azure DevOps administrator</span></span> | <span data-ttu-id="93894-255">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="93894-255">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="93894-256">Administrateur Azure Information Protection</span><span class="sxs-lookup"><span data-stu-id="93894-256">Azure Information Protection administrator</span></span> | <span data-ttu-id="93894-257">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="93894-257">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="93894-258">Administrateur de jeux de clés IEF B2C</span><span class="sxs-lookup"><span data-stu-id="93894-258">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="93894-259">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="93894-259">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="93894-260">Administrateur de la stratégie B2C IEF</span><span class="sxs-lookup"><span data-stu-id="93894-260">B2C IEF Policy administrator</span></span> | <span data-ttu-id="93894-261">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="93894-261">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="93894-262">Administrateur de workflow utilisateur B2C</span><span class="sxs-lookup"><span data-stu-id="93894-262">B2C user flow administrator</span></span> | <span data-ttu-id="93894-263">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="93894-263">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="93894-264">Administrateur de l’attribut de workflow de l’utilisateur B2C</span><span class="sxs-lookup"><span data-stu-id="93894-264">B2C user flow attribute administrator</span></span> | <span data-ttu-id="93894-265">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="93894-265">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="93894-266">Administrateur de facturation</span><span class="sxs-lookup"><span data-stu-id="93894-266">Billing administrator</span></span> | <span data-ttu-id="93894-267">69ff516a-B57D-4697-A429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="93894-267">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="93894-268">Administrateur d’application cloud</span><span class="sxs-lookup"><span data-stu-id="93894-268">Cloud application administrator</span></span> | <span data-ttu-id="93894-269">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="93894-269">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="93894-270">Administrateur d’appareil cloud</span><span class="sxs-lookup"><span data-stu-id="93894-270">Cloud device administrator</span></span> | <span data-ttu-id="93894-271">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="93894-271">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="93894-272">Administrateur de conformité</span><span class="sxs-lookup"><span data-stu-id="93894-272">Compliance administrator</span></span> | <span data-ttu-id="93894-273">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="93894-273">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="93894-274">Administrateur des données de conformité</span><span class="sxs-lookup"><span data-stu-id="93894-274">Compliance data administrator</span></span> | <span data-ttu-id="93894-275">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="93894-275">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="93894-276">Administrateur de l’accès conditionnel</span><span class="sxs-lookup"><span data-stu-id="93894-276">Conditional Access administrator</span></span> | <span data-ttu-id="93894-277">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="93894-277">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="93894-278">Approbateur d’accès au référentiel sécurisé client</span><span class="sxs-lookup"><span data-stu-id="93894-278">Customer LockBox access approver</span></span> | <span data-ttu-id="93894-279">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="93894-279">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="93894-280">Administrateur Desktop Analytics</span><span class="sxs-lookup"><span data-stu-id="93894-280">Desktop Analytics administrator</span></span> | <span data-ttu-id="93894-281">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="93894-281">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="93894-282">Lecteurs d’annuaires</span><span class="sxs-lookup"><span data-stu-id="93894-282">Directory readers</span></span> | <span data-ttu-id="93894-283">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="93894-283">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="93894-284">Administrateur Dynamics 365</span><span class="sxs-lookup"><span data-stu-id="93894-284">Dynamics 365 administrator</span></span> | <span data-ttu-id="93894-285">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="93894-285">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="93894-286">Administrateur Exchange</span><span class="sxs-lookup"><span data-stu-id="93894-286">Exchange administrator</span></span> | <span data-ttu-id="93894-287">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="93894-287">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="93894-288">IdentityAdministrateur du fournisseur externe</span><span class="sxs-lookup"><span data-stu-id="93894-288">External Identity Provider administrator</span></span> | <span data-ttu-id="93894-289">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="93894-289">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="93894-290">Administrateur général</span><span class="sxs-lookup"><span data-stu-id="93894-290">Global administrator</span></span> | <span data-ttu-id="93894-291">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="93894-291">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="93894-292">Lecteur général</span><span class="sxs-lookup"><span data-stu-id="93894-292">Global reader</span></span> | <span data-ttu-id="93894-293">f6903b21-6aba-4124-B44C-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="93894-293">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="93894-294">Administrateur de groupes</span><span class="sxs-lookup"><span data-stu-id="93894-294">Groups administrator</span></span> | <span data-ttu-id="93894-295">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="93894-295">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="93894-296">Inviteur d’invités</span><span class="sxs-lookup"><span data-stu-id="93894-296">Guest inviter</span></span> | <span data-ttu-id="93894-297">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="93894-297">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="93894-298">Administrateur du support technique</span><span class="sxs-lookup"><span data-stu-id="93894-298">Helpdesk administrator</span></span> | <span data-ttu-id="93894-299">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="93894-299">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="93894-300">Administrateur Intune</span><span class="sxs-lookup"><span data-stu-id="93894-300">Intune administrator</span></span> | <span data-ttu-id="93894-301">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="93894-301">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="93894-302">Administrateur Kaizala</span><span class="sxs-lookup"><span data-stu-id="93894-302">Kaizala administrator</span></span> | <span data-ttu-id="93894-303">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="93894-303">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="93894-304">Administrateur de licence</span><span class="sxs-lookup"><span data-stu-id="93894-304">License administrator</span></span> | <span data-ttu-id="93894-305">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="93894-305">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="93894-306">Lecteur de confidentialité du Centre de messages</span><span class="sxs-lookup"><span data-stu-id="93894-306">Message center privacy reader</span></span> | <span data-ttu-id="93894-307">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="93894-307">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="93894-308">Lecteur du Centre de messages</span><span class="sxs-lookup"><span data-stu-id="93894-308">Message center reader</span></span> | <span data-ttu-id="93894-309">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="93894-309">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="93894-310">Administrateur d’applications Office</span><span class="sxs-lookup"><span data-stu-id="93894-310">Office apps administrator</span></span> | <span data-ttu-id="93894-311">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="93894-311">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="93894-312">Administrateur de mots de passe</span><span class="sxs-lookup"><span data-stu-id="93894-312">Password administrator</span></span> | <span data-ttu-id="93894-313">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="93894-313">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="93894-314">Administrateur Power BI</span><span class="sxs-lookup"><span data-stu-id="93894-314">Power BI administrator</span></span> | <span data-ttu-id="93894-315">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="93894-315">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="93894-316">Administrateur de plateforme Power</span><span class="sxs-lookup"><span data-stu-id="93894-316">Power platform administrator</span></span> | <span data-ttu-id="93894-317">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="93894-317">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="93894-318">Administrateur d’authentification privilégié</span><span class="sxs-lookup"><span data-stu-id="93894-318">Privileged authentication administrator</span></span> | <span data-ttu-id="93894-319">0829f731-b46d-419F-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="93894-319">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="93894-320">Administrateur de rôle privilégié</span><span class="sxs-lookup"><span data-stu-id="93894-320">Privileged role administrator</span></span> | <span data-ttu-id="93894-321">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="93894-321">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="93894-322">Lecteur de rapports</span><span class="sxs-lookup"><span data-stu-id="93894-322">Reports reader</span></span> | <span data-ttu-id="93894-323">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="93894-323">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="93894-324">Administrateur de recherche</span><span class="sxs-lookup"><span data-stu-id="93894-324">Search administrator</span></span> | <span data-ttu-id="93894-325">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="93894-325">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="93894-326">Éditeur de recherche</span><span class="sxs-lookup"><span data-stu-id="93894-326">Search editor</span></span> | <span data-ttu-id="93894-327">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="93894-327">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="93894-328">Administrateur de sécurité</span><span class="sxs-lookup"><span data-stu-id="93894-328">Security administrator</span></span> | <span data-ttu-id="93894-329">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="93894-329">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="93894-330">Opérateur de sécurité</span><span class="sxs-lookup"><span data-stu-id="93894-330">Security operator</span></span> | <span data-ttu-id="93894-331">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="93894-331">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="93894-332">Lecteur de sécurité</span><span class="sxs-lookup"><span data-stu-id="93894-332">Security reader</span></span> | <span data-ttu-id="93894-333">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="93894-333">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="93894-334">Administrateur de support de service</span><span class="sxs-lookup"><span data-stu-id="93894-334">Service support administrator</span></span> | <span data-ttu-id="93894-335">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="93894-335">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="93894-336">Administrateur SharePoint</span><span class="sxs-lookup"><span data-stu-id="93894-336">SharePoint administrator</span></span> | <span data-ttu-id="93894-337">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="93894-337">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="93894-338">Administrateur Skype Entreprise</span><span class="sxs-lookup"><span data-stu-id="93894-338">Skype for Business administrator</span></span> | <span data-ttu-id="93894-339">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="93894-339">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="93894-340">Administrateur des communications Teams</span><span class="sxs-lookup"><span data-stu-id="93894-340">Teams Communications Administrator</span></span> | <span data-ttu-id="93894-341">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="93894-341">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="93894-342">Ingénieur de support des communications Teams</span><span class="sxs-lookup"><span data-stu-id="93894-342">Teams Communications Support Engineer</span></span> | <span data-ttu-id="93894-343">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="93894-343">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="93894-344">Spécialiste des communications des équipes</span><span class="sxs-lookup"><span data-stu-id="93894-344">Teams Communications Specialist</span></span> | <span data-ttu-id="93894-345">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="93894-345">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="93894-346">Administrateur du service Teams</span><span class="sxs-lookup"><span data-stu-id="93894-346">Teams Service Administrator</span></span> | <span data-ttu-id="93894-347">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="93894-347">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="93894-348">Administrateur d’utilisateurs</span><span class="sxs-lookup"><span data-stu-id="93894-348">User administrator</span></span> | <span data-ttu-id="93894-349">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="93894-349">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="93894-350">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="93894-350">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
