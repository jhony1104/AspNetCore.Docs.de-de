---
title: ASP.net Core blazor JavaScript-Interop
author: guardrex
description: Erfahren Sie, wie Sie JavaScript-Funktionen aus .net-und .NET-Methoden von JavaScript in blazor-apps aufrufen.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2019
uid: blazor/javascript-interop
ms.openlocfilehash: e578a8ad1484a2ef93bdc7470985937c4f28b7ed
ms.sourcegitcommit: 8b36f75b8931ae3f656e2a8e63572080adc78513
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/05/2019
ms.locfileid: "70310455"
---
# <a name="aspnet-core-blazor-javascript-interop"></a>ASP.net Core blazor JavaScript-Interop

Von [Javier calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27)und [Luke Latham](https://github.com/guardrex)

Eine blazor-App kann JavaScript-Funktionen aus .net-und .NET-Methoden aus JavaScript-Code aufrufen.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="invoke-javascript-functions-from-net-methods"></a>Aufrufen von JavaScript-Funktionen aus .NET-Methoden

Es gibt Zeiten, in denen .NET-Code erforderlich ist, um eine JavaScript-Funktion aufzurufen. Beispielsweise kann ein JavaScript-Befehl Browserfunktionen oder-Funktionen aus einer JavaScript-Bibliothek für die app verfügbar machen.

Zum Abrufen von JavaScript aus .NET verwenden Sie die `IJSRuntime` Abstraktion. Die `InvokeAsync<T>` -Methode nimmt einen Bezeichner für die JavaScript-Funktion an, die Sie zusammen mit einer beliebigen Anzahl von JSON-serialisierbaren Argumenten aufrufen möchten. Der Funktions Bezeichner ist relativ zum globalen Gültigkeits`window`Bereich (). Wenn Sie anrufen `window.someScope.someFunction`möchten, ist `someScope.someFunction`der Bezeichner. Es ist nicht erforderlich, die Funktion zu registrieren, bevor Sie aufgerufen wird. Der Rückgabetyp `T` muss auch JSON-serialisierbar sein.

Für serverseitige apps:

* Mehrere Benutzer Anforderungen werden von der serverseitigen App verarbeitet. Rufen Sie `JSRuntime.Current` nicht in einer Komponente auf, um JavaScript-Funktionen aufzurufen.
* Fügen Sie `IJSRuntime` die Abstraktion ein, und verwenden Sie das injizierte Objekt, um JavaScript-Interop-Aufrufe auszugeben
* Während eine blazor-App vorab erstellt wird, ist das Aufrufen von JavaScript nicht möglich, da keine Verbindung mit dem Browser hergestellt wurde. Weitere Informationen finden Sie im Abschnitt [erkennen, wenn eine blazor-App eine Vorabversion ist](#detect-when-a-blazor-app-is-prerendering) .

Das folgende Beispiel basiert auf [textdecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), einem experimentellen JavaScript-basierten Decoder. Im Beispiel wird veranschaulicht, wie eine JavaScript-Funktion aus C# einer Methode aufgerufen wird. Die JavaScript-Funktion akzeptiert ein Bytearray C# aus einer Methode, decodiert das Array und gibt den Text zur Anzeige an die Komponente zurück.

Stellen Sie im `TextDecoder` - Elementvonwwwroot/Index.html(Clientseitig)oderPages/_Host.cshtml(serverseitigeblazor-Datei)eineFunktionbereit,dievonzumDecodiereneinesbestandenenArraysverwendet`<head>` wird:

[!code-html[](javascript-interop/samples_snapshot/index-script.html)]

JavaScript-Code, wie z. b. der im vorherigen Beispiel gezeigte Code, kann auch aus einer JavaScript-Datei ( *. js*) mit einem Verweis auf die Skriptdatei geladen werden:

```html
<script src="exampleJsInterop.js"></script>
```

Folgende Komponente:

* Ruft die `ConvertArray` JavaScript-Funktion `JsRuntime` mit auf, wenn eine Komponenten Schaltfläche (**Array konvertieren**) ausgewählt ist.
* Nachdem die JavaScript-Funktion aufgerufen wurde, wird das bestandene Array in eine Zeichenfolge konvertiert. Die Zeichenfolge wird zur Anzeige an die Komponente zurückgegeben.

[!code-cshtml[](javascript-interop/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

Um die `IJSRuntime` Abstraktion zu verwenden, übernehmen Sie einen der folgenden Ansätze:

* Fügen Sie `IJSRuntime` die Abstraktion in*die Razor-Komponente (Razor*-Komponente) ein:

  [!code-cshtml[](javascript-interop/samples_snapshot/inject-abstraction.razor?highlight=1)]

* Fügen Sie `IJSRuntime` die Abstraktion in eine Klasse ( *. cs*) ein:

  [!code-csharp[](javascript-interop/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

* Verwenden Sie für die Generierung dynamischer Inhalte mit [buildrendertree](xref:blazor/components#manual-rendertreebuilder-logic)das `[Inject]` -Attribut:

  ```csharp
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

In der Client seitigen Beispiel-APP, die dieses Thema begleitet, sind zwei JavaScript-Funktionen für die Client seitige app verfügbar, die mit dem Dom interagieren, um Benutzereingaben zu empfangen und eine Willkommensnachricht anzuzeigen:

* `showPrompt`&ndash; Generiert eine Aufforderung zur Annahme von Benutzereingaben (Name des Benutzers) und gibt den Namen an den Aufrufer zurück.
* `displayWelcome`Weist dem Aufrufer eine Begrüßungs Meldung `id` mit dem-Objekt von `welcome`zu. &ndash;

*wwwroot/examplejsinterop. js*:

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=2-7)]

Platzieren Sie `<script>` das-Tag, das auf die JavaScript-Datei verweist, in der *wwwroot/Index.html* -Datei (auf dem Clientseite blazor) oder in der Datei *pages/_Host. cshtml* (serverseitig, blazor).

*wwwroot/Index.html* (Client seitige blazor-Seite):

[!code-html[](./common/samples/3.x/BlazorSample/wwwroot/index.html?highlight=15)]

*Pages/_Host. cshtml* (Serverseitige blazor-Datei):

[!code-cshtml[](javascript-interop/samples_snapshot/_Host.cshtml?highlight=29)]

Platzieren `<script>` Sie kein Tag in einer Komponenten Datei, da `<script>` das Tag nicht dynamisch aktualisiert werden kann.

.NET-Methoden Interop mit den JavaScript-Funktionen in der Datei " *examplejsinterop. js* " durch Aufrufen `IJSRuntime.InvokeAsync<T>`von.

Die `IJSRuntime` Abstraktion ist asynchron, um serverseitige Szenarios zu ermöglichen. Wenn die APP Client seitig ausgeführt wird und Sie eine JavaScript-Funktion synchron aufrufen möchten, verwenden Sie stattdessen Typumwandlung in `Invoke<T>` , und rufen Sie stattdessen auf `IJSInProcessRuntime` . Es wird empfohlen, dass die meisten JavaScript-Interop-Bibliotheken die Async-APIs verwenden, um sicherzustellen, dass die Bibliotheken in allen Szenarien, auf Client-oder Serverseite verfügbar sind.

Die Beispiel-app enthält eine Komponente zum Veranschaulichen der JavaScript-Interop. Die Komponente:

* Empfängt Benutzereingaben über eine JavaScript-Eingabeaufforderung.
* Gibt den Text zur Verarbeitung an die Komponente zurück.
* Ruft eine zweite JavaScript-Funktion auf, die mit dem Dom interagiert, um eine Willkommensnachricht anzuzeigen.

*Pages/jsinterop. Razor*:

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop1&highlight=3,19-21,23-25)]

1. Wenn `TriggerJsPrompt` durch Auswählen der Schaltfläche JavaScript- **Eingabeaufforderung** für die Komponente ausgeführt wird `showPrompt` , wird die JavaScript-Funktion, die in der Datei *wwwroot/examplejsinterop. js* bereitgestellt wird, aufgerufen.
1. Die `showPrompt` Funktion akzeptiert Benutzereingaben (den Namen des Benutzers), die HTML-codiert sind und an die Komponente zurückgegeben werden. Die Komponente speichert den Benutzernamen in einer lokalen Variablen `name`.
1. Die in `name` gespeicherte Zeichenfolge wird in eine Begrüßungsnachricht eingebunden, die an eine JavaScript- `displayWelcome`Funktion,, die an eine-Überschriften Kennung gerendert wird.

## <a name="call-a-void-javascript-function"></a>JavaScript-Funktion "void" aufruft

JavaScript-Funktionen, die [void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) oder nicht [definiert](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) zurück `IJSRuntime.InvokeAsync<object>`geben, werden `null`mit aufgerufen, das zurückgibt.

## <a name="detect-when-a-blazor-app-is-prerendering"></a>Erkennen, wenn eine blazor-App vorab durchgeführt wird
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a>Verweise auf Elemente erfassen

Einige [JavaScript-Interop](xref:blazor/javascript-interop) -Szenarien erfordern Verweise auf HTML-Elemente. Beispielsweise kann eine UI-Bibliothek einen Element Verweis für die Initialisierung erfordern, oder Sie müssen möglicherweise Befehls ähnliche APIs für ein Element, z `focus` . b. oder `play`, aufzurufen.

Erfassen Sie mithilfe des folgenden Ansatzes Verweise auf HTML-Elemente in einer-Komponente:

* Fügen Sie `@ref` dem HTML--Element ein-Attribut hinzu.
* Definieren Sie ein Feld vom `ElementReference` Typ, dessen Name `@ref` mit dem Wert des-Attributs übereinstimmt.

Das folgende Beispiel zeigt, wie Sie einen Verweis `username` auf das `<input>` -Element erfassen:

```cshtml
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!NOTE]
> Verwenden Sie **keine** aufgezeichneten Element Verweise als Methode zum Auffüllen oder Bearbeiten des DOM, wenn blazor mit den Elementen interagiert, auf die verwiesen wird. Dadurch kann das deklarative Renderingmodell beeinträchtigt werden.

Wenn .NET-Code betroffen ist, ist ein `ElementReference` nicht transparentes Handle. Das *einzige* , was Sie tun `ElementReference` können, ist die Übergabe an JavaScript-Code über JavaScript-Interop. Wenn Sie dies tun, empfängt der JavaScript-seitige Code eine `HTMLElement` -Instanz, die für die Verwendung mit normalen DOM-APIs verwendet werden kann.

Der folgende Code definiert z. b. eine .net-Erweiterungsmethode, die das Festlegen des Fokus auf ein Element ermöglicht:

*examplejsinterop. js*:

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

Verwenden `IJSRuntime.InvokeAsync<T>` Sie`ElementReference` , `exampleJsFunctions.focusElement` und verwenden Sie, um ein Element zu fokussieren:

[!code-cshtml[](javascript-interop/samples_snapshot/component1.razor?highlight=1,3,11-12)]

Wenn Sie eine Erweiterungsmethode verwenden möchten, um ein Element zu fokussieren, erstellen Sie eine statische `IJSRuntime` Erweiterungsmethode, die die-Instanz empfängt:

```csharp
public static Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<object>(
        "exampleJsFunctions.focusElement", elementRef);
}
```

Die-Methode wird direkt für das-Objekt aufgerufen. Im folgenden Beispiel wird davon ausgegangen, `Focus` dass die statische-Methode `JsInteropClasses` im-Namespace verfügbar ist:

[!code-cshtml[](javascript-interop/samples_snapshot/component2.razor?highlight=1,4,12)]

> [!IMPORTANT]
> Die `username` Variable wird erst aufgefüllt, nachdem die Komponente gerendert wurde. Wenn eine nicht `ElementReference` aufgefüllte an JavaScript-Code übermittelt wird, erhält der JavaScript- `null`Code den Wert. Um Element Verweise zu manipulieren, nachdem die Komponente das Rendering abgeschlossen hat (um den anfänglichen Fokus auf ein Element fest `OnAfterRenderAsync` zulegen `OnAfterRender` ), verwenden Sie die-oder- [Komponenten Lebenszyklus Methoden](xref:blazor/components#lifecycle-methods).

## <a name="invoke-net-methods-from-javascript-functions"></a>Aufrufen von .NET-Methoden aus JavaScript-Funktionen

### <a name="static-net-method-call"></a>Statischer .net-Methodenaufrufe

Zum Aufrufen einer statischen .NET-Methode aus JavaScript verwenden Sie `DotNet.invokeMethod` die `DotNet.invokeMethodAsync` -Funktion oder die-Funktion. Übergeben Sie den Bezeichner der statischen Methode, die aufgerufen werden soll, den Namen der Assembly, die die Funktion enthält, und alle Argumente. Die asynchrone Version ist für die Unterstützung serverseitiger Szenarien bevorzugt. Um eine .NET-Methode aus JavaScript aufzurufen, muss die .NET-Methode öffentlich und statisch sein und `[JSInvokable]` über das-Attribut verfügen. Standardmäßig ist der Methoden Bezeichner der Methodenname, aber Sie können einen anderen Bezeichner mit dem `JSInvokableAttribute` -Konstruktor angeben. Das Aufrufen offener generischer Methoden wird derzeit nicht unterstützt.

Die Beispiel-app enthält C# eine-Methode, um ein `int`Array von s zurückzugeben. Das `JSInvokable` -Attribut wird auf die-Methode angewendet.

*Pages/jsinterop. Razor*:

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop2&highlight=7-11)]

JavaScript, das dem Client bereitgestellt C# wird, ruft die .NET-Methode auf

*wwwroot/examplejsinterop. js*:

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=8-14)]

Untersuchen Sie die Konsolenausgabe in den Webentwickler Tools des Browsers, wenn Sie die Schaltfläche " **.net static-Methode für rückgabetyasync** " aufrufen auswählen.

Die Konsolenausgabe sieht wie folgt aus:

```console
Array(4) [ 1, 2, 3, 4 ]
```

Der vierte Arraywert wird an das Array (`data.push(4);`) übermittelt `ReturnArrayAsync`, das von zurückgegeben wird.

### <a name="instance-method-call"></a>Instanzmethodenaufrufe

Sie können auch .net-Instanzmethoden von JavaScript aus abrufen. So rufen Sie eine .net-Instanzmethode aus JavaScript auf:

* Übergeben Sie die .net-Instanz in eine `DotNetObjectReference` -Instanz, um Sie an JavaScript zu übergeben. Die .net-Instanz wird als Verweis an JavaScript übermittelt.
* Rufen Sie mithilfe der `invokeMethod` Funktionen oder `invokeMethodAsync` .net-Instanzmethoden für die-Instanz auf. Die .net-Instanz kann auch als Argument beim Aufrufen anderer .NET-Methoden aus JavaScript übermittelt werden.

> [!NOTE]
> Die Beispiel-App protokolliert Nachrichten in der Client seitigen Konsole. Überprüfen Sie die Konsolenausgabe des Browsers in den Entwicklertools des Browsers auf die folgenden Beispiele, die von der Beispiel-App veranschaulicht werden.

Wenn die Schaltfläche **.net-Instanzmethode "hellohelper. SayHello" des Auslösers** aufgerufen wird `Blazor`, `ExampleJsInterop.CallHelloHelperSayHello` wird aufgerufen und übergibt den Namen an die-Methode.

*Pages/jsinterop. Razor*:

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop3&highlight=8-9)]

`CallHelloHelperSayHello`Ruft die JavaScript- `sayHello` Funktion mit einer neuen Instanz `HelloHelper`von auf.

*Jsinteropclasses/examplejsinterop. cs*:

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

*wwwroot/examplejsinterop. js*:

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=15-18)]

