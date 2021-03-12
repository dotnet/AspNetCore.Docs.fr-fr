---
title: Liaison de données personnalisée dans ASP.NET Core
author: ardalis
description: Découvrez comment la liaison de données permet aux actions du contrôleur de fonctionner directement avec des types de modèle dans ASP.NET Core.
ms.author: riande
ms.date: 01/06/2020
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
uid: mvc/advanced/custom-model-binding
ms.openlocfilehash: b674d3a034124a26e2e76f2a7f0220e3f0ecea56
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588768"
---
# <a name="custom-model-binding-in-aspnet-core"></a>Liaison de données personnalisée dans ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Par [Steve Smith](https://ardalis.com/) et [Kirk Larkin](https://twitter.com/serpent5)

La liaison de données permet aux actions du contrôleur de fonctionner directement avec des types de modèle (passés en tant qu’arguments de méthode), plutôt qu’avec des requêtes HTTP. Le mappage entre les données de requête entrantes et les modèles d’application est pris en charge par les classeurs de modèles. Les développeurs peuvent étendre la fonctionnalité de liaison de données intégrée en implémentant des classeurs de modèles personnalisés (même si, en règle générale, vous n’avez pas besoin d’écrire votre propre fournisseur).

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/custom-model-binding/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="default-model-binder-limitations"></a>Limitations du classeur de modèles par défaut

Les classeurs de modèles par défaut prennent en charge la plupart des types de données .NET Core usuels et doivent répondre aux besoins de la majorité des développeurs. Ils sont censés lier directement les entrées textuelles de la requête aux types de modèle. Vous devrez peut-être transformer l’entrée avant de la lier. Par exemple, quand vous avez une clé qui peut être utilisée pour rechercher des données de modèle. Vous pouvez utiliser un classeur de modèles personnalisé pour récupérer (fetch) les données en fonction de la clé.

## <a name="model-binding-review"></a>Vérification de la liaison de données

La liaison de données utilise des définitions spécifiques pour les types sur lesquels elle opère. Un *type simple* est converti à partir d’une seule chaîne dans l’entrée. Un *type complexe* est converti à partir de plusieurs valeurs d’entrée. Le framework détermine la différence en fonction de l’existence de `TypeConverter`. Nous vous recommandons de créer un convertisseur de type si vous disposez d’un mappage `string` -> `SomeType` simple qui ne nécessite pas de ressources externes.

Avant de créer votre propre classeur de modèles personnalisé, vérifiez la façon dont les classeurs de modèles existants sont implémentés. Considérez le <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder> qui peut être utilisé pour convertir des chaînes encodées en base64 en tableaux d’octets. Les tableaux d’octets sont souvent stockés sous forme de fichiers ou de champs BLOB de base de données.

### <a name="working-with-the-bytearraymodelbinder"></a>Utilisation de ByteArrayModelBinder

Les chaînes encodées au format Base64 peuvent être utilisées pour représenter des données binaires. Par exemple, une image peut être encodée sous forme de chaîne. L’exemple comprend une image sous forme de chaîne encodée en Base64 dans [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/advanced/custom-model-binding/samples/3.x/CustomModelBindingSample/Base64String.txt).

ASP.NET Core MVC peut accepter une chaîne encodée au format base64 et utiliser `ByteArrayModelBinder` pour la convertir en tableau d’octets. Les <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> `byte[]` arguments Maps pour `ByteArrayModelBinder` :

```csharp
public IModelBinder GetBinder(ModelBinderProviderContext context)
{
    if (context == null)
    {
        throw new ArgumentNullException(nameof(context));
    }

    if (context.Metadata.ModelType == typeof(byte[]))
    {
        var loggerFactory = context.Services.GetRequiredService<ILoggerFactory>();
        return new ByteArrayModelBinder(loggerFactory);
    }

    return null;
}
```

Quand vous créez votre propre classeur de modèles personnalisé, vous pouvez implémenter votre propre `IModelBinderProvider` type ou utiliser le <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> .

