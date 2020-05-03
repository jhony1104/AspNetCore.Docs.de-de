---
title: Hostingmodellkonfiguration für ASP.NET Core Blazor
author: guardrex
description: Hier erhalten Sie Informationen zur Blazor-Hostingmodellkonfiguration, einschließlich der Integration von Razor-Komponenten in Razor Pages- und MVC-Apps.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/25/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: c7e8d1f2dcba6432072a5cc11a6c5d78e50c2398
ms.sourcegitcommit: c6f5ea6397af2dd202632cf2be66fc30f3357bcc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/26/2020
ms.locfileid: "82159618"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>Hostingmodellkonfiguration für ASP.NET Core Blazor

Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Dieser Artikel behandelt die Hostingmodellkonfiguration.

## <a name="blazor-webassembly"></a>Blazor WebAssembly

### <a name="environment"></a>Umgebung

Wenn eine App lokal ausgeführt wird, wird Umgebung standardmäßig auf „Entwicklung“ festgelegt. Wenn die App veröffentlicht wird, wird standardmäßig die Produktionsumgebung verwendet.

Eine gehostete Blazor WebAssembly-App übernimmt die Umgebung vom Server mittels einer Middleware, die die Umgebung dem Browser mitteilt, indem sie den `blazor-environment`-Header hinzufügt. Der Wert des Headers ist die Umgebung. Die gehostete Blazor-App und die Server-App verwenden gemeinsam dieselbe Umgebung. Weitere Informationen, einschließlich Informationen zum Konfigurieren der Umgebung, finden Sie unter <xref:fundamentals/environments>.

Wenn eine eigenständige App lokal ausgeführt wird, fügt der Entwicklungsserver den `blazor-environment`-Header hinzu, um die Entwicklungsumgebung anzugeben. Um die Umgebung für andere Hostingumgebungen anzugeben, fügen Sie den `blazor-environment`-Header hinzu.

Im folgenden Beispiel für IIS fügen Sie den benutzerdefinierten Header zu der veröffentlichten Datei *web.config* hinzu. Die Datei *web.config* befindet sich im Ordner */bin/Release/{ZIELFRAMEWORK}/publish*:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>

    ...

    <httpProtocol>
      <customHeaders>
        <add name="blazor-environment" value="Staging" />
      </customHeaders>
    </httpProtocol>
  </system.webServer>
</configuration>
```

> [!NOTE]
> Informationen zum Verwenden einer benutzerdefinierten Datei *web.config* für IIS, die nicht überschrieben wird, wenn die App im Ordner *publish* veröffentlicht wird, finden Sie unter <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.

Rufen Sie die Umgebung der App in einer Komponente ab, indem Sie `IWebAssemblyHostEnvironment` einfügen und die `Environment`-Eigenschaft lesen:

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

Während des Starts macht `WebAssemblyHostBuilder` die `IWebAssemblyHostEnvironment` über die `HostEnvironment`-Eigenschaft verfügbar, was es Entwicklern ermöglicht, umgebungsspezifische Logik in ihrem Code zu verwenden:

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

Die folgenden bequemen Erweiterungsmethoden ermöglichen die Überprüfung der aktuellen Umgebung auf die Namen „Entwicklung“, „Produktion“, „Staging“ sowie benutzerdefinierte Umgebungsnamen:

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* `IsEnvironment("{UMGEBUNGSNAME}")

```csharp
if (builder.HostEnvironment.IsStaging())
{
    ...
};

if (builder.HostEnvironment.IsEnvironment("Custom"))
{
    ...
};
```

Die `IWebAssemblyHostEnvironment.BaseAddress`-Eigenschaft kann während des Starts verwendet werden, wenn der `NavigationManager`-Dienst nicht verfügbar ist.

### <a name="configuration"></a>Konfiguration

Blazor WebAssembly unterstützt die Konfiguration über:

