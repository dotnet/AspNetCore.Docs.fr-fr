---
title: Fournisseurs de stratégies d’autorisation personnalisés dans ASP.NET Core
author: mjrousos
description: Découvrez comment utiliser un IAuthorizationPolicyProvider personnalisé dans une application ASP.NET Core pour générer dynamiquement des stratégies d’autorisation.
ms.author: riande
ms.custom: mvc
ms.date: 11/14/2019
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
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: 0f6f628cee0623c21a2a93aa11470005f8c78c58
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053135"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a>Fournisseurs de stratégies d’autorisation personnalisés utilisant IAuthorizationPolicyProvider dans ASP.NET Core 

Par [Mike Rousos](https://github.com/mjrousos)

En général, lors de l’utilisation de l' [autorisation basée sur la stratégie](xref:security/authorization/policies), les stratégies sont inscrites en appelant `AuthorizationOptions.AddPolicy` dans le cadre de la configuration du service d’autorisation. Dans certains scénarios, il n’est pas possible (ou souhaitable) d’inscrire toutes les stratégies d’autorisation de cette manière. Dans ce cas, vous pouvez [utiliser un personnalisé `IAuthorizationPolicyProvider` ](#ci) pour contrôler la façon dont les stratégies d’autorisation sont fournies.

Voici quelques exemples de scénarios dans lesquels un [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) personnalisé peut être utile :

* Utilisation d’un service externe pour fournir une évaluation de la stratégie.
* À l’aide d’un large éventail de stratégies (pour différents nombres de pièces ou âges, par exemple), il n’est pas judicieux d’ajouter chaque stratégie d’autorisation avec un `AuthorizationOptions.AddPolicy` appel.
* Création de stratégies au moment de l’exécution en fonction des informations contenues dans une source de données externe (par exemple, une base de données) ou détermination dynamique des exigences d’autorisation par le biais d’un autre mécanisme.

[Affichez ou téléchargez l’exemple de code](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider) à partir du [référentiel GitHub AspNetCore](https://github.com/dotnet/AspNetCore). Téléchargez le fichier ZIP du référentiel dotnet/AspNetCore. Décompressez le fichier. Accédez au dossier de projet *src/Security/Samples/CustomPolicyProvider* .

## <a name="customize-policy-retrieval"></a>Personnaliser la récupération de stratégie

Les applications ASP.NET Core utilisent une implémentation de l' `IAuthorizationPolicyProvider` interface pour récupérer des stratégies d’autorisation. Par défaut, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) est inscrit et utilisé. `DefaultAuthorizationPolicyProvider` retourne des stratégies à partir du `AuthorizationOptions` fourni dans un `IServiceCollection.AddAuthorization` appel.

Personnalisez ce comportement en inscrivant une `IAuthorizationPolicyProvider` implémentation différente dans le conteneur d' [injection de dépendances](xref:fundamentals/dependency-injection) de l’application. 

L' `IAuthorizationPolicyProvider` interface contient trois API :

* [GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) retourne une stratégie d’autorisation pour un nom donné.
* [GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) retourne la stratégie d’autorisation par défaut (la stratégie utilisée pour les `[Authorize]` attributs sans une stratégie spécifiée). 
* [GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) retourne la stratégie d’autorisation de secours (la stratégie utilisée par l’intergiciel (middleware) d’autorisation quand aucune stratégie n’est spécifiée). 

En implémentant ces API, vous pouvez personnaliser la façon dont les stratégies d’autorisation sont fournies.

## <a name="parameterized-authorize-attribute-example"></a>Exemple d’attribut Authorize paramétrable

Un scénario où `IAuthorizationPolicyProvider` est utile est l’activation `[Authorize]` d’attributs personnalisés dont les spécifications dépendent d’un paramètre. Par exemple, dans la documentation [d’autorisation basée sur des stratégies](xref:security/authorization/policies) , une stratégie basée sur l’âge (« AtLeast21 ») a été utilisée comme exemple. Si des actions de contrôleur différentes dans une application doivent être mises à la disposition des utilisateurs de *différents* âges, il peut être utile de disposer de nombreuses stratégies d’âge différentes. Au lieu d’inscrire toutes les autres stratégies basées sur l’âge nécessaires à l’application `AuthorizationOptions` , vous pouvez générer les stratégies de manière dynamique avec un personnalisé `IAuthorizationPolicyProvider` . Pour faciliter l’utilisation des stratégies, vous pouvez annoter des actions avec un attribut d’autorisation personnalisé comme `[MinimumAgeAuthorize(20)]` .

## <a name="custom-authorization-attributes"></a>Attributs d’autorisation personnalisés

Les stratégies d’autorisation sont identifiées par leur nom. La `MinimumAgeAuthorizeAttribute` procédure personnalisée décrite précédemment doit mapper des arguments dans une chaîne qui peut être utilisée pour récupérer la stratégie d’autorisation correspondante. Pour ce faire, vous pouvez dériver de `AuthorizeAttribute` et rendre la `Age` propriété encapsulée dans la `AuthorizeAttribute.Policy` propriété.

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

Ce type d’attribut a une `Policy` chaîne basée sur le préfixe codé en dur ( `"MinimumAge"` ) et un entier passé par le biais du constructeur.

Vous pouvez l’appliquer aux actions de la même façon que d’autres `Authorize` attributs, sauf qu’il prend un entier comme paramètre.

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a>IAuthorizationPolicyProvider personnalisé

Le personnalisé `MinimumAgeAuthorizeAttribute` simplifie la demande de stratégies d’autorisation pour tout âge minimal souhaité. Le prochain problème à résoudre consiste à s’assurer que les stratégies d’autorisation sont disponibles pour tous ces âges. C’est là qu’un `IAuthorizationPolicyProvider` est utile.

Lorsque `MinimumAgeAuthorizationAttribute` vous utilisez, les noms de stratégie d’autorisation suivent le modèle `"MinimumAge" + Age` , de sorte que le personnalisé `IAuthorizationPolicyProvider` doit générer des stratégies d’autorisation en :

* Analyse de l’âge à partir du nom de la stratégie.
* Utilisation `AuthorizationPolicyBuilder` de pour créer un nouveau `AuthorizationPolicy`
* Dans cet exemple et les exemples suivants, on suppose que l’utilisateur est authentifié via un cookie . `AuthorizationPolicyBuilder`Doit être construit avec au moins un nom de schéma d’autorisation, ou toujours correctement. Dans le cas contraire, il n’y a pas d’informations sur la façon de fournir une stimulation à l’utilisateur et une exception sera levée.
* Ajout de spécifications à la stratégie en fonction de l’âge avec `AuthorizationPolicyBuilder.AddRequirements` . Dans d’autres scénarios, vous pouvez utiliser `RequireClaim` , ou à la `RequireRole` `RequireUserName` place.

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
            var policy = new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme);
            policy.AddRequirements(new MinimumAgeRequirement(age));
            return Task.FromResult(policy.Build());
        }

        return Task.FromResult<AuthorizationPolicy>(null);
    }
}
```

## <a name="multiple-authorization-policy-providers"></a>Plusieurs fournisseurs de stratégies d’autorisation

Lorsque vous utilisez `IAuthorizationPolicyProvider` des implémentations personnalisées, gardez à l’esprit que ASP.net Core utilise uniquement une instance de `IAuthorizationPolicyProvider` . Si un fournisseur personnalisé n’est pas en mesure de fournir des stratégies d’autorisation pour tous les noms de stratégie qui seront utilisés, il doit s’en remettre à un fournisseur de sauvegarde. 

Par exemple, considérez une application qui nécessite à la fois des stratégies d’âge personnalisées et une récupération de stratégie basée sur les rôles plus classique. Une telle application peut utiliser un fournisseur de stratégie d’autorisation personnalisé qui :

* Tentatives d’analyse des noms de stratégie. 
* Appelle un fournisseur de stratégie différent (par exemple `DefaultAuthorizationPolicyProvider` ) si le nom de la stratégie ne contient pas d’ancienneté.

L’exemple d' `IAuthorizationPolicyProvider` implémentation illustré ci-dessus peut être mis à jour pour utiliser le `DefaultAuthorizationPolicyProvider` en créant un fournisseur de stratégie de sauvegarde dans son constructeur (à utiliser si le nom de la stratégie ne correspond pas au modèle attendu « minimum » + Age).

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

La `GetPolicyAsync` méthode peut ensuite être mise à jour pour utiliser le au `BackupPolicyProvider` lieu de retourner la valeur NULL :

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a>Stratégie par défaut

En plus de fournir des stratégies d’autorisation nommées, un personnalisé `IAuthorizationPolicyProvider` doit implémenter `GetDefaultPolicyAsync` pour fournir une stratégie d’autorisation pour les `[Authorize]` attributs sans nom de stratégie spécifié.

Dans de nombreux cas, cet attribut d’autorisation nécessite uniquement un utilisateur authentifié, ce qui vous permet d’effectuer la stratégie nécessaire avec un appel à `RequireAuthenticatedUser` :

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

Comme pour tous les aspects d’un personnalisé `IAuthorizationPolicyProvider` , vous pouvez le personnaliser en fonction des besoins. Dans certains cas, il peut être souhaitable de récupérer la stratégie par défaut à partir d’un secours `IAuthorizationPolicyProvider` .

## <a name="fallback-policy"></a>Stratégie de secours

Un personnalisé `IAuthorizationPolicyProvider` peut éventuellement implémenter `GetFallbackPolicyAsync` pour fournir une stratégie utilisée lors de la [combinaison de stratégies](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) et quand aucune stratégie n’est spécifiée. Si `GetFallbackPolicyAsync` retourne une stratégie non null, la stratégie retournée est utilisée par l’intergiciel (middleware) d’autorisation quand aucune stratégie n’est spécifiée pour la demande.

Si aucune stratégie de secours n’est requise, le fournisseur peut retourner `null` ou retarder le fournisseur de secours :

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

<a name="ci"></a>

## <a name="use-a-custom-iauthorizationpolicyprovider"></a>Utiliser un IAuthorizationPolicyProvider personnalisé

Pour utiliser des stratégies personnalisées à partir d’un `IAuthorizationPolicyProvider` , vous * **devez** _ :

_ Enregistrer les `AuthorizationHandler` types appropriés avec l’injection de dépendances (décrite dans [autorisation basée sur la stratégie](xref:security/authorization/policies#authorization-handlers)), comme avec tous les scénarios d’autorisation basés sur des stratégies.
* Inscrivez le `IAuthorizationPolicyProvider` type personnalisé dans la collection de services d’injection de dépendances de l’application dans `Startup.ConfigureServices` pour remplacer le fournisseur de stratégie par défaut.

  ```csharp
  services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
  ```

Un exemple personnalisé complet `IAuthorizationPolicyProvider` est disponible dans le [référentiel GitHub dotnet/aspnetcore](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider).
