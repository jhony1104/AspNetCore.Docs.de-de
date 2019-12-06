---
title: ASP.net Core Blazor Vorlagen
author: guardrex
description: Erfahren Sie mehr über ASP.net Core Blazor App-Vorlagen und Blazor Projektstruktur.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/25/2019
no-loc:
- Blazor
- SignalR
uid: blazor/templates
ms.openlocfilehash: bc0ea4a777e8684a7b0925377b8a19a45c2b531c
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74879657"
---
# <a name="aspnet-core-opno-locblazor-templates"></a>ASP.net Core Blazor Vorlagen

Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Das Blazor Framework bietet Vorlagen zum Entwickeln von Apps für die einzelnen Blazor Hostingmodelle:

* Blazor Webassembly (`blazorwasm`)
* Blazor Server (`blazorserver`)

Weitere Informationen zu den Hostingmodellen von Blazorfinden Sie unter <xref:blazor/hosting-models>.

Eine Schritt-für-Schritt-Anleitung zum Erstellen einer Blazor-App aus einer Vorlage finden Sie unter <xref:blazor/get-started>.

## <a name="opno-locblazor-project-structure"></a>Blazor Projektstruktur

Die folgenden Dateien und Ordner bilden eine Blazor-APP, die aus einer Blazor Vorlage generiert wird:

* *Program.cs* &ndash; den Einstiegspunkt der APP, der den ASP.net Core [Host](xref:fundamentals/host/generic-host)einrichtet. Der Code in dieser Datei wird von allen ASP.net Core apps gemeinsam, die aus ASP.net Core Vorlagen generiert werden.

* *Startup.cs* &ndash; die die Start Logik der app enthält. Die `Startup`-Klasse definiert zwei Methoden:

  * mit `ConfigureServices` &ndash; werden die [Abhängigkeits Injektions Dienste (di)](xref:fundamentals/dependency-injection) der App konfiguriert. In Blazor Server-apps werden Dienste durch Aufrufen von <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>hinzugefügt, und die `WeatherForecastService` wird zum Dienst Container hinzugefügt, der von der Beispiel `FetchData` Komponente verwendet wird.
  * `Configure` &ndash; konfiguriert die Pipeline für die Anforderungs Verarbeitung der APP:
    * Blazor Webassembly &ndash; die `App` Komponente (angegeben als `app` DOM-Element der `AddComponent`-Methode) hinzufügt, die die Stamm Komponente der APP ist.
    * Blazor Server
      * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*> wird aufgerufen, um einen Endpunkt für die Echtzeitverbindung mit dem Browser einzurichten. Die Verbindung wird mit [SignalR](xref:signalr/introduction)erstellt. dabei handelt es sich um ein Framework zum Hinzufügen von Echt Zeit webollen zu apps.
      * [Mapfallbacktopage ("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) wird aufgerufen, um die Stamm Seite der APP (*pages/_Host. cshtml*) einzurichten und die Navigation zu aktivieren.

* *wwwroot/Index.html* (Blazor Webassembly) &ndash; die Stamm Seite der APP, die als HTML-Seite implementiert ist:
  * Wenn eine Seite der APP anfänglich angefordert wird, wird diese Seite gerendert und in der Antwort zurückgegeben.
  * Die Seite gibt an, wo die Stamm `App` Komponente gerendert wird. Die `App` Komponente (*app. Razor*) wird in `Startup.Configure`als `app` DOM-Element für die `AddComponent`-Methode angegeben.
  * Die *_framework* JavaScript-Datei "/blazor.Webassembly.js" wird geladen, was Folgendes:
    * Herunterladen der .NET-Runtime, der APP und der APP-Abhängigkeiten.
    * Initialisiert die Laufzeit, um die APP auszuführen.

* *Pages/_Host. cshtml* (Blazor Server) &ndash; die Stamm Seite der APP, die als Razor Page implementiert wurde:
  * Wenn eine Seite der APP anfänglich angefordert wird, wird diese Seite gerendert und in der Antwort zurückgegeben.
  * Die *_framework/blazor.Server.js* JavaScript-Datei wird geladen, die die Echt Zeit SignalR Verbindung zwischen dem Browser und dem Server einrichtet.
  * Die Seite Host gibt an, wo die Stamm `App` Komponente (*app. Razor*) gerendert wird.

* *App. Razor* &ndash; die Stamm Komponente der APP, die das Client seitige Routing mithilfe der <xref:Microsoft.AspNetCore.Components.Routing.Router> Komponente einrichtet. Die `Router` Komponente fängt die Browser Navigation ab und rendert die Seite, die der angeforderten Adresse entspricht.

* Der Ordner " *pages* " &ndash; enthält die Routing fähigen Komponenten/Seiten (*Razor*), die die Blazor-App bilden. Die Route für jede Seite wird mit der [`@page`](xref:mvc/views/razor#page) -Direktive angegeben. Die Vorlage enthält die folgenden Komponenten:
  * `Index` (*Index. Razor*) &ndash; die die Startseite implementiert.
  * `Counter` (*counter. Razor*) &ndash; implementiert die Counter-Seite.
  * `Error` (*Error. Razor*, Blazor Server-APP) &ndash; gerendert, wenn eine nicht behandelte Ausnahme in der APP auftritt.
  * `FetchData` (*fetchdata. Razor*) &ndash; implementiert die Seite "Daten abrufen".

* Der frei *gegebene* Ordner &ndash; enthält andere von der APP verwendete UI-Komponenten (*Razor*):
  * `MainLayout` (*MainLayout. Razor*) &ndash; die Layoutkomponente der app.
  * `NavMenu` (*NavMenu. Razor*) &ndash; implementiert die Rand leisten Navigation. Schließt die [navlink-Komponente](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>) ein, die Navigationslinks zu anderen Razor-Komponenten rendert. Die `NavLink` Komponente gibt automatisch einen ausgewählten Zustand an, wenn die Komponente geladen wird, sodass der Benutzer weiß, welche Komponente derzeit angezeigt wird.

* *_Imports. Razor* -&ndash; enthält allgemeine Razor-Direktiven, die in die Komponenten der APP (*Razor*) eingeschlossen werden, z. b. [`@using`](xref:mvc/views/razor#using) Direktiven für Namespaces.

* Der *Daten* Ordner (Blazor Server) &ndash; enthält die `WeatherForecast` Klasse und Implementierung des `WeatherForecastService`, die Beispiel Wetterdaten für die `FetchData` Komponente der APP bereitstellen.

* *wwwroot* &ndash; den [Webstamm](xref:fundamentals/index#web-root) Ordner für die APP, die die öffentlichen statischen Assets der app enthält.

* *appSettings. JSON* (Blazor Server) &ndash; Konfigurationseinstellungen für die app.
