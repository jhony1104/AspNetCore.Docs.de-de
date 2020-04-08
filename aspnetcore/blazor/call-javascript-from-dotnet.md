---
title: Aufrufen von JavaScript-Funktionen über .NET-Methoden in ASP.NET Core Blazor
author: guardrex
description: In diesem Artikel erfahren Sie, wie Sie JavaScript-Funktionen über .NET-Methoden in Blazor-Apps aufrufen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/19/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: 7a27b6f1be2ef296d5b2b2a4f566e0cdedbe6480
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/06/2020
ms.locfileid: "78647521"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-opno-locblazor"></a>Aufrufen von JavaScript-Funktionen über .NET-Methoden in ASP.NET Core Blazor

Von [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Eine Blazor-App kann JavaScript-Funktionen über .NET-Methoden und .NET-Methoden über JavaScript-Funktionen aufrufen. Diese Szenarios werden als *JavaScript-Interoperabilität* (*JS Interop*) bezeichnet.

In diesem Artikel wird das Aufrufen von JavaScript-Funktionen über .NET behandelt. Weitere Informationen zum Aufrufen von .NET-Methoden über JavaScript finden Sie unter <xref:blazor/call-dotnet-from-javascript>.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Verwenden Sie die `IJSRuntime`-Abstraktion, um JavaScript über .NET aufzurufen. Fügen Sie die `IJSRuntime`-Abstraktion in Ihre Komponente ein, um JS Interop-Aufrufe auszugeben. Die `InvokeAsync<T>`-Methode akzeptiert einen Bezeichner für die JavaScript-Funktion, die Sie aufrufen möchten, sowie eine beliebige Anzahl JSON-serialisierbarer Argumente. Der Funktionsbezeichner ist relativ zum globalen Bereich (`window`). Wenn Sie `window.someScope.someFunction` aufrufen möchten, lautet der Bezeichner `someScope.someFunction`. Die Funktion muss nicht registriert werden, bevor sie aufgerufen wird. Der Rückgabetyp `T` muss ebenfalls JSON-serialisierbar sein. `T` sollte mit dem .NET-Typ übereinstimmen, der dem zurückgegebenen JSON-Typ am ehesten entspricht.

Bei Blazor Server-Apps mit aktiviertem Prerendering ist das Aufrufen von JavaScript beim ersten Prerendering nicht möglich. JavaScript-Interoperabilitätsaufrufe müssen verzögert werden, bis die Verbindung mit dem Browser hergestellt ist. Weitere Informationen finden Sie im Abschnitt [Ermitteln des Prerenderings einer Blazor Server-App](#detect-when-a-blazor-server-app-is-prerendering).

Das folgende Beispiel basiert auf [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), einem auf JavaScript basierenden Decoder. Im Beispiel wird das Aufrufen einer JavaScript-Funktion über eine C#-Methode veranschaulicht. Die JavaScript-Funktion akzeptiert ein Bytearray von einer C#-Methode, decodiert das Array und gibt den Text zum Anzeigen an die Komponente zurück.

Geben Sie im `<head>`-Element von *wwwroot/index.html* (Blazor WebAssembly) oder *Pages/_Host.cshtml* (Blazor Server) eine JavaScript-Funktion an, die `TextDecoder` verwendet, um ein übermitteltes Array zu decodieren und den decodierten Wert zurückzugeben:

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

JavaScript-Code, z. B. der Code im vorherigen Beispiel, kann auch über eine JavaScript-Datei ( *.js*) mit einem Verweis auf die Skriptdatei geladen werden:

```html
<script src="exampleJsInterop.js"></script>
```

Die folgende Komponente führt folgende Aktionen aus:

* Sie ruft die JavaScript-Funktion `convertArray` mit `JSRuntime` auf, wenn auf eine Komponentenschaltfläche (**Convert Array** (Array konvertieren)) geklickt wird.
* Nachdem die JavaScript-Funktion aufgerufen wurde, wird das übergebene Array in eine Zeichenfolge konvertiert. Die Zeichenfolge wird zur Anzeige an die Komponente zurückgegeben.

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a>IJSRuntime

Verwenden Sie einen der folgenden Ansätze, um die `IJSRuntime`-Abstraktion zu verwenden:

* Fügen Sie die `IJSRuntime`-Abstraktion in die Razor-Komponente ( *.razor*) ein:

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  Geben Sie die JavaScript-Funktion `handleTickerChanged` im `<head>`-Element von *wwwroot/index.html* (Blazor WebAssembly) oder *Pages/_Host.cshtml* (Blazor Server) an. Die Funktion wird mit `IJSRuntime.InvokeVoidAsync` aufgerufen und gibt keinen Wert zurück:

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* Fügen Sie die `IJSRuntime`-Abstraktion in eine Klasse ein ( *.cs*):

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  Geben Sie die JavaScript-Funktion `handleTickerChanged` im `<head>`-Element von *wwwroot/index.html* (Blazor WebAssembly) oder *Pages/_Host.cshtml* (Blazor Server) an. Die Funktion wird mit `JSRuntime.InvokeAsync` aufgerufen und gibt einen Wert zurück:

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* Verwenden Sie das `[Inject]`-Attribut für die dynamische Inhaltsgenerierung mit [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic):

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

In der clientseitigen Beispiel-App zu diesem Artikel stehen der App zwei JavaScript-Funktionen zur Verfügung, die mit dem DOM (Dokumentobjektmodell) interagieren, um Benutzereingaben zu empfangen und eine Begrüßungsnachricht anzuzeigen:

* `showPrompt`: Erzeugt eine Eingabeaufforderung zum Akzeptieren der Benutzereingaben (der Name des Benutzers) und gibt den Namen an den Aufrufer zurück.
* `displayWelcome`: Weist einem DOM-Objekt eine Begrüßungsnachricht vom Aufrufer mit dem `id`-Wert `welcome` zu.

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

Platzieren Sie das Tag `<script>`, das auf die JavaScript-Datei in der Datei *wwwroot/index.html* (Blazor WebAssembly) oder in der Datei *Pages/_Host.cshtml* (Blazor Server) verweist.

*wwwroot/index.html* (Blazor WebAssembly):

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

*Pages/_Host.cshtml* (Blazor Server):

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

Platzieren Sie kein `<script>`-Tag in einer Komponentendatei, weil das `<script>`-Tag nicht dynamisch aktualisiert werden kann.

Die Interoperabilität von .NET-Methoden mit den JavaScript-Funktionen in der Datei *exampleJsInterop.js* erfolgt, indem `IJSRuntime.InvokeAsync<T>` aufgerufen wird.

Die `IJSRuntime`-Abstraktion ist asynchron, um Blazor Server-Szenarios zu ermöglichen. Wenn es sich bei der App um eine Blazor WebAssembly-App handelt, sollten Sie die JavaScript-Funktion synchron aufrufen, eine Typumwandlung nach unten in `IJSInProcessRuntime` durchführen und stattdessen `Invoke<T>` aufrufen. Es wird empfohlen, für die meisten JS Interop-Bibliotheken asynchrone APIs zu verwenden, um sicherzustellen, dass die Bibliotheken in allen Szenarios verfügbar sind.

Die Beispiel-App enthält eine Komponente zur Veranschaulichung der JavaScript-Interoperabilität. Die Komponente:

* empfängt Benutzereingaben über eine JavaScript-Eingabeaufforderung.
* gibt den Text zur Verarbeitung an die Komponente zurück.
* ruft eine zweite JavaScript-Funktion auf, die mit dem DOM interagiert, um eine Begrüßungsnachricht anzuzeigen.

*Pages/JSInterop.razor*:

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
        var name = await JSRuntime.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");

        await JSRuntime.InvokeVoidAsync(
                "exampleJsFunctions.displayWelcome",
                $"Hello {name}! Welcome to Blazor!");
    }
}
```

1. Wenn `TriggerJsPrompt` ausgeführt wird, indem auf die Schaltfläche **Trigger JavaScript Prompt** (JavaScript-Eingabeaufforderung auslösen) der Komponente geklickt wird, wird die JavaScript-Funktion `showPrompt` aufgerufen, die in der Datei *wwwroot/exampleJsInterop.js* angegeben ist.
1. Die `showPrompt`-Funktion akzeptiert Benutzereingaben (den Namen des Benutzers), die HTML-codiert und an die Komponente zurückgegeben werden. Die Komponente speichert den Namen des Benutzers in der lokalen Variable `name`.
1. Die in `name` gespeicherte Zeichenfolge wird in eine Begrüßungsnachricht eingebunden, die an die JavaScript-Funktion `displayWelcome` übergeben wird, die die Begrüßungsnachricht in ein Überschriftentag rendert.

## <a name="call-a-void-javascript-function"></a>Aufrufen der JavaScript-Funktion „void“

JavaScript-Funktionen, die [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) oder [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) zurückgeben, werden mit `IJSRuntime.InvokeVoidAsync` aufgerufen.

## <a name="detect-when-a-opno-locblazor-server-app-is-prerendering"></a>Ermitteln des Prerenderings einer Blazor Server-App
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a>Erfassen von Verweisen auf Elemente

Einige JS Interop-Szenarios erfordern Verweise auf HTML-Elemente. Beispielsweise erfordert eine Benutzeroberflächenbibliothek möglicherweise einen Elementverweis für die Initialisierung, oder Sie müssen befehlsähnliche APIs für ein Element aufrufen, z. B. `focus` oder `play`.

Erfassen Sie mithilfe des folgenden Ansatzes Verweise auf HTML-Elemente in einer Komponente:

* Fügen Sie ein `@ref`-Attribut zum HTML-Element hinzu.
* Definieren Sie ein Feld vom Typ `ElementReference`, dessen Name mit dem Wert des `@ref`-Attributs übereinstimmt.

Im folgenden Beispiel wird das Erfassen eines Verweises auf das `username` `<input>`-Element veranschaulicht:

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> Verwenden Sie Elementverweise nur, um die Inhalte eines leeren Elements zu ändern, das nicht mit Blazor interagiert. Dieses Szenario ist nützlich, wenn eine Drittanbieter-API Inhalt für das Element bereitstellt. Da Blazor nicht mit dem Element interagiert, kann kein Konflikt zwischen der Darstellung von Blazor des Elements und dem DOM auftreten.
>
> Im folgenden Beispiel ist es *gefährlich*, die Inhalte der unsortierten Liste (`ul`) zu ändern, weil Blazor mit dem DOM interagiert, um die Listenelemente (`<li>`) dieses Elements aufzufüllen:
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
> Wenn die Inhalte des Elements `MyList` durch JS Interop geändert werden und Blazor versucht, diffs-Algorithmen auf das Element anzuwenden, entsprechen diese nicht dem DOM.

Bei .NET-Code handelt es sich bei `ElementReference` um einen nicht transparenten Handle. Das *Einzige*, was Sie mit `ElementReference` tun können, ist, es per JS Interop an JavaScript-Code zu übergeben. Dadurch empfängt der JavaScript-seitige Code eine `HTMLElement`-Instanz, die er mit normalen DOM-APIs verwenden kann.

Im folgenden Code wird beispielsweise eine .NET-Erweiterungsmethode definiert, die das Festlegen des Fokus auf ein Element ermöglicht:

*exampleJsInterop.js*:

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

Verwenden Sie `IJSRuntime.InvokeVoidAsync`, um eine JavaScript-Funktion aufzurufen, die keinen Wert zurückgibt. Mit dem folgenden Code wird der Fokus auf die Eingabe des Benutzernamens festgelegt, indem die vorherige JavaScript-Funktion mit dem erfassten `ElementReference`-Wert aufgerufen wird:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

Erstellen Sie eine statische Erweiterungsmethode, die die `IJSRuntime`-Instanz empfängt, um eine Erweiterungsmethode zu verwenden:

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

Die `Focus`-Methode wird direkt für das Objekt aufgerufen. Im folgenden Beispiel wird davon ausgegangen, dass die `Focus`-Methode vom `JsInteropClasses`-Namespace verfügbar ist:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> Die `username`-Variable wird erst aufgefüllt, nachdem die Komponente gerendert wurde. Wenn ein nicht aufgefüllter `ElementReference`-Wert an JavaScript-Code übergeben wird, empfängt der JavaScript-Code den Wert `null`. Verwenden Sie die [Komponentenlebenszyklusmethoden OnAfterRenderAsync oder OnAfterRender](xref:blazor/lifecycle#after-component-render), um Elementverweise zu ändern, nachdem die Komponente gerendert wurde (um den anfänglichen Fokus für ein Element festzulegen).

Verwenden Sie [ValueTask\<T>](xref:System.Threading.Tasks.ValueTask`1), wenn Sie mit generischen Typen arbeiten und einen Wert zurückgeben:

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

