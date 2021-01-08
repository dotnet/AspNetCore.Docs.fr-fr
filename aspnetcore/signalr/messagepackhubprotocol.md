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
# <a name="use-messagepack-hub-protocol-in-no-locsignalr-for-aspnet-core"></a><span data-ttu-id="35417-103">Utiliser le protocole MessagePack Hub dans SignalR pour ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="35417-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="35417-104">Cet article suppose que le lecteur est familiarisé avec les sujets abordés dans la rubrique [prise en main](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="35417-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="35417-105">Qu’est-ce que MessagePack ?</span><span class="sxs-lookup"><span data-stu-id="35417-105">What is MessagePack?</span></span>

<span data-ttu-id="35417-106">[MessagePack](https://msgpack.org/index.html) est un format de sérialisation binaire rapide et compact.</span><span class="sxs-lookup"><span data-stu-id="35417-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="35417-107">Elle est utile lorsque les performances et la bande passante sont un problème, car elle crée des messages plus petits par rapport à [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="35417-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="35417-108">Les messages binaires sont illisibles lors de la consultation des journaux et des suivis réseau, sauf si les octets sont transmis via un analyseur MessagePack.</span><span class="sxs-lookup"><span data-stu-id="35417-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="35417-109">SignalR dispose d’une prise en charge intégrée du format MessagePack et fournit des API pour le client et le serveur à utiliser.</span><span class="sxs-lookup"><span data-stu-id="35417-109">SignalR has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="35417-110">Configurer MessagePack sur le serveur</span><span class="sxs-lookup"><span data-stu-id="35417-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="35417-111">Pour activer le protocole MessagePack Hub sur le serveur, installez le `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package dans votre application.</span><span class="sxs-lookup"><span data-stu-id="35417-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="35417-112">Dans la `Startup.ConfigureServices` méthode, ajoutez `AddMessagePackProtocol` à l' `AddSignalR` appel pour activer la prise en charge de MessagePack sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="35417-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="35417-113">JSON est activé par défaut.</span><span class="sxs-lookup"><span data-stu-id="35417-113">JSON is enabled by default.</span></span> <span data-ttu-id="35417-114">L’ajout de MessagePack permet la prise en charge des clients JSON et MessagePack.</span><span class="sxs-lookup"><span data-stu-id="35417-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="35417-115">Pour personnaliser la façon dont MessagePack met en forme vos données, `AddMessagePackProtocol` utilise un délégué pour configurer les options.</span><span class="sxs-lookup"><span data-stu-id="35417-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="35417-116">Dans ce délégué, la `SerializerOptions` propriété peut être utilisée pour configurer les options de sérialisation MessagePack.</span><span class="sxs-lookup"><span data-stu-id="35417-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="35417-117">Pour plus d’informations sur le fonctionnement des programmes de résolution, consultez la bibliothèque MessagePack sur [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="35417-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="35417-118">Les attributs peuvent être utilisés sur les objets que vous souhaitez sérialiser pour définir la manière dont ils doivent être gérés.</span><span class="sxs-lookup"><span data-stu-id="35417-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="35417-119">Nous vous recommandons vivement de consulter la [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) et d’appliquer les correctifs recommandés.</span><span class="sxs-lookup"><span data-stu-id="35417-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="35417-120">Par exemple, appeler `.WithSecurity(MessagePackSecurity.UntrustedData)` lors du remplacement de `SerializerOptions` .</span><span class="sxs-lookup"><span data-stu-id="35417-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="35417-121">Configurer MessagePack sur le client</span><span class="sxs-lookup"><span data-stu-id="35417-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="35417-122">JSON est activé par défaut pour les clients pris en charge.</span><span class="sxs-lookup"><span data-stu-id="35417-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="35417-123">Les clients ne peuvent prendre en charge qu’un seul protocole.</span><span class="sxs-lookup"><span data-stu-id="35417-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="35417-124">L’ajout de la prise en charge de MessagePack remplacera tous les protocoles précédemment configurés.</span><span class="sxs-lookup"><span data-stu-id="35417-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="35417-125">Client .NET</span><span class="sxs-lookup"><span data-stu-id="35417-125">.NET client</span></span>

<span data-ttu-id="35417-126">Pour activer MessagePack dans le client .NET, installez le `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package et appelez `AddMessagePackProtocol` sur `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="35417-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="35417-127">Cet `AddMessagePackProtocol` appel prend un délégué pour configurer les options de la même façon que le serveur.</span><span class="sxs-lookup"><span data-stu-id="35417-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="35417-128">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="35417-128">JavaScript client</span></span>

<span data-ttu-id="35417-129">La prise en charge de MessagePack pour le client JavaScript est fournie par le [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) package NPM.</span><span class="sxs-lookup"><span data-stu-id="35417-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="35417-130">Installez le package en exécutant la commande suivante dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="35417-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="35417-131">Après l’installation du package NPM, le module peut être utilisé directement via un chargeur de module JavaScript ou importé dans le navigateur en référençant le fichier suivant :</span><span class="sxs-lookup"><span data-stu-id="35417-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="35417-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="35417-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="35417-133">Dans un navigateur, la `msgpack5` bibliothèque doit également être référencée.</span><span class="sxs-lookup"><span data-stu-id="35417-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="35417-134">Utilisez une `<script>` balise pour créer une référence.</span><span class="sxs-lookup"><span data-stu-id="35417-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="35417-135">La bibliothèque se trouve sur *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="35417-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="35417-136">Lors de l’utilisation de l' `<script>` élément, l’ordre est important.</span><span class="sxs-lookup"><span data-stu-id="35417-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="35417-137">Si *signalr-protocol-msgpack.js* est référencé avant *msgpack5.js*, une erreur se produit lors de la tentative de connexion à MessagePack.</span><span class="sxs-lookup"><span data-stu-id="35417-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="35417-138">*signalr.js* est également requis avant *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="35417-138">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="35417-139">L’ajout `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` de à l' `HubConnectionBuilder` configure le client pour qu’il utilise le protocole MessagePack lors de la connexion à un serveur.</span><span class="sxs-lookup"><span data-stu-id="35417-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="35417-140">À ce stade, il n’existe aucune option de configuration pour le protocole MessagePack sur le client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="35417-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

### <a name="java-client"></a><span data-ttu-id="35417-141">Client Java</span><span class="sxs-lookup"><span data-stu-id="35417-141">Java client</span></span>

<span data-ttu-id="35417-142">Pour activer MessagePack avec Java, installez le `com.microsoft.signalr.messagepack` Package.</span><span class="sxs-lookup"><span data-stu-id="35417-142">To enable MessagePack with Java, install the `com.microsoft.signalr.messagepack` package.</span></span> <span data-ttu-id="35417-143">Quand vous utilisez Gradle, ajoutez la ligne suivante à la `dependencies` section du fichier *Build. Gradle* :</span><span class="sxs-lookup"><span data-stu-id="35417-143">When using Gradle, add the following line to the `dependencies` section of the *build.gradle* file:</span></span>

```gradle
implementation 'com.microsoft.signalr.messagepack:signalr-messagepack:5.0.0'
```

<span data-ttu-id="35417-144">Quand vous utilisez Maven, ajoutez les lignes suivantes à l’intérieur `<dependencies>` de l’élément du fichier *pom.xml* :</span><span class="sxs-lookup"><span data-stu-id="35417-144">When using Maven, add the following lines inside the `<dependencies>` element of the *pom.xml* file:</span></span>

[!code-xml[pom.xml dependency element messagePack](java-client/sample/pom.xml?name=snippet_dependencyElement_messagePack)]

<span data-ttu-id="35417-145">Appelez `withHubProtocol(new MessagePackHubProtocol())` sur `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="35417-145">Call `withHubProtocol(new MessagePackHubProtocol())` on `HubConnectionBuilder`.</span></span>

```java
HubConnection messagePackConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withHubProtocol(new MessagePackHubProtocol())
    .build();
```

## <a name="messagepack-quirks"></a><span data-ttu-id="35417-146">MessagePack excentriques</span><span class="sxs-lookup"><span data-stu-id="35417-146">MessagePack quirks</span></span>

<span data-ttu-id="35417-147">Il existe quelques problèmes à connaître lors de l’utilisation du protocole MessagePack Hub.</span><span class="sxs-lookup"><span data-stu-id="35417-147">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="35417-148">MessagePack respecte la casse</span><span class="sxs-lookup"><span data-stu-id="35417-148">MessagePack is case-sensitive</span></span>

<span data-ttu-id="35417-149">Le protocole MessagePack respecte la casse.</span><span class="sxs-lookup"><span data-stu-id="35417-149">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="35417-150">Par exemple, considérez la classe C# suivante :</span><span class="sxs-lookup"><span data-stu-id="35417-150">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="35417-151">Lors de l’envoi à partir du client JavaScript, vous devez utiliser des `PascalCased` noms de propriété, car la casse doit correspondre exactement à la classe C#.</span><span class="sxs-lookup"><span data-stu-id="35417-151">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="35417-152">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="35417-152">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="35417-153">L’utilisation `camelCased` de noms n’est pas correctement liée à la classe C#.</span><span class="sxs-lookup"><span data-stu-id="35417-153">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="35417-154">Vous pouvez contourner ce contournement en utilisant l' `Key` attribut pour spécifier un nom différent pour la propriété MessagePack.</span><span class="sxs-lookup"><span data-stu-id="35417-154">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="35417-155">Pour plus d’informations, consultez [la documentation MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="35417-155">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="35417-156">DateTime. Kind n’est pas conservé lors de la sérialisation/désérialisation</span><span class="sxs-lookup"><span data-stu-id="35417-156">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="35417-157">Le protocole MessagePack ne fournit pas de méthode pour encoder la `Kind` valeur d’un `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="35417-157">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="35417-158">Par conséquent, lors de la désérialisation d’une date, le protocole MessagePack Hub est converti au format UTC si le `DateTime.Kind` n’est pas le cas, `DateTimeKind.Local` et il ne peut pas toucher l’heure et le passer tel quel.</span><span class="sxs-lookup"><span data-stu-id="35417-158">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="35417-159">Si vous utilisez des `DateTime` valeurs, nous vous recommandons de convertir au format UTC avant de les envoyer.</span><span class="sxs-lookup"><span data-stu-id="35417-159">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="35417-160">Convertissez-les de l’heure UTC en heure locale lorsque vous les recevez.</span><span class="sxs-lookup"><span data-stu-id="35417-160">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="35417-161">DateTime. MinValue n’est pas pris en charge par MessagePack dans JavaScript</span><span class="sxs-lookup"><span data-stu-id="35417-161">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="35417-162">La bibliothèque [msgpack5](https://github.com/mcollina/msgpack5) utilisée par le SignalR client JavaScript ne prend pas en charge le `timestamp96` type dans MessagePack.</span><span class="sxs-lookup"><span data-stu-id="35417-162">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="35417-163">Ce type est utilisé pour encoder les valeurs de date très volumineuses (soit très tôt dans le passé, soit très loin dans le futur).</span><span class="sxs-lookup"><span data-stu-id="35417-163">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="35417-164">La valeur de `DateTime.MinValue` est `January 1, 0001` , qui doit être encodée dans une `timestamp96` valeur.</span><span class="sxs-lookup"><span data-stu-id="35417-164">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="35417-165">Pour cette raison, l’envoi `DateTime.MinValue` à un client JavaScript n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="35417-165">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="35417-166">Lorsque `DateTime.MinValue` est reçu par le client JavaScript, l’erreur suivante est levée :</span><span class="sxs-lookup"><span data-stu-id="35417-166">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="35417-167">Habituellement, `DateTime.MinValue` est utilisé pour encoder une valeur « manquante » ou `null` .</span><span class="sxs-lookup"><span data-stu-id="35417-167">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="35417-168">Si vous devez encoder cette valeur dans MessagePack, utilisez une `DateTime` valeur Nullable ( `DateTime?` ) ou encodez une `bool` valeur distincte indiquant si la date est présente.</span><span class="sxs-lookup"><span data-stu-id="35417-168">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="35417-169">Pour plus d’informations sur cette limitation, consultez GitHub issue [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="35417-169">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="35417-170">Prise en charge de MessagePack dans l’environnement de compilation « à l’avance »</span><span class="sxs-lookup"><span data-stu-id="35417-170">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="35417-171">La bibliothèque [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) utilisée par le client et le serveur .NET utilise la génération de code pour optimiser la sérialisation.</span><span class="sxs-lookup"><span data-stu-id="35417-171">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="35417-172">Par conséquent, il n’est pas pris en charge par défaut dans les environnements qui utilisent la compilation « à l’avance » (par exemple, Xamarin iOS ou Unity).</span><span class="sxs-lookup"><span data-stu-id="35417-172">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="35417-173">Il est possible d’utiliser MessagePack dans ces environnements en prégénérant le code de sérialiseur/désérialiseur.</span><span class="sxs-lookup"><span data-stu-id="35417-173">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="35417-174">Pour plus d’informations, consultez [la documentation MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span><span class="sxs-lookup"><span data-stu-id="35417-174">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="35417-175">Une fois que vous avez généré le prétraitement des sérialiseurs, vous pouvez les inscrire à l’aide du délégué de configuration passé à `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="35417-175">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="35417-176">Les vérifications de type sont plus strictes dans MessagePack</span><span class="sxs-lookup"><span data-stu-id="35417-176">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="35417-177">Le protocole JSON Hub effectue des conversions de type pendant la désérialisation.</span><span class="sxs-lookup"><span data-stu-id="35417-177">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="35417-178">Par exemple, si l’objet entrant a une valeur de propriété qui est un nombre ( `{ foo: 42 }` ) mais que la propriété de la classe .net est de type `string` , la valeur est convertie.</span><span class="sxs-lookup"><span data-stu-id="35417-178">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="35417-179">Toutefois, MessagePack n’effectue pas cette conversion et lèvera une exception qui est visible dans les journaux côté serveur (et dans la console) :</span><span class="sxs-lookup"><span data-stu-id="35417-179">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="35417-180">Pour plus d’informations sur cette limitation, consultez GitHub issue [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="35417-180">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

### <a name="chars-and-strings-in-java"></a><span data-ttu-id="35417-181">Caractères et chaînes en Java</span><span class="sxs-lookup"><span data-stu-id="35417-181">Chars and Strings in Java</span></span>

<span data-ttu-id="35417-182">Dans le client Java, les `char` objets sont sérialisés en tant qu’objets d’un seul caractère `String` .</span><span class="sxs-lookup"><span data-stu-id="35417-182">In the java client, `char` objects will be serialized as one-character `String` objects.</span></span> <span data-ttu-id="35417-183">Cela diffère avec le client C# et JavaScript, qui les sérialise en tant qu' `short` objets.</span><span class="sxs-lookup"><span data-stu-id="35417-183">This is in contrast with the C# and JavaScript client, which serialize them as `short` objects.</span></span> <span data-ttu-id="35417-184">La spécification MessagePack elle-même ne définit pas le comportement des `char` objets, c’est pourquoi il revient à l’auteur de la bibliothèque de déterminer comment les sérialiser.</span><span class="sxs-lookup"><span data-stu-id="35417-184">The MessagePack spec itself does not define behavior for `char` objects, so it is up to the library author to determine how to serialize them.</span></span> <span data-ttu-id="35417-185">La différence de comportement entre nos clients est le résultat des bibliothèques que nous avons utilisées pour nos implémentations.</span><span class="sxs-lookup"><span data-stu-id="35417-185">The difference in behavior between our clients is a result of the libraries we used for our implementations.</span></span>

## <a name="related-resources"></a><span data-ttu-id="35417-186">Ressources associées</span><span class="sxs-lookup"><span data-stu-id="35417-186">Related resources</span></span>

* [<span data-ttu-id="35417-187">Bien démarrer</span><span class="sxs-lookup"><span data-stu-id="35417-187">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="35417-188">Client .NET</span><span class="sxs-lookup"><span data-stu-id="35417-188">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="35417-189">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="35417-189">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="35417-190">Cet article suppose que le lecteur est familiarisé avec les sujets abordés dans la rubrique [prise en main](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="35417-190">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="35417-191">Qu’est-ce que MessagePack ?</span><span class="sxs-lookup"><span data-stu-id="35417-191">What is MessagePack?</span></span>

<span data-ttu-id="35417-192">[MessagePack](https://msgpack.org/index.html) est un format de sérialisation binaire rapide et compact.</span><span class="sxs-lookup"><span data-stu-id="35417-192">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="35417-193">Elle est utile lorsque les performances et la bande passante sont un problème, car elle crée des messages plus petits par rapport à [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="35417-193">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="35417-194">Les messages binaires sont illisibles lors de la consultation des journaux et des suivis réseau, sauf si les octets sont transmis via un analyseur MessagePack.</span><span class="sxs-lookup"><span data-stu-id="35417-194">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="35417-195">SignalR dispose d’une prise en charge intégrée du format MessagePack et fournit des API que le client et le serveur doivent utiliser.</span><span class="sxs-lookup"><span data-stu-id="35417-195">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="35417-196">Configurer MessagePack sur le serveur</span><span class="sxs-lookup"><span data-stu-id="35417-196">Configure MessagePack on the server</span></span>

<span data-ttu-id="35417-197">Pour activer le protocole MessagePack Hub sur le serveur, installez le `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package dans votre application.</span><span class="sxs-lookup"><span data-stu-id="35417-197">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="35417-198">Dans la `Startup.ConfigureServices` méthode, ajoutez `AddMessagePackProtocol` à l' `AddSignalR` appel pour activer la prise en charge de MessagePack sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="35417-198">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="35417-199">JSON est activé par défaut.</span><span class="sxs-lookup"><span data-stu-id="35417-199">JSON is enabled by default.</span></span> <span data-ttu-id="35417-200">L’ajout de MessagePack permet la prise en charge des clients JSON et MessagePack.</span><span class="sxs-lookup"><span data-stu-id="35417-200">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="35417-201">Pour personnaliser la façon dont MessagePack met en forme vos données, `AddMessagePackProtocol` utilise un délégué pour configurer les options.</span><span class="sxs-lookup"><span data-stu-id="35417-201">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="35417-202">Dans ce délégué, la `FormatterResolvers` propriété peut être utilisée pour configurer les options de sérialisation MessagePack.</span><span class="sxs-lookup"><span data-stu-id="35417-202">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="35417-203">Pour plus d’informations sur le fonctionnement des programmes de résolution, consultez la bibliothèque MessagePack sur [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="35417-203">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="35417-204">Les attributs peuvent être utilisés sur les objets que vous souhaitez sérialiser pour définir la manière dont ils doivent être gérés.</span><span class="sxs-lookup"><span data-stu-id="35417-204">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="35417-205">Nous vous recommandons vivement de consulter la [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) et d’appliquer les correctifs recommandés.</span><span class="sxs-lookup"><span data-stu-id="35417-205">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="35417-206">Par exemple, en affectant `MessagePackSecurity.Active` à la propriété statique la valeur `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="35417-206">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="35417-207">La définition `MessagePackSecurity.Active` de requiert l’installation manuelle [d’une version 1.9. x de MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="35417-207">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="35417-208">`MessagePack`L’installation de la version 1.9. x met à niveau la version SignalR .</span><span class="sxs-lookup"><span data-stu-id="35417-208">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="35417-209">`MessagePack` la version 2. x introduit des modifications avec rupture et est incompatible avec les SignalR versions 3,1 et antérieures.</span><span class="sxs-lookup"><span data-stu-id="35417-209">`MessagePack` version 2.x introduced breaking changes and is incompatible with SignalR versions 3.1 and earlier.</span></span> <span data-ttu-id="35417-210">Lorsque `MessagePackSecurity.Active` n’a pas `MessagePackSecurity.UntrustedData` la valeur, un client malveillant peut provoquer un déni de service.</span><span class="sxs-lookup"><span data-stu-id="35417-210">When `MessagePackSecurity.Active` isn't set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="35417-211">Définissez `MessagePackSecurity.Active` dans `Program.Main` , comme illustré dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="35417-211">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="35417-212">Configurer MessagePack sur le client</span><span class="sxs-lookup"><span data-stu-id="35417-212">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="35417-213">JSON est activé par défaut pour les clients pris en charge.</span><span class="sxs-lookup"><span data-stu-id="35417-213">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="35417-214">Les clients ne peuvent prendre en charge qu’un seul protocole.</span><span class="sxs-lookup"><span data-stu-id="35417-214">Clients can only support a single protocol.</span></span> <span data-ttu-id="35417-215">L’ajout de la prise en charge de MessagePack remplacera tous les protocoles précédemment configurés.</span><span class="sxs-lookup"><span data-stu-id="35417-215">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="35417-216">Client .NET</span><span class="sxs-lookup"><span data-stu-id="35417-216">.NET client</span></span>

<span data-ttu-id="35417-217">Pour activer MessagePack dans le client .NET, installez le `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package et appelez `AddMessagePackProtocol` sur `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="35417-217">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="35417-218">Cet `AddMessagePackProtocol` appel prend un délégué pour configurer les options de la même façon que le serveur.</span><span class="sxs-lookup"><span data-stu-id="35417-218">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="35417-219">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="35417-219">JavaScript client</span></span>

<span data-ttu-id="35417-220">La prise en charge de MessagePack pour le client JavaScript est fournie par le [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) package NPM.</span><span class="sxs-lookup"><span data-stu-id="35417-220">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="35417-221">Installez le package en exécutant la commande suivante dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="35417-221">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="35417-222">Après l’installation du package NPM, le module peut être utilisé directement via un chargeur de module JavaScript ou importé dans le navigateur en référençant le fichier suivant :</span><span class="sxs-lookup"><span data-stu-id="35417-222">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="35417-223">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="35417-223">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="35417-224">Dans un navigateur, la `msgpack5` bibliothèque doit également être référencée.</span><span class="sxs-lookup"><span data-stu-id="35417-224">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="35417-225">Utilisez une `<script>` balise pour créer une référence.</span><span class="sxs-lookup"><span data-stu-id="35417-225">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="35417-226">La bibliothèque se trouve sur *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="35417-226">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="35417-227">Lors de l’utilisation de l' `<script>` élément, l’ordre est important.</span><span class="sxs-lookup"><span data-stu-id="35417-227">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="35417-228">Si *signalr-protocol-msgpack.js* est référencé avant *msgpack5.js*, une erreur se produit lors de la tentative de connexion à MessagePack.</span><span class="sxs-lookup"><span data-stu-id="35417-228">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="35417-229">*signalr.js* est également requis avant *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="35417-229">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="35417-230">L’ajout `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` de à l' `HubConnectionBuilder` configure le client pour qu’il utilise le protocole MessagePack lors de la connexion à un serveur.</span><span class="sxs-lookup"><span data-stu-id="35417-230">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="35417-231">À ce stade, il n’existe aucune option de configuration pour le protocole MessagePack sur le client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="35417-231">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="35417-232">MessagePack excentriques</span><span class="sxs-lookup"><span data-stu-id="35417-232">MessagePack quirks</span></span>

<span data-ttu-id="35417-233">Il existe quelques problèmes à connaître lors de l’utilisation du protocole MessagePack Hub.</span><span class="sxs-lookup"><span data-stu-id="35417-233">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="35417-234">MessagePack respecte la casse</span><span class="sxs-lookup"><span data-stu-id="35417-234">MessagePack is case-sensitive</span></span>

<span data-ttu-id="35417-235">Le protocole MessagePack respecte la casse.</span><span class="sxs-lookup"><span data-stu-id="35417-235">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="35417-236">Par exemple, considérez la classe C# suivante :</span><span class="sxs-lookup"><span data-stu-id="35417-236">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="35417-237">Lors de l’envoi à partir du client JavaScript, vous devez utiliser des `PascalCased` noms de propriété, car la casse doit correspondre exactement à la classe C#.</span><span class="sxs-lookup"><span data-stu-id="35417-237">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="35417-238">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="35417-238">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="35417-239">L’utilisation `camelCased` de noms n’est pas correctement liée à la classe C#.</span><span class="sxs-lookup"><span data-stu-id="35417-239">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="35417-240">Vous pouvez contourner ce contournement en utilisant l' `Key` attribut pour spécifier un nom différent pour la propriété MessagePack.</span><span class="sxs-lookup"><span data-stu-id="35417-240">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="35417-241">Pour plus d’informations, consultez [la documentation MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="35417-241">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="35417-242">DateTime. Kind n’est pas conservé lors de la sérialisation/désérialisation</span><span class="sxs-lookup"><span data-stu-id="35417-242">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="35417-243">Le protocole MessagePack ne fournit pas de méthode pour encoder la `Kind` valeur d’un `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="35417-243">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="35417-244">Par conséquent, lors de la désérialisation d’une date, le protocole MessagePack Hub part du principe que la date d’entrée est au format UTC.</span><span class="sxs-lookup"><span data-stu-id="35417-244">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="35417-245">Si vous utilisez des `DateTime` valeurs en heure locale, nous vous recommandons de convertir au format UTC avant de les envoyer.</span><span class="sxs-lookup"><span data-stu-id="35417-245">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="35417-246">Convertissez-les de l’heure UTC en heure locale lorsque vous les recevez.</span><span class="sxs-lookup"><span data-stu-id="35417-246">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="35417-247">Pour plus d’informations sur cette limitation, consultez GitHub issue [ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="35417-247">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="35417-248">DateTime. MinValue n’est pas pris en charge par MessagePack dans JavaScript</span><span class="sxs-lookup"><span data-stu-id="35417-248">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="35417-249">La bibliothèque [msgpack5](https://github.com/mcollina/msgpack5) utilisée par le SignalR client JavaScript ne prend pas en charge le `timestamp96` type dans MessagePack.</span><span class="sxs-lookup"><span data-stu-id="35417-249">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="35417-250">Ce type est utilisé pour encoder les valeurs de date très volumineuses (soit très tôt dans le passé, soit très loin dans le futur).</span><span class="sxs-lookup"><span data-stu-id="35417-250">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="35417-251">La valeur de `DateTime.MinValue` est `January 1, 0001` , qui doit être encodée dans une `timestamp96` valeur.</span><span class="sxs-lookup"><span data-stu-id="35417-251">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="35417-252">Pour cette raison, l’envoi `DateTime.MinValue` à un client JavaScript n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="35417-252">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="35417-253">Lorsque `DateTime.MinValue` est reçu par le client JavaScript, l’erreur suivante est levée :</span><span class="sxs-lookup"><span data-stu-id="35417-253">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="35417-254">Habituellement, `DateTime.MinValue` est utilisé pour encoder une valeur « manquante » ou `null` .</span><span class="sxs-lookup"><span data-stu-id="35417-254">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="35417-255">Si vous devez encoder cette valeur dans MessagePack, utilisez une `DateTime` valeur Nullable ( `DateTime?` ) ou encodez une `bool` valeur distincte indiquant si la date est présente.</span><span class="sxs-lookup"><span data-stu-id="35417-255">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="35417-256">Pour plus d’informations sur cette limitation, consultez GitHub issue [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="35417-256">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="35417-257">Prise en charge de MessagePack dans l’environnement de compilation « à l’avance »</span><span class="sxs-lookup"><span data-stu-id="35417-257">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="35417-258">La bibliothèque [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) utilisée par le client et le serveur .NET utilise la génération de code pour optimiser la sérialisation.</span><span class="sxs-lookup"><span data-stu-id="35417-258">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="35417-259">Par conséquent, il n’est pas pris en charge par défaut dans les environnements qui utilisent la compilation « à l’avance » (par exemple, Xamarin iOS ou Unity).</span><span class="sxs-lookup"><span data-stu-id="35417-259">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="35417-260">Il est possible d’utiliser MessagePack dans ces environnements en prégénérant le code de sérialiseur/désérialiseur.</span><span class="sxs-lookup"><span data-stu-id="35417-260">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="35417-261">Pour plus d’informations, consultez [la documentation MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="35417-261">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="35417-262">Une fois que vous avez généré le prétraitement des sérialiseurs, vous pouvez les inscrire à l’aide du délégué de configuration passé à `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="35417-262">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="35417-263">Les vérifications de type sont plus strictes dans MessagePack</span><span class="sxs-lookup"><span data-stu-id="35417-263">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="35417-264">Le protocole JSON Hub effectue des conversions de type pendant la désérialisation.</span><span class="sxs-lookup"><span data-stu-id="35417-264">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="35417-265">Par exemple, si l’objet entrant a une valeur de propriété qui est un nombre ( `{ foo: 42 }` ) mais que la propriété de la classe .net est de type `string` , la valeur est convertie.</span><span class="sxs-lookup"><span data-stu-id="35417-265">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="35417-266">Toutefois, MessagePack n’effectue pas cette conversion et lèvera une exception qui est visible dans les journaux côté serveur (et dans la console) :</span><span class="sxs-lookup"><span data-stu-id="35417-266">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="35417-267">Pour plus d’informations sur cette limitation, consultez GitHub issue [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="35417-267">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="35417-268">Ressources associées</span><span class="sxs-lookup"><span data-stu-id="35417-268">Related resources</span></span>

* [<span data-ttu-id="35417-269">Bien démarrer</span><span class="sxs-lookup"><span data-stu-id="35417-269">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="35417-270">Client .NET</span><span class="sxs-lookup"><span data-stu-id="35417-270">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="35417-271">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="35417-271">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="35417-272">Cet article suppose que le lecteur est familiarisé avec les sujets abordés dans la rubrique [prise en main](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="35417-272">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="35417-273">Qu’est-ce que MessagePack ?</span><span class="sxs-lookup"><span data-stu-id="35417-273">What is MessagePack?</span></span>

<span data-ttu-id="35417-274">[MessagePack](https://msgpack.org/index.html) est un format de sérialisation binaire rapide et compact.</span><span class="sxs-lookup"><span data-stu-id="35417-274">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="35417-275">Elle est utile lorsque les performances et la bande passante sont un problème, car elle crée des messages plus petits par rapport à [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="35417-275">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="35417-276">Les messages binaires sont illisibles lors de la consultation des journaux et des suivis réseau, sauf si les octets sont transmis via un analyseur MessagePack.</span><span class="sxs-lookup"><span data-stu-id="35417-276">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="35417-277">SignalR dispose d’une prise en charge intégrée du format MessagePack et fournit des API que le client et le serveur doivent utiliser.</span><span class="sxs-lookup"><span data-stu-id="35417-277">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="35417-278">Configurer MessagePack sur le serveur</span><span class="sxs-lookup"><span data-stu-id="35417-278">Configure MessagePack on the server</span></span>

<span data-ttu-id="35417-279">Pour activer le protocole MessagePack Hub sur le serveur, installez le `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package dans votre application.</span><span class="sxs-lookup"><span data-stu-id="35417-279">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="35417-280">Dans la `Startup.ConfigureServices` méthode, ajoutez `AddMessagePackProtocol` à l' `AddSignalR` appel pour activer la prise en charge de MessagePack sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="35417-280">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="35417-281">JSON est activé par défaut.</span><span class="sxs-lookup"><span data-stu-id="35417-281">JSON is enabled by default.</span></span> <span data-ttu-id="35417-282">L’ajout de MessagePack permet la prise en charge des clients JSON et MessagePack.</span><span class="sxs-lookup"><span data-stu-id="35417-282">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="35417-283">Pour personnaliser la façon dont MessagePack met en forme vos données, `AddMessagePackProtocol` utilise un délégué pour configurer les options.</span><span class="sxs-lookup"><span data-stu-id="35417-283">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="35417-284">Dans ce délégué, la `FormatterResolvers` propriété peut être utilisée pour configurer les options de sérialisation MessagePack.</span><span class="sxs-lookup"><span data-stu-id="35417-284">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="35417-285">Pour plus d’informations sur le fonctionnement des programmes de résolution, consultez la bibliothèque MessagePack sur [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="35417-285">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="35417-286">Les attributs peuvent être utilisés sur les objets que vous souhaitez sérialiser pour définir la manière dont ils doivent être gérés.</span><span class="sxs-lookup"><span data-stu-id="35417-286">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="35417-287">Nous vous recommandons vivement de consulter la [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) et d’appliquer les correctifs recommandés.</span><span class="sxs-lookup"><span data-stu-id="35417-287">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="35417-288">Par exemple, en affectant `MessagePackSecurity.Active` à la propriété statique la valeur `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="35417-288">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="35417-289">La définition `MessagePackSecurity.Active` de requiert l’installation manuelle [d’une version 1.9. x de MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="35417-289">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="35417-290">`MessagePack`L’installation de la version 1.9. x met à niveau la version SignalR .</span><span class="sxs-lookup"><span data-stu-id="35417-290">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="35417-291">Lorsque `MessagePackSecurity.Active` n’a pas `MessagePackSecurity.UntrustedData` la valeur, un client malveillant peut provoquer un déni de service.</span><span class="sxs-lookup"><span data-stu-id="35417-291">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="35417-292">Définissez `MessagePackSecurity.Active` dans `Program.Main` , comme illustré dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="35417-292">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="35417-293">Configurer MessagePack sur le client</span><span class="sxs-lookup"><span data-stu-id="35417-293">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="35417-294">JSON est activé par défaut pour les clients pris en charge.</span><span class="sxs-lookup"><span data-stu-id="35417-294">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="35417-295">Les clients ne peuvent prendre en charge qu’un seul protocole.</span><span class="sxs-lookup"><span data-stu-id="35417-295">Clients can only support a single protocol.</span></span> <span data-ttu-id="35417-296">L’ajout de la prise en charge de MessagePack remplacera tous les protocoles précédemment configurés.</span><span class="sxs-lookup"><span data-stu-id="35417-296">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="35417-297">Client .NET</span><span class="sxs-lookup"><span data-stu-id="35417-297">.NET client</span></span>

<span data-ttu-id="35417-298">Pour activer MessagePack dans le client .NET, installez le `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package et appelez `AddMessagePackProtocol` sur `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="35417-298">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="35417-299">Cet `AddMessagePackProtocol` appel prend un délégué pour configurer les options de la même façon que le serveur.</span><span class="sxs-lookup"><span data-stu-id="35417-299">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="35417-300">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="35417-300">JavaScript client</span></span>

<span data-ttu-id="35417-301">La prise en charge de MessagePack pour le client JavaScript est fournie par le [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) package NPM.</span><span class="sxs-lookup"><span data-stu-id="35417-301">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="35417-302">Installez le package en exécutant la commande suivante dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="35417-302">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="35417-303">Après l’installation du package NPM, le module peut être utilisé directement via un chargeur de module JavaScript ou importé dans le navigateur en référençant le fichier suivant :</span><span class="sxs-lookup"><span data-stu-id="35417-303">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="35417-304">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="35417-304">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="35417-305">Dans un navigateur, la `msgpack5` bibliothèque doit également être référencée.</span><span class="sxs-lookup"><span data-stu-id="35417-305">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="35417-306">Utilisez une `<script>` balise pour créer une référence.</span><span class="sxs-lookup"><span data-stu-id="35417-306">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="35417-307">La bibliothèque se trouve sur *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="35417-307">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="35417-308">Lors de l’utilisation de l' `<script>` élément, l’ordre est important.</span><span class="sxs-lookup"><span data-stu-id="35417-308">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="35417-309">Si *signalr-protocol-msgpack.js* est référencé avant *msgpack5.js*, une erreur se produit lors de la tentative de connexion à MessagePack.</span><span class="sxs-lookup"><span data-stu-id="35417-309">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="35417-310">*signalr.js* est également requis avant *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="35417-310">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="35417-311">L’ajout `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` de à l' `HubConnectionBuilder` configure le client pour qu’il utilise le protocole MessagePack lors de la connexion à un serveur.</span><span class="sxs-lookup"><span data-stu-id="35417-311">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="35417-312">À ce stade, il n’existe aucune option de configuration pour le protocole MessagePack sur le client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="35417-312">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="35417-313">MessagePack excentriques</span><span class="sxs-lookup"><span data-stu-id="35417-313">MessagePack quirks</span></span>

<span data-ttu-id="35417-314">Il existe quelques problèmes à connaître lors de l’utilisation du protocole MessagePack Hub.</span><span class="sxs-lookup"><span data-stu-id="35417-314">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="35417-315">MessagePack respecte la casse</span><span class="sxs-lookup"><span data-stu-id="35417-315">MessagePack is case-sensitive</span></span>

<span data-ttu-id="35417-316">Le protocole MessagePack respecte la casse.</span><span class="sxs-lookup"><span data-stu-id="35417-316">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="35417-317">Par exemple, considérez la classe C# suivante :</span><span class="sxs-lookup"><span data-stu-id="35417-317">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="35417-318">Lors de l’envoi à partir du client JavaScript, vous devez utiliser des `PascalCased` noms de propriété, car la casse doit correspondre exactement à la classe C#.</span><span class="sxs-lookup"><span data-stu-id="35417-318">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="35417-319">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="35417-319">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="35417-320">L’utilisation `camelCased` de noms n’est pas correctement liée à la classe C#.</span><span class="sxs-lookup"><span data-stu-id="35417-320">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="35417-321">Vous pouvez contourner ce contournement en utilisant l' `Key` attribut pour spécifier un nom différent pour la propriété MessagePack.</span><span class="sxs-lookup"><span data-stu-id="35417-321">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="35417-322">Pour plus d’informations, consultez [la documentation MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="35417-322">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="35417-323">DateTime. Kind n’est pas conservé lors de la sérialisation/désérialisation</span><span class="sxs-lookup"><span data-stu-id="35417-323">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="35417-324">Le protocole MessagePack ne fournit pas de méthode pour encoder la `Kind` valeur d’un `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="35417-324">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="35417-325">Par conséquent, lors de la désérialisation d’une date, le protocole MessagePack Hub part du principe que la date d’entrée est au format UTC.</span><span class="sxs-lookup"><span data-stu-id="35417-325">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="35417-326">Si vous utilisez des `DateTime` valeurs en heure locale, nous vous recommandons de convertir au format UTC avant de les envoyer.</span><span class="sxs-lookup"><span data-stu-id="35417-326">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="35417-327">Convertissez-les de l’heure UTC en heure locale lorsque vous les recevez.</span><span class="sxs-lookup"><span data-stu-id="35417-327">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="35417-328">Pour plus d’informations sur cette limitation, consultez GitHub issue [ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="35417-328">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="35417-329">DateTime. MinValue n’est pas pris en charge par MessagePack dans JavaScript</span><span class="sxs-lookup"><span data-stu-id="35417-329">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="35417-330">La bibliothèque [msgpack5](https://github.com/mcollina/msgpack5) utilisée par le SignalR client JavaScript ne prend pas en charge le `timestamp96` type dans MessagePack.</span><span class="sxs-lookup"><span data-stu-id="35417-330">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="35417-331">Ce type est utilisé pour encoder les valeurs de date très volumineuses (soit très tôt dans le passé, soit très loin dans le futur).</span><span class="sxs-lookup"><span data-stu-id="35417-331">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="35417-332">La valeur de `DateTime.MinValue` est `January 1, 0001` qui doit être encodée dans une `timestamp96` valeur.</span><span class="sxs-lookup"><span data-stu-id="35417-332">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="35417-333">Pour cette raison, l’envoi `DateTime.MinValue` à un client JavaScript n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="35417-333">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="35417-334">Lorsque `DateTime.MinValue` est reçu par le client JavaScript, l’erreur suivante est levée :</span><span class="sxs-lookup"><span data-stu-id="35417-334">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="35417-335">Habituellement, `DateTime.MinValue` est utilisé pour encoder une valeur « manquante » ou `null` .</span><span class="sxs-lookup"><span data-stu-id="35417-335">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="35417-336">Si vous devez encoder cette valeur dans MessagePack, utilisez une `DateTime` valeur Nullable ( `DateTime?` ) ou encodez une `bool` valeur distincte indiquant si la date est présente.</span><span class="sxs-lookup"><span data-stu-id="35417-336">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="35417-337">Pour plus d’informations sur cette limitation, consultez GitHub issue [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="35417-337">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="35417-338">Prise en charge de MessagePack dans l’environnement de compilation « à l’avance »</span><span class="sxs-lookup"><span data-stu-id="35417-338">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="35417-339">La bibliothèque [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) utilisée par le client et le serveur .NET utilise la génération de code pour optimiser la sérialisation.</span><span class="sxs-lookup"><span data-stu-id="35417-339">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="35417-340">Par conséquent, il n’est pas pris en charge par défaut dans les environnements qui utilisent la compilation « à l’avance » (par exemple, Xamarin iOS ou Unity).</span><span class="sxs-lookup"><span data-stu-id="35417-340">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="35417-341">Il est possible d’utiliser MessagePack dans ces environnements en prégénérant le code de sérialiseur/désérialiseur.</span><span class="sxs-lookup"><span data-stu-id="35417-341">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="35417-342">Pour plus d’informations, consultez [la documentation MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="35417-342">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="35417-343">Une fois que vous avez généré le prétraitement des sérialiseurs, vous pouvez les inscrire à l’aide du délégué de configuration passé à `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="35417-343">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="35417-344">Les vérifications de type sont plus strictes dans MessagePack</span><span class="sxs-lookup"><span data-stu-id="35417-344">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="35417-345">Le protocole JSON Hub effectue des conversions de type pendant la désérialisation.</span><span class="sxs-lookup"><span data-stu-id="35417-345">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="35417-346">Par exemple, si l’objet entrant a une valeur de propriété qui est un nombre ( `{ foo: 42 }` ) mais que la propriété de la classe .net est de type `string` , la valeur est convertie.</span><span class="sxs-lookup"><span data-stu-id="35417-346">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="35417-347">Toutefois, MessagePack n’effectue pas cette conversion et lèvera une exception qui est visible dans les journaux côté serveur (et dans la console) :</span><span class="sxs-lookup"><span data-stu-id="35417-347">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="35417-348">Pour plus d’informations sur cette limitation, consultez GitHub issue [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="35417-348">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="35417-349">Ressources associées</span><span class="sxs-lookup"><span data-stu-id="35417-349">Related resources</span></span>

* [<span data-ttu-id="35417-350">Bien démarrer</span><span class="sxs-lookup"><span data-stu-id="35417-350">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="35417-351">Client .NET</span><span class="sxs-lookup"><span data-stu-id="35417-351">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="35417-352">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="35417-352">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
