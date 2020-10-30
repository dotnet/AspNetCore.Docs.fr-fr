---
title: Fournisseurs de stratégies d’autorisation personnalisés dans ASP.NET Core
author: mjrousos
description: Découvrez comment utiliser un IAuthorizationPolicyProvider personnalisé dans une application ASP.NET Core pour générer dynamiquement des stratégies d’autorisation.
ms.author: riande
ms.custom: mvc
ms.date: 11/14/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: 0f6f628cee0623c21a2a93aa11470005f8c78c58
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053135"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a><span data-ttu-id="5e3a4-103">Fournisseurs de stratégies d’autorisation personnalisés utilisant IAuthorizationPolicyProvider dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5e3a4-103">Custom Authorization Policy Providers using IAuthorizationPolicyProvider in ASP.NET Core</span></span> 

<span data-ttu-id="5e3a4-104">Par [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="5e3a4-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="5e3a4-105">En général, lors de l’utilisation de l' [autorisation basée sur la stratégie](xref:security/authorization/policies), les stratégies sont inscrites en appelant `AuthorizationOptions.AddPolicy` dans le cadre de la configuration du service d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="5e3a4-105">Typically when using [policy-based authorization](xref:security/authorization/policies), policies are registered by calling `AuthorizationOptions.AddPolicy` as part of authorization service configuration.</span></span> <span data-ttu-id="5e3a4-106">Dans certains scénarios, il n’est pas possible (ou souhaitable) d’inscrire toutes les stratégies d’autorisation de cette manière.</span><span class="sxs-lookup"><span data-stu-id="5e3a4-106">In some scenarios, it may not be possible (or desirable) to register all authorization policies in this way.</span></span> <span data-ttu-id="5e3a4-107">Dans ce cas, vous pouvez [utiliser un personnalisé `IAuthorizationPolicyProvider` ](#ci) pour contrôler la façon dont les stratégies d’autorisation sont fournies.</span><span class="sxs-lookup"><span data-stu-id="5e3a4-107">In those cases, you can [use a custom `IAuthorizationPolicyProvider`](#ci) to control how authorization policies are supplied.</span></span>

<span data-ttu-id="5e3a4-108">Voici quelques exemples de scénarios dans lesquels un [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) personnalisé peut être utile :</span><span class="sxs-lookup"><span data-stu-id="5e3a4-108">Examples of scenarios where a custom [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) may be useful include:</span></span>

* <span data-ttu-id="5e3a4-109">Utilisation d’un service externe pour fournir une évaluation de la stratégie.</span><span class="sxs-lookup"><span data-stu-id="5e3a4-109">Using an external service to provide policy evaluation.</span></span>
* <span data-ttu-id="5e3a4-110">À l’aide d’un large éventail de stratégies (pour différents nombres de pièces ou âges, par exemple), il n’est pas judicieux d’ajouter chaque stratégie d’autorisation avec un `AuthorizationOptions.AddPolicy` appel.</span><span class="sxs-lookup"><span data-stu-id="5e3a4-110">Using a large range of policies (for different room numbers or ages, for example), so it doesn't make sense to add each individual authorization policy with an `AuthorizationOptions.AddPolicy` call.</span></span>
* <span data-ttu-id="5e3a4-111">Création de stratégies au moment de l’exécution en fonction des informations contenues dans une source de données externe (par exemple, une base de données) ou détermination dynamique des exigences d’autorisation par le biais d’un autre mécanisme.</span><span class="sxs-lookup"><span data-stu-id="5e3a4-111">Creating policies at runtime based on information in an external data source (like a database) or determining authorization requirements dynamically through another mechanism.</span></span>

<span data-ttu-id="5e3a4-112">[Affichez ou téléchargez l’exemple de code](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider) à partir du [référentiel GitHub AspNetCore](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="5e3a4-112">[View or download sample code](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider) from the [AspNetCore GitHub repository](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="5e3a4-113">Téléchargez le fichier ZIP du référentiel dotnet/AspNetCore.</span><span class="sxs-lookup"><span data-stu-id="5e3a4-113">Download the dotnet/AspNetCore repository ZIP file.</span></span> <span data-ttu-id="5e3a4-114">Décompressez le fichier.</span><span class="sxs-lookup"><span data-stu-id="5e3a4-114">Unzip the file.</span></span> <span data-ttu-id="5e3a4-115">Accédez au dossier de projet *src/Security/Samples/CustomPolicyProvider* .</span><span class="sxs-lookup"><span data-stu-id="5e3a4-115">Navigate to the *src/Security/samples/CustomPolicyProvider* project folder.</span></span>

## <a name="customize-policy-retrieval"></a><span data-ttu-id="5e3a4-116">Personnaliser la récupération de stratégie</span><span class="sxs-lookup"><span data-stu-id="5e3a4-116">Customize policy retrieval</span></span>

<span data-ttu-id="5e3a4-117">Les applications ASP.NET Core utilisent une implémentation de l' `IAuthorizationPolicyProvider` interface pour récupérer des stratégies d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="5e3a4-117">ASP.NET Core apps use an implementation of the `IAuthorizationPolicyProvider` interface to retrieve authorization policies.</span></span> <span data-ttu-id="5e3a4-118">Par défaut, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) est inscrit et utilisé.</span><span class="sxs-lookup"><span data-stu-id="5e3a4-118">By default, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) is registered and used.</span></span> <span data-ttu-id="5e3a4-119">`DefaultAuthorizationPolicyProvider` retourne des stratégies à partir du `AuthorizationOptions` fourni dans un `IServiceCollection.AddAuthorization` appel.</span><span class="sxs-lookup"><span data-stu-id="5e3a4-119">`DefaultAuthorizationPolicyProvider` returns policies from the `AuthorizationOptions` provided in an `IServiceCollection.AddAuthorization` call.</span></span>

<span data-ttu-id="5e3a4-120">Personnalisez ce comportement en inscrivant une `IAuthorizationPolicyProvider` implémentation différente dans le conteneur d' [injection de dépendances](xref:fundamentals/dependency-injection) de l’application.</span><span class="sxs-lookup"><span data-stu-id="5e3a4-120">Customize this behavior by registering a different `IAuthorizationPolicyProvider` implementation in the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> 

<span data-ttu-id="5e3a4-121">L' `IAuthorizationPolicyProvider` interface contient trois API :</span><span class="sxs-lookup"><span data-stu-id="5e3a4-121">The `IAuthorizationPolicyProvider` interface contains three APIs:</span></span>

* <span data-ttu-id="5e3a4-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) retourne une stratégie d’autorisation pour un nom donné.</span><span class="sxs-lookup"><span data-stu-id="5e3a4-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) returns an authorization policy for a given name.</span></span>
* <span data-ttu-id="5e3a4-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) retourne la stratégie d’autorisation par défaut (la stratégie utilisée pour les `[Authorize]` attributs sans une stratégie spécifiée).</span><span class="sxs-lookup"><span data-stu-id="5e3a4-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) returns the default authorization policy (the policy used for `[Authorize]` attributes without a policy specified).</span></span> 
* <span data-ttu-id="5e3a4-124">[GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) retourne la stratégie d’autorisation de secours (la stratégie utilisée par l’intergiciel (middleware) d’autorisation quand aucune stratégie n’est spécifiée).</span><span class="sxs-lookup"><span data-stu-id="5e3a4-124">[GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) returns the fallback authorization policy (the policy used by the Authorization Middleware when no policy is specified).</span></span> 

<span data-ttu-id="5e3a4-125">En implémentant ces API, vous pouvez personnaliser la façon dont les stratégies d’autorisation sont fournies.</span><span class="sxs-lookup"><span data-stu-id="5e3a4-125">By implementing these APIs, you can customize how authorization policies are provided.</span></span>

## <a name="parameterized-authorize-attribute-example"></a><span data-ttu-id="5e3a4-126">Exemple d’attribut Authorize paramétrable</span><span class="sxs-lookup"><span data-stu-id="5e3a4-126">Parameterized authorize attribute example</span></span>

<span data-ttu-id="5e3a4-127">Un scénario où `IAuthorizationPolicyProvider` est utile est l’activation `[Authorize]` d’attributs personnalisés dont les spécifications dépendent d’un paramètre.</span><span class="sxs-lookup"><span data-stu-id="5e3a4-127">One scenario where `IAuthorizationPolicyProvider` is useful is enabling custom `[Authorize]` attributes whose requirements depend on a parameter.</span></span> <span data-ttu-id="5e3a4-128">Par exemple, dans la documentation [d’autorisation basée sur des stratégies](xref:security/authorization/policies) , une stratégie basée sur l’âge (« AtLeast21 ») a été utilisée comme exemple.</span><span class="sxs-lookup"><span data-stu-id="5e3a4-128">For example, in [policy-based authorization](xref:security/authorization/policies) documentation, an age-based (“AtLeast21”) policy was used as a sample.</span></span> <span data-ttu-id="5e3a4-129">Si des actions de contrôleur différentes dans une application doivent être mises à la disposition des utilisateurs de *différents* âges, il peut être utile de disposer de nombreuses stratégies d’âge différentes.</span><span class="sxs-lookup"><span data-stu-id="5e3a4-129">If different controller actions in an app should be made available to users of *different* ages, it might be useful to have many different age-based policies.</span></span> <span data-ttu-id="5e3a4-130">Au lieu d’inscrire toutes les autres stratégies basées sur l’âge nécessaires à l’application `AuthorizationOptions` , vous pouvez générer les stratégies de manière dynamique avec un personnalisé `IAuthorizationPolicyProvider` .</span><span class="sxs-lookup"><span data-stu-id="5e3a4-130">Instead of registering all the different age-based policies that the application will need in `AuthorizationOptions`, you can generate the policies dynamically with a custom `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="5e3a4-131">Pour faciliter l’utilisation des stratégies, vous pouvez annoter des actions avec un attribut d’autorisation personnalisé comme `[MinimumAgeAuthorize(20)]` .</span><span class="sxs-lookup"><span data-stu-id="5e3a4-131">To make using the policies easier, you can annotate actions with custom authorization attribute like `[MinimumAgeAuthorize(20)]`.</span></span>

## <a name="custom-authorization-attributes"></a><span data-ttu-id="5e3a4-132">Attributs d’autorisation personnalisés</span><span class="sxs-lookup"><span data-stu-id="5e3a4-132">Custom Authorization attributes</span></span>

<span data-ttu-id="5e3a4-133">Les stratégies d’autorisation sont identifiées par leur nom.</span><span class="sxs-lookup"><span data-stu-id="5e3a4-133">Authorization policies are identified by their names.</span></span> <span data-ttu-id="5e3a4-134">La `MinimumAgeAuthorizeAttribute` procédure personnalisée décrite précédemment doit mapper des arguments dans une chaîne qui peut être utilisée pour récupérer la stratégie d’autorisation correspondante.</span><span class="sxs-lookup"><span data-stu-id="5e3a4-134">The custom `MinimumAgeAuthorizeAttribute` described previously needs to map arguments into a string that can be used to retrieve the corresponding authorization policy.</span></span> <span data-ttu-id="5e3a4-135">Pour ce faire, vous pouvez dériver de `AuthorizeAttribute` et rendre la `Age` propriété encapsulée dans la `AuthorizeAttribute.Policy` propriété.</span><span class="sxs-lookup"><span data-stu-id="5e3a4-135">You can do this by deriving from `AuthorizeAttribute` and making the `Age` property wrap the `AuthorizeAttribute.Policy` property.</span></span>

```csharp
internal class MinimumAgeAuthorizeAttribute : AuthorizeAttribute
{
    const string POLICY_PREFIX = "MinimumAge";

    public MinimumAgeAuthorizeAttribute(int age) => Age = age;

    // Get or set the Age property by manipulating the underlying Policy property
    public int Age
    {
        get
        {
            if (int.TryParse(Policy.Substring(POLICY_PREFIX.Length), out var age))
            {
                return age;
            }
            return default(int);
        }
        set
        {
            Policy = $"{POLICY_PREFIX}{value.ToString()}";
        }
    }
}
```

<span data-ttu-id="5e3a4-136">Ce type d’attribut a une `Policy` chaîne basée sur le préfixe codé en dur ( `"MinimumAge"` ) et un entier passé par le biais du constructeur.</span><span class="sxs-lookup"><span data-stu-id="5e3a4-136">This attribute type has a `Policy` string based on the hard-coded prefix (`"MinimumAge"`) and an integer passed in via the constructor.</span></span>

<span data-ttu-id="5e3a4-137">Vous pouvez l’appliquer aux actions de la même façon que d’autres `Authorize` attributs, sauf qu’il prend un entier comme paramètre.</span><span class="sxs-lookup"><span data-stu-id="5e3a4-137">You can apply it to actions in the same way as other `Authorize` attributes except that it takes an integer as a parameter.</span></span>

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a><span data-ttu-id="5e3a4-138">IAuthorizationPolicyProvider personnalisé</span><span class="sxs-lookup"><span data-stu-id="5e3a4-138">Custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="5e3a4-139">Le personnalisé `MinimumAgeAuthorizeAttribute` simplifie la demande de stratégies d’autorisation pour tout âge minimal souhaité.</span><span class="sxs-lookup"><span data-stu-id="5e3a4-139">The custom `MinimumAgeAuthorizeAttribute` makes it easy to request authorization policies for any minimum age desired.</span></span> <span data-ttu-id="5e3a4-140">Le prochain problème à résoudre consiste à s’assurer que les stratégies d’autorisation sont disponibles pour tous ces âges.</span><span class="sxs-lookup"><span data-stu-id="5e3a4-140">The next problem to solve is making sure that authorization policies are available for all of those different ages.</span></span> <span data-ttu-id="5e3a4-141">C’est là qu’un `IAuthorizationPolicyProvider` est utile.</span><span class="sxs-lookup"><span data-stu-id="5e3a4-141">This is where an `IAuthorizationPolicyProvider` is useful.</span></span>

<span data-ttu-id="5e3a4-142">Lorsque `MinimumAgeAuthorizationAttribute` vous utilisez, les noms de stratégie d’autorisation suivent le modèle `"MinimumAge" + Age` , de sorte que le personnalisé `IAuthorizationPolicyProvider` doit générer des stratégies d’autorisation en :</span><span class="sxs-lookup"><span data-stu-id="5e3a4-142">When using `MinimumAgeAuthorizationAttribute`, the authorization policy names will follow the pattern `"MinimumAge" + Age`, so the custom `IAuthorizationPolicyProvider` should generate authorization policies by:</span></span>

* <span data-ttu-id="5e3a4-143">Analyse de l’âge à partir du nom de la stratégie.</span><span class="sxs-lookup"><span data-stu-id="5e3a4-143">Parsing the age from the policy name.</span></span>
* <span data-ttu-id="5e3a4-144">Utilisation `AuthorizationPolicyBuilder` de pour créer un nouveau `AuthorizationPolicy`</span><span class="sxs-lookup"><span data-stu-id="5e3a4-144">Using `AuthorizationPolicyBuilder` to create a new `AuthorizationPolicy`</span></span>
* <span data-ttu-id="5e3a4-145">Dans cet exemple et les exemples suivants, on suppose que l’utilisateur est authentifié via un :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="5e3a4-145">In this and following examples it will be assumed that the user is authenticated via a :::no-loc(cookie):::.</span></span> <span data-ttu-id="5e3a4-146">`AuthorizationPolicyBuilder`Doit être construit avec au moins un nom de schéma d’autorisation, ou toujours correctement.</span><span class="sxs-lookup"><span data-stu-id="5e3a4-146">The `AuthorizationPolicyBuilder` should either be constructed with at least one authorization scheme name or always succeed.</span></span> <span data-ttu-id="5e3a4-147">Dans le cas contraire, il n’y a pas d’informations sur la façon de fournir une stimulation à l’utilisateur et une exception sera levée.</span><span class="sxs-lookup"><span data-stu-id="5e3a4-147">Otherwise there is no information on how to provide a challenge to the user and an exception will be thrown.</span></span>
* <span data-ttu-id="5e3a4-148">Ajout de spécifications à la stratégie en fonction de l’âge avec `AuthorizationPolicyBuilder.AddRequirements` .</span><span class="sxs-lookup"><span data-stu-id="5e3a4-148">Adding requirements to the policy based on the age with `AuthorizationPolicyBuilder.AddRequirements`.</span></span> <span data-ttu-id="5e3a4-149">Dans d’autres scénarios, vous pouvez utiliser `RequireClaim` , ou à la `RequireRole` `RequireUserName` place.</span><span class="sxs-lookup"><span data-stu-id="5e3a4-149">In other scenarios, you might use `RequireClaim`, `RequireRole`, or `RequireUserName` instead.</span></span>

```csharp
internal class MinimumAgePolicyProvider : IAuthorizationPolicyProvider
{
    const string POLICY_PREFIX = "MinimumAge";

    // Policies are looked up by string name, so expect 'parameters' (like age)
    // to be embedded in the policy names. This is abstracted away from developers
    // by the more strongly-typed attributes derived from AuthorizeAttribute
    // (like [MinimumAgeAuthorize()] in this sample)
    public Task<AuthorizationPolicy> GetPolicyAsync(string policyName)
    {
        if (policyName.StartsWith(POLICY_PREFIX, StringComparison.OrdinalIgnoreCase) &&
            int.TryParse(policyName.Substring(POLICY_PREFIX.Length), out var age))
        {
            var policy = new AuthorizationPolicyBuilder(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme);
            policy.AddRequirements(new MinimumAgeRequirement(age));
            return Task.FromResult(policy.Build());
        }

        return Task.FromResult<AuthorizationPolicy>(null);
    }
}
```

## <a name="multiple-authorization-policy-providers"></a><span data-ttu-id="5e3a4-150">Plusieurs fournisseurs de stratégies d’autorisation</span><span class="sxs-lookup"><span data-stu-id="5e3a4-150">Multiple authorization policy providers</span></span>

<span data-ttu-id="5e3a4-151">Lorsque vous utilisez `IAuthorizationPolicyProvider` des implémentations personnalisées, gardez à l’esprit que ASP.net Core utilise uniquement une instance de `IAuthorizationPolicyProvider` .</span><span class="sxs-lookup"><span data-stu-id="5e3a4-151">When using custom `IAuthorizationPolicyProvider` implementations, keep in mind that ASP.NET Core only uses one instance of `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="5e3a4-152">Si un fournisseur personnalisé n’est pas en mesure de fournir des stratégies d’autorisation pour tous les noms de stratégie qui seront utilisés, il doit s’en remettre à un fournisseur de sauvegarde.</span><span class="sxs-lookup"><span data-stu-id="5e3a4-152">If a custom provider isn't able to provide authorization policies for all policy names that will be used, it should defer to a backup provider.</span></span> 

<span data-ttu-id="5e3a4-153">Par exemple, considérez une application qui nécessite à la fois des stratégies d’âge personnalisées et une récupération de stratégie basée sur les rôles plus classique.</span><span class="sxs-lookup"><span data-stu-id="5e3a4-153">For example, consider an application that needs both custom age policies and more traditional role-based policy retrieval.</span></span> <span data-ttu-id="5e3a4-154">Une telle application peut utiliser un fournisseur de stratégie d’autorisation personnalisé qui :</span><span class="sxs-lookup"><span data-stu-id="5e3a4-154">Such an app could use a custom authorization policy provider that:</span></span>

* <span data-ttu-id="5e3a4-155">Tentatives d’analyse des noms de stratégie.</span><span class="sxs-lookup"><span data-stu-id="5e3a4-155">Attempts to parse policy names.</span></span> 
* <span data-ttu-id="5e3a4-156">Appelle un fournisseur de stratégie différent (par exemple `DefaultAuthorizationPolicyProvider` ) si le nom de la stratégie ne contient pas d’ancienneté.</span><span class="sxs-lookup"><span data-stu-id="5e3a4-156">Calls into a different policy provider (like `DefaultAuthorizationPolicyProvider`) if the policy name doesn't contain an age.</span></span>

<span data-ttu-id="5e3a4-157">L’exemple d' `IAuthorizationPolicyProvider` implémentation illustré ci-dessus peut être mis à jour pour utiliser le `DefaultAuthorizationPolicyProvider` en créant un fournisseur de stratégie de sauvegarde dans son constructeur (à utiliser si le nom de la stratégie ne correspond pas au modèle attendu « minimum » + Age).</span><span class="sxs-lookup"><span data-stu-id="5e3a4-157">The example `IAuthorizationPolicyProvider` implementation shown above can be updated to use the `DefaultAuthorizationPolicyProvider` by creating a backup policy provider in its constructor (to be used in case the policy name doesn't match its expected pattern of 'MinimumAge' + age).</span></span>

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

