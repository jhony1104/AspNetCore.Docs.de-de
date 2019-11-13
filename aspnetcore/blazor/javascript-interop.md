---
title: ASP.net Core Blazor JavaScript-Interop
author: guardrex
description: Erfahren Sie, wie Sie JavaScript-Funktionen aus .net-und .NET-Methoden aus JavaScript in Blazor-apps aufrufen.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/16/2019
no-loc:
- Blazor
uid: blazor/javascript-interop
ms.openlocfilehash: 76437ef00e00f5de1b995b4f0b1a09e5876dff8f
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73962843"
---
# <a name="aspnet-core-opno-locblazor-javascript-interop"></a><span data-ttu-id="96613-103">ASP.net Core Blazor JavaScript-Interop</span><span class="sxs-lookup"><span data-stu-id="96613-103">ASP.NET Core Blazor JavaScript interop</span></span>

<span data-ttu-id="96613-104">Von [Javier calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27)und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="96613-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="96613-105">Eine Blazor-App kann JavaScript-Funktionen von .net-und .NET-Methoden aus JavaScript-Code aufrufen.</span><span class="sxs-lookup"><span data-stu-id="96613-105">A Blazor app can invoke JavaScript functions from .NET and .NET methods from JavaScript code.</span></span>

<span data-ttu-id="96613-106">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="96613-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="invoke-javascript-functions-from-net-methods"></a><span data-ttu-id="96613-107">Aufrufen von JavaScript-Funktionen aus .NET-Methoden</span><span class="sxs-lookup"><span data-stu-id="96613-107">Invoke JavaScript functions from .NET methods</span></span>

<span data-ttu-id="96613-108">Es gibt Zeiten, in denen .NET-Code erforderlich ist, um eine JavaScript-Funktion aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="96613-108">There are times when .NET code is required to call a JavaScript function.</span></span> <span data-ttu-id="96613-109">Beispielsweise kann ein JavaScript-Befehl Browserfunktionen oder-Funktionen aus einer JavaScript-Bibliothek für die app verfügbar machen.</span><span class="sxs-lookup"><span data-stu-id="96613-109">For example, a JavaScript call can expose browser capabilities or functionality from a JavaScript library to the app.</span></span>

<span data-ttu-id="96613-110">Um JavaScript aus .net aufzurufen, verwenden Sie die `IJSRuntime`-Abstraktion.</span><span class="sxs-lookup"><span data-stu-id="96613-110">To call into JavaScript from .NET, use the `IJSRuntime` abstraction.</span></span> <span data-ttu-id="96613-111">Die `InvokeAsync<T>`-Methode nimmt einen Bezeichner für die JavaScript-Funktion an, die Sie zusammen mit einer beliebigen Anzahl von JSON-serialisierbaren Argumenten aufrufen möchten.</span><span class="sxs-lookup"><span data-stu-id="96613-111">The `InvokeAsync<T>` method takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="96613-112">Der Funktions Bezeichner ist relativ zum globalen Gültigkeitsbereich (`window`).</span><span class="sxs-lookup"><span data-stu-id="96613-112">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="96613-113">Wenn Sie `window.someScope.someFunction` anrufen möchten, ist der Bezeichner `someScope.someFunction`.</span><span class="sxs-lookup"><span data-stu-id="96613-113">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="96613-114">Es ist nicht erforderlich, die Funktion zu registrieren, bevor Sie aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="96613-114">There's no need to register the function before it's called.</span></span> <span data-ttu-id="96613-115">Der Rückgabetyp `T` muss ebenfalls JSON-serialisierbar sein.</span><span class="sxs-lookup"><span data-stu-id="96613-115">The return type `T` must also be JSON serializable.</span></span>

<span data-ttu-id="96613-116">Für Blazor Server-apps:</span><span class="sxs-lookup"><span data-stu-id="96613-116">For Blazor Server apps:</span></span>

