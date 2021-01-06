---
title: ASP.NET Core Blazor WebAssembly avec des groupes et des rôles Azure Active Directory
author: guardrex
description: Découvrez comment configurer Blazor WebAssembly pour utiliser des groupes et des rôles Azure Active Directory.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 10/27/2020
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
ms.openlocfilehash: ded70f028b3021574ba260838837d9b23abd72f1
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "94981880"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-user-defined-roles"></a><span data-ttu-id="a81f9-103">Groupes Azure Active Directory (AAD), rôles d’administrateur et rôles définis par l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="a81f9-103">Azure Active Directory (AAD) groups, Administrator Roles, and user-defined roles</span></span>

<span data-ttu-id="a81f9-104">Par [Luke Latham](https://github.com/guardrex) et [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="a81f9-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

> [!NOTE]
> <span data-ttu-id="a81f9-105">Cet article s’applique à Blazor ASP.net Core Apps version 3,1 avec Microsoft Identity v 1.0 et est prévu pour la mise à jour de 5,0 avec Identity v 2.0.</span><span class="sxs-lookup"><span data-stu-id="a81f9-105">This article applies to Blazor ASP.NET Core apps version 3.1 with Microsoft Identity v1.0 and is scheduled for update to 5.0 with Identity v2.0.</span></span> <span data-ttu-id="a81f9-106">Pour plus d’informations, voir [ Blazor WASM with AAD/B2C Groups and Roles (dotnet/AspNetCore.Docs #17683)](https://github.com/dotnet/AspNetCore.Docs/issues/17683).</span><span class="sxs-lookup"><span data-stu-id="a81f9-106">For more information, see [Blazor WASM with AAD/B2C groups and roles (dotnet/AspNetCore.Docs #17683)](https://github.com/dotnet/AspNetCore.Docs/issues/17683).</span></span>

<span data-ttu-id="a81f9-107">Azure Active Directory (AAD) fournit plusieurs approches d’autorisation qui peuvent être combinées avec ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="a81f9-107">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="a81f9-108">Groupes définis par l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="a81f9-108">User-defined groups</span></span>
  * <span data-ttu-id="a81f9-109">Sécurité</span><span class="sxs-lookup"><span data-stu-id="a81f9-109">Security</span></span>
  * <span data-ttu-id="a81f9-110">Microsoft 365</span><span class="sxs-lookup"><span data-stu-id="a81f9-110">Microsoft 365</span></span>
  * <span data-ttu-id="a81f9-111">Distribution</span><span class="sxs-lookup"><span data-stu-id="a81f9-111">Distribution</span></span>
* <span data-ttu-id="a81f9-112">Rôles</span><span class="sxs-lookup"><span data-stu-id="a81f9-112">Roles</span></span>
  * <span data-ttu-id="a81f9-113">Rôles d’administrateur AAD</span><span class="sxs-lookup"><span data-stu-id="a81f9-113">AAD Administrator Roles</span></span>
  * <span data-ttu-id="a81f9-114">Rôles définis par l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="a81f9-114">User-defined roles</span></span>

<span data-ttu-id="a81f9-115">Les instructions de cet article s’appliquent aux Blazor WebAssembly scénarios de déploiement AAD décrits dans les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="a81f9-115">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="a81f9-116">Autonome avec des comptes Microsoft</span><span class="sxs-lookup"><span data-stu-id="a81f9-116">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="a81f9-117">Autonome avec AAD</span><span class="sxs-lookup"><span data-stu-id="a81f9-117">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="a81f9-118">Hébergé avec AAD</span><span class="sxs-lookup"><span data-stu-id="a81f9-118">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="scopes"></a><span data-ttu-id="a81f9-119">Étendues</span><span class="sxs-lookup"><span data-stu-id="a81f9-119">Scopes</span></span>

<span data-ttu-id="a81f9-120">Un appel d' [API Microsoft Graph](/graph/use-the-api) est requis pour tout utilisateur d’application disposant de plus de cinq appartenances de groupe de sécurité et de rôle d’administrateur AAD.</span><span class="sxs-lookup"><span data-stu-id="a81f9-120">A [Microsoft Graph API](/graph/use-the-api) call is required for any app user with more than five AAD Administrator role and security group memberships.</span></span>

<span data-ttu-id="a81f9-121">Pour autoriser les appels API Graph, attribuez à l’application autonome ou à *`Client`* une solution hébergée l' Blazor une des [autorisations API Graph suivantes (étendues)](/graph/permissions-reference) dans le portail Azure :</span><span class="sxs-lookup"><span data-stu-id="a81f9-121">To permit Graph API calls, give the standalone or *`Client`* app of a hosted Blazor solution any of the following [Graph API permissions (scopes)](/graph/permissions-reference) in the Azure portal:</span></span>

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

<span data-ttu-id="a81f9-122">`Directory.Read.All` est l’étendue la moins privilégiée et est l’étendue utilisée pour l’exemple décrit dans cet article.</span><span class="sxs-lookup"><span data-stu-id="a81f9-122">`Directory.Read.All` is the least-privileged scope and is the scope used for the example described in this article.</span></span>

## <a name="user-defined-groups-and-administrator-roles"></a><span data-ttu-id="a81f9-123">Groupes définis par l’utilisateur et rôles d’administrateur</span><span class="sxs-lookup"><span data-stu-id="a81f9-123">User-defined groups and Administrator Roles</span></span>

<span data-ttu-id="a81f9-124">Pour configurer l’application dans le Portail Azure pour fournir une `groups` revendication d’appartenance, consultez les articles Azure suivants.</span><span class="sxs-lookup"><span data-stu-id="a81f9-124">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="a81f9-125">Affecter des utilisateurs à des groupes AAD définis par l’utilisateur et des rôles d’administrateur AAD.</span><span class="sxs-lookup"><span data-stu-id="a81f9-125">Assign users to user-defined AAD groups and AAD Administrator Roles.</span></span>

* [<span data-ttu-id="a81f9-126">Rôles utilisant les groupes de sécurité Azure AD</span><span class="sxs-lookup"><span data-stu-id="a81f9-126">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="a81f9-127">`groupMembershipClaims` attribut</span><span class="sxs-lookup"><span data-stu-id="a81f9-127">`groupMembershipClaims` attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="a81f9-128">Les exemples suivants partent du principe qu’un utilisateur est affecté au rôle d' *administrateur de facturation* AAD.</span><span class="sxs-lookup"><span data-stu-id="a81f9-128">The following examples assume that a user is assigned to the AAD *Billing Administrator* role.</span></span>

<span data-ttu-id="a81f9-129">La `groups` revendication unique envoyée par AAD présente les groupes et les rôles de l’utilisateur en tant qu’ID d’objet (Guid) dans un tableau JSON.</span><span class="sxs-lookup"><span data-stu-id="a81f9-129">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="a81f9-130">L’application doit convertir le tableau JSON de groupes et de rôles en `group` revendications individuelles pour lesquelles l’application peut créer des [stratégies](xref:security/authorization/policies) .</span><span class="sxs-lookup"><span data-stu-id="a81f9-130">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="a81f9-131">Lorsque le nombre de rôles d’administrateur AAD attribués et de groupes définis par l’utilisateur est supérieur à cinq, AAD envoie une `hasgroups` revendication avec une `true` valeur au lieu d’envoyer une `groups` revendication.</span><span class="sxs-lookup"><span data-stu-id="a81f9-131">When the number of assigned AAD Administrator Roles and user-defined groups exceeds five, AAD sends a `hasgroups` claim with a `true` value instead of sending a `groups` claim.</span></span> <span data-ttu-id="a81f9-132">Toute application pouvant comporter plus de cinq rôles et groupes affectés à ses utilisateurs doit effectuer un appel de API Graph séparé pour obtenir les rôles et les groupes d’un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="a81f9-132">Any app that may have more than five roles and groups assigned to its users must make a separate Graph API call to obtain a user's roles and groups.</span></span> <span data-ttu-id="a81f9-133">L’exemple d’implémentation fourni dans cet article s’adresse à ce scénario.</span><span class="sxs-lookup"><span data-stu-id="a81f9-133">The example implementation provided in this article addresses this scenario.</span></span> <span data-ttu-id="a81f9-134">Pour plus d’informations, consultez `groups` l' `hasgroups` article et les informations sur les revendications dans les jetons d’accès de la [plateforme Microsoft Identity : revendications de charge utile](/azure/active-directory/develop/access-tokens#payload-claims) .</span><span class="sxs-lookup"><span data-stu-id="a81f9-134">For more information, see the `groups` and `hasgroups` claims information in [Microsoft identity platform access tokens: Payload claims](/azure/active-directory/develop/access-tokens#payload-claims) article.</span></span>

<span data-ttu-id="a81f9-135">Étendez <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> pour inclure les propriétés de tableau pour les groupes et les rôles.</span><span class="sxs-lookup"><span data-stu-id="a81f9-135">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span> <span data-ttu-id="a81f9-136">Assignez un tableau vide à chaque propriété afin qu' `null` il ne soit pas nécessaire de vérifier si ces propriétés sont utilisées ultérieurement dans les `foreach` boucles.</span><span class="sxs-lookup"><span data-stu-id="a81f9-136">Assign an empty array to each property so that checking for `null` isn't required when these properties are used in `foreach` loops later.</span></span>

<span data-ttu-id="a81f9-137">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="a81f9-137">`CustomUserAccount.cs`:</span></span>

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

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="a81f9-138">Utilisez l' **une** des approches suivantes pour créer des revendications pour les groupes et rôles AAD :</span><span class="sxs-lookup"><span data-stu-id="a81f9-138">Use **either** of the following approaches to create claims for AAD groups and roles:</span></span>

* [<span data-ttu-id="a81f9-139">Utiliser le kit de développement logiciel (SDK) Graph</span><span class="sxs-lookup"><span data-stu-id="a81f9-139">Use the Graph SDK</span></span>](#use-the-graph-sdk)
* [<span data-ttu-id="a81f9-140">Utiliser un nommé `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="a81f9-140">Use a named `HttpClient`</span></span>](#use-a-named-httpclient)

### <a name="use-the-graph-sdk"></a><span data-ttu-id="a81f9-141">Utiliser le kit de développement logiciel (SDK) Graph</span><span class="sxs-lookup"><span data-stu-id="a81f9-141">Use the Graph SDK</span></span>

<span data-ttu-id="a81f9-142">Ajoutez une référence de package à l’application autonome ou à l' *`Client`* application d’une solution hébergée Blazor pour [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph) .</span><span class="sxs-lookup"><span data-stu-id="a81f9-142">Add a package reference to the standalone app or *`Client`* app of a hosted Blazor solution for [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph).</span></span>

<span data-ttu-id="a81f9-143">Ajoutez les classes et la configuration de l’utilitaire du SDK Graph dans la section *Kit de développement logiciel (SDK) Graph* de l' <xref:blazor/security/webassembly/graph-api#graph-sdk> article.</span><span class="sxs-lookup"><span data-stu-id="a81f9-143">Add the Graph SDK utility classes and configuration in the *Graph SDK* section of the <xref:blazor/security/webassembly/graph-api#graph-sdk> article.</span></span>

<span data-ttu-id="a81f9-144">Ajoutez la fabrique de comptes d’utilisateur personnalisée suivante à l’APPO autonome ou à l' *`Client`* application d’une solution hébergée Blazor ( `CustomAccountFactory.cs` ).</span><span class="sxs-lookup"><span data-stu-id="a81f9-144">Add the following custom user account factory to the standalone appo or *`Client`* app of a hosted Blazor solution (`CustomAccountFactory.cs`).</span></span> <span data-ttu-id="a81f9-145">La fabrique d’utilisateurs personnalisée est utilisée pour traiter les revendications de rôles et de groupes.</span><span class="sxs-lookup"><span data-stu-id="a81f9-145">The custom user factory is used to process roles and groups claims.</span></span> <span data-ttu-id="a81f9-146">Le `roles` tableau de revendications est abordé dans la section [rôles définis par l’utilisateur](#user-defined-roles) .</span><span class="sxs-lookup"><span data-stu-id="a81f9-146">The `roles` claim array is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="a81f9-147">Si la `hasgroups` revendication est présente, le kit de développement logiciel (SDK) Graph est utilisé pour effectuer une demande autorisée à API Graph pour obtenir les rôles et les groupes de l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="a81f9-147">If the `hasgroups` claim is present, the Graph SDK is used to make an authorized request to Graph API to obtain the user's roles and groups:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Json;
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
                userIdentity.AddClaim(new Claim("role", role));
            }

            if (userIdentity.HasClaim(c => c.Type == "hasgroups"))
            {
                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    var graphClient = ActivatorUtilities
                        .CreateInstance<GraphServiceClient>(serviceProvider);
                    var oid = userIdentity.Claims.FirstOrDefault(x => x.Type == "oid")?
                        .Value;

                    if (!string.IsNullOrEmpty(oid))
                    {
                        groupsAndAzureRoles = await graphClient.Users[oid].MemberOf
                            .Request().GetAsync();
                    }
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the error
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }

                var claim = userIdentity.Claims.FirstOrDefault(
                    c => c.Type == "hasgroups");

                userIdentity.RemoveClaim(claim);
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

<span data-ttu-id="a81f9-148">Le code précédent n’inclut pas les appartenances transitives.</span><span class="sxs-lookup"><span data-stu-id="a81f9-148">The preceding code doesn't include transitive memberships.</span></span> <span data-ttu-id="a81f9-149">Si l’application requiert des revendications d’appartenance de groupe directe et transitive :</span><span class="sxs-lookup"><span data-stu-id="a81f9-149">If the app requires direct and transitive group membership claims:</span></span>

* <span data-ttu-id="a81f9-150">Remplacez le `IUserMemberOfCollectionWithReferencesPage` type par `groupsAndAzureRoles` `IUserTransitiveMemberOfCollectionWithReferencesPage` .</span><span class="sxs-lookup"><span data-stu-id="a81f9-150">Change the `IUserMemberOfCollectionWithReferencesPage` type for `groupsAndAzureRoles` to `IUserTransitiveMemberOfCollectionWithReferencesPage`.</span></span>
* <span data-ttu-id="a81f9-151">Lorsque vous demandez les groupes et les rôles de l’utilisateur, remplacez la `MemberOf` propriété par `TransitiveMemberOf` .</span><span class="sxs-lookup"><span data-stu-id="a81f9-151">When requesting the user's groups and roles, replace the `MemberOf` property with `TransitiveMemberOf`.</span></span>

<span data-ttu-id="a81f9-152">Dans `Program.Main` ( `Program.cs` ), configurez l’authentification MSAL pour utiliser la fabrique de comptes d’utilisateur personnalisée : si l’application utilise une classe de compte d’utilisateur personnalisée qui étend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> , échangez la classe de compte d’utilisateur personnalisée pour <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="a81f9-152">In `Program.Main` (`Program.cs`), configure the MSAL authentication to use the custom user account factory: If the app uses a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

...

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

### <a name="use-a-named-httpclient"></a><span data-ttu-id="a81f9-153">Utiliser un nommé `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="a81f9-153">Use a named `HttpClient`</span></span>

::: moniker-end

<span data-ttu-id="a81f9-154">Dans l’application autonome ou l' *`Client`* application d’une solution hébergée Blazor , créez une <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> classe personnalisée.</span><span class="sxs-lookup"><span data-stu-id="a81f9-154">In the standalone app or the *`Client`* app of a hosted Blazor solution, create a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class.</span></span> <span data-ttu-id="a81f9-155">Utilisez l’étendue appropriée pour les appels de API Graph qui obtiennent des informations sur les rôles et les groupes.</span><span class="sxs-lookup"><span data-stu-id="a81f9-155">Use the correct scope for Graph API calls that obtain role and group information.</span></span>

<span data-ttu-id="a81f9-156">`GraphAPIAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="a81f9-156">`GraphAPIAuthorizationMessageHandler.cs`:</span></span>

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

<span data-ttu-id="a81f9-157">Dans `Program.Main` ( `Program.cs` ), ajoutez le <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> service d’implémentation et ajoutez un nommé <xref:System.Net.Http.HttpClient> pour effectuer des demandes de API Graph.</span><span class="sxs-lookup"><span data-stu-id="a81f9-157">In `Program.Main` (`Program.cs`), add the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> implementation service and add a named <xref:System.Net.Http.HttpClient> for making Graph API requests.</span></span> <span data-ttu-id="a81f9-158">L’exemple suivant nomme le client `GraphAPI` :</span><span class="sxs-lookup"><span data-stu-id="a81f9-158">The following example names the client `GraphAPI`:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

<span data-ttu-id="a81f9-159">Créez des classes d’objets d’annuaire AAD pour recevoir les groupes et rôles Open Data Protocol (OData) d’un appel de API Graph.</span><span class="sxs-lookup"><span data-stu-id="a81f9-159">Create AAD directory objects classes to receive the Open Data Protocol (OData) roles and groups from a Graph API call.</span></span> <span data-ttu-id="a81f9-160">OData arrive au format JSON, et un appel à <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> remplit une instance de la `DirectoryObjects` classe.</span><span class="sxs-lookup"><span data-stu-id="a81f9-160">The OData arrives in JSON format, and a call to <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> populates an instance of the `DirectoryObjects` class.</span></span>

<span data-ttu-id="a81f9-161">`DirectoryObjects.cs`:</span><span class="sxs-lookup"><span data-stu-id="a81f9-161">`DirectoryObjects.cs`:</span></span>

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

<span data-ttu-id="a81f9-162">Créez une fabrique d’utilisateurs personnalisée pour traiter les revendications de rôles et de groupes.</span><span class="sxs-lookup"><span data-stu-id="a81f9-162">Create a custom user factory to process roles and groups claims.</span></span> <span data-ttu-id="a81f9-163">L’exemple d’implémentation suivant gère également le `roles` tableau de revendications, qui est abordé dans la section [rôles définis par l’utilisateur](#user-defined-roles) .</span><span class="sxs-lookup"><span data-stu-id="a81f9-163">The following example implementation also handles the `roles` claim array, which is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="a81f9-164">Si la `hasgroups` revendication est présente, le nommé <xref:System.Net.Http.HttpClient> est utilisé pour effectuer une demande autorisée à API Graph pour obtenir les rôles et les groupes de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="a81f9-164">If the `hasgroups` claim is present, the named <xref:System.Net.Http.HttpClient> is used to make an authorized request to Graph API to obtain the user's roles and groups.</span></span> <span data-ttu-id="a81f9-165">Cette implémentation utilise le Identity point de terminaison Microsoft Platform v 1.0 `https://graph.microsoft.com/v1.0/me/memberOf` (documentation de l'[API](/graph/api/user-list-memberof)).</span><span class="sxs-lookup"><span data-stu-id="a81f9-165">This implementation uses the Microsoft Identity Platform v1.0 endpoint `https://graph.microsoft.com/v1.0/me/memberOf` ([API documentation](/graph/api/user-list-memberof)).</span></span>

<span data-ttu-id="a81f9-166">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="a81f9-166">`CustomAccountFactory.cs`:</span></span>

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
                    logger.LogError("Graph API access token failure: {Message}", 
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

<span data-ttu-id="a81f9-167">Il n’est pas nécessaire de fournir du code pour supprimer la revendication d’origine `groups` , le cas échéant, car elle est automatiquement supprimée par l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="a81f9-167">There's no need to provide code to remove the original `groups` claim, if present, because it's automatically removed by the framework.</span></span>

> [!NOTE]
> <span data-ttu-id="a81f9-168">L’approche dans cet exemple :</span><span class="sxs-lookup"><span data-stu-id="a81f9-168">The approach in this example:</span></span>
>
> * <span data-ttu-id="a81f9-169">Ajoute une <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> classe personnalisée pour joindre des jetons d’accès aux demandes sortantes.</span><span class="sxs-lookup"><span data-stu-id="a81f9-169">Adds a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class to attach access tokens to outgoing requests.</span></span>
> * <span data-ttu-id="a81f9-170">Ajoute un nommé <xref:System.Net.Http.HttpClient> pour effectuer des demandes d’API Web à un point de terminaison d’API Web sécurisé et externe.</span><span class="sxs-lookup"><span data-stu-id="a81f9-170">Adds a named <xref:System.Net.Http.HttpClient> for making web API requests to a secure, external web API endpoint.</span></span>
> * <span data-ttu-id="a81f9-171">Utilise le nommé <xref:System.Net.Http.HttpClient> pour effectuer des demandes autorisées.</span><span class="sxs-lookup"><span data-stu-id="a81f9-171">Uses the named <xref:System.Net.Http.HttpClient> to make authorized requests.</span></span>
>
> <span data-ttu-id="a81f9-172">La couverture générale de cette approche est disponible dans l' <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> article.</span><span class="sxs-lookup"><span data-stu-id="a81f9-172">General coverage for this approach is found in the <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> article.</span></span>

<span data-ttu-id="a81f9-173">Inscrire la fabrique dans `Program.Main` ( `Program.cs` ) de l’application autonome ou *`Client`* de l’application d’une solution hébergée Blazor .</span><span class="sxs-lookup"><span data-stu-id="a81f9-173">Register the factory in `Program.Main` (`Program.cs`) of the standalone app or *`Client`* app of a hosted Blazor solution.</span></span> <span data-ttu-id="a81f9-174">Consentement à l' `Directory.Read.All` étendue comme étendue supplémentaire pour l’application :</span><span class="sxs-lookup"><span data-stu-id="a81f9-174">Consent to the `Directory.Read.All` scope as an additional scope for the app:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

...

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

## <a name="authorization-configuration"></a><span data-ttu-id="a81f9-175">Configuration de l’autorisation</span><span class="sxs-lookup"><span data-stu-id="a81f9-175">Authorization configuration</span></span>

<span data-ttu-id="a81f9-176">Créez une [stratégie](xref:security/authorization/policies) pour chaque groupe ou rôle dans `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="a81f9-176">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="a81f9-177">L’exemple suivant crée une stratégie pour le rôle d' *administrateur de facturation* AAD :</span><span class="sxs-lookup"><span data-stu-id="a81f9-177">The following example creates a policy for the AAD *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="a81f9-178">Pour obtenir la liste complète des ID d’objet de rôle AAD, consultez la section [ID d’objet de rôle d’administrateur AAD](#aad-administrator-role-object-ids) .</span><span class="sxs-lookup"><span data-stu-id="a81f9-178">For the complete list of AAD role Object IDs, see the [AAD Administrator Role Object IDs](#aad-administrator-role-object-ids) section.</span></span>

<span data-ttu-id="a81f9-179">Dans les exemples suivants, l’application utilise la stratégie précédente pour autoriser l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="a81f9-179">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="a81f9-180">Le [ `AuthorizeView` composant](xref:blazor/security/index#authorizeview-component) fonctionne avec la stratégie :</span><span class="sxs-lookup"><span data-stu-id="a81f9-180">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

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

<span data-ttu-id="a81f9-181">L’accès à un composant entier peut être basé sur la stratégie à l’aide de la [ `[Authorize]` directive d’attribut](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) :</span><span class="sxs-lookup"><span data-stu-id="a81f9-181">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="a81f9-182">Si l’utilisateur n’est pas connecté, il est redirigé vers la page de connexion AAD, puis renvoyé au composant une fois qu’il se connecte.</span><span class="sxs-lookup"><span data-stu-id="a81f9-182">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="a81f9-183">Une vérification de stratégie peut également être [effectuée dans le code avec la logique procédurale](xref:blazor/security/index#procedural-logic):</span><span class="sxs-lookup"><span data-stu-id="a81f9-183">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic):</span></span>

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

## <a name="authorize-server-api-access-for-user-defined-groups-and-administrator-roles"></a><span data-ttu-id="a81f9-184">Autoriser l’accès à l’API serveur pour les groupes définis par l’utilisateur et les rôles d’administrateur</span><span class="sxs-lookup"><span data-stu-id="a81f9-184">Authorize server API access for user-defined groups and Administrator Roles</span></span>

<span data-ttu-id="a81f9-185">Outre l’autorisation des utilisateurs dans l’application webassembly côté client pour accéder aux pages et aux ressources, l’API serveur peut autoriser les utilisateurs à accéder aux points de terminaison d’API sécurisés.</span><span class="sxs-lookup"><span data-stu-id="a81f9-185">In addition to authorizing users in the client-side WebAssembly app to access pages and resources, the server API can authorize users for access to secure API endpoints.</span></span> <span data-ttu-id="a81f9-186">Une fois que l’application *serveur* a validé le jeton d’accès de l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="a81f9-186">After the *Server* app validates the user's access token:</span></span>

* <span data-ttu-id="a81f9-187">L’application API serveur utilise la revendication d’identificateur d’objet immuable de l’utilisateur [( `oid` )](/azure/active-directory/develop/id-tokens#payload-claims) à partir de son jeton d’accès pour obtenir un jeton d’accès pour API Graph.</span><span class="sxs-lookup"><span data-stu-id="a81f9-187">The server API app uses the user's immutable [object identifier claim (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) from their access token to obtain an access token for Graph API.</span></span>
* <span data-ttu-id="a81f9-188">Un appel de API Graph obtient le groupe de sécurité défini par l’utilisateur Azure et les appartenances de rôle d’administrateur en appelant [`memberOf`](/graph/api/user-list-memberof) l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="a81f9-188">A Graph API call obtains the user's Azure user-defined security group and Administrator Role memberships by calling [`memberOf`](/graph/api/user-list-memberof) on the user.</span></span>
* <span data-ttu-id="a81f9-189">Les appartenances sont utilisées pour établir des `group` revendications.</span><span class="sxs-lookup"><span data-stu-id="a81f9-189">Memberships are used to establish `group` claims.</span></span>
* <span data-ttu-id="a81f9-190">Les [stratégies d’autorisation](xref:security/authorization/policies) peuvent être utilisées pour limiter l’accès utilisateur aux points de terminaison de l’API serveur au sein de l’application.</span><span class="sxs-lookup"><span data-stu-id="a81f9-190">[Authorization policies](xref:security/authorization/policies) can be used to limit user access to server API endpoints throughout the app.</span></span>

> [!NOTE]
> <span data-ttu-id="a81f9-191">Ce guide n’inclut pas actuellement l’autorisation des utilisateurs sur la base de leurs [rôles AAD définis par l’utilisateur](#user-defined-roles).</span><span class="sxs-lookup"><span data-stu-id="a81f9-191">This guidance doesn't currently include authorizing users on the basis of their [AAD user-defined roles](#user-defined-roles).</span></span>

<span data-ttu-id="a81f9-192">Les instructions de cette section configurent l’application API serveur en tant qu' [*application démon*](/azure/active-directory/develop/scenario-daemon-overview) pour l’appel d’API Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="a81f9-192">The guidance in this section configures the server API app as a [*daemon app*](/azure/active-directory/develop/scenario-daemon-overview) for the Microsoft Graph API call.</span></span> <span data-ttu-id="a81f9-193">Cette approche n’effectue **pas** les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="a81f9-193">This approach does **not**:</span></span>

* <span data-ttu-id="a81f9-194">Exiger la `access_as_user` portée.</span><span class="sxs-lookup"><span data-stu-id="a81f9-194">Require the the `access_as_user` scope.</span></span>
* <span data-ttu-id="a81f9-195">Accédez API Graph pour le compte de l’utilisateur/client qui effectue la demande d’API.</span><span class="sxs-lookup"><span data-stu-id="a81f9-195">Access Graph API on behalf of the user/client making the API request.</span></span>

<span data-ttu-id="a81f9-196">L’appel à API Graph par l’application API serveur nécessite uniquement une **application** API de serveur API Graph étendue pour `Directory.Read.All` dans le portail Azure.</span><span class="sxs-lookup"><span data-stu-id="a81f9-196">The call to Graph API by the server API app only requires a server API app **Application** Graph API scope for `Directory.Read.All` in the Azure portal.</span></span> <span data-ttu-id="a81f9-197">Cette approche empêche absolument l’application cliente d’accéder aux données d’annuaire que l’API serveur n’autorise pas explicitement.</span><span class="sxs-lookup"><span data-stu-id="a81f9-197">This approach absolutely prevents the client app from accessing directory data that the server API doesn't explicitly permit.</span></span> <span data-ttu-id="a81f9-198">L’application cliente peut uniquement accéder aux points de terminaison de contrôleur de l’application API serveur.</span><span class="sxs-lookup"><span data-stu-id="a81f9-198">The client app is only able to access the server API app's controller endpoints.</span></span>

### <a name="azure-configuration"></a><span data-ttu-id="a81f9-199">Configuration Azure</span><span class="sxs-lookup"><span data-stu-id="a81f9-199">Azure configuration</span></span>

* <span data-ttu-id="a81f9-200">Vérifiez que l’inscription de l’application *serveur* est donnée à l' **application** (non **déléguée**) API Graph pour `Directory.Read.All` , qui est le niveau d’accès le moins privilégié pour les groupes de sécurité.</span><span class="sxs-lookup"><span data-stu-id="a81f9-200">Confirm that the *Server* app registration is given **Application** (not **Delegated**) Graph API scope for `Directory.Read.All`, which is the least-privileged access level for security groups.</span></span> <span data-ttu-id="a81f9-201">Confirmez que le consentement de l’administrateur est appliqué à l’étendue après avoir effectué l’attribution de l’étendue.</span><span class="sxs-lookup"><span data-stu-id="a81f9-201">Confirm that admin consent is applied to the scope after making the scope assignment.</span></span>
* <span data-ttu-id="a81f9-202">Attribuez une nouvelle clé secrète client à l’application *serveur* .</span><span class="sxs-lookup"><span data-stu-id="a81f9-202">Assign a new client secret to the *Server* app.</span></span> <span data-ttu-id="a81f9-203">Notez le secret de la configuration de l’application dans la section paramètres de l' [application](#app-settings) .</span><span class="sxs-lookup"><span data-stu-id="a81f9-203">Note the secret for the app's configuration in the [App settings](#app-settings) section.</span></span>

### <a name="app-settings"></a><span data-ttu-id="a81f9-204">Paramètres de l’application</span><span class="sxs-lookup"><span data-stu-id="a81f9-204">App settings</span></span>

<span data-ttu-id="a81f9-205">Dans le fichier de paramètres d’application ( `appsettings.json` ou `appsettings.Production.json` ), créez une `ClientSecret` entrée avec la clé secrète client de l’application *serveur* à partir de la portail Azure :</span><span class="sxs-lookup"><span data-stu-id="a81f9-205">In the app settings file (`appsettings.json` or `appsettings.Production.json`), create a `ClientSecret` entry with the *Server* app's client secret from the Azure portal:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "XXXXXXXXXXXX.onmicrosoft.com",
  "TenantId": "{GUID}",
  "ClientId": "{GUID}",
  "ClientSecret": "{CLIENT SECRET}"
},
```

<span data-ttu-id="a81f9-206">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a81f9-206">For example:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "contoso.onmicrosoft.com",
  "TenantId": "34bf0ec1-7aeb-4b5d-ba42-82b059b3abe8",
  "ClientId": "05d198e0-38c6-4efc-a67c-8ee87ed9bd3d",
  "ClientSecret": "54uE~9a.-wW91fe8cRR25ag~-I5gEq_92~"
},
```

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="a81f9-207">Si le domaine de l’éditeur du locataire n’est pas vérifié, l’étendue de l’API serveur pour l’accès utilisateur/client utilise un `https://` URI basé sur.</span><span class="sxs-lookup"><span data-stu-id="a81f9-207">If the tenant publisher domain isn't verified, the server API scope for user/client access uses an `https://`-based URI.</span></span> <span data-ttu-id="a81f9-208">Dans ce scénario, l’application API serveur requiert une `Audience` configuration dans le `appsettings.json` fichier.</span><span class="sxs-lookup"><span data-stu-id="a81f9-208">In this scenario, the server API app requires `Audience` configuration in the `appsettings.json` file.</span></span> <span data-ttu-id="a81f9-209">Dans la configuration suivante, la fin de la `Audience` valeur n’inclut **pas** l’étendue par défaut `/{DEFAULT SCOPE}` , où l’espace réservé `{DEFAULT SCOPE}` est l’étendue par défaut :</span><span class="sxs-lookup"><span data-stu-id="a81f9-209">In the following configuration, the end of the `Audience` value does **not** include the default scope `/{DEFAULT SCOPE}`, where the placeholder `{DEFAULT SCOPE}` is the default scope:</span></span>
>
> ```json
> {
>   "AzureAd": {
>     ...
>
>     "Audience": "https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}"
>   }
> }
>
> In the preceding configuration, the placeholder `{TENANT}` is the app's tenant, and the placeholder `{SERVER API APP CLIENT ID OR CUSTOM VALUE}` is the server API app's `ClientId` or custom value provided in the Azure portal's app registration.
>
> Example:
>
> ```json
> {
>   "AzureAd": {
>     ...
>
>     "Audience": "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd"
>   }
> }
> ```
>
> <span data-ttu-id="a81f9-210">Dans l’exemple de configuration précédent :</span><span class="sxs-lookup"><span data-stu-id="a81f9-210">In the preceding example configuration:</span></span>
>
> * <span data-ttu-id="a81f9-211">Le domaine du locataire est `contoso.onmicrosoft.com` .</span><span class="sxs-lookup"><span data-stu-id="a81f9-211">The tenant domain is `contoso.onmicrosoft.com`.</span></span>
> * <span data-ttu-id="a81f9-212">L’application API serveur `ClientId` est `41451fa7-82d9-4673-8fa5-69eff5a761fd` .</span><span class="sxs-lookup"><span data-stu-id="a81f9-212">The server API app `ClientId` is `41451fa7-82d9-4673-8fa5-69eff5a761fd`.</span></span>
>
> > [!NOTE]
> > <span data-ttu-id="a81f9-213">La configuration d’une en `Audience` général n’est **pas** obligatoire pour les applications avec un domaine d’éditeur vérifié qui a une `api://` étendue d’API basée sur.</span><span class="sxs-lookup"><span data-stu-id="a81f9-213">Configuring an `Audience` explicitly usually is **not** required for app's with a verified publisher domain that has an `api://`-based API scope.</span></span>
>
> <span data-ttu-id="a81f9-214">Pour plus d’informations, consultez <xref:blazor/security/webassembly/hosted-with-azure-active-directory#app-settings>.</span><span class="sxs-lookup"><span data-stu-id="a81f9-214">For more information, see <xref:blazor/security/webassembly/hosted-with-azure-active-directory#app-settings>.</span></span>

::: moniker-end

### <a name="authorization-policies"></a><span data-ttu-id="a81f9-215">Vous pouvez également utiliser les stratégies d'autorisation</span><span class="sxs-lookup"><span data-stu-id="a81f9-215">Authorization policies</span></span>

<span data-ttu-id="a81f9-216">Créer des [stratégies d’autorisation](xref:security/authorization/policies) pour les groupes de sécurité AAD et les rôles d’administrateur AAD dans les applications *serveur* `Startup.ConfigureServices` ( `Startup.cs` ) en fonction des ID d’objet de groupe et des [ID d’objet de rôle d’administrateur AAD](#aad-administrator-role-object-ids).</span><span class="sxs-lookup"><span data-stu-id="a81f9-216">Create [authorization policies](xref:security/authorization/policies) for AAD security groups and AAD Administrator Roles in the *Server* app's `Startup.ConfigureServices` (`Startup.cs`) based on group object IDs and [AAD Administrator Role object IDs](#aad-administrator-role-object-ids).</span></span>

<span data-ttu-id="a81f9-217">Par exemple, une stratégie de rôle d’administrateur de facturation Azure a la configuration suivante :</span><span class="sxs-lookup"><span data-stu-id="a81f9-217">For example, an Azure Billing Administrator Role policy has the following configuration:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdmin", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="a81f9-218">Pour plus d’informations, consultez <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="a81f9-218">For more information, see <xref:security/authorization/policies>.</span></span>

### <a name="controller-access"></a><span data-ttu-id="a81f9-219">Accès du contrôleur</span><span class="sxs-lookup"><span data-stu-id="a81f9-219">Controller access</span></span>

<span data-ttu-id="a81f9-220">Exiger des stratégies sur les contrôleurs de l’application *serveur* .</span><span class="sxs-lookup"><span data-stu-id="a81f9-220">Require policies on the *Server* app's controllers.</span></span>

<span data-ttu-id="a81f9-221">L’exemple suivant limite l’accès aux données de facturation du `BillingDataController` à des administrateurs de facturation Azure avec le nom de stratégie `BillingAdmin` , tel que configuré dans la section [stratégies d’autorisation](#authorization-policies) :</span><span class="sxs-lookup"><span data-stu-id="a81f9-221">The following example limits access to billing data from the `BillingDataController` to Azure Billing Administrators with a policy name of `BillingAdmin`, as configured in the [Authorization policies](#authorization-policies) section:</span></span>

```csharp
[Authorize(Policy = "BillingAdmin")]
[ApiController]
[Route("[controller]")]
public class BillingDataController : ControllerBase
{
    ...
}
```

::: moniker range=">= aspnetcore-5.0"

### <a name="packages"></a><span data-ttu-id="a81f9-222">Paquets</span><span class="sxs-lookup"><span data-stu-id="a81f9-222">Packages</span></span>

<span data-ttu-id="a81f9-223">Ajoutez des références de package à l’application *serveur* pour les packages suivants :</span><span class="sxs-lookup"><span data-stu-id="a81f9-223">Add package references to the *Server* app for the following packages:</span></span>

* [<span data-ttu-id="a81f9-224">Microsoft.Graph</span><span class="sxs-lookup"><span data-stu-id="a81f9-224">Microsoft.Graph</span></span>](https://www.nuget.org/packages/Microsoft.Graph)
* <span data-ttu-id="a81f9-225">[Microsoft. Identity . Client](https://www.nuget.org/packages/Microsoft.Identity.Client)</span><span class="sxs-lookup"><span data-stu-id="a81f9-225">[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)</span></span>

### <a name="services"></a><span data-ttu-id="a81f9-226">Services</span><span class="sxs-lookup"><span data-stu-id="a81f9-226">Services</span></span>

<span data-ttu-id="a81f9-227">Dans la méthode de l’application *serveur* `Startup.ConfigureServices` , des espaces de noms supplémentaires sont requis pour le code de la `Startup` classe de l’application *serveur* .</span><span class="sxs-lookup"><span data-stu-id="a81f9-227">In the *Server* app's `Startup.ConfigureServices` method, additional namespaces are required for the code in the `Startup` class of the *Server* app.</span></span> <span data-ttu-id="a81f9-228">Ajoutez les espaces de noms suivants à `Startup.cs` :</span><span class="sxs-lookup"><span data-stu-id="a81f9-228">Add the following namespaces to `Startup.cs`:</span></span>

```csharp
using System;
using System.IdentityModel.Tokens.Jwt;
using System.Net.Http.Headers;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.Graph;
using Microsoft.Identity.Client;
using Microsoft.IdentityModel.Logging;
```

<span data-ttu-id="a81f9-229">Lors de la configuration <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents> :</span><span class="sxs-lookup"><span data-stu-id="a81f9-229">When configuring <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span></span>

* <span data-ttu-id="a81f9-230">Incluez éventuellement le traitement de <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="a81f9-230">Optionally include processing for <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span></span> <span data-ttu-id="a81f9-231">Par exemple, l’application peut consigner l’échec de l’authentification.</span><span class="sxs-lookup"><span data-stu-id="a81f9-231">For example, the app can log failed authentication.</span></span>
* <span data-ttu-id="a81f9-232">Dans <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType> , effectuez un appel API Graph pour obtenir les groupes et les rôles de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="a81f9-232">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>, make a Graph API call to obtain the user's groups and roles.</span></span>

> [!WARNING]
> <span data-ttu-id="a81f9-233"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> fournit des informations d’identification personnelle (PII) dans la journalisation des messages.</span><span class="sxs-lookup"><span data-stu-id="a81f9-233"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> provides Personally Identifiable Information (PII) in logging messages.</span></span> <span data-ttu-id="a81f9-234">Activez uniquement les PII pour le débogage avec les comptes d’utilisateur de test.</span><span class="sxs-lookup"><span data-stu-id="a81f9-234">Only activate PII for debugging with test user accounts.</span></span>

<span data-ttu-id="a81f9-235">Dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a81f9-235">In `Startup.ConfigureServices`:</span></span>

```csharp
JwtSecurityTokenHandler.DefaultMapInboundClaims = false;

#if DEBUG
IdentityModelEventSource.ShowPII = true;
#endif

var scopes = new string[] { "https://graph.microsoft.com/.default" };

var app = ConfidentialClientApplicationBuilder.Create(Configuration["AzureAd:ClientId"])
   .WithClientSecret(Configuration["AzureAd:ClientSecret"])
   .WithAuthority(new Uri(Configuration["AzureAd:Instance"] + Configuration["AzureAd:Domain"]))
   .Build();

services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(options =>
{
    Configuration.Bind("AzureAd", options);

    options.Events = new JwtBearerEvents()
    {
        OnTokenValidated = async context =>
        {
            var accessToken = context.SecurityToken as JwtSecurityToken;

            var oid = accessToken.Claims.FirstOrDefault(x => x.Type == "oid")?
                .Value;

            if (!string.IsNullOrEmpty(oid))
            {
                var userIdentity = (ClaimsIdentity)context.Principal.Identity;

                AuthenticationResult authResult = null;

                try
                {
                    authResult = await app.AcquireTokenForClient(scopes)
                        .ExecuteAsync();
                }
                catch (MsalUiRequiredException ex)
                {
                    // Optional: Log the exception
                }
                catch (MsalServiceException ex)
                {
                    // Optional: Log the exception
                }

                var graphClient = new GraphServiceClient(
                    new DelegateAuthenticationProvider(async requestMessage => {
                        requestMessage.Headers.Authorization =
                            new AuthenticationHeaderValue("Bearer", authResult.AccessToken);

                        await Task.CompletedTask;
                    }));

                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request()
                        .GetAsync();
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the exception
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }
            }

            await Task.FromResult(0);
        }
    };
}, 
options =>
{
    Configuration.Bind("AzureAd", options);
});
```

<span data-ttu-id="a81f9-236">Dans le code précédent, la gestion des erreurs de jeton suivantes est facultative :</span><span class="sxs-lookup"><span data-stu-id="a81f9-236">In the preceding code, handling the following token errors is optional:</span></span>

* <span data-ttu-id="a81f9-237">`MsalUiRequiredException`: L’application n’a pas les autorisations suffisantes (étendues).</span><span class="sxs-lookup"><span data-stu-id="a81f9-237">`MsalUiRequiredException`: The app doesn't have sufficient permissions (scopes).</span></span>
  * <span data-ttu-id="a81f9-238">Déterminez si les étendues de l’application API serveur dans le Portail Azure incluent une autorisation d' **application** pour `Directory.Read.All` .</span><span class="sxs-lookup"><span data-stu-id="a81f9-238">Determine if the server API app scopes in the Azure portal include an **Application** permission for `Directory.Read.All`.</span></span>
  * <span data-ttu-id="a81f9-239">Confirmez que l’administrateur client a accordé des autorisations à l’application.</span><span class="sxs-lookup"><span data-stu-id="a81f9-239">Confirm that the tenant administrator has granted permissions to the app.</span></span>
* <span data-ttu-id="a81f9-240">`MsalServiceException` ( `AADSTS70011` ) : Confirmez que l’étendue est `https://graph.microsoft.com/.default` .</span><span class="sxs-lookup"><span data-stu-id="a81f9-240">`MsalServiceException` (`AADSTS70011`): Confirm that the scope is `https://graph.microsoft.com/.default`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="packages"></a><span data-ttu-id="a81f9-241">Paquets</span><span class="sxs-lookup"><span data-stu-id="a81f9-241">Packages</span></span>

<span data-ttu-id="a81f9-242">Ajoutez des références de package à l’application *serveur* pour les packages suivants :</span><span class="sxs-lookup"><span data-stu-id="a81f9-242">Add package references to the *Server* app for the following packages:</span></span>

* [<span data-ttu-id="a81f9-243">Microsoft.Graph</span><span class="sxs-lookup"><span data-stu-id="a81f9-243">Microsoft.Graph</span></span>](https://www.nuget.org/packages/Microsoft.Graph)
* <span data-ttu-id="a81f9-244">[Microsoft. Identity Model. clients. ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)</span><span class="sxs-lookup"><span data-stu-id="a81f9-244">[Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)</span></span>

### <a name="service-configuration"></a><span data-ttu-id="a81f9-245">Configuration de service</span><span class="sxs-lookup"><span data-stu-id="a81f9-245">Service configuration</span></span>

<span data-ttu-id="a81f9-246">Dans la méthode de l’application *serveur* , `Startup.ConfigureServices` Ajoutez une logique pour que les API Graph appellent et établissent `group` des revendications d’utilisateur pour les groupes de sécurité et les rôles de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="a81f9-246">In the *Server* app's `Startup.ConfigureServices` method add logic to make the Graph API call and establish user `group` claims for the user's security groups and roles.</span></span>

> [!NOTE]
> <span data-ttu-id="a81f9-247">L’exemple de code dans cette section utilise le Bibliothèque d’authentification Active Directory (ADAL), qui est basé sur Microsoft Identity Platform v 1.0.</span><span class="sxs-lookup"><span data-stu-id="a81f9-247">The example code in this section uses the Active Directory Authentication Library (ADAL), which is based on Microsoft Identity Platform v1.0.</span></span>

<span data-ttu-id="a81f9-248">Des espaces de noms supplémentaires sont requis pour le code de la `Startup` classe de l’application *serveur* .</span><span class="sxs-lookup"><span data-stu-id="a81f9-248">Additional namespaces are required for the code in the `Startup` class of the *Server* app.</span></span> <span data-ttu-id="a81f9-249">L’ensemble d' `using` instructions suivant contient les espaces de noms requis pour le code qui suit dans cette section :</span><span class="sxs-lookup"><span data-stu-id="a81f9-249">The following set of `using` statements includes the required namespaces for the code that follows in this section:</span></span>

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

<span data-ttu-id="a81f9-250">Lors de la configuration <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents> :</span><span class="sxs-lookup"><span data-stu-id="a81f9-250">When configuring <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span></span>

* <span data-ttu-id="a81f9-251">Incluez éventuellement le traitement de <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="a81f9-251">Optionally include processing for <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span></span> <span data-ttu-id="a81f9-252">Par exemple, l’application peut consigner l’échec de l’authentification.</span><span class="sxs-lookup"><span data-stu-id="a81f9-252">For example, the app can log failed authentication.</span></span>
* <span data-ttu-id="a81f9-253">Dans <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType> , effectuez un appel API Graph pour obtenir les groupes et les rôles de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="a81f9-253">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>, make a Graph API call to obtain the user's groups and roles.</span></span>

> [!WARNING]
> <span data-ttu-id="a81f9-254"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> fournit des informations d’identification personnelle (PII) dans la journalisation des messages.</span><span class="sxs-lookup"><span data-stu-id="a81f9-254"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> provides Personally Identifiable Information (PII) in logging messages.</span></span> <span data-ttu-id="a81f9-255">Activez uniquement les PII pour le débogage avec les comptes d’utilisateur de test.</span><span class="sxs-lookup"><span data-stu-id="a81f9-255">Only activate PII for debugging with test user accounts.</span></span>

<span data-ttu-id="a81f9-256">Dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a81f9-256">In `Startup.ConfigureServices`:</span></span>

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
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }
            }

            await Task.FromResult(0);
        }
    };
});
```

<span data-ttu-id="a81f9-257">Dans l’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="a81f9-257">In the preceding example:</span></span>

* <span data-ttu-id="a81f9-258">L’acquisition du jeton silencieux ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A> ) est tentée en premier, car le jeton d’accès a peut-être déjà été stocké dans le cache de jetons Adal.</span><span class="sxs-lookup"><span data-stu-id="a81f9-258">Silent token acquisition (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>) is attempted first because the access token may have already been stored in the ADAL token cache.</span></span> <span data-ttu-id="a81f9-259">Il est plus rapide d’obtenir le jeton à partir du cache que de demander un nouveau jeton.</span><span class="sxs-lookup"><span data-stu-id="a81f9-259">It's faster to obtain the token from cache than to request a new token.</span></span>
* <span data-ttu-id="a81f9-260">Si le jeton d’accès n’est pas acquis à partir du cache ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> ou <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> est levé), une assertion utilisateur ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion> ) est effectuée avec les informations d’identification du client ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential> ) pour obtenir le jeton pour le compte de l’utilisateur ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A> ).</span><span class="sxs-lookup"><span data-stu-id="a81f9-260">If the access token isn't acquired from cache (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> or <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> is thrown), a user assertion (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion>) is made with the client credential (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential>) to obtain the token on behalf of the user (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A>).</span></span> <span data-ttu-id="a81f9-261">Ensuite, le `Microsoft.Graph.GraphServiceClient` peut continuer à utiliser le jeton pour effectuer l’appel de API Graph.</span><span class="sxs-lookup"><span data-stu-id="a81f9-261">Next, the `Microsoft.Graph.GraphServiceClient` can proceed to use the token to make the Graph API call.</span></span> <span data-ttu-id="a81f9-262">Le jeton est placé dans le cache de jetons ADAL.</span><span class="sxs-lookup"><span data-stu-id="a81f9-262">The token is placed into the ADAL token cache.</span></span> <span data-ttu-id="a81f9-263">Pour les appels de API Graph ultérieurs pour le même utilisateur, le jeton est acquis à partir du cache en mode silencieux avec <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="a81f9-263">For future Graph API calls for the same user, the token is acquired from cache silently with <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>.</span></span>

::: moniker-end

<span data-ttu-id="a81f9-264">Le code de <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> n’obtient pas les appartenances transitives.</span><span class="sxs-lookup"><span data-stu-id="a81f9-264">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't obtain transitive memberships.</span></span> <span data-ttu-id="a81f9-265">Pour modifier le code afin d’obtenir les appartenances directes et transitives :</span><span class="sxs-lookup"><span data-stu-id="a81f9-265">To change the code to obtain direct and transitive memberships:</span></span>

* <span data-ttu-id="a81f9-266">Pour la ligne de code :</span><span class="sxs-lookup"><span data-stu-id="a81f9-266">For the code line:</span></span>

  ```csharp
  IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

  <span data-ttu-id="a81f9-267">Remplacez la ligne précédente par :</span><span class="sxs-lookup"><span data-stu-id="a81f9-267">Replace the preceding line with:</span></span>

  ```csharp
  IUserTransitiveMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

* <span data-ttu-id="a81f9-268">Pour la ligne de code :</span><span class="sxs-lookup"><span data-stu-id="a81f9-268">For the code line:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request().GetAsync();
  ```

  <span data-ttu-id="a81f9-269">Remplacez la ligne précédente par :</span><span class="sxs-lookup"><span data-stu-id="a81f9-269">Replace the preceding line with:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].TransitiveMemberOf.Request()
      .GetAsync();
  ```

<span data-ttu-id="a81f9-270">Le code dans <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> ne fait pas la distinction entre les groupes de sécurité AAD et les rôles d’administrateur AAD lors de la création de revendications.</span><span class="sxs-lookup"><span data-stu-id="a81f9-270">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't distinguish between AAD security groups and AAD Administrator Roles when creating claims.</span></span> <span data-ttu-id="a81f9-271">Pour que l’application fasse la distinction entre les groupes et les rôles, activez la case à cocher lors de l' `entry.ODataType` itération des groupes et des rôles.</span><span class="sxs-lookup"><span data-stu-id="a81f9-271">For the app to distinguish between groups and roles, check the `entry.ODataType` when iterating through the groups and roles.</span></span> <span data-ttu-id="a81f9-272">Pour créer des revendications de rôle et de groupe de sécurité distinctes, utilisez un code similaire à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="a81f9-272">To create separate security group and role claims, use code similar to the following:</span></span>

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

## <a name="user-defined-roles"></a><span data-ttu-id="a81f9-273">Rôles définis par l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="a81f9-273">User-defined roles</span></span>

<span data-ttu-id="a81f9-274">Une application inscrite à AAD peut également être configurée pour utiliser des rôles définis par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="a81f9-274">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="a81f9-275">Pour configurer l’application dans le Portail Azure pour fournir une `roles` revendication d’appartenance, consultez [Comment : ajouter des rôles d’application dans votre application et les recevoir dans le jeton](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) de la documentation Azure.</span><span class="sxs-lookup"><span data-stu-id="a81f9-275">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="a81f9-276">L’exemple suivant suppose qu’une application est configurée avec deux rôles :</span><span class="sxs-lookup"><span data-stu-id="a81f9-276">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="a81f9-277">Bien que vous ne puissiez pas attribuer des rôles à des groupes de sécurité sans compte Azure AD Premium, vous pouvez affecter des utilisateurs à des rôles et recevoir une `roles` revendication pour les utilisateurs disposant d’un compte Azure standard.</span><span class="sxs-lookup"><span data-stu-id="a81f9-277">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="a81f9-278">Les instructions de cette section ne nécessitent pas de compte Azure AD Premium.</span><span class="sxs-lookup"><span data-stu-id="a81f9-278">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="a81f9-279">Plusieurs rôles sont affectés dans le Portail Azure en **_rajoutant un utilisateur_** pour chaque attribution de rôle supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="a81f9-279">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="a81f9-280">La `roles` revendication unique envoyée par AAD présente les rôles définis par l’utilisateur en tant que `appRoles` `value` s dans un tableau JSON.</span><span class="sxs-lookup"><span data-stu-id="a81f9-280">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="a81f9-281">L’application doit convertir le tableau JSON de rôles en `role` revendications individuelles.</span><span class="sxs-lookup"><span data-stu-id="a81f9-281">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="a81f9-282">La `CustomUserFactory` section des [groupes définis par l’utilisateur et des rôles d’administrateur AAD](#user-defined-groups-and-administrator-roles) est configurée pour agir sur une `roles` revendication avec une valeur de tableau JSON.</span><span class="sxs-lookup"><span data-stu-id="a81f9-282">The `CustomUserFactory` shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="a81f9-283">Ajoutez et inscrivez `CustomUserFactory` dans l’application autonome ou dans l' *`Client`* application d’une Blazor solution hébergée, comme indiqué dans la section [groupes définis par l’utilisateur et rôles d’administrateur AAD](#user-defined-groups-and-administrator-roles) .</span><span class="sxs-lookup"><span data-stu-id="a81f9-283">Add and register the `CustomUserFactory` in the standalone app or *`Client`* app of a hosted Blazor solution as shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span> <span data-ttu-id="a81f9-284">Il n’est pas nécessaire de fournir du code pour supprimer la revendication d’origine `roles` , car elle est automatiquement supprimée par l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="a81f9-284">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="a81f9-285">Dans `Program.Main` l’application autonome ou l' *`Client`* application d’une solution hébergée Blazor , spécifiez la revendication nommée « `role` » comme revendication de rôle :</span><span class="sxs-lookup"><span data-stu-id="a81f9-285">In `Program.Main` of the standalone app or *`Client`* app of a hosted Blazor solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="a81f9-286">Les approches d’autorisation des composants sont fonctionnelles à ce stade.</span><span class="sxs-lookup"><span data-stu-id="a81f9-286">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="a81f9-287">L’un des mécanismes d’autorisation des composants peut utiliser le `admin` rôle pour autoriser l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="a81f9-287">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="a81f9-288">[ `AuthorizeView` composant](xref:blazor/security/index#authorizeview-component) (exemple : `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="a81f9-288">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="a81f9-289">[ `[Authorize]` directive d’attribut](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (exemple : `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="a81f9-289">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="a81f9-290">[Logique procédurale](xref:blazor/security/index#procedural-logic) (exemple : `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="a81f9-290">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="a81f9-291">Plusieurs tests de rôle sont pris en charge :</span><span class="sxs-lookup"><span data-stu-id="a81f9-291">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-administrator-role-object-ids"></a><span data-ttu-id="a81f9-292">ID d’objet du rôle administrateur AAD</span><span class="sxs-lookup"><span data-stu-id="a81f9-292">AAD Administrator Role Object IDs</span></span>

<span data-ttu-id="a81f9-293">Les ID d’objet présentés dans le tableau suivant sont utilisés pour créer des [stratégies](xref:security/authorization/policies) pour les `group` revendications.</span><span class="sxs-lookup"><span data-stu-id="a81f9-293">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="a81f9-294">Les stratégies permettent à une application d’autoriser les utilisateurs à effectuer différentes activités dans une application.</span><span class="sxs-lookup"><span data-stu-id="a81f9-294">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="a81f9-295">Pour plus d’informations, consultez la section [groupes définis par l’utilisateur et rôles d’administrateur AAD](#user-defined-groups-and-administrator-roles) .</span><span class="sxs-lookup"><span data-stu-id="a81f9-295">For more information, see the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span>

<span data-ttu-id="a81f9-296">Rôle administrateur AAD</span><span class="sxs-lookup"><span data-stu-id="a81f9-296">AAD Administrator Role</span></span> | <span data-ttu-id="a81f9-297">ID de l'objet</span><span class="sxs-lookup"><span data-stu-id="a81f9-297">Object ID</span></span>
--- | ---
<span data-ttu-id="a81f9-298">Administrateur d’application</span><span class="sxs-lookup"><span data-stu-id="a81f9-298">Application administrator</span></span> | <span data-ttu-id="a81f9-299">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="a81f9-299">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="a81f9-300">Développeur d’applications</span><span class="sxs-lookup"><span data-stu-id="a81f9-300">Application developer</span></span> | <span data-ttu-id="a81f9-301">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="a81f9-301">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="a81f9-302">Administrateur d’authentification</span><span class="sxs-lookup"><span data-stu-id="a81f9-302">Authentication administrator</span></span> | <span data-ttu-id="a81f9-303">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="a81f9-303">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="a81f9-304">Administrateur Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="a81f9-304">Azure DevOps administrator</span></span> | <span data-ttu-id="a81f9-305">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="a81f9-305">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="a81f9-306">Administrateur Azure Information Protection</span><span class="sxs-lookup"><span data-stu-id="a81f9-306">Azure Information Protection administrator</span></span> | <span data-ttu-id="a81f9-307">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="a81f9-307">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="a81f9-308">Administrateur de jeux de clés IEF B2C</span><span class="sxs-lookup"><span data-stu-id="a81f9-308">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="a81f9-309">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="a81f9-309">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="a81f9-310">Administrateur de la stratégie B2C IEF</span><span class="sxs-lookup"><span data-stu-id="a81f9-310">B2C IEF Policy administrator</span></span> | <span data-ttu-id="a81f9-311">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="a81f9-311">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="a81f9-312">Administrateur de workflow utilisateur B2C</span><span class="sxs-lookup"><span data-stu-id="a81f9-312">B2C user flow administrator</span></span> | <span data-ttu-id="a81f9-313">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="a81f9-313">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="a81f9-314">Administrateur de l’attribut de workflow de l’utilisateur B2C</span><span class="sxs-lookup"><span data-stu-id="a81f9-314">B2C user flow attribute administrator</span></span> | <span data-ttu-id="a81f9-315">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="a81f9-315">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="a81f9-316">Administrateur de facturation</span><span class="sxs-lookup"><span data-stu-id="a81f9-316">Billing administrator</span></span> | <span data-ttu-id="a81f9-317">69ff516a-B57D-4697-A429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="a81f9-317">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="a81f9-318">Administrateur d’application cloud</span><span class="sxs-lookup"><span data-stu-id="a81f9-318">Cloud application administrator</span></span> | <span data-ttu-id="a81f9-319">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="a81f9-319">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="a81f9-320">Administrateur d’appareil cloud</span><span class="sxs-lookup"><span data-stu-id="a81f9-320">Cloud device administrator</span></span> | <span data-ttu-id="a81f9-321">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="a81f9-321">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="a81f9-322">Administrateur de conformité</span><span class="sxs-lookup"><span data-stu-id="a81f9-322">Compliance administrator</span></span> | <span data-ttu-id="a81f9-323">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="a81f9-323">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="a81f9-324">Administrateur des données de conformité</span><span class="sxs-lookup"><span data-stu-id="a81f9-324">Compliance data administrator</span></span> | <span data-ttu-id="a81f9-325">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="a81f9-325">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="a81f9-326">Administrateur de l’accès conditionnel</span><span class="sxs-lookup"><span data-stu-id="a81f9-326">Conditional Access administrator</span></span> | <span data-ttu-id="a81f9-327">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="a81f9-327">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="a81f9-328">Approbateur d’accès au référentiel sécurisé client</span><span class="sxs-lookup"><span data-stu-id="a81f9-328">Customer LockBox access approver</span></span> | <span data-ttu-id="a81f9-329">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="a81f9-329">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="a81f9-330">Administrateur Desktop Analytics</span><span class="sxs-lookup"><span data-stu-id="a81f9-330">Desktop Analytics administrator</span></span> | <span data-ttu-id="a81f9-331">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="a81f9-331">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="a81f9-332">Lecteurs d’annuaires</span><span class="sxs-lookup"><span data-stu-id="a81f9-332">Directory readers</span></span> | <span data-ttu-id="a81f9-333">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="a81f9-333">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="a81f9-334">Administrateur Dynamics 365</span><span class="sxs-lookup"><span data-stu-id="a81f9-334">Dynamics 365 administrator</span></span> | <span data-ttu-id="a81f9-335">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="a81f9-335">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="a81f9-336">Administrateur Exchange</span><span class="sxs-lookup"><span data-stu-id="a81f9-336">Exchange administrator</span></span> | <span data-ttu-id="a81f9-337">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="a81f9-337">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="a81f9-338">IdentityAdministrateur du fournisseur externe</span><span class="sxs-lookup"><span data-stu-id="a81f9-338">External Identity Provider administrator</span></span> | <span data-ttu-id="a81f9-339">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="a81f9-339">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="a81f9-340">Administrateur général</span><span class="sxs-lookup"><span data-stu-id="a81f9-340">Global administrator</span></span> | <span data-ttu-id="a81f9-341">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="a81f9-341">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="a81f9-342">Lecteur général</span><span class="sxs-lookup"><span data-stu-id="a81f9-342">Global reader</span></span> | <span data-ttu-id="a81f9-343">f6903b21-6aba-4124-B44C-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="a81f9-343">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="a81f9-344">Administrateur de groupes</span><span class="sxs-lookup"><span data-stu-id="a81f9-344">Groups administrator</span></span> | <span data-ttu-id="a81f9-345">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="a81f9-345">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="a81f9-346">Inviteur d’invités</span><span class="sxs-lookup"><span data-stu-id="a81f9-346">Guest inviter</span></span> | <span data-ttu-id="a81f9-347">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="a81f9-347">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="a81f9-348">Administrateur du support technique</span><span class="sxs-lookup"><span data-stu-id="a81f9-348">Helpdesk administrator</span></span> | <span data-ttu-id="a81f9-349">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="a81f9-349">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="a81f9-350">Administrateur Intune</span><span class="sxs-lookup"><span data-stu-id="a81f9-350">Intune administrator</span></span> | <span data-ttu-id="a81f9-351">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="a81f9-351">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="a81f9-352">Administrateur Kaizala</span><span class="sxs-lookup"><span data-stu-id="a81f9-352">Kaizala administrator</span></span> | <span data-ttu-id="a81f9-353">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="a81f9-353">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="a81f9-354">Administrateur de licence</span><span class="sxs-lookup"><span data-stu-id="a81f9-354">License administrator</span></span> | <span data-ttu-id="a81f9-355">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="a81f9-355">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="a81f9-356">Lecteur de confidentialité du Centre de messages</span><span class="sxs-lookup"><span data-stu-id="a81f9-356">Message center privacy reader</span></span> | <span data-ttu-id="a81f9-357">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="a81f9-357">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="a81f9-358">Lecteur du Centre de messages</span><span class="sxs-lookup"><span data-stu-id="a81f9-358">Message center reader</span></span> | <span data-ttu-id="a81f9-359">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="a81f9-359">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="a81f9-360">Administrateur d’applications Office</span><span class="sxs-lookup"><span data-stu-id="a81f9-360">Office apps administrator</span></span> | <span data-ttu-id="a81f9-361">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="a81f9-361">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="a81f9-362">Administrateur de mots de passe</span><span class="sxs-lookup"><span data-stu-id="a81f9-362">Password administrator</span></span> | <span data-ttu-id="a81f9-363">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="a81f9-363">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="a81f9-364">Administrateur Power BI</span><span class="sxs-lookup"><span data-stu-id="a81f9-364">Power BI administrator</span></span> | <span data-ttu-id="a81f9-365">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="a81f9-365">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="a81f9-366">Administrateur de plateforme Power</span><span class="sxs-lookup"><span data-stu-id="a81f9-366">Power platform administrator</span></span> | <span data-ttu-id="a81f9-367">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="a81f9-367">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="a81f9-368">Administrateur d’authentification privilégié</span><span class="sxs-lookup"><span data-stu-id="a81f9-368">Privileged authentication administrator</span></span> | <span data-ttu-id="a81f9-369">0829f731-b46d-419F-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="a81f9-369">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="a81f9-370">Administrateur de rôle privilégié</span><span class="sxs-lookup"><span data-stu-id="a81f9-370">Privileged role administrator</span></span> | <span data-ttu-id="a81f9-371">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="a81f9-371">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="a81f9-372">Lecteur de rapports</span><span class="sxs-lookup"><span data-stu-id="a81f9-372">Reports reader</span></span> | <span data-ttu-id="a81f9-373">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="a81f9-373">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="a81f9-374">Administrateur de recherche</span><span class="sxs-lookup"><span data-stu-id="a81f9-374">Search administrator</span></span> | <span data-ttu-id="a81f9-375">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="a81f9-375">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="a81f9-376">Éditeur de recherche</span><span class="sxs-lookup"><span data-stu-id="a81f9-376">Search editor</span></span> | <span data-ttu-id="a81f9-377">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="a81f9-377">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="a81f9-378">Administrateur de sécurité</span><span class="sxs-lookup"><span data-stu-id="a81f9-378">Security administrator</span></span> | <span data-ttu-id="a81f9-379">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="a81f9-379">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="a81f9-380">Opérateur de sécurité</span><span class="sxs-lookup"><span data-stu-id="a81f9-380">Security operator</span></span> | <span data-ttu-id="a81f9-381">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="a81f9-381">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="a81f9-382">Lecteur de sécurité</span><span class="sxs-lookup"><span data-stu-id="a81f9-382">Security reader</span></span> | <span data-ttu-id="a81f9-383">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="a81f9-383">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="a81f9-384">Administrateur de support de service</span><span class="sxs-lookup"><span data-stu-id="a81f9-384">Service support administrator</span></span> | <span data-ttu-id="a81f9-385">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="a81f9-385">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="a81f9-386">Administrateur SharePoint</span><span class="sxs-lookup"><span data-stu-id="a81f9-386">SharePoint administrator</span></span> | <span data-ttu-id="a81f9-387">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="a81f9-387">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="a81f9-388">Administrateur Skype Entreprise</span><span class="sxs-lookup"><span data-stu-id="a81f9-388">Skype for Business administrator</span></span> | <span data-ttu-id="a81f9-389">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="a81f9-389">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="a81f9-390">Administrateur des communications Teams</span><span class="sxs-lookup"><span data-stu-id="a81f9-390">Teams Communications Administrator</span></span> | <span data-ttu-id="a81f9-391">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="a81f9-391">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="a81f9-392">Ingénieur de support des communications Teams</span><span class="sxs-lookup"><span data-stu-id="a81f9-392">Teams Communications Support Engineer</span></span> | <span data-ttu-id="a81f9-393">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="a81f9-393">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="a81f9-394">Spécialiste des communications des équipes</span><span class="sxs-lookup"><span data-stu-id="a81f9-394">Teams Communications Specialist</span></span> | <span data-ttu-id="a81f9-395">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="a81f9-395">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="a81f9-396">Administrateur du service Teams</span><span class="sxs-lookup"><span data-stu-id="a81f9-396">Teams Service Administrator</span></span> | <span data-ttu-id="a81f9-397">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="a81f9-397">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="a81f9-398">Administrateur d’utilisateurs</span><span class="sxs-lookup"><span data-stu-id="a81f9-398">User administrator</span></span> | <span data-ttu-id="a81f9-399">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="a81f9-399">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a81f9-400">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="a81f9-400">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
