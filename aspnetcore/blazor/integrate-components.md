---
title: Integrieren von ASP.net Core Razor-Komponenten in Razor Pages und MVC-apps
author: guardrex
description: Erfahren Sie mehr über Daten Bindungs Szenarien für Komponenten und DOM-Elemente in Blazor-apps.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: de1a37ffd9456c956e3d84fcc69431ecb794513c
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/19/2020
ms.locfileid: "77453211"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a>Integrieren von ASP.net Core Razor-Komponenten in Razor Pages und MVC-apps

Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)

Razor-Komponenten können in Razor Pages-und MVC-Apps integriert werden. Wenn die Seite oder Ansicht gerendert wird, können Komponenten gleichzeitig vorab vorgerendert werden.

## <a name="prepare-the-app-to-use-components-in-pages-and-views"></a>Vorbereiten der APP für die Verwendung von Komponenten in Seiten und Ansichten

Eine vorhandene Razor Pages-oder MVC-App kann Razor-Komponenten in Seiten und Ansichten integrieren:

1. In der Layoutdatei der APP ( *_Layout. cshtml*):

   * Fügen Sie dem `<head>`-Element das folgende `<base>`-Tag hinzu:

     ```html
     <base href="~/" />
     ```

     Beim `href` Wert (dem *App-Basispfad*) im vorhergehenden Beispiel wird davon ausgegangen, dass sich die APP im Stamm-URL-Pfad (`/`) befindet. Wenn es sich bei der App um eine untergeordnete Anwendung handelt, befolgen Sie die Anweisungen im Abschnitt *App-Basispfad* des <xref:host-and-deploy/blazor/index#app-base-path> Artikels.

     Die Datei " *_Layout. cshtml* " befindet sich im Ordner " *pages/Shared* " in einer Razor Pages-APP oder in *Sichten/freigegebenen* Ordnern in einer MVC-app.

   * Fügen Sie unmittelbar vor dem schließenden `</body>` Tag ein `<script>`-Tag für das Skript " *blazor. Server. js* " hinzu:

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     Das-Framework fügt der APP das Skript " *blazor. Server. js* " hinzu. Es ist nicht erforderlich, das Skript manuell der APP hinzuzufügen.

