---
title: Razor-Komponenten-JavaScript-interop
author: guardrex
description: Erfahren Sie, wie zum Aufrufen von JavaScript-Funktionen von .NET und .NET Methoden von JavaScript in Blazor und Razor-Komponenten-apps.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2019
uid: razor-components/javascript-interop
ms.openlocfilehash: f2588f4ed1ec2f01218283625fae4632d0a8ae58
ms.sourcegitcommit: 948e533e02c2a7cb6175ada20b2c9cabb7786d0b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/10/2019
ms.locfileid: "59515550"
---
# <a name="razor-components-javascript-interop"></a>Razor-Komponenten-JavaScript-interop

Durch [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), und [Luke Latham](https://github.com/guardrex)

Eine app für Razor-Komponenten kann JavaScript-Funktionen von .NET und .NET Aufrufen der Methoden von JavaScript-Code.

## <a name="invoke-javascript-functions-from-net-methods"></a>JavaScript-Funktionen von .NET Methoden aufrufen

Es gibt Zeiten, in denen .NET Code erforderlich, um eine JavaScript-Funktion aufrufen. Beispielsweise kann ein JavaScript-Aufruf Browserfunktionen oder Funktionen aus einer JavaScript-Bibliothek für die app verfügbar machen.

Verwenden Sie zum Aufrufen von .NET in JavaScript die `IJSRuntime` Abstraktion. Die `InvokeAsync<T>` Methode nimmt einen Bezeichner für die JavaScript-Funktion, die Sie zusammen mit der eine beliebige Anzahl von JSON-serialisierbare Argumenten aufrufen möchten. Der Funktionsbezeichner ist relativ zu den globalen Bereich (`window`). Wenn Sie aufrufen möchten `window.someScope.someFunction`, der Bezeichner ist `someScope.someFunction`. Es ist nicht erforderlich, die die Funktion zu registrieren, bevor sie aufgerufen wird. Der Rückgabetyp `T` muss auch JSON serialisierbar sein.

Für serverseitige Komponenten von ASP.NET Core Razor-apps:

* Die serverseitige app werden mehrere benutzeranforderungen verarbeitet. Rufen Sie nicht `JSRuntime.Current` in einer Komponente zum Aufrufen von JavaScript-Funktionen.
* Einfügen der `IJSRuntime` Abstraktion und verwenden das eingefügte Objekt, das JavaScript-interop-Aufrufe ausgeben.

Das folgende Beispiel basiert auf [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), einen experimentellen JavaScript-basierten Decoder. Im Beispiel wird veranschaulicht, wie zum Aufrufen einer JavaScript-Funktion aus einem C# Methode. Die JavaScript-Funktion akzeptiert ein Bytearray aus einer C# Methode, die das Array decodiert und gibt den Text an die Komponente für die Anzeige zurück.

In der `<head>` Element *wwwroot/Index.HTML*, geben Sie eine Funktion, verwendet `TextDecoder` zu einem übergebenen Array zu decodierende:

```html
<script>
  window.ConvertArray = (win1251Array) => {
    var win1251decoder = new TextDecoder('windows-1251');
    var bytes = new Uint8Array(win1251Array);
    var decodedArray = win1251decoder.decode(bytes);
    console.log(decodedArray);
    return decodedArray;
  };
</script>
```

JavaScript-Code, z. B. den Code im vorherigen Beispiel kann auch aus einer JavaScript-Datei geladen werden (*js*) mit einem Verweis auf die Skriptdatei in die *wwwroot/Index.HTML* Datei:

```html
<script src="exampleJsInterop.js"></script>
```

Die folgende Komponente:

* Ruft die `ConvertArray` JavaScript-Funktion verwendet `JsRuntime` beim Klicken auf die Schaltfläche eine Komponente (**Array konvertieren**) ausgewählt ist.
* Nachdem die JavaScript-Funktion aufgerufen wurde, wird das übergebene Array in eine Zeichenfolge konvertiert. Die Zeichenfolge wird an die Komponente für die Anzeige zurückgegeben.

```cshtml
@page "/"
@inject IJSRuntime JsRuntime;

<h1>Call JavaScript Function Example</h1>

<button type="button" class="btn btn-primary" onclick="@ConvertArray">
    Convert Array
</button>

<p class="mt-2" style="font-size:1.6em">
    <span class="badge badge-success">
        @ConvertedText
    </span>
</p>

@functions {
    // Quote (c)2005 Universal Pictures: Serenity
    // https://www.uphe.com/movies/serenity
    // David Krumholtz on IMDB: https://www.imdb.com/name/nm0472710/

    private MarkupString ConvertedText =
        new MarkupString("Select the <b>Convert Array</b> button.");

    private uint[] QuoteArray = new uint[]
        {
            60, 101, 109, 62, 67, 97, 110, 39, 116, 32, 115, 116, 111, 112, 32,
            116, 104, 101, 32, 115, 105, 103, 110, 97, 108, 44, 32, 77, 97,
            108, 46, 60, 47, 101, 109, 62, 32, 45, 32, 77, 114, 46, 32, 85, 110,
            105, 118, 101, 114, 115, 101, 10, 10,
        };

    private async void ConvertArray()
    {
        var text =
            await JsRuntime.InvokeAsync<string>("ConvertArray", QuoteArray);

        ConvertedText = new MarkupString(text);

        StateHasChanged();
    }
}
```

Verwenden der `IJSRuntime` Abstraktion, die einen der folgenden Ansätze verwenden:

* Einfügen der `IJSRuntime` Abstraktion in die Razor-Datei (*.razor*, *.cshtml*):

  ```cshtml
  @inject IJSRuntime JSRuntime

  @functions {
      public override void OnInit()
      {
          StocksService.OnStockTickerUpdated += stockUpdate =>
          {
              JSRuntime.InvokeAsync<object>(
                  "handleTickerChanged",
                  stockUpdate.symbol,
                  stockUpdate.price);
          };
      }
  }
  ```

* Einfügen der `IJSRuntime` Abstraktion in einer Klasse (*cs*):

  ```csharp
  public class JsInteropClasses
  {
      private readonly IJSRuntime _jsRuntime;

      public JsInteropClasses(IJSRuntime jsRuntime)
      {
          _jsRuntime = jsRuntime;
      }

      public Task<string> TickerChanged(string data)
      {
          // The handleTickerChanged JavaScript method is implemented
          // in a JavaScript file, such as 'wwwroot/tickerJsInterop.js'.
          return _jsRuntime.InvokeAsync<object>(
              "handleTickerChanged",
              stockUpdate.symbol,
              stockUpdate.price);
      }
  }
  ```

* Für das dynamische Generieren von Inhalten mit `BuildRenderTree`, verwenden Sie die `[Inject]` Attribut:

  ```csharp
  [Inject] IJSRuntime JSRuntime { get; set; }
  ```

In der Client-Side-Beispiel-app, die in diesem Thema begleitet, werden zwei JavaScript-Funktionen für die clientseitige Anwendung die Interaktion mit dem DOM zum Empfangen von Benutzereingaben und Anzeigen von eine Begrüßungsnachricht an verfügbar:

* `showPrompt` &ndash; Erzeugt eine Aufforderung zum Akzeptieren der Benutzereingabe (Name des Benutzers), und gibt den Namen an den Aufrufer zurück.
* `displayWelcome` &ndash; Weist eine Begrüßungsnachricht an vom Aufrufer ein DOM-Objekt mit einer `id` von `welcome`.

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=2-7)]

