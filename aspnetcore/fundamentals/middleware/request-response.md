---
title: Anforderungs- und Antwortvorgänge in ASP.NET Core
author: jkotalik
description: Erfahren Sie, wie Sie in ASP.NET Core den Anforderungstext lesen und den Antworttext schreiben.
monikerRange: '>= aspnetcore-3.0'
ms.author: jkotalik
ms.custom: mvc
ms.date: 02/26/2019
uid: fundamentals/middleware/request-response
ms.openlocfilehash: b6e3cd4b79e0c062b271c65cd5ecbdb4ef80c3a1
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58208056"
---
# <a name="request-and-response-operations-in-aspnet-core"></a>Anforderungs- und Antwortvorgänge in ASP.NET Core

Von [Justin Kotalik](https://github.com/jkotalik)

In diesem Artikel wird erläutert, wie Sie den Anforderungstext lesen und den Antworttext schreiben. Sie müssen möglicherweise Code für diese Vorgänge schreiben, wenn Sie Middleware schreiben. Andernfalls müssen Sie diesen Code in der Regel nicht schreiben, da die Vorgänge von MVC und Razor Pages behandelt werden.

In ASP.NET Core 3.0 stehen zwei Abstraktionen für Anforderungs- und Antworttext zur Verfügung: <xref:System.IO.Stream> und <xref:System.IO.Pipelines.Pipe>. [HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) ist ein <xref:System.IO.Stream> zum Lesen der Anforderung, und `HttpRequest.BodyPipe` ist ein <xref:System.IO.Pipelines.PipeReader>. [HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) ist eine `HttpResponse.BodyPipe` zum Schreiben der Antwort – ein <xref:System.IO.Pipelines.PipeWriter>.

Wir empfehlen Pipelines über Datenströme. Datenströme können bei einigen einfachen Vorgängen einfacher zu verwenden sein, aber Pipelines haben einen Leistungsvorteil und sind in den meisten Szenarien einfacher zu verwenden. Ab 3.0 verwendet ASP.NET Core intern Pipelines anstelle von Datenströmen. Beispiele:

- `FormReader`
- `TextReader`
- `TexWriter`
- `HttpResponse.WriteAsync`

Datenströme verschwinden nicht. Sie werden weiterhin überall in .NET verwendet, und viele Datenstromtypen wie `FileStreams` und `ResponseCompression` haben keine Pipelineentsprechungen.

## <a name="stream-examples"></a>Datenstrombeispiele

Nehmen wir an, Sie möchten eine Middleware erstellen, die den gesamten Anforderungstext als Liste von Zeichenfolgen liest, die in neue Zeilen umbrochen werden. Eine einfache Datenstromimplementierung könnte wie im folgenden Beispiel aussehen:

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]

Dieser Code funktioniert, aber es gibt einige Probleme:

- Vor dem Anfügen an den `StringBuilder` wird im Beispiel eine andere Zeichenfolge erstellt (`encodedString`), die sofort verworfen wird. Weil dieser Vorgang für alle Bytes im Datenstrom erfolgt, ist das Ergebnis eine zusätzliche Speicherzuweisung in der Größe des gesamten Anforderungstexts.
- Im Beispiel wird die gesamte Zeichenfolge vor dem Umbruch in neue Zeilen gelesen. Es wäre viel effizienter, das Bytearray auf neue Zeilen zu überprüfen.

Hier ist ein Beispiel, das einige dieser Probleme behebt:

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

Dieses Beispiel:

- Puffert nicht den gesamten Anforderungstext in einem `StringBuilder`, wenn keine Zeilenvorschubzeichen vorhanden sind.
- Ruft nicht `Split` für die Zeichenfolge auf.

Es gibt jedoch noch ein paar Probleme:

- Wenn neue Zeilenvorschubzeichen rar sind, wird ein großer Teil des Anforderungstexts in der Zeichenfolge gepuffert.
- Dennoch werden Zeichenfolgen erstellt (`remainingString`) und dem Zeichenfolgenpuffer hinzugefügt, woraus eine zusätzliche Zuordnung resultiert.

Diese Probleme können behoben werden, aber der Code wird immer komplizierter, und die Verbesserung ist gering. Pipelines bieten eine Möglichkeit, diese Probleme mit minimaler Codekomplexität zu lösen.

## <a name="pipelines"></a>Pipelines

Das folgende Beispiel zeigt, wie das gleiche Szenario mit einem `PipeReader` behandelt werden kann:

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

In diesem Beispiel werden viele Probleme der Datenstromimplementierungen behoben:

- Ein Zeichenfolgenpuffer ist nicht erforderlich, weil der `PipeReader` Bytes behandelt, die nicht verwendet wurden.
- Codierte Zeichenfolgen werden direkt der Liste der zurückgegebenen Zeichenfolgen hinzugefügt.
- Die Erstellung von Zeichenfolgen ist zuordnungsfrei, abgesehen von dem Speicher, den die Zeichenfolge belegt (mit Ausnahme des `ToArray()`-Aufrufs).

## <a name="adapters"></a>Adapter

Was geschieht, nachdem jetzt die Eigenschaften `Body` und `BodyPipe` beide für `HttpRequest` und `HttpResponse` verfügbar sind, wenn Sie für `Body` einen anderen Datenstrom festlegen? Eine neue Reihe von Adaptern in 3.0 passt jeden Typ automatisch einem anderen an. Wenn Sie z.B. für `HttpRequest.Body` einen neuen Datenstrom festlegen, wird für `HttpRequest.BodyPipe` automatisch ein neuer `PipeReader` festgelegt, der `HttpRequest.Body` umschließt. Das gleiche Verhalten trifft auf die Einstellung der `BodyPipe`-Eigenschaft zu. Wenn Sie für `HttpResponse.BodyPipe` einen neuen `PipeWriter` festlegen, wird für den `HttpResponse.Body` automatisch ein neuer Datenstrom festgelegt, der `HttpResponse.BodyPipe` umschließt.

## <a name="startasync"></a>StartAsync

`HttpResponse.StartAsync` ist neu in 3.0. Es wird verwendet, um anzugeben, dass Header nicht änderbar sind, und zum Ausführen von `OnStarting`-Rückrufen. In 3.0-preview3 müssen Sie `StartAsync` aufrufen, bevor Sie `HttpRequest.BodyPipe` verwenden, und in zukünftigen Releases wird es empfohlen. Wenn Kestrel als Server verwendet wird, garantiert das Aufrufen von StartAsync vor der Verwendung von `PipeReader`, das von `GetMemory` zurückgegebener Arbeitsspeicher zur internen <xref:System.IO.Pipelines.Pipe> von Kestrel gehört anstatt zu einem externen Puffer.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Einführung in System.IO.Pipelines](https://devblogs.microsoft.com/dotnet/system-io-pipelines-high-performance-io-in-net/)
- <xref:fundamentals/middleware/write>