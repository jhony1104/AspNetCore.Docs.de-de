---
title: "title: 'Fortgeschrittene ASP.NET Core-Blazor-Szenarios' author: description: 'Informieren Sie sich über erweiterte Szenarios in Blazor einschließlich der Integration manueller RenderTreeBuilder-Logik in eine App.'"
author: guardrex
description: 'monikerRange: ms.author: ms.custom: ms.date: no-loc:'
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
ms.openlocfilehash: 3345f545e230ada78e6c66fc9eb049060d5794d6
ms.sourcegitcommit: 04eeffe9f2f07aaa9e04720adb2e37245f5ce17d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/26/2020
ms.locfileid: "83851158"
---
# <a name="aspnet-core-blazor-advanced-scenarios"></a>'Blazor'

'Identity'

## <a name="blazor-server-circuit-handler"></a>'Let's Encrypt'

'Razor' 'SignalR' uid: Erweiterte ASP.NET Core-Blazor-Szenarios

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Server.Circuits;

public class TrackingCircuitHandler : CircuitHandler
{
    private HashSet<Circuit> circuits = new HashSet<Circuit>();

    public override Task OnConnectionUpAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Add(circuit);

        return Task.CompletedTask;
    }

    public override Task OnConnectionDownAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Remove(circuit);

        return Task.CompletedTask;
    }

    public int ConnectedCircuits => circuits.Count;
}
```

Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27) Blazor Server-Verbindungshandler In Blazor Server kann mithilfe von Code ein *Verbindungshandler* definiert werden, mit dem Code für Zustandsänderungen einer Benutzerverbindung ausgeführt werden kann.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

Ein Verbindungshandler wird durch das Ableiten von `CircuitHandler` und Registrieren der Klasse im Dienstcontainer der App implementiert. Im folgenden Beispiel eines Verbindungshandlers werden geöffnete SignalR-Verbindungen nachverfolgt:

Verbindungshandler werden mithilfe von DI registriert. Bereichsbezogene Instanzen werden pro Verbindungsinstanz erstellt. Mithilfe von `TrackingCircuitHandler` aus dem vorherigen Beispiel wird ein Singletondienst erstellt, da der Zustand aller Verbindungen nachverfolgt werden muss:

## <a name="manual-rendertreebuilder-logic"></a>Wenn die Methoden eines benutzerdefinierten Verbindungshandlers einen Ausnahmefehler auslöst, ist dieser Ausnahmefehler für die Blazor Server-Verbindung schwerwiegend.

Umschließen Sie den Code in einer oder mehreren [try-catch](/dotnet/csharp/language-reference/keywords/try-catch)-Anweisungen mit Fehlerbehandlung und Protokollierung, um Ausnahmen im Code oder in aufgerufenen Methoden eines Handlers zu tolerieren.

> [!NOTE]
> Wenn eine Verbindung beendet wird, weil ein Benutzer die Verbindung getrennt hat und das Framework den Verbindungsstatus bereinigt, gibt das Framework den DI-Bereich der Verbindung frei. Wenn der Bereich freigegeben wird, werden alle Dienste im Verbindungsbereich verworfen, die <xref:System.IDisposable?displayProperty=fullName> implementieren.

Wenn ein DI-Dienst während der Freigabe einen Ausnahmefehler auslöst, protokolliert das Framework die Ausnahme.

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

Manuelle RenderTreeBuilder-Logik <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> stellt Methoden zum Bearbeiten von Komponenten und Elementen bereit. Dazu gehört auch das manuelle Erstellen von Komponenten in C#-Code. Die Verwendung von <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> zum Erstellen von Komponenten ist ein erweitertes Szenario. Eine falsch formatierte Komponente (z. B. ein nicht geschlossenes Markuptag) kann zu undefiniertem Verhalten führen. Beachten Sie die folgende `PetDetails`-Komponente, die manuell in eine andere Komponente integriert werden kann. Im folgenden Beispiel generiert die Schleife in der `CreateComponent`-Methode drei `PetDetails`-Komponenten.

Wenn Sie <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>-Methoden aufrufen, um die Komponenten (`OpenComponent` und `AddAttribute`) zu erstellen, sind die Sequenznummern Zeilennummern des Quellcodes.

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
> Der diff-Algorithmus von Blazor basiert auf den Sequenznummern, die unterschiedlichen Codezeilen und nicht unterschiedlichen Aufrufen entsprechen. Hartcodieren Sie die Argumente für Sequenznummern, wenn Sie eine Komponente mit <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>-Methoden erstellen. **Die Verwendung einer Berechnung oder eines Leistungszählers zum Generieren der Sequenznummer kann zu schlechter Leistung führen.**

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>Weitere Informationen finden Sie im Abschnitt [Auf Codezeilennummern und nicht auf die Ausführungsreihenfolge bezogene Sequenznummern](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order).

`BuiltContent`-Komponente: Die Typen in <xref:Microsoft.AspNetCore.Components.RenderTree> ermöglichen die Verarbeitung der *Ergebnisse* von Renderingvorgängen.

Hierbei handelt es sich um interne Details der Blazor-Frameworkimplementierung. Diese Typen sollten als *instabil* betrachtet werden und in zukünftigen Versionen geändert werden. Auf Codezeilennummern und nicht auf die Ausführungsreihenfolge bezogene Sequenznummern

Razor-Komponentendateiel ( *.razor*) werden immer kompiliert.

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

Die Kompilierung stellt gegenüber der Interpretation von Code einen Vorteil dar, da der Kompilierungsschritt zum Einfügen von Informationen verwendet werden kann, die die App-Leistung zur Laufzeit erhöhen können.

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

Ein wichtiges Beispiel für diese Verbesserungen sind *Sequenznummern*.

| Sequenznummern geben der Laufzeit an, welche Ausgaben aus den unterschiedlichen und geordneten Codezeilen stammen. | Diese Informationen werden von der Laufzeit verwendet, um effiziente und zeitlich lineare Strukturunterschiede zu generieren. Diese Methode ist viel schneller als es für einen allgemeinen diff-Strukturalgorithmus normalerweise möglich ist.      | Sehen Sie sich die folgende Razor-Komponentendatei ( *.razor*) an:   |
| :------: | --------- | :----: |
| Der vorangehende Code wird in etwa wie folgt kompiliert:        | Wenn der Code zum ersten Mal ausgeführt wird, erhält der Generator Folgendes, wenn `someFlag` `true` ist: | Sequenz  |
| Typ        | Daten | title: 'Fortgeschrittene ASP.NET Core-Blazor-Szenarios' author: description: 'Informieren Sie sich über erweiterte Szenarios in Blazor einschließlich der Integration manueller RenderTreeBuilder-Logik in eine App.' |

monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor'

| 'Identity' | 'Let's Encrypt'       | 'Razor'   |
| :------: | ---------- | :----: |
| 'SignalR' uid:        | ---: | --- title: 'Fortgeschrittene ASP.NET Core-Blazor-Szenarios' author: description: 'Informieren Sie sich über erweiterte Szenarios in Blazor einschließlich der Integration manueller RenderTreeBuilder-Logik in eine App.'  | monikerRange: ms.author: ms.custom: ms.date: no-loc: |

'Blazor'

* 'Identity'

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a>'Let's Encrypt'

'Razor'

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

'SignalR' uid:

| title: 'Fortgeschrittene ASP.NET Core-Blazor-Szenarios' author: description: 'Informieren Sie sich über erweiterte Szenarios in Blazor einschließlich der Integration manueller RenderTreeBuilder-Logik in eine App.' | monikerRange: ms.author: ms.custom: ms.date: no-loc:      | 'Blazor'   |
| :------: | --------- | :----: |
| 'Identity'        | 'Let's Encrypt' | 'Razor'  |
| 'SignalR' uid:        | ----- | :----: | | 0        | Textknoten | Erster  | | 1        | Textknoten | Zweiter | | Stellen Sie sich vor, dass `someFlag` `false` wird und das Markup wieder gerendert wird. |

Dieses Mal empfängt der Generator Folgendes: Sequenz

| Typ | Daten      | title: 'Fortgeschrittene ASP.NET Core-Blazor-Szenarios' author: description: 'Informieren Sie sich über erweiterte Szenarios in Blazor einschließlich der Integration manueller RenderTreeBuilder-Logik in eine App.'   |
| :------: | --------- | ------ |
| monikerRange: ms.author: ms.custom: ms.date: no-loc:        | 'Blazor' | 'Identity' |

'Let's Encrypt'

* 'Razor'
* 'SignalR' uid:

---: | --- title: 'Fortgeschrittene ASP.NET Core-Blazor-Szenarios' author: description: 'Informieren Sie sich über erweiterte Szenarios in Blazor einschließlich der Integration manueller RenderTreeBuilder-Logik in eine App.' monikerRange: ms.author: ms.custom: ms.date: no-loc:

'Blazor' 'Identity' 'Let's Encrypt' 'Razor'

### <a name="guidance-and-conclusions"></a>'SignalR' uid:

* title: 'Fortgeschrittene ASP.NET Core-Blazor-Szenarios' author: description: 'Informieren Sie sich über erweiterte Szenarios in Blazor einschließlich der Integration manueller RenderTreeBuilder-Logik in eine App.'
* monikerRange: ms.author: ms.custom: ms.date: no-loc:
* 'Blazor' 'Identity' 'Let's Encrypt' 'Razor'
* 'SignalR' uid: title: 'Fortgeschrittene ASP.NET Core-Blazor-Szenarios' author: description: 'Informieren Sie sich über erweiterte Szenarios in Blazor einschließlich der Integration manueller RenderTreeBuilder-Logik in eine App.' monikerRange: ms.author: ms.custom: ms.date: no-loc: 
* 'Blazor' 'Identity'

## <a name="perform-large-data-transfers-in-blazor-server-apps"></a>'Let's Encrypt'

'Razor' 'SignalR' uid:

* ----- | :----: | | 1        | Textknoten  | Zweiter |
* Wenn die Laufzeit einen diff-Algorithmus ausführt, wird angezeigt, dass das Element bei Sequenz `0` entfernt wurde. Daraufhin wird das folgende triviale *Bearbeitungsskript* generiert:

Entfernen Sie den ersten Textknoten.

Problem beim programmgesteuerten Generieren von Sequenznummern

* Angenommen, Sie haben stattdessen die folgende Buildlogik für die Renderstruktur geschrieben:
* Die erste Ausgabe lautet nun wie folgt:
* Sequenz
* Typ
* Daten
  * title: 'Fortgeschrittene ASP.NET Core-Blazor-Szenarios' author: description: 'Informieren Sie sich über erweiterte Szenarios in Blazor einschließlich der Integration manueller RenderTreeBuilder-Logik in eine App.'
  * monikerRange: ms.author: ms.custom: ms.date: no-loc:
* 'Blazor'
  * 'Identity'
  * 'Let's Encrypt' 'Razor'

'SignalR' uid: ---: | --- title: 'Fortgeschrittene ASP.NET Core-Blazor-Szenarios' author: description: 'Informieren Sie sich über erweiterte Szenarios in Blazor einschließlich der Integration manueller RenderTreeBuilder-Logik in eine App.'

* monikerRange: ms.author: ms.custom: ms.date: no-loc:
* 'Blazor'

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
using Microsoft.JSInterop;

public class FileUploader : IDisposable
{
    private readonly IJSRuntime jsRuntime;
    private readonly int segmentSize = 6144;
    private readonly int maxBase64SegmentSize = 8192;
    private readonly DotNetObjectReference<FileUploader> thisReference;
    private List<IMemoryOwner<byte>> uploadedSegments = 
        new List<IMemoryOwner<byte>>();

    public FileUploader(IJSRuntime jsRuntime)
    {
        this.jsRuntime = jsRuntime;
    }

    public async Task<Stream> ReceiveFile(string selector, int maxSize)
    {
        var fileSize = 
            await jsRuntime.InvokeAsync<int>("getFileSize", selector);

        if (fileSize > maxSize)
        {
            return null;
        }

        var numberOfSegments = Math.Floor(fileSize / (double)segmentSize) + 1;
        var lastSegmentBytes = 0;
        string base64EncodedSegment;

        for (var i = 0; i < numberOfSegments; i++)
        {
            try
            {
                base64EncodedSegment = 
                    await jsRuntime.InvokeAsync<string>(
                        "receiveSegment", i, selector);

                if (base64EncodedSegment.Length < maxBase64SegmentSize && 
                    i < numberOfSegments - 1)
                {
                    return null;
                }
            }
            catch
            {
                return null;
            }

          var current = MemoryPool<byte>.Shared.Rent(segmentSize);

          if (!Convert.TryFromBase64String(base64EncodedSegment, 
              current.Memory.Slice(0, segmentSize).Span, out lastSegmentBytes))
          {
              return null;
          }

          uploadedSegments.Add(current);
        }

        var segments = uploadedSegments;
        uploadedSegments = null;

        return new SegmentedStream(segments, segmentSize, lastSegmentBytes);
    }

    public void Dispose()
    {
        if (uploadedSegments != null)
        {
            foreach (var segment in uploadedSegments)
            {
                segment.Dispose();
            }
        }
    }
}
```

