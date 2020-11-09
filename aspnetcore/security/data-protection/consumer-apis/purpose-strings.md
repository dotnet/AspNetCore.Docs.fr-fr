---
title: Chaînes d’objectif dans ASP.NET Core
author: rick-anderson
description: Découvrez comment les chaînes sont utilisées dans les API de protection des données ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/consumer-apis/purpose-strings
ms.openlocfilehash: 9a55131714b23909da5d00b73607078b295a1c4d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060805"
---
# <a name="purpose-strings-in-aspnet-core"></a><span data-ttu-id="dd30d-103">Chaînes d’objectif dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dd30d-103">Purpose strings in ASP.NET Core</span></span>

<a name="data-protection-consumer-apis-purposes"></a>

<span data-ttu-id="dd30d-104">Les composants qui utilisent `IDataProtectionProvider` doivent passer un paramètre d' *usage* unique à la `CreateProtector` méthode.</span><span class="sxs-lookup"><span data-stu-id="dd30d-104">Components which consume `IDataProtectionProvider` must pass a unique *purposes* parameter to the `CreateProtector` method.</span></span> <span data-ttu-id="dd30d-105">Le *paramètre* usage est inhérent à la sécurité du système de protection des données, car il permet l’isolation entre les consommateurs de chiffrement, même si les clés de chiffrement racine sont identiques.</span><span class="sxs-lookup"><span data-stu-id="dd30d-105">The purposes *parameter* is inherent to the security of the data protection system, as it provides isolation between cryptographic consumers, even if the root cryptographic keys are the same.</span></span>

<span data-ttu-id="dd30d-106">Lorsqu’un consommateur spécifie un objet, la chaîne d’objectif est utilisée avec les clés de chiffrement racines pour dériver les sous-clés de chiffrement uniques à ce consommateur.</span><span class="sxs-lookup"><span data-stu-id="dd30d-106">When a consumer specifies a purpose, the purpose string is used along with the root cryptographic keys to derive cryptographic subkeys unique to that consumer.</span></span> <span data-ttu-id="dd30d-107">Cela isole le consommateur de tous les autres consommateurs de chiffrement dans l’application : aucun autre composant ne peut lire ses charges utiles, et il ne peut pas lire les charges utiles d’un autre composant.</span><span class="sxs-lookup"><span data-stu-id="dd30d-107">This isolates the consumer from all other cryptographic consumers in the application: no other component can read its payloads, and it cannot read any other component's payloads.</span></span> <span data-ttu-id="dd30d-108">Cette isolation rend également possibles des catégories entières d’attaques contre le composant.</span><span class="sxs-lookup"><span data-stu-id="dd30d-108">This isolation also renders infeasible entire categories of attack against the component.</span></span>

![Exemple de diagramme d’objet](purpose-strings/_static/purposes.png)

<span data-ttu-id="dd30d-110">Dans le diagramme ci-dessus, les `IDataProtector` instances A et B **ne peuvent pas** lire les charges utiles les unes des autres, mais uniquement les siennes.</span><span class="sxs-lookup"><span data-stu-id="dd30d-110">In the diagram above, `IDataProtector` instances A and B **cannot** read each other's payloads, only their own.</span></span>

<span data-ttu-id="dd30d-111">La chaîne d’objectif n’a pas besoin d’être secrète.</span><span class="sxs-lookup"><span data-stu-id="dd30d-111">The purpose string doesn't have to be secret.</span></span> <span data-ttu-id="dd30d-112">Il doit tout simplement être unique dans le sens où aucun autre composant correct ne fournira jamais la même chaîne d’objectif.</span><span class="sxs-lookup"><span data-stu-id="dd30d-112">It should simply be unique in the sense that no other well-behaved component will ever provide the same purpose string.</span></span>

