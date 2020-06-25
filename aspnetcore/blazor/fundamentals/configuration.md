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
ms.openlocfilehash: b43eae03c71cabbaafa2bc0d704765e89f743279
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103252"
---
# <a name="aspnet-core-blazor-configuration"></a><span data-ttu-id="01bb5-103">Blazor-Konfiguration in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="01bb5-103">ASP.NET Core Blazor configuration</span></span>

> [!NOTE]
> <span data-ttu-id="01bb5-104">Dieses Thema gilt für Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="01bb5-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="01bb5-105">Eine allgemeine Anleitung zur App-Konfiguration in ASP.NET Core finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="01bb5-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

Blazor<span data-ttu-id="01bb5-106"> WebAssembly lädt die Konfiguration von:</span><span class="sxs-lookup"><span data-stu-id="01bb5-106"> WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="01bb5-107">Standardmäßig aus den App-Einstellungsdateien:</span><span class="sxs-lookup"><span data-stu-id="01bb5-107">App settings files by default:</span></span>
  * <span data-ttu-id="01bb5-108">*wwwroot/appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="01bb5-108">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="01bb5-109">*wwwroot/appsettings.{UMGEBUNG}.json*</span><span class="sxs-lookup"><span data-stu-id="01bb5-109">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="01bb5-110">Andere [Konfigurationsanbieter](xref:fundamentals/configuration/index), die von der App registriert werden.</span><span class="sxs-lookup"><span data-stu-id="01bb5-110">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="01bb5-111">Nicht alle Anbieter sind für Blazor WebAssembly-Apps geeignet.</span><span class="sxs-lookup"><span data-stu-id="01bb5-111">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="01bb5-112">Welche Anbieter für Blazor WebAssembly geeignet sind, erfahren Sie unter [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134) (Konfigurationsanbieter für Blazor WebAssembly).</span><span class="sxs-lookup"><span data-stu-id="01bb5-112">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="01bb5-113">Die Konfiguration in einer Blazor WebAssembly-App ist für Benutzer sichtbar.</span><span class="sxs-lookup"><span data-stu-id="01bb5-113">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="01bb5-114">**Speichern Sie keine App-Geheimnisse oder -Anmeldeinformationen in der Konfiguration.**</span><span class="sxs-lookup"><span data-stu-id="01bb5-114">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="01bb5-115">Weitere Informationen zur Konfigurationsanbietern finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="01bb5-115">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="01bb5-116">Konfiguration von App-Einstellungen</span><span class="sxs-lookup"><span data-stu-id="01bb5-116">App settings configuration</span></span>

<span data-ttu-id="01bb5-117">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="01bb5-117">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="01bb5-118">Fügen Sie eine <xref:Microsoft.Extensions.Configuration.IConfiguration>-Instanz in eine Komponente ein, um auf die Konfigurationsdaten zuzugreifen:</span><span class="sxs-lookup"><span data-stu-id="01bb5-118">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

## <a name="provider-configuration"></a><span data-ttu-id="01bb5-119">Anbieterkonfiguration</span><span class="sxs-lookup"><span data-stu-id="01bb5-119">Provider configuration</span></span>

<span data-ttu-id="01bb5-120">Im folgenden Beispiel wird ein <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource>-Element verwendet, um zusätzliche Konfiguration bereitzustellen:</span><span class="sxs-lookup"><span data-stu-id="01bb5-120">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="01bb5-121">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="01bb5-121">`Program.Main`:</span></span>

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

<span data-ttu-id="01bb5-122">Fügen Sie eine <xref:Microsoft.Extensions.Configuration.IConfiguration>-Instanz in eine Komponente ein, um auf die Konfigurationsdaten zuzugreifen:</span><span class="sxs-lookup"><span data-stu-id="01bb5-122">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="01bb5-123">Um andere Konfigurationsdateien aus dem Ordner *wwwwroot* in die Konfiguration einzulesen, verwenden Sie einen <xref:System.Net.Http.HttpClient>, um den Inhalt der Datei abzurufen.</span><span class="sxs-lookup"><span data-stu-id="01bb5-123">To read other configuration files from the *wwwroot* folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="01bb5-124">Bei diesem Ansatz kann die vorhandene <xref:System.Net.Http.HttpClient>-Dienstregistrierung den zum Lesen der Datei erstellten lokalen Client verwenden, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="01bb5-124">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="01bb5-125">*wwwroot/cars.json*:</span><span class="sxs-lookup"><span data-stu-id="01bb5-125">*wwwroot/cars.json*:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="01bb5-126">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="01bb5-126">`Program.Main`:</span></span>

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

## <a name="authentication-configuration"></a><span data-ttu-id="01bb5-127">Authentifizierungskonfiguration</span><span class="sxs-lookup"><span data-stu-id="01bb5-127">Authentication configuration</span></span>

<span data-ttu-id="01bb5-128">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="01bb5-128">*wwwroot/appsettings.json*:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="01bb5-129">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="01bb5-129">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="01bb5-130">Konfiguration der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="01bb5-130">Logging configuration</span></span>

<span data-ttu-id="01bb5-131">Fügen Sie einen Paketverweis für [Microsoft.Extensions.Logging.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/) hinzu:</span><span class="sxs-lookup"><span data-stu-id="01bb5-131">Add a package reference for [Microsoft.Extensions.Logging.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="01bb5-132">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="01bb5-132">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="01bb5-133">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="01bb5-133">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="01bb5-134">Konfiguration des Host-Generators</span><span class="sxs-lookup"><span data-stu-id="01bb5-134">Host builder configuration</span></span>

<span data-ttu-id="01bb5-135">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="01bb5-135">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="01bb5-136">Zwischengespeicherte Konfiguration</span><span class="sxs-lookup"><span data-stu-id="01bb5-136">Cached configuration</span></span>

<span data-ttu-id="01bb5-137">Konfigurationsdateien werden zur Offlineverwendung zwischengespeichert.</span><span class="sxs-lookup"><span data-stu-id="01bb5-137">Configuration files are cached for offline use.</span></span> <span data-ttu-id="01bb5-138">Bei [progressiven Web-Apps (PWAs)](xref:blazor/progressive-web-app) können Sie Konfigurationsdateien nur beim Erstellen einer neuen Bereitstellung aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="01bb5-138">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="01bb5-139">Die Bearbeitung von Konfigurationsdateien zwischen Bereitstellungen hat aus folgenden Gründen keine Auswirkungen:</span><span class="sxs-lookup"><span data-stu-id="01bb5-139">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="01bb5-140">Benutzer verfügen über zwischengespeicherte Versionen der Dateien, die sie weiterhin verwenden können.</span><span class="sxs-lookup"><span data-stu-id="01bb5-140">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="01bb5-141">Die Dateien *service-worker.js* und *service-worker-assets.js* der PWA müssen bei der Kompilierung neu erstellt werden, wodurch der App beim nächsten Onlineaufruf des Benutzers signalisiert wird, dass die App neu bereitgestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="01bb5-141">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="01bb5-142">Weitere Informationen zur Verarbeitung von Updates im Hintergrund durch PWAs finden Sie unter <xref:blazor/progressive-web-app#background-updates>.</span><span class="sxs-lookup"><span data-stu-id="01bb5-142">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>
