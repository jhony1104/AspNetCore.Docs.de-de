---
title: "title: 'Blazor-Hostingmodellkonfiguration für ASP.NET Core' author: guardrex description: 'Hier erhalten Sie Informationen zur Blazor-Hostingmodellkonfiguration, einschließlich der Integration von Razor-Komponenten in Razor Pages- und MVC-Apps.'"
author: guardrex
description: "monikerRange: '>= aspnetcore-3.1' ms.author: riande ms.custom: mvc ms.date: 05/28/2020 no-loc:"
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/28/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: e3b8b91a570210e77f307c49f7be21eeab714daa
ms.sourcegitcommit: 829dca1d5a7dcccbfe90644101c6be1d1c94ac62
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2020
ms.locfileid: "84355109"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>'Blazor'

'Identity'

'Let's Encrypt'

## <a name="blazor-webassembly"></a>'Razor'

### <a name="environment"></a>'SignalR' uid: blazor/hosting-model-configuration

Hostingmodellkonfiguration für ASP.NET Core Blazor Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)

Dieser Artikel behandelt die Hostingmodellkonfiguration. Blazor WebAssembly Umgebung Wenn eine App lokal ausgeführt wird, wird Umgebung standardmäßig auf „Entwicklung“ festgelegt.

Wenn die App veröffentlicht wird, wird standardmäßig die Produktionsumgebung verwendet. Eine gehostete Blazor WebAssembly-App übernimmt die Umgebung vom Server mittels einer Middleware, die die Umgebung dem Browser mitteilt, indem sie den `blazor-environment`-Header hinzufügt.

Der Wert des Headers ist die Umgebung. Die gehostete Blazor-App und die Server-App verwenden gemeinsam dieselbe Umgebung.

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
> Weitere Informationen, einschließlich Informationen zum Konfigurieren der Umgebung, finden Sie unter <xref:fundamentals/environments>.

Wenn eine eigenständige App lokal ausgeführt wird, fügt der Entwicklungsserver den `blazor-environment`-Header hinzu, um die Entwicklungsumgebung anzugeben.

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

Um die Umgebung für andere Hostingumgebungen anzugeben, fügen Sie den `blazor-environment`-Header hinzu.

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

Im folgenden Beispiel für IIS fügen Sie den benutzerdefinierten Header zu der veröffentlichten Datei *web.config* hinzu.

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* `IsEnvironment("{ENVIRONMENT NAME}")`

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

Die Datei *web.config* befindet sich im Ordner */bin/Release/{ZIELFRAMEWORK}/publish*:

### <a name="configuration"></a>Informationen zum Verwenden einer benutzerdefinierten Datei *web.config* für IIS, die nicht überschrieben wird, wenn die App im Ordner *publish* veröffentlicht wird, finden Sie unter <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.

Rufen Sie die Umgebung der App in einer Komponente ab, indem Sie <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> einfügen und die <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment>-Eigenschaft lesen:

* Während des Starts macht <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> die <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> über die <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment>-Eigenschaft verfügbar, was es Entwicklern ermöglicht, umgebungsspezifische Logik in ihrem Code zu verwenden:
  * Die folgenden bequemen Erweiterungsmethoden ermöglichen die Überprüfung der aktuellen Umgebung auf die Namen „Entwicklung“, „Produktion“, „Staging“ sowie benutzerdefinierte Umgebungsnamen:
  * Die <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType>-Eigenschaft kann während des Starts verwendet werden, wenn der <xref:Microsoft.AspNetCore.Components.NavigationManager>-Dienst nicht verfügbar ist.
* Konfiguration Blazor WebAssembly lädt die Konfiguration von: Standardmäßig aus den App-Einstellungsdateien:

> [!WARNING]
> *wwwroot/appsettings.json* *wwwroot/appsettings.{UMGEBUNG}.json*

Andere [Konfigurationsanbieter](xref:fundamentals/configuration/index), die von der App registriert werden.

