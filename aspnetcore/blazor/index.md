---
title: Einführung in Blazor in ASP.NET Core
author: guardrex
description: Lernen Sie ASP.NET Core Blazor kennen, eine Möglichkeit, interaktive clientseitige Webbenutzeroberflächen mit .NET in einer ASP.NET Core-App zu erstellen.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: seoapril2019
ms.date: 04/15/2019
uid: blazor/index
ms.openlocfilehash: a5b12a5c5c10a74ab192d0d67a2ba9a5617232c7
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614573"
---
# <a name="introduction-to-blazor"></a>Einführung in Blazor

Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)

## <a name="razor-components"></a>Razor Components

Das serverseitige Hostingmodell von Blazor, *Razor Components*, bietet eine Möglichkeit zum Erstellen einer interaktiven clientseitigen Webbenutzeroberfläche mit .NET:

* Erstellen von umfassenden interaktiven Benutzeroberflächen mit C# anstatt mit JavaScript.
* Gemeinsames Verwenden von serverseitiger und clientseitiger App-Logik, die ausnahmslos mit .NET geschrieben wurde.
* Rendern der Benutzeroberfläche als HTML und CSS für umfassende Browserunterstützung (einschließlich mobiler Browser).

Razor Components unterstützt Kernfunktionen, die für die meisten Apps erforderlich sind, beispielsweise:

* Parameter
* Ereignisbehandlung
* Datenbindung
* Routing
* Dependency Injection
* Layouts
* Vorlagen
* Kaskadierende Werte

Razor Components entkoppelt die Komponentenrenderinglogik von Aktualisierungen der Benutzeroberfläche. Mit ASP.NET Core Razor Components wird in .NET Core 3.0 Unterstützung zum Hosten von Razor Components in einer ASP.NET Core-App auf dem Server hinzugefügt. Aktualisierungen der Benutzeroberfläche werden über eine SignalR-Verbindung verarbeitet.

Die Runtime übernimmt die folgenden Aufgaben:

* Senden von Benutzeroberflächenereignissen aus dem Browser an den Server.
* Anwenden von vom Server gesendeten Benutzeroberflächenupdates auf den Browser nach dem Ausführen der Komponenten.

Die Verbindung, die von Razor Components für die Kommunikation mit dem Browser verwendet wird, wird auch für die Verarbeitung von JavaScript-Interopaufrufen verwendet.

![Razor Components führt .NET-Code auf dem Server aus und interagiert über eine SignalR-Verbindung mit dem Dokumentobjektmodell.](index/_static/aspnet-core-razor-components.png)

Weitere Informationen finden Sie unter <xref:blazor/hosting-models#server-side-hosting-model>.

## <a name="components"></a>Komponenten

Eine *Razor Component* ist Bestandteil der Benutzeroberfläche (z.B. Seiten, Dialogfelder oder Formulare für die Dateneingabe). Komponenten behandeln Benutzerereignisse und definieren flexible Renderinglogik für die Benutzeroberfläche. Komponenten können geschachtelt sein und wiederverwendet werden.

Komponenten sind in .NET-Assemblys integrierte .NET-Klassen, die gemeinsam genutzt und als NuGet-Pakete verteilt werden können. Die Klasse wird in der Regel in Form einer Razor-Markupseite mit der Dateierweiterung *.razor* (Razor Components) oder einer Razor-Markupseite mit der Dateierweiterung *.cshtml* (Blazor) geschrieben.

[Razor](xref:mvc/views/razor) ist eine Syntax, mit der HTML-Markup mit C#-Code kombiniert werden kann. Razor wurde entwickelt, um die Produktivität von Entwicklern zu optimieren, da diese damit und unter Verwendung der [IntelliSense](/visualstudio/ide/using-intellisense)-Unterstützung innerhalb einer Datei zwischen Markup und C# wechseln können. Razor Pages und MVC-Ansichten verwenden ebenfalls Razor. Im Gegensatz zu Razor Pages und MVC-Ansichten, die auf einem Anforderung/Antwort-Modell aufbauen, werden Komponenten speziell für die Verarbeitung der Benutzeroberflächengestaltung verwendet. Razor Components können insbesondere für die clientseitige Benutzeroberflächenlogik und -gestaltung verwendet werden.

Das folgende Markup stellt ein Beispiel einer benutzerdefinierten Dialogfeldkomponente dar:

```cshtml
<div>
    <h2>@Title</h2>
    @BodyContent
    <button onclick="@OnOK">OK</button>
</div>

@functions {
    public string Title { get; set; }
    public RenderFragment BodyContent { get; set; }
    public Action OnOK { get; set; }
}
```

Wenn diese Komponente an einer anderen Stelle in der App verwendet wird, beschleunigt IntelliSense die Entwicklung mithilfe von Syntax- und Parametervervollständigung.

Die Komponenten werden in einer In-Memory-Darstellung des Browser-DOMs gerendert, die als *Renderbaum* bezeichnet wird und anschließend verwendet werden kann, um die Benutzeroberfläche auf flexible und effiziente Weise zu aktualisieren.

## <a name="javascript-interop"></a>JavaScript-Interoperabilität

Für Apps, die JavaScript-Bibliotheken und Browser-APIs von Drittanbietern erfordern, sind die Komponenten für die Interoperabilität mit JavaScript konzipiert. Komponenten können jede Bibliothek oder API verwenden, die auch von JavaScript verwendet werden kann. C#-Code kann JavaScript-Code abfragen, und umgekehrt. Weitere Informationen finden Sie unter [JavaScript interop (JavaScript-Interoperabilität)](xref:blazor/javascript-interop).

