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
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="368a1-103">Hostingmodellkonfiguration für ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="368a1-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="368a1-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="368a1-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="368a1-105">Dieser Artikel behandelt die Hostingmodellkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="368a1-105">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a><span data-ttu-id="368a1-106">Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="368a1-106">Blazor WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="368a1-107">Umgebung</span><span class="sxs-lookup"><span data-stu-id="368a1-107">Environment</span></span>

<span data-ttu-id="368a1-108">Wenn eine App lokal ausgeführt wird, wird Umgebung standardmäßig auf „Entwicklung“ festgelegt.</span><span class="sxs-lookup"><span data-stu-id="368a1-108">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="368a1-109">Wenn die App veröffentlicht wird, wird standardmäßig die Produktionsumgebung verwendet.</span><span class="sxs-lookup"><span data-stu-id="368a1-109">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="368a1-110">Eine gehostete Blazor WebAssembly-App übernimmt die Umgebung vom Server mittels einer Middleware, die die Umgebung dem Browser mitteilt, indem sie den `blazor-environment`-Header hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="368a1-110">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="368a1-111">Der Wert des Headers ist die Umgebung.</span><span class="sxs-lookup"><span data-stu-id="368a1-111">The value of the header is the environment.</span></span> <span data-ttu-id="368a1-112">Die gehostete Blazor-App und die Server-App verwenden gemeinsam dieselbe Umgebung.</span><span class="sxs-lookup"><span data-stu-id="368a1-112">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="368a1-113">Weitere Informationen, einschließlich Informationen zum Konfigurieren der Umgebung, finden Sie unter <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="368a1-113">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="368a1-114">Wenn eine eigenständige App lokal ausgeführt wird, fügt der Entwicklungsserver den `blazor-environment`-Header hinzu, um die Entwicklungsumgebung anzugeben.</span><span class="sxs-lookup"><span data-stu-id="368a1-114">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="368a1-115">Um die Umgebung für andere Hostingumgebungen anzugeben, fügen Sie den `blazor-environment`-Header hinzu.</span><span class="sxs-lookup"><span data-stu-id="368a1-115">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="368a1-116">Im folgenden Beispiel für IIS fügen Sie den benutzerdefinierten Header zu der veröffentlichten Datei *web.config* hinzu.</span><span class="sxs-lookup"><span data-stu-id="368a1-116">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="368a1-117">Die Datei *web.config* befindet sich im Ordner */bin/Release/{ZIELFRAMEWORK}/publish*:</span><span class="sxs-lookup"><span data-stu-id="368a1-117">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

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
> <span data-ttu-id="368a1-118">Informationen zum Verwenden einer benutzerdefinierten Datei *web.config* für IIS, die nicht überschrieben wird, wenn die App im Ordner *publish* veröffentlicht wird, finden Sie unter <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="368a1-118">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="368a1-119">Rufen Sie die Umgebung der App in einer Komponente ab, indem Sie `IWebAssemblyHostEnvironment` einfügen und die `Environment`-Eigenschaft lesen:</span><span class="sxs-lookup"><span data-stu-id="368a1-119">Obtain the app's environment in a component by injecting `IWebAssemblyHostEnvironment` and reading the `Environment` property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="368a1-120">Während des Starts macht `WebAssemblyHostBuilder` die `IWebAssemblyHostEnvironment` über die `HostEnvironment`-Eigenschaft verfügbar, was es Entwicklern ermöglicht, umgebungsspezifische Logik in ihrem Code zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="368a1-120">During startup, the `WebAssemblyHostBuilder` exposes the `IWebAssemblyHostEnvironment` through the `HostEnvironment` property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="368a1-121">Die folgenden bequemen Erweiterungsmethoden ermöglichen die Überprüfung der aktuellen Umgebung auf die Namen „Entwicklung“, „Produktion“, „Staging“ sowie benutzerdefinierte Umgebungsnamen:</span><span class="sxs-lookup"><span data-stu-id="368a1-121">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* <span data-ttu-id="368a1-122">\`IsEnvironment("{UMGEBUNGSNAME}")</span><span class="sxs-lookup"><span data-stu-id="368a1-122">\`IsEnvironment("{ENVIRONMENT NAME}")</span></span>

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

<span data-ttu-id="368a1-123">Die `IWebAssemblyHostEnvironment.BaseAddress`-Eigenschaft kann während des Starts verwendet werden, wenn der `NavigationManager`-Dienst nicht verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="368a1-123">The `IWebAssemblyHostEnvironment.BaseAddress` property can be used during startup when the `NavigationManager` service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="368a1-124">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="368a1-124">Configuration</span></span>

