---
title: Einführung in Razor Components
author: guardrex
description: Informationen zu Blazor, einem .NET-Webframework, das C#/Razor und HTML verwendet und im Browser unter Verwendung von WebAssembly ausgeführt wird.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/29/2019
uid: razor-components/index
ms.openlocfilehash: 0f7a4b2ec404b6ceec2e643fea9e0635de6ad716
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667918"
---
# <a name="introduction-to-razor-components"></a>Einführung in Razor Components

Von [Steve Sanderson](http://blog.stevensanderson.com), [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

*ASP.NET Core Razor Components* wird in ASP.NET Core auf Serverseite ausgeführt, wohingegen *Blazor* (Razor Components wird auf Clientseite ausgeführt) ein experimentelles .NET-Webframework ist, das C#/Razor und HTML verwendet und im Browser unter Verwendung von WebAssembly ausgeführt wird. Blazor umfasst sämtliche Vorteile eines clientseitigen Webframeworks für Benutzeroberflächen und verwendet .NET auf dem Client.

In den letzten Jahren hat sich die Webentwicklung zwar in verschiedenen Bereichen deutlich verbessert, aber das Erstellen moderner Web-Apps birgt immer noch viele Herausforderungen. Die Verwendung von .NET im Browser bietet viele Vorteile, die die Webentwicklung vereinfacht und produktiveres Arbeiten ermöglichen kann:

* **Stabilität und Konsistenz:** .NET umfasst standardisierte Programmierframeworks für mehrere Plattformen, die einfach zu verwenden und stabil sind und zahlreiche Features beinhalten.
* **Moderne, innovative Sprachen:** .NET-Sprachen werden mithilfe von innovativen neuen Sprachfeatures stetig verbessert.
* **Branchenführende Tools:** Die Visual Studio-Produktfamilie bietet plattformübergreifend nützliche Entwicklungsfunktionen für Windows, Linux und macOS für .NET.
* **Geschwindigkeit und Skalierbarkeit:** .NET stand schon immer für Leistung, Zuverlässigkeit und Sicherheit bei der App-Entwicklung. Die Verwendung von .NET als Full-Stack-Lösung erleichtert das Erstellen von schnellen, zuverlässigen und sicheren Apps.
* **Full-Stack-Entwicklung, bei der bereits vorhandene Kenntnisse genutzt werden:** C#-/Razor-Entwickler verwenden ihre Kenntnisse, um clientseitigen Code zu schreiben und server- und clientseitige Logik für mehrere Apps freizugeben.
* **Webbrowserunterstützung:** Razor Components rendert die Benutzeroberfläche als normales Markup und JavaScript. Blazor wird unter .NET unter Verwendung offener Webstandards ohne Plug-Ins und Codetranspilierung im Browser ausgeführt. Dieses Framework funktioniert in allen modernen Webbrowsern, einschließlich mobiler Browser.

## <a name="hosting-models"></a>Hostingmodelle

### <a name="server-side-hosting-model"></a>Serverseitiges Hostingmodell

Da Razor Components die Renderinglogik einer Komponente entkoppelt, die beschreibt, wie Benutzeroberflächenupdates angewendet werden, gibt es verschiedene Möglichkeiten, Razor Components zu hosten. Mit ASP.NET Core Razor Components wird in .NET Core 3.0 Unterstützung zum Hosten von Razor Components in einer ASP.NET Core-App auf dem Server hinzugefügt, in der alle Benutzeroberflächenupdates über eine SignalR-Verbindung verarbeitet werden. Die Runtime handhabt das Senden von Benutzeroberflächenereignissen vom Browser an den Server und wendet Benutzeroberflächenupdates an, die nach dem Ausführen der Komponenten vom Server zurück an den Browser gesendet werden. Dieselbe Verbindung wird außerdem verwendet, um Interoperabilitätsaufrufe mit JavaScript zu verarbeiten.

![Razor Components führt .NET-Code auf dem Server aus und interagiert über eine SignalR-Verbindung mit dem Dokumentobjektmodell.](index/_static/aspnet-core-razor-components.png)

Weitere Informationen finden Sie unter <xref:razor-components/hosting-models#server-side-hosting-model>.

### <a name="client-side-hosting-model"></a>Clientseitiges Hostingmodell

Das Ausführen von .NET-Code in einem Webbrowser wird durch eine relativ neue Technologie namens [WebAssembly](http://webassembly.org) (Kurzform: *wasm*) ermöglicht. WebAssembly ist ein offener Webstandard, der in Webbrowsern ohne Plug-Ins unterstützt wird. Es handelt sich um ein Bytecodeformat, das für schnelles Downloaden und die maximale Ausführungsgeschwindigkeit optimiert ist.

WebAssembly-Code kann über JavaScript-Interoperabilität auf alle Funktionen des Browsers zugreifen. Gleichzeitig wird der Code in derselben vertrauenswürdigen Sandbox wie JavaScript ausgeführt, um böswillige Aktionen auf dem Clientcomputer zu verhindern.

![Blazor führt .NET-Code unter Verwendung von WebAssembly im Browser aus.](index/_static/blazor.png)

Folgendes geschieht, wenn eine Blazor-App in einem Browser erstellt und ausgeführt wird:

1. C#-Codedateien und Razor-Dateien werden in .NET-Assemblys kompiliert.
1. Die Assemblys und die .NET-Runtime werden im Browser heruntergeladen.
1. Blazor verwendet JavaScript, um einen Bootstrap für die .NET-Runtime auszuführen, und konfiguriert diese für das Laden von erforderlichen Assemblyverweisen. Die Bearbeitung von Dokumentobjektmodellen (DOM) und API-Aufrufe im Browser werden von der Blazor-Runtime über die JavaScript-Interoperabilität verarbeitet.

Wenn ältere Browser unterstützt werden sollen, die WebAssembly nicht unterstützen, können Sie das [serverseitige Hostingmodell](#server-side-hosting-model) von ASP.NET Core Razor Components verwenden.

Weitere Informationen finden Sie unter <xref:razor-components/hosting-models#client-side-hosting-model>.

## <a name="components"></a>Komponenten

Apps werden mithilfe von *Komponenten* erstellt. Komponenten sind Bestandteile der Benutzeroberfläche wie Seiten, Dialogfelder oder Formulare für die Dateneingabe. Sie können geschachtelt sein sowie wiederverwendet und für mehrere Projekte freigegeben werden.

Eine *Komponente* ist eine .NET-Klasse. Die Klasse kann entweder direkt als C#-Klasse (*\*.cs*) geschrieben werden, oder es wird eine Razor-Markupseite (*\*.cshtml*) verwendet (häufigere Methode).

[Razor](/aspnet/core/mvc/views/razor) ist eine Syntax, mit der HTML-Markup mit C#-Code kombiniert werden kann. Razor wurde entwickelt, um die Produktivität von Entwicklern zu optimieren, da diese damit und unter Verwendung der [IntelliSense](/visualstudio/ide/using-intellisense)-Unterstützung innerhalb einer Datei zwischen Markup und C# wechseln können. Das folgende Markup stellt ein Beispiel einer grundlegenden benutzerdefinierten Dialogfeldkomponente in einer Razor-Datei (*DialogComponent.cshtml*) dar:

```cshtml
<div>
    <h2>@Title</h2>
    @BodyContent
    <button onclick=@OnOK>OK</button>
</div>

@functions {
    public string Title { get; set; }
    public RenderFragment BodyContent { get; set; }
    public Action OnOK { get; set; }
}
```

Wenn diese Komponente an einer anderen Stelle in der App verwendet wird, beschleunigt IntelliSense die Entwicklung mithilfe von Syntax- und Parametervervollständigung.

Komponenten können die folgenden Eigenschaften aufweisen:

* Sie können geschachtelt sein.
* Sie können mit Razor- (*\*.cshtml*) oder C#-Code (*\*.cs*) erstellt worden sein.
* Sie können für mehrere Klassenbibliotheken freigegeben sein.
* Es können Komponententests durchgeführt worden sein, für die kein DOM für den Browser verlangt wurde.

## <a name="infrastructure"></a>Infrastruktur

Razor Components bietet die Kernfunktionen, die für die meisten Apps erforderlich sind, z. B.:

* Layouts
* Routing
* Dependency Injection

Diese Features sind alle optional. Wenn eins dieser Features nicht in einer App verwendet wird, wird die Implementierung bei der Veröffentlichung über den [Linker für Zwischensprachen (Intermediate Language, IL)](xref:host-and-deploy/razor-components/configure-linker) aus der App entfernt.

## <a name="code-sharing-and-net-standard"></a>Codefreigabe und .NET Standard

Apps können auf bereits vorhandene [.NET Standard](/dotnet/standard/net-standard)-Bibliotheken verweisen und diese verwenden. .NET Standard ist eine formale Spezifikation von .NET-APIs, die häufig für .NET-Implementierungen verwendet werden. Es wird .NET Standard 2.0 oder höher unterstützt. APIs, die nicht in einem Webbrowser angewendet werden können (z. B. zum Zugreifen auf ein Dateisystem, zum Öffnen eines Sockets, für das Threading und für andere Features) lösen die Ausnahme [PlatformNotSupportedException](/dotnet/api/system.platformnotsupportedexception) aus. .NET Standard-Klassenbibliotheken können für den gesamten Servercode und für mehrere browserbasierte Apps freigegeben werden.

## <a name="javascript-interop"></a>JavaScript-Interoperabilität

Für Apps, die JavaScript-Bibliotheken und Browser-APIs von Drittanbietern erfordern, ist WebAssembly für die Interoperabilität mit JavaScript konzipiert. Razor Components kann jede Bibliothek oder API verwenden, auf die auch JavaScript zurückgreifen kann. C#-Code kann JavaScript-Code abfragen, und umgekehrt. Weitere Informationen finden Sie unter [JavaScript interop (JavaScript-Interoperabilität)](xref:razor-components/javascript-interop).

## <a name="optimization"></a>Optimierung

Für clientseitige Apps ist die Nutzlastgröße sehr wichtig. Blazor optimiert die Nutzlastgröße, um die Downloadzeiten zu verringern. Beispielsweise werden nicht verwendete Bestandteile von .NET-Assemblys beim Buildprozess entfernt, HTTP-Antworten werden komprimiert und die .NET-Runtime und -Assemblys werden im Browser zwischengespeichert.

Razor Component bietet mithilfe der Verwaltung von .NET-Assemblys, der App-Assembly und der serverseitigen Runtime sogar eine noch kleinere Nutzlast als Blazor. Razor Components-Apps stellen den Clients nur Markup, Skripts und Stylesheets zur Verfügung.

## <a name="deployment"></a>Bereitstellung

Verwenden Sie Blazor, um eine klare eigenständige clientseitige App oder eine Full-Stack-App für ASP.NET Core zu erstellen, die sowohl server- als auch clientseitige Apps enthält:

* In einer **eigenständigen clientseitigen App** wird die Blazor-App in einen *dist*-Ordner kompiliert, der nur statische Dateien enthält. Die Dateien können in Azure App Service, auf GitHub Pages, über Internetinformationsdienste (als statische Dateiserver konfiguriert) sowie über viele weitere Server und Dienste gehostet werden. .NET muss bei der Produktion nicht auf dem Server vorhanden sein.
* In einer **Full-Stack-App für ASP.NET Core** kann Code zwischen server- und clientseitigen Apps freigegeben werden. Die ASP.NET Core Razor Components-App, die daraus entsteht und der clientseitigen Benutzeroberfläche sowie anderen serverseitigen API-Endpunkten zur Verfügung steht, kann für jede Cloud oder jeden lokalen Host erstellt und bereitgestellt werden, der von ASP.NET Core unterstützt wird.

## <a name="suggest-a-feature-or-file-a-bug-report"></a>Schlagen Sie ein Feature vor, oder melden Sie einen Fehler

Wenn Sie Vorschläge machen oder einen Fehler melden möchten, [öffnen Sie ein Issue](https://github.com/aspnet/AspNetCore/issues/new). Hilfe und Antworten von der Community zu allgemeinen Themen erhalten Sie, wenn Sie an der Konversation auf [Gitter](https://gitter.im/aspnet/Blazor) teilnehmen.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [WebAssembly](http://webassembly.org/)
* [Leitfaden für C#](/dotnet/csharp/)
* [Razor](/aspnet/core/mvc/views/razor)
* [HTML](https://www.w3.org/html/)
