---
title: Anforderungs- und Antwortvorgänge in ASP.NET Core
author: jkotalik
description: Erfahren Sie, wie Sie in ASP.NET Core den Anforderungstext lesen und den Antworttext schreiben.
monikerRange: '>= aspnetcore-3.0'
ms.author: jukotali
ms.custom: mvc
ms.date: 02/26/2019
uid: fundamentals/middleware/request-response
ms.openlocfilehash: 0c321dad256e239b61907980c09d2c088c1407ff
ms.sourcegitcommit: 0b9e767a09beaaaa4301915cdda9ef69daaf3ff2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538578"
---
# <a name="request-and-response-operations-in-aspnet-core"></a><span data-ttu-id="50056-103">Anforderungs- und Antwortvorgänge in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="50056-103">Request and response operations in ASP.NET Core</span></span>

<span data-ttu-id="50056-104">Von [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="50056-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="50056-105">In diesem Artikel wird erläutert, wie Sie den Anforderungstext lesen und den Antworttext schreiben.</span><span class="sxs-lookup"><span data-stu-id="50056-105">This article explains how to read from the request body and write to the response body.</span></span> <span data-ttu-id="50056-106">Sie müssen möglicherweise Code für diese Vorgänge schreiben, wenn Sie Middleware schreiben.</span><span class="sxs-lookup"><span data-stu-id="50056-106">You might need to write code for these operations when you're writing middleware.</span></span> <span data-ttu-id="50056-107">Andernfalls müssen Sie diesen Code in der Regel nicht schreiben, da die Vorgänge von MVC und Razor Pages behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="50056-107">Otherwise, you typically don't have to write this code because the operations are handled by MVC and Razor Pages.</span></span>

<span data-ttu-id="50056-108">In ASP.NET Core 3.0 stehen zwei Abstraktionen für Anforderungs- und Antworttext zur Verfügung: <xref:System.IO.Stream> und <xref:System.IO.Pipelines.Pipe>.</span><span class="sxs-lookup"><span data-stu-id="50056-108">In ASP.NET Core 3.0, there are two abstractions for the request and response bodies: <xref:System.IO.Stream> and <xref:System.IO.Pipelines.Pipe>.</span></span> <span data-ttu-id="50056-109">[HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) ist ein <xref:System.IO.Stream> zum Lesen der Anforderung, und `HttpRequest.BodyPipe` ist ein <xref:System.IO.Pipelines.PipeReader>.</span><span class="sxs-lookup"><span data-stu-id="50056-109">For request reading, [HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) is a <xref:System.IO.Stream>, and `HttpRequest.BodyPipe` is a <xref:System.IO.Pipelines.PipeReader>.</span></span> <span data-ttu-id="50056-110">[HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) ist eine `HttpResponse.BodyPipe` zum Schreiben der Antwort – ein <xref:System.IO.Pipelines.PipeWriter>.</span><span class="sxs-lookup"><span data-stu-id="50056-110">For response writing, [HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) is a `HttpResponse.BodyPipe` is a <xref:System.IO.Pipelines.PipeWriter>.</span></span>

<span data-ttu-id="50056-111">Wir empfehlen Pipelines über Datenströme.</span><span class="sxs-lookup"><span data-stu-id="50056-111">We recommend pipelines over streams.</span></span> <span data-ttu-id="50056-112">Datenströme können bei einigen einfachen Vorgängen einfacher zu verwenden sein, aber Pipelines haben einen Leistungsvorteil und sind in den meisten Szenarien einfacher zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="50056-112">Streams can be easier to use for some simple operations, but pipelines have a performance advantage and are easier to use in most scenarios.</span></span> <span data-ttu-id="50056-113">Ab 3.0 verwendet ASP.NET Core intern Pipelines anstelle von Datenströmen.</span><span class="sxs-lookup"><span data-stu-id="50056-113">In 3.0, ASP.NET Core is starting to use pipelines instead of streams internally.</span></span> <span data-ttu-id="50056-114">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="50056-114">Examples include:</span></span>

- `FormReader`
- `TextReader`
- `TexWriter`
- `HttpResponse.WriteAsync`

<span data-ttu-id="50056-115">Datenströme verschwinden nicht.</span><span class="sxs-lookup"><span data-stu-id="50056-115">Streams aren't going away.</span></span> <span data-ttu-id="50056-116">Sie werden weiterhin überall in .NET verwendet, und viele Datenstromtypen wie `FileStreams` und `ResponseCompression` haben keine Pipelineentsprechungen.</span><span class="sxs-lookup"><span data-stu-id="50056-116">They continue to be used throughout .NET, and many stream types don't have pipe equivalents, like `FileStreams` and `ResponseCompression`.</span></span>

## <a name="stream-examples"></a><span data-ttu-id="50056-117">Datenstrombeispiele</span><span class="sxs-lookup"><span data-stu-id="50056-117">Stream examples</span></span>

<span data-ttu-id="50056-118">Nehmen wir an, Sie möchten eine Middleware erstellen, die den gesamten Anforderungstext als Liste von Zeichenfolgen liest, die in neue Zeilen umbrochen werden.</span><span class="sxs-lookup"><span data-stu-id="50056-118">Suppose you want to create a middleware that reads the entire request body as a list of strings, splitting on new lines.</span></span> <span data-ttu-id="50056-119">Eine einfache Datenstromimplementierung könnte wie im folgenden Beispiel aussehen:</span><span class="sxs-lookup"><span data-stu-id="50056-119">A simple stream implementation might look like the following example:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]