>[!TIP]
> <span data-ttu-id="dd30d-113">L’utilisation de l’espace de noms et du nom de type du composant consommant les API de protection des données est une bonne règle empirique, comme dans la pratique, ces informations ne seront jamais en conflit.</span><span class="sxs-lookup"><span data-stu-id="dd30d-113">Using the namespace and type name of the component consuming the data protection APIs is a good rule of thumb, as in practice this information will never conflict.</span></span>
>
><span data-ttu-id="dd30d-114">Un composant créé par Contoso qui est responsable de la frappe des jetons du porteur peut utiliser contoso. Security. BearerToken comme chaîne d’objet.</span><span class="sxs-lookup"><span data-stu-id="dd30d-114">A Contoso-authored component which is responsible for minting bearer tokens might use Contoso.Security.BearerToken as its purpose string.</span></span> <span data-ttu-id="dd30d-115">Ou bien mieux, il peut utiliser contoso. Security. BearerToken. v1 comme chaîne d’objet.</span><span class="sxs-lookup"><span data-stu-id="dd30d-115">Or - even better - it might use Contoso.Security.BearerToken.v1 as its purpose string.</span></span> <span data-ttu-id="dd30d-116">Le fait d’ajouter le numéro de version permet à une version ultérieure d’utiliser contoso. Security. BearerToken. v2 comme objectif, et les différentes versions sont complètement isolées les unes des autres en ce qui concerne les charges utiles.</span><span class="sxs-lookup"><span data-stu-id="dd30d-116">Appending the version number allows a future version to use Contoso.Security.BearerToken.v2 as its purpose, and the different versions would be completely isolated from one another as far as payloads go.</span></span>

<span data-ttu-id="dd30d-117">Étant donné que le paramètre de l’objectif `CreateProtector` est un tableau de chaînes, la valeur ci-dessus aurait été spécifiée à la place comme `[ "Contoso.Security.BearerToken", "v1" ]` .</span><span class="sxs-lookup"><span data-stu-id="dd30d-117">Since the purposes parameter to `CreateProtector` is a string array, the above could've been instead specified as `[ "Contoso.Security.BearerToken", "v1" ]`.</span></span> <span data-ttu-id="dd30d-118">Cela permet d’établir une hiérarchie d’objectifs et de faire en sorte que les scénarios d’architecture mutualisée soient possibles avec le système de protection des données.</span><span class="sxs-lookup"><span data-stu-id="dd30d-118">This allows establishing a hierarchy of purposes and opens up the possibility of multi-tenancy scenarios with the data protection system.</span></span>

<a name="data-protection-contoso-purpose"></a>

>[!WARNING]
> <span data-ttu-id="dd30d-119">Les composants ne doivent pas autoriser une entrée utilisateur non fiable comme seule source d’entrée pour la chaîne de rôles.</span><span class="sxs-lookup"><span data-stu-id="dd30d-119">Components shouldn't allow untrusted user input to be the sole source of input for the purposes chain.</span></span>
>
><span data-ttu-id="dd30d-120">Par exemple, considérez un composant contoso. Messaging. SecureMessage qui est responsable du stockage des messages sécurisés.</span><span class="sxs-lookup"><span data-stu-id="dd30d-120">For example, consider a component Contoso.Messaging.SecureMessage which is responsible for storing secure messages.</span></span> <span data-ttu-id="dd30d-121">Si le composant de messagerie sécurisée devait appeler `CreateProtector([ username ])` , un utilisateur malveillant pourrait créer un compte avec le nom d’utilisateur « contoso. Security. BearerToken » pour tenter d’obtenir le composant à appeler `CreateProtector([ "Contoso.Security.BearerToken" ])` , provoquant par inadvertance le système de messagerie sécurisée pour les charges utiles susceptibles d’être perçues comme des jetons d’authentification.</span><span class="sxs-lookup"><span data-stu-id="dd30d-121">If the secure messaging component were to call `CreateProtector([ username ])`, then a malicious user might create an account with username "Contoso.Security.BearerToken" in an attempt to get the component to call `CreateProtector([ "Contoso.Security.BearerToken" ])`, thus inadvertently causing the secure messaging system to mint payloads that could be perceived as authentication tokens.</span></span>
>
><span data-ttu-id="dd30d-122">Une chaîne plus efficace pour le composant de messagerie serait `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])` , ce qui fournit une isolation appropriée.</span><span class="sxs-lookup"><span data-stu-id="dd30d-122">A better purposes chain for the messaging component would be `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])`, which provides proper isolation.</span></span>

