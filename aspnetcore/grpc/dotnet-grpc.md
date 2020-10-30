---
title: Gérer les références Protobuf avec dotnet-GRPC
author: juntaoluo
description: En savoir plus sur l’ajout, la mise à jour, la suppression et la liste de références Protobuf avec l’outil Global dotnet-GRPC.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
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
uid: grpc/dotnet-grpc
ms.openlocfilehash: f34e1543d9695e138a85db3b79e013cf5fb6d138
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059908"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a>Gérer les références Protobuf avec dotnet-GRPC

Par [John Luo](https://github.com/juntaoluo)

`dotnet-grpc` est un outil Global .NET Core pour la gestion des références [Protobuf ( *. proto* )](xref:grpc/basics#proto-file) dans un projet .net gRPC. L’outil peut être utilisé pour ajouter, actualiser, supprimer et répertorier des références Protobuf.

## <a name="installation"></a>Installation

Pour installer l' `dotnet-grpc` [outil Global .net Core](/dotnet/core/tools/global-tools), exécutez la commande suivante :

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a>Ajouter des références

`dotnet-grpc` peut être utilisé pour ajouter des références Protobuf en tant qu' `<Protobuf />` éléments au fichier *. csproj* :

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

Les références Protobuf sont utilisées pour générer les ressources du client et/ou du serveur C#. L' `dotnet-grpc` outil peut :

* Créez une référence Protobuf à partir de fichiers locaux sur le disque.
* Crée une référence Protobuf à partir d’un fichier distant spécifié par une URL.
* Vérifiez que les dépendances de package gRPC correctes sont ajoutées au projet.

Par exemple, le `Grpc.AspNetCore` package est ajouté à une application Web. `Grpc.AspNetCore` contient les bibliothèques clientes et de serveur gRPC et la prise en charge des outils. Les `Grpc.Net.Client` `Grpc.Tools` packages, et `Google.Protobuf` , qui contiennent uniquement les bibliothèques clientes gRPC et la prise en charge des outils, sont également ajoutés à une application console.

### <a name="add-file"></a>Ajouter un fichier

La `add-file` commande est utilisée pour ajouter des fichiers locaux sur le disque en tant que références Protobuf. Chemins d’accès aux fichiers fournis :

* Peut être relatif au répertoire actif ou aux chemins d’accès absolus.
* Peut contenir des caractères génériques pour les [globbing](https://wikipedia.org/wiki/Glob_(programming))de fichiers basés sur des modèles.

Si des fichiers se trouvent en dehors du répertoire du projet, un `Link` élément est ajouté pour afficher le fichier sous le dossier `Protos` dans Visual Studio.

### <a name="usage"></a>Utilisation

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a>Arguments

| Argument | Description |
|-|-|
| files | Références du fichier protobuf. Il peut s’agir d’un chemin d’accès à glob pour les fichiers protobuf locaux. |

#### <a name="options"></a>Options

| Option Short | Option longue | Description |
|-|-|-|
| -p | --projet | Chemin d’accès au fichier projet sur lequel effectuer l’opération. Si aucun fichier n’est spécifié, la commande effectue une recherche dans le répertoire actif.
| -S | --services | Type des services gRPC qui doivent être générés. Si `Default` est spécifié, `Both` est utilisé pour les projets Web et `Client` est utilisé pour les projets non Web. Les valeurs acceptées sont `Both` , `Client` ,, `Default` `None` , `Server` .
| -i | --Import-dirs | Répertoires supplémentaires à utiliser lors de la résolution des importations des fichiers protobuf. Il s’agit d’une liste de chemins séparés par des points-virgules.
| | --accès | Modificateur d’accès à utiliser pour les classes C# générées. La valeur par défaut est `Public`. Les valeurs acceptées sont `Internal` et `Public`.

### <a name="add-url"></a>Ajouter une URL

La `add-url` commande permet d’ajouter un fichier distant spécifié par une URL source en tant que référence Protobuf. Un chemin d’accès de fichier doit être fourni pour spécifier l’emplacement de téléchargement du fichier distant. Le chemin d’accès du fichier peut être relatif au répertoire actif ou à un chemin d’accès absolu. Si le chemin d’accès au fichier se trouve en dehors du répertoire du projet, un `Link` élément est ajouté pour afficher le fichier sous le dossier virtuel `Protos` dans Visual Studio.

### <a name="usage"></a>Utilisation

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a>Arguments

| Argument | Description |
|-|-|
| url | URL d’un fichier protobuf distant. |

#### <a name="options"></a>Options

| Option Short | Option longue | Description |
|-|-|-|
| -o | --output | Spécifie le chemin de téléchargement du fichier protobuf distant. C'est une option obligatoire.
| -p | --projet | Chemin d’accès au fichier projet sur lequel effectuer l’opération. Si aucun fichier n’est spécifié, la commande effectue une recherche dans le répertoire actif.
| -S | --services | Type des services gRPC qui doivent être générés. Si `Default` est spécifié, `Both` est utilisé pour les projets Web et `Client` est utilisé pour les projets non Web. Les valeurs acceptées sont `Both` , `Client` ,, `Default` `None` , `Server` .
| -i | --Import-dirs | Répertoires supplémentaires à utiliser lors de la résolution des importations des fichiers protobuf. Il s’agit d’une liste de chemins séparés par des points-virgules.
| | --accès | Modificateur d’accès à utiliser pour les classes C# générées. La valeur par défaut est `Public`. Les valeurs acceptées sont `Internal` et `Public`.

## <a name="remove"></a>Supprimer

La `remove` commande permet de supprimer les références Protobuf du fichier *. csproj* . La commande accepte les arguments de chemin d’accès et les URL source comme arguments. L’outil :

* Supprime uniquement la référence Protobuf.
* Ne supprime pas le fichier *. proto* , même s’il a été téléchargé à l’origine à partir d’une URL distante.

### <a name="usage"></a>Utilisation

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a>Arguments

| Argument | Description |
|-|-|
| references | URL ou chemins de fichier des références protobuf à supprimer. |

### <a name="options"></a>Options

| Option Short | Option longue | Description |
|-|-|-|
| -p | --projet | Chemin d’accès au fichier projet sur lequel effectuer l’opération. Si aucun fichier n’est spécifié, la commande effectue une recherche dans le répertoire actif.

## <a name="refresh"></a>Actualiser

La `refresh` commande est utilisée pour mettre à jour une référence distante avec le contenu le plus récent à partir de l’URL source. Le chemin d’accès au fichier de téléchargement et l’URL source peuvent être utilisés pour spécifier la référence à mettre à jour. Remarque :

* Les hachages du contenu du fichier sont comparés pour déterminer si le fichier local doit être mis à jour.
* Aucune information d’horodatage n’est comparée.

L’outil remplace toujours le fichier local par le fichier distant si une mise à jour est nécessaire.

### <a name="usage"></a>Utilisation

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a>Arguments

| Argument | Description |
|-|-|
| references | URL ou chemins d’accès aux références protobuf distantes qui doivent être mises à jour. Laissez cet argument vide pour actualiser toutes les références distantes. |

### <a name="options"></a>Options

| Option Short | Option longue | Description |
|-|-|-|
| -p | --projet | Chemin d’accès au fichier projet sur lequel effectuer l’opération. Si aucun fichier n’est spécifié, la commande effectue une recherche dans le répertoire actif.
| | --à sec-exécution | Génère une liste des fichiers qui seraient mis à jour sans télécharger de nouveau contenu.

## <a name="list"></a>List

La `list` commande est utilisée pour afficher toutes les références Protobuf dans le fichier projet. Si toutes les valeurs d’une colonne sont des valeurs par défaut, la colonne peut être omise.

### <a name="usage"></a>Utilisation

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a>Options

| Option Short | Option longue | Description |
|-|-|-|
| -p | --projet | Chemin d’accès au fichier projet sur lequel effectuer l’opération. Si aucun fichier n’est spécifié, la commande effectue une recherche dans le répertoire actif.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
