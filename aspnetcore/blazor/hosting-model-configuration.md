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
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="51f4f-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="51f4f-103">'Blazor'</span></span>

<span data-ttu-id="51f4f-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="51f4f-104">'Identity'</span></span>

<span data-ttu-id="51f4f-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="51f4f-105">'Let's Encrypt'</span></span>

## <a name="blazor-webassembly"></a><span data-ttu-id="51f4f-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="51f4f-106">'Razor'</span></span>

### <a name="environment"></a><span data-ttu-id="51f4f-107">'SignalR' uid: blazor/hosting-model-configuration</span><span class="sxs-lookup"><span data-stu-id="51f4f-107">'SignalR' uid: blazor/hosting-model-configuration</span></span>

<span data-ttu-id="51f4f-108">Hostingmodellkonfiguration für ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="51f4f-108">ASP.NET Core Blazor hosting model configuration</span></span> <span data-ttu-id="51f4f-109">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="51f4f-109">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="51f4f-110">Dieser Artikel behandelt die Hostingmodellkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="51f4f-110">This article covers hosting model configuration.</span></span> Blazor<span data-ttu-id="51f4f-111"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="51f4f-111"> WebAssembly</span></span> <span data-ttu-id="51f4f-112">Umgebung</span><span class="sxs-lookup"><span data-stu-id="51f4f-112">Environment</span></span> <span data-ttu-id="51f4f-113">Wenn eine App lokal ausgeführt wird, wird Umgebung standardmäßig auf „Entwicklung“ festgelegt.</span><span class="sxs-lookup"><span data-stu-id="51f4f-113">When running an app locally, the environment defaults to Development.</span></span>

<span data-ttu-id="51f4f-114">Wenn die App veröffentlicht wird, wird standardmäßig die Produktionsumgebung verwendet.</span><span class="sxs-lookup"><span data-stu-id="51f4f-114">When the app is published, the environment defaults to Production.</span></span> <span data-ttu-id="51f4f-115">Eine gehostete Blazor WebAssembly-App übernimmt die Umgebung vom Server mittels einer Middleware, die die Umgebung dem Browser mitteilt, indem sie den `blazor-environment`-Header hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="51f4f-115">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span>

<span data-ttu-id="51f4f-116">Der Wert des Headers ist die Umgebung.</span><span class="sxs-lookup"><span data-stu-id="51f4f-116">The value of the header is the environment.</span></span> <span data-ttu-id="51f4f-117">Die gehostete Blazor-App und die Server-App verwenden gemeinsam dieselbe Umgebung.</span><span class="sxs-lookup"><span data-stu-id="51f4f-117">The hosted Blazor app and the server app share the same environment.</span></span>

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
> <span data-ttu-id="51f4f-118">Weitere Informationen, einschließlich Informationen zum Konfigurieren der Umgebung, finden Sie unter <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="51f4f-118">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="51f4f-119">Wenn eine eigenständige App lokal ausgeführt wird, fügt der Entwicklungsserver den `blazor-environment`-Header hinzu, um die Entwicklungsumgebung anzugeben.</span><span class="sxs-lookup"><span data-stu-id="51f4f-119">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="51f4f-120">Um die Umgebung für andere Hostingumgebungen anzugeben, fügen Sie den `blazor-environment`-Header hinzu.</span><span class="sxs-lookup"><span data-stu-id="51f4f-120">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="51f4f-121">Im folgenden Beispiel für IIS fügen Sie den benutzerdefinierten Header zu der veröffentlichten Datei *web.config* hinzu.</span><span class="sxs-lookup"><span data-stu-id="51f4f-121">In the following example for IIS, add the custom header to the published *web.config* file.</span></span>

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

<span data-ttu-id="51f4f-122">Die Datei *web.config* befindet sich im Ordner */bin/Release/{ZIELFRAMEWORK}/publish*:</span><span class="sxs-lookup"><span data-stu-id="51f4f-122">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

### <a name="configuration"></a><span data-ttu-id="51f4f-123">Informationen zum Verwenden einer benutzerdefinierten Datei *web.config* für IIS, die nicht überschrieben wird, wenn die App im Ordner *publish* veröffentlicht wird, finden Sie unter <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="51f4f-123">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="51f4f-124">Rufen Sie die Umgebung der App in einer Komponente ab, indem Sie <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> einfügen und die <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment>-Eigenschaft lesen:</span><span class="sxs-lookup"><span data-stu-id="51f4f-124">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property:</span></span>

