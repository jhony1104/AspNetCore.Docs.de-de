---
title: ASP.net Core Blazor Lebenszyklus
author: guardrex
description: Erfahren Sie, wie Sie den Lebenszyklus von Razor-Komponenten in ASP.net Core Blazor-Apps verwenden.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/lifecycle
ms.openlocfilehash: df5bb676df59b538179a69978040521c4ee78ed1
ms.sourcegitcommit: cbd30479f42cbb3385000ef834d9c7d021fd218d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2020
ms.locfileid: "76146367"
---
# <a name="aspnet-core-opno-locblazor-lifecycle"></a>ASP.net Core Blazor Lebenszyklus

Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)

Das Blazor Framework umfasst synchrone und asynchrone Lebenszyklus Methoden. Überschreiben Sie Lebenszyklus Methoden, um während der Initialisierung und dem Rendering von Komponenten zusätzliche Vorgänge für Komponenten auszuführen.

## <a name="lifecycle-methods"></a>Lebenszyklusmethoden

### <a name="component-initialization-methods"></a>Initialisierungs Methoden für Komponenten

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> und <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> werden aufgerufen, wenn die Komponente initialisiert wird, nachdem ihre ursprünglichen Parameter von der übergeordneten Komponente empfangen wurden. Verwenden Sie `OnInitializedAsync`, wenn die Komponente einen asynchronen Vorgang ausführt und nach Abschluss des Vorgangs aktualisiert werden soll. Diese Methoden werden nur einmal aufgerufen, wenn die Komponente zum ersten Mal instanziiert wird.

Überschreiben Sie für einen synchronen Vorgang `OnInitialized`:

```csharp
protected override void OnInitialized()
{
    ...
}
```

Um einen asynchronen Vorgang auszuführen, überschreiben Sie `OnInitializedAsync`, und verwenden Sie das Schlüsselwort `await` für den Vorgang:

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

### <a name="before-parameters-are-set"></a>Vor dem Festlegen von Parametern

<xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> legt Parameter fest, die vom übergeordneten Element in der Rendering-Struktur bereitgestellt werden:

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<xref:Microsoft.AspNetCore.Components.ParameterView> enthält den gesamten Satz von Parameterwerten jedes Mal, wenn `SetParametersAsync` aufgerufen wird.

Die Standard Implementierung von `SetParametersAsync` legt den Wert jeder Eigenschaft mit dem `[Parameter]` oder `[CascadingParameter]` Attribut fest, das über einen entsprechenden Wert in der `ParameterView`verfügt. Parameter, die keinen entsprechenden Wert in `ParameterView` haben, bleiben unverändert.

Wenn `base.SetParametersAync` nicht aufgerufen wird, kann der benutzerdefinierte Code den Wert eingehender Parameter in beliebiger Weise interpretieren. Beispielsweise ist es nicht erforderlich, den Eigenschaften der-Klasse die eingehenden Parameter zuzuweisen.

### <a name="after-parameters-are-set"></a>Nachdem Parameter festgelegt wurden

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> und <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> werden aufgerufen:

* Wenn die Komponente initialisiert wird und ihren ersten Satz von Parametern von der übergeordneten Komponente empfangen hat.
* Wenn die übergeordnete Komponente erneut gerendert wird und Folgendes bereitstellt:
  * Nur bekannte primitive unveränderliche Typen, von denen sich mindestens ein Parameter geändert hat.
  * Alle komplexen typisierten Parameter. Das Framework weiß nicht, ob die Werte eines komplexen typisierten Parameters intern geändert wurden, sodass der Parametersatz als geändert behandelt wird.

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> Asynchrone Arbeit beim Anwenden von Parametern und Eigenschafts Werten muss während des `OnParametersSetAsync` Lifecycle-Ereignisses auftreten.

```csharp
protected override void OnParametersSet()
{
    ...
}
```

### <a name="after-component-render"></a>Nach dem Rendering der Komponente

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> und <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> werden aufgerufen, nachdem eine Komponente das Rendering abgeschlossen hat. Element-und Komponenten Verweise werden an diesem Punkt aufgefüllt. Verwenden Sie diese Phase, um zusätzliche Initialisierungs Schritte unter Verwendung des gerenderten Inhalts auszuführen, z. b. das Aktivieren von JavaScript-Bibliotheken von Drittanbietern, die auf den gerenderten Dom