<span data-ttu-id="dd30d-123">L’isolation fournie par et les comportements des `IDataProtectionProvider` `IDataProtector` objectifs, et sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="dd30d-123">The isolation provided by and behaviors of `IDataProtectionProvider`, `IDataProtector`, and purposes are as follows:</span></span>

* <span data-ttu-id="dd30d-124">Pour un `IDataProtectionProvider` objet donné, la `CreateProtector` méthode crée un `IDataProtector` objet qui est lié de manière unique à l' `IDataProtectionProvider` objet qui l’a créé et au paramètre usage qui a été passé dans la méthode.</span><span class="sxs-lookup"><span data-stu-id="dd30d-124">For a given `IDataProtectionProvider` object, the `CreateProtector` method will create an `IDataProtector` object uniquely tied to both the `IDataProtectionProvider` object which created it and the purposes parameter which was passed into the method.</span></span>

* <span data-ttu-id="dd30d-125">Le paramètre Purpose ne doit pas avoir la valeur null.</span><span class="sxs-lookup"><span data-stu-id="dd30d-125">The purpose parameter must not be null.</span></span> <span data-ttu-id="dd30d-126">(Si l’objectif est spécifié sous la forme d’un tableau, cela signifie que le tableau ne doit pas avoir une longueur de zéro et que tous les éléments du tableau doivent être non null.) Une fonction de chaîne vide est techniquement autorisée, mais elle est déconseillée.</span><span class="sxs-lookup"><span data-stu-id="dd30d-126">(If purposes is specified as an array, this means that the array must not be of zero length and all elements of the array must be non-null.) An empty string purpose is technically allowed but is discouraged.</span></span>

* <span data-ttu-id="dd30d-127">Les arguments à deux objectifs sont équivalents si et seulement s’ils contiennent les mêmes chaînes (à l’aide d’un comparateur ordinal) dans le même ordre.</span><span class="sxs-lookup"><span data-stu-id="dd30d-127">Two purposes arguments are equivalent if and only if they contain the same strings (using an ordinal comparer) in the same order.</span></span> <span data-ttu-id="dd30d-128">Un argument à rôle unique est équivalent au tableau d’objectifs à un seul élément correspondant.</span><span class="sxs-lookup"><span data-stu-id="dd30d-128">A single purpose argument is equivalent to the corresponding single-element purposes array.</span></span>

* <span data-ttu-id="dd30d-129">Deux `IDataProtector` objets sont équivalents si et seulement s’ils sont créés à partir d’objets équivalents `IDataProtectionProvider` à des paramètres équivalents.</span><span class="sxs-lookup"><span data-stu-id="dd30d-129">Two `IDataProtector` objects are equivalent if and only if they're created from equivalent `IDataProtectionProvider` objects with equivalent purposes parameters.</span></span>

* <span data-ttu-id="dd30d-130">Pour un `IDataProtector` objet donné, un appel à `Unprotect(protectedData)` retourne la valeur d’origine `unprotectedData` si et seulement si `protectedData := Protect(unprotectedData)` un objet équivalent est présent `IDataProtector` .</span><span class="sxs-lookup"><span data-stu-id="dd30d-130">For a given `IDataProtector` object, a call to `Unprotect(protectedData)` will return the original `unprotectedData` if and only if `protectedData := Protect(unprotectedData)` for an equivalent `IDataProtector` object.</span></span>

> [!NOTE]
> <span data-ttu-id="dd30d-131">Nous n’envisageons pas le cas où un composant choisit intentionnellement une chaîne d’objectif connue pour être en conflit avec un autre composant.</span><span class="sxs-lookup"><span data-stu-id="dd30d-131">We're not considering the case where some component intentionally chooses a purpose string which is known to conflict with another component.</span></span> <span data-ttu-id="dd30d-132">Ce type de composant serait essentiellement considéré comme malveillant et ce système n’est pas destiné à fournir des garanties de sécurité dans le cas où du code malveillant est déjà en cours d’exécution au sein du processus de travail.</span><span class="sxs-lookup"><span data-stu-id="dd30d-132">Such a component would essentially be considered malicious, and this system isn't intended to provide security guarantees in the event that malicious code is already running inside of the worker process.</span></span>
