---
<span data-ttu-id="2d52c-101">title: 'Blazor-Hostingmodellkonfiguration für ASP.NET Core' author: description: 'Hier erhalten Sie Informationen zur Blazor-Hostingmodellkonfiguration, einschließlich der Integration von Razor-Komponenten in Razor Pages- und MVC-Apps.'</span><span class="sxs-lookup"><span data-stu-id="2d52c-101">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="2d52c-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2d52c-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d52c-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d52c-103">'Blazor'</span></span>
- <span data-ttu-id="2d52c-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d52c-104">'Identity'</span></span>
- <span data-ttu-id="2d52c-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d52c-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d52c-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d52c-106">'Razor'</span></span>
- <span data-ttu-id="2d52c-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2d52c-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="2d52c-108">Hostingmodellkonfiguration für ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="2d52c-108">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="2d52c-109">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2d52c-109">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="2d52c-110">Dieser Artikel behandelt die Hostingmodellkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="2d52c-110">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="2d52c-111"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="2d52c-111"> WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="2d52c-112">Umgebung</span><span class="sxs-lookup"><span data-stu-id="2d52c-112">Environment</span></span>

<span data-ttu-id="2d52c-113">Wenn eine App lokal ausgeführt wird, wird Umgebung standardmäßig auf „Entwicklung“ festgelegt.</span><span class="sxs-lookup"><span data-stu-id="2d52c-113">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="2d52c-114">Wenn die App veröffentlicht wird, wird standardmäßig die Produktionsumgebung verwendet.</span><span class="sxs-lookup"><span data-stu-id="2d52c-114">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="2d52c-115">Eine gehostete Blazor WebAssembly-App übernimmt die Umgebung vom Server mittels einer Middleware, die die Umgebung dem Browser mitteilt, indem sie den `blazor-environment`-Header hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="2d52c-115">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="2d52c-116">Der Wert des Headers ist die Umgebung.</span><span class="sxs-lookup"><span data-stu-id="2d52c-116">The value of the header is the environment.</span></span> <span data-ttu-id="2d52c-117">Die gehostete Blazor-App und die Server-App verwenden gemeinsam dieselbe Umgebung.</span><span class="sxs-lookup"><span data-stu-id="2d52c-117">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="2d52c-118">Weitere Informationen, einschließlich Informationen zum Konfigurieren der Umgebung, finden Sie unter <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="2d52c-118">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="2d52c-119">Wenn eine eigenständige App lokal ausgeführt wird, fügt der Entwicklungsserver den `blazor-environment`-Header hinzu, um die Entwicklungsumgebung anzugeben.</span><span class="sxs-lookup"><span data-stu-id="2d52c-119">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="2d52c-120">Um die Umgebung für andere Hostingumgebungen anzugeben, fügen Sie den `blazor-environment`-Header hinzu.</span><span class="sxs-lookup"><span data-stu-id="2d52c-120">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="2d52c-121">Im folgenden Beispiel für IIS fügen Sie den benutzerdefinierten Header zu der veröffentlichten Datei *web.config* hinzu.</span><span class="sxs-lookup"><span data-stu-id="2d52c-121">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="2d52c-122">Die Datei *web.config* befindet sich im Ordner */bin/Release/{ZIELFRAMEWORK}/publish*:</span><span class="sxs-lookup"><span data-stu-id="2d52c-122">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

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
> <span data-ttu-id="2d52c-123">Informationen zum Verwenden einer benutzerdefinierten Datei *web.config* für IIS, die nicht überschrieben wird, wenn die App im Ordner *publish* veröffentlicht wird, finden Sie unter <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="2d52c-123">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="2d52c-124">Rufen Sie die Umgebung der App in einer Komponente ab, indem Sie <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> einfügen und die <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment>-Eigenschaft lesen:</span><span class="sxs-lookup"><span data-stu-id="2d52c-124">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="2d52c-125">Während des Starts macht <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> die <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> über die <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment>-Eigenschaft verfügbar, was es Entwicklern ermöglicht, umgebungsspezifische Logik in ihrem Code zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="2d52c-125">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="2d52c-126">Die folgenden bequemen Erweiterungsmethoden ermöglichen die Überprüfung der aktuellen Umgebung auf die Namen „Entwicklung“, „Produktion“, „Staging“ sowie benutzerdefinierte Umgebungsnamen:</span><span class="sxs-lookup"><span data-stu-id="2d52c-126">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

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

