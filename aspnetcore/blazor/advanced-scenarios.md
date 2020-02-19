---
title: ASP.net Core Blazor Erweiterte Szenarien
author: guardrex
description: Erfahren Sie mehr über erweiterte Szenarien in Blazor, einschließlich der Integration von manueller rendertreebuilder-Logik in eine APP.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/advanced-scenarios
ms.openlocfilehash: 5e0618faa7b1b5e4cc15e30d9c16afaf7ccabaf0
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/19/2020
ms.locfileid: "77453181"
---
# <a name="aspnet-core-blazor-advanced-scenarios"></a>Erweiterte ASP.net Core blazor-Szenarios

Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)

## <a name="manual-rendertreebuilder-logic"></a>Manuelle rendertreebuilder-Logik

`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` stellt Methoden zum Bearbeiten von Komponenten und Elementen bereit, einschließlich des manuellen C# erbauens von Komponenten im Code.

> [!NOTE]
> Die Verwendung von `RenderTreeBuilder` zum Erstellen von Komponenten ist ein erweitertes Szenario. Eine falsch formatierte Komponente (z. b. ein nicht geschlossenes Markup Kennzeichen) kann zu undefiniertem Verhalten führen.

Beachten Sie die folgende `PetDetails`-Komponente, die manuell in eine andere-Komponente integriert werden kann:

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

Im folgenden Beispiel generiert die-Schleife in der `CreateComponent`-Methode drei `PetDetails`-Komponenten. Wenn Sie `RenderTreeBuilder` Methoden aufrufen, um die Komponenten (`OpenComponent` und `AddAttribute`) zu erstellen, sind die Sequenznummern Quell Codezeilen Nummern. Der blazor Difference-Algorithmus basiert auf den Sequenznummern, die unterschiedlichen Codezeilen entsprechen, nicht unterschiedlichen Aufruf aufrufen. Wenn Sie eine Komponente mit `RenderTreeBuilder`-Methoden erstellen, hart codieren Sie die Argumente für Sequenznummern. **Die Verwendung einer Berechnung oder eines Leistungs Zählers, um die Sequenznummer zu generieren, kann zu schlechter Leistung führen.** Weitere Informationen finden Sie im Abschnitt [Sequenznummern im Zusammenhang mit Codezeilen Nummern und nicht in der Ausführungsreihenfolge](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) .

`BuiltContent` Komponente:

