---
title: "title: 'Integrieren von ASP.NET Core-Razor-Komponenten in Razor Pages- und MVC-Apps' author: description: 'Erfahren Sie mehr über Datenbindungsszenarios für Komponenten und DOM-Elemente in Blazor-Apps.'"
author: guardrex
description: 'monikerRange: ms.author: ms.custom: ms.date: no-loc:'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/25/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: 97515ec519c4bedb0478f510ec9ed739b5d76e4f
ms.sourcegitcommit: 58722eb309767e462bdbf3082bd38737a4ef168f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84105245"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a>'Blazor'

'Identity'

'Let's Encrypt' 'Razor'

'SignalR' uid:

* Integrieren von ASP.NET Core Razor-Komponenten in Razor Pages- und MVC-Apps Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)
  * Razor-Komponenten können in Razor Pages- und MVC-Apps integriert werden.
  * Wenn die Seite oder Ansicht gerendert wird, können Komponenten gleichzeitig vorab gerendert werden.
* Nachdem Sie [die App vorbereitet haben](#prepare-the-app), verwenden Sie die Anleitungen in den folgenden Abschnitten, abhängig von den Anforderungen der App: Routingfähige Komponenten: Die folgenden Abschnitte beziehen sich auf Komponenten, die über Benutzeranforderungen direkt routingfähig sind.

## <a name="prepare-the-app"></a>Befolgen Sie diese Anleitung, wenn Besucher in der Lage sein sollen, in ihrem Browser eine HTTP-Anforderung für eine Komponente mit einer [`@page`](xref:mvc/views/razor#page)-Direktive zu erstellen.

[Verwenden routingfähiger Komponenten in einer Razor Pages-App](#use-routable-components-in-a-razor-pages-app)

1. [Verwenden routingfähiger Komponenten in einer MVC-App](#use-routable-components-in-an-mvc-app)

   * [Rendern von Komponenten einer Seite oder Ansicht:](#render-components-from-a-page-or-view) Dieser Abschnitt bezieht sich auf Komponenten, die nicht über Benutzeranforderungen direkt routingfähig sind.

     ```html
     <base href="~/" />
     ```

     Befolgen Sie diese Anleitung, wenn die App Komponenten mit dem [Taghilfsprogramm für Komponenten](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) in vorhandene Seiten und Ansichten einbettet. Vorbereiten der App

     Eine vorhandene Razor Pages- oder MVC-App kann Razor-Komponenten in Seiten und Ansichten integrieren:

   * In der Layoutdatei der App ( *_Layout.cshtml*):

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     Fügen Sie das folgende `<base>`-Tag zum `<head>`-Element hinzu: Der `href`-Wert (der *App-Basispfad*) im obigen Beispiel setzt voraus, dass die App sich im URL-Stammpfad (`/`) befindet.

1. Wenn es sich bei der App um eine untergeordnete Anwendung handelt, befolgen Sie die Anweisungen im Abschnitt *App-Basispfad* des <xref:host-and-deploy/blazor/index#app-base-path>-Artikels.

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

1. Die Datei *_Layout.cshtml* befindet sich bei Razor Pages-Apps im Ordner *Pages/Shared* und bei MVC-Apps im Ordner *Views/Shared*.

   ```csharp
   services.AddServerSideBlazor();
   ```

1. Fügen Sie ein `<script>`-Tag für das Skript *blazor.server.js* direkt vor dem schließenden `</body>`-Tag ein:

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. Das Framework fügt das Skript *blazor.server.js* zur App hinzu. Das Skript muss nicht manuell zur App hinzugefügt werden.

## <a name="use-routable-components-in-a-razor-pages-app"></a>Fügen Sie eine *_Imports.razor*-Datei zum Stammordner des Projekts mit dem folgenden Inhalt ein (ändern Sie den letzten Namespace `MyAppNamespace` in den Namespace der App):

Registrieren Sie den Blazor Server-Dienst in `Startup.ConfigureServices`:

Fügen Sie in `Startup.Configure` den Blazor Hub-Endpunkt zu `app.UseEndpoints` hinzu:

1. Integrieren Sie Komponenten in eine beliebige Seite oder Ansicht.

1. Weitere Informationen finden Sie im Abschnitt [Rendern von Komponenten einer Seite oder Ansicht](#render-components-from-a-page-or-view).

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

1. Verwenden routingfähiger Komponenten in einer Razor Pages-App

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   *In diesem Abschnitt wird das Hinzufügen von Komponenten behandelt, die über Benutzeranforderungen direkt routingfähig sind.*

   So richten Sie die Unterstützung von routingfähigen Razor-Komponenten in Razor Pages-Apps ein:

   * Befolgen Sie die Anleitungen im Abschnitt [Vorbereiten der App](#prepare-the-app).
   * Fügen Sie eine *App.razor*-Datei mit dem folgenden Inhalt zum Projektstamm hinzu:

   | Fügen Sie eine *_Host.cshtml*-Datei mit dem folgenden Inhalt zum Ordner *Pages* hinzu: | Komponenten verwenden die gemeinsam verwendete Datei *_Layout.cshtml* für ihr Layout. |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> konfiguriert folgende Einstellungen für die `App`-Komponente: Ob die Komponente zuvor für die Seite gerendert wird |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Ob die Komponente als statische HTML auf der Seite gerendert wird oder ob sie die nötigen Informationen für das Bootstrapping einer Blazor-App über den Benutzer-Agent enthält. Rendermodus Beschreibung |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | title: 'Integrieren von ASP.NET Core-Razor-Komponenten in Razor Pages- und MVC-Apps' author: description: 'Erfahren Sie mehr über Datenbindungsszenarios für Komponenten und DOM-Elemente in Blazor-Apps.' |

   monikerRange: ms.author: ms.custom: ms.date: no-loc:

1. 'Blazor'

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. 'Identity' 'Let's Encrypt'

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

'Razor'

## <a name="use-routable-components-in-an-mvc-app"></a>'SignalR' uid:

title: 'Integrieren von ASP.NET Core-Razor-Komponenten in Razor Pages- und MVC-Apps' author: description: 'Erfahren Sie mehr über Datenbindungsszenarios für Komponenten und DOM-Elemente in Blazor-Apps.'

monikerRange: ms.author: ms.custom: ms.date: no-loc:

1. 'Blazor'

1. 'Identity'

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

1. 'Let's Encrypt'

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   'Razor'
   
   'SignalR' uid:

   * title: 'Integrieren von ASP.NET Core-Razor-Komponenten in Razor Pages- und MVC-Apps' author: description: 'Erfahren Sie mehr über Datenbindungsszenarios für Komponenten und DOM-Elemente in Blazor-Apps.'
   * monikerRange: ms.author: ms.custom: ms.date: no-loc:

   | 'Blazor' | 'Identity' |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | 'Let's Encrypt' 'Razor' |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | 'SignalR' uid: ------ | --- title: 'Integrieren von ASP.NET Core-Razor-Komponenten in Razor Pages- und MVC-Apps' author: description: 'Erfahren Sie mehr über Datenbindungsszenarios für Komponenten und DOM-Elemente in Blazor-Apps.' monikerRange: ms.author: ms.custom: ms.date: no-loc: |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | 'Blazor' |

   'Identity'

1. 'Let's Encrypt'

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. 'Razor'

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. 'SignalR' uid: title: 'Integrieren von ASP.NET Core-Razor-Komponenten in Razor Pages- und MVC-Apps' author: description: 'Erfahren Sie mehr über Datenbindungsszenarios für Komponenten und DOM-Elemente in Blazor-Apps.'

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

monikerRange: ms.author: ms.custom: ms.date: no-loc:

## <a name="render-components-from-a-page-or-view"></a>'Blazor'

'Identity'

'Let's Encrypt'

### <a name="render-stateful-interactive-components"></a>'Razor'

'SignalR' uid:

title: 'Integrieren von ASP.NET Core-Razor-Komponenten in Razor Pages- und MVC-Apps' author: description: 'Erfahren Sie mehr über Datenbindungsszenarios für Komponenten und DOM-Elemente in Blazor-Apps.'

* monikerRange: ms.author: ms.custom: ms.date: no-loc:
* 'Blazor'
* 'Identity'

'Let's Encrypt'

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

'Razor'

### <a name="render-noninteractive-components"></a>'SignalR' uid:

------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Rendert die `App`-Komponente in statisches HTML und fügt einen Marker für eine Blazor-Server-App hinzu Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer Blazor-App verwendet.

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Rendert einen Marker für eine Blazor-Server-App

## <a name="component-namespaces"></a>Die Ausgabe der `App`-Komponente ist nicht enthalten.

Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer Blazor-App verwendet. | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Rendert die `App`-Komponente in statisches HTML

* Weitere Informationen zum Taghilfsprogramm für Komponenten finden Sie unter <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.
* Fügen Sie eine Route mit niedriger Priorität für die Seite *_Host.cshtml* zur Endpunktkonfiguration in `Startup.Configure` hinzu:

```cshtml
@using MyAppNamespace.Components
```

Fügen Sie routingfähige Komponenten zur App hinzu.

Zum Beispiel:
