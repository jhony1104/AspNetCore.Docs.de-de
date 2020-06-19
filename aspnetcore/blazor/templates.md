---
title: ASP.NET Core-Blazor-Vorlagen
author: guardrex
description: Hier erfahren Sie mehr über ASP.NET Core-Vorlagen für Blazor-Apps und die Blazor-Projektstruktur.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/templates
ms.openlocfilehash: 3a491e43aec8291fcf03696b53240bd33e0deda1
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/19/2020
ms.locfileid: "85102486"
---
# <a name="aspnet-core-blazor-templates"></a>ASP.NET Core-Blazor-Vorlagen

Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)

Das Blazor-Framework bietet Vorlagen zum Entwickeln von Apps für die einzelnen Blazor-Hostingmodelle:

* Blazor WebAssembly (`blazorwasm`)
* Blazor Server (`blazorserver`)

Weitere Informationen zur Blazor-Hostingmodellen finden Sie unter <xref:blazor/hosting-models>.

Eine ausführliche Anleitung zum Erstellen einer Blazor-App aus einer Vorlage finden Sie unter <xref:blazor/get-started>.

Vorlagenoptionen sind verfügbar, indem Sie die `--help`-Option an den CLI-Befehl [dotnet new](/dotnet/core/tools/dotnet-new) übergeben:

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="blazor-project-structure"></a>Blazor-Projektstruktur

Die folgenden Dateien und Ordner bilden eine Blazor-App, die aus einer Blazor-Vorlage generiert wird:

* *Program.cs*: Der Einstiegspunkt der App, von dem aus Folgendes eingerichtet wird:

  * der ASP.NET Core-[Host](xref:fundamentals/host/generic-host) (Blazor Server)
  * WebAssembly-Host (Blazor-WebAssembly): Der Code in dieser Datei ist für Apps eindeutig, die aus der Blazor-WebAssembly-Vorlage (`blazorwasm`) erstellt wurden.
    * Die `App`-Komponente, die die Stammkomponente der App ist, wird als `app`-DOM-Element für die `Add`-Methode angegeben.
    * Dienste können mit der `ConfigureServices`-Methode auf dem Host-Generator konfiguriert werden (z. B. `builder.Services.AddSingleton<IMyDependency, MyDependency>();`).
    * Die Konfiguration kann über den Host-Generator (`builder.Configuration`) bereitgestellt werden.

* *Startup.cs* (Blazor-Server): Diese Datei enthält die Startlogik der App. Die `Startup`-Klasse definiert zwei Methoden:

  * `ConfigureServices`: Diese Methode konfiguriert die [DI-Dienste (Dependency Injection)](xref:fundamentals/dependency-injection) der App. In Blazor Server-Apps werden Dienste durch Aufrufe von <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> hinzugefügt, und `WeatherForecastService` wird zum Dienstcontainer hinzugefügt, damit die `FetchData`-Beispielkomponente darauf zugreifen kann.
  * `Configure`: Konfiguriert die Pipeline für die Anforderungsverarbeitung der App:
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> wird aufgerufen, um einen Endpunkt für die Echtzeitverbindung mit dem Browser einzurichten. Die Verbindung wird mit [SignalR](xref:signalr/introduction) hergestellt. Dabei handelt es sich um ein Framework zum Hinzufügen von Echtzeitwebfunktionen zu Apps.
    * [MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) wird aufgerufen, um die Stammseite der App (*Pages/_Host.cshtml*) einzurichten und die Navigation zu ermöglichen.

* *wwwroot/index.html* (Blazor WebAssembly): Die Stammseite der App, die als HTML-Seite implementiert ist:
  * Wenn eine Seite der App zum ersten Mal angefordert wird, wird diese Seite gerendert und in der Antwort zurückgegeben.
  * Die Seite gibt an, wo die `App`-Stammkomponente gerendert wird. Die `App`-Komponente (*App.razor*) wird in der `AddComponent`-Methode in `Startup.Configure` als `app`-DOM-Element angegeben.
  * Die `_framework/blazor.webassembly.js`-JavaScript-Datei wird geladen:
    * Diese lädt die .NET-Runtime, die App und der App-Abhängigkeiten herunter.
    * Sie initialisiert die Runtime, damit die App ausgeführt werden kann.

* *App.razor*: Hierbei handelt es sich um die Stammkomponente der App, die das clientseitige Routing mithilfe der <xref:Microsoft.AspNetCore.Components.Routing.Router>-Komponente einrichtet. Die <xref:Microsoft.AspNetCore.Components.Routing.Router>-Komponente fängt die Browsernavigation ab und rendert die Seite, die der angeforderten Adresse entspricht.

* Ordner *Pages*: Dieser Ordner enthält die routingfähigen Komponenten und Seiten (*RAZOR-Dateien*), aus denen die Blazor-App besteht, sowie die Razor-Stammseite einer Blazor-Server-App. Die Route für jede Seite wird mithilfe der [`@page`](xref:mvc/views/razor#page)-Anweisung angegeben. Die Vorlage besteht aus den folgenden Teilen:
  * *_Host.cshtml* (Blazor-Server): Die Stammseite der App, die als Razor-Seite implementiert ist:
    * Wenn eine Seite der App zum ersten Mal angefordert wird, wird diese Seite gerendert und in der Antwort zurückgegeben.
    * Die `_framework/blazor.server.js`-JavaScript-Datei wird geladen, die die SignalR-Echtzeitverbindung zwischen dem Browser und dem Server einrichtet.
    * Auf der Hostseite wird angegeben, wo die `App`-Stammkomponente (*App.razor*) gerendert wird.
  * `Counter` (*Counter.razor*): Implementiert die Zählerseite.
  * `Error` (*Error.razor*, nur Blazor-Server-App): Wird gerendert, wenn in der App eine nicht behandelte Ausnahme auftritt.
  * `FetchData` (*FetchData.razor*): Implementiert die Seite zum Abrufen von Daten.
  * `Index` (*Index.razor*): Implementiert die Homepage.

* Ordner *Shared*: Dieser Ordner enthält weitere Benutzeroberflächenkomponenten (*RAZOR-Dateien*), die von der App verwendet werden:
  * `MainLayout` (*MainLayout.razor*): Die Layoutkomponente der App.
  * `NavMenu` (*NavMenu.razor*): Diese Komponente implementiert die Navigation in der Seitenleiste. Sie schließt die [NavLink-Komponente](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>) ein, die Navigationslinks zu anderen Razor-Komponenten rendert. Die <xref:Microsoft.AspNetCore.Components.Routing.NavLink>-Komponente gibt automatisch einen ausgewählten Zustand an, wenn die Komponente geladen wird, sodass der Benutzer nachvollziehen kann, welche Komponente derzeit angezeigt wird.

* *_Imports.razor*: Enthält gängige Razor-Anweisungen, die in die Komponenten der App (*RAZOR-Dateien*) eingefügt werden sollen, z. B. [`@using`](xref:mvc/views/razor#using)-Anweisungen für Namespaces.

* Ordner *Data* (Blazor-Server): Dieser Ordner enthält die `WeatherForecast`-Klasse und Implementierung von `WeatherForecastService`, die Beispielwetterdaten für die `FetchData`-Komponente der App bereitstellen.

* *wwwroot*: Der Ordner [Web Root](xref:fundamentals/index#web-root) für die App, der die öffentlichen statischen Ressourcen der App enthält.

* *appsettings.json* (Blazor-Server): Konfigurationseinstellungen für die App.
