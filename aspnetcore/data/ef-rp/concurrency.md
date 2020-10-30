---
title: 'Partie 8, :::no-loc(Razor)::: pages avec EF Core dans ASP.net Core-concurrence'
author: rick-anderson
description: 'Partie 8 des :::no-loc(Razor)::: pages et Entity Framework de la série de didacticiels.'
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
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
uid: data/ef-rp/concurrency
ms.openlocfilehash: 573a509041bfb34faf50a227c451824db03f92ee
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053993"
---
# <a name="part-8-no-locrazor-pages-with-ef-core-in-aspnet-core---concurrency"></a><span data-ttu-id="56da2-103">Partie 8, :::no-loc(Razor)::: pages avec EF Core dans ASP.net Core-concurrence</span><span class="sxs-lookup"><span data-stu-id="56da2-103">Part 8, :::no-loc(Razor)::: Pages with EF Core in ASP.NET Core - Concurrency</span></span>

<span data-ttu-id="56da2-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra)et [Jon P Smith](https://twitter.com/thereformedprog)</span><span class="sxs-lookup"><span data-stu-id="56da2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), and [Jon P Smith](https://twitter.com/thereformedprog)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="56da2-105">Ce didacticiel montre comment gérer les conflits quand plusieurs utilisateurs mettent à jour une entité en même temps.</span><span class="sxs-lookup"><span data-stu-id="56da2-105">This tutorial shows how to handle conflicts when multiple users update an entity concurrently (at the same time).</span></span>

## <a name="concurrency-conflicts"></a><span data-ttu-id="56da2-106">Conflits d’accès concurrentiel</span><span class="sxs-lookup"><span data-stu-id="56da2-106">Concurrency conflicts</span></span>

<span data-ttu-id="56da2-107">Un conflit d’accès concurrentiel se produit quand :</span><span class="sxs-lookup"><span data-stu-id="56da2-107">A concurrency conflict occurs when:</span></span>

* <span data-ttu-id="56da2-108">Un utilisateur accède à la page de modification d’une entité.</span><span class="sxs-lookup"><span data-stu-id="56da2-108">A user navigates to the edit page for an entity.</span></span>
* <span data-ttu-id="56da2-109">Un autre utilisateur met à jour la même entité avant que la modification du premier utilisateur soit écrite dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="56da2-109">Another user updates the same entity before the first user's change is written to the database.</span></span>

<span data-ttu-id="56da2-110">Si la détection de l’accès concurrentiel n’est pas activée, quiconque qui met à jour la base de données en dernier remplace les modifications de l’autre utilisateur.</span><span class="sxs-lookup"><span data-stu-id="56da2-110">If concurrency detection isn't enabled, whoever updates the database last overwrites the other user's changes.</span></span> <span data-ttu-id="56da2-111">Si ce risque est acceptable, le coût de programmation de l’accès concurrentiel peut l’emporter sur l’avantage.</span><span class="sxs-lookup"><span data-stu-id="56da2-111">If this risk is acceptable, the cost of programming for concurrency might outweigh the benefit.</span></span>

### <a name="pessimistic-concurrency-locking"></a><span data-ttu-id="56da2-112">Accès concurrentiel pessimiste (verrouillage)</span><span class="sxs-lookup"><span data-stu-id="56da2-112">Pessimistic concurrency (locking)</span></span>

<span data-ttu-id="56da2-113">Une façon d’éviter les conflits d’accès concurrentiel consiste à utiliser des verrous de base de données.</span><span class="sxs-lookup"><span data-stu-id="56da2-113">One way to prevent concurrency conflicts is to use database locks.</span></span> <span data-ttu-id="56da2-114">Ceci est appelé « accès concurrentiel pessimiste ».</span><span class="sxs-lookup"><span data-stu-id="56da2-114">This is called pessimistic concurrency.</span></span> <span data-ttu-id="56da2-115">Avant de lire une ligne de base de données qu’elle entend mettre à jour, une application demande un verrou.</span><span class="sxs-lookup"><span data-stu-id="56da2-115">Before the app reads a database row that it intends to update, it requests a lock.</span></span> <span data-ttu-id="56da2-116">Dès lors qu’une ligne est verrouillée pour l’accès aux mises à jour, aucun autre utilisateur n’est autorisé à verrouiller la ligne tant que le premier verrou n’est pas libéré.</span><span class="sxs-lookup"><span data-stu-id="56da2-116">Once a row is locked for update access, no other users are allowed to lock the row until the first lock is released.</span></span>

<span data-ttu-id="56da2-117">La gestion des verrous présente des inconvénients.</span><span class="sxs-lookup"><span data-stu-id="56da2-117">Managing locks has disadvantages.</span></span> <span data-ttu-id="56da2-118">Elle peut être difficile à programmer et peut occasionner des problèmes de performances à mesure que le nombre d’utilisateurs augmente.</span><span class="sxs-lookup"><span data-stu-id="56da2-118">It can be complex to program and can cause performance problems as the number of users increases.</span></span> <span data-ttu-id="56da2-119">Entity Framework Core n’assure pas de prise en charge intégrée pour celle-ci et ce tutoriel ne vous montre pas comment l’implémenter.</span><span class="sxs-lookup"><span data-stu-id="56da2-119">Entity Framework Core provides no built-in support for it, and this tutorial doesn't show how to implement it.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="56da2-120">Accès concurrentiel optimiste</span><span class="sxs-lookup"><span data-stu-id="56da2-120">Optimistic concurrency</span></span>

<span data-ttu-id="56da2-121">L’accès concurrentiel optimiste autorise la survenance des conflits d’accès concurrentiel, et réagit correctement quand ils surviennent.</span><span class="sxs-lookup"><span data-stu-id="56da2-121">Optimistic concurrency allows concurrency conflicts to happen, and then reacts appropriately when they do.</span></span> <span data-ttu-id="56da2-122">Par exemple, Jane consulte la page de modification de département et change le montant de « Budget » pour le département « English » en le faisant passer de 350 000,00 $ à 0,00 $.</span><span class="sxs-lookup"><span data-stu-id="56da2-122">For example, Jane visits the Department edit page and changes the budget for the English department from $350,000.00 to $0.00.</span></span>

![Modification de la valeur de budget sur 0](concurrency/_static/change-budget30.png)

<span data-ttu-id="56da2-124">Avant que Jane clique sur **Save** , John consulte la même page et change le champ Start Date de 01/09/2007 en 01/09/2013.</span><span class="sxs-lookup"><span data-stu-id="56da2-124">Before Jane clicks **Save** , John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![Modification de la date de début sur 2013](concurrency/_static/change-date30.png)

<span data-ttu-id="56da2-126">Jane clique d’abord sur **Save** et voit sa modification prendre effet, puisque le navigateur affiche la page d’index avec un montant de budget égal à zéro.</span><span class="sxs-lookup"><span data-stu-id="56da2-126">Jane clicks **Save** first and sees her change take effect, since the browser displays the Index page with zero as the Budget amount.</span></span>

<span data-ttu-id="56da2-127">John clique sur **Save** dans une page Edit qui affiche toujours un budget de 350 000,00 $.</span><span class="sxs-lookup"><span data-stu-id="56da2-127">John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="56da2-128">Ce qui se passe ensuite dépend de la façon dont vous gérez les conflits d’accès concurrentiel :</span><span class="sxs-lookup"><span data-stu-id="56da2-128">What happens next is determined by how you handle concurrency conflicts:</span></span>

* <span data-ttu-id="56da2-129">Vous pouvez effectuer le suivi des propriétés modifiées par un utilisateur et mettre à jour seulement les colonnes correspondantes dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="56da2-129">You can keep track of which property a user has modified and update only the corresponding columns in the database.</span></span>

  <span data-ttu-id="56da2-130">Dans le scénario, aucune donnée ne serait perdue.</span><span class="sxs-lookup"><span data-stu-id="56da2-130">In the scenario, no data would be lost.</span></span> <span data-ttu-id="56da2-131">Des propriétés différentes ont été mises à jour par les deux utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="56da2-131">Different properties were updated by the two users.</span></span> <span data-ttu-id="56da2-132">La prochaine fois que quelqu’un consultera le département « English », il verra à la fois les modifications de Jane et de John.</span><span class="sxs-lookup"><span data-stu-id="56da2-132">The next time someone browses the English department, they will see both Jane's and John's changes.</span></span> <span data-ttu-id="56da2-133">Cette méthode de mise à jour peut réduire le nombre de conflits susceptibles d’entraîner une perte de données.</span><span class="sxs-lookup"><span data-stu-id="56da2-133">This method of updating can reduce the number of conflicts that could result in data loss.</span></span> <span data-ttu-id="56da2-134">Cette approche présente quelques inconvénients :</span><span class="sxs-lookup"><span data-stu-id="56da2-134">This approach has some disadvantages:</span></span>
 
  * <span data-ttu-id="56da2-135">Elle ne peut pas éviter une perte de données si des modifications concurrentes sont apportées à la même propriété.</span><span class="sxs-lookup"><span data-stu-id="56da2-135">Can't avoid data loss if competing changes are made to the same property.</span></span>
  * <span data-ttu-id="56da2-136">Elle n’est généralement pas pratique dans une application web.</span><span class="sxs-lookup"><span data-stu-id="56da2-136">Is generally not practical in a web app.</span></span> <span data-ttu-id="56da2-137">Elle nécessite la tenue à jour d’un état significatif afin d’effectuer le suivi de toutes les valeurs récupérées et des nouvelles valeurs.</span><span class="sxs-lookup"><span data-stu-id="56da2-137">It requires maintaining significant state in order to keep track of all fetched values and new values.</span></span> <span data-ttu-id="56da2-138">La maintenance de grandes quantités d’état peut affecter les performances de l’application.</span><span class="sxs-lookup"><span data-stu-id="56da2-138">Maintaining large amounts of state can affect app performance.</span></span>
  * <span data-ttu-id="56da2-139">Elle peut augmenter la complexité de l’application par rapport à la détection de l’accès concurrentiel sur une entité.</span><span class="sxs-lookup"><span data-stu-id="56da2-139">Can increase app complexity compared to concurrency detection on an entity.</span></span>

* <span data-ttu-id="56da2-140">Vous pouvez laisser les modifications de John remplacer les modifications de Jane.</span><span class="sxs-lookup"><span data-stu-id="56da2-140">You can let John's change overwrite Jane's change.</span></span>

  <span data-ttu-id="56da2-141">La prochaine fois que quelqu’un consultera le département « English », il verra la date 01/09/2013 et la valeur 350 000,00 $ récupérée.</span><span class="sxs-lookup"><span data-stu-id="56da2-141">The next time someone browses the English department, they will see 9/1/2013 and the fetched $350,000.00 value.</span></span> <span data-ttu-id="56da2-142">Cette approche est un scénario *Priorité au client* ou *Priorité au dernier* .</span><span class="sxs-lookup"><span data-stu-id="56da2-142">This approach is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="56da2-143">(Toutes les valeurs du client sont prioritaires par rapport à ce qui se trouve dans le magasin de données.) Si vous n’effectuez aucun codage pour la gestion de l’accès concurrentiel, le client WINS se produit automatiquement.</span><span class="sxs-lookup"><span data-stu-id="56da2-143">(All values from the client take precedence over what's in the data store.) If you don't do any coding for concurrency handling, Client Wins happens automatically.</span></span>

* <span data-ttu-id="56da2-144">Vous pouvez empêcher les modifications de John de faire l’objet d’une mise à jour dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="56da2-144">You can prevent John's change from being updated in the database.</span></span> <span data-ttu-id="56da2-145">En règle générale, l’application :</span><span class="sxs-lookup"><span data-stu-id="56da2-145">Typically, the app would:</span></span>

  * <span data-ttu-id="56da2-146">affiche un message d’erreur ;</span><span class="sxs-lookup"><span data-stu-id="56da2-146">Display an error message.</span></span>
  * <span data-ttu-id="56da2-147">indique l’état actuel des données ;</span><span class="sxs-lookup"><span data-stu-id="56da2-147">Show the current state of the data.</span></span>
  * <span data-ttu-id="56da2-148">autorise l’utilisateur à réappliquer les modifications.</span><span class="sxs-lookup"><span data-stu-id="56da2-148">Allow the user to reapply the changes.</span></span>

  <span data-ttu-id="56da2-149">Il s’agit alors d’un scénario *Priorité au magasin* .</span><span class="sxs-lookup"><span data-stu-id="56da2-149">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="56da2-150">(Les valeurs du magasin de données ont priorité sur les valeurs soumises par le client.) Vous implémentez le scénario de stockage WINS dans ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="56da2-150">(The data-store values take precedence over the values submitted by the client.) You implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="56da2-151">Cette méthode garantit qu’aucune modification n’est remplacée sans qu’un utilisateur soit averti.</span><span class="sxs-lookup"><span data-stu-id="56da2-151">This method ensures that no changes are overwritten without a user being alerted.</span></span>

## <a name="conflict-detection-in-ef-core"></a><span data-ttu-id="56da2-152">Détection de conflits dans EF Core</span><span class="sxs-lookup"><span data-stu-id="56da2-152">Conflict detection in EF Core</span></span>

<span data-ttu-id="56da2-153">EF Core lève des exceptions `DbConcurrencyException` quand il détecte des conflits.</span><span class="sxs-lookup"><span data-stu-id="56da2-153">EF Core throws `DbConcurrencyException` exceptions when it detects conflicts.</span></span> <span data-ttu-id="56da2-154">Le modèle de données doit être configuré pour activer la détection de conflits.</span><span class="sxs-lookup"><span data-stu-id="56da2-154">The data model has to be configured to enable conflict detection.</span></span> <span data-ttu-id="56da2-155">Voici les options qui permettent d’activer la détection de conflits :</span><span class="sxs-lookup"><span data-stu-id="56da2-155">Options for enabling conflict detection include the following:</span></span>

* <span data-ttu-id="56da2-156">Configurez EF Core de façon à inclure les valeurs d’origine des colonnes configurées en tant que [jetons d’accès concurrentiel](/ef/core/modeling/concurrency) dans la clause Where des commandes Update et Delete.</span><span class="sxs-lookup"><span data-stu-id="56da2-156">Configure EF Core to include the original values of columns configured as [concurrency tokens](/ef/core/modeling/concurrency) in the Where clause of Update and Delete commands.</span></span>

  <span data-ttu-id="56da2-157">Lorsque `SaveChanges` est appelé, la clause WHERE recherche les valeurs d’origine de toutes les propriétés annotées avec l' <xref:System.ComponentModel.DataAnnotations.ConcurrencyCheckAttribute> attribut.</span><span class="sxs-lookup"><span data-stu-id="56da2-157">When `SaveChanges` is called, the Where clause looks for the original values of any properties annotated with the <xref:System.ComponentModel.DataAnnotations.ConcurrencyCheckAttribute> attribute.</span></span> <span data-ttu-id="56da2-158">L’instruction update ne trouve pas de ligne à mettre à jour si aucune propriété de jeton d’accès concurrentiel n’a changé depuis la première lecture de la ligne.</span><span class="sxs-lookup"><span data-stu-id="56da2-158">The update statement won't find a row to update if any of the concurrency token properties changed since the row was first read.</span></span> <span data-ttu-id="56da2-159">EF Core interprète cela comme un conflit d’accès concurrentiel.</span><span class="sxs-lookup"><span data-stu-id="56da2-159">EF Core interprets that as a concurrency conflict.</span></span> <span data-ttu-id="56da2-160">Pour les tables de base de données qui comptent de nombreuses colonnes, cette approche peut aboutir à des clauses Where de très grande taille et peut nécessiter de grandes quantités d’états.</span><span class="sxs-lookup"><span data-stu-id="56da2-160">For database tables that have many columns, this approach can result in very large Where clauses, and can require large amounts of state.</span></span> <span data-ttu-id="56da2-161">Par conséquent, cette approche n’est généralement pas recommandée et n’est pas la méthode utilisée dans ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="56da2-161">Therefore this approach is generally not recommended, and it isn't the method used in this tutorial.</span></span>

* <span data-ttu-id="56da2-162">Dans la table de base de données, incluez une colonne de suivi qui peut être utilisée pour déterminer quand une ligne a été modifiée.</span><span class="sxs-lookup"><span data-stu-id="56da2-162">In the database table, include a tracking column that can be used to determine when a row has been changed.</span></span>

  <span data-ttu-id="56da2-163">Dans une base de données SQL Server, le type de données de la colonne de suivi est `rowversion`.</span><span class="sxs-lookup"><span data-stu-id="56da2-163">In a SQL Server database, the data type of the tracking column is `rowversion`.</span></span> <span data-ttu-id="56da2-164">La valeur de `rowversion` est un nombre séquentiel qui est incrémenté chaque fois que la ligne est mise à jour.</span><span class="sxs-lookup"><span data-stu-id="56da2-164">The `rowversion` value is a sequential number that's incremented each time the row is updated.</span></span> <span data-ttu-id="56da2-165">Dans une commande Update ou Delete, la clause Where inclut la valeur d’origine de la colonne de suivi (le numéro de version de la ligne d’origine).</span><span class="sxs-lookup"><span data-stu-id="56da2-165">In an Update or Delete command, the Where clause includes the original value of the tracking column (the original row version number).</span></span> <span data-ttu-id="56da2-166">Si la ligne mise à jour a été modifiée par un autre utilisateur, la valeur de la colonne `rowversion` est différente de la valeur d’origine.</span><span class="sxs-lookup"><span data-stu-id="56da2-166">If the row being updated has been changed by another user, the value in the `rowversion` column is different than the original value.</span></span> <span data-ttu-id="56da2-167">Dans ce cas, l’instruction Update ou Delete ne peut pas trouver la ligne à mettre à jour en raison de la clause Where.</span><span class="sxs-lookup"><span data-stu-id="56da2-167">In that case, the Update or Delete statement can't find the row to update because of the Where clause.</span></span> <span data-ttu-id="56da2-168">EF Core lève une exception d’accès concurrentiel quand aucune ligne n’est affectée par une commande Update ou Delete.</span><span class="sxs-lookup"><span data-stu-id="56da2-168">EF Core throws a concurrency exception when no rows are affected by an Update or Delete command.</span></span>

## <a name="add-a-tracking-property"></a><span data-ttu-id="56da2-169">Ajouter une propriété de suivi</span><span class="sxs-lookup"><span data-stu-id="56da2-169">Add a tracking property</span></span>

<span data-ttu-id="56da2-170">Dans *Models/Department.cs* , ajoutez une propriété de suivi nommée RowVersion :</span><span class="sxs-lookup"><span data-stu-id="56da2-170">In *Models/Department.cs* , add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Department.cs?highlight=26,27)]

