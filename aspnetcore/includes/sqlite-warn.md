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
ms.openlocfilehash: cf20722e8c8669fb17af8db032d4064ca2be2f4c
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551600"
---
> [!NOTE]
> 
> <span data-ttu-id="6da2f-101">**Limitations de SQLite**</span><span class="sxs-lookup"><span data-stu-id="6da2f-101">**SQLite limitations**</span></span>
>
> <span data-ttu-id="6da2f-102">Ce tutoriel utilise la fonctionnalité *Migrations* d’Entity Framework Core lorsque cela est possible.</span><span class="sxs-lookup"><span data-stu-id="6da2f-102">This tutorial uses the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="6da2f-103">Les migrations mettent à jour le schéma de la base de données pour qu’elle corresponde aux modifications dans le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="6da2f-103">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="6da2f-104">Toutefois, les migrations effectuent uniquement les types de modifications qui sont pris en charge par le moteur de base de données. En outre, les fonctionnalités de modification du schéma SQLite sont limitées.</span><span class="sxs-lookup"><span data-stu-id="6da2f-104">However, migrations only does the kinds of changes that the database engine supports, and SQLite's schema change capabilities are limited.</span></span> <span data-ttu-id="6da2f-105">Par exemple, l’ajout d’une colonne est pris en charge, mais pas sa suppression.</span><span class="sxs-lookup"><span data-stu-id="6da2f-105">For example, adding a column is supported, but removing a column is not supported.</span></span> <span data-ttu-id="6da2f-106">Si vous créez une migration pour supprimer une colonne, la commande `ef migrations add` réussit mais la commande `ef database update` échoue.</span><span class="sxs-lookup"><span data-stu-id="6da2f-106">If a migration is created to remove a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> 
>
> <span data-ttu-id="6da2f-107">Pour remédier aux limitations de SQLite, vous devez écrire le code de migrations manuellement pour regénérer le tableau lorsqu’un élément est modifié.</span><span class="sxs-lookup"><span data-stu-id="6da2f-107">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="6da2f-108">Ce code doit être placé dans les méthodes `Up` et `Down` pour une migration et implique les tâches suivantes :</span><span class="sxs-lookup"><span data-stu-id="6da2f-108">The code would go in the `Up` and `Down` methods for a migration and would involve:</span></span>
>
> * <span data-ttu-id="6da2f-109">La création d’un nouveau tableau.</span><span class="sxs-lookup"><span data-stu-id="6da2f-109">Creating a new table.</span></span>
> * <span data-ttu-id="6da2f-110">La copie de données de l’ancien tableau vers le nouveau.</span><span class="sxs-lookup"><span data-stu-id="6da2f-110">Copying data from the old table to the new table.</span></span>
> * <span data-ttu-id="6da2f-111">La suppression de l’ancien tableau.</span><span class="sxs-lookup"><span data-stu-id="6da2f-111">Dropping the old table.</span></span>
> * <span data-ttu-id="6da2f-112">Renommer la nouvelle table.</span><span class="sxs-lookup"><span data-stu-id="6da2f-112">Renaming the new table.</span></span>
>
> <span data-ttu-id="6da2f-113">L’écriture d’un tel code propre à une base de données n’est pas abordée dans ce tutoriel.</span><span class="sxs-lookup"><span data-stu-id="6da2f-113">Writing database-specific code of this type is outside the scope of this tutorial.</span></span> <span data-ttu-id="6da2f-114">En effet, ce tutoriel supprime et recrée la base de données chaque fois qu’une tentative d’application d’une migration échoue.</span><span class="sxs-lookup"><span data-stu-id="6da2f-114">Instead, this tutorial drops and re-creates the database whenever an attempt to apply a migration would fail.</span></span> <span data-ttu-id="6da2f-115">Pour plus d’informations, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="6da2f-115">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="6da2f-116">Limites d’un fournisseur de base de données EF Core SQLite</span><span class="sxs-lookup"><span data-stu-id="6da2f-116">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="6da2f-117">Personnaliser le code de migration</span><span class="sxs-lookup"><span data-stu-id="6da2f-117">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="6da2f-118">Amorçage des données</span><span class="sxs-lookup"><span data-stu-id="6da2f-118">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="6da2f-119">Instruction SQLite ALTER TABLE</span><span class="sxs-lookup"><span data-stu-id="6da2f-119">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)