* <span data-ttu-id="51f4f-125">Während des Starts macht <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> die <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> über die <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment>-Eigenschaft verfügbar, was es Entwicklern ermöglicht, umgebungsspezifische Logik in ihrem Code zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="51f4f-125">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables developers to have environment-specific logic in their code:</span></span>
  * <span data-ttu-id="51f4f-126">Die folgenden bequemen Erweiterungsmethoden ermöglichen die Überprüfung der aktuellen Umgebung auf die Namen „Entwicklung“, „Produktion“, „Staging“ sowie benutzerdefinierte Umgebungsnamen:</span><span class="sxs-lookup"><span data-stu-id="51f4f-126">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>
  * <span data-ttu-id="51f4f-127">Die <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType>-Eigenschaft kann während des Starts verwendet werden, wenn der <xref:Microsoft.AspNetCore.Components.NavigationManager>-Dienst nicht verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="51f4f-127">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>
* <span data-ttu-id="51f4f-128">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="51f4f-128">Configuration</span></span> Blazor<span data-ttu-id="51f4f-129"> WebAssembly lädt die Konfiguration von:</span><span class="sxs-lookup"><span data-stu-id="51f4f-129"> WebAssembly loads configuration from:</span></span> <span data-ttu-id="51f4f-130">Standardmäßig aus den App-Einstellungsdateien:</span><span class="sxs-lookup"><span data-stu-id="51f4f-130">App settings files by default:</span></span>

> [!WARNING]
> <span data-ttu-id="51f4f-131">*wwwroot/appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="51f4f-131">*wwwroot/appsettings.json*</span></span> <span data-ttu-id="51f4f-132">*wwwroot/appsettings.{UMGEBUNG}.json*</span><span class="sxs-lookup"><span data-stu-id="51f4f-132">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>

<span data-ttu-id="51f4f-133">Andere [Konfigurationsanbieter](xref:fundamentals/configuration/index), die von der App registriert werden.</span><span class="sxs-lookup"><span data-stu-id="51f4f-133">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="51f4f-134">Nicht alle Anbieter sind für Blazor WebAssembly-Apps geeignet.</span><span class="sxs-lookup"><span data-stu-id="51f4f-134">Not all providers are appropriate for Blazor WebAssembly apps.</span></span>

<span data-ttu-id="51f4f-135">Welche Anbieter für Blazor WebAssembly geeignet sind, erfahren Sie unter [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134) (Konfigurationsanbieter für Blazor WebAssembly).</span><span class="sxs-lookup"><span data-stu-id="51f4f-135">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="51f4f-136">Die Konfiguration in einer Blazor WebAssembly-App ist für Benutzer sichtbar.</span><span class="sxs-lookup"><span data-stu-id="51f4f-136">Configuration in a Blazor WebAssembly app is visible to users.</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="51f4f-137">**Speichern Sie keine App-Geheimnisse oder -Anmeldeinformationen in der Konfiguration.**</span><span class="sxs-lookup"><span data-stu-id="51f4f-137">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="51f4f-138">Weitere Informationen zur Konfigurationsanbietern finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="51f4f-138">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="51f4f-139">Konfiguration von App-Einstellungen</span><span class="sxs-lookup"><span data-stu-id="51f4f-139">App settings configuration</span></span>

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

<span data-ttu-id="51f4f-140">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="51f4f-140">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="51f4f-141">Fügen Sie eine <xref:Microsoft.Extensions.Configuration.IConfiguration>-Instanz in eine Komponente ein, um auf die Konfigurationsdaten zuzugreifen:</span><span class="sxs-lookup"><span data-stu-id="51f4f-141">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span> <span data-ttu-id="51f4f-142">Anbieterkonfiguration</span><span class="sxs-lookup"><span data-stu-id="51f4f-142">Provider configuration</span></span>

<span data-ttu-id="51f4f-143">Im folgenden Beispiel wird ein <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource>-Element verwendet, um zusätzliche Konfiguration bereitzustellen:</span><span class="sxs-lookup"><span data-stu-id="51f4f-143">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="51f4f-144">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="51f4f-144">`Program.Main`:</span></span>

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

#### <a name="authentication-configuration"></a><span data-ttu-id="51f4f-145">Fügen Sie eine <xref:Microsoft.Extensions.Configuration.IConfiguration>-Instanz in eine Komponente ein, um auf die Konfigurationsdaten zuzugreifen:</span><span class="sxs-lookup"><span data-stu-id="51f4f-145">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

