---
title: ASP.net Core Blazor Hostingmodellen
author: guardrex
description: Verstehen Blazor Webassembly-und Blazor Server-Hostingmodellen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-models
ms.openlocfilehash: 54be0e032a60c69880f428e52f9d778032385dc5
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/19/2020
ms.locfileid: "77447047"
---
# <a name="aspnet-core-opno-locblazor-hosting-models"></a>ASP.net Core Blazor Hostingmodellen

Von [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor ist ein Webframework, das für die Client seitige Ausführung im Browser auf einer [webassemblybasierten](https://webassembly.org/) .NET-Runtime ( *Blazor Webassembly*) oder Serverseite in ASP.net Core ( *Blazor Server*) konzipiert ist. Unabhängig vom Hostingmodell sind die APP-und Komponentenmodelle *identisch*.

Informationen zum Erstellen eines Projekts für die in diesem Artikel beschriebenen Hostingmodelle finden Sie unter <xref:blazor/get-started>.

Informationen zur erweiterten Konfiguration finden Sie unter <xref:blazor/hosting-model-configuration>.

## <a name="opno-locblazor-webassembly"></a>Blazor WebAssembly

Das Prinzipal-Hostingmodell für Blazor wird Client seitig im Browser in Webassembly ausgeführt. Die Blazor-App, die jeweiligen Abhängigkeiten und die .NET-Runtime werden im Browser heruntergeladen. Die App wird direkt im UI-Thread des Browsers ausgeführt. UI-Updates und Ereignis Behandlung treten innerhalb desselben Prozesses auf. Die Assets der App werden als statische Dateien auf einem Webserver oder Dienst bereitgestellt, in dem statische Inhalte für Clients bereitgestellt werden können.

![[! Schel. No-Loc (blazor)] Webassembly: Der [! Schel. Die APP NO-LOC (blazor)] wird in einem UI-Thread im Browser ausgeführt.](hosting-models/_static/blazor-webassembly.png)

Verwenden Sie zum Erstellen einer Blazor-App mithilfe des Client seitigen Hostingmodells die **Blazor Webassembly-App** -Vorlage ([dotnet New blazorwasm](/dotnet/core/tools/dotnet-new)).

Nachdem Sie die Vorlage **Blazor Webassembly-App** ausgewählt haben, haben Sie die Möglichkeit, die APP für die Verwendung eines ASP.net Core-Back-Ends zu konfigurieren, indem Sie das Kontrollkästchen **ASP.net Core gehostet** aktivieren ([dotnet New blazorwasm--Hosted](/dotnet/core/tools/dotnet-new)). Die ASP.net Core-App dient der Blazor-App für Clients. Die Blazor Webassembly-App kann über das Netzwerk mit dem Server mit Web-API-aufrufen oder [SignalR](xref:signalr/introduction) (<xref:tutorials/signalr-blazor-webassembly>) interagieren.

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

* Wird in HTML-Text (während der vorab Generierung&dagger;) umgewandelt.
* Wird verwendet, um das Markup beim regulären Rendering effizient zu aktualisieren.

&dagger;vorab &ndash; die angeforderte Razor-Komponente auf dem Server in statisches HTML kompiliert und an den Client gesendet, wo Sie für den Benutzer *gerendert* wird. Nachdem die Verbindung zwischen dem Client und dem Server hergestellt wurde, werden die statischen vorab Elemente der Komponente durch interaktive Elemente ersetzt. Durch das vorab Rendering wird die APP reaktionsfähiger für den Benutzer.

Ein Benutzeroberflächen Update in Blazor wird durch Folgendes ausgelöst:

* Benutzerinteraktion, z. b. das Auswählen einer Schaltfläche.
* App-Trigger, z. b. ein Timer.

Das Diagramm wird erneut ausgeführt, und es wird ein UI- *diff* (Differenz) berechnet. Dieser Unterschied ist der kleinste Satz an DOM-Änderungen, die erforderlich sind, um die Benutzeroberfläche auf dem Client zu aktualisieren. Der diff wird in einem binären Format an den Client gesendet und vom Browser angewendet.

Eine Komponente wird verworfen, nachdem der Benutzer auf dem Client dorthin navigiert ist. Während ein Benutzer mit einer Komponente interagiert, muss der Zustand der Komponente (Dienste, Ressourcen) im Arbeitsspeicher des Servers gespeichert werden. Da der Status vieler Komponenten möglicherweise gleichzeitig vom Server verwaltet wird, ist die Arbeitsspeicher Erschöpfung ein Problem, das behoben werden muss. Anleitungen zum Erstellen einer Blazor Server-APP, um die optimale Verwendung des Server Arbeitsspeichers sicherzustellen, finden Sie unter <xref:security/blazor/server>.

### <a name="circuits"></a>Fen

Eine Blazor Server-App basiert auf [ASP.net Core SignalR](xref:signalr/introduction). Jeder Client kommuniziert über *mindestens eine SignalR* Verbindung, die als Verbindung bezeichnet wird, mit dem Server. Eine Verbindung ist BlazorAbstraktion über SignalR Verbindungen, die temporäre Netzwerkunterbrechungen tolerieren können. Wenn ein Blazor Client feststellt, dass die Verbindung mit dem SignalR getrennt ist, versucht er, eine Verbindung mit dem Server herzustellen, indem eine neue SignalR Verbindung verwendet wird.

Jeder Browser Bildschirm (Browser Registerkarte oder IFRAME), der mit einer Blazor Server-App verbunden ist, verwendet eine SignalR Verbindung. Dies ist jedoch ein weiterer wichtiger Unterschied im Vergleich zu typischen, von Servern gerenderten apps. In einer Server gerenderten APP wird das Öffnen derselben app in mehreren Browser Bildschirmen in der Regel nicht zu zusätzlichen Ressourcenanforderungen auf dem Server übertragen. In einer Blazor Server-App erfordert jeder Browser Bildschirm eine separate Verbindung und separate Instanzen des Komponenten Zustands, die vom Server verwaltet werden.

Blazor das Schließen einer Browser Registerkarte oder navigieren zu einer externen URL zu einer Ordnungs *gemäßen Beendigung.* Im Fall einer ordnungsgemäßen Beendigung werden die Verbindung und zugehörige Ressourcen sofort freigegeben. Ein Client kann möglicherweise auch nicht ordnungsgemäß getrennt werden, beispielsweise aufgrund einer Netzwerk Unterbrechung. Blazor Server speichert getrennte Verbindungen für ein konfigurierbares Intervall, damit der Client die Verbindung wiederherstellen kann.

### <a name="ui-latency"></a>UI-Latenz

Die Benutzeroberflächen Latenz ist die Zeit, die Sie von einer initiierten Aktion bis zum Zeitpunkt der Aktualisierung der Benutzeroberfläche benötigt. Kleinere Werte für die Benutzeroberflächen Latenz sind zwingend erforderlich, damit eine APP für einen Benutzer reaktionsfähig ist. In einer Blazor Server-APP wird jede Aktion an den Server gesendet, verarbeitet und ein UI-diff zurückgesendet. Folglich ist die Benutzeroberflächen Latenz die Summe der Netzwerk Latenz und der Server Latenz bei der Verarbeitung der Aktion.

Für eine Branchen-APP, die auf ein privates Unternehmensnetzwerk beschränkt ist, sind die Auswirkungen auf die Benutzer Wahrnehmung von Latenzzeit aufgrund der Netzwerk Latenz in der Regel nicht wahrnehmbar. Für eine APP, die über das Internet bereitgestellt wird, kann die Latenz für Benutzer bemerkbar werden, insbesondere, wenn die Benutzer weitgehend geografisch verteilt sind.

Die Speicherauslastung kann auch zur APP-Latenz beitragen. Eine erhöhte Arbeitsspeicher Auslastung führt zu häufigen Garbage Collection-oder Paging-Speicher auf den Datenträger, die beide die Leistung der APP beeinträchtigen und somit die Benutzeroberflächen Latenz erhöhen. Weitere Informationen finden Sie unter <xref:security/blazor/server>.

Blazor Server-apps sollten optimiert werden, um die Benutzeroberflächen Latenz zu minimieren, indem Netzwerk Latenz und Speicherauslastung reduziert werden Einen Ansatz zum Messen der Netzwerk Latenz finden Sie unter <xref:host-and-deploy/blazor/server#measure-network-latency>. Weitere Informationen zu SignalR und Blazorfinden Sie unter:

* <xref:host-and-deploy/blazor/server>
* <xref:security/blazor/server>

### <a name="connection-to-the-server"></a>Verbindung mit dem Server

Blazor Server-apps benötigen eine aktive SignalR Verbindung mit dem Server. Wenn die Verbindung unterbrochen wird, versucht die APP, erneut eine Verbindung mit dem Server herzustellen. Solange sich der Status des Clients weiterhin im Arbeitsspeicher befindet, wird die Client Sitzung fortgesetzt, ohne den Zustand zu verlieren.

Eine Blazor Server-App als Antwort auf die erste Client Anforderung, die den UI-Status auf dem Server festlegt. Wenn der Client versucht, eine SignalR Verbindung zu erstellen, muss der Client erneut eine Verbindung mit dem gleichen Server herstellen. Blazor Server-apps, die mehr als einen Back-End-Server verwenden, sollten persistente *Sitzungen* für SignalR Verbindungen implementieren.

Sie sollten [Azure SignalR Service](/azure/azure-signalr) für Blazor Server-Apps verwenden. Der Dienst ermöglicht das zentrale Hochskalieren einer Blazor Server-App auf eine große Anzahl gleichzeitiger SignalR-Verbindungen. Persistente Sitzungen werden für den Azure SignalR-Dienst aktiviert, indem die `ServerStickyMode` Option oder der Konfigurations Wert für den Dienst auf `Required`festgelegt wird. Weitere Informationen finden Sie unter <xref:host-and-deploy/blazor/server#signalr-configuration>.

Wenn Sie IIS verwenden, werden persistente Sitzungen mit dem Routing von Anwendungsanforderungen aktiviert. Weitere Informationen finden Sie unter [http-Lastenausgleich mithilfe des Routing von Anwendungsanforderungen](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).

## <a name="additional-resources"></a>Weitere Ressourcen

* <xref:blazor/get-started>
* <xref:signalr/introduction>
* <xref:blazor/hosting-model-configuration>
* <xref:tutorials/signalr-blazor-webassembly>
