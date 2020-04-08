---
title: Abhängigkeitsinjektion in ASP.NET Core Blazor
author: guardrex
description: Erfahren Sie, wie Blazor-Apps Dienste in Komponenten einfügen können.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/20/2020
no-loc:
- Blazor
- SignalR
uid: blazor/dependency-injection
ms.openlocfilehash: 4cdde9ee8c9fd9adf00894a067d32965b180e5ec
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/06/2020
ms.locfileid: "78646693"
---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="6e50c-103">Abhängigkeitsinjektion in ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="6e50c-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="6e50c-104">Von [Rainer Stropek](https://www.timecockpit.com) und [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="6e50c-104">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="6e50c-105">Blazor unterstützt die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="6e50c-105">Blazor supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="6e50c-106">Apps können integrierte Dienste verwenden, indem Sie sie in Komponenten einfügen.</span><span class="sxs-lookup"><span data-stu-id="6e50c-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="6e50c-107">Apps können auch benutzerdefinierte Dienste definieren und registrieren und sie über die Abhängigkeitsinjektion in der gesamten App verfügbar machen.</span><span class="sxs-lookup"><span data-stu-id="6e50c-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="6e50c-108">Die Abhängigkeitsinjektion ist ein Verfahren für den Zugriff auf Dienste, die an einer zentralen Stelle konfiguriert sind.</span><span class="sxs-lookup"><span data-stu-id="6e50c-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="6e50c-109">Dies kann in Blazor-Apps für Folgendes nützlich sein:</span><span class="sxs-lookup"><span data-stu-id="6e50c-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="6e50c-110">Freigabe einer einzelnen Instanz einer Dienstklasse über viele Komponenten hinweg, bekannt als *Singleton*-Dienst.</span><span class="sxs-lookup"><span data-stu-id="6e50c-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="6e50c-111">Entkoppeln von Komponenten von konkreten Dienstklassen durch Verwendung von Referenzabstraktionen.</span><span class="sxs-lookup"><span data-stu-id="6e50c-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="6e50c-112">Betrachten Sie zum Beispiel eine `IDataAccess`-Schnittstelle für den Zugriff auf Daten in der App.</span><span class="sxs-lookup"><span data-stu-id="6e50c-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="6e50c-113">Die Schnittstelle wird durch eine konkrete `DataAccess`-Klasse implementiert und als Dienst im Dienstcontainer der App registriert.</span><span class="sxs-lookup"><span data-stu-id="6e50c-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="6e50c-114">Wenn eine Komponente die Abhängigkeitsinjektion verwendet, um eine `IDataAccess`-Implementierung zu erhalten, ist die Komponente nicht an den konkreten Typ gekoppelt.</span><span class="sxs-lookup"><span data-stu-id="6e50c-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="6e50c-115">Die Implementierung kann ausgetauscht werden, z. B. gegen eine Pseudoimplementierung in Komponententests.</span><span class="sxs-lookup"><span data-stu-id="6e50c-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="6e50c-116">Standarddienste</span><span class="sxs-lookup"><span data-stu-id="6e50c-116">Default services</span></span>