<span data-ttu-id="51f4f-146">Um andere Konfigurationsdateien aus dem Ordner *wwwwroot* in die Konfiguration einzulesen, verwenden Sie einen <xref:System.Net.Http.HttpClient>, um den Inhalt der Datei abzurufen.</span><span class="sxs-lookup"><span data-stu-id="51f4f-146">To read other configuration files from the *wwwroot* folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="51f4f-147">Bei diesem Ansatz kann die vorhandene <xref:System.Net.Http.HttpClient>-Dienstregistrierung den zum Lesen der Datei erstellten lokalen Client verwenden, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="51f4f-147">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

#### <a name="logging-configuration"></a><span data-ttu-id="51f4f-148">*wwwroot/cars.json*:</span><span class="sxs-lookup"><span data-stu-id="51f4f-148">*wwwroot/cars.json*:</span></span>

<span data-ttu-id="51f4f-149">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="51f4f-149">`Program.Main`:</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="51f4f-150">Authentifizierungskonfiguration</span><span class="sxs-lookup"><span data-stu-id="51f4f-150">Authentication configuration</span></span>

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

<span data-ttu-id="51f4f-151">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="51f4f-151">*wwwroot/appsettings.json*:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="51f4f-152">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="51f4f-152">`Program.Main`:</span></span>

