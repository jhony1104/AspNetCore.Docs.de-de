---
title: ASP.net Core Blazor JavaScript-Interop
author: guardrex
description: Erfahren Sie, wie Sie JavaScript-Funktionen aus .net-und .NET-Methoden aus JavaScript in Blazor-apps aufrufen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/javascript-interop
ms.openlocfilehash: 4edef123bc1fe41845b8060b9c3b8e77ffd2969d
ms.sourcegitcommit: c81ef12a1b6e6ac838e5e07042717cf492e6635b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76885471"
---
# <a name="aspnet-core-opno-locblazor-javascript-interop"></a>ASP.net Core Blazor JavaScript-Interop

Von [Javier calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27)und [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Eine Blazor-App kann JavaScript-Funktionen von .net-und .NET-Methoden aus JavaScript-Code aufrufen.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="invoke-javascript-functions-from-net-methods"></a>Aufrufen von JavaScript-Funktionen aus .NET-Methoden

Es gibt Zeiten, in denen .NET-Code erforderlich ist, um eine JavaScript-Funktion aufzurufen. Beispielsweise kann ein JavaScript-Befehl Browserfunktionen oder-Funktionen aus einer JavaScript-Bibliothek für die app verfügbar machen. Dieses Szenario wird als *JavaScript-Interoperabilität* (*js-Interop*) bezeichnet.

Verwenden Sie zum Abrufen von JavaScript aus .net die `IJSRuntime` Abstraktion. Um js-Interop-Aufrufe auszugeben, fügen Sie die `IJSRuntime` Abstraktion in die Komponente ein. Die `InvokeAsync<T>`-Methode nimmt einen Bezeichner für die JavaScript-Funktion an, die Sie zusammen mit einer beliebigen Anzahl von JSON-serialisierbaren Argumenten aufrufen möchten. Der Funktions Bezeichner ist relativ zum globalen Gültigkeitsbereich (`window`). Wenn Sie `window.someScope.someFunction`anrufen möchten, wird der Bezeichner `someScope.someFunction`. Es ist nicht erforderlich, die Funktion zu registrieren, bevor Sie aufgerufen wird. Der Rückgabetyp `T` muss ebenfalls JSON-serialisierbar sein. `T` sollte dem .NET-Typ entsprechen, der dem zurückgegebenen JSON-Typ am besten zugeordnet wird.

Bei Blazor Server-apps, bei denen die vorab Aktivierung aktiviert ist, ist das Aufrufen von JavaScript während der ersten vorab Generierung nicht möglich. JavaScript-Interop-Aufrufe müssen verzögert werden, bis die Verbindung mit dem Browser hergestellt ist. Weitere Informationen finden Sie im Abschnitt [erkennen, wenn eine Blazor-App eine Vorabversion ist](#detect-when-a-blazor-app-is-prerendering) .

Das folgende Beispiel basiert auf [textdecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), einem experimentellen JavaScript-basierten Decoder. Im Beispiel wird veranschaulicht, wie eine JavaScript-Funktion aus C# einer Methode aufgerufen wird. Die JavaScript-Funktion akzeptiert ein Bytearray C# aus einer Methode, decodiert das Array und gibt den Text zur Anzeige an die Komponente zurück.

Geben Sie im `<head>`-Element von *wwwroot/Index.html* (Blazor Webassembly) oder *pages/_Host. cshtml* (Blazor Server) eine JavaScript-Funktion an, die `TextDecoder` zum Decodieren eines bestandenen Arrays verwendet und den decodierten Wert zurückgibt:

[!code-html[](javascript-interop/samples_snapshot/index-script-convertarray.html)]

JavaScript-Code, wie z. b. der im vorherigen Beispiel gezeigte Code, kann auch aus einer JavaScript-Datei ( *. js*) mit einem Verweis auf die Skriptdatei geladen werden:

```html
<script src="exampleJsInterop.js"></script>
```

Folgende Komponente:

* Ruft die `convertArray` JavaScript-Funktion mithilfe von `JSRuntime` auf, wenn eine Komponenten Schaltfläche (**Array konvertieren**) ausgewählt ist.
* Nachdem die JavaScript-Funktion aufgerufen wurde, wird das bestandene Array in eine Zeichenfolge konvertiert. Die Zeichenfolge wird zur Anzeige an die Komponente zurückgegeben.

[!code-razor[](javascript-interop/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="use-of-ijsruntime"></a>Verwendung von ijsruntime

Um die `IJSRuntime` Abstraktion zu verwenden, übernehmen Sie einen der folgenden Ansätze:

* Fügen Sie die `IJSRuntime` Abstraktion in*die Razor-Komponente (Razor*) ein:

  [!code-razor[](javascript-interop/samples_snapshot/inject-abstraction.razor?highlight=1)]

  Geben Sie im `<head>`-Element von *wwwroot/Index.html* (Blazor Webassembly) oder *pages/_Host. cshtml* (Blazor Server) eine `handleTickerChanged` JavaScript-Funktion an. Die-Funktion wird mit `IJSRuntime.InvokeVoidAsync` aufgerufen und gibt keinen Wert zurück:

  [!code-html[](javascript-interop/samples_snapshot/index-script-handleTickerChanged1.html)]

* Fügen Sie die `IJSRuntime` Abstraktion in eine Klasse ( *. cs*) ein:

  [!code-csharp[](javascript-interop/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  Geben Sie im `<head>`-Element von *wwwroot/Index.html* (Blazor Webassembly) oder *pages/_Host. cshtml* (Blazor Server) eine `handleTickerChanged` JavaScript-Funktion an. Die-Funktion wird mit `JSRuntime.InvokeAsync` aufgerufen und gibt einen Wert zurück:

  [!code-html[](javascript-interop/samples_snapshot/index-script-handleTickerChanged2.html)]

* Verwenden Sie für die Generierung dynamischer Inhalte mit [buildrendertree](xref:blazor/components#manual-rendertreebuilder-logic)das `[Inject]`-Attribut:

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

In der Client seitigen Beispiel-APP, die dieses Thema begleitet, sind zwei JavaScript-Funktionen für die app verfügbar, die mit dem Dom interagieren, um Benutzereingaben zu empfangen und eine Willkommensnachricht anzuzeigen:

* `showPrompt` &ndash; generiert eine Aufforderung zur Annahme von Benutzereingaben (Name des Benutzers) und gibt den Namen an den Aufrufer zurück.
* `displayWelcome` &ndash; eine Begrüßungs Meldung vom Aufrufer einem DOM-Objekt mit einer `id` `welcome`zuweist.

*wwwroot/examplejsinterop. js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

Platzieren Sie das `<script>` Tag, das auf die JavaScript-Datei verweist, in der Datei *wwwroot/Index.html* (Blazor Webassembly) oder der Datei *pages/_Host. cshtml* (Blazor Server).

*wwwroot/Index.html* (Blazor Webassembly):

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

*Pages/_Host. cshtml* (Blazor Server):

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

Platzieren Sie kein `<script>`-Tag in einer Komponenten Datei, da das `<script>`-Tag nicht dynamisch aktualisiert werden kann.

.NET-Methoden Interop mit den JavaScript-Funktionen in der Datei " *examplejsinterop. js* " durch Aufrufen von `IJSRuntime.InvokeAsync<T>`.

Die `IJSRuntime` Abstraktion ist asynchron, um Blazor Server Szenarios zu ermöglichen. Wenn es sich bei der App um eine Blazor Webassembly-App handelt und Sie eine JavaScript-Funktion synchron aufrufen möchten, können Sie eine Typumwandlung-Funktion in `IJSInProcessRuntime` und stattdessen `Invoke<T>` aufrufen. Es wird empfohlen, dass die meisten js-Interop-Bibliotheken die Async-APIs verwenden, um sicherzustellen, dass die Bibliotheken in allen Szenarien verfügbar sind.

Die Beispiel-app enthält eine Komponente zum Veranschaulichen der JS-Interop. Die Komponente:

* Empfängt Benutzereingaben über eine JavaScript-Eingabeaufforderung.
* Gibt den Text zur Verarbeitung an die Komponente zurück.
* Ruft eine zweite JavaScript-Funktion auf, die mit dem Dom interagiert, um eine Willkommensnachricht anzuzeigen.

*Pages/jsinterop. Razor*:

```razor
@page "/JSInterop"
@using BlazorSample.JsInteropClasses
@inject IJSRuntime JSRuntime

<h1>JavaScript Interop</h1>

<h2>Invoke JavaScript functions from .NET methods</h2>

<button type="button" class="btn btn-primary" @onclick="TriggerJsPrompt">
    Trigger JavaScript Prompt
</button>

<h3 id="welcome" style="color:green;font-style:italic"></h3>

@code {
    public async Task TriggerJsPrompt()
    {
        // showPrompt is implemented in wwwroot/exampleJsInterop.js
        var name = await JSRuntime.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");
        // displayWelcome is implemented in wwwroot/exampleJsInterop.js
        await JSRuntime.InvokeVoidAsync(
                "exampleJsFunctions.displayWelcome",
                $"Hello {name}! Welcome to Blazor!");
    }
}
```

1. Wenn `TriggerJsPrompt` ausgeführt wird, indem die Schaltfläche **JavaScript-Eingabeaufforderung auslösen** der Komponente ausgewählt wird, wird die JavaScript-`showPrompt`-Funktion, die in der Datei *wwwroot/examplejsinterop. js* bereitgestellt wird, aufgerufen.
1. Die `showPrompt` Funktion akzeptiert Benutzereingaben (den Namen des Benutzers), die HTML-codiert sind und an die Komponente zurückgegeben werden. Die Komponente speichert den Benutzernamen in einer lokalen Variablen, `name`.
1. Die in `name` gespeicherte Zeichenfolge wird in eine Begrüßungsnachricht eingebunden, die an eine JavaScript-Funktion, `displayWelcome`, übermittelt wird, die die Begrüßungsnachricht in ein Überschriften-Tag rendert.

## <a name="call-a-void-javascript-function"></a>JavaScript-Funktion "void" aufruft

JavaScript-Funktionen, die [void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) oder nicht [definiert](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) zurückgeben, werden mit `IJSRuntime.InvokeVoidAsync`aufgerufen.

## <a name="detect-when-a-opno-locblazor-app-is-prerendering"></a>Erkennen, wenn eine Blazor-App vorgestellt wird
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a>Verweise auf Elemente erfassen

Einige js-Interop-Szenarien erfordern Verweise auf HTML-Elemente. Beispielsweise kann eine UI-Bibliothek einen Element Verweis für die Initialisierung erfordern, oder Sie müssen möglicherweise Befehls ähnliche APIs für ein Element, z. b. `focus` oder `play`, aufzurufen.

Erfassen Sie mithilfe des folgenden Ansatzes Verweise auf HTML-Elemente in einer-Komponente:

* Fügen Sie dem HTML--Element ein `@ref`-Attribut hinzu.
* Definieren Sie ein Feld vom Typ `ElementReference`, dessen Name mit dem Wert des `@ref` Attributs übereinstimmt.

Das folgende Beispiel zeigt, wie Sie einen Verweis auf das `username` `<input>`-Element erfassen:

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> Verwenden Sie nur einen Element Verweis, um den Inhalt eines leeren Elements zu mutieren, das nicht mit Blazorinteragiert. Dieses Szenario ist nützlich, wenn eine Drittanbieter-API Inhalt für das-Element bereitstellt. Da Blazor nicht mit dem-Element interagiert, besteht keine Möglichkeit, einen Konflikt zwischen BlazorDarstellung des-Elements und des Dom zu haben.
>
> Im folgenden Beispiel ist es *gefährlich* , den Inhalt der unsortierten Liste (`ul`) zu mutieren, da Blazor mit dem Dom interagiert, um die Listenelemente dieses Elements (`<li>`) aufzufüllen:
>
> ```razor
> <ul ref="MyList">
>     @foreach (var item in Todos)
>     {
>         <li>@item.Text</li>
>     }
> </ul>
> ```
>
> Wenn das js-Interop den Inhalt des-Elements `MyList` und Blazor versucht, Unterschiede auf das Element anzuwenden, stimmen die Unterschiede nicht mit dem Dom.

Wenn .NET-Code betroffen ist, ist ein `ElementReference` ein undurchsichtiges handle. Das *einzige* , was Sie mit `ElementReference` tun können, ist die Übergabe an JavaScript-Code über js-Interop. Wenn Sie dies tun, empfängt der JavaScript-seitige Code eine `HTMLElement`-Instanz, die mit normalen DOM-APIs verwendet werden kann.

Der folgende Code definiert z. b. eine .net-Erweiterungsmethode, die das Festlegen des Fokus auf ein Element ermöglicht:

*examplejsinterop. js*:

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

Verwenden Sie `IJSRuntime.InvokeVoidAsync`, um eine JavaScript-Funktion aufzurufen, die keinen Wert zurückgibt. Mit dem folgenden Code wird der Fokus auf die username-Eingabe festgelegt, indem die vorherige JavaScript-Funktion mit dem erfassten `ElementReference`aufgerufen wird:

[!code-razor[](javascript-interop/samples_snapshot/component1.razor?highlight=1,3,11-12)]

Erstellen Sie eine statische Erweiterungsmethode, die die `IJSRuntime` Instanz empfängt, um eine Erweiterungsmethode zu verwenden:

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

Die `Focus`-Methode wird direkt für das-Objekt aufgerufen. Im folgenden Beispiel wird davon ausgegangen, dass die `Focus`-Methode über den `JsInteropClasses`-Namespace verfügbar ist:

[!code-razor[](javascript-interop/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> Die `username` Variable wird erst aufgefüllt, nachdem die Komponente gerendert wurde. Wenn eine nicht aufgefüllte `ElementReference` an den JavaScript-Code übermittelt wird, erhält der JavaScript-Code den Wert `null`. Um Element Verweise zu manipulieren, nachdem die Komponente das Rendering abgeschlossen hat (um den anfänglichen Fokus auf ein Element festzulegen), verwenden Sie die [onafterrenderasync-oder onafterrendering-Komponenten Lebenszyklus-Methoden](xref:blazor/lifecycle#after-component-render).

Wenn Sie mit generischen Typen arbeiten und einen Wert zurückgeben, verwenden Sie [valuetask\<t >](xref:System.Threading.Tasks.ValueTask`1):

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

`GenericMethod` wird direkt für das-Objekt mit einem-Typ aufgerufen. Im folgenden Beispiel wird davon ausgegangen, dass die `GenericMethod` im `JsInteropClasses`-Namespace verfügbar ist:

[!code-razor[](javascript-interop/samples_snapshot/component3.razor?highlight=17)]

## <a name="invoke-net-methods-from-javascript-functions"></a>Aufrufen von .NET-Methoden aus JavaScript-Funktionen

### <a name="static-net-method-call"></a>Statischer .net-Methodenaufrufe

Zum Aufrufen einer statischen .NET-Methode aus JavaScript verwenden Sie die Funktionen `DotNet.invokeMethod` oder `DotNet.invokeMethodAsync`. Übergeben Sie den Bezeichner der statischen Methode, die aufgerufen werden soll, den Namen der Assembly, die die Funktion enthält, und alle Argumente. Die asynchrone Version ist für die Unterstützung von Blazor Server-Szenarien bevorzugt. Um eine .NET-Methode aus JavaScript aufzurufen, muss die .NET-Methode öffentlich und statisch sein und über das `[JSInvokable]`-Attribut verfügen. Standardmäßig ist der Methoden Bezeichner der Methodenname, aber Sie können einen anderen Bezeichner mit dem `JSInvokableAttribute`-Konstruktor angeben. Das Aufrufen offener generischer Methoden wird derzeit nicht unterstützt.

Die Beispiel-app enthält C# eine-Methode, um ein Array von `int`s zurückzugeben. Das `JSInvokable`-Attribut wird auf die-Methode angewendet.

*Pages/jsinterop. Razor*:

```razor
<button type="button" class="btn btn-primary"
        onclick="exampleJsFunctions.returnArrayAsyncJs()">
    Trigger .NET static method ReturnArrayAsync
</button>

@code {
    [JSInvokable]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

JavaScript, das dem Client bereitgestellt C# wird, ruft die .NET-Methode auf

*wwwroot/examplejsinterop. js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

Untersuchen Sie die Konsolenausgabe in den Webentwickler Tools des Browsers, wenn Sie die Schaltfläche " **.net static-Methode für rückgabetyasync** " aufrufen auswählen.

Die Konsolenausgabe sieht wie folgt aus:

```console
Array(4) [ 1, 2, 3, 4 ]
```

Der vierte Arraywert wird per pushübertragung an das von `ReturnArrayAsync`zurückgegebene Array (`data.push(4);`) übermittelt.

### <a name="instance-method-call"></a>Instanzmethodenaufrufe

Sie können auch .net-Instanzmethoden von JavaScript aus abrufen. So rufen Sie eine .net-Instanzmethode aus JavaScript auf:

* Übergeben Sie die .net-Instanz an JavaScript, indem Sie Sie in einer `DotNetObjectReference` Instanz Umpacken. Die .net-Instanz wird als Verweis an JavaScript übermittelt.
* Rufen Sie mithilfe der Funktionen `invokeMethod` oder `invokeMethodAsync` .net-Instanzmethoden für die-Instanz auf. Die .net-Instanz kann auch als Argument beim Aufrufen anderer .NET-Methoden aus JavaScript übermittelt werden.

> [!NOTE]
> Die Beispiel-App protokolliert Nachrichten in der Client seitigen Konsole. Überprüfen Sie die Konsolenausgabe des Browsers in den Entwicklertools des Browsers auf die folgenden Beispiele, die von der Beispiel-App veranschaulicht werden.

Wenn die Schaltfläche **.net-Instanzmethode "hellohelper. SayHello" des Auslösers** aufgerufen wird, wird `ExampleJsInterop.CallHelloHelperSayHello` aufgerufen und übergibt einen Namen, `Blazor`, an die-Methode.

*Pages/jsinterop. Razor*:

```razor
<button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
    Trigger .NET instance method HelloHelper.SayHello
</button>

@code {
    public async Task TriggerNetInstanceMethod()
    {
        var exampleJsInterop = new ExampleJsInterop(JSRuntime);
        await exampleJsInterop.CallHelloHelperSayHello("Blazor");
    }
}
```

`CallHelloHelperSayHello` Ruft die JavaScript-Funktion `sayHello` mit einer neuen Instanz von `HelloHelper`auf.

*Jsinteropclasses/examplejsinterop. cs*:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

*wwwroot/examplejsinterop. js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

Der Name wird an `HelloHelper`-Konstruktor übergeben, der die `HelloHelper.Name`-Eigenschaft festlegt. Wenn die JavaScript-Funktion `sayHello` ausgeführt wird, gibt `HelloHelper.SayHello` die `Hello, {Name}!` Meldung zurück, die von der JavaScript-Funktion in die Konsole geschrieben wird.

*Jsinteropclasses/hellohelper. cs*:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

Konsolenausgabe in den Webentwickler Tools des Browsers:

```console
Hello, Blazor!
```

## <a name="share-interop-code-in-a-class-library"></a>Freigeben von Interop-Code in einer Klassenbibliothek

Der JS-Interop-Code kann in einer Klassenbibliothek enthalten sein, die es Ihnen ermöglicht, den Code in einem nuget-Paket freizugeben.

Die Klassenbibliothek behandelt das Einbetten von JavaScript-Ressourcen in der erstellten Assembly. Die JavaScript-Dateien werden in den Ordner " *wwwroot* " eingefügt. Die Tools kümmern sich um die Einbettung der Ressourcen, wenn die Bibliothek erstellt wird.

Auf das integrierte nuget-Paket wird in der Projektdatei der APP genauso verwiesen, wie auf ein beliebiges nuget-Paket verwiesen wird. Nachdem das Paket wieder hergestellt wurde, kann der app-Code JavaScript so aufruft, C#als wäre es.

Weitere Informationen finden Sie unter <xref:blazor/class-libraries>.

## <a name="harden-js-interop-calls"></a>Harden-js-Interop-Aufrufe

Das js-Interop kann aufgrund von Netzwerkfehlern fehlschlagen und sollte als unzuverlässig behandelt werden. Standardmäßig wird für eine Blazor Server-App nach einer Minute ein Timeout für JS-Interop-Aufrufe auf dem Server verwendet. Wenn eine APP einen aggressiveren Timeout (z. b. 10 Sekunden) tolerieren kann, legen Sie den Timeout mithilfe eines der folgenden Ansätze fest:

* Geben Sie in `Startup.ConfigureServices`Global das Timeout an:

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* Pro Aufruf im Komponenten Code kann ein einzelner Aufruf das Timeout angeben:

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

Weitere Informationen zur Ressourcenauslastung finden Sie unter <xref:security/blazor/server>.

## <a name="additional-resources"></a>Weitere Ressourcen

* [Interopcomponent. Razor-Beispiel (dotnet/aspnetcore-GitHub-Repository, 3,1-releasebranch)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
