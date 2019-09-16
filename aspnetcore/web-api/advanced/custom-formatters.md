---
title: Benutzerdefinierte Formatierer in Web-APIs in ASP.NET Core
author: rick-anderson
description: Informationen zum Erstellen und Verwenden von benutzerdefinierten Formatierern für Web-APIs in ASP.NET Core.
ms.author: riande
ms.date: 02/08/2017
uid: web-api/advanced/custom-formatters
ms.openlocfilehash: 122edfd4ccd06ed62e071691f421d2aeef8002b4
ms.sourcegitcommit: 488cc779fc71377d9371e7a14356113e9c7eff17
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70913512"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a><span data-ttu-id="e1970-103">Benutzerdefinierte Formatierer in Web-APIs in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e1970-103">Custom formatters in ASP.NET Core Web API</span></span>

<span data-ttu-id="e1970-104">Von [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="e1970-104">By [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="e1970-105">ASP.NET Core MVC unterstützt den Datenaustausch in Web-APIs mithilfe von Eingabe- und Ausgabeformatierern.</span><span class="sxs-lookup"><span data-stu-id="e1970-105">ASP.NET Core MVC supports data exchange in Web APIs using input and output formatters.</span></span> <span data-ttu-id="e1970-106">Eingabeformatierer werden von der [Modellbindung](xref:mvc/models/model-binding) verwendet.</span><span class="sxs-lookup"><span data-stu-id="e1970-106">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="e1970-107">Ausgabeformatierer werden zum [Formatieren von Antworten](xref:web-api/advanced/formatting) verwendet.</span><span class="sxs-lookup"><span data-stu-id="e1970-107">Output formatters are used to [format responses](xref:web-api/advanced/formatting).</span></span>

<span data-ttu-id="e1970-108">Das Framework stellt integrierte Eingabe- und Ausgabeformatierer für JSON und XML bereit.</span><span class="sxs-lookup"><span data-stu-id="e1970-108">The framework provides built-in input and output formatters for JSON and XML.</span></span> <span data-ttu-id="e1970-109">Es stellt einen integrierten Ausgabeformatierer für Nur-Text bereit, jedoch keinen Eingabeformatierer.</span><span class="sxs-lookup"><span data-stu-id="e1970-109">It provides a built-in output formatter for plain text, but doesn't provide an input formatter for plain text.</span></span>

<span data-ttu-id="e1970-110">In diesem Artikel wird dargestellt, wie Sie die Unterstützung von zusätzlichen Formaten hinzufügen, indem Sie benutzerdefinierte Formatierer erstellen.</span><span class="sxs-lookup"><span data-stu-id="e1970-110">This article shows how to add support for additional formats by creating custom formatters.</span></span> <span data-ttu-id="e1970-111">Ein Beispiel für einen benutzerdefinierten Eingabeformatierer für Nur-Text finden Sie unter [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) auf GitHub.</span><span class="sxs-lookup"><span data-stu-id="e1970-111">For an example of a custom input formatter for plain text, see [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) on GitHub.</span></span>

<span data-ttu-id="e1970-112">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e1970-112">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-custom-formatters"></a><span data-ttu-id="e1970-113">Empfohlene Verwendung von benutzerdefinierten Formatierern</span><span class="sxs-lookup"><span data-stu-id="e1970-113">When to use custom formatters</span></span>

<span data-ttu-id="e1970-114">Verwenden Sie einen benutzerdefinierten Formatierer, wenn Sie möchten, dass der [Inhaltaushandlungsvorgang](xref:web-api/advanced/formatting#content-negotiation) einen Inhaltstyp unterstützt, der nicht von den integrierten Formatieren unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="e1970-114">Use a custom formatter when you want the [content negotiation](xref:web-api/advanced/formatting#content-negotiation) process to support a content type that isn't supported by the built-in formatters.</span></span>

<span data-ttu-id="e1970-115">Wenn z.B. einige der Clients für Ihre Web-API das [Protobuf](https://github.com/google/protobuf)-Format verarbeiten können, sollten Sie auch Protobuf für diese Clients verwenden, wenn Sie effizient arbeiten möchten.</span><span class="sxs-lookup"><span data-stu-id="e1970-115">For example, if some of the clients for your web API can handle the [Protobuf](https://github.com/google/protobuf) format, you might want to use Protobuf with those clients because it's more efficient.</span></span> <span data-ttu-id="e1970-116">Möglicherweise möchten Sie aber lieber Ihre Web-API verwenden, um Namen und Adressen von Kontakten im [vCard](https://wikipedia.org/wiki/VCard)-Format zu versenden – ein häufig zum Austauschen von Kontaktdaten verwendetes Format.</span><span class="sxs-lookup"><span data-stu-id="e1970-116">Or you might want your web API to send contact names and addresses in [vCard](https://wikipedia.org/wiki/VCard) format, a commonly used format for exchanging contact data.</span></span> <span data-ttu-id="e1970-117">Die in diesem Artikel enthaltene Beispiel-App implementiert einen einfachen vCard-Formatierer.</span><span class="sxs-lookup"><span data-stu-id="e1970-117">The sample app provided with this article implements a simple vCard formatter.</span></span>

## <a name="overview-of-how-to-use-a-custom-formatter"></a><span data-ttu-id="e1970-118">Übersicht über die Verwendung des Formatierers</span><span class="sxs-lookup"><span data-stu-id="e1970-118">Overview of how to use a custom formatter</span></span>

<span data-ttu-id="e1970-119">Im Folgenden werden einige Schritte zum Erstellen und Verwenden eines benutzerdefinierten Formatierers beschrieben:</span><span class="sxs-lookup"><span data-stu-id="e1970-119">Here are the steps to create and use a custom formatter:</span></span>

* <span data-ttu-id="e1970-120">Erstellen Sie eine Klasse für den Ausgabeformatierer, wenn Sie die Daten serialisieren möchten, um diese an den Client zu senden.</span><span class="sxs-lookup"><span data-stu-id="e1970-120">Create an output formatter class if you want to serialize data to send to the client.</span></span>
* <span data-ttu-id="e1970-121">Erstellen Sie eine Klasse für den Eingabeformatierer, wenn Sie die Daten deserialisieren möchten, die vom Client gesendet wurden.</span><span class="sxs-lookup"><span data-stu-id="e1970-121">Create an input formatter class if you want to deserialize data received from the client.</span></span>
* <span data-ttu-id="e1970-122">Fügen Sie Instanzen Ihrer Formatierer zu den Auflistungen `InputFormatters` und `OutputFormatters` in [MvcOptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions) hinzu.</span><span class="sxs-lookup"><span data-stu-id="e1970-122">Add instances of your formatters to the `InputFormatters` and `OutputFormatters` collections in [MvcOptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions).</span></span>

<span data-ttu-id="e1970-123">In den Folgenden Abschnitten erhalten Sie Anweisungen und Codebeispiele zu diesen Schritten.</span><span class="sxs-lookup"><span data-stu-id="e1970-123">The following sections provide guidance and code examples for each of these steps.</span></span>

## <a name="how-to-create-a-custom-formatter-class"></a><span data-ttu-id="e1970-124">Erstellen einer benutzerdefinierten Formatiererklasse</span><span class="sxs-lookup"><span data-stu-id="e1970-124">How to create a custom formatter class</span></span>

<span data-ttu-id="e1970-125">Erstellen eines Formatierers:</span><span class="sxs-lookup"><span data-stu-id="e1970-125">To create a formatter:</span></span>

* <span data-ttu-id="e1970-126">Leiten Sie die Klasse von der entsprechenden Basisklasse ab.</span><span class="sxs-lookup"><span data-stu-id="e1970-126">Derive the class from the appropriate base class.</span></span>
* <span data-ttu-id="e1970-127">Geben Sie gültige Medientypen und Codierungen im Konstruktor an.</span><span class="sxs-lookup"><span data-stu-id="e1970-127">Specify valid media types and encodings in the constructor.</span></span>
* <span data-ttu-id="e1970-128">Überschreiben Sie die Methoden `CanReadType`/`CanWriteType`</span><span class="sxs-lookup"><span data-stu-id="e1970-128">Override `CanReadType`/`CanWriteType` methods</span></span>
* <span data-ttu-id="e1970-129">Überschreiben Sie die Methoden `ReadRequestBodyAsync`/`WriteResponseBodyAsync`</span><span class="sxs-lookup"><span data-stu-id="e1970-129">Override `ReadRequestBodyAsync`/`WriteResponseBodyAsync` methods</span></span>
  
### <a name="derive-from-the-appropriate-base-class"></a><span data-ttu-id="e1970-130">Ableiten von der entsprechenden Basisklasse</span><span class="sxs-lookup"><span data-stu-id="e1970-130">Derive from the appropriate base class</span></span>

<span data-ttu-id="e1970-131">Leiten Sie für Textmedientypen wie vCard von den Basisklassen [TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) oder [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter) ab.</span><span class="sxs-lookup"><span data-stu-id="e1970-131">For text media types (for example, vCard), derive from the [TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) or [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter) base class.</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=classdef)]

<span data-ttu-id="e1970-132">Einen Eingabeformatierer finden Sie z.B. in der [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span><span class="sxs-lookup"><span data-stu-id="e1970-132">For an input formatter example, see the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

<span data-ttu-id="e1970-133">Leiten Sie für Binärtypen von den Basisklassen [InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) oder [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter) ab.</span><span class="sxs-lookup"><span data-stu-id="e1970-133">For binary types, derive from the [InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) or [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter) base class.</span></span>

### <a name="specify-valid-media-types-and-encodings"></a><span data-ttu-id="e1970-134">Angeben von gültigen Medientypen und Codierungen</span><span class="sxs-lookup"><span data-stu-id="e1970-134">Specify valid media types and encodings</span></span>

<span data-ttu-id="e1970-135">Geben Sie im Konstruktor gültige Medientypen und Codierungen an, indem Sie `SupportedMediaTypes`- und `SupportedEncodings`-Sammlungen hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="e1970-135">In the constructor, specify valid media types and encodings by adding to the `SupportedMediaTypes` and `SupportedEncodings` collections.</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=ctor&highlight=3,5-6)]

<span data-ttu-id="e1970-136">Einen Eingabeformatierer finden Sie z.B. in der [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span><span class="sxs-lookup"><span data-stu-id="e1970-136">For an input formatter example, see the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

> [!NOTE]
> <span data-ttu-id="e1970-137">Sie können in einer Formatiererklasse keine Dependency Injection für den Konstruktor durchführen.</span><span class="sxs-lookup"><span data-stu-id="e1970-137">You can't do constructor dependency injection in a formatter class.</span></span> <span data-ttu-id="e1970-138">Beispielsweise können Sie keine Protokollierung abrufen, indem Sie dem Konstruktor den Protokollparameter hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="e1970-138">For example, you can't get a logger by adding a logger parameter to the constructor.</span></span> <span data-ttu-id="e1970-139">Sie müssen das Kontextobjekt verwenden, das an Ihre Methoden übergeben wird, um auf Dienste zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="e1970-139">To access services, you have to use the context object that gets passed in to your methods.</span></span> <span data-ttu-id="e1970-140">Im [nachfolgenden](#read-write) Codebeispiel wird deutlich, wie Sie hierzu vorgehen.</span><span class="sxs-lookup"><span data-stu-id="e1970-140">A code example [below](#read-write) shows how to do this.</span></span>

### <a name="override-canreadtypecanwritetype"></a><span data-ttu-id="e1970-141">Überschreiben von „CanReadType/CanWriteType“</span><span class="sxs-lookup"><span data-stu-id="e1970-141">Override CanReadType/CanWriteType</span></span>

<span data-ttu-id="e1970-142">Geben Sie den Typ an, den Sie deserialisieren bzw. serialisieren möchten, indem Sie die Methoden `CanReadType` oder `CanWriteType` überschreiben.</span><span class="sxs-lookup"><span data-stu-id="e1970-142">Specify the type you can deserialize into or serialize from by overriding the `CanReadType` or `CanWriteType` methods.</span></span> <span data-ttu-id="e1970-143">Es kann z.B. sein, dass Sie nur vCard-Text über einen `Contact`-Typ bzw. umgekehrt erstellen können.</span><span class="sxs-lookup"><span data-stu-id="e1970-143">For example, you might only be able to create vCard text from a `Contact` type and vice versa.</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=canwritetype)]

<span data-ttu-id="e1970-144">Einen Eingabeformatierer finden Sie z.B. in der [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span><span class="sxs-lookup"><span data-stu-id="e1970-144">For an input formatter example, see the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

#### <a name="the-canwriteresult-method"></a><span data-ttu-id="e1970-145">Die CanWriteResult-Methode</span><span class="sxs-lookup"><span data-stu-id="e1970-145">The CanWriteResult method</span></span>

<span data-ttu-id="e1970-146">Manchmal müssen Sie `CanWriteResult` anstelle von `CanWriteType` überschreiben.</span><span class="sxs-lookup"><span data-stu-id="e1970-146">In some scenarios you have to override `CanWriteResult` instead of `CanWriteType`.</span></span> <span data-ttu-id="e1970-147">Verwenden Sie `CanWriteResult`, wenn alle der folgenden Bedingungen zutreffen:</span><span class="sxs-lookup"><span data-stu-id="e1970-147">Use `CanWriteResult` if the following conditions are true:</span></span>

* <span data-ttu-id="e1970-148">Ihre Aktionsmethode gibt eine Modellklasse zurück.</span><span class="sxs-lookup"><span data-stu-id="e1970-148">Your action method returns a model class.</span></span>
* <span data-ttu-id="e1970-149">Es gibt abgeleitete Klassen, die möglicherweise zur Laufzeit zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="e1970-149">There are derived classes which might be returned at runtime.</span></span>
* <span data-ttu-id="e1970-150">Sie müssen zur Laufzeit wissen, welche abgeleitete Klasse von der Aktion zurückgegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="e1970-150">You need to know at runtime which derived class was returned by the action.</span></span>

<span data-ttu-id="e1970-151">Nehmen Sie z.B. an, dass die Signatur Ihrer Aktionsmethode einen `Person`-Typ zurückgibt. Sie kann dann aber auch den Typ `Student` oder `Instructor` zurückgeben, der von `Person` abgeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="e1970-151">For example, suppose your action method signature returns a `Person` type, but it may return a `Student` or `Instructor` type that derives from `Person`.</span></span> <span data-ttu-id="e1970-152">Wenn Sie möchten, dass Ihr Formatierer nur `Student`-Objekte verarbeitet, überprüfen Sie den [Objekt](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext.object#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object)-Typ im Kontextobjekt, das in der `CanWriteResult`-Methode zur Verfügung gestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="e1970-152">If you want your formatter to handle only `Student` objects, check the type of [Object](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext.object#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) in the context object provided to the `CanWriteResult` method.</span></span> <span data-ttu-id="e1970-153">Beachten Sie, dass es nicht nötig ist, `CanWriteResult` zu verwenden, wenn die Aktionsmethode `IActionResult` zurückgibt. In diesem Fall erhält die `CanWriteType`-Methode den Runtimetyp.</span><span class="sxs-lookup"><span data-stu-id="e1970-153">Note that it's not necessary to use `CanWriteResult` when the action method returns `IActionResult`; in that case, the `CanWriteType` method receives the runtime type.</span></span>

<a id="read-write"></a>

### <a name="override-readrequestbodyasyncwriteresponsebodyasync"></a><span data-ttu-id="e1970-154">Überschreiben von „ReadRequestBodyAsync/WriteResponseBodyAsync“</span><span class="sxs-lookup"><span data-stu-id="e1970-154">Override ReadRequestBodyAsync/WriteResponseBodyAsync</span></span>

<span data-ttu-id="e1970-155">Sie deserialisieren oder serialisieren manuell in `ReadRequestBodyAsync` oder `WriteResponseBodyAsync`.</span><span class="sxs-lookup"><span data-stu-id="e1970-155">You do the actual work of deserializing or serializing in `ReadRequestBodyAsync` or `WriteResponseBodyAsync`.</span></span> <span data-ttu-id="e1970-156">Die markierten Zeilen im folgenden Beispiel zeigen, wie Sie Dienste aus dem Dependency Injection-Container abrufen. Sie können diese nicht aus den Konstruktorparametern abrufen.</span><span class="sxs-lookup"><span data-stu-id="e1970-156">The highlighted lines in the following example show how to get services from the dependency injection container (you can't get them from constructor parameters).</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=writeresponse&highlight=3-4)]

<span data-ttu-id="e1970-157">Einen Eingabeformatierer finden Sie z.B. in der [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span><span class="sxs-lookup"><span data-stu-id="e1970-157">For an input formatter example, see the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a><span data-ttu-id="e1970-158">Konfigurieren von MVC zum Verwenden eines benutzerdefinierten Formatierers</span><span class="sxs-lookup"><span data-stu-id="e1970-158">How to configure MVC to use a custom formatter</span></span>

<span data-ttu-id="e1970-159">Wenn Sie einen benutzerdefinierten Formatierer verwenden, fügen Sie der `InputFormatters`- oder `OutputFormatters`-Sammlung eine Instanz der Formatiererklasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="e1970-159">To use a custom formatter, add an instance of the formatter class to the `InputFormatters` or `OutputFormatters` collection.</span></span>

[!code-csharp[](custom-formatters/sample/Startup.cs?name=mvcoptions&highlight=3-4)]

<span data-ttu-id="e1970-160">Formatierer werden in der Reihenfolge überprüft, in der Sie sie einfügen.</span><span class="sxs-lookup"><span data-stu-id="e1970-160">Formatters are evaluated in the order you insert them.</span></span> <span data-ttu-id="e1970-161">Der erste hat Vorrang.</span><span class="sxs-lookup"><span data-stu-id="e1970-161">The first one takes precedence.</span></span>

## <a name="next-steps"></a><span data-ttu-id="e1970-162">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="e1970-162">Next steps</span></span>

* <span data-ttu-id="e1970-163">[Beispiel-App für diese Dokumentation](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample), in der einfache Eingabe- und Ausgabeformatierer für vCard implementiert werden.</span><span class="sxs-lookup"><span data-stu-id="e1970-163">[Sample app for this doc](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample), which implements simple vCard input and output formatters.</span></span> <span data-ttu-id="e1970-164">Die App liest und schreibt vCards, die dem folgenden Beispiel ähneln:</span><span class="sxs-lookup"><span data-stu-id="e1970-164">The apps reads and writes vCards that look like the following example:</span></span>

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
UID:20293482-9240-4d68-b475-325df4a83728
END:VCARD
```

<span data-ttu-id="e1970-165">Wenn Sie die vCard-Ausgabe anzeigen wollen, führen Sie die Anwendung aus, und senden Sie eine Get-Anforderung mit dem Accept-Header „text/vcard“ an `http://localhost:63313/api/contacts/` (wenn Sie über Visual Studio arbeiten) oder `http://localhost:5000/api/contacts/` (wenn Sie über die Befehlszeile arbeiten).</span><span class="sxs-lookup"><span data-stu-id="e1970-165">To see vCard output, run the application and send a Get request with Accept header "text/vcard" to `http://localhost:63313/api/contacts/` (when running from Visual Studio) or `http://localhost:5000/api/contacts/` (when running from the command line).</span></span>

<span data-ttu-id="e1970-166">Wenn Sie eine vCard einer speicherinterne Sammlung von Kontakten hinzufügen möchten, senden Sie eine Post-Anforderung an dieselbe URL mit dem Content-Type-Header „text/vcard“ und einem vCard-Text, der wie im obenstehenden Beispiel formatiert ist.</span><span class="sxs-lookup"><span data-stu-id="e1970-166">To add a vCard to the in-memory collection of contacts, send a Post request to the same URL, with Content-Type header "text/vcard" and with vCard text in the body, formatted like the example above.</span></span>
