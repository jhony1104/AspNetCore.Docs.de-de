---
title: Einführung in Razor Components
author: guardrex
description: 'Lernen Sie ASP.NET Core Razor Components kennen, eine Möglichkeit, interaktive clientseitige Webbenutzeroberflächen mit .NET in einer ASP.NET Core-App zu erstellen.'
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
uid: razor-components/index
---
# <a name="introduction-to-razor-components"></a>Einführung in Razor Components

Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)

*Razor Components* bieten eine Möglichkeit zum Erstellen einer interaktiven clientseitigen Webbenutzeroberfläche mit .NET:

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

Weitere Informationen finden Sie unter <xref:razor-components/hosting-models#server-side-hosting-model>.

*Blazor* ist das experimentelle clientseitige Hostingmodell von Razor Components. Blazor wird unter .NET unter Verwendung offener Webstandards ohne Plug-Ins und Codetranspilierung im Browser ausgeführt. Weitere Informationen finden Sie unter <xref:spa/blazor/index> und <xref:razor-components/hosting-models#client-side-hosting-model>.

## <a name="components"></a>Komponenten

Eine *Razor Component* ist Bestandteil der Benutzeroberfläche (z.B. Seiten, Dialogfelder oder Formulare für die Dateneingabe). Komponenten behandeln Benutzerereignisse und definieren flexible Renderinglogik für die Benutzeroberfläche. Komponenten können geschachtelt sein und wiederverwendet werden.

Komponenten sind in .NET-Assemblys integrierte .NET-Klassen, die gemeinsam genutzt und als NuGet-Pakete verteilt werden können. Die Klasse wird in der Regel in Form einer Razor-Markupseite mit der Dateierweiterung *.razor* geschrieben.

[Razor](xref:mvc/views/razor) ist eine Syntax, mit der HTML-Markup mit C#-Code kombiniert werden kann. Razor wurde entwickelt, um die Produktivität von Entwicklern zu optimieren, da diese damit und unter Verwendung der [IntelliSense](/visualstudio/ide/using-intellisense)-Unterstützung innerhalb einer Datei zwischen Markup und C# wechseln können. Razor Pages und MVC-Ansichten verwenden ebenfalls Razor. Im Gegensatz zu Razor Pages und MVC-Ansichten, die auf einem Anforderung/Antwort-Modell aufbauen, werden Komponenten speziell für die Verarbeitung der Benutzeroberflächengestaltung verwendet. Razor Components können insbesondere für die clientseitige Benutzeroberflächenlogik und -gestaltung verwendet werden.

Das folgende Markup stellt ein Beispiel einer benutzerdefinierten Dialogfeldkomponente in einer Razor-Datei (*DialogComponent.razor*) dar:

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

Die Komponenten werden in einer In-Memory-Darstellung des Browser-DOMs gerendert, die als *Renderbaum* bezeichnet wird und anschließend verwendet werden kann, um die Benutzeroberfläche auf flexible und effiziente Weise zu aktualisieren.

## <a name="javascript-interop"></a>JavaScript-Interoperabilität

Für Apps, die JavaScript-Bibliotheken und Browser-APIs von Drittanbietern erfordern, sind die Komponenten für die Interoperabilität mit JavaScript konzipiert. Komponenten können jede Bibliothek oder API verwenden, die auch von JavaScript verwendet werden kann. C#-Code kann JavaScript-Code abfragen, und umgekehrt. Weitere Informationen finden Sie unter [JavaScript interop (JavaScript-Interoperabilität)](xref:razor-components/javascript-interop).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:spa/blazor/index>
* [WebAssembly](http://webassembly.org/)
* [Leitfaden für C#](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [HTML](https://www.w3.org/html/)