<span data-ttu-id="368a1-125">Blazor WebAssembly unterstützt die Konfiguration über:</span><span class="sxs-lookup"><span data-stu-id="368a1-125">Blazor WebAssembly supports configuration from:</span></span>

* <span data-ttu-id="368a1-126">Den [Dateikonfigurationsanbieter](xref:fundamentals/configuration/index#file-configuration-provider) für App-Einstellungsdateien standardmäßig aus:</span><span class="sxs-lookup"><span data-stu-id="368a1-126">The [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) for app settings files by default:</span></span>
  * <span data-ttu-id="368a1-127">*wwwroot/appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="368a1-127">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="368a1-128">*wwwroot/appsettings.{UMGEBUNG}.json*</span><span class="sxs-lookup"><span data-stu-id="368a1-128">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="368a1-129">Andere [Konfigurationsanbieter](xref:fundamentals/configuration/index), die von der App registriert werden.</span><span class="sxs-lookup"><span data-stu-id="368a1-129">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span>

> [!WARNING]
> <span data-ttu-id="368a1-130">Die Konfiguration ist für Benutzer in einer Blazor WebAssembly-App sichtbar.</span><span class="sxs-lookup"><span data-stu-id="368a1-130">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="368a1-131">**Speichern Sie keine App-Geheimnisse oder -Anmeldeinformationen in der Konfiguration.**</span><span class="sxs-lookup"><span data-stu-id="368a1-131">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="368a1-132">Weitere Informationen zur Konfigurationsanbietern finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="368a1-132">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="368a1-133">Konfiguration von App-Einstellungen</span><span class="sxs-lookup"><span data-stu-id="368a1-133">App settings configuration</span></span>

<span data-ttu-id="368a1-134">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="368a1-134">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="368a1-135">Fügen Sie eine <xref:Microsoft.Extensions.Configuration.IConfiguration>-Instanz in eine Komponente ein, um auf die Konfigurationsdaten zuzugreifen:</span><span class="sxs-lookup"><span data-stu-id="368a1-135">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="368a1-136">Anbieterkonfiguration</span><span class="sxs-lookup"><span data-stu-id="368a1-136">Provider configuration</span></span>

<span data-ttu-id="368a1-137">Im folgenden Beispiel werden ein <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource>-Element und der [Dateikonfigurationsanbieter](xref:fundamentals/configuration/index#file-configuration-provider) verwendet, um zusätzliche Konfiguration bereitzustellen:</span><span class="sxs-lookup"><span data-stu-id="368a1-137">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> and the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) to supply additional configuration:</span></span>

<span data-ttu-id="368a1-138">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="368a1-138">`Program.Main`:</span></span>

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

<span data-ttu-id="368a1-139">Fügen Sie eine <xref:Microsoft.Extensions.Configuration.IConfiguration>-Instanz in eine Komponente ein, um auf die Konfigurationsdaten zuzugreifen:</span><span class="sxs-lookup"><span data-stu-id="368a1-139">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

#### <a name="authentication-configuration"></a><span data-ttu-id="368a1-140">Authentifizierungskonfiguration</span><span class="sxs-lookup"><span data-stu-id="368a1-140">Authentication configuration</span></span>

<span data-ttu-id="368a1-141">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="368a1-141">*wwwroot/appsettings.json*:</span></span>

```json
{
  "AzureAD": {
    "Authority": "https://login.microsoftonline.com/",
    "ClientId": "aeaebf0f-d416-4d92-a08f-e1d5b51fc494"
  }
}
```

<span data-ttu-id="368a1-142">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="368a1-142">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("AzureAD", options);
```

#### <a name="logging-configuration"></a><span data-ttu-id="368a1-143">Konfiguration der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="368a1-143">Logging configuration</span></span>

<span data-ttu-id="368a1-144">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="368a1-144">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="368a1-145">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="368a1-145">`Program.Main`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="368a1-146">Konfiguration des Host-Generators</span><span class="sxs-lookup"><span data-stu-id="368a1-146">Host builder configuration</span></span>

<span data-ttu-id="368a1-147">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="368a1-147">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="368a1-148">Zwischengespeicherte Konfiguration</span><span class="sxs-lookup"><span data-stu-id="368a1-148">Cached configuration</span></span>

<span data-ttu-id="368a1-149">Konfigurationsdateien werden zur Offlineverwendung zwischengespeichert.</span><span class="sxs-lookup"><span data-stu-id="368a1-149">Configuration files are cached for offline use.</span></span> <span data-ttu-id="368a1-150">Bei [progressiven Web-Apps (PWAs)](xref:blazor/progressive-web-app) können Sie Konfigurationsdateien nur beim Erstellen einer neuen Bereitstellung aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="368a1-150">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="368a1-151">Die Bearbeitung von Konfigurationsdateien zwischen Bereitstellungen hat aus folgenden Gründen keine Auswirkungen:</span><span class="sxs-lookup"><span data-stu-id="368a1-151">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="368a1-152">Benutzer verfügen über zwischengespeicherte Versionen der Dateien, die sie weiterhin verwenden können.</span><span class="sxs-lookup"><span data-stu-id="368a1-152">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="368a1-153">Die Dateien *service-worker.js* und *service-worker-assets.js* der PWA müssen bei der Kompilierung neu erstellt werden, wodurch der App beim nächsten Onlineaufruf des Benutzers signalisiert wird, dass die App neu bereitgestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="368a1-153">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="368a1-154">Weitere Informationen zur Verarbeitung von Updates im Hintergrund durch PWAs finden Sie unter <xref:blazor/progressive-web-app#background-updates>.</span><span class="sxs-lookup"><span data-stu-id="368a1-154">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="368a1-155">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="368a1-155">Logging</span></span>

<span data-ttu-id="368a1-156">Informationen zur Unterstützung der Blazor WebAssembly-Protokollierung finden Sie unter <xref:fundamentals/logging/index#create-logs-in-blazor>.</span><span class="sxs-lookup"><span data-stu-id="368a1-156">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="blazor-server"></a><span data-ttu-id="368a1-157">Blazor Server</span><span class="sxs-lookup"><span data-stu-id="368a1-157">Blazor Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="368a1-158">Reflektieren des Verbindungszustands auf der Benutzeroberfläche</span><span class="sxs-lookup"><span data-stu-id="368a1-158">Reflect the connection state in the UI</span></span>

<span data-ttu-id="368a1-159">Wenn der Client erkennt, dass keine Verbindung mehr besteht, wird dem Benutzer eine Standardbenutzeroberfläche angezeigt, während der Client versucht, eine neue Verbindung herzustellen.</span><span class="sxs-lookup"><span data-stu-id="368a1-159">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="368a1-160">Wenn die Wiederherstellung der Verbindung fehlschlägt, wird dem Benutzer die Option angezeigt, es noch mal zu versuchen.</span><span class="sxs-lookup"><span data-stu-id="368a1-160">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="368a1-161">Wenn Sie die Benutzeroberfläche anpassen möchten, definieren Sie ein Element mit einer `id` von `components-reconnect-modal` im `<body>` der Razor-Seite *_Host.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="368a1-161">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="368a1-162">In der folgenden Tabelle werden die CSS-Klassen beschrieben, die auf das `components-reconnect-modal`-Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="368a1-162">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="368a1-163">CSS-Klasse</span><span class="sxs-lookup"><span data-stu-id="368a1-163">CSS class</span></span>                       | <span data-ttu-id="368a1-164">Steht für&hellip;</span><span class="sxs-lookup"><span data-stu-id="368a1-164">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="368a1-165">Die Verbindung wurde getrennt.</span><span class="sxs-lookup"><span data-stu-id="368a1-165">A lost connection.</span></span> <span data-ttu-id="368a1-166">Der Client versucht, die Verbindung wiederherzustellen.</span><span class="sxs-lookup"><span data-stu-id="368a1-166">The client is attempting to reconnect.</span></span> <span data-ttu-id="368a1-167">Die modale Seite wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="368a1-167">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="368a1-168">Auf dem Server wird eine aktive Verbindung wiederhergestellt.</span><span class="sxs-lookup"><span data-stu-id="368a1-168">An active connection is re-established to the server.</span></span> <span data-ttu-id="368a1-169">Die modale Seite wird ausgeblendet.</span><span class="sxs-lookup"><span data-stu-id="368a1-169">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="368a1-170">Die Wiederherstellung der Verbindung ist wahrscheinlich aufgrund eines Netzwerkfehlers fehlgeschlagen.</span><span class="sxs-lookup"><span data-stu-id="368a1-170">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="368a1-171">Rufen Sie `window.Blazor.reconnect()` auf, um einen neuen Verbindungsversuch zu unternehmen.</span><span class="sxs-lookup"><span data-stu-id="368a1-171">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="368a1-172">Die Wiederherstellung der Verbindung wurde abgelehnt.</span><span class="sxs-lookup"><span data-stu-id="368a1-172">Reconnection rejected.</span></span> <span data-ttu-id="368a1-173">Der Server wurde zwar erreicht, jedoch hat dieser die Verbindung verweigert. Der Status des Benutzers auf dem Server wurde verworfen.</span><span class="sxs-lookup"><span data-stu-id="368a1-173">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="368a1-174">Rufen Sie `location.reload()` auf, um die App neu zu laden.</span><span class="sxs-lookup"><span data-stu-id="368a1-174">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="368a1-175">Dieser Verbindungszustand kann aus folgenden Gründen auftreten:</span><span class="sxs-lookup"><span data-stu-id="368a1-175">This connection state may result when:</span></span><ul><li><span data-ttu-id="368a1-176">Aufgetretener Absturz auf der serverseitigen Verbindung.</span><span class="sxs-lookup"><span data-stu-id="368a1-176">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="368a1-177">Der Client war lange nicht verbunden, sodass der Server den Benutzerstatus verworfen hat.</span><span class="sxs-lookup"><span data-stu-id="368a1-177">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="368a1-178">Komponenteninstanzen, mit denen der Benutzer interagiert, werden verworfen.</span><span class="sxs-lookup"><span data-stu-id="368a1-178">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="368a1-179">Der Server wird neu gestartet, oder der Workerprozess der App wird wiederverwendet.</span><span class="sxs-lookup"><span data-stu-id="368a1-179">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="368a1-180">Rendermodus</span><span class="sxs-lookup"><span data-stu-id="368a1-180">Render mode</span></span>

<span data-ttu-id="368a1-181">Blazor Server-Apps werden standardmäßig eingerichtet, um die Benutzeroberfläche auf dem Server schon vor der Einrichtung der Clientverbindung auf dem Server zu rendern.</span><span class="sxs-lookup"><span data-stu-id="368a1-181">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="368a1-182">Sehen Sie sich die Einrichtung der Razor-Seite *_Host.cshtml* an:</span><span class="sxs-lookup"><span data-stu-id="368a1-182">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="368a1-183">`RenderMode` konfiguriert folgende Einstellungen für die Komponente:</span><span class="sxs-lookup"><span data-stu-id="368a1-183">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="368a1-184">Ob die Komponente zuvor für die Seite gerendert wird</span><span class="sxs-lookup"><span data-stu-id="368a1-184">Is prerendered into the page.</span></span>
* <span data-ttu-id="368a1-185">Ob die Komponente als statische HTML auf der Seite gerendert wird oder ob sie die nötigen Informationen für das Bootstrapping einer Blazor-App über den Benutzer-Agent enthält</span><span class="sxs-lookup"><span data-stu-id="368a1-185">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="368a1-186">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="368a1-186">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="368a1-187">Rendert die Komponente in statische HTML und fügt einen Marker für eine Blazor Server-App hinzu.</span><span class="sxs-lookup"><span data-stu-id="368a1-187">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="368a1-188">Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer Blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="368a1-188">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="368a1-189">Rendert einen Marker für eine Blazor Server-App.</span><span class="sxs-lookup"><span data-stu-id="368a1-189">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="368a1-190">Die Ausgabe der Komponente ist nicht enthalten.</span><span class="sxs-lookup"><span data-stu-id="368a1-190">Output from the component isn't included.</span></span> <span data-ttu-id="368a1-191">Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer Blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="368a1-191">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="368a1-192">Rendert die Komponente in statischen HTML-Code.</span><span class="sxs-lookup"><span data-stu-id="368a1-192">Renders the component into static HTML.</span></span> |

<span data-ttu-id="368a1-193">Das Rendern von Serverkomponenten über eine statische HTML-Seite wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="368a1-193">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="368a1-194">Konfigurieren des SignalR-Clients für Blazor Server-Apps</span><span class="sxs-lookup"><span data-stu-id="368a1-194">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="368a1-195">In einigen Fällen müssen Sie den von den SignalR Server-Apps verwendeten Blazor-Client konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="368a1-195">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="368a1-196">Beispielsweise können Sie die Protokollierung auf dem SignalR-Client konfigurieren, um ein Verbindungsproblem zu diagnostizieren.</span><span class="sxs-lookup"><span data-stu-id="368a1-196">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="368a1-197">So konfigurieren Sie den SignalR-Client in der Datei *Pages/_Host.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="368a1-197">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="368a1-198">Fügen Sie ein `autostart="false"`-Attribut zum `<script>`-Tag für das `blazor.server.js`-Skript hinzu.</span><span class="sxs-lookup"><span data-stu-id="368a1-198">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="368a1-199">Rufen Sie `Blazor.start` auf, und übergeben ein Konfigurationsobjekt, das den SignalR-Builder angibt.</span><span class="sxs-lookup"><span data-stu-id="368a1-199">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

### <a name="logging"></a><span data-ttu-id="368a1-200">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="368a1-200">Logging</span></span>

<span data-ttu-id="368a1-201">Weitere Informationen zur Unterstützung von Blazor-Serverprotokollierung finden Sie unter <xref:fundamentals/logging/index#create-logs-in-blazor>.</span><span class="sxs-lookup"><span data-stu-id="368a1-201">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