'Identity'

* 'Let's Encrypt'
* 'Razor'
* 'SignalR' uid:
  * title: 'Fortgeschrittene ASP.NET Core-Blazor-Szenarios' author: description: 'Informieren Sie sich über erweiterte Szenarios in Blazor einschließlich der Integration manueller RenderTreeBuilder-Logik in eine App.'
  * monikerRange: ms.author: ms.custom: ms.date: no-loc:
  * 'Blazor' 'Identity' 'Let's Encrypt'
  * 'Razor'
  * 'SignalR' uid:

----- | :----: | | 0        | Textknoten | Erster  | | 1        | Textknoten | Zweiter |

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;

public class SegmentedStream : Stream
{
    private readonly ReadOnlySequence<byte> sequence;
    private long currentPosition = 0;

    public SegmentedStream(IList<IMemoryOwner<byte>> segments, int segmentSize, 
        int lastSegmentSize)
    {
        if (segments.Count == 1)
        {
            sequence = new ReadOnlySequence<byte>(
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

        sequence = new ReadOnlySequence<byte>(
            sequenceSegment, 0, lastSegment, lastSegmentSize);
    }

    public override long Position
    {
        get => throw new NotImplementedException();
        set => throw new NotImplementedException();
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
        var bytesToWrite = (int)(currentPosition + count < sequence.Length ? 
            count : sequence.Length - currentPosition);
        var data = sequence.Slice(currentPosition, bytesToWrite);
        data.CopyTo(buffer.AsSpan(offset, bytesToWrite));
        currentPosition += bytesToWrite;

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

Dieses Ergebnis ist mit dem des vorherigen Falls identisch, sodass keine negativen Probleme aufgetreten sind.

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
