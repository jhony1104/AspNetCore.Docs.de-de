---
title: GrpC für .NET-Konfiguration
author: jamesnk
description: Erfahren Sie, wie Sie GrpC für .net-apps konfigurieren.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 09/05/2019
uid: grpc/configuration
ms.openlocfilehash: 3ef92f10d914ef9fa3e13a7bdd5c863bab297f57
ms.sourcegitcommit: 73e255e846e414821b8cc20ffa3aec946735cd4e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/03/2019
ms.locfileid: "71925208"
---
# <a name="grpc-for-net-configuration"></a>GrpC für .NET-Konfiguration

## <a name="configure-services-options"></a>Optionen für die Dienst Konfiguration

GrpC-Dienste werden mit `AddGrpc` in *Startup.cs*konfiguriert. In der folgenden Tabelle werden die Optionen zum Konfigurieren der GrpC-Dienste beschrieben:

| Option | Standardwert | Beschreibung |
| ------ | ------------- | ----------- |
| `MaxSendMessageSize` | `null` | Die maximale Nachrichtengröße in Bytes, die vom Server gesendet werden kann. Der Versuch, eine Nachricht zu senden, die die konfigurierte maximale Nachrichtengröße überschreitet, führt zu einer Ausnahme. |
| `MaxReceiveMessageSize` | 4 MB | Die maximale Nachrichtengröße in Bytes, die vom Server empfangen werden kann. Wenn der Server eine Nachricht empfängt, die diesen Grenzwert überschreitet, wird eine Ausnahme ausgelöst. Das Erhöhen dieses Werts ermöglicht dem Server, größere Nachrichten zu empfangen, kann sich jedoch negativ auf die Arbeitsspeicher Auslastung auswirken. |
| `EnableDetailedErrors` | `false` | Wenn `true`der Wert ist, werden ausführliche Ausnahme Meldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Dienst Methode ausgelöst wird. Die Standardeinstellung ist `false`. Durch `EnableDetailedErrors` festlegen `true` von auf können vertrauliche Informationen nicht berücksichtigt werden. |
| `CompressionProviders` | gzip | Eine Auflistung von Komprimierungs Anbietern, die zum Komprimieren und Dekomprimieren von Nachrichten verwendet werden. Benutzerdefinierte Komprimierungs Anbieter können erstellt und der Auflistung hinzugefügt werden. Die konfigurierten Standardanbieter unterstützen **gzip** -Komprimierung. |
| `ResponseCompressionAlgorithm` | `null` | Der Komprimierungs Algorithmus, der zum Komprimieren der vom Server gesendeten Nachrichten verwendet wird. Der-Algorithmus muss einem Komprimierungs Anbieter `CompressionProviders`in entsprechen. Damit der Algorithmus eine Antwort komprimieren kann, muss der Client angeben, dass er den Algorithmus unterstützt, indem er ihn im **GrpC-Accept-Encoding-** Header sendet. |
| `ResponseCompressionLevel` | `null` | Die Komprimierungs Ebene, mit der die vom Server gesendeten Nachrichten komprimiert werden. |

Optionen können für alle Dienste konfiguriert werden, indem ein Options Delegat für `AddGrpc` den Aufruf `Startup.ConfigureServices`in bereitgestellt wird:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

Optionen für einen einzelnen Dienst überschreiben die in `AddGrpc` bereitgestellten globalen Optionen und können mithilfe `AddServiceOptions<TService>`von konfiguriert werden:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a>Konfigurieren von Client Optionen

die GrpC-Client Konfiguration ist `GrpcChannelOptions`auf ON festgelegt. In der folgenden Tabelle werden die Optionen zum Konfigurieren von GrpC-Kanälen beschrieben:

| Option | Standardwert | Beschreibung |
| ------ | ------------- | ----------- |
| `HttpClient` | Neue Instanz | Der `HttpClient` , der zum Erstellen von GrpC-Aufrufen verwendet wird Ein Client kann festgelegt werden, um ein `HttpClientHandler`benutzerdefiniertes zu konfigurieren, oder der HTTP-Pipeline zusätzliche Handler für GrpC-Aufrufe hinzufügen. Wenn kein `HttpClient` angegeben wird, wird eine neue `HttpClient` -Instanz für den Kanal erstellt. Sie wird automatisch verworfen. |
| `DisposeHttpClient` | `false` | Wenn `true`, und ein `HttpClient` angegeben wird, wird die `HttpClient` -Instanz verworfen, wenn der `GrpcChannel` verworfen wird. |
| `LoggerFactory` | `null` | Der `LoggerFactory` , der vom Client verwendet wird, um Informationen zu GrpC-aufrufen zu protokollieren. Eine `LoggerFactory` -Instanz kann aus der Abhängigkeitsinjektion aufgelöst oder `LoggerFactory.Create`mithilfe von erstellt werden. Beispiele für das Konfigurieren der Protokollierung <xref:grpc/diagnostics#grpc-client-logging>finden Sie unter. |
| `MaxSendMessageSize` | `null` | Die maximale Nachrichtengröße in Bytes, die vom Client gesendet werden kann. Der Versuch, eine Nachricht zu senden, die die konfigurierte maximale Nachrichtengröße überschreitet, führt zu einer Ausnahme. |
| `MaxReceiveMessageSize` | 4 MB | Die maximale Nachrichtengröße in Bytes, die vom Client empfangen werden kann. Wenn der Client eine Nachricht empfängt, die diesen Grenzwert überschreitet, wird eine Ausnahme ausgelöst. Das Erhöhen dieses Werts ermöglicht dem Client, größere Nachrichten zu empfangen, kann sich jedoch negativ auf die Arbeitsspeicher Auslastung auswirken. |
| `Credentials` | `null` | Eine `ChannelCredentials`-Instanz. Anmelde Informationen werden verwendet, um GrpC-aufrufen Authentifizierungs Metadaten hinzuzufügen. |
| `CompressionProviders` | gzip | Eine Auflistung von Komprimierungs Anbietern, die zum Komprimieren und Dekomprimieren von Nachrichten verwendet werden. Benutzerdefinierte Komprimierungs Anbieter können erstellt und der Auflistung hinzugefügt werden. Die konfigurierten Standardanbieter unterstützen **gzip** -Komprimierung. |

Der folgende Code

* Legt die maximale Größe für Sende-und Empfangs Nachrichten auf dem Kanal fest.
* Erstellt einen-Client.

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/diagnostics>
* <xref:tutorials/grpc/grpc-start>
