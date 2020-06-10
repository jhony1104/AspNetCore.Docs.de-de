---
title: 'title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:'
author: jamesnk
description: "'Blazor'"
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 05/26/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/configuration
ms.openlocfilehash: a90735c6cd99de30f168fb1498e705de2d6887ca
ms.sourcegitcommit: 58722eb309767e462bdbf3082bd38737a4ef168f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84106116"
---
# <a name="grpc-for-net-configuration"></a>'Identity'

## <a name="configure-services-options"></a>'Let's Encrypt'

'Razor' 'SignalR' uid:

| gRPC für .NET-Konfiguration | Konfigurieren von Dienstoptionen | gRPC-Dienste werden mit `AddGrpc` in *Startup.cs* konfiguriert. |
| ------ | ------------- | ----------- |
| In der folgenden Tabelle werden die Optionen für die Konfiguration von gRPC-Diensten beschrieben: | `null` | Option Standardwert Beschreibung |
| title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: | 'Blazor' | 'Identity' 'Let's Encrypt' 'Razor' 'SignalR' uid: |
| --- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: | `false` | 'Blazor' 'Identity' 'Let's Encrypt' |
| 'Razor' | 'SignalR' uid: | title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor' 'Identity' |
| 'Let's Encrypt' | `null` | 'Razor' 'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: |
| 'Blazor' | `null` | 'Identity' |
| 'Let's Encrypt' | 'Razor' | 'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor' 'Identity' |
| 'Let's Encrypt' | `false` | 'Razor' |

'SignalR' uid:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a>'Blazor'

'Identity' 'Let's Encrypt'

| 'Razor' | 'SignalR' uid: | title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: |
| ------ | ------------- | ----------- |
| 'Blazor' | 'Identity' | 'Let's Encrypt' 'Razor' 'SignalR' uid: |
| title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: | `null` | 'Blazor' 'Identity' |
| 'Let's Encrypt' | `false` | 'Razor' |
| 'SignalR' uid: | `null` | ------ | | MaxSendMessageSize | `null` | Die maximale Nachrichtengröße in Bytes, die vom Server gesendet werden kann. Der Versuch, eine Nachricht zu senden, die die konfigurierte maximale Nachrichtengröße überschreitet, führt zu einer Ausnahme. Wenn dieser Wert auf `null` festgelegt wird, ist die Größe der Nachricht unbegrenzt. |
| | | MaxReceiveMessageSize | 4 MB | Die maximale Nachrichtengröße in Bytes, die vom Server empfangen werden kann. | `null` | Wenn der Server eine Nachricht erhält, die diesen Grenzwert überschreitet, wird eine Ausnahme ausgelöst. Eine Erhöhung dieses Werts ermöglicht es dem Server, größere Nachrichten zu empfangen, kann sich jedoch negativ auf den Arbeitsspeicherverbrauch auswirken. Wenn dieser Wert auf `null` festgelegt wird, ist die Größe der Nachricht unbegrenzt. |
| | | EnableDetailedErrors | `false` | Bei `true` werden detaillierte Ausnahmemeldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Dienstmethode ausgelöst wird. | Der Standardwert ist `false`. | Das Festlegen von `EnableDetailedErrors` auf `true` kann zum Verlust von vertraulichen Informationen führen. | | CompressionProviders | gzip | Eine Sammlung von Komprimierungsanbietern, die zum Komprimieren und Dekomprimieren von Nachrichten verwendet werden. Es können benutzerdefinierte Komprimierungsanbieter erstellt und der Sammlung hinzugefügt werden. Die standardmäßig konfigurierten Anbieter unterstützen die **gzip**-Komprimierung. |
| | | <span style="word-break:normal;word-wrap:normal">ResponseCompressionAlgorithm</span> | `null` | Der Komprimierungsalgorithmus, der zur Komprimierung der vom Server gesendeten Nachrichten verwendet wird. | `null` | Der Algorithmus muss mit einem Komprimierungsanbieter in `CompressionProviders` übereinstimmen. Damit der Algorithmus eine Antwort komprimieren kann, muss der Client angeben, dass er den Algorithmus unterstützt, indem er ihn im **grpc-accept-encoding**-Header sendet. |
| | | ResponseCompressionLevel | `null` | Die Komprimierungsstufe, die zur Komprimierung der vom Server gesendeten Nachrichten verwendet wird. | | | Interceptors | None | Eine Sammlung von Interceptors, die bei jedem gRPC-Aufruf ausgeführt werden. | Interceptors werden in der Reihenfolge ausgeführt, in der sie registriert sind. Global konfigurierte Interceptors werden vor Interceptors ausgeführt, die für einen einzelnen Dienst konfiguriert sind. Weitere Informationen über gRPC-Interceptors finden Sie unter [gRPC-Interceptors im Vergleich zur Middleware](xref:grpc/migration#grpc-interceptors-vs-middleware). |

| | IgnoreUnknownServices | `false` | Bei `true` geben Aufrufe unbekannter Dienste und Methoden nicht den Status **UNIMPLEMENTED** zurück, und die Anforderung wird an die nächste registrierte Middleware in ASP.NET Core übergeben.

* Optionen können für alle Dienste konfiguriert werden, indem ein Optionsdelegat für den `AddGrpc`-Aufruf in `Startup.ConfigureServices` bereitgestellt wird:
* Optionen für einen einzelnen Dienst haben Vorrang vor den globalen Optionen in `AddGrpc` und können mit `AddServiceOptions<TService>` konfiguriert werden:

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Konfigurieren von Clientoptionen

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/diagnostics>
* <xref:tutorials/grpc/grpc-start>
