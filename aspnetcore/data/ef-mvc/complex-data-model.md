---
title: 'Didacticiel : créer un modèle de données complexe-ASP.NET MVC avec EF Core'
description: Dans ce tutoriel, vous ajoutez des entités et des relations, et vous personnalisez le modèle de données en spécifiant des règles de mise en forme, de validation et de mappage.
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
uid: data/ef-mvc/complex-data-model
ms.openlocfilehash: cee9e9eb4c5435f3f63f7d1d04f131d88effe9f6
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054474"
---
# <a name="tutorial-create-a-complex-data-model---aspnet-mvc-with-ef-core"></a>Didacticiel : créer un modèle de données complexe-ASP.NET MVC avec EF Core

Dans les didacticiels précédents, vous avez travaillé avec un modèle de données simple composé de trois entités. Dans ce didacticiel, vous allez ajouter des entités et des relations, et vous personnaliserez le modèle de données en spécifiant des règles de mise en forme, de validation et de mappage de base de données.

Lorsque vous aurez terminé, les classes d’entité composeront le modèle de données complet indiqué dans l’illustration suivante :

![Diagramme des entités](complex-data-model/_static/diagram.png)

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Personnaliser le modèle de données
> * Apporter des modifications à l’entité Student
> * Créer une entité Instructor
> * Créer une entité OfficeAssignment
> * Modifier l’entité Course
> * Créer l’entité Department
> * Modifier l’entité Enrollment
> * Mettre à jour le contexte de base de données
> * Remplir la base de données avec des données de test
> * Ajouter une migration
> * Changer la chaîne de connexion
> * Mettre à jour la base de données

## <a name="prerequisites"></a>Prérequis

* [À l’aide de migrations d’EF Core](migrations.md)

## <a name="customize-the-data-model"></a>Personnaliser le modèle de données

Dans cette section, vous allez apprendre à personnaliser le modèle de données en utilisant des attributs qui spécifient des règles de mise en forme, de validation et de mappage de base de données. Ensuite, dans plusieurs des sections suivantes, vous allez créer le modèle de données School complet en ajoutant des attributs aux classes que vous avez déjà créées et en créant de nouvelles classes pour les autres types d’entités dans le modèle.

### <a name="the-datatype-attribute"></a>Attribut DataType

Pour les dates d’inscription des étudiants, toutes les pages web affichent l’heure avec la date, alors que seule la date vous intéresse dans ce champ. Vous pouvez avoir recours aux attributs d’annotation de données pour apporter une modification au code, permettant de corriger le format d’affichage dans chaque vue qui affiche ces données. Pour voir un exemple de la procédure à suivre, vous allez ajouter un attribut à la propriété `EnrollmentDate` dans la classe `Student`.

Dans *Models/Student.cs*, ajoutez une instruction `using` pour l’espace de noms `System.ComponentModel.DataAnnotations` et ajoutez les attributs `DataType` et `DisplayFormat` à la propriété `EnrollmentDate`, comme indiqué dans l’exemple suivant :

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

L’attribut `DataType` sert à spécifier un type de données qui est plus spécifique que le type intrinsèque de la base de données. Dans le cas présent, nous voulons uniquement effectuer le suivi de la date, pas de la date et de l’heure. L’énumération `DataType` fournit de nombreux types de données, tels que Date, Time, PhoneNumber, Currency, EmailAddress, etc. L’attribut `DataType` peut également permettre à l’application de fournir automatiquement des fonctionnalités propres au type. Par exemple, vous pouvez créer un lien `mailto:` pour `DataType.EmailAddress`, et vous pouvez fournir un sélecteur de date pour `DataType.Date` dans les navigateurs qui prennent en charge HTML5. L’attribut `DataType` émet des attributs HTML 5 `data-` compréhensibles par les navigateurs HTML 5. Les attributs `DataType` ne fournissent aucune validation.

`DataType.Date` ne spécifie pas le format de la date qui s’affiche. Par défaut, le champ de données est affiché conformément aux formats par défaut basés sur l’objet CultureInfo du serveur.

L’attribut `DisplayFormat` est utilisé pour spécifier explicitement le format de date :

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

