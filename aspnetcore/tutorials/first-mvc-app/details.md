---
title: Partie 10, examiner les méthodes Details et Delete d’une application ASP.NET Core
author: rick-anderson
description: Partie 10 de la série de didacticiels sur ASP.NET Core MVC.
ms.author: riande
ms.date: 12/13/2018
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
uid: tutorials/first-mvc-app/details
ms.openlocfilehash: 9ceb6c3c8a6622d6e203641c2ce97a483d3d4325
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93050743"
---
# <a name="part-10-examine-the-details-and-delete-methods-of-an-aspnet-core-app"></a>Partie 10, examiner les méthodes Details et Delete d’une application ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

Ouvrez le contrôleur vidéo et examinez la méthode `Details` :

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_details)]

Le moteur de génération de modèles automatique MVC qui a créé cette méthode d’action ajoute un commentaire présentant une requête HTTP qui appelle la méthode. Dans le cas présent, il s’agit d’une requête GET avec trois segments d’URL : le contrôleur `Movies`, la méthode `Details` et une valeur `id`. N’oubliez pas que ces segments sont définis dans *Startup.cs*.

[!code-csharp[](start-mvc/sample/MvcMovie3/Startup.cs?highlight=5&name=snippet_1)]

EF facilite la recherche de données à l’aide de la méthode `FirstOrDefaultAsync`. Une fonctionnalité de sécurité importante intégrée à la méthode réside dans le fait que le code vérifie que la méthode de recherche a trouvé un film avant de tenter toute opération que ce soit avec lui. Par exemple, un pirate informatique pourrait induire des erreurs dans le site en modifiant l’URL créée par les liens, en remplaçant `http://localhost:{PORT}/Movies/Details/1` par quelque chose comme `http://localhost:{PORT}/Movies/Details/12345` (ou une autre valeur qui ne représente pas un film réel). Si vous n’avez pas recherché un film non valide, l’application lève une exception.

Examinez les méthodes `Delete` et `DeleteConfirmed`.

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_delete)]

Notez que la méthode `HTTP GET Delete` ne supprime pas le film spécifié, mais retourne une vue du film où vous pouvez soumettre (HttpPost) la suppression. L’exécution d’une opération de suppression en réponse à une requête GET (ou encore l’exécution d’une opération de modification, d’une opération de création ou de toute autre opération qui modifie des données) génère une faille de sécurité.

La méthode `[HttpPost]` qui supprime les données est nommée `DeleteConfirmed` pour donner à la méthode HTTP POST une signature ou un nom unique. Les signatures des deux méthodes sont illustrées ci-dessous :

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete2)]

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete3)]

Le Common Language Runtime (CLR) nécessite des méthodes surchargées pour avoir une signature à paramètre unique (même nom de méthode, mais liste de paramètres différentes). Toutefois, vous avez ici besoin de deux méthodes `Delete` (une pour GET et une pour POST) ayant toutes les deux la même signature de paramètre. (Elles doivent toutes les deux accepter un entier unique comme paramètre.)

Il existe deux approches pour ce problème. L’une consiste à attribuer aux méthodes des noms différents. C’est ce qu’a fait le mécanisme de génération de modèles automatique dans l’exemple précédent. Toutefois, elle présente un petit problème : ASP.NET mappe des segments d’une URL à des méthodes d’action par nom. Si vous renommez une méthode, il est probable que le routage ne pourra pas trouver cette méthode. La solution consiste à faire ce que vous voyez dans l’exemple, c’est-à-dire à ajouter l’attribut `ActionName("Delete")` à la méthode `DeleteConfirmed`. Cet attribut effectue un mappage du système de routage afin qu’une URL qui inclut /Delete/ pour une requête POST trouve la méthode `DeleteConfirmed`.

Pour contourner le problème des méthodes qui ont des noms et des signatures identiques, vous pouvez également modifier artificiellement la signature de la méthode POST pour inclure un paramètre supplémentaire (inutilisé). C’est ce que nous avons fait dans une publication précédente quand nous avons ajouté le paramètre `notUsed`. Vous pouvez faire de même ici pour la méthode `[HttpPost] Delete` :

```csharp
// POST: Movies/Delete/6
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Delete(int id, bool notUsed)
```

### <a name="publish-to-azure"></a>Publication dans Azure

Pour plus d’informations sur le déploiement sur Azure, consultez [Didacticiel : créer une asp.net Core et SQL database application dans Azure App service](/azure/app-service/tutorial-dotnetcore-sqldb-app).

> [!div class="step-by-step"]
> [Précédent](validation.md)
