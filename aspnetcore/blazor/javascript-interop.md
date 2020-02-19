---
title: ASP.net Core Blazor JavaScript-Interop
author: guardrex
description: Erfahren Sie, wie Sie JavaScript-Funktionen aus .net-und .NET-Methoden aus JavaScript in Blazor-apps aufrufen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/javascript-interop
ms.openlocfilehash: d681eea5a5e876912bd614fba8ea45a464844496
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/19/2020
ms.locfileid: "77447164"
---
# <a name="aspnet-core-opno-locblazor-javascript-interop"></a><span data-ttu-id="40000-103">ASP.net Core Blazor JavaScript-Interop</span><span class="sxs-lookup"><span data-stu-id="40000-103">ASP.NET Core Blazor JavaScript interop</span></span>

<span data-ttu-id="40000-104">Von [Javier calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27)und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="40000-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="40000-105">Eine Blazor-App kann JavaScript-Funktionen von .net-und .NET-Methoden aus JavaScript-Code aufrufen.</span><span class="sxs-lookup"><span data-stu-id="40000-105">A Blazor app can invoke JavaScript functions from .NET and .NET methods from JavaScript code.</span></span>

<span data-ttu-id="40000-106">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="40000-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="invoke-javascript-functions-from-net-methods"></a><span data-ttu-id="40000-107">Aufrufen von JavaScript-Funktionen aus .NET-Methoden</span><span class="sxs-lookup"><span data-stu-id="40000-107">Invoke JavaScript functions from .NET methods</span></span>

<span data-ttu-id="40000-108">Es gibt Zeiten, in denen .NET-Code erforderlich ist, um eine JavaScript-Funktion aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="40000-108">There are times when .NET code is required to call a JavaScript function.</span></span> <span data-ttu-id="40000-109">Beispielsweise kann ein JavaScript-Befehl Browserfunktionen oder-Funktionen aus einer JavaScript-Bibliothek für die app verfügbar machen.</span><span class="sxs-lookup"><span data-stu-id="40000-109">For example, a JavaScript call can expose browser capabilities or functionality from a JavaScript library to the app.</span></span> <span data-ttu-id="40000-110">Dieses Szenario wird als *JavaScript-Interoperabilität* (*js-Interop*) bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="40000-110">This scenario is called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="40000-111">Verwenden Sie zum Abrufen von JavaScript aus .net die `IJSRuntime` Abstraktion.</span><span class="sxs-lookup"><span data-stu-id="40000-111">To call into JavaScript from .NET, use the `IJSRuntime` abstraction.</span></span> <span data-ttu-id="40000-112">Um js-Interop-Aufrufe auszugeben, fügen Sie die `IJSRuntime` Abstraktion in die Komponente ein.</span><span class="sxs-lookup"><span data-stu-id="40000-112">To issue JS interop calls, inject the `IJSRuntime` abstraction in your component.</span></span> <span data-ttu-id="40000-113">Die `InvokeAsync<T>`-Methode nimmt einen Bezeichner für die JavaScript-Funktion an, die Sie zusammen mit einer beliebigen Anzahl von JSON-serialisierbaren Argumenten aufrufen möchten.</span><span class="sxs-lookup"><span data-stu-id="40000-113">The `InvokeAsync<T>` method takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="40000-114">Der Funktions Bezeichner ist relativ zum globalen Gültigkeitsbereich (`window`).</span><span class="sxs-lookup"><span data-stu-id="40000-114">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="40000-115">Wenn Sie `window.someScope.someFunction`anrufen möchten, wird der Bezeichner `someScope.someFunction`.</span><span class="sxs-lookup"><span data-stu-id="40000-115">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="40000-116">Es ist nicht erforderlich, die Funktion zu registrieren, bevor Sie aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="40000-116">There's no need to register the function before it's called.</span></span> <span data-ttu-id="40000-117">Der Rückgabetyp `T` muss ebenfalls JSON-serialisierbar sein.</span><span class="sxs-lookup"><span data-stu-id="40000-117">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="40000-118">`T` sollte dem .NET-Typ entsprechen, der dem zurückgegebenen JSON-Typ am besten zugeordnet wird.</span><span class="sxs-lookup"><span data-stu-id="40000-118">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="40000-119">Bei Blazor Server-apps, bei denen die vorab Aktivierung aktiviert ist, ist das Aufrufen von JavaScript während der ersten vorab Generierung nicht möglich.</span><span class="sxs-lookup"><span data-stu-id="40000-119">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="40000-120">JavaScript-Interop-Aufrufe müssen verzögert werden, bis die Verbindung mit dem Browser hergestellt ist.</span><span class="sxs-lookup"><span data-stu-id="40000-120">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="40000-121">Weitere Informationen finden Sie im Abschnitt [erkennen, wenn eine Blazor-App eine Vorabversion ist](#detect-when-a-blazor-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="40000-121">For more information, see the [Detect when a Blazor app is prerendering](#detect-when-a-blazor-app-is-prerendering) section.</span></span>

<span data-ttu-id="40000-122">Das folgende Beispiel basiert auf [textdecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), einem experimentellen JavaScript-basierten Decoder.</span><span class="sxs-lookup"><span data-stu-id="40000-122">The following example is based on [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), an experimental JavaScript-based decoder.</span></span> <span data-ttu-id="40000-123">Im Beispiel wird veranschaulicht, wie eine JavaScript-Funktion aus C# einer Methode aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="40000-123">The example demonstrates how to invoke a JavaScript function from a C# method.</span></span> <span data-ttu-id="40000-124">Die JavaScript-Funktion akzeptiert ein Bytearray C# aus einer Methode, decodiert das Array und gibt den Text zur Anzeige an die Komponente zurück.</span><span class="sxs-lookup"><span data-stu-id="40000-124">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="40000-125">Geben Sie im `<head>`-Element von *wwwroot/Index.html* (Blazor Webassembly) oder *pages/_Host. cshtml* (Blazor Server) eine JavaScript-Funktion an, die `TextDecoder` zum Decodieren eines bestandenen Arrays verwendet und den decodierten Wert zurückgibt:</span><span class="sxs-lookup"><span data-stu-id="40000-125">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](javascript-interop/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="40000-126">JavaScript-Code, wie z. b. der im vorherigen Beispiel gezeigte Code, kann auch aus einer JavaScript-Datei ( *. js*) mit einem Verweis auf die Skriptdatei geladen werden:</span><span class="sxs-lookup"><span data-stu-id="40000-126">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (*.js*) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="40000-127">Folgende Komponente:</span><span class="sxs-lookup"><span data-stu-id="40000-127">The following component:</span></span>

* <span data-ttu-id="40000-128">Ruft die `convertArray` JavaScript-Funktion mithilfe von `JSRuntime` auf, wenn eine Komponenten Schaltfläche (**Array konvertieren**) ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="40000-128">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**Convert Array**) is selected.</span></span>
* <span data-ttu-id="40000-129">Nachdem die JavaScript-Funktion aufgerufen wurde, wird das bestandene Array in eine Zeichenfolge konvertiert.</span><span class="sxs-lookup"><span data-stu-id="40000-129">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="40000-130">Die Zeichenfolge wird zur Anzeige an die Komponente zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="40000-130">The string is returned to the component for display.</span></span>

