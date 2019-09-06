---
title: Vergleich von gRPC-Diensten mit HTTP-APIs
author: jamesnk
description: Erfahren Sie, wie sich GrpC mit http-APIs vergleicht und welche Szenarios empfohlen werden.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 03/31/2019
uid: grpc/comparison
ms.openlocfilehash: c34c7ecb668e478e2be3271928a2439979a746d9
ms.sourcegitcommit: 8b36f75b8931ae3f656e2a8e63572080adc78513
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/05/2019
ms.locfileid: "70310470"
---
# <a name="comparing-grpc-services-with-http-apis"></a>Vergleich von gRPC-Diensten mit HTTP-APIs

Von [James Newton-King](https://twitter.com/jamesnk)

In diesem Artikel wird erläutert, wie [GrpC-Dienste](https://grpc.io/docs/guides/) mit http-APIs (einschließlich ASP.net Core [Web-APIs](xref:web-api/index)) verglichen werden. Die Technologie, mit der eine API für Ihre APP bereitgestellt wird, ist eine wichtige Wahl, und GrpC bietet im Vergleich zu http-APIs besondere Vorteile. In diesem Artikel werden die Stärken und Schwächen von GrpC erläutert und Szenarios für die Verwendung von GrpC gegenüber anderen Technologien empfohlen.

#### <a name="overview"></a>Übersicht

| Feature          | gRPC                                               | HTTP-APIs mit JSON           |
| ---------------- | -------------------------------------------------- | ----------------------------- |
| Vertrag         | Erforderlich ( *. proto*)                                | Optional (OpenAPI)            |
| Transport        | HTTP/2                                             | HTTP                          |
| Payload          | [Protobuf (Small, Binary)](#performance)           | JSON (groß, Menschen lesbar)  |
| Präskriptivität | [Strikte Spezifikation](#strict-specification)      | Lose. Jeder http-Wert ist gültig.      |
| Streaming        | [Client, Server, bidirektional](#streaming)       | Client, Server                |
| Browser Unterstützung  | [Nein (erfordert GrpC-Web)](#limited-browser-support) | Ja                           |
| Sicherheit         | Transport (HTTPS)                                  | Transport (HTTPS)             |
| Client Code-gen  | [Ja](#code-generation)                            | OpenAPI + Tools von Drittanbietern |

## <a name="grpc-strengths"></a>GrpC-stärken

### <a name="performance"></a>Leistung

GrpC-Nachrichten werden mithilfe von [protobuf](https://developers.google.com/protocol-buffers/docs/overview)serialisiert, einem effizienten binären Nachrichtenformat. Protobuf wird sehr schnell auf dem Server und dem Client serialisiert. Die protobuf-Serialisierung führt zu kleinen Nachrichten Nutzlasten, die für begrenzte Bandbreiten Szenarien wie Mobile Apps wichtig sind.

GrpC ist für http/2 konzipiert, eine wichtige Überarbeitung von http, die gegenüber HTTP 1. x bedeutende Leistungsvorteile bietet:

* Binäre Rahmen und Komprimierung. Das http/2-Protokoll ist für das Senden und empfangen kompakt und effizient.
* Multiplexing mehrerer http/2-Aufrufe über eine einzelne TCP-Verbindung. Multiplexing beseitigt die [zeilenweise Blockierung](https://en.wikipedia.org/wiki/Head-of-line_blocking).

### <a name="code-generation"></a>Codeerzeugung

Alle GrpC-Frameworks bieten erstklassige Unterstützung für die Codegenerierung. Eine Kerndatei für die GrpC-Entwicklung ist die Datei " [ *. proto* ](https://developers.google.com/protocol-buffers/docs/proto3)", die den Vertrag der GrpC-Dienste und-Nachrichten definiert. Aus dieser Datei werden von den GrpC-Frameworks Code generiert, um eine Dienst Basisklasse, Nachrichten und einen kompletten Client zu generieren.

Durch die Freigabe `*.proto` der Datei zwischen Server und Client können Nachrichten und Client Code von Ende zu Ende generiert werden. Die Code Generierung des Clients vermeidet das Duplizieren von Nachrichten auf dem Client und dem Server und erstellt einen stark typisierten Client für Sie. Wenn Sie keinen Client schreiben müssen, sparen Sie in Anwendungen mit vielen Diensten eine beträchtliche Entwicklungszeit.

### <a name="strict-specification"></a>Strikte Spezifikation

Eine formale Spezifikation für die HTTP-API mit JSON ist nicht vorhanden. Entwickler diskutieren das beste Format von URLs, HTTP-Verben und Antwort Codes.

Die [GrpC-Spezifikation](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) ist mit dem Format versehen, das einem GrpC-Dienst folgen muss. GrpC beseitigt die Diskussion und spart Entwicklerzeit, da gprc plattformübergreifend und Implementierungen einheitlich ist.

### <a name="streaming"></a>Streaming

HTTP/2 bietet eine Grundlage für langlebige Echt Zeit Kommunikationsstreams. GrpC bietet erstklassige Unterstützung für das Streaming über http/2.

Ein GrpC-Dienst unterstützt alle streamingkombinationen:

* Unär (kein Streaming)
* Streaming zwischen Server und Client
* Streaming von Client zu Server
* Bidirektionales Streaming

### <a name="deadlinetimeouts-and-cancellation"></a>Stichtag/Timeouts und Abbruch

mit GrpC können Clients angeben, wie lange Sie warten sollen, bis ein RPC-Vorgang beendet ist. Der [Stichtag](https://grpc.io/blog/deadlines) wird an den Server gesendet, und der Server kann entscheiden, welche Aktion ausgeführt werden soll, wenn er den Stichtag überschreitet. Beispielsweise kann der Server in Bearbeitung befindliche GrpC/http-/Datenbankanforderungen bei einem Timeout abbrechen.

Durch die Weitergabe des Stichtags und des Abbruchs durch untergeordnete GrpC-Anrufe können Sie Ressourcen Verwendungs Limits

## <a name="grpc-recommended-scenarios"></a>Empfohlene GrpC-Szenarios

GrpC eignet sich gut für die folgenden Szenarien:

* **Mikrodienste** &ndash; GrpC ist für die Kommunikation mit geringer Latenz und hohem Durchsatz konzipiert. GrpC eignet sich hervorragend für Lightweight-Dienste, bei denen die Effizienz wichtig ist.
* **Point-to-Point-Echtzeitkommunikation** &ndash; GrpC bietet hervorragende Unterstützung für bidirektionales Streaming. GrpC-Dienste können Nachrichten in Echtzeit ohne Abruf per Push Übertragung.
* **Polyglot-Umgebungen** &ndash; GrpC-Tools unterstützen alle gängigen Entwicklungs Sprachen, sodass GrpC für mehrsprachige Umgebungen eine gute Wahl ist.
* **Eingeschränkte Netzwerkumgebungen** &ndash; GrpC-Nachrichten werden mit protobuf serialisiert, einem Lightweight-Nachrichtenformat. Eine GrpC-Nachricht ist immer kleiner als eine entsprechende JSON-Nachricht.

## <a name="grpc-weaknesses"></a>GrpC-Schwächen

### <a name="limited-browser-support"></a>Eingeschränkte Browserunterstützung

Es ist nicht möglich, einen GrpC-Dienst direkt von einem Browser aus aufzurufen. GrpC verwendet häufig http/2-Features, und kein Browser bietet die erforderliche Kontrolle über Webanforderungen zur Unterstützung eines GrpC-Clients. Browser gestatten z. b. nicht, dass ein Aufrufer die Verwendung von http/2 erfordert oder den Zugriff auf zugrunde liegende http/2-Frames ermöglicht.

[GrpC-Web](https://grpc.io/docs/tutorials/basic/web.html) ist eine zusätzliche Technologie vom GrpC-Team, die im Browser eingeschränkte GrpC-Unterstützung bietet. GrpC-Web besteht aus zwei Teilen: einem JavaScript-Client, der alle modernen Browser unterstützt, und einem GrpC-WebProxy auf dem Server. Der GrpC-WebClient Ruft den Proxy auf, und der Proxy wird an den GrpC-Anforderungen an den GrpC-Server weiterleiten.

Nicht alle Funktionen von GrpC werden von GrpC-Web unterstützt. Client und bidirektionales Streaming werden nicht unterstützt, und es gibt nur eingeschränkte Unterstützung für Server Streaming.

### <a name="not-human-readable"></a>Nicht Menschen lesbar

HTTP-API-Anforderungen werden als Text gesendet und können von Menschen gelesen und erstellt werden.

GrpC-Nachrichten werden standardmäßig mit protobuf codiert. Obwohl protobuf für das Senden und empfangen effizient ist, ist das binäre Format nicht Menschen lesbar. Protobuf erfordert, dass die in der `*.proto` Datei angegebene Schnittstellen Beschreibung der Nachricht ordnungsgemäß deserialisiert wird. Zusätzliche Tools sind erforderlich, um protobuf-Nutzlasten bei der Übertragung zu analysieren und Anforderungen per Hand zu verfassen.

Funktionen wie die [Server Reflektion](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) und das [GrpC-Befehlszeilen Tool](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md) sind zur Unterstützung bei binären protobuf-Meldungen vorhanden. Protobuf-Nachrichten unterstützen außerdem [die Konvertierung in und aus JSON](https://developers.google.com/protocol-buffers/docs/proto3#json). Die integrierte JSON-Konvertierung bietet eine effiziente Möglichkeit zum Konvertieren von protobuf-Nachrichten in und aus der lesbaren Form beim Debuggen.

## <a name="alternative-framework-scenarios"></a>Alternative frameworkszenarien

Andere Frameworks werden in den folgenden Szenarien über GrpC empfohlen:

* **Verfügbare Browser-APIs** &ndash; GrpC wird im Browser nicht vollständig unterstützt. GrpC-Web kann Browserunterstützung bieten, aber es gibt Einschränkungen und führt einen Server Proxy ein.
* **Senden der Echtzeitkommunikation** &ndash; GrpC unterstützt die Echtzeitkommunikation über das Streaming, aber das Konzept, eine Nachricht an registrierte Verbindungen zu senden, ist nicht vorhanden. Beispielsweise ist in einem Chatroom-Szenario, in dem neue Chat Nachrichten an alle Clients im Chatraum gesendet werden sollen, jeder GrpC-Rückruf erforderlich, um neue Chat Nachrichten einzeln an den Client zu streamen. [Signalr](xref:signalr/introduction) ist ein nützliches Framework für dieses Szenario. Signalr hat das Konzept von permanenten Verbindungen und integrierter Unterstützung für das Senden von Nachrichten.
* **Prozessübergreifende Kommunikation** &ndash; Ein Prozess muss einen http/2-Server hosten, um eingehende GrpC-Aufrufe zu akzeptieren. Bei Windows handelt es sich bei prozessübergreifenden [kommunikationspipes](/dotnet/standard/io/pipe-operations) um eine schnelle, leichte Kommunikationsmethode.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