<span data-ttu-id="56da2-171">L' <xref:System.ComponentModel.DataAnnotations.TimestampAttribute> attribut est ce qui identifie la colonne comme une colonne de suivi d’accès concurrentiel.</span><span class="sxs-lookup"><span data-stu-id="56da2-171">The <xref:System.ComponentModel.DataAnnotations.TimestampAttribute> attribute is what identifies the column as a concurrency tracking column.</span></span> <span data-ttu-id="56da2-172">L’API Fluent est un autre moyen de spécifier la propriété de suivi :</span><span class="sxs-lookup"><span data-stu-id="56da2-172">The fluent API is an alternative way to specify the tracking property:</span></span>

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

# <a name="visual-studio"></a>[<span data-ttu-id="56da2-173">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56da2-173">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="56da2-174">Pour une base de données SQL Server, l’attribut `[Timestamp]` d’une propriété d’entité définie en tant que tableau d’octets :</span><span class="sxs-lookup"><span data-stu-id="56da2-174">For a SQL Server database, the `[Timestamp]` attribute on an entity property defined as byte array:</span></span>

* <span data-ttu-id="56da2-175">Entraîne l’inclusion de la colonne dans les clauses Where des commandes DELETE et UPDATE.</span><span class="sxs-lookup"><span data-stu-id="56da2-175">Causes the column to be included in DELETE and UPDATE WHERE clauses.</span></span>
* <span data-ttu-id="56da2-176">Définit le type de colonne dans la base de données sur [rowversion](/sql/t-sql/data-types/rowversion-transact-sql).</span><span class="sxs-lookup"><span data-stu-id="56da2-176">Sets the column type in the database to [rowversion](/sql/t-sql/data-types/rowversion-transact-sql).</span></span>

<span data-ttu-id="56da2-177">La base de données génère un numéro de version de ligne séquentiel qui est incrémenté chaque fois que la ligne est mise à jour.</span><span class="sxs-lookup"><span data-stu-id="56da2-177">The database generates a sequential row version number that's incremented each time the row is updated.</span></span> <span data-ttu-id="56da2-178">Dans une commande `Update` ou `Delete`, la clause `Where` comprend la valeur de version de ligne récupérée.</span><span class="sxs-lookup"><span data-stu-id="56da2-178">In an `Update` or `Delete` command, the `Where` clause includes the fetched row version value.</span></span> <span data-ttu-id="56da2-179">Si la ligne mise à jour a changé depuis sa récupération :</span><span class="sxs-lookup"><span data-stu-id="56da2-179">If the row being updated has changed since it was fetched:</span></span>

* <span data-ttu-id="56da2-180">La valeur de version de ligne actuelle ne correspond pas à la valeur récupérée.</span><span class="sxs-lookup"><span data-stu-id="56da2-180">The current row version value doesn't match the fetched value.</span></span>
* <span data-ttu-id="56da2-181">Les commandes `Update` ou `Delete` ne trouvent pas de ligne, car la clause `Where` recherche la valeur de version de ligne récupérée.</span><span class="sxs-lookup"><span data-stu-id="56da2-181">The `Update` or `Delete` commands don't find a row because the `Where` clause looks for the fetched row version value.</span></span>
* <span data-ttu-id="56da2-182">Une `DbUpdateConcurrencyException` est levée.</span><span class="sxs-lookup"><span data-stu-id="56da2-182">A `DbUpdateConcurrencyException` is thrown.</span></span>

<span data-ttu-id="56da2-183">Le code suivant montre une partie du T-SQL généré par EF Core quand le nom du département est mis à jour :</span><span class="sxs-lookup"><span data-stu-id="56da2-183">The following code shows a portion of the T-SQL generated by EF Core when the Department name is updated:</span></span>

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=2-3)]

