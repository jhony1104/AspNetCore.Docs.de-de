---
title: ASP.NET Core SignalR-Java-client
author: mikaelm12
description: Erfahren Sie, wie Sie mit dem ASP.NET Core SignalR-Java-Client.
monikerRange: '>= aspnetcore-2.2'
ms.author: mimengis
ms.custom: mvc
ms.date: 03/14/2019
uid: signalr/java-client
ms.openlocfilehash: 53055b2642cae7640ae79cb5ae88ad6b2714c689
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58209680"
---
# <a name="aspnet-core-signalr-java-client"></a>ASP.NET Core SignalR-Java-client

Durch [Mikael Mengistu](https://twitter.com/MikaelM_12)

Die Java-Clients kann Verbindungen mit einem ASP.NET Core SignalR-Server aus Java-Code, einschließlich Android-apps. Wie die [JavaScript-Client](xref:signalr/javascript-client) und [.NET Client](xref:signalr/dotnet-client), dem Java-Client können Sie zum Empfangen und Senden von Nachrichten mit einem Hub in Echtzeit. Der Java-Client ist in ASP.NET Core 2.2 und höher verfügbar.

In diesem Artikel erwähnten Beispiel Java-Konsolenanwendung verwendet die SignalR-Java-Client.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/java-client/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="install-the-signalr-java-client-package"></a>Installieren Sie das Paket der SignalR-Java-client

Die *Signalr-1.0.0* JAR-Datei ermöglicht Clients, die mit SignalR-Hubs herstellen. Die letzte Versionsnummer der JAR-Datei finden Sie unter den [Maven-Suchergebnissen](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).

Wenn Sie Gradle verwenden zu können, fügen Sie die folgende Zeile in der `dependencies` Teil Ihrer *build.gradle* Datei:

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0'
```

Wenn Sie mit Maven den folgenden Zeilen hinzufügen der `<dependencies>` Element Ihrer *"pom.xml"* Datei:

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a>Verbinden mit einem hub

Herstellen einer `HubConnection`, `HubConnectionBuilder` verwendet werden soll. Die Hub-URL und Protokoll-Ebene kann beim Erstellen einer Verbindungs konfiguriert werden. Konfigurieren Sie alle erforderlichen Optionen durch das Aufrufen einer der der `HubConnectionBuilder` Methoden vor dem `build`. Starten Sie die Verbindung mit `start`.

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a>Aufrufen von Hub-Methoden von client

Ein Aufruf von `send` eine hubmethode aufruft. Übergeben Sie den Namen des Hubs-Methode und alle Argumente, die in die hubmethode, die definiert `send`.

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

> [!NOTE]
> Bei Verwendung von Azure SignalR Service in *serverlose Modus*, Sie können nicht von einem Client hubmethoden aufrufen. Weitere Informationen finden Sie unter den [SignalR Service-Dokumentation](/azure/azure-signalr/signalr-concept-serverless-development-config).

## <a name="call-client-methods-from-hub"></a>Rufen Sie Client-Methoden von Hub-Instanz

Verwendung `hubConnection.on` Methoden auf dem Client definieren, die der Hub aufgerufen werden kann. Definieren Sie die Methoden, nach der Erstellung von jedoch vor dem Starten der Verbindung.

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a>Hinzufügen der Protokollierung

Der SignalR-Java-Client verwendet die [SLF4J](https://www.slf4j.org/) Bibliothek für die Protokollierung. Es ist eine allgemeine protokollierungs-API an, die Benutzer der Bibliothek, wählen ihre eigenen spezifischen protokollierungsimplementierung bereitgestellt werden, indem Sie in einer bestimmten Protokollierung Abhängigkeit bringen kann. Der folgende Codeausschnitt zeigt, wie Sie mit `java.util.logging` mit dem SignalR-Java-Client.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Wenn Sie die Anmeldung bei Ihrer Abhängigkeiten nicht konfigurieren, lädt SLF4J eine Protokollierung standardmäßig nicht-Vorgang die folgende Warnmeldung an:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Dies kann problemlos ignoriert werden.

## <a name="android-development-notes"></a>Anmerkungen zu dieser Version Android-Entwicklung

Im Hinblick auf die Android SDK-Kompatibilität für die SignalR-Client-Funktionen die folgenden Aspekte berücksichtigt, wenn Sie Ihre Android-SDK-Zielversion angeben:

* Der SignalR-Java-Client wird auf Android-API-Ebene-16 und höher ausgeführt werden.
* Herstellen einer Verbindung über den Azure SignalR Service müssen sich auf Android-API-Ebene 20 und höher da die [Azure SignalR Service](/azure/azure-signalr/signalr-overview) TLS 1.2 erfordert und unterstützt keine SHA-1-basierte Verschlüsselungssammlungen. Android [Unterstützung für SHA-256 (und höher)-Verschlüsselungssammlungen hinzugefügt](https://developer.android.com/reference/javax/net/ssl/SSLSocket) in API-Ebene 20.

## <a name="configure-bearer-token-authentication"></a>Konfigurieren von Bearer-token-Authentifizierung

In den SignalR-Java-Client, können Sie ein trägertoken, das zur Authentifizierung verwendet wird, durch die Bereitstellung einer "Access token Factory" konfigurieren, um die [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Verwendung [WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) zu einer [RxJava](https://github.com/ReactiveX/RxJava) [einzelne\<Zeichenfolge >](http://reactivex.io/documentation/single.html). Mit einem Aufruf von [Single.defer](http://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), Sie können Logik schreiben, um Zugriffstoken für den Client zu generieren.

```java
HubConnection hubConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

## <a name="known-limitations"></a>Bekannte Einschränkungen

* Es wird nur das JSON-Protokoll unterstützt.
* Nur die WebSockets-Übertragung wird unterstützt.
* Streaming wird noch nicht unterstützt.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Java-API-Referenz](/java/api/com.microsoft.signalr?view=aspnet-signalr-java)
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>
* [Serverlose Azure SignalR Service-Dokumentation](/azure/azure-signalr/signalr-concept-serverless-development-config)