Ort der `<script>` Tag, das in der JavaScript-Datei verweist auf die *wwwroot/Index.HTML* Datei:

[!code-html[](./common/samples/3.x/BlazorSample/wwwroot/index.html?highlight=15)]

Platzieren Sie keine `<script>` tag in einer Komponentendatei, da die `<script>` Tag kann nicht dynamisch aktualisiert werden.

.NET Methoden Interop mit dem JavaScript-Funktionen in der *exampleJsInterop.js* Datei durch Aufrufen von `IJSRuntime.InvokeAsync<T>`.

Die `IJSRuntime` Abstraktion ist asynchron, um die serverseitigen Szenarios zu ermöglichen. Wenn die app ausgeführt, die clientseitige wird und eine JavaScript-Funktion synchron aufgerufen werden soll, Typumwandlung `IJSInProcessRuntime` , und rufen Sie `Invoke<T>` stattdessen. Es wird empfohlen, dass die meisten Interop-JavaScript-Bibliotheken die asynchronen APIs verwenden, um sicherzustellen, dass die Bibliotheken in allen Szenarien, die clientseitige oder serverseitige verfügbar sind.

Die Beispiel-app enthält eine Komponente, um JavaScript-Interop demonstrieren. Die Komponente:

* Erhält Benutzereingaben über eine JavaScript-Eingabeaufforderung.
* Der Text wird an die Komponente für die Verarbeitung zurückgegeben.
* Ruft eine zweite JavaScript-Funktion, die mit dem DOM zum Anzeigen einer Begrüßungsnachricht interagiert.

