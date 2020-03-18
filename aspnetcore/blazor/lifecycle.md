---
title: ASP.NET Core Blazor-Lebenszyklus
author: guardrex
description: Erfahren Sie, wie die Lebenszyklusmethoden von Razor-Komponenten in ASP.NET Core Blazor-Apps verwendet werden.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/lifecycle
ms.openlocfilehash: ecacd0a9728cbefd716e9dc7cd8a8c62f3df6e0d
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78647581"
---
# <a name="aspnet-core-opno-locblazor-lifecycle"></a>ASP.NET Core Blazor-Lebenszyklus

Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)

Das Blazor-Framework umfasst synchrone und asynchrone Lebenszyklusmethoden. Setzen Sie Lebenszyklusmethoden außer Kraft, um während der Komponenteninitialisierung und des Renderings zusätzliche Vorgänge mit Komponenten durchzuführen.

## <a name="lifecycle-methods"></a>Lebenszyklusmethoden

### <a name="component-initialization-methods"></a>Methoden zur Komponenteninitialisierung

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> und <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> werden aufgerufen, wenn die Komponente initialisiert wird, nachdem sie ihre anfänglichen Parameter von ihrer übergeordneten Komponente erhalten hat. Verwenden Sie `OnInitializedAsync`, wenn die Komponente einen asynchronen Vorgang ausführt und nach Abschluss des Vorgangs aktualisiert werden soll.

Für einen synchronen Betrieb setzen Sie `OnInitialized` außer Kraft:

```csharp
protected override void OnInitialized()
{
    ...
}
```

