---
title: 'Utiliser le protocole MessagePack Hub dans SignalR pour ASP.net Core'
author: bradygaster
description: 'Ajoutez le protocole MessagePack Hub à ASP.NET Core SignalR .'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/24/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: e7d19a42e48048d2be4b87d6b0ac1ba6b2596ff1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058166"
---
# <a name="use-messagepack-hub-protocol-in-no-locsignalr-for-aspnet-core"></a><span data-ttu-id="0b27c-103">Utiliser le protocole MessagePack Hub dans SignalR pour ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="0b27c-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="0b27c-104">Cet article suppose que le lecteur est familiarisé avec les sujets abordés dans la rubrique [prise en main](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="0b27c-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="0b27c-105">Qu’est-ce que MessagePack ?</span><span class="sxs-lookup"><span data-stu-id="0b27c-105">What is MessagePack?</span></span>

<span data-ttu-id="0b27c-106">[MessagePack](https://msgpack.org/index.html) est un format de sérialisation binaire rapide et compact.</span><span class="sxs-lookup"><span data-stu-id="0b27c-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="0b27c-107">Elle est utile lorsque les performances et la bande passante sont un problème, car elle crée des messages plus petits par rapport à [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="0b27c-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="0b27c-108">Les messages binaires sont illisibles lors de la consultation des journaux et des suivis réseau, sauf si les octets sont transmis via un analyseur MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0b27c-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="0b27c-109">SignalR dispose d’une prise en charge intégrée du format MessagePack et fournit des API pour le client et le serveur à utiliser.</span><span class="sxs-lookup"><span data-stu-id="0b27c-109">SignalR has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="0b27c-110">Configurer MessagePack sur le serveur</span><span class="sxs-lookup"><span data-stu-id="0b27c-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="0b27c-111">Pour activer le protocole MessagePack Hub sur le serveur, installez le `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package dans votre application.</span><span class="sxs-lookup"><span data-stu-id="0b27c-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="0b27c-112">Dans la `Startup.ConfigureServices` méthode, ajoutez `AddMessagePackProtocol` à l' `AddSignalR` appel pour activer la prise en charge de MessagePack sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="0b27c-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="0b27c-113">JSON est activé par défaut.</span><span class="sxs-lookup"><span data-stu-id="0b27c-113">JSON is enabled by default.</span></span> <span data-ttu-id="0b27c-114">L’ajout de MessagePack permet la prise en charge des clients JSON et MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0b27c-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="0b27c-115">Pour personnaliser la façon dont MessagePack met en forme vos données, `AddMessagePackProtocol` utilise un délégué pour configurer les options.</span><span class="sxs-lookup"><span data-stu-id="0b27c-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="0b27c-116">Dans ce délégué, la `SerializerOptions` propriété peut être utilisée pour configurer les options de sérialisation MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0b27c-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="0b27c-117">Pour plus d’informations sur le fonctionnement des programmes de résolution, consultez la bibliothèque MessagePack sur [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="0b27c-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="0b27c-118">Les attributs peuvent être utilisés sur les objets que vous souhaitez sérialiser pour définir la manière dont ils doivent être gérés.</span><span class="sxs-lookup"><span data-stu-id="0b27c-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="0b27c-119">Nous vous recommandons vivement de consulter la [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) et d’appliquer les correctifs recommandés.</span><span class="sxs-lookup"><span data-stu-id="0b27c-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="0b27c-120">Par exemple, appeler `.WithSecurity(MessagePackSecurity.UntrustedData)` lors du remplacement de `SerializerOptions` .</span><span class="sxs-lookup"><span data-stu-id="0b27c-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="0b27c-121">Configurer MessagePack sur le client</span><span class="sxs-lookup"><span data-stu-id="0b27c-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="0b27c-122">JSON est activé par défaut pour les clients pris en charge.</span><span class="sxs-lookup"><span data-stu-id="0b27c-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="0b27c-123">Les clients ne peuvent prendre en charge qu’un seul protocole.</span><span class="sxs-lookup"><span data-stu-id="0b27c-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="0b27c-124">L’ajout de la prise en charge de MessagePack remplacera tous les protocoles précédemment configurés.</span><span class="sxs-lookup"><span data-stu-id="0b27c-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="0b27c-125">Client .NET</span><span class="sxs-lookup"><span data-stu-id="0b27c-125">.NET client</span></span>

<span data-ttu-id="0b27c-126">Pour activer MessagePack dans le client .NET, installez le `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package et appelez `AddMessagePackProtocol` sur `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="0b27c-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="0b27c-127">Cet `AddMessagePackProtocol` appel prend un délégué pour configurer les options de la même façon que le serveur.</span><span class="sxs-lookup"><span data-stu-id="0b27c-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="0b27c-128">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="0b27c-128">JavaScript client</span></span>

<span data-ttu-id="0b27c-129">La prise en charge de MessagePack pour le client JavaScript est fournie par le [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) package NPM.</span><span class="sxs-lookup"><span data-stu-id="0b27c-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="0b27c-130">Installez le package en exécutant la commande suivante dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="0b27c-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="0b27c-131">Après l’installation du package NPM, le module peut être utilisé directement via un chargeur de module JavaScript ou importé dans le navigateur en référençant le fichier suivant :</span><span class="sxs-lookup"><span data-stu-id="0b27c-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="0b27c-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="0b27c-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="0b27c-133">Dans un navigateur, la `msgpack5` bibliothèque doit également être référencée.</span><span class="sxs-lookup"><span data-stu-id="0b27c-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="0b27c-134">Utilisez une `<script>` balise pour créer une référence.</span><span class="sxs-lookup"><span data-stu-id="0b27c-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="0b27c-135">La bibliothèque se trouve sur *node_modules\msgpack5\dist\msgpack5.js* .</span><span class="sxs-lookup"><span data-stu-id="0b27c-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js* .</span></span>

> [!NOTE]
> <span data-ttu-id="0b27c-136">Lors de l’utilisation de l' `<script>` élément, l’ordre est important.</span><span class="sxs-lookup"><span data-stu-id="0b27c-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="0b27c-137">Si *signalr-protocol-msgpack.js* est référencé avant *msgpack5.js* , une erreur se produit lors de la tentative de connexion à MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0b27c-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js* , an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="0b27c-138">*signalr.js* est également requis avant *signalr-protocol-msgpack.js* .</span><span class="sxs-lookup"><span data-stu-id="0b27c-138">*signalr.js* is also required before *signalr-protocol-msgpack.js* .</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="0b27c-139">L’ajout `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` de à l' `HubConnectionBuilder` configure le client pour qu’il utilise le protocole MessagePack lors de la connexion à un serveur.</span><span class="sxs-lookup"><span data-stu-id="0b27c-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="0b27c-140">À ce stade, il n’existe aucune option de configuration pour le protocole MessagePack sur le client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0b27c-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="0b27c-141">MessagePack excentriques</span><span class="sxs-lookup"><span data-stu-id="0b27c-141">MessagePack quirks</span></span>

<span data-ttu-id="0b27c-142">Il existe quelques problèmes à connaître lors de l’utilisation du protocole MessagePack Hub.</span><span class="sxs-lookup"><span data-stu-id="0b27c-142">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="0b27c-143">MessagePack respecte la casse</span><span class="sxs-lookup"><span data-stu-id="0b27c-143">MessagePack is case-sensitive</span></span>

<span data-ttu-id="0b27c-144">Le protocole MessagePack respecte la casse.</span><span class="sxs-lookup"><span data-stu-id="0b27c-144">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="0b27c-145">Par exemple, considérez la classe C# suivante :</span><span class="sxs-lookup"><span data-stu-id="0b27c-145">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="0b27c-146">Lors de l’envoi à partir du client JavaScript, vous devez utiliser des `PascalCased` noms de propriété, car la casse doit correspondre exactement à la classe C#.</span><span class="sxs-lookup"><span data-stu-id="0b27c-146">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="0b27c-147">Exemple :</span><span class="sxs-lookup"><span data-stu-id="0b27c-147">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="0b27c-148">L’utilisation `camelCased` de noms n’est pas correctement liée à la classe C#.</span><span class="sxs-lookup"><span data-stu-id="0b27c-148">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="0b27c-149">Vous pouvez contourner ce contournement en utilisant l' `Key` attribut pour spécifier un nom différent pour la propriété MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0b27c-149">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="0b27c-150">Pour plus d’informations, consultez [la documentation MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="0b27c-150">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="0b27c-151">DateTime. Kind n’est pas conservé lors de la sérialisation/désérialisation</span><span class="sxs-lookup"><span data-stu-id="0b27c-151">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="0b27c-152">Le protocole MessagePack ne fournit pas de méthode pour encoder la `Kind` valeur d’un `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="0b27c-152">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="0b27c-153">Par conséquent, lors de la désérialisation d’une date, le protocole MessagePack Hub est converti au format UTC si le `DateTime.Kind` n’est pas le cas, `DateTimeKind.Local` et il ne peut pas toucher l’heure et le passer tel quel.</span><span class="sxs-lookup"><span data-stu-id="0b27c-153">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="0b27c-154">Si vous utilisez des `DateTime` valeurs, nous vous recommandons de convertir au format UTC avant de les envoyer.</span><span class="sxs-lookup"><span data-stu-id="0b27c-154">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="0b27c-155">Convertissez-les de l’heure UTC en heure locale lorsque vous les recevez.</span><span class="sxs-lookup"><span data-stu-id="0b27c-155">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="0b27c-156">DateTime. MinValue n’est pas pris en charge par MessagePack dans JavaScript</span><span class="sxs-lookup"><span data-stu-id="0b27c-156">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="0b27c-157">La bibliothèque [msgpack5](https://github.com/mcollina/msgpack5) utilisée par le SignalR client JavaScript ne prend pas en charge le `timestamp96` type dans MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0b27c-157">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="0b27c-158">Ce type est utilisé pour encoder les valeurs de date très volumineuses (soit très tôt dans le passé, soit très loin dans le futur).</span><span class="sxs-lookup"><span data-stu-id="0b27c-158">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="0b27c-159">La valeur de `DateTime.MinValue` est `January 1, 0001` , qui doit être encodée dans une `timestamp96` valeur.</span><span class="sxs-lookup"><span data-stu-id="0b27c-159">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="0b27c-160">Pour cette raison, l’envoi `DateTime.MinValue` à un client JavaScript n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="0b27c-160">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="0b27c-161">Lorsque `DateTime.MinValue` est reçu par le client JavaScript, l’erreur suivante est levée :</span><span class="sxs-lookup"><span data-stu-id="0b27c-161">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="0b27c-162">Habituellement, `DateTime.MinValue` est utilisé pour encoder une valeur « manquante » ou `null` .</span><span class="sxs-lookup"><span data-stu-id="0b27c-162">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="0b27c-163">Si vous devez encoder cette valeur dans MessagePack, utilisez une `DateTime` valeur Nullable ( `DateTime?` ) ou encodez une `bool` valeur distincte indiquant si la date est présente.</span><span class="sxs-lookup"><span data-stu-id="0b27c-163">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="0b27c-164">Pour plus d’informations sur cette limitation, consultez GitHub issue [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="0b27c-164">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="0b27c-165">Prise en charge de MessagePack dans l’environnement de compilation « à l’avance »</span><span class="sxs-lookup"><span data-stu-id="0b27c-165">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="0b27c-166">La bibliothèque [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) utilisée par le client et le serveur .NET utilise la génération de code pour optimiser la sérialisation.</span><span class="sxs-lookup"><span data-stu-id="0b27c-166">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="0b27c-167">Par conséquent, il n’est pas pris en charge par défaut dans les environnements qui utilisent la compilation « à l’avance » (par exemple, Xamarin iOS ou Unity).</span><span class="sxs-lookup"><span data-stu-id="0b27c-167">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="0b27c-168">Il est possible d’utiliser MessagePack dans ces environnements en prégénérant le code de sérialiseur/désérialiseur.</span><span class="sxs-lookup"><span data-stu-id="0b27c-168">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="0b27c-169">Pour plus d’informations, consultez [la documentation MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span><span class="sxs-lookup"><span data-stu-id="0b27c-169">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="0b27c-170">Une fois que vous avez généré le prétraitement des sérialiseurs, vous pouvez les inscrire à l’aide du délégué de configuration passé à `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="0b27c-170">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="0b27c-171">Les vérifications de type sont plus strictes dans MessagePack</span><span class="sxs-lookup"><span data-stu-id="0b27c-171">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="0b27c-172">Le protocole JSON Hub effectue des conversions de type pendant la désérialisation.</span><span class="sxs-lookup"><span data-stu-id="0b27c-172">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="0b27c-173">Par exemple, si l’objet entrant a une valeur de propriété qui est un nombre ( `{ foo: 42 }` ) mais que la propriété de la classe .net est de type `string` , la valeur est convertie.</span><span class="sxs-lookup"><span data-stu-id="0b27c-173">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="0b27c-174">Toutefois, MessagePack n’effectue pas cette conversion et lèvera une exception qui est visible dans les journaux côté serveur (et dans la console) :</span><span class="sxs-lookup"><span data-stu-id="0b27c-174">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="0b27c-175">Pour plus d’informations sur cette limitation, consultez GitHub issue [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="0b27c-175">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="0b27c-176">Ressources associées</span><span class="sxs-lookup"><span data-stu-id="0b27c-176">Related resources</span></span>

* [<span data-ttu-id="0b27c-177">Bien démarrer</span><span class="sxs-lookup"><span data-stu-id="0b27c-177">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="0b27c-178">Client .NET</span><span class="sxs-lookup"><span data-stu-id="0b27c-178">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="0b27c-179">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="0b27c-179">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="0b27c-180">Cet article suppose que le lecteur est familiarisé avec les sujets abordés dans la rubrique [prise en main](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="0b27c-180">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="0b27c-181">Qu’est-ce que MessagePack ?</span><span class="sxs-lookup"><span data-stu-id="0b27c-181">What is MessagePack?</span></span>

<span data-ttu-id="0b27c-182">[MessagePack](https://msgpack.org/index.html) est un format de sérialisation binaire rapide et compact.</span><span class="sxs-lookup"><span data-stu-id="0b27c-182">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="0b27c-183">Elle est utile lorsque les performances et la bande passante sont un problème, car elle crée des messages plus petits par rapport à [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="0b27c-183">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="0b27c-184">Les messages binaires sont illisibles lors de la consultation des journaux et des suivis réseau, sauf si les octets sont transmis via un analyseur MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0b27c-184">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="0b27c-185">SignalR dispose d’une prise en charge intégrée du format MessagePack et fournit des API que le client et le serveur doivent utiliser.</span><span class="sxs-lookup"><span data-stu-id="0b27c-185">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="0b27c-186">Configurer MessagePack sur le serveur</span><span class="sxs-lookup"><span data-stu-id="0b27c-186">Configure MessagePack on the server</span></span>

<span data-ttu-id="0b27c-187">Pour activer le protocole MessagePack Hub sur le serveur, installez le `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package dans votre application.</span><span class="sxs-lookup"><span data-stu-id="0b27c-187">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="0b27c-188">Dans la `Startup.ConfigureServices` méthode, ajoutez `AddMessagePackProtocol` à l' `AddSignalR` appel pour activer la prise en charge de MessagePack sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="0b27c-188">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="0b27c-189">JSON est activé par défaut.</span><span class="sxs-lookup"><span data-stu-id="0b27c-189">JSON is enabled by default.</span></span> <span data-ttu-id="0b27c-190">L’ajout de MessagePack permet la prise en charge des clients JSON et MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0b27c-190">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="0b27c-191">Pour personnaliser la façon dont MessagePack met en forme vos données, `AddMessagePackProtocol` utilise un délégué pour configurer les options.</span><span class="sxs-lookup"><span data-stu-id="0b27c-191">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="0b27c-192">Dans ce délégué, la `FormatterResolvers` propriété peut être utilisée pour configurer les options de sérialisation MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0b27c-192">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="0b27c-193">Pour plus d’informations sur le fonctionnement des programmes de résolution, consultez la bibliothèque MessagePack sur [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="0b27c-193">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="0b27c-194">Les attributs peuvent être utilisés sur les objets que vous souhaitez sérialiser pour définir la manière dont ils doivent être gérés.</span><span class="sxs-lookup"><span data-stu-id="0b27c-194">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="0b27c-195">Nous vous recommandons vivement de consulter la [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) et d’appliquer les correctifs recommandés.</span><span class="sxs-lookup"><span data-stu-id="0b27c-195">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="0b27c-196">Par exemple, en affectant `MessagePackSecurity.Active` à la propriété statique la valeur `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="0b27c-196">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="0b27c-197">La définition `MessagePackSecurity.Active` de requiert l’installation manuelle [d’une version 1.9. x de MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="0b27c-197">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="0b27c-198">`MessagePack`L’installation de la version 1.9. x met à niveau la version SignalR .</span><span class="sxs-lookup"><span data-stu-id="0b27c-198">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="0b27c-199">`MessagePack` la version 2. x introduit des modifications avec rupture et est incompatible avec les SignalR versions 3,1 et antérieures.</span><span class="sxs-lookup"><span data-stu-id="0b27c-199">`MessagePack` version 2.x introduced breaking changes and is incompatible with SignalR versions 3.1 and earlier.</span></span> <span data-ttu-id="0b27c-200">Lorsque `MessagePackSecurity.Active` n’a pas `MessagePackSecurity.UntrustedData` la valeur, un client malveillant peut provoquer un déni de service.</span><span class="sxs-lookup"><span data-stu-id="0b27c-200">When `MessagePackSecurity.Active` isn't set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="0b27c-201">Définissez `MessagePackSecurity.Active` dans `Program.Main` , comme illustré dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="0b27c-201">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="0b27c-202">Configurer MessagePack sur le client</span><span class="sxs-lookup"><span data-stu-id="0b27c-202">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="0b27c-203">JSON est activé par défaut pour les clients pris en charge.</span><span class="sxs-lookup"><span data-stu-id="0b27c-203">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="0b27c-204">Les clients ne peuvent prendre en charge qu’un seul protocole.</span><span class="sxs-lookup"><span data-stu-id="0b27c-204">Clients can only support a single protocol.</span></span> <span data-ttu-id="0b27c-205">L’ajout de la prise en charge de MessagePack remplacera tous les protocoles précédemment configurés.</span><span class="sxs-lookup"><span data-stu-id="0b27c-205">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="0b27c-206">Client .NET</span><span class="sxs-lookup"><span data-stu-id="0b27c-206">.NET client</span></span>

<span data-ttu-id="0b27c-207">Pour activer MessagePack dans le client .NET, installez le `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package et appelez `AddMessagePackProtocol` sur `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="0b27c-207">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="0b27c-208">Cet `AddMessagePackProtocol` appel prend un délégué pour configurer les options de la même façon que le serveur.</span><span class="sxs-lookup"><span data-stu-id="0b27c-208">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="0b27c-209">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="0b27c-209">JavaScript client</span></span>

<span data-ttu-id="0b27c-210">La prise en charge de MessagePack pour le client JavaScript est fournie par le [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) package NPM.</span><span class="sxs-lookup"><span data-stu-id="0b27c-210">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="0b27c-211">Installez le package en exécutant la commande suivante dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="0b27c-211">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="0b27c-212">Après l’installation du package NPM, le module peut être utilisé directement via un chargeur de module JavaScript ou importé dans le navigateur en référençant le fichier suivant :</span><span class="sxs-lookup"><span data-stu-id="0b27c-212">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="0b27c-213">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="0b27c-213">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="0b27c-214">Dans un navigateur, la `msgpack5` bibliothèque doit également être référencée.</span><span class="sxs-lookup"><span data-stu-id="0b27c-214">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="0b27c-215">Utilisez une `<script>` balise pour créer une référence.</span><span class="sxs-lookup"><span data-stu-id="0b27c-215">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="0b27c-216">La bibliothèque se trouve sur *node_modules\msgpack5\dist\msgpack5.js* .</span><span class="sxs-lookup"><span data-stu-id="0b27c-216">The library can be found at *node_modules\msgpack5\dist\msgpack5.js* .</span></span>

> [!NOTE]
> <span data-ttu-id="0b27c-217">Lors de l’utilisation de l' `<script>` élément, l’ordre est important.</span><span class="sxs-lookup"><span data-stu-id="0b27c-217">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="0b27c-218">Si *signalr-protocol-msgpack.js* est référencé avant *msgpack5.js* , une erreur se produit lors de la tentative de connexion à MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0b27c-218">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js* , an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="0b27c-219">*signalr.js* est également requis avant *signalr-protocol-msgpack.js* .</span><span class="sxs-lookup"><span data-stu-id="0b27c-219">*signalr.js* is also required before *signalr-protocol-msgpack.js* .</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="0b27c-220">L’ajout `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` de à l' `HubConnectionBuilder` configure le client pour qu’il utilise le protocole MessagePack lors de la connexion à un serveur.</span><span class="sxs-lookup"><span data-stu-id="0b27c-220">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="0b27c-221">À ce stade, il n’existe aucune option de configuration pour le protocole MessagePack sur le client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0b27c-221">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="0b27c-222">MessagePack excentriques</span><span class="sxs-lookup"><span data-stu-id="0b27c-222">MessagePack quirks</span></span>

<span data-ttu-id="0b27c-223">Il existe quelques problèmes à connaître lors de l’utilisation du protocole MessagePack Hub.</span><span class="sxs-lookup"><span data-stu-id="0b27c-223">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="0b27c-224">MessagePack respecte la casse</span><span class="sxs-lookup"><span data-stu-id="0b27c-224">MessagePack is case-sensitive</span></span>

<span data-ttu-id="0b27c-225">Le protocole MessagePack respecte la casse.</span><span class="sxs-lookup"><span data-stu-id="0b27c-225">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="0b27c-226">Par exemple, considérez la classe C# suivante :</span><span class="sxs-lookup"><span data-stu-id="0b27c-226">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="0b27c-227">Lors de l’envoi à partir du client JavaScript, vous devez utiliser des `PascalCased` noms de propriété, car la casse doit correspondre exactement à la classe C#.</span><span class="sxs-lookup"><span data-stu-id="0b27c-227">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="0b27c-228">Exemple :</span><span class="sxs-lookup"><span data-stu-id="0b27c-228">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="0b27c-229">L’utilisation `camelCased` de noms n’est pas correctement liée à la classe C#.</span><span class="sxs-lookup"><span data-stu-id="0b27c-229">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="0b27c-230">Vous pouvez contourner ce contournement en utilisant l' `Key` attribut pour spécifier un nom différent pour la propriété MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0b27c-230">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="0b27c-231">Pour plus d’informations, consultez [la documentation MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="0b27c-231">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="0b27c-232">DateTime. Kind n’est pas conservé lors de la sérialisation/désérialisation</span><span class="sxs-lookup"><span data-stu-id="0b27c-232">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="0b27c-233">Le protocole MessagePack ne fournit pas de méthode pour encoder la `Kind` valeur d’un `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="0b27c-233">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="0b27c-234">Par conséquent, lors de la désérialisation d’une date, le protocole MessagePack Hub part du principe que la date d’entrée est au format UTC.</span><span class="sxs-lookup"><span data-stu-id="0b27c-234">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="0b27c-235">Si vous utilisez des `DateTime` valeurs en heure locale, nous vous recommandons de convertir au format UTC avant de les envoyer.</span><span class="sxs-lookup"><span data-stu-id="0b27c-235">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="0b27c-236">Convertissez-les de l’heure UTC en heure locale lorsque vous les recevez.</span><span class="sxs-lookup"><span data-stu-id="0b27c-236">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="0b27c-237">Pour plus d’informations sur cette limitation, consultez GitHub issue [ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="0b27c-237">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="0b27c-238">DateTime. MinValue n’est pas pris en charge par MessagePack dans JavaScript</span><span class="sxs-lookup"><span data-stu-id="0b27c-238">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="0b27c-239">La bibliothèque [msgpack5](https://github.com/mcollina/msgpack5) utilisée par le SignalR client JavaScript ne prend pas en charge le `timestamp96` type dans MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0b27c-239">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="0b27c-240">Ce type est utilisé pour encoder les valeurs de date très volumineuses (soit très tôt dans le passé, soit très loin dans le futur).</span><span class="sxs-lookup"><span data-stu-id="0b27c-240">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="0b27c-241">La valeur de `DateTime.MinValue` est `January 1, 0001` , qui doit être encodée dans une `timestamp96` valeur.</span><span class="sxs-lookup"><span data-stu-id="0b27c-241">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="0b27c-242">Pour cette raison, l’envoi `DateTime.MinValue` à un client JavaScript n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="0b27c-242">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="0b27c-243">Lorsque `DateTime.MinValue` est reçu par le client JavaScript, l’erreur suivante est levée :</span><span class="sxs-lookup"><span data-stu-id="0b27c-243">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="0b27c-244">Habituellement, `DateTime.MinValue` est utilisé pour encoder une valeur « manquante » ou `null` .</span><span class="sxs-lookup"><span data-stu-id="0b27c-244">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="0b27c-245">Si vous devez encoder cette valeur dans MessagePack, utilisez une `DateTime` valeur Nullable ( `DateTime?` ) ou encodez une `bool` valeur distincte indiquant si la date est présente.</span><span class="sxs-lookup"><span data-stu-id="0b27c-245">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="0b27c-246">Pour plus d’informations sur cette limitation, consultez GitHub issue [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="0b27c-246">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="0b27c-247">Prise en charge de MessagePack dans l’environnement de compilation « à l’avance »</span><span class="sxs-lookup"><span data-stu-id="0b27c-247">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="0b27c-248">La bibliothèque [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) utilisée par le client et le serveur .NET utilise la génération de code pour optimiser la sérialisation.</span><span class="sxs-lookup"><span data-stu-id="0b27c-248">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="0b27c-249">Par conséquent, il n’est pas pris en charge par défaut dans les environnements qui utilisent la compilation « à l’avance » (par exemple, Xamarin iOS ou Unity).</span><span class="sxs-lookup"><span data-stu-id="0b27c-249">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="0b27c-250">Il est possible d’utiliser MessagePack dans ces environnements en prégénérant le code de sérialiseur/désérialiseur.</span><span class="sxs-lookup"><span data-stu-id="0b27c-250">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="0b27c-251">Pour plus d’informations, consultez [la documentation MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="0b27c-251">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="0b27c-252">Une fois que vous avez généré le prétraitement des sérialiseurs, vous pouvez les inscrire à l’aide du délégué de configuration passé à `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="0b27c-252">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="0b27c-253">Les vérifications de type sont plus strictes dans MessagePack</span><span class="sxs-lookup"><span data-stu-id="0b27c-253">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="0b27c-254">Le protocole JSON Hub effectue des conversions de type pendant la désérialisation.</span><span class="sxs-lookup"><span data-stu-id="0b27c-254">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="0b27c-255">Par exemple, si l’objet entrant a une valeur de propriété qui est un nombre ( `{ foo: 42 }` ) mais que la propriété de la classe .net est de type `string` , la valeur est convertie.</span><span class="sxs-lookup"><span data-stu-id="0b27c-255">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="0b27c-256">Toutefois, MessagePack n’effectue pas cette conversion et lèvera une exception qui est visible dans les journaux côté serveur (et dans la console) :</span><span class="sxs-lookup"><span data-stu-id="0b27c-256">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="0b27c-257">Pour plus d’informations sur cette limitation, consultez GitHub issue [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="0b27c-257">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="0b27c-258">Ressources associées</span><span class="sxs-lookup"><span data-stu-id="0b27c-258">Related resources</span></span>

* [<span data-ttu-id="0b27c-259">Bien démarrer</span><span class="sxs-lookup"><span data-stu-id="0b27c-259">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="0b27c-260">Client .NET</span><span class="sxs-lookup"><span data-stu-id="0b27c-260">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="0b27c-261">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="0b27c-261">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0b27c-262">Cet article suppose que le lecteur est familiarisé avec les sujets abordés dans la rubrique [prise en main](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="0b27c-262">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="0b27c-263">Qu’est-ce que MessagePack ?</span><span class="sxs-lookup"><span data-stu-id="0b27c-263">What is MessagePack?</span></span>

<span data-ttu-id="0b27c-264">[MessagePack](https://msgpack.org/index.html) est un format de sérialisation binaire rapide et compact.</span><span class="sxs-lookup"><span data-stu-id="0b27c-264">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="0b27c-265">Elle est utile lorsque les performances et la bande passante sont un problème, car elle crée des messages plus petits par rapport à [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="0b27c-265">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="0b27c-266">Les messages binaires sont illisibles lors de la consultation des journaux et des suivis réseau, sauf si les octets sont transmis via un analyseur MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0b27c-266">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="0b27c-267">SignalR dispose d’une prise en charge intégrée du format MessagePack et fournit des API que le client et le serveur doivent utiliser.</span><span class="sxs-lookup"><span data-stu-id="0b27c-267">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="0b27c-268">Configurer MessagePack sur le serveur</span><span class="sxs-lookup"><span data-stu-id="0b27c-268">Configure MessagePack on the server</span></span>

<span data-ttu-id="0b27c-269">Pour activer le protocole MessagePack Hub sur le serveur, installez le `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package dans votre application.</span><span class="sxs-lookup"><span data-stu-id="0b27c-269">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="0b27c-270">Dans la `Startup.ConfigureServices` méthode, ajoutez `AddMessagePackProtocol` à l' `AddSignalR` appel pour activer la prise en charge de MessagePack sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="0b27c-270">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="0b27c-271">JSON est activé par défaut.</span><span class="sxs-lookup"><span data-stu-id="0b27c-271">JSON is enabled by default.</span></span> <span data-ttu-id="0b27c-272">L’ajout de MessagePack permet la prise en charge des clients JSON et MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0b27c-272">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="0b27c-273">Pour personnaliser la façon dont MessagePack met en forme vos données, `AddMessagePackProtocol` utilise un délégué pour configurer les options.</span><span class="sxs-lookup"><span data-stu-id="0b27c-273">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="0b27c-274">Dans ce délégué, la `FormatterResolvers` propriété peut être utilisée pour configurer les options de sérialisation MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0b27c-274">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="0b27c-275">Pour plus d’informations sur le fonctionnement des programmes de résolution, consultez la bibliothèque MessagePack sur [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="0b27c-275">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="0b27c-276">Les attributs peuvent être utilisés sur les objets que vous souhaitez sérialiser pour définir la manière dont ils doivent être gérés.</span><span class="sxs-lookup"><span data-stu-id="0b27c-276">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="0b27c-277">Nous vous recommandons vivement de consulter la [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) et d’appliquer les correctifs recommandés.</span><span class="sxs-lookup"><span data-stu-id="0b27c-277">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="0b27c-278">Par exemple, en affectant `MessagePackSecurity.Active` à la propriété statique la valeur `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="0b27c-278">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="0b27c-279">La définition `MessagePackSecurity.Active` de requiert l’installation manuelle [d’une version 1.9. x de MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="0b27c-279">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="0b27c-280">`MessagePack`L’installation de la version 1.9. x met à niveau la version SignalR .</span><span class="sxs-lookup"><span data-stu-id="0b27c-280">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="0b27c-281">Lorsque `MessagePackSecurity.Active` n’a pas `MessagePackSecurity.UntrustedData` la valeur, un client malveillant peut provoquer un déni de service.</span><span class="sxs-lookup"><span data-stu-id="0b27c-281">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="0b27c-282">Définissez `MessagePackSecurity.Active` dans `Program.Main` , comme illustré dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="0b27c-282">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="0b27c-283">Configurer MessagePack sur le client</span><span class="sxs-lookup"><span data-stu-id="0b27c-283">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="0b27c-284">JSON est activé par défaut pour les clients pris en charge.</span><span class="sxs-lookup"><span data-stu-id="0b27c-284">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="0b27c-285">Les clients ne peuvent prendre en charge qu’un seul protocole.</span><span class="sxs-lookup"><span data-stu-id="0b27c-285">Clients can only support a single protocol.</span></span> <span data-ttu-id="0b27c-286">L’ajout de la prise en charge de MessagePack remplacera tous les protocoles précédemment configurés.</span><span class="sxs-lookup"><span data-stu-id="0b27c-286">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="0b27c-287">Client .NET</span><span class="sxs-lookup"><span data-stu-id="0b27c-287">.NET client</span></span>

<span data-ttu-id="0b27c-288">Pour activer MessagePack dans le client .NET, installez le `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package et appelez `AddMessagePackProtocol` sur `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="0b27c-288">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="0b27c-289">Cet `AddMessagePackProtocol` appel prend un délégué pour configurer les options de la même façon que le serveur.</span><span class="sxs-lookup"><span data-stu-id="0b27c-289">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="0b27c-290">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="0b27c-290">JavaScript client</span></span>

<span data-ttu-id="0b27c-291">La prise en charge de MessagePack pour le client JavaScript est fournie par le [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) package NPM.</span><span class="sxs-lookup"><span data-stu-id="0b27c-291">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="0b27c-292">Installez le package en exécutant la commande suivante dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="0b27c-292">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="0b27c-293">Après l’installation du package NPM, le module peut être utilisé directement via un chargeur de module JavaScript ou importé dans le navigateur en référençant le fichier suivant :</span><span class="sxs-lookup"><span data-stu-id="0b27c-293">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="0b27c-294">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="0b27c-294">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="0b27c-295">Dans un navigateur, la `msgpack5` bibliothèque doit également être référencée.</span><span class="sxs-lookup"><span data-stu-id="0b27c-295">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="0b27c-296">Utilisez une `<script>` balise pour créer une référence.</span><span class="sxs-lookup"><span data-stu-id="0b27c-296">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="0b27c-297">La bibliothèque se trouve sur *node_modules\msgpack5\dist\msgpack5.js* .</span><span class="sxs-lookup"><span data-stu-id="0b27c-297">The library can be found at *node_modules\msgpack5\dist\msgpack5.js* .</span></span>

> [!NOTE]
> <span data-ttu-id="0b27c-298">Lors de l’utilisation de l' `<script>` élément, l’ordre est important.</span><span class="sxs-lookup"><span data-stu-id="0b27c-298">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="0b27c-299">Si *signalr-protocol-msgpack.js* est référencé avant *msgpack5.js* , une erreur se produit lors de la tentative de connexion à MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0b27c-299">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js* , an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="0b27c-300">*signalr.js* est également requis avant *signalr-protocol-msgpack.js* .</span><span class="sxs-lookup"><span data-stu-id="0b27c-300">*signalr.js* is also required before *signalr-protocol-msgpack.js* .</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="0b27c-301">L’ajout `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` de à l' `HubConnectionBuilder` configure le client pour qu’il utilise le protocole MessagePack lors de la connexion à un serveur.</span><span class="sxs-lookup"><span data-stu-id="0b27c-301">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="0b27c-302">À ce stade, il n’existe aucune option de configuration pour le protocole MessagePack sur le client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0b27c-302">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="0b27c-303">MessagePack excentriques</span><span class="sxs-lookup"><span data-stu-id="0b27c-303">MessagePack quirks</span></span>

<span data-ttu-id="0b27c-304">Il existe quelques problèmes à connaître lors de l’utilisation du protocole MessagePack Hub.</span><span class="sxs-lookup"><span data-stu-id="0b27c-304">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="0b27c-305">MessagePack respecte la casse</span><span class="sxs-lookup"><span data-stu-id="0b27c-305">MessagePack is case-sensitive</span></span>

<span data-ttu-id="0b27c-306">Le protocole MessagePack respecte la casse.</span><span class="sxs-lookup"><span data-stu-id="0b27c-306">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="0b27c-307">Par exemple, considérez la classe C# suivante :</span><span class="sxs-lookup"><span data-stu-id="0b27c-307">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="0b27c-308">Lors de l’envoi à partir du client JavaScript, vous devez utiliser des `PascalCased` noms de propriété, car la casse doit correspondre exactement à la classe C#.</span><span class="sxs-lookup"><span data-stu-id="0b27c-308">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="0b27c-309">Exemple :</span><span class="sxs-lookup"><span data-stu-id="0b27c-309">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="0b27c-310">L’utilisation `camelCased` de noms n’est pas correctement liée à la classe C#.</span><span class="sxs-lookup"><span data-stu-id="0b27c-310">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="0b27c-311">Vous pouvez contourner ce contournement en utilisant l' `Key` attribut pour spécifier un nom différent pour la propriété MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0b27c-311">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="0b27c-312">Pour plus d’informations, consultez [la documentation MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="0b27c-312">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="0b27c-313">DateTime. Kind n’est pas conservé lors de la sérialisation/désérialisation</span><span class="sxs-lookup"><span data-stu-id="0b27c-313">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="0b27c-314">Le protocole MessagePack ne fournit pas de méthode pour encoder la `Kind` valeur d’un `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="0b27c-314">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="0b27c-315">Par conséquent, lors de la désérialisation d’une date, le protocole MessagePack Hub part du principe que la date d’entrée est au format UTC.</span><span class="sxs-lookup"><span data-stu-id="0b27c-315">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="0b27c-316">Si vous utilisez des `DateTime` valeurs en heure locale, nous vous recommandons de convertir au format UTC avant de les envoyer.</span><span class="sxs-lookup"><span data-stu-id="0b27c-316">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="0b27c-317">Convertissez-les de l’heure UTC en heure locale lorsque vous les recevez.</span><span class="sxs-lookup"><span data-stu-id="0b27c-317">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="0b27c-318">Pour plus d’informations sur cette limitation, consultez GitHub issue [ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="0b27c-318">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="0b27c-319">DateTime. MinValue n’est pas pris en charge par MessagePack dans JavaScript</span><span class="sxs-lookup"><span data-stu-id="0b27c-319">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="0b27c-320">La bibliothèque [msgpack5](https://github.com/mcollina/msgpack5) utilisée par le SignalR client JavaScript ne prend pas en charge le `timestamp96` type dans MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0b27c-320">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="0b27c-321">Ce type est utilisé pour encoder les valeurs de date très volumineuses (soit très tôt dans le passé, soit très loin dans le futur).</span><span class="sxs-lookup"><span data-stu-id="0b27c-321">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="0b27c-322">La valeur de `DateTime.MinValue` est `January 1, 0001` qui doit être encodée dans une `timestamp96` valeur.</span><span class="sxs-lookup"><span data-stu-id="0b27c-322">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="0b27c-323">Pour cette raison, l’envoi `DateTime.MinValue` à un client JavaScript n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="0b27c-323">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="0b27c-324">Lorsque `DateTime.MinValue` est reçu par le client JavaScript, l’erreur suivante est levée :</span><span class="sxs-lookup"><span data-stu-id="0b27c-324">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="0b27c-325">Habituellement, `DateTime.MinValue` est utilisé pour encoder une valeur « manquante » ou `null` .</span><span class="sxs-lookup"><span data-stu-id="0b27c-325">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="0b27c-326">Si vous devez encoder cette valeur dans MessagePack, utilisez une `DateTime` valeur Nullable ( `DateTime?` ) ou encodez une `bool` valeur distincte indiquant si la date est présente.</span><span class="sxs-lookup"><span data-stu-id="0b27c-326">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="0b27c-327">Pour plus d’informations sur cette limitation, consultez GitHub issue [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="0b27c-327">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="0b27c-328">Prise en charge de MessagePack dans l’environnement de compilation « à l’avance »</span><span class="sxs-lookup"><span data-stu-id="0b27c-328">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="0b27c-329">La bibliothèque [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) utilisée par le client et le serveur .NET utilise la génération de code pour optimiser la sérialisation.</span><span class="sxs-lookup"><span data-stu-id="0b27c-329">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="0b27c-330">Par conséquent, il n’est pas pris en charge par défaut dans les environnements qui utilisent la compilation « à l’avance » (par exemple, Xamarin iOS ou Unity).</span><span class="sxs-lookup"><span data-stu-id="0b27c-330">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="0b27c-331">Il est possible d’utiliser MessagePack dans ces environnements en prégénérant le code de sérialiseur/désérialiseur.</span><span class="sxs-lookup"><span data-stu-id="0b27c-331">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="0b27c-332">Pour plus d’informations, consultez [la documentation MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="0b27c-332">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="0b27c-333">Une fois que vous avez généré le prétraitement des sérialiseurs, vous pouvez les inscrire à l’aide du délégué de configuration passé à `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="0b27c-333">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="0b27c-334">Les vérifications de type sont plus strictes dans MessagePack</span><span class="sxs-lookup"><span data-stu-id="0b27c-334">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="0b27c-335">Le protocole JSON Hub effectue des conversions de type pendant la désérialisation.</span><span class="sxs-lookup"><span data-stu-id="0b27c-335">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="0b27c-336">Par exemple, si l’objet entrant a une valeur de propriété qui est un nombre ( `{ foo: 42 }` ) mais que la propriété de la classe .net est de type `string` , la valeur est convertie.</span><span class="sxs-lookup"><span data-stu-id="0b27c-336">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="0b27c-337">Toutefois, MessagePack n’effectue pas cette conversion et lèvera une exception qui est visible dans les journaux côté serveur (et dans la console) :</span><span class="sxs-lookup"><span data-stu-id="0b27c-337">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="0b27c-338">Pour plus d’informations sur cette limitation, consultez GitHub issue [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="0b27c-338">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="0b27c-339">Ressources associées</span><span class="sxs-lookup"><span data-stu-id="0b27c-339">Related resources</span></span>

* [<span data-ttu-id="0b27c-340">Bien démarrer</span><span class="sxs-lookup"><span data-stu-id="0b27c-340">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="0b27c-341">Client .NET</span><span class="sxs-lookup"><span data-stu-id="0b27c-341">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="0b27c-342">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="0b27c-342">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