<span data-ttu-id="2d52c-127">Die <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType>-Eigenschaft kann während des Starts verwendet werden, wenn der <xref:Microsoft.AspNetCore.Components.NavigationManager>-Dienst nicht verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="2d52c-127">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="2d52c-128">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="2d52c-128">Configuration</span></span>

Blazor<span data-ttu-id="2d52c-129"> WebAssembly lädt die Konfiguration von:</span><span class="sxs-lookup"><span data-stu-id="2d52c-129"> WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="2d52c-130">Standardmäßig aus den App-Einstellungsdateien:</span><span class="sxs-lookup"><span data-stu-id="2d52c-130">App settings files by default:</span></span>
  * <span data-ttu-id="2d52c-131">*wwwroot/appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="2d52c-131">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="2d52c-132">*wwwroot/appsettings.{UMGEBUNG}.json*</span><span class="sxs-lookup"><span data-stu-id="2d52c-132">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="2d52c-133">Andere [Konfigurationsanbieter](xref:fundamentals/configuration/index), die von der App registriert werden.</span><span class="sxs-lookup"><span data-stu-id="2d52c-133">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="2d52c-134">Nicht alle Anbieter sind für Blazor WebAssembly-Apps geeignet.</span><span class="sxs-lookup"><span data-stu-id="2d52c-134">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="2d52c-135">Welche Anbieter für Blazor WebAssembly geeignet sind, erfahren Sie unter [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134) (Konfigurationsanbieter für Blazor WebAssembly).</span><span class="sxs-lookup"><span data-stu-id="2d52c-135">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="2d52c-136">Die Konfiguration in einer Blazor WebAssembly-App ist für Benutzer sichtbar.</span><span class="sxs-lookup"><span data-stu-id="2d52c-136">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="2d52c-137">**Speichern Sie keine App-Geheimnisse oder -Anmeldeinformationen in der Konfiguration.**</span><span class="sxs-lookup"><span data-stu-id="2d52c-137">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="2d52c-138">Weitere Informationen zur Konfigurationsanbietern finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="2d52c-138">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="2d52c-139">Konfiguration von App-Einstellungen</span><span class="sxs-lookup"><span data-stu-id="2d52c-139">App settings configuration</span></span>

<span data-ttu-id="2d52c-140">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="2d52c-140">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="2d52c-141">Fügen Sie eine <xref:Microsoft.Extensions.Configuration.IConfiguration>-Instanz in eine Komponente ein, um auf die Konfigurationsdaten zuzugreifen:</span><span class="sxs-lookup"><span data-stu-id="2d52c-141">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="2d52c-142">Anbieterkonfiguration</span><span class="sxs-lookup"><span data-stu-id="2d52c-142">Provider configuration</span></span>

<span data-ttu-id="2d52c-143">Im folgenden Beispiel wird ein <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource>-Element verwendet, um zusätzliche Konfiguration bereitzustellen:</span><span class="sxs-lookup"><span data-stu-id="2d52c-143">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="2d52c-144">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="2d52c-144">`Program.Main`:</span></span>

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

<span data-ttu-id="2d52c-145">Fügen Sie eine <xref:Microsoft.Extensions.Configuration.IConfiguration>-Instanz in eine Komponente ein, um auf die Konfigurationsdaten zuzugreifen:</span><span class="sxs-lookup"><span data-stu-id="2d52c-145">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="2d52c-146">Um andere Konfigurationsdateien aus dem Ordner *wwwwroot* in die Konfiguration einzulesen, verwenden Sie einen <xref:System.Net.Http.HttpClient>, um den Inhalt der Datei abzurufen.</span><span class="sxs-lookup"><span data-stu-id="2d52c-146">To read other configuration files from the *wwwroot* folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="2d52c-147">Bei diesem Ansatz kann die vorhandene <xref:System.Net.Http.HttpClient>-Dienstregistrierung den zum Lesen der Datei erstellten lokalen Client verwenden, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="2d52c-147">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="2d52c-148">*wwwroot/cars.json*:</span><span class="sxs-lookup"><span data-stu-id="2d52c-148">*wwwroot/cars.json*:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="2d52c-149">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="2d52c-149">`Program.Main`:</span></span>

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

#### <a name="authentication-configuration"></a><span data-ttu-id="2d52c-150">Authentifizierungskonfiguration</span><span class="sxs-lookup"><span data-stu-id="2d52c-150">Authentication configuration</span></span>