<span data-ttu-id="56da2-184">Le code en surbrillance ci-dessus montre la clause `WHERE` contenant `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="56da2-184">The preceding highlighted code shows the `WHERE` clause containing `RowVersion`.</span></span> <span data-ttu-id="56da2-185">Si la base de données `RowVersion` n’est pas égale au paramètre `RowVersion` (`@p2`), aucune ligne n’est mise à jour.</span><span class="sxs-lookup"><span data-stu-id="56da2-185">If the database `RowVersion` doesn't equal the `RowVersion` parameter (`@p2`), no rows are updated.</span></span>

<span data-ttu-id="56da2-186">Le code en surbrillance suivant montre le T-SQL qui vérifie qu’une seule ligne a été mise à jour :</span><span class="sxs-lookup"><span data-stu-id="56da2-186">The following highlighted code shows the T-SQL that verifies exactly one row was updated:</span></span>

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=4-6)]

<span data-ttu-id="56da2-187">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) retourne le nombre de lignes affectées par la dernière instruction.</span><span class="sxs-lookup"><span data-stu-id="56da2-187">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) returns the number of rows affected by the last statement.</span></span> <span data-ttu-id="56da2-188">Si aucune ligne n’est mise à jour, EF Core lève une exception `DbUpdateConcurrencyException`.</span><span class="sxs-lookup"><span data-stu-id="56da2-188">If no rows are updated, EF Core throws a `DbUpdateConcurrencyException`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="56da2-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="56da2-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="56da2-190">Pour une base de données SQLite, l’attribut `[Timestamp]` d’une propriété d’entité définie en tant que tableau d’octets :</span><span class="sxs-lookup"><span data-stu-id="56da2-190">For a SQLite database, the `[Timestamp]` attribute on an entity property defined as byte array:</span></span>

* <span data-ttu-id="56da2-191">Entraîne l’inclusion de la colonne dans les clauses Where des commandes DELETE et UPDATE.</span><span class="sxs-lookup"><span data-stu-id="56da2-191">Causes the column to be included in DELETE and UPDATE WHERE clauses.</span></span>
* <span data-ttu-id="56da2-192">Mappe à un type de colonne BLOB.</span><span class="sxs-lookup"><span data-stu-id="56da2-192">Maps to a BLOB column type.</span></span>

<span data-ttu-id="56da2-193">Les déclencheurs de base de données mettent à jour la colonne RowVersion avec un nouveau tableau d’octets aléatoire chaque fois qu’une ligne est mise à jour.</span><span class="sxs-lookup"><span data-stu-id="56da2-193">Database triggers update the RowVersion column with a new random byte array whenever a row is updated.</span></span> <span data-ttu-id="56da2-194">Dans une commande `Update` ou `Delete`, la clause `Where` comprend la valeur récupérée de la colonne RowVersion.</span><span class="sxs-lookup"><span data-stu-id="56da2-194">In an `Update` or `Delete` command, the `Where` clause includes the fetched value of the RowVersion column.</span></span> <span data-ttu-id="56da2-195">Si la ligne mise à jour a changé depuis sa récupération :</span><span class="sxs-lookup"><span data-stu-id="56da2-195">If the row being updated has changed since it was fetched:</span></span>

* <span data-ttu-id="56da2-196">La valeur de version de ligne actuelle ne correspond pas à la valeur récupérée.</span><span class="sxs-lookup"><span data-stu-id="56da2-196">The current row version value doesn't match the fetched value.</span></span>
* <span data-ttu-id="56da2-197">Les commandes `Update` ou `Delete` ne trouvent pas de ligne, car la clause `Where` recherche la valeur de version de ligne d’origine.</span><span class="sxs-lookup"><span data-stu-id="56da2-197">The `Update` or `Delete` command doesn't find a row because the `Where` clause looks for the original row version value.</span></span>
* <span data-ttu-id="56da2-198">Une `DbUpdateConcurrencyException` est levée.</span><span class="sxs-lookup"><span data-stu-id="56da2-198">A `DbUpdateConcurrencyException` is thrown.</span></span>

---

### <a name="update-the-database"></a><span data-ttu-id="56da2-199">Mettre à jour la base de données</span><span class="sxs-lookup"><span data-stu-id="56da2-199">Update the database</span></span>

<span data-ttu-id="56da2-200">L’ajout de la propriété `RowVersion` change le modèle de données, ce qui nécessite une migration.</span><span class="sxs-lookup"><span data-stu-id="56da2-200">Adding the `RowVersion` property changes the data model, which requires a migration.</span></span>

<span data-ttu-id="56da2-201">Créez le projet.</span><span class="sxs-lookup"><span data-stu-id="56da2-201">Build the project.</span></span> 

# <a name="visual-studio"></a>[<span data-ttu-id="56da2-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56da2-202">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="56da2-203">Exécutez la commande suivante dans PMC :</span><span class="sxs-lookup"><span data-stu-id="56da2-203">Run the following command in the PMC:</span></span>

  ```powershell
  Add-Migration RowVersion
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="56da2-204">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="56da2-204">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="56da2-205">Exécutez la commande suivante dans un terminal :</span><span class="sxs-lookup"><span data-stu-id="56da2-205">Run the following command in a terminal:</span></span>

  ```dotnetcli
  dotnet ef migrations add RowVersion
  ```

---

<span data-ttu-id="56da2-206">Cette commande :</span><span class="sxs-lookup"><span data-stu-id="56da2-206">This command:</span></span>

* <span data-ttu-id="56da2-207">Crée le fichier de migration *Migrations/{horodatage}_RowVersion.cs* .</span><span class="sxs-lookup"><span data-stu-id="56da2-207">Creates the *Migrations/{time stamp}_RowVersion.cs* migration file.</span></span>
* <span data-ttu-id="56da2-208">Mettent à jour le fichier *Migrations/SchoolContextModelSnapshot.cs* .</span><span class="sxs-lookup"><span data-stu-id="56da2-208">Updates the *Migrations/SchoolContextModelSnapshot.cs* file.</span></span> <span data-ttu-id="56da2-209">La mise à jour ajoute le code en surbrillance suivant à la méthode `BuildModel` :</span><span class="sxs-lookup"><span data-stu-id="56da2-209">The update adds the following highlighted code to the `BuildModel` method:</span></span>

  [!code-csharp[](intro/samples/cu30/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=15-17)]

# <a name="visual-studio"></a>[<span data-ttu-id="56da2-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56da2-210">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="56da2-211">Exécutez la commande suivante dans PMC :</span><span class="sxs-lookup"><span data-stu-id="56da2-211">Run the following command in the PMC:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="56da2-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="56da2-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="56da2-213">Ouvrez le fichier `Migrations/<timestamp>_RowVersion.cs` et ajoutez le code mis en surbrillance :</span><span class="sxs-lookup"><span data-stu-id="56da2-213">Open the `Migrations/<timestamp>_RowVersion.cs` file and add the highlighted code:</span></span>

  [!code-csharp[](intro/samples/cu30/MigrationsSQLite/20190722151951_RowVersion.cs?highlight=16-42)]

  <span data-ttu-id="56da2-214">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="56da2-214">The preceding code:</span></span>

  * <span data-ttu-id="56da2-215">Met à jour les lignes existantes avec des valeurs d’objet blob aléatoires.</span><span class="sxs-lookup"><span data-stu-id="56da2-215">Updates existing rows with random blob values.</span></span>
  * <span data-ttu-id="56da2-216">Ajoute des déclencheurs de base de données qui définissent la colonne RowVersion sur une valeur d’objet blob aléatoire chaque fois qu’une ligne est mise à jour.</span><span class="sxs-lookup"><span data-stu-id="56da2-216">Adds database triggers that set the RowVersion column to a random blob value whenever a row is updated.</span></span>

* <span data-ttu-id="56da2-217">Exécutez la commande suivante dans un terminal :</span><span class="sxs-lookup"><span data-stu-id="56da2-217">Run the following command in a terminal:</span></span>

  ```dotnetcli
  dotnet ef database update
  ```

---

<a name="scaffold"></a>

## <a name="scaffold-department-pages"></a><span data-ttu-id="56da2-218">Générer automatiquement des modèles de pages Department</span><span class="sxs-lookup"><span data-stu-id="56da2-218">Scaffold Department pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="56da2-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56da2-219">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="56da2-220">Suivez les instructions dans [Générer automatiquement des modèles de pages Student](xref:data/ef-rp/intro#scaffold-student-pages) avec les exceptions suivantes :</span><span class="sxs-lookup"><span data-stu-id="56da2-220">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

* <span data-ttu-id="56da2-221">Créez un dossier *Pages/Departments* .</span><span class="sxs-lookup"><span data-stu-id="56da2-221">Create a *Pages/Departments* folder.</span></span>  
* <span data-ttu-id="56da2-222">Utilisez `Department` pour la classe de modèle.</span><span class="sxs-lookup"><span data-stu-id="56da2-222">Use `Department` for the model class.</span></span>
  * <span data-ttu-id="56da2-223">Utilisez la classe de contexte existante au lieu d’en créer une nouvelle.</span><span class="sxs-lookup"><span data-stu-id="56da2-223">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="56da2-224">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="56da2-224">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="56da2-225">Créez un dossier *Pages/Departments* .</span><span class="sxs-lookup"><span data-stu-id="56da2-225">Create a *Pages/Departments* folder.</span></span>

* <span data-ttu-id="56da2-226">Exécutez la commande suivante pour générer automatiquement des modèles de pages Department.</span><span class="sxs-lookup"><span data-stu-id="56da2-226">Run the following command to scaffold the Department pages.</span></span>

  <span data-ttu-id="56da2-227">**Sur Windows :**</span><span class="sxs-lookup"><span data-stu-id="56da2-227">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

  <span data-ttu-id="56da2-228">**Sur Linux ou macOS :**</span><span class="sxs-lookup"><span data-stu-id="56da2-228">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages/Departments --referenceScriptLibraries
  ```

---

<span data-ttu-id="56da2-229">Créez le projet.</span><span class="sxs-lookup"><span data-stu-id="56da2-229">Build the project.</span></span>

## <a name="update-the-index-page"></a><span data-ttu-id="56da2-230">Mettre à jour la page Index</span><span class="sxs-lookup"><span data-stu-id="56da2-230">Update the Index page</span></span>

<span data-ttu-id="56da2-231">L’outil de génération de modèles automatique créé une colonne `RowVersion` pour la page Index, mais ce champ ne s’affiche pas dans une application de production.</span><span class="sxs-lookup"><span data-stu-id="56da2-231">The scaffolding tool created a `RowVersion` column for the Index page, but that field wouldn't be displayed in a production app.</span></span> <span data-ttu-id="56da2-232">Dans ce tutoriel, le dernier octet de `RowVersion` est affiché pour montrer comment la gestion de l’accès concurrentiel fonctionne.</span><span class="sxs-lookup"><span data-stu-id="56da2-232">In this tutorial, the last byte of the `RowVersion` is displayed to help show how concurrency handling works.</span></span> <span data-ttu-id="56da2-233">Il n’est pas garanti que le dernier octet soit unique par lui-même.</span><span class="sxs-lookup"><span data-stu-id="56da2-233">The last byte isn't guaranteed to be unique by itself.</span></span>

<span data-ttu-id="56da2-234">Mettre à jour la page *Pages\Departments\Index.cshtml*  :</span><span class="sxs-lookup"><span data-stu-id="56da2-234">Update *Pages\Departments\Index.cshtml* page:</span></span>

* <span data-ttu-id="56da2-235">Remplacez Index par Departments.</span><span class="sxs-lookup"><span data-stu-id="56da2-235">Replace Index with Departments.</span></span>
* <span data-ttu-id="56da2-236">Modifiez le code contenant `RowVersion` pour afficher uniquement le dernier octet du tableau d’octets.</span><span class="sxs-lookup"><span data-stu-id="56da2-236">Change the code containing `RowVersion` to show just the last byte of the byte array.</span></span>
* <span data-ttu-id="56da2-237">Remplacez FirstMidName par FullName.</span><span class="sxs-lookup"><span data-stu-id="56da2-237">Replace FirstMidName with FullName.</span></span>

<span data-ttu-id="56da2-238">Le code suivant affiche la page mise à jour :</span><span class="sxs-lookup"><span data-stu-id="56da2-238">The following code shows the updated page:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Index.cshtml?highlight=5,8,29,48,51)]

## <a name="update-the-edit-page-model"></a><span data-ttu-id="56da2-239">Mettre à jour le modèle de page de modification</span><span class="sxs-lookup"><span data-stu-id="56da2-239">Update the Edit page model</span></span>

<span data-ttu-id="56da2-240">Mettez à jour *Pages\Departments\Edit.cshtml.cs* à l’aide du code suivant :</span><span class="sxs-lookup"><span data-stu-id="56da2-240">Update *Pages\Departments\Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_All)]

<span data-ttu-id="56da2-241">La <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.OriginalValue> est mise à jour avec la `rowVersion` valeur de l’entité lorsqu’elle a été récupérée dans la `OnGet` méthode.</span><span class="sxs-lookup"><span data-stu-id="56da2-241">The <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.OriginalValue> is updated with the `rowVersion` value from the entity when it was fetched in the `OnGet` method.</span></span> <span data-ttu-id="56da2-242">EF Core génère une commande SQL UPDATE avec une clause WHERE contenant la valeur `RowVersion` d’origine.</span><span class="sxs-lookup"><span data-stu-id="56da2-242">EF Core generates a SQL UPDATE command with a WHERE clause containing the original `RowVersion` value.</span></span> <span data-ttu-id="56da2-243">Si aucune ligne n’est affectée par la commande UPDATE (aucune ligne ne contient la valeur `RowVersion` d’origine), une exception `DbUpdateConcurrencyException` est levée.</span><span class="sxs-lookup"><span data-stu-id="56da2-243">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value), a `DbUpdateConcurrencyException` exception is thrown.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_RowVersion&highlight=17-18)]

<span data-ttu-id="56da2-244">Dans le code en surbrillance précédent :</span><span class="sxs-lookup"><span data-stu-id="56da2-244">In the preceding highlighted code:</span></span>

* <span data-ttu-id="56da2-245">La valeur de `Department.RowVersion` est celle qui se trouvait dans l’entité au moment où elle a été initialement récupérée dans la requête Get pour la page Edit.</span><span class="sxs-lookup"><span data-stu-id="56da2-245">The value in `Department.RowVersion` is what was in the entity when it was originally fetched in the Get request for the Edit page.</span></span> <span data-ttu-id="56da2-246">La valeur est fournie à la `OnPost` méthode par un champ masqué dans la :::no-loc(Razor)::: page qui affiche l’entité à modifier.</span><span class="sxs-lookup"><span data-stu-id="56da2-246">The value is provided to the `OnPost` method by a hidden field in the :::no-loc(Razor)::: page that displays the entity to be edited.</span></span> <span data-ttu-id="56da2-247">La valeur du champ masqué est copiée dans `Department.RowVersion` par le classeur de modèles.</span><span class="sxs-lookup"><span data-stu-id="56da2-247">The hidden field value is copied to `Department.RowVersion` by the model binder.</span></span>
* <span data-ttu-id="56da2-248">`OriginalValue` est la valeur qu’utilisera EF Core dans la clause Where.</span><span class="sxs-lookup"><span data-stu-id="56da2-248">`OriginalValue` is what EF Core will use in the Where clause.</span></span> <span data-ttu-id="56da2-249">Avant l’exécution de la ligne de code en surbrillance, `OriginalValue` a la valeur qui se trouvait dans la base de données au moment où `FirstOrDefaultAsync` été appelé dans cette méthode, laquelle risque d’être différente de celle qui figurait dans la page Edit.</span><span class="sxs-lookup"><span data-stu-id="56da2-249">Before the highlighted line of code executes, `OriginalValue` has the value that was in the database when `FirstOrDefaultAsync` was called in this method, which might be different from what was displayed on the Edit page.</span></span>
* <span data-ttu-id="56da2-250">Le code en surbrillance garantit qu’EF Core utilise la valeur `RowVersion` d’origine de l’entité `Department` affichée dans la clause Where de l’instruction SQL UPDATE.</span><span class="sxs-lookup"><span data-stu-id="56da2-250">The highlighted code makes sure that EF Core uses the original `RowVersion` value from the displayed `Department` entity in the SQL UPDATE statement's Where clause.</span></span>

<span data-ttu-id="56da2-251">Quand une erreur d’accès concurrentiel se produit, le code en surbrillance suivant obtient les valeurs du client (valeurs publiées dans cette méthode) et les valeurs de la base de données.</span><span class="sxs-lookup"><span data-stu-id="56da2-251">When a concurrency error happens, the following highlighted code gets the client values (the values posted to this method) and the database values.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=14,23)]

<span data-ttu-id="56da2-252">Le code suivant ajoute un message d’erreur personnalisé pour chaque colonne dont les valeurs dans la base de données sont différentes de celles envoyées à `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="56da2-252">The following code adds a custom error message for each column that has database values different from what was posted to `OnPostAsync`:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_Error)]

