---
title: Dependency Injection in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie ASP.NET Core Dependency Injection implementiert und wie Sie dieses Muster verwenden können.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/21/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: 34ed08a5b49b56fd37628032ac73fe03a34448e6
ms.sourcegitcommit: dd2a1542a4a377123490034153368c135fdbd09e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85240848"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="1717d-103">Dependency Injection in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1717d-103">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="1717d-104">Von [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), und [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="1717d-104">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1717d-105">ASP.NET Core unterstützt das Softwareentwurfsmuster Abhängigkeitsinjektion. Damit kann eine [Umkehrung der Steuerung](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) (Inversion of Control, IoC) zwischen Klassen und ihren Abhängigkeiten erreicht werden.</span><span class="sxs-lookup"><span data-stu-id="1717d-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="1717d-106">Weitere Informationen zur Abhängigkeitsinjektion innerhalb von MVC-Controllern finden Sie unter <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="1717d-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="1717d-107">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1717d-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="1717d-108">Übersicht über Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="1717d-108">Overview of dependency injection</span></span>

<span data-ttu-id="1717d-109">Eine *Abhängigkeit* ist ein beliebiges Objekt, das ein anderes Objekt benötigt.</span><span class="sxs-lookup"><span data-stu-id="1717d-109">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="1717d-110">Überprüfen Sie die folgende `MyDependency`-Klasse mit einer `WriteMessage`-Methode, von der andere Klassen in einer App abhängen:</span><span class="sxs-lookup"><span data-stu-id="1717d-110">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="1717d-111">Eine Instanz der Klasse `MyDependency` kann erstellt werden, um die `WriteMessage`-Methode einer Klasse zur Verfügung zu stellen.</span><span class="sxs-lookup"><span data-stu-id="1717d-111">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="1717d-112">Die Klasse `MyDependency` ist eine Abhängigkeit der Klasse `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="1717d-112">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="1717d-113">Die Klasse erstellt die Instanz `MyDependency` und hängt direkt von dieser ab.</span><span class="sxs-lookup"><span data-stu-id="1717d-113">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="1717d-114">Codeabhängigkeiten (wie im vorherigen Beispiel) sind problematisch und sollten aus folgenden Gründen vermieden werden:</span><span class="sxs-lookup"><span data-stu-id="1717d-114">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="1717d-115">Um `MyDependency` durch eine andere Implementierung zu ersetzen, muss die Klasse geändert werden.</span><span class="sxs-lookup"><span data-stu-id="1717d-115">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="1717d-116">Wenn `MyDependency` über Abhängigkeiten verfügt, müssen diese von der Klasse konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="1717d-116">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="1717d-117">In einem großen Projekt mit mehreren Klassen, die von `MyDependency` abhängig sind, wird der Konfigurationscode über die App verteilt.</span><span class="sxs-lookup"><span data-stu-id="1717d-117">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="1717d-118">Diese Implementierung ist nicht für Komponententests geeignet.</span><span class="sxs-lookup"><span data-stu-id="1717d-118">This implementation is difficult to unit test.</span></span> <span data-ttu-id="1717d-119">Die App sollte eine `MyDependency`-Modell- oder Stubklasse verwenden, was mit diesem Ansatz nicht möglich ist.</span><span class="sxs-lookup"><span data-stu-id="1717d-119">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="1717d-120">Die Abhängigkeitsinjektion löst dieses Problem mithilfe der folgenden Schritte:</span><span class="sxs-lookup"><span data-stu-id="1717d-120">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="1717d-121">Die Verwendung einer Schnittstelle oder Basisklasse zur Abstraktion der Abhängigkeitsimplementierung.</span><span class="sxs-lookup"><span data-stu-id="1717d-121">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="1717d-122">Registrierung der Abhängigkeit in einem Dienstcontainer.</span><span class="sxs-lookup"><span data-stu-id="1717d-122">Registration of the dependency in a service container.</span></span> <span data-ttu-id="1717d-123">ASP.NET Core stellt einen integrierten Dienstcontainer (<xref:System.IServiceProvider>) bereit.</span><span class="sxs-lookup"><span data-stu-id="1717d-123">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="1717d-124">Die Dienste werden in der App-Methode `Startup.ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="1717d-124">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="1717d-125">Die *Injektion* des Diensts in den Konstruktor der Klasse, wo er verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="1717d-125">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="1717d-126">Das Framework erstellt eine Instanz der Abhängigkeit und entfernt diese, wenn sie nicht mehr benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="1717d-126">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="1717d-127">In der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) definiert die `IMyDependency`-Schnittstelle eine Methode, die der Dienst für die App bereitstellt:</span><span class="sxs-lookup"><span data-stu-id="1717d-127">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="1717d-128">Diese Schnittstelle wird durch einen konkreten Typ (`MyDependency`) implementiert:</span><span class="sxs-lookup"><span data-stu-id="1717d-128">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="1717d-129">`MyDependency` fordert einen <xref:Microsoft.Extensions.Logging.ILogger`1> beim zugehörigen Konstruktor an.</span><span class="sxs-lookup"><span data-stu-id="1717d-129">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="1717d-130">Die Abhängigkeitsinjektion wird häufig als Verkettung verwendet.</span><span class="sxs-lookup"><span data-stu-id="1717d-130">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="1717d-131">Jede angeforderte Abhängigkeit fordert wiederum ihre eigenen Abhängigkeiten an.</span><span class="sxs-lookup"><span data-stu-id="1717d-131">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="1717d-132">Der Container löst die Abhängigkeiten im Diagramm auf und gibt den vollständig aufgelösten Dienst zurück.</span><span class="sxs-lookup"><span data-stu-id="1717d-132">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="1717d-133">Die gesammelten aufzulösenden Abhängigkeiten werden als *Abhängigkeitsstruktur*, *Abhängigkeitsdiagramm* oder *Objektdiagramm* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="1717d-133">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="1717d-134">`IMyDependency` und `ILogger<TCategoryName>` müssen im Dienstcontainer registriert werden.</span><span class="sxs-lookup"><span data-stu-id="1717d-134">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="1717d-135">`IMyDependency` ist in `Startup.ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="1717d-135">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="1717d-136">`ILogger<TCategoryName>` wird von der Protokollierungsabstraktionsinfrastruktur registriert. Es handelt sich also um einen [von einem Framework bereitgestellten Dienst](#framework-provided-services), der standardmäßig vom Framework registriert wird.</span><span class="sxs-lookup"><span data-stu-id="1717d-136">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="1717d-137">Der Container löst `ILogger<TCategoryName>` unter Verwendung der [(generischen) offenen Typen](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types) auf, wodurch die Notwendigkeit entfällt, jeden [(generischen) konstruierten Typ](/dotnet/csharp/language-reference/language-specification/types#constructed-types) zu registrieren:</span><span class="sxs-lookup"><span data-stu-id="1717d-137">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="1717d-138">In der Beispiel-App ist der Dienst `IMyDependency` mit dem konkreten Typ `MyDependency` registriert.</span><span class="sxs-lookup"><span data-stu-id="1717d-138">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="1717d-139">Die Registrierung schränkt die Lebensdauer des Diensts auf die Lebensdauer einer einzelnen Anforderung ein.</span><span class="sxs-lookup"><span data-stu-id="1717d-139">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="1717d-140">Auf die [Dienstlebensdauer](#service-lifetimes) wird später in diesem Artikel eingegangen.</span><span class="sxs-lookup"><span data-stu-id="1717d-140">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="1717d-141">Jede Erweiterungsmethode vom Typ `services.Add{SERVICE_NAME}` fügt Dienste hinzu (und konfiguriert diese möglicherweise).</span><span class="sxs-lookup"><span data-stu-id="1717d-141">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="1717d-142">So fügt beispielsweise `services.AddMvc()` die erforderlichen Dienste Razor Pages und MVC hinzu.</span><span class="sxs-lookup"><span data-stu-id="1717d-142">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="1717d-143">Es wird empfohlen, dass Apps dieser Konvention folgen.</span><span class="sxs-lookup"><span data-stu-id="1717d-143">We recommended that apps follow this convention.</span></span> <span data-ttu-id="1717d-144">Platzieren Sie Erweiterungsmethoden im Namespace [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection), um Gruppen von Dienstregistrierungen zu kapseln.</span><span class="sxs-lookup"><span data-stu-id="1717d-144">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="1717d-145">Wenn für den Dienstkonstruktor ein [integrierter Typ](/dotnet/csharp/language-reference/keywords/built-in-types-table) erforderlich ist, z. B. `string`, kann dieser Typ über [Konfiguration](xref:fundamentals/configuration/index) oder das [Optionsmuster](xref:fundamentals/configuration/options) eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="1717d-145">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="1717d-146">Eine Instanz des Diensts wird über den Konstruktor einer Klasse angefordert, in der der Dienst verwendet wird, und einem privaten Feld zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="1717d-146">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="1717d-147">Das Feld wird verwendet, um auf den Dienst bei Bedarf über die gesamte Klasse zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="1717d-147">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="1717d-148">In der Beispiel-App wird die Instanz `IMyDependency` angefordert, und mit ihr wird die App-Methode `WriteMessage` aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="1717d-148">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="1717d-149">In den Start eingefügte Dienste</span><span class="sxs-lookup"><span data-stu-id="1717d-149">Services injected into Startup</span></span>

<span data-ttu-id="1717d-150">Bei Verwendung des generischen Hosts (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) können nur die folgenden Diensttypen in den `Startup`-Konstruktor eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="1717d-150">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="1717d-151">Dienste können in `Startup.Configure` eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="1717d-151">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="1717d-152">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="1717d-152">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="1717d-153">Von Frameworks bereitgestellte Dienste</span><span class="sxs-lookup"><span data-stu-id="1717d-153">Framework-provided services</span></span>

<span data-ttu-id="1717d-154">Die Methode `Startup.ConfigureServices` definiert die von der App verwendeten Dienste. Dies umfasst auch Plattformfeatures wie Entity Framework Core und ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="1717d-154">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="1717d-155">Zunächst enthält die in `ConfigureServices` bereitgestellte `IServiceCollection` die vom Framework definierten Dienste, abhängig davon, wie der [Host konfiguriert](xref:fundamentals/index#host) wurde.</span><span class="sxs-lookup"><span data-stu-id="1717d-155">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="1717d-156">Es ist nicht ungewöhnlich, dass das Framework Hunderte von Diensten für eine auf einer ASP.NET Core-Vorlage basierende App registriert.</span><span class="sxs-lookup"><span data-stu-id="1717d-156">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="1717d-157">In der folgenden Tabelle finden Sie eine kleine Auswahl der Dienste, die vom Framework registriert werden können.</span><span class="sxs-lookup"><span data-stu-id="1717d-157">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="1717d-158">Diensttyp</span><span class="sxs-lookup"><span data-stu-id="1717d-158">Service Type</span></span> | <span data-ttu-id="1717d-159">Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="1717d-159">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="1717d-160">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="1717d-160">Transient</span></span> |
| `IHostApplicationLifetime` | <span data-ttu-id="1717d-161">Singleton</span><span class="sxs-lookup"><span data-stu-id="1717d-161">Singleton</span></span> |
| `IWebHostEnvironment` | <span data-ttu-id="1717d-162">Singleton</span><span class="sxs-lookup"><span data-stu-id="1717d-162">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="1717d-163">Singleton</span><span class="sxs-lookup"><span data-stu-id="1717d-163">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="1717d-164">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="1717d-164">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="1717d-165">Singleton</span><span class="sxs-lookup"><span data-stu-id="1717d-165">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="1717d-166">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="1717d-166">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="1717d-167">Singleton</span><span class="sxs-lookup"><span data-stu-id="1717d-167">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="1717d-168">Singleton</span><span class="sxs-lookup"><span data-stu-id="1717d-168">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="1717d-169">Singleton</span><span class="sxs-lookup"><span data-stu-id="1717d-169">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="1717d-170">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="1717d-170">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="1717d-171">Singleton</span><span class="sxs-lookup"><span data-stu-id="1717d-171">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="1717d-172">Singleton</span><span class="sxs-lookup"><span data-stu-id="1717d-172">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="1717d-173">Singleton</span><span class="sxs-lookup"><span data-stu-id="1717d-173">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="1717d-174">Registrieren weiterer Dienste mit Erweiterungsmethoden</span><span class="sxs-lookup"><span data-stu-id="1717d-174">Register additional services with extension methods</span></span>

<span data-ttu-id="1717d-175">Wenn eine Dienstsammlungs-Erweiterungsmethode verfügbar ist, um einen Dienst (und ggf. seine abhängigen Dienste) zu registrieren, ist die Konvention, eine einzige `Add{SERVICE_NAME}`-Erweiterungsmethode zu verwenden, um alle von diesem Dienst benötigten Dienste zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="1717d-175">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="1717d-176">Der folgende Code ist ein Beispiel dafür, wie mit den Erweiterungsmethoden [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) und <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> zusätzliche Dienste zum Container hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="1717d-176">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="1717d-177">Weitere Informationen finden Sie in der <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection>-Klasse in der API-Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="1717d-177">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="1717d-178">Dienstlebensdauer</span><span class="sxs-lookup"><span data-stu-id="1717d-178">Service lifetimes</span></span>

<span data-ttu-id="1717d-179">Wählen Sie eine geeignete Lebensdauer für jeden registrierten Dienst aus.</span><span class="sxs-lookup"><span data-stu-id="1717d-179">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="1717d-180">ASP.NET Core-Dienste können mit folgender Lebensdauer konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="1717d-180">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="1717d-181">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="1717d-181">Transient</span></span>

<span data-ttu-id="1717d-182">Kurzlebige Dienste (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) werden bei jeder Anforderung aus dem Dienstcontainer neu erstellt.</span><span class="sxs-lookup"><span data-stu-id="1717d-182">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="1717d-183">Diese Lebensdauer ist am besten für einfache, zustandslose Dienste geeignet.</span><span class="sxs-lookup"><span data-stu-id="1717d-183">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="1717d-184">In Apps, die Anforderungen verarbeiten, werden vorübergehende Dienste am Ende der Anforderung verworfen.</span><span class="sxs-lookup"><span data-stu-id="1717d-184">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="1717d-185">Bereichsbezogen</span><span class="sxs-lookup"><span data-stu-id="1717d-185">Scoped</span></span>

<span data-ttu-id="1717d-186">Dienste mit bereichsbezogener Lebensdauer (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) werden einmal pro Clientanforderung (-verbindung) erstellt.</span><span class="sxs-lookup"><span data-stu-id="1717d-186">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="1717d-187">In Apps, die Anforderungen verarbeiten, werden bereichsbezogene Dienste am Ende der Anforderung verworfen.</span><span class="sxs-lookup"><span data-stu-id="1717d-187">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="1717d-188">Wenn Sie einen bereichsbezogenen Dienst in einer Middleware verwenden, müssen Sie den Dienst in die `Invoke`- oder `InvokeAsync`-Methode einfügen.</span><span class="sxs-lookup"><span data-stu-id="1717d-188">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="1717d-189">Fügen Sie ihn nicht über [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection) ein, da hierdurch der Dienst ein Verhalten wie ein Singleton zeigt.</span><span class="sxs-lookup"><span data-stu-id="1717d-189">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="1717d-190">Weitere Informationen finden Sie unter <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="1717d-190">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="1717d-191">Singleton</span><span class="sxs-lookup"><span data-stu-id="1717d-191">Singleton</span></span>

<span data-ttu-id="1717d-192">Dienste mit Singleton-Lebensdauer (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) werden bei der ersten Anforderung erstellt (bzw. wenn `Startup.ConfigureServices` ausgeführt wird, und eine Instanz bei der Dienstregistrierung angegeben wird).</span><span class="sxs-lookup"><span data-stu-id="1717d-192">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="1717d-193">Jeder nachfolgenden Anforderung verwendet die gleiche Instanz.</span><span class="sxs-lookup"><span data-stu-id="1717d-193">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="1717d-194">Wenn die App ein Verhalten vom Typ „Singleton“ erfordert, wird empfohlen, den Dienstcontainer die Dienstlebensdauer verwalten zu lassen.</span><span class="sxs-lookup"><span data-stu-id="1717d-194">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="1717d-195">Implementieren Sie nicht das Designmuster „Singleton“ und stellen Sie Benutzercode bereit, um die Objektlebensdauer in der Klasse zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="1717d-195">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="1717d-196">In Apps, die Anforderungen verarbeiten, werden Singleton-Dienste verworfen, wenn der <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> beim Herunterfahren der App verworfen wird.</span><span class="sxs-lookup"><span data-stu-id="1717d-196">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="1717d-197">Es ist riskant, einen bereichsbezogenen Dienst über ein Singleton aufzulösen.</span><span class="sxs-lookup"><span data-stu-id="1717d-197">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="1717d-198">Möglicherweise weist der Dienst bei der Verarbeitung nachfolgender Anforderungen einen falschen Status auf.</span><span class="sxs-lookup"><span data-stu-id="1717d-198">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="1717d-199">Dienstregistrierungsmethoden</span><span class="sxs-lookup"><span data-stu-id="1717d-199">Service registration methods</span></span>

<span data-ttu-id="1717d-200">Methoden zur Erweiterung der Dienstregistrierung bieten Überladungen, die in bestimmten Szenarios hilfreich sind.</span><span class="sxs-lookup"><span data-stu-id="1717d-200">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="1717d-201">Methode</span><span class="sxs-lookup"><span data-stu-id="1717d-201">Method</span></span> | <span data-ttu-id="1717d-202">Automatische</span><span class="sxs-lookup"><span data-stu-id="1717d-202">Automatic</span></span><br><span data-ttu-id="1717d-203">Objekt</span><span class="sxs-lookup"><span data-stu-id="1717d-203">object</span></span><br><span data-ttu-id="1717d-204">bereinigung</span><span class="sxs-lookup"><span data-stu-id="1717d-204">disposal</span></span> | <span data-ttu-id="1717d-205">Mehrere</span><span class="sxs-lookup"><span data-stu-id="1717d-205">Multiple</span></span><br><span data-ttu-id="1717d-206">Implementierungen</span><span class="sxs-lookup"><span data-stu-id="1717d-206">implementations</span></span> | <span data-ttu-id="1717d-207">Argumentübergabe</span><span class="sxs-lookup"><span data-stu-id="1717d-207">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="1717d-208">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="1717d-208">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="1717d-209">Ja</span><span class="sxs-lookup"><span data-stu-id="1717d-209">Yes</span></span> | <span data-ttu-id="1717d-210">Ja</span><span class="sxs-lookup"><span data-stu-id="1717d-210">Yes</span></span> | <span data-ttu-id="1717d-211">Nein</span><span class="sxs-lookup"><span data-stu-id="1717d-211">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="1717d-212">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="1717d-212">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="1717d-213">Ja</span><span class="sxs-lookup"><span data-stu-id="1717d-213">Yes</span></span> | <span data-ttu-id="1717d-214">Ja</span><span class="sxs-lookup"><span data-stu-id="1717d-214">Yes</span></span> | <span data-ttu-id="1717d-215">Ja</span><span class="sxs-lookup"><span data-stu-id="1717d-215">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="1717d-216">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="1717d-216">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="1717d-217">Ja</span><span class="sxs-lookup"><span data-stu-id="1717d-217">Yes</span></span> | <span data-ttu-id="1717d-218">Nein</span><span class="sxs-lookup"><span data-stu-id="1717d-218">No</span></span> | <span data-ttu-id="1717d-219">Nein</span><span class="sxs-lookup"><span data-stu-id="1717d-219">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="1717d-220">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="1717d-220">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="1717d-221">Nein</span><span class="sxs-lookup"><span data-stu-id="1717d-221">No</span></span> | <span data-ttu-id="1717d-222">Ja</span><span class="sxs-lookup"><span data-stu-id="1717d-222">Yes</span></span> | <span data-ttu-id="1717d-223">Ja</span><span class="sxs-lookup"><span data-stu-id="1717d-223">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="1717d-224">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="1717d-224">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="1717d-225">Nein</span><span class="sxs-lookup"><span data-stu-id="1717d-225">No</span></span> | <span data-ttu-id="1717d-226">Nein</span><span class="sxs-lookup"><span data-stu-id="1717d-226">No</span></span> | <span data-ttu-id="1717d-227">Ja</span><span class="sxs-lookup"><span data-stu-id="1717d-227">Yes</span></span> |

<span data-ttu-id="1717d-228">Weitere Informationen zum Löschen von Typen finden Sie im Abschnitt [Löschen von Diensten](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="1717d-228">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="1717d-229">Ein häufiges Szenario für mehrere Implementierungen ist [Typen zu Testzwecken simulieren](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="1717d-229">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="1717d-230">`TryAdd{LIFETIME}`-Methoden registrieren den Dienst nur, wenn noch keine Implementierung registriert ist.</span><span class="sxs-lookup"><span data-stu-id="1717d-230">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="1717d-231">Im folgenden Beispiel registriert die erste Zeile `MyDependency` für `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="1717d-231">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="1717d-232">Die zweite Zeile hat keine Auswirkungen, da es für `IMyDependency` bereits eine registrierte Implementierung gibt:</span><span class="sxs-lookup"><span data-stu-id="1717d-232">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="1717d-233">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="1717d-233">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="1717d-234">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*)-Methoden registrieren den Dienst nur, wenn es nicht bereits eine Implementierung *des gleichen Typs* gibt.</span><span class="sxs-lookup"><span data-stu-id="1717d-234">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="1717d-235">Mehrere Dienste werden über `IEnumerable<{SERVICE}>` aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="1717d-235">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="1717d-236">Beim Registrieren von Diensten möchte der Entwickler nur eine Instanz hinzufügen, wenn nicht bereits eine Instanz vom gleichen Typ hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="1717d-236">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="1717d-237">Diese Methode wird in der Regel von Bibliotheksautoren verwendet, um zu vermeiden, dass zwei Kopien einer Instanz im Container registriert werden.</span><span class="sxs-lookup"><span data-stu-id="1717d-237">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="1717d-238">Im folgenden Beispiel registriert die erste Zeile `MyDep` für `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="1717d-238">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="1717d-239">Die zweite Zeile registriert `MyDep` für `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="1717d-239">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="1717d-240">Die dritte Zeile hat keine Auswirkungen, da es für `IMyDep1` bereits eine registrierte Implementierung von `MyDep` gibt:</span><span class="sxs-lookup"><span data-stu-id="1717d-240">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="1717d-241">Verhalten von Constructor Injection</span><span class="sxs-lookup"><span data-stu-id="1717d-241">Constructor injection behavior</span></span>

<span data-ttu-id="1717d-242">Dienste können durch zwei Mechanismen aufgelöst werden:</span><span class="sxs-lookup"><span data-stu-id="1717d-242">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="1717d-243"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Lässt die Erstellung von Objekten ohne Dienstregistrierung im Dependency-Injection-Container zu</span><span class="sxs-lookup"><span data-stu-id="1717d-243"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="1717d-244">`ActivatorUtilities` wird mit Abstraktionen für Benutzer verwendet. Dazu zählen Taghilfsprogramme, MVC-Controller und Modellbindungen.</span><span class="sxs-lookup"><span data-stu-id="1717d-244">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="1717d-245">Konstruktoren können Argumente akzeptieren, die nicht durch Abhängigkeitsinjektion bereitgestellt werden. Die Argumente müssen jedoch Standardwerte zuweisen.</span><span class="sxs-lookup"><span data-stu-id="1717d-245">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="1717d-246">Wenn Dienste durch `IServiceProvider` oder `ActivatorUtilities` aufgelöst werden, benötigt die [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection) einen *öffentlichen* Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="1717d-246">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="1717d-247">Wenn Dienste durch `ActivatorUtilities` aufgelöst werden, erfordert die [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection), dass nur ein anwendbarer Konstruktor vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="1717d-247">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="1717d-248">Konstruktorüberladungen werden unterstützt. Es darf jedoch nur eine Überladung vorhanden sein, deren Argumente alle durch Dependency Injection erfüllt werden können.</span><span class="sxs-lookup"><span data-stu-id="1717d-248">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="1717d-249">Entity Framework-Kontexte</span><span class="sxs-lookup"><span data-stu-id="1717d-249">Entity Framework contexts</span></span>

<span data-ttu-id="1717d-250">Entity Framework-Kontexte werden einem Dienstcontainer normalerweise mithilfe der [bereichsbezogenen Lebensdauer](#service-lifetimes) hinzugefügt, da Datenbankvorgänge von Web-Apps normalerweise auf den Clientanforderungsbereich bezogen werden.</span><span class="sxs-lookup"><span data-stu-id="1717d-250">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="1717d-251">Der Bereich einer Standardlebensdauer wird festgelegt, wenn eine Lebensdauer nicht von einer [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)-Überladung angegeben wird, wenn der Datenbankkontext registriert wird.</span><span class="sxs-lookup"><span data-stu-id="1717d-251">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="1717d-252">Dienste einer festgelegten Lebensdauer sollten keinen Datenbankkontext mit einer kürzeren Lebensdauer als die des Dienstes verwenden.</span><span class="sxs-lookup"><span data-stu-id="1717d-252">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="1717d-253">Lebensdauer und Registrierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="1717d-253">Lifetime and registration options</span></span>

<span data-ttu-id="1717d-254">Um den Unterschied zwischen der Lebensdauer und den Registrierungsoptionen zu demonstrieren, betrachten Sie die folgenden Schnittstellen, die Aufgaben als Vorgänge mit einem eindeutigen Bezeichner (`OperationId`) darstellen.</span><span class="sxs-lookup"><span data-stu-id="1717d-254">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="1717d-255">Je nachdem, wie die Dienstlebensdauer für die folgenden Schnittstellen konfiguriert ist, stellt der Container auf Anforderung einer Klasse entweder die gleiche oder eine andere Instanz des Diensts zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="1717d-255">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="1717d-256">Die Schnittstellen sind in die Klasse `Operation` implementiert.</span><span class="sxs-lookup"><span data-stu-id="1717d-256">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="1717d-257">Der `Operation`-Konstruktor generiert eine GUID, wenn noch keine angegeben ist:</span><span class="sxs-lookup"><span data-stu-id="1717d-257">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="1717d-258">`OperationService` ist registriert und hängt von jedem anderen `Operation`-Typ ab.</span><span class="sxs-lookup"><span data-stu-id="1717d-258">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="1717d-259">Wenn `OperationService` über die Abhängigkeitsinjektion angefordert wird, wird entweder eine neue Instanz jedes Diensts oder eine vorhandene Instanz basierend auf der Lebensdauer des abhängigen Diensts zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="1717d-259">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="1717d-260">Wenn vorübergehende Dienste bei der Anforderung aus dem Container erstellt werden, ist die `OperationId` von Dienst `IOperationTransient` anders als die `OperationId` von `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="1717d-260">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="1717d-261">`OperationService` erhält eine neue Instanz der Klasse `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="1717d-261">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="1717d-262">Der `OperationId`-Wert der neuen Instanz ist anders.</span><span class="sxs-lookup"><span data-stu-id="1717d-262">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="1717d-263">Wenn bereichsbezogene Dienste pro Clientanforderung erstellt werden, ist die `OperationId` in Dienst `IOperationScoped` und `OperationService` identisch innerhalb einer Clientanforderung.</span><span class="sxs-lookup"><span data-stu-id="1717d-263">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="1717d-264">Clientanforderungsübergreifend haben die beiden Dienste jedoch einen anderen gemeinsamen `OperationId`-Wert.</span><span class="sxs-lookup"><span data-stu-id="1717d-264">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="1717d-265">Wenn Singleton- und Singletoninstanzdienste einmal erstellt und für alle Clientanforderungen und alle Dienste verwendet werden, ist `OperationId` für alle Dienstanforderungen identisch.</span><span class="sxs-lookup"><span data-stu-id="1717d-265">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="1717d-266">In `Startup.ConfigureServices` wird jeder Typ entsprechend seiner benannten Lebensdauer dem Container hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="1717d-266">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="1717d-267">Der Dienst `IOperationSingletonInstance` verwendet eine bestimmte Instanz mit einer bekannten ID von `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="1717d-267">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="1717d-268">Die Verwendung dieses Typs ist eindeutig (die GUID besteht ausschließlich aus Nullen (0)).</span><span class="sxs-lookup"><span data-stu-id="1717d-268">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="1717d-269">Die Beispiel-App zeigt die Objektlebensdauer innerhalb einer Anforderung und zwischen einzelnen Anforderungen an.</span><span class="sxs-lookup"><span data-stu-id="1717d-269">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="1717d-270">Ihre Klasse `IndexModel` fordert jeden `IOperation`- und `OperationService`-Typ an.</span><span class="sxs-lookup"><span data-stu-id="1717d-270">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="1717d-271">Die Seite zeigt dann alle `OperationId`-Werte der Seitenmodellklasse und des Diensts über Eigenschaftenzuweisungen an:</span><span class="sxs-lookup"><span data-stu-id="1717d-271">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="1717d-272">Zwei folgende Ausgaben zeigen die Ergebnisse von zwei Anforderungen:</span><span class="sxs-lookup"><span data-stu-id="1717d-272">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="1717d-273">**Erste Anforderung:**</span><span class="sxs-lookup"><span data-stu-id="1717d-273">**First request:**</span></span>

<span data-ttu-id="1717d-274">Controllervorgänge:</span><span class="sxs-lookup"><span data-stu-id="1717d-274">Controller operations:</span></span>

<span data-ttu-id="1717d-275">Vorübergehend: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="1717d-275">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="1717d-276">Bereichsbezogen: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="1717d-276">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="1717d-277">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="1717d-277">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="1717d-278">Instanz: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="1717d-278">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="1717d-279">`OperationService`-Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="1717d-279">`OperationService` operations:</span></span>

<span data-ttu-id="1717d-280">Vorübergehend: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="1717d-280">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="1717d-281">Bereichsbezogen: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="1717d-281">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="1717d-282">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="1717d-282">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="1717d-283">Instanz: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="1717d-283">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="1717d-284">**Zweite Anforderung:**</span><span class="sxs-lookup"><span data-stu-id="1717d-284">**Second request:**</span></span>

<span data-ttu-id="1717d-285">Controllervorgänge:</span><span class="sxs-lookup"><span data-stu-id="1717d-285">Controller operations:</span></span>

<span data-ttu-id="1717d-286">Vorübergehend: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="1717d-286">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="1717d-287">Bereichsbezogen: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="1717d-287">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="1717d-288">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="1717d-288">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="1717d-289">Instanz: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="1717d-289">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="1717d-290">`OperationService`-Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="1717d-290">`OperationService` operations:</span></span>

<span data-ttu-id="1717d-291">Vorübergehend: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="1717d-291">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="1717d-292">Bereichsbezogen: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="1717d-292">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="1717d-293">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="1717d-293">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="1717d-294">Instanz: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="1717d-294">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="1717d-295">Beachten Sie, welche der `OperationId`-Werte innerhalb einer Anforderung und zwischen Anforderungen variieren:</span><span class="sxs-lookup"><span data-stu-id="1717d-295">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="1717d-296">Objekte vom Typ *Vorübergehend* sind immer unterschiedlich.</span><span class="sxs-lookup"><span data-stu-id="1717d-296">*Transient* objects are always different.</span></span> <span data-ttu-id="1717d-297">Der vorübergehende `OperationId`-Wert für die ersten und zweiten Clientanforderungen ist für beide `OperationService`-Vorgänge und clientanforderungsübergreifend unterschiedlich.</span><span class="sxs-lookup"><span data-stu-id="1717d-297">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="1717d-298">Eine neue Instanz wird für jede Dienstanforderung und jede Clientanforderung bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="1717d-298">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="1717d-299">*Bereichsbezogene* Objekte sind innerhalb einer Clientanforderung identisch, clientanforderungsübergreifend sind sie jedoch unterschiedlich.</span><span class="sxs-lookup"><span data-stu-id="1717d-299">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="1717d-300">Objekte vom Typ *Singleton* sind bei jedem Objekt und in jeder Anforderung identisch (unabhängig davon, ob eine `Operation`-Instanz in `Startup.ConfigureServices` bereitgestellt wird).</span><span class="sxs-lookup"><span data-stu-id="1717d-300">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="1717d-301">Abrufen von Diensten aus „Main“</span><span class="sxs-lookup"><span data-stu-id="1717d-301">Call services from main</span></span>

<span data-ttu-id="1717d-302">Erstellen Sie <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> mit [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*), um einen bereichsbezogenen Dienst innerhalb des Anwendungsbereichs aufzulösen.</span><span class="sxs-lookup"><span data-stu-id="1717d-302">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="1717d-303">Dieser Ansatz eignet sich gut dafür, beim Start auf einen bereichsbezogenen Dienst zuzugreifen und Initialisierungsaufgaben auszuführen.</span><span class="sxs-lookup"><span data-stu-id="1717d-303">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="1717d-304">Das folgende Beispiel veranschaulicht, wie man einen Kontext für `MyScopedService` in `Program.Main` erhält:</span><span class="sxs-lookup"><span data-stu-id="1717d-304">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="1717d-305">Bereichsvalidierung</span><span class="sxs-lookup"><span data-stu-id="1717d-305">Scope validation</span></span>

<span data-ttu-id="1717d-306">Wenn die App in der Entwicklungsumgebung ausgeführt wird und [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) aufruft, um den Host zu erstellen, überprüft der Standarddienstanbieter, ob:</span><span class="sxs-lookup"><span data-stu-id="1717d-306">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="1717d-307">Bereichsbezogene Dienste nicht direkt oder indirekt vom Stammdienstanbieter aufgelöst werden</span><span class="sxs-lookup"><span data-stu-id="1717d-307">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="1717d-308">Bereichsbezogene Dienste nicht direkt oder indirekt in Singletons eingefügt werden</span><span class="sxs-lookup"><span data-stu-id="1717d-308">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="1717d-309">Der Stammdienstanbieter wird erstellt, wenn <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="1717d-309">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="1717d-310">Die Lebensdauer des Stammdienstanbieters entspricht der Lebensdauer der App/des Servers, wenn der Anbieter mit der App erstellt wird und verworfen wird, wenn die App beendet wird.</span><span class="sxs-lookup"><span data-stu-id="1717d-310">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="1717d-311">Bereichsbezogene Dienste werden von dem Container verworfen, der sie erstellt hat.</span><span class="sxs-lookup"><span data-stu-id="1717d-311">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="1717d-312">Wenn ein bereichsbezogener Dienst im Stammcontainer erstellt wird, wird die Lebensdauer effektiv auf Singleton heraufgestuft, da er nur vom Stammcontainer verworfen wird, wenn die App/der Server heruntergefahren wird.</span><span class="sxs-lookup"><span data-stu-id="1717d-312">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="1717d-313">Die Überprüfung bereichsbezogener Dienste erfasst diese Situationen, wenn `BuildServiceProvider` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="1717d-313">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="1717d-314">Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="1717d-314">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="1717d-315">Anfordern von Diensten</span><span class="sxs-lookup"><span data-stu-id="1717d-315">Request Services</span></span>

<span data-ttu-id="1717d-316">Die Dienste, die innerhalb einer ASP.NET Core-Anforderung von `HttpContext` verfügbar sind, werden über die [HttpContext.RequestService](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices)-Sammlung verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="1717d-316">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="1717d-317">Anforderungsdienste stellen die Dienste dar, die als Teil der App konfiguriert und angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="1717d-317">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="1717d-318">Wenn Objekte Abhängigkeiten angeben, werden diese von den in `RequestServices` gefundenen Typen erfüllt (nicht in `ApplicationServices`).</span><span class="sxs-lookup"><span data-stu-id="1717d-318">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="1717d-319">Generell sollte die App diese Eigenschaften nicht direkt verwenden.</span><span class="sxs-lookup"><span data-stu-id="1717d-319">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="1717d-320">Fordern Sie stattdessen die von Klassen benötigten Typen über Klassenkonstruktoren an, und lassen Sie das Framework die Abhängigkeiten einfügen.</span><span class="sxs-lookup"><span data-stu-id="1717d-320">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="1717d-321">So erhalten Sie Klassen, die einfacher getestet werden können.</span><span class="sxs-lookup"><span data-stu-id="1717d-321">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="1717d-322">Fordern Sie für den Zugriff auf die `RequestServices`-Sammlung Abhängigkeiten lieber als Konstruktorparameter an.</span><span class="sxs-lookup"><span data-stu-id="1717d-322">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="1717d-323">Entwerfen von Diensten für die Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="1717d-323">Design services for dependency injection</span></span>

<span data-ttu-id="1717d-324">Best Practices:</span><span class="sxs-lookup"><span data-stu-id="1717d-324">Best practices are to:</span></span>

* <span data-ttu-id="1717d-325">Entwerfen Sie Dienste zur Verwendung der Abhängigkeitsinjektion, um ihre Abhängigkeiten zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="1717d-325">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="1717d-326">Vermeiden Sie zustandsbehaftete statische Klassen und Member.</span><span class="sxs-lookup"><span data-stu-id="1717d-326">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="1717d-327">Entwerfen Sie Apps so, dass stattdessen Singletondienste verwendet werden. Diese vermeiden das Entstehen eines globalen Zustands.</span><span class="sxs-lookup"><span data-stu-id="1717d-327">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="1717d-328">Vermeiden Sie die direkte Instanziierung abhängiger Klassen innerhalb von Diensten.</span><span class="sxs-lookup"><span data-stu-id="1717d-328">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="1717d-329">Die direkte Instanziierung koppelt den Code an eine bestimmte Implementierung.</span><span class="sxs-lookup"><span data-stu-id="1717d-329">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="1717d-330">Erstellen Sie kleine, gut gestaltete und einfach zu testende Apps.</span><span class="sxs-lookup"><span data-stu-id="1717d-330">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="1717d-331">Wenn eine Klasse zu viele eingefügte Abhängigkeiten zu haben scheint, ist dies im Allgemeinen ein Zeichen dafür, dass die Klasse zu viele Aufgaben hat und gegen das [Single-Responsibility-Prinzip (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) (Prinzip der eindeutigen Verantwortlichkeit) verstößt.</span><span class="sxs-lookup"><span data-stu-id="1717d-331">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="1717d-332">Versuchen Sie, die Klasse umzugestalten, indem Sie einige ihrer Aufgaben in eine neue Klasse verschieben.</span><span class="sxs-lookup"><span data-stu-id="1717d-332">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="1717d-333">Beachten Sie, dass der Fokus der Razor Pages-Seitenmodellklassen und MVC-Controllerklassen auf der Benutzeroberfläche liegt.</span><span class="sxs-lookup"><span data-stu-id="1717d-333">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="1717d-334">Geschäftsregeln und Implementierungsdetails für den Datenzugriff sollten in Klassen aufbewahrt werden gemäß dem Prinzip [Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) (Trennung der Zuständigkeiten).</span><span class="sxs-lookup"><span data-stu-id="1717d-334">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="1717d-335">Löschen von Diensten</span><span class="sxs-lookup"><span data-stu-id="1717d-335">Disposal of services</span></span>

<span data-ttu-id="1717d-336">Der Container ruft <xref:System.IDisposable.Dispose*> für die erstellten <xref:System.IDisposable>-Typen auf.</span><span class="sxs-lookup"><span data-stu-id="1717d-336">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="1717d-337">Wenn eine Instanz dem Container per Benutzercode hinzugefügt wird, wird sie nicht automatisch gelöscht.</span><span class="sxs-lookup"><span data-stu-id="1717d-337">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="1717d-338">Im folgenden Beispiel werden die Dienste vom Dienstcontainer erstellt und automatisch verworfen:</span><span class="sxs-lookup"><span data-stu-id="1717d-338">In the following example, the services are created by the service container and disposed automatically:</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public interface IService3 {}
public class Service3 : IService3, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<IService3>(sp => new Service3());
}
```

<span data-ttu-id="1717d-339">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="1717d-339">In the following example:</span></span>

* <span data-ttu-id="1717d-340">werden die Dienstinstanzen nicht vom Dienstcontainer erstellt.</span><span class="sxs-lookup"><span data-stu-id="1717d-340">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="1717d-341">kennt das Framework die geplante Lebensdauer der Dienste nicht.</span><span class="sxs-lookup"><span data-stu-id="1717d-341">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="1717d-342">verwirft das Framework die Dienste nicht automatisch.</span><span class="sxs-lookup"><span data-stu-id="1717d-342">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="1717d-343">werden Dienste, die nicht explizit im Entwicklercode verworfen werden, weiter ausgeführt, bis die App beendet wird.</span><span class="sxs-lookup"><span data-stu-id="1717d-343">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="1717d-344">IDisposable-Anleitung für vorübergehende and freigegebene Instanzen</span><span class="sxs-lookup"><span data-stu-id="1717d-344">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="1717d-345">Vorübergehende, eingeschränkte Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="1717d-345">Transient, limited lifetime</span></span>

<span data-ttu-id="1717d-346">**Szenario**</span><span class="sxs-lookup"><span data-stu-id="1717d-346">**Scenario**</span></span>

<span data-ttu-id="1717d-347">Für die App ist eine <xref:System.IDisposable>-Instanz mit einer vorübergehenden Lebensdauer für eines der folgenden Szenarios erforderlich:</span><span class="sxs-lookup"><span data-stu-id="1717d-347">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="1717d-348">Die-Instanz wird im Stammbereich aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="1717d-348">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="1717d-349">Die Instanz sollte verworfen werden, bevor der Bereich endet.</span><span class="sxs-lookup"><span data-stu-id="1717d-349">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="1717d-350">**Lösung**</span><span class="sxs-lookup"><span data-stu-id="1717d-350">**Solution**</span></span>

<span data-ttu-id="1717d-351">Verwenden Sie das Factorymuster, um eine Instanz außerhalb des übergeordneten Bereichs zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="1717d-351">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="1717d-352">In dieser Situation verfügt die App in der Regel über eine `Create`-Methode, die den Konstruktor des endgültigen Typs direkt aufruft.</span><span class="sxs-lookup"><span data-stu-id="1717d-352">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="1717d-353">Wenn der endgültige Typ andere Abhängigkeiten aufweist, kann die Factory folgende Aktionen ausführen:</span><span class="sxs-lookup"><span data-stu-id="1717d-353">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="1717d-354">Sie kann <xref:System.IServiceProvider> im Konstruktor empfangen.</span><span class="sxs-lookup"><span data-stu-id="1717d-354">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="1717d-355">Sie kann <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> verwenden, um eine Instanz außerhalb des Containers zu instanziieren und dabei den Container für die Abhängigkeiten verwenden.</span><span class="sxs-lookup"><span data-stu-id="1717d-355">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="1717d-356">Freigegebene Instanz, eingeschränkte Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="1717d-356">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="1717d-357">**Szenario**</span><span class="sxs-lookup"><span data-stu-id="1717d-357">**Scenario**</span></span>

<span data-ttu-id="1717d-358">Für die App ist eine freigegebene <xref:System.IDisposable>-Instanz über mehrere Dienste erforderlich, die <xref:System.IDisposable> sollte jedoch eine begrenzte Lebensdauer haben.</span><span class="sxs-lookup"><span data-stu-id="1717d-358">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="1717d-359">**Lösung**</span><span class="sxs-lookup"><span data-stu-id="1717d-359">**Solution**</span></span>

<span data-ttu-id="1717d-360">Registrieren Sie die Instanz mit einer bereichsbezogenen Lebensdauer.</span><span class="sxs-lookup"><span data-stu-id="1717d-360">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="1717d-361">Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> für den Start und zum Erstellen einer neuen <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span><span class="sxs-lookup"><span data-stu-id="1717d-361">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="1717d-362">Verwenden Sie die <xref:System.IServiceProvider>-Schnittstelle des Bereichs, um die erforderlichen Dienste abzurufen.</span><span class="sxs-lookup"><span data-stu-id="1717d-362">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="1717d-363">Löschen Sie den Bereich, wenn die Lebensdauer beendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="1717d-363">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="1717d-364">Allgemeine Richtlinien</span><span class="sxs-lookup"><span data-stu-id="1717d-364">General Guidelines</span></span>

* <span data-ttu-id="1717d-365">Registrieren Sie keine <xref:System.IDisposable>-Instanzen mit einem vorübergehenden Gültigkeitsbereich.</span><span class="sxs-lookup"><span data-stu-id="1717d-365">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="1717d-366">Verwenden Sie stattdessen das Factorymuster.</span><span class="sxs-lookup"><span data-stu-id="1717d-366">Use the factory pattern instead.</span></span>
* <span data-ttu-id="1717d-367">Lösen Sie keine vorübergehenden oder bereichsbezogenen <xref:System.IDisposable>-Instanzen im Stammbereich auf.</span><span class="sxs-lookup"><span data-stu-id="1717d-367">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="1717d-368">Die einzige allgemeine Ausnahme gilt, wenn die App die <xref:System.IServiceProvider>-Instanz erstellt bzw. erneut erstellt und freigibt, was kein ideales Muster ist.</span><span class="sxs-lookup"><span data-stu-id="1717d-368">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="1717d-369">Wenn eine <xref:System.IDisposable>-Abhängigkeit über DI empfangen wird, ist es nicht erforderlich, dass der Empfänger <xref:System.IDisposable> selbst implementiert.</span><span class="sxs-lookup"><span data-stu-id="1717d-369">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="1717d-370">Der Empfänger der <xref:System.IDisposable>-Abhängigkeit darf <xref:System.IDisposable.Dispose%2A> auf dieser Abhängigkeit nicht abrufen.</span><span class="sxs-lookup"><span data-stu-id="1717d-370">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="1717d-371">Bereiche sollten verwendet werden, um die Lebensdauer von Diensten zu steuern.</span><span class="sxs-lookup"><span data-stu-id="1717d-371">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="1717d-372">Bereiche sind nicht hierarchisch, und es gibt keine besondere Verbindung zwischen den Bereichen.</span><span class="sxs-lookup"><span data-stu-id="1717d-372">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="1717d-373">Ersetzen von Standarddienstcontainern</span><span class="sxs-lookup"><span data-stu-id="1717d-373">Default service container replacement</span></span>

<span data-ttu-id="1717d-374">Der integrierte Dienstcontainer dient dazu, die Anforderungen des Frameworks und der meisten Consumer-Apps zu erfüllen.</span><span class="sxs-lookup"><span data-stu-id="1717d-374">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="1717d-375">Die Verwendung des integrierten Containers wird empfohlen, es sei denn, Sie benötigen ein bestimmtes Feature, das von diesem nicht unterstützt wird. Einige Beispiele:</span><span class="sxs-lookup"><span data-stu-id="1717d-375">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="1717d-376">Eigenschaftsinjektion</span><span class="sxs-lookup"><span data-stu-id="1717d-376">Property injection</span></span>
* <span data-ttu-id="1717d-377">Auf Namen basierende Injektion</span><span class="sxs-lookup"><span data-stu-id="1717d-377">Injection based on name</span></span>
* <span data-ttu-id="1717d-378">Untergeordnete Container</span><span class="sxs-lookup"><span data-stu-id="1717d-378">Child containers</span></span>
* <span data-ttu-id="1717d-379">Benutzerdefinierte Verwaltung der Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="1717d-379">Custom lifetime management</span></span>
* <span data-ttu-id="1717d-380">`Func<T>`-Unterstützung für die verzögerte Initialisierung</span><span class="sxs-lookup"><span data-stu-id="1717d-380">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="1717d-381">Konventionsbasierte Registrierung</span><span class="sxs-lookup"><span data-stu-id="1717d-381">Convention-based registration</span></span>

<span data-ttu-id="1717d-382">Die folgenden Container von Drittanbietern können mit ASP.NET Core-Apps verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="1717d-382">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="1717d-383">Autofac</span><span class="sxs-lookup"><span data-stu-id="1717d-383">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="1717d-384">DryIoc</span><span class="sxs-lookup"><span data-stu-id="1717d-384">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="1717d-385">Grace</span><span class="sxs-lookup"><span data-stu-id="1717d-385">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="1717d-386">LightInject</span><span class="sxs-lookup"><span data-stu-id="1717d-386">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="1717d-387">Lamar</span><span class="sxs-lookup"><span data-stu-id="1717d-387">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="1717d-388">Stashbox</span><span class="sxs-lookup"><span data-stu-id="1717d-388">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="1717d-389">Unity</span><span class="sxs-lookup"><span data-stu-id="1717d-389">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="1717d-390">Threadsicherheit</span><span class="sxs-lookup"><span data-stu-id="1717d-390">Thread safety</span></span>

<span data-ttu-id="1717d-391">Erstellen Sie threadsichere Singleton-Dienste.</span><span class="sxs-lookup"><span data-stu-id="1717d-391">Create thread-safe singleton services.</span></span> <span data-ttu-id="1717d-392">Wenn ein Singleton-Dienst eine Abhängigkeit von einem vorübergehenden Dienst aufweist, muss der vorübergehende Dienst abhängig von der Verwendungsweise durch das Singleton ebenfalls threadsicher sein.</span><span class="sxs-lookup"><span data-stu-id="1717d-392">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="1717d-393">Die Factorymethode des einzelnen Diensts, z. B. das zweite Argument für [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), muss nicht threadsicher sein.</span><span class="sxs-lookup"><span data-stu-id="1717d-393">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="1717d-394">Wie bei `static`-Konstruktoren erfolgt der Aufruf einmalig über einen einzelnen Thread.</span><span class="sxs-lookup"><span data-stu-id="1717d-394">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="1717d-395">Empfehlungen</span><span class="sxs-lookup"><span data-stu-id="1717d-395">Recommendations</span></span>

* <span data-ttu-id="1717d-396">`async/await`- und `Task`-basierte Dienstauflösung wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="1717d-396">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="1717d-397">C# unterstützt keine asynchronen Konstruktoren. Daher wird empfohlen, asynchrone Methoden zu verwenden, nachdem der Dienst synchron aufgelöst wurde.</span><span class="sxs-lookup"><span data-stu-id="1717d-397">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="1717d-398">Vermeiden Sie das Speichern von Daten und die direkte Konfiguration im Dienstcontainer.</span><span class="sxs-lookup"><span data-stu-id="1717d-398">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="1717d-399">Der Einkaufswagen eines Benutzers sollte z. B. normalerweise nicht dem Dienstcontainer hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="1717d-399">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="1717d-400">Bei der Konfiguration sollte das [Optionsmuster](xref:fundamentals/configuration/options) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="1717d-400">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="1717d-401">Gleichermaßen sollten Sie „Datencontainer“-Objekte vermeiden, die nur vorhanden sind, um den Zugriff auf einige andere Objekte zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="1717d-401">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="1717d-402">Das tatsächlich benötige Element sollte besser über Dependency Injection angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="1717d-402">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="1717d-403">Vermeiden Sie den statischen Zugriff auf Dienste (z. B. statische Eingabe von [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) zur Verwendung an anderer Stelle).</span><span class="sxs-lookup"><span data-stu-id="1717d-403">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="1717d-404">Vermeiden Sie die Verwendung von *Dienstlocator-Mustern*.</span><span class="sxs-lookup"><span data-stu-id="1717d-404">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="1717d-405">Rufen Sie beispielsweise nicht <xref:System.IServiceProvider.GetService*> auf, um eine Dienstinstanz zu erhalten, wenn Sie stattdessen Dependency Injection verwenden können:</span><span class="sxs-lookup"><span data-stu-id="1717d-405">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="1717d-406">**Falsch:**</span><span class="sxs-lookup"><span data-stu-id="1717d-406">**Incorrect:**</span></span>

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

  <span data-ttu-id="1717d-407">**Richtig**:</span><span class="sxs-lookup"><span data-stu-id="1717d-407">**Correct**:</span></span>

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

* <span data-ttu-id="1717d-408">Eine andere Dienstlocator-Variante, die Sie vermeiden sollten, ist die Injektion einer Factory, die zur Laufzeit Abhängigkeiten auflöst.</span><span class="sxs-lookup"><span data-stu-id="1717d-408">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="1717d-409">Beide Vorgehensweisen kombinieren Strategien zur [Umkehrung der Steuerung](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="1717d-409">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="1717d-410">Vermeiden Sie den statischen Zugriff auf `HttpContext` (z. B. [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="1717d-410">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="1717d-411">Wie bei allen Empfehlungen treffen Sie möglicherweise auf Situationen, in denen eine Empfehlung ignoriert werden muss.</span><span class="sxs-lookup"><span data-stu-id="1717d-411">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="1717d-412">Es gibt nur wenige Ausnahmen, die sich meistens auf besondere Fälle innerhalb des Frameworks beziehen.</span><span class="sxs-lookup"><span data-stu-id="1717d-412">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="1717d-413">Dependency Injection stellt eine *Alternative* zu statischen bzw. globalen Objektzugriffsmustern dar.</span><span class="sxs-lookup"><span data-stu-id="1717d-413">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="1717d-414">Sie werden keinen Nutzen aus der Dependency Injection ziehen können, wenn Sie diese mit dem Zugriff auf statische Objekte kombinieren.</span><span class="sxs-lookup"><span data-stu-id="1717d-414">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="1717d-415">Empfohlene Muster für Mehrinstanzenfähigkeit in der Dependency Injection (DI)</span><span class="sxs-lookup"><span data-stu-id="1717d-415">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="1717d-416">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) bietet die Mehrinstanzenfähigkeit.</span><span class="sxs-lookup"><span data-stu-id="1717d-416">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="1717d-417">Weitere Informationen finden Sie in der [Orchard Core-Dokumentation](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="1717d-417">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="1717d-418">Beispiele und Vorgehensweisen zum Erstellen modularer Apps und mehrinstanzenfähigen Apps nur mit dem Orchard Core-Framework aber ohne CMS-spezifische Features finden Sie in den Beispiel-Apps unter https://github.com/OrchardCMS/OrchardCore.Samples.</span><span class="sxs-lookup"><span data-stu-id="1717d-418">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1717d-419">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="1717d-419">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="1717d-420">Vier Möglichkeiten zum Verwerfen von IDisposables in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1717d-420">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="1717d-421">Schreiben von sauberem Code in ASP.NET Core über Dependency Injection (MSDN)</span><span class="sxs-lookup"><span data-stu-id="1717d-421">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="1717d-422">Prinzip der expliziten Abhängigkeiten</span><span class="sxs-lookup"><span data-stu-id="1717d-422">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* <span data-ttu-id="1717d-423">[Umkehrung von Steuerungscontainern und das Abhängigkeitsinjektionsmuster (Martin Fowler)](https://www.martinfowler.com/articles/injection.html) (in englischer Sprache)</span><span class="sxs-lookup"><span data-stu-id="1717d-423">[Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)](https://www.martinfowler.com/articles/injection.html)</span></span>
* [<span data-ttu-id="1717d-424">Registrieren eines Diensts mit mehreren Schnittstellen in ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="1717d-424">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1717d-425">ASP.NET Core unterstützt das Softwareentwurfsmuster Abhängigkeitsinjektion. Damit kann eine [Umkehrung der Steuerung](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) (Inversion of Control, IoC) zwischen Klassen und ihren Abhängigkeiten erreicht werden.</span><span class="sxs-lookup"><span data-stu-id="1717d-425">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="1717d-426">Weitere Informationen zur Abhängigkeitsinjektion innerhalb von MVC-Controllern finden Sie unter <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="1717d-426">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="1717d-427">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1717d-427">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="1717d-428">Übersicht über Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="1717d-428">Overview of dependency injection</span></span>

<span data-ttu-id="1717d-429">Eine *Abhängigkeit* ist ein beliebiges Objekt, das ein anderes Objekt benötigt.</span><span class="sxs-lookup"><span data-stu-id="1717d-429">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="1717d-430">Überprüfen Sie die folgende `MyDependency`-Klasse mit einer `WriteMessage`-Methode, von der andere Klassen in einer App abhängen:</span><span class="sxs-lookup"><span data-stu-id="1717d-430">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="1717d-431">Eine Instanz der Klasse `MyDependency` kann erstellt werden, um die `WriteMessage`-Methode einer Klasse zur Verfügung zu stellen.</span><span class="sxs-lookup"><span data-stu-id="1717d-431">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="1717d-432">Die Klasse `MyDependency` ist eine Abhängigkeit der Klasse `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="1717d-432">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="1717d-433">Die Klasse erstellt die Instanz `MyDependency` und hängt direkt von dieser ab.</span><span class="sxs-lookup"><span data-stu-id="1717d-433">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="1717d-434">Codeabhängigkeiten (wie im vorherigen Beispiel) sind problematisch und sollten aus folgenden Gründen vermieden werden:</span><span class="sxs-lookup"><span data-stu-id="1717d-434">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="1717d-435">Um `MyDependency` durch eine andere Implementierung zu ersetzen, muss die Klasse geändert werden.</span><span class="sxs-lookup"><span data-stu-id="1717d-435">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="1717d-436">Wenn `MyDependency` über Abhängigkeiten verfügt, müssen diese von der Klasse konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="1717d-436">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="1717d-437">In einem großen Projekt mit mehreren Klassen, die von `MyDependency` abhängig sind, wird der Konfigurationscode über die App verteilt.</span><span class="sxs-lookup"><span data-stu-id="1717d-437">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="1717d-438">Diese Implementierung ist nicht für Komponententests geeignet.</span><span class="sxs-lookup"><span data-stu-id="1717d-438">This implementation is difficult to unit test.</span></span> <span data-ttu-id="1717d-439">Die App sollte eine `MyDependency`-Modell- oder Stubklasse verwenden, was mit diesem Ansatz nicht möglich ist.</span><span class="sxs-lookup"><span data-stu-id="1717d-439">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="1717d-440">Die Abhängigkeitsinjektion löst dieses Problem mithilfe der folgenden Schritte:</span><span class="sxs-lookup"><span data-stu-id="1717d-440">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="1717d-441">Die Verwendung einer Schnittstelle oder Basisklasse zur Abstraktion der Abhängigkeitsimplementierung.</span><span class="sxs-lookup"><span data-stu-id="1717d-441">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="1717d-442">Registrierung der Abhängigkeit in einem Dienstcontainer.</span><span class="sxs-lookup"><span data-stu-id="1717d-442">Registration of the dependency in a service container.</span></span> <span data-ttu-id="1717d-443">ASP.NET Core stellt einen integrierten Dienstcontainer (<xref:System.IServiceProvider>) bereit.</span><span class="sxs-lookup"><span data-stu-id="1717d-443">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="1717d-444">Die Dienste werden in der App-Methode `Startup.ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="1717d-444">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="1717d-445">Die *Injektion* des Diensts in den Konstruktor der Klasse, wo er verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="1717d-445">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="1717d-446">Das Framework erstellt eine Instanz der Abhängigkeit und entfernt diese, wenn sie nicht mehr benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="1717d-446">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="1717d-447">In der [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) definiert die `IMyDependency`-Schnittstelle eine Methode, die der Dienst für die App bereitstellt:</span><span class="sxs-lookup"><span data-stu-id="1717d-447">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="1717d-448">Diese Schnittstelle wird durch einen konkreten Typ (`MyDependency`) implementiert:</span><span class="sxs-lookup"><span data-stu-id="1717d-448">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="1717d-449">`MyDependency` fordert einen <xref:Microsoft.Extensions.Logging.ILogger`1> beim zugehörigen Konstruktor an.</span><span class="sxs-lookup"><span data-stu-id="1717d-449">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="1717d-450">Die Abhängigkeitsinjektion wird häufig als Verkettung verwendet.</span><span class="sxs-lookup"><span data-stu-id="1717d-450">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="1717d-451">Jede angeforderte Abhängigkeit fordert wiederum ihre eigenen Abhängigkeiten an.</span><span class="sxs-lookup"><span data-stu-id="1717d-451">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="1717d-452">Der Container löst die Abhängigkeiten im Diagramm auf und gibt den vollständig aufgelösten Dienst zurück.</span><span class="sxs-lookup"><span data-stu-id="1717d-452">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="1717d-453">Die gesammelten aufzulösenden Abhängigkeiten werden als *Abhängigkeitsstruktur*, *Abhängigkeitsdiagramm* oder *Objektdiagramm* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="1717d-453">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="1717d-454">`IMyDependency` und `ILogger<TCategoryName>` müssen im Dienstcontainer registriert werden.</span><span class="sxs-lookup"><span data-stu-id="1717d-454">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="1717d-455">`IMyDependency` ist in `Startup.ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="1717d-455">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="1717d-456">`ILogger<TCategoryName>` wird von der Protokollierungsabstraktionsinfrastruktur registriert. Es handelt sich also um einen [von einem Framework bereitgestellten Dienst](#framework-provided-services), der standardmäßig vom Framework registriert wird.</span><span class="sxs-lookup"><span data-stu-id="1717d-456">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="1717d-457">Der Container löst `ILogger<TCategoryName>` unter Verwendung der [(generischen) offenen Typen](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types) auf, wodurch die Notwendigkeit entfällt, jeden [(generischen) konstruierten Typ](/dotnet/csharp/language-reference/language-specification/types#constructed-types) zu registrieren:</span><span class="sxs-lookup"><span data-stu-id="1717d-457">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="1717d-458">In der Beispiel-App ist der Dienst `IMyDependency` mit dem konkreten Typ `MyDependency` registriert.</span><span class="sxs-lookup"><span data-stu-id="1717d-458">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="1717d-459">Die Registrierung schränkt die Lebensdauer des Diensts auf die Lebensdauer einer einzelnen Anforderung ein.</span><span class="sxs-lookup"><span data-stu-id="1717d-459">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="1717d-460">Auf die [Dienstlebensdauer](#service-lifetimes) wird später in diesem Artikel eingegangen.</span><span class="sxs-lookup"><span data-stu-id="1717d-460">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="1717d-461">Jede Erweiterungsmethode vom Typ `services.Add{SERVICE_NAME}` fügt Dienste hinzu (und konfiguriert diese möglicherweise).</span><span class="sxs-lookup"><span data-stu-id="1717d-461">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="1717d-462">So fügt beispielsweise `services.AddMvc()` die erforderlichen Dienste Razor Pages und MVC hinzu.</span><span class="sxs-lookup"><span data-stu-id="1717d-462">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="1717d-463">Es wird empfohlen, dass Apps dieser Konvention folgen.</span><span class="sxs-lookup"><span data-stu-id="1717d-463">We recommended that apps follow this convention.</span></span> <span data-ttu-id="1717d-464">Platzieren Sie Erweiterungsmethoden im Namespace [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection), um Gruppen von Dienstregistrierungen zu kapseln.</span><span class="sxs-lookup"><span data-stu-id="1717d-464">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="1717d-465">Wenn für den Dienstkonstruktor ein [integrierter Typ](/dotnet/csharp/language-reference/keywords/built-in-types-table) erforderlich ist, z. B. `string`, kann dieser Typ über [Konfiguration](xref:fundamentals/configuration/index) oder das [Optionsmuster](xref:fundamentals/configuration/options) eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="1717d-465">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="1717d-466">Eine Instanz des Diensts wird über den Konstruktor einer Klasse angefordert, in der der Dienst verwendet wird, und einem privaten Feld zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="1717d-466">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="1717d-467">Das Feld wird verwendet, um auf den Dienst bei Bedarf über die gesamte Klasse zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="1717d-467">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="1717d-468">In der Beispiel-App wird die Instanz `IMyDependency` angefordert, und mit ihr wird die App-Methode `WriteMessage` aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="1717d-468">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="1717d-469">In den Start eingefügte Dienste</span><span class="sxs-lookup"><span data-stu-id="1717d-469">Services injected into Startup</span></span>

<span data-ttu-id="1717d-470">Bei Verwendung des generischen Hosts (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) können nur die folgenden Diensttypen in den `Startup`-Konstruktor eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="1717d-470">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="1717d-471">Dienste können in `Startup.Configure` eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="1717d-471">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="1717d-472">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="1717d-472">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="1717d-473">Von Frameworks bereitgestellte Dienste</span><span class="sxs-lookup"><span data-stu-id="1717d-473">Framework-provided services</span></span>

<span data-ttu-id="1717d-474">Die Methode `Startup.ConfigureServices` definiert die von der App verwendeten Dienste. Dies umfasst auch Plattformfeatures wie Entity Framework Core und ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="1717d-474">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="1717d-475">Zunächst enthält die in `ConfigureServices` bereitgestellte `IServiceCollection` die vom Framework definierten Dienste, abhängig davon, wie der [Host konfiguriert](xref:fundamentals/index#host) wurde.</span><span class="sxs-lookup"><span data-stu-id="1717d-475">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="1717d-476">Es ist nicht ungewöhnlich, dass das Framework Hunderte von Diensten für eine auf einer ASP.NET Core-Vorlage basierende App registriert.</span><span class="sxs-lookup"><span data-stu-id="1717d-476">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="1717d-477">In der folgenden Tabelle finden Sie eine kleine Auswahl der Dienste, die vom Framework registriert werden können.</span><span class="sxs-lookup"><span data-stu-id="1717d-477">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="1717d-478">Diensttyp</span><span class="sxs-lookup"><span data-stu-id="1717d-478">Service Type</span></span> | <span data-ttu-id="1717d-479">Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="1717d-479">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="1717d-480">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="1717d-480">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="1717d-481">Singleton</span><span class="sxs-lookup"><span data-stu-id="1717d-481">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="1717d-482">Singleton</span><span class="sxs-lookup"><span data-stu-id="1717d-482">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="1717d-483">Singleton</span><span class="sxs-lookup"><span data-stu-id="1717d-483">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="1717d-484">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="1717d-484">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="1717d-485">Singleton</span><span class="sxs-lookup"><span data-stu-id="1717d-485">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="1717d-486">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="1717d-486">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="1717d-487">Singleton</span><span class="sxs-lookup"><span data-stu-id="1717d-487">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="1717d-488">Singleton</span><span class="sxs-lookup"><span data-stu-id="1717d-488">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="1717d-489">Singleton</span><span class="sxs-lookup"><span data-stu-id="1717d-489">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="1717d-490">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="1717d-490">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="1717d-491">Singleton</span><span class="sxs-lookup"><span data-stu-id="1717d-491">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="1717d-492">Singleton</span><span class="sxs-lookup"><span data-stu-id="1717d-492">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="1717d-493">Singleton</span><span class="sxs-lookup"><span data-stu-id="1717d-493">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="1717d-494">Registrieren weiterer Dienste mit Erweiterungsmethoden</span><span class="sxs-lookup"><span data-stu-id="1717d-494">Register additional services with extension methods</span></span>

<span data-ttu-id="1717d-495">Wenn eine Dienstsammlungs-Erweiterungsmethode verfügbar ist, um einen Dienst (und ggf. seine abhängigen Dienste) zu registrieren, ist die Konvention, eine einzige `Add{SERVICE_NAME}`-Erweiterungsmethode zu verwenden, um alle von diesem Dienst benötigten Dienste zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="1717d-495">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="1717d-496">Der folgende Code ist ein Beispiel dafür, wie mit den Erweiterungsmethoden [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) und <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> zusätzliche Dienste zum Container hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="1717d-496">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="1717d-497">Weitere Informationen finden Sie in der <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection>-Klasse in der API-Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="1717d-497">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="1717d-498">Dienstlebensdauer</span><span class="sxs-lookup"><span data-stu-id="1717d-498">Service lifetimes</span></span>

<span data-ttu-id="1717d-499">Wählen Sie eine geeignete Lebensdauer für jeden registrierten Dienst aus.</span><span class="sxs-lookup"><span data-stu-id="1717d-499">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="1717d-500">ASP.NET Core-Dienste können mit folgender Lebensdauer konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="1717d-500">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="1717d-501">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="1717d-501">Transient</span></span>

<span data-ttu-id="1717d-502">Kurzlebige Dienste (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) werden bei jeder Anforderung aus dem Dienstcontainer neu erstellt.</span><span class="sxs-lookup"><span data-stu-id="1717d-502">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="1717d-503">Diese Lebensdauer ist am besten für einfache, zustandslose Dienste geeignet.</span><span class="sxs-lookup"><span data-stu-id="1717d-503">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="1717d-504">In Apps, die Anforderungen verarbeiten, werden vorübergehende Dienste am Ende der Anforderung verworfen.</span><span class="sxs-lookup"><span data-stu-id="1717d-504">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="1717d-505">Bereichsbezogen</span><span class="sxs-lookup"><span data-stu-id="1717d-505">Scoped</span></span>

<span data-ttu-id="1717d-506">Dienste mit bereichsbezogener Lebensdauer (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) werden einmal pro Clientanforderung (-verbindung) erstellt.</span><span class="sxs-lookup"><span data-stu-id="1717d-506">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="1717d-507">In Apps, die Anforderungen verarbeiten, werden bereichsbezogene Dienste am Ende der Anforderung verworfen.</span><span class="sxs-lookup"><span data-stu-id="1717d-507">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="1717d-508">Wenn Sie einen bereichsbezogenen Dienst in einer Middleware verwenden, müssen Sie den Dienst in die `Invoke`- oder `InvokeAsync`-Methode einfügen.</span><span class="sxs-lookup"><span data-stu-id="1717d-508">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="1717d-509">Fügen Sie ihn nicht über [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection) ein, da hierdurch der Dienst ein Verhalten wie ein Singleton zeigt.</span><span class="sxs-lookup"><span data-stu-id="1717d-509">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="1717d-510">Weitere Informationen finden Sie unter <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="1717d-510">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="1717d-511">Singleton</span><span class="sxs-lookup"><span data-stu-id="1717d-511">Singleton</span></span>

<span data-ttu-id="1717d-512">Dienste mit Singleton-Lebensdauer (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) werden bei der ersten Anforderung erstellt (bzw. wenn `Startup.ConfigureServices` ausgeführt wird, und eine Instanz bei der Dienstregistrierung angegeben wird).</span><span class="sxs-lookup"><span data-stu-id="1717d-512">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="1717d-513">Jeder nachfolgenden Anforderung verwendet die gleiche Instanz.</span><span class="sxs-lookup"><span data-stu-id="1717d-513">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="1717d-514">Wenn die App ein Verhalten vom Typ „Singleton“ erfordert, wird empfohlen, den Dienstcontainer die Dienstlebensdauer verwalten zu lassen.</span><span class="sxs-lookup"><span data-stu-id="1717d-514">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="1717d-515">Implementieren Sie nicht das Designmuster „Singleton“ und stellen Sie Benutzercode bereit, um die Objektlebensdauer in der Klasse zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="1717d-515">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="1717d-516">In Apps, die Anforderungen verarbeiten, werden Singleton-Dienste verworfen, wenn der <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> beim Herunterfahren der App verworfen wird.</span><span class="sxs-lookup"><span data-stu-id="1717d-516">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="1717d-517">Es ist riskant, einen bereichsbezogenen Dienst über ein Singleton aufzulösen.</span><span class="sxs-lookup"><span data-stu-id="1717d-517">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="1717d-518">Möglicherweise weist der Dienst bei der Verarbeitung nachfolgender Anforderungen einen falschen Status auf.</span><span class="sxs-lookup"><span data-stu-id="1717d-518">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="1717d-519">Dienstregistrierungsmethoden</span><span class="sxs-lookup"><span data-stu-id="1717d-519">Service registration methods</span></span>

<span data-ttu-id="1717d-520">Methoden zur Erweiterung der Dienstregistrierung bieten Überladungen, die in bestimmten Szenarios hilfreich sind.</span><span class="sxs-lookup"><span data-stu-id="1717d-520">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="1717d-521">Methode</span><span class="sxs-lookup"><span data-stu-id="1717d-521">Method</span></span> | <span data-ttu-id="1717d-522">Automatische</span><span class="sxs-lookup"><span data-stu-id="1717d-522">Automatic</span></span><br><span data-ttu-id="1717d-523">Objekt</span><span class="sxs-lookup"><span data-stu-id="1717d-523">object</span></span><br><span data-ttu-id="1717d-524">bereinigung</span><span class="sxs-lookup"><span data-stu-id="1717d-524">disposal</span></span> | <span data-ttu-id="1717d-525">Mehrere</span><span class="sxs-lookup"><span data-stu-id="1717d-525">Multiple</span></span><br><span data-ttu-id="1717d-526">Implementierungen</span><span class="sxs-lookup"><span data-stu-id="1717d-526">implementations</span></span> | <span data-ttu-id="1717d-527">Argumentübergabe</span><span class="sxs-lookup"><span data-stu-id="1717d-527">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="1717d-528">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="1717d-528">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="1717d-529">Ja</span><span class="sxs-lookup"><span data-stu-id="1717d-529">Yes</span></span> | <span data-ttu-id="1717d-530">Ja</span><span class="sxs-lookup"><span data-stu-id="1717d-530">Yes</span></span> | <span data-ttu-id="1717d-531">Nein</span><span class="sxs-lookup"><span data-stu-id="1717d-531">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="1717d-532">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="1717d-532">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="1717d-533">Ja</span><span class="sxs-lookup"><span data-stu-id="1717d-533">Yes</span></span> | <span data-ttu-id="1717d-534">Ja</span><span class="sxs-lookup"><span data-stu-id="1717d-534">Yes</span></span> | <span data-ttu-id="1717d-535">Ja</span><span class="sxs-lookup"><span data-stu-id="1717d-535">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="1717d-536">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="1717d-536">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="1717d-537">Ja</span><span class="sxs-lookup"><span data-stu-id="1717d-537">Yes</span></span> | <span data-ttu-id="1717d-538">Nein</span><span class="sxs-lookup"><span data-stu-id="1717d-538">No</span></span> | <span data-ttu-id="1717d-539">Nein</span><span class="sxs-lookup"><span data-stu-id="1717d-539">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="1717d-540">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="1717d-540">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="1717d-541">Nein</span><span class="sxs-lookup"><span data-stu-id="1717d-541">No</span></span> | <span data-ttu-id="1717d-542">Ja</span><span class="sxs-lookup"><span data-stu-id="1717d-542">Yes</span></span> | <span data-ttu-id="1717d-543">Ja</span><span class="sxs-lookup"><span data-stu-id="1717d-543">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="1717d-544">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="1717d-544">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="1717d-545">Nein</span><span class="sxs-lookup"><span data-stu-id="1717d-545">No</span></span> | <span data-ttu-id="1717d-546">Nein</span><span class="sxs-lookup"><span data-stu-id="1717d-546">No</span></span> | <span data-ttu-id="1717d-547">Ja</span><span class="sxs-lookup"><span data-stu-id="1717d-547">Yes</span></span> |

<span data-ttu-id="1717d-548">Weitere Informationen zum Löschen von Typen finden Sie im Abschnitt [Löschen von Diensten](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="1717d-548">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="1717d-549">Ein häufiges Szenario für mehrere Implementierungen ist [Typen zu Testzwecken simulieren](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="1717d-549">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="1717d-550">`TryAdd{LIFETIME}`-Methoden registrieren den Dienst nur, wenn noch keine Implementierung registriert ist.</span><span class="sxs-lookup"><span data-stu-id="1717d-550">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="1717d-551">Im folgenden Beispiel registriert die erste Zeile `MyDependency` für `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="1717d-551">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="1717d-552">Die zweite Zeile hat keine Auswirkungen, da es für `IMyDependency` bereits eine registrierte Implementierung gibt:</span><span class="sxs-lookup"><span data-stu-id="1717d-552">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="1717d-553">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="1717d-553">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="1717d-554">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*)-Methoden registrieren den Dienst nur, wenn es nicht bereits eine Implementierung *des gleichen Typs* gibt.</span><span class="sxs-lookup"><span data-stu-id="1717d-554">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="1717d-555">Mehrere Dienste werden über `IEnumerable<{SERVICE}>` aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="1717d-555">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="1717d-556">Beim Registrieren von Diensten möchte der Entwickler nur eine Instanz hinzufügen, wenn nicht bereits eine Instanz vom gleichen Typ hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="1717d-556">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="1717d-557">Diese Methode wird in der Regel von Bibliotheksautoren verwendet, um zu vermeiden, dass zwei Kopien einer Instanz im Container registriert werden.</span><span class="sxs-lookup"><span data-stu-id="1717d-557">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="1717d-558">Im folgenden Beispiel registriert die erste Zeile `MyDep` für `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="1717d-558">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="1717d-559">Die zweite Zeile registriert `MyDep` für `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="1717d-559">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="1717d-560">Die dritte Zeile hat keine Auswirkungen, da es für `IMyDep1` bereits eine registrierte Implementierung von `MyDep` gibt:</span><span class="sxs-lookup"><span data-stu-id="1717d-560">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="1717d-561">Verhalten von Constructor Injection</span><span class="sxs-lookup"><span data-stu-id="1717d-561">Constructor injection behavior</span></span>

<span data-ttu-id="1717d-562">Dienste können durch zwei Mechanismen aufgelöst werden:</span><span class="sxs-lookup"><span data-stu-id="1717d-562">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="1717d-563"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Lässt die Erstellung von Objekten ohne Dienstregistrierung im Dependency-Injection-Container zu</span><span class="sxs-lookup"><span data-stu-id="1717d-563"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="1717d-564">`ActivatorUtilities` wird mit Abstraktionen für Benutzer verwendet. Dazu zählen Taghilfsprogramme, MVC-Controller und Modellbindungen.</span><span class="sxs-lookup"><span data-stu-id="1717d-564">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="1717d-565">Konstruktoren können Argumente akzeptieren, die nicht durch Abhängigkeitsinjektion bereitgestellt werden. Die Argumente müssen jedoch Standardwerte zuweisen.</span><span class="sxs-lookup"><span data-stu-id="1717d-565">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="1717d-566">Wenn Dienste durch `IServiceProvider` oder `ActivatorUtilities` aufgelöst werden, benötigt die [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection) einen *öffentlichen* Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="1717d-566">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="1717d-567">Wenn Dienste durch `ActivatorUtilities` aufgelöst werden, erfordert die [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection), dass nur ein anwendbarer Konstruktor vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="1717d-567">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="1717d-568">Konstruktorüberladungen werden unterstützt. Es darf jedoch nur eine Überladung vorhanden sein, deren Argumente alle durch Dependency Injection erfüllt werden können.</span><span class="sxs-lookup"><span data-stu-id="1717d-568">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="1717d-569">Entity Framework-Kontexte</span><span class="sxs-lookup"><span data-stu-id="1717d-569">Entity Framework contexts</span></span>

<span data-ttu-id="1717d-570">Entity Framework-Kontexte werden einem Dienstcontainer normalerweise mithilfe der [bereichsbezogenen Lebensdauer](#service-lifetimes) hinzugefügt, da Datenbankvorgänge von Web-Apps normalerweise auf den Clientanforderungsbereich bezogen werden.</span><span class="sxs-lookup"><span data-stu-id="1717d-570">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="1717d-571">Der Bereich einer Standardlebensdauer wird festgelegt, wenn eine Lebensdauer nicht von einer [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)-Überladung angegeben wird, wenn der Datenbankkontext registriert wird.</span><span class="sxs-lookup"><span data-stu-id="1717d-571">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="1717d-572">Dienste einer festgelegten Lebensdauer sollten keinen Datenbankkontext mit einer kürzeren Lebensdauer als die des Dienstes verwenden.</span><span class="sxs-lookup"><span data-stu-id="1717d-572">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="1717d-573">Lebensdauer und Registrierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="1717d-573">Lifetime and registration options</span></span>

<span data-ttu-id="1717d-574">Um den Unterschied zwischen der Lebensdauer und den Registrierungsoptionen zu demonstrieren, betrachten Sie die folgenden Schnittstellen, die Aufgaben als Vorgänge mit einem eindeutigen Bezeichner (`OperationId`) darstellen.</span><span class="sxs-lookup"><span data-stu-id="1717d-574">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="1717d-575">Je nachdem, wie die Dienstlebensdauer für die folgenden Schnittstellen konfiguriert ist, stellt der Container auf Anforderung einer Klasse entweder die gleiche oder eine andere Instanz des Diensts zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="1717d-575">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="1717d-576">Die Schnittstellen sind in die Klasse `Operation` implementiert.</span><span class="sxs-lookup"><span data-stu-id="1717d-576">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="1717d-577">Der `Operation`-Konstruktor generiert eine GUID, wenn noch keine angegeben ist:</span><span class="sxs-lookup"><span data-stu-id="1717d-577">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="1717d-578">`OperationService` ist registriert und hängt von jedem anderen `Operation`-Typ ab.</span><span class="sxs-lookup"><span data-stu-id="1717d-578">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="1717d-579">Wenn `OperationService` über die Abhängigkeitsinjektion angefordert wird, wird entweder eine neue Instanz jedes Diensts oder eine vorhandene Instanz basierend auf der Lebensdauer des abhängigen Diensts zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="1717d-579">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="1717d-580">Wenn vorübergehende Dienste bei der Anforderung aus dem Container erstellt werden, ist die `OperationId` von Dienst `IOperationTransient` anders als die `OperationId` von `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="1717d-580">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="1717d-581">`OperationService` erhält eine neue Instanz der Klasse `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="1717d-581">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="1717d-582">Der `OperationId`-Wert der neuen Instanz ist anders.</span><span class="sxs-lookup"><span data-stu-id="1717d-582">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="1717d-583">Wenn bereichsbezogene Dienste pro Clientanforderung erstellt werden, ist die `OperationId` in Dienst `IOperationScoped` und `OperationService` identisch innerhalb einer Clientanforderung.</span><span class="sxs-lookup"><span data-stu-id="1717d-583">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="1717d-584">Clientanforderungsübergreifend haben die beiden Dienste jedoch einen anderen gemeinsamen `OperationId`-Wert.</span><span class="sxs-lookup"><span data-stu-id="1717d-584">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="1717d-585">Wenn Singleton- und Singletoninstanzdienste einmal erstellt und für alle Clientanforderungen und alle Dienste verwendet werden, ist `OperationId` für alle Dienstanforderungen identisch.</span><span class="sxs-lookup"><span data-stu-id="1717d-585">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="1717d-586">In `Startup.ConfigureServices` wird jeder Typ entsprechend seiner benannten Lebensdauer dem Container hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="1717d-586">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="1717d-587">Der Dienst `IOperationSingletonInstance` verwendet eine bestimmte Instanz mit einer bekannten ID von `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="1717d-587">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="1717d-588">Die Verwendung dieses Typs ist eindeutig (die GUID besteht ausschließlich aus Nullen (0)).</span><span class="sxs-lookup"><span data-stu-id="1717d-588">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="1717d-589">Die Beispiel-App zeigt die Objektlebensdauer innerhalb einer Anforderung und zwischen einzelnen Anforderungen an.</span><span class="sxs-lookup"><span data-stu-id="1717d-589">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="1717d-590">Ihre Klasse `IndexModel` fordert jeden `IOperation`- und `OperationService`-Typ an.</span><span class="sxs-lookup"><span data-stu-id="1717d-590">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="1717d-591">Die Seite zeigt dann alle `OperationId`-Werte der Seitenmodellklasse und des Diensts über Eigenschaftenzuweisungen an:</span><span class="sxs-lookup"><span data-stu-id="1717d-591">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="1717d-592">Zwei folgende Ausgaben zeigen die Ergebnisse von zwei Anforderungen:</span><span class="sxs-lookup"><span data-stu-id="1717d-592">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="1717d-593">**Erste Anforderung:**</span><span class="sxs-lookup"><span data-stu-id="1717d-593">**First request:**</span></span>

<span data-ttu-id="1717d-594">Controllervorgänge:</span><span class="sxs-lookup"><span data-stu-id="1717d-594">Controller operations:</span></span>

<span data-ttu-id="1717d-595">Vorübergehend: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="1717d-595">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="1717d-596">Bereichsbezogen: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="1717d-596">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="1717d-597">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="1717d-597">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="1717d-598">Instanz: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="1717d-598">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="1717d-599">`OperationService`-Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="1717d-599">`OperationService` operations:</span></span>

<span data-ttu-id="1717d-600">Vorübergehend: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="1717d-600">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="1717d-601">Bereichsbezogen: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="1717d-601">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="1717d-602">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="1717d-602">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="1717d-603">Instanz: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="1717d-603">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="1717d-604">**Zweite Anforderung:**</span><span class="sxs-lookup"><span data-stu-id="1717d-604">**Second request:**</span></span>

<span data-ttu-id="1717d-605">Controllervorgänge:</span><span class="sxs-lookup"><span data-stu-id="1717d-605">Controller operations:</span></span>

<span data-ttu-id="1717d-606">Vorübergehend: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="1717d-606">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="1717d-607">Bereichsbezogen: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="1717d-607">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="1717d-608">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="1717d-608">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="1717d-609">Instanz: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="1717d-609">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="1717d-610">`OperationService`-Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="1717d-610">`OperationService` operations:</span></span>

<span data-ttu-id="1717d-611">Vorübergehend: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="1717d-611">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="1717d-612">Bereichsbezogen: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="1717d-612">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="1717d-613">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="1717d-613">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="1717d-614">Instanz: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="1717d-614">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="1717d-615">Beachten Sie, welche der `OperationId`-Werte innerhalb einer Anforderung und zwischen Anforderungen variieren:</span><span class="sxs-lookup"><span data-stu-id="1717d-615">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="1717d-616">Objekte vom Typ *Vorübergehend* sind immer unterschiedlich.</span><span class="sxs-lookup"><span data-stu-id="1717d-616">*Transient* objects are always different.</span></span> <span data-ttu-id="1717d-617">Der vorübergehende `OperationId`-Wert für die ersten und zweiten Clientanforderungen ist für beide `OperationService`-Vorgänge und clientanforderungsübergreifend unterschiedlich.</span><span class="sxs-lookup"><span data-stu-id="1717d-617">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="1717d-618">Eine neue Instanz wird für jede Dienstanforderung und jede Clientanforderung bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="1717d-618">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="1717d-619">*Bereichsbezogene* Objekte sind innerhalb einer Clientanforderung identisch, clientanforderungsübergreifend sind sie jedoch unterschiedlich.</span><span class="sxs-lookup"><span data-stu-id="1717d-619">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="1717d-620">Objekte vom Typ *Singleton* sind bei jedem Objekt und in jeder Anforderung identisch (unabhängig davon, ob eine `Operation`-Instanz in `Startup.ConfigureServices` bereitgestellt wird).</span><span class="sxs-lookup"><span data-stu-id="1717d-620">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="1717d-621">Abrufen von Diensten aus „Main“</span><span class="sxs-lookup"><span data-stu-id="1717d-621">Call services from main</span></span>

<span data-ttu-id="1717d-622">Erstellen Sie <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> mit [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*), um einen bereichsbezogenen Dienst innerhalb des Anwendungsbereichs aufzulösen.</span><span class="sxs-lookup"><span data-stu-id="1717d-622">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="1717d-623">Dieser Ansatz eignet sich gut dafür, beim Start auf einen bereichsbezogenen Dienst zuzugreifen und Initialisierungsaufgaben auszuführen.</span><span class="sxs-lookup"><span data-stu-id="1717d-623">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="1717d-624">Das folgende Beispiel veranschaulicht, wie man einen Kontext für `MyScopedService` in `Program.Main` erhält:</span><span class="sxs-lookup"><span data-stu-id="1717d-624">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="1717d-625">Bereichsvalidierung</span><span class="sxs-lookup"><span data-stu-id="1717d-625">Scope validation</span></span>

<span data-ttu-id="1717d-626">Wenn die App in der Entwicklungsumgebung ausgeführt wird, überprüft der Standarddienstanbieter, ob:</span><span class="sxs-lookup"><span data-stu-id="1717d-626">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="1717d-627">Bereichsbezogene Dienste nicht direkt oder indirekt vom Stammdienstanbieter aufgelöst werden</span><span class="sxs-lookup"><span data-stu-id="1717d-627">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="1717d-628">Bereichsbezogene Dienste nicht direkt oder indirekt in Singletons eingefügt werden</span><span class="sxs-lookup"><span data-stu-id="1717d-628">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="1717d-629">Der Stammdienstanbieter wird erstellt, wenn <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="1717d-629">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="1717d-630">Die Lebensdauer des Stammdienstanbieters entspricht der Lebensdauer der App/des Servers, wenn der Anbieter mit der App erstellt wird und verworfen wird, wenn die App beendet wird.</span><span class="sxs-lookup"><span data-stu-id="1717d-630">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="1717d-631">Bereichsbezogene Dienste werden von dem Container verworfen, der sie erstellt hat.</span><span class="sxs-lookup"><span data-stu-id="1717d-631">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="1717d-632">Wenn ein bereichsbezogener Dienst im Stammcontainer erstellt wird, wird die Lebensdauer effektiv auf Singleton heraufgestuft, da er nur vom Stammcontainer verworfen wird, wenn die App/der Server heruntergefahren wird.</span><span class="sxs-lookup"><span data-stu-id="1717d-632">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="1717d-633">Die Überprüfung bereichsbezogener Dienste erfasst diese Situationen, wenn `BuildServiceProvider` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="1717d-633">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="1717d-634">Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="1717d-634">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="1717d-635">Anfordern von Diensten</span><span class="sxs-lookup"><span data-stu-id="1717d-635">Request Services</span></span>

<span data-ttu-id="1717d-636">Die Dienste, die innerhalb einer ASP.NET Core-Anforderung von `HttpContext` verfügbar sind, werden über die [HttpContext.RequestService](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices)-Sammlung verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="1717d-636">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="1717d-637">Anforderungsdienste stellen die Dienste dar, die als Teil der App konfiguriert und angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="1717d-637">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="1717d-638">Wenn Objekte Abhängigkeiten angeben, werden diese von den in `RequestServices` gefundenen Typen erfüllt (nicht in `ApplicationServices`).</span><span class="sxs-lookup"><span data-stu-id="1717d-638">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="1717d-639">Generell sollte die App diese Eigenschaften nicht direkt verwenden.</span><span class="sxs-lookup"><span data-stu-id="1717d-639">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="1717d-640">Fordern Sie stattdessen die von Klassen benötigten Typen über Klassenkonstruktoren an, und lassen Sie das Framework die Abhängigkeiten einfügen.</span><span class="sxs-lookup"><span data-stu-id="1717d-640">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="1717d-641">So erhalten Sie Klassen, die einfacher getestet werden können.</span><span class="sxs-lookup"><span data-stu-id="1717d-641">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="1717d-642">Fordern Sie für den Zugriff auf die `RequestServices`-Sammlung Abhängigkeiten lieber als Konstruktorparameter an.</span><span class="sxs-lookup"><span data-stu-id="1717d-642">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="1717d-643">Entwerfen von Diensten für die Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="1717d-643">Design services for dependency injection</span></span>

<span data-ttu-id="1717d-644">Best Practices:</span><span class="sxs-lookup"><span data-stu-id="1717d-644">Best practices are to:</span></span>

* <span data-ttu-id="1717d-645">Entwerfen Sie Dienste zur Verwendung der Abhängigkeitsinjektion, um ihre Abhängigkeiten zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="1717d-645">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="1717d-646">Vermeiden Sie zustandsbehaftete statische Klassen und Member.</span><span class="sxs-lookup"><span data-stu-id="1717d-646">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="1717d-647">Entwerfen Sie Apps so, dass stattdessen Singletondienste verwendet werden. Diese vermeiden das Entstehen eines globalen Zustands.</span><span class="sxs-lookup"><span data-stu-id="1717d-647">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="1717d-648">Vermeiden Sie die direkte Instanziierung abhängiger Klassen innerhalb von Diensten.</span><span class="sxs-lookup"><span data-stu-id="1717d-648">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="1717d-649">Die direkte Instanziierung koppelt den Code an eine bestimmte Implementierung.</span><span class="sxs-lookup"><span data-stu-id="1717d-649">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="1717d-650">Erstellen Sie kleine, gut gestaltete und einfach zu testende Apps.</span><span class="sxs-lookup"><span data-stu-id="1717d-650">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="1717d-651">Wenn eine Klasse zu viele eingefügte Abhängigkeiten zu haben scheint, ist dies im Allgemeinen ein Zeichen dafür, dass die Klasse zu viele Aufgaben hat und gegen das [Single-Responsibility-Prinzip (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) (Prinzip der eindeutigen Verantwortlichkeit) verstößt.</span><span class="sxs-lookup"><span data-stu-id="1717d-651">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="1717d-652">Versuchen Sie, die Klasse umzugestalten, indem Sie einige ihrer Aufgaben in eine neue Klasse verschieben.</span><span class="sxs-lookup"><span data-stu-id="1717d-652">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="1717d-653">Beachten Sie, dass der Fokus der Razor Pages-Seitenmodellklassen und MVC-Controllerklassen auf der Benutzeroberfläche liegt.</span><span class="sxs-lookup"><span data-stu-id="1717d-653">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="1717d-654">Geschäftsregeln und Implementierungsdetails für den Datenzugriff sollten in Klassen aufbewahrt werden gemäß dem Prinzip [Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) (Trennung der Zuständigkeiten).</span><span class="sxs-lookup"><span data-stu-id="1717d-654">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="1717d-655">Löschen von Diensten</span><span class="sxs-lookup"><span data-stu-id="1717d-655">Disposal of services</span></span>

<span data-ttu-id="1717d-656">Der Container ruft <xref:System.IDisposable.Dispose*> für die erstellten <xref:System.IDisposable>-Typen auf.</span><span class="sxs-lookup"><span data-stu-id="1717d-656">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="1717d-657">Wenn eine Instanz dem Container per Benutzercode hinzugefügt wird, wird sie nicht automatisch gelöscht.</span><span class="sxs-lookup"><span data-stu-id="1717d-657">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="1717d-658">Im folgenden Beispiel werden die Dienste vom Dienstcontainer erstellt und automatisch verworfen:</span><span class="sxs-lookup"><span data-stu-id="1717d-658">In the following example, the services are created by the service container and disposed automatically:</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public interface IService3 {}
public class Service3 : IService3, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<IService3>(sp => new Service3());
}
```

<span data-ttu-id="1717d-659">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="1717d-659">In the following example:</span></span>

* <span data-ttu-id="1717d-660">werden die Dienstinstanzen nicht vom Dienstcontainer erstellt.</span><span class="sxs-lookup"><span data-stu-id="1717d-660">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="1717d-661">kennt das Framework die geplante Lebensdauer der Dienste nicht.</span><span class="sxs-lookup"><span data-stu-id="1717d-661">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="1717d-662">verwirft das Framework die Dienste nicht automatisch.</span><span class="sxs-lookup"><span data-stu-id="1717d-662">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="1717d-663">werden Dienste, die nicht explizit im Entwicklercode verworfen werden, weiter ausgeführt, bis die App beendet wird.</span><span class="sxs-lookup"><span data-stu-id="1717d-663">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="1717d-664">IDisposable-Anleitung für vorübergehende and freigegebene Instanzen</span><span class="sxs-lookup"><span data-stu-id="1717d-664">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="1717d-665">Vorübergehende, eingeschränkte Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="1717d-665">Transient, limited lifetime</span></span>

<span data-ttu-id="1717d-666">**Szenario**</span><span class="sxs-lookup"><span data-stu-id="1717d-666">**Scenario**</span></span>

<span data-ttu-id="1717d-667">Für die App ist eine <xref:System.IDisposable>-Instanz mit einer vorübergehenden Lebensdauer für eines der folgenden Szenarios erforderlich:</span><span class="sxs-lookup"><span data-stu-id="1717d-667">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="1717d-668">Die-Instanz wird im Stammbereich aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="1717d-668">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="1717d-669">Die Instanz sollte verworfen werden, bevor der Bereich endet.</span><span class="sxs-lookup"><span data-stu-id="1717d-669">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="1717d-670">**Lösung**</span><span class="sxs-lookup"><span data-stu-id="1717d-670">**Solution**</span></span>

<span data-ttu-id="1717d-671">Verwenden Sie das Factorymuster, um eine Instanz außerhalb des übergeordneten Bereichs zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="1717d-671">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="1717d-672">In dieser Situation verfügt die App in der Regel über eine `Create`-Methode, die den Konstruktor des endgültigen Typs direkt aufruft.</span><span class="sxs-lookup"><span data-stu-id="1717d-672">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="1717d-673">Wenn der endgültige Typ andere Abhängigkeiten aufweist, kann die Factory folgende Aktionen ausführen:</span><span class="sxs-lookup"><span data-stu-id="1717d-673">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="1717d-674">Sie kann <xref:System.IServiceProvider> im Konstruktor empfangen.</span><span class="sxs-lookup"><span data-stu-id="1717d-674">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="1717d-675">Sie kann <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> verwenden, um eine Instanz außerhalb des Containers zu instanziieren und dabei den Container für die Abhängigkeiten verwenden.</span><span class="sxs-lookup"><span data-stu-id="1717d-675">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="1717d-676">Freigegebene Instanz, eingeschränkte Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="1717d-676">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="1717d-677">**Szenario**</span><span class="sxs-lookup"><span data-stu-id="1717d-677">**Scenario**</span></span>

<span data-ttu-id="1717d-678">Für die App ist eine freigegebene <xref:System.IDisposable>-Instanz über mehrere Dienste erforderlich, die <xref:System.IDisposable> sollte jedoch eine begrenzte Lebensdauer haben.</span><span class="sxs-lookup"><span data-stu-id="1717d-678">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="1717d-679">**Lösung**</span><span class="sxs-lookup"><span data-stu-id="1717d-679">**Solution**</span></span>

<span data-ttu-id="1717d-680">Registrieren Sie die Instanz mit einer bereichsbezogenen Lebensdauer.</span><span class="sxs-lookup"><span data-stu-id="1717d-680">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="1717d-681">Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> für den Start und zum Erstellen einer neuen <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span><span class="sxs-lookup"><span data-stu-id="1717d-681">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="1717d-682">Verwenden Sie die <xref:System.IServiceProvider>-Schnittstelle des Bereichs, um die erforderlichen Dienste abzurufen.</span><span class="sxs-lookup"><span data-stu-id="1717d-682">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="1717d-683">Löschen Sie den Bereich, wenn die Lebensdauer beendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="1717d-683">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="1717d-684">Allgemeine Richtlinien</span><span class="sxs-lookup"><span data-stu-id="1717d-684">General Guidelines</span></span>

* <span data-ttu-id="1717d-685">Registrieren Sie keine <xref:System.IDisposable>-Instanzen mit einem vorübergehenden Gültigkeitsbereich.</span><span class="sxs-lookup"><span data-stu-id="1717d-685">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="1717d-686">Verwenden Sie stattdessen das Factorymuster.</span><span class="sxs-lookup"><span data-stu-id="1717d-686">Use the factory pattern instead.</span></span>
* <span data-ttu-id="1717d-687">Lösen Sie keine vorübergehenden oder bereichsbezogenen <xref:System.IDisposable>-Instanzen im Stammbereich auf.</span><span class="sxs-lookup"><span data-stu-id="1717d-687">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="1717d-688">Die einzige allgemeine Ausnahme gilt, wenn die App die <xref:System.IServiceProvider>-Instanz erstellt bzw. erneut erstellt und freigibt, was kein ideales Muster ist.</span><span class="sxs-lookup"><span data-stu-id="1717d-688">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="1717d-689">Wenn eine <xref:System.IDisposable>-Abhängigkeit über DI empfangen wird, ist es nicht erforderlich, dass der Empfänger <xref:System.IDisposable> selbst implementiert.</span><span class="sxs-lookup"><span data-stu-id="1717d-689">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="1717d-690">Der Empfänger der <xref:System.IDisposable>-Abhängigkeit darf <xref:System.IDisposable.Dispose%2A> auf dieser Abhängigkeit nicht abrufen.</span><span class="sxs-lookup"><span data-stu-id="1717d-690">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="1717d-691">Bereiche sollten verwendet werden, um die Lebensdauer von Diensten zu steuern.</span><span class="sxs-lookup"><span data-stu-id="1717d-691">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="1717d-692">Bereiche sind nicht hierarchisch, und es gibt keine besondere Verbindung zwischen den Bereichen.</span><span class="sxs-lookup"><span data-stu-id="1717d-692">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="1717d-693">Ersetzen von Standarddienstcontainern</span><span class="sxs-lookup"><span data-stu-id="1717d-693">Default service container replacement</span></span>

<span data-ttu-id="1717d-694">Der integrierte Dienstcontainer dient dazu, die Anforderungen des Frameworks und der meisten Consumer-Apps zu erfüllen.</span><span class="sxs-lookup"><span data-stu-id="1717d-694">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="1717d-695">Die Verwendung des integrierten Containers wird empfohlen, es sei denn, Sie benötigen ein bestimmtes Feature, das von diesem nicht unterstützt wird. Einige Beispiele:</span><span class="sxs-lookup"><span data-stu-id="1717d-695">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="1717d-696">Eigenschaftsinjektion</span><span class="sxs-lookup"><span data-stu-id="1717d-696">Property injection</span></span>
* <span data-ttu-id="1717d-697">Auf Namen basierende Injektion</span><span class="sxs-lookup"><span data-stu-id="1717d-697">Injection based on name</span></span>
* <span data-ttu-id="1717d-698">Untergeordnete Container</span><span class="sxs-lookup"><span data-stu-id="1717d-698">Child containers</span></span>
* <span data-ttu-id="1717d-699">Benutzerdefinierte Verwaltung der Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="1717d-699">Custom lifetime management</span></span>
* <span data-ttu-id="1717d-700">`Func<T>`-Unterstützung für die verzögerte Initialisierung</span><span class="sxs-lookup"><span data-stu-id="1717d-700">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="1717d-701">Konventionsbasierte Registrierung</span><span class="sxs-lookup"><span data-stu-id="1717d-701">Convention-based registration</span></span>

<span data-ttu-id="1717d-702">Die folgenden Container von Drittanbietern können mit ASP.NET Core-Apps verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="1717d-702">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="1717d-703">Autofac</span><span class="sxs-lookup"><span data-stu-id="1717d-703">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="1717d-704">DryIoc</span><span class="sxs-lookup"><span data-stu-id="1717d-704">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="1717d-705">Grace</span><span class="sxs-lookup"><span data-stu-id="1717d-705">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="1717d-706">LightInject</span><span class="sxs-lookup"><span data-stu-id="1717d-706">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="1717d-707">Lamar</span><span class="sxs-lookup"><span data-stu-id="1717d-707">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="1717d-708">Stashbox</span><span class="sxs-lookup"><span data-stu-id="1717d-708">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="1717d-709">Unity</span><span class="sxs-lookup"><span data-stu-id="1717d-709">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="1717d-710">Threadsicherheit</span><span class="sxs-lookup"><span data-stu-id="1717d-710">Thread safety</span></span>

<span data-ttu-id="1717d-711">Erstellen Sie threadsichere Singleton-Dienste.</span><span class="sxs-lookup"><span data-stu-id="1717d-711">Create thread-safe singleton services.</span></span> <span data-ttu-id="1717d-712">Wenn ein Singleton-Dienst eine Abhängigkeit von einem vorübergehenden Dienst aufweist, muss der vorübergehende Dienst abhängig von der Verwendungsweise durch das Singleton ebenfalls threadsicher sein.</span><span class="sxs-lookup"><span data-stu-id="1717d-712">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="1717d-713">Die Factorymethode des einzelnen Diensts, z. B. das zweite Argument für [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), muss nicht threadsicher sein.</span><span class="sxs-lookup"><span data-stu-id="1717d-713">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="1717d-714">Wie bei `static`-Konstruktoren erfolgt der Aufruf einmalig über einen einzelnen Thread.</span><span class="sxs-lookup"><span data-stu-id="1717d-714">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="1717d-715">Empfehlungen</span><span class="sxs-lookup"><span data-stu-id="1717d-715">Recommendations</span></span>

* <span data-ttu-id="1717d-716">`async/await`- und `Task`-basierte Dienstauflösung wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="1717d-716">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="1717d-717">C# unterstützt keine asynchronen Konstruktoren. Daher wird empfohlen, asynchrone Methoden zu verwenden, nachdem der Dienst synchron aufgelöst wurde.</span><span class="sxs-lookup"><span data-stu-id="1717d-717">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="1717d-718">Vermeiden Sie das Speichern von Daten und die direkte Konfiguration im Dienstcontainer.</span><span class="sxs-lookup"><span data-stu-id="1717d-718">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="1717d-719">Der Einkaufswagen eines Benutzers sollte z. B. normalerweise nicht dem Dienstcontainer hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="1717d-719">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="1717d-720">Bei der Konfiguration sollte das [Optionsmuster](xref:fundamentals/configuration/options) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="1717d-720">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="1717d-721">Gleichermaßen sollten Sie „Datencontainer“-Objekte vermeiden, die nur vorhanden sind, um den Zugriff auf einige andere Objekte zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="1717d-721">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="1717d-722">Das tatsächlich benötige Element sollte besser über Dependency Injection angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="1717d-722">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="1717d-723">Vermeiden Sie den statischen Zugriff auf Dienste (z. B. statische Eingabe von [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) zur Verwendung an anderer Stelle).</span><span class="sxs-lookup"><span data-stu-id="1717d-723">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="1717d-724">Vermeiden Sie die Verwendung des *Dienstlocatormusters*, da dieses [Steuerungsumkehrungsstrategien](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) vermischt.</span><span class="sxs-lookup"><span data-stu-id="1717d-724">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

  * <span data-ttu-id="1717d-725">Rufen Sie beispielsweise nicht <xref:System.IServiceProvider.GetService*> auf, um eine Dienstinstanz zu erhalten, wenn Sie stattdessen Abhängigkeitsinjektion verwenden können:</span><span class="sxs-lookup"><span data-stu-id="1717d-725">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="1717d-726">**Falsch:**</span><span class="sxs-lookup"><span data-stu-id="1717d-726">**Incorrect:**</span></span>

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

    <span data-ttu-id="1717d-727">**Richtig**:</span><span class="sxs-lookup"><span data-stu-id="1717d-727">**Correct**:</span></span>

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

  * <span data-ttu-id="1717d-728">Vermeiden Sie die Injektion von Factorys, die Abhängigkeiten zur Laufzeit mit <xref:System.IServiceProvider.GetService*> auflösen.</span><span class="sxs-lookup"><span data-stu-id="1717d-728">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

* <span data-ttu-id="1717d-729">Vermeiden Sie den statischen Zugriff auf `HttpContext` (z. B. [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="1717d-729">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="1717d-730">Wie bei allen Empfehlungen treffen Sie möglicherweise auf Situationen, in denen eine Empfehlung ignoriert werden muss.</span><span class="sxs-lookup"><span data-stu-id="1717d-730">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="1717d-731">Es gibt nur wenige Ausnahmen, die sich meistens auf besondere Fälle innerhalb des Frameworks beziehen.</span><span class="sxs-lookup"><span data-stu-id="1717d-731">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="1717d-732">Dependency Injection stellt eine *Alternative* zu statischen bzw. globalen Objektzugriffsmustern dar.</span><span class="sxs-lookup"><span data-stu-id="1717d-732">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="1717d-733">Sie werden keinen Nutzen aus der Dependency Injection ziehen können, wenn Sie diese mit dem Zugriff auf statische Objekte kombinieren.</span><span class="sxs-lookup"><span data-stu-id="1717d-733">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1717d-734">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="1717d-734">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="1717d-735">Vier Möglichkeiten zum Verwerfen von IDisposables in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1717d-735">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="1717d-736">Schreiben von sauberem Code in ASP.NET Core über Dependency Injection (MSDN)</span><span class="sxs-lookup"><span data-stu-id="1717d-736">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="1717d-737">Prinzip der expliziten Abhängigkeiten</span><span class="sxs-lookup"><span data-stu-id="1717d-737">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* <span data-ttu-id="1717d-738">[Umkehrung von Steuerungscontainern und das Abhängigkeitsinjektionsmuster (Martin Fowler)](https://www.martinfowler.com/articles/injection.html) (in englischer Sprache)</span><span class="sxs-lookup"><span data-stu-id="1717d-738">[Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)](https://www.martinfowler.com/articles/injection.html)</span></span>
* [<span data-ttu-id="1717d-739">Registrieren eines Diensts mit mehreren Schnittstellen in ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="1717d-739">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
