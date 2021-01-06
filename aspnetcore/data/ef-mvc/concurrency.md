---
title: 'Didacticiel : gérer la concurrence-ASP.NET MVC avec EF Core'
description: Ce didacticiel montre comment gérer les conflits quand plusieurs utilisateurs mettent à jour la même entité en même temps.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
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
uid: data/ef-mvc/concurrency
ms.openlocfilehash: d476c836e8d497ca1291992dda38da1fc9f59ed2
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054370"
---
# <a name="tutorial-handle-concurrency---aspnet-mvc-with-ef-core"></a><span data-ttu-id="793ff-103">Didacticiel : gérer la concurrence-ASP.NET MVC avec EF Core</span><span class="sxs-lookup"><span data-stu-id="793ff-103">Tutorial: Handle concurrency - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="793ff-104">Dans les didacticiels précédents, vous avez découvert comment mettre à jour des données.</span><span class="sxs-lookup"><span data-stu-id="793ff-104">In earlier tutorials, you learned how to update data.</span></span> <span data-ttu-id="793ff-105">Ce didacticiel montre comment gérer les conflits quand plusieurs utilisateurs mettent à jour la même entité en même temps.</span><span class="sxs-lookup"><span data-stu-id="793ff-105">This tutorial shows how to handle conflicts when multiple users update the same entity at the same time.</span></span>

<span data-ttu-id="793ff-106">Vous allez créer des pages web qui utilisent l’entité Department et gérer les erreurs d’accès concurrentiel.</span><span class="sxs-lookup"><span data-stu-id="793ff-106">You'll create web pages that work with the Department entity and handle concurrency errors.</span></span> <span data-ttu-id="793ff-107">Les illustrations suivantes montrent les pages Edit et Delete, notamment certains messages qui sont affichés si un conflit d’accès concurrentiel se produit.</span><span class="sxs-lookup"><span data-stu-id="793ff-107">The following illustrations show the Edit and Delete pages, including some messages that are displayed if a concurrency conflict occurs.</span></span>

![Page Edit pour les départements](concurrency/_static/edit-error.png)

![Page Delete pour les départements](concurrency/_static/delete-error.png)

<span data-ttu-id="793ff-110">Dans ce tutoriel, vous allez :</span><span class="sxs-lookup"><span data-stu-id="793ff-110">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="793ff-111">En savoir plus sur les conflits d’accès concurrentiel</span><span class="sxs-lookup"><span data-stu-id="793ff-111">Learn about concurrency conflicts</span></span>
> * <span data-ttu-id="793ff-112">Ajouter une propriété de suivi</span><span class="sxs-lookup"><span data-stu-id="793ff-112">Add a tracking property</span></span>
> * <span data-ttu-id="793ff-113">Créer un contrôleur Departments et des vues</span><span class="sxs-lookup"><span data-stu-id="793ff-113">Create Departments controller and views</span></span>
> * <span data-ttu-id="793ff-114">Mettre à jour la vue Index</span><span class="sxs-lookup"><span data-stu-id="793ff-114">Update Index view</span></span>
> * <span data-ttu-id="793ff-115">Mettre à jour les méthodes de modification</span><span class="sxs-lookup"><span data-stu-id="793ff-115">Update Edit methods</span></span>
> * <span data-ttu-id="793ff-116">Mettre à jour la vue Edit</span><span class="sxs-lookup"><span data-stu-id="793ff-116">Update Edit view</span></span>
> * <span data-ttu-id="793ff-117">Tester les conflits d'accès concurrentiel</span><span class="sxs-lookup"><span data-stu-id="793ff-117">Test concurrency conflicts</span></span>
> * <span data-ttu-id="793ff-118">Mettre à jour la page Delete</span><span class="sxs-lookup"><span data-stu-id="793ff-118">Update the Delete page</span></span>
> * <span data-ttu-id="793ff-119">Mettre à jour les vues Details et Create</span><span class="sxs-lookup"><span data-stu-id="793ff-119">Update Details and Create views</span></span>

## <a name="prerequisites"></a><span data-ttu-id="793ff-120">Prérequis</span><span class="sxs-lookup"><span data-stu-id="793ff-120">Prerequisites</span></span>

* [<span data-ttu-id="793ff-121">Mettre à jour les données associées</span><span class="sxs-lookup"><span data-stu-id="793ff-121">Update related data</span></span>](update-related-data.md)

## <a name="concurrency-conflicts"></a><span data-ttu-id="793ff-122">Conflits d’accès concurrentiel</span><span class="sxs-lookup"><span data-stu-id="793ff-122">Concurrency conflicts</span></span>

<span data-ttu-id="793ff-123">Un conflit d’accès concurrentiel se produit quand un utilisateur affiche les données d’une entité pour la modifier, puis qu’un autre utilisateur met à jour les données de la même entité avant que les modifications du premier utilisateur soient écrites dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="793ff-123">A concurrency conflict occurs when one user displays an entity's data in order to edit it, and then another user updates the same entity's data before the first user's change is written to the database.</span></span> <span data-ttu-id="793ff-124">Si vous n’activez pas la détection de ces conflits, la personne qui met à jour la base de données en dernier remplace les modifications de l’autre utilisateur.</span><span class="sxs-lookup"><span data-stu-id="793ff-124">If you don't enable the detection of such conflicts, whoever updates the database last overwrites the other user's changes.</span></span> <span data-ttu-id="793ff-125">Dans de nombreuses applications, ce risque est acceptable : s’il n’y a que quelques utilisateurs ou quelques mises à jour, ou s’il n’est pas réellement critique que des modifications soient remplacées, le coût de la programmation nécessaire à la gestion des accès concurrentiels peut être supérieur au bénéfice qu’elle apporte.</span><span class="sxs-lookup"><span data-stu-id="793ff-125">In many applications, this risk is acceptable: if there are few users, or few updates, or if isn't really critical if some changes are overwritten, the cost of programming for concurrency might outweigh the benefit.</span></span> <span data-ttu-id="793ff-126">Dans ce cas, vous ne devez pas configurer l’application pour gérer les conflits d’accès concurrentiel.</span><span class="sxs-lookup"><span data-stu-id="793ff-126">In that case, you don't have to configure the application to handle concurrency conflicts.</span></span>

### <a name="pessimistic-concurrency-locking"></a><span data-ttu-id="793ff-127">Accès concurrentiel pessimiste (verrouillage)</span><span class="sxs-lookup"><span data-stu-id="793ff-127">Pessimistic concurrency (locking)</span></span>

