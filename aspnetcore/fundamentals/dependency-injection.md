---
title: Dependency Injection in ASP.NET Core
author: guardrex
description: Erfahren Sie, wie ASP.NET Core Dependency Injection implementiert und wie Sie dieses Muster verwenden können.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/30/2020
uid: fundamentals/dependency-injection
ms.openlocfilehash: a9d268489ebcef69d64c6fd65087bc38a3581821
ms.sourcegitcommit: 0b0e485a8a6dfcc65a7a58b365622b3839f4d624
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/01/2020
ms.locfileid: "76928410"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="fc15c-103">Dependency Injection in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fc15c-103">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="fc15c-104">Von [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="fc15c-104">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="fc15c-105">ASP.NET Core unterstützt das Softwareentwurfsmuster Abhängigkeitsinjektion. Damit kann eine [Umkehrung der Steuerung](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) (Inversion of Control, IoC) zwischen Klassen und ihren Abhängigkeiten erreicht werden.</span><span class="sxs-lookup"><span data-stu-id="fc15c-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="fc15c-106">Weitere Informationen zur Abhängigkeitsinjektion innerhalb von MVC-Controllern finden Sie unter <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="fc15c-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="fc15c-107">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fc15c-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="fc15c-108">Übersicht über Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="fc15c-108">Overview of dependency injection</span></span>