L’exemple suivant montre comment utiliser `ByteArrayModelBinder` pour convertir une chaîne encodée au format base64 en `byte[]`, et comment enregistrer le résultat dans un fichier :

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/ImageController.cs?name=snippet_Post)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Vous pouvez envoyer (POST) une chaîne encodée au format base64 à cette méthode d’API à l’aide d’un outil tel que [Postman](https://www.getpostman.com/) :

![Postman](custom-model-binding/images/postman.png "Postman")

Tant que le classeur peut lier les données de requête à des propriétés ou des arguments nommés de manière appropriée, la liaison de données s’effectue correctement. L’exemple suivant montre comment utiliser `ByteArrayModelBinder` avec un modèle de vue :

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/ImageController.cs?name=snippet_SaveProfile&highlight=2)]

## <a name="custom-model-binder-sample"></a>Exemple de classeur de modèles personnalisé

Dans cette section, nous allons implémenter un classeur de modèles personnalisé qui :

- Convertit les données de requête entrantes en arguments clés fortement typés.
- Utilise Entity Framework Core pour récupérer (fetch) l’entité associée.
- Passe l’entité associée en tant qu’argument à la méthode d’action.

L’exemple suivant utilise l’attribut `ModelBinder` pour le modèle `Author` :

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Data/Author.cs?highlight=6)]

Dans le code précédent, l’attribut `ModelBinder` spécifie le type de `IModelBinder` à utiliser pour lier les paramètres d’action de `Author`.

La classe `AuthorEntityBinder` suivante est utilisée pour lier un paramètre `Author` en récupérant (fetch) l’entité à partir d’une source de données via Entity Framework Core et `authorId` :

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=snippet_Class)]

> [!NOTE]
> La classe `AuthorEntityBinder` précédente est destinée à illustrer un classeur de modèles personnalisé. La classe n’est pas destinée à illustrer les bonnes pratiques pour un scénario de recherche. Pour la recherche, liez `authorId` et interrogez la base de données dans une méthode d’action. Cette approche sépare les échecs de liaison des modèles des cas `NotFound`.

Le code suivant montre comment utiliser `AuthorEntityBinder` dans une méthode d’action :

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=snippet_Get&highlight=2)]

L’attribut `ModelBinder` permet d’appliquer `AuthorEntityBinder` aux paramètres qui n’utilisent pas les conventions par défaut :

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=snippet_GetById&highlight=2)]

Dans cet exemple, comme le nom de l’argument n’est pas le `authorId` par défaut, il est spécifié dans le paramètre à l’aide de l’attribut `ModelBinder`. Le contrôleur et la méthode d’action sont simplifiés par rapport à la recherche de l’entité dans la méthode d’action. La logique permettant de récupérer (fetch) l’auteur à l’aide d’Entity Framework Core est déplacée vers le classeur de modèles. Cela peut représenter une simplification considérable quand vous avez plusieurs méthodes qui sont liées au modèle `Author`.

Vous pouvez appliquer l’attribut `ModelBinder` à des propriétés de modèle individuelles (par exemple viewmodel) ou à des paramètres de méthode d’action afin de spécifier un classeur de modèles ou un nom de modèle particulier pour ce type ou cette action uniquement.

### <a name="implementing-a-modelbinderprovider"></a>Implémentation de ModelBinderProvider

Au lieu d’appliquer un attribut, vous pouvez implémenter `IModelBinderProvider`. C’est ainsi que les classeurs de framework intégrés sont implémentés. Quand vous spécifiez le type sur lequel votre classeur opère, vous spécifiez le type d’argument qu’il produit, et **non** l’entrée que votre classeur accepte. Le fournisseur de classeurs suivant fonctionne avec `AuthorEntityBinder`. Quand il est ajouté à la collection de fournisseurs de MVC, vous n’avez pas besoin d’utiliser l’attribut `ModelBinder` sur `Author` ou sur les paramètres typés de `Author`.

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> Remarque : Le code précédent retourne `BinderTypeModelBinder`. `BinderTypeModelBinder` sert de fabrique pour les classeurs de modèles et permet l’injection de dépendances. `AuthorEntityBinder` a besoin de l’injection de dépendances pour accéder à EF Core. Utilisez `BinderTypeModelBinder`, si votre classeur de modèles nécessite des services liés à l’injection de dépendances.