<span data-ttu-id="50056-120">Dieser Code funktioniert, aber es gibt einige Probleme:</span><span class="sxs-lookup"><span data-stu-id="50056-120">This code works, but there are some issues:</span></span>

- <span data-ttu-id="50056-121">Vor dem Anfügen an den `StringBuilder` wird im Beispiel eine andere Zeichenfolge erstellt (`encodedString`), die sofort verworfen wird.</span><span class="sxs-lookup"><span data-stu-id="50056-121">Before appending to the `StringBuilder`, the example creates another string (`encodedString`) that is thrown away immediately.</span></span> <span data-ttu-id="50056-122">Weil dieser Vorgang für alle Bytes im Datenstrom erfolgt, ist das Ergebnis eine zusätzliche Speicherzuweisung in der Größe des gesamten Anforderungstexts.</span><span class="sxs-lookup"><span data-stu-id="50056-122">This process occurs for all bytes in the stream, so the result is extra memory allocation the size of the entire request body.</span></span>
- <span data-ttu-id="50056-123">Im Beispiel wird die gesamte Zeichenfolge vor dem Umbruch in neue Zeilen gelesen.</span><span class="sxs-lookup"><span data-stu-id="50056-123">The example reads the entire string before splitting on new lines.</span></span> <span data-ttu-id="50056-124">Es wäre viel effizienter, das Bytearray auf neue Zeilen zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="50056-124">It would be more efficient to check for new lines in the byte array.</span></span>

<span data-ttu-id="50056-125">Hier ist ein Beispiel, das einige dieser Probleme behebt:</span><span class="sxs-lookup"><span data-stu-id="50056-125">Here's an example that fixes some of these issues:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

<span data-ttu-id="50056-126">Dieses Beispiel:</span><span class="sxs-lookup"><span data-stu-id="50056-126">This example:</span></span>

- <span data-ttu-id="50056-127">Puffert nicht den gesamten Anforderungstext in einem `StringBuilder`, wenn keine Zeilenvorschubzeichen vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="50056-127">Doesn't buffer the entire request body in a `StringBuilder` unless there aren't any newline characters.</span></span>
- <span data-ttu-id="50056-128">Ruft nicht `Split` für die Zeichenfolge auf.</span><span class="sxs-lookup"><span data-stu-id="50056-128">Doesn't call `Split` on the string.</span></span>