<span data-ttu-id="6e50c-117">Standarddienste werden automatisch zur Dienstsammlung der App hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="6e50c-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="6e50c-118">Dienst</span><span class="sxs-lookup"><span data-stu-id="6e50c-118">Service</span></span> | <span data-ttu-id="6e50c-119">Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="6e50c-119">Lifetime</span></span> | <span data-ttu-id="6e50c-120">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="6e50c-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="6e50c-121">Singleton</span><span class="sxs-lookup"><span data-stu-id="6e50c-121">Singleton</span></span> | <span data-ttu-id="6e50c-122">Stellt Methoden zum Senden von HTTP-Anforderungen und Empfangen von HTTP-Antworten aus einer Ressource bereit, die von einem URI identifiziert wird.</span><span class="sxs-lookup"><span data-stu-id="6e50c-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="6e50c-123">Die Instanz von `HttpClient` in einer Blazor WebAssembly-App verwendet den Browser für die Behandlung des HTTP-Datenverkehrs im Hintergrund.</span><span class="sxs-lookup"><span data-stu-id="6e50c-123">The instance of `HttpClient` in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br><span data-ttu-id="6e50c-124">Blazor Server-Apps enthalten standardmäßig kein `HttpClient`, das als Dienst konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="6e50c-124">Blazor Server apps don't include an `HttpClient` configured as a service by default.</span></span> <span data-ttu-id="6e50c-125">Stellen Sie ein `HttpClient` für eine Blazor Server-App bereit.</span><span class="sxs-lookup"><span data-stu-id="6e50c-125">Provide an `HttpClient` to a Blazor Server app.</span></span><br><br><span data-ttu-id="6e50c-126">Weitere Informationen finden Sie unter <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="6e50c-126">For more information, see <xref:blazor/call-web-api>.</span></span> |
| `IJSRuntime` | <span data-ttu-id="6e50c-127">Singleton (Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="6e50c-127">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="6e50c-128">Bereichsbezogen (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="6e50c-128">Scoped (Blazor Server)</span></span> | <span data-ttu-id="6e50c-129">Stellt eine Instanz einer JavaScript-Laufzeit dar, in der JavaScript-Aufrufe verteilt werden.</span><span class="sxs-lookup"><span data-stu-id="6e50c-129">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="6e50c-130">Weitere Informationen finden Sie unter <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="6e50c-130">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| `NavigationManager` | <span data-ttu-id="6e50c-131">Singleton (Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="6e50c-131">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="6e50c-132">Bereichsbezogen (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="6e50c-132">Scoped (Blazor Server)</span></span> | <span data-ttu-id="6e50c-133">Enthält Hilfsprogramme für die Arbeit mit URIs und dem Navigationszustand.</span><span class="sxs-lookup"><span data-stu-id="6e50c-133">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="6e50c-134">Weitere Informationen finden Sie unter [Hilfsprogramme für URI und Navigationszustand](xref:blazor/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="6e50c-134">For more information, see [URI and navigation state helpers](xref:blazor/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="6e50c-135">Ein benutzerdefinierter Dienstanbieter stellt nicht automatisch die in der Tabelle aufgeführten Standarddienste bereit.</span><span class="sxs-lookup"><span data-stu-id="6e50c-135">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="6e50c-136">Wenn Sie einen benutzerdefinierten Dienstanbieter verwenden und einen der Dienste benötigen, die in der Tabelle angezeigt werden, fügen Sie dem neuen Dienstanbieter die erforderlichen Dienste hinzu.</span><span class="sxs-lookup"><span data-stu-id="6e50c-136">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="6e50c-137">Hinzufügen von Diensten zu einer App</span><span class="sxs-lookup"><span data-stu-id="6e50c-137">Add services to an app</span></span>

### <a name="blazor-webassembly"></a><span data-ttu-id="6e50c-138">Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="6e50c-138">Blazor WebAssembly</span></span>

<span data-ttu-id="6e50c-139">Konfigurieren Sie Dienste für die Dienstsammlung der App in der `Main`-Methode von *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="6e50c-139">Configure services for the app's service collection in the `Main` method of *Program.cs*.</span></span> <span data-ttu-id="6e50c-140">Im folgenden Beispiel ist die `MyDependency`-Implementierung für `IMyDependency` registriert:</span><span class="sxs-lookup"><span data-stu-id="6e50c-140">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

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

<span data-ttu-id="6e50c-141">Nachdem der Host erstellt wurde, kann auf die Dienste vom Stammbereich der Abhängigkeitsinjektion aus zugegriffen werden, bevor irgendwelche Komponenten bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="6e50c-141">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="6e50c-142">Dies kann für die Ausführung der Initialisierungslogik vor dem Rendern von Inhalten nützlich sein:</span><span class="sxs-lookup"><span data-stu-id="6e50c-142">This can be useful for running initialization logic before rendering content:</span></span>

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

<span data-ttu-id="6e50c-143">Der Host stellt auch eine zentrale Konfigurationsinstanz für die App zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="6e50c-143">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="6e50c-144">Ausgehend vom vorhergehenden Beispiel wird die URL des Wetterdiensts von einer Standardkonfigurationsquelle (z. B. *appsettings.json*) an `InitializeWeatherAsync` übergeben:</span><span class="sxs-lookup"><span data-stu-id="6e50c-144">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, *appsettings.json*) to `InitializeWeatherAsync`:</span></span>

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

### <a name="blazor-server"></a><span data-ttu-id="6e50c-145">Blazor Server</span><span class="sxs-lookup"><span data-stu-id="6e50c-145">Blazor Server</span></span>

<span data-ttu-id="6e50c-146">Nachdem Sie eine neue App erstellt haben, untersuchen Sie die `Startup.ConfigureServices`-Methode:</span><span class="sxs-lookup"><span data-stu-id="6e50c-146">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="6e50c-147">Der Methode `ConfigureServices` wird ein <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> übergeben, das eine Liste von Dienstdeskriptorobjekten (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>) ist.</span><span class="sxs-lookup"><span data-stu-id="6e50c-147">The `ConfigureServices` method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="6e50c-148">Dienste werden durch die Bereitstellung von Dienstdeskriptoren in die Dienstsammlung aufgenommen.</span><span class="sxs-lookup"><span data-stu-id="6e50c-148">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="6e50c-149">Das folgende Beispiel veranschaulicht das Konzept mit der `IDataAccess`-Schnittstelle und seiner konkreten Implementierung `DataAccess`:</span><span class="sxs-lookup"><span data-stu-id="6e50c-149">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="6e50c-150">Lebensdauer von Diensten</span><span class="sxs-lookup"><span data-stu-id="6e50c-150">Service lifetime</span></span>

<span data-ttu-id="6e50c-151">Die Dienste können mit den in der folgenden Tabelle angegebenen Lebensdauern konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="6e50c-151">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="6e50c-152">Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="6e50c-152">Lifetime</span></span> | <span data-ttu-id="6e50c-153">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="6e50c-153">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | Blazor<span data-ttu-id="6e50c-154"> WebAssembly-Apps verfügen derzeit nicht über ein Konzept für Bereiche von Abhängigkeitsinjektionen.</span><span class="sxs-lookup"><span data-stu-id="6e50c-154"> WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="6e50c-155">`Scoped`-registrierte Dienste verhalten sich wie `Singleton`-Dienste.</span><span class="sxs-lookup"><span data-stu-id="6e50c-155">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="6e50c-156">Das Blazor Server-Hostingmodell unterstützt jedoch die Lebensdauer `Scoped`.</span><span class="sxs-lookup"><span data-stu-id="6e50c-156">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="6e50c-157">In Blazor Server-Apps wird die Registrierung eines bereichsbezogenen Diensts der *Verbindung* zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="6e50c-157">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="6e50c-158">Aus diesem Grund wird die Verwendung von bereichsbezogenen Diensten für Dienste bevorzugt, die dem aktuellen Benutzer zugeordnet werden sollen, auch wenn die aktuelle Absicht darin besteht, clientseitig im Browser ausgeführt zu werden.</span><span class="sxs-lookup"><span data-stu-id="6e50c-158">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | <span data-ttu-id="6e50c-159">Die Abhängigkeitsinjektion erstellt eine *Einzelinstanz* des Diensts.</span><span class="sxs-lookup"><span data-stu-id="6e50c-159">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="6e50c-160">Alle Komponenten, die einen `Singleton`-Dienst erfordern, erhalten eine Instanz desselben Diensts.</span><span class="sxs-lookup"><span data-stu-id="6e50c-160">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | <span data-ttu-id="6e50c-161">Immer wenn eine Komponente eine Instanz eines `Transient`-Diensts aus dem Dienstcontainer erhält, erhält sie eine *neue Instanz* des Diensts.</span><span class="sxs-lookup"><span data-stu-id="6e50c-161">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="6e50c-162">Das Abhängigkeitsinjektionssystem basiert auf dem Abhängigkeitsinjektionssystem in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6e50c-162">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="6e50c-163">Weitere Informationen finden Sie unter <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="6e50c-163">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="6e50c-164">Anfordern eines Diensts in einer Komponente</span><span class="sxs-lookup"><span data-stu-id="6e50c-164">Request a service in a component</span></span>

<span data-ttu-id="6e50c-165">Nachdem die Dienste der Dienstsammlung hinzugefügt wurden, fügen Sie die Dienste mit der Razor-Direktive [\@inject](xref:mvc/views/razor#inject) in die Komponenten ein.</span><span class="sxs-lookup"><span data-stu-id="6e50c-165">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="6e50c-166">`@inject` besitzt zwei Parameter:</span><span class="sxs-lookup"><span data-stu-id="6e50c-166">`@inject` has two parameters:</span></span>

* <span data-ttu-id="6e50c-167">Typ &ndash;: Der Typ des einzufügenden Diensts.</span><span class="sxs-lookup"><span data-stu-id="6e50c-167">Type &ndash; The type of the service to inject.</span></span>
* <span data-ttu-id="6e50c-168">Eigenschaft &ndash;: Der Name der Eigenschaft, die den eingefügten App-Dienst erhält.</span><span class="sxs-lookup"><span data-stu-id="6e50c-168">Property &ndash; The name of the property receiving the injected app service.</span></span> <span data-ttu-id="6e50c-169">Die Eigenschaft erfordert keine manuelle Erstellung.</span><span class="sxs-lookup"><span data-stu-id="6e50c-169">The property doesn't require manual creation.</span></span> <span data-ttu-id="6e50c-170">Der Compiler erstellt die Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="6e50c-170">The compiler creates the property.</span></span>

<span data-ttu-id="6e50c-171">Weitere Informationen finden Sie unter <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="6e50c-171">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="6e50c-172">Verwenden Sie mehrere `@inject`-Anweisungen, um verschiedene Dienste einzufügen.</span><span class="sxs-lookup"><span data-stu-id="6e50c-172">Use multiple `@inject` statements to inject different services.</span></span>

<span data-ttu-id="6e50c-173">Das folgende Beispiel veranschaulicht die Verwendung von `@inject`.</span><span class="sxs-lookup"><span data-stu-id="6e50c-173">The following example shows how to use `@inject`.</span></span> <span data-ttu-id="6e50c-174">Der Dienst, der `Services.IDataAccess` implementiert, wird in die Eigenschaft `DataRepository` der Komponente eingefügt.</span><span class="sxs-lookup"><span data-stu-id="6e50c-174">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="6e50c-175">Beachten Sie, dass der Code nur die Abstraktion `IDataAccess` verwendet:</span><span class="sxs-lookup"><span data-stu-id="6e50c-175">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

<span data-ttu-id="6e50c-176">Intern verwendet die generierte Eigenschaft (`DataRepository`) das Attribut `InjectAttribute`.</span><span class="sxs-lookup"><span data-stu-id="6e50c-176">Internally, the generated property (`DataRepository`) uses the `InjectAttribute` attribute.</span></span> <span data-ttu-id="6e50c-177">In der Regel wird dieses Attribut nicht direkt verwendet.</span><span class="sxs-lookup"><span data-stu-id="6e50c-177">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="6e50c-178">Wenn eine Basisklasse für Komponenten erforderlich ist und die eingefügten Eigenschaften auch für die Basisklasse erforderlich sind, fügen Sie manuell `InjectAttribute` hinzu:</span><span class="sxs-lookup"><span data-stu-id="6e50c-178">If a base class is required for components and injected properties are also required for the base class, manually add the `InjectAttribute`:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="6e50c-179">In Komponenten, die von der Basisklasse abgeleitet sind, ist die `@inject`-Direktive nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="6e50c-179">In components derived from the base class, the `@inject` directive isn't required.</span></span> <span data-ttu-id="6e50c-180">Das `InjectAttribute` der Basisklasse ist ausreichend:</span><span class="sxs-lookup"><span data-stu-id="6e50c-180">The `InjectAttribute` of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="6e50c-181">Verwenden der Abhängigkeitsinjektion in Diensten</span><span class="sxs-lookup"><span data-stu-id="6e50c-181">Use DI in services</span></span>

<span data-ttu-id="6e50c-182">Komplexe Dienste können zusätzliche Dienste erfordern.</span><span class="sxs-lookup"><span data-stu-id="6e50c-182">Complex services might require additional services.</span></span> <span data-ttu-id="6e50c-183">Im vorherigen Beispiel könnte `DataAccess` den Standarddienst `HttpClient` erfordern.</span><span class="sxs-lookup"><span data-stu-id="6e50c-183">In the prior example, `DataAccess` might require the `HttpClient` default service.</span></span> <span data-ttu-id="6e50c-184">`@inject` (oder das `InjectAttribute`) ist nicht für die Verwendung in Diensten verfügbar.</span><span class="sxs-lookup"><span data-stu-id="6e50c-184">`@inject` (or the `InjectAttribute`) isn't available for use in services.</span></span> <span data-ttu-id="6e50c-185">Stattdessen muss die *Constructor Injection* verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="6e50c-185">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="6e50c-186">Erforderliche Dienste werden durch Hinzufügen von Parametern zum Konstruktor des Diensts hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="6e50c-186">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="6e50c-187">Wenn die Abhängigkeitsinjektion den Dienst erstellt, erkennt sie die erforderlichen Dienste im Konstruktor und stellt sie entsprechend zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="6e50c-187">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

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

<span data-ttu-id="6e50c-188">Voraussetzungen für die Constructor Injection:</span><span class="sxs-lookup"><span data-stu-id="6e50c-188">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="6e50c-189">Es muss einen Konstruktor geben, dessen Argumente alle von der Abhängigkeitsinjektion erfüllt werden können.</span><span class="sxs-lookup"><span data-stu-id="6e50c-189">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="6e50c-190">Zusätzliche, nicht durch die Abhängigkeitsinjektion abgedeckte Parameter sind zulässig, wenn sie Standardwerte angeben.</span><span class="sxs-lookup"><span data-stu-id="6e50c-190">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="6e50c-191">Der anwendbare Konstruktor muss *öffentlich* sein.</span><span class="sxs-lookup"><span data-stu-id="6e50c-191">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="6e50c-192">Es muss ein anwendbarer Konstruktor vorhanden sein.</span><span class="sxs-lookup"><span data-stu-id="6e50c-192">One applicable constructor must exist.</span></span> <span data-ttu-id="6e50c-193">Im Falle einer Mehrdeutigkeit löst die Abhängigkeitsinjektion eine Ausnahme aus.</span><span class="sxs-lookup"><span data-stu-id="6e50c-193">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="6e50c-194">Hilfsprogramm-Basiskomponentenklassen zur Verwaltung eines Bereichs für die Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="6e50c-194">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="6e50c-195">In ASP.NET Core-Apps werden bereichsbezogene Dienste in der Regel der aktuellen Anforderung zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="6e50c-195">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="6e50c-196">Nachdem die Anforderung abgeschlossen ist, werden alle bereichsbezogenen oder vorübergehenden Dienste vom Abhängigkeitsinjektionssystem entsorgt.</span><span class="sxs-lookup"><span data-stu-id="6e50c-196">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="6e50c-197">In Blazor Server-Apps besteht der Anforderungsbereich für die Dauer der Clientverbindung, was dazu führen kann, dass vorübergehende und bereichsbezogene Dienste viel länger als erwartet leben.</span><span class="sxs-lookup"><span data-stu-id="6e50c-197">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="6e50c-198">In Blazor WebAssembly-Apps werden Dienste, die mit einer bereichsbezogenen Lebensdauer registriert sind, als Singletons behandelt, sodass sie länger leben als bereichsbezogene Dienste in typischen ASP.NET Core-Apps.</span><span class="sxs-lookup"><span data-stu-id="6e50c-198">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

<span data-ttu-id="6e50c-199">Ein Ansatz, der die Lebensdauer eines Diensts in Blazor-Apps begrenzt, ist die Verwendung des Typs `OwningComponentBase`.</span><span class="sxs-lookup"><span data-stu-id="6e50c-199">An approach that limits a service lifetime in Blazor apps is use of the `OwningComponentBase` type.</span></span> <span data-ttu-id="6e50c-200">`OwningComponentBase` ist ein abstrakter, von `ComponentBase` abgeleiteter Typ, der einen der Lebensdauer der Komponente entsprechenden Bereich für die Abhängigkeitsinjektion erstellt.</span><span class="sxs-lookup"><span data-stu-id="6e50c-200">`OwningComponentBase` is an abstract type derived from `ComponentBase` that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="6e50c-201">Mit diesem Bereich ist es möglich, Abhängigkeitsinjektionsdienste mit einer bereichsbezogenen Lebensdauer zu nutzen und sie so lange wie die Komponente zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="6e50c-201">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="6e50c-202">Wenn die Komponente zerstört wird, werden auch die Dienste des bereichsbezogenen Dienstanbieters der Komponente entsorgt.</span><span class="sxs-lookup"><span data-stu-id="6e50c-202">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="6e50c-203">Dies kann für Dienste nützlich sein, für die Folgendes gilt:</span><span class="sxs-lookup"><span data-stu-id="6e50c-203">This can be useful for services that:</span></span>

* <span data-ttu-id="6e50c-204">Sie sollten innerhalb einer Komponente wiederverwendet werden, da die vorübergehende Lebensdauer unangemessen ist.</span><span class="sxs-lookup"><span data-stu-id="6e50c-204">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="6e50c-205">Sie sollten nicht komponentenübergreifend freigegeben werden, da die Singleton-Lebensdauer unangemessen ist.</span><span class="sxs-lookup"><span data-stu-id="6e50c-205">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="6e50c-206">Zwei Versionen des Typs `OwningComponentBase` sind verfügbar:</span><span class="sxs-lookup"><span data-stu-id="6e50c-206">Two versions of the `OwningComponentBase` type are available:</span></span>

* <span data-ttu-id="6e50c-207">`OwningComponentBase` ist ein abstraktes, verwerfbares, untergeordnetes Element vom Typ `ComponentBase` mit einer geschützten `ScopedServices`-Eigenschaft vom Typ `IServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="6e50c-207">`OwningComponentBase` is an abstract, disposable child of the `ComponentBase` type with a protected `ScopedServices` property of type `IServiceProvider`.</span></span> <span data-ttu-id="6e50c-208">Dieser Anbieter kann zur Auflösung von Diensten verwendet werden, die der Lebensdauer der Komponente zugeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="6e50c-208">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="6e50c-209">Abhängigkeitsinjektionsdienste, die mit `@inject` oder `InjectAttribute` (`[Inject]`) in die Komponente eingefügt werden, werden nicht im Gültigkeitsbereich der Komponente erstellt.</span><span class="sxs-lookup"><span data-stu-id="6e50c-209">DI services injected into the component using `@inject` or the `InjectAttribute` (`[Inject]`) aren't created in the component's scope.</span></span> <span data-ttu-id="6e50c-210">Um den Bereich der Komponente zu verwenden, müssen die Dienste mit `ScopedServices.GetRequiredService` oder `ScopedServices.GetService` aufgelöst werden.</span><span class="sxs-lookup"><span data-stu-id="6e50c-210">To use the component's scope, services must be resolved using `ScopedServices.GetRequiredService` or `ScopedServices.GetService`.</span></span> <span data-ttu-id="6e50c-211">Allen Diensten, die unter Verwendung des `ScopedServices`-Anbieters aufgelöst werden, werden ihre Abhängigkeiten aus demselben Bereich bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="6e50c-211">Any services resolved using the `ScopedServices` provider have their dependencies provided from that same scope.</span></span>

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

* <span data-ttu-id="6e50c-212">`OwningComponentBase<T>` ist abgeleitet von `OwningComponentBase` und fügt eine Eigenschaft `Service` hinzu, die eine Instanz von `T` vom bereichsbezogenen Abhängigkeitsinjektionsanbieter zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="6e50c-212">`OwningComponentBase<T>` derives from `OwningComponentBase` and adds a property `Service` that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="6e50c-213">Dieser Typ ist eine komfortable Methode für den Zugriff auf bereichsbezogene Dienste, ohne eine Instanz von `IServiceProvider` zu verwenden, wenn es einen primären Dienst gibt, den die Anwendung aus dem Abhängigkeitsinjektionscontainer unter Verwendung des Bereichs der Komponente benötigt.</span><span class="sxs-lookup"><span data-stu-id="6e50c-213">This type is a convenient way to access scoped services without using an instance of `IServiceProvider` when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="6e50c-214">Die Eigenschaft `ScopedServices` ist verfügbar, sodass die App bei Bedarf auch Dienste mit anderen Typen erhalten kann.</span><span class="sxs-lookup"><span data-stu-id="6e50c-214">The `ScopedServices` property is available, so the app can get services of other types, if necessary.</span></span>

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

## <a name="use-of-entity-framework-dbcontext-from-di"></a><span data-ttu-id="6e50c-215">Verwenden von Entity Framework-DbContext von der Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="6e50c-215">Use of Entity Framework DbContext from DI</span></span>

<span data-ttu-id="6e50c-216">Ein allgemeiner Diensttyp zum Abrufen der Abhängigkeitsinjektion in Web-Apps sind EF-`DbContext`-Objekte (Entity Framework).</span><span class="sxs-lookup"><span data-stu-id="6e50c-216">One common service type to retrieve from DI in web apps is Entity Framework (EF) `DbContext` objects.</span></span> <span data-ttu-id="6e50c-217">Beim Registrieren von EF-Diensten mithilfe von `IServiceCollection.AddDbContext` wird `DbContext` standardmäßig als bereichsbezogener Dienst hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="6e50c-217">Registering EF services using `IServiceCollection.AddDbContext` adds the `DbContext` as a scoped service by default.</span></span> <span data-ttu-id="6e50c-218">Die Registrierung als bereichsbezogener Dienst kann zu Problemen in Blazor-Apps führen, da sie dazu führt, dass `DbContext`-Instanzen langlebig sind und in der gesamten App gemeinsam genutzt werden.</span><span class="sxs-lookup"><span data-stu-id="6e50c-218">Registering as a scoped service can lead to problems in Blazor apps because it causes `DbContext` instances to be long-lived and shared across the app.</span></span> <span data-ttu-id="6e50c-219">`DbContext` ist nicht threadsicher und darf nicht gleichzeitig verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="6e50c-219">`DbContext` isn't thread-safe and must not be used concurrently.</span></span>

<span data-ttu-id="6e50c-220">Je nach App kann die Verwendung von `OwningComponentBase` zur Begrenzung des Bereichs von `DbContext` auf eine einzelne Komponente das Problem *möglicherweise* lösen.</span><span class="sxs-lookup"><span data-stu-id="6e50c-220">Depending on the app, using `OwningComponentBase` to limit the scope of a `DbContext` to a single component *may* solve the issue.</span></span> <span data-ttu-id="6e50c-221">Wenn eine Komponente nicht parallel ein `DbContext` verwendet, reicht es aus, die Komponente aus `OwningComponentBase` abzuleiten und den `DbContext` aus `ScopedServices` abzurufen, da Folgendes sichergestellt wird:</span><span class="sxs-lookup"><span data-stu-id="6e50c-221">If a component doesn't use a `DbContext` in parallel, deriving the component from `OwningComponentBase` and retrieving the `DbContext` from `ScopedServices` is sufficient because it ensures that:</span></span>

* <span data-ttu-id="6e50c-222">Separate Komponenten teilen sich kein `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="6e50c-222">Separate components don't share a `DbContext`.</span></span>
* <span data-ttu-id="6e50c-223">Der `DbContext` lebt nur so lange, wie die von ihm abhängige Komponente.</span><span class="sxs-lookup"><span data-stu-id="6e50c-223">The `DbContext` lives only as long as the component depending on it.</span></span>

<span data-ttu-id="6e50c-224">Wenn eine einzelne Komponente gleichzeitig einen `DbContext` verwenden könnte (z. B. jedes Mal, wenn ein Benutzer eine Schaltfläche auswählt), vermeidet selbst die Verwendung von `OwningComponentBase` die Probleme mit gleichzeitigen EF-Vorgängen nicht.</span><span class="sxs-lookup"><span data-stu-id="6e50c-224">If a single component might use a `DbContext` concurrently (for example, every time a user selects a button), even using `OwningComponentBase` doesn't avoid issues with concurrent EF operations.</span></span> <span data-ttu-id="6e50c-225">Verwenden Sie in diesem Fall für jeden logischen EF-Vorgang einen anderen `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="6e50c-225">In that case, use a different `DbContext` for each logical EF operation.</span></span> <span data-ttu-id="6e50c-226">Verwenden Sie einen der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="6e50c-226">Use either of the following approaches:</span></span>

* <span data-ttu-id="6e50c-227">Erstellen Sie den `DbContext` direkt mit `DbContextOptions<TContext>` als Argument, das von der Abhängigkeitsinjektion abgerufen werden kann und threadsicher ist.</span><span class="sxs-lookup"><span data-stu-id="6e50c-227">Create the `DbContext` directly using `DbContextOptions<TContext>` as an argument, which can be retrieved from DI and is thread safe.</span></span>

    ```razor
    @page "/example"
    @inject DbContextOptions<AppDbContext> DbContextOptions

    <ul>
        @foreach (var item in _data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> _data = new List<string>();

        private async Task LoadData()
        {
            _data = await GetAsync();
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

* <span data-ttu-id="6e50c-228">Registrieren Sie den `DbContext` im Dienstcontainer mit einer vorübergehenden Lebensdauer:</span><span class="sxs-lookup"><span data-stu-id="6e50c-228">Register the `DbContext` in the service container with a transient lifetime:</span></span>
  * <span data-ttu-id="6e50c-229">Verwenden Sie bei der Registrierung des Kontexts `ServiceLifetime.Transient`.</span><span class="sxs-lookup"><span data-stu-id="6e50c-229">When registering the context, use `ServiceLifetime.Transient`.</span></span> <span data-ttu-id="6e50c-230">Die `AddDbContext`-Erweiterungsmethode nimmt zwei optionale Parameter vom Typ `ServiceLifetime` an.</span><span class="sxs-lookup"><span data-stu-id="6e50c-230">The `AddDbContext` extension method takes two optional parameters of type `ServiceLifetime`.</span></span> <span data-ttu-id="6e50c-231">Um diesen Ansatz zu verwenden, muss nur der Parameter `contextLifetime` entsprechend `ServiceLifetime.Transient` sein.</span><span class="sxs-lookup"><span data-stu-id="6e50c-231">To use this approach, only the `contextLifetime` parameter needs to be `ServiceLifetime.Transient`.</span></span> <span data-ttu-id="6e50c-232">`optionsLifetime` kann seinen Standardwert von `ServiceLifetime.Scoped` beibehalten.</span><span class="sxs-lookup"><span data-stu-id="6e50c-232">`optionsLifetime` can keep its default value of `ServiceLifetime.Scoped`.</span></span>

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * <span data-ttu-id="6e50c-233">Der vorübergehende `DbContext` kann wie üblich (mit `@inject`) in Komponenten eingefügt werden, die nicht mehrere EF-Vorgänge parallel ausführen werden.</span><span class="sxs-lookup"><span data-stu-id="6e50c-233">The transient `DbContext` can be injected as normal (using `@inject`) into components that will not execute multiple EF operations in parallel.</span></span> <span data-ttu-id="6e50c-234">Diejenigen, die mehrere EF-Vorgänge gleichzeitig durchführen können, können mit `DbContext` separate `IServiceProvider.GetRequiredService`-Objekte für jeden parallelen Vorgang anfordern.</span><span class="sxs-lookup"><span data-stu-id="6e50c-234">Those that may perform multiple EF operations simultaneously can request separate `DbContext` objects for each parallel operation using `IServiceProvider.GetRequiredService`.</span></span>

    ```razor
    @page "/example"
    @using Microsoft.Extensions.DependencyInjection
    @inject IServiceProvider ServiceProvider

    <ul>
        @foreach (var item in _data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> _data = new List<string>();

        private async Task LoadData()
        {
            _data = await GetAsync();
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

## <a name="additional-resources"></a><span data-ttu-id="6e50c-235">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="6e50c-235">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
