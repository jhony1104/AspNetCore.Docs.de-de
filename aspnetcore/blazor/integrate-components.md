---
title: Integrieren von ASP.NET Core Razor-Komponenten in Razor Pages- und MVC-Apps
author: guardrex
description: In diesem Artikel lernen Sie Datenbindungsszenarios für Komponenten und DOM-Elemente in Blazor-Apps kennen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: de1a37ffd9456c956e3d84fcc69431ecb794513c
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78649081"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a>Integrieren von ASP.NET Core Razor-Komponenten in Razor Pages- und MVC-Apps

Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)

Razor-Komponenten können in Razor Pages- und MVC-Apps integriert werden. Wenn die Seite oder Ansicht gerendert wird, können Komponenten gleichzeitig vorab gerendert werden.

## <a name="prepare-the-app-to-use-components-in-pages-and-views"></a>Vorbereiten der App zum Verwenden von Komponenten in Seiten und Ansichten

Eine vorhandene Razor Pages- oder MVC-App kann Razor-Komponenten in Seiten und Ansichten integrieren:

1. In der Layoutdatei der App ( *_Layout.cshtml*):

   * Fügen Sie das folgende `<base>`-Tag zum `<head>`-Element hinzu:

     ```html
     <base href="~/" />
     ```

     Der `href`-Wert (der *App-Basispfad*) im obigen Beispiel setzt voraus, dass die App sich im URL-Stammpfad (`/`) befindet. Wenn es sich bei der App um eine untergeordnete Anwendung handelt, befolgen Sie die Anweisungen im Abschnitt *App-Basispfad* des <xref:host-and-deploy/blazor/index#app-base-path>-Artikels.

     Die Datei *_Layout.cshtml* befindet sich bei Razor Pages-Apps im Ordner *Pages/Shared* oder bei MVC-Apps im Ordner *Views/Shared*.

   * Fügen Sie ein `<script>`-Tag für das Skript *blazor.server.js* direkt vor dem schließenden `</body>`-Tag ein:

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     Das Framework fügt das Skript *blazor.server.js* zur App hinzu. Das Skript muss nicht manuell zur App hinzugefügt werden.

1. Fügen Sie eine *_Imports.razor*-Datei zum Stammordner des Projekts mit dem folgenden Inhalt ein (ändern Sie den letzten Namespace `MyAppNamespace` in den Namespace der App):

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

1. Registrieren Sie den Blazor Server-Dienst in `Startup.ConfigureServices`:

   ```csharp
   services.AddServerSideBlazor();
   ```

