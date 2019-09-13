---
title: ASP.net Core blazor JavaScript-Interop
author: guardrex
description: Erfahren Sie, wie Sie JavaScript-Funktionen aus .net-und .NET-Methoden von JavaScript in blazor-apps aufrufen.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/07/2019
uid: blazor/javascript-interop
ms.openlocfilehash: 1572b9ee646577d094409cc33dd621f2f73dc863
ms.sourcegitcommit: 092061c4f6ef46ed2165fa84de6273d3786fb97e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2019
ms.locfileid: "70964204"
---
# <a name="aspnet-core-blazor-javascript-interop"></a><span data-ttu-id="d55e7-103">ASP.net Core blazor JavaScript-Interop</span><span class="sxs-lookup"><span data-stu-id="d55e7-103">ASP.NET Core Blazor JavaScript interop</span></span>

<span data-ttu-id="d55e7-104">Von [Javier calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27)und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d55e7-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d55e7-105">Eine blazor-App kann JavaScript-Funktionen aus .net-und .NET-Methoden aus JavaScript-Code aufrufen.</span><span class="sxs-lookup"><span data-stu-id="d55e7-105">A Blazor app can invoke JavaScript functions from .NET and .NET methods from JavaScript code.</span></span>

<span data-ttu-id="d55e7-106">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d55e7-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="invoke-javascript-functions-from-net-methods"></a><span data-ttu-id="d55e7-107">Aufrufen von JavaScript-Funktionen aus .NET-Methoden</span><span class="sxs-lookup"><span data-stu-id="d55e7-107">Invoke JavaScript functions from .NET methods</span></span>

<span data-ttu-id="d55e7-108">Es gibt Zeiten, in denen .NET-Code erforderlich ist, um eine JavaScript-Funktion aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="d55e7-108">There are times when .NET code is required to call a JavaScript function.</span></span> <span data-ttu-id="d55e7-109">Beispielsweise kann ein JavaScript-Befehl Browserfunktionen oder-Funktionen aus einer JavaScript-Bibliothek für die app verfügbar machen.</span><span class="sxs-lookup"><span data-stu-id="d55e7-109">For example, a JavaScript call can expose browser capabilities or functionality from a JavaScript library to the app.</span></span>

<span data-ttu-id="d55e7-110">Zum Abrufen von JavaScript aus .NET verwenden Sie die `IJSRuntime` Abstraktion.</span><span class="sxs-lookup"><span data-stu-id="d55e7-110">To call into JavaScript from .NET, use the `IJSRuntime` abstraction.</span></span> <span data-ttu-id="d55e7-111">Die `InvokeAsync<T>` -Methode nimmt einen Bezeichner für die JavaScript-Funktion an, die Sie zusammen mit einer beliebigen Anzahl von JSON-serialisierbaren Argumenten aufrufen möchten.</span><span class="sxs-lookup"><span data-stu-id="d55e7-111">The `InvokeAsync<T>` method takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="d55e7-112">Der Funktions Bezeichner ist relativ zum globalen Gültigkeits`window`Bereich ().</span><span class="sxs-lookup"><span data-stu-id="d55e7-112">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="d55e7-113">Wenn Sie anrufen `window.someScope.someFunction`möchten, ist `someScope.someFunction`der Bezeichner.</span><span class="sxs-lookup"><span data-stu-id="d55e7-113">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="d55e7-114">Es ist nicht erforderlich, die Funktion zu registrieren, bevor Sie aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="d55e7-114">There's no need to register the function before it's called.</span></span> <span data-ttu-id="d55e7-115">Der Rückgabetyp `T` muss auch JSON-serialisierbar sein.</span><span class="sxs-lookup"><span data-stu-id="d55e7-115">The return type `T` must also be JSON serializable.</span></span>

<span data-ttu-id="d55e7-116">Für blazor-Server-apps:</span><span class="sxs-lookup"><span data-stu-id="d55e7-116">For Blazor Server apps:</span></span>

