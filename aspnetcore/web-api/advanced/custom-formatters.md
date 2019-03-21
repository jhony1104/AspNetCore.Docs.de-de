---
title: Benutzerdefinierte Formatierer in Web-APIs in ASP.NET Core
author: rick-anderson
description: Informationen zum Erstellen und Verwenden von benutzerdefinierten Formatierern für Web-APIs in ASP.NET Core.
ms.author: tdykstra
ms.date: 02/08/2017
uid: web-api/advanced/custom-formatters
ms.openlocfilehash: 611840defd1da3b57b365c99deaf1c67f1568227
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58264630"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a>Benutzerdefinierte Formatierer in Web-APIs in ASP.NET Core

Von [Tom Dykstra](https://github.com/tdykstra)

In ASP.NET Core MVC ist die Unterstützung für Datenaustausch in Web-APIs über JSON oder integriert. In diesem Artikel wird dargestellt, wie Sie die Unterstützung von zusätzlichen Formaten hinzufügen, indem Sie benutzerdefinierte Formatierer erstellen.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="when-to-use-custom-formatters"></a>Empfohlene Verwendung von benutzerdefinierten Formatierern

Verwenden Sie einen benutzerdefinierten Formatierer, wenn Sie möchten, dass der [Inhaltaushandlungsvorgang](xref:web-api/advanced/formatting#content-negotiation) einen Inhaltstyp unterstützt, der nicht von den integrierten Formatieren (JSON und XML) unterstützt wird.

Wenn z.B. einige der Clients für Ihre Web-API das [Protobuf](https://github.com/google/protobuf)-Format verarbeiten können, sollten Sie auch Protobuf für diese Clients verwenden, wenn Sie effizient arbeiten möchten. Möglicherweise möchten Sie aber lieber Ihre Web-API verwenden, um Namen und Adressen von Kontakten im [vCard](https://wikipedia.org/wiki/VCard)-Format zu versenden – ein häufig zum Austauschen von Kontaktdaten verwendetes Format. Die in diesem Artikel enthaltene Beispiel-App implementiert einen einfachen vCard-Formatierer.

## <a name="overview-of-how-to-use-a-custom-formatter"></a>Übersicht über die Verwendung des Formatierers

Im Folgenden werden einige Schritte zum Erstellen und Verwenden eines benutzerdefinierten Formatierers beschrieben:

* Erstellen Sie eine Klasse für den Ausgabeformatierer, wenn Sie die Daten serialisieren möchten, um diese an den Client zu senden.
* Erstellen Sie eine Klasse für den Eingabeformatierer, wenn Sie die Daten deserialisieren möchten, die vom Client gesendet wurden.
* Fügen Sie Instanzen Ihrer Formatierer zu den Auflistungen `InputFormatters` und `OutputFormatters` in [MvcOptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions) hinzu.

In den Folgenden Abschnitten erhalten Sie Anweisungen und Codebeispiele zu diesen Schritten.

## <a name="how-to-create-a-custom-formatter-class"></a>Erstellen einer benutzerdefinierten Formatiererklasse

Erstellen eines Formatierers:

* Leiten Sie die Klasse von der entsprechenden Basisklasse ab.
* Geben Sie gültige Medientypen und Codierungen im Konstruktor an.
* Überschreiben Sie die Methoden `CanReadType`/`CanWriteType`
* Überschreiben Sie die Methoden `ReadRequestBodyAsync`/`WriteResponseBodyAsync`
  
### <a name="derive-from-the-appropriate-base-class"></a>Ableiten von der entsprechenden Basisklasse

Leiten Sie für Textmedientypen wie vCard von den Basisklassen [TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) oder [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter) ab.

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=classdef)]

Einen Eingabeformatierer finden Sie z.B. in der [Beispiel-App](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).

Leiten Sie für Binärtypen von den Basisklassen [InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) oder [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter) ab.

### <a name="specify-valid-media-types-and-encodings"></a>Angeben von gültigen Medientypen und Codierungen

Geben Sie im Konstruktor gültige Medientypen und Codierungen an, indem Sie `SupportedMediaTypes`- und `SupportedEncodings`-Sammlungen hinzufügen.

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=ctor&highlight=3,5-6)]

Einen Eingabeformatierer finden Sie z.B. in der [Beispiel-App](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).

