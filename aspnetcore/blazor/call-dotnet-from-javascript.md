---
title: Aufrufen von .NET-Methoden von JavaScript-Funktionen in ASP.NET Core Blazor
author: guardrex
description: In diesem Artikel erfahren Sie, wie Sie .NET-Methoden von JavaScript-Funktionen in Blazor-Apps aufrufen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: e2344dd15efd243a405373b6cf0362f28b48173a
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976949"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-opno-locblazor"></a><span data-ttu-id="e9770-103">Aufrufen von .NET-Methoden von JavaScript-Funktionen in ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="e9770-103">Call .NET methods from JavaScript functions in ASP.NET Core Blazor</span></span>

<span data-ttu-id="e9770-104">Von [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e9770-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co), and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="e9770-105">Eine Blazor-App kann JavaScript-Funktionen über .NET-Methoden und .NET-Methoden über JavaScript-Funktionen aufrufen.</span><span class="sxs-lookup"><span data-stu-id="e9770-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="e9770-106">Diese Szenarios werden als *JavaScript-Interoperabilität* (*JS Interop*) bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="e9770-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="e9770-107">In diesem Artikel wird das Aufrufen von .NET-Methoden über JavaScript behandelt.</span><span class="sxs-lookup"><span data-stu-id="e9770-107">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="e9770-108">Informationen zum Aufrufen von JavaScript-Funktionen über .NET finden Sie unter <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="e9770-108">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="e9770-109">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e9770-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="e9770-110">Statischer .NET-Methodenaufruf</span><span class="sxs-lookup"><span data-stu-id="e9770-110">Static .NET method call</span></span>

<span data-ttu-id="e9770-111">Um eine statische .NET-Methode über JavaScript aufzurufen, verwenden Sie die Funktionen `DotNet.invokeMethod` oder `DotNet.invokeMethodAsync`.</span><span class="sxs-lookup"><span data-stu-id="e9770-111">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="e9770-112">Übergeben Sie den Bezeichner der statischen Methode, die Sie aufrufen möchten, den Namen der Assembly, die die Funktion enthält, und alle Argumente.</span><span class="sxs-lookup"><span data-stu-id="e9770-112">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="e9770-113">Die asynchrone Version wird bevorzugt, um Blazor Server-Szenarien zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="e9770-113">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="e9770-114">Die .NET-Methode muss öffentlich und statisch sein sowie das Attribut `[JSInvokable]` aufweisen.</span><span class="sxs-lookup"><span data-stu-id="e9770-114">The .NET method must be public, static, and have the `[JSInvokable]` attribute.</span></span> <span data-ttu-id="e9770-115">Das Aufrufen offener generischer Methoden wird derzeit nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="e9770-115">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="e9770-116">Die Beispiel-App enthält eine C#-Methode zur Rückgabe eines `int`-Arrays.</span><span class="sxs-lookup"><span data-stu-id="e9770-116">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="e9770-117">Das `JSInvokable`-Attribut wird auf die Methode angewendet.</span><span class="sxs-lookup"><span data-stu-id="e9770-117">The `JSInvokable` attribute is applied to the method.</span></span>

<span data-ttu-id="e9770-118">*Pages/JsInterop.razor*:</span><span class="sxs-lookup"><span data-stu-id="e9770-118">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="e9770-119">Das dem Client zugestellte JavaScript ruft die C# .NET-Methode auf.</span><span class="sxs-lookup"><span data-stu-id="e9770-119">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="e9770-120">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="e9770-120">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="e9770-121">Wenn die Schaltfläche **Statische .NET-Methode ReturnArrayAsync auslösen** ausgewählt wird, untersuchen Sie die Konsolenausgabe in den Webentwicklertools des Browsers.</span><span class="sxs-lookup"><span data-stu-id="e9770-121">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="e9770-122">Die Konsolenausgabe lautet:</span><span class="sxs-lookup"><span data-stu-id="e9770-122">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="e9770-123">Der vierte Arraywert wird in das von `ReturnArrayAsync` zurückgegebene Array (`data.push(4);`) gepusht.</span><span class="sxs-lookup"><span data-stu-id="e9770-123">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="e9770-124">Standardmäßig ist der Methodenbezeichner der Methodenname, aber Sie können mit dem Konstruktor `JSInvokableAttribute` einen anderen Bezeichner angeben:</span><span class="sxs-lookup"><span data-stu-id="e9770-124">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="e9770-125">In der clientseitigen JavaScript-Datei:</span><span class="sxs-lookup"><span data-stu-id="e9770-125">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('BlazorSample', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

