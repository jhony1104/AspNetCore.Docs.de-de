---
title: "title: 'Blazor-Abhängigkeitsinjektion in ASP.NET Core' author: guardrex description: 'Erfahren Sie, wie Blazor-Apps Dienste in Komponenten einfügen können.'"
author: guardrex
description: "monikerRange: '>= aspnetcore-3.1' ms.author: riande ms.custom: mvc ms.date: 05/19/2020 no-loc:"
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
uid: blazor/dependency-injection
ms.openlocfilehash: d5b0747fb0505499197d1751511600bd91fed41d
ms.sourcegitcommit: 5fe724d143825ca799e5bd03fb45b632ea4aa7d5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/02/2020
ms.locfileid: "84271782"
---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="5fe79-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5fe79-103">'Blazor'</span></span>

<span data-ttu-id="5fe79-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5fe79-104">'Identity'</span></span>

<span data-ttu-id="5fe79-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5fe79-105">'Let's Encrypt'</span></span> <span data-ttu-id="5fe79-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5fe79-106">'Razor'</span></span> <span data-ttu-id="5fe79-107">'SignalR' uid: blazor/dependency-injection</span><span class="sxs-lookup"><span data-stu-id="5fe79-107">'SignalR' uid: blazor/dependency-injection</span></span>

