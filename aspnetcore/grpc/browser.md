---
title: GrpC in Browser-apps
author: jamesnk
description: Erfahren Sie, wie Sie GrpC-Dienste auf ASP.net Core konfigurieren können, um von Browser-Apps mithilfe von GrpC-Web aufgerufen zu werden.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/24/2020
uid: grpc/browser
ms.openlocfilehash: 6359c3b76b3cb1ba2b6d9f9a989f64cbf4c4379d
ms.sourcegitcommit: b5ceb0a46d0254cc3425578116e2290142eec0f0
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2020
ms.locfileid: "76830633"
---
# <a name="grpc-in-browser-apps"></a>GrpC in Browser-apps

Von [James Newton-King](https://twitter.com/jamesnk)

> [!IMPORTANT]
> **GrpC-Webunterstützung in .net ist experimentell**
>
> GrpC-Web für .net ist ein experimentelles Projekt, nicht ein Commit-Produkt. Wir möchten:
>
> * Testen Sie, ob unser Ansatz für die Implementierung von GrpC-Web funktioniert.
> * Informieren Sie sich darüber, ob diese Vorgehensweise für .NET-Entwickler nützlich ist, wenn Sie mit dem herkömmlichen Verfahren zum Einrichten von GrpC-Web über einen Proxy arbeiten.
>
> Bitte lassen Sie uns Feedback zu [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) , um sicherzustellen, dass wir eine solche erstellen, die Entwickler wie und produktiv sind.

Es ist nicht möglich, einen http/2-GrpC-Dienst aus einer browserbasierten App aufzurufen. [GrpC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) ist ein Protokoll, mit dem Browser-JavaScript-und blazor-apps GrpC-Dienste anrufen können. In diesem Artikel wird erläutert, wie Sie GrpC-Web in .net Core verwenden.

## <a name="configure-grpc-web-in-aspnet-core"></a>Konfigurieren von GrpC-Web in ASP.net Core

in ASP.net Core gehostete GrpC-Dienste können so konfiguriert werden, dass GrpC-Web neben http/2-GrpC unterstützt wird. für GrpC-Web sind keine Änderungen an Diensten erforderlich. Die einzige Änderung ist die Startkonfiguration.

So aktivieren Sie GrpC-Web mit einem ASP.net Core GrpC-Dienst:

* Fügen Sie einen Verweis auf das Paket " [GrpC. aspnetcore. Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) " hinzu.
* Konfigurieren Sie die APP für die Verwendung von GrpC-Web, indem Sie `AddGrpcWeb` und `UseGrpcWeb` zu *Startup.cs*hinzufügen:

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=3,10,14)]

Der vorangehende Code:

* Fügt die GrpC-Web-Middleware, die `UseGrpcWeb`nach dem Routing und vor Endpunkten hinzu.
* Gibt an, dass die `endpoints.MapGrpcService<GreeterService>()`-Methode GrpC-Web mit `EnableGrpcWeb`unterstützt. 

Alternativ dazu können Sie alle Dienste so konfigurieren, dass GrpC-Web unterstützt wird, indem Sie `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` zu Configuration Services hinzufügen.

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=5,12,16)]

Möglicherweise sind einige zusätzliche Konfigurationen erforderlich, um GrpC-Web über den Browser aufzurufen, z. b. das Konfigurieren von ASP.net Core zur Unterstützung von cors. Weitere Informationen finden Sie [unter Unterstützung von cors](xref:security/cors).

## <a name="call-grpc-web-from-the-browser"></a>GrpC-Web aus dem Browser abrufen

Browser-Apps können GrpC-Web verwenden, um GrpC-Dienste aufzurufen. Beim Aufrufen von GrpC-Diensten mit GrpC-Web über den Browser gelten einige Anforderungen und Einschränkungen:

* Der Server muss für die Unterstützung von GrpC-Web konfiguriert worden sein.
* Client Streaming und bidirektionale Streaming-Aufrufe werden nicht unterstützt. Server Streaming wird unterstützt.
* Zum Aufrufen von GrpC-Diensten in einer anderen Domäne müssen [cors](xref:security/cors) auf dem Server konfiguriert werden.

### <a name="javascript-grpc-web-client"></a>JavaScript-GrpC-WebClient

Es ist ein JavaScript-GrpC-WebClient vorhanden. Anweisungen zur Verwendung von GrpC-Web aus JavaScript finden Sie unter [Schreiben von JavaScript-Client Code mit GrpC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).

### <a name="configure-grpc-web-with-the-net-grpc-client"></a>Konfigurieren von GrpC-Web mit dem .net-GrpC-Client

Der .net-GrpC-Client kann so konfiguriert werden, dass er GrpC-Web-Aufrufe aufruft. Dies ist nützlich für [blazor Webassembly](xref:blazor/index#blazor-webassembly) -apps, die im Browser gehostet werden und dieselben http-Einschränkungen von JavaScript-Code aufweisen. Das Aufrufen von GrpC-Web mit einem .NET-Client ist mit [dem http/2-GrpC identisch](xref:grpc/client). Die einzige Änderung ist die Art und Weise, wie der Kanal erstellt wird.

So verwenden Sie GrpC-Web:

* Fügen Sie einen Verweis auf das [GrpC .net. Client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) -Paket hinzu.
* Konfigurieren Sie den Kanal für die Verwendung der `GrpcWebHandler`:

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

Der vorangehende Code:

* Konfiguriert einen Kanal für die Verwendung von GrpC-Web.
* Erstellt einen Client und führt einen-Befehl mithilfe des Kanals aus.

Die `GrpcWebHandler` verfügt bei der Erstellung über die folgenden Konfigurationsoptionen:

* **Innerhandler**: die zugrunde liegende <xref:System.Net.Http.HttpMessageHandler>, die den HTTP-Befehl ausführt, z. b. `HttpClientHandler`.
* **Mode**: `GrpcWebMode` Enum. `GrpcWebMode.GrpcWebText` konfiguriert Inhalte für die Base64-Codierung, die zur Unterstützung von serverstreamingaufrufen erforderlich ist.
* **HttpVersion**: http-Protokoll `Version`. für "GrpC-Web" ist kein bestimmtes Protokoll erforderlich, und es wird kein bestimmtes Protokoll angegeben, wenn eine Anforderung nicht konfiguriert wird.

## <a name="additional-resources"></a>Weitere Ressourcen

* [GrpC für Webclients GitHub-Projekt](https://github.com/grpc/grpc-web)
* <xref:security/cors>