Pour utiliser un fournisseur de classeurs de modèles personnalisé, ajoutez-le à `ConfigureServices` :

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-8)]

Durant l’évaluation des classeurs de modèles, la collection de fournisseurs est examinée dans un certain ordre. Le premier fournisseur qui retourne un Binder qui correspond au modèle d’entrée est utilisé. L’ajout de votre fournisseur à la fin de la collection peut donc entraîner l’appel d’un classeur de modèles intégré avant que votre Binder personnalisé ait une chance. Dans cet exemple, le fournisseur personnalisé est ajouté au début de la collection pour garantir qu’il est toujours utilisé pour les `Author` arguments d’action.

### <a name="polymorphic-model-binding"></a>Liaison de modèle polymorphe

La liaison à différents modèles de types dérivés porte le nom de liaison de modèle polymorphe. La liaison de modèle personnalisé polymorphe est requise lorsque la valeur de la demande doit être liée au type de modèle dérivé spécifique. Liaison de modèle polymorphe :

* N’est pas typique d’une API REST conçue pour interagir avec toutes les langues.
* Rend difficile la raison des modèles liés.

Toutefois, si une application requiert une liaison de modèle polymorphe, une implémentation peut se présenter comme le code suivant :

[!code-csharp[](custom-model-binding/samples/3.x/PolymorphicModelBindingSample/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a>Recommandations et bonnes pratiques

Les classeurs de modèles personnalisés :

- Ne doivent pas tenter de définir des codes d’état ou de retourner des résultats (par exemple, 404 Introuvable). En cas d’échec de la liaison de données, un [filtre d’action](xref:mvc/controllers/filters) ou une logique située dans la méthode d’action elle-même doit prendre en charge l’erreur.
- Sont surtout utiles pour éliminer le code répétitif et les problèmes transversaux des méthodes d’action.
- Ne doivent pas être utilisés pour convertir une chaîne en type personnalisé. En règle générale, <xref:System.ComponentModel.TypeConverter> est une meilleure option.

::: moniker-end
::: moniker range="< aspnetcore-3.0"

Par [Steve Smith](https://ardalis.com/)

La liaison de données permet aux actions du contrôleur de fonctionner directement avec des types de modèle (passés en tant qu’arguments de méthode), plutôt qu’avec des requêtes HTTP. Le mappage entre les données de requête entrantes et les modèles d’application est pris en charge par les classeurs de modèles. Les développeurs peuvent étendre la fonctionnalité de liaison de données intégrée en implémentant des classeurs de modèles personnalisés (même si, en règle générale, vous n’avez pas besoin d’écrire votre propre fournisseur).

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/custom-model-binding/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="default-model-binder-limitations"></a>Limitations du classeur de modèles par défaut

Les classeurs de modèles par défaut prennent en charge la plupart des types de données .NET Core usuels et doivent répondre aux besoins de la majorité des développeurs. Ils sont censés lier directement les entrées textuelles de la requête aux types de modèle. Vous devrez peut-être transformer l’entrée avant de la lier. Par exemple, quand vous avez une clé qui peut être utilisée pour rechercher des données de modèle. Vous pouvez utiliser un classeur de modèles personnalisé pour récupérer (fetch) les données en fonction de la clé.

## <a name="model-binding-review"></a>Vérification de la liaison de données

La liaison de données utilise des définitions spécifiques pour les types sur lesquels elle opère. Un *type simple* est converti à partir d’une seule chaîne dans l’entrée. Un *type complexe* est converti à partir de plusieurs valeurs d’entrée. Le framework détermine la différence en fonction de l’existence de `TypeConverter`. Nous vous recommandons de créer un convertisseur de type si vous disposez d’un mappage `string` -> `SomeType` simple qui ne nécessite pas de ressources externes.

Avant de créer votre propre classeur de modèles personnalisé, vérifiez la façon dont les classeurs de modèles existants sont implémentés. Considérez le <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder> qui peut être utilisé pour convertir des chaînes encodées en base64 en tableaux d’octets. Les tableaux d’octets sont souvent stockés sous forme de fichiers ou de champs BLOB de base de données.

### <a name="working-with-the-bytearraymodelbinder"></a>Utilisation de ByteArrayModelBinder

Les chaînes encodées au format Base64 peuvent être utilisées pour représenter des données binaires. Par exemple, une image peut être encodée sous forme de chaîne. L’exemple comprend une image sous forme de chaîne encodée en Base64 dans [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/advanced/custom-model-binding/samples/2.x/CustomModelBindingSample/Base64String.txt).

ASP.NET Core MVC peut accepter une chaîne encodée au format base64 et utiliser `ByteArrayModelBinder` pour la convertir en tableau d’octets. Les <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> `byte[]` arguments Maps pour `ByteArrayModelBinder` :

```csharp
public IModelBinder GetBinder(ModelBinderProviderContext context)
{
    if (context == null)
    {
        throw new ArgumentNullException(nameof(context));
    }

    if (context.Metadata.ModelType == typeof(byte[]))
    {
        return new ByteArrayModelBinder();
    }

    return null;
}
```

Quand vous créez votre propre classeur de modèles personnalisé, vous pouvez implémenter votre propre `IModelBinderProvider` type ou utiliser le <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> .

L’exemple suivant montre comment utiliser `ByteArrayModelBinder` pour convertir une chaîne encodée au format base64 en `byte[]`, et comment enregistrer le résultat dans un fichier :

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/ImageController.cs?name=post1)]

Vous pouvez envoyer (POST) une chaîne encodée au format base64 à cette méthode d’API à l’aide d’un outil tel que [Postman](https://www.getpostman.com/) :

![Postman](custom-model-binding/images/postman.png "Postman")

Tant que le classeur peut lier les données de requête à des propriétés ou des arguments nommés de manière appropriée, la liaison de données s’effectue correctement. L’exemple suivant montre comment utiliser `ByteArrayModelBinder` avec un modèle de vue :

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/ImageController.cs?name=post2&highlight=2)]

