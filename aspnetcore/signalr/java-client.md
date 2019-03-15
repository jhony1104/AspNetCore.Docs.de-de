---
title: ASP.NET Core SignalR-Java-client
author: mikaelm12
description: Erfahren Sie, wie Sie mit dem ASP.NET Core SignalR-Java-Client.
monikerRange: '>= aspnetcore-2.2'
ms.author: mimengis
ms.custom: mvc
ms.date: 03/14/2019
uid: signalr/java-client
ms.openlocfilehash: 09e5ce23ddcc250d212a8cdf1176f39531a9c0ba
ms.sourcegitcommit: d913bca90373c07f89b1d1df01af5fc01fc908ef
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/14/2019
ms.locfileid: "57978489"
---
# <a name="aspnet-core-signalr-java-client"></a><span data-ttu-id="69b35-103">ASP.NET Core SignalR-Java-client</span><span class="sxs-lookup"><span data-stu-id="69b35-103">ASP.NET Core SignalR Java client</span></span>

<span data-ttu-id="69b35-104">Durch [Mikael Mengistu](https://twitter.com/MikaelM_12)</span><span class="sxs-lookup"><span data-stu-id="69b35-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="69b35-105">Die Java-Clients kann Verbindungen mit einem ASP.NET Core SignalR-Server aus Java-Code, einschließlich Android-apps.</span><span class="sxs-lookup"><span data-stu-id="69b35-105">The Java client enables connecting to an ASP.NET Core SignalR server from Java code, including Android apps.</span></span> <span data-ttu-id="69b35-106">Wie die [JavaScript-Client](xref:signalr/javascript-client) und [.NET Client](xref:signalr/dotnet-client), dem Java-Client können Sie zum Empfangen und Senden von Nachrichten mit einem Hub in Echtzeit.</span><span class="sxs-lookup"><span data-stu-id="69b35-106">Like the [JavaScript client](xref:signalr/javascript-client) and the [.NET client](xref:signalr/dotnet-client), the Java client enables you to receive and send messages to a hub in real time.</span></span> <span data-ttu-id="69b35-107">Der Java-Client ist in ASP.NET Core 2.2 und höher verfügbar.</span><span class="sxs-lookup"><span data-stu-id="69b35-107">The Java client is available in ASP.NET Core 2.2 and later.</span></span>

<span data-ttu-id="69b35-108">In diesem Artikel erwähnten Beispiel Java-Konsolenanwendung verwendet die SignalR-Java-Client.</span><span class="sxs-lookup"><span data-stu-id="69b35-108">The sample Java console app referenced in this article uses the SignalR Java client.</span></span>

<span data-ttu-id="69b35-109">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/java-client/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="69b35-109">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/java-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-signalr-java-client-package"></a><span data-ttu-id="69b35-110">Installieren Sie das Paket der SignalR-Java-client</span><span class="sxs-lookup"><span data-stu-id="69b35-110">Install the SignalR Java client package</span></span>

<span data-ttu-id="69b35-111">Die *Signalr-1.0.0* JAR-Datei ermöglicht Clients, die mit SignalR-Hubs herstellen.</span><span class="sxs-lookup"><span data-stu-id="69b35-111">The *signalr-1.0.0* JAR file allows clients to connect to SignalR hubs.</span></span> <span data-ttu-id="69b35-112">Die letzte Versionsnummer der JAR-Datei finden Sie unter den [Maven-Suchergebnissen](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).</span><span class="sxs-lookup"><span data-stu-id="69b35-112">To find the latest JAR file version number, see the [Maven search results](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).</span></span>

<span data-ttu-id="69b35-113">Wenn Sie Gradle verwenden zu können, fügen Sie die folgende Zeile in der `dependencies` Teil Ihrer *build.gradle* Datei:</span><span class="sxs-lookup"><span data-stu-id="69b35-113">If using Gradle, add the following line to the `dependencies` section of your *build.gradle* file:</span></span>

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0'
```

<span data-ttu-id="69b35-114">Wenn Sie mit Maven den folgenden Zeilen hinzufügen der `<dependencies>` Element Ihrer *"pom.xml"* Datei:</span><span class="sxs-lookup"><span data-stu-id="69b35-114">If using Maven, add the following lines inside the `<dependencies>` element of your *pom.xml* file:</span></span>

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a><span data-ttu-id="69b35-115">Verbinden mit einem hub</span><span class="sxs-lookup"><span data-stu-id="69b35-115">Connect to a hub</span></span>

<span data-ttu-id="69b35-116">Herstellen einer `HubConnection`, `HubConnectionBuilder` verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="69b35-116">To establish a `HubConnection`, the `HubConnectionBuilder` should be used.</span></span> <span data-ttu-id="69b35-117">Die Hub-URL und Protokoll-Ebene kann beim Erstellen einer Verbindungs konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="69b35-117">The hub URL and log level can be configured while building a connection.</span></span> <span data-ttu-id="69b35-118">Konfigurieren Sie alle erforderlichen Optionen durch das Aufrufen einer der der `HubConnectionBuilder` Methoden vor dem `build`.</span><span class="sxs-lookup"><span data-stu-id="69b35-118">Configure any required options by calling any of the `HubConnectionBuilder` methods before `build`.</span></span> <span data-ttu-id="69b35-119">Starten Sie die Verbindung mit `start`.</span><span class="sxs-lookup"><span data-stu-id="69b35-119">Start the connection with `start`.</span></span>

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="69b35-120">Aufrufen von Hub-Methoden von client</span><span class="sxs-lookup"><span data-stu-id="69b35-120">Call hub methods from client</span></span>

<span data-ttu-id="69b35-121">Ein Aufruf von `send` eine hubmethode aufruft.</span><span class="sxs-lookup"><span data-stu-id="69b35-121">A call to `send` invokes a hub method.</span></span> <span data-ttu-id="69b35-122">Übergeben Sie den Namen des Hubs-Methode und alle Argumente, die in die hubmethode, die definiert `send`.</span><span class="sxs-lookup"><span data-stu-id="69b35-122">Pass the hub method name and any arguments defined in the hub method to `send`.</span></span>

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

> [!NOTE]
> <span data-ttu-id="69b35-123">Bei Verwendung von Azure SignalR Service in *serverlose Modus*, Sie können nicht von einem Client hubmethoden aufrufen.</span><span class="sxs-lookup"><span data-stu-id="69b35-123">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="69b35-124">Weitere Informationen finden Sie unter den [SignalR Service-Dokumentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span><span class="sxs-lookup"><span data-stu-id="69b35-124">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="69b35-125">Rufen Sie Client-Methoden von Hub-Instanz</span><span class="sxs-lookup"><span data-stu-id="69b35-125">Call client methods from hub</span></span>

<span data-ttu-id="69b35-126">Verwendung `hubConnection.on` Methoden auf dem Client definieren, die der Hub aufgerufen werden kann.</span><span class="sxs-lookup"><span data-stu-id="69b35-126">Use `hubConnection.on` to define methods on the client that the hub can call.</span></span> <span data-ttu-id="69b35-127">Definieren Sie die Methoden, nach der Erstellung von jedoch vor dem Starten der Verbindung.</span><span class="sxs-lookup"><span data-stu-id="69b35-127">Define the methods after building but before starting the connection.</span></span>

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a><span data-ttu-id="69b35-128">Hinzufügen der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="69b35-128">Add logging</span></span>

<span data-ttu-id="69b35-129">Der SignalR-Java-Client verwendet die [SLF4J](https://www.slf4j.org/) Bibliothek für die Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="69b35-129">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="69b35-130">Es ist eine allgemeine protokollierungs-API an, die Benutzer der Bibliothek, wählen ihre eigenen spezifischen protokollierungsimplementierung bereitgestellt werden, indem Sie in einer bestimmten Protokollierung Abhängigkeit bringen kann.</span><span class="sxs-lookup"><span data-stu-id="69b35-130">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="69b35-131">Der folgende Codeausschnitt zeigt, wie Sie mit `java.util.logging` mit dem SignalR-Java-Client.</span><span class="sxs-lookup"><span data-stu-id="69b35-131">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="69b35-132">Wenn Sie die Anmeldung bei Ihrer Abhängigkeiten nicht konfigurieren, lädt SLF4J eine Protokollierung standardmäßig nicht-Vorgang die folgende Warnmeldung an:</span><span class="sxs-lookup"><span data-stu-id="69b35-132">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="69b35-133">Dies kann problemlos ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="69b35-133">This can safely be ignored.</span></span>

## <a name="android-development-notes"></a><span data-ttu-id="69b35-134">Anmerkungen zu dieser Version Android-Entwicklung</span><span class="sxs-lookup"><span data-stu-id="69b35-134">Android development notes</span></span>

<span data-ttu-id="69b35-135">Im Hinblick auf die Android SDK-Kompatibilität für die SignalR-Client-Funktionen die folgenden Aspekte berücksichtigt, wenn Sie Ihre Android-SDK-Zielversion angeben:</span><span class="sxs-lookup"><span data-stu-id="69b35-135">With regards to Android SDK compatibility for the SignalR client features, consider the following items when specifying your target Android SDK version:</span></span>

* <span data-ttu-id="69b35-136">Der SignalR-Java-Client wird auf Android-API-Ebene-16 und höher ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="69b35-136">The SignalR Java Client will run on Android API Level 16 and later.</span></span>
* <span data-ttu-id="69b35-137">Herstellen einer Verbindung über den Azure SignalR Service müssen sich auf Android-API-Ebene 20 und höher da die [Azure SignalR Service](/azure/azure-signalr/signalr-overview) TLS 1.2 erfordert und unterstützt keine SHA-1-basierte Verschlüsselungssammlungen.</span><span class="sxs-lookup"><span data-stu-id="69b35-137">Connecting through the Azure SignalR Service will require Android API Level 20 and later because the [Azure SignalR Service](/azure/azure-signalr/signalr-overview) requires TLS 1.2 and doesn't support SHA-1-based cipher suites.</span></span> <span data-ttu-id="69b35-138">Android [Unterstützung für SHA-256 (und höher)-Verschlüsselungssammlungen hinzugefügt](https://developer.android.com/reference/javax/net/ssl/SSLSocket) in API-Ebene 20.</span><span class="sxs-lookup"><span data-stu-id="69b35-138">Android [added support for SHA-256 (and above) cipher suites](https://developer.android.com/reference/javax/net/ssl/SSLSocket) in API Level 20.</span></span>

## <a name="configure-bearer-token-authentication"></a><span data-ttu-id="69b35-139">Konfigurieren von Bearer-token-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="69b35-139">Configure bearer token authentication</span></span>

<span data-ttu-id="69b35-140">In den SignalR-Java-Client, können Sie ein trägertoken, das zur Authentifizierung verwendet wird, durch die Bereitstellung einer "Access token Factory" konfigurieren, um die [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="69b35-140">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an "access token factory" to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="69b35-141">Verwendung [WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) zu einer [RxJava](https://github.com/ReactiveX/RxJava) [einzelne<String>](http://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="69b35-141">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single<String>](http://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="69b35-142">Mit einem Aufruf von [Single.defer](http://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), Sie können Logik schreiben, um Zugriffstoken für den Client zu generieren.</span><span class="sxs-lookup"><span data-stu-id="69b35-142">With a call to [Single.defer](http://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

## <a name="known-limitations"></a><span data-ttu-id="69b35-143">Bekannte Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="69b35-143">Known limitations</span></span>

* <span data-ttu-id="69b35-144">Es wird nur das JSON-Protokoll unterstützt.</span><span class="sxs-lookup"><span data-stu-id="69b35-144">Only the JSON protocol is supported.</span></span>
* <span data-ttu-id="69b35-145">Nur die WebSockets-Übertragung wird unterstützt.</span><span class="sxs-lookup"><span data-stu-id="69b35-145">Only the WebSockets transport is supported.</span></span>
* <span data-ttu-id="69b35-146">Streaming wird noch nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="69b35-146">Streaming isn't supported yet.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="69b35-147">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="69b35-147">Additional resources</span></span>

* [<span data-ttu-id="69b35-148">Java-API-Referenz</span><span class="sxs-lookup"><span data-stu-id="69b35-148">Java API reference</span></span>](/java/api/com.microsoft.signalr?view=aspnet-signalr-java)
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>
* [<span data-ttu-id="69b35-149">Serverlose Azure SignalR Service-Dokumentation</span><span class="sxs-lookup"><span data-stu-id="69b35-149">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
