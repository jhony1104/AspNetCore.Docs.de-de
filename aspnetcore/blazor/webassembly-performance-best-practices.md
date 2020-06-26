---
title: Best Practices zur Blazor WebAssembly-Leistung in ASP.NET Core
author: pranavkm
description: In diesem Artikel erhalten Sie Tipps für das Steigern der Leistung von Blazor WebAssembly-Apps in ASP.NET Core und das Vermeiden von häufig auftretenden Leistungsproblemen.
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
uid: blazor/webassembly-performance-best-practices
ms.openlocfilehash: 2b6d4e706856cb28f26c2502feca4f959ca4abac
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243030"
---
# <a name="aspnet-core-blazor-webassembly-performance-best-practices"></a><span data-ttu-id="78bdf-103">Best Practices zur Blazor WebAssembly-Leistung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="78bdf-103">ASP.NET Core Blazor WebAssembly performance best practices</span></span>

<span data-ttu-id="78bdf-104">Von [Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="78bdf-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="78bdf-105">In diesem Artikel finden Sie Richtlinien zu Best Practices für die Leistung von Blazor WebAssembly-Apps in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="78bdf-105">This article provides guidelines for ASP.NET Core Blazor WebAssembly performance best practices.</span></span>

## <a name="avoid-unnecessary-component-renders"></a><span data-ttu-id="78bdf-106">Vermeiden von unnötigem Komponentenrendering</span><span class="sxs-lookup"><span data-stu-id="78bdf-106">Avoid unnecessary component renders</span></span>

<span data-ttu-id="78bdf-107">Der Vergleichsalgorithmus von Blazor verhindert das Rendern einer Komponente, wenn der Algorithmus erkennt, dass die Komponente nicht geändert wurde.</span><span class="sxs-lookup"><span data-stu-id="78bdf-107">Blazor's diffing algorithm avoids rerendering a component when the algorithm perceives that the component hasn't changed.</span></span> <span data-ttu-id="78bdf-108">Überschreiben Sie <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType>, wenn Sie eine genaue Kontrolle über das Rendern von Komponenten haben möchten.</span><span class="sxs-lookup"><span data-stu-id="78bdf-108">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> for fine-grained control over component rendering.</span></span>

<span data-ttu-id="78bdf-109">Wenn eine nur auf die Benutzeroberfläche bezogene Komponente erstellt wird, die sich nach dem ursprünglichen Rendern nicht mehr ändert, konfigurieren Sie <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> so, dass `false` zurückgegeben wird:</span><span class="sxs-lookup"><span data-stu-id="78bdf-109">If authoring a UI-only component that never changes after the initial render, configure <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to return `false`:</span></span>

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

<span data-ttu-id="78bdf-110">Für die meisten Apps ist eine genaue Kontrolle nicht erforderlich, aber <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> kann auch verwendet werden, um eine Komponente, die auf ein Benutzeroberflächenereignis antwortet, selektiv zu rendern.</span><span class="sxs-lookup"><span data-stu-id="78bdf-110">Most apps don't require fine-grained control, but <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> can also be used to selectively render a component responding to a UI event.</span></span>

<span data-ttu-id="78bdf-111">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="78bdf-111">In the following example:</span></span>

* <span data-ttu-id="78bdf-112"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> wird überschrieben und auf den Wert des <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>-Felds festgelegt, der ursprünglich `false` ist, wenn die Komponente geladen wird.</span><span class="sxs-lookup"><span data-stu-id="78bdf-112"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden and set to the value of the <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> field, which is initially `false` when the component loads.</span></span>
* <span data-ttu-id="78bdf-113">Wenn auf die Schaltfläche geklickt wird, wird <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> auf `true` festgelegt, was erzwingt, dass die Komponente noch mal mit aktualisierter `currentCount`-Eigenschaft gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="78bdf-113">When the button is selected, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is set to `true`, which forces the component to rerender with the updated `currentCount`.</span></span>
* <span data-ttu-id="78bdf-114">Unmittelbar nach dem erneuten Rendern legt <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> den Wert von <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> wieder auf `false` fest, um zukünftiges erneutes Rendern bis zum nächsten Klick auf die Schaltfläche zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="78bdf-114">Immediately after rerendering, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> sets the value of <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> back to `false` to prevent further rerendering until the next time the button is selected.</span></span>

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

<span data-ttu-id="78bdf-115">Weitere Informationen finden Sie unter <xref:blazor/components/lifecycle#after-component-render>.</span><span class="sxs-lookup"><span data-stu-id="78bdf-115">For more information, see <xref:blazor/components/lifecycle#after-component-render>.</span></span>

## <a name="virtualize-re-usable-fragments"></a><span data-ttu-id="78bdf-116">Virtualisieren wiederverwendbarer Fragmente</span><span class="sxs-lookup"><span data-stu-id="78bdf-116">Virtualize re-usable fragments</span></span>

<span data-ttu-id="78bdf-117">Komponenten stellen einen bequemen Ansatz dar, um wiederverwendbare Code- und Markupfragmente zu generieren.</span><span class="sxs-lookup"><span data-stu-id="78bdf-117">Components offer a convenient approach to produce re-usable fragments of code and markup.</span></span> <span data-ttu-id="78bdf-118">Allgemein wird empfohlen, einzelne Komponenten zu erstellen, die am besten die Anforderungen der App erfüllen können.</span><span class="sxs-lookup"><span data-stu-id="78bdf-118">In general, we recommend authoring individual components that best align with the app's requirements.</span></span> <span data-ttu-id="78bdf-119">Eine Einschränkung besteht darin, dass die einzelnen zusätzlichen untergeordneten Komponenten zur Gesamtzeit beitragen, die benötigt wird, eine übergeordnete Komponente zu rendern.</span><span class="sxs-lookup"><span data-stu-id="78bdf-119">One caveat is that each additional child component contributes to the total time it takes to render a parent component.</span></span> <span data-ttu-id="78bdf-120">Bei den meisten Apps kann dieser zusätzliche Aufwand vernachlässigt werden.</span><span class="sxs-lookup"><span data-stu-id="78bdf-120">For most apps, the additional overhead is negligible.</span></span> <span data-ttu-id="78bdf-121">Für Apps, die eine große Anzahl Komponenten erzeugen, sollte die Verwendung von Strategien in Betracht gezogen werden, die den Verarbeitungsaufwand reduzieren, z. B. indem die Anzahl gerenderter Komponenten begrenzt wird.</span><span class="sxs-lookup"><span data-stu-id="78bdf-121">Apps that produce a large number of components should consider using strategies to reduce processing overhead, such as limiting the number of rendered components.</span></span>

<span data-ttu-id="78bdf-122">Bei einem Raster oder einer Liste, die Hunderte Datensätze rendert, die Komponenten enthalten, ist die Auslastung des Prozessors für das Rendern beispielsweise höher.</span><span class="sxs-lookup"><span data-stu-id="78bdf-122">For example, a grid or list that renders hundreds of rows containing components is processor intensive to render.</span></span> <span data-ttu-id="78bdf-123">Ziehen Sie es in Betracht, ein Raster oder ein Listenlayout zu virtualisieren, sodass nur eine Teilmenge der Komponenten jeweils gleichzeitig gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="78bdf-123">Consider virtualizing a grid or list layout so that only a subset of the components is rendered at any given time.</span></span> <span data-ttu-id="78bdf-124">Ein Beispiel für das Rendern einer Teilmenge von Komponenten finden Sie bei den folgenden Komponenten unter [`Virtualization`-Beispiel-App (aspnet/samples, GitHub-Repository)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span><span class="sxs-lookup"><span data-stu-id="78bdf-124">For an example of component subset rendering, see the following components in the [`Virtualization` sample app (aspnet/samples GitHub repository)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span></span>

* <span data-ttu-id="78bdf-125">`Virtualize`-Komponente ([`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): Eine in C# geschriebene Komponente, die <xref:Microsoft.AspNetCore.Components.ComponentBase> implementiert, um mehrere Wetterdatensätze basierend auf dem Scrollen des Benutzers zu rendern</span><span class="sxs-lookup"><span data-stu-id="78bdf-125">`Virtualize` component ([`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): A component written in C# that implements <xref:Microsoft.AspNetCore.Components.ComponentBase> to render a set of weather data rows based on user scrolling.</span></span>
* <span data-ttu-id="78bdf-126">`FetchData`-Komponente ([`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): Verwendet die `Virtualize`-Komponente, um 25 Wetterdatensätze gleichzeitig anzuzeigen</span><span class="sxs-lookup"><span data-stu-id="78bdf-126">`FetchData` component ([`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): Uses the `Virtualize` component to display 25 rows of weather data at a time.</span></span>

## <a name="avoid-javascript-interop-to-marshal-data"></a><span data-ttu-id="78bdf-127">Vermeiden von JavaScript-Interoperabilität zum Marshallen von Daten</span><span class="sxs-lookup"><span data-stu-id="78bdf-127">Avoid JavaScript interop to marshal data</span></span>

<span data-ttu-id="78bdf-128">In Blazor WebAssembly muss ein JavaScript-Interop-Aufruf (JS) die WebAssembly-JS-Grenze überschreiten.</span><span class="sxs-lookup"><span data-stu-id="78bdf-128">In Blazor WebAssembly, a JavaScript (JS) interop call must traverse the WebAssembly-JS boundary.</span></span> <span data-ttu-id="78bdf-129">Das Serialisieren und Deserialisieren von Inhalt für die beiden Kontexte führt zu Verarbeitungsaufwand für die App.</span><span class="sxs-lookup"><span data-stu-id="78bdf-129">Serializing and deserializing content across the two contexts creates processing overhead for the app.</span></span> <span data-ttu-id="78bdf-130">Häufige JS-Interop-Aufrufe beeinträchtigen die Leistung oftmals.</span><span class="sxs-lookup"><span data-stu-id="78bdf-130">Frequent JS interop calls often adversely affects performance.</span></span> <span data-ttu-id="78bdf-131">Bestimmen Sie, ob die App viele kleine Nutzlasten in eine einzelne große Payload konsolidieren kann, um das Marshallen der Daten über die Grenze hinweg zu reduzieren, indem das hohe Volumen des Kontextwechselns zwischen WebAssembly und JS vermieden wird.</span><span class="sxs-lookup"><span data-stu-id="78bdf-131">To reduce the marshalling of data across the boundary, determine if the app can consolidate many small payloads into a single large payload to avoid the high volume of context switching between WebAssembly and JS.</span></span>

## <a name="use-systemtextjson"></a><span data-ttu-id="78bdf-132">Verwenden von System.Text.Json</span><span class="sxs-lookup"><span data-stu-id="78bdf-132">Use System.Text.Json</span></span>

<span data-ttu-id="78bdf-133">Die JS-Interop-Implementierung von Blazor basiert auf <xref:System.Text.Json>. Dabei handelt es sich um eine JSON-Hochleistungsserialisierungsbibliothek mit niedriger Arbeitsspeicherzuteilung.</span><span class="sxs-lookup"><span data-stu-id="78bdf-133">Blazor's JS interop implementation relies on <xref:System.Text.Json>, which is a high-performance JSON serialization library with low memory allocation.</span></span> <span data-ttu-id="78bdf-134">Das Verwenden von <xref:System.Text.Json> führt nicht zu einer zusätzlichen Nutzlastgröße für die App, wenn eine oder mehrere alternative JSON-Bibliotheken hinzufügt werden.</span><span class="sxs-lookup"><span data-stu-id="78bdf-134">Using <xref:System.Text.Json> doesn't result in additional app payload size over adding one or more alternate JSON libraries.</span></span>

<span data-ttu-id="78bdf-135">Eine Anleitung zur Migration finden Sie unter [Migration von `Newtonsoft.Json` zu `System.Text.Json`](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span><span class="sxs-lookup"><span data-stu-id="78bdf-135">For migration guidance, see [How to migrate from `Newtonsoft.Json` to `System.Text.Json`](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span></span>

## <a name="use-synchronous-and-unmarshalled-js-interop-apis-where-appropriate"></a><span data-ttu-id="78bdf-136">Verwenden von synchronen und nicht gemarshallten JS-Interop-APIs (wenn möglich)</span><span class="sxs-lookup"><span data-stu-id="78bdf-136">Use synchronous and unmarshalled JS interop APIs where appropriate</span></span>

Blazor<span data-ttu-id="78bdf-137"> WebAssembly bietet zwei zusätzliche Versionen von <xref:Microsoft.JSInterop.IJSRuntime> zusätzlich zur einzelnen für BlazorServer-Apps verfügbaren Version:</span><span class="sxs-lookup"><span data-stu-id="78bdf-137"> WebAssembly offers two additional versions of <xref:Microsoft.JSInterop.IJSRuntime> over the single version available to Blazor Server apps:</span></span>

* <span data-ttu-id="78bdf-138"><xref:Microsoft.JSInterop.IJSInProcessRuntime> ermöglicht das synchrone Aufrufen von JS-Interop-Aufrufen, was im Vergleich zu asynchronen Versionen zu weniger Aufwand führt:</span><span class="sxs-lookup"><span data-stu-id="78bdf-138"><xref:Microsoft.JSInterop.IJSInProcessRuntime> allows invoking JS interop calls synchronously, which has less overhead than the asynchronous versions:</span></span>

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

* <span data-ttu-id="78bdf-139"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> ermöglicht nicht gemarshallte JS-Interop-Aufrufe:</span><span class="sxs-lookup"><span data-stu-id="78bdf-139"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> permits unmarshalled JS interop calls:</span></span>

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
  > <span data-ttu-id="78bdf-140">Während die Verwendung von <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> den geringsten Aufwand der JS-Interop-Ansätze hat, gibt es für die JavaScript-APIs, die erforderlich sind, um mit diesen APIs zu interagieren, aktuell keine Dokumentation. Außerdem werden in diesem Bereich im Rahmen zukünftiger Releases Breaking Changes vorgenommen.</span><span class="sxs-lookup"><span data-stu-id="78bdf-140">While using <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> has the least overhead of the JS interop approaches, the JavaScript APIs required to interact with these APIs are currently undocumented and subject to breaking changes in future releases.</span></span>

## <a name="reduce-app-size"></a><span data-ttu-id="78bdf-141">Reduzieren der App-Größe</span><span class="sxs-lookup"><span data-stu-id="78bdf-141">Reduce app size</span></span>

### <a name="intermediate-language-il-linking"></a><span data-ttu-id="78bdf-142">Intermediate Language-Verknüpfung (IL)</span><span class="sxs-lookup"><span data-stu-id="78bdf-142">Intermediate Language (IL) linking</span></span>

<span data-ttu-id="78bdf-143">[Das Konfigurieren eines Linkers für eine Blazor WebAssembly-App](xref:blazor/host-and-deploy/configure-linker) reduziert die Größe der App, indem nicht genutzter Code in den Binärdateien der App gekürzt wird.</span><span class="sxs-lookup"><span data-stu-id="78bdf-143">[Linking a Blazor WebAssembly app](xref:blazor/host-and-deploy/configure-linker) reduces the app's size by trimming unused code in the app's binaries.</span></span> <span data-ttu-id="78bdf-144">Standardmäßig ist der Linker nur aktiviert, wenn der Erstellvorgang in der `Release`-Konfiguration ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="78bdf-144">By default, the linker is only enabled when building in `Release` configuration.</span></span> <span data-ttu-id="78bdf-145">Veröffentlichen Sie die App für die Bereitstellung mithilfe des Befehls [`dotnet publish`](/dotnet/core/tools/dotnet-publish) mit auf `Release` festgelegter Option [-c|--configuration](/dotnet/core/tools/dotnet-publish#options), um diesen Vorteil zu nutzen:</span><span class="sxs-lookup"><span data-stu-id="78bdf-145">To benefit from this, publish the app for deployment using the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

```dotnetcli
dotnet publish -c Release
```

### <a name="compression"></a><span data-ttu-id="78bdf-146">Komprimierung</span><span class="sxs-lookup"><span data-stu-id="78bdf-146">Compression</span></span>

<span data-ttu-id="78bdf-147">Wenn eine Blazor-WebAssembly-App veröffentlicht wird, wird die Ausgabe bei der Veröffentlichung statisch komprimiert, um die App-Größe zu verringern und den Aufwand für eine Laufzeitkomprimierung zu beseitigen.</span><span class="sxs-lookup"><span data-stu-id="78bdf-147">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> Blazor<span data-ttu-id="78bdf-148"> basiert darauf, dass der Server eine Inhaltsaushandlung ausführt und statisch komprimierte Dateien bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="78bdf-148"> relies on the server to perform content negotation and serve statically-compressed files.</span></span>

<span data-ttu-id="78bdf-149">Nach der Bereitstellung einer App überprüfen Sie, ob die App komprimierte Dateien bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="78bdf-149">After an app is deployed, verify that the app serves compressed files.</span></span> <span data-ttu-id="78bdf-150">Sehen Sie sich die Netzwerkregisterkarte der Entwicklertools eines Browsers an, und überprüfen Sie, ob die Dateien mit `Content-Encoding: br` oder `Content-Encoding: gz` bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="78bdf-150">Inspect the Network tab in a browser's Developer Tools and verify that the files are served with `Content-Encoding: br` or `Content-Encoding: gz`.</span></span> <span data-ttu-id="78bdf-151">Wenn der Host keine komprimierten Dateien bereitstellt, befolgen Sie die Anweisungen in <xref:blazor/host-and-deploy/webassembly#compression>.</span><span class="sxs-lookup"><span data-stu-id="78bdf-151">If the host isn't serving compressed files, follow the instructions in <xref:blazor/host-and-deploy/webassembly#compression>.</span></span>

### <a name="disable-unused-features"></a><span data-ttu-id="78bdf-152">Deaktivieren nicht genutzter Features</span><span class="sxs-lookup"><span data-stu-id="78bdf-152">Disable unused features</span></span>

<span data-ttu-id="78bdf-153">Die Blazor WebAssembly-Runtime schließt die folgenden .NET-Features ein, die deaktiviert werden können, wenn die App sie aufgrund einer kleineren Nutzlastgröße nicht benötigt:</span><span class="sxs-lookup"><span data-stu-id="78bdf-153">Blazor WebAssembly's runtime includes the following .NET features that can be disabled if the app doesn't require them for a smaller payload size:</span></span>

* <span data-ttu-id="78bdf-154">Eine Datendatei wird eingeschlossen, damit die Zeitzoneninformationen korrekt sind.</span><span class="sxs-lookup"><span data-stu-id="78bdf-154">A data file is included to make timezone information correct.</span></span> <span data-ttu-id="78bdf-155">Wenn die App dieses Feature nicht benötigt, ziehen Sie in Betracht, es zu deaktivieren, indem Sie die `BlazorEnableTimeZoneSupport`-MSBuild-Eigenschaft in der Projektdatei der App auf `false` festlegen:</span><span class="sxs-lookup"><span data-stu-id="78bdf-155">If the app doesn't require this feature, consider disabling it by setting the `BlazorEnableTimeZoneSupport` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

* <span data-ttu-id="78bdf-156">Die Sortierungsinformationen werden eingeschlossen, damit APIs wie <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> ordnungsgemäß funktionieren.</span><span class="sxs-lookup"><span data-stu-id="78bdf-156">Collation information is included to make APIs such as <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> work correctly.</span></span> <span data-ttu-id="78bdf-157">Wenn Sie sicher sind, dass die App die Sortierungsinformationen nicht benötigt, ziehen Sie in Betracht, sie zu deaktivieren, indem Sie die `BlazorWebAssemblyPreserveCollationData`-MSBuild-Eigenschaft in der Projektdatei der App auf `false` festlegen:</span><span class="sxs-lookup"><span data-stu-id="78bdf-157">If you're certain that the app doesn't require the collation data, consider disabling it by setting the `BlazorWebAssemblyPreserveCollationData` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```