<span data-ttu-id="2d52c-151">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="2d52c-151">*wwwroot/appsettings.json*:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="2d52c-152">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="2d52c-152">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions);
```

#### <a name="logging-configuration"></a><span data-ttu-id="2d52c-153">Konfiguration der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="2d52c-153">Logging configuration</span></span>

<span data-ttu-id="2d52c-154">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="2d52c-154">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="2d52c-155">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="2d52c-155">`Program.Main`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="2d52c-156">Konfiguration des Host-Generators</span><span class="sxs-lookup"><span data-stu-id="2d52c-156">Host builder configuration</span></span>

<span data-ttu-id="2d52c-157">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="2d52c-157">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="2d52c-158">Zwischengespeicherte Konfiguration</span><span class="sxs-lookup"><span data-stu-id="2d52c-158">Cached configuration</span></span>

<span data-ttu-id="2d52c-159">Konfigurationsdateien werden zur Offlineverwendung zwischengespeichert.</span><span class="sxs-lookup"><span data-stu-id="2d52c-159">Configuration files are cached for offline use.</span></span> <span data-ttu-id="2d52c-160">Bei [progressiven Web-Apps (PWAs)](xref:blazor/progressive-web-app) können Sie Konfigurationsdateien nur beim Erstellen einer neuen Bereitstellung aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="2d52c-160">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="2d52c-161">Die Bearbeitung von Konfigurationsdateien zwischen Bereitstellungen hat aus folgenden Gründen keine Auswirkungen:</span><span class="sxs-lookup"><span data-stu-id="2d52c-161">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="2d52c-162">Benutzer verfügen über zwischengespeicherte Versionen der Dateien, die sie weiterhin verwenden können.</span><span class="sxs-lookup"><span data-stu-id="2d52c-162">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="2d52c-163">Die Dateien *service-worker.js* und *service-worker-assets.js* der PWA müssen bei der Kompilierung neu erstellt werden, wodurch der App beim nächsten Onlineaufruf des Benutzers signalisiert wird, dass die App neu bereitgestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="2d52c-163">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="2d52c-164">Weitere Informationen zur Verarbeitung von Updates im Hintergrund durch PWAs finden Sie unter <xref:blazor/progressive-web-app#background-updates>.</span><span class="sxs-lookup"><span data-stu-id="2d52c-164">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="2d52c-165">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="2d52c-165">Logging</span></span>

<span data-ttu-id="2d52c-166">Informationen zur Unterstützung der Blazor WebAssembly-Protokollierung finden Sie unter <xref:fundamentals/logging/index#create-logs-in-blazor>.</span><span class="sxs-lookup"><span data-stu-id="2d52c-166">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="blazor-server"></a>Blazor<span data-ttu-id="2d52c-167"> Server</span><span class="sxs-lookup"><span data-stu-id="2d52c-167"> Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="2d52c-168">Reflektieren des Verbindungszustands auf der Benutzeroberfläche</span><span class="sxs-lookup"><span data-stu-id="2d52c-168">Reflect the connection state in the UI</span></span>

<span data-ttu-id="2d52c-169">Wenn der Client erkennt, dass keine Verbindung mehr besteht, wird dem Benutzer eine Standardbenutzeroberfläche angezeigt, während der Client versucht, eine neue Verbindung herzustellen.</span><span class="sxs-lookup"><span data-stu-id="2d52c-169">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="2d52c-170">Wenn die Wiederherstellung der Verbindung fehlschlägt, wird dem Benutzer die Option angezeigt, es noch mal zu versuchen.</span><span class="sxs-lookup"><span data-stu-id="2d52c-170">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="2d52c-171">Wenn Sie die Benutzeroberfläche anpassen möchten, definieren Sie ein Element mit einer `id` von `components-reconnect-modal` im `<body>` der *_Host.cshtml*-Razor-Seite:</span><span class="sxs-lookup"><span data-stu-id="2d52c-171">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="2d52c-172">In der folgenden Tabelle werden die CSS-Klassen beschrieben, die auf das `components-reconnect-modal`-Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="2d52c-172">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="2d52c-173">CSS-Klasse</span><span class="sxs-lookup"><span data-stu-id="2d52c-173">CSS class</span></span>                       | <span data-ttu-id="2d52c-174">Steht für&hellip;</span><span class="sxs-lookup"><span data-stu-id="2d52c-174">Indicates&hellip;</span></span> |
| ---
<span data-ttu-id="2d52c-175">title: 'Blazor-Hostingmodellkonfiguration für ASP.NET Core' author: description: 'Hier erhalten Sie Informationen zur Blazor-Hostingmodellkonfiguration, einschließlich der Integration von Razor-Komponenten in Razor Pages- und MVC-Apps.'</span><span class="sxs-lookup"><span data-stu-id="2d52c-175">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="2d52c-176">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2d52c-176">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d52c-177">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d52c-177">'Blazor'</span></span>
- <span data-ttu-id="2d52c-178">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d52c-178">'Identity'</span></span>
- <span data-ttu-id="2d52c-179">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d52c-179">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d52c-180">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d52c-180">'Razor'</span></span>
- <span data-ttu-id="2d52c-181">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2d52c-181">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d52c-182">title: 'Blazor-Hostingmodellkonfiguration für ASP.NET Core' author: description: 'Hier erhalten Sie Informationen zur Blazor-Hostingmodellkonfiguration, einschließlich der Integration von Razor-Komponenten in Razor Pages- und MVC-Apps.'</span><span class="sxs-lookup"><span data-stu-id="2d52c-182">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="2d52c-183">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2d52c-183">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d52c-184">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d52c-184">'Blazor'</span></span>
- <span data-ttu-id="2d52c-185">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d52c-185">'Identity'</span></span>
- <span data-ttu-id="2d52c-186">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d52c-186">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d52c-187">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d52c-187">'Razor'</span></span>
- <span data-ttu-id="2d52c-188">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2d52c-188">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d52c-189">title: 'Blazor-Hostingmodellkonfiguration für ASP.NET Core' author: description: 'Hier erhalten Sie Informationen zur Blazor-Hostingmodellkonfiguration, einschließlich der Integration von Razor-Komponenten in Razor Pages- und MVC-Apps.'</span><span class="sxs-lookup"><span data-stu-id="2d52c-189">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="2d52c-190">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2d52c-190">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d52c-191">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d52c-191">'Blazor'</span></span>
- <span data-ttu-id="2d52c-192">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d52c-192">'Identity'</span></span>
- <span data-ttu-id="2d52c-193">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d52c-193">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d52c-194">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d52c-194">'Razor'</span></span>
- <span data-ttu-id="2d52c-195">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2d52c-195">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d52c-196">title: 'Blazor-Hostingmodellkonfiguration für ASP.NET Core' author: description: 'Hier erhalten Sie Informationen zur Blazor-Hostingmodellkonfiguration, einschließlich der Integration von Razor-Komponenten in Razor Pages- und MVC-Apps.'</span><span class="sxs-lookup"><span data-stu-id="2d52c-196">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="2d52c-197">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2d52c-197">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d52c-198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d52c-198">'Blazor'</span></span>
- <span data-ttu-id="2d52c-199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d52c-199">'Identity'</span></span>
- <span data-ttu-id="2d52c-200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d52c-200">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d52c-201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d52c-201">'Razor'</span></span>
- <span data-ttu-id="2d52c-202">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2d52c-202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d52c-203">title: 'Blazor-Hostingmodellkonfiguration für ASP.NET Core' author: description: 'Hier erhalten Sie Informationen zur Blazor-Hostingmodellkonfiguration, einschließlich der Integration von Razor-Komponenten in Razor Pages- und MVC-Apps.'</span><span class="sxs-lookup"><span data-stu-id="2d52c-203">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="2d52c-204">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2d52c-204">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d52c-205">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d52c-205">'Blazor'</span></span>
- <span data-ttu-id="2d52c-206">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d52c-206">'Identity'</span></span>
- <span data-ttu-id="2d52c-207">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d52c-207">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d52c-208">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d52c-208">'Razor'</span></span>
- <span data-ttu-id="2d52c-209">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2d52c-209">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d52c-210">title: 'Blazor-Hostingmodellkonfiguration für ASP.NET Core' author: description: 'Hier erhalten Sie Informationen zur Blazor-Hostingmodellkonfiguration, einschließlich der Integration von Razor-Komponenten in Razor Pages- und MVC-Apps.'</span><span class="sxs-lookup"><span data-stu-id="2d52c-210">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="2d52c-211">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2d52c-211">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d52c-212">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d52c-212">'Blazor'</span></span>
- <span data-ttu-id="2d52c-213">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d52c-213">'Identity'</span></span>
- <span data-ttu-id="2d52c-214">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d52c-214">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d52c-215">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d52c-215">'Razor'</span></span>
- <span data-ttu-id="2d52c-216">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2d52c-216">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d52c-217">title: 'Blazor-Hostingmodellkonfiguration für ASP.NET Core' author: description: 'Hier erhalten Sie Informationen zur Blazor-Hostingmodellkonfiguration, einschließlich der Integration von Razor-Komponenten in Razor Pages- und MVC-Apps.'</span><span class="sxs-lookup"><span data-stu-id="2d52c-217">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="2d52c-218">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2d52c-218">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d52c-219">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d52c-219">'Blazor'</span></span>
- <span data-ttu-id="2d52c-220">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d52c-220">'Identity'</span></span>
- <span data-ttu-id="2d52c-221">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d52c-221">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d52c-222">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d52c-222">'Razor'</span></span>
- <span data-ttu-id="2d52c-223">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2d52c-223">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d52c-224">title: 'Blazor-Hostingmodellkonfiguration für ASP.NET Core' author: description: 'Hier erhalten Sie Informationen zur Blazor-Hostingmodellkonfiguration, einschließlich der Integration von Razor-Komponenten in Razor Pages- und MVC-Apps.'</span><span class="sxs-lookup"><span data-stu-id="2d52c-224">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="2d52c-225">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2d52c-225">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d52c-226">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d52c-226">'Blazor'</span></span>
- <span data-ttu-id="2d52c-227">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d52c-227">'Identity'</span></span>
- <span data-ttu-id="2d52c-228">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d52c-228">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d52c-229">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d52c-229">'Razor'</span></span>
- <span data-ttu-id="2d52c-230">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2d52c-230">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d52c-231">title: 'Blazor-Hostingmodellkonfiguration für ASP.NET Core' author: description: 'Hier erhalten Sie Informationen zur Blazor-Hostingmodellkonfiguration, einschließlich der Integration von Razor-Komponenten in Razor Pages- und MVC-Apps.'</span><span class="sxs-lookup"><span data-stu-id="2d52c-231">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="2d52c-232">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2d52c-232">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d52c-233">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d52c-233">'Blazor'</span></span>
- <span data-ttu-id="2d52c-234">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d52c-234">'Identity'</span></span>
- <span data-ttu-id="2d52c-235">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d52c-235">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d52c-236">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d52c-236">'Razor'</span></span>
- <span data-ttu-id="2d52c-237">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2d52c-237">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d52c-238">title: 'Blazor-Hostingmodellkonfiguration für ASP.NET Core' author: description: 'Hier erhalten Sie Informationen zur Blazor-Hostingmodellkonfiguration, einschließlich der Integration von Razor-Komponenten in Razor Pages- und MVC-Apps.'</span><span class="sxs-lookup"><span data-stu-id="2d52c-238">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="2d52c-239">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2d52c-239">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d52c-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d52c-240">'Blazor'</span></span>
- <span data-ttu-id="2d52c-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d52c-241">'Identity'</span></span>
- <span data-ttu-id="2d52c-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d52c-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d52c-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d52c-243">'Razor'</span></span>
- <span data-ttu-id="2d52c-244">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2d52c-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d52c-245">title: 'Blazor-Hostingmodellkonfiguration für ASP.NET Core' author: description: 'Hier erhalten Sie Informationen zur Blazor-Hostingmodellkonfiguration, einschließlich der Integration von Razor-Komponenten in Razor Pages- und MVC-Apps.'</span><span class="sxs-lookup"><span data-stu-id="2d52c-245">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="2d52c-246">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2d52c-246">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d52c-247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d52c-247">'Blazor'</span></span>
- <span data-ttu-id="2d52c-248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d52c-248">'Identity'</span></span>
- <span data-ttu-id="2d52c-249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d52c-249">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d52c-250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d52c-250">'Razor'</span></span>
- <span data-ttu-id="2d52c-251">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2d52c-251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d52c-252">title: 'Blazor-Hostingmodellkonfiguration für ASP.NET Core' author: description: 'Hier erhalten Sie Informationen zur Blazor-Hostingmodellkonfiguration, einschließlich der Integration von Razor-Komponenten in Razor Pages- und MVC-Apps.'</span><span class="sxs-lookup"><span data-stu-id="2d52c-252">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="2d52c-253">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2d52c-253">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d52c-254">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d52c-254">'Blazor'</span></span>
- <span data-ttu-id="2d52c-255">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d52c-255">'Identity'</span></span>
- <span data-ttu-id="2d52c-256">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d52c-256">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d52c-257">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d52c-257">'Razor'</span></span>
- <span data-ttu-id="2d52c-258">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2d52c-258">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d52c-259">title: 'Blazor-Hostingmodellkonfiguration für ASP.NET Core' author: description: 'Hier erhalten Sie Informationen zur Blazor-Hostingmodellkonfiguration, einschließlich der Integration von Razor-Komponenten in Razor Pages- und MVC-Apps.'</span><span class="sxs-lookup"><span data-stu-id="2d52c-259">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="2d52c-260">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2d52c-260">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d52c-261">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d52c-261">'Blazor'</span></span>
- <span data-ttu-id="2d52c-262">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d52c-262">'Identity'</span></span>
- <span data-ttu-id="2d52c-263">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d52c-263">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d52c-264">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d52c-264">'Razor'</span></span>
- <span data-ttu-id="2d52c-265">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2d52c-265">'SignalR' uid:</span></span> 

<span data-ttu-id="2d52c-266">---------------- | --- title: 'Blazor-Hostingmodellkonfiguration für ASP.NET Core' author: description: 'Hier erhalten Sie Informationen zur Blazor-Hostingmodellkonfiguration, einschließlich der Integration von Razor-Komponenten in Razor Pages- und MVC-Apps.'</span><span class="sxs-lookup"><span data-stu-id="2d52c-266">---------------- | --- title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="2d52c-267">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2d52c-267">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d52c-268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d52c-268">'Blazor'</span></span>
- <span data-ttu-id="2d52c-269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d52c-269">'Identity'</span></span>
- <span data-ttu-id="2d52c-270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d52c-270">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d52c-271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d52c-271">'Razor'</span></span>
- <span data-ttu-id="2d52c-272">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2d52c-272">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d52c-273">title: 'Blazor-Hostingmodellkonfiguration für ASP.NET Core' author: description: 'Hier erhalten Sie Informationen zur Blazor-Hostingmodellkonfiguration, einschließlich der Integration von Razor-Komponenten in Razor Pages- und MVC-Apps.'</span><span class="sxs-lookup"><span data-stu-id="2d52c-273">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="2d52c-274">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2d52c-274">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d52c-275">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d52c-275">'Blazor'</span></span>
- <span data-ttu-id="2d52c-276">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d52c-276">'Identity'</span></span>
- <span data-ttu-id="2d52c-277">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d52c-277">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d52c-278">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d52c-278">'Razor'</span></span>
- <span data-ttu-id="2d52c-279">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2d52c-279">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d52c-280">title: 'Blazor-Hostingmodellkonfiguration für ASP.NET Core' author: description: 'Hier erhalten Sie Informationen zur Blazor-Hostingmodellkonfiguration, einschließlich der Integration von Razor-Komponenten in Razor Pages- und MVC-Apps.'</span><span class="sxs-lookup"><span data-stu-id="2d52c-280">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="2d52c-281">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2d52c-281">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d52c-282">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d52c-282">'Blazor'</span></span>
- <span data-ttu-id="2d52c-283">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d52c-283">'Identity'</span></span>
- <span data-ttu-id="2d52c-284">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d52c-284">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d52c-285">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d52c-285">'Razor'</span></span>
- <span data-ttu-id="2d52c-286">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2d52c-286">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d52c-287">title: 'Blazor-Hostingmodellkonfiguration für ASP.NET Core' author: description: 'Hier erhalten Sie Informationen zur Blazor-Hostingmodellkonfiguration, einschließlich der Integration von Razor-Komponenten in Razor Pages- und MVC-Apps.'</span><span class="sxs-lookup"><span data-stu-id="2d52c-287">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="2d52c-288">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2d52c-288">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d52c-289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d52c-289">'Blazor'</span></span>
- <span data-ttu-id="2d52c-290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d52c-290">'Identity'</span></span>
- <span data-ttu-id="2d52c-291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d52c-291">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d52c-292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d52c-292">'Razor'</span></span>
- <span data-ttu-id="2d52c-293">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2d52c-293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d52c-294">title: 'Blazor-Hostingmodellkonfiguration für ASP.NET Core' author: description: 'Hier erhalten Sie Informationen zur Blazor-Hostingmodellkonfiguration, einschließlich der Integration von Razor-Komponenten in Razor Pages- und MVC-Apps.'</span><span class="sxs-lookup"><span data-stu-id="2d52c-294">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="2d52c-295">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2d52c-295">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d52c-296">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d52c-296">'Blazor'</span></span>
- <span data-ttu-id="2d52c-297">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d52c-297">'Identity'</span></span>
- <span data-ttu-id="2d52c-298">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d52c-298">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d52c-299">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d52c-299">'Razor'</span></span>
- <span data-ttu-id="2d52c-300">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2d52c-300">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2d52c-301">title: 'Blazor-Hostingmodellkonfiguration für ASP.NET Core' author: description: 'Hier erhalten Sie Informationen zur Blazor-Hostingmodellkonfiguration, einschließlich der Integration von Razor-Komponenten in Razor Pages- und MVC-Apps.'</span><span class="sxs-lookup"><span data-stu-id="2d52c-301">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="2d52c-302">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2d52c-302">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2d52c-303">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2d52c-303">'Blazor'</span></span>
- <span data-ttu-id="2d52c-304">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2d52c-304">'Identity'</span></span>
- <span data-ttu-id="2d52c-305">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2d52c-305">'Let's Encrypt'</span></span>
- <span data-ttu-id="2d52c-306">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2d52c-306">'Razor'</span></span>
- <span data-ttu-id="2d52c-307">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2d52c-307">'SignalR' uid:</span></span> 

<span data-ttu-id="2d52c-308">--------- | | `components-reconnect-show`     | Eine verlorene Verbindung.</span><span class="sxs-lookup"><span data-stu-id="2d52c-308">--------- | | `components-reconnect-show`     | A lost connection.</span></span> <span data-ttu-id="2d52c-309">Der Client versucht, die Verbindung wiederherzustellen.</span><span class="sxs-lookup"><span data-stu-id="2d52c-309">The client is attempting to reconnect.</span></span> <span data-ttu-id="2d52c-310">Die modale Seite wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="2d52c-310">Show the modal.</span></span> <span data-ttu-id="2d52c-311">| | `components-reconnect-hide`     | Auf dem Server wird eine aktive Verbindung wiederhergestellt.</span><span class="sxs-lookup"><span data-stu-id="2d52c-311">| | `components-reconnect-hide`     | An active connection is re-established to the server.</span></span> <span data-ttu-id="2d52c-312">Die modale Seite wird ausgeblendet.</span><span class="sxs-lookup"><span data-stu-id="2d52c-312">Hide the modal.</span></span> <span data-ttu-id="2d52c-313">| | `components-reconnect-failed`   | Die Verbindung wurde nicht wiederhergestellt, wahrscheinlich aufgrund eines Netzwerkfehlers.</span><span class="sxs-lookup"><span data-stu-id="2d52c-313">| | `components-reconnect-failed`   | Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="2d52c-314">Rufen Sie `window.Blazor.reconnect()` auf, um einen neuen Verbindungsversuch zu unternehmen.</span><span class="sxs-lookup"><span data-stu-id="2d52c-314">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> <span data-ttu-id="2d52c-315">| | `components-reconnect-rejected` | Die Wiederherstellung der Verbindung wurde abgelehnt.</span><span class="sxs-lookup"><span data-stu-id="2d52c-315">| | `components-reconnect-rejected` | Reconnection rejected.</span></span> <span data-ttu-id="2d52c-316">Der Server wurde zwar erreicht, jedoch hat dieser die Verbindung verweigert. Der Status des Benutzers auf dem Server wurde verworfen.</span><span class="sxs-lookup"><span data-stu-id="2d52c-316">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="2d52c-317">Rufen Sie `location.reload()` auf, um die App neu zu laden.</span><span class="sxs-lookup"><span data-stu-id="2d52c-317">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="2d52c-318">Dieser Verbindungszustand kann aus folgenden Gründen auftreten:</span><span class="sxs-lookup"><span data-stu-id="2d52c-318">This connection state may result when:</span></span><ul><li><span data-ttu-id="2d52c-319">Aufgetretener Absturz auf der serverseitigen Verbindung.</span><span class="sxs-lookup"><span data-stu-id="2d52c-319">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="2d52c-320">Der Client war lange nicht verbunden, sodass der Server den Benutzerstatus verworfen hat.</span><span class="sxs-lookup"><span data-stu-id="2d52c-320">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="2d52c-321">Komponenteninstanzen, mit denen der Benutzer interagiert, werden verworfen.</span><span class="sxs-lookup"><span data-stu-id="2d52c-321">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="2d52c-322">Der Server wird neu gestartet, oder der Workerprozess der App wird wiederverwendet.</span><span class="sxs-lookup"><span data-stu-id="2d52c-322">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="2d52c-323">Rendermodus</span><span class="sxs-lookup"><span data-stu-id="2d52c-323">Render mode</span></span>

Blazor<span data-ttu-id="2d52c-324"> Server-Apps werden standardmäßig eingerichtet, um die Benutzeroberfläche auf dem Server schon vor der Einrichtung der Clientverbindung auf dem Server zu rendern.</span><span class="sxs-lookup"><span data-stu-id="2d52c-324"> Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="2d52c-325">Sehen Sie sich die Einrichtung der Razor-Seite *_Host.cshtml* an:</span><span class="sxs-lookup"><span data-stu-id="2d52c-325">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="2d52c-326"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> konfiguriert folgende Einstellungen für die Komponente:</span><span class="sxs-lookup"><span data-stu-id="2d52c-326"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="2d52c-327">Ob die Komponente zuvor für die Seite gerendert wird</span><span class="sxs-lookup"><span data-stu-id="2d52c-327">Is prerendered into the page.</span></span>
* <span data-ttu-id="2d52c-328">Ob die Komponente als statische HTML auf der Seite gerendert wird oder ob sie die nötigen Informationen für das Bootstrapping einer Blazor-App über den Benutzer-Agent enthält.</span><span class="sxs-lookup"><span data-stu-id="2d52c-328">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | <span data-ttu-id="2d52c-329">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="2d52c-329">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="2d52c-330">Rendert die Komponente in statische HTML und fügt einen Marker für eine Blazor Server-App hinzu.</span><span class="sxs-lookup"><span data-stu-id="2d52c-330">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="2d52c-331">Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer Blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="2d52c-331">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="2d52c-332">Rendert einen Marker für eine Blazor Server-App.</span><span class="sxs-lookup"><span data-stu-id="2d52c-332">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="2d52c-333">Die Ausgabe der Komponente ist nicht enthalten.</span><span class="sxs-lookup"><span data-stu-id="2d52c-333">Output from the component isn't included.</span></span> <span data-ttu-id="2d52c-334">Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer Blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="2d52c-334">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="2d52c-335">Rendert die Komponente in statischen HTML-Code.</span><span class="sxs-lookup"><span data-stu-id="2d52c-335">Renders the component into static HTML.</span></span> |

<span data-ttu-id="2d52c-336">Das Rendern von Serverkomponenten über eine statische HTML-Seite wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="2d52c-336">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="2d52c-337">Konfigurieren des SignalR-Clients für Blazor Server-Apps</span><span class="sxs-lookup"><span data-stu-id="2d52c-337">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="2d52c-338">In einigen Fällen müssen Sie den von den SignalR Server-Apps verwendeten Blazor-Client konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="2d52c-338">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="2d52c-339">Beispielsweise können Sie die Protokollierung auf dem SignalR-Client konfigurieren, um ein Verbindungsproblem zu diagnostizieren.</span><span class="sxs-lookup"><span data-stu-id="2d52c-339">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="2d52c-340">So konfigurieren Sie den SignalR-Client in der Datei *Pages/_Host.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="2d52c-340">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="2d52c-341">Fügen Sie ein `autostart="false"`-Attribut zum `<script>`-Tag für das `blazor.server.js`-Skript hinzu.</span><span class="sxs-lookup"><span data-stu-id="2d52c-341">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="2d52c-342">Rufen Sie `Blazor.start` auf, und übergeben ein Konfigurationsobjekt, das den SignalR-Builder angibt.</span><span class="sxs-lookup"><span data-stu-id="2d52c-342">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

### <a name="logging"></a><span data-ttu-id="2d52c-343">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="2d52c-343">Logging</span></span>

<span data-ttu-id="2d52c-344">Weitere Informationen zur Unterstützung von Blazor-Serverprotokollierung finden Sie unter <xref:fundamentals/logging/index#create-logs-in-blazor>.</span><span class="sxs-lookup"><span data-stu-id="2d52c-344">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