<span data-ttu-id="fc15c-109">Eine *Abhängigkeit* ist ein beliebiges Objekt, das ein anderes Objekt benötigt.</span><span class="sxs-lookup"><span data-stu-id="fc15c-109">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="fc15c-110">Überprüfen Sie die folgende `MyDependency`-Klasse mit einer `WriteMessage`-Methode, von der andere Klassen in einer App abhängen:</span><span class="sxs-lookup"><span data-stu-id="fc15c-110">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="fc15c-111">Eine Instanz der Klasse `MyDependency` kann erstellt werden, um die `WriteMessage`-Methode einer Klasse zur Verfügung zu stellen.</span><span class="sxs-lookup"><span data-stu-id="fc15c-111">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="fc15c-112">Die Klasse `MyDependency` ist eine Abhängigkeit der Klasse `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="fc15c-112">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="fc15c-113">Die Klasse erstellt die Instanz `MyDependency` und hängt direkt von dieser ab.</span><span class="sxs-lookup"><span data-stu-id="fc15c-113">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="fc15c-114">Codeabhängigkeiten (wie im vorherigen Beispiel) sind problematisch und sollten aus folgenden Gründen vermieden werden:</span><span class="sxs-lookup"><span data-stu-id="fc15c-114">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="fc15c-115">Um `MyDependency` durch eine andere Implementierung zu ersetzen, muss die Klasse geändert werden.</span><span class="sxs-lookup"><span data-stu-id="fc15c-115">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="fc15c-116">Wenn `MyDependency` über Abhängigkeiten verfügt, müssen diese von der Klasse konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="fc15c-116">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="fc15c-117">In einem großen Projekt mit mehreren Klassen, die von `MyDependency` abhängig sind, wird der Konfigurationscode über die App verteilt.</span><span class="sxs-lookup"><span data-stu-id="fc15c-117">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="fc15c-118">Diese Implementierung ist nicht für Komponententests geeignet.</span><span class="sxs-lookup"><span data-stu-id="fc15c-118">This implementation is difficult to unit test.</span></span> <span data-ttu-id="fc15c-119">Die App sollte eine `MyDependency`-Modell- oder Stubklasse verwenden, was mit diesem Ansatz nicht möglich ist.</span><span class="sxs-lookup"><span data-stu-id="fc15c-119">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="fc15c-120">Die Abhängigkeitsinjektion löst dieses Problem mithilfe der folgenden Schritte:</span><span class="sxs-lookup"><span data-stu-id="fc15c-120">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="fc15c-121">Die Verwendung einer Schnittstelle oder Basisklasse zur Abstraktion der Abhängigkeitsimplementierung.</span><span class="sxs-lookup"><span data-stu-id="fc15c-121">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="fc15c-122">Registrierung der Abhängigkeit in einem Dienstcontainer.</span><span class="sxs-lookup"><span data-stu-id="fc15c-122">Registration of the dependency in a service container.</span></span> <span data-ttu-id="fc15c-123">ASP.NET Core stellt einen integrierten Dienstcontainer (<xref:System.IServiceProvider>) bereit.</span><span class="sxs-lookup"><span data-stu-id="fc15c-123">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="fc15c-124">Die Dienste werden in der App-Methode `Startup.ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="fc15c-124">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="fc15c-125">Die *Injektion* des Diensts in den Konstruktor der Klasse, wo er verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="fc15c-125">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="fc15c-126">Das Framework erstellt eine Instanz der Abhängigkeit und entfernt diese, wenn sie nicht mehr benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="fc15c-126">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="fc15c-127">In der [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) definiert die `IMyDependency`-Schnittstelle eine Methode, die der Dienst für die App bereitstellt:</span><span class="sxs-lookup"><span data-stu-id="fc15c-127">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="fc15c-128">Diese Schnittstelle wird durch einen konkreten Typ (`MyDependency`) implementiert:</span><span class="sxs-lookup"><span data-stu-id="fc15c-128">This interface is implemented by a concrete type, `MyDependency`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="fc15c-129">`MyDependency` fordert einen <xref:Microsoft.Extensions.Logging.ILogger`1> beim zugehörigen Konstruktor an.</span><span class="sxs-lookup"><span data-stu-id="fc15c-129">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="fc15c-130">Die Abhängigkeitsinjektion wird häufig als Verkettung verwendet.</span><span class="sxs-lookup"><span data-stu-id="fc15c-130">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="fc15c-131">Jede angeforderte Abhängigkeit fordert wiederum ihre eigenen Abhängigkeiten an.</span><span class="sxs-lookup"><span data-stu-id="fc15c-131">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="fc15c-132">Der Container löst die Abhängigkeiten im Diagramm auf und gibt den vollständig aufgelösten Dienst zurück.</span><span class="sxs-lookup"><span data-stu-id="fc15c-132">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="fc15c-133">Die gesammelten aufzulösenden Abhängigkeiten werden als *Abhängigkeitsstruktur*, *Abhängigkeitsdiagramm* oder *Objektdiagramm* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="fc15c-133">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="fc15c-134">`IMyDependency` und `ILogger<TCategoryName>` müssen im Dienstcontainer registriert werden.</span><span class="sxs-lookup"><span data-stu-id="fc15c-134">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="fc15c-135">`IMyDependency` ist in `Startup.ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="fc15c-135">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="fc15c-136">`ILogger<TCategoryName>` wird von der Protokollierungsabstraktionsinfrastruktur registriert. Es handelt sich also um einen [von einem Framework bereitgestellten Dienst](#framework-provided-services), der standardmäßig vom Framework registriert wird.</span><span class="sxs-lookup"><span data-stu-id="fc15c-136">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="fc15c-137">Der Container löst `ILogger<TCategoryName>` unter Verwendung der [(generischen) offenen Typen](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types) auf, wodurch die Notwendigkeit entfällt, jeden [(generischen) konstruierten Typ](/dotnet/csharp/language-reference/language-specification/types#constructed-types) zu registrieren:</span><span class="sxs-lookup"><span data-stu-id="fc15c-137">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="fc15c-138">In der Beispiel-App ist der Dienst `IMyDependency` mit dem konkreten Typ `MyDependency` registriert.</span><span class="sxs-lookup"><span data-stu-id="fc15c-138">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="fc15c-139">Die Registrierung schränkt die Lebensdauer des Diensts auf die Lebensdauer einer einzelnen Anforderung ein.</span><span class="sxs-lookup"><span data-stu-id="fc15c-139">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="fc15c-140">Auf die [Dienstlebensdauer](#service-lifetimes) wird später in diesem Artikel eingegangen.</span><span class="sxs-lookup"><span data-stu-id="fc15c-140">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="fc15c-141">Jede Erweiterungsmethode vom Typ `services.Add{SERVICE_NAME}` fügt Dienste hinzu (und konfiguriert diese möglicherweise).</span><span class="sxs-lookup"><span data-stu-id="fc15c-141">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="fc15c-142">So fügt beispielsweise `services.AddMvc()` die erforderlichen Dienste Razor Pages und MVC hinzu.</span><span class="sxs-lookup"><span data-stu-id="fc15c-142">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="fc15c-143">Es wird empfohlen, dass Apps dieser Konvention folgen.</span><span class="sxs-lookup"><span data-stu-id="fc15c-143">We recommended that apps follow this convention.</span></span> <span data-ttu-id="fc15c-144">Platzieren Sie Erweiterungsmethoden im Namespace [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection), um Gruppen von Dienstregistrierungen zu kapseln.</span><span class="sxs-lookup"><span data-stu-id="fc15c-144">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="fc15c-145">Wenn für den Dienstkonstruktor ein [integrierter Typ](/dotnet/csharp/language-reference/keywords/built-in-types-table) erforderlich ist, z. B. `string`, kann dieser Typ über [Konfiguration](xref:fundamentals/configuration/index) oder das [Optionsmuster](xref:fundamentals/configuration/options) eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="fc15c-145">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="fc15c-146">Eine Instanz des Diensts wird über den Konstruktor einer Klasse angefordert, in der der Dienst verwendet wird, und einem privaten Feld zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="fc15c-146">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="fc15c-147">Das Feld wird verwendet, um auf den Dienst bei Bedarf über die gesamte Klasse zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="fc15c-147">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="fc15c-148">In der Beispiel-App wird die Instanz `IMyDependency` angefordert, und mit ihr wird die App-Methode `WriteMessage` aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="fc15c-148">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

::: moniker-end

## <a name="services-injected-into-startup"></a><span data-ttu-id="fc15c-149">In den Start eingefügte Dienste</span><span class="sxs-lookup"><span data-stu-id="fc15c-149">Services injected into Startup</span></span>

<span data-ttu-id="fc15c-150">Bei Verwendung des generischen Hosts (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) können nur die folgenden Diensttypen in den `Startup`-Konstruktor eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="fc15c-150">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="fc15c-151">Dienste können in `Startup.Configure` eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="fc15c-151">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="fc15c-152">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="fc15c-152">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="fc15c-153">Von Frameworks bereitgestellte Dienste</span><span class="sxs-lookup"><span data-stu-id="fc15c-153">Framework-provided services</span></span>

<span data-ttu-id="fc15c-154">Die Methode `Startup.ConfigureServices` definiert die von der App verwendeten Dienste. Dies umfasst auch Plattformfeatures wie Entity Framework Core und ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="fc15c-154">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="fc15c-155">Zunächst enthält die in `ConfigureServices` bereitgestellte `IServiceCollection` die vom Framework definierten Dienste, abhängig davon, wie der [Host konfiguriert](xref:fundamentals/index#host) wurde.</span><span class="sxs-lookup"><span data-stu-id="fc15c-155">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="fc15c-156">Es ist nicht ungewöhnlich, dass das Framework Hunderte von Diensten für eine auf einer ASP.NET Core-Vorlage basierende App registriert.</span><span class="sxs-lookup"><span data-stu-id="fc15c-156">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="fc15c-157">In der folgenden Tabelle finden Sie eine kleine Auswahl der Dienste, die vom Framework registriert werden können.</span><span class="sxs-lookup"><span data-stu-id="fc15c-157">A small sample of framework-registered services is listed in the following table.</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="fc15c-158">Diensttyp</span><span class="sxs-lookup"><span data-stu-id="fc15c-158">Service Type</span></span> | <span data-ttu-id="fc15c-159">Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="fc15c-159">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="fc15c-160">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="fc15c-160">Transient</span></span> |
| `IHostApplicationLifetime` | <span data-ttu-id="fc15c-161">Singleton</span><span class="sxs-lookup"><span data-stu-id="fc15c-161">Singleton</span></span> |
| `IWebHostEnvironment` | <span data-ttu-id="fc15c-162">Singleton</span><span class="sxs-lookup"><span data-stu-id="fc15c-162">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="fc15c-163">Singleton</span><span class="sxs-lookup"><span data-stu-id="fc15c-163">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="fc15c-164">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="fc15c-164">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="fc15c-165">Singleton</span><span class="sxs-lookup"><span data-stu-id="fc15c-165">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="fc15c-166">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="fc15c-166">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="fc15c-167">Singleton</span><span class="sxs-lookup"><span data-stu-id="fc15c-167">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="fc15c-168">Singleton</span><span class="sxs-lookup"><span data-stu-id="fc15c-168">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="fc15c-169">Singleton</span><span class="sxs-lookup"><span data-stu-id="fc15c-169">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="fc15c-170">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="fc15c-170">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="fc15c-171">Singleton</span><span class="sxs-lookup"><span data-stu-id="fc15c-171">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="fc15c-172">Singleton</span><span class="sxs-lookup"><span data-stu-id="fc15c-172">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="fc15c-173">Singleton</span><span class="sxs-lookup"><span data-stu-id="fc15c-173">Singleton</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| <span data-ttu-id="fc15c-174">Diensttyp</span><span class="sxs-lookup"><span data-stu-id="fc15c-174">Service Type</span></span> | <span data-ttu-id="fc15c-175">Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="fc15c-175">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="fc15c-176">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="fc15c-176">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="fc15c-177">Singleton</span><span class="sxs-lookup"><span data-stu-id="fc15c-177">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="fc15c-178">Singleton</span><span class="sxs-lookup"><span data-stu-id="fc15c-178">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="fc15c-179">Singleton</span><span class="sxs-lookup"><span data-stu-id="fc15c-179">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="fc15c-180">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="fc15c-180">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="fc15c-181">Singleton</span><span class="sxs-lookup"><span data-stu-id="fc15c-181">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="fc15c-182">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="fc15c-182">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="fc15c-183">Singleton</span><span class="sxs-lookup"><span data-stu-id="fc15c-183">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="fc15c-184">Singleton</span><span class="sxs-lookup"><span data-stu-id="fc15c-184">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="fc15c-185">Singleton</span><span class="sxs-lookup"><span data-stu-id="fc15c-185">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="fc15c-186">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="fc15c-186">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="fc15c-187">Singleton</span><span class="sxs-lookup"><span data-stu-id="fc15c-187">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="fc15c-188">Singleton</span><span class="sxs-lookup"><span data-stu-id="fc15c-188">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="fc15c-189">Singleton</span><span class="sxs-lookup"><span data-stu-id="fc15c-189">Singleton</span></span> |

::: moniker-end

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="fc15c-190">Registrieren weiterer Dienste mit Erweiterungsmethoden</span><span class="sxs-lookup"><span data-stu-id="fc15c-190">Register additional services with extension methods</span></span>

<span data-ttu-id="fc15c-191">Wenn eine Dienstsammlungs-Erweiterungsmethode verfügbar ist, um einen Dienst (und ggf. seine abhängigen Dienste) zu registrieren, ist die Konvention, eine einzige `Add{SERVICE_NAME}`-Erweiterungsmethode zu verwenden, um alle von diesem Dienst benötigten Dienste zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="fc15c-191">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="fc15c-192">Der folgende Code ist ein Beispiel dafür, wie mit den Erweiterungsmethoden [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) und <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> zusätzliche Dienste zum Container hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="fc15c-192">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

<span data-ttu-id="fc15c-193">Weitere Informationen finden Sie in der <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection>-Klasse in der API-Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="fc15c-193">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="fc15c-194">Dienstlebensdauer</span><span class="sxs-lookup"><span data-stu-id="fc15c-194">Service lifetimes</span></span>

<span data-ttu-id="fc15c-195">Wählen Sie eine geeignete Lebensdauer für jeden registrierten Dienst aus.</span><span class="sxs-lookup"><span data-stu-id="fc15c-195">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="fc15c-196">ASP.NET Core-Dienste können mit folgender Lebensdauer konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="fc15c-196">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="fc15c-197">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="fc15c-197">Transient</span></span>

<span data-ttu-id="fc15c-198">Kurzlebige Dienste (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) werden bei jeder Anforderung aus dem Dienstcontainer neu erstellt.</span><span class="sxs-lookup"><span data-stu-id="fc15c-198">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="fc15c-199">Diese Lebensdauer ist am besten für einfache, zustandslose Dienste geeignet.</span><span class="sxs-lookup"><span data-stu-id="fc15c-199">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="fc15c-200">Bereichsbezogen</span><span class="sxs-lookup"><span data-stu-id="fc15c-200">Scoped</span></span>

<span data-ttu-id="fc15c-201">Dienste mit bereichsbezogener Lebensdauer (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) werden einmal pro Clientanforderung (-verbindung) erstellt.</span><span class="sxs-lookup"><span data-stu-id="fc15c-201">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="fc15c-202">Wenn Sie einen bereichsbezogenen Dienst in einer Middleware verwenden, müssen Sie den Dienst in die `Invoke`- oder `InvokeAsync`-Methode einfügen.</span><span class="sxs-lookup"><span data-stu-id="fc15c-202">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="fc15c-203">Fügen Sie ihn nicht über Constructor Injection ein, da hierdurch der Dienst ein Verhalten wie ein Singleton zeigt.</span><span class="sxs-lookup"><span data-stu-id="fc15c-203">Don't inject via constructor injection because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="fc15c-204">Weitere Informationen finden Sie unter <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="fc15c-204">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="fc15c-205">Singleton</span><span class="sxs-lookup"><span data-stu-id="fc15c-205">Singleton</span></span>

<span data-ttu-id="fc15c-206">Dienste mit Singleton-Lebensdauer (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) werden bei der ersten Anforderung erstellt (bzw. wenn `Startup.ConfigureServices` ausgeführt wird, und eine Instanz bei der Dienstregistrierung angegeben wird).</span><span class="sxs-lookup"><span data-stu-id="fc15c-206">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="fc15c-207">Jeder nachfolgenden Anforderung verwendet die gleiche Instanz.</span><span class="sxs-lookup"><span data-stu-id="fc15c-207">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="fc15c-208">Wenn die App ein Verhalten vom Typ „Singleton“ erfordert, wird empfohlen, den Dienstcontainer die Dienstlebensdauer verwalten zu lassen.</span><span class="sxs-lookup"><span data-stu-id="fc15c-208">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="fc15c-209">Implementieren Sie nicht das Designmuster „Singleton“ und stellen Sie Benutzercode bereit, um die Objektlebensdauer in der Klasse zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="fc15c-209">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="fc15c-210">Es ist riskant, einen bereichsbezogenen Dienst über ein Singleton aufzulösen.</span><span class="sxs-lookup"><span data-stu-id="fc15c-210">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="fc15c-211">Möglicherweise weist der Dienst bei der Verarbeitung nachfolgender Anforderungen einen falschen Status auf.</span><span class="sxs-lookup"><span data-stu-id="fc15c-211">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="fc15c-212">Dienstregistrierungsmethoden</span><span class="sxs-lookup"><span data-stu-id="fc15c-212">Service registration methods</span></span>

<span data-ttu-id="fc15c-213">Methoden zur Erweiterung der Dienstregistrierung bieten Überladungen, die in bestimmten Szenarios hilfreich sind.</span><span class="sxs-lookup"><span data-stu-id="fc15c-213">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="fc15c-214">Methode</span><span class="sxs-lookup"><span data-stu-id="fc15c-214">Method</span></span> | <span data-ttu-id="fc15c-215">Automatische</span><span class="sxs-lookup"><span data-stu-id="fc15c-215">Automatic</span></span><br><span data-ttu-id="fc15c-216">Objekt</span><span class="sxs-lookup"><span data-stu-id="fc15c-216">object</span></span><br><span data-ttu-id="fc15c-217">bereinigung</span><span class="sxs-lookup"><span data-stu-id="fc15c-217">disposal</span></span> | <span data-ttu-id="fc15c-218">Mehrere</span><span class="sxs-lookup"><span data-stu-id="fc15c-218">Multiple</span></span><br><span data-ttu-id="fc15c-219">Implementierungen</span><span class="sxs-lookup"><span data-stu-id="fc15c-219">implementations</span></span> | <span data-ttu-id="fc15c-220">Argumentübergabe</span><span class="sxs-lookup"><span data-stu-id="fc15c-220">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="fc15c-221">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="fc15c-221">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="fc15c-222">Ja</span><span class="sxs-lookup"><span data-stu-id="fc15c-222">Yes</span></span> | <span data-ttu-id="fc15c-223">Ja</span><span class="sxs-lookup"><span data-stu-id="fc15c-223">Yes</span></span> | <span data-ttu-id="fc15c-224">Nein</span><span class="sxs-lookup"><span data-stu-id="fc15c-224">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="fc15c-225">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="fc15c-225">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="fc15c-226">Ja</span><span class="sxs-lookup"><span data-stu-id="fc15c-226">Yes</span></span> | <span data-ttu-id="fc15c-227">Ja</span><span class="sxs-lookup"><span data-stu-id="fc15c-227">Yes</span></span> | <span data-ttu-id="fc15c-228">Ja</span><span class="sxs-lookup"><span data-stu-id="fc15c-228">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="fc15c-229">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="fc15c-229">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="fc15c-230">Ja</span><span class="sxs-lookup"><span data-stu-id="fc15c-230">Yes</span></span> | <span data-ttu-id="fc15c-231">Nein</span><span class="sxs-lookup"><span data-stu-id="fc15c-231">No</span></span> | <span data-ttu-id="fc15c-232">Nein</span><span class="sxs-lookup"><span data-stu-id="fc15c-232">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="fc15c-233">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="fc15c-233">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="fc15c-234">Nein</span><span class="sxs-lookup"><span data-stu-id="fc15c-234">No</span></span> | <span data-ttu-id="fc15c-235">Ja</span><span class="sxs-lookup"><span data-stu-id="fc15c-235">Yes</span></span> | <span data-ttu-id="fc15c-236">Ja</span><span class="sxs-lookup"><span data-stu-id="fc15c-236">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="fc15c-237">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="fc15c-237">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="fc15c-238">Nein</span><span class="sxs-lookup"><span data-stu-id="fc15c-238">No</span></span> | <span data-ttu-id="fc15c-239">Nein</span><span class="sxs-lookup"><span data-stu-id="fc15c-239">No</span></span> | <span data-ttu-id="fc15c-240">Ja</span><span class="sxs-lookup"><span data-stu-id="fc15c-240">Yes</span></span> |

<span data-ttu-id="fc15c-241">Weitere Informationen zum Löschen von Typen finden Sie im Abschnitt [Löschen von Diensten](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="fc15c-241">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="fc15c-242">Ein häufiges Szenario für mehrere Implementierungen ist [Typen zu Testzwecken simulieren](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="fc15c-242">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="fc15c-243">`TryAdd{LIFETIME}`-Methoden registrieren den Dienst nur, wenn noch keine Implementierung registriert ist.</span><span class="sxs-lookup"><span data-stu-id="fc15c-243">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="fc15c-244">Im folgenden Beispiel registriert die erste Zeile `MyDependency` für `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="fc15c-244">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="fc15c-245">Die zweite Zeile hat keine Auswirkungen, da es für `IMyDependency` bereits eine registrierte Implementierung gibt:</span><span class="sxs-lookup"><span data-stu-id="fc15c-245">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="fc15c-246">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="fc15c-246">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="fc15c-247">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*)-Methoden registrieren den Dienst nur, wenn es nicht bereits eine Implementierung *des gleichen Typs* gibt.</span><span class="sxs-lookup"><span data-stu-id="fc15c-247">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="fc15c-248">Mehrere Dienste werden über `IEnumerable<{SERVICE}>` aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="fc15c-248">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="fc15c-249">Beim Registrieren von Diensten möchte der Entwickler nur eine Instanz hinzufügen, wenn nicht bereits eine Instanz vom gleichen Typ hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="fc15c-249">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="fc15c-250">Diese Methode wird in der Regel von Bibliotheksautoren verwendet, um zu vermeiden, dass zwei Kopien einer Instanz im Container registriert werden.</span><span class="sxs-lookup"><span data-stu-id="fc15c-250">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="fc15c-251">Im folgenden Beispiel registriert die erste Zeile `MyDep` für `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="fc15c-251">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="fc15c-252">Die zweite Zeile registriert `MyDep` für `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="fc15c-252">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="fc15c-253">Die dritte Zeile hat keine Auswirkungen, da es für `IMyDep1` bereits eine registrierte Implementierung von `MyDep` gibt:</span><span class="sxs-lookup"><span data-stu-id="fc15c-253">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="fc15c-254">Verhalten von Constructor Injection</span><span class="sxs-lookup"><span data-stu-id="fc15c-254">Constructor injection behavior</span></span>

<span data-ttu-id="fc15c-255">Dienste können durch zwei Mechanismen aufgelöst werden:</span><span class="sxs-lookup"><span data-stu-id="fc15c-255">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="fc15c-256"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; lässt die Erstellung von Objekten ohne Dienstregistrierung im Abhängigkeitsinjektionscontainer zu.</span><span class="sxs-lookup"><span data-stu-id="fc15c-256"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="fc15c-257">`ActivatorUtilities` wird mit Abstraktionen für Benutzer verwendet. Dazu zählen Taghilfsprogramme, MVC-Controller und Modellbindungen.</span><span class="sxs-lookup"><span data-stu-id="fc15c-257">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="fc15c-258">Konstruktoren können Argumente akzeptieren, die nicht durch Abhängigkeitsinjektion bereitgestellt werden. Die Argumente müssen jedoch Standardwerte zuweisen.</span><span class="sxs-lookup"><span data-stu-id="fc15c-258">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="fc15c-259">Wenn Dienste durch `IServiceProvider` oder `ActivatorUtilities` aufgelöst werden, benötigt Constructor Injection einen *öffentlichen* Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="fc15c-259">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, constructor injection requires a *public* constructor.</span></span>

<span data-ttu-id="fc15c-260">Wenn Dienste durch `ActivatorUtilities` aufgelöst werden, erfordert Constructor Injection, dass nur ein anwendbarer Konstruktor vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="fc15c-260">When services are resolved by `ActivatorUtilities`, constructor injection requires that only one applicable constructor exists.</span></span> <span data-ttu-id="fc15c-261">Konstruktorüberladungen werden unterstützt. Es darf jedoch nur eine Überladung vorhanden sein, deren Argumente alle durch Dependency Injection erfüllt werden können.</span><span class="sxs-lookup"><span data-stu-id="fc15c-261">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="fc15c-262">Entity Framework-Kontexte</span><span class="sxs-lookup"><span data-stu-id="fc15c-262">Entity Framework contexts</span></span>

<span data-ttu-id="fc15c-263">Entity Framework-Kontexte werden einem Dienstcontainer normalerweise mithilfe der [bereichsbezogenen Lebensdauer](#service-lifetimes) hinzugefügt, da Datenbankvorgänge von Web-Apps normalerweise auf den Clientanforderungsbereich bezogen werden.</span><span class="sxs-lookup"><span data-stu-id="fc15c-263">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="fc15c-264">Der Bereich einer Standardlebensdauer wird festgelegt, wenn eine Lebensdauer nicht von einer [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)-Überladung angegeben wird, wenn der Datenbankkontext registriert wird.</span><span class="sxs-lookup"><span data-stu-id="fc15c-264">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="fc15c-265">Dienste einer festgelegten Lebensdauer sollten keinen Datenbankkontext mit einer kürzeren Lebensdauer als die des Dienstes verwenden.</span><span class="sxs-lookup"><span data-stu-id="fc15c-265">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="fc15c-266">Lebensdauer und Registrierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="fc15c-266">Lifetime and registration options</span></span>

<span data-ttu-id="fc15c-267">Um den Unterschied zwischen der Lebensdauer und den Registrierungsoptionen zu demonstrieren, betrachten Sie die folgenden Schnittstellen, die Aufgaben als Vorgänge mit einem eindeutigen Bezeichner (`OperationId`) darstellen.</span><span class="sxs-lookup"><span data-stu-id="fc15c-267">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="fc15c-268">Je nachdem, wie die Dienstlebensdauer für die folgenden Schnittstellen konfiguriert ist, stellt der Container auf Anforderung einer Klasse entweder die gleiche oder eine andere Instanz des Diensts zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="fc15c-268">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="fc15c-269">Die Schnittstellen sind in die Klasse `Operation` implementiert.</span><span class="sxs-lookup"><span data-stu-id="fc15c-269">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="fc15c-270">Der `Operation`-Konstruktor generiert eine GUID, wenn noch keine angegeben ist:</span><span class="sxs-lookup"><span data-stu-id="fc15c-270">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="fc15c-271">`OperationService` ist registriert und hängt von jedem anderen `Operation`-Typ ab.</span><span class="sxs-lookup"><span data-stu-id="fc15c-271">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="fc15c-272">Wenn `OperationService` über die Abhängigkeitsinjektion angefordert wird, wird entweder eine neue Instanz jedes Diensts oder eine vorhandene Instanz basierend auf der Lebensdauer des abhängigen Diensts zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="fc15c-272">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="fc15c-273">Wenn vorübergehende Dienste bei der Anforderung aus dem Container erstellt werden, ist die `OperationId` von Dienst `IOperationTransient` anders als die `OperationId` von `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="fc15c-273">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="fc15c-274">`OperationService` erhält eine neue Instanz der Klasse `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="fc15c-274">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="fc15c-275">Der `OperationId`-Wert der neuen Instanz ist anders.</span><span class="sxs-lookup"><span data-stu-id="fc15c-275">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="fc15c-276">Wenn bereichsbezogene Dienste pro Clientanforderung erstellt werden, ist die `OperationId` in Dienst `IOperationScoped` und `OperationService` identisch innerhalb einer Clientanforderung.</span><span class="sxs-lookup"><span data-stu-id="fc15c-276">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="fc15c-277">Clientanforderungsübergreifend haben die beiden Dienste jedoch einen anderen gemeinsamen `OperationId`-Wert.</span><span class="sxs-lookup"><span data-stu-id="fc15c-277">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="fc15c-278">Wenn Singleton- und Singletoninstanzdienste einmal erstellt und für alle Clientanforderungen und alle Dienste verwendet werden, ist `OperationId` für alle Dienstanforderungen identisch.</span><span class="sxs-lookup"><span data-stu-id="fc15c-278">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="fc15c-279">In `Startup.ConfigureServices` wird jeder Typ entsprechend seiner benannten Lebensdauer dem Container hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="fc15c-279">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

::: moniker-end

<span data-ttu-id="fc15c-280">Der Dienst `IOperationSingletonInstance` verwendet eine bestimmte Instanz mit einer bekannten ID von `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="fc15c-280">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="fc15c-281">Die Verwendung dieses Typs ist eindeutig (die GUID besteht ausschließlich aus Nullen (0)).</span><span class="sxs-lookup"><span data-stu-id="fc15c-281">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="fc15c-282">Die Beispiel-App zeigt die Objektlebensdauer innerhalb einer Anforderung und zwischen einzelnen Anforderungen an.</span><span class="sxs-lookup"><span data-stu-id="fc15c-282">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="fc15c-283">Ihre Klasse `IndexModel` fordert jeden `IOperation`- und `OperationService`-Typ an.</span><span class="sxs-lookup"><span data-stu-id="fc15c-283">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="fc15c-284">Die Seite zeigt dann alle `OperationId`-Werte der Seitenmodellklasse und des Diensts über Eigenschaftenzuweisungen an:</span><span class="sxs-lookup"><span data-stu-id="fc15c-284">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

::: moniker-end

<span data-ttu-id="fc15c-285">Zwei folgende Ausgaben zeigen die Ergebnisse von zwei Anforderungen:</span><span class="sxs-lookup"><span data-stu-id="fc15c-285">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="fc15c-286">**Erste Anforderung:**</span><span class="sxs-lookup"><span data-stu-id="fc15c-286">**First request:**</span></span>

<span data-ttu-id="fc15c-287">Controllervorgänge:</span><span class="sxs-lookup"><span data-stu-id="fc15c-287">Controller operations:</span></span>

<span data-ttu-id="fc15c-288">Vorübergehend: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="fc15c-288">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="fc15c-289">Bereichsbezogen: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="fc15c-289">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="fc15c-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="fc15c-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="fc15c-291">Instanz: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="fc15c-291">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="fc15c-292">`OperationService`-Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="fc15c-292">`OperationService` operations:</span></span>

<span data-ttu-id="fc15c-293">Vorübergehend: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="fc15c-293">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="fc15c-294">Bereichsbezogen: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="fc15c-294">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="fc15c-295">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="fc15c-295">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="fc15c-296">Instanz: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="fc15c-296">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="fc15c-297">**Zweite Anforderung:**</span><span class="sxs-lookup"><span data-stu-id="fc15c-297">**Second request:**</span></span>

<span data-ttu-id="fc15c-298">Controllervorgänge:</span><span class="sxs-lookup"><span data-stu-id="fc15c-298">Controller operations:</span></span>

<span data-ttu-id="fc15c-299">Vorübergehend: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="fc15c-299">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="fc15c-300">Bereichsbezogen: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="fc15c-300">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="fc15c-301">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="fc15c-301">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="fc15c-302">Instanz: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="fc15c-302">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="fc15c-303">`OperationService`-Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="fc15c-303">`OperationService` operations:</span></span>

<span data-ttu-id="fc15c-304">Vorübergehend: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="fc15c-304">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="fc15c-305">Bereichsbezogen: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="fc15c-305">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="fc15c-306">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="fc15c-306">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="fc15c-307">Instanz: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="fc15c-307">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="fc15c-308">Beachten Sie, welche der `OperationId`-Werte innerhalb einer Anforderung und zwischen Anforderungen variieren:</span><span class="sxs-lookup"><span data-stu-id="fc15c-308">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="fc15c-309">Objekte vom Typ *Vorübergehend* sind immer unterschiedlich.</span><span class="sxs-lookup"><span data-stu-id="fc15c-309">*Transient* objects are always different.</span></span> <span data-ttu-id="fc15c-310">Der vorübergehende `OperationId`-Wert für die ersten und zweiten Clientanforderungen ist für beide `OperationService`-Vorgänge und clientanforderungsübergreifend unterschiedlich.</span><span class="sxs-lookup"><span data-stu-id="fc15c-310">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="fc15c-311">Eine neue Instanz wird für jede Dienstanforderung und jede Clientanforderung bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="fc15c-311">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="fc15c-312">*Bereichsbezogene* Objekte sind innerhalb einer Clientanforderung identisch, clientanforderungsübergreifend sind sie jedoch unterschiedlich.</span><span class="sxs-lookup"><span data-stu-id="fc15c-312">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="fc15c-313">Objekte vom Typ *Singleton* sind bei jedem Objekt und in jeder Anforderung identisch (unabhängig davon, ob eine `Operation`-Instanz in `Startup.ConfigureServices` bereitgestellt wird).</span><span class="sxs-lookup"><span data-stu-id="fc15c-313">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="fc15c-314">Abrufen von Diensten aus „Main“</span><span class="sxs-lookup"><span data-stu-id="fc15c-314">Call services from main</span></span>

<span data-ttu-id="fc15c-315">Erstellen Sie <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> mit [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*), um einen bereichsbezogenen Dienst innerhalb des Anwendungsbereichs aufzulösen.</span><span class="sxs-lookup"><span data-stu-id="fc15c-315">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="fc15c-316">Dieser Ansatz eignet sich gut dafür, beim Start auf einen bereichsbezogenen Dienst zuzugreifen und Initialisierungsaufgaben auszuführen.</span><span class="sxs-lookup"><span data-stu-id="fc15c-316">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="fc15c-317">Das folgende Beispiel veranschaulicht, wie man einen Kontext für `MyScopedService` in `Program.Main` erhält:</span><span class="sxs-lookup"><span data-stu-id="fc15c-317">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateHostBuilder(args).Build();

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
    
        await host.RunAsync();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;

public class Program
{
    public static async Task Main(string[] args)
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
    
        await host.RunAsync();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

::: moniker-end

## <a name="scope-validation"></a><span data-ttu-id="fc15c-318">Bereichsvalidierung</span><span class="sxs-lookup"><span data-stu-id="fc15c-318">Scope validation</span></span>

<span data-ttu-id="fc15c-319">Wenn die App in der Entwicklungsumgebung ausgeführt wird, überprüft der Standarddienstanbieter, ob:</span><span class="sxs-lookup"><span data-stu-id="fc15c-319">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="fc15c-320">Bereichsbezogene Dienste nicht direkt oder indirekt vom Stammdienstanbieter aufgelöst werden</span><span class="sxs-lookup"><span data-stu-id="fc15c-320">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="fc15c-321">Bereichsbezogene Dienste nicht direkt oder indirekt in Singletons eingefügt werden</span><span class="sxs-lookup"><span data-stu-id="fc15c-321">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="fc15c-322">Der Stammdienstanbieter wird erstellt, wenn <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="fc15c-322">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="fc15c-323">Die Lebensdauer des Stammdienstanbieters entspricht der Lebensdauer der App/des Servers, wenn der Anbieter mit der App erstellt wird und verworfen wird, wenn die App beendet wird.</span><span class="sxs-lookup"><span data-stu-id="fc15c-323">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="fc15c-324">Bereichsbezogene Dienste werden von dem Container verworfen, der sie erstellt hat.</span><span class="sxs-lookup"><span data-stu-id="fc15c-324">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="fc15c-325">Wenn ein bereichsbezogener Dienst im Stammcontainer erstellt wird, wird die Lebensdauer effektiv auf Singleton heraufgestuft, da er nur vom Stammcontainer verworfen wird, wenn die App/der Server heruntergefahren wird.</span><span class="sxs-lookup"><span data-stu-id="fc15c-325">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="fc15c-326">Die Überprüfung bereichsbezogener Dienste erfasst diese Situationen, wenn `BuildServiceProvider` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="fc15c-326">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="fc15c-327">Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="fc15c-327">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="fc15c-328">Anfordern von Diensten</span><span class="sxs-lookup"><span data-stu-id="fc15c-328">Request Services</span></span>

<span data-ttu-id="fc15c-329">Die Dienste, die innerhalb einer ASP.NET Core-Anforderung von `HttpContext` verfügbar sind, werden über die [HttpContext.RequestService](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices)-Sammlung verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="fc15c-329">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="fc15c-330">Anforderungsdienste stellen die Dienste dar, die als Teil der App konfiguriert und angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="fc15c-330">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="fc15c-331">Wenn Objekte Abhängigkeiten angeben, werden diese von den in `RequestServices` gefundenen Typen erfüllt (nicht in `ApplicationServices`).</span><span class="sxs-lookup"><span data-stu-id="fc15c-331">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="fc15c-332">Generell sollte die App diese Eigenschaften nicht direkt verwenden.</span><span class="sxs-lookup"><span data-stu-id="fc15c-332">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="fc15c-333">Fordern Sie stattdessen die von Klassen benötigten Typen über Klassenkonstruktoren an, und lassen Sie das Framework die Abhängigkeiten einfügen.</span><span class="sxs-lookup"><span data-stu-id="fc15c-333">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="fc15c-334">So erhalten Sie Klassen, die einfacher getestet werden können.</span><span class="sxs-lookup"><span data-stu-id="fc15c-334">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="fc15c-335">Fordern Sie für den Zugriff auf die `RequestServices`-Sammlung Abhängigkeiten lieber als Konstruktorparameter an.</span><span class="sxs-lookup"><span data-stu-id="fc15c-335">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="fc15c-336">Entwerfen von Diensten für die Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="fc15c-336">Design services for dependency injection</span></span>

<span data-ttu-id="fc15c-337">Best Practices:</span><span class="sxs-lookup"><span data-stu-id="fc15c-337">Best practices are to:</span></span>

* <span data-ttu-id="fc15c-338">Entwerfen Sie Dienste zur Verwendung der Abhängigkeitsinjektion, um ihre Abhängigkeiten zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="fc15c-338">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="fc15c-339">Vermeiden Sie zustandsbehaftete statische Klassen und Member.</span><span class="sxs-lookup"><span data-stu-id="fc15c-339">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="fc15c-340">Entwerfen Sie Apps so, dass stattdessen Singletondienste verwendet werden. Diese vermeiden das Entstehen eines globalen Zustands.</span><span class="sxs-lookup"><span data-stu-id="fc15c-340">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="fc15c-341">Vermeiden Sie die direkte Instanziierung abhängiger Klassen innerhalb von Diensten.</span><span class="sxs-lookup"><span data-stu-id="fc15c-341">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="fc15c-342">Die direkte Instanziierung koppelt den Code an eine bestimmte Implementierung.</span><span class="sxs-lookup"><span data-stu-id="fc15c-342">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="fc15c-343">Erstellen Sie kleine, gut gestaltete und einfach zu testende Apps.</span><span class="sxs-lookup"><span data-stu-id="fc15c-343">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="fc15c-344">Wenn eine Klasse zu viele eingefügte Abhängigkeiten zu haben scheint, ist dies im Allgemeinen ein Zeichen dafür, dass die Klasse zu viele Aufgaben hat und gegen das [Single-Responsibility-Prinzip (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) (Prinzip der eindeutigen Verantwortlichkeit) verstößt.</span><span class="sxs-lookup"><span data-stu-id="fc15c-344">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="fc15c-345">Versuchen Sie, die Klasse umzugestalten, indem Sie einige ihrer Aufgaben in eine neue Klasse verschieben.</span><span class="sxs-lookup"><span data-stu-id="fc15c-345">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="fc15c-346">Beachten Sie, dass der Fokus der Razor Pages-Seitenmodellklassen und MVC-Controllerklassen auf der Benutzeroberfläche liegt.</span><span class="sxs-lookup"><span data-stu-id="fc15c-346">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="fc15c-347">Geschäftsregeln und Implementierungsdetails für den Datenzugriff sollten in Klassen aufbewahrt werden gemäß dem Prinzip [Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) (Trennung der Zuständigkeiten).</span><span class="sxs-lookup"><span data-stu-id="fc15c-347">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="fc15c-348">Löschen von Diensten</span><span class="sxs-lookup"><span data-stu-id="fc15c-348">Disposal of services</span></span>

<span data-ttu-id="fc15c-349">Der Container ruft <xref:System.IDisposable.Dispose*> für die erstellten <xref:System.IDisposable>-Typen auf.</span><span class="sxs-lookup"><span data-stu-id="fc15c-349">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="fc15c-350">Wenn eine Instanz dem Container per Benutzercode hinzugefügt wird, wird sie nicht automatisch gelöscht.</span><span class="sxs-lookup"><span data-stu-id="fc15c-350">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

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

## <a name="default-service-container-replacement"></a><span data-ttu-id="fc15c-351">Ersetzen von Standarddienstcontainern</span><span class="sxs-lookup"><span data-stu-id="fc15c-351">Default service container replacement</span></span>

<span data-ttu-id="fc15c-352">Der integrierte Dienstcontainer dient dazu, die Anforderungen des Frameworks und der meisten Consumer-Apps zu erfüllen.</span><span class="sxs-lookup"><span data-stu-id="fc15c-352">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="fc15c-353">Die Verwendung des integrierten Containers wird empfohlen, es sei denn, Sie benötigen ein bestimmtes Feature, das von diesem nicht unterstützt wird. Einige Beispiele:</span><span class="sxs-lookup"><span data-stu-id="fc15c-353">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="fc15c-354">Eigenschaftsinjektion</span><span class="sxs-lookup"><span data-stu-id="fc15c-354">Property injection</span></span>
* <span data-ttu-id="fc15c-355">Auf Namen basierende Injektion</span><span class="sxs-lookup"><span data-stu-id="fc15c-355">Injection based on name</span></span>
* <span data-ttu-id="fc15c-356">Untergeordnete Container</span><span class="sxs-lookup"><span data-stu-id="fc15c-356">Child containers</span></span>
* <span data-ttu-id="fc15c-357">Benutzerdefinierte Verwaltung der Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="fc15c-357">Custom lifetime management</span></span>
* <span data-ttu-id="fc15c-358">`Func<T>`-Unterstützung für die verzögerte Initialisierung</span><span class="sxs-lookup"><span data-stu-id="fc15c-358">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="fc15c-359">Konventionsbasierte Registrierung</span><span class="sxs-lookup"><span data-stu-id="fc15c-359">Convention-based registration</span></span>

<span data-ttu-id="fc15c-360">Die folgenden Container von Drittanbietern können mit ASP.NET Core-Apps verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="fc15c-360">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="fc15c-361">Autofac</span><span class="sxs-lookup"><span data-stu-id="fc15c-361">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="fc15c-362">DryIoc</span><span class="sxs-lookup"><span data-stu-id="fc15c-362">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="fc15c-363">Grace</span><span class="sxs-lookup"><span data-stu-id="fc15c-363">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="fc15c-364">LightInject</span><span class="sxs-lookup"><span data-stu-id="fc15c-364">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="fc15c-365">Lamar</span><span class="sxs-lookup"><span data-stu-id="fc15c-365">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="fc15c-366">Stashbox</span><span class="sxs-lookup"><span data-stu-id="fc15c-366">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="fc15c-367">Unity</span><span class="sxs-lookup"><span data-stu-id="fc15c-367">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="fc15c-368">Threadsicherheit</span><span class="sxs-lookup"><span data-stu-id="fc15c-368">Thread safety</span></span>

<span data-ttu-id="fc15c-369">Erstellen Sie threadsichere Singleton-Dienste.</span><span class="sxs-lookup"><span data-stu-id="fc15c-369">Create thread-safe singleton services.</span></span> <span data-ttu-id="fc15c-370">Wenn ein Singleton-Dienst eine Abhängigkeit von einem vorübergehenden Dienst aufweist, muss der vorübergehende Dienst abhängig von der Verwendungsweise durch das Singleton ebenfalls threadsicher sein.</span><span class="sxs-lookup"><span data-stu-id="fc15c-370">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="fc15c-371">Die Factorymethode des einzelnen Diensts, z. B. das zweite Argument für [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), muss nicht threadsicher sein.</span><span class="sxs-lookup"><span data-stu-id="fc15c-371">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="fc15c-372">Wie bei `static`-Konstruktoren erfolgt der Aufruf einmalig über einen einzelnen Thread.</span><span class="sxs-lookup"><span data-stu-id="fc15c-372">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="fc15c-373">Empfehlungen</span><span class="sxs-lookup"><span data-stu-id="fc15c-373">Recommendations</span></span>

* <span data-ttu-id="fc15c-374">`async/await`- und `Task`-basierte Dienstauflösung wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="fc15c-374">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="fc15c-375">C# unterstützt keine asynchronen Konstruktoren. Daher wird empfohlen, asynchrone Methoden zu verwenden, nachdem der Dienst synchron aufgelöst wurde.</span><span class="sxs-lookup"><span data-stu-id="fc15c-375">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="fc15c-376">Vermeiden Sie das Speichern von Daten und die direkte Konfiguration im Dienstcontainer.</span><span class="sxs-lookup"><span data-stu-id="fc15c-376">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="fc15c-377">Der Einkaufswagen eines Benutzers sollte z. B. normalerweise nicht dem Dienstcontainer hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="fc15c-377">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="fc15c-378">Bei der Konfiguration sollte das [Optionsmuster](xref:fundamentals/configuration/options) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="fc15c-378">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="fc15c-379">Gleichermaßen sollten Sie „Datencontainer“-Objekte vermeiden, die nur vorhanden sind, um den Zugriff auf einige andere Objekte zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="fc15c-379">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="fc15c-380">Das tatsächlich benötige Element sollte besser über Dependency Injection angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="fc15c-380">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="fc15c-381">Vermeiden Sie den statischen Zugriff auf Dienste (z. B. statische Eingabe von [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) zur Verwendung an anderer Stelle).</span><span class="sxs-lookup"><span data-stu-id="fc15c-381">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="fc15c-382">Vermeiden Sie die Verwendung von *Dienstlocator-Mustern*.</span><span class="sxs-lookup"><span data-stu-id="fc15c-382">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="fc15c-383">Rufen Sie beispielsweise nicht <xref:System.IServiceProvider.GetService*> auf, um eine Dienstinstanz zu erhalten, wenn Sie stattdessen Dependency Injection verwenden können:</span><span class="sxs-lookup"><span data-stu-id="fc15c-383">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="fc15c-384">**Falsch:**</span><span class="sxs-lookup"><span data-stu-id="fc15c-384">**Incorrect:**</span></span>

  ```csharp
  public class MyClass()
  {
      public void MyMethod()
      {
          var optionsMonitor = 
              _services.GetService<IOptionsMonitor<MyOptions>>();
          var option = optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```

  <span data-ttu-id="fc15c-385">**Richtig**:</span><span class="sxs-lookup"><span data-stu-id="fc15c-385">**Correct**:</span></span>

  ```csharp
  public class MyClass
  {
      private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

      public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
      {
          _optionsMonitor = optionsMonitor;
      }

      public void MyMethod()
      {
          var option = _optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```

* <span data-ttu-id="fc15c-386">Eine andere Dienstlocator-Variante, die Sie vermeiden sollten, ist die Injektion einer Factory, die zur Laufzeit Abhängigkeiten auflöst.</span><span class="sxs-lookup"><span data-stu-id="fc15c-386">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="fc15c-387">Beide Vorgehensweisen kombinieren Strategien zur [Umkehrung der Steuerung](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="fc15c-387">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="fc15c-388">Vermeiden Sie den statischen Zugriff auf `HttpContext` (z. B. [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="fc15c-388">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="fc15c-389">Wie bei allen Empfehlungen treffen Sie möglicherweise auf Situationen, in denen eine Empfehlung ignoriert werden muss.</span><span class="sxs-lookup"><span data-stu-id="fc15c-389">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="fc15c-390">Es gibt nur wenige Ausnahmen &mdash; die sich meistens auf besondere Fälle innerhalb des Frameworks beziehen.</span><span class="sxs-lookup"><span data-stu-id="fc15c-390">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="fc15c-391">Dependency Injection stellt eine *Alternative* zu statischen bzw. globalen Objektzugriffsmustern dar.</span><span class="sxs-lookup"><span data-stu-id="fc15c-391">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="fc15c-392">Sie werden keinen Nutzen aus der Dependency Injection ziehen können, wenn Sie diese mit dem Zugriff auf statische Objekte kombinieren.</span><span class="sxs-lookup"><span data-stu-id="fc15c-392">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fc15c-393">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="fc15c-393">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="fc15c-394">Schreiben von sauberem Code in ASP.NET Core über Dependency Injection (MSDN)</span><span class="sxs-lookup"><span data-stu-id="fc15c-394">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="fc15c-395">Prinzip der expliziten Abhängigkeiten</span><span class="sxs-lookup"><span data-stu-id="fc15c-395">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* <span data-ttu-id="fc15c-396">[Umkehrung von Steuerungscontainern und das Abhängigkeitsinjektionsmuster (Martin Fowler)](https://www.martinfowler.com/articles/injection.html) (in englischer Sprache)</span><span class="sxs-lookup"><span data-stu-id="fc15c-396">[Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)](https://www.martinfowler.com/articles/injection.html)</span></span>
* [<span data-ttu-id="fc15c-397">Registrieren eines Diensts mit mehreren Schnittstellen in ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="fc15c-397">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)