> [!NOTE]
> Sie können in einer Formatiererklasse keine Dependency Injection für den Konstruktor durchführen. Beispielsweise können Sie keine Protokollierung abrufen, indem Sie dem Konstruktor den Protokollparameter hinzufügen. Sie müssen das Kontextobjekt verwenden, das an Ihre Methoden übergeben wird, um auf Dienste zuzugreifen. Im [nachfolgenden](#read-write) Codebeispiel wird deutlich, wie Sie hierzu vorgehen.

### <a name="override-canreadtypecanwritetype"></a>Überschreiben von „CanReadType/CanWriteType“

Geben Sie den Typ an, den Sie deserialisieren bzw. serialisieren möchten, indem Sie die Methoden `CanReadType` oder `CanWriteType` überschreiben. Es kann z.B. sein, dass Sie nur vCard-Text über einen `Contact`-Typ bzw. umgekehrt erstellen können.

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=canwritetype)]

Einen Eingabeformatierer finden Sie z.B. in der [Beispiel-App](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).

#### <a name="the-canwriteresult-method"></a>Die CanWriteResult-Methode

Manchmal müssen Sie `CanWriteResult` anstelle von `CanWriteType` überschreiben. Verwenden Sie `CanWriteResult`, wenn alle der folgenden Bedingungen zutreffen:

* Ihre Aktionsmethode gibt eine Modellklasse zurück.
* Es gibt abgeleitete Klassen, die möglicherweise zur Laufzeit zurückgegeben werden.
* Sie müssen zur Laufzeit wissen, welche abgeleitete Klasse von der Aktion zurückgegeben wurde.

Nehmen Sie z.B. an, dass die Signatur Ihrer Aktionsmethode einen `Person`-Typ zurückgibt. Sie kann dann aber auch den Typ `Student` oder `Instructor` zurückgeben, der von `Person` abgeleitet wird. Wenn Sie möchten, dass Ihr Formatierer nur `Student`-Objekte verarbeitet, überprüfen Sie den [Objekt](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object)-Typ im Kontextobjekt, das in der `CanWriteResult`-Methode zur Verfügung gestellt wurde. Beachten Sie, dass es nicht nötig ist, `CanWriteResult` zu verwenden, wenn die Aktionsmethode `IActionResult` zurückgibt. In diesem Fall erhält die `CanWriteType`-Methode den Runtimetyp.

<a id="read-write"></a>

### <a name="override-readrequestbodyasyncwriteresponsebodyasync"></a>Überschreiben von „ReadRequestBodyAsync/WriteResponseBodyAsync“

Sie deserialisieren oder serialisieren manuell in `ReadRequestBodyAsync` oder `WriteResponseBodyAsync`. Die markierten Zeilen im folgenden Beispiel zeigen, wie Sie Dienste aus dem Dependency Injection-Container abrufen. Sie können diese nicht aus den Konstruktorparametern abrufen.

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=writeresponse&highlight=3-4)]

Einen Eingabeformatierer finden Sie z.B. in der [Beispiel-App](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a>Konfigurieren von MVC zum Verwenden eines benutzerdefinierten Formatierers

Wenn Sie einen benutzerdefinierten Formatierer verwenden, fügen Sie der `InputFormatters`- oder `OutputFormatters`-Sammlung eine Instanz der Formatiererklasse hinzu.

[!code-csharp[](custom-formatters/sample/Startup.cs?name=mvcoptions&highlight=3-4)]

Formatierer werden in der Reihenfolge überprüft, in der Sie sie einfügen. Der erste hat Vorrang.

## <a name="next-steps"></a>Nächste Schritte

* [Beispielcode für Nur-Text-Formatierer auf GitHub.](https://github.com/aspnet/Entropy/tree/master/samples/Mvc.Formatters)
* [Beispiel-App für diese Dokumentation](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample), in der einfache Eingabe- und Ausgabeformatierer für vCard implementiert werden. Die App liest und schreibt vCards, die dem folgenden Beispiel ähneln:

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
UID:20293482-9240-4d68-b475-325df4a83728
END:VCARD
```

Wenn Sie die vCard-Ausgabe anzeigen wollen, führen Sie die Anwendung aus, und senden Sie eine Get-Anforderung mit dem Accept-Header „text/vcard“ an `http://localhost:63313/api/contacts/` (wenn Sie über Visual Studio arbeiten) oder `http://localhost:5000/api/contacts/` (wenn Sie über die Befehlszeile arbeiten).

Wenn Sie eine vCard einer speicherinterne Sammlung von Kontakten hinzufügen möchten, senden Sie eine Post-Anforderung an dieselbe URL mit dem Content-Type-Header „text/vcard“ und einem vCard-Text, der wie im obenstehenden Beispiel formatiert ist.