<span data-ttu-id="50056-129">Es gibt jedoch noch ein paar Probleme:</span><span class="sxs-lookup"><span data-stu-id="50056-129">However, there are still are a few issues:</span></span>

- <span data-ttu-id="50056-130">Wenn neue Zeilenvorschubzeichen rar sind, wird ein großer Teil des Anforderungstexts in der Zeichenfolge gepuffert.</span><span class="sxs-lookup"><span data-stu-id="50056-130">If newline characters are sparse, much of the request body is buffered in the string .</span></span>
- <span data-ttu-id="50056-131">Dennoch werden Zeichenfolgen erstellt (`remainingString`) und dem Zeichenfolgenpuffer hinzugefügt, woraus eine zusätzliche Zuordnung resultiert.</span><span class="sxs-lookup"><span data-stu-id="50056-131">It still creates strings (`remainingString`) and adds them to the string buffer, which results in an extra allocation.</span></span>

<span data-ttu-id="50056-132">Diese Probleme können behoben werden, aber der Code wird immer komplizierter, und die Verbesserung ist gering.</span><span class="sxs-lookup"><span data-stu-id="50056-132">These issues are fixable, but the code is becoming more and more complicated with little improvement.</span></span> <span data-ttu-id="50056-133">Pipelines bieten eine Möglichkeit, diese Probleme mit minimaler Codekomplexität zu lösen.</span><span class="sxs-lookup"><span data-stu-id="50056-133">Pipelines provide a way to solve these problems with minimal code complexity.</span></span>

## <a name="pipelines"></a><span data-ttu-id="50056-134">Pipelines</span><span class="sxs-lookup"><span data-stu-id="50056-134">Pipelines</span></span>

<span data-ttu-id="50056-135">Das folgende Beispiel zeigt, wie das gleiche Szenario mit einem `PipeReader` behandelt werden kann:</span><span class="sxs-lookup"><span data-stu-id="50056-135">The following example shows how the same scenario can be handled using a `PipeReader`:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

<span data-ttu-id="50056-136">In diesem Beispiel werden viele Probleme der Datenstromimplementierungen behoben:</span><span class="sxs-lookup"><span data-stu-id="50056-136">This example fixes many issues that the streams implementations had:</span></span>

- <span data-ttu-id="50056-137">Ein Zeichenfolgenpuffer ist nicht erforderlich, weil der `PipeReader` Bytes behandelt, die nicht verwendet wurden.</span><span class="sxs-lookup"><span data-stu-id="50056-137">There is no need for a string buffer because the `PipeReader` handles bytes that haven't been used.</span></span>
- <span data-ttu-id="50056-138">Codierte Zeichenfolgen werden direkt der Liste der zurückgegebenen Zeichenfolgen hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="50056-138">Encoded strings are directly added to the list of returned strings.</span></span>
- <span data-ttu-id="50056-139">Die Erstellung von Zeichenfolgen ist zuordnungsfrei, abgesehen von dem Speicher, den die Zeichenfolge belegt (mit Ausnahme des `ToArray()`-Aufrufs).</span><span class="sxs-lookup"><span data-stu-id="50056-139">String creation is allocation-free besides the memory used by the string (except the `ToArray()` call).</span></span>

## <a name="adapters"></a><span data-ttu-id="50056-140">Adapter</span><span class="sxs-lookup"><span data-stu-id="50056-140">Adapters</span></span>

