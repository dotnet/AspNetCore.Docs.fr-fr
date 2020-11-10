---
title: Partie 8, ajouter une validation
author: rick-anderson
description: Partie 8 de la série de didacticiels sur les Razor pages.
ms.author: riande
ms.custom: mvc
ms.date: 09/29/2020
no-loc:
- Index
- Create
- Delete
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
ms.openlocfilehash: efae7d79ff7a0b351afc68264463546bb26b4424
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422702"
---
# <a name="part-8-add-validation-to-an-aspnet-core-no-locrazor-page"></a>Partie 8, ajouter une validation à une Razor Page ASP.net Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

Dans cette section, une logique de validation est ajoutée au modèle `Movie`. Les règles de validation sont appliquées chaque fois qu’un utilisateur crée ou modifie un film.

## <a name="validation"></a>Validation

[DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) (« **D** on't **R** epeat **Y** ourself », Ne vous répétez pas) constitue un principe clé du développement de logiciel. Razor Les pages encouragent le développement dans lequel la fonctionnalité est spécifiée une seule fois, et elle est reflétée dans l’application. DRY peut aider à :

* Réduire la quantité de code dans une application.
* Rendre le code moins sujet aux erreurs, et plus facile à tester et à maintenir.

La prise en charge de la validation fournie par les Razor pages et les Entity Framework est un bon exemple du principe Dry :

* Les règles de validation sont spécifiées de façon déclarative à un seul emplacement, dans la classe de modèle.
* Les règles sont appliquées partout dans l’application.

## <a name="add-validation-rules-to-the-movie-model"></a>Ajouter des règles de validation au modèle de film

L' `DataAnnotations` espace de noms fournit les éléments suivants :

* Ensemble d’attributs de validation intégrés qui sont appliqués de façon déclarative à une classe ou une propriété.
* Les attributs de mise en forme tels `[DataType]` que l’aide à la mise en forme et ne fournissent aucune validation.

Mettez à jour la classe `Movie` pour tirer parti des attributs de validation intégrés `[Required]`, `[StringLength]`, `[RegularExpression]` et `[Range]`.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

Les attributs de validation spécifient le comportement à appliquer sur les propriétés de modèle auxquelles ils sont appliqués :

* Les attributs `[Required]` et `[MinimumLength]` indiquent qu’une propriété doit avoir une valeur. Rien n’empêche un utilisateur d’entrer un espace blanc pour satisfaire cette validation.
* L’attribut `[RegularExpression]` sert à limiter les caractères pouvant être entrés. Dans le code précédent, `Genre` :

  * Doit utiliser seulement des lettres.
  * La première lettre doit être une majuscule. Les espaces, les chiffres et les caractères spéciaux ne sont pas autorisés.

* Le `RegularExpression` `Rating` :

  * Nécessite que le premier caractère soit une lettre majuscule.
  * Autorise les caractères spéciaux et les nombres dans les espaces suivants. « PG-13 » est valide pour une évaluation, mais échoue pour un `Genre` .

* L’attribut `[Range]` limite une valeur à une plage spécifiée.
* L' `[StringLength]` attribut peut définir une longueur maximale d’une propriété de type chaîne et, éventuellement, sa longueur minimale.
* Les types valeur, tels que,,, `decimal` `int` `float` `DateTime` , sont obligatoires par nature et n’ont pas besoin de l' `[Required]` attribut.

Les règles de validation précédentes sont utilisées pour la démonstration, elles ne sont pas optimales pour un système de production. Par exemple, le précédent empêche l’entrée d’un film avec seulement deux caractères et n’autorise pas les caractères spéciaux dans `Genre` .

L’application automatique des règles de validation par ASP.NET Core aide à :

* Permet de rendre l’application plus robuste.
* Réduisez les risques d’enregistrement des données non valides dans la base de données.

### <a name="validation-error-ui-in-no-locrazor-pages"></a>Interface utilisateur d’erreur de validation dans les Razor pages

Exécutez l’application, puis accédez à Pages/Movies.

Sélectionnez le **Create nouveau** lien. Complétez le formulaire avec des valeurs non valides. Quand la validation jQuery côté client détecte l’erreur, elle affiche un message d’erreur.

![Formulaire de vue Movie avec plusieurs erreurs de validation jQuery côté client](validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

Notez que le formulaire a affiché automatiquement un message d’erreur de validation dans chaque champ contenant une valeur non valide. Les erreurs sont appliquées à la fois côté client, à l’aide de JavaScript et jQuery et côté serveur, lorsqu’un utilisateur a désactivé JavaScript.

L’un des principaux avantages est qu' **aucune** modification du code n’était nécessaire dans les Create pages ou de modification. Une fois que les annotations de données ont été appliquées au modèle, l’interface utilisateur de validation a été activée. Les Razor pages créées dans ce didacticiel ont automatiquement récupéré les règles de validation, à l’aide des attributs de validation sur les propriétés de la `Movie` classe de modèle. Testez la validation à l’aide de la page de modification. La même validation est appliquée.

Les données de formulaire ne sont pas publiées sur le serveur tant qu’il y a des erreurs de validation côté client. Vérifiez que les données du formulaire ne sont pas publiées à l’aide d’une ou de plusieurs des approches suivantes :

* Placez un point d’arrêt dans la méthode `OnPostAsync`. Envoyez le formulaire en sélectionnant **Create** ou **Enregistrer**. Le point d’arrêt n’est jamais atteint.
* Utilisez l’[outil Fiddler](https://www.telerik.com/fiddler).
* Utilisez les outils de développement du navigateur pour surveiller le trafic réseau.

### <a name="server-side-validation"></a>Validation côté serveur

Quand JavaScript est désactivé dans le navigateur, l’envoi du formulaire avec des erreurs poste les données sur le serveur.

Facultatif : Testez la validation côté serveur :

1. Désactivez JavaScript dans le navigateur. JavaScript peut être désactivé à l’aide des outils de développement du navigateur. Si JavaScript ne peut pas être désactivé dans le navigateur, essayez un autre navigateur.
1. Définissez un point d’arrêt dans la `OnPostAsync` méthode de la Create page de modification ou.
1. Envoyez un formulaire avec des données non valides.
1. Vérifiez que l’état de modèle n’est pas valide :

   ```csharp
    if (!ModelState.IsValid)
    {
       return Page();
    }
   ```
  
Vous pouvez également [désactiver la validation côté client sur le serveur](xref:mvc/models/validation#disable-client-side-validation).

Le code suivant illustre une partie de la page *Create . cshtml* , qui a été généré au préalable dans le didacticiel. Elle est utilisée par le Create et les pages de modification pour :

* Affichez le formulaire initial.
* Réaffichez le formulaire en cas d’erreur.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

Le [Tag Helper d’entrée](xref:mvc/views/working-with-forms) utilise les attributs [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) et produit les attributs HTML nécessaires à la validation jQuery côté client. Le [Tag Helper Validation](xref:mvc/views/working-with-forms#the-validation-tag-helpers) affiche les erreurs de validation. Pour plus d’informations, consultez [Validation](xref:mvc/models/validation).

Les Create pages de modification et ne contiennent pas de règles de validation. Les règles de validation et les chaînes d’erreur sont spécifiées uniquement dans la classe `Movie`. Ces règles de validation sont appliquées automatiquement aux Razor pages qui modifient le `Movie` modèle.

Quand la logique de validation doit être modifiée, cela s’effectue uniquement dans le modèle. La validation est appliquée de manière cohérente dans toute l’application, la logique de validation est définie à un seul emplacement. La validation dans un emplacement unique permet de maintenir votre code clair, et d’en faciliter la maintenance et la mise à jour.

## <a name="use-datatype-attributes"></a>Utiliser les attributs DataType

Examiner la classe `Movie`. L’espace de noms `System.ComponentModel.DataAnnotations` fournit des attributs de mise en forme en plus de l’ensemble intégré d’attributs de validation. L’attribut `[DataType]` est appliqué aux propriétés `ReleaseDate` et `Price`.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

Les `[DataType]` attributs fournissent :

* Indicateurs pour le moteur d’affichage pour mettre en forme les données.
* Fournit des attributs tels que `<a>` pour les URL et `<a href="mailto:EmailAddress.com">` pour les courriers électroniques.

Utilisez l’attribut `[RegularExpression]` pour valider le format des données. L’attribut `[DataType]` sert à spécifier un type de données qui est plus spécifique que le type intrinsèque de la base de données. `[DataType]` les attributs ne sont pas des attributs de validation. Dans l’exemple d’application, seule la date est affichée, sans l’heure.

L' `DataType` énumération fournit de nombreux types de données, tels que,,,,, `Date` `Time` `PhoneNumber` `Currency` `EmailAddress` et bien plus encore. 

`[DataType]`Attributs :

* Peut permettre à l’application de fournir automatiquement des fonctionnalités propres au type. Par exemple, il est possible de créer un lien `mailto:` pour `DataType.EmailAddress`.
* Peut fournir un sélecteur `DataType.Date` de date dans les navigateurs qui prennent en charge HTML5.
* Émettez le code HTML 5 `data-` , prononcé « Dash de données », attributs consommés par les navigateurs HTML 5.
* Ne fournissez **pas** de validation.

`DataType.Date` ne spécifie pas le format de la date qui s’affiche. Par défaut, le champ de données est affiché conformément aux formats par défaut basés sur le `CultureInfo` du serveur.

L’annotation de données `[Column(TypeName = "decimal(18, 2)")]` est nécessaire pour qu’Entity Framework Core puisse correctement mapper `Price` en devise dans la base de données. Pour plus d’informations, consultez [Types de données](/ef/core/modeling/relational/data-types).

L’attribut `[DisplayFormat]` est utilisé pour spécifier explicitement le format de date :

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

Le `ApplyFormatInEditMode` paramètre spécifie que la mise en forme sera appliquée lorsque la valeur sera affichée pour modification. Ce comportement peut ne pas être souhaité pour certains champs. Par exemple, dans les valeurs monétaires, le symbole monétaire n’est généralement pas souhaité dans l’interface utilisateur de modification.

L’attribut `[DisplayFormat]` peut être utilisé seul, mais il est généralement préférable d’utiliser l’attribut `[DataType]`. L’attribut `[DataType]` transmet la sémantique des données, plutôt que la manière de l’afficher à l’écran. L' `[DataType]` attribut offre les avantages suivants qui ne sont pas disponibles avec `[DisplayFormat]` :

* Le navigateur peut activer des fonctionnalités HTML5, par exemple pour afficher un contrôle de calendrier, les paramètres régionaux-symbole monétaire approprié, les liens de messagerie, etc.
* Par défaut, le navigateur restitue les données à l’aide du format correct en fonction de ses paramètres régionaux.
* L’attribut `[DataType]` peut permettre à l’infrastructure ASP.NET Core de choisir le modèle de champ approprié pour afficher les données. `DisplayFormat`, S’il est utilisé par lui-même, utilise le modèle de chaîne.

**Remarque :** la validation jQuery ne fonctionne pas avec l' `[Range]` attribut et `DateTime` . Par exemple, le code suivant affiche toujours une erreur de validation côté client, même quand la date se trouve dans la plage spécifiée :

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

Il est recommandé d’éviter de compiler des dates difficiles dans les modèles, de sorte que l’utilisation de l' `[Range]` attribut et `DateTime` est déconseillée. Utilisez la [configuration](xref:fundamentals/configuration/index) pour les plages de dates et d’autres valeurs sujettes à des modifications fréquentes au lieu de les spécifier dans le code.

Le code suivant illustre la combinaison d’attributs sur une seule ligne :

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDAmult.cs?name=snippet1)]

[Prise en main de Razor Pages et EF Core](xref:data/ef-rp/intro) affiche des opérations de EF Core avancées avec des Razor pages.

### <a name="apply-migrations"></a>Appliquer des migrations

Le DataAnnotations appliqué à la classe modifie le schéma. Par exemple, DataAnnotations appliqué au champ `Title` :

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet11)]

* limite les caractères à 60 ;
* n’autorise pas de valeur `null`.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

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

Dans le menu **Outils** , sélectionnez **Gestionnaire de package NuGet > Console du gestionnaire de package**.
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

Pour plus d’informations sur le déploiement sur Azure, consultez [Didacticiel : créer une application ASP.net core dans Azure avec SQL Database](/azure/app-service/tutorial-dotnetcore-sqldb-app).

Merci d’avoir effectué cette introduction aux Razor pages. [Prise en main de Razor Pages et EF Core](xref:data/ef-rp/intro) est un excellent suivi de ce didacticiel.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>

> [!div class="step-by-step"]
> [Précédent : ajouter un nouveau champ](xref:tutorials/razor-pages/new-field)
