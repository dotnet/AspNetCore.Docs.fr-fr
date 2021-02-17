---
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
ms.openlocfilehash: 6808c71b1ca43755eea4958ff9409f40e8685694
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551505"
---
<span data-ttu-id="060e5-101">Ce didacticiel décrit ASP.NET Core MVC et Entity Framework Core avec des contrôleurs et des vues.</span><span class="sxs-lookup"><span data-stu-id="060e5-101">This tutorial teaches ASP.NET Core MVC and Entity Framework Core with controllers and views.</span></span> <span data-ttu-id="060e5-102">[ Razor Pages](xref:razor-pages/index) est un modèle de programmation alternatif.</span><span class="sxs-lookup"><span data-stu-id="060e5-102">[Razor Pages](xref:razor-pages/index) is an alternative programming model.</span></span> <span data-ttu-id="060e5-103">Pour les nouveaux développements, nous recommandons des Razor pages sur MVC avec des contrôleurs et des vues.</span><span class="sxs-lookup"><span data-stu-id="060e5-103">For new development, we recommend Razor Pages over MVC with controllers and views.</span></span> <span data-ttu-id="060e5-104">Consultez la version [ Razor pages](xref:data/ef-rp/intro) de ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="060e5-104">See the [Razor Pages](xref:data/ef-rp/intro) version of this tutorial.</span></span> <span data-ttu-id="060e5-105">Chaque tutoriel couvre des sujets que l’autre ne couvre pas :</span><span class="sxs-lookup"><span data-stu-id="060e5-105">Each tutorial covers some material the other doesn't:</span></span>

<span data-ttu-id="060e5-106">Ce didacticiel MVC présente certaines choses que le Razor didacticiel sur les pages ne peut pas :</span><span class="sxs-lookup"><span data-stu-id="060e5-106">Some things this MVC tutorial has that the Razor Pages tutorial doesn't:</span></span>

* <span data-ttu-id="060e5-107">Implémenter l’héritage dans le modèle de données</span><span class="sxs-lookup"><span data-stu-id="060e5-107">Implement inheritance in the data model</span></span>
* <span data-ttu-id="060e5-108">Exécuter des requêtes SQL brutes</span><span class="sxs-lookup"><span data-stu-id="060e5-108">Perform raw SQL queries</span></span>
* <span data-ttu-id="060e5-109">Utiliser du code dynamique LINQ pour simplifier le code</span><span class="sxs-lookup"><span data-stu-id="060e5-109">Use dynamic LINQ to simplify code</span></span>

<span data-ttu-id="060e5-110">Le didacticiel sur les Razor pages présente ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="060e5-110">Some things the Razor Pages tutorial has that this one doesn't:</span></span>

* <span data-ttu-id="060e5-111">Utiliser la méthode Select pour charger les données associées</span><span class="sxs-lookup"><span data-stu-id="060e5-111">Use Select method to load related data</span></span>
* <span data-ttu-id="060e5-112">Meilleures pratiques pour EF.</span><span class="sxs-lookup"><span data-stu-id="060e5-112">Best practices for EF.</span></span>