Le paramètre `ApplyFormatInEditMode` indique que la mise en forme doit également être appliquée quand la valeur est affichée dans une zone de texte à des fins de modification. (Ceci peut ne pas être souhaitable pour certains champs ; par exemple, pour les valeurs monétaires, vous ne souhaiterez peut-être pas que le symbole monétaire figure dans la zone de texte.)

Vous pouvez utiliser l’attribut `DisplayFormat` seul, mais il est généralement judicieux d’utiliser également l’attribut `DataType`. L’attribut `DataType` donne la sémantique des données au lieu d’expliquer comment les afficher à l’écran. Il présente, par ailleurs, les avantages suivants, dont vous ne bénéficiez pas avec `DisplayFormat` :

* Le navigateur peut activer des fonctionnalités HTML5 (par exemple pour afficher un contrôle de calendrier, le symbole monétaire correspondant aux paramètres régionaux, des liens de messagerie, une certaine validation des entrées côté client, etc.).

* Par défaut, le navigateur affiche les données à l’aide du format correspondant à vos paramètres régionaux.

Pour plus d’informations, consultez la [ \<input> documentation tag Helper](../../mvc/views/working-with-forms.md#the-input-tag-helper).

Exécutez l’application, accédez à la page d’index des étudiants et notez que les heures ne sont plus affichées pour les dates d’inscription. La même chose est vraie pour toute vue qui utilise le modèle Student.

![Page d’index des étudiants affichant les dates sans les heures](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a>Attribut StringLength

Vous pouvez également spécifier les règles de validation de données et les messages d’erreur de validation à l’aide d’attributs. L’attribut `StringLength` définit la longueur maximale dans la base de données et assure la validation côté client et côté serveur pour ASP.NET Core MVC. Vous pouvez également spécifier la longueur de chaîne minimale dans cet attribut, mais la valeur minimale n’a aucun impact sur le schéma de base de données.

Supposons que vous voulez garantir que les utilisateurs n’entrent pas plus de 50 caractères pour un nom. Pour ajouter cette limitation, ajoutez des attributs `StringLength` aux propriétés `LastName` et `FirstMidName`, comme indiqué dans l’exemple suivant :

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

L’attribut `StringLength` n’empêche pas un utilisateur d’entrer un espace blanc comme nom. Vous pouvez utiliser l’attribut `RegularExpression` pour appliquer des restrictions à l’entrée. Par exemple, le code suivant exige que le premier caractère soit en majuscule et que les autres caractères soient alphabétiques :

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z]*$")]
```

L’attribut `MaxLength` fournit des fonctionnalités similaires à l’attribut `StringLength`, mais n’assure pas la validation côté client.

Le modèle de base de données a maintenant changé d’une manière qui nécessite la modification du schéma de base de données. Vous allez utiliser des migrations pour mettre à jour le schéma sans perdre les données que vous avez éventuellement ajoutées à la base de données via l’interface utilisateur de l’application.

Enregistrez vos modifications et générez le projet. Ensuite, ouvrez la fenêtre de commande dans le dossier du projet et entrez les commandes suivantes :

```dotnetcli
dotnet ef migrations add MaxLengthOnNames
```

```dotnetcli
dotnet ef database update
```

La commande `migrations add` vous avertit qu’une perte de données peut se produire, car la modification raccourcit la longueur maximale de deux colonnes.  Les migrations créent un fichier nommé *\<timeStamp> _MaxLengthOnNames. cs*. Ce fichier contient du code dans la méthode `Up` qui met à jour la base de données pour qu’elle corresponde au modèle de données actuel. La commande `database update` a exécuté ce code.

L’horodatage utilisé comme préfixe du nom de fichier migrations est utilisé par Entity Framework pour ordonner les migrations. Vous pouvez créer plusieurs migrations avant d’exécuter la commande de mise à jour de base de données, puis toutes les migrations sont appliquées dans l’ordre où elles ont été créées.

Exécutez l’application, sélectionnez l’onglet **Students**, cliquez sur **Create New** et essayez d’entrer un nom de plus de 50 caractères. L’application doit empêcher cette opération. 

### <a name="the-column-attribute"></a>Attribut Column

Vous pouvez également utiliser des attributs pour contrôler la façon dont les classes et les propriétés sont mappées à la base de données. Supposons que vous aviez utilisé le nom `FirstMidName` pour le champ de prénom, car le champ peut également contenir un deuxième prénom. Mais vous souhaitez que la colonne de base de données soit nommée `FirstName`, car les utilisateurs qui écriront des requêtes ad-hoc par rapport à la base de données sont habitués à ce nom. Pour effectuer ce mappage, vous pouvez utiliser l’attribut `Column`.

L’attribut `Column` spécifie que lorsque la base de données sera créée, la colonne de la table `Student` qui est mappée sur la propriété `FirstMidName` sera nommée `FirstName`. En d’autres termes, lorsque votre code fait référence à `Student.FirstMidName`, les données proviennent de la colonne `FirstName` de la table `Student` ou y sont mises à jour. Si vous ne nommez pas les colonnes, elles obtiennent le nom de la propriété.

Dans le fichier *Student.cs*, ajoutez une instruction `using` pour `System.ComponentModel.DataAnnotations.Schema` et ajoutez l’attribut de nom de colonne à la propriété `FirstMidName`, comme indiqué dans le code en surbrillance suivant :

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Column&highlight=4,14)]

L’ajout de l’attribut `Column` change le modèle sur lequel repose `SchoolContext`, donc il ne correspond pas à la base de données.

Enregistrez vos modifications et générez le projet. Ensuite, ouvrez la fenêtre de commande dans le dossier du projet et entrez les commandes suivantes pour créer une autre migration :

```dotnetcli
dotnet ef migrations add ColumnFirstName
```

```dotnetcli
dotnet ef database update
```

Dans l’**Explorateur d’objets SQL Server**, ouvrez le concepteur de tables Student en double-cliquant sur la table **Student**.

![Table Students dans SSOX après les migrations](complex-data-model/_static/ssox-after-migration.png)

Avant d’appliquer les deux premières migrations, les colonnes de nom étaient de type nvarchar(MAX). Elles sont maintenant de type nvarchar(50) et le nom de colonne FirstMidName a été remplacé par FirstName.

> [!Note]
> Si vous essayez de compiler avant d’avoir fini de créer toutes les classes d’entité dans les sections suivantes, vous pouvez obtenir des erreurs de compilation.

## <a name="changes-to-student-entity"></a>Apporter des modifications à l’entité Student

![Entité Student](complex-data-model/_static/student-entity.png)

Dans *Models/Student.cs*, remplacez le code que vous avez ajouté précédemment par le code suivant. Les modifications sont mises en surbrillance.

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a>Attribut Required

L’attribut `Required` fait des propriétés de nom des champs obligatoires. L’attribut `Required` n’est pas requis pour les types non nullables tels que les types valeur (DateTime, int, double, float, etc.). Les types qui n’acceptent pas les valeurs Null sont traités automatiquement comme des champs obligatoires.

L'attribut `Required` doit être utilisé avec `MinimumLength` pour appliquer `MinimumLength`.

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a>Attribut Display

L’attribut `Display` spécifie que la légende pour les zones de texte doit être « First Name », « Last Name », « Full Name » et « Enrollment Date », au lieu du nom de propriété dans chaque instance (qui n’a pas d’espace pour séparer les mots).

### <a name="the-fullname-calculated-property"></a>Propriété calculée FullName

`FullName` est une propriété calculée qui retourne une valeur créée par concaténation de deux autres propriétés. Par conséquent, elle a uniquement un accesseur get et aucune colonne `FullName` n’est générée dans la base de données.

## <a name="create-instructor-entity"></a>Créer une entité Instructor

![Entité Instructor](complex-data-model/_static/instructor-entity.png)

Créez *Models/Instructor.cs*, en remplaçant le code du modèle par le code suivant :

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_BeforeInheritance)]

Notez que plusieurs propriétés sont identiques dans les entités Student et Instructor. Dans le didacticiel [Implémentation de l’héritage](inheritance.md) plus loin dans cette série, vous allez refactoriser ce code pour éliminer la redondance.

Vous pouvez placer plusieurs attributs sur une seule ligne et écrire les attributs `HireDate` comme suit :

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a>Propriétés de navigation CourseAssignments et OfficeAssignment

Les propriétés `CourseAssignments` et `OfficeAssignment` sont des propriétés de navigation.

Un formateur pouvant animer un nombre quelconque de cours, `CourseAssignments` est défini comme une collection.

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

Si une propriété de navigation peut contenir plusieurs entités, son type doit être une liste dans laquelle les entrées peuvent être ajoutées, supprimées et mises à jour.  Vous pouvez spécifier `ICollection<T>` ou un type tel que `List<T>` ou `HashSet<T>`. Si vous spécifiez `ICollection<T>`, EF crée une collection `HashSet<T>` par défaut.

La raison pour laquelle ce sont des entités `CourseAssignment` est expliquée ci-dessous dans la section sur les relations plusieurs-à-plusieurs.

Les règles d’entreprise de Contoso University stipulent qu’un formateur peut avoir au plus un bureau, de sorte que la propriété `OfficeAssignment` contient une seule entité OfficeAssignment (qui peut être null si aucun bureau n’est affecté).

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-officeassignment-entity"></a>Créer une entité OfficeAssignment

![Entité OfficeAssignment](complex-data-model/_static/officeassignment-entity.png)

Créez *Models/OfficeAssignment.cs* avec le code suivant :

[!code-csharp[](intro/samples/cu/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a>Attribut Key

Il existe une relation un-à-zéro-ou-un entre les entités Instructor et OfficeAssignment. Une affectation de bureau existe uniquement en relation avec le formateur auquel elle est affectée. Par conséquent, sa clé primaire est également sa clé étrangère pour l’entité Instructor. Mais Entity Framework ne peut pas reconnaître automatiquement InstructorID comme clé primaire de cette entité, car son nom ne suit pas la convention de nommage d’ID ou de classnameID. Par conséquent, l’attribut `Key` est utilisé pour l’identifier comme clé :

```csharp
[Key]
public int InstructorID { get; set; }
```

Vous pouvez également utiliser l’attribut `Key` si l’entité a sa propre clé primaire, mais que vous souhaitez nommer la propriété autrement que classnameID ou ID.

Par défaut, EF traite la clé comme n’étant pas générée par la base de données, car la colonne est utilisée pour une relation d’identification.

### <a name="the-instructor-navigation-property"></a>Propriété de navigation Instructor

L’entité Instructor a une propriété de navigation `OfficeAssignment` nullable (parce qu’un formateur n’a peut-être pas d’affectation de bureau) et l’entité OfficeAssignment a une propriété de navigation `Instructor` non nullable (comme une affectation de bureau ne peut pas exister sans formateur, `InstructorID` est non nullable). Lorsqu’une entité Instructor a une entité OfficeAssignment associée, chaque entité a une référence à l’autre dans sa propriété de navigation.

Vous pouvez placer un attribut `[Required]` sur la propriété de navigation du formateur pour spécifier qu’il doit y avoir un formateur associé, mais vous n’êtes pas obligé de le faire, car la clé étrangère `InstructorID` (qui est également la clé pour cette table) est non nullable.

## <a name="modify-course-entity"></a>Modifier l’entité Course

![Entité Course](complex-data-model/_static/course-entity.png)

Dans *Models/Course.cs*, remplacez le code que vous avez ajouté précédemment par le code suivant. Les modifications sont mises en surbrillance.

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

L’entité de cours a une propriété de clé étrangère `DepartmentID` qui pointe sur l’entité Department associée et elle a une propriété de navigation `Department`.

Entity Framework ne vous demande pas d’ajouter une propriété de clé étrangère à votre modèle de données lorsque vous avez une propriété de navigation pour une entité associée.  EF crée automatiquement des clés étrangères dans la base de données partout où elles sont nécessaires et crée des [propriétés fantôme](/ef/core/modeling/shadow-properties) pour elles. Mais le fait d’avoir la clé étrangère dans le modèle de données peut rendre les mises à jour plus simples et plus efficaces. Par exemple, lorsque vous récupérez une entité de cours à modifier, l’entité Department a la valeur Null si vous ne la chargez pas. Par conséquent, lorsque vous mettez à jour l’entité de cours, vous devriez tout d’abord récupérer l’entité Department. Lorsque la propriété de clé étrangère `DepartmentID` est incluse dans le modèle de données, vous n’avez pas besoin de récupérer l’entité Department avant de mettre à jour.

### <a name="the-databasegenerated-attribute"></a>Attribut DatabaseGenerated

L’attribut `DatabaseGenerated` avec le paramètre `None` sur la propriété `CourseID` spécifie que les valeurs de clé primaire sont fournies par l’utilisateur au lieu d’être générées par la base de données.

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

Par défaut, Entity Framework suppose que les valeurs de clé primaire sont générées par la base de données. C’est ce que vous souhaitez dans la plupart des scénarios. Toutefois, pour les entités Course, vous allez utiliser un numéro de cours spécifié par l’utilisateur comme une série de 1000 pour un département, une série de 2000 pour un autre département, etc.

L’attribut `DatabaseGenerated` peut également être utilisé pour générer des valeurs par défaut, comme dans le cas des colonnes de base de données utilisées pour enregistrer la date à laquelle une ligne a été créée ou mise à jour.  Pour plus d’informations, consultez [Propriétés générées](/ef/core/modeling/generated-properties).

### <a name="foreign-key-and-navigation-properties"></a>Propriétés de clé étrangère et de navigation

Les propriétés de clé étrangère et les propriétés de navigation dans l’entité Course reflètent les relations suivantes :

Un cours est affecté à un seul département, donc il existe une clé étrangère `DepartmentID` et une propriété de navigation `Department` pour les raisons mentionnées ci-dessus.

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

Un cours pouvant avoir un nombre quelconque d’étudiants inscrits, la propriété de navigation `Enrollments` est une collection :

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

Un cours peut être animé par plusieurs formateurs, si bien que la propriété de navigation `CourseAssignments` est une collection (le type `CourseAssignment` est expliqué [ultérieurement](#many-to-many-relationships)) :

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

## <a name="create-department-entity"></a>Créer l’entité Department

![Entité de service](complex-data-model/_static/department-entity.png)

Créez *Models/Department.cs* avec le code suivant :

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a>Attribut Column

Précédemment, vous avez utilisé l’attribut `Column` pour changer le mappage de noms de colonne. Dans le code de l’entité Department, l’attribut `Column` sert à modifier le mappage des types de données SQL afin que la colonne soit définie à l’aide du type monétaire (money) SQL Server dans la base de données :

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

Le mappage de colonnes n’est généralement pas nécessaire, car Entity Framework choisit le type de données SQL Server approprié en fonction du type CLR que vous définissez pour la propriété. Le type CLR `decimal` est mappé à un type SQL Server `decimal`. Toutefois, dans ce cas, vous savez que la colonne contiendra des montants en devise et que le type de données monétaire est plus approprié pour cela.

### <a name="foreign-key-and-navigation-properties"></a>Propriétés de clé étrangère et de navigation

Les propriétés de clé étrangère et de navigation reflètent les relations suivantes :

Un département peut ou non avoir un administrateur, et un administrateur est toujours un formateur. Par conséquent, la propriété `InstructorID` est incluse en tant que clé étrangère à l’entité Instructor, et un point d’interrogation est ajouté après la désignation du type `int` pour marquer la propriété comme nullable. La propriété de navigation est nommée `Administrator`, mais elle contient une entité Instructor :

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

Un département pouvant avoir de nombreux cours, il existe une propriété de navigation Courses :

```csharp
public ICollection<Course> Courses { get; set; }
```

> [!NOTE]
> Par convention, Entity Framework permet la suppression en cascade pour les clés étrangères non nullables et pour les relations plusieurs à plusieurs. Cela peut entraîner des règles de suppression en cascade circulaires, qui provoqueront une exception lorsque vous essaierez d’ajouter une migration. Par exemple, si vous n’avez pas défini la propriété Department.InstructorID comme nullable, EF configure une règle de suppression en cascade pour supprimer le département lorsque vous supprimez le formateur, ce qui n’est pas ce que vous voulez. Si vos règles d’entreprise exigent que la propriété `InstructorID` soit non nullable, vous devez utiliser l’instruction d’API Fluent suivante pour désactiver la suppression en cascade sur la relation :
>
> ```csharp
> modelBuilder.Entity<Department>()
>    .HasOne(d => d.Administrator)
>    .WithMany()
>    .OnDelete(DeleteBehavior.Restrict)
> ```

## <a name="modify-enrollment-entity"></a>Modifier l’entité Enrollment

![Entité Enrollment](complex-data-model/_static/enrollment-entity.png)

Dans *Models/Enrollment.cs*, remplacez le code que vous avez ajouté précédemment par le code suivant :

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a>Propriétés de clé étrangère et de navigation

Les propriétés de clé étrangère et de navigation reflètent les relations suivantes :

Un enregistrement d’inscription est utilisé pour un cours unique, si bien qu’il existe une propriété de clé étrangère `CourseID` et une propriété de navigation `Course` :

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

Un enregistrement d’inscription est utilisé pour un étudiant unique, si bien qu’il existe une propriété de clé étrangère `StudentID` et une propriété de navigation `Student` :

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a>Relations plusieurs-à-plusieurs

Il existe une relation plusieurs-à-plusieurs entre les entités Student et Course, et l’entité Enrollment fonctionne comme une table de jointure plusieurs-à-plusieurs *avec une charge utile* dans la base de données. « Avec une charge utile » signifie que la table Enrollment contient des données supplémentaires en plus des clés étrangères pour les tables jointes (dans ce cas, une clé primaire et une propriété Grade).

L’illustration suivante montre à quoi ressemblent ces relations dans un diagramme d’entité. (Ce diagramme a été généré à l’aide d’Entity Framework Power Tools pour EF 6.x ; la création du diagramme ne fait pas partie de ce didacticiel, elle est uniquement utilisée ici à titre d’illustration.)

![Relation plusieurs-à-plusieurs Student-Course](complex-data-model/_static/student-course.png)

Chaque ligne de relation comporte un 1 à une extrémité et un astérisque (*) à l’autre, ce qui indique une relation un-à-plusieurs.

Si la table Enrollment n’incluait pas d’informations de notes, elle aurait uniquement besoin de contenir les deux clés étrangères CourseID et StudentID. Dans ce cas, ce serait une table de jointure plusieurs-à-plusieurs sans charge utile (ou une table de jointure pure) dans la base de données. Les entités Instructor and Course ont ce type de relation plusieurs-à-plusieurs, et l’étape suivante consiste à créer une classe d’entité qui fonctionnera comme une table de jointure sans charge utile.

(EF 6.x prend en charge les tables de jointure implicites pour les relations plusieurs-à-plusieurs, mais EF Core ne le fait pas. Pour plus d’informations, consultez la [discussion dans le dépôt GitHub EF Core](https://github.com/aspnet/EntityFramework/issues/1368).)

## <a name="the-courseassignment-entity"></a>Entité CourseAssignment

![Entité CourseAssignment](complex-data-model/_static/courseassignment-entity.png)

Créez *Models/CourseAssignment.cs* avec le code suivant :

[!code-csharp[](intro/samples/cu/Models/CourseAssignment.cs)]

### <a name="join-entity-names"></a>Noms des entités de jointure

Une table de jointure est requise dans la base de données pour la relation plusieurs-à-plusieurs entre formateurs et cours, et elle doit être représentée par un jeu d’entités. Il est courant de nommer une entité de jointure `EntityName1EntityName2`, ce qui donnerait dans ce cas `CourseInstructor`. Toutefois, nous vous recommandons de choisir un nom qui décrit la relation. Les modèles de données sont simples au départ, puis croissent, avec des jointures sans charge utile qui obtiennent souvent des charges utiles plus tard. Si vous commencez avec un nom d’entité descriptif, vous n’aurez pas à le modifier par la suite. Dans l’idéal, l’entité de jointure aura son propre nom (éventuellement un mot unique) naturel dans le domaine d’entreprise. Par exemple, les livres et les clients pourraient être liés par le biais d’évaluations. Pour cette relation, `CourseAssignment` est un meilleur choix que `CourseInstructor`.

### <a name="composite-key"></a>Clé composite

Étant donné que les clés étrangères ne sont pas nullables et qu’elles identifient ensemble de façon unique chaque ligne de la table, une clé primaire distincte n’est pas requise. Les propriétés *InstructorID* et *CourseID* doivent fonctionner comme une clé primaire composite. La seule façon d’identifier des clés primaires composites pour EF consiste à utiliser l’*API Fluent* (ce n’est pas possible à l’aide d’attributs). Vous allez voir comment configurer la clé primaire composite dans la section suivante.

La clé composite garantit qu’en ayant plusieurs lignes pour un cours et plusieurs lignes pour un formateur, vous ne puissiez pas avoir plusieurs lignes pour les mêmes formateur et cours. L’entité de jointure `Enrollment` définit sa propre clé primaire, si bien que les doublons de ce type sont possibles. Pour éviter ces doublons, vous pourriez ajouter un index unique sur les champs de clé étrangère ou configurer `Enrollment` avec une clé composite primaire similaire à `CourseAssignment`. Pour plus d’informations, consultez [Index](/ef/core/modeling/indexes).

## <a name="update-the-database-context"></a>Mettre à jour le contexte de base de données

Ajoutez le code en surbrillance suivant au fichier *Data/SchoolContext.cs* :

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

Ce code ajoute les nouvelles entités et configure la clé primaire composite de l’entité CourseAssignment.

## <a name="about-a-fluent-api-alternative"></a>À propos de l’alternative d’API Fluent

Le code dans la méthode `OnModelCreating` de la classe `DbContext` utilise l’*API Fluent* pour configurer le comportement EF. L’API est appelée « fluent », car elle est souvent utilisée pour enchaîner une série d’appels de méthode en une seule instruction, comme dans cet exemple tiré de la [documentation d’EF Core](/ef/core/modeling/#use-fluent-api-to-configure-a-model) :

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

Dans ce didacticiel, vous utilisez l’API Fluent uniquement pour le mappage de base de données que vous ne pouvez pas faire avec des attributs. Toutefois, vous pouvez également utiliser l’API Fluent pour spécifier la majorité des règles de mise en forme, de validation et de mappage que vous pouvez spécifier à l’aide d’attributs. Certains attributs, tels que `MinimumLength`, ne peuvent pas être appliqués avec l’API Fluent. Comme mentionné précédemment, `MinimumLength` ne change pas le schéma, il applique uniquement une règle de validation côté client et côté serveur.

Certains développeurs préfèrent utiliser exclusivement l’API Fluent afin de conserver des classes d’entité « propres ». Vous pouvez combiner les attributs et l’API Fluent si vous le voulez, et il existe quelques personnalisations qui peuvent être effectuées uniquement à l’aide de l’API Fluent, mais en général la pratique recommandée consiste à choisir l’une de ces deux approches et à l’utiliser constamment, autant que possible. Si vous utilisez ces deux approches, notez que partout où il existe un conflit, l’API Fluent a priorité sur les attributs.

Pour plus d’informations sur les attributs et l’API Fluent, consultez [Méthodes de configuration](/ef/core/modeling/).

## <a name="entity-diagram-showing-relationships"></a>Diagramme des entités montrant les relations

L’illustration suivante montre le diagramme que les outils Entity Framework Power Tools créent pour le modèle School complet.

![Diagramme des entités](complex-data-model/_static/diagram.png)

Outre les lignes de relation un-à-plusieurs (1 à \*), vous pouvez voir ici la ligne de relation un-à-zéro-ou-un (1 à 0..1) entre les entités Instructor et OfficeAssignment et la ligne de relation zéro-ou-un-à-plusieurs (0..1 à *) entre les entités Instructor et Department.

## <a name="seed-database-with-test-data"></a>Remplir la base de données avec des données de test

Remplacez le code dans le fichier *Data/DbInitializer.cs* par le code suivant afin de fournir des données initiales pour les nouvelles entités que vous avez créées.

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Final)]

Comme vous l’avez vu dans le premier didacticiel, la majeure partie de ce code crée simplement de nouveaux objets d’entité et charge des exemples de données dans les propriétés requises pour les tests. Notez la façon dont les relations plusieurs à plusieurs sont gérées : le code crée des relations en créant des entités dans les jeux d’entités de jointure `Enrollments` et `CourseAssignment`.

## <a name="add-a-migration"></a>Ajouter une migration

Enregistrez vos modifications et générez le projet. Ensuite, ouvrez la fenêtre de commande dans le dossier du projet et entrez la commande `migrations add` (n’exécutez pas encore la commande de mise à jour de base de données) :

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

Vous obtenez un avertissement concernant une perte possible de données.

```text
An operation was scaffolded that may result in the loss of data. Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

Si vous tentiez d’exécuter la commande `database update` à ce stade (ne le faites pas encore), vous obtiendriez l’erreur suivante :

> L’instruction ALTER TABLE est en conflit avec la contrainte FOREIGN KEY « FK_dbo.Course_dbo.Department_DepartmentID ». Le conflit s’est produit dans la base de données « ContosoUniversity », table « dbo.Department », colonne « DepartmentID ».

Parfois, lorsque vous exécutez des migrations avec des données existantes, vous devez insérer des données stub dans la base de données pour répondre aux contraintes de clé étrangère. Le code généré dans la méthode `Up` ajoute une clé étrangère DepartmentID non nullable à la table Course. S’il existe déjà des lignes dans la table Course lorsque le code s’exécute, l’opération `AddColumn` échoue car SQL Server ne sait pas quelle valeur placer dans la colonne qui ne peut pas être null. Pour ce didacticiel, vous allez exécuter la migration sur une nouvelle base de données. Toutefois, dans une application de production, vous devriez faire en sorte que la migration traite les données existantes, si bien que les instructions suivantes montrent un exemple de la procédure à suivre pour ce faire.

Pour faire en sorte que cette migration fonctionne avec les données existantes, vous devez modifier le code pour attribuer à la nouvelle colonne une valeur par défaut et créer un département stub nommé « Temp » qui agira en tant que département par défaut. Par conséquent, les lignes Course existantes seront toutes associées au département « Temp » après l’exécution de la méthode `Up`.

* Ouvrez le fichier *{timestamp}_ComplexDataModel.cs*.

* Commentez la ligne de code qui ajoute la colonne DepartmentID à la table Course.

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

* Ajoutez le code en surbrillance suivant après le code qui crée la table Department :

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

Dans une application de production, vous devez écrire un code ou des scripts pour ajouter des lignes Department et associer des lignes Course aux nouvelles lignes Department. Vous n’avez alors plus besoin du département « Temp » ni de la valeur par défaut sur la colonne Course.DepartmentID.

Enregistrez vos modifications et générez le projet.

## <a name="change-the-connection-string"></a>Changer la chaîne de connexion

Vous avez maintenant un nouveau code dans la classe `DbInitializer` qui ajoute des données initiales pour les nouvelles entités à une base de données vide. Pour rendre EF créer une nouvelle base de données vide, modifiez le nom de la base de données dans la chaîne de connexion dans *appsettings.json* par contosouniversity3 ou un autre nom que vous n’avez pas utilisé sur l’ordinateur que vous utilisez.

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=ContosoUniversity3;Trusted_Connection=True;MultipleActiveResultSets=true"
  },