<span data-ttu-id="5fe79-108">Abhängigkeitsinjektion in ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="5fe79-108">ASP.NET Core Blazor dependency injection</span></span> <span data-ttu-id="5fe79-109">Von [Rainer Stropek](https://www.timecockpit.com) und [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="5fe79-109">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

* Blazor<span data-ttu-id="5fe79-110"> unterstützt die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (Dependency Injection, DI).</span><span class="sxs-lookup"><span data-stu-id="5fe79-110"> supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="5fe79-111">Apps können integrierte Dienste verwenden, indem Sie sie in Komponenten einfügen.</span><span class="sxs-lookup"><span data-stu-id="5fe79-111">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="5fe79-112">Apps können auch benutzerdefinierte Dienste definieren und registrieren und sie über die Abhängigkeitsinjektion in der gesamten App verfügbar machen.</span><span class="sxs-lookup"><span data-stu-id="5fe79-112">Apps can also define and register custom services and make them available throughout the app via DI.</span></span> <span data-ttu-id="5fe79-113">Die Abhängigkeitsinjektion ist ein Verfahren für den Zugriff auf Dienste, die an einer zentralen Stelle konfiguriert sind.</span><span class="sxs-lookup"><span data-stu-id="5fe79-113">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="5fe79-114">Dies kann in Blazor-Apps für Folgendes nützlich sein:</span><span class="sxs-lookup"><span data-stu-id="5fe79-114">This can be useful in Blazor apps to:</span></span> <span data-ttu-id="5fe79-115">Freigabe einer einzelnen Instanz einer Dienstklasse über viele Komponenten hinweg, bekannt als *Singleton*-Dienst.</span><span class="sxs-lookup"><span data-stu-id="5fe79-115">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>

## <a name="default-services"></a><span data-ttu-id="5fe79-116">Entkoppeln von Komponenten von konkreten Dienstklassen durch Verwendung von Referenzabstraktionen.</span><span class="sxs-lookup"><span data-stu-id="5fe79-116">Decouple components from concrete service classes by using reference abstractions.</span></span>

<span data-ttu-id="5fe79-117">Betrachten Sie zum Beispiel eine `IDataAccess`-Schnittstelle für den Zugriff auf Daten in der App.</span><span class="sxs-lookup"><span data-stu-id="5fe79-117">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span>

| <span data-ttu-id="5fe79-118">Die Schnittstelle wird durch eine konkrete `DataAccess`-Klasse implementiert und als Dienst im Dienstcontainer der App registriert.</span><span class="sxs-lookup"><span data-stu-id="5fe79-118">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> | <span data-ttu-id="5fe79-119">Wenn eine Komponente die Abhängigkeitsinjektion verwendet, um eine `IDataAccess`-Implementierung zu erhalten, ist die Komponente nicht an den konkreten Typ gekoppelt.</span><span class="sxs-lookup"><span data-stu-id="5fe79-119">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> | <span data-ttu-id="5fe79-120">Die Implementierung kann ausgetauscht werden, z. B. gegen eine Pseudoimplementierung in Komponententests.</span><span class="sxs-lookup"><span data-stu-id="5fe79-120">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="5fe79-121">Standarddienste</span><span class="sxs-lookup"><span data-stu-id="5fe79-121">Default services</span></span> | <span data-ttu-id="5fe79-122">Standarddienste werden automatisch zur Dienstsammlung der App hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="5fe79-122">Default services are automatically added to the app's service collection.</span></span><br><br><span data-ttu-id="5fe79-123">Dienst</span><span class="sxs-lookup"><span data-stu-id="5fe79-123">Service</span></span><br><br><span data-ttu-id="5fe79-124">Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="5fe79-124">Lifetime</span></span> <span data-ttu-id="5fe79-125">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="5fe79-125">Description</span></span><br><br><span data-ttu-id="5fe79-126">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="5fe79-126">Transient</span></span> |
| <xref:Microsoft.JSInterop.IJSRuntime> | <span data-ttu-id="5fe79-127">Stellt Methoden zum Senden von HTTP-Anforderungen und Empfangen von HTTP-Antworten aus einer Ressource bereit, die von einem URI identifiziert wird.</span><span class="sxs-lookup"><span data-stu-id="5fe79-127">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><span data-ttu-id="5fe79-128">Die Instanz von <xref:System.Net.Http.HttpClient> in einer Blazor WebAssembly-App verwendet den Browser für die Behandlung des HTTP-Datenverkehrs im Hintergrund.</span><span class="sxs-lookup"><span data-stu-id="5fe79-128">The instance of <xref:System.Net.Http.HttpClient> in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span> | Blazor<span data-ttu-id="5fe79-129"> Server-Apps enthalten standardmäßig keinen <xref:System.Net.Http.HttpClient>, der als Dienst konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="5fe79-129"> Server apps don't include an <xref:System.Net.Http.HttpClient> configured as a service by default.</span></span> <span data-ttu-id="5fe79-130">Stellen Sie einen <xref:System.Net.Http.HttpClient> für eine Blazor Server-App bereit.</span><span class="sxs-lookup"><span data-stu-id="5fe79-130">Provide an <xref:System.Net.Http.HttpClient> to a Blazor Server app.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | <span data-ttu-id="5fe79-131">Weitere Informationen finden Sie unter <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="5fe79-131">For more information, see <xref:blazor/call-web-api>.</span></span><br><span data-ttu-id="5fe79-132">Singleton (Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="5fe79-132">Singleton (Blazor WebAssembly)</span></span> | <span data-ttu-id="5fe79-133">Bereichsbezogen (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="5fe79-133">Scoped (Blazor Server)</span></span> <span data-ttu-id="5fe79-134">Stellt eine Instanz einer JavaScript-Laufzeit dar, in der JavaScript-Aufrufe verteilt werden.</span><span class="sxs-lookup"><span data-stu-id="5fe79-134">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> |

<span data-ttu-id="5fe79-135">Weitere Informationen finden Sie unter <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="5fe79-135">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> <span data-ttu-id="5fe79-136">Singleton (Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="5fe79-136">Singleton (Blazor WebAssembly)</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="5fe79-137">Bereichsbezogen (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="5fe79-137">Scoped (Blazor Server)</span></span>

### <a name="blazor-webassembly"></a><span data-ttu-id="5fe79-138">Enthält Hilfsprogramme für die Arbeit mit URIs und dem Navigationszustand.</span><span class="sxs-lookup"><span data-stu-id="5fe79-138">Contains helpers for working with URIs and navigation state.</span></span>

<span data-ttu-id="5fe79-139">Weitere Informationen finden Sie unter [Hilfsprogramme für URI und Navigationszustand](xref:blazor/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="5fe79-139">For more information, see [URI and navigation state helpers](xref:blazor/routing#uri-and-navigation-state-helpers).</span></span> <span data-ttu-id="5fe79-140">Ein benutzerdefinierter Dienstanbieter stellt nicht automatisch die in der Tabelle aufgeführten Standarddienste bereit.</span><span class="sxs-lookup"><span data-stu-id="5fe79-140">A custom service provider doesn't automatically provide the default services listed in the table.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<IMyDependency, MyDependency>();
        builder.RootComponents.Add<App>("app");

        await builder.Build().RunAsync();
    }
}
```

<span data-ttu-id="5fe79-141">Wenn Sie einen benutzerdefinierten Dienstanbieter verwenden und einen der Dienste benötigen, die in der Tabelle angezeigt werden, fügen Sie dem neuen Dienstanbieter die erforderlichen Dienste hinzu.</span><span class="sxs-lookup"><span data-stu-id="5fe79-141">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span> <span data-ttu-id="5fe79-142">Hinzufügen von Diensten zu einer App</span><span class="sxs-lookup"><span data-stu-id="5fe79-142">Add services to an app</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync();

        await host.RunAsync();
    }
}
```

Blazor<span data-ttu-id="5fe79-143"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="5fe79-143"> WebAssembly</span></span> <span data-ttu-id="5fe79-144">Konfigurieren Sie Dienste für die Dienstsammlung der App in der `Main`-Methode von *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="5fe79-144">Configure services for the app's service collection in the `Main` method of *Program.cs*.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync(
            host.Configuration["WeatherServiceUrl"]);

        await host.RunAsync();
    }
}
```

### <a name="blazor-server"></a><span data-ttu-id="5fe79-145">Im folgenden Beispiel ist die `MyDependency`-Implementierung für `IMyDependency` registriert:</span><span class="sxs-lookup"><span data-stu-id="5fe79-145">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

<span data-ttu-id="5fe79-146">Nachdem der Host erstellt wurde, kann auf die Dienste vom Stammbereich der Abhängigkeitsinjektion aus zugegriffen werden, bevor irgendwelche Komponenten bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="5fe79-146">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="5fe79-147">Dies kann für die Ausführung der Initialisierungslogik vor dem Rendern von Inhalten nützlich sein:</span><span class="sxs-lookup"><span data-stu-id="5fe79-147">This can be useful for running initialization logic before rendering content:</span></span> <span data-ttu-id="5fe79-148">Der Host stellt auch eine zentrale Konfigurationsinstanz für die App zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="5fe79-148">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="5fe79-149">Ausgehend vom vorhergehenden Beispiel wird die URL des Wetterdiensts von einer Standardkonfigurationsquelle (z. B. *appsettings.json*) an `InitializeWeatherAsync` übergeben:</span><span class="sxs-lookup"><span data-stu-id="5fe79-149">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, *appsettings.json*) to `InitializeWeatherAsync`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a>Blazor<span data-ttu-id="5fe79-150"> Server</span><span class="sxs-lookup"><span data-stu-id="5fe79-150"> Server</span></span>

<span data-ttu-id="5fe79-151">Nachdem Sie eine neue App erstellt haben, untersuchen Sie die `Startup.ConfigureServices`-Methode:</span><span class="sxs-lookup"><span data-stu-id="5fe79-151">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

| <span data-ttu-id="5fe79-152">Der Methode <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> wird ein <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> übergeben, das eine Liste von Dienstdeskriptorobjekten (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>) ist.</span><span class="sxs-lookup"><span data-stu-id="5fe79-152">The <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> | <span data-ttu-id="5fe79-153">Dienste werden durch die Bereitstellung von Dienstdeskriptoren in die Dienstsammlung aufgenommen.</span><span class="sxs-lookup"><span data-stu-id="5fe79-153">Services are added by providing service descriptors to the service collection.</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | <span data-ttu-id="5fe79-154">Das folgende Beispiel veranschaulicht das Konzept mit der `IDataAccess`-Schnittstelle und seiner konkreten Implementierung `DataAccess`:</span><span class="sxs-lookup"><span data-stu-id="5fe79-154">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span> <span data-ttu-id="5fe79-155">Lebensdauer von Diensten</span><span class="sxs-lookup"><span data-stu-id="5fe79-155">Service lifetime</span></span> <span data-ttu-id="5fe79-156">Die Dienste können mit den in der folgenden Tabelle angegebenen Lebensdauern konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="5fe79-156">Services can be configured with the lifetimes shown in the following table.</span></span> <span data-ttu-id="5fe79-157">Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="5fe79-157">Lifetime</span></span> <span data-ttu-id="5fe79-158">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="5fe79-158">Description</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | Blazor<span data-ttu-id="5fe79-159"> WebAssembly-Apps verfügen derzeit nicht über ein Konzept für Bereiche von Abhängigkeitsinjektionen.</span><span class="sxs-lookup"><span data-stu-id="5fe79-159"> WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="5fe79-160">`Scoped`-registrierte Dienste verhalten sich wie `Singleton`-Dienste.</span><span class="sxs-lookup"><span data-stu-id="5fe79-160">`Scoped`-registered services behave like `Singleton` services.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | <span data-ttu-id="5fe79-161">Das Blazor Server-Hostingmodell unterstützt jedoch die Lebensdauer `Scoped`.</span><span class="sxs-lookup"><span data-stu-id="5fe79-161">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> |

<span data-ttu-id="5fe79-162">In Blazor Server-Apps wird die Registrierung eines bereichsbezogenen Diensts der *Verbindung* zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="5fe79-162">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="5fe79-163">Aus diesem Grund wird die Verwendung von bereichsbezogenen Diensten für Dienste bevorzugt, die dem aktuellen Benutzer zugeordnet werden sollen, auch wenn die aktuelle Absicht darin besteht, clientseitig im Browser ausgeführt zu werden.</span><span class="sxs-lookup"><span data-stu-id="5fe79-163">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="5fe79-164">Die Abhängigkeitsinjektion erstellt eine *Einzelinstanz* des Diensts.</span><span class="sxs-lookup"><span data-stu-id="5fe79-164">DI creates a *single instance* of the service.</span></span>

<span data-ttu-id="5fe79-165">Alle Komponenten, die einen `Singleton`-Dienst erfordern, erhalten eine Instanz desselben Diensts.</span><span class="sxs-lookup"><span data-stu-id="5fe79-165">All components requiring a `Singleton` service receive an instance of the same service.</span></span> <span data-ttu-id="5fe79-166">Immer wenn eine Komponente eine Instanz eines `Transient`-Diensts aus dem Dienstcontainer erhält, erhält sie eine *neue Instanz* des Diensts.</span><span class="sxs-lookup"><span data-stu-id="5fe79-166">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span>

* <span data-ttu-id="5fe79-167">Das Abhängigkeitsinjektionssystem basiert auf dem Abhängigkeitsinjektionssystem in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5fe79-167">The DI system is based on the DI system in ASP.NET Core.</span></span>
* <span data-ttu-id="5fe79-168">Weitere Informationen finden Sie unter <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="5fe79-168">For more information, see <xref:fundamentals/dependency-injection>.</span></span> <span data-ttu-id="5fe79-169">Anfordern eines Diensts in einer Komponente</span><span class="sxs-lookup"><span data-stu-id="5fe79-169">Request a service in a component</span></span> <span data-ttu-id="5fe79-170">Nachdem die Dienste der Dienstsammlung hinzugefügt wurden, fügen Sie die Dienste mit der Razor-Anweisung [\@inject](xref:mvc/views/razor#inject) in die Komponenten ein.</span><span class="sxs-lookup"><span data-stu-id="5fe79-170">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span>

<span data-ttu-id="5fe79-171">[`@inject`](xref:mvc/views/razor#inject) besitzt zwei Parameter:</span><span class="sxs-lookup"><span data-stu-id="5fe79-171">[`@inject`](xref:mvc/views/razor#inject) has two parameters:</span></span>

<span data-ttu-id="5fe79-172">Typ: Der Typ des einzufügenden Diensts.</span><span class="sxs-lookup"><span data-stu-id="5fe79-172">Type: The type of the service to inject.</span></span>

<span data-ttu-id="5fe79-173">Eigenschaft: Der Name der Eigenschaft, die den eingefügten App-Dienst erhält.</span><span class="sxs-lookup"><span data-stu-id="5fe79-173">Property: The name of the property receiving the injected app service.</span></span> <span data-ttu-id="5fe79-174">Die Eigenschaft erfordert keine manuelle Erstellung.</span><span class="sxs-lookup"><span data-stu-id="5fe79-174">The property doesn't require manual creation.</span></span> <span data-ttu-id="5fe79-175">Der Compiler erstellt die Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="5fe79-175">The compiler creates the property.</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

<span data-ttu-id="5fe79-176">Weitere Informationen finden Sie unter <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="5fe79-176">For more information, see <xref:mvc/views/dependency-injection>.</span></span> <span data-ttu-id="5fe79-177">Verwenden Sie mehrere [`@inject`](xref:mvc/views/razor#inject)-Anweisungen, um verschiedene Dienste einzufügen.</span><span class="sxs-lookup"><span data-stu-id="5fe79-177">Use multiple [`@inject`](xref:mvc/views/razor#inject) statements to inject different services.</span></span> <span data-ttu-id="5fe79-178">Das folgende Beispiel veranschaulicht die Verwendung von [`@inject`](xref:mvc/views/razor#inject).</span><span class="sxs-lookup"><span data-stu-id="5fe79-178">The following example shows how to use [`@inject`](xref:mvc/views/razor#inject).</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="5fe79-179">Der Dienst, der `Services.IDataAccess` implementiert, wird in die Eigenschaft `DataRepository` der Komponente eingefügt.</span><span class="sxs-lookup"><span data-stu-id="5fe79-179">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="5fe79-180">Beachten Sie, dass der Code nur die Abstraktion `IDataAccess` verwendet:</span><span class="sxs-lookup"><span data-stu-id="5fe79-180">Note how the code is only using the `IDataAccess` abstraction:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="5fe79-181">Intern verwendet die generierte Eigenschaft (`DataRepository`) das Attribut [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute).</span><span class="sxs-lookup"><span data-stu-id="5fe79-181">Internally, the generated property (`DataRepository`) uses the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute.</span></span>

<span data-ttu-id="5fe79-182">In der Regel wird dieses Attribut nicht direkt verwendet.</span><span class="sxs-lookup"><span data-stu-id="5fe79-182">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="5fe79-183">Wenn eine Basisklasse für Komponenten erforderlich ist und die eingefügten Eigenschaften auch für die Basisklasse erforderlich sind, fügen Sie das [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute)-Attribut manuell hinzu:</span><span class="sxs-lookup"><span data-stu-id="5fe79-183">If a base class is required for components and injected properties are also required for the base class, manually add the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute:</span></span> <span data-ttu-id="5fe79-184">In Komponenten, die von der Basisklasse abgeleitet sind, ist die [`@inject`](xref:mvc/views/razor#inject)-Direktive nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="5fe79-184">In components derived from the base class, the [`@inject`](xref:mvc/views/razor#inject) directive isn't required.</span></span> <span data-ttu-id="5fe79-185">Das <xref:Microsoft.AspNetCore.Components.InjectAttribute> der Basisklasse ist ausreichend:</span><span class="sxs-lookup"><span data-stu-id="5fe79-185">The <xref:Microsoft.AspNetCore.Components.InjectAttribute> of the base class is sufficient:</span></span> <span data-ttu-id="5fe79-186">Verwenden der Abhängigkeitsinjektion in Diensten</span><span class="sxs-lookup"><span data-stu-id="5fe79-186">Use DI in services</span></span> <span data-ttu-id="5fe79-187">Komplexe Dienste können zusätzliche Dienste erfordern.</span><span class="sxs-lookup"><span data-stu-id="5fe79-187">Complex services might require additional services.</span></span>

```csharp
public class DataAccess : IDataAccess
{
    // The constructor receives an HttpClient via dependency
    // injection. HttpClient is a default service.
    public DataAccess(HttpClient client)
    {
        ...
    }
}
```

<span data-ttu-id="5fe79-188">Im vorherigen Beispiel könnte `DataAccess` den Standarddienst <xref:System.Net.Http.HttpClient> erfordern.</span><span class="sxs-lookup"><span data-stu-id="5fe79-188">In the prior example, `DataAccess` might require the <xref:System.Net.Http.HttpClient> default service.</span></span>

* <span data-ttu-id="5fe79-189">[`@inject`](xref:mvc/views/razor#inject) (oder das [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute)-Attribut) ist nicht für die Verwendung in Diensten verfügbar.</span><span class="sxs-lookup"><span data-stu-id="5fe79-189">[`@inject`](xref:mvc/views/razor#inject) (or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute) isn't available for use in services.</span></span> <span data-ttu-id="5fe79-190">Stattdessen muss die *Constructor Injection* verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="5fe79-190">*Constructor injection* must be used instead.</span></span>
* <span data-ttu-id="5fe79-191">Erforderliche Dienste werden durch Hinzufügen von Parametern zum Konstruktor des Diensts hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="5fe79-191">Required services are added by adding parameters to the service's constructor.</span></span>
* <span data-ttu-id="5fe79-192">Wenn die Abhängigkeitsinjektion den Dienst erstellt, erkennt sie die erforderlichen Dienste im Konstruktor und stellt sie entsprechend zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="5fe79-192">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span> <span data-ttu-id="5fe79-193">Voraussetzungen für die Constructor Injection:</span><span class="sxs-lookup"><span data-stu-id="5fe79-193">Prerequisites for constructor injection:</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="5fe79-194">Es muss einen Konstruktor geben, dessen Argumente alle von der Abhängigkeitsinjektion erfüllt werden können.</span><span class="sxs-lookup"><span data-stu-id="5fe79-194">One constructor must exist whose arguments can all be fulfilled by DI.</span></span>

<span data-ttu-id="5fe79-195">Zusätzliche, nicht durch die Abhängigkeitsinjektion abgedeckte Parameter sind zulässig, wenn sie Standardwerte angeben.</span><span class="sxs-lookup"><span data-stu-id="5fe79-195">Additional parameters not covered by DI are allowed if they specify default values.</span></span> <span data-ttu-id="5fe79-196">Der anwendbare Konstruktor muss *öffentlich* sein.</span><span class="sxs-lookup"><span data-stu-id="5fe79-196">The applicable constructor must be *public*.</span></span> <span data-ttu-id="5fe79-197">Es muss ein anwendbarer Konstruktor vorhanden sein.</span><span class="sxs-lookup"><span data-stu-id="5fe79-197">One applicable constructor must exist.</span></span> <span data-ttu-id="5fe79-198">Im Falle einer Mehrdeutigkeit löst die Abhängigkeitsinjektion eine Ausnahme aus.</span><span class="sxs-lookup"><span data-stu-id="5fe79-198">In case of an ambiguity, DI throws an exception.</span></span>

<span data-ttu-id="5fe79-199">Hilfsprogramm-Basiskomponentenklassen zur Verwaltung eines Bereichs für die Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="5fe79-199">Utility base component classes to manage a DI scope</span></span> <span data-ttu-id="5fe79-200">In ASP.NET Core-Apps werden bereichsbezogene Dienste in der Regel der aktuellen Anforderung zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="5fe79-200">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="5fe79-201">Nachdem die Anforderung abgeschlossen ist, werden alle bereichsbezogenen oder vorübergehenden Dienste vom Abhängigkeitsinjektionssystem entsorgt.</span><span class="sxs-lookup"><span data-stu-id="5fe79-201">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="5fe79-202">In Blazor Server-Apps besteht der Anforderungsbereich für die Dauer der Clientverbindung, was dazu führen kann, dass vorübergehende und bereichsbezogene Dienste viel länger als erwartet leben.</span><span class="sxs-lookup"><span data-stu-id="5fe79-202">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="5fe79-203">In Blazor WebAssembly-Apps werden Dienste, die mit einer bereichsbezogenen Lebensdauer registriert sind, als Singletons behandelt, sodass sie länger leben als bereichsbezogene Dienste in typischen ASP.NET Core-Apps.</span><span class="sxs-lookup"><span data-stu-id="5fe79-203">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

* <span data-ttu-id="5fe79-204">Ein Ansatz, der die Lebensdauer eines Diensts in Blazor-Apps begrenzt, ist die Verwendung des Typs <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.</span><span class="sxs-lookup"><span data-stu-id="5fe79-204">An approach that limits a service lifetime in Blazor apps is use of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type.</span></span>
* <span data-ttu-id="5fe79-205"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> ist ein abstrakter, von <xref:Microsoft.AspNetCore.Components.ComponentBase> abgeleiteter Typ, der einen der Lebensdauer der Komponente entsprechenden Bereich für die Abhängigkeitsinjektion erstellt.</span><span class="sxs-lookup"><span data-stu-id="5fe79-205"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract type derived from <xref:Microsoft.AspNetCore.Components.ComponentBase> that creates a DI scope corresponding to the lifetime of the component.</span></span>

<span data-ttu-id="5fe79-206">Mit diesem Bereich ist es möglich, Abhängigkeitsinjektionsdienste mit einer bereichsbezogenen Lebensdauer zu nutzen und sie so lange wie die Komponente zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="5fe79-206">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span>

* <span data-ttu-id="5fe79-207">Wenn die Komponente zerstört wird, werden auch die Dienste des bereichsbezogenen Dienstanbieters der Komponente entsorgt.</span><span class="sxs-lookup"><span data-stu-id="5fe79-207">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="5fe79-208">Dies kann für Dienste nützlich sein, für die Folgendes gilt:</span><span class="sxs-lookup"><span data-stu-id="5fe79-208">This can be useful for services that:</span></span>

  <span data-ttu-id="5fe79-209">Sie sollten innerhalb einer Komponente wiederverwendet werden, da die vorübergehende Lebensdauer unangemessen ist.</span><span class="sxs-lookup"><span data-stu-id="5fe79-209">Should be reused within a component, as the transient lifetime is inappropriate.</span></span> <span data-ttu-id="5fe79-210">Sie sollten nicht komponentenübergreifend freigegeben werden, da die Singleton-Lebensdauer unangemessen ist.</span><span class="sxs-lookup"><span data-stu-id="5fe79-210">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span> <span data-ttu-id="5fe79-211">Zwei Versionen des Typs <xref:Microsoft.AspNetCore.Components.OwningComponentBase> sind verfügbar:</span><span class="sxs-lookup"><span data-stu-id="5fe79-211">Two versions of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type are available:</span></span>

  ```razor
  @page "/preferences"
  @using Microsoft.Extensions.DependencyInjection
  @inherits OwningComponentBase

  <h1>User (@UserService.Name)</h1>

  <ul>
      @foreach (var setting in SettingService.GetSettings())
      {
          <li>@setting.SettingName: @setting.SettingValue</li>
      }
  </ul>

  @code {
      private IUserService UserService { get; set; }
      private ISettingService SettingService { get; set; }

      protected override void OnInitialized()
      {
          UserService = ScopedServices.GetRequiredService<IUserService>();
          SettingService = ScopedServices.GetRequiredService<ISettingService>();
      }
  }
  ```

* <span data-ttu-id="5fe79-212"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> ist ein abstraktes, verwerfbares, untergeordnetes Element vom Typ <xref:Microsoft.AspNetCore.Components.ComponentBase> mit einer geschützten <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices>-Eigenschaft vom Typ <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="5fe79-212"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract, disposable child of the <xref:Microsoft.AspNetCore.Components.ComponentBase> type with a protected <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property of type <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="5fe79-213">Dieser Anbieter kann zur Auflösung von Diensten verwendet werden, die der Lebensdauer der Komponente zugeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="5fe79-213">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span> <span data-ttu-id="5fe79-214">Abhängigkeitsinjektionsdienste, die mit [`@inject`](xref:mvc/views/razor#inject) oder dem [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute)-Attribut in die Komponente eingefügt werden, werden nicht im Gültigkeitsbereich der Komponente erstellt.</span><span class="sxs-lookup"><span data-stu-id="5fe79-214">DI services injected into the component using [`@inject`](xref:mvc/views/razor#inject) or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute aren't created in the component's scope.</span></span>

  ```razor
  @page "/users"
  @attribute [Authorize]
  @inherits OwningComponentBase<AppDbContext>

  <h1>Users (@Service.Users.Count())</h1>

  <ul>
      @foreach (var user in Service.Users)
      {
          <li>@user.UserName</li>
      }
  </ul>
  ```

## <a name="use-of-entity-framework-dbcontext-from-di"></a><span data-ttu-id="5fe79-215">Um den Bereich der Komponente zu verwenden, müssen die Dienste mit <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> oder <xref:System.IServiceProvider.GetService%2A> aufgelöst werden.</span><span class="sxs-lookup"><span data-stu-id="5fe79-215">To use the component's scope, services must be resolved using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> or <xref:System.IServiceProvider.GetService%2A>.</span></span>

<span data-ttu-id="5fe79-216">Allen Diensten, die unter Verwendung des <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices>-Anbieters aufgelöst werden, werden ihre Abhängigkeiten aus demselben Bereich bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="5fe79-216">Any services resolved using the <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provider have their dependencies provided from that same scope.</span></span> <span data-ttu-id="5fe79-217"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> ist abgeleitet von <xref:Microsoft.AspNetCore.Components.OwningComponentBase> und fügt eine Eigenschaft <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> hinzu, die eine Instanz von `T` vom bereichsbezogenen Abhängigkeitsinjektionsanbieter zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="5fe79-217"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> derives from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and adds a <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> property that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="5fe79-218">Dieser Typ ist eine komfortable Methode für den Zugriff auf bereichsbezogene Dienste, ohne eine Instanz von <xref:System.IServiceProvider> zu verwenden, wenn es einen primären Dienst gibt, den die Anwendung aus dem Abhängigkeitsinjektionscontainer unter Verwendung des Bereichs der Komponente benötigt.</span><span class="sxs-lookup"><span data-stu-id="5fe79-218">This type is a convenient way to access scoped services without using an instance of <xref:System.IServiceProvider> when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="5fe79-219">Die Eigenschaft <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> ist verfügbar, sodass die App bei Bedarf auch Dienste mit anderen Typen erhalten kann.</span><span class="sxs-lookup"><span data-stu-id="5fe79-219">The <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property is available, so the app can get services of other types, if necessary.</span></span>

<span data-ttu-id="5fe79-220">Verwenden von Entity Framework-DbContext von der Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="5fe79-220">Use of Entity Framework DbContext from DI</span></span> <span data-ttu-id="5fe79-221">Ein allgemeiner Diensttyp zum Abrufen der Abhängigkeitsinjektion in Web-Apps sind EF-<xref:Microsoft.EntityFrameworkCore.DbContext>-Objekte (Entity Framework).</span><span class="sxs-lookup"><span data-stu-id="5fe79-221">One common service type to retrieve from DI in web apps is Entity Framework (EF) <xref:Microsoft.EntityFrameworkCore.DbContext> objects.</span></span>

* <span data-ttu-id="5fe79-222">Beim Registrieren von EF-Diensten mithilfe von <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> wird <xref:Microsoft.EntityFrameworkCore.DbContext> standardmäßig als bereichsbezogener Dienst hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="5fe79-222">Registering EF services using <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> adds the <xref:Microsoft.EntityFrameworkCore.DbContext> as a scoped service by default.</span></span>
* <span data-ttu-id="5fe79-223">Die Registrierung als bereichsbezogener Dienst kann zu Problemen in Blazor-Apps führen, da sie dazu führt, dass <xref:Microsoft.EntityFrameworkCore.DbContext>-Instanzen langlebig sind und in der gesamten App gemeinsam genutzt werden.</span><span class="sxs-lookup"><span data-stu-id="5fe79-223">Registering as a scoped service can lead to problems in Blazor apps because it causes <xref:Microsoft.EntityFrameworkCore.DbContext> instances to be long-lived and shared across the app.</span></span>

<span data-ttu-id="5fe79-224"><xref:Microsoft.EntityFrameworkCore.DbContext> ist nicht threadsicher und darf nicht gleichzeitig verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="5fe79-224"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread-safe and must not be used concurrently.</span></span> <span data-ttu-id="5fe79-225">Je nach App kann die Verwendung von <xref:Microsoft.AspNetCore.Components.OwningComponentBase> zur Begrenzung des Bereichs von <xref:Microsoft.EntityFrameworkCore.DbContext> auf eine einzelne Komponente das Problem *möglicherweise* lösen.</span><span class="sxs-lookup"><span data-stu-id="5fe79-225">Depending on the app, using <xref:Microsoft.AspNetCore.Components.OwningComponentBase> to limit the scope of a <xref:Microsoft.EntityFrameworkCore.DbContext> to a single component *may* solve the issue.</span></span> <span data-ttu-id="5fe79-226">Wenn eine Komponente nicht parallel ein <xref:Microsoft.EntityFrameworkCore.DbContext> verwendet, reicht es aus, die Komponente aus <xref:Microsoft.AspNetCore.Components.OwningComponentBase> abzuleiten und den <xref:Microsoft.EntityFrameworkCore.DbContext> aus <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> abzurufen, da Folgendes sichergestellt wird:</span><span class="sxs-lookup"><span data-stu-id="5fe79-226">If a component doesn't use a <xref:Microsoft.EntityFrameworkCore.DbContext> in parallel, deriving the component from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and retrieving the <xref:Microsoft.EntityFrameworkCore.DbContext> from <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> is sufficient because it ensures that:</span></span>

* <span data-ttu-id="5fe79-227">Separate Komponenten teilen sich kein <xref:Microsoft.EntityFrameworkCore.DbContext>.</span><span class="sxs-lookup"><span data-stu-id="5fe79-227">Separate components don't share a <xref:Microsoft.EntityFrameworkCore.DbContext>.</span></span>

    ```razor
    @page "/example"
    @inject DbContextOptions<AppDbContext> DbContextOptions

    <ul>
        @foreach (var item in data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> data = new List<string>();

        private async Task LoadData()
        {
            data = await GetAsync();
            StateHasChanged();
        }

        public async Task<List<string>> GetAsync()
        {
            using (var context = new AppDbContext(DbContextOptions))
            {
                return await context.Products.Select(p => p.Name).ToListAsync();
            }
        }
    }
    ```

* <span data-ttu-id="5fe79-228">Der <xref:Microsoft.EntityFrameworkCore.DbContext> lebt nur so lange, wie die von ihm abhängige Komponente.</span><span class="sxs-lookup"><span data-stu-id="5fe79-228">The <xref:Microsoft.EntityFrameworkCore.DbContext> lives only as long as the component depending on it.</span></span>
  * <span data-ttu-id="5fe79-229">Wenn eine einzelne Komponente gleichzeitig einen <xref:Microsoft.EntityFrameworkCore.DbContext> verwenden könnte (z. B. jedes Mal, wenn ein Benutzer eine Schaltfläche auswählt), vermeidet selbst die Verwendung von <xref:Microsoft.AspNetCore.Components.OwningComponentBase> die Probleme mit gleichzeitigen EF-Vorgängen nicht.</span><span class="sxs-lookup"><span data-stu-id="5fe79-229">If a single component might use a <xref:Microsoft.EntityFrameworkCore.DbContext> concurrently (for example, every time a user selects a button), even using <xref:Microsoft.AspNetCore.Components.OwningComponentBase> doesn't avoid issues with concurrent EF operations.</span></span> <span data-ttu-id="5fe79-230">Verwenden Sie in diesem Fall für jeden logischen EF-Vorgang einen anderen <xref:Microsoft.EntityFrameworkCore.DbContext>.</span><span class="sxs-lookup"><span data-stu-id="5fe79-230">In that case, use a different <xref:Microsoft.EntityFrameworkCore.DbContext> for each logical EF operation.</span></span> <span data-ttu-id="5fe79-231">Verwenden Sie einen der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="5fe79-231">Use either of the following approaches:</span></span> <span data-ttu-id="5fe79-232">Erstellen Sie den <xref:Microsoft.EntityFrameworkCore.DbContext> direkt mit <xref:Microsoft.EntityFrameworkCore.DbContextOptions%601> als Argument, das von der Abhängigkeitsinjektion abgerufen werden kann und threadsicher ist.</span><span class="sxs-lookup"><span data-stu-id="5fe79-232">Create the <xref:Microsoft.EntityFrameworkCore.DbContext> directly using <xref:Microsoft.EntityFrameworkCore.DbContextOptions%601> as an argument, which can be retrieved from DI and is thread safe.</span></span>

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * <span data-ttu-id="5fe79-233">Registrieren Sie den <xref:Microsoft.EntityFrameworkCore.DbContext> im Dienstcontainer mit einer vorübergehenden Lebensdauer:</span><span class="sxs-lookup"><span data-stu-id="5fe79-233">Register the <xref:Microsoft.EntityFrameworkCore.DbContext> in the service container with a transient lifetime:</span></span> <span data-ttu-id="5fe79-234">Verwenden Sie bei der Registrierung des Kontexts <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="5fe79-234">When registering the context, use <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>.</span></span>

    ```razor
    @page "/example"
    @using Microsoft.Extensions.DependencyInjection
    @inject IServiceProvider ServiceProvider

    <ul>
        @foreach (var item in data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> data = new List<string>();

        private async Task LoadData()
        {
            data = await GetAsync();
            StateHasChanged();
        }

        public async Task<List<string>> GetAsync()
        {
            using (var context = ServiceProvider.GetRequiredService<AppDbContext>())
            {
                return await context.Products.Select(p => p.Name).ToListAsync();
            }
        }
    }
    ```

## <a name="additional-resources"></a><span data-ttu-id="5fe79-235">Die <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A>-Erweiterungsmethode nimmt zwei optionale Parameter vom Typ <xref:Microsoft.Extensions.DependencyInjection.ServiceLifetime> an.</span><span class="sxs-lookup"><span data-stu-id="5fe79-235">The <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method takes two optional parameters of type <xref:Microsoft.Extensions.DependencyInjection.ServiceLifetime>.</span></span>

* <xref:fundamentals/dependency-injection>
* <span data-ttu-id="5fe79-236">Um diesen Ansatz zu verwenden, muss nur der Parameter `contextLifetime` entsprechend <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType> sein.</span><span class="sxs-lookup"><span data-stu-id="5fe79-236">To use this approach, only the `contextLifetime` parameter needs to be <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>.</span></span>
* <xref:mvc/views/dependency-injection>