<span data-ttu-id="5e3a4-158">La `GetPolicyAsync` méthode peut ensuite être mise à jour pour utiliser le au `BackupPolicyProvider` lieu de retourner la valeur NULL :</span><span class="sxs-lookup"><span data-stu-id="5e3a4-158">Then, the `GetPolicyAsync` method can be updated to use the `BackupPolicyProvider` instead of returning null:</span></span>

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a><span data-ttu-id="5e3a4-159">Stratégie par défaut</span><span class="sxs-lookup"><span data-stu-id="5e3a4-159">Default policy</span></span>

<span data-ttu-id="5e3a4-160">En plus de fournir des stratégies d’autorisation nommées, un personnalisé `IAuthorizationPolicyProvider` doit implémenter `GetDefaultPolicyAsync` pour fournir une stratégie d’autorisation pour les `[Authorize]` attributs sans nom de stratégie spécifié.</span><span class="sxs-lookup"><span data-stu-id="5e3a4-160">In addition to providing named authorization policies, a custom `IAuthorizationPolicyProvider` needs to implement `GetDefaultPolicyAsync` to provide an authorization policy for `[Authorize]` attributes without a policy name specified.</span></span>

<span data-ttu-id="5e3a4-161">Dans de nombreux cas, cet attribut d’autorisation nécessite uniquement un utilisateur authentifié, ce qui vous permet d’effectuer la stratégie nécessaire avec un appel à `RequireAuthenticatedUser` :</span><span class="sxs-lookup"><span data-stu-id="5e3a4-161">In many cases, this authorization attribute only requires an authenticated user, so you can make the necessary policy with a call to `RequireAuthenticatedUser`:</span></span>

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