## <a name="custom-model-binder-sample"></a>Exemple de classeur de modèles personnalisé

Dans cette section, nous allons implémenter un classeur de modèles personnalisé qui :

- Convertit les données de requête entrantes en arguments clés fortement typés.
- Utilise Entity Framework Core pour récupérer (fetch) l’entité associée.
- Passe l’entité associée en tant qu’argument à la méthode d’action.

L’exemple suivant utilise l’attribut `ModelBinder` pour le modèle `Author` :

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Data/Author.cs?highlight=6)]

Dans le code précédent, l’attribut `ModelBinder` spécifie le type de `IModelBinder` à utiliser pour lier les paramètres d’action de `Author`.

La classe `AuthorEntityBinder` suivante est utilisée pour lier un paramètre `Author` en récupérant (fetch) l’entité à partir d’une source de données via Entity Framework Core et `authorId` :

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=demo)]

> [!NOTE]
> La classe `AuthorEntityBinder` précédente est destinée à illustrer un classeur de modèles personnalisé. La classe n’est pas destinée à illustrer les bonnes pratiques pour un scénario de recherche. Pour la recherche, liez `authorId` et interrogez la base de données dans une méthode d’action. Cette approche sépare les échecs de liaison des modèles des cas `NotFound`.

Le code suivant montre comment utiliser `AuthorEntityBinder` dans une méthode d’action :

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo2&highlight=2)]