`GenericMethod` wird direkt für das Objekt mit einem Typ aufgerufen. Im folgenden Beispiel wird davon ausgegangen, dass die `GenericMethod`-Methode vom `JsInteropClasses`-Namespace verfügbar ist:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a>Komponentenübergreifende Verweiselemente

`ElementReference` ist nur in der `OnAfterRender`-Methode einer Komponente garantiert gültig (und ein Elementverweis ist ein `struct`), daher kann ein Elementverweis nicht zwischen Komponenten übergeben werden.

Damit eine übergeordnete Komponente einen Elementverweis anderen Komponenten zur Verfügung stellen kann, kann die übergeordnete Komponente:

* zulassen, dass untergeordnete Komponenten Rückrufe registrieren.
* die registrierten Rückrufe während des `OnAfterRender`-Ereignisses mit dem übergebenen Elementverweis aufrufen. Dieser Ansatz ermöglicht untergeordneten Komponenten indirekt die Interaktion mit dem Elementverweis des übergeordneten Elements.

Im folgenden Blazor WebAssembly-Beispiel wird der Ansatz veranschaulicht.

In `<head>` der Datei *wwwroot/index.html*:

```html
<style>
    .red { color: red }
</style>
```

In `<body>` der Datei *wwwroot/index.html*:

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