1. Fügen Sie dem Stamm Ordner des Projekts eine *_Imports. Razor* -Datei mit folgendem Inhalt hinzu (ändern Sie den letzten Namespace, `MyAppNamespace`, in den Namespace der APP):

   ```razor
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

1. Integrieren von Komponenten in beliebige Seiten oder Ansichten. Weitere Informationen finden Sie im Abschnitt [Rendering Components from a page or View](#render-components-from-a-page-or-view) .

## <a name="use-routable-components-in-a-razor-pages-app"></a>Verwenden von Routing fähigen Komponenten in einer Razor Pages-App

*Dieser Abschnitt bezieht sich auf das Hinzufügen von Komponenten, die direkt von Benutzer Anforderungen aus Routing fähig sind.*

So unterstützen Sie Routing fähige Razor-Komponenten in Razor Pages-apps:

1. Befolgen Sie die Anweisungen im Abschnitt [Vorbereiten der APP für die Verwendung von Komponenten in Seiten und Ansichten](#prepare-the-app-to-use-components-in-pages-and-views) .

1. Fügen Sie dem Projektstamm eine *app. Razor* -Datei mit folgendem Inhalt hinzu:

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

   Weitere Informationen zu Namespaces finden Sie im Abschnitt [Komponentennamespaces](#component-namespaces) .

## <a name="use-routable-components-in-an-mvc-app"></a>Verwenden von Routing fähigen Komponenten in einer MVC-App

*Dieser Abschnitt bezieht sich auf das Hinzufügen von Komponenten, die direkt von Benutzer Anforderungen aus Routing fähig sind.*

So unterstützen Sie Routing fähige Razor-Komponenten in MVC-apps:

1. Befolgen Sie die Anweisungen im Abschnitt [Vorbereiten der APP für die Verwendung von Komponenten in Seiten und Ansichten](#prepare-the-app-to-use-components-in-pages-and-views) .

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

   Weitere Informationen zu Namespaces finden Sie im Abschnitt [Komponentennamespaces](#component-namespaces) .

## <a name="component-namespaces"></a>Komponentennamespaces

Wenn Sie einen benutzerdefinierten Ordner für die Komponenten der App verwenden, fügen Sie den Namespace, der den Ordner darstellt, entweder der Seite/Ansicht oder der Datei " *_ViewImports. cshtml* " hinzu. Siehe folgendes Beispiel:

* Ändern Sie `MyAppNamespace` in den Namespace der app.
* Wenn ein Ordner mit dem Namen *Components* nicht zum Speichern der Komponenten verwendet wird, ändern Sie `Components` in den Ordner, in dem sich die Komponenten befinden.

```cshtml
@using MyAppNamespace.Components
```

Die Datei " *_ViewImports. cshtml* " befindet sich im Ordner " *pages* " einer Razor Pages-APP oder im Ordner " *views* " einer MVC-app.

Weitere Informationen finden Sie unter <xref:blazor/components#import-components>.

## <a name="render-components-from-a-page-or-view"></a>Rendering von Komponenten aus einer Seite oder Ansicht

*Dieser Abschnitt bezieht sich auf das Hinzufügen von Komponenten zu Seiten oder Sichten, bei denen die Komponenten nicht direkt von Benutzer Anforderungen aus Routing fähig sind.*

Verwenden Sie das `Component`-taghilfsprogramm, um eine Komponente aus einer Seite oder Sicht zu Rendering:

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

Der Parametertyp muss JSON-serialisierbar sein. Dies bedeutet in der Regel, dass der Typ einen Standardkonstruktor und festleg bare Eigenschaften aufweisen muss. Beispielsweise können Sie einen Wert für `IncrementAmount` angeben, da der Typ der `IncrementAmount` ein `int`ist, bei dem es sich um einen primitiven Typ handelt, der vom JSON-Serialisierungsprogramm unterstützt wird.

`RenderMode` konfiguriert, ob die Komponente Folgendes hat:

* Wird in die Seite vorab übernommen.
* Wird als statischer HTML-Code auf der Seite gerendert, oder wenn er die erforderlichen Informationen zum Bootstrapping einer blazor-APP vom Benutzer-Agent enthält.

| `RenderMode`        | Beschreibung |
| ------------------- | ----------- |
| `ServerPrerendered` | Rendert die Komponente in statischem HTML-Format und enthält einen Marker für eine Blazor Server-app. Wenn der Benutzer-Agent gestartet wird, wird dieser Marker zum Bootstrapping einer Blazor-App verwendet. |
| `Server`            | Rendert einen Marker für eine Blazor Server-app. Die Ausgabe der Komponente ist nicht enthalten. Wenn der Benutzer-Agent gestartet wird, wird dieser Marker zum Bootstrapping einer Blazor-App verwendet. |
| `Static`            | Rendert die Komponente in statischem HTML-Format. |

Während Seiten und Ansichten Komponenten verwenden können, ist das Gegenteil nicht der Fall. Komponenten können keine Ansichts-und Seiten spezifischen Szenarien verwenden, wie z. b. partielle Sichten und Abschnitte. Um die Logik der Teilansicht in einer Komponente zu verwenden, müssen Sie die partielle Sicht Logik in eine Komponente einbeziehen.

Das Rendering von Serverkomponenten von einer statischen HTML-Seite wird nicht unterstützt.

Weitere Informationen zum Rendern von Komponenten, zum Komponenten Status und zum `Component`-taghilfsprogramm finden Sie in den folgenden Artikeln:

* <xref:blazor/hosting-models>
* <xref:blazor/hosting-model-configuration>
