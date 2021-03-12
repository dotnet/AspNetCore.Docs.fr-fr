---
title: SignalRClient Java ASP.net Core
author: mikaelm12
description: Découvrez comment utiliser le client ASP.NET Core SignalR java.
monikerRange: '>= aspnetcore-2.2'
ms.author: mimengis
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/java-client
ms.openlocfilehash: bdfaf50895612e739eb5ca068a76755f97cf24c2
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588072"
---
# <a name="aspnet-core-signalr-java-client"></a><span data-ttu-id="a78ed-103">SignalRClient Java ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="a78ed-103">ASP.NET Core SignalR Java client</span></span>

<span data-ttu-id="a78ed-104">Par [Mikael Mengistu](https://twitter.com/MikaelM_12)</span><span class="sxs-lookup"><span data-stu-id="a78ed-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="a78ed-105">Le client Java permet de se connecter à un SignalR serveur ASP.net core à partir de code Java, y compris les applications Android.</span><span class="sxs-lookup"><span data-stu-id="a78ed-105">The Java client enables connecting to an ASP.NET Core SignalR server from Java code, including Android apps.</span></span> <span data-ttu-id="a78ed-106">À l’instar du client [JavaScript](xref:signalr/javascript-client) et du [client .net](xref:signalr/dotnet-client), le client Java vous permet de recevoir et d’envoyer des messages à un hub en temps réel.</span><span class="sxs-lookup"><span data-stu-id="a78ed-106">Like the [JavaScript client](xref:signalr/javascript-client) and the [.NET client](xref:signalr/dotnet-client), the Java client enables you to receive and send messages to a hub in real time.</span></span> <span data-ttu-id="a78ed-107">Le client Java est disponible dans ASP.NET Core 2,2 et versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="a78ed-107">The Java client is available in ASP.NET Core 2.2 and later.</span></span>

<span data-ttu-id="a78ed-108">L’exemple d’application de console Java référencé dans cet article utilise le SignalR client Java.</span><span class="sxs-lookup"><span data-stu-id="a78ed-108">The sample Java console app referenced in this article uses the SignalR Java client.</span></span>

<span data-ttu-id="a78ed-109">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/java-client/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a78ed-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/java-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-signalr-java-client-package"></a><span data-ttu-id="a78ed-110">Installer le SignalR package client Java</span><span class="sxs-lookup"><span data-stu-id="a78ed-110">Install the SignalR Java client package</span></span>

<span data-ttu-id="a78ed-111">Le fichier jar *signalr-1.0.0* permet aux clients de se connecter aux SignalR hubs.</span><span class="sxs-lookup"><span data-stu-id="a78ed-111">The *signalr-1.0.0* JAR file allows clients to connect to SignalR hubs.</span></span> <span data-ttu-id="a78ed-112">Pour rechercher le dernier numéro de version du fichier JAR, consultez les résultats de la [recherche Maven](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).</span><span class="sxs-lookup"><span data-stu-id="a78ed-112">To find the latest JAR file version number, see the [Maven search results](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).</span></span>

<span data-ttu-id="a78ed-113">Si vous utilisez Gradle, ajoutez la ligne suivante à la `dependencies` section de votre fichier *Build. Gradle* :</span><span class="sxs-lookup"><span data-stu-id="a78ed-113">If using Gradle, add the following line to the `dependencies` section of your *build.gradle* file:</span></span>

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0'
```

<span data-ttu-id="a78ed-114">Si vous utilisez Maven, ajoutez les lignes suivantes à l’intérieur `<dependencies>` de l’élément de votre fichier *pom.xml* :</span><span class="sxs-lookup"><span data-stu-id="a78ed-114">If using Maven, add the following lines inside the `<dependencies>` element of your *pom.xml* file:</span></span>

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a><span data-ttu-id="a78ed-115">Se connecter à un concentrateur</span><span class="sxs-lookup"><span data-stu-id="a78ed-115">Connect to a hub</span></span>

<span data-ttu-id="a78ed-116">Pour établir un `HubConnection` , le `HubConnectionBuilder` doit être utilisé.</span><span class="sxs-lookup"><span data-stu-id="a78ed-116">To establish a `HubConnection`, the `HubConnectionBuilder` should be used.</span></span> <span data-ttu-id="a78ed-117">L’URL du Hub et le niveau de journalisation peuvent être configurés lors de la création d’une connexion.</span><span class="sxs-lookup"><span data-stu-id="a78ed-117">The hub URL and log level can be configured while building a connection.</span></span> <span data-ttu-id="a78ed-118">Configurez toutes les options requises en appelant l’une des `HubConnectionBuilder` méthodes antérieures à `build` .</span><span class="sxs-lookup"><span data-stu-id="a78ed-118">Configure any required options by calling any of the `HubConnectionBuilder` methods before `build`.</span></span> <span data-ttu-id="a78ed-119">Démarrez la connexion avec `start` .</span><span class="sxs-lookup"><span data-stu-id="a78ed-119">Start the connection with `start`.</span></span>

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="a78ed-120">Appeler les méthodes de concentrateur à partir du client</span><span class="sxs-lookup"><span data-stu-id="a78ed-120">Call hub methods from client</span></span>

<span data-ttu-id="a78ed-121">Un appel à `send` appelle une méthode de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="a78ed-121">A call to `send` invokes a hub method.</span></span> <span data-ttu-id="a78ed-122">Transmettez le nom de la méthode de concentrateur et les arguments définis dans la méthode de concentrateur à `send` .</span><span class="sxs-lookup"><span data-stu-id="a78ed-122">Pass the hub method name and any arguments defined in the hub method to `send`.</span></span>

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

> [!NOTE]
> <span data-ttu-id="a78ed-123">L’appel de méthodes de Hub à partir d’un client est pris en charge uniquement lors de l’utilisation du SignalR service Azure en mode *par défaut* .</span><span class="sxs-lookup"><span data-stu-id="a78ed-123">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="a78ed-124">Pour plus d’informations, consultez [Forum aux questions (référentiel GitHub Azure-signalr)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span><span class="sxs-lookup"><span data-stu-id="a78ed-124">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="a78ed-125">Appeler des méthodes clientes à partir du Hub</span><span class="sxs-lookup"><span data-stu-id="a78ed-125">Call client methods from hub</span></span>

<span data-ttu-id="a78ed-126">Utilisez `hubConnection.on` pour définir des méthodes sur le client que le hub peut appeler.</span><span class="sxs-lookup"><span data-stu-id="a78ed-126">Use `hubConnection.on` to define methods on the client that the hub can call.</span></span> <span data-ttu-id="a78ed-127">Définissez les méthodes après la génération mais avant de commencer la connexion.</span><span class="sxs-lookup"><span data-stu-id="a78ed-127">Define the methods after building but before starting the connection.</span></span>

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a><span data-ttu-id="a78ed-128">Ajouter une journalisation</span><span class="sxs-lookup"><span data-stu-id="a78ed-128">Add logging</span></span>

<span data-ttu-id="a78ed-129">Le SignalR client Java utilise la bibliothèque [SLF4J](https://www.slf4j.org/) pour la journalisation.</span><span class="sxs-lookup"><span data-stu-id="a78ed-129">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="a78ed-130">Il s’agit d’une API de journalisation de haut niveau qui permet aux utilisateurs de la bibliothèque de choisir leur propre implémentation de journalisation spécifique en introduisant une dépendance de journalisation spécifique.</span><span class="sxs-lookup"><span data-stu-id="a78ed-130">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="a78ed-131">L’extrait de code suivant montre comment utiliser `java.util.logging` avec le SignalR client Java.</span><span class="sxs-lookup"><span data-stu-id="a78ed-131">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="a78ed-132">Si vous ne configurez pas la journalisation dans vos dépendances, SLF4J charge un journal de non-opération par défaut avec le message d’avertissement suivant :</span><span class="sxs-lookup"><span data-stu-id="a78ed-132">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="a78ed-133">Cela peut être ignoré en toute sécurité.</span><span class="sxs-lookup"><span data-stu-id="a78ed-133">This can safely be ignored.</span></span>

## <a name="android-development-notes"></a><span data-ttu-id="a78ed-134">Remarques sur le développement Android</span><span class="sxs-lookup"><span data-stu-id="a78ed-134">Android development notes</span></span>

<span data-ttu-id="a78ed-135">En ce qui concerne la Android SDK compatibilité pour les SignalR fonctionnalités clientes, tenez compte des éléments suivants lorsque vous spécifiez votre version de Android SDK cible :</span><span class="sxs-lookup"><span data-stu-id="a78ed-135">With regards to Android SDK compatibility for the SignalR client features, consider the following items when specifying your target Android SDK version:</span></span>

* <span data-ttu-id="a78ed-136">Le SignalR client Java s’exécutera sur l’API Android de niveau 16 et versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="a78ed-136">The SignalR Java Client will run on Android API Level 16 and later.</span></span>
* <span data-ttu-id="a78ed-137">La connexion via le SignalR service Azure nécessite le niveau d’API Android 20 et versions ultérieures, car le [ SignalR service azure](/azure/azure-signalr/signalr-overview) requiert TLS 1,2 et ne prend pas en charge les suites de chiffrement SHA-1.</span><span class="sxs-lookup"><span data-stu-id="a78ed-137">Connecting through the Azure SignalR Service will require Android API Level 20 and later because the [Azure SignalR Service](/azure/azure-signalr/signalr-overview) requires TLS 1.2 and doesn't support SHA-1-based cipher suites.</span></span> <span data-ttu-id="a78ed-138">Android a [ajouté la prise en charge des suites de chiffrement SHA-256 (et versions ultérieures)](https://developer.android.com/reference/javax/net/ssl/SSLSocket) au niveau de l’API 20.</span><span class="sxs-lookup"><span data-stu-id="a78ed-138">Android [added support for SHA-256 (and above) cipher suites](https://developer.android.com/reference/javax/net/ssl/SSLSocket) in API Level 20.</span></span>

## <a name="configure-bearer-token-authentication"></a><span data-ttu-id="a78ed-139">Configurer l’authentification du jeton du porteur</span><span class="sxs-lookup"><span data-stu-id="a78ed-139">Configure bearer token authentication</span></span>

<span data-ttu-id="a78ed-140">Dans le SignalR client Java, vous pouvez configurer un jeton de porteur à utiliser pour l’authentification en fournissant une « fabrique de jetons d’accès » au [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="a78ed-140">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an "access token factory" to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="a78ed-141">Utilisez [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) pour fournir un [RxJava](https://github.com/ReactiveX/RxJava) [unique \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="a78ed-141">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="a78ed-142">Avec un appel à [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), vous pouvez écrire une logique pour produire des jetons d’accès pour votre client.</span><span class="sxs-lookup"><span data-stu-id="a78ed-142">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

::: moniker range=">= aspnetcore-5.0"

### <a name="passing-class-information-in-java"></a><span data-ttu-id="a78ed-143">Transmission d’informations de classe en Java</span><span class="sxs-lookup"><span data-stu-id="a78ed-143">Passing Class information in Java</span></span>

<span data-ttu-id="a78ed-144">Lors de l’appel des `on` `invoke` méthodes, ou `stream` de `HubConnection` dans le client Java, les utilisateurs doivent passer un `Type` objet plutôt qu’un `Class<?>` objet pour décrire tout générique `Object` passé à la méthode.</span><span class="sxs-lookup"><span data-stu-id="a78ed-144">When calling the `on`, `invoke`, or `stream` methods of `HubConnection` in the Java client, users should pass a `Type` object rather than a `Class<?>` object to describe any generic `Object` passed to the method.</span></span> <span data-ttu-id="a78ed-145">Une `Type` peut être acquise à l’aide de la `TypeReference` classe fournie.</span><span class="sxs-lookup"><span data-stu-id="a78ed-145">A `Type` can be acquired using the provided `TypeReference` class.</span></span> <span data-ttu-id="a78ed-146">Par exemple, à l’aide d’une classe générique personnalisée nommée `Foo<T>` , le code suivant obtient `Type` :</span><span class="sxs-lookup"><span data-stu-id="a78ed-146">For example, using a custom generic class named `Foo<T>`, the following code gets the `Type`:</span></span>

```java
Type fooType = new TypeReference<Foo<String>>() { }).getType();
```

<span data-ttu-id="a78ed-147">Pour les non génériques, tels que les primitives ou d’autres types non paramétrables tels que `String` , vous pouvez simplement utiliser le intégré `.class` .</span><span class="sxs-lookup"><span data-stu-id="a78ed-147">For non-generics, such as primitives or other non-parameterized types like `String`, you can simply use the built-in `.class`.</span></span>

<span data-ttu-id="a78ed-148">Quand vous appelez l’une de ces méthodes avec un ou plusieurs types d’objet, utilisez la syntaxe des génériques lors de l’appel de la méthode.</span><span class="sxs-lookup"><span data-stu-id="a78ed-148">When calling one of these methods with one or more object types, use the generics syntax when invoking the method.</span></span> <span data-ttu-id="a78ed-149">Par exemple, lors de l’inscription d’un `on` Gestionnaire pour une méthode nommée `func` , qui prend comme arguments une chaîne et un `Foo<String>` objet, utilisez le code suivant pour définir une action pour imprimer les arguments :</span><span class="sxs-lookup"><span data-stu-id="a78ed-149">For example, when registering an `on` handler for a method named `func`, which takes as arguments a String and a `Foo<String>` object, use the following code to set an action to print the arguments:</span></span>

```java
hubConnection.<String, Foo<String>>on("func", (param1, param2) ->{
    System.out.println(param1);
    System.out.println(param2);
}, String.class, fooType);
```

<span data-ttu-id="a78ed-150">Cette Convention est nécessaire, car nous ne pouvons pas récupérer des informations complètes sur les types complexes à l’aide de la `Object.getClass` méthode en raison de l’effacement de type dans Java.</span><span class="sxs-lookup"><span data-stu-id="a78ed-150">This convention is necessary because we can not retrieve complete information about complex types with the `Object.getClass` method due to type erasure in Java.</span></span> <span data-ttu-id="a78ed-151">Par exemple, `getClass` l’appel de sur un ne `ArrayList<String>` retourne pas `Class<ArrayList<String>>` , mais `Class<ArrayList>` à la place, qui ne donne pas suffisamment d’informations au désérialiseur pour désérialiser correctement un message entrant.</span><span class="sxs-lookup"><span data-stu-id="a78ed-151">For example, calling `getClass` on an `ArrayList<String>` would not return `Class<ArrayList<String>>`, but rather `Class<ArrayList>`, which does not give the deserializer enough information to correctly deserialize an incoming message.</span></span> <span data-ttu-id="a78ed-152">Il en va de même pour les objets personnalisés.</span><span class="sxs-lookup"><span data-stu-id="a78ed-152">The same is true for custom objects.</span></span>

::: moniker-end

## <a name="known-limitations"></a><span data-ttu-id="a78ed-153">Limitations connues</span><span class="sxs-lookup"><span data-stu-id="a78ed-153">Known limitations</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="a78ed-154">Le transport de secours et le transport des événements envoyés par le serveur ne sont pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="a78ed-154">Transport fallback and the Server Sent Events transport aren't supported.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

* <span data-ttu-id="a78ed-155">Le transport de secours et le transport des événements envoyés par le serveur ne sont pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="a78ed-155">Transport fallback and the Server Sent Events transport aren't supported.</span></span>
* <span data-ttu-id="a78ed-156">Seul le protocole JSON est pris en charge.</span><span class="sxs-lookup"><span data-stu-id="a78ed-156">Only the JSON protocol is supported.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="a78ed-157">Seul le protocole JSON est pris en charge.</span><span class="sxs-lookup"><span data-stu-id="a78ed-157">Only the JSON protocol is supported.</span></span>
* <span data-ttu-id="a78ed-158">Seul le transport WebSocket est pris en charge.</span><span class="sxs-lookup"><span data-stu-id="a78ed-158">Only the WebSockets transport is supported.</span></span>
* <span data-ttu-id="a78ed-159">La diffusion en continu n’est pas encore prise en charge.</span><span class="sxs-lookup"><span data-stu-id="a78ed-159">Streaming isn't supported yet.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="a78ed-160">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="a78ed-160">Additional resources</span></span>

* [<span data-ttu-id="a78ed-161">Informations de référence sur l’API Java</span><span class="sxs-lookup"><span data-stu-id="a78ed-161">Java API reference</span></span>](/java/api/com.microsoft.signalr?view=aspnet-signalr-java)
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>
* [<span data-ttu-id="a78ed-162">SignalRDocumentation sans serveur de service Azure</span><span class="sxs-lookup"><span data-stu-id="a78ed-162">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
