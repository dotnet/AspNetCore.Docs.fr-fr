---
title: :::no-loc(SignalR):::Client Java ASP.net Core
author: mikaelm12
description: 'Découvrez comment utiliser le client ASP.NET Core :::no-loc(SignalR)::: java.'
monikerRange: '>= aspnetcore-2.2'
ms.author: mimengis
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: signalr/java-client
ms.openlocfilehash: 638333176ae31b088bdf5ebefe97e87bde6c0d32
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051458"
---
# <a name="aspnet-core-no-locsignalr-java-client"></a><span data-ttu-id="7d2cc-103">:::no-loc(SignalR):::Client Java ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="7d2cc-103">ASP.NET Core :::no-loc(SignalR)::: Java client</span></span>

<span data-ttu-id="7d2cc-104">Par [Mikael Mengistu](https://twitter.com/MikaelM_12)</span><span class="sxs-lookup"><span data-stu-id="7d2cc-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="7d2cc-105">Le client Java permet de se connecter à un :::no-loc(SignalR)::: serveur ASP.net core à partir de code Java, y compris les applications Android.</span><span class="sxs-lookup"><span data-stu-id="7d2cc-105">The Java client enables connecting to an ASP.NET Core :::no-loc(SignalR)::: server from Java code, including Android apps.</span></span> <span data-ttu-id="7d2cc-106">À l’instar du client [JavaScript](xref:signalr/javascript-client) et du [client .net](xref:signalr/dotnet-client), le client Java vous permet de recevoir et d’envoyer des messages à un hub en temps réel.</span><span class="sxs-lookup"><span data-stu-id="7d2cc-106">Like the [JavaScript client](xref:signalr/javascript-client) and the [.NET client](xref:signalr/dotnet-client), the Java client enables you to receive and send messages to a hub in real time.</span></span> <span data-ttu-id="7d2cc-107">Le client Java est disponible dans ASP.NET Core 2,2 et versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="7d2cc-107">The Java client is available in ASP.NET Core 2.2 and later.</span></span>

<span data-ttu-id="7d2cc-108">L’exemple d’application de console Java référencé dans cet article utilise le :::no-loc(SignalR)::: client Java.</span><span class="sxs-lookup"><span data-stu-id="7d2cc-108">The sample Java console app referenced in this article uses the :::no-loc(SignalR)::: Java client.</span></span>

<span data-ttu-id="7d2cc-109">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7d2cc-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-java-client-package"></a><span data-ttu-id="7d2cc-110">Installer le :::no-loc(SignalR)::: package client Java</span><span class="sxs-lookup"><span data-stu-id="7d2cc-110">Install the :::no-loc(SignalR)::: Java client package</span></span>

<span data-ttu-id="7d2cc-111">Le fichier jar *signalr-1.0.0* permet aux clients de se connecter aux :::no-loc(SignalR)::: hubs.</span><span class="sxs-lookup"><span data-stu-id="7d2cc-111">The *signalr-1.0.0* JAR file allows clients to connect to :::no-loc(SignalR)::: hubs.</span></span> <span data-ttu-id="7d2cc-112">Pour rechercher le dernier numéro de version du fichier JAR, consultez les résultats de la [recherche Maven](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).</span><span class="sxs-lookup"><span data-stu-id="7d2cc-112">To find the latest JAR file version number, see the [Maven search results](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).</span></span>

<span data-ttu-id="7d2cc-113">Si vous utilisez Gradle, ajoutez la ligne suivante à la `dependencies` section de votre fichier *Build. Gradle* :</span><span class="sxs-lookup"><span data-stu-id="7d2cc-113">If using Gradle, add the following line to the `dependencies` section of your *build.gradle* file:</span></span>

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0'
```

<span data-ttu-id="7d2cc-114">Si vous utilisez Maven, ajoutez les lignes suivantes à l’intérieur `<dependencies>` de l’élément de votre fichier *pom.xml* :</span><span class="sxs-lookup"><span data-stu-id="7d2cc-114">If using Maven, add the following lines inside the `<dependencies>` element of your *pom.xml* file:</span></span>

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a><span data-ttu-id="7d2cc-115">Se connecter à un concentrateur</span><span class="sxs-lookup"><span data-stu-id="7d2cc-115">Connect to a hub</span></span>

<span data-ttu-id="7d2cc-116">Pour établir un `HubConnection` , le `HubConnectionBuilder` doit être utilisé.</span><span class="sxs-lookup"><span data-stu-id="7d2cc-116">To establish a `HubConnection`, the `HubConnectionBuilder` should be used.</span></span> <span data-ttu-id="7d2cc-117">L’URL du Hub et le niveau de journalisation peuvent être configurés lors de la création d’une connexion.</span><span class="sxs-lookup"><span data-stu-id="7d2cc-117">The hub URL and log level can be configured while building a connection.</span></span> <span data-ttu-id="7d2cc-118">Configurez toutes les options requises en appelant l’une des `HubConnectionBuilder` méthodes antérieures à `build` .</span><span class="sxs-lookup"><span data-stu-id="7d2cc-118">Configure any required options by calling any of the `HubConnectionBuilder` methods before `build`.</span></span> <span data-ttu-id="7d2cc-119">Démarrez la connexion avec `start` .</span><span class="sxs-lookup"><span data-stu-id="7d2cc-119">Start the connection with `start`.</span></span>

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="7d2cc-120">Appeler les méthodes de concentrateur à partir du client</span><span class="sxs-lookup"><span data-stu-id="7d2cc-120">Call hub methods from client</span></span>

<span data-ttu-id="7d2cc-121">Un appel à `send` appelle une méthode de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="7d2cc-121">A call to `send` invokes a hub method.</span></span> <span data-ttu-id="7d2cc-122">Transmettez le nom de la méthode de concentrateur et les arguments définis dans la méthode de concentrateur à `send` .</span><span class="sxs-lookup"><span data-stu-id="7d2cc-122">Pass the hub method name and any arguments defined in the hub method to `send`.</span></span>

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

> [!NOTE]
> <span data-ttu-id="7d2cc-123">L’appel de méthodes de Hub à partir d’un client est pris en charge uniquement lors de l’utilisation du :::no-loc(SignalR)::: service Azure en mode *par défaut* .</span><span class="sxs-lookup"><span data-stu-id="7d2cc-123">Calling hub methods from a client is only supported when using the Azure :::no-loc(SignalR)::: Service in *Default* mode.</span></span> <span data-ttu-id="7d2cc-124">Pour plus d’informations, consultez [Forum aux questions (référentiel GitHub Azure-signalr)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span><span class="sxs-lookup"><span data-stu-id="7d2cc-124">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="7d2cc-125">Appeler des méthodes clientes à partir du Hub</span><span class="sxs-lookup"><span data-stu-id="7d2cc-125">Call client methods from hub</span></span>

<span data-ttu-id="7d2cc-126">Utilisez `hubConnection.on` pour définir des méthodes sur le client que le hub peut appeler.</span><span class="sxs-lookup"><span data-stu-id="7d2cc-126">Use `hubConnection.on` to define methods on the client that the hub can call.</span></span> <span data-ttu-id="7d2cc-127">Définissez les méthodes après la génération mais avant de commencer la connexion.</span><span class="sxs-lookup"><span data-stu-id="7d2cc-127">Define the methods after building but before starting the connection.</span></span>

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a><span data-ttu-id="7d2cc-128">Ajouter une journalisation</span><span class="sxs-lookup"><span data-stu-id="7d2cc-128">Add logging</span></span>

<span data-ttu-id="7d2cc-129">Le :::no-loc(SignalR)::: client Java utilise la bibliothèque [SLF4J](https://www.slf4j.org/) pour la journalisation.</span><span class="sxs-lookup"><span data-stu-id="7d2cc-129">The :::no-loc(SignalR)::: Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="7d2cc-130">Il s’agit d’une API de journalisation de haut niveau qui permet aux utilisateurs de la bibliothèque de choisir leur propre implémentation de journalisation spécifique en introduisant une dépendance de journalisation spécifique.</span><span class="sxs-lookup"><span data-stu-id="7d2cc-130">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="7d2cc-131">L’extrait de code suivant montre comment utiliser `java.util.logging` avec le :::no-loc(SignalR)::: client Java.</span><span class="sxs-lookup"><span data-stu-id="7d2cc-131">The following code snippet shows how to use `java.util.logging` with the :::no-loc(SignalR)::: Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="7d2cc-132">Si vous ne configurez pas la journalisation dans vos dépendances, SLF4J charge un journal de non-opération par défaut avec le message d’avertissement suivant :</span><span class="sxs-lookup"><span data-stu-id="7d2cc-132">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="7d2cc-133">Cela peut être ignoré en toute sécurité.</span><span class="sxs-lookup"><span data-stu-id="7d2cc-133">This can safely be ignored.</span></span>

## <a name="android-development-notes"></a><span data-ttu-id="7d2cc-134">Remarques sur le développement Android</span><span class="sxs-lookup"><span data-stu-id="7d2cc-134">Android development notes</span></span>

<span data-ttu-id="7d2cc-135">En ce qui concerne la Android SDK compatibilité pour les :::no-loc(SignalR)::: fonctionnalités clientes, tenez compte des éléments suivants lorsque vous spécifiez votre version de Android SDK cible :</span><span class="sxs-lookup"><span data-stu-id="7d2cc-135">With regards to Android SDK compatibility for the :::no-loc(SignalR)::: client features, consider the following items when specifying your target Android SDK version:</span></span>

* <span data-ttu-id="7d2cc-136">Le :::no-loc(SignalR)::: client Java s’exécutera sur l’API Android de niveau 16 et versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="7d2cc-136">The :::no-loc(SignalR)::: Java Client will run on Android API Level 16 and later.</span></span>
* <span data-ttu-id="7d2cc-137">La connexion via le :::no-loc(SignalR)::: service Azure nécessite le niveau d’API Android 20 et versions ultérieures, car le [ :::no-loc(SignalR)::: service azure](/azure/azure-signalr/signalr-overview) requiert TLS 1,2 et ne prend pas en charge les suites de chiffrement SHA-1.</span><span class="sxs-lookup"><span data-stu-id="7d2cc-137">Connecting through the Azure :::no-loc(SignalR)::: Service will require Android API Level 20 and later because the [Azure :::no-loc(SignalR)::: Service](/azure/azure-signalr/signalr-overview) requires TLS 1.2 and doesn't support SHA-1-based cipher suites.</span></span> <span data-ttu-id="7d2cc-138">Android a [ajouté la prise en charge des suites de chiffrement SHA-256 (et versions ultérieures)](https://developer.android.com/reference/javax/net/ssl/SSLSocket) au niveau de l’API 20.</span><span class="sxs-lookup"><span data-stu-id="7d2cc-138">Android [added support for SHA-256 (and above) cipher suites](https://developer.android.com/reference/javax/net/ssl/SSLSocket) in API Level 20.</span></span>

## <a name="configure-bearer-token-authentication"></a><span data-ttu-id="7d2cc-139">Configurer l’authentification du jeton du porteur</span><span class="sxs-lookup"><span data-stu-id="7d2cc-139">Configure bearer token authentication</span></span>

<span data-ttu-id="7d2cc-140">Dans le :::no-loc(SignalR)::: client Java, vous pouvez configurer un jeton de porteur à utiliser pour l’authentification en fournissant une « fabrique de jetons d’accès » au [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="7d2cc-140">In the :::no-loc(SignalR)::: Java client, you can configure a bearer token to use for authentication by providing an "access token factory" to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="7d2cc-141">Utilisez [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) pour fournir un [RxJava](https://github.com/ReactiveX/RxJava) [unique \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="7d2cc-141">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="7d2cc-142">Avec un appel à [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), vous pouvez écrire une logique pour produire des jetons d’accès pour votre client.</span><span class="sxs-lookup"><span data-stu-id="7d2cc-142">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

## <a name="known-limitations"></a><span data-ttu-id="7d2cc-143">Limitations connues</span><span class="sxs-lookup"><span data-stu-id="7d2cc-143">Known limitations</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="7d2cc-144">Seul le protocole JSON est pris en charge.</span><span class="sxs-lookup"><span data-stu-id="7d2cc-144">Only the JSON protocol is supported.</span></span>
* <span data-ttu-id="7d2cc-145">Le transport de secours et le transport des événements envoyés par le serveur ne sont pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="7d2cc-145">Transport fallback and the Server Sent Events transport aren't supported.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="7d2cc-146">Seul le protocole JSON est pris en charge.</span><span class="sxs-lookup"><span data-stu-id="7d2cc-146">Only the JSON protocol is supported.</span></span>
* <span data-ttu-id="7d2cc-147">Seul le transport WebSocket est pris en charge.</span><span class="sxs-lookup"><span data-stu-id="7d2cc-147">Only the WebSockets transport is supported.</span></span>
* <span data-ttu-id="7d2cc-148">La diffusion en continu n’est pas encore prise en charge.</span><span class="sxs-lookup"><span data-stu-id="7d2cc-148">Streaming isn't supported yet.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="7d2cc-149">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="7d2cc-149">Additional resources</span></span>

* [<span data-ttu-id="7d2cc-150">Informations de référence sur l’API Java</span><span class="sxs-lookup"><span data-stu-id="7d2cc-150">Java API reference</span></span>](/java/api/com.microsoft.signalr?view=aspnet-signalr-java)
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>
* [<span data-ttu-id="7d2cc-151">:::no-loc(SignalR):::Documentation sans serveur de service Azure</span><span class="sxs-lookup"><span data-stu-id="7d2cc-151">Azure :::no-loc(SignalR)::: Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
