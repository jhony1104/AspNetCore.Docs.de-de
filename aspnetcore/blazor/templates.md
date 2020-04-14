---
title: ASP.NET Core-Blazor-Vorlagen
author: guardrex
description: Hier erfahren Sie mehr über ASP.NET Core-Vorlagen für Blazor-Apps und die Blazor-Projektstruktur.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: blazor/templates
ms.openlocfilehash: 71a9d9eee8637dda0b3cecac82ff96a0c3bfedb5
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80320984"
---
# <a name="aspnet-core-opno-locblazor-templates"></a>ASP.NET Core-Blazor-Vorlagen

Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Das Blazor-Framework bietet Vorlagen zum Entwickeln von Apps für die einzelnen Blazor-Hostingmodelle:

* Blazor WebAssembly (`blazorwasm`)
* Blazor Server (`blazorserver`)

Weitere Informationen zur Blazor-Hostingmodellen finden Sie unter <xref:blazor/hosting-models>.

Eine ausführliche Anleitung zum Erstellen einer Blazor-App aus einer Vorlage finden Sie unter <xref:blazor/get-started>.

## <a name="opno-locblazor-project-structure"></a>Blazor-Projektstruktur

Die folgenden Dateien und Ordner bilden eine Blazor-App, die aus einer Blazor-Vorlage generiert wird:

* *Program.cs:* der Einstiegspunkt der App, von dem aus Folgendes eingerichtet wird:

  * der ASP.NET Core-[Host](xref:fundamentals/host/generic-host) (Blazor Server)
  * der WebAssembly-Host (Blazor WebAssembly): Der Code in dieser Datei ist für Apps eindeutig, die aus der BlazorWebAssembly-Vorlage (`blazorwasm`) erstellt wurden.
    * Die `App`-Komponente, die die Stammkomponente der App ist, wird als `app`-DOM-Element für die `Add`-Methode angegeben.
    * Dienste können mit der `ConfigureServices`-Methode auf dem Host-Generator konfiguriert werden (z. B. `builder.Services.AddSingleton<IMyDependency, MyDependency>();`).
    * Die Konfiguration kann über den Host-Generator (`builder.Configuration`) bereitgestellt werden.

* *Startup.cs* (Blazor Server): Diese Datei enthält die Startlogik der App. Die `Startup`-Klasse definiert zwei Methoden:

  * `ConfigureServices`: Diese Methode konfiguriert die [DI-Dienste (Dependency Injection)](xref:fundamentals/dependency-injection) der App. In Blazor Server-Apps werden Dienste durch Aufrufe von <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*> hinzugefügt, und `WeatherForecastService` wird zum Dienstcontainer hinzugefügt, damit die `FetchData`-Beispielkomponente darauf zugreifen kann.
  * `Configure`: Konfiguriert die Pipeline für die Anforderungsverarbeitung der App:
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*> wird aufgerufen, um einen Endpunkt für die Echtzeitverbindung mit dem Browser einzurichten. Die Verbindung wird mit [SignalR](xref:signalr/introduction) hergestellt. Dabei handelt es sich um ein Framework zum Hinzufügen von Echtzeitwebfunktionen zu Apps.
    * [MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) wird aufgerufen, um die Stammseite der App (*Pages/_Host.cshtml*) einzurichten und die Navigation zu ermöglichen.

* *wwwroot/index.html* (Blazor WebAssembly): die Stammseite der App, die als HTML-Seite implementiert wird:
  * Wenn eine Seite der App zum ersten Mal angefordert wird, wird diese Seite gerendert und in der Antwort zurückgegeben.
  * Die Seite gibt an, wo die `App`-Stammkomponente gerendert wird. Die `App`-Komponente (*App.razor*) wird in der `AddComponent`-Methode in `Startup.Configure` als `app`-DOM-Element angegeben.
  * Die `_framework/blazor.webassembly.js`-JavaScript-Datei wird geladen:
    * Diese lädt die .NET-Runtime, die App und der App-Abhängigkeiten herunter.
    * Sie initialisiert die Runtime, damit die App ausgeführt werden kann.

* *App.razor*: Hierbei handelt es sich um die Stammkomponente der App, die das clientseitige Routing mithilfe der <xref:Microsoft.AspNetCore.Components.Routing.Router>-Komponente einrichtet. Die `Router`-Komponente fängt die Browsernavigation ab und rendert die Seite, die der angeforderten Adresse entspricht.

* *Pages*: Dieser Ordner enthält die routingfähigen Komponenten und Seiten ( *.razor*), aus denen die Blazor-App besteht, sowie die Razor-Stammseite einer Blazor-Server-App. Die Route für jede Seite wird mithilfe der [`@page`](xref:mvc/views/razor#page)-Anweisung angegeben. Die Vorlage besteht aus den folgenden Teilen:
  * *_Host.cshtml* (Blazor-Server): die Stammseite der App, die als Razor-Seite implementiert ist:
    * Wenn eine Seite der App zum ersten Mal angefordert wird, wird diese Seite gerendert und in der Antwort zurückgegeben.
    * Die `_framework/blazor.server.js`-JavaScript-Datei wird geladen, die die SignalR-Echtzeitverbindung zwischen dem Browser und dem Server einrichtet.
    * Auf der Hostseite wird angegeben, wo die `App`-Stammkomponente (*App.razor*) gerendert wird.
  * `Counter` (*Counter.razor*) implementiert die Zählerseite
  * `Error` (*Error.razor*, nur Blazor Server-Apps): wird gerendert, wenn ein Ausnahmefehler in der App auftritt
  * `FetchData` (*FetchData.razor*): implementiert die Seite zum Abrufen von Daten
  * `Index` (*Index.razor*): implementiert die Startseite

* *Shared*: Dieser Ordner enthält weitere Benutzeroberflächenkomponenten ( *.razor*), die von der App verwendet werden:
  * `MainLayout` (*MainLayout.razor*): die Layoutkomponente der App
  * `NavMenu` (*NavMenu.razor*): Diese Komponente implementiert die Navigation in der Seitenleiste. Sie schließt die [NavLink-Komponente](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>) ein, die Navigationslinks zu anderen Razor-Komponenten rendert. Die `NavLink`-Komponente gibt automatisch einen ausgewählten Zustand an, wenn die Komponente geladen wird, sodass der Benutzer nachvollziehen kann, welche Komponente derzeit angezeigt wird.

* *_Imports.razor*: enthält gängige Razor-Anweisungen, die in die Komponenten der App ( *.razor*) eingefügt werden sollen, z. B. [`@using`](xref:mvc/views/razor#using)-Anweisungen für Namespaces

* *Data* (Blazor Server): Dieser Ordner enthält die `WeatherForecast`-Klasse und Implementierung von `WeatherForecastService`, die Beispielwetterdaten für die `FetchData`-Komponente der App bereitstellen.

* *wwwroot*: der [Web Root](xref:fundamentals/index#web-root)-Ordner für die App, der die öffentlichen statischen Ressourcen der App enthält

* *appsettings.json* (Blazor Server): Konfigurationseinstellungen für die App