<span data-ttu-id="56da2-253">Le code en surbrillance suivant affecte à `RowVersion` la nouvelle valeur récupérée à partir de la base de données.</span><span class="sxs-lookup"><span data-stu-id="56da2-253">The following highlighted code sets the `RowVersion` value to the new value retrieved from the database.</span></span> <span data-ttu-id="56da2-254">La prochaine fois que l’utilisateur cliquera sur **Save** , seules les erreurs d’accès concurrentiel qui se sont produites depuis le dernier affichage de la page Edit seront interceptées.</span><span class="sxs-lookup"><span data-stu-id="56da2-254">The next time the user clicks **Save** , only concurrency errors that happen since the last display of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=28)]

<span data-ttu-id="56da2-255">L’instruction `ModelState.Remove` est nécessaire car `ModelState` contient l’ancienne valeur `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="56da2-255">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="56da2-256">Dans la :::no-loc(Razor)::: page, la `ModelState` valeur d’un champ est prioritaire sur les valeurs de propriété du modèle lorsque les deux sont présentes.</span><span class="sxs-lookup"><span data-stu-id="56da2-256">In the :::no-loc(Razor)::: Page, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

### <a name="update-the-edit-page"></a><span data-ttu-id="56da2-257">Mettre à jour la page Edit</span><span class="sxs-lookup"><span data-stu-id="56da2-257">Update the Edit page</span></span>

<span data-ttu-id="56da2-258">Mettez à jour *Pages/Departments/Edit.cshtml* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="56da2-258">Update *Pages/Departments/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

<span data-ttu-id="56da2-259">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="56da2-259">The preceding code:</span></span>

* <span data-ttu-id="56da2-260">Il met à jour la directive `page` en remplaçant `@page` par `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="56da2-260">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="56da2-261">Ajoute une version de ligne masquée.</span><span class="sxs-lookup"><span data-stu-id="56da2-261">Adds a hidden row version.</span></span> <span data-ttu-id="56da2-262">`RowVersion` doit être ajouté de sorte que la publication (postback) lie la valeur.</span><span class="sxs-lookup"><span data-stu-id="56da2-262">`RowVersion` must be added so postback binds the value.</span></span>
* <span data-ttu-id="56da2-263">Affiche le dernier octet de `RowVersion` à des fins de débogage.</span><span class="sxs-lookup"><span data-stu-id="56da2-263">Displays the last byte of `RowVersion` for debugging purposes.</span></span>
* <span data-ttu-id="56da2-264">Remplace `ViewData` par le `InstructorNameSL` fortement typé.</span><span class="sxs-lookup"><span data-stu-id="56da2-264">Replaces `ViewData` with the strongly-typed `InstructorNameSL`.</span></span>

### <a name="test-concurrency-conflicts-with-the-edit-page"></a><span data-ttu-id="56da2-265">Tester les conflits d’accès concurrentiel avec la page Edit</span><span class="sxs-lookup"><span data-stu-id="56da2-265">Test concurrency conflicts with the Edit page</span></span>

<span data-ttu-id="56da2-266">Ouvrez deux instances de navigateur de la page Edit sur le département English :</span><span class="sxs-lookup"><span data-stu-id="56da2-266">Open two browsers instances of Edit on the English department:</span></span>

* <span data-ttu-id="56da2-267">Exécutez l’application et sélectionnez Departments.</span><span class="sxs-lookup"><span data-stu-id="56da2-267">Run the app and select Departments.</span></span>
* <span data-ttu-id="56da2-268">Cliquez avec le bouton droit sur le lien hypertexte **Edit** correspondant au département English, puis sélectionnez **Open in new tab** .</span><span class="sxs-lookup"><span data-stu-id="56da2-268">Right-click the **Edit** hyperlink for the English department and select **Open in new tab** .</span></span>
* <span data-ttu-id="56da2-269">Sous le premier onglet, cliquez sur le lien hypertexte **Edit** correspondant au département English.</span><span class="sxs-lookup"><span data-stu-id="56da2-269">In the first tab, click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="56da2-270">Les deux onglets de navigateur affichent les mêmes informations.</span><span class="sxs-lookup"><span data-stu-id="56da2-270">The two browser tabs display the same information.</span></span>

<span data-ttu-id="56da2-271">Changez le nom sous le premier onglet de navigateur, puis cliquez sur **Save** .</span><span class="sxs-lookup"><span data-stu-id="56da2-271">Change the name in the first browser tab and click **Save** .</span></span>

![Page 1 de modification de département après changement](concurrency/_static/edit-after-change-130.png)

<span data-ttu-id="56da2-273">Le navigateur affiche la page Index avec la valeur modifiée et un indicateur rowVersion mis à jour.</span><span class="sxs-lookup"><span data-stu-id="56da2-273">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="56da2-274">Notez l’indicateur rowVersion mis à jour ; il est affiché sur la deuxième publication (postback) sous l’autre onglet.</span><span class="sxs-lookup"><span data-stu-id="56da2-274">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="56da2-275">Changez un champ différent sous le deuxième onglet du navigateur.</span><span class="sxs-lookup"><span data-stu-id="56da2-275">Change a different field in the second browser tab.</span></span>

![Page Edit 2 du département après changement](concurrency/_static/edit-after-change-230.png)

<span data-ttu-id="56da2-277">Cliquez sur **Enregistrer** .</span><span class="sxs-lookup"><span data-stu-id="56da2-277">Click **Save** .</span></span> <span data-ttu-id="56da2-278">Des messages d’erreur s’affichent pour tous les champs qui ne correspondent pas aux valeurs de la base de données :</span><span class="sxs-lookup"><span data-stu-id="56da2-278">You see error messages for all fields that don't match the database values:</span></span>

![Message d’erreur de page de modification de département](concurrency/_static/edit-error30.png)

<span data-ttu-id="56da2-280">Cette fenêtre de navigateur n’avait pas l’intention de changer le champ Name.</span><span class="sxs-lookup"><span data-stu-id="56da2-280">This browser window didn't intend to change the Name field.</span></span> <span data-ttu-id="56da2-281">Copiez et collez la valeur actuelle (Languages) dans le champ Name.</span><span class="sxs-lookup"><span data-stu-id="56da2-281">Copy and paste the current value (Languages) into the Name field.</span></span> <span data-ttu-id="56da2-282">Tabulation. La validation côté client supprime le message d’erreur.</span><span class="sxs-lookup"><span data-stu-id="56da2-282">Tab out. Client-side validation removes the error message.</span></span>

<span data-ttu-id="56da2-283">Cliquez à nouveau sur **Enregistrer** .</span><span class="sxs-lookup"><span data-stu-id="56da2-283">Click **Save** again.</span></span> <span data-ttu-id="56da2-284">La valeur que vous avez entrée sous le deuxième onglet du navigateur est enregistrée.</span><span class="sxs-lookup"><span data-stu-id="56da2-284">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="56da2-285">Les valeurs enregistrées sont visibles dans la page Index.</span><span class="sxs-lookup"><span data-stu-id="56da2-285">You see the saved values in the Index page.</span></span>

## <a name="update-the-delete-page-model"></a><span data-ttu-id="56da2-286">Mettre à jour le modèle de page de suppression</span><span class="sxs-lookup"><span data-stu-id="56da2-286">Update the Delete page model</span></span>

<span data-ttu-id="56da2-287">Mettez à jour *Pages/Departments/Delete.cshtml.cs* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="56da2-287">Update *Pages/Departments/Delete.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Delete.cshtml.cs)]

<span data-ttu-id="56da2-288">La page Delete détecte les conflits d’accès concurrentiel quand l’entité a changé après avoir été récupérée.</span><span class="sxs-lookup"><span data-stu-id="56da2-288">The Delete page detects concurrency conflicts when the entity has changed after it was fetched.</span></span> <span data-ttu-id="56da2-289">`Department.RowVersion` est la version de ligne quand l’entité a été récupérée.</span><span class="sxs-lookup"><span data-stu-id="56da2-289">`Department.RowVersion` is the row version when the entity was fetched.</span></span> <span data-ttu-id="56da2-290">Quand EF Core crée la commande SQL DELETE, il inclut une clause WHERE avec `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="56da2-290">When EF Core creates the SQL DELETE command, it includes a WHERE clause with `RowVersion`.</span></span> <span data-ttu-id="56da2-291">Si après l’exécution de la commande SQL DELETE aucune ligne n’est affectée :</span><span class="sxs-lookup"><span data-stu-id="56da2-291">If the SQL DELETE command results in zero rows affected:</span></span>

* <span data-ttu-id="56da2-292">La valeur de `RowVersion` dans la commande SQL DELETE ne correspond pas à `RowVersion` dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="56da2-292">The `RowVersion` in the SQL DELETE command doesn't match `RowVersion` in the database.</span></span>
* <span data-ttu-id="56da2-293">Une exception DbUpdateConcurrencyException est levée.</span><span class="sxs-lookup"><span data-stu-id="56da2-293">A DbUpdateConcurrencyException exception is thrown.</span></span>
* <span data-ttu-id="56da2-294">`OnGetAsync` est appelée avec `concurrencyError`.</span><span class="sxs-lookup"><span data-stu-id="56da2-294">`OnGetAsync` is called with the `concurrencyError`.</span></span>

### <a name="update-the-delete-page"></a><span data-ttu-id="56da2-295">Mettre à jour la page Delete</span><span class="sxs-lookup"><span data-stu-id="56da2-295">Update the Delete page</span></span>

<span data-ttu-id="56da2-296">Mettez à jour *Pages/Departments/Delete.cshtml* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="56da2-296">Update *Pages/Departments/Delete.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Delete.cshtml?highlight=1,10,39,42,51)]

<span data-ttu-id="56da2-297">Le code précédent apporte les modifications suivantes :</span><span class="sxs-lookup"><span data-stu-id="56da2-297">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="56da2-298">Il met à jour la directive `page` en remplaçant `@page` par `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="56da2-298">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="56da2-299">Il ajoute un message d’erreur.</span><span class="sxs-lookup"><span data-stu-id="56da2-299">Adds an error message.</span></span>
* <span data-ttu-id="56da2-300">Il remplace FirstMidName par FullName dans le champ **Administrator** .</span><span class="sxs-lookup"><span data-stu-id="56da2-300">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>
* <span data-ttu-id="56da2-301">Il change `RowVersion` pour afficher le dernier octet.</span><span class="sxs-lookup"><span data-stu-id="56da2-301">Changes `RowVersion` to display the last byte.</span></span>
* <span data-ttu-id="56da2-302">Ajoute une version de ligne masquée.</span><span class="sxs-lookup"><span data-stu-id="56da2-302">Adds a hidden row version.</span></span> <span data-ttu-id="56da2-303">`RowVersion` doit être ajouté de sorte que la publication (postback) lie la valeur.</span><span class="sxs-lookup"><span data-stu-id="56da2-303">`RowVersion` must be added so postback binds the value.</span></span>

