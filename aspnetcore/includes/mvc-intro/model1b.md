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
ms.openlocfilehash: 3c21d2a5604c2dfc96624fb5d161537797925fef
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552343"
---
<span data-ttu-id="7d1a4-101">Ajoutez les propriétés suivantes à la classe `Movie` :</span><span class="sxs-lookup"><span data-stu-id="7d1a4-101">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="7d1a4-102">La classe `Movie` contient :</span><span class="sxs-lookup"><span data-stu-id="7d1a4-102">The `Movie` class contains:</span></span>

* <span data-ttu-id="7d1a4-103">Le champ `Id`, qui est nécessaire à la base de données pour la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="7d1a4-103">The `Id` field which is required by the database for the primary key.</span></span>
* <span data-ttu-id="7d1a4-104">`[DataType(DataType.Date)]`: L’attribut [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) spécifie le type des données ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="7d1a4-104">`[DataType(DataType.Date)]`:  The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="7d1a4-105">Avec cet attribut :</span><span class="sxs-lookup"><span data-stu-id="7d1a4-105">With this attribute:</span></span>

  * <span data-ttu-id="7d1a4-106">L’utilisateur n’est pas obligé d’entrer les informations de temps dans le champ de date.</span><span class="sxs-lookup"><span data-stu-id="7d1a4-106">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="7d1a4-107">Seule la date est affichée, pas les informations de temps.</span><span class="sxs-lookup"><span data-stu-id="7d1a4-107">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="7d1a4-108">Les [DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) sont traitées dans un prochain didacticiel.</span><span class="sxs-lookup"><span data-stu-id="7d1a4-108">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>