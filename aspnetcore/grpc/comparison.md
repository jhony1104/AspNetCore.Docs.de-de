---
title: Vergleich von gRPC-Diensten mit HTTP-APIs
author: jamesnk
description: Erfahren Sie, wie gRPC verglichen mit HTTP-APIs und was hat empfehlen Szenarien sind.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 03/31/2019
uid: grpc/comparison
ms.openlocfilehash: 655c921788deb30f3c0f3b47f4440dc8701c0f59
ms.sourcegitcommit: ccbb84ae307a5bc527441d3d509c20b5c1edde05
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/19/2019
ms.locfileid: "65874936"
---
# <a name="comparing-grpc-services-with-http-apis"></a>Vergleich von gRPC-Diensten mit HTTP-APIs

Durch [James Newton-King](https://twitter.com/jamesnk)

In diesem Artikel wird erläutert, wie [gRPC Services](https://grpc.io/docs/guides/) vergleichen mit HTTP-APIs (einschließlich ASP.NET Core [Web-APIs](xref:web-api/index)). Die Technologie verwendet, um eine API für Ihre app bereitzustellen. es ist wichtig und gRPC bietet besondere Vorteile im Vergleich zu HTTP-APIs. Dieser Artikel beschreibt die Stärken und Schwächen des gRPC und empfiehlt die Szenarios für die Verwendung von gRPC gegenüber anderen Technologien.

#### <a name="overview"></a>Übersicht

|    Feature             |    gRPC                                                 |    HTTP-APIs mit JSON-Code                       |
|------------------------|---------------------------------------------------------|----------------------------------------------|
|    Vertrag            |    Erforderlich (`*.proto`)                                 |    Optional (OpenAPI)                        |
|    Transport           |    HTTP/2                                               |    HTTP                                      |
|    Payload             |    [Protobuf (klein, binär)](#performance)             |    JSON (groß, lesbare)              |
|    Prescriptiveness    |    [Strict-Spezifikation](#strict-specification)        |    Gehen verloren. HTTP ist gültig                  |
|    Streaming           |    [Client, Server oder bidirektionale](#streaming)         |    Client und server                            |
|    Browserunterstützung     |    [Nein (erfordert Grpc-Web)](#limited-browser-support)   |    Ja                                       |
|    Sicherheit            |    Transport (HTTPS)                                    |    Transport (HTTPS)                         |
|    Client-codegenerierung     |    [Ja](#code-generation)                              |    OpenAPI und die Tools von Drittanbietern             |

## <a name="grpc-strengths"></a>gRPC Stärken

### <a name="performance"></a>Leistung

gRPC-Nachrichten werden mit serialisiert [Protobuf](https://developers.google.com/protocol-buffers/docs/overview), eine effiziente binäre Nachrichten-Format. Protobuf serialisiert sehr schnell auf dem Server und Client. Protobuf Serialisierung führt kleine Nachricht-Nutzlasten, die wichtig für Szenarien mit eingeschränkter Bandbreite wie mobile apps.

gRPC dient für HTTP/2, eine größere Überarbeitung von HTTP, die beachtliche Leistungsvorteile über HTTP bietet 1.x:

* Binäre Framing- und Komprimierung. HTTP/2-Protokoll ist kompakter und effizienter, sowohl beim Senden und empfangen.
* Durchführen von Multiplexing von mehreren Aufrufen von HTTP/2 über eine einzige TCP-Verbindung. Durchführen von Multiplexing beseitigt [Head-of-Line-Blockierung](https://en.wikipedia.org/wiki/Head-of-line_blocking).

### <a name="code-generation"></a>Codeerzeugung

Alle gRPC-Frameworks bieten erstklassige Unterstützung für die codegenerierung. Eine Datei "Core" gRPC-Entwicklung ist das [ `*.proto` Datei](https://developers.google.com/protocol-buffers/docs/proto3), definiert den Vertrag des gRPC-Dienste und Nachrichten. Aus dieser Datei gRPC-Frameworks wird Code eine Dienst-Basisklasse, Nachrichten und eine vollständige Client generieren.

Anhand der `*.proto` Datei zwischen Server und Client, Nachrichten und Clientcode aus End Ende generiert werden. Codegenerierung des Clients Duplizierung von Nachrichten auf dem Client und Server beseitigt und einen stark typisierten Client für Sie erstellt. Ohne Schreiben eines spart Zeit, in Anwendungen mit vielen Diensten.

### <a name="strict-specification"></a>Strict-Spezifikation

Eine formale Spezifikation für die HTTP-API mit JSON-Code ist nicht vorhanden. Entwickler diskutiert das beste Format von URLs, HTTP-Verben und Antwortcodes.

Die [gRPC-Spezifikation](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) schreibt das Format ein gRPC-Dienst folgen muss. gRPC Debatte beseitigt und spart Entwicklungszeit, da es sich bei gPRC plattformübergreifende codewiederverwendung sowie Implementierungen konsistent ist.

### <a name="streaming"></a>Streaming

HTTP/2 bietet eine Grundlage für langlebige, Kommunikation in Echtzeit-Streams. gRPC bietet erstklassige Unterstützung für das streaming über HTTP/2.

Ein gRPC-Dienst unterstützt alle streaming Kombinationen:

* Unäre (kein streaming)
* Streaming-Client-Server
* Streaming Server-Client
* Bidirektionale streaming

### <a name="deadlinetimeouts-and-cancellation"></a>Stichtag/Timeouts und Abbruch

gRPC ermöglicht Clients, um anzugeben, wie lange sie bereit sind, zu warten, bis ein RPC abgeschlossen sind. Die [Stichtag](https://grpc.io/blog/deadlines) wird an den Server gesendet und der Server kann entscheiden, welche Aktionen durchzuführen sind, wenn den Stichtag überschritten. Beispielsweise kann die Server in Ausführung befindlichen gRPC/HTTP/Datenbank-Anforderungen bei einem Timeout abgebrochen werden.

Weitergabe der gemäß dem Stichtag und mittels der untergeordneten gRPC Aufrufe können Grenzwerte für die Ressourcenverwendung zu erzwingen.

## <a name="grpc-recommended-scenarios"></a>gRPC Szenarien empfohlen

gRPC eignet sich gut für die folgenden Szenarien:

* **Microservices** &ndash; gRPC ist für geringe Latenz und hohem Durchsatz Kommunikation konzipiert. gRPC eignet sich hervorragend für einfache Microservices, wobei Effizienz unbedingt.
* **Punkt zu Punkt-Kommunikation in Echtzeit** &ndash; gRPC bietet ausgezeichnete Unterstützung für bidirektionale streaming. gRPC-Dienste können Nachrichten ohne Abruf in Echtzeit übertragen.
* **Polygot Umgebungen** &ndash; gRPC-Tools unterstützt alle gängigen Entwicklungssprachen, wodurch gRPC eine gute Wahl für mehrsprachigen Umgebungen.
* **Eingeschränkte netzwerkumgebungen** &ndash; gRPC-Nachrichten mit Protobuf, einem schlanken Nachricht-Format serialisiert werden. Eine gRPC-Nachricht ist immer kleiner als eine entsprechende JSON-Nachricht.

## <a name="grpc-weaknesses"></a>gRPC Schwächen

### <a name="limited-browser-support"></a>Eingeschränkte Browserunterstützung

Es ist unmöglich, die heute direkt einen gRPC-Dienst in einem Browser aufrufen. gRPC verwendet häufig die HTTP/2-Features und nicht-Browser stellt die Steuerungsebene über webanforderungen zur Unterstützung einer gRPC-Clients erforderlich. Z. B. Browser nicht gestattet es einem Aufrufer erforderlich ist, dass HTTP/2 verwendet werden, oder ermöglichen den Zugriff auf zugrunde liegende HTTP/2-Frames.

[gRPC-Web-](https://grpc.io/docs/tutorials/basic/web.html) ist eine weitere Technologie aus dem gRPC-Team, das eingeschränkte gRPC-Unterstützung in den Browser enthält. gRPC-Website besteht aus zwei Teilen: einem JavaScript-Client, die von allen modernen Browsern und ein gRPC-Web-Proxy auf dem Server unterstützt. Der gRPC-Web-Client Ruft den Proxy, und der Proxy wird der gRPC-Anforderungen an den gRPC-Server weitergeleitet.

Nicht alle gRPC Funktionen werden von gRPC-Website unterstützt. Client und bidirektionale streaming wird nicht unterstützt, und besteht eingeschränkte Unterstützung für das streaming Server.

### <a name="not-human-readable"></a>Nicht lesbar

HTTP-API-Anforderungen können als Text gesendet und gelesen und durch Menschen erstellt werden.

gRPC-Nachrichten werden standardmäßig mit Protobuf codiert. Protobuf effizient zu senden und empfangen, seine Binärform ist nicht die Menschen lesbar. Protobuf erfordert der Nachricht schnittstellenbeschreibung angegeben, der `*.proto` Datei für die korrekte Deserialisierung. Zusätzliche Tools sind erforderlich, um Protobuf-Nutzlasten, bei der Übertragung zu analysieren und Anforderungen manuell erstellen.

Funktionen, z. B. [Server Reflektion](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) und [gRPC-Befehlszeilentool](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md) vorhanden sein, um Unterstützung bei der binären Protobuf-Nachrichten. Außerdem Nachrichten Protobuf Unterstützung [Konvertierung in und aus JSON-](https://developers.google.com/protocol-buffers/docs/proto3#json). Die integrierte JSON-Konvertierung bietet eine effiziente Möglichkeit, Protobuf-Nachrichten an und von Menschen lesbaren Format konvertieren, beim Debuggen.

## <a name="alternative-framework-scenarios"></a>Alternative Framework-Szenarios

Andere Frameworks werden über gRPC in den folgenden Szenarien empfohlen:

* **Browser zugänglich APIs** &ndash; gRPC wird vollständig im Browser nicht unterstützt. gRPC-Website bieten Unterstützung für Browser, aber es gelten Einschränkungen und stellt einen Server-Proxy.
* **Broadcast-Kommunikation in Echtzeit** &ndash; gRPC unterstützt die Kommunikation per streaming in Echtzeit, aber das Konzept der Übertragung einer Nachricht an die registrierten Verbindungen ist nicht vorhanden. Z. B. in einem Chatraum-Szenario, in dem neuen Chat-Nachrichten an alle Clients im Chatraum gesendet werden soll, jeden Aufruf gRPC muss einzeln neuen Chat-Nachrichten an den Client zu streamen. [SignalR](xref:signalr/introduction) ist eine nützliche Framework für dieses Szenario. SignalR ist das Konzept von permanenten Verbindungen und die integrierte Unterstützung zum Übertragen von Nachrichten.
* **Kommunikation zwischen Prozessen** &ndash; ein Prozess muss einen HTTP/2-Server zum Annehmen von eingehenden gRPC-Aufrufe hosten. Für Windows, Kommunikation zwischen Prozessen [Pipes](/dotnet/standard/io/pipe-operations) ist eine schnelle und einfache Methode der Kommunikation.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