Der Name wird an `HelloHelper`den-Konstruktor übergeben, mit dem die `HelloHelper.Name` -Eigenschaft festgelegt wird. Wenn die JavaScript- `sayHello` Funktion ausgeführt wird `HelloHelper.SayHello` , gibt `Hello, {Name}!` die Meldung zurück, die von der JavaScript-Funktion in die Konsole geschrieben wird.

*Jsinteropclasses/hellohelper. cs*:

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

Konsolenausgabe in den Webentwickler Tools des Browsers:

```console
Hello, Blazor!
```

## <a name="share-interop-code-in-a-class-library"></a>Freigeben von Interop-Code in einer Klassenbibliothek

JavaScript-Interop-Code kann in einer Klassenbibliothek enthalten sein, die es Ihnen ermöglicht, den Code in einem nuget-Paket freizugeben.

Die Klassenbibliothek behandelt das Einbetten von JavaScript-Ressourcen in der erstellten Assembly. Die JavaScript-Dateien werden in den Ordner " *wwwroot* " eingefügt. Die Tools kümmern sich um die Einbettung der Ressourcen, wenn die Bibliothek erstellt wird.

Auf das integrierte nuget-Paket wird in der Projektdatei der APP genauso verwiesen, wie auf ein beliebiges nuget-Paket verwiesen wird. Nachdem das Paket wieder hergestellt wurde, kann der app-Code JavaScript so aufruft, C#als wäre es.

Weitere Informationen finden Sie unter <xref:blazor/class-libraries>.