<span data-ttu-id="793ff-128">Si votre application doit éviter la perte accidentelle de données dans des scénarios d’accès concurrentiel, une manière de le faire consiste à utiliser des verrous de base de données.</span><span class="sxs-lookup"><span data-stu-id="793ff-128">If your application does need to prevent accidental data loss in concurrency scenarios, one way to do that is to use database locks.</span></span> <span data-ttu-id="793ff-129">Ceci est appelé « accès concurrentiel pessimiste ».</span><span class="sxs-lookup"><span data-stu-id="793ff-129">This is called pessimistic concurrency.</span></span> <span data-ttu-id="793ff-130">Par exemple, avant de lire une ligne d’une base de données, vous demandez un verrou pour lecture seule ou pour accès avec mise à jour.</span><span class="sxs-lookup"><span data-stu-id="793ff-130">For example, before you read a row from a database, you request a lock for read-only or for update access.</span></span> <span data-ttu-id="793ff-131">Si vous verrouillez une ligne pour accès avec mise à jour, aucun autre utilisateur n’est autorisé à verrouiller la ligne pour lecture seule ou pour accès avec mise à jour, car ils obtiendraient ainsi une copie de données qui sont en cours de modification.</span><span class="sxs-lookup"><span data-stu-id="793ff-131">If you lock a row for update access, no other users are allowed to lock the row either for read-only or update access, because they would get a copy of data that's in the process of being changed.</span></span> <span data-ttu-id="793ff-132">Si vous verrouillez une ligne pour accès en lecture seule, d’autres utilisateurs peuvent également la verrouiller pour accès en lecture seule, mais pas pour accès avec mise à jour.</span><span class="sxs-lookup"><span data-stu-id="793ff-132">If you lock a row for read-only access, others can also lock it for read-only access but not for update.</span></span>

<span data-ttu-id="793ff-133">La gestion des verrous présente des inconvénients.</span><span class="sxs-lookup"><span data-stu-id="793ff-133">Managing locks has disadvantages.</span></span> <span data-ttu-id="793ff-134">Elle peut être complexe à programmer.</span><span class="sxs-lookup"><span data-stu-id="793ff-134">It can be complex to program.</span></span> <span data-ttu-id="793ff-135">Elle nécessite des ressources de gestion de base de données importantes, et peut provoquer des problèmes de performances au fil de l’augmentation du nombre d’utilisateurs d’une application.</span><span class="sxs-lookup"><span data-stu-id="793ff-135">It requires significant database management resources, and it can cause performance problems as the number of users of an application increases.</span></span> <span data-ttu-id="793ff-136">Pour ces raisons, certains systèmes de gestion de base de données ne prennent pas en charge l’accès concurrentiel pessimiste.</span><span class="sxs-lookup"><span data-stu-id="793ff-136">For these reasons, not all database management systems support pessimistic concurrency.</span></span> <span data-ttu-id="793ff-137">Entity Framework Core n’en fournit pas de prise en charge intégrée et ce didacticiel ne vous montre comment l’implémenter.</span><span class="sxs-lookup"><span data-stu-id="793ff-137">Entity Framework Core provides no built-in support for it, and this tutorial doesn't show you how to implement it.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="793ff-138">Accès concurrentiel optimiste</span><span class="sxs-lookup"><span data-stu-id="793ff-138">Optimistic Concurrency</span></span>

<span data-ttu-id="793ff-139">La solution alternative à l’accès concurrentiel pessimiste est l’accès concurrentiel optimiste.</span><span class="sxs-lookup"><span data-stu-id="793ff-139">The alternative to pessimistic concurrency is optimistic concurrency.</span></span> <span data-ttu-id="793ff-140">L’accès concurrentiel optimiste signifie autoriser la survenance des conflits d’accès concurrentiel, puis de réagir correctement quand ils surviennent.</span><span class="sxs-lookup"><span data-stu-id="793ff-140">Optimistic concurrency means allowing concurrency conflicts to happen, and then reacting appropriately if they do.</span></span> <span data-ttu-id="793ff-141">Par exemple, Jane consulte la page Department Edit et change le montant de « Budget » pour le département « English » en le passant de $350 000,00 à $0,00.</span><span class="sxs-lookup"><span data-stu-id="793ff-141">For example, Jane visits the Department Edit page and changes the Budget amount for the English department from $350,000.00 to $0.00.</span></span>

![Modification de la valeur de budget sur 0](concurrency/_static/change-budget.png)

<span data-ttu-id="793ff-143">Avant que Jane clique sur **Save**, John consulte la même page et change le champ Start Date de 01/09/2007 en 01/09/2013.</span><span class="sxs-lookup"><span data-stu-id="793ff-143">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![Modification de la date de début sur 2013](concurrency/_static/change-date.png)

<span data-ttu-id="793ff-145">Jane clique la première sur **Save** et voit sa modification quand le navigateur revient à la page Index.</span><span class="sxs-lookup"><span data-stu-id="793ff-145">Jane clicks **Save** first and sees her change when the browser returns to the Index page.</span></span>

![Le budget est passé à zéro](concurrency/_static/budget-zero.png)

<span data-ttu-id="793ff-147">John clique à son tour sur **Save** sur une page Edit qui affiche toujours un budget de $350 000,00.</span><span class="sxs-lookup"><span data-stu-id="793ff-147">Then John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="793ff-148">Ce qui se passe ensuite est déterminé par la façon dont vous gérez les conflits d’accès concurrentiel.</span><span class="sxs-lookup"><span data-stu-id="793ff-148">What happens next is determined by how you handle concurrency conflicts.</span></span>

<span data-ttu-id="793ff-149">Voici quelques-unes des options :</span><span class="sxs-lookup"><span data-stu-id="793ff-149">Some of the options include the following:</span></span>

* <span data-ttu-id="793ff-150">Vous pouvez effectuer le suivi des propriétés modifiées par un utilisateur et mettre à jour seulement les colonnes correspondantes dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="793ff-150">You can keep track of which property a user has modified and update only the corresponding columns in the database.</span></span>

     <span data-ttu-id="793ff-151">Dans l’exemple de scénario, aucune donnée ne serait perdue, car des propriétés différentes ont été mises à jour par chacun des deux utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="793ff-151">In the example scenario, no data would be lost, because different properties were updated by the two users.</span></span> <span data-ttu-id="793ff-152">La prochaine fois que quelqu’un examine le département « English », il voit à la fois les modifications de Jane et de John : une date de début au 01/09/2013 et un budget de zéro dollars.</span><span class="sxs-lookup"><span data-stu-id="793ff-152">The next time someone browses the English department, they will see both Jane's and John's changes -- a start date of 9/1/2013 and a budget of zero dollars.</span></span> <span data-ttu-id="793ff-153">Cette méthode de mise à jour peut réduire le nombre de conflits qui peuvent entraîner des pertes de données, mais elle ne peut pas éviter la perte de données si des modifications concurrentes sont apportées à la même propriété d’une entité.</span><span class="sxs-lookup"><span data-stu-id="793ff-153">This method of updating can reduce the number of conflicts that could result in data loss, but it can't avoid data loss if competing changes are made to the same property of an entity.</span></span> <span data-ttu-id="793ff-154">Un tel fonctionnement d’Entity Framework dépend de la façon dont vous implémentez votre code de mise à jour.</span><span class="sxs-lookup"><span data-stu-id="793ff-154">Whether the Entity Framework works this way depends on how you implement your update code.</span></span> <span data-ttu-id="793ff-155">Il n’est pas souvent pratique dans une application web, car il peut nécessiter la gestion de grandes quantités d’états pour effectuer le suivi de toutes les valeurs de propriété d’origine d’une entité, ainsi que des nouvelles valeurs.</span><span class="sxs-lookup"><span data-stu-id="793ff-155">It's often not practical in a web application, because it can require that you maintain large amounts of state in order to keep track of all original property values for an entity as well as new values.</span></span> <span data-ttu-id="793ff-156">La conservation d’un grand nombre d’États peut affecter les performances de l’application, car elle nécessite des ressources serveur ou elle doit être incluse dans la page Web elle-même (par exemple, dans les champs masqués) ou dans un cookie .</span><span class="sxs-lookup"><span data-stu-id="793ff-156">Maintaining large amounts of state can affect application performance because it either requires server resources or must be included in the web page itself (for example, in hidden fields) or in a cookie.</span></span>

