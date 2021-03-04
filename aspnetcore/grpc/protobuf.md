---
title: Créer des messages Protobuf pour les applications .NET
author: jamesnk
description: Découvrez comment créer des messages Protobuf pour les applications .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 02/12/2021
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
uid: grpc/protobuf
ms.openlocfilehash: 65a84c34e182b7a330d14e1f7ace17790ee4ed47
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102110168"
---
# <a name="create-protobuf-messages-for-net-apps"></a>Créer des messages Protobuf pour les applications .NET

Par [James Newton-King](https://twitter.com/jamesnk) et [Mark Rendle](https://twitter.com/markrendle)

gRPC utilise [Protobuf](https://developers.google.com/protocol-buffers) comme langage IDL (Interface Definition Language). Protobuf IDL est un format indépendant de la langue permettant de spécifier les messages envoyés et reçus par les services gRPC. Les messages Protobuf sont définis dans des `.proto` fichiers. Ce document explique comment les concepts Protobuf sont mappés à .NET.

## <a name="protobuf-messages"></a>Messages Protobuf

Les messages sont l’objet principal de transfert de données dans Protobuf. Elles sont conceptuellement similaires aux classes .NET.

```protobuf
syntax = "proto3";

option csharp_namespace = "Contoso.Messages";

message Person {
    int32 id = 1;
    string first_name = 2;
    string last_name = 3;
}  
```

La définition du message précédent spécifie trois champs en tant que paires nom-valeur. Comme les propriétés sur les types .NET, chaque champ a un nom et un type. Le type de champ peut être un [type valeur scalaire Protobuf](#scalar-value-types), par exemple `int32` , ou un autre message.

Le [Guide de style Protobuf](https://developers.google.com/protocol-buffers/docs/style) recommande l’utilisation `underscore_separated_names` de pour les noms de champs. Les nouveaux messages Protobuf créés pour les applications .NET doivent suivre les instructions de style Protobuf. Les outils .NET génèrent automatiquement des types .NET qui utilisent les normes d’affectation de noms .NET. Par exemple, un `first_name` champ Protobuf génère une `FirstName` propriété .net.

En plus d’un nom, chaque champ de la définition de message a un numéro unique. Les numéros de champ sont utilisés pour identifier les champs lorsque le message est sérialisé vers Protobuf. La sérialisation d’un petit nombre est plus rapide que la sérialisation de l’intégralité du nom de champ. Comme les numéros de champ identifient un champ, il est important de prendre soin de les modifier. Pour plus d’informations sur la modification des messages Protobuf <xref:grpc/versioning> , consultez.

Quand une application est générée, les outils Protobuf génèrent des types .NET à partir de `.proto` fichiers. Le `Person` message génère une classe .net :

```csharp
public class Person
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

Pour plus d’informations sur les messages Protobuf, consultez le [Guide du langage Protobuf](https://developers.google.com/protocol-buffers/docs/proto3#simple).

## <a name="scalar-value-types"></a>Types valeur scalaires

Protobuf prend en charge une plage de types valeur scalaire native. Le tableau suivant les répertorie avec leur type C# équivalent :

| Type Protobuf | Type C#      |
| ------------- | ------------ |
| `double`      | `double`     |
| `float`       | `float`      |
| `int32`       | `int`        |
| `int64`       | `long`       |
| `uint32`      | `uint`       |
| `uint64`      | `ulong`      |
| `sint32`      | `int`        |
| `sint64`      | `long`       |
| `fixed32`     | `uint`       |
| `fixed64`     | `ulong`      |
| `sfixed32`    | `int`        |
| `sfixed64`    | `long`       |
| `bool`        | `bool`       |
| `string`      | `string`     |
| `bytes`       | `ByteString` |

Les valeurs scalaires ont toujours une valeur par défaut et ne peuvent pas être définies sur `null` . Cette contrainte comprend `string` et `ByteString` qui sont des classes C#. `string` la valeur par défaut est une valeur de chaîne vide et sa `ByteString` valeur par défaut est un octet vide. Toute tentative de leur définition pour `null` générer une erreur.

Les [types Wrapper Nullable](#nullable-types) peuvent être utilisés pour prendre en charge les valeurs NULL.

### <a name="dates-and-times"></a>Dates et heures

Les types scalaires natifs ne fournissent pas de valeurs de date et d’heure équivalentes à. Les, <xref:System.DateTimeOffset> <xref:System.DateTime> et <xref:System.TimeSpan> . Ces types peuvent être spécifiés à l’aide d’extensions de *types connus* de Protobuf. Ces extensions fournissent la génération de code et la prise en charge du runtime pour les types de champs complexes sur les plateformes prises en charge.

Le tableau suivant indique les types de date et d’heure :

| Type .NET        | Type de Well-Known Protobuf    |
| ---------------- | --------------------------- |
| `DateTimeOffset` | `google.protobuf.Timestamp` |
| `DateTime`       | `google.protobuf.Timestamp` |
| `TimeSpan`       | `google.protobuf.Duration`  |

```protobuf  
syntax = "proto3"

import "google/protobuf/duration.proto";  
import "google/protobuf/timestamp.proto";

message Meeting {
    string subject = 1;
    google.protobuf.Timestamp start = 2;
    google.protobuf.Duration duration = 3;
}  
```

Les propriétés générées dans la classe C# ne sont pas les types de date et d’heure .NET. Les propriétés utilisent les `Timestamp` `Duration` classes et dans l' `Google.Protobuf.WellKnownTypes` espace de noms. Ces classes fournissent des méthodes pour la conversion vers et à partir de `DateTimeOffset` , `DateTime` et `TimeSpan` .

```csharp
// Create Timestamp and Duration from .NET DateTimeOffset and TimeSpan.
var meeting = new Meeting
{
    Time = Timestamp.FromDateTimeOffset(meetingTime), // also FromDateTime()
    Duration = Duration.FromTimeSpan(meetingLength)
};

// Convert Timestamp and Duration to .NET DateTimeOffset and TimeSpan.
var time = meeting.Time.ToDateTimeOffset();
var duration = meeting.Duration?.ToTimeSpan();
```

> [!NOTE]
> Le `Timestamp` type fonctionne avec des heures UTC. `DateTimeOffset` les valeurs ont toujours un offset égal à zéro, et la `DateTime.Kind` propriété est toujours `DateTimeKind.Utc` .

### <a name="nullable-types"></a>Types Nullable

La génération de code Protobuf pour C# utilise les types natifs, tels que `int` pour `int32` . Par conséquent, les valeurs sont toujours incluses et ne peuvent pas être `null` .

Pour les valeurs qui requièrent explicite `null` , telles que l’utilisation `int?` de dans le code c#, les types de Well-Known de Protobuf incluent des wrappers qui sont compilés en types C# Nullable. Pour les utiliser, importez `wrappers.proto` dans votre `.proto` fichier, comme le code suivant :

```protobuf  
syntax = "proto3"

import "google/protobuf/wrappers.proto"

message Person {
    // ...
    google.protobuf.Int32Value age = 5;
}
```

`wrappers.proto` les types ne sont pas exposés dans les propriétés générées. Protobuf les mappe automatiquement aux types Nullable .NET appropriés dans les messages C#. Par exemple, un `google.protobuf.Int32Value` champ génère une `int?` propriété. Les propriétés de type référence comme `string` et `ByteString` sont inchangées, sauf qu' `null` elles peuvent être assignées sans erreur.

Le tableau suivant présente la liste complète des types de wrappers avec leur type C# équivalent :

| Type C#      | Wrapper de type Well-Known       |
| ------------ | ----------------------------- |
| `bool?`      | `google.protobuf.BoolValue`   |
| `double?`    | `google.protobuf.DoubleValue` |
| `float?`     | `google.protobuf.FloatValue`  |
| `int?`       | `google.protobuf.Int32Value`  |
| `long?`      | `google.protobuf.Int64Value`  |
| `uint?`      | `google.protobuf.UInt32Value` |
| `ulong?`     | `google.protobuf.UInt64Value` |
| `string`     | `google.protobuf.StringValue` |
| `ByteString` | `google.protobuf.BytesValue`  |

### <a name="bytes"></a>Octets

Les charges utiles binaires sont prises en charge dans Protobuf avec le `bytes` type de valeur scalaire. Une propriété générée en C# utilise `ByteString` comme type de propriété.

Utilisez `ByteString.CopyFrom(byte[] data)` pour créer une nouvelle instance à partir d’un tableau d’octets :

```csharp
var data = await File.ReadAllBytesAsync(path);

var payload = new PayloadResponse();
payload.Data = ByteString.CopyFrom(data);
```

`ByteString` l’accès aux données s’effectue directement à l’aide `ByteString.Span` de ou de `ByteString.Memory` . Ou appelez `ByteString.ToByteArray()` pour convertir une instance en un tableau d’octets :

```csharp
var payload = await client.GetPayload(new PayloadRequest());

await File.WriteAllBytesAsync(path, payload.Data.ToByteArray());
```

### <a name="decimals"></a>Décimales

Protobuf ne prend pas en charge en mode natif le `decimal` type .net, juste `double` et `float` . Il existe une discussion continue dans le projet Protobuf sur la possibilité d’ajouter un type décimal standard aux types de Well-Known, avec la prise en charge de la plateforme pour les langages et les infrastructures qui le prennent en charge. Rien n’a encore été implémenté.

Il est possible de créer une définition de message pour représenter le `decimal` type qui fonctionne pour la sérialisation sécurisée entre les clients et les serveurs .net. Toutefois, les développeurs sur d’autres plateformes doivent comprendre le format utilisé et implémenter leur propre gestion.

#### <a name="creating-a-custom-decimal-type-for-protobuf"></a>Création d’un type décimal personnalisé pour Protobuf

```protobuf
package CustomTypes;

// Example: 12345.6789 -> { units = 12345, nanos = 678900000 }
message DecimalValue {

    // Whole units part of the amount
    int64 units = 1;

    // Nano units of the amount (10^-9)
    // Must be same sign as units
    sfixed32 nanos = 2;
}
```

Le `nanos` champ représente les valeurs de `0.999_999_999` à `-0.999_999_999` . Par exemple, la `decimal` valeur est `1.5m` représentée sous la forme `{ units = 1, nanos = 500_000_000 }` . C’est la raison pour laquelle le `nanos` champ de cet exemple utilise le `sfixed32` type, qui s’encode plus efficacement que `int32` pour les valeurs plus élevées. Si le `units` champ est négatif, le `nanos` champ doit également être négatif.

> [!NOTE]
> Il existe plusieurs autres algorithmes pour l’encodage de `decimal` valeurs en tant que chaînes d’octets, mais ce message est plus facile à comprendre que l’un d’entre eux. Les valeurs ne sont pas affectées par Big-endian ou Little-endian sur différentes plateformes.

La conversion entre ce type et le `decimal` type BCL peut être implémentée en C# comme suit :

```csharp
namespace CustomTypes
{
    public partial class DecimalValue
    {
        private const decimal NanoFactor = 1_000_000_000;
        public DecimalValue(long units, int nanos)
        {
            Units = units;
            Nanos = nanos;
        }

        public long Units { get; }
        public int Nanos { get; }

        public static implicit operator decimal(CustomTypes.DecimalValue grpcDecimal)
        {
            return grpcDecimal.Units + grpcDecimal.Nanos / NanoFactor;
        }

        public static implicit operator CustomTypes.DecimalValue(decimal value)
        {
            var units = decimal.ToInt64(value);
            var nanos = decimal.ToInt32((value - units) * NanoFactor);
            return new CustomTypes.DecimalValue(units, nanos);
        }
    }
}
```

## <a name="collections"></a>Collections

### <a name="lists"></a>Listes

Les listes dans Protobuf sont spécifiées à l’aide du `repeated` mot clé prefix sur un champ. L’exemple suivant montre comment créer une liste :

```protobuf
message Person {
    // ...
    repeated string roles = 8;
}
```

Dans le code généré, `repeated` les champs sont représentés par le `Google.Protobuf.Collections.RepeatedField<T>` type générique.

```csharp
public class Person
{
    // ...
    public RepeatedField<string> Roles { get; }
}
```

L'objet `RepeatedField<T>` implémente l'objet <xref:System.Collections.Generic.IList%601>. Vous pouvez donc utiliser des requêtes LINQ ou la convertir en un tableau ou une liste. `RepeatedField<T>` les propriétés n’ont pas d’accesseur Set public. Les éléments doivent être ajoutés à la collection existante.

```csharp
var person = new Person();

// Add one item.
person.Roles.Add("user");

// Add all items from another collection.
var roles = new [] { "admin", "manager" };
person.Roles.Add(roles);
```

### <a name="dictionaries"></a>Dictionnaires

Le <xref:System.Collections.Generic.IDictionary%602> type .net est représenté dans Protobuf à l’aide de `map<key_type, value_type>` .

```protobuf
message Person {
    // ...
    map<string, string> attributes = 9;
}
```

Dans le code .NET généré, `map` les champs sont représentés par le `Google.Protobuf.Collections.MapField<TKey, TValue>` type générique. L'objet `MapField<TKey, TValue>` implémente l'objet <xref:System.Collections.Generic.IDictionary%602>. Comme les `repeated` Propriétés, les `map` Propriétés n’ont pas d’accesseur Set public. Les éléments doivent être ajoutés à la collection existante.

```csharp
var person = new Person();

// Add one item.
person.Attributes["created_by"] = "James";

// Add all items from another collection.
var attributes = new Dictionary<string, string>
{
    ["last_modified"] = DateTime.UtcNow.ToString()
};
person.Attributes.Add(attributes);
```

## <a name="unstructured-and-conditional-messages"></a>Messages non structurés et conditionnels

Protobuf est un format de messagerie contrat en premier. Les messages d’une application, y compris ses champs et types, doivent être spécifiés dans `.proto` les fichiers lors de la génération de l’application. La conception « contrat en premier » de Protobuf est très utile pour appliquer le contenu des messages, mais peut limiter les scénarios où un contrat strict n’est pas obligatoire :

* Messages avec des charges utiles inconnues. Par exemple, un message avec un champ qui peut contenir n’importe quel message.
* Messages conditionnels. Par exemple, un message retourné par un service gRPC peut être un résultat de réussite ou un résultat d’erreur.
* Valeurs dynamiques. Par exemple, un message avec un champ qui contient une collection non structurée de valeurs, similaire à JSON.

Protobuf offre des fonctionnalités et des types de langage pour prendre en charge ces scénarios.

### <a name="any"></a>Quelconque

Le `Any` type vous permet d’utiliser des messages en tant que types incorporés sans avoir leur `.proto` définition. Pour utiliser le `Any` type, importez `any.proto` .

```protobuf
import "google/protobuf/any.proto";

message Status {
    string message = 1;
    google.protobuf.Any detail = 2;
}
```

```csharp
// Create a status with a Person message set to detail.
var status = new ErrorStatus();
status.Detail = Any.Pack(new Person { FirstName = "James" });

// Read Person message from detail.
if (status.Detail.Is(Person.Descriptor))
{
    var person = status.Detail.Unpack<Person>();
    // ...
}
```

### <a name="oneof"></a>Oneof

`oneof` les champs sont une fonctionnalité de langage. Le compilateur gère le `oneof` mot clé lorsqu’il génère la classe de message. L’utilisation `oneof` de pour spécifier un message de réponse qui peut retourner `Person` ou `Error` peut se présenter comme suit :

```protobuf
message Person {
    // ...
}

message Error {
    // ...
}

message ResponseMessage {
  oneof result {
    Error error = 1;
    Person person = 2;
  }
}
```

Les champs de l' `oneof` ensemble doivent avoir des numéros de champ uniques dans la déclaration globale du message.

Lors de l’utilisation de `oneof` , le code C# généré inclut une énumération qui spécifie les champs qui ont été définis. Vous pouvez tester l’énumération pour rechercher le champ défini. Les champs qui ne sont pas définis retournent `null` ou la valeur par défaut, plutôt que de lever une exception.

```csharp
var response = await client.GetPersonAsync(new RequestMessage());

switch (response.ResultCase)
{
    case ResponseMessage.ResultOneofCase.Person:
        HandlePerson(response.Person);
        break;
    case ResponseMessage.ResultOneofCase.Error:
        HandleError(response.Error);
        break;
    default:
        throw new ArgumentException("Unexpected result.");
}
```

### <a name="value"></a>Valeur

Le `Value` type représente une valeur typée dynamiquement. Il peut s’agir d' `null` un nombre, d’une chaîne, d’une valeur booléenne, d’un dictionnaire de valeurs ( `Struct` ) ou d’une liste de valeurs ( `ValueList` ). `Value` est un type de Well-Known Protobuf qui utilise la fonctionnalité décrite précédemment `oneof` . Pour utiliser le `Value` type, importez `struct.proto` .

```protobuf
import "google/protobuf/struct.proto";

message Status {
    // ...
    google.protobuf.Value data = 3;
}
```

```csharp
// Create dynamic values.
var status = new Status();
status.Data = Value.FromStruct(new Struct
{
    Fields =
    {
        ["enabled"] = Value.ForBoolean(true),
        ["metadata"] = Value.ForList(
            Value.FromString("value1"),
            Value.FromString("value2"))
    }
});

// Read dynamic values.
switch (status.Data.KindCase)
{
    case Value.KindOneofCase.StructValue:
        foreach (var field in status.Data.StructValue.Fields)
        {
            // Read struct fields...
        }
        break;
    // ...
}
```

L’utilisation de `Value` direct peut être détaillée. Une autre façon d’utiliser `Value` est la prise en charge intégrée de Protobuf pour le mappage des messages à JSON. `JsonFormatter`Les types de Protobuf et `JsonWriter` peuvent être utilisés avec n’importe quel message Protobuf. `Value` est particulièrement bien adapté à la conversion vers et à partir de JSON.

Il s’agit de l’équivalent JSON du code précédent :

```csharp
// Create dynamic values from JSON.
var status = new Status();
status.Data = Value.Parser.ParseJson(@"{
    ""enabled"": true,
    ""metadata"": [ ""value1"", ""value2"" ]
}");

// Convert dynamic values to JSON.
// JSON can be read with a library like System.Text.Json or Newtonsoft.Json
var json = JsonFormatter.Default.Format(status.Data);
var document = JsonDocument.Parse(json);
```

## <a name="additional-resources"></a>Ressources supplémentaires

* [Guide du langage Protobuf](https://developers.google.com/protocol-buffers/docs/proto3#simple)
* <xref:grpc/versioning>