Der `firstRender` Parameter für `OnAfterRenderAsync` und `OnAfterRender`:

* Wird auf `true` festgelegt, wenn die Komponenteninstanz zum ersten Mal gerendert wird.
* Kann verwendet werden, um sicherzustellen, dass die Initialisierungs Arbeit nur einmal ausgeführt wird.

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
> Asynchrone Arbeit unmittelbar nach dem Rendering muss während des `OnAfterRenderAsync` Lifecycle-Ereignisses stattfinden.
>
> Auch wenn Sie eine <xref:System.Threading.Tasks.Task> von `OnAfterRenderAsync`zurückgeben, plant das Framework keinen weiteren Renderingdurchlauf für die Komponente, sobald diese Aufgabe abgeschlossen ist. Dadurch wird eine unendliche Renderschleife vermieden. Dies unterscheidet sich von den anderen Lebenszyklus Methoden, die einen weiteren renderzyklus planen, sobald die zurückgegebene Aufgabe abgeschlossen ist.

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

`OnAfterRender` und `OnAfterRenderAsync` werden *nicht aufgerufen, wenn die Vorab Anmeldung auf dem Server erfolgt.*

### <a name="suppress-ui-refreshing"></a>UI-Aktualisierung unterdrücken

Überschreiben Sie <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*>, um die Benutzeroberfläche zu aktualisieren. Wenn die Implementierung `true`zurückgibt, wird die Benutzeroberfläche aktualisiert:

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

`ShouldRender` wird jedes Mal aufgerufen, wenn die Komponente gerendert wird.

Auch wenn `ShouldRender` überschrieben wird, wird die Komponente immer anfänglich gerendert.

## <a name="state-changes"></a>Statusänderungen

<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> benachrichtigt die Komponente, dass sich der Status geändert hat. Wenn dies zutrifft, bewirkt der Aufruf von `StateHasChanged`, dass die Komponente erneut ausgeführt wird.

## <a name="handle-incomplete-async-actions-at-render"></a>Unvollständige Async-Aktionen bei Rendering behandeln

Asynchrone Aktionen, die in Lebenszyklus Ereignissen ausgeführt werden, wurden möglicherweise nicht abgeschlossen, bevor die Komponente gerendert Objekte können `null` oder nicht vollständig mit Daten aufgefüllt werden, während die Lebenszyklus Methode ausgeführt wird. Stellen Sie Renderinglogik bereit, um zu bestätigen, dass Objekte initialisiert Stellen Sie Platzhalter-Benutzeroberflächen Elemente (z. b. eine Lade Nachricht) dar, während Objekte `null`werden.

In der `FetchData` Komponente der Blazor Vorlagen wird `OnInitializedAsync` überschrieben, um Vorhersagedaten (`forecasts`) asynchron zu empfangen. Wenn `forecasts` `null`ist, wird dem Benutzer eine Meldung zum Laden angezeigt. Nachdem der von `OnInitializedAsync` zurückgegebene `Task` abgeschlossen ist, wird die Komponente mit dem aktualisierten Zustand erneut ausgeführt.

*Pages/fetchdata. Razor* in der Blazor Server-Vorlage:

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a>Komponenten Beseitigung mit iverwerf

Wenn eine Komponente <xref:System.IDisposable>implementiert, wird die verwerfen- [Methode](/dotnet/standard/garbage-collection/implementing-dispose) aufgerufen, wenn die Komponente aus der Benutzeroberfläche entfernt wird. In der folgenden Komponente werden `@implements IDisposable` und die `Dispose`-Methode verwendet:

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
> Das Aufrufen von <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> in `Dispose` wird nicht unterstützt. `StateHasChanged` werden möglicherweise als Teil des renderens des Renderers aufgerufen, sodass das Anfordern von Aktualisierungen der Benutzeroberfläche an diesem Punkt nicht unterstützt wird.

## <a name="handle-errors"></a>Behandeln von Fehlern

Informationen zum Behandeln von Fehlern während der Ausführung der Lebenszyklus Methode finden Sie unter <xref:blazor/handle-errors#lifecycle-methods>.