* <span data-ttu-id="d55e7-117">Mehrere Benutzer Anforderungen werden von der blazor-Server-App verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="d55e7-117">Multiple user requests are processed by the Blazor Server app.</span></span> <span data-ttu-id="d55e7-118">Rufen Sie `JSRuntime.Current` nicht in einer Komponente auf, um JavaScript-Funktionen aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="d55e7-118">Don't call `JSRuntime.Current` in a component to invoke JavaScript functions.</span></span>
* <span data-ttu-id="d55e7-119">Fügen Sie `IJSRuntime` die Abstraktion ein, und verwenden Sie das injizierte Objekt, um JavaScript-Interop-Aufrufe auszugeben</span><span class="sxs-lookup"><span data-stu-id="d55e7-119">Inject the `IJSRuntime` abstraction and use the injected object to issue JavaScript interop calls.</span></span>
* <span data-ttu-id="d55e7-120">Während eine blazor-App vorab erstellt wird, ist das Aufrufen von JavaScript nicht möglich, da keine Verbindung mit dem Browser hergestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="d55e7-120">While a Blazor app is prerendering, calling into JavaScript isn't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="d55e7-121">Weitere Informationen finden Sie im Abschnitt [erkennen, wenn eine blazor-App eine Vorabversion ist](#detect-when-a-blazor-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="d55e7-121">For more information, see the [Detect when a Blazor app is prerendering](#detect-when-a-blazor-app-is-prerendering) section.</span></span>

<span data-ttu-id="d55e7-122">Das folgende Beispiel basiert auf [textdecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), einem experimentellen JavaScript-basierten Decoder.</span><span class="sxs-lookup"><span data-stu-id="d55e7-122">The following example is based on [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), an experimental JavaScript-based decoder.</span></span> <span data-ttu-id="d55e7-123">Im Beispiel wird veranschaulicht, wie eine JavaScript-Funktion aus C# einer Methode aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="d55e7-123">The example demonstrates how to invoke a JavaScript function from a C# method.</span></span> <span data-ttu-id="d55e7-124">Die JavaScript-Funktion akzeptiert ein Bytearray C# aus einer Methode, decodiert das Array und gibt den Text zur Anzeige an die Komponente zurück.</span><span class="sxs-lookup"><span data-stu-id="d55e7-124">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="d55e7-125">Geben Sie im `TextDecoder` - Elementvonwwwroot/Index.html(blazorWebassembly)oderPages/_Host.cshtml(blazorServer)eineFunktionan,dievonzumDecodiereneinesbestandenenArraysverwendet`<head>` wird:</span><span class="sxs-lookup"><span data-stu-id="d55e7-125">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a function that uses `TextDecoder` to decode a passed array:</span></span>

[!code-html[](javascript-interop/samples_snapshot/index-script.html)]

<span data-ttu-id="d55e7-126">JavaScript-Code, wie z. b. der im vorherigen Beispiel gezeigte Code, kann auch aus einer JavaScript-Datei ( *. js*) mit einem Verweis auf die Skriptdatei geladen werden:</span><span class="sxs-lookup"><span data-stu-id="d55e7-126">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (*.js*) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="d55e7-127">Folgende Komponente:</span><span class="sxs-lookup"><span data-stu-id="d55e7-127">The following component:</span></span>

* <span data-ttu-id="d55e7-128">Ruft die `ConvertArray` JavaScript-Funktion `JsRuntime` mit auf, wenn eine Komponenten Schaltfläche (**Array konvertieren**) ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="d55e7-128">Invokes the `ConvertArray` JavaScript function using `JsRuntime` when a component button (**Convert Array**) is selected.</span></span>
* <span data-ttu-id="d55e7-129">Nachdem die JavaScript-Funktion aufgerufen wurde, wird das bestandene Array in eine Zeichenfolge konvertiert.</span><span class="sxs-lookup"><span data-stu-id="d55e7-129">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="d55e7-130">Die Zeichenfolge wird zur Anzeige an die Komponente zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="d55e7-130">The string is returned to the component for display.</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

<span data-ttu-id="d55e7-131">Um die `IJSRuntime` Abstraktion zu verwenden, übernehmen Sie einen der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="d55e7-131">To use the `IJSRuntime` abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="d55e7-132">Fügen Sie `IJSRuntime` die Abstraktion in*die Razor-Komponente (Razor*-Komponente) ein:</span><span class="sxs-lookup"><span data-stu-id="d55e7-132">Inject the `IJSRuntime` abstraction into the Razor component (*.razor*):</span></span>

  [!code-cshtml[](javascript-interop/samples_snapshot/inject-abstraction.razor?highlight=1)]