### <a name="test-concurrency-conflicts"></a><span data-ttu-id="56da2-304">Tester les conflits d'accès concurrentiel</span><span class="sxs-lookup"><span data-stu-id="56da2-304">Test concurrency conflicts</span></span>

<span data-ttu-id="56da2-305">Créez un département test.</span><span class="sxs-lookup"><span data-stu-id="56da2-305">Create a test department.</span></span>

<span data-ttu-id="56da2-306">Ouvrez deux instances de navigateur de la page Delete sur le département test :</span><span class="sxs-lookup"><span data-stu-id="56da2-306">Open two browsers instances of Delete on the test department:</span></span>

* <span data-ttu-id="56da2-307">Exécutez l’application et sélectionnez Departments.</span><span class="sxs-lookup"><span data-stu-id="56da2-307">Run the app and select Departments.</span></span>
* <span data-ttu-id="56da2-308">Cliquez avec le bouton droit sur le lien hypertexte **Delete** correspondant au département test, puis sélectionnez **Open in new tab** .</span><span class="sxs-lookup"><span data-stu-id="56da2-308">Right-click the **Delete** hyperlink for the test department and select **Open in new tab** .</span></span>
* <span data-ttu-id="56da2-309">Cliquez sur le lien hypertexte **Edit** correspondant au département test.</span><span class="sxs-lookup"><span data-stu-id="56da2-309">Click the **Edit** hyperlink for the test department.</span></span>

<span data-ttu-id="56da2-310">Les deux onglets de navigateur affichent les mêmes informations.</span><span class="sxs-lookup"><span data-stu-id="56da2-310">The two browser tabs display the same information.</span></span>

<span data-ttu-id="56da2-311">Changez le budget sous le premier onglet de navigateur, puis cliquez sur **Save** .</span><span class="sxs-lookup"><span data-stu-id="56da2-311">Change the budget in the first browser tab and click **Save** .</span></span>

<span data-ttu-id="56da2-312">Le navigateur affiche la page Index avec la valeur modifiée et un indicateur rowVersion mis à jour.</span><span class="sxs-lookup"><span data-stu-id="56da2-312">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="56da2-313">Notez l’indicateur rowVersion mis à jour ; il est affiché sur la deuxième publication (postback) sous l’autre onglet.</span><span class="sxs-lookup"><span data-stu-id="56da2-313">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="56da2-314">Supprimez le service test du deuxième onglet. Une erreur d’accès concurrentiel s’affiche avec les valeurs actuelles de la base de données.</span><span class="sxs-lookup"><span data-stu-id="56da2-314">Delete the test department from the second tab. A concurrency error is display with the current values from the database.</span></span> <span data-ttu-id="56da2-315">Le fait de cliquer sur **supprimer** supprime l’entité, sauf si `RowVersion` a été mis à jour.</span><span class="sxs-lookup"><span data-stu-id="56da2-315">Clicking **Delete** deletes the entity, unless `RowVersion` has been updated.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="56da2-316">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="56da2-316">Additional resources</span></span>