* <span data-ttu-id="793ff-157">Vous pouvez laisser les modifications de John remplacer les modifications de Jane.</span><span class="sxs-lookup"><span data-stu-id="793ff-157">You can let John's change overwrite Jane's change.</span></span>

     <span data-ttu-id="793ff-158">La prochaine fois que quelqu’un consultera le département « English », il verra la date du 01/09/2013 et la valeur $350 000,00 restaurée.</span><span class="sxs-lookup"><span data-stu-id="793ff-158">The next time someone browses the English department, they will see 9/1/2013 and the restored $350,000.00 value.</span></span> <span data-ttu-id="793ff-159">Ceci s’appelle un scénario *Priorité au client* ou *Priorité au dernier entré* (Last in Wins).</span><span class="sxs-lookup"><span data-stu-id="793ff-159">This is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="793ff-160">(Toutes les valeurs du client sont prioritaires par rapport à ce qui se trouve dans le magasin de données.) Comme indiqué dans la présentation de cette section, si vous n’effectuez aucun codage pour la gestion de l’accès concurrentiel, cela se produit automatiquement.</span><span class="sxs-lookup"><span data-stu-id="793ff-160">(All values from the client take precedence over what's in the data store.) As noted in the introduction to this section, if you don't do any coding for concurrency handling, this will happen automatically.</span></span>

* <span data-ttu-id="793ff-161">Vous pouvez empêcher les modifications de John de faire l’objet d’une mise à jour dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="793ff-161">You can prevent John's change from being updated in the database.</span></span>

     <span data-ttu-id="793ff-162">En règle générale, vous affichez un message d’erreur, vous lui montrez l’état actuel des données et vous lui permettez de réappliquer ses modifications s’il veut toujours les faire.</span><span class="sxs-lookup"><span data-stu-id="793ff-162">Typically, you would display an error message, show him the current state of the data, and allow him to reapply his changes if he still wants to make them.</span></span> <span data-ttu-id="793ff-163">Il s’agit alors d’un scénario *Priorité au magasin*.</span><span class="sxs-lookup"><span data-stu-id="793ff-163">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="793ff-164">(Les valeurs du magasin de données ont priorité sur les valeurs soumises par le client.) Vous allez implémenter le scénario de stockage WINS dans ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="793ff-164">(The data-store values take precedence over the values submitted by the client.) You'll implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="793ff-165">Cette méthode garantit qu’aucune modification n’est remplacée sans qu’un utilisateur soit averti de ce qui se passe.</span><span class="sxs-lookup"><span data-stu-id="793ff-165">This method ensures that no changes are overwritten without a user being alerted to what's happening.</span></span>

### <a name="detecting-concurrency-conflicts"></a><span data-ttu-id="793ff-166">Détection des conflits d’accès concurrentiel</span><span class="sxs-lookup"><span data-stu-id="793ff-166">Detecting concurrency conflicts</span></span>

<span data-ttu-id="793ff-167">Vous pouvez résoudre les conflits en gérant les exceptions `DbConcurrencyException` levées par Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="793ff-167">You can resolve conflicts by handling `DbConcurrencyException` exceptions that the Entity Framework throws.</span></span> <span data-ttu-id="793ff-168">Pour savoir quand lever ces exceptions, Entity Framework doit être en mesure de détecter les conflits.</span><span class="sxs-lookup"><span data-stu-id="793ff-168">In order to know when to throw these exceptions, the Entity Framework must be able to detect conflicts.</span></span> <span data-ttu-id="793ff-169">Par conséquent, vous devez configurer de façon appropriée la base de données et le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="793ff-169">Therefore, you must configure the database and the data model appropriately.</span></span> <span data-ttu-id="793ff-170">Voici quelques options pour l’activation de la détection des conflits :</span><span class="sxs-lookup"><span data-stu-id="793ff-170">Some options for enabling conflict detection include the following:</span></span>

* <span data-ttu-id="793ff-171">Dans la table de base de données, incluez une colonne de suivi qui peut être utilisée pour déterminer quand une ligne a été modifiée.</span><span class="sxs-lookup"><span data-stu-id="793ff-171">In the database table, include a tracking column that can be used to determine when a row has been changed.</span></span> <span data-ttu-id="793ff-172">Vous pouvez ensuite configurer Entity Framework pour inclure cette colonne dans la clause WHERE des commandes SQL UPDATE et DELETE.</span><span class="sxs-lookup"><span data-stu-id="793ff-172">You can then configure the Entity Framework to include that column in the Where clause of SQL Update or Delete commands.</span></span>

     <span data-ttu-id="793ff-173">Le type de données de la colonne de suivi est généralement `rowversion`.</span><span class="sxs-lookup"><span data-stu-id="793ff-173">The data type of the tracking column is typically `rowversion`.</span></span> <span data-ttu-id="793ff-174">La valeur de `rowversion` est un nombre séquentiel qui est incrémenté chaque fois que la ligne est mise à jour.</span><span class="sxs-lookup"><span data-stu-id="793ff-174">The `rowversion` value is a sequential number that's incremented each time the row is updated.</span></span> <span data-ttu-id="793ff-175">Dans une commande UPDATE ou DELETE, la clause WHERE inclut la valeur d’origine de la colonne de suivi (la version d’origine de la ligne).</span><span class="sxs-lookup"><span data-stu-id="793ff-175">In an Update or Delete command, the Where clause includes the original value of the tracking column (the original row version) .</span></span> <span data-ttu-id="793ff-176">Si la ligne à mettre à jour a été changée par un autre utilisateur, la valeur de la colonne `rowversion` est différente de la valeur d’origine : l’instruction UPDATE ou DELETE ne peut donc pas trouver la ligne à mettre à jour en raison de la clause WHERE.</span><span class="sxs-lookup"><span data-stu-id="793ff-176">If the row being updated has been changed by another user, the value in the `rowversion` column is different than the original value, so the Update or Delete statement can't find the row to update because of the Where clause.</span></span> <span data-ttu-id="793ff-177">Quand Entity Framework trouve qu’aucune ligne n’a été mise à jour par la commande UPDATE ou DELETE (c’est-à-dire quand le nombre de lignes affectées est égal à zéro), il interprète ceci comme étant un conflit d’accès concurrentiel.</span><span class="sxs-lookup"><span data-stu-id="793ff-177">When the Entity Framework finds that no rows have been updated by the Update or Delete command (that is, when the number of affected rows is zero), it interprets that as a concurrency conflict.</span></span>

* <span data-ttu-id="793ff-178">Configurez Entity Framework de façon à inclure les valeurs d’origine de chaque colonne dans la table de la clause WHERE des commandes UPDATE et DELETE.</span><span class="sxs-lookup"><span data-stu-id="793ff-178">Configure the Entity Framework to include the original values of every column in the table in the Where clause of Update and Delete commands.</span></span>

     <span data-ttu-id="793ff-179">Comme dans la première option, si quelque chose dans la ligne a changé depuis la première lecture de la ligne, la clause WHERE ne retourne pas de ligne à mettre à jour, ce qui est interprété par Entity Framework comme un conflit d’accès concurrentiel.</span><span class="sxs-lookup"><span data-stu-id="793ff-179">As in the first option, if anything in the row has changed since the row was first read, the Where clause won't return a row to update, which the Entity Framework interprets as a concurrency conflict.</span></span> <span data-ttu-id="793ff-180">Pour les tables de base de données qui ont beaucoup de colonnes, cette approche peut aboutir à des clauses WHERE de très grande taille et nécessiter la gestion de grandes quantités d’états.</span><span class="sxs-lookup"><span data-stu-id="793ff-180">For database tables that have many columns, this approach can result in very large Where clauses, and can require that you maintain large amounts of state.</span></span> <span data-ttu-id="793ff-181">Comme indiqué précédemment, la gestion de grandes quantités d’états peut affecter les performances de l’application.</span><span class="sxs-lookup"><span data-stu-id="793ff-181">As noted earlier, maintaining large amounts of state can affect application performance.</span></span> <span data-ttu-id="793ff-182">Par conséquent, cette approche n’est généralement pas recommandée et n’est pas la méthode utilisée dans ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="793ff-182">Therefore this approach is generally not recommended, and it isn't the method used in this tutorial.</span></span>

     <span data-ttu-id="793ff-183">Si vous ne voulez pas implémenter cette approche de l’accès concurrentiel, vous devez marquer toutes les propriétés qui ne sont pas des clés primaires de l’entité dont vous voulez suivre les accès concurrentiels en leur ajoutant l’attribut `ConcurrencyCheck`.</span><span class="sxs-lookup"><span data-stu-id="793ff-183">If you do want to implement this approach to concurrency, you have to mark all non-primary-key properties in the entity you want to track concurrency for by adding the `ConcurrencyCheck` attribute to them.</span></span> <span data-ttu-id="793ff-184">Cette modification permet à Entity Framework d’inclure toutes les colonnes dans la clause SQL WHERE des instructions UPDATE et DELETE.</span><span class="sxs-lookup"><span data-stu-id="793ff-184">That change enables the Entity Framework to include all columns in the SQL Where clause of Update and Delete statements.</span></span>

<span data-ttu-id="793ff-185">Dans le reste de ce didacticiel, vous ajoutez une propriété de suivi `rowversion` à l’entité Department, vous créez un contrôleur et des vues, et vous testez pour vérifier que tout fonctionne correctement.</span><span class="sxs-lookup"><span data-stu-id="793ff-185">In the remainder of this tutorial you'll add a `rowversion` tracking property to the Department entity, create a controller and views, and test to verify that everything works correctly.</span></span>

## <a name="add-a-tracking-property"></a><span data-ttu-id="793ff-186">Ajouter une propriété de suivi</span><span class="sxs-lookup"><span data-stu-id="793ff-186">Add a tracking property</span></span>

<span data-ttu-id="793ff-187">Dans *Models/Department.cs*, ajoutez une propriété de suivi nommée RowVersion :</span><span class="sxs-lookup"><span data-stu-id="793ff-187">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

<span data-ttu-id="793ff-188">L’attribut `Timestamp` spécifie que cette colonne sera incluse dans la clause WHERE des commandes UPDATE et DELETE envoyées à la base de données.</span><span class="sxs-lookup"><span data-stu-id="793ff-188">The `Timestamp` attribute specifies that this column will be included in the Where clause of Update and Delete commands sent to the database.</span></span> <span data-ttu-id="793ff-189">L’attribut est nommé `Timestamp`, car les versions précédentes de SQL Server utilisaient un type de données SQL `timestamp` avant son remplacement par le type SQL `rowversion`.</span><span class="sxs-lookup"><span data-stu-id="793ff-189">The attribute is called `Timestamp` because previous versions of SQL Server used a SQL `timestamp` data type before the SQL `rowversion` replaced it.</span></span> <span data-ttu-id="793ff-190">Le type .NET pour `rowversion` est un tableau d’octets.</span><span class="sxs-lookup"><span data-stu-id="793ff-190">The .NET type for `rowversion` is a byte array.</span></span>

<span data-ttu-id="793ff-191">Si vous préférez utiliser l’API actuelle, vous pouvez utiliser la méthode `IsConcurrencyToken` (dans *Data/SchoolContext.cs*) pour spécifier la propriété de suivi, comme indiqué dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="793ff-191">If you prefer to use the fluent API, you can use the `IsConcurrencyToken` method (in *Data/SchoolContext.cs*) to specify the tracking property, as shown in the following example:</span></span>

```csharp
modelBuilder.Entity<Department>()
    .Property(p => p.RowVersion).IsConcurrencyToken();
```

<span data-ttu-id="793ff-192">En ajoutant une propriété, vous avez changé le modèle de base de données et vous devez donc effectuer une autre migration.</span><span class="sxs-lookup"><span data-stu-id="793ff-192">By adding a property you changed the database model, so you need to do another migration.</span></span>

<span data-ttu-id="793ff-193">Enregistrez vos modifications et générez le projet, puis entrez les commandes suivantes dans la fenêtre Commande :</span><span class="sxs-lookup"><span data-stu-id="793ff-193">Save your changes and build the project, and then enter the following commands in the command window:</span></span>

```dotnetcli
dotnet ef migrations add RowVersion
```

```dotnetcli
dotnet ef database update
```

## <a name="create-departments-controller-and-views"></a><span data-ttu-id="793ff-194">Créer un contrôleur Departments et des vues</span><span class="sxs-lookup"><span data-stu-id="793ff-194">Create Departments controller and views</span></span>

<span data-ttu-id="793ff-195">Générez automatiquement un modèle de contrôleur Departments et des vues, comme vous l’avez fait précédemment pour les étudiants, les cours et les enseignants.</span><span class="sxs-lookup"><span data-stu-id="793ff-195">Scaffold a Departments controller and views as you did earlier for Students, Courses, and Instructors.</span></span>

![Générer automatiquement un modèle Department](concurrency/_static/add-departments-controller.png)

<span data-ttu-id="793ff-197">Dans le fichier *DepartmentsController.cs*, changez les quatre occurrences de « FirstMidName » en « FullName », de façon que les listes déroulantes de l’administrateur du département contiennent le nom complet de l’enseignant et non pas simplement son nom de famille.</span><span class="sxs-lookup"><span data-stu-id="793ff-197">In the *DepartmentsController.cs* file, change all four occurrences of "FirstMidName" to "FullName" so that the department administrator drop-down lists will contain the full name of the instructor rather than just the last name.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_Dropdown)]

