---
title: Anforderungs- und Antwortvorgänge in ASP.NET Core
author: jkotalik
description: Erfahren Sie, wie Sie in ASP.NET Core den Anforderungstext lesen und den Antworttext schreiben.
monikerRange: '>= aspnetcore-3.0'
ms.author: jukotali
ms.custom: mvc
ms.date: 08/29/2019
uid: fundamentals/middleware/request-response
ms.openlocfilehash: b473fa02e1d23f02bc5d2e15fa54ab7b1dbbb17c
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/06/2020
ms.locfileid: "78650833"
---
# <a name="request-and-response-operations-in-aspnet-core"></a><span data-ttu-id="c4425-103">Anforderungs- und Antwortvorgänge in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c4425-103">Request and response operations in ASP.NET Core</span></span>

<span data-ttu-id="c4425-104">Von [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="c4425-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="c4425-105">In diesem Artikel wird erläutert, wie Sie den Anforderungstext lesen und den Antworttext schreiben.</span><span class="sxs-lookup"><span data-stu-id="c4425-105">This article explains how to read from the request body and write to the response body.</span></span> <span data-ttu-id="c4425-106">Möglicherweise ist Code für diese Vorgänge erforderlich, wenn Sie Middleware schreiben.</span><span class="sxs-lookup"><span data-stu-id="c4425-106">Code for these operations might be required when writing middleware.</span></span> <span data-ttu-id="c4425-107">Abgesehen vom Schreiben von Middleware ist benutzerdefinierter Code in der Regel nicht erforderlich, da die Vorgänge von MVC und Razor Pages behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="c4425-107">Outside of writing middleware, custom code isn't generally required because the operations are handled by MVC and Razor Pages.</span></span>

<span data-ttu-id="c4425-108">Es stehen zwei Abstraktionen für die Anforderungs- und Antworttexte zur Verfügung: <xref:System.IO.Stream> und <xref:System.IO.Pipelines.Pipe>.</span><span class="sxs-lookup"><span data-stu-id="c4425-108">There are two abstractions for the request and response bodies: <xref:System.IO.Stream> and <xref:System.IO.Pipelines.Pipe>.</span></span> <span data-ttu-id="c4425-109">[HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) ist ein <xref:System.IO.Stream> zum Lesen der Anforderung, und `HttpRequest.BodyReader` ist ein <xref:System.IO.Pipelines.PipeReader>.</span><span class="sxs-lookup"><span data-stu-id="c4425-109">For request reading, [HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) is a <xref:System.IO.Stream>, and `HttpRequest.BodyReader` is a <xref:System.IO.Pipelines.PipeReader>.</span></span> <span data-ttu-id="c4425-110">[HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) ist eine <xref:System.IO.Stream> zum Schreiben der Antwort, und `HttpResponse.BodyWriter` ist eine <xref:System.IO.Pipelines.PipeWriter>.</span><span class="sxs-lookup"><span data-stu-id="c4425-110">For response writing, [HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) is a <xref:System.IO.Stream>, and `HttpResponse.BodyWriter` is a <xref:System.IO.Pipelines.PipeWriter>.</span></span>

<span data-ttu-id="c4425-111">Anstelle von Datenströmen werden [Pipelines](/dotnet/standard/io/pipelines) empfohlen.</span><span class="sxs-lookup"><span data-stu-id="c4425-111">[Pipelines](/dotnet/standard/io/pipelines) are recommended over streams.</span></span> <span data-ttu-id="c4425-112">Datenströme können bei einigen einfachen Vorgängen einfacher zu verwenden sein, aber Pipelines haben einen Leistungsvorteil und sind in den meisten Szenarien einfacher zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="c4425-112">Streams can be easier to use for some simple operations, but pipelines have a performance advantage and are easier to use in most scenarios.</span></span> <span data-ttu-id="c4425-113">ASP.NET Core beginnt, intern Pipelines anstelle von Datenströmen zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="c4425-113">ASP.NET Core is starting to use pipelines instead of streams internally.</span></span> <span data-ttu-id="c4425-114">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="c4425-114">Examples include:</span></span>

* `FormReader`
* `TextReader`
* `TextWriter`
* `HttpResponse.WriteAsync`

<span data-ttu-id="c4425-115">Datenströme werden jedoch nicht aus dem Framework entfernt.</span><span class="sxs-lookup"><span data-stu-id="c4425-115">Streams aren't being removed from the framework.</span></span> <span data-ttu-id="c4425-116">Datenströme werden weiterhin in .NET verwendet, und für viele Datenstromtypen gibt es keine entsprechenden Pipelines, z. B. `FileStreams` und `ResponseCompression`.</span><span class="sxs-lookup"><span data-stu-id="c4425-116">Streams continue to be used throughout .NET, and many stream types don't have pipe equivalents, such as `FileStreams` and `ResponseCompression`.</span></span>

## <a name="stream-examples"></a><span data-ttu-id="c4425-117">Datenstrombeispiele</span><span class="sxs-lookup"><span data-stu-id="c4425-117">Stream examples</span></span>

<span data-ttu-id="c4425-118">Angenommen, Sie möchten eine Middleware erstellen, die den gesamten Anforderungstext als Liste von Zeichenfolgen liest, die in neue Zeilen umbrochen werden.</span><span class="sxs-lookup"><span data-stu-id="c4425-118">Suppose the goal is to create a middleware that reads the entire request body as a list of strings, splitting on new lines.</span></span> <span data-ttu-id="c4425-119">Eine einfache Datenstromimplementierung könnte wie im folgenden Beispiel aussehen:</span><span class="sxs-lookup"><span data-stu-id="c4425-119">A simple stream implementation might look like the following example:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="c4425-120">Dieser Code funktioniert, aber es gibt einige Probleme:</span><span class="sxs-lookup"><span data-stu-id="c4425-120">This code works, but there are some issues:</span></span>

* <span data-ttu-id="c4425-121">Vor dem Anfügen an den `StringBuilder` wird im Beispiel eine andere Zeichenfolge erstellt (`encodedString`), die sofort verworfen wird.</span><span class="sxs-lookup"><span data-stu-id="c4425-121">Before appending to the `StringBuilder`, the example creates another string (`encodedString`) that is thrown away immediately.</span></span> <span data-ttu-id="c4425-122">Weil dieser Vorgang für alle Bytes im Datenstrom erfolgt, ist das Ergebnis eine zusätzliche Speicherzuweisung in der Größe des gesamten Anforderungstexts.</span><span class="sxs-lookup"><span data-stu-id="c4425-122">This process occurs for all bytes in the stream, so the result is extra memory allocation the size of the entire request body.</span></span>
* <span data-ttu-id="c4425-123">Im Beispiel wird die gesamte Zeichenfolge vor dem Umbruch in neue Zeilen gelesen.</span><span class="sxs-lookup"><span data-stu-id="c4425-123">The example reads the entire string before splitting on new lines.</span></span> <span data-ttu-id="c4425-124">Es ist viel effizienter, das Bytearray auf neue Zeilen zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="c4425-124">It's more efficient to check for new lines in the byte array.</span></span>

<span data-ttu-id="c4425-125">Es folgt ein Beispiel, in dem einige dieser Probleme behoben werden:</span><span class="sxs-lookup"><span data-stu-id="c4425-125">Here's an example that fixes some of the preceding issues:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

<span data-ttu-id="c4425-126">Dieses obige Beispiel:</span><span class="sxs-lookup"><span data-stu-id="c4425-126">This preceding example:</span></span>

* <span data-ttu-id="c4425-127">Puffert nicht den gesamten Anforderungstext in einem `StringBuilder`, wenn keine Zeilenvorschubzeichen vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="c4425-127">Doesn't buffer the entire request body in a `StringBuilder` unless there aren't any newline characters.</span></span>
* <span data-ttu-id="c4425-128">Ruft nicht `Split` für die Zeichenfolge auf.</span><span class="sxs-lookup"><span data-stu-id="c4425-128">Doesn't call `Split` on the string.</span></span>

<span data-ttu-id="c4425-129">Es gibt jedoch noch ein paar Probleme:</span><span class="sxs-lookup"><span data-stu-id="c4425-129">However, there are still are a few issues:</span></span>

* <span data-ttu-id="c4425-130">Wenn nur wenige neue Zeilenvorschubzeichen vorliegen, wird ein großer Teil des Anforderungstexts in der Zeichenfolge gepuffert.</span><span class="sxs-lookup"><span data-stu-id="c4425-130">If newline characters are sparse, much of the request body is buffered in the string.</span></span>
* <span data-ttu-id="c4425-131">Der Code erstellt weiterhin Zeichenfolgen (`remainingString`) und fügt diese zum Zeichenfolgenpuffer hinzu, was zu einer weiteren Zuteilung führt.</span><span class="sxs-lookup"><span data-stu-id="c4425-131">The code continues to create strings (`remainingString`) and adds them to the string buffer, which results in an extra allocation.</span></span>

<span data-ttu-id="c4425-132">Diese Probleme können behoben werden, aber der Code wird immer komplizierter, und die Verbesserung ist gering.</span><span class="sxs-lookup"><span data-stu-id="c4425-132">These issues are fixable, but the code is becoming progressively more complicated with little improvement.</span></span> <span data-ttu-id="c4425-133">Pipelines bieten eine Möglichkeit, diese Probleme mit minimaler Codekomplexität zu lösen.</span><span class="sxs-lookup"><span data-stu-id="c4425-133">Pipelines provide a way to solve these problems with minimal code complexity.</span></span>

## <a name="pipelines"></a><span data-ttu-id="c4425-134">Pipelines</span><span class="sxs-lookup"><span data-stu-id="c4425-134">Pipelines</span></span>

<span data-ttu-id="c4425-135">Das folgende Beispiel zeigt, wie das gleiche Szenario mit einem `PipeReader` behandelt werden kann:</span><span class="sxs-lookup"><span data-stu-id="c4425-135">The following example shows how the same scenario can be handled using a `PipeReader`:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

<span data-ttu-id="c4425-136">In diesem Beispiel werden viele Probleme der Datenstromimplementierungen behoben:</span><span class="sxs-lookup"><span data-stu-id="c4425-136">This example fixes many issues that the streams implementations had:</span></span>

* <span data-ttu-id="c4425-137">Ein Zeichenfolgenpuffer ist nicht erforderlich, weil der `PipeReader` Bytes behandelt, die nicht verwendet wurden.</span><span class="sxs-lookup"><span data-stu-id="c4425-137">There's no need for a string buffer because the `PipeReader` handles bytes that haven't been used.</span></span>
* <span data-ttu-id="c4425-138">Codierte Zeichenfolgen werden direkt der Liste der zurückgegebenen Zeichenfolgen hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="c4425-138">Encoded strings are directly added to the list of returned strings.</span></span>
* <span data-ttu-id="c4425-139">Die Erstellung von Zeichenfolgen ist zuordnungsfrei, abgesehen von dem Speicher, den die Zeichenfolge belegt (mit Ausnahme des `ToArray()`-Aufrufs).</span><span class="sxs-lookup"><span data-stu-id="c4425-139">String creation is allocation-free besides the memory used by the string (except the `ToArray()` call).</span></span>

## <a name="adapters"></a><span data-ttu-id="c4425-140">Adapter</span><span class="sxs-lookup"><span data-stu-id="c4425-140">Adapters</span></span>

<span data-ttu-id="c4425-141">Die Eigenschaften `Body` und `BodyReader/BodyWriter` sind für `HttpRequest` und `HttpResponse` verfügbar.</span><span class="sxs-lookup"><span data-stu-id="c4425-141">Both `Body` and `BodyReader/BodyWriter` properties are available for `HttpRequest` and `HttpResponse`.</span></span> <span data-ttu-id="c4425-142">Wenn Sie für `Body` einen anderen Datenstrom festlegen, passen neue Adapter die Typen automatisch aneinander an.</span><span class="sxs-lookup"><span data-stu-id="c4425-142">When you set `Body` to a different stream, a new set of adapters automatically adapt each type to the other.</span></span> <span data-ttu-id="c4425-143">Wenn Sie für `HttpRequest.Body` einen neuen Datenstrom festlegen, wird für `HttpRequest.BodyReader` automatisch ein neuer `PipeReader` festgelegt, der `HttpRequest.Body` umschließt.</span><span class="sxs-lookup"><span data-stu-id="c4425-143">If you set `HttpRequest.Body` to a new stream, `HttpRequest.BodyReader` is automatically set to a new `PipeReader` that wraps `HttpRequest.Body`.</span></span>

## <a name="startasync"></a><span data-ttu-id="c4425-144">StartAsync</span><span class="sxs-lookup"><span data-stu-id="c4425-144">StartAsync</span></span>

<span data-ttu-id="c4425-145">`HttpResponse.StartAsync` wird verwendet, um anzugeben, dass Header nicht änderbar sind, und um `OnStarting`-Rückrufe auszuführen.</span><span class="sxs-lookup"><span data-stu-id="c4425-145">`HttpResponse.StartAsync` is used to indicate that headers are unmodifiable and to run `OnStarting` callbacks.</span></span> <span data-ttu-id="c4425-146">Bei der Verwendung von Kestrel als Server garantiert der Aufruf von `StartAsync` vor der Verwendung von `PipeReader`, dass von `GetMemory` zurückgegebener Arbeitsspeicher zu internen <xref:System.IO.Pipelines.Pipe>-Pipeline von Kestrel gehört, anstatt einem externen Puffer.</span><span class="sxs-lookup"><span data-stu-id="c4425-146">When using Kestrel as a server, calling `StartAsync` before using the `PipeReader` guarantees that memory returned by `GetMemory` belongs to Kestrel's internal <xref:System.IO.Pipelines.Pipe> rather than an external buffer.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c4425-147">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="c4425-147">Additional resources</span></span>

* [<span data-ttu-id="c4425-148">Einführung in System.IO.Pipelines</span><span class="sxs-lookup"><span data-stu-id="c4425-148">Introducing System.IO.Pipelines</span></span>](https://devblogs.microsoft.com/dotnet/system-io-pipelines-high-performance-io-in-net/)
* <xref:fundamentals/middleware/write>