* [<span data-ttu-id="56da2-317">Concurrency Tokens in EF Core (Jetons d’accès concurrentiel dans EF Core)</span><span class="sxs-lookup"><span data-stu-id="56da2-317">Concurrency Tokens in EF Core</span></span>](/ef/core/modeling/concurrency)
* [<span data-ttu-id="56da2-318">Gestion de l’accès concurrentiel dans EF Core</span><span class="sxs-lookup"><span data-stu-id="56da2-318">Handle concurrency in EF Core</span></span>](/ef/core/saving/concurrency)
* [<span data-ttu-id="56da2-319">Débogage d’une source ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="56da2-319">Debugging ASP.NET Core 2.x source</span></span>](https://github.com/dotnet/AspNetCore.Docs/issues/4155)

## <a name="next-steps"></a><span data-ttu-id="56da2-320">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="56da2-320">Next steps</span></span>

<span data-ttu-id="56da2-321">Ce tutoriel est le dernier de la série.</span><span class="sxs-lookup"><span data-stu-id="56da2-321">This is the last tutorial in the series.</span></span> <span data-ttu-id="56da2-322">Des rubriques supplémentaires sont abordées dans la [version MVC de cette série de tutoriels](xref:data/ef-mvc/index).</span><span class="sxs-lookup"><span data-stu-id="56da2-322">Additional topics are covered in the [MVC version of this tutorial series](xref:data/ef-mvc/index).</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="56da2-323">Tutoriel précédent</span><span class="sxs-lookup"><span data-stu-id="56da2-323">Previous tutorial</span></span>](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="56da2-324">Ce didacticiel montre comment gérer les conflits quand plusieurs utilisateurs mettent à jour une entité en même temps.</span><span class="sxs-lookup"><span data-stu-id="56da2-324">This tutorial shows how to handle conflicts when multiple users update an entity concurrently (at the same time).</span></span> <span data-ttu-id="56da2-325">Si vous rencontrez des problèmes que vous ne pouvez pas résoudre, [téléchargez ou affichez l’application terminée](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="56da2-325">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="56da2-326">[Instructions de téléchargement](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="56da2-326">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="concurrency-conflicts"></a><span data-ttu-id="56da2-327">Conflits d’accès concurrentiel</span><span class="sxs-lookup"><span data-stu-id="56da2-327">Concurrency conflicts</span></span>

<span data-ttu-id="56da2-328">Un conflit d’accès concurrentiel se produit quand :</span><span class="sxs-lookup"><span data-stu-id="56da2-328">A concurrency conflict occurs when:</span></span>

* <span data-ttu-id="56da2-329">Un utilisateur accède à la page de modification d’une entité.</span><span class="sxs-lookup"><span data-stu-id="56da2-329">A user navigates to the edit page for an entity.</span></span>
* <span data-ttu-id="56da2-330">Un autre utilisateur met à jour la même entité avant que la modification du premier utilisateur soit écrite dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="56da2-330">Another user updates the same entity before the first user's change is written to the DB.</span></span>

<span data-ttu-id="56da2-331">Si la détection d’accès concurrentiel n’est pas activée, quand des mises à jour simultanées se produisent :</span><span class="sxs-lookup"><span data-stu-id="56da2-331">If concurrency detection isn't enabled, when concurrent updates occur:</span></span>

* <span data-ttu-id="56da2-332">La dernière mise à jour est prioritaire.</span><span class="sxs-lookup"><span data-stu-id="56da2-332">The last update wins.</span></span> <span data-ttu-id="56da2-333">Autrement dit, les dernières valeurs mises à jour sont enregistrées dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="56da2-333">That is, the last update values are saved to the DB.</span></span>
* <span data-ttu-id="56da2-334">La première des mises à jour en cours est perdue.</span><span class="sxs-lookup"><span data-stu-id="56da2-334">The first of the current updates are lost.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="56da2-335">Accès concurrentiel optimiste</span><span class="sxs-lookup"><span data-stu-id="56da2-335">Optimistic concurrency</span></span>

<span data-ttu-id="56da2-336">L’accès concurrentiel optimiste autorise la survenance des conflits d’accès concurrentiel, et réagit correctement quand ils surviennent.</span><span class="sxs-lookup"><span data-stu-id="56da2-336">Optimistic concurrency allows concurrency conflicts to happen, and then reacts appropriately when they do.</span></span> <span data-ttu-id="56da2-337">Par exemple, Jane consulte la page de modification de département et change le montant de « Budget » pour le département « English » en le faisant passer de 350 000,00 $ à 0,00 $.</span><span class="sxs-lookup"><span data-stu-id="56da2-337">For example, Jane visits the Department edit page and changes the budget for the English department from $350,000.00 to $0.00.</span></span>

![Modification de la valeur de budget sur 0](concurrency/_static/change-budget.png)

<span data-ttu-id="56da2-339">Avant que Jane clique sur **Save** , John consulte la même page et change le champ Start Date de 01/09/2007 en 01/09/2013.</span><span class="sxs-lookup"><span data-stu-id="56da2-339">Before Jane clicks **Save** , John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![Modification de la date de début sur 2013](concurrency/_static/change-date.png)

<span data-ttu-id="56da2-341">Jane clique la première sur **Save** et voit sa modification quand le navigateur revient à la page Index.</span><span class="sxs-lookup"><span data-stu-id="56da2-341">Jane clicks **Save** first and sees her change when the browser displays the Index page.</span></span>

![Le budget est passé à zéro](concurrency/_static/budget-zero.png)

<span data-ttu-id="56da2-343">John clique sur **Save** dans une page Edit qui affiche toujours un budget de 350 000,00 $.</span><span class="sxs-lookup"><span data-stu-id="56da2-343">John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="56da2-344">Ce qui se passe ensuite est déterminé par la façon dont vous gérez les conflits d’accès concurrentiel.</span><span class="sxs-lookup"><span data-stu-id="56da2-344">What happens next is determined by how you handle concurrency conflicts.</span></span>

<span data-ttu-id="56da2-345">L’accès concurrentiel optimiste comprend les options suivantes :</span><span class="sxs-lookup"><span data-stu-id="56da2-345">Optimistic concurrency includes the following options:</span></span>

* <span data-ttu-id="56da2-346">Vous pouvez effectuer le suivi des propriétés modifiées par un utilisateur et mettre à jour seulement les colonnes correspondantes dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="56da2-346">You can keep track of which property a user has modified and update only the corresponding columns in the DB.</span></span>

  <span data-ttu-id="56da2-347">Dans le scénario, aucune donnée ne serait perdue.</span><span class="sxs-lookup"><span data-stu-id="56da2-347">In the scenario, no data would be lost.</span></span> <span data-ttu-id="56da2-348">Des propriétés différentes ont été mises à jour par les deux utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="56da2-348">Different properties were updated by the two users.</span></span> <span data-ttu-id="56da2-349">La prochaine fois que quelqu’un consultera le département « English », il verra à la fois les modifications de Jane et de John.</span><span class="sxs-lookup"><span data-stu-id="56da2-349">The next time someone browses the English department, they will see both Jane's and John's changes.</span></span> <span data-ttu-id="56da2-350">Cette méthode de mise à jour peut réduire le nombre de conflits susceptibles d’entraîner une perte de données.</span><span class="sxs-lookup"><span data-stu-id="56da2-350">This method of updating can reduce the number of conflicts that could result in data loss.</span></span> <span data-ttu-id="56da2-351">Cette approche a les caractéristiques suivantes :</span><span class="sxs-lookup"><span data-stu-id="56da2-351">This approach:</span></span>
 
  * <span data-ttu-id="56da2-352">Elle ne peut pas éviter une perte de données si des modifications concurrentes sont apportées à la même propriété.</span><span class="sxs-lookup"><span data-stu-id="56da2-352">Can't avoid data loss if competing changes are made to the same property.</span></span>
  * <span data-ttu-id="56da2-353">Elle n’est généralement pas pratique dans une application web.</span><span class="sxs-lookup"><span data-stu-id="56da2-353">Is generally not practical in a web app.</span></span> <span data-ttu-id="56da2-354">Elle nécessite la tenue à jour d’un état significatif afin d’effectuer le suivi de toutes les valeurs récupérées et des nouvelles valeurs.</span><span class="sxs-lookup"><span data-stu-id="56da2-354">It requires maintaining significant state in order to keep track of all fetched values and new values.</span></span> <span data-ttu-id="56da2-355">La maintenance de grandes quantités d’état peut affecter les performances de l’application.</span><span class="sxs-lookup"><span data-stu-id="56da2-355">Maintaining large amounts of state can affect app performance.</span></span>
  * <span data-ttu-id="56da2-356">Elle peut augmenter la complexité de l’application par rapport à la détection de l’accès concurrentiel sur une entité.</span><span class="sxs-lookup"><span data-stu-id="56da2-356">Can increase app complexity compared to concurrency detection on an entity.</span></span>

* <span data-ttu-id="56da2-357">Vous pouvez laisser les modifications de John remplacer les modifications de Jane.</span><span class="sxs-lookup"><span data-stu-id="56da2-357">You can let John's change overwrite Jane's change.</span></span>

  <span data-ttu-id="56da2-358">La prochaine fois que quelqu’un consultera le département « English », il verra la date 01/09/2013 et la valeur 350 000,00 $ récupérée.</span><span class="sxs-lookup"><span data-stu-id="56da2-358">The next time someone browses the English department, they will see 9/1/2013 and the fetched $350,000.00 value.</span></span> <span data-ttu-id="56da2-359">Cette approche est un scénario *Priorité au client* ou *Priorité au dernier* .</span><span class="sxs-lookup"><span data-stu-id="56da2-359">This approach is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="56da2-360">(Toutes les valeurs du client sont prioritaires par rapport à ce qui se trouve dans le magasin de données.) Si vous n’effectuez aucun codage pour la gestion de l’accès concurrentiel, le client WINS se produit automatiquement.</span><span class="sxs-lookup"><span data-stu-id="56da2-360">(All values from the client take precedence over what's in the data store.) If you don't do any coding for concurrency handling, Client Wins happens automatically.</span></span>

* <span data-ttu-id="56da2-361">Vous pouvez empêcher les modifications de John d’être mises à jour dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="56da2-361">You can prevent John's change from being updated in the DB.</span></span> <span data-ttu-id="56da2-362">En règle générale, l’application :</span><span class="sxs-lookup"><span data-stu-id="56da2-362">Typically, the app would:</span></span>

  * <span data-ttu-id="56da2-363">affiche un message d’erreur ;</span><span class="sxs-lookup"><span data-stu-id="56da2-363">Display an error message.</span></span>
  * <span data-ttu-id="56da2-364">indique l’état actuel des données ;</span><span class="sxs-lookup"><span data-stu-id="56da2-364">Show the current state of the data.</span></span>
  * <span data-ttu-id="56da2-365">autorise l’utilisateur à réappliquer les modifications.</span><span class="sxs-lookup"><span data-stu-id="56da2-365">Allow the user to reapply the changes.</span></span>

  <span data-ttu-id="56da2-366">Il s’agit alors d’un scénario *Priorité au magasin* .</span><span class="sxs-lookup"><span data-stu-id="56da2-366">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="56da2-367">(Les valeurs du magasin de données ont priorité sur les valeurs soumises par le client.) Vous implémentez le scénario de stockage WINS dans ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="56da2-367">(The data-store values take precedence over the values submitted by the client.) You implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="56da2-368">Cette méthode garantit qu’aucune modification n’est remplacée sans qu’un utilisateur soit averti.</span><span class="sxs-lookup"><span data-stu-id="56da2-368">This method ensures that no changes are overwritten without a user being alerted.</span></span>

## <a name="handling-concurrency"></a><span data-ttu-id="56da2-369">Gestion de l’accès concurrentiel</span><span class="sxs-lookup"><span data-stu-id="56da2-369">Handling concurrency</span></span> 

<span data-ttu-id="56da2-370">Quand une propriété est configurée en tant que [jeton d’accès concurrentiel](/ef/core/modeling/concurrency) :</span><span class="sxs-lookup"><span data-stu-id="56da2-370">When a property is configured as a [concurrency token](/ef/core/modeling/concurrency):</span></span>

* <span data-ttu-id="56da2-371">EF Core vérifie que cette propriété n’a pas été modifiée après avoir été récupérée.</span><span class="sxs-lookup"><span data-stu-id="56da2-371">EF Core verifies that property has not been modified after it was fetched.</span></span> <span data-ttu-id="56da2-372">La vérification a lieu quand [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) ou [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) est appelée.</span><span class="sxs-lookup"><span data-stu-id="56da2-372">The check occurs when [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) or [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) is called.</span></span>
* <span data-ttu-id="56da2-373">Si la propriété a été modifiée après avoir été récupérée, une [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception) est levée.</span><span class="sxs-lookup"><span data-stu-id="56da2-373">If the property has been changed after it was fetched, a [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception) is thrown.</span></span> 

<span data-ttu-id="56da2-374">Le modèle de données et de la base de données doivent être configurés pour prendre en charge la levée de `DbUpdateConcurrencyException`.</span><span class="sxs-lookup"><span data-stu-id="56da2-374">The DB and data model must be configured to support throwing `DbUpdateConcurrencyException`.</span></span>

### <a name="detecting-concurrency-conflicts-on-a-property"></a><span data-ttu-id="56da2-375">Détection des conflits d’accès concurrentiel sur une propriété</span><span class="sxs-lookup"><span data-stu-id="56da2-375">Detecting concurrency conflicts on a property</span></span>

<span data-ttu-id="56da2-376">Les conflits d’accès concurrentiel peuvent être détectés au niveau de la propriété avec l’attribut [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute).</span><span class="sxs-lookup"><span data-stu-id="56da2-376">Concurrency conflicts can be detected at the property level with the [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute) attribute.</span></span> <span data-ttu-id="56da2-377">L’attribut peut être appliqué à plusieurs propriétés sur le modèle.</span><span class="sxs-lookup"><span data-stu-id="56da2-377">The attribute can be applied to multiple properties on the model.</span></span> <span data-ttu-id="56da2-378">Pour plus d’informations, consultez [Data Annotations-ConcurrencyCheck (Annotations de données-ConcurrencyCheck)](/ef/core/modeling/concurrency#data-annotations).</span><span class="sxs-lookup"><span data-stu-id="56da2-378">For more information, see [Data Annotations-ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).</span></span>

<span data-ttu-id="56da2-379">Nous n’utilisons pas l’attribut `[ConcurrencyCheck]` dans ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="56da2-379">The `[ConcurrencyCheck]` attribute isn't used in this tutorial.</span></span>

### <a name="detecting-concurrency-conflicts-on-a-row"></a><span data-ttu-id="56da2-380">Détection des conflits d’accès concurrentiel sur une ligne</span><span class="sxs-lookup"><span data-stu-id="56da2-380">Detecting concurrency conflicts on a row</span></span>

<span data-ttu-id="56da2-381">Pour détecter les conflits d’accès concurrentiel, une colonne de suivi [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) est ajoutée au modèle.</span><span class="sxs-lookup"><span data-stu-id="56da2-381">To detect concurrency conflicts, a [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) tracking column is added to the model.</span></span>  <span data-ttu-id="56da2-382">`rowversion` :</span><span class="sxs-lookup"><span data-stu-id="56da2-382">`rowversion` :</span></span>

* <span data-ttu-id="56da2-383">est propre à SQL Server.</span><span class="sxs-lookup"><span data-stu-id="56da2-383">Is SQL Server specific.</span></span> <span data-ttu-id="56da2-384">D’autres bases de données peuvent ne pas fournir une fonctionnalité similaire.</span><span class="sxs-lookup"><span data-stu-id="56da2-384">Other databases may not provide a similar feature.</span></span>
* <span data-ttu-id="56da2-385">Sert à déterminer qu’une entité n’a pas été modifiée depuis qu’elle a été récupérée à partir de la base de données.</span><span class="sxs-lookup"><span data-stu-id="56da2-385">Is used to determine that an entity has not been changed since it was fetched from the DB.</span></span> 

<span data-ttu-id="56da2-386">La base de données génère un numéro `rowversion` séquentiel qui est incrémenté chaque fois que la ligne est mise à jour.</span><span class="sxs-lookup"><span data-stu-id="56da2-386">The DB generates a sequential `rowversion` number that's incremented each time the row is updated.</span></span> <span data-ttu-id="56da2-387">Dans une commande `Update` ou `Delete`, la clause `Where` comprend la valeur récupérée de `rowversion`.</span><span class="sxs-lookup"><span data-stu-id="56da2-387">In an `Update` or `Delete` command, the `Where` clause includes the fetched value of `rowversion`.</span></span> <span data-ttu-id="56da2-388">Si la ligne mise à jour a changé :</span><span class="sxs-lookup"><span data-stu-id="56da2-388">If the row being updated has changed:</span></span>

* <span data-ttu-id="56da2-389">`rowversion` ne correspond pas à la valeur récupérée.</span><span class="sxs-lookup"><span data-stu-id="56da2-389">`rowversion` doesn't match the fetched value.</span></span>
* <span data-ttu-id="56da2-390">Les commandes `Update` ou `Delete` ne trouvent pas de ligne, car la clause `Where` comprend la valeur `rowversion` récupérée.</span><span class="sxs-lookup"><span data-stu-id="56da2-390">The `Update` or `Delete` commands don't find a row because the `Where` clause includes the fetched `rowversion`.</span></span>
* <span data-ttu-id="56da2-391">Une `DbUpdateConcurrencyException` est levée.</span><span class="sxs-lookup"><span data-stu-id="56da2-391">A `DbUpdateConcurrencyException` is thrown.</span></span>

<span data-ttu-id="56da2-392">Dans EF Core, quand aucune ligne n’a été mise à jour par une commande `Update` ou `Delete`, une exception d’accès concurrentiel est levée.</span><span class="sxs-lookup"><span data-stu-id="56da2-392">In EF Core, when no rows have been updated by an `Update` or `Delete` command, a concurrency exception is thrown.</span></span>

### <a name="add-a-tracking-property-to-the-department-entity"></a><span data-ttu-id="56da2-393">Ajouter une propriété de suivi à l’entité Department</span><span class="sxs-lookup"><span data-stu-id="56da2-393">Add a tracking property to the Department entity</span></span>

<span data-ttu-id="56da2-394">Dans *Models/Department.cs* , ajoutez une propriété de suivi nommée RowVersion :</span><span class="sxs-lookup"><span data-stu-id="56da2-394">In *Models/Department.cs* , add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

<span data-ttu-id="56da2-395">L’attribut [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) spécifie que cette colonne est incluse dans la clause `Where` des commandes `Update` et `Delete`.</span><span class="sxs-lookup"><span data-stu-id="56da2-395">The [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) attribute specifies that this column is included in the `Where` clause of `Update` and `Delete` commands.</span></span> <span data-ttu-id="56da2-396">L’attribut se nomme `Timestamp`, car les versions précédentes de SQL Server utilisaient un type de données SQL `timestamp` avant son remplacement par le type SQL `rowversion`.</span><span class="sxs-lookup"><span data-stu-id="56da2-396">The attribute is called `Timestamp` because previous versions of SQL Server used a SQL `timestamp` data type before the SQL `rowversion` type replaced it.</span></span>

<span data-ttu-id="56da2-397">L’API Fluent peut également spécifier la propriété de suivi :</span><span class="sxs-lookup"><span data-stu-id="56da2-397">The fluent API can also specify the tracking property:</span></span>

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

<span data-ttu-id="56da2-398">Le code suivant montre une partie du T-SQL généré par EF Core quand le nom du département est mis à jour :</span><span class="sxs-lookup"><span data-stu-id="56da2-398">The following code shows a portion of the T-SQL generated by EF Core when the Department name is updated:</span></span>

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=2-3)]

<span data-ttu-id="56da2-399">Le code en surbrillance ci-dessus montre la clause `WHERE` contenant `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="56da2-399">The preceding highlighted code shows the `WHERE` clause containing `RowVersion`.</span></span> <span data-ttu-id="56da2-400">Si la `RowVersion` de la base de données n’est pas égale au paramètre `RowVersion` (`@p2`), aucune ligne n’est mise à jour.</span><span class="sxs-lookup"><span data-stu-id="56da2-400">If the DB `RowVersion` doesn't equal the `RowVersion` parameter (`@p2`), no rows are updated.</span></span>

<span data-ttu-id="56da2-401">Le code en surbrillance suivant montre le T-SQL qui vérifie qu’une seule ligne a été mise à jour :</span><span class="sxs-lookup"><span data-stu-id="56da2-401">The following highlighted code shows the T-SQL that verifies exactly one row was updated:</span></span>

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=4-6)]

<span data-ttu-id="56da2-402">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) retourne le nombre de lignes affectées par la dernière instruction.</span><span class="sxs-lookup"><span data-stu-id="56da2-402">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) returns the number of rows affected by the last statement.</span></span> <span data-ttu-id="56da2-403">Si aucune ligne n’est mise à jour, EF Core lève une `DbUpdateConcurrencyException`.</span><span class="sxs-lookup"><span data-stu-id="56da2-403">In no rows are updated, EF Core throws a `DbUpdateConcurrencyException`.</span></span>

<span data-ttu-id="56da2-404">Vous pouvez voir le T-SQL généré par EF Core dans la fenêtre Sortie de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="56da2-404">You can see the T-SQL EF Core generates in the output window of Visual Studio.</span></span>

### <a name="update-the-db"></a><span data-ttu-id="56da2-405">Mettre à jour la base de données</span><span class="sxs-lookup"><span data-stu-id="56da2-405">Update the DB</span></span>

<span data-ttu-id="56da2-406">L’ajout de la propriété `RowVersion` change le modèle de base de données, ce qui nécessite une migration.</span><span class="sxs-lookup"><span data-stu-id="56da2-406">Adding the `RowVersion` property changes the DB model, which requires a migration.</span></span>

<span data-ttu-id="56da2-407">Créez le projet.</span><span class="sxs-lookup"><span data-stu-id="56da2-407">Build the project.</span></span> <span data-ttu-id="56da2-408">Entrez ce qui suit dans une fenêtre de commande :</span><span class="sxs-lookup"><span data-stu-id="56da2-408">Enter the following in a command window:</span></span>

```dotnetcli
dotnet ef migrations add RowVersion
dotnet ef database update
```

<span data-ttu-id="56da2-409">Les commandes précédentes :</span><span class="sxs-lookup"><span data-stu-id="56da2-409">The preceding commands:</span></span>

* <span data-ttu-id="56da2-410">Ajoutent le fichier de migration *Migrations/{horodatage}_RowVersion.cs* .</span><span class="sxs-lookup"><span data-stu-id="56da2-410">Adds the *Migrations/{time stamp}_RowVersion.cs* migration file.</span></span>
* <span data-ttu-id="56da2-411">Mettent à jour le fichier *Migrations/SchoolContextModelSnapshot.cs* .</span><span class="sxs-lookup"><span data-stu-id="56da2-411">Updates the *Migrations/SchoolContextModelSnapshot.cs* file.</span></span> <span data-ttu-id="56da2-412">La mise à jour ajoute le code en surbrillance suivant à la méthode `BuildModel` :</span><span class="sxs-lookup"><span data-stu-id="56da2-412">The update adds the following highlighted code to the `BuildModel` method:</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=14-16)]

* <span data-ttu-id="56da2-413">Exécutent des migrations pour mettre à jour la base de données.</span><span class="sxs-lookup"><span data-stu-id="56da2-413">Runs migrations to update the DB.</span></span>

<a name="scaffold"></a>

## <a name="scaffold-the-departments-model"></a><span data-ttu-id="56da2-414">Générer automatiquement le modèle Departments</span><span class="sxs-lookup"><span data-stu-id="56da2-414">Scaffold the Departments model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="56da2-415">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56da2-415">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="56da2-416">Suivez les instructions fournies dans [Générer automatiquement le modèle d’étudiant](xref:data/ef-rp/intro#scaffold-student-pages) et utilisez `Department` pour la classe de modèle.</span><span class="sxs-lookup"><span data-stu-id="56da2-416">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-student-pages) and use `Department` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="56da2-417">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="56da2-417">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="56da2-418">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="56da2-418">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

---

<span data-ttu-id="56da2-419">La commande précédente génère automatiquement le modèle `Department`.</span><span class="sxs-lookup"><span data-stu-id="56da2-419">The preceding command scaffolds the `Department` model.</span></span> <span data-ttu-id="56da2-420">Ouvrez le projet dans Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="56da2-420">Open the project in Visual Studio.</span></span>

<span data-ttu-id="56da2-421">Créez le projet.</span><span class="sxs-lookup"><span data-stu-id="56da2-421">Build the project.</span></span>

### <a name="update-the-departments-index-page"></a><span data-ttu-id="56da2-422">Mettre à jour la page d’index des départements</span><span class="sxs-lookup"><span data-stu-id="56da2-422">Update the Departments Index page</span></span>

<span data-ttu-id="56da2-423">Le moteur de génération de modèles automatique créé une colonne `RowVersion` pour la page Index, mais ce champ ne doit pas être affiché.</span><span class="sxs-lookup"><span data-stu-id="56da2-423">The scaffolding engine created a `RowVersion` column for the Index page, but that field shouldn't be displayed.</span></span> <span data-ttu-id="56da2-424">Dans ce didacticiel, le dernier octet de `RowVersion` est affiché afin d’aider à mieux comprendre l’accès concurrentiel.</span><span class="sxs-lookup"><span data-stu-id="56da2-424">In this tutorial, the last byte of the `RowVersion` is displayed to help understand concurrency.</span></span> <span data-ttu-id="56da2-425">Il n’est pas garanti que le dernier octet soit unique.</span><span class="sxs-lookup"><span data-stu-id="56da2-425">The last byte isn't guaranteed to be unique.</span></span> <span data-ttu-id="56da2-426">Une application réelle n’afficherait pas `RowVersion` ou le dernier octet de `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="56da2-426">A real app wouldn't display `RowVersion` or the last byte of `RowVersion`.</span></span>

<span data-ttu-id="56da2-427">Mettez à jour la page Index :</span><span class="sxs-lookup"><span data-stu-id="56da2-427">Update the Index page:</span></span>

* <span data-ttu-id="56da2-428">Remplacez Index par Departments.</span><span class="sxs-lookup"><span data-stu-id="56da2-428">Replace Index with Departments.</span></span>
* <span data-ttu-id="56da2-429">Remplacez le balisage contenant `RowVersion` par le dernier octet de `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="56da2-429">Replace the markup containing `RowVersion` with the last byte of `RowVersion`.</span></span>
* <span data-ttu-id="56da2-430">Remplacez FirstMidName par FullName.</span><span class="sxs-lookup"><span data-stu-id="56da2-430">Replace FirstMidName with FullName.</span></span>

<span data-ttu-id="56da2-431">Le balisage suivant montre la page mise à jour :</span><span class="sxs-lookup"><span data-stu-id="56da2-431">The following markup shows the updated page:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Departments/Index.cshtml?highlight=5,8,29,47,50)]

### <a name="update-the-edit-page-model"></a><span data-ttu-id="56da2-432">Mettre à jour le modèle de page de modification</span><span class="sxs-lookup"><span data-stu-id="56da2-432">Update the Edit page model</span></span>

<span data-ttu-id="56da2-433">Mettez à jour *Pages\Departments\Edit.cshtml.cs* à l’aide du code suivant :</span><span class="sxs-lookup"><span data-stu-id="56da2-433">Update *Pages\Departments\Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="56da2-434">Pour détecter un problème d’accès concurrentiel, [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) est mise à jour avec la valeur `rowVersion` de l’entité récupérée.</span><span class="sxs-lookup"><span data-stu-id="56da2-434">To detect a concurrency issue, the [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) is updated with the `rowVersion` value from the entity it was fetched.</span></span> <span data-ttu-id="56da2-435">EF Core génère une commande SQL UPDATE avec une clause WHERE contenant la valeur `RowVersion` d’origine.</span><span class="sxs-lookup"><span data-stu-id="56da2-435">EF Core generates a SQL UPDATE command with a WHERE clause containing the original `RowVersion` value.</span></span> <span data-ttu-id="56da2-436">Si aucune ligne n’est affectée par la commande UPDATE (aucune ligne ne contient la valeur `RowVersion` d’origine), une exception `DbUpdateConcurrencyException` est levée.</span><span class="sxs-lookup"><span data-stu-id="56da2-436">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value), a `DbUpdateConcurrencyException` exception is thrown.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_rv&highlight=24-999)]

<span data-ttu-id="56da2-437">Dans le code précédent, `Department.RowVersion` est la valeur quand l’entité a été récupérée.</span><span class="sxs-lookup"><span data-stu-id="56da2-437">In the preceding code, `Department.RowVersion` is the value when the entity was fetched.</span></span> <span data-ttu-id="56da2-438">`OriginalValue` est la valeur présente dans la base de données quand `FirstOrDefaultAsync` a été appelée dans cette méthode.</span><span class="sxs-lookup"><span data-stu-id="56da2-438">`OriginalValue` is the value in the DB when `FirstOrDefaultAsync` was called in this method.</span></span>

<span data-ttu-id="56da2-439">Le code suivant obtient les valeurs du client (celles envoyées à cette méthode) et les valeurs de la base de données :</span><span class="sxs-lookup"><span data-stu-id="56da2-439">The following code gets the client values (the values posted to this method) and the DB values:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=9,18)]

<span data-ttu-id="56da2-440">Le code suivant ajoute un message d’erreur personnalisé pour chaque colonne dont les valeurs dans la base de données sont différentes de celles envoyées à `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="56da2-440">The following code adds a custom error message for each column that has DB values different from what was posted to `OnPostAsync`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_err)]

<span data-ttu-id="56da2-441">Le code en surbrillance suivant affecte à `RowVersion` la nouvelle valeur récupérée à partir de la base de données.</span><span class="sxs-lookup"><span data-stu-id="56da2-441">The following highlighted code sets the `RowVersion` value to the new value retrieved from the DB.</span></span> <span data-ttu-id="56da2-442">La prochaine fois que l’utilisateur cliquera sur **Save** , seules les erreurs d’accès concurrentiel qui se sont produites depuis le dernier affichage de la page Edit seront interceptées.</span><span class="sxs-lookup"><span data-stu-id="56da2-442">The next time the user clicks **Save** , only concurrency errors that happen since the last display of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=23)]

