---
title: ASP.net Core blazor-Zustands Verwaltung
author: guardrex
description: Erfahren Sie, wie Sie den Status in blazor-Server-apps beibehalten.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: blazor/state-management
ms.openlocfilehash: 67042fa9b86125fe95d877dbce246abeb6f35dd0
ms.sourcegitcommit: 35a86ce48041caaf6396b1e88b0472578ba24483
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72391274"
---
# <a name="aspnet-core-blazor-state-management"></a>ASP.net Core blazor-Zustands Verwaltung

Von [Steve Sanderson](https://github.com/SteveSandersonMS)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Der blazor-Server ist ein Zustands behaftetes App-Framework. In den meisten Fällen behält die APP eine laufende Verbindung mit dem Server bei. Der Benutzer Zustand wird im Speicher des Servers *in einer Verbindung*gespeichert. 

Beispiele für den Zustand einer Benutzer Verbindung sind:

* Die gerenderte Benutzeroberfläche @ no__t-0Die Hierarchie der Komponenten Instanzen und ihre letzte Renderausgabe.
* Die Werte aller Felder und Eigenschaften in Komponenten Instanzen.
* In [Abhängigkeitsinjektion (di)](xref:fundamentals/dependency-injection) -Dienst Instanzen gehaltene Daten, die auf die Verbindung beschränkt sind.

> [!NOTE]
> In diesem Artikel wird die Zustands Persistenz in blazor Server-apps behandelt. Blazor Webassembly-Apps können die [Client seitige Zustands Persistenz im Browser](#client-side-in-the-browser) nutzen, erfordern jedoch benutzerdefinierte Lösungen oder Drittanbieter Pakete, die den Rahmen dieses Artikels sprengen.

## <a name="blazor-circuits"></a>Blazor-Verbindungen

Wenn ein Benutzer einen vorübergehenden Netzwerk Verbindungsverlust erfährt, versucht blazor, den Benutzer erneut mit der ursprünglichen Verbindung zu verbinden, damit die APP weiterhin verwendet werden kann. Es ist jedoch nicht immer möglich, einen Benutzer mit der ursprünglichen Verbindung im Arbeitsspeicher des Servers wiederherzustellen:

* Der Server kann eine getrennte Verbindung nicht ewig beibehalten. Der Server muss eine getrennte Verbindung nach einem Timeout freigeben, oder wenn der Server nicht über genügend Arbeitsspeicher verfügt.
* In MultiServer-Bereitstellungs Umgebungen mit Lastenausgleich können alle Server Verarbeitungsanforderungen zu einem beliebigen Zeitpunkt nicht verfügbar sein. Bei einzelnen Servern tritt möglicherweise ein Fehler auf, oder Sie werden automatisch entfernt, wenn die Gesamtmenge der Anforderungen nicht mehr verarbeitet werden muss. Der ursprüngliche Server ist möglicherweise nicht verfügbar, wenn der Benutzer versucht, erneut eine Verbindung herzustellen.
* Der Benutzer kann seinen Browser schließen und erneut öffnen oder die Seite erneut laden, wodurch alle Zustände im Arbeitsspeicher des Browsers entfernt werden. Beispielsweise gehen Werte, die durch JavaScript-Interop-Aufrufe festgelegt wurden, verloren.

Wenn ein Benutzer nicht erneut mit der ursprünglichen Verbindung verbunden werden kann, erhält der Benutzer eine neue Verbindung mit einem leeren Zustand. Dies entspricht dem Schließen und erneuten Öffnen einer Desktop-App.

## <a name="preserve-state-across-circuits"></a>Status übergreifende Beibehaltung des Zustands

In einigen Szenarios ist die Beibehaltung des Zustands über mehrere Verbindungen wünschenswert. Eine APP kann wichtige Daten für einen Benutzer beibehalten, wenn Folgendes der folgenden ist:

* Der Webserver steht nicht mehr zur Verfügung.
* Der Browser des Benutzers ist gezwungen, eine neue Verbindung mit einem neuen Webserver zu starten.

Im Allgemeinen gilt: die Zustands übergreifende Zustands Verwaltung gilt für Szenarien, in denen Benutzer aktiv Daten erstellen und nicht nur Daten lesen, die bereits vorhanden sind.

*Speichern Sie die Daten nicht nur im Speicher des Servers*, um den Status außerhalb einer einzelnen Verbindung beizubehalten. Die APP muss die Daten an einem anderen Speicherort speichern. Zustands Persistenz ist nicht automatisch @ no__t-0Sie müssen bei der Entwicklung der APP Schritte ausführen, um Zustands behaftete Daten Persistenz zu implementieren.

Die Daten Persistenz ist in der Regel nur für einen hochwertigen Status erforderlich, den Benutzer für die Erstellung aufgewendet haben. In den folgenden Beispielen spart die Beibehaltung des Zustands entweder Zeit oder Hilfsmittel in kommerziellen Aktivitäten:

* Mehreren Schritten Webform &ndash; ist es zeitaufwändig, dass ein Benutzer die Daten für mehrere abgeschlossene Schritte eines mehrstufigen Prozesses erneut eingibt, wenn der Status verloren geht. Ein Benutzer verliert den Zustand in diesem Szenario, wenn er vom mehrstufigen Formular weg navigiert und später zum Formular zurückkehrt.
* Einkaufswagen &ndash; alle kommerziell wichtigen Komponenten einer APP, die einen potenziellen Umsatz darstellen, können verwaltet werden. Ein Benutzer, der seinen Zustand verliert und somit seinen Warenkorb, kann weniger Produkte oder Dienste kaufen, wenn er zu einem späteren Zeitpunkt zur Website zurückkehrt.

Es ist in der Regel nicht notwendig, den einfach neu erstellten Zustand beizubehalten, z. b. den Benutzernamen, der in ein Anmelde Dialogfeld eingegeben wurde, das nicht übermittelt wurde.

> [!IMPORTANT]
> Eine APP kann nur den *App-Zustand*persistent speichern. UIs können nicht persistent gespeichert werden, z. b. Komponenten Instanzen und deren Rendering-Strukturen. Komponenten und Rendering-Strukturen sind in der Regel nicht serialisierbar. Um eine ähnliche Situation wie die erweiterten Knoten einer TreeView zu erhalten, muss die APP über benutzerdefinierten Code verfügen, um das Verhalten als serialisierbaren App-Zustand zu modellieren.

## <a name="where-to-persist-state"></a>Speicherort des Zustands

Es gibt drei allgemeine Speicherorte für die Beibehaltung des Zustands in einer blazor-Server-app. Jeder Ansatz eignet sich am besten für verschiedene Szenarien und hat unterschiedliche Einschränkungen:

* [Server seitig in einer Datenbank](#server-side-in-a-database)
* [URL](#url)
* [Client seitig im Browser](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a>Server seitig in einer Datenbank

Für permanente Daten Persistenz oder für Daten, die mehrere Benutzer oder Geräte umfassen müssen, ist eine unabhängige serverseitige Datenbank fast sicherlich die beste Wahl. Zu den Optionen gehören:

* Relationale SQL-Datenbank
* Schlüssel-Wert-Speicher
* BLOB-Speicher
* Tabellen Speicher

Nachdem die Daten in der Datenbank gespeichert wurden, kann eine neue Verbindung jederzeit von einem Benutzer gestartet werden. Die Daten des Benutzers werden aufbewahrt und sind in jeder neuen Verbindung verfügbar.

Weitere Informationen zu den Azure-Datenspeicher Optionen finden Sie in der [Azure Storage-Dokumentation](/azure/storage/) und [Azure-Datenbanken](https://azure.microsoft.com/product-categories/databases/).

### <a name="url"></a>URL

Modellieren Sie die Daten bei vorübergehenden Daten, die den Navigations Zustand darstellen, als Teil der URL. Zu den in der URL modellierten Zuständen zählen beispielsweise:

* Die ID einer angezeigten Entität.
* Die aktuelle Seitenzahl in einem Auslagerungs Raster.

Der Inhalt der Adressleiste des Browsers wird beibehalten:

* , Wenn der Benutzer die Seite manuell neu lädt.
* Wenn der Webserver nicht mehr verfügbar ist @ no__t-0der Benutzer ist gezwungen, die Seite erneut zu laden, damit eine Verbindung mit einem anderen Server hergestellt werden kann.

Weitere Informationen zum Definieren von URL-Mustern mit der `@page`-Direktive finden Sie unter <xref:blazor/routing>.

### <a name="client-side-in-the-browser"></a>Client seitig im Browser

Bei vorübergehenden Daten, die der Benutzer aktiv erstellt, sind die `localStorage`-und `sessionStorage`-Auflistungen des Browsers ein gängiger Sicherungs Speicher. Die APP muss den gespeicherten Zustand nicht verwalten oder löschen, wenn die Verbindung abgebrochen wird. Dies ist ein Vorteil gegenüber Server seitigem Speicher.

> [!NOTE]
> "Client seitig" bezieht sich in diesem Abschnitt auf Client seitige Szenarios im Browser, nicht auf das [blizor-webassemblyhostingmodell](xref:blazor/hosting-models#blazor-webassembly). `localStorage` und `sessionStorage` können in blazor Webassembly-Apps verwendet werden, jedoch nur durch das Schreiben von benutzerdefiniertem Code oder das Verwenden eines Drittanbieter Pakets.

`localStorage` und `sessionStorage` unterscheiden sich wie folgt:

* `localStorage` ist auf den Browser des Benutzers festgelegt. Wenn der Benutzer die Seite erneut lädt oder den Browser schließt und erneut öffnet, wird der Zustand beibehalten. Wenn der Benutzer mehrere Browser Registerkarten öffnet, wird der Status auf den Registerkarten freigegeben. Daten bleiben in `localStorage` bestehen, bis Sie explizit gelöscht werden.
* `sessionStorage` ist auf die Registerkarte Browser des Benutzers festgelegt. Wenn der Benutzer die Registerkarte erneut lädt, bleibt der Zustand erhalten. Wenn der Benutzer die Registerkarte oder den Browser schließt, geht der Status verloren. Wenn der Benutzer mehrere Browser Registerkarten öffnet, verfügt jede Registerkarte über eine eigene unabhängige Version der Daten.

Im Allgemeinen ist `sessionStorage` sicherer zu verwenden. `sessionStorage` vermeidet das Risiko, dass ein Benutzer mehrere Registerkarten öffnet und auf Folgendes stößt:

* Fehler in Zustands Speicher zwischen Registerkarten.
* Verwirrendes Verhalten, wenn eine Registerkarte den Zustand anderer Registerkarten überschreibt.

`localStorage` ist die bessere Wahl, wenn die APP den Zustand über schließen beibehalten und den Browser erneut öffnen muss.

Einschränkungen für die Verwendung von Browser Speicher:

* Ähnlich wie bei der Verwendung einer serverseitigen Datenbank sind das Laden und Speichern von Daten asynchron.
* Im Gegensatz zu einer serverseitigen Datenbank ist der Speicher während der vorab Ausführung nicht verfügbar, da die angeforderte Seite im Browser während der vorab Erstellungsphase nicht vorhanden ist.
* Die Speicherung von einigen Kilobyte an Daten kann für blazor-Server-apps dauerhaft gespeichert werden. Über einige Kilobytes hinaus müssen Sie die Auswirkungen auf die Leistung in Erwägung gezogen, da die Daten im Netzwerk geladen und gespeichert werden.
* Benutzer können die Daten anzeigen oder manipulieren. ASP.net Core [Datenschutz](xref:security/data-protection/introduction) kann das Risiko mindern.

## <a name="third-party-browser-storage-solutions"></a>Browser Speicherlösungen von Drittanbietern

Nuget-Pakete von Drittanbietern bieten APIs zum Arbeiten mit `localStorage` und `sessionStorage`.

Es lohnt sich zu erwägen, ein Paket auszuwählen, das den [Datenschutz](xref:security/data-protection/introduction)ASP.net Core transparent verwendet. ASP.net Core Datenschutz verschlüsselt gespeicherte Daten und verringert das potenzielle Risiko der Manipulation gespeicherter Daten. Wenn JSON-serialisierte Daten als Klartext gespeichert werden, können Benutzer die Daten mithilfe von Browser Entwicklertools anzeigen und auch die gespeicherten Daten ändern. Das Sichern von Daten ist nicht immer problematisch, da die Daten möglicherweise trivial sind. Beispielsweise ist das Lesen oder Ändern der gespeicherten Farbe eines UI-Elements für den Benutzer oder die Organisation kein erhebliches Sicherheitsrisiko. Vermeiden Sie es Benutzern, *sensible Daten*zu überprüfen oder zu manipulieren.

## <a name="protected-browser-storage-experimental-package"></a>Experimentelles Paket für geschützten Browser Speicher

Ein Beispiel für ein nuget-Paket, das den [Schutz von Daten](xref:security/data-protection/introduction) für `localStorage` und `sessionStorage` bietet, ist [Microsoft. aspnetcore. protectedbrowserstorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).

> [!WARNING]
> `Microsoft.AspNetCore.ProtectedBrowserStorage` ist ein nicht unterstütztes experimentelles Paket, das zu diesem Zeitpunkt nicht für die Produktion geeignet ist.

### <a name="installation"></a>Installation

So installieren Sie das Paket "`Microsoft.AspNetCore.ProtectedBrowserStorage`":

1. Fügen Sie im Projekt der blazor-Server-APP einen Paket Verweis auf [Microsoft. aspnetcore. protectedbrowserstorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage)hinzu.
1. Fügen Sie im HTML-Code der obersten Ebene (z. b. in der Datei *pages/_Host. cshtml* in der Standard Projektvorlage) das folgende `<script>`-Tag hinzu:

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. Nennen Sie in der `Startup.ConfigureServices`-Methode `AddProtectedBrowserStorage`, um der Dienst Sammlung `localStorage`-und `sessionStorage`-Dienste hinzuzufügen:

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a>Speichern und Laden von Daten in einer Komponente

Verwenden Sie für jede Komponente, die das Laden oder Speichern von Daten im Browser Speicher erfordert, [@inject](xref:blazor/dependency-injection#request-a-service-in-a-component) , um eine Instanz von einem der folgenden Komponenten einzufügen:

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

Die Auswahl hängt von dem Sicherungs Speicher ab, den Sie verwenden möchten. Im folgenden Beispiel wird `sessionStorage` verwendet:

```cshtml
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

Die `@using`-Anweisung kann in einer *_Imports. Razor* -Datei anstelle der-Komponente abgelegt werden. Wenn Sie die Datei *_Imports. Razor* verwenden, ist der Namespace für größere Segmente der APP oder der gesamten app verfügbar.

Um den `currentCount`-Wert in der `Counter`-Komponente der Projektvorlage beizubehalten, ändern Sie die `IncrementCount`-Methode so, dass `ProtectedSessionStore.SetAsync` verwendet wird:

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

Bei größeren, realistischeren Apps ist die Speicherung einzelner Felder ein unwahrscheinliches Szenario. Apps werden wahrscheinlich ganze Modell Objekte speichern, die einen komplexen Zustand enthalten. die JSON-Daten werden von `ProtectedSessionStore` automatisch serialisiert und deserialisiert.

Im vorangehenden Codebeispiel werden die `currentCount`-Daten im Browser des Benutzers als `sessionStorage['count']` gespeichert. Die Daten werden nicht als Klartext gespeichert, sondern durch ASP.net Core [Datenschutz](xref:security/data-protection/introduction)geschützt. Die verschlüsselten Daten können angezeigt werden, wenn `sessionStorage['count']` in der Entwickler Konsole des Browsers ausgewertet wird.

Um die `currentCount`-Daten wiederherzustellen, wenn der Benutzer später zur `Counter`-Komponente zurückkehrt (auch wenn Sie sich auf einer vollständig neuen Verbindung befinden), verwenden Sie `ProtectedSessionStore.GetAsync`:

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

Wenn die Parameter der Komponente den Navigations Zustand einschließen, wird `ProtectedSessionStore.GetAsync` aufgerufen und das Ergebnis in `OnParametersSetAsync`, nicht `OnInitializedAsync` zugewiesen. `OnInitializedAsync` wird nur einmal aufgerufen, wenn die Komponente zum ersten Mal instanziiert wird. `OnInitializedAsync` wird später nicht erneut aufgerufen, wenn der Benutzer zu einer anderen URL navigiert, während er auf der gleichen Seite bleibt.

> [!WARNING]
> Die Beispiele in diesem Abschnitt funktionieren nur, wenn für den Server keine vorab Aktivierung aktiviert ist. Wenn die vorab Aktivierung aktiviert ist, wird ein Fehler ähnlich dem folgenden generiert:
>
> > JavaScript-Interop-Aufrufe können zu diesem Zeitpunkt nicht ausgegeben werden. Dies liegt daran, dass die Komponente vorab in eine Vorabversion übernommen wird.
>
> Deaktivieren Sie entweder die vorab Ausführung, oder fügen Sie zusätzlichen Code hinzu, um mit der vorab Ausführung zu arbeiten. Weitere Informationen zum Schreiben von Code, der mit der vorab Generierung funktioniert, finden Sie im Abschnitt [handle-vorab](#handle-prerendering) Version.

### <a name="handle-the-loading-state"></a>Behandeln des Ladezustands

Da der Browser Speicher asynchron ist (auf den über eine Netzwerkverbindung zugegriffen wird), gibt es immer einen Zeitraum, bis die Daten geladen sind und zur Verwendung durch eine Komponente verfügbar sind. Um die besten Ergebnisse zu erzielen, sollten Sie während des Ladens eine Ladezustands Meldung erstellen, anstatt leere oder Standarddaten anzuzeigen.

Ein Ansatz besteht darin, zu überprüfen, ob die Daten `null` (noch geladen) sind oder nicht. In der standardmäßigen `Counter`-Komponente wird die Anzahl in einem `int` gespeichert. Legen Sie `currentCount` NULL-Werte fest, indem Sie dem Typ (`int`) ein Fragezeichen (`?`) hinzufügen:

```csharp
private int? currentCount;
```

Anstatt die Schaltfläche Anzahl und **Inkrement** bedingungslos anzuzeigen, können Sie diese Elemente nur anzeigen, wenn die Daten geladen werden:

```cshtml
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

### <a name="handle-prerendering"></a>Vorab Rendering

Während der Vorab Anmeldung:

* Eine interaktive Verbindung zum Browser des Benutzers ist nicht vorhanden.
* Der Browser verfügt noch nicht über eine Seite, auf der der JavaScript-Code ausgeführt werden kann.

`localStorage` oder `sessionStorage` ist während der vorab Generierung nicht verfügbar. Wenn die Komponente versucht, mit dem Speicher zu interagieren, wird ein Fehler ähnlich dem folgenden generiert:

> JavaScript-Interop-Aufrufe können zu diesem Zeitpunkt nicht ausgegeben werden. Dies liegt daran, dass die Komponente vorab in eine Vorabversion übernommen wird.

Eine Möglichkeit, den Fehler zu beheben, besteht darin, die vorab Generierung zu deaktivieren. Dies ist normalerweise die beste Wahl, wenn die APP den browserbasierten Speicher stark nutzt. Das vorab Rendering erhöht die Komplexität und profitiert von der APP nicht, da die APP keine nützlichen Inhalte vorab bereitstellen kann, bis `localStorage` oder `sessionStorage` verfügbar sind.

Öffnen Sie zum Deaktivieren der vorab Generierung die Datei *pages/_Host. cshtml* , und ändern Sie den-Befehl in `Html.RenderComponentAsync<App>(RenderMode.Server)`.

Die vorab Generierung ist möglicherweise nützlich für andere Seiten, die nicht `localStorage` oder `sessionStorage` verwenden. Verschieben Sie den Ladevorgang so lange, bis der Browser mit der Verbindung verbunden ist, um die vorab Ausführung zu aktivieren. Im folgenden finden Sie ein Beispiel für das Speichern eines Leistungs Zählers:

```cshtml
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

### <a name="factor-out-the-state-preservation-to-a-common-location"></a>Einen allgemeinen Speicherort für die Zustands Beibehaltung berücksichtigen

Wenn viele Komponenten auf Browser basiertem Speicher basieren, wird durch erneutes Implementieren des Zustands Anbieter Codes die Code Duplizierung erstellt. Eine Möglichkeit zur Vermeidung von Code Duplizierung besteht darin, eine über *geordnete Zustands Anbieter Komponente* zu erstellen, die die Zustands Anbieter Logik kapselt. Untergeordnete Komponenten können ohne Berücksichtigung des Zustands Persistenzmechanismus mit beibehaltenen Daten arbeiten.

Im folgenden Beispiel einer `CounterStateProvider`-Komponente werden die Counter-Daten persistent gespeichert:

```cshtml
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

Die Komponente `CounterStateProvider` verarbeitet die Lade Phase, indem ihr untergeordneter Inhalt nicht gerendert wird, bis der Ladevorgang beendet ist.

Um die Komponente "`CounterStateProvider`" zu verwenden, müssen Sie eine Instanz der Komponente um eine beliebige andere Komponente umschließen, die Zugriff auf den Gegenstand benötigt. Um den Status für alle Komponenten in einer APP zugänglich zu machen, wrappen Sie die Komponente "`CounterStateProvider`" um die `Router` in der Komponente "`App`" ("*app. Razor*"):

```cshtml
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

Umschließende Komponenten empfangen und können den persistenten Status des Zählers ändern. Die folgende `Counter`-Komponente implementiert das Muster:

```cshtml
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

Die vorherige Komponente ist nicht erforderlich, um mit `ProtectedBrowserStorage` zu interagieren, auch nicht mit einer "Lade Phase".

Um mit der vorab Ausführung wie zuvor beschrieben umzugehen, kann `CounterStateProvider` geändert werden, sodass alle Komponenten, die die Daten des Zählers nutzen, automatisch mit der vorab Ausführung arbeiten. Weitere Informationen finden Sie im Abschnitt Vorabversion des [Handles](#handle-prerendering) .

Im Allgemeinen wird ein über *geordnetes Zustands Anbieter-Komponenten* Muster empfohlen:

* , Um den Zustand in vielen anderen Komponenten zu verarbeiten.
* , Wenn nur ein Zustands Objekt der obersten Ebene beibehalten werden soll.

Es ist besser, das Laden und Speichern des Zustands Global zu vermeiden, um viele verschiedene Zustands Objekte beizubehalten und verschiedene Teilmengen von Objekten an unterschiedlichen Stellen zu verarbeiten.
