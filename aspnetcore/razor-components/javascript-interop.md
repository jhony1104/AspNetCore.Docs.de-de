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
# <a name="razor-components-javascript-interop"></a><span data-ttu-id="24a47-103">Razor-Komponenten-JavaScript-interop</span><span class="sxs-lookup"><span data-stu-id="24a47-103">Razor Components JavaScript interop</span></span>

<span data-ttu-id="24a47-104">Durch [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="24a47-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="24a47-105">Eine app für Razor-Komponenten kann JavaScript-Funktionen von .NET und .NET Aufrufen der Methoden von JavaScript-Code.</span><span class="sxs-lookup"><span data-stu-id="24a47-105">A Razor Components app can invoke JavaScript functions from .NET and .NET methods from JavaScript code.</span></span>

## <a name="invoke-javascript-functions-from-net-methods"></a><span data-ttu-id="24a47-106">JavaScript-Funktionen von .NET Methoden aufrufen</span><span class="sxs-lookup"><span data-stu-id="24a47-106">Invoke JavaScript functions from .NET methods</span></span>

<span data-ttu-id="24a47-107">Es gibt Zeiten, in denen .NET Code erforderlich, um eine JavaScript-Funktion aufrufen.</span><span class="sxs-lookup"><span data-stu-id="24a47-107">There are times when .NET code is required to call a JavaScript function.</span></span> <span data-ttu-id="24a47-108">Beispielsweise kann ein JavaScript-Aufruf Browserfunktionen oder Funktionen aus einer JavaScript-Bibliothek für die app verfügbar machen.</span><span class="sxs-lookup"><span data-stu-id="24a47-108">For example, a JavaScript call can expose browser capabilities or functionality from a JavaScript library to the app.</span></span>

<span data-ttu-id="24a47-109">Verwenden Sie zum Aufrufen von .NET in JavaScript die `IJSRuntime` Abstraktion.</span><span class="sxs-lookup"><span data-stu-id="24a47-109">To call into JavaScript from .NET, use the `IJSRuntime` abstraction.</span></span> <span data-ttu-id="24a47-110">Die `InvokeAsync<T>` Methode nimmt einen Bezeichner für die JavaScript-Funktion, die Sie zusammen mit der eine beliebige Anzahl von JSON-serialisierbare Argumenten aufrufen möchten.</span><span class="sxs-lookup"><span data-stu-id="24a47-110">The `InvokeAsync<T>` method takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="24a47-111">Der Funktionsbezeichner ist relativ zu den globalen Bereich (`window`).</span><span class="sxs-lookup"><span data-stu-id="24a47-111">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="24a47-112">Wenn Sie aufrufen möchten `window.someScope.someFunction`, der Bezeichner ist `someScope.someFunction`.</span><span class="sxs-lookup"><span data-stu-id="24a47-112">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="24a47-113">Es ist nicht erforderlich, die die Funktion zu registrieren, bevor sie aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="24a47-113">There's no need to register the function before it's called.</span></span> <span data-ttu-id="24a47-114">Der Rückgabetyp `T` muss auch JSON serialisierbar sein.</span><span class="sxs-lookup"><span data-stu-id="24a47-114">The return type `T` must also be JSON serializable.</span></span>

<span data-ttu-id="24a47-115">Für serverseitige Komponenten von ASP.NET Core Razor-apps:</span><span class="sxs-lookup"><span data-stu-id="24a47-115">For server-side ASP.NET Core Razor Components apps:</span></span>

* <span data-ttu-id="24a47-116">Die serverseitige app werden mehrere benutzeranforderungen verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="24a47-116">Multiple user requests are processed by the server-side app.</span></span> <span data-ttu-id="24a47-117">Rufen Sie nicht `JSRuntime.Current` in einer Komponente zum Aufrufen von JavaScript-Funktionen.</span><span class="sxs-lookup"><span data-stu-id="24a47-117">Don't call `JSRuntime.Current` in a component to invoke JavaScript functions.</span></span>
* <span data-ttu-id="24a47-118">Einfügen der `IJSRuntime` Abstraktion und verwenden das eingefügte Objekt, das JavaScript-interop-Aufrufe ausgeben.</span><span class="sxs-lookup"><span data-stu-id="24a47-118">Inject the `IJSRuntime` abstraction and use the injected object to issue JavaScript interop calls.</span></span>

<span data-ttu-id="24a47-119">Das folgende Beispiel basiert auf [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), einen experimentellen JavaScript-basierten Decoder.</span><span class="sxs-lookup"><span data-stu-id="24a47-119">The following example is based on [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), an experimental JavaScript-based decoder.</span></span> <span data-ttu-id="24a47-120">Im Beispiel wird veranschaulicht, wie zum Aufrufen einer JavaScript-Funktion aus einem C# Methode.</span><span class="sxs-lookup"><span data-stu-id="24a47-120">The example demonstrates how to invoke a JavaScript function from a C# method.</span></span> <span data-ttu-id="24a47-121">Die JavaScript-Funktion akzeptiert ein Bytearray aus einer C# Methode, die das Array decodiert und gibt den Text an die Komponente für die Anzeige zurück.</span><span class="sxs-lookup"><span data-stu-id="24a47-121">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="24a47-122">In der `<head>` Element *wwwroot/Index.HTML*, geben Sie eine Funktion, verwendet `TextDecoder` zu einem übergebenen Array zu decodierende:</span><span class="sxs-lookup"><span data-stu-id="24a47-122">Inside the `<head>` element of *wwwroot/index.html*, provide a function that uses `TextDecoder` to decode a passed array:</span></span>

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

<span data-ttu-id="24a47-123">JavaScript-Code, z. B. den Code im vorherigen Beispiel kann auch aus einer JavaScript-Datei geladen werden (*js*) mit einem Verweis auf die Skriptdatei in die *wwwroot/Index.HTML* Datei:</span><span class="sxs-lookup"><span data-stu-id="24a47-123">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (*.js*) with a reference to the script file in the *wwwroot/index.html* file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="24a47-124">Die folgende Komponente:</span><span class="sxs-lookup"><span data-stu-id="24a47-124">The following component:</span></span>

* <span data-ttu-id="24a47-125">Ruft die `ConvertArray` JavaScript-Funktion verwendet `JsRuntime` beim Klicken auf die Schaltfläche eine Komponente (**Array konvertieren**) ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="24a47-125">Invokes the `ConvertArray` JavaScript function using `JsRuntime` when a component button (**Convert Array**) is selected.</span></span>
* <span data-ttu-id="24a47-126">Nachdem die JavaScript-Funktion aufgerufen wurde, wird das übergebene Array in eine Zeichenfolge konvertiert.</span><span class="sxs-lookup"><span data-stu-id="24a47-126">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="24a47-127">Die Zeichenfolge wird an die Komponente für die Anzeige zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="24a47-127">The string is returned to the component for display.</span></span>

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

<span data-ttu-id="24a47-128">Verwenden der `IJSRuntime` Abstraktion, die einen der folgenden Ansätze verwenden:</span><span class="sxs-lookup"><span data-stu-id="24a47-128">To use the `IJSRuntime` abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="24a47-129">Einfügen der `IJSRuntime` Abstraktion in die Razor-Datei (*.razor*, *.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="24a47-129">Inject the `IJSRuntime` abstraction into the Razor file (*.razor*, *.cshtml*):</span></span>

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

* <span data-ttu-id="24a47-130">Einfügen der `IJSRuntime` Abstraktion in einer Klasse (*cs*):</span><span class="sxs-lookup"><span data-stu-id="24a47-130">Inject the `IJSRuntime` abstraction into a class (*.cs*):</span></span>

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

* <span data-ttu-id="24a47-131">Für das dynamische Generieren von Inhalten mit `BuildRenderTree`, verwenden Sie die `[Inject]` Attribut:</span><span class="sxs-lookup"><span data-stu-id="24a47-131">For dynamic content generation with `BuildRenderTree`, use the `[Inject]` attribute:</span></span>

  ```csharp
  [Inject] IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="24a47-132">In der Client-Side-Beispiel-app, die in diesem Thema begleitet, werden zwei JavaScript-Funktionen für die clientseitige Anwendung die Interaktion mit dem DOM zum Empfangen von Benutzereingaben und Anzeigen von eine Begrüßungsnachricht an verfügbar:</span><span class="sxs-lookup"><span data-stu-id="24a47-132">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the client-side app that interact with the DOM to receive user input and display a welcome message:</span></span>

* `showPrompt` <span data-ttu-id="24a47-133">&ndash; Erzeugt eine Aufforderung zum Akzeptieren der Benutzereingabe (Name des Benutzers), und gibt den Namen an den Aufrufer zurück.</span><span class="sxs-lookup"><span data-stu-id="24a47-133">&ndash; Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* `displayWelcome` <span data-ttu-id="24a47-134">&ndash; Weist eine Begrüßungsnachricht an vom Aufrufer ein DOM-Objekt mit einer `id` von `welcome`.</span><span class="sxs-lookup"><span data-stu-id="24a47-134">&ndash; Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="24a47-135">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="24a47-135">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="24a47-136">Ort der `<script>` Tag, das in der JavaScript-Datei verweist auf die *wwwroot/Index.HTML* Datei:</span><span class="sxs-lookup"><span data-stu-id="24a47-136">Place the `<script>` tag that references the JavaScript file in the *wwwroot/index.html* file:</span></span>

[!code-html[](./common/samples/3.x/BlazorSample/wwwroot/index.html?highlight=15)]

<span data-ttu-id="24a47-137">Platzieren Sie keine `<script>` tag in einer Komponentendatei, da die `<script>` Tag kann nicht dynamisch aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="24a47-137">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="24a47-138">.NET Methoden Interop mit dem JavaScript-Funktionen in der *exampleJsInterop.js* Datei durch Aufrufen von `IJSRuntime.InvokeAsync<T>`.</span><span class="sxs-lookup"><span data-stu-id="24a47-138">.NET methods interop with the JavaScript functions in the *exampleJsInterop.js* file by calling `IJSRuntime.InvokeAsync<T>`.</span></span>

<span data-ttu-id="24a47-139">Die `IJSRuntime` Abstraktion ist asynchron, um die serverseitigen Szenarios zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="24a47-139">The `IJSRuntime` abstraction is asynchronous to allow for server-side scenarios.</span></span> <span data-ttu-id="24a47-140">Wenn die app ausgeführt, die clientseitige wird und eine JavaScript-Funktion synchron aufgerufen werden soll, Typumwandlung `IJSInProcessRuntime` , und rufen Sie `Invoke<T>` stattdessen.</span><span class="sxs-lookup"><span data-stu-id="24a47-140">If the app runs client-side and you want to invoke a JavaScript function synchronously, downcast to `IJSInProcessRuntime` and call `Invoke<T>` instead.</span></span> <span data-ttu-id="24a47-141">Es wird empfohlen, dass die meisten Interop-JavaScript-Bibliotheken die asynchronen APIs verwenden, um sicherzustellen, dass die Bibliotheken in allen Szenarien, die clientseitige oder serverseitige verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="24a47-141">We recommend that most JavaScript interop libraries use the async APIs to ensure that the libraries are available in all scenarios, client-side or server-side.</span></span>

<span data-ttu-id="24a47-142">Die Beispiel-app enthält eine Komponente, um JavaScript-Interop demonstrieren.</span><span class="sxs-lookup"><span data-stu-id="24a47-142">The sample app includes a component to demonstrate JavaScript interop.</span></span> <span data-ttu-id="24a47-143">Die Komponente:</span><span class="sxs-lookup"><span data-stu-id="24a47-143">The component:</span></span>

* <span data-ttu-id="24a47-144">Erhält Benutzereingaben über eine JavaScript-Eingabeaufforderung.</span><span class="sxs-lookup"><span data-stu-id="24a47-144">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="24a47-145">Der Text wird an die Komponente für die Verarbeitung zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="24a47-145">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="24a47-146">Ruft eine zweite JavaScript-Funktion, die mit dem DOM zum Anzeigen einer Begrüßungsnachricht interagiert.</span><span class="sxs-lookup"><span data-stu-id="24a47-146">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="24a47-147">*Pages/JSInterop.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="24a47-147">*Pages/JSInterop.cshtml*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.cshtml?name=snippet_JSInterop1&highlight=3,19-21,23-25)]

1. <span data-ttu-id="24a47-148">Wenn `TriggerJsPrompt` durch Auswahl der Komponente ausgeführt wird **Trigger JavaScript Eingabeaufforderung** Schaltfläche den JavaScript-Code `showPrompt` Funktion aus der *wwwroot/exampleJsInterop.js* Datei ist wird aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="24a47-148">When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the JavaScript `showPrompt` function provided in the *wwwroot/exampleJsInterop.js* file is called.</span></span>
1. <span data-ttu-id="24a47-149">Die `showPrompt` -Funktion akzeptiert Benutzereingaben (Name des Benutzers), die HTML-codiert und an die Komponente zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="24a47-149">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="24a47-150">Die Komponente speichert den Namen des Benutzers in einer lokalen Variablen, `name`.</span><span class="sxs-lookup"><span data-stu-id="24a47-150">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="24a47-151">Die Zeichenfolge, die in gespeicherten `name` wird in eine Begrüßungsnachricht an, die eine JavaScript-Funktion übergeben wird, integriert `displayWelcome`, die Willkommensnachricht in der Überschrift-Tag gerendert.</span><span class="sxs-lookup"><span data-stu-id="24a47-151">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="capture-references-to-elements"></a><span data-ttu-id="24a47-152">Erfassen Sie Verweise auf Elemente</span><span class="sxs-lookup"><span data-stu-id="24a47-152">Capture references to elements</span></span>

<span data-ttu-id="24a47-153">Einige [JavaScript-Interop](xref:razor-components/javascript-interop) Szenarien müssen Verweise auf HTML-Elemente.</span><span class="sxs-lookup"><span data-stu-id="24a47-153">Some [JavaScript interop](xref:razor-components/javascript-interop) scenarios require references to HTML elements.</span></span> <span data-ttu-id="24a47-154">Beispielsweise kann eine UI-Bibliothek ein Elementverweises benötigen, für die Initialisierung, oder müssen Sie möglicherweise Befehl-ähnliche APIs aufrufen auf ein Element, z. B. `focus` oder `play`.</span><span class="sxs-lookup"><span data-stu-id="24a47-154">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="24a47-155">Sie können Verweise auf HTML-Elemente in einer Komponente erfassen, durch das Hinzufügen eine `ref` -Attribut auf das HTML-Element und das anschließende definieren ein Feld vom Typ `ElementRef` , deren Name entspricht dem Wert der `ref` Attribut.</span><span class="sxs-lookup"><span data-stu-id="24a47-155">You can capture references to HTML elements in a component by adding a `ref` attribute to the HTML element and then defining a field of type `ElementRef` whose name matches the value of the `ref` attribute.</span></span>

<span data-ttu-id="24a47-156">Das folgende Beispiel zeigt einen Verweis auf das Eingabeelement Benutzernamen erfassen:</span><span class="sxs-lookup"><span data-stu-id="24a47-156">The following example shows capturing a reference to the username input element:</span></span>

```cshtml
<input ref="username" ...>

@functions {
    ElementRef username;
}
```

> [!NOTE]
> <span data-ttu-id="24a47-157">Führen Sie **nicht** erfasste Elementverweise als eine Möglichkeit zum Auffüllen des DOM verwenden</span><span class="sxs-lookup"><span data-stu-id="24a47-157">Do **not** use captured element references as a way of populating the DOM.</span></span> <span data-ttu-id="24a47-158">Auf diese Weise kann die deklarative Renderingmodell beeinträchtigen.</span><span class="sxs-lookup"><span data-stu-id="24a47-158">Doing so may interfere with the declarative rendering model.</span></span>

<span data-ttu-id="24a47-159">Als .NET Code geht, eine `ElementRef` ist ein nicht transparentes Handle.</span><span class="sxs-lookup"><span data-stu-id="24a47-159">As far as .NET code is concerned, an `ElementRef` is an opaque handle.</span></span> <span data-ttu-id="24a47-160">Die *nur* Sache, die du `ElementRef` über über JavaScript-Interop an JavaScript-Code übergeben.</span><span class="sxs-lookup"><span data-stu-id="24a47-160">The *only* thing you can do with `ElementRef` is pass it through to JavaScript code via JavaScript interop.</span></span> <span data-ttu-id="24a47-161">Wenn Sie dies tun, empfängt der JavaScript-Seite-Code ein `HTMLElement` -Instanz, die sie mit normalen DOM-APIs verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="24a47-161">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="24a47-162">Der folgende Code definiert z. B. eine .NET-Erweiterungsmethode, die es ermöglicht, den Fokus auf ein Element festlegen:</span><span class="sxs-lookup"><span data-stu-id="24a47-162">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="24a47-163">*exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="24a47-163">*exampleJsInterop.js*:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="24a47-164">Verwendung `IJSRuntime.InvokeAsync<T>` , und rufen Sie `exampleJsFunctions.focusElement` mit einer `ElementRef` ein Element zu konzentrieren:</span><span class="sxs-lookup"><span data-stu-id="24a47-164">Use `IJSRuntime.InvokeAsync<T>` and call `exampleJsFunctions.focusElement` with an `ElementRef` to focus an element:</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/component1.cshtml?highlight=1,3,7,11-12)]

<span data-ttu-id="24a47-165">Um eine Erweiterungsmethode verwenden, um ein Element zu konzentrieren, erstellen Sie eine statische Erweiterungsmethode, die empfängt die `IJSRuntime` Instanz:</span><span class="sxs-lookup"><span data-stu-id="24a47-165">To use an extension method to focus an element, create a static extension method that receives the `IJSRuntime` instance:</span></span>

```csharp
public static Task Focus(this ElementRef elementRef, IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<object>(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="24a47-166">Die Methode wird direkt auf das Objekt aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="24a47-166">The method is called directly on the object.</span></span> <span data-ttu-id="24a47-167">Im folgende Beispiel wird vorausgesetzt, dass die statische `Focus` Methode steht in der `JsInteropClasses` Namespace:</span><span class="sxs-lookup"><span data-stu-id="24a47-167">The following example assumes that the static `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/component2.cshtml?highlight=1,4,8,12)]

> [!IMPORTANT]
> <span data-ttu-id="24a47-168">Die `username` Variable wird nur aufgefüllt, nachdem die Komponente rendert und die Ausgabe enthält die `>` Element.</span><span class="sxs-lookup"><span data-stu-id="24a47-168">The `username` variable is only populated after the component renders and its output includes the `>` element.</span></span> <span data-ttu-id="24a47-169">Wenn Sie versuchen, eine nicht aufgefüllte übergeben `ElementRef` für JavaScript-Code, der JavaScript-Code empfängt `null`.</span><span class="sxs-lookup"><span data-stu-id="24a47-169">If you try to pass an unpopulated `ElementRef` to JavaScript code, the JavaScript code receives `null`.</span></span> <span data-ttu-id="24a47-170">Elementverweise zu ändern, nachdem die Komponente verwenden (um den Anfangsfokus für ein Element festgelegt) Rendern abgeschlossen ist die `OnAfterRenderAsync` oder `OnAfterRender` [Komponente Lebenszyklusmethoden](xref:razor-components/components#lifecycle-methods).</span><span class="sxs-lookup"><span data-stu-id="24a47-170">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the `OnAfterRenderAsync` or `OnAfterRender` [component lifecycle methods](xref:razor-components/components#lifecycle-methods).</span></span>

## <a name="invoke-net-methods-from-javascript-functions"></a><span data-ttu-id="24a47-171">Aufrufen von .NET Methoden von JavaScript-Funktionen</span><span class="sxs-lookup"><span data-stu-id="24a47-171">Invoke .NET methods from JavaScript functions</span></span>

### <a name="static-net-method-call"></a><span data-ttu-id="24a47-172">Statischen Methodenaufruf in .NET</span><span class="sxs-lookup"><span data-stu-id="24a47-172">Static .NET method call</span></span>

<span data-ttu-id="24a47-173">Um eine statische .NET-Methode aus JavaScript aufrufen zu können, verwenden Sie die `DotNet.invokeMethod` oder `DotNet.invokeMethodAsync` Funktionen.</span><span class="sxs-lookup"><span data-stu-id="24a47-173">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="24a47-174">Übergeben Sie den Bezeichner der statischen Methode, die Sie aufrufen, den Namen der Assembly mit der Funktion und alle Argumente möchten.</span><span class="sxs-lookup"><span data-stu-id="24a47-174">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="24a47-175">Die asynchrone Version wird bevorzugt, um serverseitige Szenarien zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="24a47-175">The asynchronous version is preferred to support server-side scenarios.</span></span> <span data-ttu-id="24a47-176">Um aus JavaScript aufgerufen werden, die .NET-Methode muss öffentliche, statische und durch `[JSInvokable]`.</span><span class="sxs-lookup"><span data-stu-id="24a47-176">To be invokable from JavaScript, the .NET method must be public, static, and decorated with `[JSInvokable]`.</span></span> <span data-ttu-id="24a47-177">Der Methodenbezeichner wird standardmäßig der Name der Methode, aber Sie können angeben, einen anderen Bezeichner mit dem `JSInvokableAttribute` Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="24a47-177">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor.</span></span> <span data-ttu-id="24a47-178">Offene generische Methoden aufrufen wird derzeit nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="24a47-178">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="24a47-179">Die beispielanwendung enthält einen C# Methode, um ein Array von zurückzugeben `int`s.</span><span class="sxs-lookup"><span data-stu-id="24a47-179">The sample app includes a C# method to return an array of `int`s.</span></span> <span data-ttu-id="24a47-180">Die Methode wird ergänzt, mit der `JSInvokable` Attribut.</span><span class="sxs-lookup"><span data-stu-id="24a47-180">The method is decorated with the `JSInvokable` attribute.</span></span>

<span data-ttu-id="24a47-181">*Pages/JsInterop.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="24a47-181">*Pages/JsInterop.cshtml*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.cshtml?name=snippet_JSInterop2&highlight=7-11)]

<span data-ttu-id="24a47-182">JavaScript, die für den Client bereitgestellt Ruft die C# .NET-Methode übergeben.</span><span class="sxs-lookup"><span data-stu-id="24a47-182">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="24a47-183">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="24a47-183">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="24a47-184">Wenn die **Trigger .NET statische Methode ReturnArrayAsync** ausgewählt ist, überprüfen Sie die Konsolenausgabe in im Browser auf die Web-Entwicklertools.</span><span class="sxs-lookup"><span data-stu-id="24a47-184">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="24a47-185">Die Konsolenausgabe wird:</span><span class="sxs-lookup"><span data-stu-id="24a47-185">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="24a47-186">Der vierte Arraywert wird in das Array abgelegt (`data.push(4);`) vom `ReturnArrayAsync`.</span><span class="sxs-lookup"><span data-stu-id="24a47-186">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

### <a name="instance-method-call"></a><span data-ttu-id="24a47-187">Instanz-Methodenaufruf</span><span class="sxs-lookup"><span data-stu-id="24a47-187">Instance method call</span></span>

<span data-ttu-id="24a47-188">Sie können auch Instanzmethoden für .NET aus JavaScript aufrufen.</span><span class="sxs-lookup"><span data-stu-id="24a47-188">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="24a47-189">Um eine Instanzmethode für .NET aus JavaScript aufrufen zu können, zuerst übergeben Sie die .NET Instanz in JavaScript durch wrapping in eine `DotNetObjectRef` Instanz.</span><span class="sxs-lookup"><span data-stu-id="24a47-189">To invoke a .NET instance method from JavaScript, first pass the .NET instance to JavaScript by wrapping it in a `DotNetObjectRef` instance.</span></span> <span data-ttu-id="24a47-190">Die .NET-Instanz als Verweis an JavaScript übergeben wird, und Sie können .NET von Instanzmethoden für die Instanz mit Aufrufen der `invokeMethod` oder `invokeMethodAsync` Funktionen.</span><span class="sxs-lookup"><span data-stu-id="24a47-190">The .NET instance is passed by reference to JavaScript, and you can invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="24a47-191">Die Instanz der .NET kann auch als Argument übergeben werden, wenn andere .NET Methoden von JavaScript aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="24a47-191">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="24a47-192">Die Beispiel-app protokolliert Meldungen in die Client-Side-Konsole.</span><span class="sxs-lookup"><span data-stu-id="24a47-192">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="24a47-193">Überprüfen Sie für den folgenden Beispielen gezeigt, die von der Beispiel-app im Browser auf die Konsolenausgabe in das browserentwicklertools.</span><span class="sxs-lookup"><span data-stu-id="24a47-193">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="24a47-194">Wenn die **Trigger .NET Instanzmethode HelloHelper.SayHello** ausgewählt ist, `ExampleJsInterop.CallHelloHelperSayHello` aufgerufen wird, und übergeben Sie einen Namen, `Blazor`, an die Methode.</span><span class="sxs-lookup"><span data-stu-id="24a47-194">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="24a47-195">*Pages/JsInterop.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="24a47-195">*Pages/JsInterop.cshtml*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.cshtml?name=snippet_JSInterop3&highlight=8-9)]

`CallHelloHelperSayHello` <span data-ttu-id="24a47-196">Ruft die JavaScript-Funktion `sayHello` durch eine neue Instanz der `HelloHelper`.</span><span class="sxs-lookup"><span data-stu-id="24a47-196">invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="24a47-197">*JsInteropClasses/ExampleJsInterop.cs*:</span><span class="sxs-lookup"><span data-stu-id="24a47-197">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

<span data-ttu-id="24a47-198">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="24a47-198">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="24a47-199">Der Name wird zum übergeben `HelloHelper`Konstruktor, der legt der `HelloHelper.Name` Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="24a47-199">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="24a47-200">Wenn die JavaScript-Funktion `sayHello` ausgeführt wird, `HelloHelper.SayHello` gibt die `Hello, {Name}!` -Nachricht, die von der JavaScript-Funktion in die Konsole geschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="24a47-200">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="24a47-201">*JsInteropClasses/HelloHelper.cs*:</span><span class="sxs-lookup"><span data-stu-id="24a47-201">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="24a47-202">Ausgabe im im Browser auf die Web-Entwicklertools-Konsole:</span><span class="sxs-lookup"><span data-stu-id="24a47-202">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

## <a name="share-interop-code-in-a-razor-component-class-library"></a><span data-ttu-id="24a47-203">Freigeben von Interop-Code in einer Klassenbibliothek für die Razor-Komponente</span><span class="sxs-lookup"><span data-stu-id="24a47-203">Share interop code in a Razor Component class library</span></span>

<span data-ttu-id="24a47-204">Interop-JavaScript-Code in einer Klassenbibliothek für die Razor-Komponente enthalten sein kann (`dotnet new razorclasslib`), können Sie den Code in einem NuGet-Paket verwenden.</span><span class="sxs-lookup"><span data-stu-id="24a47-204">JavaScript interop code can be included in a Razor Component class library (`dotnet new razorclasslib`), which allows you to share the code in a NuGet package.</span></span>

<span data-ttu-id="24a47-205">Die Komponente der Razor-Klassenbibliothek behandelt die Einbettung von JavaScript-Ressourcen in der erstellten Assembly.</span><span class="sxs-lookup"><span data-stu-id="24a47-205">The Razor Component class library handles embedding JavaScript resources in the built assembly.</span></span> <span data-ttu-id="24a47-206">Die JavaScript-Dateien befinden sich der *"Wwwroot"* Ordner.</span><span class="sxs-lookup"><span data-stu-id="24a47-206">The JavaScript files are placed in the *wwwroot* folder.</span></span> <span data-ttu-id="24a47-207">Das Tool übernimmt Einbettung der Ressourcen aus, wenn die Bibliothek erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="24a47-207">The tooling takes care of embedding the resources when the library is built.</span></span>

<span data-ttu-id="24a47-208">Das erstellte NuGet-Paket wird in der Projektdatei der app verwiesen werden, genau wie normale NuGet-Pakets verwiesen wird.</span><span class="sxs-lookup"><span data-stu-id="24a47-208">The built NuGet package is referenced in the project file of the app just as any normal NuGet package is referenced.</span></span> <span data-ttu-id="24a47-209">Nach der Wiederherstellung der app kann app-Code in JavaScript aufrufen, als wäre er C#.</span><span class="sxs-lookup"><span data-stu-id="24a47-209">After the app is restored, app code can call into JavaScript as if it were C#.</span></span>

<span data-ttu-id="24a47-210">Weitere Informationen finden Sie unter <xref:razor-components/class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="24a47-210">For more information, see <xref:razor-components/class-libraries>.</span></span>