```

Enregistrez vos modifications dans *appsettings.json* .

> [!NOTE]
> Comme alternative au changement de nom de la base de données, vous pouvez supprimer la base de données. Utilisez **SQL Server Object Explorer** (SSOX) ou la commande CLI `database drop` :
>
> ```dotnetcli
> dotnet ef database drop
> ```

## <a name="update-the-database"></a>Mettre à jour la base de données

Une fois que vous avez modifié le nom de la base de données ou supprimé la base de données, exécutez la commande `database update` dans la fenêtre de commande pour exécuter les migrations.

```dotnetcli
dotnet ef database update
```

Exécutez l’application pour que la méthode `DbInitializer.Initialize` exécute la nouvelle base de données et la remplisse.

Ouvrez la base de données dans SSOX comme vous l’avez fait précédemment, puis développez le nœud **Tables** pour voir que toutes les tables ont été créées. (Si SSOX est resté ouvert, cliquez sur le bouton **Actualiser**.)

![Tables dans SSOX](complex-data-model/_static/ssox-tables.png)

Exécutez l’application pour déclencher le code d’initialiseur qui remplit la base de données.

Cliquez avec le bouton droit sur la table **CourseAssignment** et sélectionnez **Afficher les données** pour vérifier qu’elle comporte des données.

![Données CourseAssignment dans SSOX](complex-data-model/_static/ssox-ci-data.png)

## <a name="get-the-code"></a>Obtenir le code

[Télécharger ou afficher l’application complète.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Personnaliser le modèle de données
> * Apporter des modifications à l’entité Student
> * Créer une entité Instructor
> * Créer une entité OfficeAssignment
> * Modifier l’entité Course
> * Créer l’entité Department
> * Modifier l’entité Enrollment
> * Mettre à jour le contexte de base de données
> * Remplir la base de données avec des données de test
> * Ajouter une migration
> * Changer la chaîne de connexion
> * Base de données mise à jour

Passez au tutoriel suivant pour en savoir plus sur l’accès aux données associées.

> [!div class="nextstepaction"]
> [Suivant : accéder aux données associées](read-related-data.md)