```razor
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

> [!WARNING]
> Die Typen in `Microsoft.AspNetCore.Components.RenderTree` die Verarbeitung der *Ergebnisse* von Renderingvorgängen zulassen. Hierbei handelt es sich um interne Details der blazor-Framework-Implementierung. Diese Typen sollten als *instabil* eingestuft werden und in zukünftigen Versionen geändert werden können.

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>Sequenznummern beziehen sich auf Codezeilen Nummern und keine Ausführungsreihenfolge.

Razor-Komponenten Dateien *(Razor*) werden immer kompiliert. Die Kompilierung ist ein potenzieller Vorteil gegenüber der Interpretation von Code, da der Kompilierungs Schritt zum Einfügen von Informationen verwendet werden kann, die die APP-Leistung zur Laufzeit erhöhen

Ein wichtiges Beispiel für diese Verbesserungen sind *Sequenznummern*. Sequenznummern geben der Laufzeit an, welche Ausgaben aus den unterschiedlichen und geordneten Codezeilen stammen. Diese Informationen werden von der Common Language Runtime verwendet, um effiziente Strukturunterschiede in linearer Zeit zu generieren, die wesentlich schneller ist als bei einem allgemeinen Struktur Vergleichsalgorithmus.

Sehen Sie sich die folgende Razor-Komponenten Datei ( *. Razor*) an:

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

Der vorangehende Code wird in etwa wie folgt kompiliert:

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

Wenn der Code zum ersten Mal ausgeführt wird, erhält der Generator Folgendes, wenn `someFlag` `true`ist:

| Sequenz | Typ      | Daten   |
| :------: | --------- | :----: |
| 0        | Textknoten | First  |
| 1        | Textknoten | Sekunde |

Stellen Sie sich vor, dass `someFlag` `false`wird und das Markup wieder gerendert wird. Dieses Mal empfängt der Generator Folgendes:

| Sequenz | Typ       | Daten   |
| :------: | ---------- | :----: |
| 1        | Textknoten  | Sekunde |

Wenn die Laufzeit einen diff ausführt, wird angezeigt, dass das Element bei Sequenz `0` entfernt wurde, sodass das folgende triviale *Bearbeitungs Skript*generiert wird:

* Entfernen Sie den ersten Textknoten.

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a>Das Problem beim programmgesteuerten Erstellen von Sequenznummern

Angenommen, Sie haben die folgende Logik des Renderstruktur-Generators geschrieben:

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

Nun lautet die erste Ausgabe wie folgt:

| Sequenz | Typ      | Daten   |
| :------: | --------- | :----: |
| 0        | Textknoten | First  |
| 1        | Textknoten | Sekunde |

Dieses Ergebnis ist mit dem vorherigen Fall identisch, sodass keine negativen Probleme aufgetreten sind. `someFlag` wird im zweiten Rendering `false`, und die Ausgabe lautet:

| Sequenz | Typ      | Daten   |
| :------: | --------- | ------ |
| 0        | Textknoten | Sekunde |

Dieses Mal sieht der Vergleichsalgorithmus, dass *zwei* Änderungen aufgetreten sind, und der Algorithmus generiert das folgende Bearbeitungs Skript:

* Ändern Sie den Wert für den ersten Textknoten in `Second`.
* Entfernen Sie den zweiten Textknoten.

Das Erzeugen der Sequenznummern hat alle nützlichen Informationen dazu verloren, wo die `if/else` branches und Schleifen im ursprünglichen Code vorhanden waren. Dies führt **doppelt so lange** wie zuvor zu einem diff.

Dies ist ein sehr einfaches Beispiel. In realistischeren Fällen mit komplexen und tief geschachtelten Strukturen und vor allem mit Schleifen sind die Leistungskosten in der Regel höher. Anstatt sofort festzustellen, welche Schleifen Blöcke oder Verzweigungen eingefügt oder entfernt wurden, muss der diff-Algorithmus tief in die renderstrukturen rekurdieren. Dies führt in der Regel dazu, dass längere Bearbeitungs Skripts erstellt werden müssen, da der diff-Algorithmus falsch formatiert ist, wie sich die alten und neuen Strukturen voneinander unterscheiden.

### <a name="guidance-and-conclusions"></a>Anleitungen und Schlussfolgerungen

* Die APP-Leistung leidet, wenn Sequenznummern dynamisch generiert werden.
* Das Framework kann zur Laufzeit automatisch keine eigenen Sequenznummern erstellen, da die erforderlichen Informationen nicht vorhanden sind, es sei denn, Sie werden zur Kompilierzeit aufgezeichnet.
* Schreiben Sie keine langen Blöcke der manuell implementierten `RenderTreeBuilder` Logik. Bevorzugen Sie *Razor* -Dateien, und ermöglichen Sie es dem Compiler, sich mit den Sequenznummern zu beschäftigen. Wenn Sie manuelle `RenderTreeBuilder` Logik nicht vermeiden können, teilen Sie lange Code Blöcke in kleinere Teile auf, die `OpenRegion`/`CloseRegion` aufrufen umschließen. Jede Region verfügt über einen eigenen separaten Bereich von Sequenznummern, sodass Sie in jeder Region von NULL (oder einer beliebigen anderen beliebigen Zahl) neu starten können.
* Wenn Sequenznummern hart codiert sind, erfordert der Vergleichsalgorithmus nur, dass die Sequenznummern den Wert erhöhen. Die Anfangswerte und Lücken sind irrelevant. Eine berechtigte Option besteht darin, die Codezeilen Nummer als Sequenznummer zu verwenden oder von NULL zu beginnen und um Werte oder Hunderte (oder ein beliebiges bevorzugtes Intervall) zu erhöhen. 
* Blazor verwendet Sequenznummern, während andere Benutzeroberflächen-Frameworks, die Sie nicht verwenden, diese verwenden. Das diffalling ist weitaus schneller, wenn Sequenznummern verwendet werden, und Blazor hat den Vorteil eines Kompilierungs Schritts, der automatisch Sequenznummern für Entwickler erstellt, die *Razor* -Dateien erstellen.