## <a name="update-index-view"></a><span data-ttu-id="793ff-198">Mettre à jour la vue Index</span><span class="sxs-lookup"><span data-stu-id="793ff-198">Update Index view</span></span>

<span data-ttu-id="793ff-199">Le moteur de génération de modèles automatique a créé une colonne RowVersion pour la vue Index, mais ce champ ne doit pas être affiché.</span><span class="sxs-lookup"><span data-stu-id="793ff-199">The scaffolding engine created a RowVersion column in the Index view, but that field shouldn't be displayed.</span></span>

<span data-ttu-id="793ff-200">Remplacez le code dans *Students/Index.cshtml* par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="793ff-200">Replace the code in *Views/Departments/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Departments/Index.cshtml?highlight=4,7,44)]

<span data-ttu-id="793ff-201">Ceci change l’en-tête en « Departments », supprime la colonne RowVersion et montre à l’administrateur le nom complet au lieu du prénom.</span><span class="sxs-lookup"><span data-stu-id="793ff-201">This changes the heading to "Departments", deletes the RowVersion column, and shows full name instead of first name for the administrator.</span></span>

## <a name="update-edit-methods"></a><span data-ttu-id="793ff-202">Mettre à jour les méthodes de modification</span><span class="sxs-lookup"><span data-stu-id="793ff-202">Update Edit methods</span></span>

