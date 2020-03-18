---
title: Aufrufen von .NET-Methoden von JavaScript-Funktionen in ASP.NET Core Blazor
author: guardrex
description: In diesem Artikel erfahren Sie, wie Sie .NET-Methoden von JavaScript-Funktionen in Blazor-Apps aufrufen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/19/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: f4964341e261c65269eedafbbd6e676c1054f427
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78647407"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-opno-locblazor"></a>Aufrufen von .NET-Methoden von JavaScript-Funktionen in ASP.NET Core Blazor

Von [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Eine Blazor-App kann JavaScript-Funktionen über .NET-Methoden und .NET-Methoden über JavaScript-Funktionen aufrufen. Diese Szenarios werden als *JavaScript-Interoperabilität* (*JS Interop*) bezeichnet.

In diesem Artikel wird das Aufrufen von .NET-Methoden über JavaScript behandelt. Informationen zum Aufrufen von JavaScript-Funktionen über .NET finden Sie unter <xref:blazor/call-javascript-from-dotnet>.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="static-net-method-call"></a>Statischer .NET-Methodenaufruf

Um eine statische .NET-Methode über JavaScript aufzurufen, verwenden Sie die Funktionen `DotNet.invokeMethod` oder `DotNet.invokeMethodAsync`. Übergeben Sie den Bezeichner der statischen Methode, die Sie aufrufen möchten, den Namen der Assembly, die die Funktion enthält, und alle Argumente. Die asynchrone Version wird bevorzugt, um Blazor Server-Szenarien zu unterstützen. Die .NET-Methode muss öffentlich und statisch sein sowie das Attribut `[JSInvokable]` aufweisen. Das Aufrufen offener generischer Methoden wird derzeit nicht unterstützt.

Die Beispiel-App enthält eine C#-Methode zur Rückgabe eines `int`-Arrays. Das `JSInvokable`-Attribut wird auf die Methode angewendet.

*Pages/JsInterop.razor*:

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

Das dem Client zugestellte JavaScript ruft die C# .NET-Methode auf.

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

Wenn die Schaltfläche **Statische .NET-Methode ReturnArrayAsync auslösen** ausgewählt wird, untersuchen Sie die Konsolenausgabe in den Webentwicklertools des Browsers.

Die Konsolenausgabe lautet:

```console
Array(4) [ 1, 2, 3, 4 ]
```

Der vierte Arraywert wird in das von `ReturnArrayAsync` zurückgegebene Array (`data.push(4);`) gepusht.

Standardmäßig ist der Methodenbezeichner der Methodenname, aber Sie können mit dem Konstruktor `JSInvokableAttribute` einen anderen Bezeichner angeben:

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

In der clientseitigen JavaScript-Datei:

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('BlazorSample', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

## <a name="instance-method-call"></a>Aufruf der Instanzmethode

Sie können auch .NET-Instanzmethoden von JavaScript aus aufrufen. So rufen Sie .NET-Instanzmethoden von JavaScript aus auf

* Übergeben Sie die .NET-Instanz durch Verweis auf JavaScript:
  * Führen Sie einen statischen Aufruf an `DotNetObjectReference.Create` durch.
  * Umschließen Sie die Instanz mit einer `DotNetObjectReference`-Instanz, und rufen Sie `Create` für die `DotNetObjectReference`-Instanz auf. Entsorgen Sie `DotNetObjectReference`-Objekte (ein Beispiel folgt später in diesem Abschnitt).
* Rufen Sie .NET-Instanzmethoden für die Instanz mit den Funktionen `invokeMethod` oder `invokeMethodAsync` auf. Die .NET-Instanz kann auch als Argument übergeben werden, wenn andere .NET-Methoden von JavaScript aus aufgerufen werden.

> [!NOTE]
> Die Beispiel-App protokolliert Nachrichten an die clientseitige Konsole. Für die folgenden Beispiele, die durch die Beispiel-App veranschaulicht werden, untersuchen Sie die Konsolenausgabe des Browsers in den Entwicklertools des Browsers.

Wenn die Schaltfläche **.NET-Instanzmethode HelloHelper.SayHello auslösen** ausgewählt wird, wird `ExampleJsInterop.CallHelloHelperSayHello` aufgerufen und ein Name, `Blazor`, an die Methode übergeben.

*Pages/JsInterop.razor*:

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

`CallHelloHelperSayHello` ruft die JavaScript-Funktion `sayHello` mit einer neuen Instanz von `HelloHelper` auf.

*JsInteropClasses/ExampleJsInterop.cs*:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

Der Name wird an den Konstruktor von `HelloHelper` übergeben, der die Eigenschaft `HelloHelper.Name` festlegt. Wenn die JavaScript-Funktion `sayHello` ausgeführt wird, gibt `HelloHelper.SayHello` die Nachricht `Hello, {Name}!` zurück, die von der JavaScript-Funktion in die Konsole geschrieben wird.

*JsInteropClasses/HelloHelper.cs*:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

Konsolenausgabe in den Webentwicklertools des Browsers:

```console
Hello, Blazor!
```

Um eine Arbeitsspeicherverlust zu vermeiden und die automatische Speicherbereinigung für eine Komponente zu ermöglichen, die ein `DotNetObjectReference` erstellt, entsorgen Sie das Objekt in der Klasse, die die `DotNetObjectReference`-Instanz erstellt hat:

```csharp
public class ExampleJsInterop : IDisposable
{
    private readonly IJSRuntime _jsRuntime;
    private DotNetObjectReference<HelloHelper> _objRef;

    public ExampleJsInterop(IJSRuntime jsRuntime)
    {
        _jsRuntime = jsRuntime;
    }

    public ValueTask<string> CallHelloHelperSayHello(string name)
    {
        _objRef = DotNetObjectReference.Create(new HelloHelper(name));

        return _jsRuntime.InvokeAsync<string>(
            "exampleJsFunctions.sayHello",
            _objRef);
    }

    public void Dispose()
    {
        _objRef?.Dispose();
    }
}
```
  
Das vorhergehende Muster, das in der Klasse `ExampleJsInterop` gezeigt wird, kann auch in einer Komponente implementiert werden:
  
```razor
@page "/JSInteropComponent"
@using BlazorSample.JsInteropClasses
@implements IDisposable
@inject IJSRuntime JSRuntime

<h1>JavaScript Interop</h1>

<button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
    Trigger .NET instance method HelloHelper.SayHello
</button>

@code {
    private DotNetObjectReference<HelloHelper> _objRef;

    public async Task TriggerNetInstanceMethod()
    {
        _objRef = DotNetObjectReference.Create(new HelloHelper("Blazor"));

        await JSRuntime.InvokeAsync<string>(
            "exampleJsFunctions.sayHello",
            _objRef);
    }

    public void Dispose()
    {
        _objRef?.Dispose();
    }
}
```

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:blazor/call-javascript-from-dotnet>
* [InteropComponent.razor-Beispiel (dotnet/AspNetCore-GitHub-Repository, Releasebranch 3.1)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [Ausführen umfangreicher Datenübertragungen in Blazor Server-Apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