Um einen asynchronen Vorgang auszuführen, setzen Sie `OnInitializedAsync` außer Kraft und verwenden das Schlüsselwort `await` für den Vorgang:

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Blazor Server-Apps, die [ihren Inhalt vorab rendern](xref:blazor/hosting-model-configuration#render-mode) rufen `OnInitializedAsync` **_zweimal_** auf:

* Einmal, wenn die Komponente anfänglich statisch als Teil der Seite gerendert wird.
* Ein zweites Mal, wenn der Browser eine Verbindung mit dem Server herstellt.

Informationen zum Verhindern, dass Entwicklercode in `OnInitializedAsync` zweimal ausgeführt wird, finden Sie im Abschnitt [Zustandsbehaftete erneute Verbindung nach dem Prerendering](#stateful-reconnection-after-prerendering).

Während für eine Blazor Server-App ein Prerendering durchgeführt wird, sind bestimmte Aktionen (z. B. Aufrufe in JavaScript) nicht möglich, da noch keine Verbindung mit dem Browser hergestellt wurde. Komponenten müssen wahrscheinlich unterschiedlich rendern, wenn dafür ein Prerendering durchgeführt wurde. Weitere Informationen finden Sie im Abschnitt [Ermitteln des Prerenderings einer App](#detect-when-the-app-is-prerendering).

### <a name="before-parameters-are-set"></a>Bevor die Parameter festgelegt werden

<xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> legt Parameter fest, die vom übergeordneten Element der Komponente in der Renderstruktur bereitgestellt werden:

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<xref:Microsoft.AspNetCore.Components.ParameterView> enthält bei jedem Aufruf von `SetParametersAsync` den gesamten Satz von Parameterwerten.

Die Standardimplementierung von `SetParametersAsync` legt den Wert der einzelnen Eigenschaften mit dem Attribut `[Parameter]` oder `[CascadingParameter]` fest, die einen entsprechenden Wert in `ParameterView` aufweist. Parameter, die keinen entsprechenden Wert in `ParameterView` haben, bleiben unverändert.

Wenn `base.SetParametersAync` nicht aufgerufen wird, kann der benutzerdefinierte Code den eingehenden Parameterwert in jeder gewünschten Weise interpretieren. Beispielsweise ist es nicht erforderlich, die eingehenden Parameter den Eigenschaften der Klasse zuzuordnen.

### <a name="after-parameters-are-set"></a>Nachdem die Parameter festgelegt wurden

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> und <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> werden aufgerufen:

* Wenn die Komponente initialisiert ist und ihren ersten Parametersatz von ihrer übergeordneten Komponente erhalten hat.
* Wenn die übergeordnete Komponente neu gerendert wird und Folgendes bereitstellt:
  * Nur bekannte einfache, unveränderliche Typen, von denen sich mindestens ein Parameter geändert hat.
  * Beliebige Parameter mit komplexem Typ. Das Framework kann nicht wissen, ob die Werte eines Parameters mit komplexem Typ intern mutiert sind, also behandelt es den Parametersatz als geändert.

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> Die asynchrone Arbeit bei der Anwendung von Parametern und Eigenschaftswerten muss während des `OnParametersSetAsync`-Lebenszyklusereignisses erfolgen.

```csharp
protected override void OnParametersSet()
{
    ...
}
```

### <a name="after-component-render"></a>Nach dem Rendern der Komponente

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> und <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> werden aufgerufen, nachdem eine Komponente das Rendering beendet hat. Element- und Komponentenverweise werden an dieser Stelle aufgefüllt. In dieser Phase können Sie zusätzliche Initialisierungsschritte unter Verwendung des gerenderten Inhalts durchführen, z. B. die Aktivierung von JavaScript-Bibliotheken von Drittanbietern, die mit den gerenderten DOM-Elementen arbeiten.

Der Parameter `firstRender` für `OnAfterRenderAsync` und `OnAfterRender`:

* Wird auf `true` festgelegt, wenn die Komponenteninstanz zum ersten Mal gerendert wird.
* Kann verwendet werden, um sicherzustellen, dass die Initialisierung nur einmal durchgeführt wird.

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

> [!NOTE]
> Die asynchrone Arbeit unmittelbar nach dem Rendering muss während des `OnAfterRenderAsync`-Lebenszyklusereignisses erfolgen.
>
> Selbst wenn Sie ein <xref:System.Threading.Tasks.Task> von `OnAfterRenderAsync` zurückgeben, plant das Framework keinen weiteren Renderzyklus für Ihre Komponente ein, sobald diese Aufgabe abgeschlossen ist. Damit soll eine unendliche Renderschleife vermieden werden. Dies unterscheidet sich von den anderen Lebenszyklusmethoden, die einen weiteren Renderzyklus planen, sobald die zurückgegebene Aufgabe abgeschlossen ist.

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

`OnAfterRender` und `OnAfterRenderAsync` *werden beim Prerendering auf dem Server nicht aufgerufen.*

### <a name="suppress-ui-refreshing"></a>Unterdrücken der UI-Aktualisierung

Setzen Sie <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> außer Kraft, um die Aktualisierung der Benutzeroberfläche zu unterdrücken. Wenn die Implementierung `true` zurückgibt, wird die Benutzeroberfläche aktualisiert:

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

`ShouldRender` wird jedes Mal aufgerufen, wenn die Komponente gerendert wird.

Selbst wenn `ShouldRender` außer Kraft gesetzt wird, wird die Komponente immer anfänglich gerendert.

## <a name="state-changes"></a>Statusänderungen

<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> benachrichtigt die Komponente, dass sich ihr Zustand geändert hat. Gegebenenfalls bewirkt der Aufruf von `StateHasChanged` das erneute Rendern der Komponente.

## <a name="handle-incomplete-async-actions-at-render"></a>Behandeln unvollständiger asynchroner Aktionen beim Rendern

Asynchrone Aktionen, die in Lebenszyklusereignissen ausgeführt werden, sind möglicherweise nicht abgeschlossen, bevor die Komponente gerendert wird. Objekte können während der Ausführung der Lebenszyklusmethode `null` oder unvollständig mit Daten gefüllt sein. Stellen Sie eine Renderinglogik bereit, um zu bestätigen, dass die Objekte initialisiert sind. Rendern Sie UI-Elemente für Platzhalter (z. B. eine Nachricht zum Ladevorgang), während Objekte `null` sind.

In der `FetchData`-Komponente der Blazor-Vorlagen wird `OnInitializedAsync` außer Kraft gesetzt, um Vorhersagedaten asynchron zu erhalten (`forecasts`). Wenn `forecasts` gleich `null` ist, wird dem Benutzer eine Nachricht zum Ladevorgang angezeigt. Nachdem die von `OnInitializedAsync` zurückgegebene `Task` abgeschlossen ist, wird die Komponente mit dem aktualisierten Zustand neu gerendert.

*Pages/FetchData.razor* in der Blazor-Servervorlage:

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a>Beseitigung von Komponenten mit IDisposable

Wenn eine Komponente <xref:System.IDisposable> implementiert, wird die [Dispose-Methode](/dotnet/standard/garbage-collection/implementing-dispose) aufgerufen, wenn die Komponente aus der Benutzeroberfläche entfernt wird. Die folgende Komponente verwendet `@implements IDisposable` und die `Dispose`-Methode:

```razor
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

> [!NOTE]
> Der Aufruf von <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> in `Dispose` wird nicht unterstützt. `StateHasChanged` könnte im Rahmen des Beendens des Renderers aufgerufen werden, sodass die Anforderung von UI-Updates an diesem Punkt nicht unterstützt wird.

## <a name="handle-errors"></a>Behandeln von Fehlern

Informationen zur Behandlung von Fehlern während der Ausführung von Lebenszyklusmethoden finden Sie unter <xref:blazor/handle-errors#lifecycle-methods>.

## <a name="stateful-reconnection-after-prerendering"></a>Zustandsbehaftete erneute Verbindung nach dem Prerendering

Wenn in einer Blazor-Server-App `RenderMode` gleich `ServerPrerendered` ist, wird die Komponente zunächst statisch als Teil der Seite gerendert. Sobald der Browser eine Verbindung mit dem Server herstellt, wird die Komponente *wieder* gerendert, und die Komponente ist nun interaktiv. Wenn die [OnInitialized{Async}](xref:blazor/lifecycle#component-initialization-methods)-Lebenszyklusmethode zur Initialisierung der Komponente vorhanden ist, wird die Methode *zweimal* ausgeführt:

* Wenn die Komponente statisch vorab gerendert ist.
* Nachdem die Serververbindung hergestellt wurde.

Dies kann zu einer spürbaren Änderung der auf der Benutzeroberfläche angezeigten Daten führen, wenn die Komponente schließlich gerendert wird.

So vermeiden Sie das Szenario des doppelten Renderings in einer Blazor-Server-App

* Übergeben Sie einen Bezeichner, der zum Zwischenspeichern des Status während des Prerenderings und zum Abrufen des Zustands nach dem Neustart der App verwendet werden kann.
* Verwenden Sie den Bezeichner während des Prerenderings, um den Zustand der Komponenten zu speichern.
* Verwenden Sie den Bezeichner nach dem Prerendering, um den zwischengespeicherten Zustand abzurufen.

Der folgende Code veranschaulicht eine aktualisierte `WeatherForecastService` in einer vorlagenbasierten Blazor-Server-App, die das doppelte Rendering vermeidet:

```csharp
public class WeatherForecastService
{
    private static readonly string[] _summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = _summaries[rng.Next(_summaries.Length)]
            }).ToArray();
        });
    }
}
```

Weitere Informationen zum `RenderMode` finden Sie unter <xref:blazor/hosting-model-configuration#render-mode>.

## <a name="detect-when-the-app-is-prerendering"></a>Erkennen, wenn für die App ein Prerendering durchgeführt wird

[!INCLUDE[](~/includes/blazor-prerendering.md)]