<span data-ttu-id="56da2-443">L’instruction `ModelState.Remove` est nécessaire car `ModelState` contient l’ancienne valeur `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="56da2-443">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="56da2-444">Dans la :::no-loc(Razor)::: page, la `ModelState` valeur d’un champ est prioritaire sur les valeurs de propriété du modèle lorsque les deux sont présentes.</span><span class="sxs-lookup"><span data-stu-id="56da2-444">In the :::no-loc(Razor)::: Page, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

## <a name="update-the-edit-page"></a><span data-ttu-id="56da2-445">Mettre à jour la page Edit</span><span class="sxs-lookup"><span data-stu-id="56da2-445">Update the Edit page</span></span>

<span data-ttu-id="56da2-446">Mettez à jour *Pages/Departments/Edit.cshtml* avec le balisage suivant :</span><span class="sxs-lookup"><span data-stu-id="56da2-446">Update *Pages/Departments/Edit.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

<span data-ttu-id="56da2-447">Le balisage précédent :</span><span class="sxs-lookup"><span data-stu-id="56da2-447">The preceding markup:</span></span>

* <span data-ttu-id="56da2-448">Il met à jour la directive `page` en remplaçant `@page` par `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="56da2-448">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="56da2-449">Ajoute une version de ligne masquée.</span><span class="sxs-lookup"><span data-stu-id="56da2-449">Adds a hidden row version.</span></span> <span data-ttu-id="56da2-450">`RowVersion` doit être ajouté afin que la publication lie la valeur.</span><span class="sxs-lookup"><span data-stu-id="56da2-450">`RowVersion` must be added so post back binds the value.</span></span>
* <span data-ttu-id="56da2-451">Affiche le dernier octet de `RowVersion` à des fins de débogage.</span><span class="sxs-lookup"><span data-stu-id="56da2-451">Displays the last byte of `RowVersion` for debugging purposes.</span></span>
* <span data-ttu-id="56da2-452">Remplace `ViewData` par le `InstructorNameSL` fortement typé.</span><span class="sxs-lookup"><span data-stu-id="56da2-452">Replaces `ViewData` with the strongly-typed `InstructorNameSL`.</span></span>

## <a name="test-concurrency-conflicts-with-the-edit-page"></a><span data-ttu-id="56da2-453">Tester les conflits d’accès concurrentiel avec la page Edit</span><span class="sxs-lookup"><span data-stu-id="56da2-453">Test concurrency conflicts with the Edit page</span></span>

<span data-ttu-id="56da2-454">Ouvrez deux instances de navigateur de la page Edit sur le département English :</span><span class="sxs-lookup"><span data-stu-id="56da2-454">Open two browsers instances of Edit on the English department:</span></span>