<span data-ttu-id="51f4f-153">Konfiguration der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="51f4f-153">Logging configuration</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="51f4f-154">Fügen Sie einen Paketverweis für [Microsoft.Extensions.Logging.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/) hinzu:</span><span class="sxs-lookup"><span data-stu-id="51f4f-154">Add a package reference for [Microsoft.Extensions.Logging.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span></span>

<span data-ttu-id="51f4f-155">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="51f4f-155">*wwwroot/appsettings.json*:</span></span> <span data-ttu-id="51f4f-156">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="51f4f-156">`Program.Main`:</span></span> <span data-ttu-id="51f4f-157">Konfiguration des Host-Generators</span><span class="sxs-lookup"><span data-stu-id="51f4f-157">Host builder configuration</span></span>

* <span data-ttu-id="51f4f-158">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="51f4f-158">`Program.Main`:</span></span>
* <span data-ttu-id="51f4f-159">Zwischengespeicherte Konfiguration</span><span class="sxs-lookup"><span data-stu-id="51f4f-159">Cached configuration</span></span>

<span data-ttu-id="51f4f-160">Konfigurationsdateien werden zur Offlineverwendung zwischengespeichert.</span><span class="sxs-lookup"><span data-stu-id="51f4f-160">Configuration files are cached for offline use.</span></span>

### <a name="logging"></a><span data-ttu-id="51f4f-161">Bei [progressiven Web-Apps (PWAs)](xref:blazor/progressive-web-app) können Sie Konfigurationsdateien nur beim Erstellen einer neuen Bereitstellung aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="51f4f-161">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span>

<span data-ttu-id="51f4f-162">Die Bearbeitung von Konfigurationsdateien zwischen Bereitstellungen hat aus folgenden Gründen keine Auswirkungen:</span><span class="sxs-lookup"><span data-stu-id="51f4f-162">Editing configuration files between deployments has no effect because:</span></span>

## <a name="blazor-server"></a><span data-ttu-id="51f4f-163">Benutzer verfügen über zwischengespeicherte Versionen der Dateien, die sie weiterhin verwenden können.</span><span class="sxs-lookup"><span data-stu-id="51f4f-163">Users have cached versions of the files that they continue to use.</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="51f4f-164">Die Dateien *service-worker.js* und *service-worker-assets.js* der PWA müssen bei der Kompilierung neu erstellt werden, wodurch der App beim nächsten Onlineaufruf des Benutzers signalisiert wird, dass die App neu bereitgestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="51f4f-164">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="51f4f-165">Weitere Informationen zur Verarbeitung von Updates im Hintergrund durch PWAs finden Sie unter <xref:blazor/progressive-web-app#background-updates>.</span><span class="sxs-lookup"><span data-stu-id="51f4f-165">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span> <span data-ttu-id="51f4f-166">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="51f4f-166">Logging</span></span>

<span data-ttu-id="51f4f-167">Informationen zur Unterstützung der Blazor WebAssembly-Protokollierung finden Sie unter <xref:fundamentals/logging/index#create-logs-in-blazor>.</span><span class="sxs-lookup"><span data-stu-id="51f4f-167">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

Blazor<span data-ttu-id="51f4f-168"> Server</span><span class="sxs-lookup"><span data-stu-id="51f4f-168"> Server</span></span>

| <span data-ttu-id="51f4f-169">Reflektieren des Verbindungszustands auf der Benutzeroberfläche</span><span class="sxs-lookup"><span data-stu-id="51f4f-169">Reflect the connection state in the UI</span></span>                       | <span data-ttu-id="51f4f-170">Wenn der Client erkennt, dass keine Verbindung mehr besteht, wird dem Benutzer eine Standardbenutzeroberfläche angezeigt, während der Client versucht, eine neue Verbindung herzustellen.</span><span class="sxs-lookup"><span data-stu-id="51f4f-170">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="51f4f-171">Wenn die Wiederherstellung der Verbindung fehlschlägt, wird dem Benutzer die Option angezeigt, es noch mal zu versuchen.</span><span class="sxs-lookup"><span data-stu-id="51f4f-171">If reconnection fails, the user is provided the option to retry.</span></span> <span data-ttu-id="51f4f-172">Wenn Sie die Benutzeroberfläche anpassen möchten, definieren Sie ein Element mit einer `id` von `components-reconnect-modal` im `<body>` der *_Host.cshtml*-Razor-Seite:</span><span class="sxs-lookup"><span data-stu-id="51f4f-172">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span> <span data-ttu-id="51f4f-173">In der folgenden Tabelle werden die CSS-Klassen beschrieben, die auf das `components-reconnect-modal`-Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="51f4f-173">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="51f4f-174">CSS-Klasse</span><span class="sxs-lookup"><span data-stu-id="51f4f-174">CSS class</span></span> <span data-ttu-id="51f4f-175">Steht für&hellip;</span><span class="sxs-lookup"><span data-stu-id="51f4f-175">Indicates&hellip;</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="51f4f-176">Die Verbindung wurde getrennt.</span><span class="sxs-lookup"><span data-stu-id="51f4f-176">A lost connection.</span></span> <span data-ttu-id="51f4f-177">Der Client versucht, die Verbindung wiederherzustellen.</span><span class="sxs-lookup"><span data-stu-id="51f4f-177">The client is attempting to reconnect.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="51f4f-178">Die modale Seite wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="51f4f-178">Show the modal.</span></span> <span data-ttu-id="51f4f-179">Auf dem Server wird eine aktive Verbindung wiederhergestellt.</span><span class="sxs-lookup"><span data-stu-id="51f4f-179">An active connection is re-established to the server.</span></span> <span data-ttu-id="51f4f-180">Die modale Seite wird ausgeblendet.</span><span class="sxs-lookup"><span data-stu-id="51f4f-180">Hide the modal.</span></span> <span data-ttu-id="51f4f-181">Die Wiederherstellung der Verbindung ist wahrscheinlich aufgrund eines Netzwerkfehlers fehlgeschlagen.</span><span class="sxs-lookup"><span data-stu-id="51f4f-181">Reconnection failed, probably due to a network failure.</span></span><ul><li><span data-ttu-id="51f4f-182">Rufen Sie `window.Blazor.reconnect()` auf, um einen neuen Verbindungsversuch zu unternehmen.</span><span class="sxs-lookup"><span data-stu-id="51f4f-182">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span></li><li><span data-ttu-id="51f4f-183">Die Wiederherstellung der Verbindung wurde abgelehnt.</span><span class="sxs-lookup"><span data-stu-id="51f4f-183">Reconnection rejected.</span></span> <span data-ttu-id="51f4f-184">Der Server wurde zwar erreicht, jedoch hat dieser die Verbindung verweigert. Der Status des Benutzers auf dem Server wurde verworfen.</span><span class="sxs-lookup"><span data-stu-id="51f4f-184">The server was reached but refused the connection, and the user's state on the server is lost.</span></span></li><li><span data-ttu-id="51f4f-185">Rufen Sie `location.reload()` auf, um die App neu zu laden.</span><span class="sxs-lookup"><span data-stu-id="51f4f-185">To reload the app, call `location.reload()`.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="51f4f-186">Dieser Verbindungszustand kann aus folgenden Gründen auftreten:</span><span class="sxs-lookup"><span data-stu-id="51f4f-186">This connection state may result when:</span></span>

<span data-ttu-id="51f4f-187">Aufgetretener Absturz auf der serverseitigen Verbindung.</span><span class="sxs-lookup"><span data-stu-id="51f4f-187">A crash in the server-side circuit occurs.</span></span> <span data-ttu-id="51f4f-188">Der Client war lange nicht verbunden, sodass der Server den Benutzerstatus verworfen hat.</span><span class="sxs-lookup"><span data-stu-id="51f4f-188">The client is disconnected long enough for the server to drop the user's state.</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="51f4f-189">Komponenteninstanzen, mit denen der Benutzer interagiert, werden verworfen.</span><span class="sxs-lookup"><span data-stu-id="51f4f-189">Instances of the components that the user is interacting with are disposed.</span></span>

* <span data-ttu-id="51f4f-190">Der Server wird neu gestartet, oder der Workerprozess der App wird wiederverwendet.</span><span class="sxs-lookup"><span data-stu-id="51f4f-190">The server is restarted, or the app's worker process is recycled.</span></span>
* <span data-ttu-id="51f4f-191">Rendermodus</span><span class="sxs-lookup"><span data-stu-id="51f4f-191">Render mode</span></span>

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | Blazor<span data-ttu-id="51f4f-192"> Server-Apps werden standardmäßig eingerichtet, um die Benutzeroberfläche auf dem Server schon vor der Einrichtung der Clientverbindung auf dem Server zu rendern.</span><span class="sxs-lookup"><span data-stu-id="51f4f-192"> Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="51f4f-193">Sehen Sie sich die Einrichtung der Razor-Seite *_Host.cshtml* an:</span><span class="sxs-lookup"><span data-stu-id="51f4f-193">This is set up in the *_Host.cshtml* Razor page:</span></span> <span data-ttu-id="51f4f-194"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> konfiguriert folgende Einstellungen für die Komponente:</span><span class="sxs-lookup"><span data-stu-id="51f4f-194"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="51f4f-195">Ob die Komponente zuvor für die Seite gerendert wird</span><span class="sxs-lookup"><span data-stu-id="51f4f-195">Is prerendered into the page.</span></span> <span data-ttu-id="51f4f-196">Ob die Komponente als statische HTML auf der Seite gerendert wird oder ob sie die nötigen Informationen für das Bootstrapping einer Blazor-App über den Benutzer-Agent enthält.</span><span class="sxs-lookup"><span data-stu-id="51f4f-196">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span> <span data-ttu-id="51f4f-197">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="51f4f-197">Description</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="51f4f-198">Rendert die Komponente in statische HTML und fügt einen Marker für eine Blazor Server-App hinzu.</span><span class="sxs-lookup"><span data-stu-id="51f4f-198">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> |

<span data-ttu-id="51f4f-199">Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer Blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="51f4f-199">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span>

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="51f4f-200">Rendert einen Marker für eine Blazor Server-App.</span><span class="sxs-lookup"><span data-stu-id="51f4f-200">Renders a marker for a Blazor Server app.</span></span>

<span data-ttu-id="51f4f-201">Die Ausgabe der Komponente ist nicht enthalten.</span><span class="sxs-lookup"><span data-stu-id="51f4f-201">Output from the component isn't included.</span></span> <span data-ttu-id="51f4f-202">Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer Blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="51f4f-202">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span>

<span data-ttu-id="51f4f-203">Rendert die Komponente in statischen HTML-Code.</span><span class="sxs-lookup"><span data-stu-id="51f4f-203">Renders the component into static HTML.</span></span>

* <span data-ttu-id="51f4f-204">Das Rendern von Serverkomponenten über eine statische HTML-Seite wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="51f4f-204">Rendering server components from a static HTML page isn't supported.</span></span>
* <span data-ttu-id="51f4f-205">Konfigurieren des SignalR-Clients für Blazor Server-Apps</span><span class="sxs-lookup"><span data-stu-id="51f4f-205">Configure the SignalR client for Blazor Server apps</span></span>

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

### <a name="logging"></a><span data-ttu-id="51f4f-206">In einigen Fällen müssen Sie den von den SignalR Server-Apps verwendeten Blazor-Client konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="51f4f-206">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span>

<span data-ttu-id="51f4f-207">Beispielsweise können Sie die Protokollierung auf dem SignalR-Client konfigurieren, um ein Verbindungsproblem zu diagnostizieren.</span><span class="sxs-lookup"><span data-stu-id="51f4f-207">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>