1. Fügen Sie den Blazor Hub-Endpunkt in `Startup.Configure` zu `app.UseEndpoints` hinzu:

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. Integrieren Sie Komponenten in eine beliebige Seite oder Ansicht. Weitere Informationen finden Sie im Abschnitt [Rendern von Komponenten einer Seite oder Ansicht](#render-components-from-a-page-or-view).

## <a name="use-routable-components-in-a-razor-pages-app"></a>Verwenden routingfähiger Komponenten in einer Razor Pages-App

*In diesem Abschnitt wird das Hinzufügen von Komponenten behandelt, die über Benutzeranforderungen direkt routingfähig sind.*

So unterstützen Sie routingfähige Razor-Komponenten in Razor Pages-Apps:

1. Befolgen Sie die Anweisungen im Abschnitt [Vorbereiten der App zum Verwenden von Komponenten in Seiten und Ansichten](#prepare-the-app-to-use-components-in-pages-and-views).

1. Fügen Sie eine *App.razor*-Datei mit dem folgenden Inhalt zum Projektstamm hinzu:

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

1. Fügen Sie eine *_Host.cshtml*-Datei mit dem folgenden Inhalt zum Ordner *Pages* hinzu:

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   Komponenten verwenden die gemeinsam verwendete Datei *_Layout.cshtml* für ihr Layout.

1. Fügen Sie eine Route mit niedriger Priorität für die Seite *_Host.cshtml* zur Endpunktkonfiguration in `Startup.Configure` hinzu:

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. Fügen Sie routingfähige Komponenten zur App hinzu. Zum Beispiel:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   Weitere Informationen zu Namespaces finden Sie im Abschnitt [Komponentennamespaces](#component-namespaces).

## <a name="use-routable-components-in-an-mvc-app"></a>Verwenden routingfähiger Komponenten in einer MVC-App

*In diesem Abschnitt wird das Hinzufügen von Komponenten behandelt, die über Benutzeranforderungen direkt routingfähig sind.*

So unterstützen Sie routingfähige Razor-Komponenten in MVC-Apps:

1. Befolgen Sie die Anweisungen im Abschnitt [Vorbereiten der App zum Verwenden von Komponenten in Seiten und Ansichten](#prepare-the-app-to-use-components-in-pages-and-views).

1. Fügen Sie eine *App.razor*-Datei mit dem folgenden Inhalt zum Projektstamm hinzu:

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

1. Fügen Sie eine *_Host.cshtml*-Datei mit dem folgenden Inhalt zum Ordner *Views/Home* hinzu:

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   Komponenten verwenden die gemeinsam verwendete Datei *_Layout.cshtml* für ihr Layout.

1. Fügen Sie eine Aktion zum Home-Controller hinzu:

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. Fügen Sie eine Route mit niedriger Priorität für die Controlleraktion hinzu, die die Ansicht *_Host.cshtml* an die Endpunktkonfiguration in `Startup.Configure` zurückgibt:

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. Erstellen Sie einen *Pages*-Ordner, und fügen Sie routingfähige Komponenten zur App hinzu. Zum Beispiel:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   Weitere Informationen zu Namespaces finden Sie im Abschnitt [Komponentennamespaces](#component-namespaces).

## <a name="component-namespaces"></a>Komponentennamespaces

Wenn Sie einen benutzerdefinierten Ordner für die Komponenten der App verwenden, fügen Sie den Namespace zur Seite/Ansicht oder zur Datei *_ViewImports.cshtml* hinzu. Im folgenden Beispiel:

* Ändern Sie `MyAppNamespace` in den Namespace der App.
* Wenn die Komponenten nicht in einem Ordner namens *Components* enthalten sind, ändern Sie `Components` in den Namen des Ordners, in dem sich die Komponenten befinden.

```cshtml
@using MyAppNamespace.Components
```

Die Datei *_ViewImports.cshtml* befindet sich bei Razor Pages-Apps im Ordner *Pages* oder bei MVC-Apps im Ordner *Views*.

Weitere Informationen finden Sie unter <xref:blazor/components#import-components>.

## <a name="render-components-from-a-page-or-view"></a>Rendern von Komponenten einer Seite oder Ansicht

*In diesem Abschnitt wird das Hinzufügen von Komponenten zu Seiten oder Ansichten behandelt, wenn die Komponenten nicht direkt über Benutzeranforderungen routingfähig sind.*

Verwenden Sie das `Component`-Taghilfsprogramm zum Rendern einer Komponente einer Seite oder Ansicht:

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

Der Parametertyp muss mit JSON serialisierbar sein, in der Regel bedeutet das, dass der Typ über einen Standardkonstruktor und festlegbare Eigenschaften verfügen muss. Sie können beispielsweise einen Wert für `IncrementAmount` festlegen, weil `IncrementAmount` vom Typ `int` ist. Dabei handelt es sich um einen primitiven Typ, der vom JSON-Serialisierungsmodul unterstützt wird.

`RenderMode` konfiguriert folgende Einstellungen für die Komponente:

* Ob die Komponente zuvor für die Seite gerendert wird
* Ob die Komponente als statische HTML auf der Seite gerendert wird oder ob sie die nötigen Informationen für das Bootstrapping einer Blazor-App über den Benutzer-Agent enthält

| `RenderMode`        | Beschreibung |
| ------------------- | ----------- |
| `ServerPrerendered` | Rendert die Komponente in statische HTML und fügt einen Marker für eine Blazor Server-App hinzu. Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer Blazor-App verwendet. |
| `Server`            | Rendert einen Marker für eine Blazor Server-App. Die Ausgabe der Komponente ist nicht enthalten. Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer Blazor-App verwendet. |
| `Static`            | Rendert die Komponente in statisches HTML-Format. |

Seiten und Ansichten können zwar Komponenten verwenden, doch das Gegenteil ist nicht der Fall. Komponenten können ansichts- und seitenspezifische Szenarios nicht nutzen, z. B. Teilansichten und Abschnitte. Zum Verwenden der Logik einer Teilansicht in einer Komponente müssen Sie die Logik in die Komponente verlagern.

Das Rendern von Serverkomponenten über eine statische HTML-Seite wird nicht unterstützt.

Weitere Informationen über das Rendern von Komponenten, den Komponentenzustand und das `Component`-Taghilfsprogramm finden Sie in den folgenden Artikeln:

* <xref:blazor/hosting-models>
* <xref:blazor/hosting-model-configuration>