<span data-ttu-id="5e3a4-162">Comme pour tous les aspects d’un personnalisé `IAuthorizationPolicyProvider` , vous pouvez le personnaliser en fonction des besoins.</span><span class="sxs-lookup"><span data-stu-id="5e3a4-162">As with all aspects of a custom `IAuthorizationPolicyProvider`, you can customize this, as needed.</span></span> <span data-ttu-id="5e3a4-163">Dans certains cas, il peut être souhaitable de récupérer la stratégie par défaut à partir d’un secours `IAuthorizationPolicyProvider` .</span><span class="sxs-lookup"><span data-stu-id="5e3a4-163">In some cases, it may be desirable to retrieve the default policy from a fallback `IAuthorizationPolicyProvider`.</span></span>

## <a name="fallback-policy"></a><span data-ttu-id="5e3a4-164">Stratégie de secours</span><span class="sxs-lookup"><span data-stu-id="5e3a4-164">Fallback policy</span></span>

<span data-ttu-id="5e3a4-165">Un personnalisé `IAuthorizationPolicyProvider` peut éventuellement implémenter `GetFallbackPolicyAsync` pour fournir une stratégie utilisée lors de la [combinaison de stratégies](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) et quand aucune stratégie n’est spécifiée.</span><span class="sxs-lookup"><span data-stu-id="5e3a4-165">A custom `IAuthorizationPolicyProvider` can optionally implement `GetFallbackPolicyAsync` to provide a policy that's used when [combining policies](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) and when no policies are specified.</span></span> <span data-ttu-id="5e3a4-166">Si `GetFallbackPolicyAsync` retourne une stratégie non null, la stratégie retournée est utilisée par l’intergiciel (middleware) d’autorisation quand aucune stratégie n’est spécifiée pour la demande.</span><span class="sxs-lookup"><span data-stu-id="5e3a4-166">If `GetFallbackPolicyAsync` returns a non-null policy, the returned policy is used by the Authorization Middleware when no policies are specified for the request.</span></span>