*Pages/JSInterop.cshtml*:

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.cshtml?name=snippet_JSInterop1&highlight=3,19-21,23-25)]

1. Wenn `TriggerJsPrompt` durch Auswahl der Komponente ausgeführt wird **Trigger JavaScript Eingabeaufforderung** Schaltfläche den JavaScript-Code `showPrompt` Funktion aus der *wwwroot/exampleJsInterop.js* Datei ist wird aufgerufen.
1. Die `showPrompt` -Funktion akzeptiert Benutzereingaben (Name des Benutzers), die HTML-codiert und an die Komponente zurückgegeben. Die Komponente speichert den Namen des Benutzers in einer lokalen Variablen, `name`.
1. Die Zeichenfolge, die in gespeicherten `name` wird in eine Begrüßungsnachricht an, die eine JavaScript-Funktion übergeben wird, integriert `displayWelcome`, die Willkommensnachricht in der Überschrift-Tag gerendert.

## <a name="capture-references-to-elements"></a>Erfassen Sie Verweise auf Elemente

Einige [JavaScript-Interop](xref:razor-components/javascript-interop) Szenarien müssen Verweise auf HTML-Elemente. Beispielsweise kann eine UI-Bibliothek ein Elementverweises benötigen, für die Initialisierung, oder müssen Sie möglicherweise Befehl-ähnliche APIs aufrufen auf ein Element, z. B. `focus` oder `play`.

Sie können Verweise auf HTML-Elemente in einer Komponente erfassen, durch das Hinzufügen eine `ref` -Attribut auf das HTML-Element und das anschließende definieren ein Feld vom Typ `ElementRef` , deren Name entspricht dem Wert der `ref` Attribut.

Das folgende Beispiel zeigt einen Verweis auf das Eingabeelement Benutzernamen erfassen:

```cshtml
<input ref="username" ...>

@functions {
    ElementRef username;
}
```

> [!NOTE]
> Führen Sie **nicht** erfasste Elementverweise als eine Möglichkeit zum Auffüllen des DOM verwenden Auf diese Weise kann die deklarative Renderingmodell beeinträchtigen.

Als .NET Code geht, eine `ElementRef` ist ein nicht transparentes Handle. Die *nur* Sache, die du `ElementRef` über über JavaScript-Interop an JavaScript-Code übergeben. Wenn Sie dies tun, empfängt der JavaScript-Seite-Code ein `HTMLElement` -Instanz, die sie mit normalen DOM-APIs verwenden kann.

Der folgende Code definiert z. B. eine .NET-Erweiterungsmethode, die es ermöglicht, den Fokus auf ein Element festlegen:

*exampleJsInterop.js*:

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

Verwendung `IJSRuntime.InvokeAsync<T>` , und rufen Sie `exampleJsFunctions.focusElement` mit einer `ElementRef` ein Element zu konzentrieren:

[!code-cshtml[](javascript-interop/samples_snapshot/component1.cshtml?highlight=1,3,7,11-12)]

Um eine Erweiterungsmethode verwenden, um ein Element zu konzentrieren, erstellen Sie eine statische Erweiterungsmethode, die empfängt die `IJSRuntime` Instanz:

```csharp
public static Task Focus(this ElementRef elementRef, IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<object>(
        "exampleJsFunctions.focusElement", elementRef);
}
```

Die Methode wird direkt auf das Objekt aufgerufen. Im folgende Beispiel wird vorausgesetzt, dass die statische `Focus` Methode steht in der `JsInteropClasses` Namespace:

[!code-cshtml[](javascript-interop/samples_snapshot/component2.cshtml?highlight=1,4,8,12)]

> [!IMPORTANT]
> Die `username` Variable wird nur aufgefüllt, nachdem die Komponente rendert und die Ausgabe enthält die `>` Element. Wenn Sie versuchen, eine nicht aufgefüllte übergeben `ElementRef` für JavaScript-Code, der JavaScript-Code empfängt `null`. Elementverweise zu ändern, nachdem die Komponente verwenden (um den Anfangsfokus für ein Element festgelegt) Rendern abgeschlossen ist die `OnAfterRenderAsync` oder `OnAfterRender` [Komponente Lebenszyklusmethoden](xref:razor-components/components#lifecycle-methods).

## <a name="invoke-net-methods-from-javascript-functions"></a>Aufrufen von .NET Methoden von JavaScript-Funktionen

### <a name="static-net-method-call"></a>Statischen Methodenaufruf in .NET

Um eine statische .NET-Methode aus JavaScript aufrufen zu können, verwenden Sie die `DotNet.invokeMethod` oder `DotNet.invokeMethodAsync` Funktionen. Übergeben Sie den Bezeichner der statischen Methode, die Sie aufrufen, den Namen der Assembly mit der Funktion und alle Argumente möchten. Die asynchrone Version wird bevorzugt, um serverseitige Szenarien zu unterstützen. Um aus JavaScript aufgerufen werden, die .NET-Methode muss öffentliche, statische und durch `[JSInvokable]`. Der Methodenbezeichner wird standardmäßig der Name der Methode, aber Sie können angeben, einen anderen Bezeichner mit dem `JSInvokableAttribute` Konstruktor. Offene generische Methoden aufrufen wird derzeit nicht unterstützt.

Die beispielanwendung enthält einen C# Methode, um ein Array von zurückzugeben `int`s. Die Methode wird ergänzt, mit der `JSInvokable` Attribut.

*Pages/JsInterop.cshtml*:

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.cshtml?name=snippet_JSInterop2&highlight=7-11)]