## <a name="code-sharing-and-net-standard"></a>Codefreigabe und .NET Standard

Apps können auf bereits vorhandene [.NET Standard](/dotnet/standard/net-standard)-Bibliotheken verweisen und diese verwenden. .NET Standard ist eine formale Spezifikation von .NET-APIs, die häufig für .NET-Implementierungen verwendet werden. Blazor implementiert .NET Standard 2.0. APIs, die nicht in einem Webbrowser angewendet werden können (z.B. zum Zugreifen auf ein Dateisystem, zum Öffnen eines Sockets, für das Threading und für andere Features), lösen die Ausnahme <xref:System.PlatformNotSupportedException> aus. .NET-Standardklassenbibliotheken können auf verschiedenen .NET-Plattformen wie Blazor, .NET Framework, .NET Core, Xamarin, Mono und Unity freigegeben werden.

## <a name="blazor"></a>Blazor

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

Blazor ist ein Single-Page-App-Framework zum Erstellen von interaktiven clientseitigen Web-Apps mit .NET. Blazor verwendet offene Webstandards ohne Plug-Ins oder Codetranspilation. Dieses Framework funktioniert in allen modernen Webbrowsern, einschließlich mobiler Browser.

Die Verwendung von .NET im Browser für die clientseitige Webentwicklung bietet viele Vorteile:

* **C#-Sprache**: Schreiben Sie Code in C# anstatt in JavaScript.
* **.NET-Ökosystem**: Nutzen Sie das vorhandene Ökosystem von .NET-Bibliotheken.
* **Full-Stack-Entwicklung**: Nutzen Sie server- und clientseitige Logik gemeinsam.
* **Geschwindigkeit und Skalierbarkeit:** NET wurde für Leistung, Zuverlässigkeit und Sicherheit konzipiert.
* **Branchenführende Tools:** Bleiben Sie mit Visual Studio unter Windows, Linux und macOS produktiv.
* **Stabilität und Konsistenz**:  Setzen Sie auf eine Sammlung von Sprachen, Frameworks und Tools, die stabil, funktionsreich und einfach zu verwenden sind.

Das Ausführen von .NET-Code in einem Webbrowser wird durch [WebAssembly](http://webassembly.org) (Kurzform: *wasm*) ermöglicht. WebAssembly ist ein offener Webstandard, der in Webbrowsern ohne Plug-Ins unterstützt wird. Es handelt sich um ein Bytecodeformat, das für schnelles Downloaden und die maximale Ausführungsgeschwindigkeit optimiert ist.

WebAssembly-Code kann über JavaScript-Interoperabilität auf alle Funktionen des Browsers zugreifen. Gleichzeitig wird der über WebAssembly ausgeführte .NET-Code in derselben vertrauenswürdigen Sandbox wie JavaScript ausgeführt, um böswillige Aktionen auf dem Clientcomputer zu verhindern.

![Blazor führt .NET-Code unter Verwendung von WebAssembly im Browser aus.](index/_static/blazor.png)

Folgendes geschieht, wenn eine Blazor-App in einem Browser erstellt und ausgeführt wird:

* C#-Codedateien und Razor-Dateien werden in .NET-Assemblys kompiliert.
* Die Assemblys und die .NET-Runtime werden im Browser heruntergeladen.
* Blazor startet die .NET-Runtime und konfiguriert die Runtime zum Laden der Assemblys für die App. Die Bearbeitung von Dokumentobjektmodellen (DOM) und API-Aufrufen im Browser wird von der Blazor-Runtime über die JavaScript-Interop verarbeitet.

Blazor unterstützt Kernfunktionen, die für die meisten Apps erforderlich sind, beispielsweise:

* Parameter
* Ereignisbehandlung
* Datenbindung
* Routing
* Dependency Injection
* Layouts
* Vorlagen
* Kaskadierende Werte

Um die Größe der heruntergeladenen App zu verringern, wird nicht verwendeter Code aus der App entfernt, wenn sie vom [IL-Linker (Intermediate Language)](xref:host-and-deploy/blazor/configure-linker) veröffentlicht wird.

Die Blazor-Clientseite ist ein clientseitiges Hostingmodell. Da Blazor die Renderinglogik einer Komponente von der Art und Weise entkoppelt, wie Benutzeroberflächenupdates angewendet werden, besteht Flexibilität hinsichtlich des Hostings von Blazor. Verwenden Sie ASP.NET Core [Razor Components](#razor-components), um Razor Components auf dem Server in einer ASP.NET Core-App zu hosten, in der Benutzeroberflächenupdates über eine SignalR-Verbindung verarbeitet werden. Weitere Informationen finden Sie unter <xref:blazor/hosting-models#server-side-hosting-model>. 

Die Größe der Nutzlast ist ein wichtiger Leistungsfaktor für die Nutzbarkeit einer App. Blazor optimiert die Nutzlastgröße, um die Downloadzeiten zu verringern:

* Nicht verwendete Teile von .NET-Assemblys werden während des Buildvorgangs entfernt.
* HTTP-Antworten werden komprimiert.
* Die .NET-Runtime und die Assemblys werden im Browser zwischengespeichert.

[Razor Components](#razor-components) bietet mithilfe der Verwaltung von .NET-Assemblys, der App-Assembly und der serverseitigen Runtime eine kleinere Nutzlast als Blazor. Razor Components-Apps stellen den Clients nur Markupdateien und statische Ressourcen zur Verfügung.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [WebAssembly](http://webassembly.org/)
* [Leitfaden für C#](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [HTML](https://www.w3.org/html/)
