---
title: Présentation de l’autorisation dans ASP.NET Core
author: rick-anderson
description: Découvrez les principes de base de l’autorisation et le fonctionnement de l’autorisation dans les applications ASP.NET Core.
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
uid: security/authorization/introduction
ms.openlocfilehash: 8afee7d8bc6b7ad71154916f4a41a2b417b24f9a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060246"
---
# <a name="introduction-to-authorization-in-aspnet-core"></a><span data-ttu-id="c8060-103">Présentation de l’autorisation dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c8060-103">Introduction to authorization in ASP.NET Core</span></span>

<a name="security-authorization-introduction"></a>

<span data-ttu-id="c8060-104">L’autorisation fait référence au processus qui détermine ce qu’un utilisateur est en mesure de faire.</span><span class="sxs-lookup"><span data-stu-id="c8060-104">Authorization refers to the process that determines what a user is able to do.</span></span> <span data-ttu-id="c8060-105">Par exemple, un utilisateur administratif est autorisé à créer une bibliothèque de documents, à ajouter des documents, à modifier des documents et à les supprimer.</span><span class="sxs-lookup"><span data-stu-id="c8060-105">For example, an administrative user is allowed to create a document library, add documents, edit documents, and delete them.</span></span> <span data-ttu-id="c8060-106">Un utilisateur non-administrateur travaillant avec la bibliothèque est autorisé uniquement à lire les documents.</span><span class="sxs-lookup"><span data-stu-id="c8060-106">A non-administrative user working with the library is only authorized to read the documents.</span></span>

<span data-ttu-id="c8060-107">L’autorisation est orthogonale et indépendante de l’authentification.</span><span class="sxs-lookup"><span data-stu-id="c8060-107">Authorization is orthogonal and independent from authentication.</span></span> <span data-ttu-id="c8060-108">Toutefois, l’autorisation nécessite un mécanisme d’authentification.</span><span class="sxs-lookup"><span data-stu-id="c8060-108">However, authorization requires an authentication mechanism.</span></span> <span data-ttu-id="c8060-109">L’authentification est le processus qui consiste à déterminer l’identité d’un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="c8060-109">Authentication is the process of ascertaining who a user is.</span></span> <span data-ttu-id="c8060-110">L’authentification peut créer une ou plusieurs identités pour l’utilisateur actuel.</span><span class="sxs-lookup"><span data-stu-id="c8060-110">Authentication may create one or more identities for the current user.</span></span>

<span data-ttu-id="c8060-111">Pour plus d’informations sur l’authentification dans ASP.NET Core, consultez <xref:security/authentication/index> .</span><span class="sxs-lookup"><span data-stu-id="c8060-111">For more information about authentication in ASP.NET Core, see <xref:security/authentication/index>.</span></span>

## <a name="authorization-types"></a><span data-ttu-id="c8060-112">Types d’autorisation</span><span class="sxs-lookup"><span data-stu-id="c8060-112">Authorization types</span></span>

<span data-ttu-id="c8060-113">ASP.NET Core autorisation fournit un [rôle](xref:security/authorization/roles) simple, déclaratif et un modèle riche [basé sur des stratégies](xref:security/authorization/policies) .</span><span class="sxs-lookup"><span data-stu-id="c8060-113">ASP.NET Core authorization provides a simple, declarative [role](xref:security/authorization/roles) and a rich [policy-based](xref:security/authorization/policies) model.</span></span> <span data-ttu-id="c8060-114">L’autorisation est exprimée en spécifications et les gestionnaires évaluent les revendications d’un utilisateur par rapport aux exigences.</span><span class="sxs-lookup"><span data-stu-id="c8060-114">Authorization is expressed in requirements, and handlers evaluate a user's claims against requirements.</span></span> <span data-ttu-id="c8060-115">Les vérifications impératives peuvent reposer sur des stratégies ou des stratégies simples qui évaluent à la fois l’identité de l’utilisateur et les propriétés de la ressource à laquelle l’utilisateur tente d’accéder.</span><span class="sxs-lookup"><span data-stu-id="c8060-115">Imperative checks can be based on simple policies or policies which evaluate both the user identity and properties of the resource that the user is attempting to access.</span></span>

## <a name="namespaces"></a><span data-ttu-id="c8060-116">Espaces de noms</span><span class="sxs-lookup"><span data-stu-id="c8060-116">Namespaces</span></span>

<span data-ttu-id="c8060-117">Les composants d’autorisation, y compris les `AuthorizeAttribute` `AllowAnonymousAttribute` attributs et, se trouvent dans l' `Microsoft.AspNetCore.Authorization` espace de noms.</span><span class="sxs-lookup"><span data-stu-id="c8060-117">Authorization components, including the `AuthorizeAttribute` and `AllowAnonymousAttribute` attributes, are found in the `Microsoft.AspNetCore.Authorization` namespace.</span></span>

<span data-ttu-id="c8060-118">Consultez la documentation sur l' [autorisation simple](xref:security/authorization/simple).</span><span class="sxs-lookup"><span data-stu-id="c8060-118">Consult the documentation on [simple authorization](xref:security/authorization/simple).</span></span>
