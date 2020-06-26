---
title: Blazor-Konfiguration in ASP.NET Core
author: guardrex
description: In diesem Artikel erhalten Sie weitere Informationen zur Konfiguration von Blazor-Apps, einschließlich der App-Einstellungen, der Authentifizierung und Protokollierungskonfiguration.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/configuration
ms.openlocfilehash: 0e36b81d771b07e85158724c02210ee50a3ab118
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242679"
---
# <a name="aspnet-core-blazor-configuration"></a>Blazor-Konfiguration in ASP.NET Core

> [!NOTE]
> Dieses Thema gilt für Blazor WebAssembly. Eine allgemeine Anleitung zur App-Konfiguration in ASP.NET Core finden Sie unter <xref:fundamentals/configuration/index>.

Blazor WebAssembly lädt die Konfiguration von:

* Standardmäßig aus den App-Einstellungsdateien:
  * `wwwroot/appsettings.json`
  * `wwwroot/appsettings.{ENVIRONMENT}.json`
* Andere [Konfigurationsanbieter](xref:fundamentals/configuration/index), die von der App registriert werden. Nicht alle Anbieter sind für Blazor WebAssembly-Apps geeignet. Welche Anbieter für Blazor WebAssembly geeignet sind, erfahren Sie unter [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134) (Konfigurationsanbieter für Blazor WebAssembly).

> [!WARNING]
> Die Konfiguration in einer Blazor WebAssembly-App ist für Benutzer sichtbar. **Speichern Sie keine App-Geheimnisse oder -Anmeldeinformationen in der Konfiguration.**

Weitere Informationen zur Konfigurationsanbietern finden Sie unter <xref:fundamentals/configuration/index>.

## <a name="app-settings-configuration"></a>Konfiguration von App-Einstellungen

`wwwroot/appsettings.json`:

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

## <a name="provider-configuration"></a>Anbieterkonfiguration

Im folgenden Beispiel wird ein <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource>-Element verwendet, um zusätzliche Konfiguration bereitzustellen:

`Program.Main`:

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

Fügen Sie eine <xref:Microsoft.Extensions.Configuration.IConfiguration>-Instanz in eine Komponente ein, um auf die Konfigurationsdaten zuzugreifen:

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

Um andere Konfigurationsdateien aus dem Ordner `wwwroot` in die Konfiguration einzulesen, verwenden Sie einen <xref:System.Net.Http.HttpClient>, um den Inhalt der Datei abzurufen. Bei diesem Ansatz kann die vorhandene <xref:System.Net.Http.HttpClient>-Dienstregistrierung den zum Lesen der Datei erstellten lokalen Client verwenden, wie im folgenden Beispiel gezeigt:

`wwwroot/cars.json`:

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

## <a name="authentication-configuration"></a>Authentifizierungskonfiguration

`wwwroot/appsettings.json`:

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

`Program.Main`:

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a>Konfiguration der Protokollierung

Fügen Sie einen Paketverweis für [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/) hinzu:

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

`wwwroot/appsettings.json`:

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
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a>Konfiguration des Host-Generators

`Program.Main`:

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a>Zwischengespeicherte Konfiguration

Konfigurationsdateien werden zur Offlineverwendung zwischengespeichert. Bei [progressiven Web-Apps (PWAs)](xref:blazor/progressive-web-app) können Sie Konfigurationsdateien nur beim Erstellen einer neuen Bereitstellung aktualisieren. Die Bearbeitung von Konfigurationsdateien zwischen Bereitstellungen hat aus folgenden Gründen keine Auswirkungen:

* Benutzer verfügen über zwischengespeicherte Versionen der Dateien, die sie weiterhin verwenden können.
* Die Dateien `service-worker.js` und `service-worker-assets.js` der PWA müssen bei der Kompilierung neu erstellt werden, wodurch der App beim nächsten Onlineaufruf des Benutzers signalisiert wird, dass die App neu bereitgestellt wurde.

Weitere Informationen zur Verarbeitung von Updates im Hintergrund durch PWAs finden Sie unter <xref:blazor/progressive-web-app#background-updates>.