<span data-ttu-id="793ff-203">Dans la méthode HttpGet `Edit` et la méthode `Details`, ajoutez `AsNoTracking`.</span><span class="sxs-lookup"><span data-stu-id="793ff-203">In both the HttpGet `Edit` method and the `Details` method, add `AsNoTracking`.</span></span> <span data-ttu-id="793ff-204">Dans la méthode HttpGet `Edit`, ajoutez un chargement hâtif pour l’administrateur.</span><span class="sxs-lookup"><span data-stu-id="793ff-204">In the HttpGet `Edit` method, add eager loading for the Administrator.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EagerLoading)]

<span data-ttu-id="793ff-205">Remplacez le code existant pour la méthode HttpPost `Edit` méthode par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="793ff-205">Replace the existing code for the HttpPost `Edit` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EditPost)]

<span data-ttu-id="793ff-206">Le code commence par essayer de lire le département à mettre à jour.</span><span class="sxs-lookup"><span data-stu-id="793ff-206">The code begins by trying to read the department to be updated.</span></span> <span data-ttu-id="793ff-207">Si la méthode `FirstOrDefaultAsync` retourne null, c’est que le département a été supprimé par un autre utilisateur.</span><span class="sxs-lookup"><span data-stu-id="793ff-207">If the `FirstOrDefaultAsync` method returns null, the department was deleted by another user.</span></span> <span data-ttu-id="793ff-208">Dans ce cas, le code utilise les valeurs du formulaire envoyé pour créer une entité Department de façon que la page Edit puisse être réaffichée avec un message d’erreur.</span><span class="sxs-lookup"><span data-stu-id="793ff-208">In that case the code uses the posted form values to create a department entity so that the Edit page can be redisplayed with an error message.</span></span> <span data-ttu-id="793ff-209">Vous pouvez aussi ne pas recréer l’entité Department si vous affichez seulement un message d’erreur sans réafficher les champs du département.</span><span class="sxs-lookup"><span data-stu-id="793ff-209">As an alternative, you wouldn't have to re-create the department entity if you display only an error message without redisplaying the department fields.</span></span>

<span data-ttu-id="793ff-210">La vue stocke la valeur d’origine de `RowVersion` dans un champ masqué, et cette méthode reçoit cette valeur dans le paramètre `rowVersion`.</span><span class="sxs-lookup"><span data-stu-id="793ff-210">The view stores the original `RowVersion` value in a hidden field, and this method receives that value in the `rowVersion` parameter.</span></span> <span data-ttu-id="793ff-211">Avant d’appeler `SaveChanges`, vous devez placer la valeur d’origine de la propriété `RowVersion` dans la collection `OriginalValues` pour l’entité.</span><span class="sxs-lookup"><span data-stu-id="793ff-211">Before you call `SaveChanges`, you have to put that original `RowVersion` property value in the `OriginalValues` collection for the entity.</span></span>

```csharp
_context.Entry(departmentToUpdate).Property("RowVersion").OriginalValue = rowVersion;
```

<span data-ttu-id="793ff-212">Ensuite, quand Entity Framework crée une commande SQL UPDATE, cette commande inclut une clause WHERE qui recherche une ligne contenant la valeur d’origine de `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="793ff-212">Then when the Entity Framework creates a SQL UPDATE command, that command will include a WHERE clause that looks for a row that has the original `RowVersion` value.</span></span> <span data-ttu-id="793ff-213">Si aucune ligne n’est affectée par la commande UPDATE (aucune ligne ne contient la valeur `RowVersion` d’origine), Entity Framework lève une exception `DbUpdateConcurrencyException`.</span><span class="sxs-lookup"><span data-stu-id="793ff-213">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value),  the Entity Framework throws a `DbUpdateConcurrencyException` exception.</span></span>

<span data-ttu-id="793ff-214">Le code du bloc catch pour cette exception obtient l’entité Department affectée qui a les valeurs mises à jour de la propriété `Entries` sur l’objet d’exception.</span><span class="sxs-lookup"><span data-stu-id="793ff-214">The code in the catch block for that exception gets the affected Department entity that has the updated values from the `Entries` property on the exception object.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=164)]

<span data-ttu-id="793ff-215">La collection `Entries` n’a qu’un objet `EntityEntry`.</span><span class="sxs-lookup"><span data-stu-id="793ff-215">The `Entries` collection will have just one `EntityEntry` object.</span></span>  <span data-ttu-id="793ff-216">Vous pouvez utiliser cet objet pour obtenir les nouvelles valeurs entrées par l’utilisateur et les valeurs actuelles de la base de données.</span><span class="sxs-lookup"><span data-stu-id="793ff-216">You can use that object to get the new values entered by the user and the current database values.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=165-166)]

<span data-ttu-id="793ff-217">Le code ajoute un message d’erreur personnalisé pour chaque colonne dont les valeurs dans la base de données diffèrent de ce que l’utilisateur a entré dans la page Edit (un seul champ est montré ici par souci de concision).</span><span class="sxs-lookup"><span data-stu-id="793ff-217">The code adds a custom error message for each column that has database values different from what the user entered on the Edit page (only one field is shown here for brevity).</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=174-178)]

<span data-ttu-id="793ff-218">Enfin, le code affecte la nouvelle valeur récupérée auprès de la base de données à `RowVersion` pour `departmentToUpdate`.</span><span class="sxs-lookup"><span data-stu-id="793ff-218">Finally, the code sets the `RowVersion` value of the `departmentToUpdate` to the new value retrieved from the database.</span></span> <span data-ttu-id="793ff-219">Cette nouvelle valeur de `RowVersion` est stockée dans le champ masqué quand la page Edit est réaffichée et, la prochaine fois que l’utilisateur clique sur **Save**, seules les erreurs d’accès concurrentiel qui se produisent depuis le réaffichage de la page Edit sont interceptées.</span><span class="sxs-lookup"><span data-stu-id="793ff-219">This new `RowVersion` value will be stored in the hidden field when the Edit page is redisplayed, and the next time the user clicks **Save**, only concurrency errors that happen since the redisplay of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=199-200)]

<span data-ttu-id="793ff-220">L’instruction `ModelState.Remove` est nécessaire car `ModelState` contient l’ancienne valeur `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="793ff-220">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="793ff-221">Dans la vue, la valeur `ModelState` d’un champ est prioritaire par rapport aux valeurs de propriétés du modèle quand les deux sont présentes.</span><span class="sxs-lookup"><span data-stu-id="793ff-221">In the view, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