* <span data-ttu-id="56da2-455">Exécutez l’application et sélectionnez Departments.</span><span class="sxs-lookup"><span data-stu-id="56da2-455">Run the app and select Departments.</span></span>
* <span data-ttu-id="56da2-456">Cliquez avec le bouton droit sur le lien hypertexte **Edit** correspondant au département English, puis sélectionnez **Open in new tab** .</span><span class="sxs-lookup"><span data-stu-id="56da2-456">Right-click the **Edit** hyperlink for the English department and select **Open in new tab** .</span></span>
* <span data-ttu-id="56da2-457">Sous le premier onglet, cliquez sur le lien hypertexte **Edit** correspondant au département English.</span><span class="sxs-lookup"><span data-stu-id="56da2-457">In the first tab, click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="56da2-458">Les deux onglets de navigateur affichent les mêmes informations.</span><span class="sxs-lookup"><span data-stu-id="56da2-458">The two browser tabs display the same information.</span></span>

<span data-ttu-id="56da2-459">Changez le nom sous le premier onglet de navigateur, puis cliquez sur **Save** .</span><span class="sxs-lookup"><span data-stu-id="56da2-459">Change the name in the first browser tab and click **Save** .</span></span>

![Page 1 de modification de département après changement](concurrency/_static/edit-after-change-1.png)

<span data-ttu-id="56da2-461">Le navigateur affiche la page Index avec la valeur modifiée et un indicateur rowVersion mis à jour.</span><span class="sxs-lookup"><span data-stu-id="56da2-461">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="56da2-462">Notez l’indicateur rowVersion mis à jour ; il est affiché sur la deuxième publication (postback) sous l’autre onglet.</span><span class="sxs-lookup"><span data-stu-id="56da2-462">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="56da2-463">Changez un champ différent sous le deuxième onglet du navigateur.</span><span class="sxs-lookup"><span data-stu-id="56da2-463">Change a different field in the second browser tab.</span></span>

![Page Edit 2 du département après changement](concurrency/_static/edit-after-change-2.png)

<span data-ttu-id="56da2-465">Cliquez sur **Enregistrer** .</span><span class="sxs-lookup"><span data-stu-id="56da2-465">Click **Save** .</span></span> <span data-ttu-id="56da2-466">Des messages d’erreur s’affichent pour tous les champs qui ne correspondent pas aux valeurs de la base de données :</span><span class="sxs-lookup"><span data-stu-id="56da2-466">You see error messages for all fields that don't match the DB values:</span></span>

![Message d’erreur de page de modification de département](concurrency/_static/edit-error.png)

<span data-ttu-id="56da2-468">Cette fenêtre de navigateur n’avait pas l’intention de changer le champ Name.</span><span class="sxs-lookup"><span data-stu-id="56da2-468">This browser window didn't intend to change the Name field.</span></span> <span data-ttu-id="56da2-469">Copiez et collez la valeur actuelle (Languages) dans le champ Name.</span><span class="sxs-lookup"><span data-stu-id="56da2-469">Copy and paste the current value (Languages) into the Name field.</span></span> <span data-ttu-id="56da2-470">Tabulation. La validation côté client supprime le message d’erreur.</span><span class="sxs-lookup"><span data-stu-id="56da2-470">Tab out. Client-side validation removes the error message.</span></span>

![Message d’erreur de page de modification de département](concurrency/_static/cv.png)

<span data-ttu-id="56da2-472">Cliquez à nouveau sur **Enregistrer** .</span><span class="sxs-lookup"><span data-stu-id="56da2-472">Click **Save** again.</span></span> <span data-ttu-id="56da2-473">La valeur que vous avez entrée sous le deuxième onglet du navigateur est enregistrée.</span><span class="sxs-lookup"><span data-stu-id="56da2-473">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="56da2-474">Les valeurs enregistrées sont visibles dans la page Index.</span><span class="sxs-lookup"><span data-stu-id="56da2-474">You see the saved values in the Index page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="56da2-475">Mettre à jour la page Delete</span><span class="sxs-lookup"><span data-stu-id="56da2-475">Update the Delete page</span></span>

<span data-ttu-id="56da2-476">Mettez à jour le modèle de page de suppression avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="56da2-476">Update the Delete page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Delete.cshtml.cs)]

<span data-ttu-id="56da2-477">La page Delete détecte les conflits d’accès concurrentiel quand l’entité a changé après avoir été récupérée.</span><span class="sxs-lookup"><span data-stu-id="56da2-477">The Delete page detects concurrency conflicts when the entity has changed after it was fetched.</span></span> <span data-ttu-id="56da2-478">`Department.RowVersion` est la version de ligne quand l’entité a été récupérée.</span><span class="sxs-lookup"><span data-stu-id="56da2-478">`Department.RowVersion` is the row version when the entity was fetched.</span></span> <span data-ttu-id="56da2-479">Quand EF Core crée la commande SQL DELETE, il inclut une clause WHERE avec `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="56da2-479">When EF Core creates the SQL DELETE command, it includes a WHERE clause with `RowVersion`.</span></span> <span data-ttu-id="56da2-480">Si après l’exécution de la commande SQL DELETE aucune ligne n’est affectée :</span><span class="sxs-lookup"><span data-stu-id="56da2-480">If the SQL DELETE command results in zero rows affected:</span></span>

* <span data-ttu-id="56da2-481">La valeur de `RowVersion` dans la commande SQL DELETE ne correspond pas à `RowVersion` dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="56da2-481">The `RowVersion` in the SQL DELETE command doesn't match `RowVersion` in the DB.</span></span>
* <span data-ttu-id="56da2-482">Une exception DbUpdateConcurrencyException est levée.</span><span class="sxs-lookup"><span data-stu-id="56da2-482">A DbUpdateConcurrencyException exception is thrown.</span></span>
* <span data-ttu-id="56da2-483">`OnGetAsync` est appelée avec `concurrencyError`.</span><span class="sxs-lookup"><span data-stu-id="56da2-483">`OnGetAsync` is called with the `concurrencyError`.</span></span>

### <a name="update-the-delete-page"></a><span data-ttu-id="56da2-484">Mettre à jour la page Delete</span><span class="sxs-lookup"><span data-stu-id="56da2-484">Update the Delete page</span></span>

<span data-ttu-id="56da2-485">Mettez à jour *Pages/Departments/Delete.cshtml* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="56da2-485">Update *Pages/Departments/Delete.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

<span data-ttu-id="56da2-486">Le code précédent apporte les modifications suivantes :</span><span class="sxs-lookup"><span data-stu-id="56da2-486">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="56da2-487">Il met à jour la directive `page` en remplaçant `@page` par `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="56da2-487">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="56da2-488">Il ajoute un message d’erreur.</span><span class="sxs-lookup"><span data-stu-id="56da2-488">Adds an error message.</span></span>
* <span data-ttu-id="56da2-489">Il remplace FirstMidName par FullName dans le champ **Administrator** .</span><span class="sxs-lookup"><span data-stu-id="56da2-489">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>
* <span data-ttu-id="56da2-490">Il change `RowVersion` pour afficher le dernier octet.</span><span class="sxs-lookup"><span data-stu-id="56da2-490">Changes `RowVersion` to display the last byte.</span></span>
* <span data-ttu-id="56da2-491">Ajoute une version de ligne masquée.</span><span class="sxs-lookup"><span data-stu-id="56da2-491">Adds a hidden row version.</span></span> <span data-ttu-id="56da2-492">`RowVersion` doit être ajouté afin que la publication lie la valeur.</span><span class="sxs-lookup"><span data-stu-id="56da2-492">`RowVersion` must be added so post back binds the value.</span></span>

### <a name="test-concurrency-conflicts-with-the-delete-page"></a><span data-ttu-id="56da2-493">Tester les conflits d’accès concurrentiel avec la page Delete</span><span class="sxs-lookup"><span data-stu-id="56da2-493">Test concurrency conflicts with the Delete page</span></span>

<span data-ttu-id="56da2-494">Créez un département test.</span><span class="sxs-lookup"><span data-stu-id="56da2-494">Create a test department.</span></span>

<span data-ttu-id="56da2-495">Ouvrez deux instances de navigateur de la page Delete sur le département test :</span><span class="sxs-lookup"><span data-stu-id="56da2-495">Open two browsers instances of Delete on the test department:</span></span>

* <span data-ttu-id="56da2-496">Exécutez l’application et sélectionnez Departments.</span><span class="sxs-lookup"><span data-stu-id="56da2-496">Run the app and select Departments.</span></span>
* <span data-ttu-id="56da2-497">Cliquez avec le bouton droit sur le lien hypertexte **Delete** correspondant au département test, puis sélectionnez **Open in new tab** .</span><span class="sxs-lookup"><span data-stu-id="56da2-497">Right-click the **Delete** hyperlink for the test department and select **Open in new tab** .</span></span>
* <span data-ttu-id="56da2-498">Cliquez sur le lien hypertexte **Edit** correspondant au département test.</span><span class="sxs-lookup"><span data-stu-id="56da2-498">Click the **Edit** hyperlink for the test department.</span></span>

<span data-ttu-id="56da2-499">Les deux onglets de navigateur affichent les mêmes informations.</span><span class="sxs-lookup"><span data-stu-id="56da2-499">The two browser tabs display the same information.</span></span>

<span data-ttu-id="56da2-500">Changez le budget sous le premier onglet de navigateur, puis cliquez sur **Save** .</span><span class="sxs-lookup"><span data-stu-id="56da2-500">Change the budget in the first browser tab and click **Save** .</span></span>

<span data-ttu-id="56da2-501">Le navigateur affiche la page Index avec la valeur modifiée et un indicateur rowVersion mis à jour.</span><span class="sxs-lookup"><span data-stu-id="56da2-501">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="56da2-502">Notez l’indicateur rowVersion mis à jour ; il est affiché sur la deuxième publication (postback) sous l’autre onglet.</span><span class="sxs-lookup"><span data-stu-id="56da2-502">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="56da2-503">Supprimez le service test du deuxième onglet. Une erreur d’accès concurrentiel s’affiche avec les valeurs actuelles de la base de données.</span><span class="sxs-lookup"><span data-stu-id="56da2-503">Delete the test department from the second tab. A concurrency error is display with the current values from the DB.</span></span> <span data-ttu-id="56da2-504">Le fait de cliquer sur **supprimer** supprime l’entité, sauf si `RowVersion` a été mis à jour.</span><span class="sxs-lookup"><span data-stu-id="56da2-504">Clicking **Delete** deletes the entity, unless `RowVersion` has been updated.</span></span>

<span data-ttu-id="56da2-505">Pour découvrir comment hériter d’un modèle de données, consultez [Héritage](xref:data/ef-mvc/inheritance).</span><span class="sxs-lookup"><span data-stu-id="56da2-505">See [Inheritance](xref:data/ef-mvc/inheritance) on how to inherit a data model.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="56da2-506">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="56da2-506">Additional resources</span></span>

* [<span data-ttu-id="56da2-507">Concurrency Tokens in EF Core (Jetons d’accès concurrentiel dans EF Core)</span><span class="sxs-lookup"><span data-stu-id="56da2-507">Concurrency Tokens in EF Core</span></span>](/ef/core/modeling/concurrency)
* [<span data-ttu-id="56da2-508">Gestion de l’accès concurrentiel dans EF Core</span><span class="sxs-lookup"><span data-stu-id="56da2-508">Handle concurrency in EF Core</span></span>](/ef/core/saving/concurrency)
* [<span data-ttu-id="56da2-509">Version YouTube de ce tutoriel(gestion des conflits d’accès concurrentiel)</span><span class="sxs-lookup"><span data-stu-id="56da2-509">YouTube version of this tutorial(Handling Concurrency Conflicts)</span></span>](https://youtu.be/EosxHTFgYps)
* [<span data-ttu-id="56da2-510">Version YouTube de ce tutoriel(Partie 2)</span><span class="sxs-lookup"><span data-stu-id="56da2-510">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=kcxERLnaGO0)
* [<span data-ttu-id="56da2-511">Version YouTube de ce tutoriel(Partie 3)</span><span class="sxs-lookup"><span data-stu-id="56da2-511">YouTube version of this tutorial(Part 3)</span></span>](https://www.youtube.com/watch?v=d4RbpfvELRs)

> [!div class="step-by-step"]
> [<span data-ttu-id="56da2-512">Précédent</span><span class="sxs-lookup"><span data-stu-id="56da2-512">Previous</span></span>](xref:data/ef-rp/update-related-data)

::: moniker-end