[!code-razor[](javascript-interop/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="use-of-ijsruntime"></a><span data-ttu-id="40000-131">Verwendung von ijsruntime</span><span class="sxs-lookup"><span data-stu-id="40000-131">Use of IJSRuntime</span></span>

<span data-ttu-id="40000-132">Um die `IJSRuntime` Abstraktion zu verwenden, übernehmen Sie einen der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="40000-132">To use the `IJSRuntime` abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="40000-133">Fügen Sie die `IJSRuntime` Abstraktion in*die Razor-Komponente (Razor*) ein:</span><span class="sxs-lookup"><span data-stu-id="40000-133">Inject the `IJSRuntime` abstraction into the Razor component (*.razor*):</span></span>

  [!code-razor[](javascript-interop/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="40000-134">Geben Sie im `<head>`-Element von *wwwroot/Index.html* (Blazor Webassembly) oder *pages/_Host. cshtml* (Blazor Server) eine `handleTickerChanged` JavaScript-Funktion an.</span><span class="sxs-lookup"><span data-stu-id="40000-134">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="40000-135">Die-Funktion wird mit `IJSRuntime.InvokeVoidAsync` aufgerufen und gibt keinen Wert zurück:</span><span class="sxs-lookup"><span data-stu-id="40000-135">The function is called with `IJSRuntime.InvokeVoidAsync` and doesn't return a value:</span></span>

  [!code-html[](javascript-interop/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="40000-136">Fügen Sie die `IJSRuntime` Abstraktion in eine Klasse ( *. cs*) ein:</span><span class="sxs-lookup"><span data-stu-id="40000-136">Inject the `IJSRuntime` abstraction into a class (*.cs*):</span></span>

  [!code-csharp[](javascript-interop/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="40000-137">Geben Sie im `<head>`-Element von *wwwroot/Index.html* (Blazor Webassembly) oder *pages/_Host. cshtml* (Blazor Server) eine `handleTickerChanged` JavaScript-Funktion an.</span><span class="sxs-lookup"><span data-stu-id="40000-137">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="40000-138">Die-Funktion wird mit `JSRuntime.InvokeAsync` aufgerufen und gibt einen Wert zurück:</span><span class="sxs-lookup"><span data-stu-id="40000-138">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](javascript-interop/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="40000-139">Verwenden Sie für die Generierung dynamischer Inhalte mit [buildrendertree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic)das `[Inject]`-Attribut:</span><span class="sxs-lookup"><span data-stu-id="40000-139">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="40000-140">In der Client seitigen Beispiel-APP, die dieses Thema begleitet, sind zwei JavaScript-Funktionen für die app verfügbar, die mit dem Dom interagieren, um Benutzereingaben zu empfangen und eine Willkommensnachricht anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="40000-140">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="40000-141">`showPrompt` &ndash; generiert eine Aufforderung zur Annahme von Benutzereingaben (Name des Benutzers) und gibt den Namen an den Aufrufer zurück.</span><span class="sxs-lookup"><span data-stu-id="40000-141">`showPrompt` &ndash; Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="40000-142">`displayWelcome` &ndash; eine Begrüßungs Meldung vom Aufrufer einem DOM-Objekt mit einer `id` `welcome`zuweist.</span><span class="sxs-lookup"><span data-stu-id="40000-142">`displayWelcome` &ndash; Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="40000-143">*wwwroot/examplejsinterop. js*:</span><span class="sxs-lookup"><span data-stu-id="40000-143">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="40000-144">Platzieren Sie das `<script>` Tag, das auf die JavaScript-Datei verweist, in der Datei *wwwroot/Index.html* (Blazor Webassembly) oder der Datei *pages/_Host. cshtml* (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="40000-144">Place the `<script>` tag that references the JavaScript file in the *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server).</span></span>

<span data-ttu-id="40000-145">*wwwroot/Index.html* (Blazor Webassembly):</span><span class="sxs-lookup"><span data-stu-id="40000-145">*wwwroot/index.html* (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="40000-146">*Pages/_Host. cshtml* (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="40000-146">*Pages/_Host.cshtml* (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

<span data-ttu-id="40000-147">Platzieren Sie kein `<script>`-Tag in einer Komponenten Datei, da das `<script>`-Tag nicht dynamisch aktualisiert werden kann.</span><span class="sxs-lookup"><span data-stu-id="40000-147">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="40000-148">.NET-Methoden Interop mit den JavaScript-Funktionen in der Datei " *examplejsinterop. js* " durch Aufrufen von `IJSRuntime.InvokeAsync<T>`.</span><span class="sxs-lookup"><span data-stu-id="40000-148">.NET methods interop with the JavaScript functions in the *exampleJsInterop.js* file by calling `IJSRuntime.InvokeAsync<T>`.</span></span>

<span data-ttu-id="40000-149">Die `IJSRuntime` Abstraktion ist asynchron, um Blazor Server Szenarios zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="40000-149">The `IJSRuntime` abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="40000-150">Wenn es sich bei der App um eine Blazor Webassembly-App handelt und Sie eine JavaScript-Funktion synchron aufrufen möchten, können Sie eine Typumwandlung-Funktion in `IJSInProcessRuntime` und stattdessen `Invoke<T>` aufrufen.</span><span class="sxs-lookup"><span data-stu-id="40000-150">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to `IJSInProcessRuntime` and call `Invoke<T>` instead.</span></span> <span data-ttu-id="40000-151">Es wird empfohlen, dass die meisten js-Interop-Bibliotheken die Async-APIs verwenden, um sicherzustellen, dass die Bibliotheken in allen Szenarien verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="40000-151">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

<span data-ttu-id="40000-152">Die Beispiel-app enthält eine Komponente zum Veranschaulichen der JS-Interop.</span><span class="sxs-lookup"><span data-stu-id="40000-152">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="40000-153">Die Komponente:</span><span class="sxs-lookup"><span data-stu-id="40000-153">The component:</span></span>

* <span data-ttu-id="40000-154">Empfängt Benutzereingaben über eine JavaScript-Eingabeaufforderung.</span><span class="sxs-lookup"><span data-stu-id="40000-154">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="40000-155">Gibt den Text zur Verarbeitung an die Komponente zurück.</span><span class="sxs-lookup"><span data-stu-id="40000-155">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="40000-156">Ruft eine zweite JavaScript-Funktion auf, die mit dem Dom interagiert, um eine Willkommensnachricht anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="40000-156">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="40000-157">*Pages/jsinterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="40000-157">*Pages/JSInterop.razor*:</span></span>

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

1. <span data-ttu-id="40000-158">Wenn `TriggerJsPrompt` ausgeführt wird, indem die Schaltfläche **JavaScript-Eingabeaufforderung auslösen** der Komponente ausgewählt wird, wird die JavaScript-`showPrompt`-Funktion, die in der Datei *wwwroot/examplejsinterop. js* bereitgestellt wird, aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="40000-158">When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the JavaScript `showPrompt` function provided in the *wwwroot/exampleJsInterop.js* file is called.</span></span>
1. <span data-ttu-id="40000-159">Die `showPrompt` Funktion akzeptiert Benutzereingaben (den Namen des Benutzers), die HTML-codiert sind und an die Komponente zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="40000-159">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="40000-160">Die Komponente speichert den Benutzernamen in einer lokalen Variablen, `name`.</span><span class="sxs-lookup"><span data-stu-id="40000-160">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="40000-161">Die in `name` gespeicherte Zeichenfolge wird in eine Begrüßungsnachricht eingebunden, die an eine JavaScript-Funktion, `displayWelcome`, übermittelt wird, die die Begrüßungsnachricht in ein Überschriften-Tag rendert.</span><span class="sxs-lookup"><span data-stu-id="40000-161">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="40000-162">JavaScript-Funktion "void" aufruft</span><span class="sxs-lookup"><span data-stu-id="40000-162">Call a void JavaScript function</span></span>

<span data-ttu-id="40000-163">JavaScript-Funktionen, die [void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) oder nicht [definiert](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) zurückgeben, werden mit `IJSRuntime.InvokeVoidAsync`aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="40000-163">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with `IJSRuntime.InvokeVoidAsync`.</span></span>

## <a name="detect-when-a-opno-locblazor-app-is-prerendering"></a><span data-ttu-id="40000-164">Erkennen, wenn eine Blazor-App vorgestellt wird</span><span class="sxs-lookup"><span data-stu-id="40000-164">Detect when a Blazor app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="40000-165">Verweise auf Elemente erfassen</span><span class="sxs-lookup"><span data-stu-id="40000-165">Capture references to elements</span></span>

<span data-ttu-id="40000-166">Einige js-Interop-Szenarien erfordern Verweise auf HTML-Elemente.</span><span class="sxs-lookup"><span data-stu-id="40000-166">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="40000-167">Beispielsweise kann eine UI-Bibliothek einen Element Verweis für die Initialisierung erfordern, oder Sie müssen möglicherweise Befehls ähnliche APIs für ein Element, z. b. `focus` oder `play`, aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="40000-167">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="40000-168">Erfassen Sie mithilfe des folgenden Ansatzes Verweise auf HTML-Elemente in einer-Komponente:</span><span class="sxs-lookup"><span data-stu-id="40000-168">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="40000-169">Fügen Sie dem HTML--Element ein `@ref`-Attribut hinzu.</span><span class="sxs-lookup"><span data-stu-id="40000-169">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="40000-170">Definieren Sie ein Feld vom Typ `ElementReference`, dessen Name mit dem Wert des `@ref` Attributs übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="40000-170">Define a field of type `ElementReference` whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="40000-171">Das folgende Beispiel zeigt, wie Sie einen Verweis auf das `username` `<input>`-Element erfassen:</span><span class="sxs-lookup"><span data-stu-id="40000-171">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="40000-172">Verwenden Sie nur einen Element Verweis, um den Inhalt eines leeren Elements zu mutieren, das nicht mit Blazorinteragiert.</span><span class="sxs-lookup"><span data-stu-id="40000-172">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="40000-173">Dieses Szenario ist nützlich, wenn eine Drittanbieter-API Inhalt für das-Element bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="40000-173">This scenario is useful when a 3rd party API supplies content to the element.</span></span> <span data-ttu-id="40000-174">Da Blazor nicht mit dem-Element interagiert, besteht keine Möglichkeit, einen Konflikt zwischen BlazorDarstellung des-Elements und des Dom zu haben.</span><span class="sxs-lookup"><span data-stu-id="40000-174">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="40000-175">Im folgenden Beispiel ist es *gefährlich* , den Inhalt der unsortierten Liste (`ul`) zu mutieren, da Blazor mit dem Dom interagiert, um die Listenelemente dieses Elements (`<li>`) aufzufüllen:</span><span class="sxs-lookup"><span data-stu-id="40000-175">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
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
> <span data-ttu-id="40000-176">Wenn das js-Interop den Inhalt des-Elements `MyList` und Blazor versucht, Unterschiede auf das Element anzuwenden, stimmen die Unterschiede nicht mit dem Dom.</span><span class="sxs-lookup"><span data-stu-id="40000-176">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="40000-177">Wenn .NET-Code betroffen ist, ist ein `ElementReference` ein undurchsichtiges handle.</span><span class="sxs-lookup"><span data-stu-id="40000-177">As far as .NET code is concerned, an `ElementReference` is an opaque handle.</span></span> <span data-ttu-id="40000-178">Das *einzige* , was Sie mit `ElementReference` tun können, ist die Übergabe an JavaScript-Code über js-Interop.</span><span class="sxs-lookup"><span data-stu-id="40000-178">The *only* thing you can do with `ElementReference` is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="40000-179">Wenn Sie dies tun, empfängt der JavaScript-seitige Code eine `HTMLElement`-Instanz, die mit normalen DOM-APIs verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="40000-179">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="40000-180">Der folgende Code definiert z. b. eine .net-Erweiterungsmethode, die das Festlegen des Fokus auf ein Element ermöglicht:</span><span class="sxs-lookup"><span data-stu-id="40000-180">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="40000-181">*examplejsinterop. js*:</span><span class="sxs-lookup"><span data-stu-id="40000-181">*exampleJsInterop.js*:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="40000-182">Verwenden Sie `IJSRuntime.InvokeVoidAsync`, um eine JavaScript-Funktion aufzurufen, die keinen Wert zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="40000-182">To call a JavaScript function that doesn't return a value, use `IJSRuntime.InvokeVoidAsync`.</span></span> <span data-ttu-id="40000-183">Mit dem folgenden Code wird der Fokus auf die username-Eingabe festgelegt, indem die vorherige JavaScript-Funktion mit dem erfassten `ElementReference`aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="40000-183">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured `ElementReference`:</span></span>

[!code-razor[](javascript-interop/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="40000-184">Erstellen Sie eine statische Erweiterungsmethode, die die `IJSRuntime` Instanz empfängt, um eine Erweiterungsmethode zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="40000-184">To use an extension method, create a static extension method that receives the `IJSRuntime` instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="40000-185">Die `Focus`-Methode wird direkt für das-Objekt aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="40000-185">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="40000-186">Im folgenden Beispiel wird davon ausgegangen, dass die `Focus`-Methode über den `JsInteropClasses`-Namespace verfügbar ist:</span><span class="sxs-lookup"><span data-stu-id="40000-186">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](javascript-interop/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="40000-187">Die `username` Variable wird erst aufgefüllt, nachdem die Komponente gerendert wurde.</span><span class="sxs-lookup"><span data-stu-id="40000-187">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="40000-188">Wenn eine nicht aufgefüllte `ElementReference` an den JavaScript-Code übermittelt wird, erhält der JavaScript-Code den Wert `null`.</span><span class="sxs-lookup"><span data-stu-id="40000-188">If an unpopulated `ElementReference` is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="40000-189">Um Element Verweise zu manipulieren, nachdem die Komponente das Rendering abgeschlossen hat (um den anfänglichen Fokus auf ein Element festzulegen), verwenden Sie die [onafterrenderasync-oder onafterrendering-Komponenten Lebenszyklus-Methoden](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="40000-189">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [OnAfterRenderAsync or OnAfterRender component lifecycle methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="40000-190">Wenn Sie mit generischen Typen arbeiten und einen Wert zurückgeben, verwenden Sie [valuetask\<t >](xref:System.Threading.Tasks.ValueTask`1):</span><span class="sxs-lookup"><span data-stu-id="40000-190">When working with generic types and returning a value, use [ValueTask\<T>](xref:System.Threading.Tasks.ValueTask`1):</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="40000-191">`GenericMethod` wird direkt für das-Objekt mit einem-Typ aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="40000-191">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="40000-192">Im folgenden Beispiel wird davon ausgegangen, dass die `GenericMethod` im `JsInteropClasses`-Namespace verfügbar ist:</span><span class="sxs-lookup"><span data-stu-id="40000-192">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](javascript-interop/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="40000-193">Komponenten übergreifende Verweis Elemente</span><span class="sxs-lookup"><span data-stu-id="40000-193">Reference elements across components</span></span>

<span data-ttu-id="40000-194">Ein `ElementReference` ist nur in der `OnAfterRender`-Methode einer Komponente, und ein Element Verweis ist ein `struct`), sodass ein Element Verweis nicht zwischen Komponenten übermittelt werden kann.</span><span class="sxs-lookup"><span data-stu-id="40000-194">An `ElementReference` is only guaranteed valid in a component's `OnAfterRender` method (and an element reference is a `struct`), so an element reference can't be passed between components.</span></span>

<span data-ttu-id="40000-195">Damit eine übergeordnete Komponente einen Element Verweis für andere Komponenten verfügbar macht, kann die übergeordnete Komponente folgende Aktionen ausführen:</span><span class="sxs-lookup"><span data-stu-id="40000-195">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="40000-196">Ermöglicht es untergeordneten Komponenten, Rückrufe zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="40000-196">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="40000-197">Rufen Sie die registrierten Rückrufe während des `OnAfterRender` Ereignisses mit dem bestandenen Element Verweis auf.</span><span class="sxs-lookup"><span data-stu-id="40000-197">Invoke the registered callbacks during the `OnAfterRender` event with the passed element reference.</span></span> <span data-ttu-id="40000-198">Indirekt ermöglicht dieser Ansatz, dass untergeordnete Komponenten mit dem Element Verweis des übergeordneten Elements interagieren.</span><span class="sxs-lookup"><span data-stu-id="40000-198">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="40000-199">Im folgenden Blazor Webassembly-Beispiel wird der Ansatz veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="40000-199">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="40000-200">Im `<head>` von *wwwroot/Index.html*:</span><span class="sxs-lookup"><span data-stu-id="40000-200">In the `<head>` of *wwwroot/index.html*:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="40000-201">Im `<body>` von *wwwroot/Index.html*:</span><span class="sxs-lookup"><span data-stu-id="40000-201">In the `<body>` of *wwwroot/index.html*:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="40000-202">*Pages/index. Razor* (übergeordnete Komponente):</span><span class="sxs-lookup"><span data-stu-id="40000-202">*Pages/Index.razor* (parent component):</span></span>

```razor
@page "/"

<h1 @ref="_title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="40000-203">*Pages/index. Razor. cs*:</span><span class="sxs-lookup"><span data-stu-id="40000-203">*Pages/Index.razor.cs*:</span></span>

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

<span data-ttu-id="40000-204">*Shared/surveyprompt. Razor* (untergeordnete Komponente):</span><span class="sxs-lookup"><span data-stu-id="40000-204">*Shared/SurveyPrompt.razor* (child component):</span></span>

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

<span data-ttu-id="40000-205">*Shared/surveyprompt. Razor. cs*:</span><span class="sxs-lookup"><span data-stu-id="40000-205">*Shared/SurveyPrompt.razor.cs*:</span></span>

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

## <a name="invoke-net-methods-from-javascript-functions"></a><span data-ttu-id="40000-206">Aufrufen von .NET-Methoden aus JavaScript-Funktionen</span><span class="sxs-lookup"><span data-stu-id="40000-206">Invoke .NET methods from JavaScript functions</span></span>

### <a name="static-net-method-call"></a><span data-ttu-id="40000-207">Statischer .net-Methodenaufrufe</span><span class="sxs-lookup"><span data-stu-id="40000-207">Static .NET method call</span></span>

<span data-ttu-id="40000-208">Zum Aufrufen einer statischen .NET-Methode aus JavaScript verwenden Sie die Funktionen `DotNet.invokeMethod` oder `DotNet.invokeMethodAsync`.</span><span class="sxs-lookup"><span data-stu-id="40000-208">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="40000-209">Übergeben Sie den Bezeichner der statischen Methode, die aufgerufen werden soll, den Namen der Assembly, die die Funktion enthält, und alle Argumente.</span><span class="sxs-lookup"><span data-stu-id="40000-209">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="40000-210">Die asynchrone Version ist für die Unterstützung von Blazor Server-Szenarien bevorzugt.</span><span class="sxs-lookup"><span data-stu-id="40000-210">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="40000-211">Die .NET-Methode muss öffentlich und statisch sein und über das `[JSInvokable]`-Attribut verfügen.</span><span class="sxs-lookup"><span data-stu-id="40000-211">The .NET method must be public, static, and have the `[JSInvokable]` attribute.</span></span> <span data-ttu-id="40000-212">Das Aufrufen offener generischer Methoden wird derzeit nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="40000-212">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="40000-213">Die Beispiel-app enthält C# eine-Methode, um ein Array von `int`s zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="40000-213">The sample app includes a C# method to return an array of `int`s.</span></span> <span data-ttu-id="40000-214">Das `JSInvokable`-Attribut wird auf die-Methode angewendet.</span><span class="sxs-lookup"><span data-stu-id="40000-214">The `JSInvokable` attribute is applied to the method.</span></span>

<span data-ttu-id="40000-215">*Pages/jsinterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="40000-215">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="40000-216">JavaScript, das dem Client bereitgestellt C# wird, ruft die .NET-Methode auf</span><span class="sxs-lookup"><span data-stu-id="40000-216">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="40000-217">*wwwroot/examplejsinterop. js*:</span><span class="sxs-lookup"><span data-stu-id="40000-217">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="40000-218">Untersuchen Sie die Konsolenausgabe in den Webentwickler Tools des Browsers, wenn Sie die Schaltfläche " **.net static-Methode für rückgabetyasync** " aufrufen auswählen.</span><span class="sxs-lookup"><span data-stu-id="40000-218">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="40000-219">Die Konsolenausgabe sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="40000-219">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="40000-220">Der vierte Arraywert wird per pushübertragung an das von `ReturnArrayAsync`zurückgegebene Array (`data.push(4);`) übermittelt.</span><span class="sxs-lookup"><span data-stu-id="40000-220">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="40000-221">Standardmäßig ist der Methoden Bezeichner der Methodenname, aber Sie können einen anderen Bezeichner mit dem `JSInvokableAttribute`-Konstruktor angeben:</span><span class="sxs-lookup"><span data-stu-id="40000-221">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="40000-222">In der Client seitigen JavaScript-Datei:</span><span class="sxs-lookup"><span data-stu-id="40000-222">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('BlazorSample', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

### <a name="instance-method-call"></a><span data-ttu-id="40000-223">Instanzmethodenaufrufe</span><span class="sxs-lookup"><span data-stu-id="40000-223">Instance method call</span></span>

<span data-ttu-id="40000-224">Sie können auch .net-Instanzmethoden von JavaScript aus abrufen.</span><span class="sxs-lookup"><span data-stu-id="40000-224">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="40000-225">So rufen Sie eine .net-Instanzmethode aus JavaScript auf:</span><span class="sxs-lookup"><span data-stu-id="40000-225">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="40000-226">Übergeben Sie die .net-Instanz als Verweis an JavaScript:</span><span class="sxs-lookup"><span data-stu-id="40000-226">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="40000-227">Erstellen Sie einen statischen Aufruf`DotNetObjectReference.Create`.</span><span class="sxs-lookup"><span data-stu-id="40000-227">Make a static call to `DotNetObjectReference.Create`.</span></span>
  * <span data-ttu-id="40000-228">Wrappen Sie die Instanz in einer `DotNetObjectReference` Instanz, und nennen Sie `Create` auf der `DotNetObjectReference` Instanz.</span><span class="sxs-lookup"><span data-stu-id="40000-228">Wrap the instance in a `DotNetObjectReference` instance and call `Create` on the `DotNetObjectReference` instance.</span></span> <span data-ttu-id="40000-229">Verwerfen von `DotNetObjectReference` Objekten (ein Beispiel wird später in diesem Abschnitt angezeigt).</span><span class="sxs-lookup"><span data-stu-id="40000-229">Dispose of `DotNetObjectReference` objects (an example appears later in this section).</span></span>
* <span data-ttu-id="40000-230">Rufen Sie mithilfe der Funktionen `invokeMethod` oder `invokeMethodAsync` .net-Instanzmethoden für die-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="40000-230">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="40000-231">Die .net-Instanz kann auch als Argument beim Aufrufen anderer .NET-Methoden aus JavaScript übermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="40000-231">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="40000-232">Die Beispiel-App protokolliert Nachrichten in der Client seitigen Konsole.</span><span class="sxs-lookup"><span data-stu-id="40000-232">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="40000-233">Überprüfen Sie die Konsolenausgabe des Browsers in den Entwicklertools des Browsers auf die folgenden Beispiele, die von der Beispiel-App veranschaulicht werden.</span><span class="sxs-lookup"><span data-stu-id="40000-233">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="40000-234">Wenn die Schaltfläche **.net-Instanzmethode "hellohelper. SayHello" des Auslösers** aufgerufen wird, wird `ExampleJsInterop.CallHelloHelperSayHello` aufgerufen und übergibt einen Namen, `Blazor`, an die-Methode.</span><span class="sxs-lookup"><span data-stu-id="40000-234">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="40000-235">*Pages/jsinterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="40000-235">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="40000-236">`CallHelloHelperSayHello` Ruft die JavaScript-Funktion `sayHello` mit einer neuen Instanz von `HelloHelper`auf.</span><span class="sxs-lookup"><span data-stu-id="40000-236">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="40000-237">*Jsinteropclasses/examplejsinterop. cs*:</span><span class="sxs-lookup"><span data-stu-id="40000-237">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

<span data-ttu-id="40000-238">*wwwroot/examplejsinterop. js*:</span><span class="sxs-lookup"><span data-stu-id="40000-238">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="40000-239">Der Name wird an `HelloHelper`-Konstruktor übergeben, der die `HelloHelper.Name`-Eigenschaft festlegt.</span><span class="sxs-lookup"><span data-stu-id="40000-239">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="40000-240">Wenn die JavaScript-Funktion `sayHello` ausgeführt wird, gibt `HelloHelper.SayHello` die `Hello, {Name}!` Meldung zurück, die von der JavaScript-Funktion in die Konsole geschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="40000-240">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="40000-241">*Jsinteropclasses/hellohelper. cs*:</span><span class="sxs-lookup"><span data-stu-id="40000-241">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="40000-242">Konsolenausgabe in den Webentwickler Tools des Browsers:</span><span class="sxs-lookup"><span data-stu-id="40000-242">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

<span data-ttu-id="40000-243">Löschen Sie das Objekt in der Klasse, die die `DotNetObjectReference` Instanz erstellt hat, um einen Speicherplatz zu vermeiden und Garbage Collection in einer Komponente zuzulassen, von der ein `DotNetObjectReference`erstellt wird:</span><span class="sxs-lookup"><span data-stu-id="40000-243">To avoid a memory leak and allow garbage collection on a component that creates a `DotNetObjectReference`, dispose of the object in the class that created the `DotNetObjectReference` instance:</span></span>

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
  
<span data-ttu-id="40000-244">Das vorangehende Muster, das in der `ExampleJsInterop`-Klasse dargestellt wird, kann auch in einer-Komponente implementiert werden:</span><span class="sxs-lookup"><span data-stu-id="40000-244">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>
  
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

## <a name="share-interop-code-in-a-class-library"></a><span data-ttu-id="40000-245">Freigeben von Interop-Code in einer Klassenbibliothek</span><span class="sxs-lookup"><span data-stu-id="40000-245">Share interop code in a class library</span></span>

<span data-ttu-id="40000-246">Der JS-Interop-Code kann in einer Klassenbibliothek enthalten sein, die es Ihnen ermöglicht, den Code in einem nuget-Paket freizugeben.</span><span class="sxs-lookup"><span data-stu-id="40000-246">JS interop code can be included in a class library, which allows you to share the code in a NuGet package.</span></span>

<span data-ttu-id="40000-247">Die Klassenbibliothek behandelt das Einbetten von JavaScript-Ressourcen in der erstellten Assembly.</span><span class="sxs-lookup"><span data-stu-id="40000-247">The class library handles embedding JavaScript resources in the built assembly.</span></span> <span data-ttu-id="40000-248">Die JavaScript-Dateien werden in den Ordner " *wwwroot* " eingefügt.</span><span class="sxs-lookup"><span data-stu-id="40000-248">The JavaScript files are placed in the *wwwroot* folder.</span></span> <span data-ttu-id="40000-249">Die Tools kümmern sich um die Einbettung der Ressourcen, wenn die Bibliothek erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="40000-249">The tooling takes care of embedding the resources when the library is built.</span></span>

<span data-ttu-id="40000-250">Auf das integrierte nuget-Paket wird in der Projektdatei der APP genauso verwiesen, wie auf ein beliebiges nuget-Paket verwiesen wird.</span><span class="sxs-lookup"><span data-stu-id="40000-250">The built NuGet package is referenced in the app's project file the same way that any NuGet package is referenced.</span></span> <span data-ttu-id="40000-251">Nachdem das Paket wieder hergestellt wurde, kann der app-Code JavaScript so aufruft, C#als wäre es.</span><span class="sxs-lookup"><span data-stu-id="40000-251">After the package is restored, app code can call into JavaScript as if it were C#.</span></span>

<span data-ttu-id="40000-252">Weitere Informationen finden Sie unter <xref:blazor/class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="40000-252">For more information, see <xref:blazor/class-libraries>.</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="40000-253">Harden-js-Interop-Aufrufe</span><span class="sxs-lookup"><span data-stu-id="40000-253">Harden JS interop calls</span></span>

<span data-ttu-id="40000-254">Das js-Interop kann aufgrund von Netzwerkfehlern fehlschlagen und sollte als unzuverlässig behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="40000-254">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="40000-255">Standardmäßig wird für eine Blazor Server-App nach einer Minute ein Timeout für JS-Interop-Aufrufe auf dem Server verwendet.</span><span class="sxs-lookup"><span data-stu-id="40000-255">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="40000-256">Wenn eine APP einen aggressiveren Timeout (z. b. 10 Sekunden) tolerieren kann, legen Sie den Timeout mithilfe eines der folgenden Ansätze fest:</span><span class="sxs-lookup"><span data-stu-id="40000-256">If an app can tolerate a more aggressive timeout, such as 10 seconds, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="40000-257">Geben Sie in `Startup.ConfigureServices`Global das Timeout an:</span><span class="sxs-lookup"><span data-stu-id="40000-257">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="40000-258">Pro Aufruf im Komponenten Code kann ein einzelner Aufruf das Timeout angeben:</span><span class="sxs-lookup"><span data-stu-id="40000-258">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="40000-259">Weitere Informationen zur Ressourcenauslastung finden Sie unter <xref:security/blazor/server>.</span><span class="sxs-lookup"><span data-stu-id="40000-259">For more information on resource exhaustion, see <xref:security/blazor/server>.</span></span>

## <a name="perform-large-data-transfers-in-opno-locblazor-server-apps"></a><span data-ttu-id="40000-260">Ausführen umfangreicher Datenübertragungen in Blazor Server-apps</span><span class="sxs-lookup"><span data-stu-id="40000-260">Perform large data transfers in Blazor Server apps</span></span>

<span data-ttu-id="40000-261">In einigen Szenarien müssen große Datenmengen zwischen JavaScript und Blazorübertragen werden.</span><span class="sxs-lookup"><span data-stu-id="40000-261">In some scenarios, large amounts of data must be transferred between JavaScript and Blazor.</span></span> <span data-ttu-id="40000-262">In der Regel erfolgen große Datenübertragungen in folgenden Zeit Mengen:</span><span class="sxs-lookup"><span data-stu-id="40000-262">Typically, large data transfers occur when:</span></span>

* <span data-ttu-id="40000-263">Browser Dateisystem-APIs werden zum Hochladen oder Herunterladen einer Datei verwendet.</span><span class="sxs-lookup"><span data-stu-id="40000-263">Browser file system APIs are used to upload or download a file.</span></span>
* <span data-ttu-id="40000-264">Interop mit einer Drittanbieter Bibliothek ist erforderlich.</span><span class="sxs-lookup"><span data-stu-id="40000-264">Interop with a third party library is required.</span></span>

<span data-ttu-id="40000-265">In Blazor Server ist eine Einschränkung vorhanden, um zu verhindern, dass einzelne große Nachrichten übergeben werden, die zu Leistungsproblemen führen können.</span><span class="sxs-lookup"><span data-stu-id="40000-265">In Blazor Server, a limitation is in place to prevent passing single large messages that may result in performance issues.</span></span>

<span data-ttu-id="40000-266">Beachten Sie die folgenden Anleitungen, wenn Sie Code entwickeln, mit dem Daten zwischen JavaScript und Blazorübertragen werden:</span><span class="sxs-lookup"><span data-stu-id="40000-266">Consider the following guidance when developing code that transfers data between JavaScript and Blazor:</span></span>

* <span data-ttu-id="40000-267">Segmentieren Sie die Daten in kleinere Teile, und senden Sie die Daten Segmente sequenziell, bis alle Daten vom Server empfangen werden.</span><span class="sxs-lookup"><span data-stu-id="40000-267">Slice the data into smaller pieces, and send the data segments sequentially until all of the data is received by the server.</span></span>
* <span data-ttu-id="40000-268">Keine großen Objekte in JavaScript und C# Code zuordnen.</span><span class="sxs-lookup"><span data-stu-id="40000-268">Don't allocate large objects in JavaScript and C# code.</span></span>
* <span data-ttu-id="40000-269">Blockieren Sie den Haupt-UI-Thread nicht für lange Zeiträume, wenn Sie Daten senden oder empfangen.</span><span class="sxs-lookup"><span data-stu-id="40000-269">Don't block the main UI thread for long periods when sending or receiving data.</span></span>
* <span data-ttu-id="40000-270">Freien Arbeitsspeicher freigeben, wenn der Prozess abgeschlossen oder abgebrochen wird.</span><span class="sxs-lookup"><span data-stu-id="40000-270">Free any memory consumed when the process is completed or cancelled.</span></span>
* <span data-ttu-id="40000-271">Erzwingen Sie die folgenden zusätzlichen Anforderungen aus Sicherheitsgründen:</span><span class="sxs-lookup"><span data-stu-id="40000-271">Enforce the following additional requirements for security purposes:</span></span>
  * <span data-ttu-id="40000-272">Deklarieren Sie die maximale Datei-oder Datengröße, die übermittelt werden kann.</span><span class="sxs-lookup"><span data-stu-id="40000-272">Declare the maximum file or data size that can be passed.</span></span>
  * <span data-ttu-id="40000-273">Deklarieren Sie die minimale Uploadrate vom Client an den Server.</span><span class="sxs-lookup"><span data-stu-id="40000-273">Declare the minimum upload rate from the client to the server.</span></span>
* <span data-ttu-id="40000-274">Nachdem die Daten vom Server empfangen wurden, können die Daten wie folgt lauten:</span><span class="sxs-lookup"><span data-stu-id="40000-274">After the data is received by the server, the data can be:</span></span>
  * <span data-ttu-id="40000-275">Temporär in einem Arbeitsspeicher Puffer gespeichert, bis alle Segmente gesammelt wurden.</span><span class="sxs-lookup"><span data-stu-id="40000-275">Temporarily stored in a memory buffer until all of the segments are collected.</span></span>
  * <span data-ttu-id="40000-276">Wird sofort genutzt.</span><span class="sxs-lookup"><span data-stu-id="40000-276">Consumed immediately.</span></span> <span data-ttu-id="40000-277">Beispielsweise können die Daten sofort in einer Datenbank gespeichert oder auf den Datenträger geschrieben werden, wenn die einzelnen Segmente empfangen werden.</span><span class="sxs-lookup"><span data-stu-id="40000-277">For example, the data can be stored immediately in a database or written to disk as each segment is received.</span></span>

<span data-ttu-id="40000-278">Die folgende dateiuploader-Klasse verarbeitet js-Interop mit dem-Client.</span><span class="sxs-lookup"><span data-stu-id="40000-278">The following file uploader class handles JS interop with the client.</span></span> <span data-ttu-id="40000-279">Die Uploader-Klasse verwendet js-Interop für Folgendes:</span><span class="sxs-lookup"><span data-stu-id="40000-279">The uploader class uses JS interop to:</span></span>

* <span data-ttu-id="40000-280">Rufen Sie den Client ab, um ein Daten Segment zu senden.</span><span class="sxs-lookup"><span data-stu-id="40000-280">Poll the client to send a data segment.</span></span>
* <span data-ttu-id="40000-281">Abbrechen der Transaktion, wenn ein Abruf Timeout auftritt.</span><span class="sxs-lookup"><span data-stu-id="40000-281">Abort the transaction if polling times out.</span></span>

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
using Microsoft.JSInterop;

public class FileUploader : IDisposable
{
    private readonly IJSRuntime _jsRuntime;
    private readonly int _segmentSize = 6144;
    private readonly int _maxBase64SegmentSize = 8192;
    private readonly DotNetObjectReference<FileUploader> _thisReference;
    private List<IMemoryOwner<byte>> _uploadedSegments = 
        new List<IMemoryOwner<byte>>();

    public FileUploader(IJSRuntime jsRuntime)
    {
        _jsRuntime = jsRuntime;
    }

    public async Task<Stream> ReceiveFile(string selector, int maxSize)
    {
        var fileSize = 
            await _jsRuntime.InvokeAsync<int>("getFileSize", selector);

        if (fileSize > maxSize)
        {
            return null;
        }

        var numberOfSegments = Math.Floor(fileSize / (double)_segmentSize) + 1;
        var lastSegmentBytes = 0;
        string base64EncodedSegment;

        for (var i = 0; i < numberOfSegments; i++)
        {
            try
            {
                base64EncodedSegment = 
                    await _jsRuntime.InvokeAsync<string>(
                        "receiveSegment", i, selector);

                if (base64EncodedSegment.Length < _maxBase64SegmentSize && 
                    i < numberOfSegments - 1)
                {
                    return null;
                }
            }
            catch
            {
                return null;
            }

          var current = MemoryPool<byte>.Shared.Rent(_segmentSize);

          if (!Convert.TryFromBase64String(base64EncodedSegment, 
              current.Memory.Slice(0, _segmentSize).Span, out lastSegmentBytes))
          {
              return null;
          }

          _uploadedSegments.Add(current);
        }

        var segments = _uploadedSegments;
        _uploadedSegments = null;

        return new SegmentedStream(segments, _segmentSize, lastSegmentBytes);
    }

    public void Dispose()
    {
        if (_uploadedSegments != null)
        {
            foreach (var segment in _uploadedSegments)
            {
                segment.Dispose();
            }
        }
    }
}
```

<span data-ttu-id="40000-282">Im vorherigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="40000-282">In the preceding example:</span></span>

* <span data-ttu-id="40000-283">Der `_maxBase64SegmentSize` ist auf `8192`festgelegt, der aus `_maxBase64SegmentSize = _segmentSize * 4 / 3`berechnet wird.</span><span class="sxs-lookup"><span data-stu-id="40000-283">The `_maxBase64SegmentSize` is set to `8192`, which is calculated from `_maxBase64SegmentSize = _segmentSize * 4 / 3`.</span></span>
* <span data-ttu-id="40000-284">Low-Level-APIs für die Arbeitsspeicherverwaltung werden verwendet, um die Speichersegmente auf dem Server in `_uploadedSegments`zu speichern.</span><span class="sxs-lookup"><span data-stu-id="40000-284">Low level .NET Core memory management APIs are used to store the memory segments on the server in `_uploadedSegments`.</span></span>
* <span data-ttu-id="40000-285">Eine `ReceiveFile`-Methode wird verwendet, um den Upload über js-Interop zu behandeln:</span><span class="sxs-lookup"><span data-stu-id="40000-285">A `ReceiveFile` method is used to handle the upload through JS interop:</span></span>
  * <span data-ttu-id="40000-286">Die Dateigröße wird in Bytes über js-Interop mit `_jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`festgelegt.</span><span class="sxs-lookup"><span data-stu-id="40000-286">The file size is determined in bytes through JS interop with `_jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`.</span></span>
  * <span data-ttu-id="40000-287">Die Anzahl der zu empfangenden Segmente wird berechnet und in `numberOfSegments`gespeichert.</span><span class="sxs-lookup"><span data-stu-id="40000-287">The number of segments to receive are calculated and stored in `numberOfSegments`.</span></span>
  * <span data-ttu-id="40000-288">Die Segmente werden in einer `for` Schleife durch JS-Interop mit `_jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`angefordert.</span><span class="sxs-lookup"><span data-stu-id="40000-288">The segments are requested in a `for` loop through JS interop with `_jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`.</span></span> <span data-ttu-id="40000-289">Alle Segmente, aber die letzte müssen vor dem decodieren 8.192 Bytes betragen.</span><span class="sxs-lookup"><span data-stu-id="40000-289">All segments but the last must be 8,192 bytes before decoding.</span></span> <span data-ttu-id="40000-290">Der Client ist gezwungen, die Daten auf effiziente Weise zu senden.</span><span class="sxs-lookup"><span data-stu-id="40000-290">The client is forced to send the data in an efficient manner.</span></span>
  * <span data-ttu-id="40000-291">Für jedes empfangene Segment werden Überprüfungen vor der Decodierung mit <xref:System.Convert.TryFromBase64String*>ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="40000-291">For each segment received, checks are performed before decoding with <xref:System.Convert.TryFromBase64String*>.</span></span>
  * <span data-ttu-id="40000-292">Ein Datenstrom mit den Daten wird nach Abschluss des Uploads als neuer <xref:System.IO.Stream> (`SegmentedStream`) zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="40000-292">A stream with the data is returned as a new <xref:System.IO.Stream> (`SegmentedStream`) after the upload is complete.</span></span>

<span data-ttu-id="40000-293">Die segmentierte Stream-Klasse macht die Liste der Segmente als schreibgeschützte, nicht suchbare <xref:System.IO.Stream>verfügbar:</span><span class="sxs-lookup"><span data-stu-id="40000-293">The segmented stream class exposes the list of segments as a readonly non-seekable <xref:System.IO.Stream>:</span></span>

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;

public class SegmentedStream : Stream
{
    private readonly ReadOnlySequence<byte> _sequence;
    private long _currentPosition = 0;

    public SegmentedStream(IList<IMemoryOwner<byte>> segments, int segmentSize, 
        int lastSegmentSize)
    {
        if (segments.Count == 1)
        {
            _sequence = new ReadOnlySequence<byte>(
                segments[0].Memory.Slice(0, lastSegmentSize));
            return;
        }

        var sequenceSegment = new BufferSegment<byte>(
            segments[0].Memory.Slice(0, segmentSize));
        var lastSegment = sequenceSegment;

        for (int i = 1; i < segments.Count; i++)
        {
            var isLastSegment = i + 1 == segments.Count;
            lastSegment = lastSegment.Append(segments[i].Memory.Slice(
                0, isLastSegment ? lastSegmentSize : segmentSize));
        }

        _sequence = new ReadOnlySequence<byte>(
            sequenceSegment, 0, lastSegment, lastSegmentSize);
    }

    public override long Position
    {
        get => throw new NotImplementedException();
        set => throw new NotImplementedException();
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
        var bytesToWrite = (int)(_currentPosition + count < _sequence.Length ? 
            count : _sequence.Length - _currentPosition);
        var data = _sequence.Slice(_currentPosition, bytesToWrite);
        data.CopyTo(buffer.AsSpan(offset, bytesToWrite));
        _currentPosition += bytesToWrite;

        return bytesToWrite;
    }

    private class BufferSegment<T> : ReadOnlySequenceSegment<T>
    {
        public BufferSegment(ReadOnlyMemory<T> memory)
        {
            Memory = memory;
        }

        public BufferSegment<T> Append(ReadOnlyMemory<T> memory)
        {
            var segment = new BufferSegment<T>(memory)
            {
                RunningIndex = RunningIndex + Memory.Length
            };

            Next = segment;

            return segment;
        }
    }

    public override bool CanRead => true;

    public override bool CanSeek => false;

    public override bool CanWrite => false;

    public override long Length => throw new NotImplementedException();

    public override void Flush() => throw new NotImplementedException();

    public override long Seek(long offset, SeekOrigin origin) => 
        throw new NotImplementedException();

    public override void SetLength(long value) => 
        throw new NotImplementedException();

    public override void Write(byte[] buffer, int offset, int count) => 
        throw new NotImplementedException();
}
```

<span data-ttu-id="40000-294">Der folgende Code implementiert JavaScript-Funktionen, um die Daten zu empfangen:</span><span class="sxs-lookup"><span data-stu-id="40000-294">The following code implements JavaScript functions to receive the data:</span></span>

```javascript
function getFileSize(selector) {
  const file = getFile(selector);
  return file.size;
}

async function receiveSegment(segmentNumber, selector) {
  const file = getFile(selector);
  var segments = getFileSegments(file);
  var index = segmentNumber * 6144;
  return await getNextChunk(file, index);
}

function getFile(selector) {
  const element = document.querySelector(selector);
  if (!element) {
    throw new Error('Invalid selector');
  }
  const files = element.files;
  if (!files || files.length === 0) {
    throw new Error(`Element ${elementId} doesn't contain any files.`);
  }
  const file = files[0];
  return file;
}

function getFileSegments(file) {
  const segments = Math.floor(size % 6144 === 0 ? size / 6144 : 1 + size / 6144);
  return segments;
}

async function getNextChunk(file, index) {
  const length = file.size - index <= 6144 ? file.size - index : 6144;
  const chunk = file.slice(index, index + length);
  index += length;
  const base64Chunk = await this.base64EncodeAsync(chunk);
  return { base64Chunk, index };
}

async function base64EncodeAsync(chunk) {
  const reader = new FileReader();
  const result = new Promise((resolve, reject) => {
    reader.addEventListener('load',
      () => {
        const base64Chunk = reader.result;
        const cleanChunk = 
          base64Chunk.replace('data:application/octet-stream;base64,', '');
        resolve(cleanChunk);
      },
      false);
    reader.addEventListener('error', reject);
  });
  reader.readAsDataURL(chunk);
  return result;
}
```

## <a name="additional-resources"></a><span data-ttu-id="40000-295">Weitere Ressourcen</span><span class="sxs-lookup"><span data-stu-id="40000-295">Additional resources</span></span>

* [<span data-ttu-id="40000-296">Interopcomponent. Razor-Beispiel (dotnet/aspnetcore-GitHub-Repository, 3,1-releasebranch)</span><span class="sxs-lookup"><span data-stu-id="40000-296">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