## <a name="update-edit-view"></a><span data-ttu-id="793ff-222">Mettre à jour la vue Edit</span><span class="sxs-lookup"><span data-stu-id="793ff-222">Update Edit view</span></span>

<span data-ttu-id="793ff-223">Dans *Views/Departments/Edit.cshtml*, faites les modifications suivantes :</span><span class="sxs-lookup"><span data-stu-id="793ff-223">In *Views/Departments/Edit.cshtml*, make the following changes:</span></span>

* <span data-ttu-id="793ff-224">Ajoutez un champ masqué pour enregistrer la valeur de la propriété `RowVersion`, immédiatement après le champ masqué pour la propriété `DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="793ff-224">Add a hidden field to save the `RowVersion` property value, immediately following the hidden field for the `DepartmentID` property.</span></span>

* <span data-ttu-id="793ff-225">Ajoutez une option « Select Administrator » à la liste déroulante.</span><span class="sxs-lookup"><span data-stu-id="793ff-225">Add a "Select Administrator" option to the drop-down list.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Departments/Edit.cshtml?highlight=16,34-36)]

## <a name="test-concurrency-conflicts"></a><span data-ttu-id="793ff-226">Tester les conflits d'accès concurrentiel</span><span class="sxs-lookup"><span data-stu-id="793ff-226">Test concurrency conflicts</span></span>

<span data-ttu-id="793ff-227">Exécutez l’application et accédez à la page Index des départements.</span><span class="sxs-lookup"><span data-stu-id="793ff-227">Run the app and go to the Departments Index page.</span></span> <span data-ttu-id="793ff-228">Cliquez avec le bouton droit sur le lien hypertexte **Edit** pour le département « English », sélectionnez **Ouvrir dans un nouvel onglet**, puis cliquez sur le lien hypertexte **Edit** pour le département « English ».</span><span class="sxs-lookup"><span data-stu-id="793ff-228">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**, then click the **Edit** hyperlink for the English department.</span></span> <span data-ttu-id="793ff-229">Les deux onglets du navigateur affichent maintenant les mêmes informations.</span><span class="sxs-lookup"><span data-stu-id="793ff-229">The two browser tabs now display the same information.</span></span>

<span data-ttu-id="793ff-230">Changez un champ sous le premier onglet du navigateur, puis cliquez sur **Save**.</span><span class="sxs-lookup"><span data-stu-id="793ff-230">Change a field in the first browser tab and click **Save**.</span></span>

![Page 1 de modification de département après changement](concurrency/_static/edit-after-change-1.png)

<span data-ttu-id="793ff-232">Le navigateur affiche la page Index avec la valeur modifiée.</span><span class="sxs-lookup"><span data-stu-id="793ff-232">The browser shows the Index page with the changed value.</span></span>

<span data-ttu-id="793ff-233">Changez un champ sous le deuxième onglet du navigateur.</span><span class="sxs-lookup"><span data-stu-id="793ff-233">Change a field in the second browser tab.</span></span>

![Page Edit 2 du département après changement](concurrency/_static/edit-after-change-2.png)

<span data-ttu-id="793ff-235">Cliquez sur **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="793ff-235">Click **Save**.</span></span> <span data-ttu-id="793ff-236">Vous voyez un message d’erreur :</span><span class="sxs-lookup"><span data-stu-id="793ff-236">You see an error message:</span></span>

![Message d’erreur de page de modification de département](concurrency/_static/edit-error.png)

<span data-ttu-id="793ff-238">Cliquez à nouveau sur **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="793ff-238">Click **Save** again.</span></span> <span data-ttu-id="793ff-239">La valeur que vous avez entrée sous le deuxième onglet du navigateur est enregistrée.</span><span class="sxs-lookup"><span data-stu-id="793ff-239">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="793ff-240">Vous voyez les valeurs enregistrées quand la page Index apparaît.</span><span class="sxs-lookup"><span data-stu-id="793ff-240">You see the saved values when the Index page appears.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="793ff-241">Mettre à jour la page Delete</span><span class="sxs-lookup"><span data-stu-id="793ff-241">Update the Delete page</span></span>

<span data-ttu-id="793ff-242">Pour la page Delete, Entity Framework détecte les conflits d’accès concurrentiel provoqués par un autre utilisateur qui modifie le service de façon similaire.</span><span class="sxs-lookup"><span data-stu-id="793ff-242">For the Delete page, the Entity Framework detects concurrency conflicts caused by someone else editing the department in a similar manner.</span></span> <span data-ttu-id="793ff-243">Quand la méthode HttpGet `Delete` affiche la vue de confirmation, la vue inclut la version d’origine de `RowVersion` dans un champ masqué.</span><span class="sxs-lookup"><span data-stu-id="793ff-243">When the HttpGet `Delete` method displays the confirmation view, the view includes the original `RowVersion` value in a hidden field.</span></span> <span data-ttu-id="793ff-244">Cette valeur est ensuite disponible pour la méthode HttpPost `Delete` qui est appelée quand l’utilisateur confirme la suppression.</span><span class="sxs-lookup"><span data-stu-id="793ff-244">That value is then available to the HttpPost `Delete` method that's called when the user confirms the deletion.</span></span> <span data-ttu-id="793ff-245">Quand Entity Framework crée la commande SQL DELETE, il inclut une clause WHERE avec la valeur d’origine de `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="793ff-245">When the Entity Framework creates the SQL DELETE command, it includes a WHERE clause with the original `RowVersion` value.</span></span> <span data-ttu-id="793ff-246">Si la commande a pour résultat qu’aucune ligne n’est affectée (ce qui signifie que la ligne a été modifiée après l’affichage de la page de confirmation de la suppression), une exception d’accès concurrentiel est levée et la méthode HttpGet `Delete` est appelée avec un indicateur d’erreur défini sur true pour réafficher la page de confirmation avec un message d’erreur.</span><span class="sxs-lookup"><span data-stu-id="793ff-246">If the command results in zero rows affected (meaning the row was changed after the Delete confirmation page was displayed), a concurrency exception is thrown, and the HttpGet `Delete` method is called with an error flag set to true in order to redisplay the confirmation page with an error message.</span></span> <span data-ttu-id="793ff-247">Il est également possible qu’aucune ligne ne soit affectée en raison du fait que la ligne a été supprimée par un autre utilisateur : dans ce cas, aucun message d’erreur n’est donc affiché.</span><span class="sxs-lookup"><span data-stu-id="793ff-247">It's also possible that zero rows were affected because the row was deleted by another user, so in that case no error message is displayed.</span></span>