* <span data-ttu-id="d55e7-133">Fügen Sie `IJSRuntime` die Abstraktion in eine Klasse ( *. cs*) ein:</span><span class="sxs-lookup"><span data-stu-id="d55e7-133">Inject the `IJSRuntime` abstraction into a class (*.cs*):</span></span>

  [!code-csharp[](javascript-interop/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

* <span data-ttu-id="d55e7-134">Verwenden Sie für die Generierung dynamischer Inhalte mit [buildrendertree](xref:blazor/components#manual-rendertreebuilder-logic)das `[Inject]` -Attribut:</span><span class="sxs-lookup"><span data-stu-id="d55e7-134">For dynamic content generation with [BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```csharp
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="d55e7-135">In der Client seitigen Beispiel-APP, die dieses Thema begleitet, sind zwei JavaScript-Funktionen für die app verfügbar, die mit dem Dom interagieren, um Benutzereingaben zu empfangen und eine Willkommensnachricht anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="d55e7-135">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="d55e7-136">`showPrompt`&ndash; Generiert eine Aufforderung zur Annahme von Benutzereingaben (Name des Benutzers) und gibt den Namen an den Aufrufer zurück.</span><span class="sxs-lookup"><span data-stu-id="d55e7-136">`showPrompt` &ndash; Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="d55e7-137">`displayWelcome`Weist dem Aufrufer eine Begrüßungs Meldung `id` mit dem-Objekt von `welcome`zu. &ndash;</span><span class="sxs-lookup"><span data-stu-id="d55e7-137">`displayWelcome` &ndash; Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="d55e7-138">*wwwroot/examplejsinterop. js*:</span><span class="sxs-lookup"><span data-stu-id="d55e7-138">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="d55e7-139">Platzieren Sie `<script>` das-Tag, das auf die JavaScript-Datei verweist, in der Datei *wwwroot/Index.html* (blazor Webassembly) oder der Datei *pages/_Host. cshtml* (blazor Server).</span><span class="sxs-lookup"><span data-stu-id="d55e7-139">Place the `<script>` tag that references the JavaScript file in the *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server).</span></span>

<span data-ttu-id="d55e7-140">*wwwroot/Index.html* (Blazor Webassembly):</span><span class="sxs-lookup"><span data-stu-id="d55e7-140">*wwwroot/index.html* (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorSample/wwwroot/index.html?highlight=15)]

<span data-ttu-id="d55e7-141">*Pages/_Host. cshtml* (Blazor-Server):</span><span class="sxs-lookup"><span data-stu-id="d55e7-141">*Pages/_Host.cshtml* (Blazor Server):</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/_Host.cshtml?highlight=29)]

<span data-ttu-id="d55e7-142">Platzieren `<script>` Sie kein Tag in einer Komponenten Datei, da `<script>` das Tag nicht dynamisch aktualisiert werden kann.</span><span class="sxs-lookup"><span data-stu-id="d55e7-142">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="d55e7-143">.NET-Methoden Interop mit den JavaScript-Funktionen in der Datei " *examplejsinterop. js* " durch Aufrufen `IJSRuntime.InvokeAsync<T>`von.</span><span class="sxs-lookup"><span data-stu-id="d55e7-143">.NET methods interop with the JavaScript functions in the *exampleJsInterop.js* file by calling `IJSRuntime.InvokeAsync<T>`.</span></span>

<span data-ttu-id="d55e7-144">Die `IJSRuntime` Abstraktion ist asynchron, um die Verwendung von blazor-Server Szenarios zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="d55e7-144">The `IJSRuntime` abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="d55e7-145">Wenn es sich bei der App um eine blazor Webassembly-App handelt und Sie eine JavaScript-Funktion synchron aufrufen möchten `IJSInProcessRuntime` , verwenden `Invoke<T>` Sie stattdessen Typumwandlung in, und rufen Sie stattdessen auf.</span><span class="sxs-lookup"><span data-stu-id="d55e7-145">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to `IJSInProcessRuntime` and call `Invoke<T>` instead.</span></span> <span data-ttu-id="d55e7-146">Es wird empfohlen, dass die meisten JavaScript-Interop-Bibliotheken die Async-APIs verwenden, um sicherzustellen, dass die Bibliotheken in allen Szenarien verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="d55e7-146">We recommend that most JavaScript interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

