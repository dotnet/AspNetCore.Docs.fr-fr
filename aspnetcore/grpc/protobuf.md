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
# <a name="create-protobuf-messages-for-net-apps"></a><span data-ttu-id="7fd0c-103">Créer des messages Protobuf pour les applications .NET</span><span class="sxs-lookup"><span data-stu-id="7fd0c-103">Create Protobuf messages for .NET apps</span></span>

<span data-ttu-id="7fd0c-104">Par [James Newton-King](https://twitter.com/jamesnk) et [Mark Rendle](https://twitter.com/markrendle)</span><span class="sxs-lookup"><span data-stu-id="7fd0c-104">By [James Newton-King](https://twitter.com/jamesnk) and [Mark Rendle](https://twitter.com/markrendle)</span></span>

<span data-ttu-id="7fd0c-105">gRPC utilise [Protobuf](https://developers.google.com/protocol-buffers) comme langage IDL (Interface Definition Language).</span><span class="sxs-lookup"><span data-stu-id="7fd0c-105">gRPC uses [Protobuf](https://developers.google.com/protocol-buffers) as its Interface Definition Language (IDL).</span></span> <span data-ttu-id="7fd0c-106">Protobuf IDL est un format indépendant de la langue permettant de spécifier les messages envoyés et reçus par les services gRPC.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-106">Protobuf IDL is a language neutral format for specifying the messages sent and received by gRPC services.</span></span> <span data-ttu-id="7fd0c-107">Les messages Protobuf sont définis dans des `.proto` fichiers.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-107">Protobuf messages are defined in `.proto` files.</span></span> <span data-ttu-id="7fd0c-108">Ce document explique comment les concepts Protobuf sont mappés à .NET.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-108">This document explains how Protobuf concepts map to .NET.</span></span>

## <a name="protobuf-messages"></a><span data-ttu-id="7fd0c-109">Messages Protobuf</span><span class="sxs-lookup"><span data-stu-id="7fd0c-109">Protobuf messages</span></span>

<span data-ttu-id="7fd0c-110">Les messages sont l’objet principal de transfert de données dans Protobuf.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-110">Messages are the main data transfer object in Protobuf.</span></span> <span data-ttu-id="7fd0c-111">Elles sont conceptuellement similaires aux classes .NET.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-111">They are conceptually similar to .NET classes.</span></span>

```protobuf
syntax = "proto3";

option csharp_namespace = "Contoso.Messages";

message Person {
    int32 id = 1;
    string first_name = 2;
    string last_name = 3;
}  
```

<span data-ttu-id="7fd0c-112">La définition du message précédent spécifie trois champs en tant que paires nom-valeur.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-112">The preceding message definition specifies three fields as name-value pairs.</span></span> <span data-ttu-id="7fd0c-113">Comme les propriétés sur les types .NET, chaque champ a un nom et un type.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-113">Like properties on .NET types, each field has a name and a type.</span></span> <span data-ttu-id="7fd0c-114">Le type de champ peut être un [type valeur scalaire Protobuf](#scalar-value-types), par exemple `int32` , ou un autre message.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-114">The field type can be a [Protobuf scalar value type](#scalar-value-types), e.g. `int32`, or another message.</span></span>

<span data-ttu-id="7fd0c-115">Le [Guide de style Protobuf](https://developers.google.com/protocol-buffers/docs/style) recommande l’utilisation `underscore_separated_names` de pour les noms de champs.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-115">The [Protobuf style guide](https://developers.google.com/protocol-buffers/docs/style) recommends using `underscore_separated_names` for field names.</span></span> <span data-ttu-id="7fd0c-116">Les nouveaux messages Protobuf créés pour les applications .NET doivent suivre les instructions de style Protobuf.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-116">New Protobuf messages created for .NET apps should follow the Protobuf style guidelines.</span></span> <span data-ttu-id="7fd0c-117">Les outils .NET génèrent automatiquement des types .NET qui utilisent les normes d’affectation de noms .NET.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-117">.NET tooling automatically generates .NET types that use .NET naming standards.</span></span> <span data-ttu-id="7fd0c-118">Par exemple, un `first_name` champ Protobuf génère une `FirstName` propriété .net.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-118">For example, a `first_name` Protobuf field generates a `FirstName` .NET property.</span></span>

<span data-ttu-id="7fd0c-119">En plus d’un nom, chaque champ de la définition de message a un numéro unique.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-119">In addition to a name, each field in the message definition has a unique number.</span></span> <span data-ttu-id="7fd0c-120">Les numéros de champ sont utilisés pour identifier les champs lorsque le message est sérialisé vers Protobuf.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-120">Field numbers are used to identify fields when the message is serialized to Protobuf.</span></span> <span data-ttu-id="7fd0c-121">La sérialisation d’un petit nombre est plus rapide que la sérialisation de l’intégralité du nom de champ.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-121">Serializing a small number is faster than serializing the entire field name.</span></span> <span data-ttu-id="7fd0c-122">Comme les numéros de champ identifient un champ, il est important de prendre soin de les modifier.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-122">Because field numbers identify a field it is important to take care when changing them.</span></span> <span data-ttu-id="7fd0c-123">Pour plus d’informations sur la modification des messages Protobuf <xref:grpc/versioning> , consultez.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-123">For more information about changing Protobuf messages see <xref:grpc/versioning>.</span></span>

<span data-ttu-id="7fd0c-124">Quand une application est générée, les outils Protobuf génèrent des types .NET à partir de `.proto` fichiers.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-124">When an app is built the Protobuf tooling generates .NET types from `.proto` files.</span></span> <span data-ttu-id="7fd0c-125">Le `Person` message génère une classe .net :</span><span class="sxs-lookup"><span data-stu-id="7fd0c-125">The `Person` message generates a .NET class:</span></span>

```csharp
public class Person
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

<span data-ttu-id="7fd0c-126">Pour plus d’informations sur les messages Protobuf, consultez le [Guide du langage Protobuf](https://developers.google.com/protocol-buffers/docs/proto3#simple).</span><span class="sxs-lookup"><span data-stu-id="7fd0c-126">For more information about Protobuf messages see the [Protobuf language guide](https://developers.google.com/protocol-buffers/docs/proto3#simple).</span></span>

## <a name="scalar-value-types"></a><span data-ttu-id="7fd0c-127">Types valeur scalaires</span><span class="sxs-lookup"><span data-stu-id="7fd0c-127">Scalar Value Types</span></span>

<span data-ttu-id="7fd0c-128">Protobuf prend en charge une plage de types valeur scalaire native.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-128">Protobuf supports a range of native scalar value types.</span></span> <span data-ttu-id="7fd0c-129">Le tableau suivant les répertorie avec leur type C# équivalent :</span><span class="sxs-lookup"><span data-stu-id="7fd0c-129">The following table lists them all with their equivalent C# type:</span></span>

| <span data-ttu-id="7fd0c-130">Type Protobuf</span><span class="sxs-lookup"><span data-stu-id="7fd0c-130">Protobuf type</span></span> | <span data-ttu-id="7fd0c-131">Type C#</span><span class="sxs-lookup"><span data-stu-id="7fd0c-131">C# type</span></span>      |
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

<span data-ttu-id="7fd0c-132">Les valeurs scalaires ont toujours une valeur par défaut et ne peuvent pas être définies sur `null` .</span><span class="sxs-lookup"><span data-stu-id="7fd0c-132">Scalar values always have a default value and can't be set to `null`.</span></span> <span data-ttu-id="7fd0c-133">Cette contrainte comprend `string` et `ByteString` qui sont des classes C#.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-133">This constraint includes `string` and `ByteString` which are C# classes.</span></span> <span data-ttu-id="7fd0c-134">`string` la valeur par défaut est une valeur de chaîne vide et sa `ByteString` valeur par défaut est un octet vide.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-134">`string` defaults to an empty string value and `ByteString` defaults to an empty bytes value.</span></span> <span data-ttu-id="7fd0c-135">Toute tentative de leur définition pour `null` générer une erreur.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-135">Attempting to set them to `null` throws an error.</span></span>

<span data-ttu-id="7fd0c-136">Les [types Wrapper Nullable](#nullable-types) peuvent être utilisés pour prendre en charge les valeurs NULL.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-136">[Nullable wrapper types](#nullable-types) can be used to support null values.</span></span>

### <a name="dates-and-times"></a><span data-ttu-id="7fd0c-137">Dates et heures</span><span class="sxs-lookup"><span data-stu-id="7fd0c-137">Dates and times</span></span>

<span data-ttu-id="7fd0c-138">Les types scalaires natifs ne fournissent pas de valeurs de date et d’heure équivalentes à. Les, <xref:System.DateTimeOffset> <xref:System.DateTime> et <xref:System.TimeSpan> .</span><span class="sxs-lookup"><span data-stu-id="7fd0c-138">The native scalar types don't provide for date and time values, equivalent to .NET's <xref:System.DateTimeOffset>, <xref:System.DateTime>, and <xref:System.TimeSpan>.</span></span> <span data-ttu-id="7fd0c-139">Ces types peuvent être spécifiés à l’aide d’extensions de *types connus* de Protobuf.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-139">These types can be specified by using some of Protobuf's *Well-Known Types* extensions.</span></span> <span data-ttu-id="7fd0c-140">Ces extensions fournissent la génération de code et la prise en charge du runtime pour les types de champs complexes sur les plateformes prises en charge.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-140">These extensions provide code generation and runtime support for complex field types across the supported platforms.</span></span>

<span data-ttu-id="7fd0c-141">Le tableau suivant indique les types de date et d’heure :</span><span class="sxs-lookup"><span data-stu-id="7fd0c-141">The following table shows the date and time types:</span></span>

| <span data-ttu-id="7fd0c-142">Type .NET</span><span class="sxs-lookup"><span data-stu-id="7fd0c-142">.NET type</span></span>        | <span data-ttu-id="7fd0c-143">Type de Well-Known Protobuf</span><span class="sxs-lookup"><span data-stu-id="7fd0c-143">Protobuf Well-Known Type</span></span>    |
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

<span data-ttu-id="7fd0c-144">Les propriétés générées dans la classe C# ne sont pas les types de date et d’heure .NET.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-144">The generated properties in the C# class aren't the .NET date and time types.</span></span> <span data-ttu-id="7fd0c-145">Les propriétés utilisent les `Timestamp` `Duration` classes et dans l' `Google.Protobuf.WellKnownTypes` espace de noms.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-145">The properties use the `Timestamp` and `Duration` classes in the `Google.Protobuf.WellKnownTypes` namespace.</span></span> <span data-ttu-id="7fd0c-146">Ces classes fournissent des méthodes pour la conversion vers et à partir de `DateTimeOffset` , `DateTime` et `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="7fd0c-146">These classes provide methods for converting to and from `DateTimeOffset`, `DateTime`, and `TimeSpan`.</span></span>

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
> <span data-ttu-id="7fd0c-147">Le `Timestamp` type fonctionne avec des heures UTC.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-147">The `Timestamp` type works with UTC times.</span></span> <span data-ttu-id="7fd0c-148">`DateTimeOffset` les valeurs ont toujours un offset égal à zéro, et la `DateTime.Kind` propriété est toujours `DateTimeKind.Utc` .</span><span class="sxs-lookup"><span data-stu-id="7fd0c-148">`DateTimeOffset` values always have an offset of zero, and the `DateTime.Kind` property is always `DateTimeKind.Utc`.</span></span>

### <a name="nullable-types"></a><span data-ttu-id="7fd0c-149">Types Nullable</span><span class="sxs-lookup"><span data-stu-id="7fd0c-149">Nullable types</span></span>

<span data-ttu-id="7fd0c-150">La génération de code Protobuf pour C# utilise les types natifs, tels que `int` pour `int32` .</span><span class="sxs-lookup"><span data-stu-id="7fd0c-150">The Protobuf code generation for C# uses the native types, such as `int` for `int32`.</span></span> <span data-ttu-id="7fd0c-151">Par conséquent, les valeurs sont toujours incluses et ne peuvent pas être `null` .</span><span class="sxs-lookup"><span data-stu-id="7fd0c-151">So the values are always included and can't be `null`.</span></span>

<span data-ttu-id="7fd0c-152">Pour les valeurs qui requièrent explicite `null` , telles que l’utilisation `int?` de dans le code c#, les types de Well-Known de Protobuf incluent des wrappers qui sont compilés en types C# Nullable.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-152">For values that require explicit `null`, such as using `int?` in C# code, Protobuf's Well-Known Types include wrappers that are compiled to nullable C# types.</span></span> <span data-ttu-id="7fd0c-153">Pour les utiliser, importez `wrappers.proto` dans votre `.proto` fichier, comme le code suivant :</span><span class="sxs-lookup"><span data-stu-id="7fd0c-153">To use them, import `wrappers.proto` into your `.proto` file, like the following code:</span></span>

```protobuf  
syntax = "proto3"

import "google/protobuf/wrappers.proto"

message Person {
    // ...
    google.protobuf.Int32Value age = 5;
}
```

<span data-ttu-id="7fd0c-154">`wrappers.proto` les types ne sont pas exposés dans les propriétés générées.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-154">`wrappers.proto` types aren't exposed in generated properties.</span></span> <span data-ttu-id="7fd0c-155">Protobuf les mappe automatiquement aux types Nullable .NET appropriés dans les messages C#.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-155">Protobuf automatically maps them to appropriate .NET nullable types in C# messages.</span></span> <span data-ttu-id="7fd0c-156">Par exemple, un `google.protobuf.Int32Value` champ génère une `int?` propriété.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-156">For example, a `google.protobuf.Int32Value` field generates an `int?` property.</span></span> <span data-ttu-id="7fd0c-157">Les propriétés de type référence comme `string` et `ByteString` sont inchangées, sauf qu' `null` elles peuvent être assignées sans erreur.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-157">Reference type properties like `string` and `ByteString` are unchanged except `null` can be assigned to them without error.</span></span>

<span data-ttu-id="7fd0c-158">Le tableau suivant présente la liste complète des types de wrappers avec leur type C# équivalent :</span><span class="sxs-lookup"><span data-stu-id="7fd0c-158">The following table shows the complete list of wrapper types with their equivalent C# type:</span></span>

| <span data-ttu-id="7fd0c-159">Type C#</span><span class="sxs-lookup"><span data-stu-id="7fd0c-159">C# type</span></span>      | <span data-ttu-id="7fd0c-160">Wrapper de type Well-Known</span><span class="sxs-lookup"><span data-stu-id="7fd0c-160">Well-Known Type wrapper</span></span>       |
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

### <a name="bytes"></a><span data-ttu-id="7fd0c-161">Octets</span><span class="sxs-lookup"><span data-stu-id="7fd0c-161">Bytes</span></span>

<span data-ttu-id="7fd0c-162">Les charges utiles binaires sont prises en charge dans Protobuf avec le `bytes` type de valeur scalaire.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-162">Binary payloads are supported in Protobuf with the `bytes` scalar value type.</span></span> <span data-ttu-id="7fd0c-163">Une propriété générée en C# utilise `ByteString` comme type de propriété.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-163">A generated property in C# uses `ByteString` as the property type.</span></span>

<span data-ttu-id="7fd0c-164">Utilisez `ByteString.CopyFrom(byte[] data)` pour créer une nouvelle instance à partir d’un tableau d’octets :</span><span class="sxs-lookup"><span data-stu-id="7fd0c-164">Use `ByteString.CopyFrom(byte[] data)` to create a new instance from a byte array:</span></span>

```csharp
var data = await File.ReadAllBytesAsync(path);

var payload = new PayloadResponse();
payload.Data = ByteString.CopyFrom(data);
```

<span data-ttu-id="7fd0c-165">`ByteString` l’accès aux données s’effectue directement à l’aide `ByteString.Span` de ou de `ByteString.Memory` .</span><span class="sxs-lookup"><span data-stu-id="7fd0c-165">`ByteString` data is accessed directly using `ByteString.Span` or `ByteString.Memory`.</span></span> <span data-ttu-id="7fd0c-166">Ou appelez `ByteString.ToByteArray()` pour convertir une instance en un tableau d’octets :</span><span class="sxs-lookup"><span data-stu-id="7fd0c-166">Or call `ByteString.ToByteArray()` to convert an instance back into a byte array:</span></span>

```csharp
var payload = await client.GetPayload(new PayloadRequest());

await File.WriteAllBytesAsync(path, payload.Data.ToByteArray());
```

### <a name="decimals"></a><span data-ttu-id="7fd0c-167">Décimales</span><span class="sxs-lookup"><span data-stu-id="7fd0c-167">Decimals</span></span>

<span data-ttu-id="7fd0c-168">Protobuf ne prend pas en charge en mode natif le `decimal` type .net, juste `double` et `float` .</span><span class="sxs-lookup"><span data-stu-id="7fd0c-168">Protobuf doesn't natively support the .NET `decimal` type, just `double` and `float`.</span></span> <span data-ttu-id="7fd0c-169">Il existe une discussion continue dans le projet Protobuf sur la possibilité d’ajouter un type décimal standard aux types de Well-Known, avec la prise en charge de la plateforme pour les langages et les infrastructures qui le prennent en charge.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-169">There's an ongoing discussion in the Protobuf project about the possibility of adding a standard decimal type to the Well-Known Types, with platform support for languages and frameworks that support it.</span></span> <span data-ttu-id="7fd0c-170">Rien n’a encore été implémenté.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-170">Nothing has been implemented yet.</span></span>

<span data-ttu-id="7fd0c-171">Il est possible de créer une définition de message pour représenter le `decimal` type qui fonctionne pour la sérialisation sécurisée entre les clients et les serveurs .net.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-171">It's possible to create a message definition to represent the `decimal` type that works for safe serialization between .NET clients and servers.</span></span> <span data-ttu-id="7fd0c-172">Toutefois, les développeurs sur d’autres plateformes doivent comprendre le format utilisé et implémenter leur propre gestion.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-172">But developers on other platforms would have to understand the format being used and implement their own handling for it.</span></span>

#### <a name="creating-a-custom-decimal-type-for-protobuf"></a><span data-ttu-id="7fd0c-173">Création d’un type décimal personnalisé pour Protobuf</span><span class="sxs-lookup"><span data-stu-id="7fd0c-173">Creating a custom decimal type for Protobuf</span></span>

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

<span data-ttu-id="7fd0c-174">Le `nanos` champ représente les valeurs de `0.999_999_999` à `-0.999_999_999` .</span><span class="sxs-lookup"><span data-stu-id="7fd0c-174">The `nanos` field represents values from `0.999_999_999` to `-0.999_999_999`.</span></span> <span data-ttu-id="7fd0c-175">Par exemple, la `decimal` valeur est `1.5m` représentée sous la forme `{ units = 1, nanos = 500_000_000 }` .</span><span class="sxs-lookup"><span data-stu-id="7fd0c-175">For example, the `decimal` value `1.5m` would be represented as `{ units = 1, nanos = 500_000_000 }`.</span></span> <span data-ttu-id="7fd0c-176">C’est la raison pour laquelle le `nanos` champ de cet exemple utilise le `sfixed32` type, qui s’encode plus efficacement que `int32` pour les valeurs plus élevées.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-176">This is why the `nanos` field in this example uses the `sfixed32` type, which encodes more efficiently than `int32` for larger values.</span></span> <span data-ttu-id="7fd0c-177">Si le `units` champ est négatif, le `nanos` champ doit également être négatif.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-177">If the `units` field is negative, the `nanos` field should also be negative.</span></span>

> [!NOTE]
> <span data-ttu-id="7fd0c-178">Il existe plusieurs autres algorithmes pour l’encodage de `decimal` valeurs en tant que chaînes d’octets, mais ce message est plus facile à comprendre que l’un d’entre eux.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-178">There are multiple other algorithms for encoding `decimal` values as byte strings, but this message is easier to understand than any of them.</span></span> <span data-ttu-id="7fd0c-179">Les valeurs ne sont pas affectées par Big-endian ou Little-endian sur différentes plateformes.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-179">The values are not affected by big-endian or little-endian on different platforms.</span></span>

<span data-ttu-id="7fd0c-180">La conversion entre ce type et le `decimal` type BCL peut être implémentée en C# comme suit :</span><span class="sxs-lookup"><span data-stu-id="7fd0c-180">Conversion between this type and the BCL `decimal` type might be implemented in C# like this:</span></span>

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

## <a name="collections"></a><span data-ttu-id="7fd0c-181">Collections</span><span class="sxs-lookup"><span data-stu-id="7fd0c-181">Collections</span></span>

### <a name="lists"></a><span data-ttu-id="7fd0c-182">Listes</span><span class="sxs-lookup"><span data-stu-id="7fd0c-182">Lists</span></span>

<span data-ttu-id="7fd0c-183">Les listes dans Protobuf sont spécifiées à l’aide du `repeated` mot clé prefix sur un champ.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-183">Lists in Protobuf are specified by using the `repeated` prefix keyword on a field.</span></span> <span data-ttu-id="7fd0c-184">L’exemple suivant montre comment créer une liste :</span><span class="sxs-lookup"><span data-stu-id="7fd0c-184">The following example shows how to create a list:</span></span>

```protobuf
message Person {
    // ...
    repeated string roles = 8;
}
```

<span data-ttu-id="7fd0c-185">Dans le code généré, `repeated` les champs sont représentés par le `Google.Protobuf.Collections.RepeatedField<T>` type générique.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-185">In the generated code, `repeated` fields are represented by the `Google.Protobuf.Collections.RepeatedField<T>` generic type.</span></span>

```csharp
public class Person
{
    // ...
    public RepeatedField<string> Roles { get; }
}
```

<span data-ttu-id="7fd0c-186">L'objet `RepeatedField<T>` implémente l'objet <xref:System.Collections.Generic.IList%601>.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-186">`RepeatedField<T>` implements <xref:System.Collections.Generic.IList%601>.</span></span> <span data-ttu-id="7fd0c-187">Vous pouvez donc utiliser des requêtes LINQ ou la convertir en un tableau ou une liste.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-187">So you can use LINQ queries or convert it to an array or a list.</span></span> <span data-ttu-id="7fd0c-188">`RepeatedField<T>` les propriétés n’ont pas d’accesseur Set public.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-188">`RepeatedField<T>` properties don't have a public setter.</span></span> <span data-ttu-id="7fd0c-189">Les éléments doivent être ajoutés à la collection existante.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-189">Items should be added to the existing collection.</span></span>

```csharp
var person = new Person();

// Add one item.
person.Roles.Add("user");

// Add all items from another collection.
var roles = new [] { "admin", "manager" };
person.Roles.Add(roles);
```

### <a name="dictionaries"></a><span data-ttu-id="7fd0c-190">Dictionnaires</span><span class="sxs-lookup"><span data-stu-id="7fd0c-190">Dictionaries</span></span>

<span data-ttu-id="7fd0c-191">Le <xref:System.Collections.Generic.IDictionary%602> type .net est représenté dans Protobuf à l’aide de `map<key_type, value_type>` .</span><span class="sxs-lookup"><span data-stu-id="7fd0c-191">The .NET <xref:System.Collections.Generic.IDictionary%602> type is represented in Protobuf using `map<key_type, value_type>`.</span></span>

```protobuf
message Person {
    // ...
    map<string, string> attributes = 9;
}
```

<span data-ttu-id="7fd0c-192">Dans le code .NET généré, `map` les champs sont représentés par le `Google.Protobuf.Collections.MapField<TKey, TValue>` type générique.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-192">In generated .NET code, `map` fields are represented by the `Google.Protobuf.Collections.MapField<TKey, TValue>` generic type.</span></span> <span data-ttu-id="7fd0c-193">L'objet `MapField<TKey, TValue>` implémente l'objet <xref:System.Collections.Generic.IDictionary%602>.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-193">`MapField<TKey, TValue>` implements <xref:System.Collections.Generic.IDictionary%602>.</span></span> <span data-ttu-id="7fd0c-194">Comme les `repeated` Propriétés, les `map` Propriétés n’ont pas d’accesseur Set public.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-194">Like `repeated` properties, `map` properties don't have a public setter.</span></span> <span data-ttu-id="7fd0c-195">Les éléments doivent être ajoutés à la collection existante.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-195">Items should be added to the existing collection.</span></span>

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

## <a name="unstructured-and-conditional-messages"></a><span data-ttu-id="7fd0c-196">Messages non structurés et conditionnels</span><span class="sxs-lookup"><span data-stu-id="7fd0c-196">Unstructured and conditional messages</span></span>

<span data-ttu-id="7fd0c-197">Protobuf est un format de messagerie contrat en premier.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-197">Protobuf is a contract-first messaging format.</span></span> <span data-ttu-id="7fd0c-198">Les messages d’une application, y compris ses champs et types, doivent être spécifiés dans `.proto` les fichiers lors de la génération de l’application.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-198">An app's messages, including its fields and types, must be specified in `.proto` files when the app is built.</span></span> <span data-ttu-id="7fd0c-199">La conception « contrat en premier » de Protobuf est très utile pour appliquer le contenu des messages, mais peut limiter les scénarios où un contrat strict n’est pas obligatoire :</span><span class="sxs-lookup"><span data-stu-id="7fd0c-199">Protobuf's contract-first design is great at enforcing message content but can limit scenarios where a strict contract isn't required:</span></span>

* <span data-ttu-id="7fd0c-200">Messages avec des charges utiles inconnues.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-200">Messages with unknown payloads.</span></span> <span data-ttu-id="7fd0c-201">Par exemple, un message avec un champ qui peut contenir n’importe quel message.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-201">For example, a message with a field that could contain any message.</span></span>
* <span data-ttu-id="7fd0c-202">Messages conditionnels.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-202">Conditional messages.</span></span> <span data-ttu-id="7fd0c-203">Par exemple, un message retourné par un service gRPC peut être un résultat de réussite ou un résultat d’erreur.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-203">For example, a message returned from a gRPC service might be a success result or an error result.</span></span>
* <span data-ttu-id="7fd0c-204">Valeurs dynamiques.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-204">Dynamic values.</span></span> <span data-ttu-id="7fd0c-205">Par exemple, un message avec un champ qui contient une collection non structurée de valeurs, similaire à JSON.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-205">For example, a message with a field that contains an unstructured collection of values, similar to JSON.</span></span>

<span data-ttu-id="7fd0c-206">Protobuf offre des fonctionnalités et des types de langage pour prendre en charge ces scénarios.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-206">Protobuf offers language features and types to support these scenarios.</span></span>

### <a name="any"></a><span data-ttu-id="7fd0c-207">Quelconque</span><span class="sxs-lookup"><span data-stu-id="7fd0c-207">Any</span></span>

<span data-ttu-id="7fd0c-208">Le `Any` type vous permet d’utiliser des messages en tant que types incorporés sans avoir leur `.proto` définition.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-208">The `Any` type lets you use messages as embedded types without having their `.proto` definition.</span></span> <span data-ttu-id="7fd0c-209">Pour utiliser le `Any` type, importez `any.proto` .</span><span class="sxs-lookup"><span data-stu-id="7fd0c-209">To use the `Any` type, import `any.proto`.</span></span>

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

### <a name="oneof"></a><span data-ttu-id="7fd0c-210">Oneof</span><span class="sxs-lookup"><span data-stu-id="7fd0c-210">Oneof</span></span>

<span data-ttu-id="7fd0c-211">`oneof` les champs sont une fonctionnalité de langage.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-211">`oneof` fields are a language feature.</span></span> <span data-ttu-id="7fd0c-212">Le compilateur gère le `oneof` mot clé lorsqu’il génère la classe de message.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-212">The compiler handles the `oneof` keyword when it generates the message class.</span></span> <span data-ttu-id="7fd0c-213">L’utilisation `oneof` de pour spécifier un message de réponse qui peut retourner `Person` ou `Error` peut se présenter comme suit :</span><span class="sxs-lookup"><span data-stu-id="7fd0c-213">Using `oneof` to specify a response message that could either return a `Person` or `Error` might look like this:</span></span>

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

<span data-ttu-id="7fd0c-214">Les champs de l' `oneof` ensemble doivent avoir des numéros de champ uniques dans la déclaration globale du message.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-214">Fields within the `oneof` set must have unique field numbers in the overall message declaration.</span></span>

<span data-ttu-id="7fd0c-215">Lors de l’utilisation de `oneof` , le code C# généré inclut une énumération qui spécifie les champs qui ont été définis.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-215">When using `oneof`, the generated C# code includes an enum that specifies which of the fields has been set.</span></span> <span data-ttu-id="7fd0c-216">Vous pouvez tester l’énumération pour rechercher le champ défini.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-216">You can test the enum to find which field is set.</span></span> <span data-ttu-id="7fd0c-217">Les champs qui ne sont pas définis retournent `null` ou la valeur par défaut, plutôt que de lever une exception.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-217">Fields that aren't set return `null` or the default value, rather than throwing an exception.</span></span>

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

### <a name="value"></a><span data-ttu-id="7fd0c-218">Valeur</span><span class="sxs-lookup"><span data-stu-id="7fd0c-218">Value</span></span>

<span data-ttu-id="7fd0c-219">Le `Value` type représente une valeur typée dynamiquement.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-219">The `Value` type represents a dynamically typed value.</span></span> <span data-ttu-id="7fd0c-220">Il peut s’agir d' `null` un nombre, d’une chaîne, d’une valeur booléenne, d’un dictionnaire de valeurs ( `Struct` ) ou d’une liste de valeurs ( `ValueList` ).</span><span class="sxs-lookup"><span data-stu-id="7fd0c-220">It can be either `null`, a number, a string, a boolean, a dictionary of values (`Struct`), or a list of values (`ValueList`).</span></span> <span data-ttu-id="7fd0c-221">`Value` est un type de Well-Known Protobuf qui utilise la fonctionnalité décrite précédemment `oneof` .</span><span class="sxs-lookup"><span data-stu-id="7fd0c-221">`Value` is a Protobuf Well-Known Type that uses the previously discussed `oneof` feature.</span></span> <span data-ttu-id="7fd0c-222">Pour utiliser le `Value` type, importez `struct.proto` .</span><span class="sxs-lookup"><span data-stu-id="7fd0c-222">To use the `Value` type, import `struct.proto`.</span></span>

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

<span data-ttu-id="7fd0c-223">L’utilisation de `Value` direct peut être détaillée.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-223">Using `Value` directly can be verbose.</span></span> <span data-ttu-id="7fd0c-224">Une autre façon d’utiliser `Value` est la prise en charge intégrée de Protobuf pour le mappage des messages à JSON.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-224">An alternative way to use `Value` is with Protobuf's built-in support for mapping messages to JSON.</span></span> <span data-ttu-id="7fd0c-225">`JsonFormatter`Les types de Protobuf et `JsonWriter` peuvent être utilisés avec n’importe quel message Protobuf.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-225">Protobuf's `JsonFormatter` and `JsonWriter` types can be used with any Protobuf message.</span></span> <span data-ttu-id="7fd0c-226">`Value` est particulièrement bien adapté à la conversion vers et à partir de JSON.</span><span class="sxs-lookup"><span data-stu-id="7fd0c-226">`Value` is particularly well suited to being converted to and from JSON.</span></span>

<span data-ttu-id="7fd0c-227">Il s’agit de l’équivalent JSON du code précédent :</span><span class="sxs-lookup"><span data-stu-id="7fd0c-227">This is the JSON equivalent of the previous code:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="7fd0c-228">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="7fd0c-228">Additional resources</span></span>

* [<span data-ttu-id="7fd0c-229">Guide du langage Protobuf</span><span class="sxs-lookup"><span data-stu-id="7fd0c-229">Protobuf language guide</span></span>](https://developers.google.com/protocol-buffers/docs/proto3#simple)
* <xref:grpc/versioning>
