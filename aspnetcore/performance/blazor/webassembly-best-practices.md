---
title: Bewährte Methoden für die ASP.net Core Blazor webassemblyleistung
author: pranavkm
description: Tipps zum Steigern der Leistung in ASP.net Core Blazor Webassembly-apps und vermeiden von häufigen Leistungsproblemen.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/08/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/blazor/webassembly-best-practices
ms.openlocfilehash: 950d87a6f09e998e47e96c93c5d68bb3f19ddafb
ms.sourcegitcommit: 74d80a36103fdbd54baba0118535a4647f511913
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/08/2020
ms.locfileid: "84529631"
---
# <a name="aspnet-core-blazor-webassembly-performance-best-practices"></a><span data-ttu-id="c5090-103">Bewährte Methoden für die ASP.net Core Blazor webassemblyleistung</span><span class="sxs-lookup"><span data-stu-id="c5090-103">ASP.NET Core Blazor WebAssembly performance best practices</span></span>

<span data-ttu-id="c5090-104">Von [Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="c5090-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="c5090-105">Dieser Artikel enthält Richtlinien für die Blazor bewährten Methoden der ASP.net Core Webassembly.</span><span class="sxs-lookup"><span data-stu-id="c5090-105">This article provides guidelines for ASP.NET Core Blazor WebAssembly performance best practices.</span></span>

## <a name="avoid-unnecessary-component-renders"></a><span data-ttu-id="c5090-106">Unnötige Komponenten Rendering vermeiden</span><span class="sxs-lookup"><span data-stu-id="c5090-106">Avoid unnecessary component renders</span></span>

Blazor<span data-ttu-id="c5090-107">der diffingalgorithmus vermeidet das erneute Ausführen einer Komponente, wenn der Algorithmus erkennt, dass sich die Komponente nicht geändert hat.</span><span class="sxs-lookup"><span data-stu-id="c5090-107">'s diffing algorithm avoids rerendering a component when the algorithm perceives that the component hasn't changed.</span></span> <span data-ttu-id="c5090-108">Überschreiben Sie, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> um das Komponenten Rendering genauer zu steuern.</span><span class="sxs-lookup"><span data-stu-id="c5090-108">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> for fine-grained control over component rendering.</span></span>

<span data-ttu-id="c5090-109">Wenn Sie eine nur für die Benutzeroberfläche geschützte Komponente erstellen, die sich nach dem anfänglichen Rendering nie ändert, konfigurieren <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> Sie für Folgendes `false` :</span><span class="sxs-lookup"><span data-stu-id="c5090-109">If authoring a UI-only component that never changes after the initial render, configure <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to return `false`:</span></span>

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

<span data-ttu-id="c5090-110">Die meisten apps benötigen keine differenzierte Kontrolle, können aber <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> auch verwendet werden, um eine Komponente selektiv zu erzeugen, die auf ein UI-Ereignis reagiert.</span><span class="sxs-lookup"><span data-stu-id="c5090-110">Most apps don't require fine-grained control, but <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> can also be used to selectively render a component responding to a UI event.</span></span>

<span data-ttu-id="c5090-111">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="c5090-111">In the following example:</span></span>

* <span data-ttu-id="c5090-112"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>wird überschrieben und auf den Wert des Felds festgelegt <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> , das anfänglich ist, `false` Wenn die Komponente geladen wird.</span><span class="sxs-lookup"><span data-stu-id="c5090-112"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden and set to the value of the <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> field, which is initially `false` when the component loads.</span></span>
* <span data-ttu-id="c5090-113">Wenn die Schaltfläche ausgewählt wird, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> wird auf festgelegt. Dadurch wird `true` die Komponente gezwungen, die Aktualisierung mit dem aktualisierten durchzusetzen `currentCount` .</span><span class="sxs-lookup"><span data-stu-id="c5090-113">When the button is selected, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is set to `true`, which forces the component to rerender with the updated `currentCount`.</span></span>
* <span data-ttu-id="c5090-114">Unmittelbar nach der erneuten Ausführung <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> legt den Wert von auf zurück, um die erneute Ausführung <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> bis zum `false` nächsten auswählen der Schaltfläche zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="c5090-114">Immediately after rerendering, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> sets the value of <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> back to `false` to prevent further rerendering until the next time the button is selected.</span></span>

```razor
<p>Current count: @currentCount</p>

<button @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;
    private bool shouldRender;

    protected override bool ShouldRender() => shouldRender;

    protected override void OnAfterRender(bool first)
    {
        shouldRender = false;
    }

    private void IncrementCount()
    {
        currentCount++;
        shouldRender = true;
    }
}
```

<span data-ttu-id="c5090-115">Weitere Informationen finden Sie unter <xref:blazor/lifecycle#after-component-render>.</span><span class="sxs-lookup"><span data-stu-id="c5090-115">For more information, see <xref:blazor/lifecycle#after-component-render>.</span></span>

## <a name="virtualize-re-usable-fragments"></a><span data-ttu-id="c5090-116">Wiederverwendbare Fragmente virtualisieren</span><span class="sxs-lookup"><span data-stu-id="c5090-116">Virtualize re-usable fragments</span></span>

<span data-ttu-id="c5090-117">Komponenten bieten einen einfachen Ansatz zum Erstellen wiederverwendbarer Code Fragmente und Markup.</span><span class="sxs-lookup"><span data-stu-id="c5090-117">Components offer a convenient approach to produce re-usable fragments of code and markup.</span></span> <span data-ttu-id="c5090-118">Im Allgemeinen empfiehlt es sich, individuelle Komponenten zu erstellen, die den Anforderungen der APP am besten entsprechen.</span><span class="sxs-lookup"><span data-stu-id="c5090-118">In general, we recommend authoring individual components that best align with the app's requirements.</span></span> <span data-ttu-id="c5090-119">Ein Nachteil ist, dass jede zusätzliche untergeordnete Komponente zur Gesamtzeit beiträgt, die zum Rendering einer übergeordneten Komponente benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="c5090-119">One caveat is that each additional child component contributes to the total time it takes to render a parent component.</span></span> <span data-ttu-id="c5090-120">Bei den meisten apps ist der zusätzliche mehr Aufwand unerheblich.</span><span class="sxs-lookup"><span data-stu-id="c5090-120">For most apps, the additional overhead is negligible.</span></span> <span data-ttu-id="c5090-121">Apps, die eine große Anzahl von Komponenten erzeugen, sollten die Verwendung von Strategien in Erwägung gezogen werden, um den Verarbeitungsaufwand zu reduzieren</span><span class="sxs-lookup"><span data-stu-id="c5090-121">Apps that produce a large number of components should consider using strategies to reduce processing overhead, such as limiting the number of rendered components.</span></span>

<span data-ttu-id="c5090-122">Beispielsweise ist ein Raster oder eine Liste, das Hunderte von Zeilen mit Komponenten rendert, Prozessor intensiv zum Rendern.</span><span class="sxs-lookup"><span data-stu-id="c5090-122">For example, a grid or list that renders hundreds of rows containing components is processor intensive to render.</span></span> <span data-ttu-id="c5090-123">Sie sollten eine Virtualisierung eines Raster-oder Listen Layouts in Erwägung gezogen, sodass nur eine Teilmenge der Komponenten zu einem beliebigen Zeitpunkt gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="c5090-123">Consider virtualizing a grid or list layout so that only a subset of the components is rendered at any given time.</span></span> <span data-ttu-id="c5090-124">Ein Beispiel für das Rendering von Komponenten Teilmengen finden Sie unter den folgenden Komponenten in der [Beispiel-App für Virtualisierung (ASPNET/Samples-GitHub-Repository)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span><span class="sxs-lookup"><span data-stu-id="c5090-124">For an example of component subset rendering, see the following components in the [Virtualization sample app (aspnet/samples GitHub repository)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span></span>

* <span data-ttu-id="c5090-125">`Virtualize`Component ([Shared/Virtualize. Razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): eine in c# geschriebene Komponente, <xref:Microsoft.AspNetCore.Components.ComponentBase> mit der implementiert wird, um einen Satz von Wetterdaten Zeilen basierend auf dem Benutzer Bildlauf zu erzeugen.</span><span class="sxs-lookup"><span data-stu-id="c5090-125">`Virtualize` component ([Shared/Virtualize.razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): A component written in C# that implements <xref:Microsoft.AspNetCore.Components.ComponentBase> to render a set of weather data rows based on user scrolling.</span></span>
* <span data-ttu-id="c5090-126">`FetchData`Component ([pages/fetchdata. Razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): verwendet die- `Virtualize` Komponente, um 25 Zeilen von Wetterdaten gleichzeitig anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="c5090-126">`FetchData` component ([Pages/FetchData.razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): Uses the `Virtualize` component to display 25 rows of weather data at a time.</span></span>

## <a name="avoid-javascript-interop-to-marshal-data"></a><span data-ttu-id="c5090-127">Vermeiden von JavaScript-Interop zum Mars Hallen von Daten</span><span class="sxs-lookup"><span data-stu-id="c5090-127">Avoid JavaScript interop to marshal data</span></span>

<span data-ttu-id="c5090-128">In Blazor Webassembly muss ein JavaScript (JS)-Interop-Befehl die Webassembly-js-Grenze überschreiten.</span><span class="sxs-lookup"><span data-stu-id="c5090-128">In Blazor WebAssembly, a JavaScript (JS) interop call must traverse the WebAssembly-JS boundary.</span></span> <span data-ttu-id="c5090-129">Durch das Serialisieren und Deserialisieren von Inhalten in den beiden Kontexten wird der Verarbeitungsaufwand für die app erzeugt.</span><span class="sxs-lookup"><span data-stu-id="c5090-129">Serializing and deserializing content across the two contexts creates processing overhead for the app.</span></span> <span data-ttu-id="c5090-130">Häufige js-Interop-Aufrufe beeinträchtigen häufig die Leistung.</span><span class="sxs-lookup"><span data-stu-id="c5090-130">Frequent JS interop calls often adversely affects performance.</span></span> <span data-ttu-id="c5090-131">Um das Marshalling von Daten über die Grenze zu reduzieren, stellen Sie fest, ob die APP viele kleine Nutzlasten in einer einzelnen großen Nutzlast konsolidieren kann, um die große Menge an Kontext wechseln zwischen Webassembly und JS zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="c5090-131">To reduce the marshalling of data across the boundary, determine if the app can consolidate many small payloads into a single large payload to avoid the high volume of context switching between WebAssembly and JS.</span></span>

## <a name="use-systemtextjson"></a><span data-ttu-id="c5090-132">Verwenden von "System. Text. JSON"</span><span class="sxs-lookup"><span data-stu-id="c5090-132">Use System.Text.Json</span></span>

Blazor<span data-ttu-id="c5090-133">die JS-Interop-Implementierung basiert auf, bei der es sich <xref:System.Text.Json> um eine leistungsstarke JSON-serialisierungsbibliothek mit geringer Speicher Belegung handelt.</span><span class="sxs-lookup"><span data-stu-id="c5090-133">'s JS interop implementation relies on <xref:System.Text.Json>, which is a high-performance JSON serialization library with low memory allocation.</span></span> <span data-ttu-id="c5090-134">Die Verwendung <xref:System.Text.Json> von führt nicht zu einer zusätzlichen App-Nutzlastgröße beim Hinzufügen einer oder mehrerer alternativer JSON-Bibliotheken.</span><span class="sxs-lookup"><span data-stu-id="c5090-134">Using <xref:System.Text.Json> doesn't result in additional app payload size over adding one or more alternate JSON libraries.</span></span>

<span data-ttu-id="c5090-135">Eine Migrations Anleitung finden [Sie unter Migrieren von "newtonsoft. JSON" zu "System. Text. JSON](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to)".</span><span class="sxs-lookup"><span data-stu-id="c5090-135">For migration guidance, see [How to migrate from Newtonsoft.Json to System.Text.Json](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span></span>

## <a name="use-synchronous-and-unmarshalled-js-interop-apis-where-appropriate"></a><span data-ttu-id="c5090-136">Verwenden Sie gegebenenfalls synchrone und ungemarshallte js-Interop-APIs.</span><span class="sxs-lookup"><span data-stu-id="c5090-136">Use synchronous and unmarshalled JS interop APIs where appropriate</span></span>

Blazor<span data-ttu-id="c5090-137">Webassembly bietet zwei weitere Versionen von <xref:Microsoft.JSInterop.IJSRuntime> über der einzelnen Version, die für Server-apps verfügbar ist Blazor :</span><span class="sxs-lookup"><span data-stu-id="c5090-137"> WebAssembly offers two additional versions of <xref:Microsoft.JSInterop.IJSRuntime> over the single version available to Blazor Server apps:</span></span>

* <span data-ttu-id="c5090-138"><xref:Microsoft.JSInterop.IJSInProcessRuntime>ermöglicht das synchrone Aufrufen von JS-Interop-aufrufen, was weniger Aufwand als die asynchronen Versionen hat:</span><span class="sxs-lookup"><span data-stu-id="c5090-138"><xref:Microsoft.JSInterop.IJSInProcessRuntime> allows invoking JS interop calls synchronously, which has less overhead than the asynchronous versions:</span></span>

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (IJSInProcessRuntime)JS;

          var value = jsInProcess.Invoke<string>("jsInteropCall");
      }
  }
  ```

* <span data-ttu-id="c5090-139"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime>lässt nicht marshallte js-Interop-Aufrufe zu:</span><span class="sxs-lookup"><span data-stu-id="c5090-139"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> permits unmarshalled JS interop calls:</span></span>

  ```javascript
  function jsInteropCall() {
    return BINDING.js_to_mono_obj("Hello world");
  }
  ```

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (WebAssemblyJSRuntime)JS;

          var value = jsInProcess.InvokeUnmarshalled<string>("jsInteropCall");
      }
  }
  ```

  > [!WARNING]
  > <span data-ttu-id="c5090-140">Bei der Verwendung <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> von ist der geringste mehr Aufwand für die JS-Interop-Ansätze erforderlich, aber die JavaScript-APIs, die für die Interaktion mit diesen APIs erforderlich sind, sind zurzeit nicht dokumentiert und unterliegen in zukünftigen Versionen</span><span class="sxs-lookup"><span data-stu-id="c5090-140">While using <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> has the least overhead of the JS interop approaches, the JavaScript APIs required to interact with these APIs are currently undocumented and subject to breaking changes in future releases.</span></span>

## <a name="reduce-app-size"></a><span data-ttu-id="c5090-141">Reduzieren der APP-Größe</span><span class="sxs-lookup"><span data-stu-id="c5090-141">Reduce app size</span></span>

### <a name="intermediate-language-il-linking"></a><span data-ttu-id="c5090-142">Intermediate Language (IL)-Verknüpfung</span><span class="sxs-lookup"><span data-stu-id="c5090-142">Intermediate Language (IL) linking</span></span>

<span data-ttu-id="c5090-143">[Verknüpfen eines Blazor Webassembly-App](xref:host-and-deploy/blazor/configure-linker) reduziert die Größe der APP, indem nicht verwendeter Code in den Binärdateien der APP gekürzt wird.</span><span class="sxs-lookup"><span data-stu-id="c5090-143">[Linking a Blazor WebAssembly app](xref:host-and-deploy/blazor/configure-linker) reduces the app's size by trimming unused code in the app's binaries.</span></span> <span data-ttu-id="c5090-144">Standardmäßig ist der Linker nur beim Aufbau in der `Release` Konfiguration aktiviert.</span><span class="sxs-lookup"><span data-stu-id="c5090-144">By default, the linker is only enabled when building in `Release` configuration.</span></span> <span data-ttu-id="c5090-145">Um davon zu profitieren, veröffentlichen Sie die APP für die Bereitstellung mit dem Befehl " [dotnet Publish](/dotnet/core/tools/dotnet-publish) ", wobei die Option [-c |--Configuration](/dotnet/core/tools/dotnet-publish#options) auf festgelegt ist `Release` :</span><span class="sxs-lookup"><span data-stu-id="c5090-145">To benefit from this, publish the app for deployment using the [dotnet publish](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

```dotnetcli
dotnet publish -c Release
```

### <a name="compression"></a><span data-ttu-id="c5090-146">Komprimierung</span><span class="sxs-lookup"><span data-stu-id="c5090-146">Compression</span></span>

<span data-ttu-id="c5090-147">Wenn eine Blazor Webassembly-App veröffentlicht wird, wird die Ausgabe statisch während der Veröffentlichung komprimiert, um die Größe der APP zu reduzieren und den mehr Aufwand für die Lauf Zeit Komprimierung zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="c5090-147">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> Blazor<span data-ttu-id="c5090-148">basiert auf dem-Server, um die Inhalts Erfüllung auszuführen und statisch komprimierte Dateien zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="c5090-148"> relies on the server to perform content negotation and serve statically-compressed files.</span></span>

<span data-ttu-id="c5090-149">Nachdem eine APP bereitgestellt wurde, überprüfen Sie, ob die APP komprimierte Dateien bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="c5090-149">After an app is deployed, verify that the app serves compressed files.</span></span> <span data-ttu-id="c5090-150">Überprüfen Sie die Registerkarte Netzwerk im Entwicklertools eines Browsers, und überprüfen Sie, ob die Dateien mit oder bedient werden `Content-Encoding: br` `Content-Encoding: gz` .</span><span class="sxs-lookup"><span data-stu-id="c5090-150">Inspect the Network tab in a browser's Developer Tools and verify that the files are served with `Content-Encoding: br` or `Content-Encoding: gz`.</span></span> <span data-ttu-id="c5090-151">Wenn der Host keine komprimierten Dateien bedient, befolgen Sie die Anweisungen unter <xref:host-and-deploy/blazor/webassembly#compression> .</span><span class="sxs-lookup"><span data-stu-id="c5090-151">If the host isn't serving compressed files, follow the instructions in <xref:host-and-deploy/blazor/webassembly#compression>.</span></span>

### <a name="disable-unused-features"></a><span data-ttu-id="c5090-152">Nicht verwendete Funktionen deaktivieren</span><span class="sxs-lookup"><span data-stu-id="c5090-152">Disable unused features</span></span>

Blazor<span data-ttu-id="c5090-153">Die Laufzeit der Webassembly umfasst die folgenden .NET-Funktionen, die deaktiviert werden können, wenn die APP Sie nicht für eine geringere Nutzlastgröße benötigt:</span><span class="sxs-lookup"><span data-stu-id="c5090-153"> WebAssembly's runtime includes the following .NET features that can be disabled if the app doesn't require them for a smaller payload size:</span></span>

* <span data-ttu-id="c5090-154">Eine Datendatei ist enthalten, um Zeitzoneninformationen korrekt zu machen.</span><span class="sxs-lookup"><span data-stu-id="c5090-154">A data file is included to make timezone information correct.</span></span> <span data-ttu-id="c5090-155">Wenn die APP diese Funktion nicht benötigt, können Sie Sie deaktivieren, indem Sie die `BlazorEnableTimeZoneSupport` MSBuild-Eigenschaft in der Projektdatei der APP auf Folgendes festlegen `false` :</span><span class="sxs-lookup"><span data-stu-id="c5090-155">If the app doesn't require this feature, consider disabling it by setting the `BlazorEnableTimeZoneSupport` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

* <span data-ttu-id="c5090-156">Sortierungs Informationen sind enthalten, damit APIs wie z <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> . b. ordnungsgemäß funktionieren.</span><span class="sxs-lookup"><span data-stu-id="c5090-156">Collation information is included to make APIs such as <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> work correctly.</span></span> <span data-ttu-id="c5090-157">Wenn Sie sicher sind, dass die APP die Sortierungs Daten nicht benötigt, können Sie diese deaktivieren, indem Sie die `BlazorWebAssemblyPreserveCollationData` MSBuild-Eigenschaft in der Projektdatei der APP auf Folgendes festlegen `false` :</span><span class="sxs-lookup"><span data-stu-id="c5090-157">If you're certain that the app doesn't require the collation data, consider disabling it by setting the `BlazorWebAssemblyPreserveCollationData` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```
