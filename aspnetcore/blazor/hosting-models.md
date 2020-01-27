---
title: ASP.net Core Blazor Hostingmodellen
author: guardrex
description: Verstehen Blazor Webassembly-und Blazor Server-Hostingmodellen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
- blazor.webassembly.js
uid: blazor/hosting-models
ms.openlocfilehash: 2c66bede9c1e31b22fd1612fead556176d6f192b
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76726863"
---
# <a name="aspnet-core-opno-locblazor-hosting-models"></a>ASP.net Core Blazor Hostingmodellen

Von [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor ist ein Webframework, das für die Client seitige Ausführung im Browser auf einer [webassemblybasierten](https://webassembly.org/) .NET-Runtime ( *Blazor Webassembly*) oder Serverseite in ASP.net Core ( *Blazor Server*) konzipiert ist. Unabhängig vom Hostingmodell sind die APP-und Komponentenmodelle *identisch*.

Informationen zum Erstellen eines Projekts für die in diesem Artikel beschriebenen Hostingmodelle finden Sie unter <xref:blazor/get-started>.

## <a name="opno-locblazor-webassembly"></a>Blazor WebAssembly

Das Prinzipal-Hostingmodell für Blazor wird Client seitig im Browser in Webassembly ausgeführt. Die Blazor-App, die jeweiligen Abhängigkeiten und die .NET-Runtime werden im Browser heruntergeladen. Die App wird direkt im UI-Thread des Browsers ausgeführt. UI-Updates und Ereignis Behandlung treten innerhalb desselben Prozesses auf. Die Assets der App werden als statische Dateien auf einem Webserver oder Dienst bereitgestellt, in dem statische Inhalte für Clients bereitgestellt werden können.

![[! Schel. No-Loc (blazor)] Webassembly: [! Schel. Die APP NO-LOC (blazor)] wird in einem UI-Thread im Browser ausgeführt.](hosting-models/_static/blazor-webassembly.png)

Verwenden Sie zum Erstellen einer Blazor-App mithilfe des Client seitigen Hostingmodells die **Blazor Webassembly-App** -Vorlage ([dotnet New blazorwasm](/dotnet/core/tools/dotnet-new)).

Nachdem Sie die Vorlage **Blazor Webassembly-App** ausgewählt haben, haben Sie die Möglichkeit, die APP für die Verwendung eines ASP.net Core-Back-Ends zu konfigurieren, indem Sie das Kontrollkästchen **ASP.net Core gehostet** aktivieren ([dotnet New blazorwasm--Hosted](/dotnet/core/tools/dotnet-new)). Die ASP.net Core-App dient der Blazor-App für Clients. Die Blazor Webassembly-App kann mithilfe von Web-API-aufrufen oder [SignalR](xref:signalr/introduction)mit dem Server über das Netzwerk interagieren.

Die Vorlagen enthalten das `blazor.webassembly.js` Skript, das Folgendes behandelt:

* Herunterladen der .NET-Laufzeit, der APP und der APP-Abhängigkeiten.
* Initialisierung der Laufzeit zum Ausführen der app.

Das Blazor Webassembly-Hostingmodell bietet mehrere Vorteile:

* Es gibt keine serverseitige .net-Abhängigkeit. Die APP funktioniert nach dem Herunterladen auf den Client vollständig.
* Client Ressourcen und-Funktionen sind vollständig ausgelastet.
* Die Arbeit wird vom Server an den Client verlagert.
* Ein ASP.net Core Webserver ist nicht erforderlich, um die APP zu hosten. Server lose Bereitstellungs Szenarien sind möglich (z. b. das Bereitstellen der APP aus einem CDN).

Es gibt Nachteile für Blazor Webassembly-Hosting:

* Die APP ist auf die Funktionen des Browsers beschränkt.
* Kompatible Client Hardware und Software (z. b. webassemblyunterstützung) ist erforderlich.
* Die Download Größe ist größer, und die Auslastung von apps dauert länger.
* Die Unterstützung von .NET-Laufzeit und-Tools ist weniger ausgereift. Beispielsweise gibt es Einschränkungen in [.NET Standard](/dotnet/standard/net-standard) Unterstützung und Debuggen.

## <a name="opno-locblazor-server"></a>Blazor Server

Mit dem Blazor Server-Hostingmodell wird die APP auf dem Server innerhalb einer ASP.net Core-app ausgeführt. Benutzeroberflächenupdates, Ereignisbehandlung und JavaScript-Aufrufe werden über eine [SignalR](xref:signalr/introduction)-Verbindung verarbeitet.

![Der Browser interagiert mit der APP (gehostet innerhalb einer ASP.net Core-APP) auf dem Server über einen [! Schel. No-Loc (signalr)]-Verbindung.](hosting-models/_static/blazor-server.png)

Verwenden Sie zum Erstellen einer Blazor-App mithilfe des Blazor Server-Hostingmodells die ASP.net Core **Blazor Server-App** -Vorlage ([dotnet New blazorserver](/dotnet/core/tools/dotnet-new)). Die ASP.net Core-App hostet die Blazor Server-APP und erstellt den SignalR Endpunkt, auf dem Clients eine Verbindung herstellen.

Die ASP.net Core-App verweist auf die `Startup` Klasse der APP, um Folgendes hinzuzufügen:

* Server seitige Dienste.
* Die APP für die Pipeline für die Anforderungs Verarbeitung.

Mit dem `blazor.server.js` Skript&dagger; wird die Client Verbindung hergestellt. Es liegt in der Verantwortung der APP, den App-Zustand nach Bedarf beizubehalten und wiederherzustellen (z. b. im Fall einer verlorenen Netzwerkverbindung).

Das Blazor Server-Hostingmodell bietet mehrere Vorteile:

* Die Download Größe ist erheblich kleiner als eine Blazor Webassembly-APP, und die APP lädt viel schneller.
* Die App nutzt vollständige Vorteile der Serverfunktionen, einschließlich der Verwendung von .net Core-kompatiblen APIs.
* .Net Core auf dem Server wird verwendet, um die APP auszuführen, sodass vorhandene .NET-Tools, wie z. b. das Debuggen, erwartungsgemäß funktionieren.
* Thin Clients werden unterstützt. Blazor Server-apps funktionieren z. b. mit Browsern, die Webassembly nicht unterstützen, und auf Geräten, die auf Ressourcen beschränkt sind.
* Die .net-C# /Codebasis der APP, einschließlich des Komponenten Codes der APP, wird nicht für Clients bereitgestellt.

Es gibt Nachteile für Blazor Server Hosting:

* Eine höhere Latenz ist normalerweise vorhanden. Jede Benutzerinteraktion umfasst einen Netzwerkhop.
* Es gibt keine Offline Unterstützung. Wenn die Client Verbindung fehlschlägt, funktioniert die APP nicht mehr.
* Skalierbarkeit ist eine Herausforderung für apps mit vielen Benutzern. Der Server muss mehrere Clientverbindungen verwalten und den Client Status verarbeiten.
* Ein ASP.net Core Server ist erforderlich, um der APP zu dienen. Server lose Bereitstellungs Szenarien sind nicht möglich (z. b. für die APP aus einem CDN).

&dagger;das `blazor.server.js` Skript von einer eingebetteten Ressource im ASP.net Core freigegebenen Framework bereitgestellt wird.

### <a name="comparison-to-server-rendered-ui"></a>Vergleich mit der Server gerenderten Benutzeroberfläche

Eine Möglichkeit, Blazor Server-apps zu verstehen, besteht darin, zu verstehen, wie Sie sich von herkömmlichen Modellen zum Rendern von Benutzeroberflächen in ASP.net Core Apps mithilfe von Razor-Ansichten oder Razor Pages Beide Modelle verwenden die Razor-Sprache, um HTML-Inhalte zu beschreiben. Sie unterscheiden sich jedoch deutlich von der Darstellung von Markup.

Wenn eine Razor Page oder eine Ansicht gerendert wird, gibt jede Zeile von Razor-Code HTML-Code in Textform aus. Nach dem Rendering verwirft der Server die Seiten-oder Sicht Instanz, einschließlich eines beliebigen Zustands, der erstellt wurde. Wenn eine andere Anforderung für die Seite auftritt, z. b. wenn die Server Validierung fehlschlägt und die Validierungs Zusammenfassung angezeigt wird:

* Die gesamte Seite wird erneut in HTML-Text umgeleitet.
* Die Seite wird an den Client gesendet.

Eine Blazor-App besteht aus wiederverwendbaren Elementen der UI, die als *Komponenten*bezeichnet werden. Eine Komponente enthält C# Code, Markup und andere Komponenten. Wenn eine Komponente gerendert wird, erstellt Blazor ein Diagramm der enthaltenen Komponenten, ähnlich wie HTML-oder XML-Dokumentobjektmodell (DOM). Dieses Diagramm enthält den Komponenten Zustand in Eigenschaften und Feldern. Blazor wertet das Komponenten Diagramm aus, sodass eine binäre Darstellung des Markups erzeugt wird. Das binäre Format kann wie folgt lauten:

* Wird in HTML-Text (während der vorab Generierung) umgewandelt.
* Wird verwendet, um das Markup beim regulären Rendering effizient zu aktualisieren.

Ein Benutzeroberflächen Update in Blazor wird durch Folgendes ausgelöst:

* Benutzerinteraktion, z. b. das Auswählen einer Schaltfläche.
* App-Trigger, z. b. ein Timer.

Das Diagramm wird erneut ausgeführt, und es wird ein UI- *diff* (Differenz) berechnet. Dieser Unterschied ist der kleinste Satz an DOM-Änderungen, die erforderlich sind, um die Benutzeroberfläche auf dem Client zu aktualisieren. Der diff wird in einem binären Format an den Client gesendet und vom Browser angewendet.

Eine Komponente wird verworfen, nachdem der Benutzer auf dem Client dorthin navigiert ist. Während ein Benutzer mit einer Komponente interagiert, muss der Zustand der Komponente (Dienste, Ressourcen) im Arbeitsspeicher des Servers gespeichert werden. Da der Status vieler Komponenten möglicherweise gleichzeitig vom Server verwaltet wird, ist die Arbeitsspeicher Erschöpfung ein Problem, das behoben werden muss. Anleitungen zum Erstellen einer Blazor Server-APP, um die optimale Verwendung des Server Arbeitsspeichers sicherzustellen, finden Sie unter <xref:security/blazor/server>.

### <a name="integrate-razor-components-into-razor-pages-and-mvc-apps"></a>Integrieren von Razor-Komponenten in Razor Pages und MVC-apps

#### <a name="use-components-in-pages-and-views"></a>Verwenden von Komponenten in Seiten und Ansichten

Eine vorhandene Razor Pages-oder MVC-App kann Razor-Komponenten in Seiten und Ansichten integrieren:

1. In der Layoutdatei der APP ( *_Layout. cshtml*):

   * Fügen Sie dem `<head>`-Element das folgende `<base>`-Tag hinzu:

     ```html
     <base href="~/" />
     ```

     Beim `href` Wert (dem *App-Basispfad*) im vorhergehenden Beispiel wird davon ausgegangen, dass sich die APP im Stamm-URL-Pfad (`/`) befindet. Wenn es sich bei der App um eine untergeordnete Anwendung handelt, befolgen Sie die Anweisungen im Abschnitt *App-Basispfad* des <xref:host-and-deploy/blazor/index#app-base-path> Artikels.

     Die Datei " *_Layout. cshtml* " befindet sich im Ordner " *pages/Shared* " in einer Razor Pages-APP oder in *Sichten/freigegebenen* Ordnern in einer MVC-app.

   * Fügen Sie ein `<script>`-Tag für das Skript " *blazor. Server. js* " innerhalb des schließenden `</body>`-Tags hinzu:

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     Das-Framework fügt der APP das Skript " *blazor. Server. js* " hinzu. Es ist nicht erforderlich, das Skript manuell der APP hinzuzufügen.

1. Fügen Sie dem Stamm Ordner des Projekts eine *_Imports. Razor* -Datei mit folgendem Inhalt hinzu (ändern Sie den letzten Namespace, `MyAppNamespace`, in den Namespace der APP):

   ```csharp
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. Fügen Sie in `Startup.ConfigureServices`den Blazor Server-Dienst hinzu:

   ```csharp
   services.AddServerSideBlazor();
   ```

1. Fügen Sie in `Startup.Configure`den Blazor Hub-Endpunkt `app.UseEndpoints`hinzu:

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. Integrieren von Komponenten in beliebige Seiten oder Ansichten. Weitere Informationen finden Sie im Abschnitt *integrieren von Komponenten in Razor Pages und MVC-apps* des <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps> Artikels.

#### <a name="use-routable-components-in-a-razor-pages-app"></a>Verwenden von Routing fähigen Komponenten in einer Razor Pages-App

So unterstützen Sie Routing fähige Razor-Komponenten in Razor Pages-apps:

1. Befolgen Sie die Anweisungen im Abschnitt [Verwenden von Komponenten in Seiten und Ansichten](#use-components-in-pages-and-views) .

1. Fügen Sie die Datei " *app. Razor* " dem Stamm des Projekts mit folgendem Inhalt hinzu:

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. Fügen Sie dem Ordner *pages* eine *_Host. cshtml* -Datei mit folgendem Inhalt hinzu:

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   Komponenten verwenden die freigegebene *_Layout. cshtml* -Datei für das Layout.

1. Fügen Sie eine Route mit niedriger Priorität für die *_Host. cshtml* -Seite zur Endpunkt Konfiguration in `Startup.Configure`hinzu:

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. Fügen Sie der APP Routing fähige Komponenten hinzu. Beispiel:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   Wenn Sie einen benutzerdefinierten Ordner zum Speichern der APP-Komponenten verwenden, fügen Sie den Namespace, der den Ordner darstellt, der Datei *pages/_ViewImports. cshtml* hinzu. Weitere Informationen finden Sie unter <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>.

#### <a name="use-routable-components-in-an-mvc-app"></a>Verwenden von Routing fähigen Komponenten in einer MVC-App

So unterstützen Sie Routing fähige Razor-Komponenten in MVC-apps:

1. Befolgen Sie die Anweisungen im Abschnitt [Verwenden von Komponenten in Seiten und Ansichten](#use-components-in-pages-and-views) .

1. Fügen Sie die Datei " *app. Razor* " dem Stamm des Projekts mit folgendem Inhalt hinzu:

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. Fügen Sie dem Ordner " *views/Home* " eine *_Host. cshtml* -Datei mit folgendem Inhalt hinzu:

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   Komponenten verwenden die freigegebene *_Layout. cshtml* -Datei für das Layout.

1. Fügen Sie dem Home-Controller eine Aktion hinzu:

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. Fügen Sie eine Route mit niedriger Priorität für die Controller Aktion hinzu, die die Ansicht *_Host. cshtml* an die Endpunkt Konfiguration in `Startup.Configure`zurückgibt:

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. Erstellen Sie einen Ordner *pages* , und fügen Sie der APP Routing fähige Komponenten hinzu. Beispiel:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   Wenn Sie einen benutzerdefinierten Ordner zum Speichern der APP-Komponenten verwenden, fügen Sie den Namespace, der den Ordner darstellt, der *views/_ViewImports. cshtml-* Datei hinzu. Weitere Informationen finden Sie unter <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>.

### <a name="circuits"></a>Fen

Eine Blazor Server-App basiert auf [ASP.net Core SignalR](xref:signalr/introduction). Jeder Client kommuniziert über *mindestens eine SignalR* Verbindung, die als Verbindung bezeichnet wird, mit dem Server. Eine Verbindung ist BlazorAbstraktion über SignalR Verbindungen, die temporäre Netzwerkunterbrechungen tolerieren können. Wenn ein Blazor Client feststellt, dass die Verbindung mit dem SignalR getrennt ist, versucht er, eine Verbindung mit dem Server herzustellen, indem eine neue SignalR Verbindung verwendet wird.

Jeder Browser Bildschirm (Browser Registerkarte oder IFRAME), der mit einer Blazor Server-App verbunden ist, verwendet eine SignalR Verbindung. Dies ist jedoch ein weiterer wichtiger Unterschied im Vergleich zu typischen, von Servern gerenderten apps. In einer Server gerenderten APP wird das Öffnen derselben app in mehreren Browser Bildschirmen in der Regel nicht zu zusätzlichen Ressourcenanforderungen auf dem Server übertragen. In einer Blazor Server-App erfordert jeder Browser Bildschirm eine separate Verbindung und separate Instanzen des Komponenten Zustands, die vom Server verwaltet werden.

Blazor das Schließen einer Browser Registerkarte oder navigieren zu einer externen URL zu einer Ordnungs *gemäßen Beendigung.* Im Fall einer ordnungsgemäßen Beendigung werden die Verbindung und zugehörige Ressourcen sofort freigegeben. Ein Client kann möglicherweise auch nicht ordnungsgemäß getrennt werden, beispielsweise aufgrund einer Netzwerk Unterbrechung. Blazor Server speichert getrennte Verbindungen für ein konfigurierbares Intervall, damit der Client die Verbindung wiederherstellen kann. Weitere Informationen finden Sie im Abschnitt [Wiederherstellen der Verbindung mit dem gleichen Server](#reconnection-to-the-same-server) .

### <a name="ui-latency"></a>UI-Latenz

Die Benutzeroberflächen Latenz ist die Zeit, die Sie von einer initiierten Aktion bis zum Zeitpunkt der Aktualisierung der Benutzeroberfläche benötigt. Kleinere Werte für die Benutzeroberflächen Latenz sind zwingend erforderlich, damit eine APP für einen Benutzer reaktionsfähig ist. In einer Blazor Server-APP wird jede Aktion an den Server gesendet, verarbeitet und ein UI-diff zurückgesendet. Folglich ist die Benutzeroberflächen Latenz die Summe der Netzwerk Latenz und der Server Latenz bei der Verarbeitung der Aktion.

Für eine Branchen-APP, die auf ein privates Unternehmensnetzwerk beschränkt ist, sind die Auswirkungen auf die Benutzer Wahrnehmung von Latenzzeit aufgrund der Netzwerk Latenz in der Regel nicht wahrnehmbar. Für eine APP, die über das Internet bereitgestellt wird, kann die Latenz für Benutzer bemerkbar werden, insbesondere, wenn die Benutzer weitgehend geografisch verteilt sind.

Die Speicherauslastung kann auch zur APP-Latenz beitragen. Eine erhöhte Arbeitsspeicher Auslastung führt zu häufigen Garbage Collection-oder Paging-Speicher auf den Datenträger, die beide die Leistung der APP beeinträchtigen und somit die Benutzeroberflächen Latenz erhöhen. Weitere Informationen finden Sie unter <xref:security/blazor/server>.

Blazor Server-apps sollten optimiert werden, um die Benutzeroberflächen Latenz zu minimieren, indem Netzwerk Latenz und Speicherauslastung reduziert werden Einen Ansatz zum Messen der Netzwerk Latenz finden Sie unter <xref:host-and-deploy/blazor/server#measure-network-latency>. Weitere Informationen zu SignalR und Blazorfinden Sie unter:

* <xref:host-and-deploy/blazor/server>
* <xref:security/blazor/server>

### <a name="connection-to-the-server"></a>Verbindung mit dem Server

Blazor Server-apps benötigen eine aktive SignalR Verbindung mit dem Server. Wenn die Verbindung unterbrochen wird, versucht die APP, erneut eine Verbindung mit dem Server herzustellen. Solange sich der Status des Clients weiterhin im Arbeitsspeicher befindet, wird die Client Sitzung fortgesetzt, ohne den Zustand zu verlieren.

#### <a name="reconnection-to-the-same-server"></a>Erneute Verbindung mit demselben Server

Eine Blazor Server-App als Antwort auf die erste Client Anforderung, die den UI-Status auf dem Server festlegt. Wenn der Client versucht, eine SignalR Verbindung zu erstellen, muss der Client erneut eine Verbindung mit dem gleichen Server herstellen. Blazor Server-apps, die mehr als einen Back-End-Server verwenden, sollten persistente *Sitzungen* für SignalR Verbindungen implementieren.

Sie sollten [Azure SignalR Service](/azure/azure-signalr) für Blazor Server-Apps verwenden. Der Dienst ermöglicht das zentrale Hochskalieren einer Blazor Server-App auf eine große Anzahl gleichzeitiger SignalR-Verbindungen. Persistente Sitzungen werden für den Azure SignalR-Dienst aktiviert, indem die `ServerStickyMode` Option oder der Konfigurations Wert für den Dienst auf `Required`festgelegt wird. Weitere Informationen finden Sie unter <xref:host-and-deploy/blazor/server#signalr-configuration>.

Bei der Verwendung von IIS sind persistente Sitzungen mit Routing von Anwendungsanforderungen aktiviert. Weitere Informationen finden Sie unter [HTTP-Lastenausgleiche mithilfe von Routing von Anwendungsanforderungen](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).

#### <a name="reflect-the-connection-state-in-the-ui"></a>Reflektieren des Verbindungs Zustands in der Benutzeroberfläche

Wenn der Client erkennt, dass die Verbindung unterbrochen wurde, wird dem Benutzer eine Standardbenutzer Oberfläche angezeigt, während der Client versucht, die Verbindung wiederherzustellen. Wenn bei der erneuten Verbindungs Herstellung ein Fehler auftritt, wird dem Benutzer die Option zum erneuten versuchen bereitgestellt.

Zum Anpassen der Benutzeroberfläche definieren Sie ein Element mit einem `id` `components-reconnect-modal` im `<body>` der Razor Page *_Host. cshtml* :

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

In der folgenden Tabelle werden die CSS-Klassen beschrieben, die auf das `components-reconnect-modal` Element angewendet werden.

| CSS-Klasse                       | Gibt&hellip; |
| ------------------------------- | ------------------------- |
| `components-reconnect-show`     | Eine verlorene Verbindung. Der Client versucht, die Verbindung wiederherzustellen. Zeigen Sie die modale an. |
| `components-reconnect-hide`     | Eine aktive Verbindung mit dem Server wird wieder hergestellt. Blenden Sie den modalen aus. |
| `components-reconnect-failed`   | Fehler beim erneuten Herstellen der Verbindung, wahrscheinlich aufgrund eines Netzwerk Fehlers. Um erneut eine Verbindung herzustellen, wenden Sie `window.Blazor.reconnect()`an. |
| `components-reconnect-rejected` | Erneute Verbindung abgelehnt. Der Server wurde erreicht, hat jedoch die Verbindung abgelehnt, und der Benutzer Zustand auf dem Server geht verloren. Um die APP erneut zu laden, wenden Sie `location.reload()`an. Der Verbindungsstatus kann folgende Ergebnisse haben:<ul><li>Ein Absturz in der serverseitigen Verbindung tritt auf.</li><li>Der Client ist so lange getrennt, dass der Server den Zustand des Benutzers löscht. Instanzen der Komponenten, mit denen der Benutzer interagiert, werden verworfen.</li><li>Der Server wird neu gestartet, oder der Arbeitsprozess der APP wird wieder verwendet.</li></ul> |

### <a name="stateful-reconnection-after-prerendering"></a>Zustands behaftete erneute Verbindung nach der vorab Ausführung

Blazor Server-apps werden standardmäßig so eingerichtet, dass Sie die Benutzeroberfläche auf dem Server vorab ausführen, bevor die Client Verbindung mit dem Server hergestellt wird. Dies wird auf der Razor Page *_Host. cshtml* eingerichtet:

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

`RenderMode` konfiguriert, ob die Komponente Folgendes hat:

* Wird in die Seite vorab übernommen.
* Wird auf der Seite als statischer HTML-Code gerendert, oder, wenn er die erforderlichen Informationen zum Bootstrapping einer Blazor-APP vom Benutzer-Agent enthält.

| `RenderMode`        | Beschreibung |
| ------------------- | ----------- |
| `ServerPrerendered` | Rendert die Komponente in statischem HTML-Format und enthält einen Marker für eine Blazor Server-app. Wenn der Benutzer-Agent gestartet wird, wird dieser Marker zum Bootstrapping einer Blazor-App verwendet. |
| `Server`            | Rendert einen Marker für eine Blazor Server-app. Die Ausgabe der Komponente ist nicht enthalten. Wenn der Benutzer-Agent gestartet wird, wird dieser Marker zum Bootstrapping einer Blazor-App verwendet. |
| `Static`            | Rendert die Komponente in statischem HTML-Format. |

Das Rendering von Serverkomponenten von einer statischen HTML-Seite wird nicht unterstützt.

Wenn `RenderMode` `ServerPrerendered`ist, wird die Komponente anfänglich statisch als Teil der Seite gerendert. Sobald der Browser eine Verbindung mit dem Server herstellt, wird die Komponente *wieder*gerendert, und die Komponente ist nun interaktiv. Wenn die [OnInitialized {Async}](xref:blazor/lifecycle#component-initialization-methods) -Lebenszyklus Methode zum Initialisieren der Komponente vorhanden ist, wird die-Methode *zweimal*ausgeführt:

* Bei statischer vorab Erstellung der Komponente.
* Nachdem die Server Verbindung hergestellt wurde.

Dies kann zu einer merklichen Änderung in den Daten führen, die in der Benutzeroberfläche angezeigt werden, wenn die Komponente schließlich gerendert wird.

So vermeiden Sie das doppelte renderingszenario in einer Blazor Server-App:

* Übergeben Sie einen Bezeichner, der verwendet werden kann, um den Zustand während der vorab Ausführung zwischenzuspeichern und den Zustand nach dem Neustart der APP abzurufen.
* Verwenden Sie den Bezeichner während der vorab Einschreibung, um den Komponenten Zustand zu speichern.
* Verwenden Sie nach der vorab Ausführung den Bezeichner, um den zwischengespeicherten Zustand abzurufen.

Der folgende Code veranschaulicht eine aktualisierte `WeatherForecastService` in einer Vorlagen basierten Blazor Server-APP, die das doppelte Rendering vermeidet:

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

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a>Zustands behaftete interaktive Komponenten von Razor Pages und Ansichten

Zustands behaftete interaktive Komponenten können einer Razor Page oder Ansicht hinzugefügt werden.

Wenn die Seite oder Ansicht gerendert wird:

* Die Komponente wird mit der Seite oder der Ansicht vorab überstehen.
* Der anfängliche Komponenten Zustand, der für die vorab Generierung verwendet wird, geht verloren.
* Der neue Komponenten Status wird erstellt, wenn die SignalR Verbindung hergestellt wird.

Die folgende Razor Page rendert eine `Counter` Komponente:

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a>Nicht interaktive Komponenten von Razor Pages und Ansichten

Auf der folgenden Razor-Seite wird die `Counter` Komponente statisch mit einem Anfangswert gerendert, der mit einem Formular angegeben wird:

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

Da `MyComponent` statisch gerendert wird, kann die Komponente nicht interaktiv sein.

### <a name="detect-when-the-app-is-prerendering"></a>Erkennen, wenn die APP vorab durchgeführt wird

[!INCLUDE[](~/includes/blazor-prerendering.md)]

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a>Konfigurieren des SignalR Clients für Blazor Server-apps

Manchmal müssen Sie den SignalR Client konfigurieren, der von Blazor Server-Apps verwendet wird. Beispielsweise können Sie die Protokollierung für den SignalR Client konfigurieren, um ein Verbindungsproblem zu diagnostizieren.

So konfigurieren Sie den SignalR-Client in der Datei *pages/_Host. cshtml* :

* Fügen Sie dem `<script>`-Tag für das `blazor.server.js` Skript ein `autostart="false"` Attribut hinzu.
* Ruft `Blazor.start` auf und übergibt ein Konfigurationsobjekt, das den SignalR Generator angibt.

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

## <a name="additional-resources"></a>Weitere Ressourcen

* <xref:blazor/get-started>
* <xref:signalr/introduction>