## <a name="instance-method-call"></a><span data-ttu-id="e9770-126">Aufruf der Instanzmethode</span><span class="sxs-lookup"><span data-stu-id="e9770-126">Instance method call</span></span>

<span data-ttu-id="e9770-127">Sie können auch .NET-Instanzmethoden von JavaScript aus aufrufen.</span><span class="sxs-lookup"><span data-stu-id="e9770-127">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="e9770-128">So rufen Sie .NET-Instanzmethoden von JavaScript aus auf</span><span class="sxs-lookup"><span data-stu-id="e9770-128">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="e9770-129">Übergeben Sie die .NET-Instanz durch Verweis auf JavaScript:</span><span class="sxs-lookup"><span data-stu-id="e9770-129">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="e9770-130">Führen Sie einen statischen Aufruf an `DotNetObjectReference.Create` durch.</span><span class="sxs-lookup"><span data-stu-id="e9770-130">Make a static call to `DotNetObjectReference.Create`.</span></span>
  * <span data-ttu-id="e9770-131">Umschließen Sie die Instanz mit einer `DotNetObjectReference`-Instanz, und rufen Sie `Create` für die `DotNetObjectReference`-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="e9770-131">Wrap the instance in a `DotNetObjectReference` instance and call `Create` on the `DotNetObjectReference` instance.</span></span> <span data-ttu-id="e9770-132">Entsorgen Sie `DotNetObjectReference`-Objekte (ein Beispiel folgt später in diesem Abschnitt).</span><span class="sxs-lookup"><span data-stu-id="e9770-132">Dispose of `DotNetObjectReference` objects (an example appears later in this section).</span></span>
* <span data-ttu-id="e9770-133">Rufen Sie .NET-Instanzmethoden für die Instanz mit den Funktionen `invokeMethod` oder `invokeMethodAsync` auf.</span><span class="sxs-lookup"><span data-stu-id="e9770-133">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="e9770-134">Die .NET-Instanz kann auch als Argument übergeben werden, wenn andere .NET-Methoden von JavaScript aus aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="e9770-134">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="e9770-135">Die Beispiel-App protokolliert Nachrichten an die clientseitige Konsole.</span><span class="sxs-lookup"><span data-stu-id="e9770-135">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="e9770-136">Für die folgenden Beispiele, die durch die Beispiel-App veranschaulicht werden, untersuchen Sie die Konsolenausgabe des Browsers in den Entwicklertools des Browsers.</span><span class="sxs-lookup"><span data-stu-id="e9770-136">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="e9770-137">Wenn die Schaltfläche **.NET-Instanzmethode HelloHelper.SayHello auslösen** ausgewählt wird, wird `ExampleJsInterop.CallHelloHelperSayHello` aufgerufen und ein Name, `Blazor`, an die Methode übergeben.</span><span class="sxs-lookup"><span data-stu-id="e9770-137">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="e9770-138">*Pages/JsInterop.razor*:</span><span class="sxs-lookup"><span data-stu-id="e9770-138">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="e9770-139">`CallHelloHelperSayHello` ruft die JavaScript-Funktion `sayHello` mit einer neuen Instanz von `HelloHelper` auf.</span><span class="sxs-lookup"><span data-stu-id="e9770-139">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="e9770-140">*JsInteropClasses/ExampleJsInterop.cs*:</span><span class="sxs-lookup"><span data-stu-id="e9770-140">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

<span data-ttu-id="e9770-141">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="e9770-141">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="e9770-142">Der Name wird an den Konstruktor von `HelloHelper` übergeben, der die Eigenschaft `HelloHelper.Name` festlegt.</span><span class="sxs-lookup"><span data-stu-id="e9770-142">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="e9770-143">Wenn die JavaScript-Funktion `sayHello` ausgeführt wird, gibt `HelloHelper.SayHello` die Nachricht `Hello, {Name}!` zurück, die von der JavaScript-Funktion in die Konsole geschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="e9770-143">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="e9770-144">*JsInteropClasses/HelloHelper.cs*:</span><span class="sxs-lookup"><span data-stu-id="e9770-144">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="e9770-145">Konsolenausgabe in den Webentwicklertools des Browsers:</span><span class="sxs-lookup"><span data-stu-id="e9770-145">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