<span data-ttu-id="d55e7-147">Die Beispiel-app enthält eine Komponente zum Veranschaulichen der JavaScript-Interop.</span><span class="sxs-lookup"><span data-stu-id="d55e7-147">The sample app includes a component to demonstrate JavaScript interop.</span></span> <span data-ttu-id="d55e7-148">Die Komponente:</span><span class="sxs-lookup"><span data-stu-id="d55e7-148">The component:</span></span>

* <span data-ttu-id="d55e7-149">Empfängt Benutzereingaben über eine JavaScript-Eingabeaufforderung.</span><span class="sxs-lookup"><span data-stu-id="d55e7-149">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="d55e7-150">Gibt den Text zur Verarbeitung an die Komponente zurück.</span><span class="sxs-lookup"><span data-stu-id="d55e7-150">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="d55e7-151">Ruft eine zweite JavaScript-Funktion auf, die mit dem Dom interagiert, um eine Willkommensnachricht anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="d55e7-151">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="d55e7-152">*Pages/jsinterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="d55e7-152">*Pages/JSInterop.razor*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop1&highlight=3,19-21,23-25)]

1. <span data-ttu-id="d55e7-153">Wenn `TriggerJsPrompt` durch Auswählen der Schaltfläche JavaScript- **Eingabeaufforderung** für die Komponente ausgeführt wird `showPrompt` , wird die JavaScript-Funktion, die in der Datei *wwwroot/examplejsinterop. js* bereitgestellt wird, aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="d55e7-153">When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the JavaScript `showPrompt` function provided in the *wwwroot/exampleJsInterop.js* file is called.</span></span>
1. <span data-ttu-id="d55e7-154">Die `showPrompt` Funktion akzeptiert Benutzereingaben (den Namen des Benutzers), die HTML-codiert sind und an die Komponente zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="d55e7-154">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="d55e7-155">Die Komponente speichert den Benutzernamen in einer lokalen Variablen `name`.</span><span class="sxs-lookup"><span data-stu-id="d55e7-155">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="d55e7-156">Die in `name` gespeicherte Zeichenfolge wird in eine Begrüßungsnachricht eingebunden, die an eine JavaScript- `displayWelcome`Funktion,, die an eine-Überschriften Kennung gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="d55e7-156">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="d55e7-157">JavaScript-Funktion "void" aufruft</span><span class="sxs-lookup"><span data-stu-id="d55e7-157">Call a void JavaScript function</span></span>

<span data-ttu-id="d55e7-158">JavaScript-Funktionen, die [void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) oder nicht [definiert](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) zurück `IJSRuntime.InvokeAsync<object>`geben, werden `null`mit aufgerufen, das zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="d55e7-158">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with `IJSRuntime.InvokeAsync<object>`, which returns `null`.</span></span>

## <a name="detect-when-a-blazor-app-is-prerendering"></a><span data-ttu-id="d55e7-159">Erkennen, wenn eine blazor-App vorab durchgeführt wird</span><span class="sxs-lookup"><span data-stu-id="d55e7-159">Detect when a Blazor app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="d55e7-160">Verweise auf Elemente erfassen</span><span class="sxs-lookup"><span data-stu-id="d55e7-160">Capture references to elements</span></span>

