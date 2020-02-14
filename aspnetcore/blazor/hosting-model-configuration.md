---
title: Konfiguration des ASP.net Core Blazor Hostingmodells
author: guardrex
description: Erfahren Sie mehr über die Konfiguration von Blazor Hostingmodellen, einschließlich der Integration von Razor-Komponenten in Razor Pages und MVC-apps.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: 91dd1aa32bb5165845eb4794377a50ccbd01adc3
ms.sourcegitcommit: d2ba66023884f0dca115ff010bd98d5ed6459283
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2020
ms.locfileid: "77214940"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>Konfiguration des ASP.net Core blazor-Hostingmodells

Von [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

In diesem Artikel wird die Konfiguration des Hostingmodells behandelt.

<!-- For future use:

## Blazor WebAssembly

-->

## <a name="blazor-server"></a>Blazor Server

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

   * Fügen Sie direkt vor dem schließenden `</body>`-Tag ein `<script>`-Tag für das Skript " *blazor. Server. js* " hinzu:

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

1. Registrieren Sie in `Startup.ConfigureServices`den blazor-Server Dienst:

   ```csharp
   services.AddServerSideBlazor();
   ```

1. Fügen Sie in `Startup.Configure`den blazor-Hub-Endpunkt `app.UseEndpoints`hinzu:

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

| `RenderMode`        | BESCHREIBUNG |
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
