---
title: Utiliser le protocole MessagePack Hub dans SignalR pour ASP.net Core
author: bradygaster
description: Ajoutez le protocole MessagePack Hub à ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/24/2020
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
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: 7c3640e9cd2c5d392400a115813584861f789554
ms.sourcegitcommit: 8b0e9a72c1599ce21830c843558a661ba908ce32
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024689"
---
# <a name="use-messagepack-hub-protocol-in-no-locsignalr-for-aspnet-core"></a>Utiliser le protocole MessagePack Hub dans SignalR pour ASP.net Core

::: moniker range=">= aspnetcore-5.0"

Cet article suppose que le lecteur est familiarisé avec les sujets abordés dans la rubrique [prise en main](xref:tutorials/signalr).

## <a name="what-is-messagepack"></a>Qu’est-ce que MessagePack ?

[MessagePack](https://msgpack.org/index.html) est un format de sérialisation binaire rapide et compact. Elle est utile lorsque les performances et la bande passante sont un problème, car elle crée des messages plus petits par rapport à [JSON](https://www.json.org/). Les messages binaires sont illisibles lors de la consultation des journaux et des suivis réseau, sauf si les octets sont transmis via un analyseur MessagePack. SignalR dispose d’une prise en charge intégrée du format MessagePack et fournit des API pour le client et le serveur à utiliser.

## <a name="configure-messagepack-on-the-server"></a>Configurer MessagePack sur le serveur

Pour activer le protocole MessagePack Hub sur le serveur, installez le `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package dans votre application. Dans la `Startup.ConfigureServices` méthode, ajoutez `AddMessagePackProtocol` à l' `AddSignalR` appel pour activer la prise en charge de MessagePack sur le serveur.

> [!NOTE]
> JSON est activé par défaut. L’ajout de MessagePack permet la prise en charge des clients JSON et MessagePack.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Pour personnaliser la façon dont MessagePack met en forme vos données, `AddMessagePackProtocol` utilise un délégué pour configurer les options. Dans ce délégué, la `SerializerOptions` propriété peut être utilisée pour configurer les options de sérialisation MessagePack. Pour plus d’informations sur le fonctionnement des programmes de résolution, consultez la bibliothèque MessagePack sur [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Les attributs peuvent être utilisés sur les objets que vous souhaitez sérialiser pour définir la manière dont ils doivent être gérés.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(new CustomResolver())
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

> [!WARNING]
> Nous vous recommandons vivement de consulter la [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) et d’appliquer les correctifs recommandés. Par exemple, appeler `.WithSecurity(MessagePackSecurity.UntrustedData)` lors du remplacement de `SerializerOptions` .

## <a name="configure-messagepack-on-the-client"></a>Configurer MessagePack sur le client

> [!NOTE]
> JSON est activé par défaut pour les clients pris en charge. Les clients ne peuvent prendre en charge qu’un seul protocole. L’ajout de la prise en charge de MessagePack remplacera tous les protocoles précédemment configurés.

### <a name="net-client"></a>Client .NET

Pour activer MessagePack dans le client .NET, installez le `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package et appelez `AddMessagePackProtocol` sur `HubConnectionBuilder` .

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Cet `AddMessagePackProtocol` appel prend un délégué pour configurer les options de la même façon que le serveur.

### <a name="javascript-client"></a>Client JavaScript

La prise en charge de MessagePack pour le client JavaScript est fournie par le [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) package NPM. Installez le package en exécutant la commande suivante dans une interface de commande :

```bash
npm install @microsoft/signalr-protocol-msgpack
```

Après l’installation du package NPM, le module peut être utilisé directement via un chargeur de module JavaScript ou importé dans le navigateur en référençant le fichier suivant :

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

Dans un navigateur, la `msgpack5` bibliothèque doit également être référencée. Utilisez une `<script>` balise pour créer une référence. La bibliothèque se trouve sur *node_modules\msgpack5\dist\msgpack5.js*.

> [!NOTE]
> Lors de l’utilisation de l' `<script>` élément, l’ordre est important. Si *signalr-protocol-msgpack.js* est référencé avant *msgpack5.js*, une erreur se produit lors de la tentative de connexion à MessagePack. *signalr.js* est également requis avant *signalr-protocol-msgpack.js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

L’ajout `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` de à l' `HubConnectionBuilder` configure le client pour qu’il utilise le protocole MessagePack lors de la connexion à un serveur.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> À ce stade, il n’existe aucune option de configuration pour le protocole MessagePack sur le client JavaScript.

### <a name="java-client"></a>Client Java

Pour activer MessagePack avec Java, installez le `com.microsoft.signalr.messagepack` Package. Quand vous utilisez Gradle, ajoutez la ligne suivante à la `dependencies` section du fichier *Build. Gradle* :

```gradle
implementation 'com.microsoft.signalr.messagepack:signalr-messagepack:5.0.0'
```

Quand vous utilisez Maven, ajoutez les lignes suivantes à l’intérieur `<dependencies>` de l’élément du fichier *pom.xml* :

[!code-xml[pom.xml dependency element messagePack](java-client/sample/pom.xml?name=snippet_dependencyElement_messagePack)]

Appelez `withHubProtocol(new MessagePackHubProtocol())` sur `HubConnectionBuilder` .

```java
HubConnection messagePackConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withHubProtocol(new MessagePackHubProtocol())
    .build();
```

## <a name="messagepack-quirks"></a>MessagePack excentriques

Il existe quelques problèmes à connaître lors de l’utilisation du protocole MessagePack Hub.

### <a name="messagepack-is-case-sensitive"></a>MessagePack respecte la casse

Le protocole MessagePack respecte la casse. Par exemple, considérez la classe C# suivante :

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Lors de l’envoi à partir du client JavaScript, vous devez utiliser des `PascalCased` noms de propriété, car la casse doit correspondre exactement à la classe C#. Par exemple :

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

L’utilisation `camelCased` de noms n’est pas correctement liée à la classe C#. Vous pouvez contourner ce contournement en utilisant l' `Key` attribut pour spécifier un nom différent pour la propriété MessagePack. Pour plus d’informations, consultez [la documentation MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime. Kind n’est pas conservé lors de la sérialisation/désérialisation

Le protocole MessagePack ne fournit pas de méthode pour encoder la `Kind` valeur d’un `DateTime` . Par conséquent, lors de la désérialisation d’une date, le protocole MessagePack Hub est converti au format UTC si le `DateTime.Kind` n’est pas le cas, `DateTimeKind.Local` et il ne peut pas toucher l’heure et le passer tel quel. Si vous utilisez des `DateTime` valeurs, nous vous recommandons de convertir au format UTC avant de les envoyer. Convertissez-les de l’heure UTC en heure locale lorsque vous les recevez.

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime. MinValue n’est pas pris en charge par MessagePack dans JavaScript

La bibliothèque [msgpack5](https://github.com/mcollina/msgpack5) utilisée par le SignalR client JavaScript ne prend pas en charge le `timestamp96` type dans MessagePack. Ce type est utilisé pour encoder les valeurs de date très volumineuses (soit très tôt dans le passé, soit très loin dans le futur). La valeur de `DateTime.MinValue` est `January 1, 0001` , qui doit être encodée dans une `timestamp96` valeur. Pour cette raison, l’envoi `DateTime.MinValue` à un client JavaScript n’est pas pris en charge. Lorsque `DateTime.MinValue` est reçu par le client JavaScript, l’erreur suivante est levée :

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Habituellement, `DateTime.MinValue` est utilisé pour encoder une valeur « manquante » ou `null` . Si vous devez encoder cette valeur dans MessagePack, utilisez une `DateTime` valeur Nullable ( `DateTime?` ) ou encodez une `bool` valeur distincte indiquant si la date est présente.

Pour plus d’informations sur cette limitation, consultez GitHub issue [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Prise en charge de MessagePack dans l’environnement de compilation « à l’avance »

La bibliothèque [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) utilisée par le client et le serveur .NET utilise la génération de code pour optimiser la sérialisation. Par conséquent, il n’est pas pris en charge par défaut dans les environnements qui utilisent la compilation « à l’avance » (par exemple, Xamarin iOS ou Unity). Il est possible d’utiliser MessagePack dans ces environnements en prégénérant le code de sérialiseur/désérialiseur. Pour plus d’informations, consultez [la documentation MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin). Une fois que vous avez généré le prétraitement des sérialiseurs, vous pouvez les inscrire à l’aide du délégué de configuration passé à `AddMessagePackProtocol` :

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        StaticCompositeResolver.Instance.Register(
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        );
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(StaticCompositeResolver.Instance)
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a>Les vérifications de type sont plus strictes dans MessagePack

Le protocole JSON Hub effectue des conversions de type pendant la désérialisation. Par exemple, si l’objet entrant a une valeur de propriété qui est un nombre ( `{ foo: 42 }` ) mais que la propriété de la classe .net est de type `string` , la valeur est convertie. Toutefois, MessagePack n’effectue pas cette conversion et lèvera une exception qui est visible dans les journaux côté serveur (et dans la console) :

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Pour plus d’informations sur cette limitation, consultez GitHub issue [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).

### <a name="chars-and-strings-in-java"></a>Caractères et chaînes en Java

Dans le client Java, les `char` objets sont sérialisés en tant qu’objets d’un seul caractère `String` . Cela diffère avec le client C# et JavaScript, qui les sérialise en tant qu' `short` objets. La spécification MessagePack elle-même ne définit pas le comportement des `char` objets, c’est pourquoi il revient à l’auteur de la bibliothèque de déterminer comment les sérialiser. La différence de comportement entre nos clients est le résultat des bibliothèques que nous avons utilisées pour nos implémentations.

## <a name="related-resources"></a>Ressources associées

* [Bien démarrer](xref:tutorials/signalr)
* [Client .NET](xref:signalr/dotnet-client)
* [Client JavaScript](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

Cet article suppose que le lecteur est familiarisé avec les sujets abordés dans la rubrique [prise en main](xref:tutorials/signalr).

## <a name="what-is-messagepack"></a>Qu’est-ce que MessagePack ?

[MessagePack](https://msgpack.org/index.html) est un format de sérialisation binaire rapide et compact. Elle est utile lorsque les performances et la bande passante sont un problème, car elle crée des messages plus petits par rapport à [JSON](https://www.json.org/). Les messages binaires sont illisibles lors de la consultation des journaux et des suivis réseau, sauf si les octets sont transmis via un analyseur MessagePack. SignalR dispose d’une prise en charge intégrée du format MessagePack et fournit des API que le client et le serveur doivent utiliser.

## <a name="configure-messagepack-on-the-server"></a>Configurer MessagePack sur le serveur

Pour activer le protocole MessagePack Hub sur le serveur, installez le `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package dans votre application. Dans la `Startup.ConfigureServices` méthode, ajoutez `AddMessagePackProtocol` à l' `AddSignalR` appel pour activer la prise en charge de MessagePack sur le serveur.

> [!NOTE]
> JSON est activé par défaut. L’ajout de MessagePack permet la prise en charge des clients JSON et MessagePack.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Pour personnaliser la façon dont MessagePack met en forme vos données, `AddMessagePackProtocol` utilise un délégué pour configurer les options. Dans ce délégué, la `FormatterResolvers` propriété peut être utilisée pour configurer les options de sérialisation MessagePack. Pour plus d’informations sur le fonctionnement des programmes de résolution, consultez la bibliothèque MessagePack sur [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Les attributs peuvent être utilisés sur les objets que vous souhaitez sérialiser pour définir la manière dont ils doivent être gérés.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> Nous vous recommandons vivement de consulter la [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) et d’appliquer les correctifs recommandés. Par exemple, en affectant `MessagePackSecurity.Active` à la propriété statique la valeur `MessagePackSecurity.UntrustedData` . La définition `MessagePackSecurity.Active` de requiert l’installation manuelle [d’une version 1.9. x de MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3). `MessagePack`L’installation de la version 1.9. x met à niveau la version SignalR . `MessagePack` la version 2. x introduit des modifications avec rupture et est incompatible avec les SignalR versions 3,1 et antérieures. Lorsque `MessagePackSecurity.Active` n’a pas `MessagePackSecurity.UntrustedData` la valeur, un client malveillant peut provoquer un déni de service. Définissez `MessagePackSecurity.Active` dans `Program.Main` , comme illustré dans le code suivant :

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>Configurer MessagePack sur le client

> [!NOTE]
> JSON est activé par défaut pour les clients pris en charge. Les clients ne peuvent prendre en charge qu’un seul protocole. L’ajout de la prise en charge de MessagePack remplacera tous les protocoles précédemment configurés.

### <a name="net-client"></a>Client .NET

Pour activer MessagePack dans le client .NET, installez le `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package et appelez `AddMessagePackProtocol` sur `HubConnectionBuilder` .

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Cet `AddMessagePackProtocol` appel prend un délégué pour configurer les options de la même façon que le serveur.

### <a name="javascript-client"></a>Client JavaScript

La prise en charge de MessagePack pour le client JavaScript est fournie par le [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) package NPM. Installez le package en exécutant la commande suivante dans une interface de commande :

```bash
npm install @microsoft/signalr-protocol-msgpack
```

Après l’installation du package NPM, le module peut être utilisé directement via un chargeur de module JavaScript ou importé dans le navigateur en référençant le fichier suivant :

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

Dans un navigateur, la `msgpack5` bibliothèque doit également être référencée. Utilisez une `<script>` balise pour créer une référence. La bibliothèque se trouve sur *node_modules\msgpack5\dist\msgpack5.js*.

> [!NOTE]
> Lors de l’utilisation de l' `<script>` élément, l’ordre est important. Si *signalr-protocol-msgpack.js* est référencé avant *msgpack5.js*, une erreur se produit lors de la tentative de connexion à MessagePack. *signalr.js* est également requis avant *signalr-protocol-msgpack.js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

L’ajout `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` de à l' `HubConnectionBuilder` configure le client pour qu’il utilise le protocole MessagePack lors de la connexion à un serveur.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> À ce stade, il n’existe aucune option de configuration pour le protocole MessagePack sur le client JavaScript.

## <a name="messagepack-quirks"></a>MessagePack excentriques

Il existe quelques problèmes à connaître lors de l’utilisation du protocole MessagePack Hub.

### <a name="messagepack-is-case-sensitive"></a>MessagePack respecte la casse

Le protocole MessagePack respecte la casse. Par exemple, considérez la classe C# suivante :

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Lors de l’envoi à partir du client JavaScript, vous devez utiliser des `PascalCased` noms de propriété, car la casse doit correspondre exactement à la classe C#. Par exemple :

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

L’utilisation `camelCased` de noms n’est pas correctement liée à la classe C#. Vous pouvez contourner ce contournement en utilisant l' `Key` attribut pour spécifier un nom différent pour la propriété MessagePack. Pour plus d’informations, consultez [la documentation MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime. Kind n’est pas conservé lors de la sérialisation/désérialisation

Le protocole MessagePack ne fournit pas de méthode pour encoder la `Kind` valeur d’un `DateTime` . Par conséquent, lors de la désérialisation d’une date, le protocole MessagePack Hub part du principe que la date d’entrée est au format UTC. Si vous utilisez des `DateTime` valeurs en heure locale, nous vous recommandons de convertir au format UTC avant de les envoyer. Convertissez-les de l’heure UTC en heure locale lorsque vous les recevez.

Pour plus d’informations sur cette limitation, consultez GitHub issue [ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime. MinValue n’est pas pris en charge par MessagePack dans JavaScript

La bibliothèque [msgpack5](https://github.com/mcollina/msgpack5) utilisée par le SignalR client JavaScript ne prend pas en charge le `timestamp96` type dans MessagePack. Ce type est utilisé pour encoder les valeurs de date très volumineuses (soit très tôt dans le passé, soit très loin dans le futur). La valeur de `DateTime.MinValue` est `January 1, 0001` , qui doit être encodée dans une `timestamp96` valeur. Pour cette raison, l’envoi `DateTime.MinValue` à un client JavaScript n’est pas pris en charge. Lorsque `DateTime.MinValue` est reçu par le client JavaScript, l’erreur suivante est levée :

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Habituellement, `DateTime.MinValue` est utilisé pour encoder une valeur « manquante » ou `null` . Si vous devez encoder cette valeur dans MessagePack, utilisez une `DateTime` valeur Nullable ( `DateTime?` ) ou encodez une `bool` valeur distincte indiquant si la date est présente.

Pour plus d’informations sur cette limitation, consultez GitHub issue [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Prise en charge de MessagePack dans l’environnement de compilation « à l’avance »

La bibliothèque [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) utilisée par le client et le serveur .NET utilise la génération de code pour optimiser la sérialisation. Par conséquent, il n’est pas pris en charge par défaut dans les environnements qui utilisent la compilation « à l’avance » (par exemple, Xamarin iOS ou Unity). Il est possible d’utiliser MessagePack dans ces environnements en prégénérant le code de sérialiseur/désérialiseur. Pour plus d’informations, consultez [la documentation MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports). Une fois que vous avez généré le prétraitement des sérialiseurs, vous pouvez les inscrire à l’aide du délégué de configuration passé à `AddMessagePackProtocol` :

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a>Les vérifications de type sont plus strictes dans MessagePack

Le protocole JSON Hub effectue des conversions de type pendant la désérialisation. Par exemple, si l’objet entrant a une valeur de propriété qui est un nombre ( `{ foo: 42 }` ) mais que la propriété de la classe .net est de type `string` , la valeur est convertie. Toutefois, MessagePack n’effectue pas cette conversion et lèvera une exception qui est visible dans les journaux côté serveur (et dans la console) :

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Pour plus d’informations sur cette limitation, consultez GitHub issue [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Ressources associées

* [Bien démarrer](xref:tutorials/signalr)
* [Client .NET](xref:signalr/dotnet-client)
* [Client JavaScript](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Cet article suppose que le lecteur est familiarisé avec les sujets abordés dans la rubrique [prise en main](xref:tutorials/signalr).

## <a name="what-is-messagepack"></a>Qu’est-ce que MessagePack ?

[MessagePack](https://msgpack.org/index.html) est un format de sérialisation binaire rapide et compact. Elle est utile lorsque les performances et la bande passante sont un problème, car elle crée des messages plus petits par rapport à [JSON](https://www.json.org/). Les messages binaires sont illisibles lors de la consultation des journaux et des suivis réseau, sauf si les octets sont transmis via un analyseur MessagePack. SignalR dispose d’une prise en charge intégrée du format MessagePack et fournit des API que le client et le serveur doivent utiliser.

## <a name="configure-messagepack-on-the-server"></a>Configurer MessagePack sur le serveur

Pour activer le protocole MessagePack Hub sur le serveur, installez le `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package dans votre application. Dans la `Startup.ConfigureServices` méthode, ajoutez `AddMessagePackProtocol` à l' `AddSignalR` appel pour activer la prise en charge de MessagePack sur le serveur.

> [!NOTE]
> JSON est activé par défaut. L’ajout de MessagePack permet la prise en charge des clients JSON et MessagePack.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Pour personnaliser la façon dont MessagePack met en forme vos données, `AddMessagePackProtocol` utilise un délégué pour configurer les options. Dans ce délégué, la `FormatterResolvers` propriété peut être utilisée pour configurer les options de sérialisation MessagePack. Pour plus d’informations sur le fonctionnement des programmes de résolution, consultez la bibliothèque MessagePack sur [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Les attributs peuvent être utilisés sur les objets que vous souhaitez sérialiser pour définir la manière dont ils doivent être gérés.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> Nous vous recommandons vivement de consulter la [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) et d’appliquer les correctifs recommandés. Par exemple, en affectant `MessagePackSecurity.Active` à la propriété statique la valeur `MessagePackSecurity.UntrustedData` . La définition `MessagePackSecurity.Active` de requiert l’installation manuelle [d’une version 1.9. x de MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3). `MessagePack`L’installation de la version 1.9. x met à niveau la version SignalR . Lorsque `MessagePackSecurity.Active` n’a pas `MessagePackSecurity.UntrustedData` la valeur, un client malveillant peut provoquer un déni de service. Définissez `MessagePackSecurity.Active` dans `Program.Main` , comme illustré dans le code suivant :

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>Configurer MessagePack sur le client

> [!NOTE]
> JSON est activé par défaut pour les clients pris en charge. Les clients ne peuvent prendre en charge qu’un seul protocole. L’ajout de la prise en charge de MessagePack remplacera tous les protocoles précédemment configurés.

### <a name="net-client"></a>Client .NET

Pour activer MessagePack dans le client .NET, installez le `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package et appelez `AddMessagePackProtocol` sur `HubConnectionBuilder` .

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Cet `AddMessagePackProtocol` appel prend un délégué pour configurer les options de la même façon que le serveur.

### <a name="javascript-client"></a>Client JavaScript

La prise en charge de MessagePack pour le client JavaScript est fournie par le [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) package NPM. Installez le package en exécutant la commande suivante dans une interface de commande :

```bash
npm install @aspnet/signalr-protocol-msgpack
```

Après l’installation du package NPM, le module peut être utilisé directement via un chargeur de module JavaScript ou importé dans le navigateur en référençant le fichier suivant :

*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*

Dans un navigateur, la `msgpack5` bibliothèque doit également être référencée. Utilisez une `<script>` balise pour créer une référence. La bibliothèque se trouve sur *node_modules\msgpack5\dist\msgpack5.js*.

> [!NOTE]
> Lors de l’utilisation de l' `<script>` élément, l’ordre est important. Si *signalr-protocol-msgpack.js* est référencé avant *msgpack5.js*, une erreur se produit lors de la tentative de connexion à MessagePack. *signalr.js* est également requis avant *signalr-protocol-msgpack.js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

L’ajout `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` de à l' `HubConnectionBuilder` configure le client pour qu’il utilise le protocole MessagePack lors de la connexion à un serveur.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> À ce stade, il n’existe aucune option de configuration pour le protocole MessagePack sur le client JavaScript.

## <a name="messagepack-quirks"></a>MessagePack excentriques

Il existe quelques problèmes à connaître lors de l’utilisation du protocole MessagePack Hub.

### <a name="messagepack-is-case-sensitive"></a>MessagePack respecte la casse

Le protocole MessagePack respecte la casse. Par exemple, considérez la classe C# suivante :

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Lors de l’envoi à partir du client JavaScript, vous devez utiliser des `PascalCased` noms de propriété, car la casse doit correspondre exactement à la classe C#. Par exemple :

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

L’utilisation `camelCased` de noms n’est pas correctement liée à la classe C#. Vous pouvez contourner ce contournement en utilisant l' `Key` attribut pour spécifier un nom différent pour la propriété MessagePack. Pour plus d’informations, consultez [la documentation MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime. Kind n’est pas conservé lors de la sérialisation/désérialisation

Le protocole MessagePack ne fournit pas de méthode pour encoder la `Kind` valeur d’un `DateTime` . Par conséquent, lors de la désérialisation d’une date, le protocole MessagePack Hub part du principe que la date d’entrée est au format UTC. Si vous utilisez des `DateTime` valeurs en heure locale, nous vous recommandons de convertir au format UTC avant de les envoyer. Convertissez-les de l’heure UTC en heure locale lorsque vous les recevez.

Pour plus d’informations sur cette limitation, consultez GitHub issue [ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime. MinValue n’est pas pris en charge par MessagePack dans JavaScript

La bibliothèque [msgpack5](https://github.com/mcollina/msgpack5) utilisée par le SignalR client JavaScript ne prend pas en charge le `timestamp96` type dans MessagePack. Ce type est utilisé pour encoder les valeurs de date très volumineuses (soit très tôt dans le passé, soit très loin dans le futur). La valeur de `DateTime.MinValue` est `January 1, 0001` qui doit être encodée dans une `timestamp96` valeur. Pour cette raison, l’envoi `DateTime.MinValue` à un client JavaScript n’est pas pris en charge. Lorsque `DateTime.MinValue` est reçu par le client JavaScript, l’erreur suivante est levée :

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Habituellement, `DateTime.MinValue` est utilisé pour encoder une valeur « manquante » ou `null` . Si vous devez encoder cette valeur dans MessagePack, utilisez une `DateTime` valeur Nullable ( `DateTime?` ) ou encodez une `bool` valeur distincte indiquant si la date est présente.

Pour plus d’informations sur cette limitation, consultez GitHub issue [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Prise en charge de MessagePack dans l’environnement de compilation « à l’avance »

La bibliothèque [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) utilisée par le client et le serveur .NET utilise la génération de code pour optimiser la sérialisation. Par conséquent, il n’est pas pris en charge par défaut dans les environnements qui utilisent la compilation « à l’avance » (par exemple, Xamarin iOS ou Unity). Il est possible d’utiliser MessagePack dans ces environnements en prégénérant le code de sérialiseur/désérialiseur. Pour plus d’informations, consultez [la documentation MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports). Une fois que vous avez généré le prétraitement des sérialiseurs, vous pouvez les inscrire à l’aide du délégué de configuration passé à `AddMessagePackProtocol` :

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a>Les vérifications de type sont plus strictes dans MessagePack

Le protocole JSON Hub effectue des conversions de type pendant la désérialisation. Par exemple, si l’objet entrant a une valeur de propriété qui est un nombre ( `{ foo: 42 }` ) mais que la propriété de la classe .net est de type `string` , la valeur est convertie. Toutefois, MessagePack n’effectue pas cette conversion et lèvera une exception qui est visible dans les journaux côté serveur (et dans la console) :

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Pour plus d’informations sur cette limitation, consultez GitHub issue [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Ressources associées

* [Bien démarrer](xref:tutorials/signalr)
* [Client .NET](xref:signalr/dotnet-client)
* [Client JavaScript](xref:signalr/javascript-client)

::: moniker-end
