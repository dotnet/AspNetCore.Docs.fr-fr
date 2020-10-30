---
title: Partie 8, ajouter une validation à une Razor Page ASP.net Core
author: rick-anderson
description: Partie 8 de la série de didacticiels sur les Razor pages.
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
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
uid: tutorials/razor-pages/validation
ms.openlocfilehash: 991a0f29c0edc5a220dfde69bd22dc4ed758394d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060727"
---
# <a name="part-8-add-validation-to-an-aspnet-core-no-locrazor-page"></a>Partie 8, ajouter une validation à une Razor Page ASP.net Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

Dans cette section, une logique de validation est ajoutée au modèle `Movie`. Les règles de validation sont appliquées chaque fois qu’un utilisateur crée ou modifie un film.

## <a name="validation"></a>Validation

[DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) (« **D** on't **R** epeat **Y** ourself », Ne vous répétez pas) constitue un principe clé du développement de logiciel. Razor Les pages encouragent le développement dans lequel la fonctionnalité est spécifiée une seule fois, et elle est reflétée dans l’application. DRY peut aider à :

* Réduire la quantité de code dans une application.
* Rendre le code moins sujet aux erreurs, et plus facile à tester et à maintenir.

La prise en charge de la validation fournie par les Razor pages et les Entity Framework est un bon exemple du principe Dry. Des règles de validation sont spécifiées de façon déclarative à un seul emplacement (dans la classe de modèle), et sont appliquées partout dans l’application.

## <a name="add-validation-rules-to-the-movie-model"></a>Ajouter des règles de validation au modèle de film

L’espace de noms DataAnnotations fournit un ensemble d’attributs de validation intégrés qui sont appliqués de façon déclarative à une classe ou à une propriété. DataAnnotations contient également des attributs de mise en forme, comme `DataType`, qui aident à effectuer la mise en forme et ne fournissent aucune validation.

Mettez à jour la classe `Movie` pour tirer parti des attributs de validation intégrés `Required`, `StringLength`, `RegularExpression` et `Range`.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

Les attributs de validation spécifient le comportement que vous souhaitez appliquer sur les propriétés du modèle sur lesquels ils sont appliqués :

* Les attributs `Required` et `MinimumLength` indiquent qu’une propriété doit avoir une valeur, mais rien n’empêche un utilisateur d’entrer un espace pour satisfaire à cette validation.
* L’attribut `RegularExpression` sert à limiter les caractères pouvant être entrés. Dans le code précédent, « Genre » :

  * Doit utiliser seulement des lettres.
  * La première lettre doit être une majuscule. Les espaces, les chiffres et les caractères spéciaux ne sont pas autorisés.

* L’expression `RegularExpression` « Rating » :

  * Nécessite que le premier caractère soit une lettre majuscule.
  * Autorise les caractères spéciaux et les chiffres aux emplacements qui suivent. « PG-13 » est valide pour une évaluation, mais échoue pour un « Genre ».

* L’attribut `Range` limite une valeur à une plage spécifiée.
* L’attribut `StringLength` vous permet de définir la longueur maximale d’une propriété de chaîne, et éventuellement sa longueur minimale.
* Les types valeur (tels que `decimal`, `int`, `float` et `DateTime`) sont obligatoires par nature et n’ont pas besoin de l’attribut `[Required]`.

L’application automatique des règles de validation par ASP.NET Core permet d’accroître la fiabilité de votre application. Cela garantit également que vous n’oublierez pas de valider un élément et que vous n’autoriserez pas par inadvertance l’insertion de données incorrectes dans la base de données.

### <a name="validation-error-ui-in-no-locrazor-pages"></a>Interface utilisateur d’erreur de validation dans les Razor pages

Exécutez l’application, puis accédez à Pages/Movies.

Sélectionnez le lien **Créer nouveau** . Complétez le formulaire avec des valeurs non valides. Quand la validation jQuery côté client détecte l’erreur, elle affiche un message d’erreur.

![Formulaire de vue Movie avec plusieurs erreurs de validation jQuery côté client](validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

Notez que le formulaire a affiché automatiquement un message d’erreur de validation dans chaque champ contenant une valeur non valide. Les erreurs sont appliquées à la fois côté client (à l’aide de JavaScript et de jQuery) et côté serveur (quand JavaScript est désactivé pour un utilisateur).

L’un des principaux avantages est qu’ **aucun** changement de code n’a été nécessaire dans les pages Créer ou Modifier. Une fois les attributs DataAnnotations appliqués au modèle, l’interface utilisateur de validation a été activée. Les Razor pages créées dans ce didacticiel ont automatiquement récupéré les règles de validation (à l’aide des attributs de validation sur les propriétés de la `Movie` classe de modèle). Testez la validation à l’aide de la page de modification. La même validation est appliquée.

Les données de formulaire ne sont pas publiées sur le serveur tant qu’il y a des erreurs de validation côté client. Vérifiez que les données du formulaire ne sont pas publiées à l’aide d’une ou de plusieurs des approches suivantes :

* Placez un point d’arrêt dans la méthode `OnPostAsync`. Envoyer le formulaire (en sélectionnant **Créer** ou **Enregistrer** ). Le point d’arrêt n’est jamais atteint.
* Utilisez l’[outil Fiddler](https://www.telerik.com/fiddler).
* Utilisez les outils de développement du navigateur pour surveiller le trafic réseau.

### <a name="server-side-validation"></a>Validation côté serveur

Quand JavaScript est désactivé dans le navigateur, l’envoi du formulaire avec des erreurs poste les données sur le serveur.

Facultatif : Testez la validation côté serveur :

* Désactivez JavaScript dans le navigateur. Vous pouvez désactiver JavaScript à l’aide d’outils de développement du navigateur. Si vous ne pouvez pas désactiver JavaScript dans le navigateur, essayez un autre navigateur.
* Définissez un point d’arrêt dans la méthode `OnPostAsync` de la page Créer ou Modifier.
* Envoyez un formulaire avec des données non valides.
* Vérifiez que l’état de modèle n’est pas valide :

  ```csharp
   if (!ModelState.IsValid)
   {
      return Page();
   }
  ```
  
Vous pouvez également [désactiver la validation côté client sur le serveur](xref:mvc/models/validation#disable-client-side-validation).

Le code suivant affiche la partie de la page *Create.cshtml* générée automatiquement plus tôt dans le tutoriel. Elle est utilisée par les pages Créer et Modifier pour afficher le formulaire initial et le réafficher en cas d’erreur.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

Le [Tag Helper d’entrée](xref:mvc/views/working-with-forms) utilise les attributs [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) et produit les attributs HTML nécessaires à la validation jQuery côté client. Le [Tag Helper Validation](xref:mvc/views/working-with-forms#the-validation-tag-helpers) affiche les erreurs de validation. Pour plus d’informations, consultez [Validation](xref:mvc/models/validation).

Les pages Créer et Modifier ne contiennent pas de règles de validation. Les règles de validation et les chaînes d’erreur sont spécifiées uniquement dans la classe `Movie`. Ces règles de validation sont appliquées automatiquement aux Razor pages qui modifient le `Movie` modèle.

Quand la logique de validation doit être modifiée, cela s’effectue uniquement dans le modèle. La validation est appliquée de manière cohérente dans l’ensemble de l’application (la logique de validation est définie dans un emplacement unique). La validation dans un emplacement unique permet de maintenir votre code clair, et d’en faciliter la maintenance et la mise à jour.

## <a name="using-datatype-attributes"></a>Utilisation d’attributs DataType

Examiner la classe `Movie`. L’espace de noms `System.ComponentModel.DataAnnotations` fournit des attributs de mise en forme en plus de l’ensemble intégré d’attributs de validation. L’attribut `DataType` est appliqué aux propriétés `ReleaseDate` et `Price`.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

Les attributs `DataType` fournissent uniquement des conseils permettant au moteur de vue de mettre en forme les données (et fournissent des attributs tels que `<a>` pour les URL et `<a href="mailto:EmailAddress.com">` pour l’e-mail). Utilisez l’attribut `RegularExpression` pour valider le format des données. L’attribut `DataType` sert à spécifier un type de données qui est plus spécifique que le type intrinsèque de la base de données. Les attributs `DataType` ne sont pas des attributs de validation. Dans l’exemple d’application, seule la date est affichée, sans l’heure.

L’énumération `DataType` fournit de nombreux types de données, tels que Date, Time, PhoneNumber, Currency ou EmailAddress. L’attribut `DataType` peut également permettre à l’application de fournir automatiquement des fonctionnalités propres au type. Par exemple, il est possible de créer un lien `mailto:` pour `DataType.EmailAddress`. Un sélecteur de date peut être fourni pour `DataType.Date` dans les navigateurs qui prennent en charge HTML5. Les `DataType` attributs émettent des attributs HTML 5 `data-` (prononciation des données) que les navigateurs HTML 5 consomment. Les attributs `DataType` ne fournissent **aucune** validation.

`DataType.Date` ne spécifie pas le format de la date qui s’affiche. Par défaut, le champ de données est affiché conformément aux formats par défaut basés sur le `CultureInfo` du serveur.

L’annotation de données `[Column(TypeName = "decimal(18, 2)")]` est nécessaire pour qu’Entity Framework Core puisse correctement mapper `Price` en devise dans la base de données. Pour plus d’informations, consultez [Types de données](/ef/core/modeling/relational/data-types).

L’attribut `DisplayFormat` est utilisé pour spécifier explicitement le format de date :

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

Le paramètre `ApplyFormatInEditMode` indique que la mise en forme doit être appliquée quand la valeur est affichée à des fins de modification. Vous pouvez souhaiter ce comportement pour certains champs. Par exemple, dans les valeurs monétaires, vous ne voulez probablement pas le symbole monétaire dans l’interface utilisateur de modification.

L’attribut `DisplayFormat` peut être utilisé seul, mais il est généralement préférable d’utiliser l’attribut `DataType`. L’attribut `DataType` donne la sémantique des données, plutôt que de décrire comment effectuer le rendu sur un écran, et il offre les avantages suivants dont vous ne bénéficiez pas avec DisplayFormat :

* Le navigateur peut activer des fonctionnalités HTML5 (par exemple pour afficher un contrôle de calendrier, le symbole monétaire correspondant aux paramètres régionaux, des liens de messagerie, etc.).
* Par défaut, le navigateur affiche les données à l’aide du format correspondant à vos paramètres régionaux.
* L’attribut `DataType` peut permettre à l’infrastructure ASP.NET Core de choisir le modèle de champ approprié pour afficher les données. `DisplayFormat`, S’il est utilisé par lui-même, utilise le modèle de chaîne.

**Remarque :** la validation jQuery ne fonctionne pas avec l' `Range` attribut et `DateTime` . Par exemple, le code suivant affiche toujours une erreur de validation côté client, même quand la date se trouve dans la plage spécifiée :

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

Il n’est généralement pas recommandé de compiler des dates dures dans vos modèles. Par conséquent, l’utilisation de l’attribut `Range` et de `DateTime` est déconseillée.

Le code suivant illustre la combinaison d’attributs sur une seule ligne :

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDAmult.cs?name=snippet1)]

[Prise en main de Razor Pages et EF Core](xref:data/ef-rp/intro) affiche des opérations de EF Core avancées avec des Razor pages.

### <a name="apply-migrations"></a>Appliquer des migrations

Le DataAnnotations appliqué à la classe modifie le schéma. Par exemple, DataAnnotations appliqué au champ `Title` :

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet11)]

* limite les caractères à 60 ;
* n’autorise pas de valeur `null`.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

La table `Movie` a actuellement le schéma suivant :

```sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (MAX)  NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (MAX)  NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (MAX)  NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

Les modifications précédentes du schéma n’entraînent pas la levée d’une exception par EF. Cependant, créez une migration pour que le schéma soit cohérent avec le modèle.

Dans le menu **Outils** , sélectionnez **Gestionnaire de package NuGet > Console du gestionnaire de package** .
Dans la console du gestionnaire de package, entrez les commandes suivantes :

```powershell
Add-Migration New_DataAnnotations
Update-Database
```

`Update-Database` exécute les méthodes `Up` de la classe `New_DataAnnotations`. Examinez la méthode `Up` :

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Migrations/20190724163003_New_DataAnnotations.cs?name=snippet)]

La table `Movie` mise à jour a le schéma suivant :

```sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (60)   NOT NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (30)   NOT NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (5)    NOT NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pour Mac](#tab/visual-studio-code+visual-studio-mac)

Des migrations ne sont pas requises pour SQLite.

---

### <a name="publish-to-azure"></a>Publication dans Azure

Pour plus d’informations sur le déploiement sur Azure, consultez [Didacticiel : créer une application ASP.net core dans Azure avec SQL Database](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).

Merci d’avoir effectué cette introduction aux Razor pages. [Prise en main de Razor Pages et EF Core](xref:data/ef-rp/intro) est un excellent suivi de ce didacticiel.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>
* [Version YouTube de ce tutoriel](https://youtu.be/b63m66eu7us)

> [!div class="step-by-step"]
> [Précédent : Ajout d’un nouveau champ](xref:tutorials/razor-pages/new-field)