* Den [Dateikonfigurationsanbieter](xref:fundamentals/configuration/index#file-configuration-provider) für App-Einstellungsdateien standardmäßig aus:
  * *wwwroot/appsettings.json*
  * *wwwroot/appsettings.{UMGEBUNG}.json*
* Andere [Konfigurationsanbieter](xref:fundamentals/configuration/index), die von der App registriert werden.

> [!WARNING]
> Die Konfiguration ist für Benutzer in einer Blazor WebAssembly-App sichtbar. **Speichern Sie keine App-Geheimnisse oder -Anmeldeinformationen in der Konfiguration.**

Weitere Informationen zur Konfigurationsanbietern finden Sie unter <xref:fundamentals/configuration/index>.

#### <a name="app-settings-configuration"></a>Konfiguration von App-Einstellungen

*wwwroot/appsettings.json*:

```json
{
  "message": "Hello from config!"
}
```

Fügen Sie eine <xref:Microsoft.Extensions.Configuration.IConfiguration>-Instanz in eine Komponente ein, um auf die Konfigurationsdaten zuzugreifen:

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a>Anbieterkonfiguration

Im folgenden Beispiel werden ein <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource>-Element und der [Dateikonfigurationsanbieter](xref:fundamentals/configuration/index#file-configuration-provider) verwendet, um zusätzliche Konfiguration bereitzustellen:

`Program.Main`:

```csharp
using Microsoft.Extensions.Configuration;

...

var vehicleData = new Dictionary<string, string>()
{
    { "color", "blue" },
    { "type", "car" },
    { "wheels:count", "3" },
    { "wheels:brand", "Blazin" },
    { "wheels:brand:type", "rally" },
    { "wheels:year", "2008" },
};

var memoryConfig = new MemoryConfigurationSource { InitialData = vehicleData };

...

builder.Configuration
    .Add(memoryConfig)
    .AddJsonFile("cars.json", optional: false, reloadOnChange: true);
```

Fügen Sie eine <xref:Microsoft.Extensions.Configuration.IConfiguration>-Instanz in eine Komponente ein, um auf die Konfigurationsdaten zuzugreifen:

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</p>
    <li>Brand: @Configuration["wheels:brand"]</p>
    <li>Type: @Configuration["wheels:brand:type"]</p>
    <li>Year: @Configuration["wheels:year"]</p>
</ul>

@code {
    var wheelsSection = Configuration.GetSection("wheels");
    
    ...
}
```

#### <a name="authentication-configuration"></a>Authentifizierungskonfiguration

*wwwroot/appsettings.json*:

```json
{
  "AzureAD": {
    "Authority": "https://login.microsoftonline.com/",
    "ClientId": "aeaebf0f-d416-4d92-a08f-e1d5b51fc494"
  }
}
```

`Program.Main`:

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("AzureAD", options);
```

#### <a name="logging-configuration"></a>Konfiguration der Protokollierung

*wwwroot/appsettings.json*:

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```

`Program.Main`:

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a>Konfiguration des Host-Generators

`Program.Main`:

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a>Zwischengespeicherte Konfiguration

Konfigurationsdateien werden zur Offlineverwendung zwischengespeichert. Bei [progressiven Web-Apps (PWAs)](xref:blazor/progressive-web-app) können Sie Konfigurationsdateien nur beim Erstellen einer neuen Bereitstellung aktualisieren. Die Bearbeitung von Konfigurationsdateien zwischen Bereitstellungen hat aus folgenden Gründen keine Auswirkungen:

* Benutzer verfügen über zwischengespeicherte Versionen der Dateien, die sie weiterhin verwenden können.
* Die Dateien *service-worker.js* und *service-worker-assets.js* der PWA müssen bei der Kompilierung neu erstellt werden, wodurch der App beim nächsten Onlineaufruf des Benutzers signalisiert wird, dass die App neu bereitgestellt wurde.

Weitere Informationen zur Verarbeitung von Updates im Hintergrund durch PWAs finden Sie unter <xref:blazor/progressive-web-app#background-updates>.

### <a name="logging"></a>Protokollierung

Informationen zur Unterstützung der Blazor WebAssembly-Protokollierung finden Sie unter <xref:fundamentals/logging/index#create-logs-in-blazor>.

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

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a>Konfigurieren des SignalR-Clients für Blazor Server-Apps

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

### <a name="logging"></a>Protokollierung

Weitere Informationen zur Unterstützung von Blazor-Serverprotokollierung finden Sie unter <xref:fundamentals/logging/index#create-logs-in-blazor>.