<span data-ttu-id="50056-141">Was geschieht, nachdem jetzt die Eigenschaften `Body` und `BodyPipe` beide für `HttpRequest` und `HttpResponse` verfügbar sind, wenn Sie für `Body` einen anderen Datenstrom festlegen?</span><span class="sxs-lookup"><span data-stu-id="50056-141">Now that both `Body` and `BodyPipe` properties are available for `HttpRequest` and `HttpResponse`, what happens when you set `Body` to a different stream?</span></span> <span data-ttu-id="50056-142">Eine neue Reihe von Adaptern in 3.0 passt jeden Typ automatisch einem anderen an.</span><span class="sxs-lookup"><span data-stu-id="50056-142">In 3.0, a new set of adapters automatically adapt each type to the other.</span></span> <span data-ttu-id="50056-143">Wenn Sie z.B. für `HttpRequest.Body` einen neuen Datenstrom festlegen, wird für `HttpRequest.BodyPipe` automatisch ein neuer `PipeReader` festgelegt, der `HttpRequest.Body` umschließt.</span><span class="sxs-lookup"><span data-stu-id="50056-143">For example, if you set `HttpRequest.Body` to a new stream, `HttpRequest.BodyPipe` is automatically set to a new `PipeReader` that wraps `HttpRequest.Body`.</span></span> <span data-ttu-id="50056-144">Das gleiche Verhalten trifft auf die Einstellung der `BodyPipe`-Eigenschaft zu.</span><span class="sxs-lookup"><span data-stu-id="50056-144">The same behavior applies to setting the `BodyPipe` property.</span></span> <span data-ttu-id="50056-145">Wenn Sie für `HttpResponse.BodyPipe` einen neuen `PipeWriter` festlegen, wird für den `HttpResponse.Body` automatisch ein neuer Datenstrom festgelegt, der `HttpResponse.BodyPipe` umschließt.</span><span class="sxs-lookup"><span data-stu-id="50056-145">If `HttpResponse.BodyPipe` is set to a new `PipeWriter`, the `HttpResponse.Body` is automatically set to a new stream that wraps `HttpResponse.BodyPipe`.</span></span>

## <a name="startasync"></a><span data-ttu-id="50056-146">StartAsync</span><span class="sxs-lookup"><span data-stu-id="50056-146">StartAsync</span></span>

<span data-ttu-id="50056-147">`HttpResponse.StartAsync` ist neu in 3.0.</span><span class="sxs-lookup"><span data-stu-id="50056-147">`HttpResponse.StartAsync` is new in 3.0.</span></span> <span data-ttu-id="50056-148">Es wird verwendet, um anzugeben, dass Header nicht änderbar sind, und zum Ausführen von `OnStarting`-Rückrufen.</span><span class="sxs-lookup"><span data-stu-id="50056-148">It is used to indicate that headers are unmodifiable and to run `OnStarting` callbacks.</span></span> <span data-ttu-id="50056-149">In 3.0-preview3 müssen Sie `StartAsync` aufrufen, bevor Sie `HttpRequest.BodyPipe` verwenden, und in zukünftigen Releases wird es empfohlen.</span><span class="sxs-lookup"><span data-stu-id="50056-149">In 3.0-preview3, you have to call `StartAsync` before using `HttpRequest.BodyPipe`, and in future releases, it will be a recommendation.</span></span> <span data-ttu-id="50056-150">Wenn Kestrel als Server verwendet wird, garantiert das Aufrufen von StartAsync vor der Verwendung von `PipeReader`, das von `GetMemory` zurückgegebener Arbeitsspeicher zur internen <xref:System.IO.Pipelines.Pipe> von Kestrel gehört anstatt zu einem externen Puffer.</span><span class="sxs-lookup"><span data-stu-id="50056-150">When using Kestrel as a server, calling StartAsync before using the `PipeReader` guarantees that memory returned by `GetMemory` will belong to Kestrel's internal <xref:System.IO.Pipelines.Pipe> rather than an external buffer.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="50056-151">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="50056-151">Additional resources</span></span>

- [<span data-ttu-id="50056-152">Einführung in System.IO.Pipelines</span><span class="sxs-lookup"><span data-stu-id="50056-152">Introducing System.IO.Pipelines</span></span>](https://devblogs.microsoft.com/dotnet/system-io-pipelines-high-performance-io-in-net/)
- <xref:fundamentals/middleware/write>