#### <a name="app-settings-configuration"></a>Nicht alle Anbieter sind für Blazor WebAssembly-Apps geeignet.

Welche Anbieter für Blazor WebAssembly geeignet sind, erfahren Sie unter [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134) (Konfigurationsanbieter für Blazor WebAssembly).

```json
{
  "message": "Hello from config!"
}
```

Die Konfiguration in einer Blazor WebAssembly-App ist für Benutzer sichtbar.

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a>**Speichern Sie keine App-Geheimnisse oder -Anmeldeinformationen in der Konfiguration.**

Weitere Informationen zur Konfigurationsanbietern finden Sie unter <xref:fundamentals/configuration/index>.

Konfiguration von App-Einstellungen

```csharp
using Microsoft.Extensions.Configuration.Memory;

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

builder.Configuration.Add(memoryConfig);
```

*wwwroot/appsettings.json*:

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</li>
    <li>Brand: @Configuration["wheels:brand"]</li>
    <li>Type: @Configuration["wheels:brand:type"]</li>
    <li>Year: @Configuration["wheels:year"]</li>
</ul>

@code {
    var wheelsSection = Configuration.GetSection("wheels");
    
    ...
}
```

Fügen Sie eine <xref:Microsoft.Extensions.Configuration.IConfiguration>-Instanz in eine Komponente ein, um auf die Konfigurationsdaten zuzugreifen: Anbieterkonfiguration

Im folgenden Beispiel wird ein <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource>-Element verwendet, um zusätzliche Konfiguration bereitzustellen:

```json
{
    "size": "tiny"
}
```

`Program.Main`:

```csharp
using Microsoft.Extensions.Configuration;

...

var client = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddTransient(sp => client);

using var response = await client.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

#### <a name="authentication-configuration"></a>Fügen Sie eine <xref:Microsoft.Extensions.Configuration.IConfiguration>-Instanz in eine Komponente ein, um auf die Konfigurationsdaten zuzugreifen:

Um andere Konfigurationsdateien aus dem Ordner *wwwwroot* in die Konfiguration einzulesen, verwenden Sie einen <xref:System.Net.Http.HttpClient>, um den Inhalt der Datei abzurufen.

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

Bei diesem Ansatz kann die vorhandene <xref:System.Net.Http.HttpClient>-Dienstregistrierung den zum Lesen der Datei erstellten lokalen Client verwenden, wie im folgenden Beispiel gezeigt:

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

#### <a name="logging-configuration"></a>*wwwroot/cars.json*:

`Program.Main`:

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

Authentifizierungskonfiguration

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

*wwwroot/appsettings.json*:

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a>`Program.Main`:

Konfiguration der Protokollierung

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a>Fügen Sie einen Paketverweis für [Microsoft.Extensions.Logging.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/) hinzu:

*wwwroot/appsettings.json*: `Program.Main`: Konfiguration des Host-Generators

* `Program.Main`:
* Zwischengespeicherte Konfiguration

Konfigurationsdateien werden zur Offlineverwendung zwischengespeichert.

### <a name="logging"></a>Bei [progressiven Web-Apps (PWAs)](xref:blazor/progressive-web-app) können Sie Konfigurationsdateien nur beim Erstellen einer neuen Bereitstellung aktualisieren.

Die Bearbeitung von Konfigurationsdateien zwischen Bereitstellungen hat aus folgenden Gründen keine Auswirkungen:

## <a name="blazor-server"></a>Benutzer verfügen über zwischengespeicherte Versionen der Dateien, die sie weiterhin verwenden können.

### <a name="reflect-the-connection-state-in-the-ui"></a>Die Dateien *service-worker.js* und *service-worker-assets.js* der PWA müssen bei der Kompilierung neu erstellt werden, wodurch der App beim nächsten Onlineaufruf des Benutzers signalisiert wird, dass die App neu bereitgestellt wurde.

Weitere Informationen zur Verarbeitung von Updates im Hintergrund durch PWAs finden Sie unter <xref:blazor/progressive-web-app#background-updates>. Protokollierung

