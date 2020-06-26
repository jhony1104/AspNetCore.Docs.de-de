---
title: Benutzerdefinierte Formatierer in Web-APIs in ASP.NET Core
author: rick-anderson
description: Informationen zum Erstellen und Verwenden von benutzerdefinierten Formatierern für Web-APIs in ASP.NET Core.
ms.author: riande
ms.date: 02/08/2017
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/advanced/custom-formatters
ms.openlocfilehash: 27819f77cf86c946ab0415d3583dfbab80a24cf5
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408863"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a>Benutzerdefinierte Formatierer in Web-APIs in ASP.NET Core

Von [Kirk Larkin](https://twitter.com/serpent5) und [Tom Dykstra](https://github.com/tdykstra).

ASP.NET Core MVC unterstützt den Datenaustausch in Web-APIs mithilfe von Eingabe- und Ausgabeformatierern. Eingabeformatierer werden von der [Modellbindung](xref:mvc/models/model-binding) verwendet. Ausgabe Formatierer werden verwendet, um [Antworten zu formatieren](xref:web-api/advanced/formatting).

Das Framework stellt integrierte Eingabe- und Ausgabeformatierer für JSON und XML bereit. Es stellt einen integrierten Ausgabeformatierer für Nur-Text bereit, jedoch keinen Eingabeformatierer.

In diesem Artikel wird dargestellt, wie Sie die Unterstützung von zusätzlichen Formaten hinzufügen, indem Sie benutzerdefinierte Formatierer erstellen. Ein Beispiel für einen benutzerdefinierten Formatierer für die nur-Text-Eingabe finden Sie unter [textplaininputformatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) auf GitHub.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="when-to-use-custom-formatters"></a>Empfohlene Verwendung von benutzerdefinierten Formatierern

Verwenden Sie einen benutzerdefinierten Formatierer, um Unterstützung für einen Inhaltstyp hinzuzufügen, der nicht von den "BULT in"-Formatierern behandelt wird

## <a name="overview-of-how-to-use-a-custom-formatter"></a>Übersicht über die Verwendung des Formatierers

So erstellen Sie einen benutzerdefinierten Formatierer:

* Zum Serialisieren von Daten, die an den Client gesendet werden, erstellen Sie eine Ausgabe formatterklasse.
* Zum Deserialisieren von Daten, die vom Client empfangen werden, erstellen Sie eine eingabeformatterklasse.
* Fügen Sie den `InputFormatters` -und- `OutputFormatters` [Auflistungen in mvcoptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions)Instanzen von formatiererklassen hinzu.

## <a name="how-to-create-a-custom-formatter-class"></a>Erstellen einer benutzerdefinierten Formatiererklasse

Erstellen eines Formatierers:

* Leiten Sie die Klasse von der entsprechenden Basisklasse ab. Die Beispiel-APP wird von <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> und abgeleitet <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> .
* Geben Sie gültige Medientypen und Codierungen im Konstruktor an.
* Überschreiben Sie die <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.CanReadType%2A>- und <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter.CanWriteType%2A>-Methoden.
* Überschreiben Sie die <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.ReadRequestBodyAsync%2A>- und `WriteResponseBodyAsync`-Methoden.

Der folgende Code zeigt die- `VcardOutputFormatter` Klasse aus dem [Beispiel](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/3.1sample):

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=snippet)]
  
### <a name="derive-from-the-appropriate-base-class"></a>Ableiten von der entsprechenden Basisklasse

Leiten Sie für Textmedientypen wie vCard von den Basisklassen [TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) oder [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter) ab.

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=classdef)]

Leiten Sie für Binärtypen von den Basisklassen [InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) oder [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter) ab.

### <a name="specify-valid-media-types-and-encodings"></a>Angeben von gültigen Medientypen und Codierungen

Geben Sie im Konstruktor gültige Medientypen und Codierungen an, indem Sie `SupportedMediaTypes`- und `SupportedEncodings`-Sammlungen hinzufügen.

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=ctor)]

Eine Formatiererklasse kann **keine** Konstruktorinjektion für ihre Abhängigkeiten verwenden. `ILogger<VcardOutputFormatter>`Kann z. b. nicht als Parameter zum-Konstruktor hinzugefügt werden. Verwenden Sie zum Zugreifen auf Dienste das Kontext Objekt, das an die-Methoden übermittelt wird. Ein Codebeispiel in diesem Artikel und das [Beispiel](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/3.1sample) zeigen, wie Sie dies tun.

