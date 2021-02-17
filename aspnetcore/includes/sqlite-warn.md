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
> **Limitations de SQLite**
>
> Ce tutoriel utilise la fonctionnalité *Migrations* d’Entity Framework Core lorsque cela est possible. Les migrations mettent à jour le schéma de la base de données pour qu’elle corresponde aux modifications dans le modèle de données. Toutefois, les migrations effectuent uniquement les types de modifications qui sont pris en charge par le moteur de base de données. En outre, les fonctionnalités de modification du schéma SQLite sont limitées. Par exemple, l’ajout d’une colonne est pris en charge, mais pas sa suppression. Si vous créez une migration pour supprimer une colonne, la commande `ef migrations add` réussit mais la commande `ef database update` échoue. 
>
> Pour remédier aux limitations de SQLite, vous devez écrire le code de migrations manuellement pour regénérer le tableau lorsqu’un élément est modifié. Ce code doit être placé dans les méthodes `Up` et `Down` pour une migration et implique les tâches suivantes :
>
> * La création d’un nouveau tableau.
> * La copie de données de l’ancien tableau vers le nouveau.
> * La suppression de l’ancien tableau.
> * Renommer la nouvelle table.
>
> L’écriture d’un tel code propre à une base de données n’est pas abordée dans ce tutoriel. En effet, ce tutoriel supprime et recrée la base de données chaque fois qu’une tentative d’application d’une migration échoue. Pour plus d’informations, consultez les ressources suivantes :
>
> * [Limites d’un fournisseur de base de données EF Core SQLite](/ef/core/providers/sqlite/limitations)
> * [Personnaliser le code de migration](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Amorçage des données](/ef/core/modeling/data-seeding)
> * [Instruction SQLite ALTER TABLE](https://sqlite.org/lang_altertable.html)