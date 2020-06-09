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
# <a name="aspnet-core-blazor-webassembly-performance-best-practices"></a>Bewährte Methoden für die ASP.net Core Blazor webassemblyleistung

Von [Pranav Krishnamoorthy](https://github.com/pranavkm)

Dieser Artikel enthält Richtlinien für die Blazor bewährten Methoden der ASP.net Core Webassembly.

## <a name="avoid-unnecessary-component-renders"></a>Unnötige Komponenten Rendering vermeiden

Blazorder diffingalgorithmus vermeidet das erneute Ausführen einer Komponente, wenn der Algorithmus erkennt, dass sich die Komponente nicht geändert hat. Überschreiben Sie, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> um das Komponenten Rendering genauer zu steuern.

Wenn Sie eine nur für die Benutzeroberfläche geschützte Komponente erstellen, die sich nach dem anfänglichen Rendering nie ändert, konfigurieren <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> Sie für Folgendes `false` :

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

Die meisten apps benötigen keine differenzierte Kontrolle, können aber <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> auch verwendet werden, um eine Komponente selektiv zu erzeugen, die auf ein UI-Ereignis reagiert.

Im folgenden Beispiel:

* <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>wird überschrieben und auf den Wert des Felds festgelegt <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> , das anfänglich ist, `false` Wenn die Komponente geladen wird.
* Wenn die Schaltfläche ausgewählt wird, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> wird auf festgelegt. Dadurch wird `true` die Komponente gezwungen, die Aktualisierung mit dem aktualisierten durchzusetzen `currentCount` .
* Unmittelbar nach der erneuten Ausführung <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> legt den Wert von auf zurück, um die erneute Ausführung <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> bis zum `false` nächsten auswählen der Schaltfläche zu verhindern.

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

Weitere Informationen finden Sie unter <xref:blazor/lifecycle#after-component-render>.

## <a name="virtualize-re-usable-fragments"></a>Wiederverwendbare Fragmente virtualisieren

Komponenten bieten einen einfachen Ansatz zum Erstellen wiederverwendbarer Code Fragmente und Markup. Im Allgemeinen empfiehlt es sich, individuelle Komponenten zu erstellen, die den Anforderungen der APP am besten entsprechen. Ein Nachteil ist, dass jede zusätzliche untergeordnete Komponente zur Gesamtzeit beiträgt, die zum Rendering einer übergeordneten Komponente benötigt wird. Bei den meisten apps ist der zusätzliche mehr Aufwand unerheblich. Apps, die eine große Anzahl von Komponenten erzeugen, sollten die Verwendung von Strategien in Erwägung gezogen werden, um den Verarbeitungsaufwand zu reduzieren

Beispielsweise ist ein Raster oder eine Liste, das Hunderte von Zeilen mit Komponenten rendert, Prozessor intensiv zum Rendern. Sie sollten eine Virtualisierung eines Raster-oder Listen Layouts in Erwägung gezogen, sodass nur eine Teilmenge der Komponenten zu einem beliebigen Zeitpunkt gerendert wird. Ein Beispiel für das Rendering von Komponenten Teilmengen finden Sie unter den folgenden Komponenten in der [Beispiel-App für Virtualisierung (ASPNET/Samples-GitHub-Repository)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):

* `Virtualize`Component ([Shared/Virtualize. Razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): eine in c# geschriebene Komponente, <xref:Microsoft.AspNetCore.Components.ComponentBase> mit der implementiert wird, um einen Satz von Wetterdaten Zeilen basierend auf dem Benutzer Bildlauf zu erzeugen.
* `FetchData`Component ([pages/fetchdata. Razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): verwendet die- `Virtualize` Komponente, um 25 Zeilen von Wetterdaten gleichzeitig anzuzeigen.

## <a name="avoid-javascript-interop-to-marshal-data"></a>Vermeiden von JavaScript-Interop zum Mars Hallen von Daten

In Blazor Webassembly muss ein JavaScript (JS)-Interop-Befehl die Webassembly-js-Grenze überschreiten. Durch das Serialisieren und Deserialisieren von Inhalten in den beiden Kontexten wird der Verarbeitungsaufwand für die app erzeugt. Häufige js-Interop-Aufrufe beeinträchtigen häufig die Leistung. Um das Marshalling von Daten über die Grenze zu reduzieren, stellen Sie fest, ob die APP viele kleine Nutzlasten in einer einzelnen großen Nutzlast konsolidieren kann, um die große Menge an Kontext wechseln zwischen Webassembly und JS zu vermeiden.

## <a name="use-systemtextjson"></a>Verwenden von "System. Text. JSON"

Blazordie JS-Interop-Implementierung basiert auf, bei der es sich <xref:System.Text.Json> um eine leistungsstarke JSON-serialisierungsbibliothek mit geringer Speicher Belegung handelt. Die Verwendung <xref:System.Text.Json> von führt nicht zu einer zusätzlichen App-Nutzlastgröße beim Hinzufügen einer oder mehrerer alternativer JSON-Bibliotheken.

Eine Migrations Anleitung finden [Sie unter Migrieren von "newtonsoft. JSON" zu "System. Text. JSON](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to)".

## <a name="use-synchronous-and-unmarshalled-js-interop-apis-where-appropriate"></a>Verwenden Sie gegebenenfalls synchrone und ungemarshallte js-Interop-APIs.

BlazorWebassembly bietet zwei weitere Versionen von <xref:Microsoft.JSInterop.IJSRuntime> über der einzelnen Version, die für Server-apps verfügbar ist Blazor :

* <xref:Microsoft.JSInterop.IJSInProcessRuntime>ermöglicht das synchrone Aufrufen von JS-Interop-aufrufen, was weniger Aufwand als die asynchronen Versionen hat:

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

* <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime>lässt nicht marshallte js-Interop-Aufrufe zu:

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
  > Bei der Verwendung <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> von ist der geringste mehr Aufwand für die JS-Interop-Ansätze erforderlich, aber die JavaScript-APIs, die für die Interaktion mit diesen APIs erforderlich sind, sind zurzeit nicht dokumentiert und unterliegen in zukünftigen Versionen

## <a name="reduce-app-size"></a>Reduzieren der APP-Größe

### <a name="intermediate-language-il-linking"></a>Intermediate Language (IL)-Verknüpfung

[Verknüpfen eines Blazor Webassembly-App](xref:host-and-deploy/blazor/configure-linker) reduziert die Größe der APP, indem nicht verwendeter Code in den Binärdateien der APP gekürzt wird. Standardmäßig ist der Linker nur beim Aufbau in der `Release` Konfiguration aktiviert. Um davon zu profitieren, veröffentlichen Sie die APP für die Bereitstellung mit dem Befehl " [dotnet Publish](/dotnet/core/tools/dotnet-publish) ", wobei die Option [-c |--Configuration](/dotnet/core/tools/dotnet-publish#options) auf festgelegt ist `Release` :

```dotnetcli
dotnet publish -c Release
```

### <a name="compression"></a>Komprimierung

Wenn eine Blazor Webassembly-App veröffentlicht wird, wird die Ausgabe statisch während der Veröffentlichung komprimiert, um die Größe der APP zu reduzieren und den mehr Aufwand für die Lauf Zeit Komprimierung zu entfernen. Blazorbasiert auf dem-Server, um die Inhalts Erfüllung auszuführen und statisch komprimierte Dateien zu verarbeiten.

Nachdem eine APP bereitgestellt wurde, überprüfen Sie, ob die APP komprimierte Dateien bereitstellt. Überprüfen Sie die Registerkarte Netzwerk im Entwicklertools eines Browsers, und überprüfen Sie, ob die Dateien mit oder bedient werden `Content-Encoding: br` `Content-Encoding: gz` . Wenn der Host keine komprimierten Dateien bedient, befolgen Sie die Anweisungen unter <xref:host-and-deploy/blazor/webassembly#compression> .

### <a name="disable-unused-features"></a>Nicht verwendete Funktionen deaktivieren

BlazorDie Laufzeit der Webassembly umfasst die folgenden .NET-Funktionen, die deaktiviert werden können, wenn die APP Sie nicht für eine geringere Nutzlastgröße benötigt:

* Eine Datendatei ist enthalten, um Zeitzoneninformationen korrekt zu machen. Wenn die APP diese Funktion nicht benötigt, können Sie Sie deaktivieren, indem Sie die `BlazorEnableTimeZoneSupport` MSBuild-Eigenschaft in der Projektdatei der APP auf Folgendes festlegen `false` :

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

* Sortierungs Informationen sind enthalten, damit APIs wie z <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> . b. ordnungsgemäß funktionieren. Wenn Sie sicher sind, dass die APP die Sortierungs Daten nicht benötigt, können Sie diese deaktivieren, indem Sie die `BlazorWebAssemblyPreserveCollationData` MSBuild-Eigenschaft in der Projektdatei der APP auf Folgendes festlegen `false` :

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```