JavaScript, die für den Client bereitgestellt Ruft die C# .NET-Methode übergeben.

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=8-14)]

Wenn die **Trigger .NET statische Methode ReturnArrayAsync** ausgewählt ist, überprüfen Sie die Konsolenausgabe in im Browser auf die Web-Entwicklertools.

Die Konsolenausgabe wird:

```console
Array(4) [ 1, 2, 3, 4 ]
```

Der vierte Arraywert wird in das Array abgelegt (`data.push(4);`) vom `ReturnArrayAsync`.

### <a name="instance-method-call"></a>Instanz-Methodenaufruf

Sie können auch Instanzmethoden für .NET aus JavaScript aufrufen. Um eine Instanzmethode für .NET aus JavaScript aufrufen zu können, zuerst übergeben Sie die .NET Instanz in JavaScript durch wrapping in eine `DotNetObjectRef` Instanz. Die .NET-Instanz als Verweis an JavaScript übergeben wird, und Sie können .NET von Instanzmethoden für die Instanz mit Aufrufen der `invokeMethod` oder `invokeMethodAsync` Funktionen. Die Instanz der .NET kann auch als Argument übergeben werden, wenn andere .NET Methoden von JavaScript aufgerufen.

> [!NOTE]
> Die Beispiel-app protokolliert Meldungen in die Client-Side-Konsole. Überprüfen Sie für den folgenden Beispielen gezeigt, die von der Beispiel-app im Browser auf die Konsolenausgabe in das browserentwicklertools.

Wenn die **Trigger .NET Instanzmethode HelloHelper.SayHello** ausgewählt ist, `ExampleJsInterop.CallHelloHelperSayHello` aufgerufen wird, und übergeben Sie einen Namen, `Blazor`, an die Methode.

*Pages/JsInterop.cshtml*:

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.cshtml?name=snippet_JSInterop3&highlight=8-9)]

`CallHelloHelperSayHello` Ruft die JavaScript-Funktion `sayHello` durch eine neue Instanz der `HelloHelper`.

*JsInteropClasses/ExampleJsInterop.cs*:

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=15-18)]

Der Name wird zum übergeben `HelloHelper`Konstruktor, der legt der `HelloHelper.Name` Eigenschaft. Wenn die JavaScript-Funktion `sayHello` ausgeführt wird, `HelloHelper.SayHello` gibt die `Hello, {Name}!` -Nachricht, die von der JavaScript-Funktion in die Konsole geschrieben wird.

*JsInteropClasses/HelloHelper.cs*:

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

Ausgabe im im Browser auf die Web-Entwicklertools-Konsole:

```console
Hello, Blazor!
```

## <a name="share-interop-code-in-a-razor-component-class-library"></a>Freigeben von Interop-Code in einer Klassenbibliothek für die Razor-Komponente

Interop-JavaScript-Code in einer Klassenbibliothek für die Razor-Komponente enthalten sein kann (`dotnet new razorclasslib`), können Sie den Code in einem NuGet-Paket verwenden.

Die Komponente der Razor-Klassenbibliothek behandelt die Einbettung von JavaScript-Ressourcen in der erstellten Assembly. Die JavaScript-Dateien befinden sich der *"Wwwroot"* Ordner. Das Tool übernimmt Einbettung der Ressourcen aus, wenn die Bibliothek erstellt wird.

Das erstellte NuGet-Paket wird in der Projektdatei der app verwiesen werden, genau wie normale NuGet-Pakets verwiesen wird. Nach der Wiederherstellung der app kann app-Code in JavaScript aufrufen, als wäre er C#.

Weitere Informationen finden Sie unter <xref:razor-components/class-libraries>.
