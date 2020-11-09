---
title: Fournisseurs de protection des données éphémères dans ASP.NET Core
author: rick-anderson
description: Découvrez les détails de l’implémentation des fournisseurs de protection des données ASP.NET Core éphémères.
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
uid: security/data-protection/implementation/key-storage-ephemeral
ms.openlocfilehash: ed0fca88ecf2b002a4421fb120d90adff1b5b12e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052696"
---
# <a name="ephemeral-data-protection-providers-in-aspnet-core"></a><span data-ttu-id="10a96-103">Fournisseurs de protection des données éphémères dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="10a96-103">Ephemeral data protection providers in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-storage-ephemeral"></a>

<span data-ttu-id="10a96-104">Il existe des scénarios dans lesquels une application a besoin d’un inepte `IDataProtectionProvider` .</span><span class="sxs-lookup"><span data-stu-id="10a96-104">There are scenarios where an application needs a throwaway `IDataProtectionProvider`.</span></span> <span data-ttu-id="10a96-105">Par exemple, le développeur peut simplement expérimenter une application console unique, ou l’application elle-même est temporaire (il s’agit d’un script ou d’un projet de test unitaire).</span><span class="sxs-lookup"><span data-stu-id="10a96-105">For example, the developer might just be experimenting in a one-off console application, or the application itself is transient (it's scripted or a unit test project).</span></span> <span data-ttu-id="10a96-106">Pour prendre en charge ces scénarios, le package [Microsoft. AspNetCore. dataprotection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) comprend un type `EphemeralDataProtectionProvider` .</span><span class="sxs-lookup"><span data-stu-id="10a96-106">To support these scenarios the [Microsoft.AspNetCore.DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) package includes a type `EphemeralDataProtectionProvider`.</span></span> <span data-ttu-id="10a96-107">Ce type fournit une implémentation de base de `IDataProtectionProvider` dont le dépôt de clé est détenu uniquement en mémoire et n’est pas écrit dans un magasin de stockage.</span><span class="sxs-lookup"><span data-stu-id="10a96-107">This type provides a basic implementation of `IDataProtectionProvider` whose key repository is held solely in-memory and isn't written out to any backing store.</span></span>

<span data-ttu-id="10a96-108">Chaque instance de `EphemeralDataProtectionProvider` utilise sa propre clé principale unique.</span><span class="sxs-lookup"><span data-stu-id="10a96-108">Each instance of `EphemeralDataProtectionProvider` uses its own unique master key.</span></span> <span data-ttu-id="10a96-109">Par conséquent, si une `IDataProtector` racine à un `EphemeralDataProtectionProvider` génère une charge utile protégée, cette charge utile ne peut être déprotégée que par un équivalent `IDataProtector` (à partir de la même chaîne d' [objectif](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes) ) associé à la même `EphemeralDataProtectionProvider` instance.</span><span class="sxs-lookup"><span data-stu-id="10a96-109">Therefore, if an `IDataProtector` rooted at an `EphemeralDataProtectionProvider` generates a protected payload, that payload can only be unprotected by an equivalent `IDataProtector` (given the same [purpose](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes) chain) rooted at the same `EphemeralDataProtectionProvider` instance.</span></span>

<span data-ttu-id="10a96-110">L’exemple suivant illustre l’instanciation d’un `EphemeralDataProtectionProvider` et son utilisation pour protéger et ôter la protection des données.</span><span class="sxs-lookup"><span data-stu-id="10a96-110">The following sample demonstrates instantiating an `EphemeralDataProtectionProvider` and using it to protect and unprotect data.</span></span>

```csharp
using System;
using Microsoft.AspNetCore.DataProtection;

public class Program
{
    public static void Main(string[] args)
    {
        const string purpose = "Ephemeral.App.v1";

        // create an ephemeral provider and demonstrate that it can round-trip a payload
        var provider = new EphemeralDataProtectionProvider();
        var protector = provider.CreateProtector(purpose);
        Console.Write("Enter input: ");
        string input = Console.ReadLine();

        // protect the payload
        string protectedPayload = protector.Protect(input);
        Console.WriteLine($"Protect returned: {protectedPayload}");

        // unprotect the payload
        string unprotectedPayload = protector.Unprotect(protectedPayload);
        Console.WriteLine($"Unprotect returned: {unprotectedPayload}");

        // if I create a new ephemeral provider, it won't be able to unprotect existing
        // payloads, even if I specify the same purpose
        provider = new EphemeralDataProtectionProvider();
        protector = provider.CreateProtector(purpose);
        unprotectedPayload = protector.Unprotect(protectedPayload); // THROWS
    }
}

/*
* SAMPLE OUTPUT
*
* Enter input: Hello!
* Protect returned: CfDJ8AAAAAAAAAAAAAAAAAAAAA...uGoxWLjGKtm1SkNACQ
* Unprotect returned: Hello!
* << throws CryptographicException >>
*/
```
