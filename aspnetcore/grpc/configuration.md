---
title: gRPC für ASP.NET Core-Konfiguration
author: jamesnk
description: Erfahren Sie, wie gRPC für ASP.NET Core-apps zu konfigurieren.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 05/30/2019
uid: grpc/configuration
ms.openlocfilehash: e269d701f45c0b852a9006107f0162cc5af2c38a
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67814923"
---
# <a name="grpc-for-aspnet-core-configuration"></a>gRPC für ASP.NET Core-Konfiguration

## <a name="configure-services-options"></a>Konfigurieren Sie Optionen für Dienste

Die folgende Tabelle beschreibt die Optionen zum Konfigurieren der gRPC-Dienste:

| Option | Standardwert | Beschreibung |
| ------ | ------------- | ----------- |
| `SendMaxMessageSize` | `null` | Die maximale Nachrichtengröße in Bytes, die vom Server gesendet werden können. Es wird versucht, eine Nachricht zu senden, die überschreitet die konfigurierte maximale Nachricht Größe führt zu einer Ausnahme. |
| `ReceiveMaxMessageSize` | 4 MB | Die maximale Nachrichtengröße in Bytes, die vom Server empfangen werden können. Wenn der Server eine Nachricht, die diesen Grenzwert überschreitet empfängt, wird eine Ausnahme ausgelöst. Durch Erhöhen dieses Wertes können den Server, größere Nachrichten zu empfangen, aber arbeitsspeichernutzung negativ beeinträchtigt werden kann. |
| `EnableDetailedErrors` | `false` | Wenn `true`, detaillierte ausnahmemeldungen werden an Clients zurückgegeben, wenn eine Ausnahme in einer Dienstmethode ausgelöst wird. Die Standardeinstellung ist `false`. Festlegen von `EnableDetailedErrors` zu `true` kann Offenlegung von vertraulichen Informationen. |
| `CompressionProviders` | gzip | Eine Auflistung von Komprimierung-Anbietern, die zum Komprimieren und Dekomprimieren von Nachrichten verwendet werden soll. Benutzerdefinierte Komprimierung-Anbieter können erstellt und der Auflistung hinzugefügt werden. Standardmäßig konfiguriert der Anbieter unterstützt **Gzip** Komprimierung. |
| `ResponseCompressionAlgorithm` | `null` | Der Komprimierungsalgorithmus verwendet, um vom Server gesendeten Nachrichten zu komprimieren. Der Algorithmus muss einen Anbieter Komprimierung in übereinstimmen `CompressionProviders`. Für den Algorithmus, um eine Antwort zu komprimieren, muss der Client angeben, senden Sie sie der Algorithmus unterstützt die **Grpc-accept-encoding** Header. |
| `ResponseCompressionLevel` | `null` | Die Compress-Ebene verwendet, um vom Server gesendeten Nachrichten zu komprimieren. |

Optionen für alle Dienste konfiguriert werden können, durch die Bereitstellung einer Optionen-Delegat, der die `AddGrpc` Aufrufen in `Startup.ConfigureServices`:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

Optionen für einen einzelnen Dienst außer Kraft setzen die globale Optionen im `AddGrpc` und kann konfiguriert werden, mithilfe von `AddServiceOptions<TService>`:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a>Konfigurieren Sie Clientoptionen

Der folgende Code legt die maximale Client senden und Empfangen der Nachrichtengröße:

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-6)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