<span data-ttu-id="d55e7-161">Einige [JavaScript-Interop](xref:blazor/javascript-interop) -Szenarien erfordern Verweise auf HTML-Elemente.</span><span class="sxs-lookup"><span data-stu-id="d55e7-161">Some [JavaScript interop](xref:blazor/javascript-interop) scenarios require references to HTML elements.</span></span> <span data-ttu-id="d55e7-162">Beispielsweise kann eine UI-Bibliothek einen Element Verweis für die Initialisierung erfordern, oder Sie müssen möglicherweise Befehls ähnliche APIs für ein Element, z `focus` . b. oder `play`, aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="d55e7-162">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="d55e7-163">Erfassen Sie mithilfe des folgenden Ansatzes Verweise auf HTML-Elemente in einer-Komponente:</span><span class="sxs-lookup"><span data-stu-id="d55e7-163">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="d55e7-164">Fügen Sie `@ref` dem HTML--Element ein-Attribut hinzu.</span><span class="sxs-lookup"><span data-stu-id="d55e7-164">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="d55e7-165">Definieren Sie ein Feld vom `ElementReference` Typ, dessen Name `@ref` mit dem Wert des-Attributs übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="d55e7-165">Define a field of type `ElementReference` whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="d55e7-166">Das folgende Beispiel zeigt, wie Sie einen Verweis `username` auf das `<input>` -Element erfassen:</span><span class="sxs-lookup"><span data-stu-id="d55e7-166">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```cshtml
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!NOTE]
> <span data-ttu-id="d55e7-167">Verwenden Sie **keine** aufgezeichneten Element Verweise als Methode zum Auffüllen oder Bearbeiten des DOM, wenn blazor mit den Elementen interagiert, auf die verwiesen wird.</span><span class="sxs-lookup"><span data-stu-id="d55e7-167">Do **not** use captured element references as a way of populating or manipulating the DOM when Blazor interacts with the elements referenced.</span></span> <span data-ttu-id="d55e7-168">Dadurch kann das deklarative Renderingmodell beeinträchtigt werden.</span><span class="sxs-lookup"><span data-stu-id="d55e7-168">Doing so may interfere with the declarative rendering model.</span></span>

<span data-ttu-id="d55e7-169">Wenn .NET-Code betroffen ist, ist ein `ElementReference` nicht transparentes Handle.</span><span class="sxs-lookup"><span data-stu-id="d55e7-169">As far as .NET code is concerned, an `ElementReference` is an opaque handle.</span></span> <span data-ttu-id="d55e7-170">Das *einzige* , was Sie tun `ElementReference` können, ist die Übergabe an JavaScript-Code über JavaScript-Interop.</span><span class="sxs-lookup"><span data-stu-id="d55e7-170">The *only* thing you can do with `ElementReference` is pass it through to JavaScript code via JavaScript interop.</span></span> <span data-ttu-id="d55e7-171">Wenn Sie dies tun, empfängt der JavaScript-seitige Code eine `HTMLElement` -Instanz, die für die Verwendung mit normalen DOM-APIs verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="d55e7-171">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="d55e7-172">Der folgende Code definiert z. b. eine .net-Erweiterungsmethode, die das Festlegen des Fokus auf ein Element ermöglicht:</span><span class="sxs-lookup"><span data-stu-id="d55e7-172">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="d55e7-173">*examplejsinterop. js*:</span><span class="sxs-lookup"><span data-stu-id="d55e7-173">*exampleJsInterop.js*:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="d55e7-174">Verwenden `IJSRuntime.InvokeAsync<T>` Sie`ElementReference` , `exampleJsFunctions.focusElement` und verwenden Sie, um ein Element zu fokussieren:</span><span class="sxs-lookup"><span data-stu-id="d55e7-174">Use `IJSRuntime.InvokeAsync<T>` and call `exampleJsFunctions.focusElement` with an `ElementReference` to focus an element:</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="d55e7-175">Wenn Sie eine Erweiterungsmethode verwenden möchten, um ein Element zu fokussieren, erstellen Sie eine statische `IJSRuntime` Erweiterungsmethode, die die-Instanz empfängt:</span><span class="sxs-lookup"><span data-stu-id="d55e7-175">To use an extension method to focus an element, create a static extension method that receives the `IJSRuntime` instance:</span></span>

```csharp
public static Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<object>(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="d55e7-176">Die-Methode wird direkt für das-Objekt aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="d55e7-176">The method is called directly on the object.</span></span> <span data-ttu-id="d55e7-177">Im folgenden Beispiel wird davon ausgegangen, `Focus` dass die statische-Methode `JsInteropClasses` im-Namespace verfügbar ist:</span><span class="sxs-lookup"><span data-stu-id="d55e7-177">The following example assumes that the static `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/component2.razor?highlight=1,4,12)]

> [!IMPORTANT]
> <span data-ttu-id="d55e7-178">Die `username` Variable wird erst aufgefüllt, nachdem die Komponente gerendert wurde.</span><span class="sxs-lookup"><span data-stu-id="d55e7-178">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="d55e7-179">Wenn eine nicht `ElementReference` aufgefüllte an JavaScript-Code übermittelt wird, erhält der JavaScript- `null`Code den Wert.</span><span class="sxs-lookup"><span data-stu-id="d55e7-179">If an unpopulated `ElementReference` is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="d55e7-180">Um Element Verweise zu manipulieren, nachdem die Komponente das Rendering abgeschlossen hat (um den anfänglichen Fokus auf ein Element fest `OnAfterRenderAsync` zulegen `OnAfterRender` ), verwenden Sie die-oder- [Komponenten Lebenszyklus Methoden](xref:blazor/components#lifecycle-methods).</span><span class="sxs-lookup"><span data-stu-id="d55e7-180">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the `OnAfterRenderAsync` or `OnAfterRender` [component lifecycle methods](xref:blazor/components#lifecycle-methods).</span></span>

## <a name="invoke-net-methods-from-javascript-functions"></a><span data-ttu-id="d55e7-181">Aufrufen von .NET-Methoden aus JavaScript-Funktionen</span><span class="sxs-lookup"><span data-stu-id="d55e7-181">Invoke .NET methods from JavaScript functions</span></span>

### <a name="static-net-method-call"></a><span data-ttu-id="d55e7-182">Statischer .net-Methodenaufrufe</span><span class="sxs-lookup"><span data-stu-id="d55e7-182">Static .NET method call</span></span>

<span data-ttu-id="d55e7-183">Zum Aufrufen einer statischen .NET-Methode aus JavaScript verwenden Sie `DotNet.invokeMethod` die `DotNet.invokeMethodAsync` -Funktion oder die-Funktion.</span><span class="sxs-lookup"><span data-stu-id="d55e7-183">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="d55e7-184">Übergeben Sie den Bezeichner der statischen Methode, die aufgerufen werden soll, den Namen der Assembly, die die Funktion enthält, und alle Argumente.</span><span class="sxs-lookup"><span data-stu-id="d55e7-184">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="d55e7-185">Die asynchrone Version ist für die Unterstützung von blazor-Server Szenarios bevorzugt.</span><span class="sxs-lookup"><span data-stu-id="d55e7-185">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="d55e7-186">Um eine .NET-Methode aus JavaScript aufzurufen, muss die .NET-Methode öffentlich und statisch sein und `[JSInvokable]` über das-Attribut verfügen.</span><span class="sxs-lookup"><span data-stu-id="d55e7-186">To invoke a .NET method from JavaScript, the .NET method must be public, static, and have the `[JSInvokable]` attribute.</span></span> <span data-ttu-id="d55e7-187">Standardmäßig ist der Methoden Bezeichner der Methodenname, aber Sie können einen anderen Bezeichner mit dem `JSInvokableAttribute` -Konstruktor angeben.</span><span class="sxs-lookup"><span data-stu-id="d55e7-187">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor.</span></span> <span data-ttu-id="d55e7-188">Das Aufrufen offener generischer Methoden wird derzeit nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="d55e7-188">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="d55e7-189">Die Beispiel-app enthält C# eine-Methode, um ein `int`Array von s zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="d55e7-189">The sample app includes a C# method to return an array of `int`s.</span></span> <span data-ttu-id="d55e7-190">Das `JSInvokable` -Attribut wird auf die-Methode angewendet.</span><span class="sxs-lookup"><span data-stu-id="d55e7-190">The `JSInvokable` attribute is applied to the method.</span></span>

<span data-ttu-id="d55e7-191">*Pages/jsinterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="d55e7-191">*Pages/JsInterop.razor*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop2&highlight=7-11)]

<span data-ttu-id="d55e7-192">JavaScript, das dem Client bereitgestellt C# wird, ruft die .NET-Methode auf</span><span class="sxs-lookup"><span data-stu-id="d55e7-192">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="d55e7-193">*wwwroot/examplejsinterop. js*:</span><span class="sxs-lookup"><span data-stu-id="d55e7-193">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="d55e7-194">Untersuchen Sie die Konsolenausgabe in den Webentwickler Tools des Browsers, wenn Sie die Schaltfläche " **.net static-Methode für rückgabetyasync** " aufrufen auswählen.</span><span class="sxs-lookup"><span data-stu-id="d55e7-194">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="d55e7-195">Die Konsolenausgabe sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="d55e7-195">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="d55e7-196">Der vierte Arraywert wird an das Array (`data.push(4);`) übermittelt `ReturnArrayAsync`, das von zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="d55e7-196">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

### <a name="instance-method-call"></a><span data-ttu-id="d55e7-197">Instanzmethodenaufrufe</span><span class="sxs-lookup"><span data-stu-id="d55e7-197">Instance method call</span></span>

<span data-ttu-id="d55e7-198">Sie können auch .net-Instanzmethoden von JavaScript aus abrufen.</span><span class="sxs-lookup"><span data-stu-id="d55e7-198">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="d55e7-199">So rufen Sie eine .net-Instanzmethode aus JavaScript auf:</span><span class="sxs-lookup"><span data-stu-id="d55e7-199">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="d55e7-200">Übergeben Sie die .net-Instanz in eine `DotNetObjectReference` -Instanz, um Sie an JavaScript zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="d55e7-200">Pass the .NET instance to JavaScript by wrapping it in a `DotNetObjectReference` instance.</span></span> <span data-ttu-id="d55e7-201">Die .net-Instanz wird als Verweis an JavaScript übermittelt.</span><span class="sxs-lookup"><span data-stu-id="d55e7-201">The .NET instance is passed by reference to JavaScript.</span></span>
* <span data-ttu-id="d55e7-202">Rufen Sie mithilfe der `invokeMethod` Funktionen oder `invokeMethodAsync` .net-Instanzmethoden für die-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="d55e7-202">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="d55e7-203">Die .net-Instanz kann auch als Argument beim Aufrufen anderer .NET-Methoden aus JavaScript übermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="d55e7-203">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="d55e7-204">Die Beispiel-App protokolliert Nachrichten in der Client seitigen Konsole.</span><span class="sxs-lookup"><span data-stu-id="d55e7-204">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="d55e7-205">Überprüfen Sie die Konsolenausgabe des Browsers in den Entwicklertools des Browsers auf die folgenden Beispiele, die von der Beispiel-App veranschaulicht werden.</span><span class="sxs-lookup"><span data-stu-id="d55e7-205">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="d55e7-206">Wenn die Schaltfläche **.net-Instanzmethode "hellohelper. SayHello" des Auslösers** aufgerufen wird `Blazor`, `ExampleJsInterop.CallHelloHelperSayHello` wird aufgerufen und übergibt den Namen an die-Methode.</span><span class="sxs-lookup"><span data-stu-id="d55e7-206">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="d55e7-207">*Pages/jsinterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="d55e7-207">*Pages/JsInterop.razor*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop3&highlight=8-9)]

<span data-ttu-id="d55e7-208">`CallHelloHelperSayHello`Ruft die JavaScript- `sayHello` Funktion mit einer neuen Instanz `HelloHelper`von auf.</span><span class="sxs-lookup"><span data-stu-id="d55e7-208">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="d55e7-209">*Jsinteropclasses/examplejsinterop. cs*:</span><span class="sxs-lookup"><span data-stu-id="d55e7-209">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

<span data-ttu-id="d55e7-210">*wwwroot/examplejsinterop. js*:</span><span class="sxs-lookup"><span data-stu-id="d55e7-210">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="d55e7-211">Der Name wird an `HelloHelper`den-Konstruktor übergeben, mit dem die `HelloHelper.Name` -Eigenschaft festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="d55e7-211">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="d55e7-212">Wenn die JavaScript- `sayHello` Funktion ausgeführt wird `HelloHelper.SayHello` , gibt `Hello, {Name}!` die Meldung zurück, die von der JavaScript-Funktion in die Konsole geschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="d55e7-212">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="d55e7-213">*Jsinteropclasses/hellohelper. cs*:</span><span class="sxs-lookup"><span data-stu-id="d55e7-213">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="d55e7-214">Konsolenausgabe in den Webentwickler Tools des Browsers:</span><span class="sxs-lookup"><span data-stu-id="d55e7-214">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

## <a name="share-interop-code-in-a-class-library"></a><span data-ttu-id="d55e7-215">Freigeben von Interop-Code in einer Klassenbibliothek</span><span class="sxs-lookup"><span data-stu-id="d55e7-215">Share interop code in a class library</span></span>

<span data-ttu-id="d55e7-216">JavaScript-Interop-Code kann in einer Klassenbibliothek enthalten sein, die es Ihnen ermöglicht, den Code in einem nuget-Paket freizugeben.</span><span class="sxs-lookup"><span data-stu-id="d55e7-216">JavaScript interop code can be included in a class library, which allows you to share the code in a NuGet package.</span></span>

<span data-ttu-id="d55e7-217">Die Klassenbibliothek behandelt das Einbetten von JavaScript-Ressourcen in der erstellten Assembly.</span><span class="sxs-lookup"><span data-stu-id="d55e7-217">The class library handles embedding JavaScript resources in the built assembly.</span></span> <span data-ttu-id="d55e7-218">Die JavaScript-Dateien werden in den Ordner " *wwwroot* " eingefügt.</span><span class="sxs-lookup"><span data-stu-id="d55e7-218">The JavaScript files are placed in the *wwwroot* folder.</span></span> <span data-ttu-id="d55e7-219">Die Tools kümmern sich um die Einbettung der Ressourcen, wenn die Bibliothek erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="d55e7-219">The tooling takes care of embedding the resources when the library is built.</span></span>

<span data-ttu-id="d55e7-220">Auf das integrierte nuget-Paket wird in der Projektdatei der APP genauso verwiesen, wie auf ein beliebiges nuget-Paket verwiesen wird.</span><span class="sxs-lookup"><span data-stu-id="d55e7-220">The built NuGet package is referenced in the app's project file the same way that any NuGet package is referenced.</span></span> <span data-ttu-id="d55e7-221">Nachdem das Paket wieder hergestellt wurde, kann der app-Code JavaScript so aufruft, C#als wäre es.</span><span class="sxs-lookup"><span data-stu-id="d55e7-221">After the package is restored, app code can call into JavaScript as if it were C#.</span></span>

<span data-ttu-id="d55e7-222">Weitere Informationen finden Sie unter <xref:blazor/class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="d55e7-222">For more information, see <xref:blazor/class-libraries>.</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="d55e7-223">Harden-js-Interop-Aufrufe</span><span class="sxs-lookup"><span data-stu-id="d55e7-223">Harden JS interop calls</span></span>

<span data-ttu-id="d55e7-224">Das js-Interop kann aufgrund von Netzwerkfehlern fehlschlagen und sollte als unzuverlässig behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="d55e7-224">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="d55e7-225">Standardmäßig wird für eine blazor-Server-App nach einer Minute ein Timeout für JS-Interop-Aufrufe auf dem Server verwendet.</span><span class="sxs-lookup"><span data-stu-id="d55e7-225">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="d55e7-226">Wenn eine APP einen aggressiveren Timeout (z. b. 10 Sekunden) tolerieren kann, legen Sie den Timeout mithilfe eines der folgenden Ansätze fest:</span><span class="sxs-lookup"><span data-stu-id="d55e7-226">If an app can tolerate a more aggressive timeout, such as 10 seconds, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="d55e7-227">Geben Sie `Startup.ConfigureServices`in Global das Timeout an:</span><span class="sxs-lookup"><span data-stu-id="d55e7-227">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="d55e7-228">Pro Aufruf im Komponenten Code kann ein einzelner Aufruf das Timeout angeben:</span><span class="sxs-lookup"><span data-stu-id="d55e7-228">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="d55e7-229">Weitere Informationen zur Ressourcenauslastung finden <xref:security/blazor/server>Sie unter.</span><span class="sxs-lookup"><span data-stu-id="d55e7-229">For more information on resource exhaustion, see <xref:security/blazor/server>.</span></span>
