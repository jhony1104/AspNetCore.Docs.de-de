---
title: Blazor-Zustandsverwaltung in ASP.NET Core
author: guardrex
description: Erfahren Sie, wie Sie den Zustand in Blazor-Server-Apps beibehalten.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/state-management
ms.openlocfilehash: 5e14a0697fbc98575970b93dfa12c68e9f561c56
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967414"
---
# <a name="aspnet-core-blazor-state-management"></a>Blazor-Zustandsverwaltung in ASP.NET Core

Von [Steve Sanderson](https://github.com/SteveSandersonMS)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor Server ist ein zustandsbehaftetes App-Framework. In den meisten Fällen behält die App eine laufende Verbindung mit dem Server bei. Der Benutzerzustand wird in einer *Verbindung* im Speicher des Servers gespeichert. 

Beispiele für den Zustand einer Benutzerverbindung sind:

* Die gerenderte Benutzeroberfläche – die Hierarchie der Komponenteninstanzen und deren neueste Renderausgabe.
* Die Werte aller Felder und Eigenschaften in Komponenteninstanzen.
* Daten, die in [-Abhängigkeitsinjektion (DI)](xref:fundamentals/dependency-injection)-Dienstinstanzen gespeichert sind, die auf die Verbindung beschränkt sind.

> [!NOTE]
> In diesem Artikel wird die Zustandspersistenz in Blazor Server-Apps behandelt. Blazor-Webassemblyanwendungen können [clientseitige Zustandspersistenz im Browser nutzen](#client-side-in-the-browser), erfordern jedoch benutzerdefinierte Lösungen oder Drittanbieterpakete, die den Rahmen dieses Artikels sprengen würden.

## <a name="blazor-circuits"></a>Blazor-Verbindungen

Wenn die Netzwerkverbindung vorübergehend getrennt wird, versucht Blazor, den Benutzer nochmals mit der ursprünglichen Verbindung zu verbinden, damit er die mit der Verwendung der App fortfahren kann. Es ist jedoch nicht immer möglich, einen Benutzer noch mal mit der ursprünglichen Verbindung im Arbeitsspeicher des Servers zu verbinden:

* Der Server kann eine getrennte Verbindung nicht dauerhaft beibehalten. Der Server muss eine getrennte Verbindung nach einem Timeout freigeben, oder wenn der Server nicht über genügend Arbeitsspeicher verfügt.
* In Bereitstellungsumgebungen mit mehreren Servern und Lastenausgleich kann es vorkommen, dass ein Server, der Anforderungen verarbeitet, zu einem bestimmten Zeitpunkt nicht mehr verfügbar ist. Bei einzelnen Servern tritt möglicherweise ein Fehler auf, oder sie werden automatisch entfernt, wenn nicht mehr das gesamte Anforderungsvolumen verarbeitet werden muss. Der ursprüngliche Server ist möglicherweise nicht verfügbar, wenn der Benutzer versucht, noch mal eine Verbindung herzustellen.
* Der Benutzer kann den Browser schließen und noch mal öffnen oder die Seite erneut laden, wodurch alle Zustände im Arbeitsspeicher des Browsers entfernt werden. So gehen beispielsweise Werte, die durch JavaScript-Interoperabilitätsaufrufe festgelegt wurden, verloren.

Wenn ein Benutzer nicht erneut mit der ursprünglichen Verbindung verbunden werden kann, erhält der Benutzer eine neue Verbindung mit einem leeren Zustand. Dies entspricht dem Schließen und erneuten Öffnen einer Desktop-App.

## <a name="preserve-state-across-circuits"></a>Beibehaltung von zustandsübergreifenden Verbindungen

In einigen Szenarios ist die Beibehaltung des Zustands über mehrere Verbindungen wünschenswert. Eine App kann in folgenden Fällen wichtige Daten für einen Benutzer beibehalten:

* Der Webserver ist nicht mehr verfügbar.
* Der Browser des Benutzers ist gezwungen, eine neue Verbindung mit einem neuen Webserver zu starten.

Im Allgemeinen gilt: die Beibehaltung des Zustands über mehrere Verbindungen hinweg erfolgt in Szenarios, in denen Benutzer aktiv Daten erstellen und nicht nur Daten lesen, die bereits vorhanden sind.

Um den Zustand über eine einzelne Verbindung hinaus beizubehalten, *speichern Sie die Daten nicht nur im Arbeitsspeicher des Servers*. Die App muss die Daten an einem anderen Speicherort speichern. Die Zustandspersistenz erfolgt nicht automatisch – Sie müssen bei der Entwicklung der App Schritte ausführen, um zustandsbehaftete Datenpersistenz zu implementieren.

Die Datenpersistenz ist in der Regel nur für Zustände mit hohem Wert erforderlich, die von den Benutzern mit großem Aufwand erstellt wurden. In den folgenden Beispielen werden durch die Beibehaltung des Zustands Zeit- oder Hilfsmitteleinsparungen in kommerziellen Aktivitäten erzielt:

* Aus mehreren Schritten bestehendes WebForm &ndash; es ist für den Benutzer zeitaufwändig, Daten für mehrere abgeschlossene Schritte eines mehrstufigen Prozesses wiederholt einzugeben, wenn der Zustand nicht mehr vorhanden ist. Der Benutzer verliert in diesem Szenario seinen Zustand, wenn er vom mehrstufigen Formular weg navigiert und später zum Formular zurückkehrt.
* Warenkorb &ndash; kommerziell wichtige Komponenten einer App, die potenzielle Umsätze ermöglichen, können beibehalten werden. Ein Benutzer, der seinen Zustand verliert und dessen Warenkorb dadurch gelöscht wird, kann weniger Produkte oder Dienste kaufen, wenn er zu einem späteren Zeitpunkt zur Website zurückkehrt.

Es ist in der Regel nicht notwendig, einen Zustand beizubehalten, der einfach erneut erstellt werden kann, z. B. den in ein Anmeldedialogfeld eingegebenen Benutzernamen, der nicht übermittelt wurde.

> [!IMPORTANT]
> Eine App kann nur den *App-Zustand* beibehalten. Benutzeroberflächen wie Komponenteninstanzen und deren Renderingstrukturen können nicht beibehalten werden. Komponenten und Renderingstrukturen sind in der Regel nicht serialisierbar. Um eine Art von Benutzeroberflächenzustand, z. B. von den erweiterten Knoten einer Strukturansicht, zu erhalten, muss die App über benutzerdefinierten Code verfügen, um das Verhalten als serialisierbaren App-Zustand modellieren zu können.

## <a name="where-to-persist-state"></a>Speicherort des Zustands

Es gibt drei allgemeine Speicherorte für die Beibehaltung des Zustands in einer Blazor Server-App. Die Ansätze eignen sich jeweils für verschiedene Szenarios und weisen unterschiedliche Einschränkungen auf:

* [Serverseitig in einer Datenbank](#server-side-in-a-database)
* [URL](#url)
* [Clientseitig im Browser](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a>Serverseitig in einer Datenbank

Für die dauerhafte Beibehaltung von Daten oder für Daten, die mehrere Benutzer oder Geräte einbeziehen müssen, ist eine unabhängige serverseitige Datenbank in den meisten Fällen die beste Lösung. Zu den Optionen gehören:

* Relationale SQL-Datenbank
* Schlüssel-Wert-Speicherung
* Blob-Speicher
* Tabellenspeicher

Nachdem die Daten in der Datenbank gespeichert wurden, kann der Benutzer jederzeit eine neue Verbindung starten. Die Daten des Benutzers werden beibehalten und sind in jeder neuen Verbindung verfügbar.

Weitere Informationen zu den Azure-Datenspeicheroptionen finden Sie in der [Azure Storage-Dokumentation](/azure/storage/) und [Azure-Datenbanken](https://azure.microsoft.com/product-categories/databases/).

### <a name="url"></a>URL

Modellieren Sie vorübergehende Daten, die den Navigationszustand darstellen, als Teil der URL. Beispiele für in der URL modellierte Zustände:

* Die ID einer angezeigten Entität.
* Die aktuelle Seitenzahl in einem ausgelagerten Raster.

Der Inhalt der Adressleiste des Browsers wird in folgenden Fällen beibehalten:

* Der Benutzer lädt die Seite nochmals manuell.
* Wenn der Webserver nicht mehr verfügbar ist, wird der Benutzer gezwungen, die Seite nochmals zu laden, damit eine Verbindung mit einem anderen Server hergestellt werden kann.

Weitere Informationen zum Definieren von URL-Mustern mit der `@page`-Direktive finden Sie unter <xref:blazor/routing>.

### <a name="client-side-in-the-browser"></a>Clientseitig im Browser

Für vorübergehende Daten, die der Benutzer aktiv erstellt, sind die `localStorage`- und `sessionStorage`-Sammlungen des Browsers ein häufig verwendeter Sicherungsspeicher. Die App muss den gespeicherten Zustand nicht verwalten oder löschen, wenn die Verbindung abgebrochen wird. Dies ist ein Vorteil gegenüber serverseitigem Speicher.

> [!NOTE]
> „Clientseitig“ bezieht sich in diesem Abschnitt auf clientseitige Szenarios im Browser, nicht auf das [Blazor-Webassembly-Hostingmodell](xref:blazor/hosting-models#blazor-webassembly). `localStorage` und `sessionStorage` können in Blazor-Webassembly-Apps verwendet werden, jedoch nur durch Schreiben von benutzerdefiniertem Code oder Verwenden eines Drittanbieterpakets.

`localStorage` und `sessionStorage` unterscheiden sich wie folgt:

* `localStorage` bezieht sich auf den Browser des Benutzers. Wenn der Benutzer die Seite noch mal lädt oder den Browser schließt und erneut öffnet, wird der Zustand beibehalten. Wenn der Benutzer mehrere Browserregisterkarten öffnet, wird der Zustand auf allen Registerkarten freigegeben. Daten bleiben in `localStorage` so lange beibehalten, bis sie explizit gelöscht werden.
* `sessionStorage` bezieht sich auf die Registerkarte des Browsers des Benutzers. Wenn der Benutzer die Registerkarte noch mal lädt, wird der Zustand beibehalten. Wenn der Benutzer die Registerkarte oder den Browser schließt, geht der Zustand verloren. Wenn der Benutzer mehrere Browserregisterkarten öffnet, hat jede Registerkarte eine eigene unabhängige Version der Daten.

Im Allgemeinen ist `sessionStorage` in der Anwendung sicherer. `sessionStorage` verhindert, dass ein Benutzer mehrere Registerkarten öffnet und auf folgende Probleme stößt:

* Fehler in Zustandsspeicher über mehrere Registerkarten hinweg
* Verwirrendes Verhalten, wenn eine Registerkarte den Zustand anderer Registerkarten überschreibt

`localStorage` ist die bessere Wahl, wenn der Zustand der App auch nach dem Schließen und erneuten Öffnen des Browsers bestehen bleiben muss.

Einschränkungen bei der Verwendung des Browserspeichers:

* Ähnlich wie bei der Verwendung einer serverseitigen Datenbank erfolgt das Laden und Speichern von Daten asynchron.
* Im Gegensatz zu einer serverseitigen Datenbank ist der Speicher während des Prerenderings nicht verfügbar, da die angeforderte Seite im Browser während der Prerenderingphase nicht vorhanden ist.
* Es ist ohne Weiteres möglich, einige wenige Kilobyte Daten für Blazor Server-Apps zu speichern. Bei mehr Kilobyte müssen Beeinträchtigungen der Leistung berücksichtigt werden, da die Daten über das Netzwerk geladen und gespeichert werden.
* Benutzer können die Daten anzeigen oder manipulieren. Die [Datenschutzlösung](xref:security/data-protection/introduction) von ASP.NET Core reduziert die damit verbundenen Risiken.

## <a name="third-party-browser-storage-solutions"></a>Browserspeicherlösungen von Drittanbietern

NuGet-Pakete von Drittanbietern stellen APIs für die Verwendung mit `localStorage` und `sessionStorage` bereit.

Erwägen Sie die Auswahl eines Pakets, das die [Datenschutzlösung](xref:security/data-protection/introduction) von ASP.NET Core transparent verwendet. Die Datenschutzlösung von ASP.NET Core verschlüsselt gespeicherte Daten und verringert das potenzielle Risiko, dass gespeicherte Daten manipuliert werden. Wenn JSON-serialisierte Daten als Klartext gespeichert werden, können Benutzer die Daten mithilfe von Browserentwicklertools anzeigen und auch die gespeicherten Daten ändern. Das Sichern von Daten ist nicht immer problematisch, da die Daten möglicherweise trivial sind. Beispielsweise stellt das Lesen oder Ändern der gespeicherten Farbe eines Benutzeroberflächenelements für den Benutzer oder die Organisation kein ernsthaftes Sicherheitsrisiko dar. Vermeiden Sie, dass Benutzer *sensible Daten* überprüfen oder manipulieren können.

## <a name="protected-browser-storage-experimental-package"></a>Experimentelles Paket für geschützten Browserspeicher

Ein Beispiel für ein NuGet-Paket, das [Datenschutz](xref:security/data-protection/introduction) für `localStorage` und `sessionStorage` bereitstellt, ist [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).

> [!WARNING]
> `Microsoft.AspNetCore.ProtectedBrowserStorage` ist ein nicht unterstütztes experimentelles Paket, das derzeit nicht für den Einsatz in der Produktion geeignet ist.

### <a name="installation"></a>Installation

So installieren Sie das `Microsoft.AspNetCore.ProtectedBrowserStorage`-Paket

1. Fügen Sie im Blazor Server-App-Projekt einen Paketverweis für [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage) hinzu.
1. Fügen Sie im HTML-Code der obersten Ebene (z. B. in der Datei *Pages/_Host.cshtml* in der Standardprojektvorlage) den folgenden `<script>`-Tag hinzu:

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. Rufen Sie für die `Startup.ConfigureServices`-Methode `AddProtectedBrowserStorage` auf, um der Dienstsammlung die Dienste `localStorage` und `sessionStorage` hinzuzufügen:

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a>Speichern und Laden von Daten in einer Komponente

Verwenden Sie für jede Komponente, die das Laden oder Speichern von Daten im Browserspeicher erfordert, [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component), um eine Instanz von einem der folgenden Komponenten einzufügen:

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

Die Auswahl hängt von dem Sicherungsspeicher ab, den Sie verwenden möchten. Im folgenden Beispiel wird `sessionStorage` verwendet:

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

Die `@using`-Anweisung kann in einer *_Imports.razor*-Datei statt in der Komponente abgelegt werden. Bei Verwendung der *_Imports.razor*-Datei wird der Namespace für größere Segmente der App oder die gesamte App zur Verfügung gestellt.

Um den `currentCount`-Wert in der `Counter`-Komponente der Projektvorlage beizubehalten, ändern Sie die `IncrementCount`-Methode so, dass `ProtectedSessionStore.SetAsync` verwendet wird:

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

Bei größeren, realistischeren Apps ist die Speicherung einzelner Felder ein unwahrscheinliches Szenario. Apps speichern meist ganze Modellobjekte, die komplexe Zustände enthalten. `ProtectedSessionStore` serialisiert und deserialisiert JSON-Daten automatisch.

Im vorangehenden Codebeispiel werden die `currentCount`-Daten als `sessionStorage['count']` im Browser des Benutzers gespeichert. Die Daten werden nicht im Klartext gespeichert, sondern mit der [Datenschutzlösung](xref:security/data-protection/introduction) von ASP.NET Core geschützt. Die verschlüsselten Daten können angezeigt werden, wenn `sessionStorage['count']` in der Entwicklerkonsole des Browsers ausgewertet wird.

Verwenden Sie `ProtectedSessionStore.GetAsync`, um die `currentCount`-Daten wiederherzustellen, wenn der Benutzer zu einem späteren Zeitpunkt zur `Counter`-Komponente zurückkehrt (auch wenn er sich in einer vollständig neuen Verbindung befindet):

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

Wenn die Parameter der Komponente den Navigationszustand enthalten, rufen Sie `ProtectedSessionStore.GetAsync` auf, und weisen Sie das Ergebnis in `OnParametersSetAsync` und nicht in `OnInitializedAsync` zu. `OnInitializedAsync` wird nur einmal aufgerufen, wenn die Komponente zum ersten Mal instanziiert wird. `OnInitializedAsync` wird später nicht nochmals aufgerufen, wenn der Benutzer zu einer anderen URL navigiert, während er auf der gleichen Seite bleibt. Weitere Informationen finden Sie unter <xref:blazor/lifecycle>.

> [!WARNING]
> Die Beispiele in diesem Abschnitt funktionieren nur, wenn für den Server kein Prerendering aktiviert ist. Wenn Prerendering aktiviert ist, wird ein Fehler ähnlich dem folgenden generiert:
>
> > JavaScript-Interop-Aufrufe können derzeit nicht durchgeführt werden. Dies liegt daran, dass die Komponente vorab gerendert wird.
>
> Deaktivieren Sie entweder Prerendering, oder fügen Sie zusätzlichen Code hinzu, um mit Prerendering zu arbeiten. Weitere Informationen zum Schreiben von Code, der mit Prerendering kompatibel ist, finden Sie im Abschnitt [Handhabung von Prerendering](#handle-prerendering).

### <a name="handle-the-loading-state"></a>Handhabung des Ladezustands

Da die Browserspeicherung asynchron erfolgt (Zugriff über eine Netzwerkverbindung), vergeht immer eine gewisse Zeit, bis die Daten geladen und für die Verwendung durch eine Komponente verfügbar sind. Die besten Ergebnisse erzielen Sie, wenn Sie während des Ladevorgangs eine Ladezustandsmeldung rendern, anstatt leere oder Standarddaten anzuzeigen.

Ein Ansatz besteht darin, zu beobachten, ob die Daten den Zustand `null` aufweisen (werden noch geladen) oder nicht. In der `Counter`-Standardkomponente wird die Anzahl in einer `int` gespeichert. Legen Sie fest, dass `currentCount` NULL-Werte aufweisen können, indem Sie dem Typ (`int`) ein Fragezeichen (`?`) hinzufügen:

```csharp
private int? currentCount;
```

Anstatt bedingungslos die Anzahl und die Schaltfläche **Inkrement** anzuzeigen, legen Sie fest, dass diese Elemente nur dann angezeigt werden, wenn die Daten geladen sind:

```razor
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>

    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a>Handhabung von Prerendering

Während des Prerenderings:

* Eine interaktive Verbindung zum Browser des Benutzers ist nicht vorhanden.
* Der Browser verfügt noch nicht über eine Seite, auf der der JavaScript-Code ausgeführt werden kann.

`localStorage` oder `sessionStorage` sind während des Prerenderings nicht verfügbar. Wenn die Komponente versucht, mit dem Speicher zu interagieren, wird ein Fehler ähnlich dem folgenden generiert:

> JavaScript-Interop-Aufrufe können derzeit nicht durchgeführt werden. Dies liegt daran, dass die Komponente vorab gerendert wird.

Eine Möglichkeit, den Fehler zu beheben, besteht darin, das Prerendering zu deaktivieren. Dies ist normalerweise die beste Lösung, wenn die App den browserbasierten Speicher stark nutzt. Das Prerendering erhöht die Komplexität und bringt der App keinen Nutzen, da die App keinen nützlichen Inhalt vorab rendern kann, bevor nicht `localStorage` oder `sessionStorage` verfügbar sind.

Öffnen Sie die Datei *Pages/_Host.cshtml*, und ändern Sie die `render-mode`-Eigenschaft des [Komponententaghilfsprogramms](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) in <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>, um das Prerendering zu deaktivieren.

Das Prerendering ist möglicherweise nützlich für andere Seiten, die `localStorage` oder `sessionStorage` nicht verwenden. Damit das Prerendering aktiviert bleibt, verschieben Sie den Ladevorgang, bis der Browser mit der Verbindung verbunden ist. Im Folgenden finden Sie ein Beispiel für das Speichern eines Zählerwerts:

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

... rendering code goes here ...

@code {
    private int? currentCount;
    private bool isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            // When execution reaches this point, the first *interactive* render
            // is complete. The component has an active connection to the browser.
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        currentCount = await ProtectedLocalStore.GetAsync<int>("prerenderedCount");
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedSessionStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a>Ausklammern der Zustandsbeibehaltung an einen allgemeinen Speicherort

Wenn viele Komponenten auf browserbasierte Speicherung zurückgreifen, führt ein erneutes Implementieren des Zustandsanbietercodes häufig zur Duplizierung des Codes. Eine Möglichkeit, die Duplizierung von Code zu vermeiden, besteht darin, eine *übergeordnete Komponente des Zustandsanbieters zu erstellen*, die die Zustandsanbieterlogik kapselt. Untergeordnete Komponenten können ohne Berücksichtigung des Zustandspersistenzmechanismus mit beibehaltenen Daten arbeiten.

Im folgenden Beispiel sehen Sie eine `CounterStateProvider`-Komponente, für die die Zählerdaten beibehalten werden:

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (hasLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool hasLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        hasLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

Die `CounterStateProvider`-Komponente verarbeitet die Ladephase, indem der untergeordnete Inhalt erst nach Abschluss des Ladenvorgangs gerendert wird.

Um die `CounterStateProvider`-Komponente zu verwenden, umschließen Sie eine Instanz der Komponente in einer beliebigen anderen Komponente, die Zugriff auf den Zählerzustand benötigt. Um den Zustand für alle Komponenten in einer App zugänglich zu machen, umschließen Sie die `CounterStateProvider`-Komponente um den `Router` in der `App`-Komponente (*App.razor*):

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

Umschließende Komponenten erhalten den beibehaltenen Zählerzustand und können diesen ändern. Die folgende `Counter`-Komponente implementiert das Muster:

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>

<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

Die vorherige Komponente muss weder mit `ProtectedBrowserStorage` interagieren, noch eine „Ladephase“ verarbeiten.

Für eine Handhabung des Prerenderings wie oben beschrieben kann `CounterStateProvider` dahingehend geändert werden, dass alle Komponenten, die Zählerdaten verbrauchen, automatisch mit dem Prerendering arbeiten. Ausführliche Informationen finden Sie im Abschnitt [Handhabung von Prerendering](#handle-prerendering).

In folgenden Fällen wird im Allgemeinen das Muster der *übergeordneten Komponente des Zustandsanbieters* empfohlen:

* Der Zustand soll in vielen anderen Komponenten verwendet werden können.
* Es muss nur ein Zustandsobjekt auf der obersten Ebene beibehalten werden.

Um viele verschiedene Zustandsobjekte beizubehalten und verschiedene Teilmengen von Objekten an verschiedenen Orten zu verarbeiten, ist es besser, das Laden und Speichern von Zustandsobjekten nicht global zu handhaben.