L’attribut `ModelBinder` permet d’appliquer `AuthorEntityBinder` aux paramètres qui n’utilisent pas les conventions par défaut :

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo1&highlight=2)]

Dans cet exemple, comme le nom de l’argument n’est pas le `authorId` par défaut, il est spécifié dans le paramètre à l’aide de l’attribut `ModelBinder`. Le contrôleur et la méthode d’action sont simplifiés par rapport à la recherche de l’entité dans la méthode d’action. La logique permettant de récupérer (fetch) l’auteur à l’aide d’Entity Framework Core est déplacée vers le classeur de modèles. Cela peut représenter une simplification considérable quand vous avez plusieurs méthodes qui sont liées au modèle `Author`.

Vous pouvez appliquer l’attribut `ModelBinder` à des propriétés de modèle individuelles (par exemple viewmodel) ou à des paramètres de méthode d’action afin de spécifier un classeur de modèles ou un nom de modèle particulier pour ce type ou cette action uniquement.

### <a name="implementing-a-modelbinderprovider"></a>Implémentation de ModelBinderProvider

Au lieu d’appliquer un attribut, vous pouvez implémenter `IModelBinderProvider`. C’est ainsi que les classeurs de framework intégrés sont implémentés. Quand vous spécifiez le type sur lequel votre classeur opère, vous spécifiez le type d’argument qu’il produit, et **non** l’entrée que votre classeur accepte. Le fournisseur de classeurs suivant fonctionne avec `AuthorEntityBinder`. Quand il est ajouté à la collection de fournisseurs de MVC, vous n’avez pas besoin d’utiliser l’attribut `ModelBinder` sur `Author` ou sur les paramètres typés de `Author`.

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> Remarque : Le code précédent retourne `BinderTypeModelBinder`. `BinderTypeModelBinder` sert de fabrique pour les classeurs de modèles et permet l’injection de dépendances. `AuthorEntityBinder` a besoin de l’injection de dépendances pour accéder à EF Core. Utilisez `BinderTypeModelBinder`, si votre classeur de modèles nécessite des services liés à l’injection de dépendances.

Pour utiliser un fournisseur de classeurs de modèles personnalisé, ajoutez-le à `ConfigureServices` :

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

Durant l’évaluation des classeurs de modèles, la collection de fournisseurs est examinée dans un certain ordre. Le premier fournisseur qui retourne un classeur est utilisé. L’ajout de votre fournisseur à la fin de la collection peut entraîner l’appel d’un classeur de modèles intégré avant votre classeur personnalisé. Dans cet exemple, le fournisseur personnalisé est ajouté au début de la collection afin qu’il soit utilisé pour les arguments d’action `Author`.

### <a name="polymorphic-model-binding"></a>Liaison de modèle polymorphe

La liaison à différents modèles de types dérivés porte le nom de liaison de modèle polymorphe. La liaison de modèle personnalisé polymorphe est requise lorsque la valeur de la demande doit être liée au type de modèle dérivé spécifique. Liaison de modèle polymorphe :

* N’est pas typique d’une API REST conçue pour interagir avec toutes les langues.
* Rend difficile la raison des modèles liés.

Toutefois, si une application requiert une liaison de modèle polymorphe, une implémentation peut se présenter comme le code suivant :

[!code-csharp[](custom-model-binding/samples/3.x/PolymorphicModelBindingSample/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a>Recommandations et bonnes pratiques

Les classeurs de modèles personnalisés :

- Ne doivent pas tenter de définir des codes d’état ou de retourner des résultats (par exemple, 404 Introuvable). En cas d’échec de la liaison de données, un [filtre d’action](xref:mvc/controllers/filters) ou une logique située dans la méthode d’action elle-même doit prendre en charge l’erreur.
- Sont surtout utiles pour éliminer le code répétitif et les problèmes transversaux des méthodes d’action.
- Ne doivent pas être utilisés pour convertir une chaîne en type personnalisé. En règle générale, <xref:System.ComponentModel.TypeConverter> est une meilleure option.

::: moniker-end
