---
title: Erweiterte ASP.NET Core-Blazor-Szenarios
author: guardrex
description: Informieren Sie sich über erweiterte Szenarios in Blazor einschließlich der Integration manueller RenderTreeBuilder-Logik in eine App.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/advanced-scenarios
ms.openlocfilehash: 9f1e5ea4d883a027f40ac0eccc7a9bba1435139d
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767199"
---
# <a name="aspnet-core-blazor-advanced-scenarios"></a>Erweiterte ASP.NET Core Blazor-Szenarios

Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)

## <a name="blazor-server-circuit-handler"></a>Blazor Server-Verbindungshandler

Blazor Server ermöglicht Code das Definieren eines *Verbindungshandlers*, mit dem Code für Änderungen am Zustand einer Benutzerverbindung ausgeführt werden kann. Ein Verbindungshandler wird durch das Ableiten von `CircuitHandler` und Registrieren der Klasse im Dienstcontainer der App implementiert. Im folgenden Beispiel eines Verbindungshandlers werden geöffnete SignalR-Verbindungen nachverfolgt:

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Server.Circuits;

public class TrackingCircuitHandler : CircuitHandler
{
    private HashSet<Circuit> _circuits = new HashSet<Circuit>();

    public override Task OnConnectionUpAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        _circuits.Add(circuit);