<span data-ttu-id="e9770-146">Verwenden Sie einen der folgenden Ansätze, um einen Arbeitsspeicherverlust zu vermeiden und die Garbage Collection für eine Komponente zu ermöglichen, die ein `DotNetObjectReference` erstellt:</span><span class="sxs-lookup"><span data-stu-id="e9770-146">To avoid a memory leak and allow garbage collection on a component that creates a `DotNetObjectReference`, adopt one of the following approaches:</span></span>

* <span data-ttu-id="e9770-147">Löschen Sie das Objekt in der Klasse, die die `DotNetObjectReference`-Instanz erstellt hat:</span><span class="sxs-lookup"><span data-stu-id="e9770-147">Dispose of the object in the class that created the `DotNetObjectReference` instance:</span></span>

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

  <span data-ttu-id="e9770-148">Das vorhergehende Muster, das in der Klasse `ExampleJsInterop` gezeigt wird, kann auch in einer Komponente implementiert werden:</span><span class="sxs-lookup"><span data-stu-id="e9770-148">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>

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

* <span data-ttu-id="e9770-149">Wenn die Komponente oder Klasse die `DotNetObjectReference`-Instanz nicht löscht, löschen Sie das Objekt im Client, indem Sie `.dispose()` aufrufen:</span><span class="sxs-lookup"><span data-stu-id="e9770-149">When the component or class doesn't dispose of the `DotNetObjectReference`, dispose of the object on the client by calling `.dispose()`:</span></span>

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a><span data-ttu-id="e9770-150">Aufrufen der Komponenteninstanzmethode</span><span class="sxs-lookup"><span data-stu-id="e9770-150">Component instance method call</span></span>

<span data-ttu-id="e9770-151">So rufen Sie die .NET-Methoden einer Komponente auf:</span><span class="sxs-lookup"><span data-stu-id="e9770-151">To invoke a component's .NET methods:</span></span>

* <span data-ttu-id="e9770-152">Verwenden Sie die `invokeMethod`- oder `invokeMethodAsync`-Funktion, um einen statischen Methodenaufruf an die Komponente auszuführen.</span><span class="sxs-lookup"><span data-stu-id="e9770-152">Use the `invokeMethod` or `invokeMethodAsync` function to make a static method call to the component.</span></span>
* <span data-ttu-id="e9770-153">Die statische Methode der Komponente umschließt den Aufruf der Instanzmethode als aufgerufene `Action`.</span><span class="sxs-lookup"><span data-stu-id="e9770-153">The component's static method wraps the call to its instance method as an invoked `Action`.</span></span>

<span data-ttu-id="e9770-154">In der clientseitigen JavaScript-Datei:</span><span class="sxs-lookup"><span data-stu-id="e9770-154">In the client-side JavaScript:</span></span>

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethod('BlazorSample', 'UpdateMessageCaller');
}
```

<span data-ttu-id="e9770-155">*Pages/JSInteropComponent.razor:*</span><span class="sxs-lookup"><span data-stu-id="e9770-155">*Pages/JSInteropComponent.razor*:</span></span>

```razor
@page "/JSInteropComponent"

<p>
    Message: @_message
</p>

<p>
    <button onclick="updateMessageCallerJS()">Call JS Method</button>
</p>

@code {
    private static Action _action;
    private string _message = "Select the button.";

    protected override void OnInitialized()
    {
        _action = UpdateMessage;
    }

    private void UpdateMessage()
    {
        _message = "UpdateMessage Called!";
        StateHasChanged();
    }

    [JSInvokable]
    public static void UpdateMessageCaller()
    {
        _action.Invoke();
    }
}
```

<span data-ttu-id="e9770-156">Wenn mehrere Komponenten mit eigenen aufzurufenden Instanzmethoden vorliegen, verwenden Sie eine Hilfsklasse, um die Instanzmethoden aller Komponenten (als `Action`) aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="e9770-156">When there are several components, each with instance methods to call, use a helper class to invoke the instance methods (as `Action`s) of each component.</span></span>

<span data-ttu-id="e9770-157">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e9770-157">In the following example:</span></span>

* <span data-ttu-id="e9770-158">Die `JSInterop`-Komponente enthält mehrere `ListItem`-Komponenten.</span><span class="sxs-lookup"><span data-stu-id="e9770-158">The `JSInterop` component contains several `ListItem` components.</span></span>
* <span data-ttu-id="e9770-159">Alle `ListItem`-Komponenten bestehen aus einer Nachricht und einer Schaltfläche.</span><span class="sxs-lookup"><span data-stu-id="e9770-159">Each `ListItem` component is composed of a message and a button.</span></span>
* <span data-ttu-id="e9770-160">Wenn auf die Schaltfläche einer `ListItem`-Komponente geklickt wird, ändert die `UpdateMessage`-Methode des `ListItem`-Elements den Text des Listenelements und blendet die Schaltfläche aus.</span><span class="sxs-lookup"><span data-stu-id="e9770-160">When a `ListItem` component button is selected, that `ListItem`'s `UpdateMessage` method changes the list item text and hides the button.</span></span>

<span data-ttu-id="e9770-161">*MessageUpdateInvokeHelper.cs:*</span><span class="sxs-lookup"><span data-stu-id="e9770-161">*MessageUpdateInvokeHelper.cs*:</span></span>

```csharp
using System;
using Microsoft.JSInterop;