<span data-ttu-id="5e3a4-167">Si aucune stratégie de secours n’est requise, le fournisseur peut retourner `null` ou retarder le fournisseur de secours :</span><span class="sxs-lookup"><span data-stu-id="5e3a4-167">If no fallback policy is required, the provider can return `null` or defer to the fallback provider:</span></span>

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

<a name="ci"></a>

## <a name="use-a-custom-iauthorizationpolicyprovider"></a><span data-ttu-id="5e3a4-168">Utiliser un IAuthorizationPolicyProvider personnalisé</span><span class="sxs-lookup"><span data-stu-id="5e3a4-168">Use a custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="5e3a4-169">Pour utiliser des stratégies personnalisées à partir d’un `IAuthorizationPolicyProvider` , vous \* **devez** _ :</span><span class="sxs-lookup"><span data-stu-id="5e3a4-169">To use custom policies from an `IAuthorizationPolicyProvider`, you \* **must** _:</span></span>

<span data-ttu-id="5e3a4-170">_ Enregistrer les `AuthorizationHandler` types appropriés avec l’injection de dépendances (décrite dans [autorisation basée sur la stratégie](xref:security/authorization/policies#authorization-handlers)), comme avec tous les scénarios d’autorisation basés sur des stratégies.</span><span class="sxs-lookup"><span data-stu-id="5e3a4-170">_ Register the appropriate `AuthorizationHandler` types with dependency injection (described in [policy-based authorization](xref:security/authorization/policies#authorization-handlers)), as with all policy-based authorization scenarios.</span></span>
* <span data-ttu-id="5e3a4-171">Inscrivez le `IAuthorizationPolicyProvider` type personnalisé dans la collection de services d’injection de dépendances de l’application dans `Startup.ConfigureServices` pour remplacer le fournisseur de stratégie par défaut.</span><span class="sxs-lookup"><span data-stu-id="5e3a4-171">Register the custom `IAuthorizationPolicyProvider` type in the app's dependency injection service collection in `Startup.ConfigureServices` to replace the default policy provider.</span></span>

  ```csharp
  services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
  ```

<span data-ttu-id="5e3a4-172">Un exemple personnalisé complet `IAuthorizationPolicyProvider` est disponible dans le [référentiel GitHub dotnet/aspnetcore](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider).</span><span class="sxs-lookup"><span data-stu-id="5e3a4-172">A complete custom `IAuthorizationPolicyProvider` sample is available in the [dotnet/aspnetcore GitHub repository](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider).</span></span>