* <span data-ttu-id="96613-117">Mehrere Benutzer Anforderungen werden von der Blazor Server-App verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="96613-117">Multiple user requests are processed by the Blazor Server app.</span></span> <span data-ttu-id="96613-118">Rufen Sie `JSRuntime.Current` nicht in einer Komponente auf, um JavaScript-Funktionen aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="96613-118">Don't call `JSRuntime.Current` in a component to invoke JavaScript functions.</span></span>
* <span data-ttu-id="96613-119">Fügen Sie die `IJSRuntime`-Abstraktion ein, und verwenden Sie das injizierte Objekt, um JavaScript-Interop-Aufrufe</span><span class="sxs-lookup"><span data-stu-id="96613-119">Inject the `IJSRuntime` abstraction and use the injected object to issue JavaScript interop calls.</span></span>
* <span data-ttu-id="96613-120">Während eine Blazor-App vorab erstellt wird, ist das Aufrufen von JavaScript nicht möglich, da keine Verbindung mit dem Browser hergestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="96613-120">While a Blazor app is prerendering, calling into JavaScript isn't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="96613-121">Weitere Informationen finden Sie im Abschnitt [erkennen, wenn eine Blazor-App eine Vorabversion ist](#detect-when-a-blazor-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="96613-121">For more information, see the [Detect when a Blazor app is prerendering](#detect-when-a-blazor-app-is-prerendering) section.</span></span>

<span data-ttu-id="96613-122">Das folgende Beispiel basiert auf [textdecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), einem experimentellen JavaScript-basierten Decoder.</span><span class="sxs-lookup"><span data-stu-id="96613-122">The following example is based on [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), an experimental JavaScript-based decoder.</span></span> <span data-ttu-id="96613-123">Im Beispiel wird veranschaulicht, wie eine JavaScript-Funktion aus C# einer Methode aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="96613-123">The example demonstrates how to invoke a JavaScript function from a C# method.</span></span> <span data-ttu-id="96613-124">Die JavaScript-Funktion akzeptiert ein Bytearray C# aus einer Methode, decodiert das Array und gibt den Text zur Anzeige an die Komponente zurück.</span><span class="sxs-lookup"><span data-stu-id="96613-124">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="96613-125">Geben Sie im `<head>`-Element von *wwwroot/Index.html* (Blazor Webassembly) oder *pages/_Host. cshtml* (Blazor Server) eine JavaScript-Funktion an, die `TextDecoder` zum Decodieren eines bestandenen Arrays verwendet und den decodierten Wert zurückgibt:</span><span class="sxs-lookup"><span data-stu-id="96613-125">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](javascript-interop/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="96613-126">JavaScript-Code, wie z. b. der im vorherigen Beispiel gezeigte Code, kann auch aus einer JavaScript-Datei ( *. js*) mit einem Verweis auf die Skriptdatei geladen werden:</span><span class="sxs-lookup"><span data-stu-id="96613-126">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (*.js*) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="96613-127">Folgende Komponente:</span><span class="sxs-lookup"><span data-stu-id="96613-127">The following component:</span></span>

* <span data-ttu-id="96613-128">Ruft die `convertArray` JavaScript-Funktion mithilfe von `JSRuntime` auf, wenn eine Komponenten Schaltfläche (**Array konvertieren**) ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="96613-128">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**Convert Array**) is selected.</span></span>
* <span data-ttu-id="96613-129">Nachdem die JavaScript-Funktion aufgerufen wurde, wird das bestandene Array in eine Zeichenfolge konvertiert.</span><span class="sxs-lookup"><span data-stu-id="96613-129">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="96613-130">Die Zeichenfolge wird zur Anzeige an die Komponente zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="96613-130">The string is returned to the component for display.</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

##  <a name="use-of-ijsruntime"></a><span data-ttu-id="96613-131">Verwendung von ijsruntime</span><span class="sxs-lookup"><span data-stu-id="96613-131">Use of IJSRuntime</span></span>

