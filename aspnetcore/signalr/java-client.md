---
title: ASP.net Core SignalR Java-Clients
author: mikaelm12
description: Erfahren Sie, wie Sie den ASP.net Core SignalR Java-Client verwenden.
monikerRange: '>= aspnetcore-2.2'
ms.author: mimengis
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/java-client
ms.openlocfilehash: 6919eabf454f16887e012161a454a4848c45002b
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652213"
---
# <a name="aspnet-core-opno-locsignalr-java-client"></a>ASP.net Core SignalR Java-Clients

Von [Mikael Mengistu](https://twitter.com/MikaelM_12)

Der Java-Client ermöglicht das Herstellen einer Verbindung mit einem ASP.net Core SignalR Server aus Java-Code, einschließlich Android-Apps. Wie der [JavaScript-Client](xref:signalr/javascript-client) und der [.NET-Client](xref:signalr/dotnet-client)ermöglicht der Java-Client das empfangen und Senden von Nachrichten an einen Hub in Echtzeit. Der Java-Client ist in ASP.net Core 2,2 und höher verfügbar.

Die Java-Beispiel Konsolen-APP, auf die in diesem Artikel verwiesen wird, verwendet den SignalR Java-Client

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="install-the-opno-locsignalr-java-client-package"></a>Installieren des SignalR Java-Client Pakets

Die jar *-Datei signalr-1.0.0* ermöglicht Clients das Herstellen einer Verbindung mit SignalR Hubs. Die aktuelle JAR-Datei-Versionsnummer finden Sie in den [Maven-Suchergebnissen](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).

Wenn Sie gradle verwenden, fügen Sie die folgende Zeile zum Abschnitt "`dependencies`" in der Datei " *Build. gradle* " hinzu:

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0'
```

Wenn Sie Maven verwenden, fügen Sie die folgenden Zeilen innerhalb des `<dependencies>`-Elements der Datei " *Pom. XML* " hinzu:

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a>Verbinden mit einem hub

Zum Einrichten einer `HubConnection`sollte die `HubConnectionBuilder` verwendet werden. Die Hub-URL und die Protokollebene können während der Verbindungs Herstellung konfiguriert werden. Konfigurieren Sie alle erforderlichen Optionen, indem Sie eine der `HubConnectionBuilder` Methoden aufrufen, bevor Sie `build`. Starten Sie die Verbindung mit `start`.

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a>Aufrufen von Hub-Methoden von client

Ein Aufruf von `send` Ruft eine Hub-Methode auf. Übergeben Sie den Namen der Hub-Methode und alle in der Hub-Methode definierten Argumente an `send`.

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

> [!NOTE]
> Wenn Sie Azure SignalR Service im *Server losen Modus*verwenden, können Sie keine hubmethoden von einem Client aus abrufen. Weitere Informationen finden Sie in der [Dokumentation zumSignalR-Dienst](/azure/azure-signalr/signalr-concept-serverless-development-config).

## <a name="call-client-methods-from-hub"></a>Rufen Sie Client-Methoden von Hub-Instanz

Verwenden Sie `hubConnection.on`, um Methoden auf dem Client zu definieren, die der Hub abrufen kann. Definieren Sie die Methoden nach dem Aufbau, aber vor dem Starten der Verbindung.

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a>Protokollierung hinzufügen

Der SignalR Java-Client verwendet die [SLF4J](https://www.slf4j.org/) -Bibliothek für die Protokollierung. Dabei handelt es sich um eine allgemeine Protokollierungs-API, die es Benutzern der Bibliothek ermöglicht, ihre eigene spezifische Protokollierungs Implementierung zu wählen, indem Sie eine bestimmte Protokollierungs Abhängigkeit einbinden. Der folgende Code Ausschnitt zeigt, wie `java.util.logging` mit dem SignalR Java-Client verwendet wird.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Wenn Sie die Protokollierung in ihren Abhängigkeiten nicht konfigurieren, lädt SLF4J eine Standard Protokollierung ohne Vorgang mit folgender Warnmeldung:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Dies kann sicher ignoriert werden.

## <a name="android-development-notes"></a>Hinweise zur Android-Entwicklung

Beachten Sie beim Angeben der Ziel Android SDK Version die folgenden Punkte, wenn Sie die Android SDK Kompatibilität für die SignalR-Client Features beachten:

* Der SignalR Java-Client kann auf Android-API-Ebene 16 und höher ausgeführt werden.
* Zum Herstellen einer Verbindung über den Azure SignalR-Dienst sind Android-API-Ebene 20 und höher erforderlich, da der [Azure SignalR-Dienst](/azure/azure-signalr/signalr-overview) TLS 1,2 erfordert und keine SHA-1-basierten Verschlüsselungs Sammlungen unterstützt. Android hat [Unterstützung für die Verschlüsselungs Sammlungen SHA-256 (und höher)](https://developer.android.com/reference/javax/net/ssl/SSLSocket) auf API-Ebene 20 hinzugefügt.

## <a name="configure-bearer-token-authentication"></a>Konfigurieren der bearertokenauthentifizierung

Im SignalR Java-Client können Sie ein bearertoken für die Authentifizierung konfigurieren, indem Sie dem [httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)eine "zugriffstokenfactory" bereitstellen. Verwenden Sie [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , um eine [rxjava](https://github.com/ReactiveX/RxJava) - [\<Zeichenfolge >](https://reactivex.io/documentation/single.html)bereitzustellen. Beim Aufrufen von " [Single.](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)Write" können Sie Logik schreiben, um Zugriffs Token für den Client zu erstellen.

```java
HubConnection hubConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

## <a name="known-limitations"></a>Bekannte Einschränkungen

::: moniker range=">= aspnetcore-3.0"

* Nur das JSON-Protokoll wird unterstützt.
* Der Transport Fall Back und der Transport der Server gesendeten Ereignisse werden nicht unterstützt.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Nur das JSON-Protokoll wird unterstützt.
* Nur der websockets-Transport wird unterstützt.
* Streaming wird noch nicht unterstützt.

::: moniker-end

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Java-API-Verweis](/java/api/com.microsoft.signalr?view=aspnet-signalr-java)
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>
* [Server lose Dokumentation zu Azure SignalR Service](/azure/azure-signalr/signalr-concept-serverless-development-config)
