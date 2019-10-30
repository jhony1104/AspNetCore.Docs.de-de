---
title: ASP.net Core blazor-Hostingmodelle
author: guardrex
description: Grundlegendes zum Hostingmodell von blazor Webassembly-und blazor-Servern.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: blazor/hosting-models
ms.openlocfilehash: be67c129af4f071d10719e0bbf121de761dde9f4
ms.sourcegitcommit: 16cf016035f0c9acf3ff0ad874c56f82e013d415
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73033995"
---
# <a name="aspnet-core-blazor-hosting-models"></a>ASP.net Core blazor-Hostingmodelle

Von [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor ist ein Webframework, das für die Client seitige Ausführung im Browser auf einer [webassemblybasierten](https://webassembly.org/) .NET-Runtime (*blazor Webassembly*) oder Serverseite in ASP.net Core (*blazor-Server*) konzipiert ist. Unabhängig vom Hostingmodell sind die APP-und Komponentenmodelle *identisch*.

Informationen zum Erstellen eines Projekts für die in diesem Artikel beschriebenen Hostingmodelle finden Sie unter <xref:blazor/get-started>.

## <a name="blazor-webassembly"></a>Blazor WebAssembly

Das Prinzipal-Hostingmodell für blazor führt die Client seitige Ausführung im Browser in Webassembly aus. Die Blazor-App, die jeweiligen Abhängigkeiten und die .NET-Runtime werden im Browser herunterladen. Die App wird direkt im UI-Thread des Browsers ausgeführt. UI-Updates und Ereignis Behandlung treten innerhalb desselben Prozesses auf. Die Assets der App werden als statische Dateien auf einem Webserver oder Dienst bereitgestellt, in dem statische Inhalte für Clients bereitgestellt werden können.

![Blazor-Webassembly: Die blazor-APP wird in einem UI-Thread im Browser ausgeführt.](hosting-models/_static/blazor-webassembly.png)

Um eine blazor-App mithilfe des Client seitigen Hostingmodells zu erstellen, verwenden Sie die Vorlage " **blazor Webassembly-App** " ([dotnet New blazorwasm](/dotnet/core/tools/dotnet-new)).

Nachdem Sie die APP-Vorlage für die **blazor-Webassembly** ausgewählt haben, können Sie die APP für die Verwendung eines ASP.net Core-Back-Ends konfigurieren, indem Sie das Kontrollkästchen **ASP.net Core gehostet** aktivieren ([dotnet New blazorwasm--Hosted](/dotnet/core/tools/dotnet-new)). Die ASP.net Core-App bedient die blazor-APP an Clients. Die blazor Webassembly-App kann über das Netzwerk mithilfe von Web-API-aufrufen oder [signalr](xref:signalr/introduction)mit dem Server interagieren.

Die Vorlagen enthalten das Skript " *blazor. Webassembly. js* ", das Folgendes behandelt:

* Herunterladen der .NET-Laufzeit, der APP und der APP-Abhängigkeiten.
* Initialisierung der Laufzeit zum Ausführen der app.

Das Webassembly-Hostingmodell von blazor bietet mehrere Vorteile:

* Es gibt keine serverseitige .net-Abhängigkeit. Die APP funktioniert nach dem Herunterladen auf den Client vollständig.
* Client Ressourcen und-Funktionen sind vollständig ausgelastet.
* Die Arbeit wird vom Server an den Client verlagert.
* Ein ASP.net Core Webserver ist nicht erforderlich, um die APP zu hosten. Server lose Bereitstellungs Szenarien sind möglich (z. b. das Bereitstellen der APP aus einem CDN).

Es gibt Nachteile für das blizor-webassemblyhosting:

* Die APP ist auf die Funktionen des Browsers beschränkt.
* Kompatible Client Hardware und Software (z. b. webassemblyunterstützung) ist erforderlich.
* Die Download Größe ist größer, und die Auslastung von apps dauert länger.
* Die Unterstützung von .NET-Laufzeit und-Tools ist weniger ausgereift. Beispielsweise gibt es Einschränkungen in [.NET Standard](/dotnet/standard/net-standard) Unterstützung und Debuggen.

## <a name="blazor-server"></a>Blazor Server

Mit dem serverhostingmodell von blazor wird die APP auf dem Server innerhalb einer ASP.net Core-app ausgeführt. Benutzeroberflächenupdates, Ereignisbehandlung und JavaScript-Aufrufe werden über eine [SignalR](xref:signalr/introduction)-Verbindung verarbeitet.

![Der Browser interagiert mit der APP (gehostet innerhalb einer ASP.net Core-APP) auf dem Server über eine signalr-Verbindung.](hosting-models/_static/blazor-server.png)

Verwenden Sie die Vorlage "ASP.net Core **blazor Server-App** " ([dotnet New blazorserver](/dotnet/core/tools/dotnet-new)), um eine blazor-App mithilfe des blazor-Hostingmodells zu erstellen. Die ASP.net Core-App hostet die blazor-Server-APP und erstellt den signalr-Endpunkt, mit dem Clients eine Verbindung herstellen

Die ASP.net Core-App verweist auf die `Startup` Klasse der APP, um Folgendes hinzuzufügen:

* Server seitige Dienste.
* Die APP für die Pipeline für die Anforderungs Verarbeitung.

Das Skript " *blazor. Server. js* " &dagger; die Client Verbindung herstellt. Es liegt in der Verantwortung der APP, den App-Zustand nach Bedarf beizubehalten und wiederherzustellen (z. b. im Fall einer verlorenen Netzwerkverbindung).

Das serverhostingmodell von blazor bietet verschiedene Vorteile:

* Die Download Größe ist erheblich kleiner als eine blazor Webassembly-APP, und die APP lädt viel schneller.
* Die App nutzt vollständige Vorteile der Serverfunktionen, einschließlich der Verwendung von .net Core-kompatiblen APIs.
* .Net Core auf dem Server wird verwendet, um die APP auszuführen, sodass vorhandene .NET-Tools, wie z. b. das Debuggen, erwartungsgemäß funktionieren.
* Thin Clients werden unterstützt. Beispielsweise funktionieren blazor-Server-apps mit Browsern, die Webassembly und auf Geräten mit eingeschränkter Ressourcen nicht unterstützen.
* Die .net-C# /Codebasis der APP, einschließlich des Komponenten Codes der APP, wird nicht für Clients bereitgestellt.

Es gibt Nachteile des blazor-Server Hostings:

* Eine höhere Latenz ist normalerweise vorhanden. Jede Benutzerinteraktion umfasst einen Netzwerkhop.
* Es gibt keine Offline Unterstützung. Wenn die Client Verbindung fehlschlägt, funktioniert die APP nicht mehr.
* Skalierbarkeit ist eine Herausforderung für apps mit vielen Benutzern. Der Server muss mehrere Clientverbindungen verwalten und den Client Status verarbeiten.
* Ein ASP.net Core Server ist erforderlich, um der APP zu dienen. Server lose Bereitstellungs Szenarien sind nicht möglich (z. b. für die APP aus einem CDN).

&dagger;The " *blazor. Server. js* "-Skript wird von einer eingebetteten Ressource im ASP.net Core freigegebenen Framework bereitgestellt.

### <a name="comparison-to-server-rendered-ui"></a>Vergleich mit der Server gerenderten Benutzeroberfläche

Eine Möglichkeit zum verstehen von blazor-Server-apps besteht darin, zu verstehen, wie Sie sich von herkömmlichen Modellen für das Rendern von Benutzeroberflächen in ASP.net Core Apps mithilfe von Razor-Ansichten Razor Pages oder Beide Modelle verwenden die Razor-Sprache, um HTML-Inhalte zu beschreiben. Sie unterscheiden sich jedoch deutlich von der Darstellung von Markup.

Wenn eine Razor Page oder eine Ansicht gerendert wird, gibt jede Zeile von Razor-Code HTML-Code in Textform aus. Nach dem Rendering verwirft der Server die Seiten-oder Sicht Instanz, einschließlich eines beliebigen Zustands, der erstellt wurde. Wenn eine andere Anforderung für die Seite auftritt, z. b. wenn die Server Validierung fehlschlägt und die Validierungs Zusammenfassung angezeigt wird:

* Die gesamte Seite wird erneut in HTML-Text umgeleitet.
* Die Seite wird an den Client gesendet.

Eine blazor-App besteht aus wiederverwendbaren Elementen der UI, die als *Komponenten*bezeichnet werden. Eine Komponente enthält C# Code, Markup und andere Komponenten. Wenn eine Komponente gerendert wird, erzeugt blazor ein Diagramm der enthaltenen Komponenten, ähnlich wie ein HTML-oder XML-Dokumentobjektmodell (DOM). Dieses Diagramm enthält den Komponenten Zustand in Eigenschaften und Feldern. Blazor wertet das Komponenten Diagramm aus, sodass eine binäre Darstellung des Markups erzeugt wird. Das binäre Format kann wie folgt lauten:

* Wird in HTML-Text (während der vorab Generierung) umgewandelt.
* Wird verwendet, um das Markup beim regulären Rendering effizient zu aktualisieren.

Ein Benutzeroberflächen Update in blazor wird durch Folgendes ausgelöst:

* Benutzerinteraktion, z. b. das Auswählen einer Schaltfläche.
* App-Trigger, z. b. ein Timer.

Das Diagramm wird erneut ausgeführt, und es wird ein UI- *diff* (Differenz) berechnet. Dieser Unterschied ist der kleinste Satz an DOM-Änderungen, die erforderlich sind, um die Benutzeroberfläche auf dem Client zu aktualisieren. Der diff wird in einem binären Format an den Client gesendet und vom Browser angewendet.

Eine Komponente wird verworfen, nachdem der Benutzer auf dem Client dorthin navigiert ist. Während ein Benutzer mit einer Komponente interagiert, muss der Zustand der Komponente (Dienste, Ressourcen) im Arbeitsspeicher des Servers gespeichert werden. Da der Status vieler Komponenten möglicherweise gleichzeitig vom Server verwaltet wird, ist die Arbeitsspeicher Erschöpfung ein Problem, das behoben werden muss. Anleitungen zum Erstellen einer blazor-Server-APP, um die optimale Verwendung des Server Arbeitsspeichers sicherzustellen, finden Sie unter <xref:security/blazor/server>.

### <a name="circuits"></a>Fen

Eine blazor-Server-APP wird zusätzlich zu [ASP.net Core signalr](xref:signalr/introduction)erstellt. Jeder Client kommuniziert *über eine oder*mehrere signalr-Verbindungen, die als Verbindung bezeichnet werden, mit dem Server. Eine Verbindung ist die Abstraktion von blazor über signalr-Verbindungen, die temporäre Netzwerkunterbrechungen tolerieren können. Wenn ein blazor-Client feststellt, dass die signalr-Verbindung getrennt ist, versucht er, eine Verbindung mit dem Server mithilfe einer neuen signalr-Verbindung herzustellen.

Jeder Browser Bildschirm (Browser Registerkarte oder IFRAME), der mit einer blazor-Server-App verbunden ist, verwendet eine signalr-Verbindung. Dies ist jedoch ein weiterer wichtiger Unterschied im Vergleich zu typischen, von Servern gerenderten apps. In einer Server gerenderten APP wird das Öffnen derselben app in mehreren Browser Bildschirmen in der Regel nicht zu zusätzlichen Ressourcenanforderungen auf dem Server übertragen. In einer blazor-Server-App erfordert jeder Browser Bildschirm eine separate Verbindung und separate Instanzen des Komponenten Zustands, die vom Server verwaltet werden.

Blazor betrachtet das Schließen einer Browser Registerkarte oder das Navigieren zu einer externen URL zu einer Ordnungs *gemäßen Beendigung.* Im Fall einer ordnungsgemäßen Beendigung werden die Verbindung und zugehörige Ressourcen sofort freigegeben. Ein Client kann möglicherweise auch nicht ordnungsgemäß getrennt werden, beispielsweise aufgrund einer Netzwerk Unterbrechung. Der blazor-Server speichert getrennte Verbindungen für ein konfigurierbares Intervall, damit der Client die Verbindung wiederherstellen kann. Weitere Informationen finden Sie im Abschnitt [Wiederherstellen der Verbindung mit dem gleichen Server](#reconnection-to-the-same-server) .

### <a name="ui-latency"></a>UI-Latenz

Die Benutzeroberflächen Latenz ist die Zeit, die Sie von einer initiierten Aktion bis zum Zeitpunkt der Aktualisierung der Benutzeroberfläche benötigt. Kleinere Werte für die Benutzeroberflächen Latenz sind zwingend erforderlich, damit eine APP für einen Benutzer reaktionsfähig ist. In einer blazor-Server-APP wird jede Aktion an den Server gesendet, verarbeitet und ein UI-diff zurückgesendet. Folglich ist die Benutzeroberflächen Latenz die Summe der Netzwerk Latenz und der Server Latenz bei der Verarbeitung der Aktion.

Für eine Branchen-APP, die auf ein privates Unternehmensnetzwerk beschränkt ist, sind die Auswirkungen auf die Benutzer Wahrnehmung von Latenzzeit aufgrund der Netzwerk Latenz in der Regel nicht wahrnehmbar. Für eine APP, die über das Internet bereitgestellt wird, kann die Latenz für Benutzer bemerkbar werden, insbesondere, wenn die Benutzer weitgehend geografisch verteilt sind.

Die Speicherauslastung kann auch zur APP-Latenz beitragen. Eine erhöhte Arbeitsspeicher Auslastung führt zu häufigen Garbage Collection-oder Paging-Speicher auf den Datenträger, die beide die Leistung der APP beeinträchtigen und somit die Benutzeroberflächen Latenz erhöhen. Weitere Informationen finden Sie unter <xref:security/blazor/server>.

Blazor-Server-apps sollten optimiert werden, um die Benutzeroberflächen Latenz zu minimieren, indem Netzwerk Latenz und Speicherauslastung reduziert werden Einen Ansatz zum Messen der Netzwerk Latenz finden Sie unter <xref:host-and-deploy/blazor/server#measure-network-latency>. Weitere Informationen zu signalr und blazor finden Sie unter:

* <xref:host-and-deploy/blazor/server>
* <xref:security/blazor/server>

### <a name="reconnection-to-the-same-server"></a>Erneute Verbindung mit demselben Server

Für blazor-Server-Apps ist eine aktive signalr-Verbindung mit dem Server erforderlich. Wenn die Verbindung unterbrochen wird, versucht die APP, erneut eine Verbindung mit dem Server herzustellen. Solange sich der Status des Clients weiterhin im Arbeitsspeicher befindet, wird die Client Sitzung fortgesetzt, ohne den Zustand zu verlieren.

Wenn der Client erkennt, dass die Verbindung unterbrochen wurde, wird dem Benutzer eine Standardbenutzer Oberfläche angezeigt, während der Client versucht, die Verbindung wiederherzustellen. Wenn bei der erneuten Verbindungs Herstellung ein Fehler auftritt, wird dem Benutzer die Option zum erneuten versuchen bereitgestellt. Zum Anpassen der Benutzeroberfläche müssen Sie ein Element mit `components-reconnect-modal` als `id` auf der Razor Page *_Host. cshtml* definieren. Der Client aktualisiert dieses Element mit einer der folgenden CSS-Klassen basierend auf dem Status der Verbindung:

* `components-reconnect-show` &ndash; die Benutzeroberfläche anzeigen, um eine verlorene Verbindung anzugeben, und der Client versucht, die Verbindung wiederherzustellen.
* `components-reconnect-hide` &ndash; der Client über eine aktive Verbindung verfügt, blenden Sie die Benutzeroberfläche aus.
* `components-reconnect-failed` &ndash; Verbindungsfehler, wahrscheinlich aufgrund eines Netzwerk Fehlers. Um erneut eine Verbindung herzustellen, wenden Sie `window.Blazor.reconnect()` an.
* `components-reconnect-rejected` &ndash; erneute Verbindung abgelehnt. Der Server wurde erreicht, hat jedoch die Verbindung abgelehnt, und der Benutzer Zustand auf dem Server ist nicht mehr vorhanden. Um die APP erneut zu laden, wenden Sie `location.reload()` an. Der Verbindungsstatus kann folgende Ergebnisse haben:
  * Ein Absturz in der Verbindung (serverseitiger Code) tritt auf.
  * Der Client ist so lange getrennt, dass der Server den Zustand des Benutzers löscht. Instanzen von Komponenten, mit denen der Benutzer interagiert hat, werden verworfen.

### <a name="stateful-reconnection-after-prerendering"></a>Zustands behaftete erneute Verbindung nach der vorab Ausführung

Blazor-Server-apps werden standardmäßig so eingerichtet, dass Sie die Benutzeroberfläche auf dem Server vorab ausführen, bevor die Client Verbindung mit dem Server hergestellt wird. Dies wird auf der Razor page " *_Host. cshtml* " eingerichtet:

```cshtml
<body>
    <app>@(await Html.RenderComponentAsync<App>(RenderMode.ServerPrerendered))</app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

`RenderMode` konfiguriert, ob die Komponente Folgendes hat:

* Wird in die Seite vorab übernommen.
* Wird als statischer HTML-Code auf der Seite gerendert, oder wenn er die erforderlichen Informationen zum Bootstrapping einer blazor-APP vom Benutzer-Agent enthält.

| `RenderMode`        | Beschreibung |
| ------------------- | ----------- |
| `ServerPrerendered` | Rendert die Komponente in statischem HTML-Format und enthält einen Marker für eine blazor-Server-app. Wenn der Benutzer-Agent gestartet wird, wird dieser Marker zum Bootstrapping einer blazor-App verwendet. Parameter werden nicht unterstützt. |
| `Server`            | Rendert einen Marker für eine blazor-Server-app. Die Ausgabe der Komponente ist nicht enthalten. Wenn der Benutzer-Agent gestartet wird, wird dieser Marker zum Bootstrapping einer blazor-App verwendet. Parameter werden nicht unterstützt. |
| `Static`            | Rendert die Komponente in statischem HTML-Format. Parameter werden unterstützt. |

Das Rendering von Serverkomponenten von einer statischen HTML-Seite wird nicht unterstützt.

Der Client stellt erneut eine Verbindung mit dem Server mit dem gleichen Zustand her, der für die Vorabversion der APP verwendet wurde. Wenn sich der Status der APP weiterhin im Arbeitsspeicher befindet, wird der Komponenten Status nach dem Herstellen der signalr-Verbindung nicht erneut umgeleitet.

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a>Zustands behaftete interaktive Komponenten von Razor Pages und Ansichten

Zustands behaftete interaktive Komponenten können einer Razor Page oder Ansicht hinzugefügt werden.

Wenn die Seite oder Ansicht gerendert wird:

* Die Komponente wird mit der Seite oder der Ansicht vorab überstehen.
* Der anfängliche Komponenten Zustand, der für die vorab Generierung verwendet wird, geht verloren.
* Der neue Komponenten Status wird erstellt, wenn die signalr-Verbindung hergestellt wird.

Die folgende Razor Page rendert eine `Counter` Komponente:

```cshtml
<h1>My Razor Page</h1>

@(await Html.RenderComponentAsync<Counter>(RenderMode.ServerPrerendered))
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a>Nicht interaktive Komponenten von Razor Pages und Ansichten

Auf der folgenden Razor-Seite wird die `MyComponent` Komponente statisch mit einem Anfangswert gerendert, der mit einem Formular angegeben wird:

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

@(await Html.RenderComponentAsync<MyComponent>(RenderMode.Static, 
    new { InitialValue = InitialValue }))

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

Da `MyComponent` statisch gerendert wird, kann die Komponente nicht interaktiv sein.

### <a name="detect-when-the-app-is-prerendering"></a>Erkennen, wenn die APP vorab durchgeführt wird

[!INCLUDE[](~/includes/blazor-prerendering.md)]

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a>Konfigurieren des signalr-Clients für blazor-Server-apps

Manchmal müssen Sie den signalr-Client konfigurieren, der von den blazor-Server-Apps verwendet wird. Beispielsweise können Sie die Protokollierung auf dem signalr-Client konfigurieren, um ein Verbindungsproblem zu diagnostizieren.

So konfigurieren Sie den signalr-Client in der Datei *pages/_Host. cshtml* :

* Fügen Sie dem `<script>`-Tag für das Skript " *blazor. Server. js* " ein `autostart="false"` Attribut hinzu.
* Ruft `Blazor.start` auf und übergibt ein Konfigurationsobjekt, das den signalr-Generator angibt.

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:blazor/get-started>
* <xref:signalr/introduction>