        return Task.CompletedTask;
    }

    public override Task OnConnectionDownAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        _circuits.Remove(circuit);

        return Task.CompletedTask;
    }

    public int ConnectedCircuits => _circuits.Count;
}
```

Verbindungshandler werden mithilfe von DI registriert. Bereichsbezogene Instanzen werden pro Verbindungsinstanz erstellt. Mithilfe von `TrackingCircuitHandler` aus dem vorherigen Beispiel wird ein Singletondienst erstellt, da der Zustand aller Verbindungen nachverfolgt werden muss:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

Wenn die Methoden eines benutzerdefinierten Verbindungshandlers einen Ausnahmefehler auslöst, ist dieser Ausnahmefehler für die Blazor Server-Verbindung schwerwiegend. Umschließen Sie den Code in einer oder mehreren [try-catch](/dotnet/csharp/language-reference/keywords/try-catch)-Anweisungen mit Fehlerbehandlung und Protokollierung, um Ausnahmen im Code oder in aufgerufenen Methoden eines Handlers zu tolerieren.

Wenn eine Verbindung beendet wird, weil ein Benutzer die Verbindung getrennt hat und das Framework den Verbindungsstatus bereinigt, gibt das Framework den DI-Bereich der Verbindung frei. Wenn der Bereich freigegeben wird, werden alle Dienste im Verbindungsbereich verworfen, die <xref:System.IDisposable?displayProperty=fullName> implementieren. Wenn ein DI-Dienst während der Freigabe einen Ausnahmefehler auslöst, protokolliert das Framework die Ausnahme.

## <a name="manual-rendertreebuilder-logic"></a>Manuelle RenderTreeBuilder-Logik

`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` stellt Methoden zum Bearbeiten von Komponenten und Elementen bereit. Dazu gehört auch das manuelle Erstellen von Komponenten in C#-Code.

> [!NOTE]
> Die Verwendung von `RenderTreeBuilder` zum Erstellen von Komponenten ist ein erweitertes Szenario. Eine falsch formatierte Komponente (z. B. ein nicht geschlossenes Markuptag) kann zu undefiniertem Verhalten führen.

Beachten Sie die folgende `PetDetails`-Komponente, die manuell in eine andere Komponente integriert werden kann.

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

Im folgenden Beispiel generiert die Schleife in der `CreateComponent`-Methode drei `PetDetails`-Komponenten. Wenn Sie `RenderTreeBuilder`-Methoden aufrufen, um die Komponenten (`OpenComponent` und `AddAttribute`) zu erstellen, sind die Sequenznummern Zeilennummern des Quellcodes. Der diff-Algorithmus von Blazor basiert auf den Sequenznummern, die unterschiedlichen Codezeilen und nicht unterschiedlichen Aufrufen entsprechen. Hartcodieren Sie die Argumente für Sequenznummern, wenn Sie eine Komponente mit `RenderTreeBuilder`-Methoden erstellen. **Die Verwendung einer Berechnung oder eines Leistungszählers zum Generieren der Sequenznummer kann zu schlechter Leistung führen.** Weitere Informationen finden Sie im Abschnitt [Auf Codezeilennummern und nicht auf die Ausführungsreihenfolge bezogene Sequenznummern](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order).

`BuiltContent`-Komponente:

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
> Die Typen in `Microsoft.AspNetCore.Components.RenderTree` ermöglichen die Verarbeitung der *Ergebnisse* von Renderingvorgängen. Hierbei handelt es sich um interne Details der Blazor-Frameworkimplementierung. Diese Typen sollten als *instabil* betrachtet werden und in zukünftigen Versionen geändert werden.

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>Auf Codezeilennummern und nicht auf die Ausführungsreihenfolge bezogene Sequenznummern

Razor-Komponentendateien ( *.razor*) werden immer kompiliert. Die Kompilierung stellt gegenüber der Interpretation von Code einen Vorteil dar, da der Kompilierungsschritt zum Einfügen von Informationen verwendet werden kann, die die App-Leistung zur Laufzeit erhöhen können.

Ein wichtiges Beispiel für diese Verbesserungen sind *Sequenznummern*. Sequenznummern geben der Laufzeit an, welche Ausgaben aus den unterschiedlichen und geordneten Codezeilen stammen. Diese Informationen werden von der Laufzeit verwendet, um effiziente und zeitlich lineare Strukturunterschiede zu generieren. Diese Methode ist viel schneller als es für einen allgemeinen diff-Strukturalgorithmus normalerweise möglich ist.

Sehen Sie sich die folgende Razor-Komponentendatei ( *.razor*) an:

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

Wenn der Code zum ersten Mal ausgeführt wird, erhält der Generator Folgendes, wenn `someFlag` `true` ist:

| Sequenz | Geben Sie Folgendes ein:      | Daten   |
| :------: | --------- | :----: |
| 0        | Textknoten | First  |
| 1        | Textknoten | Second |

Stellen Sie sich vor, dass `someFlag` `false` wird und das Markup wieder gerendert wird. Dieses Mal empfängt der Generator Folgendes:

| Sequenz | Geben Sie Folgendes ein:       | Daten   |
| :------: | ---------- | :----: |
| 1        | Textknoten  | Second |

Wenn die Laufzeit einen diff-Algorithmus ausführt, wird angezeigt, dass das Element bei Sequenz `0` entfernt wurde. Daraufhin wird das folgende triviale *Bearbeitungsskript* generiert:

* Entfernen Sie den ersten Textknoten.

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a>Problem beim programmgesteuerten Generieren von Sequenznummern

Angenommen, Sie haben stattdessen die folgende Buildlogik für die Renderstruktur geschrieben:

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

Die erste Ausgabe lautet nun wie folgt:

| Sequenz | Geben Sie Folgendes ein:      | Daten   |
| :------: | --------- | :----: |
| 0        | Textknoten | First  |
| 1        | Textknoten | Second |

Dieses Ergebnis ist mit dem des vorherigen Falls identisch, sodass keine negativen Probleme aufgetreten sind. `someFlag` ist beim zweiten Rendering `false`, und die Ausgabe lautet wie folgt:

| Sequenz | Geben Sie Folgendes ein:      | Daten   |
| :------: | --------- | ------ |
| 0        | Textknoten | Second |

Dieses Mal zeigt der diff-Algorithmus an, dass *zwei* Änderungen aufgetreten sind, und der Algorithmus generiert das folgende Bearbeitungsskript:

* Ändern Sie den Wert des ersten Textknotens in `Second`.
* Entfernen Sie den zweiten Textknoten.

Durch das Erzeugen der Sequenznummern sind alle nützlichen Informationen dazu verloren gegangen, wo die `if/else`-Branches und -Schleifen im ursprünglichen Code vorhanden waren. Dies führt zu einem **doppelt so langen diff** wie zuvor.

Dies ist ein triviales Beispiel. In realistischeren Fällen mit komplexen und tief geschachtelten Strukturen und vor allem mit Schleifen sind die Leistungskosten in der Regel höher. Anstatt sofort festzustellen, welche Schleifenblöcke oder Branches eingefügt oder entfernt wurden, muss der diff-Algorithmus eine tiefe Rekursion der Renderstrukturen durchführen. Dies führt in der Regel dazu, dass längere Bearbeitungsskripts erstellt werden müssen, da der diff-Algorithmus falsch informiert ist, wie sich die alten und neuen Strukturen aufeinander beziehen.

### <a name="guidance-and-conclusions"></a>Anleitungen und Schlussfolgerungen

* Die App-Leistung leidet, wenn Sequenznummern dynamisch generiert werden.
* Das Framework kann zur Laufzeit automatisch keine eigenen Sequenznummern erstellen, da die erforderlichen Informationen nicht vorhanden sind, es sei denn, sie werden zur Kompilierzeit aufgezeichnet.
* Schreiben Sie keine langen Blöcke manuell implementierter `RenderTreeBuilder`-Logik. Bevorzugen Sie *.razor*-Dateien, und ermöglichen Sie es dem Compiler, die Sequenznummern zu verarbeiten. Wenn Sie manuelle `RenderTreeBuilder`-Logik nicht vermeiden können, teilen Sie lange Codeblöcke in kleinere Teile auf, die in `OpenRegion`/`CloseRegion`-Aufrufe eingebunden werden. Jede Region verfügt über einen eigenen separaten Bereich von Sequenznummern, sodass Sie in jeder Region von 0 (Null; oder von jeder anderen beliebigen Zahl) aus starten können.
* Wenn Sequenznummern hartcodiert sind, erfordert der diff-Algorithmus nur, dass die Sequenznummern den Wert erhöhen. Die Anfangswerte und Lücken sind irrelevant. Eine legitime Option besteht darin, die Codezeilennummer als Sequenznummer zu verwenden oder von 0 (Null) aus zu starten und Werte in Einer- oder Hunderterschritten (oder einem beliebigen bevorzugten Intervall) zu erhöhen. 
* Blazor verwendet Sequenznummern, während andere Benutzeroberflächenframeworks diese nicht für Verzeichnisvergleiche verwenden. Das Vergleichen ist weitaus schneller, wenn Sequenznummern verwendet werden. Außerdem hat Blazor den Vorteil eines Kompilierungsschritts, der Sequenznummern automatisch für Entwickler verarbeitet, die *.razor*-Dateien erstellen.

## <a name="perform-large-data-transfers-in-blazor-server-apps"></a>Ausführen umfangreicher Datenübertragungen in Blazor Server-Apps

In einigen Szenarios müssen große Datenmengen zwischen JavaScript und Blazorübertragen werden. In der Regel erfolgen große Datenübertragungen in den folgenden Fällen:

* Dateisystem-APIs des Browsers werden für das Hochladen oder Herunterladen einer Datei verwendet.
* Es ist eine Interaktion mit einer Drittanbieterbibliothek erforderlich.

In Blazor Server gibt es eine Einschränkung, um zu verhindern, dass einzelne große Nachrichten übergeben werden, die zu Leistungsproblemen führen können.

Beachten Sie die folgenden Anleitungen, wenn Sie Code zum Übertragen von Daten zwischen JavaScript und Blazor entwickeln:

* Segmentieren Sie die Daten in kleinere Teile, und senden Sie die Datensegmente sequenziell, bis alle Daten vom Server empfangen wurden.
* Ordnen Sie in JavaScript- und C#-Code keine großen Objekte zu.
* Blockieren Sie den hauptsächlichen Benutzeroberflächenthread nicht für lange Zeiträume, wenn Sie Daten senden oder empfangen.
* Geben Sie belegten Arbeitsspeicher frei, wenn der Prozess abgeschlossen oder abgebrochen wird.
* Erzwingen Sie die folgenden zusätzlichen Anforderungen aus Sicherheitsgründen:
  * Deklarieren Sie die maximale Datei- oder Datengröße, die übermittelt werden kann.
  * Deklarieren Sie die minimale Uploadrate vom Client an den Server.
* Nachdem die Daten vom Server empfangen wurden, ist mit den Daten Folgendes möglich:
  * Sie können temporär in einem Speicherpuffer gespeichert werden, bis alle Segmente gesammelt wurden.
  * Sie können sofort verarbeitet werden. Beispielsweise können die Daten sofort in einer Datenbank gespeichert oder auf den Datenträger geschrieben werden, wenn die einzelnen Segmente empfangen werden.

Die folgende Dateiuploaderklasse verarbeitet JS Interop mit dem Client. Die Uploaderklasse verwendet JS Interop für Folgendes:

* Abrufen des Clients, um ein Datensegment zu senden
* Abbrechen der Transaktion, wenn ein Timeout für den Abruf auftritt

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

Im vorherigen Beispiel:

* `_maxBase64SegmentSize` wird auf `8192` festgelegt. Dies wird folgendermaßen berechnet: `_maxBase64SegmentSize = _segmentSize * 4 / 3`.
* Die .NET Core-APIs für die Speicherverwaltung auf niedriger Ebene werden zum Speichern der Speichersegmente auf dem Server in `_uploadedSegments` verwendet.
* Eine `ReceiveFile`-Methode wird verwendet, um den Upload über JS Interop zu verarbeiten:
  * Die Dateigröße wird in Bytes über JS Interop mit `_jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)` angegeben.
  * Die Anzahl der zu empfangenden Segmente wird berechnet und in `numberOfSegments`gespeichert.
  * Die Segmente werden in einer `for`-Schleife durch JS Interop mit `_jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)` angefordert. Alle Segmente bis auf das letzte müssen vor dem Decodieren eine Größe von 8.192 Bytes haben. Der Client wird gezwungen, die Daten auf effiziente Weise zu senden.
  * Für jedes empfangene Segment werden vor der Decodierung mit <xref:System.Convert.TryFromBase64String*> Überprüfungen ausgeführt.
  * Ein Datenstrom mit den Daten wird nach Abschluss des Uploads als neuer <xref:System.IO.Stream> (`SegmentedStream`) zurückgegeben.

Die segmentierte Streamklasse macht die Liste der Segmente als schreibgeschützten, nicht suchbaren <xref:System.IO.Stream> verfügbar:

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

Der folgende Code implementiert JavaScript-Funktionen, um die Daten zu empfangen:

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