public class MessageUpdateInvokeHelper
{
    private Action _action;

    public MessageUpdateInvokeHelper(Action action)
    {
        _action = action;
    }

    [JSInvokable("BlazorSample")]
    public void UpdateMessageCaller()
    {
        _action.Invoke();
    }
}
```

<span data-ttu-id="e9770-162">In der clientseitigen JavaScript-Datei:</span><span class="sxs-lookup"><span data-stu-id="e9770-162">In the client-side JavaScript:</span></span>

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

<span data-ttu-id="e9770-163">*Shared/ListItem.razor:*</span><span class="sxs-lookup"><span data-stu-id="e9770-163">*Shared/ListItem.razor*:</span></span>

```razor
@inject IJSRuntime JsRuntime

<li>
    @_message
    <button @onclick="InteropCall" style="display:@_display">InteropCall</button>
</li>

@code {
    private string _message = "Select one of these list item buttons.";
    private string _display = "inline-block";
    private MessageUpdateInvokeHelper _messageUpdateInvokeHelper;

    protected override void OnInitialized()
    {
        _messageUpdateInvokeHelper = new MessageUpdateInvokeHelper(UpdateMessage);
    }

    protected async Task InteropCall()
    {
        await JsRuntime.InvokeVoidAsync("updateMessageCallerJS",
            DotNetObjectReference.Create(_messageUpdateInvokeHelper));
    }

    private void UpdateMessage()
    {
        _message = "UpdateMessage Called!";
        _display = "none";
        StateHasChanged();
    }
}
```

<span data-ttu-id="e9770-164">*Pages/JSInterop.razor*:</span><span class="sxs-lookup"><span data-stu-id="e9770-164">*Pages/JSInterop.razor*:</span></span>

```razor
@page "/JSInterop"

<h1>List of components</h1>

<ul>
    <ListItem />
    <ListItem />
    <ListItem />
    <ListItem />
</ul>
```

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="e9770-165">Vermeiden von Objektzirkelbezügen</span><span class="sxs-lookup"><span data-stu-id="e9770-165">Avoid circular object references</span></span>

<span data-ttu-id="e9770-166">Objekte, die Zirkelbezüge enthalten, können auf dem Client für folgende Vorgänge nicht serialisiert werden:</span><span class="sxs-lookup"><span data-stu-id="e9770-166">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="e9770-167">.NET-Methodenaufrufe.</span><span class="sxs-lookup"><span data-stu-id="e9770-167">.NET method calls.</span></span>
* <span data-ttu-id="e9770-168">JavaScript-Methodenaufrufe von C#, wenn der Rückgabetyp Zirkelbezüge enthält.</span><span class="sxs-lookup"><span data-stu-id="e9770-168">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="e9770-169">Weitere Informationen finden Sie unter den folgenden Problemen:</span><span class="sxs-lookup"><span data-stu-id="e9770-169">For more information, see the following issues:</span></span>

* [<span data-ttu-id="e9770-170">Zirkelbezüge werden nicht unterstützt, die Zweite (dotnet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="e9770-170">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="e9770-171">Vorschlag: Hinzufügen eines Mechanismus zur Verarbeitung von Zirkelbezügen bei der Serialisierung (dotnet/runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="e9770-171">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a><span data-ttu-id="e9770-172">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="e9770-172">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* [<span data-ttu-id="e9770-173">InteropComponent.razor-Beispiel (dotnet/AspNetCore-GitHub-Repository, Releasebranch 3.1)</span><span class="sxs-lookup"><span data-stu-id="e9770-173">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* <span data-ttu-id="e9770-174">[Ausführen umfangreicher Datenübertragungen in Blazor Server-Apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span><span class="sxs-lookup"><span data-stu-id="e9770-174">[Perform large data transfers in Blazor Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span></span>