### <a name="update-the-delete-methods-in-the-departments-controller"></a><span data-ttu-id="793ff-248">Mettre à jour les méthodes Delete dans le contrôleur Departments</span><span class="sxs-lookup"><span data-stu-id="793ff-248">Update the Delete methods in the Departments controller</span></span>

<span data-ttu-id="793ff-249">Dans *DepartmentsController.cs*, remplacez la méthode HttpGet `Delete` par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="793ff-249">In *DepartmentsController.cs*, replace the HttpGet `Delete` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeleteGet&highlight=1,10,14-17,21-29)]

<span data-ttu-id="793ff-250">La méthode accepte un paramètre facultatif qui indique si la page est réaffichée après une erreur d’accès concurrentiel.</span><span class="sxs-lookup"><span data-stu-id="793ff-250">The method accepts an optional parameter that indicates whether the page is being redisplayed after a concurrency error.</span></span> <span data-ttu-id="793ff-251">Si cet indicateur a la valeur true et que le département spécifié n’existe plus, c’est qu’il a été supprimé par un autre utilisateur.</span><span class="sxs-lookup"><span data-stu-id="793ff-251">If this flag is true and the department specified no longer exists, it was deleted by another user.</span></span> <span data-ttu-id="793ff-252">Dans ce cas, le code redirige vers la page Index.</span><span class="sxs-lookup"><span data-stu-id="793ff-252">In that case, the code redirects to the Index page.</span></span>  <span data-ttu-id="793ff-253">Si cet indicateur a la valeur true et que le département existe, c’est qu’il a été modifié par un autre utilisateur.</span><span class="sxs-lookup"><span data-stu-id="793ff-253">If this flag is true and the Department does exist, it was changed by another user.</span></span> <span data-ttu-id="793ff-254">Dans ce cas, le code envoie un message d’erreur à la vue en utilisant `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="793ff-254">In that case, the code sends an error message to the view using `ViewData`.</span></span>

<span data-ttu-id="793ff-255">Remplacez le code de la méthode HttpPost `Delete` (nommée `DeleteConfirmed`) par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="793ff-255">Replace the code in the HttpPost `Delete` method (named `DeleteConfirmed`) with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeletePost&highlight=1,3,5-8,11-18)]

<span data-ttu-id="793ff-256">Dans le code du modèle généré automatiquement que vous venez de remplacer, cette méthode n’acceptait qu’un seul ID d’enregistrement :</span><span class="sxs-lookup"><span data-stu-id="793ff-256">In the scaffolded code that you just replaced, this method accepted only a record ID:</span></span>

```csharp
public async Task<IActionResult> DeleteConfirmed(int id)
```

<span data-ttu-id="793ff-257">Vous avez changé ce paramètre en une instance d’entité Department créée par le classeur de modèles.</span><span class="sxs-lookup"><span data-stu-id="793ff-257">You've changed this parameter to a Department entity instance created by the model binder.</span></span> <span data-ttu-id="793ff-258">Ceci permet à Entity Framework d’accéder à la valeur de la propriété RowVersion en plus de la clé d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="793ff-258">This gives EF access to the RowVersion property value in addition to the record key.</span></span>

```csharp
public async Task<IActionResult> Delete(Department department)
```

<span data-ttu-id="793ff-259">Vous avez également changé le nom de la méthode d’action de `DeleteConfirmed` en `Delete`.</span><span class="sxs-lookup"><span data-stu-id="793ff-259">You have also changed the action method name from `DeleteConfirmed` to `Delete`.</span></span> <span data-ttu-id="793ff-260">Le code du modèle généré automatiquement utilisait le nom `DeleteConfirmed` pour donner à la méthode HttpPost une signature unique.</span><span class="sxs-lookup"><span data-stu-id="793ff-260">The scaffolded code used the name `DeleteConfirmed` to give the HttpPost method a unique signature.</span></span> <span data-ttu-id="793ff-261">(Le CLR exige que les méthodes surchargées aient des paramètres de méthode différents.) Maintenant que les signatures sont uniques, vous pouvez respecter la convention MVC et utiliser le même nom pour les méthodes de suppression HttpPost et HttpGet.</span><span class="sxs-lookup"><span data-stu-id="793ff-261">(The CLR requires overloaded methods to have different method parameters.) Now that the signatures are unique, you can stick with the MVC convention and use the same name for the HttpPost and HttpGet delete methods.</span></span>

<span data-ttu-id="793ff-262">Si le département est déjà supprimé, la méthode `AnyAsync` retourne la valeur false et l’application revient simplement à la méthode Index.</span><span class="sxs-lookup"><span data-stu-id="793ff-262">If the department is already deleted, the `AnyAsync` method returns false and the application just goes back to the Index method.</span></span>

<span data-ttu-id="793ff-263">Si une erreur d’accès concurrentiel est interceptée, le code réaffiche la page de confirmation de suppression et fournit un indicateur indiquant qu’elle doit afficher un message d’erreur d’accès concurrentiel.</span><span class="sxs-lookup"><span data-stu-id="793ff-263">If a concurrency error is caught, the code redisplays the Delete confirmation page and provides a flag that indicates it should display a concurrency error message.</span></span>

### <a name="update-the-delete-view"></a><span data-ttu-id="793ff-264">Mettre à jour la vue Delete</span><span class="sxs-lookup"><span data-stu-id="793ff-264">Update the Delete view</span></span>

<span data-ttu-id="793ff-265">Dans *Views/Departments/Delete.cshtml*, remplacez le code du modèle généré automatiquement par le code suivant, qui ajoute un champ de message d’erreur et des champs masqués pour les propriétés DepartmentID et RowVersion.</span><span class="sxs-lookup"><span data-stu-id="793ff-265">In *Views/Departments/Delete.cshtml*, replace the scaffolded code with the following code that adds an error message field and hidden fields for the DepartmentID and RowVersion properties.</span></span> <span data-ttu-id="793ff-266">Les modifications sont mises en surbrillance.</span><span class="sxs-lookup"><span data-stu-id="793ff-266">The changes are highlighted.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Departments/Delete.cshtml?highlight=9,38,44,45,48)]

<span data-ttu-id="793ff-267">Ceci apporte les modifications suivantes :</span><span class="sxs-lookup"><span data-stu-id="793ff-267">This makes the following changes:</span></span>

* <span data-ttu-id="793ff-268">Ajoute un message d’erreur entre les titres `h2` et `h3`.</span><span class="sxs-lookup"><span data-stu-id="793ff-268">Adds an error message between the `h2` and `h3` headings.</span></span>

* <span data-ttu-id="793ff-269">Il remplace FirstMidName par FullName dans le champ **Administrator**.</span><span class="sxs-lookup"><span data-stu-id="793ff-269">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>

* <span data-ttu-id="793ff-270">Supprime le champ RowVersion.</span><span class="sxs-lookup"><span data-stu-id="793ff-270">Removes the RowVersion field.</span></span>

* <span data-ttu-id="793ff-271">Ajoute un champ masqué pour la propriété `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="793ff-271">Adds a hidden field for the `RowVersion` property.</span></span>

