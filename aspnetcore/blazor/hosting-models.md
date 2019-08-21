---
title: ASP.net Core blazor-Hostingmodelle
author: guardrex
description: Grundlegendes zu Client seitigen und serverseitigen edzor-Hostingmodellen.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/01/2019
uid: blazor/hosting-models
ms.openlocfilehash: 64393e826cb17550085f468f5916fca55973908f
ms.sourcegitcommit: 89fcc6cb3e12790dca2b8b62f86609bed6335be9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68993390"
---
# <a name="aspnet-core-blazor-hosting-models"></a>ASP.net Core blazor-Hostingmodelle

Von [Daniel Roth](https://github.com/danroth27)

Blazor ist ein Webframework, das für die clientseitige Ausführung im Browser auf einer [WebAssembly](https://webassembly.org/)-basierten .NET-Laufzeit (*Blazor, clientseitig*) oder serverseitigen Ausführung in ASP.NET Core (*Blazor, serverseitig*) konzipiert ist. Unabhängig vom Hostingmodell sind die APP-und Komponentenmodelle *identisch*.

Informationen zum Erstellen eines Projekts für die in diesem Artikel beschriebenen Hostingmodelle <xref:blazor/get-started>finden Sie unter.

## <a name="client-side"></a>Clientseitig

Das Prinzipal-Hostingmodell für blazor führt die Client seitige Ausführung im Browser in Webassembly aus. Die Blazor-App, die jeweiligen Abhängigkeiten und die .NET-Runtime werden im Browser herunterladen. Die App wird direkt im UI-Thread des Browsers ausgeführt. UI-Updates und Ereignis Behandlung treten innerhalb desselben Prozesses auf. Die Assets der App werden als statische Dateien auf einem Webserver oder Dienst bereitgestellt, in dem statische Inhalte für Clients bereitgestellt werden können.

![Client seitige blazor-Komponente: Die blazor-APP wird in einem UI-Thread im Browser ausgeführt.](hosting-models/_static/client-side.png)

Um eine blazor-App mithilfe des Client seitigen Hostingmodells zu erstellen, verwenden Sie die Vorlage " **blazor Webassembly-App** " ([dotnet New blazorwasm](/dotnet/core/tools/dotnet-new)).

Nachdem Sie die APP-Vorlage für die **blazor-Webassembly** ausgewählt haben, können Sie die APP für die Verwendung eines ASP.net Core-Back-Ends konfigurieren, indem Sie das Kontrollkästchen **ASP.net Core gehostet** aktivieren ([dotnet New blazorwasm--Hosted](/dotnet/core/tools/dotnet-new)). Die ASP.net Core-App bedient die blazor-APP an Clients. Die Client seitige blazor-App kann über das Netzwerk mithilfe von Web-API-aufrufen oder [signalr](xref:signalr/introduction)mit dem Server interagieren.

Die Vorlagen enthalten das Skript " *blazor. Webassembly. js* ", das Folgendes behandelt:

* Herunterladen der .NET-Laufzeit, der APP und der APP-Abhängigkeiten.
* Initialisierung der Laufzeit zum Ausführen der app.

Das Client seitige Hostingmodell bietet mehrere Vorteile:

* Es gibt keine serverseitige .net-Abhängigkeit. Die APP funktioniert nach dem Herunterladen auf den Client vollständig.
* Client Ressourcen und-Funktionen sind vollständig ausgelastet.
* Die Arbeit wird vom Server an den Client verlagert.
* Ein ASP.net Core Webserver ist nicht erforderlich, um die APP zu hosten. Server lose Bereitstellungs Szenarien sind möglich (z. b. das Bereitstellen der APP aus einem CDN).

Es gibt Nachteile zum Client seitigen Hosting:

* Die APP ist auf die Funktionen des Browsers beschränkt.
* Kompatible Client Hardware und Software (z. b. webassemblyunterstützung) ist erforderlich.
* Die Download Größe ist größer, und die Auslastung von apps dauert länger.
* Die Unterstützung von .NET-Laufzeit und-Tools ist weniger ausgereift. Beispielsweise gibt es Einschränkungen in [.NET Standard](/dotnet/standard/net-standard) Unterstützung und Debuggen.

## <a name="server-side"></a>Serverseitig

Mit dem serverseitigen Hostingmodell wird die APP auf dem Server innerhalb einer ASP.net Core-app ausgeführt. Benutzeroberflächenupdates, Ereignisbehandlung und JavaScript-Aufrufe werden über eine [SignalR](xref:signalr/introduction)-Verbindung verarbeitet.

![Der Browser interagiert mit der APP (gehostet innerhalb einer ASP.net Core-APP) auf dem Server über eine signalr-Verbindung.](hosting-models/_static/server-side.png)

Um eine blazor-App mit dem serverseitigen Hostingmodell zu erstellen, verwenden Sie die ASP.net Core **blazor-Server-App** -Vorlage ([dotnet New blazorserver](/dotnet/core/tools/dotnet-new)). Die ASP.net Core-App hostet die serverseitige APP und erstellt den signalr-Endpunkt, mit dem Clients eine Verbindung herstellen.

Die ASP.net Core App verweist auf die `Startup` Klasse der APP, die hinzugefügt werden soll:

* Server seitige Dienste.
* Die APP für die Pipeline für die Anforderungs Verarbeitung.

Das Skript&dagger; " *blazor. Server. js* " stellt die Client Verbindung her. Es liegt in der Verantwortung der APP, den App-Zustand nach Bedarf beizubehalten und wiederherzustellen (z. b. im Fall einer verlorenen Netzwerkverbindung).

Das serverseitige Hostingmodell bietet mehrere Vorteile:

* Die Download Größe ist erheblich kleiner als eine Client seitige APP, und die APP lädt viel schneller.
* Die App nutzt vollständige Vorteile der Serverfunktionen, einschließlich der Verwendung von .net Core-kompatiblen APIs.
* .Net Core auf dem Server wird verwendet, um die APP auszuführen, sodass vorhandene .NET-Tools, wie z. b. das Debuggen, erwartungsgemäß funktionieren.
* Thin Clients werden unterstützt. Serverseitige apps arbeiten z. b. mit Browsern, die Webassembly nicht unterstützen, und auf Geräten mit Ressourcen Beschränkung.
* Die .net-C# /Codebasis der APP, einschließlich des Komponenten Codes der APP, wird nicht für Clients bereitgestellt.

Es gibt Nachteile zum serverseitigen Hosting:

* Eine höhere Latenz ist normalerweise vorhanden. Jede Benutzerinteraktion umfasst einen Netzwerkhop.
* Es gibt keine Offline Unterstützung. Wenn die Client Verbindung fehlschlägt, funktioniert die APP nicht mehr.
* Skalierbarkeit ist eine Herausforderung für apps mit vielen Benutzern. Der Server muss mehrere Clientverbindungen verwalten und den Client Status verarbeiten.
* Ein ASP.net Core Server ist erforderlich, um der APP zu dienen. Server lose Bereitstellungs Szenarien sind nicht möglich (z. b. für die APP aus einem CDN).

&dagger;Das Skript " *blazor. Server. js* " wird von einer eingebetteten Ressource in der ASP.net Core freigegebenen Framework bereitgestellt.

### <a name="reconnection-to-the-same-server"></a>Erneute Verbindung mit demselben Server

Serverseitige blazor-apps erfordern eine aktive signalr-Verbindung mit dem Server. Wenn die Verbindung unterbrochen wird, versucht die APP, erneut eine Verbindung mit dem Server herzustellen. Solange sich der Status des Clients weiterhin im Arbeitsspeicher befindet, wird die Client Sitzung fortgesetzt, ohne den Zustand zu verlieren.
 
Wenn der Client erkennt, dass die Verbindung unterbrochen wurde, wird dem Benutzer eine Standardbenutzer Oberfläche angezeigt, während der Client versucht, die Verbindung wiederherzustellen. Wenn bei der erneuten Verbindungs Herstellung ein Fehler auftritt, wird dem Benutzer die Option zum erneuten versuchen bereitgestellt. Definieren Sie zum Anpassen der Benutzeroberfläche ein Element `components-reconnect-modal` mit als `id` dessen auf der Razor Page *_Host. cshtml* . Der Client aktualisiert dieses Element mit einer der folgenden CSS-Klassen basierend auf dem Status der Verbindung:
 
* `components-reconnect-show`&ndash; Zeigen Sie die Benutzeroberfläche an, um anzugeben, dass die Verbindung unterbrochen wurde und der Client versucht, die Verbindung wiederherzustellen.
* `components-reconnect-hide`&ndash; Der Client verfügt über eine aktive Verbindung, die die Benutzeroberfläche ausblenden.
* `components-reconnect-failed`&ndash; Fehler beim erneuten Herstellen der Verbindung. Um erneut eine erneute Verbindung herzustellen, `window.Blazor.reconnect()`wird aufgerufen.

### <a name="stateful-reconnection-after-prerendering"></a>Zustands behaftete erneute Verbindung nach der vorab Ausführung
 
Serverseitige blazor-apps werden standardmäßig so eingerichtet, dass Sie die Benutzeroberfläche auf dem Server vorab ausführen, bevor die Client Verbindung mit dem Server hergestellt wird. Dies wird auf der Razor page " *_Host. cshtml* " eingerichtet:
 
```cshtml
<body>
    <app>@(await Html.RenderComponentAsync<App>())</app>
 
    <script src="_framework/blazor.server.js"></script>
</body>
```
 
Der Client stellt erneut eine Verbindung mit dem Server mit dem gleichen Zustand her, der für die Vorabversion der APP verwendet wurde. Wenn sich der Status der APP weiterhin im Arbeitsspeicher befindet, wird der Komponenten Status nach dem Herstellen der signalr-Verbindung nicht erneut umgeleitet.

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a>Zustands behaftete interaktive Komponenten von Razor Pages und Ansichten
 
Zustands behaftete interaktive Komponenten können einer Razor Page oder Ansicht hinzugefügt werden. Wenn die Seite oder Sicht gerendert wird, wird die Komponente mit der Komponente vorab in diese integriert. Die APP stellt dann erneut eine Verbindung mit dem Komponenten Zustand her, sobald die Client Verbindung hergestellt ist, solange sich der Status noch im Speicher befindet.
 
Beispielsweise wird mit der folgenden Razor Page eine `Counter` Komponente mit einer anfänglichen Anzahl gerendert, die mithilfe eines Formulars angegeben wird:
 
```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialCount" />
    <button type="submit">Set initial count</button>
</form>
 
@(await Html.RenderComponentAsync<Counter>(new { InitialCount = InitialCount }))
 
@code {
    [BindProperty(SupportsGet=true)]
    public int InitialCount { get; set; }
}
```

### <a name="detect-when-the-app-is-prerendering"></a>Erkennen, wenn die APP vorab durchgeführt wird
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

### <a name="configure-the-signalr-client-for-blazor-server-side-apps"></a>Konfigurieren des signalr-Clients für serverseitige blazor-apps
 
Manchmal müssen Sie den signalr-Client konfigurieren, der von serverseitigen blazor-Apps verwendet wird. Beispielsweise können Sie die Protokollierung auf dem signalr-Client konfigurieren, um ein Verbindungsproblem zu diagnostizieren.
 
So konfigurieren Sie den signalr-Client in der Datei *pages/_Host. cshtml* :

* Fügen Sie `autostart="false"` dem `<script>` -Tag für das Skript " *blazor. Server. js* " ein Attribut hinzu.
* Ruft `Blazor.start` auf und übergibt ein Konfigurationsobjekt, das den signalr-Generator angibt.
 
```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging(2); // LogLevel.Information
    }
  });
</script>
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:blazor/get-started>
* <xref:signalr/introduction>
