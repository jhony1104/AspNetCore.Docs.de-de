---
title: Hostingmodellkonfiguration für ASP.NET Core Blazor
author: guardrex
description: Hier erhalten Sie Informationen zur Blazor-Hostingmodellkonfiguration, einschließlich der Integration von Razor-Komponenten in Razor Pages- und MVC-Apps.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: bd44643877e45c5b48b0972bcc2f637fbc5d98f2
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78646789"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>Hostingmodellkonfiguration für ASP.NET Core Blazor

Von [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Dieser Artikel behandelt die Hostingmodellkonfiguration.

<!-- For future use:

## Blazor WebAssembly

-->

## <a name="blazor-server"></a>Blazor Server

### <a name="reflect-the-connection-state-in-the-ui"></a>Reflektieren des Verbindungszustands auf der Benutzeroberfläche

Wenn der Client erkennt, dass keine Verbindung mehr besteht, wird dem Benutzer eine Standardbenutzeroberfläche angezeigt, während der Client versucht, eine neue Verbindung herzustellen. Wenn die Wiederherstellung der Verbindung fehlschlägt, wird dem Benutzer die Option angezeigt, es noch mal zu versuchen.

Wenn Sie die Benutzeroberfläche anpassen möchten, definieren Sie ein Element mit einer `id` von `components-reconnect-modal` im `<body>` der Razor-Seite *_Host.cshtml*.

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

In der folgenden Tabelle werden die CSS-Klassen beschrieben, die auf das `components-reconnect-modal`-Element angewendet werden.

| CSS-Klasse                       | Steht für&hellip; |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | Die Verbindung wurde getrennt. Der Client versucht, die Verbindung wiederherzustellen. Die modale Seite wird angezeigt. |
| `components-reconnect-hide`     | Auf dem Server wird eine aktive Verbindung wiederhergestellt. Die modale Seite wird ausgeblendet. |
| `components-reconnect-failed`   | Die Wiederherstellung der Verbindung ist wahrscheinlich aufgrund eines Netzwerkfehlers fehlgeschlagen. Rufen Sie `window.Blazor.reconnect()` auf, um einen neuen Verbindungsversuch zu unternehmen. |
| `components-reconnect-rejected` | Die Wiederherstellung der Verbindung wurde abgelehnt. Der Server wurde zwar erreicht, jedoch hat dieser die Verbindung verweigert. Der Status des Benutzers auf dem Server wurde verworfen. Rufen Sie `location.reload()` auf, um die App neu zu laden. Dieser Verbindungszustand kann aus folgenden Gründen auftreten:<ul><li>Aufgetretener Absturz auf der serverseitigen Verbindung.</li><li>Der Client war lange nicht verbunden, sodass der Server den Benutzerstatus verworfen hat. Komponenteninstanzen, mit denen der Benutzer interagiert, werden verworfen.</li><li>Der Server wird neu gestartet, oder der Workerprozess der App wird wiederverwendet.</li></ul> |

### <a name="render-mode"></a>Rendermodus

Blazor Server-Apps werden standardmäßig eingerichtet, um die Benutzeroberfläche auf dem Server schon vor der Einrichtung der Clientverbindung auf dem Server zu rendern. Sehen Sie sich die Einrichtung der Razor-Seite *_Host.cshtml* an:

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

`RenderMode` konfiguriert folgende Einstellungen für die Komponente:

* Ob die Komponente zuvor für die Seite gerendert wird
* Ob die Komponente als statische HTML auf der Seite gerendert wird oder ob sie die nötigen Informationen für das Bootstrapping einer Blazor-App über den Benutzer-Agent enthält

| `RenderMode`        | Beschreibung |
| ------------------- | ----------- |
| `ServerPrerendered` | Rendert die Komponente in statische HTML und fügt einen Marker für eine Blazor Server-App hinzu. Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer Blazor-App verwendet. |
| `Server`            | Rendert einen Marker für eine Blazor Server-App. Die Ausgabe der Komponente ist nicht enthalten. Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer Blazor-App verwendet. |
| `Static`            | Rendert die Komponente in statischen HTML-Code. |

Das Rendern von Serverkomponenten über eine statische HTML-Seite wird nicht unterstützt.

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a>Rendern von zustandsbehafteten interaktiven Komponenten von Razor-Seiten und -Ansichten

Zustandsbehaftete interaktive Komponenten können einer Razor-Seite oder -Ansicht hinzugefügt werden.

Wenn die Seite oder Ansicht gerendert wird:

* Die Komponente wird mit der Seite oder Ansicht vorab gerendert.
* Der anfängliche Komponentenzustand, der zum Rendern vorab genutzt wurde, geht verloren.
* Der neue Komponentenzustand wird erstellt, wenn die SignalR-Verbindung hergestellt wird.

Die folgende Razor-Seite rendert eine `Counter`-Komponente:

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a>Rendern von nicht interaktiven Komponenten von Razor-Seiten und Ansichten

Auf der folgenden Razor-Seite wird die `Counter`-Komponente statisch mit einem Anfangswert gerendert, der mit einem Formular angegeben wird:

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

Da `MyComponent` statisch gerendert wird, darf die Komponente nicht interaktiv sein.

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a>Konfigurieren des SignalR-Clients für Blazor Server-Apps

In einigen Fällen müssen Sie den von den SignalR Server-Apps verwendeten Blazor-Client konfigurieren. Beispielsweise können Sie die Protokollierung auf dem SignalR-Client konfigurieren, um ein Verbindungsproblem zu diagnostizieren.

So konfigurieren Sie den SignalR-Client in der Datei *Pages/_Host.cshtml*:

* Fügen Sie ein `autostart="false"`-Attribut zum `<script>`-Tag für das `blazor.server.js`-Skript hinzu.
* Rufen Sie `Blazor.start` auf, und übergeben ein Konfigurationsobjekt, das den SignalR-Builder angibt.

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