*Pages/Index.razor* (übergeordnete Komponente):

```razor
@page "/"

<h1 @ref="_title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

*Pages/Index.razor.cs*:

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace BlazorSample.Pages
{
    public partial class Index : 
        ComponentBase, IObservable<ElementReference>, IDisposable
    {
        private bool _disposing;
        private IList<IObserver<ElementReference>> _subscriptions = 
            new List<IObserver<ElementReference>>();
        private ElementReference _title;

        protected override void OnAfterRender(bool firstRender)
        {
            base.OnAfterRender(firstRender);

            foreach (var subscription in _subscriptions)
            {
                try
                {
                    subscription.OnNext(_title);
                }
                catch (Exception)
                {
                    throw;
                }
            }
        }

        public void Dispose()
        {
            _disposing = true;

            foreach (var subscription in _subscriptions)
            {
                try
                {
                    subscription.OnCompleted();
                }
                catch (Exception)
                {
                }
            }

            _subscriptions.Clear();
        }

        public IDisposable Subscribe(IObserver<ElementReference> observer)
        {
            if (_disposing)
            {
                throw new InvalidOperationException("Parent being disposed");
            }

            _subscriptions.Add(observer);

            return new Subscription(observer, this);
        }

        private class Subscription : IDisposable
        {
            public Subscription(IObserver<ElementReference> observer, Index self)
            {
                Observer = observer;
                Self = self;
            }

            public IObserver<ElementReference> Observer { get; }
            public Index Self { get; }

            public void Dispose()
            {
                Self._subscriptions.Remove(Observer);
            }
        }
    }
}
```