### <a name="override-canreadtype-and-canwritetype"></a>Überschreiben von "canlestype" und "CanWrite Type"

Geben Sie den zu deserialisierenden oder zu serialisierenden Typ durch Überschreiben der- `CanReadType` Methode oder der- `CanWriteType` Methode an Beispielsweise das Erstellen von vCard-Text von einem `Contact` Typ und umgekehrt.

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=canwritetype)]

#### <a name="the-canwriteresult-method"></a>Die CanWriteResult-Methode

In einigen Szenarien `CanWriteResult` muss anstelle von überschrieben werden `CanWriteType` . Verwenden Sie `CanWriteResult`, wenn alle der folgenden Bedingungen zutreffen:

* Die Aktionsmethode gibt eine Modell Klasse zurück.
* Es gibt abgeleitete Klassen, die möglicherweise zur Laufzeit zurückgegeben werden.
* Die von der Aktion zurückgegebene abgeleitete Klasse muss zur Laufzeit bekannt sein.

Angenommen, die Aktionsmethode lautet wie folgt:

* Signature gibt einen `Person` Typ zurück.
* Kann einen- `Student` oder-Typ zurückgeben `Instructor` , der von abgeleitet wird `Person` . 

Damit der Formatierer nur- `Student` Objekte behandelt, überprüfen Sie den Typ des [Objekts](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext.object#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) im Kontext Objekt, das für die-Methode bereitgestellt wird `CanWriteResult` . Wenn die Aktionsmethode Folgendes zurückgibt `IActionResult` :

* Es ist nicht erforderlich, zu verwenden `CanWriteResult` .
* Die- `CanWriteType` Methode empfängt den Lauf Zeittyp.

<a id="read-write"></a>

### <a name="override-readrequestbodyasync-and-writeresponsebodyasync"></a>Überschreiben von "Read requestbodyasync" und "schreiteresponsebodyasync"

Die Deserialisierung oder Serialisierung wird in `ReadRequestBodyAsync` oder ausgeführt `WriteResponseBodyAsync` . Im folgenden Beispiel wird gezeigt, wie Sie Dienste aus dem Container für die Abhängigkeitsinjektion erhalten. Dienste können nicht aus Konstruktorparametern abgerufen werden.

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=writeresponse)]

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a>Konfigurieren von MVC zum Verwenden eines benutzerdefinierten Formatierers

Wenn Sie einen benutzerdefinierten Formatierer verwenden, fügen Sie der `InputFormatters`- oder `OutputFormatters`-Sammlung eine Instanz der Formatiererklasse hinzu.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](custom-formatters/3.1sample/Startup.cs?name=mvcoptions)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](custom-formatters/sample/Startup.cs?name=mvcoptions&highlight=3-4)]

::: moniker-end

Formatierer werden in der Reihenfolge überprüft, in der Sie sie einfügen. Der erste hat Vorrang.

## <a name="the-completed-vcardinputformatter-class"></a>Die abgeschlossene `VcardInputFormatter` Klasse

Der folgende Code zeigt die- `VcardInputFormatter` Klasse aus dem [Beispiel](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/3.1sample):

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardInputFormatter.cs?name=snippet)]

## <a name="test-the-app"></a>Testen der App

[Führen Sie die Beispiel-App für diesen Artikel aus](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample), der grundlegende vCard-Eingabe-und-Ausgabe Formatierer implementiert. Die APP liest und schreibt vCards ähnlich der folgenden:

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
END:VCARD
```

Um die vcardausgabe anzuzeigen, führen Sie die APP aus, und senden Sie eine GET-Anforderung mit dem Accept-Header `text/vcard` an `https://localhost:5001/api/contacts` .

So fügen Sie der Auflistung von Kontakten im Arbeitsspeicher eine vCard hinzu:

* Senden Sie eine `Post` Anforderung an `/api/contacts` mit einem Tool wie postman.
* Legen Sie den Header `Content-Type` auf `text/vcard` fest.
* Legen Sie `vCard` Text im Textkörper fest, wie im vorherigen Beispiel dargestellt.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:web-api/advanced/formatting>
* <xref:grpc/dotnet-grpc>
