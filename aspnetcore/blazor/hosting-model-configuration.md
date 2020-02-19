---
title: Konfiguration des ASP.net Core Blazor Hostingmodells
author: guardrex
description: Erfahren Sie mehr über die Konfiguration von Blazor Hostingmodellen, einschließlich der Integration von Razor-Komponenten in Razor Pages und MVC-apps.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: bd44643877e45c5b48b0972bcc2f637fbc5d98f2
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/19/2020
ms.locfileid: "77447034"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>Konfiguration des ASP.net Core blazor-Hostingmodells

Von [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

In diesem Artikel wird die Konfiguration des Hostingmodells behandelt.

<!-- For future use:

## Blazor WebAssembly

-->

## <a name="blazor-server"></a>Blazor Server

### <a name="reflect-the-connection-state-in-the-ui"></a>Reflektieren des Verbindungs Zustands in der Benutzeroberfläche

Wenn der Client erkennt, dass die Verbindung unterbrochen wurde, wird dem Benutzer eine Standardbenutzer Oberfläche angezeigt, während der Client versucht, die Verbindung wiederherzustellen. Wenn bei der erneuten Verbindungs Herstellung ein Fehler auftritt, wird dem Benutzer die Option zum erneuten versuchen bereitgestellt.

Zum Anpassen der Benutzeroberfläche definieren Sie ein Element mit einem `id` `components-reconnect-modal` im `<body>` der Razor Page *_Host. cshtml* :

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

In der folgenden Tabelle werden die CSS-Klassen beschrieben, die auf das `components-reconnect-modal` Element angewendet werden.

| CSS-Klasse                       | Gibt&hellip; |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | Eine verlorene Verbindung. Der Client versucht, die Verbindung wiederherzustellen. Zeigen Sie die modale an. |
| `components-reconnect-hide`     | Eine aktive Verbindung mit dem Server wird wieder hergestellt. Blenden Sie den modalen aus. |
| `components-reconnect-failed`   | Fehler beim erneuten Herstellen der Verbindung, wahrscheinlich aufgrund eines Netzwerk Fehlers. Um erneut eine Verbindung herzustellen, wenden Sie `window.Blazor.reconnect()`an. |
| `components-reconnect-rejected` | Erneute Verbindung abgelehnt. Der Server wurde erreicht, hat jedoch die Verbindung abgelehnt, und der Benutzer Zustand auf dem Server geht verloren. Um die APP erneut zu laden, wenden Sie `location.reload()`an. Der Verbindungsstatus kann folgende Ergebnisse haben:<ul><li>Ein Absturz in der serverseitigen Verbindung tritt auf.</li><li>Der Client ist so lange getrennt, dass der Server den Zustand des Benutzers löscht. Instanzen der Komponenten, mit denen der Benutzer interagiert, werden verworfen.</li><li>Der Server wird neu gestartet, oder der Arbeitsprozess der APP wird wieder verwendet.</li></ul> |

### <a name="render-mode"></a>Rendermodus

Blazor-Server-apps werden standardmäßig so eingerichtet, dass Sie die Benutzeroberfläche auf dem Server vorab ausführen, bevor die Client Verbindung mit dem Server hergestellt wird. Dies wird auf der Razor Page *_Host. cshtml* eingerichtet:

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
* Wird als statischer HTML-Code auf der Seite gerendert, oder wenn er die erforderlichen Informationen zum Bootstrapping einer blazor-APP vom Benutzer-Agent enthält.

| `RenderMode`        | Beschreibung |
| ------------------- | ----------- |
| `ServerPrerendered` | Rendert die Komponente in statischem HTML-Format und enthält einen Marker für eine Blazor Server-app. Wenn der Benutzer-Agent gestartet wird, wird dieser Marker zum Bootstrapping einer Blazor-App verwendet. |
| `Server`            | Rendert einen Marker für eine Blazor Server-app. Die Ausgabe der Komponente ist nicht enthalten. Wenn der Benutzer-Agent gestartet wird, wird dieser Marker zum Bootstrapping einer Blazor-App verwendet. |
| `Static`            | Rendert die Komponente in statischem HTML-Format. |

Das Rendering von Serverkomponenten von einer statischen HTML-Seite wird nicht unterstützt.

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