<span data-ttu-id="793ff-272">Exécutez l’application et accédez à la page Index des départements.</span><span class="sxs-lookup"><span data-stu-id="793ff-272">Run the app and go to the Departments Index page.</span></span> <span data-ttu-id="793ff-273">Cliquez avec le bouton droit sur le lien hypertexte **Delete** pour le département « English », sélectionnez **Ouvrir dans un nouvel onglet** puis, sous le premier onglet, cliquez sur le lien hypertexte **Edit** pour le département « English ».</span><span class="sxs-lookup"><span data-stu-id="793ff-273">Right-click the **Delete** hyperlink for the English department and select **Open in new tab**, then in the first tab click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="793ff-274">Dans la première fenêtre, changez une des valeurs, puis cliquez sur **Save** :</span><span class="sxs-lookup"><span data-stu-id="793ff-274">In the first window, change one of the values, and click **Save**:</span></span>

![Page Edit pour les départements après modification avant la suppression](concurrency/_static/edit-after-change-for-delete.png)

<span data-ttu-id="793ff-276">Sous le deuxième onglet, cliquez sur **Delete**.</span><span class="sxs-lookup"><span data-stu-id="793ff-276">In the second tab, click **Delete**.</span></span> <span data-ttu-id="793ff-277">Vous voyez le message d’erreur d’accès concurrentiel et les valeurs du département sont actualisées avec ce qui est actuellement dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="793ff-277">You see the concurrency error message, and the Department values are refreshed with what's currently in the database.</span></span>

![Page de confirmation de suppression du département avec erreur d’accès concurrentiel](concurrency/_static/delete-error.png)

<span data-ttu-id="793ff-279">Si vous recliquez sur **Delete**, vous êtes redirigé vers la page Index, qui montre que le département a été supprimé.</span><span class="sxs-lookup"><span data-stu-id="793ff-279">If you click **Delete** again, you're redirected to the Index page, which shows that the department has been deleted.</span></span>

## <a name="update-details-and-create-views"></a><span data-ttu-id="793ff-280">Mettre à jour les vues Details et Create</span><span class="sxs-lookup"><span data-stu-id="793ff-280">Update Details and Create views</span></span>

<span data-ttu-id="793ff-281">Vous pouvez éventuellement nettoyer le code du modèle généré automatiquement dans les vues Details et Create.</span><span class="sxs-lookup"><span data-stu-id="793ff-281">You can optionally clean up scaffolded code in the Details and Create views.</span></span>

<span data-ttu-id="793ff-282">Remplacez le code de *Views/Departments/Details.cshtml* pour supprimer la colonne RowVersion et afficher le nom complet de l’administrateur.</span><span class="sxs-lookup"><span data-stu-id="793ff-282">Replace the code in *Views/Departments/Details.cshtml* to delete the RowVersion column and show the full name of the Administrator.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Departments/Details.cshtml?highlight=35)]

<span data-ttu-id="793ff-283">Remplacez le code de *Views/Departments/Create.cshtml* pour ajouter une option de sélection à la liste déroulante.</span><span class="sxs-lookup"><span data-stu-id="793ff-283">Replace the code in *Views/Departments/Create.cshtml* to add a Select option to the drop-down list.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Departments/Create.cshtml?highlight=32-34)]

## <a name="get-the-code"></a><span data-ttu-id="793ff-284">Obtenir le code</span><span class="sxs-lookup"><span data-stu-id="793ff-284">Get the code</span></span>

[<span data-ttu-id="793ff-285">Télécharger ou afficher l’application complète.</span><span class="sxs-lookup"><span data-stu-id="793ff-285">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="793ff-286">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="793ff-286">Additional resources</span></span>

 <span data-ttu-id="793ff-287">Pour plus d’informations sur la gestion de l’accès concurrentiel dans EF Core, consultez [Conflits d’accès concurrentiel](/ef/core/saving/concurrency).</span><span class="sxs-lookup"><span data-stu-id="793ff-287">For more information about how to handle concurrency in EF Core, see [Concurrency conflicts](/ef/core/saving/concurrency).</span></span>

## <a name="next-steps"></a><span data-ttu-id="793ff-288">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="793ff-288">Next steps</span></span>

<span data-ttu-id="793ff-289">Dans ce tutoriel, vous allez :</span><span class="sxs-lookup"><span data-stu-id="793ff-289">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="793ff-290">Découvrez les conflits d’accès concurrentiel</span><span class="sxs-lookup"><span data-stu-id="793ff-290">Learned about concurrency conflicts</span></span>
> * <span data-ttu-id="793ff-291">Propriété de suivi ajoutée</span><span class="sxs-lookup"><span data-stu-id="793ff-291">Added a tracking property</span></span>
> * <span data-ttu-id="793ff-292">Créez un contrôleur Departments et des vues</span><span class="sxs-lookup"><span data-stu-id="793ff-292">Created Departments controller and views</span></span>
> * <span data-ttu-id="793ff-293">Vue Index mise à jour</span><span class="sxs-lookup"><span data-stu-id="793ff-293">Updated Index view</span></span>
> * <span data-ttu-id="793ff-294">Méthodes de modification mises à jour</span><span class="sxs-lookup"><span data-stu-id="793ff-294">Updated Edit methods</span></span>
> * <span data-ttu-id="793ff-295">Vue Edit mise à jour</span><span class="sxs-lookup"><span data-stu-id="793ff-295">Updated Edit view</span></span>
> * <span data-ttu-id="793ff-296">Conflits d’accès concurrentiel testés</span><span class="sxs-lookup"><span data-stu-id="793ff-296">Tested concurrency conflicts</span></span>
> * <span data-ttu-id="793ff-297">Mettre à jour la page Delete</span><span class="sxs-lookup"><span data-stu-id="793ff-297">Updated the Delete page</span></span>
> * <span data-ttu-id="793ff-298">Vues Details et Create mises à jour</span><span class="sxs-lookup"><span data-stu-id="793ff-298">Updated Details and Create views</span></span>

<span data-ttu-id="793ff-299">Passez au tutoriel suivant pour découvrir comment implémenter l’héritage table par hiérarchie pour les entités Instructor et Student.</span><span class="sxs-lookup"><span data-stu-id="793ff-299">Advance to the next tutorial to learn how to implement table-per-hierarchy inheritance for the Instructor and Student entities.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="793ff-300">Étape suivante : implémenter l’héritage TPH (table par hiérarchie)</span><span class="sxs-lookup"><span data-stu-id="793ff-300">Next: Implement table-per-hierarchy inheritance</span></span>](inheritance.md)