<span data-ttu-id="96613-132">Um die `IJSRuntime`-Abstraktion zu verwenden, übernehmen Sie einen der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="96613-132">To use the `IJSRuntime` abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="96613-133">Fügen Sie die `IJSRuntime`-Abstraktion in*die Razor-Komponente (Razor*) ein:</span><span class="sxs-lookup"><span data-stu-id="96613-133">Inject the `IJSRuntime` abstraction into the Razor component (*.razor*):</span></span>

  [!code-cshtml[](javascript-interop/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="96613-134">Geben Sie im `<head>`-Element von *wwwroot/Index.html* (Blazor Webassembly) oder *pages/_Host. cshtml* (Blazor Server) eine `handleTickerChanged` JavaScript-Funktion an.</span><span class="sxs-lookup"><span data-stu-id="96613-134">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="96613-135">Die-Funktion wird mit `IJSRuntime.InvokeVoidAsync` aufgerufen und gibt keinen Wert zurück:</span><span class="sxs-lookup"><span data-stu-id="96613-135">The function is called with `IJSRuntime.InvokeVoidAsync` and doesn't return a value:</span></span>

  [!code-html[](javascript-interop/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="96613-136">Fügen Sie die `IJSRuntime`-Abstraktion in eine Klasse ( *. cs*) ein:</span><span class="sxs-lookup"><span data-stu-id="96613-136">Inject the `IJSRuntime` abstraction into a class (*.cs*):</span></span>

  [!code-csharp[](javascript-interop/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="96613-137">Geben Sie im `<head>`-Element von *wwwroot/Index.html* (Blazor Webassembly) oder *pages/_Host. cshtml* (Blazor Server) eine `handleTickerChanged` JavaScript-Funktion an.</span><span class="sxs-lookup"><span data-stu-id="96613-137">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="96613-138">Die-Funktion wird mit `JSRuntime.InvokeAsync` aufgerufen und gibt einen Wert zurück:</span><span class="sxs-lookup"><span data-stu-id="96613-138">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](javascript-interop/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="96613-139">Verwenden Sie für die Generierung dynamischer Inhalte mit [buildrendertree](xref:blazor/components#manual-rendertreebuilder-logic)das `[Inject]`-Attribut:</span><span class="sxs-lookup"><span data-stu-id="96613-139">For dynamic content generation with [BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```csharp
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="96613-140">In der Client seitigen Beispiel-APP, die dieses Thema begleitet, sind zwei JavaScript-Funktionen für die app verfügbar, die mit dem Dom interagieren, um Benutzereingaben zu empfangen und eine Willkommensnachricht anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="96613-140">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="96613-141">`showPrompt` &ndash; generiert eine Aufforderung zur Annahme von Benutzereingaben (Name des Benutzers) und gibt den Namen an den Aufrufer zurück.</span><span class="sxs-lookup"><span data-stu-id="96613-141">`showPrompt` &ndash; Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="96613-142">`displayWelcome` &ndash; weist eine Begrüßungs Meldung vom Aufrufer einem DOM-Objekt mit einem `id` von `welcome` zu.</span><span class="sxs-lookup"><span data-stu-id="96613-142">`displayWelcome` &ndash; Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="96613-143">*wwwroot/examplejsinterop. js*:</span><span class="sxs-lookup"><span data-stu-id="96613-143">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="96613-144">Platzieren Sie das `<script>` Tag, das auf die JavaScript-Datei verweist, in der Datei *wwwroot/Index.html* (Blazor Webassembly) oder der Datei *pages/_Host. cshtml* (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="96613-144">Place the `<script>` tag that references the JavaScript file in the *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server).</span></span>

<span data-ttu-id="96613-145">*wwwroot/Index.html* (Blazor Webassembly):</span><span class="sxs-lookup"><span data-stu-id="96613-145">*wwwroot/index.html* (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=15)]

<span data-ttu-id="96613-146">*Pages/_Host. cshtml* (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="96613-146">*Pages/_Host.cshtml* (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=21)]

<span data-ttu-id="96613-147">Platzieren Sie kein `<script>`-Tag in einer Komponenten Datei, da das Tag "`<script>`" nicht dynamisch aktualisiert werden kann.</span><span class="sxs-lookup"><span data-stu-id="96613-147">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="96613-148">.NET-Methoden Interop mit den JavaScript-Funktionen in der Datei " *examplejsinterop. js* " durch Aufrufen von `IJSRuntime.InvokeAsync<T>`.</span><span class="sxs-lookup"><span data-stu-id="96613-148">.NET methods interop with the JavaScript functions in the *exampleJsInterop.js* file by calling `IJSRuntime.InvokeAsync<T>`.</span></span>

<span data-ttu-id="96613-149">Die `IJSRuntime` Abstraktion ist asynchron, um Blazor Server Szenarios zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="96613-149">The `IJSRuntime` abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="96613-150">Wenn es sich bei der App um eine Blazor Webassembly-App handelt und Sie eine JavaScript-Funktion synchron aufrufen möchten, können Sie eine Typumwandlung-Funktion in `IJSInProcessRuntime` und stattdessen `Invoke<T>` aufrufen.</span><span class="sxs-lookup"><span data-stu-id="96613-150">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to `IJSInProcessRuntime` and call `Invoke<T>` instead.</span></span> <span data-ttu-id="96613-151">Es wird empfohlen, dass die meisten JavaScript-Interop-Bibliotheken die Async-APIs verwenden, um sicherzustellen, dass die Bibliotheken in allen Szenarien verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="96613-151">We recommend that most JavaScript interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

<span data-ttu-id="96613-152">Die Beispiel-app enthält eine Komponente zum Veranschaulichen der JavaScript-Interop.</span><span class="sxs-lookup"><span data-stu-id="96613-152">The sample app includes a component to demonstrate JavaScript interop.</span></span> <span data-ttu-id="96613-153">Die Komponente:</span><span class="sxs-lookup"><span data-stu-id="96613-153">The component:</span></span>

* <span data-ttu-id="96613-154">Empfängt Benutzereingaben über eine JavaScript-Eingabeaufforderung.</span><span class="sxs-lookup"><span data-stu-id="96613-154">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="96613-155">Gibt den Text zur Verarbeitung an die Komponente zurück.</span><span class="sxs-lookup"><span data-stu-id="96613-155">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="96613-156">Ruft eine zweite JavaScript-Funktion auf, die mit dem Dom interagiert, um eine Willkommensnachricht anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="96613-156">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="96613-157">*Pages/jsinterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="96613-157">*Pages/JSInterop.razor*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorWebAssemblySample/Pages/JsInterop.razor?name=snippet_JSInterop1&highlight=3,19-21,23-25)]

1. <span data-ttu-id="96613-158">Wenn `TriggerJsPrompt` ausgeführt wird, indem die Schaltfläche **JavaScript-Eingabeaufforderung** der Komponente ausgewählt wird, wird die JavaScript-Funktion `showPrompt`, die in der Datei *wwwroot/examplejsinterop. js* bereitgestellt wird, aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="96613-158">When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the JavaScript `showPrompt` function provided in the *wwwroot/exampleJsInterop.js* file is called.</span></span>
1. <span data-ttu-id="96613-159">Die Funktion "`showPrompt`" akzeptiert Benutzereingaben (den Namen des Benutzers), die HTML-codiert sind und an die Komponente zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="96613-159">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="96613-160">Die Komponente speichert den Benutzernamen in einer lokalen Variablen, `name`.</span><span class="sxs-lookup"><span data-stu-id="96613-160">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="96613-161">Die in `name` gespeicherte Zeichenfolge wird in eine Begrüßungsnachricht eingebunden, die an eine JavaScript-Funktion, `displayWelcome`, übermittelt wird, die die Begrüßungsnachricht in ein Überlagerungs Kennzeichen rendert.</span><span class="sxs-lookup"><span data-stu-id="96613-161">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="96613-162">JavaScript-Funktion "void" aufruft</span><span class="sxs-lookup"><span data-stu-id="96613-162">Call a void JavaScript function</span></span>

<span data-ttu-id="96613-163">JavaScript-Funktionen, die [void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) oder nicht [definiert](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) zurückgeben, werden mit `IJSRuntime.InvokeVoidAsync` aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="96613-163">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with `IJSRuntime.InvokeVoidAsync`.</span></span>

## <a name="detect-when-a-opno-locblazor-app-is-prerendering"></a><span data-ttu-id="96613-164">Erkennen, wenn eine Blazor-App vorgestellt wird</span><span class="sxs-lookup"><span data-stu-id="96613-164">Detect when a Blazor app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="96613-165">Verweise auf Elemente erfassen</span><span class="sxs-lookup"><span data-stu-id="96613-165">Capture references to elements</span></span>

<span data-ttu-id="96613-166">Einige [JavaScript-Interop](xref:blazor/javascript-interop) -Szenarien erfordern Verweise auf HTML-Elemente.</span><span class="sxs-lookup"><span data-stu-id="96613-166">Some [JavaScript interop](xref:blazor/javascript-interop) scenarios require references to HTML elements.</span></span> <span data-ttu-id="96613-167">Beispielsweise kann eine UI-Bibliothek einen Element Verweis für die Initialisierung erfordern, oder Sie müssen möglicherweise Befehls ähnliche APIs für ein Element, z. b. `focus` oder `play`, aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="96613-167">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="96613-168">Erfassen Sie mithilfe des folgenden Ansatzes Verweise auf HTML-Elemente in einer-Komponente:</span><span class="sxs-lookup"><span data-stu-id="96613-168">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="96613-169">Fügen Sie dem HTML--Element ein `@ref`-Attribut hinzu.</span><span class="sxs-lookup"><span data-stu-id="96613-169">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="96613-170">Definieren Sie ein Feld vom Typ "`ElementReference`", dessen Name mit dem Wert des Attributs "`@ref`" übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="96613-170">Define a field of type `ElementReference` whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="96613-171">Das folgende Beispiel zeigt, wie Sie einen Verweis auf das `username`-`<input>`-Element erfassen:</span><span class="sxs-lookup"><span data-stu-id="96613-171">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```cshtml
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!NOTE]
> <span data-ttu-id="96613-172">Verwenden Sie **keine** aufgezeichneten Element Verweise als Möglichkeit zum Auffüllen des DOM.</span><span class="sxs-lookup"><span data-stu-id="96613-172">Do **not** use captured element references as a way of populating the DOM.</span></span> <span data-ttu-id="96613-173">Dadurch kann das deklarative Renderingmodell beeinträchtigt werden.</span><span class="sxs-lookup"><span data-stu-id="96613-173">Doing so may interfere with the declarative rendering model.</span></span>

<span data-ttu-id="96613-174">Wenn .NET-Code betroffen ist, ist ein `ElementReference` ein undurchsichtiges handle.</span><span class="sxs-lookup"><span data-stu-id="96613-174">As far as .NET code is concerned, an `ElementReference` is an opaque handle.</span></span> <span data-ttu-id="96613-175">Das *einzige* , was Sie mit `ElementReference` tun können, ist die Übergabe an JavaScript-Code über JavaScript-Interop.</span><span class="sxs-lookup"><span data-stu-id="96613-175">The *only* thing you can do with `ElementReference` is pass it through to JavaScript code via JavaScript interop.</span></span> <span data-ttu-id="96613-176">Wenn Sie dies tun, empfängt der JavaScript-seitige Code eine `HTMLElement`-Instanz, die mit normalen DOM-APIs verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="96613-176">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="96613-177">Der folgende Code definiert z. b. eine .net-Erweiterungsmethode, die das Festlegen des Fokus auf ein Element ermöglicht:</span><span class="sxs-lookup"><span data-stu-id="96613-177">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="96613-178">*examplejsinterop. js*:</span><span class="sxs-lookup"><span data-stu-id="96613-178">*exampleJsInterop.js*:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="96613-179">Verwenden Sie `IJSRuntime.InvokeAsync<T>`, und verwenden Sie `exampleJsFunctions.focusElement` mit einem `ElementReference`, um sich auf ein Element zu konzentrieren:</span><span class="sxs-lookup"><span data-stu-id="96613-179">Use `IJSRuntime.InvokeAsync<T>` and call `exampleJsFunctions.focusElement` with an `ElementReference` to focus an element:</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="96613-180">Wenn Sie eine Erweiterungsmethode verwenden möchten, um ein Element zu fokussieren, erstellen Sie eine statische Erweiterungsmethode, die die Instanz `IJSRuntime` empfängt:</span><span class="sxs-lookup"><span data-stu-id="96613-180">To use an extension method to focus an element, create a static extension method that receives the `IJSRuntime` instance:</span></span>

```csharp
public static Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<object>(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="96613-181">Die-Methode wird direkt für das-Objekt aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="96613-181">The method is called directly on the object.</span></span> <span data-ttu-id="96613-182">Im folgenden Beispiel wird davon ausgegangen, dass die statische `Focus`-Methode im `JsInteropClasses`-Namespace verfügbar ist:</span><span class="sxs-lookup"><span data-stu-id="96613-182">The following example assumes that the static `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/component2.razor?highlight=1,4,12)]

> [!IMPORTANT]
> <span data-ttu-id="96613-183">Die Variable "`username`" wird erst aufgefüllt, nachdem die Komponente gerendert wurde.</span><span class="sxs-lookup"><span data-stu-id="96613-183">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="96613-184">Wenn eine nicht aufgefüllte `ElementReference` an den JavaScript-Code übermittelt wird, erhält der JavaScript-Code den Wert `null`.</span><span class="sxs-lookup"><span data-stu-id="96613-184">If an unpopulated `ElementReference` is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="96613-185">Um Element Verweise zu bearbeiten, nachdem die Komponente das Rendering abgeschlossen hat (um den anfänglichen Fokus auf ein Element festzulegen), verwenden Sie die `OnAfterRenderAsync`-oder `OnAfterRender`- [Lebenszyklus Methoden der Komponente](xref:blazor/components#lifecycle-methods).</span><span class="sxs-lookup"><span data-stu-id="96613-185">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the `OnAfterRenderAsync` or `OnAfterRender` [component lifecycle methods](xref:blazor/components#lifecycle-methods).</span></span>

## <a name="invoke-net-methods-from-javascript-functions"></a><span data-ttu-id="96613-186">Aufrufen von .NET-Methoden aus JavaScript-Funktionen</span><span class="sxs-lookup"><span data-stu-id="96613-186">Invoke .NET methods from JavaScript functions</span></span>

### <a name="static-net-method-call"></a><span data-ttu-id="96613-187">Statischer .net-Methodenaufrufe</span><span class="sxs-lookup"><span data-stu-id="96613-187">Static .NET method call</span></span>

<span data-ttu-id="96613-188">Zum Aufrufen einer statischen .NET-Methode aus JavaScript verwenden Sie die Funktionen `DotNet.invokeMethod` oder `DotNet.invokeMethodAsync`.</span><span class="sxs-lookup"><span data-stu-id="96613-188">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="96613-189">Übergeben Sie den Bezeichner der statischen Methode, die aufgerufen werden soll, den Namen der Assembly, die die Funktion enthält, und alle Argumente.</span><span class="sxs-lookup"><span data-stu-id="96613-189">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="96613-190">Die asynchrone Version ist für die Unterstützung von Blazor Server-Szenarien bevorzugt.</span><span class="sxs-lookup"><span data-stu-id="96613-190">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="96613-191">Um eine .NET-Methode aus JavaScript aufzurufen, muss die .NET-Methode öffentlich und statisch sein und über das `[JSInvokable]`-Attribut verfügen.</span><span class="sxs-lookup"><span data-stu-id="96613-191">To invoke a .NET method from JavaScript, the .NET method must be public, static, and have the `[JSInvokable]` attribute.</span></span> <span data-ttu-id="96613-192">Standardmäßig ist der Methoden Bezeichner der Methodenname, aber Sie können einen anderen Bezeichner mit dem `JSInvokableAttribute`-Konstruktor angeben.</span><span class="sxs-lookup"><span data-stu-id="96613-192">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor.</span></span> <span data-ttu-id="96613-193">Das Aufrufen offener generischer Methoden wird derzeit nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="96613-193">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="96613-194">Die Beispiel-app enthält C# eine-Methode, um ein Array von `int`S zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="96613-194">The sample app includes a C# method to return an array of `int`s.</span></span> <span data-ttu-id="96613-195">Das `JSInvokable`-Attribut wird auf die-Methode angewendet.</span><span class="sxs-lookup"><span data-stu-id="96613-195">The `JSInvokable` attribute is applied to the method.</span></span>

<span data-ttu-id="96613-196">*Pages/jsinterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="96613-196">*Pages/JsInterop.razor*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorWebAssemblySample/Pages/JsInterop.razor?name=snippet_JSInterop2&highlight=7-11)]

<span data-ttu-id="96613-197">JavaScript, das dem Client bereitgestellt C# wird, ruft die .NET-Methode auf</span><span class="sxs-lookup"><span data-stu-id="96613-197">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="96613-198">*wwwroot/examplejsinterop. js*:</span><span class="sxs-lookup"><span data-stu-id="96613-198">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="96613-199">Untersuchen Sie die Konsolenausgabe in den Webentwickler Tools des Browsers, wenn Sie die Schaltfläche " **.net static-Methode für rückgabetyasync** " aufrufen auswählen.</span><span class="sxs-lookup"><span data-stu-id="96613-199">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="96613-200">Die Konsolenausgabe sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="96613-200">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="96613-201">Der vierte Arraywert wird per pushübertragung an das von `ReturnArrayAsync` zurückgegebene Array (`data.push(4);`) übermittelt.</span><span class="sxs-lookup"><span data-stu-id="96613-201">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

### <a name="instance-method-call"></a><span data-ttu-id="96613-202">Instanzmethodenaufrufe</span><span class="sxs-lookup"><span data-stu-id="96613-202">Instance method call</span></span>

<span data-ttu-id="96613-203">Sie können auch .net-Instanzmethoden von JavaScript aus abrufen.</span><span class="sxs-lookup"><span data-stu-id="96613-203">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="96613-204">So rufen Sie eine .net-Instanzmethode aus JavaScript auf:</span><span class="sxs-lookup"><span data-stu-id="96613-204">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="96613-205">Übergeben Sie die .net-Instanz an JavaScript, indem Sie Sie in einer `DotNetObjectReference`-Instanz Umpacken.</span><span class="sxs-lookup"><span data-stu-id="96613-205">Pass the .NET instance to JavaScript by wrapping it in a `DotNetObjectReference` instance.</span></span> <span data-ttu-id="96613-206">Die .net-Instanz wird als Verweis an JavaScript übermittelt.</span><span class="sxs-lookup"><span data-stu-id="96613-206">The .NET instance is passed by reference to JavaScript.</span></span>
* <span data-ttu-id="96613-207">Aufrufen von .net-Instanzmethoden auf der-Instanz mit den Funktionen `invokeMethod` oder `invokeMethodAsync`.</span><span class="sxs-lookup"><span data-stu-id="96613-207">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="96613-208">Die .net-Instanz kann auch als Argument beim Aufrufen anderer .NET-Methoden aus JavaScript übermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="96613-208">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="96613-209">Die Beispiel-App protokolliert Nachrichten in der Client seitigen Konsole.</span><span class="sxs-lookup"><span data-stu-id="96613-209">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="96613-210">Überprüfen Sie die Konsolenausgabe des Browsers in den Entwicklertools des Browsers auf die folgenden Beispiele, die von der Beispiel-App veranschaulicht werden.</span><span class="sxs-lookup"><span data-stu-id="96613-210">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="96613-211">Wenn die Schaltfläche **.net-Instanzmethode "hellohelper. SayHello" des Auslösers** aufgerufen wird, wird `ExampleJsInterop.CallHelloHelperSayHello` aufgerufen und übergibt einen Namen, `Blazor`, an die-Methode.</span><span class="sxs-lookup"><span data-stu-id="96613-211">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="96613-212">*Pages/jsinterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="96613-212">*Pages/JsInterop.razor*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorWebAssemblySample/Pages/JsInterop.razor?name=snippet_JSInterop3&highlight=8-9)]

<span data-ttu-id="96613-213">`CallHelloHelperSayHello` ruft die JavaScript-Funktion `sayHello` mit einer neuen Instanz von `HelloHelper` auf.</span><span class="sxs-lookup"><span data-stu-id="96613-213">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="96613-214">*Jsinteropclasses/examplejsinterop. cs*:</span><span class="sxs-lookup"><span data-stu-id="96613-214">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

<span data-ttu-id="96613-215">*wwwroot/examplejsinterop. js*:</span><span class="sxs-lookup"><span data-stu-id="96613-215">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="96613-216">Der Name wird an `HelloHelper`-Konstruktor übergeben, der die `HelloHelper.Name`-Eigenschaft festlegt.</span><span class="sxs-lookup"><span data-stu-id="96613-216">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="96613-217">Wenn die JavaScript-Funktion `sayHello` ausgeführt wird, gibt `HelloHelper.SayHello` die `Hello, {Name}!`-Nachricht zurück, die von der JavaScript-Funktion in die Konsole geschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="96613-217">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="96613-218">*Jsinteropclasses/hellohelper. cs*:</span><span class="sxs-lookup"><span data-stu-id="96613-218">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="96613-219">Konsolenausgabe in den Webentwickler Tools des Browsers:</span><span class="sxs-lookup"><span data-stu-id="96613-219">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

## <a name="share-interop-code-in-a-class-library"></a><span data-ttu-id="96613-220">Freigeben von Interop-Code in einer Klassenbibliothek</span><span class="sxs-lookup"><span data-stu-id="96613-220">Share interop code in a class library</span></span>

<span data-ttu-id="96613-221">JavaScript-Interop-Code kann in einer Klassenbibliothek enthalten sein, die es Ihnen ermöglicht, den Code in einem nuget-Paket freizugeben.</span><span class="sxs-lookup"><span data-stu-id="96613-221">JavaScript interop code can be included in a class library, which allows you to share the code in a NuGet package.</span></span>

<span data-ttu-id="96613-222">Die Klassenbibliothek behandelt das Einbetten von JavaScript-Ressourcen in der erstellten Assembly.</span><span class="sxs-lookup"><span data-stu-id="96613-222">The class library handles embedding JavaScript resources in the built assembly.</span></span> <span data-ttu-id="96613-223">Die JavaScript-Dateien werden in den Ordner " *wwwroot* " eingefügt.</span><span class="sxs-lookup"><span data-stu-id="96613-223">The JavaScript files are placed in the *wwwroot* folder.</span></span> <span data-ttu-id="96613-224">Die Tools kümmern sich um die Einbettung der Ressourcen, wenn die Bibliothek erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="96613-224">The tooling takes care of embedding the resources when the library is built.</span></span>

<span data-ttu-id="96613-225">Auf das integrierte nuget-Paket wird in der Projektdatei der APP genauso verwiesen, wie auf ein beliebiges nuget-Paket verwiesen wird.</span><span class="sxs-lookup"><span data-stu-id="96613-225">The built NuGet package is referenced in the app's project file the same way that any NuGet package is referenced.</span></span> <span data-ttu-id="96613-226">Nachdem das Paket wieder hergestellt wurde, kann der app-Code JavaScript so aufruft, C#als wäre es.</span><span class="sxs-lookup"><span data-stu-id="96613-226">After the package is restored, app code can call into JavaScript as if it were C#.</span></span>

<span data-ttu-id="96613-227">Weitere Informationen finden Sie unter <xref:blazor/class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="96613-227">For more information, see <xref:blazor/class-libraries>.</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="96613-228">Harden-js-Interop-Aufrufe</span><span class="sxs-lookup"><span data-stu-id="96613-228">Harden JS interop calls</span></span>

<span data-ttu-id="96613-229">Das js-Interop kann aufgrund von Netzwerkfehlern fehlschlagen und sollte als unzuverlässig behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="96613-229">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="96613-230">Standardmäßig wird für eine Blazor Server-App nach einer Minute ein Timeout für JS-Interop-Aufrufe auf dem Server verwendet.</span><span class="sxs-lookup"><span data-stu-id="96613-230">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="96613-231">Wenn eine APP einen aggressiveren Timeout (z. b. 10 Sekunden) tolerieren kann, legen Sie den Timeout mithilfe eines der folgenden Ansätze fest:</span><span class="sxs-lookup"><span data-stu-id="96613-231">If an app can tolerate a more aggressive timeout, such as 10 seconds, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="96613-232">Geben Sie in `Startup.ConfigureServices` Global das Timeout an:</span><span class="sxs-lookup"><span data-stu-id="96613-232">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="96613-233">Pro Aufruf im Komponenten Code kann ein einzelner Aufruf das Timeout angeben:</span><span class="sxs-lookup"><span data-stu-id="96613-233">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="96613-234">Weitere Informationen zur Ressourcenauslastung finden Sie unter <xref:security/blazor/server>.</span><span class="sxs-lookup"><span data-stu-id="96613-234">For more information on resource exhaustion, see <xref:security/blazor/server>.</span></span>
