---
title: Dependency Injection in ASP.NET Core
author: guardrex
description: Erfahren Sie, wie ASP.NET Core Dependency Injection implementiert und wie Sie dieses Muster verwenden können.
ms.author: riande
ms.custom: mvc
ms.date: 02/25/2019
uid: fundamentals/dependency-injection
ms.openlocfilehash: 72e3ae1c4be18edaa2e7f1906cf65269e5ed2288
ms.sourcegitcommit: 191d21c1e37b56f0df0187e795d9a56388bbf4c7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/08/2019
ms.locfileid: "57665495"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="51342-103">Dependency Injection in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="51342-103">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="51342-104">Von [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="51342-104">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="51342-105">ASP.NET Core unterstützt das Softwareentwurfsmuster Abhängigkeitsinjektion. Damit kann eine [Umkehrung der Steuerung](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) (Inversion of Control, IoC) zwischen Klassen und ihren Abhängigkeiten erreicht werden.</span><span class="sxs-lookup"><span data-stu-id="51342-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="51342-106">Weitere Informationen zur Abhängigkeitsinjektion innerhalb von MVC-Controllern finden Sie unter <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="51342-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="51342-107">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="51342-107">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="51342-108">Übersicht über Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="51342-108">Overview of dependency injection</span></span>

<span data-ttu-id="51342-109">Eine *Abhängigkeit* ist ein beliebiges Objekt, das ein anderes Objekt benötigt.</span><span class="sxs-lookup"><span data-stu-id="51342-109">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="51342-110">Überprüfen Sie die folgende `MyDependency`-Klasse mit einer `WriteMessage`-Methode, von der andere Klassen in einer App abhängen:</span><span class="sxs-lookup"><span data-stu-id="51342-110">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="51342-111">Eine Instanz der Klasse `MyDependency` kann erstellt werden, um die `WriteMessage`-Methode einer Klasse zur Verfügung zu stellen.</span><span class="sxs-lookup"><span data-stu-id="51342-111">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="51342-112">Die Klasse `MyDependency` ist eine Abhängigkeit der Klasse `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="51342-112">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="51342-113">Eine Instanz der Klasse `MyDependency` kann erstellt werden, um die `WriteMessage`-Methode einer Klasse zur Verfügung zu stellen.</span><span class="sxs-lookup"><span data-stu-id="51342-113">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="51342-114">Die Klasse `MyDependency` ist eine Abhängigkeit der Klasse `HomeController`:</span><span class="sxs-lookup"><span data-stu-id="51342-114">The `MyDependency` class is a dependency of the `HomeController` class:</span></span>

```csharp
public class HomeController : Controller
{
    MyDependency _dependency = new MyDependency();

    public async Task<IActionResult> Index()
    {
        await _dependency.WriteMessage(
            "HomeController.Index created this message.");

        return View();
    }
}
```

::: moniker-end

<span data-ttu-id="51342-115">Die Klasse erstellt die Instanz `MyDependency` und hängt direkt von dieser ab.</span><span class="sxs-lookup"><span data-stu-id="51342-115">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="51342-116">Codeabhängigkeiten (wie im vorherigen Beispiel) sind problematisch und sollten aus folgenden Gründen vermieden werden:</span><span class="sxs-lookup"><span data-stu-id="51342-116">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="51342-117">Um `MyDependency` durch eine andere Implementierung zu ersetzen, muss die Klasse geändert werden.</span><span class="sxs-lookup"><span data-stu-id="51342-117">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="51342-118">Wenn `MyDependency` über Abhängigkeiten verfügt, müssen diese von der Klasse konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="51342-118">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="51342-119">In einem großen Projekt mit mehreren Klassen, die von `MyDependency` abhängig sind, wird der Konfigurationscode über die App verteilt.</span><span class="sxs-lookup"><span data-stu-id="51342-119">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="51342-120">Diese Implementierung ist nicht für Komponententests geeignet.</span><span class="sxs-lookup"><span data-stu-id="51342-120">This implementation is difficult to unit test.</span></span> <span data-ttu-id="51342-121">Die App sollte eine `MyDependency`-Modell- oder Stubklasse verwenden, was mit diesem Ansatz nicht möglich ist.</span><span class="sxs-lookup"><span data-stu-id="51342-121">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="51342-122">Die Abhängigkeitsinjektion löst dieses Problem mithilfe der folgenden Schritte:</span><span class="sxs-lookup"><span data-stu-id="51342-122">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="51342-123">Die Verwendung einer Schnittstelle zur Abstraktion der Abhängigkeitsimplementierung.</span><span class="sxs-lookup"><span data-stu-id="51342-123">The use of an interface to abstract the dependency implementation.</span></span>
* <span data-ttu-id="51342-124">Registrierung der Abhängigkeit in einem Dienstcontainer.</span><span class="sxs-lookup"><span data-stu-id="51342-124">Registration of the dependency in a service container.</span></span> <span data-ttu-id="51342-125">ASP.NET Core stellt einen integrierten Dienstcontainer ([IServiceProvider](/dotnet/api/system.iserviceprovider)) bereit.</span><span class="sxs-lookup"><span data-stu-id="51342-125">ASP.NET Core provides a built-in service container, [IServiceProvider](/dotnet/api/system.iserviceprovider).</span></span> <span data-ttu-id="51342-126">Die Dienste werden in der App-Methode `Startup.ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="51342-126">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="51342-127">Die *Injektion* des Diensts in den Konstruktor der Klasse, wo er verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="51342-127">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="51342-128">Das Framework erstellt eine Instanz der Abhängigkeit und entfernt diese, wenn sie nicht mehr benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="51342-128">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="51342-129">In der [Beispiel-App](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) definiert die `IMyDependency`-Schnittstelle eine Methode, die der Dienst für die App bereitstellt:</span><span class="sxs-lookup"><span data-stu-id="51342-129">In the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="51342-130">Diese Schnittstelle wird durch einen konkreten Typ (`MyDependency`) implementiert:</span><span class="sxs-lookup"><span data-stu-id="51342-130">This interface is implemented by a concrete type, `MyDependency`:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="51342-131">`MyDependency` fordert [ILogger&lt;TCategoryName&gt;](/dotnet/api/microsoft.extensions.logging.ilogger-1) im Konstruktor an.</span><span class="sxs-lookup"><span data-stu-id="51342-131">`MyDependency` requests an [ILogger&lt;TCategoryName&gt;](/dotnet/api/microsoft.extensions.logging.ilogger-1) in its constructor.</span></span> <span data-ttu-id="51342-132">Die Abhängigkeitsinjektion wird häufig als Verkettung verwendet.</span><span class="sxs-lookup"><span data-stu-id="51342-132">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="51342-133">Jede angeforderte Abhängigkeit fordert wiederum ihre eigenen Abhängigkeiten an.</span><span class="sxs-lookup"><span data-stu-id="51342-133">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="51342-134">Der Container löst die Abhängigkeiten im Diagramm auf und gibt den vollständig aufgelösten Dienst zurück.</span><span class="sxs-lookup"><span data-stu-id="51342-134">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="51342-135">Die gesammelten aufzulösenden Abhängigkeiten werden als *Abhängigkeitsstruktur*, *Abhängigkeitsdiagramm* oder *Objektdiagramm* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="51342-135">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="51342-136">`IMyDependency` und `ILogger<TCategoryName>` müssen im Dienstcontainer registriert werden.</span><span class="sxs-lookup"><span data-stu-id="51342-136">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="51342-137">`IMyDependency` ist in `Startup.ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="51342-137">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="51342-138">`ILogger<TCategoryName>` wird von der Protokollierungsabstraktionsinfrastruktur registriert. Es handelt sich also um einen [von einem Framework bereitgestellten Dienst](#framework-provided-services), der standardmäßig vom Framework registriert wird.</span><span class="sxs-lookup"><span data-stu-id="51342-138">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="51342-139">In der Beispiel-App ist der Dienst `IMyDependency` mit dem konkreten Typ `MyDependency` registriert.</span><span class="sxs-lookup"><span data-stu-id="51342-139">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="51342-140">Die Registrierung schränkt die Lebensdauer des Diensts auf die Lebensdauer einer einzelnen Anforderung ein.</span><span class="sxs-lookup"><span data-stu-id="51342-140">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="51342-141">Auf die [Dienstlebensdauer](#service-lifetimes) wird später in diesem Artikel eingegangen.</span><span class="sxs-lookup"><span data-stu-id="51342-141">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="51342-142">Jede Erweiterungsmethode vom Typ `services.Add{SERVICE_NAME}` fügt Dienste hinzu (und konfiguriert diese möglicherweise).</span><span class="sxs-lookup"><span data-stu-id="51342-142">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="51342-143">So fügt beispielsweise `services.AddMvc()` die erforderlichen Dienste Razor Pages und MVC hinzu.</span><span class="sxs-lookup"><span data-stu-id="51342-143">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="51342-144">Es wird empfohlen, dass Apps dieser Konvention folgen.</span><span class="sxs-lookup"><span data-stu-id="51342-144">We recommended that apps follow this convention.</span></span> <span data-ttu-id="51342-145">Platzieren Sie Erweiterungsmethoden im Namespace [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection), um Gruppen von Dienstregistrierungen zu kapseln.</span><span class="sxs-lookup"><span data-stu-id="51342-145">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="51342-146">Wenn für den Dienstkonstruktor ein Grundtyp erforderlich ist, z. B. `string`, kann dieser über [Konfiguration](xref:fundamentals/configuration/index) und das [Optionsmuster](xref:fundamentals/configuration/options) eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="51342-146">If the service's constructor requires a primitive, such as a `string`, the primitive can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

<span data-ttu-id="51342-147">Eine Instanz des Diensts wird über den Konstruktor einer Klasse angefordert, in der der Dienst verwendet wird, und einem privaten Feld zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="51342-147">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="51342-148">Das Feld wird verwendet, um auf den Dienst bei Bedarf über die gesamte Klasse zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="51342-148">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="51342-149">In der Beispiel-App wird die Instanz `IMyDependency` angefordert, und mit ihr wird die App-Methode `WriteMessage` aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="51342-149">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Controllers/MyDependencyController.cs?name=snippet1&highlight=3,5-8,13-14)]

::: moniker-end

## <a name="framework-provided-services"></a><span data-ttu-id="51342-150">Von Frameworks bereitgestellte Dienste</span><span class="sxs-lookup"><span data-stu-id="51342-150">Framework-provided services</span></span>

<span data-ttu-id="51342-151">Die Methode `Startup.ConfigureServices` definiert die von der App verwendeten Dienste. Dies beinhaltet Plattformfunktionen wie Entity Framework Core und ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="51342-151">The `Startup.ConfigureServices` method is responsible for defining the services the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="51342-152">Zunächst enthält die in `ConfigureServices` bereitgestellte `IServiceCollection` folgende definierten Dienste (abhängig von der [Vorgehensweise bei der Konfiguration des Hosts](xref:fundamentals/index#host)):</span><span class="sxs-lookup"><span data-stu-id="51342-152">Initially, the `IServiceCollection` provided to `ConfigureServices` has the following services defined (depending on [how the host was configured](xref:fundamentals/index#host)):</span></span>

| <span data-ttu-id="51342-153">Diensttyp</span><span class="sxs-lookup"><span data-stu-id="51342-153">Service Type</span></span> | <span data-ttu-id="51342-154">Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="51342-154">Lifetime</span></span> |
| ------------ | -------- |
| [<span data-ttu-id="51342-155">Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory</span><span class="sxs-lookup"><span data-stu-id="51342-155">Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.builder.iapplicationbuilderfactory) | <span data-ttu-id="51342-156">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="51342-156">Transient</span></span> |
| [<span data-ttu-id="51342-157">Microsoft.AspNetCore.Hosting.IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="51342-157">Microsoft.AspNetCore.Hosting.IApplicationLifetime</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) | <span data-ttu-id="51342-158">Singleton</span><span class="sxs-lookup"><span data-stu-id="51342-158">Singleton</span></span> |
| [<span data-ttu-id="51342-159">Microsoft.AspNetCore.Hosting.IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="51342-159">Microsoft.AspNetCore.Hosting.IHostingEnvironment</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) | <span data-ttu-id="51342-160">Singleton</span><span class="sxs-lookup"><span data-stu-id="51342-160">Singleton</span></span> |
| [<span data-ttu-id="51342-161">Microsoft.AspNetCore.Hosting.IStartup</span><span class="sxs-lookup"><span data-stu-id="51342-161">Microsoft.AspNetCore.Hosting.IStartup</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.istartup) | <span data-ttu-id="51342-162">Singleton</span><span class="sxs-lookup"><span data-stu-id="51342-162">Singleton</span></span> |
| [<span data-ttu-id="51342-163">Microsoft.AspNetCore.Hosting.IStartupFilter</span><span class="sxs-lookup"><span data-stu-id="51342-163">Microsoft.AspNetCore.Hosting.IStartupFilter</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.istartupfilter) | <span data-ttu-id="51342-164">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="51342-164">Transient</span></span> |
| [<span data-ttu-id="51342-165">Microsoft.AspNetCore.Hosting.Server.IServer</span><span class="sxs-lookup"><span data-stu-id="51342-165">Microsoft.AspNetCore.Hosting.Server.IServer</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.server.iserver) | <span data-ttu-id="51342-166">Singleton</span><span class="sxs-lookup"><span data-stu-id="51342-166">Singleton</span></span> |
| [<span data-ttu-id="51342-167">Microsoft.AspNetCore.Http.IHttpContextFactory</span><span class="sxs-lookup"><span data-stu-id="51342-167">Microsoft.AspNetCore.Http.IHttpContextFactory</span></span>](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextfactory) | <span data-ttu-id="51342-168">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="51342-168">Transient</span></span> |
| [<span data-ttu-id="51342-169">Microsoft.Extensions.Logging.ILogger&lt;T&gt;</span><span class="sxs-lookup"><span data-stu-id="51342-169">Microsoft.Extensions.Logging.ILogger&lt;T&gt;</span></span>](/dotnet/api/microsoft.extensions.logging.ilogger) | <span data-ttu-id="51342-170">Singleton</span><span class="sxs-lookup"><span data-stu-id="51342-170">Singleton</span></span> |
| [<span data-ttu-id="51342-171">Microsoft.Extensions.Logging.ILoggerFactory</span><span class="sxs-lookup"><span data-stu-id="51342-171">Microsoft.Extensions.Logging.ILoggerFactory</span></span>](/dotnet/api/microsoft.extensions.logging.iloggerfactory) | <span data-ttu-id="51342-172">Singleton</span><span class="sxs-lookup"><span data-stu-id="51342-172">Singleton</span></span> |
| [<span data-ttu-id="51342-173">Microsoft.Extensions.ObjectPool.ObjectPoolProvider</span><span class="sxs-lookup"><span data-stu-id="51342-173">Microsoft.Extensions.ObjectPool.ObjectPoolProvider</span></span>](/dotnet/api/microsoft.extensions.objectpool.objectpoolprovider) | <span data-ttu-id="51342-174">Singleton</span><span class="sxs-lookup"><span data-stu-id="51342-174">Singleton</span></span> |
| [<span data-ttu-id="51342-175">Microsoft.Extensions.Options.IConfigureOptions&lt;T&gt;</span><span class="sxs-lookup"><span data-stu-id="51342-175">Microsoft.Extensions.Options.IConfigureOptions&lt;T&gt;</span></span>](/dotnet/api/microsoft.extensions.options.iconfigureoptions-1) | <span data-ttu-id="51342-176">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="51342-176">Transient</span></span> |
| [<span data-ttu-id="51342-177">Microsoft.Extensions.Options.IOptions&lt;T&gt;</span><span class="sxs-lookup"><span data-stu-id="51342-177">Microsoft.Extensions.Options.IOptions&lt;T&gt;</span></span>](/dotnet/api/microsoft.extensions.options.ioptions-1) | <span data-ttu-id="51342-178">Singleton</span><span class="sxs-lookup"><span data-stu-id="51342-178">Singleton</span></span> |
| [<span data-ttu-id="51342-179">System.Diagnostics.DiagnosticSource</span><span class="sxs-lookup"><span data-stu-id="51342-179">System.Diagnostics.DiagnosticSource</span></span>](/dotnet/core/api/system.diagnostics.diagnosticsource) | <span data-ttu-id="51342-180">Singleton</span><span class="sxs-lookup"><span data-stu-id="51342-180">Singleton</span></span> |
| [<span data-ttu-id="51342-181">System.Diagnostics.DiagnosticListener</span><span class="sxs-lookup"><span data-stu-id="51342-181">System.Diagnostics.DiagnosticListener</span></span>](/dotnet/core/api/system.diagnostics.diagnosticlistener) | <span data-ttu-id="51342-182">Singleton</span><span class="sxs-lookup"><span data-stu-id="51342-182">Singleton</span></span> |

<span data-ttu-id="51342-183">Wenn eine Dienstsammlungs-Erweiterungsmethode verfügbar ist, um einen Dienst (und ggf. seine abhängigen Dienste) zu registrieren, ist die Konvention, eine einzige `Add{SERVICE_NAME}`-Erweiterungsmethode zu verwenden, um alle von diesem Dienst benötigten Dienste zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="51342-183">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="51342-184">Der folgende Code ist ein Beispiel für das Hinzufügen zusätzlicher Dienste zum Container mit den Erweiterungsmethoden [AddDbContext](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext), [AddIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.addidentity) und [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc):</span><span class="sxs-lookup"><span data-stu-id="51342-184">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext), [AddIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.addidentity), and [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddMvc();
}
```

<span data-ttu-id="51342-185">Weitere Informationen finden Sie im Artikel [ServiceCollection-Klasse](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollection) in der API-Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="51342-185">For more information, see the [ServiceCollection Class](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollection) in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="51342-186">Dienstlebensdauer</span><span class="sxs-lookup"><span data-stu-id="51342-186">Service lifetimes</span></span>

<span data-ttu-id="51342-187">Wählen Sie eine geeignete Lebensdauer für jeden registrierten Dienst aus.</span><span class="sxs-lookup"><span data-stu-id="51342-187">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="51342-188">ASP.NET Core-Dienste können mit folgender Lebensdauer konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="51342-188">ASP.NET Core services can be configured with the following lifetimes:</span></span>

<span data-ttu-id="51342-189">**Transient** (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="51342-189">**Transient**</span></span>

<span data-ttu-id="51342-190">Dienste mit vorübergehender Lebensdauer werden bei jeder Anforderung neu erstellt.</span><span class="sxs-lookup"><span data-stu-id="51342-190">Transient lifetime services are created each time they're requested.</span></span> <span data-ttu-id="51342-191">Diese Lebensdauer ist am besten für einfache, zustandslose Dienste geeignet.</span><span class="sxs-lookup"><span data-stu-id="51342-191">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="51342-192">**Scoped** (bereichsbezogen)</span><span class="sxs-lookup"><span data-stu-id="51342-192">**Scoped**</span></span>

<span data-ttu-id="51342-193">Dienste mit bereichsbezogener Lebensdauer werden einmal pro Anforderung erstellt.</span><span class="sxs-lookup"><span data-stu-id="51342-193">Scoped lifetime services are created once per request.</span></span>

> [!WARNING]
> <span data-ttu-id="51342-194">Wenn Sie einen bereichsbezogenen Dienst in einer Middleware verwenden, müssen Sie den Dienst in die `Invoke`- oder `InvokeAsync`-Methode einfügen.</span><span class="sxs-lookup"><span data-stu-id="51342-194">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="51342-195">Fügen Sie ihn nicht über Constructor Injection ein, da hierdurch der Dienst ein Verhalten wie ein Singleton zeigt.</span><span class="sxs-lookup"><span data-stu-id="51342-195">Don't inject via constructor injection because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="51342-196">Weitere Informationen finden Sie unter <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="51342-196">For more information, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="51342-197">**Singleton**</span><span class="sxs-lookup"><span data-stu-id="51342-197">**Singleton**</span></span>

<span data-ttu-id="51342-198">Dienste mit einer Singletonlebensdauer werden bei der ersten Anforderung erstellt (bzw. wenn `ConfigureServices` ausgeführt wird, und eine Instanz bei der Dienstregistrierung angegeben wird).</span><span class="sxs-lookup"><span data-stu-id="51342-198">Singleton lifetime services are created the first time they're requested (or when `ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="51342-199">Jeder nachfolgenden Anforderung verwendet die gleiche Instanz.</span><span class="sxs-lookup"><span data-stu-id="51342-199">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="51342-200">Wenn die App ein Verhalten vom Typ „Singleton“ erfordert, wird empfohlen, den Dienstcontainer die Dienstlebensdauer verwalten zu lassen.</span><span class="sxs-lookup"><span data-stu-id="51342-200">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="51342-201">Implementieren Sie nicht das Designmuster „Singleton“ und stellen Sie Benutzercode bereit, um die Objektlebensdauer in der Klasse zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="51342-201">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="51342-202">Es ist riskant, einen bereichsbezogenen Dienst über ein Singleton aufzulösen.</span><span class="sxs-lookup"><span data-stu-id="51342-202">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="51342-203">Möglicherweise weist der Dienst bei der Verarbeitung nachfolgender Anforderungen einen falschen Status auf.</span><span class="sxs-lookup"><span data-stu-id="51342-203">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="51342-204">Verhalten von Constructor Injection</span><span class="sxs-lookup"><span data-stu-id="51342-204">Constructor injection behavior</span></span>

<span data-ttu-id="51342-205">Dienste können durch zwei Mechanismen aufgelöst werden:</span><span class="sxs-lookup"><span data-stu-id="51342-205">Services can be resolved by two mechanisms:</span></span>

* `IServiceProvider`
* <span data-ttu-id="51342-206">[ActivatorUtilities](/dotnet/api/microsoft.extensions.dependencyinjection.activatorutilities) &ndash; lässt die Erstellung von Objekten ohne Dienstregistrierung im Abhängigkeitsinjektionscontainer zu.</span><span class="sxs-lookup"><span data-stu-id="51342-206">[ActivatorUtilities](/dotnet/api/microsoft.extensions.dependencyinjection.activatorutilities) &ndash; Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="51342-207">`ActivatorUtilities` wird mit Abstraktionen für Benutzer verwendet. Dazu zählen Taghilfsprogramme, MVC-Controller und Modellbindungen.</span><span class="sxs-lookup"><span data-stu-id="51342-207">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="51342-208">Konstruktoren können Argumente akzeptieren, die nicht durch Abhängigkeitsinjektion bereitgestellt werden. Die Argumente müssen jedoch Standardwerte zuweisen.</span><span class="sxs-lookup"><span data-stu-id="51342-208">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="51342-209">Wenn Dienste durch `IServiceProvider` oder `ActivatorUtilities` aufgelöst werden, benötigt Constructor Injection einen *öffentlichen* Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="51342-209">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, constructor injection requires a *public* constructor.</span></span>

<span data-ttu-id="51342-210">Wenn Dienste durch `ActivatorUtilities` aufgelöst werden, erfordert Constructor Injection, dass nur ein anwendbarer Konstruktor vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="51342-210">When services are resolved by `ActivatorUtilities`, constructor injection requires that only one applicable constructor exists.</span></span> <span data-ttu-id="51342-211">Konstruktorüberladungen werden unterstützt. Es darf jedoch nur eine Überladung vorhanden sein, deren Argumente alle durch Dependency Injection erfüllt werden können.</span><span class="sxs-lookup"><span data-stu-id="51342-211">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="51342-212">Entity Framework-Kontexte</span><span class="sxs-lookup"><span data-stu-id="51342-212">Entity Framework contexts</span></span>

<span data-ttu-id="51342-213">Entity Framework-Kontexte werden einem Dienstcontainer normalerweise mithilfe der [bereichsbezogenen Lebensdauer](#service-lifetimes) hinzugefügt, da Datenbankvorgänge von Web-Apps normalerweise auf den Abfragebereich bezogen werden.</span><span class="sxs-lookup"><span data-stu-id="51342-213">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the request.</span></span> <span data-ttu-id="51342-214">Der Bereich einer Standardlebensdauer wird festgelegt, wenn eine Lebensdauer nicht von einer <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext*>-Überladung angegeben wird, wenn der Datenbankkontext registriert wird.</span><span class="sxs-lookup"><span data-stu-id="51342-214">The default lifetime is scoped if a lifetime isn't specified by an <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext*> overload when registering the database context.</span></span> <span data-ttu-id="51342-215">Dienste einer festgelegten Lebensdauer sollten keinen Datenbankkontext mit einer kürzeren Lebensdauer als die des Dienstes verwenden.</span><span class="sxs-lookup"><span data-stu-id="51342-215">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="51342-216">Lebensdauer und Registrierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="51342-216">Lifetime and registration options</span></span>

<span data-ttu-id="51342-217">Um den Unterschied zwischen der Lebensdauer und den Registrierungsoptionen zu demonstrieren, betrachten Sie die folgenden Schnittstellen, die Aufgaben als Vorgänge mit einem eindeutigen Bezeichner (`OperationId`) darstellen.</span><span class="sxs-lookup"><span data-stu-id="51342-217">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="51342-218">Je nachdem, wie die Dienstlebensdauer für die folgenden Schnittstellen konfiguriert ist, stellt der Container auf Anforderung einer Klasse entweder die gleiche oder eine andere Instanz des Diensts zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="51342-218">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="51342-219">Die Schnittstellen sind in die Klasse `Operation` implementiert.</span><span class="sxs-lookup"><span data-stu-id="51342-219">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="51342-220">Der `Operation`-Konstruktor generiert eine GUID, wenn noch keine angegeben ist:</span><span class="sxs-lookup"><span data-stu-id="51342-220">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="51342-221">`OperationService` ist registriert und hängt von jedem anderen `Operation`-Typ ab.</span><span class="sxs-lookup"><span data-stu-id="51342-221">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="51342-222">Wenn `OperationService` über die Abhängigkeitsinjektion angefordert wird, wird entweder eine neue Instanz jedes Diensts oder eine vorhandene Instanz basierend auf der Lebensdauer des abhängigen Diensts zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="51342-222">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="51342-223">Wenn vorübergehende Dienste bei der Anforderung erstellt werden, ist `OperationId` von Dienst `IOperationTransient` anders als `OperationId` von `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="51342-223">If transient services are created when requested, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="51342-224">`OperationService` erhält eine neue Instanz der Klasse `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="51342-224">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="51342-225">Der `OperationId`-Wert der neuen Instanz ist anders.</span><span class="sxs-lookup"><span data-stu-id="51342-225">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="51342-226">Wenn bereichsbezogene Dienste pro Anforderung erstellt werden, ist `OperationId` in Dienst `IOperationScoped` und `OperationService` identisch innerhalb einer Anforderung.</span><span class="sxs-lookup"><span data-stu-id="51342-226">If scoped services are created per request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a request.</span></span> <span data-ttu-id="51342-227">Anforderungsübergreifend haben die beiden Dienste jedoch einen anderen gemeinsamen `OperationId`-Wert.</span><span class="sxs-lookup"><span data-stu-id="51342-227">Across requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="51342-228">Wenn Singleton und Singletoninstanzdienste einmal erstellt und für alle Anforderungen und alle Dienste verwendet werden, ist `OperationId` für alle Dienstanforderungen identisch.</span><span class="sxs-lookup"><span data-stu-id="51342-228">If singleton and singleton-instance services are created once and used across all requests and all services, the `OperationId` is constant across all service requests.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="51342-229">In `Startup.ConfigureServices` wird jeder Typ entsprechend seiner benannten Lebensdauer dem Container hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="51342-229">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=12-15,18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

::: moniker-end

<span data-ttu-id="51342-230">Der Dienst `IOperationSingletonInstance` verwendet eine bestimmte Instanz mit einer bekannten ID von `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="51342-230">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="51342-231">Die Verwendung dieses Typs ist eindeutig (die GUID besteht ausschließlich aus Nullen (0)).</span><span class="sxs-lookup"><span data-stu-id="51342-231">It's clear when this type is in use (its GUID is all zeroes).</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="51342-232">Die Beispiel-App zeigt die Objektlebensdauer innerhalb einer Anforderung und zwischen einzelnen Anforderungen an.</span><span class="sxs-lookup"><span data-stu-id="51342-232">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="51342-233">Ihre Klasse `IndexModel` fordert jeden `IOperation`- und `OperationService`-Typ an.</span><span class="sxs-lookup"><span data-stu-id="51342-233">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="51342-234">Die Seite zeigt dann alle `OperationId`-Werte der Seitenmodellklasse und des Diensts über Eigenschaftenzuweisungen an:</span><span class="sxs-lookup"><span data-stu-id="51342-234">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="51342-235">Die Beispiel-App zeigt die Objektlebensdauer innerhalb einer Anforderung und zwischen einzelnen Anforderungen an.</span><span class="sxs-lookup"><span data-stu-id="51342-235">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="51342-236">Die Beispiel-App enthält das Element `OperationsController`, dass jeden `IOperation`- und `OperationService`-Typ anfordert.</span><span class="sxs-lookup"><span data-stu-id="51342-236">The sample app includes an `OperationsController` that requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="51342-237">Die Aktion `Index` legt die Dienste für `ViewBag` fest, damit die `OperationId`-Werte des Diensts angezeigt werden:</span><span class="sxs-lookup"><span data-stu-id="51342-237">The `Index` action sets the services into the `ViewBag` for display of the service's `OperationId` values:</span></span>

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Controllers/OperationsController.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="51342-238">Zwei folgende Ausgaben zeigen die Ergebnisse von zwei Anforderungen:</span><span class="sxs-lookup"><span data-stu-id="51342-238">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="51342-239">**Erste Anforderung:**</span><span class="sxs-lookup"><span data-stu-id="51342-239">**First request:**</span></span>

<span data-ttu-id="51342-240">Controllervorgänge:</span><span class="sxs-lookup"><span data-stu-id="51342-240">Controller operations:</span></span>

<span data-ttu-id="51342-241">Vorübergehend: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="51342-241">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="51342-242">Bereichsbezogen: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="51342-242">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="51342-243">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="51342-243">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="51342-244">Instanz: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="51342-244">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="51342-245">`OperationService`-Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="51342-245">`OperationService` operations:</span></span>

<span data-ttu-id="51342-246">Vorübergehend: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="51342-246">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="51342-247">Bereichsbezogen: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="51342-247">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="51342-248">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="51342-248">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="51342-249">Instanz: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="51342-249">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="51342-250">**Zweite Anforderung:**</span><span class="sxs-lookup"><span data-stu-id="51342-250">**Second request:**</span></span>

<span data-ttu-id="51342-251">Controllervorgänge:</span><span class="sxs-lookup"><span data-stu-id="51342-251">Controller operations:</span></span>

<span data-ttu-id="51342-252">Vorübergehend: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="51342-252">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="51342-253">Bereichsbezogen: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="51342-253">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="51342-254">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="51342-254">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="51342-255">Instanz: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="51342-255">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="51342-256">`OperationService`-Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="51342-256">`OperationService` operations:</span></span>

<span data-ttu-id="51342-257">Vorübergehend: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="51342-257">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="51342-258">Bereichsbezogen: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="51342-258">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="51342-259">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="51342-259">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="51342-260">Instanz: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="51342-260">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="51342-261">Beachten Sie, welche der `OperationId`-Werte innerhalb einer Anforderung und zwischen Anforderungen variieren:</span><span class="sxs-lookup"><span data-stu-id="51342-261">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="51342-262">Objekte vom Typ *Vorübergehend* sind immer unterschiedlich.</span><span class="sxs-lookup"><span data-stu-id="51342-262">*Transient* objects are always different.</span></span> <span data-ttu-id="51342-263">Beachten Sie, dass der vorübergehende `OperationId`-Wert für die ersten und zweiten Anforderungen für beide `OperationService`-Vorgänge und anforderungsübergreifend unterschiedlich ist.</span><span class="sxs-lookup"><span data-stu-id="51342-263">Note that the transient `OperationId` value for both the first and second requests are different for both `OperationService` operations and across requests.</span></span> <span data-ttu-id="51342-264">Eine neue Instanz wird für jeden Dienst und jede Anforderung bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="51342-264">A new instance is provided to each service and request.</span></span>
* <span data-ttu-id="51342-265">Objekte vom Typ *Bereichsbezogen* sind innerhalb einer Anforderung identisch, anforderungsübergreifend sind sie jedoch unterschiedlich.</span><span class="sxs-lookup"><span data-stu-id="51342-265">*Scoped* objects are the same within a request but different across requests.</span></span>
* <span data-ttu-id="51342-266">Objekte vom Typ *Singleton* sind bei jedem Objekt und in jeder Anforderung identisch (unabhängig davon, ob eine `Operation`-Instanz in `ConfigureServices` bereitgestellt wird).</span><span class="sxs-lookup"><span data-stu-id="51342-266">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="51342-267">Abrufen von Diensten aus „Main“</span><span class="sxs-lookup"><span data-stu-id="51342-267">Call services from main</span></span>

<span data-ttu-id="51342-268">Erstellen Sie [IServiceScope](/dotnet/api/microsoft.extensions.dependencyinjection.iservicescope) mit [IServiceScopeFactory.CreateScope](/dotnet/api/microsoft.extensions.dependencyinjection.iservicescopefactory.createscope), um einen bereichsbezogenen Dienst innerhalb des Anwendungsbereichs aufzulösen.</span><span class="sxs-lookup"><span data-stu-id="51342-268">Create an [IServiceScope](/dotnet/api/microsoft.extensions.dependencyinjection.iservicescope) with [IServiceScopeFactory.CreateScope](/dotnet/api/microsoft.extensions.dependencyinjection.iservicescopefactory.createscope) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="51342-269">Dieser Ansatz eignet sich gut dafür, beim Start auf einen bereichsbezogenen Dienst zuzugreifen und Initialisierungsaufgaben auszuführen.</span><span class="sxs-lookup"><span data-stu-id="51342-269">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="51342-270">Das folgende Beispiel veranschaulicht, wie man einen Kontext für `MyScopedService` in `Program.Main` erhält:</span><span class="sxs-lookup"><span data-stu-id="51342-270">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

```csharp
public static void Main(string[] args)
{
    var host = CreateWebHostBuilder(args).Build();

    using (var serviceScope = host.Services.CreateScope())
    {
        var services = serviceScope.ServiceProvider;

        try
        {
            var serviceContext = services.GetRequiredService<MyScopedService>();
            // Use the context here
        }
        catch (Exception ex)
        {
            var logger = services.GetRequiredService<ILogger<Program>>();
            logger.LogError(ex, "An error occurred.");
        }
    }

    host.Run();
}
```

## <a name="scope-validation"></a><span data-ttu-id="51342-271">Bereichsvalidierung</span><span class="sxs-lookup"><span data-stu-id="51342-271">Scope validation</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="51342-272">Wenn die App in der Entwicklungsumgebung ausgeführt wird, überprüft der Standarddienstanbieter, ob:</span><span class="sxs-lookup"><span data-stu-id="51342-272">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="51342-273">Bereichsbezogene Dienste nicht direkt oder indirekt vom Stammdienstanbieter aufgelöst werden</span><span class="sxs-lookup"><span data-stu-id="51342-273">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="51342-274">Bereichsbezogene Dienste nicht direkt oder indirekt in Singletons eingefügt werden</span><span class="sxs-lookup"><span data-stu-id="51342-274">Scoped services aren't directly or indirectly injected into singletons.</span></span>

::: moniker-end

<span data-ttu-id="51342-275">Der Stammdienstanbieter wird erstellt, wenn [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="51342-275">The root service provider is created when [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) is called.</span></span> <span data-ttu-id="51342-276">Die Lebensdauer des Stammdienstanbieters entspricht der Lebensdauer der App/des Servers, wenn der Anbieter mit der App erstellt wird und verworfen wird, wenn die App beendet wird.</span><span class="sxs-lookup"><span data-stu-id="51342-276">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="51342-277">Bereichsbezogene Dienste werden von dem Container verworfen, der sie erstellt hat.</span><span class="sxs-lookup"><span data-stu-id="51342-277">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="51342-278">Wenn ein bereichsbezogener Dienst im Stammcontainer erstellt wird, wird die Lebensdauer effektiv auf Singleton heraufgestuft, da er nur vom Stammcontainer verworfen wird, wenn die App/der Server heruntergefahren wird.</span><span class="sxs-lookup"><span data-stu-id="51342-278">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="51342-279">Die Überprüfung bereichsbezogener Dienste erfasst diese Situationen, wenn `BuildServiceProvider` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="51342-279">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="51342-280">Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="51342-280">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="51342-281">Anfordern von Diensten</span><span class="sxs-lookup"><span data-stu-id="51342-281">Request Services</span></span>

<span data-ttu-id="51342-282">Die Dienste, die innerhalb einer ASP.NET Core-Anforderung von `HttpContext` verfügbar sind, werden über die [HttpContext.RequestService](/dotnet/api/microsoft.aspnetcore.http.httpcontext.requestservices)-Sammlung verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="51342-282">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](/dotnet/api/microsoft.aspnetcore.http.httpcontext.requestservices) collection.</span></span>

<span data-ttu-id="51342-283">Anforderungsdienste stellen die Dienste dar, die als Teil der App konfiguriert und angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="51342-283">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="51342-284">Wenn Objekte Abhängigkeiten angeben, werden diese von den in `RequestServices` gefundenen Typen erfüllt (nicht in `ApplicationServices`).</span><span class="sxs-lookup"><span data-stu-id="51342-284">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="51342-285">Generell sollte die App diese Eigenschaften nicht direkt verwenden.</span><span class="sxs-lookup"><span data-stu-id="51342-285">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="51342-286">Fordern Sie stattdessen die von Klassen benötigten Typen über Klassenkonstruktoren an, und lassen Sie das Framework die Abhängigkeiten einfügen.</span><span class="sxs-lookup"><span data-stu-id="51342-286">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="51342-287">So erhalten Sie Klassen, die einfacher getestet werden können.</span><span class="sxs-lookup"><span data-stu-id="51342-287">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="51342-288">Fordern Sie für den Zugriff auf die `RequestServices`-Sammlung Abhängigkeiten lieber als Konstruktorparameter an.</span><span class="sxs-lookup"><span data-stu-id="51342-288">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="51342-289">Entwerfen von Diensten für die Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="51342-289">Design services for dependency injection</span></span>

<span data-ttu-id="51342-290">Best Practices:</span><span class="sxs-lookup"><span data-stu-id="51342-290">Best practices are to:</span></span>

* <span data-ttu-id="51342-291">Entwerfen Sie Dienste zur Verwendung der Abhängigkeitsinjektion, um ihre Abhängigkeiten zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="51342-291">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="51342-292">Vermeiden Sie zustandsbehaftete, statische Methodenaufrufe.</span><span class="sxs-lookup"><span data-stu-id="51342-292">Avoid stateful, static method calls.</span></span>
* <span data-ttu-id="51342-293">Vermeiden Sie die direkte Instanziierung abhängiger Klassen innerhalb von Diensten.</span><span class="sxs-lookup"><span data-stu-id="51342-293">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="51342-294">Die direkte Instanziierung koppelt den Code an eine bestimmte Implementierung.</span><span class="sxs-lookup"><span data-stu-id="51342-294">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="51342-295">Erstellen Sie kleine, gut gestaltete und einfach zu testende Apps.</span><span class="sxs-lookup"><span data-stu-id="51342-295">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="51342-296">Wenn eine Klasse zu viele eingefügte Abhängigkeiten zu haben scheint, ist dies im Allgemeinen ein Zeichen dafür, dass die Klasse zu viele Aufgaben hat und gegen das [Single-Responsibility-Prinzip (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) (Prinzip der eindeutigen Verantwortlichkeit) verstößt.</span><span class="sxs-lookup"><span data-stu-id="51342-296">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="51342-297">Versuchen Sie, die Klasse umzugestalten, indem Sie einige ihrer Aufgaben in eine neue Klasse verschieben.</span><span class="sxs-lookup"><span data-stu-id="51342-297">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="51342-298">Beachten Sie, dass der Fokus der Razor Pages-Seitenmodellklassen und MVC-Controllerklassen auf der Benutzeroberfläche liegt.</span><span class="sxs-lookup"><span data-stu-id="51342-298">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="51342-299">Geschäftsregeln und Implementierungsdetails für den Datenzugriff sollten in Klassen aufbewahrt werden gemäß dem Prinzip [Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) (Trennung der Zuständigkeiten).</span><span class="sxs-lookup"><span data-stu-id="51342-299">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="51342-300">Löschen von Diensten</span><span class="sxs-lookup"><span data-stu-id="51342-300">Disposal of services</span></span>

<span data-ttu-id="51342-301">Der Container ruft `Dispose` für die erstellten `IDisposable`-Typen auf.</span><span class="sxs-lookup"><span data-stu-id="51342-301">The container calls `Dispose` for the `IDisposable` types it creates.</span></span> <span data-ttu-id="51342-302">Wenn eine Instanz dem Container per Benutzercode hinzugefügt wird, wird sie nicht automatisch gelöscht.</span><span class="sxs-lookup"><span data-stu-id="51342-302">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

```csharp
// Services that implement IDisposable:
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}
public class Service3 : IDisposable {}

public interface ISomeService {}
public class SomeServiceImplementation : ISomeService, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    // The container creates the following instances and disposes them automatically:
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<ISomeService>(sp => new SomeServiceImplementation());

    // The container doesn't create the following instances, so it doesn't dispose of
    // the instances automatically:
    services.AddSingleton<Service3>(new Service3());
    services.AddSingleton(new Service3());
}
```

::: moniker range="= aspnetcore-1.0"

> [!NOTE]
> <span data-ttu-id="51342-303">In ASP.NET Core 1.0 ruft der Container für *alle* `IDisposable`-Objekte, einschließlich der nicht von ihm erstellten Objekte, „Dispose“ auf.</span><span class="sxs-lookup"><span data-stu-id="51342-303">In ASP.NET Core 1.0, the container calls dispose on *all* `IDisposable` objects, including those it didn't create.</span></span>

::: moniker-end

## <a name="default-service-container-replacement"></a><span data-ttu-id="51342-304">Ersetzen von Standarddienstcontainern</span><span class="sxs-lookup"><span data-stu-id="51342-304">Default service container replacement</span></span>

<span data-ttu-id="51342-305">Der integrierte Dienstcontainer dient dazu, die Anforderungen des Frameworks und der meisten Consumer-Apps zu erfüllen.</span><span class="sxs-lookup"><span data-stu-id="51342-305">The built-in service container is meant to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="51342-306">Die Verwendung der integrierten Container wird empfohlen, es sei denn, Sie benötigen ein bestimmtes Feature, das nicht unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="51342-306">We recommend using the built-in container unless you need a specific feature that it doesn't support.</span></span> <span data-ttu-id="51342-307">Im Folgenden werden einige der Features aufgeführt, die von Containern von Drittanbietern unterstützt werden, aber nicht im integrierten Container enthalten sind:</span><span class="sxs-lookup"><span data-stu-id="51342-307">Some of the features supported in 3rd party containers not found in the built-in container:</span></span>

* <span data-ttu-id="51342-308">Eigenschaftsinjektion</span><span class="sxs-lookup"><span data-stu-id="51342-308">Property injection</span></span>
* <span data-ttu-id="51342-309">Auf Namen basierende Injektion</span><span class="sxs-lookup"><span data-stu-id="51342-309">Injection based on name</span></span>
* <span data-ttu-id="51342-310">Untergeordnete Container</span><span class="sxs-lookup"><span data-stu-id="51342-310">Child containers</span></span>
* <span data-ttu-id="51342-311">Benutzerdefinierte Verwaltung der Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="51342-311">Custom lifetime management</span></span>
* <span data-ttu-id="51342-312">`Func<T>`-Unterstützung für die verzögerte Initialisierung</span><span class="sxs-lookup"><span data-stu-id="51342-312">`Func<T>` support for lazy initialization</span></span>

<span data-ttu-id="51342-313">Eine Liste einiger Container, die Adapter unterstützen, finden Sie in der [Datei „readme.md“ zur Dependency Injection](https://github.com/aspnet/Extensions/tree/master/src/DependencyInjection).</span><span class="sxs-lookup"><span data-stu-id="51342-313">See the [Dependency Injection readme.md file](https://github.com/aspnet/Extensions/tree/master/src/DependencyInjection) for a list of some of the containers that support adapters.</span></span>

<span data-ttu-id="51342-314">Im folgenden Beispiel wird der integrierte Container durch [Autofac](https://autofac.org/) ersetzt:</span><span class="sxs-lookup"><span data-stu-id="51342-314">The following sample replaces the built-in container with [Autofac](https://autofac.org/):</span></span>

* <span data-ttu-id="51342-315">Installieren Sie das entsprechende Containerpaket bzw. die entsprechenden Containerpakete:</span><span class="sxs-lookup"><span data-stu-id="51342-315">Install the appropriate container package(s):</span></span>

  * [<span data-ttu-id="51342-316">Autofac</span><span class="sxs-lookup"><span data-stu-id="51342-316">Autofac</span></span>](https://www.nuget.org/packages/Autofac/)
  * [<span data-ttu-id="51342-317">Autofac.Extensions.DependencyInjection</span><span class="sxs-lookup"><span data-stu-id="51342-317">Autofac.Extensions.DependencyInjection</span></span>](https://www.nuget.org/packages/Autofac.Extensions.DependencyInjection/)

* <span data-ttu-id="51342-318">Konfigurieren Sie den Container in `Startup.ConfigureServices`, und geben Sie einen `IServiceProvider`-Wert zurück:</span><span class="sxs-lookup"><span data-stu-id="51342-318">Configure the container in `Startup.ConfigureServices` and return an `IServiceProvider`:</span></span>

    ```csharp
    public IServiceProvider ConfigureServices(IServiceCollection services)
    {
        services.AddMvc();
        // Add other framework services

        // Add Autofac
        var containerBuilder = new ContainerBuilder();
        containerBuilder.RegisterModule<DefaultModule>();
        containerBuilder.Populate(services);
        var container = containerBuilder.Build();
        return new AutofacServiceProvider(container);
    }
    ```

    <span data-ttu-id="51342-319">Um einen Drittanbietercontainer zu verwenden, muss `Startup.ConfigureServices` `IServiceProvider` zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="51342-319">To use a 3rd party container, `Startup.ConfigureServices` must return `IServiceProvider`.</span></span>

* <span data-ttu-id="51342-320">Konfigurieren Sie Autofac in `DefaultModule`:</span><span class="sxs-lookup"><span data-stu-id="51342-320">Configure Autofac in `DefaultModule`:</span></span>

    ```csharp
    public class DefaultModule : Module
    {
        protected override void Load(ContainerBuilder builder)
        {
            builder.RegisterType<CharacterRepository>().As<ICharacterRepository>();
        }
    }
    ```

<span data-ttu-id="51342-321">Zur Laufzeit wird Autofac verwendet, um Typen aufzulösen und Abhängigkeiten einzufügen.</span><span class="sxs-lookup"><span data-stu-id="51342-321">At runtime, Autofac is used to resolve types and inject dependencies.</span></span> <span data-ttu-id="51342-322">Weitere Informationen zur Verwendung von Autofac mit ASP.NET Core finden Sie in der [Autofac-Dokumentation](https://docs.autofac.org/en/latest/integration/aspnetcore.html).</span><span class="sxs-lookup"><span data-stu-id="51342-322">To learn more about using Autofac with ASP.NET Core, see the [Autofac documentation](https://docs.autofac.org/en/latest/integration/aspnetcore.html).</span></span>

### <a name="thread-safety"></a><span data-ttu-id="51342-323">Threadsicherheit</span><span class="sxs-lookup"><span data-stu-id="51342-323">Thread safety</span></span>

<span data-ttu-id="51342-324">Singleton-Dienste müssen threadsicher sein.</span><span class="sxs-lookup"><span data-stu-id="51342-324">Singleton services need to be thread safe.</span></span> <span data-ttu-id="51342-325">Wenn ein Singleton-Dienst eine Abhängigkeit von einem vorübergehenden Dienst aufweist, muss der vorübergehende Dienst abhängig von der Verwendungsweise durch den Singleton-Dienst ebenfalls threadsicher sein.</span><span class="sxs-lookup"><span data-stu-id="51342-325">If a singleton service has a dependency on a transient service, the transient service may also need to be thread safe depending how it's used by the singleton.</span></span>

<span data-ttu-id="51342-326">Die Factorymethode des einzelnen Diensts, z. B. das zweite Argument für [AddSingleton&lt;TService&gt;(IServiceCollection, Func&lt;IServiceProvider,TService&gt;)](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions.addsingleton#Microsoft_Extensions_DependencyInjection_ServiceCollectionServiceExtensions_AddSingleton__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Func_System_IServiceProvider___0__), muss nicht threadsicher sein.</span><span class="sxs-lookup"><span data-stu-id="51342-326">The factory method of single service, such as the second argument to [AddSingleton&lt;TService&gt;(IServiceCollection, Func&lt;IServiceProvider,TService&gt;)](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions.addsingleton#Microsoft_Extensions_DependencyInjection_ServiceCollectionServiceExtensions_AddSingleton__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Func_System_IServiceProvider___0__), doesn't need to be thread-safe.</span></span> <span data-ttu-id="51342-327">Wie bei `static`-Konstruktoren erfolgt der Aufruf einmalig über einen einzelnen Thread.</span><span class="sxs-lookup"><span data-stu-id="51342-327">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="51342-328">Empfehlungen</span><span class="sxs-lookup"><span data-stu-id="51342-328">Recommendations</span></span>

* <span data-ttu-id="51342-329">`async/await`- und `Task`-basierte Dienstauflösung wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="51342-329">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="51342-330">C# unterstützt keine asynchronen Konstruktoren, daher wird empfohlen, asynchrone Methoden zu verwenden, nachdem der Dienst synchron aufgelöst wurde.</span><span class="sxs-lookup"><span data-stu-id="51342-330">C# does not support asynchronous constructors, therefore the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="51342-331">Vermeiden Sie das Speichern von Daten und die direkte Konfiguration im Dienstcontainer.</span><span class="sxs-lookup"><span data-stu-id="51342-331">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="51342-332">Der Einkaufswagen eines Benutzers sollte z. B. normalerweise nicht dem Dienstcontainer hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="51342-332">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="51342-333">Bei der Konfiguration sollte das [Optionsmuster](xref:fundamentals/configuration/options) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="51342-333">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="51342-334">Gleichermaßen sollten Sie „Datencontainer“-Objekte vermeiden, die nur vorhanden sind, um den Zugriff auf einige andere Objekte zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="51342-334">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="51342-335">Das tatsächlich benötige Element sollte besser über Dependency Injection angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="51342-335">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="51342-336">Vermeiden Sie den statischen Zugriff auf Dienste (z. B. statische Eingabe von [IApplicationBuilder.ApplicationServices](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder.applicationservices) zur Verwendung an anderer Stelle).</span><span class="sxs-lookup"><span data-stu-id="51342-336">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder.applicationservices) for use elsewhere).</span></span>

* <span data-ttu-id="51342-337">Vermeiden Sie die Verwendung von *Dienstlocator-Mustern*.</span><span class="sxs-lookup"><span data-stu-id="51342-337">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="51342-338">Rufen Sie beispielsweise nicht <xref:System.IServiceProvider.GetService*> auf, um eine Dienstinstanz zu erhalten, wenn Sie stattdessen Dependency Injection verwenden können.</span><span class="sxs-lookup"><span data-stu-id="51342-338">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead.</span></span> <span data-ttu-id="51342-339">Eine andere Dienstlocator-Variante, die Sie vermeiden sollten, ist die Injektion einer Factory, die zur Laufzeit Abhängigkeiten auflöst.</span><span class="sxs-lookup"><span data-stu-id="51342-339">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="51342-340">Beide Vorgehensweisen kombinieren Strategien zur [Umkehrung der Steuerung](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="51342-340">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="51342-341">Vermeiden Sie den statischen Zugriff auf `HttpContext` (z. B. [IHttpContextAccessor.HttpContext](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor.httpcontext)).</span><span class="sxs-lookup"><span data-stu-id="51342-341">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor.httpcontext)).</span></span>

<span data-ttu-id="51342-342">Wie bei allen Empfehlungen treffen Sie möglicherweise auf Situationen, in denen eine Empfehlung ignoriert werden muss.</span><span class="sxs-lookup"><span data-stu-id="51342-342">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="51342-343">Es gibt nur wenige Ausnahmen &mdash; die sich meistens auf besondere Fälle innerhalb des Frameworks beziehen.</span><span class="sxs-lookup"><span data-stu-id="51342-343">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="51342-344">Dependency Injection stellt eine *Alternative* zu statischen bzw. globalen Objektzugriffsmustern dar.</span><span class="sxs-lookup"><span data-stu-id="51342-344">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="51342-345">Sie werden keinen Nutzen aus der Dependency Injection ziehen können, wenn Sie diese mit dem Zugriff auf statische Objekte kombinieren.</span><span class="sxs-lookup"><span data-stu-id="51342-345">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="51342-346">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="51342-346">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="51342-347">Schreiben von sauberem Code in ASP.NET Core über Dependency Injection (MSDN)</span><span class="sxs-lookup"><span data-stu-id="51342-347">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="51342-348">Prinzip der expliziten Abhängigkeiten</span><span class="sxs-lookup"><span data-stu-id="51342-348">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* <span data-ttu-id="51342-349">[Umkehrung von Steuerungscontainern und das Abhängigkeitsinjektionsmuster (Martin Fowler)](https://www.martinfowler.com/articles/injection.html) (in englischer Sprache)</span><span class="sxs-lookup"><span data-stu-id="51342-349">[Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)](https://www.martinfowler.com/articles/injection.html)</span></span>
* [<span data-ttu-id="51342-350">Registrieren eines Diensts mit mehreren Schnittstellen in ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="51342-350">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)
