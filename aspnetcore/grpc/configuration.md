---
title: GrpC für .NET-Konfiguration
author: jamesnk
description: Erfahren Sie, wie Sie GrpC für .net-apps konfigurieren.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 09/05/2019
uid: grpc/configuration
ms.openlocfilehash: de478752f94713d7f3d55ed66e6410500c3a72e8
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75355731"
---
# <a name="grpc-for-net-configuration"></a>GrpC für .NET-Konfiguration

## <a name="configure-services-options"></a>Optionen für die Dienst Konfiguration

GrpC-Dienste werden in *Startup.cs*mit `AddGrpc` konfiguriert. In der folgenden Tabelle werden die Optionen zum Konfigurieren der GrpC-Dienste beschrieben:

| -Option | Standardwert | Beschreibung |
| ------ | ------------- | ----------- |
| `MaxSendMessageSize` | `null` | Die maximale Nachrichtengröße in Bytes, die vom Server gesendet werden kann. Der Versuch, eine Nachricht zu senden, die die konfigurierte maximale Nachrichtengröße überschreitet, führt zu einer Ausnahme. |
| `MaxReceiveMessageSize` | 4 MB | Die maximale Nachrichtengröße in Bytes, die vom Server empfangen werden kann. Wenn der Server eine Nachricht empfängt, die diesen Grenzwert überschreitet, wird eine Ausnahme ausgelöst. Das Erhöhen dieses Werts ermöglicht dem Server, größere Nachrichten zu empfangen, kann sich jedoch negativ auf die Arbeitsspeicher Auslastung auswirken. |
| `EnableDetailedErrors` | `false` | Wenn `true`, werden detaillierte Ausnahme Meldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Dienst Methode ausgelöst wird. Die Standardeinstellung ist `false`. Wenn Sie `EnableDetailedErrors` auf `true` festlegen, können vertrauliche Informationen nicht berücksichtigt werden. |
| `CompressionProviders` | gzip | Eine Auflistung von Komprimierungs Anbietern, die zum Komprimieren und Dekomprimieren von Nachrichten verwendet werden. Benutzerdefinierte Komprimierungs Anbieter können erstellt und der Auflistung hinzugefügt werden. Die konfigurierten Standardanbieter unterstützen **gzip** -Komprimierung. |
| `ResponseCompressionAlgorithm` | `null` | Der Komprimierungs Algorithmus, der zum Komprimieren der vom Server gesendeten Nachrichten verwendet wird. Der Algorithmus muss mit einem Komprimierungs Anbieter in `CompressionProviders`identisch sein. Damit der Algorithmus eine Antwort komprimieren kann, muss der Client angeben, dass er den Algorithmus unterstützt, indem er ihn im **GrpC-Accept-Encoding-** Header sendet. |
| `ResponseCompressionLevel` | `null` | Die Komprimierungs Ebene, mit der die vom Server gesendeten Nachrichten komprimiert werden. |
| `Interceptors` | Keine | Eine Auflistung von Interceptors, die mit jedem GrpC-Befehl ausgeführt werden. Interceptors werden in der Reihenfolge ausgeführt, in der Sie registriert sind. Global konfigurierte Interceptors werden ausgeführt, bevor Interceptors für einen einzelnen Dienst konfiguriert werden. Weitere Informationen zu GrpC-Interceptors finden Sie unter [GrpC-Interceptors und Middleware](xref:grpc/migration#grpc-interceptors-vs-middleware). |

Optionen können für alle Dienste konfiguriert werden, indem ein Options Delegat für den `AddGrpc` Aufruf in `Startup.ConfigureServices`bereitgestellt wird:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

Optionen für einen einzelnen Dienst überschreiben die in `AddGrpc` bereitgestellten globalen Optionen und können mithilfe `AddServiceOptions<TService>`konfiguriert werden:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a>Konfigurieren von Client Optionen

die GrpC-Client Konfiguration ist auf `GrpcChannelOptions`festgelegt. In der folgenden Tabelle werden die Optionen zum Konfigurieren von GrpC-Kanälen beschrieben:

| -Option | Standardwert | Beschreibung |
| ------ | ------------- | ----------- |
| `HttpClient` | Neue Instanz | Der `HttpClient`, der zum Erstellen von GrpC-Aufrufen verwendet wird. Ein Client kann festgelegt werden, um ein benutzerdefiniertes `HttpClientHandler`zu konfigurieren, oder der HTTP-Pipeline zusätzliche Handler für GrpC-Aufrufe hinzufügen. Wenn keine `HttpClient` angegeben ist, wird für den Kanal eine neue `HttpClient` Instanz erstellt. Sie wird automatisch verworfen. |
| `DisposeHttpClient` | `false` | Wenn `true`und ein `HttpClient` angegeben ist, wird die `HttpClient` Instanz verworfen, wenn die `GrpcChannel` verworfen wird. |
| `LoggerFactory` | `null` | Die `LoggerFactory`, die vom Client verwendet wird, um Informationen zu GrpC-aufrufen zu protokollieren. Eine `LoggerFactory` Instanz kann aus der Abhängigkeitsinjektion aufgelöst oder mithilfe `LoggerFactory.Create`erstellt werden. Beispiele für das Konfigurieren der Protokollierung finden Sie unter <xref:grpc/diagnostics#grpc-client-logging>. |
| `MaxSendMessageSize` | `null` | Die maximale Nachrichtengröße in Bytes, die vom Client gesendet werden kann. Der Versuch, eine Nachricht zu senden, die die konfigurierte maximale Nachrichtengröße überschreitet, führt zu einer Ausnahme. |
| `MaxReceiveMessageSize` | 4 MB | Die maximale Nachrichtengröße in Bytes, die vom Client empfangen werden kann. Wenn der Client eine Nachricht empfängt, die diesen Grenzwert überschreitet, wird eine Ausnahme ausgelöst. Das Erhöhen dieses Werts ermöglicht dem Client, größere Nachrichten zu empfangen, kann sich jedoch negativ auf die Arbeitsspeicher Auslastung auswirken. |
| `Credentials` | `null` | Eine `ChannelCredentials`-Instanz. Anmelde Informationen werden verwendet, um GrpC-aufrufen Authentifizierungs Metadaten hinzuzufügen. |
| `CompressionProviders` | gzip | Eine Auflistung von Komprimierungs Anbietern, die zum Komprimieren und Dekomprimieren von Nachrichten verwendet werden. Benutzerdefinierte Komprimierungs Anbieter können erstellt und der Auflistung hinzugefügt werden. Die konfigurierten Standardanbieter unterstützen **gzip** -Komprimierung. |

Der folgende Code

* Legt die maximale Größe für Sende-und Empfangs Nachrichten auf dem Kanal fest.
* Erstellt einen-Client.

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Weitere Ressourcen

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/diagnostics>
* <xref:tutorials/grpc/grpc-start>