Informationen zur Unterstützung der Blazor WebAssembly-Protokollierung finden Sie unter <xref:fundamentals/logging/index#create-logs-in-blazor>.

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

Blazor Server

| Reflektieren des Verbindungszustands auf der Benutzeroberfläche                       | Wenn der Client erkennt, dass keine Verbindung mehr besteht, wird dem Benutzer eine Standardbenutzeroberfläche angezeigt, während der Client versucht, eine neue Verbindung herzustellen. |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | Wenn die Wiederherstellung der Verbindung fehlschlägt, wird dem Benutzer die Option angezeigt, es noch mal zu versuchen. Wenn Sie die Benutzeroberfläche anpassen möchten, definieren Sie ein Element mit einer `id` von `components-reconnect-modal` im `<body>` der *_Host.cshtml*-Razor-Seite: In der folgenden Tabelle werden die CSS-Klassen beschrieben, die auf das `components-reconnect-modal`-Element angewendet werden. |
| `components-reconnect-hide`     | CSS-Klasse Steht für&hellip; |
| `components-reconnect-failed`   | Die Verbindung wurde getrennt. Der Client versucht, die Verbindung wiederherzustellen. |
| `components-reconnect-rejected` | Die modale Seite wird angezeigt. Auf dem Server wird eine aktive Verbindung wiederhergestellt. Die modale Seite wird ausgeblendet. Die Wiederherstellung der Verbindung ist wahrscheinlich aufgrund eines Netzwerkfehlers fehlgeschlagen.<ul><li>Rufen Sie `window.Blazor.reconnect()` auf, um einen neuen Verbindungsversuch zu unternehmen.</li><li>Die Wiederherstellung der Verbindung wurde abgelehnt. Der Server wurde zwar erreicht, jedoch hat dieser die Verbindung verweigert. Der Status des Benutzers auf dem Server wurde verworfen.</li><li>Rufen Sie `location.reload()` auf, um die App neu zu laden.</li></ul> |

### <a name="render-mode"></a>Dieser Verbindungszustand kann aus folgenden Gründen auftreten:

Aufgetretener Absturz auf der serverseitigen Verbindung. Der Client war lange nicht verbunden, sodass der Server den Benutzerstatus verworfen hat.

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

Komponenteninstanzen, mit denen der Benutzer interagiert, werden verworfen.

* Der Server wird neu gestartet, oder der Workerprozess der App wird wiederverwendet.
* Rendermodus

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | Blazor Server-Apps werden standardmäßig eingerichtet, um die Benutzeroberfläche auf dem Server schon vor der Einrichtung der Clientverbindung auf dem Server zu rendern. |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Sehen Sie sich die Einrichtung der Razor-Seite *_Host.cshtml* an: <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> konfiguriert folgende Einstellungen für die Komponente: |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Ob die Komponente zuvor für die Seite gerendert wird Ob die Komponente als statische HTML auf der Seite gerendert wird oder ob sie die nötigen Informationen für das Bootstrapping einer Blazor-App über den Benutzer-Agent enthält. Beschreibung |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Rendert die Komponente in statische HTML und fügt einen Marker für eine Blazor Server-App hinzu. |

Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer Blazor-App verwendet.

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a>Rendert einen Marker für eine Blazor Server-App.

Die Ausgabe der Komponente ist nicht enthalten. Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer Blazor-App verwendet.

Rendert die Komponente in statischen HTML-Code.

* Das Rendern von Serverkomponenten über eine statische HTML-Seite wird nicht unterstützt.
* Konfigurieren des SignalR-Clients für Blazor Server-Apps

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

### <a name="logging"></a>In einigen Fällen müssen Sie den von den SignalR Server-Apps verwendeten Blazor-Client konfigurieren.

Beispielsweise können Sie die Protokollierung auf dem SignalR-Client konfigurieren, um ein Verbindungsproblem zu diagnostizieren.