*Shared/SurveyPrompt.razor* (untergeordnete Komponente):

```razor
@inject IJSRuntime JS

<div class="alert alert-secondary mt-4" role="alert">
    <span class="oi oi-pencil mr-2" aria-hidden="true"></span>
    <strong>@Title</strong>

    <span class="text-nowrap">
        Please take our
        <a target="_blank" class="font-weight-bold" 
            href="https://go.microsoft.com/fwlink/?linkid=2109206">brief survey</a>
    </span>
    and tell us what you think.
</div>

@code {
    [Parameter]
    public string Title { get; set; }
}
```

*Shared/SurveyPrompt.razor.cs*:

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace BlazorSample.Shared
{
    public partial class SurveyPrompt : 
        ComponentBase, IObserver<ElementReference>, IDisposable
    {
        private IDisposable _subscription = null;

        [Parameter]
        public IObservable<ElementReference> Parent { get; set; }

        protected override void OnParametersSet()
        {
            base.OnParametersSet();

            if (_subscription != null)
            {
                _subscription.Dispose();
            }

            _subscription = Parent.Subscribe(this);
        }

        public void OnCompleted()
        {
            _subscription = null;
        }

        public void OnError(Exception error)
        {
            _subscription = null;
        }

        public void OnNext(ElementReference value)
        {
            JS.InvokeAsync<object>(
                "setElementClass", new object[] { value, "red" });
        }

        public void Dispose()
        {
            _subscription?.Dispose();
        }
    }
}
```

## <a name="harden-js-interop-calls"></a>Festschreiben von JS Interop-Aufrufen

JS Interop kann aufgrund von Netzwerkfehlern fehlschlagen und sollte als unzuverlässig behandelt werden. Standardmäßig weisen Blazor Server-Apps einen Timeout für JS Interop-Aufrufe an den Server nach einer Minute auf. Wenn eine App ein engeres Timeout tolerieren kann, z. B. 10 Sekunden, legen Sie das Timeout mit einem der folgenden Ansätze durch:

* Legen Sie das Timeout global in `Startup.ConfigureServices` fest:

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* Pro Aufruf im Komponentencode kann ein einzelner Aufruf das Timeout festlegen:

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

Weitere Informationen zur Ressourcenauslastung finden Sie unter <xref:security/blazor/server>.

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:blazor/call-dotnet-from-javascript>
* [InteropComponent.razor-Beispiel (dotnet/AspNetCore-GitHub-Repository, Releasebranch 3.1)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [Ausführen umfangreicher Datenübertragungen in Blazor Server-Apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
